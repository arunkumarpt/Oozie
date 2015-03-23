## Oozie
A Common Solution, Oozie
 It was clear that there was a need for a general purpose system to run multi-stage Hadoop jobs with the following  requirements.
 It should use an adequate and well understood programming model to facilitate its adoption and to reduce developers ramp up time.
 It should be easy to troubleshot and recover jobs when something goes wrong.
 It should be extensible to support new types of jobs.
 It should scale to support several thousands concurrent jobs.
 Jobs should run in a server to increase reliability.
 It should be a multi-tenant service to reduce the cost of operation.
 
## What Exactly Is Oozie?
Oozie is an orchestration system for Hadoop jobs. Oozie is designed to run multi-stage Hadoop jobs as a single job: an Oozie job. Oozie jobs can be configured to run on demand or they can run periodically on a regular basis. Oozie jobs running on demand are called Workflow jobs. Oozie jobs running periodically on a regular basis are called Coordinator jobs. There is a third type of Oozie job called Bundle jobs; a Bundle job is a collection of Coordinator jobs managed as a single job.

## Sample workflow.xml

```
<workflow-app xmlns="uri:oozie:workflow:0.4" name="identity-WF">

  <start to="identity-MR"/>

  <action name="identity-MR">
    <map-reduce>
      <job-tracker>${jobTracker}</job-tracker>
      <name-node>${nameNode}</name-node>
      <prepare>
        <delete path="${exampleDir}/data/output"/>
      </prepare>
      <configuration>
        <property>
          <name>mapred.mapper.class</name>
          <value>org.apache.hadoop.mapred.lib.IdentityMapper</value>
        </property>
        <property>
          <name>mapred.reducer.class</name>
          <value>org.apache.hadoop.mapred.lib.IdentityReducer</value>
        </property>
        <property>
          <name>mapred.input.dir</name>
          <value>${exampleDir}/data/input</value>
        </property>
        <property>
          <name>mapred.output.dir</name>
          <value>${exampleDir}/data/output</value>
        </property>
      </configuration>
    </map-reduce>
    <ok to="success"/>
    <error to="fail"/>
  </action>

  <kill name="fail">
    <message>The Identity Map-Reduce job failed!</message>
  </kill>

  <end name="success"/>

</workflow-app>
```

We must deploy the application to HDFS. The directory structure is

```
app/
 |
 |-- workflow.xml
 
```

Before we can run the Oozie job, we need a job.properties file in our local file system specifying the required parameters for the job and the location of the application package in HDFS:
```
nameNode=hdfs://localhost:8020
jobTracker=localhost:8021
exampleDir=${nameNode}/user/${user.name}/ch01-identity

oozie.wf.application.path=${exampleDir}/app
```

We are now ready to submit the job to Oozie. We use the oozie command-line tool for this:

```
$ export OOZIE_URL=http://localhost:11000/oozie
$ oozie job -run -config target/example/job.properties
job: 0000006-130606115200591-oozie-joe-W
```

For now, we just need to know that with the -run option, we can run an Oozie job. And with -config, we can specify the location of the job.properties file.

Oozie Deployment
In this section, we outline how to deploy and configure Oozie and its related modules in a real system. As explained in the section Oozie Architecture, there are four basic systems in a standard Oozie setup. A short overview of each of those systems will help you understand the Oozie setup and installation better.
The Oozie server runs in a web container (such as Tomcat) and manages Oozie job scheduling and execution. Oozie server is actually a Hadoop client and a database client while it acts as a server for Oozie clients. It also provides an optional web user interface for basic monitoring of jobs. This web UI utilizes a java script library called extJS, which is not apache compliant. In short, Oozie server needs to package all these required libraries into oozie.war. While the Oozie server can be installed on any machine co-located with any other system, we recommend installing it on a separate machine to ensure it’s stability especially in large production systems.
The Oozie client can connect to the Oozie server in multiple ways. The Oozie Command-Line Interface (CLI) is the most popular and convenient way of interacting with the Oozie server. In addition, Oozie server provides a standard REST API, enabling one to write a client application in any language. Last but not the least, Oozie also provides a Java client library that could be used in any JVM-based application. The three types of client applications can run on any machine provided it has network access to the Oozie web service.
Typically, Oozie server works with any recent Hadoop version. Oozie requires two things to connect to any Hadoop system. First, Oozie should use the same version of the Hadoop jars as the installed Hadoop services. Secondly, Hadoop’s core-site.xml should be configured to allow the Oozie service user account (oozie) to act as a proxy user for Hadoop services (details explained later in this chapter).
Oozie supports multiple DBMS for it’s internal use including Derby, PostgreSql, MySQL, and Oracle. Oozie server requires two things to support any particular DBMS. First, the appropriate JDBC jar should be included in the oozie.war file. Secondly, the oozie-site.xml must be configured with the relevant JDBC connection details. The Oozie package by default includes the required jar and configurations for Derby. Deploying and configuring a different DBMS system is the user’s responsibility. Although you can install the DBMS in any machine, we recommend you install the DB server on a separate machine from the Oozie server in production environments.














exercise
--------

poll on a job ID
use oozie and go through all actions

all oozie actions

coordinator based on time/data availability
use java apis to kick off jobs


create dynamic job.properties in java program 





