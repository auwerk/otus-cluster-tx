# Домашнее задание по теме "Распределенные транзакции"

## Сервисы

[otus-service-user](https://github.com/auwerk/otus-service-user)

## Настройка /etc/hosts

Добавить записи:

- 127.0.0.1 arch.homework
- 127.0.0.1 keycloak.arch.homework

## Разворачивание кластера

- Создание пространств имён

```shell
kubectl apply -f ./namespaces.yaml
```

- Установка cert-manager, создание self-signed cluster issuer

```shell
helm install cert-manager jetstack/cert-manager --set installCRDs=true --namespace security
kubectl apply -f ./cert-manager/resources.yaml
```

- Установка nginx-ingress-controller

```shell
helm install nginx-ingress ingress-nginx/ingress-nginx --namespace nginx-ic
```

- Установка Keycloak

```shell
helm install keycloak bitnami/keycloak -f ./keycloak/keycloak-values.yaml --namespace security
```

- Импорт realm и клиента в Keycloak

1. Зайти в консоль администрирования Keycloak (admin/NIMDA после деплоя)
2. Импортировать realm из ./keycloak/config/realms/otus.json
3. В импортированный realm импортировать клиент из ./keycloak/config/clients/otus-service-user.json

- Установка oauth2-proxy

```shell
helm install oauth2-proxy oauth2-proxy/oauth2-proxy -f ./oauth2-proxy/oauth2-proxy-values.yaml --namespace rev-proxy
```

- Установка PostgreSQL для сервиса user

```shell
kubectl apply -f ./db/resources/otus-user.yaml
helm install postgresql -f ./db/values/postgresql-user-values.yaml bitnami/postgresql --namespace otus-user
```

- Установка сервиса user

```shell
helm install otus-service-user --namespace otus-user .\helm-charts\otus-service-user\
```

- Установка PostgreSQL для сервиса order

```shell
kubectl apply -f ./db/resources/otus-order.yaml
helm install postgresql -f ./db/values/postgresql-order-values.yaml bitnami/postgresql --namespace otus-order
```
