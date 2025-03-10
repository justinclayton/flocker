.. _configuration:

============================
Required Configuration Files
============================

Flocker operates on two configuration files: application and deployment.
Together these configurations define a deployment.

The configuration is represented using yaml syntax.

Application Configuration
-------------------------

The application configuration consists of a version and short, human-meaningful application names and the parameters necessary to run those applications.

The required parameters are ``version`` and ``applications``.
For now the ``version`` must be ``1``.

The parameters required to define an application are:

- ``image``

  This is the name of the Docker image which will be used to start the container which will run the application.
  Optionally, this may include a tag using the ``<name>:<tag>`` syntax.

  For example, an application which is meant to use version 1.0 of ClusterHQ's ``flocker-dev`` Docker image is configured like this:

  .. code-block:: yaml

     "image": "clusterhq/flocker-dev:v1.0"

The following parameters are optional when defining an application:

- ``ports``

  This is an optional list of port mappings to expose to the outside world.
  Connections to the *external* port on the host machine are forwarded to the *internal* port in the container.

  .. code-block:: yaml

     "ports":
     - "internal": 80
       "external": 8080

- ``links``

  This is an optional list of links to make to other containers, providing a mechanism by which your containers can communicate even when they are located on different hosts.
  Linking containers in Flocker works by populating a number of environment variables in the application specifying a link.

  The environment variables generated are named for the specified alias and local port, while their values will point to the configured remote port.
  For example, given an application configuration containing a ``links`` section as follows:

  .. code-block:: yaml

     "links":
     - "local_port": 80
       "remote_port": 8080
       "alias": "apache"

  The above configuration will produce environment variables in that application using the same format as generated by Docker::

     APACHE_PORT_80_TCP=tcp://example.com:8080
     APACHE_PORT_80_TCP_PROTO=tcp
     APACHE_PORT_80_TCP_ADDR=example.com
     APACHE_PORT_80_TCP_PORT=8080

  .. warning::

     As you may have noticed in the example above, unlike Docker links, the destination port will not be the port used to create the environment variable names.
     Flocker implements linking via the ports exposed to the network, whereas Docker creates an internal tunnel between linked containers, an approach that is not compatible with the deployment of links across multiple nodes.

  .. note::
     Only TCP links are supported by Flocker, therefore the ``TCP`` portion of the environment variable names and the ``tcp`` value of the ``_PROTO`` and ``_TCP`` variables are not configurable.

.. _volume configuration:

- ``volume``

  This specifies that the application container requires a volume.
  It also allows you to specify where in the container the volume will be mounted via the ``mountpoint`` key.
  The value for this key must be a string giving an absolute path.
  Optionally, you can also specify the maximum size of the volume via the ``maximum_size`` key.
  The value for this key must be either a string giving the maximum size in bytes, or a string giving the maximum size including the first letter of the storage unit, for example 1G for 1 Gigabyte.
  Supported units are K for kilobytes, M for megabytes, G for gigabytes and T for terabytes.
  The following examples are all valid ways to specify a maximum size:

  .. code-block:: yaml

    "maximum_size": "500M"
    "maximum_size": "1073741824"
    "maximum_size": "2.5G"

  Here is a complete example of a ``volume`` entry:

  .. code-block:: yaml

     "volume":
       "mountpoint": "/var/www/data"
       "maximum_size": "500M"

- ``environment``

  This is an optional mapping of key/value pairs for environment variables that will be applied to the application container.
  Keys and values for environment variables must be strings and only ASCII characters are supported at this time.

  .. code-block:: yaml

     "environment":
       "foo": "bar"
       "baz": "qux"

- ``mem_limit``

  This is an optional integer value representing the maximum RAM allocated to a container, in bytes.

  .. code-block:: yaml

     "mem_limit": 100000000

- ``cpu_shares``

  This is an optional integer value representing the relative weighting given to a container for CPU cycles.
  For example, the default value is 1024, so for example a container running at a configured value of 512 will receive half the CPU cycles of another container running at the default.
  See the `Docker Run reference`_ for more information on CPU and memory limits.

  .. code-block:: yaml

     "cpu_shares": 512

.. _restart configuration:

- ``restart_policy``

  This specifies the restart policy for this Application.
  There must be a ``name`` sub-key whose value may be one of: ``never``, ``always``, or ``on-failure``.

  .. warning::

     The supplied policy is recorded but not not used.

     Containers will always behave as though created with the ``always`` restart policy, irrespective of the ``restart_policy`` in the configuration.
     They are restarted if they are ever stopped: for example, when they terminate, or when a machine is rebooted or Docker is restarted.

  The ``on-failure`` restart policy accepts an optional ``maximum_retry_count`` sub-key, which specifies how many times Docker will attempt to restart the application container in the event of repeated failures.
  See the `Docker Restart Policy reference <https://docs.docker.com/reference/commandline/cli/#restart-policies>`_ for more information on restart policies.

  .. code-block:: yaml

     "restart_policy":
       "name": "on-failure"
       "maximum_retry_count": 10


Here's an example of a simple but complete configuration defining one application:

.. code-block:: yaml

  "version": 1
  "applications":
    "site-clusterhq.com":
      "image": "clusterhq/clusterhq-website"
      "environment":
        "WP_ADMIN_USERNAME": "administrator"
        "WP_ADMIN_PASSWORD": "password"
      "ports":
      - "internal": 80
        "external": 8080
      "mem_limit": 100000000
      "volume":
        "mountpoint": "/var/mysql/data"
     "restart_policy":
       "name": "on-failure"
       "maximum_retry_count": 10


.. _fig-compatible-config:

Fig-compatible Application Configuration
----------------------------------------

As an alternative to Flocker's configuration syntax, you may also use `Fig`_'s configuration syntax to define applications.

.. note::

   Flocker does not yet support the entire range of configuration directives available in Fig.
   The parameters currently supported to define an application in Fig syntax are:

- ``image``

  This is the name of the Docker image which will be used to start the container which will run the application.
  Optionally, this may include a tag using the ``<name>:<tag>`` syntax.

  For example, in an application which is meant to use version 5.6 of ``MySQL``, the Docker image is configured like this:

  .. code-block:: yaml

     image: "mysql:5.6"

- ``environment``

  This is an optional mapping of key/value pairs for environment variables that will be applied to the application container.
  Keys and values for environment variables must be strings and only ASCII characters are supported at this time.
  Environment variables may take the form of a dictionary or a list of Bash-style exports, e.g. the following two examples will produce identical results.

  Dictionary mappings:

  .. code-block:: yaml

     environment:
       "WP_ADMIN_USERNAME": "admin"
       "WP_ADMIN_PASSWORD": "8x6nqf5arbt"

  List mappings:

  .. code-block:: yaml

     environment:
       - "WP_ADMIN_USERNAME=admin"
       - "WP_ADMIN_PASSWORD=8x6nqf5arbt"

- ``ports``

  This is an optional list of port mappings to expose to the outside world, with each entry in ``external:internal`` format.
  Connections to the *external* port on the host machine are forwarded to the *internal* port in the container.
  You should wrap port mappings in quotes, as per the example below, to explicitly specify the mappings as strings.
  This is because YAML will parse numbers in the form of ``xx:yy`` as base 60 numbers, leading to erroneous behaviour.

  .. code-block:: yaml

     ports:
       - "8080:80"

- ``links``

  This is an optional list of links to make to other containers, providing a mechanism by which your containers can communicate even when they are located on different hosts.
  Linking containers in Flocker works by populating a number of environment variables in the application specifying a link.
  The environment variables created will be mapped to the name or alias of an application along with exposed internal and external ports.
  For example, a configuration:

  .. code-block:: yaml

     links:
       - "mysql:db"

  Where ``mysql`` is another application defined in the configuration, ``db`` will be the alias available to the application linking ``mysql`` and the following environment variables will be populated (assuming port mapping in ``mysql`` of ``3306:3306``::

     DB_PORT_3306_TCP=tcp://example.com:3306
     DB_PORT_3306_TCP_PROTO=tcp
     DB_PORT_3306_TCP_ADDR=example.com
     DB_PORT_3306_TCP_PORT=3306

  If an alias is not specified in a link configuration, the environment variable prefix will be the application name.
  For example:

  .. code-block:: yaml

     links:
       - "mysql"

  will populate environment variables::

     MYSQL_PORT_3306_TCP=tcp://example.com:3306
     MYSQL_PORT_3306_TCP_PROTO=tcp
     MYSQL_PORT_3306_TCP_ADDR=example.com
     MYSQL_PORT_3306_TCP_PORT=3306

- ``volumes``

  This is an optional list specifying volumes to be mounted inside a container.

  .. warning::

     Flocker only supports one volume per container at this time.
     Therefore if using a Fig compatible configuration, the ``volumes`` list should contain only one entry.

  The value for an entry in this list must be a string giving an absolute path.

  .. code-block:: yaml

     volumes:
       - "/var/lib/mysql"

- ``mem_limit``

  This is an optional integer value representing the maximum RAM allocated to a container, in bytes.

  .. code-block:: yaml

     "mem_limit": 100000000

Here's a complete example of a Fig compatible application configuration for Flocker:

.. code-block:: yaml

   "mysql":
     image: "mysql:5.6.17"
     environment:
       "MYSQL_ROOT_PASSWORD": "clusterhq"
     ports:
       - "3306:3306"
     mem_limit: 100000000
     volumes:
       - "/var/lib/mysql"


Deployment Configuration
------------------------

The deployment configuration specifies which applications are run on what nodes.
It consists of a version and a mapping from node IPv4 addresses to application names.

The required parameters are ``version`` and ``nodes``.
For now the ``version`` must be ``1``.

Each entry under ``nodes`` should be a mapping of the desired deployment node's IP address to a list of application names that match those defined in the application configuration.

Here's an example of a simple but complete configuration defining a deployment of one application on one host:

.. code-block:: yaml

  "version": 1
  "nodes":
    "203.0.113.100":
      - "site-clusterhq.com"
      - "postgresql"

.. _`Fig`: http://www.fig.sh/yml.html
.. _`Docker Run reference`: http://docs.docker.com/reference/run/#runtime-constraints-on-cpu-and-memory
