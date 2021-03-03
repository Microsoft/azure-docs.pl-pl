---
title: Filtry i manifesty dynamiczne | Microsoft Docs
description: W tym temacie opisano sposób tworzenia filtrów, aby klient mógł używać ich do przesyłania strumieniowego określonych sekcji strumienia. Media Services tworzy dynamiczne manifesty do archiwizowania tego selektywnego przesyłania strumieniowego.
services: media-services
documentationcenter: ''
author: cenkdin
manager: femila
editor: ''
ms.assetid: ff102765-8cee-4c08-a6da-b603db9e2054
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 96f0c4d4ea7c32e2b58807204ef45b75feae7132
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101727332"
---
# <a name="filters-and-dynamic-manifests"></a>Filtry i manifesty dynamiczne

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector" title1="Wybierz używaną wersję Media Services:"]
> * [Wersja 2](media-services-dynamic-manifest-overview.md)
> * [Wersja 3](../latest/filters-dynamic-manifest-overview.md)

Począwszy od wersji 2,17, Media Services umożliwia zdefiniowanie filtrów dla zasobów. Te filtry są reguł po stronie serwera, które pozwolą klientom na wykonywanie takich czynności jak: odtwarzanie tylko w sekcji filmu wideo (zamiast odtwarzania całego wideo) lub określania tylko podzbioru plików audio i wideo, które może obsłużyć urządzenie klienta (zamiast wszystkich odniesień, które są skojarzone z zasobem). Takie filtrowanie zasobów jest realizowane za pośrednictwem **dynamicznego manifestu** s, które są tworzone na żądanie klienta w celu przesyłania strumieniowego wideo na podstawie określonych filtrów.

W tym temacie omówiono typowe scenariusze, w których używanie filtrów byłoby korzystne dla klientów i linki do tematów, które pokazują, jak tworzyć filtry programowo.

## <a name="overview"></a>Omówienie
Podczas dostarczania zawartości do klientów (przesyłanie strumieniowe zdarzeń na żywo lub wideo na żądanie) celem jest dostarczanie wysokiej jakości wideo do różnych urządzeń w różnych warunkach sieciowych. Aby osiągnąć ten cel, wykonaj następujące czynności:

* Koduj strumień do strumienia wideo o wysokiej szybkości transmisji bitów ([adaptacyjnej szybkości transmisji bitów](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)) (będzie to wymagać jakości i warunków sieciowych). 
* Użyj Media Services [dynamicznego](media-services-dynamic-packaging-overview.md) tworzenia pakietów, aby dynamicznie ponownie spakować strumień do różnych protokołów (będzie to wymagać przesyłania strumieniowego na różnych urządzeniach). Media Services obsługuje dostarczanie następujących technologii przesyłania strumieniowego z adaptacyjną szybkością transmisji bitów: HTTP Live Streaming (HLS), Smooth Streaming i KRESKa MPEG. 

### <a name="manifest-files"></a>Pliki manifestu
Podczas kodowania zasobu do przesyłania strumieniowego z adaptacyjną szybkością transmisji bitów, tworzony jest plik **manifestu** (listy odtwarzania) (plik jest oparty na tekście lub XML). Plik **manifestu** zawiera metadane przesyłania strumieniowego, takie jak: typ śledzenia (audio, wideo lub tekst), nazwa ścieżki, godzina rozpoczęcia i zakończenia, szybkość transmisji bitów (jakość), Języki śledzenia, okno prezentacji (okno przewijania stałego czasu trwania), koder-dekoder wideo (FourCC). Nakazuje również graczowi pobranie następnego fragmentu, dostarczając informacji o następnych, dostępnych fragmentach wideo i ich lokalizacji. Fragmenty (lub segmenty) to rzeczywiste "fragmenty zawartości wideo.

Oto przykład pliku manifestu: 

```xml
<?xml version="1.0" encoding="UTF-8"?>    
<SmoothStreamingMedia MajorVersion="2" MinorVersion="0" Duration="330187755" TimeScale="10000000">

<StreamIndex Chunks="17" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="8">
<QualityLevel Index="0" Bitrate="5860941" FourCC="H264" MaxWidth="1920" MaxHeight="1080" CodecPrivateData="0000000167640028AC2CA501E0089F97015202020280000003008000001931300016E360000E4E1FF8C7076850A4580000000168E9093525" />
<QualityLevel Index="1" Bitrate="4602724" FourCC="H264" MaxWidth="1920" MaxHeight="1080" CodecPrivateData="0000000167640028AC2CA501E0089F97015202020280000003008000001931100011EDC00002CD29FF8C7076850A45800000000168E9093525" />
<QualityLevel Index="2" Bitrate="3319311" FourCC="H264" MaxWidth="1280" MaxHeight="720" CodecPrivateData="000000016764001FAC2CA5014016EC054808080A00000300020000030064C0800067C28000103667F8C7076850A4580000000168E9093525" />
<QualityLevel Index="3" Bitrate="2195119" FourCC="H264" MaxWidth="960" MaxHeight="540" CodecPrivateData="000000016764001FAC2CA503C045FBC054808080A000000300200000064C1000044AA0000ABA9FE31C1DA14291600000000168E9093525" />
<QualityLevel Index="4" Bitrate="1469881" FourCC="H264" MaxWidth="960" MaxHeight="540" CodecPrivateData="000000016764001FAC2CA503C045FBC054808080A000000300200000064C04000B71A0000E4E1FF8C7076850A4580000000168E9093525" />
<QualityLevel Index="5" Bitrate="978815" FourCC="H264" MaxWidth="640" MaxHeight="360" CodecPrivateData="000000016764001EAC2CA50280BFE5C0548303032000000300200000064C08001E8480004C4B7F8C7076850A45800000000168E9093525" />
<QualityLevel Index="6" Bitrate="638374" FourCC="H264" MaxWidth="640" MaxHeight="360" CodecPrivateData="000000016764001EAC2CA50280BFE5C0548303032000000300200000064C080013D60000C65DFE31C1DA1429160000000168E9093525" />
<QualityLevel Index="7" Bitrate="388851" FourCC="H264" MaxWidth="320" MaxHeight="180" CodecPrivateData="000000016764000DAC2CA505067E7C054830303200000300020000030064C040030D40003D093F8C7076850A45800000000168E9093525" />

<c t="0" d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="9600000"/>
</StreamIndex>


<StreamIndex Chunks="17" Type="audio" Url="QualityLevels({bitrate})/Fragments(AAC_und_ch2_128kbps={start time})" QualityLevels="1" Name="AAC_und_ch2_128kbps">
<QualityLevel AudioTag="255" Index="0" BitsPerSample="16" Bitrate="125658" FourCC="AACL" CodecPrivateData="1210" Channels="2" PacketSize="4" SamplingRate="44100" />

<c t="0" d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="6965987" /></StreamIndex>


<StreamIndex Chunks="17" Type="audio" Url="QualityLevels({bitrate})/Fragments(AAC_und_ch2_56kbps={start time})" QualityLevels="1" Name="AAC_und_ch2_56kbps">
<QualityLevel AudioTag="255" Index="0" BitsPerSample="16" Bitrate="53655" FourCC="AACL" CodecPrivateData="1210" Channels="2" PacketSize="4" SamplingRate="44100" />

<c t="0" d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="6965987" /></StreamIndex>

</SmoothStreamingMedia>
```

### <a name="dynamic-manifests"></a>Manifesty dynamiczne
Istnieją [scenariusze](media-services-dynamic-manifest-overview.md#scenarios) , w których klient potrzebuje większej elastyczności niż opisywany w pliku manifestu domyślnego elementu zawartości. Na przykład:

* Specyficzne dla urządzenia: dostarcza tylko określone wersje i/lub określone ścieżki języka, które są obsługiwane przez urządzenie używane do odtwarzania zawartości ("filtrowanie wersji"). 
* Skróć manifest, aby pokazać podrzędny klip zdarzenia na żywo ("filtrowanie podklipu").
* Przytnij początek filmu wideo ("przycinanie filmu wideo").
* Dostosuj okno prezentacji (DVR), aby zapewnić ograniczoną długość okna DVR w odtwarzaczu ("Dostosowywanie okna prezentacji").

Aby osiągnąć tę elastyczność, Media Services oferuje **dynamiczne manifesty** na podstawie wstępnie zdefiniowanych [filtrów](media-services-dynamic-manifest-overview.md#filters).  Po zdefiniowaniu filtrów klienci mogą używać ich do przesyłania strumieniowego określonych plików lub podklipów wideo. Określają one filtry w adresie URL przesyłania strumieniowego. Filtry mogą być stosowane do protokołów przesyłania strumieniowego z adaptacyjną szybkością transmisji bitów obsługiwanej przez [pakowanie dynamiczne](media-services-dynamic-packaging-overview.md): HLS, MPEG-kreska i Smooth Streaming. Na przykład:

Adres URL KRESKi MPEG z filtrem

`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf,filter=MyLocalFilter)`

Smooth Streaming adres URL z filtrem

`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyLocalFilter)`


Aby uzyskać więcej informacji na temat sposobu dostarczania zawartości i tworzenia adresów URL przesyłania strumieniowego, zobacz [dostarczanie zawartości przegląd](media-services-deliver-content-overview.md).

> [!NOTE]
> Należy zauważyć, że manifesty dynamiczne nie zmieniają elementu zawartości i domyślnego manifestu dla tego elementu zawartości. Klient może wybrać żądanie przesyłania strumieniowego z filtrami lub bez. 
> 
> 

### <a name="filters"></a><a id="filters"></a>Filtry
Istnieją dwa typy filtrów zasobów: 

* Filtry globalne (mogą być stosowane do dowolnego elementu zawartości na koncie Azure Media Services, mają okres istnienia konta) i 
* Filtry lokalne (mogą być stosowane tylko do zasobu, z którym filtr został skojarzony podczas tworzenia, mają okres istnienia elementu zawartości). 

Globalne i lokalne typy filtrów mają dokładnie te same właściwości. Główną różnicą między tymi dwoma jest to, dla jakich scenariuszy, jakiego typu plik jest bardziej odpowiedni. Filtry globalne są zwykle odpowiednie dla profilów urządzeń (filtrowanie wersji), w których filtry lokalne mogą być używane do przycinania określonego elementu zawartości.

## <a name="common-scenarios"></a><a id="scenarios"></a>Typowe scenariusze
Jak wspomniano wcześniej, podczas dostarczania zawartości klientom (przesyłania strumieniowego na żywo lub filmów wideo na żądanie) celem jest dostarczanie wysokiej jakości wideo do różnych urządzeń w różnych warunkach sieciowych. Ponadto mogą istnieć inne wymagania, które obejmują filtrowanie zasobów i korzystanie z **manifestu dynamicznego**. Poniższe sekcje zawierają krótkie omówienie różnych scenariuszy filtrowania.

* Określ tylko podzestaw plików audio i wideo, które mogą obsłużyć niektóre urządzenia (zamiast wszystkich odwzorowania, które są skojarzone z zasobem). 
* Odtwarzaj tylko sekcję filmu wideo (zamiast odtwarzać cały film wideo).
* Dostosuj okno prezentacji DVR.

## <a name="rendition-filtering"></a>Filtrowanie wersji
Możesz zakodować zasób do wielu profilów kodowania (H. 264), H. 264 High, AACL, AACH, Dolby Digital Plus) i wielu szybkości transmisji bitów. Jednak nie wszystkie urządzenia klienckie będą obsługiwać wszystkie profile zasobów i szybkości transmisji bitów. Na przykład starsze urządzenia z systemem Android obsługują tylko linię bazową H. 264 i AACL. Wysyłanie wyższych szybkości transmisji bitów do urządzenia, które nie mogą uzyskać korzyści, podzielenia przepustowości i obliczeń urządzeń. Takie urządzenie musi zdekodować wszystkie dane, tylko w celu skalowania w dół na potrzeby wyświetlania.

Za pomocą manifestu dynamicznego można tworzyć profile urządzeń, takie jak Mobile, Console, HD/SD itp., a także dołączać ścieżki i cechy, które mają być częścią każdego profilu.

![Przykład filtrowania wersji][renditions2]

W poniższym przykładzie koder został użyty do zakodowania zasobu Mezzanine do siedmiu plików ISO pliki MP4 (od 180p do 1080p). Zakodowany element zawartości może być dynamicznie spakowany w jednym z następujących protokołów przesyłania strumieniowego: HLS, gładki i MPEG.  W górnej części diagramu zostanie wyświetlony manifest HLS dla elementu zawartości bez filtrów (zawiera on wszystkie siedem wersji).  W lewym dolnym rogu jest pokazywany manifest HLS, do którego zastosowano filtr o nazwie "OTT". Filtr "OTT" określa, aby usunąć wszystkie szybkości transmisji bitów poniżej 1Mbps, co spowodowało odłączenie dwóch dolnych poziomów jakości w odpowiedzi. W prawym dolnym rogu jest pokazywany manifest HLS, do którego zastosowano filtr o nazwie "Mobile". Filtr "mobilny" określa, aby usunąć wersje, w których rozdzielczość jest większa niż 720, co spowodowało odłączenie dwóch oddzielonych wersji.

![Filtrowanie wersji][renditions1]

## <a name="removing-language-tracks"></a>Usuwanie ścieżek języka
Twoje zasoby mogą zawierać wiele języków audio, takich jak angielski, hiszpański, francuski itp. Zazwyczaj Menedżer zestawu SDK odtwarzacza domyślnie wybiera ścieżki audio i dostępne ścieżki audio dla poszczególnych użytkowników. Nie trzeba opracowywać takich zestawów SDK odtwarzacza, wymaga to różnych implementacji dla graczy zależnych od konkretnego urządzenia. Ponadto na niektórych platformach interfejsy API odtwarzacza są ograniczone i nie obejmują funkcji wyboru audio, w której użytkownicy nie mogą wybrać ani zmienić domyślnej ścieżki audio. Filtry zasobów umożliwiają sterowanie zachowaniem przez tworzenie filtrów, które zawierają tylko odpowiednie języki audio.

![Filtrowanie ścieżek języka][language_filter]

## <a name="trimming-start-of-an-asset"></a>Przycinanie początku elementu zawartości
W przypadku większości zdarzeń przesyłania strumieniowego na żywo operatory uruchamiają niektóre testy przed rzeczywistym zdarzeniem. Na przykład mogą one zawierać najbardziej za, podobne do tego przed rozpoczęciem zdarzenia: "Program rozpocznie się chwilę". Jeśli program jest archiwizowany, dane testowe i niezawierające są również archiwizowane i uwzględniane w prezentacji. Te informacje nie powinny jednak być widoczne dla klientów. Za pomocą manifestu dynamicznego można utworzyć filtr czasu rozpoczęcia i usunąć niechciane dane z manifestu.

![Rozpoczęcie przycinania][trim_filter]

## <a name="creating-subclips-views-from-a-live-archive"></a>Tworzenie podklipów (widoków) z archiwum na żywo
Wiele wydarzeń na żywo jest długotrwałych, a Archiwum na żywo może zawierać wiele zdarzeń. Po zakończeniu wydarzenia na żywo nadawcy mogą chcieć podzielić Archiwum na żywo na uruchomienie programu logicznego i Zatrzymaj sekwencje. Następnie należy opublikować te programy wirtualne oddzielnie bez przetwarzania na żywo Archiwum i nie utworzyć oddzielnych zasobów (co nie przynosi korzyści dla istniejących fragmentów buforowanych w sieci CDN). Przykładami takich programów wirtualnych są kwartały gry piłkarskiej lub Basketball, innings w siatkówki lub pojedyncze zdarzenia dowolnego programu sportowego.

Za pomocą manifestu dynamicznego można utworzyć filtry przy użyciu godzin rozpoczęcia/zakończenia i utworzyć widoki wirtualne w górnej części archiwum na żywo. 

![Filtr podklipu][subclip_filter]

Filtrowany element zawartości:

![Narciarstwo][skiing]

## <a name="adjusting-presentation-window-dvr"></a>Dostosowywanie okna prezentacji (DVR)
Obecnie Azure Media Services oferuje archiwum cykliczne, w którym można skonfigurować czas trwania od 5 minut do 25 godzin. Filtrowanie manifestu może służyć do tworzenia okna wycofywania DVR w górnej części archiwum, bez usuwania nośnika. Istnieje wiele scenariuszy, w których nadawcy chcą mieć ograniczone okno DVR, aby można je było przenieść razem z aktywną krawędzią i w tym samym czasie pozostawić większe okno archiwizowania. Nadawca może chcieć użyć danych znajdujących się poza oknem DVR, aby wyróżnić klipy, lub może chcieć udostępnić różne okna DVR dla różnych urządzeń. Na przykład większość urządzeń przenośnych nie obsługuje dużych okien DVR (w przypadku urządzeń przenośnych można korzystać z 2-minutowego okna DVR) i godziny dla klientów stacjonarnych.

![Okno DVR][dvr_filter]

## <a name="adjusting-livebackoff-live-position"></a>Dostosowywanie LiveBackoff (pozycja na żywo)
Filtrowanie manifestu może służyć do usuwania kilku sekund od aktywnej krawędzi programu na żywo. Filtrowanie pozwala nadawcom oglądać prezentację w punkcie publikacji w wersji zapoznawczej i tworzyć punkty wstawiania anonsów przed odebraniem strumienia (z przystąpieniem do 30 sekund). Nadawcy mogą następnie wypchnąć te anonse do swoich platform klienta w czasie, aby zostały odebrane i przetworzyć informacje przed możliwością anonsowania.

Oprócz obsługi anonsów ustawienie LiveBackoff może służyć do dopasowywania pozycji osób przeglądających, dzięki czemu klienci będą mogli odpadać i trafiać fragmenty na żywo, a mimo to uzyskać błąd HTTP 404 lub 412.

![livebackoff_filter][livebackoff_filter]

## <a name="combining-multiple-rules-in-a-single-filter"></a>Łączenie wielu reguł w jednym filtrze
W jednym filtrze można połączyć wiele reguł filtrowania. Przykładowo można zdefiniować "regułę zakresu", aby usunąć z archiwum na żywo, a także odfiltrować dostępne szybkości transmisji bitów. W przypadku stosowania wielu reguł filtrowania wynik końcowy jest częścią wspólną wszystkich reguł.

![wiele reguł][multiple-rules]

## <a name="create-filters-programmatically"></a>Programistyczne tworzenie filtrów
W poniższym artykule omówiono Media Services jednostek, które są powiązane z filtrami. W tym artykule przedstawiono również sposób programowanego tworzenia filtrów.  

[Tworzenie filtrów za pomocą interfejsów API REST](media-services-rest-dynamic-manifest.md).

## <a name="combining-multiple-filters-filter-composition"></a>Łączenie wielu filtrów (kompozycja filtrów)
Można również połączyć wiele filtrów w jednym adresie URL. 

W poniższym scenariuszu przedstawiono, dlaczego warto połączyć filtry:

1. Należy odfiltrować jakość wideo dla urządzeń przenośnych, takich jak Android lub iPAD (w celu ograniczenia jakości wideo). Aby usunąć niepożądane jakości, należy utworzyć filtr globalny odpowiedni dla profilów urządzeń. Jak wspomniano wcześniej w tym artykule, filtry globalne mogą być używane dla wszystkich zasobów w ramach tego samego konta usługi Media Services bez żadnych dalszych powiązań. 
2. Należy również przyciąć godzinę rozpoczęcia i zakończenia elementu zawartości. Aby to osiągnąć, należy utworzyć filtr lokalny i ustawić godzinę początkową/końcową. 
3. Chcesz połączyć oba te filtry (bez kombinacji, należy dodać filtrowanie jakości do filtru przycinania, co utrudnia użycie filtru).

Aby połączyć filtry, należy ustawić nazwy filtrów na adres URL manifestu/listy odtwarzania z rozdzielonymi średnikami. Załóżmy, że masz filtr o nazwie *MyMobileDevice* , który ma filtry o jakości i masz inną nazwę " *StartTime* ", aby ustawić określony czas rozpoczęcia. Można połączyć je w następujący sposób:

`http://teststreaming.streaming.mediaservices.windows.net/3d56a4d-b71d-489b-854f-1d67c0596966/64ff1f89-b430-43f8-87dd-56c87b7bd9e2.ism/Manifest(filter=MyMobileDevice;MyStartTime)`

Można połączyć maksymalnie trzy filtry. 

Aby uzyskać więcej informacji, zobacz [ten](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) blog.

## <a name="know-issues-and-limitations"></a>Znane problemy i ograniczenia
* Manifest dynamiczny działa w granicach grupę GOP (klatek kluczowych), dlatego przycinanie ma dokładność grupę GOP. 
* Możesz użyć tej samej nazwy filtru dla filtrów lokalnych i globalnych. Filtry lokalne mają wyższy priorytet i przesłonią filtry globalne.
* W przypadku aktualizacji filtru może upłynąć do 2 minut, zanim punkt końcowy przesyłania strumieniowego odświeża reguły. Jeśli zawartość została obsłużona przy użyciu niektórych filtrów (w przypadku serwerów proxy i pamięci podręcznej usługi CDN), aktualizacja tych filtrów może skutkować awariami odtwarzacza. Zaleca się wyczyszczenie pamięci podręcznej po zaktualizowaniu filtru. Jeśli ta opcja nie jest możliwa, należy rozważyć użycie innego filtru.

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Wyraź opinię
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Zobacz też
[Omówienie dostarczania zawartości do klientów](media-services-deliver-content-overview.md)

[renditions1]: ./media/media-services-dynamic-manifest-overview/media-services-rendition-filter.png
[renditions2]: ./media/media-services-dynamic-manifest-overview/media-services-rendition-filter2.png

[rendered_subclip]: ./media/media-services-dynamic-manifests/media-services-rendered-subclip.png
[timeline_trim_event]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-event.png
[timeline_trim_subclip]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-subclip.png

[multiple-rules]:./media/media-services-dynamic-manifest-overview/media-services-multiple-rules-filters.png

[subclip_filter]: ./media/media-services-dynamic-manifest-overview/media-services-subclips-filter.png
[trim_event]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-event.png
[trim_subclip]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-subclip.png
[trim_filter]: ./media/media-services-dynamic-manifest-overview/media-services-trim-filter.png
[redered_subclip]: ./media/media-services-dynamic-manifests/media-services-rendered-subclip.png
[livebackoff_filter]: ./media/media-services-dynamic-manifest-overview/media-services-livebackoff-filter.png
[language_filter]: ./media/media-services-dynamic-manifest-overview/media-services-language-filter.png
[dvr_filter]: ./media/media-services-dynamic-manifest-overview/media-services-dvr-filter.png
[skiing]: ./media/media-services-dynamic-manifest-overview/media-services-skiing.png
