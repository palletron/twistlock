Twistlock
=========
Container management system
---------------------------

The idea of Twistlock is to provide an abstract interface for running
containers and linking them to external services and resources.

Interface
---------
A container interface consists of a control script and a description file.
The control script must implement the following commands:

1. The build command prepares the container for running. It takes no
parameters.
2. The start command runs the container, it reads in a JSON file with
runtime configuration information like the resource mount points.
3. The stop command stops the container.
4. The link command configures the container to connect to given IP endpoints for
services it consumes.

The description file describes the resources that the container provides,
and the resources that the container consumes.

Provisioner
------

The Twistlock provisioner listens for JSON-rpc requests given to it over a socket.
It will be capable of the following actions:

1. Add container description. This command makes the service download or receive
a container description and add it to its internal lists of containers it can provision.
2. Build container. This makes the provisioner invoke the build script of a matching
container description.
3. Run container. This makes the provisioner launch the container. This command requires
the parameters specified in the container description file to be specified in the request.
The provisioner will respond with a container identifier for the running container.
4. Stop container. Invokes the stop container script for that container instance.
5. Link container. This configures a container to connect to a service on a given ip address
and port.
6. Container status. This returns whether an instance of the container is running at the moment.

Question: should the provisioner maintain state? It already retains state in the form of available
container templates, and should also expose state in the form of whether containers are running.
We could expand this to also expose lists of running containers, and their link states. The
provisioner would have to store this somehow. Ideally it could recover all information from the
control script, but then a control script would have to store information as well, or have
functionality to discover instances of itself.

Abstraction
------
Twistlock abstracts over container provisioning systems like Docker. The main motivation
for the abstraction is to separate the process of launching and controlling running containers
from the specification and building of the containers. The result should be a small generic
interface that is easy for container management systems to implement and reason with.

The interface should only cover details that are visible to the outside of containers.

Runtime reconfiguration
------
A common situation is that the ip addresses of services change. Ideally the containers can be
updated of these changes without having to be turned off and on again. For lxc containers this
feature is easy enough to implement. 

I am wondering if runtime reconfiguration should be possible for just network links, or for all
variables specified in the description file. For mounts this might be possible, but probably
dangerous, and for environment variables this does not seem to be possible at all.

So for now the conclusion I am going to make is that it is only reasonable for network links,
so I am going to add a link action to the list of actions. If in the future other runtime
configuration options also seem suitable they might get their own actions or if it’s decided
that there are more than one of them they might be grouped in a reconfigure command.

Twistlock Management System
========================

The Twistlock management system is a web user interface that allows users to easily add new container
templates and to build run and link them. There will be a single web backend that can talk to multiple
twistlock provisioners. Information about the current state and organisation of the container instances
is stored in a database. Possibly the system is also authenticated.

System Organisation
------------------

A system is a group of containers working together, like a Ruby on Rails application that uses a Postgres
SQL container for persistance, a Redis container for caching and a Sidekiq container for tasks, which also
links to a Redis container to maintain its queue. Twistlock will let you define a template for this whole
system of containers so the system can be replicated, but also so that it can be scaled. Each node
in a system template could also be a system. Perhaps the Postgres node is not a single container but in
fact a replicated and sharded postgres cluster of containers, spread over multiple datacenters.

For each twistlock provisioner the user can define roles and resources. For example a twistlock provisioner
can have the ‘db-server’ role, and have associated with it a data directory that is mounted on high performance
SSD drives in a RAID. Associated with a role and its resources can perhaps be a provisioning script that could
automatically provision a new machine with the required resources, and run on it a twistlock provisioner tagged
with that role.

The previous two concepts are combined to allow the user to provision hardware, initialize its resources and tagging
it with roles and then starting the system templates by assigning them to the servers with the matching roles.
