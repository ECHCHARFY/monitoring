## Installing (using Helm) and Configuring Prometheus and Grafana in Kubernetes Cluster


# 1. Install Prometheus in Kubernetes Cluster using Helm

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
helm install prometheus prometheus-community/prometheus -f prometheus.yaml -n monitoring
```

## c. Access the Prometheus Server UI

  * Using the `kubectl expose` command
    ```bash
    kubectl expose service prometheus-server --type=LoadBalancer --target-port=9090 --name=prometheus-server-access -n monitoring
    ```

## Access the Alert Manager Server
This can be done in multiple ways:

Using the command provided when you run the helm install...
export POD_NAME=$(kubectl get pods --namespace default -l "app.kubernetes.io/name=alertmanager,app.kubernetes.io/instance=prometheus" -o jsonpath="{.items[0].metadata.name}")
kubectl --namespace default port-forward $POD_NAME 9093
Using the kubectl expose command
kubectl expose service prometheus-alertmanager --type=NodePort --target-port=9093 --name=alertmanager-access
Using Ingress - to be treated later! When you access the Alert Manager, you would appreciate the set alerts if any, among other features.
e. Configure Alert Manager for Email Notifications
Get the configmap being used by Alert Manager
kubectl get configmaps
Edit the configmap
kubectl edit configmap prometheus-alertmanager
Add the content of the prometheus-alert.yaml found in this repository
Save and quit.
Refresh the alert manager access by doing these:
Delete the alert manager pod
kubectl delete pod prometheus-alertmanager-0
Because Alert Manager is deployed as a Stateful Set, the pod would be recreated automatically. Check!
Refresh the URL and note the changes in Status tab
Once there is an alert, you would receive an email notification.


# 2. Install Grafana

## a. Add Grafana Helm Charts
```bash
helm repo add grafana https://grafana.github.io/helm-charts -n monitoring
helm repo update
```

## b. Install Grafana
```bash
helm install grafana grafana/grafana -n monitoring
```

## c. Access the Grafana UI

  * Using the `kubectl expose` command
    ```bash
    kubectl expose service grafana --type=LoadBalncer --target-port=3000 --name=grafana-access -n monitoring
    ```

  * Retrieve the `admin` password to login
    ```bash
    kubectl get secret --namespace monitoring grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
    ```
  * With username `admin` and the password from the above command, login!
  * Add Data Source
  * Import Dashboards - 6417, 3119, 8919, 7249, 11074 etc
