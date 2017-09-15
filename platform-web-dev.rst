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

1. Download and install XCode
2. Download and install XCode CLI Tools
3. Download and install latest Oracle JDK
4. Install `MacPorts <https://www.macports.org>`_
5. Once macports in installed run 
    ``sudo port install python27 libxml libxml2 nasm pcre cyrus-sasl2 graphviz-devel wget openjpeg xmlsec``

6. Set python27 as the active python
    ``sudo port select --set python python27``

7. Set python27 as the active python2
    ``sudo port select --set python2 python27``

8. Create a SSH key and add to GitHub by following the directions at https://help.github.com/articles/adding-a-new-ssh-key-to-your-github-account/

9. Download and install the macOS lxml berkeleydb hack::

    git clone git@github.com:NextThought/nti-lxml-macOS-berkeleydb-hack.git
    pushd nti-lxml-macOS-berkeleydb-hack
    make
    popd

10. Add the following lines to ``~/.profile``

