---
title: Pobieranie dzienników w celu rozwiązywania problemów z usługami danych z obsługą usługi Azure Arc
description: Dowiedz się, jak pobierać pliki dzienników z kontrolera danych w celu rozwiązywania problemów z usługami danych z obsługą usługi Azure Arc.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 0c4cff7583f08fe27649cee464fcef802cddd88f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93234051"
---
# <a name="get-logs-to-troubleshoot-azure-arc-enabled-data-services"></a>Pobieranie dzienników w celu rozwiązywania problemów z usługami danych z obsługą usługi Azure Arc

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Przed kontynuowaniem należy wykonać następujące czynności:

* [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]. Aby uzyskać więcej informacji, zobacz [Instalowanie narzędzi klienckich do wdrażania usług Azure Arc Data Services i zarządzania nimi](./install-client-tools.md).
* Konto administratora do logowania się do kontrolera danych z włączonym łukiem platformy Azure.

## <a name="get-log-files"></a>Pobierz pliki dziennika

Dzienniki usług można pobrać ze wszystkich zasobników lub określonych zasobników w celu rozwiązywania problemów. Jednym ze sposobów jest użycie standardowych narzędzi Kubernetes, takich jak `kubectl logs` polecenie. W tym artykule użyjesz [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] Narzędzia, które ułatwia pobieranie wszystkich dzienników jednocześnie.

1. Zaloguj się do kontrolera danych przy użyciu konta administratora.

   ```console
   azdata login
   ```

2. Uruchom następujące polecenie, aby zrzucić dzienniki:

   ```console
   azdata arc dc debug copy-logs --namespace <namespace name> --exclude-dumps --skip-compress
   ```

   Na przykład:

   ```console
   #azdata arc dc debug copy-logs --namespace arc --exclude-dumps --skip-compress
   ```

Kontroler danych tworzy pliki dziennika w bieżącym katalogu roboczym w podkatalogu o nazwie `logs` . 

## <a name="options"></a>Opcje

`azdata arc dc debug copy-logs`Polecenie udostępnia następujące opcje zarządzania danymi wyjściowymi:

* Wyprowadzanie plików dziennika do innego katalogu przy użyciu `--target-folder` parametru.
* Kompresuj pliki, pomijając `--skip-compress` parametr.
* Wywołaj i Uwzględnij zrzuty pamięci, pomijając `--exclude-dumps` . Ta metoda nie jest zalecana, chyba że pomoc techniczna firmy Microsoft zażądał zrzutów pamięci. Pobieranie zrzutu pamięci wymaga ustawienia kontrolera danych `allowDumps` `true` po utworzeniu kontrolera danych.
* Przefiltruj, aby zbierać dzienniki dla tylko określonego ( `--pod` ) lub kontenera ( `--container` ) według nazwy.
* Przefiltruj, aby zbierać dzienniki dla określonego zasobu niestandardowego przez przekazanie `--resource-kind` `--resource-name` parametrów i. `resource-kind`Wartość parametru powinna być jedną z nazw niestandardowych definicji zasobów. Te nazwy można pobrać przy użyciu polecenia `kubectl get customresourcedefinition` .

Za pomocą tych parametrów można zastąpić `<parameters>` w następującym przykładzie: 

```console
azdata arc dc debug copy-logs --target-folder <desired folder> --exclude-dumps --skip-compress -resource-kind <custom resource definition name> --resource-name <resource name> --namespace <namespace name>
```

Na przykład:

```console
#azdata arc dc debug copy-logs --target-folder C:\temp\logs --exclude-dumps --skip-compress --resource-kind postgresql-12 --resource-name pg1 --namespace arc
```

Przykładem jest następująca hierarchia folderów. Jest zorganizowany według nazwy, a następnie kontenera, a następnie według hierarchii katalogów w kontenerze.

```output
<export directory>
├───debuglogs-arc-20200827-180403
│   ├───bootstrapper-vl8j2
│   │   └───bootstrapper
│   │       ├───apt
│   │       └───fsck
│   ├───control-j2dm5
│   │   ├───controller
│   │   │   └───controller
│   │   │       ├───2020-08-27
│   │   │       └───2020-08-28
│   │   └───fluentbit
│   │       ├───agent
│   │       ├───fluentbit
│   │       └───supervisor
│   │           └───log
│   ├───controldb-0
│   │   ├───fluentbit
│   │   │   ├───agent
│   │   │   ├───fluentbit
│   │   │   └───supervisor
│   │   │       └───log
│   │   └───mssql-server
│   │       ├───agent
│   │       ├───mssql
│   │       ├───mssql-server
│   │       └───supervisor
│   │           └───log
│   ├───controlwd-ln6j8
│   │   └───controlwatchdog
│   │       └───controlwatchdog
│   ├───logsdb-0
│   │   └───elasticsearch
│   │       ├───agent
│   │       ├───elasticsearch
│   │       ├───provisioner
│   │       └───supervisor
│   │           └───log
│   ├───logsui-7gg2d
│   │   └───kibana
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───kibana
│   │       └───supervisor
│   │           └───log
│   ├───metricsdb-0
│   │   └───influxdb
│   │       ├───agent
│   │       ├───influxdb
│   │       └───supervisor
│   │           └───log
│   ├───metricsdc-2f62t
│   │   └───telegraf
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───supervisor
│   │       │   └───log
│   │       └───telegraf
│   ├───metricsdc-jznd2
│   │   └───telegraf
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───supervisor
│   │       │   └───log
│   │       └───telegraf
│   ├───metricsdc-n5vnx
│   │   └───telegraf
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───supervisor
│   │       │   └───log
│   │       └───telegraf
│   ├───metricsui-h748h
│   │   └───grafana
│   │       ├───agent
│   │       ├───grafana
│   │       └───supervisor
│   │           └───log
│   └───mgmtproxy-r5zxs
│       ├───fluentbit
│       │   ├───agent
│       │   ├───fluentbit
│       │   └───supervisor
│       │       └───log
│       └───service-proxy
│           ├───agent
│           ├───nginx
│           └───supervisor
│               └───log
└───debuglogs-kube-system-20200827-180431
    ├───coredns-8bbb65c89-kklt7
    │   └───coredns
    ├───coredns-8bbb65c89-z2vvr
    │   └───coredns
    ├───coredns-autoscaler-5585bf8c9f-g52nt
    │   └───autoscaler
    ├───kube-proxy-5c9s2
    │   └───kube-proxy
    ├───kube-proxy-h6x56
    │   └───kube-proxy
    ├───kube-proxy-nd2b7
    │   └───kube-proxy
    ├───metrics-server-5f54b8994-vpm5r
    │   └───metrics-server
    └───tunnelfront-db87f4cd8-5xwxv
        ├───tunnel-front
        │   ├───apt
        │   └───journal
        └───tunnel-probe
            ├───apt
            ├───journal
            └───openvpn
```

## <a name="next-steps"></a>Następne kroki

[azdata Arc — Dzienniki debugowania DC](/sql/azdata/reference/reference-azdata-arc-dc-debug#azdata-arc-dc-debug-copy-logs?toc=/azure/azure-arc/data/toc.json&bc=/azure/azure-arc/data/breadcrumb/toc.json)
