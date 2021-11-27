# ELK REPOSITORY #
The ELK Stack is an acronym for a combination of three widely used open source projects: E=Elasticsearch (based on Lucene), L=Logstash, and K=Kibana. With the addition of Beats, the ELK Stack is now known as the Elastic Stack.

# Architecture of the folder #
 - .devops/ ==> Where we put all devops stuff
 - .devops/ansible ==> Where we put all yml files for ansible deployment of ELK stack

# Infrastructure #
Elastic cluster is deployed on INFO-TD-E room :

```
|--------------------------------------------------------------------------------------------------------------------------------------|
|tc405-112-01.insa-lyon.fr |  134.214.202.26  | (MASTER NODE) |  (DATA NODE) |   (METRICBEAT)    |  (KIBANA NODE)   | (HEARTBEAT NODE) |
|--------------------------|------------------|---------------|--------------|-------------------|------------------|------------------|
|tc405-112-02.insa-lyon.fr |  134.214.202.27  | (MASTER NODE) |  (DATA NODE) |   (METRICBEAT)    |                  |                  |
|tc405-112-03.insa-lyon.fr |  134.214.202.28  | (MASTER NODE) |  (DATA NODE) |   (METRICBEAT)    |                  |                  |
|tc405-112-04.insa-lyon.fr |  134.214.202.29  | (MASTER NODE) |  (DATA NODE) |   (METRICBEAT)    |                  |                  |
|tc405-112-05.insa-lyon.fr |  134.214.202.30  | (MASTER NODE) |  (DATA NODE) |   (METRICBEAT)    |                  |                  |
|--------------------------|------------------|---------------|--------------|-------------------|------------------|------------------|
|tc405-112-06.insa-lyon.fr |  134.214.202.31  | (WORKER NODE) |  (DATA NODE) |   (METRICBEAT)    |                  |                  |
|tc405-112-07.insa-lyon.fr |  134.214.202.32  | (WORKER NODE) |  (DATA NODE) |   (METRICBEAT)    |                  |                  |
|tc405-112-08.insa-lyon.fr |  134.214.202.33  | (WORKER NODE) |  (DATA NODE) |   (METRICBEAT)    |                  |                  |
|tc405-112-09.insa-lyon.fr |  134.214.202.34  | (WORKER NODE) |  (DATA NODE) |   (METRICBEAT)    |                  |                  |
|tc405-112-10.insa-lyon.fr |  134.214.202.35  | (WORKER NODE) |  (DATA NODE) |   (METRICBEAT)    |                  |                  |
|tc405-112-11.insa-lyon.fr |  134.214.202.36  | (WORKER NODE) |  (DATA NODE) |   (METRICBEAT)    |                  |                  |
|tc405-112-12.insa-lyon.fr |  134.214.202.37  | (WORKER NODE) |  (DATA NODE) |   (METRICBEAT)    |                  |                  |
|tc405-112-13.insa-lyon.fr |  134.214.202.38  | (WORKER NODE) |  (DATA NODE) |   (METRICBEAT)    |                  |                  |
|tc405-112-14.insa-lyon.fr |  134.214.202.39  | (WORKER NODE) |  (DATA NODE) |   (METRICBEAT)    |                  |                  |
|tc405-112-15.insa-lyon.fr |  134.214.202.40  | (WORKER NODE) |  (DATA NODE) |   (METRICBEAT)    |                  |                  |
|--------------------------------------------------------------------------------------------------------------------------------------|
```

# MUST Have #
In order to connect to infrastructure, you have to be connected with INSA VPN.

You also need to connect to one node, ex: tc405-112-15.insa-lyon.fr, and copy/paste your rsa key:
```
mkdir /home/<YOUR_USERNAME>/.ssh
exit
```
From your local computer:
```
scp ~/.ssh/id_rsa.pub tc405-112-15.insa-lyon.fr:/home/<YOUR_USERNAME>/.ssh/authorized_keys
```

On your local computer, you also need to add this config file for ssh in order to deploy this cluster project:
```
vim /home/<YOUR_USERNAME>/.ssh/config
```
and add this into it:
```
Host *
  HostkeyAlgorithms +ssh-rsa
  PubkeyAcceptedKeyTypes +ssh-rsa
  IdentityFile ~/.ssh/id_rsa.pub

Host tc405-112-01.insa-lyon.fr
    User <YOUR_USERNAME>
    LocalForward 5602 localhost:5602
Host tc405-112-02.insa-lyon.fr
    User <YOUR_USERNAME>
Host tc405-112-03.insa-lyon.fr
    User <YOUR_USERNAME>
Host tc405-112-04.insa-lyon.fr
    User <YOUR_USERNAME>
Host tc405-112-05.insa-lyon.fr
    User <YOUR_USERNAME>
Host tc405-112-06.insa-lyon.fr
    User <YOUR_USERNAME>
Host tc405-112-07.insa-lyon.fr
    User <YOUR_USERNAME>
Host tc405-112-08.insa-lyon.fr
    User <YOUR_USERNAME>
Host tc405-112-09.insa-lyon.fr
    User <YOUR_USERNAME>
Host tc405-112-10.insa-lyon.fr
    User <YOUR_USERNAME>
Host tc405-112-11.insa-lyon.fr
    User <YOUR_USERNAME>
Host tc405-112-12.insa-lyon.fr
    User <YOUR_USERNAME>
Host tc405-112-13.insa-lyon.fr
    User <YOUR_USERNAME>
Host tc405-112-14.insa-lyon.fr
    User <YOUR_USERNAME>
Host tc405-112-15.insa-lyon.fr
    User <YOUR_USERNAME>
```
Now you can ssh with no password like this:
```
ssh tc405-112-15.insa-lyon.fr
```
# Deployment #
In order to deploy our ES cluster (Elasticsearch and Kibana) follow this step:

- Step1:
```
cd .devops/ansible/
```
- Step2:
```
ansible-playbook playbook.yml -i inventory/insa -u <YOUR_USERNAME>
```
- Step3: Status of the cluster
```
curl -XGET '<host_ip>:9201/_cluster/health?pretty'
curl -XGET '<host_ip>:9201/_cluster/stats?human&pretty'
```

# ES Endpoints #
IMPORTANT:
- Elasticsearch port number: 9201
- Elasticsearch discovery node port number: 9301
- Elasticsearch logs in /tmp/elk/var/log/elasticsearch
- Elasticsearch data are store in /tmp/elk/var/data/elasticsearch
- Kibana port number: 5602
- Logstash access: 134.214.202.29:5044

Access to kibana dashboard:
```
ssh tc405-112-01.insa-lyon.fr
```
Now you can go to your browser and type, http://localhost:5602

Access to elasticsearch DB:
```
ssh <host>
```
```
curl http://<host_ip>:9201/
```
This should return:
```
{
  "name" : "tc405-112-01.insa-lyon.fr",
  "cluster_name" : "es_cluster",
  "cluster_uuid" : "10LdBNvZROiPtR4dK6vVeQ",
  "version" : {
    "number" : "7.2.0",
    "build_flavor" : "default",
    "build_type" : "tar",
    "build_hash" : "508c38a",
    "build_date" : "2019-06-20T15:54:18.811730Z",
    "build_snapshot" : false,
    "lucene_version" : "8.0.0",
    "minimum_wire_compatibility_version" : "6.8.0",
    "minimum_index_compatibility_version" : "6.0.0-beta1"
  },
  "tagline" : "You Know, for Search"
}
```
Write fake data to ES cluster:
```
curl -X POST -H "Content-Type: application/json" -H "Cache-Control: no-cache" -d '{
    "user" : "FloppyTestUser",
    "post_date" : "2021-11-23T12:30:00",
    "message" : "trying out Elasticsearch"
    }' "http://<host_ip>:9201/sampleindex/sampletype/"
```
This should return:
```
{"_index":"sampleindex","_type":"sampletype","_id":"Mi8Y9nwBV6GKKp_U0rbx","_version":1,"result":"created","_shards":{"total":2,"successful":1,"failed":0},"_seq_no":0,"_primary_term":1}
```
Read fake data to ES cluster:
```
curl http://<host_ip>:9201/sampleindex
```
This should return:
```
{"sampleindex":{"aliases":{},"mappings":{"properties":{"message":{"type":"text","fields":{"keyword":{"type":"keyword","ignore_above":256}}},"post_date":{"type":"date"},"user":{"type":"text","fields":{"keyword":{"type":"keyword","ignore_above":256}}}}},"settings":{"index":{"creation_date":"1636216392608","number_of_shards":"1","number_of_replicas":"1","uuid":"rG_yAsYeREKiefslzrJPiw","version":{"created":"7020099"},"provided
```

# Monitoring #
In order to monitore our ES cluster, you have to be connected to VPN before launch monitoring. Make sure you already installed docker and docker-compose on your local computer.
```
cd monitoring
docker-compose up -d
```
Verify that your container are up
```
docker ps
```
Now you can go on http://localhost:3000/ , login= admin and password = pass

