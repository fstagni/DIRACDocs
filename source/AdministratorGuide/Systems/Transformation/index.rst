=====================
Transformation System
=====================

.. toctree::
   :maxdepth: 1

.. contents:: Table of contents
   :depth: 4
  

The Transformation System (TS) is used to automatise common tasks related to the production activity. As an example, the TS can handle the generation of Data Re-processing jobs as soon as a 'pre-defined' data-set is available or Data Replication to 'pre-defined' SE destinations as soon as the first replica is registered in the Catalog.

For each high-level production task, the production manager has to create a specific Transformation. The main features of a Transformation are the following:

- Type (*e.g.* MC Simulation, Data Reprocessing, Removal, Replication)
- Plugin (Standard, etc.)
- It can have Input Files or not

The main features of the TS are:

- Generation of many identical tasks, differing by one parameter (it can be the Input File or not)
- Possibility to easily extend the number of tasks 
- One single high-level object (the Transformation) associated to a given production for global monitoring

Disadvantages:

- The submission is slow, since there is no use of Parametric jobs
- It's not possible to use ISB to ship local files as for 'normal' Jobs

------------
Architecture
------------

The TS is composed by Services, DB, Agents and Clients (in the following we refer to the current installed DIRAC version, v6r10p20).

* **Services**

  * TransformationManagerHandler:
    DISET request handler base class for the TransformationDB

* **DB**

  * TransformationDB:
    it's used to collect and serve the necessary information in order to automate the task of job preparation for high level  transformations. This class is typically used as a base class for more specific data processing databases. Here below the DB tables:

  ::

      mysql> use TransformationDB;
      Database changed
      mysql> show tables;
      +------------------------------+
      | Tables_in_TransformationDB   |
      +------------------------------+
      | AdditionalParameters         |
      | DataFiles                    |
      | Replicas                     |
      | TaskInputs                   |
      | TransformationFileTasks      |
      | TransformationFiles          |
      | TransformationInputDataQuery |
      | TransformationLog            |
      | TransformationTasks          |
      | Transformations              |
      +------------------------------+


  **Note** that since version v6r10, there are important changes in the TransformatioDB, as explained in the `release notes <https://github.com/DIRACGrid/DIRAC/wiki/DIRAC-v6r10#transformationdb>`_ (for example the Replicas table can be removed). Also, it is highly suggested to move to InnoDB. 

* **Agents**

  * TransformationAgent: it processes transformations found in the TransformationDB and creates the associated tasks, by connecting input files with tasks given a plugin. It's not useful for MCSimulation type

  * WorkflowTaskAgent: it takes workflow tasks created in the TransformationDB and it submits to the WMS

  * MCExtensionAgent: it extends the number of tasks given the Transformation definition. To work it needs to know how many events each production will need, and how many events each job will produce. It is only used for 'MCSimulation' type

  * TransformationCleaningAgent: it cleans up the finalised Transformations

  * RequestTaskAgent: it takes request tasks created in the TransformationDB and submits to the RMS

  * InputDataAgent: it updates the transformation files of active Transformations given an InputDataQuery fetched from the Transformation Service

  * ValidateOutputDataAgent: it runs few integrity checks

The complete list can be found in the `DIRAC project GitHub repository <https://github.com/DIRACGrid/DIRAC/tree/integration/TransformationSystem/Agent>`_ (ADD a schema).

* **Clients**

  * TaskManager: it contains WorkflowsTasks and RequestTasks modules, for managing jobs and requests tasks, i.e. it contains classes wrapping the logic of how to 'transform' a Task in a job/request

  * TransformationClient: class that contains client access to the transformation DB handler (main client to the service/DB). It exposes the functionalities available in the DIRAC/TransformationHandler. This inherits the DIRAC base Client for direct execution of server functionality

  * Transformation: it wraps some functionalities mostly to use the 'TransformationClient' client

-------------
Configuration
-------------

* **Operations**

  * In the Operations/[VO]/Transformations section, *Transformation Types* must be added
  * By default, the WorkflowTaskAgent will treat all the *DataProcessing* transformations and the RequestTaskAgent all the *DataManipulation* ones
  * An example of working configuration is give below::

        Transformations
        {
          DataProcessing = MCSimulation
          DataProcessing += CorsikaRepro
          DataProcessing += Merge
          DataProcessing += Analysis
          DataProcessing += DataReprocessing
          DataManipulation = Removal
          DataManipulation += Replication
        }

* **Agents** 

  * Agents must be configured in the Systems/Transformation/[VO]/Agents section
  * The *Transformation Types* to be treated by the agent must be configured if and only if they are different from those set in the 'Operations' section. This is useful, for example, in case one wants several agents treating different transformation types, *e.g.*: one WorkflowTaskAgent for DataReprocessing transformations, a second for Merge and MCStripping, etc. Advantage is speedup.
  * For the WorkflowTaskAgent and RequestTaskAgent some options must be added manually
  * An example of working configuration is give below

  ::

        WorkflowTaskAgent
        {
          #Transformation types to be treated by the agent
          TransType = MCSimulation
          TransType += DataReconstruction
          TransType += DataStripping
          TransType += MCStripping
          TransType += Merge
          TransType += DataReprocessing
          #Task statuses considered transient that should be monitored for updates
          TaskUpdateStatus = Submitted
          TaskUpdateStatus += Received
          TaskUpdateStatus += Waiting
          TaskUpdateStatus += Running
          TaskUpdateStatus += Matched
          TaskUpdateStatus += Completed
          TaskUpdateStatus += Failed
          #Flag to eanble task submission
          SubmitTasks = yes
          #Flag for checking reserved tasks that failed submission
          CheckReserved = yes
          #Flag to enable task monitoring
          MonitorTasks = yes
          PollingTime = 120
          MonitorFiles = yes
        }
        RequestTaskAgent
        {
          PollingTime = 120
          SubmitTasks = yes
          CheckReserved = yes
          MonitorTasks = yes
          MonitorFiles = yes
          TaskUpdateStatus = Submitted
          TaskUpdateStatus += Received
          TaskUpdateStatus += Waiting
          TaskUpdateStatus += Running
          TaskUpdateStatus += Matched
          TaskUpdateStatus += Completed
          TaskUpdateStatus += Failed
          TransType = Removal
          TransType += Replication
        }

---------
Use-cases
---------

-------------
MC Simulation 
-------------

Generation of many identical jobs which don't need Input Files and having as varying parameter a variable built from @{JOB_ID}.

* **Agents**

  ::

    WorkflowTaskAgent, MCExtensionAgent (optional)

The WorkflowTaskAgent uses the TaskManager client to transform a 'Task' into a 'Job'.

* Example:

  ::

    from DIRAC.TransformationSystem.Client.Transformation import Transformation
    from DIRAC.Interfaces.API.Job import Job
    j = myJob()
    ...
    t = Transformation( )
    t.setTransformationName("MCProd") # This must be unique
    t.setTransformationGroup("Group1")
    t.setType("MCSimulation")
    t.setDescription("MC prod example")
    t.setLongDescription( "This is the long description of my production" ) #mandatory
    t.setBody ( j.workflow.toXML() )
    t.addTransformation() #transformation is created here
    t.setStatus("Active")
    t.setAgentType("Automatic")

-------------
Re-processing
-------------

Generation of identical jobs with Input Files.

* **Agents**

  ::

    TransformationAgent, WorkflowTaskAgent, InputDataAgent (used for DFC query)

* Example with Input Files list

  ::

    from DIRAC.TransformationSystem.Client.Transformation import Transformation
    from DIRAC.TransformationSystem.Client.TransformationClient import TransformationClient
    from DIRAC.Interfaces.API.Job import Job
    j = myJob()
    ...
    t = Transformation( )
    tc = TransformationClient( )
    t.setTransformationName("Reprocessing_1") # This must be unique
    t.setType("DataReprocessing")
    t.setDescription("repro example")
    t.setLongDescription( "This is the long description of my reprocessing" ) #mandatory
    t.setBody ( j.workflow.toXML() )
    t.addTransformation() #transformation is created here
    t.setStatus("Active")
    t.setAgentType("Automatic")
    transID = t.getTransformationID()
    tc.addFilesToTransformation(transID['Value'],infileList) # Files are added here


* Example with Input Files as a result of a DFC query. 
  Just replace the above example with a DFC query (example taken from CTA):

  ::

    tc.createTransformationInputDataQuery(transID['Value'], {'particle': 'proton','prodName':'ConfigtestCorsika','outputType':'corsikaData'}) 

**Note:**

  * *Transformation Type* = 'DataReprocessing'
  * If the 'MonitorFiles' option is enabled in the agent configuration, failed jobs are automatically rescheduled

-------------------------------
Data management transformations
-------------------------------

Generation of bulk data removal/replication requests from a fixed file list or as a result of a DFC query

* **Agents**

  ::

    TransformationAgent, RequestTaskAgent, InputDataAgent (for DFC query)

  Requests are then treated by the RMS (see `RequestManagement <http://diracgrid.org/files/docs/AdministratorGuide/Systems/RequestManagement/rms.html>`_):

  * Check the logs of RequestExecutingAgent, *e.g.*:

    ::

      2014-07-08 08:27:33 UTC RequestManagement/RequestExecutingAgent/00000188_00000001   INFO: request '00000188_00000001' is done 

  * Query the ReqDB to check the requests

* Example of data removal

  ::

    from DIRAC.TransformationSystem.Client.Transformation import Transformation
    from DIRAC.TransformationSystem.Client.TransformationClient import TransformationClient

    infileList = []
    ...

    t = Transformation( )
    tc = TransformationClient( )
    t.setTransformationName("DM_Removal") # Must be unique 
    #t.setTransformationGroup("Group1")
    t.setType("Removal")
    t.setPlugin("Standard") # Not needed. The default is 'Standard'
    t.setDescription("dataset1 Removal")
    t.setLongDescription( "Long description of dataset1 Removal" ) # Mandatory
    t.setGroupSize(2) # Here you specify how many files should be grouped within the same request, e.g. 100 
    t.setBody ( "Removal;RemoveFile" ) # Mandatory (the default is a ReplicateAndRegister operation)
    t.addTransformation() # Transformation is created here
    t.setStatus("Active")
    t.setAgentType("Automatic")
    transID = t.getTransformationID()
    tc.addFilesToTransformation(transID['Value'],infileList) # Files are added here

**Note:**

* It's not needed to set a Plugin, the default is 'Standard'
* It's mandatory to set the Body, otherwise the default operation is 'ReplicateAndRegister'
* It's not needed to set a SourceSE nor a TargetSE
* This script remove all replicas of each file. We should verify how to remove only a subset of replicas (SourceSE?)
* If you add non existing files to a Transformation, you won't get any particular status, the Transformation just does not progress

---------------------------------------
Data replication based on Catalog Query
---------------------------------------

* Example of data replication (file list as a result of a DFC query, example taken from CTA)

  ::

    from DIRAC.TransformationSystem.Client.Transformation import Transformation
    from DIRAC.TransformationSystem.Client.TransformationClient import TransformationClient
    t = Transformation( )
    tc = TransformationClient( )
    t.setTransformationName("DM_ReplicationByQuery1") # This must vary 
    #t.setTransformationGroup("Group1")
    t.setType("Replication") 
    t.setSourceSE(['CYF-STORM-Disk','DESY-ZN-Disk']) # A list of SE where at least 1 SE is the valid one
    t.setTargetSE(['CEA-Disk'])
    t.setDescription("data Replication")
    t.setLongDescription( "data Replication" ) #mandatory
    t.setGroupSize(1)
    t.setPlugin("Broadcast")
    t.addTransformation() #transformation is created here
    t.setStatus("Active")
    t.setAgentType("Automatic")
    transID = t.getTransformationID()
    tc.createTransformationInputDataQuery(transID['Value'], {'particle': 'gamma','prodName':'Config_test300113','outputType':'Data','simtelArrayProdVersion':'prod-2_21122012_simtel','runNumSeries':'0'}) # Add files to Transformation based on Catalog Query

--------------------------
Actions on transformations
--------------------------

* **Start**
* **Stop**
* **Flush:** It has a meaning only depending on the plugin used, for example the 'BySize' plugin, used *e.g.* for merging productions, creates a task if there are enough files in input to have at least a certain size: 'flush' will make the 'BySize' plugin to ignore such requirement
* **Complete:** The transformation can be archived by the TransformationCleaningAgent. Archived means that the data produced stay, but not the entries in the TransformationDB
* **Clean:** The transformation is cleaned by the TransformationCleaningAgent: jobs are killed and removed from WMS; produced and stored files are removed from the Storage Elements











