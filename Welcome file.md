## EUDAMED DOMIBUS Configuration and Testing
This document talks about how you can configure and test domibus, send and receive messages from **EUDAMED**
The following docker images are created
 1. domibus-tomcat
 2. domibus-red-tomcat
 3. domibus-mysql
 4. domibus-red-mysql
 5. redactivemq
 6. activemq
 7. 
## Procedure for creating domibus-tomcat docker image

 - Download the DOMIBUS instance and domibus-msh-distribution from the domibus website
 - Navigate to conf/domibus.properties
 - Change the security alias domibus.security.key.private.alias to the corresponding gateway name red_gw or blue_gw
 -  move the jks files from domibus-msh-distribution conf/ domibus to keystores under conf under the domibus instance
 - run the dockerfile with `docker build -t domibus-tomcat .` for building the docker image

## Procedure for creating domibus-mysql docker image

- Create a docker file with mysql:8 as a base image
- Add the following sql automation scripts to the docker image

    FROM  mysql:8
    ENV  SQL_SCRIPTS_HOME /usr/local/sqlscripts
    RUN  mkdir  $SQL_SCRIPTS_HOME
    COPY ./additionalContent/custom-scripts  $SQL_SCRIPTS_HOME
    
    COPY  mysql-5.1.ddl  $SQL_SCRIPTS_HOME
    
    COPY  mysql-5.1-data.ddl  $SQL_SCRIPTS_HOME
    
    RUN  cp  $SQL_SCRIPTS_HOME/mysql-5.1-createDb.sql /docker-entrypoint-initdb.d/0_mysql-5.1-createDb.sql
    
    RUN  cp  $SQL_SCRIPTS_HOME/mysql-5.1.ddl /docker-entrypoint-initdb.d/1_mysql-5.1.sql
    
    RUN  cp  $SQL_SCRIPTS_HOME/mysql-5.1-data.ddl /docker-entrypoint-initdb.d/2_mysql-5.1-data.sql
    WORKDIR /usr/local/sqlscripts
    EXPOSE  3306


## Procedure for creating ActiveMQ docker image

 - Download ActiveMQ from https://activemq.apache.org/
 - Change jettyPort bean property in jetty.xml from localhost to 0.0.0.0
 - run the `docker build -t domibus-tomcat .` command 
 - Change the **activeMQ.broker.host** property in domibus.properties to point to the name of     the host of the activemq instance and **activeMQ.brokerName** to localhost

## Run the Domibus instance

We can run the domibus instance in a separate network within the docker landscape.  Navigate to domibus-blue and open combined. 

Run the command docker compose up

This will run the services in the docker container

## Testing the DomiBus Connectivity

 - Build the dispatcher application with the command `mvn clean install`
 - Run the dispatcher application with the command `mvn spring-boot:run`
 - Build the node-test-client with the command `npm run build`
 - Run the node-test-client with the command `npm run start`
 - in the browser tryout localhost:8000. This will send message to the AMQP queue. 
 - Open the Admin screen of the DOMIBUS application upload the pModes from msh-distribution.
 - Change the hostname and port to the corresponding hostnames and port of the domibus instances in the docker container or externally.
 - See whether the messages are consumed at the DOMIBUS instance


<!--stackedit_data:
eyJoaXN0b3J5IjpbLTEzNTY0NzYxOThdfQ==
-->