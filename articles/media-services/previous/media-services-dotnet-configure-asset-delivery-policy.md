---
title: Konfigurowanie zasad dostarczania elementów zawartości przy użyciu zestawu .NET SDK | Microsoft Docs
description: W tym temacie pokazano, jak skonfigurować różne zasady dostarczania zasobów przy użyciu zestawu SDK programu Azure Media Services .NET.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 3ec46f58-6cbb-4d49-bac6-1fd01a5a456b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 82e152f257e867dfdbf97c92b041ce57c5a1bdbc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103014268"
---
# <a name="configure-asset-delivery-policies-with-net-sdk"></a>Konfigurowanie zasad dostarczania elementów zawartości przy użyciu zestawu SDK platformy .NET

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

[!INCLUDE [media-services-selector-asset-delivery-policy](../../../includes/media-services-selector-asset-delivery-policy.md)]

## <a name="overview"></a>Omówienie
Jeśli planujesz dostarczanie zaszyfrowanych zasobów, jeden z kroków w przepływie pracy Media Services dostarczania zawartości konfiguruje zasady dostarczania dla zasobów. Zasady dostarczania elementów zawartości mówią, Media Services jak chcesz, aby zasób został dostarczony: w jakim protokole przesyłania strumieniowego powinien być dynamicznie spakowany pakiet zawartości (na przykład MPEG PAUZy, HLS, Smooth Streaming lub wszystkie), czy chcesz dynamicznie szyfrować element zawartości i jak (koperta lub typowe szyfrowanie).

W tym artykule omówiono przyczynę i sposób tworzenia i konfigurowania zasad dostarczania elementów zawartości.

>[!NOTE]
>Po utworzeniu konta AMS zostanie do niego dodany **domyślny** punkt końcowy przesyłania strumieniowego w stanie **zatrzymanym** . Aby rozpocząć przesyłanie strumieniowe zawartości oraz korzystać z dynamicznego tworzenia pakietów i szyfrowania dynamicznego, punkt końcowy przesyłania strumieniowego, z którego chcesz strumieniowo przesyłać zawartość, musi mieć stan **Uruchomiony**. 
>
>Ponadto, aby można było korzystać z dynamicznego tworzenia pakietów i szyfrowania dynamicznego, element zawartości musi zawierać zestaw z adaptacyjną szybkością transmisji bitów pliki MP4 lub z adaptacyjną szybkością transmisji bitów Smooth Streaming.

Możesz zastosować różne zasady do tego samego elementu zawartości. Można na przykład zastosować szyfrowanie PlayReady do szyfrowania kopert Smooth Streaming i AES do HLS. Protokoły, które nie są zdefiniowane w zasadzie dostarczania (można na przykład dodać jedną zasadę, która określa tylko protokół HLS), nie mogą korzystać z przesyłania strumieniowego. Wyjątkiem jest przypadek, w którym nie zdefiniowano żadnych zasad dostarczania elementów zawartości. Wówczas wszystkie protokoły mogą być przesyłane bez zabezpieczeń.

Jeśli chcesz dostarczyć zasób zaszyfrowanego magazynu, musisz skonfigurować zasady dostarczania zasobów. Zanim będzie można przesłać strumieniowo zasób, serwer przesyłania strumieniowego usunie szyfrowanie magazynu i strumieniuje zawartość przy użyciu określonych zasad dostarczania. Na przykład, aby dostarczyć zasób szyfrowany przy użyciu klucza szyfrowania Advanced Encryption Standard (AES), ustaw typ zasad na **DynamicEnvelopeEncryption**. Aby usunąć szyfrowanie magazynu i przesłać strumieniowo element zawartości, ustaw dla opcji Typ zasad wartość **NoDynamicEncryption**. Przykłady pokazujące sposób konfigurowania tych typów zasad są następujące.

W zależności od sposobu skonfigurowania zasad dostarczania elementów zawartości można dynamicznie spakować, szyfrować i przesyłać strumieniowo następujące protokoły przesyłania strumieniowego: Smooth Streaming, HLS i PAUZa MPEG.

Na poniższej liście przedstawiono formaty używane do przesyłania strumieniowego gładki, HLS i PAUZy.

Smooth Streaming:

{nazwa punktu końcowego przesyłania strumieniowego-nazwa konta usługi Media Services}.streaming.mediaservices.windows.net/{identyfikator lokalizatora}/{nazwa pliku}.ism/Manifest

HLS

{nazwa punktu końcowego przesyłania strumieniowego-nazwa konta usługi Media Services}.streaming.mediaservices.windows.net/{identyfikator lokalizatora}/{nazwa pliku}.ism/Manifest(format=m3u8-aapl)

MPEG DASH

{nazwa punktu końcowego przesyłania strumieniowego-nazwa konta usługi Media Services}.streaming.mediaservices.windows.net/{identyfikator lokalizatora}/{nazwa pliku}.ism/Manifest(format=mpd-time-csf)

## <a name="considerations"></a>Zagadnienia do rozważenia
* Przed usunięciem AssetDeliveryPolicy należy usunąć wszystkie lokalizatory przesyłania strumieniowego skojarzone z elementem zawartości. W razie potrzeby możesz później utworzyć nowe lokalizatory przesyłania strumieniowego z nowym AssetDeliveryPolicy.
* Nie można utworzyć lokalizatora przesyłania strumieniowego w zaszyfrowanym elemencie zawartości magazynu, jeśli nie ustawiono żadnych zasad dostarczania zasobów.  Jeśli element zawartości nie jest szyfrowany jako magazyn, system zezwoli na utworzenie lokalizatora i strumieniowe przekazanie elementu zawartości bez zasad dostarczania elementów zawartości.
* Można mieć wiele zasad dostarczania zasobów skojarzonych z pojedynczym elementem zawartości, ale można określić tylko jeden sposób obsługi danego AssetDeliveryProtocol.  Znaczenie, jeśli próbujesz połączyć dwie zasady dostarczania, które określają protokół AssetDeliveryProtocol. SmoothStreaming, który spowoduje błąd, ponieważ system nie wie, który z nich ma być stosowany, gdy klient wysyła żądanie Smooth Streaming.
* Jeśli masz zasób z istniejącym lokalizatorem przesyłania strumieniowego, nie możesz połączyć nowych zasad z elementem zawartości (można odłączyć istniejące zasady od elementu zawartości lub zaktualizować zasady dostarczania skojarzone z elementem zawartości).  Najpierw musisz usunąć lokalizator przesyłania strumieniowego, dostosować zasady, a następnie ponownie utworzyć lokalizator przesyłania strumieniowego.  Tego samego locatorId można użyć podczas ponownego tworzenia lokalizatora przesyłania strumieniowego, ale należy upewnić się, że nie spowoduje to problemów dla klientów, ponieważ zawartość może być buforowana przez źródło lub w podrzędnej sieci CDN.

## <a name="clear-asset-delivery-policy"></a>Wyczyść zasady dostarczania zasobów

Poniższa metoda **ConfigureClearAssetDeliveryPolicy** określa, że nie należy stosować szyfrowania dynamicznego i dostarczania strumienia przy użyciu jednego z następujących protokołów: kreska MPEG, HLS i protokoły Smooth Streaming. Możesz chcieć zastosować te zasady do zaszyfrowanych zasobów magazynu.

Aby uzyskać informacje na temat wartości, które można określić podczas tworzenia AssetDeliveryPolicy, zobacz sekcję [typy używane podczas definiowania AssetDeliveryPolicy](#types) .

```csharp
    static public void ConfigureClearAssetDeliveryPolicy(IAsset asset)
    {
        IAssetDeliveryPolicy policy =
        _context.AssetDeliveryPolicies.Create("Clear Policy",
        AssetDeliveryPolicyType.NoDynamicEncryption,
        AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.Dash, null);
        
        asset.DeliveryPolicies.Add(policy);
    }
```
## <a name="dynamiccommonencryption-asset-delivery-policy"></a>Zasady dostarczania elementów zawartości DynamicCommonEncryption

Poniższa metoda **CreateAssetDeliveryPolicy** tworzy **AssetDeliveryPolicy** , który jest skonfigurowany do stosowania dynamicznego uwierzytelniania wspólnego (**DynamicCommonEncryption**) do płynnego protokołu przesyłania strumieniowego (inne protokoły będą blokowane w przypadku przesyłania strumieniowego). Metoda przyjmuje dwa parametry: **zasób** (zasób, do którego chcesz zastosować zasady dostarczania) i **IContentKey** (klucz zawartości typu **CommonEncryption** , aby uzyskać więcej informacji, zobacz: [Tworzenie klucza zawartości](media-services-dotnet-create-contentkey.md#common_contentkey)).

Aby uzyskać informacje na temat wartości, które można określić podczas tworzenia AssetDeliveryPolicy, zobacz sekcję [typy używane podczas definiowania AssetDeliveryPolicy](#types) .

```csharp
    static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {
        Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);
        
        Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
                new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
            {
                {AssetDeliveryPolicyConfigurationKey.PlayReadyLicenseAcquisitionUrl, acquisitionUrl.ToString()},
            };
    
            var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                    "AssetDeliveryPolicy",
                AssetDeliveryPolicyType.DynamicCommonEncryption,
                AssetDeliveryProtocol.SmoothStreaming,
                assetDeliveryPolicyConfiguration);
    
            // Add AssetDelivery Policy to the asset
            asset.DeliveryPolicies.Add(assetDeliveryPolicy);
    
            Console.WriteLine();
            Console.WriteLine("Adding Asset Delivery Policy: " +
                assetDeliveryPolicy.AssetDeliveryPolicyType);
     }
```

Azure Media Services również umożliwia dodanie szyfrowania Widevine. W poniższym przykładzie pokazano, że do zasad dostarczania elementów zawartości jest dodawany zarówno PlayReady, jak i Widevine.

```csharp
    static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {
        // Get the PlayReady license service URL.
        Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);


        // GetKeyDeliveryUrl for Widevine attaches the KID to the URL.
        // For example: https://amsaccount1.keydelivery.mediaservices.windows.net/Widevine/?KID=268a6dcb-18c8-4648-8c95-f46429e4927c.  
        // The WidevineBaseLicenseAcquisitionUrl (used below) also tells Dynamic Encryption 
        // to append /? KID =< keyId > to the end of the url when creating the manifest.
        // As a result Widevine license acquisition URL will have KID appended twice, 
        // so we need to remove the KID that in the URL when we call GetKeyDeliveryUrl.

        Uri widevineUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.Widevine);
        UriBuilder uriBuilder = new UriBuilder(widevineUrl);
        uriBuilder.Query = String.Empty;
        widevineUrl = uriBuilder.Uri;

        Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
        {
            {AssetDeliveryPolicyConfigurationKey.PlayReadyLicenseAcquisitionUrl, acquisitionUrl.ToString()},
            {AssetDeliveryPolicyConfigurationKey.WidevineLicenseAcquisitionUrl, widevineUrl.ToString()}

        };

        var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                "AssetDeliveryPolicy",
            AssetDeliveryPolicyType.DynamicCommonEncryption,
            AssetDeliveryProtocol.Dash,
            assetDeliveryPolicyConfiguration);


        // Add AssetDelivery Policy to the asset
        asset.DeliveryPolicies.Add(assetDeliveryPolicy);

    }
```
> [!NOTE]
> W przypadku szyfrowania za pomocą Widevine, można tylko dostarczyć przy użyciu ŁĄCZNIKa. Upewnij się, że w protokole dostarczania zasobów jest określona KRESKa.
> 
> 

## <a name="dynamicenvelopeencryption-asset-delivery-policy"></a>Zasady dostarczania elementów zawartości DynamicEnvelopeEncryption
Poniższa metoda **CreateAssetDeliveryPolicy** tworzy **AssetDeliveryPolicy** , który jest skonfigurowany do stosowania dynamicznego szyfrowania kopert (**DynamicEnvelopeEncryption**) do protokołów Smooth Streaming, HLS i pauz (Jeśli zdecydujesz się nie określać niektórych protokołów, zostaną one zablokowane na potrzeby przesyłania strumieniowego). Metoda przyjmuje dwa parametry: **zasób** (zasób, do którego chcesz zastosować zasady dostarczania) i **IContentKey** (klucz zawartości typu **EnvelopeEncryption** , aby uzyskać więcej informacji, zobacz: [Tworzenie klucza zawartości](media-services-dotnet-create-contentkey.md#envelope_contentkey)).

Aby uzyskać informacje na temat wartości, które można określić podczas tworzenia AssetDeliveryPolicy, zobacz sekcję [typy używane podczas definiowania AssetDeliveryPolicy](#types) .   

```csharp
    private static void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {

        //  Get the Key Delivery Base Url by removing the Query parameter.  The Dynamic Encryption service will
        //  automatically add the correct key identifier to the url when it generates the Envelope encrypted content
        //  manifest.  Omitting the IV will also cause the Dynamic Encryption service to generate a deterministic
        //  IV for the content automatically.  By using the EnvelopeBaseKeyAcquisitionUrl and omitting the IV, this
        //  allows the AssetDelivery policy to be reused by more than one asset.
        //
        Uri keyAcquisitionUri = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.BaselineHttp);
        UriBuilder uriBuilder = new UriBuilder(keyAcquisitionUri);
        uriBuilder.Query = String.Empty;
        keyAcquisitionUri = uriBuilder.Uri;

        // The following policy configuration specifies: 
        //   key url that will have KID=<Guid> appended to the envelope and
        //   the Initialization Vector (IV) to use for the envelope encryption.
        Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string> 
        {
            {AssetDeliveryPolicyConfigurationKey.EnvelopeBaseKeyAcquisitionUrl, keyAcquisitionUri.ToString()},
        };

        IAssetDeliveryPolicy assetDeliveryPolicy =
            _context.AssetDeliveryPolicies.Create(
                        "AssetDeliveryPolicy",
                        AssetDeliveryPolicyType.DynamicEnvelopeEncryption,
                        AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.Dash,
                        assetDeliveryPolicyConfiguration);

        // Add AssetDelivery Policy to the asset
        asset.DeliveryPolicies.Add(assetDeliveryPolicy);

        Console.WriteLine();
        Console.WriteLine("Adding Asset Delivery Policy: " + assetDeliveryPolicy.AssetDeliveryPolicyType);
    }
```

## <a name="types-used-when-defining-assetdeliverypolicy"></a><a id="types"></a>Typy używane podczas definiowania AssetDeliveryPolicy

### <a name="assetdeliveryprotocol"></a><a id="AssetDeliveryProtocol"></a>AssetDeliveryProtocol

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
### <a name="assetdeliverypolicytype"></a><a id="AssetDeliveryPolicyType"></a>AssetDeliveryPolicyType

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
### <a name="contentkeydeliverytype"></a><a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType

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
### <a name="assetdeliverypolicyconfigurationkey"></a><a id="AssetDeliveryPolicyConfigurationKey"></a>AssetDeliveryPolicyConfigurationKey

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

