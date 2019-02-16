.. _sink-config-options:

Shell Sink Configuration Options
-------------------------------


Shell
^^^^^^^^^^

``shell.command``
  The shell command to run.

  * Type: string
  * Importance: high


Retries
^^^^^^^

``max.retries``
  The maximum number of times to retry on errors before failing the task.

  * Type: int
  * Default: 10
  * Valid Values: [0,...]
  * Importance: medium

``retry.backoff.ms``
  The time in milliseconds to wait following an error before a retry attempt is made.

  * Type: int
  * Default: 3000
  * Valid Values: [0,...]
  * Importance: medium