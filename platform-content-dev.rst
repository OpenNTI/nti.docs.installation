********************************
Content Developer Installation Guide
********************************

This guide provides information to setup and run a local server environment to support local development of content for the platform.

This guide assumes a macOS installation environment.  While a similar process can be followed on a \*NIX environment your mileage may vary.

System Preparation
==================

There are two sets of system preparation instructions. One for those who wish to use python virtual environments and `those who do not <https://github.com/NextThought/nti.docs.installation/blob/master/parts/system-prep-no-virtualenv.rst>`_.

Platform Setup
==============

Now we can move on to actually installing the platform components.  This guide assumes the server will be installed beneath `~/Projects`.  If you wish to install the server elsewhere you will need to adjust the steps in the remainder of the guide.

Our server infrastructure uses `Buildout <http://www.buildout.org/en/latest/>`_ to install and manage server configuration and dependencies for our main application server and dependent applications.  At a high level, buildout takes care of setting up your system given a specification defined in a buildout config file.

#. Checkout the dataserver buildout configuration files::

    cd ~/Projects
    svn co https://repos.nextthought.com/svn/nti-svn/NextThoughtPlatform/trunk/nti.dataserver-buildout nti.dataserver-buildout
    cd nti.dataserver-buildout

#. Initialize the buildout environment::

    ./bootstrap.sh
    
#. Run buildout::

    ./bin/buildout -c platform_content_developer_environment.cfg


Platform Operation
==================

Start the server
----------------

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

    http https://alpha.dev:8082/dataserver2/logon.ping

Updating the platform
---------------------

You'll want to ensure you update the server code frequently.  Most people update at least daily.  The following steps can be used to update the server.  Again, don't forget to ensure you are in the proper virtualenv.

::

    cd ~/Projects/nti.dataserver-buildout
    svn up
    ./bootstrap.sh
    ./bin/buildout -c platform_content_developer_environment.cfg

Buildout provides a mechanism for updating only the source code (without running full buildout). If there are source only changes you need pulled in this is often much faster than running full buildout.

::

    cd ~/Projects/nti.dataserver-buildout
    ./bin/develop update

You can also update specific sources using:

::

    ./bin/develop update my.package.name

Server Scripts
--------------

There are a number of useful scripts buildout installs in its ``bin`` directory.  This directory includes many ``nti_*`` prefixed scripts that can be used to execute admin functions in your server.  For example ``nti_create_user`` provides a command line mechanism for creating admin users.  All scripts should provide a ``-h`` arg giving unix style help output.


Tips & Tricks
=============

Git Rebase on Pull
------------------

If you haven't already done so, configure ``git`` to make all new branches rebase on pull by default:

::

    git config branch.autosetuprebase always --global


Set ``master``, ``develop`` to default to rebase on pull

::

    git config branch.master.rebase true
    git config branch.develop.rebase true


We cannot make this change centrally. It must be made per-clone.  This explains why you would want to rebase on pull: http://stevenharman.net/git-pull-with-automatic-rebase

It basically simplifies your interactions. so you can simply ``git pull`` to get updated code, instead of ``git pull -r`` or ``git fetch && git rebase...`` etc. With out this change, a ``git pull`` will make a merge bubble, and thats just ugly.


