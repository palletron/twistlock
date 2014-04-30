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
3. The description file describes the resources that the container provides,
and the resources that the container consumes.

