---
title: Przesyłanie strumieniowe na żywo przy użyciu Azure Media Services do tworzenia strumieni o wysokiej szybkości transmisji bitów | Microsoft Docs
description: W tym temacie opisano sposób konfigurowania kanału, który odbiera strumień na żywo o pojedynczej szybkości transmisji bitów z kodera lokalnego, a następnie wykonuje kodowanie na żywo w strumieniu adaptacyjnej szybkości transmisji bitów przy użyciu Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 30ce6556-b0ff-46d8-a15d-5f10e4c360e2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: anilmur
ms.reviewer: juliako
ms.openlocfilehash: b9b4cd54375a13da95259e27da680255f785df45
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103013214"
---
# <a name="live-streaming-using-azure-media-services-to-create-multi-bitrate-streams"></a>Transmisja strumieniowa na żywo korzystająca z usługi Azure Media Services do tworzenia strumieni o różnej szybkości transmisji bitów

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Od 12 maja 2018 kanały na żywo nie będą już obsługiwać protokołu pozyskiwania strumienia transportowego RTP/MPEG-2. Przeprowadź migrację z protokołów RTP/MPEG-2 do protokołu RTMP lub pofragmentowanych plików MP4 (Smooth Streaming).

## <a name="overview"></a>Omówienie
W Azure Media Services (AMS) **kanał** reprezentuje potok służący do przetwarzania zawartości przesyłania strumieniowego na żywo. **Kanał** odbiera strumienie wejściowe na żywo na jeden z dwóch sposobów:

* Lokalny koder na żywo wysyła strumień o pojedynczej szybkości transmisji bitów do kanału, w którym włączono obsługę kodowania na żywo za pomocą Media Services w jednym z następujących formatów: RTMP lub Smooth Streaming (fragmentacja MP4). Kanał wykonuje następnie kodowanie na żywo przychodzącego strumienia o pojedynczej szybkości transmisji bitów do postaci strumienia wideo o różnych szybkościach transmisji bitów (adaptacyjnej szybkości transmisji bitów). Po odebraniu żądania usługa Media Services dostarcza strumień do klientów.
* Lokalny koder na żywo wysyła protokół **RTMP** o dużej szybkości transmisji bitów lub **Smooth Streaming** (pofragmentowany plik MP4) do kanału, w którym nie włączono kodowania na żywo z użyciem usługi AMS. Pozyskiwane strumienie są przekazywane przez **kanał** bez żadnego dalszego przetwarzania. Ta metoda jest nazywana **przekazywaniem**. Można użyć następujących koderów na żywo, które wychodzące z wieloszybkościowej transmisji bitów Smooth Streaming: MediaExcel, ATEME, Wyobraź Communications, Envivio, Cisco i element. Następujące kodery dynamiczne są wyprowadzane przez RTMP: [Telestream Wirecast](media-services-configure-wirecast-live-encoder.md), Haivision, kodery Teradek.  Koder na żywo może także wysłać strumień o pojedynczej szybkości transmisji bitów do kanału, który nie obsługuje kodowania na żywo, nie jest to jednak zalecane. Po odebraniu żądania usługa Media Services dostarcza strumień do klientów.

  > [!NOTE]
  > Użycie metody przekazującej to najbardziej ekonomiczny sposób na przesyłanie strumieniowe na żywo.
  > 
  > 

Począwszy od wersji 2,10 Media Services, podczas tworzenia kanału można określić, w jaki sposób chcesz, aby kanał odbierał strumień wejściowy i czy chcesz, aby kanał wykonywał kodowanie na żywo strumienia. Dostępne są dwie opcje:

* **Brak** — Określ tę wartość, jeśli planujesz używać lokalnego kodera na żywo, który będzie wyprowadzać strumień o szybkości transmisji bitów (strumień Pass-through). W tym przypadku strumień przychodzący przeszedł do danych wyjściowych bez kodowania. Jest to zachowanie kanału przed wydaniem 2,10.  Aby uzyskać bardziej szczegółowe informacje na temat pracy z kanałami tego typu, zobacz [przesyłanie strumieniowe na żywo z użyciem koderów lokalnych, które tworzą strumienie o wielu szybkościach transmisji bitów](media-services-live-streaming-with-onprem-encoders.md).
* **Standardowa** — wybierz tę wartość, jeśli planujesz używać Media Services do kodowania strumienia na żywo o pojedynczej szybkości transmisji bitów do strumienia o większej szybkości transmisji bitów. Należy pamiętać, że istnieje wpływ rozliczania na kodowanie na żywo i należy pamiętać, że pozostawienie kanału kodowania na żywo w stanie "uruchomiona" spowoduje naliczenie opłat naliczanych.  Zaleca się natychmiastowe zatrzymanie uruchomionych kanałów po zakończeniu zdarzenia przesyłania strumieniowego na żywo, aby uniknąć dodatkowych opłat godzinowych.

> [!NOTE]
> W tym temacie omówiono atrybuty kanałów włączonych do wykonywania kodowania na żywo (**standardowego** typu kodowania). Aby uzyskać informacje na temat pracy z kanałami, w których nie włączono obsługi kodowania na żywo, zobacz [przesyłanie strumieniowe na żywo z użyciem koderów lokalnych, które tworzą strumienie o wysokiej szybkości transmisji bitów](media-services-live-streaming-with-onprem-encoders.md).
> 
> Zapoznaj się z sekcją [zagadnienia](media-services-manage-live-encoder-enabled-channels.md#Considerations) .
> 
> 

## <a name="billing-implications"></a>Implikacje rozliczeń
Kanał kodowania na żywo rozpoczyna rozliczanie od razu po zmianie stanu na "uruchomiony" za pośrednictwem interfejsu API.   Możesz również wyświetlić stan w Azure Portal lub w narzędziu Eksplorator Azure Media Services ( https://aka.ms/amse) .

W poniższej tabeli przedstawiono sposób, w jaki Stany kanałów mapują się na Stany rozliczeń w interfejsie API i Azure Portal. Stany różnią się nieco między interfejsem użytkownika interfejsu API i portalu. Gdy tylko kanał jest w stanie "uruchomiony" za pośrednictwem interfejsu API lub w stanie "gotowe" lub "streaming" w Azure Portal, rozliczenia będą aktywne.
Aby zatrzymać rozliczanie kanału od Ciebie, należy zatrzymać kanał za pośrednictwem interfejsu API lub Azure Portal.
Użytkownik jest odpowiedzialny za zatrzymywanie kanałów po zakończeniu pracy z kanałem kodowania na żywo.  Niepowodzenie zatrzymania kanału kodowania spowoduje dalsze rozliczanie.

### <a name="channel-states-and-how-they-map-to-the-billing-mode"></a><a id="states"></a>Stany kanału i sposób ich mapowania do trybu rozliczania
Bieżący stan kanału. Możliwe wartości to:

* **Zatrzymano**. Jest to początkowy stan kanału po jego utworzeniu (o ile Autostart nie został wybrany w portalu). W tym stanie nie ma rozliczeń. W tym stanie właściwości kanału mogą być aktualizowane, ale przesyłanie strumieniowe jest niedozwolone.
* **Rozpoczęcie**. Kanał jest uruchamiany. W tym stanie nie ma rozliczeń. W tym stanie nie są dozwolone żadne aktualizacje ani przesyłanie strumieniowe. Jeśli wystąpi błąd, kanał powróci do stanu zatrzymany.
* **Uruchomione**. Kanał może przetwarzać strumienie na żywo. Jest teraz użycie rozliczeń. Należy zatrzymać kanał, aby zapobiec dalszemu rozliczeniu. 
* **Zatrzymywanie**. Kanał jest zatrzymywany. W tym stanie przejściowym nie ma rozliczeń. W tym stanie nie są dozwolone żadne aktualizacje ani przesyłanie strumieniowe.
* **Usuwanie**. Kanał jest usuwany. W tym stanie przejściowym nie ma rozliczeń. W tym stanie nie są dozwolone żadne aktualizacje ani przesyłanie strumieniowe.

W poniższej tabeli przedstawiono sposób, w jaki Stany kanałów mapują się do trybu rozliczania. 

| Stan kanału | Wskaźniki interfejsu użytkownika portalu | Czy jest to rozliczenia? |
| --- | --- | --- |
| Uruchamianie |Uruchamianie |Nie (stan przejściowy) |
| Uruchomienie |Gotowe (brak uruchomionych programów)<br/>lub<br/>Przesyłanie strumieniowe (co najmniej jeden uruchomiony program) |TAK |
| Zatrzymywanie |Zatrzymywanie |Nie (stan przejściowy) |
| Zatrzymano |Zatrzymano |Nie |

### <a name="automatic-shut-off-for-unused-channels"></a>Automatyczne wyłączenie dla nieużywanych kanałów
Począwszy od 25 stycznia 2016, Media Services wycofał aktualizację, która automatycznie przerywa kanał (z włączonym kodowaniem na żywo) po uruchomieniu w nieużywanym stanie przez długi czas. Dotyczy to kanałów, które nie mają aktywnych programów, a które nie otrzymały źródła danych wejściowych przez dłuższy czas.

Próg nieużywanego okresu wynosi nominalnie 12 godzin, ale może ulec zmianie.

## <a name="live-encoding-workflow"></a>Przepływ pracy Live Encoding
Poniższy diagram przedstawia przepływ pracy przesyłania strumieniowego na żywo, gdzie kanał odbiera strumień o pojedynczej szybkości transmisji bitów w jednym z następujących protokołów: RTMP lub Smooth Streaming; następnie koduje strumień do strumienia o większej szybkości transmisji bitów. 

![Przepływ pracy na żywo][live-overview]

## <a name="common-live-streaming-scenario"></a><a id="scenario"></a>Typowy scenariusz transmisji strumieniowej na żywo
Poniżej przedstawiono ogólne etapy tworzenia typowych aplikacji transmisji strumieniowej na żywo.

> [!NOTE]
> Obecnie maksymalny zalecany czas trwania wydarzenia na żywo wynosi 8 godzin.
>
> Istnieje wpływ rozliczania na kodowanie na żywo i należy pamiętać, że pozostawienie kanału kodowania na żywo w stanie "uruchomiona" spowoduje naliczenie opłat za godzinę. Zaleca się natychmiastowe zatrzymanie uruchomionych kanałów po zakończeniu zdarzenia przesyłania strumieniowego na żywo, aby uniknąć dodatkowych opłat godzinowych. 

1. Podłącz kamerę wideo do komputera. Uruchom i skonfiguruj lokalny koder na żywo, który może wyprowadzać strumień o **pojedynczej** szybkości transmisji bitów w jednym z następujących protokołów: RTMP lub Smooth Streaming. 

    Ten krok można również wykonać po utworzeniu kanału.
2. Utwórz i uruchom kanał. 
3. Pobierz adres URL pozyskiwania kanału. 

    Koder na żywo używa adresu URL pozyskiwania do wysyłania strumienia do kanału.
4. Pobierz adres URL podglądu kanału. 

    Użyj tego adresu URL, aby sprawdzić, czy kanał prawidłowo odbiera strumień na żywo.
5. Utwórz program. 

    W przypadku korzystania z Azure Portal Tworzenie programu powoduje także utworzenie elementu zawartości. 

    W przypadku korzystania z zestawu SDK platformy .NET lub REST należy utworzyć zasób i określić, aby użyć tego elementu zawartości podczas tworzenia programu. 
6. Opublikuj element zawartości skojarzony z programem.   

    >[!NOTE]
    >Po utworzeniu konta usługi AMS zostanie do niego dodany **domyślny** punkt końcowy przesyłania strumieniowego mający stan **Zatrzymany**. Punkt końcowy przesyłania strumieniowego, z którego chcesz strumieniowo przesyłać zawartość, musi mieć stan **Uruchomiony**. 

7. Uruchom program, gdy wszystko będzie gotowe do rozpoczęcia przesyłania strumieniowego i archiwizacji.
8. Opcjonalnie można przesłać do kodera na żywo sygnał o rozpoczęciu reklamy. Reklama jest wstawiana do strumienia wyjściowego.
9. Zatrzymaj program w dowolnym momencie, w którym chcesz zatrzymać przesyłanie strumieniowe i archiwizowanie wydarzenia.
10. Usuń program (opcjonalnie można również usunąć zasób).   

> [!NOTE]
> Nie należy zapominać o zatrzymaniu Live Encoding kanału. Należy pamiętać, że istnieje co godzinę dla kodowania na żywo i należy pamiętać, że pozostawienie kanału kodowania na żywo w stanie "uruchomiona" spowoduje naliczenie opłat za rozliczenia.  Zaleca się natychmiastowe zatrzymanie uruchomionych kanałów po zakończeniu zdarzenia przesyłania strumieniowego na żywo, aby uniknąć dodatkowych opłat godzinowych. 
> 
> 

## <a name="channels-input-ingest-configurations"></a><a id="channel"></a>Konfiguracje wejścia/wyjścia kanału
### <a name="ingest-streaming-protocol"></a><a id="Ingest_Protocols"></a>Pozyskiwanie protokołu przesyłania strumieniowego
Jeśli ustawiono **Typ kodera** **Standardowy**, prawidłowe są następujące opcje:

* Pojedyncze szybkości **transmisji** bitów
* **Fragmentacja** pojedynczej szybkości transmisji bitów (Smooth Streaming)

#### <a name="single-bitrate-rtmp"></a><a id="single_bitrate_RTMP"></a>Pojedyncza szybkość transmisji bitów w formacie RTMP
Zagadnienia do rozważenia:

* Strumień przychodzący nie może zawierać wideo o wysokiej szybkości transmisji bitów
* Strumień wideo powinien mieć średnią szybkość transmisji bitów poniżej 15 MB/s
* Strumień audio powinien mieć średnią szybkość transmisji bitów poniżej 1 MB/s
* Obsługiwane są następujące kodery-dekoder:
* Wideo MPEG-4 AVC/H 264
* Linia bazowa, główny, wysoki profil (8-bitowy 4:2:0)
* Profil o wysokiej 10 (10-bitowy 4:2:0)
* Profil o wysokiej 422 (10-bitowy 4:2:2)
* Dźwięk MPEG-2 AAC-LC
* Mono, stereo, przestrzenny (5,1, 7,1)
* częstotliwość próbkowania 44,1 kHz
* Pakowanie ADTS w stylu MPEG-2
* Zalecane kodery obejmują:
* [Wirecast telestrumienia](media-services-configure-wirecast-live-encoder.md)
* Koder na żywo usługi Flash Media

#### <a name="single-bitrate-fragmented-mp4-smooth-streaming"></a>Pojedyncza szybkość transmisji bitów podzielonej zawartości w formacie MP4 (Smooth Streaming)
Typowy przypadek użycia:

Użyj lokalnych koderów na żywo od dostawców, takich jak technologie ATEME, Ericsson, Envivio, aby wysłać strumień wejściowy za pośrednictwem otwartego Internetu do pobliskiego centrum danych platformy Azure.

Zagadnienia do rozważenia:

Tak samo jak w przypadku protokołu [RTMP o pojedynczej szybkości transmisji bitów](media-services-manage-live-encoder-enabled-channels.md#single_bitrate_RTMP).

#### <a name="other-considerations"></a>Inne zagadnienia
* Nie można zmienić protokołu wejściowego, gdy kanał lub skojarzone z nim programy są uruchomione. Jeśli potrzebujesz różnych protokołów, utwórz osobny kanał dla każdego protokołu wejściowego.
* Maksymalna rozdzielczość przychodzącego strumienia wideo to 1920 x 1080, a maksymalnie 60 pól na sekundę w przypadku przeplotu lub 30 klatek na sekundę w przypadku progresywnego.

### <a name="ingest-urls-endpoints"></a>Adresy URL pozyskiwania (punkty końcowe)
Kanał udostępnia wejściowy punkt końcowy (adres URL pozyskiwania) określony w koderze dynamicznym, dzięki czemu koder może wypchnąć strumienie do kanałów.

Po utworzeniu kanału możesz uzyskać adresy URL pozyskiwania. Aby uzyskać te adresy URL, kanał nie musi znajdować się w stanie **uruchomienia** . Gdy wszystko będzie gotowe do rozpoczęcia wypychania danych do kanału, musi być w stanie **uruchomionym** . Gdy kanał zacznie pozyskiwanie danych, możesz wyświetlić podgląd strumienia za pośrednictwem adresu URL w wersji zapoznawczej.

Istnieje możliwość pozyskiwania pofragmentowanego strumienia MP4 (Smooth Streaming) na żywo za pośrednictwem połączenia TLS. Aby przejąć protokół TLS, należy zaktualizować adres URL pozyskiwania do protokołu HTTPS. Obecnie AMS nie obsługuje protokołu TLS z domenami niestandardowymi.  

### <a name="allowed-ip-addresses"></a>Dozwolone adresy IP
Można zdefiniować adresy IP, które mogą publikować wideo w tym kanale. Dozwolone adresy IP można określić jako pojedynczy adres IP (na przykład "10.0.0.1"), zakres adresów IP przy użyciu adresu IP i maski podsieci CIDR (np. "10.0.0.1/22") lub zakres adresów IP przy użyciu adresu IP i maski podsieci dziesiętnej (na przykład "10.0.0.1 (255.255.252.0)").

Jeśli adresy IP nie zostaną określone i brakuje definicji reguły, to żaden adres IP nie będzie dozwolony. Aby zezwolić na jakikolwiek adres IP, utwórz regułę i ustaw wartość 0.0.0.0/0.

## <a name="channel-preview"></a>Podgląd kanału
### <a name="preview-urls"></a>Adresy URL podglądu
Kanały zapewniają punkt końcowy (wersja zapoznawcza), który służy do wyświetlania podglądu i weryfikowania strumienia przed dalszem przetwarzaniem i dostarczaniem.

Możesz uzyskać adres URL wersji zapoznawczej podczas tworzenia kanału. Aby uzyskać adres URL, kanał nie musi znajdować się w stanie **uruchomionym** .

Gdy kanał zacznie pozyskiwanie danych, możesz wyświetlić podgląd strumienia.

> [!NOTE]
> Obecnie strumień w wersji zapoznawczej można dostarczyć tylko w pofragmentowanym formacie MP4 (Smooth Streaming) niezależnie od określonego typu danych wejściowych.  Aby wyświetlić swój strumień, możesz użyć odtwarzacza hostowanego w Azure Portal.
> 
> 

### <a name="allowed-ip-addresses"></a>Dozwolone adresy IP
Można zdefiniować adresy IP, które mogą łączyć się z punktem końcowym wersji zapoznawczej. Jeśli adresy IP nie zostaną określone, dozwolony jest dowolny adres IP. Dozwolone adresy IP można określić jako pojedynczy adres IP (na przykład "10.0.0.1"), zakres adresów IP przy użyciu adresu IP i maski podsieci CIDR (np. "10.0.0.1/22") lub zakres adresów IP przy użyciu adresu IP i maski podsieci dziesiętnej (na przykład "10.0.0.1 (255.255.252.0)").

## <a name="live-encoding-settings"></a>Ustawienia kodowania na żywo
W tej sekcji opisano, jak można dopasować ustawienia dla kodera na żywo w kanale, gdy **typ kodowania** kanału jest ustawiony na wartość **standardowa**.

> [!NOTE]
> Kanał informacyjny dotyczący wkładu może zawierać tylko jedną ścieżkę audio — wprowadzanie wielu ścieżek audio nie jest obecnie obsługiwane. Podczas przeprowadzania kodowania na żywo z [lokalnymi kodowaniem na żywo](media-services-live-streaming-with-onprem-encoders.md)można wysłać kanał informacyjny udziału w protokole Smooth Streaming zawierającym wiele ścieżek audio.
> 
> 

### <a name="ad-marker-source"></a>Źródło znacznika usługi AD
Możesz określić źródło dla sygnałów znaczników AD. Wartość domyślna to **API**, co oznacza, że koder na żywo w kanale powinien nasłuchiwać ASYNCHRONICZNEGO **interfejsu API znaczników usługi AD**.

### <a name="cea-708-closed-captions"></a>Napisy CEA 708
Opcjonalna Flaga wskazująca, że koder na żywo ma ignorować wszystkie CEAe podpisy 708 osadzone w przychodzącym wideo. Gdy flaga ma wartość false (domyślnie), koder wykrywa i ponownie wstawia dane z CEA 708 do wyjściowych strumieni wideo.

#### <a name="index"></a>Indeks
Zaleca się wysyłanie w strumieniu transportu pojedynczego programu (SPTS). Jeśli strumień wejściowy zawiera wiele programów, koder na żywo w kanale analizuje tabelę mapy programu (PMT) w danych wejściowych, identyfikuje dane wejściowe, które mają nazwę typu strumienia MPEG-2 AAC ADTS lub AC-3 system-A lub AC-3 system-B lub MPEG-2 Private PES lub MPEG-1 audio lub MPEG-2, i rozmieszcza je w kolejności określonej w funkcji PMT. Indeks oparty na zero jest następnie używany do pobrania n-ty wpisu w tym rozmieszczeniu.

#### <a name="language"></a>Język
Identyfikator języka strumienia audio zgodny z ISO 639-2, taki jak aparatu. Jeśli nie istnieje, wartość domyślna to UND (undefined).

### <a name="system-preset"></a><a id="preset"></a>Ustawienia wstępne systemu
Określa ustawienie wstępne, które ma być używane przez koder na żywo w ramach tego kanału. Obecnie jedyną dozwoloną wartością jest **Default720p** (domyślnie).

**Default720p** zakodowanie wideo do następujących 6 warstw.

#### <a name="output-video-stream"></a>Wyjściowy strumień wideo

| Multimedia | Width | Height | MaxFPS | Profil | Nazwa strumienia wyjściowego |
| --- | --- | --- | --- | --- | --- |
| 3500 |1280 |720 |30 |Wys. |Video_1280x720_3500kbps |
| 2200 |960 |540 |30 |Wys. |Video_960x540_2200kbps |
| 1350 |704 |396 |30 |Wys. |Video_704x396_1350kbps |
| 850 |512 |288 |30 |Wys. |Video_512x288_850kbps |
| 550 |384 |216 |30 |Wys. |Video_384x216_550kbps |
| 200 |340 |192 |30 |Wys. |Video_340x192_200kbps |

#### <a name="output-audio-stream"></a>Wyjściowy strumień audio

Dźwięk jest zakodowany do stereo AAC-LC o 128 kb/s, częstotliwość próbkowania 48 kHz.

## <a name="signaling-advertisements"></a>Sygnalizowanie ogłoszeń
Gdy kanał ma Live Encoding włączony, masz składnik w potoku, który przetwarza wideo, i może go manipulować. Możesz sygnalizować kanałowi, aby wstawiać do strumienia transmisji bitów wychodzącej i/lub anonse. Obrazy z obrazami są nadal używane do pozyskania danych wejściowych na żywo w niektórych przypadkach (na przykład w przypadku przerwy w działaniu handlowym). Sygnały anonsowania to sygnały zsynchronizowane czasowo, które są osadzane w strumieniu wychodzącym, aby poinformować odtwarzacz wideo o konieczności podjęcia specjalnej akcji — na przykład w celu przełączenia do reklamy w odpowiednim czasie. Zobacz ten [blog](https://codesequoia.wordpress.com/2014/02/24/understanding-scte-35/) , aby zapoznać się z omówieniem mechanizmu sygnalizowania SCTE-35 używanego do tego celu. Poniżej znajduje się typowy scenariusz, który można zaimplementować w zdarzeniu na żywo.

1. Zapoznaj się z podglądem obrazu przed rozpoczęciem zdarzenia.
2. Zapoznaj się z podglądem po zakończeniu zdarzenia, aby wyświetlić obraz po ZDARZENIu.
3. Wyświetlanie obrazu zdarzenia błędu, jeśli wystąpił problem podczas zdarzenia (na przykład awaria zasilacza w stadium).
4. Wyślij obraz rozbicie usługi AD w celu ukrycia na żywo kanału informacyjnego w trakcie przerwy komercyjnej.

Poniżej znajdują się właściwości, które można ustawić podczas sygnalizowania anonsów. 

### <a name="duration"></a>Czas trwania
Czas trwania przerwy komercyjnej (w sekundach). Ta wartość musi być wartością dodatnią różną od zera, aby można było rozpocząć przerwy komercyjne. Gdy podział komercyjny jest w toku, a czas trwania jest ustawiony na zero z CueId dopasowuje się do przechodzenia do zera, ten przerwanie zostanie anulowane.

### <a name="cueid"></a>CueId
Unikatowy identyfikator dla przerwy komercyjnej, który ma być używany przez aplikację podrzędny do podejmowania odpowiednich akcji. Musi być dodatnią liczbą całkowitą. Można ustawić tę wartość na dowolną losowo dodatnią liczbę całkowitą lub użyć systemu nadrzędnego do śledzenia identyfikatorów wskaźnika. Przed przesłaniem za pomocą interfejsu API należy przeprowadzić normalizację wszelkich identyfikatorów do dodatnich liczb całkowitych.

### <a name="show-slate"></a>Pokaż moje
Opcjonalny. Sygnalizuje koder na żywo, aby przełączać się do [domyślnego](media-services-manage-live-encoder-enabled-channels.md#default_slate) obrazu przedniego podczas handlowego przerwy i ukrywać przychodzące źródło wideo. Dźwięk jest również wyciszony na przedniej. Wartość domyślna to **false**. 

Używany obraz będzie to ten określony za pośrednictwem domyślnej właściwości identyfikator elementu zawartości w momencie tworzenia kanału. Obraz będzie rozciągany w celu dopasowania go do rozmiaru obrazu wyświetlanego. 

## <a name="insert-slate--images"></a>Wstaw obrazy z obrazami
Koder na żywo w kanale może być sygnalizowane do przełączania do obrazu z obrazem. Może być również zasygnalizowani, aby zakończyć do końca. 

Koder na żywo można skonfigurować w taki sposób, aby przełączać się do obrazu z obrazem i ukrywać sygnał wideo w pewnych sytuacjach — na przykład podczas przerwy w usłudze AD. Jeśli taki symbol nie jest skonfigurowany, wejściowy film wideo nie jest maskowany podczas tego przerwania usługi AD.

### <a name="duration"></a>Czas trwania
Czas trwania (w sekundach). Ta wartość musi być wartością dodatnią różną od zera, aby można było zacząć od siebie. W przypadku korzystania z ciągłego nałożenia, gdy zostanie określony czas trwania zero, ten, który zostanie zamknięty, zostanie zakończony.

### <a name="insert-slate-on-ad-marker"></a>Wstaw znacznik z Niemnie do usługi AD
Po ustawieniu na wartość true, to ustawienie konfiguruje koder na żywo, aby wstawić obraz obrazu w trakcie przerwy w usłudze AD. Wartością domyślną jest true. 

### <a name="default-slate-asset-id"></a><a id="default_slate"></a>Domyślny identyfikator elementu zawartości

Opcjonalny. Określa identyfikator elementu zawartości Media Services, który zawiera obraz obrazu. Wartość domyślna to null. 


> [!NOTE] 
> Przed utworzeniem kanału obraz obrazu z następującymi ograniczeniami powinien zostać przekazany jako dedykowany element zawartości (żadne inne pliki nie powinny znajdować się w tym elemencie zawartości). Ten obraz jest używany tylko wtedy, gdy koder na żywo jest wstawiany z powodu przerwy w działaniu usługi AD. Obecnie nie ma możliwości użycia obrazu niestandardowego, gdy koder na żywo wejdzie w stan "utracony sygnał wejściowy". Tę funkcję można głosować w [tym miejscu](https://feedback.azure.com/forums/169396-azure-media-services/suggestions/10190457-define-custom-slate-image-on-a-live-encoder-channel).

* 1920 x 1080.
* Co najwyżej 3 MB.
* Nazwa pliku musi mieć rozszerzenie *. jpg.
* Obraz musi być przekazany do elementu zawartości jako jedyna AssetFile w tym elemencie zawartości, a ten AssetFile powinien być oznaczony jako plik podstawowy. Zasób nie może być zaszyfrowany za pomocą magazynu.

Jeśli **domyślny identyfikator elementu zawartości** nie zostanie określony, a **w polu Wstaw znacznik w usłudze AD** jest ustawiona **wartość true**, do ukrycia wejściowego strumienia wideo zostanie użyty domyślny obraz Azure Media Services. Dźwięk jest również wyciszony na przedniej. 

## <a name="channels-programs"></a>Programy kanału
Kanał jest skojarzony z programami, które umożliwiają kontrolowanie publikowania i przechowywania segmentów strumienia na żywo. Kanały zarządzają programami. Relacja kanału i programu jest bardzo podobna do tradycyjnego nośnika, gdzie kanał ma stały strumień zawartości, a program jest objęty zakresem pewnego czasu zdarzenia w tym kanale.

Można określić liczbę godzin, aby zachować zarejestrowaną zawartość na potrzeby programu przez ustawienie długości **Okna archiwum**. Ta wartość musi mieścić się w zakresie od 5 minut do maksymalnie 25 godzin. Długość okna archiwum określa również maksymalną liczbę czasu, przez jaką klienci mogą odwrócić w czasie od bieżącego stanowiska na żywo. Programy mogą być transmitowane w określonym czasie, ale zawartość, która wykracza poza długość okna, jest stale odrzucana. Wartość tej właściwości określa również, jak długie mogą być manifesty na kliencie.

Każdy program jest skojarzony z zasobem, który przechowuje zawartość przesyłaną strumieniowo. Element zawartości jest mapowany na kontener blokowych obiektów BLOB na koncie usługi Azure Storage, a pliki w elemencie zawartości są przechowywane jako obiekty blob w tym kontenerze. Aby opublikować program, aby klienci mogli wyświetlić strumień, należy utworzyć lokalizator OnDemand dla skojarzonego elementu zawartości. Lokalizator umożliwia utworzenie adresu URL przesyłania strumieniowego, który można udostępnić klientom.

Kanał obsługuje maksymalnie trzy współbieżnie uruchomione programy, dzięki czemu można tworzyć wiele archiwów tego samego strumienia przychodzącego. Umożliwia to w razie potrzeby publikowanie i archiwizację różnych części wydarzenia. Na przykład zgodnie z wymaganiami biznesowymi potrzebna jest archiwizacja sześciu godzin programu, ale do emisji przeznaczonych jest tylko ostatnich 10 minut. W tym celu należy utworzyć dwa jednocześnie uruchomione programy. Jeden z programów jest skonfigurowany na potrzeby archiwizacji sześciu godzin zdarzenia, ale ten program nie jest publikowany. Drugi z programów jest skonfigurowany w celu archiwizacji 10 minut wydarzenia i ten program zostanie opublikowany.

Nie należy używać ponownie istniejących programów dla nowych zdarzeń. Zamiast tego należy utworzyć i uruchomić nowy program dla każdego zdarzenia, zgodnie z opisem w sekcji Programowanie strumieniowego na żywo aplikacji.

Uruchom program, gdy wszystko będzie gotowe do rozpoczęcia przesyłania strumieniowego i archiwizacji. Zatrzymaj program w dowolnym momencie, w którym chcesz zatrzymać przesyłanie strumieniowe i archiwizowanie wydarzenia. 

Aby usunąć zarchiwizowaną zawartość, zatrzymaj i usuń program, a następnie usuń skojarzony element zawartości. Nie można usunąć elementu zawartości, jeśli jest on używany przez program. Najpierw należy usunąć program. 

Nawet po zatrzymaniu i usunięciu programu użytkownicy będą mogli przesyłać strumieniowo zarchiwizowaną zawartość wideo na żądanie tak długo, jak zasoby nie zostaną usunięte.

Jeśli chcesz zachować zarchiwizowaną zawartość, ale bez udostępniania jej do przesyłania strumieniowego, usuń lokalizator przesyłania strumieniowego.

## <a name="getting-a-thumbnail-preview-of-a-live-feed"></a>Pobieranie podglądu miniatury kanału informacyjnego na żywo
Gdy Live Encoding jest włączona, możesz teraz uzyskać podgląd kanału informacyjnego na żywo, który dociera do kanału. Może to być cenny narzędzie do sprawdzenia, czy na żywo kanał informacyjny rzeczywiście dociera do kanału. 

## <a name="channel-states-and-how-states-map-to-the-billing-mode"></a><a id="states"></a>Stany kanału i sposób, w jaki Stany są mapowane do trybu rozliczania
Bieżący stan kanału. Możliwe wartości to:

* **Zatrzymano**. Jest to początkowy stan kanału po jego utworzeniu. W tym stanie właściwości kanału mogą być aktualizowane, ale przesyłanie strumieniowe jest niedozwolone.
* **Rozpoczęcie**. Kanał jest uruchamiany. W tym stanie nie są dozwolone żadne aktualizacje ani przesyłanie strumieniowe. Jeśli wystąpi błąd, kanał powróci do stanu zatrzymany.
* **Uruchomione**. Kanał może przetwarzać strumienie na żywo.
* **Zatrzymywanie**. Kanał jest zatrzymywany. W tym stanie nie są dozwolone żadne aktualizacje ani przesyłanie strumieniowe.
* **Usuwanie**. Kanał jest usuwany. W tym stanie nie są dozwolone żadne aktualizacje ani przesyłanie strumieniowe.

W poniższej tabeli przedstawiono sposób, w jaki Stany kanałów mapują się do trybu rozliczania. 

| Stan kanału | Wskaźniki interfejsu użytkownika portalu | Rozliczane? |
| --- | --- | --- |
| Uruchamianie |Uruchamianie |Nie (stan przejściowy) |
| Uruchomienie |Gotowe (brak uruchomionych programów)<br/>lub<br/>Przesyłanie strumieniowe (co najmniej jeden uruchomiony program) |Tak |
| Zatrzymywanie |Zatrzymywanie |Nie (stan przejściowy) |
| Zatrzymano |Zatrzymano |Nie |

> [!NOTE]
> Obecnie średnia wartość początku kanału wynosi około 2 minuty, ale czasami może potrwać do 20 minut. Resetowanie kanału może potrwać do 5 minut.
> 
> 

## <a name="considerations"></a><a id="Considerations"></a>Zagadnienia do rozważenia
* W przypadku kanału **standardowego** typu kodowania w przypadku utraty źródła danych wejściowych/kanału informacyjnego, jest on kompensowany przez zastępowanie źródłowego wideo/audio błędem i wysunięciem. Kanał będzie w dalszym ciągu emitować do momentu wznowienia kanału wejścia/wyjścia. Zalecamy, aby kanał na żywo nie pozostawał w stanie dłuższym niż 2 godziny. Po tym momencie zachowanie kanału na potrzeby ponownego połączenia danych wejściowych nie jest gwarantowane, ani nie jest zachowaniem w odpowiedzi na polecenie resetowania. Należy zatrzymać kanał, usunąć go i utworzyć nowy.
* Nie można zmienić protokołu wejściowego, gdy kanał lub skojarzone z nim programy są uruchomione. Jeśli potrzebujesz różnych protokołów, utwórz osobny kanał dla każdego protokołu wejściowego.
* Za każdym razem, gdy ponownie konfigurujesz koder na żywo, wywołaj metodę **resetowania** kanału. Przed zresetowaniem kanału należy zatrzymać program. Po zresetowaniu kanału należy ponownie uruchomić program.
* Kanał można zatrzymać tylko wtedy, gdy jest w stanie uruchomienia, a wszystkie programy w kanale zostały zatrzymane.
* Domyślnie można dodawać tylko 5 kanałów do konta Media Services. Jest to przydział elastyczny dla wszystkich nowych kont. Aby uzyskać więcej informacji, zobacz [przydziały i ograniczenia](media-services-quotas-and-limitations.md).
* Nie można zmienić protokołu wejściowego, gdy kanał lub skojarzone z nim programy są uruchomione. Jeśli potrzebujesz różnych protokołów, utwórz osobny kanał dla każdego protokołu wejściowego.
* Opłaty są naliczane tylko wtedy, gdy kanał jest w stanie **uruchomienia** . Aby uzyskać więcej informacji, zapoznaj się z [tą](media-services-manage-live-encoder-enabled-channels.md#states) sekcją.
* Obecnie maksymalny zalecany czas trwania wydarzenia na żywo wynosi 8 godzin. 
* Upewnij się, że punkt końcowy przesyłania strumieniowego, z którego chcesz przesłać strumieniowo zawartość w stanie **uruchomienia** .
* Ustawienie wstępne kodowania używa pojęcia "Max Frame Rate" z 30 fps. Dlatego jeśli dane wejściowe to 60fps/wyświetlaczu i, ramki wejściowe są usuwane/cofane z przeplotem do 30/29.97 fps. Jeśli dane wejściowe to 50fps/50i, ramki wejściowe są usuwane/cofane z przeplotem do 25 fps. Jeśli dane wejściowe to 25 fps, dane wyjściowe pozostają na 25 fps.
* Nie zapomnij zatrzymać kanałów po zakończeniu. Jeśli tego nie zrobisz, rozliczenia będą kontynuowane.

## <a name="known-issues"></a>Znane problemy
* Czas uruchamiania kanału został ulepszony na średnią wynoszącą 2 minuty, ale czasami może to potrwać do 20 minut.
* Obrazy z obrazami powinny być zgodne z ograniczeniami opisanymi [tutaj](media-services-manage-live-encoder-enabled-channels.md#default_slate). W przypadku próby utworzenia kanału z domyślnym przedplanszą, który jest większy niż 1920 x 1080, żądanie zostanie ostatecznie zatrzymane.
* Jeszcze raz.... nie zapomnij zatrzymać kanałów po zakończeniu przesyłania strumieniowego. Jeśli tego nie zrobisz, rozliczenia będą kontynuowane.

## <a name="need-help"></a>Potrzebujesz pomocy?

Możesz otworzyć bilet pomocy technicznej, przechodząc do [nowego żądania obsługi](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="next-step"></a>Następny krok

Przejrzyj ścieżki szkoleniowe dotyczące usługi Media Services.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Wyraź opinię
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Powiązane tematy
[Dostarczanie wydarzeń transmisji strumieniowej na żywo za pośrednictwem usługi Azure Media Services](media-services-overview.md)

[Tworzenie kanałów wykonujących kodowanie na żywo z pojedynczej szybkości transmisji bitów do strumienia z adaptacyjną szybkością transmisji bitów przy użyciu portalu](media-services-portal-creating-live-encoder-enabled-channel.md)

[Tworzenie kanałów wykonujących kodowanie na żywo z pojedynczej szybkości transmisji bitów do strumienia z adaptacyjną szybkością transmisji bitów przy użyciu zestawu .NET SDK](media-services-dotnet-creating-live-encoder-enabled-channel.md)

[Zarządzanie kanałami przy użyciu interfejsu API REST](/rest/api/media/operations/channel)

[Koncepcje Media Services](media-services-concepts.md)

[Azure Media Services pofragmentowana Specyfikacja pozyskiwania na żywo w formacie MP4](../media-services-fmp4-live-ingest-overview.md)

[live-overview]: ./media/media-services-manage-live-encoder-enabled-channels/media-services-live-streaming-new.png
