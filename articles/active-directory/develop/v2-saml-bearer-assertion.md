---
title: Microsoft Identity platform & przepływ potwierdzenia elementu "okaziciela" elementu SAML | Azure
description: Dowiedz się, jak pobierać dane z Microsoft Graph bez monitowania użytkownika o poświadczenia przy użyciu przepływu potwierdzenia elementu "okaziciela".
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: kenwith
ms.reviewer: paulgarn
ms.openlocfilehash: 6e7e4dd6383b1f264ff2da7893d9f86a3708217d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89227920"
---
# <a name="microsoft-identity-platform-and-oauth-20-saml-bearer-assertion-flow"></a>Microsoft Identity platform i OAuth 2,0 SAML Flow Assertion
Przepływ potwierdzenia elementu "OAuth 2,0 SAML" umożliwia żądanie tokenu dostępu OAuth przy użyciu potwierdzenia SAML, gdy klient musi używać istniejącej relacji zaufania. Sygnatura zastosowana do potwierdzenia SAML zapewnia uwierzytelnianie autoryzowanej aplikacji. Potwierdzenie SAML to token zabezpieczający XML wystawiony przez dostawcę tożsamości i używany przez dostawcę usług. Dostawca usług opiera się na swojej zawartości, aby zidentyfikować podmiot potwierdzenia dla celów związanych z zabezpieczeniami.

Potwierdzenie SAML jest ogłaszane w punkcie końcowym tokenu OAuth.  Punkt końcowy przetwarza potwierdzenie i wystawia token dostępu w oparciu o wcześniejsze zatwierdzenie aplikacji. Klient nie musi mieć ani przechowywać tokenu odświeżania ani nie musi być wpisem tajnym klienta wymaganym do przesłania do punktu końcowego tokenu.

Przepływ potwierdzenia okaziciela języka SAML jest przydatny podczas pobierania danych z Microsoft Graph interfejsów API (które obsługują tylko uprawnienia delegowane) bez monitowania użytkownika o poświadczenia. W tym scenariuszu przydzielenie poświadczeń klienta, które jest preferowane dla procesów w tle, nie działa.

W przypadku aplikacji, które wykonują interaktywne logowanie oparte na przeglądarce w celu uzyskania potwierdzenia SAML, a następnie chcesz dodać dostęp do chronionego interfejsu API protokołu OAuth (na przykład Microsoft Graph), możesz utworzyć żądanie OAuth w celu uzyskania tokenu dostępu do interfejsu API. Gdy przeglądarka zostanie przekierowana do usługi Azure AD w celu uwierzytelnienia użytkownika, przeglądarka zostanie pobrana z logowania przy użyciu protokołu SAML, a użytkownik nie musi wprowadzać ich poświadczeń.

Przepływ potwierdzenia okaziciela protokołu OAuth jest obsługiwany tylko w przypadku użytkowników uwierzytelnianych przy użyciu dostawców tożsamości, takich jak federacyjne Active Directory Federation Services (ADFS) do Azure Active Directory.  Potwierdzenie SAML uzyskane z usług AD FS może być używane w przepływie protokołu OAuth w celu uwierzytelnienia użytkownika.

![Przepływ OAuth](./media/v2-saml-bearer-assertion/1.png)

## <a name="call-graph-using-saml-bearer-assertion"></a>Wywołaj wykres przy użyciu potwierdzenia elementu SAML okaziciela
Teraz poinformuj nas o tym, jak można w rzeczywistości pobrać programowo potwierdzenia SAML. Ta metoda jest testowana z usługami AD FS. Jednak działa to z dowolnym dostawcą tożsamości, który obsługuje zwrot SAML Assertion programowo. Podstawowy proces to: uzyskiwanie potwierdzenia SAML, uzyskiwanie tokenu dostępu i Microsoft Graph dostępu.

### <a name="prerequisites"></a>Wymagania wstępne

Ustanów relację zaufania między serwerem autoryzacji/środowiskiem (Microsoft 365) i dostawcą tożsamości lub wystawcą potwierdzenia odnoszącego się do programu SAML 2,0 (AD FS). Aby skonfigurować usługi AD FS na potrzeby logowania jednokrotnego i jako dostawcy tożsamości, można odwołać się do [tego artykułu](/archive/blogs/canitpro/step-by-step-setting-up-ad-fs-and-enabling-single-sign-on-to-office-365).

Zarejestruj aplikację w [portalu](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade):
1. Zaloguj się do [bloku Rejestracja aplikacji portalu](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) (należy pamiętać, że dla interfejs API programu Graph są używane punkty końcowe v 2.0, dlatego należy zarejestrować aplikację w tym portalu. W przeciwnym razie mogliśmy użyć rejestracji w usłudze Azure Active Directory). 
1. Wybierz pozycję **Nowa rejestracja**.
1. Po wyświetleniu strony **Rejestrowanie aplikacji** podaj informacje dotyczące rejestracji aplikacji: 
    1. **Nazwa** — podaj znaczącą nazwę aplikacji, która będzie wyświetlana użytkownikom aplikacji.
    1. **Obsługiwane typy konta** — wybierz konta, które aplikacja ma obsługiwać.
    1. **Identyfikator URI przekierowania (opcjonalnie)** — wybierz typ aplikacji, którą tworzysz, sieci Web lub klienta publicznego (Mobile & Desktop), a następnie wprowadź identyfikator URI przekierowania (lub adres URL odpowiedzi) dla aplikacji.
    1. Po zakończeniu wybierz pozycję **Rejestruj**.
1. Zanotuj identyfikator aplikacji (klienta).
1. W lewym okienku wybierz pozycję **certyfikaty & wpisy tajne**. Kliknij pozycję **Nowy wpis tajny klienta** w sekcji wpisy **tajne klienta** . Skopiuj nowy wpis tajny klienta, nie będzie można go pobrać po opuszczeniu bloku.
1. W lewym okienku wybierz pozycję **uprawnienia interfejsu API** , a następnie **Dodaj uprawnienie**. Wybierz **Microsoft Graph**, następnie **delegowane uprawnienia**, a następnie wybierz pozycję **zadania. Przeczytaj** , ponieważ zamierzamy użyć interfejs API programu Graph Outlook. 

Zainstaluj program [Poster](https://www.getpostman.com/), narzędzie wymagane do testowania przykładowych żądań.  Później można skonwertować żądania na kod.

### <a name="get-the-saml-assertion-from-adfs"></a>Pobieranie potwierdzenia SAML z usług ADFS
Utwórz żądanie POST w punkcie końcowym usług ADFS przy użyciu koperty protokołu SOAP, aby pobrać potwierdzenie SAML:

![Pobierz potwierdzenie SAML](./media/v2-saml-bearer-assertion/2.png)

Wartości nagłówka:

![Wartości nagłówka](./media/v2-saml-bearer-assertion/3.png)

Treść żądania ADFS:

![Treść żądania ADFS](./media/v2-saml-bearer-assertion/4.png)

Po pomyślnym opublikowaniu żądania należy odebrać potwierdzenie SAML z usług AD FS. Tylko dane znacznika **SAML: Assertion** są wymagane, przekonwertuj je na kodowanie Base64, aby użyć ich w dalszych żądaniach.

### <a name="get-the-oauth2-token-using-the-saml-assertion"></a>Pobieranie tokenu OAuth2 przy użyciu potwierdzenia SAML 
W tym kroku Pobierz token OAuth2 przy użyciu odpowiedzi potwierdzenia usług AD FS.

1. Utwórz żądanie POST, jak pokazano poniżej, korzystając z wartości nagłówka:

    ![Żądanie POST](./media/v2-saml-bearer-assertion/5.png)
1. W treści żądania Zastąp **client_id**, **client_secret**i **Assertion** (potwierdzenie protokołu SAML zakodowane algorytmem Base64 uzyskało poprzedni krok):

    ![Treść żądania](./media/v2-saml-bearer-assertion/6.png)
1. Po pomyślnym żądaniu otrzymasz token dostępu z usługi Azure Active Directory.

### <a name="get-the-data-with-the-oauth-token"></a>Pobieranie danych przy użyciu tokenu OAuth

Po odebraniu tokenu dostępu Wywołaj interfejsy API programu Graph (zadania w programie Outlook w tym przykładzie). 

1. Utwórz żądanie GET z tokenem dostępu pobranym w poprzednim kroku:

    ![Żądanie GET](./media/v2-saml-bearer-assertion/7.png)

1. Po pomyślnym żądaniu otrzymasz odpowiedź JSON.

## <a name="next-steps"></a>Następne kroki

Poznaj różne [przepływy uwierzytelniania i scenariusze aplikacji](authentication-flows-app-scenarios.md).
