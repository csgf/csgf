**********************************************
Configuring the Grid & Cloud Engine on Liferay
**********************************************

=============
Prerequisites
=============

Check java version using the following command:

.. code:: 

    [liferayadmin@centos6 ~]$ java -version
    java version "1.7.0_79"
    OpenJDK Runtime Environment (rhel-2.5.5.3.el6_6-x86_64 u79-b14)
    OpenJDK 64-Bit Server VM (build 24.79-b02, mixed mode)

the version installed should be 1.7, if you have installed a previous one, please update it before to proceed.

As root user, download the `vomsdir.tar.gz <https://raw.githubusercontent.com/csgf/eToken/master/docs/others/vomsdir.tar.gz>`_

.. code:: 

    [root@centos6 ~]# mv vomsdir /etc/grid-security/


Create the directory (as liferayadmin)

.. code:: 

	[liferayadmin@centos6 ~]$ mkdir /tmp/jobOutput/

--------------------------
MySQL Server Configuration
--------------------------

Remeber that you need to configure the database. You can do that following :doc:`config-user-tracking-db-in-mysql`


================================
Grid & Cloud Engine Installation
================================

Before starting the installation make sure your liferay domains is stopped. 

.. code::

    [liferayadmin@centos6 ~]$ /opt/glassfish3/bin/asadmin stop-domain liferay
    Waiting for the domain to stop ......
    Command stop-domain executed successfully.

------------
Dependencies
------------

Download Grid & Cloud Engine and JSAGA libraries from `here <http://grid.ct.infn.it/csgf/binaries/GridEngine_v1.5.10.zip>`_

Unzip GridEngine_v1.5.9.zip:

.. code::

	[liferayadmin@centos6 ~]$ unzip GridEngine_v1.5.10.zip


copy the extracted lib folder under the liferay domain folder:

.. code::

	[liferayadmin@centos6 ~]$ cp -r lib /opt/glassfish3/glassfish/domains/liferay/

-------------
Configuration
-------------

**LogFile**

Download the attached GridEngineLogConfig.xml `link <https://raw.githubusercontent.com/csgf/csgf/master/installation-configuration/docs/figures-and-documents/GridEngineLogConfig.xml>`_, and move this file to the Liferay config folder:

.. code::

	[liferayadmin@centos6 ~]$ mv GridEngineLogConfig.xml \ 
	/opt/glassfish3/glassfish/domains/liferay/config

**Glassfish Configuration**

Restart the Glassfish server and when the server is up access the web administration console:

[http://sg-server:4848]

fill with username liferayadmin and the password you set for the glassfish administrator and create the required resources. 

**JNDI Resources**

Select `Resources -> JNDI -> Custom Resources` from left panel. Then on the right panel you can create the resources by clicking the **New...** button.

1. Create **GridEngine-CheckStatusPool** with the following parameters [`1`_]:
    - **JNDI Name**: GridEngine-CheckStatusPool
    - **Resource Type**: it.infn.ct.ThreadPool.CheckJobStatusThreadPoolExecutor
    - **Factory Class**: it.infn.ct.ThreadPool.CheckJobStatusThreadPoolExecutorFactory
    - **Additional Properties**:
        - **corePoolSize**: 50
        - **maximumPoolSize**: 100
        - **keepAliveTime**: 4
        - **timeUnit**: MINUTES
        - **allowCoreThreadTimeOut**: true
        - **prestartAllCoreThreads**: true

.. _1:

.. figure:: figures-and-documents/GridEngine-CheckStatusPool.png
   :align: center
   :alt: GridEngine-CheckStatusPool
   :scale: 80%
   :figclass: text    
   
   GridEngine-CheckStatusPool JNDI Resource

2. Create **GridEngine-Pool** with the following parameters [`2`_]:
    - **JNDI Name**: GridEngine-Pool
    - **Resource Type**: it.infn.ct.ThreadPool.ThreadPoolExecutor
    - **Factory Class**: it.infn.ct.ThreadPool.ThreadPoolExecutorFactory
    - **Additional Properties**:
        - **corePoolSize**: 50
        - **maximumPoolSize**: 100
        - **keepAliveTime**: 4
        - **timeUnit**: MINUTES
        - **allowCoreThreadTimeOut**: true
        - **prestartAllCoreThreads**: true

.. _2:

.. figure:: figures-and-documents/GridEngine-Pool.png
   :align: center
   :alt: GridEngine-Pool
   :scale: 80%
   :figclass: text    
   
   GridEngine-Pooll JNDI Resource

3. Create **JobCheckStatusService** with the following parameters [`3`_]:
    - **JNDI Name**: JobCheckStatusService
    - **Resource Type**: it.infn.ct.GridEngine.JobService.JobCheckStatusService
    - **Factory Class**: it.infn.ct.GridEngine.JobService.JobCheckStatusServiceFactory
    - **Additional Properties**:
        - **jobsupdatinginterval**: 900

.. _3:

.. figure:: figures-and-documents/JobCheckStatusService.png
   :align: center
   :alt: JobCheckStatusService
   :scale: 80%
   :figclass: text    
   
   JobCheckStatusService JNDI Resource

4. Create **JobServices-Dispatcher** with the following parameters [`4`_]:
    - **JNDI Name**: JobServices-Dispatcher
    - **Resource Type**: it.infn.ct.GridEngine.JobService.JobServicesDispatcher
    - **Factory Class**: it.infn.ct.GridEngine.JobService.JobServicesDispatcherFactory
    - **Additional Properties**:
        - **retrycount**: 3;
        - **resubnumber**: 10;
        - **myproxyservers**: gridit=myproxy.ct.infn.it; prod.vo.eu-eela.eu=myproxy.ct.infn.it; cometa=myproxy.ct.infn.it; eumed=myproxy.ct.infn.it; vo.eu-decide.eu=myproxy.ct.infn.it; sagrid=myproxy.ct.infn.it; euindia=myproxy.ct.infn.it; see=myproxy.ct.infn.it;

.. _4:

.. figure:: figures-and-documents/JobServices-Dispatcher.png
   :align: center
   :alt: JobServices-Dispatcher
   :scale: 80%
   :figclass: text    
   
   JobServices-Dispatcher JNDI Resource


**JDBC Resources**

Now you have to create the required JDBC Connection Pools. Select `Resources -> JDBC -> JDBC Connection Pools` from left panel. On the right panel you can create the resources by clicking the **New...** button.

- Create **UserTrackingPool** with the following parameters:
    - General Settings (Step 1/2) see [`5`_]:
        - **Pool Name**: UserTrackingPool
        - **Resource Type**: select javax.sql.ConnectionPoolDataSource
        - **Database Driver Vendor**: select MySql
        - Click Next
    - Advanced Settings (Step 2/2) [`6`_]:
       - Edit the default parameters in **Pool Settings** using the following values:
            - **Initial and Minimum Pool Size**: 64
            - **Maximum Pool Size**: 256
       - Select all default Additional properties and delete them
            - Add the following properties:
            
        ========    =====
        Name        Value
        ========    =====
        Url         jdbc:mysql://`sg-database`:3306/userstracking
        User        tracking_user
        Password    usertracking
        ========    =====
       
       - Click Finish

Please pay attention to the Url property, ***sg-database*** should be replaced with the correct Url of your database machine.
You can check if you have correctly configured the Connection Pool by clicking on Ping button,  you should see the message **Ping Succeded**, otherwise please check your configuration.

.. _5:

.. figure:: figures-and-documents/UsersTrackingPool.png
   :align: center
   :alt: UsersTrackingPool
   :scale: 80%
   :figclass: text    
   
   UsersTrackingPool JDBC General settings
   
.. _6:

.. figure:: figures-and-documents/UsersTrackingPool_AP.png
   :align: center
   :alt: UsersTrackingPool_AP
   :scale: 80%
   :figclass: text    
   
   UsersTrackingPool JDBC Advanced settings

Finally, you have to create the required JDBC Resources. Select `Resources -> JDBC -> JDBC Resources` from left panel. On the right panel you can create the resources by clicking the **New...** button.

- Create **jdbc/UserTrackingPool** with the following parameter [`7`_]:
    - **JNDI Name**: jdbc/UserTrackingPool
    - **Pool name**: select usertrackingPool

.. _7:

.. figure:: figures-and-documents/jdbcUsersTrackingPool.png
   :align: center
   :alt: jdbcUsersTrackingPool
   :scale: 90%
   :figclass: text    
   
   jdbcUsersTrackingPool JDBC Resource

- Create **jdbc/gehibernatepool** with the following parameter [`8`_]:
    - **JNDI Name**: jdbc/gehibernatepool
    - **Pool name**: select usertrackingPool

.. _8:

.. figure:: figures-and-documents/jdbcgehibernatepool.png
   :align: center
   :alt: jdbcgehibernatepool
   :scale: 88%
   :figclass: text    
   
   jdbcgehibernatepool JDBC Resource

---------------------
Finalize installation
---------------------

From the left side menu, select *Applications*, find and check marketplace-portlet on the rigth panel and click the **Disable** button
 
Now, restart glassfish to finalize installation.
    
