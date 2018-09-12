Directories and GitHub repos
============================
Setting up the following directory structure, and cloning these repos, will make
it easier to follow the tutorials provided in this documentation.

1. Setup what will become our top working directory ``~/flatiron``

.. code-block:: bash

   mkdir flatiron
   cd flatiron
   mkdir maf_local
   mkdir my_repos
   mkdir -p run_local/output

2. Clone the following repos into these directories

.. code-block:: bash

   cd maf_local
   git clone https://github.com/LSST-nonproject/sims_maf_contrib.git
   git clone https://github.com/lsst/sims_maf.git
   cd ..
   cd my_repos
   git clone https://github.com/lsst-ts/scheduler_config
   git clone git clone https://github.com/lsst/sims_featureScheduler.git

3. If you are on a linux system you will need to open up the read/write permissions
of any of the local directories that will be mounted inside of docker containers.
The for these tutorials you will need to do this for ``maf_local``, ``my_repos``,
and ``run_local``. Again, please see this `link <https://docs.docker.com/install/linux/linux-postinstall/#next-steps/>`_
for additional setup steps for using docker on linux.

.. code-block:: bash

   chmod a+rw maf_local
   chmod a+rw my_repos
   chmod a+rw run_local
