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

## # Guide de configuration d'Alert Manager

Ce guide explique comment accéder au serveur Alert Manager et configurer les notifications par email.

## 1. Accéder au serveur Alert Manager

Vous pouvez accéder au serveur Alert Manager de plusieurs façons.

### a. Utiliser la commande fournie après l'installation Helm

Exécutez les commandes suivantes pour obtenir le nom du pod et démarrer le port forwarding :

```bash
export POD_NAME=$(kubectl get pods --namespace default -l "app.kubernetes.io/name=alertmanager,app.kubernetes.io/instance=prometheus" -o jsonpath="{.items[0].metadata.name}")
kubectl --namespace default port-forward $POD_NAME 9093

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
