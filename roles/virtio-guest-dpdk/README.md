VirtIO-guest-dpdk
=================

This role configures a guest to run a dpdk application using vfio-pci drivers (no IO-MMU).
The dpdk application that is run is "testpmd" and it's run inside a detached tmux session called "guest-testpmd"


Requirements
------------

The guest must have been configured with virtio-compatible devices

Role Variables
--------------

- guest_cmdline_options: The kernel's cmdline options that will be configured in the guest. Default "*default_hugepagesz=1G hugepagesz=1G hugepages=1 intel_iommu=on iommu=pt isolcpus=1,2 rcu_nocbs=1,2 nohz_full=1,2*"

- nics: The list of NICs to use in the dpdk application specified by their PCI addresses


Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

    - hosts: guest
      roles:
         - role: virtio-guest-dpdk
           vars:
             nics:
               - "0000:00:10.0"
               - "0000:00:11.0"

Limitations
-----------

* If run against a freshly installed guest, no ssh key will be configured.

TODOs
-----

* Make testpmd options and script configurable. That will allow more complex tests and scenarios
* Add more architectures (e.g: io-mmu)
* Run a specific version (packet version or git ref) of dpdk