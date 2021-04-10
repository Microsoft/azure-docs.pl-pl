---
title: Przesyłanie strumieniowe na żywo za pomocą koderów lokalnych, które tworzą strumienie o wysokiej szybkości transmisji bitów — platforma Azure | Microsoft Docs
description: W tym temacie opisano sposób konfigurowania kanału, który odbiera strumień na żywo o wysokiej szybkości transmisji bitów z kodera lokalnego.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: d9f0912d-39ec-4c9c-817b-e5d9fcf1f7ea
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 316372f091833519f0479d07355d2845c82743b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103014846"
---
# <a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders"></a>Praca z kanałami odbierającymi strumień na żywo o wysokiej szybkości transmisji bitów z koderów lokalnych

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Od 12 maja 2018 kanały na żywo nie będą już obsługiwać protokołu pozyskiwania strumienia transportowego RTP/MPEG-2. Przeprowadź migrację z protokołów RTP/MPEG-2 do protokołu RTMP lub pofragmentowanych plików MP4 (Smooth Streaming).

## <a name="overview"></a>Omówienie
W Azure Media Services *kanał* reprezentuje potok do przetwarzania zawartości przesyłania strumieniowego na żywo. Kanał odbiera strumienie wejściowe na żywo na jeden z dwóch sposobów:

* Lokalny koder na żywo wysyła strumień RTMP o dużej szybkości transmisji bitów lub Smooth Streaming (pofragmentowany plik MP4) do kanału, w którym nie włączono kodowania na żywo z Media Services. Pozyskiwane strumienie są przekazywane przez kanały bez dalszych procesów. Ta metoda jest nazywana *przekazywaniem*. Koder na żywo może również wysyłać strumień o pojedynczej szybkości transmisji bitów do kanału, w którym nie włączono obsługi kodowania na żywo, ale nie zalecamy tego. Media Services dostarcza strumieniu do klientów, którzy go żądają.

  > [!NOTE]
  > Użycie metody przekazującej to najbardziej ekonomiczny sposób na przesyłanie strumieniowe na żywo.


* Lokalny koder na żywo wysyła strumień o pojedynczej szybkości transmisji bitów do kanału, w którym włączono obsługę kodowania na żywo za pomocą Media Services w jednym z następujących formatów: RTMP lub Smooth Streaming (fragmentacja MP4). Następnie kanał wykonuje kodowanie na żywo przychodzącego strumienia o pojedynczej szybkości transmisji bitów do strumienia wideo o wysokiej szybkości transmisji bitów (adaptacyjnej). Media Services dostarcza strumieniu do klientów, którzy go żądają.

Począwszy od wersji 2,10 Media Services, podczas tworzenia kanału można określić, w jaki sposób kanał ma otrzymać strumień wejściowy. Możesz również określić, czy kanał ma wykonywać kodowanie na żywo strumienia. Dostępne są dwie opcje:

* **Przekazuj**: należy określić tę wartość, jeśli planujesz używać lokalnego kodera na żywo, który ma strumień o wysokiej szybkości transmisji bitów (strumień Pass-through) jako dane wyjściowe. W tym przypadku strumień przychodzący przechodzi do danych wyjściowych bez kodowania. Jest to zachowanie kanału przed wydaniem 2,10. Ten artykuł zawiera szczegółowe informacje o pracy z kanałami tego typu.
* **Live Encoding**: Wybierz tę wartość, jeśli planujesz używać Media Services do kodowania strumienia na żywo o pojedynczej szybkości transmisji bitów do strumienia o większej szybkości transmisji bitów. Pozostawienie kanału kodowania na żywo w stanie **uruchomionym** wiąże się z naliczeniem opłat. Zalecamy natychmiastowe zatrzymanie uruchomionych kanałów po zakończeniu zdarzenia przesyłania strumieniowego na żywo, aby uniknąć dodatkowych opłat godzinowych. Media Services dostarcza strumieniu do klientów, którzy go żądają.

> [!NOTE]
> W tym artykule omówiono atrybuty kanałów, które nie obsługują wykonywania kodowania na żywo. Aby uzyskać informacje na temat pracy z kanałami obsługującymi kodowanie na żywo, zobacz [przesyłanie strumieniowe na żywo przy użyciu Azure Media Services do tworzenia strumieni o wysokiej szybkości transmisji bitów](media-services-manage-live-encoder-enabled-channels.md).
>
>Aby uzyskać informacje o zalecanych koderach lokalnych, zobacz [zalecane w przypadku koderów lokalnych](media-services-recommended-encoders.md).

Na poniższym diagramie przedstawiono przepływ pracy przesyłania strumieniowego na żywo, który używa lokalnego kodera na żywo w celu uzyskania strumieniowego protokołu RTMP o dużej szybkości transmisji bitów lub fragmentacji plików MP4 (Smooth Streaming) jako danych wyjściowych.

![Przepływ pracy na żywo][live-overview]

## <a name="common-live-streaming-scenario"></a><a id="scenario"></a>Typowy scenariusz przesyłania strumieniowego na żywo
W poniższych krokach opisano zadania związane z tworzeniem typowych aplikacji przesyłanych strumieniowo na żywo.

1. Podłącz kamerę wideo do komputera. Uruchom i skonfiguruj lokalny koder na żywo, który ma strumień o dużej szybkości transmisji bitów lub fragmentację MP4 (Smooth Streaming) jako dane wyjściowe. Aby uzyskać więcej informacji, zobacz temat [Obsługa protokołu RTMP i kodery na żywo w usłudze Azure Media Services](https://go.microsoft.com/fwlink/?LinkId=532824).

    Ten krok można także wykonać po utworzeniu kanału.
2. Tworzenie i uruchamianie kanału.

3. Pobierz adres URL pozyskiwania kanału.

    Koder na żywo używa adresu URL pozyskiwania do wysłania strumienia do kanału.
4. Pobierz adres URL wersji zapoznawczej kanału.

    Użyj tego adresu URL, aby sprawdzić, czy kanał prawidłowo odbiera strumień na żywo.
5. Utwórz program.

    W przypadku korzystania z Azure Portal Tworzenie programu powoduje także utworzenie elementu zawartości.

    Korzystając z zestawu SDK platformy .NET lub REST, należy utworzyć element zawartości i określić, aby użyć tego elementu zawartości podczas tworzenia programu.
6. Opublikuj element zawartości skojarzony z programem.   

    >[!NOTE]
    >Po utworzeniu konta Azure Media Services zostanie do niego dodany **domyślny** punkt końcowy przesyłania strumieniowego w stanie **zatrzymanym** . Punkt końcowy przesyłania strumieniowego, z którego chcesz strumieniowo przesyłać zawartość, musi mieć stan **Uruchomiony**.

7. Uruchom program, gdy wszystko będzie gotowe do rozpoczęcia przesyłania strumieniowego i archiwizowania.

8. Opcjonalnie można przesłać do kodera na żywo sygnał o rozpoczęciu reklamy. Reklama jest wstawiana do strumienia wyjściowego.

9. Zatrzymaj program w dowolnym momencie, w którym chcesz zatrzymać przesyłanie strumieniowe i archiwizowanie wydarzenia.

10. Usuń program (opcjonalnie można również usunąć element zawartości).     

## <a name="description-of-a-channel-and-its-related-components"></a><a id="channel"></a>Opis kanału i powiązanych z nim składników
### <a name="channel-input-ingest-configurations"></a><a id="channel_input"></a>Konfiguracje wejścia kanału (pozyskiwania)
#### <a name="ingest-streaming-protocol"></a><a id="ingest_protocols"></a>Pozyskiwanie protokołu przesyłania strumieniowego
Media Services obsługuje pozyskiwanie dynamicznych kanałów informacyjnych przy użyciu dużej szybkości transmisji bitów w postaci plików MP4 i wieloszybkościowej transmisji bitów jako protokołów przesyłania strumieniowego. Po wybraniu protokołu przesyłania strumieniowego RTMP dla kanału zostanie utworzonych dwa punkty końcowe (wejściowe):

* **Podstawowy adres URL**: określa w pełni kwalifikowany adres URL podstawowego punktu końcowego protokołu RTMP w kanale.
* **Pomocniczy adres URL** (opcjonalnie): określa w pełni kwalifikowany adres URL pomocniczego punktu końcowego protokołu RTMP dla kanału.

Użyj pomocniczego adresu URL, jeśli chcesz poprawić trwałość i odporność na uszkodzenia strumienia pozyskiwania (a także tryb failover kodera i odporność na uszkodzenia), szczególnie w przypadku następujących scenariuszy:

- Jednokrotne wypychanie pojedynczego kodera do podstawowych i pomocniczych adresów URL:

    Głównym celem tego scenariusza jest zapewnienie większej odporności na fluktuacje i wahania sieci. Niektóre kodery RTMP nie obsługują dobrze połączeń sieci. Po rozłączeniu z siecią koder może zatrzymać kodowanie, a następnie nie wysyłać danych buforowanych, gdy nastąpi ponowne łączenie. Powoduje to przerwy i utratę danych. Połączenia sieci mogą wystąpić z powodu nieprawidłowej sieci lub konserwacji po stronie platformy Azure. Podstawowe/pomocnicze adresy URL zmniejszają problemy z siecią i zapewniają kontrolowany proces uaktualniania. Za każdym razem, gdy nastąpi rozłączenie zaplanowanej sieci, Media Services zarządza podstawową i pomocniczymi oddziałami i zapewnia opóźnione rozłączenie między nimi. Kodery następnie mają czas na zachowanie wysyłania danych i ponownego nawiązania połączenia. Kolejność rozłączeń może być Losowa, ale zawsze będzie można opóźnić między podstawowymi/pomocniczymi lub pomocniczymi i podstawowymi adresami URL. W tym scenariuszu koder jest nadal single point of failure.

- Wiele koderów, przy czym każdy koder wypychany do dedykowanego punktu:

    W tym scenariuszu przedstawiono zarówno koder, jak i nadmiarowość. W tym scenariuszu encoder1 wypchnięcie do podstawowego adresu URL, a encoder2 wypchnięcie do pomocniczego adresu URL. Gdy koder kończy się niepowodzeniem, inny koder może przechowywać dane. Nadmiarowość danych może być utrzymywana, ponieważ Media Services nie rozłącza podstawowych i pomocniczych adresów URL w tym samym czasie. W tym scenariuszu przyjęto założenie, że kodery są zsynchronizowane i zapewniają dokładnie te same dane.  

- Wielokrotne Przepchnij dwa kodery do podstawowych i pomocniczych adresów URL:

    W tym scenariuszu oba kodery wypychanie danych do podstawowych i pomocniczych adresów URL. Zapewnia to najlepszą niezawodność i odporność na uszkodzenia, a także nadmiarowość danych. W tym scenariuszu można tolerować zarówno błędy koderów, jak i rozłączać, nawet jeśli jeden koder przestanie działać. Przyjęto założenie, że kodery są zsynchronizowane i zapewniają dokładnie te same dane.  

Aby uzyskać informacje na temat koderów na żywo RTMP, zobacz [Azure Media Services obsługa protokołu RTMP i kodery na żywo](https://go.microsoft.com/fwlink/?LinkId=532824).

#### <a name="ingest-urls-endpoints"></a>Adresy URL pozyskiwania (punkty końcowe)
Kanał udostępnia wejściowy punkt końcowy (adres URL pozyskiwania) określony w koderze dynamicznym, dzięki czemu koder może wypchnąć strumienie do kanałów.   

Podczas tworzenia kanału można uzyskać adresy URL pozyskiwania. Aby uzyskać te adresy URL, kanał nie musi znajdować się w stanie **uruchomienia** . Gdy wszystko będzie gotowe do rozpoczęcia wypychania danych do kanału, kanał musi być w stanie **uruchomionym** . Po rozpoczęciu pobierania danych przez kanał można wyświetlić podgląd strumienia za pośrednictwem adresu URL w wersji zapoznawczej.

Istnieje możliwość pozyskiwania pofragmentowanego strumienia danych MP4 (Smooth Streaming) na żywo za pośrednictwem połączenia TLS. Aby przejąć protokół TLS, należy zaktualizować adres URL pozyskiwania do protokołu HTTPS. Obecnie nie można pozyskiwać protokołu RTMP przez protokół TLS.

#### <a name="keyframe-interval"></a><a id="keyframe_interval"></a>Interwał klatek kluczowych
Jeśli używasz lokalnego kodera na żywo do generowania strumienia o większej szybkości transmisji bitów, interwał klatek kluczowych określa czas trwania grupy obrazów (grupę GOP), który jest używany przez ten koder zewnętrzny. Gdy kanał odbiera ten strumień przychodzący, można dostarczyć strumień na żywo do aplikacji do odtwarzania klienta w jednym z następujących formatów: Smooth Streaming, dynamiczne adaptacyjne przesyłanie strumieniowe za pośrednictwem protokołu HTTP (PAUZ) i HTTP Live Streaming (HLS). Gdy wykonujesz przesyłanie strumieniowe na żywo, HLS jest zawsze spakowane dynamicznie. Domyślnie Media Services automatycznie oblicza współczynnik pakietów HLS segmentu (fragmenty na segment) w oparciu o interwał klatek kluczowych, który jest odbierany z kodera na żywo.

W poniższej tabeli przedstawiono sposób obliczania czasu trwania segmentu:

| Interwał klatek kluczowych | Współczynnik pakowania segmentu HLS (FragmentsPerSegment) | Przykład |
| --- | --- | --- |
| Mniejsze niż lub równe 3 sekundy |3:1 |Jeśli KeyFrameInterval (lub grupę GOP) wynosi 2 sekundy, domyślny współczynnik tworzenia pakietów segmentu HLS to 3 – 1. Spowoduje to utworzenie 6-sekundowego segmentu HLS. |
| od 3 do 5 sekund |2:1 |Jeśli KeyFrameInterval (lub grupę GOP) ma wartość 4 sekund, domyślny współczynnik tworzenia pakietów segmentu HLS to 2 – 1. Spowoduje to utworzenie 8-sekundowego segmentu HLS. |
| Więcej niż 5 sekund |1:1 |Jeśli KeyFrameInterval (lub grupę GOP) to 6 sekund, domyślny współczynnik tworzenia pakietów segmentu HLS wynosi od 1 do 1. Spowoduje to utworzenie 6-sekundowego segmentu HLS. |

Można zmienić współczynnik fragmentów dla segmentu przez skonfigurowanie danych wyjściowych kanału i ustawienie FragmentsPerSegment w ChannelOutputHls.

Możesz również zmienić wartość interwału klatek kluczowych, ustawiając właściwość KeyFrameInterval w ChannelInput. Jeśli jawnie ustawisz KeyFrameInterval, współczynnik pakowania segmentu HLS FragmentsPerSegment jest obliczany przez opisane wcześniej reguły.  

Jeśli jawnie ustawisz zarówno KeyFrameInterval, jak i FragmentsPerSegment, Media Services używa ustawionych wartości.

#### <a name="allowed-ip-addresses"></a>Dozwolone adresy IP
Można zdefiniować adresy IP, które mogą publikować wideo w tym kanale. Dozwolony adres IP można określić jako jedną z następujących wartości:

* Pojedynczy adres IP (na przykład 10.0.0.1)
* Zakres adresów IP, który używa adresu IP i maski podsieci CIDR (na przykład 10.0.0.1/22)
* Zakres adresów IP, który używa adresu IP i maski podsieci z kropką dziesiętną (na przykład 10.0.0.1 (255.255.252.0))

Jeśli nie określono adresów IP i nie ma definicji reguły, adres IP nie jest dozwolony. Aby zezwolić na jakikolwiek adres IP, utwórz regułę i ustaw wartość 0.0.0.0/0.

### <a name="channel-preview"></a>Podgląd kanału
#### <a name="preview-urls"></a>Adresy URL podglądu
Kanały zapewniają punkt końcowy (wersja zapoznawcza), który służy do wyświetlania podglądu i weryfikowania strumienia przed dalszem przetwarzaniem i dostarczaniem.

Możesz uzyskać adres URL wersji zapoznawczej podczas tworzenia kanału. Aby uzyskać adres URL, kanał nie musi znajdować się w stanie **uruchomionym** . Po rozpoczęciu pobierania danych przez kanał można wyświetlić podgląd strumienia.

Obecnie strumień w wersji zapoznawczej można dostarczyć tylko w pofragmentowanym formacie MP4 (Smooth Streaming), niezależnie od określonego typu danych wejściowych. Aby przetestować płynny strumień, można użyć odtwarzacza [monitora kondycji Smooth Streaming](https://playready.directtaps.net/smoothstreaming/) . Do wyświetlania strumienia można także użyć odtwarzacza hostowanego w Azure Portal.

#### <a name="allowed-ip-addresses"></a>Dozwolone adresy IP
Można zdefiniować adresy IP, które mogą łączyć się z punktem końcowym wersji zapoznawczej. Jeśli nie określono adresów IP, dozwolony jest dowolny adres IP. Dozwolony adres IP można określić jako jedną z następujących wartości:

* Pojedynczy adres IP (na przykład 10.0.0.1)
* Zakres adresów IP, który używa adresu IP i maski podsieci CIDR (na przykład 10.0.0.1/22)
* Zakres adresów IP, który używa adresu IP i maski podsieci z kropką dziesiętną (na przykład 10.0.0.1 (255.255.252.0))

### <a name="channel-output"></a>Dane wyjściowe kanału
Aby uzyskać informacje na temat danych wyjściowych kanału, zobacz sekcję [Interwał klatek kluczowych](#keyframe_interval) .

### <a name="channel-managed-programs"></a>Programy zarządzane przez kanał
Kanał jest skojarzony z programami, które umożliwiają kontrolowanie publikowania i przechowywania segmentów w strumieniu na żywo. Kanały zarządzają programami. Relacja kanału i programu jest podobna do tradycyjnego nośnika, gdzie kanał ma stały strumień zawartości, a program jest objęty zakresem pewnego czasu zdarzenia w tym kanale.

Można określić liczbę godzin, aby zachować zarejestrowaną zawartość na potrzeby programu przez ustawienie długości **Okna archiwum**. Ta wartość musi mieścić się w zakresie od 5 minut do maksymalnie 25 godzin. Długość okna archiwum określa również maksymalną liczbę czasu, przez jaką klienci mogą odwrócić w czasie od bieżącego stanowiska na żywo. Programy mogą być transmitowane w określonym czasie, ale zawartość, która wykracza poza długość okna, jest stale odrzucana. Wartość tej właściwości określa również, jak długie mogą być manifesty na kliencie.

Każdy program jest skojarzony z zasobem, który przechowuje zawartość przesyłaną strumieniowo. Element zawartości jest mapowany na kontener blokowych obiektów BLOB na koncie usługi Azure Storage, a pliki w elemencie zawartości są przechowywane jako obiekty blob w tym kontenerze. Aby opublikować program, aby klienci mogli wyświetlić strumień, należy utworzyć lokalizator OnDemand dla skojarzonego elementu zawartości. Ten lokalizator umożliwia utworzenie adresu URL przesyłania strumieniowego, który można udostępnić klientom.

Kanał obsługuje maksymalnie trzy współbieżnie uruchomione programy, dzięki czemu można tworzyć wiele archiwów tego samego strumienia przychodzącego. W razie konieczności można opublikować i zarchiwizować różne części zdarzenia. Załóżmy na przykład, że wymaganie biznesowe polega na zarchiwizowaniu 6 godzin programu, ale do emisji tylko przez ostatnich 10 minut. W tym celu należy utworzyć dwa jednocześnie uruchomione programy. Jeden program jest ustawiony do archiwizacji sześciu godzin zdarzenia, ale program nie jest publikowany. Drugi program jest ustawiony do archiwum przez 10 minut i ten program jest publikowany.

Nie należy używać ponownie istniejących programów dla nowych zdarzeń. Zamiast tego należy utworzyć nowy program dla każdego zdarzenia. Uruchom program, gdy wszystko będzie gotowe do rozpoczęcia przesyłania strumieniowego i archiwizowania. Zatrzymaj program w dowolnym momencie, w którym chcesz zatrzymać przesyłanie strumieniowe i archiwizowanie wydarzenia.

Aby usunąć zarchiwizowaną zawartość, Zatrzymaj i Usuń program, a następnie usuń skojarzony element zawartości. Nie można usunąć elementu zawartości, jeśli jest on wykorzystywany przez program. Najpierw należy usunąć program.

Nawet po zatrzymaniu i usunięciu programu użytkownicy będą mogli przesyłać strumieniowo zarchiwizowaną zawartość jako wideo na żądanie, dopóki nie usuniesz elementu zawartości. Jeśli chcesz zachować zarchiwizowaną zawartość, ale nie będzie ona dostępna do przesyłania strumieniowego, Usuń lokalizator przesyłania strumieniowego.

## <a name="channel-states-and-billing"></a><a id="states"></a>Stany kanału i rozliczenia
Możliwe wartości bieżącego stanu kanału to:

* **Zatrzymano**: jest to początkowy stan kanału po jego utworzeniu. W tym stanie właściwości kanału mogą być aktualizowane, ale przesyłanie strumieniowe jest niedozwolone.
* **Uruchamianie**: kanał jest uruchamiany. W tym stanie nie są dozwolone żadne aktualizacje ani przesyłanie strumieniowe. Jeśli wystąpi błąd, kanał powróci do stanu **zatrzymany** .
* **Uruchomiono**: kanał może przetwarzać strumienie na żywo.
* **Zatrzymywanie**: kanał jest zatrzymywany. W tym stanie nie są dozwolone żadne aktualizacje ani przesyłanie strumieniowe.
* **Usuwanie**: kanał jest usuwany. W tym stanie nie są dozwolone żadne aktualizacje ani przesyłanie strumieniowe.

W poniższej tabeli przedstawiono sposób, w jaki Stany kanałów mapują się do trybu rozliczania.

| Stan kanału | Wskaźniki interfejsu użytkownika portalu | Rozliczane? |
| --- | --- | --- |
| **Uruchamianie** |**Uruchamianie** |Nie (stan przejściowy) |
| **Uruchomienie** |**Gotowe** (brak uruchomionych programów)<p><p>lub<p>**Przesyłanie strumieniowe** (co najmniej jeden uruchomiony program) |Tak |
| **Zatrzymywanie** |**Zatrzymywanie** |Nie (stan przejściowy) |
| **Zatrzymano** |**Zatrzymano** |Nie |

## <a name="closed-captioning-and-ad-insertion"></a><a id="cc_and_ads"></a>Napisy i wstawianie AD
W poniższej tabeli przedstawiono obsługiwane standardy dla podpisów kodowanych i wstawiania do usługi AD.

| Standardowa (Standard) | Uwagi |
| --- | --- |
| CEA-708 i EIA-608 (708/608) |CEA-708 i EIA-608 to standardy zamykania podpisów dla Stany Zjednoczone i Kanady.<p><p>Obecnie podpisy są obsługiwane tylko wtedy, gdy są wykonywane w zakodowanym strumieniu wejściowym. Musisz użyć kodera multimediów na żywo, który może wstawić 608 lub 708 podpisy w zakodowanym strumieniu, który jest wysyłany do Media Services. Media Services dostarcza zawartość z wstawionymi napisami do Twoich przeglądających. |
| TTML wewnątrz. ISMT (Smooth Streaming ścieżek tekstowych) |Media Services dynamiczne pakowanie umożliwia klientom przesyłanie strumieniowe zawartości w jednym z następujących formatów: PAUZy, HLS lub Smooth Streaming. Jeśli jednak pozyskasz pofragmentowany plik MP4 (Smooth Streaming) z napisami wewnątrz. ISMT (Smooth Streaming ścieżki tekstowe), możesz dostarczyć strumień tylko do Smooth Streaming klientów. |
| SCTE-35 |SCTE-35 to system sygnałów cyfrowych, który służy do sygnalizowania wstawiania reklamowego. Odbiorniki podrzędne używają sygnału do łączenia reklamy ze strumieniem przez przydzielony czas. SCTE-35 musi być wysłany jako ścieżka rozrzedzona w strumieniu wejściowym.<p><p>Obecnie jedynym obsługiwanym formatem strumienia wejściowego, który ma sygnały usługi AD, jest fragmentacja MP4 (Smooth Streaming). Jedynym obsługiwanym formatem danych wyjściowych jest również Smooth Streaming. |

## <a name="considerations"></a><a id="considerations"></a>Zagadnienia do rozważenia
Jeśli używasz lokalnego kodera na żywo do wysyłania strumienia o większej szybkości transmisji bitów do kanału, mają zastosowanie następujące ograniczenia:

* Upewnij się, że masz wystarczającą liczbę bezpłatnych połączeń z Internetem, aby wysyłać dane do punktów pozyskiwania.
* Użycie dodatkowego adresu URL pozyskiwania wymaga dodatkowej przepustowości.
* Ruch przychodzący strumienia o różnej szybkości transmisji bitów może mieć maksymalnie 10 poziomów jakości wideo (warstw) i maksymalnie 5 ścieżek audio.
* Najwyższa średnia szybkość transmisji bitów dla dowolnego z poziomów jakości wideo powinna wynosić poniżej 10 MB/s.
* Zagregowana średnia szybkość transmisji bitów dla wszystkich strumieni wideo i audio powinna wynosić poniżej 25 MB/s.
* Nie można zmienić protokołu wejściowego, gdy kanał lub skojarzone z nim programy są uruchomione. Jeśli potrzebujesz różnych protokołów, utwórz osobny kanał dla każdego protokołu wejściowego.
* Można pozyskać pojedynczą szybkość transmisji bitów w kanale. Jednak ponieważ kanał nie przetwarza strumienia, aplikacje klienckie będą również otrzymywać strumienie o pojedynczej szybkości transmisji bitów. (Nie zalecamy tej opcji).

Poniżej przedstawiono inne zagadnienia związane z pracą z kanałami i powiązanymi składnikami:

* Za każdym razem, gdy ponownie konfigurujesz koder na żywo, wywołaj metodę **resetowania** kanału. Przed zresetowaniem kanału należy zatrzymać program. Po zresetowaniu kanału należy ponownie uruchomić program.

  > [!NOTE]
  > Po ponownym uruchomieniu programu należy skojarzyć go z nowym zasobem i utworzyć nowy lokalizator. 
  
* Kanał można zatrzymać tylko wtedy, gdy jest w stanie **uruchomienia** , a wszystkie programy w kanale zostały zatrzymane.
* Domyślnie do konta Media Services można dodać tylko pięć kanałów. Aby uzyskać więcej informacji, zobacz [przydziały i ograniczenia](media-services-quotas-and-limitations.md).
* Opłaty są naliczane tylko wtedy, gdy kanał jest w stanie **uruchomienia** . Aby uzyskać więcej informacji, zobacz sekcję [Stany kanału i rozliczenia](media-services-live-streaming-with-onprem-encoders.md#states) .

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="suggestions-and-feedback"></a>Sugestie i opinie

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Powiązane tematy
[Zalecane w przypadku koderów lokalnych](media-services-recommended-encoders.md)

[Azure Media Services fragmentacja — Specyfikacja pozyskiwania danych MP4](../media-services-fmp4-live-ingest-overview.md)

[Przegląd usługi Azure Media Services i typowe scenariusze](media-services-overview.md)

[Koncepcje Media Services](media-services-concepts.md)

[live-overview]: ./media/media-services-manage-channels-overview/media-services-live-streaming-current.png
