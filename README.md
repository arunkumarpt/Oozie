## Oozie
Secheduling Jobs and create process flow 
 - polling
       waiting for some locations to be existed.
It should be DAG : No loops are permitted
control nodes and action nodes
Derby  and mySQL
There is a Web Server as well


tomcat -> Oozie web app
data -> Derby
Client ->GN : Oozie server URL

Action
Control 

EL constants are counters in Oozie

Decision nodes are based on the EL constants.




predicate and join...

fork for multiple jobs to run based on one action

prepare is to prepare delete kind of stuffs for pig


join  for checking whether completing all the forked job 

Action nodes

every job has an attribute with it.

Oozie support property file. Give all the properties in the property file.

after running the job, we will get a job id.






