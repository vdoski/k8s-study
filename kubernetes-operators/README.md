1. Создан cr.yml
2. При его apply выдаётся ошибка: error: resource mapping not found for name: "mysql-instance" namespace: "" from "deploy/cr.yml": no matches for kind "MySQL" in version "otus.homework/v1"
ensure CRDs are installed first

Если верить презентации, ошибка должна быть такой: error: unable to recognize "deploy/cr.yml": no matches for kind "MySQL" in version
"otus.homework/v1"

В принципе, одно и то же по контексту.
3. Создан crd.yml
4. При apply ошибка:
error: resource mapping not found for name: "mysqls.otus.homework" namespace: "" from "deploy/crd.yml": no matches for kind "CustomResourceDefinition" in version "apiextensions.k8s.io/v1beta1"
ensure CRDs are installed first
5. Меняю версию с беты на v1
6. Ошибка теперь следующая:
The CustomResourceDefinition "mysqls.otus.homework" is invalid: spec.versions[0].schema.openAPIV3Schema: Required value: schemas are required
7. Посмотрел документацию, добавил в конфиг 
    schema:
      openAPIV3Schema:
          type: object
8. Объект создался без ошибки теперь
9. Теперь ошибка несколько иная при apply первого конфига:
Error from server (BadRequest): error when creating "deploy/cr.yml": MySQL in version "v1" cannot be handled as a MySQL: strict decoding error: unknown field "spec", unknown field "usless_data"
10. Добавил в cr.yml такие поля:
   schema:
      openAPIV3Schema:
          type: object
          properties:
            spec:
              type: object
              properties:
                cronSpec:
                  type: string
                image:
                  type: string
                replicas:
                  type: integer
11. Теперь стало чуть лучше, ругается на другие поля:
Error from server (BadRequest): error when creating "deploy/cr.yml": MySQL in version "v1" cannot be handled as a MySQL: strict decoding error: unknown field "spec.database", unknown field "spec.password", unknown field "spec.storage_size", unknown field "usless_data"
12. Ага, понял что такое, дописал как надо, получилось теперь так:
schema:
      openAPIV3Schema:
          type: object
          properties:
            spec:
              type: object
              properties:
                database:
                  type: string
                image:
                  type: string
                password:
                  type: string
                storage_size:
                  type: string
            usless_data:
              type: string
13. Фиксирую результат в гите
14. Проверил взаимодействие с crd через команды:
    k get crd
    k get mysqls.otus.homework
    k describe mysqls.otus.homework
Вывод последней команды:
      kubernetes-operators git:(kubernetes-operators) k describe mysqls.otus.homework 
Name:         mysql-instance
Namespace:    default
Labels:       <none>
Annotations:  <none>
API Version:  otus.homework/v1
Kind:         MySQL
Metadata:
  Creation Timestamp:  2023-10-02T09:57:50Z
  Generation:          1
  Resource Version:    47218
  UID:                 cb7fc029-b6b4-4535-8231-d4a661307cbe
Spec:
  Database:      otus-database
  Image:         mysql:5.7
  Password:      otuspassword
  storage_size:  1Gi
usless_data:     useless info
Events:          <none>

15. В следующей части презентации как раз идёт речь о валидации, а я её как раз уже сделал. Вывод - в современной версии crd нельзя не указывать validation.

16. Задание по CRD:
Если сейчас из описания mysql убрать строчку из спецификации, то манифест будет принят API сервером. Для того, чтобы этого избежать, добавьте описание обязательных полей в CustomResourceDefinition

Полагаю, ответ в том чтобы добавить значения default: "..."

17. Далее идёт разработка контроллера на пайтоне
18. Запускаю новый сервис-аккаунт, роль, связываю роль с сервис аккаунтом, далее деплой-оператор
19. Проверил что появились PVC:
  kubernetes-operators git:(kubernetes-operators) ✗ k get pvc
NAME                        STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
backup-mysql-instance-pvc   Bound    pvc-53b2499f-3af2-4d00-88fa-9f8a791671fe   1Gi        RWO            standard       101s
mysql-instance-pvc          Bound    pvc-112970cb-ac19-424e-8d5e-99ae9f566b62   1Gi        RWO            standard       101s
20. Далее заполняю бд тестовыми данными, проверяю что заполнилось:

kubernetes-operators git:(kubernetes-operators) ✗ kubectl exec -it $MYSQLPOD -- mysql -potuspassword -e "select * from test;" otus-database
mysql: [Warning] Using a password on the command line interface can be insecure.
+----+-------------+
| id | name        |
+----+-------------+
|  1 | some data   |
|  2 | some data-2 |
+----+-------------+