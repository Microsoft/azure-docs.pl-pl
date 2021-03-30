---
title: Notification Hubs powiązania Azure Functions
description: Dowiedz się, jak używać powiązań centrum powiadomień platformy Azure w Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.custom: devx-track-csharp
ms.date: 11/21/2017
ms.author: cshoe
ms.openlocfilehash: c4198a1b73f76d61e39324befc85b55bd260e363
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88212219"
---
# <a name="notification-hubs-output-binding-for-azure-functions"></a>Notification Hubs powiązanie danych wyjściowych dla Azure Functions

W tym artykule wyjaśniono, jak wysyłać powiadomienia wypychane za pomocą powiązań [usługi Azure Notification Hubs](../notification-hubs/notification-hubs-push-notification-overview.md) w programie Azure Functions. Azure Functions obsługuje powiązania wyjściowe dla Notification Hubs.

Należy skonfigurować usługę Azure Notification Hubs dla usługi powiadomień platformy (PNS), która ma być używana. Aby dowiedzieć się, jak uzyskać powiadomienia wypychane w aplikacji klienckiej z Notification Hubs, zobacz [wprowadzenie do Notification Hubs](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) i wybierz docelową platformę klienta z listy rozwijanej w górnej części strony.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!IMPORTANT]
> Firma Google ma [przestarzałe Google Cloud Messaging (GCM) na korzyść usługi Firebase Cloud Messaging (FCM)](https://developers.google.com/cloud-messaging/faq). To powiązanie danych wyjściowych nie obsługuje FCM. Aby wysyłać powiadomienia przy użyciu programu FCM, należy użyć [interfejsu API Firebase](https://firebase.google.com/docs/cloud-messaging/server#choosing-a-server-option) bezpośrednio w funkcji lub użyć [powiadomień dotyczących szablonów](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md).

## <a name="packages---functions-1x"></a>Pakiety — funkcje 1. x

Powiązania Notification Hubs są dostępne w pakiecie NuGet [Microsoft. Azure. WebJobs. Extensions. NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.NotificationHubs) w wersji 1. x. Kod źródłowy pakietu znajduje się w repozytorium [Azure-WebJobs-SDK-Extensions — rozszerzenia](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/v2.x/src/WebJobs.Extensions.NotificationHubs) GitHub.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x-and-higher"></a>Pakiety — funkcje 2. x i nowsze

To powiązanie jest niedostępne w funkcjach 2. x i wyższych.

## <a name="example---template"></a>Przykład — szablon

Wysyłane powiadomienia mogą być natywnymi powiadomieniami lub [powiadomieniami o szablonach](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md). Natywne powiadomienia są przeznaczone dla określonej platformy klienta zgodnie z konfiguracją we `platform` Właściwości powiązania danych wyjściowych. Powiadomienia szablonu mogą służyć do kierowania wielu platform.   

Zobacz przykład specyficzny dla języka:

* [Parametr out skryptu języka C#](#c-script-template-example---out-parameter)
* [Skrypt C# — asynchroniczny](#c-script-template-example---asynchronous)
* [Skrypt C# — JSON](#c-script-template-example---json)
* [Typy bibliotek skryptów C#](#c-script-template-example---library-types)
* [F#](#f-template-example)
* [JavaScript](#javascript-template-example)

### <a name="c-script-template-example---out-parameter"></a>Przykładowy parametr szablonu skryptu C#

Ten przykład wysyła powiadomienie dla [rejestracji szablonu](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) , która zawiera `message` symbol zastępczy w szablonie.

```cs
using System;
using System.Threading.Tasks;
using System.Collections.Generic;

public static void Run(string myQueueItem,  out IDictionary<string, string> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    notification = GetTemplateProperties(myQueueItem);
}

private static IDictionary<string, string> GetTemplateProperties(string message)
{
    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
    templateProperties["message"] = message;
    return templateProperties;
}
```

### <a name="c-script-template-example---asynchronous"></a>Przykładowy szablon skryptu C# — asynchroniczny

W przypadku korzystania z kodu asynchronicznego parametry out są niedozwolone. W tym przypadku użyj `IAsyncCollector` do zwrócenia powiadomienia dotyczącego szablonu. Poniższy kod stanowi asynchroniczny przykład kodu. 

```cs
using System;
using System.Threading.Tasks;
using System.Collections.Generic;

public static async Task Run(string myQueueItem, IAsyncCollector<IDictionary<string,string>> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    log.Info($"Sending Template Notification to Notification Hub");
    await notification.AddAsync(GetTemplateProperties(myQueueItem));    
}

private static IDictionary<string, string> GetTemplateProperties(string message)
{
    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
    templateProperties["user"] = "A new user wants to be added : " + message;
    return templateProperties;
}
```

### <a name="c-script-template-example---json"></a>Przykładowy szablon skryptu C# — JSON

Ten przykład wysyła powiadomienie dotyczące [rejestracji szablonu](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) zawierającej `message` symbol zastępczy w szablonie przy użyciu prawidłowego ciągu JSON.

```cs
using System;

public static void Run(string myQueueItem,  out string notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    notification = "{\"message\":\"Hello from C#. Processed a queue item!\"}";
}
```

### <a name="c-script-template-example---library-types"></a>Przykład szablonu skryptu C# — typy bibliotek

Ten przykład pokazuje, jak używać typów zdefiniowanych w [bibliotece Microsoft Azure Notification Hubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/). 

```cs
#r "Microsoft.Azure.NotificationHubs"

using System;
using System.Threading.Tasks;
using Microsoft.Azure.NotificationHubs;

public static void Run(string myQueueItem,  out Notification notification, TraceWriter log)
{
   log.Info($"C# Queue trigger function processed: {myQueueItem}");
   notification = GetTemplateNotification(myQueueItem);
}

private static TemplateNotification GetTemplateNotification(string message)
{
    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
    templateProperties["message"] = message;
    return new TemplateNotification(templateProperties);
}
```

### <a name="f-template-example"></a>Przykład szablonu języka F #

Ten przykład wysyła powiadomienie dla [rejestracji szablonu](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) zawierającej `location` i `message` .

```fsharp
let Run(myTimer: TimerInfo, notification: byref<IDictionary<string, string>>) =
    notification = dict [("location", "Redmond"); ("message", "Hello from F#!")]
```

### <a name="javascript-template-example"></a>Przykład szablonu JavaScript

Ten przykład wysyła powiadomienie dla [rejestracji szablonu](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) zawierającej `location` i `message` .

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    if (myTimer.IsPastDue)
    {
        context.log('Node.js is running late!');
    }
    context.log('Node.js timer trigger function ran!', timeStamp);  
    context.bindings.notification = {
        location: "Redmond",
        message: "Hello from Node!"
    };
    context.done();
};
```

## <a name="example---apns-native"></a>Przykład — natywny APN

Ten przykładowy skrypt w języku C# pokazuje, jak wysłać natywne powiadomienie usługi APNS. 

```cs
#r "Microsoft.Azure.NotificationHubs"
#r "Newtonsoft.Json"

using System;
using Microsoft.Azure.NotificationHubs;
using Newtonsoft.Json;

public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a new user to be processed in the form of a JSON string with 
    // a "name" value.
    //
    // The JSON format for a native APNS notification is ...
    // { "aps": { "alert": "notification message" }}  

    log.LogInformation($"Sending APNS notification of a new user");    
    dynamic user = JsonConvert.DeserializeObject(myQueueItem);    
    string apnsNotificationPayload = "{\"aps\": {\"alert\": \"A new user wants to be added (" + 
                                        user.name + ")\" }}";
    log.LogInformation($"{apnsNotificationPayload}");
    await notification.AddAsync(new AppleNotification(apnsNotificationPayload));        
}
```

## <a name="example---wns-native"></a>Przykład — WNS natywny

Ten przykładowy skrypt w języku C# pokazuje, jak używać typów zdefiniowanych w [bibliotece Microsoft Azure Notification Hubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) do wysyłania wyskakujących powiadomień natywnych WNS. 

```cs
#r "Microsoft.Azure.NotificationHubs"
#r "Newtonsoft.Json"

using System;
using Microsoft.Azure.NotificationHubs;
using Newtonsoft.Json;

public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a new user to be processed in the form of a JSON string with 
    // a "name" value.
    //
    // The XML format for a native WNS toast notification is ...
    // <?xml version="1.0" encoding="utf-8"?>
    // <toast>
    //      <visual>
    //     <binding template="ToastText01">
    //       <text id="1">notification message</text>
    //     </binding>
    //   </visual>
    // </toast>

    log.Info($"Sending WNS toast notification of a new user");    
    dynamic user = JsonConvert.DeserializeObject(myQueueItem);    
    string wnsNotificationPayload = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                                    "<toast><visual><binding template=\"ToastText01\">" +
                                        "<text id=\"1\">" + 
                                            "A new user wants to be added (" + user.name + ")" + 
                                        "</text>" +
                                    "</binding></visual></toast>";

    log.Info($"{wnsNotificationPayload}");
    await notification.AddAsync(new WindowsNotification(wnsNotificationPayload));        
}
```

## <a name="attributes"></a>Atrybuty

W [bibliotekach klas języka C#](functions-dotnet-class-library.md), Użyj atrybutu [NotificationHub](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs) .

Parametry i właściwości konstruktora atrybutu są opisane w sekcji [konfiguracji](#configuration) .

## <a name="configuration"></a>Konfigurowanie

W poniższej tabeli objaśniono właściwości konfiguracji powiązań, które zostały ustawione w *function.js* pliku i `NotificationHub` atrybutu:

|function.jswłaściwości | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**Wprowadź** |n/d| Musi być ustawiony na `notificationHub` . |
|**wskazywa** |n/d| Musi być ustawiony na `out` . | 
|**Nazwij** |n/d| Nazwa zmiennej używana w kodzie funkcji dla komunikatu centrum powiadomień. |
|**tagExpression** |**TagExpression** | Wyrażenia tagów umożliwiają określenie, że powiadomienia mają być dostarczane do zestawu urządzeń, które zostały zarejestrowane w celu otrzymywania powiadomień pasujących do wyrażenia tagu.  Aby uzyskać więcej informacji, zobacz [Routing i wyrażenia tagów](../notification-hubs/notification-hubs-tags-segment-push-message.md). |
|**hubName** | **HubName** | Nazwa zasobu centrum powiadomień w Azure Portal. |
|**połączenia** | **ConnectionStringSetting** | Nazwa ustawienia aplikacji, która zawiera Notification Hubs parametry połączenia.  Parametry połączenia muszą być ustawione na wartość *DefaultFullSharedAccessSignature* dla centrum powiadomień. Zobacz [Konfiguracja parametrów połączenia](#connection-string-setup) w dalszej części tego artykułu.|
|**platformach** | **Platforma** | Właściwość platform wskazuje platformę klienta, której dotyczą Twoje powiadomienia. Domyślnie, jeśli właściwość platformy zostanie pominięta z powiązania danych wyjściowych, można użyć powiadomień szablonów dla wszystkich platform skonfigurowanych w centrum powiadomień platformy Azure. Aby uzyskać więcej informacji o używaniu szablonów na ogół do wysyłania powiadomień międzyplatformowych za pomocą Centrum powiadomień platformy Azure, zobacz [Szablony](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md). Po ustawieniu **platforma** musi mieć jedną z następujących wartości: <ul><li><code>apns</code>&mdash;Apple Push Notification Service. Aby uzyskać więcej informacji na temat konfigurowania centrum powiadomień dla usługi APNS i otrzymywania powiadomień w aplikacji klienckiej, zobacz [wysyłanie powiadomień wypychanych do systemu iOS przy użyciu usługi Azure Notification Hubs](../notification-hubs/xamarin-notification-hubs-ios-push-notification-apns-get-started.md).</li><li><code>adm</code>&mdash;[Amazon Device Messaging](https://developer.amazon.com/device-messaging). Aby uzyskać więcej informacji na temat konfigurowania centrum powiadomień dla usługi ADM i otrzymywania powiadomień w aplikacji Kindle, zobacz [wprowadzenie with Notification Hubs for Kindle Apps](../notification-hubs/notification-hubs-android-push-notification-google-fcm-get-started.md).</li><li><code>wns</code>&mdash;[Usługi powiadomień wypychanych systemu Windows](/windows/uwp/design/shell/tiles-and-notifications/windows-push-notification-services--wns--overview) ukierunkowane na platformy Windows. Windows Phone 8,1 i nowsze są również obsługiwane przez WNS. Aby uzyskać więcej informacji, zobacz [wprowadzenie do Notification Hubs dla aplikacji platformy uniwersalnej systemu Windows](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).</li><li><code>mpns</code>&mdash;[Usługa powiadomień wypychanych firmy Microsoft](/previous-versions/windows/apps/ff402558(v=vs.105)). Ta platforma obsługuje Windows Phone 8 i starszych Windows Phone platform. Aby uzyskać więcej informacji, zobacz [wysyłanie powiadomień wypychanych przy użyciu usługi Azure Notification Hubs w Windows Phone](../notification-hubs/notification-hubs-windows-mobile-push-notifications-mpns.md).</li></ul> |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

### <a name="functionjson-file-example"></a>Przykład function.jspliku

Oto przykład powiązania Notification Hubs w *function.js* pliku.

```json
{
  "bindings": [
    {
      "type": "notificationHub",
      "direction": "out",
      "name": "notification",
      "tagExpression": "",
      "hubName": "my-notification-hub",
      "connection": "MyHubConnectionString",
      "platform": "apns"
    }
  ],
  "disabled": false
}
```

### <a name="connection-string-setup"></a>Konfiguracja parametrów połączenia

Aby użyć powiązania danych wyjściowych centrum powiadomień, należy skonfigurować parametry połączenia dla centrum. Możesz wybrać istniejące centrum powiadomień lub utworzyć nowe po prawej stronie na karcie *integracja* w Azure Portal. Parametry połączenia można również skonfigurować ręcznie. 

Aby skonfigurować parametry połączenia z istniejącym centrum powiadomień:

1. W [Azure Portal](https://portal.azure.com)przejdź do centrum powiadomień, wybierz pozycję **zasady dostępu** i wybierz przycisk Kopiuj obok zasad **DefaultFullSharedAccessSignature** . Spowoduje to skopiowanie parametrów połączenia dla zasad *DefaultFullSharedAccessSignatureymi* do centrum powiadomień. Te parametry połączenia umożliwiają funkcji wysyłanie komunikatów powiadomień do centrum.
    ![Kopiowanie parametrów połączenia centrum powiadomień](./media/functions-bindings-notification-hubs/get-notification-hub-connection.png)
1. Przejdź do aplikacji funkcji w Azure Portal, wybierz pozycję **Ustawienia aplikacji**, Dodaj klucz, taki jak **MyHubConnectionString**, wklej skopiowany *DefaultFullSharedAccessSignature*  dla centrum powiadomień jako wartość, a następnie kliknij przycisk **Zapisz**.

Nazwa tego ustawienia aplikacji ma wartość ustawienia połączenie wyjściowe powiązania w *function.jsna* lub atrybut platformy .NET. Zapoznaj się z [sekcją konfiguracji](#configuration) wcześniejszą w tym artykule.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="exceptions-and-return-codes"></a>Wyjątki i kody powrotu

| Wiązanie | Odwołanie |
|---|---|
| Centrum powiadomień | [Przewodnik obsługi](/rest/api/notificationhubs/) |

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o wyzwalaczach i powiązaniach usługi Azure Functions](functions-triggers-bindings.md)
