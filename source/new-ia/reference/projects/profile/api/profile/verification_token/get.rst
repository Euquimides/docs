:is-up-to-date: True
:nosearch:

.. _newIa-crafter-profile-api-profile-verification_token-get:

======================
Get Verification Token
======================

Returns the verification token that corresponds to the given ID.

--------------------
Resource Information
--------------------

.. include:: /includes/profile-api-url-prefix.rst

+----------------------------+-------------------------------------------------------------------+
|| HTTP Verb                 || GET                                                              |
+----------------------------+-------------------------------------------------------------------+
|| URL                       || ``/api/1/profile/verification_token/:id``                        |
+----------------------------+-------------------------------------------------------------------+
|| Response Formats          || ``JSON``                                                         |
+----------------------------+-------------------------------------------------------------------+

----------
Parameters
----------

+---------------------+-------------+---------------+----------------------------------------------+
|| Name               || Type       || Required     || Description                                 |
+=====================+=============+===============+==============================================+
|| accessTokenId      || String     || |checkmark|  || The access token ID of the application      |
||                    ||            ||              || making the call                             |
+---------------------+-------------+---------------+----------------------------------------------+
|| id                 || String     || |checkmark|  || The token ID                                |
+---------------------+-------------+---------------+----------------------------------------------+

-------
Example
-------

^^^^^^^
Request
^^^^^^^

.. code-block:: none

  GET .../api/1/profile/verification_token/de60f56d-3a4b-4dec-b798-fb22b2964c14?accessTokenId=e8f5170c-877b-416f-b70f-4b09772f8e2d

^^^^^^^^
Response
^^^^^^^^

``Status 200 OK``

.. code-block:: json

  {
    "tenant": "sample-tenant",
    "profileId": "592715d4d4c650e226b03b62",
    "timestamp": 1495746812910,
    "id": "de60f56d-3a4b-4dec-b798-fb22b2964c14"
  }

---------
Responses
---------

+---------+----------------------------------------+---------------------------------------------+
|| Status || Location                              || Response Body                              |
+=========+========================================+=============================================+
|| 200    ||                                       || See example above.                         |
+---------+----------------------------------------+---------------------------------------------+
|| 500    ||                                       || ``{ "message" : "Internal server error" }``|
+---------+----------------------------------------+---------------------------------------------+
