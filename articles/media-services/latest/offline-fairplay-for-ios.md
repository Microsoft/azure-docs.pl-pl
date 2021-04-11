---
title: Media Services w wersji 3 FairPlay Streaming dla systemu iOS
description: Ten temat zawiera omówienie i pokazuje, jak używać Azure Media Services v3 do dynamicznego szyfrowania zawartości HTTP Live Streaming (HLS) przy użyciu usługi Apple FairPlay w trybie offline.
services: media-services
author: willzhan
manager: femila
ms.service: media-services
ms.topic: how-to
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: 0412ef303a301873d437e03e91c58c0a2e8e7e15
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105640731"
---
# <a name="offline-fairplay-streaming-for-ios-with-media-services-v3"></a>FairPlay przesyłania strumieniowego w trybie offline dla systemu iOS z Media Services v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

 Azure Media Services zawiera zestaw dobrze zaprojektowanych [usług ochrony zawartości](https://azure.microsoft.com/services/media-services/content-protection/) , które obejmują:

- Microsoft PlayReady
- Google Widevine
    
    Widevine to usługa świadczona przez firmę Google Inc. z zastrzeżeniem warunków użytkowania i zasad zachowania poufności informacji w firmie Google, Inc.
- Apple FairPlay
- Szyfrowanie AES-128

Szyfrowanie/Advanced szyfrowania (Digital Rights Management) jest wykonywane dynamicznie na żądanie różnych protokołów przesyłania strumieniowego. Usługi dostarczania kluczy odszyfrowywania licencji DRM/AES są również udostępniane przez Media Services.

Oprócz ochrony zawartości w przypadku przesyłania strumieniowego online przez różne protokoły przesyłania strumieniowego tryb offline dla chronionej zawartości jest również często żądaną funkcją. Obsługa trybu offline jest wymagana w następujących scenariuszach:

* Odtwarzanie, gdy połączenie internetowe nie jest dostępne, na przykład podczas podróży.
* Niektórzy dostawcy zawartości mogą nie zezwalać na dostarczanie licencji DRM poza granicami kraju/regionu. Jeśli użytkownicy chcą oglądać zawartość poza krajem/regionem, wymagane jest pobranie do trybu offline.
* W niektórych krajach/regionach dostępność i/lub przepustowość Internetu są nadal ograniczone. Użytkownicy mogą najpierw pobrać zawartość, aby obejrzeć ją w rozdzielczości wystarczającej do prawidłowego wyświetlania. W takim przypadku problem zwykle nie jest dostępny w sieci, ale ogranicza przepustowość sieci. Dostawca platformy wideo w trybie offline (OTT) z góry (OVP) ma zażądać pomocy online.

W tym artykule omówiono obsługę trybu offline FairPlay streaming (FPS), która jest przeznaczona dla urządzeń z systemem iOS 10 lub nowszym. Ta funkcja nie jest obsługiwana przez inne platformy firmy Apple, takie jak systemu watchOS, systemu tvOS lub Safari na macOS.

> [!NOTE]
> W przypadku funkcji DRM w trybie offline jest naliczana tylko jedna prośba o licencję podczas pobierania zawartości. Nie są naliczane opłaty za żadne błędy.

## <a name="prerequisites"></a>Wymagania wstępne

Przed zaimplementowaniem funkcji DRM w trybie offline dla programu FairPlay na urządzeniu z systemem iOS 10 +:

* Przejrzyj ochronę zawartości online dla FairPlay: 

    - [Wymagania licencyjne i konfiguracja technologii FairPlay firmy Apple](fairplay-license-overview.md)
    - [Używanie usługi dostarczania licencji i szyfrowania dynamicznego w technologii DRM](protect-with-drm.md)
    - Przykład platformy .NET obejmujący konfigurację strumienia FPS w trybie online: [ConfigureFairPlayPolicyOptions](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L505)
* Uzyskaj zestaw SDK FPS z sieci Apple Developer Network. Zestaw SDK FPS zawiera dwa składniki:

    - Zestaw SDK serwera FPS, który zawiera moduł zabezpieczeń (KSM), przykłady klienta, specyfikację i zestaw wektorów testów.
    - Pakiet wdrożeniowy FPS, który zawiera specyfikację D, wraz z instrukcjami dotyczącymi sposobu generowania certyfikatu FPS, klucza prywatnego określonego dla klienta i klucza tajnego aplikacji. Firma Apple emituje pakiet wdrożeniowy FPS tylko do licencjonowanych dostawców zawartości.
* Sklonuj https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git . 

    Aby dodać konfiguracje FairPlay, należy zmodyfikować kod [zaszyfrowany za pomocą technologii DRM przy użyciu platformy .NET](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM) .  

## <a name="configure-content-protection-in-azure-media-services"></a>Konfigurowanie ochrony zawartości w Azure Media Services

W metodzie [GetOrCreateContentKeyPolicyAsync](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L189) wykonaj następujące czynności:

Usuń komentarz z kodu, który konfiguruje opcję zasad FairPlay:

```csharp
ContentKeyPolicyFairPlayConfiguration fairplayConfig = ConfigureFairPlayPolicyOptions();
```

Usuń także komentarz do kodu, który dodaje CBCS ContentKeyPolicyOption do listy ContentKeyPolicyOptions

```csharp
options.Add(
    new ContentKeyPolicyOption()
    {
        Configuration = fairplayConfig,
        Restriction = restriction,
        Name = "ContentKeyPolicyOption_CBCS"
    });
```

## <a name="enable-offline-mode"></a>Włącz tryb offline

Aby włączyć tryb offline, Utwórz niestandardowe StreamingPolicy i użyj jego nazwy podczas tworzenia StreamingLocator w [CreateStreamingLocatorAsync](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L561).
 
```csharp
CommonEncryptionCbcs objStreamingPolicyInput= new CommonEncryptionCbcs()
{
    Drm = new CbcsDrmConfiguration()
    {
        FairPlay = new StreamingPolicyFairPlayConfiguration()
        {
            AllowPersistentLicense = true // This enables offline mode
        }
    },
    EnabledProtocols = new EnabledProtocols()
    {
        Hls = true,
        Dash = true // Even though DASH under CBCS is not supported for either CSF or CMAF, HLS-CMAF-CBCS uses DASH-CBCS fragments in its HLS playlist
    },

    ContentKeys = new StreamingPolicyContentKeys()
    {
        // Default key must be specified if keyToTrackMappings is present
        DefaultKey = new DefaultKey()
        {
            Label = "CBCS_DefaultKeyLabel"
        }
    }
}

```

Teraz Twoje konto Media Services jest skonfigurowane do dostarczania licencji FairPlay w trybie offline.

## <a name="sample-ios-player"></a>Przykładowy odtwarzacz iOS

Obsługa trybu offline FPS jest dostępna tylko w systemie iOS 10 i nowszych. Zestaw SDK serwera FPS (wersja 3,0 lub nowsza) zawiera dokument i przykład dla trybu offline w trybie online. Zestaw SDK serwera (w wersji 3,0 lub nowszej) zawiera następujące dwa elementy związane z trybem offline:

* Dokument: "odtwarzanie w trybie offline za pomocą FairPlay streaming i HTTP Live Streaming". Apple, 14 września 2016. W zestawie SDK serwera FPS w wersji 4,0 ten dokument jest scalany z głównym dokumentem FPS.
* Przykładowy kod: HLSCatalog Sample (część zestawu SDK serwera firmy Apple dla systemu) dla trybu offline systemu \FairPlay w wersji 3.1 \ Development\Client\ HLSCatalog_With_FPS \HLSCatalog\. W aplikacji przykładowej HLSCatalog następujące pliki kodu są używane do implementowania funkcji trybu offline:

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

![Strumienie aplikacji FairPlay dla systemu iOS w trybie offline](media/offline-fairplay-for-ios/offline-fairplay-ios-app-streams.png)

Jeśli zostały skonfigurowane, użyj własnego adresu URL wideo, adresu URL pozyskiwania licencji FairPlay oraz adresu URL certyfikatu aplikacji. Możesz też przejść do następnej sekcji zawierającej przykłady testowe.

## <a name="integrated-test"></a>Test zintegrowany

Trzy próbki testowe w Media Services obejmują następujące trzy scenariusze:

* Chronione za pomocą FPS z użyciem wideo, audio i alternatywnej ścieżki audio
* Chronione za pomocą FPS z użyciem wideo i audio, ale bez alternatywnej ścieżki audio
* Chroniona FPS, z tylko wideo i bez dźwięku

Te przykłady można znaleźć w [tej witrynie demonstracyjnej](https://aka.ms/poc#22)przy użyciu odpowiedniego certyfikatu aplikacji hostowanego w aplikacji sieci Web platformy Azure.
W przypadku wersji 3 lub 4 zestawu SDK serwera FPS, jeśli główna lista odtwarzania zawiera alternatywny dźwięk, w trybie offline jest odtwarzany tylko dźwięk. W związku z tym należy rozdzielić alternatywny dźwięk. Innymi słowy, drugi i trzeci przykłady wymienione wcześniej działają w trybie online i offline. Przykładowa podano w pierwszej kolejności dźwięk tylko w trybie offline, podczas gdy Transmisja strumieniowa w trybie online działa prawidłowo.

## <a name="offline-fairplay-questions"></a>Pytania FairPlay w trybie offline

Zobacz tematy dotyczące [FairPlay w trybie offline](questions-collection.md#why-does-only-audio-play-but-not-video-during-offline-mode).
