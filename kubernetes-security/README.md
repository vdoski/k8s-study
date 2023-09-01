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