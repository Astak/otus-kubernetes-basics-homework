# OTUS | Базовые сущности Kubernetes | Домашнее задание
## Как установить
Создать пространство имен и переключить на него контекст:
```
kubectl create ns otus
kubectl config set-context --current --namespace=otus
```
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
    --set auth.database=users_api
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
DB_PORT: 5432
DB_NAME: users_api
EOF
kubectl create configmap users-api-config --from-file=config.yml=config.yml
```
### Скачать helm chart приложения users-api
```
git clone https://github.com/Astak/otus-kubernetes-basics-homework.git
cd otus-kubernetes-basics-homework
```

### Установать heml chart приложения users-api
```
helm install users-api helm/users_api/ \
--set dbSecret=db-user-pass
--set appConfig=users-api-config
```