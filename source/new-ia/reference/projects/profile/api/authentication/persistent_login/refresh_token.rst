:is-up-to-date: True
:nosearch:

.. _newIa-crafter-profile-api-authentication-persistent_login-refresh_token:

========================
Refresh Persistent Login
========================

Refreshes the token of the specified persistent login.

--------------------
Resource Information
--------------------

.. include:: /includes/profile-api-url-prefix.rst

+-----------------+-----------------------------------------------------------------------------+
| HTTP Verb       | POST                                                                        |
+-----------------+-----------------------------------------------------------------------------+
| URL             | ``/api/1/authentication/persistent_login/:id/refresh_token``                |
+-----------------+-----------------------------------------------------------------------------+
| Response Formats| ``JSON``                                                                    |
+-----------------+-----------------------------------------------------------------------------+

----------
Parameters
----------

+-------------------------+-------------+---------------+-----------------------------------------+
|| Name                   || Type       || Required     || Description                            |
+=========================+=============+===============+=========================================+
|| accessTokenId          || String     || |checkmark|  || The access token ID of the application |
||                        ||            ||              || making the call                        |
+-------------------------+-------------+---------------+-----------------------------------------+
|| id                     || String     || |checkmark|  || The ID of the persistent login         |
+-------------------------+-------------+---------------+-----------------------------------------+

-------
Example
-------

^^^^^^^
Request
^^^^^^^

.. code-block:: none

   POST .../api/1/authentication/persistent_login/b68c0013-d7b2-4004-8463-d7ba03e15d94/refresh_token

.. code-block:: none

  accessTokenId=e8f5170c-877b-416f-b70f-4b09772f8e2d

^^^^^^^^
Response
^^^^^^^^

``Status 200 OK``

.. code-block:: json

  {
    "tenant": "default",
    "profileId": "5925a68def86951f895cf497",
    "token": "f390e222-c844-44a2-ad38-afa471527101",
    "timestamp": 1495665332589,
    "id": "b68c0013-d7b2-4004-8463-d7ba03e15d94"
  }

---------
Responses
---------

+---------+---------------------------------+----------------------------------------------------+
|| Status || Location                       || Response Body                                     |
+=========+=================================+====================================================+
|| 200    ||                                || See example above.                                |
+---------+---------------------------------+----------------------------------------------------+
|| 500    ||                                || ``{ "message" : "Internal server error" }``       |
+---------+---------------------------------+----------------------------------------------------+
