Задание 1:

Запусить statefulset c Minio.
kubectl apply -f https://raw.githubusercontent.com/express42/otus-platform- snippets/master/Module-02/Kuberenetes-volumes/minio-statefulset.yaml

Запусить headless service c Minio
kubectl apply -f https://raw.githubusercontent.com/express42/otus-platform-snippets/master/Module-02/Kuberenetes-volumes/minio-headless-service.yaml


Дополнительное задание: перенести ключи в secrets.
1) Создал файлик с секретом, зашифрован в base64
2) В minio-statefulset прописал откуда брать логин и пароль
3) Проверил с помощью браузера, но предварительно пробросил порт kubectl port-forward pod/minio-1 9000:9000 &