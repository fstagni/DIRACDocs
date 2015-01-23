=====================
Web Portal User guide
=====================

  
The DIRAC Web portal is a user friendly interface which allows user to interact to the DIRAC components. 
It can be easily extended by other VO or it can be integrated to other portal. 
It is developed by taking into account what we have learnt using the current web portal.


Therms:
-------

**Application** 

   A web page called application in the new portal, for example: monitoring, accounting, production management. 
   
**Desktop** 

   It is a container which contains different applications. Each application always open in a desktop. The desktop is your work environment. 

**State** 

   The state is the actual status of an application or a desktop. The state can be saved and it can be reused. A saved state can be shared within
   the VO or between users. 

**Theme**

   It is a graphical appearance of the web portal. DIRAC provides two themes: Desktop and Tab themes. Both themes provide similar functionalities. 
   The difference is the way of how the applications are managed. 
   The "**Desktop theme**" is similar to Microsoft Windows. It allows to work with a single desktop.
   The "**Tab theme**" is similar to web browser. Each desktop is a tab. The users can work with different desktops in the same time. 
    
Concepts:
---------

Two protocol is allowed: **http** and **htpps**. 
**http** protocol is very restricted. It only allows to access limited functionalities. It is recommended to the site administrators. The state of applications or
desktops can not be saved.
**https** protocol allow to access all functionalities of DIRAC depending on your role (proxy group). 
The state of the application is not saved in the URL. The URL only contains the name of application or desktop. For example: https://lhcb-portal-dirac.cern.ch/DIRAC/s:LHCb-Production/g:lhcb_prmgr/?view=tabs&theme=Grey&url_state=1|AllPlots  

  
.. toctree::
   :maxdepth: 1


   BrowseRemoteConfiguration/index
   DataLoggingMonitor/index
   ErrorConsole/index
   JobMonitoring/index
   ManageProxies/index
   ManageRemoteConfiguration/index
   Overview/index
   PilotMonitor/index
   PilotSummary/index
   ProductionMonitor/index
   ProxyActionLogs/index
   RawIntegrity/index
   ShowHistoryOfServerChanges/index
   SiteSummary/index
   StorageDirectorySummary/index

