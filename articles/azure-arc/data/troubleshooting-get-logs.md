---
title: Pobieranie dzienników do rozwiązywania problemów z kontrolerem danych z obsługą usługi Azure Arc
description: Pobierz dzienniki usługi, aby rozwiązać problem z kontrolerem danych z obsługą usługi Azure Arc.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 71c84b35c001be7fafdc2df53014050ae21dec63
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90939116"
---
# <a name="get-azure-arc-enabled-data-services-logs"></a>Pobierz dzienniki usług danych z obsługą usługi Azure Arc

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby można było pobrać dzienniki usług danych z obsługą usługi Azure ARC, potrzebne jest narzędzie Azure Data CLI. [Instrukcje instalacji](./install-client-tools.md)

Musisz mieć możliwość zalogowania się do usługi kontrolera usług danych z obsługą usługi Azure Arc jako administrator.

## <a name="get-azure-arc-enabled-data-services-logs"></a>Pobierz dzienniki usług danych z obsługą usługi Azure Arc

W celu rozwiązywania problemów można uzyskać dzienniki usług danych z obsługą usługi Azure Arc w ramach wszystkich zasobników lub określonych zasobników.  Można to zrobić za pomocą standardowych narzędzi Kubernetes, takich jak `kubectl logs` polecenie lub w tym artykule, będziesz używać narzędzia interfejsu wiersza polecenia platformy Azure, które ułatwia pobieranie wszystkich dzienników jednocześnie.

Najpierw upewnij się, że zalogowano się do kontrolera danych.

```console
azdata login
```

Następnie uruchom następujące polecenie, aby zrzucić dzienniki:
```console
azdata arc dc debug copy-logs --namespace <namespace name> --exclude-dumps --skip-compress

#Example:
#azdata arc dc debug copy-logs --namespace arc --exclude-dumps --skip-compress
```

Pliki dziennika zostaną utworzone w bieżącym katalogu roboczym domyślnie w podkatalogu o nazwie "Logs".  Pliki dziennika można wyprowadzać do innego katalogu przy użyciu `--target-folder` parametru.

Możesz wybrać kompresję plików, pomijając `--skip-compress` parametr.

Można wyzwolić i uwzględnić zrzuty pamięci, pomijając `--exclude-dumps` , ale nie jest to zalecane, chyba że pomoc techniczna firmy Microsoft zażądał zrzutów pamięci.  Wykonanie zrzutu pamięci wymaga ustawienia kontrolera danych `allowDumps` ustawionego na `true` czas tworzenia kontrolera danych.

Opcjonalnie możesz wybrać filtr, aby zbierać dzienniki dla tylko określonego ( `--pod` ) lub kontenera ( `--container` ) według nazwy.

Możesz również wybrać filtr, aby zbierać dzienniki dla określonego zasobu niestandardowego przez przekazanie do `--resource-kind` i `--resource-name` parametr.  `resource-kind`Wartość parametru powinna być jedną z niestandardowych nazw definicji zasobów, które mogą zostać pobrane przez polecenie `kubectl get customresourcedefinition` .

```console
azdata arc dc debug copy-logs --target-folder <desired folder> --exclude-dumps --skip-compress -resource-kind <custom resource definition name> --resource-name <resource name> --namespace <namespace name>

#Example
#azdata arc dc debug copy-logs --target-folder C:\temp\logs --exclude-dumps --skip-compress --resource-kind postgresql-12 --resource-name pg1 --namespace arc
```

Przykład hierarchii folderów.  Należy zauważyć, że hierarchia folderów jest uporządkowana według nazwy nazwy, a następnie według kontenera, a następnie według hierarchii katalogów w kontenerze.

```console
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