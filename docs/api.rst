.. _API:

*************************
Template Library REST API
*************************

This section explains usage of the Template Library REST API.
Template Library REST API is written in Kotlin programming language using KTOR framework and provides several endpoints
to access Template Library's data from storage.

Public access
**************

REST API is currently accessible on `template-library-xopera.xlab.si/api/`_.

.. _REST API design:

REST API design
***************

REST API Swagger design is shown on the image below (:numref:`rest-api-design`).

.. _rest-api-design:

.. figure:: /images/api/rest-api.png
    :target: _images/rest-api.png
    :width: 100%
    :align: center

    Template Library REST API design.

Endpoints are linked into bigger groups that are explained in a table below:

+-----------------+--------------------------------------------+
| Endpoint group  | Purpose and description                    |
+=================+============================================+
| public          | Publicly available endpoints               |
+-----------------+--------------------------------------------+
| users           | Operations about users                     |
+-----------------+--------------------------------------------+
| userGroups      | Operations about groups of users           |
+-----------------+--------------------------------------------+
| templateGroups  | Operations about groups of templates       |
+-----------------+--------------------------------------------+
| templateTypes   | Operations for TOSCA template types        |
+-----------------+--------------------------------------------+
| templates       | Access to TOSCA templates                  |
+-----------------+--------------------------------------------+

Mostly the requests include JSON objects for the transmission of the data.
With some requests you can also use multipart objects.

.. _Swagger UI web interface:

Swagger UI web interface
************************

For better user experience and testing the REST API has its own OpenAPI specification and can be therefore publicly
accessed through Swagger UI on `template-library-xopera.xlab.si/swagger/`_.
This part will help you to interact with API easily since all the endpoints can be accessed from here.

.. _REST API usage:

REST API usage
**************

This part explains the usage of the Template Library REST API.

.. _Public access:

Public access
=============

To give users some idea on how Template Library works without having to create an account and login, we have opened
some endpoints (with GET requests) publicly.
These endpoints start with */public* prefix and can be accessed from anywhere (e.g. from console with curl or from
Swagger UI and so on).

.. _User login and authentication:

User login and authentication
=============================

Template Library REST API authentication goes through Open Source Identity and Access Management called `Keycloak`_.
Keycloak instance is accessible at `openid-xopera.xlab.si`_.
Apart from XLAB Keycloak users, there we also connected other identity providers that have their own users such as
RADON Keycloak users (go to `openid-xopera.xlab.si/auth/realms/keycloak`_
(or `openid-xopera.xlab.si/auth/realms/keycloak`_), click on button :guilabel:`RADON authentication` and you will be
redirected to RADON Eclipse Che instance where you can log in).

Template Library REST API is integrated with Keycloak(Eclipse Che) authentication.
So, if try to initiate a request (for instance on ``api/users`` from browser address bar) targeting any the of REST API
endpoints you need to be logged in into your Keycloak account.
If you are not logged in, you will be redirected to the url where you can log in with your Keycloak user credentials.
After that you will be redirected back to the REST API endpoint the request was initiated from.
Then you will be able to use all REST API endpoints manually or from Eclipse Che.

.. _keycloak-auth:

.. figure:: /images/api/keycloak-auth.png
    :target: _images/keycloak-auth.png
    :width: 100%
    :align: center

    Keycloak auth redirection.

.. _keycloak-auth2:

.. figure:: /images/api/keycloak-auth2.png
    :target: _images/keycloak-auth2.png
    :width: 100%
    :align: center

    Keycloak authentication including identity providers.

You can retrieve the current user data with GET request on ``api/users/current`` and logout from Template Library you
can use the ``api/users/current/logout`` endpoint.

.. note::

    Keycloak redirection will probably not work at first if you try to send the request from the Swagger UI
    so rather use browser address bar or for instance curl from Eclipse Che shell script when accessing the API for the
    first time.
    After the first login the Keycloak cookies will be stored into browser and you will be able to use Swagger UI as
    well.

.. _Users, templates and groups:

Users, templates and groups
===========================

So now, when you are authorized, you can proceed with your actions.
The logical workflow would feature creating a new group at first which will be a structure where you will gather you
templates and connect them with users who will be able to add their own templates to the group.
To create a new group of users use a POST request on ``/userGroups``.
You will have to provide the following params in the request:

+----------------+--------------------------+
| Parameter      | Description              |
+================+==========================+
| name           | Unique name of the group |
+----------------+--------------------------+
| description    | Group description        |
+----------------+--------------------------+

After the group is created, the next step would be to add users to the group.
To do that use the ``/users/{username}/groups`` endpoint and fill in the properties below (currently only group owners
can add new users, ``groupId`` and ``groupName`` params are mutually exclusive):

+----------------+-------------------------------------------+
| Parameter      | Description                               |
+================+===========================================+
| username       | Username (find it using other requests)   |
+----------------+-------------------------------------------+
| groupId        | Group id (find it using other requests)   |
+----------------+-------------------------------------------+
| groupName      | Group name (find it using other requests) |
+----------------+-------------------------------------------+

After that action user is a part of the group and he will be able to see all the templates from all the linked template
groups.

.. note::

    All REST API plain text parameters that you send within you request have various input checks and limitations
    in characters and length.
    This includes names, passwords and files.

Template Library sees templates as entities marked mainly with TOSCA template files.
The all idea behind that is that TOSCA standard is very complex and consists of several definitions and types (nodes,
relationships, capabilities and so on) so instead of putting all those definitions into one giant service template file
it is much nicer to dismantle your large TOSCA blueprint to pieces or models.
Ideally each model is actually a small TOSCA template or `TOSCA CSAR`_ with only one relevant type (e.g node type)
defined.
The (TOSCA) template types are one of the crucial things that allow us to distinguish modules thoroughly.
The types used in Template Library are shown in the table below and can also be fetched through ``/templateTypes``
endpoint.

+----------------+-----------------------------------------------------------+
| Template type  | Purpose and description                                   |
+================+===========================================================+
| data           | TOSCA data type                                           |
+----------------+-----------------------------------------------------------+
| artifact       | TOSCA artifact type                                       |
+----------------+-----------------------------------------------------------+
| capability     | TOSCA capability type                                     |
+----------------+-----------------------------------------------------------+
| requirement    | TOSCA requirement type                                    |
+----------------+-----------------------------------------------------------+
| relationship   | TOSCA relationship type                                   |
+----------------+-----------------------------------------------------------+
| interface      | TOSCA interface type                                      |
+----------------+-----------------------------------------------------------+
| node           | TOSCA node type                                           |
+----------------+-----------------------------------------------------------+
| group          | TOSCA group type                                          |
+----------------+-----------------------------------------------------------+
| policy         | TOSCA policy type                                         |
+----------------+-----------------------------------------------------------+
|| blueprint     || Full application blueprint (usually a zip archive or a   |
||               || compressed `TOSCA CSAR`_)                                |
+----------------+-----------------------------------------------------------+
| other          | Other definitions                                         |
+----------------+-----------------------------------------------------------+

.. note::

    Your modules can include a lot of files - for instance TOSCA templates, Ansible playbooks, configuration files and
    so on so keep in mind that you will have to upload them as a compressed archive (like zip or tar).
    We recommend you to use `TOSCA CSAR`_ structure and then pack your modules to a zip file, which you will upload to
    TPS.

So, as you can see from the table you will have to pick the suitable type.
You can even save the whole application blueprints (`TOSCA CSAR`_ files) for the cases when you have prepared you
solution and it cannot be simply divided into smaller pieces.
The module with template also should be linked to its implementations which are the actual actuators for different
automation tools (i.e. Ansible playbooks, Chef recipes, Puppet scripts and so on) that are executed by the TOSCA
orchestrator (such as ``opera``).
In this perspective module could be for instance AWS S3 bucket with very simple TOSCA template that would include just
the node type for the bucket like in the TOSCA ``service.yml`` template below.

.. code-block:: yaml

    ---
    tosca_definitions_version: tosca_simple_yaml_1_3

    node_types:
      radon.nodes.s3_bucket:
        derived_from: tosca.nodes.SoftwareComponent
        properties:
          bucket_name:
            type: string
            description: The name of the bucket
          aws_region:
            type: string
            description: AWS region
        interfaces:
          Standard:
            type: tosca.interfaces.node.lifecycle.Standard
            inputs:
              bucket_name: { default: { get_property: [SELF, bucket_name] }, type: string }
              aws_region: { default: { get_property: [SELF, aws_region] }, type: string }
            operations:
              create: playbooks/create.yml
              delete: playbooks/delete.yml
    ...

The template also has a link to two implementations in the form of Ansible playbooks which are also part of the module.
The first one is the ``create.yml`` used for the create TOSCA operation and is shown below.

.. code-block:: yaml

    ---
    - hosts: all
      gather_facts: no
      tasks:
        - name: "Create AWS S3 bucket {{ bucket_name }}"
          s3_bucket:
            name: "{{ bucket_name }}"
            state: present
            region: "{{ aws_region }}"
    ...

The second playbook (``delete.yml``, see below) is for the delete TOSCA operation and could be used for undeployment.

.. code-block:: yaml

    ---
    - hosts: all
      gather_facts: no
      tasks:
        - name: "Remove S3 bucket {{ bucket_name }}"
          s3_bucket:
            name: "{{ bucket_name }}"
            region: "{{ aws_region }}"
            state: absent
            force: yes
    ...

So, these three files basically depict a module (which is basically a compressed `TOSCA CSAR`_ or zip archive).
Along with the metadata the module definition is complete.
So, if you want to add your new module to the Template Library you should use ``/templates`` endpoint and provide the
following information:

+--------------------+--------------------------------------------------+
| Parameter          | Description                                      |
+====================+==================================================+
| name               | Short module name (e.g. aws_bucket)              |
+--------------------+--------------------------------------------------+
| description        | Module description (e.g. AWS bucket node type)   |
+--------------------+--------------------------------------------------+
| templateTypeName   | Template type unique name identifier (e.g. node) |
+--------------------+--------------------------------------------------+
|| publicAccess      || Specify if module should be private/public      |
||                   || (private templates are only visible in groups)  |
+--------------------+--------------------------------------------------+

After that your template - module has been added to the Template Library.
You can see that there is no space to upload your module archive (i.e. your TOSCA YAML template files, their
implementations - e.g. Ansible playbooks and all the other accompanying) in the request above.
That is because you will add your files later on when creating a new version of your module.
The next step you can take is to add your module to the proper group (if it already exists, otherwise you will have to
create it by yourself).
So if we turn back to the simple example of ``aws_bucket`` module this would probably fit in the group called AWS or
something similar.

To create a new group of templates use a POST request on ``/templateGroups`` where you will have to provide the
following params in the request:

+--------------------+--------------------------------------------------+
| Parameter          | Description                                      |
+====================+==================================================+
| name               | Unique name of the group                         |
+--------------------+--------------------------------------------------+
| description        | Group description                                |
+--------------------+--------------------------------------------------+
|| publicAccess      || Specify if group should be private/public       |
||                   || (private groups aren't visible for other users) |
+--------------------+--------------------------------------------------+

Template groups are meant to serve as an abstraction layer for grouping together the connected templates (AWS, Azure,
GCP, OpenFaas, etc.).
Private template groups can contain private or public templates and are accessed by giving access to a specific user
group, whereas public template groups can contain only public templates and can be accessed by everyone.

To add template to the created template group use ``/template/{templateName}/groups`` endpoint and provide the request
params below (currently only group owners can add new templates) where ``groupId`` and ``groupName`` params are
mutually exclusive:

+----------------+-------------------------------------------+
| Parameter      | Description                               |
+================+===========================================+
| templateName   | Template name                             |
+----------------+-------------------------------------------+
| groupId        | Group id (find it using other requests)   |
+----------------+-------------------------------------------+
| groupName      | Group name (find it using other requests) |
+----------------+-------------------------------------------+

.. _Template group access:

Template group access
=====================

To be able to share private templates with other users it is possible to link groups of users to the groups of templates
(currently it is not possible to grant access for a single user to a single template).

To add access to templates fot the whole user group use
``/userGroups/{userGroupName}/templateGroup/{templateGroupName}`` REST API endpoint and provide the following
params:

+---------------------+------------------------------------------------------+
| Parameter           | Description                                          |
+=====================+======================================================+
| userGroupName       | Name of the user group                               |
+---------------------+------------------------------------------------------+
| templateGroupName   | Name of the template group                           |
+---------------------+------------------------------------------------------+

When template group is linked to user group its templates can be accessed by all group users even if they are private.
If the template is public anyone can access it so if you want to keep your template just for yourself set its access to
private and keep it away from any template groups.

.. _Versioning:

Versioning
==========

Modules as TOSCA templates can change through time.
You can modify TOSCA YAML profile version, add some new properties, attributes or inputs, you can provide new
implementations (Ansible playbooks) or completely change the TOSCA template.
To realize that and to save older features module templates are viewed through versions.
Each version of module is unique and immutable and therefore previous versions cannot be changed.
This idea of everlasting module versions originates from the Python Package Index (PyPI) which was our inspiration when
designing the Template Library solution.
In PyPI every Python package version is fixed.
Even if some older versions of the package are dormant and are not being used anymore they are still present since you
never now when you want to relive your past, go back and maybe resuscitate some forgotten features.

To add your new version of the template send a POST request on the ``/templates/{templateName}/versions/`` endpoint
and provide the request attributes below:

+---------------------+-----------------------------------------------------+
| Parameter           | Description                                         |
+=====================+=====================================================+
| templateName        | Name of the existing template                       |
+---------------------+-----------------------------------------------------+
| versionName         | Unique semantic template version name               |
+---------------------+-----------------------------------------------------+
| readmeFile          | Optional markdown README.md file with instructions  |
+---------------------+-----------------------------------------------------+
| templateFile        | Compressed module files or TOSCA YAML template file |
+---------------------+-----------------------------------------------------+

After that your version is added and you can download it using ``/versions/{versionName}/templateFile`` endpoint.

.. note::

    POST request on ``/templates/{templateName}/versions/`` has a body that includes multipart form data type.
    So when using with ``curl`` and with other programming languages be careful.
    The ``templateFile`` parameter should be an archive (like zip or tar) with your module files or a single TOSCA YAML
    template.

To retrieve version info send a GET request on the ``/templates/{templateName}/versions/{versionName}`` endpoint.
To retrieve all version files use GET request on ``/templates/{templateName}/versions/{versionName}/templateFile``.
This will give your compressed template file.
The response you get is visible in the image below :numref:`api-file-response` (or you can explore downloadable example
here: :download:`AwsBucket_0.1.0.zip </files/AwsBucket_0.1.0.zip>`).

.. _api-file-response:

.. figure:: /images/api/api-file-response.png
    :target: _images/api-file-response.png
    :width: 70%
    :align: center

    REST API Swagger UI download file response.

.. hint::

    Use GET request on ``/templates/{templateName}/versions/{versionName}/readme`` to retrieve README file for a
    specific template version.

.. _Example with curl:

Example with curl
=================

The following code block shows an example of using the Template Library API through ``curl``:

.. code-block:: bash

    # variables
    api="https://template-library-xopera.xlab.si/api"
    auth_base_url="https://openid-xopera.xlab.si"

    # public Template Library endpoints can be used directly with curl without authentication
    curl -X GET "$api/public/templateTypes"
    curl -X GET "$api/public/templates"
    curl -X GET "$api/public/templates/filter"
    curl -X GET "$api/public/templates/{templateName}/versions"
    curl -X GET "$api/public/templates/{templateName}/versions/{versionName}"
    curl -X GET "$api/public/templates/{templateName}/versions/{versionName}/templateFile"
    curl -X GET "$api/public/templates/{templateName}/versions/{versionName}/readme"

    # using secured endpoints requires Keycloak authentication
    your_username=YOUR_USERNAME
    your_password=YOUR_PASSWORD

    # set cookiecurl and choose your auth method below
    alias cookiecurl="curl -sSL --cookie-jar cookiejar.txt --cookie cookiejar.txt"
    response_from_credentials_redirected_to_next_auth="$(cookiecurl $api/users/current)"

    # a) login flow - internal Keycloak auth (XLAB)
    redirect_url_internal="$(echo $response_from_credentials_redirected_to_next_auth | xmllint --html --xpath "string(//form[@id='kc-form-login']/@action)" - 2>/dev/null)"
    redirect_url="$redirect_url_internal"

    # b) login flow - RADON Keycloak auth (ENG)
    redirect_url_to_radonauth="$(echo $response_from_credentials_redirected_to_next_auth | xmllint --html --xpath "string(//a[@id='social-keycloak-oidc-provider-to-radon-eng-2']/@href)" - 2>/dev/null)"
    response_radonauth="$(cookiecurl ${auth_base_url}${redirect_url_to_radonauth})"
    login_url_radonauth="$(echo $response_radonauth | xmllint --html --xpath "string(//form[@id='kc-form-login']/@action)" - 2>/dev/null)"
    cookiecurl "$login_url_radonauth" -d "username=$your_username" -d "password=$your_password" -d credentialId=""
    redirect_url="$redirect_url_radonauth"

    # c) login flow - RADON Keycloak auth (Azure)
    redirect_url_to_radonauth="$(echo $response_from_credentials_redirected_to_next_auth | xmllint --html --xpath "string(//a[@id='social-keycloak-oidc-provider-to-radon-azure']/@href)" - 2>/dev/null)"
    response_radonauth="$(cookiecurl ${auth_base_url}${redirect_url_to_radonauth})"
    login_url_radonauth="$(echo $response_radonauth | xmllint --html --xpath "string(//form[@id='kc-form-login']/@action)" - 2>/dev/null)"
    cookiecurl "$login_url_radonauth" -d "username=$your_username" -d "password=$your_password" -d credentialId=""
    redirect_url="$redirect_url_radonauth"

    # final login step, sets cookies and automatically completes the /users/current request
    cookiecurl "$redirect_url" -d "username=$your_username" -d "password=$your_password" -d credentialId=""

    # TPS requires you to be mindful
    cookiecurl "$api/auth/consent" -XPOST -d "{\"iAcknowledgePotentialDataLossAndAmAwareOfAllRisks\": true}"

    # usage of the secured TPS API endpoints
    cookiecurl -X GET "$api/templateTypes"
    cookiecurl -X GET "$api/templates"
    cookiecurl -X GET "$api/templates/filter"
    cookiecurl -X GET "$api/templates/{templateName}/versions"
    cookiecurl -X GET "$api/templates/{templateName}/versions/{versionName}"
    cookiecurl -X GET "$api/templates/{templateName}/versions/{versionName}/templateFile"
    cookiecurl -X GET "$api/templates/{templateName}/versions/{versionName}/readme"
    cookiecurl -X POST "$api/templates" -d "{\"name\": \"MyTemplate\", \"description\": \"AWS blueprint\", \"templateTypeName\": \"blueprint\", \"publicAccess\": true}"
    cookiecurl -X POST "$api/userGroups" -d "{\"name\": \"MyUserGroup\", \"description\": \"My users\"}"
    cookiecurl -X POST "$api/templateGroups" -d "{\"name\": \"MyTemplateGroup\", \"description\": \"My TOSCA templates\", \"publicAccess\": true}"
    cookiecurl -X PUT "$api/templates" -d "{\"name\": \"MyUpdatedTemplate\", \"description\": \"AWS blueprint\", \"templateTypeName\": \"blueprint\", \"publicAccess\": false}"
    cookiecurl -X PUT "$api/userGroups" -d "{\"name\": \"MyUpdatedUserGroup\", \"description\": \"My updated users\"}"
    cookiecurl -X PUT "$api/templateGroups" -d "{\"name\": \"MyUpdatedTemplateGroup\", \"description\": \"My updated TOSCA templates\", \"publicAccess\": false}"
    cookiecurl -X DELETE "$api/templates?templateName={templateName}'"
    cookiecurl -X DELETE "$api/userGroups?groupName={userGroupName}'"userGroupName
    cookiecurl -X DELETE "$api/templateGroups?groupName={templateGroupName}'"

.. _template-library-xopera.xlab.si/api/: https://template-library-xopera.xlab.si/api/
.. _template-library-xopera.xlab.si/swagger/: https://template-library-xopera.xlab.si/swagger/
.. _openid-xopera.xlab.si: https://openid-xopera.xlab.si
.. _openid-xopera.xlab.si/auth/realms/keycloak: https://openid-xopera.xlab.si/auth/realms/keycloak
.. _template-library-xopera.xlab.si/api/auth/register: https://template-library-xopera.xlab.si/api/auth/register
.. _template-library-xopera.xlab.si/api/users: https://template-library-xopera.xlab.si/api/users
.. _TOSCA CSAR: https://docs.oasis-open.org/tosca/TOSCA-Simple-Profile-YAML/v1.3/cos01/TOSCA-Simple-Profile-YAML-v1.3-cos01.html#_Toc26969474
.. _Keycloak: https://www.keycloak.org/
