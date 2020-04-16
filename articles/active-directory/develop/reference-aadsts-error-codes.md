---
title: Kody błędów autoryzacji uwierzytelniania usługi Azure AD &
description: Dowiedz się więcej o kodach błędów AADSTS, które są zwracane z usługi tokenu zabezpieczeń usługi Azure AD (STS).
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: reference
ms.date: 04/07/2020
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 87a962709638391887eaa275f059bf4ceae9218b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406969"
---
# <a name="azure-ad-authentication-and-authorization-error-codes"></a>Kody błędów uwierzytelniania i autoryzacji usługi Azure AD

Szukasz informacji na temat kodów błędów AADSTS, które są zwracane z usługi tokenu zabezpieczającego usługi Azure Active Directory (Azure AD)? Przeczytaj ten dokument, aby znaleźć opisy błędów AADSTS, poprawki i niektóre sugerowane obejścia.

> [!NOTE]
> Niniejsze informacje mają charakter wstępny i mogą ulec zmianom. Masz pytanie lub nie możesz znaleźć tego, czego szukasz? Utwórz problem z githubem lub zobacz [Opcje pomocy technicznej i pomocy dla deweloperów,](active-directory-develop-help-support.md) aby dowiedzieć się więcej o innych sposobach uzyskania pomocy i pomocy technicznej.
>
> Ta dokumentacja jest dostarczana dla wskazówki dla deweloperów i administratorów, ale nigdy nie powinny być używane przez samego klienta. Kody błędów mogą ulec zmianie w dowolnym momencie, aby zapewnić bardziej szczegółowe komunikaty o błędach, które mają pomóc deweloperowi podczas tworzenia aplikacji. Aplikacje, które biorą zależność od numerów kodu tekstu lub błędu zostaną przerwane w czasie.

## <a name="handling-error-codes-in-your-application"></a>Obsługa kodów błędów w aplikacji

[Specyfikacja OAuth2.0](https://tools.ietf.org/html/rfc6749#section-5.2) zawiera wskazówki dotyczące obsługi błędów podczas uwierzytelniania przy użyciu `error` części odpowiedzi na błąd. 

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
| `error_description` | Określony komunikat o błędzie, który może pomóc deweloperowi zidentyfikować główną przyczynę błędu uwierzytelniania. Nigdy nie używaj tego pola do reagowania na błąd w kodzie. |
| `error_codes` | Lista kodów błędów specyficznych dla STS, które mogą pomóc w diagnostyce.  |
| `timestamp`   | Czas, w którym wystąpił błąd. |
| `trace_id`    | Unikatowy identyfikator żądania, który może pomóc w diagnostyce. |
| `correlation_id` | Unikatowy identyfikator żądania, który może pomóc w diagnostyce między składnikami. |
| `error_uri` |  Łącze do strony wyszukiwania błędów z dodatkowymi informacjami o błędzie.  Jest to tylko do użycia przez dewelopera, nie należy przedstawiać go użytkownikom.  Obecny tylko wtedy, gdy system wyszukiwania błędów ma dodatkowe informacje o błędzie — nie wszystkie błędy mają dodatkowe informacje.|

Pole `error` zawiera kilka możliwych wartości — przejrzyj łącza do dokumentacji protokołu i specyfikacje OAuth `authorization_pending` 2.0, aby dowiedzieć się więcej o konkretnych błędach (na przykład w [przepływie kodu urządzenia)](v2-oauth2-device-code.md)i o tym, jak na nie reagować.  Niektóre typowe są wymienione tutaj:

| Kod błędu         | Opis        | Akcja klienta    |
|--------------------|--------------------|------------------|
| `invalid_request`  | Błąd protokołu, taki jak brak wymaganego parametru. | Napraw i ponownie prześlij żądanie.|
| `invalid_grant`    | Niektóre materiały uwierzytelniania (kod uwierzytelniania, token odświeżania, token dostępu, wyzwanie PKCE) były nieprawidłowe, niepardzielalne, brakujące lub w inny sposób nieużywalne | Spróbuj nowego żądania `/authorize` do punktu końcowego, aby uzyskać nowy kod autoryzacji.  Należy wziąć pod uwagę przeglądanie i sprawdzanie poprawności użycia protokołów przez tę aplikację. |
| `unauthorized_client` | Uwierzytelniony klient nie jest upoważniony do używania tego typu dotacji autoryzacji. | Zwykle dzieje się tak, gdy aplikacja kliencka nie jest zarejestrowana w usłudze Azure AD lub nie jest dodawana do dzierżawy usługi Azure AD użytkownika. Aplikacja może monitować użytkownika o instrukcje dotyczące instalowania aplikacji i dodawania jej do usługi Azure AD. |
| `invalid_client` | Uwierzytelnianie klienta nie powiodło się.  | Poświadczenia klienta są nieprawidłowe. Aby naprawić, administrator aplikacji aktualizuje poświadczenia.   |
| `unsupported_grant_type` | Serwer autoryzacji nie obsługuje typu udzielania autoryzacji. | Zmień typ dotacji w żądaniu. Ten typ błędu powinien występować tylko podczas opracowywania i być wykryty podczas wstępnego testowania. |
| `invalid_resource` | Zasób docelowy jest nieprawidłowy, ponieważ nie istnieje, usługa Azure AD nie może go znaleźć lub nie jest poprawnie skonfigurowany. | Oznacza to, że zasób, jeśli istnieje, nie został skonfigurowany w dzierżawie. Aplikacja może monitować użytkownika o instrukcje dotyczące instalowania aplikacji i dodawania jej do usługi Azure AD.  Podczas tworzenia zwykle wskazuje niepoprawnie konfiguracji dzierżawy testu lub literówki w nazwie żądanego zakresu. |
| `interaction_required` | Żądanie wymaga interakcji z użytkownikiem. Na przykład wymagany jest dodatkowy krok uwierzytelniania. | Ponów próbę żądania z tego samego zasobu, interactievly, tak aby użytkownik może wykonać wszelkie wyzwania wymagane.  |
| `temporarily_unavailable` | Serwer jest tymczasowo zbyt zajęty, aby obsłużyć żądanie. | Ponów próbę żądania. Aplikacja kliencka może wyjaśnić użytkownikowi, że jego odpowiedź jest opóźniona z powodu stanu tymczasowego. |

## <a name="lookup-current-error-code-information"></a>Wyszukiwanie bieżących informacji o kodzie błędu
Kody błędów i komunikaty mogą ulec zmianie.  Aby uzyskać najbardziej aktualne informacje, `https://login.microsoftonline.com/error` zapoznaj się ze stroną, aby znaleźć opisy błędów AADSTS, poprawki i sugerowane obejścia.  

Wyszukaj numeryczną część zwracanego kodu błędu.  Na przykład, jeśli otrzymałeś kod błędu "AADSTS16000", `https://login.microsoftonline.com/error` wykonaj wyszukiwanie w "16000".  Możesz również połączyć się bezpośrednio z określonym błędem, dodając `https://login.microsoftonline.com/error?code=16000`numer kodu błędu do adresu URL: .

## <a name="aadsts-error-codes"></a>Kody błędów AADSTS

| Błąd | Opis |
|---|---|
| AADSTS16000 | SelectUserAccount — jest to przerwanie generowane przez usługę Azure AD, co powoduje, że interfejs użytkownika, który umożliwia użytkownikowi wybór spośród wielu prawidłowych sesji logowania jednokrotnego. Ten błąd jest dość powszechne i mogą `prompt=none` być zwracane do aplikacji, jeśli jest określony. |
| AADSTS16001 | UserAccountSelectionInvalid — ten błąd zostanie wyświetlony, jeśli użytkownik kliknie kafelek, który logika wyboru sesji odrzuciła. Po wyzwoleniu ten błąd umożliwia użytkownikowi odzyskanie, wybierając ze zaktualizowanej listy kafelków/sesji lub wybierając inne konto. Ten błąd może wystąpić z powodu wady kodu lub sytuacji wyścigu. |
| AADSTS16002 | AppSessionSelectionInvalid — wymagania dotyczące identyfikatora SID określonej w aplikacji nie zostały spełnione.  |
| AADSTS16003 | SsoUserAccountNotFoundInResourceTenant — wskazuje, że użytkownik nie został jawnie dodany do dzierżawy. |
| AADSTS17003 | CredentialKeyProvisioningFailed — usługa Azure AD nie może aprowizować klucza użytkownika. |
| AADSTS20001 | WsFedSignInResponseError — wystąpił problem z dostawcą tożsamości federacyjnej. Skontaktuj się ze swoim dostawcą tożsamości, aby rozwiązać ten problem. |
| AADSTS20012 | WsFedMessageInvalid — wystąpił problem z dostawcą tożsamości federacyjnej. Skontaktuj się ze swoim dostawcą tożsamości, aby rozwiązać ten problem. |
| AADSTS20033 | FedMetadataInvalidTenantName — występuje problem z dostawcą tożsamości federacyjnej. Skontaktuj się ze swoim dostawcą tożsamości, aby rozwiązać ten problem. |
| AADSTS40008 | OAuth2IdPUnretryableServerError — wystąpił problem z dostawcą tożsamości federacyjnej. Skontaktuj się ze swoim dostawcą tożsamości, aby rozwiązać ten problem. |
| AADSTS40009 | OAuth2IdPRefreshTokenRedemptionUserError — występuje problem z dostawcą tożsamości federacyjnej. Skontaktuj się ze swoim dostawcą tożsamości, aby rozwiązać ten problem. |
| AADSTS40010 | OAuth2IdPRetryableServerError — wystąpił problem z dostawcą tożsamości federacyjnej. Skontaktuj się ze swoim dostawcą tożsamości, aby rozwiązać ten problem. |
| AADSTS40015 | OAuth2IdPAuthCodeRedemptionUserError — wystąpił problem z dostawcą tożsamości federacyjnej. Skontaktuj się ze swoim dostawcą tożsamości, aby rozwiązać ten problem. |
| AADSTS50000 | TokenIssuanceError — występuje problem z usługą logowania. [Otwórz bilet pomocy technicznej](../fundamentals/active-directory-troubleshooting-support-howto.md), aby rozwiązać ten problem. |
| AADSTS50001 | InvalidResource — zasób jest wyłączony lub nie istnieje. Sprawdź kod aplikacji, aby upewnić się, że określono dokładny adres URL zasobu dla zasobu, do którego próbujesz uzyskać dostęp.  |
| AADSTS50002 | NotAllowedTenant — logowanie nie powiodło się z powodu ograniczonego dostępu serwera proxy w dzierżawie. Jeśli jest to własne zasady dzierżawy, można zmienić ustawienia dzierżawy z ograniczeniami, aby rozwiązać ten problem. |
| AADSTS50003 | MissingSigningKey — logowanie nie powiodło się z powodu braku klucza podpisywania lub certyfikatu. Może to być spowodowane tym, że w aplikacji nie skonfigurowano klucza podpisywania. Zapoznaj się z rezolucjami [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured)przedstawionymi na . Jeśli nadal widzisz problemy, skontaktuj się z właścicielem aplikacji lub administratorem aplikacji. |
| AADSTS50005 | DevicePolicyError — użytkownik próbował zalogować się do urządzenia z platformy, która obecnie nie jest obsługiwana za pośrednictwem zasad dostępu warunkowego. |
| AADSTS50006 | InvalidSignature — weryfikacja podpisu nie powiodła się z powodu nieprawidłowego podpisu. |
| AADSTS50007 | PartnerEncryptionCertificateMissing — nie znaleziono certyfikatu szyfrowania partnera dla tej aplikacji. [Otwórz bilet pomocy technicznej](../fundamentals/active-directory-troubleshooting-support-howto.md) w firmie Microsoft, aby uzyskać ten stały. |
| AADSTS50008 | InvalidSamlToken — nie ma potwierdzenia SAML lub jest błędnie skonfigurowany w tokenie. Skontaktuj się z dostawcą federacji. |
| AADSTS50010 | AudienceUriValidationFailed — sprawdzanie poprawności identyfikatora URI odbiorców dla aplikacji nie powiodło się, ponieważ nie skonfigurowano żadnych odbiorców tokenów. |
| AADSTS50011 | InvalidReplyTo — brak adresu odpowiedzi, nieprawidłowo skonfigurowany lub nie pasuje do adresów odpowiedzi skonfigurowanych dla aplikacji.  W ramach rozwiązania upewnij się, że ten brakujący adres odpowiedzi do aplikacji usługi Azure Active Directory lub mieć osobę z uprawnieniami do zarządzania aplikacją w usłudze Active Directory, zrób to za Ciebie.|
| AADSTS50012 | UwierzytelnianieNienie - Uwierzytelnianie nie powiodło się z jednego z następujących powodów:<ul><li>Nazwa podmiotu certyfikatu podpisywania nie jest autoryzowana</li><li>Nie znaleziono pasujących zasad zaufanego urzędu dla nazwy podmiotu autoryzowanego</li><li>Łańcuch certyfikatów jest nieprawidłowy</li><li>Certyfikat podpisywania jest nieprawidłowy</li><li>Zasada nie jest skonfigurowana w dzierżawie</li><li>Odcisk palca certyfikatu podpisywania nie jest autoryzowany</li><li>Twierdzenie klienta zawiera nieprawidłowy podpis</li></ul> |
| AADSTS50013 | InvalidAssertion — twierdzenie jest nieprawidłowe z różnych powodów — wystawca tokenu nie pasuje do wersji interfejsu api w prawidłowym zakresie czasu -expired -malformed — Token odświeżania w asercji nie jest podstawowym tokenem odświeżania. |
| AADSTS50014 | GuestUserInPendingState — wykup użytkownika jest w stanie oczekiwania. Konto użytkownika gościa nie jest jeszcze w pełni utworzone. |
| AADSTS50015 | ViralUserLegalAgeConsentState - Użytkownik wymaga zgody prawnej grupy wiekowej. |
| AADSTS50017 | CertificateValidationFailed - Sprawdzanie poprawności certyfikacji nie powiodło się, przyczyny z następujących powodów:<ul><li>Nie można odnaleźć certyfikatu wystawcy na liście zaufanych certyfikatów</li><li>Nie można odnaleźć oczekiwanego elementu CrlSegment</li><li>Nie można odnaleźć certyfikatu wystawcy na liście zaufanych certyfikatów</li><li>Punkt dystrybucji różnicowej listy CRL jest skonfigurowany bez odpowiedniego punktu dystrybucji listy CRL</li><li>Nie można pobrać prawidłowych segmentów programu CRL z powodu problemu z przesuwem czasu</li><li>Nie można pobrać listy CRL</li></ul>Skontaktuj się z administratorem dzierżawy. |
| AADSTS50020 | UserUnauthorized — użytkownicy są nieautoryzowani do wywołania tego punktu końcowego. |
| AADSTS50027 | InvalidJwtToken — nieprawidłowy token JWT z następujących powodów:<ul><li>nie zawiera oświadczenia nonce, oświadczenia sub</li><li>niezgodność identyfikatora podmiotu</li><li>zduplikowane oświadczenie w oświadczeniach idToken</li><li>nieoczekiwany wystawca</li><li>nieoczekiwani odbiorcy</li><li>nie mieści się w prawidłowym zakresie czasu </li><li>format tokenu nie jest prawidłowy</li><li>weryfikacja tokenu zewnętrznego identyfikatora od wystawcy nie powiodła się.</li></ul> |
| AADSTS50029 | Nieprawidłowy identyfikator URI — nazwa domeny zawiera nieprawidłowe znaki. Skontaktuj się z administratorem dzierżawy. |
| AADSTS50032 | WeakRsaKey — wskazuje błędną próbę użycia przez użytkownika słabego klucza RSA. |
| AADSTS50033 | RetryableError — wskazuje błąd przejściowy nie związany z operacjami bazy danych. |
| AADSTS50034 | UserAccountNotFound — Aby zalogować się do tej aplikacji, konto musi zostać dodane do katalogu. |
| AADSTS50042 | UnableToGeneratePairwiseIdentifierWithMissingSalt — sól wymagana do wygenerowania identyfikatora parowania jest zasadniczo nika. Skontaktuj się z administratorem dzierżawy. |
| AADSTS50043 | UnableToGeneratePairwiseIdentifierWithMultipleSalts |
| AADSTS50048 | SubjectMismatchesIssuer — podmiot niezgodności oświadczenie wystawcy w twierdzeniu klienta. Skontaktuj się z administratorem dzierżawy. |
| AADSTS50049 | NoSuchInstanceForDiscovery — nieznane lub nieprawidłowe wystąpienie. |
| AADSTS50050 | MalformedDiscoveryRequest — żądanie jest zniekształcone. |
| AADSTS50053 | IdsLocked — konto jest zablokowane, ponieważ użytkownik próbował zalogować się zbyt wiele razy przy przy obliczu nieprawidłowego identyfikatora użytkownika lub hasła. |
| AADSTS50055 | InvalidPasswordExpiredPassword — hasło wygasło. |
| AADSTS50056 | Nieprawidłowe lub null hasło -Hasło nie istnieje w magazynie dla tego użytkownika. |
| AADSTS50057 | UserDisabled — konto użytkownika jest wyłączone. Konto zostało wyłączone przez administratora. |
| AADSTS50058 | UserInformationNotProvided — oznacza to, że użytkownik nie jest zalogowany. Jest to typowy błąd, który jest oczekiwany, gdy użytkownik jest nieuwierzyty i nie zalogował się.</br>Jeśli ten błąd jest zalecany w kontekście jedno i tak, gdy użytkownik wcześniej się zalogował, oznacza to, że sesja logowanie jednośmiętne nie została znaleziona lub nieprawidłowa.</br>Ten błąd może zostać zwrócony do aplikacji, jeśli prompt=none jest określony. |
| AADSTS50059 | MissingTenantRealmAndNoUserInformationProvided — informacje identyfikujące dzierżawę nie zostały znalezione w żądaniu lub implikowane przez dostarczone poświadczenia. Użytkownik może skontaktować się z administratorem dzierżawy, aby pomóc rozwiązać problem. |
| AADSTS50061 | SignoutInvalidRequest — żądanie wylogowywania jest nieprawidłowe. |
| AADSTS50064 | CredentialAuthenticationError — sprawdzanie poprawności poświadczeń na nazwę użytkownika lub hasło nie powiodło się. |
| AADSTS50068 | SignoutInitiatorNotParticipant - Signout nie powiodło się. Aplikacja, która zainicjowała wylogowanie, nie jest uczestnikiem bieżącej sesji. |
| AADSTS50070 | SignoutUnknownSessionIdentifier — signout nie powiodło się. Żądanie wylogowywania określiło identyfikator nazwy, który nie odpowiadał istniejącym sesjom. |
| AADSTS50071 | SignoutMessageExpired — żądanie wylogowania wygasło. |
| AADSTS50072 | UserStrongAuthEnrollmentRequiredInterrupt — użytkownik musi zarejestrować się do uwierzytelniania drugiego czynnika (interaktywnego). |
| AADSTS50074 | UserStrongAuthClientAuthNRequiredInterrupt — wymagane jest silne uwierzytelnianie, a użytkownik nie przeszedł wyzwania usługi MFA. |
| AADSTS50076 | UserStrongAuthClientAuthNRequired — ze względu na zmianę konfiguracji dokonaną przez administratora lub przeniesienie do nowej lokalizacji, użytkownik musi użyć uwierzytelniania wieloskładnikowego, aby uzyskać dostęp do zasobu. Ponów próbę ponownego użycia nowego żądania autoryzacji dla zasobu. |
| AADSTS50079 | UserStrongAuthEnrollmentRequired — Ze względu na zmianę konfiguracji dokonaną przez administratora lub, ponieważ użytkownik przeniósł się do nowej lokalizacji, użytkownik jest zobowiązany do korzystania z uwierzytelniania wieloskładnikowego. |
| AADSTS50085 | Token odświeżania wymaga danych logowania dostawy tożsamości z serwisów społecznościowych. Użytkownik musi zalogować się ponownie przy użyciu nazwy użytkownika i hasła |
| AADSTS50086 | Okręg wyborczy SasNonRetryableError |
| AADSTS50087 | SasRetryableError — usługa jest tymczasowo niedostępna. Spróbuj ponownie. |
| AADSTS50089 | Token uwierzytelniania wygasł — uwierzytelnianie nie powiodło się. Niech użytkownik spróbuje ponownie zalogować się za pomocą nazwy użytkownika -password. |
| AADSTS50097 | Wymagane uwierzytelnienie urządzenia — wymagane jest uwierzytelnianie urządzenia. |
| AADSTS50099 | PKeyAuthInvalidJwtUnauthorized — podpis JWT jest nieprawidłowy. |
| AADSTS50105 | EntitlementGrantsNotFound — zalogowany użytkownik nie jest przypisany do roli dla zalogowanego w aplikacji. Przypisz użytkownika do aplikacji. Aby uzyskać[https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role)więcej informacji: . |
| AADSTS50107 | InvalidRealmUri — żądany obiekt obszaru federacji nie istnieje. Skontaktuj się z administratorem dzierżawy. |
| AADSTS50120 | ThresholdJwtInvalidJwtFormat - Problem z nagłówkiem JWT. Skontaktuj się z administratorem dzierżawy. |
| AADSTS50124 | ClaimsTransformationInvalidInputParameter — transformacja oświadczeń zawiera nieprawidłowy parametr wejściowy. Skontaktuj się z administratorem dzierżawy, aby zaktualizować zasady. |
| AADSTS50125 | PasswordResetRegistrationRequiredInterrupt - Logowanie zostało przerwane z powodu resetowania hasła lub wpisu rejestracji hasła. |
| AADSTS50126 | InvalidUserNameOrPassword — błąd sprawdzania poprawności poświadczeń z powodu nieprawidłowej nazwy użytkownika lub hasła. |
| AADSTS50127 | BrokerAppNotInstalled — użytkownik musi zainstalować aplikację brokera, aby uzyskać dostęp do tej zawartości. |
| AADSTS50128 | Nieprawidłowa nazwa domeny — w żądaniu nie znaleziono żadnych informacji identyfikujących dzierżawę ani dorozumianych przez podane poświadczenia. |
| AADSTS50129 | DeviceIsNotWorkplaceJoined — do rejestracji urządzenia wymagane jest sprzężenie w miejscu pracy. |
| AADSTS50131 | Funkcja Dostępu warunkowegoNiezała — wskazuje różne błędy dostępu warunkowego, takie jak zły stan urządzenia z systemem Windows, żądanie zablokowane z powodu podejrzanej aktywności, zasad dostępu lub decyzji dotyczących zasad zabezpieczeń. |
| AADSTS50132 | SsoArtifactInvalidOrExpired — sesja jest nieprawidłowa z powodu wygaśnięcia hasła lub ostatniej zmiany hasła. |
| AADSTS50133 | SsoArtifactRevoked — sesja jest nieprawidłowa z powodu wygaśnięcia hasła lub ostatniej zmiany hasła. |
| AADSTS50134 | DeviceFlowAuthorizeWrongDatacenter — nieprawidłowe centrum danych. Aby autoryzować żądanie zainicjowane przez aplikację w przepływie urządzenia OAuth 2.0, strona autoryzująca musi znajdować się w tym samym centrum danych, w którym znajduje się oryginalne żądanie. |
| AADSTS50135 | PasswordChangeCompromisedPassword - Zmiana hasła jest wymagana ze względu na ryzyko konta. |
| AADSTS50136 | RedirectMsaSessionToApp — wykryto pojedynczą sesję MSA. |
| AADSTS50139 | SessionMissingMsaOAuth2RefreshToken — sesja jest nieprawidłowa z powodu brakującego zewnętrznego tokenu odświeżania. |
| AADSTS50140 | KmsiInterrupt — ten błąd wystąpił z powodu przerwania "Keep me signed in", gdy użytkownik logował się. [Otwórz bilet pomocy technicznej](../fundamentals/active-directory-troubleshooting-support-howto.md) z identyfikatorem korelacji, identyfikatorem żądania i kodem błędu, aby uzyskać więcej informacji. |
| AADSTS50143 | Niezgodność sesji — sesja jest nieprawidłowa, ponieważ dzierżawa użytkownika nie jest zgodna z wskazówką domeny z powodu innego zasobu.  [Otwórz bilet pomocy technicznej](../fundamentals/active-directory-troubleshooting-support-howto.md) z identyfikatorem korelacji, identyfikatorem żądania i kodem błędu, aby uzyskać więcej szczegółów. |
| AADSTS50144 | InvalidPasswordExpiredOnPremPassword — hasło usługi Active Directory użytkownika wygasło. Wygeneruj nowe hasło dla użytkownika lub niech użytkownik użyje samoobsługowego narzędzia resetowania, aby zresetować swoje hasło. |
| AADSTS50146 | MissingCustomSigningKey — ta aplikacja musi być skonfigurowana przy pomocą klucza podpisywania specyficzne dla aplikacji. Albo nie została skonfigurowana przy jego użyciu, albo klucz wygasł lub nie jest jeszcze ważny. |
| AADSTS50147 | MissingCodeChallenge — rozmiar parametru wyzwanie kodu jest nieprawidłowy. |
| AADSTS50155 | DeviceAuthenticationFailed — uwierzytelnianie urządzenia nie powiodło się dla tego użytkownika. |
| AADSTS50158 | ExternalSecurityChallenge - Zewnętrzne wyzwanie bezpieczeństwa nie zostało spełnione. |
| AADSTS50161 | InvalidExternalSecurityChallengeConfiguration — oświadczenia wysyłane przez zewnętrznego dostawcę nie wystarczą lub brak oświadczenia żądanego do zewnętrznego dostawcy. |
| AADSTS50166 | ExternalClaimsProviderThrottled — nie można wysłać żądania do dostawcy oświadczeń. |
| AADSTS50168 | ChromeBrowserSsoInterruptRequired — klient jest w stanie uzyskać token SSO za pośrednictwem rozszerzenia Kont systemu Windows 10, ale token nie został znaleziony w żądaniu lub dostarczony token wygasł. |
| AADSTS50169 | InvalidRequestBadRealm — obszar nie jest skonfigurowanym obszarem bieżącego obszaru nazw usługi. |
| AADSTS50170 | MissingExternalClaimsProviderMapping — brakuje mapowania formantów zewnętrznych. |
| AADSTS50177 | ExternalChallengeNotSupportedForPassthroughUsers — wyzwanie zewnętrzne nie jest obsługiwane dla użytkowników przechodzień. |
| AADSTS50178 | SessionControlNotSupportedForPassthroughUsers — kontrola sesji nie jest obsługiwana dla użytkowników przekazu. |
| AADSTS50180 | WindowsIntegratedAuthMissing — potrzebne jest zintegrowane uwierzytelnianie systemu Windows. Włącz dzierżawę dla bezproblemowego logowania jednokrotnego. |
| AADSTS50187 | DeviceInformationNotProvided — usługa nie może wykonać uwierzytelniania urządzenia. |
| AADSTS50196 | LoopDetected — wykryto pętlę klienta. Sprawdź logikę aplikacji, aby upewnić się, że buforowanie tokenów jest implementowane i że warunki błędu są obsługiwane poprawnie.  Aplikacja dokonała zbyt wiele tego samego żądania w zbyt krótkim okresie, wskazując, że jest w stanie błędnym lub jest obraźliwie żąda tokenów. |
| AADSTS50197 | KonfliktNieżdities — nie można odnaleźć użytkownika. Spróbuj zalogować się ponownie. |
| AADSTS50199 | CmsiInterrupt — ze względów bezpieczeństwa wymagane jest potwierdzenie użytkownika dla tego żądania.  Ponieważ jest to błąd "interaction_required", klient powinien wykonać interaktywną erę.  Dzieje się tak, ponieważ system webview został użyty do żądania tokenu dla aplikacji macierzystej — użytkownik musi zostać poproszony, aby zapytać, czy to rzeczywiście aplikacja, którą zamierzali się zalogować.|
| AADSTS51000 | RequiredFeatureNotEnabled — funkcja jest wyłączona. |
| AADSTS51001 | DomainHintMustbePresent — wskazówka domeny musi być obecny z lokalnym identyfikatorem zabezpieczeń lub lokalną siecią UPN. |
| AADSTS51004 | UserAccountNotInDirectory — konto użytkownika nie istnieje w katalogu. |
| AADSTS51005 | TemporaryRedirect — odpowiednik stanu HTTP 307, który wskazuje, że żądane informacje znajdują się w identyfikatorze URI określonym w nagłówku lokalizacji. Po otrzymaniu tego stanu postępuj zgodnie z nagłówkiem lokalizacji skojarzonym z odpowiedzią. Gdy oryginalna metoda żądania była POST, przekierowane żądanie będzie również używać METODY POST. |
| AADSTS51006 | ForceReauthDueToInsufficientAuth — potrzebne jest zintegrowane uwierzytelnianie systemu Windows. Użytkownik zalogowany przy użyciu tokenu sesji, w którego brakuje oświadczenia uwierzytelniania zintegrowanego systemu Windows. Poproś użytkownika o ponowne zalogowanie się. |
| AADSTS52004 | DelegationDoesNotExistForLinkedIn — użytkownik nie udzielił zgody na dostęp do zasobów LinkedIn. |
| AADSTS53000 | DeviceNotCompliant — zasady dostępu warunkowego wymaga zgodnego urządzenia, a urządzenie nie jest zgodne. Użytkownik musi zarejestrować swoje urządzenie u zatwierdzonego dostawcy mdm, takiego jak intune. |
| AADSTS53001 | DeviceNotDomainJoined — zasady dostępu warunkowego wymagają urządzenia przyłączonego do domeny, a urządzenie nie jest przyłączone do domeny. Niech użytkownik użyje urządzenia przyłączanego do domeny. |
| AADSTS53002 | ApplicationUsedIsNotAnApprovedApp — używana aplikacja nie jest zatwierdzoną aplikacją dla dostępu warunkowego. Użytkownik musi użyć jednej z aplikacji z listy zatwierdzonych aplikacji, aby uzyskać dostęp. |
| AADSTS53003 | BlockedByConditionalAccess - Dostęp został zablokowany przez zasady dostępu warunkowego. Zasady dostępu nie zezwalają na wystawienie tokenu. |
| AADSTS53004 | ProofUpBlockedDueToRisk — użytkownik musi zakończyć proces rejestracji uwierzytelniania wieloskładnikowego przed uzyskaniam dostępu do tej zawartości. Użytkownik powinien zarejestrować się w celu uwierzytelniania wieloskładnikowego. |
| AADSTS54000 | MinorUserBlockedLegalAgeGroupRule |
| AADSTS65001 | DelegationDoesNotExist — użytkownik lub administrator nie wyraził zgody na korzystanie z aplikacji o identyfikatorze X. Wyślij interaktywne żądanie autoryzacji dla tego użytkownika i zasobu. |
| AADSTS65004 | UserDeclinedConsent — użytkownik odmówił zgody na dostęp do aplikacji. Użytkownik musi ponowić próbę zalogowania się i wyrazić zgodę na aplikację|
| AADSTS65005 | MisconfiguredApplication — lista dostępu do zasobów wymagana przez aplikację nie zawiera aplikacji wykrywalnych przez zasób lub aplikacja kliencka zażądała dostępu do zasobu, który nie został określony na liście dostępu do wymaganych zasobów lub usługa Graph zwróciła złe żądanie lub nie znaleziono zasobu. Jeśli aplikacja obsługuje SAML, być może skonfigurowano aplikację z niewłaściwym identyfikatorem (encja). Wypróbuj rozdzielczość wymienioną dla SAML, korzystając z poniższego linku:[https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#no-resource-in-requiredresourceaccess-list](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav) |
| AADSTS67003 | AktorNotValidServiceIdentity |
| AADSTS70000 | InvalidGrant — uwierzytelnianie nie powiodło się. Token odświeżania jest nieprawidłowy. Błąd może być spowodowany następującymi przyczynami:<ul><li>Nagłówek powiązania tokenu jest pusty</li><li>Skrót powiązania tokenu nie jest zgodny</li></ul> |
| AADSTS70001 | UnauthorizedClient — aplikacja jest wyłączona. |
| AADSTS70002 | InvalidClient — błąd sprawdzania poprawności poświadczeń. Określony client_secret nie odpowiada oczekiwanej wartości dla tego klienta. Popraw client_secret i spróbuj ponownie. Aby uzyskać więcej informacji, zobacz [Żądanie tokenu dostępu za pomocą kodu autoryzacji.](v2-oauth2-auth-code-flow.md#request-an-access-token) |
| AADSTS70003 | UnsupportedGrantType — aplikacja zwróciła nieobsługiowany typ dotacji. |
| AADSTS70004 | InvalidRedirectUri — aplikacja zwróciła nieprawidłowy identyfikator URI przekierowania. Adres przekierowania określony przez klienta nie pasuje do żadnych skonfigurowanych adresów ani żadnych adresów na liście zatwierdzonych dostawców OIDC. |
| AADSTS70005 | UnsupportedResponseType — aplikacja zwróciła nieobsługicony typ odpowiedzi z następujących powodów:<ul><li>typ odpowiedzi "token" nie jest włączony dla aplikacji</li><li>typ odpowiedzi „id_token” wymaga zakresu OpenID — zawiera nieobsługiwaną wartość parametru OAuth w zakodowanym kanale wctx</li></ul> |
| AADSTS70007 | UnsupportedResponseMode — aplikacja zwróciła nieobsługiwałą wartość `response_mode` podczas żądania tokenu.  |
| AADSTS70008 | ExpiredOrRevokedGrant — token odświeżania wygasł z powodu braku aktywności. Token został wydany na XXX i był nieaktywny przez pewien czas. |
| AADSTS70011 | InvalidScope — zakres wymagany przez aplikację jest nieprawidłowy. |
| AADSTS70012 | MsaServerError — wystąpił błąd serwera podczas uwierzytelniania użytkownika MSA (konsumenta). Spróbuj ponownie. Jeśli nadal się nie powiedzie, [otwórz bilet pomocy technicznej](../fundamentals/active-directory-troubleshooting-support-howto.md) |
| AADSTS70016 | AuthorizationPending — błąd przepływu urządzenia OAuth 2.0. Autoryzacja jest w toku. Urządzenie ponowi próbę sondowania żądania. |
| AADSTS70018 | BadVerificationCode — nieprawidłowy kod weryfikacyjny z powodu wpisywania przez użytkownika nieprawidłowego kodu użytkownika dla przepływu kodu urządzenia. Autoryzacja nie jest zatwierdzona. |
| AADSTS70019 | CodeExpired — kod weryfikacyjny wygasł. Niech użytkownik ponowi próbę logowania. |
| AADSTS75001 | BindingSerializationError — wystąpił błąd podczas wiązania komunikatu SAML. |
| AADSTS75003 | UnsupportedBindingError — aplikacja zwróciła błąd związany z nieobsługiwym powiązaniem (odpowiedź protokołu SAML nie może być wysyłana za pośrednictwem powiązań innych niż HTTP POST). |
| AADSTS75005 | Saml2MessageInvalid — usługa Azure AD nie obsługuje żądania SAML wysyłanego przez aplikację dla aplikacji SSO. |
| AADSTS75008 | RequestDeniedError — żądanie z aplikacji zostało odrzucone, ponieważ żądanie SAML miało nieoczekiwane miejsce docelowe. |
| AADSTS75011 | NoMatchedAuthnContextInOutputClaims — metoda uwierzytelniania, za pomocą której użytkownik uwierzytelniony z usługą nie pasuje do żądanej metody uwierzytelniania. |
| AADSTS75016 | Saml2AuthenticationRequestInvalidNameIDPolicy — saml2 żądanie uwierzytelniania ma nieprawidłową nazwęPolicy. |
| AADSTS80001 | OnPremiseStoreIsNotAvailable — agent uwierzytelniania nie może połączyć się z usługą Active Directory. Upewnij się, że serwery agentów są członkami tego samego lasu usługi AD co użytkownicy, których hasła muszą zostać zweryfikowane i mogą połączyć się z usługą Active Directory. |
| AADSTS80002 | OnPremisePasswordValidatorRequestTimedout — przekroczona przekroczona przekroczona przekroczona limit czasu żądania sprawdzania poprawności hasła. Upewnij się, że usługa Active Directory jest dostępna i odpowiada na żądania agentów. |
| AADSTS80005 | OnPremisePasswordValidatorUnpredictableWebException — wystąpił nieznany błąd podczas przetwarzania odpowiedzi od agenta uwierzytelniania. Ponów próbę żądania. Jeśli nadal nie powiedzie się, [otwórz bilet pomocy technicznej,](../fundamentals/active-directory-troubleshooting-support-howto.md) aby uzyskać więcej szczegółów na temat błędu. |
| AADSTS80007 | OnPremisePasswordValidatorErrorOccurredOnPrem — agent uwierzytelniania nie może zweryfikować hasła użytkownika. Sprawdź dzienniki agenta, aby uzyskać więcej informacji i sprawdź, czy usługa Active Directory działa zgodnie z oczekiwaniami. |
| AADSTS80010 | OnPremisePasswordValidationEncryptionException — agent uwierzytelniania nie może odszyfrować hasła. |
| AADSTS80012 | OnPremisePasswordValidationAccountLogonInvalidHours — użytkownicy próbowali zalogować się poza dozwolonymi godzinami (jest to określone w ad). |
| AADSTS80013 | OnPremisePasswordValidationTimeSkew — nie można ukończyć próby uwierzytelnienia z powodu pochylenia czasu między komputerem z uruchomionym agentem uwierzytelniania a usługą AD. Rozwiązywanie problemów z synchronizacją czasu. |
| AADSTS81004 | DesktopSsoIdentityInTicketIsNotAuthenticated — próba uwierzytelniania Kerberos nie powiodła się. |
| AADSTS81005 | DesktopSsoAuthenticationPackageNotSupported — pakiet uwierzytelniania nie jest obsługiwany. |
| AADSTS81006 | DesktopSsoNoAuthorizationHeader — nie znaleziono nagłówka autoryzacji. |
| AADSTS81007 | DesktopSsoTenantIsNotOptIn — dzierżawa nie jest włączona dla bezproblemowego logowania jednokrotnego. |
| AADSTS81009 | DesktopSsoAuthorizationHeaderValueWithBadFormat — nie można zweryfikować biletu Kerberos użytkownika. |
| AADSTS81010 | DesktopSsoAuthTokenInvalid — bezproblemowe logowanie jednokrotne nie powiodło się, ponieważ bilet protokołu Kerberos użytkownika wygasł lub jest nieprawidłowy. |
| AADSTS81011 | DesktopSsoLookupUserBySidFailed — nie można znaleźć obiektu użytkownika na podstawie informacji w bilecie Kerberos użytkownika. |
| AADSTS81012 | DesktopSsoMismatchBetweenTokenUpnAndChosenUpn — użytkownik próbujący zalogować się do usługi Azure AD różni się od użytkownika zalogowany do urządzenia. |
| AADSTS90002 | InvalidTenantName — nazwa dzierżawy nie została znaleziona w magazynie danych. Sprawdź, czy masz poprawny identyfikator dzierżawy. |
| AADSTS90004 | InvalidRequestFormat — żądanie nie jest poprawnie sformatowane. |
| AADSTS90005 | InvalidRequestWithMultiple Wymagania — nie można ukończyć żądania. Żądanie jest nieprawidłowe, ponieważ nie można używać identyfikatora i wskazówki logowania razem.  |
| AADSTS90006 | ExternalServerRetryableError — usługa jest tymczasowo niedostępna.|
| AADSTS90007 | InvalidSessionId — złe żądanie. Nie można przeanalizować identyfikatora przekazanej sesji. |
| AADSTS90008 | TokenForItselfRequiresGraphPermission — użytkownik lub administrator nie wyraził zgody na korzystanie z aplikacji. Co najmniej aplikacja wymaga dostępu do usługi Azure AD, określając uprawnienia logowania i odczytu profilu użytkownika. |
| AADSTS90009 | TokenForItselfMissingIdenticalAppIdentifier — aplikacja żąda tokenu dla siebie. Ten scenariusz jest obsługiwany tylko wtedy, gdy zasób, który jest określony jest przy użyciu identyfikatora aplikacji opartej na identyfikatorze GUID. |
| AADSTS90010 | NotSupported — nie można utworzyć algorytmu. |
| AADSTS90012 | RequestTimeout — limit czasu żądany został przesunięty. |
| AADSTS90013 | InvalidUserInput — dane wejściowe od użytkownika jest nieprawidłowy. |
| AADSTS90014 | Brak WymaganePole — ten kod błędu może pojawić się w różnych przypadkach, gdy oczekiwane pole nie jest obecny w poświadczenia. |
| AADSTS90015 | QueryStringTooLong — ciąg zapytania jest za długi. |
| AADSTS90016 | Brak wymaganegoclaim — token dostępu jest nieprawidłowy. Brakuje wymaganego oświadczenia. |
| AADSTS90019 | MissingTenantRealm — usługa Azure AD nie może określić identyfikatora dzierżawy z żądania. |
| AADSTS90022 | AuthenticatedInvalidPrincipalNameFormat — format nazwy głównej jest nieprawidłowy lub `name[/host][@realm]` nie spełnia oczekiwanego formatu. Nazwa główna jest wymagana, host i królestwo są opcjonalne i mogą być ustawione na null. |
| AADSTS90023 | InvalidRequest — żądanie usługi uwierzytelniania jest nieprawidłowe. |
| AADSTS9002313 | InvalidRequest — żądanie jest nieprawidłowo sformułowane lub nieprawidłowe. - Problem polega na tym, że coś było nie tak z żądaniem do określonego punktu końcowego. Sugestia tego problemu jest, aby uzyskać ślad skrzypek występujących błędów i patrząc, czy żądanie jest rzeczywiście poprawnie sformatowany, czy nie. |
| AADSTS90024 | RequestBudgetExceedError — wystąpił błąd przejściowy. Spróbuj ponownie. |
| AADSTS90033 | MsodsServiceNiedostępne — usługa katalogowa online firmy Microsoft (MSODS) jest niedostępna. |
| AADSTS90036 | MsodsServiceUnretryableFailure — wystąpił nieoczekiwany błąd, którego nie można ponowić próby z usługi WCF hostowanego przez usługę MSODS. [Otwórz bilet pomocy technicznej](../fundamentals/active-directory-troubleshooting-support-howto.md), aby uzyskać więcej informacji na temat błędu. |
| AADSTS90038 | NationalCloudTenantRedirection — określony dzierżawca "Y" należy do Chmury Krajowej "X". Bieżące wystąpienie chmury "Z" nie jest powiększone z X. Zwracany jest błąd przekierowania w chmurze. |
| AADSTS90043 | NationalCloudAuthCodeRedirection — funkcja jest wyłączona. |
| AADSTS90051 | InvalidNationalCloudId — identyfikator chmury krajowej zawiera nieprawidłowy identyfikator chmury. |
| AADSTS90055 | TenantThrottlingError — istnieje zbyt wiele przychodzących żądań. Ten wyjątek jest zgłaszany dla zablokowanych dzierżawców. |
| AADSTS90056 | BadResourceRequest — aby zrealizować kod tokenu dostępu, aplikacja powinna `/token` wysłać żądanie POST do punktu końcowego. Ponadto przed tym należy podać kod autoryzacji i wysłać go `/token` w żądaniu POST do punktu końcowego. Zapoznaj się z tym artykułem, aby zapoznać się z [https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code)omówieniem przepływu kodu autoryzacyjnego OAuth 2.0: . Przekieruj `/authorize` użytkownika do punktu końcowego, który zwróci authorization_code. Publikując żądanie w `/token` punkcie końcowym, użytkownik pobiera token dostępu. Zaloguj się w witrynie Azure portal i sprawdź **rejestracje aplikacji > punktami końcowymi,** aby potwierdzić, że dwa punkty końcowe zostały poprawnie skonfigurowane. |
| AADSTS90072 | PassThroughUserMfaError — konto zewnętrzne, które użytkownik loguje się za pomocą nie istnieje w dzierżawie, do których się zalogowali; więc użytkownik nie może spełnić wymagania usługi MFA dla dzierżawy. Konto musi zostać dodane jako użytkownik zewnętrzny w dzierżawie pierwszy. Wyloguj się i zaloguj się przy pomocą innego konta użytkownika usługi Azure AD. |
| AADSTS90081 | OrgIdWsFederationMessageInvalid — wystąpił błąd, gdy usługa próbowała przetworzyć komunikat federacji WS. Wiadomość jest nieprawidłowa. |
| AADSTS90082 | OrgIdWsFederationNotSupported — wybrane zasady uwierzytelniania dla żądania nie są obecnie obsługiwane. |
| AADSTS90084 | OrgIdWsFederationGuestNotAllowed — konta gościa nie są dozwolone dla tej witryny. |
| AADSTS90085 | OrgIdWsFederationSltRedemptionFailed — usługa nie może wydać tokenu, ponieważ obiekt firmy nie został jeszcze zainicjowany. |
| AADSTS90086 | OrgIdWsTrustDaTokenExpired — token DA użytkownika wygasł. |
| AADSTS90087 | OrgIdWsFederationMessageCreationFromUriFailed — wystąpił błąd podczas tworzenia komunikatu Federacji WS z identyfikatora URI. |
| AADSTS90090 | GraphRetryableError — usługa jest tymczasowo niedostępna. |
| AADSTS90091 | GraphServiceNiereachowalne |
| AADSTS90092 | GraphNonRetryableError |
| AADSTS90093 | GraphUserUnauthorized — wykres zwrócił z zabronionym kodem błędu dla żądania. |
| AADSTS90094 | Wymagana zgoda administratora — wymagana jest zgoda administratora. |
| AADSTS900382 | Klient poufny nie jest obsługiwany w żądaniu Cross Cloud. |
| AADSTS90100 | InvalidRequestParameter - Parametr jest pusty lub nieprawidłowy. |
| AADSTS901002 | AADSTS901002: Parametr żądania "zasób" nie jest obsługiwany. |
| AADSTS90101 | InvalidEmailAddress — podane dane nie są prawidłowym adresem e-mail. Adres e-mail musi `someone@example.com`być w formacie . |
| AADSTS90102 | InvalidUriParameter - Wartość musi być prawidłowym bezwzględnym identyfikatorem URI. |
| AADSTS90107 | InvalidXml — żądanie jest nieprawidłowe. Upewnij się, że dane nie mają nieprawidłowych znaków.|
| AADSTS90114 | InvalidExpiryDate — sygnatura czasowa wygaśnięcia tokenu zbiorczego spowoduje wystawienie wygasłego tokenu. |
| AADSTS90117 | InvalidRequestInput |
| AADSTS90119 | InvalidUserCode — kod użytkownika jest zerowy lub pusty.|
| AADSTS90120 | InvalidDeviceFlowRequest — żądanie zostało już autoryzowane lub odrzucone. |
| AADSTS90121 | InvalidEmptyRequest — nieprawidłowe puste żądanie.|
| AADSTS90123 | IdentityProviderAccessDenied — token nie może zostać wystawiony, ponieważ dostawca wystawiania tożsamości lub oświadczeń odmówił żądania. |
| AADSTS90124 | V1ResourceV2GlobalEndpointNotSupported — zasób nie `/common` jest `/consumers` obsługiwany przez lub punktów końcowych. Zamiast `/organizations` tego należy użyć punktu końcowego specyficznego dla dzierżawy. |
| AADSTS90125 | DebugModeEnrollTenantNotFound — użytkownik nie jest w systemie. Upewnij się, że nazwa użytkownika została wprowadzona poprawnie. |
| AADSTS90126 | DebugModeEnrollTenantNotInferred — typ użytkownika nie jest obsługiwany w tym punkcie końcowym. System nie może wywnioskować dzierżawy użytkownika z nazwy użytkownika. |
| AADSTS90130 | NonConvergedAppV2GlobalEndpointNotSupported — aplikacja nie jest `/common` obsługiwana przez lub `/consumers` punktów końcowych. Zamiast `/organizations` tego należy użyć punktu końcowego specyficznego dla dzierżawy. |
| AADSTS120000 | HasłoChangeIncorrectCurrentPassword |
| AADSTS120002 | PasswordChangeInvalidNewPasswordWeak |
| AADSTS120003 | PasswordChangeInvalidNewPasswordContainsMemberName |
| AADSTS120004 | PasswordChangeOnPremComplexity |
| AADSTS120005 | PasswordChangeOnPremSuccessCloudFail |
| AADSTS120008 | PasswordChangeAsyncJobStateTerminated — wystąpił błąd, którego nie można ponowić.|
| AADSTS120011 | Hasło ZmianaAsyncUpnInferenceFailed |
| AADSTS120012 | HasłoChangeNeedsToHappenOnPrem |
| AADSTS120013 | PasswordChangeOnPremisesConnectivityFailure |
| AADSTS120014 | PasswordChangeOnPremUserAccountLockedOutOrDisabled |
| AADSTS120015 | Wymagane hasłoZmienianie |
| AADSTS120016 | HasłoZnajdySznikUsznikNieszowy |
| AADSTS120018 | PasswordChangePasswordDoesnotComplyFuzzyPolicy |
| AADSTS120020 | PasswordChangeFailure |
| AADSTS120021 | Program PartnerServiceSsprInternalServiceError |
| AADSTS130004 | NgcKeyNotFound — podmiotu zabezpieczeń użytkownika nie ma skonfigurowany klucz NGC ID. |
| AADSTS130005 | NgcInvalidSignature - Zweryfikowany podpis klucza NGC nie powiódł się.|
| AADSTS130006 | NgcTransportKeyNotFound — klucz transportu NGC nie jest skonfigurowany na urządzeniu. |
| AADSTS130007 | NgcDeviceIsDisabled — urządzenie jest wyłączone. |
| AADSTS130008 | NgcDeviceIsNotFound — nie znaleziono urządzenia, do którego odwołuje się klucz NGC. |
| AADSTS135010 | KeyNotFound (Nieukryty klucz) |
| AADSTS140000 | InvalidRequestNonce — nie podano identyfikatora żądania. |
| AADSTS140001 | InvalidSessionKey — klucz sesji jest nieprawidłowy.|
| AADSTS165900 | InvalidApiRequest — nieprawidłowe żądanie. |
| AADSTS220450 | NieobsługiwałaAndroidWebViewVersion - wersja Chrome WebView nie jest obsługiwana. |
| AADSTS220501 | InvalidCrlDownload (Nieprawidłowy wiersz) |
| AADSTS221000 | DeviceOnlyTokensNotSupportedByResource — zasób nie jest skonfigurowany do akceptowania tokenów tylko na urządzeniu. |
| AADSTS240001 | BulkAADJTokenUnauthorized — użytkownik nie jest autoryzowany do rejestrowania urządzeń w usłudze Azure AD. |
| AADSTS240002 | WymaganeClaimIsMissing — id_token nie może być używany `urn:ietf:params:oauth:grant-type:jwt-bearer` jako dotacja.|
| AADSTS530032 | BlockedByConditionalAccessOnSecurityPolicy — administrator dzierżawy skonfigurował zasadę zabezpieczeń, która blokuje to żądanie. Sprawdź zasady zabezpieczeń, które są zdefiniowane na poziomie dzierżawy, aby ustalić, czy żądanie spełnia wymagania zasad. |
| AADSTS700016 | UnauthorizedClient_DoesNotMatchRequest — aplikacja nie została znaleziona w katalogu/dzierżawcy. Może się tak zdarzyć, jeśli aplikacja nie została zainstalowana przez administratora dzierżawy lub na co wyraziła zgodę dowolnego użytkownika w dzierżawie. Być może została błędnie skonfigurowana wartość identyfikatora dla aplikacji lub wysłano żądanie uwierzytelnienia do niewłaściwej dzierżawy. |
| AADSTS700020 | Wymagane interakcje — dotacja dostępu wymaga interakcji. |
| AADSTS700022 | InvalidMultipleResourcesScope — podana wartość dla zakresu parametru wejściowego nie jest prawidłowa, ponieważ zawiera więcej niż jeden zasób. |
| AADSTS700023 | InvalidResourcelessScope — podana wartość dla zakresu parametrów wejściowych nie jest prawidłowa, gdy żąda token dostępu. |
| AADSTS7000222| InvalidClientSecretExpiredKeysProvided — dostarczone klucze tajne klienta wygasły. Odwiedź witrynę Azure Portal, aby utworzyć nowe klucze dla aplikacji lub rozważ użycie poświadczeń certyfikatu w celu zwiększenia zabezpieczeń:https://aka.ms/certCreds |
| AADSTS700005 | InvalidGrantRedeemAgainstWrongTenant — pod warunkiem, że kod autoryzacji jest przeznaczony do użycia w stosunku do innych dzierżawców, w ten sposób odrzucone. Kod autoryzacji OAuth2 musi zostać zrealizowany względem tej samej dzierżawy, dla jakiej został nabyty (/common lub /{tenant-ID}, stosownie do przypadku) |
| AADSTS1000000 | UserNotBoundError — interfejs API powiązania wymaga użytkownika usługi Azure AD również uwierzytelnić za pomocą zewnętrznego IDP, który nie został jeszcze zdarzyło. |
| AADSTS1000002 | BindCompleteInterruptError — powiązanie zostało zakończone pomyślnie, ale użytkownik musi zostać poinformowany. |
| AADSTS7000112 | UnauthorizedClientApplicationDisabled — aplikacja jest wyłączona. |
| AADSTS7500529 | Wartość "SAMLId-Guid" nie jest prawidłowym identyfikatorem SAML — usługa Azure AD używa tego atrybutu do wypełniania atrybutu InResponseTo zwracanej odpowiedzi. Identyfikator nie może zaczynać się od liczby, więc wspólną strategią jest poprzedzanie ciągu, takiego jak "id" do reprezentacji ciągu identyfikatora GUID. Na przykład id6c1c178c166d486687be4aaf5e482730 jest prawidłowym identyfikatorem. |

## <a name="next-steps"></a>Następne kroki

* Masz pytanie lub nie możesz znaleźć tego, czego szukasz? Utwórz problem z githubem lub zobacz [Opcje pomocy technicznej i pomocy dla deweloperów,](active-directory-develop-help-support.md) aby dowiedzieć się więcej o innych sposobach uzyskania pomocy i pomocy technicznej.
