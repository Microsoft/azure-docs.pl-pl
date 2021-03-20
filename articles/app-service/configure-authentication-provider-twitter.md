---
title: Konfigurowanie uwierzytelniania w usłudze Twitter
description: Dowiedz się, jak skonfigurować uwierzytelnianie w usłudze Twitter jako dostawcę tożsamości dla aplikacji App Service lub Azure Functions.
ms.assetid: c6dc91d7-30f6-448c-9f2d-8e91104cde73
ms.topic: article
ms.date: 02/28/2020
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: 11c913b12b4dcb7d2a5ffa532064b347b82904ef
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "80519910"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-twitter-login"></a>Skonfiguruj App Service lub aplikację Azure Functions do używania logowania do usługi Twitter

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

W tym artykule przedstawiono sposób konfigurowania Azure App Service lub Azure Functions do korzystania z usługi Twitter jako dostawcy uwierzytelniania.

Aby wykonać procedurę opisaną w tym artykule, musisz mieć konto w usłudze Twitter z zweryfikowanym adresem e-mail i numerem telefonu. Aby utworzyć nowe konto w usłudze Twitter, przejdź do [Twitter.com].

## <a name="register-your-application-with-twitter"></a><a name="register"> </a>Rejestrowanie aplikacji w usłudze Twitter

1. Zaloguj się do [Azure Portal] i przejdź do swojej aplikacji. Skopiuj **adres URL**. Zostanie ona użyta do skonfigurowania aplikacji usługi Twitter.
1. Przejdź do witryny sieci Web [deweloperów usługi Twitter] i zaloguj się przy użyciu poświadczeń konta w usłudze Twitter, a następnie wybierz pozycję **Utwórz aplikację**.
1. Wprowadź **nazwę aplikacji** i **Opis aplikacji** dla nowej aplikacji. Wklej **adres URL** aplikacji do pola **adres URL witryny sieci Web** . W sekcji **adresy URL wywołania zwrotnego** wprowadź adres URL protokołu HTTPS aplikacji App Service i dołącz ścieżkę `/.auth/login/twitter/callback` . Na przykład `https://contoso.azurewebsites.net/.auth/login/twitter/callback`.
1. W dolnej części strony wpisz co najmniej 100 znaków w polu **powiedz nam, jak będzie używana ta aplikacja**, a następnie wybierz pozycję **Utwórz**. Kliknij przycisk **Utwórz** ponownie w oknie podręcznym. Zostaną wyświetlone szczegóły aplikacji.
1. Wybierz kartę **klucze i tokeny dostępu** .

   Zanotuj te wartości:
   - Klucz interfejsu API
   - Klucz tajny interfejsu API

   > [!NOTE]
   > Klucz tajny interfejsu API jest ważnym poświadczeniem zabezpieczeń. Nie należy udostępniać tego klucza tajnego nikomu ani rozpowszechniać go wraz z Twoją aplikacją.

## <a name="add-twitter-information-to-your-application"></a><a name="secrets"> </a>Dodawanie informacji usługi Twitter do aplikacji

1. Przejdź do aplikacji w [Azure Portal].
1. Wybierz pozycję **Ustawienia**  >  **uwierzytelnianie/autoryzacja** i upewnij się, że **uwierzytelnianie App Service** jest **włączone**.
1. Wybierz pozycję **Twitter**.
1. Wklej `API key` `API secret key` wartości i, które zostały uzyskane wcześniej.
1. Wybierz przycisk **OK**.

   ![Zrzut ekranu ustawień usługi Twitter aplikacji mobilnych][1]

   Domyślnie App Service zapewnia uwierzytelnianie, ale nie ogranicza uprawnień dostępu do zawartości i interfejsów API witryny. Musisz autoryzować użytkowników w kodzie aplikacji.

1. Obowiązkowe Aby ograniczyć dostęp do witryny tylko do użytkowników uwierzytelnionych przez serwis Twitter, ustaw **akcję do wykonania, gdy żądanie nie zostanie uwierzytelnione** do usługi **Twitter**. Po ustawieniu tej funkcji aplikacja wymaga uwierzytelnienia wszystkich żądań. Przekierowuje także wszystkie nieuwierzytelnione żądania do usługi Twitter w celu uwierzytelnienia.

   > [!CAUTION]
   > Ograniczenie dostępu w ten sposób dotyczy wszystkich wywołań aplikacji, które mogą nie być pożądane dla aplikacji, które mają publicznie dostępną stronę główną, tak jak w przypadku aplikacji jednostronicowych. W przypadku takich aplikacji **Zezwalanie na żądania anonimowe (żadna akcja)** może być preferowana, aby aplikacja ręcznie uruchamiała sam uwierzytelnienie. Aby uzyskać więcej informacji, zobacz temat [przepływ uwierzytelniania](overview-authentication-authorization.md#authentication-flow).

1. Wybierz pozycję **Zapisz**.

Możesz teraz przystąpić do uwierzytelniania w aplikacji za pomocą usługi Twitter.

## <a name="next-steps"></a><a name="related-content"> </a>Następne kroki

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-twitter-authentication/app-service-twitter-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-twitter-authentication/mobile-app-twitter-settings.png

<!-- URLs. -->

[Deweloperzy usługi Twitter]: https://go.microsoft.com/fwlink/p/?LinkId=268300
[twitter.com]: https://go.microsoft.com/fwlink/p/?LinkID=268287
[Witryna Azure Portal]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md
