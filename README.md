Rotterdam
=========
Container management system
---------------------------

The idea of Rotterdam is to provide an abstract interface for running
containers and linking them to external services and resources.

Interface
---------
A container interface consists of a group of scripts and a description file:

1. The build script prepares the container for running. It takes no
parameters.
2. The start script runs the container, it reads in a JSON file with
runtime configuration information like the IP endpoints of external
services.
3. The stop script stops the container.
4. The description file describes the resources that the container provides,
and the resources that the container consumes.

System
------

The Rotterdam system listens for JSON-rpc requests given to it over a socket.
It will be capable of the following actions:

1. Add container description. This command makes the service download or receive
a container description and add it to its internal lists of containers it can provision.
2. Build container. This makes the system invoke the build script of a matching
container description.
3. Run container. This makes the system launch the container. This command requires
the parameters specified in the container description file to be specified in the request.
The system will respond with a container identifier for the running container.
4. Stop container. Invokes the stop container script for that container instance.
