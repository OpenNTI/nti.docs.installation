nti.dataserver-buildout Environment Setup
=========================================

Prerequisites
-------------

For any questions, please see the **Q&A** section near the bottom of the
document. In order to begin the process, make sure your machine is
equipped with these programs--and make sure you can access them in your
terminal:

-  Xcode
-  Xcode Command Line Tools
-  git (included with XCode Tools)
-  macPorts

Xcode can be downloaded from the App Store. Earlier versions can be
sourced from Apple's developer sites. See
`this <https://stackoverflow.com/questions/7047735/where-can-i-download-old-versions-of-xcode>`__
StackOverflow discussion for the most up-to-date method on accessing
these older versions.

Make sure to run Xcode at least once to accept the licensing agreement
or type ``$ xcodebuild -license`` to agree via the terminal.

Xcode Command Line Tools can be installed with the command:

::

    $ sudo xcode-select --install

once you have installed XCode.

MacPorts can be sourced from the project's
`homepage <https://www.macports.org/>`__

Also make sure you're able to clone projects from the NextThought GitHub
organization via SSH! If not, please seek assistance in setting up a
valid SSH key for git usage.

Getting Started--Preparing the Environment
------------------------------------------

Python
~~~~~~

First, we need to setup a version of python that's in line with what the
buildout requires. This might seem trivial if you're new to the process,
but time will likely prove you wrong:

|MacOS python is a journey| 

It's generally recommended to avoid the
default MacOS pre-installed system python, so let's get started by
installing a copy of Python2.7 using macPorts:

::

    $ sudo port install python27 py27-pip py27-cython

Plenty of dependencies will also be installed.

Now we can set this version of python as our default one, over the
system's supplied python:

::

    $ sudo port select --set python python27
    $ sudo port select --set python2 python27

Now let's do the same with ``pip`` and ``cython``

::

    $ sudo port select --set pip pip27
    $ sudo port select --set cython cython27

***NOTE:*** You will likely need to restart the bash session for these
changes to take effect. You can test which python you're currently using
with:

::

    $ which python

If you see ``/usr/bin/python``, then you're still on the system python!
If you see ``/opt/local/bin/python`` then you're using MacPorts
correctly.

Now lets install some basic packages we need for the buildout:

::

    $ pip install zc.buildout setuptools==44.0.0

***NOTE:*** Due to some other tools in our pipeline, we're forced into
using a version of python's ``setuptools`` *below* the modern version,
at the time of writing, we prefer version ``44.0.0``. Consult the
``versions.cfg`` in the buildout directory for the most current versions
required.

***NOTE:*** ``pip`` may give you warnings during installation that the
packages are not being installed on ``PATH``, so you might want to
update your ``PATH`` environmental variable to include the given
location if necessary. This operation can vary in different macOS
versions, so consult relevant resources for your version on how to do
that.

Subversion
~~~~~~~~~~

Subversion is no longer pre-installed with XCode (Catalina and onwards),
so we'll need the macPort for it:

::

    $ sudo port install subversion

We'll also need to do some work to get our credentials working properly.
One easy way to do this is to just clone an existing repo, which will
prompt you for credentials:

::

    $ svn co https://repos.nextthought.com/svn/nti-svn/nti.contentlibrary --username [USERNAME]

If you have no subversion credentials, seek support from other team
members.

OpenSSL
~~~~~~~

If openSSL was somehow missed by previous ports, install it with:

::

    $ sudo port install openssl

In order to get the python package ``cryptography`` working, we need to
make some changes to default ``clang`` flags. Edit your ``.zprofile`` or
other ``.bashrc`` equivalent to include these environmental variables
(on newer versions of macOS):

::

    $ nano ~/.zprofile

    #OpenSSL fix
    export CPPFLAGS=-I/opt/local/include
    export LDFLAGS=-L/opt/local/lib

This should allow openSSL to compile properly.

Getting Started--Running the Buildout
-------------------------------------

First, lets begin by cloning the ``nti.dataserver-buildout`` repo from
our GitHub organization, stepping into its directory, and examining its
current directory structure:

::

    $ git clone git@github.com:NextThought/nti.dataserver-buildout.git
    $ cd nti.dataserver-buildout
    $ ls 

If all goes well, you should be greeted with a slew of ``.cfg`` files.

Lets change to the current ``development`` branch of the project:

::

    $ git checkout development

Now you should be able to run the initial part of the buildout--the
bootstrapping process. Make sure you're in the
``nti.dataserver-buildout`` directory and run:

::

    $ buildout bootstrap

This should generate a collection of files that now reside in the
``bin/`` directory.

Now, *in theory* we should be able to run the buildout like so:

::

    $ ./bin/buildout -c [config file]

There are multiple configs you could run, depending on your needs, but
for now lets stick with the main platform configuration:

::

    $ ./bin/buildout -c platform_base_developer_environment.cfg 

There will likely be any number of issues that stop the buildout from
completing. See some of the common issues and solutions below:

Common Buildout Issues
----------------------

Error: Couldn't install: Pillow
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

This generally happens due to a mismatch between the expected SDK and
your actual SDK versions. One installation, for instance, yielded this
error:

::

    Compiling with an SDK that doesn't seem to exist: /Library/Developer/CommandLineTools/SDKs/MacOSX11.0.sdk
    Please check your Xcode installation

In this case, the build is looking for SDK version ``11.0``, which we
are lacking...or are we?

::

    $ ls /Library/Developer/CommandLineTools/SDKs
    MacOSX.sdk  MacOSX10.15.sdk MacOSX11.1.sdk  MacOSX11.3.sdk  MacOSX11.sdk

This looks like a versoning typo (``11.0`` vs ``11``). This can be fixed
with a simple symlink:

::

    $  sudo ln -s /Library/Developer/CommandLineTools/SDKs/MacOSX11.sdk /Library/Developer/CommandLineTools/SDKs/MacOSX11.0.sdk

Make sure to adjust the versions to your situation.

Error while installing reportlab (implicitly declaring library function)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

This is an issue that started appearing in Big Sur, the fix requires
some more additions to the ``CPPFLAGS`` environment variable. The
``-Wno-error=implicit-function-decleration`` flag seems to fix this
issue. Make the necessary modifications to ``.zprofile`` or other
``.bashrc`` equivalents:

::

    $ nano ~/.zprofile
    export CPPFLAGS="-I/opt/local/include -Wno-error=implicit-function-declaration"

``passwords`` Asking for Password
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The buildout may ask you for a password at some point, depending on what
configuration you're attempting to run. This is a byproduct of the
``nti.recipe.passwords`` egg being installed. Inputting the incorrect
password should raise some sort of error. Please consult with other team
members for proper credential access.

In Summary
----------

For anyone who needs the quick and dirty command line summary for
required installs and fixes: ***NOTE:*** Don't forget to install Xcode ,
its command line tools, and MacPorts first! You'll also need to add
subversion credentials similar to the method mentioned above before
running the buildout!

::

    sudo port install python27 py27-pip py27-cython
    sudo port select --set python python27
    sudo port select --set python2 python27
    sudo port select --set pip pip27
    sudo port select --set cython cython27
    pip install zc.buildout setuptools==44.0.0
    sudo port install subversion
    sudo port install openssl
    echo """ 
    export CPPFLAGS=-I/opt/local/include -Wno-error=implicit-function-declaration
    export LDFLAGS=-L/opt/local/lib
    """ >> ~/.zprofile

In Conclusion
-------------

As you could see above, getting a buildout running involves a lot of
fixing random issues as they appear--so don't get discouraged if you
encounter things not in this document. (and feel free to Pull Request
amendments as you have issues!) If you do run into issues outside of
this document's scope, here's a few things to consider in the quest for
fixes:

1. **Double-Triple check installations and versions.** You never know
   when you might be using outdated 'system' supplied software or
   versions that are either too new or too old for proper support. When
   in doubt check the ``versions.cfg`` for specific versions, and
   consult github issue pages for any packages acting up--it might just
   be an honest to god unfixed bug. Use the ``which`` command constantly
   to ensure you're in the right place and don't forget to consult the
   macPort support pages for more information on your installed
   software.
2. **Check your ``.zprofile``/``.bashrc`` and make sure environmental
   variables are set as needed.** As I showed above, there are plenty of
   compiler flags and other variables that need to be pre-set for the
   buildout to function. Some of these requirements come and go as
   OS/software versions change, to make sure to keep an active lookout
   for updates!
3. **Consider your credentials.** The MacOS keychain works in
   semi-mysterious ways, and may lock/unlock credentials for things like
   subversion without you realizing it. If you seem to have issues with
   ``git``/``svn`` look deeper into your current keychain situation.

Q&A
---

What's Buildout?
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Buildout is esentially an automation tool that helps you streamline the
creation of environments in order to test/develop software. You can find
additional information and documentation
`here <http://www.buildout.org/en/latest/>`__. 

What's MacPorts? 
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
You can think of it sort of like a package manager for MacOS. It's generally
used to install and extend software that you might be lacking in the
base MacOS environment. See more details
`here <https://www.macports.org/>`__. 

Can I use a Virtual Environment? 
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
That's frankly up to you. Considering how fast the macOS
ecosystem changes, it might be more sane than going without one. At the
same time, there's enough non-python dependencies that you'll be forced
to keep track of OS-specific quirks anyways. So for now it's your call.

Can I use Homebrew instead of MacPorts? 
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Preferably not, if you can avoid it. MacPorts is our standard tool for software installation, and this guide (among a lot of our documentation) will expect you to have
programs installed in the expected MacPorts locations. 

What if this Documentation Doesn't Address my Problem? 
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
You can try the tips above for troubleshooting and consult with team members. If you successfully find a fix, please amend this document and submit a pull request detailing the problem and your solution!

.. |MacOS python is a journey| image:: https://imgs.xkcd.com/comics/python_environment.png
