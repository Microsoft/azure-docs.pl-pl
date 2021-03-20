---
title: 'Samouczek: rejestrowanie aplikacji jednostronicowej'
titleSuffix: Azure AD B2C
description: Postępuj zgodnie z tym samouczkiem, aby dowiedzieć się, jak zarejestrować aplikację jednostronicową (SPA) w Azure Active Directory B2C przy użyciu Azure Portal.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 08/19/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d2eaf1dce432821dcfc693dc69dcf975a3d8be8d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92503865"
---
# <a name="tutorial-register-a-single-page-application-spa-in-azure-active-directory-b2c"></a>Samouczek: rejestrowanie aplikacji jednostronicowej (SPA) w Azure Active Directory B2C

Aby [aplikacje](application-types.md) mogły korzystać z Azure Active Directory B2C (Azure AD B2C), należy je zarejestrować w dzierżawie, którą zarządzasz. W tym samouczku pokazano, jak zarejestrować aplikację jednostronicową (SPA) przy użyciu Azure Portal.

## <a name="overview-of-authentication-options"></a>Przegląd opcji uwierzytelniania

Wiele nowoczesnych aplikacji sieci Web jest skompilowanych jako aplikacje jednostronicowe po stronie klienta ("aplikacji jednostronicowych"). Deweloperzy zapisują je przy użyciu języka JavaScript lub środowiska SPA, takiego jak kątowy, Vue i reagują. Aplikacje te działają w przeglądarce internetowej i mają inne cechy uwierzytelniania niż tradycyjne aplikacje sieci Web po stronie serwera.

Azure AD B2C dostępne są **dwie** opcje umożliwiające jednostronicowe aplikacje logowania użytkowników i uzyskiwanie tokenów w celu uzyskania dostępu do usług zaplecza lub interfejsów API sieci Web:

### <a name="authorization-code-flow-with-pkce"></a>Przepływ kodu autoryzacji (z PKCE)
- [Przepływ kodu autoryzacji OAuth 2,0 (z PKCE)](./authorization-code-flow.md). Przepływ kodu autoryzacji umożliwia aplikacji wymianę kodu autoryzacji dla tokenów **identyfikatora** , aby reprezentować tokeny uwierzytelnionego użytkownika i **dostępu** , które są konieczne do wywołania chronionych interfejsów API. Ponadto zwraca tokeny **odświeżania** , które zapewniają długoterminowy dostęp do zasobów w imieniu użytkowników, bez konieczności interakcji z tymi użytkownikami. 

Jest to **zalecane** podejście. Tokeny odświeżania o ograniczonym okresie istnienia ułatwiają aplikacji dostosowanie do [nowoczesnej ochrony plików cookie w przeglądarce](../active-directory/develop/reference-third-party-cookies-spas.md), takich jak Safari itp.

Aby skorzystać z tego przepływu, aplikacja może używać biblioteki uwierzytelniania, która obsługuje tę funkcję, na przykład [MSAL.js 2. x](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa). 

![Aplikacje jednostronicowe — uwierzytelnianie](./media/tutorial-single-page-app/spa-app-auth.svg)

### <a name="implicit-grant-flow"></a>Niejawny przepływ dotacji
- [Niejawny przepływ OAuth 2,0](implicit-flow-single-page-application.md). Niektóre struktury, takie jak [MSAL.js 1. x](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp), obsługują tylko niejawny przepływ. Przepływ niejawnego przydzielenia umożliwia aplikacji uzyskanie tokenów dotyczących **identyfikatorów** i **dostępu** . W przeciwieństwie do przepływu kodu autoryzacji niejawny przepływ przyznania nie zwraca **tokenu odświeżania**. 

![Aplikacje jednostronicowe — niejawne](./media/tutorial-single-page-app/spa-app.svg)

Ten przepływ uwierzytelniania nie obejmuje scenariuszy aplikacji, które wykorzystują Międzyplatformowe struktury JavaScript, takie jak elektron i reagowanie na nie. Te scenariusze wymagają dalszych możliwości interakcji z natywnymi platformami.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Jeśli nie utworzono jeszcze własnej [dzierżawy Azure AD B2C](tutorial-create-tenant.md), utwórz ją teraz. Możesz użyć istniejącej dzierżawy Azure AD B2C.

## <a name="register-the-spa-application"></a>Rejestrowanie aplikacji SPA

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Na pasku narzędzi portalu wybierz ikonę **katalog i subskrypcję** , a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.
1. W Azure Portal Wyszukaj i wybierz pozycję **Azure AD B2C**.
1. Wybierz pozycję **rejestracje aplikacji**, a następnie wybierz pozycję **Nowa rejestracja**.
1. Wprowadź **nazwę** aplikacji. Na przykład *spaapp1*.
1. W obszarze **obsługiwane typy kont** wybierz pozycję **konta w dowolnym dostawcy tożsamości lub katalogu organizacji (do uwierzytelniania użytkowników za pomocą przepływów użytkowników).**
1. W obszarze **Identyfikator URI przekierowania** wybierz pozycję **aplikacja jednostronicowa (Spa)**, a następnie wprowadź `https://jwt.ms` wartość w polu tekstowym adres URL.

    Identyfikator URI przekierowania jest punktem końcowym, do którego użytkownik jest wysyłany przez serwer autoryzacji (Azure AD B2C w tym przypadku) po zakończeniu interakcji z użytkownikiem oraz do którego zostanie wysłany token dostępu lub kod autoryzacji po pomyślnej autoryzacji. W aplikacji produkcyjnej zwykle jest dostępny publicznie punkt końcowy, w którym działa aplikacja, na przykład `https://contoso.com/auth-response` . Do celów testowych, takich jak ten samouczek, można ustawić dla niego `https://jwt.ms` aplikację sieci Web firmy Microsoft, która wyświetla zdekodowaną zawartość tokenu (zawartość tokenu nigdy nie opuszcza przeglądarki). Podczas tworzenia aplikacji możesz dodać punkt końcowy, w którym aplikacja nasłuchuje lokalnie, na przykład `http://localhost:5000` . Można w dowolnym momencie dodawać i modyfikować identyfikatory URI przekierowań w zarejestrowanych aplikacjach.

    Identyfikatory URI przekierowania dotyczą następujących ograniczeń:

    * Adres URL odpowiedzi musi rozpoczynać się od schematu `https` , chyba że jest używany `localhost` .
    * W adresie URL odpowiedzi jest rozróżniana wielkość liter. Jego wielkość liter musi być zgodna z wielkością liter w ścieżce URL działającej aplikacji. Na przykład jeśli aplikacja zawiera jako część swojej ścieżki `.../abc/response-oidc` , nie należy określać jej `.../ABC/response-oidc` w adresie URL odpowiedzi. Ponieważ przeglądarka sieci Web traktuje ścieżki w miarę uwzględniania wielkości liter, pliki cookie skojarzone z programem `.../abc/response-oidc` mogą zostać wykluczone w przypadku przekierowania do niezgodnego z wielkością liter `.../ABC/response-oidc` adresów URL.

1. W obszarze **uprawnienia** zaznacz pole wyboru *Udziel zgody na uprawnienia administratora do OpenID Connect i offline_access* .
1. Wybierz pozycję **Zarejestruj**.


## <a name="enable-the-implicit-flow"></a>Włącz przepływ niejawny
W przypadku użycia niejawnego przepływu należy włączyć przepływ niejawnego przydzielenia w rejestracji aplikacji.

1. W menu po lewej stronie w obszarze **Zarządzaj** wybierz pozycję **uwierzytelnianie**.
1. W obszarze **niejawne przyznanie** zaznacz pola wyboru **tokeny dostępu** i **tokeny identyfikatora** .
1. Wybierz pozycję **Zapisz**.

## <a name="migrate-from-the-implicit-flow"></a>Migrowanie z niejawnego przepływu

Jeśli masz istniejącą aplikację, która używa przepływu niejawnego, zalecamy przeprowadzenie migracji do korzystania z przepływu kodu autoryzacji przy użyciu struktury, która obsługuje tę funkcję, na przykład [MSAL.js 2. x](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser).

Jeśli wszystkie produkcyjne aplikacje jednostronicowe reprezentowane przez rejestrację aplikacji używają przepływu kodu autoryzacji, należy wyłączyć ustawienia przepływu niejawnego. 

1. W menu po lewej stronie w obszarze **Zarządzaj** wybierz pozycję **uwierzytelnianie**.
1. W obszarze **niejawne przyznanie** Usuń zaznaczenie pól wyboru **tokeny dostępu** i **tokeny identyfikatora** .
1. Wybierz pozycję **Zapisz**.

Aplikacje korzystające z niejawnego przepływu mogą nadal działać w przypadku pozostawienia włączonego przepływu niejawnego (zaznaczone).

* * *

## <a name="next-steps"></a>Następne kroki

Następnie Dowiedz się, jak tworzyć przepływy użytkowników, aby umożliwić użytkownikom rejestrowanie i logowanie się oraz zarządzanie profilami.

> [!div class="nextstepaction"]
> [Tworzenie przepływów użytkowników w Azure Active Directory B2C >](tutorial-create-user-flows.md)
