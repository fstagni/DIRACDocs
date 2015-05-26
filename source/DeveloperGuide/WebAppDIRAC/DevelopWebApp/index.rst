.. _webappdirac_developwebapp:

==============================
Developing new web application
==============================

The new DIRAC web framework provides many facilities to develop and test web applications. 
This framework loads each application:

* in a separate window, and these windows can be arranged at the desktop area by means of resizing, moving and pinning. 
* in a separate tab and these tabs can be customized.

In this tutorial we are going to explain the ways of developing and testing 

new applications.
Before you start this tutorial, it is desirable that you have some experience with programming in Python, JavaScript, HTML, 
CSS scripting, client-server communication (such as AJAX and web sockets) and sufficient knowledge 
in object-oriented programming. If you are not familiar with some of the web technologies, or 
there has been a while since you used those technologies, please visit the W3CSchool web site (`<http://www.w3schools.com/>`_). T
here, you can find tutorials that you can use to learn or to refresh your knowledge for web-programming. 
As well we suggest to read :ref:`webappdirac_setupeclipse` section.

Each application consists of two parts:

* Client side (CS): Builds the user interface and communicates with the web server in order to get necessary data and show it appropriately.
* Server side (SS): Provides services to the client side run in browser.

The folder structure of the server side web installation is as follows:

* <Module name folder such as DIRAC, LHCbDIRAC, WebAppDIRAC>

   * WebApp
      * __init__.py
      * handler: contains all the server side implementations of the framework and all the applications.
         * __init__.py
      * static: contains all the static content that can be loaded by the client side such as JavaScript files, images and css files
         * <Module name folder such as DIRAC, LHCbDIRAC, WebAppDIRAC>: contains the client side implementation of each application
            * Application 1
            * Application 2
      * template: contains all the templates used by the files in the handler folder

In order to explain how to develop an application, we will go step by step creating an example one. We will name it MyApp.
