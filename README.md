# Welcome to floodplain!

So first, clone the repository somewher:

```bash
git clone https://github.com/floodplainio/floodplain-demo-setup.git
```

This repository only contains one real file, a docker compose file that starts a bunch of containers. I'll list them here to explain their function.

## floodplain/floodplain-postgres-demo

This is an example dataset, added to a debezium postgres instance. It is a regular postgres database (but with change capture enabled) and with an example dataset.

If you have some database client, you can connect to it like this:

jdbc:postgresql://localhost/dvdrental

Username: postgress Password: mysecretpassword

The schema we're using is 'public', and there are about 15 tables with data about (fictional) films, actors, customers and stores.

## floodplain/cdcservice:0.0.1

A tiny Kotlin service that slowly inserts data into the 'payment' table of postgres. Later we will use this to show the change data capture.

If you want to see how it works, the source is here:

https://github.com/floodplainio/floodplain-cdc.git

However one of the big selling points of change data capture is that you don't really need to know how the 'original' application works. It does not matter if it is written in Kotlin or in Cobol, we will only use the change capture feed.

## A Kafka cluster

- image: wurstmeister/zookeeper
- image: wurstmeister/kafka:2.12-2.4.0

We'll also start a kafka cluster, and for that we also need a zookeeper instance. Note that in any kind of serious setup you would not run zookeeper or kafka on a single node, but for demo purposes it is fine.

## Kafka Manager

- image: hlebalbau/kafka-manager:2.0.0.2
-

Kafka manager is a web UI for Kafka, and can be very helpful to figure out what's going on in the kafka cluster.
Once the whole instance is up, point a browser to http://localhost:9000
There, click "Add cluster", give it a name, and add 'zookeeper:2181' as zookeeper instance. Check "Poll consumer information" and "Enable Active OffsetCache" and create the cluster.
This tool helps demystify what's going on in Kafka. Note that sometimes the metrics don't update immediately, be patient.

## MongoDB

- image: mongo:latest

A super basic MongoDB instance, which I use as 'default sink' because it is a very easy to operate, and you can throw any document into it.
To see what's going on, you can install a client like Robo3T.

It is empty, and will be accessible using port 27017 on localhost.

## Kafka Connect

- image: floodplain/debezium-with-mongodb:1.1

This is a debezium connector, enhanced with a HTTP connector, a very experimental Google Sheets connector and a MongoDB connector.

This is the Kafka Connect instance (exposing http://localhost:8083) where it receives commands.
