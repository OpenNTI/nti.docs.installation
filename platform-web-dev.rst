================================
Web Developer Installation Guide
================================

This guide provides information to setup and run a local server environment to support local development of platform web components.

This guide assumes a Mac OSX installation environment.  While a similar process can be followed on a \*NIX environment your mileage may vary.

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

