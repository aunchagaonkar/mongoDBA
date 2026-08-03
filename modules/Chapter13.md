# MongoDB Database Metrics & Monitoring

---

## Lesson 1: Core Metrics

### Question 1
**Which of the following is the ideal value for objects scanned when reviewing the Query Targeting metrics?** *(Select one)*

- **a. 1**
  - **Correct**: The ideal ratio of scanned objects to documents returned is about **1:1**.

- **b. 100**
  - **Incorrect**: This value indicates that 100 objects were scanned and 1 was returned. Ideally, we want a ratio of about 1:1.

- **c. 1000**
  - **Incorrect**: This value indicates that 1000 objects were scanned and 1 was returned. Ideally, we want a ratio of about 1:1.

- **d. 10000**
  - **Incorrect**: This value indicates that 10000 objects were scanned and 1 was returned. Ideally, we want a ratio of about 1:1.

---

### Question 2
**You need to check if the storage subsystem has become a bottleneck. Which of the following storage metrics will help you determine if operations are waiting to be serviced?** *(Select one)*

- **a. Swap Usage**
  - **Incorrect**: Swap Usage is a metric used to determine how much memory is being placed on the swap device. To determine if operations are waiting to be serviced, look at **Disk Queue Depth**.

- **b. Disk Queue Depth**
  - **Correct**: Disk Queue Depth tells us the average length of the queue of requests issued to the disk partition used by MongoDB. This metric will indicate whether or not operations are waiting to be serviced.

- **c. System CPU**
  - **Incorrect**: System CPU displays the CPU usage of all processes on the node. To determine if operations are waiting to be serviced, look at **Disk Queue Depth**.

- **d. Disk Space Percent Free**
  - **Incorrect**: Disk Space Percent Free tells us the percentage of free disk space on the partition used by MongoDB. To determine if operations are waiting to be serviced, look at **Disk Queue Depth**.

---

## Lesson 2: More Metrics

### Question 1
**Which of the following metrics allows you to view the rate at which different types of operations are being performed within your deployment?** *(Select one)*

- **a. Tickets Available**
  - **Incorrect**: Tickets Available indicates the number of concurrent read and write operations available to the MongoDB storage engine. To view the rate at which different operations are being performed, check **Opcounters**.

- **b. Network Traffic**
  - **Incorrect**: Network Traffic metrics provide information about your network performance and include `bytesIn`, `bytesOut`, and `numRequests`. To view the rate at which different operations are being performed, check **Opcounters**.

- **c. Connections**
  - **Incorrect**: Connections represent the total number of open network connections to the database deployment by application, shell clients, and internal MongoDB connections. To view the rate at which different operations are being performed, check **Opcounters**.

- **d. Opcounters**
  - **Correct**: Opcounters measures the rate at which operations are performed, which can help correlate changes in operations with your MongoDB deployment's performance.

---

### Question 2
**Which of the following metrics should you watch if you wish to monitor network traffic for your deployment?** *(Select all that apply)*

- **a. `bytesIn`**
  - **Correct**: `bytesIn` displays the average rate of physical bytes (after any wire compression) sent **to** the database server per second over the selected sample period.

- **b. `bytesOut`**
  - **Correct**: `bytesOut` displays the average rate of physical bytes (after any wire compression) sent **from** the database server per second over the selected sample period.

- **c. `numRequests`**
  - **Correct**: `numRequests` displays the average rate of requests sent to the database server per second over the selected sample period.

- **d. Tickets Available**
  - **Incorrect**: Tickets Available is a metric that indicates the number of concurrent read and write operations available to the WiredTiger storage engine and will **not** help with monitoring network traffic.

---

## Lesson 3: Monitoring M10+

### Code Summary: Monitoring M10+

Review the following code, which demonstrates how to check metrics for your Atlas cluster by using the Atlas CLI.

#### List Processes

Run the following Atlas CLI command to return all running processes for your project:

```bash
atlas processes list
```

#### Retrieve Process Metrics

Run the following Atlas CLI command to retrieve connection metrics for one cluster node:

```bash
atlas metrics processes <cluster id> \
  --period P1D \
  --granularity PT5M \
  --output json \
  --type connections
```

---

### Question 1
**Given the Real-Time Performance Panel metrics displayed, how many connections are currently active?** *(Select one)*

- **a. 15**
  - **Incorrect**: The number of active connections is **47**, found at the top of the Real-Time Performance Panel.

- **b. 100.3**
  - **Incorrect**: The number of active connections is **47**, found at the top of the Real-Time Performance Panel.

- **c. 0**
  - **Incorrect**: The number of active connections is **47**, found at the top of the Real-Time Performance Panel.

- **d. 47**
  - **Correct**: 47 connections are currently active, which is well under the 1500 limit for an M10 cluster.

---

### Question 2
**MongoDB Atlas supports more than 40 metrics for M10+ clusters only.** *(True/False)*

- **a. True**
  - **Correct**: Free and Shared clusters have access to metrics related to connections, network, logical size, and Opcounters, while M10 and above clusters have access to more than 40 metrics.

- **b. False**
  - **Incorrect**: Free and Shared clusters have access to metrics related to connections, network, logical size, and Opcounters, while **M10 and above** clusters have access to more than 40 metrics.

---

## Lesson 4: Configure Alerts

### Code Summary: Configure Alerts

Review the following code, which demonstrates how to configure alert settings with the Atlas CLI.

#### View Alert Settings

Run the following Atlas CLI command to view your project's current alert settings:

```bash
atlas alerts settings list --output json
```

#### Create a New Alert

Run the following command to create a new alert configuration that notifies a user via email when a new user joins the project:

```bash
atlas alerts settings create \
  --event JOINED_GROUP \
  --enabled \
  --notificationIntervalMin 5 \
  --notificationType USER \
  --notificationEmailEnabled \
  --notificationUsername <username> \
  --output json \
  --projectId <project id>
```

#### Update Alert Settings

Run the following command to update an existing alert's settings:

```bash
atlas alerts settings update <alert id> \
  --event JOINED_GROUP \
  --enabled \
  --notificationIntervalMin 5 \
  --notificationType USER \
  --notificationEmailEnabled \
  --notificationUsername <username> \
  --output json \
  --projectId <project id>
```

#### Delete an Alert

Run the following command to delete an alert:

```bash
atlas alerts settings delete <alertConfigId>
```

---

### Question 1
**You are creating new alerts for an M5 (shared-tier) cluster and you successfully configure an alert for a Query Targeting metric. However, the alert isn't being triggered, even though several queries are exceeding the threshold you set. Which of the following explains why?** *(Select one)*

- **a. The Query Targeting alert condition cannot be configured to be different from the default threshold.**
  - **Incorrect**: Alert conditions for any metric can be configured across all cluster tiers, however, shared-tier clusters will only trigger alerts related to the metrics they support: Network, Connections, Logical Size, and Opcounters.

- **b. The Query Target alert condition will not trigger an alert for an M5 cluster.**
  - **Correct**: Alerts can be configured for any metric across all cluster tiers, however, **shared-tier clusters** will only trigger alerts related to the metrics supported by those clusters, including Network, Connections, Logical Size, and Opcounters.

- **c. Alerts cannot be configured for Query Targeting metrics.**
  - **Incorrect**: Alerts can be configured for any metric, but shared-tier clusters will only trigger alerts related to the metrics they support.

---

### Question 2
**You have been granted the `clusterAdmin` role. Will you be able to configure new alert conditions for a specific host?** *(Select one)*

- **a. Yes, the `clusterAdmin` role provides the greatest cluster admin access and will allow you to configure new alerts for a specific host.**
  - **Incorrect**: While the `clusterAdmin` role provides the greatest cluster admin access, you will **not** be able to configure new alerts without the **Project Owner** role.

- **b. No, the Project Owner role is required to configure any new alerts for a specific host.**
  - **Correct**: You must have the **Project Owner** role in order to successfully configure new alerts for a specific host.

---

## Lesson 5: Respond to Alerts

### Code Summary: Respond to Alerts

Review the following code, which demonstrates how to respond to a triggered alert with the Atlas CLI.

#### View Alerts

Run the following command to view your project's alerts:

```bash
atlas alerts list --output json
```

#### Acknowledge an Alert

Run the following command to acknowledge an alert:

```bash
atlas alerts acknowledge <alertId> --comment <comment>
```

#### Unacknowledge an Alert

Run the following command to unacknowledge an alert:

```bash
atlas alerts unacknowledge <alertId>
```

---

### Question 1
**You receive a notification from Atlas that an alert has been triggered. What information is required to successfully acknowledge the alert with the `atlas alerts acknowledge` command?** *(Select one)*

- **a. `metricName`**
  - **Incorrect**: You cannot provide `metricName` as an option when acknowledging an alert. The `atlas alerts acknowledge` command requires **`id`** to successfully acknowledge an alert.

- **b. `comment`**
  - **Incorrect**: `comment` is **optional** when acknowledging an alert. The `atlas alerts acknowledge` command requires **`id`** to successfully acknowledge an alert.

- **c. `hostnameAndPort`**
  - **Incorrect**: You cannot provide `hostnameAndPort` as an option when acknowledging an alert. The `atlas alerts acknowledge` command requires **`id`** to successfully acknowledge an alert.

- **d. `id`**
  - **Correct**: The `atlas alerts acknowledge` command requires **`id`** to successfully acknowledge an alert.

---

### Question 2
**Under which of the following circumstances will an Atlas alert's status change to `CLOSED`?** *(Select one)*

- **a. When the alert is acknowledged**
  - **Incorrect**: Acknowledging an alert will **not** change its status to `CLOSED`. An alert's status will only change to `CLOSED` once the condition that triggered the alert is resolved.

- **b. When an alert is disabled**
  - **Incorrect**: Disabling an alert will **not** mark it as `CLOSED`. An alert's status will only change to `CLOSED` once the condition that triggered the alert is resolved.

- **c. When the alert condition is resolved**
  - **Correct**: An alert's status will only change to `CLOSED` once the **condition that triggered the alert is resolved**.

- **d. When a Project Owner manually changes the alert status**
  - **Incorrect**: A Project Owner **cannot** manually close an alert. An alert's status will only change to `CLOSED` once the condition that triggered the alert is resolved.

---

## Lesson 6: Integrations

### Code Summary: Integrations

Review the following code, which demonstrates how to configure Atlas to send metric data to Prometheus and display it with Grafana.

#### Install Prometheus on Ubuntu Linux

**1. Update your APT package lists:**

```bash
sudo apt update
```

**2. Install the `prometheus` APT package:**

```bash
sudo apt install --yes prometheus
```

**3. Confirm the `prometheus` service is running:**

```bash
sudo systemctl status --full prometheus
```

**4. Check that the Prometheus server is ready to serve traffic:**

```bash
curl http://localhost:9090/-/ready
```

#### Edit the Prometheus Configuration File

**1. Append the copied Atlas scrape configuration snippet to `prometheus.yml`:**

```bash
sudo nano /etc/prometheus/prometheus.yml
```

**2. Restart the `prometheus` service to apply changes:**

```bash
sudo systemctl restart prometheus
```

**3. Confirm that the Atlas deployment targets are present and healthy:**

```bash
curl http://localhost:9090/api/v1/targets | jq --raw-output '.data.activeTargets[] | .scrapeUrl + " " + .health'
```

#### Install Grafana on Ubuntu Linux

**1. Install required packages:**

```bash
sudo apt-get install --yes apt-transport-https software-properties-common wget
```

**2. Download the Grafana repository signing key:**

```bash
sudo wget -q -O /usr/share/keyrings/grafana.key https://apt.grafana.com/gpg.key
```

**3. Add Grafana's package repository to your APT sources:**

```bash
echo "deb [signed-by=/usr/share/keyrings/grafana.key] https://apt.grafana.com stable main" | sudo tee -a /etc/apt/sources.list.d/grafana.list
```

**4. Update APT package lists and install Grafana:**

```bash
sudo apt update
sudo apt install --yes grafana
```

#### Start Grafana as a Service

```bash
sudo systemctl start grafana-server
sudo systemctl enable grafana-server
sudo systemctl status --full grafana-server
curl http://localhost:3000/api/health
```

#### Add Prometheus to the Grafana Server

```bash
curl \
  --header 'Content-Type: application/json' \
  --user 'admin:admin' \
  --request 'POST' \
  --data '{"name": "Prometheus", "type": "prometheus", "url": "http://localhost:9090", "access": "proxy"}' \
  http://localhost:3000/api/datasources
```

Now you're ready to start creating Grafana visualizations using the Prometheus data source!

---

### Question 1
**MongoDB Atlas supports hybrid monitoring solutions via 3rd-party integrations.** *(True/False)*

- **a. True**
  - **Correct**: With Atlas integrations you can create hybrid monitoring solutions to meet the unique needs of your deployment.

- **b. False**
  - **Incorrect**: MongoDB Atlas **does** support hybrid monitoring solutions via 3rd-party integrations.

---

### Question 2
**MongoDB Atlas provides 3rd-party integration services with the following data.** *(Select all that apply)*

- **a. Index suggestions**
  - **Incorrect**: You can monitor slow queries with third-party services, but those services won't provide index suggestions. However, MongoDB's **Performance Advisor** suggests new indexes to improve query performance based on slow queries.

- **b. Alerts**
  - **Correct**: You can integrate Atlas with third-party monitoring services to receive Atlas alerts in external monitoring services, and to view and analyze performance metrics that Atlas collects about your cluster.

- **c. Query results**
  - **Incorrect**: You **cannot** query a MongoDB database with a 3rd-party monitoring service.

- **d. Performance metrics that Atlas collects about your cluster**
  - **Correct**: You can integrate Atlas with third-party monitoring services to receive Atlas alerts, and to view and analyze performance metrics that Atlas collects about your cluster.

---

## Lesson 7: Self-Managed Monitoring

### Code Summary: Self-Managed Monitoring

Review the following code, which demonstrates how to configure the Percona MongoDB Exporter as a Prometheus target.

#### Install Percona MongoDB Exporter on Ubuntu Linux

**1. Download version 0.39.0 of the Percona MongoDB Exporter:**

```bash
wget https://github.com/percona/mongodb_exporter/releases/download/v0.39.0/mongodb_exporter-0.39.0.linux-amd64.tar.gz
```

**2. Extract the downloaded tarball:**

```bash
tar xvzf mongodb_exporter-0.39.0.linux-amd64.tar.gz
```

**3. Move the binary to `/usr/local/bin/`:**

```bash
sudo mv mongodb_exporter-0.39.0.linux-amd64/mongodb_exporter /usr/local/bin/
```

#### Create a New User

Create a user with the `clusterMonitor` role so that Percona MongoDB Exporter can read metrics:

```javascript
mongosh
use admin
db.createUser({
  user: "test",
  pwd: "testing",
  roles: [
    { role: "clusterMonitor", db: "admin" },
    { role: "read", db: "local" }
  ]
})
exit
```

#### Create a Service for Percona MongoDB Exporter

**1. Create a new service file:**

```bash
sudo nano /lib/systemd/system/mongodb_exporter.service
```

**2. Add the following contents:**

```ini
[Unit]
Description=MongoDB Exporter
User=prometheus

[Service]
Type=simple
Restart=always
ExecStart=/usr/local/bin/mongodb_exporter \
  --collect-all \
  --mongodb.uri=mongodb://test:testing@localhost:27017

[Install]
WantedBy=multi-user.target
```

**3. Reload the daemon and start the service:**

```bash
sudo systemctl daemon-reload
sudo systemctl start mongodb_exporter
sudo systemctl enable mongodb_exporter
sudo systemctl status --full mongodb_exporter
```

**4. Confirm MongoDB metrics are available via the `/metrics` endpoint:**

```bash
curl http://localhost:9216/metrics
```

#### Configure Percona MongoDB Exporter as a Prometheus Target

**1. Open the Prometheus configuration file:**

```bash
sudo nano /etc/prometheus/prometheus.yml
```

**2. Append the following scrape configuration snippet:**

```yaml
...
scrape_configs:
  ...
  - job_name: 'mongodb_exporter'
    static_configs:
      - targets: ['localhost:9216']
...
```

**3. Restart Prometheus and verify the target is healthy:**

```bash
sudo systemctl restart prometheus
curl http://localhost:9090/api/v1/targets | jq --raw-output '.data.activeTargets[] | .scrapeUrl + " " + .health'
```

Now you're ready to start creating Grafana visualizations using the Prometheus data source!

---

### Question 1
**Prometheus gathers metrics from MongoDB Atlas via 3rd-party integration but can also collect metrics from a self-managed deployment using which of the following?** *(Select one)*

- **a. Data Dog**
  - **Incorrect**: DataDog is a 3rd-party service that can be used **separately** from Prometheus to monitor a MongoDB deployment.

- **b. Custom Webhooks**
  - **Incorrect**: Prometheus doesn't use custom webhooks to monitor a self-managed MongoDB deployment.

- **c. Grafana**
  - **Incorrect**: Grafana is an open-source dashboard application that can **display** metrics from Prometheus, not collect them.

- **d. Percona MongoDB Exporter**
  - **Correct**: We can configure the **Percona MongoDB Exporter** as a Prometheus target to collect metrics from a self-managed MongoDB deployment.

---

## Lesson 8: Command Line Metrics

### Code Summary: Command Line Metrics

Review the following code, which demonstrates how to retrieve metrics from the MongoDB Shell.

#### `serverStatus`

To return a document that provides an overview of the database's state:

```javascript
db.runCommand(
  {
    serverStatus: 1
  }
)
```

To return a specific object, like `connections`:

```javascript
db.runCommand( { serverStatus: 1 } ).connections
```

#### `currentOp`

To return a document with all currently active operations:

```javascript
db.adminCommand(
  {
    currentOp: true,
    "$all": true
  }
)
```

#### `killOp`

To kill an active operation:

```javascript
db.adminCommand(
  {
    killOp: 1,
    op: <opid>,
    comment: <any>
  }
)
```

---

### Question 1
**MongoDB Atlas gathers active operation metrics from the database instance using which of the following commands?** *(Select one)*

- **a. `currentOp`**
  - **Correct**: The `currentOp` command will successfully return a document with all in-progress operations.

- **b. `activeOp`**
  - **Incorrect**: `activeOp` is **not a valid command**. `currentOp` will successfully return a document with all in-progress operations.

- **c. `serverStatus`**
  - **Incorrect**: `serverStatus` returns a document that provides an overview of a database's state, but that does **not** include active operations. Use `currentOp` for in-progress operations.

- **d. `collStats`**
  - **Incorrect**: `collStats` returns storage statistics for a given collection. Use `currentOp` for in-progress operations.

---

### Question 2
**You wish to view the total number of active connections to your deployment while in the MongoDB Shell. Which of the following commands should you use?** *(Select one)*

- **a. `killOp`**
  - **Incorrect**: `killOp` is an administrative command that allows you to kill active operations — it does **not** provide connection metrics.

- **b. `currentOp`**
  - **Incorrect**: `currentOp` is an administrative command that returns information on in-progress operations for the `mongod` instance — not connection metrics.

- **c. `serverStatus`**
  - **Correct**: `serverStatus` is a diagnostic database command that returns a document providing an overview of the database's state, **including connection metrics**.

---

## Conclusion

### MongoDB Database Metrics & Monitoring

In this unit, you learned how to:

- Use core metrics to monitor a MongoDB deployment
- Use the Real-Time Performance Panel and the Metrics Tab in the Atlas UI to monitor M10+ clusters
- Use Atlas CLI commands to retrieve monitoring metrics for an Atlas cluster
- Create, update, and delete an Atlas alert with the Atlas UI and the Atlas CLI
- Respond to an Atlas alert with the Atlas UI and the Atlas CLI
- Integrate with 3rd-party services, like Prometheus, to monitor an Atlas cluster
- Monitor a self-managed deployment with 3rd-party tools like the Percona MongoDB Exporter, Prometheus, and Grafana
- Use MongoDB commands `serverStatus`, `currentOp`, and `killOp` to view metrics for a deployment

---

## Resources

Use the following resources to learn more about monitoring a MongoDB deployment:

- **Lesson 1: Core Metrics**
  - [How to Monitor MongoDB](https://www.mongodb.com/docs/atlas/monitoring-alerts/)
  - [Review Available Metrics](https://www.mongodb.com/docs/atlas/review-available-metrics/)

- **Lesson 2: More Metrics**
  - [How to Monitor MongoDB](https://www.mongodb.com/docs/atlas/monitoring-alerts/)
  - [Review Available Metrics](https://www.mongodb.com/docs/atlas/review-available-metrics/)

- **Lesson 3: Monitoring M10+**
  - [Monitor Your Database Deployments](https://www.mongodb.com/docs/atlas/monitoring-alerts/)
  - [Monitor Real-Time Performance](https://www.mongodb.com/docs/atlas/real-time-performance-panel/)
  - [Replication Metrics Tab](https://www.mongodb.com/docs/atlas/cluster-metrics/)
  - [Atlas CLI: atlas metrics processes](https://www.mongodb.com/docs/atlas/cli/stable/command/atlas-metrics-processes/)

- **Lesson 4: Configure Alerts**
  - [Configure Atlas Alert Settings](https://www.mongodb.com/docs/atlas/configure-alerts/)
  - [Review Alert Conditions](https://www.mongodb.com/docs/atlas/reference/alert-conditions/)
  - [Atlas CLI: atlas alerts settings list](https://www.mongodb.com/docs/atlas/cli/stable/command/atlas-alerts-settings-list/)
  - [Atlas CLI: atlas alerts settings create](https://www.mongodb.com/docs/atlas/cli/stable/command/atlas-alerts-settings-create/)
  - [Atlas CLI: atlas alerts settings update](https://www.mongodb.com/docs/atlas/cli/stable/command/atlas-alerts-settings-update/)
  - [Atlas CLI: atlas alerts settings delete](https://www.mongodb.com/docs/atlas/cli/stable/command/atlas-alerts-settings-delete/)

- **Lesson 5: Respond to Alerts**
  - [Resolve Alerts](https://www.mongodb.com/docs/atlas/alert-resolutions/)
  - [Atlas CLI: atlas alerts list](https://www.mongodb.com/docs/atlas/cli/stable/command/atlas-alerts-list/)
  - [Atlas CLI: atlas alerts acknowledge](https://www.mongodb.com/docs/atlas/cli/stable/command/atlas-alerts-acknowledge/)
  - [Atlas CLI: atlas alerts unacknowledge](https://www.mongodb.com/docs/atlas/cli/stable/command/atlas-alerts-unacknowledge/)

- **Lesson 6: Integrations**
  - [Atlas Integrations](https://www.mongodb.com/docs/atlas/integrations/)
  - [Integrate with Prometheus](https://www.mongodb.com/docs/atlas/tutorial/prometheus-integration/)

- **Lesson 7: Self-Managed Monitoring**
  - [Cloud Manager](https://www.mongodb.com/cloud/cloud-manager)

- **Lesson 8: Command Line Metrics**
  - [serverStatus](https://www.mongodb.com/docs/manual/reference/command/serverStatus/)
  - [currentOp](https://www.mongodb.com/docs/manual/reference/command/currentOp/)
  - [killOp](https://www.mongodb.com/docs/manual/reference/command/killOp/)
