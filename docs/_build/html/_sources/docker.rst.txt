Docker installation and configuration
=====================================

++++++++++++
Installation
++++++++++++
Please follow the install instructions provided by Docker that match your
operating system.

1. `Docker for Mac <https://docs.docker.com/docker-for-mac/install/>`_
2. `Docker for Windows <https://docs.docker.com/docker-for-windows/>`_
3. `Docker for Ubuntu <https://docs.docker.com/install/linux/docker-ce/ubuntu/>`_

.. note::
    Those using Linux also see think `link <https://docs.docker.com/install/linux/linux-postinstall/#next-steps/>`_

+++++++++++++++++++++++++++++++++++++
Memory Allocation for Mac and Windows
+++++++++++++++++++++++++++++++++++++
MAF and OpSim can use quite a lot of memory and CPU when running. To ensure
your containers do not crash Docker will need to be configured to have access
to more memory. Navigate to the Docker settings on your system, then the advanced
tab. Increase the ``Memory`` bar to 6 - 8 GB if your system has that much.

.. note::
    Running a full 10 year simulation typically requires ``25GB`` of memory so
    it is unlikely that you will be able to run long simulations on your laptop.
