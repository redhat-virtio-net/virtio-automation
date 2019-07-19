VirtIO-host
==========

This role prepares a host to run a virtio benchmark. It performs the following tasks:

 - Configure the host correctly:
 -  - setting hugepages in the kernel's cmndline
 -  - Installs dependencies
 - Runs testpmd in a detached tmux session with 4 ports:
 - - ports 0-1: vhost-user virtual devices to be used by a VM
 - - ports 2-3: NICs connected through vfio driver
 - - In order to attach to the testpmd console ssh to the host and run "tmux attach-session {session_name}" (e.g: *tmux attach-session host-testpmd*)
 - Run a guest VM:
 - - Downloads a base image, cleans it and boots a guest VM configured with the
appropriate virtio-vhost interfaces.
 - - A ssh key is injected to the root user. The path of such key must be set as variable
 - - It uses a specific libvirt network to have management access to the guest (i.e: ssh).
DHCP host reservation is used to fix the IP address and a hook is installed to enable port-forwarding, hence the guest should be accessible via ssh on {host}:2222

In order to use this role, a ssh key must be created for the guest:

    $ ssh-keygen -t rsa -N "" -f my.key
    $ cat > benchmark.yml <<EOF
    ---
    - hosts: hosts
      tasks:
        - import_role:
            name: virtio-host
          vars:
            nics:
              - "0000:04:00.0"
              - "0000:04:00.1"
            guest_pubkey: guest_id_rsa.pub
    EOF
    $ ansible-playbook benchmark.yml


Requirements
------------

The host must have two dpdk-compatible NICS

Role Variables
--------------

Guest Variables:

* remote_image: URL of a base qcow2 image to use as guest
* local_image_file: path of the local file image file
* guest_mgt_mac: The MAC address to configure in the guest for management interface
* guest_mgt_ip: the IP address to assign the guest management interface.
Currently the network configuration is not editable so the IP must be in the range 192.168.200.0/24
* guest_pubkey: The path to the public key that will be injected to the guest on creation

Host Variables:

* cmdline_options: The kernel's command line options. Default:
"*intel_pstate=disable mce=ignore_ce default_hugepagesz=1G hugepagesz=1G hugepages=6 isolcpus=2-7 rcu_nocbs=2-7 nohz_full=2-7 iommu=pt intel_iommu=on*"
* nics: A list of NICS (defined by their PCI address) to use by the hosts's dataplane
* testpmd_session: The testpmd dataplane in the host runs inside a tmux session that can
be reattached for debugging purposes. This variable specifies the name of the session



Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

    - hosts: servers
      roles:
         -role: virtio-host
          vars:
            nics:
              - 0000:02:10.0
              - 0000:02:10.1
            remote_image: https://example.com/rhel8.1.x86_64.qcow2



TODOs
------------------

* Make it possible to download and build dpdk and qemu so that we don't necessarily use those in the repository
* Right now the CPUs that the host and guest use for their dpdk processes are hardcoded. In order to be more flexible, we should make it configurable according to the host's capabilities, the number of guests, etc
* Add more guests
* prepare.yml: There are more system-specific configuration that should be automated in  [this reference architecture](http://doc.dpdk.org/guides/howto/pvp_reference_benchmark.html)
* 
