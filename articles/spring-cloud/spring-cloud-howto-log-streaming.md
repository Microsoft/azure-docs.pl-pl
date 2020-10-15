---
title: Przesyłanie strumieniowe dzienników aplikacji Azure Spring Cloud w czasie rzeczywistym
description: Jak używać przesyłania strumieniowego dzienników do natychmiastowego wyświetlania dzienników aplikacji
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/14/2019
ms.custom: devx-track-java
ms.openlocfilehash: 04b03e3857bc5b01c25112f2a0d60c6468ecd42a
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2020
ms.locfileid: "92093926"
---
# <a name="stream-azure-spring-cloud-app-logs-in-real-time"></a>Przesyłanie strumieniowe dzienników aplikacji Azure Spring Cloud w czasie rzeczywistym

**Ten artykuł ma zastosowanie do:** ✔️ Java ✔️ C #

Chmura sprężynowa Azure umożliwia przesyłanie strumieniowe dzienników w interfejsie wiersza polecenia platformy Azure w czasie rzeczywistym w celu rozwiązywania problemów. Możesz również [analizować dzienniki i metryki przy użyciu ustawień diagnostycznych](./diagnostic-services.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Zainstaluj [rozszerzenie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) dla chmury wiosennej, minimalną wersję 0.2.0.
* Wystąpienie **chmurowej platformy Azure** z uruchomioną aplikacją, na przykład [aplikacja Wiosenna w chmurze](./spring-cloud-quickstart.md).

> [!NOTE]
>  Rozszerzenie interfejsu wiersza polecenia ASC zostało zaktualizowane z wersji 0.2.0 do 0.2.1. Ta zmiana ma wpływ na składnię polecenia przesyłania strumieniowego dzienników: `az spring-cloud app log tail` , która została zastąpiona przez: `az spring-cloud app logs` . Polecenie: `az spring-cloud app log tail` będzie przestarzałe w przyszłej wersji. Jeśli używasz wersji 0.2.0, możesz przeprowadzić uaktualnienie do 0.2.1. Najpierw usuń starą wersję za pomocą polecenia: `az extension remove -n spring-cloud` .  Następnie zainstaluj 0.2.1 przez polecenie: `az extension add -n spring-cloud` .

## <a name="use-cli-to-tail-logs"></a>Używanie interfejsu wiersza polecenia do dzienników końcowych

Aby uniknąć wielokrotnego określania nazwy grupy zasobów i wystąpienia usługi, należy ustawić domyślną nazwę grupy zasobów i nazwę klastra.
```
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
```
W poniższych przykładach nazwa grupy zasobów i usługi zostanie pominięta w poleceniach.

### <a name="tail-log-for-app-with-single-instance"></a>Dziennik końcowy aplikacji z pojedynczym wystąpieniem
Jeśli aplikacja o nazwie auth-Service ma tylko jedno wystąpienie, można wyświetlić dziennik wystąpienia aplikacji za pomocą następującego polecenia:
```
az spring-cloud app logs -n auth-service
```
Spowoduje to zwrócenie dzienników:
```
...
2020-01-15 01:54:40.481  INFO [auth-service,,,] 1 --- [main] o.apache.catalina.core.StandardService  : Starting service [Tomcat]
2020-01-15 01:54:40.482  INFO [auth-service,,,] 1 --- [main] org.apache.catalina.core.StandardEngine  : Starting Servlet engine: [Apache Tomcat/9.0.22]
2020-01-15 01:54:40.760  INFO [auth-service,,,] 1 --- [main] o.a.c.c.C.[Tomcat].[localhost].[/uaa]  : Initializing Spring embedded WebApplicationContext
2020-01-15 01:54:40.760  INFO [auth-service,,,] 1 --- [main] o.s.web.context.ContextLoader  : Root WebApplicationContext: initialization completed in 7203 ms

...
```

### <a name="tail-log-for-app-with-multiple-instances"></a>Dziennik końcowy aplikacji z wieloma wystąpieniami
Jeśli istnieje wiele wystąpień dla aplikacji o nazwie `auth-service` , można wyświetlić dziennik wystąpienia przy użyciu `-i/--instance` opcji. 

Najpierw można pobrać nazwy wystąpienia aplikacji za pomocą następującego polecenia.

```
az spring-cloud app show -n auth-service --query properties.activeDeployment.properties.instances -o table
```
Z wynikami:

```
Name                                         Status    DiscoveryStatus
-------------------------------------------  --------  -----------------
auth-service-default-12-75cc4577fc-pw7hb  Running   UP
auth-service-default-12-75cc4577fc-8nt4m  Running   UP
auth-service-default-12-75cc4577fc-n25mh  Running   UP
``` 
Następnie można przesłać strumieniowo dzienniki wystąpienia aplikacji z `-i/--instance` opcją opcji:

```
az spring-cloud app logs -n auth-service -i auth-service-default-12-75cc4577fc-pw7hb
```

Możesz również uzyskać szczegółowe informacje o wystąpieniach aplikacji z Azure Portal.  Po wybraniu **aplikacji** w okienku nawigacji po lewej stronie usługi w chmurze Azure wiosny wybierz pozycję **wystąpienia aplikacji**.

### <a name="continuously-stream-new-logs"></a>Ciągle Przesyłaj strumieniowo nowe dzienniki
Domyślnie program `az spring-cloud ap log tail` drukuje tylko istniejące dzienniki przesyłane strumieniowo do konsoli aplikacji, a następnie kończy pracę. Jeśli chcesz przesyłać strumieniowo nowe dzienniki, Dodaj polecenie-f (--Obserwuj):  

```
az spring-cloud app logs -n auth-service -f
``` 
Aby sprawdzić wszystkie obsługiwane opcje rejestrowania:
``` 
az spring-cloud app logs -h 
```

## <a name="next-steps"></a>Następne kroki
* [Szybki Start: monitorowanie aplikacji w chmurze platformy Azure z użyciem dzienników, metryk i śledzenia](spring-cloud-quickstart-logs-metrics-tracing.md)
* [Analizowanie dzienników i metryk przy użyciu ustawień diagnostycznych](./diagnostic-services.md)

