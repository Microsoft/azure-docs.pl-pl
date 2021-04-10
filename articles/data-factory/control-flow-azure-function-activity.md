---
title: Działanie funkcji platformy Azure w Azure Data Factory
description: Dowiedz się, jak używać działania funkcji platformy Azure do uruchamiania funkcji platformy Azure w potoku Data Factory
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/09/2019
ms.openlocfilehash: 13f00907737a99bc5dcd8c21d660ce83aa681908
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104783799"
---
# <a name="azure-function-activity-in-azure-data-factory"></a>Działanie funkcji platformy Azure w Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]
Działanie funkcji platformy Azure umożliwia uruchamianie [Azure Functions](../azure-functions/functions-overview.md) w potoku Data Factory. Aby uruchomić funkcję platformy Azure, należy utworzyć połączenie połączonej usługi i działanie określające funkcję platformy Azure, która ma zostać wykonana.

Aby uzyskać 8-minutowy wprowadzenie i demonstrację tej funkcji, Obejrzyj następujące wideo:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Run-Azure-Functions-from-Azure-Data-Factory-pipelines/player]

## <a name="azure-function-linked-service"></a>Połączona usługa funkcji platformy Azure

Zwracany typ funkcji platformy Azure musi być prawidłowy `JObject` . (Należy pamiętać, że [JArray](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_Linq_JArray.htm) *nie* jest `JObject` .) Każdy typ zwracany inny niż błąd `JObject` i podnosi zawartość odpowiedzi na błędy użytkownika *nie jest prawidłowym JObject*.

| **Właściwość** | **Opis** | **Wymagane** |
| --- | --- | --- |
| typ   | Właściwość Type musi mieć wartość: **AzureFunction** | tak |
| adres URL aplikacji funkcji | Adres URL aplikacja funkcji platformy Azure. Format to `https://<accountname>.azurewebsites.net` . Ten adres URL jest wartością w sekcji **adresu URL** podczas wyświetlania aplikacja funkcji w Azure Portal  | tak |
| klucz funkcji | Klucz dostępu dla funkcji platformy Azure. Kliknij sekcję **Zarządzanie** odpowiednią funkcją i skopiuj **klucz funkcji** lub **klucz hosta**. Dowiedz się więcej tutaj: [Azure Functions wyzwalacze i powiązania HTTP](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) | tak |
|   |   |   |

## <a name="azure-function-activity"></a>Działanie funkcji platformy Azure

| **Właściwość**  | **Opis** | **Dozwolone wartości** | **Wymagane** |
| --- | --- | --- | --- |
| name  | Nazwa działania w potoku  | Ciąg | tak |
| typ  | Typ działania to "AzureFunctionActivity" | Ciąg | tak |
| połączona usługa | Połączona usługa funkcji platformy Azure dla odpowiednich aplikacja funkcji platformy Azure  | Odwołanie do połączonej usługi | tak |
| Nazwa funkcji  | Nazwa funkcji w aplikacja funkcji platformy Azure, którą wywołuje to działanie | Ciąg | tak |
| method  | Metoda interfejsu API REST dla wywołania funkcji | Obsługiwane typy ciągów: "GET", "POST", "PUT"   | tak |
| header  | Nagłówki wysyłane do żądania. Na przykład, aby ustawić język i typ dla żądania: "heads": {"Accept-Language": "en-us", "Content-Type": "Application/JSON"} | Ciąg (lub wyrażenie z typem ResultType ciągu) | Nie |
| body  | treść, która jest wysyłana wraz z żądaniem do metody interfejsu API funkcji  | Ciąg (lub wyrażenie z typem ResultType String) lub Object.   | Wymagane dla metod PUT/POST |
|   |   |   | |

Zobacz schemat ładunku żądania w sekcji [schematu ładunku żądania](control-flow-web-activity.md#request-payload-schema) .

## <a name="routing-and-queries"></a>Routing i zapytania

Działanie funkcji platformy Azure obsługuje **Routing**. Jeśli na przykład funkcja platformy Azure ma punkt końcowy  `https://functionAPP.azurewebsites.net/api/<functionName>/<value>?code=<secret>` , `functionName` do użycia w działaniu funkcji platformy Azure znajduje się wartość `<functionName>/<value>` . Tę funkcję można Sparametryzuj, aby zapewnić odpowiednie `functionName` środowisko uruchomieniowe.

Działanie funkcji platformy Azure obsługuje również **zapytania**. Zapytanie musi zostać dołączone jako część elementu `functionName` . Na przykład, gdy nazwa funkcji jest `HttpTriggerCSharp` i zapytanie, które chcesz dołączyć `name=hello` , można utworzyć `functionName` w działaniu funkcji platformy Azure jako `HttpTriggerCSharp?name=hello` . Ta funkcja może być sparametryzowana, aby można było określić wartość w czasie wykonywania.

## <a name="timeout-and-long-running-functions"></a>Funkcje limitu czasu i długotrwałego działania

Azure Functions limit czasu po 230 sekund niezależnie od `functionTimeout` ustawień skonfigurowanych w ustawieniach. Więcej informacji znajduje się w [tym artykule](../azure-functions/functions-versions.md#timeout). Aby obejść ten problem, postępuj zgodnie ze wzorcem asynchronicznym lub użyj Durable Functions. Korzyść Durable Functions polega na tym, że oferują własne mechanizmy śledzenia stanu, więc nie trzeba implementować własnych.

Dowiedz się więcej na temat Durable Functions w [tym artykule](../azure-functions/durable/durable-functions-overview.md). Można skonfigurować działanie funkcji platformy Azure w celu wywołania funkcji trwałej, która zwróci odpowiedź z innym identyfikatorem URI, na przykład w [tym przykładzie](../azure-functions/durable/durable-functions-http-features.md#http-api-url-discovery). Ponieważ `statusQueryGetUri` zwraca stan HTTP 202, gdy funkcja jest uruchomiona, można sondować stan funkcji przy użyciu działania sieci Web. Wystarczy skonfigurować działanie sieci Web z `url` polem ustawionym na `@activity('<AzureFunctionActivityName>').output.statusQueryGetUri` . Po zakończeniu trwałej funkcji dane wyjściowe funkcji będą dane wyjściowe działania sieci Web.


## <a name="sample"></a>Przykład

Możesz znaleźć przykład Data Factory, który korzysta z funkcji platformy Azure w celu wyodrębnienia zawartości pliku TAR w [tym miejscu](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV2/UntarAzureFilesWithAzureFunction).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o działaniach w Data Factory w [potokach i działaniach w programie Azure Data Factory](concepts-pipelines-activities.md).
