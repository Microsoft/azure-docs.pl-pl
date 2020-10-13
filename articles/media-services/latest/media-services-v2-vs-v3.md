---
title: Migrowanie z Azure Media Services V2 do wersji v3
description: W tym artykule opisano zmiany wprowadzone w Azure Media Services v3 i przedstawiono różnice między dwiema wersjami.
services: media-services
documentationcenter: na
author: IngridAtMicrosoft
manager: femila
editor: ''
tags: ''
keywords: azure media services, stream, broadcast, live, offline
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 10/01/2020
ms.author: inhenkel
ms.openlocfilehash: 515379a4207a582b441d132b1c28ff11bc83c714
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91651756"
---
# <a name="media-services-v2-vs-v3"></a>Media Services V2 a v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

W tym artykule opisano zmiany wprowadzone w Azure Media Services v3 i przedstawiono różnice między dwiema wersjami.

## <a name="general-changes-from-v2"></a>Ogólne zmiany z wersji 2

* Aby uzyskać zmiany dotyczące zasobów, zobacz sekcję [zmiany dotyczące zasobów](#asset-specific-changes) , która znajduje się poniżej.
* Zestawy SDK V3 są teraz rozłączone z ZESTAWem danych magazynu, co zapewnia większą kontrolę nad wersją zestawu SDK magazynu, którego chcesz używać, oraz pozwala uniknąć problemów z przechowywaniem wersji. 
* W przypadku interfejsów API V3 wszystkie stawki bitów kodowania są w bitach na sekundę. Różni się to od ustawień wstępnych w wersji 2 Media Encoder Standard. Na przykład szybkość transmisji bitów w v2 zostanie określona jako 128 (KB/s), ale w wersji 3 będzie 128000 (bity/s). 
* Jednostki AssetFiles, AccessPolicies i IngestManifests nie istnieją w wersji 3.
* ContentKeys nie jest już jednostką, jest teraz właściwością lokalizatora przesyłania strumieniowego.
* Obsługa Event Grid zastępuje NotificationEndpoints.
* Zmieniono nazwy następujących jednostek:

   * V3 JobOutput zastępuje zadanie w wersji 2 i jest teraz częścią zadania. Dane wejściowe i wyjściowe są teraz na poziomie zadania. Aby uzyskać więcej informacji, zobacz [Tworzenie danych wejściowych zadania z pliku lokalnego](job-input-from-local-file-how-to.md). 

       Aby uzyskać historię postępu zadania, nasłuchiwanie zdarzeń EventGrid. Aby uzyskać więcej informacji, zobacz [Obsługa zdarzeń Event Grid](reacting-to-media-services-events.md).
    * Lokalizator przesyłania strumieniowego zastępuje lokalizator.
    * Wydarzenie na żywo zastępuje kanał.<br/>Rozliczenia wydarzeń na żywo opierają się na licznikach kanału na żywo. Aby uzyskać więcej informacji, zobacz temat [rozliczenia](live-event-states-billing.md) i [Cennik](https://azure.microsoft.com/pricing/details/media-services/).
    * Wyjście na żywo zastępuje program.
* Dane wyjściowe na żywo są uruchamiane w momencie utworzenia i zatrzymywane podczas usuwania. Programy pracowały inaczej w interfejsach API v2, musiały zostać uruchomione po utworzeniu.
* Aby uzyskać informacje o zadaniu, należy znać nazwę przekształcenia, w której utworzono zadanie. 
* W wersji 2 pliki metadanych [danych wejściowych](../previous/media-services-input-metadata-schema.md) i [wyjściowych](../previous/media-services-output-metadata-schema.md) XML są generowane w wyniku zadania kodowania. W wersji 3 format metadanych został zmieniony z XML na JSON. 
* W Media Services V2 można określić wektor inicjalizacji (IV). W Media Services V3 nie można określić FairPlay IV. Chociaż nie ma to wpływu na klientów korzystających z Media Services w przypadku dostarczania pakietów i licencji, może to być problem w przypadku korzystania z systemu DRM innej firmy w celu dostarczenia licencji FairPlay (Tryb hybrydowy). W takim przypadku ważne jest, aby wiedzieć, że FairPlay IV pochodzi od identyfikatora klucza cbcs i można go pobrać za pomocą tej formuły:

    ```
    string cbcsIV =  Convert.ToBase64String(HexStringToByteArray(cbcsGuid.ToString().Replace("-", string.Empty)));
    ```

    with

    ``` 
    public static byte[] HexStringToByteArray(string hex)
    {
        return Enumerable.Range(0, hex.Length)
            .Where(x => x % 2 == 0)
            .Select(x => Convert.ToByte(hex.Substring(x, 2), 16))
            .ToArray();
    }
    ```

    Aby uzyskać więcej informacji, zobacz [Azure Functions kodu C# dla Media Services V3 w trybie hybrydowym dla operacji Live i VOD](https://github.com/Azure-Samples/media-services-v3-dotnet-core-functions-integration/tree/master/LiveAndVodDRMOperationsV3).
 
> [!NOTE]
> Zapoznaj się z konwencjami nazewnictwa, które są stosowane do [zasobów Media Services v3](media-services-apis-overview.md#naming-conventions). Przejrzyj również [nazwy obiektów BLOB](assets-concept.md#naming).

## <a name="feature-gaps-with-respect-to-v2-apis"></a>Brakujące funkcje w porównaniu z interfejsami API w wersji 2

Interfejs API v3 ma następujące luki w odniesieniu do interfejsu API w wersji 2. Zamknięcie przerw jest w toku.

* [Koder w warstwie Premium](../previous/media-services-premium-workflow-encoder-formats.md) i starsze [procesory analizy multimediów](../previous/media-services-analytics-overview.md) (Azure Media Services Indexer 2 — wersja zapoznawcza, Front redactor itp.) nie są dostępne za pośrednictwem wersji 3.<br/>Klienci, którzy chcą przeprowadzić migrację z Media Indexer 1 lub 2 wersji zapoznawczej, mogą natychmiast użyć ustawień wstępnych AudioAnalyzer w interfejsie API v3.  To nowe ustawienie wstępne zawiera więcej funkcji niż starszy Media Indexer 1 lub 2. 
* Wiele [zaawansowanych funkcji Media Encoder standard w](../previous/media-services-advanced-encoding-with-mes.md) interfejsie API v2 nie są obecnie dostępne w wersji 3, na przykład:
  
    * Łączenie elementów zawartości
    * Nakładki
    * Przycinanie
    * Miniaturowe sprites
    * Wstawianie dyskretnej ścieżki audio, gdy dane wejściowe nie zawierają dźwięku
    * Wstawianie ścieżki wideo, gdy dane wejściowe nie zawierają wideo
* Zdarzenia na żywo z transkodowaniem obecnie nie obsługują wstawiania poza strumień i znacznik usługi AD za pośrednictwem wywołania interfejsu API. 
* Zapoznaj się z `https://github.com/Azure-Samples/media-services-v2-dotnet-core-restsharp-sample.git` przykładowym kodem, aby uzyskać najlepsze rozwiązania i wzorce dotyczące korzystania z interfejsu API REST w wersji 2. Rdzeń SDK.

## <a name="asset-specific-changes"></a>Zmiany dotyczące zasobów

* W przypadku zasobów utworzonych w wersji 3 Media Services obsługuje tylko [szyfrowanie magazynu po stronie serwera usługi Azure Storage](../../storage/common/storage-service-encryption.md).
    * Można używać interfejsów API v3 z zasobami utworzonymi przy użyciu interfejsów API v2, które mają [szyfrowanie magazynu](../previous/media-services-rest-storage-encryption.md) (AES 256) zapewniane przez Media Services.
    * Nie można tworzyć nowych zasobów przy użyciu starszej wersji [szyfrowania pamięci](../previous/media-services-rest-storage-encryption.md) AES 256 z użyciem interfejsów API v3.
* Właściwości [zasobu](assets-concept.md)w wersji 3 różnią się od v2, zobacz [jak mapa właściwości](#map-v3-asset-properties-to-v2).
* Właściwość IAsset. ParentAssets nie istnieje w wersji 3.

### <a name="map-v3-asset-properties-to-v2"></a>Mapuj właściwości zasobu v3 do wersji 2

W poniższej tabeli przedstawiono sposób, w jaki właściwości [zasobu](/rest/api/media/assets/createorupdate#asset)w wersji v3 mapują się na właściwości zasobu w 2.

|Właściwości v3|Właściwości v2|
|---|---|
|`id` -(unikatowy) pełna ścieżka Azure Resource Manager, zobacz przykłady w elemencie [zawartości](/rest/api/media/assets/createorupdate)||
|`name` -(unikatowy) zobacz [konwencje nazewnictwa](media-services-apis-overview.md#naming-conventions) ||
|`alternateId`|`AlternateId`|
|`assetId`|`Id` -(unikatowa) wartość zaczyna się od `nb:cid:UUID:` prefiksu.|
|`created`|`Created`|
|`description`|`Name`|
|`lastModified`|`LastModified`|
|`storageAccountName`|`StorageAccountName`|
|`storageEncryptionFormat`| `Options` (opcje tworzenia)|
|`type`||

### <a name="storage-side-encryption"></a>Szyfrowanie po stronie magazynu

Aby chronić zasoby w spoczynku, zasoby powinny być szyfrowane przez szyfrowanie po stronie magazynu. W poniższej tabeli przedstawiono, w jaki sposób szyfrowanie po stronie magazynu działa w Media Services:

|Opcja szyfrowania|Opis|Media Services v2|Media Services v3|
|---|---|---|---|
|Media Services szyfrowanie magazynu|Szyfrowanie AES-256, klucz zarządzany przez Media Services.|Obsługiwane<sup>(1)</sup>|Nieobsługiwane<sup>(2)</sup>|
|[szyfrowanie usługi Storage danych magazynowanych](../../storage/common/storage-service-encryption.md)|Szyfrowanie po stronie serwera oferowane przez usługę Azure Storage, klucz zarządzany przez platformę Azure lub przez klienta.|Obsługiwane|Obsługiwane|
|[Szyfrowanie Client-Side magazynu](../../storage/common/storage-client-side-encryption.md)|Szyfrowanie po stronie klienta oferowane przez usługę Azure Storage, klucz zarządzany przez klienta w Key Vault.|Nieobsługiwane|Nieobsługiwane|

<sup>1</sup> , gdy Media Services obsługuje obsługę zawartości w trybie Wyczyść/bez żadnej formy szyfrowania, nie jest to zalecane.

<sup>2</sup> w Media Services v3, szyfrowanie magazynu (szyfrowanie AES-256) jest obsługiwane tylko w przypadku zgodności z poprzednimi wersjami, gdy zasoby zostały utworzone przy użyciu Media Services V2. Oznacza to, że wersja v3 współpracuje z istniejącymi zasobami zaszyfrowanymi magazynu, ale nie umożliwia tworzenia nowych.

## <a name="code-differences"></a>Różnice w kodzie

W poniższej tabeli przedstawiono różnice w kodzie między wersjami 2 i V3 dla typowych scenariuszy.

|Scenariusz|Interfejs API v2|Interfejs API v3|
|---|---|---|
|Tworzenie elementu zawartości i przekazywanie pliku |[przykład v2 .NET](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L113)|[przykład .NET w wersji 3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L169)|
|Przesyłanie zadania|[przykład v2 .NET](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L146)|[przykład .NET w wersji 3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L298)<br/><br/>Pokazuje, jak najpierw utworzyć transformację, a następnie przesłać zadanie.|
|Publikowanie elementu zawartości przy użyciu szyfrowania AES |1. Utwórz ContentKeyAuthorizationPolicyOption<br/>2. Utwórz ContentKeyAuthorizationPolicy<br/>3. Utwórz AssetDeliveryPolicy<br/>4. Utwórz element zawartości i przekaż zawartość lub Prześlij zadanie, a następnie użyj elementu zawartości wyjściowej<br/>5. Skojarz AssetDeliveryPolicy z elementem zawartości<br/>6. Utwórz ContentKey<br/>7. Dołącz ContentKey do zasobu<br/>8. Utwórz AccessPolicy<br/>9. Tworzenie lokalizatora<br/><br/>[przykład v2 .NET](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L64)|1. Utwórz zasady klucza zawartości<br/>2. Utwórz element zawartości<br/>3. Przekaż zawartość lub użyj elementu zawartości jako JobOutput<br/>4. Tworzenie lokalizatora przesyłania strumieniowego<br/><br/>[przykład .NET w wersji 3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs#L105)|
|Pobierz szczegóły zadania i Zarządzaj zadaniami |[Zarządzanie zadaniami za pomocą wersji 2](../previous/media-services-dotnet-manage-entities.md#get-a-job-reference) |[Zarządzanie zadaniami za pomocą wersji 3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L546)|

> [!NOTE]
> Oznacz ten artykuł zakładką i Kontynuuj sprawdzanie aktualizacji.

## <a name="ask-questions-give-feedback-get-updates"></a>Zadawaj pytania, Przekaż opinię, uzyskaj aktualizacje

Zapoznaj się z artykułem [community Azure Media Services](media-services-community.md) , aby zobaczyć różne sposoby zadawania pytań, przekazać Opinie i uzyskać aktualizacje dotyczące Media Services.

## <a name="next-steps"></a>Następne kroki

[Wskazówki dotyczące migracji dotyczące przenoszenia z Media Services V2 do wersji v3](migrate-from-v2-to-v3.md)
