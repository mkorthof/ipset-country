# ipset-country
## Block countries using iptables + ipset + ipdeny.com

Supports RH, Debian with iptables and/or firewalld - both ipv4 and ipv6

This used to be a [gist](https://gist.github.com/mkorthof/3033ff64c4a5b4bd31336d422104d543) but was moved here instead

INSTALLATION:
-------------

- setup firewall if you have not done so yet, at least INPUT chain
- run this script from cron, e.g. /etc/cron.daily
- to run on boot you can also add it to e.g. /etc/rc.local or systemd
- use argument "force" to load unchanged zonefiles instead of skipping

NOTE: this script will insert an iptables REJECT rule for ipset

Also available, and more extensive:
[github.com/tokiclover/dotfiles/blob/master/bin/ips.bash](https://github.com/tokiclover/dotfiles/blob/master/bin/ips.bash)

CONFIGURATION:
--------------

Set OS using `DISTRO` setting (default is "auto)"

- "auto"
- "debian"
- "redhat"
- "manual"
  - `confdir="/etc/iptables"` (example)
  - `rulesfile="${confdir}/myrules"`  (example)

---

Specify countries to block as "ISOCODE,Name" (same as ipdeny.com), multiple entries should be seperated by semicolon ";"

Example:

`COUNTRY="CN,China; US,United States; RU,Russia"`

---

Set to "1" to enable firewalld:

`FIREWALLD=0`

---

Urls for ipv4 and/or ipv6 block files, you probably do not have to change these:

`IPDENY_URL="http://www.ipdeny.com/ipblocks/data/aggregated"`
`IPDENY_URL_6="http://www.ipdeny.com/ipv6/ipaddresses/blocks"`

---

Log file location:

`LOG="/var/log/ipset-country.log"`

---

Other options are explained in [script](ipset-country)

IPSET:
------

Useful ipset commands:

- `ipset list`
- `ipset test setname <ip>`
- `ipset flush`
- `ipset destroy`

CHANGES:
--------

[20190905] tested on debian 10 and centos 7
[20190905] blocking multiple countries should work
[20190905] it will check if INPUT chain exists in iptables
[20190905] cleaned it up a bit
[20190905] using firewalld is also supported now