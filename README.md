# Bringing up the docker compose stack
---
Before starting the compose stack, create the docker volumes and network:
```
docker volume create grafana-volume
docker volume create influxdb-volume

docker network create monitoring_network
```

To bring the stack up in a detached state use `docker compose up -d`

# Integrating with Proxmox
---
1. Create a InfluxDB account via the web console found at port `8086`. An example organization could be your name, and the bucket can be 'proxmox'.
2. In InfluxDB, create an API token with read and write access to the bucket you created. This API key will be used by proxmox and grafana.
3. In Proxmox, find the 'Metric Server' section under the Datacenter. Click Add -> InfluxDB. Here you specify how you want Proxmox to send metrics to Influx.
4. Name it whatever you want, specify Influx's server IP, port `8086`, and select HTTP as the protocol. Enter the organization, bucket, and token, you configured on InfluxDB.
5. Create the connection and verify that your nodes are sending metrics successfully. You can do this by going to Influx's Data Explorer. If you run into problems, view the system logs of the proxmox nodes.
6. Now it's time to setup InfluxDB as a datasource in Grafana. Navigate to Grafana at port `3000` on the server you brought it up at. The default username and password is 'admin' and 'admin'.
7. At Connections -> Data Sources, select InfluxDB.
8. In the data source configuration page, select Flux as the query language, and specify InfluxDB's IP and port in the url section.
9. The only selection under 'Auth' should be 'Skip TLS Verify' if you don't want to use HTTPS or any other authentication mechanisms. If you do, that's outside the scope of this document - sorry.
10. Under 'InfluxDB Details' provide the organization, token, and bucket you setup in Influx, and save the data source.

Now you can setup your dashboards using the InfluxDB data source. Grafana has a selection of Proxmox dashboards you can use. This one is a good place to start: [Proxmox Cluster Dashboard](https://grafana.com/grafana/dashboards/15356-proxmox-cluster-flux/)
