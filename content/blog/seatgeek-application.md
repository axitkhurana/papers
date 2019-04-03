
+++
date = "2012-03-02 02:22:00"
draft = false
title = "SeatGeek application"
slug = "seatgeek-application"
tags = []
banner = ""
aliases = ['/seatgeek-application/']
+++

<p>I was going over the <a href="http://hackerne.ws" title="Hacker News" target="_blank">HN</a> March Who&rsquo;s Hiring <a href="http://hackerne.ws/item?id=3652041" title="Who's Hiring" target="_blank">post</a>, looking for internships, when I clicked on SeatGeek&rsquo;s web engineer <a href="http://hackerne.ws/item?id=3652372" title="SeatGeek's Post" target="_blank">post</a>.</p>
<p>I found this in the left column:</p>
<blockquote>
<p><span>For bonus points, you can apply by hacking into our backend jobs admin panel and submitting your resume here: </span><a href="http://apply.seatgeek.com/" target="_blank">http://apply.seatgeek.com/</a></p>
</blockquote>
<p>I checked out the link and the related blog post. This was awesome. The first message:</p>
<blockquote>
<p><span>This page must be viewed from the SeatGeek browser</span></p>
</blockquote>
<p>This was easy, changed my user agent to SeatGeek in the Chrome Developer Tools&rsquo; settings. We have the New Applicant Form with a note </p>
<blockquote>
<p><span>Note: only &lsquo;admin&rsquo; users may submit new applicants</span> </p>
</blockquote>
<p>Submitting the form leads to a blank page.</p>
<p>Had to look into the source. Ah! A hidden input field &rsquo;_csrf&rsquo; with the value 'this is required (and this value is incorrect)&rsquo;. Changing the value to admin was a dumb move. Should be a csrf token, some kind of hash value.</p>
<p>Next thing checked the resources tab. A cookie with the name sg.session and a value with admin and csrf.token as substring.</p>
<p> urlllib.unquote(cookie_value) on a python prompt gives</p>
<p>&rsquo;{&ldquo;admin&rdquo;:0,&ldquo;csrf.token&rdquo;:&ldquo;XcfT7I4tqPM9vBbAb/esyW0mxMVJbBXqda8VjstXaZA=&rdquo;}&rsquo;</p>
<p>submitting with csrf.token value in _csrf gives an error:</p>
<blockquote>
<p><span>You must submit as an 'admin&rsquo;</span></p>
</blockquote>
<p>Modify the cookie to </p>
<p>&rsquo;{&ldquo;admin&rdquo;:1,&ldquo;csrf.token&rdquo;:&ldquo;XcfT7I4tqPM9vBbAb/esyW0mxMVJbBXqda8VjstXaZA=&rdquo;}&rsquo;</p>
<p>and quote it back. Submitting with modified cookie and csrf token value gives:</p>
<blockquote>
<div class="flash">
<div class="message notice">
<ul class="list"><li>Akshit Khurana created successfully</li>
</ul></div>
</div>
<p class="first">Thank you for submitting a new applicant, they will be contacted shortly.</p>
</blockquote>
<p>I am looking forward to hearing from them. This was an exciting, had fun applying.</p>

