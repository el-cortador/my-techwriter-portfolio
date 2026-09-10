---
title: "Справочник параметров values.yaml"
description: "Параметры values.yaml для установки и обновления Kestrel: типы, значения по умолчанию, пример."
summary: ""
date: 2026-09-10T00:00:00+00:00
lastmod: 2026-09-10T00:00:00+00:00
draft: false
weight: 40
toc: true
slug: "configuration-reference"
params:
  seo:
    title: "" # custom title (optional)
    description: "" # custom description (recommended)
    canonical: "" # custom canonical URL (optional)
    robots: "" # custom robot tags (optional)
---

Полный список параметров, которые можно задать при установке или обновлении Kestrel — как через файл `values.yaml`, так и через флаг `--set` / `--set-string`.

## Основные параметры

| Параметр | Тип | Обязательный | По умолчанию | Описание |
| --- | --- | --- | --- | --- |
| `default.domain` | string | да | — | Доменное имя, по которому будет доступен веб-интерфейс Kestrel |
| `replicaCount` | int | нет | `1` | Количество реплик основного сервиса |
| `image.tag` | string | нет | значение `VERSION` при установке | Версия образа Kestrel |

## Ingress

| Параметр | Тип | Обязательный | По умолчанию | Описание |
| --- | --- | --- | --- | --- |
| `ingress.enabled` | bool | нет | `true` | Включает создание Ingress-ресурса |
| `ingress.className` | string | нет | `nginx` | Имя `IngressClass`, настроенного в кластере |

## Хранилище

| Параметр | Тип | Обязательный | По умолчанию | Описание |
| --- | --- | --- | --- | --- |
| `persistence.enabled` | bool | нет | `true` | Включает постоянное хранилище для локальных артефактов |
| `persistence.size` | string | нет | `10Gi` | Размер создаваемого PVC |

## Учётные данные внешних сервисов (`secret.infracreds.envs`)

| Параметр | Тип | Обязательный | Описание |
| --- | --- | --- | --- |
| `POSTGRES_HOST` | string | да | Адрес сервера PostgreSQL |
| `POSTGRES_USER` | string | да | Имя пользователя PostgreSQL |
| `POSTGRES_PASSWORD` | string | да | Пароль пользователя PostgreSQL |
| `MINIO_ROOT_USER` | string | да | Access key S3-совместимого хранилища |
| `MINIO_ROOT_PASSWORD` | string | да | Secret key S3-совместимого хранилища |
| `CLICKHOUSE_ADMIN_USER` | string | да | Имя администратора ClickHouse |
| `CLICKHOUSE_ADMIN_PASSWORD` | string | да | Пароль администратора ClickHouse |
| `NEW_ENCRYPTION_SECRET_KEY` | string | да | Ключ шифрования базы данных, генерируется командой `openssl rand -hex 32`. Значение нельзя менять при последующих обновлениях — см. раздел [Обновление и удаление](/docs/kestrel-docs/6-kestrel-upgrade-uninstall) |

## Доступ к container-реестру (`pullSecret.appsec-hub-pullsecret`)

| Параметр | Тип | Обязательный | Описание |
| --- | --- | --- | --- |
| `username` | string | да | Логин для доступа к container-реестру |
| `password` | string | да | Пароль для доступа к container-реестру |

## Пример фрагмента values.yaml

```yaml
default:
  domain: kestrel.example.com

replicaCount: 2

ingress:
  enabled: true
  className: nginx

persistence:
  enabled: true
  size: 20Gi

secret:
  infracreds:
    envs:
      POSTGRES_HOST: postgres.internal.example.com
      POSTGRES_USER: kestrel
      POSTGRES_PASSWORD: "********"
      MINIO_ROOT_USER: kestrel-storage
      MINIO_ROOT_PASSWORD: "********"
      CLICKHOUSE_ADMIN_USER: kestrel-analytics
      CLICKHOUSE_ADMIN_PASSWORD: "********"
      NEW_ENCRYPTION_SECRET_KEY: "********"

pullSecret:
  appsec-hub-pullsecret:
    username: registry-user
    password: "********"
```

{{< callout context="note" icon="outline/info-circle" >}}
Имена технических ресурсов (namespace, имя релиза, `pullSecret.appsec-hub-pullsecret`) используют внутренний codename `appsec-hub` — это отдельное от публичного названия продукта Kestrel обозначение, которое сохраняется для обратной совместимости команд установки.
{{< /callout >}}
