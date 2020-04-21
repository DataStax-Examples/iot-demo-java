# IoT Cassandra Demo Application  

There are a large number of devices that are generating, tracking, and sharing data across a variety of networks. This can be  overwhelming to most data management solutions. Cassandra is a great fit for consuming lots of time-series data that comes directly from users, devices, sensors, and similar mechanisms that exist in a variety of geographic locations.

This is a small demo to show how to insert meter readings for a smart reader. Note: the readings come in through a file with a number of readings per day.

Contributor(s): [Patrick Callaghan](https://github.com/PatrickCallaghan)

## Objectives

* To demonstrate how Cassandra and Datastax can be used to solve IoT data management issues.

## Project Layout

* [SchemaSetup.java](/src/main/java/com/datastax/demo/SchemaSetup.java) - Sets up the smart meter reader schema.
* [Main.java](/src/main/java/com/datastax/smartmeter/Main.java) - Inserts meter reading in table when run.
* [BillingCycleProcessor.java](/src/main/java/com/datastax/smartmeter/BillingCycleProcessor.java) -  Runs a billingCycle, which accummulates usages for a specific time period
* [Aggregate.java](/src/main/java/com/datastax/smartmeter/Aggregate.java) - Runs an day aggregation, which sums the usage for a day.

## How this Works
After setting up the smart reader schema, meter reading are inserted into the table from a generated file with number of readings per day, per customer. A billing cycle can be run to determine accumulated usages for a specific time, period. A sum of the usage for a day may also be obtained.

## Setup and Running

### Prerequisites

* Java 8
* A Cassandra, DSE cluster or Astra database
* Maven to compile and run code

### Running

* **Setup the schema**

Note : This will drop the keyspace "datastax_iot_demo" and create a new one. All existing data will be lost.

To specify contact points use the contactPoints command line parameter e.g.

	-DcontactPoints=192.168.25.100,192.168.25.101

The contact points can take mulitple points in the IP,IP,IP (no spaces).

To create the a single node cluster with replication factor of 1 for standard localhost setup, run the following

To create the schema, run the following

	mvn clean compile exec:java -Dexec.mainClass="com.datastax.demo.SchemaSetup" -DcontactPoints=localhost -Dexec.cleanupDaemonThreads=false

* **Insert meter readings**  

To insert some meter readings, run the following

	mvn clean compile exec:java -Dexec.mainClass="com.datastax.smartmeter.Main" -DcontactPoints=localhost

You can use `-DnoOfCustomers` and `-DnoOfDays` to change the no of customer readings and the no of days (in the past) to be inserted. Defaults are 100 and 180 respectively.

To view the data using cqlsh, run

	select * from datastax_iot_demo.smart_meter_reading where meter_id = 1;

* **Run a  billingCycle**  

To run a billingCycle, which accummulates usages for a specific time period, run

	mvn clean compile exec:java -Dexec.mainClass="com.datastax.smartmeter.BillingCycleProcessor" -DcontactPoints=localhost

To specific billing cycle use `-DbillingCycle` (Default is 7).

* **Run a DAY aggregation**

To run a DAY aggregation, which sums the usage for a day, run

	mvn clean compile exec:java -Dexec.mainClass="com.datastax.smartmeter.Aggregate" -DcontactPoints=localhost

You can use `-DnoOfCustomers` and `-DnoOfDays` to change the no of customer readings and the no of days (in the past) to be aggregated. Defaults are 100 and 180 respectively.

To view the data using cqlsh, run

	select * from datastax_iot_demo.smart_meter_reading_aggregates where meter_id = 1 and aggregatetype ='DAY';

* **Remove tables and schema**  

To remove the tables and the schema, run the following.

    mvn clean compile exec:java -Dexec.mainClass="com.datastax.demo.SchemaTeardown" -DcontactPoints=localhost
