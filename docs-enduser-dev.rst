****************************************
End User Documentation Development Guide
****************************************

This guide provides information to setup and use an environment for developing end user documentation with Sphinx.

This guide assumes a macOS installation environment.  While a similar process can be followed on a \*NIX environment your mileage may vary.

System Setup
============

Our installation requires several prerequisites that are first installed via `MacPorts <https://www.macports.org/install.php>`_. In addition to macports installed libraries, XCode, XCode CLI Tools, and the JAVA JDK are required.

#. Download and install XCode from the appstore
#. Download and install XCode CLI Tools::

    xcode-select --install
    xcodebuild -license

#. Install `MacPorts <https://www.macports.org/install.php>`_

   #. Once macports is installed run::

       sudo port install python27 py27-pip py27-virtualenvwrapper

   #. Set python27 as the active python::

       sudo port select --set python python27

   #. Set python27 as the active python2::

       sudo port select --set python2 python27

   #. Set pip27 as the active pip::

       sudo port select --set pip pip27

#. Create your virtual environment::

    TBD

#. Follow the `GitHub directions <https://help.github.com/articles/adding-a-new-ssh-key-to-your-github-account/>`_ to create and add a SSH key

#. Clone the end user documentation repository::

       mkdir -p ~/Projects
       cd ~/Projects
       git clone git@github.com:NextThought/nti.docs.platform.enduser.git

Developing Content
==================

Each time you go to make changes to the content you should follow the flow below.

#. Preparation

   #. Open a new terminal
   
   #. In the terminal run the following commands::
   
       workon userdocs
       cd ~/Projects/nti.docs.platform.enduser
       git checkout master
       git pull

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

