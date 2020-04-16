---
title: Działanie funkcji platformy Azure w fabryce danych platformy Azure
description: Dowiedz się, jak używać działania funkcji platformy Azure do uruchamiania funkcji platformy Azure w potoku usługi Data Factory
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.openlocfilehash: ee2e59e794cf34a8fd5043a56867a81c2537f1ae
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415304"
---
# <a name="azure-function-activity-in-azure-data-factory"></a>Działanie funkcji platformy Azure w fabryce danych platformy Azure
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]
Działanie funkcji platformy Azure umożliwia [uruchamianie funkcji platformy Azure](../azure-functions/functions-overview.md) w potoku fabryki danych. Aby uruchomić funkcję platformy Azure, należy utworzyć połączenie połączonej usługi i działanie, które określa funkcję platformy Azure, którą zamierzasz wykonać.

Aby zapoznać się z ośmiominutowym wprowadzeniem i prezentacją tej funkcji, obejrzyj następujący film:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Run-Azure-Functions-from-Azure-Data-Factory-pipelines/player]

## <a name="azure-function-linked-service"></a>Usługa połączona z funkcją platformy Azure

Typ zwracany funkcji platformy Azure musi `JObject`być prawidłowy. (Należy pamiętać, że [JArray](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_Linq_JArray.htm) *nie* jest `JObject`.) Każdy typ zwracany inny niż `JObject` kończy się niepowodzeniem i wywołuje błąd użytkownika Zawartość odpowiedzi nie jest *prawidłowym JObject*.

| **Właściwość** | **Opis** | **Wymagane** |
| --- | --- | --- |
| type   | Właściwość typu musi być ustawiona na: **AzureFunction** | tak |
| url aplikacji funkcji | Adres URL aplikacji funkcji platformy Azure. Format `https://<accountname>.azurewebsites.net`jest . Ten adres URL jest wartością w sekcji **adres URL** podczas wyświetlania aplikacji funkcji w witrynie Azure portal  | tak |
| klawisz funkcyjny | Klucz dostępu dla funkcji platformy Azure. Kliknij sekcję **Zarządzaj** dla danej funkcji i skopiuj **klucz funkcyjny** lub **klucz hosta**. Dowiedz się więcej tutaj: [Wyzwalacze i powiązania HTTP usługi Azure Functions HTTP](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) | tak |
|   |   |   |

## <a name="azure-function-activity"></a>Działanie funkcji platformy Azure

| **Właściwość**  | **Opis** | **Dozwolone wartości** | **Wymagane** |
| --- | --- | --- | --- |
| name  | Nazwa działania w potoku  | Ciąg | tak |
| type  | Typ działania to "AzureFunctionActivity" | Ciąg | tak |
| usługa połączona | Usługa połączona z funkcją platformy Azure dla odpowiedniej aplikacji funkcji platformy Azure  | Odwołanie do usługi połączonej | tak |
| nazwa funkcji  | Nazwa funkcji w aplikacji funkcji platformy Azure, która wywołuje to działanie | Ciąg | tak |
| method  | REST METODA INTERFEJSU API dla wywołania funkcji | Obsługiwane typy: "GET", "POST", "PUT"   | tak |
| nagłówek  | Nagłówki, które są wysyłane do żądania. Na przykład, aby ustawić język i wpisać na żądanie: "headers": { "Accept-Language": "en-us", "Content-Type": "application/json" } | Ciąg (lub wyrażenie z wynikiemTylią ciągu) | Nie |
| body  | który jest wysyłany wraz z żądaniem do metody api funkcji  | Ciąg (lub wyrażenie z wynikiemTylią ciągu) lub obiekt.   | Wymagane dla metod PUT/POST |
|   |   |   | |

Zobacz schemat ładunku żądania w sekcji Schemat [ładunku żądania.](control-flow-web-activity.md#request-payload-schema)

## <a name="routing-and-queries"></a>Routing i zapytania

Działanie funkcji platformy Azure obsługuje **routing**. Na przykład jeśli funkcja platformy Azure `https://functionAPP.azurewebsites.net/api/<functionName>/<value>?code=<secret>`ma `functionName` punkt końcowy, a następnie `<functionName>/<value>`do użycia w usłudze Azure function activity jest . Można sparametryzować tę funkcję, aby zapewnić żądane `functionName` w czasie wykonywania.

Działanie funkcji platformy Azure obsługuje również **kwerendy**. Kwerenda musi zostać uwzględniona jako `functionName`część pliku . Na przykład, gdy nazwa `HttpTriggerCSharp` funkcji jest i kwerendy, które chcesz dołączyć jest `name=hello`, a następnie można skonstruować `functionName` w usłudze Azure function activity as `HttpTriggerCSharp?name=hello`. Ta funkcja może być sparametryzowana, dzięki czemu wartość można określić w czasie wykonywania.

## <a name="timeout-and-long-running-functions"></a>Limit czasu i funkcje długotrwałego działania

Upłynie czas po upływie 230 sekund, niezależnie od `functionTimeout` ustawienia skonfigurowanego w ustawieniach. Aby uzyskać więcej informacji, zobacz [ten artykuł](../azure-functions/functions-versions.md#timeout). Aby obejść to zachowanie, wykonaj wzorzec asynchronii lub użyj funkcji trwałych. Zaletą funkcji trwałych jest to, że oferują one swój własny mechanizm śledzenia stanu, więc nie trzeba implementować własne.

Dowiedz się więcej o funkcjach trwałych w [tym artykule](../azure-functions/durable/durable-functions-overview.md). Można skonfigurować działanie funkcji platformy Azure, aby wywołać funkcję trwałą, która zwróci odpowiedź z innym identyfikatorem URI, na [przykład.](../azure-functions/durable/durable-functions-http-features.md#http-api-url-discovery) Ponieważ `statusQueryGetUri` zwraca stan HTTP 202, gdy funkcja jest uruchomiona, można sondować stan funkcji za pomocą aktywności w sieci Web. Wystarczy skonfigurować aktywność `url` w sieci `@activity('<AzureFunctionActivityName>').output.statusQueryGetUri`Web z polem ustawionym na . Po zakończeniu funkcji trwałe, dane wyjściowe funkcji będzie dane wyjściowe działania sieci Web.


## <a name="sample"></a>Przykład

W tym miejscu można znaleźć przykład fabryki danych, która używa funkcji platformy Azure do wyodrębniania zawartości [pliku](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV2/UntarAzureFilesWithAzureFunction)tar.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o działaniach w obszarze Fabryka danych w [potokach i działaniach w usłudze Azure Data Factory](concepts-pipelines-activities.md).
