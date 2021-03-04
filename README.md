Ansible Role: systemd-networkd
==============================

Ansible role to configure systemd-networkd profiles.

Role Variables
--------------

```yaml
# link profiles
systemd_networkd_link: {}

# netdev profiles
systemd_networkd_netdev: {}

# network profiles
systemd_networkd_network: {}

# does the role have to restart systemd-networkd to apply the new profiles ?
systemd_networkd_apply_config: false

# enable or not systemd_resolved
systemd_networkd_enable_resolved: true

# remove configuration files matching a prefix
systemd_networkd_cleanup: false
systemd_networkd_cleanup_prefix: ''
systemd_networkd_cleanup_prefix_is_regex: false
```

Dependencies
------------

None

Example Playbook
-------------------------

1) Configure a network profile

```yaml
systemd_networkd_network:
  eth0:
    - Match:
      - Name: "eth0"
    - Network:
      - DHCP: "no"
      - IPv6AcceptRouterAdvertisements: "no"
      - DNS: 8.8.8.8
      - DNS: 8.8.4.4
      - Domains: "your.tld"

      - Address: "192.0.2.176/24"
      - Gateway: "192.0.2.1"

      - Address: "2001:db8::302/64"
      - Address: "fc00:0:0:103::302/64"
      - Gateway: "2001:db8::1"
```

It will create a `eth0.network` profile in `/etc/systemd/network/`, and enable
`systemd-networkd` and `systemd-resolved`.

Every key under `systemd_networkd_*` corresponds to the profile name to create
(`.network` in `systemd_networkd_network`, `.link` in systemd_networkd_link,
etc…). Then every key under the profile name is a section documented in
systemd-networkd, which contains the couples of `option: value` for your
profile. Each couple is then converted to the format `option=value`.

2) Configure a bonding interface

```yaml
systemd_networkd_netdev:
  bond0:
    - NetDev:
      - Name: "bond0"
      - Kind: "bond"
    - Bond:
      - Mode: "802.3ad"
      - LACPTransmitRate: "fast"

systemd_networkd_network:
  bond0:
    - Match:
      - Name: "eth*"
    - Network:
      - DHCP: "yes"
      - Bond: "bond0"
```

What will create an LACP bond interface `bond0`, containing all interfaces
starting by `eth`.

License
-------

Tool under the BSD license. Do not hesitate to report bugs, ask me some
questions or do some pull request if you want to!
