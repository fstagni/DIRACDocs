.. _eclipse_environment:

=======================
Configuring *Eclipse*
=======================

*THIS WE NEED TO MODIFY*

Two extensions are required for developing DIRAC with *Eclipse*. To install them go to 
*Help->Install new software->top right button "Add..." -> Insert name and URL* and then select the software to install in the list.

 - *pyDev* : Use http://pydev.org/updates as the URL to install from. For more info go to http://pydev.org/updates
 - *EGit* : Git team provider for eclipse. Use http://download.eclipse.org/egit/updates as the URL. 
   For more info go to http://www.eclipse.org/egit/
 
Now you need to configure the *pyDev* plugin. Go to *Window->Preferences* (*Eclipse->preferences* if you're in a MacOSX box). 
In the preferences pane go to *Pydev->Editor*, select 2 as the tab length and click "Replace tabs with spaces when typing". 
In *Pydev->Editor->Code Style->Code formatter* check all the boxes. 
 
For *EGit* you simply need to configure your name and mail. Go to the preferences pane and then go to 
*Team->Git->Configuration* and add two entries: *user.name* with your name and *user.email* with your email.

That's it! *Eclipse* is configured now :)


Creating a development installation in Eclipse
=================================================

All that remains is to import these directories as projects in Eclipse. To import DIRAC:

 1. File -> Import...
 2. Git -> Projects from Git and click *Next*.
 3. In the "Import Projects from Git" click *Add*.
 4. In the "Add Git Repositories", click *Browse* and select the DIRAC source code folder you cloned into before. Then click *Search* and the *.git* directory in the DIRAC source code directory should appear. Select it and click *OK*.
 5. In the "Import Projects from Git" pane the DIRAC folder should now appear. Select it and click *Next*.

Create as a general project

 6. Select "Import as General Project" and click *Next*.
 7. In the "Import Project from Git" write the project name of your Workspace and then click *Finish*.

Create as a pydev project (may not work in all versions of eclipse/pydev)

 6. Select "Use the New Project wizard" and click *Finish*.
 7. In the *New Project* wizard choose *Pydev -> Pydev project* and click *Next*.
 8. Choose the necessary settings for the project, in particular:
 
    - Project name, e.g. DIRAC_dev
    - The project working directory
    - Use Python 2.6 grammar
    - Choose the python interpreter ( you might need to set it up in a separate form )
    - Uncheck creation of *src* directory
    
 9. Click *Finish*.   
 
If you want to add DIRACWeb to eclipse repeat the same steps with the Web source directory. For additional extensions, add them as projects to Eclipse. You'll have to look on how to do it depending on your team provider. For instance, if you are using subversion for your extension:

 1. Go to the *SVN Repository Exploring* exploring perspective
 2. In the *SVN repositories* panel, right click -> New -> Repository Location and fill in the details for your repository
 3. Once the repository appears in the *SVN repositories* panel, browse it until you find the extension directory.
 4. Once you find the extension directory -> right click -> Find/Check out as...
 5. Select *Check out as project with the name specified* and fill in the extension name (name ending with DIRAC). For instance LHCbDIRAC
 6. Click next
 7. Uncheck *Use default workspace location* and browse to the directory where DIRAC is installed. If DIRAC is in */some/path/DIRAC*, select */some/path*
 8. Click finish

That's it! You have a nice development installation set up :)
 
 
Setting up a working set for the DIRAC workspace
--------------------------------------------------

Eclipse can manage several projects and developers may need have more than one development installation. It is useful to set up a view per installation. To define different views for each installation we will use Eclipse's *working sets*. A *working set* is nothing more than a group of projects. By defining a *working set* Eclipse can hide the rest of the projects so only the projects in the current *working set* are shown. To define a *working set*:

 1. Click on the small arrow on the package explorer and then on *Select working set...*
 
 .. image:: images/workingsets-01.png
  :align: center
  
 2. Click on *New...* and then selecte *Resource* and then click *Next>*
 
 .. image:: images/workingsets-02.png
  :align: center
  
 3. Give it a meaningful name and select all the projects you want to include in the *working set* and click *Finish*
 
 .. image:: images/workingsets-03.png
  :align: center
  
 4. Now the new working set will appear. If you want to activate it just select it and click *OK*
 
 .. image:: images/workingsets-04.png
  :align: center
 
Now, to change the active working set or to disable them:

 1. Click again on the small arrow on the package explorer and then on *Select working set...* as before
 2. Select the working set you want to activate or select *No working sets* to deactivate them
 
 
 

