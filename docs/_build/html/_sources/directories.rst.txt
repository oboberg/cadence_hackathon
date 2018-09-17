.. _directories:

Directories and GitHub repos
============================
Setting up the following directory structure, and cloning these repos, will make
it easier to follow the tutorials provided in this documentation. These paths
can be changed to whatever you like, just be sure to update the examples accordingly.

1. Setup what will become our top working directory ``~/flatiron``

.. code-block:: bash

   mkdir flatiron
   cd flatiron
   mkdir maf_local
   mkdir my_repos
   mkdir -p opsimv4_data/run_local/output

2. Clone the following repos into these directories

.. code-block:: bash

   cd maf_local
   git clone https://github.com/LSST-nonproject/sims_maf_contrib.git
   cd ..
   cd my_repos
   git clone https://github.com/lsst/sims_maf.git
   git clone https://github.com/lsst-ts/scheduler_config
   git clone https://github.com/lsst/sims_featureScheduler.git

3. If you are on a linux system you will need to open up the read/write permissions
of any of the local directories that will be mounted inside of docker containers.
For these tutorials you will need to do this for ``maf_local``, ``my_repos``,
and ``opsimv4_data``. Again, please see this `link <https://docs.docker.com/install/linux/linux-postinstall/#next-steps/>`_
for additional setup steps for using docker on linux.

.. code-block:: bash

   chmod a+rw maf_local
   chmod a+rw my_repos
   chmod a+rw opsimv4_data
