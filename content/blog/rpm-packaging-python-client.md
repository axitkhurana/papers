
+++
date = "2012-11-25 19:12:52"
draft = false
title = "RPM: Packaging python client"
slug = "rpm-packaging-python-client"
tags = []
banner = ""
aliases = ['/rpm-packaging-python-client/']
+++

<p><strong>Package Management Systems </strong>take care of everything from installation, upgrading, configuring to removing software packages. They are the original &ldquo;Application Stores&rdquo;. RPM is one such package manager used in various distributions like Fedora, CentOS, openSUSE, Mandriva etc.</p>
<p>Packages, in addition to the software and data, also have some meta data with them, such as the version number, license and dependencies.</p>
<p>To create an RPM package, you need the source files and a spec file that has information about your package.</p>
<p>A specification file looks like this:</p>
<pre>Name:      popcorn
Version:   0.1
Release:   0
Summary:   Client for popularity contest for RPMs
License:   MIT
Url:       <a href="http://github.com/opensuse/popcorn" target="_blank">http://github.com/opensuse/popcorn</a>
Group:     System/Packages
Source:    %{name}.tar.gz
Requires:  cron, python, rpm-python
BuildRoot: %{_tmppath}/%{name}-%{version}-build
BuildArch: noarch

%description
Popcorn tracks the collection of packages and repositories that users have installed.

%prep
%setup -q -c -n %{name}

%build

%install
install -Dm755 popcorn-client %{buildroot}%{_bindir}/popcorn
install -Dm644 popcorn.conf   %{buildroot}%{_sysconfdir}/popcorn.conf
install -Dm755 popcorn.cron   %{buildroot}%{_sysconfdir}/cron.monthly/popcorn
install -Dm644 popcorn.log %{buildroot}%{_localstatedir}/log/popcorn.log

%clean
rm -rf %{buildroot}

%files
%defattr(-, root, root, -)
%doc README LICENSE
%{_bindir}/popcorn
%config(noreplace) %{_sysconfdir}/popcorn.conf
%config(noreplace) %{_sysconfdir}/cron.monthly/popcorn
%ghost %{_localstatedir}/log/popcorn.log

%changelog
</pre>
<p>Keep your spec file in <code>~/rpmbuild/SPECS </code>directory, and source files in <code>~/rpmbuild/SOURCES </code> directory.</p>
<p>Common tags used in SPEC file:</p>
<ul><li>Name: The name of the package, SPEC file name should match this name. It should follow the <a href="http://en.opensuse.org/openSUSE:Package_naming_guidelines" target="_blank">package naming guidelines</a>.</li>
<li>Version: Version number of your software.</li>
<li>Release: Start from 1 for a version, if a new package is released for the same version of software, increment release number.</li>
<li>Summary: One line summary of the package.</li>
<li>License: The open source license used. E.g. MIT, GPLv2</li>
<li>Url: The project website URL.</li>
<li>Group: Pre existing group for type of software. You can find the list of groups in <code>/usr/share/doc/packages/rpm/GROUPS file on openSUSE. </code></li>
<li>Sources: Name of compressed source file.</li>
<li>Requires: List of packages required to run the software.</li>
<li>BuildArch: If the package is architecture independent, mention noarch.</li>
<li>%description: Longer description of the software.</li>
<li>%prep: Commands to prepare the program<em>, </em>e.g. decompressing the source files</li>
<li>%build: Commands for building/compiling source files for installation.</li>
<li>%install: Commands to install the program. This should copy files from buildroot to builddir.[1]</li>
<li>%clean: For cleaning the buildroot directory.</li>
<li>%files: Files to be placed in the binary RPM.</li>
<li>%changelog: Changes in the package.</li>
</ul><p>Use <code>rpmbuild -ba NAME.spec </code>to build the pacakage.</p>
<p>rpmbuild reads the SPEC file and perform the following operations:</p>
<ul><li>unpacks (%prep) sources from source directory to build directory</li>
<li>compiles (%build) files in build directory</li>
<li>%install: reads from build directory, and writes the files to be installed by an end user, in the build root directory.</li>
<li>creates binary rpm, source rpm package reading from build root directory.</li>
</ul><p>RPMs are created in <code>%_rpmdir</code> directory, <code>~/rpmbuild/RPMS/noarch/</code> in this case. Use rpmlint to check common problems in RPM package.</p>
<p>Files included in packaged Popcorn[2] client:</p>
<ol><li>popcorn: the client python script that collects anonymous package usage data and sends to popcorn server.</li>
<li>popcorn.conf: configuration file having basic settings for popcorn.</li>
<li>popcorn.log: log file for data sent to server.</li>
</ol><p><em>Popcorn is an openSUSE project[3] that tracks usage of linux packages.</em></p>
<p><br/>Useful links:</p>
<ul><li><a href="https://fedoraproject.org/wiki/Packaging:RPMMacros" target="_blank">https://fedoraproject.org/wiki/Packaging:RPMMacros</a></li>
<li><a href="http://www.rpm.org/max-rpm/s1-rpm-inside-files-list-directives.html" target="_blank">http://www.rpm.org/max-rpm/s1-rpm-inside-files-list-directives.html</a></li>
</ul><p>[1] Reference: <a href="https://fedoraproject.org/wiki/How_to_create_an_RPM_package" target="_blank">Fedora&rsquo;s Guide for creating an RPM package</a></p>
<p>[2] My <a href="http://www.google-melange.com/gsoc/proposal/review/google/gsoc2012/axitkhurana/3002" target="_blank">GSoC &lsquo;12 Proposal</a> for the project. Thanks <a href="http://mapleoin.eu/" target="_blank">Ionuț Arțăriși</a> for mentoring :)</p>
<p>[3] <a href="https://github.com/opensuse/popcorn" target="_blank">https://github.com/opensuse/popcorn</a></p>

