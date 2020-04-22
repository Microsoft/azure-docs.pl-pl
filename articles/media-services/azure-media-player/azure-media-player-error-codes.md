---
title: Kody błędów programu Azure Media Player
description: Odwołanie do kodu błędu dla programu Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: error-reference
ms.date: 04/20/2020
ms.openlocfilehash: 13d804ec39c3d7753d4ee04962a88d4451fb04d9
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727257"
---
# <a name="error-codes"></a>Kody błędów #

Gdy odtwarzanie nie może się rozpocząć lub zostało zatrzymane, zostanie onoooooozostało uruchamiane, a `error()` funkcja zwróci kod i opcjonalny komunikat, aby pomóc deweloperowi aplikacji uzyskać więcej szczegółów. `error().message`nie jest komunikatem wyświetlanym użytkownikowi.  Komunikat wyświetlany użytkownikowi jest oparty `error().code` na bitach 27-20, patrz tabela poniżej.

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
- 1 - WZMACNIACZ
- 2 — AzureHtml5JS
- 3 - Błyski
- 4 - Silverlightss
- 5 - Html5
- 6 - Html5FairPlayHLS

## <a name="error-codes-bits-27-0-28-bits"></a>Kody błędów, bity [27-0] (28 bitów) ##

Opisz szczegóły błędu, bity 27-20 zapewniają wysoki poziom, bity 19-0 zapewniają więcej szczegółów, jeśli są dostępne.


| amp.errorCode. [nazwa] | Kody, Bity [27-0] (28 bitów) | Opis |
|---|---:|---|
| **zakres błędów MEDIA_ERR_ABORTED (0x0100000 - 0x01FFFFF)** | | |
| abortedErrUnknown | 0x0100000 | Ogólny błąd przerwania |
| abortedErrNotImplemented | 0x0100001 | Błąd przerwania, nie zaimplementowany |
| abortedErrHttpMixedContentBlocked | 0x010002 | Błąd przerwania, zawartość mieszana zablokowana — `http://` zazwyczaj `https://` występuje podczas ładowania strumienia ze strony |
| **MEDIA_ERR_NETWORK błędy wartość początkowa (0x0200000 - 0x02FFFFF)** | | |
| siećErrNieżne | 0x020000 | Ogólny błąd sieci |
| networkErrHttpBadUrlFormat | 0x0200190 | Http 400 odpowiedź na błąd |
| networkErHttpUserAuth Wymagane | 0x0200191 | Http 401 odpowiedź na błąd |
| networkErrHttpUserForbidden | 0x0200193 | Http 403 odpowiedź na błąd |
| networkErHttpUrlNotFound | 0x0200194 | Http 404 odpowiedź na błąd |
| networkErrHttpNotallowed | 0x0200195 | Http 405 odpowiedź na błąd |
| networkErrHttpGone | 0x020019A | Http 410 odpowiedź na błąd |
| networkErrHttpPregenerowanawarunkaFailowana | 0x020019C | Http 412 odpowiedź na błąd |
| networkErrHttpInternalServerFailure | 0x02001F4 | Http 500 odpowiedź na błąd
| networkErrHttpBadGateway | 0x02001F6 | Http 502 odpowiedź na błąd |
| networkErrHttpServiceDostępny | 0x02001F7 | Http 503 odpowiedź na błąd |
| networkErrHttpGatewayTimeout | 0x02001F8 | Http 504 odpowiedź na błąd |
| siećErrTimeout | 0x0200258 | Błąd limitu czasu sieci
| siećErerr | 0x0200259 | Odpowiedź na błąd połączenia sieciowego |
| **MEDIA_ERR_DECODE błędy (0x0300000 - 0x03FFFFF)** | | |
| dekodowanieErrUnknown | 0x0300000 | Ogólny błąd dekodowania |
| **MEDIA_ERR_SRC_NOT_SUPPORTED błędy (0x0400000 - 0x04FFFFF)** | | |
| srcErrNież | 0x0400000 | Błąd źródła ogólnego nie jest obsługiwany |
| srcErrParsePresentation srcErrParsePresentation srcErrParsePresentation src | 0x0400001 | Błąd analizy prezentacji |
| srcErrParseSegment | 0x0400002 | Błąd analizy segmentu |
| srcErrUnsupportedPresentation srcErrUnsupportedpresentation srcErrUnsupportedPresentation src | 0x0400003 | Prezentacja nie jest obsługiwana |
| srcErrInvalidSegment | 0x0400004 | Nieprawidłowy segment |
| **MEDIA_ERR_ENCRYPTED błędy wartość początkowa(0x0500000 - 0x05FFFFF)** | | |
| szyfrowanieErrNieżone | 0x0500000 | Ogólny zaszyfrowany błąd | 
| encryptErrDecrypterNotFound | 0x0500001 | Nie znaleziono odszyfrowania |
| szyfrowanieErrDecrypterInit | 0x0500002 | Błąd inicjowania odszyfrowywania |
| encryptErRDecrypterNotSupportowane | 0x0500003 | Odszyfrowywanie nie jest obsługiwane |
| szyfrowanieErrKeyAcquire | 0x0500004 | Przejęcie klucza nie powiodło się |
| szyfrowanieErrDecryption | 0x0500005 | Odszyfrowywanie segmentu nie powiodło się |
| szyfrowanieErrLicenseAcquire | 0x0500006 | Nabycie licencji nie powiodło się |
| **SRC_PLAYER_MISMATCH błędy wartość początkowa(0x0600000 - 0x06FFFFF)** | | |
| srcPlayerMismatchNie wiadomo | 0x0600000 | Ogólny brak pasującego gracza technicznego do gry w źródło |
| srcPlayerMismatchFlashNotInstalled | 0x0600001 |Wtyczka Flash nie jest zainstalowana, jeśli jest zainstalowane źródło może odtwarzać. *LUB* Program Flash 30 jest zainstalowany i odtwarza zawartość AES.  Jeśli tak jest, spróbuj innej przeglądarki. Flash 30 nie jest obecnie obsługiwane od 7 czerwca. Zobacz [znane problemy,](azure-media-player-known-issues.md) aby uzyskać więcej informacji. Uwaga: Jeśli 0x00600003, zarówno Flash, jak i Silverlight nie są zainstalowane, jeśli określono w techOrder.|
| srcPlayerMismatchSilverlightNotIninstalowany | 0x060002 | Wtyczka Silverlight nie jest zainstalowana, jeśli jest zainstalowane źródło może grać. Uwaga: Jeśli 0x00600003, zarówno Flash, jak i Silverlight nie są zainstalowane, jeśli określono w techOrder. |
| | 0x00600003 | Zarówno Flash, jak i Silverlight nie są zainstalowane, jeśli określono w techOrder. |
| **Nieznane błędy (0x0FF00000)** | | |
| errNie wiadomo | 0xFF00000 | Nieznane błędy |

## <a name="user-error-messages"></a>Komunikaty o błędach użytkowników ##

Wyświetlany komunikat użytkownika jest oparty na bitach kodu błędu 27-20.

- MEDIA_ERR_ABORTED (1) -"Przerwałeś odtwarzanie wideo"
- MEDIA_ERR_NETWORK (2) - "Błąd sieciowy spowodował, że pobieranie wideo nie powiodło się w niepełnym wymiarze czasowym."
- MEDIA_ERR_DECODE (3) - "Odtwarzanie wideo zostało przerwane z powodu problemu z uszkodzeniem lub dlatego, że używane funkcje wideo przeglądarki nie były obsługiwane."
- MEDIA_ERR_SRC_NOT_SUPPORTED (4)-"Nie można załadować wideo, ponieważ serwer lub sieć nie powiodły się lub ponieważ format nie jest obsługiwany."
- MEDIA_ERR_ENCRYPTED (5) - "Film jest zaszyfrowany i nie mamy kluczy do odszyfrowania go."
- SRC_PLAYER_MISMATCH (6) - "Nie znaleziono żadnego zgodnego źródła dla tego filmu."
- MEDIA_ERR_UNKNOWN (0xFF) - "Wystąpił nieznany błąd."

## <a name="examples"></a>Przykłady ##

### <a name="0x10600001"></a>0x10600001 ##

"Nie znaleziono żadnego zgodnego źródła dla tego filmu." jest wyświetlany użytkownikowi końcowemu.

Nie ma odtwarzacza technicznego, który mógłby odtwarzać żądane źródła, ale jeśli wtyczka Flash jest zainstalowana, prawdopodobnie można było odtworzyć źródło.  

### <a name="0x20200194"></a>0x20200194 ###

"Błąd sieciowy spowodował, że pobieranie wideo nie powiodło się w niepełnym wymiarze czasowym." jest wyświetlany użytkownikowi końcowemu.

Nie można odtworzyć usługi AzureHtml5JS z odpowiedzi http 404.

### <a name="categorizing-errors"></a>Kategoryzowanie błędów ###

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

### <a name="catching-a-specific-error"></a>Wyłapywanie określonego błędu ###

Poniższy kod wychwytuje tylko 404 błędy:

```javascript
    if(myPlayer.error().code & amp.errorCode.networkErrHttpUrlNotFound) {
        // all http 404 errors
    }
```

## <a name="next-steps"></a>Następne kroki ##

- [Szybki start programu Azure Media Player](azure-media-player-quickstart.md)