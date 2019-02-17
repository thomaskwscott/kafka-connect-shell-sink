Shell Sink Connector
===================

The Shell sink connector allows you to run shell commands triggered by messages consumed from Kafka.
The connector polls data from Kafka and allows substitution of message elements (topic,key,value) into the resulting shell command.

Quick Start - Writing a file.
===================

.. include:: includes/prerequisites.rst

In this demo we'll use the Shell Sink connector to read messages from Kafka and use the Shell Sink
connector to write the data to a new file.

---------------------------------
Create the Kafka topic
---------------------------------

Before we can read the file we need to create a topic to hold the file data.
From inside a cp-demo broker container (``docker-compose exec kafka1 bash``):

#. Create topics:

    .. sourcecode:: bash

        kafka-topics --zookeeper zookeeper:2181 --topic file.data --create --replication-factor 1 --partitions 1

---------------------------------
Produce messages to the topic
---------------------------------


From inside a cp-demo broker container (``docker-compose exec kafka1 bash``):

#. Create a test file:

    .. sourcecode:: bash

        echo "{\"schema\":null,\"payload\":\"test message\"}" > /etc/kafka/secrets/test_message

#. Create a test script:

    .. sourcecode:: bash

        kafka-console-producer --broker-list kafka1:10091 --topic file.data < /etc/kafka/secrets/test_message


----------------------------
Load the Shell Sink Connector
----------------------------

Now we submit the Shell Sink connector to the cp-demo connect instance. This will fetch the previously
produced messages from Kafka into a file:

#.  From outside the container in the cp-demo root directory:

    .. sourcecode:: bash

        curl -X POST -H "Content-Type: application/json" \
        --cert scripts/security/connect.certificate.pem \
        --key scripts/security/connect.key \
        --tlsv1.2 \
        --cacert scripts/security/snakeoil-ca-1.crt \
        --data '{ \
        "name": "shell-sink", \
        "config": {"connector.class":"uk.co.threefi.connect.shell.ShellSinkConnector", \
                "tasks.max":"1", \
                "shell.command":"echo \"${value}\" >> /etc/kafka/secrets/out.file", \
                "topics":"file.data" \
                }}' \
        https://localhost:8083/connectors

-------------------
Confirm the results
-------------------

From inside a cp-demo Connect container (``docker-compose exec connect bash``):

    .. sourcecode:: bash

        cat /etc/kafka/secrets/out.file


Features
===================

Key/Topic substitutions
^^^^^^^^^^^^^^^^^^^^^^^

The special strings ``${key}``, ``${topic}`` and ``${value}`` can be used in the shell.command property to
inject message data into the command run.


