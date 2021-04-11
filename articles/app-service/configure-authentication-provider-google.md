---
title: Skonfiguruj uwierzytelnianie Google
description: Dowiedz się, jak skonfigurować uwierzytelnianie Google jako dostawcę tożsamości dla aplikacji App Service lub Azure Functions.
ms.assetid: 2b2f9abf-9120-4aac-ac5b-4a268d9b6e2b
ms.topic: article
ms.date: 03/29/2021
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: f6bec32fa928e840569ed95c35a056db91ea9737
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106077996"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-google-login"></a>Skonfiguruj App Service lub aplikację Azure Functions do korzystania z logowania Google

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

W tym temacie opisano sposób konfigurowania Azure App Service lub Azure Functions do korzystania z usługi Google jako dostawcy uwierzytelniania.

Aby wykonać procedurę opisaną w tym temacie, musisz mieć konto Google, które ma zweryfikowany adres e-mail. Aby utworzyć nowe konto Google, przejdź do strony [accounts.google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302).

## <a name="register-your-application-with-google"></a><a name="register"> </a>Zarejestruj swoją aplikację w usłudze Google

1. Aby utworzyć identyfikator klienta i klucz tajny klienta, postępuj zgodnie z dokumentacją Google w witrynie [google Sign-In](https://developers.google.com/identity/sign-in/web/server-side-flow) . Nie ma potrzeby wprowadzania żadnych zmian w kodzie. Po prostu Użyj następujących informacji:
    - W przypadku **autoryzowanych źródeł języka JavaScript** Użyj `https://<app-name>.azurewebsites.net` nazwy aplikacji w temacie *\<app-name>* .
    - Aby uzyskać **Autoryzowany identyfikator URI przekierowania**, użyj elementu `https://<app-name>.azurewebsites.net/.auth/login/google/callback` .
1. Skopiuj identyfikator aplikacji i wartości klucza tajnego aplikacji.

    > [!IMPORTANT]
    > Wpis tajny aplikacji jest ważnym poświadczeniem zabezpieczeń. Nie należy udostępniać tego klucza tajnego nikomu ani rozpowszechniać go w aplikacji klienckiej.

## <a name="add-google-information-to-your-application"></a><a name="secrets"> </a>Dodawanie informacji Google do aplikacji

1. Zaloguj się do [Azure Portal] i przejdź do swojej aplikacji.
1. W menu po lewej stronie wybierz pozycję **uwierzytelnianie** . Kliknij pozycję **Dodaj dostawcę tożsamości**.
1. Wybierz pozycję **Google** na liście rozwijanej dostawca tożsamości. Wklej wartości identyfikatora aplikacji i klucza tajnego aplikacji, które zostały uzyskane wcześniej.

    Wpis tajny będzie przechowywany jako [ustawienie aplikacji](./configure-common.md#configure-app-settings) do gniazd-Sticky o nazwie `GOOGLE_PROVIDER_AUTHENTICATION_SECRET` . Możesz później zaktualizować to ustawienie, aby użyć [odwołań Key Vault](./app-service-key-vault-references.md) , jeśli chcesz zarządzać wpisem tajnym w programie Azure Key Vault.

1. Jeśli jest to pierwszy dostawca tożsamości skonfigurowany dla aplikacji, zostanie wyświetlony monit z sekcją **Ustawienia uwierzytelniania App Service** . W przeciwnym razie możesz przejść do następnego kroku.
    
    Te opcje określają, w jaki sposób aplikacja reaguje na nieuwierzytelnione żądania, a wybór domyślny spowoduje przekierowanie wszystkich żądań logowania za pomocą tego nowego dostawcy. Możesz zmienić to zachowanie Dostosuj teraz lub dostosować te ustawienia później na głównym ekranie **uwierzytelniania** , wybierając pozycję **Edytuj** obok pozycji **Ustawienia uwierzytelniania**. Aby dowiedzieć się więcej na temat tych opcji, zobacz [przepływ uwierzytelniania](overview-authentication-authorization.md#authentication-flow).

1. Obowiązkowe Kliknij przycisk **Dalej: zakresy** i Dodaj zakresy potrzebne aplikacji. Będą one wymagane w czasie logowania dla przepływów opartych na przeglądarce.
1. Kliknij pozycję **Dodaj**.

Teraz można przystąpić do uwierzytelniania w aplikacji za pomocą usługi Google. Dostawca zostanie wyświetlony na liście na ekranie **uwierzytelniania** . W tym miejscu możesz edytować lub usunąć tę konfigurację dostawcy.

## <a name="next-steps"></a><a name="related-content"> </a>Następne kroki

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: https://go.microsoft.com/fwlink/p/?LinkId=268303

[Witryna Azure Portal]: https://portal.azure.com/

