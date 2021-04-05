---
title: Tworzenie punktu końcowego usługi RESTful
description: W tym samouczku pokazano, jak utworzyć punkt końcowy RESTful dla dostawców niestandardowych. Szczegóły dotyczące obsługi żądań i odpowiedzi dla obsługiwanych metod HTTP RESTful.
author: jjbfour
ms.topic: tutorial
ms.date: 01/13/2021
ms.author: jobreen
ms.openlocfilehash: 54d0df287865d5d92403bf68227a2d4c5faa8bb4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98200213"
---
# <a name="author-a-restful-endpoint-for-custom-providers"></a>Tworzenie punktu końcowego RESTful dla dostawców niestandardowych

Dostawca niestandardowy to kontrakt między platformą Azure i punktem końcowym. Dostawcy niestandardowi mogą dostosowywać przepływy pracy na platformie Azure. W tym samouczku przedstawiono sposób tworzenia niestandardowego punktu końcowego RESTful dostawcy. Jeśli nie znasz dostawców niestandardowych platformy Azure, zapoznaj [się z tematem Omówienie niestandardowych dostawców zasobów](overview.md).

> [!NOTE]
> W tym samouczku przedstawiono samouczek dotyczący [konfigurowania Azure Functions dla dostawców niestandardowych platformy Azure](./tutorial-custom-providers-function-setup.md). Niektóre kroki opisane w tym samouczku działają tylko wtedy, gdy aplikacja funkcji platformy Azure została skonfigurowana do pracy z dostawcami niestandardowymi.

## <a name="work-with-custom-actions-and-custom-resources"></a>Współpraca z akcjami niestandardowymi i zasobami niestandardowymi

W tym samouczku zaktualizujesz aplikację funkcji, aby działała jako punkt końcowy RESTful dla niestandardowego dostawcy. Zasoby i akcje na platformie Azure są modelowane po następującej podstawowej specyfikacji RESTful:

- **Put**: Tworzenie nowego zasobu
- **Get (wystąpienie)**: pobieranie istniejącego zasobu
- **Usuwanie**: Usuwanie istniejącego zasobu
- **Wpis**: wyzwalanie akcji
- **Pobierz (zbieranie)**: Wyświetl listę wszystkich istniejących zasobów

 Na potrzeby tego samouczka używasz usługi Azure Table Storage. Jednak może to być dowolna baza danych lub usługa magazynu.

## <a name="partition-custom-resources-in-storage"></a>Partycjonowanie zasobów niestandardowych w magazynie

Ponieważ tworzysz usługę RESTful, musisz zapisać utworzone zasoby. W przypadku usługi Azure Table Storage konieczne jest wygenerowanie kluczy partycji i wierszy dla danych. W przypadku dostawców niestandardowych dane powinny być partycjonowane do niestandardowego dostawcy. Gdy żądanie przychodzące jest wysyłane do niestandardowego dostawcy, Dostawca niestandardowy dodaje `x-ms-customproviders-requestpath` nagłówek do żądań wychodzących do punktu końcowego.

Poniższy przykład przedstawia `x-ms-customproviders-requestpath` nagłówek dla zasobu niestandardowego:

```
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{myResourceType}/{myResourceName}
```

Na podstawie nagłówka przykładu `x-ms-customproviders-requestpath` można utworzyć parametry *PartitionKey* i *rowKey* dla magazynu, jak pokazano w poniższej tabeli:

Parametr | Template | Opis
---|---|---
*partitionKey* | `{subscriptionId}:{resourceGroupName}:{resourceProviderName}` | Parametr *partitionKey* określa sposób partycjonowania danych. Zwykle dane są partycjonowane przez niestandardowe wystąpienie dostawcy.
*rowKey* | `{myResourceType}:{myResourceName}` | Parametr *rowKey* określa indywidualny identyfikator dla danych. Zazwyczaj identyfikator jest nazwą zasobu.

Należy również utworzyć nową klasę, aby modelować zasób niestandardowy. W tym samouczku dodasz następującą klasę **CustomResource** do aplikacji funkcji:

```csharp
// Custom Resource Table Entity
public class CustomResource : TableEntity
{
    public string Data { get; set; }
}
```
**CustomResource** jest prostą, ogólną klasą akceptującą dowolne dane wejściowe. Jest on oparty na **klasy tableentity**, który jest używany do przechowywania danych. Klasa **CustomResource** dziedziczy dwie właściwości z **klasy tableentity**: **partitionKey** i **rowKey**.

## <a name="support-custom-provider-restful-methods"></a>Obsługa niestandardowych metod RESTful dostawcy

> [!NOTE]
> Jeśli nie kopiujesz kodu bezpośrednio z tego samouczka, zawartość odpowiedzi musi być prawidłowym kodem JSON, który ustawia `Content-Type` nagłówek na `application/json` .

Teraz, po skonfigurowaniu partycjonowania danych, Utwórz podstawowe metody CRUD i Trigger dla zasobów niestandardowych i akcji niestandardowych. Ponieważ Dostawcy niestandardowi działają jako proxy, punkt końcowy RESTful musi modelować i obsługiwać żądanie i odpowiedź. Poniższe fragmenty kodu pokazują, jak obsłużyć podstawowe operacje RESTful.

### <a name="trigger-a-custom-action"></a>Wyzwalanie akcji niestandardowej

W przypadku dostawców niestandardowych akcja niestandardowa jest wyzwalana przez żądania POST. Akcja niestandardowa może opcjonalnie zaakceptować treść żądania, która zawiera zestaw parametrów wejściowych. Następnie akcja zwraca odpowiedź, która sygnalizuje wynik akcji oraz o tym, czy zakończyła się powodzeniem, czy niepowodzeniem.

Dodaj następującą metodę **TriggerCustomAction** do aplikacji funkcji:

```csharp
/// <summary>
/// Triggers a custom action with some side effects.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <returns>The HTTP response result of the custom action.</returns>
public static async Task<HttpResponseMessage> TriggerCustomAction(HttpRequestMessage requestMessage)
{
    var myCustomActionRequest = await requestMessage.Content.ReadAsStringAsync();

    var actionResponse = requestMessage.CreateResponse(HttpStatusCode.OK);
    actionResponse.Content = myCustomActionRequest != string.Empty ? 
        new StringContent(JObject.Parse(myCustomActionRequest).ToString(), System.Text.Encoding.UTF8, "application/json") :
        null;
    return actionResponse;
}
```

Metoda **TriggerCustomAction** akceptuje żądanie przychodzące i po prostu odsyła odpowiedź z kodem stanu.

### <a name="create-a-custom-resource"></a>Tworzenie zasobu niestandardowego

W przypadku dostawców niestandardowych zasób niestandardowy jest tworzony za pomocą żądań PUT. Dostawca niestandardowy akceptuje treść żądania JSON, która zawiera zestaw właściwości dla niestandardowego zasobu. Zasoby na platformie Azure są zgodne z modelem RESTful. Możesz użyć tego samego adresu URL żądania, aby utworzyć, pobrać lub usunąć zasób.

Dodaj następującą metodę **CreateCustomResource** , aby utworzyć nowe zasoby:

```csharp
/// <summary>
/// Creates a custom resource and saves it to table storage.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="tableStorage">The Azure Table storage account.</param>
/// <param name="azureResourceId">The parsed Azure resource ID.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider ID.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The HTTP response containing the created custom resource.</returns>
public static async Task<HttpResponseMessage> CreateCustomResource(HttpRequestMessage requestMessage, CloudTable tableStorage, ResourceId azureResourceId, string partitionKey, string rowKey)
{
    // Adds the Azure top-level properties.
    var myCustomResource = JObject.Parse(await requestMessage.Content.ReadAsStringAsync());
    myCustomResource["name"] = azureResourceId.Name;
    myCustomResource["type"] = azureResourceId.FullResourceType;
    myCustomResource["id"] = azureResourceId.Id;

    // Save the resource into storage.
    var insertOperation = TableOperation.InsertOrReplace(
        new CustomResource
        {
            PartitionKey = partitionKey,
            RowKey = rowKey,
            Data = myCustomResource.ToString(),
        });
    await tableStorage.ExecuteAsync(insertOperation);

    var createResponse = requestMessage.CreateResponse(HttpStatusCode.OK);
    createResponse.Content = new StringContent(myCustomResource.ToString(), System.Text.Encoding.UTF8, "application/json");
    return createResponse;
}
```

Metoda **CreateCustomResource** aktualizuje przychodzące żądanie w celu uwzględnienia **identyfikatora**, **nazwy** i **typu** pól specyficznych dla platformy Azure. Te pola to właściwości najwyższego poziomu używane przez usługi na platformie Azure. Umożliwiają one współdziałanie dostawcy niestandardowego z innymi usługami, takimi jak Azure Policy, szablony Azure Resource Manager i dziennik aktywności platformy Azure.

Właściwość | Przykład | Opis
---|---|---
**Nazwij** | {myCustomResourceName} | Nazwa zasobu niestandardowego
**Wprowadź** | Microsoft. CustomProviders/resourceProviders/{resourceTypeName} | Przestrzeń nazw typu zasobu
**id** | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>dostawcy/Microsoft. CustomProviders/resourceProviders/{resourceProviderName}/<br>{resourceTypeName}/{myCustomResourceName} | Identyfikator zasobu

Oprócz dodawania właściwości, dokument JSON również został zapisany w usłudze Azure Table Storage.

### <a name="retrieve-a-custom-resource"></a>Pobieranie zasobu niestandardowego

W przypadku dostawców niestandardowych zasób niestandardowy jest pobierany za pomocą żądań GET. Dostawca niestandardowy *nie* akceptuje treści żądania JSON. W przypadku żądań GET punkt końcowy używa `x-ms-customproviders-requestpath` nagłówka do zwrócenia już utworzonego zasobu.

Dodaj następującą metodę **RetrieveCustomResource** , aby pobrać istniejące zasoby:

```csharp
/// <summary>
/// Retrieves a custom resource.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="tableStorage">The Azure Table storage account.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider ID.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The HTTP response containing the existing custom resource.</returns>
public static async Task<HttpResponseMessage> RetrieveCustomResource(HttpRequestMessage requestMessage, CloudTable tableStorage, string partitionKey, string rowKey)
{
    // Attempt to retrieve the Existing Stored Value
    var tableQuery = TableOperation.Retrieve<CustomResource>(partitionKey, rowKey);
    var existingCustomResource = (CustomResource)(await tableStorage.ExecuteAsync(tableQuery)).Result;

    var retrieveResponse = requestMessage.CreateResponse(
        existingCustomResource != null ? HttpStatusCode.OK : HttpStatusCode.NotFound);

    retrieveResponse.Content = existingCustomResource != null ?
            new StringContent(existingCustomResource.Data, System.Text.Encoding.UTF8, "application/json"):
            null;
    return retrieveResponse;
}
```

Na platformie Azure zasoby są zgodne z modelem RESTful. Adres URL żądania, który tworzy zasób, również zwraca zasób w przypadku wykonania żądania GET.

### <a name="remove-a-custom-resource"></a>Usuwanie zasobu niestandardowego

W przypadku dostawców niestandardowych zasób niestandardowy jest usuwany przez żądania DELETE. Dostawca niestandardowy *nie* akceptuje treści żądania JSON. W przypadku żądania usunięcia punkt końcowy używa `x-ms-customproviders-requestpath` nagłówka do usunięcia już utworzonego zasobu.

Dodaj następującą metodę **RemoveCustomResource** , aby usunąć istniejące zasoby:

```csharp
/// <summary>
/// Removes an existing custom resource.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="tableStorage">The Azure storage account table.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider ID.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The HTTP response containing the result of the deletion.</returns>
public static async Task<HttpResponseMessage> RemoveCustomResource(HttpRequestMessage requestMessage, CloudTable tableStorage, string partitionKey, string rowKey)
{
    // Attempt to retrieve the Existing Stored Value
    var tableQuery = TableOperation.Retrieve<CustomResource>(partitionKey, rowKey);
    var existingCustomResource = (CustomResource)(await tableStorage.ExecuteAsync(tableQuery)).Result;

    if (existingCustomResource != null) {
        var deleteOperation = TableOperation.Delete(existingCustomResource);
        await tableStorage.ExecuteAsync(deleteOperation);
    }

    return requestMessage.CreateResponse(
        existingCustomResource != null ? HttpStatusCode.OK : HttpStatusCode.NoContent);
}
```

Na platformie Azure zasoby są zgodne z modelem RESTful. Adres URL żądania, który tworzy zasób, również usuwa zasób, jeśli jest wykonywane żądanie DELETE.

### <a name="list-all-custom-resources"></a>Wyświetl listę wszystkich zasobów niestandardowych

W przypadku dostawców niestandardowych można wyliczyć listę istniejących zasobów niestandardowych przy użyciu żądań pobrania kolekcji. Dostawca niestandardowy *nie* akceptuje treści żądania JSON. W przypadku kolekcji żądań GET punkt końcowy używa `x-ms-customproviders-requestpath` nagłówka do wyliczenia już utworzonych zasobów.

Dodaj następującą metodę **EnumerateAllCustomResources** , aby wyliczyć istniejące zasoby:

```csharp
/// <summary>
/// Enumerates all the stored custom resources for a given type.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="tableStorage">The Azure Table storage account.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider ID.</param>
/// <param name="resourceType">The resource type of the enumeration.</param>
/// <returns>The HTTP response containing a list of resources stored under 'value'.</returns>
public static async Task<HttpResponseMessage> EnumerateAllCustomResources(HttpRequestMessage requestMessage, CloudTable tableStorage, string partitionKey, string resourceType)
{
    // Generate upper bound of the query.
    var rowKeyUpperBound = new StringBuilder(resourceType);
    rowKeyUpperBound[rowKeyUpperBound.Length - 1]++;

    // Create the enumeration query.
    var enumerationQuery = new TableQuery<CustomResource>().Where(
        TableQuery.CombineFilters(
            TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, partitionKey),
            TableOperators.And,
            TableQuery.CombineFilters(
                TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, resourceType),
                TableOperators.And,
                TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, rowKeyUpperBound.ToString()))));
    
    var customResources = (await tableStorage.ExecuteQuerySegmentedAsync(enumerationQuery, null))
        .ToList().Select(customResource => JToken.Parse(customResource.Data));

    var enumerationResponse = requestMessage.CreateResponse(HttpStatusCode.OK);
    enumerationResponse.Content = new StringContent(new JObject(new JProperty("value", customResources)).ToString(), System.Text.Encoding.UTF8, "application/json");
    return enumerationResponse;
}
```

> [!NOTE]
> RowKey QueryComparisons. GreaterThan i QueryComparisons. LessThan jest składnią usługi Azure Table Storage, aby wykonać zapytanie "StartsWith" dotyczące ciągów.

Aby wyświetlić listę wszystkich istniejących zasobów, wygeneruj zapytanie usługi Azure Table Storage, które zapewnia, że zasoby istnieją w niestandardowej partycji dostawcy. Następnie zapytanie sprawdza, czy klucz wiersza zaczyna się od tej samej `{myResourceType}` wartości.

## <a name="integrate-restful-operations"></a>Integrowanie operacji RESTful

Po dodaniu wszystkich metod RESTful do aplikacji funkcji należy zaktualizować metodę **uruchomieniową** Main, która wywołuje funkcje do obsługi różnych żądań REST:

```csharp
/// <summary>
/// Entry point for the Azure function app webhook that acts as the service behind a custom provider.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="log">The logger.</param>
/// <param name="tableStorage">The Azure Table storage account.</param>
/// <returns>The HTTP response for the custom Azure API.</returns>
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger log, CloudTable tableStorage)
{
    // Get the unique Azure request path from request headers.
    var requestPath = req.Headers.GetValues("x-ms-customproviders-requestpath").FirstOrDefault();

    if (requestPath == null)
    {
        var missingHeaderResponse = req.CreateResponse(HttpStatusCode.BadRequest);
        missingHeaderResponse.Content = new StringContent(
            new JObject(new JProperty("error", "missing 'x-ms-customproviders-requestpath' header")).ToString(),
            System.Text.Encoding.UTF8, 
            "application/json");
    }

    log.LogInformation($"The Custom Provider Function received a request '{req.Method}' for resource '{requestPath}'.");

    // Determines if it is a collection level call or action.
    var isResourceRequest = requestPath.Split('/').Length % 2 == 1;
    var azureResourceId = isResourceRequest ? 
        ResourceId.FromString(requestPath) :
        ResourceId.FromString($"{requestPath}/");

    // Create the Partition Key and Row Key
    var partitionKey = $"{azureResourceId.SubscriptionId}:{azureResourceId.ResourceGroupName}:{azureResourceId.Parent.Name}";
    var rowKey = $"{azureResourceId.FullResourceType.Replace('/', ':')}:{azureResourceId.Name}";

    switch (req.Method)
    {
        // Action request for a custom action.
        case HttpMethod m when m == HttpMethod.Post && !isResourceRequest:
            return await TriggerCustomAction(
                requestMessage: req);

        // Enumerate request for all custom resources.
        case HttpMethod m when m == HttpMethod.Get && !isResourceRequest:
            return await EnumerateAllCustomResources(
                requestMessage: req,
                tableStorage: tableStorage,
                partitionKey: partitionKey,
                resourceType: rowKey);

        // Retrieve request for a custom resource.
        case HttpMethod m when m == HttpMethod.Get && isResourceRequest:
            return await RetrieveCustomResource(
                requestMessage: req,
                tableStorage: tableStorage,
                partitionKey: partitionKey,
                rowKey: rowKey);

        // Create request for a custom resource.
        case HttpMethod m when m == HttpMethod.Put && isResourceRequest:
            return await CreateCustomResource(
                requestMessage: req,
                tableStorage: tableStorage,
                azureResourceId: azureResourceId,
                partitionKey: partitionKey,
                rowKey: rowKey);

        // Remove request for a custom resource.
        case HttpMethod m when m == HttpMethod.Delete && isResourceRequest:
            return await RemoveCustomResource(
                requestMessage: req,
                tableStorage: tableStorage,
                partitionKey: partitionKey,
                rowKey: rowKey);

        // Invalid request received.
        default:
            return req.CreateResponse(HttpStatusCode.BadRequest);
    }
}
```

Zaktualizowana Metoda **Run** obejmuje teraz powiązanie danych wejściowych *TableStorage* dodane do usługi Azure Table Storage. Pierwsza część metody odczytuje `x-ms-customproviders-requestpath` Nagłówek i używa `Microsoft.Azure.Management.ResourceManager.Fluent` biblioteki, aby przeanalizować wartość jako identyfikator zasobu. `x-ms-customproviders-requestpath`Nagłówek jest wysyłany przez niestandardowego dostawcę i określa ścieżkę żądania przychodzącego.

Korzystając z przeanalizowanego identyfikatora zasobu, można wygenerować wartości **partitionKey** i **rowKey** dla danych w celu wyszukania lub zapisania zasobów niestandardowych.

Po dodaniu metod i klas należy zaktualizować metody **using** dla aplikacji funkcji. Dodaj następujący kod na początku pliku C#:

```csharp
#r "Newtonsoft.Json"
#r "Microsoft.WindowsAzure.Storage"
#r "../bin/Microsoft.Azure.Management.ResourceManager.Fluent"

using System;
using System.Net;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Configuration;
using System.Text;
using System.Threading;
using System.Globalization;
using System.Collections.Generic;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.WindowsAzure.Storage.Table;
using Microsoft.Azure.Management.ResourceManager.Fluent.Core;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
```

Jeśli w dowolnym momencie tego samouczka zostanie utracona, można znaleźć pełny przykład kodu w [odsyłaczu do punktu końcowego RESTful języka C# dostawcy niestandardowego](./reference-custom-providers-csharp-endpoint.md). Po zakończeniu działania aplikacji funkcji Zapisz adres URL aplikacji funkcji. Może służyć do wyzwalania aplikacji funkcji w kolejnych samouczkach.

## <a name="next-steps"></a>Następne kroki

W tym artykule opisano tworzenie punktu końcowego RESTful do pracy z punktem końcowym niestandardowego dostawcy platformy Azure. Aby dowiedzieć się, jak utworzyć dostawcę niestandardowego, przejdź do samouczka dotyczącego artykułu [: Tworzenie niestandardowego dostawcy](./tutorial-custom-providers-create.md).
