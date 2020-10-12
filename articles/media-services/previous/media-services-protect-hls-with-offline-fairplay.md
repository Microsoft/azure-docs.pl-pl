---
title: Ochrona zawartości HLS za pomocą trybu offline Apple FairPlay-Azure | Microsoft Docs
description: Ten temat zawiera omówienie i pokazuje, jak używać Azure Media Services do dynamicznego szyfrowania zawartości HTTP Live Streaming (HLS) przy użyciu usługi Apple FairPlay w trybie offline.
services: media-services
keywords: HLS, DRM, FairPlay streaming (FPS), offline, iOS 10
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2020
ms.author: willzhan
ms.reviewer: dwgeo
ms.custom: devx-track-csharp
ms.openlocfilehash: 5e003e17490c2e35ef3f8adfdef6de0377cda7d5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89259714"
---
# <a name="offline-fairplay-streaming-for-ios"></a>Przesyłanie strumieniowe w trybie offline przy użyciu technologii FairPlay na potrzeby systemu iOS

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector" title1="Wybierz używaną wersję Media Services:"]
> * [Wersja 3](../latest/offline-fairplay-for-ios.md)
> * [Wersja 2](media-services-protect-hls-with-offline-fairplay.md)

> [!NOTE]
> Do usługi Media Services w wersji 2 nie są już dodawane żadne nowe funkcje. <br/>Zapoznaj się z najnowszą wersją [Media Services wersja 3](../latest/index.yml). Zobacz też [wskazówki dotyczące migracji od wersji 2 do V3](../latest/migrate-from-v2-to-v3.md)

Azure Media Services zawiera zestaw dobrze zaprojektowanych [usług ochrony zawartości](https://azure.microsoft.com/services/media-services/content-protection/) , które obejmują:

- Microsoft PlayReady
- Google Widevine
- Apple FairPlay
- Szyfrowanie AES-128

Szyfrowanie/Advanced szyfrowania (Digital Rights Management) jest wykonywane dynamicznie na żądanie różnych protokołów przesyłania strumieniowego. Usługi dostarczania kluczy odszyfrowywania licencji DRM/AES są również udostępniane przez Media Services.

Oprócz ochrony zawartości w przypadku przesyłania strumieniowego online przez różne protokoły przesyłania strumieniowego tryb offline dla chronionej zawartości jest również często żądaną funkcją. Obsługa trybu offline jest wymagana w następujących scenariuszach:

* Odtwarzanie, gdy połączenie internetowe nie jest dostępne, na przykład podczas podróży.
* Niektórzy dostawcy zawartości mogą nie zezwalać na dostarczanie licencji DRM poza granicami kraju/regionu. Jeśli użytkownicy chcą oglądać zawartość poza krajem/regionem, wymagane jest pobranie do trybu offline.
* W niektórych krajach/regionach dostępność i/lub przepustowość Internetu są nadal ograniczone. Użytkownicy mogą najpierw pobrać zawartość, aby obejrzeć ją w rozdzielczości wystarczającej do prawidłowego wyświetlania. W takim przypadku problem zwykle nie jest dostępny w sieci, ale ogranicza przepustowość sieci. Dostawca platformy wideo w trybie offline (OTT) z góry (OVP) ma zażądać pomocy online.

W tym artykule omówiono obsługę trybu offline FairPlay streaming (FPS), która jest przeznaczona dla urządzeń z systemem iOS 10 lub nowszym. Ta funkcja nie jest obsługiwana przez inne platformy firmy Apple, takie jak systemu watchOS, systemu tvOS lub Safari na macOS.

## <a name="preliminary-steps"></a>Kroki wstępne
Przed zaimplementowaniem funkcji DRM w trybie offline dla programu FairPlay na urządzeniu z systemem iOS 10 +:

* Zapoznaj się z ochroną zawartości online dla usługi FairPlay. Aby uzyskać więcej informacji, zobacz następujące artykuły i przykłady:

    - [Firma Apple FairPlay Streaming for Azure Media Services jest ogólnie dostępna](https://azure.microsoft.com/blog/apple-FairPlay-streaming-for-azure-media-services-generally-available/)
    - [Ochrona zawartości HLS za pomocą oprogramowania Apple FairPlay lub Microsoft PlayReady](./media-services-protect-hls-with-fairplay.md)
    - [Przykład do przesyłania strumieniowego w trybie online FPS](https://azure.microsoft.com/resources/samples/media-services-dotnet-dynamic-encryption-with-FairPlay/)

* Uzyskaj zestaw SDK FPS z sieci Apple Developer Network. Zestaw SDK FPS zawiera dwa składniki:

    - Zestaw SDK serwera FPS, który zawiera moduł zabezpieczeń (KSM), przykłady klienta, specyfikację i zestaw wektorów testów.
    - Pakiet wdrożeniowy FPS, który zawiera specyfikację D, wraz z instrukcjami dotyczącymi sposobu generowania certyfikatu FPS, klucza prywatnego określonego dla klienta i klucza tajnego aplikacji. Firma Apple emituje pakiet wdrożeniowy FPS tylko do licencjonowanych dostawców zawartości.

## <a name="configuration-in-media-services"></a>Konfiguracja w Media Services
W przypadku konfiguracji trybu offline na sekundę za pomocą [zestawu sdk Media Services .NET](https://www.nuget.org/packages/windowsazure.mediaservices)Użyj zestawu SDK Media Services .NET w wersji 4.0.0.4 lub nowszej, który udostępnia interfejs API do konfigurowania trybu offline w trybie online.
Potrzebny jest również kod roboczy do konfigurowania ochrony zawartości w trybie online. Po uzyskaniu kodu w celu skonfigurowania ochrony zawartości w trybie online dla usługi FPS wymagane są tylko następujące dwie zmiany.

## <a name="code-change-in-the-fairplay-configuration"></a>Zmiana kodu w konfiguracji FairPlay
Pierwsza zmiana polega na zdefiniowaniu wartości logicznej "Włącz tryb offline" o nazwie objDRMSettings. EnableOfflineMode, która ma wartość true, jeśli włącza scenariusz DRM w trybie offline. W zależności od tego wskaźnika wprowadź następujące zmiany w konfiguracji FairPlay:

```csharp
if (objDRMSettings.EnableOfflineMode)
    {
        FairPlayConfiguration = Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(
            objX509Certificate2,
            pfxPassword,
            pfxPasswordId,
            askId,
            iv, 
            RentalAndLeaseKeyType.PersistentUnlimited,
            0x9999);
    }
    else
    {
        FairPlayConfiguration = Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(
            objX509Certificate2,
            pfxPassword,
            pfxPasswordId,
            askId,
            iv);
    }
```

## <a name="code-change-in-the-asset-delivery-policy-configuration"></a>Zmiana kodu w konfiguracji zasad dostarczania elementów zawartości
Druga zmiana polega na dodaniu trzeciego klucza do słownika<AssetDeliveryPolicyConfigurationKey, String>.
Dodaj AssetDeliveryPolicyConfigurationKey, jak pokazano poniżej:
 
```csharp
// FPS offline mode
    if (drmSettings.EnableOfflineMode)
    {
        objDictionary_AssetDeliveryPolicyConfigurationKey.Add(AssetDeliveryPolicyConfigurationKey.AllowPersistentLicense, "true");
        Console.WriteLine("FPS OFFLINE MODE: AssetDeliveryPolicyConfigurationKey.AllowPersistentLicense added into asset delivery policy configuration.");
    }

    // for IAssetDelivery for FPS
    IAssetDeliveryPolicy objIAssetDeliveryPolicy = objCloudMediaContext.AssetDeliveryPolicies.Create(
            drmSettings.AssetDeliveryPolicyName,
            AssetDeliveryPolicyType.DynamicCommonEncryptionCbcs,
            AssetDeliveryProtocol.HLS,
            objDictionary_AssetDeliveryPolicyConfigurationKey);
```

Po wykonaniu tego kroku ciąg <Dictionary_AssetDeliveryPolicyConfigurationKey> w zasadach dostarczania zasobów FPS zawiera następujące trzy wpisy:

* AssetDeliveryPolicyConfigurationKey. FairPlayBaseLicenseAcquisitionUrl lub AssetDeliveryPolicyConfigurationKey. FairPlayLicenseAcquisitionUrl, w zależności od czynników, takich jak/s/serwer kluczy, oraz tego, czy te same zasady dostarczania zasobów są używane w wielu zasobach
* AssetDeliveryPolicyConfigurationKey.CommonEncryptionIVForCbcs
* AssetDeliveryPolicyConfigurationKey.AllowPersistentLicense

Teraz Twoje konto Media Services jest skonfigurowane do dostarczania licencji FairPlay w trybie offline.

## <a name="sample-ios-player"></a>Przykładowy odtwarzacz iOS
Obsługa trybu offline FPS jest dostępna tylko w systemie iOS 10 i nowszych. Zestaw SDK serwera FPS (wersja 3,0 lub nowsza) zawiera dokument i przykład dla trybu offline w trybie online. Zestaw SDK serwera (w wersji 3,0 lub nowszej) zawiera następujące dwa elementy związane z trybem offline:

* Dokument: "odtwarzanie w trybie offline za pomocą FairPlay streaming i HTTP Live Streaming". Apple, 14 września 2016. W zestawie SDK serwera FPS w wersji 4,0 ten dokument jest scalany z głównym dokumentem FPS.
* Przykładowy kod: HLSCatalog przykład dla trybu offline w trybie online w zestawie SDK \FairPlay Streaming Server w wersji 3.1 \ Development\Client\ HLSCatalog_With_FPS \HLSCatalog\. W aplikacji przykładowej HLSCatalog następujące pliki kodu są używane do implementowania funkcji trybu offline:

    - Plik kodu AssetPersistenceManager. SWIFT: AssetPersistenceManager jest główną klasą w tym przykładzie, który ilustruje sposób:

        - Zarządzaj pobieranymi strumieniami HLS, takimi jak interfejsy API używane do uruchamiania i anulowania pobierania oraz usuwania istniejących zasobów poza urządzeniami.
        - Monitoruj postęp pobierania.
    - Pliki kodu AssetListTableViewController. Swift i AssetListTableViewCell. SWIFT: AssetListTableViewController jest głównym interfejsem tego przykładu. Zawiera listę zasobów, których można użyć do odtwarzania, pobrania, usunięcia lub anulowania pobierania. 

W tych krokach pokazano, jak skonfigurować uruchomiony odtwarzacz systemu iOS. Przy założeniu, że zaczynasz od przykładu HLSCatalog w zestawie SDK serwera FPS w wersji 4.0.1, wprowadź następujące zmiany kodu:

W HLSCatalog\Shared\Managers\ContentKeyDelegate.swift Zaimplementuj metodę `requestContentKeyFromKeySecurityModule(spcData: Data, assetID: String)` przy użyciu następującego kodu. Niech "drmUr" będzie zmienną przypisaną do adresu URL HLS.

```swift
    var ckcData: Data? = nil
    
    let semaphore = DispatchSemaphore(value: 0)
    let postString = "spc=\(spcData.base64EncodedString())&assetId=\(assetIDString)"
    
    if let postData = postString.data(using: .ascii, allowLossyConversion: true), let drmServerUrl = URL(string: self.drmUrl) {
        var request = URLRequest(url: drmServerUrl)
        request.httpMethod = "POST"
        request.setValue(String(postData.count), forHTTPHeaderField: "Content-Length")
        request.setValue("application/x-www-form-urlencoded", forHTTPHeaderField: "Content-Type")
        request.httpBody = postData
        
        URLSession.shared.dataTask(with: request) { (data, _, error) in
            if let data = data, var responseString = String(data: data, encoding: .utf8) {
                responseString = responseString.replacingOccurrences(of: "<ckc>", with: "").replacingOccurrences(of: "</ckc>", with: "")
                ckcData = Data(base64Encoded: responseString)
            } else {
                print("Error encountered while fetching FairPlay license for URL: \(self.drmUrl), \(error?.localizedDescription ?? "Unknown error")")
            }
            
            semaphore.signal()
            }.resume()
    } else {
        fatalError("Invalid post data")
    }
    
    semaphore.wait()
    return ckcData
```

W HLSCatalog\Shared\Managers\ContentKeyDelegate.swift Zaimplementuj metodę `requestApplicationCertificate()` . Ta implementacja zależy od tego, czy osadzasz certyfikat (tylko klucz publiczny) z urządzeniem lub hostuje certyfikat w sieci Web. W poniższej implementacji użyto certyfikatu aplikacji hostowanej użytego w przykładach testu. Let "certUrl" to zmienna, która zawiera adres URL certyfikatu aplikacji.

```swift
func requestApplicationCertificate() throws -> Data {

        var applicationCertificate: Data? = nil
        do {
            applicationCertificate = try Data(contentsOf: URL(string: certUrl)!)
        } catch {
            print("Error loading FairPlay application certificate: \(error)")
        }
        
        return applicationCertificate
    }
```

W przypadku końcowej zintegrowanego testu zarówno adres URL wideo, jak i adres URL certyfikatu aplikacji są podane w sekcji "test zintegrowany".

W HLSCatalog\Shared\Resources\Streams.plist Dodaj adres URL testu wideo. W polu Identyfikator klucza zawartości Użyj adresu URL pozyskiwania licencji FairPlay z protokołem SKD jako wartość unikatową.

![Strumienie aplikacji FairPlay dla systemu iOS w trybie offline](media/media-services-protect-hls-with-offline-FairPlay/media-services-offline-FairPlay-ios-app-streams.png)

Jeśli zostały skonfigurowane, użyj własnego adresu URL wideo, adresu URL pozyskiwania licencji FairPlay oraz adresu URL certyfikatu aplikacji. Możesz też przejść do następnej sekcji zawierającej przykłady testowe.

## <a name="integrated-test"></a>Test zintegrowany
Trzy próbki testowe w Media Services obejmują następujące trzy scenariusze:

* Chronione za pomocą FPS z użyciem wideo, audio i alternatywnej ścieżki audio
* Chronione za pomocą FPS z użyciem wideo i audio, ale bez alternatywnej ścieżki audio
* Chroniona FPS, z tylko wideo i bez dźwięku

Te przykłady można znaleźć w [tej witrynie demonstracyjnej](https://aka.ms/poc#22)przy użyciu odpowiedniego certyfikatu aplikacji hostowanego w aplikacji sieci Web platformy Azure.
W przypadku wersji 3 lub 4 zestawu SDK serwera FPS, jeśli główna lista odtwarzania zawiera alternatywny dźwięk, w trybie offline jest odtwarzany tylko dźwięk. W związku z tym należy rozdzielić alternatywny dźwięk. Innymi słowy, drugi i trzeci przykłady wymienione wcześniej działają w trybie online i offline. Przykładowa podano w pierwszej kolejności dźwięk tylko w trybie offline, podczas gdy Transmisja strumieniowa w trybie online działa prawidłowo.

## <a name="faq"></a>Najczęściej zadawane pytania
Poniższe często zadawane pytania zapewniają pomoc w rozwiązywaniu problemów:

- **Dlaczego dźwięk jest odtwarzany tylko w trybie offline, ale nie wideo?** Takie zachowanie wydaje się być projektem przykładowej aplikacji. Jeśli istnieje alternatywna ścieżka audio (w przypadku programu HLS) w trybie offline, zarówno system iOS 10, jak i iOS 11 domyślnie są alternatywną ścieżką audio. Aby zrekompensować to zachowanie w trybie offline w trybie online, Usuń alternatywną ścieżkę audio ze strumienia. Aby to zrobić na Media Services, Dodaj filtr manifestu dynamicznego "audio-Only = false". Innymi słowy, adres URL HLS ma koniec. ISM/manifest (format = M3U8-AAPL, audio-Only = false). 
- **Dlaczego nadal Odtwarzaj tylko dźwięk bez wideo w trybie offline po dodaniu tylko audio = FAŁSZ?** W zależności od projektu klucza pamięci podręcznej usługi Content Delivery Network (CDN) zawartość może być buforowana. Przeczyść pamięć podręczną.
- **Czy tryb offline jest również obsługiwany w systemie iOS 11 oprócz systemu iOS 10?** Tak. Tryb offline FPS jest obsługiwany w systemach iOS 10 i iOS 11.
- **Dlaczego nie mogę znaleźć dokumentu "odtwarzanie w trybie offline za pomocą FairPlay streaming i HTTP Live Streaming" w zestawie SDK serwera FPS?** Ponieważ zestaw FPS SDK serwera w wersji 4, ten dokument został scalony w przewodniku programowania strumieniowego FairPlay.
- **Co oznacza ostatni parametr w następującym interfejsie API dla trybu online z FPS?**
`Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(objX509Certificate2, pfxPassword, pfxPasswordId, askId, iv, RentalAndLeaseKeyType.PersistentUnlimited, 0x9999);`

    Aby zapoznać się z dokumentacją dla tego interfejsu API, zobacz [FairPlayConfiguration. CreateSerializedFairPlayOptionConfiguration](/dotnet/api/microsoft.windowsazure.mediaservices.client.fairplay.fairplayconfiguration.createserializedfairplayoptionconfiguration?view=azure-dotnet). Parametr reprezentuje czas trwania dzierżawy w trybie offline, a drugi jako jednostkę.
- **Co to jest struktura plików pobierana/w trybie offline na urządzeniach z systemem iOS?** Pobrana struktura plików na urządzeniu z systemem iOS wygląda podobnie do poniższego zrzutu ekranu. `_keys`Folder przechowuje pobrane licencje FPS z jednym plikiem magazynu dla każdego hosta usługi licencjonowania. `.movpkg`Folder przechowuje zawartość audio i wideo. Pierwszy folder o nazwie kończącej się znakiem łącznika, po którym następuje wartość liczbowa, zawiera zawartość wideo. Wartość liczbowa jest PeakBandwidthą dla odwzorowań wideo. Drugi folder o nazwie kończącej się znakiem łącznika, po którym następuje 0, zawiera zawartość audio. Trzeci folder o nazwie "Data" zawiera główną listę odtwarzania zawartości FPS. Na koniec boot.xml zawiera pełny opis `.movpkg` zawartości folderu. 

![Struktura pliku przykładowej aplikacji w trybie offline FairPlay iOS](media/media-services-protect-hls-with-offline-FairPlay/media-services-offline-FairPlay-file-structure.png)

Przykładowy plik boot.xml:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<HLSMoviePackage xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://apple.com/IMG/Schemas/HLSMoviePackage" xsi:schemaLocation="http://apple.com/IMG/Schemas/HLSMoviePackage /System/Library/Schemas/HLSMoviePackage.xsd">
  <Version>1.0</Version>
  <HLSMoviePackageType>PersistedStore</HLSMoviePackageType>
  <Streams>
    <Stream ID="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" Path="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(127000)/Manifest(aac_eng_2_127,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
    <Stream ID="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" Path="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(161000)/Manifest(video,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
  </Streams>
  <MasterPlaylist>
    <NetworkURL>https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/manifest(format=m3u8-aapl,audio-only=false)</NetworkURL>
  </MasterPlaylist>
  <DataItems Directory="Data">
    <DataItem>
      <ID>CB50F631-8227-477A-BCEC-365BBF12BCC0</ID>
      <Category>Playlist</Category>
      <Name>master.m3u8</Name>
      <DataPath>Playlist-master.m3u8-CB50F631-8227-477A-BCEC-365BBF12BCC0.data</DataPath>
      <Role>Master</Role>
    </DataItem>
  </DataItems>
</HLSMoviePackage>
```

## <a name="additional-notes"></a>Uwagi dodatkowe

* Widevine to usługa świadczona przez firmę Google Inc. z zastrzeżeniem warunków użytkowania i zasad zachowania poufności informacji w firmie Google, Inc.

## <a name="summary"></a>Podsumowanie
Ten dokument zawiera następujące kroki i informacje, których można użyć do zaimplementowania trybu offline FPS:

* Media Services Konfiguracja ochrony zawartości za pośrednictwem interfejsu API platformy Media Services .NET konfiguruje dynamiczne szyfrowanie FairPlay i dostarczanie licencji FairPlay w Media Services.
* Odtwarzacz systemu iOS oparty na przykładzie z zestawu SDK serwera w warstwie FPS konfiguruje odtwarzacz systemu iOS, który może odtwarzać zawartość FPS w trybie przesyłania strumieniowego online lub w trybie offline.
* Przykładowe wideo FPS służy do testowania trybu offline i przesyłania strumieniowego online.
* Często zadawane pytania dotyczące trybu offline FPS.

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]
