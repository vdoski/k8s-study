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
