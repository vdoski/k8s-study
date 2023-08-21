# k8s-study

Выполнено ДЗ №1

    Основное ДЗ
    Задание со *

В процессе сделано:

    Настроено локальное окружение

    Разобран механизм перезапуска Podов:

    Minishift для запуска Pod`ов: -- kube-scheduler-minikube -- kube-apiserver-minikube -- kube-controller-manager-minikube -- etcd-minikube Использует механизм static pods, так как внутри VM использует kubeadm, однако это далеко не всегда static pod`ы, да и не всегда вообще Pod`ы. Например rke запускает эти модули k8s средствами docker`а в контейнерах.

    -- coredns Запускается средставим k8s используя контроллер Deployment

    -- kube-proxy Запускается средставим k8s используя контроллер DaemonSet

    Написан Dockerfile и собран контейнер с веб серевером

    Написан манифест для запуска контейнера в Pod`e с init контейнером

    ⭐ Дополнен манифест для перевода Pod`a в фазу Ready

Как запустить проект:

    Запустить команду

git clone https://github.com/otus-kuber-2020-04/xom4ek_platform.git && \
cd xom4ek_platform && \
git checkout kubernetes-intro && \
kubectl create -f kubernetes-intro/web-pod.yaml && \
kubectl create -f kubernetes-intro/frontend-pod-healthy.yaml

Как проверить работоспособность:

    Выполнить команды:

kubectl port-forward --address 0.0.0.0 pod/web 8000:8000 &
curl http://127.0.0.1:8000

    ⭐ Выполнить команду:

kubectl get pods -l run=frontend --field-selector=status.phase=Running

PR checklist:

    Выставлен label с темой домашнего задания
