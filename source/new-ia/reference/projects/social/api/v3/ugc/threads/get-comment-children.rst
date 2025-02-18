:is-up-to-date: True
:nosearch:

.. _newIa-crafter-social-api-ugc-threads-get-children:

====================
Get Comment Children
====================

Returns all children for a given comment.

--------------------
Resource Information
--------------------

.. include:: /includes/social-api-url-prefix.rst

+--------------------------+---------------------------------------------------------------------+
|| HTTP Verb               || GET                                                                |
+--------------------------+---------------------------------------------------------------------+
|| URL                     || ``/api/3/threads/:id/comments/:commentId/children``                |
+--------------------------+---------------------------------------------------------------------+
|| Response Formats        || ``JSON``                                                           |
+--------------------------+---------------------------------------------------------------------+

----------
Parameters
----------

+----------------+----------+---------------+--------------------------------------------+
|| Name          || Type    || Required     || Description                               |
+================+==========+===============+============================================+
|| context       || String  || |checkmark|  || The ID of the Social Context              |
+----------------+----------+---------------+--------------------------------------------+
|| id            || String  || |checkmark|  || The ID of the thread                      |
+----------------+----------+---------------+--------------------------------------------+
|| commentId     || String  || |checkmark|  || The ID of the comment                     |
+----------------+----------+---------------+--------------------------------------------+
|| recursive     || Integer ||              || Levels of comments to return              |
+----------------+----------+---------------+--------------------------------------------+
|| pageNumber    || Integer ||              || Page number to return                     |
+----------------+----------+---------------+--------------------------------------------+
|| pageSize      || Integer ||              || Comments per page                         |
+----------------+----------+---------------+--------------------------------------------+
|| childrenCount || Integer ||              || Amount of children to return              |
+----------------+----------+---------------+--------------------------------------------+
|| sortBy        || List    ||              || List of fields to order by                |
+----------------+----------+---------------+--------------------------------------------+
|| sortOrder     || List    ||              || List of sort orders for each field        |
+----------------+----------+---------------+--------------------------------------------+

-------
Example
-------

^^^^^^^
Request
^^^^^^^

.. code-block:: none

  GET .../api/3/threads/Welcome/comments/59678d3f300426156e21df50/children?context=f5b143c2-f1c0-4a10-b56e-f485f00d3fe9

^^^^^^^^
Response
^^^^^^^^

``Status 200 OK``

.. code-block:: json
  :linenos:

  {
    "total": 1,
    "pageSize": 666,
    "pageNumber": 0,
    "watched": false,
    "comments": [
      {
        "ancestors": [
          "59678d3f300426156e21df50"
        ],
        "targetId": "Welcome",
        "subject": "",
        "body": "This is a response.",
        "createdBy": "59667e8abd4787992596ba6b",
        "lastModifiedBy": "59667e8abd4787992596ba6b",
        "createdDate": "2017-07-13T14:15Z",
        "lastModifiedDate": "2017-07-13T14:15Z",
        "anonymousFlag": false,
        "attributes": {},
        "children": [],
        "attachments": [],
        "moderationStatus": "UNMODERATED",
        "votesUp": [],
        "votesDown": [],
        "flags": [],
        "_id": "5967d4c6300426156e21df57"
      }
    ]
  }

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
|| 404    ||                               | .. code-block:: json                                |
||        ||                               |                                                     |
||        ||                               |   { "message" : "Unable to find ugc with            |
||        ||                               |   id ..." }                                         |
+---------+--------------------------------+-----------------------------------------------------+
|| 500    ||                               || ``{ "message" : "Internal server error" }``        |
+---------+--------------------------------+-----------------------------------------------------+
