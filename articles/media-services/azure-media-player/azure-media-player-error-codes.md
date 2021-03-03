---
title: Azure Media Player kody błędów
description: Odwołanie do kodu błędu dla Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: error-reference
ms.date: 04/20/2020
ms.openlocfilehash: 89f063ab50cfd880552e80a77ba798e4d06de5b3
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101709584"
---
# <a name="error-codes"></a>Kody błędów #

Gdy odtwarzanie nie można uruchomić lub zostało zatrzymane, zostanie wyzwolone zdarzenie błędu, a `error()` funkcja zwróci kod i opcjonalny komunikat, aby pomóc deweloperowi aplikacji uzyskać więcej szczegółów. `error().message` nie jest wyświetlany komunikat dla użytkownika.  Komunikat wyświetlany dla użytkownika jest oparty na `error().code` bitach 27-20, zobacz poniższą tabelę.

```javascript

    var myPlayer = amp('vid1');
    myPlayer.addEventListener('error', function() {
        var errorDetails = myPlayer.error();
        var code = errorDetails.code;
        var message = errorDetails.message;
    }
```

## <a name="error-codes-bits-31-28-4-bits"></a>Kody błędów, bity [31-28] (4 bity) ##

Opisz obszar błędu.

- 0 — Nieznany
- 1-AMP
- 2 — AzureHtml5JS
- 3 — błysk
- 4 — Silverlight
- 5 — HTML5
- 6 — Html5FairPlayHLS

## <a name="error-codes-bits-27-0-28-bits"></a>Kody błędów, bity [27-0] (28 bitów) ##

Opisz szczegóły błędu, bity 27-20 zapewniają wysoki poziom, bity 19-0 podaj więcej szczegółów, jeśli są dostępne.


| amp. errorCode. Nazwij | Kody, bity [27-0] (28 bitów) | Opis |
|---|---:|---|
| **Zakres błędów MEDIA_ERR_ABORTED (0x0100000-0x01FFFFF)** | | |
| abortedErrUnknown | 0x0100000 | Ogólny błąd przerwania |
| abortedErrNotImplemented | 0x0100001 | Błąd przerwania, niezaimplementowany |
| abortedErrHttpMixedContentBlocked | 0x0100002 | Błąd przerwania, zablokowana zawartość mieszana — ogólnie występuje podczas ładowania `http://` strumienia ze `https://` strony |
| **Wartość początkowa błędów MEDIA_ERR_NETWORK (0x0200000-0x02FFFFF)** | | |
| networkErrUnknown | 0x0200000 | Ogólny błąd sieci |
| networkErrHttpBadUrlFormat | 0x0200190 | Odpowiedź na błąd HTTP 400 |
| networkErrHttpUserAuthRequired | 0x0200191 | Odpowiedź na błąd HTTP 401 |
| networkErrHttpUserForbidden | 0x0200193 | Odpowiedź na błąd HTTP 403 |
| networkErrHttpUrlNotFound | 0x0200194 | Odpowiedź na błąd HTTP 404 |
| networkErrHttpNotAllowed | 0x0200195 | Odpowiedź na błąd HTTP 405 |
| networkErrHttpGone | 0x020019A | Odpowiedź na błąd http 410 |
| networkErrHttpPreconditionFailed | 0x020019C | Odpowiedź na błąd http 412 |
| networkErrHttpInternalServerFailure | 0x02001F4 | Odpowiedź na błąd HTTP 500
| networkErrHttpBadGateway | 0x02001F6 | Odpowiedź na błąd http 502 |
| networkErrHttpServiceUnavailable | 0x02001F7 | Odpowiedź na błąd HTTP 503 |
| networkErrHttpGatewayTimeout | 0x02001F8 | Odpowiedź na błąd http 504 |
| networkErrTimeout | 0x0200258 | Błąd limitu czasu sieci
| networkErrErr | 0x0200259 | Odpowiedź na błąd połączenia sieciowego |
| **Błędy MEDIA_ERR_DECODE (0x0300000 – 0x03FFFFF)** | | |
| decodeErrUnknown | 0x0300000 | Ogólny błąd dekodowania |
| **Błędy MEDIA_ERR_SRC_NOT_SUPPORTED (0x0400000 – 0x04FFFFF)** | | |
| srcErrUnknown | 0x0400000 | Błąd nieobsługiwanego źródła ogólnego |
| srcErrParsePresentation | 0x0400001 | Błąd analizowania prezentacji |
| srcErrParseSegment | 0x0400002 | Błąd analizy segmentu |
| srcErrUnsupportedPresentation | 0x0400003 | Prezentacja nie jest obsługiwana |
| srcErrInvalidSegment | 0x0400004 | Nieprawidłowy segment |
| **Wartość początkowa błędów MEDIA_ERR_ENCRYPTED (0x0500000-0x05FFFFF)** | | |
| encryptErrUnknown | 0x0500000 | Ogólny błąd zaszyfrowany | 
| encryptErrDecrypterNotFound | 0x0500001 | Nie znaleziono Decrypter |
| encryptErrDecrypterInit | 0x0500002 | Błąd inicjowania Decrypter |
| encryptErrDecrypterNotSupported | 0x0500003 | Decrypter nie jest obsługiwana |
| encryptErrKeyAcquire | 0x0500004 | Nie można pobrać klucza |
| encryptErrDecryption | 0x0500005 | Odszyfrowywanie segmentu nie powiodło się |
| encryptErrLicenseAcquire | 0x0500006 | Pozyskiwanie licencji nie powiodło się |
| **Wartość początkowa błędów SRC_PLAYER_MISMATCH (0x0600000-0x06FFFFF)** | | |
| srcPlayerMismatchUnknown | 0x0600000 | Ogólny brak pasującego odtwarzacza Tech., aby odtworzyć Źródło |
| srcPlayerMismatchFlashNotInstalled | 0x0600001 |Wtyczka Flash nie jest zainstalowana, jeśli jest zainstalowana źródło może być odtwarzane. *Lub* Flash 30 jest zainstalowany i odtwarza zawartość AES.  W takim przypadku spróbuj użyć innej przeglądarki. Program Flash 30 nie jest obecnie obsługiwany z dniem siódmym. Zobacz [znane problemy](azure-media-player-known-issues.md) , aby uzyskać więcej szczegółów. Uwaga: Jeśli 0x00600003, programy Flash i Silverlight nie są zainstalowane, jeśli zostały określone w techOrder.|
| srcPlayerMismatchSilverlightNotInstalled | 0x0600002 | Wtyczka Silverlight nie jest zainstalowana, jeśli jest zainstalowana źródło może być odtwarzane. Uwaga: Jeśli 0x00600003, programy Flash i Silverlight nie są zainstalowane, jeśli zostały określone w techOrder. |
| | 0x00600003 | Programy Flash i Silverlight nie są zainstalowane, jeśli zostały określone w techOrder. |
| **Nieznane błędy (0x0FF00000)** | | |
| errUnknown | 0xFF00000 | Nieznane błędy |

## <a name="user-error-messages"></a>Komunikaty o błędach użytkownika ##

Wyświetlany komunikat użytkownika jest oparty na bitach kodu błędu 27-20.

- MEDIA_ERR_ABORTED (1) — "odtwarzanie wideo zostało przerwane"
- MEDIA_ERR_NETWORK (2) — "błąd sieci spowodował, że pobieranie wideo kończy się niepowodzeniem."
- MEDIA_ERR_DECODE (3) — "odtwarzanie wideo zostało przerwane z powodu problemu z uszkodzeniem lub ponieważ wideo używane przez przeglądarkę nie było obsługiwane".
- MEDIA_ERR_SRC_NOT_SUPPORTED (4) — "wideo nie może zostać załadowane, ponieważ serwer lub sieć nie powiodły się lub format nie jest obsługiwany".
- MEDIA_ERR_ENCRYPTED (5) — "film wideo jest szyfrowany i nie mamy kluczy do odszyfrowania".
- SRC_PLAYER_MISMATCH (6) — "nie znaleziono zgodnego źródła dla tego wideo".
- MEDIA_ERR_UNKNOWN (0xFF) — "Wystąpił nieznany błąd".

## <a name="examples"></a>Przykłady ##

### <a name="0x10600001"></a>0x10600001 ##

"Nie znaleziono zgodnego źródła dla tego wideo". jest wyświetlany użytkownikowi końcowemu.

Nie istnieje odtwarzacz Tech., który może odtwarzać żądane źródła, ale jeśli wtyczka Flash jest zainstalowana, prawdopodobnie można odtworzyć źródło.  

### <a name="0x20200194"></a>0x20200194 ###

"Błąd sieci spowodował, że pobieranie wideo kończy się niepowodzeniem." jest wyświetlany użytkownikowi końcowemu.

Nie można odtworzyć AzureHtml5JS z odpowiedzi HTTP 404.

### <a name="categorizing-errors"></a>Określanie kategorii błędów ###

```javascript
    if(myPlayer.error().code & amp.errorCode.abortedErrStart) {
        // MEDIA_ERR_ABORTED errors
    }
    else if(myPlayer.error().code & amp.errorCode.networkErrStart) {
        // MEDIA_ERR_NETWORK errors
    }
    else if(myPlayer.error().code & amp.errorCode.decodeErrStart) {
        // MEDIA_ERR_DECODE errors
    }
    else if(myPlayer.error().code & amp.errorCode.srcErrStart) {
        // MEDIA_ERR_SRC_NOT_SUPPORTED errors
    }
    else if(myPlayer.error().code & amp.errorCode.encryptErrStart) {
        // MEDIA_ERR_ENCRYPTED errors
    }
    else if(myPlayer.error().code & amp.errorCode.srcPlayerMismatchStart) {
        // SRC_PLAYER_MISMATCH errors
    }
    else {
        // unknown errors
    }
```

### <a name="catching-a-specific-error"></a>Przechwytywanie określonego błędu ###

Poniższy kod obchwytuje tylko błędy 404:

```javascript
    if(myPlayer.error().code & amp.errorCode.networkErrHttpUrlNotFound) {
        // all http 404 errors
    }
```

## <a name="next-steps"></a>Następne kroki ##

- [Azure Media Player — Szybki Start](azure-media-player-quickstart.md)