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

   #. Set py27-virtualenvwrapper as the active virtualenvwrapper::

       sudo port select --set virtualenvwrapper py27-virtualenvwrapper

#. Add the following lines to ``~/.profile``::

    export VIRTUALENVWRAPPER_PYTHON='/opt/local/bin/python2.7'
    export VIRTUALENVWRAPPER_VIRTUALENV='/opt/local/bin/virtualenv-2.7'
    export VIRTUALENVWRAPPER_VIRTUALENV_CLONE='/opt/local/bin/virtualenv-clone-2.7'
    source /opt/local/bin/virtualenvwrapper.sh-2.7

#. Run ``source ~/.profile``

#. Create your virtual environment::

    mkvirtualenv userdocs

#. Follow the `GitHub directions <https://help.github.com/articles/adding-a-new-ssh-key-to-your-github-account/>`_ to create and add a SSH key

#. Clone the end user documentation repository::

       mkdir -p ~/Projects
       cd ~/Projects
       git clone git@github.com:NextThought/nti.docs.platform.enduser.git

   NOTE: If asked to accept the fingerprint of 'github.com', select yes.

Developing Content
==================

Each time you go to make changes to the content you should follow the flow below.

#. Preparation to make edits

   #. Open a new terminal
   
   #. In the terminal run the following commands::
   
       workon userdocs
       cd ~/Projects/nti.docs.platform.enduser
       git checkout master
       git pull

   #. If starting a new update, you will need to create a new branch to hold the work. If you are continuing work on an existing update proceed to the next step.::

       git branch branchname
       git push --set-upstream origin branchname
      
   NOTE: Branch names should be in the format of 'user/brief_discription_of_update_reason' e.g. 'sjones/release_lola_llama'
   
   #. Checkout the branch for the work at hand::
   
       git checkout sjones/release_lola_llama

#. Edit workflow. While making changes you should cycle through the steps frequently. You should make small sets of changes at a time, preview the changes, and then commit your work frequently so nothing is lost in cases of failure or user mistake.

   #. Edit and save the appropriate files in ``~/Projects/nti.docs.platform.enduser/guide``
   
   #. Preview changes by running the following commands in the terminal::
   
       make clean
       make html
       open ~/Projects/nti.docs.platform.enduser/_build/html/index.html
   
   #. Regularly commit your changes back to the repository to protect against machine failure or large errors::
   
       git add .
       git commit -m "Checkpoint"
       git push
       
   #. Repeat steps 1-3 until all changes are made and satisfactory or you need to stop working on it.

#. At the end of the work session

   #. If the update is not complete, commit and push a checkpoint of your work. Otherwise skip to the next step.::
   
       git add .
       git commit -m "Checkpoint"
       git push
       
   #. If the updates are complete, perform the following::
   
      #. Commit all outstanding changes with a commit message that summarizes the changes made. If there are no outstanding changes, this will cause an error mesage to occur. In that case we will add descriptive commit message in a latter stage.::
      
          git add .
          git commit -m "Descriptive commit message"
          git push

      #. Create a pull request for your branch using the GitHub UI.

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

