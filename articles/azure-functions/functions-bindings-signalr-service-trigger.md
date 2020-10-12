---
title: Powiązanie wyzwalacza usługi Azure Functions sygnalizującego
description: Dowiedz się, jak wysyłać komunikaty usługi sygnalizujące z Azure Functions.
author: chenyl
ms.topic: reference
ms.custom: devx-track-csharp
ms.date: 05/11/2020
ms.author: chenyl
ms.openlocfilehash: e2651afbcdc3bae71bb531aa0e821f83264c295d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88212591"
---
# <a name="signalr-service-trigger-binding-for-azure-functions"></a>Powiązanie wyzwalacza usługi sygnalizującej dla Azure Functions

Użyj powiązania wyzwalacza *sygnalizującego* , aby odpowiedzieć na komunikaty wysyłane z usługi Azure Signal Service. Gdy funkcja jest wyzwalana, komunikaty przesyłane do funkcji są analizowane jako obiekt JSON.

Aby uzyskać informacje na temat konfiguracji i szczegółów konfiguracji, zobacz [Omówienie](functions-bindings-signalr-service.md).

## <a name="example"></a>Przykład

Poniższy przykład pokazuje funkcję, która odbiera komunikat przy użyciu powiązania wyzwalacza i rejestruje komunikat.

# <a name="c"></a>[C#](#tab/csharp)

Powiązanie wyzwalacza usługi sygnalizującej dla języka C# ma dwa modele programowania. Model oparty na klasie i tradycyjny model. Model oparty na klasie może zapewnić spójne środowisko programistyczne po stronie serwera. I tradycyjny model zapewnia większą elastyczność i przypomina inne powiązania funkcji.

### <a name="with-class-based-model"></a>Z modelem opartym na klasie

Aby uzyskać szczegółowe informacje, zobacz [model oparty na klasie](../azure-signalr/signalr-concept-serverless-development-config.md#class-based-model) .

```cs
public class SignalRTestHub : ServerlessHub
{
    [FunctionName("SignalRTest")]
    public async Task SendMessage([SignalRTrigger]InvocationContext invocationContext, string message, ILogger logger)
    {
        logger.LogInformation($"Receive {message} from {invocationContext.ConnectionId}.");
    }
}
```

### <a name="with-traditional-model"></a>Z tradycyjnym modelem

Tradycyjny model przestrzega konwencji platformy Azure opracowanej przez język C#. Jeśli nie masz doświadczenia z nim, możesz poznać [dokumenty](./functions-dotnet-class-library.md).

```cs
[FunctionName("SignalRTest")]
public static async Task Run([SignalRTrigger("SignalRTest", "messages", "SendMessage", parameterNames: new string[] {"message"})]InvocationContext invocationContext, string message, ILogger logger)
{
    logger.LogInformation($"Receive {message} from {invocationContext.ConnectionId}.");
}
```

#### <a name="use-attribute-signalrparameter-to-simplify-parameternames"></a>Użyj atrybutu `[SignalRParameter]` , aby uprościć `ParameterNames`

Ponieważ jest to nieskomplikowany sposób `ParameterNames` , `SignalRParameter` zapewnia się osiągnięcie tego samego celu.

```cs
[FunctionName("SignalRTest")]
public static async Task Run([SignalRTrigger("SignalRTest", "messages", "SendMessage")]InvocationContext invocationContext, [SignalRParameter]string message, ILogger logger)
{
    logger.LogInformation($"Receive {message} from {invocationContext.ConnectionId}.");
}
```

# <a name="c-script"></a>[Skrypt C#](#tab/csharp-script)

Poniżej przedstawiono powiązania danych w *function.js* pliku:

Przykład function.js:

```json
{
    "type": "signalRTrigger",
    "name": "invocation",
    "hubName": "SignalRTest",
    "category": "messages",
    "event": "SendMessage",
    "parameterNames": [
        "message"
    ],
    "direction": "in"
}
```

Oto kod skryptu w języku C#:

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using System;
using Microsoft.Azure.WebJobs.Extensions.SignalRService;
using Microsoft.Extensions.Logging;

public static void Run(InvocationContext invocation, string message, ILogger logger)
{
    logger.LogInformation($"Receive {message} from {invocationContext.ConnectionId}.");
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Poniżej przedstawiono powiązania danych w *function.js* pliku:

Przykład function.js:

```json
{
    "type": "signalRTrigger",
    "name": "invocation",
    "hubName": "SignalRTest",
    "category": "messages",
    "event": "SendMessage",
    "parameterNames": [
        "message"
    ],
    "direction": "in"
}
```

Oto kod JavaScript:

```javascript
module.exports = function (context, invocation) {
    context.log(`Receive ${context.bindingData.message} from ${invocation.ConnectionId}.`)
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

Poniżej przedstawiono powiązania danych w *function.js* pliku:

Przykład function.js:

```json
{
    "type": "signalRTrigger",
    "name": "invocation",
    "hubName": "SignalRTest",
    "category": "messages",
    "event": "SendMessage",
    "parameterNames": [
        "message"
    ],
    "direction": "in"
}
```

Oto kod języka Python:

```python
import logging
import json
import azure.functions as func

def main(invocation) -> None:
    invocation_json = json.loads(invocation)
    logging.info("Receive {0} from {1}".format(invocation_json['Arguments'][0], invocation_json['ConnectionId']))
```

---

## <a name="configuration"></a>Konfiguracja

### <a name="signalrtrigger"></a>SignalRTrigger

W poniższej tabeli objaśniono właściwości konfiguracji powiązań, które zostały ustawione w *function.js* pliku i `SignalRTrigger` atrybutu.

|function.jswłaściwości | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**Wprowadź**| nie dotyczy | Musi być ustawiony na `SignalRTrigger` .|
|**wskazywa**| nie dotyczy | Musi być ustawiony na `in` .|
|**Nazwij**| nie dotyczy | Nazwa zmiennej używana w kodzie funkcji dla obiektu kontekstu wywołania wyzwalacza. |
|**hubName**|**HubName**| Ta wartość musi być ustawiona na nazwę centrum sygnalizującego, aby funkcja została wyzwolona.|
|**kategorii**|**Kategoria**| Ta wartość musi być ustawiona jako kategoria komunikatów dla funkcji, która ma być wyzwalana. Kategoria może być jedną z następujących wartości: <ul><li>**połączenia**: w tym zdarzenia *połączone* i *rozłączone*</li><li>**komunikaty**: łącznie ze wszystkimi innymi zdarzeniami, z wyjątkiem kategorii *połączenia*</li></ul> |
|**event**|**Wydarzen**| Ta wartość musi być ustawiona jako zdarzenie komunikatów dla funkcji, która ma być wyzwalana. W przypadku kategorii *messages* , Event to *obiekt docelowy* w [komunikacie wywołania](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding) wysyłanym przez klientów. W kategorii *połączenia* jest używana tylko *połączona* i *rozłączona* . |
|**parameterNames**|**ParameterNames**| Obowiązkowe Lista nazw, które wiążą się z parametrami. |
|**connectionStringSetting**|**ConnectionStringSetting**| Nazwa ustawienia aplikacji zawierającego parametry połączenia usługi sygnalizującego (wartość domyślna to "AzureSignalRConnectionString"). |

## <a name="payload"></a>Ładunku

Typ danych wejściowych wyzwalacza jest zadeklarowany jako `InvocationContext` lub jako typ niestandardowy. W przypadku wybrania opcji `InvocationContext` Uzyskaj pełny dostęp do zawartości żądania. W przypadku typu niestandardowego środowisko uruchomieniowe próbuje przeanalizować treści żądania JSON w celu ustawienia właściwości obiektu.

### <a name="invocationcontext"></a>InvocationContext

InvocationContext zawiera całą zawartość wiadomości wysyłanej z usługi sygnalizującej.

|Właściwość w InvocationContext | Opis|
|------------------------------|------------|
|Argumenty| Dostępne dla kategorii *messages* . Zawiera *argumenty* w [komunikacie wywołania](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding)|
|Błąd| Dostępne dla zdarzenia *odłączonego* . Może być pusta, jeśli połączenie zostało zamknięte bez błędu lub zawiera komunikaty o błędach.|
|Koncentrator| Nazwa centrum, do którego należy wiadomość.|
|Kategoria| Kategoria komunikatu.|
|Wydarzenie| Zdarzenie.|
|ConnectionId| Identyfikator połączenia klienta, który wysyła komunikat.|
|UserId| Tożsamość użytkownika klienta wysyłającego komunikat.|
|Nagłówki| Nagłówki żądania.|
|Zapytanie| Zapytanie o żądanie, gdy klienci łączą się z usługą.|
|Oświadczenia| Oświadczenia klienta.|

## <a name="using-parameternames"></a>Korzystanie z akcji `ParameterNames`

Właściwość `ParameterNames` w `SignalRTrigger` umożliwia Powiązywanie argumentów komunikatów wywołania do parametrów funkcji. Zapewnia to wygodniejszy sposób uzyskiwania dostępu do argumentów `InvocationContext` .

Załóżmy, że masz klienta sygnału języka JavaScript próbującego wywołać metodę `broadcast` w usłudze Azure Function z dwoma argumentami.

```javascript
await connection.invoke("broadcast", message1, message2);
```

Możesz uzyskać dostęp do tych dwóch argumentów z parametru, a także przypisać typ parametru do nich za pomocą `ParameterNames` .

### <a name="remarks"></a>Uwagi

Dla powiązania parametru, kolejność. Jeśli używasz `ParameterNames` , kolejność w programie jest `ParameterNames` zgodna z kolejnością argumentów wywoływanych w kliencie. Jeśli używasz atrybutu `[SignalRParameter]` w języku C#, kolejność argumentów w metodach funkcji platformy Azure odpowiada kolejności argumentów w klientach.

`ParameterNames``[SignalRParameter]` **nie można** jednocześnie używać atrybutu i w tym samym czasie lub zostanie wyświetlony wyjątek.

## <a name="send-messages-to-signalr-service-trigger-binding"></a>Wyślij komunikaty do powiązania wyzwalacza usługi sygnalizującego

Funkcja platformy Azure generuje adres URL dla powiązania wyzwalacza usługi sygnalizującego i jest sformatowany w następujący sposób:

```http
https://<APP_NAME>.azurewebsites.net/runtime/webhooks/signalr?code=<API_KEY>
```

`API_KEY`Funkcja jest generowana przez funkcję platformy Azure. Możesz uzyskać `API_KEY` z Azure Portal, tak jak w przypadku korzystania z wyzwalacza usługi sygnalizacji.
:::image type="content" source="media/functions-bindings-signalr-service/signalr-keys.png" alt-text="Klucz interfejsu API":::

Należy ustawić ten adres URL w `UrlTemplate` usłudze w ustawieniach nadrzędnego usługi sygnalizującej.

## <a name="next-steps"></a>Następne kroki

* [Programowanie i konfigurowanie w usłudze Azure Functions za pomocą usługi Azure SignalR Service](../azure-signalr/signalr-concept-serverless-development-config.md)
* [Przykład powiązania wyzwalacza usługi sygnalizującego](https://github.com/Azure/azure-functions-signalrservice-extension/tree/dev/samples/bidirectional-chat)
