:is-up-to-date: True
:nosearch:

.. index:: Upgrading CrafterCMS; Upgrading

.. _newIa-upgrading-craftercms:

====================
Upgrading CrafterCMS
====================

This section details the steps required to upgrade your CrafterCMS install.

.. WARNING::
   * This guide assumes that you're trying to upgrade a site from a stock 3.1.x Studio and with some slight Studio configuration changes. If your site configuration is heavily customized or your Studio is a custom overlay you might need additional work that is not specified here.

   * The following release versions are able to upgrade to 4.0.0

     - 3.1.9
     - 3.1.12
     - 3.1.13
     - 3.1.17 and later versions

     If you are upgrading from a version other than the ones listed above, you will need to upgrade to one of the above listed supported upgrade paths release version first before upgrading to 4.0.0.  See the 3.1 release’s Upgrading CrafterCMS page here: https://docs.craftercms.org

|

Here are the instructions for upgrading CrafterCMS based on how it was installed (on a server or via Kubernetes/Docker Compose):

.. toctree::
   :maxdepth: 1
   :titlesonly:

   upgrading-crafter.rst
   docker/index.rst

For more information on adding upgrade scripts for your customizations such as changes to Crafter Studio's configuration or database:

.. toctree::
   :maxdepth: 1
   :titlesonly:

   add-to-upgrade-scripts.rst


