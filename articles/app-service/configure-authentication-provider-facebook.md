---
title: Konfigurowanie uwierzytelniania w usłudze Facebook
description: Dowiedz się, jak skonfigurować uwierzytelnianie w serwisie Facebook jako dostawcę tożsamości dla aplikacji App Service lub Azure Functions.
ms.assetid: b6b4f062-fcb4-47b3-b75a-ec4cb51a62fd
ms.topic: article
ms.date: 03/29/2021
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: bc639eaea76b3309d6ed047e73c726040da19639
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078013"
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

1. Zaloguj się do [Azure Portal] i przejdź do swojej aplikacji.
1. W menu po lewej stronie wybierz pozycję **uwierzytelnianie** . Kliknij pozycję **Dodaj dostawcę tożsamości**.
1. Wybierz pozycję **Facebook** na liście rozwijanej dostawca tożsamości. Wklej wartości identyfikatora aplikacji i klucza tajnego aplikacji, które zostały uzyskane wcześniej.

    Wpis tajny będzie przechowywany jako [ustawienie aplikacji](./configure-common.md#configure-app-settings) do gniazd-Sticky o nazwie `FACEBOOK_PROVIDER_AUTHENTICATION_SECRET` . Możesz później zaktualizować to ustawienie, aby użyć [odwołań Key Vault](./app-service-key-vault-references.md) , jeśli chcesz zarządzać wpisem tajnym w programie Azure Key Vault.

1. Jeśli jest to pierwszy dostawca tożsamości skonfigurowany dla aplikacji, zostanie wyświetlony monit z sekcją **Ustawienia uwierzytelniania App Service** . W przeciwnym razie możesz przejść do następnego kroku.
    
    Te opcje określają, w jaki sposób aplikacja reaguje na nieuwierzytelnione żądania, a wybór domyślny spowoduje przekierowanie wszystkich żądań logowania za pomocą tego nowego dostawcy. Możesz zmienić to zachowanie Dostosuj teraz lub dostosować te ustawienia później na głównym ekranie **uwierzytelniania** , wybierając pozycję **Edytuj** obok pozycji **Ustawienia uwierzytelniania**. Aby dowiedzieć się więcej na temat tych opcji, zobacz [przepływ uwierzytelniania](overview-authentication-authorization.md#authentication-flow).

1. Obowiązkowe Kliknij przycisk **Dalej: zakresy** i Dodaj zakresy potrzebne aplikacji. Będą one wymagane w czasie logowania dla przepływów opartych na przeglądarce.
1. Kliknij pozycję **Dodaj**.

Możesz teraz przystąpić do uwierzytelniania w aplikacji za pomocą usługi Facebook. Dostawca zostanie wyświetlony na liście na ekranie **uwierzytelniania** . W tym miejscu możesz edytować lub usunąć tę konfigurację dostawcy.

## <a name="next-steps"></a><a name="related-content"> </a>Następne kroki

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->
[Deweloperzy serwisu Facebook]: https://go.microsoft.com/fwlink/p/?LinkId=268286
[facebook.com]: https://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/
[Witryna Azure Portal]: https://portal.azure.com/
