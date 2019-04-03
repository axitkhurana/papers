
+++
date = "2016-05-29 03:52:20"
draft = false
title = "PyCon '15 sprints: first CPython patch"
slug = "making-basic-auth-twice-as-fast"
tags = []
banner = ""
aliases = ['/making-basic-auth-twice-as-fast/']
+++

The best part of PyCon is not the conference itself. It's after PyCon.

Sprints are 4 days of access to the best python minds helping you build software that the world uses. Django, CPython, Mailman, Sage, pandas, buildbot [and many more](https://us.pycon.org/2015/community/sprints/) participated last year. 

You don't want to miss sprints. In fact, if you have 3 days only I would suggest you choose sprints over the conference itself. Talks and tutorials are recorded, you can watch them whenever you'd like.

## CPython

I've had been thinking about contributing to CPython for a long time, occasionally fishing for easy issues in the bug tracker, following the core-mentorship list, downloading the source etc. I was so ready to fix something!

After the intros to sprint projects and I went to the CPython room, started looking at the list of bugs to fix.

I wanted to make a fix that wasn't just documentation or too small to feel like I missed the opportunity. After going through a couple I found this one: [Urllib2 authentication memory](http://bugs.python.org/issue7159) which looked significant but good work for 4 days in a new codebase.

### The problem

> For each request requiring HTTP authentication, urllib2 submits the
request without authentication, receives the server's 401
error/challenge, then re-submits the request with authentication.

> This is compliant behavior. The problem comes in that urllib2 repeats
this for every ensuing request to *the same namespace.*

> At times this is just an inefficiency--every request gets sent twice,
often with POST data (which can be sizeable).

> Sometimes, especially with large POST bodies, this causes a connection
failure.

> This isn't non-compliant behavior, but RFC2617 (sections 2, 3.3)
suggests that once an HTTP client authenticates, it pre-emptively send
authentication with ensuing requests.

What this means is every time urllib2 sends a request with basic auth, it will first make the request without credentials, get a 401 unauthorized, check the auth the endpoint expects and then send the same request with credentials. 

This is expected behavior but inefficient if we know the endpoint needs the credentials. We're making 2x the number of requests!

### Why?

Before I even thought about writing a fix, I was curious why this was RFC compliant behavior. Why not just send the credentials?

I had a feeling this was because of the stateless nature of HTTP, [RFC 2617](https://www.ietf.org/rfc/rfc2617.txt) gave me a little more information:

>  The "basic" authentication scheme is based on the model that the
   client must authenticate itself with a user-ID and a password for
   each realm.

>   Upon receipt of an unauthorized request for a URI within the
   protection space, the origin server MAY respond with a challenge like
   the following:

>      WWW-Authenticate: Basic realm="WallyWorld"

>   where "WallyWorld" is the string assigned by the server to identify
   the protection space of the Request-URI. 

>   To receive authorization, the client sends the userid and password,
   separated by a single colon (":") character, within a base64
   encoded string in the credentials.

>   A client SHOULD assume that all paths at or deeper than the depth of
   the last symbolic element in the path field of the Request-URI also
   are within the protection space specified by the Basic realm value of
   the current challenge. **A client MAY preemptively send the
   corresponding Authorization header with requests for resources in
   that space without receipt of *another* challenge from the server.**

### Things to fix

1. According to the RFC a client **may** preemptively send auth headers after the first challenge, so once we hit the URL without the credentials, get back a 401, we can send the credentials in subsequent requests.
2. Another [issue](http://bugs.python.org/issue19494) mentions some APIs like Github doesn't send 401 for unauthorized, they send 404s which means we should have the ability to directly send the credentials without getting the first 401.

### Architecture

The API isn't the most friendly one (you should use [requests](http://docs.python-requests.org/en/master/) instead), but this is how you make a request:

```
  51 # set up authentication info
  52 authinfo = urllib.request.HTTPBasicAuthHandler()
  53 authinfo.add_password(realm='PDQ Application',
  54                       uri='https://mahler:8092/site-updates.py',
  55                       user='klem',
  56                       passwd='geheim$parole')
  57
  58 proxy_support = urllib.request.ProxyHandler({"http" : "http://ahad-haam:3128"})
  59
  60 # build a new opener that adds authentication and caching FTP handlers
  61 opener = urllib.request.build_opener(proxy_support, authinfo,
  62                                      urllib.request.CacheFTPHandler)
  63
  64 # install it
  65 urllib.request.install_opener(opener)
  66
  67 f = urllib.request.urlopen('http://www.python.org/')
```

Two classes we care about here:

1. HTTPBasicAuthHandler: specifies what do  for different status codes with methods
2. PasswordManager: manages username, password, realm for your requests

### Initial approach

The [existing patch](https://hg.python.org/cpython/rev/fb3061ba6fd2) added a new `BasicAuthHandler` subclass that sends the credentials without even waiting for the first 401. That would work the Github case but not the first case.

### Proposed patch

We released instead of creating a subclassing `BasicAuthHandler` we could just add a new password manager that subclasses `HTTPPasswordMgrWithDefaultRealm`. Since we want to support sending credentials even before we get a 401 response from the server (which has the realm value), we store the password in a default (`None`) realm.


#### HTTPPasswordMgrWithPriorAuth

Subclasses `HTTPPasswordMgrWithDefaultRealm` and adds a `uri -> is_authenticated` mapping. What that gives us is that if the user has already authenticated for a particular URL root, we don't need to get a 401 from the server for every request for that URL tree, we can just send the credentials.

##### Whaat?

1. Request to axitkhurana.com/locked/ (say this endpoint requires basic auth)
2. We get a 401 back
3. We send credentials this time
4. Get a 200 SUCCESS back
5. We update the dictionary mapping `{'/locked/': True}`
6. Every subsequent request to /locked/ or /locked/abc sends the credentials without waiting for 401, *making everything ~2x faster*

##### What about the Github case?

Sending credentials even before the first 401.

Use the method:

* `update_authenticated(self, uri, is_authenticated=False)`: Updates the `is_authenticated` flag for the given *uri* or list of URIs.

Set `is_authenticated` to `True` before making a request manually instead of waiting for the first successful auth. Voila!

### Accepted

After some design discussions, tests, a couple more useful methods and [R. David Murray](https://twitter.com/rdavidmurray) rewriting the docs to make them readable, I finally got my [first contribution to CPython](https://hg.python.org/cpython/rev/1b9e81cb83bc)!


