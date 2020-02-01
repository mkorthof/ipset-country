# ipset-country

## Block countries using iptables + ipset + ipdeny.com

---

_This used to be a [Gist](https://gist.github.com/mkorthof/3033ff64c4a5b4bd31336d422104d543) but was moved here instead_  
_Please do not add Gist comments, but create an issue [here](https://github.com/mkorthof/ipset-country/issues)_

---

- [x] Also works with ipverse.com and other providers
- [x] Supports RH, Debian with iptables and/or firewalld
- [x] Both ipv4 and ipv6 are supported

Installation
------------

- Setup firewall if you have not done so yet, at least INPUT chain
- Run this script from cron, e.g. /etc/cron.daily or a [systemd timer](https://www.freedesktop.org/software/systemd/man/systemd.timer.html)
- To run on boot you can also add it to e.g. /etc/rc.local or systemd
- Use argument "force" to load unchanged zonefiles instead of skipping

This script will insert an iptables 'REJECT' or 'DROP' rule for ipset.  
Make sure you do not lock yourself out in case of issues on a remote system.

Configuration
-------------

**Distro:**

If needed change OS using `DISTRO` setting. Default is "auto" which should be OK.

Options are:
- "auto", "debian" or "redhat"
- "manual"
  - `confdir="/etc/iptables"` (example)
  - `rulesfile="${confdir}/myrules"` (example)

---

**Countries:**

Specify countries to block as `"ISOCODE,Name"` (same as ipdeny.com), multiple entries should be seperated by semicolon `;`

Example:  
`COUNTRY="CN,China; US,United States; RU,Russia"`

---

**Firewalld:**  
Set this option to "1" to enable firewalld: `FIREWALLD=0`

* _NOTE:_
There are issues with firewalld on CentOS/RHEL 8 which can cause your firewall to break resulting in being locked out. Adding large ipsets apparently can takes a VERY long time. To abort you need remote console access and run `pkill firewal-cmd; nft flush ruleset`

---

**Blocklist provider:**

Set URLs for ipv4 and/or ipv6 block files, you probably do not have to change these.  
To use [ipverse.net](http://ipverse.net) instead of [ipdeny.com](https://ipdeny.com) and for more details see [script](ipset-country)

- `IPBLOCK_URL_V4="http://www.ipdeny.com/ipblocks/data/aggregated"`
- `IPBLOCK_URL_V6="http://www.ipdeny.com/ipv6/ipaddresses/blocks"`

---

**Logs:**  
In case you want to change file location set: `LOG="/var/log/ipset-country.log"`

---

**Other options are explained in [ipset-country.sh script](ipset-country)**

IPset
------

Useful ipset commands:

- `ipset list`
- `ipset test setname <ip>`
- `ipset flush`
- `ipset destroy`

Changes
-------

- [20200129] added option to DROP instead of REJECT (#1)
- [20191116] added ipverse support, md5check option
- [20190905] tested on debian 10 and centos 7
- [20190905] blocking multiple countries should work
- [20190905] it will check if INPUT chain exists in iptables
- [20190905] cleaned it up a bit
- [20190905] using firewalld is also supported now

Other
-----

Also available: [github.com/tokiclover/dotfiles/blob/master/bin/ips.bash](https://github.com/tokiclover/dotfiles/blob/master/bin/ips.bash)

