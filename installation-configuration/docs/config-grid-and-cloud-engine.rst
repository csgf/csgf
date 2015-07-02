**********************************************
Configuring the Grid & Cloud Engine on Liferay
**********************************************

Check java version using the following command:


::

	java -version command


the version installed should be 1.7, if you have installed a previous one, please update it before to proceed.

Create the directory (as root)

::


	mkdir /etc/grid-security/vomsdir


Create the directory (as liferayadmin)

::

	mkdir /tmp/jobOutput/


**MySQL Server Configuration**

Remeber that you need to configure the database. You can do that following `this guide <https://sourceforge.net/p/ctsciencegtwys/wiki/ConfigUserTrackingDBInMySQL>`_


**Installation**

Before starting the installation make sure your liferay domains is stopped. 

**Library Dependencies**

Download Grid & Cloud Engine and JSAGA libraries from `here <http://sourceforge.net/projects/ctsciencegtwys/files/catania-grid-engine/1.5.9/Liferay6.1/GridEngine_v1.5.9.zip/download>`_

Unzip GridEngine_v1.5.9.zip:

::


	unzip GridEngine_v1.5.9.zip


copy the extracted lib folder under the liferay domain folder:

::

	cp -r lib /opt/glassfish3/glassfish/domains/liferay/


**Configuration**


	**LogFile**

Download the attached GridEngineLogConfig.xml `link <https://sourceforge.net/p/ctsciencegtwys/wiki/ConfigCTGridEngine/attachment/GridEngineLogConfig.xml>`_, and move this file to the Liferay config folder:

::


	mv GridEngineLogConfig.xml /opt/glassfish3/glassfish/domains/liferay/config



**Glassfish Configuration**

Restart the Glassfish server and when the server is up access the web administration console:

[http://sg-server:4848]

fill with username liferayadmin and the password you set for the glassfish administrator and create the required resources. 

**JNDI Resources*


Select Resources -> JNDI -> Custom Resources from left panel. On the right panel you can create the resources by clicking the New... button.

**GridEngine-CheckStatusPool**

* Create **GridEngine-CheckStatusPool** with the following parameters;
	** **JNDI Name**: GridEngine-CheckStatusPool;
	** **Resource Type**: it.infn.ct.ThreadPool.CheckJobStatusThreadPoolExecutor
    	** **Factory Class**: it.infn.ct.ThreadPool.CheckJobStatusThreadPoolExecutorFactory
    	*** **Additional Properties**:
        	* **corePoolSize**: 50
       		* **maximumPoolSize**: 100
        	* **keepAliveTime**: 4
        	* **timeUnit**: MINUTES
        	* **allowCoreThreadTimeOut**: true
        	* **prestartAllCoreThreads**: true

.. image:: figures-and-documents/GridEngine-CheckStatusPool.png
   :align: center



**GridEngine-Pool**

* Create **GridEngine-Pool** with the following parameters:
	
	* **JNDI Name**: GridEngine-Pool;
	
	* **Resource Type**: it.infn.ct.ThreadPool.ThreadPoolExecutor

	* **Factory Class**: it.infn.ct.ThreadPool.ThreadPoolExecutorFactory
    	
	* **Additional Properties**:
        	
		* **corePoolSize**: 50
        	
		* **maximumPoolSize**: 100
        
		* **keepAliveTime**: 4
        
		* **timeUnit**: MINUTES
        
		* **allowCoreThreadTimeOut**: true
        
		* **prestartAllCoreThreads**: true

.. image:: figures-and-documents/GridEngine-Pool.png
   :align: center


**JobCheckStatusService**

* Create **JobCheckStatusService** with the following parameters:

	* **JNDI Name**: JobCheckStatusService;

	* **Resource Type**: it.infn.ct.GridEngine.JobService.JobCheckStatusService

	 * **Factory Class**: it.infn.ct.GridEngine.JobService.JobCheckStatusServiceFactory

	* **Additional Properties**:
        	
		* **jobsupdatinginterval**: 900

.. image:: figures-and-documents/JobCheckStatusService.png
   :align: center


##### JobServices-Dispatcher
* Create **JobServices-Dispatcher** with the following parameters:
    * **JNDI Name**: JobServices-Dispatcher;
    * **Resource Type**: it.infn.ct.GridEngine.JobService.JobServicesDispatcher
    * **Factory Class**: it.infn.ct.GridEngine.JobService.JobServicesDispatcherFactory
    * **Additional Properties**:
        * **retrycount**: 3;
        * **resubnumber**: 10;
        * **myproxyservers**: gridit=myproxy.ct.infn.it;prod.vo.eu-eela.eu=myproxy.ct.infn.it;cometa=myproxy.ct.infn.it;eumed=myproxy.ct.infn.it;vo.eu-decide.eu=myproxy.ct.infn.it;sagrid=myproxy.ct.infn.it;euindia=myproxy.ct.infn.it;see=myproxy.ct.infn.it;



.. image:: figures-and-documents/JobServices-Dispatcher.png
   :align: center


**JDBC Resources**

Select Resources -> JDBC -> JDBC Connection Pools from left panel. On the right panel you can create the resources by clicking the New... button.

**UserTrackingPool**

* Create **UserTrackingPool** with the following parameters:

	* General Settings (Step 1/2):
        	
		* **Pool Name**: usertrackingPool
		
		* **Resource Type**: select javax.sql.DataSource

		* **Database Driver Vendor**: select MySql
        
		* Click Next
    
	* Advanced Settings (Step 2/2):
        
		* Edit the default parameters in **Pool Settings** using the following values:

			* **Initial and Minimum Pool Size**: 64

			* **Maximum Pool Size**: 256
        	
		* Select all default Additional properties and delete them
           
			 * Add the following properties:
                
				1. **Name**: Url         **Value**: jdbc:mysql://sg-database:3306/userstracking
               	
				*  **Name**: User        **Value**: tracking_user
                	
				*  **Name**: Password    **Value**: usertracking
        
	* Click Save

Please pay attention to the Url property, ***sg-database*** should be replaced with the correct Url of your database machine.
You can check if you have correctly configured the Connection Pool by clicking on Ping button,  you should see the message **Ping Succeded**, otherwise please check your configuration.

.. image:: figures-and-documents/UsersTrackingPool.png
   :align: center

.. image:: figures-and-documents/UsersTrackingPool_AP.png
   :align: center


Restart glassfish to save the resources.

Select Resources -> JDBC -> JDBC Resources from left panel. On the right panel you can create the resources by clicking the New... button.

**jdbc/UserTrackingPool**

* Create **jdbc/UserTrackingPool** with the following parameter:

	* **JNDI Name**: jdbc/UserTrackingPool;

	* **Pool name**: select usertrackingPool.

.. image:: figures-and-documents/jdbcUsersTrackingPool.png
   :align: center


**jdbc/gehibernatepool**

* Create **jdbc/gehibernatepool** with the following parameter:

	* **JNDI Name**: jdbc/gehibernatepool;

	* **Pool name**: select usertrackingPool.


.. image:: figures-and-documents/jdbcgehibernatepool.png
   :align: center
