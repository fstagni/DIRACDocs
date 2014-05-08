======================================
Check your installation
======================================

Is my installation correctly done?
--------------------------------------

We will now do few, very simple checks. The first can be done by using the python interactive shell. For these examples I will actually use `iPython <http://ipython.org/>`_, which is a highly recommended shell.

    In [1]: from DIRAC.Core.Base.Script import parseCommandLine
    
    In [2]: parseCommandLine()
    Out[2]: True

Was this good? If it wasn't, then you should probably hit the "previous" button of this guide.

So, what's that about? These 2 lines will initialize DIRAC. They are used in several places, especially for the scripts: each and every script in DIRAC start with those 2 lines above.


The real basic stuff
--------------------

Let's start with the **logger**

    In [3]: from DIRAC import gLogger

    In [4]: gLogger.notice('Hello world')
    Hello world 
    Out[4]: True

You'll use gLogger a lot. What's that? It is a `singleton <http://en.wikipedia.org/wiki/Singleton_pattern>`_ object for logging in DIRAC. Needless to say, you'll use it a lot.

    In [5]: gLogger.info('Hello world')
    Out[5]: True

Why "Hello world" was not printed? Because the logging level is too high:

    In [6]: gLogger.getLevel()
    Out[6]: 'NOTICE'

But we can increase it simply doing, for example:

    In [7]: gLogger.setLevel('VERBOSE')
    Out[7]: True
    
    In [8]: gLogger.info('Hello world')
    Hello world 
    Out[8]: True

In DIRAC, you should not use print. Use the gLogger instead.






Playing with the Configuration Service
--------------------------------------


