---
title: Filtrowanie, porządkowanie i stronicowanie jednostek
description: Dowiedz się więcej na temat filtrowania, porządkowania i stronicowania jednostek Azure Media Services v3.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: overview
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 19febc833f087afdfecde3274a1044598d082521
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98955313"
---
# <a name="filtering-ordering-and-paging-of-media-services-entities"></a>Filtrowanie, porządkowanie i stronicowanie jednostek Media Services

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

W tym temacie omówiono opcje zapytania OData i obsługa podziału na strony dostępne podczas wyświetlania listy jednostek Azure Media Services v3.

## <a name="considerations"></a>Zagadnienia do rozważenia

* Właściwości jednostek, które są typu, `Datetime` są zawsze w formacie UTC.
* Biały znak w ciągu zapytania powinien być kodowany przy użyciu adresu URL przed wysłaniem żądania.

## <a name="comparison-operators"></a>Operatory porównania

Aby porównać pole z wartością stałą, można użyć następujących operatorów:

Operatory równości:

- `eq`: Sprawdź, czy pole jest *równe* stałej wartości.
- `ne`: Sprawdź, czy pole *nie jest równe* wartości stałej.

Operatory zakresu:

- `gt`: Sprawdź, czy pole jest *większe niż* wartość stała.
- `lt`: Sprawdź, czy pole jest *mniejsze niż* wartość stała.
- `ge`: Sprawdź, czy pole jest *większe niż lub równe* wartości stałej.
- `le`: Sprawdź, czy pole jest *mniejsze niż lub równe* wartości stałej.

## <a name="filter"></a>Filtr

Użyj `$filter` , aby podać parametr filtru OData, aby znaleźć tylko te obiekty, które Cię interesują.

Poniższy przykład zarest filtruje `alternateId` wartość elementu zawartości:

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$filter=properties/alternateId%20eq%20'unique identifier'
```

W poniższym przykładzie w języku C# filtry dotyczące daty utworzenia zasobu:

```csharp
var odataQuery = new ODataQuery<Asset>("properties/created lt 2018-05-11T17:39:08.387Z");
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName, odataQuery);
```

## <a name="order-by"></a>Porządkuj według

Służy `$orderby` do sortowania zwracanych obiektów przez określony parametr. Na przykład:  

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01$orderby=properties/created%20gt%202018-05-11T17:39:08.387Z
```

Aby posortować wyniki w kolejności rosnącej lub malejącej, Dołącz `asc` albo `desc` do nazwy pola, oddzielone spacją. Na przykład: `$orderby properties/created desc`.

## <a name="skip-token"></a>Pomiń token

Jeśli odpowiedź na zapytanie zawiera wiele elementów, usługa zwraca `$skiptoken` `@odata.nextLink` wartość (), która jest używana do uzyskania następnej strony wyników. Użyj go do strony za pomocą całego zestawu wyników.

W Media Services V3 nie można skonfigurować rozmiaru strony. Rozmiar strony jest różny w zależności od typu jednostki. Przeczytaj poszczególne sekcje, które obserwują, aby uzyskać szczegółowe informacje.

Jeśli obiekty są tworzone lub usuwane podczas stronicowania w kolekcji, zmiany zostaną odzwierciedlone w zwracanych wynikach (jeśli te zmiany znajdują się w części kolekcji, która nie została pobrana).

> [!TIP]
> Zawsze używaj `nextLink` do wyliczania kolekcji i nie zależą od określonego rozmiaru strony.
>
> `nextLink`Wartość będzie obecna tylko wtedy, gdy istnieje więcej niż jedna strona jednostek.

Rozważmy następujący przykład `$skiptoken` użycia. Upewnij się, że zastąpisz *amstestaccount* z nazwą konta i ustawisz wartość *interfejsu API-Version* w najnowszej wersji.

W przypadku żądania listy zasobów takich jak:

```
GET  https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01 HTTP/1.1
x-ms-client-request-id: dd57fe5d-f3be-4724-8553-4ceb1dbe5aab
Content-Type: application/json; charset=utf-8
```

Otrzymasz odpowiedź podobną do tej:

```
HTTP/1.1 200 OK

{
"value":[
{
"name":"Asset 0","id":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 0","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-0000-0000-0000-000000000000","created":"2018-12-11T22:12:44.98Z","lastModified":"2018-12-11T22:15:48.003Z","container":"asset-00000000-0000-0000-0000-0000000000000","storageAccountName":"amsacctname","storageEncryptionFormat":"None"
}
},
// lots more assets
{
"name":"Asset 517","id":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 517","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-0000-0000-0000-000000000000","created":"2018-12-11T22:14:08.473Z","lastModified":"2018-12-11T22:19:29.657Z","container":"asset-00000000-0000-0000-0000-000000000000","storageAccountName":"amsacctname","storageEncryptionFormat":"None"
}
}
],"@odata.nextLink":"https:// management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517"
}
```

Następnie Zażądaj następnej strony, wysyłając żądanie Get dla:

```
https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517
```

W poniższym przykładzie w języku C# pokazano, jak wyliczyć wszystkie lokalizatory przesyłania strumieniowego na koncie.

```csharp
var firstPage = await MediaServicesArmClient.StreamingLocators.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.StreamingLocators.ListNextAsync(currentPage.NextPageLink);
}
```

## <a name="using-logical-operators-to-combine-query-options"></a>Używanie operatorów logicznych do łączenia opcji zapytania

Media Services v3 obsługuje operatory logiczne **lub** i **i** . 

Poniższy przykład REST sprawdza stan zadania:

```
https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/qbtest/providers/Microsoft.Media/mediaServices/qbtest/transforms/VideoAnalyzerTransform/jobs?$filter=properties/state%20eq%20Microsoft.Media.JobState'Scheduled'%20or%20properties/state%20eq%20Microsoft.Media.JobState'Processing'&api-version=2018-07-01
```

To samo zapytanie jest konstruowane w języku C# w następujący sposób: 

```csharp
var odataQuery = new ODataQuery<Job>("properties/state eq Microsoft.Media.JobState'Scheduled' or properties/state eq Microsoft.Media.JobState'Processing'");
client.Jobs.List(config.ResourceGroup, config.AccountName, VideoAnalyzerTransformName, odataQuery);
```

## <a name="filtering-and-ordering-options-of-entities"></a>Opcje filtrowania i porządkowania jednostek

W poniższej tabeli przedstawiono sposób stosowania opcji filtrowania i porządkowania do różnych jednostek:

|Nazwa jednostki|Nazwa właściwości|Filtr|Zamówienie|
|---|---|---|---|
|[Elementy zawartości](/rest/api/media/assets/)|name|`eq`, `gt`, `lt`, `ge`, `le`|`asc` i `desc`|
||Właściwości. alternateId |`eq`||
||Właściwości. assetId |`eq`||
||Właściwości. utworzone| `eq`, `gt`, `lt`| `asc` i `desc`|
|[Zasady kluczy zawartości](/rest/api/media/contentkeypolicies)|name|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` i `desc`|
||Właściwości. utworzone    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` i `desc`|
||Properties. Description    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`||
||Właściwości. lastModified|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` i `desc`|
||Właściwości. policyId|`eq`, `ne`||
|[Zadania](/rest/api/media/jobs)| name  | `eq`            | `asc` i `desc`|
||Properties. State        | `eq`, `ne`        |                         |
||Właściwości. utworzone      | `gt`, `ge`, `lt`, `le`| `asc` i `desc`|
||Właściwości. lastModified | `gt`, `ge`, `lt`, `le` | `asc` i `desc`| 
|[Lokalizatory przesyłania strumieniowego](/rest/api/media/streaminglocators)|name|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` i `desc`|
||Właściwości. utworzone    |`eq`, `ne`, `ge`, `le`,  `gt`, `lt`|`asc` i `desc`|
||Właściwości. endTime    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` i `desc`|
|[Zasady przesyłania strumieniowego](/rest/api/media/streamingpolicies)|name|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` i `desc`|
||Właściwości. utworzone    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` i `desc`|
|[Przekształcenia](/rest/api/media/transforms)| name | `eq`            | `asc` i `desc`|
|| Właściwości. utworzone      | `gt`, `ge`, `lt`, `le`| `asc` i `desc`|
|| Właściwości. lastModified | `gt`, `ge`, `lt`, `le`| `asc` i `desc`|

## <a name="next-steps"></a>Następne kroki

* [Wyświetl listę zasobów](/rest/api/media/assets/list)
* [Wyświetl listę zasad dotyczących kluczy zawartości](/rest/api/media/contentkeypolicies/list)
* [Wyświetl listę zadań](/rest/api/media/jobs/list)
* [Wyświetlanie listy zasad przesyłania strumieniowego](/rest/api/media/streamingpolicies/list)
* [Wyświetlanie listy lokalizatorów przesyłania strumieniowego](/rest/api/media/streaminglocators/list)
* [Strumieniowe przesyłanie pliku](stream-files-dotnet-quickstart.md)
* [Limity przydziału i ograniczenia](limits-quotas-constraints.md)
