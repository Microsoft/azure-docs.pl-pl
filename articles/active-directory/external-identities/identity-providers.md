---
title: Dostawcy tożsamości dla tożsamości zewnętrznych — Azure AD
description: Dowiedz się, jak używać usługi Azure AD jako domyślnego dostawcy tożsamości na potrzeby udostępniania użytkownikom zewnętrznym.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 03/02/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: bdef929b27c636b3908dd7a88eb93adc2382a53f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101687751"
---
# <a name="identity-providers-for-external-identities"></a>Dostawcy tożsamości dla tożsamości zewnętrznych

> [!NOTE]
> Niektóre funkcje wymienione w tym artykule stanowią publiczną funkcję w wersji zapoznawczej programu Azure Active Directory. Aby uzyskać więcej informacji na temat wersji zapoznawczych, zobacz [dodatkowe warunki użytkowania wersji](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)zapoznawczych Microsoft Azure.

*Dostawca tożsamości* tworzy, i przechowuje informacje dotyczące tożsamości oraz zarządza nimi, zapewniając jednocześnie aplikacjom usługi uwierzytelniania. W przypadku udostępniania aplikacji i zasobów użytkownikom zewnętrznym usługa Azure AD jest domyślnym dostawcą tożsamości na potrzeby udostępniania. Oznacza to, że w przypadku zapraszania użytkowników zewnętrznych, którzy już mają usługę Azure AD lub konto Microsoft, mogą oni automatycznie logować się bez dalszej konfiguracji.

Oprócz kont usługi Azure AD tożsamości zewnętrzne oferują wielu dostawców tożsamości.

- **Konta Microsoft** (wersja zapoznawcza): Użytkownicy-Goście mogą korzystać z własnych prywatnych konto Microsoft (MSA), aby zrealizować zaproszenia do współpracy B2B. Podczas konfigurowania przepływu samoobsługowego tworzenia konta użytkownika można dodać [konto Microsoft (wersja zapoznawcza)](microsoft-account.md) jako jednego z dozwolonych dostawców tożsamości. Żadna dodatkowa konfiguracja nie jest wymagana w celu udostępnienia tego dostawcy tożsamości dla przepływów użytkowników.

- **Wyślij wiadomość e-mail jednorazowo** (wersja zapoznawcza): podczas realizowania zaproszenia lub uzyskiwania dostępu do zasobu udostępnionego użytkownik-Gość może zażądać kodu tymczasowego, który jest wysyłany na adres e-mail. Następnie wprowadzają ten kod, aby kontynuować logowanie. Funkcja jednorazowego kodu dostępu wiadomości e-mail uwierzytelnia użytkowników gościa B2B, gdy nie można uwierzytelnić się w inny sposób. Podczas konfigurowania przepływu samoobsługowego tworzenia konta użytkownika możesz dodać **wiadomość e-mail One-Time kod dostępu (wersja zapoznawcza)** jako jednego z dozwolonych dostawców tożsamości. Wymagane są pewne czynności konfiguracyjne; Zobacz [jednorazowe uwierzytelnianie kodu dostępu za pośrednictwem poczty e-mail](one-time-passcode.md).

- **Google**: w usłudze Google Federation użytkownicy zewnętrzni mogą wykorzystać zaproszenia od Ciebie, logując się do aplikacji przy użyciu własnych kont usługi Gmail. Usługi Google Federation można także używać w przepływach użytkownika samoobsługowego rejestrowania. Zobacz, jak [dodać firmę Google jako dostawcę tożsamości](google-federation.md).
   > [!IMPORTANT]
   > **Od 4 stycznia 2021** firma Google jest [przestarzałą obsługą logowania do usługi WebView](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html). W przypadku korzystania z usługi Google Federation lub samoobsługowego rejestrowania się w usłudze Gmail należy [przetestować natywne aplikacje biznesowe pod kątem zgodności](google-federation.md#deprecation-of-webview-sign-in-support).

- **Facebook**: podczas kompilowania aplikacji można skonfigurować samoobsługowe rejestrowanie i włączyć Federacji w serwisie Facebook, dzięki czemu użytkownicy mogą zarejestrować się w aplikacji przy użyciu własnych kont w serwisie Facebook. Usługi Facebook można używać tylko w przypadku przepływów użytkowników samoobsługowego rejestrowania i nie są dostępne jako opcja logowania, gdy użytkownicy korzystają z zaproszeń. Zobacz, jak [dodać Facebook jako dostawcę tożsamości](facebook-federation.md).

- **Federacja bezpośrednia**: można również skonfigurować bezpośrednią Federacji z dowolnym zewnętrznym dostawcą tożsamości, który obsługuje protokoły SAML lub WS-Fed. Bezpośrednia Federacja umożliwia użytkownikom zewnętrznym realizowanie zaproszeń od Ciebie, logując się do aplikacji przy użyciu istniejących kont społecznościowych lub firmowych. Zobacz sposób [konfigurowania Federacji bezpośredniej](direct-federation.md).
   > [!NOTE]
   > Bezpośrednich dostawców tożsamości Federacji nie można używać w przepływach użytkownika samoobsługowego rejestrowania.

## <a name="adding-social-identity-providers"></a>Dodawanie dostawców tożsamości społecznościowych

Usługa Azure AD jest domyślnie włączona w celu samoobsługowego rejestrowania się, więc użytkownicy zawsze mają możliwość rejestracji przy użyciu konta usługi Azure AD. Można jednak włączyć innych dostawców tożsamości, w tym dostawców tożsamości społecznościowych, takich jak Google lub Facebook. Aby skonfigurować dostawców tożsamości społecznościowych w dzierżawie usługi Azure AD, należy utworzyć aplikację u dostawcy tożsamości i skonfigurować poświadczenia. Uzyskasz identyfikator klienta lub aplikacji oraz klucz tajny klienta lub aplikacji, który można następnie dodać do dzierżawy usługi Azure AD.

Po dodaniu dostawcy tożsamości do dzierżawy usługi Azure AD:

- W przypadku zapraszania użytkownika zewnętrznego do aplikacji lub zasobów w organizacji użytkownik zewnętrzny może zalogować się przy użyciu własnego konta z tym dostawcą tożsamości.
- Po włączeniu samoobsługowego [rejestrowania się](self-service-sign-up-overview.md) w aplikacjach użytkownicy zewnętrzni mogą zarejestrować się w aplikacjach przy użyciu własnych kont z dodanymi dostawcami tożsamości. Będą oni mogli wybierać spośród opcji dostawców tożsamości społecznościowych, które zostały udostępnione na stronie rejestracji:

   ![Zrzut ekranu przedstawiający ekran logowania z opcjami Google i Facebook](media/identity-providers/sign-in-with-social-identity.png)

Aby zapewnić optymalne środowisko logowania, sfederować się z dostawcami tożsamości wszędzie tam, gdzie to możliwe, aby zaproszony Goście mogli bezproblemowo korzystać z aplikacji.  

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak dodać dostawców tożsamości do logowania się do aplikacji, zapoznaj się z następującymi artykułami:

- [Dodaj wiadomość e-mail jednorazowe uwierzytelnianie kodu dostępu](one-time-passcode.md)
- [Dodaj firmę Google](google-federation.md) jako dozwolonego dostawcę tożsamości społecznościowej
- [Dodaj aplikację Facebook](facebook-federation.md) jako dozwolonego dostawcę tożsamości społecznościowej
- [Skonfiguruj bezpośrednią Federacji](direct-federation.md) z każdą organizacją, której dostawca tożsamości obsługuje protokół SAML 2,0 lub WS-Fed. Należy zauważyć, że Federacja bezpośrednia nie jest opcją dla przepływów użytkownika samoobsługowego rejestrowania.
