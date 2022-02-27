# ipset-country

## Block or allow countries using iptables, ipset and ipdeny.com

---

_This used to be a [Gist](https://gist.github.com/mkorthof/3033ff64c4a5b4bd31336d422104d543) but was moved here instead_  
_Please do not add Gist comments, but create an issue [here](https://github.com/mkorthof/ipset-country/issues)_

---

- [x] Also works with ipverse.com and other providers
- [x] Supports RH, Debian with iptables and/or firewalld
- [x] Both ipv4 and ipv6 are supported

Installation
------------

1) Setup firewall if you have not done so yet, **at least INPUT chain** is needed
2) Run this script from cron, e.g. /etc/cron.daily or a [systemd timer](https://www.freedesktop.org/software/systemd/man/systemd.timer.html) (see below)
3) To run on boot you can also add it to e.g. /etc/rc.local or systemd
4) Use argument "-f" to load unchanged zonefiles instead of skipping

- To automatically setup a systemd service and daily timer run: `ipset-country -i`
- To uninstall run:`ipset-country -u`

Running this script will insert an iptables 'REJECT' or 'DROP' rule for ipset.
Make sure you do not lock yourself out in case of issues on a remote system.

In case of issues check the log file (/var/log/ipset-country.log)

Configuration
-------------

***All options are set and explained in the script itself: [ipset-country](ipset-country)***

Optionally you can use a seperate config file located in the same directory as the script, "/etc" or "/usr/local/etc". Specify a custom location using `ipset-country -c /path/to/conf`

The config file will overwrite any options set in script. To create a new conf file run:

``` bash
sed -n '/# CONFIGURATION:/,/# END OF CONFIG/p' ipset-country > ipset-country.conf
```

---

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

**Firewalls and options:** 

Iptables and ipset are used by default to create the chains, rules and ipsets. If firewalld frontend is enabled it will be used instead.

- Blacklist: block specified Countries, set `MODE` to "reject" or "drop"
- Whitelist: allow specified Countries and block all others, set `MODE` to "accept"

Iptables:

Set target to use when ip matches country: "accept", "drop" or "reject". Default is `MODE="reject"`

FirewallD:

Set this option to "1" to enable firewalld: `FIREWALLD=0`

Set `FIREWALLD_MODE=0` to use the default Blacklist mode (uses 'drop' zone). Change to "1" for Whitelist ('public' zone). _See MODE above for more information_

* _NOTE:_
There are issues with firewalld on CentOS/RHEL 8 which can cause your firewall to break resulting in being locked out. Adding large ipsets apparently can take a VERY long time. To abort you need remote console access and run `pkill firewal-cmd; nft flush ruleset`

---

**Block list providers:**

Set URLs for ipv4 and/or ipv6 block files, you probably do not have to change these.  
To use [ipverse.net](http://ipverse.net) instead of [ipdeny.com](https://ipdeny.com) and for more details see [script](ipset-country)

- `IPBLOCK_URL_V4="http://www.ipdeny.com/ipblocks/data/aggregated"`
- `IPBLOCK_URL_V6="http://www.ipdeny.com/ipv6/ipaddresses/blocks"`

---

**Logs:**  
In case you want to change file location set: `LOG="/var/log/ipset-country.log"`

---

IPset
------

Useful ipset commands:

- `ipset list`
- `ipset test setname <ip>`
- `ipset flush`
- `ipset destroy`

Changes
-------

- [20220227] fixed iptables-legacy paths (pr #16 by mainboarder)
- [20201212] added config file option, systemd install (pr #14 by srulikuk)
- [20201108] added flush option, fix restore=0 (pr #13 by srulikuk)
- [20200927] fixed restore + logips bug (pr #10 by G4bbix)
- [20200605] added Blacklist/Whitelist mode (#3)
- [20200129] added option to DROP instead of REJECT (#1)
- [20191116] added ipverse support, md5check option
- [20190905] tested on debian 10 and centos 7
- [20190905] blocking multiple countries should work
- [20190905] it will check if INPUT chain exists in iptables
- [20190905] cleaned it up a bit
- [20190905] using firewalld is also supported now

Alternatives
------------

Also available: [github.com/tokiclover/dotfiles/blob/master/bin/ips.bash](https://github.com/tokiclover/dotfiles/blob/master/bin/ips.bash)

