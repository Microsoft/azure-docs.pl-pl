---
title: Azure Event Grid wyzwalacza dla Azure Functions
description: Dowiedz się, jak uruchamiać kod Event Grid zdarzenia w Azure Functions są wysyłane.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: devx-track-csharp, fasttrack-edit, devx-track-python
ms.openlocfilehash: 17968f2c137eef51eecdb6c7098c7056944dc970
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782193"
---
# <a name="azure-event-grid-trigger-for-azure-functions"></a>Azure Event Grid wyzwalacza dla Azure Functions

Użyj wyzwalacza funkcji, aby odpowiedzieć na zdarzenie wysłane do Event Grid tematu.

Aby uzyskać informacje na temat konfiguracji i instalacji, zobacz [omówienie .](./functions-bindings-event-grid.md)

## <a name="example"></a>Przykład

# <a name="c"></a>[C#](#tab/csharp)

Aby uzyskać przykład wyzwalacza HTTP, zobacz [Receive events to an HTTP endpoint (Odbieranie zdarzeń do punktu końcowego HTTP).](../event-grid/receive-events.md)

### <a name="c-2x-and-higher"></a>C# (2.x i wyższe)

W poniższym przykładzie pokazano funkcję [języka C#,](functions-dotnet-class-library.md) która wiąże się z funkcją `EventGridEvent` :

```cs
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace Company.Function
{
    public static class EventGridTriggerCSharp
    {
        [FunctionName("EventGridTest")]
        public static void EventGridTest([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
        {
            log.LogInformation(eventGridEvent.Data.ToString());
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz Pakiety, [Atrybuty,](#attributes-and-annotations) [Konfiguracja](#configuration)i [Użycie](#usage).

### <a name="version-1x"></a>Wersja 1.x

W poniższym przykładzie przedstawiono funkcję Functions 1.x [języka C#,](functions-dotnet-class-library.md) która jest wiązana z funkcją `JObject` :

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Azure.WebJobs.Host;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using Microsoft.Extensions.Logging;

namespace Company.Function
{
    public static class EventGridTriggerCSharp
    {
        [FunctionName("EventGridTriggerCSharp")]
        public static void Run([EventGridTrigger]JObject eventGridEvent, ILogger log)
        {
            log.LogInformation(eventGridEvent.ToString(Formatting.Indented));
        }
    }
}
```

# <a name="c-script"></a>[Skrypt języka C#](#tab/csharp-script)

W poniższym przykładzie pokazano powiązanie wyzwalacza wfunction.js *pliku* i funkcję skryptu [języka C#,](functions-reference-csharp.md) która używa powiązania.

Oto dane powiązania wfunction.js *pliku:*

```json
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

### <a name="version-2x-and-higher"></a>Wersja 2.x lub nowsza

Oto przykład powiązania z elementem `EventGridEvent` :

```csharp
#r "Microsoft.Azure.EventGrid"
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(EventGridEvent eventGridEvent, ILogger log)
{
    log.LogInformation(eventGridEvent.Data.ToString());
}
```

Aby uzyskać więcej informacji, zobacz Pakiety, [Atrybuty,](#attributes-and-annotations) [Konfiguracja](#configuration)i [Użycie](#usage).

### <a name="version-1x"></a>Wersja 1.x

Oto kod skryptu języka C# functions 1.x, który jest wiązany z `JObject` :

```cs
#r "Newtonsoft.Json"

using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

public static void Run(JObject eventGridEvent, TraceWriter log)
{
    log.Info(eventGridEvent.ToString(Formatting.Indented));
}
```

# <a name="java"></a>[Java](#tab/java)

Ta sekcja zawiera następujące przykłady:

* [Event Grid wyzwalacza, parametr ciągu](#event-grid-trigger-string-parameter)
* [Event Grid wyzwalacz, parametr POJO](#event-grid-trigger-pojo-parameter)

Poniższe przykłady pokazują powiązanie wyzwalacza w języku [Java,](functions-reference-java.md) które używa powiązania i wyświetla zdarzenie, najpierw odbierając zdarzenie jako, a drugie `String` jako obiekt POJO.

### <a name="event-grid-trigger-string-parameter"></a>Event Grid wyzwalacza, parametr ciągu

```java
  @FunctionName("eventGridMonitorString")
  public void logEvent(
    @EventGridTrigger(
      name = "event"
    ) 
    String content, 
    final ExecutionContext context) {
      context.getLogger().info("Event content: " + content);      
  }
```

### <a name="event-grid-trigger-pojo-parameter"></a>Event Grid wyzwalacz, parametr POJO

W tym przykładzie użyto następującego obiektu POJO reprezentującego właściwości najwyższego poziomu Event Grid zdarzenia:

```java
import java.util.Date;
import java.util.Map;

public class EventSchema {

  public String topic;
  public String subject;
  public String eventType;
  public Date eventTime;
  public String id;
  public String dataVersion;
  public String metadataVersion;
  public Map<String, Object> data;

}
```

Po przybyciu ładunek JSON zdarzenia jest dese/serializowany do obiektu POJO na użytek ```EventSchema``` funkcji . Ten proces umożliwia funkcji uzyskiwanie dostępu do właściwości zdarzenia w sposób obiektowy.

```java
  @FunctionName("eventGridMonitor")
  public void logEvent(
    @EventGridTrigger(
      name = "event"
    ) 
    EventSchema event, 
    final ExecutionContext context) {
      context.getLogger().info("Event content: ");
      context.getLogger().info("Subject: " + event.subject);
      context.getLogger().info("Time: " + event.eventTime); // automatically converted to Date by the runtime
      context.getLogger().info("Id: " + event.id);
      context.getLogger().info("Data: " + event.data);
  }
```

W [bibliotece środowiska uruchomieniowego funkcji języka Java](/java/api/overview/azure/functions/runtime)użyj `EventGridTrigger` adnotacji dla parametrów, których wartość pochodziłaby z usługi EventGrid. Parametry z tymi adnotacjami powodują uruchomienie funkcji po przybyciu zdarzenia.  Tej adnotacji można używać z natywnymi typami języka Java, obiektami POJO lub wartościami dopuszczaymi wartość null przy użyciu funkcji `Optional<T>` .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

W poniższym przykładzie pokazano powiązanie wyzwalacza wfunction.js *pliku* i funkcję [języka JavaScript,](functions-reference-node.md) która używa powiązania.

Oto dane powiązania wfunction.js *pliku:*

```json
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

Oto kod JavaScript:

```javascript
module.exports = function (context, eventGridEvent) {
    context.log("JavaScript Event Grid function processed a request.");
    context.log("Subject: " + eventGridEvent.subject);
    context.log("Time: " + eventGridEvent.eventTime);
    context.log("Data: " + JSON.stringify(eventGridEvent.data));
    context.done();
};
```

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

W poniższym przykładzie pokazano, jak skonfigurować Event Grid wyzwalacza wfunction.js *pliku.*

```powershell
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    }
  ]
}
```

Zdarzenie Event Grid jest udostępniane funkcji za pośrednictwem parametru o nazwie , jak pokazano w `eventGridEvent` poniższym przykładzie programu PowerShell.

```powershell
param($eventGridEvent, $TriggerMetadata)

# Make sure to pass hashtables to Out-String so they're logged correctly
$eventGridEvent | Out-String | Write-Host
```

# <a name="python"></a>[Python](#tab/python)

W poniższym przykładzie pokazano powiązanie wyzwalacza wfunction.js *pliku* i funkcję [języka Python,](functions-reference-python.md) która używa powiązania.

Oto dane powiązania wfunction.js *pliku:*

```json
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "event",
      "direction": "in"
    }
  ],
  "disabled": false,
  "scriptFile": "__init__.py"
}
```

Oto kod języka Python:

```python
import json
import logging

import azure.functions as func

def main(event: func.EventGridEvent):

    result = json.dumps({
        'id': event.id,
        'data': event.get_json(),
        'topic': event.topic,
        'subject': event.subject,
        'event_type': event.event_type,
    })

    logging.info('Python EventGrid trigger processed an event: %s', result)
```

---

## <a name="attributes-and-annotations"></a>Atrybuty i adnotacje

# <a name="c"></a>[C#](#tab/csharp)

W [bibliotekach klas języka C#](functions-dotnet-class-library.md)użyj [atrybutu EventGridTrigger.](https://github.com/Azure/azure-functions-eventgrid-extension/blob/master/src/EventGridExtension/TriggerBinding/EventGridTriggerAttribute.cs)

Oto atrybut `EventGridTrigger` w sygnaturze metody:

```csharp
[FunctionName("EventGridTest")]
public static void EventGridTest([EventGridTrigger] JObject eventGridEvent, ILogger log)
{
    ...
}
```

Pełny przykład można znaleźć w przykładzie w języku C#.

# <a name="c-script"></a>[Skrypt języka C#](#tab/csharp-script)

Atrybuty nie są obsługiwane przez skrypt języka C#.

# <a name="java"></a>[Java](#tab/java)

Adnotacja [EventGridTrigger](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/annotation/EventGridTrigger.java) umożliwia deklaratywne skonfigurowanie powiązania Event Grid przez podanie wartości konfiguracji. Zobacz [sekcje przykładów](#example) [i konfiguracji,](#configuration) aby uzyskać więcej szczegółów.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Atrybuty nie są obsługiwane przez język JavaScript.

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Atrybuty nie są obsługiwane przez program PowerShell.

# <a name="python"></a>[Python](#tab/python)

Atrybuty nie są obsługiwane przez język Python.

---

## <a name="configuration"></a>Konfigurowanie

W poniższej tabeli wyjaśniono właściwości konfiguracji powiązania ustawione w pliku *function.jspliku.* Nie ma żadnych parametrów konstruktora ani właściwości do ustawienia w `EventGridTrigger` atrybutze .

|function.jswłaściwości |Opis|
|---------|---------|
| **Typu** | Wymagane — musi mieć wartość `eventGridTrigger` . |
| **kierunek** | Wymagane — musi mieć wartość `in` . |
| **Nazwa** | Required — nazwa zmiennej używana w kodzie funkcji dla parametru, który odbiera dane zdarzenia. |

## <a name="usage"></a>Użycie

# <a name="c"></a>[C#](#tab/csharp)

W Azure Functions 1.x można użyć następujących typów parametrów dla Event Grid wyzwalacza:

* `JObject`
* `string`

W Azure Functions 2.x i wyższych dostępna jest również opcja użycia następującego typu parametru dla Event Grid wyzwalacza:

* `Microsoft.Azure.EventGrid.Models.EventGridEvent`- Definiuje właściwości pól wspólnych dla wszystkich typów zdarzeń.

> [!NOTE]
> Jeśli spróbujesz powiązać funkcje z programem w wersji 1, kompilator wyświetli komunikat "przestarzały" i zaleci jego `Microsoft.Azure.WebJobs.Extensions.EventGrid.EventGridEvent` `Microsoft.Azure.EventGrid.Models.EventGridEvent` użycie. Aby użyć nowszego typu, odwołaj się do pakietu NuGet [Microsoft.Azure.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid) i w pełni kwalifikowaj nazwę typu, poprzedzając `EventGridEvent` ją prefiksem `Microsoft.Azure.EventGrid.Models` .

# <a name="c-script"></a>[Skrypt języka C#](#tab/csharp-script)

W Azure Functions 1.x można użyć następujących typów parametrów dla Event Grid wyzwalacza:

* `JObject`
* `string`

W Azure Functions 2.x i wyższych dostępna jest również opcja użycia następującego typu parametru dla Event Grid wyzwalacza:

* `Microsoft.Azure.EventGrid.Models.EventGridEvent`- Definiuje właściwości pól wspólnych dla wszystkich typów zdarzeń.

> [!NOTE]
> Jeśli spróbujesz powiązać funkcje z programem w wersji 1, kompilator wyświetli komunikat "przestarzały" i zaleci jego `Microsoft.Azure.WebJobs.Extensions.EventGrid.EventGridEvent` `Microsoft.Azure.EventGrid.Models.EventGridEvent` użycie. Aby użyć nowszego typu, odwołaj się do pakietu NuGet [Microsoft.Azure.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid) i w pełni kwalifikowaj nazwę typu, poprzedzając `EventGridEvent` ją prefiksem `Microsoft.Azure.EventGrid.Models` . Aby uzyskać informacje o tym, jak odwoływać się do pakietów NuGet w funkcji skryptu języka C#, zobacz [Using NuGet packages (Używanie pakietów NuGet)](functions-reference-csharp.md#using-nuget-packages)

# <a name="java"></a>[Java](#tab/java)

Wystąpienie Event Grid jest dostępne za pośrednictwem parametru skojarzonego z `EventGridTrigger` atrybutem, typizonego jako `EventSchema` . Zobacz [przykład, aby](#example) uzyskać więcej szczegółów.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Wystąpienie Event Grid jest dostępne za pośrednictwem parametru skonfigurowanego w *function.jswe* właściwości `name` pliku.

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Wystąpienie Event Grid jest dostępne za pośrednictwem parametru skonfigurowanego w *function.jswe* właściwości `name` pliku.

# <a name="python"></a>[Python](#tab/python)

Wystąpienie Event Grid jest dostępne za pośrednictwem parametru skonfigurowanego wfunction.js *właściwości* pliku `name` o typie `func.EventGridEvent` .

---

## <a name="event-schema"></a>Schemat zdarzeń

Dane dla zdarzenia Event Grid są odbierane jako obiekt JSON w treści żądania HTTP. Kod JSON wygląda podobnie do poniższego przykładu:

```json
[{
  "topic": "/subscriptions/{subscriptionid}/resourceGroups/eg0122/providers/Microsoft.Storage/storageAccounts/egblobstore",
  "subject": "/blobServices/default/containers/{containername}/blobs/blobname.jpg",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2018-01-23T17:02:19.6069787Z",
  "id": "{guid}",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "{guid}",
    "requestId": "{guid}",
    "eTag": "0x8D562831044DDD0",
    "contentType": "application/octet-stream",
    "contentLength": 2248,
    "blobType": "BlockBlob",
    "url": "https://egblobstore.blob.core.windows.net/{containername}/blobname.jpg",
    "sequencer": "000000000000272D000000000003D60F",
    "storageDiagnostics": {
      "batchId": "{guid}"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

Przedstawiony przykład to tablica jednego elementu. Event Grid zawsze wysyła tablicę i może wysyłać więcej niż jedno zdarzenie w tablicy. Środowisko uruchomieniowe wywołuje funkcję raz dla każdego elementu tablicy.

Właściwości najwyższego poziomu w danych JSON zdarzenia są takie same wśród wszystkich typów zdarzeń, podczas gdy zawartość właściwości jest specyficzna `data` dla każdego typu zdarzenia. Przedstawiony przykład jest dla zdarzenia magazynu obiektów blob.

Aby uzyskać wyjaśnienia typowych i specyficznych dla zdarzeń właściwości, zobacz [Właściwości](../event-grid/event-schema.md#event-properties) zdarzenia w Event Grid dokumentacji.

Typ `EventGridEvent` definiuje tylko właściwości najwyższego poziomu; właściwość jest `Data` właściwością `JObject` .

## <a name="create-a-subscription"></a>Tworzenie subskrypcji

Aby rozpocząć odbieranie Event Grid http, utwórz subskrypcję usługi Event Grid, która określa adres URL punktu końcowego, który wywołuje funkcję.

### <a name="azure-portal"></a>Azure Portal

W przypadku funkcji, które tworzysz w Azure Portal za pomocą  wyzwalacza Event Grid, wybierz pozycję Integracja, a następnie wybierz wyzwalacz Event Grid **i** wybierz pozycję Utwórz Event Grid **subskrypcji**.

:::image type="content" source="media/functions-bindings-event-grid/portal-sub-create.png" alt-text="Połącz nową subskrypcję zdarzeń, aby wyzwolić w portalu.":::

Po wybraniu tego linku w portalu zostanie otwarta strona Tworzenie **subskrypcji** zdarzeń z zdefiniowanym punktem końcowym bieżącego wyzwalacza.

:::image type="content" source="media/functions-bindings-event-grid/endpoint-url.png" alt-text="Tworzenie subskrypcji zdarzeń z już zdefiniowanym punktem końcowym funkcji" :::

Aby uzyskać więcej informacji na temat tworzenia subskrypcji przy użyciu usługi Azure Portal, zobacz Tworzenie zdarzenia niestandardowego [— Azure Portal](../event-grid/custom-event-quickstart-portal.md) w Event Grid dokumentacji.

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby utworzyć subskrypcję przy użyciu [interfejsu wiersza](/cli/azure/get-started-with-azure-cli)polecenia platformy Azure, użyj [polecenia az eventgrid event-subscription create.](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_create)

Polecenie wymaga adresu URL punktu końcowego, który wywołuje funkcję. W poniższym przykładzie pokazano wzorzec adresu URL specyficzny dla wersji:

#### <a name="version-2x-and-higher-runtime"></a>Środowisko uruchomieniowe w wersji 2.x (lub wyższej)

```http
https://{functionappname}.azurewebsites.net/runtime/webhooks/eventgrid?functionName={functionname}&code={systemkey}
```

#### <a name="version-1x-runtime"></a>Środowisko uruchomieniowe w wersji 1.x

```http
https://{functionappname}.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName={functionname}&code={systemkey}
```

Klucz systemowy jest kluczem autoryzacji, który musi zostać uwzględniony w adresie URL punktu końcowego dla Event Grid wyzwalacza. W poniższej sekcji opisano sposób uzyskania klucza systemowego.

Oto przykład subskrybowania konta magazynu obiektów blob (z symbolem zastępczym klucza systemowego):

#### <a name="version-2x-and-higher-runtime"></a>Środowisko uruchomieniowe w wersji 2.x (lub wyższej)

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup \
    --provider-namespace Microsoft.Storage --resource-type storageAccounts \
    --resource-name myblobstorage12345 --name myFuncSub \
    --included-event-types Microsoft.Storage.BlobCreated \
    --subject-begins-with /blobServices/default/containers/images/blobs/ \
    --endpoint https://mystoragetriggeredfunction.azurewebsites.net/runtime/webhooks/eventgrid?functionName=imageresizefunc&code=<key>
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup ^
    --provider-namespace Microsoft.Storage --resource-type storageAccounts ^
    --resource-name myblobstorage12345 --name myFuncSub ^
    --included-event-types Microsoft.Storage.BlobCreated ^
    --subject-begins-with /blobServices/default/containers/images/blobs/ ^
    --endpoint https://mystoragetriggeredfunction.azurewebsites.net/runtime/webhooks/eventgrid?functionName=imageresizefunc&code=<key>
```

---

#### <a name="version-1x-runtime"></a>Środowisko uruchomieniowe w wersji 1.x

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup \
    --provider-namespace Microsoft.Storage --resource-type storageAccounts \
    --resource-name myblobstorage12345 --name myFuncSub \
    --included-event-types Microsoft.Storage.BlobCreated \
    --subject-begins-with /blobServices/default/containers/images/blobs/ \
    --endpoint https://mystoragetriggeredfunction.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName=imageresizefunc&code=<key>
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup ^
    --provider-namespace Microsoft.Storage --resource-type storageAccounts ^
    --resource-name myblobstorage12345 --name myFuncSub ^
    --included-event-types Microsoft.Storage.BlobCreated ^
    --subject-begins-with /blobServices/default/containers/images/blobs/ ^
    --endpoint https://mystoragetriggeredfunction.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName=imageresizefunc&code=<key>
```

---

Aby uzyskać więcej informacji na temat tworzenia subskrypcji, zobacz przewodnik Szybki start usługi [Blob Storage](../storage/blobs/storage-blob-event-quickstart.md#subscribe-to-your-storage-account) lub inne Event Grid Szybki start.

### <a name="get-the-system-key"></a>Uzyskiwanie klucza systemowego

Klucz systemowy można uzyskać przy użyciu następującego interfejsu API (HTTP GET):

#### <a name="version-2x-and-higher-runtime"></a>Środowisko uruchomieniowe w wersji 2.x (lub wyższej)

```
http://{functionappname}.azurewebsites.net/admin/host/systemkeys/eventgrid_extension?code={masterkey}
```

#### <a name="version-1x-runtime"></a>Środowisko uruchomieniowe w wersji 1.x

```
http://{functionappname}.azurewebsites.net/admin/host/systemkeys/eventgridextensionconfig_extension?code={masterkey}
```

Jest to interfejs API administratora, dlatego wymaga klucza głównego [aplikacji funkcji](functions-bindings-http-webhook-trigger.md#authorization-keys). Nie należy mylić klucza systemowego (w celu wywołania funkcji wyzwalacza Event Grid) z kluczem głównym (do wykonywania zadań administracyjnych w aplikacji funkcji). Podczas subskrybowania Event Grid tematu należy użyć klucza systemowego.

Oto przykład odpowiedzi, która dostarcza klucz systemowy:

```
{
  "name": "eventgridextensionconfig_extension",
  "value": "{the system key for the function}",
  "links": [
    {
      "rel": "self",
      "href": "{the URL for the function, without the system key}"
    }
  ]
}
```

Klucz główny aplikacji funkcji można uzyskać z karty **Ustawienia aplikacji funkcji** w portalu.

> [!IMPORTANT]
> Klucz główny zapewnia administratorowi dostęp do aplikacji funkcji. Nie należy udostępniać tego klucza innym osobom ani rozpowszechniać go w natywnych aplikacjach klienckich.

Aby uzyskać więcej informacji, zobacz [klucze autoryzacji w](functions-bindings-http-webhook-trigger.md#authorization-keys) artykule z informacjami o wyzwalaczu HTTP.

Alternatywnie możesz wysłać żądania HTTP PUT, aby samodzielnie określić wartość klucza.

## <a name="local-testing-with-viewer-web-app"></a>Testowanie lokalne za pomocą aplikacji internetowej przeglądarki

Aby przetestować wyzwalacz Event Grid lokalnie, musisz pobrać żądania HTTP Event Grid z ich źródła w chmurze na maszynę lokalną. Jednym ze sposobów jest przechwytowanie żądań w trybie online i ręczne ich ponowne wysyłanie na komputerze lokalnym:

1. [Tworzenie aplikacji internetowej przeglądarki,](#create-a-viewer-web-app) która przechwytuje komunikaty o zdarzeniach.
1. [Utwórz subskrypcję Event Grid, która](#create-an-event-grid-subscription) wysyła zdarzenia do aplikacji przeglądarki.
1. [Wygeneruj](#generate-a-request) żądanie i skopiuj treść żądania z aplikacji przeglądarki.
1. [Ręcznie opublikuj żądanie pod adresem](#manually-post-the-request) URL hosta lokalnego funkcji Event Grid wyzwalacza.

Po zakończenie testowania możesz użyć tej samej subskrypcji w środowisku produkcyjnym, aktualizując punkt końcowy. Użyj polecenia [az eventgrid event-subscription update interfejsu](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_update) wiersza polecenia platformy Azure.

### <a name="create-a-viewer-web-app"></a>Tworzenie aplikacji internetowej przeglądarki

Aby uprościć przechwytywanie komunikatów o zdarzeniach, możesz wdrożyć wstępnie sbudowaną aplikację [internetową,](https://github.com/Azure-Samples/azure-event-grid-viewer) która wyświetla komunikaty o zdarzeniach. Wdrożone rozwiązanie zawiera plan usługi App Service, aplikację internetową usługi App Service i kod źródłowy z repozytorium GitHub.

Wybierz pozycję **Wdróż na platformie Azure** w celu wdrożenia rozwiązania w subskrypcji. W witrynie Azure Portal podaj wartości parametrów.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png" alt="Button to Deploy to Azure." /></a>

Wdrożenie może potrwać kilka minut. Po pomyślnym wdrożeniu należy wyświetlić aplikację sieci Web i upewnić się, że jest uruchomiona. W przeglądarce sieci Web przejdź do: `https://<your-site-name>.azurewebsites.net`

Zobaczysz witrynę, w której nie opublikowano jeszcze żadnych zdarzeń.

![Wyświetlanie nowej witryny](media/functions-bindings-event-grid/view-site.png)

### <a name="create-an-event-grid-subscription"></a>Tworzenie subskrypcji usługi Event Grid

Utwórz Event Grid typu, który chcesz przetestować, i nadaj jej adres URL z aplikacji internetowej jako punkt końcowy dla powiadomień o zdarzeniach. Punkt końcowy dla aplikacji internetowej musi zawierać sufiks `/api/updates/`. Pełny adres URL to `https://<your-site-name>.azurewebsites.net/api/updates`

Aby uzyskać informacje na temat tworzenia subskrypcji przy użyciu usługi Azure Portal, zobacz Tworzenie zdarzenia niestandardowego [— Azure Portal](../event-grid/custom-event-quickstart-portal.md) w Event Grid dokumentacji.

### <a name="generate-a-request"></a>Generowanie żądania

Wyzwolić zdarzenie, które wygeneruje ruch HTTP do punktu końcowego aplikacji internetowej.  Jeśli na przykład utworzono subskrypcję usługi Blob Storage, przekaż lub usuń obiekt blob. Gdy żądanie pojawia się w aplikacji internetowej, skopiuj treść żądania.

Żądanie weryfikacji subskrypcji zostanie odebrane jako pierwsze. zignoruj wszystkie żądania weryfikacji i skopiuj żądanie zdarzenia.

![Kopiowanie treści żądania z aplikacji internetowej](media/functions-bindings-event-grid/view-results.png)

### <a name="manually-post-the-request"></a>Ręczne opublikuj żądanie

Uruchom funkcję Event Grid lokalnie. Nagłówki i muszą być ustawione ręcznie, a wszystkie pozostałe wartości mogą być pozostawione `Content-Type` `aeg-event-type` jako domyślne.

Użyj narzędzia, takiego jak [Postman](https://www.getpostman.com/) lub [curl,](https://curl.haxx.se/docs/httpscripting.html) aby utworzyć żądanie HTTP POST:

* Ustaw `Content-Type: application/json` nagłówek.
* Ustaw `aeg-event-type: Notification` nagłówek.
* Wklej dane RequestBin do treści żądania.
* Opublikuj wpis pod adresem URL Event Grid wyzwalacza.
  * W przypadku wersji 2.x i wyższych użyj następującego wzorca:

    ```
    http://localhost:7071/runtime/webhooks/eventgrid?functionName={FUNCTION_NAME}
    ```

  * W przypadku wersji 1.x użyj:

    ```
    http://localhost:7071/admin/extensions/EventGridExtensionConfig?functionName={FUNCTION_NAME}
    ```

Parametr `functionName` musi być nazwą określoną w `FunctionName` atrybutze .

Na poniższych zrzutach ekranu przedstawiono nagłówki i treść żądania w programie Postman:

![Nagłówki w programie Postman](media/functions-bindings-event-grid/postman2.png)

![Treść żądania w programie Postman](media/functions-bindings-event-grid/postman.png)

Funkcja Event Grid wyzwalacza jest wykonywana i wyświetla dzienniki podobne do poniższego przykładu:

![Przykładowe Event Grid funkcji wyzwalacza](media/functions-bindings-event-grid/eg-output.png)

## <a name="next-steps"></a>Następne kroki

* [Wysyłanie zdarzenia Event Grid zdarzeń](./functions-bindings-event-grid-output.md)
