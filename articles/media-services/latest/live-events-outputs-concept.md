---
title: Wydarzenia na żywo i pojęcia dotyczące danych wyjściowych na żywo
description: Ten temat zawiera omówienie zdarzeń na żywo i danych wyjściowych na żywo w Azure Media Services wersji 3.
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
ms.date: 10/23/2020
ms.author: inhenkel
ms.openlocfilehash: a66532856263d31e9070bc99f297ae105ca48312
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102454791"
---
# <a name="live-events-and-live-outputs-in-media-services"></a>Zdarzenia na żywo i wyjście na żywo w Media Services

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services pozwala na dostarczanie na żywo wydarzeń klientom w chmurze platformy Azure. Aby skonfigurować Twoje zdarzenia przesyłania strumieniowego na żywo w Media Services v3, należy zrozumieć koncepcje omówione w tym artykule.

> [!TIP]
> W przypadku klientów przeprowadzających migrację z interfejsów API Media Services V2, jednostka **zdarzenia na żywo** zastępuje **kanał** w wersji 2, a **dane wyjściowe na żywo** zastępują **program**.

## <a name="live-events"></a>Wydarzenia na żywo

[Zdarzenia na żywo](/rest/api/media/liveevents) są odpowiedzialne za pozyskiwanie i przetwarzanie kanałów wideo na żywo. Podczas tworzenia zdarzenia na żywo tworzony jest podstawowy i pomocniczy punkt końcowy wejścia, którego można użyć do wysłania sygnału na żywo ze zdalnego kodera. Zdalny koder na żywo wysyła strumieniowe źródło danych do tego wejściowego punktu końcowego przy użyciu protokołu [RTMP](https://www.adobe.com/devnet/rtmp.html) lub [Smooth Streaming](/openspecs/windows_protocols/ms-sstr/8383f27f-7efe-4c60-832a-387274457251) (fragmentacja-MP4). W przypadku protokołu pozyskiwania RTMP zawartość może być wysyłana w postaci zwykłej ( `rtmp://` ) lub bezpiecznie zaszyfrowanej w sieci ( `rtmps://` ). W przypadku protokołu pozyskiwania Smooth Streaming obsługiwane schematy adresów URL to `http://` lub `https://` .  

## <a name="live-event-types"></a>Typy zdarzeń na żywo

[Wydarzenie na żywo](/rest/api/media/liveevents) można ustawić na *przekazywanie* (lokalny koder na żywo wysyła strumień o wielokrotnej szybkości transmisji bitów) lub *kodowanie na żywo* (lokalny koder na żywo wysyła strumień o pojedynczej szybkości transmisji bitów). Typy są ustawiane podczas tworzenia przy użyciu [LiveEventEncodingType](/rest/api/media/liveevents/create#liveeventencodingtype):

* **LiveEventEncodingType. None**: lokalny koder na żywo wysyła strumień o wielokrotnej szybkości transmisji bitów. Pozyskiwany strumień przeszedł przez wydarzenie na żywo bez dalszej obróbki. Nazywana również trybem przekazywania.
* **LiveEventEncodingType. Standard**: lokalny koder na żywo wysyła strumień o pojedynczej szybkości transmisji bitów do zdarzenia na żywo, a Media Services tworzy wiele strumieni szybkości transmisji bitów. Jeśli kanał informacyjny udziału ma rozdzielczość 720 lub wyższą, ustawienie wstępne **Default720p** będzie kodować zestaw par rozdzielczości/szybkości transmisji bitów.
* **LiveEventEncodingType. Premium1080p**: lokalny koder na żywo wysyła strumień o pojedynczej szybkości transmisji bitów do zdarzenia na żywo, a Media Services tworzy wiele strumieni szybkości transmisji bitów. Ustawienie wstępne Default1080p określa wyjściowy zestaw par rozdzielczości/szybkości transmisji bitów.

### <a name="pass-through"></a>Przekazywanie

![zdarzenie przekazywania na żywo z przykładowym diagramem Media Services](./media/live-streaming/pass-through.svg)

Korzystając ze zdarzenia Pass-through **Live**, można polegać na lokalnym koderie na żywo w celu wygenerowania strumienia wideo o wielu szybkościach transmisji bitów i wysłania go jako źródła strumieniowego do zdarzenia na żywo (przy użyciu protokołu RTMP lub fragmentacji MP4). Zdarzenie na żywo odbywa się następnie przez przychodzące strumienie wideo bez dalszej obróbki. Takie zdarzenie przekazywania na żywo jest zoptymalizowane pod kątem długotrwałych wydarzeń na żywo lub 24x365 liniowe przesyłanie strumieniowe na żywo. Podczas tworzenia tego typu zdarzenia na żywo należy określić brak (LiveEventEncodingType. None).

Możesz wysyłać kanał informacyjny o rozdzielczości maksymalnie 4K oraz z szybkością 60 klatek na sekundę, za pomocą koderów-dekoderów wideo H.264/AVC lub H.265/HEVC i kodera-dekodera audio AAC (AAC-LC, HE-AACv1 lub HE-AACv2). Aby uzyskać więcej informacji, zobacz [Porównanie typów zdarzeń na żywo](live-event-types-comparison.md).

> [!NOTE]
> Użycie metody przekazującej to najbardziej ekonomiczny sposób wykonywania transmisji strumieniowej na żywo, gdy wykonujesz wiele zdarzeń w długim czasie, i już zainwestowano w kodery lokalne. Zobacz szczegóły [cennika](https://azure.microsoft.com/pricing/details/media-services/) .
>

Zobacz przykład kodu platformy .NET służący do tworzenia zdarzenia Pass-through Live w [zdarzeniu na żywo przy użyciu DVR](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/4a436376e77bad57d6cbfdc02d7df6c615334574/Live/LiveEventWithDVR/Program.cs#L214).

### <a name="live-encoding"></a>Kodowanie na żywo  

![kodowanie na żywo z przykładowym diagramem Media Services](./media/live-streaming/live-encoding.svg)

W przypadku korzystania z funkcji kodowania na żywo z Media Services należy skonfigurować lokalny koder na żywo, aby wysyłał wideo o pojedynczej szybkości transmisji bitów jako źródło źródła danych do zdarzenia na żywo (przy użyciu protokołu RTMP lub Fragmented-Mp4). Następnie skonfigurujesz wydarzenie na żywo, aby zakodować strumień przychodzącej pojedynczej szybkości transmisji bitów do [strumienia wideo o wielu szybkościach transmisji bitów](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming), a następnie dane wyjściowe dostępne do dostarczenia w celu odtwarzania urządzeń za pomocą protokołów takich jak MPEG-kreska, HLS i Smooth Streaming.

W przypadku korzystania z kodowania na żywo można wysłać kanał informacyjny dotyczący tylko rozdzielczości o rozdzielczości do 1080p (30 klatek na sekundę) przy użyciu kodera-dekoder wideo H. 264/AVC i AAC (AAC-LC, AACv1 lub AACv2). Należy pamiętać, że zdarzenia przekazywane na żywo mogą obsługiwać rozdzielczość do 4K w 60 klatkach na sekundę. Aby uzyskać więcej informacji, zobacz [Porównanie typów zdarzeń na żywo](live-event-types-comparison.md).

Rozdzielczości i szybkości transmisji bitów zawarte w danych wyjściowych z kodera na żywo są określane przez ustawienie wstępne. Jeśli używany jest **Standardowy** koder na żywo (LiveEventEncodingType. Standard), a następnie ustawienie wstępne *Default720p* określa zestaw sześciu par szybkości rozpoznawania/bitu, przechodząc od 720 o 3,5 MB/s do 192p o 200 KB/s. W przeciwnym razie, jeśli używany jest **Premium1080p** Live Encoder (LiveEventEncodingType. Premium1080p), a następnie ustawienie wstępne *Default1080p* określa zestaw sześciu par rozdzielczości/szybkości transmisji bitów, przechodzących z 1080p o 3,5 MB/s do 180p o 200 KB/s. Aby uzyskać informacje, zobacz [Ustawienia wstępne systemu](live-event-types-comparison.md#system-presets).

> [!NOTE]
> Jeśli musisz dostosować ustawienie wstępne kodowania na żywo, Otwórz bilet pomocy technicznej za pośrednictwem Azure Portal. Określ żądaną tabelę rozdzielczości i szybkości transmisji bitów. Sprawdź, czy istnieje tylko jedna warstwa o godzinie 720 (jeśli żądasz ustawienia wstępnego dla standardowego kodera na żywo) lub w lokalizacji 1080p (jeśli żądasz ustawienia wstępnego dla kodera Premium1080p Live) i 6 warstw.

## <a name="creating-live-events"></a>Tworzenie zdarzeń na żywo

### <a name="options"></a>Opcje

Podczas tworzenia zdarzenia na żywo można określić następujące opcje:

* Możesz podać nazwę i opis zdarzenia na żywo.
* Kodowanie w chmurze obejmuje przekazywanie (bez kodowania w chmurze), Standard (do 720) lub Premium (do 1080p). W przypadku kodowania Standard i Premium można wybrać tryb rozciągnięcia kodowanego wideo.
  * Brak: ściśle szanuje rozdzielczość wyjściową określoną w ustawieniu ustawienie wstępne kodowania, bez uwzględnienia współczynnika proporcji pikseli lub współczynnika proporcji wejściowego wideo.
  * Autodopasowanie rozmiaru: zastępuje rozdzielczość wyjściową i zmienia ją tak, aby pasowała do współczynnika proporcji danych wejściowych, bez dopełnienia. Na przykład, jeśli dane wejściowe to 1920 x 1080, a ustawienie wstępne kodowania prosi o 1280x1280, wartość w ustawieniu wstępnym jest zastępowana, a dane wyjściowe będą znajdować się w 1280x720, co zachowuje współczynnik proporcji danych 16:9.
  * Funkcja autodopasowania: program konsole dane wyjściowe (z polem letterbox lub filarem) w celu zagwarantowania rozdzielczości wyjściowej, przy jednoczesnym zapewnieniu, że aktywny region wideo w danych wyjściowych ma ten sam współczynnik proporcji co dane wejściowe. Na przykład, jeśli dane wejściowe to 1920 x 1080, a dla ustawienia wstępnego kodowania zostanie wyświetlony monit o podanie wartości 1280x1280, dane wyjściowe będą znajdować się na 1280x1280, który zawiera wewnętrzny prostokąt 1280x720 przy współczynniku proporcji 16:9, z obszarami filarów 280 pikseli o szerokości od lewej i prawej.
* Protokół przesyłania strumieniowego (obecnie obsługiwane są protokoły RTMP i Smooth Streaming). Nie można zmienić opcji protokołu, gdy działa zdarzenie na żywo lub skojarzone z nim wyjście na żywo. Jeśli potrzebujesz różnych protokołów, Utwórz oddzielne zdarzenie na żywo dla każdego protokołu przesyłania strumieniowego.
* Identyfikator wejściowy, który jest unikatowym identyfikatorem globalnym dla strumienia danych wejściowych zdarzenia na żywo.
* Statyczny prefiks nazwy hosta, który zawiera brak (w którym przypadku jest używany losowy ciąg szesnastkowy 128), użyj nazwy zdarzenia na żywo lub użyj nazwy niestandardowej.  W przypadku wybrania opcji używania nazwy klienta ta wartość jest prefiksem nazwy hosta niestandardowego.
* Można zmniejszyć czas oczekiwania między emisją na żywo i odtwarzaniem przez ustawienie interwału klatek wejścia, który jest czasem trwania (w sekundach) każdego segmentu nośnika w danych wyjściowych HLS. Wartość powinna być liczbą całkowitą różną od zera z zakresu od 0,5 do 20 sekund.  Wartość domyślna to 2 sekundy, jeśli *nie* są ustawione żadne interwały wejściowe lub wyjściowe ramki klucza. Interwał klatek kluczowych jest dozwolony tylko w przypadku zdarzeń przekazujących.
* Podczas tworzenia zdarzenia można ustawić Autouruchamianie. Gdy Autostart ma wartość true, zdarzenie na żywo zostanie uruchomione po utworzeniu. Rozliczanie zaczyna się zaraz po rozpoczęciu uruchamiania zdarzenia na żywo. Należy jawnie wywołać zatrzymywanie zasobu zdarzenia na żywo, aby zatrzymać dalsze rozliczanie. Możesz też uruchomić zdarzenie, gdy wszystko jest gotowe do rozpoczęcia przesyłania strumieniowego.

> [!NOTE]
> Maksymalna szybkość klatek to 30 fps dla kodowania Standard i Premium.

## <a name="standby-mode"></a>Tryb wstrzymania

Podczas tworzenia zdarzenia na żywo można ustawić tryb gotowości. Gdy zdarzenie jest w trybie wstrzymania, można edytować opis, prefiks statycznej nazwy hosta i ograniczyć ustawienia dostępu do danych wejściowych i podglądu.  Tryb wstrzymania jest nadal w trybie rozliczeniowym, ale jest naliczany inaczej niż podczas uruchamiania strumienia na żywo.

Aby uzyskać więcej informacji, zobacz [Stany wydarzeń na żywo i rozliczenia](live-event-states-billing.md).

* Ograniczenia dotyczące adresów IP w pozyskiwaniu i podglądzie. Można zdefiniować adresy IP, które mogą pozyskiwanie wideo dla tego zdarzenia na żywo. Jako dozwolone adresy IP można podać pojedynczy adres IP (na przykład „10.0.0.1”), zakres adresów IP przy użyciu adresu IP i maski podsieci CIDR (na przykład „10.0.0.1/22”) lub zakres adresów IP przy użyciu adresu IP i maski podsieci w notacji z kropką dziesiętną (na przykład, „10.0.0.1(255.255.252.0)”).
<br/><br/>
Jeśli nie określono adresów IP i nie ma definicji reguły, adres IP nie będzie dozwolony. Aby zezwolić na jakikolwiek adres IP, utwórz regułę i ustaw wartość 0.0.0.0/0.<br/>Adresy IP muszą znajdować się w jednym z następujących formatów: adres IpV4 z czterema numerami lub zakresem adresów CIDR.
<br/><br/>
Jeśli chcesz włączyć niektóre adresy IP na własnych zaporach lub chcesz ograniczyć dane wejściowe do zdarzeń na żywo do adresów IP platformy Azure, Pobierz plik JSON z [zakresów adresów IP centrum danych platformy Azure](https://www.microsoft.com/download/details.aspx?id=41653). Aby uzyskać szczegółowe informacje na temat tego pliku, wybierz sekcję **szczegóły** na stronie.

* Podczas tworzenia zdarzenia można włączyć transkrypcje na żywo. Domyślnie transkrypcja dynamiczna jest wyłączona. Aby uzyskać więcej informacji na temat transkrypcji na żywo, Przeczytaj [dynamiczną transkrypcję](live-transcription.md).

### <a name="naming-rules"></a>Reguły nazewnictwa

* Maksymalna Nazwa zdarzenia na żywo to 32 znaków.
* Nazwa powinna być zgodna z tym wzorcem [wyrażenia regularnego](/dotnet/standard/base-types/regular-expression-language-quick-reference) : `^[a-zA-Z0-9]+(-*[a-zA-Z0-9])*$` .

Zobacz również [konwencje nazewnictwa punktów końcowych przesyłania strumieniowego](streaming-endpoint-concept.md#naming-convention).

> [!TIP]
> Aby zagwarantować unikatowość nazwy zdarzenia na żywo, można wygenerować identyfikator GUID, a następnie usunąć wszystkie łączniki i nawiasy klamrowe (jeśli istnieją). Ciąg będzie unikatowy dla wszystkich wydarzeń na żywo, a jego długość jest gwarantowana na 32.

## <a name="live-event-ingest-urls"></a>Adresy URL pozyskiwania zdarzeń na żywo

Po utworzeniu zdarzenia na żywo możesz uzyskać adresy URL pozyskiwania na żywo w usłudze Live on-premises Encoder. Koder na żywo używa tych adresów URL do wprowadzenia strumienia na żywo. Aby uzyskać więcej informacji, zobacz [zalecane lokalne kodery na żywo](recommended-on-premises-live-encoders.md).

>[!NOTE]
> W przypadku wersji interfejsu API 2020-05-01 znaczącym adresy URL są nazywane statycznymi nazwami hostów

Możesz użyć znaczących lub nieznaczących adresów URL.

> [!NOTE]
> Aby adres URL pozyskiwania był predykcyjny, ustaw tryb "znaczącym".

* Adres URL inny niż znaczącym

    Znaczącym adres URL jest trybem domyślnym w Media Services v3. Możesz szybko uzyskać wydarzenie na żywo, ale adres URL pozyskiwania jest znany tylko po rozpoczęciu zdarzenia na żywo. Adres URL zmieni się, jeśli zatrzymasz/uruchomisz wydarzenie na żywo. Znaczącym jest przydatne w scenariuszach, gdy użytkownik końcowy chce przesyłać strumieniowo przy użyciu aplikacji, w której aplikacja chce uzyskać na żywo zdarzenie JNW i ma dynamiczny adres URL pozyskiwania nie jest problemem.

    Jeśli aplikacja kliencka nie musi wstępnie generować adresu URL pozyskiwania przed utworzeniem zdarzenia na żywo, zezwól Media Services automatycznie generować token dostępu dla zdarzenia na żywo.

* Adres URL znaczącym

    Tryb znaczącym jest preferowany przez duże nadawcy multimediów, którzy korzystają z koderów emisji sprzętowej i nie chcą ponownie konfigurować swoich koderów po rozpoczęciu wydarzenia na żywo. Ci nadawcy chcą uzyskać adres URL pozyskiwania predykcyjnego, który nie zmienia się w czasie.

    > [!NOTE]
    > W Azure Portal adres URL znaczącym ma nazwę "*statyczny prefiks nazwy hosta*".

    Aby określić ten tryb w interfejsie API, ustaw opcję na `useStaticHostName` `true` godzina utworzenia (domyślnie `false` ). Gdy `useStaticHostname` ma wartość true, `hostnamePrefix` Określa pierwszą część nazwy hosta przypisanej do podglądu zdarzenia na żywo i punkty końcowe pozyskiwania. Końcowa nazwa hosta będzie kombinacją tego prefiksu, nazwy konta usługi Media i krótkiego kodu dla Azure Media Services centrum danych.

    Aby uniknąć losowego tokenu w adresie URL, należy również przekazać własny token dostępu ( `LiveEventInput.accessToken` ) w czasie jego tworzenia.  Token dostępu musi być prawidłowym ciągiem identyfikatora GUID (z łącznikami lub bez nich). Po ustawieniu trybu nie można go zaktualizować.

    Token dostępu musi być unikatowy w centrum danych. Jeśli Twoja aplikacja musi używać adresu URL znaczącym, zaleca się, aby zawsze utworzyć nowe wystąpienie GUID dla tokenu dostępu (zamiast ponownie używać dowolnego istniejącego identyfikatora GUID).

    Użyj następujących interfejsów API, aby włączyć adres URL znaczącym i ustawić token dostępu na prawidłowy identyfikator GUID (na przykład `"accessToken": "1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"` ).  

    |Język|Włącz adres URL znaczącym|Określanie tokenu dostępu|
    |---|---|---|
    |REST|[Właściwości. vanityUrl](/rest/api/media/liveevents/create#liveevent)|[LiveEventInput. accessToken](/rest/api/media/liveevents/create#liveeventinput)|
    |Interfejs wiersza polecenia|[--znaczącym-URL](/cli/azure/ams/live-event#az-ams-live-event-create)|[--token dostępu](/cli/azure/ams/live-event#optional-parameters)|
    |.NET|[LiveEvent.VanityUrl](/dotnet/api/microsoft.azure.management.media.models.liveevent#Microsoft_Azure_Management_Media_Models_LiveEvent_VanityUrl)|[LiveEventInput. AccessToken](/dotnet/api/microsoft.azure.management.media.models.liveeventinput.accesstoken#Microsoft_Azure_Management_Media_Models_LiveEventInput_AccessToken)|

### <a name="live-ingest-url-naming-rules"></a>Reguły nazewnictwa adresów URL pozyskiwania na żywo

* *Losowy* ciąg poniżej to 128-bitowa liczba szesnastkowa (która składa się z 32 znaków 0–9 a–f).
* *token dostępu*: prawidłowy ciąg identyfikatora GUID ustawiany podczas korzystania z trybu znaczącym. Na przykład `"1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"`.
* *Nazwa strumienia*: wskazuje nazwę strumienia dla określonego połączenia. Wartość nazwy strumienia jest zwykle dodawana przez używany koder na żywo. Można skonfigurować koder na żywo, aby używał dowolnych nazw do opisywania połączenia, na przykład: "video1_audio1", "video2_audio1", "Stream".

#### <a name="non-vanity-url"></a>Adres URL inny niż znaczącym

##### <a name="rtmp"></a>RTMP

`rtmp://<random 128bit hex string>.channel.media.azure.net:1935/live/<auto-generated access token>/<stream name>`<br/>
`rtmp://<random 128bit hex string>.channel.media.azure.net:1936/live/<auto-generated access token>/<stream name>`<br/>
`rtmps://<random 128bit hex string>.channel.media.azure.net:2935/live/<auto-generated access token>/<stream name>`<br/>
`rtmps://<random 128bit hex string>.channel.media.azure.net:2936/live/<auto-generated access token>/<stream name>`<br/>

##### <a name="smooth-streaming"></a>Płynne przesyłanie strumieniowe

`http://<random 128bit hex string>.channel.media.azure.net/<auto-generated access token>/ingest.isml/streams(<stream name>)`<br/>
`https://<random 128bit hex string>.channel.media.azure.net/<auto-generated access token>/ingest.isml/streams(<stream name>)`<br/>

#### <a name="vanity-url"></a>Adres URL znaczącym

W następujących ścieżkach `<live-event-name>` oznacza nazwę nadaną dla zdarzenia lub nazwę niestandardową używaną podczas tworzenia zdarzenia na żywo.

##### <a name="rtmp"></a>RTMP

`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1935/live/<your access token>/<stream name>`<br/>
`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1936/live/<your access token>/<stream name>`<br/>
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2935/live/<your access token>/<stream name>`<br/>
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2936/live/<your access token>/<stream name>`<br/>

##### <a name="smooth-streaming"></a>Płynne przesyłanie strumieniowe

`http://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<your access token>/ingest.isml/streams(<stream name>)`<br/>
`https://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<your access token>/ingest.isml/streams(<stream name>)`<br/>

## <a name="live-event-preview-url"></a>Adres URL podglądu zdarzenia na żywo

Gdy wydarzenie na żywo zacznie otrzymywać strumieniowe źródło danych, można użyć jego punktu końcowego w wersji zapoznawczej, aby wyświetlić podgląd i sprawdzić, czy otrzymujesz strumień na żywo przed dalszej publikacji. Po sprawdzeniu, czy strumień wersji zapoznawczej jest dobry, możesz użyć zdarzenia na żywo, aby udostępnić strumień na żywo na potrzeby dostarczania przez jeden lub więcej (wstępnie utworzony) punktów końcowych przesyłania strumieniowego. Aby to osiągnąć, Utwórz nowe [dane wyjściowe](/rest/api/media/liveoutputs) na żywo dla zdarzenia na żywo.

> [!IMPORTANT]
> Przed kontynuowaniem upewnij się, że wideo przepływa do adresu URL wersji zapoznawczej.

## <a name="live-event-long-running-operations"></a>Operacje długotrwałe dla zdarzenia na żywo

Aby uzyskać szczegółowe informacje, zobacz [długotrwałe operacje](media-services-apis-overview.md#long-running-operations).

## <a name="live-outputs"></a>Wyjście na żywo

Gdy strumień przepływa do zdarzenia na żywo, możesz rozpocząć zdarzenie przesyłania strumieniowego, tworząc element [zawartości](/rest/api/media/assets), [dane wyjściowe](/rest/api/media/liveoutputs)i [lokalizator przesyłania strumieniowego](/rest/api/media/streaminglocators). na żywo wyjście będzie archiwizować strumień i udostępnić je osobom oglądającym za pomocą [punktu końcowego przesyłania strumieniowego](/rest/api/media/streamingendpoints).  

Aby uzyskać szczegółowe informacje na temat danych wyjściowych na żywo, zobacz [Korzystanie z funkcji DVR w chmurze](live-event-cloud-dvr.md).

## <a name="frequently-asked-questions"></a>Często zadawane pytania

Zobacz artykuł [często zadawane pytania](frequently-asked-questions.md#live-streaming) .

## <a name="ask-questions-and-get-updates"></a>Zadawaj pytania i otrzymuj aktualizacje

Zapoznaj się z artykułem [community Azure Media Services](media-services-community.md) , aby zobaczyć różne sposoby zadawania pytań, przekazać Opinie i uzyskać aktualizacje dotyczące Media Services.

## <a name="next-steps"></a>Następne kroki

[Samouczek przesyłania strumieniowego na żywo](stream-live-tutorial-with-api.md)
