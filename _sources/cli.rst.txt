.. _CLI:

********************
Template Library CLI
********************

This section explains the installation and the usage of the Template Library Command Line Interface (CLI).

.. _CLI prerequisites:

Prerequisites
#############

Template Library CLI requires python 3 (and a virtual environment).
In a typical modern Linux environment, we should already be set.
In Ubuntu, however, we might need to run the following commands:

.. code-block:: console

    $ sudo apt update
    $ sudo apt install -y python3-venv python3-wheel python-wheel-common

.. _CLI installation:

Installation
############

The only thing that needs to be installed if you wish to use Template Library from the command line is the Template
library CLI.
The CLI tool goes by the name `xopera-template-library`_ and you can install it as a Python package from `PyPI`_ with
the command below (The simplest way to test the package is to install it into a virtual environment):

.. code-block:: console

    $ python3 -m venv .venv && . .venv/bin/activate
    $ pip install xopera-template-library

.. note::

    `xopera-template-library`_ is a Python pip package so it is published on Python Package Index (`PyPI`_) where you
    can find more information about this package versions and updates.
    There is also a development releases available at `Test PyPI`_ instance that are released some time before
    production release.

.. _CLI usage:

Usage
#####

Template Library CLI is a client tool for managing templates, blueprints and their versions.
It is here to save you from executing complex curl requests and will offer you to easily access and operate with the
Template Library.

.. code-block:: console

    $ xopera-template-library template -h
    usage: xopera-template-library [-h] [-v]
                                   {template,template-group,user-group,user,setup,login,logout}
                                   ...

    positional arguments:
      {template,template-group,user-group,user,setup,login,logout}
        template            Template actions.
        template-group      Actions for groups of templates.
        user-group          Actions for groups of users.
        user                User info.
        setup               Setup client variables.
        login               Login to your account.
        logout              Logout of your account.

    optional arguments:
      -h, --help            show this help message and exit
      -v, --verbose         Increase output verbosity

.. tip::

    At any point the ``-h/--help`` flag is available to display proceeding options.
    There is also ``-v/--verbose`` flag that will turn on the debug mode to see more output.

The next sections will walk you through all Template Library CLI commands and their functions.

.. _CLI setup:

Setup
*****

The ``setup`` command allows users to configure the API endpoint and is the first step of Template Library CLI use.

Before you begin using ``xopera-template-library`` tool you have to make sure that Template Library REST API endpoint
and Keycloak auth endpoint are properly configured.
This can be done with ``setup`` command where you will be able to change the default values that are:

.. code-block:: python

    # default REST API endpoint
    REST_API_ENDPOINT = "https://template-library-xopera.xlab.si/api"
    # default Keycloak endpoint
    KEYCLOAK_ENDPOINT = "https://openid-xopera.xlab.si"

Here's the example:

.. code-block:: console

    $ xopera-template-library setup --keycloak
    Current Template Library REST API endpoint: https://template-library-xopera.xlab.si/api
    Modify API endpoint (press enter to keep the current):
    Template Library REST API endpoint has been set to: https://template-library-xopera.xlab.si/api.

    Current Keycloak auth endpoint: https://openid-xopera.xlab.si
    Modify Keycloak endpoint (press enter to keep the current):
    Keycloak endpoint has been set to: https://openid-xopera.xlab.si.

If you run only ``xopera-template-library setup`` you will be able to modify only the REST API endpoint.
You have to use ``--keycloak`` flag to tell the CLI that you will modify Keycloak auth endpoint too.

.. _CLI auth:

Auth (login and logout)
***********************

To use the CLI you will need an account for Template Library.

Before invoking any Template Library actions you need to login with your account, which can be created in using Template
library API (:ref:`API`) using the special http basic auth credentials.
Another way of having an account is using the XLAB Keycloak instance (accessible at `openid-xopera.xlab.si`_).

So, with Template Library CLI you can login as:

- XLAB Keycloak user (default)
- other Keycloak user from Keycloak identity providers such as RADON
  (use `--identity-provider IDENTITY_PROVIDER_ID` flag)

Here's the example:

.. code-block:: console

    # login as XLAB Keycloak user, with prompts
    $ xopera-template-library login
    Username: username1
    Password: *******
    User 'username1' logged in.

    # login as XLAB Keycloak user, prompt for password
    $ xopera-template-library login --username "username1"
    Password: *******
    User 'username1' logged in.

    # login as XLAB Keycloak user, skip prompts
    $ xopera-template-library login --username "username1" --password "password1"
    User 'username1' logged in.

    # login as Keycloak user from one of Keycloak identity providers (RADON Azure)
    # use this when you know the ID of your identity provider (--identity-provider IDENTITY_PROVIDER_ID)
    $ xopera-template-library login --identity-provider zocial-keycloak-xlab-oidc-provider-to-keycloak-radon-azure
    Using Keycloak identity provider zocial-keycloak-xlab-oidc-provider-to-keycloak-radon-azure (Log in with RADON credentials (Azure)).
    Username: username1
    Password: *******
    User 'username1' logged in.

    # login as Keycloak user from one of Keycloak identity providers (RADON ENG)
    # if you do not know the ID of the provider you can leave out the ID and the
    # available providers will be listed and you will be able to choose one for authentication.
    $ xopera-template-library login --identity-provider
    Keycloak identity provider was not specified or is not available. You can pick your identity provider in the next steps.
    Do you want to continue? (Y/n):
    +List of available Keycloak identity providers--------------------------------------+---------------------------------------------+
    | Provider number | Provider id                                                     | Provider description                        |
    +-----------------+-----------------------------------------------------------------+---------------------------------------------+
    | 1               | social-keycloak-xlab-oidc-provider-to-keycloak-radon            | Log in with RADON credentials (ENG)         |
    | 2               | social-keycloak-xlab-oidc-provider-to-keycloak-radon-azure      | Log in with RADON credentials (Azure)       |
    +-----------------+-----------------------------------------------------------------+---------------------------------------------+
    Type in the valid provider number from the table above: 1
    Using Keycloak identity provider social-keycloak-xlab-oidc-provider-to-keycloak-radon (Log in with RADON credentials (ENG)).
    Username: username2
    Password: *******
    User 'username2' logged in.

The following arguments are required but you will be prompted by an input if missing: ``--username``, ``--password``.

Logging out removes all user data.

Here's the example:

.. code-block:: console

    $ xopera-template-library logout
    User 'username1' logged out.

.. danger::

    Be careful not to expose your Keycloak username and password. Rather type in your password in prompt without
    using the ``--password`` flag.

.. _CLI templates:

Templates
*********

Templates can be uploaded with ``save`` and downloaded with ``get`` command.
User can also list public and private templates according to name and view version information.

.. code-block:: console

    $ xopera-template-library template
    positional arguments:
      {create,save,get,list,version,list-groups}
        create              Initialize a template directory and files.
        save                Save a template to database.
        get                 Get a template from database.
        list                List templates from database.
        version             List versions of a template from database.
        list-groups         List template groups the template is in.

    optional arguments:
      -h, --help            show this help message and exit

.. _Create model and create blueprint:

Create model and create blueprint
*********************************

The ``template create`` command generates all directories and files that are needed to upload a Template Library model.
After generating a model (TOSCA template) or a blueprint (`TOSCA CSAR`_) user can add in his own code.
You will be asked for your template's name and type.
Possible types are `data`, `artifact`, `capability`, `requirement`, `relationship`, `interface`, `node`, `group`,
`policy`, `csar` and `other`.

So, when using the ``xopera-template-library template create`` CLI command this will automatically create the structure
for your model (model will be created in current directory in a folder that has the same name as your model).
If your model is just a basic TOSCA entity (e.g. node, relationship, etc.) the generated directory will contain the
following files:

.. code-block:: console

    $ xopera-template-library template create
    Template type: node
    Template name: node
    Model 'node' created in ./node.

    $ tree node
    node
    ├── files
    │   ├── create.yml
    │   └── delete.yml
    ├── NodeType.tosca
    └── README.md

And if you decide to create a blueprint which should be a valid `TOSCA CSAR`_, you can do the following:

.. code-block:: console

    $ xopera-template-library template create --type csar --name csar
    Blueprint (TOSCA CSAR) 'csar' created in ./csar.

    $ tree csar
    csar
    ├── files
    │   ├── create.yml
    │   └── delete.yml
    ├── README.md
    ├── ServiceTemplate.tosca
    └── TOSCA-Metadata
        └── TOSCA.meta

After the basic files are generated you can edit them and upload your template to the library (you can use ``save``
CLI command).

.. note::

    The generated structure is similar to `RADON particles`_ and is compatible with `Eclipse Winery`_ modelling tool.

.. _CLI save template:

Save template
=============

To save a template user needs to provide a unique name of template, template's privacy (only the user that added a
private template can manage it), and version of the template (by default templates are private, for publishing your
template publicly, add ``--public`` flag.).

Here's the example:

.. code-block:: console

    # upload a public template to Template Library (without README)
    $ xopera-template-library template save --name AwsLambda --path example/AwsLambdaFunction --version 0.0.1 --public
    Path to README file (optional, can be left empty):
    Description of the template: Testing TPS CLI TOSCA node upload
    Template has been added successfully!
    Version insertion was successful!

    # save a private CSAR to Template Library (with README)
    $ xopera-template-library template save --name TestCSAR --path examples/csar --readme examples/README.md --version 0.0.1
    Description of the template: Testing TPS CLI TOSCA CSAR upload
    Template has been added successfully!
    Version insertion was successful!

The following arguments are required: ``--name``, ``--path``, ``--version``. If ``--public`` flag is present, the
template will be marked as public.

.. _Get template:

Get template
============

A template can be downloaded from the database by its name.
User also has to define the path where downloaded template directory will save.

See the next example:

.. code-block:: console

    $ xopera-template-library template get --name AwsLambda --path AwsLambda
    Downloaded AwsLambda version 0.1.0 to 'AwsLambda'.

    $ xopera-template-library template get --name DemoBlueprintOpenFaaS --path DemoBlueprintOpenFaaS --version 0.5.0
    Downloaded DemoBlueprintOpenFaaS version 0.5.0 to 'DemoBlueprintOpenFaaS'.

The following arguments are required: ``--path``, ``--name``. By default last version of template is downloaded.
For other versions specify ``--version``.

.. _CLI list and filter templates:

List and filter templates
=========================

All the available templates or just a single template can be listed.
By default this command lists all public templates and user's private templates.
Templates can also be filtered using their keyword, template type or publicity.

Here's some examples of listing and filtering:

.. code-block:: console

    $ xopera-template-library template list
    +List of templates----------------+-------------------------+------+--------+------------+---------------------+
    | ID | Name                       | Description             | Type | Public | Created by | Created at          |
    +----+----------------------------+-------------------------+------+--------+------------+---------------------+
    | 1  | AwsBucket                  | radon.nodes             | node | True   | admin      | 2020-05-29 11:51:12 |
    | 2  | AwsLambda                  | radon.nodes.function    | node | True   | admin      | 2020-05-29 11:55:50 |
    | 3  | AwsBucketNotification      | radon.nodes.triggers    | node | True   | admin      | 2020-05-29 11:59:03 |
    | 4  | AwsRole                    | radon.policies          | node | True   | admin      | 2020-05-29 12:02:35 |
    | 5  | AwsApiGateway              | radon.nodes.triggers    | node | True   | admin      | 2020-05-29 12:05:32 |
    | 6  | AzureContainer             | radon.nodes             | node | True   | admin      | 2020-06-01 06:36:30 |
    | 7  | AzureContainerNotification | radon.nodes             | node | True   | admin      | 2020-06-01 06:42:14 |
    | 8  | AzureFunction              | radon.nodes             | node | True   | admin      | 2020-06-01 06:51:52 |
    | 9  | MinIOBucket                | radon.nodes             | node | True   | admin      | 2020-06-01 07:44:56 |
    | 10 | OpenFaaSFunction           | radon.nodes             | node | True   | admin      | 2020-06-01 08:01:33 |
    | 11 | OpenFaaSFunctionBuild      | radon.nodes             | node | True   | admin      | 2020-06-01 08:37:16 |
    | 12 | DemoBlueprintAws           | node.blueprint          | csar | True   | admin      | 2020-07-21 10:04:22 |
    | 13 | GcpBucket                  | radon.nodes.GcpBucket   | node | True   | admin      | 2020-07-21 10:04:22 |
    | 14 | GcpFunction                | radon.nodes.GcpFunction | node | True   | admin      | 2020-07-21 10:04:22 |
    | 15 | DemoBlueprintGcp           | node.blueprint          | csar | True   | admin      | 2020-07-21 10:04:22 |
    +----+----------------------------+-------------------------+------+--------+------------+---------------------+

    $ xopera-template-library template list -h
    usage: xopera-template-library template list [-h] [--keyword KEYWORD]
                                                 [--type TYPE] [--public PUBLIC]

    optional arguments:
      -h, --help         show this help message and exit
      --keyword KEYWORD  Keyword in template name or description.
      --type TYPE        Template type name.
      --public PUBLIC    Public (true) or private (false) access.

    $ xopera-template-library template list --keyword Aws --type node --public true
    +List of templates-----------+----------------------+------+--------+------------+---------------------+
    | ID | Name                  | Description          | Type | Public | Created by | Created at          |
    +----+-----------------------+----------------------+------+--------+------------+---------------------+
    | 1  | AwsBucket             | radon.nodes          | node | True   | admin      | 2020-05-29 11:51:12 |
    | 2  | AwsLambda             | radon.nodes.function | node | True   | admin      | 2020-05-29 11:55:50 |
    | 3  | AwsBucketNotification | radon.nodes.triggers | node | True   | admin      | 2020-05-29 11:59:03 |
    | 4  | AwsRole               | radon.policies       | node | True   | admin      | 2020-05-29 12:02:35 |
    | 5  | AwsApiGateway         | radon.nodes.triggers | node | True   | admin      | 2020-05-29 12:05:32 |
    +----+-----------------------+----------------------+------+--------+------------+---------------------+

.. _CLI versions:

Versions
========

Template versions can be listed by using ``version`` argument and template name.

Example:

.. code-block:: console

    $ xopera-template-library template version --name AzureContainer
    +List of versions for AzureContainer+---------------------+
    | Version name | Template file name | Created at          |
    +--------------+--------------------+---------------------+
    | 0.1.0        | NodeType.tosca     | 2020-06-01 06:42:14 |
    | 0.2.0        | NodeType.tosca     | 2020-10-01 16:05:33 |
    | 0.3.0        | NodeType.tosca     | 2020-12-15 10:55:26 |
    +--------------+--------------------+---------------------+

.. _CLI template groups:

Template groups
***************

Templates can be ordered in template groups.
User can create a template group, list existing groups and get a list of templates in a template group.
Template group owners can add and remove templates.

Examples:

.. code-block:: console

    $ xopera-template-library template-group create --group_name MyGroup --group_description "My super models"
    Group was added successfully!

    $ xopera-template-library template-group list
    +List of groups-----+-----------------+------------+---------------------+
    | ID | Name         | Description     | Created by | Created at          |
    +----+--------------+-----------------+------------+---------------------+
    | 1  | AWS          | AWS models      | admin      | 2021-02-03 07:52:54 |
    | 2  | Azure        | Azure models    | user1      | 2021-02-03 07:53:05 |
    | 3  | GCP          | GCP models      | user2      | 2021-02-03 07:53:13 |
    | 4  | OpenFaaS     | OpenFaaS models | user3      | 2021-02-03 07:53:27 |
    | 5  | MyGroup      | My super models | user4      | 2021-02-03 07:53:27 |
    +----+--------------+-----------------+------------+---------------------+

    $ xopera-template-library template-group add-template --group_name AWS --template_name AwsBucket
    Template was successfully added to the group!

    $ xopera-template-library template-group get --group_name AWS
    +List of templates in template group 'AWS'----------+------+--------+------------+---------------------+
    | ID | Name                  | Description          | Type | Public | Created by | Created at          |
    +----+-----------------------+----------------------+------+--------+------------+---------------------+
    | 2  | AwsBucket             | radon.nodes          | node | True   | admin      | 2020-05-29 11:51:12 |
    | 3  | AwsLambda             | radon.nodes.function | node | True   | admin      | 2020-05-29 11:55:50 |
    | 4  | AwsBucketNotification | radon.nodes.triggers | node | True   | admin      | 2020-05-29 11:59:03 |
    | 5  | AwsRole               | radon.policies       | node | True   | admin      | 2020-05-29 12:02:35 |
    | 6  | AwsApiGateway         | radon.nodes.triggers | node | True   | admin      | 2020-05-29 12:05:32 |
    | 13 | DemoBlueprintAws      | node.blueprint       | csar | True   | admin      | 2020-07-21 10:04:22 |
    +----+-----------------------+----------------------+------+--------+------------+---------------------+

    $ xopera-template-library template-group add-template --group_name AWS --template_name AwsBucket
    Template is already in that group!

    $ xopera-template-library template-group remove-template --group_name AwsGroup --template_name AwsBucket
    Template was successfully removed from the group!

.. _CLI user groups:

User groups
***********

Users can be members of user groups.
User groups can be granted access to groups of templates.
User can create a user group, list existing groups, get a list of users in a given user group and get a list of
template groups a user group is granted access to.
User group owners can add and remove users and access to template groups.

Some simple examples are below:

.. code-block:: console

    $ xopera-template-library user-group create --group_name developers --group_description "Developers group"
    Group was added successfully!

    $ xopera-template-library user-group list
    +List of groups-------+----------------+------------+---------------------+
    | ID | Name           | Description    | Created by | Created at          |
    +----+----------------+----------------+------------+---------------------+
    | 1  | admin          | Admin users    | admin      | 2021-02-03 07:57:39 |
    | 2  | La_Pulga       | Team Messi     | messi      | 2021-02-03 09:27:19 |
    | 3  | El_Pibe_de_Oro | Team Maradona  | marado     | 2021-02-04 18:29:03 |
    | 4  | O_Rei          | Team Pele      | pele       | 2021-02-04 14:53:17 |
    | 5  | developers     | Dec team       | d10s       | 2021-02-04 14:53:17 |
    +----+----------------+----------------+------------+---------------------+

    $ xopera-template-library user-group get-users --group_name El_Pibe_de_Oro

    +-----------------------+
    | Username              |
    +-----------------------+
    | marado                |
    | valdano               |
    | burruchaga            |
    +-----------------------+

    $ xopera-template-library user-group add-user --group_name La_Pulga --username xavi
    User was successfully added to the group!

    $ xopera-template-library user-group remove-user --group_name La_Pulga --username suarez
    User was successfully removed from the group!

    $ xopera-template-library user-group add-templates --user_group developers --template_group AWS
    Access to template group was added successfully!

    $ xopera-template-library user-group add-templates --user_group developers --template_group Azure
    This user group already has access to this template group!

    $ xopera-template-library user-group remove-templates --user_group developers --template_group GCP
    Access to template group was revoked successfully!

.. _CLI users:

Users
*****

Users can look up their info, list user groups they are members of and list template groups they have access to.

Here's some examples:

.. code-block:: console

    $ xopera-template-library user info
    +User data-----------+--------------------+--------------------+---------------------+
    | ID | Name          | Username           | Email              | Created at          |
    +----+---------------+--------------------+--------------------+---------------------+
    | 6  | Keycloak user | user@example.com   | user@example.com   | 2020-10-29 14:55:39 |
    +----+---------------+--------------------+--------------------+---------------------+

    $ xopera-template-library user user-groups
    +List of groups-----------+------------+---------------------+
    | ID | Name | Description | Created by | Created at          |
    +----+------+-------------+------------+---------------------+
    | 1  | xlab | Admin users | admin      | 2021-02-03 07:57:39 |
    +----+------+-------------+------------+---------------------+

    $ xopera-template-library user template-groups
    +List of template groups accessible by 'xlab'--+---------------------+
    | ID | Name     | Description     | Created by | Created at          |
    +----+----------+-----------------+------------+---------------------+
    | 1  | AWS      | AWS models      | admin      | 2021-02-03 07:52:54 |
    | 2  | Azure    | Azure models    | admin      | 2021-02-03 07:53:05 |
    | 3  | GCP      | GCP models      | admin      | 2021-02-03 07:53:13 |
    | 4  | OpenFaaS | OpenFaaS models | admin      | 2021-02-03 07:53:27 |
    +----+----------+-----------------+------------+---------------------+

.. _CLI debugging:

Debugging
*********

If you have some problems when you are executing commands with the Template Library CLI you can use switch to debug
mode.
To debug your client add ``-v`` or ``--verbose`` argument after calling ``xopera-template-library``:

Take a look at some examples below:

.. code-block:: console

    $ xopera-template-library --verbose setup

    DEBUG:root:Metadata storage: /home/user/.config/template_library
    DEBUG:root:[Errno 17] File exists: '/home/user/.config/template_library'
    Current Template Library REST API endpoint: https://template-library-xopera.xlab.si/api
    Modify API endpoint (press enter to keep the current):
    Template Library REST API endpoint has been set to: https://template-library-xopera.xlab.si/api.

    Current Keycloak auth endpoint: https://openid-xopera.xlab.si
    Modify Keycloak endpoint (press enter to keep the current):
    Keycloak endpoint has been set to: https://openid-xopera.xlab.si.
    DEBUG:root:https://template-library-xopera.xlab.si/api
    DEBUG:root:https://openid-xopera.xlab.si

    $ xopera-template-library -v template list

    DEBUG:root:Metadata storage: /home/anzoman/.config/template_library
    DEBUG:root:https://template-library-xopera.xlab.si/api
    DEBUG:root:https://openid-xopera.xlab.si
    DEBUG:urllib3.connectionpool:Starting new HTTPS connection (1): template-library-xopera.xlab.si:443
    DEBUG:urllib3.connectionpool:https://template-library-xopera.xlab.si:443 "GET /api/templates/filter HTTP/1.1" 200 1061
    DEBUG:urllib3.connectionpool:Starting new HTTPS connection (1): template-library-xopera.xlab.si:443
    DEBUG:urllib3.connectionpool:https://template-library-xopera.xlab.si:443 "GET /api/users?user_id=1 HTTP/1.1" 200 154
    DEBUG:urllib3.connectionpool:Starting new HTTPS connection (1): template-library-xopera.xlab.si:443
    DEBUG:urllib3.connectionpool:https://template-library-xopera.xlab.si:443 "GET /api/users?user_id=1 HTTP/1.1" 200 154
    DEBUG:urllib3.connectionpool:Starting new HTTPS connection (1): template-library-xopera.xlab.si:443
    DEBUG:urllib3.connectionpool:https://template-library-xopera.xlab.si:443 "GET /api/users?user_id=1 HTTP/1.1" 200 154
    DEBUG:urllib3.connectionpool:Starting new HTTPS connection (1): template-library-xopera.xlab.si:443
    DEBUG:urllib3.connectionpool:https://template-library-xopera.xlab.si:443 "GET /api/users?user_id=1 HTTP/1.1" 200 154
    DEBUG:urllib3.connectionpool:Starting new HTTPS connection (1): template-library-xopera.xlab.si:443
    DEBUG:urllib3.connectionpool:https://template-library-xopera.xlab.si:443 "GET /api/users?user_id=1 HTTP/1.1" 200 154
    DEBUG:urllib3.connectionpool:Starting new HTTPS connection (1): template-library-xopera.xlab.si:443
    DEBUG:urllib3.connectionpool:https://template-library-xopera.xlab.si:443 "GET /api/users?user_id=1 HTTP/1.1" 200 154
    DEBUG:urllib3.connectionpool:Starting new HTTPS connection (1): template-library-xopera.xlab.si:443

    +List of templates----------------+-------------------------+------+--------+------------+---------------------+
    | ID | Name                       | Description             | Type | Public | Created by | Created at          |
    +----+----------------------------+-------------------------+------+--------+------------+---------------------+
    | 1  | AwsBucket                  | radon.nodes             | node | True   | admin      | 2020-05-29 11:51:12 |
    | 2  | AwsLambda                  | radon.nodes.function    | node | True   | admin      | 2020-05-29 11:55:50 |
    | 3  | AwsBucketNotification      | radon.nodes.triggers    | node | True   | admin      | 2020-05-29 11:59:03 |
    | 4  | AwsRole                    | radon.policies          | node | True   | admin      | 2020-05-29 12:02:35 |
    | 5  | AwsApiGateway              | radon.nodes.triggers    | node | True   | admin      | 2020-05-29 12:05:32 |
    | 6  | AzureContainer             | radon.nodes             | node | True   | admin      | 2020-06-01 06:36:30 |
    | 7  | AzureContainerNotification | radon.nodes             | node | True   | admin      | 2020-06-01 06:42:14 |
    | 8  | AzureFunction              | radon.nodes             | node | True   | admin      | 2020-06-01 06:51:52 |
    | 9  | MinIOBucket                | radon.nodes             | node | True   | admin      | 2020-06-01 07:44:56 |
    | 10 | OpenFaaSFunction           | radon.nodes             | node | True   | admin      | 2020-06-01 08:01:33 |
    | 11 | OpenFaaSFunctionBuild      | radon.nodes             | node | True   | admin      | 2020-06-01 08:37:16 |
    | 12 | DemoBlueprintAws           | node.blueprint          | csar | True   | admin      | 2020-07-21 10:04:22 |
    | 13 | GcpBucket                  | radon.nodes.GcpBucket   | node | True   | admin      | 2020-07-21 10:04:22 |
    | 14 | GcpFunction                | radon.nodes.GcpFunction | node | True   | admin      | 2020-07-21 10:04:22 |
    | 15 | DemoBlueprintGcp           | node.blueprint          | csar | True   | admin      | 2020-07-21 10:04:22 |
    +----+----------------------------+-------------------------+------+--------+------------+---------------------+

.. _CLI user story:

User story
##########

This subsection provides a concrete usage scenario for the Template Library Command Line Interface which needs to be
installed first (you can follow :ref:`CLI installation` part).
Here we are talking about the user named John who already has a Template Library account (John is an XLAB Keycloak
user) and has already installed Template Library CLI into Python virtual environment.

The story begins when John would like to create his own blueprint to deploy an application.
Let's say that he wants to create an AWS application for creating thumbnails from images.
When an image is uploaded to the source AWS bucket, the bucket notification invokes the AWS Lambda resizing function
that creates thumbnails and uploads them to the destination bucket where user can see them.
John knows there are already a lot of modules/templates saved in the Template Library and he decides to use the tool
via Template Library CLI.
First he has to setup the CLI and log in with his credentials.

.. code-block:: console

    (.venv) $ xopera-template-library setup
    Current Template Library REST API endpoint: https://template-library-xopera.xlab.si/api
    Modify API endpoint (press enter to keep the current):
    Template Library REST API endpoint has been set to: https://template-library-xopera.xlab.si/api.

    (.venv) $ xopera-template-library login --username johnsmith --password johnssecurepass123
    User 'johnsmith' logged in.

.. warning::

    Don't be like John.
    Omit the ``--password`` switch and rather type in your password through prompt.

After that the program is ready to use.
John can now generate his project folder and a startup blueprint (`TOSCA CSAR`_) file.

.. code-block:: console

    (.venv) $ xopera-template-library template create --type csar --name csar
    Template type: csar
    Template name: johns-project
    Blueprint (TOSCA CSAR) 'csar' created in ./johns-project.

    (.venv) $ tree johns-project
    johns-project
    ├── files
    │   ├── create.yml
    │   └── delete.yml
    ├── README.md
    ├── ServiceTemplate.tosca
    └── TOSCA-Metadata
        └── TOSCA.meta

    (.venv) $ cat johns-project/TOSCA-Metadata/TOSCA.meta
    TOSCA-Meta-File-Version: '1.1'
    CSAR-Version: '1.1'
    Created-By: Template Library CLI
    Entry-Definitions: ServiceTemplate.tosca

Template Library CLI has generated a valid `TOSCA CSAR`_ for John which is evident from `TOSCA-Metadata/TOSCA.meta`
file.
Then he lists all public templates in the Template Library to see what he can use with ``template list`` CLI command:

.. code-block:: console

    (.venv) $ xopera-template-library template list

    +List of templates----------------+-------------------------+------+--------+------------+---------------------+
    | ID | Name                       | Description             | Type | Public | Created by | Created at          |
    +----+----------------------------+-------------------------+------+--------+------------+---------------------+
    | 1  | AwsBucket                  | radon.nodes             | node | True   | admin      | 2020-05-29 11:51:12 |
    | 2  | AwsLambda                  | radon.nodes.function    | node | True   | admin      | 2020-05-29 11:55:50 |
    | 3  | AwsBucketNotification      | radon.nodes.triggers    | node | True   | admin      | 2020-05-29 11:59:03 |
    | 4  | AwsRole                    | radon.policies          | node | True   | admin      | 2020-05-29 12:02:35 |
    | 5  | AwsApiGateway              | radon.nodes.triggers    | node | True   | admin      | 2020-05-29 12:05:32 |
    | 6  | AzureContainer             | radon.nodes             | node | True   | admin      | 2020-06-01 06:36:30 |
    | 7  | AzureContainerNotification | radon.nodes             | node | True   | admin      | 2020-06-01 06:42:14 |
    | 8  | AzureFunction              | radon.nodes             | node | True   | admin      | 2020-06-01 06:51:52 |
    +----+----------------------------+-------------------------+------+--------+------------+---------------------+

He decides on `AwsLambda` and `AwsBucket` and downloads the templates.

.. code-block:: console

    (.venv) $ xopera-template-library entity-template get --path johns-project/files/AwsLambda
    Downloaded AwsLambda version 0.1.0 to 'AwsLambda'.

    (.venv) $ xopera-template-library entity-template get --path johns-project/files/AwsBucket
    Downloaded AwsBucket version 0.1.0 to 'AwsBucket'.

He erases some unneeded files and modifies his TOSCA service template.
His structure now looks like this:

.. code-block:: console

    (.venv) $ tree johns-project
    johns-project
    ├── files
    │   ├── AwsBucket
    │   │   ├── files
    │   │   │   ├── create.yml
    │   │   │   └── delete.yml
    │   │   └── NodeType.tosca
    │   ├── AwsLambda
    │   │   ├── files
    │   │   │   ├── create.yml
    │   │   │   └── delete.yml
    │   │   └── NodeType.tosca
    ├── README.md
    ├── ServiceTemplate.tosca
    └── TOSCA-Metadata
        └── TOSCA.meta

But he also wants to add module for AWS Bucket Notification.
To make things easier he uses ``template create`` CLI command to generate the necessary files and directories.

.. code-block:: console

    (.venv) $ xopera-template-library template create
    Template type: node
    Template name: johns-project/files/AwsBucketNotification
    Model 'AwsBucketNotification' created in johns-project/files/AwsBucketNotification.

    (.venv) $ tree johns-project
    johns-project
    ├── files
    │   ├── AwsBucket
    │   │   ├── files
    │   │   │   ├── create.yml
    │   │   │   └── delete.yml
    │   │   └── NodeType.tosca
    │   ├── AwsBucketNotification
    │   │   ├── files
    │   │   │   ├── create.yml
    │   │   │   └── delete.yml
    │   │   └── NodeType.tosca
    │   ├── AwsLambda
    │   │   ├── files
    │   │   │   ├── create.yml
    │   │   │   └── delete.yml
    │   │   └── NodeType.tosca
    ├── README.md
    ├── ServiceTemplate.tosca
    └── TOSCA-Metadata
        └── TOSCA.meta

John modifies his TOSCA service template and includes the created `AwsBucketNotification` module.
Now his blueprint is prepared and he can test it with the TOSCA orchestrator (like `opera`_) to see how the deployment
works (we will skip this part since this is not the point here).
John thinks his blueprint is pretty good, so he decides to add it to the public Template Library collection because
sharing is caring.
Before he does that he should compress his files (a valid `TOSCA CSAR`_ is usually a zip file).

.. code-block:: console

    (.venv) $ cd johns-project
    (.venv) $ zip -r JohnsBlueprint.zip ServiceTemplate.tosca files TOSCA-Metadata
    (.venv) $ xopera-template-library template save --name JohnsBlueprint --path johns-project/JohnsBlueprint.zip --public_access True --version 0.0.1
    Path to README file (optional, can be left empty):
    Description of the template: Johns blueprint with AWS thumbnail generator
    Template has been added successfully!
    Version insertion was successful!

John has submitted his blueprint and others can now download it from Template Library to test his magnificent work.

.. note::

    Some parts of blueprint preparation such as AWS Lambda function preparation CSAR validation are skipped for brevity.

.. _TPS CLI video:

TPS CLI video
#############

In the video below we present the TPS and how to use it through the CLI.
Template Library CLI is a client tool that is connected to the TPS REST API.
The tool can be used from the user’s console for managing templates, blueprints and their versions.
It is here to save you from executing complex curl requests and will offer you to easily access and operate with the
Template Library.
The CLI tool goes by the name `xopera-template-library`_ and you can install it as a Python package from `PyPI`_.

.. raw:: html

    <div style="text-align: center; margin-bottom: 2em;">
    <iframe width="100%" height="350" src="https://www.youtube.com/embed/28eTwojw5ac" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
    </div>

.. _CLI commands:

CLI commands
############

The next section lists and describes all Template Library CLI commands and their options.

------------------------------------------------------------------------------------------------------------------------

.. _setup_command:

setup
*****

.. argparse::
    :module: src.cli
    :func: create_parser
    :prog: xopera-template-library
    :path: setup

------------------------------------------------------------------------------------------------------------------------

.. _login_command:

login
*****

.. argparse::
    :module: src.cli
    :func: create_parser
    :prog: xopera-template-library
    :path: login

------------------------------------------------------------------------------------------------------------------------

.. _logout_command:

logout
******

.. argparse::
    :module: src.cli
    :func: create_parser
    :prog: xopera-template-library
    :path: logout

------------------------------------------------------------------------------------------------------------------------

.. _user_command:

user
****

.. argparse::
    :module: src.cli
    :func: create_parser
    :prog: xopera-template-library
    :path: user

------------------------------------------------------------------------------------------------------------------------

.. _user-group_command:

user-group
**********

.. argparse::
    :module: src.cli
    :func: create_parser
    :prog: xopera-template-library
    :path: template

------------------------------------------------------------------------------------------------------------------------

.. _template_command:

template
********

.. argparse::
    :module: src.cli
    :func: create_parser
    :prog: xopera-template-library
    :path: template

------------------------------------------------------------------------------------------------------------------------

.. _template-group_command:

template-group
**************

.. argparse::
    :module: src.cli
    :func: create_parser
    :prog: xopera-template-library
    :path: template

------------------------------------------------------------------------------------------------------------------------

.. _xopera-template-library: https://pypi.org/project/xopera-template-library
.. _PyPI: https://pypi.org/project/xopera-template-library
.. _Test PyPI: https://test.pypi.org/project/xopera-template-library
.. _openid-xopera.xlab.si: https://openid-xopera.xlab.si
.. _RADON particles: https://github.com/radon-h2020/radon-particles
.. _Eclipse Winery: https://projects.eclipse.org/projects/soa.winery
.. _TOSCA CSAR: https://docs.oasis-open.org/tosca/TOSCA-Simple-Profile-YAML/v1.3/cos01/TOSCA-Simple-Profile-YAML-v1.3-cos01.html#_Toc26969474
.. _opera: https://github.com/xlab-si/xopera-opera
