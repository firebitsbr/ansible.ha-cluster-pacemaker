ha-cluster-pacemaker
=========

Role for configuring and expanding basic pacemaker cluster on CentOS/RHEL 6/7 and Fedora 28 systems.

Experimental support for Fedora 29 and RHEL 8 Beta with pcs-0.10 when using OndrejHome.pcs-modules-2 from develop branch.

Requirements
------------

RHEL: It is expected that machines will already be registered and subscribed for access to 'High Availability' or 'Resilient storage' channels.

Fedora: Don't forget to set `ansible_python_interpreter=/usr/bin/python3` for Fedora hosts as shown in example inventory at the end of this README. On Fedora systems this role uses Python 3.

RHEL 8: Don't forget to set `ansible_python_interpreter=/usr/libexec/platform-python` for RHEL 8 hosts as shown in example inventory at the end of this README. On RHEL 8 systems this role uses python from system (Python 3)

This role requires at least version `2.9` of `python-jinja2` library. When this role is run from RHEL/CentOS 7 system you may encounter issue with old `python-jinja2` package described in Issue #6. To get the updated version of python-jinja2 and its dependencies you can use following RPM repository - https://copr.fedorainfracloud.org/coprs/ondrejhome/ansible-deps-el7/.

Role Variables
--------------

  - user used for authorizing cluster nodes

    ```
    cluster_user: 'hacluster'
    ```

  - password for user used for authorizing cluster nodes

    ```
    cluster_user_pass: 'testtest'
    ```

  - group to which cluster user belongs (should be 'haclient')

    ```
    cluster_group: 'haclient'
    ```

  - name of the cluster

    ```
    cluster_name: 'pacemaker'
    ```

  - configuration of firewall for clustering, NOTE in RHEL/Centos 6 this replaces iptables configuration file!
  
    ```
    cluster_firewall: true
    ```

  - enable cluster on boot
  
    ```
    cluster_enable_service: true
    ```

  - configure cluster with fence_xvm fencing device ?
    This will copy /etc/cluster/fence_xvm.key to nodes and add fencing devices to cluster
    NOTE: you need to define 'vm_name' in the inventory for each cluster node
  
    ```
    cluster_configure_fence_xvm: true
    ```

  - configure cluster with fence_kdump fencing device ?
    This starts kdump service and defines the fence_kdump stonith devices.
    NOTE: if the kdump service is not started this won't work properly or at all
  
    ```
    cluster_configure_fence_kdump: false
    ```

  - (RHEL only) enable the repositories containint packages needed
    ```
    enable_repos: true
    ```

  - (RHEL only) enable the extended update (EUS) repositories containint packages needed
    ```
    enable_eus_repos: false
    ```

  - (RHEL only) enable the SAP Solutions update service (E4S) repositories containint packages needed
    ```
    enable_e4s_repos: false
    ```

  - (RHEL only) enable Beta repositories containint packages needed
    ```
    enable_beta_repos: false
    ```

  - (RHEL only) type of enable repositories, note that E4S repos have only 'ha' type available
    - ha - High-Availability
    - rs - Resilient Storage
    ```
    repos_type: 'ha'
    ```

  - (RHEL only) custom_repository allows enabling an arbitrarily named repository to be enabled.
    RHEL8 repo names can be found at http://downloads.redhat.com/redhat/rhel/rhel-8-beta/rhel-8-beta.repo
    ```
    custom_repository: ''

    ```

  - (CentOS only) install the needed packages from the CD-ROM media available at /dev/cdrom
    ```
    use_local_media: false
    ```

  - Enable or disable PCSD web GUI. By default the role keeps the default of installation means that
    PCSD web GUI is disabled on CentOS/RHEL 6.X and enabled on CentOS/RHEL 7.X. `true` or `false` can
    be passed to this variable to make sure that PCSD web GUI is enabled or disabled.
    ```
    enable_pcsd_gui: 'nochange'
    ```

  - Cluster transport protocol. By default this role will use what is default for give OS.
    For CentOS/RHEL 6.X this means 'udp' (UDP multicast) and for CentOS/RHEL 7.X this means 'udpu'
    (UDP unicast). This variable accepts following options: `default`, `udp` and `udpu`.
    ```
    cluster_transport: 'default'
    ```

  - Allow adding nodes to existing cluster when used with OndrejHome.pcs-modules-2 v16 or newer.
    ```
    allow_cluster_expansion: false
    ```

Example Playbook
----------------

Example playbook for creating cluster named 'test-cluster' enabled on boot, with fence_xvm and firewall settings

    - hosts: servers
      roles:
         - { role: 'OndrejHome.ha-cluster-pacemaker', cluster_name: 'test-cluster' }

Example for creating cluster named 'test-cluster' without configuring firewalling and without fence_xvm.
For cluster to get properly authorize it is expected that firewall is already configured or disabled.

    - hosts: servers
      roles:
         - { role: 'OndrejHome.ha-cluster-pacemaker', cluster_name: 'test-cluster', cluster_firewall: false, cluster_configure_fence_xvm: false }

Inventory file example for CentOS/RHEL and Fedora systems.

    [cluster-el]
    192.168.22.21 vm_name=fastvm-centos-7.6-21
    192.168.22.22 vm_name=fastvm-centos-7.6-22
    [cluster-fedora]
    192.168.22.23 vm_name=fastvm-fedora28-23 ansible_python_interpreter=/usr/bin/python3
    192.168.22.24 vm_name=fastvm-fedora28-24 ansible_python_interpreter=/usr/bin/python3
    [cluster-rhel8-beta]
    192.168.22.25 vm_name=fastvm-rhel-8.0-beta-25 ansible_python_interpreter=/usr/libexec/platform-python
    192.168.22.26 vm_name=fastvm-rhel-8.0-beta-26 ansible_python_interpreter=/usr/libexec/platform-python


License
-------

GPLv3

Author Information
------------------

WARNING: this is alpha-version quality proof-of concept role that still needs some polishing and is using custom modules 
         to interact with pacemaker through python. This is suitable for testing purposes only.

To get in touch with author you can use email ondrej-xa2iel8u@famera.cz or create a issue on github when requesting some feature.
