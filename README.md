# Installing (using Helm) and Configuring Prometheus, Alertmanager and Grafana in Kubernetes Cluster

# 1. Install Helm in Kubernetes cluster

Run these commands to install helm
Reference: `https://helm.sh/docs/intro/install/`
```bash
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh
```

# 2. Install Prometheus in Kubernetes Cluster using Helm

## a. Add Prometheus Helm Charts
```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
```
To search the repository, run:
```bash
helm search repo prometheus-community
```
## b. Install Prometheus
```bash
helm install [RELEASE_NAME] prometheus-community/prometheus -f prometheus.yaml
```
The command above would do the following:
  * Install Prometheus-Server, Alert Manager, Kube-State-Metrics, Node-Exporter and PushGateway
  * Add configurations to the Prometheus Server to set custom alerting rules. You may add as many rules as required in to the file.
  * The file `prometheus.yaml` is found within this repository.

## c. Access the Prometheus Server UI
This can be done in multiple ways:
  * Using the command provided when you run the `helm install...`
    ```bash
    export POD_NAME=$(kubectl get pods --namespace default -l "app.kubernetes.io/name=prometheus,app.kubernetes.io/instance=prometheus" -o jsonpath="{.items[0].metadata.name}")
    kubectl --namespace default port-forward $POD_NAME 9090
    ```
  * Using the `kubectl expose` command
    ```bash
    kubectl expose service prometheus-server --type=NodePort --target-port=9090 --name=prometheus-server-access
    ```
  * Using Ingress - to be treated later!
When you access the Prometheus Server, you would appreciate the set alerting rules among other features.

## d. Access the Alert Manager Server
This can be done in multiple ways:
  * Using the command provided when you run the `helm install...`
    ```bash
    export POD_NAME=$(kubectl get pods --namespace default -l "app.kubernetes.io/name=alertmanager,app.kubernetes.io/instance=prometheus" -o jsonpath="{.items[0].metadata.name}")
    kubectl --namespace default port-forward $POD_NAME 9093
    ```
  * Using the `kubectl expose` command
    ```bash
    kubectl expose service prometheus-alertmanager --type=NodePort --target-port=9093 --name=alertmanager-access
    ```
  * Using Ingress - to be treated later!
When you access the Alert Manager, you would appreciate the set alerts if any, among other features.

## e. Configure Alert Manager for Email Notifications
  * Get the configmap being used by Alert Manager
    ```bash
    kubectl get configmaps
    ```
  * Edit the configmap
    ```bash
    kubectl edit configmap prometheus-alertmanager
    ```
  * Add the content of the `prometheus-alert.yaml` found in this repository
  * Save and quit.
  * Refresh the alert manager access by doing these:
    - Delete the alert manager pod
      ```bash
      kubectl delete pod prometheus-alertmanager-0
      ```
    - Because Alert Manager is deployed as a Stateful Set, the pod would be recreated automatically. Check!
    - Refresh the URL and note the changes in `Status` tab
    - Once there is an alert, you would receive an email notification.

# 3. Install Grafana

## a. Add Grafana Helm Charts
```bash
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update
```
To search the repository, run:
```bash
helm search repo grafana
```
## b. Install Grafana
```bash
helm install [RELEASE_NAME] grafana/grafana
```

## c. Access the Grafana UI
This can be done in multiple ways:
  * Using the command provided when you run the `helm install...`
    ```bash
    export POD_NAME=$(kubectl get pods --namespace default -l "app.kubernetes.io/name=grafana,app.kubernetes.io/instance=grafana" -o jsonpath="{.items[0].metadata.name}")
    kubectl --namespace default port-forward $POD_NAME 3000
    ```
  * Using the `kubectl expose` command
    ```bash
    kubectl expose service grafana --type=NodePort --target-port=3000 --name=grafana-access
    ```
  * Using Ingress - to be treated later!
  * Retrieve the `admin` password to login
    ```bash
    kubectl get secret --namespace default grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
    ```
  * With username `admin` and the password from the above command, login!
  * Add Data Source
  * Import Dashboards - 6417, 3119, 8919, 7249, 11074 etc
When you access the Grafana UI, you would appreciate the different components.

## Hope you got it!

## DevOps Training at Merciboi Systems Solutions
## info@merciboi.com; register@merciboi.com
## www.merciboi.com/training

# Merciboi Systems Solutions
# Canada
# info@merciboi.com
# www.merciboi.com; www.merciboi.ca
