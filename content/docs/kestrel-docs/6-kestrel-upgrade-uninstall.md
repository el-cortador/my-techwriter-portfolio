---
title: "Обновление и удаление"
description: "Обновление Kestrel до новой версии, откат к предыдущей ревизии и удаление."
summary: ""
date: 2026-09-10T00:00:00+00:00
lastmod: 2026-09-10T00:00:00+00:00
draft: false
weight: 60
toc: true
slug: "upgrade-uninstall"
params:
  seo:
    title: "" # custom title (optional)
    description: "" # custom description (recommended)
    canonical: "" # custom canonical URL (optional)
    robots: "" # custom robot tags (optional)
---

## Обновление Kestrel

1. Скачайте чарт новой версии:

   ```bash
   helm pull oci://$CHART_URL/appsec-hub/charts/appsec-hub --version <новая_версия>
   ```

2. Разархивируйте чарт и сравните новый `values.yaml` с текущим — обратите внимание на новые обязательные параметры.

3. Выполните обновление:

   ```bash
   helm upgrade appsec-hub . \
   --namespace appsec-hub \
   --values values.yaml
   ```

4. Проверьте статус подов и версию релиза:

   ```bash
   kubectl get pods -n appsec-hub
   helm list -n appsec-hub
   ```

   {{< callout context="note" icon="outline/info-circle" >}}
   Не меняйте значение `NEW_ENCRYPTION_SECRET_KEY` при обновлении — иначе Kestrel не сможет расшифровать ранее сохранённые данные.
   {{< /callout >}}

## Откат к предыдущей версии

Посмотреть историю ревизий релиза:

```bash
helm history appsec-hub -n appsec-hub
```

Откатиться на конкретную ревизию:

```bash
helm rollback appsec-hub <REVISION> -n appsec-hub
```

## Удаление Kestrel

Удалить ресурсы, созданные Helm-чартом:

```bash
helm uninstall appsec-hub -n appsec-hub
```

{{< callout context="note" icon="outline/info-circle" >}}
Команда удаляет только ресурсы, созданные чартом. Внешние сервисы — PostgreSQL, ClickHouse, S3-совместимое хранилище — не затрагиваются и при необходимости очищаются отдельно.
{{< /callout >}}

Полностью удалить namespace вместе с оставшимися ресурсами:

```bash
kubectl delete namespace appsec-hub
```
