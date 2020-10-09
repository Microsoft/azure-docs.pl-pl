---
title: Konfigurowanie i odczytywanie dzienników z wyzwalaczem Azure Functions dla Cosmos DB
description: Dowiedz się, jak uwidocznić dzienniki w potoku rejestrowania Azure Functions podczas korzystania z wyzwalacza Azure Functions dla Cosmos DB
author: ealsur
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/17/2019
ms.author: maquaran
ms.openlocfilehash: 31c9f9d94acab7322cf11d084e4010f2defd6f3c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85261702"
---
# <a name="how-to-configure-and-read-the-logs-when-using-azure-functions-trigger-for-cosmos-db"></a>Sposób konfigurowania i odczytywania dzienników przy użyciu wyzwalacza Azure Functions dla Cosmos DB

W tym artykule opisano, jak można skonfigurować środowisko Azure Functions, aby wysłać wyzwalacz Azure Functions dla dzienników Cosmos DB do skonfigurowanego [rozwiązania do monitorowania](../azure-functions/functions-monitoring.md).

## <a name="included-logs"></a>Uwzględnione dzienniki

Wyzwalacz Azure Functions dla Cosmos DB używa wewnętrznie [biblioteki procesora źródła zmian](./change-feed-processor.md) , a Biblioteka generuje zestaw dzienników kondycji, których można użyć do monitorowania wewnętrznych operacji w celu [rozwiązywania problemów](./troubleshoot-changefeed-functions.md).

Dzienniki kondycji opisują sposób, w jaki wyzwalacz Azure Functions dla Cosmos DB zachowuje się podczas próby wykonania operacji w scenariuszach równoważenia obciążenia lub inicjalizacji.

## <a name="enabling-logging"></a>Włączanie rejestrowania

Aby włączyć rejestrowanie w przypadku korzystania z wyzwalacza Azure Functions dla Cosmos DB, Znajdź `host.json` plik w Azure Functions projekcie lub aplikacji Azure Functions i [Skonfiguruj poziom wymaganych rejestracji](../azure-functions/functions-monitoring.md#log-configuration-in-hostjson). Musisz włączyć ślady,  `Host.Triggers.CosmosDB` tak jak pokazano w następującym przykładzie:

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

## <a name="query-the-logs"></a>Wysyłanie zapytań do dzienników

Uruchom następujące zapytanie, aby wykonać zapytanie dotyczące dzienników generowanych przez wyzwalacz Azure Functions dla Cosmos DB na [platformie Azure Application Insights "Analiza](../azure-monitor/app/analytics.md):

```sql
traces
| where customDimensions.Category == "Host.Triggers.CosmosDB"
```

## <a name="next-steps"></a>Następne kroki

* [Włącz monitorowanie](../azure-functions/functions-monitoring.md) w aplikacjach Azure Functions.
* Dowiedz się [, jak diagnozować i rozwiązywać typowe problemy](./troubleshoot-changefeed-functions.md) podczas korzystania z wyzwalacza Azure Functions dla Cosmos DB.
