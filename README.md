Dist-Detect
===========

Dist-Detect is an active commandline scanner to detect the Linux or
Unix distribution running on a remote host by looking at the banners
or responses of typical Unix netowrk services.

Dist-Detect is currently work in progress. For now only the SSH
service is supported and works already quite well in detecting Debian
and derivatives (Ubuntu, Raspbian, etc.), but HTTP/HTTPS, DNS (`dig
version.bind ch txt +short @<IP>`) and SMTP might be a good data
source as well.

Purpose
-------

Quickly get an idea …

* … of the Linux/BSD/Unix distribution and distribution release of a
  remote system …

* … if the admin applies security updates regularily …

* … if the remote system is running an EoL release …

… just by looking at the responses of a few common network services,
i.e. very fast.

This is especially useful in heterogenous networks (e.g. with BYOD or
many self-managed machines) as common in academia, data-centers with a
lot of internet-facing, rented servers/racks, etc.

### Focus on Low Hanging Fruits

* If the scanner finds something bad, it's quite sure → nearly no False Positives
* Unknown or unclear versions stay unknown or unclear → will contain False Negatives

Example
-------

```
SSH-2.0-OpenSSH_7.4p1 Debian-10+deb9u4
```

The `7.4p1 Debian` as well as the `deb9` clearly show that this is a
Debian 9 Stretch. From the banner you can determine the according
package version to be `1:7.4p1-10+deb9u4`.

Now you can check against the version in the Debian 9 Stretch
(security) repositories (e.g. [in the Debian Package
Tracker](https://tracker.debian.org/pkg/openssh) if it's the latest
one (it's not as of this writing) and hence if OpenSSH security
updates as provided by Debian have been applied.

This tools tries to automate this kind of analysis and is hence
allowing to scan your whole network quickly for obviously outdated
machines. I call this _Low Hanging Fruits Scanning_.


Work in Progress
----------------

As of now, this work in progress.

The inital prototype used hardcoded (and handcoded :-) regular
expression (which were outdated quite quicly).

Currently the package repositories of Debian, Ubuntu and Raspbian are
checked for the current OpenSSH versions and then the result is stored
in an SQLite database. This database is then queried when translating
OpenSSH banners into package versions and uptodateness information.

Especially the database schema will likely still change without
migration path between each incarnation at the current stage of
development. (But since the database can be easily regenerated, this
should be no real issue.)

Requirements
------------

Dist-Detect is written in **Perl (5.14 or a higher 5.x version)** and
requires the following non-core Perl modules (CPAN distributions and
Debian packages names in parentheses):

* [`Class::C3`](https://metacpan.org/pod/Class::C3)
  ([`Class-C3`](https://metacpan.org/release/Class-C3),
  [`libclass-c3-perl`](https://packages.debian.org/libclass-c3-perl))

* [`Dpkg::Compression::FileHandle`](https://metacpan.org/pod/Dpkg::Compression::FileHandle)
  and [`Dpkg::Version`](https://metacpan.org/pod/Dpkg::Version)
  ([`Dpkg`](https://metacpan.org/release/Dpkg),
  [`libdpkg-perl`](https://packages.debian.org/libdpkg-perl))

* [`DPKG::Parse::Packages`](https://metacpan.org/pod/DPKG::Parse::Packages)
  ([`DPKG-Parse`](https://metacpan.org/release/DPKG-Parse),
  [`libdpkg-parse-perl`](https://packages.debian.org/libdpkg-parse-perl))

* [`File::Touch`](https://metacpan.org/pod/File::Touch)
  ([`File-Touch`](https://metacpan.org/release/File-Touch),
  [`libfile-touch-perl`](https://packages.debian.org/libfile-touch-perl))

* [`IO::Socket::INET6`](https://metacpan.org/pod/IO::Socket::INET6)
  ([`IO-Socket-INET6`](https://metacpan.org/release/IO-Socket-INET6),
  [`libio-socket-inet6-perl`](https://packages.debian.org/libio-socket-inet6-perl))

* [`Mojo::Collection`](https://metacpan.org/pod/Mojo::Collection),
  [`Mojo::Date`](https://metacpan.org/pod/Mojo::Date),
  [`Mojo::File`](https://metacpan.org/pod/Mojo::File),
  [`Mojo::UserAgent`](https://metacpan.org/pod/Mojo::UserAgent) and
  many more `Mojo::*` indirectly, like `Mojo::JSON`
  ([`Mojolicious`](https://metacpan.org/release/Mojolicious),
  [`libmojolicious-perl`](https://packages.debian.org/libmojolicious-perl))

* [`Mojo::SQLite`](https://metacpan.org/pod/Mojo::SQLite)
  ([`Mojo-SQLite`](https://metacpan.org/release/Mojo-SQLite),
  [`libmojo-sqlite-perl`](https://packages.debian.org/libmojo-sqlite-perl))

* [`Net::CIDR::Set`](https://metacpan.org/pod/Net::CIDR::Set)
  ([`Net-CIDR-Set`](https://metacpan.org/release/Net-CIDR-Set),
  [`libnet-cidr-set-perl`](https://packages.debian.org/libnet-cidr-set-perl))

* [`Net::DNS`](https://metacpan.org/pod/Net::DNS)
  ([`Net-DNS`](https://metacpan.org/release/Net-DNS),
  [`libnet-dns-perl`](https://packages.debian.org/libnet-dns-perl))

* [`Params::Validate`](https://metacpan.org/pod/Params::Validate)
  ([`Params-Validate`](https://metacpan.org/release/Params-Validate),
  [`libparams-validate-perl`](https://packages.debian.org/libparams-validate-perl))

* [`Try::Tiny`](https://metacpan.org/pod/Try::Tiny)
  ([`Try-Tiny`](https://metacpan.org/release/Try-Tiny),
  [`libtry-tiny-perl`](https://packages.debian.org/libtry-tiny-perl))

* [`YAML::Any`](https://metacpan.org/pod/YAML::Any)
  ([`YAML`](https://metacpan.org/release/YAML),
  [`libyaml-perl`](https://packages.debian.org/libyaml-perl))


Installation
------------

None yet. Use it just from the checked out git repository by calling
the scripts in `bin/` with their relative or full path.


TODO
----

* Consitent tags: NoSecUpd vs NO-UPD

* Add package list downloaders and scrapers for CentOS, openSUSE and
  macOS.

* Distinguish between repos where SSH signatures changes often (active
  security mirrors of Debian and derivatives) and where they change
  seldomly (old-releases.ubuntu.com / archive.debian.org).

* Make output more human readable. Maybe use a commandline switch to
  produce either machine or human readable output. The human readable
  output could be still machine readable by e.g. using YAML instead of
  purely line-based formats.

* Debian Jessie is at the moment available on the normal mirrors and
  in the historical archive and hence gets flagged EOL even if that
  might be wrong depending on the (not easily detectable)
  architecture. Handle this better.


Plans
-----

### Store most current OpenSSH release version in database

The [bin/newest-openssh-version-on-*.pl](bin/) scripts are a good
start for that.

### Consider Further Services

If e.g. the SSH banner was `SSH-2.0-OpenSSH_7.4`, this could be (at
least) a RHEL 7.4 or higher, or a macOS 10.12.4 to 10.12.6. So other
protocols should add more confidence or limit the list of possible
operating systems and distributions.

#### HTTP / HTTPS

```
$ HEAD http://ssh-was-ambiguous/
200 OK
Connection: close
Date: […]
Server: Apache/2.4.6 (Red Hat Enterprise Linux)
```

Here, port 80 told us the distribution even though the SSH banner was
ambiguous.

```
HEAD http://somecentos6/
200 OK
Date: […]
Server: Apache/2.2.15 (CentOS)
```

#### SMTP

```
$ echo QUIT | nc mymailserver 25
220 mymailserver ESMTP Postfix (Debian/GNU)
221 2.0.0 Bye
$ echo QUIT | nc afriendsmailserver 25
220-afriendsmailserver ESMTP Proxmox
221 2.0.0 Bye
$ echo QUIT | nc anothermailserver 25
220 anothermailserver ESMTP Exim 4.86_2 Ubuntu Thu, 10 Oct 2019 17:35:32 +0200
221 anothermailserver closing connection
```

We often don't get the version, but at least the Linux
distribution. Again helpful if the SSH banner is ambiguous.

### DNS

```
$ dig +short -t txt -c chaos version.bind @ams.sns-pb.isc.org
"9.9.7-P2"
$ dig +short version.bind CH TXT @a.iana-servers.net
"Knot DNS 2.6.3"
 dig +short version.bind CH TXT @ns.nlnetlabs.nl
"NSD 4.2.2"
$ dig +short version.bind CH TXT oneofmydnsservers
"9.9.5-9+deb8u18-Debian"
$ dig +short version.bind CH TXT somerhel7
"9.11.4-P2-RedHat-9.11.4-9.P2.el7"
$ dig +short version.bind CH TXT anotherrhel7
"9.9.4-RedHat-9.9.4-74.el7_6.2"
$ dig version.bind ch txt +short @127.0.0.1
"unbound 1.9.4"
$ dig version.bind ch txt +short @192.168.1.1
"dnsmasq-2.78"
```

Please note that [fpdns](https://github.com/kirei/fpdns) is about as
(un)suitable as `nmap` for this purpose (but much faster): It does
real fingerprinting and not evaluating the actual data it can retrieve
from a DNS server.

An exception would be cases like these:

```
$ dig +short version.bind CH TXT @8.8.8.8
$ dig +short version.bind CH TXT @a.ns.nic.cz
$ dig +short version.bind CH TXT @ns2.switch.ch
"contact dns-operation@switch.ch"
$ dig +short version.bind CH TXT @a.nic.de
"ns-1.de.nl1.bind"
```


Ideas
-----

* Also store results and scan dates in a database.

* Someone suggested to also look at SIP ports.

* Parse package changelogs for existing versions.

* Add optional scanning backends.

    * SSH:
        * [scanssh](http://www.monkey.org/~provos/scanssh/),
        * [ZMap's ZGrab](https://github.com/zmap/zgrab] with its "x/crypto SSH scanner" plugin.
    * Any service which provides exact Linux kernel versions, like
      open Redis servers. ;-)
    * Generic TCP:
        * [pnscan](https://github.com/ptrrkssn/pnscan),
        * [masscan](https://github.com/robertdavidgraham/masscan),
        * [ZMap](https://zmap.io/]?
        * [pf_ring](https://www.ntop.org/products/packet-capture/pf_ring/)?
    * Online (i.e. publicly available data):
        * [Shodan.io](https://www.shodan.io/)?

* Ping (likely with [fping](https://www.fping.org/)?) before scan.

* Maybe use https://repology.org/api and
  https://repology.org/project/openssh/versions instead of or in
  addition to scraping package lists. Probably filter returned list to
  only use these package names:
    * `openssh`
    * `openssh-server`
    * `openssh-portable`
  The following list is questionable as they're patched versions which
  may lag behind the unpatched version:
    * `openssh-krb5` (SlackBuilds)
    * `openssh-gssapi` (AUR)
    * `openssh-multiple-bindaddress` (AUR)
    * `openssh-with-hpn` (nixpkgs)

* Another source might be [Wikidata's OpenSSH
  entry](https://www.wikidata.org/wiki/Q847062) and [its JSON
  representation](https://www.wikidata.org/wiki/Special:EntityData/Q847062.json).
    * Probably less reliable than repology because likely only manual
      / crowd-sourced data updates.

* Add support for more [Debian
  derivatives](https://wiki.debian.org/Derivatives/Census):

    * Supported derivatives ones like Trisquel, Linux Mint, and Kali Linux.
    * Live-CDs like Tails, Grml and Knoppix
    * Discontinued ones (you also want to detect them) like Tanglu

* Let the package list parser optionally generate rules from every
  banner of a non-up-to-date or end-of-life version for SNORT (and
  hence also Suricata) and/or Zeek (formerly known as Bro).

* Maybe also take the output of
  [ssh-audit](https://github.com/arthepsy/ssh-audit) as a signature
  for an OS. Needs a collection of signatures, though.

* Maybe allow `scanhost.pl` to read scan targets from `STDIN` or use a
  configuration file for batch scans, too.

### Wishlist

* A way to detect that some Linux distribution is running inside the
  Windows Subsystem for Linux (WSL).


License and Copyright
---------------------

Copyright 2019, Axel Beckert <axel@ethz.ch> and [ETH
Zurich](https://www.ethz.ch/).

Dist-Detect is free software: you can redistribute it and/or modify it
under the terms of the [GNU General Public
License](https://www.gnu.org/licenses/gpl.html) as published by the
Free Software Foundation, either [version 3 of the
License](https://www.gnu.org/licenses/gpl-3.0.html), or (at your
option) any later version.

Dist-Detect is distributed in the hope that it will be useful, but
WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the GNU
General Public License for more details.

You should have received [a copy of the GNU General Public
License](LICENSE.md) along with Dist-Detect.  If not, see
<https://www.gnu.org/licenses/>.


Thanks!
-------

* Jakob Dhondt for the project name suggestion.
* The [SWITCH Open Source Security Tools Hackathon
  2019](https://www.eventbrite.de/e/open-source-security-tools-hackathon-2019-tickets-59395447382)
  for providing the right atmosphere to get the project away from the
  Proof of Concept state. :-)


Resources
---------

### Slide Decks about Dist-Detect

* [Short Slideshow via
  GitPitch](https://gitpitch.com/xtaran/dist-detect) ([Source file
  rendered as normal Markdown
  file](https://github.com/xtaran/dist-detect/blob/master/PITCHME.md))

* [More verbose slide deck (English) based on AsciiDoc and
  Slidy](https://github.com/xtaran/dist-detect/blob/master/slides/dist-detect-slides_en.adoc)

* [More verbose slide deck (German) based on AsciiDoc and
  Slidy](https://github.com/xtaran/dist-detect/blob/master/slides/dist-detect-slides_de.adoc)

### OpenSSH Upstream

* [OpenSSH Release Notes](https://www.openssh.com/releasenotes.html)

### Package Versions

* [OpenSSH in the Debian Package
  Tracker](https://tracker.debian.org/pkg/openssh)

* [Launchpad: OpenSSH package in Ubuntu
  (Overview)](https://launchpad.net/ubuntu/+source/openssh)

* [CertDepot: RHEL7 Changes between
  versions](https://www.certdepot.net/rhel7-changes-between-versions/)

* [OpenSSH package of
  SuSE/openSUSE](https://software.opensuse.org/package/openssh)

* [Open Source Code in use at Apple](https://opensource.apple.com/)

### Specific Details

* [Debian Bug #562048 explains how the DebianBanner patch came
  along](https://bugs.debian.org/562048)

### Similar Tools

* [ssh-version](https://github.com/vaporup/ssh-tools/blob/master/ssh-version)
  (probably too slow to be used as backend, more thought as small and
  simple commandline tool)

### Unsorted

* https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html-single/7.4_release_notes/#BZ1341754
* https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html-single/7.1_release_notes/#idm140132757747728
* https://jira.atlassian.com/browse/SRCTREE-4346
