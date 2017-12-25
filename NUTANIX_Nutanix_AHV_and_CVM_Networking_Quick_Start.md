## Nutanix AHV and CVM Networking Quick Start

This document will help separate the 10Gb interfaces from the 1Gb interfaces onto different bridges. Sometimes this is requested to isolate VM traffic to just the 1Gb interfaces leaving just the Hypervisor and CVM traffic to the 10Gb network. Also, we will add each Hypervisor host and Controller Virtual Machine to VLAN 200. (Note, and this is mentioned later in the post, Nutanix recommends leaving CVM and AHV traffic on a native VLAN / no tagging as this makes future cluster expansion easier)

First login to each CVM individually, I recommend using the console as several of the steps below can result in lost network connectivity to the CVM.

    nutanix@cvm:~$: ssh root@192.168.5.1 (192.168.5.1 is the internal IP address to AHV on each node accessible via KVM regardless of network connectivity)

Add the `br1` bridge:

    nutanix@cvm:~$: ovs-vsctl add-br br1
    
Do this to each host in the cluster if you login to each AHV individually

By default, all interfaces are part of Bridge 0, we need to exclude the 1Gb ports before adding them to the new br1 bridge.

Remove the 1Gb links from bond0

    nutanix@cvm$ allssh manage_ovs – -bridge_name br0 – -bond_name bond0 – -interfaces 10g update_uplinks

You may have to do this per host via KVM if this breaks the connection because of removing the 1Gb link from the bond. During foundation install, the 1Gb link is used, removing this link from the bond will subsequently result in lost connection to the CVM. So from a KVM console execute the following: 

    manage_ovs – -bridge_name br0 – -bond_name bond0 – -interfaces 10g update_uplinks per host.

This keeps the 10Gb ports on bridge `br0`, while excluding the 1Gb Nics. We will add 1Gb nice to bridge `br1` next.

Create the new bridge bond for the 1Gb interfaces (again, you’ll have to do this per host via KVM if you CVM’s lose connectivity via the 1Gb links)

    nutanix@cvm$ manage_ovs – -bridge_name br1 – -bond_name bond1 – -interfaces 1g update_uplinks (do this to each host)

Nutanix’s recommended configuration is to keep the CVM and Hypervisor on the native VLAN. However in certain situations native VLAN’s may not be allowed per company policy. If that is the case, you need to make sure BOTH CVM and Hypervisor reside in the same VLAN.

We will now update br0 to have the appropriate VLAN tag for the 10Gb uplinks for both the CVM and AHV. (We’ll just call this VLAN 200)

Do the following for each node in the cluster

     nutanix@CVM$ ssh root@192.168.5.1 “ovs-vsctl set port br0 tag=200”
     nutanix@CVM$ ssh root@192.168.5.1 “ovs-vsctl list port br0”
