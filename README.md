# Architecture Overview

Real-time Data Warehouse is popular among internet based companies who has frontend web/mobile app, needs to process the data on real-time bases to achieve a fast data feedback to support the daily business.

The objective of this prototype is to build a stack that could process CDC data captured from a certain relational database source on a real-time manner. log file (txt/json format) generated from front web/mobile app as data source is not discussed in this prototype. 

This Prototype considers RDS Mysql instance to be the CDC data source. Other mainstream datasources are also supported by debezium as well. Detailed information about the source data bases debezium supported could be refered as the link: https://debezium.io/documentation/reference/1.8/connectors/index.html.

The CDC data (*an entire database granularity*) is captured by the Debezium server and then sent to MSK cluster who plays as a buffering pool for those CDC data. 

The flink CDC module originally deployed on EMR cluster plays as consumer of those CDC data. A certain ETL logic might take place during the consuming process by manipulating the sql queries of flink sql table (consumers of the CDC data stored on MSK cluster). *Different from the upper CDC data producing to MSK cluster processing, this downsteam CDC data consuming process is table level granularity operation.*

Finally, these processed CDC data is written into Hudi@EMR cluster.

