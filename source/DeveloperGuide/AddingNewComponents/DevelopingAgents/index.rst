======================================
Developing Agents
======================================

Simplest Agent
-------------------

All the DIRAC Agents are built in the same framework where developers should provide
an Agent by inheriting the base AgentModule class. 

Creating an Agent is best illustrated by the example below which is presenting a fully 
functional although simplest possible agent:: 
    
   """ :mod: SimplestAgent
   
       Simplest Agent send a simple log message
   """
    
   # # imports
   from DIRAC import S_OK, S_ERROR
   from DIRAC.Core.Base.AgentModule import AgentModule
   
   
   __RCSID__ = "Id: $"
   
   class SimplestAgent( AgentModule ):
     """
     .. class:: SimplestAgent
   
     Simplest agent
     print a message on log
     """
   
     def initialize( self ):
       """ agent's initalisation
   
       :param self: self reference
       """
   
       self.message = self.am_getOption( 'Message', "SimplestAgent is working..." )
       self.log.info( "message = %s" % self.message )
   
       return S_OK()
   
     def execute( self ):
       """ execution in one agent's cycle
   
       :param self: self reference
       """
   
       self.log.info( "message: %s" % self.message )
   
       return S_OK()   

Let us walk through this code to see which elements should be provided.

First comes the documentation string describing the service purpose and behavior. It is
followed by the ''__RCSID__'' global module variable which we have already seen in the services part.

Several import statements will be clear from the subsequent code.

The Agent name is SimplestAgent. The ''initialize'' method is
called once when the Agent is created. Here one can put creation and initialization
of the global variables if necessary. **Please not that the __init__ method cannot be used 
when developing an Agent. It is used to intialize the module before it can be used**

One can define initialize() method with no arguments although this is not necessary.
The details of the caller can be obtained using the "getRemoteCredentials()" method
of the base RequestHandler class.
The other useful method is getCSOption() which allows to extract options from the Service
section in the Configuration Service.

Now comes the definition of the '''execute''' method.
This method is executed evry time Agent runs. Place your code inside this method.
Other methods can be defined in the same file and used via '''execute''' method

The result must always be returned as an S_OK or S_ERROR structure.

Default Agent Configuration parameters
------------------------------------------

The Agent is written. It should be placed to the Agent directory of one
of the DIRAC System directories in the code repository, for example FrameworkSystem. 
The default Service Configuration parameters should be added to the corresponding 
System ConfigTemplate.cfg file. In our case the Service section in the ConfigTemplate.cfg 
will look like the following::

  Agents
  {
    SimplestAgent
    {
      LogLevel = INFO
      LogBackends = stdout
      PollingTime = 60
      Message = still working...
    }
  }  
  
Polling time define execution time scheduling.
The Message is this agent specific option.


Installing the Agent
------------------------

Once the Agent is ready it should be installed. The DIRAC Server installation is described
in documentation. If you are adding the Agent to an already existing installation it is
sufficient to execute the following in this DIRAC instance::

  > dirac-install-agent Framework SimplestAgent
  
This command will do several things:

  * It will create the SimpleAgent Agent directory in the standard place and will set 
    it up under the ''runit'' control - the standard DIRAC way of running permanent processes. 
  * The SimplestAgent Agent section will be added to the Configuration System.
    
The Agent can be also installed using the SystemAdministrator CLI interface::

  > install agent Framework SimplestAgent
  
The SystemAdministrator interface can also be used to remotely control the Agent, start or
stop it, uninstall, get the Agent status, etc.       

Checking the Agent output from log messages
------------------------------------------------

Login to dirac-admin-sysadmin-cli as administrator.
Show log of SimplestAgent::

  > show log Framework SimplestAgent
      
An info message will appear in log::

  Framewrok/SimplestAgent  INFO: message: still working...

Note that the service is always returning the result in the form of S_OK/S_ERROR structure.        
 
