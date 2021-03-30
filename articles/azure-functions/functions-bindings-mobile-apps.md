---
title: Mobile Apps powiązania Azure Functions
description: Dowiedz się, jak używać powiązań usługi Azure Mobile Apps w programie Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.custom: devx-track-csharp
ms.date: 11/21/2017
ms.author: cshoe
ms.openlocfilehash: 5ea58cc3d9f3615a74249b36f3f9ffb79caddda1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88212240"
---
# <a name="mobile-apps-bindings-for-azure-functions"></a>Mobile Apps powiązania Azure Functions 

> [!NOTE]
> Powiązania Mobile Apps platformy Azure są dostępne tylko dla Azure Functions 1. x. Nie są one obsługiwane w Azure Functions 2. x i wyższych.

W tym artykule opisano sposób pracy z powiązaniami [Mobile Apps platformy Azure](/previous-versions/azure/app-service-mobile/app-service-mobile-value-prop) w programie Azure Functions. Azure Functions obsługuje powiązania wejściowe i wyjściowe dla Mobile Apps.

Powiązania Mobile Apps umożliwiają odczytywanie i aktualizowanie tabel danych w aplikacjach mobilnych.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pakiety — funkcje 1. x

Powiązania Mobile Apps są dostępne w pakiecie NuGet [Microsoft. Azure. WebJobs. Extensions. MobileApps](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MobileApps) w wersji 1. x. Kod źródłowy pakietu znajduje się w repozytorium [Azure-WebJobs-SDK-Extensions — rozszerzenia](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.MobileApps/) GitHub.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="input"></a>Dane wejściowe

Powiązanie danych wejściowych Mobile Apps ładuje rekord z punktu końcowego tabeli mobilnej i przekazuje go do funkcji. W funkcjach C# i F # wszelkie zmiany wprowadzone do rekordu są automatycznie wysyłane z powrotem do tabeli, gdy funkcja zostanie zakończona pomyślnie.

## <a name="input---example"></a>Dane wejściowe — przykład

Zobacz przykład specyficzny dla języka:

* [Skrypt języka C# (csx)](#input---c-script-example)
* JavaScript

### <a name="input---c-script-example"></a>Przykładowy skrypt Input-C#

W poniższym przykładzie pokazano Mobile Apps powiązania wejściowego w *function.js* pliku i [funkcji skryptu języka C#](functions-reference-csharp.md) , która używa powiązania. Funkcja jest wyzwalana przez komunikat kolejki, który ma identyfikator rekordu. Funkcja odczytuje określony rekord i modyfikuje jego `Text` Właściwość.

Oto dane powiązania w *function.js* pliku:

```json
{
"bindings": [
    {
        "name": "myQueueItem",
        "queueName": "myqueue-items",
        "connection": "",
        "type": "queueTrigger",
        "direction": "in"
    },
    {
        "name": "record",
        "type": "mobileTable",
        "tableName": "MyTable",
        "id": "{queueTrigger}",
        "connection": "My_MobileApp_Url",
        "apiKey": "My_MobileApp_Key",
        "direction": "in"
    }
]
}
```
W sekcji [Konfiguracja](#input---configuration) objaśniono te właściwości.

Oto kod skryptu w języku C#:

```cs
#r "Newtonsoft.Json"    
using Newtonsoft.Json.Linq;

public static void Run(string myQueueItem, JObject record)
{
    if (record != null)
    {
        record["Text"] = "This has changed.";
    }    
}
```

### <a name="input---javascript"></a>Dane wejściowe — JavaScript

W poniższym przykładzie pokazano Mobile Apps powiązania wejściowego w *function.js* pliku i [funkcji języka JavaScript](functions-reference-node.md) , która używa powiązania. Funkcja jest wyzwalana przez komunikat kolejki, który ma identyfikator rekordu. Funkcja odczytuje określony rekord i modyfikuje jego `Text` Właściwość.

Oto dane powiązania w *function.js* pliku:

```json
{
"bindings": [
    {
        "name": "myQueueItem",
        "queueName": "myqueue-items",
        "connection": "",
        "type": "queueTrigger",
        "direction": "in"
    },
    {
        "name": "record",
        "type": "mobileTable",
        "tableName": "MyTable",
        "id": "{queueTrigger}",
        "connection": "My_MobileApp_Url",
        "apiKey": "My_MobileApp_Key",
        "direction": "in"
    }
]
}
```
W sekcji [Konfiguracja](#input---configuration) objaśniono te właściwości.

Oto kod JavaScript:

```javascript
module.exports = function (context, myQueueItem) {    
    context.log(context.bindings.record);
    context.done();
};
```

## <a name="input---attributes"></a>Atrybuty wejściowe

W [bibliotekach klas języka C#](functions-dotnet-class-library.md)Użyj atrybutu [Mobile](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) .

Aby uzyskać informacje na temat właściwości atrybutów, które można skonfigurować, zobacz [następującą sekcję konfiguracyjną](#input---configuration).

## <a name="input---configuration"></a>Dane wejściowe — konfiguracja

W poniższej tabeli objaśniono właściwości konfiguracji powiązań, które zostały ustawione w *function.js* pliku i `MobileTable` atrybutu.

|function.jswłaściwości | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
| **Wprowadź**| n/d | Musi być ustawiony na "mobilny"|
| **wskazywa**| n/d |Musi być ustawiona na wartość "in"|
| **Nazwij**| n/d | Nazwa parametru wejściowego w sygnaturze funkcji.|
|**tableName** |**TableName**|Nazwa tabeli danych aplikacji mobilnej|
| **id**| **#C1** | Identyfikator rekordu do pobrania. Może być statyczny lub oparty na wyzwalaczu, który wywołuje funkcję. Na przykład, jeśli używasz wyzwalacza kolejki dla funkcji, program `"id": "{queueTrigger}"` używa wartości ciągu komunikatu w kolejce jako identyfikatora rekordu do pobrania.|
|**połączenia**|**Połączenie**|Nazwa ustawienia aplikacji z adresem URL aplikacji mobilnej. Funkcja używa tego adresu URL do konstruowania wymaganych operacji REST dla aplikacji mobilnej. Utwórz ustawienie aplikacji w aplikacji funkcji, która zawiera adres URL aplikacji mobilnej, a następnie określ nazwę ustawienia aplikacji we `connection` właściwości w powiązaniu wejściowym. Adres URL wygląda następująco `http://<appname>.azurewebsites.net` .
|**apiKey**|**ApiKey**|Nazwa ustawienia aplikacji z kluczem interfejsu API aplikacji mobilnej. Podaj klucz interfejsu API w przypadku [zaimplementowania klucza interfejsu API w aplikacji mobilnej Node.js](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key)lub [ZAIMPLEMENTUJ klucz interfejsu API w aplikacji mobilnej platformy .NET](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key). Aby podać klucz, Utwórz ustawienie aplikacji w aplikacji funkcji, która zawiera klucz interfejsu API, a następnie Dodaj `apiKey` Właściwość w powiązaniu wejściowym przy użyciu nazwy ustawienia aplikacji. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Nie udostępniaj klucza interfejsu API klientom aplikacji mobilnej. Powinien być bezpiecznie dystrybuowany tylko do klientów po stronie usług, takich jak Azure Functions. Azure Functions przechowuje informacje o połączeniu i klucze interfejsu API jako ustawienia aplikacji, tak aby nie były one sprawdzane w repozytorium kontroli źródła. Zapewnia to ochronę poufnych informacji.

## <a name="input---usage"></a>Dane wejściowe — użycie

W funkcjach języka C#, gdy rekord z określonym IDENTYFIKATORem zostanie znaleziony, jest przenoszona do nazwanego parametru [JObject](https://www.newtonsoft.com/json/help/html/t_newtonsoft_json_linq_jobject.htm) . Gdy rekord nie zostanie znaleziony, wartość parametru to `null` . 

W funkcjach JavaScript rekord jest przesyłany do `context.bindings.<name>` obiektu. Gdy rekord nie zostanie znaleziony, wartość parametru to `null` . 

W funkcjach C# i F # wszelkie zmiany wprowadzone w rekordzie wejściowym (parametr wejściowy) są automatycznie wysyłane z powrotem do tabeli, gdy funkcja zostanie zakończona pomyślnie. Nie można zmodyfikować rekordu w funkcjach JavaScript.

## <a name="output"></a>Dane wyjściowe

Użyj powiązania danych wyjściowych Mobile Apps, aby zapisać nowy rekord w tabeli Mobile Apps.  

## <a name="output---example"></a>Dane wyjściowe — przykład

Zobacz przykład specyficzny dla języka:

* [C#](#output---c-example)
* [Skrypt języka C# (csx)](#output---c-script-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Dane wyjściowe — przykład w języku C#

Poniższy przykład pokazuje [funkcję języka C#](functions-dotnet-class-library.md) wyzwalaną przez komunikat w kolejce i tworzy rekord w tabeli aplikacji mobilnej.

```csharp
[FunctionName("MobileAppsOutput")]        
[return: MobileTable(ApiKeySetting = "MyMobileAppKey", TableName = "MyTable", MobileAppUriSetting = "MyMobileAppUri")]
public static object Run(
    [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
    TraceWriter log)
{
    return new { Text = $"I'm running in a C# function! {myQueueItem}" };
}
```

### <a name="output---c-script-example"></a>Wyjście — przykład skryptu w języku C#

Poniższy przykład przedstawia Mobile Apps powiązanie danych wyjściowych w *function.js* pliku i [funkcji skryptu języka C#](functions-reference-csharp.md) , która używa powiązania. Funkcja jest wyzwalana przez komunikat w kolejce i tworzy nowy rekord z ustaloną wartością `Text` właściwości.

Oto dane powiązania w *function.js* pliku:

```json
{
"bindings": [
    {
        "name": "myQueueItem",
        "queueName": "myqueue-items",
        "connection": "",
        "type": "queueTrigger",
        "direction": "in"
    },
    {
        "name": "record",
        "type": "mobileTable",
        "tableName": "MyTable",
        "connection": "My_MobileApp_Url",
        "apiKey": "My_MobileApp_Key",
        "direction": "out"
    }
]
}
```

W sekcji [Konfiguracja](#output---configuration) objaśniono te właściwości.

Oto kod skryptu w języku C#:

```cs
public static void Run(string myQueueItem, out object record)
{
    record = new {
        Text = $"I'm running in a C# function! {myQueueItem}"
    };
}
```

### <a name="output---javascript-example"></a>Dane wyjściowe — przykład JavaScript

Poniższy przykład przedstawia Mobile Apps powiązanie danych wyjściowych w *function.js* pliku i [funkcji języka JavaScript](functions-reference-node.md) , która używa powiązania. Funkcja jest wyzwalana przez komunikat w kolejce i tworzy nowy rekord z ustaloną wartością `Text` właściwości.

Oto dane powiązania w *function.js* pliku:

```json
{
"bindings": [
    {
        "name": "myQueueItem",
        "queueName": "myqueue-items",
        "connection": "",
        "type": "queueTrigger",
        "direction": "in"
    },
    {
        "name": "record",
        "type": "mobileTable",
        "tableName": "MyTable",
        "connection": "My_MobileApp_Url",
        "apiKey": "My_MobileApp_Key",
        "direction": "out"
    }
],
"disabled": false
}
```

W sekcji [Konfiguracja](#output---configuration) objaśniono te właściwości.

Oto kod JavaScript:

```javascript
module.exports = function (context, myQueueItem) {

    context.bindings.record = {
        text : "I'm running in a Node function! Data: '" + myQueueItem + "'"
    }   

    context.done();
};
```

## <a name="output---attributes"></a>Dane wyjściowe — atrybuty

W [bibliotekach klas języka C#](functions-dotnet-class-library.md)Użyj atrybutu [Mobile](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) .

Aby uzyskać informacje na temat właściwości atrybutów, które można skonfigurować, zobacz [wyjście-konfiguracja](#output---configuration). Oto `MobileTable` przykład atrybutu w sygnaturze metody:

```csharp
[FunctionName("MobileAppsOutput")]        
[return: MobileTable(ApiKeySetting = "MyMobileAppKey", TableName = "MyTable", MobileAppUriSetting = "MyMobileAppUri")]
public static object Run(
    [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
    TraceWriter log)
{
    ...
}
```

Aby zapoznać się z kompletnym przykładem, zobacz [dane wyjściowe — przykład w języku C#](#output---c-example).

## <a name="output---configuration"></a>Dane wyjściowe — Konfiguracja

W poniższej tabeli objaśniono właściwości konfiguracji powiązań, które zostały ustawione w *function.js* pliku i `MobileTable` atrybutu.

|function.jswłaściwości | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
| **Wprowadź**| n/d | Musi być ustawiony na "mobilny"|
| **wskazywa**| n/d |Musi być ustawiona na wartość "out"|
| **Nazwij**| n/d | Nazwa parametru wyjściowego w sygnaturze funkcji.|
|**tableName** |**TableName**|Nazwa tabeli danych aplikacji mobilnej|
|**połączenia**|**MobileAppUriSetting**|Nazwa ustawienia aplikacji z adresem URL aplikacji mobilnej. Funkcja używa tego adresu URL do konstruowania wymaganych operacji REST dla aplikacji mobilnej. Utwórz ustawienie aplikacji w aplikacji funkcji, która zawiera adres URL aplikacji mobilnej, a następnie określ nazwę ustawienia aplikacji we `connection` właściwości w powiązaniu wejściowym. Adres URL wygląda następująco `http://<appname>.azurewebsites.net` .
|**apiKey**|**ApiKeySetting**|Nazwa ustawienia aplikacji z kluczem interfejsu API aplikacji mobilnej. Podaj klucz interfejsu API w przypadku [zaimplementowania klucza interfejsu API w zaplecze aplikacji mobilnej Node.js](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key)lub [ZAIMPLEMENTUJ klucz interfejsu API w zapleczu aplikacji mobilnej platformy .NET](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key). Aby podać klucz, Utwórz ustawienie aplikacji w aplikacji funkcji, która zawiera klucz interfejsu API, a następnie Dodaj `apiKey` Właściwość w powiązaniu wejściowym przy użyciu nazwy ustawienia aplikacji. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Nie udostępniaj klucza interfejsu API klientom aplikacji mobilnej. Powinien być bezpiecznie dystrybuowany tylko do klientów po stronie usług, takich jak Azure Functions. Azure Functions przechowuje informacje o połączeniu i klucze interfejsu API jako ustawienia aplikacji, tak aby nie były one sprawdzane w repozytorium kontroli źródła. Zapewnia to ochronę poufnych informacji.

## <a name="output---usage"></a>Dane wyjściowe — użycie

W funkcjach skryptu języka C# Użyj nazwanego parametru wyjściowego typu, `out object` Aby uzyskać dostęp do rekordu wyjściowego. W bibliotekach klas języka C# `MobileTable` atrybut może być używany z dowolnym z następujących typów:

* `ICollector<T>` lub `IAsyncCollector<T>` , gdzie `T` jest albo `JObject` dowolnego typu z `public string Id` właściwością.
* `out JObject`
* `out T` lub `out T[]` , gdzie `T` jest dowolnym typem z `public string Id` właściwością.

W Node.js funkcje Użyj, `context.bindings.<name>` Aby uzyskać dostęp do rekordu wyjściowego.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o wyzwalaczach i powiązaniach usługi Azure Functions](functions-triggers-bindings.md)
