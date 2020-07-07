---
title: Zarządzanie zasobami w Azure Media Services
titleSuffix: Azure Media Services
description: Zasób, w którym można wprowadzać multimedia (na przykład za pośrednictwem przekazywania lub pozyskiwania na żywo), nośnik wyjściowy (z danych wyjściowych zadania) i publikować multimedia z (do przesyłania strumieniowego). Ten temat przedstawia przegląd sposobu tworzenia nowego zasobu i przekazywania plików.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/26/2020
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 9136fd702fad5c12a8ec97a68ff8a592a203d7d2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "80582194"
---
# <a name="manage-assets"></a>Zarządzanie elementami zawartości

W Azure Media Services [zasób](https://docs.microsoft.com/rest/api/media/assets) jest tam, gdzie 

* Przekaż pliki multimedialne do zasobu,
* Pozyskiwanie i archiwizowanie strumieni na żywo w elemencie zawartości
* wyprowadza wyniki kodowania zadania analizy do elementu zawartości.
* Publikowanie multimediów na potrzeby przesyłania strumieniowego 
* Pobierz pliki z elementu zawartości.

Ten temat zawiera omówienie sposobu przekazywania plików do zasobu i wykonywania innych typowych operacji. Zawiera również linki do przykładów kodu i powiązanych tematów.

## <a name="prerequisite"></a>Wymaganie wstępne 

Przed rozpoczęciem opracowywania, przejrzyj:

* [Pojęcia](concepts-overview.md)
* [Programowanie przy użyciu interfejsów api Media Services v3](media-services-apis-overview.md) (zawiera informacje na temat uzyskiwania dostępu do interfejsów API, konwencji nazewnictwa itd.) 

## <a name="upload-media-files-into-an-asset"></a>Przekazywanie plików multimedialnych do zasobu

Po przekazaniu plików cyfrowych do magazynu i skojarzeniu ich z elementem zawartości można używać ich w Media Services kodowania, przesyłania strumieniowego i analizowania przepływów danych. Jednym z typowych przepływów pracy Media Services jest przekazywanie, kodowanie i przesyłanie strumieniowe pliku. Ta sekcja zawiera opis ogólnych kroków.

1. Użyj interfejsu API usługi Media Services w wersji 3, aby utworzyć nowy element zawartości typu „input”. Ta operacja polega na utworzeniu kontenera na koncie magazynu skojarzonym z kontem usługi Media Services. Interfejs API zwraca nazwę kontenera (na przykład `"container": "asset-b8d8b68a-2d7f-4d8c-81bb-8c7bbbe67ee4"` ).

    Jeśli masz już kontener obiektów blob, który chcesz skojarzyć z elementem zawartości, możesz określić nazwę kontenera podczas tworzenia elementu zawartości. Usługa Media Services aktualnie obsługuje tylko obiekty blob w katalogu głównym kontenera, a nie w obrębie ścieżek w nazwie pliku. W związku z tym kontener z nazwą pliku „input.mp4” będzie działać. Jednak kontener z nazwą pliku "Video/Inputs/input.mp4" nie będzie działał.

    Za pomocą wiersza polecenia platformy Azure można przekazywać pliki bezpośrednio do dowolnego konta magazynu i kontenera, do których masz uprawnienia w subskrypcji.

    Nazwa kontenera musi być unikatowa i zgodna ze wskazówkami dotyczącymi nazewnictwa magazynu. Nazwa nie musi być zgodna z formatowaniem nazwy kontenera elementów zawartości usługi Media Services (Asset-GUID).

    ```azurecli
    az storage blob upload -f /path/to/file -c MyContainer -n MyBlob
    ```
2. Pobierz adres URL sygnatury dostępu współdzielonego z uprawnieniami odczytu/zapisu, który będzie używany do przekazywania plików cyfrowych do kontenera elementów zawartości.

    W celu [utworzenia listy adresów URL kontenerów elementów zawartości](https://docs.microsoft.com/rest/api/media/assets/listcontainersas) można użyć interfejsu API usługi Media Services.

    **AssetContainerSas. listContainerSas** przyjmuje parametr [ListContainerSasInput](https://docs.microsoft.com/rest/api/media/assets/listcontainersas#listcontainersasinput) , który został ustawiony `expiryTime` . Czas powinien być ustawiony na < 24 godziny.

    [ListContainerSasInput](https://docs.microsoft.com/rest/api/media/assets/listcontainersas#listcontainersasinput) zwraca wiele adresów URL sygnatury dostępu współdzielonego, ponieważ istnieją dwa klucze konta magazynu dla każdego konta magazynu. Konto magazynu ma dwa klucze, ponieważ ułatwia pracę w trybie failover i bezproblemowe rotację kluczy konta magazynu. Pierwszy adres URL sygnatury dostępu współdzielonego reprezentuje pierwszy klucz konta magazynu, a drugi adres URL sygnatury dostępu współdzielonego reprezentuje drugi klucz.
3. Użyj interfejsów API usługi Azure Storage lub zestawów SDK (na przykład [interfejsu API REST magazynu](../../storage/common/storage-rest-api-auth.md) lub [zestawu SDK platformy .NET](../../storage/blobs/storage-quickstart-blobs-dotnet.md)) do przekazywania plików do kontenera zasobów.
4. W celu utworzenia przekształcenia i zadania przetwarzającego element zawartości „input” należy użyć interfejsów API usługi Media Services w wersji 3. Aby uzyskać więcej informacji, zobacz [Przekształcenia i zadania](transform-concept.md).
5. Przesyłaj strumieniowo zawartość z zasobu "output".

### <a name="create-a-new-asset"></a>Tworzenie nowego elementu zawartości

> [!NOTE]
> Właściwości elementu zawartości typu datetime są zawsze w formacie UTC.

#### <a name="rest"></a>REST

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{amsAccountName}/assets/{assetName}?api-version=2018-07-01
```

Aby zapoznać się z przykładem, zobacz [Tworzenie zasobu z](https://docs.microsoft.com/rest/api/media/assets/createorupdate#examples) przykładem Rest.

W przykładzie pokazano, jak utworzyć **treść żądania** , gdzie można określić opis, nazwę kontenera, konto magazynu i inne przydatne informacje.

#### <a name="curl"></a>cURL

```cURL
curl -X PUT \
  'https://management.azure.com/subscriptions/00000000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Media/mediaServices/amsAccountName/assets/myOutputAsset?api-version=2018-07-01' \
  -H 'Accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
  "properties": {
    "description": "",
  }
}'
```

#### <a name="net"></a>.NET

```csharp
 Asset asset = await client.Assets.CreateOrUpdateAsync(resourceGroupName, accountName, assetName, new Asset());
```

### <a name="see-also"></a>Zobacz także

* [Tworzenie danych wejściowych zadania z pliku lokalnego](job-input-from-local-file-how-to.md)
* [Utwórz dane wejściowe zadania na podstawie adresu URL HTTPS](job-input-from-http-how-to.md)

## <a name="ingest-and-archive-live-streams-into-an-asset"></a>Pozyskiwanie i archiwizowanie strumieni na żywo w elemencie zawartości

W Media Services obiekt [danych wyjściowych na żywo](https://docs.microsoft.com/rest/api/media/liveoutputs) przypomina cyfrowy rejestrator wideo, który będzie przechwytywać i rejestrować strumień na żywo do zasobu na koncie Media Services. Zarejestrowana zawartość jest zachowywana w kontenerze zdefiniowanym [przez zasób zasobu](https://docs.microsoft.com/rest/api/media/assets) .

Aby uzyskać więcej informacji, zobacz:

* [Korzystanie z funkcji DVR w chmurze](live-event-cloud-dvr.md)
* [Live — samouczek dotyczący przesyłania strumieniowego](stream-live-tutorial-with-api.md)

## <a name="output-the-results-of-a-job-to-an-asset"></a>Wyprowadzanie wyników zadania do zasobu

W Media Services, podczas przetwarzania wideo (na przykład kodowania lub analizowania) należy utworzyć [zasób](assets-concept.md) wyjściowy do przechowywania wyniku [zadania](transforms-jobs-concept.md).

Aby uzyskać więcej informacji, zobacz:

* [Kodowanie wideo](encoding-concept.md)
* [Tworzenie danych wejściowych zadania z pliku lokalnego](job-input-from-local-file-how-to.md)

## <a name="publish-an-asset-for-streaming"></a>Publikowanie elementu zawartości na potrzeby przesyłania strumieniowego

Aby opublikować element zawartości na potrzeby przesyłania strumieniowego, należy utworzyć [lokalizator przesyłania strumieniowego](streaming-locators-concept.md). Lokalizator przesyłania strumieniowego musi znać nazwę zasobu, który ma zostać opublikowany. 

Aby uzyskać więcej informacji, zobacz:

[Samouczek: przekazywanie, kodowanie i przesyłanie strumieniowe filmów wideo z Media Services v3](stream-files-tutorial-with-api.md)

## <a name="download-results-of-a-job-from-an-output-asset"></a>Pobierz wyniki zadania z wyjściowego elementu zawartości

Następnie można pobrać te wyniki zadania do folderu lokalnego przy użyciu usługi multimediów i interfejsów API magazynu. 

Zobacz przykład [plików do pobrania](download-results-howto.md) .

## <a name="filtering-ordering-paging"></a>Filtrowanie, porządkowanie, stronicowanie

Zobacz [filtrowanie, porządkowanie, stronicowanie jednostek Media Services](entities-overview.md).

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z pełnymi przykładami kodu, które demonstrują przekazywanie, kodowanie, analizowanie, przesyłanie strumieniowe na żywo i na żądanie: 

* [Java](https://docs.microsoft.com/samples/azure-samples/media-services-v3-java/azure-media-services-v3-samples-using-java/), 
* [.NET](https://docs.microsoft.com/samples/azure-samples/media-services-v3-dotnet/azure-media-services-v3-samples-using-net/), 
* [Reszta](https://docs.microsoft.com/samples/azure-samples/media-services-v3-rest-postman/azure-media-services-postman-collection/).
