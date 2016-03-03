MultiChain Explorer
===================

MultiChain Explorer is a free block chain browser for [MultiChain](http://www.multichain.com/) blockchains.

https://github.com/MultiChain/multichain-explorer

    Copyright(C) 2015,2016 by Coin Sciences Ltd.
    Copyright(C) 2011,2012,2013 by Abe developers.
    License: GNU Affero General Public License, see the file LICENSE.txt.
    Portions Copyright (c) 2010 Gavin Andresen, see bct-LICENSE.txt.


Welcome to MultiChain Explorer!
===============================

This software reads the MultiChain block file, transforms and loads the
data into a database, and presents a web interface similar to that
popularized by Bitcoin block explorers like http://blockexplorer.com/.

MultiChain Explorer is a fork of the popular [Abe](https://github.com/bitcoin-abe/bitcoin-abe) project to add support for MultiChain blockchains with assets and permissions.  MultiChain nodes must be online for all Explorer functions to work.

MultiChain Explorer is still under  development, so things may break or change!


System Requirements
-------------------

You must have Python 2.x installed on your system.

If pycrypto is not on your system, you will have to install it:

    sudo pip install pycrypto

If your Python setup is not complete, you may have to install:

    sudo apt-get install python-dev
    sudo apt-get install python-pip

You also need to install Sqlite3 on your system.

    sudo apt-get install sqlite3 libsqlite3-dev


Installation
------------

To install MultiChain explorer for the current user (recommended):

    cd multichain-explorer
    python setup.py install --user

If you have root permission and want to install MultiChain explorer for all users on the system:

    cd multichain-explorer
    sudo python setup.py install

The explorer needs to connect to a local MultiChain node.  Before configuring the explorer, let's make sure you have a MultiChain blockchain up and running.


Create and launch a MultiChain Blockchain
-----------------------------------------

If you do not yet have a chain you want to explore, [Download MultiChain](http://www.multichain.com/download-install/) to install MultiChain and create a chain named ````chain1```` as follows:

    multichain-util create chain1
    multichaind chain1 -daemon

By default the [runtime parameter](http://www.multichain.com/developers/runtime-parameters/) ````txindex```` is enabled so that the node keeps track of all transactions across the blockchain, and not just those for the node's wallet. This is required for the Explorer to work correctly.

_The rest of this document assumes your blockchain is named ````chain1````. If not, please substitute accordingly._


Configure MultiChain.conf
-------------------------

The explorer needs to communicate with the blockchain using JSON-RPC.  When you created the blockchain, the JSON-RPC connection details were automatically created by MultiChain and stored in a file named ````multichain.conf````.

The explorer will read this file. If you examine the file you will see a username and password have been auto-generated.

    cd ~/.multichain/chain1/
    cat multichain.conf

All you need to do is add the RPC port number. Copy the ````default-rpc-port```` value from ````params.dat```` and add an entry to ````multichain.conf```` as follows:

    cd ~/.multichain/chain1/
    grep rpc params.dat
    # Make a note of the default-rpc-port value, let's say it's 1234, and add it to multichain.conf
    echo "rpcport=1234" >> multichain.conf


Configure the Explorer
----------------------

The bundled example config file ````chain1.example.conf```` can be used as a template for your own chain.

    cd multichain-explorer
    cp chain1.example.conf chain1.conf

You can store the config file ````chain1.conf```` anywhere you want. When you launch the explorer you can specify the location of your config file. By default, it will look for a config file in the current directory.

The following changes can be made:

* Change ````port```` to the port number for serving web pages.
* Change ````host```` to ````0.0.0.0```` to serve web pages to anybody.
* Change ````dirname```` to match the directory for your blockchain.
* Change ````chain```` to set how the chain should be listed in the explorer.
* Change ````connect-args```` for the location to store the explorer database.

Note: The explorer will automatically read MultiChain specific parameters such as the magic handshake, address checksum, version and script version bytes from ````params.dat````.


Launch the Explorer
-------------------

To load existing blockchain data into the explorer:

    cd multichain-explorer
    python -m Mce.abe --config chain1.conf --commit-bytes 100000 --no-serve

Look for output such as:

    block_tx 1 1
    block_tx 2 2
    ...

This step may take several minutes to even days depending on chain size and hardware.

To launch the explorer and serve web pages from your local computer:

    cd multichain-explorer
    python -m Mce.abe --config chain1.conf

By default, the explorer will be listening for web requests on port 2750, unless you changed it in the Explorer's configuration file.  In your browser visit:

    http://localhost:2750/

To launch the explorer on a server, make sure the explorer is not accidentally terminated when you close your SSH terminal connection.

    cd multichain-explorer
    nohup python -m Mce.abe --config mychain.conf &

To check the explorer is runnning, in your browser visit:

    http://ip_address_of_server:2750/


Reset the Explorer
----------------------

To start over with new chain data for a chain of the same name, simply:

1. Stop the explorer.

2. Delete the explorer database file ````chain1.explorer.sqlite```` (set in ````connect-args```` parameter in ````chain1.conf````).

3. Launch the explorer as above.


Misc Notes
----------
* Currently it is not recommended to configure multiple chains in one config file as the search function does not search across chains for an address

* You can run two instances of the Explorer with the same config file, with one being passed the ````--no-serve```` argument and the other ````--no-load````, so that one instance only loads data into the database, and the other only serves web pages.
