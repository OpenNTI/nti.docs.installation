================================
Web Developer Installation Guide
================================

This guide provides information to setup and run a local server environment to support local development of platform web components.

This guide assumes a Mac OSX installation environment.  While a similar process can be followed on a \*NIX environment your mileage may vary.

Server Setup
------------
Our server infrastructure uses [Buildout](http://www.buildout.org/en/latest/) to install and manage server configuration and dependencies for our main application server and dependent applications.  At a high level, buildout takes care of setting up your system given a specification defined in a buildout config file.

Prerequisites
-------------

Our installation requires several prerequisites that are first installed via [MacPorts](https://www.macports.org). In addition to macports installed libraries, XCode, XCode CLI Tools, and the JAVA JDK are required.

1. Download/Install XCode
2. Download/Install XCode CLI Tools
3. Download/Install latest Oracle JDK
4. Install [MacPorts](https://www.macports.org)

Once macports in installed the following packages need to be installed.

1. `sudo port install python27`
2. `sudo port install libxml`
3. `sudo port install libxml2`
4. `sudo port install nasm`
5. `sudo port install pcre`
6. `sudo port install cyrus-sasl2`
7. `sudo port install graphviz-devel`
8. `sudo port install wget`
9. `sudo port install openjpeg`
10. `sudo port install xmlsec`

Or you can install all these at once using once command.

`sudo port install python27 libxml libxml2 ...`

Set python27 as the active python

`sudo port select --set python python27`
`sudo port select --set python2 python27`

