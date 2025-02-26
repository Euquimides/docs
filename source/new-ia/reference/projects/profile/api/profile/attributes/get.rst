:is-up-to-date: True
:nosearch:

.. _newIa-crafter-profile-api-profile-attributes-get:

==============
Get Attributes
==============

Returns the attributes of a profile.

--------------------
Resource Information
--------------------

.. include:: /includes/profile-api-url-prefix.rst

+----------------------------+-------------------------------------------------------------------+
|| HTTP Verb                 || GET                                                              |
+----------------------------+-------------------------------------------------------------------+
|| URL                       || ``/api/1/profile/:id/attributes``                                |
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
|| id                 || String     || |checkmark|  || The profile's ID                            |
+---------------------+-------------+---------------+----------------------------------------------+
|| attributesToReturn || String     ||              || The name of the attributes to return        |
||                    ||            ||              || (don't specify to return all)               |
+---------------------+-------------+---------------+----------------------------------------------+

-------
Example
-------

^^^^^^^
Request
^^^^^^^

.. code-block:: none

  GET .../api/1/profile/592887d7d4c650213cc2f400/attributes?accessTokenId=e8f5170c-877b-416f-b70f-4b09772f8e2d


^^^^^^^^
Response
^^^^^^^^

``Status 200 OK``

.. code-block:: json

  {
    "firstName": "John",
    "lastName": "Doe"
  }

---------
Responses
---------

+---------+--------------------------------------+-----------------------------------------------+
|| Status || Location                            || Response Body                                |
+=========+======================================+===============================================+
|| 200    ||                                     || See example above.                           |
+---------+--------------------------------------+-----------------------------------------------+
|| 500    ||                                     || ``{ "message" : "Internal server error" }``  |
+---------+--------------------------------------+-----------------------------------------------+
