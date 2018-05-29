Configure data sources
=========================

To let all the BI tools work properly you need to configure DB connection. There are two different options available for the configuration:

- **JNDI** (recommended)
- **JDBC**.
Let’s have a look on these two types.

Connect to your data
--------------------

In order to connect to your data, you have to define a new data source connection. Defining a data source allows Knowage to access data transparently without the need to redefine the connection to the database in case some of its configuration properties change over time. 

Knowage manages two types of data source connections:
- direct JDBC connections, which are directly managed by Knowage;
- connections retrived as JNDI resources, which are managed by the application server on which Knowage is working. This allows the application server to optimize data access, e.g., by defining connection pools.

The second type of connection is the one recommended in real projects.

To add a new connection, first add the relative JDBC driver to the folder KnowageServer-<version>/lib and restart Knowage. Then, login as administrator (user: *biadmin*, password: *biadmin* are the default credential) and select the **Data source** item from the **Data provider** panel in the administrator menu.

   .. figure:: media/image25.png
      :alt: Left: Add a new data source. Right: Data source details.

      Add a new data source, Right: Data source details.

By clicking the **Add** button on the top right corner of the left panel, an empty form will be displayed on the right.

The detail page of each data source (on the right side as shown in the figure above) includes the following properties:

Label
   Mandatory identifier of the data source.
Description
   Description of the data source.
Dialect
   The dialect used to access the database. Supported dialects are: 
      + Oracle
      + SQL Server
      + HyperSQL
      + MySQL
      + PostgreSQL
      + Ingres
      + DB2
      + AS400
Read Only
   Available options are: *Read Only* and *Read-and-write*. In case the data source is defined as read-and-write, it can be used by Knowage to write temporary tables.
Write
   Default If a data source is set as *Write Default* then it is used by Knowage for writing temporary tables also coming from other *Read Only* data sources. Note that each Knowage installation can have only one *Write Default* data source.
Type
   The available options are 
      + **JDBC** If you want to define a direct *JDBC* connection, then you have to also set the following fields:
         - **URL** Database URL. An example for MySQL databases is *jdbc:mysql://localhost:3306/foodmart_key*
         - **User** Database username
         - **Password** Database password.
         - **Driver** Driver class name. An example for MySQL databases is *com.mysql. jdbc.Driver*.
      + **JNDI** If instead you want to define a JNDI connection, fill in the following fields:
         - **Multischema** Available options are *Yes* or *No*. If *Yes*, the JNDI resource full name is calculated at runtime by appending a user’s profile attribute (specified in the *Multischema attribute* field) to the JNDI base name defined in the server.xml, we suppose it has been told at the end of installation or during server configuration.
         - **Schema attribute** The name of the profile attribute that determines the schema name.
         - **JNDI NAME** It depends on the application server. For instance, for Tomcat 7 it has the format java:comp/env/jdbc/<resource_name>. If the data source is multischema, then the string is java:comp/env/jdbc/<prefix>.

Once you have filled the form, you can test the new data source by clicking on the *Test* button at the top right corner of the page and then save it.

Now you are connected to your data and you can start a new Business Intelligence project with Knowage!

Big Data and NoSQL
-------------------

In this section we describe how you can connect Knowage to different Big Data data sources. Plese note that these connections are available for products KnowageBD and KnowagePM.

Hive
~~~~~~

Apache Hive is a data warehouse infrastructure built on top of Hadoop for providing data summarization, query, and analysis. Apache Hive supports analysis of large datasets stored in Hadoop’s HDFS and compatible file systems such as Amazon S3 filesystem. It provides an   SQL-like language called HiveQL with schema on read and transparently converts queries to map/reduce, Apache Tez and Spark. All three execution engines can run in Hadoop YARN.

Every distribution of Hadoop provides its JDBC driver for Hive. We suggest you to use or the Apache one or the one specific of your distribution. In general the JDBC driver for Hive is composed by different .jars, and so you should deploy the JDBC driver with all dependencies in your application server. If you are creating a model you should create a new *Data Source Connection* and import the JDBC driver and all the dependencies.

For example suppose you want to connect to Hive using Apache driver you should include these libraries (according to your Hive version) shown in Figure 4.2.

   |image35|

   Figure 4.2: Libraries to include in the apache driver.

If you forget to add one or more libraries you will have a *NoClassDefFoundError* like this:

.. code-block:: console
   :linenos: inline
   java.lang.NoClassDefFoundError: org/apache/log4j/Level at
   org.slf4j.LoggerFactory.bind(LoggerFactory.java:121) at
   org.slf4j.LoggerFactory.performInitialization(LoggerFactory.java:111) at                                                           |
   org.slf4j.LoggerFactory.getILoggerFactory(LoggerFactory.java:268)  at 
   org.slf4j.LoggerFactory.getLogger(LoggerFactory.java:241) at
   org.slf4j.LoggerFactory.getLogger(LoggerFactory.java:254) at
   org.apache.hive.service.auth.HiveAuthFactory.<clinit>(   
   
+-----------------------------------------------------------------------+
| java.lang.NoClassDefFoundError: org/apache/log4j/Level at             |
| org.slf4j.LoggerFactory.bind(LoggerFactory.java:121) at               |
| org.slf4j.LoggerFactory.performInitialization(LoggerFactory.java      |
|                                                                       |
|    :111) at                                                           |
|    org.slf4j.LoggerFactory.getILoggerFactory(LoggerFactory.java:268)  |
|    at org.slf4j.LoggerFactory.getLogger(LoggerFactory.java:241) at    |
|    org.slf4j.LoggerFactory.getLogger(LoggerFactory.java:254) at       |
|    org.apache.hive.service.auth.HiveAuthFactory.<clinit>(             |
|                                                                       |
|    HiveAuthFactory.java:58) at                                        |
|    org.apache.hive.jdbc.HiveConnection.createBinaryTransport(         |
|                                                                       |
|    HiveConnection.java:393) at                                        |
|    org.apache.hive.jdbc.HiveConnection.openTransport(HiveConnection.  |
|    java:187) at                                                       |
|    org.apache.hive.jdbc.HiveConnection.<init>(HiveConnection.java:163 |
| )                                                                     |
|    at org.apache.hive.jdbc.HiveDriver.connect(HiveDriver.java:105) at |
|    org.eclipse.datatools.connectivity.drivers.jdbc.JDBCConnection.    |
|                                                                       |
|    createConnection(JDBCConnection.java:214) at                       |
|    org.eclipse.datatools.connectivity.DriverConnectionBase.           |
|    internalCreateConnection(DriverConnectionBase.java:105) at         |
|    org.eclipse.datatools.connectivity.DriverConnectionBase.open(      |
|                                                                       |
|    DriverConnectionBase.java:54) at                                   |
|    org.eclipse.datatools.connectivity.drivers.jdbc.JDBCConnection.ope |
| n(                                                                    |
|                                                                       |
|    JDBCConnection.java:73) at                                         |
|    org.eclipse.datatools.connectivity.drivers.jdbc.                   |
|                                                                       |
|    JDBCConnectionFactory.createConnection(JDBCConnectionFactory.java: |
| 53)                                                                   |
|    at org.eclipse.datatools.connectivity.internal.                    |
|                                                                       |
|    ConnectionFactoryProvider.createConnection(ConnectionFactoryProvid |
| er.java                                                               |
+-----------------------------------------------------------------------+



Spark SQL

+------------------------------------------------------------------------+
| :83) at org.eclipse.datatools.connectivity.internal.ConnectionProfile. |
|                                                                        |
| createConnection(ConnectionProfile.java:359)                           |
+------------------------------------------------------------------------+

..

   18

   Code 4.1: NoClassDefFoundError.

   The parameters for the Hive connection are:

-  **Dialect:** Hive QL;

-  **Driver Class:** org.apache.hive.jdbc.HiveDriver (if you are not
   using some specific driver of some distribution. In this case search
   in the documentation of the distribution);

-  **Connection URL:**
   jdbc:hive2://<host1>:<port1>,<host2>:<port2>/dbName;sess\_
   var_list?hive_conf_list#hive_var_list.

..

   Here <host1>:<port1>,<host2>:<port2> is a server instance or a comma
   separated list of server instances to connect to (if dynamic service
   discovery is enabled). If empty, the embedded server will be used.

   A simple example is: jdbc:hive2://192.168.0.125:10000.

Spark SQL
---------

   Spark SQL reuses the Hive front end and metastore, giving you full
   compatibility with existing Hive data, queries and UDFs. Simply
   install it alongside Hive. For the installation of Spark we suggest
   you to look at the spark webstite
   `http://spark.apache.org/. <http://spark.apache.org/>`__ To create a
   connection to the Spark SQL Apache Thrift server you should use the
   same JDBC driver of Hive. So:

-  **Driver Class:**\ org.apache.hive.jdbc.HiveDriver (if you are not
      using some specific driver of some distro. In this case search in
      the documentation of the distro);

-  **Connection URL:**
      jdbc:hive2://<host1>:<port1>,<host2>:<port2>/dbName;sess\_
      var_list?hive_conf_list#hive_var_list.

..

   Look at the Hive section for the details about parameters. The port
   in this case is not the port of Hive but the one of Spark SQL thrift
   server (usually 10001)

HBase
-----

   ApacheHBase\ :sup:`TM` is theHadoop columnar database, a distributed,
   scalable, big data store. Use Apache HBase\ :sup:`TM` when you need
   random, realtime read/write access to your Big Data. This project’s
   goal is the hosting of very large tables (billions of rows, millions
   of columns) atop clusters of commodity hardware. Apache HBase is an
   open-source, distributed, versioned, non-relational database modeled
   after Google’sBigtable (see article “A Distributed Storage System for
   Structured Data”by Chang et al.). Just as Bigtable leverages the
   distributed data Impala

   storage provided by the Google File System, Apache HBase provides
   Bigtable-like capabilities on top of Hadoop and HDFS. HBase doesnt
   provide natively a JDBC driver so a common approach is to use Apache
   Phoenix https://phoenix.apache.org/ as layer to perform SQL on HBase.

   If you use Phoenix you should deploy the JDBC driver of Phoenix on
   the Knowage application server.

   The parameters for the Phoenix connection are:

-  **Dialect:** Hive QL;

-  **Driver Class:** org.apache.phoenix.jdbc.PhoenixDriver;

-  **Connection URL:**
   jdbc:phoenix[:<zookeeperquorum>[:<portnumber>][:<rootnode>
   ][:<principal>][:<keytabfile>]].

..

   The definition of a business model over HBase data using Knowage Meta
   will be available in the next releases.

Impala
------

   Impala (currently an Apache Incubator project) is the open source,
   analytic MPP database for Apache Hadoop. To create a connection to
   Impala you should download the jdbc driver from the Cloudera web site
   and deploy it, with all dependencies, on the application server. The
   definition of the url can be different between versions of the
   driver, please double check on the Cloudera web site.

   Example parameters for Impala connection are:

-  **Dialect:** Hive SQL;

-  **Driver Class:** com.cloudera.impala.jdbc4.Driver;

-  **Connection URL:** jdbc:impala://dn03:21050/default.

MongoDB
-------

   MongoDB is an open-sourcedocument databasethat provides high
   performance, high availability, and automatic scaling. MongoDB
   obviates the need for an Object Relational Mapping (ORM) to
   facilitate development.

   MongoDB is different from the other dbs Knowage can handle, because
   it doesnt provide a JDBC driver, but a java connector. So to create a
   connection to MongoDB you should download the java connector and
   deploy on the Knowage application server (youll find it in the
   connectors web page of MongoDB web site).

   Example parameters for the connection are:

Cassandra

-  **Dialect:** MongoDB;

-  **Driver Class:** mongo;

-  **Connection URL:** localhost:27017/foodamrt.

..

   The definition of a business model over MongoBD data using Knowage
   Meta will be available in the next releases.

Cassandra
---------

   Apache Cassandra is an open source distributed database management
   system designed to handle large amounts of data across many commodity
   servers, providing high availability with no single point of failure.
   Cassandra offers robust support for clusters spanning multiple
   datacenters, with asynchronous masterless replication allowing low
   latency operations for all clients.

   There are different ways to connect Knowage to Cassandra.

   If you are using Datastax Enrterprise you can use Spark SQL connector
   and query Cassandra using pseudo standard SQL
   (`https://github.com/datastax/spark-cassandra-connector/
   blob/master/doc/2_loading.md) <https://github.com/datastax/spark-cassandra-connector/blob/master/doc/2_loading.md>`__

   Another solution is to download the Apache JDBC Driver and query
   Cassandra using the language CQL. Also in this case the JDBC driver
   is composed by different jars, and so you should deploy the JDBC
   driver with all dependencies in your application server.

   An example of Cassandra Apache driver (with dependencies) is:

-  apache-cassandra-clientutil-1.2.6.jar

-  apache-cassandra-thrift-1.2.6.jar

-  cassandra-all-1.2.9.jar

-  cassandra-jdbc-2.1.1.jar

-  guava-15.0.jar

-  jackson-core-asl-1.9.2.jar

-  jackson-mapper-asl-1.9.2.jar

-  libthrift-0.7.0.jar

-  log4j-1.2.16.jar

-  sfl4j-api-1.6.1.jar

-  sfl4j-log4j12-1.6.1.jar

..

   Example parameters for the connection are:

Neo4j

-  **Dialect:** Cassandra;

-  **Driver Class:** org.apache.cassandra.cql.jdbc.CassandraDriver;

-  **Connection URL:** jdbc:cassandra://193.109.207.65:9160/foodmart.

..

   Unless you are using Spark SQL to read from Cassandra, the definition
   of a business model over Cassandra data using Knowage Meta will be
   available in the next releases.

Neo4j
-----

   Neo4j is a graph database management system developed by Neo
   Technology, Inc., described by its developers as an ACID-compliant
   transactional database with native graph storage and processing.
   Neo4j is available in a GPL3-licensed open-source "community
   edition", with online backup and high availability extensions
   licensed under the terms of the Affero General Public License. Neo
   also licenses Neo4j with these extensions under closed-source
   commercial terms. Neo4j is implemented in Java and accessible from
   software written in other languages using the Cypher Query Language.
   Here is a simple example of a cypher query (cast of movies starting
   with T)

+----------------------------------------------------------+
| MATCH (actor:Person)-[:ACTED_IN]->(movie:Movie)          |
|                                                          |
| WHERE movie.title =~ "T.*"                               |
|                                                          |
| RETURN movie.title as title, collect(actor.name) as cast |
|                                                          |
| ORDER BY title ASC LIMIT 10;                             |
+----------------------------------------------------------+

..


   Code 4.2: Cypher query example.

   To Use Neo4J in Knowage you should download the JDBC driver from
   Neo4J web site and than deploy the driver with all dependencies on
   your application server. In Neo4J official website you can find a
   distribution of the JDBC driver with all dependencies included.

   Example parameters for the connection are:

-  **Dialect:** Neo4j;

-  **Driver Class:** org.neo4j.jdbc.Driver;

-  **Connection URL:** jdbc:neo4j://MN03:7474.

..

   The definition of a business model over Neo4j data using Knowage Meta
   will be available in the next releases.

Drill
-----

   Drill is an Apache open-source SQL query engine for Big Data
   exploration. Drill is designed from the ground up to support
   high-performance analysis on the semi-structured and rapidly VoltDB

   evolving data coming from modern Big Data applications, while still
   providing the familiarity and ecosystem of ANSI SQL, the
   industry-standard query language. Drill provides plug-andplay
   integration with existing Apache Hive and Apache HBase deployments.
   To use Drill in Knowage you should download the JDBC driver from
   Apache Drill web site and than deploy the driver with all
   dependencies on your application server.

   Example parameters for the connection are:

-  **Dialect:** Drill;

-  **Driver Class:** org.apache.drill.jdbc.Driver;

-  **Connection URL:** jdbc:drill:zk=maprdemo:5181/drill/demo_mapr.

VoltDB
------

   ltDB is an in-memory database designed by several well-known database
   system researchers, including A.C.M. Turing Award winner Michael
   Stonebraker (who was involved in Ingres and POSTGRES), Sam Madden,
   and Daniel Abadi. It is an ACID-compliant RDBMS which uses a shared
   nothing architecture. It includes both enterprise and community
   editions. The community edition is licensed under the GNU Affero
   General Public License. Additional features in the commercially
   licensed VoltDB Enterprise version include durability, high
   availability, and Export integrations. To use VoltDB in Knowage you
   should download the JDBC driver from VoltDB web site and than deploy
   the driver with all dependencies on your application server.

   Example parameters for the connection are:

-  **Dialect:** Drill;

-  **Driver Class:** org.voltdb.jdbc.Driver;

-  **Connection URL:** jdbc:voltdb://MN05:21212.

Others
------

   Knowage can connect to several other data sources using JDBC drivers
   (for example Big SQL,

   Vertica,) without any modification of the platform. The standard
   approach is to deploy the JDBC driver with all dependencies on the
   application server and than configure a connection in the data source
   catalogue. For big data data sources we suggest you to use HiveQL as
   dialect.
