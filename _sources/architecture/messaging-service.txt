Messaging Service
#################

.. blockdiag::

    blockdiag {
        MessagingService -> WebService
        MessagingService -> AMQP
        MessagingService -> Cache
    }

More is TBD.