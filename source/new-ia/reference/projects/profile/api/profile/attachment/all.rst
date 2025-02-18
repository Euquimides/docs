:is-up-to-date: True
:nosearch:

.. _newIa-crafter-profile-api-profile-attachment-all:

===========================
Get All Profile Attachments
===========================

Gets all attachments for the given profile.

--------------------
Resource Information
--------------------

.. include:: /includes/profile-api-url-prefix.rst

+----------------------------+-------------------------------------------------------------------+
|| HTTP Verb                 || GET                                                              |
+----------------------------+-------------------------------------------------------------------+
|| URL                       || ``/api/1/profile/:id/attachments/``                              |
+----------------------------+-------------------------------------------------------------------+
|| Response Formats          || ``JSON``                                                         |
+----------------------------+-------------------------------------------------------------------+

----------
Parameters
----------

+-------------------+-------------+---------------+----------------------------------------------+
|| Name             || Type       || Required     || Description                                 |
+===================+=============+===============+==============================================+
|| accessTokenId    || String     || |checkmark|  || The access token ID of the application      |
||                  ||            ||              || making the call                             |
+-------------------+-------------+---------------+----------------------------------------------+
|| id               || String     || |checkmark|  || The profile's ID                            |
+-------------------+-------------+---------------+----------------------------------------------+

-------
Example
-------

^^^^^^^
Request
^^^^^^^

.. code-block:: none

  GET .../api/1/profile/59284659d4c650213cc2f3fc/attachments?accessTokenId=e8f5170c-877b-416f-b70f-4b09772f8e2d


^^^^^^^^
Response
^^^^^^^^

``Status 200 OK``

.. code-block:: json
  :linenos:

  [
    {
      "md5": "498a1e16be56873ef53a1a61295d1781",
      "contentType": "image/jpeg",
      "fileSize": "22.6 KB",
      "fileName": "picture1",
      "fileSizeBytes": 23193,
      "id": "59285cd3d4c650213cc2f3fd"
    }
  ]

---------
Responses
---------

+---------+-----------------------------------+--------------------------------------------------+
|| Status || Location                         || Response Body                                   |
+=========+===================================+==================================================+
|| 200    ||                                  || See example above.                              |
+---------+-----------------------------------+--------------------------------------------------+
|| 500    ||                                  || ``{ "message" : "Internal server error" }``     |
+---------+-----------------------------------+--------------------------------------------------+
