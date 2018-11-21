
# docker-elk

## Overview
Complete functional replica of an ELK Stack used on the MDTP Platform built in docker-compose to allow quick testing of the tech stack.

### Environment Variables
The provided .env file sets the ELK version used by the corresponding docker images and needs to be sourced before running the stack. The version number can be adjusted as required by setting the variable:

```
ES_TAG=<version number>
```
### Running the stack
Within the root directory of the repo, the docker-compose file can by run in ther terminal to build the stack 
```
# For a single data node 
docker-compose up

# For a multiple data nodes, scale up to the value required
docker-compose up --scale elasticsearch-data=<number of data nodes>
```
### Accessing Kibana
When composed, the kibana app can be accessed by visiting localhost:5601 in a browser

### Adding Logs
Once the stack is running, logs can be fed in by adding .log files in the directory client/logs.

Indicies can then be created in the management panel of kibana

The logs fed into the system should display up within the discover panel of kibana.

### Healthcheck
In a seperate terminal, the kibana shell can be accessed and within this a healthcheck can be curled, running the commands:
```
docker exec -it kibana bash
curl -v https://elasticsearch-ingest-1:9200/_cluster/health?pretty
```

The response should contain a json similar to below, showing the total nodes within the elasticsearch cluster (2 ingest + 1 query + no. of data nodes scaled when composed up)
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

Alternatively via the kibana app, by enabling monitoring in the corresponding panel,will also show health information about the elasticsearch cluster

### License
This code is open source software licensed under the [Apache 2.0 License]("http://www.apache.org/licenses/LICENSE-2.0.html").
