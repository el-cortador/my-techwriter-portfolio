---
title: "Устранение неполадок"
description: "Типичные проблемы при установке и работе Kestrel: причины и способы проверки."
summary: ""
date: 2026-09-10T00:00:00+00:00
lastmod: 2026-09-10T00:00:00+00:00
draft: false
weight: 50
toc: true
slug: "troubleshooting"
params:
  seo:
    title: "" # custom title (optional)
    description: "" # custom description (recommended)
    canonical: "" # custom canonical URL (optional)
    robots: "" # custom robot tags (optional)
---

## Под не переходит в статус Running

Возможные причины и способы их устранения:

- **StorageClass не настроен по умолчанию.** Выполните `kubectl get storageclass` — ожидаемый вывод должен содержать пометку `(default)` напротив нужного класса;
- **Недостаточно прав на создание ресурсов в namespace `appsec-hub`.** Проверьте роли и привязки RBAC для используемой учётной записи;
- **Не удаётся загрузить образ из реестра.** Проверьте события пода: `kubectl describe pod <pod-name> -n appsec-hub` — обратите внимание на ошибки вида `ImagePullBackOff` и корректность значений в `pullSecret.appsec-hub-pullsecret`.

## Ingress создан, но веб-интерфейс недоступен

Способы устранения:

- Убедитесь, что в кластере установлен и настроен `ingress-nginx-controller`;
- Проверьте, что `ingress.className` в `values.yaml` совпадает с реальным именем `IngressClass` в кластере: `kubectl get ingressclass`;
- Проверьте статус ресурса: `kubectl get ingress -n appsec-hub` — поле `ADDRESS` должно быть заполнено.

## Ошибка авторизации при `helm registry login` или `helm pull`

Способы устранения:

- Проверьте, что значения `CHART_USERNAME`, `CHART_PASSWORD` и `CHART_URL` актуальны — они выдаются службой поддержки и могут иметь ограниченный срок действия;
- Повторите `helm registry login`, прежде чем выполнять `helm pull`.

## После обновления Kestrel не может расшифровать существующие данные

Значение `NEW_ENCRYPTION_SECRET_KEY` должно оставаться неизменным при всех последующих обновлениях. Если ключ был утерян или изменён по ошибке — восстановление данных без исходного значения невозможно, поэтому храните его в надёжном месте сразу после первой установки.

## Kestrel не подключается к PostgreSQL, ClickHouse или S3-хранилищу

Способы устранения:

- Проверьте сетевую доступность внешних сервисов из кластера (правила сетевых политик, security groups, firewall);
- Убедитесь, что значения в `secret.infracreds.envs.*` актуальны и не содержат опечаток;
- Проверьте логи основного сервиса: `kubectl logs -n appsec-hub deployment/appsec-hub` — ошибки подключения обычно указывают, какой именно сервис недоступен.
