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
The state of the application is not saved in the **URL**. The URL only contains the name of application or desktop. For example: `https://lhcb-portal-dirac.cern.ch/DIRAC/s:LHCb-Production/g:lhcb_prmgr/?view=tabs&theme=Grey&url_state=1|AllPlots`   

**Format of the URL**
   
   * Tab theme:
   
   Format of the URL when the Tab theme is used: 
   1. https://: protocol
   2. lhcb-portal-dirac.cern.ch/DIRAC/: host.
   3. s:LHCb-Production: DIRAC setup.
   4. g:lhcb_prmgr : role
   5. view=tabs : it is the theme. It can be **desktop** and **tabs**.
   6. theme=Grey: it is the look and feel.
   7. &url_state=1 it is desktop or application.
   8 |AllPlots it is the desktop name. the **Default** desktop is **. |AllPlots,*LHCbDIRAC.LHCbJobMonitor.classes.LHCbJobMonitor:AllUserJobs,* 
  
   * Desktop theme

If you have a state saved under Desktop theme, you can open using Tab theme. This is other way around as well.

   
An video tutorial available on `https://www.youtube.com/watch?v=vKBpED0IyLc` link.

.. toctree::
   :maxdepth: 1

   TabTheme/index
   DesktopTheme/index
