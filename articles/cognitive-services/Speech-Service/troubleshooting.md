---
title: Rozwiązywanie problemów z zestawem Speech SDK — Speech Service
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera informacje ułatwiające rozwiązywanie problemów, które mogą wystąpić podczas korzystania z zestawu Speech SDK.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: jhakulin
ms.openlocfilehash: 421b9adf4ae5d2c641484e646bea096716d46cca
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "74815417"
---
# <a name="troubleshoot-the-speech-sdk"></a>Rozwiązywanie problemów z zestawem Speech SDK

Ten artykuł zawiera informacje ułatwiające rozwiązywanie problemów, które mogą wystąpić podczas korzystania z zestawu Speech SDK.

## <a name="error-websocket-upgrade-failed-with-an-authentication-error-403"></a>Błąd: nie można uaktualnić protokołu WebSocket z powodu błędu uwierzytelniania (403)

Być może masz niewłaściwy punkt końcowy dla swojego regionu lub usługi. Sprawdź identyfikator URI, aby upewnić się, że jest poprawny.

Ponadto może wystąpić problem z kluczem subskrypcji lub tokenem autoryzacji. Aby uzyskać więcej informacji, zobacz następną sekcję.

## <a name="error-http-403-forbidden-or-http-401-unauthorized"></a>Błąd: HTTP 403 — Dostęp zabroniony lub HTTP 401 nie jest autoryzowany

Ten błąd jest często spowodowany przez problemy z uwierzytelnianiem. Żądania połączenia bez prawidłowego `Ocp-Apim-Subscription-Key` lub `Authorization` nagłówka są odrzucane ze stanem 403 lub 401.

* Jeśli używasz klucza subskrypcji do uwierzytelniania, może zostać wyświetlony następujący błąd:

    - Brak klucza subskrypcji lub jest on nieprawidłowy
    - Przekroczono limit przydziału użycia subskrypcji

* Jeśli używasz tokenu autoryzacji do uwierzytelniania, może zostać wyświetlony błąd, ponieważ:

    - Token autoryzacji jest nieprawidłowy
    - Token autoryzacji wygasł

### <a name="validate-your-subscription-key"></a>Weryfikowanie klucza subskrypcji

Aby sprawdzić, czy masz prawidłowy klucz subskrypcji, możesz uruchomić jedno z poniższych poleceń.

> [!NOTE]
> Zastąp `YOUR_SUBSCRIPTION_KEY` i `YOUR_REGION` własnym kluczem subskrypcji i skojarzonym regionem.

* PowerShell

    ```Powershell
    $FetchTokenHeader = @{
      'Content-type'='application/x-www-form-urlencoded'
      'Content-Length'= '0'
      'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY'
    }
    $OAuthToken = Invoke-RestMethod -Method POST -Uri https://YOUR_REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken -Headers $FetchTokenHeader
    $OAuthToken
    ```

* cURL

    ```
    curl -v -X POST "https://YOUR_REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY" -H "Content-type: application/x-www-form-urlencoded" -H "Content-Length: 0"
    ```

Jeśli wprowadzono prawidłowy klucz subskrypcji, polecenie zwróci Token autoryzacji, w przeciwnym razie zwracany jest błąd.

### <a name="validate-an-authorization-token"></a>Sprawdzanie poprawności tokenu autoryzacji

Jeśli używasz tokenu autoryzacji do uwierzytelniania, Uruchom jedno z następujących poleceń, aby sprawdzić, czy token autoryzacji jest nadal ważny. Tokeny są prawidłowe przez 10 minut.

> [!NOTE]
> Zamień na `YOUR_AUDIO_FILE` ścieżkę do zarejestrowanego pliku dźwiękowego. Zamień na `YOUR_ACCESS_TOKEN` Token autoryzacji zwrócony w poprzednim kroku. Zamień na `YOUR_REGION` prawidłowy region.

* PowerShell

    ```Powershell
    $SpeechServiceURI =
    'https://YOUR_REGION.stt.speech.microsoft.com/speech/recognition/interactive/cognitiveservices/v1?language=en-US'

    # $OAuthToken is the authorization token returned by the token service.
    $RecoRequestHeader = @{
      'Authorization' = 'Bearer '+ $OAuthToken
      'Transfer-Encoding' = 'chunked'
      'Content-type' = 'audio/wav; codec=audio/pcm; samplerate=16000'
    }

    # Read audio into byte array.
    $audioBytes = [System.IO.File]::ReadAllBytes("YOUR_AUDIO_FILE")

    $RecoResponse = Invoke-RestMethod -Method POST -Uri $SpeechServiceURI -Headers $RecoRequestHeader -Body $audioBytes

    # Show the result.
    $RecoResponse
    ```

* cURL

    ```
    curl -v -X POST "https://YOUR_REGION.stt.speech.microsoft.com/speech/recognition/interactive/cognitiveservices/v1?language=en-US" -H "Authorization: Bearer YOUR_ACCESS_TOKEN" -H "Transfer-Encoding: chunked" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE
    ```

Jeśli wprowadzono prawidłowy token autoryzacji, polecenie zwróci transkrypcję pliku audio, w przeciwnym razie zwracany jest błąd.

---

## <a name="error-http-400-bad-request"></a>Błąd: Nieprawidłowe żądanie HTTP 400

Ten błąd występuje zazwyczaj, gdy treść żądania zawiera nieprawidłowe dane audio. Obsługiwany jest tylko format WAV. Sprawdź również nagłówki żądania, aby upewnić się, że określono odpowiednie wartości dla `Content-Type` i `Content-Length` .

## <a name="error-http-408-request-timeout"></a>Błąd: limit czasu żądania HTTP 408

Błąd jest najprawdopodobniej spowodowany tym, że żadne dane audio nie są wysyłane do usługi. Ten błąd może być również spowodowany przez problemy z siecią.

## <a name="recognitionstatus-in-the-response-is-initialsilencetimeout"></a>"RecognitionStatus" w odpowiedzi to "InitialSilenceTimeout"

Ten problem jest zwykle spowodowany przez dane audio. Ten błąd może pojawić się, ponieważ:

* Na początku dźwięku istnieje długi rozciągnięcie. W takim przypadku usługa kończy rozpoznawanie po kilku sekundach i zwraca `InitialSilenceTimeout` .

* Dźwięk używa nieobsługiwanego formatu dekodera, co powoduje, że dane audio będą traktowane jako wyciszenie.

## <a name="next-steps"></a>Następne kroki

* [Zapoznaj się z informacjami o wersji](releasenotes.md)
