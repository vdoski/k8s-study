Создан неймспейс prometheus
Добавляем репозиторий в хельм: helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
При установке хельма возникает следующая ошибка:
    Error: failed to install CRD crds/crd-alertmanager.yaml: resource mapping not found for name: "alertmanagers.monitoring.coreos.com" namespace: "" from "": no matches for kind "CustomResourceDefinition" in version "apiextensions.k8s.io/v1beta1"
ensure CRDs are installed first
Выяснил, что prometheus-operator DEPRECATED, осуждается?? Плюс сложности с установкой crd, с этим не вышло ничего. Просто установил
    helm install monitoring prometheus-community/kube-prometheus-stack 
Переменные назначили такими:
    ---
defaultRules:
  create: true
  rules:
    etcd: false 
    kubeSchedulerAlerting: true
    kubeSchedulerRecording: true
kubeControllerManager:
  enabled: false
kubeEtcd:
  enabled: false
prometheus:
  prometheusSpec:
    serviceMonitorSelector:
      matchLabels:
        prometheus: devops
commonLabels:
  prometheus: devops
grafana:
  adminPassword: q1w2e3r4t5y6

Появились подики:
     k8s-study git:(kubernetes-monitoring) ✗ kubectl get pods -n monitoring 
NAME                                                     READY   STATUS    RESTARTS   AGE
alertmanager-monitoring-kube-prometheus-alertmanager-0   2/2     Running   0          2m56s
monitoring-grafana-64f6bddbf6-xzrd2                      3/3     Running   0          3m16s
monitoring-kube-prometheus-operator-7598bbff5b-p8jdj     1/1     Running   0          3m16s
monitoring-kube-state-metrics-6bf9797777-vk8z8           1/1     Running   0          3m16s
monitoring-prometheus-node-exporter-655vx                1/1     Running   0          3m16s
prometheus-monitoring-kube-prometheus-prometheus-0       2/2     Running   0          2m56s

Далее задеплоил nginx.
Сделал service-manager со следующими параметрами:
apiVersion: monitoring.coreos.com/v1
kind: ServiceMonitor
metadata:
  name: web-monitor
  labels:
    prometheus: devops
spec:
  selector:
    matchLabels:
      app: nginx
  namespaceSelector:
    matchNames:
      - default
  endpoints:
    - port: metrics
      path: /metrics

Проследил что всё в прометеусе подключилось, далее подключил графану и настроил подходящий дашборд с визуализацией.