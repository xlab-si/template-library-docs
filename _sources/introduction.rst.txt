.. _Introduction:

************
Introduction
************

.. _tps_mark_logo:

.. figure:: /images/other/xopera-tps-mark-black-text-mid.png
    :target: _images/xopera-tps-mark-black-text-mid.png
    :width: 40%
    :align: center

The Template Library is a place to manage your `OASIS TOSCA`_ orchestration modules (TOSCA templates), blueprints
(TOSCA CSARs) and publish the implementations (e.g. Ansible playbooks) of the modules. The organisation and managing of
the TOSCA content can be divided in three user scenarios:

.. tabs::

   .. tab:: Individual user

        User can manage the material by herself locally, but when she starts to collaborate with others, better tools
        are required as a repository or a special publishing tool.

   .. tab:: Community collaboration

      For a simple collaboration within the open communities and research groups an organised and
      online library needs to be used. One example of this kind of library is `RADON Particles`_, based on the GitHub
      repository. This open repository of TOSCA templates allows users to share, contribute to and use templates.

   .. tab:: Enterprise and advanced use

      Enterprise, advanced use, template publishing server is the next scenario. Advanced users build complex
      applications based on TOSCA models, therefore strict dependencies can tightly couple specific groups of modules.
      New versions of modules can result in new issues that make applications, based on them, defective. The goal of
      Template Library publishing service is to simplify the complexity of managing multiple versions of TOSCA
      templates and to serve as a publishing service that does not permit changes of published content and persuades
      users to organise each modification in new versions. Moreover this approach also allows to have a private content
      and sharing of the content inside groups. This is the basis for business model of selling model templates.

This documentation will focus on **publishing service** version of the application.

.. _Purpose and description:

Purpose and description
#######################

A Template Library is a service or shared repository that can hold the knowledge about:

- module templates (e.g. TOSCA templates)
- module implementations (e.g. Ansible playbooks behind TOSCA templates)
- blueprints (e.g. the whole application designs created from TOSCA CSARs)

.. _Functionalities:

Functionalities
***************

Template Library brings the following functionalities:

* User and group management:
    - registration
    - create groups
    - share content
* Search:
    - search over the content metadata
    - list content by categories
    - show basic info about TOSCA templates
    - display versions and developers/release notes 
* Published content, including:
    - Version
    - Description
    - content (zip, file, folder)
    - link to code (e.g. Github)

.. _Available services:

Available services
******************

All currently available Template Library's services and tools are:

- **Template Library GUI**: `template-library-xopera.xlab.si/`_
- **Template Library REST API**: `template-library-xopera.xlab.si/api/`_
- **Template Library REST API Swagger UI**: `template-library-xopera.xlab.si/swagger/`_
- **Template Library CLI**: `xopera-template-library`_
- **Template Library VS Code/Eclipse Che Theia plugin**: `template-library-vscode-plugin`_
- **Documentation**: `template-library-xopera.xlab.si/docs/`_

.. _Business value:

Business value
**************

The Template Library is a unique place for managing TOSCA modules, implementations and blueprints. Business value of
this tool is twofold. First, it is a valuable content broker between application developers and (Dev)Ops. Both groups
can version and publish their modules and applications and make them available through the Template Library service.
Second, Template Library equips users with the content for developing applications. This content could be public or on
the restricted access area, which makes open opportunities for the developers of the modules and applications to create
and offer a valuable content through the Template Library.

.. note::

    To prevent any possible confusions remember that Template Library (service) or its parts may also be called TPS
    (Template Publishing Service) or TLPS (Template Library Publishing Service).

.. _Template Library and xOpera SaaS overview:

Template Library and xOpera SaaS overview
*****************************************

The `xOpera`_ ecosystem includes tools that target optimizing deployment processes and reducing the human factor along
with a faster preparation of deployment scripts. The video presented in this blog points out the most crucial
functionalities of xOpera SaaS and TPS:

- Template Library Publishing Service (TPS) opens up a place for publishing, storing, managing, downloading and
  versioning of OASIS TOSCA modules and blueprints (i.e., TOSCA CSARs).
- Similar templates can be grouped together to form a FaaS abstraction layer such as a bundle of ready to use templates
  for deployment to cloud providers (e.g., AWS, Azure, GCP, OpenFaaS, etc.).
- Template groups in TPS can be used for connecting to corresponding groups of users and therefore enable working on
  different templates in a team and sharing them with other teams later.
- TPS brings different modes of interaction such as REST API, CLI client, browser-based GUI and Eclipse Che/VS Code
  plugin.
- Published deployment scripts in TPS can orchestrate the deployment with xOpera SaaS, which introduces a browser
  service for orchestration with a lightweight opera orchestrator compliant with OASIS TOSCA standard and powered by
  Ansible automation engine.
- Users can choose the corresponding templates and create a new project, secrets and credentials for deployment. Then
  they can deploy the application and observe the progress and status of the deployment.
- It is possible to organize multiple projects in multiple workspaces, manage provider credentials and assign them
  directly to workspaces. They can all run concurrently and users can even share the workspaces with other members.
- Apart from standard validation, deployment and un-deployment, xOpera SaaS also offers more complex orchestration
  actions such as redeployment, discovering template differences or invoking TOSCA policy triggers to enable vertical
  or horizontal scaling.
- The SaaS component is available through an API, GUI or Eclipse Che/VS Code plugin. The core part of the SaaS is the
  `opera`_ orchestrator, which is CLI and can be installed as a Python package from PyPI.

.. raw:: html

    <div style="text-align: center; margin-bottom: 2em;">
    <iframe width="100%" height="350" src="https://www.youtube.com/embed/0hpKJ_LBlk8" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
    </div>

The following videos show how Template Library and xOpera SaaS work in action:

- `TPS with CLI`_
- `TPS with Eclipse Che`_
- `xOpera SaaS with GUI`_
- `xOpera SaaS with Eclipse Che`_

.. hint::

    The blueprints stored in Template Library can be deployed with TOSCA orchestrator such as `xOpera`_ orchestrator
    called `opera`_, which can be installed as a Python package.

.. _OASIS TOSCA: https://www.oasis-open.org/committees/tc_home.php?wg_abbrev=tosca
.. _RADON Particles: https://github.com/radon-h2020/radon-particles
.. _template-library-xopera.xlab.si/: https://template-library-xopera.xlab.si/
.. _template-library-xopera.xlab.si/api/: https://template-library-xopera.xlab.si/api/
.. _template-library-xopera.xlab.si/swagger/: https://template-library-xopera.xlab.si/swagger/
.. _xopera-template-library: https://pypi.org/project/xopera-template-library
.. _template-library-xopera.xlab.si/docs/: https://template-library-xopera.xlab.si/docs/
.. _template-library-vscode-plugin: https://github.com/xlab-si/template-library-vscode-plugin
.. _opera: https://pypi.org/project/opera/
.. _xOpera: https://xlab-si.github.io/xopera-docs/
.. _TPS with CLI: https://youtu.be/28eTwojw5ac
.. _TPS with Eclipse Che: https://youtu.be/vCjfZ4Iue0E
.. _xOpera SaaS with GUI: https://youtu.be/T4XviKWLc-A
.. _xOpera SaaS with Eclipse Che: https://youtu.be/SIiLOe5dSqc
