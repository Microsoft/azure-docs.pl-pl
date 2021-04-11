---
title: Konfigurowanie uwierzytelniania w usłudze Twitter
description: Dowiedz się, jak skonfigurować uwierzytelnianie w usłudze Twitter jako dostawcę tożsamości dla aplikacji App Service lub Azure Functions.
ms.assetid: c6dc91d7-30f6-448c-9f2d-8e91104cde73
ms.topic: article
ms.date: 03/29/2021
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: 6ecce954991d9f3901c54a6f87fc803b32469862
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106077979"
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

   > [!IMPORTANT]
   > Klucz tajny interfejsu API jest ważnym poświadczeniem zabezpieczeń. Nie należy udostępniać tego klucza tajnego nikomu ani rozpowszechniać go wraz z Twoją aplikacją.

## <a name="add-twitter-information-to-your-application"></a><a name="secrets"> </a>Dodawanie informacji usługi Twitter do aplikacji

1. Zaloguj się do [Azure Portal] i przejdź do swojej aplikacji.
1. W menu po lewej stronie wybierz pozycję **uwierzytelnianie** . Kliknij pozycję **Dodaj dostawcę tożsamości**.
1. Wybierz pozycję **Twitter** na liście rozwijanej dostawca tożsamości. Wklej `API key` `API secret key` wartości i, które zostały uzyskane wcześniej.

    Wpis tajny będzie przechowywany jako [ustawienie aplikacji](./configure-common.md#configure-app-settings) do gniazd-Sticky o nazwie `TWITTER_PROVIDER_AUTHENTICATION_SECRET` . Możesz później zaktualizować to ustawienie, aby użyć [odwołań Key Vault](./app-service-key-vault-references.md) , jeśli chcesz zarządzać wpisem tajnym w programie Azure Key Vault.

1. Jeśli jest to pierwszy dostawca tożsamości skonfigurowany dla aplikacji, zostanie wyświetlony monit z sekcją **Ustawienia uwierzytelniania App Service** . W przeciwnym razie możesz przejść do następnego kroku.
    
    Te opcje określają, w jaki sposób aplikacja reaguje na nieuwierzytelnione żądania, a wybór domyślny spowoduje przekierowanie wszystkich żądań logowania za pomocą tego nowego dostawcy. Możesz zmienić to zachowanie Dostosuj teraz lub dostosować te ustawienia później na głównym ekranie **uwierzytelniania** , wybierając pozycję **Edytuj** obok pozycji **Ustawienia uwierzytelniania**. Aby dowiedzieć się więcej na temat tych opcji, zobacz [przepływ uwierzytelniania](overview-authentication-authorization.md#authentication-flow).

1. Kliknij pozycję **Dodaj**.

Możesz teraz przystąpić do uwierzytelniania w aplikacji za pomocą usługi Twitter. Dostawca zostanie wyświetlony na liście na ekranie **uwierzytelniania** . W tym miejscu możesz edytować lub usunąć tę konfigurację dostawcy.

## <a name="next-steps"></a><a name="related-content"> </a>Następne kroki

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->

[Deweloperzy usługi Twitter]: https://go.microsoft.com/fwlink/p/?LinkId=268300
[twitter.com]: https://go.microsoft.com/fwlink/p/?LinkID=268287
[Witryna Azure Portal]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md
