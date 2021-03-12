---
title: Konfigurowanie zasad dostarczania elementów zawartości przy użyciu interfejsu API REST Media Services | Microsoft Docs
description: W tym temacie pokazano, jak skonfigurować różne zasady dostarczania zasobów przy użyciu interfejsu API REST Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: cfowler
editor: ''
ms.assetid: 5cb9d32a-e68b-4585-aa82-58dded0691d0
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 65a7b9479dae1826f478ca5e038ef5feb1caefb6
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103017328"
---
# <a name="configuring-asset-delivery-policies"></a>Konfigurowanie zasad dostarczania elementów zawartości

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

[!INCLUDE [media-services-selector-asset-delivery-policy](../../../includes/media-services-selector-asset-delivery-policy.md)]

Jeśli planujesz dostarczanie dynamicznie szyfrowanych zasobów, jeden z kroków w przepływie pracy Media Services dostarczania zawartości konfiguruje zasady dostarczania dla zasobów. Zasady dostarczania elementów zawartości mówią, Media Services jak chcesz, aby zasób został dostarczony: w jakim protokole przesyłania strumieniowego powinien być dynamicznie spakowany pakiet zawartości (na przykład MPEG PAUZy, HLS, Smooth Streaming lub wszystkie), czy chcesz dynamicznie szyfrować element zawartości i jak (koperta lub typowe szyfrowanie).

W tym temacie omówiono przyczynę i sposób tworzenia i konfigurowania zasad dostarczania elementów zawartości.

> [!NOTE]
> Po utworzeniu konta usługi AMS zostanie do niego dodany **domyślny** punkt końcowy przesyłania strumieniowego mający stan **Zatrzymany**. Aby rozpocząć przesyłanie strumieniowe zawartości oraz korzystać z dynamicznego tworzenia pakietów i szyfrowania dynamicznego, punkt końcowy przesyłania strumieniowego, z którego chcesz strumieniowo przesyłać zawartość, musi mieć stan **Uruchomiony**. 
>
> Ponadto, aby można było korzystać z dynamicznego tworzenia pakietów i szyfrowania dynamicznego, element zawartości musi zawierać zestaw z adaptacyjną szybkością transmisji bitów pliki MP4 lub z adaptacyjną szybkością transmisji bitów Smooth Streaming.

Możesz zastosować różne zasady do tego samego elementu zawartości. Można na przykład zastosować szyfrowanie PlayReady do szyfrowania kopert Smooth Streaming i AES do HLS. Protokoły, które nie są zdefiniowane w zasadzie dostarczania (można na przykład dodać jedną zasadę, która określa tylko protokół HLS), nie mogą korzystać z przesyłania strumieniowego. Wyjątkiem od tej reguły jest przypadek, w którym nie zdefiniowano żadnej zasady dostarczania elementów zawartości. Wówczas wszystkie protokoły mogą być przesyłane bez zabezpieczeń.

Jeśli chcesz dostarczyć zasób zaszyfrowanego magazynu, musisz skonfigurować zasady dostarczania zasobów. Zanim będzie można przesłać strumieniowo zasób, serwer przesyłania strumieniowego usunie szyfrowanie magazynu i strumieniuje zawartość przy użyciu określonych zasad dostarczania. Na przykład, aby dostarczyć zasób szyfrowany przy użyciu klucza szyfrowania Advanced Encryption Standard (AES), ustaw typ zasad na **DynamicEnvelopeEncryption**. Aby usunąć szyfrowanie magazynu i przesłać strumieniowo element zawartości, ustaw dla opcji Typ zasad wartość **NoDynamicEncryption**. Przykłady pokazujące sposób konfigurowania tych typów zasad są następujące.

W zależności od sposobu skonfigurowania zasad dostarczania elementów zawartości możliwe jest dynamiczne pakowanie, dynamiczne szyfrowanie i przesyłanie strumieniowe następujących protokołów przesyłania strumieniowego: Smooth Streaming, HLS, strumienie dla KRESek MPEG.

Na poniższej liście przedstawiono formaty używane do przesyłania strumieniowego gładkie, HLS, KRESKa.

Smooth Streaming:

{nazwa punktu końcowego przesyłania strumieniowego-nazwa konta usługi Media Services}.streaming.mediaservices.windows.net/{identyfikator lokalizatora}/{nazwa pliku}.ism/Manifest

HLS

{nazwa punktu końcowego przesyłania strumieniowego-nazwa konta usługi Media Services}.streaming.mediaservices.windows.net/{identyfikator lokalizatora}/{nazwa pliku}.ism/Manifest(format=m3u8-aapl)

MPEG DASH

{nazwa punktu końcowego przesyłania strumieniowego-nazwa konta usługi Media Services}.streaming.mediaservices.windows.net/{identyfikator lokalizatora}/{nazwa pliku}.ism/Manifest(format=mpd-time-csf)


Aby uzyskać instrukcje dotyczące sposobu publikowania elementów zawartości i tworzenia adresu URL przesyłania strumieniowego, zobacz artykuł [Build a streaming URL](media-services-deliver-streaming-content.md) (Tworzenie adresu URL przesyłania strumieniowego).

## <a name="considerations"></a>Zagadnienia do rozważenia
* Nie można usunąć AssetDeliveryPolicy skojarzonej z elementem zawartości, gdy istnieje lokalizator OnDemand (streaming) dla tego elementu zawartości. Zalecenie polega na usunięciu zasad z elementu zawartości przed usunięciem zasad.
* Nie można utworzyć lokalizatora przesyłania strumieniowego w zaszyfrowanym elemencie zawartości magazynu, jeśli nie ustawiono żadnych zasad dostarczania zasobów.  Jeśli element zawartości nie jest szyfrowany jako magazyn, system zezwoli na utworzenie lokalizatora i strumieniowe przekazanie elementu zawartości bez zasad dostarczania elementów zawartości.
* Można mieć wiele zasad dostarczania zasobów skojarzonych z pojedynczym elementem zawartości, ale można określić tylko jeden sposób obsługi danego AssetDeliveryProtocol.  Znaczenie, jeśli próbujesz połączyć dwie zasady dostarczania, które określają protokół AssetDeliveryProtocol. SmoothStreaming, który spowoduje błąd, ponieważ system nie wie, który z nich ma być stosowany, gdy klient wysyła żądanie Smooth Streaming.
* Jeśli masz zasób z istniejącym lokalizatorem przesyłania strumieniowego, nie możesz połączyć nowych zasad z zasobem, odłączyć istniejących zasad od elementu zawartości lub zaktualizować zasady dostarczania skojarzone z elementem zawartości.  Najpierw musisz usunąć lokalizator przesyłania strumieniowego, dostosować zasady, a następnie ponownie utworzyć lokalizator przesyłania strumieniowego.  Tego samego locatorId można użyć podczas ponownego tworzenia lokalizatora przesyłania strumieniowego, ale należy upewnić się, że nie spowoduje to problemów dla klientów, ponieważ zawartość może być buforowana przez źródło lub w podrzędnej sieci CDN.

> [!NOTE]
> 
> Podczas uzyskiwania dostępu do jednostek w Media Services należy ustawić określone pola nagłówka i wartości w żądaniach HTTP. Aby uzyskać więcej informacji, zobacz [konfigurowanie Media Services tworzenia interfejsu API REST](media-services-rest-how-to-use.md).

## <a name="connect-to-media-services"></a>Łączenie się z usługą Media Services

Aby uzyskać informacje na temat nawiązywania połączenia z interfejsem API usługi AMS, zobacz [dostęp do interfejsu api Azure Media Services przy użyciu uwierzytelniania w usłudze Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="clear-asset-delivery-policy"></a>Wyczyść zasady dostarczania zasobów
### <a name="create-asset-delivery-policy"></a><a id="create_asset_delivery_policy"></a>Tworzenie zasad dostarczania elementów zawartości
Poniższe żądanie HTTP tworzy zasady dostarczania elementów zawartości, które określają, że nie mają zastosowania szyfrowania dynamicznego i dostarczania strumienia do dowolnego z następujących protokołów: PAUZy MPEG, HLS i protokołów Smooth Streaming. 

Aby uzyskać informacje na temat wartości, które można określić podczas tworzenia AssetDeliveryPolicy, zobacz sekcję [typy używane podczas definiowania AssetDeliveryPolicy](#types) .   

Żądanie:

```console
POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: 4651882c-d7ad-4d5e-86ab-f07f47dcb41e
Host: media.windows.net

{"Name":"Clear Policy",
"AssetDeliveryProtocol":7,
"AssetDeliveryPolicyType":2,
"AssetDeliveryConfiguration":null}
```

Odpowiedź:

```output
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 363
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: https://media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3A92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd')
Server: Microsoft-IIS/8.5
x-ms-client-request-id: 4651882c-d7ad-4d5e-86ab-f07f47dcb41e
request-id: 6aedbf93-4bc2-4586-8845-fd45590136af
x-ms-request-id: 6aedbf93-4bc2-4586-8845-fd45590136af
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Sun, 08 Feb 2015 06:21:27 GMT

{"odata.metadata":"https://media.windows.net/api/$metadata#AssetDeliveryPolicies/@Element",
"Id":"nb:adpid:UUID:92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd",
"Name":"Clear Policy",
"AssetDeliveryProtocol":7,
"AssetDeliveryPolicyType":2,
"AssetDeliveryConfiguration":null,
"Created":"2015-02-08T06:21:27.6908329Z",
"LastModified":"2015-02-08T06:21:27.6908329Z"}
```

### <a name="link-asset-with-asset-delivery-policy"></a><a id="link_asset_with_asset_delivery_policy"></a>Połącz zasób z zasadami dostarczania zasobów
Poniższe żądanie HTTP łączy określony element zawartości z zasadami dostarczania zasobów do programu.

Żądanie:

```console
POST https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A86933344-9539-4d0c-be7d-f842458693e0')/$links/DeliveryPolicies HTTP/1.1
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Content-Type: application/json
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: 56d2763f-6e72-419d-ba3c-685f6db97e81
Host: media.windows.net

{"uri":"https://media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3A92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd')"}
```

Odpowiedź:

```output
HTTP/1.1 204 No Content
```


## <a name="dynamicenvelopeencryption-asset-delivery-policy"></a>Zasady dostarczania elementów zawartości DynamicEnvelopeEncryption
### <a name="create-content-key-of-the-envelopeencryption-type-and-link-it-to-the-asset"></a>Utwórz klucz zawartości typu EnvelopeEncryption i połącz go z zasobem
W przypadku określania zasad dostarczania DynamicEnvelopeEncryption należy upewnić się, że zasób jest połączony z kluczem zawartości typu EnvelopeEncryption. Aby uzyskać więcej informacji, zobacz: [Tworzenie klucza zawartości](media-services-rest-create-contentkey.md)).

### <a name="get-delivery-url"></a><a id="get_delivery_url"></a>Pobierz adres URL dostarczania
Pobierz adres URL dostarczania dla określonej metody dostarczania klucza zawartości utworzonego w poprzednim kroku. Klient używa zwróconego adresu URL, aby zażądać klucza AES lub licencji PlayReady w celu odtwarzania chronionej zawartości.

Określ typ adresu URL do pobrania w treści żądania HTTP. Jeśli chronisz zawartość za pomocą oprogramowania PlayReady, poproś o adres URL pozyskiwania licencji PlayReady Media Services, używając 1 dla typu Delivery: {"Delivery: 1}. Jeśli chronisz zawartość przy użyciu szyfrowania kopert, zażądaj adresu URL pozyskiwania kluczy przez określenie 2 dla elementu Delivery: {"Key Delivery": 2}.

Żądanie:

```console
POST https://media.windows.net/api/ContentKeys('nb:kid:UUID:dc88f996-2859-4cf7-a279-c52a9d6b2f04')/GetKeyDeliveryUrl HTTP/1.1
Content-Type: application/json
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: 569d4b7c-a446-4edc-b77c-9fb686083dd8
Host: media.windows.net
Content-Length: 21

{"keyDeliveryType":2}
```

Odpowiedź:

```output
HTTP/1.1 200 OK
Cache-Control: no-cache
Content-Length: 198
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Server: Microsoft-IIS/8.5
x-ms-client-request-id: 569d4b7c-a446-4edc-b77c-9fb686083dd8
request-id: d26f65d2-fe65-4136-8fcf-31545be68377
x-ms-request-id: d26f65d2-fe65-4136-8fcf-31545be68377
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Sun, 08 Feb 2015 21:42:30 GMT

{"odata.metadata":"media.windows.net/api/$metadata#Edm.String","value":"https://amsaccount1.keydelivery.mediaservices.windows.net/?KID=dc88f996-2859-4cf7-a279-c52a9d6b2f04"}
```


### <a name="create-asset-delivery-policy"></a>Tworzenie zasad dostarczania elementów zawartości
Poniższe żądanie HTTP tworzy **AssetDeliveryPolicy** , który jest skonfigurowany do stosowania dynamicznego szyfrowania kopert (**DynamicEnvelopeEncryption**) do protokołu **HLS** (w tym przykładzie inne protokoły będą blokowane w przypadku przesyłania strumieniowego). 

Aby uzyskać informacje na temat wartości, które można określić podczas tworzenia AssetDeliveryPolicy, zobacz sekcję [typy używane podczas definiowania AssetDeliveryPolicy](#types) .   

Żądanie:

```console
POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
User-Agent: Microsoft ADO.NET Data Services
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
Host: media.windows.net

{"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":4,"AssetDeliveryPolicyType":3,"AssetDeliveryConfiguration":"[{\"Key\":2,\"Value\":\"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\\/\"}]"}
```


Odpowiedź:

```output
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 460
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3Aec9b994e-672c-4a5b-8490-a464eeb7964b')
Server: Microsoft-IIS/8.5
x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
request-id: c2a1ac0e-9644-474f-b38f-b9541c3a7c5f
x-ms-request-id: c2a1ac0e-9644-474f-b38f-b9541c3a7c5f
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Mon, 09 Feb 2015 05:24:38 GMT

{"odata.metadata":"media.windows.net/api/$metadata#AssetDeliveryPolicies/@Element","Id":"nb:adpid:UUID:ec9b994e-672c-4a5b-8490-a464eeb7964b","Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":4,"AssetDeliveryPolicyType":3,"AssetDeliveryConfiguration":"[{\"Key\":2,\"Value\":\"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\\/\"}]","Created":"2015-02-09T05:24:38.9167436Z","LastModified":"2015-02-09T05:24:38.9167436Z"}
```


### <a name="link-asset-with-asset-delivery-policy"></a>Połącz zasób z zasadami dostarczania zasobów
Zobacz [link do zasobu z zasadami dostarczania zasobów](#link_asset_with_asset_delivery_policy)

## <a name="dynamiccommonencryption-asset-delivery-policy"></a>Zasady dostarczania elementów zawartości DynamicCommonEncryption
### <a name="create-content-key-of-the-commonencryption-type-and-link-it-to-the-asset"></a>Utwórz klucz zawartości typu CommonEncryption i połącz go z zasobem
W przypadku określania zasad dostarczania DynamicCommonEncryption należy upewnić się, że zasób jest połączony z kluczem zawartości typu CommonEncryption. Aby uzyskać więcej informacji, zobacz: [Tworzenie klucza zawartości](media-services-rest-create-contentkey.md)).

### <a name="get-delivery-url"></a>Pobierz adres URL dostarczania
Pobierz adres URL dostarczania dla metody dostarczania PlayReady klucza zawartości utworzonego w poprzednim kroku. Klient używa zwróconego adresu URL, aby zażądać licencji PlayReady w celu odtwarzania chronionej zawartości. Aby uzyskać więcej informacji, zobacz [uzyskiwanie adresu URL dostarczania](#get_delivery_url).

### <a name="create-asset-delivery-policy"></a>Tworzenie zasad dostarczania elementów zawartości
Poniższe żądanie HTTP tworzy **AssetDeliveryPolicy** , który jest skonfigurowany do stosowania dynamicznego wspólnego szyfrowania (**DynamicCommonEncryption**) do protokołu **Smooth Streaming** (w tym przykładzie inne protokoły będą blokowane w przypadku przesyłania strumieniowego). 

Aby uzyskać informacje na temat wartości, które można określić podczas tworzenia AssetDeliveryPolicy, zobacz sekcję [typy używane podczas definiowania AssetDeliveryPolicy](#types) .   

Żądanie:

```console
POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
User-Agent: Microsoft ADO.NET Data Services
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
Host: media.windows.net

{"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":1,"AssetDeliveryPolicyType":4,"AssetDeliveryConfiguration":"[{\"Key\":2,\"Value\":\"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\/PlayReady\/"}]"}
```


Jeśli chcesz chronić zawartość przy użyciu funkcji DRM Widevine, zaktualizuj wartości AssetDeliveryConfiguration, aby użyć WidevineLicenseAcquisitionUrl (o wartości 7) i określ adres URL usługi dostarczania licencji. Korzystając z następujących partnerów AMS, można uzyskać pomoc w dostarczaniu licencji Widevine: [Axinom](https://www.axinom.com), [EZDRM](https://ezdrm.com/), [castLabs](https://castlabs.com/company/partners/azure/).

Na przykład: 

```console
{"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":2,"AssetDeliveryPolicyType":4,"AssetDeliveryConfiguration":"[{\"Key\":7,\"Value\":\"https:\\/\\/example.net\/WidevineLicenseAcquisition\/"}]"}
```

> [!NOTE]
> W przypadku szyfrowania za pomocą Widevine, można tylko dostarczyć przy użyciu ŁĄCZNIKa. Upewnij się, że w protokole dostarczania zasobów została określona KRESKa (2).
> 
> 

### <a name="link-asset-with-asset-delivery-policy"></a>Połącz zasób z zasadami dostarczania zasobów
Zobacz [link do zasobu z zasadami dostarczania zasobów](#link_asset_with_asset_delivery_policy)

## <a name="types-used-when-defining-assetdeliverypolicy"></a><a id="types"></a>Typy używane podczas definiowania AssetDeliveryPolicy

### <a name="assetdeliveryprotocol"></a>AssetDeliveryProtocol

Poniższe Wyliczenie zawiera opis wartości, które można ustawić dla protokołu dostarczania zasobów.

```csharp
[Flags]
public enum AssetDeliveryProtocol
{
    /// <summary>
    /// No protocols.
    /// </summary>
    None = 0x0,

    /// <summary>
    /// Smooth streaming protocol.
    /// </summary>
    SmoothStreaming = 0x1,

    /// <summary>
    /// MPEG Dynamic Adaptive Streaming over HTTP (DASH)
    /// </summary>
    Dash = 0x2,

    /// <summary>
    /// Apple HTTP Live Streaming protocol.
    /// </summary>
    HLS = 0x4,

    ProgressiveDownload = 0x10, 

    /// <summary>
    /// Include all protocols.
    /// </summary>
    All = 0xFFFF
}
```

### <a name="assetdeliverypolicytype"></a>AssetDeliveryPolicyType

Poniższe Wyliczenie zawiera opis wartości, które można ustawić dla typu zasad dostarczania zasobów.  

```csharp
public enum AssetDeliveryPolicyType
{
    /// <summary>
    /// Delivery Policy Type not set.  An invalid value.
    /// </summary>
    None,

    /// <summary>
    /// The Asset should not be delivered via this AssetDeliveryProtocol. 
    /// </summary>
    Blocked, 

    /// <summary>
    /// Do not apply dynamic encryption to the asset.
    /// </summary>
    /// 
    NoDynamicEncryption,  

    /// <summary>
    /// Apply Dynamic Envelope encryption.
    /// </summary>
    DynamicEnvelopeEncryption,

    /// <summary>
    /// Apply Dynamic Common encryption.
    /// </summary>
    DynamicCommonEncryption
    }
```

### <a name="contentkeydeliverytype"></a>ContentKeyDeliveryType

Poniższe Wyliczenie zawiera opis wartości, których można użyć do skonfigurowania metody dostarczania klucza zawartości dla klienta.
    
```csharp
public enum ContentKeyDeliveryType
{
    /// <summary>
    /// None.
    ///
    </summary>
    None = 0,

    /// <summary>
    /// Use PlayReady License acquisition protocol
    ///
    </summary>
    PlayReadyLicense = 1,

    /// <summary>
    /// Use MPEG Baseline HTTP key protocol.
    ///
    </summary>
    BaselineHttp = 2,

    /// <summary>
    /// Use Widevine License acquisition protocol
    ///
    </summary>
    Widevine = 3

}
```


### <a name="assetdeliverypolicyconfigurationkey"></a>AssetDeliveryPolicyConfigurationKey

Poniższe Wyliczenie zawiera opis wartości, które można skonfigurować w celu skonfigurowania kluczy używanych do uzyskania określonej konfiguracji dla zasad dostarczania elementów zawartości.

```csharp
public enum AssetDeliveryPolicyConfigurationKey
{
    /// <summary>
    /// No policies.
    /// </summary>
    None,

    /// <summary>
    /// Exact Envelope key URL.
    /// </summary>
    EnvelopeKeyAcquisitionUrl,

    /// <summary>
    /// Base key url that will have KID=<Guid> appended for Envelope.
    /// </summary>
    EnvelopeBaseKeyAcquisitionUrl,

    /// <summary>
    /// The initialization vector to use for envelope encryption in Base64 format.
    /// </summary>
    EnvelopeEncryptionIVAsBase64,

    /// <summary>
    /// The PlayReady License Acquisition Url to use for common encryption.
    /// </summary>
    PlayReadyLicenseAcquisitionUrl,

    /// <summary>
    /// The PlayReady Custom Attributes to add to the PlayReady Content Header
    /// </summary>
    PlayReadyCustomAttributes,

    /// <summary>
    /// The initialization vector to use for envelope encryption.
    /// </summary>
    EnvelopeEncryptionIV,

    /// <summary>
    /// Widevine DRM acquisition url
    /// </summary>
    WidevineLicenseAcquisitionUrl
}
```

## <a name="additional-notes"></a>Uwagi dodatkowe

* Widevine to usługa świadczona przez firmę Google Inc. z zastrzeżeniem warunków użytkowania i zasad zachowania poufności informacji w firmie Google, Inc.

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Wyraź opinię
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

