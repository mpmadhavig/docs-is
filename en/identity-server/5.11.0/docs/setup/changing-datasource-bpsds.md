# Changing the Default Datasource for BPS

This document guides you to change the underlying databases that are
used in business process service(BPS) components that support
[workflow management features](../../learn/workflow-management) of the
Wso2 Identity Server.

By default WSO2 Identity Server uses Embedded H2 database as the BPS
datasource. However, you can change this to any database type that is
supported by WSO2 Identity Server.

Following are the sample configuration for each database type.

??? example "PostgreSQL"
    
    1. deployment.toml Configurations.
        ```
        [bps_database.config]
        url = "jdbc:postgresql://localhost:5432/gregdb"
        username = "regadmin"
        password = "regadmin"
        driver = "org.postgresql.Driver"
      
        [bps_database.config.pool_options]
        defaultAutoCommit = false
        commitOnReturn=true
        ```
        
    2. Executing database scripts. 
    
        Navigate to `<IS-HOME>/dbscripts`. Execute the scripts in the following file, against the created database.    
            
          - `<IS-HOME>/dbscripts/bps/bpel/create/postgresql.sql`

??? example "MySQL"

    1. deployment.toml Configurations.
        ```
        [bps_database.config]
        url = "jdbc:mysql://localhost:3306/IAMtest?useSSL=false"
        username = "root"
        password = "root"
        driver = "com.mysql.jdbc.Driver"
        ```
    
    2. Executing database scripts. 

        Navigate to `<IS-HOME>/dbscripts`. Execute the scripts in the following file, against the created database.    
         
          - `<IS-HOME>/dbscripts/bps/bpel/create/mysql.sql`

??? example "DB2"

    1. deployment.toml Configurations.
        ```
        [bps_database.config]
        url = "jdbc:db2://192.168.108.31:50000/test"
        username = "db2inst1"
        password = "db2inst1"
        driver = "com.ibm.db2.jcc.DB2Driver"
        ```    
    2. Executing database scripts. 
    
        Navigate to `<IS-HOME>/dbscripts`. Execute the scripts in the following files, against the created database.    
         
          - `<IS-HOME>/dbscripts/bps/bpel/create/db2.sql`

??? example "MSSQL"

    1. deployment.toml Configurations.
        ```
        [bps_database.config]
        url = "jdbc:sqlserver://localhost:1433;databaseName=test;SendStringParametersAsUnicode=false"
        username = "sa"
        password = "pass#word2"
        driver = "com.microsoft.sqlserver.jdbc.SQLServerDriver"
        ```
    2. Executing database scripts. 
    
        Navigate to <IS-HOME>/dbscripts. Execute the scripts in the following file, against the created database.    
         
          - `<IS-HOME>/dbscripts/bps/bpel/create/mssql.sql`
    

??? example "Oracle"

    1. deployment.toml Configurations.
        ```
        [bps_database.config]
        url = "jdbc:oracle:thin:@localhost:1521/ORCLPDB"
        username = "IS590Test"
        password = "ora12c"
        driver = "oracle.jdbc.OracleDriver"
        ```
    2. Executing database scripts. 
    
        Navigate to <IS-HOME>/dbscripts. Execute the scripts in the following file, against the created database.    
         
          - `<IS-HOME>/dbscripts/bps/bpel/create/oracle.sql`

**Advanced BPS Database Configurations**

Apart from above basic configurations, WSO2 Identity Server supports advanced BPS database configurations such as 
the following. The explanation for the following configurations can be found in the 
[Performance Tuning](../../setup/performance-tuning-recommendations/#jdbc-pool-configuration) documentation.

   ``` toml
   [bps_database.config.pool_options]
   maxActive = "50"
   maxWait = "60000"
   minIdle = "5"
   testOnBorrow = true
   validationInterval = "30000"
   validationQuery = "SELECT 1; COMMIT" #Set this query based on DB type
   defaultAutoCommit=false
   commitOnReturn=true
   ```
