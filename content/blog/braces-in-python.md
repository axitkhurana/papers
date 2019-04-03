
+++
date = "2012-01-11 20:25:01"
draft = false
title = "Braces in Python"
slug = "braces-in-python"
tags = ['Python', 'Braces', 'C', 'Whitespace']
banner = ""
aliases = ['/braces-in-python/']
+++

<p>One of the hidden features of python I <a href="http://stackoverflow.com/questions/101268/hidden-features-of-python#112303" title="On Stackoverflow" target="_blank">discovered</a> today is that if you don&rsquo;t like whitespace, you can use the C-style {} by issuing:</p>
<p>&gt; &gt; &gt; from __future__ import braces</p>

<p>This is what python replies:</p>
<p>  File &ldquo;&lt;stdin&gt;&rdquo;, line 1<br/>SyntaxError: <strong>not a chance</strong></p>
<p>:D</p>

