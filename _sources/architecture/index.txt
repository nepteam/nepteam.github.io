Architecture
############

High-level Service Classification
*********************************

.. blockdiag::

    blockdiag {
        Application -> WebService
        Application -> MessagingService
        AdminWebInterface -> WebService
        AdminWebInterface -> MessagingService
    }

Notes:

- The web interface will be implemented in Python as a Flask application via WSGI with Apache 2.
- The web API service will be implemented in Java with Spring Framework.
- The message service, including web socket service, will be implemented in Python with Tori.
- The data store will be MySQL for user directory and either Riak or LevelDB for data and logs.

.. toctree::
   :glob:
   :titlesonly:
   :maxdepth: 2

   *
