---
title: Debuguj błędy podczas uruchamiania aplikacji poleceń niestandardowych
titleSuffix: Azure Cognitive Services
description: W tym artykule dowiesz się, jak debugować błędy środowiska uruchomieniowego w aplikacji poleceń niestandardowych.
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: 49d9b91df896646da7bf36e077d9f3c9187137dd
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95021800"
---
# <a name="debug-errors-when-running-a-custom-commands-application"></a>Debuguj błędy podczas uruchamiania aplikacji poleceń niestandardowych

W tym artykule opisano sposób debugowania w przypadku wyświetlenia błędów podczas uruchamiania aplikacji poleceń niestandardowych. 

## <a name="connection-failed"></a>Nie można nawiązać połączenia

Jeśli aplikacja do uruchamiania poleceń niestandardowych z [aplikacji klienckiej (z zestawem SDK mowy)](./how-to-custom-commands-setup-speech-sdk.md) lub [Klient asystenta głosowego systemu Windows](./how-to-custom-commands-developer-flow-test.md), mogą wystąpić błędy połączeń wymienione poniżej:

| Kod błędu | Szczegóły |
| ------- | -------- |
| [401](#error-401) | AuthenticationFailure: uaktualnienie protokołu WebSocket nie powiodło się z powodu błędu uwierzytelniania |
| [1002](#error-1002)] | Serwer zwrócił kod stanu "404", gdy oczekiwano kodu stanu "101". |

### <a name="error-401"></a>Błąd 401
- Region określony w aplikacji klienckiej jest niezgodny z regionem aplikacji polecenia niestandardowego

- Klucz zasobu mowy jest nieprawidłowy
    
    Upewnij się, że klucz zasobu mowy jest prawidłowy.

### <a name="error-1002"></a>Błąd 1002 
- Aplikacja polecenia niestandardowego nie jest opublikowana
    
    Opublikuj aplikację w portalu.

- Niestandardowa identyfikator aplikacji polecenia jest nieprawidłowa

    Upewnij się, że identyfikator aplikacji polecenia niestandardowego jest poprawny.
 Niestandardowa aplikacja poleceń poza zasobem mowy

    Upewnij się, że aplikacja polecenia niestandardowego jest tworzona w ramach zasobu mowy.

Aby uzyskać więcej informacji na temat rozwiązywania problemów z połączeniem, odwołuje się do programu [asystenta głosowego systemu Windows](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/tree/master/clients/csharp-wpf#troubleshooting) .


## <a name="dialog-is-canceled"></a>Okno dialogowe zostało anulowane

Podczas uruchamiania aplikacji poleceń niestandardowych okno dialogowe zostało anulowane w przypadku wystąpienia niektórych błędów.

- Jeśli testujesz aplikację w portalu, będzie ona mogła bezpośrednio wyświetlić opis anulowania i odtworzyć błąd earcon. 

- Jeśli uruchamiasz aplikację za pomocą [klienta asystenta głosowego systemu Windows](./how-to-custom-commands-developer-flow-test.md), earcon błąd. Możesz znaleźć **wydarzenie: CancelledDialog** w **dziennikach aktywności**.

- W przypadku korzystania z aplikacji klienckiej przykładowej aplikacji klienckiej [(z zestawem SDK mowy)](./how-to-custom-commands-setup-speech-sdk.md)earcon błąd. Możesz znaleźć **wydarzenie: CancelledDialog** pod **stanem**.

- Jeśli tworzysz własną aplikację kliencką, zawsze możesz zaprojektować odpowiednie logiki, aby obsługiwać zdarzenia CancelledDialog.

Zdarzenie CancelledDialog składa się z kodu i opisu anulowania, jak wymieniono poniżej:

| Kod anulowania | Opis anulowania |
| ------- | --------------- | ----------- |
| [MaxTurnThresholdReached](#no-progress-was-made-after-the-max-number-of-turns-allowed) | Nie wykonano postępu po maksymalnej dozwolonej liczbie przekroczeń |
| [RecognizerQuotaExceeded](#recognizer-usage-quota-exceeded) | Przekroczono limit przydziału użycia aparatu rozpoznawania |
| [RecognizerConnectionFailed](#connection-to-the-recognizer-failed) | Nie można nawiązać połączenia z aparatem rozpoznawania |
| [RecognizerUnauthorized](#this-application-cannot-be-accessed-with-the-current-subscription) | Nie można uzyskać dostępu do tej aplikacji przy użyciu bieżącej subskrypcji |
| [RecognizerInputExceededAllowedLength](#input-exceeds-the-maximum-supported-length) | Dane wejściowe przekraczają maksymalną obsługiwaną długość dla aparatu rozpoznawania |
| [RecognizerNotFound](#recognizer-not-found) | Nie znaleziono aparatu rozpoznawania |
| [RecognizerInvalidQuery](#invalid-query-for-the-recognizer) | Nieprawidłowe zapytanie dla aparatu rozpoznawania |
| [RecognizerError](#recognizer-return-an-error) | Aparat rozpoznawania zwraca błąd |

### <a name="no-progress-was-made-after-the-max-number-of-turns-allowed"></a>Nie wykonano postępu po maksymalnej dozwolonej liczbie przekroczeń
Okno dialogowe zostało anulowane, gdy wymagane miejsce nie zostało pomyślnie zaktualizowane po określonej liczbie przedziałów. Maksymalna liczba kompilacji to 3.

### <a name="recognizer-usage-quota-exceeded"></a>Przekroczono limit przydziału użycia aparatu rozpoznawania
Language Understanding (LUIS) ma limity użycia zasobów. Zwykle "błąd przekroczenia limitu przydziału użycia aparatu rozpoznawania" może być spowodowany przez: 
- LUIS tworzenie przekracza limit

    Dodaj zasób predykcyjny do aplikacji poleceń niestandardowych: 
    1. Przejdź do pozycji **Ustawienia**, zasób Luis
    1. Wybierz zasób przewidywania z **zasobów predykcyjnych** lub kliknij pozycję **Utwórz nowy zasób** . 

- Zasób predykcyjny LUIS przekracza limit

    Jeśli korzystasz z zasobu przewidywania F0, obowiązuje limit 10 tysięcy/miesiąc, 5 zapytań na sekundę.

Aby uzyskać więcej informacji na temat limitów zasobów LUIS, zobacz temat [użycie i limit zasobów Language Understanding](../luis/luis-limits.md#resource-usage-and-limits)

### <a name="connection-to-the-recognizer-failed"></a>Nie można nawiązać połączenia z aparatem rozpoznawania
Zazwyczaj oznacza to, że przejściowy błąd połączenia Language Understanding (LUIS). Spróbuj ponownie, a problem powinien zostać rozwiązany.

### <a name="this-application-cannot-be-accessed-with-the-current-subscription"></a>Nie można uzyskać dostępu do tej aplikacji przy użyciu bieżącej subskrypcji
Twoja subskrypcja nie ma autoryzacji do uzyskania dostępu do aplikacji LUIS. 

### <a name="input-exceeds-the-maximum-supported-length"></a>Dane wejściowe przekraczają maksymalną obsługiwaną Długość
Twoje dane wejściowe przekroczyły 500 znaków. Dozwolony jest tylko od 500 znaków do wypowiedź danych wejściowych.

### <a name="invalid-query-for-the-recognizer"></a>Nieprawidłowe zapytanie dla aparatu rozpoznawania
Twoje dane wejściowe przekroczyły 500 znaków. Dozwolony jest tylko od 500 znaków do wypowiedź danych wejściowych.

### <a name="recognizer-return-an-error"></a>Aparat rozpoznawania zwraca błąd
Aparat rozpoznawania LUIS zwrócił błąd podczas próby rozpoznania danych wejściowych.

### <a name="recognizer-not-found"></a>Nie znaleziono aparatu rozpoznawania
Nie można znaleźć typu aparatu rozpoznawania określonego w modelu okna dialogowego poleceń niestandardowych. Obecnie obsługujemy tylko [aparat rozpoznawania Language Understanding (Luis)](https://www.luis.ai/).

## <a name="other-common-errors"></a>Inne typowe błędy
### <a name="unexpected-response"></a>Nieoczekiwana odpowiedź
Nieoczekiwane odpowiedzi mogą być spowodowane wieloma elementami. Kilka testów do rozpoczęcia z:
- Tak/nie ma intencji w przykładowych zdaniach

    Obecnie nie obsługujemy opcji tak/nie, z wyjątkiem sytuacji, gdy jest używana funkcja z potwierdzeniem. Nie zostaną wykryte wszystkie zamiary tak/nie zdefiniowane w przykładowych zdaniach.

- Podobne intencje i przykładowe zdania między poleceniami

    Dokładność rozpoznawania LUIS może się pojawić, jeśli dwa polecenia mają podobne zamiary i przykładowe zdania. Możesz spróbować wykonać funkcje poleceń i przykładowe zdania jak to możliwe.

    Najlepszym rozwiązaniem w zakresie zwiększenia dokładności rozpoznawania jest [Luis najlepszych](../luis/luis-concept-best-practices.md)rozwiązań.

- Okno dialogowe zostało anulowane
    
    Sprawdź przyczyny anulowania w powyższej sekcji.

### <a name="error-while-rendering-the-template"></a>Wystąpił błąd podczas renderowania szablonu
W odpowiedzi na mowę użyto niezdefiniowanego parametru. 

### <a name="object-reference-not-set-to-an-instance-of-an-object"></a>Odwołanie do obiektu nie jest ustawione na wystąpienie obiektu
W ładunku JSON zdefiniowanego w akcji **Wyślij do klienta** istnieje pusty parametr.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zobacz przykłady w witrynie GitHub](https://aka.ms/speech/cc-samples)