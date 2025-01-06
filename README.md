# CNPG Setup Guide

This guide provides instructions for setting up a **CloudNativePG (CNPG) cluster** and monitoring it using **Prometheus** and **Grafana**. Follow the steps below to get everything up and running.

---

## Prerequisites

Before you begin, ensure that the following are installed and configured:

- A **Kubernetes cluster**
- **Helm**
- **kubectl**

---

## CNPG Operator Installation

To install the CNPG operator, run the following command:

```sh
kubectl apply --server-side -f \
  https://raw.githubusercontent.com/cloudnative-pg/cloudnative-pg/release-1.25/releases/cnpg-1.25.0.yaml
```

---

## Deploy a CNPG Cluster

### 1. Create the Cluster Configuration File

Here’s an example configuration file, `cluster-example.yaml`:

```yaml
apiVersion: postgresql.cnpg.io/v1
kind: Cluster
metadata:
  name: cluster
spec:
  instances: 3
  storage:
    size: 1Gi
  monitoring:
    enablePodMonitor: true
```

### 2. Apply the Configuration

Run the following command to apply the configuration:

```sh
kubectl apply -f cluster-example.yaml
```

### (Optional) Create a Namespace

If you prefer to deploy the cluster in a specific namespace, first create the namespace:

```sh
kubectl create namespace db
```

Then apply the configuration file to the namespace:

```sh
kubectl apply -f cluster-example.yaml -n db
```

---

## Prometheus and Grafana Installation

### 1. Add Helm Repository and Install Charts

Add the **Prometheus Community** Helm repository:

```sh
helm repo add prometheus-community \
  https://prometheus-community.github.io/helm-charts
```

Install Prometheus and Grafana using the following command:

```sh
helm upgrade --install \
  -f https://raw.githubusercontent.com/cloudnative-pg/cloudnative-pg/main/docs/src/samples/monitoring/kube-stack-config.yaml \
  prometheus-community \
  prometheus-community/kube-prometheus-stack
```

To install in a specific namespace, append `-n <namespace>` to the command.

---

### 2. Access the Grafana Dashboard

To access the Grafana dashboard, expose its service using **port-forwarding**, **NodePort**, or **Ingress**. For port-forwarding, use:

Default login credentials are: admin:prom-operator

```sh
kubectl port-forward svc/prometheus-community-grafana 3000:80
```

Once forwarded, access Grafana at `http://localhost:3000`

### 3. Add a Grafana Dashboard

Copy the `grafana-dashboard.json` file to import a pre-configured dashboard. Below are example screenshots of the Grafana setup:

![Grafana Metrics](https://github.com/user-attachments/assets/a8e21b8b-adcc-41bd-8f16-44183790da9d)

![Dashboard View](https://github.com/user-attachments/assets/164878f1-d739-4510-b06e-b337d810bbdf)

(Optional) Add k8s-dashboard.json as well

---

## Configure CNPG as a Data Source in Grafana

### 1. Retrieve Database Credentials

Run the following command to get the CNPG database credentials:

```sh
kubectl get secret cluster-app -n db \
  -o jsonpath="{.data.pgpass}" | base64 -d
```

The output will resemble:

```
cluster-rw:5432:app:app:password
```

### 2. Add the Data Source in Grafana

Use the retrieved credentials to configure CNPG as a data source in Grafana. Below are example screenshots of the configuration process:

![Data Source Configuration](https://github.com/user-attachments/assets/214f3a92-b203-4835-8004-d2cff92df231)

![Final Data Source Setup](https://github.com/user-attachments/assets/ce525bee-1405-4b23-9f84-ac0b6dd1cb00)


## PostgreSQL Admin 4 Installation



To install **PostgreSQL Admin 4**, follow these steps:



### 1. Add the Helm Repository



Add the **Runix Helm** repository:



```sh

helm repo add runix https://helm.runix.net/

```



### 2. Install pgAdmin 4



Run the following command to install pgAdmin 4:



```sh

helm install my-pgadmin4 runix/pgadmin4 --version 1.34.0

```



### 3. Expose the Service



Expose the pgAdmin 4 service to access the web GUI using port-forwarding, NodePort, or Ingress.



The default login credentials are:



- **Username:** chart@domain.com  

- **Password:** SuperSecret



### 4. Register a New Server



After logging into the pgAdmin 4 interface, follow these steps:



1. Right-click on **Servers** > **Register Server**.

2. Use the same credentials you used when configuring the Grafana data source.



Below is an example of the setup:

![obraz](https://github.com/user-attachments/assets/5de6b9a3-d222-4b15-91fe-9d1093b4564b)

![obraz](https://github.com/user-attachments/assets/4a8e6731-3b07-419b-a27f-4c8aaccf1794)


## Sample grafana dashboard

Here's a basic example demonstrating the possibilities of using CloudNativePG with Grafana. A simple table and query highlight the integration's potential

![obraz](https://github.com/user-attachments/assets/9315b5c1-c555-432a-9acb-1dfc252a2bcf)

![obraz](https://github.com/user-attachments/assets/a2c5cdf5-5dee-49f6-b5ea-bded486358a8)





---

## Conclusion

With this setup, you now have a fully functional environment for managing and monitoring your PostgreSQL clusters.  

- **CNPG** provides a robust PostgreSQL cluster management system.  
- **Prometheus** and **Grafana** offer comprehensive monitoring and metrics visualization.  
- **pgAdmin 4** allows for easy database administration through its intuitive web interface.  

This complete stack ensures that you can efficiently manage your databases, monitor performance, and visualize critical metrics.


  
