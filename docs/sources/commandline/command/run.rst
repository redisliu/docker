:title: Run Command
:description: Run a command in a new container
:keywords: run, container, docker, documentation 

===========================================
``run`` -- Run a command in a new container
===========================================

::

    Usage: docker run [OPTIONS] IMAGE[:TAG] [COMMAND] [ARG...]

    Run a command in a new container

      -a=map[]: Attach to stdin, stdout or stderr
      -c=0: CPU shares (relative weight)
      -cidfile="": Write the container ID to the file
      -d=false: Detached mode: Run container in the background, print new container id
      -e=[]: Set environment variables
      -h="": Container host name
      -i=false: Keep stdin open even if not attached
      -privileged=false: Give extended privileges to this container
      -m=0: Memory limit (in bytes)
      -n=true: Enable networking for this container
      -p=[]: Map a network port to the container
      -rm=false: Automatically remove the container when it exits (incompatible with -d)
      -t=false: Allocate a pseudo-tty
      -u="": Username or UID
      -dns=[]: Set custom dns servers for the container
      -v=[]: Create a bind mount with: [host-dir]:[container-dir]:[rw|ro]. If "container-dir" is missing, then docker creates a new volume.
      -volumes-from="": Mount all volumes from the given container
      -entrypoint="": Overwrite the default entrypoint set by the image
      -w="": Working directory inside the container
      -lxc-conf=[]: Add custom lxc options -lxc-conf="lxc.cgroup.cpuset.cpus = 0,1"
      -expose=[]: Expose a port from the container without publishing it to your host
      -link="": Add link to another container (containerid:alias)

Examples
--------

.. code-block:: bash

    sudo docker run -cidfile /tmp/docker_test.cid ubuntu echo "test"

This will create a container and print "test" to the console. The
``cidfile`` flag makes docker attempt to create a new file and write the
container ID to it. If the file exists already, docker will return an
error. Docker will close this file when docker run exits.

.. code-block:: bash

   docker run mount -t tmpfs none /var/spool/squid

This will *not* work, because by default, most potentially dangerous
kernel capabilities are dropped; including ``cap_sys_admin`` (which is
required to mount filesystems). However, the ``-privileged`` flag will
allow it to run:

.. code-block:: bash

   docker run -privileged mount -t tmpfs none /var/spool/squid

The ``-privileged`` flag gives *all* capabilities to the container,
and it also lifts all the limitations enforced by the ``device``
cgroup controller. In other words, the container can then do almost
everything that the host can do. This flag exists to allow special
use-cases, like running Docker within Docker.

.. code-block:: bash

   docker  run -w /path/to/dir/ -i -t  ubuntu pwd

The ``-w`` lets the command being executed inside directory given, 
here /path/to/dir/. If the path does not exists it is created inside the 
container.

.. code-block:: bash

   docker  run  -v `pwd`:`pwd` -w `pwd` -i -t  ubuntu pwd

The ``-v`` flag mounts the current working directory into the container. 
The ``-w`` lets the command being executed inside the current 
working directory, by changing into the directory to the value
returned by ``pwd``. So this combination executes the command
using the container, but inside the current working directory.

.. code-block:: bash

    docker run -p 127.0.0.0::80 ubuntu bash

This the ``-p`` flag now allows you to bind a port to a specific
interface of the host machine.  In this example port ``80`` of the 
container will have a dynamically allocated port bound to 127.0.0.1 
of the host.

.. code-block:: bash

    docker run -p 127.0.0.1:80:80 ubuntu bash

This will bind port ``80`` of the container to port ``80`` on 127.0.0.1 of your
host machine.

.. code-block:: bash

    docker run -expose 80 ubuntu bash

This will expose port ``80`` of the container for use within a link
without publishing the port to the host system's interfaces.  

.. code-block:: bash

    docker run -link /redis:redis ubuntu bash

The ``-link`` flag will link the container named ``/redis`` into the 
newly created container with the alias ``redis``.  The new container
can access the network and environment of the redis container via
environment variables.
