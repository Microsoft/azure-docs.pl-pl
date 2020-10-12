---
title: Azure Media Services błędy pakowania i pochodzenia | Microsoft Docs
description: W tym temacie opisano błędy, które mogą pojawić się w usłudze Azure Media Services Streaming Endpoint (ORGIN).
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
ms.date: 05/07/2019
ms.author: inhenkel
ms.openlocfilehash: 8442c52052a2016da002d392c6fe0834b3813229
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89295329"
---
# <a name="streaming-endpoint-origin-errors"></a>Błędy punktu końcowego przesyłania strumieniowego (źródło)

W tym temacie opisano błędy, które mogą pojawić się w [usłudze Azure Media Services Streaming Endpoint Service](streaming-endpoint-concept.md).

## <a name="400-bad-request"></a>400 Nieprawidłowe żądanie

Żądanie zawiera nieprawidłowe informacje i zostało odrzucone z tymi kodami błędów i z jednego z następujących powodów:

|Kod błędu|Wartość szesnastkowa |Opis błędu|
|---|---|---|
|MPE_BAD_URL_SYNTAX |0x80890201|Błąd składni lub formatu adresu URL. Przykłady obejmują żądania dla nieprawidłowego typu, nieprawidłowy fragment lub niepoprawną ścieżkę. |
|MPE_ENC_ENCRYPTION_NOT_SPECIFIED_IN_URL |0x8088024C|W adresie URL żądania nie ma tagu szyfrowania. Żądania CMAF wymagają tagu szyfrowania w adresie URL. Inne protokoły, które są skonfigurowane z więcej niż jednym typem szyfrowania, wymagają również znacznika szyfrowania dla uściślania. |
|MPE_STORAGE_BAD_URL_SYNTAX |0x808900E9|Żądanie do magazynu w celu spełnienia żądania nie powiodło się z powodu nieprawidłowego błędu żądania. |

## <a name="403-forbidden"></a>403 Zabronione

Żądanie jest niedozwolone z jednego z następujących powodów:

|Kod błędu|Wartość szesnastkowa |Opis błędu|
|---|---|---|
|MPE_STORAGE_AUTHENTICATION_FAILED |0x808900EA|Żądanie do magazynu w celu spełnienia żądania nie powiodło się z powodu błędu uwierzytelniania. Taka sytuacja może wystąpić, jeśli klucze magazynu zostały obrócone i usługa nie mogła zsynchronizować kluczy magazynu. <br/><br/>Skontaktuj się z pomocą techniczną platformy Azure, aby [uzyskać pomoc i wsparcie](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) w Azure Portal.|
|MPE_STORAGE_INSUFFICIENT_ACCOUNT_PERMISSIONS |0x808900EB |Błąd operacji magazynu, dostęp nie powiódł się z powodu niewystarczających uprawnień konta. |
|MPE_STORAGE_ACCOUNT_IS_DISABLED |0x808900EC |Żądanie do magazynu w celu spełnienia żądania nie powiodło się, ponieważ konto magazynu jest wyłączone. |
|MPE_STORAGE_AUTHENTICATION_FAILURE |0x808900F3 |Wystąpił błąd operacji magazynu, próba uzyskania dostępu nie powiodła się z powodu błędów ogólnych. |
|MPE_OUTPUT_FORMAT_BLOCKED |0x80890207 |Format danych wyjściowych jest zablokowany ze względu na konfigurację w StreamingPolicy. |
|MPE_ENC_ENCRYPTION_REQUIRED |0x8088021E |Dla zawartości wymagane jest szyfrowanie, zasady dostarczania są wymagane dla formatu danych wyjściowych. |
|MPE_ENC_ENCRYPTION_NOT_SET_IN_DELIVERY_POLICY |0x8088024D |Nie ustawiono szyfrowania w ustawieniach zasad dostarczania. |

## <a name="404-not-found"></a>404 — Nie znaleziono

Operacja podejmuje próbę wykonania operacji na zasobie, który już nie istnieje. Na przykład zasób mógł już zostać usunięty.

|Kod błędu|Wartość szesnastkowa |Opis błędu|
|---|---|---|
|MPE_EGRESS_TRACK_NOT_FOUND |0x80890209 |Nie znaleziono wymaganej ścieżki. |
|MPE_RESOURCE_NOT_FOUND |0x808901F9 |Nie znaleziono żądanego zasobu. |
|MPE_UNAUTHORIZED |0x80890244 |Dostęp nie jest autoryzowany. |
|MPE_EGRESS_TIMESTAMP_NOT_FOUND |0x8089020A |Nie znaleziono żądanego sygnatury czasowej. |
|MPE_EGRESS_FILTER_NOT_FOUND |0x8089020C |Nie znaleziono żądanego filtru manifestu dynamicznego. |
|MPE_FRAGMENT_BY_INDEX_NOT_FOUND |0x80890252 |Żądany indeks fragmentu jest poza prawidłowym zakresem. |
|MPE_LIVE_MEDIA_ENTRIES_NOT_FOUND |0x80890254 |Nie można znaleźć wpisów multimediów na żywo w celu pobrania buforu Moov. |
|MPE_FRAGMENT_TIMESTAMP_NOT_FOUND |0x80890255 |Nie można znaleźć fragmentu w żądanym czasie dla określonej ścieżki.<br/><br/>Czy fragment nie znajduje się w magazynie. Wypróbuj inną warstwę prezentacji, która może mieć fragment. |
|MPE_MANIFEST_MEDIA_ENTRY_NOT_FOUND |0x80890256 |Nie można znaleźć wpisu nośnika dla wymaganej szybkości transmisji bitów w manifeście. <br/><br/>Może być to, że gracz prosi o ścieżkę wideo z określoną szybkością transmisji bitów, która nie została zamieszczona w manifeście.|
|MPE_METADATA_NOT_FOUND |0x80890257 |Nie można znaleźć pewnych metadanych w manifeście lub nie można odnaleźć bazy danych z magazynu. |
|MPE_STORAGE_RESOURCE_NOT_FOUND |0x808900ED |Błąd operacji magazynu, nie znaleziono zasobu. |

## <a name="409-conflict"></a>409 Konflikt

Identyfikator podany dla zasobu w `PUT` operacji lub został `POST` podjęty przez istniejący zasób. Aby rozwiązać ten problem, użyj innego identyfikatora dla zasobu.

|Kod błędu|Wartość szesnastkowa |Opis błędu|
|---|---|---|
|MPE_STORAGE_CONFLICT  |0x808900EE  |Błąd operacji magazynu, błąd konfliktu.  |

## <a name="410"></a>410

|Kod błędu|Wartość szesnastkowa |Opis błędu|
|---|---|---|
|MPE_FILTER_FORCE_END_LEFT_EDGE_CROSSED_DVR_WINDOW|0x80890263|W przypadku przesyłania strumieniowego na żywo, gdy filtr ma forceEndTimestamp o wartości true, początkowy lub końcowy znacznik czasu znajduje się poza bieżącym oknem DVR.|

## <a name="412-precondition-failure"></a>412 niepowodzenie warunku wstępnego

W operacji określono element eTag, który jest inny niż wersja dostępna na serwerze, czyli błąd współbieżności optymistycznej. Ponów żądanie po odczytaniu najnowszej wersji zasobu i zaktualizowaniu elementu eTag dla żądania.

|Kod błędu|Wartość szesnastkowa |Opis błędu|
|---|---|---|
|MPE_FRAGMENT_NOT_READY |0x80890200 |Żądany fragment nie jest gotowy.|
|MPE_STORAGE_PRECONDITION_FAILED| 0x808900EF|Błąd operacji magazynu, błąd warunku wstępnego.|

## <a name="415-unsupported-media-type"></a>415 nieobsługiwany typ nośnika

Format ładunku Wysłany przez klienta ma nieobsługiwany format.

|Kod błędu|Wartość szesnastkowa |Opis błędu|
|---|---|---|
|MPE_ENC_ALREADY_ENCRYPTED| 0x8088021F| Nie powinno być stosowane szyfrowanie dla już zaszyfrowanej zawartości.|
|MPE_ENC_INVALID_INPUT_ENCRYPTION_FORMAT|0x8088021D |Szyfrowanie jest nieprawidłowe dla formatu danych wejściowych.|
|MPE_INVALID_ASSET_DELIVERY_POLICY_TYPE|0x8088021C| Typ zasad dostarczania jest nieprawidłowy.|
|MPE_ENC_MULTIPLE_SAME_DELIVERY_TYPE|0x8088024E |Oryginalne ustawienia mogą być współużytkowane przez wiele formatów danych wyjściowych.|
|MPE_FORMAT_NOT_SUPPORTED|0x80890205|Format lub typ nośnika nie jest obsługiwany. Na przykład Media Services nie obsługuje liczby poziomów jakości przekraczającej 64. W znaczniku wideo FLV Media Services nie obsługuje ramki wideo z wieloma usługami SPS i wieloma PPS.|
|MPE_INPUT_FORMAT_NOT_SUPPORTED|0x80890218| Żądany format danych wejściowych elementu zawartości nie jest obsługiwany. Media Services obsługuje formaty plików gładkich (dynamicznych), MP4 (VoD) i pobierania progresywnego.|
|MPE_OUTPUT_FORMAT_NOT_SUPPORTED|0x8089020D|Żądany format danych wyjściowych nie jest obsługiwany. Media Services obsługuje gładkie, PAUZy (CSF, CMAF), HLS (v3, v4, CMAF) i progresywne formaty pobierania.|
|MPE_ENCRYPTION_NOT_SUPPORTED|0x80890208|Napotkano nieobsługiwany typ szyfrowania.|
|MPE_MEDIA_TYPE_NOT_SUPPORTED|0x8089020E|Żądany typ nośnika nie jest obsługiwany przez format danych wyjściowych. Obsługiwane typy to wideo, audio lub podtytuł "SUBT".|
|MPE_MEDIA_ENCODING_NOT_SUPPORTED|0x8089020F|Źródłowy nośnik zasobów został zakodowany przy użyciu formatu multimediów, który nie jest zgodny z formatem danych wyjściowych.|
|MPE_VIDEO_ENCODING_NOT_SUPPORTED|0x80890210|Źródłowy zasób został zakodowany przy użyciu formatu wideo, który nie jest zgodny z formatem danych wyjściowych. Obsługiwane są godziny. 264, AVC, H. 265 (HEVC, hev1 lub hvc1).|
|MPE_AUDIO_ENCODING_NOT_SUPPORTED|0x80890211|Źródłowy zasób został zakodowany przy użyciu formatu audio, który jest niezgodny z formatem danych wyjściowych. Obsługiwane formaty audio to AAC, E-AC3 (DD +), Dolby DTS.|
|MPE_SOURCE_PROTECTION_CONVERSION_NOT_SUPPORTED|0x80890212|Nie można przekonwertować chronionego zasobu źródłowego na format danych wyjściowych.|
|MPE_OUTPUT_PROTECTION_FORMAT_NOT_SUPPORTED|0x80890213|Format danych wyjściowych nie obsługuje formatu ochrony.|
|MPE_INPUT_PROTECTION_FORMAT_NOT_SUPPORTED|0x80890219|Format danych wejściowych nie obsługuje formatu ochrony.|
|MPE_INVALID_VIDEO_NAL_UNIT|0x80890231|Nieprawidłowa jednostka NAL wideo, na przykład tylko pierwsze NAL w przykładzie może być AUD.|
|MPE_INVALID_NALU_SIZE|0x80890260|Nieprawidłowy rozmiar jednostki NAL.|
|MPE_INVALID_NALU_LENGTH_FIELD|0x80890261|Nieprawidłowa wartość długości jednostki NAL.|
|MPE_FILTER_INVALID|0x80890236|Nieprawidłowe filtry manifestu dynamicznego.|
|MPE_FILTER_VERSION_INVALID|0x80890237|Nieprawidłowe lub nieobsługiwane wersje filtrów.|
|MPE_FILTER_TYPE_INVALID|0x80890238|Nieprawidłowy typ filtru.|
|MPE_FILTER_RANGE_ATTRIBUTE_INVALID|0x80890239|Filtr jest określony jako nieprawidłowy zakres.|
|MPE_FILTER_TRACK_ATTRIBUTE_INVALID|0x8089023A|Filtr jest określony dla nieprawidłowego atrybutu ścieżki.|
|MPE_FILTER_PRESENTATION_WINDOW_INVALID|0x8089023B|Filtr określa nieprawidłową długość okna prezentacji.|
|MPE_FILTER_LIVE_BACKOFF_INVALID|0x8089023C|Nieprawidłowe wycofanie jest określone przez filtr.|
|MPE_FILTER_MULTIPLE_SAME_TYPE_FILTERS|0x8089023D|W starszych filtrach jest obsługiwany tylko jeden element absTimeInHNS.|
|MPE_FILTER_REMOVED_ALL_STREAMS|0x8089023E|Po zastosowaniu filtrów nie ma więcej strumieni.|
|MPE_FILTER_LIVE_BACKOFF_OVER_DVRWINDOW|0x8089023F|Wycofanie na żywo jest poza oknem DVR.|
|MPE_FILTER_LIVE_BACKOFF_OVER_PRESENTATION_WINDOW|0x80890262|Wyłączona kopia zapasowa jest większa niż okno prezentacji.|
|MPE_FILTER_COMPOSITION_FILTER_COUNT_OVER_LIMIT|0x80890246|Przekroczono maksymalną liczbę dozwolonych filtrów domyślnych (10).|
|MPE_FILTER_COMPOSITION_MULTIPLE_FIRST_QUALITY_OPERATOR_NOT_ALLOWED|0x80890248|W łączonych filtrach żądań nie można używać wielu pierwszych operatorów jakości wideo.|
|MPE_FILTER_FIRST_QUALITY_ATTRIBUTE_INVALID|0x80890249|Liczba atrybutów pierwszej jakości szybkości transmisji musi wynosić jeden (1).|
|MPE_HLS_SEGMENT_TOO_LARGE|0x80890243|Czas trwania segmentu HLS musi być mniejszy niż jeden trzeci okna DVR i HLS z powrotem.|
|MPE_KEY_FRAME_INTERVAL_TOO_LARGE|0x808901FE|Czasy trwania fragmentów muszą być mniejsze lub równe około 20 sekund lub poziomy jakości danych wejściowych nie są wyrównywane czasowo.|
|MPE_DTS_RESERVEDBOX_EXPECTED|0x80890105|Błąd specyficzny dla usług DTS, nie można odnaleźć ReservedBox, gdy powinien on być obecny w DTSSpecficBox podczas analizowania pola DTS.|
|MPE_DTS_INVALID_CHANNEL_COUNT|0x80890106|Błąd specyficzny dla usług DTS, podczas analizowania pola DTSSpecficBox nie znaleziono żadnych kanałów.|
|MPE_DTS_SAMPLETYPE_MISMATCH|0x80890107|Błąd specyficzny dla usług DTS, niezgodność typów próbek w DTSSpecficBox.|
|MPE_DTS_MULTIASSET_DTSH_MISMATCH|0x80890108|Błąd specyficzny dla usługi DTS, wiele elementów zawartości jest ustawionych, ale niezgodność typów DTSH.|
|MPE_DTS_INVALID_CORESTREAM_SIZE|0x80890109|Błąd specyficzny dla usług DTS, rozmiar podstawowego strumienia jest nieprawidłowy.|
|MPE_DTS_INVALID_SAMPLE_RESOLUTION|0x8089010A|Błąd specyficzny dla DTS, rozpoznawanie próbek jest nieprawidłowe.|
|MPE_DTS_INVALID_SUBSTREAM_INDEX|0x8089010B|Błąd specyficzny dla usług DTS, indeks rozszerzenia strumienia podrzędnego jest nieprawidłowy.|
|MPE_DTS_INVALID_BLOCK_NUM|0x8089010C|Błąd specyficzny dla usług DTS, numer bloku strumienia podrzędnego jest nieprawidłowy.|
|MPE_DTS_INVALID_SAMPLING_FREQUENCE|0x8089010D|Błąd specyficzny dla DTS, częstotliwość próbkowania jest nieprawidłowa.|
|MPE_DTS_INVALID_REFCLOCKCODE|0x8089010E|Błąd specyficzny dla DTS, kod zegara odwołania w rozszerzeniu strumienia podrzędnego jest nieprawidłowy.|
|MPE_DTS_INVALID_SPEAKERS_REMAP|0x8089010F|Błąd specyficzny dla DTS, liczba ustawionych odłożenia głośników jest nieprawidłowa.|

Aby zapoznać się z artykułami i przykładami dotyczącymi szyfrowania, zobacz:

- [Koncepcja: Ochrona zawartości](content-protection-overview.md)
- [Koncepcja: zasady dotyczące kluczy zawartości](content-key-policy-concept.md)
- [Koncepcja: zasady przesyłania strumieniowego](streaming-policy-concept.md)
- [Przykład: Ochrona przy użyciu szyfrowania AES](protect-with-aes128.md)
- [Przykład: Ochrona przy użyciu technologii DRM](protect-with-drm.md)

Aby uzyskać wskazówki dotyczące filtru, zobacz:

- [Koncepcja: manifesty dynamiczne](filters-dynamic-manifest-overview.md)
- [Koncepcja: filtry](filters-concept.md)
- [Przykład: Tworzenie filtrów za pomocą interfejsów API REST](filters-dynamic-manifest-rest-howto.md)
- [Przykład: Tworzenie filtrów za pomocą platformy .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Przykład: Tworzenie filtrów za pomocą interfejsu wiersza polecenia](filters-dynamic-manifest-cli-howto.md)

Aby zapoznać się z artykułami i przykładami na żywo, zobacz:

- [Koncepcja: Omówienie przesyłania strumieniowego na żywo](live-streaming-overview.md)
- [Koncepcja: wydarzenia na żywo i wyjście na żywo](live-events-outputs-concept.md)
- [Przykład: samouczek dotyczący przesyłania strumieniowego na żywo](stream-live-tutorial-with-api.md)

## <a name="416-range-not-satisfiable"></a>416 Niewłaściwy zakres

|Kod błędu|Wartość szesnastkowa |Opis błędu|
|---|---|---|
|MPE_STORAGE_INVALID_RANGE|0x808900F1|Błąd operacji magazynu, zwrócony błąd HTTP 416, nieprawidłowy zakres.|

## <a name="500-internal-server-error"></a>500 Wewnętrzny błąd serwera

Podczas przetwarzania żądania Media Services napotka jakiś błąd, który uniemożliwia kontynuowanie przetwarzania.  

|Kod błędu|Wartość szesnastkowa |Opis błędu|
|---|---|---|
|MPE_STORAGE_SOCKET_TIMEOUT|0x808900F4|Odbierane i tłumaczone z kodu błędu WinHTTP ERROR_WINHTTP_TIMEOUT (0x00002ee2).|
|MPE_STORAGE_SOCKET_CONNECTION_ERROR|0x808900F5|Odbierane i tłumaczone z kodu błędu WinHTTP ERROR_WINHTTP_CONNECTION_ERROR (0x00002efe).|
|MPE_STORAGE_SOCKET_NAME_NOT_RESOLVED|0x808900F6|Odbierane i tłumaczone z kodu błędu WinHTTP ERROR_WINHTTP_NAME_NOT_RESOLVED (0x00002ee7).|
|MPE_STORAGE_INTERNAL_ERROR|0x808900E6|Błąd operacji magazynu, ogólna InternalError jednego z błędów HTTP 500.|
|MPE_STORAGE_OPERATION_TIMED_OUT|0x808900E7|Błąd operacji magazynu, ogólna OperationTimedOut jednego z błędów HTTP 500.|
|MPE_STORAGE_FAILURE|0x808900F2|Błąd operacji magazynu, inne błędy HTTP 500 niż InternalError lub OperationTimedOut.|

## <a name="503-service-unavailable"></a>503 — usługa niedostępna

Serwer nie może obecnie odbierać żądań. Ten błąd może być spowodowany przez nadmierną liczbę żądań do usługi. Mechanizm ograniczania usług Media Services ogranicza użycie zasobów dla aplikacji, które zgłaszają zbyt wiele żądań do usługi.

> [!NOTE]
> Sprawdź komunikat o błędzie i ciąg kodu błędu, aby uzyskać bardziej szczegółowe informacje na temat przyczyny błędu 503. Ten błąd nie zawsze oznacza ograniczenie przepustowości.
> 

|Kod błędu|Wartość szesnastkowa |Opis błędu|
|---|---|---|
|MPE_STORAGE_SERVER_BUSY|0x808900E8|Błąd operacji magazynu, odebrano błąd 503 zajętego serwera HTTP.|

## <a name="ask-questions-give-feedback-get-updates"></a>Zadawaj pytania, Przekaż opinię, uzyskaj aktualizacje

Zapoznaj się z artykułem [community Azure Media Services](media-services-community.md) , aby zobaczyć różne sposoby zadawania pytań, przekazać Opinie i uzyskać aktualizacje dotyczące Media Services.

## <a name="see-also"></a>Zobacz też

- [Kody błędów kodowania](/rest/api/media/jobs/get#joberrorcode)
- [Koncepcje Azure Media Services](concepts-overview.md)
- [Limity przydziału i ograniczenia](limits-quotas-constraints.md)

## <a name="next-steps"></a>Następne kroki

[Przykład: dostęp do błędu ErrorCode i komunikatu z ApiException za pomocą platformy .NET](configure-connect-dotnet-howto.md#connect-to-the-net-client)
