---
title: Azure Media Services pofragmentowana Specyfikacja pozyskiwania na żywo w formacie MP4 | Microsoft Docs
description: Ta specyfikacja zawiera opis protokołu i formatu dla fragmentacji pozyskiwania strumieniowego na żywo opartego na formacie MP4 dla Azure Media Services. W tym dokumencie omówiono także najlepsze rozwiązania związane z tworzeniem wysoce nadmiarowych i niezawodnych mechanizmów pozyskiwania na żywo.
services: media-services
documentationcenter: ''
author: cenkdin
manager: femila
editor: ''
ms.assetid: 43fac263-a5ea-44af-8dd5-cc88e423b4de
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 7323ae611431e1d91fd1a8471914be388fcc4712
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92019515"
---
# <a name="azure-media-services-fragmented-mp4-live-ingest-specification"></a>Azure Media Services pofragmentowana Specyfikacja pozyskiwania na żywo w formacie MP4 

Ta specyfikacja zawiera opis protokołu i formatu dla fragmentacji pozyskiwania strumieniowego na żywo opartego na formacie MP4 dla Azure Media Services. Media Services udostępnia usługę przesyłania strumieniowego na żywo, której klienci mogą używać do przesyłania strumieniowego wydarzeń na żywo i emisji w czasie rzeczywistym przy użyciu platformy Azure jako platformy w chmurze. W tym dokumencie omówiono także najlepsze rozwiązania związane z tworzeniem wysoce nadmiarowych i niezawodnych mechanizmów pozyskiwania na żywo.

## <a name="1-conformance-notation"></a>1. notacja zgodności
Kluczowe słowa "musi," "nie może być," "wymagane," "," "nie mogą," "," "," "nie powinno być," "zalecane," "," i "opcjonalne" w tym dokumencie są interpretowane jako opisane w dokumencie RFC 2119.

## <a name="2-service-diagram"></a>2. diagram usług
Na poniższym diagramie przedstawiono architekturę wysokiego poziomu usługi przesyłania strumieniowego na żywo w Media Services:

1. Koder na żywo wypychanie kanałów informacyjnych na żywo do kanałów, które są tworzone i obsługiwane za pośrednictwem zestawu SDK Azure Media Services.
1. Kanały, programy i punkty końcowe przesyłania strumieniowego w Media Services obsługują wszystkie funkcje przesyłania strumieniowego na żywo, w tym pozyskiwanie, formatowanie, DVR z chmury, zabezpieczenia, skalowalność i nadmiarowość.
1. Opcjonalnie klienci mogą wybrać wdrożenie warstwy Content Delivery Network platformy Azure między punktem końcowym przesyłania strumieniowego i punktami końcowymi klienta.
1. Strumień punktów końcowych klienta z punktu końcowego przesyłania strumieniowego przy użyciu protokołu HTTP adaptacyjnego przesyłania strumieniowego. Przykładami mogą być Microsoft Smooth Streaming, dynamiczne adaptacyjne przesyłanie strumieniowe za pośrednictwem protokołu HTTP (KRESKi lub MPEG) oraz Apple HTTP Live Streaming (HLS).

![przepływ pozyskiwania][image1]

## <a name="3-bitstream-format--iso-14496-12-fragmented-mp4"></a>3. format Bitstream — obraz ISO 14496-12 pofragmentowany
Format sieci na potrzeby pozyskiwania strumieniowego na żywo omówiony w niniejszym dokumencie jest oparty na [ISO-14496-12]. Aby uzyskać szczegółowy opis pofragmentowanego formatu MP4 i rozszerzeń dla plików wideo na żądanie i pozyskiwania strumieniowego na żywo, zobacz [[MS-SSTR]](/openspecs/windows_protocols/ms-sstr/8383f27f-7efe-4c60-832a-387274457251).

### <a name="live-ingest-format-definitions"></a>Definicje formatu pozyskiwania na żywo
Na poniższej liście opisano specjalne definicje formatu, które mają zastosowanie do pozyskiwania na żywo w Azure Media Services:

1. Pola **ftyp**, **manifestu serwera Live** i **Moov** muszą być wysyłane przy użyciu każdego żądania (post protokołu HTTP). Te pola muszą być wysyłane na początku strumienia i za każdym razem, gdy koder musi ponownie nawiązać połączenie, aby wznowić pozyskiwanie strumienia. Aby uzyskać więcej informacji, zobacz sekcję 6 w [1].
1. Sekcja 3.3.2 w [1] definiuje opcjonalne pole o nazwie **StreamManifestBox** for Live pozyskiwania. Ze względu na logikę routingu modułu równoważenia obciążenia platformy Azure, korzystanie z tego pola jest przestarzałe. Pole nie powinno być obecne podczas pozyskiwania do Media Services. Jeśli to pole jest obecne, Media Services dyskretnie zignoruje je.
1. Pole **TrackFragmentExtendedHeaderBox** zdefiniowane w 3.2.3.2 w [1] musi być obecne dla każdego fragmentu.
1. W celu wygenerowania segmentów multimediów, które mają identyczne adresy URL w wielu centrach danych, należy użyć wersji 2 pola **TrackFragmentExtendedHeaderBox** . Pole indeks fragmentu jest wymagane w przypadku międzycentrowego przełączania do trybu failover opartych na indeksach formatów przesyłania strumieniowego, takich jak Apple HLS i MPEG-KRESKa. Aby włączyć tryb failover między centrami danych, indeks fragmentu musi być synchronizowany w wielu koderach i zwiększony o 1 dla każdego kolejnego fragmentu nośnika, nawet w przypadku ponownego uruchomienia lub awarii kodera.
1. Sekcja 3.3.6 w [1] definiuje pole o nazwie **MovieFragmentRandomAccessBox** (**mfra**), które może zostać wysłane na końcu pozyskiwania na żywo w celu wskazania końca strumienia (EOS) do kanału. Ze względu na logikę pozyskiwania Media Services, użycie EOS jest przestarzałe i nie należy wysyłać pola **mfra** na potrzeby pozyskiwania na żywo. W przypadku wysłania Media Services dyskretnie zignoruje ją. Aby zresetować stan punktu pozyskiwania, zalecamy użycie [resetowania kanału](/rest/api/media/operations/channel#reset_channels). Zalecane jest również, aby zakończyć prezentację i strumień przy użyciu [Zatrzymaj program](/rest/api/media/operations/program#stop_programs) .
1. Czas trwania fragmentu MP4 powinien być stały, aby zmniejszyć rozmiar manifestów klientów. Stały czas trwania fragmentu MP4 poprawia również algorytmy pobierania heurystycznych przez użycie tagów powtarzających. Czas trwania może ulec wahaniu w celu zrekompensowania stawek za ramki niebędące liczbami całkowitymi.
1. Czas trwania fragmentu MP4 powinien wynosić od około 2 do 6 sekund.
1. Sygnatury czasowe fragmentarycznego fragmentu i indeksy (**TrackFragmentExtendedHeaderBox** `fragment_ absolute_ time` i `fragment_index` ) powinny dotrzeć do rosnącej kolejności. Chociaż Media Services jest odporny na zduplikowane fragmenty, ma ograniczoną możliwość zmiany kolejności fragmentów w zależności od osi czasu nośnika.

## <a name="4-protocol-format--http"></a>4. format protokołu — HTTP
Pakiet ISO pozyskiwania na żywo pozyskiwania na podstawie plików MP4 dla Media Services używa standardowego długotrwałego żądania HTTP POST do przesyłania danych zakodowanych w postaci pofragmentowanej do usługi. Każde polecenie HTTP POST wysyła kompletny pofragmentowany plik MP4 Bitstream ("Stream"), zaczynając od początku z polami nagłówka (**ftyp**, **Live Server manifest** i **Moov** Box) i kontynuując sekwencję fragmentów (pola **moof** i **MDAT** ). Aby poznać składnię adresu URL żądania HTTP POST, zobacz sekcję 9,2 w [1]. Przykładem adresu URL wpisu jest: 

`http://customer.channel.mediaservices.windows.net/ingest.isml/streams(720p)`

### <a name="requirements"></a>Wymagania
Poniżej przedstawiono szczegółowe wymagania:

1. Koder powinien rozpocząć emitowanie, wysyłając żądanie HTTP POST z pustą "treścią" (zero długości zawartości) przy użyciu tego samego adresu URL pozyskiwania. Może to pomóc koderowi szybko wykryć, czy punkt końcowy pozyskiwania na żywo jest prawidłowy i czy wymagane są jakiekolwiek uwierzytelnianie lub inne warunki. Dla protokołu HTTP serwer nie może wysłać odpowiedzi HTTP do momentu otrzymania całego żądania, w tym treści wpisu. Uwzględniając długotrwały charakter zdarzenia na żywo bez tego kroku, koder może nie być w stanie wykryć żadnego błędu, dopóki nie zakończy się wysyłanie wszystkich danych.
1. Koder musi obsługiwać wszelkie błędy lub wyzwania związane z uwierzytelnianiem z powodu (1). Jeśli (1) powiedzie się z odpowiedzią 200, Kontynuuj.
1. Koder musi rozpocząć nowe żądanie HTTP POST ze pofragmentowanym strumieniem MP4. Ładunek musi rozpoczynać się od pól nagłówka, a następnie fragmentów. Należy pamiętać, że pola **ftyp**, " **manifestu serwera Live**" i **Moov** (w tej kolejności) muszą być wysyłane przy użyciu każdego żądania, nawet jeśli koder musi ponownie nawiązać połączenie, ponieważ poprzednie żądanie zostało przerwane przed końcem strumienia. 
1. Koder musi używać kodowania transferu fragmentarycznego na potrzeby przekazywania, ponieważ nie jest możliwe przewidywalność całej długości zawartości zdarzenia na żywo.
1. Gdy zdarzenie jest przekroczenia, po wysłaniu ostatniego fragmentu koder musi bezpiecznie zakończyć sekwencję komunikatów kodowania transferu fragmentarycznego (większość stosów klienta HTTP obsługuje ją automatycznie). Koder musi poczekać, aż usługa zwróci końcowy kod odpowiedzi, a następnie zakończy połączenie. 
1. Koder nie może używać `Events()` rzeczownika zgodnie z opisem w artykule 9,2 w [1] na potrzeby pozyskiwania na żywo w Media Services.
1. Jeśli żądanie POST protokołu HTTP kończy się lub zostanie przerwane z powodu błędu TCP przed końcem strumienia, koder musi wydać nowe żądanie POST przy użyciu nowego połączenia i postępować zgodnie z wcześniejszymi wymaganiami. Ponadto koder musi ponownie wysłać poprzednie dwa fragmenty MP4 dla każdej ścieżki w strumieniu i wznowić bez przedstawiania ciągłości na osi czasu multimediów. Ponowne wysłanie ostatnich dwóch fragmentów plików MP4 dla każdej ścieżki gwarantuje, że nie utracisz danych. Innymi słowy, jeśli strumień zawiera dźwięk i ścieżkę wideo, a bieżące żądanie POST nie powiedzie się, koder musi ponownie nawiązać połączenie i ponownie wysłać ostatnie dwa fragmenty dla ścieżki audio, które zostały wcześniej pomyślnie wysłane, oraz ostatnie dwa fragmenty dla ścieżki wideo, które zostały wcześniej pomyślnie wysłane, aby upewnić się, że nie ma utraty danych. Koder musi obsługiwać bufor "do przodu" fragmentów nośnika, który jest wysyłany ponownie po ponownym nawiązaniu połączenia.

## <a name="5-timescale"></a>5. Skala czasu
[[MS-SSTR]](/openspecs/windows_protocols/ms-sstr/8383f27f-7efe-4c60-832a-387274457251) opisuje użycie skali czasu dla **SmoothStreamingMedia** (sekcja 2.2.2.1), **strumieńelement** (sekcja 2.2.2.3), **StreamFragmentElement** (sekcja 2.2.2.6) i **LiveSMIL** (sekcja 2.2.7.3.1). Jeśli wartość skali czasu nie jest obecna, użyta wartość domyślna to 10 000 000 (10 MHz). Chociaż specyfikacja formatu Smooth Streaming nie blokuje użycia innych wartości skali czasu, Większość implementacji koderów używa tej wartości domyślnej (10 MHz) do generowania Smooth Streaming danych pozyskiwania. Ze względu na funkcję [dynamicznego tworzenia pakietów usługi Azure Media](./previous/media-services-dynamic-packaging-overview.md) zalecamy użycie skali czasu 90-kHz dla strumieni wideo i 44,1 khz lub 48,1 kHz dla strumieni audio. Jeśli różne wartości skali czasu są używane dla różnych strumieni, należy wysłać skalę czasu na poziomie strumienia. Aby uzyskać więcej informacji, zobacz [[MS-SSTR]](/openspecs/windows_protocols/ms-sstr/8383f27f-7efe-4c60-832a-387274457251).     

## <a name="6-definition-of-stream"></a>6. Definicja "Stream"
Stream to podstawowa jednostka operacji pozyskiwania na żywo na potrzeby tworzenia prezentacji na żywo, obsługi trybu failover przesyłania strumieniowego i scenariuszy nadmiarowości. Strumień jest definiowany jako jeden unikatowy, pofragmentowany plik MP4 Bitstream, który może zawierać jedną ścieżkę lub wiele ścieżek. Pełna prezentacja na żywo może zawierać co najmniej jeden strumień, w zależności od konfiguracji koderów na żywo. Poniższe przykłady ilustrują różne opcje używania strumieni do redagowania pełnej prezentacji na żywo.

**Przykład:** 

Klient chce utworzyć prezentację przesyłania strumieniowego na żywo, która obejmuje następujące szybkości transmisji audio/wideo:

Wideo – 3000 KB/s, 1500 KB/s, 750 KB/s

Audio – 128 kb/s

### <a name="option-1-all-tracks-in-one-stream"></a>Opcja 1: wszystkie ścieżki w jednym strumieniu
W przypadku tej opcji pojedynczy koder generuje wszystkie ścieżki audio/wideo, a następnie umieszcza je w jednym pofragmentowanym Bitstream MP4. Pofragmentowany plik MP4 Bitstream jest następnie wysyłany za pośrednictwem jednego połączenia HTTP POST. W tym przykładzie istnieje tylko jeden strumień dla tej prezentacji na żywo.

![Strumienie — jedna ścieżka][image2]

### <a name="option-2-each-track-in-a-separate-stream"></a>Opcja 2: każda ścieżka w osobnym strumieniu
W przypadku tej opcji koder umieszcza jedną ścieżkę do każdego fragmentu MP4 Bitstream, a następnie zapisuje wszystkie strumienie za pośrednictwem oddzielnych połączeń HTTP. Można to zrobić za pomocą jednego kodera lub z wieloma koderami. Pozyskiwanie na żywo widzi tę prezentację na żywo, która składa się z czterech strumieni.

![Strumienie — oddzielne ścieżki][image3]

### <a name="option-3-bundle-audio-track-with-the-lowest-bitrate-video-track-into-one-stream"></a>Opcja 3: łączenie ścieżki audio z najniższą szybkością transmisji bitów wideo w jednym strumieniu
W przypadku tej opcji klient zdecyduje się na podzielenie ścieżki audio o najniższej szybkości transmisji bitów w jednym fragmencie plików MP4 Bitstream i pozostawienie pozostałych dwóch ścieżek wideo jako oddzielnych strumieni. 

![Strumienie — ścieżki audio i wideo][image4]

### <a name="summary"></a>Podsumowanie
Nie jest to pełna lista wszystkich możliwych opcji pozyskiwania w tym przykładzie. W rzeczywistości każde grupowanie ścieżek do strumieni jest obsługiwane przez pozyskiwanie na żywo. Klienci i dostawcy koderów mogą wybierać własne implementacje w oparciu o złożoność inżynierów, pojemność kodera oraz zagadnienia związane z nadmiarowością i pracą w trybie failover. Jednak w większości przypadków istnieje tylko jedna ścieżka audio dla całej prezentacji na żywo. Dlatego ważne jest, aby zapewnić healthiness strumienia pozyskiwania, który zawiera ścieżkę audio. Takie zagadnienie często polega na umieszczeniu ścieżki audio we własnym strumieniu (jak w opcji 2) lub umieszczeniu jej w ścieżce wideo o najniższej szybkości transmisji bitów (jak w przypadku opcji 3). Ponadto w celu zapewnienia lepszej nadmiarowości i odporności na uszkodzenia, wysłanie tej samej ścieżki audio w dwóch różnych strumieniach (opcja 2 z nadmiarowymi ścieżkami audio) lub zgrupowanie ścieżki audio z co najmniej dwoma ścieżkami wideo o najmniejszej szybkości transmisji bitów (opcja 3 z dźwiękiem w co najmniej dwóch strumieniach wideo) jest zdecydowanie zalecane do pozyskiwania na żywo w Media Services.

## <a name="7-service-failover"></a>7. Usługa przełączania do trybu failover
Ze względu na charakter przesyłania strumieniowego na żywo dobre wsparcie w trybie failover ma kluczowe znaczenie dla zapewnienia dostępności usługi. Media Services jest zaprojektowana do obsługi różnych typów błędów, w tym błędów sieci, błędów serwera i problemów z magazynem. W połączeniu z właściwą logiką trybu failover na stronie kodera na żywo klienci mogą uzyskać wysoce niezawodną usługę przesyłania strumieniowego na żywo z chmury.

W tej sekcji omówiono scenariusze pracy awaryjnej usługi. W takim przypadku awaria występuje w obrębie usługi i jest w niej manifestuje się jako błąd sieciowy. Poniżej przedstawiono kilka zaleceń dotyczących implementacji kodera w celu obsługi trybu failover usługi:

1. Aby ustanowić połączenie TCP, użyj 10-sekundowego limitu czasu. Jeśli próba nawiązania połączenia trwa dłużej niż 10 sekund, Przerwij operację i spróbuj ponownie. 
1. Użyj krótkiego limitu czasu na potrzeby wysyłania fragmentów komunikatów żądania HTTP. Jeśli docelowy czas trwania fragmentu MP4 wynosi N sekund, Użyj limitu czasu wysyłania między N i 2 N sekund; na przykład jeśli czas trwania fragmentu MP4 wynosi 6 sekund, Użyj limitu czasu wynoszącego od 6 do 12 sekund. W przypadku wystąpienia limitu czasu Zresetuj połączenie, Otwórz nowe połączenie i Wznów pobieranie strumienia przy użyciu nowego połączenia. 
1. Obsługuj bufor kroczący, który ma ostatnie dwa fragmenty dla każdej ścieżki, które zostały pomyślnie i całkowicie wysłane do usługi.  Jeśli żądanie HTTP POST dla strumienia zostało przerwane lub przestanie obowiązywać przed końcem strumienia, Otwórz nowe połączenie i Rozpocznij inne żądanie HTTP POST, Wyślij ponownie nagłówki strumienia, ponownie Wyślij ostatnie dwa fragmenty dla każdej ścieżki i Wznów przesyłanie strumienia bez zapewnienia ciągłości na osi czasu nośnika. Zmniejsza to ryzyko utraty danych.
1. Firma Microsoft zaleca, aby koder nie ograniczał liczby ponownych prób nawiązania połączenia lub wznowienia przesyłania strumieniowego po wystąpieniu błędu TCP.
1. Po błędzie protokołu TCP:
  
    a. Bieżące połączenie musi być zamknięte i należy utworzyć nowe połączenie dla nowego żądania HTTP POST.

    b. Nowy adres URL POST protokołu HTTP musi być taki sam jak początkowy adres URL.
  
    c. Nowy wpis HTTP musi zawierać nagłówki strumienia (**ftyp**, **pole manifestu serwera Live** i **Moov** ), które są identyczne z NAGŁÓWKAmi strumienia w początkowym wpisie.
  
    d. Ostatnie dwa fragmenty wysyłane dla każdej ścieżki muszą zostać wysłane ponownie, a przesyłanie strumieniowe musi zostać wznowione bez przedstawiania ciągłości na osi czasu multimediów. Sygnatury czasowe fragmentów MP4 muszą stale wzrastać, nawet w przypadku żądań POST protokołu HTTP.
1. Koder powinien kończyć żądanie HTTP POST, jeśli dane nie są wysyłane z szybkością proporcjonalną do czasu trwania fragmentu MP4.  Żądanie HTTP POST, które nie wysyła danych, może uniemożliwić Media Services szybkie odłączenie od kodera w przypadku aktualizacji usługi. Z tego powodu ścieżki HTTP POST for rozrzedzone (AD Signal) powinny być krótkie, kończące się zaraz po wysłaniu fragmentu rozrzedzenia.

## <a name="8-encoder-failover"></a>8. koder trybu failover
Tryb failover kodera to drugi typ scenariusza trybu failover, który musi zostać rozkierowany do kompleksowego dostarczania strumieniowego na żywo. W tym scenariuszu warunek błędu występuje po stronie kodera. 

![Tryb failover kodera][image5]

Poniższe oczekiwania dotyczą punktu końcowego pozyskiwania na żywo, gdy występuje tryb failover programu Encoder:

1. NALEŻY utworzyć nowe wystąpienie kodera, aby kontynuować przesyłanie strumieniowe, jak pokazano na diagramie (Stream for 3000k Video, z linią kreskowaną).
1. Nowy koder musi używać tego samego adresu URL dla żądań POST protokołu HTTP jako wystąpienia zakończonego niepowodzeniem.
1. Żądanie POST nowego kodera musi zawierać te same pofragmentowane pola nagłówka MP4 jako wystąpienie zakończone niepowodzeniem.
1. Nowy koder musi być poprawnie zsynchronizowany ze wszystkimi innymi uruchomionymi koderami dla tej samej prezentacji na żywo w celu generowania zsynchronizowanych próbek audio/wideo z wyrównanymi granicami fragmentu.
1. Nowy strumień musi być semantycznie odpowiednikiem poprzedniego strumienia i można go zamiennie zmieniać na poziomach nagłówka i fragmentu.
1. Nowy koder powinien próbować zminimalizować utratę danych. `fragment_absolute_time` `fragment_index` Fragmenty nośników i powinny wzrosnąć od punktu, w którym koder został ostatnio zatrzymany. `fragment_absolute_time`I `fragment_index` powinno zwiększyć się w sposób ciągły, ale w razie potrzeby można wprowadzić nieprzerwanie. Media Services ignoruje fragmenty, które zostały już odebrane i przetworzone, więc lepszym rozwiązaniem jest błąd po stronie ponownego wysyłania fragmentów niż w celu wprowadzenia zaniechania na osi czasu multimediów. 

## <a name="9-encoder-redundancy"></a>9. nadmiarowość kodera
W przypadku niektórych krytycznych wydarzeń na żywo, które wymagają jeszcze wyższej dostępności i jakości obsługi, zalecamy użycie funkcji aktywne-aktywne nadmiarowe kodery w celu zapewnienia bezproblemowej pracy w trybie failover bez utraty danych.

![nadmiarowość kodera][image6]

Jak pokazano na tym diagramie, dwie grupy koderów wypychane są dwie kopie każdego strumienia jednocześnie do usługi Live. Ta konfiguracja jest obsługiwana, ponieważ Media Services może odfiltrować zduplikowane fragmenty na podstawie identyfikatora strumienia i sygnatury czasowej fragmentacji. Otrzymany strumień na żywo i archiwum to jedna kopia wszystkich strumieni, które są najlepszą możliwą agregacją z dwóch źródeł. Na przykład w hipotetycznym skrajnym przypadku, o ile istnieje jeden koder (nie musi to być ten sam) uruchomiony w danym punkcie w czasie dla każdego strumienia, wynikowy strumień na żywo z usługi jest ciągły bez utraty danych. 

Wymagania dotyczące tego scenariusza są prawie takie same jak wymagania w przypadku "przełączania do trybu failover dla kodera", z wyjątkiem tego, że drugi zestaw koderów jest uruchomiony w tym samym czasie co koder podstawowy.

## <a name="10-service-redundancy"></a>10. nadmiarowość usługi
W przypadku wysoce nadmiarowej dystrybucji globalnej czasami trzeba mieć kopię zapasową między regionami, aby obsługiwać regionalne awarie. Powiększanie w topologii "nadmiarowość kodera" pozwala klientom wybrać nadmiarowe wdrożenie usługi w innym regionie, który jest połączony z drugim zestawem koderów. Klienci mogą również współdziałać z dostawcą Content Delivery Network, aby wdrożyć globalne Traffic Manager przed dwoma wdrożeniami usług, aby bezproblemowo kierować ruch klientów. Wymagania dotyczące koderów są takie same jak w przypadku "nadmiarowości kodera". Jedyny wyjątek polega na tym, że drugi zestaw koderów musi być wskazany w innym punkcie końcowym pozyskiwania na żywo. Na poniższym diagramie przedstawiono tę konfigurację:

![nadmiarowość usługi][image7]

## <a name="11-special-types-of-ingestion-formats"></a>11. specjalne typy formatów pozyskiwania
W tej sekcji omówiono specjalne typy formatów pozyskiwania na żywo, które są przeznaczone do obsługi określonych scenariuszy.

### <a name="sparse-track"></a>Ścieżka rozrzedzona
Podczas dostarczania prezentacji przesyłania strumieniowego na żywo z rozbudowanym doświadczeniem klienta często konieczne jest przesyłanie zdarzeń zsynchronizowanych czasowo lub Sygnalizowanie w paśmie przy użyciu głównych danych nośnika. Przykładem jest dynamiczne Wstawianie AD na żywo. Ten typ sygnalizowania zdarzeń różni się od zwykłego przesyłania strumieniowego audio/wideo ze względu na jego rozrzedzony charakter. Innymi słowy, dane sygnalizujące zwykle nie są wykonywane w sposób ciągły, a interwał może być trudny do przewidywania. Koncepcja ścieżki rozrzedzonej została zaprojektowana w celu pozyskiwania i emitowania danych sygnalizacyjnych w paśmie.

Następujące kroki są zalecaną implementacją do pozyskiwania ścieżki rozrzedzonej:

1. Utwórz oddzielną pofragmentowaną Bitstream MP4, która zawiera tylko ścieżki rozrzedzone bez ścieżek audio/wideo.
1. W **polu manifestu serwera aktywnego** , zgodnie z definicją w sekcji 6 w [1], użyj parametru *parentTrackName* , aby określić nazwę ścieżki nadrzędnej. Aby uzyskać więcej informacji, zobacz sekcję 4.2.1.2.1.2 w [1].
1. W **polu manifest serwera na żywo** **manifestOutput** musi mieć ustawioną **wartość true**.
1. Mając na względzie rozrzedzony charakter zdarzenia sygnalizującego, zalecamy wykonanie następujących czynności:
   
    a. Na początku zdarzenia na żywo Koder wysyła do usługi początkowe pola nagłówka, co umożliwia usłudze rejestrowanie ścieżki rozrzedzonej w manifeście klienta.
   
    b. Koder powinien kończyć żądanie HTTP POST, gdy dane nie są wysyłane. Długotrwały wpis HTTP, który nie wysyła danych, może uniemożliwić Media Services szybkie odłączenie od kodera w przypadku aktualizacji usługi lub ponownego uruchomienia serwera. W takich przypadkach serwer multimediów jest tymczasowo blokowany w operacji odbierania w gnieździe.
   
    c. W czasie, gdy dane sygnalizujące nie są dostępne, koder powinien zamknąć żądanie HTTP POST. Gdy żądanie POST jest aktywne, koder powinien wysyłać dane.

    d. W przypadku wysyłania fragmentów rozrzedzonych koder może ustawić jawny nagłówek Content-Length, jeśli jest dostępny.

    e. Podczas wysyłania fragmentów rozrzedzonych przy użyciu nowego połączenia koder powinien zacząć wysyłać z pól nagłówka, po których następuje nowe fragmenty. Dzieje się tak w przypadku, gdy nastąpi przejście w tryb failover między programem i nowe połączenie rozrzedzone jest ustanawiane na nowym serwerze, który nie widział ścieżki rozrzedzonej przed.

    f. Fragment ścieżki rozrzedzonej jest dostępny dla klienta, gdy odpowiedni fragment ścieżki nadrzędnej, który ma taką samą lub większą wartość sygnatury czasowej, jest udostępniany klientowi. Na przykład, jeśli fragmentacja rozrzedzona ma sygnaturę czasową t = 1000, oczekuje się, że po wyświetleniu przez klienta "wideo" (przy założeniu, że nazwa ścieżki nadrzędnej to "wideo"), znacznik czasu jest równy 1000 lub więcej, może pobrać fragment rozrzedzenia t = 1000. Należy zauważyć, że rzeczywisty sygnał może być używany dla innego położenia na osi czasu prezentacji dla określonego celu. W tym przykładzie istnieje możliwość, że fragment rozrzedzony t = 1000 ma ładunek XML, który służy do wstawiania reklamy w położeniu kilku sekund później.

    przykład Ładunek fragmentów ścieżki rozrzedzonej może znajdować się w różnych formatach (takich jak XML, text lub Binary), w zależności od scenariusza.

### <a name="redundant-audio-track"></a>Nadmiarowa ścieżka audio
W typowym scenariuszu przesyłania strumieniowego HTTP (na przykład Smooth Streaming lub PAUZy) często istnieje tylko jedna ścieżka audio w całej prezentacji. W przeciwieństwie do ścieżek wideo, które mają wiele poziomów jakości dla klienta do wyboru w warunkach błędów, ścieżka audio może być single point of failure, jeśli pozyskiwanie strumienia zawierającego ścieżkę audio zostało przerwane. 

Aby rozwiązać ten problem, Media Services obsługuje pozyskiwanie na żywo nadmiarowych ścieżek audio. Pomysłem jest, że ta sama ścieżka audio może być wysyłana wielokrotnie w różnych strumieniach. Mimo że usługa rejestruje ścieżkę dźwiękową tylko raz w manifeście klienta, może używać nadmiarowych ścieżek audio jako kopii zapasowych do pobierania fragmentów audio, jeśli podstawowa ścieżka audio ma problemy. Aby pozyskać nadmiarowe ścieżki audio, koder musi:

1. Utwórz tę samą ścieżkę audio dla wielu fragmentów bitstreams MP4. Nadmiarowe ścieżki audio muszą być semantycznie równoważne, z tą samą sygnaturą czasową fragmentu i mogą być zamienne na poziomach nagłówka i fragmentu.
1. Upewnij się, że wpis "audio" w manifeście serwera na żywo (sekcja 6 w [1]) jest taki sam dla wszystkich nadmiarowych ścieżek audio.

W przypadku nadmiarowych ścieżek audio zalecana jest następująca implementacja:

1. Wyślij każdą unikatową ścieżkę audio w strumieniu. Ponadto Wyślij nadmiarowy strumień dla każdego z tych strumieni ścieżki audio, gdzie drugi strumień różni się od pierwszego tylko identyfikatorem w adresie URL POST protokołu HTTP: {Protocol}://{Server Address}/{Publishing Path =/Streams ({Identifier}).
1. Użyj oddzielnych strumieni do wysłania dwóch najniższych szybkości transmisji wideo. Każdy z tych strumieni powinien również zawierać kopię każdej unikatowej ścieżki audio. Na przykład jeśli obsługiwane są wiele języków, te strumienie powinny zawierać ścieżki audio dla każdego języka.
1. Użyj oddzielnych wystąpień serwera (koder), aby kodować i wysyłać nadmiarowe strumienie wymienione w (1) i (2). 

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Wyraź opinię
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

[image1]: ./media/media-services-fmp4-live-ingest-overview/media-services-image1.png
[image2]: ./media/media-services-fmp4-live-ingest-overview/media-services-image2.png
[image3]: ./media/media-services-fmp4-live-ingest-overview/media-services-image3.png
[image4]: ./media/media-services-fmp4-live-ingest-overview/media-services-image4.png
[image5]: ./media/media-services-fmp4-live-ingest-overview/media-services-image5.png
[image6]: ./media/media-services-fmp4-live-ingest-overview/media-services-image6.png
[image7]: ./media/media-services-fmp4-live-ingest-overview/media-services-image7.png