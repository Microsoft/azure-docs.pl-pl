---
title: Azure Functions wyzwalacza Cosmos DB zaawansowanej konfiguracji
description: Informacje dotyczące konfigurowania rejestrowania i zasad połączeń używanych przez wyzwalacz Azure Functions dla Cosmos DB
author: ealsur
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/19/2020
ms.author: maquaran
ms.openlocfilehash: c47d18726d9581b1b03aa2e676a71d6ca1bc1b7d
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93086470"
---
# <a name="how-to-configure-logging-and-connectivity-with-the-azure-functions-trigger-for-cosmos-db"></a>Jak skonfigurować rejestrowanie i łączność z wyzwalaczem Azure Functions dla Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

W tym artykule opisano zaawansowane opcje konfiguracji, które można ustawić podczas korzystania z wyzwalacza Azure Functions dla Cosmos DB.

## <a name="enabling-trigger-specific-logs"></a>Włączanie wyzwalacza określonych dzienników

Wyzwalacz Azure Functions dla Cosmos DB używa wewnętrznie [biblioteki procesora źródła zmian](./change-feed-processor.md) , a Biblioteka generuje zestaw dzienników kondycji, których można użyć do monitorowania wewnętrznych operacji w celu [rozwiązywania problemów](./troubleshoot-changefeed-functions.md).

Dzienniki kondycji opisują sposób, w jaki wyzwalacz Azure Functions dla Cosmos DB zachowuje się podczas próby wykonania operacji w scenariuszach równoważenia obciążenia lub inicjalizacji.

### <a name="enabling-logging"></a>Włączanie rejestrowania

Aby włączyć rejestrowanie w przypadku korzystania z wyzwalacza Azure Functions dla Cosmos DB, Znajdź `host.json` plik w Azure Functions projekcie lub aplikacji Azure Functions i [Skonfiguruj poziom wymaganych rejestracji](../azure-functions/functions-monitoring.md#log-levels-and-categories). Włącz ślady,  `Host.Triggers.CosmosDB` tak jak pokazano w następującym przykładzie:

```js
{
  "version": "2.0",
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "Host.Triggers.CosmosDB": "Trace"
    }
  }
}
```

Po wdrożeniu funkcji platformy Azure przy użyciu zaktualizowanej konfiguracji zostanie wyświetlony wyzwalacz Azure Functions dla Cosmos DB dzienników w ramach śladów. Możesz wyświetlić dzienniki w skonfigurowanym dostawcy rejestrowania w *kategorii* `Host.Triggers.CosmosDB` .

### <a name="query-the-logs"></a>Wysyłanie zapytań do dzienników

Uruchom następujące zapytanie, aby wykonać zapytanie dotyczące dzienników generowanych przez wyzwalacz Azure Functions dla Cosmos DB na [platformie Azure Application Insights "Analiza](../azure-monitor/log-query/log-query-overview.md):

```sql
traces
| where customDimensions.Category == "Host.Triggers.CosmosDB"
```

## <a name="configuring-the-connection-policy"></a>Konfigurowanie zasad połączenia

Istnieją dwa tryby połączeń — tryb bezpośredni i tryb bramy. Aby dowiedzieć się więcej o tych trybach połączeń, zobacz artykuł dotyczący [trybów połączeń](sql-sdk-connection-modes.md) . Domyślnie **brama** jest używana do ustanowienia wszystkich połączeń w wyzwalaczu Azure Functions dla Cosmos DB. Jednak może nie być najlepszą opcją dla scenariuszy opartych na wydajności.

### <a name="changing-the-connection-mode-and-protocol"></a>Zmiana trybu połączenia i protokołu

Dostępne są dwa ustawienia konfiguracji kluczy umożliwiające skonfigurowanie zasad połączenia klienta — **tryb połączenia** i **Protokół połączenia** . Można zmienić domyślny tryb połączenia i protokół używany przez wyzwalacz Azure Functions dla Cosmos DB i wszystkich [powiązań Azure Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2-output.md)). Aby zmienić ustawienia domyślne, należy zlokalizować `host.json` plik w projekcie Azure Functions lub w aplikacji Azure Functions i dodać następujące [dodatkowe ustawienie](../azure-functions/functions-bindings-cosmosdb-v2-output.md#hostjson-settings):

```js
{
  "cosmosDB": {
    "connectionMode": "Direct",
    "protocol": "Tcp"
  }
}
```

Gdzie `connectionMode` musi mieć żądany tryb połączenia (Direct lub Gateway) i `protocol` wymagany protokół połączeń (TCP lub https). 

Jeśli projekt Azure Functions działa w środowisku uruchomieniowym Azure Functions V1, konfiguracja ma niewielką różnicę nazw, należy użyć `documentDB` zamiast `cosmosDB` :

```js
{
  "documentDB": {
    "connectionMode": "Direct",
    "protocol": "Tcp"
  }
}
```

> [!NOTE]
> Podczas pracy z planem hostingu Azure Functions, każde wystąpienie ma limit liczby połączeń gniazda, które może obsłużyć. Podczas pracy z trybem Direct/TCP przez zaprojektowanie większej liczby połączeń są tworzone i można napotkać [Limit planu zużycia](../azure-functions/manage-connections.md#connection-limit), w takim przypadku można użyć trybu bramy lub uruchomić Azure Functions w [trybie App Service](../azure-functions/functions-scale.md#app-service-plan).

## <a name="next-steps"></a>Następne kroki

* [Limity połączeń w Azure Functions](../azure-functions/manage-connections.md#connection-limit)
* [Włącz monitorowanie](../azure-functions/functions-monitoring.md) w aplikacjach Azure Functions.
* Dowiedz się [, jak diagnozować i rozwiązywać typowe problemy](./troubleshoot-changefeed-functions.md) podczas korzystania z wyzwalacza Azure Functions dla Cosmos DB.