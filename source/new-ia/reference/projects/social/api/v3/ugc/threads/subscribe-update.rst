:is-up-to-date: True
:nosearch:

.. _newIa-crafter-social-api-ugc-threads-subscribe-update:

===================
Update Subscription
===================

Subscribes the current user to a given thread.

--------------------
Resource Information
--------------------

.. include:: /includes/social-api-url-prefix.rst

+----------------------------+-------------------------------------------------------------------+
|| HTTP Verb                 || POST                                                             |
+----------------------------+-------------------------------------------------------------------+
|| URL                       || ``/api/3/threads/:id/subscribe/update``                          |
+----------------------------+-------------------------------------------------------------------+
|| Response Formats          || ``JSON``                                                         |
+----------------------------+-------------------------------------------------------------------+

----------
Parameters
----------

+-------------+----------+---------------+--------------------------------------------+
|| Name       || Type    || Required     || Description                               |
+=============+==========+===============+============================================+
|| context    || String  || |checkmark|  || The ID of the Social Context              |
+-------------+----------+---------------+--------------------------------------------+
|| id         || String  || |checkmark|  || The ID of the thread to update            |
+-------------+----------+---------------+--------------------------------------------+
|| frequency  || String  ||              || Type of notifications to receive          |
+-------------+----------+---------------+--------------------------------------------+

.. NOTE::
  The possible values for the ``frequency`` parameter are:
  
  - weekly
  - daily
  - instant

-------
Example
-------

^^^^^^^
Request
^^^^^^^

.. code-block:: none

  POST .../api/3/threads/Welcome/subscribe/update

.. code-block:: none

  context=f5b143c2-f1c0-4a10-b56e-f485f00d3fe9
  frequency=daily

^^^^^^^^
Response
^^^^^^^^

``Status 200 OK``

.. code-block:: json
  :linenos:

  true

---------
Responses
---------

+---------+--------------------------------+-----------------------------------------------------+
|| Status || Location                      || Response Body                                      |
+=========+================================+=====================================================+
|| 200    ||                               || See example above.                                 |
+---------+--------------------------------+-----------------------------------------------------+
|| 401    ||                               || ``{ "message" : "User must be logged in" }``       |
+---------+--------------------------------+-----------------------------------------------------+
|| 403    ||                               | .. code-block:: json                                |
||        ||                               |                                                     |
||        ||                               |   { "message" : "Current subject does not have      |
||        ||                               |   permission to execute global action ..." }        |
+---------+--------------------------------+-----------------------------------------------------+
|| 500    ||                               || ``{ "message" : "Internal server error" }``        |
+---------+--------------------------------+-----------------------------------------------------+
