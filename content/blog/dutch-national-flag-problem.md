
+++
date = "2011-10-05 18:14:00"
draft = false
title = "Dutch National Flag Problem"
slug = "dutch-national-flag-problem"
tags = ['Algorithms', 'Dutch National Flag', 'C++', 'Partition']
banner = ""
aliases = ['/dutch-national-flag-problem/']
+++

<p>Given balls of three colors, say red white and blue randomly arranged, the aim is to arrange all the balls of same color together as in the flag using only the swap operation.</p>
<p>The algorithm is similar to the partition algorithm used in quicksort. We separate the array into three regions: &lt;low, &gt;=low &amp;&amp; &lt;high, &gt;high.</p>
<p>void three_partition(int data[], int size, int low, int high)<br/> {<br/>     int x = -1;<br/>     int y = size;<br/>     for(int i = 0;i&lt;y;)<br/>     {<br/>         if(data[i]&lt;low)<br/>         {<br/>             swap(data[i], data[++x]);<br/>             i++;<br/>         }<br/>         else if(data[i]&gt;=high)<br/>         {<br/>             swap(data[i], data[&ndash;y]);<br/>         }<br/>         else<br/>         {<br/>             i++;<br/>         }<br/>     }<br/> }</p>
<p>Lets give numbers corresponding to colors, say red = 0, white = 1 and blue = 2, pass low = 1 and high =2 in the function.</p>
<p>Btw: <a title="Dutch national flag" target="_blank" href="http://flagspot.net/images/n/nl.gif">dutch national flag</a>.</p>

