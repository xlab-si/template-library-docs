.. _plugin:

***********************************************
Template Library plugin for VS Code/Eclipse Che
***********************************************

The Template Library also has `Eclipse Che`_ (`Eclipse Theia`_) and `Visual Studio Code`_ plugin.
The plugin is available on `Open VSX Registry`_ and `VS Marketplace`_.
The code can also be explored in `template-library-vscode-plugin`_ GitHub repository.

The Template Library VS Code/Eclipse Che plugin is used for communication between Template Library and the
`Visual Studio Code`_ or `Eclipse Theia`_.
Using the plugin, the user is able to manage, store and retrieve his TOSCA modules (templates, blueprints
(`TOSCA CSAR`_ files)) and their implementations (e.g. Ansible playbooks) from `Eclipse Theia`_ or
`Visual Studio Code`_.
Template Library plugin is also used in `RADON IDE`_.

.. _Manual installation and publishing:

Manual installation and publishing
**********************************

You can freely skip this part because the plugin can be installed through `Open VSX Registry`_ or `VS Marketplace`_.
This is originally a `Visual Studio Code`_ extension/plugin, so the best way to test it locally is to through VS Code's
extension development host.
`Eclipse Che`_ uses an open-source cloud and desktop IDE framework called
`Eclipse Theia`_ within the workspaces.
`Eclipse Theia`_ Cloud & Desktop IDE Platform that is used within Eclipse Che workspaces is very similar to VS Code and
therefore VS Code extensions can be also used in Theia.

.. _VS Code installation:

VS Code installation
====================

To install the plugin to your Visual Studio Code editor you will need the latest `vsix` plugin package which you can
get from here: `RADON plugin registry`_.
Then you just have to import the `vsix` file to the VS Code plugins.

.. _plugin_install:

.. figure:: /images/plugin/install.png
    :target: _images/install.png
    :width: 50%
    :align: center

    Template Library plugin VS Code installation.

After that you will be able to use the plugin in any VS Code window.

.. _plugin_installed:

.. figure:: /images/plugin/installed.png
    :target: _images/installed.png
    :width: 90%
    :align: center

    Template Library plugin installed.

.. _Eclipse Che Theia installation:

Eclipse Che Theia installation
==============================

TPS plugin is primarily meant for usage in `Eclipse Theia`_ which is the main editor in the `Eclipse Che`_.
To install the plugin to Che you will need the prepared YAML devfile (which also uses the prepared `meta.yaml` file).
The files to try this can be found in `Template Library publishing-samples from GitHub`_.

.. _Publishing:

Publishing
==========

Here's what you need to do to test the plugin and to package/publish it:

.. code-block:: bash

    # install Node JS from 'https://nodejs.org/en/download/'
    # and test it with node -v and npm -v command

    # install prerequisite packages
    npm install

    # test the plugin in VS Code (this will open a new window with your extension loaded)
    press F5 (Run Extension)

    # package and publish the extension
    # if you don't have npx install it with: npm install npx
    npx vsce package

.. _Main features:

Main features
*************

The extension uses `Template Library REST API`_ and can therefore invoke various Template Library actions.

Currently, supported actions are:

- setting Template Library REST API endpoint
- creating and publishing TOSCA template or CSAR and its version
- downloading a specific template version files
- deleting saved login info (`Keycloak`_ cookies)

.. _Plugin usage:

Plugin Usage
************

The plugin is invoked by right clicking on the file from file explorer or in the editor.
There are four commands that can be selected from the dropdown options and these are further explained within the next
sections.

.. _plugin_commands:

.. figure:: /images/plugin/commands.png
    :target: _images/commands.png
    :width: 50%
    :align: center

    Template Library plugin commands.

.. _Template Library authentication:

Template Library authentication
===============================

When right clicking on any Template Library plugin command (except from set API endpoint and clean login info
commands), the extension will verify user's credentials if the data has been saved.
So, the first time when user wants to use the plugin, he will be offered a set of options to select the preferred
authentication method for the Template library.
Since Template Library auth works through `Keycloak`_, there can be multiple login methods.
You can login with:

- default `XLAB Keycloak`_ credentials
- other identity providers that are connected to the Keycloak instance

.. _plugin_auth:

.. figure:: /images/plugin/auth_methods.png
    :target: _images/auth_methods.png
    :width: 80%
    :align: center

    Template Library plugin auth.

If the login does not succeed, you will be warned and will have to login again.
If the login succeeds, the Keycloak auth cookies will be stored into the local storage and next time you invoke the any
plugin command, you won't have to login again.
But if you for instance set Template Library API endpoint to something else or if you wish to login as another Keycloak
user, the it is wise to clear saved login data by invoking the "Delete login info" action.

.. _plugin_login_password:

.. figure:: /images/plugin/login_password.png
    :target: _images/login_password.png
    :width: 80%
    :align: center

    Password prompt for Template Library plugin.

.. _Template Library set REST API endpoint:

Template Library set REST API endpoint
======================================

This command is used to set TPS REST API endpoint that will be used for executing the TPS HTTP requests.
The default value here is `https://template-library-xopera.xlab.si/api` which is pointing to the public TPS REST API URL.
This command was meant mostly for testing different versions of TPS API so currently there is no need to change it.

.. _plugin_set_api_endpoint:

.. figure:: /images/plugin/set_api_endpoint.png
    :target: _images/set_api_endpoint.png
    :width: 90%
    :align: center

    Set Template Library API endpoint for Template Library plugin.

.. _Template Library config actions:

Template Library config actions
===============================

If you choose this option the TPS actions can be invoked via JSON config file.
If you right clicked on the JSON file (from the editor or from the file explorer) you will be offered to chose it as a
config file.
If not, you will be asked to select this configuration file from other folders.

JSON object that is present in the config file should follow an exact structure with which depends on the type of the
action.
The JSON keys specified are not mutually exclusive so you can execute multiple TPS actions with one JSON config file.

.. _Create template JSON config:

Create template JSON config
---------------------------

JSON object for creating a template must have all these keys:

+---------------------------------+------------------------------------------------------------------------+
| JSON key                        | Description                                                            |
+=================================+========================================================================+
| **uploadTemplateName**          | Template name you want to create                                       |
+---------------------------------+------------------------------------------------------------------------+
| **uploadTemplateDescription**   | Template description                                                   |
+---------------------------------+------------------------------------------------------------------------+
| | **uploadTemplateTypeName**    | | Template type name (one of: data, artifact, capability, requirement, |
| |                               | | relationship, interface, node, group, policy, csar, other)           |
+---------------------------------+------------------------------------------------------------------------+
| **uploadPublicAccess**          | Make template publicly visible for other TPS users (true/false)        |
+---------------------------------+------------------------------------------------------------------------+

Example:

.. code-block:: json

    {
        "uploadTemplateName": "awsBucket",
        "uploadTemplateDescription": "AWS bucket node",
        "uploadTemplateTypeName": "node",
        "uploadPublicAccess": "true"
    }

.. _plugin_upload_config:

.. figure:: /images/plugin/upload_config.png
    :target: _images/upload_config.png
    :width: 80%
    :align: center

    Upload config action.

.. _Upload template version JSON config:

Upload template version JSON config
-----------------------------------

When uploading a template version you can use the following keys (`upload_readme_file` is optional):

+----------------------------------+-------------------------------------------------------------------+
| JSON key                         | Description                                                       |
+==================================+===================================================================+
| **uploadVersionName**            | Semantic version name                                             |
+----------------------------------+-------------------------------------------------------------------+
| **uploadReadmeFile**             | Optional path to README file to upload                            |
+----------------------------------+-------------------------------------------------------------------+
| **uploadTemplateFile**           | TOSCA YAML service template file or compressed `TOSCA CSAR`_      |
+----------------------------------+-------------------------------------------------------------------+

Example:

.. code-block:: json

    {
        "uploadVersionName": "2.1.5",
        "uploadReadmeFile": "./aws_bucket/README.md",
        "uploadTemplateFile": "./aws_bucket/AwsBucket.zip",
    }

.. _plugin_upload_success:

.. figure:: /images/plugin/upload_success.png
    :target: _images/upload_success.png
    :width: 80%
    :align: center

    Successful template version upload.

.. _Download template version JSON config:

Download template version JSON config
-------------------------------------

When downloading template version files you will get all version files (TOSCA template and playbooks) compressed in a
zip file.

+----------------------------+---------------------------------------------------------------------+
| JSON key                   | Description                                                         |
+============================+=====================================================================+
| **downloadTemplateName**   | Name of the template you want to download                           |
+----------------------------+---------------------------------------------------------------------+
| **downloadVersionName**    | Semantic template version you want to get files from (e.g. `3.5.8`) |
+----------------------------+---------------------------------------------------------------------+
| **downloadPath**           | Path where the downloaded file will be stored                       |
+----------------------------+---------------------------------------------------------------------+

Example:

.. code-block:: json

    {
        "downloadTemplateName": "awsBucket",
        "downloadVersionName": "2.1.5",
        "downloadPath": "./AwsBucket.zip"
    }

.. _plugin_download_config:

.. figure:: /images/plugin/download_config.png
    :target: _images/download_config.png
    :width: 80%
    :align: center

    Download config action.

.. _plugin_download_success:

.. figure:: /images/plugin/download_success.png
    :target: _images/download_success.png
    :width: 80%
    :align: center

    Successful template version download.

.. _Template Library interactive actions:

Template Library interactive actions
====================================

This TPS `Eclipse Che`_ extension command will guide you through an interactive `Eclipse Theia`_ tasks, where you will be
able to create templates, upload template versions or download version files from Template Library service.
A few images from the plugin are shown below.

.. _plugin_interactive_choices:

.. figure:: /images/plugin/interactive_choices.png
    :target: _images/interactive_choices.png
    :width: 80%
    :align: center

    Interactive action choices.

.. _plugin_template_action_choices:

.. figure:: /images/plugin/template_action_choices.png
    :target: _images/template_action_choices.png
    :width: 80%
    :align: center

    Template upload action choices.

.. _plugin_template_type_choices:

.. figure:: /images/plugin/template_type_choices.png
    :target: _images/template_type_choices.png
    :width: 80%
    :align: center

    Template type choices.

.. _plugin_pick_template_file:

.. figure:: /images/plugin/pick_template_file.png
    :target: _images/pick_template_file.png
    :width: 80%
    :align: center

    Pick template file for upload.

.. _plugin_pick_from_filesystem:

.. figure:: /images/plugin/pick_from_filesystem.png
    :target: _images/pick_from_filesystem.png
    :width: 80%
    :align: center

    Pick template file from filesystem.

.. _plugin_interactive_pick_template:

.. figure:: /images/plugin/download_interactive_pick_template.png
    :target: _images/download_interactive_pick_template.png
    :width: 80%
    :align: center

    Pick template to download.

.. _plugin_download_interactive_select_path:

.. figure:: /images/plugin/download_interactive_select_path.png
    :target: _images/download_interactive_select_path.png
    :width: 80%
    :align: center

    Select download file path.

.. _Template Library delete login info:

Template Library delete login info
==================================

This Template Library plugin command will make sure that the saved login data gets deleted (e.g. Keycloak cookies).
After that you will have to login again if you activate any TPS commands.

.. note::

    For more information take a look at `Template Library Che plugin GitHub repository`_.

.. tip::

    You can also use `Template Library CLI`_ in Theia workspace terminal.

.. _TPS Che plugin video:

TPS Che plugin video
********************

In the video below we present the TPS and how to use it through the `Eclipse Che`_ (`RADON IDE`_).

.. raw:: html

    <div style="text-align: center; margin-bottom: 2em;">
    <iframe width="100%" height="350" src="https://www.youtube.com/embed/vCjfZ4Iue0E" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
    </div>

.. _Template Library CLI: https://pypi.org/project/xopera-template-library
.. _RADON IDE: https://github.com/radon-h2020/radon-ide
.. _Eclipse Che: https://www.eclipse.org/che
.. _Eclipse Theia: https://theia-ide.org
.. _Visual Studio Code: https://code.visualstudio.com
.. _RADON plugin registry: https://github.com/radon-h2020/radon-plugin-registry
.. _Template Library publishing-samples from GitHub: https://github.com/radon-h2020/radon-template-library-publishing-service-plugin/tree/master/publishing-samples
.. _Template Library REST API: https://template-library-xopera.xlab.si/swagger/
.. _Keycloak: https://www.keycloak.org/
.. _XLAB Keycloak: https://openid-xopera.xlab.si/auth/realms/master/account
.. _Template Library Che plugin GitHub repository: https://github.com/xlab-si/template-library-vscode-plugin
.. _TOSCA CSAR: https://docs.oasis-open.org/tosca/TOSCA-Simple-Profile-YAML/v1.3/cos01/TOSCA-Simple-Profile-YAML-v1.3-cos01.html#_Toc26969474
.. _template-library-vscode-plugin: https://github.com/xlab-si/template-library-vscode-plugin
.. _Open VSX Registry: https://open-vsx.org/extension/xlab/template-library
.. _VS Marketplace: https://marketplace.visualstudio.com/items?itemName=xlab.template-library
