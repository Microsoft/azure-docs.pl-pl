---
title: TechnicalProfiles
titleSuffix: Azure AD B2C
description: Określ element TechnicalProfiles zasad niestandardowych w Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/04/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a70b8be6c034f002f0e387b5d3b4ba5deafa1a5e
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102120508"
---
# <a name="technicalprofiles"></a>TechnicalProfiles

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Profil techniczny zapewnia platformę z wbudowanym mechanizmem do komunikowania się z różnymi rodzajami stron. Profile techniczne są używane do komunikowania się z dzierżawcą Azure AD B2C, tworzenia użytkownika lub odczytywania profilu użytkownika. Profil techniczny może być samodzielnie potwierdzony w celu umożliwienia interakcji z użytkownikiem. Na przykład Zbierz poświadczenia użytkownika w celu zalogowania się, a następnie wyrenderowania strony rejestracji lub resetowania hasła.

## <a name="type-of-technical-profiles"></a>Typ profilów technicznych

Profil techniczny umożliwia realizację następujących typów scenariuszy:

- [Application Insights](analytics-with-application-insights.md) — wysyłanie danych zdarzenia do [Application Insights](../azure-monitor/app/app-insights-overview.md).
- [Azure Active Directory](active-directory-technical-profile.md) — zapewnia obsługę Azure Active Directory B2C zarządzania użytkownikami.
- [Usługa Azure ad Multi-Factor Authentication](multi-factor-auth-technical-profile.md) — zapewnia obsługę weryfikowania numeru telefonu przy użyciu usługi azure AD Multi-Factor Authentication (MFA). 
- [Przekształcanie oświadczeń](claims-transformation-technical-profile.md) — Wywołaj przekształcenia oświadczeń wyjściowych na potrzeby manipulowania wartościami oświadczeń, Weryfikuj oświadczenia lub ustaw wartości domyślne dla zestawu oświadczeń wyjściowych.
- [Wskazówka tokenu identyfikatora](id-token-hint.md) — sprawdza poprawność `id_token_hint` podpisu tokenu JWT, nazwy wystawcy oraz odbiorców tokenu i wyodrębnia z tokenu przychodzącego.
- [Wystawca tokenu JWT](jwt-issuer-technical-profile.md) — emituje token JWT, który jest zwracany z powrotem do aplikacji jednostki uzależnionej.
- [OAuth1](oauth1-technical-profile.md) -Federacja z dowolnym dostawcą tożsamości protokołu OAuth 1,0.
- [OAuth2](oauth2-technical-profile.md) -Federacja z dowolnym dostawcą tożsamości protokołu OAuth 2,0.
- [Hasło](one-time-password-technical-profile.md) jednorazowe — zapewnia obsługę zarządzania generowaniem i weryfikacją hasła jednorazowego.
- [OpenID Connect Connect](openid-connect-technical-profile.md) -Federation z dowolnym dostawcą tożsamości protokołu OpenID Connect Connect.
- [Współczynnik telefonu](phone-factor-technical-profile.md) — Obsługa rejestrowania i weryfikowania numerów telefonów.
- [Dostawca RESTful](restful-technical-profile.md) — wywoływanie usług interfejsu API REST, takich jak sprawdzanie danych wejściowych użytkownika, Wzbogacanie danych użytkownika lub Integrowanie z aplikacjami biznesowymi.
- [Dostawca tożsamości SAML](identity-provider-generic-saml.md) — Federacja z dowolnym dostawcą tożsamości protokołu SAML.
- [Wystawca tokenów SAML](saml-service-provider.md) — emituje token SAML zwracanego z powrotem do aplikacji jednostki uzależnionej.
- [Samodzielne](self-asserted-technical-profile.md) współpracujące z użytkownikiem. Na przykład Zbierz poświadczenia użytkownika w celu zalogowania się, renderowania strony rejestracji lub resetowania hasła.
- [Zarządzanie sesjami](custom-policy-reference-sso.md) — obsługa różnych typów sesji.

## <a name="technical-profile-flow"></a>Przepływ profilu technicznego

Wszystkie typy profilów technicznych mają takie same koncepcje. Zacznij od odczytywania oświadczeń wejściowych, uruchamiaj transformację oświadczeń. Następnie należy skontaktować się ze skonfigurowaną stroną, taką jak dostawca tożsamości, interfejs API REST lub usługi katalogowe Azure AD. Po zakończeniu procesu profil techniczny zwróci oświadczenia wyjściowe i może uruchomić transformację oświadczeń wyjściowych. Na poniższym diagramie pokazano, w jaki sposób przekształcenia i mapowania, do których odwołuje się profil techniczny, są przetwarzane. Po wykonaniu transformacji oświadczeń, oświadczenia wyjściowe są natychmiast przechowywane w zbiorze oświadczeń. Bez względu na to, że profil techniczny współdziała z.

![Diagram ilustrujący przepływ profilu technicznego](./media/technical-profiles/technical-profile-flow.png)

1. **Zarządzanie sesjami logowania** jednokrotnego — przywraca stan sesji profilu technicznego przy użyciu funkcji [zarządzania sesjami logowania jednokrotnego](custom-policy-reference-sso.md).
1. **Przekształcanie oświadczeń wejściowych** — przed rozpoczęciem pracy z profilem technicznym Azure AD B2C uruchamia [transformację oświadczeń](claimstransformations.md)wejściowych.
1. **Oświadczenia wejściowe** — oświadczenia są pobierane z zbioru oświadczeń, który jest używany w profilu technicznym.
1. **Wykonywanie profilu technicznego** — profil techniczny wymienia oświadczenia ze skonfigurowaną stroną. Na przykład:
    - Przekieruj użytkownika do dostawcy tożsamości, aby zakończyć logowanie. Po pomyślnym zalogowaniu użytkownik wraca do tyłu, a wykonywanie profilu technicznego będzie kontynuowane.
    - Wywołaj interfejs API REST podczas wysyłania parametrów jako InputClaims i pobierania informacji z powrotem jako OutputClaims.
    - Utwórz lub zaktualizuj konto użytkownika.
    - Wysyła i weryfikuje wiadomość tekstową usługi MFA.
1. **Sprawdzanie poprawności profilów technicznych** — [samodzielnie potwierdzony profil techniczny](self-asserted-technical-profile.md) może wywoływać [Sprawdzanie poprawności profilów technicznych](validation-technical-profile.md) w celu weryfikacji danych profilowanych przez użytkownika.
1. **Oświadczenia wyjściowe** — oświadczenia są zwracane z powrotem do zbioru oświadczeń. Możesz użyć tych oświadczeń w następnych krokach aranżacji lub przekształceń oświadczeń wyjściowych.
1. **Przekształcenia oświadczeń wyjściowych** — po ukończeniu profilu technicznego Azure AD B2C uruchamia [transformację oświadczeń](claimstransformations.md)wyjściowych. 
1. **Zarządzanie sesjami logowania jednokrotnego (SSO)** — utrzymuje dane profilu technicznego w sesji przy użyciu funkcji [zarządzania sesjami logowania jednokrotnego](custom-policy-reference-sso.md).

Element **TechnicalProfiles** zawiera zestaw profilów technicznych obsługiwanych przez dostawcę usług. Każdy dostawca oświadczeń musi mieć co najmniej jeden profil techniczny. Profil techniczny określa punkty końcowe i protokoły, które są konieczne do komunikowania się z dostawcą oświadczeń. Dostawca oświadczeń może mieć wiele profilów technicznych.

```xml
<ClaimsProvider>
  <DisplayName>Display name</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Technical profile identifier">
      <DisplayName>Display name of technical profile</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        ...
      </Metadata>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

Element **profilu technicznym** zawiera następujący atrybut:

| Atrybut | Wymagane | Opis |
|---------|---------|---------|
| Id | Tak | Unikatowy identyfikator profilu technicznego. Profil techniczny może być przywoływany przy użyciu tego identyfikatora z innych elementów w pliku zasad. Na przykład **OrchestrationSteps** i **ValidationTechnicalProfile**. |

**Profilu technicznym** zawiera następujące elementy:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| Domena | 0:1 | Nazwa domeny profilu technicznego. Na przykład jeśli Twój profil techniczny określa dostawcę tożsamości w serwisie Facebook, nazwa domeny to Facebook.com. |
| Nazwa wyświetlana | 1:1 | Nazwa wyświetlana profilu technicznego. |
| Opis | 0:1 | Opis profilu technicznego. |
| Protokół | 1:1 | Protokół używany do komunikacji z drugą stroną. |
| Metadane | 0:1 | Kolekcja klucz/wartość, która kontroluje zachowanie profilu technicznego. |
| InputTokenFormat | 0:1 | Format tokenu wejściowego. Możliwe wartości: `JSON` , `JWT` , `SAML11` lub `SAML2` . `JWT`Wartość reprezentuje token sieci Web JSON zgodnie ze specyfikacją IETF. `SAML11`Wartość reprezentuje token zabezpieczający protokołu SAML 1,1 zgodnie ze specyfikacją języka Oasis.  `SAML2`Wartość reprezentuje token zabezpieczający protokołu SAML 2,0 zgodnie ze specyfikacją języka Oasis. |
| OutputTokenFormat | 0:1 | Format tokenu wyjściowego. Możliwe wartości: `JSON` , `JWT` , `SAML11` lub `SAML2` . |
| CryptographicKeys | 0:1 | Lista kluczy kryptograficznych, które są używane w profilu technicznym. |
| InputClaimsTransformations | 0:1 | Lista wcześniej zdefiniowanych odwołań do transformacji oświadczeń, które należy wykonać przed wysłaniem jakichkolwiek oświadczeń do dostawcy oświadczeń lub jednostki uzależnionej. |
| InputClaims | 0:1 | Lista wcześniej zdefiniowanych odwołań do typów zgłoszeń, które są pobierane jako dane wejściowe w profilu technicznym. |
| PersistedClaims | 0:1 | Lista wcześniej zdefiniowanych odwołań do typów zgłoszeń, które będą utrwalane przez profil techniczny. |
| DisplayClaims | 0:1 | Lista wcześniej zdefiniowanych odwołań do typów zgłoszeń, które są prezentowane przez [własny profil techniczny](self-asserted-technical-profile.md). Funkcja DisplayClaims jest obecnie w **wersji zapoznawczej**. |
| OutputClaims | 0:1 | Lista wcześniej zdefiniowanych odwołań do typów zgłoszeń, które są pobierane jako dane wyjściowe w profilu technicznym. |
| OutputClaimsTransformations | 0:1 | Lista wcześniej zdefiniowanych odwołań do transformacji oświadczeń, które należy wykonać po odebraniu oświadczeń od dostawcy oświadczeń. |
| ValidationTechnicalProfiles | 0: n | Lista odwołań do innych profilów technicznych używanych przez profil techniczny do celów weryfikacji. Aby uzyskać więcej informacji, zobacz temat [Sprawdzanie poprawności profilu technicznego](validation-technical-profile.md)|
| SubjectNamingInfo | 0:1 | Kontroluje produkcję nazwy podmiotu w tokenach, w których nazwa podmiotu jest określona oddzielnie od oświadczeń. Na przykład uwierzytelnianie OAuth lub SAML.  |
| IncludeInSso | 0:1 |  Określa, czy użycie tego profilu technicznego ma mieć zastosowanie do działania logowania jednokrotnego (SSO) dla sesji lub zamiast tego wymagać jawnej interakcji. Ten element jest prawidłowy tylko w profilach SelfAsserted używanych w profilu technicznym weryfikacji. Możliwe wartości: `true` (ustawienie domyślne) lub `false` . |
| IncludeClaimsFromTechnicalProfile | 0:1 | Identyfikator profilu technicznego, z którego mają zostać dodane wszystkie oświadczenia wejściowe i wyjściowe do tego profilu technicznego. Profil techniczny, do którego istnieje odwołanie, musi być zdefiniowany w tym samym pliku zasad. |
| IncludeTechnicalProfile |0:1 | Identyfikator profilu technicznego, z którego mają zostać dodane wszystkie dane do tego profilu technicznego. |
| UseTechnicalProfileForSessionManagement | 0:1 | Inny profil techniczny, który ma być używany na potrzeby zarządzania sesją. |
|EnabledForUserJourneys| 0:1 |Kontroluje, czy profil techniczny jest wykonywany w podróży użytkownika.  |

## <a name="protocol"></a>Protokół

**Protokół** określa protokół, który ma być używany do komunikacji z drugą stroną. Element **Protocol** zawiera następujące atrybuty:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Nazwa | Tak | Nazwa prawidłowego protokołu obsługiwanego przez Azure AD B2C, który jest używany jako część profilu technicznego. Możliwe wartości: `OAuth1` , `OAuth2` ,,, `SAML2` `OpenIdConnect` `Proprietary` lub `None` . |
| Program obsługi | Nie | Jeśli nazwa protokołu jest ustawiona na `Proprietary` , określ nazwę zestawu, który jest używany przez Azure AD B2C, aby określić procedurę obsługi protokołu. |

## <a name="metadata"></a>Metadane

Element **Metadata** zawiera odpowiednie opcje konfiguracji w określonym protokole. Lista obsługiwanych metadanych jest udokumentowana w odpowiedniej specyfikacji [profilu technicznego](#type-of-technical-profiles) . Element **Metadata** zawiera następujący element:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| Element | 0: n | Metadane odnoszące się do profilu technicznego. Każdy typ profilu technicznego ma inny zestaw elementów metadanych. Aby uzyskać więcej informacji, zobacz sekcję typy profilów technicznych.  |

### <a name="item"></a>Element

Element **Item** elementu **Metadata** zawiera następujący atrybut:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Klucz | Tak | Klucz metadanych. Zobacz każdy [Typ profilu technicznego](#type-of-technical-profiles), aby wyświetlić listę elementów metadanych. |

Poniższy przykład ilustruje użycie metadanych związanych z [profilem technicznym OAuth2](oauth2-technical-profile.md#metadata).

```xml
<TechnicalProfile Id="Facebook-OAUTH">
  ...
  <Metadata>
    <Item Key="ProviderName">facebook</Item>
    <Item Key="authorization_endpoint">https://www.facebook.com/dialog/oauth</Item>
    <Item Key="AccessTokenEndpoint">https://graph.facebook.com/oauth/access_token</Item>
    <Item Key="HttpBinding">GET</Item>
    <Item Key="UsePolicyInRedirectUri">0</Item>
    ...
  </Metadata>
  ...
</TechnicalProfile>
```

Poniższy przykład ilustruje użycie metadanych związanych z [profilem technicznym interfejsu API REST](restful-technical-profile.md#metadata).

```xml
<TechnicalProfile Id="REST-Validate-Email">
  ...
  <Metadata>
    <Item Key="ServiceUrl">https://api.sendgrid.com/v3/mail/send</Item>
    <Item Key="AuthenticationType">Bearer</Item>
    <Item Key="SendClaimsIn">Body</Item>
    ...
  </Metadata>
  ...
</TechnicalProfile>
```

## <a name="cryptographic-keys"></a>Klucze kryptograficzne

Aby ustanowić relację zaufania z usługami, które integrują się z usługą, Azure AD B2C przechowuje wpisy tajne i certyfikaty w postaci [kluczy zasad](policy-keys-overview.md). Podczas wykonywania przez profil techniczny Azure AD B2C pobiera klucze kryptograficzne z kluczy zasad Azure AD B2C. Następnie używa kluczy ustanawiania zaufania, szyfrowania lub podpisywania tokenu. Te relacje zaufania składają się z:

- Federacja z dostawcami tożsamości [OAuth1](oauth1-technical-profile.md#cryptographic-keys), [OAuth2](oauth2-technical-profile.md#cryptographic-keys)i [SAML](identity-provider-generic-saml.md)
- Zabezpieczanie połączenia za pomocą [usług interfejsu API REST](secure-rest-api.md)
- Podpisywanie i szyfrowanie tokenów [JWT](jwt-issuer-technical-profile.md#cryptographic-keys) i [SAML](saml-service-provider.md)

Element **CryptographicKeys** zawiera następujący element:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| Klucz | 1: n | Klucz kryptograficzny używany w tym profilu technicznym. |

### <a name="key"></a>Klucz

Element **Key** zawiera następujący atrybut:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Id | Nie | Unikatowy identyfikator konkretnej pary kluczy, do której odwołuje się inne elementy w pliku zasad. |
| Identyfikatorze storagereferenceid | Tak | Identyfikator kontenera klucza magazynu, do którego odwołuje się inne elementy w pliku zasad. |

## <a name="input-claims-transformations"></a>Przekształcenia oświadczeń wejściowych

Element **InputClaimsTransformations** może zawierać kolekcję elementów transformacji oświadczeń wejściowych, które są używane do modyfikowania oświadczeń wejściowych lub generują nowe. 

Dane wyjściowe poprzedniej transformacji oświadczeń w kolekcji transformacji oświadczeń mogą być oświadczeniami wejściowymi kolejnych transformacji oświadczeń wejściowych, co pozwala na zastosowanie sekwencji transformacji oświadczeń w zależności od siebie.

Element **InputClaimsTransformations** zawiera następujący element:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| InputClaimsTransformation | 1: n | Identyfikator transformacji oświadczeń, która powinna zostać wykonana przed wysłaniem jakichkolwiek oświadczeń do dostawcy oświadczeń lub jednostki uzależnionej. Transformacja oświadczeń może służyć do modyfikowania istniejących oświadczeń ClaimsSchema lub generowania nowych. |

### <a name="inputclaimstransformation"></a>InputClaimsTransformation

Element **InputClaimsTransformation** zawiera następujący atrybut:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Identyfikator odwołania | Tak | Identyfikator transformacji oświadczeń zdefiniowany już w pliku zasad lub nadrzędnym pliku zasad. |

Następujące profile techniczne odnoszą się do transformacji oświadczeń **CreateOtherMailsFromEmail** . Przekształcenie oświadczeń dodaje wartość `email` oświadczenia do `otherMails` kolekcji, przed utrwaleniem danych w katalogu.

```xml
<TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
  ...
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="CreateOtherMailsFromEmail" />
  </InputClaimsTransformations>
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="alternativeSecurityId" />
    <PersistedClaim ClaimTypeReferenceId="userPrincipalName" />
    <PersistedClaim ClaimTypeReferenceId="mailNickName" DefaultValue="unknown" />
    <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />
    <PersistedClaim ClaimTypeReferenceId="otherMails" />
    <PersistedClaim ClaimTypeReferenceId="givenName" />
    <PersistedClaim ClaimTypeReferenceId="surname" />
  </PersistedClaims>
  ...
</TechnicalProfile>
```

## <a name="input-claims"></a>Oświadczenia wejściowe

**InputClaims** pobiera oświadczenia z zbioru oświadczeń i są używane w profilu technicznym. Na przykład [samodzielnie potwierdzony profil techniczny](self-asserted-technical-profile.md) używa oświadczeń wejściowych do wstępnego wypełniania oświadczeń wyjściowych dostarczanych przez użytkownika. Profil techniczny interfejsu API REST używa oświadczeń wejściowych do wysyłania parametrów wejściowych do punktu końcowego interfejsu API REST. Azure Active Directory używa jako unikatowego identyfikatora do odczytywania, aktualizowania lub usuwania konta.

Element **InputClaims** zawiera następujący element:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| Oświadczenie inputclaim | 1: n | Oczekiwany typ zgłoszenia wejściowego. |

### <a name="inputclaim"></a>Oświadczenie inputclaim

Element **oświadczenie inputclaim** zawiera następujące atrybuty:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Tak | Identyfikator typu żądania. Oświadczenie jest już zdefiniowane w sekcji schematu oświadczeń w pliku zasad lub nadrzędnym pliku zasad. |
| DefaultValue | Nie | Wartość domyślna, która ma zostać użyta do utworzenia żądania, jeśli nie istnieje w nim zastrzeżenie wskazywane przez ClaimTypeReferenceId, aby uzyskane wystąpienie może być używane jako oświadczenie inputclaim przez profil techniczny. |
| PartnerClaimType | Nie | Identyfikator typu dla partnera zewnętrznego, na który jest mapowany określony typ roszczeń zasad. Jeśli atrybut PartnerClaimType nie zostanie określony, określony typ roszczeń zasad jest mapowany na typ wiązania partnera o tej samej nazwie. Użyj tej właściwości, gdy nazwa typu usługi jest inna od drugiej strony. Na przykład nazwa pierwszego wystąpienia to "imięname", podczas gdy Partner używa roszczeń o nazwie "first_name". |

## <a name="display-claims"></a>Wyświetl oświadczenia

Element **DisplayClaims** zawiera listę oświadczeń, które mają być wyświetlane na ekranie w celu zbierania danych od użytkownika. W kolekcji Wyświetl oświadczenia można dołączyć odwołanie do [typu oświadczenia](claimsschema.md) [lub do utworzonego](display-controls.md) ekranu. 

- Typ zgłoszenia to odwołanie do żądania, które ma być wyświetlane na ekranie. 
  - Aby wymusić użytkownikowi podanie wartości dla określonego żądania, ustaw **wymagany** atrybut elementu **DisplayClaim** na `true` .
  - Aby wstępnie wypełnić wartości oświadczeń ekranu, użyj oświadczeń wejściowych, które zostały wcześniej opisane. Element może również zawierać wartość domyślną.
  - Element **ClaimType** w kolekcji **DisplayClaims** musi określać element **UserInputType** dla dowolnego typu danych wejściowych użytkownika obsługiwanego przez Azure AD B2C. Na przykład: `TextBox` lub `DropdownSingleSelect`.
- Kontrolka wyświetlania to element interfejsu użytkownika, który ma specjalne funkcje i współdziała z Azure AD B2C usługą zaplecza. Umożliwia użytkownikowi wykonywanie akcji na stronie, które wywołują profil techniczny weryfikacji na zapleczu. Na przykład weryfikacja adresu e-mail, numeru telefonu lub numeru lojalnościowego klienta.

Kolejność elementów w **DisplayClaims** określa kolejność, w której Azure AD B2C renderuje oświadczenia na ekranie. 

Element **DisplayClaims** zawiera następujący element:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| DisplayClaim | 1: n | Oczekiwany typ zgłoszenia wejściowego. |

### <a name="displayclaim"></a>DisplayClaim

Element **DisplayClaim** zawiera następujące atrybuty:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Nie | Identyfikator typu "Claim" jest już zdefiniowany w sekcji ClaimsSchema w pliku zasad lub nadrzędnym pliku zasad. |
| DisplayControlReferenceId | Nie | Identyfikator [kontrolki wyświetlania](display-controls.md) zdefiniowany już w sekcji ClaimsSchema w pliku zasad lub nadrzędnym pliku zasad. |
| Wymagane | Nie | Wskazuje, czy jest wymagane żądanie wyświetlania. |

Poniższy przykład ilustruje sposób użycia oświadczeń wyświetlania i wyświetlania kontrolek w profilu technicznym z własnym potwierdzeniem.

![Profil techniczny z własnym potwierdzeń z wyświetlanymi oświadczeniami](./media/technical-profiles/display-claims.png)

W następującym profilu technicznym:

- Pierwsze powiadomienie wyświetla odwołanie do `emailVerificationControl` kontrolki wyświetlania, która gromadzi i weryfikuje adres e-mail.
- Piąte zgłoszenie do wyświetlania `phoneVerificationControl` , które zbiera i weryfikuje numer telefonu.
- Inne oświadczenia są wyświetlane jako oświadczenia, które mają być zbierane od użytkownika.

```xml
<TechnicalProfile Id="Id">
  <DisplayClaims>
    <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
    <DisplayClaim DisplayControlReferenceId="phoneVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="newPassword" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
  </DisplayClaims>
</TechnicalProfile>
```

### <a name="persisted-claims"></a>Utrwalone oświadczenia

Element **PersistedClaims** zawiera wszystkie wartości, które powinny być utrwalane przez [profil techniczny usługi Azure AD](active-directory-technical-profile.md) z możliwymi do mapowania informacjami o typie już zdefiniowanym w sekcji [ClaimsSchema](claimsschema.md) w zasadach i nazwie atrybutu usługi Azure AD.

Nazwa tego żądania jest nazwą [atrybutu usługi Azure AD](user-profile-attributes.md) , chyba że określono atrybut **PartnerClaimType** , który zawiera nazwę atrybutu usługi Azure AD.

Element **PersistedClaims** zawiera następujące elementy:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| PersistedClaim | 1: n | Typ zgłoszenia, który ma być trwały. |

### <a name="persistedclaim"></a>PersistedClaim

Element **PersistedClaim** zawiera następujące atrybuty:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Tak | Identyfikator typu "Claim" jest już zdefiniowany w sekcji ClaimsSchema w pliku zasad lub nadrzędnym pliku zasad. |
| DefaultValue | Nie | Wartość domyślna, która ma zostać użyta do utworzenia żądania, jeśli nie istnieje. |
| PartnerClaimType | Nie | Identyfikator typu dla partnera zewnętrznego, na który jest mapowany określony typ roszczeń zasad. Jeśli atrybut PartnerClaimType nie zostanie określony, określony typ roszczeń zasad jest mapowany na typ wiązania partnera o tej samej nazwie. Użyj tej właściwości, gdy nazwa typu usługi jest inna od drugiej strony. Na przykład nazwa pierwszego wystąpienia to "imięname", podczas gdy Partner używa roszczeń o nazwie "first_name". |

W poniższym przykładzie profil techniczny usługi **AAD-UserWriteUsingLogonEmail** lub [pakiet początkowy](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccounts), który tworzy nowe konto lokalne, utrzymuje następujące oświadczenia:

```xml
<PersistedClaims>
  <PersistedClaim ClaimTypeReferenceId="email" PartnerClaimType="signInNames.emailAddress" />
  <PersistedClaim ClaimTypeReferenceId="newPassword" PartnerClaimType="password"/>
  <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />
  <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration" />
  <PersistedClaim ClaimTypeReferenceId="givenName" />
  <PersistedClaim ClaimTypeReferenceId="surname" />
</PersistedClaims>
```

## <a name="output-claims"></a>Oświadczenia wyjściowe

**OutputClaims** to kolekcja oświadczeń, które są zwracane z powrotem do zbioru oświadczeń po zakończeniu profilu technicznego. Możesz użyć tych oświadczeń w następnych krokach aranżacji lub przekształceń oświadczeń wyjściowych. Element **OutputClaims** zawiera następujący element:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| Oświadczenie outputclaim | 1: n | Oczekiwany typ zgłoszenia wyjściowego. |

### <a name="outputclaim"></a>Oświadczenie outputclaim

Element **oświadczenie outputclaim** zawiera następujące atrybuty:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Tak | Identyfikator typu "Claim" jest już zdefiniowany w sekcji ClaimsSchema w pliku zasad lub nadrzędnym pliku zasad. |
| DefaultValue | Nie | Wartość domyślna, która ma zostać użyta do utworzenia żądania, jeśli nie istnieje. |
|AlwaysUseDefaultValue |Nie |Wymuś użycie wartości domyślnej.  |
| PartnerClaimType | Nie | Identyfikator typu dla partnera zewnętrznego, na który jest mapowany określony typ roszczeń zasad. Jeśli nie określono atrybutu typu wiązania partnera, określony typ żądania zasad jest mapowany na typ wiązania partnera o tej samej nazwie. Użyj tej właściwości, gdy nazwa typu usługi jest inna od drugiej strony. Na przykład nazwa pierwszego wystąpienia to "imięname", podczas gdy Partner używa roszczeń o nazwie "first_name". |

## <a name="output-claims-transformations"></a>Przekształcenia oświadczeń wyjściowych

Element **OutputClaimsTransformations** może zawierać kolekcję elementów **OutputClaimsTransformation** . Przekształcenia oświadczeń wyjściowych są używane do modyfikowania oświadczeń wyjściowych lub generowania nowych. Po wykonaniu oświadczenia wyjściowe są umieszczane w zbiorze oświadczeń. Tych oświadczeń można użyć w kolejnych krokach aranżacji.

Dane wyjściowe poprzedniej transformacji oświadczeń w kolekcji transformacji oświadczeń mogą być oświadczeniami wejściowymi kolejnych transformacji oświadczeń wejściowych, co pozwala na uzyskanie sekwencji transformacji oświadczeń w zależności od siebie.

Element **OutputClaimsTransformations** zawiera następujący element:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| OutputClaimsTransformation | 1: n | Identyfikatory przekształceń oświadczeń, które należy wykonać przed wysłaniem oświadczeń do dostawcy oświadczeń lub jednostki uzależnionej. Transformacja oświadczeń może służyć do modyfikowania istniejących oświadczeń ClaimsSchema lub generowania nowych. |

### <a name="outputclaimstransformation"></a>OutputClaimsTransformation

Element **OutputClaimsTransformation** zawiera następujący atrybut:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Identyfikator odwołania | Tak | Identyfikator transformacji oświadczeń zdefiniowany już w pliku zasad lub nadrzędnym pliku zasad. |

Poniższy profil techniczny odwołuje się do przekształcenia oświadczeń AssertAccountEnabledIsTrue w celu ocenienia, czy konto jest włączone, czy nie po odczytaniu `accountEnabled` oświadczenia z katalogu.    

```xml
<TechnicalProfile Id="AAD-UserReadUsingEmailAddress">
  ...
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
    <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="accountEnabled" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
    <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
  </OutputClaims>
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertAccountEnabledIsTrue" />
  </OutputClaimsTransformations>
  ...
</TechnicalProfile>
```

## <a name="validation-technical-profiles"></a>Sprawdzanie poprawności profilów technicznych

Profil techniczny weryfikacji służy do sprawdzania poprawności oświadczeń danych wyjściowych w [profilu technicznym](self-asserted-technical-profile.md#validation-technical-profiles), który został potwierdzony. Profil techniczny weryfikacji jest zwykłym profilem technicznym z dowolnego protokołu, takiego jak [Azure Active Directory](active-directory-technical-profile.md) lub [interfejs API REST](restful-technical-profile.md). Profil techniczny walidacji zwraca oświadczenia wyjściowe lub zwraca kod błędu. Komunikat o błędzie jest renderowany do użytkownika na ekranie, dzięki czemu użytkownik może ponowić próbę.

Na poniższym diagramie pokazano, w jaki sposób Azure AD B2C sprawdzać poprawność poświadczeń użytkownika przy użyciu profilu technicznego weryfikacji

![Przepływ profilu technicznego weryfikacji diagramu](./media/technical-profiles/validation-technical-profile.png) 

Element **ValidationTechnicalProfiles** zawiera następujący element:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| ValidationTechnicalProfile | 1: n | Identyfikatory profilów technicznych, które są używane, sprawdzają niektóre lub wszystkie oświadczenia wynikowe profilu technicznego. Wszystkie oświadczenia wejściowe profilu technicznego, do którego istnieje odwołanie, muszą pojawić się w oświadczeniach wyjściowych w profilu technicznym odwołania. |

### <a name="validationtechnicalprofile"></a>ValidationTechnicalProfile

Element **ValidationTechnicalProfile** zawiera następujący atrybut:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Identyfikator odwołania | Tak | Identyfikator profilu technicznego jest już zdefiniowany w pliku zasad lub nadrzędnym pliku zasad. |

## <a name="subjectnaminginfo"></a>SubjectNamingInfo

**SubjectNamingInfo** definiuje nazwę podmiotu używaną w tokenach w [zasadach jednostki uzależnionej](relyingparty.md#subjectnaminginfo). **SubjectNamingInfo** zawiera następujący atrybut:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Claim | Tak | Identyfikator typu "Claim" jest już zdefiniowany w sekcji ClaimsSchema w pliku zasad. |

## <a name="include-technical-profile"></a>Uwzględnij profil techniczny

Profil techniczny może zawierać inny profil techniczny, aby zmienić ustawienia lub dodać nową funkcję. Element **IncludeTechnicalProfile** jest odwołaniem do wspólnego profilu technicznego, z którego pochodzi profil techniczny. Aby zmniejszyć nadmiarowość i złożoność elementów zasad, należy użyć dołączenia w przypadku wielu profilów technicznych, które współdzielą elementy podstawowe. Użyj wspólnego profilu technicznego ze wspólnym zestawem konfiguracji oraz z określonymi profilami technicznymi zadań, które obejmują wspólny profil techniczny. 

Załóżmy, że masz [profil techniczny interfejsu API REST](restful-technical-profile.md) z jednym punktem końcowym, w którym należy wysyłać różne zestawy oświadczeń dla różnych scenariuszy. Utwórz wspólny profil techniczny z udostępnianymi funkcjami, takimi jak identyfikator URI punktu końcowego interfejsu API REST, metadane, typ uwierzytelniania i klucze kryptograficzne. Utwórz określone profile techniczne zadań, które obejmują wspólny profil techniczny. Następnie Dodaj oświadczenia wejściowe, oświadczenia wyjściowe lub Zastąp identyfikator URI punktu końcowego interfejsu API REST związany z tym profilem technicznym.

Element **IncludeTechnicalProfile** zawiera następujący atrybut:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Identyfikator odwołania | Tak | Identyfikator profilu technicznego zdefiniowany już w pliku zasad lub nadrzędnym pliku zasad. |


Poniższy przykład ilustruje sposób użycia dołączenia:

- *Interfejs REST-Common-API —* wspólny profil techniczny z konfiguracją podstawową.
- *Rest-ValidateProfile* — obejmuje profil techniczny *REST-API-Common* oraz określa oświadczenia wejściowe i wyjściowe.
- *Rest-UpdateProfile* — obejmuje profil techniczny *REST-API-Common* , określa oświadczenia wejściowe i zastępuje `ServiceUrl` metadane.

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-API-Common">
      <DisplayName>Base REST API configuration</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity</Item>
        <Item Key="AuthenticationType">Basic</Item>
        <Item Key="SendClaimsIn">Body</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_B2cRestClientId" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_B2cRestClientSecret" />
      </CryptographicKeys>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>

    <TechnicalProfile Id="REST-ValidateProfile">
      <DisplayName>Validate the account and return promo code</DisplayName>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="objectId" />
        <InputClaim ClaimTypeReferenceId="email" />
        <InputClaim ClaimTypeReferenceId="userLanguage" PartnerClaimType="lang" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="promoCode" />
      </OutputClaims>
      <IncludeTechnicalProfile ReferenceId="REST-API-Common" />
    </TechnicalProfile>

    <TechnicalProfile Id="REST-UpdateProfile">
       <Metadata>
        <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/update</Item>
      </Metadata>
      <DisplayName>Update the user profile</DisplayName>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="objectId" />
        <InputClaim ClaimTypeReferenceId="email" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="REST-API-Common" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

### <a name="multi-level-inclusion"></a>Dołączanie wielopoziomowe 

Profil techniczny może obejmować pojedynczy profil techniczny. Nie ma żadnego limitu liczby poziomów dołączenia. Na przykład profil techniczny **AAD-UserReadUsingAlternativeSecurityId-NOERROR** obejmuje usługi **AAD-UserReadUsingAlternativeSecurityId**. Ten profil techniczny ustawia `RaiseErrorIfClaimsPrincipalDoesNotExist` element metadanych na `true` i zgłasza błąd, jeśli konto społecznościowe nie istnieje w katalogu. **AAD-UserReadUsingAlternativeSecurityId-NOERROR** zastępuje to zachowanie i wyłącza ten komunikat o błędzie.

```xml
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId-NoError">
  <Metadata>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">false</Item>
  </Metadata>
  <IncludeTechnicalProfile ReferenceId="AAD-UserReadUsingAlternativeSecurityId" />
</TechnicalProfile>
```

W przypadku usługi **AAD-UserReadUsingAlternativeSecurityId** uwzględniono `AAD-Common` profil techniczny.

```xml
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="Operation">Read</Item>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
    <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">User does not exist. Please sign up before you can sign in.</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityId" PartnerClaimType="alternativeSecurityId" Required="true" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
    <OutputClaim ClaimTypeReferenceId="givenName" />
    <OutputClaim ClaimTypeReferenceId="surname" />
  </OutputClaims>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

W przypadku usługi **AAD-UserReadUsingAlternativeSecurityId-NOERROR** i  **AAD-UserReadUsingAlternativeSecurityId** nie określono wymaganego elementu **Protocol** , ponieważ jest on określony w profilu technicznym usługi **AAD-Common** .

```xml
<TechnicalProfile Id="AAD-Common">
  <DisplayName>Azure Active Directory</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
</TechnicalProfile>
```

## <a name="use-technical-profile-for-session-management"></a>Używanie profilu technicznego do zarządzania sesją

Odwołanie do elementu **UseTechnicalProfileForSessionManagement** do [profilu technicznego sesji logowania](custom-policy-reference-sso.md)jednokrotnego. Element **UseTechnicalProfileForSessionManagement** zawiera następujący atrybut:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Identyfikator odwołania | Tak | Identyfikator profilu technicznego jest już zdefiniowany w pliku zasad lub nadrzędnym pliku zasad. |

## <a name="enabled-for-user-journeys"></a>Włączono dla podróży użytkownika

[ClaimsProviderSelections](userjourneys.md#identity-provider-selection) w podróży użytkownika definiuje listę opcji wyboru dostawcy oświadczeń i ich kolejność. Za pomocą filtru **EnabledForUserJourneys** element, który jest dostępny dla użytkownika. Element **EnabledForUserJourneys** zawiera jedną z następujących wartości:

- **Zawsze** należy wykonać profil techniczny.
- **Nigdy**, Pomiń profil techniczny.
- **OnClaimsExistence** wykonać tylko wtedy, gdy istnieje określone określone w profilu technicznym.
- **OnItemExistenceInStringCollectionClaim**, wykonaj tylko wtedy, gdy element istnieje w ramach żądania kolekcji ciągów.
- **OnItemAbsenceInStringCollectionClaim** wykonać tylko wtedy, gdy element nie istnieje w ramach żądania kolekcji ciągów.

Korzystanie z **OnClaimsExistence**, **OnItemExistenceInStringCollectionClaim** lub **OnItemAbsenceInStringCollectionClaim** wymaga podania następujących metadanych: 

- **ClaimTypeOnWhichToEnable** — określa typ zgłoszenia, który ma zostać obliczony.
- **ClaimValueOnWhichToEnable** — określa wartość do porównania.

Następujący profil techniczny jest wykonywany tylko wtedy, gdy kolekcja ciągów **skojarzeni** zawiera wartość `facebook.com` :

```xml
<TechnicalProfile Id="UnLink-Facebook-OAUTH">
  <DisplayName>Unlink Facebook</DisplayName>
...
    <Metadata>
      <Item Key="ClaimTypeOnWhichToEnable">identityProviders</Item>
      <Item Key="ClaimValueOnWhichToEnable">facebook.com</Item>
    </Metadata>
...
  <EnabledForUserJourneys>OnItemExistenceInStringCollectionClaim</EnabledForUserJourneys>
</TechnicalProfile>
```
