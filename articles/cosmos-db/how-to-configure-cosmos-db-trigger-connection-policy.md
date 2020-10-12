---
title: Wyzwalacz Azure Functions dla zasad połączenia Cosmos DB
description: Dowiedz się, jak skonfigurować zasady połączenia używane przez wyzwalacz Azure Functions dla Cosmos DB
author: ealsur
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/17/2019
ms.author: maquaran
ms.openlocfilehash: df30a0ddca58e6d7bd74184fa7287df6818cfc37
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84117145"
---
# <a name="how-to-configure-the-connection-policy-used-by-azure-functions-trigger-for-cosmos-db"></a>Jak skonfigurować zasady połączenia używane przez wyzwalacz Azure Functions dla Cosmos DB

W tym artykule opisano, jak można skonfigurować zasady połączenia przy użyciu wyzwalacza Azure Functions, aby Cosmos DB do nawiązywania połączenia z kontem usługi Azure Cosmos.

## <a name="why-is-the-connection-policy-important"></a>Dlaczego zasady połączeń są ważne?

Istnieją dwa tryby połączeń — tryb bezpośredni i tryb bramy. Aby dowiedzieć się więcej na temat tych trybów połączeń, zobacz artykuł dotyczący [wskazówek dotyczących wydajności](./performance-tips.md#networking) . Domyślnie **brama** jest używana do ustanowienia wszystkich połączeń w wyzwalaczu Azure Functions dla Cosmos DB. Jednak może nie być najlepszą opcją dla scenariuszy opartych na wydajności.

## <a name="changing-the-connection-mode-and-protocol"></a>Zmiana trybu połączenia i protokołu

Dostępne są dwa ustawienia konfiguracji kluczy umożliwiające skonfigurowanie zasad połączenia klienta — **tryb połączenia** i **Protokół połączenia**. Można zmienić domyślny tryb połączenia i protokół używany przez wyzwalacz Azure Functions dla Cosmos DB i wszystkich [powiązań Azure Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2-output.md)). Aby zmienić ustawienia domyślne, należy zlokalizować `host.json` plik w projekcie Azure Functions lub w aplikacji Azure Functions i dodać następujące [dodatkowe ustawienie](../azure-functions/functions-bindings-cosmosdb-v2-output.md#hostjson-settings):

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
* [Porady dotyczące wydajności Azure Cosmos DB](./performance-tips.md)
* [Przykłady kodu](https://github.com/ealsur/serverless-recipes/tree/master/connectionmode)
