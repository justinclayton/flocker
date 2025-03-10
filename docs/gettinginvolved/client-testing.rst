.. _client-testing:

Client Testing
==============

Automated Testing
-----------------

Flocker includes client installation tests and a tool for running them in Docker containers.

.. note::

   On OS X, use the system Python together with Homebrew's OpenSSL.

   .. prompt:: bash $

      brew install libffi openssl
      env LDFLAGS="-L$(brew --prefix openssl)/lib" CFLAGS="-I$(brew --prefix openssl)/include" pip install -e .[dev]
      mkvirtualenv --python=/usr/bin/python new_virtualenv

The client tests are called like this:

.. prompt:: bash $

   admin/run-client-tests <options>


The :program:`admin/run-client-tests` script has several options:

.. program:: admin/run-client-tests

.. option:: --distribution <distribution>

   Specifies the distribution on which to run the installation test.

.. option:: --flocker-version <version>

   Specifies the version of flocker to install.
   If this isn't specified, the most recent version will be installed.
   If a branch is also specified, the most recent version from that branch will be installed.
   If a branch is not specified, the most recent release will be installed.

   .. note::

      The build server merges forward before building packages, except on release branches.
      If you want to run the client tests against a branch in development,
      you probably only want to specify the branch.

.. option:: --branch <branch>

   Specifies the branch from which packages are installed.
   If this isn't specified, packages will be installed from the release repository.

.. option:: --build-server <buildserver>

   Specifies the base URL of the build server to install from.
   This is probably only useful when testing changes to the build server.

.. option:: --pip

   Use pip to install the client, rather than using packages.

To see the supported values for each option, run:

.. prompt:: bash $

   admin/run-client-tests --help


Manual Testing
--------------

Sometimes it is useful to manually test CLIs and their installation instructions on various platforms.

OS X
~~~~

ClusterHQ has a Mac with the ability to start an OS X Virtual Machine.
An internal document describing how to use this is available at "Infrastructure > OS X Development Machine".

Linux
~~~~~

To test on various Linux distributions, it is possible to create either Docker containers or Vagrant virtual machines.

Using Docker
^^^^^^^^^^^^

To create a Docker container, choose a Docker image from the `Docker Hub <https://hub.docker.com/>`_, and start it as below:

.. prompt:: bash $

   docker run -i -t ubuntu /bin/bash

or:

.. prompt:: bash $

   docker run -i -t fedora:20 /bin/bash

for example.

This will likely allow you to test commands as a root user.
If you want to test as a non-root user, create a new user which has the ability to use ``sudo``.

Using Vagrant
^^^^^^^^^^^^^

To create a Vagrant virtual machine, choose a Vagrant box from `Atlas <https://atlas.hashicorp.com/boxes/search>`_ and start it as below:

.. prompt:: bash $

   vagrant init ubuntu/trusty64
   vagrant up
   vagrant ssh

for example.
