
+++
date = "2012-06-30 15:56:26"
draft = false
title = "UUIDs"
slug = "uuids"
tags = []
banner = ""
aliases = ['/uuids/']
+++

<p><em>This blog post is a part of “the things I learned while working on popcorn”, Popcorn tracks usage of linux packages. <a href="http://mapleoin.eu/" target="_blank">Ionuț Arțăriși</a></em><em> is my mentor for this <a href="http://www.google-melange.com/gsoc/proposal/review/google/gsoc2012/axitkhurana/3002" title="GSoC Proposal" target="_blank">GSoC project</a> for openSUSE.</em></p>
<p>Popcorn requires UUIDs (Universally unique IDs) so that it doesn&rsquo;t receive submissions from the same system again before a certain period of time.</p>
<p>We used to use smolt id, but smolt package is not present in all Linux distributions.</p>
<p>So on a quest to find a universally unique ID that is present on all distributions, Petr Uzel pointed me to <a href="http://0pointer.de/blog/projects/ids.html" target="_blank">Lennart&rsquo;s Blog Post</a>.</p>
<p><strong>tl;dr</strong> use/var/lib/dbus/machine-id if you are looking for a globally unique id for a Linux system.</p>
<p><span>Hardware IDs:</span></p>
<ul><li><strong>/sys/class/dmi/id/product_uuid</strong>: mainboard uuid</li>
<li><strong>CPUID/EAX=3</strong>: CPU serial number</li>
<li><strong>/sys/class/net/*/address</strong>: One or more network MAC addresses</li>
<li><strong>/sys/bus/usb/devices/*/serial</strong>:Serial numbers of various USB devices</li>
</ul><p>The problems with hardware IDs are that:</p>
<ul><li>They are bound to specific, replaceable hardware</li>
<li>Not universally available</li>
<li>Filled with bogus data/random in virtual environments</li>
</ul><p><span>Software IDs:</span></p>
<ul><li><strong>/proc/sys/kernel/random/boot_id</strong>: Random ID unique for each boot</li>
<li><strong>/var/lib/dbus/machine-id</strong>: identifies specific Linux installation.<br/><ul><li>Doesn&rsquo;t change if hardware is replaced</li>
<li>Reliable in virtualized environments</li>
<li>Globally unique</li>
</ul></li>
<li><strong>/proc/self/sessionid: </strong>identifies a specific login session, maintained by kernel and shared by each process of a session. In some distributions this is always(uint32_t) -1</li>
<li><strong>getuid()</strong>: To identify a particular user. Locally unique, may be reassigned to new user if users are deleted.</li>
</ul><p>We can combine locally unique ids with /var/lib/dbus/machine-id to generate globally unique ids for a particular user or session.</p>
<p><span>Generating UUIDs:</span></p>
<p>You can also generate UUIDs using uuidgen command or simply read 16 bytes from /dev/random</p>

