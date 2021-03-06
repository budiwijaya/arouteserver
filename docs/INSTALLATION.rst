Installation
============

1. Strongly suggested: install ``pip`` and setup a `Virtualenv <https://virtualenv.pypa.io/en/latest/installation.html>`_:

  .. code:: bash

    # on Debian/Ubuntu:
    sudo apt-get install python-virtualenv

    # on CentOS:
    sudo yum install epel-release
    sudo yum install python-pip python-virtualenv 

    # setup a virtualenv
    mkdir arouteserver
    cd arouteserver
    virtualenv venv
    source venv/bin/activate

  More: ``virtualenv`` `installation <https://virtualenv.pypa.io/en/latest/installation.html>`_ and `usage <https://virtualenv.pypa.io/en/latest/userguide.html>`_.

2. Install the program.
   
        - If you plan to run :doc:`Live tests <LIVETESTS>`, to build your own scenarios or to contribute to the project, clone the GitHub repository locally and install dependencies:

        .. code:: bash

            # from within the previously created arouteserver directory
            git clone https://github.com/pierky/arouteserver.git ./
            export PYTHONPATH="`pwd`"
            pip install -r requirements.txt


        - If you plan to just use the program to build configurations, you can install it using ``pip``:

        .. code:: bash

           pip install arouteserver

3. Setup your system layout (confirmation will be asked before each action):

  .. code:: bash

    # if you installed from GitHub
    export PYTHONPATH="`pwd`"
    ./scripts/arouteserver setup

    # if you used pip
    arouteserver setup

  The program will ask to create some directories (under ``/etc/arouteserver`` by detault) and to copy some files there.
  These paths can be changed by editing the ``arouteserver.yml`` program configuration file or by using command line arguments. More information in the :doc:`configuration section <CONFIG>`.

External programs
-----------------

ARouteServer uses the following external programs:

- `bgpq3 <https://github.com/snar/bgpq3>`_ is used to gather information about routing policies.
  
  To install it:

  .. code:: bash

    mkdir /path/to/bgpq3/directory
    cd /path/to/bgpq3/directory
    git clone https://github.com/snar/bgpq3.git ./
    # make and gcc packages required
    ./configure
    make
    make install

- `Docker <https://www.docker.com/>`_ is used to perform :doc:`live validation <LIVETESTS>` of configurations.

  To install it, please refer to its `official guide <https://www.docker.com/products/overview>`_.
