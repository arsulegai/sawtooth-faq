Unofficial FAQ: Sawtooth Installation and Configuration
========================
[PREVIOUS_ | HOME_ | NEXT_]

.. contents::

.. Warning::
   **This FAQ was written by a non-expert so may contain both fiction and fact!**

How do I list and install Sawtooth packages?
--------------------------------------------
The following setups the Sawtooth stable repository, lists the packages,
and installs the core packages
(sawtooth, python3-sawtooth-cli, python3-sawtooth-sdk, python3-sawtooth-signing):

.. code:: sh

    $ sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 8AA7AF1F1091A5FD
    $ sudo add-apt-repository 'deb http://repo.sawtooth.me/ubuntu/1.0/stable xenial universe'
    $ sudo apt update
    $ aptitude install sawtooth python3-sawtooth-*
    $ aptitude search sawtooth
    p  python3-sawtooth-block-info     - Sawtooth Block Info Transaction Processor
    iA python3-sawtooth-cli            - Sawtooth CLI
    p  python3-sawtooth-config         - Sawtooth Config Transaction Processor
    p  python3-sawtooth-ias-client     - Sawtooth IAS Client
    p  python3-sawtooth-ias-proxy      - Sawtooth IAS Proxy
    c  python3-sawtooth-identity       - Sawtooth Identity Transaction Processor
    iA python3-sawtooth-intkey         - Sawtooth IntKey Python Example
    p  python3-sawtooth-manage         - Sawtooth Lake Management Library
    iA python3-sawtooth-poet-cli       - Sawtooth PoET CLI
    iA python3-sawtooth-poet-common    - Sawtooth PoET Common Modules
    iA python3-sawtooth-poet-core      - Sawtooth Core Consensus Module
    iA python3-sawtooth-poet-families  - Sawtooth Transaction Processor Families
    p  python3-sawtooth-poet-sgx       - Sawtooth PoET SGX Enclave
    iA python3-sawtooth-poet-simulator - Sawtooth PoET Simulator Enclave
    iA python3-sawtooth-rest-api       - Sawtooth REST API
    i  python3-sawtooth-sdk            - Sawtooth Python SDK
    iA python3-sawtooth-settings       - Sawtooth Settings Transaction Processor
    iA python3-sawtooth-signing        - Sawtooth Signing Library
    iA python3-sawtooth-validator      - Sawtooth Validator
    iA python3-sawtooth-xo             - Sawtooth XO Example
    i  sawtooth                        - Hyperledger Sawtooth Distributed Ledger
    p  sawtooth-admin-tools            - Sawtooth Admin Tools
    BB sawtooth-cxx-sdk                - Hyperledger Sawtooth C++ SDK
    p  sawtooth-intkey-tp-go           - Sawtooth IntKey TP Go
    p  sawtooth-noop-tp-go             - Sawtooth Noop TP Go
    p  sawtooth-smallbank-tp-go        - Sawtooth Smallbank TP Go
    p  sawtooth-xo-tp-go               - Sawtooth Go XO TP

For more, up-to-date installation information see
https://sawtooth.hyperledger.org/docs/core/releases/latest/sysadmin_guide/installation.html

How do I edit Sawtooth settings?
------------------------------------
With ``.toml`` configuration files in ``/etc/sawtooth`` .
Examples are in the directory as ``.toml.example`` .
For details, see
https://sawtooth.hyperledger.org/docs/core/nightly/master/sysadmin_guide/configuring_sawtooth.html

Configuration files include:

``validator.toml``
	Validator configuration file
``rest_api.toml``
	REST API configuration file
``cli.toml``
	Sawtooth CLI configuration file
``poet_enclave.toml``
	PoET SGX Enclave configuration file
``path.toml``
	Directory path configuration (or use ``$SAWTOOTH_HOME``)
``identity.toml``
	Identity TP configuration file
``settings.toml``
	Settings TP configuration file
``log_config.toml``
	Log configuration file

More transaction-processor specific configuration files may be present.

I get this error installing Ubuntu packages: ``Could not get lock /var/lib/dpkg/lock``
----------------------------
The file lock is probably left over from a previous failed install.
The solution is ``sudo rm  /var/lib/dpkg/lock`` .
This assumes you're not multitasking and installing something else in another terminal on the same host.

I get this error installing ``sawtooth-cxx-sdk``: ``Depends: protobuf but it is not installable``
--------------------------------------------
The C++ SDK package is in the nightly repository.
Until the package dependency is fixed, here's a workaround to force an install:


.. code:: sh

    $ sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 44FC67F19B2466EA
    $ sudo apt-add-repository "deb [trusted=yes] http://repo.sawtooth.me/ubuntu/nightly xenial universe"
    $ sudo apt update
    $ apt download sawtooth-cxx-sdk
    $ sudo dpkg -i  sawtooth-cxx-sdk_1.1.1.dev808_amd64.deb
    $ pkg contents sawtooth-cxx-sdk

I get this error when running ``sawtooth setting list`` or ``xo list`` : ``Error 503: Service Unavailable``
-----------------------------
This usually occurs when there is no genesis node created. To create, type the following:

.. code:: sh

    # Create the genesis node:
    sawtooth keygen
    sawset genesis
    sudo -u sawtooth sawadm genesis config-genesis.batch
    # Start the validator:
    sudo sawadm keygen
    sudo -u sawtooth sawtooth-validator -vv

I get this error when running ``sudo -u sawtooth sawadm genesis config-genesis.batch`` : ``Permission denied``
------------------------------------
The ownership or permission is wrong. To fix it, type:

.. code:: sh

    $ sudo chown sawtooth:sawtooth /var/lib/sawtooth
    $ sudo chmod 750 sawtooth:sawtooth /var/lib/sawtooth
    $ ls -ld /var/lib/sawtooth
    drwxr-x--- 2 sawtooth sawtooth 4096 Jun  2 14:43 /var/lib/sawtooth

How to I delete previously-existing blockchain data?
----------------------------------
Type the following: ``sudo -u sawtooth rm -rf /var/lib/sawtooth/*``
This deletes the entire database--for development and purposes.

How do I delete or change a specific block in the blockchain?
----------------------------------------
You cannot delete blocks--they are immutable by design.
You can create a new transaction (or block of transactions)
that reverse a previous transaction.

I get a usage error running ``sawnet peers`` or ``sawnet list-blocks``
----------------------------------------------------
These commands were added after the Sawtooth 1.0.4 release and are not available in earlier releases.

How can I change on-chain settings without deleting the blockchain?
------------------------------------------
Use the ``sawset`` command. This allows you to change settings such
as maximum batches per block or target wait time.

I got error ``gpg: keyserver receive failed: keyserver error`` when executing ``sudo apt-key adv --keyserver hkp://keyserver...``
------------------------------
This error means your machine couldn't add the supplied key to trusted list. This key is later used to authenticate and get sawtooth package.
One of the possible reason for this error is that your machine is trying to connect to keyserver through a proxy server. Add proxy server details in the command to solve this issue. For examople, ``sudo apt-key adv --keyserver-options http-proxy=http://[username:password]@<proxyserver>:<port> --keyserver hkp://keyse...`` (notice usage of flag ``--keyserver-options`` here).

What does this error mean ``repository ... xenial InRelease' doesn't support architecture 'i386'``?
---------------------------
Following could be possibilities:

#. You installed on a 32-bit-only system. Install on a 64-bit system.

#. You are using 64-bit system, but your linux variant has enabled additional architecture i386. ``apt`` is expecting the repository for all configured architectures on your machine. One safe way to solve this error would be to tell ``apt`` to get only 64-bit repository. For example, ``sudo add-apt-repository 'deb [arch=amd64] http://repo.sawtooth.me/ubuntu.....'``.

I get this error running ``sawset``: ``ModuleNotFoundError: No module named 'colorlog'``
-------------------------------
Something went wrong with installing Python dependencies or they were removed.
In this case, install ``colorlog`` with ``sudo apt install python3-colorlog`` or with``pip3 install colorlog``

I get this error starting Sawtooth: ``lmdb.DiskError: /var/lib/sawtooth/poet-key-state-03efb2aa.lmdb: No space left on device``
-----------------------------
Besides the obvious problem of no disk space, it could be your OS or filesystem does not support sparse files. The LMDB databases used by Sawtooth are 1TB sparse (mostly unallocated) files.

I get this error when running ``sawtooth sawadm genesis config-genesis.batch``:  ``Processing config-genesis.batch... Error: Unable to read config-genesis.batch``
------------------------------
This error can occur when there is no sawtooth user and group.
This should have been done by the package ``postinst`` script.
To add, type ``addgroup --system sawtooth; adduser --system --ingroup sawtooth sawtooth`` .

It could be a file or directory permission problem--try changing the file ownership with ``chown sawtooth:sawtooth config-genesis.batch`` and move it to a sawtooth-writable directory. For example ``mv config-genesis.batch /tmp; cd /tmp``

Another cause is the file doesn't exist. Create it with ``sawset genesis`` .


How do I install Sawtooth on AWS?
----------------
* Sign up for a free AWS Free Tier account, if you don't have an account. The AWS Free Tier is free for qualifying developers. This gives you 1 Micro instance (or any combination of instances up to 750 hours/month) for 12 months. See https://aws.amazon.com/free/
* Create your instance from the Hyperledger Sawtooth product page on AWS Marketplace, at https://aws.amazon.com/marketplace/pp/B075TKQCC2
* Follow instructions to launch an AWS Marketplace instance at
https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/launch-marketplace-console.html
* Then follow the instructions for using your Sawtooth AWS instance at
https://sawtooth.hyperledger.org/docs/core/nightly/master/app_developers_guide/aws.html

How do I use ssh with AWS?
--------------------
By default ssh access to AWS instances are disabled.
To enable, first paste the contents of your public key, at `` ~/.ssh/id_rsa.pub`` , to ``Key Pairs``  under your EC2 Dashboard.  Use this key when creating your Sawtooth instance.

After creating your AWS Sawtooth instance, go to your EC2 Dashboard and click on the security group for your instance (usually ``default``). Select the ``Inbound`` tab and  ``Edit``. Add ``SSH`` (TCP port 22) and Source ``Anywhere`` (or ``My IP`` and your IP address) and save.  I had to reboot the instance (Actions --> InstanceState --> Reboot) to get it to work.


How to I build Sawtooth from source?
------------------------------------
Use ``git`` to download the source, then ``build_all`` to build. Type ``./bin/build_all`` for options. For example:
.. code:: sh

    $ sawtooth --version
    $ git clone https://github.com/hyperledger/sawtooth-core
    $ cd sawtooth-core
    $ ./bin/build_all -l python

For details, see
https://github.com/hyperledger/sawtooth-core/blob/master/BUILD.md

How do I install Sawtooth on FreeBSD?
------------------------------
Sawtooth is supported for Ubuntu Linux with binary packages.
For other other \*IX-like systems, including FreeBSD, you can build from source.
The following blog may help:
https://wiki.freebsd.org/HyperledgerSawtooth
This is based on FreeBSD 11.1. Docker is not required to run Sawtooth.
See also this bug for the status of the FreeBSD Sawtooth port:
https://bugs.freebsd.org/bugzilla/show_bug.cgi?id=228581

I get this error while installing Sawtooth: ``Error starting userland proxy: listen tcp 0.0.0.0:8080: bind: address already in use``
-----------------------------
You already have a program running that uses TCP port 8080. Either kill it or change the port you use to something else.
To find the process(es) that have port 8080 open, type ``sudo lsof -t -i:8080``
Then kill the processes. Check again that they have not restarted. Also check that they are not Docker containers that have restarted.

I get this error after setting up a Sawtooth network: ``Can't send message PING_RESPONSE back to ... because connection OutboundConnectionThread- tcp://192.168.0.100:8800 not in dispatcher``
----------------------------------------------------
The usual problem when you get this message is configuring the peer endpoints

* If you are using Ubuntu directly instead of Docker, use the Validator's hostname or IP address instead of the default (``validator``), which only works with Docker, or ``localhost``, which may not be routable

* If you are using Docker, make sure the Docker ports are mapped to the Ubuntu OS, and that the OS IP address/port is routable between the two machines. Check the ``expose:`` and ``ports:`` entries in your ``docker-compose.yaml`` file or similar file

* Verify network connectivity to the remote machine with ``ping``

* Verify port connectivity ``telnet aremotehostname 8800`` (replace ``aremotehostname`` with the remote peer's hostname or IP address). Control-c out if it connects

* Verify network and port connectivity in the other direction (remote to local)

* Check peer configuration in your local and remote ``/etc/sawtooth/validator.toml`` files. Check the ``peering`` and ``endpoint`` lines. Check the ``seeds`` line (for dynamic peering) or ``peers`` line (for static peering)

I get ``unmet dependencies`` errors installing Sawtooth on Ubuntu 18.04 LTS.
---------------------------
Ubuntu 18.04 LTS is not supported yet. Use Ubuntu 16.04 LTS for now.
You can also install Sawtooth with Docker. See:
https://sawtooth.hyperledger.org/docs/core/releases/latest/app_developers_guide/docker.html

I get this error installing Sawtooth: ``No matching distribution found for sawtooth_rest_api``
--------------------------
You tried to install Sawtooth using Python pip.
I don't know if could work. I know installing Sawtooth using Ubuntu/Debian installation tools (such as apt, apt-get, dpkg, aptitude) works OK

I get this error with ``add-apt-repository``: Couldn't connect to accessibility bus: Failed to connect to socket . . . Connection refused
----------------------------
It is just a warning and you can ignore it. Verify the Sawtooth repository was added in ``/etc/apt/sources.list`` The cause is the command tried to start a graphic display (probably over SSH) when it was not available. A workaround to remove the warning is to add ``export NO_AT_BRIDGE=1`` to ``~/.bashrc``

How do I list sawtooth command line options?
--------------------------------------------
For the Sawtooth CLIs (sawadm, sawset, sawnet, sawtooth), append ``-h`` after the command to list subcommands (for example, ``sawadm -h`` ). For the Sawtooth subcommands, append ``-h`` after the subcommand (for example, ``sawadm keygen -h`` ).


[PREVIOUS_ | HOME_ | NEXT_]

.. _PREVIOUS: sawtooth.rst
.. _HOME: README.rst
.. _NEXT: transaction-processing.rst

© Copyright 2018, Intel Corporation.
