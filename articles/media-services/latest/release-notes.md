---
title: Informacje o wersji usługi Azure Media Services w wersji 3 | Dokumenty firmy Microsoft
description: Aby być na bieżąco z najnowszymi rozwiązaniami, w tym artykule przedstawiono najnowsze aktualizacje usługi Azure Media Services w wersji 3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: na
ms.topic: article
ms.date: 04/20/2020
ms.author: juliako
ms.openlocfilehash: b4849b4fbfdbaece46f5669f4c242e864b1ca533
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81769753"
---
# <a name="azure-media-services-v3-release-notes"></a>Informacje o wersji usługi Azure Media Services w wersji 3

>Otrzymuj powiadomienia o tym, kiedy można ponownie odwiedzić `https://docs.microsoft.com/api/search/rss?search=%22Azure+Media+Services+v3+release+notes%22&locale=en-us` tę stronę, aby uzyskać aktualizacje, kopiując i wklejając ten adres URL: do czytnika kanałów RSS.

Aby być na bieżąco z najnowszymi wydarzeniami, w tym artykule znajdziesz informacje na temat:

* Najnowsze wydania
* Znane problemy
* Poprawki błędów
* Funkcje uznane za przestarzałe

## <a name="known-issues"></a>Znane problemy

> [!NOTE]
> Za pomocą [witryny Azure Portal](https://portal.azure.com/) można zarządzać [zdarzeniami na żywo](live-events-outputs-concept.md)w wersji 3 , [wyświetlać zasoby w](assets-concept.md)wersji 3 , uzyskać informacje o uzyskiwaniu dostępu do interfejsów API. W przypadku wszystkich innych zadań zarządzania (na przykład Przekształceń i zadań) użyj [interfejsu API REST,](https://aka.ms/ams-v3-rest-ref) [interfejsu wiersza polecenia](https://aka.ms/ams-v3-cli-ref)lub jednego z [obsługiwanych pakietów SDK.](media-services-apis-overview.md#sdks)

Aby uzyskać więcej informacji, zobacz [Wskazówki dotyczące migracji dotyczące przechodzenia z usługi Media Services w wersji 2 do wersji 3](migrate-from-v2-to-v3.md#known-issues).

## <a name="april-2020"></a>Kwiecień 2020 r.

### <a name="improvements-in-documentation"></a>Ulepszenia w dokumentacji

Dokumenty programu Azure Media Player zostały zmigrowane do [dokumentacji platformy Azure.](../azure-media-player/azure-media-player-overview.md)

## <a name="january-2020"></a>Styczeń 2020 r.

### <a name="improvements-in-media-processors"></a>Ulepszenia procesorów multimediów

- Ulepszona obsługa źródeł z przeplotem w analizie wideo — taka zawartość jest teraz poprawnie usuwana z przeplotem przed wysłaniem do aparatów wnioskowania.
- Podczas generowania miniatur w trybie "Najlepszy" koder przeszukuje teraz ponad 30 sekund, aby wybrać ramkę, która nie jest monochromatyczna.

### <a name="azure-government-cloud-updates"></a>Aktualizacje chmury platformy Azure dla instytucji rządowych

Usługi Media Services GA'ed w następujących regionach platformy Azure Government: *USGov Arizona* i *USGov Texas*.

## <a name="december-2019"></a>Grudzień 2019 r.

Dodano obsługę sieci CDN dla nagłówków *z systemem Prefetch origin-assist* do przesyłania strumieniowego na żywo i wideo na żądanie; dostępne dla klientów, którzy mają bezpośrednią umowę z Akamai CDN. Funkcja usługi Origin-Assist CDN-Prefetch obejmuje następujące wymiany nagłówków HTTP między siecią CDN Akamai a źródłem usługi Azure Media Services:

|Nagłówek HTTP|Wartości|Nadawca|Odbiornik|Przeznaczenie|
| ---- | ---- | ---- | ---- | ----- |
|Cdn-Origin-Assist-Prefetch-Enabled | 1 (domyślnie) lub 0 |CDN|Origin|Aby wskazać, że sieć CDN jest włączona z dostępem z dostępem do sieci|
|CDN-Origin-Assist-Ścieżka wstępna| Przykład: <br/>Fragmenty(wideo=1400000000,format=mpd-time-cmaf)|Origin|CDN|Aby zapewnić ścieżkę z przedsprzedażową do sieci CDN|
|Żądanie z asystą wstępną CDN|1 (żądanie z góry) lub 0 (zwykłe żądanie)|CDN|Origin|Aby wskazać żądanie z sieci CDN jest|

Aby wyświetlić część wymiany nagłówka w akcji, możesz wykonać następujące czynności:

1. Użyj postman lub curl, aby wysłać żądanie do źródła usługi Media Services dla segmentu lub fragmentu audio lub wideo. Upewnij się, że w żądaniu dodano nagłówek CDN-Origin-Assist-Prefetch-Enabled: 1.
2. W odpowiedzi powinien zostać wyświetlony nagłówek CDN-Origin-Assist-Prefetch-Path ze ścieżką względną jako jego wartość.

## <a name="november-2019"></a>Listopad 2019 r.

### <a name="live-transcription-preview"></a>Podgląd transkrypcji na żywo

Transkrypcja na żywo jest teraz w publicznej wersji zapoznawczej i jest dostępna do użytku w regionie Zachodnie stany USA 2.

Transkrypcja na żywo jest przeznaczona do pracy w połączeniu z wydarzeniami na żywo jako możliwość dodatku.  Jest obsługiwany zarówno w trybie przekazywania, jak i w przypadku zdarzeń na żywo kodowania standardowego lub premium.  Gdy ta funkcja jest włączona, usługa używa funkcji [Zamiana mowy na tekst](../../cognitive-services/speech-service/speech-to-text.md) usług Cognitive Services do transkrypcji wypowiadanych słów w przychodzącym dźwięku do tekstu. Tekst ten jest następnie udostępniany do dostarczenia wraz z wideo i audio w protokołach MPEG-DASH i HLS. Rozliczenia są oparte na nowym liczniku dodatku, który jest dodatkowym kosztem zdarzenia na żywo, gdy jest w stanie "Uruchomiony".  Aby uzyskać szczegółowe informacje na temat transkrypcji i rozliczeń na żywo, zobacz [Transkrypcja na żywo](live-transcription.md)

> [!NOTE]
> Obecnie transkrypcja na żywo jest dostępna tylko jako funkcja podglądu w regionie Zachodnie stany USA 2. Obsługuje transkrypcję słów mówionych w języku angielskim (en-us) tylko w tej chwili.

### <a name="content-protection"></a>Ochrona zawartości

Funkcja *zapobiegania powtórce tokenów* wydana w ograniczonych regionach we wrześniu jest teraz dostępna we wszystkich regionach.
Klienci usługi Media Services mogą teraz ustawić limit liczby razy, w których ten sam token może być użyty do żądania klucza lub licencji. Aby uzyskać więcej informacji, zobacz [Zapobieganie powtarzaniu powtórzów tokenów](content-protection-overview.md#token-replay-prevention).

### <a name="new-recommended-live-encoder-partners"></a>Nowi rekomendowani partnerzy ekcoderów na żywo

Dodano obsługę następujących nowych zalecanych koderów partnerów do przesyłania strumieniowego RTMP na żywo:

- [Cambria Na żywo 4.3](https://www.capellasystems.net/products/cambria-live/)
- [Kamery akcji GoPro Hero7/8 i Max](https://gopro.com/help/articles/block/getting-started-with-live-streaming)
- [Restream.io](https://restream.io/)

### <a name="file-encoding-enhancements"></a>Ulepszenia kodowania plików

- Nowe ustawienie wstępne kodowania z uwzględnieniem zawartości jest już dostępne. Tworzy zestaw plików MP4 wyrównanych do GOP przy użyciu kodowania z uwzględnieniem zawartości. Biorąc pod uwagę wszelkie zawartości wejściowej, usługa wykonuje początkową lekką analizę zawartości wejściowej. Używa tych wyników do określenia optymalnej liczby warstw, odpowiedniej szybkości transmisji bitów i ustawień rozdzielczości dla dostarczania za pomocą adaptacyjnego przesyłania strumieniowego. To ustawienie wstępne jest szczególnie skuteczne w przypadku filmów o niskiej złożoności i średniej złożoności, w których pliki wyjściowe są o niższych szybkościach transmisji bitów, ale w jakości, która nadal zapewnia widzom dobre wrażenia. Wyjście będzie zawierać pliki MP4 z wideo i audio z przeplotem. Aby uzyskać więcej informacji, zobacz [specyfikacje otwartego interfejsu API](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json).
- Zwiększona wydajność i wielowątkową dla re-sizer w standardowym koderze. W określonych warunkach klient powinien zobaczyć wzrost wydajności między 5-40% kodowania VOD. Zawartość o niskiej złożoności zakodowana w wielu szybkościach transmisji bitów spowoduje wzrost wydajności o najwyższej wydajności. 
- Standardowe kodowanie utrzymuje teraz regularną kadencję GOP dla zawartości o zmiennej szybkości klatek (VFR) podczas kodowania VOD podczas korzystania z ustawienia GOP opartego na czasie.  Oznacza to, że klient przesyłający zawartość o mieszanej szybkości klatek, która waha się między 15-30 kl./s, na przykład powinien teraz zobaczyć regularne odległości GOP obliczone na wyjściu, aby dostosować szybkość przesyłania strumieniowego plików MP4. Poprawi to możliwość płynnego przełączania się między torami podczas dostarczania przez HLS lub DASH. 
-  Ulepszona synchronizacja AV dla zawartości źródłowej o zmiennej szybkości klatek (VFR)

### <a name="video-indexer-video-analytics"></a>Indeksator wideo, analiza wideo

- Klatki kluczowe wyodrębnione przy użyciu predefiniowanego ustawienia VideoAnalyzer są teraz w oryginalnej rozdzielczości wideo, a nie są ponownie rozmiar. Wyodrębnianie klatek kluczowych o wysokiej rozdzielczości zapewnia oryginalną jakość obrazów i umożliwia korzystanie z modeli sztucznej inteligencji opartych na obrazie dostarczanych przez usługi Microsoft Computer Vision i Custom Vision, aby uzyskać jeszcze więcej szczegółowych informacji z wideo.

## <a name="september-2019"></a>Wrzesień 2019 r.

###  <a name="media-services-v3"></a>Media Services v3  

#### <a name="live-linear-encoding-of-live-events"></a>Liniowe kodowanie na żywo wydarzeń na żywo

Media Services v3 ogłasza podgląd 24 godzin x 365 dni na żywo liniowego kodowania wydarzeń na żywo.

###  <a name="media-services-v2"></a>Media Services v2  

#### <a name="deprecation-of-media-processors"></a>Wycofanie procesorów multimediów

Ogłaszamy wycofanie *indeksatora multimediów Azure* i usługi Azure Media *Indexer 2 Preview.* W przypadku dat wycofania zobacz temat [starszych składników.](../previous/legacy-components.md) [Indeksator wideo usługi Azure Media Services](https://docs.microsoft.com/azure/media-services/video-indexer/) zastępuje te starsze procesory multimediów.

Aby uzyskać więcej informacji, zobacz [Migrowanie z indeksatora multimediów azure i indeksatora multimediów azure 2 do indeksatora wideo usługi Azure Media Services.](../previous/migrate-indexer-v1-v2.md)

## <a name="august-2019"></a>Sierpień 2019 r.

###  <a name="media-services-v3"></a>Media Services v3  

#### <a name="south-africa-regional-pair-is-open-for-media-services"></a>Para regionalna w Republice Południowej Afryki jest otwarta dla Media Services 

Usługi Media Services są teraz dostępne w regionach Afryki Północnej i Południowej Afryki Zachodniej.

Aby uzyskać więcej informacji, zobacz [Chmury i regiony, w których istnieje usługi Media Services w wersji 3](azure-clouds-regions.md).

###  <a name="media-services-v2"></a>Media Services v2  

#### <a name="deprecation-of-media-processors"></a>Wycofanie procesorów multimediów

Ogłaszamy wycofanie procesorów *multimediów Windows Azure Media Encoder* (WAME) i Azure Media *Encoder* (AME), które są wycofywane. Aby uzyskać daty wycofania, zobacz ten temat [starszych składników.](../previous/legacy-components.md)

Aby uzyskać szczegółowe informacje, zobacz [Migrowanie wame do standardu kodera multimediów](https://go.microsoft.com/fwlink/?LinkId=2101334) i [migrowanie AME do media encoder Standard](https://go.microsoft.com/fwlink/?LinkId=2101335).
 
## <a name="july-2019"></a>Lipiec 2019 r.

### <a name="content-protection"></a>Ochrona zawartości

Podczas przesyłania strumieniowego zawartości chronionej za pomocą ograniczenia tokenu użytkownicy końcowi muszą uzyskać token, który jest wysyłany w ramach żądania dostarczenia klucza. Funkcja *zapobiegania powtarzaniu powtórzów tokenu* umożliwia klientom usługi Media Services ustawienie limitu liczby razy tego samego tokenu, który może być użyty do żądania klucza lub licencji. Aby uzyskać więcej informacji, zobacz [Zapobieganie powtarzaniu powtórzów tokenów](content-protection-overview.md#token-replay-prevention).

Od lipca funkcja podglądu była dostępna tylko w centrum USA i w centrum usa west central.

## <a name="june-2019"></a>Czerwiec 2019 r.

### <a name="video-subclipping"></a>Podksyt wideo

Teraz można przycinać lub subklipować wideo podczas kodowania go za pomocą [zadania](https://docs.microsoft.com/rest/api/media/jobs). 

Ta funkcja działa z dowolnego [przekształcenia,](https://docs.microsoft.com/rest/api/media/transforms) który jest zbudowany przy użyciu [wbudowanych wbudowanychstandardencoderpreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) presetów lub [standardencoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) presetów. 

Zobacz przykłady:

* [Subclip wideo z .NET](subclip-video-dotnet-howto.md)
* [Subclip wideo z REST](subclip-video-rest-howto.md)

## <a name="may-2019"></a>Maj 2019 r.

### <a name="azure-monitor-support-for-media-services-diagnostic-logs-and-metrics"></a>Obsługa dzienników diagnostycznych i metryk usługi Azure Monitor dla usług Media Services

Teraz można użyć usługi Azure Monitor do wyświetlania danych telemetrycznych emitowanych przez usługę Media Services.

* Dzienniki diagnostyczne usługi Azure Monitor służy do monitorowania żądań wysyłanych przez punkt końcowy dostarczania kluczy usługi Media Services. 
* Monitorowanie metryk emitowanych przez [punkty końcowe przesyłania strumieniowego](streaming-endpoint-concept.md)usług Media Services .   

Aby uzyskać szczegółowe informacje, zobacz [Monitorowanie metryk i dzienników diagnostycznych usługi Media Services](media-services-metrics-diagnostic-logs.md).

### <a name="multi-audio-tracks-support-in-dynamic-packaging"></a>Obsługa wielu ścieżek audio w dynamic packaging 

Podczas przesyłania strumieniowego zasobów, które mają wiele ścieżek audio z wieloma kodekami i językami, [dynamic packaging](dynamic-packaging-overview.md) obsługuje teraz wiele ścieżek audio dla wyjścia HLS (wersja 4 lub wyższa).

### <a name="korea-regional-pair-is-open-for-media-services"></a>Korea regionalna para jest otwarta dla Media Services 

Usługi media są teraz dostępne w regionach Korei Środkowej i Południowej. 

Aby uzyskać więcej informacji, zobacz [Chmury i regiony, w których istnieje usługi Media Services w wersji 3](azure-clouds-regions.md).

### <a name="performance-improvements"></a>Ulepszenia wydajności

Dodano aktualizacje, które obejmują poprawę wydajności usługi Media Services.

* Zaktualizowano maksymalny rozmiar pliku obsługiwany do przetwarzania. Zobacz, [Przydziały i limity](limits-quotas-constraints.md).
* [Ulepszenia szybkości kodowania](media-reserved-units-cli-how-to.md#choosing-between-different-reserved-unit-types).

## <a name="april-2019"></a>Kwiecień 2019 r.

### <a name="new-presets"></a>Nowe ustawienia wstępne

* [FaceDetectorPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#facedetectorpreset) został dodany do wbudowanych ustawień predefiniowanych analizatora.
* [ContentAwareEncodingExperimental](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset) został dodany do wbudowanych ustawień ekkodera. Aby uzyskać więcej informacji, zobacz [Kodowanie z uwzględnieniem zawartości](content-aware-encoding.md). 

## <a name="march-2019"></a>Marzec 2019 r.

Dynamic Packaging obsługuje teraz technologię Dolby Atmos. Aby uzyskać więcej informacji, zobacz [Kodeki audio obsługiwane przez dynamiczne opakowanie](dynamic-packaging-overview.md#audio-codecs).

Teraz można określić listę filtrów zasobów lub kont, które miałyby zastosowanie do lokalizatora przesyłania strumieniowego. Aby uzyskać więcej informacji, zobacz [Kojarzenie filtrów z lokalizatorem przesyłania strumieniowego](filters-concept.md#associating-filters-with-streaming-locator).

## <a name="february-2019"></a>Luty 2019 r.

Usługa Media Services w wersji 3 jest teraz obsługiwana w chmurach krajowych platformy Azure. Nie wszystkie funkcje są jeszcze dostępne we wszystkich chmurach. Aby uzyskać szczegółowe informacje, zobacz [Chmury i regiony, w których istnieje usługa Azure Media Services w wersji 3.](azure-clouds-regions.md)

[Zdarzenie Microsoft.Media.JobOutputProgress](media-services-event-schemas.md#monitoring-job-output-progress) zostało dodane do schematów usługi Azure Event Grid for Media Services.

## <a name="january-2019"></a>Styczeń 2019 r.

### <a name="media-encoder-standard-and-mpi-files"></a>Pliki Media Encoder Standard i MPI 

Podczas kodowania za pomocą media encoder Standard w celu tworzenia plików MP4 generowany jest nowy plik .mpi i dodawany do wyjściowego zasobu. Ten plik MPI ma na celu zwiększenie wydajności dla [dynamicznego pakowania](dynamic-packaging-overview.md) i przesyłania strumieniowego scenariuszy.

Nie należy modyfikować ani usuwać pliku MPI ani przyjmować żadnych zależności w usłudze od istnienia (lub nie) takiego pliku.

## <a name="december-2018"></a>Grudzień 2018 r.

Aktualizacje z wersji GA interfejsu API V3 obejmują:
       
* **Właściwości PresentationTimeRange** nie są już "wymagane" dla **filtrów zasobów** i **filtrów kont**. 
* Usunięto opcje kwerend $top i $skip dla **zadań** i **przekształceń** i dodano $orderby. W ramach dodawania nowej funkcji zamawiania odkryto, że opcje $top i $skip zostały przypadkowo ujawnione wcześniej, mimo że nie są zaimplementowane.
* Rozszerzalność wyliczenia została ponownie włączona. Ta funkcja została włączona w wersjach zapoznawców sdk i została przypadkowo wyłączona w wersji GA.
* Zmieniono nazwę dwóch wstępnie zdefiniowanych zasad przesyłania strumieniowego. **SecureStreaming** jest teraz **MultiDrmCencStreaming**. **SecureStreamingWithFairPlay** jest teraz **Predefined_MultiDrmStreaming**.

## <a name="november-2018"></a>Listopad 2018 r.

Moduł CLI 2.0 jest teraz dostępny dla [usługi Azure Media Services w wersji 3 GA](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest) — v 2.0.50.

### <a name="new-commands"></a>Nowe polecenia

- [konto az ams](https://docs.microsoft.com/cli/azure/ams/account?view=azure-cli-latest)
- [az ams konto-filtr](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest)
- [az ams aktywa](https://docs.microsoft.com/cli/azure/ams/asset?view=azure-cli-latest)
- [az ams asset-filter az ams](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest)
- [az ams content-key-policy](https://docs.microsoft.com/cli/azure/ams/content-key-policy?view=azure-cli-latest)
- [az ams praca](https://docs.microsoft.com/cli/azure/ams/job?view=azure-cli-latest)
- [az ams live-event](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest)
- [az ams live-wyjście](https://docs.microsoft.com/cli/azure/ams/live-output?view=azure-cli-latest)
- [az ams streaming-punkt końcowy](https://docs.microsoft.com/cli/azure/ams/streaming-endpoint?view=azure-cli-latest)
- [lokalizator strumieniowy az ams](https://docs.microsoft.com/cli/azure/ams/streaming-locator?view=azure-cli-latest)
- [konto az ams mru](https://docs.microsoft.com/cli/azure/ams/account/mru?view=azure-cli-latest) - umożliwia zarządzanie Media Reserved Units. Aby uzyskać więcej informacji, zobacz [Skalowanie jednostek zarezerwowanych nośników](media-reserved-units-cli-how-to.md).

### <a name="new-features-and-breaking-changes"></a>Nowe funkcje i przełomowe zmiany

#### <a name="asset-commands"></a>Polecenia zasobów

- ```--storage-account```i ```--container``` argumenty dodane.
- Domyślne wartości dla czasu wygaśnięcia (Teraz + 23h) i uprawnień (Odczyt) w ```az ams asset get-sas-url``` poleceniu dodane.

#### <a name="job-commands"></a>Polecenia zadania

- ```--correlation-data```i ```--label``` dodano argumenty
- ```--output-asset-names```zmieniono ```--output-assets```nazwę na . Teraz akceptuje oddzieloną spację listę zasobów w formacie "assetName=label". Zasób bez etykiety może być wysyłany w ten sposób: 'assetName='.

#### <a name="streaming-locator-commands"></a>Polecenia lokalizatora przesyłania strumieniowego

- ```az ams streaming locator```polecenie podstawowe ```az ams streaming-locator```zastąpiono poleceniem .
- ```--streaming-locator-id```i ```--alternative-media-id support``` argumenty dodane.
- ```--content-keys argument```zaktualizowany argument.
- ```--content-policy-name```zmieniono ```--content-key-policy-name```nazwę na .

#### <a name="streaming-policy-commands"></a>Polecenia zasad przesyłania strumieniowego

- ```az ams streaming policy```polecenie podstawowe ```az ams streaming-policy```zastąpiono poleceniem .
- Parametry szyfrowania ```az ams streaming-policy create``` obsługują dodane.

#### <a name="transform-commands"></a>Przekształcanie poleceń

- ```--preset-names```argumentem ```--preset```zastąpionym argumentem . Teraz można ustawić tylko 1 wyjście / preset na raz ```az ams transform output add```(aby dodać więcej trzeba uruchomić ). Ponadto można ustawić niestandardowe StandardEncoderPreset przekazując ścieżkę do niestandardowego JSON.
- ```az ams transform output remove```można wykonać, przekazując indeks wyjściowy, aby usunąć.
- ```--relative-priority, --on-error, --audio-language and --insights-to-extract```argumenty dodane ```az ams transform create``` ```az ams transform output add``` w i polecenia.

## <a name="october-2018---ga"></a>Październik 2018 - GA

W tej sekcji opisano aktualizacje usługi Azure Media Services (AMS) w październiku.

### <a name="rest-v3-ga-release"></a>REST v3 wersja GA

[Wersja REST v3 GA](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01) zawiera więcej interfejsów API dla filtrów manifestu na poziomie konta/zasobu i obsługi drm.

#### <a name="azure-resource-management"></a>Zarządzanie zasobami platformy Azure 

Obsługa usługi Azure Resource Management umożliwia ujednolicone zarządzanie i operacje interfejsu API (teraz wszystko w jednym miejscu).

Począwszy od tej wersji, można użyć szablonów Menedżera zasobów do tworzenia wydarzeń na żywo.

#### <a name="improvement-of-asset-operations"></a>Usprawnienie operacji na aktywach 

Wprowadzono następujące ulepszenia:

- Pozyskiwania z adresów URL http(ów) lub adresów URL SYGNATUR USŁUGI Azure Blob Storage.
- Określ własne nazwy kontenerów dla zasobów. 
- Łatwiejsza obsługa danych wyjściowych w celu tworzenia niestandardowych przepływów pracy za pomocą usługi Azure Functions.

#### <a name="new-transform-object"></a>Nowy obiekt Transform

Nowy obiekt **Przekształcanie** upraszcza model kodowania. Nowy obiekt ułatwia tworzenie i udostępnianie szablonów i ustawień predefiniowanych Menedżera zasobów kodowania. 

#### <a name="azure-active-directory-authentication-and-rbac"></a>Uwierzytelnianie usługi Azure Active Directory i rbac

Uwierzytelnianie usługi Azure AD i kontrola dostępu oparta na rolach (RBAC) umożliwiają bezpieczne transformacje, liveEvents, zasady klucza zawartości lub zasoby według roli lub użytkowników w usłudze Azure AD.

#### <a name="client-sdks"></a>Zestawy SDK klienta  

Języki obsługiwane w umiaźnych usługach Media Services w wersji 3: .NET Core, Java, Node.js, Ruby, Typescript, Python, Go.

#### <a name="live-encoding-updates"></a>Aktualizacje kodowania na żywo

Wprowadzono następujące aktualizacje kodowania na żywo:

- Nowy tryb niskiego opóźnienia na żywo (10 sekund end-to-end).
- Ulepszona obsługa RTMP (zwiększona stabilność i większa obsługa kodera źródłowego).
- RTMPS bezpieczne połknienia.

    Podczas tworzenia wydarzenia na żywo otrzymujesz teraz 4 adresy URL pozyskiwania. 4 adresy URL pozyskiwania są prawie identyczne, mają ten sam token przesyłania strumieniowego (AppId), tylko część numeru portu jest inna. Dwa adresy URL są podstawowymi i kopiami zapasowymi dla RTMPS. 
- 24-godzinna obsługa transkodowania. 
- Ulepszona obsługa sygnalizacji reklam w RTMP przez SCTE35.

#### <a name="improved-event-grid-support"></a>Ulepszona obsługa siatki zdarzeń

Możesz zobaczyć następujące ulepszenia obsługi siatki zdarzeń:

- Integracja z usługą Azure Event Grid w celu łatwiejszego programowania za pomocą aplikacji logiki i usług Azure Functions. 
- Subskrybuj zdarzenia dotyczące kodowania, kanałów na żywo i innych.

### <a name="cmaf-support"></a>Wsparcie CMAF

Obsługa szyfrowania CMAF i "cbcs" dla odtwarzaczy Apple HLS (iOS 11+) i MPEG-DASH obsługujących cmaf.

### <a name="video-indexer"></a>Indeksator wideo

Video Indexer GA prasowa została ogłoszona w sierpniu. Aby uzyskać nowe informacje na temat aktualnie obsługiwanych funkcji, zobacz [Co to jest indeksator wideo](../../cognitive-services/video-indexer/video-indexer-overview.md?toc=/azure/media-services/video-indexer/toc.json&bc=/azure/media-services/video-indexer/breadcrumb/toc.json). 

### <a name="plans-for-changes"></a>Plany zmian

#### <a name="azure-cli-20"></a>Interfejs wiersza polecenia platformy Azure 2.0
 
Moduł interfejsu wiersza polecenia platformy Azure 2.0, który obejmuje operacje na wszystkich funkcjach (w tym na żywo, zasady klucza zawartości, filtry kont/zasobów, zasady przesyłania strumieniowego) już wkrótce. 

### <a name="known-issues"></a>Znane problemy

Następujący problem dotyczy tylko klientów, którzy korzystali z interfejsu API w wersji zapoznawczej dla środków trwałych lub filtrów kont.

Jeśli zasoby lub filtry kont zostały utworzone między 28.09 a 10.12 za pomocą interfejsu wiersza polecenia lub interfejsów API usługi Media Services w wersji 3, należy usunąć wszystkie filtry zasobów i kont i ponownie utworzyć je z powodu konfliktu wersji. 

## <a name="may-2018---preview"></a>Maj 2018 - Wersja zapoznawcza

### <a name="net-sdk"></a>Zestaw SDK .NET

W sdku .NET występują następujące funkcje:

* **Przekształca** i **zadania** do kodowania lub analizowania zawartości multimedialnej. Przykłady można znaleźć [w pliku Strumieniowanie plików](stream-files-tutorial-with-api.md) i [Analizuj](analyze-videos-tutorial-with-api.md).
* **Lokalizatory przesyłania strumieniowego** do publikowania i przesyłania strumieniowego zawartości na urządzeniach użytkowników końcowych
* **Zasady przesyłania strumieniowego** i **zasady kluczy zawartości** w celu skonfigurowania dostarczania kluczy i ochrony zawartości (DRM) podczas dostarczania zawartości.
* **Wydarzenia na żywo** i **wyjścia na żywo,** aby skonfigurować pozyskiwania i archiwizowanie zawartości strumieniowej na żywo.
* **Zasoby** do przechowywania i publikowania zawartości multimedialnej w usłudze Azure Storage. 
* **Punkty końcowe przesyłania strumieniowego** do konfigurowania i skalowania dynamicznego pakowania, szyfrowania i przesyłania strumieniowego zarówno dla zawartości multimedialnej na żywo, jak i na żądanie.

### <a name="known-issues"></a>Znane problemy

* Podczas przesyłania zadania można określić, aby pozyskiwać źródłowy film wideo przy użyciu adresów URL HTTPS, adresów URL sygnatury dostępu Współdzielonego lub ścieżek do plików znajdujących się w magazynie obiektów Blob platformy Azure. Obecnie usługa AMS w wersji 3 nie obsługuje fragmentarycznego kodowania transferu przez adresy URL HTTPS.

## <a name="ask-questions-give-feedback-get-updates"></a>Zadawaj pytania, przekazuj opinie, otrzyj aktualizacje

Zapoznaj się z artykułem [społeczności usługi Azure Media Services,](media-services-community.md) aby zobaczyć różne sposoby zadawania pytań, przekazywania opinii i otrzymywać aktualizacje dotyczące usługi Media Services.

## <a name="next-steps"></a>Następne kroki

- [Omówienie](media-services-overview.md)
- [Aktualizacje dokumentacji usługi Media Services w wersji 3](docs-release-notes.md)
- [Informacje o wersji usługi Media Services w wersji 2](../previous/media-services-release-notes.md)
