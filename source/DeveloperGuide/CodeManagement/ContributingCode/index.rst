==================================
Contributing code 
==================================

The *Github* service is providing the Git code repository as well as multiple other services
to help managing complex software projects developed by large teams. It supports a certain
development process fully adopted by the DIRAC Project. 

DIRAC releases nomenclature
-----------------------------

Release version name conventions
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@

The DIRAC release versions have the form ``vXrYpZ``, where ``X``, ``Y`` and ``Z`` are incrementally
increasing interger numbers. For example, v1r0p1. ``X`` corresponds to the major release number,
``Y`` corresponds to the minor release number and ``Z`` corresponds to the patch number ( see below ).
It is possible that the patch number is not present in the release version, for example v6r7 . 

The version of prereleases used in the DIRAC certification procedure is constructed in a form: 
``vXrY-preZ``, where the ``vXrY`` part corresponds to the new release being tested and ``Z`` 
denotes the prerelease number.

Release versions are used as tags in Git terms to mark the set of codes corresponding to the
given release.

Release types
@@@@@@@@@@@@@@

We distinguish *releases*, *patches* and *pre-releases*. Releases in turn can be *major* and *minor*.

*major release*
  major releases are created when there is an important change in the DIRAC functionality involving
  changes in the service interfaces making some of the previous major release clients incompatible 
  with the new services. DIRAC clients and services belonging to the same major release are still
  compatible even if they belong to different normal releases. In the release version the major
  release is denoted by the number following the initial letter "v".
  
*minor release*      
  minor releases are created whenever a significant new functionality is added or important changes
  of the existing functionality are done. Minor releases necessitate certification step in order to make
  the new code available in production for users. In the release version minor releases are denoted
  by the number following the letter "r".
  
*patch*
  patches are created for a minor and/or obvious bug fixes and minor functionality changes. This
  is the responsibility of the developer to ensure that the patch changes only fix known problems 
  and do not introduce new bugs or undesirable side effects. Patch releases are not subject to the
  full release certification procedure. Patches are applied to the existing releases. In the release 
  version patch releases are denoted by the number following the letter "p".

*pre-release*
  the DIRAC certification procedure goes through a series of *pre-releases* used to test carefully the
  code to be released in production. The prerelease versions have a form ``vXrY-preZ``.  

DIRAC release branches
-------------------------

The following branches are used in managing DIRAC releases:

*integration branch*
  this branch is used to assemble new code developments that will be eventually released as a new major or
  minor release. 
  
*release branches*
  the release branches contain the code corresponding to a given major or minor release. This is the production
  code which is distributed for the DIRAC installations. The release branches are created when a new minor
  release is done. The patches are incorporated into the release branches. The release branch names have the
  form ``rel-vXrY``, where ``vXrY`` part corresponds to the branch minor release version.  
  
*master branch*
  the master branch corresponds to the current stable production code. It is a copy of the corresponding
  release branch.   

Contributing new code
-----------------------

The developers are working on the new codes following the procedure below. 

Github repository developer fork
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@

All the DIRAC developers must register as Github users. Once registered, they create their copies of the
main DIRAC code repository, so called *forks*. Now they have two remote repositories to work with. 

Local Git environment
@@@@@@@@@@@@@@@@@@@@@@@

In the local Git environment developers create two "remotes" corresponding to the two remote repositories
above:

*release*
  this remote is pointing to the main DIRAC project repository. It can be created using the following
  command:::
    
    git remote add release git@github.com:DIRACGrid/DIRAC.git

*origin*
  this remote is pointing to the DIRAC project personal fork repository of the developer. It can be 
  created using the following command:::
    
    git remote add origin git://github.com/<username>/DIRAC.git
    
  where the <username> is the use name of the developer in the Github service
  
Note that the names of the remotes, *upstream* and *origin*, are conventional. But it is highly
recommended to follow this convention to have homogeneous environment with other developers.

Working on the new code
@@@@@@@@@@@@@@@@@@@@@@@@@@

The work on the new features to be incorporated eventually in a new release should start in a local
branch created from the current *integration* branch of the main DIRAC repository. Let call the 
new development branch "newdev", for example. It shoukd be created with the following commands:::

  git fetch release
  git checkout -b newdev release/integration
  
This will create the new *newdev* branch locally starting from the current status of the main DIRAC
repository. The "newdev" branch becomes the working branch. 

The new codes are created in the *newdev* branch and when they are ready to be incorporated into
the main DIRAC code base, the following procedure should be followed. First the local development
branch should receive all the new changes in the main *integration* branch that were added since
the development branch was created:::

   git checkout newdev
   git fetch release  
   git rebase release/integration
   
This might need resolving possible conflicts following git instructions. Once the conflicts are
resolved, the *newdev* branch should be pushed to the developer personal Github repository:::

   git push origin newdev
   
Now the newly developed code is in the personal Github repository and the developer can make a 
*Pull Request* (*PR*) to incorporate it into the main integration branch. This is done using the Github
service web interface. This interface is changing often since the Github service is evolving. But
the procedure includes in general the following steps:

- go to the personal fork of the DIRAC repository in the Github portal 
- choose the *newdev* branch in the branch selector
- press the "Pull Request" button 
- choose the *integration* as the target branch of the *PR*
- give a meaningful name to the *PR* describing shortly the new developments
- give a more detailed description of the new developments suitable to be included into
  the release notes
- press "Submit Pull Request" button  

The *PR* is submitted. It is now up to the release manager to examine the *PR* and to incorporate
it into the new release. 

After the *PR* is submitted and before it is merged into the main *integration* branch, the developer
can still add new changes to the *newdev* branch locally and push the changes to the *origin* personal
remote repository. These changes will be automatically added to the *PR* already submitted. After the
*PR* is merged by the release manager into the main *integration* branch, it is recommended to remove
the *newdev* branch from the remote personal repository in order to avoid conflicts with later uploads
of this branch. This can be done with the following command:::

  git push origin :newdev
  
Working on a patch
@@@@@@@@@@@@@@@@@@@@

Making a patch is very similar to contributing the new code. The only difference is that the target 
branch for the corresponding *PR* is the release branch to which the patch is meant to. For the developer
it is very important to choose the right target release branch. The release branches in the main
project repository are containing the code that is currently in production. Different DIRAC installations
may use different releases. Therefore, the target release branch for a patch is the earliest release still
in production for some DIRAC installations and for which the patch is relevant. 

As a matter of reminder, here is a set of commands to make a patch. First, start with the new branch
to work on the patch based on the target release branch, for example rel-v6r7 :::

  git fetch release
  git checkout -b fix-v6r7 release/rel-v6r7
  
Make the necessary changes to the code of the branch and then push them to the developer's fork:::

  git push origin fix-v6r7
  
Do the *PR* with the rel-v6r7 as a target branch. Scrap the patch branch from the forked repository:::

  git push origin :fix-v6r7     

The patches incorporated into a release branch will be propagated to the more recent release branches
and to the integration branch by te release manager. There is no need to make separate *PR*'s of the
same patch to other branches. 
     
Resolving *PR* conflicts
@@@@@@@@@@@@@@@@@@@@@@@@@@             
             
Even when preparing a *PR* you follow the procedure described above, there is no guarantee that there 
will be no conflicts when merging the *PR*. The release manager will ask you to find and fix merge 
conflicts made by your *PR*. Assuming you have a local clone of your DIRAC repository and the new 
code was developed in the *featurebranch*, you have to try merge it by hand to find and understand 
the source of conflicts. For that you should first checkout your feature branch, and try to rebase 
your branch on upstream/integration ( or on release branch for the patches ), e.g.::   

  $ git checkout featurebranch
  Switched to branch 'featurebranch'
  $ git fetch release
  remote: Counting objects: 1366, done.
  remote: Compressing objects: 100% (528/528), done.
  remote: Total 1138 (delta 780), reused 952 (delta 605)
  Receiving objects: 100% (1138/1138), 334.89 KiB, done.
  Resolving deltas: 100% (780/780), completed with 104 local objects.
  From git://github.com/DIRACGrid/DIRAC
   * [new branch]      integration -> DIRAC/integration
   * [new branch]      master     -> DIRAC/master
   * [new tag]         v6r0-pre1  -> v6r0-pre1
   * [new tag]         v6r0-pre2  -> v6r0-pre2
  From git://github.com/DIRACGrid/DIRAC
   * [new tag]         v6r0-pre3  -> v6r0-pre3
  $ git rebase release/integration
  First, rewinding head to replay your work on top of it...
  Applying: added .metadata to .gitignore
  Using index info to reconstruct a base tree...
  Falling back to patching base and 3-way merge...
  Auto-merging .gitignore
  CONFLICT (content): Merge conflict in .gitignore
  Failed to merge in the changes.
  Patch failed at 0001 added .metadata to .gitignore

  When you have resolved this problem run "git rebase --continue".
  If you would prefer to skip this patch, instead run "git rebase --skip".
  To restore the original branch and stop rebasing run "git rebase --abort".

On this stage git will tell you which changes cannot be merged automatically, in 
above example there is only one conflict in .gitignore file. Now you should open 
this file and find all conflict markers (">>>>>>>" and "<<<<<<<<"), edit it 
choosing which lines are valid, add make another commit and pull request.                
      
  
