---
title: Omówienie przesyłania strumieniowego na żywo przy użyciu usługi Azure Media Services | Dokumenty firmy Microsoft
description: Ten artykuł zawiera omówienie przesyłania strumieniowego na żywo przy użyciu usługi Microsoft Azure Media Services.
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
ms.openlocfilehash: f875b4a5c4f1322f4a992dc3738ab1ce6431149d
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641120"
---
# <a name="overview-of-live-streaming-using-media-services"></a>Omówienie przesyłania strumieniowego na żywo przy użyciu usług multimedialnych

> [!NOTE]
> Do usługi Media Services w wersji 2 nie są już dodawane żadne nowe funkcje. <br/>Sprawdź najnowszą wersję usługi [Media Services w wersji 3](https://docs.microsoft.com/azure/media-services/latest/). Zobacz też [wskazówki dotyczące migracji z wersji 2 do v3](../latest/migrate-from-v2-to-v3.md)

## <a name="overview"></a>Omówienie

Podczas dostarczania zdarzeń przesyłania strumieniowego na żywo z usługi Azure Media Services często zaangażowane są następujące składniki:

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

Za pomocą usługi Media Services można korzystać z [dynamicznego pakowania,](media-services-dynamic-packaging-overview.md)które umożliwia transmisję transmisji na żywo w formatach MPEG DASH, HLS i Smooth Streaming z kanału składkowego, który jest wysyłany do usługi. Twoi widzowie mogą odtwarzać transmisję na żywo z dowolnymi graczami zgodnymi z HLS, DASH lub Smooth Streaming. Za pomocą programu Azure Media Player w aplikacjach sieci web lub aplikacjach mobilnych można dostarczać strumień w dowolnym z tych protokołów.

> [!NOTE]
> Od 12 maja 2018 r. kanały na żywo nie będą już obsługiwać protokołu pozyskiwania strumienia transportu RTP/MPEG-2. Przemiń z RTP/MPEG-2 do RTMP lub pofragmentowanych protokołów pozyskiwania MP4 (Smooth Streaming).

## <a name="streaming-endpoints-channels-programs"></a>Punkty końcowe przesyłania strumieniowego, kanały, programy

W usłudze Azure Media Services **kanały**, **programy**, i **punkty końcowe przesyłania strumieniowego** obsługują wszystkie funkcje transmisji strumieniowej na żywo, w tym pozyskiwanie, formatowanie, cyfrowy rejestrator wideo (DVR), zabezpieczenia, skalowalność i nadmiarowość.

**Kanał** reprezentuje potok przetwarzania zawartości transmisji strumieniowej na żywo. Kanał może odbierać przychodzące strumienie na żywo w następujący sposób:

* Lokalny koder na żywo wysyła plik **RTMP** o różnych szybkościach transmisji bitów lub plik **Smooth Streaming** (pofragmentowany plik MP4) do kanału, który jest skonfigurowany do obsługi dostarczania zawartości w formie **przekazywania**. Dostarczanie w formie **przekazywania** występuje wtedy, gdy pozyskiwane strumienie są przekazywane przez **kanał** bez dalszego przetwarzania. Można użyć następujących koderów na żywo, które wyprowadzają wielosytowe płynne przesyłanie strumieniowe: MediaExcel, Ateme, Imagine Communications, Envivio, Cisco i Elemental. Następujące enkodery na żywo wyjściowe RTMP: Telestream Wirecast, Haivision, Teradek transkodery.  Koder na żywo może także wysłać strumień o pojedynczej szybkości transmisji bitów do kanału, który nie obsługuje kodowania na żywo, nie jest to jednak zalecane. Po odebraniu żądania usługa Media Services dostarcza strumień do klientów.

  > [!NOTE]
  > Metoda przekazywania to najbardziej ekonomiczne rozwiązanie transmisji strumieniowej na żywo w przypadku organizowania wielu wydarzeń w długim okresie oraz poczynionych inwestycji w kodery lokalne. Zobacz szczegółowe informacje o [cenach](https://azure.microsoft.com/pricing/details/media-services/).
  > 
  > 
* Lokalny koder na żywo wysyła strumień o pojedynczej szybkości transmisji bitów do kanału, który jest włączony do wykonywania kodowania na żywo za pomocą usługi Media Services w jednym z następujących formatów: RTMP lub Smooth Streaming (pofragmentowany format MP4). Znane są następujące kodery na żywo z wyjściem RTMP do pracy z kanałami tego typu: Telestream Wirecast. Kanał wykonuje następnie kodowanie na żywo przychodzącego strumienia o pojedynczej szybkości transmisji bitów do postaci strumienia wideo o różnych szybkościach transmisji bitów (adaptacyjnej szybkości transmisji bitów). Po odebraniu żądania usługa Media Services dostarcza strumień do klientów.

Począwszy od wersji Media Services 2.10, podczas tworzenia kanału, można określić, w jaki sposób kanał ma odbierać strumień wejściowy i czy kanał ma wykonywać kodowanie na żywo strumienia. Dostępne są dwie opcje:

* **Brak** (pass-through) — określ tę wartość, jeśli planujesz używać lokalnego kodera na żywo, który będzie wyprowadzał strumień z szybkością wielu bitów (strumień przekazywany). W takim przypadku strumień przychodzący przeszedł do danych wyjściowych bez kodowania. Jest to zachowanie kanału przed wydaniem 2.10.  
* **Standardowy** — wybierz tę wartość, jeśli zamierzasz użyć usługi Media Services do zakodowania strumienia na żywo o pojedynczej szybkości transmisji bitów do strumienia o wielu szybkościach transmisji bitów. Ta metoda jest bardziej ekonomiczne skalowanie szybko dla rzadkich zdarzeń. Należy pamiętać, że istnieje wpływ rozliczeń dla kodowania na żywo i należy pamiętać, że pozostawienie kanału kodowania na żywo w stanie "Uruchomiony" spowoduje naliczenie opłat rozliczeniowych.  Zaleca się natychmiastowe zatrzymanie uruchamiania kanałów po zakończeniu transmisji na żywo, aby uniknąć dodatkowych opłat godzinowych.

## <a name="comparison-of-channel-types"></a>Porównanie typów kanałów

Poniższa tabela zawiera przewodnik po porównaniu dwóch typów kanałów obsługiwanych w programie Media Services

| Funkcja | Kanał przekazywalny | Kanał standardowy |
| --- | --- | --- |
| Pojedyncze dane wejściowe o szybkości transmisji bitów są kodowane do wielu szybkości transmisji bitów w chmurze |Nie |Tak |
| Maksymalna rozdzielczość, liczba warstw |1080p, 8 warstw, 60+fps |720p, 6 warstw, 30 kl./s |
| Protokoły wejściowe |RTMP, płynne przesyłanie strumieniowe |RTMP, płynne przesyłanie strumieniowe |
| Price |Zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/media-services/) i kliknij kartę "Wideo na żywo" |Zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/media-services/) |
| Maksymalny czas wykonywania |24x7 |8 godzin |
| Obsługa wstawiania łupków |Nie |Tak |
| Obsługa sygnalizacji reklam |Nie |Tak |
| Napisy pass-through CEA 608/708 |Tak |Tak |
| Obsługa nieujemnych wejściowych gopów |Tak |Nie – wejście musi być ustalone 2sec GOPs |
| Obsługa wprowadzania zmiennej liczby klatek na sekundę |Tak |Nie – wejście musi być ustaloną szybkością klatek na sekundę.<br/>Niewielkie różnice są tolerowane, na przykład podczas scen o dużym ruchu. Ale koder nie może upuścić do 10 klatek na sekundę. |
| Automatyczne wyłączania kanałów po utracie źródła danych wejściowych |Nie |Po 12 godzinach, jeśli nie ma uruchomionego programu |

## <a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders-pass-through"></a>Praca z kanałami odbierającymi strumień na żywo o różnych szybkościach transmisji bitów z koderów lokalnych (przekazujących)

Na poniższym diagramie przedstawiono główne elementy platformy AMS, które są zaangażowane w przepływ pracy **przekazywania**.

![Przepływ pracy na żywo](./media/media-services-live-streaming-workflow/media-services-live-streaming-current.png)

Aby uzyskać więcej informacji, zobacz temat [Praca z kanałami odbierającymi strumień na żywo o różnych szybkościach transmisji bitów z koderów lokalnych](media-services-live-streaming-with-onprem-encoders.md).

## <a name="working-with-channels-that-are-enabled-to-perform-live-encoding-with-azure-media-services"></a>Praca z kanałami obsługującymi kodowanie na żywo za pomocą usługi Azure Media Services

Na poniższym diagramie przedstawiono główne elementy platformy AMS biorące udział w przepływie pracy transmisji strumieniowej na żywo, gdy kanał obsługuje kodowanie na żywo za pomocą usługi Media Services.

![Przepływ pracy na żywo](./media/media-services-live-streaming-workflow/media-services-live-streaming-new.png)

Aby uzyskać więcej informacji, zobacz temat [Praca z kanałami obsługującymi funkcję Live Encoding w usłudze Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

## <a name="description-of-a-channel-and-its-related-components"></a>Opis kanału i powiązanych z nim składników

### <a name="channel"></a>Channel

W umywce usługi Media Services [kanały](https://docs.microsoft.com/rest/api/media/operations/channel)są odpowiedzialne za przetwarzanie zawartości przesyłanych strumieniowo na żywo. Kanał zawiera wejściowy punkt końcowy (adres URL pozyskiwania), który następnie należy podać transkoderowi na żywo. Kanał odbiera transmisje wejściowe na żywo z transkodera na żywo i udostępnia go do przesyłania strumieniowego za pośrednictwem jednego lub więcej Punktów StreamingEndpoints. Kanały zapewniają również punkt końcowy w wersji zapoznawczej (adres URL w wersji zapoznawczej), którego używasz do wyświetlania podglądu i sprawdzania poprawności strumienia przed dalszym przetwarzaniem i dostarczaniem.

Podczas tworzenia kanału możesz uzyskać adres URL pozyskiwania i adres URL podglądu. Aby uzyskać te adresy URL, kanał nie musi być w stanie uruchomionym. Gdy będziesz gotowy, aby rozpocząć wypychanie danych z transkodera na żywo do kanału, kanał musi zostać uruchomiony. Gdy transkoder na żywo zacznie pojejeć dane, możesz wyświetlić podgląd strumienia.

Każde konto usługi Media Services może zawierać wiele kanałów, wiele programów i wiele punktów StreamingEndpoints. W zależności od potrzeb przepustowości i zabezpieczeń usługi StreamingEndpoint mogą być dedykowane do jednego lub więcej kanałów. Każdy StreamingEndpoint można wyciągnąć z dowolnego kanału.

Podczas tworzenia kanału można określić dozwolone adresy IP w jednym z następujących formatów: adres IpV4 z 4 numerami, zakres adresów CIDR.

### <a name="program"></a>Program
[Program](https://docs.microsoft.com/rest/api/media/operations/program) umożliwia sterowanie publikowaniem i przechowywaniem segmentów w strumieniu na żywo. Kanały zarządzają programami. Relacja kanału i programu jest bardzo podobna do relacji w tradycyjnych multimediach, gdzie kanał ma stały strumień zawartości, a program obejmuje niektóre zdarzenia czasowe na tym kanale.
Można określić liczbę godzin przechowywania zarejestrowanej zawartości programu, ustawiając właściwość **ArchiveWindowLength.** Ta wartość musi mieścić się w zakresie od 5 minut do maksymalnie 25 godzin.

ArchiveWindowLength dyktuje również maksymalną ilość czasu, przez jaki klienci mogą szukać w czasie z bieżącej pozycji na żywo. Programy mogą być transmitowane w określonym czasie, ale zawartość, która wykracza poza długość okna, jest stale odrzucana. Wartość tej właściwości określa również, jak długo manifesty klienta mogą rosnąć.

Każdy program jest skojarzony z zasobem. Aby opublikować program, należy utworzyć lokalizator dla skojarzonego zasobu. Lokalizator umożliwia utworzenie adresu URL przesyłania strumieniowego, który można udostępnić klientom.

Kanał obsługuje maksymalnie trzy jednocześnie uruchomione programy, dzięki czemu można tworzyć wiele archiwów tego samego strumienia przychodzącego. Umożliwia to w razie potrzeby publikowanie i archiwizację różnych części wydarzenia. Na przykład zgodnie z wymaganiami biznesowymi potrzebna jest archiwizacja sześciu godzin programu, ale do emisji przeznaczonych jest tylko ostatnich 10 minut. W tym celu należy utworzyć dwa jednocześnie uruchomione programy. Jeden z programów jest skonfigurowany na potrzeby archiwizacji sześciu godzin zdarzenia, ale ten program nie jest publikowany. Drugi z programów jest skonfigurowany w celu archiwizacji 10 minut wydarzenia i ten program zostanie opublikowany.

## <a name="billing-implications"></a>Implikacje rozliczeń
Kanał rozpoczyna rozliczenia, jak tylko zostanie przesunie się do stanu "Uruchomiony" za pośrednictwem interfejsu API.  

W poniższej tabeli pokazano, jak stany kanału mapują stany rozliczeń w interfejsie API i witrynie Azure portal. Należy zauważyć, że stany różnią się nieco między interfejsem API i ux portalu. Jak tylko kanał jest w stanie "Uruchamianie" za pośrednictwem interfejsu API lub w stanie "Gotowe" lub "Przesyłanie strumieniowe" w witrynie Azure portal, rozliczenia będą aktywne.

Aby zatrzymać kanał od rozliczeń dalej, należy zatrzymać kanał za pośrednictwem interfejsu API lub w witrynie Azure portal.
Użytkownik jest odpowiedzialny za zatrzymanie kanałów po zakończeniu pracy z kanałem. Niezatrzymanie kanału spowoduje dalsze rozliczenia.

> [!NOTE]
> Podczas pracy z kanałami standardowymi program AMS automatycznie wyłącza dowolny kanał, który jest nadal w stanie "Uruchomiony" 12 godzin po utracie źródła danych wejściowych i nie ma uruchomionych programów. Jednak nadal będą naliczane za czas, w jakim kanał był w stanie "Uruchomiony".
>
>

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

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Powiązane tematy
[Specyfikacja pochłoniętych plików MP4 w usłudze AZURE Media Services](../media-services-fmp4-live-ingest-overview.md)

[Praca z kanałami, które są włączone do wykonywania kodowania na żywo za pomocą usługi Azure Media Services](media-services-manage-live-encoder-enabled-channels.md)

[Praca z kanałami odbierającymi strumień na żywo o różnych szybkościach transmisji bitów z koderów lokalnych](media-services-live-streaming-with-onprem-encoders.md)

[Przydziały i ograniczenia](media-services-quotas-and-limitations.md).  

[Pojęcia dotyczące usług multimedialnych](media-services-concepts.md)
