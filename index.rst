Catania Science Gateway Framework Documentation
===============================================

.. _GITHUB: https://github.com/csgf/

## About 

The [Catania Science Gateway Framework](http://www.catania-science-gateways.it/) is a standards-compliant framework for building science gateways. 

## License and Availability

The CSGF is open source and released under the Apache 2.0 license. All code is available on GITHUB_

The documentation is organized in the following sections:

* Introduction (in progress)
   Backgroudn information on the framework and the e-Infrastructure environment which it is built for
* :ref:`insta-docs`
   How to install various services, portlets and libraries
* :ref:`core-docs`
   Documentation of the core services which enable the interoperability of the framework with other components as well as e-Science infratructures.
* :ref:`app-docs`
   Documentation of the respective application portlets which are supported
* :ref:`mobile-docs`
   Documentation of the mobile interfaces to various applications, APIs and framework components
* :ref:`api-docs`
   Documentation of the framework APIs and examples of how to build your own services with them.
* :ref:`training-docs`
   Learn to use and build applications with our self-paced training materials. 


.. _insta-docs:
.. toctree::
   :maxdepth: 2
   :caption: Installation and configuration
   
   installation-configuration/docs/index   
 
.. _core-docs:
.. toctree::
   :maxdepth: 4
   :caption: Core Services

   eToken/docs/index
   glibrary/docs/index
   jsaga-adaptor-rocci/docs/index
   federated-login-ext/docs/index
   application-registry-portlet/docs/index
   myjobs-portlet/docs/index
   sgmon/docs/index  


.. _app-docs:
.. toctree::
   :maxdepth: 2
   :caption: Web Applications

   mpi-portlet/docs/index
   mi-parallel-portlet/docs/index
   abinitdm-portlet/docs/index 
   octave2-portlet/docs/index
   r-portlet/docs/index
   wrf-portlet/docs/index
   aleph-portlet/docs/index
   corsika-lago-portlet/docs/index
   nuclemd-portlet/docs/index
   clustalw-portlet/docs/index
   picalc-portlet/docs/index
   trodan-portlet/docs/index
   iort-portlet/docs/index
   cloudapps-portlet/docs/index
   mycloud-portlet/docs/index
   astra-portlet/docs/index 
   sonification-portlet/docs/index
   semantic-search-portlet/docs/index
   parallel-semantic-search-portlet/docs/index
   knowledge-base-portlet/docs/index


.. _mobile-docs:
.. toctree::
   :maxdepth: 2
   :caption: Mobile Apps

   semantic-search-app/docs/index
   dchrp-ecsg-mobile/docs/index
   earthserver-sg-mobile/docs/index

.. _api-docs:
.. toctree::
   :maxdepth: 2
   :caption: API Services

   scoap3-harvester-api/docs/index
   semantic-search-api/docs/index


.. _training-docs:
.. toctree::
   :maxdepth: 2
   :caption: Training materials
 
   training-materials/docs/index
