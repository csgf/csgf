This pages explains how to configure your Service Provider in order to demand authentication to Shibboleth

[TOC]

# Prerequisites 

An apache server and shibboleth installed on the machine

# Configure Apache files 

You should have some apache files configure as follow (usually store in */etc/httpd/conf.d*)

## shib.conf

~~~~~~~~~
vim shib.conf
...
LoadModule mod_shib /usr/lib64/shibboleth/mod_shib_22.so

<Location /secure>
  AuthType shibboleth
  ShibRequestSetting requireSession 1
  require valid-user
</Location>
...
~~~~~~~~~

## shibSec.conf

~~~~~~~~~
vim shibSec.conf
...
#
# Configuration for Liferay Login
#

<Location /c/portal/login>
  AuthType shibboleth
  ShibRequestSetting requireSession 1
  require valid-user
</Location>
<Location /not_authorised>
  AuthType shibboleth
  ShibRequestSetting requireSession 1
  require valid-user
</Location>

#
# Configuration for metadata
#

Alias /shibboleth/ "/var/www/metadata/"

<Directory "/var/www/metadata">
</Directory>
...
~~~~~~~~~

## proxy_ajp.conf

Once you configure the custom url you need to avoid them to contact the glassfish listener
~~~~~~~~~
vim proxy_ajp.conf
...
ProxyPass /shibboleth/ !
ProxyPass /Shibboleth.sso/ !
ProxyPass / ajp://localhost:8009/
...
~~~~~~~~~

# Configure Liferay to contact Shibboleth for authentication

## Install Shibboleth plugin

Download the [shibboleth plugin from here](http://sourceforge.net/projects/ctsciencegtwys/files/catania-science-gateway/plugins/ShibbolethLib-1.0.jar/download) and copy it to: 

~~~~~~~~~
/opt/liferay/glassfish3/glassfish/domains/liferay/applications/liferay611cega2/WEB-INF/lib
~~~~~~~~~
Now edit the portlet-ext.properties adding these lines:

~~~~~~~~~
vim /opt/liferay/glassfish3/glassfish/domains/liferay/applications/liferay611cega2/WEB-INF/classes/portal-ext.properties
...
# Shibboleth Config (Remember to install the Shibboleth plugin)

auto.login.hooks=it.infn.ct.security.shibboleth.ShibbolethAutoLogin,com.liferay.portal.security.auth.CASAutoLogin,com.liferay.portal.security.auth.FacebookAutoLogin,com.liferay.portal.security.auth.NtlmAutoLogin,com.liferay.portal.security.auth.OpenIdAutoLogin,com.liferay.portal.security.auth.OpenSSOAutoLogin,com.liferay.portal.security.auth.RememberMeAutoLogin,com.liferay.portal.security.auth.SiteMinderAutoLogin
auth.login.url=/c/portal/login

default.logout.page.path=/Shibboleth.sso/Logout
logout.events.post=com.liferay.portal.events.LogoutPostAction,it.infn.ct.security.shibboleth.ShibbolethLocalLogout
...
~~~~~~~~~
Finally insert the filter in web.xml

~~~~~~~~~
vim /opt/glassfish3/glassfish/domains/liferay/applications/liferay611cega2/WEB-INF/web.xml

...
 <filter>
      <filter-name>Shibboleth Filter</filter-name>
      <filter-class>it.infn.ct.security.shibboleth.filters.ShibbolethFilter</filter-class>
      <init-param>
           <param-name>auth_failure_redirect</param-name>
           <param-value>/not_authorised</param-value>
       </init-param>
  </filter>
  <filter-mapping>
       <filter-name>Shibboleth Filter</filter-name>
       <url-pattern>/c/portal/login</url-pattern>
       <dispatcher>REQUEST</dispatcher>
       <dispatcher>FORWARD</dispatcher>
  </filter-mapping>
...
~~~~~~~~~

# References
[https://wiki.shibboleth.net/confluence/display/SHIB2/MetadataForSP](https://wiki.shibboleth.net/confluence/display/SHIB2/MetadataForSP)
