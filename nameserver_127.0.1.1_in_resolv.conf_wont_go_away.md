
NetworkManager is the program which (via the resolvconf utility) inserts address `127.0.1.1` into `resolv.conf`. 
`NM` inserts that address if an only if it is configured to start an instance of the `dnsmasq` program to serve 
as a local forwarding nameserver. That `dnsmasq` instance listens for queries at address `127.0.1.1.`

If you do not want to use a local forwarding nameserver then configure `NetworkManager` not to start a `dnsmasq` 
instance and not to insert that address. In `/etc/NetworkManager/NetworkManager.conf` comment out the line 

`dns=dnsmasq`

	sudo vi /etc/NetworkManager/NetworkManager.conf

	[main]
	plugins=ifupdown,keyfile,ofono
	#dns=dnsmasq

and restart the NetworkManager service.

	sudo service network-manager restart

In this mode, `NetworkManager` updates `/etc/resolv.conf` (still via resolvconf) to include the nameserver 
addresses NetworkManager has for active connections.

If you want to disable the `resolvconf` mechanism for updating `resolv.conf` and just use a static 
`resolv.conf` file, do the following.

	sudo rm -f /etc/resolv.conf  # Delete the symbolic link
	sudo nano /etc/resolv.conf   # Create static file

	# Content of static resolv.conf
	nameserver 8.8.4.4
	nameserver 8.8.8.8
