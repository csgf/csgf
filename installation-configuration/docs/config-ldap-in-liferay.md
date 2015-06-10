This page explains how our LDAP server is configured in order to allow authentication and authorisation of users by an Identity Provider and and a Service Provider.  

[TOC]

# Prerequisites
## LDAP Configuration

The following sections describes branches present in our LDAP and what any branch is meant to. For each branch is shown all user **attributes (UA)** and all **operational attributes (OA)**.

### LDAP root <dc=local\> 
It's the route of the LDAP server

~~~~~~~~
# extended LDIF
#
# LDAPv3
# base <dc=local> with scope baseObject
# filter: (objectClass=*)
# requesting: ALL
#

# local
dn: dc=local
objectClass: dcObject
objectClass: organization
dc: local
o: INFN

# search result
search: 2
result: 0 Success

# numResponses: 2
# numEntries: 1
~~~~~~~~  

### Administrator user for idp <cn=idp,dc=local\>
It's the administrator user who register users in the IDP

~~~~~~~~
# extended LDIF
#
# LDAPv3
# base <cn=idp,dc=local> with scope baseObject
# filter: (objectClass=*)
# requesting: ALL
#

# idp, local
dn: cn=idp,dc=local
objectClass: top
objectClass: person
objectClass: simpleSecurityObject
sn: IDPCT
cn: idp
description:: QWNjb3VudCB1c2F0byBwZXIgbGEgZ2VzdGlvbmUgZGVsbGUgaWRlbnRpdMOgIGZh
 dHRlIGRhIElEUE9QRU4=

# search result
search: 2
result: 0 Success

# numResponses: 2
# numEntries: 1
~~~~~~~~

### Administrator user for the Science Gateway <cn=liferayadmin,dc=local\>
It's the administrator user who is configured in the Science Gateway

~~~~~~~~
# extended LDIF
#
# LDAPv3
# base <cn=liferayadmin,dc=local> with scope baseObject
# filter: (objectClass=*)
# requesting: ALL
#

# liferayadmin, local
dn: cn=liferayadmin,dc=local
objectClass: top
objectClass: person
objectClass: simpleSecurityObject
cn: liferayadmin
sn: Liferay

# search result
search: 2
result: 0 Success

# numResponses: 2
# numEntries: 1
~~~~~~~~

### Country grouping organisations <c=IT,ou=Organisations,dc=local\>

We group all users' organisations according to their country. For example all Italian organisations are stored in this branch. 

~~~~~~~~
# extended LDIF
#
# LDAPv3
# base <c=IT,ou=Organisations,dc=local> with scope baseObject
# filter: (objectClass=*)
# requesting: ALL
#

# IT, Organisations, local
dn: c=IT,ou=Organisations,dc=local
objectClass: top
objectClass: country
objectClass: friendlyCountry
c: IT
co: Italy
description: Europe, Southern Europe

# search result
search: 2
result: 0 Success

# numResponses: 2
# numEntries: 1
~~~~~~~~

### Example of organisations <o=INFN,c=IT,ou=Organisations,dc=local\>

The example below shows the entry for the INFN which is an Italian organisation. 

~~~~~~~~
# extended LDIF
#
# LDAPv3
# base <o=INFN,c=IT,ou=Organisations,dc=local> with scope baseObject
# filter: (objectClass=*)
# requesting: ALL
#

# INFN, IT, Organisations, local
dn: o=INFN,c=IT,ou=Organisations,dc=local
objectClass: top
objectClass: organization
description: National Institute of Nuclear Physics
o: INFN
registeredAddress: http://www.infn.it

# search result
search: 2
result: 0 Success

# numResponses: 2
# numEntries: 1
~~~~~~~~

### Example of division of organisation <ou=Catania,o=INFN,c=IT,ou=Organisations,dc=local\>

When an organisation has many divisions they are grouped inside it. The example below shows the Division of Catania of INFN

~~~~~~~~
# extended LDIF
#
# LDAPv3
# base <ou=Catania,o=INFN,c=IT,ou=Organisations,dc=local> with scope baseObject
# filter: (objectClass=*)
# requesting: ALL
#

# Catania, INFN, IT, Organisations, local
dn: ou=Catania,o=INFN,c=IT,ou=Organisations,dc=local
objectClass: top
objectClass: organizationalUnit
ou: Catania
registeredAddress: www.ct.infn.it
postalAddress: Via Santa Sofia 64, 95123, Catania

# search result
search: 2
result: 0 Success

# numResponses: 2
# numEntries: 1
~~~~~~~~

### Example of Services <dc=idpct,ou=Services,dc=local\>

In this branch all users who can be authenticated by the idp are stored. These are distinguished by the administrator because they are stored in a different group.   

~~~~~~~~
# extended LDIF
#
# LDAPv3
# base <dc=idpct,ou=Services,dc=local> with scope baseObject
# filter: (objectClass=*)
# requesting: ALL
#

# idpct, Services, local
dn: dc=idpct,ou=Services,dc=local
objectClass: dcObject
objectClass: domainRelatedObject
objectClass: domain
objectClass: top
dc: idpct
associatedDomain: idp.ct.infn.it

# search result
search: 2
result: 0 Success

# numResponses: 2
# numEntries: 1
~~~~~~~~

### IDP Administrators <cn=Administrator,ou=Group,dc=idpct,ou=Services,dc=local\>

In the example below, the list of all users able to administrate the ldap is shown (i.e. to edit it). User's group is configured so that user's dn is unique. As you can see, together with administrator1, administrator2 and aministrator3, there is the liferayadmin users that is configured in liferay to make the two services communicate. Differently, administrator1, as a real user, is present the group People. 


~~~~~~~~
# extended LDIF
#
# LDAPv3
# base <cn=Administrator,ou=Group,dc=idpct,ou=Services,dc=local> with scope baseObject
# filter: (objectClass=*)
# requesting: ALL
#

# Administrator, Group, idpct, Services, local
dn: cn=Administrator,ou=Group,dc=idpct,ou=Services,dc=local
objectClass: top
objectClass: groupOfUniqueNames
uniqueMember: cn=administrator1,ou=People,dc=local
uniqueMember: cn=administrator2,ou=People,dc=local
uniqueMember: cn=administrator3,ou=People,dc=local
uniqueMember: cn=liferayadmin,dc=local
cn: Administrator
description: Users in this group have administrative privileges on this server

# search result
search: 2
result: 0 Success

# numResponses: 2
# numEntries: 1
~~~~~~~~

### List of IDP Users <cn=Users,ou=Group,dc=idpct,ou=Services,dc=local\>

In the same group we have the list of users who can be authenticated by the IDP. In order to make these users log in the Science Gateway (SG) is possible to connect the SG to this services or create a brand new services. This new branch then will be the one responsible for the authorisation. Liferay will do a map 1:1 between the group items present in the services and the different roles assigned to the user of the SG. For example let's suppose we use IDP branch for both authentication and authorisation. In this special case the user with cn=administrator1 will be administrator both of IDP and of the Science Gateway. 

~~~~~~~~
# extended LDIF
#
# LDAPv3
# base <cn=Users,ou=Group,dc=idpct,ou=Services,dc=local> with scope baseObject
# filter: (objectClass=*)
# requesting: ALL
#

# Users, Group, idpct, Services, local
dn: cn=Users,ou=Group,dc=idpct,ou=Services,dc=local
uniqueMember: cn=fmarco76,ou=People,dc=local
uniqueMember: cn=rotondo,ou=People,dc=local
uniqueMember: cn=barbera,ou=People,dc=local
uniqueMember: cn=brunor,ou=People,dc=local
....
....
cn: Users
description: List of users using this server for authentication
objectClass: top
objectClass: groupOfUniqueNames

# search result
search: 2
result: 0 Success

# numResponses: 2
# numEntries: 1
~~~~~~~~

### User branch example <cn=rotondo,ou=People,dc=local\>

Below we have all the information related to an user. 

~~~~~~~~
# extended LDIF
#
# LDAPv3
# base <cn=rotondo,ou=People,dc=local> with scope baseObject
# filter: (objectClass=*)
# requesting: ALL
#

# rotondo, People, local
dn: cn=rotondo,ou=People,dc=local
cn: rotondo
displayName: rotondo
initials: RR
mail: riccardo.rotondo@ct.infn.it
mail: riccardo.rotondo@garr.it
mail: net.ricky@gmail.com
objectClass: top
objectClass: person
objectClass: organizationalPerson
objectClass: inetOrgPerson
sn: Rotondo
givenName: Riccardo
registeredAddress: riccardo.rotondo@ct.infn.it
o: o=INFN,c=IT,ou=Organisations,dc=local
o: o=GARR,c=IT,ou=Organisations,dc=local
ou: ou=Catania,o=INFN,c=IT,ou=Organisations,dc=local
postalAddress: Via Santa Sofia 64, 95123, Catania
telephoneNumber: 00 39 095 3785519
mobile: Skype: riccardo.ro
title: Mr.

# search result
search: 2
result: 0 Success

# numResponses: 2
# numEntries: 1
~~~~~~~~

Let's explain some fields in more detail

#### cn 

**Mandatory field**

It's the screen name associated to the user. It appears in the dn and must be unique as it's used by the services group for authentication and/or authorisation. 

#### display name

**Mandatory field**

It's used by the Science Gateway, it can be set equal to the cn. 

#### sn

**Mandatory field**

User surname

#### givenName

User first name

#### mail

**Mandatory field**

As you can see from the example, this value accepts many variables. It's used to specify different mail corresponding to different identity provider. 

#### registeredAddress

**Mandatory field**

It's the mail recognised by the IDP and it's the id used by the Science Gateway to identify to the user. 

#### o

**Mandatory field**

It's the organisation the user belong to. 

#### ou

**Optional field**

In case it exists, it's the division of the organisation. 

#### Others fields

All others entry: initials, title, postalAddress, telephoneNumber, mobile are **optional field**. 

### Operational attributes for user <cn=rotondo,ou=People,dc=local\> +: 

Once a user is present in the People branch, the dn can be inserted in services where the users need to access. This operation will modify automatically the user item adding the operational attributes corresponding to that services. 

A query to ldap requesting the operational attributes for example, show that this user can be authenticated by the IDP as a User ***(memberOf: cn=Users,ou=Group,dc=idpct,ou=Services,dc=local)*** and is authorised to access to the Science Gateway that refers to the service sgw ***(memberOf: cn=GenericUser,ou=Group,dc=sgw,ou=Services,dc=local)***. Moreover he is not only a simple user but he owns other roles, Administrator and CloudManager ***(memberOf: cn=Administrator,ou=Group,dc=sgw,ou=Services,dc=local memberOf: cn=CloudManager,ou=Group,dc=sgw,ou=Services,dc=local)***.

~~~~~~~~~
# extended LDIF
#
# LDAPv3
# base <cn=rotondo,ou=People,dc=local> with scope baseObject
# filter: (objectClass=*)
# requesting: + 
#

# rotondo, People, local
dn: cn=rotondo,ou=People,dc=local
structuralObjectClass: inetOrgPerson
entryUUID: ac78cfbc-4bbe-1030-8b49-c57783a62e4d
creatorsName: cn=admin,dc=local
createTimestamp: 20110726103506Z
memberOf: cn=Users,ou=Group,dc=idpct,ou=Services,dc=local
memberOf: cn=GenericUser,ou=Group,dc=sgw,ou=Services,dc=local
memberOf: cn=Administrator,ou=Group,dc=sgw,ou=Services,dc=local
memberOf: cn=Administrator,ou=Group,dc=sgw,ou=Services,dc=local
pwdChangedTime: 20110726104012Z
entryCSN: 20130313091536.316389Z#000000#000#000000
modifiersName: cn=rotondo,ou=People,dc=local
modifyTimestamp: 20130313091536Z
entryDN: cn=rotondo,ou=People,dc=local
subschemaSubentry: cn=Subschema
hasSubordinates: FALSE

# search result
search: 2
result: 0 Success
~~~~~~~~~

# Service Provider Configuration
## Add CA Certificate to the keystore

Before configuring Liferay you should verify, for example with a utility such as ldapsearch, that SSL communication is possible between the service provider (from now on sg-server) and the ldap server (from now on ldap-server). 

Before doing the first test, make sure you have added the certificate of the certification authority that released your ldap-server certificate. This certificate must be added to the glassfish keystore, which is the application server we use in the science gateway, our service provider. 

The path of the keystore can be seen with: 

~~~~~~~~~
[liferayadmin@sg-server ~]$ ps aux |grep glassfish
500       1659  1.4 19.6 8295936 3208572 pts/1 Sl   Feb19 465:35 /usr/lib/jvm/java-1.7.0-openjdk-1.7.0.9.x86_64/bin/java -cp /opt/glassfish3/glassfish/modules/glassfish.jar -XX:+UnlockDiagnosticVMOptions -XX:+UseConcMarkSweepGC -XX:MaxPermSize=256m -XX:MaxNewSize=700m -XX:NewSize=700m -XX:NewRatio=2 -XX:SurvivorRatio=10 -Xmx3072m -Xms3072m -javaagent:/opt/glassfish3/glassfish/lib/monitor/btrace-agent.jar=unsafe=true,noServer=true -server -Dfelix.fileinstall.disableConfigSave=false -Djavax.net.ssl.keyStore=/opt/glassfish3/glassfish/domains/liferay/config/keystore.jks -Djava.awt.headless=true -Dfelix.fileinstall.poll=5000 -Djava.endorsed.dirs=/opt/glassfish3/glassfish/modules/endorsed:/opt/glassfish3/glassfish/lib/endorsed -Dfelix.fileinstall.bundles.startTransient=true -Djavax.net.ssl.trustStore=/opt/glassfish3/glassfish/domains/liferay/config/cacerts.jks -Dcom.sun.enterprise.security.httpsOutboundKeyAlias=s1as -Djava.security.auth.login.config=/opt/glassfish3/glassfish/domains/liferay/config/login.conf -DANTLR_USE_DIRECT_CLASS_LOADING=true -Dgosh.args=--nointeractive -Dosgi.shell.telnet.maxconn=1 -Djdbc.drivers=org.apache.derby.jdbc.ClientDriver -Dfelix.fileinstall.dir=/opt/glassfish3/glassfish/modules/autostart/ -Dosgi.shell.telnet.port=6666 -Djava.security.policy=/opt/glassfish3/glassfish/domains/liferay/config/server.policy -Dfelix.fileinstall.log.level=2 -Dcom.sun.aas.instanceRoot=/opt/glassfish3/glassfish/domains/liferay -Dcom.sun.enterprise.config.config_environment_factory_class=com.sun.enterprise.config.serverbeans.AppserverConfigEnvironmentFactory -Dosgi.shell.telnet.ip=127.0.0.1 -Dcom.sun.aas.installRoot=/opt/glassfish3/glassfish -Djava.ext.dirs=/usr/lib/jvm/java-1.7.0-openjdk-1.7.0.9.x86_64/lib/ext:/usr/lib/jvm/java-1.7.0-openjdk-1.7.0.9.x86_64/jre/lib/ext:/opt/glassfish3/glassfish/domains/liferay/lib/ext -Dcompany-id-properties=true -Dfelix.fileinstall.bundles.new.start=true -Dorg.glassfish.additionalOSGiBundlesToStart=org.apache.felix.shell,org.apache.felix.gogo.runtime,org.apache.felix.gogo.shell,org.apache.felix.gogo.command -Djava.library.path=/opt/glassfish3/glassfish/lib:/usr/java/packages/lib/amd64:/usr/lib64:/lib64:/lib:/usr/lib com.sun.enterprise.glassfish.bootstrap.ASMain -domainname liferay -asadmin-args --host,,,localhost,,,--port,,,4848,,,--secure=false,,,--terse=false,,,--echo=false,,,--interactive=true,,,start-domain,,,--verbose=false,,,--debug=false,,,--domaindir,,,/opt/glassfish3/glassfish/domains,,,liferay -instancename server -verbose false -debug false -asadmin-classpath /opt/glassfish3/glassfish/modules/admin-cli.jar -asadmin-classname com.sun.enterprise.admin.cli.AsadminMain -upgrade false -type DAS -domaindir /opt/glassfish3/glassfish/domains/liferay -read-stdin true
500       3990  0.0  0.0 103236   880 pts/0    S+   10:59   0:00 grep glassfish
~~~~~~~~~ 

Suppose that your CA certificate location is:

~~~~~~~~~
/home/liferayadmin/INFN_CA.cer
~~~~~~~~~

execute: 

~~~~~~~~~
liferayadmin@sg-devel ~]$  keytool -import -alias infn-ca -file /home/lif\
erayadmin/INFN_CA.cer -keystore /opt/glassfish3/glassfish/domains/liferay/config/cacerts.jks
~~~~~~~~~

Test if you are able to get the list of users with: 

~~~~~~~~~
liferayadmin@sg-devel ~]$ ldapsearch -x -H ldaps://ldap-server -b ou=People,dc=local
~~~~~~~~~

## Shibboleth Configuration


You will need to configure Shibboleth, which is the federated identity solution that Catania-SG uses.

The file *shibboleth2.xml* in /etc/shibboleth contains the configurations necessary.

~~~~~~~~~
root@sg-devel ~]$ vim /etc/shibboleth/shibboleth2.xml
~~~~~~~~~

Locate the *SSO entityID=* and replace the information below.

~~~~~~~~~
 <SSO entityID="https://idp.someaddress.com/idp/shibboleth">
    SAML2 SAML1
 </SSO> 
~~~~~~~~~

Secondly, you will need to request that the LDAP provider sends you their LDAP metadata, in XML form, which is then added to a file called **partner-metadata.xml** in the /etc/shibboleth directory.

~~~~~~~~~
root@sg-devel ~]$ vim /etc/shibboleth/partner-metadata.xml
~~~~~~~~~


Thirdly, you will need to edit proxy_ajp.conf file

~~~~~~~~~
root@sg-devel ~]$ vim /etc/httpd/conf.d/proxy_ajp.conf
~~~~~~~~~

and uncomment the following lines

~~~~~~~~~
ProxyPass /shibboleth/ !
ProxyPass /Shibboleth.sso/ !
ProxyPass / ajp://localhost:8009/ 
~~~~~~~~~

Then ensure that the **shibd** daemon has been started, and will start again if the server reboots.

~~~~~~~~~
:::shell
root@sg-devel ~]$ service shibd start
root@sg-devel ~]$ chkconfig shibd on
~~~~~~~~~


## Liferay Configuration

You need to edit the liferay portal-ext.properties file: 

~~~~~~~~~
 vim /opt/glassfish3/glassfish/domains/liferay/applications/liferay611cega2/WEB-INF/classes/portal-ext.properties 

# LDAP server configuration

ldap.import.method=group
ldap.import.enabled=true
ldap.import.create.role.per.group=true
ldap.import.interval=3
~~~~~~~~~

Restart the domain. 

Now access your liferay server from the web interface. 

Click on the top right *Go to --> Control Panel*
Then on the left *Portal Settings*
On the right *Authentication*
In the top bar click LDAP
Set the option as in figure:

<img src="http://img541.imageshack.us/img541/3536/liferayldap.png" alt="LDAP"></img>

Now click on ADD to add and ldap server that liferay will contact to authorise users. 

<img src="http://img138.imageshack.us/img138/6784/ldapconfig.png" alt="LDAPconfig"></img>

If you organised your ldap as ours here there is the list of value you need to add: 

**Connection**
~~~~~~~~~
Server Name: Your ldap server name
Base Provider URL: ldaps://your-ldap-hostname:636
Base DN: dc=local
Principal: cn=liferayadmin,dc=local
Credentials: liferayadmin-password
~~~~~~~~~

**Users**
~~~~~~~~~
Authertication Search Filter: (&(cn=@screen_name@)(memberOf=cn=GenericUser,ou=Group,dc=sgw,ou=Services,dc=local))
Import Search Filter: (&(objectClass=inetOrgPerson)(memberOf=cn=GenericUser,ou=Group,dc=sgw,ou=Services,dc=local))
Screen Name: cn
Password: userPassword
Email Address: registeredAddress
First Name: givenName
Last Name: sn
Job Title: title
Group
memberOf
~~~~~~~~~

**Groups**
~~~~~~~~~
Import Search Filter: (&(objectClass=groupOfUniqueNames)(o=dc=sgw,ou=Services,dc=local))
Group Name: cn
Description: description
User: uniqueMember
~~~~~~~~~

**Export**
~~~~~~~~~~
Users DN: ou=People,dc=local
User Default Object Classes: top,person,inetOrgPerson,organizationalPerson
Groups DN: ou=Group,dc=sgw,ou=Services,dc=local
Group Default Object Classes: top,groupOfUniqueNames
~~~~~~~~~~

Click Save
