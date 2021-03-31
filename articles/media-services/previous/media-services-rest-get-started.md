---
title: Wprowadzenie do dostarczania zawartości na żądanie przy użyciu usługi REST | Microsoft Docs
description: Ten samouczek przeprowadzi Cię przez kroki wdrażania aplikacji do dostarczania zawartości na żądanie z Azure Media Services przy użyciu interfejsu API REST.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 88194b59-e479-43ac-b179-af4f295e3780
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.openlocfilehash: ccf4bc18270dcdbcae02f7b45bea2a05ed663227
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103008879"
---
# <a name="get-started-with-delivering-content-on-demand-using-rest"></a>Wprowadzenie do dostarczania zawartości na żądanie przy użyciu usługi REST

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)] 

> [!NOTE]
> Do usługi Media Services w wersji 2 nie są już dodawane żadne nowe funkcje. <br/>Zapoznaj się z najnowszą wersją [Media Services wersja 3](../latest/index.yml). Zobacz też [wskazówki dotyczące migracji od wersji 2 do V3](../latest/migrate-v-2-v-3-migration-introduction.md)

Ten przewodnik Szybki Start przeprowadzi Cię przez kroki wdrażania aplikacji do dostarczania zawartości wideo na żądanie (VoD) przy użyciu interfejsów API REST usługi Azure Media Services (AMS).

Samouczek przedstawia podstawowy przepływ pracy usług Media Services oraz najczęściej występujące obiekty i zadania programowania wymagane w celu projektowania usług Media Services. Po zakończeniu samouczka można przesłać strumieniowo lub pobrać progresywnie przykładowy plik multimedialny, który został przekazany, zakodowany i pobrany.

Na poniższym obrazie przedstawiono niektóre z najczęściej używanych obiektów podczas tworzenia aplikacji VoD w modelu Media Services OData.

Kliknij obraz, aby go wyświetlić w pełnym rozmiarze.  

[![Diagram przedstawiający niektóre z najczęściej używanych obiektów w modelu danych Azure Media Services obiektów na potrzeby tworzenia aplikacji wideo na żądanie.](./media/media-services-rest-get-started/media-services-overview-object-model-small.png)](./media/media-services-rest-get-started/media-services-overview-object-model.png#lightbox)

## <a name="prerequisites"></a>Wymagania wstępne
Aby rozpocząć programowanie Media Services przy użyciu interfejsów API REST, wymagane są następujące wymagania wstępne.

* Konto platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [Bezpłatna wersja próbna platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* Konto usługi Media Services. Aby utworzyć konto usługi Media Services, zobacz temat [Jak utworzyć konto usługi Media Services](media-services-portal-create-account.md).
* Zrozumienie sposobu tworzenia przy użyciu interfejsu API REST Media Services. Aby uzyskać więcej informacji, zobacz [Media Services API REST — Omówienie](media-services-rest-how-to-use.md).
* Wybrana przez Ciebie aplikacja, która może wysyłać żądania HTTP i odpowiedzi. Ten samouczek używa [programu Fiddler](https://www.telerik.com/download/fiddler).

W tym przewodniku szybki start przedstawiono następujące zadania.

1. Uruchamianie punktów końcowych przesyłania strumieniowego (przy użyciu witryny Azure Portal).
2. Połącz się z kontem Media Services za pomocą interfejsu API REST.
3. Utwórz nowy element zawartości i Przekaż plik wideo za pomocą interfejsu API REST.
4. Kodowanie pliku źródłowego do zestawu plików MP4 z adaptacyjną szybkością transmisji bitów przy użyciu interfejsu API REST.
5. Opublikowanie elementu zawartości i pobieranie przesyłania strumieniowego oraz adresów URL pobierania progresywnego za pomocą interfejsu API REST.
6. Odtwarzanie zawartości.

>[!NOTE]
>Limit różnych zasad usługi AMS wynosi 1 000 000 (na przykład zasad lokalizatorów lub ContentKeyAuthorizationPolicy). Użyj tego samego identyfikatora zasad, jeśli zawsze używasz tych samych dni/uprawnień dostępu, na przykład zasad dla lokalizatorów, które mają być nadal wykonywane przez długi czas (zasady bez przekazywania). Aby uzyskać więcej informacji, zobacz [ten](media-services-dotnet-manage-entities.md#limit-access-policies) artykuł.

Aby uzyskać szczegółowe informacje na temat jednostek REST usługi AMS używanych w tym artykule, zobacz [Azure Media Services Dokumentacja interfejsu API REST](/rest/api/media/operations/azure-media-services-rest-api-reference). Zobacz też temat [Azure Media Services pojęcia](media-services-concepts.md).

>[!NOTE]
>Podczas uzyskiwania dostępu do jednostek w Media Services należy ustawić określone pola nagłówka i wartości w żądaniach HTTP. Aby uzyskać więcej informacji, zobacz [konfigurowanie Media Services tworzenia interfejsu API REST](media-services-rest-how-to-use.md).

## <a name="start-streaming-endpoints-using-the-azure-portal"></a>Uruchamianie punktów końcowych przesyłania strumieniowego przy użyciu witryny Azure Portal

Podczas pracy z Azure Media Services jednym z najbardziej typowych scenariuszy jest dostarczanie wideo za pośrednictwem przesyłania strumieniowego z adaptacyjną szybkością transmisji bitów. Usługa Media Services udostępnia funkcję dynamicznego tworzenia pakietów, która pozwala dostarczać kodowaną zawartość plików MP4 z adaptacyjną szybkością transmisji bitów w formatach przesyłania strumieniowego obsługiwanych przez usługę Media Services (MPEG DASH, HLS, Smooth Streaming) w odpowiednim czasie bez konieczności przechowywania wersji wstępnie utworzonych pakietów poszczególnych formatów przesyłania strumieniowego.

>[!NOTE]
>Po utworzeniu konta usługi AMS zostanie do niego dodany **domyślny** punkt końcowy przesyłania strumieniowego mający stan **Zatrzymany**. Aby rozpocząć przesyłanie strumieniowe zawartości oraz korzystać z dynamicznego tworzenia pakietów i szyfrowania dynamicznego, punkt końcowy przesyłania strumieniowego, z którego chcesz strumieniowo przesyłać zawartość, musi mieć stan **Uruchomiony**.

Aby uruchomić punkt końcowy przesyłania strumieniowego, wykonaj następujące czynności:

1. Zaloguj się do [Azure Portal](https://portal.azure.com/).
2. W oknie Ustawienia kliknij pozycję Punkty końcowe przesyłania strumieniowego.
3. Kliknij domyślny punkt końcowy przesyłania strumieniowego.

    Zostanie wyświetlone okno SZCZEGÓŁY DOMYŚLNEGO PUNKTU KOŃCOWEGO PRZESYŁANIA STRUMIENIOWEGO.

4. Kliknij ikonę Uruchom.
5. Kliknij przycisk Zapisz, aby zapisać zmiany.

## <a name="connect-to-the-media-services-account-with-rest-api"></a><a id="connect"></a>Nawiązywanie połączenia z kontem Media Services przy użyciu interfejsu API REST

Aby uzyskać informacje na temat nawiązywania połączenia z interfejsem API usługi AMS, zobacz [dostęp do interfejsu api Azure Media Services przy użyciu uwierzytelniania w usłudze Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="create-a-new-asset-and-upload-a-video-file-with-rest-api"></a><a id="upload"></a>Tworzenie nowego elementu zawartości i przekazywanie pliku wideo za pomocą interfejsu API REST

Za pomocą usługi Media Services można przekazać pliki cyfrowe do elementu zawartości. Jednostka **zasobu** może zawierać wideo, audio, obrazy, kolekcje miniatur, ścieżki tekstowe i pliki napisów (oraz metadane dotyczące tych plików).  Gdy pliki zostaną przekazane do elementu zawartości, zawartość jest bezpiecznie przechowywana w chmurze w celu dalszej przetwarzania i przesyłania strumieniowego.

Jedną z wartości, które należy podać podczas tworzenia elementu zawartości, jest opcja tworzenia zasobów. Właściwość **Options** jest wartością wyliczenia opisującą opcje szyfrowania, za pomocą których można utworzyć zasób. Prawidłowa wartość jest jedną z wartości z poniższej listy, a nie kombinacją wartości z tej listy:

* **Brak**  =  **0** — żadne szyfrowanie nie jest używane. W przypadku korzystania z tej opcji zawartość nie jest chroniona podczas przesyłania ani przechowywania w magazynie.
    Jeśli planujesz dostarczać zawartość w formacie MP4 przy użyciu pobierania progresywnego, użyj tej opcji.
* **StorageEncrypted**  =  **1** — szyfruje zawartość Wyczyść lokalnie przy użyciu szyfrowania AES-256 bit, a następnie przekazuje je do usługi Azure Storage, gdzie jest przechowywana w stanie spoczynku. Elementy zawartości chronione przy użyciu szyfrowania magazynu są automatycznie odszyfrowywane i umieszczane w systemie szyfrowania plików przed kodowaniem, a także opcjonalnie ponownie szyfrowane przed przesłaniem zwrotnym w formie nowego elementu zawartości wyjściowej. Pierwotnym zastosowaniem szyfrowania magazynu jest zabezpieczenie za pomocą silnego szyfrowania wysokiej jakości multimedialnych plików wejściowych przechowywanych na dysku.
* **CommonEncryptionProtected**  =  **2** . Użyj tej opcji, jeśli przesyłasz zawartość, która została już zaszyfrowana i chroniona za pomocą Common Encryption lub technologii DRM (na przykład Smooth Streaming chronione za pomocą technologii PlayReady DRM).
* **EnvelopeEncryptionProtected**  =  **4** — Użyj tej opcji, jeśli przekazujesz HLS szyfrowany przy użyciu algorytmu AES. Pliki muszą być zakodowane i zaszyfrowane przez Menedżera transformacji.

### <a name="create-an-asset"></a>Tworzenie zasobu
Element zawartości to kontener dla wielu typów lub zestawów obiektów w Media Services, w tym wideo, audio, obrazy, kolekcje miniatur, ścieżki tekstowe i pliki napisów. W interfejsie API REST Tworzenie elementu zawartości wymaga wysłania żądania POST do Media Services i umieszczenia wszelkich informacji o właściwościach zasobu w treści żądania.

Poniższy przykład pokazuje, jak utworzyć element zawartości.

**Żądanie HTTP**

```console
POST https://wamsbayclus001rest-hs.cloudapp.net/api/Assets HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
x-ms-client-request-id: c59de965-bc89-4295-9a57-75d897e5221e
Host: wamsbayclus001rest-hs.cloudapp.net
Content-Length: 45

{"Name":"BigBuckBunny.mp4", "Options":"0"}
```

**Odpowiedź HTTP**

Jeśli to się powiedzie, zwracane są następujące elementy:

```console
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 452
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Assets('nb%3Acid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')
Server: Microsoft-IIS/8.5
x-ms-client-request-id: c59de965-bc89-4295-9a57-75d897e5221e
request-id: e98be122-ae09-473a-8072-0ccd234a0657
x-ms-request-id: e98be122-ae09-473a-8072-0ccd234a0657
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Sun, 18 Jan 2015 22:06:40 GMT

{  
    odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets/@Element",
   "Id":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
   "State":0,
   "Created":"2015-01-18T22:06:40.6010903Z",
   "LastModified":"2015-01-18T22:06:40.6010903Z",
   "AlternateId":null,
   "Name":"BigBuckBunny2.mp4",
   "Options":0,
   "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
   "StorageAccountName":"storagetestaccount001"
}
```

### <a name="create-an-assetfile"></a>Utwórz AssetFile
Jednostka [AssetFile](/rest/api/media/operations/assetfile) reprezentuje plik wideo lub audio, który jest przechowywany w kontenerze obiektów BLOB. Plik zasobów jest zawsze skojarzony z zasobem, a element zawartości może zawierać jeden lub wiele AssetFiles. Zadanie kodera Media Services nie powiedzie się, jeśli obiekt pliku zasobów nie jest skojarzony z plikiem cyfrowym w kontenerze obiektów BLOB.

Po przekazaniu pliku multimediów cyfrowych do kontenera obiektów BLOB używasz żądania **scalania** http, aby zaktualizować AssetFile za pomocą informacji o pliku multimedialnym (jak pokazano w dalszej części tematu).

**Żądanie HTTP**

```console
POST https://wamsbayclus001rest-hs.cloudapp.net/api/Files HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
Host: wamsbayclus001rest-hs.cloudapp.net
Content-Length: 164

{  
   "IsEncrypted":"false",
   "IsPrimary":"false",
   "MimeType":"video/mp4",
   "Name":"BigBuckBunny.mp4",
   "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
}
```

**Odpowiedź HTTP**

```console
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 535
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5')
Server: Microsoft-IIS/8.5
request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
x-ms-request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Mon, 19 Jan 2015 00:34:07 GMT

{  
   "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Files/@Element",
   "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
   "Name":"BigBuckBunny.mp4",
   "ContentFileSize":"0",
   "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
   "EncryptionVersion":null,
   "EncryptionScheme":null,
   "IsEncrypted":false,
   "EncryptionKeyId":null,
   "InitializationVector":null,
   "IsPrimary":false,
   "LastModified":"2015-01-19T00:34:08.1934137Z",
   "Created":"2015-01-19T00:34:08.1934137Z",
   "MimeType":"video/mp4",
   "ContentChecksum":null
}
```

### <a name="creating-the-accesspolicy-with-write-permission"></a>Tworzenie AccessPolicy z uprawnieniami do zapisu
Przed przekazaniem plików do usługi BLOB Storage Ustaw uprawnienia dostępu do zapisu w elemencie zawartości. W tym celu Opublikuj żądanie HTTP w zestawie jednostek AccessPolicies. Zdefiniuj wartość DurationInMinutes podczas tworzenia lub w odpowiedzi zostanie wyświetlony komunikat o błędzie wewnętrznego serwera 500. Aby uzyskać więcej informacji na temat AccessPolicies, zobacz [AccessPolicy](/rest/api/media/operations/accesspolicy).

Poniższy przykład pokazuje, jak utworzyć AccessPolicy:

**Żądanie HTTP**

```console
POST https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
Host: wamsbayclus001rest-hs.cloudapp.net
Content-Length: 74

{"Name":"NewUploadPolicy", "DurationInMinutes":"440", "Permissions":"2"}
```

**Odpowiedź HTTP**

W przypadku powodzenia następuje zwrócenie następującej odpowiedzi:

```console
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 312
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies('nb%3Apid%3AUUID%3Abe0ac48d-af7d-4877-9d60-1805d68bffae')
Server: Microsoft-IIS/8.5
request-id: 74c74545-7e0a-4cd6-a440-c1c48074a970
x-ms-request-id: 74c74545-7e0a-4cd6-a440-c1c48074a970
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Sun, 18 Jan 2015 22:18:06 GMT

{  
   "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#AccessPolicies/@Element",
   "Id":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
   "Created":"2015-01-18T22:18:06.6370575Z",
   "LastModified":"2015-01-18T22:18:06.6370575Z",
   "Name":"NewUploadPolicy",
   "DurationInMinutes":440.0,
   "Permissions":2
}
```

### <a name="get-the-upload-url"></a>Pobierz adres URL przekazywania

Aby otrzymać rzeczywisty adres URL przekazywania, Utwórz lokalizator sygnatury dostępu współdzielonego. Lokalizatory definiują czas rozpoczęcia i typ punktu końcowego połączenia dla klientów, którzy chcą uzyskać dostęp do plików w elemencie zawartości. Można utworzyć wiele jednostek lokalizatora dla danej AccessPolicy i pary zasobów, aby obsługiwać różne żądania klientów i ich potrzeby. Każdy z tych lokalizatorów używa wartości StartTime oraz wartości DurationInMinutes AccessPolicy, aby określić długość czasu, przez jaki można użyć adresu URL. Aby uzyskać więcej informacji, zobacz [lokalizator](/rest/api/media/operations/locator).

Adres URL sygnatury dostępu współdzielonego ma następujący format:

`{https://myaccount.blob.core.windows.net}/{asset name}/{video file name}?{SAS signature}`

Zagadnienia do rozważenia:

* W tym samym czasie nie można mieć więcej niż pięciu unikatowych lokalizatorów skojarzonych z danym elementem zawartości. 
* Jeśli zachodzi potrzeba natychmiastowego przekazania plików, należy ustawić wartość StartTime na pięć minut przed bieżącą godziną. Wynika to z faktu, że nastąpi przechylenie zegara między komputerem klienckim a Media Services. Ponadto wartość StartTime musi mieć następujący format: RRRR-MM-DDTgg: mm: SSS (na przykład "2014-05-23T17:53:50Z").    
* Po utworzeniu lokalizatora do użycia może wystąpić 30-40 sekund. Ten problem dotyczy zarówno [adresów URL sygnatury dostępu współdzielonego](../../storage/common/storage-sas-overview.md) , jak i lokalizatorów pochodzenia.

Poniższy przykład pokazuje, jak utworzyć lokalizator adresów URL sygnatury dostępu współdzielonego, jak określono przez Właściwość Type w treści żądania ("1" dla lokalizatora sygnatury dostępu współdzielonego i "2" dla lokalizatora źródła na żądanie). Zwrócona Właściwość **Path** zawiera adres URL, którego należy użyć do przekazania pliku.

**Żądanie HTTP**

```console
POST https://wamsbayclus001rest-hs.cloudapp.net/api/Locators HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
Host: wamsbayclus001rest-hs.cloudapp.net
Content-Length: 178

{  
   "AccessPolicyId":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
   "AssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
   "StartTime":"2015-02-18T16:45:53",
   "Type":1
}
```

**Odpowiedź HTTP**

W przypadku powodzenia następuje zwrócenie następującej odpowiedzi:

```console
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 949
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Locators('nb%3Alid%3AUUID%3Aaf57bdd8-6751-4e84-b403-f3c140444b54')
Server: Microsoft-IIS/8.5
request-id: 2adeb1f8-89c5-4cc8-aa4f-08cdfef33ae0
x-ms-request-id: 2adeb1f8-89c5-4cc8-aa4f-08cdfef33ae0
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Mon, 19 Jan 2015 03:01:29 GMT

{  
   "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Locators/@Element",
   "Id":"nb:lid:UUID:af57bdd8-6751-4e84-b403-f3c140444b54",
   "ExpirationDateTime":"2015-02-19T00:05:53",
   "Type":1,
   "Path":"https://storagetestaccount001.blob.core.windows.net/asset-f438649c-313c-46e2-8d68-7d2550288247?sv=2012-02-12&sr=c&si=af57bdd8-6751-4e84-b403-f3c140444b54&sig=fE4btwEfZtVQFeC0Wh3Kwks2OFPQfzl5qTMW5YytiuY%3D&st=2015-02-18T16%3A45%3A53Z&se=2015-02-19T00%3A05%3A53Z",
   "BaseUri":"https://storagetestaccount001.blob.core.windows.net/asset-f438649c-313c-46e2-8d68-7d2550288247",
   "ContentAccessComponent":"?sv=2012-02-12&sr=c&si=af57bdd8-6751-4e84-b403-f3c140444b54&sig=fE4btwEfZtVQFeC0Wh3Kwks2OFPQfzl5qTMW5YytiuY%3D&st=2015-02-18T16%3A45%3A53Z&se=2015-02-19T00%3A05%3A53Z",
   "AccessPolicyId":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
   "AssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
   "StartTime":"2015-02-18T16:45:53",
   "Name":null
}
```

### <a name="upload-a-file-into-a-blob-storage-container"></a>Przekaż plik do kontenera magazynu obiektów BLOB
Po skonfigurowaniu AccessPolicy i lokalizatora rzeczywisty plik zostanie przekazany do kontenera usługi Azure Blob Storage za pomocą interfejsów API REST usługi Azure Storage. Pliki muszą być przekazywane jako blokowe obiekty blob. Stronicowe obiekty blob nie są obsługiwane przez Azure Media Services.  

> [!NOTE]
> Należy dodać nazwę pliku, który ma zostać przekazany do wartości **ścieżki** lokalizatora otrzymanej w poprzedniej sekcji. Na przykład `https://storagetestaccount001.blob.core.windows.net/asset-e7b02da4-5a69-40e7-a8db-e8f4f697aac0/BigBuckBunny.mp4?`.
>
>

Aby uzyskać więcej informacji na temat pracy z obiektami BLOB usługi Azure Storage, zobacz [API REST usługa BLOB Service](/rest/api/storageservices/blob-service-rest-api).

### <a name="update-the-assetfile"></a>Aktualizowanie AssetFile
Teraz, gdy plik został przekazany, zaktualizuj informacje o rozmiarze FileAsset (i innych). Na przykład:

```console
MERGE https://wamsbayclus001rest-hs.cloudapp.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5') HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
Host: wamsbayclus001rest-hs.cloudapp.net

{  
   "ContentFileSize":"1186540",
   "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
   "MimeType":"video/mp4",
   "Name":"BigBuckBunny.mp4",
   "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
}
```

**Odpowiedź HTTP**

Jeśli to się powiedzie, zwracane są następujące elementy:

```console
HTTP/1.1 204 No Content
...
```

## <a name="delete-the-locator-and-accesspolicy"></a>Usuń lokalizator i AccessPolicy
**Żądanie HTTP**

```console
DELETE https://wamsbayclus001rest-hs.cloudapp.net/api/Locators('nb%3Alid%3AUUID%3Aaf57bdd8-6751-4e84-b403-f3c140444b54') HTTP/1.1
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
Host: wamsbayclus001rest-hs.cloudapp.net
```

**Odpowiedź HTTP**

Jeśli to się powiedzie, zwracane są następujące elementy:

```console
HTTP/1.1 204 No Content
...
```

**Żądanie HTTP**

```console
DELETE https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies('nb%3Apid%3AUUID%3Abe0ac48d-af7d-4877-9d60-1805d68bffae') HTTP/1.1
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
Host: wamsbayclus001rest-hs.cloudapp.net
```

**Odpowiedź HTTP**

Jeśli to się powiedzie, zwracane są następujące elementy:

```console
HTTP/1.1 204 No Content
...
```

## <a name="encode-the-source-file-into-a-set-of-adaptive-bitrate-mp4-files"></a><a id="encode"></a>Kodowanie pliku źródłowego do zestawu plików MP4 z adaptacyjną szybkością transmisji bitów

Po pozyskaniu zasobów do Media Services, można zakodować, transmuxed, znak wodny i tak dalej, przed dostarczeniem ich do klientów. Te działania są zaplanowane i uruchamiane w wielu wystąpieniach ról w tle, aby zapewnić wysoką wydajność oraz dostępność. Te działania są nazywane zadaniami, a każde zadanie składa się z niepodzielnych zadań, które wykonują rzeczywistą pracę w pliku zasobów (Aby uzyskać więcej informacji, zobacz [zadania](/rest/api/media/operations/job), opisy [zadań](/rest/api/media/operations/task) ).

Jak wspomniano wcześniej, podczas pracy z Azure Media Services jednym z najczęściej spotykanych scenariuszy jest dostarczanie do klientów przesyłania strumieniowego z adaptacyjną szybkością transmisji bitów. Media Services może dynamicznie spakować zestaw plików MP4 z adaptacyjną szybkością transmisji bitów do jednego z następujących formatów: HTTP Live Streaming (HLS), Smooth Streaming, KRESKa MPEG.

W poniższej sekcji pokazano, jak utworzyć zadanie zawierające jedno zadanie kodowania. Zadanie określa transkodowanie pliku Mezzanine do zestawu pliki MP4 z adaptacyjną szybkością transmisji bitów przy użyciu **Media Encoder Standard**. Sekcja pokazuje również, jak monitorować postęp przetwarzania zadań. Po zakończeniu zadania można utworzyć lokalizatory, które są potrzebne do uzyskania dostępu do zasobów.

### <a name="get-a-media-processor"></a>Pobierz procesor multimediów
W Media Services procesor multimediów to składnik obsługujący konkretne zadanie przetwarzania, takie jak kodowanie, Konwersja formatu, szyfrowanie lub odszyfrowywanie zawartości multimedialnej. W przypadku zadania kodowania pokazanego w tym samouczku będziemy używać Media Encoder Standard.

Poniższy kod żąda identyfikatora kodera.

**Żądanie HTTP**

```console
    GET https://wamsbayclus001rest-hs.cloudapp.net/api/MediaProcessors()?$filter=Name%20eq%20'Media%20Encoder%20Standard' HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
    Host: wamsbayclus001rest-hs.cloudapp.net
```

**Odpowiedź HTTP**

```console
HTTP/1.1 200 OK
Cache-Control: no-cache
Content-Length: 273
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Server: Microsoft-IIS/8.5
request-id: 6beb04b4-55a7-480d-8aa8-e5c5d59ffa1f
x-ms-request-id: 6beb04b4-55a7-480d-8aa8-e5c5d59ffa1f
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Mon, 19 Jan 2015 07:54:09 GMT

{  
   "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#MediaProcessors",
   "value":[  
      {  
         "Id":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
         "Description":"Media Encoder Standard",
         "Name":"Media Encoder Standard",
         "Sku":"",
         "Vendor":"Microsoft",
         "Version":"1.1"
      }
   ]
}
```

### <a name="create-a-job"></a>Tworzenie zadania
Każde zadanie może mieć jedno lub więcej zadań w zależności od typu przetwarzania, które chcesz wykonać. Za pomocą interfejsu API REST można tworzyć zadania i powiązane z nimi zadania na jeden z dwóch sposobów: zadania mogą być definiowane w tekście przy użyciu właściwości Nawigacja zadania w jednostkach zadania lub przez przetwarzanie wsadowe OData. Zestaw SDK Media Services używa przetwarzania wsadowego. Jednak w celu odczytywania przykładów kodu w tym artykule zadania są zdefiniowane w tekście. Aby uzyskać informacje na temat przetwarzania wsadowego, zobacz artykuł [Przetwarzanie wsadowe protokołu Open Data Protocol (OData)](https://www.odata.org/documentation/odata-version-3-0/batch-processing/).

Poniższy przykład pokazuje, jak utworzyć i ogłosić zadanie przy użyciu jednego zestawu zadań do kodowania wideo z określoną rozdzielczością i jakością. Poniższa sekcja dokumentacji zawiera listę wszystkich [ustawień predefiniowanych zadań](./media-services-mes-presets-overview.md) obsługiwanych przez procesor Media Encoder Standard.  

**Żądanie HTTP**

```console
POST https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs HTTP/1.1
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
Host: wamsbayclus001rest-hs.cloudapp.net
Content-Length: 482

{  
   "Name":"NewTestJob",
   "InputMediaAssets":[  
      {  
         "__metadata":{  
            "uri":"https://wamsbayclus001rest-hs.net/api/Assets('nb%3Acid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')"
         }
      }
   ],
   "Tasks":[  
      {  
         "Configuration":"Adaptive Streaming",
         "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
         "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset>
            <outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"
      }
   ]
}
```

**Odpowiedź HTTP**

W przypadku powodzenia następuje zwrócenie następującej odpowiedzi:

```console
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 1215
Content-Type: application/json;odata=verbose;charset=utf-8
Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')
Server: Microsoft-IIS/8.5
request-id: 532ac1ec-a475-4dce-b2d5-7c8ce94ac87c
x-ms-request-id: 532ac1ec-a475-4dce-b2d5-7c8ce94ac87c
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Mon, 19 Jan 2015 08:04:35 GMT

{  
   "d":{  
      "__metadata":{  
         "id":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')",
         "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')",
         "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Job"
      },
      "Tasks":{  
         "__deferred":{  
            "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/Tasks"
         }
      },
      "OutputMediaAssets":{  
         "__deferred":{  
            "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/OutputMediaAssets"
         }
      },
      "InputMediaAssets":{  
         "__deferred":{  
            "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')/InputMediaAssets"
         }
      },
      "Id":"nb:jid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
      "Name":"NewTestJob",
      "Created":"2015-01-19T08:04:34.3287057Z",
      "LastModified":"2015-01-19T08:04:34.3287057Z",
      "EndTime":null,
      "Priority":0,
      "RunningDuration":0,
      "StartTime":null,
      "State":0,
      "TemplateId":null,
      "JobNotificationSubscriptions":{  
         "__metadata":{  
            "type":"Collection(Microsoft.Cloud.Media.Vod.Rest.Data.Models.JobNotificationSubscription)"
         },
         "results":[  

         ]
      }
   }
}
```

Istnieje kilka istotnych kwestii, które należy wziąć pod uwagę w przypadku każdego żądania zadania:

* Właściwości TaskBody — muszą używać literału XML do definiowania liczby zasobów wejściowych lub wyjściowych, które są używane przez zadanie. Artykuł zadania zawiera definicję schematu XML dla kodu XML.
* W definicji TaskBody — każda wartość wewnętrzna dla `<inputAsset>` i `<outputAsset>` musi być ustawiona jako JobInputAsset (wartość) lub JobOutputAsset (wartość).
* Zadanie może mieć wiele elementów zawartości wyjściowej. Jeden JobOutputAsset (x) może być używany tylko raz jako dane wyjściowe zadania w zadaniu.
* Można określić JobInputAsset lub JobOutputAsset jako element zawartości wejściowej zadania.
* Zadania nie mogą tworzyć cyklu.
* Parametr value przekazany do JobInputAsset lub JobOutputAsset reprezentuje wartość indeksu dla elementu zawartości. Rzeczywiste elementy zawartości są zdefiniowane we właściwościach nawigacji InputMediaAssets i OutputMediaAssets w definicji jednostki zadania.

> [!NOTE]
> Ponieważ Media Services jest oparty na protokole OData V3, poszczególne zasoby w kolekcjach właściwości nawigacji InputMediaAssets i OutputMediaAssets są przywoływane przez parę nazwa-wartość "__metadata: URI".
>
>

* InputMediaAssets mapuje do jednego lub większej liczby zasobów utworzonych w Media Services. OutputMediaAssets są tworzone przez system. Nie odwołują się do istniejącego elementu zawartości.
* OutputMediaAssets można nazwać przy użyciu atrybutu assetname. Jeśli ten atrybut nie jest obecny, nazwa OutputMediaAsset jest dowolną wartością tekstu wewnętrznego `<outputAsset>` elementu z sufiksem wartości nazwy zadania lub wartości identyfikatora zadania (w przypadku, gdy właściwość Name nie jest zdefiniowana). Jeśli na przykład ustawisz wartość dla elementu assetname na "sample", właściwość OutputMediaAsset Name zostanie ustawiona na "sample". Jeśli jednak nie ustawisz wartości dla elementu assetname, ale ustawisz nazwę zadania na "NewJob", wówczas nazwą OutputMediaAsset będzie "JobOutputAsset (wartość) _NewJob".

    Poniższy przykład pokazuje, jak ustawić atrybut assetname:

    ```console
    "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"CustomOutputAssetName\">JobOutputAsset(0)</outputAsset></taskBody>"
    ```
* Aby włączyć tworzenie łańcucha zadań:

  * Zadanie musi mieć co najmniej dwa zadania podrzędne
  * Musi istnieć co najmniej jedno zadanie, którego dane wejściowe są dane wyjściowe innego zadania w zadaniu.

Aby uzyskać więcej informacji, zobacz [Tworzenie zadania kodowania za pomocą interfejsu API REST Media Services](media-services-rest-encode-asset.md).

### <a name="monitor-processing-progress"></a>Monitorowanie postępu przetwarzania
Stan zadania można pobrać przy użyciu właściwości State, jak pokazano w następującym przykładzie:

**Żądanie HTTP**

```console
GET https://wamsbayclus001rest-hs.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/State HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN>
Host: wamsbayclus001rest-hs.net
Content-Length: 0
```

**Odpowiedź HTTP**

W przypadku powodzenia następuje zwrócenie następującej odpowiedzi:

```console
HTTP/1.1 200 OK
Cache-Control: no-cache
Content-Length: 17
Content-Type: application/json;odata=verbose;charset=utf-8
Server: Microsoft-IIS/7.5
x-ms-request-id: 01324d81-18e2-4493-a3e5-c6186209f0c8
X-Content-Type-Options: nosniff
DataServiceVersion: 1.0;
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
Date: Sun, 13 May 2012 05:16:53 GMT

{"d":{"State":2}}
```

### <a name="cancel-a-job"></a>Anulowanie zadania
Media Services umożliwia anulowanie uruchamiania zadań za pomocą funkcji CancelJob. To wywołanie zwraca 400 kod błędu, jeśli użytkownik próbuje anulować zadanie, gdy jego stan zostanie anulowany, anulowanie, błąd lub zakończenie.

Poniższy przykład pokazuje, jak wywołać CancelJob.

**Żądanie HTTP**

```console
GET https://wamsbayclus001rest-hs.net/API/CancelJob?jobid='nb%3ajid%3aUUID%3a71d2dd33-efdf-ec43-8ea1-136a110bd42c' HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN>
Host: wamsbayclus001rest-hs.net
```

Jeśli to się powiedzie, kod odpowiedzi 204 jest zwracany bez treści komunikatu.

> [!NOTE]
> Musisz zakodować URL identyfikator zadania (zwykle NB: jid: UUID: wartość someValue) podczas przekazywania go jako parametru do CancelJob.
>
>

### <a name="get-the-output-asset"></a>Pobierz element zawartości wyjściowej
Poniższy kod przedstawia sposób żądania identyfikatora elementu zawartości wyjściowej.

**Żądanie HTTP**

```console
GET https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/OutputMediaAssets() HTTP/1.1
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
User-Agent: Microsoft ADO.NET Data Services
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
Host: wamsbayclus001rest-hs.cloudapp.net
```

**Odpowiedź HTTP**

```console
HTTP/1.1 200 OK
Cache-Control: no-cache
Content-Length: 445
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Server: Microsoft-IIS/8.5
request-id: 73cd605d-066a-46f1-8358-f4bd25a9220a
x-ms-request-id: 73cd605d-066a-46f1-8358-f4bd25a9220a
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Mon, 19 Jan 2015 08:28:13 GMT

{  
   "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets",
   "value":[  
      {  
         "Id":"nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
         "State":0,
         "Created":"2015-01-19T07:52:15.603",
         "LastModified":"2015-01-19T07:52:15.603",
         "AlternateId":null,
         "Name":"Multibitrate MP4s",
         "Options":0,
         "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-71d2dd33-efdf-ec43-8ea1-136a110bd42c",
         "StorageAccountName":"storagetestaccount001"
      }
   ]
}
```

## <a name="publish-the-asset-and-get-streaming-and-progressive-download-urls-with-rest-api"></a><a id="publish_get_urls"></a>Publikowanie elementu zawartości oraz pobieranie przesyłania strumieniowego i pobieranie progresywnych adresów URL przy użyciu interfejsu API REST

Aby przesłać strumieniowo lub pobrać element zawartości, należy go najpierw opublikować, tworząc lokalizator. Lokalizatory zapewniają dostęp do plików znajdujących się w elemencie zawartości. Usługa Media Services obsługuje dwa typy lokalizatorów: lokalizatory OnDemandOrigin używane do strumieniowego przesyłania plików multimedialnych (na przykład w formacie MPEG DASH, HLS i Smooth Streaming) oraz lokalizatory sygnatury dostępu współdzielonego (SAS) używane do pobierania plików multimedialnych. 

Po utworzeniu lokalizatorów można tworzyć adresy URL, które są używane do przesyłania strumieniowego lub pobierania plików.

>[!NOTE]
>Po utworzeniu konta usługi AMS zostanie do niego dodany **domyślny** punkt końcowy przesyłania strumieniowego mający stan **Zatrzymany**. Aby rozpocząć przesyłanie strumieniowe zawartości oraz korzystać z dynamicznego tworzenia pakietów i szyfrowania dynamicznego, punkt końcowy przesyłania strumieniowego, z którego chcesz strumieniowo przesyłać zawartość, musi mieć stan **Uruchomiony**.

Adres URL dla protokołu Smooth Streaming ma następujący format:

```console
{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest
```

Adres URL dla protokołu HLS ma następujący format:

```console
{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)
```

Adres URL dla protokołu MPEG DASH ma następujący format:

```console
{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)
```

Adres URL SAS używany do pobierania plików ma następujący format:

```console
{blob container name}/{asset name}/{file name}/{SAS signature}
```

W tej sekcji przedstawiono sposób wykonywania następujących zadań niezbędnych do "publikacji" zasobów.  

* Tworzenie AccessPolicy z uprawnieniami do odczytu
* Tworzenie adresu URL sygnatury dostępu współdzielonego na potrzeby pobierania zawartości
* Tworzenie adresu URL źródła dla zawartości przesyłania strumieniowego

### <a name="creating-the-accesspolicy-with-read-permission"></a>Tworzenie AccessPolicy z uprawnieniami do odczytu
Przed pobraniem lub przesłaniem strumieniowym zawartości multimedialnej należy najpierw zdefiniować AccessPolicy z uprawnieniami do odczytu i utworzyć odpowiednią jednostkę lokalizatora, która określa typ mechanizmu dostarczania, który ma zostać włączony dla klientów. Aby uzyskać więcej informacji na temat dostępnych właściwości, zobacz [AccessPolicy Entity Properties](/rest/api/media/operations/accesspolicy#accesspolicy_properties).

Poniższy przykład pokazuje, jak określić AccessPolicy dla uprawnień do odczytu dla danego elementu zawartości.

```console
POST https://wamsbayclus001rest-hs.net/API/AccessPolicies HTTP/1.1
Content-Type: application/json
Accept: application/json
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN>
Host: wamsbayclus001rest-hs.net
Content-Length: 74
Expect: 100-continue

{"Name": "DownloadPolicy", "DurationInMinutes" : "300", "Permissions" : 1}
```

Jeśli to się powiedzie, zwracany jest 201 kod sukcesu opisujący utworzoną jednostkę AccessPolicy. Następnie należy użyć identyfikatora AccessPolicy wraz z identyfikatorem zasobu zasobu zawierającego plik, który chcesz dostarczyć (na przykład dane wyjściowe), aby utworzyć jednostkę lokalizatora.

> [!NOTE]
> Ten podstawowy przepływ pracy jest taki sam jak przekazywanie pliku podczas pozyskiwania elementu zawartości (zgodnie z wcześniejszym opisem w tym temacie). Ponadto, podobnie jak przekazywanie plików, jeśli użytkownik (lub klienci) muszą natychmiast uzyskać dostęp do plików, należy ustawić wartość StartTime na pięć minut przed bieżącą godziną. Ta akcja jest niezbędna, ponieważ między klientem i Media Services może być nachylony zegar. Wartość StartTime musi mieć następujący format: RRRR-MM-DDTgg: mm: SSS (na przykład "2014-05-23T17:53:50Z").
>
>

### <a name="creating-a-sas-url-for-downloading-content"></a>Tworzenie adresu URL sygnatury dostępu współdzielonego na potrzeby pobierania zawartości
Poniższy kod pokazuje, jak uzyskać adres URL, którego można użyć do pobrania wcześniej utworzonego i przekazanego pliku multimedialnego. AccessPolicy ma ustawione uprawnienia do odczytu, a ścieżka lokalizatora odnosi się do adresu URL pobierania sygnatury dostępu współdzielonego.

```console
POST https://wamsbayclus001rest-hs.net/API/Locators HTTP/1.1
Content-Type: application/json
Accept: application/json
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN>
Host: wamsbayclus001rest-hs.net
Content-Length: 182
Expect: 100-continue

{"AccessPolicyId": "nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8", "AssetId" : "nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c", "StartTime" : "2014-05-17T16:45:53", "Type":1}
```

W przypadku powodzenia następuje zwrócenie następującej odpowiedzi:

```console
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 1150
Content-Type: application/json;odata=verbose;charset=utf-8
Location: https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')
Server: Microsoft-IIS/7.5
x-ms-request-id: 8cfd8556-3064-416a-97f2-67d9e35f0911
X-Content-Type-Options: nosniff
DataServiceVersion: 1.0;
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
Date: Mon, 14 May 2012 21:41:32 GMT

{  
   "d":{  
      "__metadata":{  
         "id":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')",
         "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')",
         "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Locator"
      },
      "AccessPolicy":{  
         "__deferred":{  
            "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')/AccessPolicy"
         }
      },
      "Asset":{  
         "__deferred":{  
            "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/Asset"
         }
      },
      "Id":"nb:lid:UUID:8e5a821d-2194-4d00-8884-adf979856874",
      "ExpirationDateTime":"\/Date(1337049393000)\/",
      "Type":1,
      "Path":"https://storagetestaccount001.blob.core.windows.net/asset-71d2dd33-efdf-ec43-8ea1-136a110bd42c?st=2012-05-14T21%3A36%3A33Z&se=2012-05-15T02%3A36%3A33Z&sr=c&si=8e5a821d-2194-4d00-8884-adf979856874&sig=y75dViDpC5V8WutrXM%2B%2FGpR3uOtqmlISiNlHU1YUBOg%3D",
      "AccessPolicyId":"nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8",
      "AssetId":"nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
      "StartTime":"\/Date(1337031393000)\/"
   }
}
```

Właściwość zwracanej ścieżki zawiera adres URL sygnatury **dostępu** współdzielonego.

> [!NOTE]
> W przypadku pobierania zawartości zaszyfrowanej magazynu należy ręcznie odszyfrować ją przed jej renderowaniem lub użyć odszyfrowania magazynu MediaProcessor w zadaniu przetwarzania w celu wygenerowania plików w postaci jasnej do OutputAsset, a następnie pobrania z tego elementu zawartości. Aby uzyskać więcej informacji na temat przetwarzania, zobacz Tworzenie zadania kodowania za pomocą interfejsu API REST Media Services. Ponadto lokalizatory adresów URL sygnatury dostępu współdzielonego nie można zaktualizować po ich utworzeniu. Na przykład nie można ponownie użyć tego samego lokalizatora z zaktualizowaną wartością StartTime. Jest to spowodowane sposobem tworzenia adresów URL sygnatury dostępu współdzielonego. Jeśli chcesz uzyskać dostęp do zasobu do pobrania po wygaśnięciu lokalizatora, musisz utworzyć nowy element z nowym okresem StartTime.
>
>

### <a name="download-files"></a>Pobieranie plików
Po skonfigurowaniu usługi AccessPolicy i lokalizatora można pobierać pliki przy użyciu interfejsów API REST usługi Azure Storage.  

> [!NOTE]
> Należy dodać nazwę pliku, który ma zostać pobrany do wartości **ścieżki** lokalizatora otrzymanej w poprzedniej sekcji. Na przykład `https://storagetestaccount001.blob.core.windows.net/asset-e7b02da4-5a69-40e7-a8db-e8f4f697aac0/BigBuckBunny.mp4` ? . . .

Aby uzyskać więcej informacji na temat pracy z obiektami BLOB usługi Azure Storage, zobacz [API REST usługa BLOB Service](/rest/api/storageservices/blob-service-rest-api).

W wyniku zadania kodowania, które zostało wykonane wcześniej (kodowanie w ramach adaptacyjnego zestawu MP4), istnieje wiele plików MP4, które można pobrać stopniowo. Na przykład:    

* `https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

* `https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

* `https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

* `https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

* `https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

* `https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

* `https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

* `https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_56kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

### <a name="creating-a-streaming-url-for-streaming-content"></a>Tworzenie adresu URL przesyłania strumieniowego dla zawartości przesyłania strumieniowego
Poniższy kod przedstawia sposób tworzenia lokalizatora adresów URL przesyłania strumieniowego:

```console
POST https://wamsbayclus001rest-hs/API/Locators HTTP/1.1
Content-Type: application/json
Accept: application/json
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN>
Host: wamsbayclus001rest-hs
Content-Length: 182
Expect: 100-continue

{"AccessPolicyId": "nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8", "AssetId" : "nb:cid:UUID:eb5540a2-116e-4d36-b084-7e9958f7f3c3", "StartTime" : "2014-05-17T16:45:53",, "Type":2}
```

W przypadku powodzenia następuje zwrócenie następującej odpowiedzi:

```console
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 981
Content-Type: application/json;odata=verbose;charset=utf-8
Location: https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')
Server: Microsoft-IIS/7.5
x-ms-request-id: 2eac4158-fc78-4fa2-81ee-c9f582dc385f
X-Content-Type-Options: nosniff
DataServiceVersion: 1.0;
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
Date: Mon, 14 May 2012 21:41:39 GMT

{  
   "d":{  
      "__metadata":{  
         "id":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')",
         "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')",
         "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Locator"
      },
      "AccessPolicy":{  
         "__deferred":{  
            "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')/AccessPolicy"
         }
      },
      "Asset":{  
         "__deferred":{  
            "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')/Asset"
         }
      },
      "Id":"nb:lid:UUID:52034bf6-dfae-4d83-aad3-3bd87dcb1a5d",
      "ExpirationDateTime":"\/Date(1337049395000)\/",
      "Type":2,
      "Path":"http://wamsbayclus001rest-hs.net/52034bf6-dfae-4d83-aad3-3bd87dcb1a5d/",
      "AccessPolicyId":"nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8",
      "AssetId":"nb:cid:UUID:eb5540a2-116e-4d36-b084-7e9958f7f3c3",
      "StartTime":"\/Date(1337031395000)\/"
   }
}
```

Aby przesłać strumieniowo adres URL źródła Smooth Streaming w odtwarzaczu multimediów przesyłanych strumieniowo, należy dołączyć Właściwość Path z nazwą pliku manifestu Smooth Streaming, po którym następuje wartość "/manifest".

`http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest`

Aby przesłać strumieniowo HLS, Dołącz (format = M3U8-AAPL) po "/manifest".

`http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=m3u8-aapl)`

Aby przesłać strumień MPEG, Dołącz (format = MPD-Time-CSF) po "/manifest".

`http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=mpd-time-csf)`


## <a name="play-your-content"></a><a id="play"></a>Odtwórz zawartość
Do przesyłania strumieniowego zawartości wideo użyj [odtwarzacza usługi Azure Media Services](https://aka.ms/azuremediaplayer).

Aby przetestować pobieranie progresywne, wklej adres URL do przeglądarki (na przykład IE, Chrome, Safari).

## <a name="next-steps-media-services-learning-paths"></a>Następne kroki: ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Wyraź opinię
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]