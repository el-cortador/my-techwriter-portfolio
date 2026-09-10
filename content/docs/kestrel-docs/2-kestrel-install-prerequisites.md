---
title: "Предварительные требования"
description: "Инструменты и требования к Kubernetes-кластеру, которые нужны до установки Kestrel."
summary: ""
date: 2026-09-10T00:00:00+00:00
lastmod: 2026-09-10T00:00:00+00:00
draft: false
weight: 20
toc: true
slug: "install-prerequisites"
params:
  seo:
    title: "" # custom title (optional)
    description: "" # custom description (recommended)
    canonical: "" # custom canonical URL (optional)
    robots: "" # custom robot tags (optional)
---

Перед установкой Kestrel с помощью Helm-чарта убедитесь, что выполнены все условия ниже.

## Инструменты на рабочей машине

Рабочая машина, на которую будет установлен Kestrel, должна соответствовать следующим требованиям:
- установлены утилиты `kubectl`, `helm` и `openssl` (нужна для генерации ключа шифрования при установке);
- настроена конфигурация `kubeconfig` — команда `kubectl get nodes` должна возвращать список узлов кластера;
- есть доступ к container-реестру, например `registry.example.com` (адрес и учётные данные предоставляются администратором или службой поддержки).

## Требования к Kubernetes-кластеру

Перед установкой убедитесь, что кластер Kubernetes соответствует следующим требованиям:
- установлены и настроены `ingress-nginx-controller` и соответствующий `IngressClass`;
- настроен `StorageClass`, помеченный как `default` — проверьте командой:

  ```bash
  kubectl get storageclass
  ```

- у администратора есть права на создание ресурсов в целевом namespace;
- развёрнуты и доступны из кластера внешние сервисы для хранения данных: PostgreSQL, ClickHouse и S3-совместимое хранилище (например, MinIO).

Как только все пункты выполнены, переходите к разделу [Установка с помощью Helm-чарта](/docs/kestrel-docs/3-kestrel-install).
