=============
Release Notes
=============

See the :ref:`upgrading instructions <upgrading>` for information on upgrading Flocker clusters from earlier releases.
If you have a Vagrant tutorial environment from a previous release see :ref:`upgrading-vagrant-environment`.

You can learn more about where we might be going with future releases by:

* Stopping by the ``#clusterhq`` channel on ``irc.freenode.net``.
* Visiting our GitHub repository at https://github.com/ClusterHQ/flocker.

Next Release
============

* The :ref:`Flocker plugin for Docker<docker-plugin>` is now part of the core Flocker system, instead of an experimental Labs project.
* Unexpected errors in agent state discovery no longer break the agent convergence loop.


v1.4.0
======

* The :ref:`dataset API <api>` added support for leases.
  Leases prevent a dataset from being deleted or moved off a node.
* Fix line splitting when logging to `systemd`'s journal.
* Various performance and scalability improvements.
* Remove limits on size of configuration and state in agent protocol.
* Prevent repeated restart of containers with CPU shares or memory limits.

v1.3.1
======

* Fixed a bug in previous fix where OpenStack Cinder volumes failed to mount.
* Creation of a ZFS pool using ZFS 0.6.5 or later requires the setting of a :ref:`ZFS_MODULE_LOADING environment variable<zfs-creating-pool>`.

v1.3
====

* Fixed a bug where OpenStack Cinder volumes could be mapped to the wrong device and therefore mounted in the wrong location.

v1.2
====

* If you upgrade to Docker 1.8.1 you may find pulling images unreliable in flocker-deploy and the Flocker Containers API due to Docker bug `#15699`_.
  You may be able to workaround this by appending the image tag to the end of the image name (e.g. :latest).
* Flocker ``.deb`` and ``.rpm`` packages no longer declare any dependency on a Docker package.
  Docker is required for the container management functionality but a Docker package must be selected and installed manually.
  This provides more control over the version of Docker used with Flocker.
* Flocker's container management functionality now integrates with SELinux.
  Flocker can now be used in ``SELinux=enforcing`` environments.
* Flocker now includes :ref:`bug reporting documentation<flocker-bug-reporting>` and an accompanying command line tool called ``flocker-diagnostics``.

v1.1
====

* ``flocker-deploy`` supports specification of the pathnames of certificate and key files.
  See :ref:`flocker-deploy-authentication`.
* The agent configuration file allows specification of a CA certificate for OpenStack HTTPS verification.
  See :ref:`openstack-dataset-backend`.
* Flocker can now start containers using images from private Docker registries.
* On CentOS 7, installing or upgrading the ``clusterhq-flocker-node`` package now reloads the ``rsyslog`` service to ensure that Flocker logging policy takes immediate effect.

v1.0.3
======

* On Ubuntu-14.04, log files are now written to /var/log/flocker and rotated in five 100MiB files, so as not fill up the system disk.

v1.0.2
======

* On CentOS 7, Flocker logs are no longer written to /var/log/messages since this filled up disk space too quickly.
  The logs are still available via journald.
* The "on-failure" and "always" restart policies for containers have been temporarily disabled due to poor interaction with node reboots for containers with volumes (FLOC-2467).
  See :ref:`restart policy<restart configuration>`.

v1.0.1
======

Upgrading is strongly recommended for all users of v1.0.0.

* The EBS storage driver now more reliably selects the correct OS device file corresponding to an EBS volume being used.
* Additional safety checks were added to ensure only empty volumes are formatted.
* ClusterHQ Labs projects, including the Flocker Docker Plugin and an experimental Volumes CLI and GUI are now documented in the :ref:`Labs section <labs-projects>`.

v1.0
====

* Dataset backend support for :ref:`AWS Elastic Block Storage (EBS)<aws-dataset-backend>`, :ref:`OpenStack Cinder<openstack-dataset-backend>`, and :ref:`EMC ScaleIO and XtremIO<emc-dataset-backend>`.
* Third parties can write Flocker storage drivers so that their storage systems work with Flocker.
  See :ref:`contribute-flocker-driver`.
* It is now necessary to specify a dataset backend for each agent node.
  See :ref:`post-installation-configuration`.
* Flocker-initiated communication is secured with TLS.
  See :ref:`authentication`.
* ``flocker-deploy`` now requires the hostname of the control service as its first argument.
* Added REST API functions to manage containers in a cluster alongside datasets.
  See :ref:`api`.
* Removed support for installing ``flocker-node`` on Fedora 20.
* Ubuntu CLI installation instructions now use Debian packages instead of pip packaging.
  See :ref:`installing-flocker-cli-ubuntu-14.04` and :ref:`installing-flocker-cli-ubuntu-15.04`.
* Bug fixes and improvements focused on security and stability across platforms.

v0.4
====

* New :ref:`REST API<api>` for managing datasets.
* Applications can now be configured with a :ref:`restart policy<restart configuration>`.
* Volumes can now be configured with a :ref:`maximum size<volume configuration>`.
* Documentation now includes :ref:`instructions for installing flocker-node on CentOS 7<centos-7-install>`.
* SELinux must be disabled before installing Flocker.
  A future version of Flocker may provide a different integration strategy.

v0.3.2
======

* Documented how to configure the Fedora firewall on certain cloud platforms.


v0.3.1
======

* Applications can now be :ref:`configured with a CPU and memory limit<configuration>`.
* Documentation now includes instructions for installing flocker-node on Fedora 20.
* Documentation now includes instructions for deploying ``flocker-node`` on three popular cloud services: :ref:`Amazon EC2<aws-install>`, :ref:`Rackspace<rackspace-install>`, and DigitalOcean.


v0.3
====

* ``geard`` is no longer used to manage Docker containers.
* Added support for `Fig`_ compatible :ref:`application configuration <fig-compatible-config>` files.


v0.2
====

* Moving volumes between nodes is now done with a :ref:`two-phase push<clustering>` that should dramatically decrease application downtime when moving large amounts of data.
* Added support for environment variables in the :ref:`application configuration<configuration>`.
* Added basic support for links between containers in the :ref:`application configuration<configuration>`.

v0.1
====

Everything is new since this is our first release.


.. _`Fig`: http://www.fig.sh/yml.html
.. _`#15699`: https://github.com/docker/docker/issues/15699
