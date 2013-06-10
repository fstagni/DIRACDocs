-----------------------------
New Request Management System
-----------------------------

:author:  Krzysztof Daniel Ciba <Krzysztof.Ciba@NOSPAMgmail.com>
:date:    Fri, 28th May 2013
:version: first


System Overview
---------------

The Request Management System (RMS) is designed for management of simple operations that are performed 
asynchronously on behalf of users - owners of the requests. The RMS is used for multiple purposes: failure 
recovery (failover system), data management tasks and some others. It is designed as an open system easily 
extendible for new types of operations.  

Architecture and functionality
------------------------------

The core of the RequestManagementSystem is a ReqDB database which holds requests records togethers with 
all related data: operations that have to be performed in the defined order and possibly a set of files
attached. All avaiable and useful queries to the ReqDB are exposed to the request client (ReqClient) 
by ReqManager service.

.. image:: ../../../_static/Systems/RMS/ReqDBSchema.png
   :alt: ReqDB schema.
   :align: center 

Each table in the ReqDB has a corresponding class in the new API with full CRUD support, where each table field 
is exposed as a property in the related class. 

.. image:: ../../../_static/Systems/RMS/RequestZoo.png
   :alt: Request, Operation and File API.
   :align: center 

The record class is instrumented with the internal observer for 
its children (a Request instance is observing states for all defined Operations, each Operation is observing 
states of Files) and built in state machine, which automatizes state propagation:

 * state machine for Request

   .. image:: ../../../_static/Systems/RMS/RequestSTM.png
      :alt: State machine for Request.
      :align: center 

 * state machine for Operation

   .. image:: ../../../_static/Systems/RMS/OperationSTM.png
      :alt: State machine for operation.
      :align: center 

 * state machine for File

   .. image:: ../../../_static/Systems/RMS/FileSTM.png
      :alt: State machine for File.
      :align: center 

User is allowed to change only File statuses and in case of specific Operation's types - Operation statuses, as Request 
builtin observer will propagate and set correct status on the higher level.

Request construction and validation
-----------------------------------

Construction of a new request is quite simple, one has to create a new Request instance::

  >>> from DIRAC.RequestManagementSystem.Client.Request import Request
  >>> from DIRAC.RequestManagementSystem.Client.Operation import Operation
  >>> from DIRAC.RequestManagementSystem.Client.File import File
  >>> request = Request() # # create Request instance
  >>> request.RequestName = "foobarbaz"
  >>> operation = Operation() # # create new operation 
  >>> operation.Type = "ReplicateAndRegister"
  >>> operation.TargetSE = [ "CERN-USER", "PIC-USER" ]
  >>> opFile = File() # #  create File instance
  >>> opFile.LFN = "/foo/bar/baz" # # and fill some bits 
  >>> opFile.Checksum = "123456"
  >>> opFile.ChecksumType = "adler32"
  >>> operation.addFile( opFile ) # # add File to Operation
  >>> request.addOperation( operation ) # # add Operation to Request

Using Request.addOperation method will enqueue operation to the end of operatins list in the request. If you need 
to modify execution order, you can use Request.insertBefore() or Request.insertAfter() methods. 
Please notice there is no limit of Operations per Request, but it is not recommended to keep over tehre  
more than a few. In case of number of Files in a single Operation the limit is set to one hundred.     
 
Request and Operation classes are behaving as any iterable python object, i.e. you can loop over operations 
in the request using::

  >>> for op in request: print op.Type
  ReplicateAndRegister
  >>> for opFile in operation: print opFile.LFN, opFile.Status, opFile.Checksum
  /foo/bar/baz Waiting 123456 
  >>> len( request ) # # number of operations 
  1
  >>> len( operation ) # # number of files in operation   
  1
  >>> request[0].Type # # __getitem__, there is also __setitem__ and __delitem__ defined for Request and Operation
  'ReplicateAndRegister'
  >>> operation in request # # __contains__ in Request
  True
  >>> opFile in operation # # __contains__ in Operation
  True


Once the request is ready, you can insert it to the ReqDB::

  >>> from DIRAC.RequestManagementSystem.Client.ReqClient import ReqClient
  >>> rc = ReqClient() # # create client
  >>> rc.putRequest( request ) # # put request to ReqDB

Reading request back can be done using two methods:

  * for reading::

  >>> from DIRAC.RequestManagementSystem.Client.ReqClient import ReqClient
  >>> rc = ReqClient() # # create client
  >>> rc.peekRequest( "foobarbaz" ) # # get request from ReqDB for reading

  * for execution (request status on DB side will flip to 'Assigned')::

  >>> from DIRAC.RequestManagementSystem.Client.ReqClient import ReqClient
  >>> rc = ReqClient() # # create client
  >>> rc.getRequest( "foobarbaz" ) # # get request from ReqDB for execution


If you don't specify request name in RequestClient.getRequest or RequestClient.peekRequest, the one with "Waiting" 
status  and the oldest Request.LastUpdate value will be chosen. 


Request validation
------------------

The validation of a new Request that is about to enter the system for execution is checked at two levels:

  * low-level: each property in Request, Operation and File classes is instrumeted to check if value provided 
    to its setter has a meaningful type and value::

  >>> opFile.LFN = 1
  Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "DIRAC/RequestManagementSystem/private/Record.py", line 52, in __setattr__
    object.__setattr__( self, name, value )
  File "DIRAC/RequestManagementSystem/Client/File.py", line 137, in LFN
    raise TypeError( "LFN has to be a string!" )
  TypeError: LFN has to be a string!
  >>> operation.SubmitTime = False
  Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "DIRAC/RequestManagementSystem/private/Record.py", line 52, in __setattr__
    object.__setattr__( self, name, value )
  File "DIRAC/RequestManagementSystem/Client/Operation.py", line 370, in SubmitTime
    raise TypeError( "SubmitTime should be a datetime.datetime!" )
  TypeError: SubmitTime should be a datetime.datetime!

  * high-level: additionally there is also a request validator helper class - a gatekeeper checking if request 
    is properly defined. The validator is blocking insertion of a new record to the ReqDB in case of missing or 
    malformed attrubutes and returning S_ERROR describing the reason for rejection, i.e.::

  >>> from DIRAC.RequestManagementSystem.private.RequestValidator import gRequestValidator
  >>> from DIRAC.RequestManagementSystem.Client.Request import Request
  >>> invalid = Request()
  >>> gRequestValidator.validate( invalid )
  {'Message': 'RequestName not set', 'OK': False}
  >>> invalid.RequestName = "foobarbaz"
  >>> gRequestValidator.validate( invalid )
  {'Message': "Operations not present in request 'foobarbaz'", 'OK': False}
  >>> from DIRAC.RequestManagementSystem.Client.Operation import Operation
  >>> invalid.addOperation( Operation() )
  {'OK': True, 'Value': ''}
  >>> gRequestValidator.validate( invalid )
  {'Message': "Operation #0 in request 'foobarbaz' hasn't got Type set", 'OK': False}
  >>> invalid[0].Type = "ForwardDISET"
  >>> gRequestValidator.validate( invalid )
  {'Message': "Operation #0 of type 'ForwardDISET' is missing Arguments attribute.", 'OK': False}






Request execution
-----------------

Execution of the all possible requests is done in only one agent: RequestExecutingAgent using special set 
of handlers derived from OperationHandlerBase helper class. What is different from the previos attempt is 
the way the request is treated: the agent will try to execute request as a whole in one go, while previously 
there was several different agents in place, each trying to execute one sub-request type. This approach was
a horrible complication for maintain request's state machine.

The agent is using ProcessPool utility to create slave subprocesses for execution. Each pool worker is:
 
  * downloads and sets up request owner proxy
  * loop over defined and waiting operations
  * creates on-demand and executes specific operation handler instance
  * if operation status is not updated after treatment inside handler, worker jumps out the loop 
    otherwise tries to pick up next waiting Operation 
    
Outside the main execution loop worker is checking request status and depending of its value finlizes request 
and putting it back to the ReqDB.

Extending
---------




Installation
------------

1. Login to host, install ReqDB::

  dirac-install-db ReqDB

2. Install ReqManagerHandler service::

  dirac-install-service RequestManagement/ReqManager

3. Install at least one ReqProxy service::

  dirac-install-service RequestManagement/ReqProxy

and modify CS by adding:

  Systems/RequestManagement/<Configuration>/URLs/ReqProxyURLs = <ReqProxy FQDN1>, <ReqProxy FQDN1>   

4. Install CleanReqDBAgent::

  dirac-install-agent RequestManagement/CleanReqDBAgent

5. Install RequestExecutingAgent::

  dirac-install-agent RequestManagement/RequestExecutingAgent

If one RequestExecutingAgent is not enough (and this is a working horse replacing DISETForwadingAgent, TransferAgent, RemovalAgent and RegistrationAgent),
put in place a few of those.


1. If VO is using FTS system, install FTSDB::

  dirac-install-db FTSDB

2. Stop DataManagement/TransferDBMonitor service and install FTSManagerHandler::

  runsvctrl d runit/DataManagement/TransferDBMonitor
  dirac-install-service DataManagement/FTSManager

3. Configure FTS sites using command dirac-dms-add-ftssite (not included in v6r9-pre1!!!)::

  dirac-dms-add-ftssite SITENAME FTSSERVERURL

In case of LHCb VO::

  dirac-admin-add-ftssite CERN.ch https://fts22-t0-export.cern.ch:8443/glite-data-transfer-fts/services/FileTransfer 50
  dirac-admin-add-ftssite CNAF.it https://fts.cr.cnaf.infn.it:8443/glite-data-transfer-fts/services/FileTransfer 50
  dirac-admin-add-ftssite PIC.es https://fts.pic.es:8443/glite-data-transfer-fts/services/FileTransfer 50
  dirac-admin-add-ftssite RAL.uk https://lcgfts.gridpp.rl.ac.uk:8443/glite-data-transfer-fts/services/FileTransfer 50
  dirac-admin-add-ftssite SARA.nl https://fts.grid.sara.nl:8443/glite-data-transfer-fts/services/FileTransfer 50
  dirac-admin-add-ftssite NIKHEF.nl https://fts.grid.sara.nl:8443/glite-data-transfer-fts/services/FileTransfer 50
  dirac-admin-add-ftssite GRIDKA.de https://fts-fzk.gridka.de:8443/glite-data-transfer-fts/services/FileTransfer 50
  dirac-admin-add-ftssite IN2P3.fr https://cclcgftsprod.in2p3.fr:8443/glite-data-transfer-fts/services/FileTransfer 50
 
4. Install CleanFTSDBAgent::

  dirac-install-agent DataManagement/CleanFTSDBAgent

5. Install FTSAgent::

  dirac-install-agent DataManagement/FTSAgent


Again, as in case of RequestExecutingAgent, if one instance is not enough, you can easily clone it to several instances. 


7. Once all requests from old version of system are processed, shutdown and remove agents:: 

  RequestManagement/DISETForwardingAgent
  RequestManagement/RequestCleaningAgent
  DataManagement/TransferAgent
  DataManagement/RegistrationAgent
  DataManagement/RemovalAgent

and services::

  RequestManagement/RequestManager
  RequestManagement/RequestProxy
  DataManagement/TransferDBMonitor

and dbs::

  RequestManagement/RequestDB
  DataManagement/TransferDB

