# ipset-country

 Block or allow countries using iptables, ipset and ipdeny.com

- [x] Supports RH and Debian with iptables, nftables and firewalld
- [x] Also works with ipverse.com and other block list providers
- [x] Both ipv4 and ipv6 are supported

## Installation

Setup firewall first if you have not done so yet, **at least an input chain** is needed.

Then run this script manually and if all is well, add to cron (e.g. /etc/cron.daily) or systemd service.

To automatically setup a daily systemd [timer](https://www.freedesktop.org/software/systemd/man/latest/systemd.timer.html), run `ipset-country -i`.

To uninstall, run: `ipset-country -u`.

Or to run script on boot, add it to rc (e.g. '/etc/rc.local').

> Running this script will add rules to your firewall. Make sure you do not lock yourself out in case of issues on a remote system.

## Configuration

Note that **all options** and settings are explained **in the script itself**, see [ipset-country](ipset-country)

Optionally, you can use a separate config file located in the same directory as the script. To specify a custom location use `ipset-country -c /path/to/conf` (like "/etc/ipset-country.conf" or "/usr/local/etc/ipset-country.conf".)

The config file will overwrite any options set in script. To create a new conf file, run:

``` bash
sed -n '/# CONFIGURATION:/,/# END OF CONFIG/p' ipset-country > ipset-country.conf
```

### Distro

If needed, change OS using `DISTRO` setting. Default is "auto", which should usually work OK.

Options are:
- "auto", "debian" or "redhat"
- "manual"
  - `confdir="/etc/iptables"` (example)
  - `rulesfile="${confdir}/myrules"` (example)

### Countries

Specify countries to block as `"ISOCODE,Name"` (same as ipdeny.com), multiple entries should be separated by semicolon `;`

Example:
`COUNTRY="CN,China; US,United States; RU,Russia"`

### Logs

In case of issues check the log file '/var/log/ipset-country.log'.

To change log file location, set: `LOG="/path/to/log"`

Disable all logging: `LOG="/dev/null 2>&1"`

Or, log to screen only: `LOG="/dev/stdout"`

## Firewalls and options

Set option `FIREWALL` to: "iptables", "ntftables" or "firewalld"

Default is "iptables"

To block specified Countries, set `MODE` to target "reject" or "drop"  (blacklist).

To allow specified Countries and block all others, set `MODE` to "accept" (whitelist).
Default is "reject".

### Iptables

The script will add iptables chains, rules and sets (needs `ipset`).

Change `DENY_RULENUM` to "1" to insert 'deny' rule at beginning of existing rules.

Or, set a specific rule number (see `iptables --numeric -L INPUT --line-numbers`)

Default is 0 (add at end).

### NFTables

Uses nft with native sets. Needs at least a table and "input" chain already setup.

Minimal ipv4 example:

```
nft add table ip filter
nft add chain ip filter input \{ type filter hook input priority 0\; \}
```

```
table ip filter {
    chain input {
      type filter hook input priority filter; policy accept;
      # ...
    }
  }
```

To use optional location specifier of an existing rule set `NFT_RULE_LOC`. Default is empty/unset (`""`), which appends.

Example: `NFT_RULE_LOC="handle 3"` or `NFT_RULE_LOC="index 5"`

See `nft --handle list ruleset` or `man nft` for more details.

### FirewallD:

FirewallD does not support `LOGIPS=1` or `MODE='reject"`

Set `MODE` to "drop" or "accept":

 - if mode is "accept", ipset will be added to "drop zone" as allowed (whitelist)
 - if mode is "drop", ipset will added to "public zone" as denied (blacklist)

>  There are issues with firewalld and nft on CentOS/RHEL 8 which can cause your firewall to break resulting in being locked out. Adding large ipsets apparently can take a VERY long time.
To abort, you need remote console access and run: `pkill firewal-cmd; nft flush ruleset`

### UFW

> Unsupported frontend. Apparently it is possible to run both iptables and ufw and mix rules. Enable with `UFW=1` (untested).

## Block list providers

Set URLs for ipv4 and/or ipv6 block files, you probably do not have to change these.  

By default [ipdeny.com](https://ipdeny.com) is used

```
IPBLOCK_URL_V4="http://www.ipdeny.com/ipblocks/data/aggregated"
IPBLOCK_URL_V6="http://www.ipdeny.com/ipv6/ipaddresses/blocks"
```

To change to [ipverse](https://github.com/ipverse/rir-ip), set:

```
IPBLOCK_URL="https://raw.githubusercontent.com/ipverse/rir-ip/master/country"
```

Add argument `-f` to load unchanged zonefiles instead of skipping

For more details see inside script.

## Commands

Useful commands to check and clear blocked ips

### ipset

- `ipset list`
- `ipset test setname <ip>`
- `ipset flush`
- `ipset destroy`

### nft

- `nft --handle list ruleset`
- `nft list table ip filter`
- `nft list sets`
- `nft list set ip filter <proto>-<country>`  (e.g. 'ipv4-china')
- `nft list chain ip filter input`
- `nft flush set ip filter <proto>-<country>`
- `nft delete set ip filter <proto>-<country>`

## Changes

- [20250729] add suport for nftables
- [20250721] add option to inject rejct rule on specific rulenum (pr #22 by miathedev)
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

## Alternatives

Also available: [github.com/tokiclover/dotfiles/blob/master/bin/ips.bash](https://github.com/tokiclover/dotfiles/blob/master/bin/ips.bash)


---

_This used to be a [Gist](https://gist.github.com/mkorthof/3033ff64c4a5b4bd31336d422104d543) but was moved here instead_
_Please do not add Gist comments, but create an issue [here](https://github.com/mkorthof/ipset-country/issues)_

---