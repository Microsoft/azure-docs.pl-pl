---
title: Zdefiniuj profil techniczny z własnym potwierdzeniem w zasadach niestandardowych
titleSuffix: Azure AD B2C
description: Zdefiniuj profil techniczny z własnym potwierdzeniem w zasadach niestandardowych w Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/15/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 817267414555ea0641e8fb8a8392976a4789c780
ms.sourcegitcommit: 93329b2fcdb9b4091dbd632ee031801f74beb05b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2020
ms.locfileid: "92096219"
---
# <a name="define-a-self-asserted-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Zdefiniuj własny profil techniczny w Azure Active Directory B2C zasad niestandardowych

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Wszystkie interakcje w Azure Active Directory B2C (Azure AD B2C), w których użytkownik powinien podać dane wejściowe, są profilami technicznymi z własnym potwierdzeń. Na przykład Strona rejestracji, Strona logowania lub Resetowanie hasła.

## <a name="protocol"></a>Protokół

Atrybut **name** elementu **Protocol** musi być ustawiony na `Proprietary` . Atrybut **programu obsługi** musi zawierać w pełni kwalifikowaną nazwę zestawu programu obsługi protokołu, który jest używany przez Azure AD B2C, dla własnego potwierdzenia: `Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

Poniższy przykład przedstawia profil techniczny z własnym potwierdzeniem do rejestracji w wiadomościach e-mail:

```xml
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
```

## <a name="input-claims"></a>Oświadczenia wejściowe

W profilu technicznym z własnym potwierdzeniem można użyć elementów **InputClaims** i **InputClaimsTransformations** , aby wstępnie wypełnić wartość oświadczeń, które pojawiają się na stronie z możliwością potwierdzenia (wyświetlanie oświadczeń). Na przykład w obszarze Edytowanie zasad profilu Użytkownik najpierw odczytuje profil użytkownika z usługi Azure AD B2C Directory, a następnie samodzielnie potwierdzony profil techniczny ustawi oświadczenia wejściowe przy użyciu danych użytkownika przechowywanych w profilu użytkownika. Te oświadczenia są zbierane z profilu użytkownika, a następnie prezentowane użytkownikowi, który może edytować istniejące dane.

```xml
<TechnicalProfile Id="SelfAsserted-ProfileUpdate">
...
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="alternativeSecurityId" />
    <InputClaim ClaimTypeReferenceId="userPrincipalName" />
    <InputClaim ClaimTypeReferenceId="givenName" />
    <InputClaim ClaimTypeReferenceId="surname" />
  </InputClaims>
```

## <a name="display-claims"></a>Wyświetl oświadczenia

Funkcja wyświetlania oświadczeń jest obecnie w **wersji zapoznawczej**.

Element **DisplayClaims** zawiera listę oświadczeń, które mają być wyświetlane na ekranie w celu zbierania danych od użytkownika. Aby wstępnie wypełnić wartości oświadczeń ekranu, użyj oświadczeń wejściowych, które zostały wcześniej opisane. Element może również zawierać wartość domyślną.

Kolejność oświadczeń w **DisplayClaims** określa kolejność, w której Azure AD B2C renderuje oświadczenia na ekranie. Aby wymusić użytkownikowi podanie wartości dla określonego żądania, ustaw **wymagany** atrybut elementu **DisplayClaim** na `true` .

Element **ClaimType** w kolekcji **DisplayClaims** musi określać element **UserInputType** dla dowolnego typu danych wejściowych użytkownika obsługiwanego przez Azure AD B2C. Na przykład: `TextBox` lub `DropdownSingleSelect`.

### <a name="add-a-reference-to-a-displaycontrol"></a>Dodawanie odwołania do elementu DisplayControl

W kolekcji Wyświetl oświadczenia można dołączyć odwołanie [do utworzonego ekranu](display-controls.md) . Kontrolka wyświetlania to element interfejsu użytkownika, który ma specjalne funkcje i współdziała z Azure AD B2C usługą zaplecza. Umożliwia użytkownikowi wykonywanie akcji na stronie, które wywołują profil techniczny weryfikacji na zapleczu. Na przykład weryfikacja adresu e-mail, numeru telefonu lub numeru lojalnościowego klienta.

Poniższy przykład `TechnicalProfile` ilustruje sposób użycia oświadczeń wyświetlania z kontrolkami wyświetlania.

* Pierwsze powiadomienie wyświetla odwołanie do `emailVerificationControl` kontrolki wyświetlania, która gromadzi i weryfikuje adres e-mail.
* Piąte zgłoszenie do wyświetlania `phoneVerificationControl` , które zbiera i weryfikuje numer telefonu.
* Inne oświadczenia są wyświetlane jako oświadczenia, które mają być zbierane od użytkownika.

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

Jak wspomniano wcześniej, w przypadku wystąpienia elementu z odwołaniem do kontrolki wyświetlania może zostać uruchomione własne sprawdzenie poprawności, na przykład zweryfikowanie adresu e-mail. Ponadto strona samodzielna obsługuje sprawdzanie poprawności całej strony, w tym wszelkie dane wejściowe użytkownika (typy lub kontrolki wyświetlania), przed przejściem do następnego kroku aranżacji.

### <a name="combine-usage-of-display-claims-and-output-claims-carefully"></a>Dokładne łączenie użycia oświadczeń i oświadczeń wyjściowych

W przypadku określenia co najmniej jednego elementu **DisplayClaim** w profilu technicznym z własnym poproszonym należy użyć DisplayClaim dla *każdego* żądania, które ma być wyświetlane na ekranie i zbierać od użytkownika. Żadne oświadczenia wyjściowe nie są wyświetlane przez własny profil techniczny, który zawiera co najmniej jedno oświadczenie dotyczące wyświetlania.

Rozważmy następujący przykład, w którym `age` jest zdefiniowane jako zgłoszenie **wyjściowe** w zasadach podstawowych. Przed dodaniem jakichkolwiek oświadczeń ekranu do profilu technicznego z własnym potwierdzeniem `age` oświadczenie jest wyświetlane na ekranie na potrzeby zbierania danych od użytkownika:

```xml
<TechnicalProfile Id="id">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="age" />
  </OutputClaims>
</TechnicalProfile>
```

Jeśli zasada liścia, która dziedziczy ten element podstawowy, określa `officeNumber` jako rolę **wyświetlaną** :

```xml
<TechnicalProfile Id="id">
  <DisplayClaims>
    <DisplayClaim ClaimTypeReferenceId="officeNumber" />
  </DisplayClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="officeNumber" />
  </OutputClaims>
</TechnicalProfile>
```

`age`W zasadach podstawowych nie jest już wyświetlane na ekranie użytkownika — jest to efektywnie "ukryte". Aby wyświetlić to `age` zdarzenie i zebrać wartość wieku od użytkownika, należy dodać `age` **DisplayClaim**.

## <a name="output-claims"></a>Oświadczenia wyjściowe

Element **OutputClaims** zawiera listę oświadczeń do zwrócenia do następnego kroku aranżacji. Atrybut **DefaultValue** działa tylko wtedy, gdy nie ustawiono tego żądania. Jeśli została ustawiona w poprzednim kroku aranżacji, wartość domyślna nie zacznie obowiązywać, nawet jeśli użytkownik opuści wartość pustą. Aby wymusić użycie wartości domyślnej, ustaw atrybut **AlwaysUseDefaultValue** na `true` .

Ze względów bezpieczeństwa wartość żądania hasła ( `UserInputType` ustawiona na `Password` ) jest dostępna tylko w profilach technicznych weryfikacji profilu technicznego z własnymi potwierdzeń. Nie można użyć roszczeń hasła w następnych krokach aranżacji. 

> [!NOTE]
> W poprzednich wersjach programu Identity Experience Framework (IEF) oświadczenia wyjściowe były używane do zbierania danych od użytkownika. Aby zebrać dane od użytkownika, należy zamiast tego użyć kolekcji **DisplayClaims** .

Element **OutputClaimsTransformations** może zawierać kolekcję elementów **OutputClaimsTransformation** , które są używane do modyfikowania oświadczeń wyjściowych lub generowania nowych.

### <a name="when-you-should-use-output-claims"></a>Kiedy należy używać oświadczeń wyjściowych

W profilu technicznym z własnym potwierdzeniem kolekcja oświadczeń wychodzących zwraca oświadczenia do następnego kroku aranżacji.

Użyj oświadczeń wyjściowych, gdy:

- **Oświadczenia są wyprowadzane przez transformację oświadczeń wyjściowych**.
- **Ustawianie wartości domyślnej w ramach zgłoszenia wyjściowego** bez zbierania danych od użytkownika lub zwracania danych z profilu kontroli technicznej. `LocalAccountSignUpWithLogonEmail`Profil techniczny z własnym potwierdzeń ustawia **SelfAsserted** do wykonania `true` .
- **Profil techniczny weryfikacji zwraca oświadczenia wyjściowe** — Twój profil techniczny może wywołać profil techniczny weryfikacji, który zwraca pewne oświadczenia. Można chcieć wyrównać oświadczenia i zwrócić je do następnych kroków aranżacji w podróży użytkownika. Na przykład podczas logowania się przy użyciu konta lokalnego, z własnym profilem technicznym, nazywa `SelfAsserted-LocalAccountSignin-Email` się profilem technicznym weryfikacji o nazwie `login-NonInteractive` . Ten profil techniczny sprawdza poprawność poświadczeń użytkownika, a także zwraca profil użytkownika. Takie jak "userPrincipalName", "displayName", "podaną nazwę" i "nazwisko".
- **Kontrolka wyświetlania zwraca oświadczenia wyjściowe** — Twój profil techniczny może mieć odwołanie do [kontrolki wyświetlania](display-controls.md). Kontrolka wyświetlania zwraca pewne oświadczenia, takie jak zweryfikowany adres e-mail. Można chcieć wyrównać oświadczenia i zwrócić je do następnych kroków aranżacji w podróży użytkownika. Funkcja kontrolki wyświetlania jest obecnie w **wersji zapoznawczej**.

W poniższym przykładzie zademonstrowano korzystanie z własnego profilu technicznego, który używa zarówno oświadczeń, jak i oświadczeń wyjściowych.

```xml
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="IpAddressClaimReferenceId">IpAddress</Item>
    <Item Key="ContentDefinitionReferenceId">api.localaccountsignup</Item>
    <Item Key="language.button_continue">Create</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" />
  </InputClaims>
  <DisplayClaims>
    <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
    <DisplayClaim DisplayControlReferenceId="SecondaryEmailVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="newPassword" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
  </DisplayClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" Required="true" />
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />
    <OutputClaim ClaimTypeReferenceId="authenticationSource" />
    <OutputClaim ClaimTypeReferenceId="newUser" />
  </OutputClaims>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="AAD-UserWriteUsingLogonEmail" />
  </ValidationTechnicalProfiles>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
</TechnicalProfile>
```

## <a name="persist-claims"></a>Utrwalanie oświadczeń

Element PersistedClaims nie jest używany. Profil techniczny z własnym potwierdzeniem nie utrwala danych do Azure AD B2C. Zamiast tego jest wykonywane wywołanie w profilu technicznym weryfikacji, który jest odpowiedzialny za utrwalanie danych. Na przykład zasady tworzenia konta używają `LocalAccountSignUpWithLogonEmail` profilu technicznego z własnym potwierdzeniem do zebrania nowego profilu użytkownika. `LocalAccountSignUpWithLogonEmail`Profil techniczny wywołuje sprawdzanie poprawności profilu technicznego w celu utworzenia konta w Azure AD B2C.

## <a name="validation-technical-profiles"></a>Sprawdzanie poprawności profilów technicznych

Profil techniczny weryfikacji jest używany do sprawdzania poprawności niektórych lub wszystkich oświadczeń wyjściowych w profilu technicznym, do którego się odwołuje. Oświadczenia wejściowe profilu technicznego weryfikacji muszą pojawić się w oświadczeniach wyjściowych profilu technicznego z potwierdzeniem. Profil techniczny weryfikacji sprawdza poprawność danych wejściowych użytkownika i może zwrócić błąd użytkownika.

Profil techniczny weryfikacji może być dowolnym profilem technicznym w zasadach, takim jak [Azure Active Directory](active-directory-technical-profile.md) lub profile techniczne [interfejsu API REST](restful-technical-profile.md) . W poprzednim przykładzie `LocalAccountSignUpWithLogonEmail` profil techniczny sprawdza, czy signinName nie istnieje w katalogu. W przeciwnym razie sprawdzanie poprawności profilu technicznego spowoduje utworzenie konta lokalnego i zwrócenie identyfikatora objectId, authenticationSource, newUser. `SelfAsserted-LocalAccountSignin-Email`Profil techniczny wywołuje `login-NonInteractive` Sprawdzanie poprawności profilu technicznego w celu zweryfikowania poświadczeń użytkownika.

Możesz również wywołać profil techniczny interfejsu API REST z logiką biznesową, zastępować oświadczenia wejściowe lub wzbogacać dane użytkowników, zapewniając dalszą integrację z firmową aplikacją biznesową. Aby uzyskać więcej informacji, zobacz temat [Sprawdzanie poprawności profilu technicznego](validation-technical-profile.md)

## <a name="metadata"></a>Metadane

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| ustawienie. operatmode <sup>1</sup>| Nie | Dla strony logowania ta właściwość kontroluje zachowanie pola username, takie jak walidacja danych wejściowych i komunikaty o błędach. Oczekiwane wartości: `Username` lub `Email` .  |
| AllowGenerationOfClaimsWithNullValues| Nie| Zezwalaj na generowanie elementu Claim z wartością null. Na przykład w przypadku użytkownika nie jest zaznaczone pole wyboru.|
| ContentDefinitionReferenceId | Tak | Identyfikator [definicji zawartości](contentdefinitions.md) skojarzonej z tym profilem technicznym. |
| EnforceEmailVerification | Nie | W przypadku rejestrowania lub edytowania profilu wymusza weryfikację poczty e-mail. Możliwe wartości: `true` (ustawienie domyślne) lub `false` . |
| Ustawianie. retryLimit | Nie | Określa, ile razy użytkownik może próbować podać dane, które są sprawdzane względem profilu technicznego weryfikacji. Na przykład użytkownik próbuje zarejestrować się przy użyciu konta, które już istnieje, i kontynuuje podejmowanie prób aż do osiągnięcia limitu.
| SignUpTarget <sup>1</sup>| Nie | Docelowy identyfikator wymiany programu Exchange. Gdy użytkownik kliknie przycisk rejestracji, Azure AD B2C wykonuje określony identyfikator programu Exchange. |
| Ustawianie. showCancelButton | Nie | Wyświetla przycisk Anuluj. Możliwe wartości: `true` (ustawienie domyślne) lub `false` |
| Ustawianie. showContinueButton | Nie | Wyświetla przycisk Kontynuuj. Możliwe wartości: `true` (ustawienie domyślne) lub `false` |
| Ustawianie. showSignupLink <sup>2</sup>| Nie | Wyświetla przycisk rejestracji. Możliwe wartości: `true` (ustawienie domyślne) lub `false` |
| Ustawianie. forgotPasswordLinkLocation <sup>2</sup>| Nie| Wyświetla łącze zapomniane hasło. Możliwe wartości: `AfterInput` (domyślnie) łącze jest wyświetlane w dolnej części strony lub `None` usuwa łącze zapomniane hasło.|
| Ustawianie. enableRememberMe <sup>2</sup>| Nie| Wyświetla pole wyboru nie wylogowuj [mnie](custom-policy-keep-me-signed-in.md) . Możliwe wartości: `true` , lub `false` (wartość domyślna). |
| Ustawianie. inputVerificationDelayTimeInMilliseconds <sup>3</sup>| Nie| Usprawnia środowisko użytkownika, czekając na zakończenie wpisywania przez użytkownika, a następnie sprawdza poprawność wartości. Wartość domyślna 2000 milisekund. |
| IncludeClaimResolvingInClaimsHandling  | Nie | W przypadku oświadczeń wejściowych i wyjściowych określa, czy w profilu technicznym znajduje się [rozpoznawanie oświadczeń](claim-resolver-overview.md) . Możliwe wartości: `true` , lub `false`   (wartość domyślna). Jeśli chcesz użyć programu rozpoznawania oświadczeń w profilu technicznym, ustaw dla tej opcji wartość `true` . |

Uwagi:
1. Dostępne dla definicji zawartości [DataUri](contentdefinitions.md#datauri) typu `unifiedssp` lub `unifiedssd` .
1. Dostępne dla definicji zawartości [DataUri](contentdefinitions.md#datauri) typu `unifiedssp` lub `unifiedssd` . [Układ strony w wersji](page-layout.md) 1.1.0 lub nowszej.
1. Dostępne dla [wersji układu strony](page-layout.md) 1.2.0 i nowszych.

## <a name="cryptographic-keys"></a>Klucze kryptograficzne

Element **CryptographicKeys** nie jest używany.
