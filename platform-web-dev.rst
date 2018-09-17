********************************
Web Developer Installation Guide
********************************

This guide provides information to setup and run a local server environment to support local development of platform web components.

This guide assumes a macOS installation environment.  While a similar process can be followed on a \*NIX environment your mileage may vary.

System Preparation
==================

Our installation requires several prerequisites that are first installed via `MacPorts <https://www.macports.org/install.php>`_. In addition to macports installed libraries, XCode, XCode CLI Tools, and the JAVA JDK are required.

#. Download and install XCode from the appstore
#. Download and install XCode CLI Tools::

    sudo xcode-select --install
    sudo xcodebuild -license

#. Download and install latest `Oracle JDK <http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html>`_
#. Install `MacPorts <https://www.macports.org/install.php>`_

   #. Once macports is installed run::

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

#. Add the following entries to ``/etc/hosts``::

    127.0.0.1    alpha.dev
    127.0.0.1    connect.dev
    127.0.0.1    demo.dev
    127.0.0.1    edbooks.dev
    127.0.0.1    history.dev
    127.0.0.1    i2.dev
    127.0.0.1    iled.dev
    127.0.0.1    janux.dev
    127.0.0.1    k20.dev
    127.0.0.1    litworld.dev
    127.0.0.1    mathcounts.dev
    127.0.0.1    oc.dev
    127.0.0.1    okstate.dev
    127.0.0.1    prmia.dev
    127.0.0.1    genius.dev
    127.0.0.1    housing.dev

#. Install `Node.js <http://nodejs.org>`_

   #. Install `nvm <https://github.com/creationix/nvm>`_
   #. Use nvm to install NodeJS::

       nvm install node
       nvm install lts/*
       nvm alias default node

   #. Connect to `NextThought private NPM repo <https://npm.nextthought.com>`_ with the credentials provided as part of your onboarding process::

       npm set registry https://npm.nextthought.com
       npm login --registry https://npm.nextthought.com

Platform Setup
==============

Now we can move on to actually installing the platform components.  This guide assumes the server will be installed beneath `~/Projects`.  If you wish to install the server elsewhere you will need to adjust the steps in the remainder of the guide.

Our server infrastructure uses `Buildout <http://www.buildout.org/en/latest/>`_ to install and manage server configuration and dependencies for our main application server and dependent applications.  At a high level, buildout takes care of setting up your system given a specification defined in a buildout config file.

#. Checkout the dataserver buildout configuration files::

    cd ~/Projects
    git clone git@github.com:NextThought/nti.dataserver-buildout.git nti.dataserver-buildout
    cd nti.dataserver-buildout

#. Initialize the buildout environment::

    ./bootstrap.sh
    
#. Run buildout::

    ./bin/buildout -c platform_web_developer_environment.cfg


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
    ./bin/buildout -c platform_web_developer_environment.cfg

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

Application Development Quickstart
----------------------------------

Check out the web app

::

    git clone git@github.com:NextThought/nti.web.app
    cd nti.web.app
    npm install

While you're working on this project, run:

::

    npm start

To build the project

::

    npm run build

To run the test suite

::

    npm test

The test run using jest, you can pass the same arguments to npm test as jest


Tips & Tricks
=============

Git Rebase on Pull
------------------

If you haven't already done so, configure ``git`` to rebase on pull by default.

::

    git config --global pull.rebase true
    

Or if you prefer to manage this per repo/branch: This sets ``master`` and ``my-feature`` to default to rebase on pull:

::

    git config branch.master.rebase true
    git config branch.my-feature.rebase true


We cannot make this change centrally. It must be made per-clone.  This explains why we want to rebase on pull: http://stevenharman.net/git-pull-with-automatic-rebase

It basically simplifies your interactions. so you can simply ``git pull`` to get updated code, instead of ``git pull -r`` or ``git fetch && git rebase...`` etc. With out this change, a ``git pull`` will make a merge bubble, and thats just ugly.

Text Editors
------------

    As long as you can have a LIVE eslint plugin with your editor, you should be good to go. If you prefer an editor that can't do that, you need to run ``npm run check`` pretty regularly. ``JenkinsBot`` will file a bug if lint is checked in.


* `Atom <https://atom.io/>`_ is one of our favorite text editors. You can use the package manger either in app or on the command line with ``apm`` (like ``npm``) to install packages:

  * Required packages

    * ``editorconfig`` - plugin to consume our project defined tabbing/newline rules.
    * ``nuclide`` or ``atom-ide-ui`` or ``linter`` - UI suport for displaying code issues.
    * ``linter-eslint`` - linter engine for JS files
    * ``language-babel`` - Language support (syntax highlighting) for modern JS (above and beyond core `language-javascript`)

* `Visual Studio Code <http://code.visualstudio.com/>`_ (like atom) very nice out-of-box experience.
* `Sublime Text <http://www.sublimetext.com/>`_ As of 3.0, very clean & fast.

