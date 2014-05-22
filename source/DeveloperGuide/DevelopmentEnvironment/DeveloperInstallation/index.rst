    .. _develper_installation:

======================================
Setting up a development installation
======================================

-------------------------------------
Sharing your development
-------------------------------------

Once you're familiar with the basics of how GIT works. You're ready to clone the DIRAC source repository.
DIRAC repository is hosted at https://github.com/DIRACGrid/DIRAC. This has been already outlined in the previous sections, so we will not repeat it here.

In any case, it's now time to setup a developer installation. A developer installation is a "closed" installation: an installation that can, even, be used while being disconnected from the network.

-------------------------------------------
Setting up your development installation
-------------------------------------------

DIRAC developers tend to use eclipse for developing DIRAC. It is not mandatory but it is recommended. The following steps
will try to guide you on setting up a development installation for DIRAC in eclipse. If you don't need/want eclipse just
follow the next section and skip the rest.

Checking out the source
=========================

First you need to check out all the sources you need to start working on DIRAC or on any extension. Go to a clean directory
( from now on we will call that directory *devRoot* ) and:

     1. Go to your *devRoot* directory
 2. Check out DIRAC source code. DIRAC source is hosted     on *github.com*. So, you have to do:            :

      git clone git@github.com:yourusername/DIRAC  .git

    This will create a *devRoot/DIRAC* for you.

    If you don't intend to develop DIRAC and just need it for developing extensions do::

      git clone https://github.com/DIRACGrid/DIRAC.git

   3. This will create a *remote* pointer ( in git terms ) in the local git repository called *origin* that points to your source repository.
      In that repository you will publish your code to be released. But all the releases will be done in the
      https://github.com/DIRACGrid/DIRAC repository.
    You need to define a *remote* for that repository to be able to pull newly released changes into your working repo.
      We will name that repository *relea    se*::

       git remote add release https://githu    b.com/DIRACGrid/DIRAC.git
     git fetch relea            se

 4. If you need DIRACWeb ex  tension, for example, do the same with the repo at https://github.com/DIRACGrid/DIRACWeb
 5. If you need to check out any extension do so in the *devRoot* directory. For instance::

       svn co svn+ssh://svn.cern.ch/reps/dirac/LHCbDIRAC/trunk/LHCbDIRAC LHCbDIRAC

 6. Repeat step 4 for each extension you need
 7. Deploy DIRAC scripts by running::

       DIRAC/Core/scripts/dirac-deploy-scripts.py

 8. It is a good idea to add the Install MySQL

    DIRAC mainly uses *MySQL* as its database engine. There are many database structures used by DIRAC. Each system tipically has more than one database structure to keep its state. The first step is to install *MySQL* from your distribution. For rpm based distributions execute::

      > yum install mysql-server
  
    For debian based distributions execute::
  
        > apt-get install mysql-server
    
    If you have either another distribution or another operative system please head to `MySQL <http://www.mysql.com/>`_ to check how to install MySQL in your host. 
      Once MySQL is installed you need to create a database in MySQL for each database you want to use or develop. First create the database::

        > mysqladmin -u root -p create yourdbname
  
    Once the database has been created::

      > mysql -u root -p yourdbname
      Welcome to the MySQL monitor.  Commands end with ; or \g.
      Your MySQL connection id is 42
      Server version: 5.6.14 MySQL Community Server (GPL)

    Copyright (c) 2000, 2013, Oracle and/or its affiliates. All rights reserved.

    Oracle is a registered trademark of Oracle Corporation and/or its
    affiliates. Other names may be trademarks of their respective
    owners.

    Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

    mysql> grant all on yourdbname to yourusername identified by 'yourpasswd';
  Query OK, 0 rows affected (0.01 sec)
  mysql> grant all on yourdbname.* to yourusername identified by 'yourpasswd';
  Query OK, 0 rows affected (0.01 sec)
  mysql> flush privileges;
  Query OK, 0 rows affected (0.01 sec)
  
With the previous steps you've created a database called *yourdbname* and authorized user *yourusername* with password *yourpasswd* to use it.
 directory to your $PATH.

I8.stall MySQL

    DIRAC mainly uses *MySQL* as its database engine. There are many database structures used by DIRAC. Each system tipically has more than one database structure to keep its state. The first step is to install *MySQL* from your distribution. For rpm based distributions execute::

      > yum install mysql-server
  
    For debian based distributions execute::

      > apt-get install mysql-server
  
    If you have either another distribution or another operative system please head to `MySQL <http://www.mysql.com/>`_ to check how to install MySQL in your host. 
    Once MySQL is installed you need to create a database in MySQL for each database you want to use or develop. First create the database::

      > mysqladmin -u root -p create yourdbname
  
    Once the database has been created::
  
        > mysql -u root -p yourdbname
        Welcome to the MySQL monitor.  Commands end with ; or \g.
      Your MySQL connection id is 42
        Server version: 5.6.14 MySQL Community Server (GPL)

      Copyright (c) 2000, 2013, Oracle and/or its affiliates. All rights reserved.

  Oracle is a registered trademark of Oracle Corporation and/or its
  affiliates. Other names may be trademarks of their respective
  owners.

  Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

  mysql> grant all on yourdbname to yourusername identified by 'yourpasswd';
  Query OK, 0 rows affected (0.01 sec)
  mysql> grant all on yourdbname.* to yourusername identified by 'yourpasswd';
  Query OK, 0 rows affected (0.01 sec)
  mysql> flush privileges;
  Query OK, 0 rows affected (0.01 sec)
  
With the previous steps you've created a database called *yourdbname* and authorized user *yourusername* with password *yourpasswd* to use it.


 8. Now you need to install the required python packages for DIRAC to be able to run. There are two ways of doing that:

   8.1 If you want to use your own python (you can use versions 2.6 or 2.7, but it is highly suggested to use python 2.7) you can install all the required packages by hand. 
       First, you'll need to install few packages for your distrubution, e.g. you will need gcc, python-devel, openssl-devel, mysql, mysql-devel, python-pip.
       
       Then, you can use pip to install specifc python tools::

         pip install GSI
         pip install MySQL-python
         pip install mock


       Now, remembers to update the $PYTHONPATH with the directory where you put your DIRAC code (and the code of possible extensions).  

   8.2 The second possibility is to use the same script that is used for the server installations. 
       This is needed if you don't have python 2.6 ot 2.7 available for your system or you just want to get the DIRAC External binaries for you platform::
              scripts/dirac-install -X -t server -i 26
  
      This may take a while if there aren't externals available for your platform and they have to be compiled. In any case, we suggest to try with the first alternative  .

  
 9. Last step is to to configure DIRAC. There are 2 ways to do that: the first, and suggested way, is to work in isolation. 

   At this point, the key becomes understanding how the DIRAC `Configuration Service (CS) <http://diracgrid.org/files/docs/AdministratorGuide/Configuration/ConfigurationStructure/index.html>`_ works. I'll explain here briefly. The CS is a layered structure: whenever you access a CS information (e.g. using a "gConfig" object, see later), DIRAC will first check into your local "dirac.cfg" file (it can be in your home as .dirac.cfg, or in etc/ directory, see the link above). If this will not be found, it will look for such info in the CS servers available. 

   When you develop locally, you don't need to access any CS server: instead, you need to have total control. So, you need to work a bit on the local dirac.cfg file. There is not much else needed, just create your own etc/dirac.cfg. The example that follows might not be easy to understand at a first sight, but it will become easy soon. The syntax is extremely simple, yet verbose: simply, only brackets and equalities are used. 

   9.1 If you want to create an isolated installation just creaate a *etc/dirac.cfg* file with (create the etc directory first)::

      DIRAC
      {
        Setup = DeveloperSetup
        Setups
        {
          DeveloperSetup
          {
      #      Framework
      #      {
      #      }
          }
        }
      }
      Systems
      {
      #  SystemName
      #  {
      #    Framework
      #    URLs
      #    {
      #    }
      #    Services
      #    {
      #    }
      #  }
      }
      Registry
      {
      #  Users
      #  {
      #    userName
      #    {
      #      DN = 
      #      CA = 
      #      Email = 
      #    }
      #  }
      #  Groups
      #  {
      #    devGroup
      #    {
      #      Users = 
      #    }
      #  }
      }

   9.2 The second possibility (ALTERNATIVE to the previous one, and not suggested) is to issue the following script::

         scripts/dirac-configure -S setupyouwanttorun -C configurationserverslist -n sitename -H

      
      This is a standard script, widely used for non-developer installations, that will connect to an already existing installation when the configurationserverslist is given


 10. Now, it's time to deal with certificates. You will need 2 types of certificates: your own certificate, and a machine certificate. We will create self-signed certificates, but before, create a ``~/.globus`` directory. Inside there, generate a self-signed certificate with the command::
    
       openssl req -x509 -newkey rsa:2048 -keyout userkey.pem -out usercert.pem -days 365
      
    For what regards the machine certificate (that are necessary to run services and agents)
    
    INSERT_HERE
      
 11. As a reminder, from now on, every time you want to publish something to your public repository do::

       git push origin localbranch:remotebranch

     if you want to push a new branch

     or just::

       git push origin

     for an already pushed branch

 12. To bring changes from the release repository do::

       git fetch release
       git rebase release/integration

You're ready for DIRAC development !


