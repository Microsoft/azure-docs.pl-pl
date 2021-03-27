---
title: Kody błędów zdarzeń na żywo Azure Media Services
description: W tym artykule wymieniono kody błędów zdarzeń na żywo.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: error-reference
ms.date: 03/26/2021
ms.author: inhenkel
ms.openlocfilehash: 7c30649fe3486f812569cb51f609356a6cbfd58f
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2021
ms.locfileid: "105627544"
---
# <a name="media-services-live-event-error-codes"></a>Kody błędów zdarzeń na żywo Media Services

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

W poniższej tabeli wymieniono kody błędów [zdarzeń na żywo](live-events-outputs-concept.md) .

## <a name="liveeventconnectionrejected"></a>LiveEventConnectionRejected

Po zasubskrybowaniu zdarzeń [Event Grid](../../event-grid/index.yml) dla zdarzenia na żywo może zostać wyświetlony jeden z następujących błędów ze zdarzenia [LiveEventConnectionRejected](monitoring/media-services-event-schemas.md\#liveeventconnectionrejected) .
> [!div class="mx-tdCol2BreakAll"]
>| Błąd | Informacje |
>|--|--|
>|**MPE_RTMP_APPID_AUTH_FAILURE** ||
>|Opis | Nieprawidłowy adres URL pozyskiwania |
>|Sugerowane rozwiązanie| Identyfikator aplikacji jest tokenem GUID w adresie URL pozyskiwania RTMP. Upewnij się, że jest ona zgodna z adresem URL pozyskiwania z interfejsu API. |
>|**MPE_INGEST_ENCODER_CONNECTION_DENIED** ||
>| Opis |Protokół IP kodera nie istnieje na liście dozwolonych adresów IP |
>| Sugerowane rozwiązanie| Upewnij się, że adres IP kodera znajduje się na liście dozwolonych adresów IP. Użyj narzędzia online, takiego jak *whoismyip* lub *Kalkulator CIDR* , aby ustawić odpowiednią wartość.  Upewnij się, że koder może dotrzeć do serwera przed rzeczywistym zdarzeniem na żywo. |
>|**MPE_INGEST_RTMP_SETDATAFRAME_NOT_RECEIVED** ||
>| Opis|Koder RTMP nie wysłał `setDataFrame` polecenia. |
>| Sugerowane rozwiązanie|Większość koderów komercyjnych wysyła metadane strumienia. Dla kodera, który wypycha pojedyncze przepchnięcie szybkości transmisji bitów, może to nie być problem. LiveEvent może obliczać szybkość transmisji bitów w przypadku braku metadanych strumienia.  W przypadku pozyskiwania o wysokiej szybkości transmisji bitów dla kanału PassThru lub podwójnego wypychania można spróbować dołączyć ciąg zapytania z parametrami "videodatarate" i "audiodatarate" w adresie URL pozyskiwania. Przybliżona wartość może zadziałała. Jednostka ma wartość w kbit. Na przykład  `rtmp://hostname:1935/live/GUID_APPID/streamname?videodatarate=5000&audiodatarate=192` |
>|**MPE_INGEST_CODEC_NOT_SUPPORTED** ||
>| Opis|Określony koder-dekoder nie jest obsługiwany.|
>| Sugerowane rozwiązanie| LiveEvent odebrał nieobsługiwany koder-dekoder. Na przykład w przypadku protokołu RTMP LiveEvent odebrano koder-dekoder wideo bez AVC.  Sprawdź ustawienia wstępne kodera. |
>|**MPE_INGEST_DESCRIPTION_INFO_NOT_RECEIVED** ||
>| Opis |Informacje o opisie nośnika nie zostały odebrane przed dostarczeniem rzeczywistych danych nośnika. |
>| Sugerowane rozwiązanie|LiveEvent nie otrzymuje opisu strumienia (nagłówka lub znacznika FLV) z kodera. Jest to naruszenie protokołu. Skontaktuj się z dostawcą kodera. |
>|**MPE_INGEST_MEDIA_QUALITIES_EXCEEDED** ||
>| Opis|Liczba jakości dla typu audio lub wideo przekroczyła maksymalny dozwolony limit. |
>| Sugerowane rozwiązanie|Gdy tryb zdarzenia na żywo jest Live Encoding, koder powinien wypchnąć pojedynczą szybkość transmisji bitów audio i wideo.  Należy zauważyć, że dozwolone jest nadmiarowe wypychanie z tej samej szybkości transmisji bitów. Sprawdź ustawienia predefiniowane ustawień kodera lub danych wyjściowych, aby upewnić się, że wyprowadza strumień o pojedynczej szybkości transmisji bitów. |
>|**MPE_INGEST_BITRATE_AGGREGATED_EXCEEDED** ||
>| Opis|Łączna liczba przychodzących szybkości transmisji bitów w zdarzeniu lub w usłudze kanału na żywo przekroczyła maksymalny dozwolony limit. |
>| Sugerowane rozwiązanie|Koder przekracza maksymalną szybkość transmisji bitów. Ten limit agreguje wszystkie dane przychodzące z kodera. Sprawdź ustawienia predefiniowane lub wyjściowe kodera, aby zmniejszyć szybkość transmisji bitów. |
>|**MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID** ||
>| Opis|Sygnatura czasowa dla FLVTag wideo lub audio jest nieprawidłowa z kodera RTMP. |
>| Sugerowane rozwiązanie|Przestarzałe. |
>|**MPE_INGEST_FRAMERATE_EXCEEDED** ||
>| Opis|Przychodzące strumienie dla koderów przychodzących z szybkością klatek przekraczają maksymalną dozwoloną liczbę 30 fps na potrzeby kodowania zdarzeń/kanałów na żywo. |
>| Sugerowane rozwiązanie|Sprawdź ustawienia wstępne dla kodera, aby zmniejszyć liczbę klatek na 36 fps. |
>|**MPE_INGEST_VIDEO_RESOLUTION_NOT_SUPPORTED** ||
>| Opis|Przychodzące strumienie przekazywanego kodera przekraczają następujące dozwolone rozwiązania: 1920x1088 do kodowania na żywo zdarzeń/kanałów i 4096 x 2160 dla zdarzeń przesyłanych/kanałów na żywo. |
>| Sugerowane rozwiązanie|Sprawdź ustawienia wstępne kodera pod kątem mniejszej rozdzielczości wideo, aby nie przekroczyć limitu. |
>|**MPE_INGEST_RTMP_TOO_LARGE_UNPROCESSED_FLV** |
>| Opis|Wydarzenie na żywo otrzymało dużą ilość danych audio jednocześnie lub dużą ilość danych wideo bez klatek kluczowych. Koder został rozłączony, aby umożliwić mu ponowną próbę ponowienia przy użyciu prawidłowych danych. |
>| Sugerowane rozwiązanie|Upewnij się, że Koder wysyła ramkę kluczową dla każdego interwału klatek kluczowych (grupę GOP).  Włącz ustawienia, takie jak "stała szybkość transmisji bitów (CBR)" lub "Wyrównaj kluczowe ramki". Czasami może to pomóc w zresetowaniu kodera. Jeśli to nie pomoże, skontaktuj się z dostawcą kodera. |

## <a name="liveeventencoderdisconnected"></a>LiveEventEncoderDisconnected

Może zostać wyświetlony jeden z następujących błędów ze zdarzenia [LiveEventEncoderDisconnected](monitoring/media-services-event-schemas.md\#liveeventencoderdisconnected) .

> [!div class="mx-tdCol2BreakAll"]
>| Błąd | Informacje |
>|--|--|
>|**MPE_RTMP_SESSION_IDLE_TIMEOUT** |
>| Opis|Przekroczono limit czasu bezczynności sesji RTMP po upływie limitu czasu. |
>|Sugerowane rozwiązanie|Dzieje się tak zazwyczaj, gdy koder przestaje odbierać dane wejściowe, tak że sesja stanie się bezczynna, ponieważ nie ma żadnych danych do wypchnięcia. Sprawdź, czy stan kodera lub źródła danych wejściowych jest w dobrej kondycji. |
>|**MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID** |
>|Opis| Sygnatura czasowa dla FLVTag wideo lub audio jest nieprawidłowa z kodera RTMP. |
>| Sugerowane rozwiązanie| Przestarzałe. |
>|**MPE_CAPACITY_LIMIT_REACHED** |
>| Opis|Koder wysyła zbyt szybkie dane. |
>| Sugerowane rozwiązanie|Dzieje się tak, gdy koder rozdzieli duży zestaw fragmentów w krótkim czasie.  Może to teoretycznie nastąpić, gdy koder nie może wypchnąć danych z powodu problemu z siecią i danych o rozerwaniu, gdy sieć jest dostępna. Znajdź przyczynę z dziennika kodera lub dziennika systemowego. |
>|**Nieznane kody błędów** |
>| Opis| Te kody błędów mogą należeć do zakresu od błędu pamięci do zduplikowanych wpisów w mapie skrótów. Może się tak zdarzyć, gdy Koder wysyła duży zestaw fragmentów w krótkim czasie.  Może to mieć miejsce, gdy koder nie może wypchnąć danych z powodu problemu z siecią, a następnie wysyła wszystkie opóźnione fragmenty jednocześnie, gdy sieć stanie się dostępna. |
>|Sugerowane rozwiązanie| Sprawdź dzienniki kodera.|

## <a name="other-error-codes"></a>Inne kody błędów

> [!div class="mx-tdCol2BreakAll"]
>| Błąd | Informacje |Odrzucone/odłączone zdarzenie|
>|--|--|--|
>|**ERROR_END_OF_MEDIA** ||Tak|
>| Opis|Jest to błąd ogólny. ||
>|Sugerowane rozwiązanie| Brak.||
>|**MPI_SYSTEM_MAINTENANCE** ||Tak|
>| Opis|Koder odłączony z powodu aktualizacji usługi lub konserwacji systemu. ||
>|Sugerowane rozwiązanie|Upewnij się, że koder włącza funkcję AutoConnect. Umożliwia koderowi ponowne nawiązanie połączenia z nadmiarowym punktem końcowym zdarzenia na żywo, który nie jest w trakcie konserwacji. ||
>|**MPE_BAD_URL_SYNTAX** ||Tak|
>| Opis|Adres URL pozyskiwania jest niepoprawnie sformatowany. ||
>|Sugerowane rozwiązanie|Upewnij się, że adres URL pozyskiwania jest poprawnie sformatowany. W przypadku protokołu RTMP powinna być `rtmp[s]://hostname:port/live/GUID_APPID/streamname` ||
>|**MPE_CLIENT_TERMINATED_SESSION** ||Tak|
>| Opis|Koder rozłączył sesję.  ||
>|Sugerowane rozwiązanie|To nie jest błąd. Koder zainicjował rozłączenie, w tym bezpieczne rozłączenie. Jeśli jest to nieoczekiwane rozłączenie, Sprawdź dzienniki kodera. |
>|**MPE_INGEST_BITRATE_NOT_MATCH** ||Nie|
>| Opis|Szybkość danych przychodzących nie jest zgodna z oczekiwaną szybkością transmisji bitów. ||
>|Sugerowane rozwiązanie|Jest to ostrzeżenie, które jest wykonywane, gdy szybkość danych przychodzących jest zbyt niska lub szybka. Sprawdź dziennik kodera lub Dziennik systemowy.||
>|**MPE_INGEST_DISCONTINUITY** ||Nie|
>| Opis| Dane przychodzące są discontinuty.||
>|Sugerowane rozwiązanie| Jest to ostrzeżenie, że koder odrzuca dane z powodu problemu z siecią lub problemu z zasobami systemowymi. Sprawdź dziennik kodera lub Dziennik systemowy. Monitoruj zasoby systemowe (procesor, pamięć lub sieć). Jeśli procesor CPU systemu jest zbyt wysoki, spróbuj obniżyć szybkość transmisji bitów lub użyj opcji kodera H/W z karty grafika systemowa.||

## <a name="see-also"></a>Zobacz też

[Kody błędów punktu końcowego przesyłania strumieniowego (Źródło)](streaming-endpoint-error-codes.md)

## <a name="next-steps"></a>Następne kroki

[Samouczek: przesyłanie strumieniowe na żywo za pomocą Media Services](stream-live-tutorial-with-api.md)
