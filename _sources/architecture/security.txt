Security Design
###############

Eryri will use ACL (access control list) to control access to any controllers.

The security is classified in two different levels: application and user.

It is currently undetermined how we want to do this. However, one possible implementation is to use [the OAuth 2.0 Authorization Framework](http://oauth.net/2/) as specified in [RFC 6749](http://tools.ietf.org/html/rfc6749).

.. contents::

Application-level Security
==========================

The communication between *web service* and *application*, including the (admin) web interface, is protected to unauthorized access from vulnerable applications.

The *web service* will enforce this level of security for all external requests.

Available Permits
-----------------

* master (authorizes everything, bypass all restrictions, default to the CLI for the web service)
* service_security_write
* service_security_read
* user_security_write
* user_security_read
* user_info_write
* user_info_read
* data_write
* data_read

Authentication and Authorization
--------------------------------

The authentication and authorization solely relied on the combination of OAuth2
access token and application-level permits.

To register the application, the web-service CLI or any applications with the
security_write permit should be able to register an application. Only the
web-service CLI can grant the master and service-security-related permits.

Please note that the admin interface (web) should have the *master* permit.

Here is an example how it works from the web-service CLI.

### Example: authorize/register an application

From the console,

.. code-block:: bash

    eryri-ws application authorize "KotobaApp"
        --description "Japanese Learning App"
        --website http://kotoba.shiroyuki.com
        --callback http://kotoba.shiroyuki.com/authorization
        --permits user_security_write,user_security_read,data_write

then, the user should see the response similar to below.

.. code-block:: javascript

    {
        "access_token": "1234567890wertyuiopasdfgh",
        "secret": "zxcvbnmasdfghjkl"
    }

Example: update the application registry
----------------------------------------

Note: the set of parameters (precondition) is the same as the authorization.

From the console,

.. code-block:: bash

    eryri-ws application update KotobaApp
        --permits "-user_security_write,-user_security_read,user_info_read,+data_read"

In this example, we revoke all user-security permits, grant the user_info_read
permit and data_read permits.

then, the user should see the blank feedback or noisy one if there are errors.

Example: retrieve the application registry
------------------------------------------

Note: the set of parameters (precondition) is the same as the authorization.

From the console,

.. code-block:: bash

    eryri-ws application show KotobaApp

then, the user should see the similar feedback.

.. code-block:: javascript

    {
      "name": "KotobaApp",
      "description": "Japanese Learning App",
      "access_token": "1234567890wertyuiopasdfgh",
      "secret": "zxcvbnmasdfghjkl",
      "website": "http://kotoba.shiroyuki.com",
      "callback": "http://kotoba.shiroyuki.com/authorization",
      "created": "12345678", // unix timestamp
      "updated": "23456789", // unix timestamp
      "permits": [
        "data_write",
        "user_info_read",
        "data_read"
      ]
    }

User-level Security
===================

The user directory should allow:

1. Each user may belong to none or more than one group.
2. Each user may have more than one group.
3. Each user may have zero or more then one permit (access or action), same as group's.
4. Each group may have zero or more then one permit.

Then, the algorithm to validate the access should something like:

.. code-block:: python

    # Pseudo code in Python
    def is_allowed(permits, expected_permits):
      for permit in permits:
        if permit in expected_permits:
          return True

      return False

    def restrict_access(handling_callback, permits=[], group_names=[]):
      user = find(session.username)

      if not user:
        raise HttpError(403)

      try:
        if groups:
          allowed_groups = find_allowed_groups(group_names)

          for allowed_group in allowed_groups:
            if is_allowed(user.group.permits, allowed_group):
              raise PermitAllowedException('group')

        if is_allowed(permits, user.permits):
          raise PermitAllowedException('user')

        raise HttpError(403)
      except PermitAllowedException as e1:
        handling_callback(...)

    @restrict_access('teacher', 'teacher_assistant')
    def question_create(*args, **kwargs):
        """ do something """

Security Permits
----------------

* master (exception, allow everything, highest priority)
* forbidden (exception, forbid from authentication, second highest priority)
* user_create
* user_modify
* user_delete
* user_permission_control
* user_permission_control_for_master_permit
* user_permission_control_for_forbidden_permit
* group_create
* group_modify
* group_delete
* group_permission_control
* group_permission_control_for_master_permit
* group_permission_control_for_forbidden_permit

Application Permits
-------------------

TBD

More readings
=============

- http://oauth.net/2/
- http://tools.ietf.org/html/rfc6749
- https://dev.twitter.com/docs/auth/application-only-auth
- https://dev.twitter.com/docs/auth/oauth/single-user-with-examples