# Setting-up-Prometheus-and-Grafana-in-EKS-Cluster
EKS Cluster Setup Guide
=======================

Amazon Elastic Kubernetes Service (EKS) is a fully managed container orchestration service provided by AWS. It simplifies the deployment and management of containerized applications by taking care of the Kubernetes control plane while you manage the worker nodes. This guide will walk you through setting up an EKS cluster, deploying a sample application, and setting up monitoring with Prometheus and Grafana.

Features
--------

-   **Managed Kubernetes Service**: Focus on deploying and managing containerized applications without managing the Kubernetes control plane.
-   **Scalability**: Easily scale applications and resources based on demand.
-   **Security**: Leverage AWS's robust security features for secure deployments.

Pre-requisites
--------------

1.  **AWS CLI**: Command line tools for working with AWS services, including Amazon EKS.
2.  **eksctl**: A command line tool for working with EKS clusters that automates many tasks.
3.  **kubectl**: A command line tool for working with Kubernetes clusters.
4.  **AWS Access Keys**: Ensure AWS credentials are configured in your environment.

### Setting Up AWS CLI

1.  Install the AWS CLI.
2.  Configure the AWS CLI with your credentials:
    
    `aws configure`
    
    You will be prompted for:
    -   Access key
    -   Secret access key
    -   AWS Region
    -   Output format

Creating an EKS Cluster
-----------------------

1.  Install `eksctl` following the instructions from the eksctl documentation.
2.  Create an EKS cluster using the following command:

    `eksctl create cluster --name demo-eks --region us-east-2 --nodegroup-name my-nodes --node-type t3.small --managed --nodes 2`

    This command creates an EKS cluster, which might take 15 to 20 minutes. `eksctl` uses CloudFormation under the hood, creating one stack for the EKS master control plane and another stack for the worker nodes.

### Verifying the EKS Cluster

1.  Verify the cluster creation:

    `eksctl get cluster --name demo-eks --region us-east-2`

### Connecting to the EKS Cluster

1.  Use `kubectl` to interact with the cluster:

    `kubectl get nodes
    kubectl get ns`

Deploying Nginx on the Kubernetes Cluster
-----------------------------------------

1.  Create an Nginx deployment:

    `kubectl create deployment nginx --image=nginx`

2.  View the deployments:

    `kubectl get deployments`

Deleting the EKS Cluster
------------------------

1.  Delete the EKS cluster:

    `eksctl delete cluster --name demo-eks --region us-east-2`

Setting Up Monitoring on EKS Cluster with Prometheus and Grafana
----------------------------------------------------------------

### What is Prometheus?

Prometheus is an open-source monitoring tool that provides out-of-the-box monitoring capabilities for Kubernetes. It collects and stores metrics as time-series data.

### What is Grafana?

Grafana is an open-source visualization and analytics software. It allows you to query, visualize, alert on, and explore your metrics.

### Why System Monitoring is Critical?

-   **Alerting**: Early warning of issues before they become serious or costly.
-   **Visibility**: Real-time insights into the health and performance of applications and infrastructure.
-   **Capacity Planning**: Make informed decisions about capacity planning by analyzing data over time.

### Prometheus Architecture

-   **Prometheus server**: Processes and stores metrics data.
-   **Alert Manager**: Sends alerts to any systems/channels.
-   **Grafana**: Visualizes scraped data in a UI.

### Installation Method

Using Helm is recommended for installing Prometheus and Grafana because it simplifies the installation of all components in one command and ensures no configuration steps are missed.

### Pre-requisites

-   An EKS cluster up and running.
-   Helm 3 installed.
-   An EC2 instance to access the EKS cluster.

### Implementation Steps

1.  **Add Helm Repositories**:

    sh

    Copy code

    `helm repo add stable https://charts.helm.sh/stable
    helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
    helm search repo prometheus-community`

2.  **Create Prometheus Namespace**:

    sh

    Copy code

    `kubectl create namespace prometheus`

3.  **Install kube-prometheus-stack**:

    sh

    Copy code

    `helm install stable prometheus-community/kube-prometheus-stack -n prometheus`

4.  **Verify Installation**:

    sh

    Copy code

    `kubectl get pods -n prometheus
    kubectl get svc -n prometheus`

5.  **Edit Services to Use LoadBalancer**:

    sh

    Copy code

    `kubectl edit svc stable-kube-prometheus-sta-prometheus -n prometheus
    kubectl edit svc stable-grafana -n prometheus`

6.  **Verify Service Changes**:

    sh

    Copy code

    `kubectl get svc -n prometheus`

7.  **Access Grafana UI in the Browser**:

    -   Use the URL obtained from the previous step.
    -   Username: `admin`
    -   Password: `prom-operator`

### Create a Dashboard in Grafana

1.  Click the '+' button on the left panel and select 'Import'.
2.  Enter `12740` dashboard ID under Grafana.com Dashboard.
3.  Click 'Load'.
4.  Select 'Prometheus' as the endpoint under the Prometheus data sources drop-down.
5.  Click 'Import'.

This will display the monitoring dashboard for all cluster nodes
