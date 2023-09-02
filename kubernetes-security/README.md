1) Выполнена предподготовка к выполнению ДЗ
2) task01
Создать Service Account bob, дать ему роль admin в рамках всего кластера
Создать Service Account dave без доступа к кластеру
3) Создал новый неймспэйс (удобно чтобы потом прихлопнуть всё вместе после окончания работ)
4) Задеплоил три подика в этот неймспэйс
5) Создал сервис-аккаунт с именем bob
В текущей версии куба в отличии от материала в леции, теперь токен создаётся иначе:
Note:

Versions of Kubernetes before v1.22 automatically created long term credentials for accessing the Kubernetes API. This older mechanism was based on creating token Secrets that could then be mounted into running Pods. In more recent versions, including Kubernetes v1.28, API credentials are obtained directly by using the TokenRequest API, and are mounted into Pods using a projected volume. The tokens obtained using this method have bounded lifetimes, and are automatically invalidated when the Pod they are mounted into is deleted.

You can still manually create a service account token Secret; for example, if you need a token that never expires. However, using the TokenRequest subresource to obtain a token to access the API is recommended instead.
6) Создал в деректории дополнительный файлик с конфигом такого содержания:
apiVersion: v1
contexts:
- context:
    cluster: minikube
    namespace: security 
    user: bob 
  name: static 
kind: Config
preferences: {}
users:
- name: bob 
  user:
    token: [токен]

7) С помощью конфига связал роль claster-admin с пользователем боб.
8) Теперь по запросу k cluster-info я могу получить информацию о кластере.
9) Так же теперь я могу получать инфу по подам и что примечательно, теперь по умолчанию мне выдаются поды из namespace security. Однако и default просмотреть не запрещено.
10) Создал нового пользователя dave, ему доступ к кластеру не давал. Error from server (Forbidden): services is forbidden: User "system:serviceaccount:default:dave" cannot list resource "services" in API group "" in the namespace "kube-system"

Задание 2

Создать Namespace prometheus
Создать Service Account carol в этом Namespace
Дать всем Service Account в Namespace prometheus возможность
делать get, list, watch в отношении Pods всего кластера

Результат работы:
- команда: kubectl auth can-i --list --namespace=prometheus --as=system:serviceaccount:prometheus:carol
- ответ: "pods                                            []                                    []               [get watch list]"

Задание 3

Создать Namespace dev
Создать Service Account jane в Namespace dev
Дать jane роль admin в рамках Namespace dev
Создать Service Account ken в Namespace dev
Дать ken роль view в рамках Namespace dev

Результат команды: 

kubectl auth can-i --list --namespace=dev --as=system:serviceaccount:dev:jane
Resources                                        Non-Resource URLs                     Resource Names   Verbs
leases.coordination.k8s.io                       []                                    []               [create delete deletecollection get list patch update watch]
rolebindings.rbac.authorization.k8s.io           []                                    []               [create delete deletecollection get list patch update watch]
roles.rbac.authorization.k8s.io                  []                                    []               [create delete deletecollection get list patch update watch]
configmaps                                       []                                    []               [create delete deletecollection patch update get list watch]
events                                           []                                    []               [create delete deletecollection patch update get list watch]
persistentvolumeclaims                           []                                    []               [create delete deletecollection patch update get list watch]
pods                                             []                                    []               [create delete deletecollection patch update get list watch]
replicationcontrollers/scale                     []                                    []               [create delete deletecollection patch update get list watch]
replicationcontrollers                           []                                    []               [create delete deletecollection patch update get list watch]
services                                         []                                    []               [create delete deletecollection patch update get list watch]
daemonsets.apps                                  []                                    []               [create delete deletecollection patch update get list watch]
deployments.apps/scale                           []                                    []               [create delete deletecollection patch update get list watch]
deployments.apps                                 []                                    []               [create delete deletecollection patch update get list watch]
replicasets.apps/scale                           []                                    []               [create delete deletecollection patch update get list watch]
replicasets.apps                                 []                                    []               [create delete deletecollection patch update get list watch]
statefulsets.apps/scale                          []                                    []               [create delete deletecollection patch update get list watch]
statefulsets.apps                                []                                    []               [create delete deletecollection patch update get list watch]
horizontalpodautoscalers.autoscaling             []                                    []               [create delete deletecollection patch update get list watch]
cronjobs.batch                                   []                                    []               [create delete deletecollection patch update get list watch]
jobs.batch                                       []                                    []               [create delete deletecollection patch update get list watch]
daemonsets.extensions                            []                                    []               [create delete deletecollection patch update get list watch]
deployments.extensions/scale                     []                                    []               [create delete deletecollection patch update get list watch]
deployments.extensions                           []                                    []               [create delete deletecollection patch update get list watch]
ingresses.extensions                             []                                    []               [create delete deletecollection patch update get list watch]
networkpolicies.extensions                       []                                    []               [create delete deletecollection patch update get list watch]
replicasets.extensions/scale                     []                                    []               [create delete deletecollection patch update get list watch]
replicasets.extensions                           []                                    []               [create delete deletecollection patch update get list watch]
replicationcontrollers.extensions/scale          []                                    []               [create delete deletecollection patch update get list watch]
ingresses.networking.k8s.io                      []                                    []               [create delete deletecollection patch update get list watch]
networkpolicies.networking.k8s.io                []                                    []               [create delete deletecollection patch update get list watch]
poddisruptionbudgets.policy                      []                                    []               [create delete deletecollection patch update get list watch]
deployments.apps/rollback                        []                                    []               [create delete deletecollection patch update]
deployments.extensions/rollback                  []                                    []               [create delete deletecollection patch update]
pods/eviction                                    []                                    []               [create]
serviceaccounts/token                            []                                    []               [create]
selfsubjectreviews.authentication.k8s.io         []                                    []               [create]
localsubjectaccessreviews.authorization.k8s.io   []                                    []               [create]
selfsubjectaccessreviews.authorization.k8s.io    []                                    []               [create]
selfsubjectrulesreviews.authorization.k8s.io     []                                    []               [create]
pods/attach                                      []                                    []               [get list watch create delete deletecollection patch update]
pods/exec                                        []                                    []               [get list watch create delete deletecollection patch update]
pods/portforward                                 []                                    []               [get list watch create delete deletecollection patch update]
pods/proxy                                       []                                    []               [get list watch create delete deletecollection patch update]
secrets                                          []                                    []               [get list watch create delete deletecollection patch update]
services/proxy                                   []                                    []               [get list watch create delete deletecollection patch update]
bindings                                         []                                    []               [get list watch]
endpoints                                        []                                    []               [get list watch]
limitranges                                      []                                    []               [get list watch]
namespaces/status                                []                                    []               [get list watch]
namespaces                                       []                                    []               [get list watch]
persistentvolumeclaims/status                    []                                    []               [get list watch]
pods/log                                         []                                    []               [get list watch]
pods/status                                      []                                    []               [get list watch]
replicationcontrollers/status                    []                                    []               [get list watch]
resourcequotas/status                            []                                    []               [get list watch]
resourcequotas                                   []                                    []               [get list watch]
services/status                                  []                                    []               [get list watch]
controllerrevisions.apps                         []                                    []               [get list watch]
daemonsets.apps/status                           []                                    []               [get list watch]
deployments.apps/status                          []                                    []               [get list watch]
replicasets.apps/status                          []                                    []               [get list watch]
statefulsets.apps/status                         []                                    []               [get list watch]
horizontalpodautoscalers.autoscaling/status      []                                    []               [get list watch]
cronjobs.batch/status                            []                                    []               [get list watch]
jobs.batch/status                                []                                    []               [get list watch]
endpointslices.discovery.k8s.io                  []                                    []               [get list watch]
daemonsets.extensions/status                     []                                    []               [get list watch]
deployments.extensions/status                    []                                    []               [get list watch]
ingresses.extensions/status                      []                                    []               [get list watch]
replicasets.extensions/status                    []                                    []               [get list watch]
ingresses.networking.k8s.io/status               []                                    []               [get list watch]
poddisruptionbudgets.policy/status               []                                    []               [get list watch]

###################################################################################################

kubectl auth can-i --list --namespace=dev --as=system:serviceaccount:dev:ken 

Resources                                       Non-Resource URLs                     Resource Names   Verbs
selfsubjectreviews.authentication.k8s.io        []                                    []               [create]
selfsubjectaccessreviews.authorization.k8s.io   []                                    []               [create]
selfsubjectrulesreviews.authorization.k8s.io    []                                    []               [create]
bindings                                        []                                    []               [get list watch]
configmaps                                      []                                    []               [get list watch]
endpoints                                       []                                    []               [get list watch]
events                                          []                                    []               [get list watch]
limitranges                                     []                                    []               [get list watch]
namespaces/status                               []                                    []               [get list watch]
namespaces                                      []                                    []               [get list watch]
persistentvolumeclaims/status                   []                                    []               [get list watch]
persistentvolumeclaims                          []                                    []               [get list watch]
pods/log                                        []                                    []               [get list watch]
pods/status                                     []                                    []               [get list watch]
pods                                            []                                    []               [get list watch]
replicationcontrollers/scale                    []                                    []               [get list watch]
replicationcontrollers/status                   []                                    []               [get list watch]
replicationcontrollers                          []                                    []               [get list watch]
resourcequotas/status                           []                                    []               [get list watch]
resourcequotas                                  []                                    []               [get list watch]
serviceaccounts                                 []                                    []               [get list watch]
services/status                                 []                                    []               [get list watch]
services                                        []                                    []               [get list watch]
controllerrevisions.apps                        []                                    []               [get list watch]
daemonsets.apps/status                          []                                    []               [get list watch]
daemonsets.apps                                 []                                    []               [get list watch]
deployments.apps/scale                          []                                    []               [get list watch]
deployments.apps/status                         []                                    []               [get list watch]
deployments.apps                                []                                    []               [get list watch]
replicasets.apps/scale                          []                                    []               [get list watch]
replicasets.apps/status                         []                                    []               [get list watch]
replicasets.apps                                []                                    []               [get list watch]
statefulsets.apps/scale                         []                                    []               [get list watch]
statefulsets.apps/status                        []                                    []               [get list watch]
statefulsets.apps                               []                                    []               [get list watch]
horizontalpodautoscalers.autoscaling/status     []                                    []               [get list watch]
horizontalpodautoscalers.autoscaling            []                                    []               [get list watch]
cronjobs.batch/status                           []                                    []               [get list watch]
cronjobs.batch                                  []                                    []               [get list watch]
jobs.batch/status                               []                                    []               [get list watch]
jobs.batch                                      []                                    []               [get list watch]
endpointslices.discovery.k8s.io                 []                                    []               [get list watch]
daemonsets.extensions/status                    []                                    []               [get list watch]
daemonsets.extensions                           []                                    []               [get list watch]
deployments.extensions/scale                    []                                    []               [get list watch]
deployments.extensions/status                   []                                    []               [get list watch]
deployments.extensions                          []                                    []               [get list watch]
ingresses.extensions/status                     []                                    []               [get list watch]
ingresses.extensions                            []                                    []               [get list watch]
networkpolicies.extensions                      []                                    []               [get list watch]
replicasets.extensions/scale                    []                                    []               [get list watch]
replicasets.extensions/status                   []                                    []               [get list watch]
replicasets.extensions                          []                                    []               [get list watch]
replicationcontrollers.extensions/scale         []                                    []               [get list watch]
ingresses.networking.k8s.io/status              []                                    []               [get list watch]
ingresses.networking.k8s.io                     []                                    []               [get list watch]
networkpolicies.networking.k8s.io               []                                    []               [get list watch]
poddisruptionbudgets.policy/status              []                                    []               [get list watch]
poddisruptionbudgets.policy                     []                                    []               [get list watch]