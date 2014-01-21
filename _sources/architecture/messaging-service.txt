Messaging Service
#################

.. blockdiag::

    blockdiag {
        MessagingService -> WebService
        MessagingService -> AMQP
        MessagingService -> Cache
    }

Prerequisite
============

- Python*
- Tori Framework / Tornado Framework (for WebSocket)
- RabbitMQ
.. - MySQL

.. note::

    ...

More is TBD.