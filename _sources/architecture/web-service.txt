Web Service
###########

.. contents::

How it normally works
*********************

The API will be implemented as JSON RPC.

Please note that all requests to the API will require an **application access
token set** which only the web interface (only via a user with the **master
permit**) or the command line interface can manage the token.

The API will always expect all incoming requests to have a header **X-WSAPI-TOKEN**
with an **application access token set**.

If the application access token set is not present or invalid, the service will
return an empty HTTP-403 response.

.. note::

    In the early stage of development, the usage of application access token sets
    will not be enforced.

Security
********

POST /authentication
====================

Perform the user authentication

Request
-------

Format
~~~~~~

.. code-block:: javascript

    {
        "key": E_MAIL_AS_KEY,
        "password": PASSWORD
    }

Example
~~~~~~~

.. code-block:: javascript

    {
        "key": "ramen@food.org",
        "password": "magic-word"
    }

Response
--------

This API returns an **empty HTTP-200** response if succeeded.

This API returns an **empty HTTP-400** response if the parameter ``key`` (as e-mail address) is invalid or any fields are empty or empty string.

This API returns an **empty HTTP-401** response if the authentication fails.

This API returns an **empty HTTP-403** response if the token is invalid.

GET /users
==========

Retrieve the list of users or run the case-insensitive search for users by alias, full name or e-mail address.

Parameters via Query String
---------------------------

===== ======= ======== ======= ========================================================
Key   Type    Required Default Description
===== ======= ======== ======= ========================================================
page  int, >0 No       1       The page number
size  int, >0 No       8       The number of results per page
query str     No       \-      The search query for alias, full name or e-mail address.
===== ======= ======== ======= ========================================================

Response
--------

The result will always be in a JSON-formatted HTTP-200 response like the following.

.. code-block:: javascript

    {
        "query": STR_GIVEN_QUERY_OR_NULL,
        "page": INT_PAGE_NUMBER,
        "size": NUMBER_OF_RESULT_PER_PAGE,
        "list": [
            {
                "id": USER_1_ID,
                "url": URL_TO_USER_1
            },
            {
                "id": USER_2_ID,
                "url": URL_TO_USER_2
            },
            //...
            {
                "id": USER_N_ID,
                "url": URL_TO_USER_N
            }
        ]
    }

.. note:: The search result will order by e-mail address, alias and full name.

GET /users/ID_OR_EMAIL_ADDRESS
==============================

Retrieve the user information (without security-sensitive information) by an
identifier or an e-mail address.

The parameter ``ID_OR_EMAIL_ADDRESS`` can be either an e-mail address (by pattern)
or a user (unique) identifier.

Response
--------

The result will always be in a JSON-formatted HTTP-200 response like the following.

.. code-block:: javascript

    {
        "_method": ("identifier", "email")
        // non-security-sensitive user properties
        "id": USER_IDENTIFIER
    }

PUT /users/ID_OR_EMAIL_ADDRESS
==============================

TBD

DELETE /users/ID_OR_EMAIL_ADDRESS
=================================

Delete the user by an identifier or an e-mail address.

The parameter ``ID_OR_EMAIL_ADDRESS`` can be either an e-mail address (by pattern)
or a user (unique) identifier.

Response
--------

The result will always be in an empty HTTP-200 response if the user exists.

The result will always be in an empty HTTP-410 response if the user does not exists.