# About

This is a bash script for rate limiting throughput to and from a remote host on your machine - and optionally and outgoing TCP and UDP ports too (But not incoming for those two... yet).

It is intended to be placed/symlinked into /etc/NetworkManager/dispatcher.d/ as an executable .sh file so that every time the network state changes it can check if it needs to be rate limiting communications to and from remote hosts or not.

The configuration is pretty simple.

It

* Makes an IFB interface for and named after each physical interface on the host

* Creates a qdisc on the new IFB interface and the host interface.

    * The physical interface's default root qdisc has two child classes, one set to 10000mbps (This might not be needed at all... actually...) and a child of that one which sets the by-default 20mbps limit defined in config.sh.

    * The IFB interface is configured nearly the same but with only one child of which the only purpose is o set the by-default 20mbps limit. This is where return traffic from remote IPs gets thrown to, ensuring they don't exceed the limit on the way in.

* The configured IPs will be rate limited on both egress and ingress with the help of the IFB device.

* The configured ports will be put on the second class of the host's qdisc which is the one with the by-default 20mbps limit.

Placing the script (Or symlinking it) into /etc/NetworkManager/dispatcher.d/ as executable and restarting the NetworkManager service is enough to start using it for throughput-sensitive networks.

This is more or less a stub commit of something I'd like to work much more on later. Many years ago I thought you had to use a combination of tc and iptables to tag things into the correct classes, but it seems tc can do all of that on its own albeit with a syntax describing packet types at a pretty low level.
