---
title: Publikowanie Azure Media Services zawartości przy użyciu interfejsu REST
description: Dowiedz się, jak utworzyć lokalizator, który jest używany do tworzenia adresu URL przesyłania strumieniowego. Kod używa interfejsu API REST.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: ff332c30-30c6-4ed1-99d0-5fffd25d4f23
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: b8733d499b2396160a73906f16a69291cf0b9d71
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103015424"
---
# <a name="publish-azure-media-services-content-using-rest"></a>Publikowanie Azure Media Services zawartości przy użyciu interfejsu REST

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [.NET](media-services-deliver-streaming-content.md)
> * [REST](media-services-rest-deliver-streaming-content.md)
> * [Portal](media-services-portal-publish.md)
> 
> 

Można przesyłać strumieniowo plik MP4 z adaptacyjną szybkością transmisji bitów, tworząc lokalizator przesyłania strumieniowego OnDemand i tworzący adres URL przesyłania strumieniowego. W artykule [kodowanie elementu zawartości](media-services-rest-encode-asset.md) pokazano, jak kodować zestaw plików MP4 z adaptacyjną szybkością transmisji bitów. Jeśli zawartość jest zaszyfrowana, skonfiguruj zasady dostarczania zasobów (zgodnie z opisem w [tym](media-services-rest-configure-asset-delivery-policy.md) artykule) przed utworzeniem lokalizatora. 

Lokalizatora przesyłania strumieniowego OnDemand można także użyć do kompilowania adresów URL, które wskazują pliki MP4, które mogą być stopniowo pobierane.  

W tym artykule pokazano, jak utworzyć lokalizator przesyłania strumieniowego OnDemand, aby opublikować element zawartości i utworzyć gładkie i HLS adresy URL przesyłania strumieniowego. Przedstawiono w nim również sposób tworzenia progresywnych adresów URL pobierania.

W [poniższej](#types) sekcji przedstawiono typy wyliczeniowe, których wartości są używane w wywołaniach Rest.   

> [!NOTE]
> Podczas uzyskiwania dostępu do jednostek w Media Services należy ustawić określone pola nagłówka i wartości w żądaniach HTTP. Aby uzyskać więcej informacji, zobacz [konfigurowanie Media Services tworzenia interfejsu API REST](media-services-rest-how-to-use.md).
> 

## <a name="connect-to-media-services"></a>Łączenie się z usługą Media Services

Aby uzyskać informacje na temat nawiązywania połączenia z interfejsem API usługi AMS, zobacz [dostęp do interfejsu api Azure Media Services przy użyciu uwierzytelniania w usłudze Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

>[!NOTE]
>Po pomyślnym nawiązaniu połączenia z usługą https://media.windows.net zostanie wyświetlone przekierowanie 301 określające inny identyfikator URI Media Services. Należy wykonać kolejne wywołania nowego identyfikatora URI.

## <a name="create-an-ondemand-streaming-locator"></a>Tworzenie lokalizatora przesyłania strumieniowego OnDemand
Aby utworzyć lokalizator przesyłania strumieniowego OnDemand i uzyskać adresy URL, należy wykonać następujące czynności:

1. Jeśli zawartość jest zaszyfrowana, Zdefiniuj zasady dostępu.
2. Utwórz lokalizator przesyłania strumieniowego OnDemand.
3. Jeśli planujesz przesyłanie strumieniowe, Pobierz plik manifestu przesyłania strumieniowego (. ISM) w elemencie zawartości. 
   
   Jeśli planujesz pobieranie progresywne, Pobierz nazwy plików MP4 w elemencie zawartości. 
4. Kompiluj adresy URL do pliku manifestu lub plików MP4. 
5. Nie można utworzyć lokalizatora przesyłania strumieniowego przy użyciu AccessPolicy, który obejmuje uprawnienia do zapisu lub usuwania.

### <a name="create-an-access-policy"></a>Tworzenie zasad dostępu

>[!NOTE]
>Limit różnych zasad usługi AMS wynosi 1 000 000 (na przykład zasad lokalizatorów lub ContentKeyAuthorizationPolicy). Użyj tego samego identyfikatora zasad, jeśli zawsze używasz tych samych dni/uprawnień dostępu, na przykład zasad dla lokalizatorów, które mają być nadal wykonywane przez długi czas (zasady bez przekazywania). Aby uzyskać więcej informacji, zobacz [ten](media-services-dotnet-manage-entities.md#limit-access-policies) artykuł.

Żądanie:

```console
POST https://media.windows.net/api/AccessPolicies HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: 6bcfd511-a561-448d-a022-a319a89ecffa
Host: media.windows.net
Content-Length: 68

{"Name":"access policy","DurationInMinutes":43200.0,"Permissions":1}
```

Odpowiedź:

```console
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 311
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: https:/media.windows.net/api/AccessPolicies('nb%3Apid%3AUUID%3A69c80d98-7830-407f-a9af-e25f4b0d3e5f')
Server: Microsoft-IIS/8.5
request-id: a877528a-bdb4-4414-9862-273f8e64f882
x-ms-request-id: a877528a-bdb4-4414-9862-273f8e64f882
x-ms-client-request-id: 6bcfd511-a561-448d-a022-a319a89ecffa
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 18 Feb 2015 06:52:09 GMT

{"odata.metadata":"https://media.windows.net/api/$metadata#AccessPolicies/@Element","Id":"nb:pid:UUID:69c80d98-7830-407f-a9af-e25f4b0d3e5f","Created":"2015-02-18T06:52:09.8862191Z","LastModified":"2015-02-18T06:52:09.8862191Z","Name":"access policy","DurationInMinutes":43200.0,"Permissions":1}
```

### <a name="create-an-ondemand-streaming-locator"></a>Tworzenie lokalizatora przesyłania strumieniowego OnDemand
Utwórz lokalizator dla określonego zasobu i zasad zasobów.

Żądanie:

```console
POST https://media.windows.net/api/Locators HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: ac159492-9a0c-40c3-aacc-551b1b4c5f62
Host: media.windows.net
Content-Length: 181

{"AccessPolicyId":"nb:pid:UUID:1480030d-c481-430a-9687-535c6a5cb272","AssetId":"nb:cid:UUID:cc1e445d-1500-80bd-538e-f1e4b71b465e","StartTime":"2015-02-18T06:34:47.267872Z","Type":2}
```
Odpowiedź:

```console
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 637
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: https://media.windows.net/api/Locators('nb%3Alid%3AUUID%3Abe245661-2bbd-4fc6-b14f-9cf9a1492e5e')
Server: Microsoft-IIS/8.5
request-id: 5bd5864a-0afd-44c0-a67a-4044a2c9043b
x-ms-request-id: 5bd5864a-0afd-44c0-a67a-4044a2c9043b
x-ms-client-request-id: ac159492-9a0c-40c3-aacc-551b1b4c5f62
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 18 Feb 2015 06:58:37 GMT

{"odata.metadata":"https://media.windows.net/api/$metadata#Locators/@Element","Id":"nb:lid:UUID:be245661-2bbd-4fc6-b14f-9cf9a1492e5e","ExpirationDateTime":"2015-03-20T06:34:47.267872+00:00","Type":2,"Path":"https://amstest1.streaming.mediaservices.windows.net/be245661-2bbd-4fc6-b14f-9cf9a1492e5e/","BaseUri":"https://amstest1.streaming.mediaservices.windows.net","ContentAccessComponent":"be245661-2bbd-4fc6-b14f-9cf9a1492e5e","AccessPolicyId":"nb:pid:UUID:1480030d-c481-430a-9687-535c6a5cb272","AssetId":"nb:cid:UUID:cc1e445d-1500-80bd-538e-f1e4b71b465e","StartTime":"2015-02-18T06:34:47.267872+00:00","Name":null}
```

### <a name="build-streaming-urls"></a>Kompiluj adresy URL przesyłania strumieniowego
Użyj wartości **Path** zwróconej po utworzeniu lokalizatora, aby utworzyć adresy URL gładkie, HLS i MPEG. 

Smooth Streaming: **ścieżka** + nazwa pliku manifestu + "/manifest"

Przykład:

`https://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest`

HLS: **Path** + nazwa pliku manifestu + "/manifest (format = M3U8-AAPL)"

Przykład:

`https://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=m3u8-aapl)`


KRESKa: **ścieżka** + nazwa pliku manifestu + "/manifest (format = MPD-Time-CSF)"

Przykład:

`https://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=mpd-time-csf)`


### <a name="build-progressive-download-urls"></a>Kompiluj adresy URL pobierania progresywnego
Użyj wartości **Path** zwróconej po utworzeniu lokalizatora w celu SKOMPILOWANIA adresu URL pobierania progresywnego.   

URL: **ścieżka** + plik zasobów nazwa MP4

Przykład:

`https://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4`

## <a name="enum-types"></a><a id="types"></a>Typy wyliczeniowe

```console
[Flags]
public enum AccessPermissions
{
    None = 0,
    Read = 1,
    Write = 2,
    Delete = 4,
    List = 8,
}

public enum LocatorType
{
    None = 0,
    Sas = 1,
    OnDemandOrigin = 2,
}
```

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Wyraź opinię
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Zobacz też
[Omówienie interfejsu API REST usługi Media Services Operations](media-services-rest-how-to-use.md)

[Konfigurowanie zasad dostarczania elementów zawartości](media-services-rest-configure-asset-delivery-policy.md)

