# A Quagga-Based RPKI-Enabled Router

## Configure

Installing and running should be as easy as:

1. Install VirtualBox
2. Install Vagrant
3. Clone this repository:
```
	# git clone https://github.com/carlosm3011/ops-quagga-rpki-router ops-quagga-rpki-router.git
```
3. cd ops-quagga-rpki-router.git
4. run "vagrant up"

## Run the router

1. Connect with "vagrant ssh" to the recently-created box
2. Start bgpd with
```
	# bgpd -d &
```
3. Check it's working correctly
   The bgpd daemon will take a few seconds to properly start up and connect to the RPKI Cache.  After it has sucessfully booted, try the following commands (on the running virtual machine):
```
	# vtysh
	# show rpki prefix-table
	# show rpki cache-connection
```

## Author and Contact Information
