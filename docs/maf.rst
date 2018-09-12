MAF
===
A simple introduction to using MAF with Docker. Before
following these directions, make sure you have set up the directories as
described in :ref:`Directories and GitHub repos <directories>`.
If you have followed those directions, make sure your present working directory is ``~/flatiron``.


++++++++++++++++++++++++++
Download an OpSim database
++++++++++++++++++++++++++
These commands are run on your local host in the ``~/flatiron`` directory.

.. code-block:: bash

    $ wget http://astro-lsst-01.astro.washington.edu:8081/db_gzip/baseline2018a.db.gz
    $ gunzip baseline2018a.db.gz
    $ mv baseline2018a.db sims_maf_contrib/tutorials/baseline2018a.db


++++++++++++++++++++++++
Start a Docker container
++++++++++++++++++++++++
Running the following command will start a docker container using the
``oboberg/maf:latest`` image. This command will also mount local directories into the container so that the MAF output is saved.

.. code-block:: bash

        docker run -it --rm -v ${PWD}/maf_local:/home/docmaf/maf_local \
                            -v ${PWD}/my_repos:/home/docmaf/my_repos \
                            -p 8888:8888 \
                             oboberg/maf:latest

Breakdown of command:
 - ``docker run`` run a docker container
 - ``-it`` Give me an an interactive shell in the container
 - ``--rm`` remove the container after it is stopped
 - ``-v ${PWD}/maf_local`` mounts the local ``maf_local`` into the container at the path ``/home/docmaf/maf_local``.
 - ``-v ${PWD}/my_repos`` mounts the local ``my_repos`` into the container at the path ``/home/docmaf/my_repos``.
 - ``-p 8888:8888`` this is read as ``port on host:port on container``. Meaning port ``8888`` in the container will be fed to port ``8888`` on your local host.This allows you to use things like ``jupyter lab``.
 - ``oboberg/maf:latest`` this is the name of the docker image. If you don't already have it from doing ``docker pull oboberg/maf:latest``, it will automatically be pulled.


+++++++++++++++++++++++++++
Now we are in the container
+++++++++++++++++++++++++++
The terminal where you ran the docker command will now be a terminal inside the
docker container. It will look something like this

.. code-block:: bash

    (lsst-scipipe-10a4fa6) [docmaf@7b8b90333725 ~]$ ls
    maf_local  my_repos  repo_pulls.sh  repos  stack  startup.sh


++++++++++++++++++++++++++++++++++
Setup the sims_maf_contrib package
++++++++++++++++++++++++++++++++++

.. code-block:: bash

    (lsst-scipipe-10a4fa6) [docmaf@7b8b90333725 ~]$ cd maf_local/sims_maf_contrib/
    (lsst-scipipe-10a4fa6) [docmaf@7b8b90333725 sims_maf_contrib]$ eups declare sims_maf_contrib -r . -t $USER
    (lsst-scipipe-10a4fa6) [docmaf@7b8b90333725 sims_maf_contrib]$ setup sims_maf_contrib -t $USER


At this point we will ``cd`` into the directory with the tutorial notebooks and then start up
``jupyter lab``.

.. code-block:: bash

    (lsst-scipipe-10a4fa6) [docmaf@7b8b90333725 ~]$ cd ~/maf_local/sims_maf_contrib/tutorials/

++++++++++++++++++++
Starting jupyter lab
++++++++++++++++++++

.. code-block:: bash

    (lsst-scipipe-10a4fa6) [docmaf@e6fe5279c797 tutorials]$ jupyter lab --ip=0.0.0.0

.. note::
    If you prefer jupyter notebook just do: ``jupyter notebook --ip=0.0.0.0``. Also, make
    sure nothing else is using port 8888 on your local machine.


You should see a dialog similar to this one, but the token will be some string of letters and numbers.

.. code-block:: bash

    Copy/paste this URL into your browser when you connect for the first time,
    to login with a token:
    http://(7b8b90333725 or 127.0.0.1):8888/?token=sometoken

When copying and pasting this URL into your local browser you will need to
replace ``(7b8b90333725 or 127.0.0.1)`` with either ``localhost``, ``8888``, or
``127.0.0.1``. So the actual URL you will use in your browser should look something
like this::

    http://localhost:8888/?token=sometoken

Once you copy that into your browser and hit enter you should see the familiar
jupyter lab landing page.

Go ahead and click on ``Introduction Notebook.ipynb`` and start running through the cells.
As long as you put ``baseline2018a.db`` in the correct directory the notebook will work right out of the box.
See the first couple of steps of this document if you still need to get the database.
To kill the jupyter lab/notebook go to the terminal where you started it and do control C twice.
This will bring you back to the command prompt.

++++++++++++++++++++++
Quitting the container
++++++++++++++++++++++

You can now quit the container by simply typing exit.

.. code-block:: bash

    (lsst-scipipe-10a4fa6) [docmaf@7b8b90333725 ~]$ exit

Any work that you did in the ``maf_local`` directory in the container, will be saved to the local directory ``~/flatiron/maf_local``.

.. note::
    Since we started the container with the --rm flag it will be deleted as soon as we exit.
    You certainly don’t have to use this flag, but be sure to manage the running or stopped containers you having lying around.
