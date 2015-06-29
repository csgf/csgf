Catania Science Gateway Framework Documentation
===============================================

.. _GitHub: https://github.com/csgf/
The CSGF is open source and released under the Apache 2.0 license. All code is available on GitHub_


The documentation is organized in the following sections:


* :ref:`intro-docs`
	This section provides an introduction on the CSGF, including its global architecture.
* :ref:`insta-docs`
	This section provides a step-by-step guide to install and configure a server hosting a CSGF-based Science Gateway.
* :ref:`core-docs`
	This section includes the documentation of all the core services of the CSGF.
* :ref:`web-docs`
	This section includes the documentation of all the web applications that have been integrated in the Science Gateways powered by the CSGF.
* :ref:`mobile-docs`
	This section includes the documentation of the apps for mobile appliances which are part of the CSGF.
* :ref:`api-docs`
	This section includes the documentation of the APIs written to use some of the CSGF services.
* :ref:`training-docs`
	This section contains a collection of training materials for developers, including the instructions about how to setup the CSGF development environment.

.. _intro-docs:
.. toctree::
	:maxdepth: 2
	:caption: INTRODUCTION

	introduction/index


.. _insta-docs:
.. toctree::
	:maxdepth: 2
	:caption: INSTALLATION AND CONFIGURATION

	installation-configuration/docs/index


.. _core-docs:
.. toctree::
	:maxdepth: 4
	:caption: CORE SERVICES

	application-registry-portlet/docs/index
	eToken/docs/index
	federated-login-ext/docs/index
	glibrary/docs/index
        grid-and-cloud-engine/docs/index
	jsaga-adaptor-rocci/docs/index
	myjobs-portlet/docs/index
	OpenIdConnectLiferay/docs/index
	sgmon/docs/index


.. _web-docs:
.. toctree::
	:maxdepth: 4
	:caption: WEB APPLICATIONS

        abinitdm-portlet/docs/index
        aleph-portlet/docs/index
        astra-portlet/docs/index
        cloudapps-portlet/docs/index
        clustalw-portlet/docs/index
        corsika-lago-portlet/docs/index
        iort-portlet/docs/index
        knowledge-base-portlet/docs/index
        mpi-portlet/docs/index
        mi-parallel-portlet/docs/index
        mycloud-portlet/docs/index
        nuclemd-portlet/docs/index
        octave2-portlet/docs/index
        parallel-semantic-search-portlet/docs/index
        picalc-portlet/docs/index
        r-portlet/docs/index
        semantic-search-portlet/docs/index
        sonification-portlet/docs/index
        trodan-portlet/docs/index
        wrf-portlet/docs/index



.. _mobile-docs:
.. toctree::
	:maxdepth: 2
	:caption: MOBILE APPS

	aginfra-sg-mobile/docs/index
	dchrp-ecsg-mobile/docs/index
	earthserver-sg-mobile/docs/index
	semantic-search-app/docs/index


.. _api-docs:
.. toctree::
	:maxdepth: 2
	:caption: API SERVICES

	scoap3-harvester-api/docs/index
	semantic-search-api/docs/index


.. _training-docs:
.. toctree::
	:maxdepth: 2
	:caption: TRAINING MATERIALS

	training-materials/docs/index
