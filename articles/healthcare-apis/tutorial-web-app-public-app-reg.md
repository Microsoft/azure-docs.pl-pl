---
title: Samouczek aplikacji sieci Web — konfiguracja aplikacji klienckiej
description: W tym samouczku przedstawiono kroki rejestrowania aplikacji publicznej na potrzeby przygotowania do wdrożenia aplikacji sieci Web
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: caitlinv39
ms.date: 01/03/2020
ms.openlocfilehash: f559c87a5c3285bf966f9389d623b2262e93cbca
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94425817"
---
# <a name="client-application-registration"></a>Rejestracja aplikacji klienta
W poprzednim samouczku wdrożono i skonfigurowano interfejs API platformy Azure dla usługi FHIR. Po skonfigurowaniu interfejsu API platformy Azure dla usługi FHIR zostanie zarejestrowana publiczna aplikacja kliencka. Możesz zapoznać się z pełnym [zarejestrowaniem publicznej aplikacji klienckiej](register-public-azure-ad-client-app.md) , aby uzyskać więcej informacji lub rozwiązać problemy, ale w tym samouczku zostały zwołane najważniejsze kroki.

1. Przejdź do Azure Active Directory
1. Wybierz pozycję **Rejestracja aplikacji**  -->  **Nowa rejestracja**
1. Nadaj nazwę aplikacji
1. Wybierz pozycję **publiczny Klient/natywny (mobile & Desktop)** i ustaw identyfikator URI przekierowania na https://www.getpostman.com/oauth2/callback .

   :::image type="content" source="media/tutorial-web-app/register-public-app.png" alt-text="Zrzut ekranu przedstawiający okienko Zarejestruj aplikację oraz przykładową nazwę aplikacji i adres URL przekierowania.":::

## <a name="client-application-settings"></a>Ustawienia aplikacji klienta

Po zarejestrowaniu aplikacji klienckiej Skopiuj identyfikator aplikacji (klienta) i identyfikator dzierżawy ze strony przegląd. Te dwie wartości będą potrzebne później podczas uzyskiwania dostępu do klienta.

:::image type="content" source="media/tutorial-web-app/client-id-tenant-id.png" alt-text="Zrzut ekranu przedstawiający okienko ustawienia aplikacji klienckiej z wyróżnionymi identyfikatorami aplikacji i katalogów.":::

### <a name="connect-with-web-app"></a>Łączenie z aplikacją sieci Web

Jeśli [aplikacja sieci Web została zapisywana](tutorial-web-app-write-web-app.md) w celu nawiązania połączenia z interfejsem API platformy Azure dla usługi FHIR, należy również ustawić odpowiednie opcje uwierzytelniania. 

1. W menu po lewej stronie w obszarze **Zarządzaj** wybierz pozycję **uwierzytelnianie**. 

1. Aby dodać nową konfigurację platformy, wybierz pozycję **Sieć Web**.

1. Skonfiguruj identyfikator URI przekierowania w przygotowaniu do tworzenia aplikacji sieci Web w czwartej części tego samouczka. Aby to zrobić, Dodaj `https://\<WEB-APP-NAME>.azurewebsites.net` do listy URI przekierowania. Jeśli wybierzesz inną nazwę w kroku, w którym [napiszesz aplikację sieci Web](tutorial-web-app-write-web-app.md), musisz wrócić i zaktualizować ją.

1. Zaznacz pola wyboru **token dostępu** i **token identyfikatora** .

   :::image type="content" source="media/tutorial-web-app/web-app-authentication.png" alt-text="Zrzut ekranu przedstawiający blok ustawień uwierzytelniania aplikacji z instrukcjami dodawania wyróżnionej platformy.":::

## <a name="add-api-permissions"></a>Dodawanie uprawnień do interfejsu API

Teraz, po skonfigurowaniu prawidłowego uwierzytelniania, Ustaw uprawnienia interfejsu API:

1. Wybierz pozycję **uprawnienia interfejsu API** i kliknij pozycję **Dodaj uprawnienie**.
1. W obszarze interfejsy API, które są **wykorzystywane przez moją organizację** , Wyszukaj interfejsy API usługi Azure opieki zdrowotnej.
1. Wybierz **user_impersonation** i kliknij przycisk **Dodaj uprawnienia**.

:::image type="content" source="media/tutorial-web-app/api-permissions.png" alt-text="Zrzut ekranu przedstawiający blok Dodawanie uprawnień interfejsu API z wyróżnionymi krokami dodawania uprawnień interfejsu API.":::

## <a name="next-steps"></a>Następne kroki
Masz teraz publiczną aplikację kliencką. W następnym samouczku będziemy przeprowadzać testy i uzyskiwać dostęp do tej aplikacji za pomocą programu Poster.

>[!div class="nextstepaction"]
>[Testowanie aplikacji klienckiej w programie Poster](tutorial-web-app-test-postman.md)
