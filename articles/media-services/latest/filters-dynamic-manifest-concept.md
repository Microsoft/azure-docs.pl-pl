---
title: Filtrowanie manifestów przy użyciu Pakowarki dynamicznej
description: Dowiedz się, jak tworzyć filtry przy użyciu Pakowarki dynamicznego do filtrowania i selektywnego przesyłania strumieniowego manifestów.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 1fb8724edc32477bb1c399db0493833e9df984bb
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106277576"
---
# <a name="filter-your-manifests-using-dynamic-packager"></a>Filtrowanie manifestów przy użyciu Pakowarki dynamicznej

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

W przypadku dostarczania zawartości przesyłania strumieniowego z adaptacyjną szybkością transmisji bitów do urządzeń czasami konieczne jest opublikowanie wielu wersji manifestu w celu uwzględnienia konkretnych możliwości urządzenia lub dostępnej przepustowości sieci. [Pakowarka dynamiczna](encode-dynamic-packaging-concept.md) pozwala określić filtry, które mogą odfiltrować określone kodery-de, rozdzielczości, szybkości transmisji bitów i kombinacje ścieżek audio na bieżąco. To filtrowanie eliminuje konieczność tworzenia wielu kopii. Wystarczy opublikować nowy adres URL z określonym zestawem filtrów skonfigurowanym dla urządzeń docelowych (iOS, Android, biznesu SmartTV lub przeglądarki) oraz możliwości sieci (scenariusze o wysokiej przepustowości, mobilnych lub niskiej przepustowości). W takim przypadku klienci mogą manipulować przesyłaniem strumieniowym zawartości za pośrednictwem ciągu zapytania (przez określenie dostępnych [filtrów zasobów lub filtrów kont](filters-concept.md)) i używać filtrów do przesyłania strumieniowego określonych sekcji strumienia.

Niektóre scenariusze dostarczania wymagają upewnienia się, że klient nie może uzyskać dostępu do określonych ścieżek. Na przykład może nie chcieć opublikować manifestu, który zawiera ścieżki HD do określonej warstwy subskrybenta. Lub, może być konieczne usunięcie określonych ścieżek z adaptacyjną szybkością transmisji bitów (ABR), aby zmniejszyć koszty dostarczania do określonego urządzenia, które nie byłoby korzystne w przypadku dodatkowych śledzenia. W takim przypadku można skojarzyć listę wstępnie utworzonych filtrów z [lokalizatorem przesyłania strumieniowego](stream-streaming-locators-concept.md) podczas tworzenia. Następnie klienci nie mogą manipulować strumieniem zawartości, ponieważ jest ona definiowana przez **lokalizator przesyłania strumieniowego**.

Filtrowanie można łączyć przez określenie [filtrów w lokalizatorze przesyłania strumieniowego](filters-concept.md#associating-filters-with-streaming-locator) + dodatkowe filtry specyficzne dla urządzenia, które klient określa w adresie URL. Ta kombinacja jest przydatna do ograniczania dodatkowych ścieżek, takich jak metadane, strumienie zdarzeń, Języki audio lub ścieżki audio opisowe.

Ta możliwość określania różnych filtrów w strumieniu zapewnia zaawansowane rozwiązanie do **dynamicznego manipulowania manifestem** ukierunkowane na wiele scenariuszy przypadków użycia dla urządzeń docelowych. W tym temacie objaśniono koncepcje dotyczące **manifestów dynamicznych** i przedstawiono przykłady scenariuszy, w których można użyć tej funkcji.

> [!NOTE]
> Manifesty dynamiczne nie zmieniają elementu zawartości i domyślnego manifestu dla tego elementu zawartości.

## <a name="overview-of-manifests"></a>Omówienie manifestów

Azure Media Services obsługuje protokoły HLS, KRESKi MPEG i Smooth Streaming. W ramach tworzenia [pakietów dynamicznych](encode-dynamic-packaging-concept.md)manifesty klienta przesyłania strumieniowego (HLS Master list odtwarzania, opis prezentacji nośników kreskowych [MPD] i Smooth Streaming) są dynamicznie generowane na podstawie selektora formatu w adresie URL. Aby uzyskać więcej informacji, zobacz Protokoły dostarczania w [typowym przepływie pracy na żądanie](encode-dynamic-packaging-concept.md#to-prepare-your-source-files-for-delivery).

### <a name="get-and-examine-manifest-files"></a>Pobieranie i sprawdzanie plików manifestu

Należy określić listę warunków właściwości śledzenia filtru na podstawie ścieżek strumienia (na żywo lub wideo na żądanie [VOD]), które należy uwzględnić w dynamicznie tworzonym manifeście. Aby pobrać i przejrzeć właściwości ścieżek, należy najpierw załadować manifest Smooth Streaming.

[Pliki przekazywania, kodowania i przesyłania strumieniowego z użyciem samouczka platformy .NET](stream-files-tutorial-with-api.md#get-streaming-urls) pokazują, jak utworzyć adresy URL przesyłania strumieniowego przy użyciu platformy .NET. Po uruchomieniu aplikacji jeden z adresów URL wskazuje manifest Smooth Streaming: `https://amsaccount-usw22.streaming.media.azure.net/00000000-0000-0000-0000-0000000000000/ignite.ism/manifest` .<br/> Skopiuj i wklej adres URL na pasku adresu przeglądarki. Plik zostanie pobrany. Można go otworzyć w dowolnym edytorze tekstu.

Aby zapoznać się z przykładem REST, zobacz [przekazywanie, kodowanie i przesyłanie strumieniowe plików](stream-files-tutorial-with-rest.md#list-paths-and-build-streaming-urls).

### <a name="monitor-the-bitrate-of-a-video-stream"></a>Monitoruj szybkość transmisji strumienia wideo

Za pomocą [strony demonstracyjnej Azure Media Player](https://aka.ms/azuremediaplayer) można monitorować szybkość transmisji strumieniowej wideo. Na stronie demonstracyjnej są wyświetlane informacje diagnostyczne na karcie **Diagnostyka** :

![Diagnostyka Azure Media Player][amp_diagnostics]

### <a name="examples-urls-with-filters-in-query-string"></a>Przykłady: adresy URL z filtrami w ciągu zapytania

Można zastosować filtry do protokołów ABR przesyłania strumieniowego: HLS, MPEG-KRESKa i Smooth Streaming. W poniższej tabeli przedstawiono kilka przykładów adresów URL z filtrami:

|Protokół|Przykład|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="rendition-filtering"></a>Filtrowanie wersji

Możesz zakodować zasób do wielu profilów kodowania (H. 264), H. 264 High, AACL, AACH, Dolby Digital Plus) i wielu szybkości transmisji bitów. Jednak nie wszystkie urządzenia klienckie będą obsługiwać wszystkie profile zasobów i szybkości transmisji bitów. Na przykład starsze urządzenia z systemem Android obsługują tylko linię bazową H. 264 i AACL. Wysyłanie wyższych szybkości transmisji bitów do urządzenia, które nie mogą uzyskać korzyści z ograniczenia przepustowości i urządzenia. Takie urządzenie musi zdekodować wszystkie dane, tylko do skalowania w dół na potrzeby wyświetlania.

Za pomocą manifestu dynamicznego można utworzyć profile urządzeń (takie jak Mobile, Console lub HD/SD) i uwzględnić ścieżki i jakości, które mają być częścią każdego profilu. To nazywa się filtrowaniem wersji. Na poniższym diagramie przedstawiono przykład.

![Przykład filtrowania odwzorowania z manifestem dynamicznym][renditions2]

W poniższym przykładzie koder został użyty do zakodowania zasobu Mezzanine do siedmiu plików ISO pliki MP4 (od 180p do 1080p). Zakodowany element zawartości może być [dynamicznie spakowany](encode-dynamic-packaging-concept.md) w jednym z następujących protokołów przesyłania strumieniowego: HLS, pauzy MPEG i gładki.

W górnej części poniższego diagramu przedstawiono manifest HLS dla elementu zawartości bez filtrów. (Zawiera wszystkie siedem wersji).  W lewym dolnym rogu diagram przedstawia manifest HLS, do którego zastosowano filtr o nazwie "OTT". Filtr "OTT" określa usunięcie wszystkich szybkości transmisji bitów poniżej 1 MB/s, tak aby dwa poziomy jakości dolne zostały usunięte w odpowiedzi. W prawym dolnym rogu Diagram pokazuje manifest HLS, do którego zastosowano filtr o nazwie "Mobile". Filtr "Mobile" określa usunięcie wersji, gdzie rozdzielczość jest większa niż 720, więc dwie wersje 1080p zostały usunięte.

![Filtrowanie wersji przy użyciu manifestu dynamicznego][renditions1]

## <a name="removing-language-tracks"></a>Usuwanie ścieżek języka
Twoje zasoby mogą obejmować wiele języków audio, takich jak angielski, hiszpański, francuski i tak dalej. Zazwyczaj zestaw SDK odtwarzacza zarządza domyślnymi opcjami śledzenia dźwięku i dostępne ścieżki audio na wybór użytkownika.

Opracowywanie takich zestawów SDK odtwarzacza jest trudne, ponieważ wymaga różnych implementacji dla środowisk odtwarzacza właściwych dla konkretnych urządzeń. Ponadto na niektórych platformach interfejsy API odtwarzacza są ograniczone i nie obejmują funkcji wyboru audio, w której użytkownicy nie mogą wybrać ani zmienić domyślnej ścieżki audio. Filtry zasobów umożliwiają sterowanie zachowaniem przez tworzenie filtrów, które zawierają tylko odpowiednie języki audio.

![Filtrowanie ścieżek języka z manifestem dynamicznym][language_filter]

## <a name="trimming-the-start-of-an-asset"></a>Przycinanie początku elementu zawartości

W przypadku większości zdarzeń przesyłania strumieniowego na żywo operatory uruchamiają niektóre testy przed rzeczywistym zdarzeniem. Na przykład mogą one zawierać najbardziej za, podobne do tego przed rozpoczęciem zdarzenia: "Program rozpocznie się chwilę".

Jeśli program jest archiwizowany, dane testowe i niezawierające są również archiwizowane i uwzględniane w prezentacji. Jednak te informacje nie powinny być widoczne dla klientów. Za pomocą manifestu dynamicznego można utworzyć filtr czasu rozpoczęcia i usunąć niechciane dane z manifestu.

![Przycinanie początku elementu zawartości z manifestem dynamicznym][trim_filter]

## <a name="creating-subclips-views-from-a-live-archive"></a>Tworzenie podklipów (widoków) z archiwum na żywo

Wiele wydarzeń na żywo jest długotrwałych, a Archiwum na żywo może zawierać wiele zdarzeń. Po zakończeniu wydarzenia na żywo nadawcy mogą chcieć podzielić Archiwum na żywo na uruchomienie programu logicznego i Zatrzymaj sekwencje.

Te programy wirtualne można opublikować oddzielnie bez przetwarzania na żywo Archiwum i bez tworzenia oddzielnych zasobów (co nie przynosi korzyści dla istniejących fragmentów pamięci podręcznej w sieci CDN). Przykładami takich programów wirtualnych są kwartały gry piłkarskiej lub Basketball, innings w siatkówki lub pojedyncze zdarzenia dowolnego programu sportowego.

Za pomocą manifestu dynamicznego można tworzyć filtry przy użyciu godzin rozpoczęcia/zakończenia i tworzyć widoki wirtualne w górnej części archiwum na żywo.

![Filtr podklipu z manifestem dynamicznym][subclip_filter]

Oto przefiltrowany element zawartości:

![Filtrowany element zawartości z manifestem dynamicznym][skiing]

## <a name="adjusting-the-presentation-window-dvr"></a>Dostosowywanie okna prezentacji (DVR)

Obecnie Azure Media Services oferuje archiwum cykliczne, w którym można skonfigurować czas trwania od 1 minuty do 25 godzin. Filtrowanie manifestu może służyć do tworzenia okna wycofywania DVR w górnej części archiwum, bez usuwania nośnika. Istnieje wiele scenariuszy, w których nadawcy chcą mieć ograniczone okno DVR, aby można je było przenieść razem z aktywną krawędzią i w tym samym czasie pozostawić większe okno archiwizowania. Nadawca może chcieć użyć danych z okna DVR, aby wyróżnić klipy, lub może chcieć udostępnić różne okna DVR dla różnych urządzeń. Na przykład większość urządzeń przenośnych nie obsługuje dużych okien DVR (w przypadku urządzeń przenośnych można korzystać z 2-minutowego okna DVR) i godziny dla klientów stacjonarnych.

![Okno DVR z manifestem dynamicznym][dvr_filter]

## <a name="adjusting-livebackoff-live-position"></a>Dostosowywanie LiveBackoff (pozycja na żywo)

Filtrowanie manifestu może służyć do usuwania kilku sekund od aktywnej krawędzi programu na żywo. Filtrowanie pozwala nadawcom oglądać prezentację w punkcie publikacji w wersji zapoznawczej i tworzyć punkty wstawiania anonsów przed odebraniem strumienia (za pomocą kopii zapasowej przez 30 sekund). Nadawcy mogą następnie wypchnąć te anonse do swoich platform klienta w czasie, aby mogły odbierać i przetwarzać informacje przed możliwością anonsowania.

Poza obsługą anonsów Ustawienia aktywnej kopii zapasowej można użyć do dostosowania położenia osób przeglądających, dzięki czemu klienci będą mogli odrywać i trafiać fragmenty z serwera. Dzięki temu klienci nie będą mogli uzyskać błędu HTTP 404 lub 412.

![Filtrowanie z powrotem na żywo z manifestem dynamicznym][livebackoff_filter]

## <a name="combining-multiple-rules-in-a-single-filter"></a>Łączenie wielu reguł w jednym filtrze

W jednym filtrze można połączyć wiele reguł filtrowania. Na przykład można zdefiniować "regułę zakresu", aby usunąć z archiwum na żywo, a także odfiltrować dostępne szybkości transmisji bitów. W przypadku stosowania wielu reguł filtrowania wynik końcowy jest częścią wspólną wszystkich reguł.

![Wiele reguł filtrowania z manifestem dynamicznym][multiple-rules]

## <a name="combining-multiple-filters-filter-composition"></a>Łączenie wielu filtrów (kompozycja filtrów)

Można również połączyć wiele filtrów w jednym adresie URL. W poniższym scenariuszu przedstawiono, dlaczego warto połączyć filtry:

1. Należy odfiltrować jakość wideo dla urządzeń przenośnych, takich jak Android lub iPad (w celu ograniczenia jakości wideo). Aby usunąć niepożądane jakości, należy utworzyć filtr konta odpowiedni dla profilów urządzeń. Filtry kont można używać dla wszystkich zasobów w ramach tego samego konta Media Services bez żadnych dalszych powiązań.
1. Należy również przyciąć godzinę rozpoczęcia i zakończenia elementu zawartości. Aby wykonać przycinanie, utworzysz filtr zasobów i ustawisz godzinę rozpoczęcia/zakończenia.
1. Chcesz połączyć oba te filtry. Bez kombinacji należy dodać filtrowanie jakości do filtru przycinania, co spowodowałoby trudniejsze użycie filtru.

Aby połączyć filtry, należy ustawić nazwy filtrów na adres URL manifestu/listy odtwarzania w formacie rozdzielanym średnikami. Przyjmijmy, że masz filtr o nazwie *MyMobileDevice* , który filtruje różne właściwości, i masz inną nazwę " *StartTime* ", aby ustawić określony czas rozpoczęcia. Można połączyć maksymalnie trzy filtry.

Aby uzyskać więcej informacji, zobacz [ten wpis w blogu](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).

## <a name="considerations-and-limitations"></a>Istotne zagadnienia i ograniczenia

- Wartości dla **forceEndTimestamp**, **presentationWindowDuration** i **liveBackoffDuration** nie należy ustawiać dla filtru VOD. Są one używane tylko w scenariuszach filtrowania na żywo.
- Manifest dynamiczny działa w granicach grupę GOP (klatek kluczowych), dlatego przycinanie ma dokładność grupę GOP.
- Możesz użyć tej samej nazwy filtru dla kont i filtrów zasobów. Filtry zasobów mają wyższy priorytet i przesłonią filtry konta.
- W przypadku aktualizacji filtru może upłynąć do 2 minut, zanim punkt końcowy przesyłania strumieniowego odświeża reguły. W przypadku użycia filtrów do obsłużenia zawartości (i przepełnienia zawartości w pamięci podręcznej serwerów proxy i sieci CDN) aktualizowanie tych filtrów może skutkować awariami odtwarzacza. Zalecamy wyczyszczenie pamięci podręcznej po zaktualizowaniu filtru. Jeśli ta opcja nie jest możliwa, należy rozważyć użycie innego filtru.
- Klienci muszą ręcznie pobrać manifest i przeanalizować dokładną sygnaturę czasową rozpoczęcia i skalę czasu.

    - Aby określić właściwości ścieżek w elemencie zawartości, [Pobierz i sprawdź plik manifestu](#get-and-examine-manifest-files).
    - Formuła do ustawiania właściwości sygnatury czasowej filtru zasobów: <br/>startTimestamp = &lt; czas rozpoczęcia w manifeście &gt;  +   &lt; oczekiwany czas rozpoczęcia filtru w sekundach &gt; * Skala czasu

## <a name="next-steps"></a>Następne kroki

W poniższych artykułach pokazano, jak programowo utworzyć filtry:  

- [Tworzenie filtrów za pomocą interfejsów API REST](filters-dynamic-manifest-rest-howto.md)
- [Tworzenie filtrów za pomocą programu .NET](filters-dynamic-manifest-dotnet-how-to.md)
- [Tworzenie filtrów za pomocą interfejsu wiersza polecenia](filters-dynamic-manifest-cli-how-to.md)

[renditions1]: ./media/filters-dynamic-manifest-concept/media-services-rendition-filter.png
[renditions2]: ./media/filters-dynamic-manifest-concept/media-services-rendition-filter2.png

[rendered_subclip]: ./media/filters-dynamic-manifests/media-services-rendered-subclip.png
[timeline_trim_event]: ./media/filters-dynamic-manifests/media-services-timeline-trim-event.png
[timeline_trim_subclip]: ./media/filters-dynamic-manifests/media-services-timeline-trim-subclip.png

[multiple-rules]:./media/filters-dynamic-manifest-concept/media-services-multiple-rules-filters.png

[subclip_filter]: ./media/filters-dynamic-manifest-concept/media-services-subclips-filter.png
[trim_event]: ./media/filters-dynamic-manifests/media-services-timeline-trim-event.png
[trim_subclip]: ./media/filters-dynamic-manifests/media-services-timeline-trim-subclip.png
[trim_filter]: ./media/filters-dynamic-manifest-concept/media-services-trim-filter.png
[redered_subclip]: ./media/filters-dynamic-manifests/media-services-rendered-subclip.png
[livebackoff_filter]: ./media/filters-dynamic-manifest-concept/media-services-livebackoff-filter.png
[language_filter]: ./media/filters-dynamic-manifest-concept/media-services-language-filter.png
[dvr_filter]: ./media/filters-dynamic-manifest-concept/media-services-dvr-filter.png
[skiing]: ./media/filters-dynamic-manifest-concept/media-services-skiing.png
[amp_diagnostics]: ./media/filters-dynamic-manifest-concept/amp_diagnostics.png
