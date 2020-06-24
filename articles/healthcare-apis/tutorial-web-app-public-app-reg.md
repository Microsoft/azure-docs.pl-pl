---
title: Samouczek aplikacji sieci Web — konfiguracja aplikacji klienckiej
description: W tym samouczku przedstawiono kroki rejestrowania aplikacji publicznej na potrzeby przygotowania do wdrożenia aplikacji sieci Web
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: mihansen
ms.author: cavoeg
author: caitlinv39
ms.date: 01/03/2020
ms.openlocfilehash: 58e21e46edfe1d03d42bf2202bcf1f22282631a9
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2020
ms.locfileid: "84872641"
---
# <a name="client-application-registration"></a>Rejestracja aplikacji klienta
W poprzednim samouczku wdrożono i skonfigurowano interfejs API platformy Azure dla usługi FHIR. Po skonfigurowaniu interfejsu API platformy Azure dla usługi FHIR zostanie zarejestrowana publiczna aplikacja kliencka. Możesz zapoznać się z pełnym [zarejestrowaniem publicznej aplikacji klienckiej](register-public-azure-ad-client-app.md) , aby uzyskać więcej informacji lub rozwiązać problemy, ale w tym samouczku zostały zwołane najważniejsze kroki.

1. Przejdź do Azure Active Directory
1. Wybierz pozycję **Rejestracja aplikacji**  -->  **Nowa rejestracja**
1. Nazwij aplikację i skonfiguruj identyfikator URI przekierowania nahttps://www.getpostman.com/oauth2/callback


![Rejestracja aplikacji klienta](media/tutorial-web-app/reg-public-app.png)

## <a name="client-application-settings"></a>Ustawienia aplikacji klienta
Po zarejestrowaniu aplikacji klienckiej Skopiuj identyfikator aplikacji (klienta) ze strony przegląd. Ta wartość będzie potrzebna później podczas uzyskiwania dostępu do klienta.

![Kopiuj identyfikator aplikacji](media/tutorial-web-app/app-id.png)

Następnie ustaw odpowiednie opcje uwierzytelniania. Wybierz pozycję **uwierzytelnianie** po lewej stronie. Sprawdź pola **token dostępu** i **token identyfikatora** . Możesz również skonfigurować identyfikator URI przekierowania w przygotowaniu do tworzenia aplikacji sieci Web w czwartej części tego samouczka. W tym celu Dodaj https:// \< Web-App-NAME>. azurewebsites.NET do listy URI przekierowania. Jeśli wybierzesz inną nazwę w kroku, w którym [napiszesz aplikację sieci Web](tutorial-web-app-write-web-app.md), musisz wrócić i zaktualizować ją.

![Ustawienia uwierzytelniania aplikacji](media/tutorial-web-app/app-authentication.png)

Teraz, po skonfigurowaniu prawidłowego uwierzytelniania, Ustaw uprawnienia interfejsu API. 
1. Wybierz pozycję **uprawnienia interfejsu API** i kliknij pozycję **Dodaj uprawnienie** .
1. W obszarze interfejsy API, do których **używa Moja organizacja**, Wyszukaj interfejsy API usługi Azure opieki zdrowotnej
1. Wybierz **user_impersonation** i kliknij przycisk **Dodaj uprawnienia**

## <a name="next-steps"></a>Następne kroki
Masz teraz publiczną aplikację kliencką. W następnym samouczku będziemy przeprowadzać testy i uzyskiwać dostęp do tej aplikacji za pomocą programu Poster.

>[!div class="nextstepaction"]
>[Testowanie aplikacji klienckiej w programie Poster](tutorial-web-app-test-postman.md)
