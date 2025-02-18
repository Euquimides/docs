:is-up-to-date: True
:nosearch:

.. _newIa-crafter-studio-api-repo-rebuild-database:

=============================
Rebuild Database (deprecated)
=============================

Rebuild Crafter Studio's database and object state with the underlying repository.

.. important::
    This API is deprecated and provided only as a reference.  Please see :studio_swagger_url:`#/repository/rebuildDatabase` for the current version.

--------------------
Resource Information
--------------------

.. include:: /includes/studio-api-url-prefix.rst

+----------------------------+-------------------------------------------------------------------+
|| HTTP Verb                 || POST                                                             |
+----------------------------+-------------------------------------------------------------------+
|| URL                       || ``/api/1/services/api/1/repo/rebuild-database.json``             |
+----------------------------+-------------------------------------------------------------------+
|| Response Formats          || ``JSON``                                                         |
+----------------------------+-------------------------------------------------------------------+
|| Required Role             || Admin, site member                                               |
+----------------------------+-------------------------------------------------------------------+

----------
Parameters
----------

+---------------+-------------+---------------+--------------------------------------------------+
|| Name         || Type       || Required     || Description                                     |
+===============+=============+===============+==================================================+
|| site_id      || String     || |checkmark|  || Site to use                                     |
+---------------+-------------+---------------+--------------------------------------------------+

-------
Example
-------
^^^^^^^
Request
^^^^^^^

``POST .../api/1/services/api/1/repo/rebuild-database.json``

.. code-block:: json

  {
    "site_id" : "mysite"
  }

^^^^^^^^
Response
^^^^^^^^

``Status 200 OK``

.. code-block:: json

  null

---------
Responses
---------

+---------+-------------------------------------------+---------------------------------------------------+
|| Status || Location                                 || Response Body                                    |
+=========+===========================================+===================================================+
|| 200    ||                                          || See example above.                               |
+---------+-------------------------------------------+---------------------------------------------------+
|| 400    ||                                          || ``{ "message" : "Bad Request" }``                |
+---------+-------------------------------------------+---------------------------------------------------+
