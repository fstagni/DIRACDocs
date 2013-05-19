======================================
Check your installation
======================================


Is my installation correctly done?
--------------------------------------

We will now do few, very simple checks. The first can be done by using the python interactive shell. For these examples I will actually use `iPython <http://ipython.org/>`_, which is a highly recommended shell.

.. code-block:: python

   In [1]: from DIRAC.Core.Base.Script import parseCommandLine
   
   In [2]: parseCommandLine()
   Out[2]: True

Was this good? If it wasn't, then you should probably hit the "previous" button of this guide.

So, what's that about? These 2 lines will initialize DIRAC. They are used in several places, especially for the scripts: each and every script in DIRAC start with those 2 lines above.

Let's do one more check:

.. code-block:: python

   In [14]: from DIRAC import gConfig

   In [15]: gConfig.getValue('/DIRAC/Setup')
   Out[15]: 'DeveloperSetup'

Was this good? If it wasn't, again, then you should probably hit the "previous" button of this guide.

Let's not think about you just typed right now. It will become more clear later.


The real basic stuff
--------------------

Let's start with the **logger**

.. code-block:: python

   In [3]: from DIRAC import gLogger

   In [4]: gLogger.notice('Hello world')
   Hello world 
   Out[4]: True

What's that? It is a `singleton <http://en.wikipedia.org/wiki/Singleton_pattern>`_ object for logging in DIRAC. Needless to say, you'll use it a lot.

.. code-block:: python

   In [5]: gLogger.info('Hello world')
   Out[5]: True

Why "Hello world" was not printed? Because the logging level is too high:

.. code-block:: python

   In [6]: gLogger.getLevel()
   Out[6]: 'NOTICE'

But we can increase it simply doing, for example:

.. code-block:: python

   In [7]: gLogger.setLevel('VERBOSE')
   Out[7]: True
    
   In [8]: gLogger.info('Hello world')
   Hello world 
   Out[8]: True

In DIRAC, you should not use print. Use the gLogger instead.


Let's continue, and we have a look at the **return codes**:

.. code-block:: python

   In [11]: from DIRAC import S_OK, S_ERROR

These 2 are the basic return codes that you should use. How do they work?

.. code-block:: python

   In [12]: S_OK('All is good')
   Out[12]: {'OK': True, 'Value': 'All is good'}

   In [13]: S_ERROR('Damn it')
   Out[13]: {'Message': 'Damn it', 'OK': False}

Quite clear, isn't it? Often, you'll end up doing a lot of code like that:

.. code-block:: python

   result = aDIRACMethod()
   if not result['OK']:
       gLogger.error('aDIRACMethod-Fail', "Call to aDIRACMethod() failed with message %s" %result['Message'])
       return result
   else:
       returnedValue = result['Value']



Playing with the Configuration Service
--------------------------------------

If you are here, it means that your developer installation contains a **dirac.cfg** file, that should stay in the $DIRACINSTALLATION/etc directory. 
We'll play a bit with it now.

You have already done this:

   In [14]: from DIRAC import gConfig

   In [15]: gConfig.getValue('/DIRAC/Setup')
   Out[15]: 'DeveloperSetup'

Where does 'DeveloperSetup' come from? Open that dirac.cfg and search for it. Got it? it's in 

.. code-block:: python
   
   DIRAC
   {
     ...
     Setup = DeveloperSetup
     ...
   }

Easy, huh? Try to get something else now, still using gConfig.getValue().

So, gConfig is another singleton: it is the guy you need to call for basic interactions with the `Configuration Service <needAReference>`_. 
If you are here, we assume you already know about the CS servers and layers. More information can be found in the Administration guide.
We remind that, for a developer installation, we will work in ISOLATION, so with only the local dirac.cfg

Mostly, gConfig exposes get type of methods:

.. code-block:: python
   
   In [2]: gConfig.get
   gConfig.getOption       gConfig.getOptionsDict  gConfig.getServersList  
   gConfig.getOptions      gConfig.getSections     gConfig.getValue        

for example, try:

.. code-block:: python
   
   In [2]: gConfig.getOptionsDict('/DIRAC')

In the next section we will modify a bit the dirac.cfg file. Before doing that, have a look at it. 
It's important what's in there, but for the developer installation it is also important what it is NOT there. We said we will work in isolation.
So, it's important that this file does not contain any URL to server infrastructure (at least, not at this level: later, when you will feel more confortable, you can add some).

A very important option of the cfg file is "DIRAC/Configuration/Server": this option can contain the URL(s) of the running Configuration Server.
But, as said, for doing development, this option should stay empty.


Getting a Proxy
---------------------

We assume that you have already your public and private certificates key in $HOME/.globus. 
Then, do the following::

   dirac-proxy-init

You probably got something like::

   toffo@pclhcb181:~/LHCbCode/DIRAC$ dirac-proxy-init 
   Generating proxy... 
   Enter Certificate password:
   DN /DC=ch/DC=cern/OU=Organic Units/OU=Users/CN=fstagni/CN=693025/CN=Federico Stagni is not registered 

This is because DIRAC still doesn't know you exist. You should add yourself to the CS. For example, I had add the following section::

   Registry
   {
     Users
     {
       fstagni
       {
         DN = /DC=ch/DC=cern/OU=Organic Units/OU=Users/CN=fstagni/CN=693025/CN=Federico Stagni
         CA = /DC=ch/DC=cern/CN=CERN Trusted Certification Authority
         Email = federico.stagni@cern.ch
       }
     }
     

All the info you want and much more in::

   openssl x509 -in usercert.pem -text


Now, it's time to issue again::

   toffo@pclhcb181:~/.globus$ dirac-proxy-init 
   Generating proxy... 
   Enter Certificate password:
   User fstagni has no groups defined 
   
So, let's add the groups within the /Registry section::

       Groups
       {
         devGroup
         {
           Users = fstagni 
         }
       }

You can keep playing with it (e.g. adding some properties), but for the moment this is enough.


Exercise
--------

Code a python module in DIRAC.Core.Utilities where there is only the following function:

.. code-block:: python

   def checkCAOfUser( user, CA ):
     """ user, and CA are string
     """

This function should:
* Get from the CS the registered Certification Authority for the user
* if the CA is the expected one return S_OK, else return S_ERROR

Code a python script that:
* call such function
* log wih info or error mode depending on the result

Remember to start the script with:

.. code-block:: python

   #!/usr/bin/env python
   """ Some doc: what does this script should do?
   """
   from DIRAC.Core.Base import Script
   Script.parseCommandLine()

