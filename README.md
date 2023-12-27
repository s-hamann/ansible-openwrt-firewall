OpenWrt Firewall
================

This role handles firewall (fw4) and routing configuration on [OpenWrt](https://www.openwrt.org/) targets.

Requirements
------------

This role has no special requirements on the controller.

It does, however, require a working [Python](https://www.python.org/) installation on the target system or [gekmihesg's Ansible library for OpenWrt](https://github.com/gekmihesg/ansible-openwrt) on the Ansible controller.

Role Variables
--------------

* `openwrt_firewall_config`  
  A dictionary that describes the global firewall configuration.
  Refer to [the documentation](https://openwrt.org/docs/guide-user/firewall/firewall_configuration#defaults) for information about valid options.
* `openwrt_firewall_zones`  
  A list of firewall zones to configure. List items are dictionaries that describe the zone configuration.
  All keys are simply passed to the OpenWrt zone configuration.
  Refer to [the documentation](https://openwrt.org/docs/guide-user/firewall/firewall_configuration#zones) for information about valid and required options.
* `openwrt_firewall_forwardings`  
  A list of firewall forwardings to configure. List items are dictionaries that describe the forwarding configuration.
  All keys are simply passed to the OpenWrt forwarding configuration.
  Refer to [the documentation](https://openwrt.org/docs/guide-user/firewall/firewall_configuration#forwardings) for information about valid and required options.
* `openwrt_firewall_enable_default_rules`  
  Whether or not to set up OpenWrt's default firewall rules before the rules in `openwrt_firewall_rules`.
  Defaults to `true`.
* `openwrt_firewall_rules`  
  A list of firewall rules to configure. List items are dictionaries that describe the rule configuration.
  All keys are simply passed to the OpenWrt rule configuration.
  Refer to [the documentation](https://openwrt.org/docs/guide-user/firewall/firewall_configuration#rules) for information about valid and required options.
* `openwrt_firewall_redirects`  
  A list of firewall redirects to configure. List items are dictionaries that describe the redirect configuration.
  All keys are simply passed to the OpenWrt redirect configuration.
  Refer to [the documentation](https://openwrt.org/docs/guide-user/firewall/firewall_configuration#redirects) for information about valid and required options.
* `openwrt_firewall_ipsets`  
  A list of firewall IP sets to configure. List items are dictionaries that describe the IP set configuration.
  All keys are simply passed to the OpenWrt IP set configuration.
  Refer to [the documentation](https://openwrt.org/docs/guide-user/firewall/firewall_configuration#ip_sets) for information about valid and required options.
* `openwrt_firewall_drop_in_includes`  
  A list of firewall drop-in includes to configure.
  These includes are loaded at the beginning of the fw4 table.
  Refer to [the documentation](https://openwrt.org/docs/guide-user/firewall/firewall_configuration#default_drop-in_includes) for more information.
  Each list item is a dictionary with the following keys:
  * `name`  
    The name (without extension) of the file in `/etc/nftables.d/` to write the content to.
    Mandatory.
  * `src`  
    Path to the include file on the Ansible controller that is to be copied to the target system.
  * `content`  
    Content of the include file.
    Exactly on of `src` and `content` must be set.
* `openwrt_firewall_includes`  
  A list of firewall includes to configure. List items are dictionaries that describe the include configuration.
  Most keys are simply passed to the OpenWrt include configuration.
  Refer to [the documentation](https://openwrt.org/docs/guide-user/firewall/firewall_configuration#includes_2203_and_later_with_fw4) for information about valid and required options.
  The special key `content` is not written to the include configuration.
  Instead, it is written to the file indicated by the `path` key.
  The included script can therefore be kept in the Ansible configuration.
  If it is stored as a separate file, use the `ansible.builtin.file` lookup.

Dependencies
------------

This role does not depend on any specific roles.

However, it was written for fw4 and therefore supports only OpenWrt 22.03 or newer.
Older versions may or may not work.

Example Configuration
---------------------

The following is a short example for some of the configuration options this role provides:

```yaml
openwrt_firewall_config:
  drop_invalid: true
  synflood_rate: 50/s
openwrt_firewall_zones:
  - name: wan
    network:
      - wan
      - wan6
    input: REJECT
    output: ACCEPT
    forward: REJECT
    masq: true
    mtu_fix: true
  - name: guest
    network: guest
    input: REJECT
    output: ACCEPT
    forward: REJECT
  - name: lan
    network: lan
    input: REJECT
    output: ACCEPT
    forward: REJECT

openwrt_firewall_forwardings:
  - src: guest
    dest: wan
  - src: lan
    dest: wan
```

License
-------

MIT
