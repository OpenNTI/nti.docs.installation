================================
Web Developer Installation Guide
================================

This guide provides information to setup and run a local server environment to support local development of platform web components.

This guide assumes a macOS installation environment.  While a similar process can be followed on a \*NIX environment your mileage may vary.

Server Setup
------------
Our server infrastructure uses `Buildout <http://www.buildout.org/en/latest/>`_ to install and manage server configuration and dependencies for our main application server and dependent applications.  At a high level, buildout takes care of setting up your system given a specification defined in a buildout config file.

Prerequisites
+++++++++++++

Our installation requires several prerequisites that are first installed via `MacPorts <https://www.macports.org>`_. In addition to macports installed libraries, XCode, XCode CLI Tools, and the JAVA JDK are required.

#. Download and install XCode from the appstore
#. Download and install XCode CLI Tools::

    xcode-select --install
    xcodebuild -license

#. Download and install latest `Oracle JDK <http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html>`_
#. Install `MacPorts <https://www.macports.org/install.php>`_
#. Once macports in installed run::

    sudo port install python27 py27-pip libxml libxml2 nasm pcre cyrus-sasl2 graphviz-devel wget openjpeg xmlsec

#. Set python27 as the active python::

    sudo port select --set python python27

#. Set python27 as the active python2::

    sudo port select --set python2 python27

#. Set pip27 as the active pip::

    sudo port select --set pip pip27

#. Update MarkupSafe to 1.0 (Latest in MacPorts is 0.23)::

    sudo pip install -U markupsafe

#. Follow the `GitHub directions <https://help.github.com/articles/adding-a-new-ssh-key-to-your-github-account/>`_ to create and add a SSH key

#. Download and install the macOS lxml berkeleydb hack::

    git clone git@github.com:NextThought/nti-lxml-macOS-berkeleydb-hack.git
    pushd nti-lxml-macOS-berkeleydb-hack
    make
    popd
    rm -rf nti-lxml-macOS-berkeleydb-hack

#. Add the following lines to ``~/.profile``::

    export VIRTUAL_ENV=/opt/local
    export CPPFLAGS=-I/opt/local/include
    export LDFLAGS=-L/opt/local/lib

#. Run ``source ~/.profile``

#. Create the following directories::

    mkdir ~/Projects
    mkdir ~/Projects/DataserverGlobalLibrary
    
Install the server
++++++++++++++++++

Now we can move on to actually installing the server components.  This guide assumes the server will be installed beneath `~/Projects`.  If you wish to install the server elsewhere you will need to adjust the steps in the remainder of the guide.

#. Checkout the dataserver buildout configuration files::

    cd ~/Projects
    svn co https://repos.nextthought.com/svn/nti-svn/NextThoughtPlatform/trunk/nti.dataserver-buildout nti.dataserver-buildout
    cd nti.dataserver-buildout

#. Initialize the buildout environment::

    ./bootstrap.sh
    
#. Run buildout::

    ./bin/buildout -c platform_web_developer_environment.cfg

Start the server
++++++++++++++++

At this point we should have everything we need installed and setup to run the server. The server consists of a handful of processes.  `Supervisor <http://supervisord.org>`_ is used to manage these processes so that you don't need to manage them manually.  To start the server move to the buidout directory and start the supervisor daemon.

::

    cd ~/Projects/nti.dataserver-buildout
    ./bin/supervisord -n

You should see output that shows all the processes are running.  If you aren't sure you can use `supervisorctl` to verify the status of each process.

::

    ./bin/supervisorctl status

If any processes are failing to start you can check the process logs in ``var/log`` to look for errors.  If you ever run into issues with the server this information is useful in debugging.

Assuming everything is up you should be able to hit the server. A good litmus test is that fetching logon.ping should return a 200. I like to use `HTTPie <https://httpie.org>`_ for my command line interactions with the server although you can certainly use any REST client.

::

    http https://localhost:8082/dataserver2/logon.ping


Updating the server
+++++++++++++++++++

You'll want to ensure you update the server code frequently.  Most people update at least daily.  The following steps can be used to update the server.  Again, don't forget to ensure you are in the proper virtualenv.

::

    cd ~/Projects/nti.dataserver-buildout
    svn up
    ./bootstrap.sh
    ./bin/buildout -c platform_web_developer_environment.cfg

Buildout provides a mechanism for updating only the source code (without running full buildout). If there are source only changes you need pulled in this is often much faster than running full buildout.

::

    cd ~/Projects/nti.dataserver-buildout
    ./bin/develop update

You can also update specific sources using:

::

    ./bin/develop update my.package.name

Server Scripts
++++++++++++++

There are a number of useful scripts buildout installs in its ``bin`` directory.  This directory includes many ``nti_*`` prefixed scripts that can be used to execute admin functions in your server.  For example ``nti_create_user`` provides a command line mechanism for creating admin users.  All scripts should provide a ``-h`` arg giving unix style help output.



App Installation
----------------

