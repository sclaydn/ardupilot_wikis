.. _git-submodules:

==============
Git Submodules
==============

   .. image:: ../images/git-submodules.png
       :width: 70%

ArduPilot is dependent upon several external code repositories which are held in **submodules**.  These are forked, cloned and built along with ArduPilot.

- `ChibiOS <https://github.com/ChibiOS>`__
- `DroneCAN <https://github.com/DroneCAN>`__
- `waf <https://github.com/waf-project/waf>`__

This page describes how we use `git submodules <https://git-scm.com/book/en/v2/Git-Tools-Submodules>`__ in the ArduPilot build.

Submodule approach
------------------

ArduPilot uses a single level of *git submodules*, with all modules stored in the `modules <https://github.com/ArduPilot/ardupilot/tree/master/modules>`__
directory. This approach was chosen as it makes for diagnosis of issues with submodules simpler.  This means that if an external project (i.e. MAVLink) has submodules of its own, those submodule appear directly in the `ArduPilot modules directory <https://github.com/ArduPilot/ardupilot/tree/master/modules>`__.

ArduPilot maintains local forks of each external project's repo in order to shield itself from unexpected changes.

You may also note that the URLs used for the submodules use the old
``git://`` protocol. This was done to make it less likely we will get
accidental commits on the master repositories while developers are
getting used to *git submodules* (as the ``git://`` protocol is
read-only). Developers with commit access to the submodules should add a
new ardupilot remote with a writeable protocol as needed.

Updating your local repo's submodules
-------------------------------------

To manually update submodules use the following command

::

    git submodule update --recursive

Very occasionally a new submodule is added to ArduPilot, after which every developer must run this command:

::

    git submodule init

Common errors
-------------

The following is a list of comment errors and how to deal with them.

Update a modified submodule
---------------------------

If you applied modification to a submodule for a branch *develop*, it is necessary to avoid your modifications to be replaced by the original submodule when you run the command "git submodule update –init –recursive":

   - Create your own repository for the submodule where you can commit the modifications
   - In the *develop* branch repository, modify the file .gitmodule to link your own submodule repository rather than the *master* branch
   - Apply your modification to the submodule, commit and push it back to your remote repository
   - Back to the *develop* branch you can see the modification using git status. Commit the modification and you are done (example below is done for the MAVLink submodule)

::

   git add .gitmodules
   git add modules/mavlink
   git commit -m 'mavlink: submodule update + modify gitmodule'
   git push
   
The submodule MAVLink of the *develop* branch is now linked to a different repository than the *master*. The first command is necessary to reflect that change to the superproject:

::

   git submodule sync

the usual command below will then be updated with the approriate submodule according to the branch (*master* or *develop*) you are working on.

::

   git submodule update --init --recursive

Disaster recovery
-----------------

If things have gone very badly wrong with your git tree the simplest
thing to do it to remove the modules/ directory completely from your local repo and run these commands
to reinitialise and update the submodules:

::

    git submodule init
    git submodule update --recursive
