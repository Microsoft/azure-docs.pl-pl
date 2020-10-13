---
title: Nowe środowisko Rejestracje aplikacji w programie Azure AD B2C
description: Wprowadzenie do nowego środowiska rejestracji aplikacji w Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 05/25/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: eed0527b69dcaacd3a8cd0cf7cd178aa2aca3468
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89433912"
---
# <a name="the-new-app-registrations-experience-for-azure-active-directory-b2c"></a>Nowe środowisko Rejestracje aplikacji dla Azure Active Directory B2C

Nowe środowisko **[rejestracje aplikacji](https://aka.ms/b2cappregistrations)** dla Azure Active Directory B2C (Azure AD B2C) jest teraz ogólnie dostępne. Jeśli wiesz już, jak korzystać z **aplikacji** do rejestrowania aplikacji dla Azure AD B2C, o których mowa w tym miejscu jako "starsze środowisko", ten przewodnik umożliwi rozpoczęcie pracy przy użyciu nowego środowiska.

## <a name="overview"></a>Omówienie
Wcześniej należało zarządzać Azure AD B2C aplikacjami dostępnymi dla klientów niezależnie od reszty aplikacji przy użyciu starszego środowiska. Są to różne środowiska tworzenia aplikacji w różnych miejscach na platformie Azure.

Nowe środowisko pokazuje wszystkie Azure AD B2C rejestracje aplikacji i rejestracje aplikacji usługi Azure AD w jednym miejscu i zapewnia spójny sposób zarządzania nimi. Od tworzenia aplikacji dla klientów w celu zarządzania aplikacją przy użyciu uprawnień Microsoft Graph do zarządzania zasobami wystarczy tylko poznać jeden ze sposobów.

Aby uzyskać dostęp do nowego środowiska, przejdź do **rejestracje aplikacji** w dzierżawie Azure AD B2C z usług **Azure AD B2C** lub **Azure Active Directory** w Azure Portal.

Środowisko Rejestracje aplikacji Azure AD B2C jest oparte na ogólnej [rejestracji aplikacji](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) dla dowolnej dzierżawy usługi Azure AD, ale jest dostosowane do Azure AD B2C dzierżawców.

## <a name="whats-not-changing"></a>Co nie jest zmieniane?
- Aplikacje i powiązane konfiguracje można znaleźć w temacie w nowym środowisku. Nie trzeba ponownie rejestrować aplikacji, a użytkownicy aplikacji nie będą musieli zalogować się ponownie.

> [!NOTE]
> Aby wyświetlić wszystkie utworzone wcześniej aplikacje, przejdź do bloku **rejestracje aplikacji** i wybierz kartę **wszystkie aplikacje** . Spowoduje to wyświetlenie aplikacji utworzonych w starszym środowisku, nowym środowisku i tych utworzonych w usłudze Azure AD.

## <a name="key-new-features"></a>Najważniejsze nowe funkcje

-   **Ujednolicona lista aplikacji** zawiera wszystkie aplikacje, które uwierzytelniają się za pomocą Azure AD B2C i usługi Azure AD w jednym wygodnym miejscu. Ponadto można korzystać z funkcji dostępnych już dla aplikacji usługi Azure AD, w tym dla **utworzonych** dat, **certyfikatów & Stanów tajnych** , paska wyszukiwania i wielu innych.

-   **Rejestracja połączonej aplikacji** umożliwia szybkie zarejestrowanie aplikacji, niezależnie od tego, czy jest to aplikacja dołączona do klienta czy aplikacja, aby uzyskać dostęp do Microsoft Graph.

- Okienko **punkty końcowe** umożliwia szybkie identyfikowanie odpowiednich punktów końcowych dla danego scenariusza, w tym konfiguracji OpenID Connect Connect, metadanych SAML, interfejsu API Microsoft Graph oraz [punktów końcowych przepływu użytkownika protokołu OAuth 2,0](tokens-overview.md#endpoints).

- **Uprawnienia interfejsu API** i **Uwidacznianie interfejsu API** zapewniają bardziej rozległy zakres, uprawnienia i zarządzanie zgodą. Teraz można także przypisać do aplikacji uprawnienia programu MS Graph i usługi Azure AD Graph.

-   **Właściciele** i **manifest** są teraz dostępne dla aplikacji, które uwierzytelniają się za pomocą Azure AD B2C. Można dodać właścicieli do rejestracji i bezpośrednio edytować właściwości aplikacji [przy użyciu edytora manifestu](../active-directory/develop/reference-app-manifest.md).


## <a name="new-supported-account-types"></a>Nowe obsługiwane typy kont

W nowym środowisku wybierz typ konta pomocy technicznej z następujących opcji:
- Tylko konta w tym katalogu organizacji.
- Konta w dowolnym katalogu organizacji (dowolny katalog usługi Azure AD — wielodostępny).
- Konta w dowolnym katalogu organizacyjnym lub u dowolnego dostawcy tożsamości. Do uwierzytelniania użytkowników za pomocą Azure AD B2C.

Aby poznać różne typy kont, wybierz pozycję **Pomóż mi wybrać** w temacie środowisko tworzenia.

W starszym środowisku aplikacje były zawsze tworzone jako aplikacje dla klientów. Dla tych aplikacji typ konta jest ustawiony na **konta w dowolnym katalogu organizacyjnym lub u dowolnego dostawcy tożsamości. Do uwierzytelniania użytkowników za pomocą Azure AD B2C**.
> [!NOTE]
> Ta opcja jest wymagana, aby można było uruchamiać Azure AD B2C przepływów użytkowników w celu uwierzytelniania użytkowników dla tej aplikacji. Dowiedz się [, jak zarejestrować aplikację do użycia z przepływami użytkowników.](tutorial-register-applications.md)

Można również użyć tej opcji, aby użyć Azure AD B2C jako dostawcy usługi SAML. [Dowiedz się więcej](identity-provider-adfs2016-custom.md).

## <a name="applications-for-devops-scenarios"></a>Aplikacje dla scenariuszy DevOps
Przy użyciu innych typów kont można utworzyć aplikację do zarządzania scenariuszami DevOps, takich jak używanie Microsoft Graph do przekazywania zasad struktury środowiska tożsamości lub udostępniania użytkownikom. Dowiedz się, [jak zarejestrować aplikację Microsoft Graph, aby zarządzać zasobami Azure AD B2C](microsoft-graph-get-started.md).

Niektóre uprawnienia Microsoft Graph mogą nie być widoczne, ponieważ wiele z tych uprawnień nie ma zastosowania do użytkowników indywidualnych platformy Azure B2C. [Przeczytaj więcej na temat zarządzania użytkownikami za pomocą Microsoft Graph](manage-user-accounts-graph-api.md).

## <a name="admin-consent-and-offline_accessopenid-scopes"></a>Zgoda administratora i zakresy offline_access + OpenID Connect
<!-- Azure AD B2C doesn't support user consent. That is, when a user signs into an application, the user doesn't see a screen requesting consent for the application permissions. All permissions have to be granted through admin consent.  -->

Zakres **OpenID Connect** jest niezbędny, aby Azure AD B2C mógł logować użytkowników do aplikacji. Do wystawiania tokenów odświeżania dla użytkownika jest wymagany zakres **offline_access** . Te zakresy zostały wcześniej dodane i domyślnie podano zgodę administratora. Teraz można łatwo dodawać uprawnienia do tych zakresów podczas procesu tworzenia, upewniając się, że jest zaznaczona opcja **Udziel zgody na uprawnienia administratora do OpenID Connect i offline_access** . W przeciwnym razie uprawnienia Microsoft Graph mogą być dodawane z zgodą administratora w ustawieniach **uprawnień interfejsu API** dla istniejącej aplikacji.

Dowiedz się więcej o [uprawnieniach i zgodzie](../active-directory/develop/v2-permissions-and-consent.md).

## <a name="platformsauthentication-reply-urlsredirect-uris"></a>Platformy/uwierzytelnianie: adresy URL odpowiedzi/identyfikatory URI przekierowania
W starszych wersjach, różne typy platform były zarządzane w obszarze **Właściwości** jako adresy URL odpowiedzi dla aplikacji sieci Web/interfejsów API i URI przekierowania dla klientów natywnych. "Natywni klienci" są również nazywane "klientami publicznymi" i obejmują aplikacje dla systemów iOS, macOS, Android i innych typów aplikacji mobilnych i klasycznych.

W nowym środowisku adresy URL odpowiedzi i identyfikatory URI przekierowania są określane jako identyfikatory URI przekierowania i można je znaleźć w sekcji **uwierzytelniania** aplikacji. Rejestracje aplikacji nie są ograniczone do aplikacji sieci Web ani natywnej aplikacji. Możesz użyć tej samej rejestracji aplikacji dla wszystkich typów platform przez zarejestrowanie odpowiednich identyfikatorów URI przekierowania.

Identyfikatory URI przekierowania są wymagane do skojarzenia z typem aplikacji: Web lub Public (Mobile and Desktop). [Dowiedz się więcej na temat identyfikatorów URI przekierowania](../active-directory/develop/quickstart-register-app.md#add-a-redirect-uri)

<!-- Whether an application should be treated as a public client is inferred at run-time from the Redirect URI platform type, if possible. The **Treat application as a public client** setting should be set to **Yes** for flows that might not use a redirect URI, such as ROPC flows. -->

Platformy **iOS/macOS** i **Android** to typ klienta publicznego. Umożliwiają one łatwą konfigurację aplikacji iOS/macOS lub Android przy użyciu odpowiednich identyfikatorów URI przekierowania do użycia z MSAL. Dowiedz się więcej o [opcjach konfiguracji aplikacji](../active-directory/develop/msal-client-applications.md).


## <a name="application-certificates--secrets"></a>Certyfikaty aplikacji & wpisy tajne

W nowym środowisku zamiast **kluczy**należy użyć bloku **Certyfikaty & wpisy tajne** do zarządzania certyfikatami i wpisami tajnymi. Certyfikaty & wpisy tajne pozwalają aplikacjom identyfikować się do usługi uwierzytelniania w przypadku otrzymywania tokenów w lokalizacji adresowej sieci Web (przy użyciu schematu HTTPS). Podczas uwierzytelniania w usłudze Azure AD zalecamy używanie certyfikatu zamiast klucza tajnego klienta. Nie można używać certyfikatów do uwierzytelniania w odniesieniu do Azure AD B2C.


## <a name="features-not-applicable-in-azure-ad-b2c-tenants"></a>Funkcje niemające zastosowania w dzierżawach Azure AD B2C
Następujące możliwości rejestracji aplikacji usługi Azure AD nie mają zastosowania do dzierżaw Azure AD B2C ani do nich dostępnych:
- **Role i Administratorzy** — wymaga Azure AD — wersja Premium licencji P1 lub P2, która nie jest obecnie dostępna dla Azure AD B2C.
- **Znakowanie** — Dostosowywanie interfejsu użytkownika/środowiska użytkownika jest konfigurowane w środowisku **znakowania firmowego** lub w ramach przepływu pracy. Dowiedz się, jak [dostosować interfejs użytkownika w Azure Active Directory B2C](customize-ui-overview.md).
- **Weryfikacja domeny wydawcy** — Twoja aplikacja jest zarejestrowana w usłudze *. onmicrosoft.com*, która nie jest zweryfikowaną domeną. Ponadto domena wydawcy jest używana głównie do udzielania zgody użytkownika, która nie ma zastosowania do Azure AD B2C aplikacji do uwierzytelniania użytkowników. [Dowiedz się więcej o domenie wydawcy](https://docs.microsoft.com/azure/active-directory/develop/howto-configure-publisher-domain).
- **Konfiguracja tokenu** — token jest konfigurowany jako część przepływu użytkownika, a nie aplikacji.
- Środowisko **Szybki Start** nie jest obecnie dostępne dla dzierżawców Azure AD B2C.
- Blok **asystenta integracji** nie jest obecnie dostępny dla dzierżawców Azure AD B2C.


## <a name="limitations"></a>Ograniczenia
Nowe środowisko ma następujące ograniczenia:
- W tej chwili Azure AD B2C nie rozróżniają możliwości uzyskiwania dostępu lub tokenów identyfikatorów dla niejawnych przepływów. Oba typy tokenów są dostępne dla niejawnego przepływu dotacji, jeśli wybrano opcję **tokeny identyfikatora** w bloku **uwierzytelnianie** .
<!-- - Azure AD B2C doesn't currently support the single-page application "SPA" app type.  -->
- Zmiana wartości obsługiwanych kont nie jest obsługiwana w interfejsie użytkownika. Musisz użyć manifestu aplikacji, o ile nie przejdziesz do przełączania między jedną dzierżawą i wieloma dzierżawcami usługi Azure AD.

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć pracę z nowym interfejsem rejestracji aplikacji:
* Dowiedz się [, jak zarejestrować aplikację sieci Web](tutorial-register-applications.md).
* Dowiedz się [, jak zarejestrować internetowy interfejs API](add-web-api-application.md).
* Dowiedz się, [jak zarejestrować natywną aplikację kliencką](add-native-application.md).
* Dowiedz się, [jak zarejestrować aplikację Microsoft Graph, aby zarządzać zasobami Azure AD B2C](microsoft-graph-get-started.md).
* Dowiedz się [, jak używać Azure AD B2C jako dostawcy usługi SAML.](identity-provider-adfs2016-custom.md)
* Dowiedz się więcej o [typach aplikacji](application-types.md).
