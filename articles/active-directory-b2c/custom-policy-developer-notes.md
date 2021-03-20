---
title: Uwagi dla deweloperów dotyczące zasad niestandardowych
titleSuffix: Azure AD B2C
description: Uwagi dla deweloperów dotyczące konfigurowania i konserwowania Azure AD B2C przy użyciu zasad niestandardowych.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 05/19/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 76dcb9aec935dd4ea4f57a1362953d9741d8eaf0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102095803"
---
# <a name="developer-notes-for-custom-policies-in-azure-active-directory-b2c"></a>Uwagi dla deweloperów dotyczące zasad niestandardowych w Azure Active Directory B2C

Konfiguracja zasad niestandardowych w Azure Active Directory B2C jest teraz ogólnie dostępna. Ta metoda konfiguracji jest przeznaczona dla deweloperów zaawansowanej tożsamości, tworząc złożone rozwiązania do tworzenia tożsamości. Zasady niestandardowe sprawiają, że możliwości programu Identity platform są dostępne w dzierżawach Azure AD B2C.
Zaawansowani deweloperzy tożsamości korzystający z zasad niestandardowych powinny planować inwestowanie w czasie wykonywania instruktażu i odczytywania dokumentów referencyjnych.

Chociaż większość dostępnych opcji zasad niestandardowych jest teraz ogólnie dostępna, istnieją podstawowe możliwości, takie jak typy profilów technicznych i interfejsy API definicji zawartości, które znajdują się na różnych etapach cyklu życia oprogramowania. Trwa wiele innych. W poniższej tabeli określono poziom dostępności na bardziej szczegółowym poziomie.

## <a name="features-that-are-generally-available"></a>Funkcje, które są ogólnie dostępne

- Tworzenie i przekazywanie niestandardowych podróży użytkowników uwierzytelniania przy użyciu zasad niestandardowych.
    - Opisz przedziały użytkownika krok po kroku jako wymianę między dostawcami oświadczeń.
    - Definiowanie rozgałęziania warunkowego w podróży użytkownika.
- Współdziałanie z usługami z obsługą interfejsu API REST w przypadku niestandardowych podróży użytkowników uwierzytelniania.
- Sfederować z dostawcami tożsamości, które są zgodne z protokołem OpenIDConnect.
- Sfederować z dostawcami tożsamości, które są zgodne z protokołem SAML 2,0.

## <a name="responsibilities-of-custom-policy-feature-set-developers"></a>Obowiązki zasad niestandardowych — Ustawianie deweloperów

Ręczna konfiguracja zasad zapewnia dostęp na niższym poziomie do podstawowej platformy Azure AD B2C i skutkuje tworzeniem unikatowej struktury zaufania. Wiele możliwych permutacji niestandardowych dostawców tożsamości, relacji zaufania, integracji z usługami zewnętrznymi i przepływów pracy krok po kroku wymagają strategii metodycznej do projektowania i konfigurowania.

Deweloperzy korzystający z zestawu funkcji zasad niestandardowych powinni przestrzegać następujących wytycznych:

- Zapoznaj się z językiem konfiguracji zasad niestandardowych oraz zarządzania kluczami i wpisami tajnymi. Aby uzyskać więcej informacji, zobacz [TrustFrameworkPolicy](trustframeworkpolicy.md).
- Przejmij na własność scenariusze i integracje niestandardowe. Zadokumentuj swoją służbową i poinformuj swoją organizację o działającej witrynie.
- Wykonaj testy scenariusza metodyczne.
- Postępuj zgodnie z zaleceniami dotyczącymi tworzenia oprogramowania i przemieszczania. Zalecane jest co najmniej jedno środowisko deweloperskie i testowe.
- Bądź na bieżąco z informacjami dotyczącymi nowych osiągnięć od dostawców tożsamości i usług, z którymi integrujesz się. Na przykład Śledź zmiany w kluczach tajnych i zaplanowanych i nieplanowanych zmianach w usłudze.
- Skonfiguruj aktywne monitorowanie i monitoruj czas odpowiedzi w środowiskach produkcyjnych. Aby uzyskać więcej informacji na temat integrowania z Application Insights, zobacz [Azure Active Directory B2C: zbieranie dzienników](analytics-with-application-insights.md).
- Kontynuuj kontaktowe adresy e-mail z bieżącą subskrypcją platformy Azure i Kontynuuj pracę z wiadomościami e-mail zespołu w witrynie Microsoft Live.
- Wykonaj działania w odpowiednim czasie, gdy jest to zalecane przez zespół Microsoft Live site.

## <a name="terms-for-features-in-public-preview"></a>Warunki dotyczące funkcji w publicznej wersji zapoznawczej

- Zachęcamy do korzystania z funkcji publicznej wersji zapoznawczej tylko do celów ewaluacyjnych.
- Umowy dotyczące poziomu usług (umowy SLA) nie mają zastosowania do funkcji publicznej wersji zapoznawczej.
- Żądania pomocy technicznej dotyczące funkcji publicznej wersji zapoznawczej można zgłosić za pomocą zwykłych kanałów pomocy technicznej.

## <a name="features-by-stage-and-known-issues"></a>Funkcje według etapów i znanych problemów

Niestandardowe możliwości zasad są pod stałym programowaniem. Poniższa tabela zawiera indeks funkcji i dostępności składników.


### <a name="protocols-and-authorization-flows"></a>Protokoły i przepływy autoryzacji

| Cecha | Opracowywanie zawartości | Wersja zapoznawcza | Ogólna dostępność | Uwagi |
|-------- | :-----------: | :-------: | :--: | ----- |
| [Kod autoryzacji OAuth2](authorization-code-flow.md) |  |  | X |  |
| OAuth2 kod autoryzacji z PKCE |  |  | X | [Klienci publiczni i aplikacje jednostronicowe](authorization-code-flow.md)  |
| [Niejawny przepływ OAuth2](implicit-flow-single-page-application.md) |  |  | X |  |
| [Poświadczenia hasła właściciela zasobu OAuth2](ropc-custom.md) |  | X |  |  |
| [OIDC połączenie](openid-connect.md) |  |  | X |  |
| [SAML2](saml-service-provider.md)  |  |  |X  | Zaksięguj i Przekieruj powiązania. |
| OAuth1 |  |  |  | Nieobsługiwane. |
| WSFED | X |  |  |  |

### <a name="identify-providers-federation"></a>Identyfikowanie Federacji dostawców 

| Cecha | Opracowywanie zawartości | Wersja zapoznawcza | Ogólna dostępność | Uwagi |
|-------- | :-----------: | :-------: | :--: | ----- |
| [OpenID Connect](openid-connect-technical-profile.md) |  |  | X | Na przykład Google +.  |
| [OAuth2](oauth2-technical-profile.md) |  |  | X | Na przykład w serwisie Facebook.  |
| [OAuth1](oauth1-technical-profile.md) |  | X |  | Na przykład serwis Twitter. |
| [SAML2](identity-provider-generic-saml.md) |  |   | X | Na przykład usługi Salesforce, ADFS. |
| WSFED| X |  |  |  |


### <a name="rest-api-integration"></a>Integracja interfejsu API REST

| Cecha | Opracowywanie zawartości | Wersja zapoznawcza | Ogólna dostępność | Uwagi |
|-------- | :-----------: | :-------: | :--: | ----- |
| [Interfejs API REST z uwierzytelnianiem podstawowym](secure-rest-api.md#http-basic-authentication) |  |  | X |  |
| [Interfejs API REST z uwierzytelnianiem certyfikatu klienta](secure-rest-api.md#https-client-certificate-authentication) |  |  | X |  |
| [Interfejs API REST z uwierzytelnianiem okaziciela OAuth2](secure-rest-api.md#oauth2-bearer-authentication) |  | X |  |  |

### <a name="component-support"></a>Obsługa składników

| Cecha | Opracowywanie zawartości | Wersja zapoznawcza | Ogólna dostępność | Uwagi |
| ------- | :-----------: | :-------: | :--: | ----- |
| [Uwierzytelnianie przy współczynniku telefonicznym](phone-factor-technical-profile.md) |  |  | X |  |
| [Uwierzytelnianie usługi Azure AD MFA](multi-factor-auth-technical-profile.md) |  | X |  |  |
| [Hasło jednorazowe](one-time-password-technical-profile.md) |  | X |  |  |
| [Azure Active Directory](active-directory-technical-profile.md) jako katalog lokalny |  |  | X |  |
| Weryfikacja podsystemu wiadomości e-mail platformy Azure do wiadomości e-mail |  |  | X |  |
| [Dostawcy usług poczty e-mail innych firm](custom-email-mailjet.md) |  |X  |  |  |
| [Obsługa wielu języków](localization.md)|  |  | X |  |
| [Walidacje predykatu](predicates.md) |  |  | X | Na przykład złożoność hasła. |
| [Kontrolki wyświetlania](display-controls.md) |  |X  |  |  |


### <a name="app-ief-integration"></a>Integracja App-IEF

| Cecha | Opracowywanie zawartości | Wersja zapoznawcza | Ogólna dostępność | Uwagi |
| ------- | :-----------: | :-------: | :--: | ----- |
| Parametr ciągu zapytania `domain_hint` |  |  | X | Dostępne jako roszczeń mogą być przesyłane do dostawcy tożsamości. |
| Parametr ciągu zapytania `login_hint` |  |  | X | Dostępne jako roszczeń mogą być przesyłane do dostawcy tożsamości. |
| Wstaw dane JSON do podróży użytkownika za pośrednictwem `client_assertion` | X |  |  | Będzie przestarzałe. |
| Wstaw dane JSON do podróży użytkownika jako `id_token_hint` |  | X |  | Podejście do przodu do przekazywania kodu JSON. |
| [Przekaż token dostawcy tożsamości do aplikacji](idp-pass-through-user-flow.md) |  | X |  | Na przykład z usługi Facebook do aplikacji. |


### <a name="session-management"></a>Zarządzanie sesjami

| Cecha | Opracowywanie zawartości | Wersja zapoznawcza | Ogólna dostępność | Uwagi |
| ------- | :-----------: | :-------: | :--: | ----- |
| [Domyślny dostawca sesji SSO](custom-policy-reference-sso.md#defaultssosessionprovider) |  |  | X |  |
| [Dostawca sesji logowania zewnętrznego](custom-policy-reference-sso.md#externalloginssosessionprovider) |  |  | X |  |
| [Dostawca sesji SSO protokołu SAML](custom-policy-reference-sso.md#samlssosessionprovider) |  |  | X |  |
| [OAuthSSOSessionProvider](custom-policy-reference-sso.md#oauthssosessionprovider)  |  | X |  |  |
| [Wylogowanie jednokrotne](session-behavior.md#sign-out)  |  | X |  |  |

### <a name="security"></a>Zabezpieczenia

| Cecha | Opracowywanie zawartości | Wersja zapoznawcza | Ogólna dostępność | Uwagi |
|-------- | :-----------: | :-------: | :--: | ----- |
| Klucze zasad — generowanie, ręczne, przekazywanie |  |  | X |  |
| Klucze zasad — RSA/certyfikat, wpisy tajne |  |  | X |  |


### <a name="developer-interface"></a>Interfejs dewelopera

| Cecha | Opracowywanie zawartości | Wersja zapoznawcza | Ogólna dostępność | Uwagi |
| ------- | :-----------: | :-------: | :--: | ----- |
| Witryna Azure Portal — środowisko IEF |  |  | X |  |
| Przekazywanie zasad |  |  | X |  |
| [Application Insights dzienniki podróży użytkownika](troubleshoot-with-application-insights.md) |  | X |  | Używane do rozwiązywania problemów podczas opracowywania.  |
| [Application Insights dzienników zdarzeń](analytics-with-application-insights.md) |  | X |  | Służy do monitorowania przepływów użytkowników w środowisku produkcyjnym. |


## <a name="next-steps"></a>Następne kroki

- Sprawdź [Microsoft Graph operacje dostępne dla Azure AD B2C](microsoft-graph-operations.md)
- Dowiedz się więcej [na temat zasad niestandardowych i różnic dotyczących przepływów użytkowników](custom-policy-overview.md).
