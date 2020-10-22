---
title: Wyświetlanie dzienników i metryk przy użyciu Kibana i Grafana
description: Wyświetlanie dzienników i metryk przy użyciu Kibana i Grafana
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 3693c30a34601512770f5d9071f5d786410fb00e
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2020
ms.locfileid: "92360381"
---
# <a name="view-logs-and-metrics-using-kibana-and-grafana"></a>Wyświetlanie dzienników i metryk przy użyciu Kibana i Grafana

Dostępne są pulpity nawigacyjne narzędzi Kibana i Grafana, które zapewniają szczegółowe informacje i przejrzystość w przestrzeniach nazw Kubernetes używanych przez usługi danych z obsługą usługi Azure Arc.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="retrieve-the-ip-address-of-your-cluster"></a>Pobieranie adresu IP klastra

Aby uzyskać dostęp do pulpitów nawigacyjnych, należy pobrać adres IP klastra. Metoda pobierania poprawnego adresu IP różni się w zależności od sposobu wdrożenia Kubernetes. Przejdź do opcji poniżej, aby znaleźć odpowiedni dla siebie sposób.

### <a name="azure-virtual-machine"></a>Maszyna wirtualna platformy Azure

Aby pobrać publiczny adres IP, użyj następującego polecenia:

```azurecli
az network public-ip list -g azurearcvm-rg --query "[].{PublicIP:ipAddress}" -o table
```

### <a name="kubeadm-cluster"></a>Klaster Kubeadm

Aby pobrać adres IP klastra, użyj następującego polecenia:

```console
kubectl cluster-info
```


### <a name="aks-or-other-load-balanced-cluster"></a>AKS lub inny klaster z równoważeniem obciążenia

Aby monitorować środowisko w AKS lub innym klastrze z równoważeniem obciążenia, należy uzyskać adres IP usługi serwera proxy zarządzania. Użyj tego polecenia, aby pobrać **zewnętrzny adres IP** :

```console
kubectl get svc mgmtproxy-svc-external -n <namespace>

#Example:
#kubectl get svc mgmtproxy-svc-external -n arc
NAME                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)           AGE
mgmtproxy-svc-external   LoadBalancer   10.0.186.28   52.152.148.25   30777:30849/TCP   19h
```

## <a name="additional-firewall-configuration"></a>Dodatkowa konfiguracja zapory

Aby uzyskać dostęp do punktów końcowych Kibana i Grafana, konieczne może okazać się otwieranie portów na zaporze.

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

## <a name="monitor-azure-sql-managed-instances-on-azure-arc"></a>Monitoruj wystąpienia zarządzane usługi Azure SQL w usłudze Azure Arc

Teraz, gdy masz adres IP i masz dostęp do portów, powinno być możliwe uzyskanie dostępu do Grafana i Kibana.

> [!NOTE]
>  Po wyświetleniu monitu o podanie nazwy użytkownika i hasła wprowadź nazwę użytkownika i hasło podane w momencie utworzenia kontrolera danych usługi Azure Arc.

> [!NOTE]
>  Zostanie wyświetlony monit z ostrzeżeniem dotyczącym certyfikatu, ponieważ certyfikaty używane w wersji zapoznawczej są certyfikatami z podpisem własnym.

Użyj poniższego wzorca adresu URL, aby uzyskać dostęp do pulpitów nawigacyjnych rejestrowania i monitorowania dla wystąpienia zarządzanego Azure SQL:

```html
https://<external-ip-from-above>:30777/grafana
https://<external-ip-from-above>:30777/kibana
```

Odpowiednie pulpity nawigacyjne to:

* "Metryki wystąpienia zarządzanego usługi Azure SQL"
* "Metryki węzła hosta"
* "Metryki" hosta "

## <a name="monitor-azure-database-for-postgresql-hyperscale---azure-arc"></a>Monitorowanie Azure Database for PostgreSQL skalowanie — łuk platformy Azure

Użyj poniższego wzorca adresu URL, aby uzyskać dostęp do pulpitów nawigacyjnych rejestrowania i monitorowania dla PostgreSQL:

```html
https://<external-ip-from-above>:30777/grafana
https://<external-ip-from-above>:30777/kibana
```

Odpowiednie pulpity nawigacyjne to:

* "Metryki Postgres"
* "Metryki tabeli Postgres"
* "Metryki węzła hosta"
* "Metryki" hosta "

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

