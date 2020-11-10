---
title: Kody błędów autoryzacji & uwierzytelniania usługi Azure AD
description: Dowiedz się więcej o kodach błędów AADSTS zwracanych z usługi tokenu zabezpieczeń (STS) usługi Azure AD.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: reference
ms.date: 11/09/2020
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 59ed397f3bd54a324f8fa375a3ea870860c31142
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94410933"
---
# <a name="azure-ad-authentication-and-authorization-error-codes"></a>Kody błędów uwierzytelniania i autoryzacji usługi Azure AD

Szukasz informacji o kodach błędów AADSTS, które są zwracane z usługi tokenu zabezpieczającego (STS) Azure Active Directory (Azure AD)? Przeczytaj ten dokument, aby znaleźć AADSTS opisów błędów, poprawek i niektórych sugerowanych obejść.

> [!NOTE]
> Niniejsze informacje mają charakter wstępny i mogą ulec zmianom. Masz pytanie lub nie możesz znaleźć tego, czego szukasz? Utwórz problem w usłudze GitHub lub zapoznaj się z [pomocą techniczną i opcjami pomocy dla deweloperów](./developer-support-help-options.md) , aby dowiedzieć się więcej na temat innych sposobów uzyskiwania pomocy i obsługi technicznej.
>
> Ta dokumentacja jest dostępna dla wskazówek dotyczących deweloperów i administratorów, ale nigdy nie powinna być używana przez klienta. Kody błędów mogą ulec zmianie w dowolnym momencie w celu zapewnienia bardziej szczegółowych komunikatów o błędach, które są przeznaczone do ułatwienia deweloperom podczas kompilowania aplikacji. Aplikacje, które są zależne od tekstu lub numerów kodów błędów, zostaną złamane z upływem czasu.

## <a name="handling-error-codes-in-your-application"></a>Obsługa kodów błędów w aplikacji

[Specyfikacja OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-5.2) zawiera wskazówki dotyczące sposobu obsługi błędów podczas uwierzytelniania przy użyciu `error` części odpowiedzi na błąd. 

Oto przykładowa odpowiedź na błąd:

```json
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://example.contoso.com/activity.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7", 
  "error_uri":"https://login.microsoftonline.com/error?code=70011"
}
```

| Parametr         | Opis    |
|-------------------|----------------|
| `error`       | Ciąg kodu błędu, który może służyć do klasyfikowania typów błędów, które występują i powinny być używane do reagowania na błędy. |
| `error_description` | Konkretny komunikat o błędzie, który może ułatwić deweloperom zidentyfikowanie głównej przyczyny błędu uwierzytelniania. Nigdy nie używaj tego pola, aby reagować na błąd w kodzie. |
| `error_codes` | Lista kodów błędów specyficznych dla usługi STS, które mogą pomóc w diagnostyce.  |
| `timestamp`   | Godzina wystąpienia błędu. |
| `trace_id`    | Unikatowy identyfikator żądania, które może pomóc w diagnostyce. |
| `correlation_id` | Unikatowy identyfikator żądania, które może pomóc w diagnostyce między składnikami. |
| `error_uri` |  Link do strony wyszukiwania błędów z dodatkowymi informacjami o błędzie.  Jest to przeznaczone tylko dla deweloperów, ale nie są one widoczne dla użytkowników.  Dostępne tylko wtedy, gdy system wyszukiwania błędów zawiera dodatkowe informacje o błędzie — nie wszystkie błędy mają dodatkowe informacje.|

`error`Pole ma kilka możliwych wartości — Przejrzyj łącza do dokumentacji protokołu i specyfikacje OAuth 2,0, aby dowiedzieć się więcej o określonych błędach (na przykład `authorization_pending` w [przepływie kodu urządzenia](v2-oauth2-device-code.md)) i sposobach reagowania na nie.  Niektóre typowe są wymienione tutaj:

| Kod błędu         | Opis        | Akcja klienta    |
|--------------------|--------------------|------------------|
| `invalid_request`  | Błąd protokołu, taki jak brak wymaganego parametru. | Napraw i ponownie prześlij żądanie.|
| `invalid_grant`    | Niektóre materiały uwierzytelniania (kod uwierzytelniania, token odświeżania, token dostępu, wyzwanie PKCE) były nieprawidłowe, nie można ich przeanalizować lub nie mogą być używane w inny sposób | Wypróbuj nowe żądanie do `/authorize` punktu końcowego, aby uzyskać nowy kod autoryzacji.  Należy rozważyć przejrzenie i sprawdzenie poprawności użycia protokołów przez aplikację. |
| `unauthorized_client` | Uwierzytelniony klient nie ma autoryzacji do korzystania z tego typu udzielania autoryzacji. | Zwykle dzieje się tak, gdy aplikacja kliencka nie jest zarejestrowana w usłudze Azure AD lub nie została dodana do dzierżawy usługi Azure AD użytkownika. Aplikacja może monitować użytkownika z instrukcją dotyczącą instalowania aplikacji i dodawania jej do usługi Azure AD. |
| `invalid_client` | Uwierzytelnianie klienta nie powiodło się.  | Poświadczenia klienta są nieprawidłowe. Aby rozwiązać ten problem, administrator aplikacji aktualizuje poświadczenia.   |
| `unsupported_grant_type` | Serwer autoryzacji nie obsługuje typu przydzielenia autoryzacji. | Zmień typ dotacji w żądaniu. Ten typ błędu powinien wystąpić tylko podczas opracowywania i być wykrywany podczas wstępnego testowania. |
| `invalid_resource` | Zasób docelowy jest nieprawidłowy, ponieważ nie istnieje, usługa Azure AD nie może go odnaleźć lub nie jest poprawnie skonfigurowana. | Wskazuje to, że zasób, jeśli istnieje, nie został skonfigurowany w dzierżawie. Aplikacja może monitować użytkownika z instrukcją dotyczącą instalowania aplikacji i dodawania jej do usługi Azure AD.  Podczas programowania zwykle oznacza to, że nieprawidłowa konfiguracja dzierżawy testowej lub literówka w nazwie żądanego zakresu. |
| `interaction_required` | Żądanie wymaga interakcji z użytkownikiem. Na przykład wymagany jest dodatkowy krok uwierzytelniania. | Spróbuj ponownie wykonać żądanie z tym samym zasobem, interaktywnie, aby użytkownik mógł wykonać wszystkie wymagane wyzwania.  |
| `temporarily_unavailable` | Serwer jest tymczasowo zbyt zajęty, aby obsłużyć żądanie. | Ponów żądanie. Aplikacja kliencka może wyjaśnić użytkownikowi, że jego odpowiedź jest opóźniona ze względu na tymczasowy warunek. |

## <a name="lookup-current-error-code-information"></a>Wyszukaj bieżące informacje o kodzie błędu
Kody błędów i komunikaty mogą ulec zmianie.  Aby uzyskać najbardziej aktualne informacje, zapoznaj się ze [https://login.microsoftonline.com/error](https://login.microsoftonline.com/error) stroną, aby znaleźć AADSTS opisów błędów, poprawek i sugerowanych rozwiązań.  

Na przykład jeśli wystąpił kod błędu "AADSTS50058", wykonaj wyszukiwanie w [https://login.microsoftonline.com/error](https://login.microsoftonline.com/error) "50058".  Możesz również połączyć się bezpośrednio z określonym błędem, dodając numer kodu błędu do adresu URL: [https://login.microsoftonline.com/error?code=50058](https://login.microsoftonline.com/error?code=50058) .

## <a name="aadsts-error-codes"></a>Kody błędów AADSTS

| Błąd | Opis |
|---|---|
| AADSTS16000 | SelectUserAccount — jest to przerwanie zgłoszone przez usługę Azure AD, które powoduje, że użytkownik może wybrać spośród wielu prawidłowych sesji logowania jednokrotnego. Ten błąd jest dość powszechny i może zostać zwrócony do aplikacji, jeśli `prompt=none` jest określony. |
| AADSTS16001 | UserAccountSelectionInvalid — ten błąd zostanie wyświetlony, jeśli użytkownik kliknie kafelek, który został odrzucony przez sesję Selecting Logic. Po wyzwoleniu ten błąd umożliwia użytkownikowi odzyskanie przez pobranie ze zaktualizowanej listy kafelków/sesji lub wybranie innego konta. Ten błąd może wystąpić ze względu na defekt kodu lub sytuację wyścigu. |
| AADSTS16002 | AppSessionSelectionInvalid — nie spełniono wymagania dotyczącego identyfikatora SID określonego przez aplikację.  |
| AADSTS16003 | SsoUserAccountNotFoundInResourceTenant — wskazuje, że użytkownik nie został jawnie dodany do dzierżawy. |
| AADSTS17003 | CredentialKeyProvisioningFailed — usługa Azure AD nie może udostępnić klucza użytkownika. |
| AADSTS20001 | WsFedSignInResponseError — wystąpił problem z dostawcą tożsamości federacyjnej. Skontaktuj się ze swoim dostawcą tożsamości, aby rozwiązać ten problem. |
| AADSTS20012 | WsFedMessageInvalid — wystąpił problem z dostawcą tożsamości federacyjnej. Skontaktuj się ze swoim dostawcą tożsamości, aby rozwiązać ten problem. |
| AADSTS20033 | FedMetadataInvalidTenantName — wystąpił problem z dostawcą tożsamości federacyjnej. Skontaktuj się ze swoim dostawcą tożsamości, aby rozwiązać ten problem. |
| AADSTS40008 | OAuth2IdPUnretryableServerError — wystąpił problem z dostawcą tożsamości federacyjnej. Skontaktuj się ze swoim dostawcą tożsamości, aby rozwiązać ten problem. |
| AADSTS40009 | OAuth2IdPRefreshTokenRedemptionUserError — wystąpił problem z dostawcą tożsamości federacyjnej. Skontaktuj się ze swoim dostawcą tożsamości, aby rozwiązać ten problem. |
| AADSTS40010 | OAuth2IdPRetryableServerError — wystąpił problem z dostawcą tożsamości federacyjnej. Skontaktuj się ze swoim dostawcą tożsamości, aby rozwiązać ten problem. |
| AADSTS40015 | OAuth2IdPAuthCodeRedemptionUserError — wystąpił problem z dostawcą tożsamości federacyjnej. Skontaktuj się ze swoim dostawcą tożsamości, aby rozwiązać ten problem. |
| AADSTS50000 | TokenIssuanceError — wystąpił problem z usługą logowania. [Otwórz bilet pomocy technicznej](../fundamentals/active-directory-troubleshooting-support-howto.md), aby rozwiązać ten problem. |
| AADSTS50001 | InvalidResource — zasób jest wyłączony lub nie istnieje. Sprawdź kod aplikacji, aby upewnić się, że został określony dokładny adres URL zasobu dla zasobu, do którego próbujesz uzyskać dostęp.  |
| AADSTS50002 | NotAllowedTenant — logowanie nie powiodło się z powodu ograniczonego dostępu do serwera proxy w dzierżawie. Jeśli są to Twoje własne zasady dzierżawy, możesz zmienić ustawienia dzierżawy z ograniczeniami, aby rozwiązać ten problem. |
| AADSTS50003 | MissingSigningKey — logowanie nie powiodło się z powodu braku klucza podpisywania lub certyfikatu. Może to być spowodowane tym, że w aplikacji nie został skonfigurowany klucz podpisywania. Zapoznaj się z rozwiązaniami opisanymi pod adresem [.. /Manage-Apps/Application-Sign-in-problem-Federated-SSO-Gallery.MD # certyfikat-lub-Key-nie został skonfigurowany](../manage-apps/application-sign-in-problem-federated-sso-gallery.md#certificate-or-key-not-configured). Jeśli nadal widzisz problemy, skontaktuj się z właścicielem aplikacji lub administratorem aplikacji. |
| AADSTS50005 | DevicePolicyError — użytkownik próbował zalogować się na urządzeniu z platformy, która nie jest obecnie obsługiwana przez zasady dostępu warunkowego. |
| AADSTS50006 | InvalidSignature — weryfikacja podpisu nie powiodła się z powodu nieprawidłowej sygnatury. |
| AADSTS50007 | PartnerEncryptionCertificateMissing — certyfikat szyfrowania partnera nie został znaleziony dla tej aplikacji. Aby uzyskać ten problem [, Otwórz bilet pomocy technicznej](../fundamentals/active-directory-troubleshooting-support-howto.md) w firmie Microsoft. |
| AADSTS50008 | InvalidSamlToken — brak potwierdzenia SAML lub został on nieprawidłowo skonfigurowany w tokenie. Skontaktuj się z dostawcą federacji. |
| AADSTS50010 | AudienceUriValidationFailed — weryfikacja identyfikatora URI odbiorców dla aplikacji nie powiodła się, ponieważ nie skonfigurowano żadnych odbiorców tokenu. |
| AADSTS50011 | InvalidReplyTo — brak adresu odpowiedzi, jest on niepoprawnie skonfigurowany lub nie jest zgodny z adresami odpowiedzi skonfigurowanymi dla aplikacji.  Ponieważ w ramach rozwiązania Zadbaj o dodanie tego brakującego adresu zwrotnego do aplikacji Azure Active Directory lub skontaktuj się z osobą z uprawnieniami do zarządzania aplikacją w Active Directory zrób to za Ciebie.|
| AADSTS50012 | AuthenticationFailed — uwierzytelnianie nie powiodło się z jednego z następujących powodów:<ul><li>Nazwa podmiotu certyfikatu podpisywania nie jest autoryzowana</li><li>Nie znaleziono zgodnych zasad zaufanych urzędów dla autoryzowanej nazwy podmiotu</li><li>Łańcuch certyfikatów jest nieprawidłowy</li><li>Certyfikat podpisywania jest nieprawidłowy</li><li>Zasady nie są skonfigurowane w dzierżawie</li><li>Odcisk palca certyfikatu podpisywania nie jest autoryzowany</li><li>Potwierdzenie klienta zawiera nieprawidłową sygnaturę</li></ul> |
| AADSTS50013 | InvalidAssertion-Assertion jest nieprawidłowy z powodu różnych powodów — Wystawca tokenu nie jest zgodny z wersją interfejsu API w ramach prawidłowego zakresu czasu — wygasły token odświeżania w potwierdzeniu nie jest podstawowym tokenem odświeżania. |
| AADSTS50014 | GuestUserInPendingState — wykup użytkownika jest w stanie oczekiwania. Konto użytkownika-gościa nie jest jeszcze w pełni utworzone. |
| AADSTS50015 | ViralUserLegalAgeConsentRequiredState — użytkownik wymaga zgody na zgodę grupy wiekowej. |
| AADSTS50017 | CertificateValidationFailed — weryfikacja certyfikacji nie powiodła się, przyczyny z następujących powodów:<ul><li>Nie można odnaleźć certyfikatu wystawcy na liście zaufanych certyfikatów</li><li>Nie można odnaleźć oczekiwanego elementu CrlSegment</li><li>Nie można odnaleźć certyfikatu wystawcy na liście zaufanych certyfikatów</li><li>Punkt dystrybucji różnicowej listy CRL jest skonfigurowany bez odpowiedniego punktu dystrybucji listy CRL</li><li>Nie można pobrać prawidłowych segmentów listy CRL z powodu problemu z przekroczeniem limitu czasu</li><li>Nie można pobrać listy CRL</li></ul>Skontaktuj się z administratorem dzierżawy. |
| AADSTS50020 | UserUnauthorized — użytkownicy nie mają autoryzacji do wywołania tego punktu końcowego. |
| AADSTS50027 | InvalidJwtToken — nieprawidłowy token JWT z następujących powodów:<ul><li>nie zawiera oświadczenia nonce, oświadczenia sub</li><li>niezgodność identyfikatora podmiotu</li><li>zduplikowane oświadczenie w oświadczeniach idToken</li><li>nieoczekiwany wystawca</li><li>nieoczekiwani odbiorcy</li><li>nie mieści się w prawidłowym zakresie czasu </li><li>format tokenu nie jest prawidłowy</li><li>weryfikacja tokenu zewnętrznego identyfikatora od wystawcy nie powiodła się.</li></ul> |
| AADSTS50029 | Nieprawidłowy identyfikator URI — nazwa domeny zawiera nieprawidłowe znaki. Skontaktuj się z administratorem dzierżawy. |
| AADSTS50032 | WeakRsaKey-wskazuje błędnego użytkownika próbującego użyć słabego klucza RSA. |
| AADSTS50033 | RetryableError-wskazuje przejściowy błąd, który nie jest związany z operacjami bazy danych. |
| AADSTS50034 | UserAccountNotFound — aby zalogować się do tej aplikacji, należy dodać konto do katalogu. |
| AADSTS50042 | UnableToGeneratePairwiseIdentifierWithMissingSalt — w zasadzie brakuje soli wymaganej do wygenerowania identyfikatora parowania. Skontaktuj się z administratorem dzierżawy. |
| AADSTS50043 | UnableToGeneratePairwiseIdentifierWithMultipleSalts |
| AADSTS50048 | SubjectMismatchesIssuer — niezgodność podmiotu wystawcy w potwierdzeniu klienta. Skontaktuj się z administratorem dzierżawy. |
| AADSTS50049 | NoSuchInstanceForDiscovery — nieznane lub nieprawidłowe wystąpienie. |
| AADSTS50050 | MalformedDiscoveryRequest — żądanie jest źle sformułowane. |
| AADSTS50053 | IdsLocked — konto jest zablokowane, ponieważ użytkownik próbował zalogować się zbyt wiele razy przy użyciu niepoprawnego identyfikatora użytkownika lub hasła. |
| AADSTS50055 | InvalidPasswordExpiredPassword — hasło wygasło. |
| AADSTS50056 | Nieprawidłowe lub puste hasło — hasło nie istnieje w sklepie dla tego użytkownika. |
| AADSTS50057 | UserDisabled — konto użytkownika jest wyłączone. Konto zostało wyłączone przez administratora. |
| AADSTS50058 | UserInformationNotProvided — oznacza to, że użytkownik nie jest zalogowany. Jest to typowy błąd oczekiwany w sytuacji, gdy użytkownik jest nieuwierzytelniony i jeszcze nie jest zalogowany.</br>Jeśli ten błąd jest zalecany w kontekście logowania jednokrotnego, w którym użytkownik zalogował się wcześniej, oznacza to, że nie można odnaleźć sesji logowania jednokrotnego lub jest ona nieprawidłowa.</br>Ten błąd może zostać zwrócony do aplikacji, jeśli jest wyświetlany monit = none. |
| AADSTS50059 | Nie znaleziono informacji identyfikacyjnych MissingTenantRealmAndNoUserInformationProvided-dzierżawców w żądaniu ani IMPLIKOWANYCH przez żadne podane poświadczenia. Użytkownik może skontaktować się z administratorem dzierżawy, aby pomóc w rozwiązaniu problemu. |
| AADSTS50061 | SignoutInvalidRequest — żądanie wylogowania jest nieprawidłowe. |
| AADSTS50064 | CredentialAuthenticationError — weryfikacja poświadczeń dla nazwy użytkownika lub hasła zakończyła się niepowodzeniem. |
| AADSTS50068 | SignoutInitiatorNotParticipant — wylogowywanie nie powiodło się. Aplikacja, która zainicjowała wylogowywanie, nie jest uczestnikiem bieżącej sesji. |
| AADSTS50070 | SignoutUnknownSessionIdentifier — wylogowywanie nie powiodło się. Żądanie wylogowania określiło identyfikator nazwy, który nie jest zgodny z istniejącymi sesjami. |
| AADSTS50071 | SignoutMessageExpired — żądanie wylogowania wygasło. |
| AADSTS50072 | UserStrongAuthEnrollmentRequiredInterrupt — użytkownik musi zarejestrować się na potrzeby uwierzytelniania dwuskładnikowego (Interactive). |
| AADSTS50074 | UserStrongAuthClientAuthNRequiredInterrupt — jest wymagane silne uwierzytelnianie, a użytkownik nie przeszedł z wyzwania usługi MFA. |
| AADSTS50076 | UserStrongAuthClientAuthNRequired — ze względu na zmianę konfiguracji podjętą przez administratora lub przeniesiono ją do nowej lokalizacji, użytkownik musi użyć uwierzytelniania wieloskładnikowego w celu uzyskania dostępu do zasobu. Ponów próbę, używając nowego autoryzowanego żądania dla zasobu. |
| AADSTS50079 | UserStrongAuthEnrollmentRequired — z powodu zmiany konfiguracji wprowadzonej przez administratora lub przenoszonej przez użytkownika do nowej lokalizacji użytkownik musi korzystać z uwierzytelniania wieloskładnikowego. |
| AADSTS50085 | Token odświeżania wymaga danych logowania dostawy tożsamości z serwisów społecznościowych. Użytkownik musi zalogować się ponownie przy użyciu nazwy użytkownika i hasła |
| AADSTS50086 | SasNonRetryableError |
| AADSTS50087 | SasRetryableError — usługa jest tymczasowo niedostępna. Spróbuj ponownie. |
| AADSTS50089 | Token uwierzytelniania wygasł — uwierzytelnianie nie powiodło się. Użytkownik spróbuje ponownie zalogować się przy użyciu nazwy użytkownika i hasła. |
| AADSTS50097 | DeviceAuthenticationRequired — wymagane jest uwierzytelnianie urządzenia. |
| AADSTS50099 | PKeyAuthInvalidJwtUnauthorized — podpis JWT jest nieprawidłowy. |
| AADSTS50105 | EntitlementGrantsNotFound — zalogowany użytkownik nie jest przypisany do roli dla zalogowanej aplikacji. Przypisz użytkownika do aplikacji. Aby uzyskać więcej informacji:[.. /Manage-Apps/Application-Sign-in-problem-Federated-SSO-Gallery.MD # użytkownik-a-rola](../manage-apps/application-sign-in-problem-federated-sso-gallery.md#user-not-assigned-a-role). |
| AADSTS50107 | InvalidRealmUri — żądany obiekt Federacji nie istnieje. Skontaktuj się z administratorem dzierżawy. |
| AADSTS50120 | ThresholdJwtInvalidJwtFormat — wystaw z nagłówkiem JWT. Skontaktuj się z administratorem dzierżawy. |
| AADSTS50124 | ClaimsTransformationInvalidInputParameter — transformacja oświadczeń zawiera nieprawidłowy parametr wejściowy. Skontaktuj się z administratorem dzierżawy, aby zaktualizować zasady. |
| AADSTS50125 | PasswordResetRegistrationRequiredInterrupt — logowanie zostało przerwane z powodu resetowania hasła lub wpisu rejestracji hasła. |
| AADSTS50126 | InvalidUserNameOrPassword — błąd podczas walidacji poświadczeń z powodu nieprawidłowej nazwy użytkownika lub hasła. |
| AADSTS50127 | BrokerAppNotInstalled — użytkownik musi zainstalować aplikację brokera, aby uzyskać dostęp do tej zawartości. |
| AADSTS50128 | Nieprawidłowa nazwa domeny — nie znaleziono informacji identyfikacyjnych dzierżawy w żądaniu lub IMPLIKOWANYCH przez żadne podane poświadczenia. |
| AADSTS50129 | Do zarejestrowania urządzenia wymagane jest dołączenie do DeviceIsNotWorkplaceJoined. |
| AADSTS50131 | ConditionalAccessFailed-wskazuje różne błędy dostępu warunkowego, takie jak zły stan urządzenia z systemem Windows, żądanie zablokowane z powodu podejrzanych działań, zasad dostępu lub decyzji dotyczących zasad zabezpieczeń. |
| AADSTS50132 | SsoArtifactInvalidOrExpired — sesja nie jest prawidłowa z powodu wygaśnięcia hasła lub ostatniej zmiany hasła. |
| AADSTS50133 | SsoArtifactRevoked — sesja nie jest prawidłowa z powodu wygaśnięcia hasła lub ostatniej zmiany hasła. |
| AADSTS50134 | DeviceFlowAuthorizeWrongDatacenter — niewłaściwe centrum danych. Aby autoryzować żądanie zainicjowane przez aplikację w przepływie urządzeń OAuth 2,0, osoba autoryzowana musi znajdować się w tym samym centrum danych, w którym znajduje się oryginalne żądanie. |
| AADSTS50135 | PasswordChangeCompromisedPassword — zmiana hasła jest wymagana ze względu na ryzyko związane z kontem. |
| AADSTS50136 | RedirectMsaSessionToApp — wykryto pojedynczą sesję MSA. |
| AADSTS50139 | SessionMissingMsaOAuth2RefreshToken — sesja jest nieprawidłowa z powodu braku zewnętrznego tokenu odświeżania. |
| AADSTS50140 | KmsiInterrupt — ten błąd wystąpił z powodu przerwania "nie wylogowuj mnie" podczas logowania użytkownika. [Otwórz bilet pomocy technicznej](../fundamentals/active-directory-troubleshooting-support-howto.md) z identyfikatorem korelacji, identyfikatorem żądania i kodem błędu, aby uzyskać więcej informacji. |
| AADSTS50143 | Niezgodność sesji — sesja jest nieprawidłowa, ponieważ dzierżawa użytkownika nie odpowiada wskazówce dotyczącej domeny z powodu innego zasobu. [Otwórz bilet pomocy technicznej](../fundamentals/active-directory-troubleshooting-support-howto.md) z identyfikatorem korelacji, identyfikatorem żądania i kodem błędu, aby uzyskać więcej informacji. |
| AADSTS50144 | InvalidPasswordExpiredOnPremPassword hasło użytkownika Active Directory wygasło. Wygeneruj nowe hasło dla użytkownika lub użyj narzędzia resetowania samoobsługowego do zresetowania hasła. |
| AADSTS50146 | MissingCustomSigningKey — ta aplikacja jest wymagana do skonfigurowania klucza podpisywania specyficznego dla aplikacji. Albo nie została skonfigurowana przy jego użyciu, albo klucz wygasł lub nie jest jeszcze ważny. |
| AADSTS50147 | MissingCodeChallenge — rozmiar parametru wezwania kodu jest nieprawidłowy. |
| AADSTS50155 | DeviceAuthenticationFailed — uwierzytelnianie urządzenia nie powiodło się dla tego użytkownika. |
| AADSTS50158 | ExternalSecurityChallenge — zewnętrzny test zabezpieczeń nie został spełniony. |
| AADSTS50161 | InvalidExternalSecurityChallengeConfiguration — oświadczenia wysyłane przez dostawcę zewnętrznego nie są wystarczające lub brakujące oświadczenie zażądało dostawcy zewnętrznemu. |
| AADSTS50166 | ExternalClaimsProviderThrottled — nie można wysłać żądania do dostawcy oświadczeń. |
| AADSTS50168 | ChromeBrowserSsoInterruptRequired — klient może uzyskać token logowania jednokrotnego za pomocą rozszerzenia kont systemu Windows 10, ale nie znaleziono tokenu w żądaniu lub dostarczony token wygasł. |
| AADSTS50169 | InvalidRequestBadRealm — obszar nie jest skonfigurowanym obszarem przestrzeni nazw bieżącej usługi. |
| AADSTS50170 | MissingExternalClaimsProviderMapping — Brak mapowania formantów zewnętrznych. |
| AADSTS50177 | ExternalChallengeNotSupportedForPassthroughUsers — zewnętrzny test nie jest obsługiwany w przypadku użytkowników przekazujących. |
| AADSTS50178 | SessionControlNotSupportedForPassthroughUsers — kontrola sesji nie jest obsługiwana w przypadku użytkowników przekazujących. |
| AADSTS50180 | Wymagana jest WindowsIntegratedAuthMissing zintegrowane uwierzytelnianie systemu Windows. Włącz dzierżawę dla bezproblemowego logowania jednokrotnego. |
| AADSTS50187 | DeviceInformationNotProvided — usługa nie może wykonać uwierzytelniania urządzenia. |
| AADSTS50196 | LoopDetected — wykryto pętlę klienta. Sprawdź logikę aplikacji, aby upewnić się, że buforowanie tokenów jest zaimplementowane i że warunki błędów są obsługiwane poprawnie.  Aplikacja spowodowała zbyt wiele tego samego żądania w zbyt krótkim czasie, co oznacza, że jest w stanie wadliwym lub jest abusively żądającym tokenów. |
| AADSTS50197 | ConflictingIdentities — nie można odnaleźć użytkownika. Spróbuj zalogować się ponownie. |
| AADSTS50199 | CmsiInterrupt — ze względów bezpieczeństwa wymagane jest potwierdzenie użytkownika dla tego żądania.  Ponieważ jest to błąd "interaction_required", klient powinien wykonać uwierzytelnianie interaktywne.  Dzieje się tak, ponieważ System WebView został użyty do zażądania tokenu dla aplikacji natywnej — użytkownik musi zostać poproszony o podanie monitu o zalogowanie się w aplikacji, w której zarejestrowano. Aby uniknąć tego monitu, identyfikator URI przekierowania powinien być częścią następującej bezpiecznej listy: <br />http://<br />https://<br />msauth://(tylko system iOS)<br />msauthv2://(tylko system iOS)<br />Chrome-Extension://(tylko przeglądarka dla programu Desktop Chrome) |
| AADSTS51000 | RequiredFeatureNotEnabled — funkcja jest wyłączona. |
| AADSTS51001 | DomainHintMustbePresent — Wskazówka domeny musi być obecna przy użyciu lokalnego identyfikatora zabezpieczeń lub lokalnej nazwy UPN. |
| AADSTS51004 | UserAccountNotInDirectory — konto użytkownika nie istnieje w katalogu. |
| AADSTS51005 | TemporaryRedirect — odpowiednik stanu HTTP 307, który wskazuje, że żądane informacje znajdują się w identyfikatorze URI określonym w nagłówku lokalizacji. Po otrzymaniu tego stanu postępuj zgodnie z nagłówkiem lokalizacji skojarzonym z odpowiedzią. Gdy oryginalna Metoda żądania została OPUBLIKOWANa, przekierowane żądanie będzie również używać metody POST. |
| AADSTS51006 | Wymagana jest ForceReauthDueToInsufficientAuth zintegrowane uwierzytelnianie systemu Windows. Użytkownik zalogował się przy użyciu tokenu sesji, w którym brakuje zintegrowanego uwierzytelniania systemu Windows. Poproś użytkownika o ponowne zalogowanie. |
| AADSTS52004 | DelegationDoesNotExistForLinkedIn — użytkownik nie podał zgody na dostęp do zasobów serwisu LinkedIn. |
| AADSTS53000 | Zasady dostępu warunkowego DeviceNotCompliant wymagają zgodnego urządzenia, a urządzenie nie jest zgodne. Użytkownik musi zarejestrować swoje urządzenie przy użyciu zatwierdzonego dostawcy rozwiązania do zarządzania urządzeniami przenośnymi, na przykład usługi Intune. |
| AADSTS53001 | DeviceNotDomainJoined — zasady dostępu warunkowego wymagają urządzenia przyłączonego do domeny, a urządzenie nie jest przyłączone do domeny. Użytkownik musi korzystać z urządzenia przyłączonego do domeny. |
| AADSTS53002 | ApplicationUsedIsNotAnApprovedApp — używana aplikacja nie jest zatwierdzoną aplikacją dla dostępu warunkowego. Użytkownik musi użyć jednej z aplikacji z listy zatwierdzonych aplikacji do użycia w celu uzyskania dostępu. |
| AADSTS53003 | BlockedByConditionalAccess — dostęp został zablokowany przez zasady dostępu warunkowego. Zasady dostępu nie zezwalają na wystawianie tokenów. |
| AADSTS53004 | ProofUpBlockedDueToRisk — użytkownik musi ukończyć proces rejestracji w usłudze MFA, aby uzyskać dostęp do tej zawartości. Użytkownik powinien zarejestrować się w celu uwierzytelniania wieloskładnikowego. |
| AADSTS54000 | MinorUserBlockedLegalAgeGroupRule |
| AADSTS65001 | DelegationDoesNotExist — użytkownik lub administrator nie wyraził zgody na używanie aplikacji z IDENTYFIKATORem X. Wyślij interaktywne żądanie autoryzacji dla tego użytkownika i zasobu. |
| AADSTS65004 | UserDeclinedConsent — użytkownik odrzucił zgodę na dostęp do aplikacji. Użytkownik musi ponowić próbę zalogowania się i wyrazić zgodę na aplikację|
| AADSTS65005 | MisconfiguredApplication — lista dostępu do zasobów wymagana przez aplikację nie zawiera aplikacji wykrywalnych przez zasób lub aplikacja kliencka zażądała dostępu do zasobu, który nie został określony w wymaganej liście dostępu do zasobów lub usługa grafu zwróciła Nieprawidłowe żądanie lub nie znaleziono zasobu. Jeśli aplikacja obsługuje protokół SAML, być może skonfigurowano aplikację z nieprawidłowym identyfikatorem (jednostką). Wypróbuj rozwiązanie wymienione w polu SAML przy użyciu poniższego linku: [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#no-resource-in-requiredresourceaccess-list](../manage-apps/application-sign-in-problem-federated-sso-gallery.md?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav) |
| AADSTS650052 | Aplikacja musi mieć dostęp do usługi `(\"{name}\")` , której Twoja organizacja `\"{organization}\"` nie subskrybuje lub nie została włączona. Skontaktuj się z administratorem IT, aby zapoznać się z konfiguracją subskrypcji usług. |
| AADSTS67003 | ActorNotValidServiceIdentity |
| AADSTS70000 | InvalidGrant — uwierzytelnianie nie powiodło się. Token odświeżania jest nieprawidłowy. Błąd może być spowodowany następującymi przyczynami:<ul><li>Nagłówek powiązania tokenu jest pusty</li><li>Wartość skrótu powiązania tokenu nie jest zgodna</li></ul> |
| AADSTS70001 | UnauthorizedClient — aplikacja jest wyłączona. |
| AADSTS70002 | InvalidClient — błąd podczas walidacji poświadczeń. Określony client_secret nie jest zgodny z oczekiwaną wartością tego klienta. Popraw client_secret i spróbuj ponownie. Aby uzyskać więcej informacji, zobacz [Używanie kodu autoryzacji do żądania tokenu dostępu](v2-oauth2-auth-code-flow.md#request-an-access-token). |
| AADSTS70003 | UnsupportedGrantType — aplikacja zwróciła nieobsługiwany typ dotacji. |
| AADSTS70004 | InvalidRedirectUri — aplikacja zwróciła nieprawidłowy identyfikator URI przekierowania. Adres przekierowania określony przez klienta nie pasuje do żadnych skonfigurowanych adresów ani żadnych adresów na liście zatwierdzonych dostawców OIDC. |
| AADSTS70005 | UnsupportedResponseType — aplikacja zwróciła nieobsługiwany typ odpowiedzi z następujących powodów:<ul><li>typ odpowiedzi "token" nie jest włączony dla aplikacji</li><li>typ odpowiedzi „id_token” wymaga zakresu OpenID — zawiera nieobsługiwaną wartość parametru OAuth w zakodowanym kanale wctx</li></ul> |
| AADSTS70007 | UnsupportedResponseMode — aplikacja zwróciła nieobsługiwaną wartość dla `response_mode` żądania tokenu.  |
| AADSTS70008 | ExpiredOrRevokedGrant — token odświeżania wygasł z powodu braku aktywności. Token został wystawiony w dniu XXX i był nieaktywny przez określony czas. |
| AADSTS70011 | InvalidScope — zakres żądany przez aplikację jest nieprawidłowy. |
| AADSTS70012 | MsaServerError — Wystąpił błąd serwera podczas uwierzytelniania użytkownika MSA (konsumenta). Spróbuj ponownie. Jeśli próba ponownie nie powiedzie się, [otwórz bilet pomocy technicznej](../fundamentals/active-directory-troubleshooting-support-howto.md) |
| AADSTS70016 | Błąd przepływu urządzenia AuthorizationPending-OAuth 2,0. Autoryzacja jest w stanie oczekiwania. Urządzenie ponowi próbę sondowania żądania. |
| AADSTS70018 | BadVerificationCode — nieprawidłowy kod weryfikacyjny ze względu na to, że użytkownik pisze nieprawidłowy kod użytkownika dla przepływu kodu urządzenia. Autoryzacja nie jest zatwierdzona. |
| AADSTS70019 | CodeExpired — upłynął kod weryfikacyjny. Użytkownik musi się ponownie zalogować. |
| AADSTS75001 | BindingSerializationError — Wystąpił błąd podczas wiązania komunikatu SAML. |
| AADSTS75003 | UnsupportedBindingError — aplikacja zwróciła błąd związany z nieobsługiwanym powiązaniem (nie można wysłać odpowiedzi protokołu SAML za pośrednictwem powiązań innych niż POST protokołu HTTP). |
| AADSTS75005 | Saml2MessageInvalid — usługa Azure AD nie obsługuje żądania SAML wysyłanego przez aplikację na potrzeby logowania jednokrotnego. |
| AADSTS7500514 | Nie znaleziono obsługiwanego typu odpowiedzi SAML. Obsługiwane typy odpowiedzi to "odpowiedź" (w przestrzeni nazw XML "urn: języka Oasis: names: TC: SAML: 2.0: Protocol") lub "Assertion" (w przestrzeni nazw XML "urn: języka Oasis: names: TC: SAML: 2.0: Assertion"). Błąd aplikacji — deweloper obsłuży ten błąd.|
| AADSTS75008 | RequestDeniedError — żądanie od aplikacji zostało odrzucone, ponieważ żądanie SAML miało nieoczekiwane miejsce docelowe. |
| AADSTS75011 | NoMatchedAuthnContextInOutputClaims — Metoda uwierzytelniania, za pomocą której użytkownik uwierzytelniony przy użyciu usługi nie jest zgodna z żądaną metodą uwierzytelniania. |
| AADSTS75016 | Żądanie uwierzytelniania Saml2AuthenticationRequestInvalidNameIDPolicy-SAML2 ma nieprawidłowe NameIdPolicy. |
| AADSTS80001 | OnPremiseStoreIsNotAvailable — Agent uwierzytelniania nie może nawiązać połączenia z Active Directory. Upewnij się, że serwery agentów są członkami tego samego lasu usługi AD co użytkownicy, których hasła muszą zostać sprawdzone i mogą łączyć się z Active Directory. |
| AADSTS80002 | Przekroczono limit czasu żądania weryfikacji hasła OnPremisePasswordValidatorRequestTimedout. Upewnij się, że Active Directory jest dostępny i odpowiada na żądania od agentów. |
| AADSTS80005 | OnPremisePasswordValidatorUnpredictableWebException — wystąpił nieznany błąd podczas przetwarzania odpowiedzi z agenta uwierzytelniania. Ponów żądanie. Jeśli problem nie zniknie, [Otwórz bilet pomocy technicznej](../fundamentals/active-directory-troubleshooting-support-howto.md) , aby uzyskać więcej szczegółowych informacji o błędzie. |
| AADSTS80007 | OnPremisePasswordValidatorErrorOccurredOnPrem — Agent uwierzytelniania nie może zweryfikować hasła użytkownika. Sprawdź dzienniki agenta, aby uzyskać więcej informacji i sprawdź, czy Active Directory działa zgodnie z oczekiwaniami. |
| AADSTS80010 | OnPremisePasswordValidationEncryptionException — Agent uwierzytelniania nie może odszyfrować hasła. |
| AADSTS80012 | OnPremisePasswordValidationAccountLogonInvalidHours — użytkownicy próbujący się zalogować poza dozwolonymi godzinami (określono w usłudze AD). |
| AADSTS80013 | OnPremisePasswordValidationTimeSkew — próba uwierzytelnienia nie mogła zostać zakończona z powodu przesunięcia czasu między komputerem, na którym jest uruchomiony Agent uwierzytelniania i AD. Rozwiązywanie problemów z synchronizacją czasu. |
| AADSTS81004 | DesktopSsoIdentityInTicketIsNotAuthenticated — próba uwierzytelniania przy użyciu protokołu Kerberos nie powiodła się. |
| AADSTS81005 | DesktopSsoAuthenticationPackageNotSupported — pakiet uwierzytelniania nie jest obsługiwany. |
| AADSTS81006 | DesktopSsoNoAuthorizationHeader — nie znaleziono nagłówka autoryzacji. |
| AADSTS81007 | DesktopSsoTenantIsNotOptIn — dzierżawa nie jest włączona na potrzeby bezproblemowego logowania jednokrotnego. |
| AADSTS81009 | DesktopSsoAuthorizationHeaderValueWithBadFormat — nie można zweryfikować biletu Kerberos użytkownika. |
| AADSTS81010 | DesktopSsoAuthTokenInvalid-bezproblemowe logowanie jednokrotne nie powiodło się, ponieważ bilet protokołu Kerberos użytkownika wygasł lub jest nieprawidłowy. |
| AADSTS81011 | DesktopSsoLookupUserBySidFailed — nie można znaleźć obiektu użytkownika na podstawie informacji w biletach protokołu Kerberos użytkownika. |
| AADSTS81012 | DesktopSsoMismatchBetweenTokenUpnAndChosenUpn — użytkownik próbujący zalogować się do usługi Azure AD różni się od użytkownika zalogowanego na urządzeniu. |
| AADSTS90002 | InvalidTenantName — nie można odnaleźć nazwy dzierżawy w magazynie danych. Upewnij się, że masz prawidłowy identyfikator dzierżawy. |
| AADSTS90004 | InvalidRequestFormat — żądanie nie jest prawidłowo sformatowane. |
| AADSTS90005 | InvalidRequestWithMultipleRequirements — nie można zakończyć żądania. Żądanie jest nieprawidłowe, ponieważ nie można jednocześnie używać identyfikatora i wskazówki logowania.  |
| AADSTS90006 | ExternalServerRetryableError — usługa jest tymczasowo niedostępna.|
| AADSTS90007 | InvalidSessionId — Nieprawidłowe żądanie. Nie można przeanalizować identyfikatora przesłanej sesji. |
| AADSTS90008 | TokenForItselfRequiresGraphPermission — użytkownik lub administrator nie wyraził zgody na korzystanie z aplikacji. Co najmniej aplikacja wymaga dostępu do usługi Azure AD, określając uprawnienie Logowanie i odczyt profilu użytkownika. |
| AADSTS90009 | TokenForItselfMissingIdenticalAppIdentifier — aplikacja żąda tokenu dla siebie. Ten scenariusz jest obsługiwany tylko wtedy, gdy określony zasób używa identyfikatora aplikacji opartego na identyfikatorze GUID. |
| AADSTS90010 | NotSupported — nie można utworzyć algorytmu. |
| AADSTS90012 | RequestTimeout — upłynął limit czasu żądania. |
| AADSTS90013 | InvalidUserInput — dane wejściowe użytkownika są nieprawidłowe. |
| AADSTS90014 | MissingRequiredField — ten kod błędu może pojawić się w różnych przypadkach, gdy w poświadczeniu nie ma oczekiwanego pola. |
| AADSTS90015 | QueryStringTooLong — ciąg zapytania jest zbyt długi. |
| AADSTS90016 | MissingRequiredClaim — token dostępu jest nieprawidłowy. Brak wymaganego żądania. |
| AADSTS90019 | MissingTenantRealm — usługa Azure AD nie mogła określić identyfikatora dzierżawy z żądania. |
| AADSTS90022 | AuthenticatedInvalidPrincipalNameFormat — format nazwy głównej jest nieprawidłowy lub nie jest zgodny z oczekiwanym `name[/host][@realm]` formatem. Nazwa główna jest wymagana, Host i obszar są opcjonalne i mogą być ustawione na wartość null. |
| AADSTS90023 | InvalidRequest — żądanie usługi uwierzytelniania jest nieprawidłowe. |
| AADSTS9002313 | InvalidRequest — żądanie jest źle sformułowane lub nieprawidłowe. — Ten problem występuje, ponieważ wystąpił problem z żądaniem do określonego punktu końcowego. Sugestia tego problemu polega na uzyskaniu programu Fiddler śledzenia błędu i przejrzeniu, czy żądanie jest właściwie sformatowane. |
| AADSTS90024 | RequestBudgetExceededError — Wystąpił błąd przejściowy. Spróbuj ponownie. |
| AADSTS90033 | MsodsServiceUnavailable — usługa Katalog online firmy Microsoft (MSODS) nie jest dostępna. |
| AADSTS90036 | MsodsServiceUnretryableFailure — wystąpił nieoczekiwany błąd, którego nie można ponownie ponowić z usługi WCF hostowanej przez MSODS. [Otwórz bilet pomocy technicznej](../fundamentals/active-directory-troubleshooting-support-howto.md), aby uzyskać więcej informacji na temat błędu. |
| AADSTS90038 | NationalCloudTenantRedirection — określona dzierżawa "Y" należy do chmury krajowej "X". Bieżące wystąpienie w chmurze "Z" nie sfederować z X. Zwracany jest błąd przekierowania w chmurze. |
| AADSTS90043 | NationalCloudAuthCodeRedirection — funkcja jest wyłączona. |
| AADSTS90051 | InvalidNationalCloudId — identyfikator chmury krajowej zawiera nieprawidłowy identyfikator chmury. |
| AADSTS90055 | TenantThrottlingError — zbyt wiele żądań przychodzących. Ten wyjątek jest zgłaszany dla zablokowanych dzierżawców. |
| AADSTS90056 | BadResourceRequest — aby zrealizować kod dla tokenu dostępu, aplikacja powinna wysłać żądanie POST do `/token` punktu końcowego. Ponadto przed tym należy podać kod autoryzacji i wysłać go w żądaniu POST do `/token` punktu końcowego. Zapoznaj się z tym artykułem, aby uzyskać omówienie przepływu kodu autoryzacji OAuth 2,0: [.. /azuread-dev/V1-Protocols-OAuth-Code.MD](../azuread-dev/v1-protocols-oauth-code.md). Kierowanie użytkownika do `/authorize` punktu końcowego, który zwróci authorization_code. Po opublikowaniu żądania do `/token` punktu końcowego użytkownik otrzymuje token dostępu. Zaloguj się do Azure Portal i sprawdź **punkty końcowe > rejestracje aplikacji** , aby upewnić się, że dwa punkty końcowe zostały prawidłowo skonfigurowane. |
| AADSTS90072 | PassThroughUserMfaError — konto zewnętrzne, za pomocą którego loguje się użytkownik, nie istnieje w dzierżawie, do której się zarejestrował; Dlatego użytkownik nie może spełnić wymagań usługi MFA dla dzierżawy. Ten błąd może również wystąpić, jeśli użytkownicy są zsynchronizowani, ale w atrybucie ImmutableID (sourceAnchor) między Active Directory i Azure AD występuje niezgodność. Najpierw należy dodać konto jako użytkownika zewnętrznego w dzierżawie. Wyloguj się i zaloguj się przy użyciu innego konta użytkownika usługi Azure AD. |
| AADSTS90081 | OrgIdWsFederationMessageInvalid — Wystąpił błąd, gdy usługa podjęła próbę przetworzenia komunikatu WS-Federation. Komunikat jest nieprawidłowy. |
| AADSTS90082 | OrgIdWsFederationNotSupported — wybrane zasady uwierzytelniania dla żądania nie są obecnie obsługiwane. |
| AADSTS90084 | OrgIdWsFederationGuestNotAllowed — konta gościa nie są dozwolone w tej witrynie. |
| AADSTS90085 | OrgIdWsFederationSltRedemptionFailed — usługa nie może wystawić tokenu, ponieważ obiekt firmy nie został jeszcze zainicjowany. |
| AADSTS90086 | OrgIdWsTrustDaTokenExpired — token użytkownika DA ważność. |
| AADSTS90087 | OrgIdWsFederationMessageCreationFromUriFailed — Wystąpił błąd podczas tworzenia komunikatu WS-Federation z identyfikatora URI. |
| AADSTS90090 | GraphRetryableError — usługa jest tymczasowo niedostępna. |
| AADSTS90091 | GraphServiceUnreachable |
| AADSTS90092 | GraphNonRetryableError |
| AADSTS90093 | GraphUserUnauthorized-Graph zwrócił z niedozwolonym kodem błędu dla żądania. |
| AADSTS90094 | AdminConsentRequired — wymagana jest zgoda administratora. |
| AADSTS900382 | Klient poufny nie jest obsługiwany w żądaniu między chmurą. |
| AADSTS90099 | Aplikacja "{appId}" ({nazwa_aplikacji}) nie została autoryzowana w dzierżawie "{dzierżawca}". Aplikacje muszą mieć autoryzację, aby uzyskać dostęp do dzierżawy klienta, zanim będzie można z nich korzystać Administratorzy delegowany przez partnerów. Zapewnij wstępną zgodę lub wykonaj odpowiednie interfejsy API Centrum partnerskiego, aby autoryzować aplikację. |
| AADSTS90100 | InvalidRequestParameter — parametr jest pusty lub nieprawidłowy. |
| AADSTS901002 | AADSTS901002: parametr żądania "Resource" nie jest obsługiwany. |
| AADSTS90101 | InvalidEmailAddress — podane dane nie są prawidłowym adresem e-mail. Adres e-mail musi mieć format `someone@example.com` . |
| AADSTS90102 | InvalidUriParameter — wartość musi być prawidłowym bezwzględnym identyfikatorem URI. |
| AADSTS90107 | InvalidXml — żądanie jest nieprawidłowe. Upewnij się, że dane nie zawierają nieprawidłowych znaków.|
| AADSTS90114 | InvalidExpiryDate — sygnatura czasowa wygaśnięcia tokenu zbiorczego spowoduje wystawienie wygasłego tokenu. |
| AADSTS90117 | InvalidRequestInput |
| AADSTS90119 | InvalidUserCode — kod użytkownika ma wartość null lub jest pusty.|
| AADSTS90120 | InvalidDeviceFlowRequest — żądanie zostało już autoryzowane lub odrzucone. |
| AADSTS90121 | InvalidEmptyRequest — nieprawidłowe puste żądanie.|
| AADSTS90123 | IdentityProviderAccessDenied — nie można wystawić tokenu, ponieważ dostawca wystawiania dla tożsamości lub żądania odrzucił żądanie. |
| AADSTS90124 | V1ResourceV2GlobalEndpointNotSupported — zasób nie jest obsługiwany w `/common` `/consumers` punktach końcowych lub. `/organizations`Zamiast tego użyj punktu końcowego określonego dla dzierżawy. |
| AADSTS90125 | DebugModeEnrollTenantNotFound — użytkownik nie jest w systemie. Upewnij się, że nazwa użytkownika została wprowadzona prawidłowo. |
| AADSTS90126 | DebugModeEnrollTenantNotInferred — typ użytkownika nie jest obsługiwany w tym punkcie końcowym. System nie może wywnioskować dzierżawy użytkownika z nazwy użytkownika. |
| AADSTS90130 | NonConvergedAppV2GlobalEndpointNotSupported — aplikacja nie jest obsługiwana w przypadku `/common` `/consumers` punktów końcowych lub. `/organizations`Zamiast tego użyj punktu końcowego określonego dla dzierżawy. |
| AADSTS120000 | PasswordChangeIncorrectCurrentPassword |
| AADSTS120002 | PasswordChangeInvalidNewPasswordWeak |
| AADSTS120003 | PasswordChangeInvalidNewPasswordContainsMemberName |
| AADSTS120004 | PasswordChangeOnPremComplexity |
| AADSTS120005 | PasswordChangeOnPremSuccessCloudFail |
| AADSTS120008 | PasswordChangeAsyncJobStateTerminated — Wystąpił błąd niepowodujący ponowienia próby.|
| AADSTS120011 | PasswordChangeAsyncUpnInferenceFailed |
| AADSTS120012 | PasswordChangeNeedsToHappenOnPrem |
| AADSTS120013 | PasswordChangeOnPremisesConnectivityFailure |
| AADSTS120014 | PasswordChangeOnPremUserAccountLockedOutOrDisabled |
| AADSTS120015 | PasswordChangeADAdminActionRequired |
| AADSTS120016 | PasswordChangeUserNotFoundBySspr |
| AADSTS120018 | PasswordChangePasswordDoesnotComplyFuzzyPolicy |
| AADSTS120020 | PasswordChangeFailure |
| AADSTS120021 | PartnerServiceSsprInternalServiceError |
| AADSTS130004 | NgcKeyNotFound — podmiot zabezpieczeń użytkownika nie ma skonfigurowanego klucza identyfikatora NGC. |
| AADSTS130005 | Weryfikowanie sygnatury klucza NgcInvalidSignature-NGC nie powiodło się.|
| AADSTS130006 | NgcTransportKeyNotFound — klucz transportu NGC nie jest skonfigurowany na urządzeniu. |
| AADSTS130007 | NgcDeviceIsDisabled — urządzenie jest wyłączone. |
| AADSTS130008 | NgcDeviceIsNotFound — nie znaleziono urządzenia, do którego odwołuje się klucz NGC. |
| AADSTS135010 | KeyNotFound |
| AADSTS140000 | InvalidRequestNonce — nie podano identyfikatora żądania. |
| AADSTS140001 | InvalidSessionKey — klucz sesji jest nieprawidłowy.|
| AADSTS165900 | InvalidApiRequest — Nieprawidłowe żądanie. |
| AADSTS220450 | UnsupportedAndroidWebViewVersion — wersja programu Chrome WebView nie jest obsługiwana. |
| AADSTS220501 | InvalidCrlDownload |
| AADSTS221000 | DeviceOnlyTokensNotSupportedByResource — zasób nie jest skonfigurowany do akceptowania tokenów tylko dla urządzeń. |
| AADSTS240001 | BulkAADJTokenUnauthorized — użytkownik nie ma uprawnień do rejestrowania urządzeń w usłudze Azure AD. |
| AADSTS240002 | RequiredClaimIsMissing — id_token nie może być używana jako `urn:ietf:params:oauth:grant-type:jwt-bearer` Grant.|
| AADSTS530032 | BlockedByConditionalAccessOnSecurityPolicy — Administrator dzierżawy skonfigurował zasadę zabezpieczeń, która blokuje to żądanie. Sprawdź zasady zabezpieczeń zdefiniowane na poziomie dzierżawy, aby ustalić, czy żądanie spełnia wymagania zasad. |
| AADSTS700016 | UnauthorizedClient_DoesNotMatchRequest — aplikacja nie została znaleziona w katalogu/dzierżawie. Taka sytuacja może wystąpić, jeśli aplikacja nie została zainstalowana przez administratora dzierżawy lub żaden użytkownik w dzierżawie nie wyraził odpowiedniej zgody. Być może nieprawidłowo skonfigurowano wartość identyfikatora aplikacji lub wysyłano żądanie uwierzytelniania do niewłaściwej dzierżawy. |
| AADSTS700020 | InteractionRequired — udzielanie dostępu wymaga interakcji. |
| AADSTS700022 | InvalidMultipleResourcesScope — podana wartość zakresu parametru wejściowego jest nieprawidłowa, ponieważ zawiera więcej niż jeden zasób. |
| AADSTS700023 | InvalidResourcelessScope — podana wartość dla zakresu parametru wejściowego jest nieprawidłowa podczas żądania tokenu dostępu. |
| AADSTS7000215 | Podano nieprawidłowy klucz tajny klienta. Błąd dewelopera — aplikacja próbuje zalogować się bez wymaganych lub poprawnych parametrów uwierzytelniania.|
| AADSTS7000222 | InvalidClientSecretExpiredKeysProvided — wygasłe klucze tajne klienta. Odwiedź Azure Portal, aby utworzyć nowe klucze dla aplikacji, lub Rozważ użycie poświadczeń certyfikatu w celu zwiększenia bezpieczeństwa: [https://aka.ms/certCreds](https://aka.ms/certCreds) |
| AADSTS700005 | Kod autoryzacji podany przez InvalidGrantRedeemAgainstWrongTenant jest przeznaczony do użycia w odniesieniu do innych dzierżawców, w rezultacie odrzucony. Kod autoryzacji OAuth2 musi zostać zrealizowany w odniesieniu do tej samej dzierżawy, dla którego uzyskano (odpowiednio/typowe lub/{tenant-ID}) |
| AADSTS1000000 | UserNotBoundError — interfejs API bind wymaga, aby użytkownik usługi Azure AD mógł także uwierzytelniać się przy użyciu zewnętrznego dostawcy tożsamości, który jeszcze nie wystąpił. |
| AADSTS1000002 | BindCompleteInterruptError — powiązanie zostało wykonane pomyślnie, ale użytkownik musi zostać poinformowany. |
| AADSTS7000112 | UnauthorizedClientApplicationDisabled — aplikacja jest wyłączona. |
| AADSTS7000114| Aplikacja "appIdentifier" nie może tworzyć aplikacji w imieniu.|
| AADSTS7500529 | Wartość "SAMLId-GUID" nie jest prawidłowym IDENTYFIKATORem SAML — usługa Azure AD używa tego atrybutu do wypełniania atrybutu InResponseTo zwróconej odpowiedzi. Identyfikator nie może zaczynać się od liczby, więc typową strategią jest dołączenie ciągu takiego jak "ID" do ciągu reprezentującego identyfikator GUID. Na przykład id6c1c178c166d486687be4aaf5e482730 jest prawidłowym IDENTYFIKATORem. |

## <a name="next-steps"></a>Następne kroki

* Masz pytanie lub nie możesz znaleźć tego, czego szukasz? Utwórz problem w usłudze GitHub lub zapoznaj się z [pomocą techniczną i opcjami pomocy dla deweloperów](./developer-support-help-options.md) , aby dowiedzieć się więcej na temat innych sposobów uzyskiwania pomocy i obsługi technicznej.
