Logger Component
================

.. seo::
    :description: Instructions for setting up the central logging component in esphomelib.
    :image: file-document-box.png

The logger component automatically logs all log messages through the
serial port and through MQTT topics. By default, all logs with a
severity higher than ``DEBUG`` will be shown. Decreasing the log level
can help with the performance of the application and memory size.

.. code-block:: yaml

    # Example configuration entry
    logger:
      level: DEBUG

Configuration variables:
------------------------

-  **baud_rate** (*Optional*, int): The baud rate to use for the serial
   UART port. Defaults to ``115200``. Set to ``0`` to disable logging via UART.
-  **tx_buffer_size** (*Optional*, string): The size of the buffer used
   for log messages. Decrease this if you’re having memory problems.
   Defaults to 512.
-  **level** (*Optional*, string): The global log level. Any log message
   with a lower severity will not be shown. Defaults to DEBUG.
-  **logs** (*Optional*, mapping): Manually set the log level for a
   specific component or tag. See :ref:`Manual Log Levels for more
   information <logger-manual_tag_specific_levels>`.
-  **id** (*Optional*, :ref:`config-id`): Manually specify the ID used for code generation.

.. _logger-log_levels:

Log Levels
----------

Possible log levels are (sorted by severity):

-  ``NONE``

  - No messages are logged.

-  ``ERROR``

  - With this log level, only errors are logged. Errors are issues that prevent the ESP from working
    correctly. Color: red

-  ``WARN``

  - With this log level, warnings and errors are logged. Warnings are issues like invalid readings from
    sensors that esphomelib can recover from. Color: yellow

-  ``INFO``

  - With this log level, everything up to info messages are logged; so errors, warnings and info. Color: green

-  ``DEBUG`` (**Default**)

  - Everything up to this log level is logged. Debug messages include the current readings from a sensor
    and status messages. Color: cyan

-  ``VERBOSE``

  - Like debug, but a few more messages that are usually deemed to be spam are also included. Color: grey

-  ``VERY_VERBOSE``

  - All internal messages are logged. Including all the data flowing through data buses like
    i2c, spi or uart. Color: white

.. _logger-manual_tag_specific_levels:

Manual Tag-Specific Log Levels
------------------------------

If some component is spamming the logs and you want to manually set the
log level for it, first identify the tag of the log messages in question
and then disable them in your configuration.

Suppose we want to have verbose log messages globally, but the MQTT
client spams too much. In the following example, we’d first see that the
tag of the MQTT client is ``mqtt.client`` (before the first colon) and
the tag for MQTT components is ``mqtt.component``.

.. figure:: images/logger-manual_log_level.png

Next, we can manually set the log levels in the configuration like this:

.. code-block:: yaml

    logger:
      level: VERBOSE
      logs:
        mqtt.component: DEBUG
        mqtt.client: ERROR

Please note that the global log level determines what log messages are
saved in the binary. So for example a ``INFO`` global log message will
purge all ``DEBUG`` log statements from the binary in order to conserve
space. This however means that you cannot set tag-specific log levels
that have a lower severity than the global log level.

.. _logger-log_action:

``logger.log`` Action
---------------------

Print a formatted message to the logs.

In the ``format`` option, you can use ``printf``-style formatting (see :ref:`display-printf`).

.. code-block:: yaml

    on_...:
      then:
        - logger.log: "Hello World"

        # Formatted:
        - logger.log:
            format: "The temperature sensor reports value %.1f and humidity %.1f"
            args: [ 'id(temperature_sensor).state', 'id(humidity_sensor).state' ]

Configuration options:

-  **format** (**Required**, string): The format for the message in :ref:`printf-style <display-printf>`.
-  **args** (*Optional*, list of :ref:`lambda <config-lambda>`): The optional arguments for the
   format message.
-  **level** (*Optional*, string): The :ref:`log level <logger-log_levels>` to print the message
   with. Defaults to ``DEBUG``.
-  **tag** (*Optional*, string): The tag (seen in front of the message in the logs) to print the message
   with. Defaults to ``main``.

See Also
--------

- :doc:`API Reference </api/core/log>`
- `Edit this page on GitHub <https://github.com/OttoWinter/esphomedocs/blob/current/esphomeyaml/components/logger.rst>`__

.. disqus::
