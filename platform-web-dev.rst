================================
Web Developer Installation Guide
================================

This guide provides information to setup and run a local server environment to support local development of platform web components.

This guide assumes a Mac OSX installation environment.  While a similar process can be followed on a \*NIX environment your mileage may vary.

Server Setup
------------
Our server infrastructure uses `Buildout <http://www.buildout.org/en/latest/>`_ to install and manage server configuration and dependencies for our main application server and dependent applications.  At a high level, buildout takes care of setting up your system given a specification defined in a buildout config file.

Prerequisites
-------------

Our installation requires several prerequisites that are first installed via `MacPorts <https://www.macports.org>`_. In addition to macports installed libraries, XCode, XCode CLI Tools, and the JAVA JDK are required.

#. Download and install XCode
#. Download and install XCode CLI Tools
#. Download and install latest Oracle JDK
#. Install `MacPorts <https://www.macports.org>`_
#. Once macports in installed run::

    sudo port install python27 libxml libxml2 nasm pcre cyrus-sasl2 graphviz-devel wget openjpeg xmlsec

#. Set python27 as the active python::

    sudo port select --set python python27

#. Set python27 as the active python2::

    sudo port select --set python2 python27

#. Follow the `GitHub directions <https://help.github.com/articles/adding-a-new-ssh-key-to-your-github-account/>`_ to create and add a SSH key

#. Download and install the macOS lxml berkeleydb hack::

    git clone git@github.com:NextThought/nti-lxml-macOS-berkeleydb-hack.git
    pushd nti-lxml-macOS-berkeleydb-hack
    make
    popd

#. Add the following lines to ``~/.profile``::

    export VIRTUAL_ENV=/opt/local
    export CPPFLAGS=-I/opt/local/include
    export LDFLAGS=-L/opt/local/lib

