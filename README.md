#bitnami images
Setting Up a Prometheus Web Monitoring Environment

System Requirements/Prerequisites:

This tutorial will be hands-on. If you'd like to follow along, be sure you have the following:

Basic knowledge of the Linux command-line interface (CLI)

Ubuntu server version with 22.04 or above

A user account with sudo privileges

Alertmanager

Node-Exporter

Blackbox exporter

Uptime-kuma

**Setting up Prometheus:**

Before installing anything, run sudo apt update first before installing anything inside a Linux instance. After installing updates, run sudo apt install prometheus to install Prometheus. After installing, go to /etc/prometheus directory and edit prometheus.yml by using any Unix editors like vi or nano, change the Ip consists localhost to the server Ip or ec2 public Ip, and the port number remain unchanged. In the configuration file, you can adjust system-level settings related to Prometheus server, such as the network interface and port it listens on, data retention time, and log levels. These settings are essentially the command-line arguments passed to the Prometheus server when it starts. Here’s a simple example of configuration:

\# Specify that Prometheus server listens on 0.0.0.0:9090 and retains data for 15 days

ARGS="--web.listen-address='0.0.0.0:9090' --storage.tsdb.retention=15d"

Managing Prometheus Server:

You can use the systemctl command to control Prometheus:

Check Prometheus server status:

systemctl status prometheus

Start Prometheus server:

sudo systemctl start prometheus

Restart Prometheus server:

sudo systemctl restart prometheus

Stop Prometheus server:

sudo systemctl stop prometheus

Reload Prometheus server:

sudo systemctl reload prometheus

To access Prometheus server, use port 9090 to access it, the doc will talk about it at the lower part.

Once access successful,it will be like:

**Setting up Alertmanager**

Alertmanager is a important component that manages alerts received from Prometheus servers and other client applications and also acts as a server. Its main responsibilities include duplicating, grouping, and sending alerts to the right recipients, such as email, PagerDuty, or OpsGenie. In addition, Alertmanager handles operations such as suspending and shutting down alerts.

To install Alertmanager, type this command into your terminal:

sudo apt install prometheus-alertmanager

The configuration file of node-exporter is in its configuration file is located at /etc/default/prometheus-node-exporter. The configuration process is similar. In this configuration file, you can adjust the system information that Node Exporter collects.

**You can also use the systemctl command to control Alertmanager:**

\# Check Alertmanager server status

systemctl status prometheus-alertmanager

\# Start Alertmanager

sudo systemctl start prometheus-alertmanager

\# Restart Alertmanager

sudo systemctl restart prometheus-alertmanager

\# Stop Alertmanager

sudo systemctl stop prometheus-alertmanager

\# Reload Alertmanager

sudo systemctl reload prometheus-alertmanager

**Installing Node exporter**

To install Node exporter, type this command into your terminal:

sudo apt install prometheus-alertmanager

Node Exporter itself acts as a server, and its configuration file is located at /etc/default/prometheus-node-exporter. The configuration process is similar. In this configuration file, you can adjust the system information that Node Exporter collects.

**You can also use the systemctl command to control Node Exporter:**

\# Check Node Exporter Status

systemctl status prometheus-node-exporter

\# Start Node Exporter

sudo systemctl start prometheus-node-exporter

\# Restart Node Exporter

sudo systemctl restart prometheus-node-exporter

\# Stop Node Exporter

sudo systemctl stop prometheus-node-exporter

\# Reload Node Exporter

sudo systemctl reload prometheus-node-exporter

**Setting up Prometheus**

The default configuration file for the Prometheus server is located at /etc/prometheus/prometheus.yml. In this file, you can adjust various settings related to the Prometheus server. The configuration file uses YAML syntax, and the parameter settings and explanations can be found in the official Prometheus documentation or in the official configuration file examples.

Here’s a simple example of a configuration file(this is the initial config when installed):

\# Default parameters

global:

scrape_interval: 15s # Interval for scraping monitoring information

evaluation_interval: 15s # Interval for evaluating alert rules

\# Additional label information when forwarding data or alerts to external systems

external_labels:

monitor: 'example'

\# Alertmanager configuration

alerting:

alertmanagers:

\- static_configs:

\- targets: \['localhost:9093'\] # Alertmanager server address

\# Load alert rules; periodically evaluate alerts based on the evaluation_interval

rule_files:

\- "first_rules.yml"

\- "second_rules.yml" #use the absolute path of the rule

\# Scrape monitoring information

scrape_configs:

\# Scrape Prometheus server's own information

\- job_name: 'prometheus'

\# Override default settings

scrape_interval: 5s # Interval for scraping monitoring information

scrape_timeout: 5s # Interval for evaluating alert rules

metrics_path: '/metrics' # Metrics path (default: '/metrics')

scheme: 'http' # Transport protocol (default: 'http')

static_configs:

\- targets: \['localhost:9090'\] # Address of the monitored machine

\# Scrape Node Exporter information

\- job_name: 'node'

static_configs:

\- targets: \['localhost:9100'\] # Address of the monitored machine

In the global section, you can set default values for various parameters. These defaults apply to other configuration contexts as well. The alerting section connects to the Alertmanager server by specifying its address. The rule_files section allows you to load custom alert rules for periodic evaluation. The scrape_configs section defines the monitored machine information. In this example, we have two sources: Prometheus server itself and a self-installed Node Exporter node.

When configuring job and instance settings, you can test each instance’s metrics_path to ensure it returns all monitoring information. After making changes to the Prometheus server configuration, reload it for the settings to take effect:

\# Reload Prometheus

sudo systemctl reload Prometheus

After reloading, use port 9090 to access Prometheus server via a web browser. You will see:

The default homepage is graph, at graph, you can use PromQL queries to retrieve monitoring information from the database. For example, by entering node_load1, you can query the one-minute load average of the monitored instance. You can add more than one graph to monitor.

**Installing Grafana**

Grafana is a dashboard tool used to display various monitoring data collected by Prometheus. On Ubuntu Linux, you can install Grafana using the following commands:

\# Import the developer key

wget -q -O - <https://packages.grafana.com/gpg.key> | sudo apt-key add -

\# Add the Grafana package repository

echo "deb <https://packages.grafana.com/oss/deb> stable main" | sudo tee -a /etc/apt/sources.list.d/grafana.list

\# Install Grafana

sudo apt-get update

sudo apt-get install grafana

\# Enable Grafana service

sudo systemctl daemon-reload

sudo systemctl enable grafana-server

**You can control the Grafana server using the systemctl command:**

\# Check Grafana server status

systemctl status grafana-server

\# Start Grafana server

sudo systemctl start grafana-server

\# Restart Grafana server

sudo systemctl restart grafana-server

\# Stop Grafana server

sudo systemctl stop grafana-server

\# Reload Grafana server

sudo systemctl reload grafana-server

Once Grafana is installed, open <http://localhost:3000/> in your browser. The default username is admin, and the default password is also admin. You’ll be prompted to reset the password upon login. Once logged in, you’ll see the Grafana web interface.

Follow the steps to configure Prometheus as a data source and create dashboards.

1. Click on “Add your first data source”.
2. In the “Add data source” page, choose “Prometheus”.
3. Set the URL field to the address of your Prometheus server. If it’s local, use <http://localhost:9090/>
4. Click on the “Import” feature to import a dashboard.
5. In the “Import via grafana.com” field, enter 1860 to import the Node Exporter Full dashboard.
6. Select the Prometheus data source and click “Import”.

Navigate to the Grafana dashboards, and you’ll see various monitoring data like instance status.

**Prometheus Alerts**

The default configuration file for the Prometheus server is located at \`/etc/prometheus/prometheus.yml\`. In this file, you can adjust various settings related to the Prometheus server. The configuration file uses YAML syntax, and the parameter settings and explanations can be found in the official Prometheus documentation or in the official configuration file examples. Here's a simple example of a configuration file:

groups:

\- name: example_node

rules:

\- alert: HostHighCpuLoad

expr: 100 - (avg by(instance) (rate(node_cpu_seconds_total{mode="idle"}\[2m\])) \* 100) > 80

for: 0m

labels:

severity: warning

annotations:

summary: Host high CPU load (instance {{ $labels.instance }})

description: "CPU load is > 80%\\n VALUE = {{ $value }}\\n LABELS = {{ $labels }}"

With [Awesome Prometheus Alerts](https://samber.github.io/awesome-prometheus-alerts/) collection, you’ll find numerous commonly used alert rule examples. I recommend selecting the ones you need, copying them, and then adjusting as necessary.

There is another rule for website monitoring, here is the yml for the rule:

Once you’ve written your alert rules, you can use the promtool tool to check if the syntax is correct:

\# Check alert rule syntax

promtool check rules cpu.yml

Checking cpu.yml

SUCCESS: 1 rules found

After configuring your alert rules, restart Prometheus:

\# Restart Prometheus server

sudo systemctl restart Prometheus

You can now navigate to the Rules page in Prometheus to view the currently configured alert rules.If not, you may try to test it manually to make sure the rule state is ok.

**Alertmanager Alarm Message Delivery Settings**

You can configure the Alertmanager by editing the /etc/prometheus/alertmanager.yml configuration file. You can refer to the official Prometheus documentation for guidance on setting up alert notifications. The most common method is to send alerts via email:

\# Default parameters

global:

\# SMTP server configuration

smtp_smarthost: 'localhost:25' #use your email server address

smtp_from: 'alertmanager@localhost'

\# smtp_auth_username: 'alertmanager'

\# smtp_auth_password: 'password'

smtp_require_tls: false

\# Alert notification routing configuration

route:

\# Group alerts by merging identical alertname, cluster, and service

group_by: \['alertname', 'cluster', 'service'\]

\# Wait time after alert group creation

group_wait: 30s #you can change the interval

\# Interval for sending new alert messages for the same group

group_interval: 5m

\# Interval for resending the same alert messages

repeat_interval: 3h

\# Default recipient

receiver: admin-mails # you can change the name of the receiver

\# Receiver configuration

receivers:

\- name: 'admin-mails'

email_configs:

\- to: 'root@localhost' #reciver/admin’s email

The smtp_require_tls setting here can be configured to determine whether to use TLS-encrypted SMTP connections. During the initial development and testing stages, it’s convenient to set it to false. You can later add TLS encryption functionality in the production environment.

After updating the Alertmanager configuration, restart the Alertmanager server with the following command:

\# Restart the Alertmanager server

sudo systemctl restart prometheus-alertmanager

**Test the rules**

To test alert rules, you can intentionally put your system’s CPU under heavy load. You can achieve this by running the following command:

\# Put CPU under heavy load

cat /dev/zero > /dev/null

If you want to test additional system load scenarios, consider using Stress-ng, a tool for testing CPU, memory, and disk I/O load. It provides various stress tests to simulate different workloads.

When the CPU load exceeds 80%, Prometheus will trigger an alert. You can monitor the current alerts on the Prometheus Alerts page.

You may test other or you own rule by various commands, programs of editing some configuration.