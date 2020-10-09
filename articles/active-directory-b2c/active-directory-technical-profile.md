---
title: Definiowanie profilu technicznego usługi Azure AD w ramach zasad niestandardowych
titleSuffix: Azure AD B2C
description: Zdefiniuj profil techniczny Azure Active Directory w zasadach niestandardowych w programie Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 67acf675c6636c5d1066d4fe25310d875fa7c064
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85201518"
---
# <a name="define-an-azure-active-directory-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Zdefiniuj profil techniczny Azure Active Directory w Azure Active Directory B2C zasadach niestandardowych

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) zapewnia obsługę Azure Active Directory zarządzania użytkownikami. W tym artykule opisano szczegóły dotyczące profilu technicznego dotyczącego współpracy z dostawcą oświadczeń obsługującym ten standardowy protokół.

## <a name="protocol"></a>Protokół

Atrybut **name** elementu **Protocol** musi być ustawiony na `Proprietary` . Atrybut **programu obsługi** musi zawierać w pełni kwalifikowaną nazwę zestawu programu obsługi protokołu `Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null` .

Poniżej przedstawiono [zasady niestandardowe](custom-policy-get-started.md#custom-policy-starter-pack) profile techniczne usługi Azure AD w pakiecie **AAD — wspólny** profil techniczny. Profile techniczne usługi Azure AD nie określają protokołu, ponieważ protokół jest skonfigurowany w profilu technicznym w **usłudze AAD** :
 
- **AAD-UserReadUsingAlternativeSecurityId** i **AAD-UserReadUsingAlternativeSecurityId-NOERROR** — wyszukiwanie konta społecznościowego w katalogu.
- **AAD-UserWriteUsingAlternativeSecurityId** — Tworzenie nowego konta społecznościowego.
- **AAD-UserReadUsingEmailAddress** — wyszukiwanie konta lokalnego w katalogu.
- **AAD-UserWriteUsingLogonEmail** — Tworzenie nowego konta lokalnego.
- **AAD-UserWritePasswordUsingObjectId** — aktualizuje hasło do konta lokalnego.
- **AAD-UserWriteProfileUsingObjectId** — aktualizowanie profilu użytkownika konta lokalnego lub społecznościowego.
- **AAD-UserReadUsingObjectId** — odczytywanie profilu użytkownika konta lokalnego lub społecznościowego.
- **AAD-UserWritePhoneNumberUsingObjectId** — Napisz numer telefonu MFA konta lokalnego lub społecznościowego

W poniższym przykładzie przedstawiono profil techniczny usługi **AAD-Common** :

```xml
<TechnicalProfile Id="AAD-Common">
  <DisplayName>Azure Active Directory</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />

  <CryptographicKeys>
    <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
  </CryptographicKeys>

  <!-- We need this here to suppress the SelfAsserted provider from invoking SSO on validation profiles. -->
  <IncludeInSso>false</IncludeInSso>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

## <a name="inputclaims"></a>InputClaims

Element InputClaims zawiera zastrzeżenie, które jest używane do wyszukania konta w katalogu lub utworzenia nowego. W kolekcji oświadczeń wejściowych musi znajdować się tylko jeden element oświadczenie inputclaim dla wszystkich profilów technicznych usługi Azure AD. Może być konieczne zamapowanie nazwy żądania zdefiniowanego w zasadach na nazwę zdefiniowaną w Azure Active Directory.

Aby odczytać, zaktualizować lub usunąć istniejące konto użytkownika, jest to klucz, który jednoznacznie identyfikuje konto w katalogu usługi Azure AD. Na przykład **objectid**, **userPrincipalName**, **signInNames. EmailAddress**, **signInNames. username**lub **alternativeSecurityId**. 

Aby utworzyć nowe konto użytkownika, jest to klucz, który jednoznacznie identyfikuje konto lokalne lub federacyjne. Na przykład konto lokalne: **signInNames. EmailAddress**lub **signInNames. username**. Dla konta federacyjnego: **alternativeSecurityId**.

Element [InputClaimsTransformations](technicalprofiles.md#inputclaimstransformations) może zawierać kolekcję elementów transformacji oświadczeń wejściowych, które są używane do modyfikowania oświadczenia wejściowego lub generują nowe.

## <a name="outputclaims"></a>OutputClaims

Element **OutputClaims** zawiera listę oświadczeń zwracanych przez profil techniczny usługi Azure AD. Może być konieczne zamapowanie nazwy żądania zdefiniowanego w zasadach na nazwę zdefiniowaną w Azure Active Directory. Można również uwzględnić oświadczenia, które nie są zwracane przez Azure Active Directory, o ile atrybut ten jest ustawiony `DefaultValue` .

Element [OutputClaimsTransformations](technicalprofiles.md#outputclaimstransformations) może zawierać kolekcję elementów **OutputClaimsTransformation** , które są używane do modyfikowania oświadczeń wyjściowych lub generowania nowych.

Na przykład profil techniczny usługi **AAD-UserWriteUsingLogonEmail** tworzy konto lokalne i zwraca następujące oświadczenia:

- **objectid**, który jest identyfikatorem nowego konta
- **newUser**, który wskazuje, czy użytkownik jest nowy
- **authenticationSource**, w którym są ustawiane uwierzytelnianie `localAccountAuthentication`
- **userPrincipalName**, czyli główna nazwa użytkownika nowego konta
- **signInNames. EmailAddress**, czyli nazwa logowania do konta, podobna do dodanego **adresu e-mail**

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="objectId" />
  <OutputClaim ClaimTypeReferenceId="newUser" PartnerClaimType="newClaimsPrincipalCreated" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
  <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
  <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
</OutputClaims>
```

## <a name="persistedclaims"></a>PersistedClaims

Element **PersistedClaims** zawiera wszystkie wartości, które powinny być utrwalane przez usługę Azure AD, z możliwymi do mapowania informacjami o typie już zdefiniowanym w sekcji [ClaimsSchema](claimsschema.md) w zasadach i nazwie atrybutu usługi Azure AD.

Profil techniczny usługi **AAD-UserWriteUsingLogonEmail** , który tworzy nowe konto lokalne, utrzymuje następujące oświadczenia:

```xml
  <PersistedClaims>
    <!-- Required claims -->
    <PersistedClaim ClaimTypeReferenceId="email" PartnerClaimType="signInNames.emailAddress" />
    <PersistedClaim ClaimTypeReferenceId="newPassword" PartnerClaimType="password"/>
    <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />
    <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration" />

    <!-- Optional claims. -->
    <PersistedClaim ClaimTypeReferenceId="givenName" />
    <PersistedClaim ClaimTypeReferenceId="surname" />
  </PersistedClaims>
```

Nazwa tego żądania jest nazwą atrybutu usługi Azure AD, chyba że określono atrybut **PartnerClaimType** , który zawiera nazwę atrybutu usługi Azure AD.

## <a name="requirements-of-an-operation"></a>Wymagania operacji

- W zbiorze oświadczeń musi być dokładnie jeden element **oświadczenie inputclaim** dla wszystkich profilów technicznych usługi Azure AD.
- W [artykule atrybuty profilu użytkownika](user-profile-attributes.md) opisano obsługiwane Azure AD B2C atrybuty profilu użytkownika, których można użyć w oświadczeniach wejściowych, oświadczeniach wyjściowych i utrwalonych oświadczeniach. 
- Jeśli operacja jest `Write` lub `DeleteClaims` , to musi także występować w elemencie **PersistedClaims** .
- Wartość żądania **userPrincipalName** musi mieć format `user@tenant.onmicrosoft.com` .
- Żądanie **DisplayName** jest wymagane i nie może być pustym ciągiem.

## <a name="azure-ad-technical-provider-operations"></a>Operacje związane z dostawcą technicznym usługi Azure AD

### <a name="read"></a>Odczyt

Operacja **odczytu** odczytuje dane dotyczące jednego konta użytkownika. Poniższy profil techniczny odczytuje dane dotyczące konta użytkownika przy użyciu identyfikatora obiektu użytkownika:

```xml
<TechnicalProfile Id="AAD-UserReadUsingObjectId">
  <Metadata>
    <Item Key="Operation">Read</Item>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
  </Metadata>
  <IncludeInSso>false</IncludeInSso>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
  </InputClaims>
  <OutputClaims>

    <!-- Required claims -->
    <OutputClaim ClaimTypeReferenceId="strongAuthenticationPhoneNumber" />

    <!-- Optional claims -->
    <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
    <OutputClaim ClaimTypeReferenceId="givenName" />
    <OutputClaim ClaimTypeReferenceId="surname" />
  </OutputClaims>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

### <a name="write"></a>Zapisywanie

Operacja **zapisu** tworzy lub aktualizuje pojedyncze konto użytkownika. Poniższy profil techniczny tworzy nowe konto społecznościowe:

```xml
<TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="Operation">Write</Item>
    <Item Key="RaiseErrorIfClaimsPrincipalAlreadyExists">true</Item>
    <Item Key="UserMessageIfClaimsPrincipalAlreadyExists">You are already registered, please press the back button and sign in instead.</Item>
  </Metadata>
  <IncludeInSso>false</IncludeInSso>
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="CreateOtherMailsFromEmail" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityId" PartnerClaimType="alternativeSecurityId" Required="true" />
  </InputClaims>
  <PersistedClaims>
    <!-- Required claims -->
    <PersistedClaim ClaimTypeReferenceId="alternativeSecurityId" />
    <PersistedClaim ClaimTypeReferenceId="userPrincipalName" />
    <PersistedClaim ClaimTypeReferenceId="mailNickName" DefaultValue="unknown" />
    <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />

    <!-- Optional claims -->
    <PersistedClaim ClaimTypeReferenceId="otherMails" />
    <PersistedClaim ClaimTypeReferenceId="givenName" />
    <PersistedClaim ClaimTypeReferenceId="surname" />
  </PersistedClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="newUser" PartnerClaimType="newClaimsPrincipalCreated" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
  </OutputClaims>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
</TechnicalProfile>
```

### <a name="deleteclaims"></a>DeleteClaims

Operacja **DeleteClaims** czyści informacje z podanej listy oświadczeń. Poniższy profil techniczny usuwa oświadczenia:

```xml
<TechnicalProfile Id="AAD-DeleteClaimsUsingObjectId">
  <Metadata>
    <Item Key="Operation">DeleteClaims</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
  </InputClaims>
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="objectId" />
    <PersistedClaim ClaimTypeReferenceId="Verified.strongAuthenticationPhoneNumber" PartnerClaimType="strongAuthenticationPhoneNumber" />
  </PersistedClaims>
  <OutputClaims />
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

### <a name="deleteclaimsprincipal"></a>DeleteClaimsPrincipal

Operacja **DeleteClaimsPrincipal** usuwa jedno konto użytkownika z katalogu. Poniższy profil techniczny usuwa konto użytkownika z katalogu przy użyciu głównej nazwy użytkownika:

```xml
<TechnicalProfile Id="AAD-DeleteUserUsingObjectId">
  <Metadata>
    <Item Key="Operation">DeleteClaimsPrincipal</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
  </InputClaims>
  <OutputClaims/>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

Poniższy profil techniczny usuwa konto użytkownika społecznościowego przy użyciu **alternativeSecurityId**:

```xml
<TechnicalProfile Id="AAD-DeleteUserUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="Operation">DeleteClaimsPrincipal</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="alternativeSecurityId" Required="true" />
  </InputClaims>
  <OutputClaims/>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```
## <a name="metadata"></a>Metadane

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Operacja | Tak | Operacja do wykonania. Możliwe wartości: `Read` , `Write` , `DeleteClaims` lub `DeleteClaimsPrincipal` . |
| RaiseErrorIfClaimsPrincipalDoesNotExist | Nie | Zgłoś błąd, jeśli obiekt użytkownika nie istnieje w katalogu. Możliwe wartości: `true` lub `false` . |
| RaiseErrorIfClaimsPrincipalAlreadyExists | Nie | Zgłoś błąd, jeśli obiekt użytkownika już istnieje. Możliwe wartości: `true` lub `false` .|
| ApplicationObjectId | Nie | Identyfikator obiektu aplikacji dla atrybutów rozszerzenia. Value: ObjectId aplikacji. Aby uzyskać więcej informacji, zobacz [Używanie atrybutów niestandardowych w niestandardowych zasadach edytowania profilu](custom-policy-custom-attributes.md). |
| ClientId | Nie | Identyfikator klienta służący do uzyskiwania dostępu do dzierżawy jako osoba trzecia. Aby uzyskać więcej informacji, zobacz [Używanie atrybutów niestandardowych w niestandardowych zasadach edytowania profilu](custom-policy-custom-attributes.md) |
| IncludeClaimResolvingInClaimsHandling  | Nie | W przypadku oświadczeń wejściowych i wyjściowych określa, czy w profilu technicznym znajduje się [rozpoznawanie oświadczeń](claim-resolver-overview.md) . Możliwe wartości: `true` , lub `false`   (wartość domyślna). Jeśli chcesz użyć programu rozpoznawania oświadczeń w profilu technicznym, ustaw dla tej opcji wartość `true` . |

### <a name="ui-elements"></a>Elementy interfejsu użytkownika
 
Przy użyciu poniższych ustawień można skonfigurować komunikat o błędzie wyświetlany w przypadku awarii. Metadane należy skonfigurować w profilu technicznym z [własnym potwierdzeniem](self-asserted-technical-profile.md) . Komunikaty o błędach można [lokalizować](localization.md).

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| UserMessageIfClaimsPrincipalAlreadyExists | Nie | Jeśli błąd ma zostać podniesiony (zobacz Opis atrybutu RaiseErrorIfClaimsPrincipalAlreadyExists), określ komunikat, który ma być wyświetlany użytkownikowi, jeśli obiekt użytkownika już istnieje. |
| UserMessageIfClaimsPrincipalDoesNotExist | Nie | Jeśli błąd ma zostać podniesiony (zobacz Opis atrybutu RaiseErrorIfClaimsPrincipalDoesNotExist), określ komunikat, który ma być wyświetlany użytkownikowi, jeśli obiekt użytkownika nie istnieje. |


## <a name="next-steps"></a>Następne kroki

Zapoznaj się z poniższym artykułem, na przykład korzystając z profilu technicznego usługi Azure AD:

- [Dodawanie oświadczeń i dostosowywanie danych wejściowych użytkownika przy użyciu zasad niestandardowych w Azure Active Directory B2C](custom-policy-configure-user-input.md)














