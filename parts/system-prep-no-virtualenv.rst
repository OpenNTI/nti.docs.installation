System Preparation
==================

Our installation requires several prerequisites that are first installed via `MacPorts <https://www.macports.org/install.php>`_. In addition to macports installed libraries, XCode, XCode CLI Tools, and the JAVA JDK are required.

#. Download and install XCode from the appstore
#. Download and install XCode CLI Tools::

    xcode-select --install
    sudo xcodebuild -license

#. Download and install latest `Oracle JDK <http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html>`_
#. Install `MacPorts <https://www.macports.org/install.php>`_

   #. Once macports is installed run::

       sudo port install python27 py27-pip libxml libxml2 nasm pcre cyrus-sasl2 graphviz-devel wget openjpeg xmlsec ghostscript pdf2svg

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
