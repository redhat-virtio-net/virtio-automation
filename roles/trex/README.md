trex
====

This role installs and configures a [T-Rex](https://trex-tgn.cisco.com/) packet generator in a host.

The install path is /opt/t-rex

Requirements
------------

The target server must be properly connected via two NICs to the DUT. The NIC PCI address can be specified via arguments to the role.

Role Variables
--------------

* nics: a list of nics to use by the packet generator (specified by their PCI addresses)
* trex_url: The URL where the t-rex code will be downloaded from. Default: ttp://trex-tgn.cisco.com/trex
* trex_version: The version of t-rex to download. Default: latest



Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

    - hosts: generator
      roles:
         - role: trex
           vars:
             nics
               - "0000:00:10.0"
               - "0000:00:11.0"
TODOs
-----

* Automatically configure and run a specific test (and retrieve results)
