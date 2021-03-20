---
title: Przepływ logowania aplikacji przy użyciu platformy tożsamości firmy Microsoft | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się więcej o przepływie logowania aplikacji internetowych, klasycznych i mobilnych na platformie tożsamości firmy Microsoft.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/18/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 1f9f330ab140fa66b5a66a112c47ca2a68ba56bf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98755695"
---
# <a name="app-sign-in-flow-with-the-microsoft-identity-platform"></a>Przepływ logowania aplikacji z platformą tożsamości firmy Microsoft

W tym temacie omówiono podstawowy przepływ logowania dla aplikacji internetowych, klasycznych i mobilnych przy użyciu platformy tożsamości firmy Microsoft. Zobacz [przepływy uwierzytelniania i scenariusze aplikacji](authentication-flows-app-scenarios.md) , aby poznać scenariusze logowania obsługiwane przez platformę tożsamości firmy Microsoft.

## <a name="web-app-sign-in-flow"></a>Przepływ logowania aplikacji sieci Web

Gdy użytkownik nawiguje w przeglądarce do aplikacji sieci Web, następuje:

* Aplikacja sieci Web określa, czy użytkownik jest uwierzytelniany.
* Jeśli użytkownik nie jest uwierzytelniony, aplikacja internetowa deleguje do usługi Azure AD, aby zalogować użytkownika. Ten proces logowania będzie zgodny z zasadami organizacji, co może oznaczać, że użytkownik musi wprowadzić poświadczenia przy użyciu [uwierzytelniania wieloskładnikowego](../authentication/concept-mfa-howitworks.md) (czasami nazywanego uwierzytelnianiem dwuskładnikowym lub funkcji 2FA) lub nie używać hasła w ogóle (na przykład przy użyciu usługi Windows Hello).
* Użytkownik zostanie poproszony o zgodę na dostęp wymagany przez aplikację kliencką. Dlatego aplikacje klienckie muszą być zarejestrowane w usłudze Azure AD, dzięki czemu platforma tożsamości firmy Microsoft może dostarczać tokeny reprezentujące dostęp, do którego użytkownik wyraził zgodę.

Po pomyślnym uwierzytelnieniu użytkownika:

* Platforma tożsamości firmy Microsoft wysyła token do aplikacji sieci Web.
* Plik cookie jest zapisywany, skojarzony z domeną usługi Azure AD, która zawiera tożsamość użytkownika w pliku JAR cookie w przeglądarce. Przy następnym użyciu przeglądarki w celu przejścia do punktu końcowego autoryzacji platformy tożsamości firmy Microsoft przeglądarka prezentuje plik cookie, aby użytkownik nie musiał zalogować się ponownie. Jest to również sposób, w jaki uzyskuje się Logowanie jednokrotne. Plik cookie jest tworzony przez usługę Azure AD i może być zrozumiały tylko dla usługi Azure AD.
* Następnie aplikacja internetowa sprawdza poprawność tokenu. Jeśli sprawdzanie poprawności zakończy się pomyślnie, aplikacja sieci Web wyświetli chronioną stronę i zapisze plik cookie sesji w pliku cookie w przeglądarce. Gdy użytkownik nawiguje do innej strony, aplikacja sieci Web wie, że użytkownik jest uwierzytelniany w oparciu o plik cookie sesji.

Poniższy diagram sekwencji podsumowuje tę interakcję:

![proces uwierzytelniania aplikacji sieci Web](media/authentication-scenarios/web-app-how-it-appears-to-be.png)

### <a name="how-a-web-app-determines-if-the-user-is-authenticated"></a>Jak aplikacja internetowa decyduje o tym, czy użytkownik jest uwierzytelniony

Deweloperzy aplikacji sieci Web mogą wskazać, czy wszystkie lub tylko niektóre strony wymagają uwierzytelniania. Na przykład w ASP.NET/ASP.NET rdzeń jest to wykonywane przez dodanie `[Authorize]` atrybutu do akcji kontrolera.

Ten atrybut powoduje, że ASP.NET sprawdza obecność pliku cookie sesji zawierającego tożsamość użytkownika. Jeśli plik cookie nie istnieje, ASP.NET przekierowuje uwierzytelnianie do określonego dostawcy tożsamości. Jeśli dostawca tożsamości to usługa Azure AD, aplikacja sieci Web przekierowuje uwierzytelnianie do programu `https://login.microsoftonline.com` , co spowoduje wyświetlenie okna dialogowego logowania.

### <a name="how-a-web-app-delegates-sign-in-to-the-microsoft-identity-platform-and-obtains-a-token"></a>Jak aplikacja sieci Web deleguje logowanie do platformy tożsamości firmy Microsoft i uzyskuje token

Uwierzytelnianie użytkowników odbywa się za pośrednictwem przeglądarki. Protokół OpenID Connect używa standardowych komunikatów protokołu HTTP.

* Aplikacja sieci Web wysyła HTTP 302 (przekierowanie) do przeglądarki w celu korzystania z platformy tożsamości firmy Microsoft.
* Po uwierzytelnieniu użytkownika platforma tożsamości firmy Microsoft wysyła token do aplikacji sieci Web przy użyciu przekierowania za pośrednictwem przeglądarki.
* Przekierowanie jest dostarczane przez aplikację internetową w formie identyfikatora URI przekierowania. Ten identyfikator URI przekierowania jest zarejestrowany w obiekcie aplikacji usługi Azure AD. Może istnieć kilka identyfikatorów URI przekierowania, ponieważ aplikacja może zostać wdrożona w kilku adresach URL. W związku z tym aplikacja sieci Web będzie również musiała określić identyfikator URI przekierowania, który ma być używany.
* Usługa Azure AD weryfikuje, czy identyfikator URI przekierowania Wysłany przez aplikację sieci Web jest jednym z zarejestrowanych identyfikatorów URI przekierowania dla aplikacji.

## <a name="desktop-and-mobile-app-sign-in-flow"></a>Przepływ logowania aplikacji klasycznej i mobilnej

Opisany powyżej przepływ ma zastosowanie z niewielkimi różnicami w aplikacjach komputerowych i mobilnych.

Aplikacje klasyczne i mobilne mogą korzystać z osadzonego formantu sieci Web lub przeglądarki systemowej w celu uwierzytelniania. Na poniższym diagramie przedstawiono, w jaki sposób aplikacja klasyczna lub mobilna używa biblioteki uwierzytelniania firmy Microsoft (MSAL) w celu uzyskania tokenów dostępu i wywołania interfejsów API sieci Web.

![Aplikacja klasyczna, która wygląda](media/authentication-scenarios/desktop-app-how-it-appears-to-be.png)

MSAL używa przeglądarki do uzyskiwania tokenów. Podobnie jak w przypadku aplikacji sieci Web, uwierzytelnianie jest delegowane do platformy tożsamości firmy Microsoft.

Ponieważ usługa Azure AD zapisuje ten sam plik cookie tożsamości w przeglądarce, jak w przypadku aplikacji sieci Web, jeśli aplikacja natywna lub mobilna korzysta z przeglądarki systemu, natychmiast otrzyma Logowanie jednokrotne przy użyciu odpowiedniej aplikacji sieci Web.

Domyślnie MSAL używa przeglądarki systemowej. Wyjątkiem są .NET Framework aplikacje pulpitu, w których osadzony formant służy do zapewnienia bardziej zintegrowanego środowiska użytkownika.

## <a name="next-steps"></a>Następne kroki

W przypadku innych tematów obejmujących podstawowe informacje dotyczące uwierzytelniania i autoryzacji:

* Zobacz [uwierzytelnianie i autoryzacja](authentication-vs-authorization.md) , aby dowiedzieć się więcej na temat podstawowych pojęć związanych z uwierzytelnianiem i autoryzacją w usłudze Microsoft Identity platform.
* Zobacz [tokeny zabezpieczające](security-tokens.md) , aby dowiedzieć się, jak tokeny dostępu, tokeny odświeżania i tokeny identyfikatorów są używane podczas uwierzytelniania i autoryzacji.
* Zobacz [model aplikacji](application-model.md) , aby dowiedzieć się więcej o procesie rejestracji aplikacji, aby umożliwić integrację z platformą tożsamości firmy Microsoft.

Aby dowiedzieć się więcej o przepływie logowania do aplikacji:

* Zobacz [przepływy uwierzytelniania i scenariusze aplikacji](authentication-flows-app-scenarios.md) , aby dowiedzieć się więcej o innych scenariuszach uwierzytelniania użytkowników obsługiwanych przez platformę tożsamości firmy Microsoft.
* Zobacz [biblioteki MSAL](msal-overview.md) , aby dowiedzieć się więcej o bibliotekach firmy Microsoft, które ułatwiają tworzenie aplikacji współpracujących z kontami Microsoft, kontami usługi Azure AD i Azure AD B2C użytkowników w jednym, usprawnionym modelu programowania.
