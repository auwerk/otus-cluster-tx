# Домашнее задание по теме "Распределенные транзакции"

## Сервисы

- [otus-service-user](https://github.com/auwerk/otus-service-user)
- [otus-service-order](https://github.com/auwerk/otus-service-order)
- [otus-service-product](https://github.com/auwerk/otus-service-product)
- [otus-service-billing](https://github.com/auwerk/otus-service-billing)

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

- Установка сервиса order

```shell
helm install otus-service-order --namespace otus-order .\helm-charts\otus-service-order\
```

- Установка PostgreSQL для сервиса product

```shell
kubectl apply -f ./db/resources/otus-product.yaml
helm install postgresql -f ./db/values/postgresql-product-values.yaml bitnami/postgresql --namespace otus-product
```

- Установка сервиса product

```shell
helm install otus-service-product --namespace otus-product .\helm-charts\otus-service-product\
```

- Установка PostgreSQL для сервиса billing

```shell
kubectl apply -f ./db/resources/otus-billing.yaml
helm install postgresql -f ./db/values/postgresql-billing-values.yaml bitnami/postgresql --namespace otus-billing
```

- Установка сервиса billing

```shell
helm install otus-service-billing --namespace otus-billing .\helm-charts\otus-service-billing\
```

- Установка PostgreSQL для сервиса license

```shell
kubectl apply -f ./db/resources/otus-license.yaml
helm install postgresql -f ./db/values/postgresql-license-values.yaml bitnami/postgresql --namespace otus-license
```

- Установка сервиса license

```shell
helm install otus-service-license --namespace otus-license .\helm-charts\otus-service-license\
```
