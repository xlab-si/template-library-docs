.. _GUI:

********************
Template Library GUI
********************

This part explains the Template Library Graphical User Interface (GUI), which is accessible at
`template-library-xopera.xlab.si/`_.

.. _GUI Usage:

Usage
#####

TPS web interface is used for displaying and searching for IaC TOSCA modules and service templates published in the TPS.
This provides an alternative way to browse through the TPS content in addition to the other options as REST API
endpoint calls, REST API Swagger UI, CLI, Eclipse Che plugin.
This addition enables an easier way to search for and view templates in a browser on the go.

It displays publicly available templates' data to users without authentication.
User authorization is integrated with RADON IAM with Keycloak and is needed for accessing the private TPS content.
No additional authorization is added for using TPS GUI.
User can login with Keycloak by clicking *Log in* link in navigation.

All users can search public templates by keyword, preview the template’s last version’s readme file and download
compressed version files, while authenticated users can also view their private templates.

.. _gui_search:

.. figure:: /images/gui/search.png
    :target: _images/search.png
    :width: 100%
    :align: center

    Searching templates by keyword.

A click on template info opens additional view with last version name, date and download link for version files.
If the template has a README file it is displayed.

.. _gui_template_version:

.. figure:: /images/gui/template_details.png
    :target: _images/template_details.png
    :width: 100%
    :align: center

    Display of template version's information and files.

In sidebar users can choose to view templates in template groups or ordered by template type.

.. _gui_template_groups:

.. figure:: /images/gui/template_groups.png
    :target: _images/template_groups.png
    :width: 100%
    :align: center

    List templates in template groups.

.. _template-library-xopera.xlab.si/: https://template-library-xopera.xlab.si/
