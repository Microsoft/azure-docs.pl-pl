---
title: Azure Media Services typy LiveEvent
description: W Azure Media Services wydarzenie na żywo można ustawić na jedno *-* lub *kodowanie na żywo*. W tym artykule przedstawiono szczegółową tabelę, która porównuje typy zdarzeń na żywo.
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
ms.openlocfilehash: 3126cbf4f9d87e9baa7267e309210b751a47069d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98897802"
---
# <a name="live-event-types-comparison"></a>Porównanie typów zdarzeń na żywo

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

W Azure Media Services wydarzenie na  [żywo](/rest/api/media/liveevents) można ustawić na *przekazywanie* (lokalny koder na żywo wysyła strumień o wielokrotnej szybkości transmisji bitów) lub *kodowanie na żywo* (lokalny koder na żywo wysyła strumień o pojedynczej szybkości transmisji bitów). 

W tym artykule porównano funkcje typów zdarzeń na żywo.

## <a name="types-comparison"></a>Porównanie typów 

Poniższa tabela zawiera porównanie funkcji typów zdarzeń na żywo. Typy są ustawiane podczas tworzenia przy użyciu [LiveEventEncodingType](/rest/api/media/liveevents/create#liveeventencodingtype):

* **LiveEventEncodingType. None** — lokalny koder na żywo wysyła strumień o wielokrotnej szybkości transmisji bitów. Pozyskiwane strumienie są przekazywane przez wydarzenie na żywo bez dalszej obróbki. Nazywana również zdarzeniem przekazującym.
* **LiveEventEncodingType. Standard** — lokalny koder na żywo wysyła strumień o pojedynczej szybkości transmisji bitów do zdarzenia na żywo, a Media Services tworzy wiele strumieni szybkości transmisji bitów. Jeśli kanał informacyjny udziału ma rozdzielczość 720 lub wyższą, ustawienia wstępne **Default720p** zakodują zestaw par rozdzielczości i szybkości transmisji bitów (szczegóły znajdują się w dalszej części artykułu).
* **LiveEventEncodingType. Premium1080p** — lokalny koder na żywo wysyła strumień o pojedynczej szybkości transmisji bitów do zdarzenia na żywo, a Media Services tworzy wiele strumieni szybkości transmisji bitów. Ustawienie wstępne Default1080p określa zestaw danych wyjściowych par rozdzielczości/szybkości transmisji bitów (szczegóły znajdują się w dalszej części artykułu). 

| Cecha | Zdarzenie przekazywania na żywo | Standardowe lub Premium1080p zdarzenie na żywo |
| --- | --- | --- |
| Dane wejściowe o pojedynczej szybkości transmisji bitów są zakodowane w wielu szybkościach transmisji bitów w chmurze |Nie |Tak |
| Maksymalna rozdzielczość wideo dla kanału informacyjnego udziału |4K (4096x2160 o 60 klatek/s) |1080p (1920x1088 o 30 klatkach/s)|
| Zalecana maksymalna liczba warstw w strumieniu wkładu|Do 12|Jeden dźwięk|
| Maksymalna liczba warstw w danych wyjściowych| Analogicznie jak dane wejściowe|Do 6 (Zobacz poniższe ustawienia wstępne systemu)|
| Maksymalna agregowana przepustowość kanału informacyjnego udziału|60 MB/s|Nie dotyczy|
| Maksymalna szybkość transmisji bitów dla pojedynczej warstwy w udziale |20 MB/s|20 MB/s|
| Obsługa wielu ścieżek audio z wieloma językami|Tak|Nie|
| Obsługiwane wejściowe kodery-dekoder wideo |H. 264/AVC i H. 265/HEVC|H. 264/AVC|
| Obsługiwane wyjściowe kodery-dekoder wideo|Analogicznie jak dane wejściowe|H. 264/AVC|
| Obsługiwana głębia bitowa wideo, dane wejściowe i wyjściowe|Do 10 bitów włącznie z HDR 10/HLG|8-bitowa|
| Obsługiwane wejściowe kodery-dekoder audio|AAC-LC, IT-AAC V1, IT-AAC v2|AAC-LC, IT-AAC V1, IT-AAC v2|
| Obsługiwane wyjściowe kodery-dekoder audio|Analogicznie jak dane wejściowe|AAC — LC|
| Maksymalna rozdzielczość wideo dla wyjściowego wideo|Analogicznie jak dane wejściowe|Standard-720, Premium1080p-1080p|
| Maksymalna szybkość klatek dla wejściowego wideo|60 klatek na sekundę|Standardowe lub Premium1080p — 30 klatek na sekundę|
| Protokoły wejściowe|RTMP, pofragmentowane — MP4 (Smooth Streaming)|RTMP, pofragmentowane — MP4 (Smooth Streaming)|
| Cena|Zobacz [stronę cennika](https://azure.microsoft.com/pricing/details/media-services/) i kliknij kartę "wideo na żywo"|Zobacz [stronę cennika](https://azure.microsoft.com/pricing/details/media-services/) i kliknij kartę "wideo na żywo"|
| Maksymalny czas działania| 24 godz. x 365 dni, na żywo liniowe | 24 godz. x 365 dni, na żywo liniowe (wersja zapoznawcza)|
| Możliwość przekazywania osadzonych podpisów CEA 608/708|Tak|Tak|
| Możliwość włączenia transkrypcji na żywo|Tak|Tak|
| Obsługa wstawiania przednich|Nie|Nie|
| Obsługa sygnałów usługi AD za pośrednictwem interfejsu API| Nie|Nie|
| Obsługa sygnałów usługi AD przy użyciu komunikatów SCTE-35 w paśmie|Tak|Tak|
| Możliwość odzyskania od krótkich miejsc w źródle wkładu|Tak|Częściowe|
| Obsługa niejednorodnych danych wejściowych GOPs|Tak|Nie — dane wejściowe muszą mieć stały czas trwania grupę GOP|
| Obsługa danych wejściowych stawki ramki zmiennej|Tak|Nie — dane wejściowe muszą być stałym wskaźnikiem ramki. Niewielkie wahania są tolerowane, na przykład podczas wysokiego poziomu ruchów. Ale kanał informacyjny udziału nie może porzucić szybkości klatek (na przykład do 15 klatek na sekundę).|
| Automatyczny ShutOff zdarzenia na żywo, gdy źródło danych wejściowych zostanie utracone|Nie|Po upływie 12 godzin, jeśli nie ma LiveOutput uruchomionego|

## <a name="system-presets"></a>Ustawienia wstępne systemu

Rozdzielczości i szybkości transmisji bitów zawarte w danych wyjściowych z kodera na żywo są określane przez [ustawienie wstępnename](/rest/api/media/liveevents/create#liveeventencoding). W przypadku korzystania ze **standardowego** kodera na żywo (LiveEventEncodingType. Standard) *Default720p* ustawienie wstępne określa zestaw 6 par rozdzielczości/szybkości transmisji bitów opisanych poniżej. W przeciwnym razie, jeśli używany jest koder **Premium1080p** na żywo (LiveEventEncodingType. Premium1080p), a następnie ustawienie wstępne *Default1080p* określa wyjściowy zestaw par rozdzielczości/szybkości transmisji bitów.

> [!NOTE]
> Nie można zastosować ustawienia wstępnego Default1080p dla zdarzenia na żywo, jeśli został on skonfigurowany dla standardowego kodowania na żywo — zostanie wyświetlony komunikat o błędzie. Zostanie również wyświetlony komunikat o błędzie w przypadku próby zastosowania ustawienia wstępnego Default720p do Premium1080p Live Encoder.

### <a name="output-video-streams-for-default720p"></a>Wyjściowe strumienie wideo dla Default720p

Jeśli kanał informacyjny udziału ma rozdzielczość 720 lub wyższą, ustawienia wstępne **Default720p** zakodowania źródła strumieniowego do następujących 6 warstw. W poniższej tabeli szybkość transmisji bitów jest w KB/s, MaxFPS reprezentuje maksymalną dozwoloną liczbę klatek (w klatkach/sekundę), profil reprezentuje używany profil H. 264.

| Multimedia | Width | Height | MaxFPS | Profil |
| --- | --- | --- | --- | --- |
| 3500 |1280 |720 |30 |Wys. |
| 2200 |960 |540 |30 |Wys. |
| 1350 |704 |396 |30 |Wys. |
| 850 |512 |288 |30 |Wys. |
| 550 |384 |216 |30 |Wys. |
| 200 |340 |192 |30 |Wys. |

> [!NOTE]
> Jeśli musisz dostosować ustawienie wstępne kodowania na żywo, Otwórz bilet pomocy technicznej za pośrednictwem witryny Azure Portal. Należy określić pożądaną tabelę rozdzielczości wideo i szybkości transmisji bitów. Dostosowanie szybkości transmisji bitów audio nie jest obsługiwane. Sprawdź, czy istnieje tylko jedna warstwa dla rozdzielczości 720p i maksymalnie 6 warstw. Należy również określić żądanie wstępne.

### <a name="output-video-streams-for-default1080p"></a>Wyjściowe strumienie wideo dla Default1080p

Jeśli kanał informacyjny udziału ma rozdzielczość 1080p, ustawienia wstępne **Default1080p** zakodują źródło danych do następujących 6 warstw.

| Multimedia | Width | Height | MaxFPS | Profil |
| --- | --- | --- | --- | --- |
| 5500 |1920 |1080 |30 |Wys. |
| 3000 |1280 |720 |30 |Wys. |
| 1600 |960 |540 |30 |Wys. |
| 800 |640 |360 |30 |Wys. |
| 400 |480 |270 |30 |Wys. |
| 200 |320 |180 |30 |Wys. |

> [!NOTE]
> Jeśli musisz dostosować ustawienie wstępne kodowania na żywo, Otwórz bilet pomocy technicznej za pośrednictwem witryny Azure Portal. Musisz określić odpowiednią tabelę z rozdzielczością i szybkościami transmisji bitów. Sprawdź, czy istnieje tylko jedna warstwa w lokalizacji 1080p i co najwyżej 6 warstw. Ponadto należy określić, że żądasz ustawienia wstępnego dla kodera Premium1080p na żywo. Określone wartości szybkości transmisji bitów i rozdzielczości mogą być dostosowane z upływem czasu.

### <a name="output-audio-stream-for-default720p-and-default1080p"></a>Wyjściowy strumień audio dla Default720p i Default1080p

W przypadku ustawień wstępnych *Default720p* i *Default1080p* dźwięk jest zakodowany do stereo AAC-LC o 128 kb/s. Częstotliwość próbkowania następuje po ścieżce audio w kanale informacyjnym udziału.

## <a name="implicit-properties-of-the-live-encoder"></a>Niejawne właściwości kodera na żywo

W poprzedniej sekcji opisano właściwości kodera na żywo, który można jawnie kontrolować przy użyciu ustawień predefiniowanych — takich jak liczba warstw, rozdzielczości i szybkości transmisji bitów. W tej sekcji objaśniono niejawne właściwości.

### <a name="group-of-pictures-gop-duration"></a>Czas trwania grupy obrazów (grupę GOP)

Koder na żywo jest zgodny ze strukturą [grupę GOP](https://en.wikipedia.org/wiki/Group_of_pictures) kanału informacyjnego, co oznacza, że warstwy wyjściowe będą miały ten sam czas grupę GOP. W związku z tym zaleca się skonfigurowanie kodera lokalnego w celu utworzenia kanału informacyjnego, który ma stały czas trwania grupę GOP (zazwyczaj 2 sekundy). Zapewni to, że strumienie wychodzące HLS i MPEG z usługi również mają stałe czasy grupę GOP. Małe wahania w czasie trwania grupę GOP są prawdopodobnie tolerowane przez większość urządzeń.

### <a name="frame-rate"></a>Szybkość klatek

Koder na żywo również następuje po czasie trwania poszczególnych klatek wideo w kanale informacyjnym, co oznacza, że warstwy wyjściowe będą zawierały ramki o tych samych czasach. W związku z tym zaleca się skonfigurowanie kodera lokalnego w taki sposób, aby generował źródło danych, które ma stałą szybkość klatek (co najwyżej 30 klatek na sekundę). Zapewni to, że strumienie wychodzące HLS i MPEG z usługi również mają czasy trwania stałych klatek. Małe wahania stawek za ramki mogą być tolerowane przez większość urządzeń, ale nie ma żadnej gwarancji, że koder na żywo będzie generował dane wyjściowe, które będą prawidłowo odtwarzane. Lokalny koder na żywo nie powinien zawierać ramek (np. w obszarze niskie warunki baterii lub w jakikolwiek sposób zmieniana jest szybkość klatek.

### <a name="resolution-of-contribution-feed-and-output-layers"></a>Rozwiązanie kanału informacyjnego dotyczącego udziału i warstw wyjściowych

Koder na żywo jest skonfigurowany tak, aby uniknąć konwersji kanału informacyjnego udziału. W wyniku tego nie zostanie przekroczenie maksymalnej rozdzielczości warstwy wyjściowej.

Na przykład w przypadku wysłania kanału informacyjnego o wartości 720 do zdarzenia na żywo skonfigurowanego do Default1080p na żywo, wyjście będzie miało tylko 5 warstw, rozpoczynając od 720 o godzinie 3Mbps, przechodząc do 1080p o 200 KB/s. Lub w przypadku wysłania kanału informacyjnego udziału o 360p do zdarzenia na żywo skonfigurowanego dla standardowego kodowania na żywo, dane wyjściowe będą zawierać 3 warstwy (w rozdzielczościach 288p, 216p i 192p). W przypadku wygenerowania w przypadku wysłania kanału informacyjnego, powiedzmy, 160x90 pikseli do standardowego kodera na żywo, dane wyjściowe będą zawierać jedną warstwę w rozdzielczości 160x90, która ma taką samą szybkość transmisji bitów jak w przypadku źródła strumieniowego.

### <a name="bitrate-of-contribution-feed-and-output-layers"></a>Szybkość transmisji bitów dla kanału informacyjnego i warstwy wyjściowe

Koder na żywo jest skonfigurowany tak, aby przestrzegać ustawień szybkości transmisji bitów w ustawieniach wstępnych, niezależnie od szybkości transmisji strumieniowej źródła danych. Z tego powodu szybkość transmisji bitów warstwy wyjściowej może przekroczyć wielkość źródła danych. Jeśli na przykład wyślesz wiadomość w kanale informacyjnym o rozdzielczości 720 na 1 MB/s, warstwy wyjściowe pozostaną takie same, jak w powyższej [tabeli](live-event-types-comparison.md#output-video-streams-for-default720p) .

## <a name="next-steps"></a>Następne kroki

[Omówienie przesyłania strumieniowego na żywo](live-streaming-overview.md)
