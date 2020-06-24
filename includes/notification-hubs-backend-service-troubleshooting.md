---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 343fef941870b6deaad777fac9b9d258d454aa2b
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2020
ms.locfileid: "85081423"
---
#### <a name="no-response-from-the-backend-service"></a>Brak odpowiedzi z usługi wewnętrznej bazy danych

Podczas testowania lokalnego upewnij się, że usługa zaplecza działa i używa poprawnego portu.

Jeśli testujesz **aplikację interfejsu API platformy Azure**, sprawdź, czy usługa jest uruchomiona i została wdrożona i została uruchomiona bez błędu.

Należy sprawdzić, czy adres podstawowy został określony poprawnie w programie **[Poster](https://www.postman.com/downloads)** lub w konfiguracji aplikacji mobilnej podczas testowania przez klienta. Adres podstawowy powinien indicatively być `https://<api_name>.azurewebsites.net/` lub `https://localhost:5001/` podczas testowania lokalnego.

#### <a name="not-receiving-notifications-on-android-after-starting-or-stopping-a-debug-session"></a>Nie odebrano powiadomień w systemie Android po rozpoczęciu lub zatrzymywaniu sesji debugowania

Pamiętaj o ponownym zarejestrowaniu po uruchomieniu lub zatrzymywaniu sesji debugowania. Debuger spowoduje wygenerowanie nowego tokenu **Firebase** . Należy również zaktualizować instalację centrum powiadomień.

#### <a name="receiving-a-401-status-code-from-the-backend-service"></a>Otrzymywanie kodu stanu 401 z usługi wewnętrznej bazy danych

Sprawdź, czy jest ustawiony nagłówek żądania **apikey** i czy ta wartość jest zgodna z konfiguracją skonfigurowaną dla usługi zaplecza.

Jeśli ten błąd występuje podczas testowania lokalnego, upewnij się, że wartość klucza zdefiniowana w konfiguracji klienta jest zgodna z wartością ustawienia użytkownika **uwierzytelnianie: ApiKey** używana przez [interfejs API](#create-the-api-app).

Jeśli testujesz za pomocą **aplikacji interfejsu API**, upewnij się, że wartość klucza w pliku konfiguracji klienta odpowiada ustawieniu **uwierzytelniania: ApiKey** używanym w [aplikacji interfejsu API](#create-the-api-app).

> [!NOTE]
> Jeśli to ustawienie zostało utworzone lub zmienione po wdrożeniu usługi wewnętrznej bazy danych, należy ponownie uruchomić usługę, aby zaczęła obowiązywać.

W przypadku wybrania opcji nie Dokończ [uwierzytelniania klientów przy użyciu klucza interfejsu API](#authenticate-clients-using-an-api-key-optional) upewnij się, że atrybut **Autoryzuj** nie został zastosowany do klasy **NotificationsController** .

#### <a name="receiving-a-404-status-code-from-the-backend-service"></a>Otrzymywanie kodu stanu 404 z usługi wewnętrznej bazy danych

Sprawdź, czy punkt końcowy i metoda żądania HTTP są poprawne. Na przykład punkty końcowe powinny indicatively:

- **[PUT]**`https://<api_name>.azurewebsites.net/api/notifications/installations`
- **[Usuń]**`https://<api_name>.azurewebsites.net/api/notifications/installations/<installation_id>`
- **[Post]**`https://<api_name>.azurewebsites.net/api/notifications/requests`

Lub podczas testowania lokalnego:

- **[PUT]**`https://localhost:5001/api/notifications/installations`
- **[Usuń]**`https://localhost:5001/api/notifications/installations/<installation_id>`
- **[Post]**`https://localhost:5001/api/notifications/requests`

Podczas określania adresu podstawowego w aplikacji klienckiej upewnij się, że jest ona zakończona `/` . Adres podstawowy powinien indicatively być `https://<api_name>.azurewebsites.net/` lub `https://localhost:5001/` podczas testowania lokalnego.

#### <a name="unable-to-register-and-a-notification-hub-error-message-is-displayed"></a>Nie można zarejestrować i zostanie wyświetlony komunikat o błędzie centrum powiadomień

Sprawdź, czy urządzenie testowe ma łączność sieciową. Następnie określ kod stanu odpowiedzi HTTP przez ustawienie punktu przerwania w celu sprawdzenia wartości właściwości **StatusCode** w **HttpResponse**.

Przejrzyj poprzednie sugestie dotyczące rozwiązywania problemów, jeśli są stosowane na podstawie kodu stanu.

Ustaw punkt przerwania w wierszach, które zwracają te konkretne kody stanu dla odpowiedniego interfejsu API. Następnie spróbuj wywołać usługę zaplecza, gdy debugujesz lokalnie.

Sprawdź, czy usługa zaplecza działa zgodnie z oczekiwaniami za pośrednictwem programu **[Poster](https://www.postman.com/downloads)** przy użyciu odpowiedniego ładunku. Użyj rzeczywistego ładunku utworzonego przez kod klienta dla danej platformy.

Zapoznaj się z sekcją konfiguracyjną specyficzną dla platformy, aby upewnić się, że nie zostały pominięte żadne kroki. Sprawdź, czy odpowiednie wartości są rozpoznawane dla `installation id` i `token` zmienne dla odpowiedniej platformy.

#### <a name="unable-to-resolve-an-id-for-the-device-error-message-is-displayed"></a>Nie można rozpoznać identyfikatora dla komunikatu o błędzie urządzenia

Zapoznaj się z sekcją konfiguracyjną specyficzną dla platformy, aby upewnić się, że nie zostały pominięte żadne kroki.
