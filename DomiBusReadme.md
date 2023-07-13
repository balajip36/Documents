---


---

<h2 id="eudamed-domibus-configuration-and-testing">EUDAMED DOMIBUS Configuration and Testing</h2>
<p>This document talks about how you can configure and test domibus, send and receive messages from <strong>EUDAMED</strong><br>
The following docker images are created</p>
<ol>
<li>domibus-tomcat</li>
<li>domibus-red-tomcat</li>
<li>domibus-mysql</li>
<li>domibus-red-mysql</li>
<li>redactivemq</li>
<li>activemq</li>
<li></li>
</ol>
<h2 id="procedure-for-creating-domibus-tomcat-docker-image">Procedure for creating domibus-tomcat docker image</h2>
<ul>
<li>Download the DOMIBUS instance and domibus-msh-distribution from the domibus website</li>
<li>Navigate to conf/domibus.properties</li>
<li>Change the security alias domibus.security.key.private.alias to the corresponding gateway name red_gw or blue_gw</li>
<li>move the jks files from domibus-msh-distribution conf/ domibus to keystores under conf under the domibus instance</li>
<li>run the dockerfile with <code>docker build -t domibus-tomcat .</code> for building the docker image</li>
</ul>
<h2 id="procedure-for-creating-domibus-mysql-docker-image">Procedure for creating domibus-mysql docker image</h2>
<ul>
<li>
<p>Create a docker file with mysql:8 as a base image</p>
</li>
<li>
<p>Add the following sql automation scripts to the docker image</p>
<p>FROM  mysql:8<br>
ENV  SQL_SCRIPTS_HOME /usr/local/sqlscripts<br>
RUN  mkdir  $SQL_SCRIPTS_HOME<br>
COPY ./additionalContent/custom-scripts  $SQL_SCRIPTS_HOME</p>
<p>COPY  mysql-5.1.ddl  $SQL_SCRIPTS_HOME</p>
<p>COPY  mysql-5.1-data.ddl  $SQL_SCRIPTS_HOME</p>
<p>RUN  cp  $SQL_SCRIPTS_HOME/mysql-5.1-createDb.sql /docker-entrypoint-initdb.d/0_mysql-5.1-createDb.sql</p>
<p>RUN  cp  $SQL_SCRIPTS_HOME/mysql-5.1.ddl /docker-entrypoint-initdb.d/1_mysql-5.1.sql</p>
<p>RUN  cp  $SQL_SCRIPTS_HOME/mysql-5.1-data.ddl /docker-entrypoint-initdb.d/2_mysql-5.1-data.sql<br>
WORKDIR /usr/local/sqlscripts<br>
EXPOSE  3306</p>
</li>
</ul>
<h2 id="procedure-for-creating-activemq-docker-image">Procedure for creating ActiveMQ docker image</h2>
<ul>
<li>Download ActiveMQ from <a href="https://activemq.apache.org/">https://activemq.apache.org/</a></li>
<li>Change jettyPort bean property in jetty.xml from localhost to 0.0.0.0</li>
<li>run the <code>docker build -t domibus-tomcat .</code> command</li>
<li>Change the <strong>activeMQ.broker.host</strong> property in domibus.properties to point to the name of     the host of the activemq instance and <strong>activeMQ.brokerName</strong> to localhost</li>
</ul>
<h2 id="run-the-domibus-instance">Run the Domibus instance</h2>
<p>We can run the domibus instance in a separate network within the docker landscape.  Navigate to domibus-blue and open combined.</p>
<p>Run the command docker compose up</p>
<p>This will run the services in the docker container</p>
<h2 id="testing-the-domibus-connectivity">Testing the DomiBus Connectivity</h2>
<ul>
<li>Build the dispatcher application with the command <code>mvn clean install</code></li>
<li>Run the dispatcher application with the command <code>mvn spring-boot:run</code></li>
<li>Build the node-test-client with the command <code>npm run build</code></li>
<li>Run the node-test-client with the command <code>npm run start</code></li>
<li>in the browser tryout localhost:8000. This will send message to the AMQP queue.</li>
<li>Open the Admin screen of the DOMIBUS application upload the pModes from msh-distribution.</li>
<li>Change the hostname and port to the corresponding hostnames and port of the domibus instances in the docker container or externally.</li>
<li>See whether the messages are consumed at the DOMIBUS instance</li>
</ul>

