---
title: Przesyłanie strumieniowe na żywo za pomocą usługi Azure Media Services do tworzenia strumieni o wielu szybkościach transmisji bitów | Dokumenty firmy Microsoft
description: W tym temacie opisano sposób konfigurowania kanału, który odbiera pojedynczy strumień na żywo z szybkości transmisji bitów z lokalnego kodera, a następnie wykonuje kodowanie na żywo w celu adaptacyjnego strumienia szybkości transmisji bitów za pomocą usługi Media Services.
services: media-services
documentationcenter: ''
author: anilmur
manager: femila
editor: ''
ms.assetid: 30ce6556-b0ff-46d8-a15d-5f10e4c360e2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: anilmur
ms.reviewer: juliako
ms.openlocfilehash: 6210d6ee4877c6ba84178340cf0a6610e402da31
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641102"
---
# <a name="live-streaming-using-azure-media-services-to-create-multi-bitrate-streams"></a>Transmisja strumieniowa na żywo korzystająca z usługi Azure Media Services do tworzenia strumieni o różnej szybkości transmisji bitów

> [!NOTE]
> Od 12 maja 2018 r. kanały na żywo nie będą już obsługiwać protokołu pozyskiwania strumienia transportu RTP/MPEG-2. Przemiń z RTP/MPEG-2 do RTMP lub pofragmentowanych protokołów pozyskiwania MP4 (Smooth Streaming).

## <a name="overview"></a>Omówienie
W usłudze Azure Media Services (AMS) **kanał** reprezentuje potok przetwarzania zawartości przesyłania strumieniowego na żywo. **Kanał** odbiera transmisje wejściowe na żywo na jeden z dwóch sposobów:

* Lokalny koder na żywo wysyła strumień o pojedynczej szybkości transmisji bitów do kanału, który jest włączony do wykonywania kodowania na żywo za pomocą usługi Media Services w jednym z następujących formatów: RTMP lub Płynne przesyłanie strumieniowe (Fragmentaryczny MP4). Kanał wykonuje następnie kodowanie na żywo przychodzącego strumienia o pojedynczej szybkości transmisji bitów do postaci strumienia wideo o różnych szybkościach transmisji bitów (adaptacyjnej szybkości transmisji bitów). Po odebraniu żądania usługa Media Services dostarcza strumień do klientów.
* Lokalny koder na żywo wysyła do kanału o wielu szybkościach transmisji bitów **RTMP** lub **płynne przesyłanie strumieniowe** (Fragmented MP4), który nie jest włączony do wykonywania kodowania na żywo za pomocą usługi AMS. Pozyskane strumienie przechodzą przez **kanał**s bez dalszego przetwarzania. Ta metoda jest nazywana **pass-through**. Można użyć następujących koderów na żywo, które wyprowadzają wielosytowe płynne przesyłanie strumieniowe: MediaExcel, Ateme, Imagine Communications, Envivio, Cisco i Elemental. Następujące kodery na żywo wyjście RTMP: [Telestream Wirecast](media-services-configure-wirecast-live-encoder.md), Haivision, Teradek kodery.  Koder na żywo może także wysłać strumień o pojedynczej szybkości transmisji bitów do kanału, który nie obsługuje kodowania na żywo, nie jest to jednak zalecane. Po odebraniu żądania usługa Media Services dostarcza strumień do klientów.

  > [!NOTE]
  > Korzystanie z metody pass-through jest najbardziej ekonomicznym sposobem przesyłania strumieniowego na żywo.
  > 
  > 

Począwszy od wersji Media Services 2.10, podczas tworzenia kanału, można określić, w jaki sposób kanał ma odbierać strumień wejściowy i czy kanał ma wykonywać kodowanie na żywo strumienia. Dostępne są dwie opcje:

* **Brak** — określ tę wartość, jeśli planujesz użyć lokalnego kodera na żywo, który będzie wyprowadzał strumień z szybkością wielu bitów (strumień przekazu). W takim przypadku strumień przychodzący przeszedł do danych wyjściowych bez kodowania. Jest to zachowanie kanału przed wydaniem 2.10.  Aby uzyskać bardziej szczegółowe informacje na temat pracy z kanałami tego typu, zobacz [Przesyłanie strumieniowe na żywo za pomocą koderów lokalnych, które tworzą strumienie o wielu szybkościach transmisji bitów](media-services-live-streaming-with-onprem-encoders.md).
* **Standardowy** — wybierz tę wartość, jeśli zamierzasz użyć usługi Media Services do zakodowania strumienia na żywo o pojedynczej szybkości transmisji bitów do strumienia o wielu szybkościach transmisji bitów. Należy pamiętać, że istnieje wpływ rozliczeń dla kodowania na żywo i należy pamiętać, że pozostawienie kanału kodowania na żywo w stanie "Uruchomiony" spowoduje naliczenie opłat rozliczeniowych.  Zaleca się natychmiastowe zatrzymanie uruchamiania kanałów po zakończeniu transmisji na żywo, aby uniknąć dodatkowych opłat godzinowych.

> [!NOTE]
> W tym temacie omówiono atrybuty kanałów, które są włączone do wykonywania kodowania na żywo **(standardowy** typ kodowania). Aby uzyskać informacje na temat pracy z kanałami, które nie są włączone do wykonywania kodowania na żywo, zobacz [Przesyłanie strumieniowe na żywo za pomocą koderów lokalnych, które tworzą strumienie o wielu szybkościach transmisji bitów](media-services-live-streaming-with-onprem-encoders.md).
> 
> Zapoznaj się z sekcją [Zagadnienia.](media-services-manage-live-encoder-enabled-channels.md#Considerations)
> 
> 

## <a name="billing-implications"></a>Implikacje rozliczeń
Kanał kodowania na żywo rozpoczyna rozliczenia, gdy tylko zostanie przesunie się do stanu "Uruchomiony" za pośrednictwem interfejsu API.   Stan można również wyświetlić w witrynie Azure portal lubhttps://aka.ms/amse)w narzędziu Azure Media Services Explorer ( .

W poniższej tabeli pokazano, jak stany kanału mapują stany rozliczeń w interfejsie API i witrynie Azure portal. Stany różnią się nieco między interfejsem API i ux portalu. Jak tylko kanał jest w stanie "Uruchamianie" za pośrednictwem interfejsu API lub w stanie "Gotowe" lub "Przesyłanie strumieniowe" w witrynie Azure portal, rozliczenia będą aktywne.
Aby zatrzymać kanał od rozliczeń dalej, należy zatrzymać kanał za pośrednictwem interfejsu API lub w witrynie Azure portal.
Użytkownik jest odpowiedzialny za zatrzymanie kanałów po zakończeniu pracy z kanałem kodowania na żywo.  Niezatrzymanie kanału kodowania spowoduje dalsze rozliczenia.

### <a name="channel-states-and-how-they-map-to-the-billing-mode"></a><a id="states"></a>Stany kanału i sposób mapowania do trybu rozliczeniowego
Bieżący stan kanału. Możliwe wartości to:

* **Zatrzymano**. Jest to początkowy stan kanału po jego utworzeniu (chyba że autostart został wybrany w portalu.) W tym stanie nie ma żadnych rozliczeń. W tym stanie właściwości kanału mogą być aktualizowane, ale przesyłanie strumieniowe nie jest dozwolone.
* **Uruchamianie**. Kanał jest uruchamiany. W tym stanie nie ma żadnych rozliczeń. W tym stanie nie są dozwolone żadne aktualizacje ani przesyłanie strumieniowe. Jeśli wystąpi błąd, kanał powraca do stanu Zatrzymane.
* **Bieganie**. Kanał jest zdolny do przetwarzania transmisji na żywo. Jest teraz użycie rozliczeń. Aby zapobiec dalszym płatnościom, należy zatrzymać kanał. 
* **Zatrzymanie**. Kanał jest zatrzymany. W tym przejściowym stanie nie ma żadnych rozliczeń. W tym stanie nie są dozwolone żadne aktualizacje ani przesyłanie strumieniowe.
* **Usuwanie**pliku . Kanał jest usuwany. W tym przejściowym stanie nie ma żadnych rozliczeń. W tym stanie nie są dozwolone żadne aktualizacje ani przesyłanie strumieniowe.

W poniższej tabeli przedstawiono, jak stany kanału mapują tryb rozliczeń. 

| Stan kanału | Wskaźniki interfejsu użytkownika portalu | Czy to billing? |
| --- | --- | --- |
| Uruchamianie |Uruchamianie |Nie (stan przejściowy) |
| Działanie |Gotowy (brak uruchomionych programów)<br/>lub<br/>Przesyłanie strumieniowe (co najmniej jeden uruchomiony program) |TAK |
| Zatrzymywanie |Zatrzymywanie |Nie (stan przejściowy) |
| Zatrzymano |Zatrzymano |Nie |

### <a name="automatic-shut-off-for-unused-channels"></a>Automatyczne wyłączanie nieużywanych kanałów
Począwszy od 25 stycznia 2016 r. usługa Media Services wprowadziła aktualizację, która automatycznie zatrzymuje kanał (z włączonym kodowaniem na żywo) po długim okresie działania w stanie nieużywanym. Dotyczy to kanałów, które nie mają aktywnych programów i które nie otrzymały wejściowego kanału wkładu przez dłuższy okres czasu.

Próg dla niewykorzystanego okresu wynosi nominalnie 12 godzin, ale może ulec zmianie.

## <a name="live-encoding-workflow"></a>Przepływ pracy kodowania na żywo
Poniższy diagram reprezentuje przepływ pracy przesyłania strumieniowego na żywo, w którym kanał odbiera pojedynczy strumień szybkości transmisji bitów w jednym z następujących protokołów: RTMP lub Smooth Streaming; następnie koduje strumień do strumienia o wielu szybkościach transmisji bitów. 

![Przepływ pracy na żywo][live-overview]

## <a name="common-live-streaming-scenario"></a><a id="scenario"></a>Typowy scenariusz transmisji strumieniowej na żywo
Poniżej przedstawiono ogólne etapy tworzenia typowych aplikacji transmisji strumieniowej na żywo.

> [!NOTE]
> Obecnie maksymalny zalecany czas trwania wydarzenia na żywo wynosi 8 godzin.
>
> Istnieje wpływ rozliczeń dla kodowania na żywo i należy pamiętać, że pozostawienie kanału kodowania na żywo w stanie "Uruchomiony" spowoduje naliczenie opłat godzinowych. Zaleca się natychmiastowe zatrzymanie uruchamiania kanałów po zakończeniu transmisji na żywo, aby uniknąć dodatkowych opłat godzinowych. 

1. Podłącz kamerę wideo do komputera. Uruchom i skonfiguruj lokalny koder na żywo, który może przesyłać **pojedynczy** strumień szybkości transmisji bitów w jednym z następujących protokołów: RTMP lub Smooth Streaming. 

    Ten krok można również wykonać po utworzeniu kanału.
2. Utwórz i uruchom kanał. 
3. Pobierz adres URL pozyskiwania kanału. 

    Koder na żywo używa adresu URL pozyskiwania do wysyłania strumienia do kanału.
4. Pobierz adres URL podglądu kanału. 

    Użyj tego adresu URL, aby sprawdzić, czy kanał prawidłowo odbiera strumień na żywo.
5. Tworzenie programu. 

    Podczas korzystania z witryny Azure portal, tworzenie programu również tworzy zasób. 

    Podczas korzystania z .NET SDK lub REST należy utworzyć zasób i określić, aby użyć tego zasobu podczas tworzenia programu. 
6. Opublikuj zasób skojarzony z programem.   

    >[!NOTE]
    >Po utworzeniu konta usługi AMS zostanie do niego dodany **domyślny** punkt końcowy przesyłania strumieniowego mający stan **Zatrzymany**. Punkt końcowy przesyłania strumieniowego, z którego chcesz strumieniowo przesyłać zawartość, musi mieć stan **Uruchomiony**. 

7. Uruchom program, gdy wszystko będzie gotowe do rozpoczęcia przesyłania strumieniowego i archiwizacji.
8. Opcjonalnie można przesłać do kodera na żywo sygnał o rozpoczęciu reklamy. Reklama jest wstawiana do strumienia wyjściowego.
9. Zatrzymaj program w dowolnym momencie, w którym chcesz zatrzymać przesyłanie strumieniowe i archiwizowanie wydarzenia.
10. Usuń program (opcjonalnie można również usunąć zasób).   

> [!NOTE]
> Bardzo ważne jest, aby nie zapomnieć o zatrzymaniu kanału kodowania na żywo. Należy pamiętać, że istnieje wpływ na rozliczenia godzinowe dla kodowania na żywo i należy pamiętać, że pozostawienie kanału kodowania na żywo w stanie "Uruchomiony" spowoduje naliczenie opłat rozliczeniowych.  Zaleca się natychmiastowe zatrzymanie uruchamiania kanałów po zakończeniu transmisji na żywo, aby uniknąć dodatkowych opłat godzinowych. 
> 
> 

## <a name="channels-input-ingest-configurations"></a><a id="channel"></a>Konfiguracje wprowadzania (pozyskiwania) kanału
### <a name="ingest-streaming-protocol"></a><a id="Ingest_Protocols"></a>Połknienie protokołu przesyłania strumieniowego
Jeśli **typ kodera** jest ustawiony na **Standardowy,** prawidłowe opcje to:

* Rtmp o pojedynczej szybkości transmisji **bitów**
* Pojedyncza bitrate **Fragmented MP4** (płynne przesyłanie strumieniowe)

#### <a name="single-bitrate-rtmp"></a><a id="single_bitrate_RTMP"></a>Pojedyncza szybkość transmisji bitów w formacie RTMP
Zagadnienia do rozważenia:

* Strumień przychodzący nie może zawierać wideo o wielu szybkościach transmisji bitów
* Strumień wideo powinien mieć średnią szybkość transmisji bitów poniżej 15 Mb/s
* Strumień audio powinien mieć średnią szybkość transmisji bitów poniżej 1 Mb/s
* Poniżej znajdują się obsługiwane kodeki:
* Mpeg-4 AVC / H.264 Wideo
* Linia bazowa, główny, profil wysoki (8-bitowy 4:2:0)
* Wysoki profil 10 (10-bitowy 4:2:0)
* Wysoki profil 422 (10-bitowy 4:2:2)
* MPEG-2 AAC-LC Audio
* Mono, Stereo, Surround (5.1, 7.1)
* Częstotliwość próbkowania 44,1 kHz
* Opakowania ADTS w stylu MPEG-2
* Zalecane kodery obejmują:
* [Telestream Wirecast](media-services-configure-wirecast-live-encoder.md)
* Koder Flash Media Live

#### <a name="single-bitrate-fragmented-mp4-smooth-streaming"></a>Pojedyncza szybkość transmisji bitów podzielonej zawartości w formacie MP4 (Smooth Streaming)
Typowy przypadek użycia:

Użyj lokalnych koderów na żywo od dostawców, takich jak Elemental Technologies, Ericsson, Ateme, Envivio, aby wysłać strumień wejściowy za pośrednictwem otwartego Internetu do pobliskiego centrum danych platformy Azure.

Zagadnienia do rozważenia:

Tak samo jak w przypadku [pojedynczej szybkości transmisji bitów RTMP](media-services-manage-live-encoder-enabled-channels.md#single_bitrate_RTMP).

#### <a name="other-considerations"></a>Inne zagadnienia
* Nie można zmienić protokołu wejściowego, gdy kanał lub skojarzone z nim programy są uruchomione. Jeśli potrzebujesz różnych protokołów, utwórz osobny kanał dla każdego protokołu wejściowego.
* Maksymalna rozdzielczość przychodzącego strumienia wideo to 1920x1080, a co najwyżej 60 pól na sekundę w przypadku przeplotu lub 30 klatek na sekundę w przypadku postępu.

### <a name="ingest-urls-endpoints"></a>Pochwyć adresy URL (punkty końcowe)
Kanał zawiera wejściowy punkt końcowy (adres URL pozyskiwania), który można określić w koderze na żywo, dzięki czemu koder może wypychać strumienie do kanałów.

Po utworzeniu kanału możesz uzyskać adresy URL pozyskiwania. Aby uzyskać te adresy URL, kanał nie musi być w stanie **uruchamiania.** Gdy wszystko będzie gotowe do rozpoczęcia wypychania danych do kanału, musi być w stanie **uruchamiania.** Po uruchomieniu kanału pozyskiwania danych można wyświetlić podgląd strumienia za pomocą adresu URL wersji zapoznawczej.

Masz możliwość pozyskiwania fragmentarycznego mp4 (Smooth Streaming) strumień na żywo za połączenie TLS. Aby nalecieć za pośrednictwem protokołu TLS, należy zaktualizować adres URL pozyskiwania do protokołu HTTPS. Obecnie usługa AMS nie obsługuje protokołu TLS z domenami niestandardowymi.  

### <a name="allowed-ip-addresses"></a>Dozwolone adresy IP
Można zdefiniować adresy IP, które mogą publikować wideo na tym kanale. Dozwolone adresy IP można określić jako pojedynczy adres IP (na przykład "10.0.0.1"), zakres ADRESÓW IP przy użyciu adresu IP i maski podsieci CIDR (na przykład "10.0.0.1/22") lub zakres IP przy użyciu adresu IP i kropkowanego podsieci dziesiętnej (na przykład '10.0.0.1(255.255.252.0)").

Jeśli adresy IP nie zostaną określone i brakuje definicji reguły, to żaden adres IP nie będzie dozwolony. Aby zezwolić na jakikolwiek adres IP, utwórz regułę i ustaw wartość 0.0.0.0/0.

## <a name="channel-preview"></a>Podgląd kanału
### <a name="preview-urls"></a>Wyświetlanie adresów URL w wersji zapoznawczej
Kanały zapewniają punkt końcowy podglądu (adres URL w wersji zapoznawczej), który służy do podglądu i sprawdzania poprawności strumienia przed dalszym przetwarzaniem i dostarczaniem.

Podczas tworzenia kanału możesz uzyskać adres URL podglądu. Aby uzyskać adres URL, kanał nie musi być w stanie **uruchamiania.**

Po uruchomieniu kanału pozyskiwania danych można wyświetlić podgląd strumienia.

> [!NOTE]
> Obecnie strumień podglądu może być dostarczany tylko w formacie Fragmented MP4 (Smooth Streaming), niezależnie od określonego typu wejściowego.  Odtwarzacza hostowanego w witrynie Azure portal można wyświetlić, aby wyświetlić strumień.
> 
> 

### <a name="allowed-ip-addresses"></a>Dozwolone adresy IP
Można zdefiniować adresy IP, które mogą łączyć się z punktem końcowym podglądu. Jeśli nie określono żadnych adresów IP, każdy adres IP będzie dozwolony. Dozwolone adresy IP można określić jako pojedynczy adres IP (na przykład "10.0.0.1"), zakres ADRESÓW IP przy użyciu adresu IP i maski podsieci CIDR (na przykład "10.0.0.1/22") lub zakres IP przy użyciu adresu IP i kropkowanego podsieci dziesiętnej (na przykład '10.0.0.1(255.255.252.0)").

## <a name="live-encoding-settings"></a>Ustawienia kodowania na żywo
W tej sekcji opisano, jak można dostosować ustawienia kodera na żywo w kanale, gdy **typ kodowania** kanału jest ustawiony na **Standardowy**.

> [!NOTE]
> Kanał wkładu może zawierać tylko jedną ścieżkę audio — obecnie nie jest obsługiwane łech można posiedzić wiele ścieżek audio. Podczas kodowania na żywo za pomocą [lokalnych kodów na żywo](media-services-live-streaming-with-onprem-encoders.md)można wysłać kanał wpłaty w protokole Płynne przesyłanie strumieniowe zawierającego wiele ścieżek audio.
> 
> 

### <a name="ad-marker-source"></a>Źródło znacznika reklamy
Możesz określić źródło sygnałów znaczników reklam. Wartością domyślną jest **Api**, co oznacza, że koder na żywo w kanale powinien nasłuchiwać asynchronicznego **interfejsu API znacznika reklamy.**

### <a name="cea-708-closed-captions"></a>CEA 708 Podpisy kodowane
Opcjonalna flaga informująca kodera na żywo o ignorowaniu wszystkich danych podpisów CEA 708 osadzonych w przychodzącym filmie wideo. Gdy flaga jest ustawiona na false (domyślnie), koder wykryje i ponownie wstawi dane CEA 708 do strumieni wideo wyjściowych.

#### <a name="index"></a>Indeks
Zaleca się wysyłanie w jednym strumieniu transportu programu (SPTS). Jeśli strumień wejściowy zawiera wiele programów, koder na żywo w kanale analizuje tabelę map programu (PMT) na danych wejściowych, identyfikuje wejścia, które mają nazwę typu strumienia MPEG-2 AAC ADTS lub AC-3 System-A lub AC-3 System-B lub MPEG-2 Private PES lub MPEG-1 Audio lub MPEG-2 Audio i rozmieszcza je w kolejności określonej w PMT. Indeks oparty na wartości zero jest następnie używany do pobrania n-ty wpis w tym układzie.

#### <a name="language"></a>Język
Identyfikator języka strumienia audio, zgodny z normą ISO 639-2, na przykład ENG. Jeśli nie jest obecny, domyślnie jest UND (undefined).

### <a name="system-preset"></a><a id="preset"></a>Ustawienia wstępne systemu
Określa ustawienie predefiniowane, które ma być używane przez koder na żywo w tym kanale. Obecnie jedyną dozwoloną wartością jest **Default720p** (domyślnie).

**Domyślnie720p** zakoduje film na następujących 6 warstwach.

#### <a name="output-video-stream"></a>Wyjściowy strumień wideo

| Bitrate | impulsów | Właściwość Height | Maksymalna liczba klatek na sekundę | Profil | Nazwa strumienia wyjściowego |
| --- | --- | --- | --- | --- | --- |
| 3500 |1280 |720 |30 |Wysoka |Video_1280x720_3500kbps |
| 2200 |960 |540 |30 |Wysoka |Video_960x540_2200kbps |
| 1350 |704 |396 |30 |Wysoka |Video_704x396_1350kbps |
| 850 |512 |288 |30 |Wysoka |Video_512x288_850kbps |
| 550 |384 |216 |30 |Wysoka |Video_384x216_550kbps |
| 200 |340 |192 |30 |Wysoka |Video_340x192_200kbps |

#### <a name="output-audio-stream"></a>Strumień audio wyjściowego

Dźwięk jest kodowany do stereo AAC-LC z szybkością 128 kb/s, częstotliwość próbkowania 48 kHz.

## <a name="signaling-advertisements"></a>Sygnalizacja reklam
Gdy twój kanał ma włączone kodowanie na żywo, masz składnik w potoku, który przetwarza wideo i można nim manipulować. Można zasygnalizować kanałowi, aby wstawić plansze i/lub reklamy do wychodzącego strumienia adaptacyjnej szybkości transmisji bitów. Łupki to obrazy, których można użyć do ukrycia wejściowego kanału na żywo w niektórych przypadkach (na przykład podczas przerwy handlowej). Sygnały reklamowe to sygnały zsynchronizowane z synchronizacją czasu, które osadzasz w strumieniu wychodzącym, aby poinformować odtwarzacz wideo o podjęcie specjalnych działań , takich jak przejście na reklamę w odpowiednim czasie. Zobacz ten [blog,](https://codesequoia.wordpress.com/2014/02/24/understanding-scte-35/) aby zapoznać się z mechanizmem sygnalizacji SCTE-35 używanym w tym celu. Poniżej znajduje się typowy scenariusz, który można zaimplementować w wydarzeniu na żywo.

1. Niech widzowie otrzymają obraz przed wydarzeniem przed rozpoczęciem wydarzenia.
2. Niech widzowie otrzymają obraz PO WYDARZENIU po zakończeniu wydarzenia.
3. Niech widzowie otrzymają obraz ZDARZENIA BŁĘDU, jeśli wystąpi problem podczas wydarzenia (na przykład awaria zasilania na stadionie).
4. Wyślij obraz AD-BREAK, aby ukryć kanał zdarzeń na żywo podczas przerwy handlowej.

Poniżej przedstawiono właściwości, które można ustawić podczas sygnalizowania reklam. 

### <a name="duration"></a>Czas trwania
Czas trwania, w sekundach przerwy handlowej. Musi to być wartość dodatnia niezerowa, aby rozpocząć przerwę handlową. Gdy przerwa handlowa jest w toku, a czas trwania jest ustawiony na zero z CueId pasujące w toku przerwy handlowej, a następnie, że przerwa jest anulowana.

### <a name="cueid"></a>Identyfikator CueId
Unikalny identyfikator przerwy handlowej, który ma być używany przez aplikację niższego szczebla do podjęcia odpowiednich działań. Musi być dodatnią całkowitej liczby. Można ustawić tę wartość na dowolną losową dodatnią ujęć całkowitą lub użyć systemu nadrzędnego do śledzenia identyfikatorów cue. Upewnij się, że normalizuje wszelkie identyfikatory do dodatnich liczby całkowite przed przesłaniem za pośrednictwem interfejsu API.

### <a name="show-slate"></a>Pokaż łupek
Element opcjonalny. Sygnalizuje koderowi na żywo, aby przełączył się na domyślny obraz [łupków](media-services-manage-live-encoder-enabled-channels.md#default_slate) podczas przerwy handlowej i ukrył przychodzący kanał wideo. Dźwięk jest również wyciszony podczas łupków. Wartość domyślna to **false**. 

Używany obraz będzie określony za pośrednictwem domyślnej właściwości identyfikatora zasobu łupków w momencie tworzenia kanału. Łupek zostanie rozciągnięty, aby dopasować go do rozmiaru obrazu wyświetlanego. 

## <a name="insert-slate--images"></a>Wstawianie obrazów łupków
Koder na żywo w kanale może być sygnalizowany, aby przełączyć się na obraz łupków. Może być również sygnalizowane, aby zakończyć w toku łupków. 

Koder na żywo można skonfigurować tak, aby przełączał się na obraz z łupków i ukrywał przychodzący sygnał wideo w określonych sytuacjach – na przykład podczas przerwy reklamowej. Jeśli taki łupek nie jest skonfigurowany, wejściowy film wideo nie jest maskowany podczas tej przerwy reklamowej.

### <a name="duration"></a>Czas trwania
Czas trwania łupka w sekundach. Musi to być wartość dodatnia niezerowa, aby rozpocząć łupek. Jeśli jest w toku łupków i czas trwania zero jest określony, a następnie, że w toku łupków zostanie zakończona.

### <a name="insert-slate-on-ad-marker"></a>Wstawianie łupków na znaczniku reklamy
Po ustawieniu na wartość true to ustawienie konfiguruje koder na żywo do wstawiania obrazu z łupków podczas przerwy reklamy. Wartością domyślną jest true. 

### <a name="default-slate-asset-id"></a><a id="default_slate"></a>Domyślny identyfikator zasobu łupków

Element opcjonalny. Określa identyfikator zasobu zasobu usługi Media Services zawierający obraz łupków. Wartość domyślna to wartość null. 


> [!NOTE] 
> Przed utworzeniem kanału obraz łupków z następującymi ograniczeniami powinien zostać przekazany jako dedykowany zasób (żadne inne pliki nie powinny znajdować się w tym zasobie). Ten obraz jest używany tylko wtedy, gdy koder na żywo wstawia planszę z powodu przerwy reklamy lub został jawnie zasygnalizowany o wstawieniu tablicy. Obecnie nie ma opcji użycia niestandardowego obrazu, gdy koder na żywo wejdzie w taki stan "utracony sygnał wejściowy". Możesz głosować na tę funkcję [tutaj](https://feedback.azure.com/forums/169396-azure-media-services/suggestions/10190457-define-custom-slate-image-on-a-live-encoder-channel).

* Co najwyżej 1920x1080 w rozdzielczości.
* Najwyżej 3 Mbytes w rozmiarze.
* Nazwa pliku musi mieć rozszerzenie *.jpg.
* Obraz musi zostać przekazany do zasobu jako jedyny plik zasobów w tym zasobie, a ten plik zasobów powinien być oznaczony jako plik podstawowy. Zasób nie może być zaszyfrowany magazynem.

Jeśli **domyślny identyfikator zasobu łupków** nie jest określony, a **wstawianie łupków w znaczniku reklamy** jest ustawione na **true,** domyślny obraz usługi Azure Media Services będzie używany do ukrywania wejściowego strumienia wideo. Dźwięk jest również wyciszony podczas łupków. 

## <a name="channels-programs"></a>Programy kanału
Kanał jest skojarzony z programami, które umożliwiają kontrolowanie publikowania i przechowywania segmentów strumienia na żywo. Kanały zarządzają programami. Relacja Kanał i program jest bardzo podobna do tradycyjnych mediów, w których kanał ma stały strumień zawartości, a program jest ograniczony do pewnego zdarzenia czasowego na tym kanale.

Można określić liczbę godzin, aby zachować zarejestrowaną zawartość na potrzeby programu przez ustawienie długości **Okna archiwum**. Ta wartość musi mieścić się w zakresie od 5 minut do maksymalnie 25 godzin. Długość okna archiwum określa również maksymalną liczbę czasu, przez jaki klienci mogą szukać w czasie z bieżącej pozycji na żywo. Programy mogą być transmitowane w określonym czasie, ale zawartość, która wykracza poza długość okna, jest stale odrzucana. Wartość tej właściwości określa również, jak długie mogą być manifesty na kliencie.

Każdy program jest skojarzony z zasobem, który przechowuje zawartość przesyłaną strumieniowo. Zasób jest mapowany do kontenera bloku obiektów blob na koncie usługi Azure Storage, a pliki w zasobie są przechowywane jako obiekty BLOB w tym kontenerze. Aby opublikować program, aby klienci mogli wyświetlić strumień, należy utworzyć lokalizator OnDemand dla skojarzonego zasobu. Lokalizator umożliwia utworzenie adresu URL przesyłania strumieniowego, który można udostępnić klientom.

Kanał obsługuje maksymalnie trzy jednocześnie uruchomione programy, dzięki czemu można utworzyć wiele archiwów tego samego strumienia przychodzącego. Umożliwia to w razie potrzeby publikowanie i archiwizację różnych części wydarzenia. Na przykład zgodnie z wymaganiami biznesowymi potrzebna jest archiwizacja sześciu godzin programu, ale do emisji przeznaczonych jest tylko ostatnich 10 minut. W tym celu należy utworzyć dwa jednocześnie uruchomione programy. Jeden z programów jest skonfigurowany na potrzeby archiwizacji sześciu godzin zdarzenia, ale ten program nie jest publikowany. Drugi z programów jest skonfigurowany w celu archiwizacji 10 minut wydarzenia i ten program zostanie opublikowany.

Nie należy używać ponownie istniejących programów dla nowych zdarzeń. Zamiast tego należy utworzyć i uruchomić nowy program dla każdego zdarzenia, zgodnie z opisem w sekcji Programowanie aplikacji do przesyłania strumieniowego na żywo.

Uruchom program, gdy wszystko będzie gotowe do rozpoczęcia przesyłania strumieniowego i archiwizacji. Zatrzymaj program w dowolnym momencie, w którym chcesz zatrzymać przesyłanie strumieniowe i archiwizowanie wydarzenia. 

Aby usunąć zarchiwizowaną zawartość, zatrzymaj i usuń program, a następnie usuń skojarzony element zawartości. Nie można usunąć elementu zawartości, jeśli jest on używany przez program. Najpierw należy usunąć program. 

Nawet po zatrzymaniu i usunięciu programu użytkownicy będą mogli przesyłać strumieniowo zarchiwizowaną zawartość wideo na żądanie tak długo, jak zasoby nie zostaną usunięte.

Jeśli chcesz zachować zarchiwizowaną zawartość, ale bez udostępniania jej do przesyłania strumieniowego, usuń lokalizator przesyłania strumieniowego.

## <a name="getting-a-thumbnail-preview-of-a-live-feed"></a>Wyświetlanie podglądu miniatur w kanale na żywo
Gdy kodowanie na żywo jest włączone, możesz teraz uzyskać podgląd kanału na żywo, gdy dotrze do kanału. Może to być cenne narzędzie, aby sprawdzić, czy twój kanał na żywo rzeczywiście dociera do kanału. 

## <a name="channel-states-and-how-states-map-to-the-billing-mode"></a><a id="states"></a>Stany kanału i sposób mapowania stanów do trybu rozliczeń
Bieżący stan kanału. Możliwe wartości to:

* **Zatrzymano**. Jest to początkowy stan kanału po jego utworzeniu. W tym stanie właściwości kanału mogą być aktualizowane, ale przesyłanie strumieniowe nie jest dozwolone.
* **Uruchamianie**. Kanał jest uruchamiany. W tym stanie nie są dozwolone żadne aktualizacje ani przesyłanie strumieniowe. Jeśli wystąpi błąd, kanał powraca do stanu Zatrzymane.
* **Bieganie**. Kanał jest zdolny do przetwarzania transmisji na żywo.
* **Zatrzymanie**. Kanał jest zatrzymany. W tym stanie nie są dozwolone żadne aktualizacje ani przesyłanie strumieniowe.
* **Usuwanie**pliku . Kanał jest usuwany. W tym stanie nie są dozwolone żadne aktualizacje ani przesyłanie strumieniowe.

W poniższej tabeli przedstawiono, jak stany kanału mapują tryb rozliczeń. 

| Stan kanału | Wskaźniki interfejsu użytkownika portalu | Rozliczane? |
| --- | --- | --- |
| Uruchamianie |Uruchamianie |Nie (stan przejściowy) |
| Działanie |Gotowy (brak uruchomionych programów)<br/>lub<br/>Przesyłanie strumieniowe (co najmniej jeden uruchomiony program) |Tak |
| Zatrzymywanie |Zatrzymywanie |Nie (stan przejściowy) |
| Zatrzymano |Zatrzymano |Nie |

> [!NOTE]
> Obecnie średnia startu kanału wynosi około 2 minut, ale czasami może potrwać do 20 + minut. Resetowanie kanałów może potrwać do 5 minut.
> 
> 

## <a name="considerations"></a><a id="Considerations"></a>Zagadnienia do rozważenia
* Gdy typ kodowania Kanał **standardowy** doświadcza utraty źródła wejściowego/źródła wkładu, kompensuje go, zastępując źródłowy obraz/audio łupkiem błędu i ciszą. Kanał będzie nadal emitować łupków, aż do wejścia /wkładu paszy zostanie wznowione. Zaleca się, aby kanał na żywo nie był pozostawiony w takim stanie dłużej niż 2 godziny. Poza tym punktem zachowanie kanału na ponowne połączenie wejściowe nie jest gwarantowana, ani jego zachowanie w odpowiedzi na reset polecenia. Będziesz musiał zatrzymać kanał, usunąć go i utworzyć nowy.
* Nie można zmienić protokołu wejściowego, gdy kanał lub skojarzone z nim programy są uruchomione. Jeśli potrzebujesz różnych protokołów, utwórz osobny kanał dla każdego protokołu wejściowego.
* Za każdym razem, gdy ponownie skonfigurujesz koder na żywo, wywołaj metodę **Reset** na kanale. Przed zresetowaniem kanału należy zatrzymać program. Po zresetowaniu kanału uruchom ponownie program.
* Kanał można zatrzymać tylko wtedy, gdy jest w stanie uruchamiania, a wszystkie programy na kanale zostały zatrzymane.
* Domyślnie do konta usługi Media Services można dodać tylko 5 kanałów. Jest to przydział miękki dla wszystkich nowych kont. Aby uzyskać więcej informacji, zobacz [Przydziały i ograniczenia](media-services-quotas-and-limitations.md).
* Nie można zmienić protokołu wejściowego, gdy kanał lub skojarzone z nim programy są uruchomione. Jeśli potrzebujesz różnych protokołów, utwórz osobny kanał dla każdego protokołu wejściowego.
* Naliczane są tylko wtedy, gdy kanał jest w stanie **uruchomiony.** Aby uzyskać więcej informacji, zapoznaj się z [tą](media-services-manage-live-encoder-enabled-channels.md#states) sekcją.
* Obecnie maksymalny zalecany czas trwania wydarzenia na żywo wynosi 8 godzin. 
* Upewnij się, że punkt końcowy przesyłania strumieniowego, z którego chcesz przesyłać strumieniowo zawartość w stanie **Uruchamianie.**
* Predefiniowane kodowanie używa pojęcia "maksymalna liczba klatek na sekundę" 30 kl./s. Więc jeśli wejście jest 60fps / 59.94i, ramki wejściowe są porzucone / de-przeplot do 30/29.97 fps. Jeśli wejście wynosi 50 kl./s/50i, klatki wejściowe są odrzucane/odmtowane do 25 kl./s. Jeśli wejście wynosi 25 kl./s, wyjście pozostaje przy 25 kl./s.
* Nie zapomnij zatrzymać swoje kanały, gdy skończysz. Jeśli tego nie zrobisz, rozliczenia będą kontynuowane.

## <a name="known-issues"></a>Znane problemy
* Czas uruchomienia kanału został poprawiony do średnio 2 minut, ale w okresach zwiększonego popytu może trwać do 20 + minut.
* Obrazy łupków powinny być zgodne z ograniczeniami opisanymi [tutaj](media-services-manage-live-encoder-enabled-channels.md#default_slate). Jeśli spróbujesz utworzyć kanał z domyślną planszę, która jest większa niż 1920x1080, żądanie zostanie ostatecznie błąd.
* Jeszcze raz.... nie zapomnij zatrzymać swoje kanały, gdy skończysz przesyłać strumieniowo. Jeśli tego nie zrobisz, rozliczenia będą kontynuowane.

## <a name="need-help"></a>Potrzebujesz pomocy?

Możesz otworzyć bilet pomocy technicznej, przechodząc do [nowego żądania pomocy technicznej](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="next-step"></a>Następny krok

Przejrzyj ścieżki szkoleniowe dotyczące usługi Media Services.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Powiązane tematy
[Dostarczanie wydarzeń transmisji strumieniowej na żywo za pośrednictwem usługi Azure Media Services](media-services-overview.md)

[Tworzenie kanałów, które wykonują kodowanie na żywo z szybkości transmisji bitów do adaptacyjnego strumienia szybkości transmisji bitów za pomocą portalu](media-services-portal-creating-live-encoder-enabled-channel.md)

[Tworzenie kanałów, które wykonują kodowanie na żywo z szybkości transmisji bitów do adaptacyjnego strumienia szybkości transmisji bitów za pomocą pliku .NET SDK](media-services-dotnet-creating-live-encoder-enabled-channel.md)

[Zarządzanie kanałami za pomocą interfejsu REST API](https://docs.microsoft.com/rest/api/media/operations/channel)

[Pojęcia dotyczące usług multimedialnych](media-services-concepts.md)

[Specyfikacja pochłoniętych plików MP4 w usłudze AZURE Media Services](../media-services-fmp4-live-ingest-overview.md)

[live-overview]: ./media/media-services-manage-live-encoder-enabled-channels/media-services-live-streaming-new.png

