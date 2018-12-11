
# docker-elk

## Overview
Complete functional replica of an ELK Stack used on the MDTP Platform to allow quick testing of any changes. This has been built using 

## Docker Compose Stack

### Environment Variables
The provided .env file sets the ELK version used by the corresponding docker images and needs to be sourced before running the stack. The version number can be adjusted as required by setting the variable:

```
ES_TAG=<version number>
```
### Running the stack
Within the docker-compose directory of the repo, the docker-compose file can by run in the terminal to build the stack 
```
# For a single data node 
docker-compose up

# For a multiple data nodes, scale up to the number required
docker-compose up --scale elasticsearch-data=<number of data nodes>
```

### Healthcheck
In a seperate terminal, the kibana shell can be accessed and within this a healthcheck can be curled, running the commands:
```
docker exec -it kibana bash
curl -v https://elasticsearch-query:9200/_cluster/health?pretty
```

The response should contain a json similar to below, showing the total nodes within the elasticsearch cluster (2 ingest + 1 query + no. of data nodes scaled when composed up). Along with other ifnroamtion about the cluster.
```
{
  "cluster_name" : "elasticsearch",
  "status" : "green",
  "timed_out" : false,
  "number_of_nodes" : 6,
  "number_of_data_nodes" : 3,
  "active_primary_shards" : 10,
  "active_shards" : 20,
  "relocating_shards" : 0,
  "initializing_shards" : 0,
  "unassigned_shards" : 0,
  "delayed_unassigned_shards" : 0,
  "number_of_pending_tasks" : 0,
  "number_of_in_flight_fetch" : 0,
  "task_max_waiting_in_queue_millis" : 0,
  "active_shards_percent_as_number" : 100.0
}
```

Alternatively via the kibana app, enabling monitoring will also show health information about the elasticsearch cluster.

## Kubernetes Stack

The ELK has also been developed for deployment using kubernetes and can be found within the 'k8' directory, this uses a modified and simplifed version of the docker-compose file used previously.

The ELK version images have been hard coded as part of the docker-compose file, so any changes have to made throughout the file as required. An .env file does not currently work here.

### Deploying the stack

Whilst using the kubernetes bundled service with docker, ensure that kubernetes is enabled (and kubernetes is selected for the orchestrator, not swarm) as part of the preferences of the docker app and that it is running.

Within the k8 directory of the repo, the stack can be deployed using the command below in the terminal. \<stack-name> can be defined arbitrarily.

```
docker stack deploy --compose-file k8-docker-compose.yml <stack-name>
```

This sets up a single node cluster, which contains pods and containers with:
logstash pods for client interaction and an ingest points, redis, singular elasticsearch ingest, data and query nodes, and a kibana container.

### Checking Deployment

Successful deployment of the stack can be monitored as the pods are initialised and after by running the command 
```
docker stack services <stack-name>
```

Similarly using kubectl the service deployed can be checked and the pods 
```
kubectl get services
kubectl get pods
```

### Healthcheck
In a seperate terminal, the kibana shell can be accessed and within this a healthcheck can be curled, running the commands:

```
docker exec -it <kibana-docker-container-id> bash
curl -v http://elasticsearch-query:9200/_cluster/health?pretty
```

The response should contain a json similar to below, showing 3 nodes within the elasticsearch cluster (1 ingest + 1 query + 1 data). Along with other ifnroamtion about the cluster.
```
{
  "cluster_name" : "elasticsearch",
  "status" : "green",
  "timed_out" : false,
  "number_of_nodes" : 3,
  "number_of_data_nodes" : 1,
  "active_primary_shards" : 8,
  "active_shards" : 8,
  "relocating_shards" : 0,
  "initializing_shards" : 0,
  "unassigned_shards" : 5,
  "delayed_unassigned_shards" : 0,
  "number_of_pending_tasks" : 0,
  "number_of_in_flight_fetch" : 0,
  "task_max_waiting_in_queue_millis" : 0,
  "active_shards_percent_as_number" : 61.53846153846154
}
```

Alternatively via the kibana app, enabling monitoring will also show health information about the elasticsearch cluster.

## Accessing Kibana
When composed (by docker-compose) or the stack is deployed (with k8s), the kibana app can be accessed by visiting localhost:5601 in a browser

## Adding Logs
Logs are not provided with this repository, once the stack is running, these can be fed in by adding .log files in the local repo directory \<tool>/client/logs (where \<tool> defines either docker-compose or k8s directories).

Once identified by kibana, indicies can then be created in the management panel.

The logs fed into the system should display on kibana's discover panel.