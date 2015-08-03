**************************************
Configuring MySQL Database for liferay
**************************************

=======================
Prerequisites - Machine
=======================

These instructions describes how to install and configure a mysql server on a Debian 6.0.3

.. code::

	root@sg-database:~# lsb_release -a
	No LSB modules are available.
	Distributor ID: Debian
	Description:    Debian GNU/Linux 6.0.3 (squeeze)
	Release:        6.0.3
	Codename:       squeeze


Install vim: 

.. code::

	apt-get install vim

==================================
MySQL Installation & Configuration
==================================


Install MySQL server and client:

.. code::

	apt-get mysql-client mysql-server


Create databases
----------------

First you need to set mysql password:
 
.. code::

	mysqladmin -u root password 'rootPassword'


Then you can create an user and a database for liferay, and an user and a database for the Catania Grid Engine. 

Database lportal
----------------

Access as root using the password you have just set:

.. code::

	root@sg-database:~# mysql -u root -p
	Enter password: 
	Welcome to the MySQL monitor.  Commands end with ; or \g.
	Your MySQL connection id is 3430
	Server version: 5.1.49-3 (Debian)
	
	Copyright (c) 2000, 2010, Oracle and/or its affiliates. All rights reserved.
	This software comes with ABSOLUTELY NO WARRANTY. This is free software,
	and you are welcome to modify and redistribute it under the GPL v2 license
	
	Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
	
	mysql> 


Add a new user and a new database named lportal. Give the user the privileges to access the database. It's important to grant the privileges even for the access from the science gateway

.. code:: sql

	CREATE USER 'liferayadmin' IDENTIFIED BY 'fillWithYourPassword';
	Query OK, 0 rows affected (0.00 sec)
	
	CREATE DATABASE lportal;
	Query OK, 1 row affected (0.00 sec)
	
	GRANT ALL PRIVILEGES ON lportal.* TO 'liferayadmin'@'localhost'
    IDENTIFIED BY 'yourPasswd';
	Query OK, 0 rows affected (0.05 sec)
	
	GRANT ALL PRIVILEGES ON lportal.* TO 'liferayadmin'@'IPOfsg-server'
    IDENTIFIED BY 'yourPasswd';
	Query OK, 0 rows affected (0.05 sec)
	
	FLUSH PRIVILEGES;
	Query OK, 0 rows affected (0.04 sec)
	
	exit
	Bye

===============
Troubleshooting
===============

**Firewall**

In case you are not able to connect to the database from the server check the firewall rules, in particular for the port 3306

**Access the database remotely**


Check that mysql is enabled to accept remote connections:

.. code:: sql

	root@sg-database:~# vim /etc/my.cnf
		
	bind-address            = 0.0.0.0 
