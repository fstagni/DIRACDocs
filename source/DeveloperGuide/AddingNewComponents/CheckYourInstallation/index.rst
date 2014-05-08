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


Getting a certificate
---------------------

Now, it's time to issue:

    dirac-proxy-init

That should print something like:

    Generating proxy... 
    Enter Certificate password:
    Uploading proxy for lhcb_user... 
    Uploading proxy for private_pilot... 
    Proxy generated: 
    subject      : /DC=ch/DC=cern/OU=Organic Units/OU=Users/CN=fstagni/CN=693025/CN=Federico Stagni/CN=proxy
    issuer       : /DC=ch/DC=cern/OU=Organic Units/OU=Users/CN=fstagni/CN=693025/CN=Federico Stagni
    identity     : /DC=ch/DC=cern/OU=Organic Units/OU=Users/CN=fstagni/CN=693025/CN=Federico Stagni
    timeleft     : 23:59:58
    DIRAC group  : lhcb_user
    path         : /tmp/x509up_u1000
    username     : fstagni
    properties   : NormalUser 

    Proxies uploaded: 
      DN                                                                               | Group         | Until (GMT) 
     /DC=ch/DC=cern/OU=Organic Units/OU=Users/CN=fstagni/CN=693025/CN=Federico Stagni | lhcb_prmgr    | 2014/07/03 10:46 
     /DC=ch/DC=cern/OU=Organic Units/OU=Users/CN=fstagni/CN=693025/CN=Federico Stagni | lhcb_user     | 2014/07/03 10:46 
     /DC=ch/DC=cern/OU=Organic Units/OU=Users/CN=fstagni/CN=693025/CN=Federico Stagni | private_pilot | 2014/07/03 10:46 
     /DC=ch/DC=cern/OU=Organic Units/OU=Users/CN=fstagni/CN=693025/CN=Federico Stagni | lhcb_pilot    | 2014/07/03 10:46 

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

