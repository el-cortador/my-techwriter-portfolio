---
title: "Установка с помощью Helm-чарта"
description: "Пошаговая установка Kestrel в Kubernetes с помощью Helm-чарта и проверка работоспособности."
summary: ""
date: 2026-09-10T00:00:00+00:00
lastmod: 2026-09-10T00:00:00+00:00
draft: false
weight: 30
toc: true
slug: "install"
params:
  seo:
    title: "" # custom title (optional)
    description: "" # custom description (recommended)
    canonical: "" # custom canonical URL (optional)
    robots: "" # custom robot tags (optional)
---

## Перед началом работы

Перед началом установки убедитесь, что выполнены все условия из раздела [Предварительные требования](/docs/kestrel-docs/2-kestrel-install-prerequisites).

## Установка

Для установки Kestrel с помощью Helm-чарта выполните следующие шаги:

1. Задайте переменные окружения:

   ```bash
   export CHART_USERNAME=""
   export CHART_PASSWORD=""
   export CHART_URL="registry.example.com"
   export VERSION="1.6.0"
   ```

   {{< callout context="note" icon="outline/info-circle" >}}
   Значения `CHART_URL`, `CHART_USERNAME`, `CHART_PASSWORD`, `VERSION` необходимо запросить у службы поддержки.
   {{< /callout >}}

2. Авторизуйтесь в реестре Helm-чартов:

   ```bash
   helm registry login \
   --username $CHART_USERNAME \
   --password $CHART_PASSWORD \
   $CHART_URL
   ```

3. Скачайте чарт:

   ```bash
   helm pull oci://$CHART_URL/appsec-hub/charts/appsec-hub --version $VERSION
   ```

4. Разархивируйте чарт и перейдите в директорию `appsec-hub`:

   ```bash
   tar xvf appsec-hub-$VERSION.tgz
   cd appsec-hub
   ```

5. Сгенерируйте ключ шифрования для базы данных:

   ```bash
   openssl rand -hex 32
   ```

   Сохраните значение: оно понадобится на следующем шаге и при всех последующих обновлениях.

6. Отредактируйте файл `values.yaml` в соответствии с вашей инфраструктурой. Полное описание параметров — в разделе [Справочник параметров values.yaml](/docs/kestrel-docs/4-kestrel-configuration-reference).

   | Параметр | Описание |
   | --- | --- |
   | `default.domain` | Доменное имя для веб-интерфейса |
   | `secret.infracreds.envs.POSTGRES_*` | Адрес и учётные данные PostgreSQL |
   | `secret.infracreds.envs.MINIO_*` | Учётные данные MinIO/S3 |
   | `secret.infracreds.envs.CLICKHOUSE_*` | Учётные данные ClickHouse |
   | `secret.infracreds.envs.NEW_ENCRYPTION_SECRET_KEY` | Ключ шифрования базы данных из шага 5 |
   | `pullSecret.appsec-hub-pullsecret.*` | Учётные данные для доступа к container-реестру |

7. Установите Kestrel:

   ```bash
   helm upgrade --install appsec-hub \
   --create-namespace \
   --namespace appsec-hub \
   --values values.yaml \
   .
   ```

   {{< callout context="note" icon="outline/info-circle" >}}
   Вы также можете передать значения параметров через командную строку, не редактируя файл `values.yaml`. Для этого выполните команду:

   ```bash
   helm upgrade --install appsec-hub . \
   --create-namespace \
   --namespace appsec-hub \
   --set default.domain="example.com" \
   --set secret.infracreds.envs.POSTGRES_HOST="postgres.example.com" \
   --set secret.infracreds.envs.POSTGRES_USER="postgres-user" \
   --set-string secret.infracreds.envs.POSTGRES_PASSWORD="password" \
   --set secret.infracreds.envs.MINIO_ROOT_USER="root-user" \
   --set-string secret.infracreds.envs.MINIO_ROOT_PASSWORD="password" \
   --set secret.infracreds.envs.CLICKHOUSE_ADMIN_USER="admin-user" \
   --set-string secret.infracreds.envs.CLICKHOUSE_ADMIN_PASSWORD="password" \
   --set-string secret.infracreds.envs.NEW_ENCRYPTION_SECRET_KEY="key" \
   --set pullSecret.appsec-hub-pullsecret.username="username" \
   --set-string pullSecret.appsec-hub-pullsecret.password="password"
   ```
   {{< /callout >}}

## Проверка работоспособности

Для проверки работоспособности Kestrel выполните следующие шаги:

1. Убедитесь, что поды запущены:

   ```bash
   kubectl get pods -n appsec-hub
   ```

   Поды должны иметь статус `Running`, а показатель `READY` должен соответствовать ожидаемому (например, `1/1`).

2. Проверьте статус `Ingress`:

   ```bash
   kubectl get ingress -n appsec-hub
   ```

3. Откройте в браузере адрес, указанный в `default.domain`, и войдите в систему.
