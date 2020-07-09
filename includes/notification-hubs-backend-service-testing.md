---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 5e75c5d5510f596eb7911cae0310e60b6bef67bf
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86146452"
---
### <a name="send-a-test-notification"></a>Wysyłanie powiadomienia testowego

1. Otwórz nową kartę w programie [Poster](https://www.postman.com/downloads/).

1. Ustaw żądanie na **wpis**, a następnie wprowadź następujący adres:

    ```xml
    https://<app_name>.azurewebsites.net/api/notifications/requests
    ```

1. Jeśli wybrano opcję ukończenia [uwierzytelniania klientów przy użyciu klucza interfejsu API](#authenticate-clients-using-an-api-key-optional) , należy skonfigurować nagłówki żądania, aby zawierały wartość **apikey** .

   | Klucz                            | Wartość                          |
   | ------------------------------ | ------------------------------ |
   | apikey                         | <your_api_key>                 |

1. Wybierz opcję **RAW** dla **treści**, a następnie wybierz pozycję **JSON** z listy opcje formatu, a następnie Dołącz niepewną zawartość **JSON** :

    ```json
    {
        "text": "Message from Postman!",
        "action": "action_a"
    }
    ```

1. Wybierz przycisk **kod** , który znajduje się poniżej przycisku **Zapisz** w prawym górnym rogu okna. Żądanie powinno wyglądać podobnie do poniższego przykładu w przypadku wyświetlania **kodu HTML** (w zależności od tego, czy został dołączony nagłówek **apikey** ):

    ```html
    POST /api/notifications/requests HTTP/1.1
    Host: https://<app_name>.azurewebsites.net
    apikey: <your_api_key>
    Content-Type: application/json

    {
        "text": "Message from backend service",
        "action": "action_a"
    }
    ```

1. Uruchom aplikację **PushDemo** na jednej lub obu platformach docelowych (**Android** i **iOS**).

    > [!NOTE]
    > W przypadku testowania w systemie **Android** upewnij się, że nie uruchomiono **debugowania**lub jeśli aplikacja została wdrożona przez uruchomienie aplikacji, Wymuś zamknięcie aplikacji i jej ponowne uruchomienie przy użyciu programu uruchamiającego.

1. W aplikacji **PushDemo** naciśnij przycisk **zarejestruj** .

1. Z powrotem w programie **[Poster](https://www.postman.com/downloads)** Zamknij okno **Generuj fragmenty kodu** (jeśli jeszcze tego nie zrobiono), a następnie kliknij przycisk **Wyślij** .

1. Sprawdź, czy w programie **[Poster](https://www.postman.com/downloads)** znajduje się odpowiedź **200 OK** i czy alert pojawia się w aplikacji z **odebraną akcją Action**.  

1. Zamknij aplikację **PushDemo** , a następnie ponownie kliknij przycisk **Wyślij** **[.](https://www.postman.com/downloads)**

1. Sprawdź, czy ponownie otrzymujesz odpowiedź **200 OK** w **[ogłoszeniu](https://www.postman.com/downloads)** . Sprawdź, czy w obszarze powiadomień dla aplikacji **PushDemo** jest wyświetlana informacja o poprawnym komunikacie.

1. Naciśnij pozycję powiadomienie, aby upewnić się, że aplikacja zostanie otwarta i zostanie wyświetlona **Akcja akcja akcji odebrana** .

1. Z powrotem w programie **[Poster](https://www.postman.com/downloads)** zmodyfikuj poprzednią treść żądania, aby wysłać powiadomienie dyskretne, określając *action_b* zamiast *action_a* dla wartości **akcji** .

    ```json
    {
        "action": "action_b",
        "silent": true
    }
    ```

1. Gdy aplikacja jest nadal otwarta, kliknij przycisk **Wyślij** w programie **[Poster](https://www.postman.com/downloads)**.

1. Sprawdź, czy otrzymujesz odpowiedź na **200 OK** w programie **[Poster](https://www.postman.com/downloads)** i czy alert pojawia się w aplikacji pokazującej **odebraną akcję ActionB** zamiast akcji **Action**.

1. Zamknij aplikację **PushDemo** , a następnie ponownie kliknij przycisk **Wyślij** **[.](https://www.postman.com/downloads)**

1. Sprawdź, czy otrzymujesz odpowiedź na **200 OK** w programie **[Poster](https://www.postman.com/downloads)** i czy powiadomienie dyskretne nie jest wyświetlane w obszarze powiadomień.
