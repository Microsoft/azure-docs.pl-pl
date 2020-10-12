---
title: Omówienie przesyłania strumieniowego na żywo przy użyciu Azure Media Services | Microsoft Docs
description: Ten artykuł zawiera omówienie przesyłania strumieniowego na żywo przy użyciu Microsoft Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: fb63502e-914d-4c1f-853c-4a7831bb08e8
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: eb508831d7a10537f27bb5b4e55f3a0627ce1f3c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89265971"
---
# <a name="overview-of-live-streaming-using-media-services"></a>Omówienie przesyłania strumieniowego na żywo przy użyciu Media Services

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Do usługi Media Services w wersji 2 nie są już dodawane żadne nowe funkcje. <br/>Zapoznaj się z najnowszą wersją [Media Services wersja 3](../latest/index.yml). Zobacz też [wskazówki dotyczące migracji od wersji 2 do V3](../latest/migrate-from-v2-to-v3.md)

## <a name="overview"></a>Omówienie

W przypadku dostarczania zdarzeń przesyłania strumieniowego na żywo za pomocą Azure Media Services często są wykorzystywane następujące składniki:

* Kamera, która umożliwia emisję wydarzenia.
* Koder wideo na żywo, który konwertuje sygnały z kamery na strumienie wysyłane do usługi transmisji strumieniowej na żywo.

    Opcjonalnie wiele zsynchronizowanych czasowo koderów na żywo. W przypadku niektórych wydarzeń na żywo o kluczowym znaczeniu, które wymagają bardzo wysokiej dostępności i jakości obsługi, zaleca się wdrożenie nadmiarowych koderów w trybie aktywny/aktywny z synchronizacją czasu w celu uzyskania płynnego trybu failover bez utraty danych.
* Usługa transmisji strumieniowej na żywo, która umożliwia wykonanie następujących czynności:

  * pozyskiwanie zawartości na żywo przy użyciu różnych protokołów transmisji strumieniowej na żywo (np. RTMP lub Smooth Streaming);
  * (opcjonalnie) kodowanie strumienia do strumienia o adaptacyjnej szybkości transmisji bitów;
  * wyświetlanie podglądu transmisji strumieniowej na żywo;
  * rejestrowanie i przechowywanie pozyskiwanej zawartości w celu późniejszego przesłania jej strumieniowo (wideo na żądanie);
  * dostarczanie zawartości za pośrednictwem wspólnych protokołów przesyłania strumieniowego (np. MPEG DASH, Smooth, HLS) bezpośrednio do klientów lub do usługi Content Delivery Network (CDN) w celu dalszej dystrybucji.

Usługa **Microsoft Azure Media Services** (AMS) zapewnia możliwość pozyskiwania, kodowania, podglądu, przechowywania i dostarczania transmisji strumieniowej zawartości na żywo.

Za pomocą Media Services można korzystać z funkcji [dynamicznego tworzenia pakietów](media-services-dynamic-packaging-overview.md), która umożliwia emitowanie strumieni na żywo w postaci łączników MPEG, HLS i Smooth Streaming z kanału informacyjnego, który jest wysyłany do usługi. Osoby przeglądające mogą odtworzyć strumień na żywo za pomocą dowolnych HLS, ŁĄCZNIKów lub Smooth Streaming zgodnych graczy. Możesz użyć Azure Media Player w aplikacjach sieci Web lub aplikacji mobilnych, aby dostarczyć strumień do dowolnego z tych protokołów.

> [!NOTE]
> Od 12 maja 2018 kanały na żywo nie będą już obsługiwać protokołu pozyskiwania strumienia transportowego RTP/MPEG-2. Przeprowadź migrację z protokołów RTP/MPEG-2 do protokołu RTMP lub pofragmentowanych plików MP4 (Smooth Streaming).

## <a name="streaming-endpoints-channels-programs"></a>Punkty końcowe przesyłania strumieniowego, kanały, programy

W usłudze Azure Media Services **kanały**, **programy**, i **punkty końcowe przesyłania strumieniowego** obsługują wszystkie funkcje transmisji strumieniowej na żywo, w tym pozyskiwanie, formatowanie, cyfrowy rejestrator wideo (DVR), zabezpieczenia, skalowalność i nadmiarowość.

**Kanał** reprezentuje potok przetwarzania zawartości transmisji strumieniowej na żywo. Kanał może odbierać przychodzące strumienie na żywo w następujący sposób:

* Lokalny koder na żywo wysyła plik **RTMP** o różnych szybkościach transmisji bitów lub plik **Smooth Streaming** (pofragmentowany plik MP4) do kanału, który jest skonfigurowany do obsługi dostarczania zawartości w formie **przekazywania**. Dostarczanie w formie **przekazywania** występuje wtedy, gdy pozyskiwane strumienie są przekazywane przez **kanał** bez dalszego przetwarzania. Można użyć następujących koderów na żywo, które wychodzące z wieloszybkościowej transmisji bitów Smooth Streaming: MediaExcel, ATEME, Wyobraź Communications, Envivio, Cisco i element. Następujące kodery na żywo wyprowadzają RTMP: Telestream Wirecast, Haivision, Teradek transkodery.  Koder na żywo może także wysłać strumień o pojedynczej szybkości transmisji bitów do kanału, który nie obsługuje kodowania na żywo, nie jest to jednak zalecane. Po odebraniu żądania usługa Media Services dostarcza strumień do klientów.

  > [!NOTE]
  > Metoda przekazywania to najbardziej ekonomiczne rozwiązanie transmisji strumieniowej na żywo w przypadku organizowania wielu wydarzeń w długim okresie oraz poczynionych inwestycji w kodery lokalne. Zobacz szczegółowe informacje o [cenach](https://azure.microsoft.com/pricing/details/media-services/).
  > 
  > 
* Lokalny koder na żywo wysyła strumień o pojedynczej szybkości transmisji bitów do kanału, w którym włączono obsługę kodowania na żywo za pomocą Media Services w jednym z następujących formatów: RTMP lub Smooth Streaming (fragmentacja MP4). Następujące kodery na żywo z danymi wyjściowymi RTMP są znane do pracy z kanałami tego typu: Telestream Wirecast. Kanał wykonuje następnie kodowanie na żywo przychodzącego strumienia o pojedynczej szybkości transmisji bitów do postaci strumienia wideo o różnych szybkościach transmisji bitów (adaptacyjnej szybkości transmisji bitów). Po odebraniu żądania usługa Media Services dostarcza strumień do klientów.

Począwszy od wersji 2,10 Media Services, podczas tworzenia kanału można określić, w jaki sposób chcesz, aby kanał odbierał strumień wejściowy i czy chcesz, aby kanał wykonywał kodowanie na żywo strumienia. Dostępne są dwie opcje:

* **Brak** (przekazanie) — Określ tę wartość, jeśli planujesz używać lokalnego kodera na żywo, który będzie wyprowadzać strumień o szybkości transmisji bitów (strumień Pass-through). W tym przypadku strumień przychodzący przeszedł do danych wyjściowych bez kodowania. Jest to zachowanie kanału przed wydaniem 2,10.  
* **Standardowa** — wybierz tę wartość, jeśli planujesz używać Media Services do kodowania strumienia na żywo o pojedynczej szybkości transmisji bitów do strumienia o większej szybkości transmisji bitów. Ta metoda jest bardziej ekonomiczna w przypadku szybkiego skalowania w górę w przypadku rzadko występujących zdarzeń. Należy pamiętać, że istnieje wpływ rozliczania na kodowanie na żywo i należy pamiętać, że pozostawienie kanału kodowania na żywo w stanie "uruchomiona" spowoduje naliczenie opłat naliczanych.  Zaleca się natychmiastowe zatrzymanie uruchomionych kanałów po zakończeniu zdarzenia przesyłania strumieniowego na żywo, aby uniknąć dodatkowych opłat godzinowych.

## <a name="comparison-of-channel-types"></a>Porównanie typów kanałów

Poniższa tabela zawiera Przewodnik porównujący dwa typy kanałów obsługiwane w Media Services

| Cecha | Kanał Pass-through | Kanał standardowy |
| --- | --- | --- |
| Dane wejściowe o pojedynczej szybkości transmisji bitów są zakodowane w wielu szybkościach transmisji bitów w chmurze |Nie |Tak |
| Rozdzielczość maksymalna, liczba warstw |1080p, 8 warstw, 60 klatek na sekundę |720, 6 warstw, 30 fps |
| Protokoły wejściowe |RTMP, Smooth Streaming |RTMP, Smooth Streaming |
| Cena |Zobacz [stronę cennika](https://azure.microsoft.com/pricing/details/media-services/) i kliknij kartę "wideo na żywo" |Zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/media-services/) |
| Maksymalny czas działania |24x7 |8 godzin |
| Obsługa wstawiania przednich |Nie |Tak |
| Obsługa sygnalizowania AD |Nie |Tak |
| Podpisy CEA 608/708 |Tak |Tak |
| Obsługa niejednorodnych danych wejściowych GOPs |Tak |Nie — dane wejściowe muszą być stałe 2sec GOPs |
| Obsługa danych wejściowych stawki ramki zmiennej |Tak |Nie — dane wejściowe muszą być stałym wskaźnikiem ramki.<br/>Niewielkie wahania są tolerowane, na przykład podczas wysokiego poziomu ruchów. Jednak koder nie może porzucić 10 klatek na sekundę. |
| Autoshutoff kanałów, gdy źródło danych wejściowych jest tracone |Nie |Po upływie 12 godzin, jeśli nie jest uruchomiony żaden program |

## <a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders-pass-through"></a>Praca z kanałami odbierającymi strumień na żywo o różnych szybkościach transmisji bitów z koderów lokalnych (przekazujących)

Na poniższym diagramie przedstawiono główne elementy platformy AMS, które są zaangażowane w przepływ pracy **przekazywania**.

![Przepływ pracy na żywo](./media/media-services-live-streaming-workflow/media-services-live-streaming-current.png)

Aby uzyskać więcej informacji, zobacz temat [Praca z kanałami odbierającymi strumień na żywo o różnych szybkościach transmisji bitów z koderów lokalnych](media-services-live-streaming-with-onprem-encoders.md).

## <a name="working-with-channels-that-are-enabled-to-perform-live-encoding-with-azure-media-services"></a>Praca z kanałami obsługującymi kodowanie na żywo za pomocą usługi Azure Media Services

Na poniższym diagramie przedstawiono główne elementy platformy AMS biorące udział w przepływie pracy transmisji strumieniowej na żywo, gdy kanał obsługuje kodowanie na żywo za pomocą usługi Media Services.

![Przepływ pracy na żywo](./media/media-services-live-streaming-workflow/media-services-live-streaming-new.png)

Aby uzyskać więcej informacji, zobacz temat [Praca z kanałami obsługującymi funkcję Live Encoding w usłudze Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

## <a name="description-of-a-channel-and-its-related-components"></a>Opis kanału i powiązanych z nim składników

### <a name="channel"></a>Kanał

W Media Services [kanał](/rest/api/media/operations/channel)s jest odpowiedzialny za przetwarzanie zawartości przesyłania strumieniowego na żywo. Kanał udostępnia wejściowy punkt końcowy (adres URL pozyskiwania), który następnie można przekazać do transkodera na żywo. Kanał odbiera strumienie danych wejściowych na żywo z transkodera dynamicznego i udostępnia je do przesyłania strumieniowego za pośrednictwem co najmniej jednego StreamingEndpointsu. Kanały udostępniają również punkt końcowy (wersja zapoznawcza) służący do wyświetlania podglądu i weryfikowania strumienia przed dalszem przetwarzaniem i dostarczaniem.

Podczas tworzenia kanału można uzyskać adres URL pozyskiwania i adres URL wersji zapoznawczej. Aby uzyskać te adresy URL, kanał nie musi znajdować się w stanie uruchomienia. Gdy wszystko będzie gotowe do rozpoczęcia wypychania danych z transkodera na żywo do kanału, należy uruchomić kanał. Gdy transkoder na żywo zacznie pozyskiwanie danych, można wyświetlić podgląd strumienia.

Każde konto Media Services może zawierać wiele kanałów, wiele programów i wiele StreamingEndpoints. W zależności od potrzeb dotyczących przepustowości i zabezpieczeń usługi StreamingEndpoint Services mogą być dedykowane dla jednego lub kilku kanałów. Każdy StreamingEndpoint może pobierać z dowolnego kanału.

Podczas tworzenia kanału można określić dozwolone adresy IP w jednym z następujących formatów: adres IpV4 z 4 numerami, zakres adresów CIDR.

### <a name="program"></a>Program
[Program](/rest/api/media/operations/program) umożliwia kontrolowanie publikowania i przechowywania segmentów w strumieniu na żywo. Kanały zarządzają programami. Relacja kanału i programu jest bardzo podobna do relacji w tradycyjnych multimediach, gdzie kanał ma stały strumień zawartości, a program obejmuje niektóre zdarzenia czasowe na tym kanale.
Możesz określić liczbę godzin, przez którą ma zostać zachowana zarejestrowana zawartość dla programu, ustawiając właściwość **ArchiveWindowLength** . Ta wartość musi mieścić się w zakresie od 5 minut do maksymalnie 25 godzin.

ArchiveWindowLength określa również maksymalną ilość czasu, jaką klienci mogą odszukać w czasie z bieżącej pozycji na żywo. Programy mogą być transmitowane w określonym czasie, ale zawartość, która wykracza poza długość okna, jest stale odrzucana. Wartość tej właściwości określa również, jak długo mogą się zwiększać manifesty klienta.

Każdy program jest skojarzony z zasobem. Aby opublikować program, należy utworzyć lokalizator dla skojarzonego elementu zawartości. Lokalizator umożliwia utworzenie adresu URL przesyłania strumieniowego, który można udostępnić klientom.

Kanał obsługuje maksymalnie trzy jednocześnie uruchomione programy, dzięki czemu można tworzyć wiele archiwów tego samego strumienia przychodzącego. Umożliwia to w razie potrzeby publikowanie i archiwizację różnych części wydarzenia. Na przykład zgodnie z wymaganiami biznesowymi potrzebna jest archiwizacja sześciu godzin programu, ale do emisji przeznaczonych jest tylko ostatnich 10 minut. W tym celu należy utworzyć dwa jednocześnie uruchomione programy. Jeden z programów jest skonfigurowany na potrzeby archiwizacji sześciu godzin zdarzenia, ale ten program nie jest publikowany. Drugi z programów jest skonfigurowany w celu archiwizacji 10 minut wydarzenia i ten program zostanie opublikowany.

## <a name="billing-implications"></a>Implikacje rozliczeń
Kanał rozpoczyna rozliczanie od razu po zmianie stanu na "uruchomiony" za pośrednictwem interfejsu API.  

W poniższej tabeli przedstawiono sposób, w jaki Stany kanałów mapują się na Stany rozliczeń w interfejsie API i Azure Portal. Należy pamiętać, że Stany różnią się nieco między interfejsem użytkownika interfejsu API i portalu. Gdy tylko kanał jest w stanie "uruchomiony" za pośrednictwem interfejsu API lub w stanie "gotowe" lub "streaming" w Azure Portal, rozliczenia będą aktywne.

Aby zatrzymać rozliczanie kanału od Ciebie, należy zatrzymać kanał za pośrednictwem interfejsu API lub Azure Portal.
Użytkownik jest odpowiedzialny za zatrzymywanie kanałów po zakończeniu pracy z kanałem. Niepowodzenie zatrzymania kanału spowoduje dalsze rozliczanie.

> [!NOTE]
> Podczas pracy z kanałami standardowymi, AMS będzie ShutOff dowolny kanał, który nadal jest w stanie "uruchomiony" 12 godzin po utracie danych wejściowych i nie ma uruchomionych programów. Jednak nadal będą naliczane opłaty za czas działania kanału w stanie "uruchomiony".
>
>

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

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Powiązane tematy
[Azure Media Services pofragmentowana Specyfikacja pozyskiwania na żywo w formacie MP4](../media-services-fmp4-live-ingest-overview.md)

[Praca z kanałami obsługującymi Live Encoding z Azure Media Services](media-services-manage-live-encoder-enabled-channels.md)

[Praca z kanałami odbierającymi strumień na żywo o różnych szybkościach transmisji bitów z koderów lokalnych](media-services-live-streaming-with-onprem-encoders.md)

[Limity przydziału i ograniczenia](media-services-quotas-and-limitations.md).  

[Koncepcje Media Services](media-services-concepts.md)
