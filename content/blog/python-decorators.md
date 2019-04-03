
+++
date = "2012-06-09 19:36:00"
draft = false
title = "Python Decorators"
slug = "python-decorators"
tags = ['Decorators', 'Python']
banner = ""
aliases = ['/python-decorators/']
+++

<p>We know python functions are objects:</p>
<ul><li>can be assigned to a variable</li>
<li>can be defined in another function</li>
</ul><p>Hence, a function can return another function.</p>
<p>Decorators are wrappers, they let you execute code before and after a function without the need to modify the function itself.</p>
<pre><span class="lnr">1 </span><span class="Statement">def</span> <span class="Identifier">decorator</span>(fn):
<span class="lnr">2 </span>    <span class="Statement">def</span> <span class="Identifier">wrapper</span>():
<span class="lnr">3 </span>        <span class="Identifier">print</span> <span class="Constant">"Before fn runs"</span>
<span class="lnr">4 </span>        fn()
<span class="lnr">5 </span>        <span class="Identifier">print</span> <span class="Constant">"After fn runs"</span>
<span class="lnr">6 </span>    <span class="Statement">return</span> wrapper<br/> </pre>
<p>Decorator syntax:</p>
<pre><span class="lnr"> 1 </span><span class="PreProc">@</span><span class="Identifier">decorator</span>
<span class="lnr"> 2 </span><span class="Statement">def</span> <span class="Identifier">standalone_fn</span>():
<span class="lnr"> 3 </span>    <span class="Identifier">print</span> <span class="Constant">"this is a stand alone fn"</span>
<span class="lnr"> 4 </span>
<span class="lnr"> 5 </span>standalone_fn()
<span class="lnr"> 6 </span>
<span class="lnr"> 7 </span><span class="Comment"># prints:</span>
<span class="lnr"> 8 </span><span class="Comment"># Before fn runs</span>
<span class="lnr"> 9 </span><span class="Comment"># this is a stand alone fn</span>
<span class="lnr">10 </span><span class="Comment"># After fn runs</span></pre>
<p>This is equivalent to manually doing:</p>
<pre><span class="lnr">1 </span><span class="Comment"># this is equivalent to:</span>
<span class="lnr">2 </span>standalone_fn = decorator(standalone_fn)
<span class="lnr">3 </span>
<span class="lnr">4 </span>standalone_fn()
<span class="lnr">5 </span>
<span class="lnr">6 </span><span class="Comment"># prints:</span>
<span class="lnr">7 </span><span class="Comment"># Before fn runs</span>
<span class="lnr">8 </span><span class="Comment"># this is a stand alone fn</span>
<span class="lnr">9 </span><span class="Comment"># After fn runs</span>
</pre>
<p>Say, my standalone function had arguments, how do we pass arguments to decorators? Let the wrapper pass the arguments.</p>
<pre><span class="lnr">1 </span><span class="Statement">def</span> <span class="Identifier">decorator</span>(fn):
<span class="lnr">2 </span>    <span class="Statement">def</span> <span class="Identifier">wrapper</span>(*args, **kwargs):
<span class="lnr">3 </span>        <span class="Identifier">print</span> <span class="Constant">"Before fn runs"</span>
<span class="lnr">4 </span>        fn(*args, **kwargs)
<span class="lnr">5 </span>        <span class="Identifier">print</span> <span class="Constant">"After fn runs"</span>
<span class="lnr">6 </span>    <span class="Statement">return</span> wrapper
</pre>
<p>Passing arguments to decorators:</p>
<p>What if you want decorators to have arguments themselves. You need to wrap the decorator with a function which accepts those arguments and returns your decorator.</p>
<pre><span class="lnr"> 1 </span><span class="Statement">def</span> <span class="Identifier">decorator_maker</span>(arg1, arg2):
<span class="lnr"> 2 </span>    <span class="Statement">def</span> <span class="Identifier">decorator</span>(f):
<span class="lnr"> 3 </span>        <span class="Statement">def</span> <span class="Identifier">wrapper</span>(*args, **kwargs):
<span class="lnr"> 4 </span>            <span class="Identifier">print</span> arg1
<span class="lnr"> 5 </span>            f(*args, **kwargs)
<span class="lnr"> 6 </span>            <span class="Identifier">print</span> arg2
<span class="lnr"> 7 </span>        <span class="Statement">return</span> wrapper
<span class="lnr"> 8 </span>    <span class="Statement">return</span> decorator
<span class="lnr"> 9 </span>
<span class="lnr">10 </span><span class="Comment"># this can be used as:</span>
<span class="lnr">11 </span>
<span class="lnr">12 </span><span class="PreProc">@</span><span class="Identifier">decorator_maker</span>(<span class="Constant">'before'</span>, <span class="Constant">'after'</span>)
<span class="lnr">13 </span><span class="Statement">def</span> <span class="Identifier">fn_to_decorate</span>(fn_a1, fn_a2):
<span class="lnr">14 </span>    <span class="Comment"># do something here like:</span>
<span class="lnr">15 </span>    <span class="Identifier">print</span> fn_a1, fn_a2
</pre>
<p>Let&rsquo;s take an example now, I used this for returning JSON in addition to HTML from flask view functions that return dicts.</p>
<pre><span class="lnr"> 1 </span><span class="Statement">def</span> <span class="Identifier">render</span>(template=<span class="Identifier">None</span>):
<span class="lnr"> 2 </span>    <span class="Statement">def</span> <span class="Identifier">decorator</span>(f):
<span class="lnr"> 3 </span>        <span class="PreProc">@</span><span class="Identifier">wraps</span>(f)
<span class="lnr"> 4 </span>        <span class="Statement">def</span> <span class="Identifier">decorated_function</span>(*args, **kwargs):
<span class="lnr"> 5 </span>            ctx = f(*args, **kwargs)
<span class="lnr"> 6 </span>            <span class="Statement">if</span> ctx <span class="Statement">is</span> <span class="Identifier">None</span>:
<span class="lnr"> 7 </span>                ctx = {}
<span class="lnr"> 8 </span>            <span class="Statement">elif</span> <span class="Statement">not</span> <span class="Identifier">isinstance</span>(ctx, <span class="Identifier">dict</span>):
<span class="lnr"> 9 </span>                <span class="Statement">return</span> ctx
<span class="lnr">10 </span>            render_type = request_wants_json()
<span class="lnr">11 </span>            <span class="Statement">if</span> render_type:
<span class="lnr">12 </span>                <span class="Statement">return</span> jsonify(ctx)
<span class="lnr">13 </span>            <span class="Statement">return</span> render_template(template, **ctx)
<span class="lnr">14 </span>        <span class="Statement">return</span> decorated_function
<span class="lnr">15 </span>    <span class="Statement">return</span> decorator
</pre>
<p>What this decorator does is, it takes the dict returned by view (the function f) in ctx,  if the requested wanted JSON, returns the jsonified dict, else renders as HTML using the template. The decorator itself requires an argument for template.</p>
<p>What is @wraps(f) ?</p>
<p>When you use a decorator, you&rsquo;re replacing one function with another. That means if you print f.__name__ it will decorated_function instead of f (if @wraps(f) is not used). Quoting from docs</p>
<p>This is a convenience function for invoking <span class="pre">partial(update_wrapper,</span> <span class="pre">wrapped=wrapped,</span> <span class="pre">assigned=assigned,</span> <span class="pre">updated=updated)</span> as a function decorator when defining a wrapper function.</p>
<p>Bibliography:</p>
<ul><li><a href="http://stackoverflow.com/a/1594484/775446" title="Stack Overflow answer" target="_blank">e-satis&rsquo;s awesome answer on SO</a></li>
<li><a href="http://docs.python.org/library/functools.html#functools.wraps" title="functools.wraps" target="_blank">Python docs on wraps</a></li>
<li><a href="http://flask.pocoo.org/docs/patterns/viewdecorators/" title="Flask view decorators" target="_blank">Flask view decorators</a></li>
</ul>

