---
title: Konfigurowanie uwierzytelniania w usłudze Facebook
description: Dowiedz się, jak skonfigurować uwierzytelnianie w serwisie Facebook jako dostawcę tożsamości dla aplikacji App Service lub Azure Functions.
ms.assetid: b6b4f062-fcb4-47b3-b75a-ec4cb51a62fd
ms.topic: article
ms.date: 06/06/2019
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: b6aad323c0d6fa8f59c9fad203640c477b162503
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "80519956"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-facebook-login"></a>Skonfiguruj App Service lub aplikację Azure Functions do korzystania z logowania w serwisie Facebook

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

W tym artykule przedstawiono sposób konfigurowania Azure App Service lub Azure Functions do korzystania z serwisu Facebook jako dostawcy uwierzytelniania.

Aby wykonać procedurę opisaną w tym artykule, musisz mieć konto w serwisie Facebook z zweryfikowanym adresem e-mail i numerem telefonu komórkowego. Aby utworzyć nowe konto w usłudze Facebook, przejdź do [Facebook.com].

## <a name="register-your-application-with-facebook"></a><a name="register"> </a>Zarejestruj swoją aplikację w usłudze Facebook

1. Przejdź do witryny internetowej [deweloperów serwisu Facebook] i zaloguj się przy użyciu poświadczeń konta w serwisie Facebook.

   Jeśli nie masz konta w serwisie Facebook for Developers, wybierz pozycję **Rozpocznij pracę** i postępuj zgodnie z instrukcjami rejestracji.
1. Wybierz pozycję **Moje aplikacje**  >  **Dodaj nową aplikację**.
1. W polu **Nazwa wyświetlana** :
   1. Wpisz unikatową nazwę aplikacji.
   1. Podaj **kontaktowy adres e-mail**.
   1. Wybierz pozycję **Utwórz identyfikator aplikacji**.
   1. Ukończ sprawdzanie zabezpieczeń.

   Zostanie otwarty pulpit nawigacyjny dewelopera dla nowej aplikacji w serwisie Facebook.
1. Wybierz pozycję **pulpit nawigacyjny** logowanie do usługi  >  **Facebook**  >  **Konfiguracja**  >  **sieci Web**.
1. W lewym okienku nawigacji w obszarze **Logowanie do serwisu Facebook** wybierz pozycję **Ustawienia**.
1. W polu **prawidłowe identyfikatory URI przekierowania OAuth** wprowadź wartość `https://<app-name>.azurewebsites.net/.auth/login/facebook/callback` . Pamiętaj, aby zamienić na `<app-name>` nazwę aplikacji Azure App Service.
1. Wybierz pozycję **Zapisz zmiany**.
1. W lewym okienku wybierz pozycję **Ustawienia**  >  **podstawowe**. 
1. W polu **klucz tajny aplikacji** wybierz pozycję **Pokaż**. Skopiuj wartości **identyfikatora aplikacji** i **klucza tajnego aplikacji**. Są one używane później do konfigurowania aplikacji App Service na platformie Azure.

   > [!IMPORTANT]
   > Wpis tajny aplikacji jest ważnym poświadczeniem zabezpieczeń. Nie należy udostępniać tego klucza tajnego nikomu ani rozpowszechniać go w aplikacji klienckiej.
   >

1. Konto w serwisie Facebook użyte do zarejestrowania aplikacji jest administratorem aplikacji. W tym momencie tylko Administratorzy mogą logować się do tej aplikacji.

   Aby uwierzytelnić inne konta w usłudze Facebook, wybierz pozycję **Przegląd aplikacji** i Włącz opcję **Udostępnij \<your-app-name> publicznie** , aby umożliwić dostęp do aplikacji za pomocą uwierzytelniania w serwisie Facebook.

## <a name="add-facebook-information-to-your-application"></a><a name="secrets"> </a>Dodawanie informacji w serwisie Facebook do aplikacji

1. Zaloguj się do [Azure Portal] i przejdź do aplikacji App Service.
1. Wybierz pozycję **Ustawienia**  >  **uwierzytelnianie/autoryzacja** i upewnij się, że **uwierzytelnianie App Service** jest **włączone**.
1. Wybierz pozycję **Facebook**, a następnie wklej wartości w polu Identyfikator aplikacji i klucz tajny aplikacji, które zostały uzyskane wcześniej. Włącz wszystkie zakresy wymagane przez aplikację.
1. Wybierz przycisk **OK**.

   ![Zrzut ekranu ustawień serwisu Facebook aplikacji mobilnych][0]

    Domyślnie App Service zapewnia uwierzytelnianie, ale nie ogranicza uprawnień dostępu do zawartości i interfejsów API witryny. Musisz autoryzować użytkowników w kodzie aplikacji.
1. Obowiązkowe Aby ograniczyć dostęp tylko do użytkowników uwierzytelnionych przez serwis Facebook, ustaw **akcję do wykonania, gdy żądanie nie zostanie uwierzytelnione** do **serwisu Facebook**. Po ustawieniu tej funkcji aplikacja wymaga uwierzytelnienia wszystkich żądań. Przekierowuje także wszystkie nieuwierzytelnione żądania do usługi Facebook w celu uwierzytelnienia.

   > [!CAUTION]
   > Ograniczenie dostępu w ten sposób dotyczy wszystkich wywołań aplikacji, które mogą nie być pożądane dla aplikacji, które mają publicznie dostępną stronę główną, tak jak w przypadku aplikacji jednostronicowych. W przypadku takich aplikacji **Zezwalanie na żądania anonimowe (żadna akcja)** może być preferowana, aby aplikacja ręcznie uruchamiała sam uwierzytelnienie. Aby uzyskać więcej informacji, zobacz temat [przepływ uwierzytelniania](overview-authentication-authorization.md#authentication-flow).

1. Wybierz pozycję **Zapisz**.

Możesz teraz przystąpić do uwierzytelniania w aplikacji za pomocą usługi Facebook.

## <a name="next-steps"></a><a name="related-content"> </a>Następne kroki

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->
[0]: ./media/app-service-mobile-how-to-configure-facebook-authentication/mobile-app-facebook-settings.png

<!-- URLs. -->
[Deweloperzy serwisu Facebook]: https://go.microsoft.com/fwlink/p/?LinkId=268286
[facebook.com]: https://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/
[Witryna Azure Portal]: https://portal.azure.com/
