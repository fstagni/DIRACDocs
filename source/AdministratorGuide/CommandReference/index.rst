====================================
Administrator Command Reference
====================================

In this subsection all the dirac-admin commands available are explained. You can 
get up-to-date documentation by using the -h switch on any of them. The following command line 
flags are common to all DIRAC scripts making use of the *parseCommandLine* method of the base *Script* class::

      General options: 
        -o:  --option=         : Option=value to add 
        -s:  --section=        : Set base section for relative parsed options 
        -c:  --cert=           : Use server certificate to connect to Core Services 
        -d   --debug           : Set debug mode (-dd is extra debug) 
        -h   --help            : Shows this help 

General information:

.. toctree::
    :maxdepth: 2

    dirac-admin-externals-versions
    dirac-admin-service-ports
    dirac-platform

Managing Registry:

.. toctree::
    :maxdepth: 2   
     
    dirac-admin-add-group
    dirac-admin-add-host
    dirac-admin-add-user
    dirac-admin-delete-user        
    dirac-admin-list-hosts
    dirac-admin-list-users
    dirac-admin-modify-user
    dirac-admin-sync-users-from-file
    dirac-admin-user-quota
    dirac-admin-users-with-proxy
    
Managing Resources:

.. toctree::
    :maxdepth: 2   
    
    dirac-admin-add-site

    dirac-admin-allow-catalog
    dirac-admin-allow-se
    dirac-admin-allow-site
    dirac-admin-ban-catalog
    dirac-admin-ban-se
    dirac-admin-ban-site
    dirac-admin-bdii-ce-state
    dirac-admin-bdii-ce-voview
    dirac-admin-bdii-ce
    dirac-admin-bdii-cluster
    dirac-admin-bdii-sa
    dirac-admin-bdii-site
    dirac-admin-ce-info
    dirac-admin-get-banned-sites
    dirac-admin-get-site-mask
    dirac-admin-get-site-protocols
    dirac-admin-set-site-protocols
    dirac-admin-site-info
    dirac-admin-site-mask-logging
    
Workload management commands:

.. toctree::
    :maxdepth: 2

    dirac-admin-get-job-pilot-output
    dirac-admin-get-job-pilots
    dirac-admin-get-pilot-info
    dirac-admin-get-pilot-logging-info
    dirac-admin-get-pilot-output
    dirac-admin-pilot-summary
    dirac-admin-reoptimize-jobs
    dirac-admin-reset-job
    dirac-admin-show-task-queues
    dirac-admin-submit-pilot-for-job
    dirac-jobexec

Transformation management commands:

.. toctree::
    :maxdepth: 2

    dirac-transformation-archive
    dirac-transformation-clean
    dirac-transformation-cli
    dirac-transformation-remove-output
    dirac-transformation-resolve-problematics
    dirac-transformation-verify-outputdata
    
Managing DIRAC installation:

.. toctree::
    :maxdepth: 2

    dirac-framework-ping-service
    dirac-install-agent
    dirac-install-db
    dirac-install-service
    dirac-install-web-portal
    dirac-install
    dirac-restart-component
    dirac-restart-mysql
    dirac-start-component
    dirac-start-mysql
    dirac-status-component
    dirac-stop-component
    dirac-stop-mysql
    dirac-monitoring-get-components-status
    dirac-service
    dirac-setup-site
    dirac-configure
    dirac-admin-get-CAs   
        
Managing DIRAC software:

.. toctree::
    :maxdepth: 2
    
    dirac-create-distribution-tarball
    dirac-create-svn-branch
    dirac-create-svn-tag
    dirac-deploy-scripts
    dirac-distribution
    dirac-externals-requirements
    dirac-fix-ld-library-path
    
Other commands:

.. toctree::
    :maxdepth: 2

    dirac-admin-accounting-cli
    
    dirac-admin-get-proxy
    dirac-admin-proxy-upload
    dirac-admin-upload-proxy

    dirac-admin-request-summary
    dirac-admin-select-requests

    dirac-admin-sysadmin-cli

    dirac-configuration-cli
    dirac-configuration-dump-local-cache
    dirac-configuration-shell


    dirac-repo-monitor

    dirac-rss-reassign-token
    dirac-rss-renew-token

    dirac-stager-monitor-request
    dirac-stager-stage-files

    install_site.sh
