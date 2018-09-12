OpSim
=====
Here we will go over how to run a very simple one night simulation. Before
following these directions, make sure you have set up the directories as
described in :ref:`Directories and GitHub repos <directories>`. If you have
followed those directions, make sure your present working directory is ``~/flatiron``.

++++++++++++++++++++++++++++++
Getting the OpSim Docker image
++++++++++++++++++++++++++++++
Use this command on your local host to pull the latest OpSim Docker image from
the docker hub::

    docker pull oboberg/opsim4_fbs_py3:latest

++++++++++++++++++++++++
Start a Docker container
++++++++++++++++++++++++
Running the following command will start a docker container using the
``oboberg/opsim4_fbs_py3:latest`` image. This command will also mount local
directories into the container so that the OpSim output is saved.

.. code-block:: bash

        docker run -it --rm -v ${PWD}/opsimv4_data/run_local:/home/opsim/run_local \
                            -v ${PWD}/my_repos:/home/opsim/my_repos \
                            -e OPSIM_HOSTNAME=opsim-docker\
                            -p 8888:8888 \
                             oboberg/opsim4_fbs_py3:latest

Breakdown of command:
 - ``docker run`` run a docker container
 - ``-it`` Give me an an interactive shell in the container
 - ``--rm`` remove the container after it is stopped
 - ``-v ${PWD}/opsimv4_data/run_local`` mounts the local `run_dir` into the container at the path ``/home/opsim/run_local``.
 - ``-e OPSIM_HOSTNAME=opsim-docker`` sets the ``OPSIM_HOSTNAME`` environment variable inside the container. This sets the name of the run tracking database and other output files. You can change this to whatever name you like.
 - ``-p 8888:8888`` this is read as ``port on host:port on container``. Meaning port ``8888`` in the container will be fed to port ``8888`` on your local host.This allows you to use things like ``jupyter lab``.
 - ``oboberg/opsim4_fbs_py3:latest`` this is the name of the docker image. If you don't already have it from doing ``docker pull oboberg/opsim4_fbs_py3:latest``, it will automatically be pulled.

After running this command you will see a lot of dialog about all of the packages that are
being setup. The final message will be about a ``scons`` test failing, but you do not
need to worry about this.


+++++++++++++++++++++++++++
Now we are in the container
+++++++++++++++++++++++++++
The terminal where you ran the docker command will now be a terminal inside the
docker container. It will look something like this

.. code-block:: bash

    (lsst-scipipe-fcd27eb) [opsim@9d54f5d124e1 ~]

If you run an ``ls`` command you should see the following if you setup the directory structure
previously mentioned.

.. code-block:: bash

    (lsst-scipipe-fcd27eb) [opsim@9d54f5d124e1 ~]$ ls
    dds  default_configs  my_repos  pull_and_config.sh  pull_repos.sh  repos  run_and_config.sh  run_local  sky_brightness_data  stack  startup_fbs.sh


+++++++++++++++++++++++++++++++++
Setup the OpSim tracking database
+++++++++++++++++++++++++++++++++
Run this command to setup the database that will be used to track OpSim runs. For
this command make sure you give the full path for the ``save-dir``.

.. code-block:: bash

   manage_db --save-dir=/home/opsim/run_local/output/

If you ``ls`` the directory ``run_local/output/`` you will see the file ``opsim-docker_sessions.db``.

.. code-block:: bash

    (lsst-scipipe-fcd27eb) [opsim@9d54f5d124e1 ~]$ ls run_local/output/
    opsim-docker_sessions.db

.. note::
    To get sense of how the volume mounting works, open a new terminal, or system browser,
    and navigate to ``~/flatiron/opsimv4_data/run_local/output``. There you will also see
    the file ``opsim-docker_sessions.db``.


+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
Start a one day simulation with the feature based scheduler
+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

In the docker container ``cd`` in the the ``run_local`` directory and run this
command. (Note: do not include the ``(lsst-scipipe-fcd27eb) [opsim@9d54f5d124e1 run_local]$`` bit)

.. code-block:: bash

    (lsst-scipipe-fcd27eb) [opsim@9d54f5d124e1 run_local]$ opsim4 --frac-duration=0.003

``--frac-duration`` sets the length of the simulation and it is untis of fraction of a year.
``(1 / 365)`` is about ``0.003``, for a full simulation ``--frac-duration`` is ``10``.

Now if you ``ls`` in the the ``run_local`` directory you see that a log file has been
produced. The actual OpSim database created by our one night run will be in ``run_local/output``.

.. code-block:: bash

    (lsst-scipipe-fcd27eb) [opsim@9d54f5d124e1 run_local]$ ls
    opsim-docker_2000.log  output
    (lsst-scipipe-fcd27eb) [opsim@9d54f5d124e1 run_local]$ ls output/
    opsim-docker_2000.db  opsim-docker_sessions.db

.. note::
    You can see that the log file and output database share the same file root
    as the session database ``opsim-docker``. The number ``2000`` will be autamotically
    increased by ``1`` as we run additional simulations.

++++++++++++++++++++++++++++++++++++++++++++++++++++++
Start a one day simulation with the proposal scheduler
++++++++++++++++++++++++++++++++++++++++++++++++++++++
To use the proposal scheduler we simply provide another command line option to
the ``opsim4`` command.

.. code-block:: bash

   (lsst-scipipe-fcd27eb) [opsim@9d54f5d124e1 run_local]$ opsim4 --frac-duration=0.003 --scheduler proposal

Again, if you ``ls`` in the the ``run_local`` directory you see that another log file and output database
have been produced. Also note that the run number increased from ``2000`` to ``2001``.

.. code-block:: bash

    (lsst-scipipe-fcd27eb) [opsim@9d54f5d124e1 run_local]$ ls
    opsim-docker_2000.log  opsim-docker_2001.log  output
    (lsst-scipipe-fcd27eb) [opsim@9d54f5d124e1 run_local]$ ls output/
    opsim-docker_2000.db  opsim-docker_2001.db  opsim-docker_sessions.db


++++++++++++++++++++++++++++++
Useful information in the logs
++++++++++++++++++++++++++++++
Let's run the ``head`` command on the two logs to see some useful information
about which scheduler was used, and the path to the default configuration.

- First the feature based run ``opsim-docker_2000``

.. code-block:: bash

    (lsst-scipipe-fcd27eb) [opsim@9d54f5d124e1 run_local]$ head -n5 opsim-docker_2000.log
    2018-09-12 18:31:36,203 - INFO - root - Loading feature driver
    2018-09-12 18:31:36,616 - DEBUG - matplotlib.backends - backend Qt5Agg version 5.9.2
    2018-09-12 18:31:37,062 - DEBUG - kernel.Simulator - Using default configuration path: /home/opsim/repos/scheduler_config/config_run/
    2018-09-12 18:31:37,198 - INFO - kernel.Simulator - Initializing simulation
    2018-09-12 18:31:37,199 - INFO - kernel.Simulator - Simulation Session Id = 2000

- You can see in ``line 1`` that the feature scheduler was used ``INFO - root - Loading feature driver``
- You can also see the path to the configuration that was used for the simulation ``Using default configuration path: /home/opsim/repos/scheduler_config/config_run/``.
  We will come back to this in the next section.

- Now the proposal based run ``opsim-docker_2001``

.. code-block:: bash

    (lsst-scipipe-fcd27eb) [opsim@9d54f5d124e1 run_local]$ head -n5 opsim-docker_2001.log
    2018-09-12 18:47:00,561 - INFO - root - Loading proposal driver
    2018-09-12 18:47:00,813 - INFO - schedulerDriver - buildFieldsTable: 5292 fields
    2018-09-12 18:47:00,818 - DEBUG - kernel.Simulator - Using default configuration path: /home/opsim/repos/scheduler_config/config_run/
    2018-09-12 18:47:01,241 - DEBUG - matplotlib.backends - backend Qt5Agg version 5.9.2
    2018-09-12 18:47:01,537 - INFO - kernel.Simulator - Initializing simulation

- You can see in ``line 1`` that the proposal scheduler was used ``INFO - root - Loading proposal driver``
- You can also see that the same path was use for the configuration ``Using default configuration path: /home/opsim/repos/scheduler_config/config_run/``.


+++++++++++++++++++++++
Configuring Simulations
+++++++++++++++++++++++

OpSim has recently been redesigned to read configurations from a GitHub repository
called ``scheduler_config`` that can be found `here <https://github.com/lsst-ts/scheduler_config/tree/master>`_.
Within that repository there is a directory called ``config_run``, which is where OpSim looks for the configuration
for a simulation. From the two previous log files, we can see how this is set up in the docker container. OpSim
is looking in ``/home/opsim/repos/scheduler_config/config_run/`` for the configuration.

When using the ``feature based scheduler``, OpSim is reading ``feature_scheduler.py`` for how
to run the simulation. For the ``proposal based scheduler``, OpSim will look in this directory
for ``PexConfig`` files that correspond to individual proposals in the simulation (e.g WFD, NES, SCP).

Setup up the repos for new configurations
-----------------------------------------
We want to try a different configuration, but let's not use the ``scheduler_config`` repo that is built
into the container, instead we will ``eups declare`` the cloned repo we mounted when we started the
container.

.. code-block:: bash

    (lsst-scipipe-fcd27eb) [opsim@9d54f5d124e1 ~]$ cd /home/opsim/my_repos/scheduler_config/
    (lsst-scipipe-fcd27eb) [opsim@9d54f5d124e1 scheduler_config]$ eups declare scheduler_config -r . -t $USER
    (lsst-scipipe-fcd27eb) [opsim@9d54f5d124e1 scheduler_config]$ setup scheduler_config -t $USER
    (lsst-scipipe-fcd27eb) [opsim@9d54f5d124e1 scheduler_config]$ scons

Now if you run ``eups list -v scheduler_config`` you will see the correct repo is setup.

.. code-block:: bash

    (lsst-scipipe-fcd27eb) [opsim@9d54f5d124e1 scheduler_config]$ eups list -v scheduler_config
    git        /home/opsim/stack/stack/miniconda3-4.5.4-fcd27eb /home/opsim/repos/scheduler_config                     	current
    tag:opsim  /home/opsim/.eups    /home/opsim/my_repos/scheduler_config                  	user:opsim setup

We are now ready to edit the configurations. From the ``/home/opsim/my_repos/scheduler_config`` directory,
go ahead and make a new branch in the repo.

.. code-block:: bash

    (lsst-scipipe-fcd27eb) [opsim@9d54f5d124e1 scheduler_config]$ git checkout -b my_config_test
    Switched to a new branch 'my_config_test'

.. note::
    Any of the edits that we are about to do in ``/home/opsim/my_repos/scheduler_config`` can
    either be done in the docker container terminal using ``vi``, or you can edit it them using
    your favorite local editor in the ``~/flatiron/my_repos`` directory.


A new feature based configuration
---------------------------------
For the ``feature based scheduler`` we will edit the file ``~/my_repos/scheduler_config/config_run/feature_scheduler.py``.
If you are doing this from the inside the container with ``vi``, use the following, or edit it on your local host.

.. code-block:: bash

    (lsst-scipipe-fcd27eb) [opsim@9d54f5d124e1 scheduler_config]$ vi ~/my_repos/scheduler_config/config_run/feature_scheduler.py

For this example we will remove the deep drilling fields, the pairs survey, and we won't take any observations in the ``r filter``.
Edit the file to look like this and save.

.. code-block:: python

    import numpy as np
    import healpy as hp
    import lsst.sims.featureScheduler as fs
    from lsst.ts.scheduler.kernel import SurveyTopology

    if __name__ == 'config':
        survey_topology = SurveyTopology()
        survey_topology.num_general_props = 4
        survey_topology.general_propos = ["NorthEclipticSpur", "SouthCelestialPole", "WideFastDeep", "GalacticPlane"]
        survey_topology.num_seq_props = 1
        survey_topology.sequence_propos = ["DeepDrillingCosmology1"]

        target_maps = {}
        nside = fs.set_default_nside(nside=32)  # Required

        target_maps['u'] = fs.generate_goal_map(NES_fraction=0.,
                                                WFD_fraction=0.31, SCP_fraction=0.15,
                                                GP_fraction=0.15, nside=nside,
                                                generate_id_map=True)
        target_maps['g'] = fs.generate_goal_map(NES_fraction=0.2,
                                                WFD_fraction=0.44, SCP_fraction=0.15,
                                                GP_fraction=0.15, nside=nside,
                                                generate_id_map=True)
        #target_maps['r'] = fs.generate_goal_map(NES_fraction=0.46,
        #                                        WFD_fraction=1.0, SCP_fraction=0.15,
        #                                        GP_fraction=0.15, nside=nside,
        #                                        generate_id_map=True)
        target_maps['i'] = fs.generate_goal_map(NES_fraction=0.46,
                                                WFD_fraction=1.0, SCP_fraction=0.15,
                                                GP_fraction=0.15, nside=nside,
                                                generate_id_map=True)
        target_maps['z'] = fs.generate_goal_map(NES_fraction=0.4,
                                                WFD_fraction=0.9, SCP_fraction=0.15,
                                                GP_fraction=0.15, nside=nside,
                                                generate_id_map=True)
        target_maps['y'] = fs.generate_goal_map(NES_fraction=0.,
                                                WFD_fraction=0.9, SCP_fraction=0.15,
                                                GP_fraction=0.15, nside=nside,
                                                generate_id_map=True)

        filters = ['u', 'g', 'i', 'z', 'y']
        surveys = []

        for filtername in filters:
            bfs = []
            bfs.append(fs.M5_diff_basis_function(filtername=filtername, nside=nside))
            bfs.append(fs.Target_map_basis_function(filtername=filtername,
                                                    target_map=target_maps[filtername][0],
                                                    out_of_bounds_val=hp.UNSEEN, nside=nside))

            bfs.append(fs.MeridianStripeBasisFunction(nside=nside, width=(8.,)))
            bfs.append(fs.Slewtime_basis_function(filtername=filtername, nside=nside))
            bfs.append(fs.Strict_filter_basis_function(filtername=filtername))
            bfs.append(fs.Avoid_Fast_Revists(filtername=filtername, gap_min=240., nside=nside))

            weights = np.array([3.0, 0.5, 1., 3., 3., 3.])
            # surveys.append(fs.Greedy_survey_fields(bfs, weights, block_size=1, filtername=filtername, dither=False,
            #                                        nside=nside, smoothing_kernel=9,
            #                                        tag_fields=True, tag_map=target_maps[filtername][1]))
            surveys.append(fs.Greedy_survey_fields(bfs, weights, block_size=1, filtername=filtername, dither=True,
                                                   nside=nside,
                                                   tag_fields=True,
                                                   tag_map=target_maps[filtername][1],
                                                   tag_names=target_maps[filtername][2]))


        scheduler = fs.Core_scheduler(surveys, nside=nside)  # Required

Now we are set to run a new feature based simulation and this configuration will be used.

.. code-block:: bash

    (lsst-scipipe-fcd27eb) [opsim@9d54f5d124e1 run_local]$ cd ~/run_local/
    (lsst-scipipe-fcd27eb) [opsim@9d54f5d124e1 run_local]$ opsim4 --frac-duration=0.003

When it is done have a look at the new log file, which should be ``opsim-docker_2002.log``. You see that
the configuration was indeed read from the mounted repository.

.. code-block:: bash

    (lsst-scipipe-fcd27eb) [opsim@9d54f5d124e1 run_local]$ head -n5 opsim-docker_2002.log
    2018-09-12 20:17:43,187 - INFO - root - Loading feature driver
    2018-09-12 20:17:43,575 - DEBUG - matplotlib.backends - backend Qt5Agg version 5.9.2
    2018-09-12 20:17:43,951 - DEBUG - kernel.Simulator - Using default configuration path: /home/opsim/my_repos/scheduler_config/config_run/
    2018-09-12 20:17:44,272 - INFO - kernel.Simulator - Initializing simulation
    2018-09-12 20:17:44,272 - INFO - kernel.Simulator - Simulation Session Id = 2002

If you want to check that no observations were taken in ``r`` you can quickly do so wtih ``sqlite3``.

.. code-block:: bash

    (lsst-scipipe-fcd27eb) [opsim@9d54f5d124e1 output]$ cd ~/run_local/output/
    (lsst-scipipe-fcd27eb) [opsim@9d54f5d124e1 output]$ sqlite3 opsim-docker_2002.db
    SQLite version 3.23.1 2018-04-10 17:39:29
    Enter ".help" for usage hints.
    sqlite> sqlite> select * from summaryallprops where filter = 'r';
    sqlite> .exit
    (lsst-scipipe-fcd27eb) [opsim@9d54f5d124e1 output]$

This query will return nothing. For contrast, try the same thing with the first feature based run
(``opsim-docker_2002.db``) that used the default configuration.


A new proposal based run
------------------------
For proposal based runs there is not a single file that we edit, but rather a series
of ``PexConfig`` python files. For this example we will configure the simulation
to only do the Wide Fast Deep (WFD) area, plus deep drilling, and do single ``30`` second snaps, instead
of two ``15`` second snaps.

First we will edit the ``vi ~/my_repos/scheduler_config/config_run/survey.py`` file already in the repository so it only includes WFD.
This is easily done by adding this line ``config.general_proposals=['WideFastDeep']`` to the end of the file.

.. code-block:: python

    """
    This is an example configuration for some of the basic parameters for simulations.

    07/2018 - Version 0
    """
    import lsst.ts.schedulerConfig.survey
    assert type(config)==lsst.ts.schedulerConfig.survey.Survey, 'config is of type %s.%s instead of lsst.ts.schedulerConfig.survey.Survey' % (type(config).__module__, type(config).__name__)
    # The delay (units=seconds) to skip the simulation time forward when not receiving a target.
    config.idle_delay=60.0

    # The start date (format=YYYY-MM-DD) of the survey.
    config.start_date='2022-10-01'

    # The fractional duration (units=years) of the survey.
    config.duration=10.0

    config.general_proposals=['WideFastDeep']

To edit the snaps we will need to create a new file called ``widefastdeep_prop.py`` in
``~/my_repos/scheduler_config/config_run/``. Here we will do this with ``touch``.

.. code-block:: bash

    (lsst-scipipe-fcd27eb) [opsim@9d54f5d124e1 config_run]$ touch widefastdeep_prop.py

Then edit that file to contain the following

.. code-block:: python

    import lsst.ts.schedulerConfig.science.wide_fast_deep
    assert type(config)==lsst.ts.schedulerConfig.science.wide_fast_deep.WideFastDeep, 'config is of type %s.%s instead of lsst.ts.schedulerConfig.science.wide_fast_deep.WideFastDeep' % (type(config).__module__, type(config).__name__)
    config.filters['u'].exposures=[30]
    config.filters['g'].exposures=[30]
    config.filters['r'].exposures=[30]
    config.filters['i'].exposures=[30]
    config.filters['z'].exposures=[30]
    config.filters['y'].exposures=[30]

Now ``cd`` back to ``~/run_local`` and start a one night simulation.

.. code-block:: bash

   (lsst-scipipe-fcd27eb) [opsim@9d54f5d124e1 run_local]$ opsim4 --frac-duration=0.003 --scheduler proposal

We will use ``sqlite3`` again to illustrate that the configuration worked.

.. code-block:: bash

    (lsst-scipipe-fcd27eb) [opsim@9d54f5d124e1 run_local] cd ~/run_local/output
    (lsst-scipipe-fcd27eb) [opsim@9d54f5d124e1 output]$ cp opsim-docker_2006.db opsim-docker_2003.db
    (lsst-scipipe-fcd27eb) [opsim@9d54f5d124e1 output]$ sqlite3 opsim-docker_2003.db
    SQLite version 3.23.1 2018-04-10 17:39:29
    Enter ".help" for usage hints.
    sqlite> select * from Proposal;
    1|2003|WideFastDeep|General
    2|2003|DeepDrillingCosmology1|Sequence
    sqlite> .exit

As you can see only ``WideFastDeep`` and ``DeepDrillingCosmology1`` are present
in the ``Proposal`` table.

Switching back to the defaults
------------------------------
To switch back to the default configurations we can use ``eups``.

.. code-block:: bash

    (lsst-scipipe-fcd27eb) [opsim@9d54f5d124e1 output]$ eups list -v scheduler_config
    git        /home/opsim/stack/stack/miniconda3-4.5.4-fcd27eb /home/opsim/repos/scheduler_config current setup
    tag:opsim  /home/opsim/.eups    /home/opsim/my_repos/scheduler_config

++++++++++++++++++++++
Quitting the container
++++++++++++++++++++++
To quit the container simply type ``exit`` at the command promt.

.. code-block:: bash

    (lsst-scipipe-fcd27eb) [opsim@9d54f5d124e1 ~] exit

Since we started the container with the ``--rm`` flag it will be deleted as soon
as we exit. Therefore only edits and output in the mounted directories will be
saved on your local host.
