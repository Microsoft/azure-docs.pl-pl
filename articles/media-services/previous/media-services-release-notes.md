---
title: Informacje o wersji Azure Media Services | Microsoft Docs
description: W tym artykule omówiono informacje o wersji Microsoft Azure Media Services V2.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: media
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2019
ms.author: juliako
ms.custom: devx-track-csharp
ms.openlocfilehash: bd4f50365ced61e35ae6d10a9763a0f9815098d7
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/14/2020
ms.locfileid: "92014107"
---
# <a name="azure-media-services-release-notes"></a>Informacje o wersji Azure Media Services

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Te informacje o wersji Azure Media Services podsumowują zmiany z poprzednich wersji i znanych problemów.

> [!NOTE]
> Do usługi Media Services w wersji 2 nie są już dodawane żadne nowe funkcje. <br/>Zapoznaj się z najnowszą wersją [Media Services wersja 3](../latest/index.yml). Zobacz też [wskazówki dotyczące migracji od wersji 2 do V3](../latest/migrate-from-v2-to-v3.md)

Chcemy poznać naszych klientów, aby móc skupić się na rozwiązywaniu problemów, które mają wpływ na Ciebie. Aby zgłosić problem lub zadawać pytania, Prześlij wpis na stronie [Azure Media Services MSDN forum]. 

## <a name="known-issues"></a><a name="issues"></a>Znane problemy
### <a name="media-services-general-issues"></a><a name="general_issues"></a>Problemy ogólne Media Services

| Problem | Description |
| --- | --- |
| W interfejsie API REST nie są dostępne kilka typowych nagłówków HTTP. |W przypadku tworzenia aplikacji Media Services przy użyciu interfejsu API REST można stwierdzić, że niektóre typowe pola nagłówka HTTP (w tym identyfikator żądania klienta, identyfikator żądania i identyfikator klienta żądania) nie są obsługiwane. Nagłówki zostaną dodane w przyszłej aktualizacji. |
| Procent — kodowanie nie jest dozwolone. |Media Services używa wartości właściwości IAssetFile.Name podczas kompilowania adresów URL dla zawartości przesyłania strumieniowego (na przykład `http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters` ). Z tego powodu nie jest dozwolone kodowanie procentowo. Wartość właściwości Name nie może zawierać żadnego z następujących [znaków:%-Encoding](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters), które są zastrzeżone.! * ' ();: @ &= + $,/?% # [] ". Ponadto dla rozszerzenia nazwy pliku może istnieć tylko jeden ".". |
| Metoda ListBlobs, która jest częścią zestawu SDK usługi Azure Storage w wersji 3. x, kończy się niepowodzeniem. |Media Services generuje adresy URL sygnatury dostępu współdzielonego na podstawie wersji [2012-02-12](/rest/api/storageservices/version-2012-02-12) . Jeśli chcesz użyć zestawu SDK magazynu do wyświetlania obiektów BLOB w kontenerze obiektów blob, użyj metody [CloudBlobContainer. ListBlobs](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobs) , która jest częścią zestawu SDK magazynu w wersji 2. x. |
| Mechanizm ograniczania Media Services ogranicza użycie zasobów dla aplikacji, które wysyłają nadmierne żądania do usługi. Usługa może zwrócić kod stanu HTTP "Usługa niedostępna" 503. |Aby uzyskać więcej informacji, zobacz Opis kodu stanu HTTP 503 w [Media Services kody błędów](media-services-encoding-error-codes.md). |
| Gdy wykonujesz zapytania o jednostki, limit 1 000 jednostek jest zwracany jednocześnie, ponieważ publiczna wersja REST 2 ogranicza wyniki zapytania do 1 000 wyników. |Użyj funkcji Skip i Take (.NET)/Top (REST) zgodnie z opisem w [tym przykładzie platformy .NET](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) i [przykładowym interfejsem API REST](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities). |
| Niektórzy klienci mogą przechodzić przez problem z tagiem REPEAT w manifeście Smooth Streaming. |Aby uzyskać więcej informacji, zobacz [tę sekcję](media-services-deliver-content-overview.md#known-issues). |
| Nie można serializować Media Services obiektów zestawu SDK platformy .NET. w związku z tym wynik nie współpracuje z usługą Azure cache for Redis. |Jeśli próbujesz serializować obiekt elementu zawartości zestawu SDK w celu dodania go do usługi Azure cache for Redis, zostanie zgłoszony wyjątek. |
|Interfejs API REST reaguje na komunikat o błędzie informujący o tym, że nie można uzyskać dostępu do filtru przy użyciu tej wersji interfejsu API REST "podczas próby pobrania filtru na poziomie zasobu lub konta.|Filtr został utworzony lub zmodyfikowany przy użyciu nowszej wersji interfejsu API, która jest używana do próby pobrania filtru. Taka sytuacja może wystąpić, jeśli dwie wersje interfejsu API są używane przez kod lub narzędzia używane przez klienta.  Najlepszym rozwiązaniem jest uaktualnienie kodu lub narzędzi w celu użycia nowszych lub dwóch wersji interfejsu API.|

## <a name="rest-api-version-history"></a><a name="rest_version_history"></a>Historia wersji interfejsu API REST
Informacje o historii wersji interfejsu API REST Media Services można znaleźć w [dokumentacji interfejsu API rest Azure Media Services].

## <a name="september-2020"></a>Wrzesień 2020

Następujące właściwości w wersji 2 nie będą już wypełniane historycznymi danymi postępu zadania:

* [HistoricalEvents](/dotnet/api/microsoft.windowsazure.mediaservices.client.itask.historicalevents)
* [PerfMessage](/dotnet/api/microsoft.windowsazure.mediaservices.client.itask.perfmessage)

Aby uzyskać historię zadań, należy używać powiadomień o zadaniach w wersji 2 za pośrednictwem elementów webhook lub kolejkowanie komunikatów za pomocą punktów końcowych powiadomień. Aby uzyskać więcej informacji, zobacz:

* [Monitorowanie Media Services powiadomień zadań przy użyciu usługi Azure queue storage](media-services-dotnet-check-job-progress-with-queues.md)
* [Monitorowanie powiadomień dotyczących zadań Media Services za pomocą elementów webhook platformy Azure](media-services-dotnet-check-job-progress-with-webhooks.md)

## <a name="february-2020"></a>Luty 2020 r.

Niektóre procesory multimediów analitycznych zostaną wycofane. Aby uzyskać daty wycofania, zobacz temat [starsze składniki](legacy-components.md) .

## <a name="september-2019"></a>Wrzesień 2019 r.

### <a name="deprecation-of-media-processors"></a>Wycofanie procesorów multimediów

Ogłaszamy przestarzałe *Azure Media Indexer* i *Azure Media Indexer 2 wersji zapoznawczej*. [Azure Media Services Video Indexer](../video-indexer/index.yml) zastępuje te starsze procesory nośników.

Aby uzyskać daty wycofania, zobacz temat ten [starszy składnik](legacy-components.md) .

Zobacz również [Migrowanie z Azure Media Indexer i Azure Media Indexer 2 do Azure Media Services Video Indexer](migrate-indexer-v1-v2.md).

## <a name="august-2019"></a>Sierpień 2019 r.

### <a name="deprecation-of-media-processors"></a>Wycofanie procesorów multimediów

Ogłaszamy przestarzałe procesory multimediów w *systemie Windows Azure Media Encoder* (WAME) i *Azure Media Encoder* (azwa). Aby uzyskać daty wycofania, zobacz temat ten [starszy składnik](legacy-components.md) .

Aby uzyskać szczegółowe informacje, zobacz [Migrowanie WAME do Media Encoder Standard](./migrate-windows-azure-media-encoder.md) i [Migrowanie azwa do Media Encoder Standard](./migrate-azure-media-encoder.md).

## <a name="march-2019"></a>Marzec 2019 r.

Funkcja w wersji zapoznawczej Azure Media Services była przestarzała.

## <a name="december-2018"></a>Grudzień 2018 r.

Funkcja w wersji zapoznawczej Azure Media Services zostanie wkrótce wycofana. Od 19 grudnia 2018, Media Services nie będą już wprowadzać zmian ani ulepszeń w odniesieniu do multimediów. 29 marca 2019, zostanie wycofana i nie będzie już dostępna.

## <a name="october-2018"></a>Październik 2018 r.

### <a name="cmaf-support"></a>Obsługa CMAF

Obsługa szyfrowania CMAF i "cbcs" dla odtwarzaczy Apple HLS (iOS 11 +) i MPEG-PAUZ, które obsługują CMAF.

### <a name="web-vtt-thumbnail-sprites"></a>Miniatury ikon VTT sieci Web

Teraz możesz używać Media Services do generowania miniaturowych ikon VTT sieci Web przy użyciu interfejsów API v2. Aby uzyskać więcej informacji, zobacz [generowanie miniatury Sprite](generate-thumbnail-sprite.md).

## <a name="july-2018"></a>Lipiec 2018 r.

Najnowsza wersja usługi zawiera niewielkie zmiany formatowania komunikatów o błędach zwracanych przez usługę, gdy zadanie nie powiedzie się, w odniesieniu do tego, w jaki sposób jest podzielony na dwa lub więcej wierszy.

## <a name="may-2018"></a>Maj 2018 r. 

Od 12 maja 2018 kanały na żywo nie będą już obsługiwać protokołu pozyskiwania strumienia transportowego RTP/MPEG-2. Przeprowadź migrację z protokołów RTP/MPEG-2 do protokołu RTMP lub pofragmentowanych plików MP4 (Smooth Streaming).

## <a name="october-2017-release"></a>Wersja z października 2017 r.
> [!IMPORTANT] 
> Media Services jest przestarzałą obsługą kluczy uwierzytelniania usługi Azure Access Control Service. 22 czerwca 2018 nie można już uwierzytelniać się za pomocą Media Services zaplecza za pośrednictwem kodu przy użyciu kluczy Access Control Service. Musisz zaktualizować swój kod, aby używać Azure Active Directory (Azure AD) na potrzeby [uwierzytelniania opartego na usłudze Azure AD](media-services-use-aad-auth-to-access-ams-api.md). Obejrzyj informacje o ostrzeżeniach dotyczących tej zmiany w Azure Portal.

### <a name="updates-for-october-2017"></a>Aktualizacje dla października 2017
#### <a name="sdks"></a>Zestawy SDK
* Zestaw .NET SDK został zaktualizowany do obsługi uwierzytelniania usługi Azure AD. Obsługa uwierzytelniania Access Control Service została usunięta z najnowszego zestawu .NET SDK w systemie Nuget.org, co pozwala na szybszą migrację do usługi Azure AD. 
* Zestaw SDK języka JAVA został zaktualizowany do obsługi uwierzytelniania usługi Azure AD. Do zestawu Java SDK dodano obsługę uwierzytelniania przy użyciu usługi Azure AD. Aby uzyskać informacje na temat używania zestawu Java SDK z Media Services, zobacz Wprowadzenie do [zestawu SDK klienta Java dla Azure Media Services](media-services-java-how-to-use.md)

#### <a name="file-based-encoding"></a>Kodowanie oparte na plikach
* Możesz teraz użyć kodera Premium, aby kodować zawartość do kodera wideo o wysokiej wydajności (HEVC). Jeśli wybierzesz pozycję H. 265 na inne kodery-dekoder, na przykład H. 264, nie ma żadnego wpływu na ceny. Informacje o licencjach patentowych HEVC można znaleźć w temacie dotyczącym [usług online](https://azure.microsoft.com/support/legal/).
* Dla źródłowego wideo zakodowanego za pomocą kodera wideo H. 265 (HEVC), takiego jak wideo przechwycone przy użyciu iOS11 lub GoPro Hero 6, można teraz użyć kodera Premium lub standardowego kodera do kodowania tych filmów wideo. Aby uzyskać informacje na temat licencji patentowych, zobacz temat [usługi online](https://azure.microsoft.com/support/legal/).
* W przypadku zawartości zawierającej wiele ścieżek dźwiękowych języka należy prawidłowo oznaczyć wartości języka zgodnie ze specyfikacją odpowiedniego formatu pliku (na przykład ISO MP4). Następnie można użyć kodera standardowego do kodowania zawartości do przesyłania strumieniowego. Wynikowy lokalizator przesyłania strumieniowego zawiera listę dostępnych języków dźwiękowych.
* Koder w warstwie Standardowa obsługuje teraz dwa nowe ustawienia domyślne systemu "audio", "AAC audio" i "AAC dobry Quality". Oba wytwarzają dane wyjściowe kodowania audio (AAC) stereo przy częstotliwości bitowej 128 kb/s i 192 kb/s.
* Koder w warstwie Premium obsługuje teraz formaty plików QuickTime/MOV jako dane wejściowe. Koder-dekoder wideo musi być jednym z [typów Apple ProRes wymienionych w tym artykule w witrynie GitHub](./media-services-media-encoder-standard-formats.md). Dźwięk musi być AAC lub impulsem modulacji kodu (PCM). Koder w warstwie Premium nie obsługuje na przykład wideo WSPÓŁBIEŻNE/DVCPro opakowanego w pliki QuickTime/MOV jako dane wejściowe. Koder standardowy obsługuje te kodery-dekoder wideo.
* W koderach wprowadzono następujące poprawki błędów:

    * Teraz można przesyłać zadania przy użyciu wejściowego zasobu. Po zakończeniu tych zadań można zmodyfikować element zawartości (na przykład dodać, usunąć lub zmienić jego nazwę) i przesłać dodatkowe zadania.
    * Ulepszono jakość miniatur JPEG produkowanych przez koder standardowy.
    * Koder standardowy obsługuje metadane wejściowe i Generowanie miniatur w bardzo krótkim wideo.
    * Ulepszenia dekodera H. 264 używane w standardowym Koderie eliminują niektóre rzadkie artefakty. 

#### <a name="media-analytics"></a>Media Analytics
Ogólna dostępność Azure Media Redactor: ten procesor multimedialny wykonuje zachowywanie anonimowości przez rozmycie twarzy wybranych osób i jest idealnym rozwiązaniem do użycia w scenariuszach bezpieczeństwa publicznego i multimediów. 

Aby zapoznać się z omówieniem tego nowego procesora, zobacz [ten wpis w blogu](https://azure.microsoft.com/blog/azure-media-redactor/). Aby uzyskać informacje na temat dokumentacji i ustawień, zobacz [redagowanie twarzy przy użyciu Azure Media Analytics](media-services-face-redaction.md).



## <a name="june-2017-release"></a>Wydanie z czerwca 2017

Media Services teraz obsługuje [uwierzytelnianie oparte na usłudze Azure AD](media-services-use-aad-auth-to-access-ams-api.md).

> [!IMPORTANT]
> Obecnie Media Services obsługuje model uwierzytelniania Access Control Service. Autoryzacja Access Control Service zostanie zaniechana 1 czerwca 2018. Zalecamy jak najszybszą migrację do modelu uwierzytelniania za pomocą usługi Azure AD.

## <a name="march-2017-release"></a>Wydanie z marca 2017

Możesz teraz użyć kodera standardowego, aby [automatycznie wygenerować drabinę szybkości transmisji bitów](media-services-autogen-bitrate-ladder-with-mes.md) , określając ciąg "adaptacyjne przesyłanie strumieniowe" w trakcie tworzenia zadania kodowania. Aby zakodować wideo do przesyłania strumieniowego za pomocą Media Services, użyj ustawienia wstępnego "adaptacyjne przesyłanie strumieniowe". Aby dostosować ustawienie wstępne kodowania dla danego scenariusza, możesz zacząć od [tych ustawień wstępnych](media-services-mes-presets-overview.md).

Teraz można użyć Media Encoder Standard lub Media Encoder Premium Workflow do [utworzenia zadania kodowania, które generuje fragmenty fMP4](media-services-generate-fmp4-chunks.md). 

## <a name="february-2017-release"></a>Wydanie z lutego 2017

Od 1 kwietnia 2017, każdy rekord zadania na koncie starszej niż 90 dni jest automatycznie usuwany wraz ze skojarzonymi z nim rekordami zadań. Usuwanie odbywa się nawet wtedy, gdy całkowita liczba rekordów przekracza limit przydziału. Aby zarchiwizować informacje o zadaniu/zadaniu, można użyć kodu opisanego w temacie [Zarządzanie zasobami i powiązanymi jednostkami przy użyciu zestawu SDK platformy .net Media Services](media-services-dotnet-manage-entities.md).

## <a name="january-2017-release"></a>Wydanie z stycznia 2017

W Media Services punkt końcowy przesyłania strumieniowego reprezentuje usługę przesyłania strumieniowego, która umożliwia dostarczanie zawartości bezpośrednio do aplikacji odtwarzacza klienta lub do usługi Content Delivery Network (CDN) w celu dalszej dystrybucji. Media Services zapewnia również bezproblemowe integrację z usługą Azure Content Delivery Network. Strumień wychodzący z usługi StreamingEndpoint może być strumieniem na żywo, wideo na żądanie lub pobieranym progresywnym zasobem na koncie Media Services. Każde konto Media Services zawiera domyślny punkt końcowy przesyłania strumieniowego. W ramach konta można utworzyć dodatkowe punkty końcowe przesyłania strumieniowego. 

Istnieją dwie wersje punktów końcowych przesyłania strumieniowego, 1,0 i 2,0. Od 10 stycznia 2017 wszystkie nowo utworzone konta Media Services obejmują domyślny punkt końcowy przesyłania strumieniowego w wersji 2,0. Dodatkowe punkty końcowe przesyłania strumieniowego dodawane do tego konta również są w wersji 2,0. Ta zmiana nie ma wpływu na istniejące konta. Istniejące punkty końcowe przesyłania strumieniowego są w wersji 1,0 i można je uaktualnić do wersji 2,0. Ta zmiana obejmuje zachowanie, rozliczenia i zmiany funkcji. Aby uzyskać więcej informacji, zobacz [Streaming endpoints overview (Omówienie punktów końcowych przesyłania strumieniowego)](media-services-streaming-endpoints-overview.md).

Począwszy od wersji 2,15 Media Services dodano następujące właściwości do jednostki punktu końcowego przesyłania strumieniowego:

* CdnProvider 
* CdnProfile
* FreeTrialEndTime 
* StreamingEndpointVersion 

Aby uzyskać więcej informacji o tych właściwościach, zobacz [StreamingEndpoint](/rest/api/media/operations/streamingendpoint). 

## <a name="december-2016-release"></a>Wydanie z grudnia 2016

 Teraz można użyć Media Services, aby uzyskać dostęp do danych telemetrycznych/metryk dla swoich usług. Korzystając z bieżącej wersji Media Services, można zbierać dane telemetryczne dotyczące kanału na żywo, punktu końcowego przesyłania strumieniowego i obiektów archiwum. Aby uzyskać więcej informacji, zobacz [Media Services telemetrii](media-services-telemetry-overview.md).

## <a name="july-2016-release"></a><a name="july_changes16"></a>Wydanie 2016 lipca
### <a name="updates-to-the-manifest-file-ism-generated-by-encoding-tasks"></a>Aktualizacje pliku manifestu (*. ISM) generowane przez zadania kodowania
Gdy zadanie kodowania jest przesyłane do Media Encoder Standard lub Media Encoder Premium, zadanie kodowania generuje [plik manifestu przesyłania strumieniowego](media-services-deliver-content-overview.md) (*. ISM) w wyjściowym elemencie zawartości. W najnowszej wersji usługi została zaktualizowana Składnia tego pliku manifestu przesyłania strumieniowego.

> [!NOTE]
> Składnia pliku manifestu przesyłania strumieniowego (ISM) jest zarezerwowana do użytku wewnętrznego. Może ona ulec zmianie w przyszłych wersjach. Nie należy modyfikować ani manipulować zawartością tego pliku.
> 
> 

### <a name="a-new-client-manifest-ismc-file-is-generated-in-the-output-asset-when-an-encoding-task-outputs-one-or-more-mp4-files"></a>Nowy manifest klienta (*. ISMC) jest generowany w wyjściowym elemencie zawartości, gdy zadanie kodowania wyprowadza co najmniej jeden plik MP4
Począwszy od najnowszej wersji usługi, po zakończeniu zadania kodowania, które generuje jeden lub więcej plików MP4, wyjściowy element zawartości zawiera również plik manifestu klienta przesyłania strumieniowego (*. ISMC). Plik. ISMC pomaga zwiększyć wydajność dynamicznego przesyłania strumieniowego. 

> [!NOTE]
> Składnia pliku manifestu klienta (. ISMC) jest zarezerwowana do użytku wewnętrznego. Może ona ulec zmianie w przyszłych wersjach. Nie należy modyfikować ani manipulować zawartością tego pliku.
> 
> 

Aby uzyskać więcej informacji, zobacz [ten blog](/archive/blogs/randomnumber/encoder-changes-within-azure-media-services-now-create-ismc-file).

### <a name="known-issues"></a>Znane problemy
Niektórzy klienci mogą przechodzić przez problem z tagiem REPEAT w manifeście Smooth Streaming. Aby uzyskać więcej informacji, zobacz [tę sekcję](media-services-deliver-content-overview.md#known-issues).

## <a name="april-2016-release"></a><a id="apr_changes16"></a>Wydanie z kwietnia 2016
### <a name="media-analytics"></a>Media Analytics
 Media Services wprowadzono Media Analytics do zaawansowanej analizy filmów wideo. Aby uzyskać więcej informacji, zobacz [Omówienie usługi Media Services Analytics](./legacy-components.md).

### <a name="apple-fairplay-preview"></a>Apple FairPlay (wersja zapoznawcza)
Teraz możesz używać Media Services do dynamicznego szyfrowania zawartości HTTP Live Streaming (HLS) za pomocą Apple FairPlay. Możesz również użyć usługi dostarczania licencji Media Services, aby dostarczać licencje FairPlay klientom. Aby uzyskać więcej informacji, zobacz "Używanie Azure Media Services do przesyłania strumieniowego zawartości HLS chronionej przez Apple FairPlay".

## <a name="february-2016-release"></a><a id="feb_changes16"></a>Wydanie z lutego 2016
Najnowsza wersja zestawu Media Services SDK dla platformy .NET (3.5.3) zawiera rozwiązanie do usuwania błędów związanych z usługą Google Widevine. Nie można ponownie użyć AssetDeliveryPolicy w przypadku wielu zasobów zaszyfrowanych za pomocą Widevine. W ramach tej poprawki błędu dodano następującą właściwość do zestawu SDK: WidevineBaseLicenseAcquisitionUrl.

```csharp
Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
    new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
{
    {AssetDeliveryPolicyConfigurationKey.WidevineBaseLicenseAcquisitionUrl,"http://testurl"},

};
```

## <a name="january-2016-release"></a><a id="jan_changes_16"></a>Wydanie z stycznia 2016
Zmieniono nazwy jednostek zarezerwowanych na kodowanie, aby zmniejszyć liczbę pomyłek z nazwami koderów.

Jednostki zarezerwowane w warstwach Podstawowa, standardowa i Premium zostały odpowiednio zmienione na jednostki zarezerwowane S1, S2 i S3. Klienci korzystający z jednostek zarezerwowanych na podstawowe kodowanie dzisiaj Zobacz S1 jako etykietę w Azure Portal (i na rachunku). Klienci korzystający z wersji Standard i Premium zobaczą odpowiednio etykiety S2 i S3. 

## <a name="december-2015-release"></a><a id="dec_changes_15"></a>Wydanie z grudnia 2015

### <a name="media-encoder-deprecation-announcement"></a>Anons dotyczący wycofania kodera multimediów

 Koder multimedialny będzie przestarzały, począwszy od około 12 miesięcy od wydania Media Encoder Standard.

### <a name="azure-sdk-for-php"></a>Zestaw Azure SDK dla środowiska PHP
Zespół zestawu Azure SDK opublikował nową wersję [zestawu Azure SDK dla pakietu php](https://github.com/Azure/azure-sdk-for-php) , która zawiera aktualizacje i nowe funkcje dla Media Services. W szczególności zestaw Media Services SDK dla języka PHP obsługuje teraz najnowsze funkcje [ochrony zawartości](media-services-content-protection-overview.md) . Te funkcje są szyfrowaniem dynamicznym przy użyciu algorytmu AES i DRM (PlayReady i Widevine) z ograniczeniami dotyczącymi tokenów i bez nich. Obsługuje ona również skalowanie [jednostek kodowania](media-services-dotnet-encoding-units.md).

Aby uzyskać więcej informacji, zobacz:

* Poniższe [przykłady kodu](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices) ułatwiają szybkie rozpoczęcie pracy:
  * **vodworkflow_aes. php**: ten plik php pokazuje, jak korzystać z szyfrowania dynamicznego aes-128 i usługi Key Delivery. Jest on oparty na przykładzie programu .NET opisanym w temacie [Korzystanie z szyfrowania dynamicznego AES-128 i usługi Key Delivery](media-services-protect-with-aes128.md).
  * **vodworkflow_aes. php**: ten plik php pokazuje, jak używać szyfrowania dynamicznego PlayReady i usługi dostarczania licencji. Jest on oparty na przykładowym środowisku .NET opisanym w temacie [Korzystanie z szyfrowania Common Encryption Widevine](media-services-protect-with-playready-widevine.md).
  * **scale_encoding_units. php**: ten plik php przedstawia sposób skalowania jednostek zarezerwowanych kodowania.

## <a name="november-2015-release"></a><a id="nov_changes_15"></a>Wydanie z listopada 2015
 Media Services teraz oferuje usługę dostarczania licencji Widevine w chmurze. Aby uzyskać więcej informacji, zobacz [ten blog](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/). Zobacz również [ten samouczek](media-services-protect-with-playready-widevine.md) i [repozytorium GitHub](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm). 

Usługi dostarczania licencji Widevine udostępniane przez Media Services są w wersji zapoznawczej. Aby uzyskać więcej informacji, zobacz [ten blog](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/).

## <a name="october-2015-release"></a><a id="oct_changes_15"></a>Wydanie z października 2015
Media Services jest teraz aktywny w następujących centrach danych: Brazylia Południowa, Indie Zachodnie, Indie Południowe i Indie Środkowe. Teraz można używać Azure Portal do [tworzenia kont usługi Media Service](media-services-portal-create-account.md) i wykonywania różnych zadań opisanych na [stronie internetowej z dokumentacją Media Services](https://azure.microsoft.com/documentation/services/media-services/). Live Encoding nie jest włączona w tych centrach danych. Ponadto nie wszystkie typy jednostek zarezerwowanych w ramach kodowania są dostępne w tych centrach danych.

* Brazylia Południowa: dostępne są tylko jednostki zarezerwowane w warstwie Standardowa i podstawowa.
* Indie Zachodnie, Indie Południowe i Indie Środkowe: dostępne są tylko podstawowe zarezerwowane jednostki kodowania.

## <a name="september-2015-release"></a><a id="september_changes_15"></a>Wydanie z września 2015
Media Services teraz oferuje możliwość ochrony zarówno wideo na żądanie, jak i strumieni na żywo za pomocą Widevine modularnej technologii DRM. W celu zapewnienia licencji Widevine można użyć następujących partnerów usług dostarczania:
* [Axinom](https://www.axinom.com) 
* [EZDRM](https://ezdrm.com/) 
* [castLabs](https://castlabs.com/company/partners/azure/) 

Aby uzyskać więcej informacji, zobacz [ten blog](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/).
  
Aby skorzystać z usługi Widevine, można skonfigurować obiekt AssetDeliveryConfiguration przy użyciu zestawu [Media Services .NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices/) (począwszy od wersji 3.5.1) lub interfejsu API REST. 
* Media Services dodano obsługę filmów wideo Apple ProRes. Teraz możesz przekazać pliki filmów wideo w formacie QuickTime, które korzystają z ProRes firmy Apple lub innych koderów. Aby uzyskać więcej informacji, zobacz [ten blog](https://azure.microsoft.com/blog/announcing-support-for-apple-prores-videos-in-azure-media-services/).
* Teraz można używać Media Encoder Standard do wykonywania wycinków podrzędnych i wyodrębniania archiwów na żywo. Aby uzyskać więcej informacji, zobacz [ten blog](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/).
* Wprowadzono następujące aktualizacje filtrowania: 
  
  * Teraz można używać formatu Apple HLS z filtrem tylko audio. Ta aktualizacja służy do usuwania śledzenia tylko audio przez określenie (tylko audio = false) w adresie URL.
  * Po zdefiniowaniu filtrów dla zasobów można połączyć wiele filtrów (maksymalnie trzy) w jednym adresie URL.
    
    Aby uzyskać więcej informacji, zobacz [ten blog](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).
* Media Services teraz obsługuje ramki I w HLS w wersji 4. Obsługa ramek i jest optymalna. Domyślnie wszystkie dane wyjściowe w wersji 4 HLS zawierają listę odtwarzania ramek (EXT-X-I-FRAME-STREAM-INF).
Aby uzyskać więcej informacji, zobacz [ten blog](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).

## <a name="august-2015-release"></a><a id="august_changes_15"></a>Wydanie 2015 sierpnia
* Zestaw Media Services SDK dla wersji Java 0.8.0 Release i nowe przykłady są teraz dostępne. Aby uzyskać więcej informacji, zobacz:
    
* Azure Media Player zostało zaktualizowane z obsługą strumienia wielodźwiękowego. Aby uzyskać więcej informacji, zobacz [ten wpis w blogu](https://azure.microsoft.com/blog/2015/08/13/azure-media-player-update-with-multi-audio-stream-support/).

## <a name="july-2015-release"></a><a id="july_changes_15"></a>Wydanie 2015 lipca
* Ogólna dostępność Media Encoder Standard została ogłoszona. Aby uzyskać więcej informacji, zobacz [ten wpis w blogu](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/).
  
    Media Encoder Standard używa ustawień wstępnych, zgodnie z opisem w [tej sekcji](./media-services-mes-presets-overview.md). W przypadku użycia ustawienia wstępnego dla kodowania 4K należy uzyskać typ jednostki zarezerwowanej Premium. Aby uzyskać więcej informacji, zobacz [skalowanie kodowania](media-services-scale-media-processing-overview.md).
* Podpisy w czasie rzeczywistym są używane z Media Services i Media Player. Aby uzyskać więcej informacji, zobacz [ten wpis w blogu](https://azure.microsoft.com/blog/2015/07/08/live-real-time-captions-with-azure-media-services-and-player/).

### <a name="media-services-net-sdk-updates"></a>Media Services aktualizacji zestawu SDK platformy .NET
Zestaw SDK Media Services platformy .NET jest teraz w wersji 3.4.0.0. Wprowadzono następujące aktualizacje: 

* Obsługa została zaimplementowana dla archiwum na żywo. Nie można pobrać elementu zawartości zawierającego Archiwum na żywo.
* Obsługa filtrów dynamicznych została zaimplementowana.
* Funkcja została zaimplementowana, aby umożliwić użytkownikom przechowywanie kontenera magazynu podczas usuwania elementu zawartości.
* Poprawki błędów dotyczą zasad ponawiania prób w kanałach.
* Media Encoder Premium Workflow została włączona.

## <a name="june-2015-release"></a><a id="june_changes_15"></a>Wydanie z czerwca 2015
### <a name="media-services-net-sdk-updates"></a>Media Services aktualizacji zestawu SDK platformy .NET
Zestaw SDK Media Services platformy .NET jest teraz w wersji 3.3.0.0. Wprowadzono następujące aktualizacje: 

* Dodano obsługę dla specyfikacji odnajdywania OpenID Connect Connect.
* Dodano obsługę przerzucania kluczy na stronie dostawca tożsamości.

Jeśli używasz dostawcy tożsamości, który uwidacznia dokument odnajdywania OpenID Connect (jako usługi Azure AD, Google i Salesforce), możesz wydać Media Services, aby uzyskać klucze podpisywania w celu weryfikacji tokenów sieci Web JSON (JWTs) z specyfikacji OpenID Connect Connect Discovery. 

Aby uzyskać więcej informacji, zobacz [Korzystanie z kluczy sieci Web JSON z specyfikacji OpenID Connect Connect Discovery do pracy z uwierzytelnianiem JWT w Media Services](http://gtrifonov.com/2015/06/07/using-json-web-keys-from-openid-connect-discovery-spec-to-work-with-jwt-token-authentication-in-azure-media-services/).

## <a name="may-2015-release"></a><a id="may_changes_15"></a>Wydanie 2015 maja
Ogłoszono następujące nowe funkcje:

* [Wersja zapoznawcza kodowania na żywo z Media Services](media-services-manage-live-encoder-enabled-channels.md)
* [Manifest dynamiczny](media-services-dynamic-manifest-overview.md)

## <a name="april-2015-release"></a><a id="april_changes_15"></a>Wydanie z kwietnia 2015
### <a name="general-media-services-updates"></a>Ogólne aktualizacje Media Services
* [Media Player](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/) zostało ogłoszone.
* Począwszy od Media Services REST 2,10, kanały, które są skonfigurowane do pozyskiwania protokołu przesyłania komunikatów Real-Time (RTMP), są tworzone przy użyciu podstawowych i dodatkowych adresów URL pozyskiwania. Aby uzyskać więcej informacji, zobacz Konfiguracje pozyskiwania [kanałów](media-services-live-streaming-with-onprem-encoders.md#channel_input).
* Azure Media Indexer został zaktualizowany.
* Dodano obsługę języka hiszpańskiego.
* Dodano nową konfigurację formatu XML.

Aby uzyskać więcej informacji, zobacz [ten blog](https://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/).

### <a name="media-services-net-sdk-updates"></a>Media Services aktualizacji zestawu SDK platformy .NET
Zestaw SDK Media Services platformy .NET jest teraz w wersji 3.2.0.0. Wprowadzono następujące aktualizacje:

* Nieprzerwana zmiana: TokenRestrictionTemplate. Issuer i TokenRestrictionTemplate. odbiorca zostały zmienione na wartość typu String.
* Wprowadzono aktualizacje związane z tworzeniem niestandardowych zasad ponawiania.
* Poprawki błędów dotyczą przekazywania i pobierania plików.
* Klasa MediaServicesCredentials teraz akceptuje podstawowe i pomocnicze punkty końcowe kontroli dostępu do uwierzytelniania.

## <a name="march-2015-release"></a><a id="march_changes_15"></a>Wydanie z marca 2015
### <a name="general-media-services-updates"></a>Ogólne aktualizacje Media Services
* Media Services teraz zapewnia Content Delivery Network integrację. Aby zapewnić obsługę integracji, właściwość CdnEnabled została dodana do StreamingEndpoint. CdnEnabled może być używany z interfejsami API REST rozpoczynającymi się od wersji 2,9. Aby uzyskać więcej informacji, zobacz [StreamingEndpoint](/rest/api/media/operations/streamingendpoint). CdnEnabled można użyć z zestawem SDK platformy .NET rozpoczynającym się od wersji 3.1.0.2. Aby uzyskać więcej informacji, zobacz [StreamingEndpoint](/archive/blogs/randomnumber/encoder-changes-within-azure-media-services-now-create-ismc-file).
* Media Encoder Premium Workflow zostało ogłoszone. Aby uzyskać więcej informacji, zobacz [wprowadzenie kodowania Premium w Azure Media Services](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/).

## <a name="february-2015-release"></a><a id="february_changes_15"></a>Wydanie z lutego 2015
### <a name="general-media-services-updates"></a>Ogólne aktualizacje Media Services
Interfejs API REST Media Services ma teraz wersję 2,9. Począwszy od tej wersji, można włączyć integrację Content Delivery Network z punktami końcowymi przesyłania strumieniowego. Aby uzyskać więcej informacji, zobacz [StreamingEndpoint](/rest/api/media/operations/streamingendpoint).

## <a name="january-2015-release"></a><a id="january_changes_15"></a>Wydanie z stycznia 2015
### <a name="general-media-services-updates"></a>Ogólne aktualizacje Media Services
Ogłoszono ogólną dostępność ochrony zawartości przy użyciu szyfrowania dynamicznego. Aby uzyskać więcej informacji, zobacz [Media Services ulepszanie zabezpieczeń przesyłania strumieniowego dzięki ogólnej dostępności technologii DRM](https://azure.microsoft.com/blog/2015/01/29/azure-media-services-enhances-streaming-security-with-general-availability-of-drm-technology/).

### <a name="media-services-net-sdk-updates"></a>Media Services aktualizacji zestawu SDK platformy .NET
Zestaw SDK Media Services platformy .NET jest teraz w wersji 3.1.0.1.

W tej wersji oznaczono Konstruktor default Microsoft. WindowsAzure. MediaServices. Client. ContentKeyAuthorization. TokenRestrictionTemplate jako przestarzały. Nowy Konstruktor przyjmuje wartość TokenType jako argument.

```csharp
TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);
```


## <a name="december-2014-release"></a><a id="december_changes_14"></a>Wydanie z grudnia 2014
### <a name="general-media-services-updates"></a>Ogólne aktualizacje Media Services
* Niektóre aktualizacje i nowe funkcje zostały dodane do Media Indexer. Aby uzyskać więcej informacji, zobacz [Azure Media Indexer informacji](https://azure.microsoft.com/blog/2014/12/03/azure-media-indexer-version-1-1-6-7-release-notes/)o wersji 1.1.6.7.
* Dodano nowy interfejs API REST, którego można użyć do zaktualizowania jednostek zarezerwowanych kodowania. Aby uzyskać więcej informacji, zobacz [EncodingReservedUnitType with REST](/rest/api/media/operations/encodingreservedunittype).
* Dodano obsługę mechanizmu CORS dla usługi dostarczania kluczy.
* Wprowadzono ulepszenia wydajności w celu zbadania opcji zasad autoryzacji.
* W centrum danych w Chinach: [adres URL dostarczania klucza](/rest/api/media/operations/contentkey#get_delivery_service_url) jest teraz dla klienta (podobnie jak w przypadku innych centrów danych).
* Dodano czas trwania elementu "HLS". Podczas przesyłania strumieniowego na żywo HLS jest zawsze spakowana dynamicznie. Domyślnie Media Services automatycznie oblicza współczynnik tworzenia pakietów segmentu HLS (FragmentsPerSegment) na podstawie interwału klatek kluczowych (KeyFrameInterval). Ta metoda jest również nazywana grupą obrazów (grupę GOP) otrzymywanych z kodera na żywo. Aby uzyskać więcej informacji, zobacz [Working with Media Services Live Streaming](/previous-versions/azure/dn783466(v=azure.100)).

### <a name="media-services-net-sdk-updates"></a>Media Services aktualizacji zestawu SDK platformy .NET
[Zestaw SDK Media Services platformy .NET](https://www.nuget.org/packages/windowsazure.mediaservices/) jest teraz w wersji 3.1.0.0. Wprowadzono następujące aktualizacje:

* Zależność zestawu .NET SDK została uaktualniona do platformy .NET 4,5.
* Dodano nowy interfejs API, którego można użyć do zaktualizowania jednostek zarezerwowanych kodowania. Aby uzyskać więcej informacji, zobacz [Aktualizacja typu jednostki zarezerwowanej i zwiększanie liczby jednostek zarezerwowanych przy użyciu platformy .NET](media-services-dotnet-encoding-units.md).
* Dodano obsługę JWT uwierzytelniania tokenu. Aby uzyskać więcej informacji, zobacz [uwierzytelnianie tokenu JWT w Media Services i szyfrowanie dynamiczne](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).
* Dodano względne przesunięcia dla BeginDate i ExpirationDate w szablonie licencji PlayReady.

## <a name="november-2014-release"></a><a id="november_changes_14"></a>Wydanie z listopada 2014
* Teraz można użyć Media Services do pozyskiwania zawartości na żywo Smooth Streaming (fMP4) za pośrednictwem połączenia TLS. Aby przejąć protokół TLS, należy zaktualizować adres URL pozyskiwania do protokołu HTTPS. Obecnie Media Services nie obsługuje protokołu TLS z domenami niestandardowymi. Aby uzyskać więcej informacji na temat przesyłania strumieniowego na żywo, zobacz [Working with Azure Media Services Live Streaming](/previous-versions/azure/dn783466(v=azure.100)).
* Obecnie nie można pozyskać strumienia na żywo RTMP za pośrednictwem połączenia TLS.
* Można przesyłać strumieniowo za pośrednictwem protokołu TLS tylko wtedy, gdy punkt końcowy przesyłania strumieniowego, z którego dostarczasz zawartość, został utworzony po 10 września 2014. Jeśli adresy URL przesyłania strumieniowego są oparte na punktach końcowych przesyłania strumieniowego utworzonych po 10 września 2014, adres URL zawiera "streaming.mediaservices.windows.net" (nowy format). Adresy URL przesyłania strumieniowego zawierające "origin.mediaservices.windows.net" (stary format) nie obsługują protokołu TLS. Jeśli adres URL ma stary format i chcesz przesłać strumieniowo za pośrednictwem protokołu TLS, [Utwórz nowy punkt końcowy przesyłania strumieniowego](media-services-portal-manage-streaming-endpoints.md). Aby przesłać strumieniowo zawartość za pośrednictwem protokołu TLS, Użyj adresów URL opartych na nowym punkcie końcowym przesyłania strumieniowego.

### <a name="media-services-net-sdk"></a><a id="oct_sdk"></a>Zestaw .NET SDK usługi Media Services
Rozszerzenia Media Services SDK dla platformy .NET to teraz wersja 2.0.0.3.

Zestaw Media Services SDK dla platformy .NET jest teraz w wersji 3.0.0.8. Wprowadzono następujące aktualizacje:

* Refaktoryzacja została zaimplementowana w klasach zasad ponawiania prób.
* Do nagłówków żądań HTTP został dodany ciąg agenta użytkownika.
* Dodano krok kompilacji przywracania NuGet.
* Testy scenariusza zostały naprawione, aby użyć certyfikatu x509 z repozytorium.
* Ustawienia walidacji zostały dodane do momentu, gdy aktualizacja kanału i przesyłania strumieniowego została zakończona.

### <a name="new-github-repository-to-host-media-services-samples"></a>Nowe repozytorium GitHub do hostowania przykładów Media Services
Przykłady znajdują się w [repozytorium Media Services Samples](https://github.com/Azure/Azure-Media-Services-Samples)w witrynie GitHub.

## <a name="september-2014-release"></a><a id="september_changes_14"></a>Wydanie z września 2014
Metadane REST Media Services są teraz w wersji 2,7. Aby uzyskać więcej informacji na temat najnowszych aktualizacji REST, zobacz [Informacje o interfejsie API REST Media Services](/rest/api/media/operations/azure-media-services-rest-api-reference).

Zestaw Media Services SDK dla platformy .NET jest teraz w wersji 3.0.0.7

### <a name="breaking-changes"></a><a id="sept_14_breaking_changes"></a>Zmiany powodujące niezgodność
* Zmieniono nazwę pochodzenia na [StreamingEndpoint].
* Zmiana została wprowadzona w domyślnym zachowaniu w przypadku używania Azure Portal do kodowania i publikowania plików MP4.

### <a name="new-featuresscenarios-that-are-part-of-the-general-availability-release"></a><a id="sept_14_GA_changes"></a>Nowe funkcje/scenariusze, które są częścią ogólnej wersji dostępności
* Wprowadzono Media Indexer procesora multimediów. Aby uzyskać więcej informacji, zobacz [indeksowanie plików nośników przy użyciu Media Indexer](/previous-versions/azure/dn783455(v=azure.100)).
* Za pomocą jednostki [StreamingEndpoint] można dodawać nazwy domen niestandardowych (hosta).
  
    Aby użyć niestandardowej nazwy domeny jako nazwy punktu końcowego Media Services streaming, Dodaj niestandardowe nazwy hostów do punktu końcowego przesyłania strumieniowego. Użyj Media Services interfejsów API REST lub zestawu .NET SDK, aby dodać niestandardowe nazwy hostów.
  
    Obowiązują następujące zastrzeżenia:
  
  * Musisz mieć własność niestandardowej nazwy domeny.
  * Własność nazwy domeny musi być zweryfikowana przez Media Services. Aby sprawdzić poprawność domeny, Utwórz rekord CName, który mapuje domenę nadrzędną MediaServicesAccountId na zweryfikowanie strefy DNS MediaServices-DNS.
  * Należy utworzyć inny rekord CName, który mapuje niestandardową nazwę hosta (na przykład sports.contoso.com) na nazwę hosta Media Services StreamingEndpoint (na przykład amstest.streaming.mediaservices.windows.net).

    Aby uzyskać więcej informacji, zobacz Właściwość CustomHostNames w artykule [StreamingEndpoint](/rest/api/media/operations/streamingendpoint) .

### <a name="new-featuresscenarios-that-are-part-of-the-public-preview-release"></a><a id="sept_14_preview_changes"></a>Nowe funkcje/scenariusze, które są częścią publicznej wersji zapoznawczej
* Podgląd przesyłania strumieniowego na żywo. Aby uzyskać więcej informacji, zobacz [Working with Media Services Live Streaming](/previous-versions/azure/dn783466(v=azure.100)).
* Usługa dostarczania kluczy. Aby uzyskać więcej informacji, zobacz [Korzystanie z szyfrowania dynamicznego AES-128 i usługi Key Delivery](/previous-versions/azure/dn783457(v=azure.100)).
* Szyfrowanie dynamiczne AES. Aby uzyskać więcej informacji, zobacz [Korzystanie z szyfrowania dynamicznego AES-128 i usługi Key Delivery](/previous-versions/azure/dn783457(v=azure.100)).
* Usługa dostarczania licencji PlayReady. 
* Szyfrowanie dynamiczne PlayReady. 
* Media Services szablon licencji PlayReady. Aby uzyskać więcej informacji, zobacz [Omówienie szablonu licencji programu Media Services PlayReady].
* Strumieniowo zaszyfrowane zasoby zasobów magazynu. Aby uzyskać więcej informacji, zobacz [przesyłanie strumieniowe zawartości w pamięci podręcznej](/previous-versions/azure/dn783451(v=azure.100)).

## <a name="august-2014-release"></a><a id="august_changes_14"></a>Wydanie 2014 sierpnia
Po zakodowaniu elementu zawartości dane wyjściowe są generowane po zakończeniu zadania kodowania. Do tej wersji program Media Services Encoder wygenerował metadane dotyczące zasobów wyjściowych. Począwszy od tej wersji koder generuje również metadane dotyczące zasobów wejściowych. Aby uzyskać więcej informacji, zobacz [metadane wejściowe] i [metadane wyjściowe].

## <a name="july-2014-release"></a><a id="july_changes_14"></a>Wydanie 2014 lipca
Następujące poprawki błędów zostały wykonane dla Pakowarki Azure Media Services i modułu szyfrującego:

* Gdy zasób Archiwum na żywo jest przesyłany do HLS, odtwarzany jest tylko dźwięk z tyłu: ten problem został rozwiązany i teraz można odtworzyć zarówno dźwięk, jak i wideo.
* Gdy zasób jest opakowany do HLS i AES 128-bitowe szyfrowanie kopert, spakowane strumienie nie są odtwarzane na urządzeniach z systemem Android: ten błąd został rozwiązany, a spakowany strumień jest odtwarzany na urządzeniach z systemem Android, które obsługują HLS.

## <a name="may-2014-release"></a><a id="may_changes_14"></a>Wydanie 2014 maja
### <a name="general-media-services-updates"></a><a id="may_14_changes"></a>Ogólne aktualizacje Media Services
Teraz można używać [pakietów dynamicznych] do przesyłania strumieniowego HLS w wersji 3. Aby przesłać strumieniowo HLS w wersji 3, Dodaj następujący format do ścieżki lokalizatora źródła: *. ISM/manifest (format = M3U8-AAPL-v3). Aby uzyskać więcej informacji, zobacz [ten forum](https://social.msdn.microsoft.com/Forums/en-US/13b8a776-9519-4145-b9ed-d2b632861fde/dynamic-packaging-to-hls-v3).

Pakowanie dynamiczne obsługuje teraz również dostarczanie HLS (w wersji 3 i 4) zaszyfrowanej przy użyciu oprogramowania PlayReady w oparciu o Smooth Streaming statycznie szyfrowany przy użyciu technologii PlayReady. Aby uzyskać informacje na temat sposobu szyfrowania Smooth Streaming przy użyciu oprogramowania PlayReady, zobacz [ochrona Smooth Streaming przy użyciu oprogramowania PlayReady](/previous-versions/azure/dn189154(v=azure.100)).

### <a name="media-services-net-sdk-updates"></a><a name="may_14_donnet_changes"></a>Media Services aktualizacji zestawu SDK platformy .NET
Zestaw SDK Media Services platformy .NET jest teraz w wersji 3.0.0.5. Wprowadzono następujące aktualizacje:

* Szybkość i odporność są lepsze podczas przekazywania i pobierania zasobów multimedialnych.
* Wprowadzono ulepszenia logiki ponawiania i przejściową obsługę wyjątków: 
  
  * Podczas wykonywania zapytania, zapisywania zmian i przekazywania lub pobierania plików Ulepszono funkcję wykrywania błędów przejściowych i logiki ponawiania. 
  * Po otrzymaniu wyjątków sieci Web (na przykład podczas żądania tokenu Access Control Service) błędy krytyczne są teraz szybsze.

Aby uzyskać więcej informacji, zobacz [logika ponawiania w zestawie Media Services SDK dla platformy .NET].

## <a name="januaryfebruary-2014-releases"></a><a id="jan_feb_changes_14"></a>Wydania styczeń/luty 2014
### <a name="media-services-net-sdk-3001-3002-and-3003"></a><a name="jan_fab_14_donnet_changes"></a>Media Services .NET SDK 3.0.0.1, 3.0.0.2 i 3.0.0.3
Zmiany w 3.0.0.1 i 3.0.0.2 obejmują:

* Problemy związane z użyciem zapytań LINQ z instrukcjami OrderBy zostały naprawione.
* Rozwiązania testowe w usłudze [GitHub] zostały podzielone na testy jednostkowe i testy oparte na scenariuszach.

Aby uzyskać więcej informacji o zmianach, zobacz [Media Services .NET SDK 3.0.0.1 i 3.0.0.2](http://gtrifonov.com/2014/02/07/windows-azure-media-services-net-sdk-3-0-0-2-release/index.html).

W wersji 3.0.0.3 wprowadzono następujące zmiany:

* Zależności usługi Azure Storage zostały uaktualnione w celu korzystania z wersji 3.0.3.0.
* Problem ze zgodnością z poprzednimi wersjami został ustalony dla 3,0. *.* wydanie.

## <a name="december-2013-release"></a><a id="december_changes_13"></a>Wydanie z grudnia 2013
### <a name="media-services-net-sdk-3000"></a><a name="dec_13_donnet_changes"></a>Media Services 3.0.0.0 .NET SDK
> [!NOTE]
> Wersje 3.0. x. x nie są zgodne z poprzednimi wersjami 2.4. x. x.
> 
> 

Najnowsza wersja zestawu SDK Media Services jest teraz 3.0.0.0. Możesz pobrać najnowszy pakiet z programu NuGet lub uzyskać bity z usługi [GitHub].

Począwszy od zestawu SDK Media Services w wersji 3.0.0.0 można ponownie użyć tokenów [Access Control Service usługi Azure AD](/previous-versions/azure/azure-services/hh147631(v=azure.100)) . Aby uzyskać więcej informacji, zobacz sekcję "ponowne używanie tokenów Access Control Service" w temacie [Connect to Media Services with the Media Services SDK for .NET](/previous-versions/azure/jj129571(v=azure.100)).

### <a name="media-services-net-sdk-extensions-2000"></a><a name="dec_13_donnet_ext_changes"></a>2.0.0.0 rozszerzeń zestawu SDK platformy .NET Media Services
 Rozszerzenia zestawu SDK dla programu Media Services .NET to zestaw metod rozszerzających i funkcji pomocników, które upraszczają kod i ułatwiają tworzenie przy użyciu Media Services. Najnowsze bity można uzyskać z [Media Services rozszerzeń zestawu SDK platformy .NET](https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev).

## <a name="november-2013-release"></a><a id="november_changes_13"></a>Wydanie z listopada 2013
### <a name="media-services-net-sdk-changes"></a><a name="nov_13_donnet_changes"></a>Zmiany Media Services zestawu SDK platformy .NET
Począwszy od tej wersji, zestaw Media Services SDK dla platformy .NET obsługuje błędy przejściowe, które mogą wystąpić w przypadku wywołania do warstwy interfejsu API REST Media Services.

## <a name="august-2013-release"></a><a id="august_changes_13"></a>Wydanie 2013 sierpnia
### <a name="media-services-powershell-cmdlets-included-in-azure-sdk-tools"></a><a name="aug_13_powershell_changes"></a>Media Services polecenia cmdlet programu PowerShell zawarte w narzędziach zestawu Azure SDK
Następujące polecenia cmdlet programu PowerShell Media Services są teraz zawarte w [narzędziach zestawu Azure SDK](https://github.com/Azure/azure-sdk-tools):

* Get-AzureMediaServices 

    Na przykład: `Get-AzureMediaServicesAccount`
* New-AzureMediaServicesAccount 
  
    Na przykład: `New-AzureMediaServicesAccount -Name "MediaAccountName" -Location "Region" -StorageAccountName "StorageAccountName"`
* New-AzureMediaServicesKey 
  
    Na przykład: `New-AzureMediaServicesKey -Name "MediaAccountName" -KeyType Secondary -Force`
* Remove-AzureMediaServicesAccount 
  
    Na przykład: `Remove-AzureMediaServicesAccount -Name "MediaAccountName" -Force`

## <a name="june-2013-release"></a><a id="june_changes_13"></a>Wydanie z czerwca 2013
### <a name="media-services-changes"></a><a name="june_13_general_changes"></a>Media Services zmiany
Następujące zmiany wymienione w tej sekcji są aktualizacjami zawartymi w wersji 2013 czerwca Media Services:

* Możliwość łączenia wielu kont magazynu z kontem usługi multimediów. 
    * StorageAccount
    * Asset. StorageAccountName i Asset. StorageAccount
* Możliwość aktualizowania zadania. priorytet. 
* Jednostki i właściwości powiązane z powiadomieniem: 
    * JobNotificationSubscription
    * Operacja
    * Zadanie
* Element zawartości. URI 
* Locator.Name 

### <a name="media-services-net-sdk-changes"></a><a name="june_13_dotnet_changes"></a>Zmiany Media Services zestawu SDK platformy .NET
W Czerwiec Media Services 2013 wersjach zestawu SDK uwzględniono następujące zmiany. Najnowszy Media Services SDK jest dostępny w witrynie GitHub.

* Począwszy od wersji 2.3.0.0 modułu, zestaw SDK Media Services obsługuje łączenie wielu kont magazynu z kontem Media Services. Następujące interfejsy API obsługują tę funkcję:
  
    * Typ IStorageAccount
    * Microsoft. WindowsAzure. MediaServices. Client. CloudMediaContext. StorageAccounts — Właściwość
    * Właściwość StorageAccount
    * Właściwość StorageAccountName
  
      Aby uzyskać więcej informacji, zobacz [Zarządzanie zasobami Media Services na wielu kontach magazynu](/previous-versions/azure/dn271889(v=azure.100)).
* Interfejsy API związane z powiadomieniami. Począwszy od wersji 2.2.0.0, można nasłuchiwać powiadomień usługi Azure queue storage. Aby uzyskać więcej informacji, zobacz [Obsługa powiadomień o zadaniach Media Services](/previous-versions/azure/dn261241(v=azure.100)).
  
    * Microsoft. WindowsAzure. MediaServices. Client. IJob. JobNotificationSubscriptions — Właściwość
    * Microsoft. WindowsAzure. MediaServices. Client. INotificationEndPoint — typ
    * Microsoft. WindowsAzure. MediaServices. Client. IJobNotificationSubscription — typ
    * Microsoft. WindowsAzure. MediaServices. Client. NotificationEndPointCollection — typ
    * Microsoft. WindowsAzure. MediaServices. Client. NotificationEndPointType — typ
* Zależność od zestawu SDK klienta magazynu 2,0 (Microsoft.WindowsAzure.StorageClient.dll)
* Zależność od protokołu OData 5,5 (Microsoft.Data.OData.dll)

## <a name="december-2012-release"></a><a id="december_changes_12"></a>Wydanie z grudnia 2012
### <a name="media-services-net-sdk-changes"></a><a name="dec_12_dotnet_changes"></a>Zmiany Media Services zestawu SDK platformy .NET
* IntelliSense: w wielu typach została dodana dokumentacja IntelliSense.
* Microsoft. Practices. TransientFaultHandling. Core: Rozwiązano problem polegający na tym, że zestaw SDK nadal ma zależność od starej wersji tego zestawu. Zestaw SDK teraz odwołuje się do wersji 5.1.1209.1 tego zestawu.

Poprawki dotyczące problemów znalezionych w zestawie SDK listopad 2012:

* IAsset. Locators. Count: Ten licznik jest teraz poprawnie raportowany dla nowych interfejsów IAsset po usunięciu wszystkich lokalizatorów.
* IAssetFile. ContentFileSize: Ta wartość jest teraz prawidłowo ustawiona po przesłaniu przez IAssetFile. upload (FilePath).
* IAssetFile. ContentFileSize: tę właściwość można teraz ustawić podczas tworzenia pliku zasobów. Wcześniej była tylko do odczytu.
* IAssetFile. upload (FilePath): Rozwiązano problem polegający na tym, że w metodzie przekazywania synchronicznego wystąpił następujący błąd podczas przekazywania wielu plików do elementu zawartości. Błąd: "serwer nie może uwierzytelnić żądania. Upewnij się, że wartość nagłówka autoryzacji jest poprawnie sformułowana, łącznie z podpisem ".
* IAssetFile. UploadAsync: Rozwiązano problem, który ogranicza jednoczesne przekazywanie plików do pięciu plików.
* IAssetFile. UploadProgressChanged: to zdarzenie jest teraz udostępniane przez zestaw SDK.
* IAssetFile. DownloadAsync (String, BlobTransferClient, ILocator, CancellationToken): to teraz Przeciążenie metody zostało dostarczone.
* IAssetFile. DownloadAsync: Rozwiązano problem, który ogranicza jednoczesne pobieranie plików do pięciu plików.
* IAssetFile. Delete (): problem został rozwiązany, a wywołanie metody Delete może zgłosić wyjątek, jeśli żaden plik nie został przekazany do IAssetFile.
* Zadania: Rozwiązano problem polegający na tym, że łańcuch zadań "MP4 do wygładzania strumieni" z "zadaniem ochrony PlayReady" przy użyciu szablonu zadania nie utworzył żadnych zadań w ogóle.
* EncryptionUtils. GetCertificateFromStore (): Ta metoda nie zgłasza wyjątku odwołania o wartości null z powodu błędu podczas znajdowania certyfikatu w oparciu o problemy z konfiguracją certyfikatów.

## <a name="november-2012-release"></a><a id="november_changes_12"></a>Wydanie z listopada 2012
Zmiany wymienione w tej sekcji zostały zaktualizowane w wersji 2012 (wersja 2.0.0.0) zestawu SDK. Te zmiany mogą wymagać zmodyfikowania lub zapisania kodu w wersji zapoznawczej zestawu SDK z czerwca 2012.

* Elementy zawartości
  
    * IAsset. Create (assetname) jest *jedyną* funkcją tworzenia zasobów. IAsset. Create nie jest już przeciążać plików jako części wywołania metody. Użyj IAssetFile do przekazywania.
    * Metoda IAsset. publish oraz wartość wyliczenia AssetState. publish zostały usunięte z zestawu SDK usług. Każdy kod, który opiera się na tej wartości, musi zostać ponownie zapisany.
* FileInfo
  
    * Ta klasa została usunięta i zastąpiona przez IAssetFile.
  
* IAssetFiles
  
    * IAssetFile zastępuje FileInfo i ma inne zachowanie. Aby go użyć, Utwórz wystąpienie obiektu IAssetFiles, a następnie Przekaż plik za pomocą zestawu SDK Media Services lub zestawu SDK magazynu. Można użyć następujących przeciążeń IAssetFile. upload:
  
        * IAssetFile. upload (filePath): Ta metoda synchroniczna blokuje wątek i zalecamy ją tylko w przypadku przekazywania pojedynczego pliku.
        * IAssetFile. UploadAsync (filePath, blobTransferClient, Locator, cancellationToken): Ta metoda asynchroniczna jest preferowanym mechanizmem przekazywania. 
    
            Znana usterka: Jeśli używasz tokenu anulowania, przekazywanie zostało anulowane. Zadania mogą mieć wiele stanów anulowania. Należy prawidłowo przechwycić i obsłużyć wyjątki.
* Lokalizatory
  
    * Wersje specyficzne dla źródła zostały usunięte. Kontekst specyficzny dla sygnatury dostępu współdzielonego. Lokalizatory. CreateSasLocator (Asset, accessPolicy) zostaną oznaczone jako przestarzałe lub usunięte przez ogólnie dostępną. Zapoznaj się z sekcją "lokalizatory" w obszarze "nowe funkcje", aby zapoznać się z zaktualizowanym zachowaniem.

## <a name="june-2012-preview-release"></a><a id="june_changes_12"></a>Czerwiec 2012 wersja zapoznawcza
W wersji z listopada zestawu SDK wprowadzono następujące funkcje:

* Usuwanie jednostek
  
    * Obiekty IAsset, IAssetFile, ILocator, IAccessPolicy i IContentKey są teraz usuwane na poziomie obiektu, czyli IObject. Delete (), zamiast wymagać usunięcia w kolekcji, czyli cloudMediaContext. ObjCollection. Delete (objInstance).
* Lokalizatory
  
    * Teraz lokalizatory muszą być tworzone przy użyciu metody ustawiającej. Muszą używać wartości wyliczeniowych Locatortype. SAS lub Locatortype. OnDemandOrigin jako argumentu dla określonego typu lokalizatora, który ma zostać utworzony.
    * Nowe właściwości zostały dodane do lokalizatorów, aby ułatwić uzyskiwanie użytecznych identyfikatorów URI dla zawartości. Ten projekt lokalizatorów zapewnia większą elastyczność w zakresie rozszerzania rozwiązań innych firm i zwiększa łatwość używania aplikacji klienckich dla multimediów.
* Obsługa metod asynchronicznych
  
    * Obsługa asynchroniczna została dodana do wszystkich metod.

## <a name="additional-notes"></a>Uwagi dodatkowe

* Widevine to usługa świadczona przez firmę Google Inc. z zastrzeżeniem warunków użytkowania i zasad zachowania poufności informacji w firmie Google, Inc.

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

<!-- Anchors. -->

<!-- Images. -->

<!--- URLs. --->
[Microsoft Q&A question page for Azure Media Services]: /answers/topics/azure-media-services.html
[Dokumentacja interfejsu API REST Azure Media Services]: /rest/api/media/operations/azure-media-services-rest-api-reference
[Media Services pricing details]: https://azure.microsoft.com/pricing/details/media-services/
[Metadane wejściowe]: ./media-services-input-metadata-schema.md
[Metadane wyjściowe]: ./media-services-output-metadata-schema.md
[Deliver content]: /previous-versions/azure/hh973618(v=azure.100)
[Index media files with the Azure Media Indexer]: /previous-versions/azure/dn783455(v=azure.100)
[StreamingEndpoint]: /rest/api/media/operations/streamingendpoint
[Work with Media Services live streaming]: /previous-versions/azure/dn783466(v=azure.100)
[Use AES-128 dynamic encryption and the key delivery service]: /previous-versions/azure/dn783457(v=azure.100)
[Use PlayReady dynamic encryption and the license delivery service]: /previous-versions/azure/dn783467(v=azure.100)
[Preview features]: https://azure.microsoft.com/services/preview/
[Omówienie szablonu licencji PlayReady usługi Media Services]: /previous-versions/azure/dn783459(v=azure.100)
[Stream storage-encrypted content]: /previous-versions/azure/dn783451(v=azure.100)
[Azure portal]: https://portal.azure.com
[Dynamiczne tworzenie pakietów]: /previous-versions/azure/jj889436(v=azure.100)
[Nick Drouin's blog]: http://blog-ndrouin.azurewebsites.net/hls-v3-new-old-thing/
[Protect Smooth Streaming with PlayReady]: /previous-versions/azure/dn189154(v=azure.100)
[Logika ponawiania w zestawie Media Services SDK dla platformy .NET]: ./media-services-retry-logic-in-dotnet-sdk.md
[Grass Valley announces EDIUS 7 streaming through the cloud]: https://www.streamingmedia.com/Producer/Articles/ReadArticle.aspx?ArticleID=96351&utm_source=dlvr.it&utm_medium=twitter
[Control Media Services Encoder output file names]: /previous-versions/azure/dn303341(v=azure.100)
[Create overlays]: /previous-versions/azure/dn640496(v=azure.100)
[Stitch video segments]: /previous-versions/azure/dn640504(v=azure.100)
[Azure Media Services .NET SDK 3.0.0.1 and 3.0.0.2 releases]: http://www.gtrifonov.com/2014/02/07/windows-azure-media-services-.net-sdk-3.0.0.2-release/
[Azure AD Access Control Service]: /previous-versions/azure/azure-services/hh147631(v=azure.100)
[Connect to Media Services with the Media Services SDK for .NET]: /previous-versions/azure/jj129571(v=azure.100)
[Media Services .NET SDK extensions]: https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev
[Azure SDK tools]: https://github.com/Azure/azure-sdk-tools
[GitHub]: https://github.com/Azure/azure-sdk-for-media-services
[Manage Media Services assets across multiple Storage accounts]: /previous-versions/azure/dn271889(v=azure.100)
[Handle Media Services job notifications]: /previous-versions/azure/dn261241(v=azure.100)