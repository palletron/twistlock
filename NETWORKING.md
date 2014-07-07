Networking
========

So the way I would like networking to work is that every twistlock container perceives network services as available on localhost. The way this would be achieved is that twistlock employs iptables NAT rules to expose the services of other containers on the local container interface. Connections to remote machines could be done through ambassador containers that establish tunnels, which would be linked into the consumer containers using the same iptables NAT strategy.

Detail
======

To launch a container with preconfigured links, we must first launch a network-only container. We make all the links the container needs to this container. Then we launch the actual container with the `--net=container:container_name` option to have it inherit the network interface of the network-only container.
