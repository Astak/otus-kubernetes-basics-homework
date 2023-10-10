# OTUS | Базовые сущности Kubernetes | Домашнее задание
## Как установить
### Создать секрет для базы данных
```
kubectl create secret generic db-user-pass \
    --from-literal=postgres-password=your-admin-password \
    --from-literal=password=your-password
```
### Установка базы Postgres
```
helm install db \
    --set auth.existingSecret=db-user-pass
    --set auth.username=otus
    --set auth.database=users
    oci://registry-1.docker.io/bitnamicharts/postgresql
```
### Создать ConfigMap для хранения настроек приложения
В поле DB_HOST ввести значение, которое отобразилось как DNS имя БД после установки Postgres
```
cat <<EOF >./config.yml
PORT: 80
DB_USER: otus
DB_PASSWORD: will-be-replaced-by-value-from-secret
DB_HOST: db-postgresql.otus.svc.cluster.local
DB_PORT: 5435
DB_NAME: users
EOF
kubectl create configmap config --from-file=config.yml=config.yml
```