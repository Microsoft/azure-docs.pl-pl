---
title: Wyświetlanie dzienników i metryk przy użyciu Kibana i Grafana
description: Wyświetlanie dzienników i metryk przy użyciu Kibana i Grafana
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 12/08/2020
ms.topic: how-to
ms.openlocfilehash: cb53aba300b933c78d9ac2f5fc5cf8054f3413e3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104670005"
---
# <a name="view-logs-and-metrics-using-kibana-and-grafana"></a>Wyświetlanie dzienników i metryk przy użyciu Kibana i Grafana

Dostępne są pulpity nawigacyjne narzędzi Kibana i Grafana, które zapewniają szczegółowe informacje i przejrzystość w przestrzeniach nazw Kubernetes używanych przez usługi danych z obsługą usługi Azure Arc.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]


## <a name="monitor-azure-sql-managed-instances-on-azure-arc"></a>Monitoruj wystąpienia zarządzane usługi Azure SQL w usłudze Azure Arc

Aby uzyskać dostęp do dzienników i pulpitów nawigacyjnych monitorowania dla wystąpienia zarządzanego usługi SQL, uruchom następujące `azdata` polecenie interfejsu wiersza polecenia

```bash

azdata arc sql endpoint list -n <name of SQL instance>

```
Odpowiednie pulpity nawigacyjne Grafana są następujące:

* "Metryki wystąpienia zarządzanego usługi Azure SQL"
* "Metryki węzła hosta"
* "Metryki" hosta "


> [!NOTE]
>  Po wyświetleniu monitu o podanie nazwy użytkownika i hasła wprowadź nazwę użytkownika i hasło podane w momencie utworzenia kontrolera danych usługi Azure Arc.

> [!NOTE]
>  Zostanie wyświetlony monit z ostrzeżeniem dotyczącym certyfikatu, ponieważ certyfikaty używane w wersji zapoznawczej są certyfikatami z podpisem własnym.


## <a name="monitor-azure-database-for-postgresql-hyperscale-on-azure-arc"></a>Monitoruj Azure Database for PostgreSQL skalowanie w usłudze Azure Arc

Aby uzyskać dostęp do dzienników i pulpitów nawigacyjnych monitorowania dla PostgreSQL, uruchom następujące `azdata` polecenie interfejsu wiersza polecenia

```bash

azdata arc postgres endpoint list -n <name of postgreSQL instance>

```

Odpowiednie pulpity nawigacyjne postgreSQL są następujące:

* "Metryki Postgres"
* "Metryki tabeli Postgres"
* "Metryki węzła hosta"
* "Metryki" hosta "


## <a name="additional-firewall-configuration"></a>Dodatkowa konfiguracja zapory

W zależności od tego, gdzie jest wdrożony kontroler danych, aby uzyskać dostęp do punktów końcowych Kibana i Grafana, należy otworzyć porty w zaporze.

Poniżej znajduje się przykład, jak to zrobić dla maszyny wirtualnej platformy Azure. Należy to zrobić, jeśli wdrożono Kubernetes przy użyciu skryptu.

Poniższe kroki przedstawiają sposób tworzenia reguły sieciowej grupy zabezpieczeń dla punktów końcowych Kibana i Grafana:

### <a name="find-the-name-of-the-nsg"></a>Znajdowanie nazwy sieciowej grupy zabezpieczeń

```azurecli
az network nsg list -g azurearcvm-rg --query "[].{NSGName:name}" -o table
```

### <a name="add-the-nsg-rule"></a>Dodawanie reguły sieciowej grupy zabezpieczeń

Po utworzeniu nazwy sieciowej grupy zabezpieczeń można dodać regułę przy użyciu następującego polecenia:

```azurecli
az network nsg rule create -n ports_30777 --nsg-name azurearcvmNSG --priority 600 -g azurearcvm-rg --access Allow --description 'Allow Kibana and Grafana ports' --destination-address-prefixes '*' --destination-port-ranges 30777 --direction Inbound --protocol Tcp --source-address-prefixes '*' --source-port-ranges '*'
```


## <a name="next-steps"></a>Następne kroki
- Wypróbuj [przekazywanie metryk i dzienników do Azure monitor](upload-metrics-and-logs-to-azure-monitor.md)
- Przeczytaj o Grafana:
   - [Rozpoczęcie pracy](https://grafana.com/docs/grafana/latest/getting-started/getting-started)
   - [Podstawy Grafana](https://grafana.com/tutorials/grafana-fundamentals/#1)
   - [Samouczki Grafana](https://grafana.com/tutorials/grafana-fundamentals/#1)
- Przeczytaj o Kibana
   - [Wprowadzenie](https://www.elastic.co/webinars/getting-started-kibana?baymax=default&elektra=docs&storm=top-video)
   - [Przewodnik Kibana](https://www.elastic.co/guide/en/kibana/current/index.html)
   - [Wprowadzenie do rozwijania pulpitu nawigacyjnego za pomocą wizualizacji danych w programie Kibana](https://www.elastic.co/webinars/dashboard-drilldowns-with-data-visualizations-in-kibana/)
   - [Jak kompilować pulpity nawigacyjne Kibana](https://www.elastic.co/webinars/how-to-build-kibana-dashboards/)

