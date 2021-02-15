---
title: Dodawanie AD FS jako dostawcy tożsamości SAML przy użyciu zasad niestandardowych
titleSuffix: Azure AD B2C
description: Skonfiguruj AD FS 2016 przy użyciu protokołu SAML i zasad niestandardowych w programie Azure Active Directory B2C
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/12/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 6dda65be98934ce90e985b241078ae8019afb7e0
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100361268"
---
# <a name="add-ad-fs-as-a-saml-identity-provider-using-custom-policies-in-azure-active-directory-b2c"></a>Dodawanie AD FS jako dostawcy tożsamości SAML przy użyciu zasad niestandardowych w programie Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

W tym artykule opisano sposób włączania logowania do konta użytkownika AD FS przy użyciu [zasad niestandardowych](custom-policy-overview.md) w Azure Active Directory B2C (Azure AD B2C). Aby włączyć logowanie, Dodaj [profil techniczny dostawcy tożsamości SAML](saml-identity-provider-technical-profile.md) do zasad niestandardowych.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="create-a-self-signed-certificate"></a>Tworzenie certyfikatu z podpisem własnym

[!INCLUDE [active-directory-b2c-create-self-signed-certificate](../../includes/active-directory-b2c-create-self-signed-certificate.md)]

## <a name="create-a-policy-key"></a>Tworzenie klucza zasad

Musisz przechowywać certyfikat w dzierżawie Azure AD B2C.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Upewnij się, że używasz katalogu zawierającego dzierżawcę Azure AD B2C. W górnym menu wybierz pozycję **katalog i subskrypcja** , a następnie wybierz katalog, w którym znajduje się Twoja dzierżawa.
3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Na stronie Przegląd wybierz pozycję **Struktura środowiska tożsamości**.
5. Wybierz pozycję **klucze zasad** , a następnie wybierz pozycję **Dodaj**.
6. W obszarze **Opcje** wybierz opcję `Upload` .
7. Wprowadź **nazwę** klucza zasad. Na przykład `SAMLSigningCert`. Prefiks `B2C_1A_` jest automatycznie dodawany do nazwy klucza.
8. Przejdź do pliku certyfikatu PFX z kluczem prywatnym i wybierz go.
9. Kliknij pozycję **Utwórz**.

## <a name="add-a-claims-provider"></a>Dodawanie dostawcy oświadczeń

Jeśli chcesz, aby użytkownicy mogli się logować przy użyciu konta AD FS, musisz zdefiniować konto jako dostawcę oświadczeń, z którym Azure AD B2C może komunikować się za pośrednictwem punktu końcowego. Punkt końcowy zawiera zestaw oświadczeń, które są używane przez Azure AD B2C do sprawdzenia, czy określony użytkownik został uwierzytelniony.

Konto AD FS można zdefiniować jako dostawcę oświadczeń, dodając je do elementu **ClaimsProviders** w pliku rozszerzenia zasad. Aby uzyskać więcej informacji, zobacz [Definiowanie profilu technicznego dostawcy tożsamości SAML](saml-identity-provider-technical-profile.md).

1. Otwórz *TrustFrameworkExtensions.xml*.
1. Znajdź element **ClaimsProviders** . Jeśli nie istnieje, Dodaj ją do elementu głównego.
1. Dodaj nową **ClaimsProvider** w następujący sposób:

    ```xml
    <ClaimsProvider>
      <Domain>contoso.com</Domain>
      <DisplayName>Contoso AD FS</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Contoso-SAML2">
          <DisplayName>Contoso AD FS</DisplayName>
          <Description>Login with your AD FS account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="PartnerEntity">https://your-AD-FS-domain/federationmetadata/2007-06/federationmetadata.xml</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="userPrincipalName" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name"/>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication"/>
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-idp"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Zastąp ciąg `your-AD-FS-domain` nazwą domeny AD FS i Zastąp wartość **identityProvider** wynikowe w systemie DNS (arbitralną wartość wskazującą domenę).

1. Znajdź `<ClaimsProviders>` sekcję i Dodaj następujący fragment kodu XML. Jeśli zasady zawierają już `SM-Saml-idp` profil techniczny, przejdź do następnego kroku. Aby uzyskać więcej informacji, zobacz temat [Zarządzanie sesjami logowania](custom-policy-reference-sso.md)jednokrotnego.

    ```xml
    <ClaimsProvider>
      <DisplayName>Session Management</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="SM-Saml-idp">
          <DisplayName>Session Management Provider</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="IncludeSessionIndex">false</Item>
            <Item Key="RegisterServiceProviders">false</Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Zapisz plik.

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]

```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="configure-an-ad-fs-relying-party-trust"></a>Konfigurowanie AD FS zaufania jednostki uzależnionej

Aby użyć AD FS jako dostawcy tożsamości w Azure AD B2C, należy utworzyć AD FS jednostki uzależnionej relacja zaufania z Azure AD B2C metadanych SAML. Poniższy przykład przedstawia adres URL metadanych SAML Azure AD B2C profilu technicznego:

```
https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/your-policy/samlp/metadata?idptp=your-technical-profile
```

Zastąp następujące wartości:

- **dzierżawy** z nazwą dzierżawy, taką jak Your-tenant.onmicrosoft.com.
- **Twoje zasady** z nazwą zasad. Na przykład B2C_1A_signup_signin_adfs.
- **Twój profil techniczny** o nazwie profilu technicznego dostawcy tożsamości SAML. Na przykład contoso-SAML2.

Otwórz przeglądarkę i przejdź do adresu URL. Upewnij się, że wpisany adres URL jest poprawny i że masz dostęp do pliku metadanych XML. Aby dodać nowe zaufanie jednostki uzależnionej za pomocą przystawki Zarządzanie AD FS i ręcznie skonfigurować ustawienia, należy wykonać poniższą procedurę na serwerze federacyjnym. Minimalnym wymaganiem do wykonania tej procedury jest członkostwo w **grupie Administratorzy** lub równoważnej na komputerze lokalnym.

1. W Menedżer serwera wybierz pozycję **Narzędzia**, a następnie wybierz pozycję **Zarządzanie AD FS**.
2. Wybierz pozycję **Dodaj zaufanie jednostki uzależnionej**.
3. Na stronie **powitalnej** wybierz pozycję **oświadczenia**, a następnie kliknij przycisk **Uruchom**.
4. Na stronie **Wybierz źródło danych** wybierz pozycję **Importuj dane dotyczące jednostki uzależnionej Publikuj online lub w sieci lokalnej**, podaj adres URL metadanych Azure AD B2C, a następnie kliknij przycisk **dalej**.
5. Na stronie **Określanie nazwy wyświetlanej** wprowadź **nazwę wyświetlaną**, w obszarze **uwagi** wprowadź opis tego zaufania jednostki uzależnionej, a następnie kliknij przycisk **dalej**.
6. Na stronie **Wybierz zasady Access Control** wybierz zasady, a następnie kliknij przycisk **dalej**.
7. Na **gotowy do dodawania zaufania** Przejrzyj ustawienia, a następnie kliknij pozycję **Dalej** zapisać swoje jednostki uzależnionej informacje zaufania.
8. Na stronie **zakończenie** kliknij przycisk **Zamknij**, ta akcja spowoduje automatyczne wyświetlenie okna dialogowego **Edytowanie reguł dotyczących roszczeń** .
9. Wybierz pozycję **Dodaj regułę**.
10. W **Szablon reguł oświadczeń**, wybierz opcję **Wyślij atrybuty LDAP jako oświadczenia**.
11. Podaj **nazwę reguły dla roszczeń**. W polu **magazyn atrybutów** wybierz pozycję **Wybierz Active Directory**, Dodaj następujące oświadczenia, a następnie kliknij przycisk **Zakończ** i **OK**.

    | Atrybut LDAP | Typ zgłoszenia wychodzącego |
    | -------------- | ------------------- |
    | User-Principal-Name | userPrincipalName |
    | Nazwisko | family_name |
    | Given-Name | given_name |
    | Adres E-mail | poczta e-mail |
    | Display-Name | name |

    Należy zauważyć, że te nazwy nie będą wyświetlane na liście rozwijanej Typ zgłoszenia wychodzącego. Należy ręcznie wpisać je w. (Lista rozwijana jest w rzeczywistości edytowalna).

12.  Na podstawie typu certyfikatu może być konieczne ustawienie algorytmu wyznaczania wartości skrótu. W oknie Właściwości zaufania jednostki uzależnionej (Demonstracja B2C) wybierz kartę **Zaawansowane** i Zmień **algorytm Secure Hash** na `SHA-256` , a następnie kliknij przycisk **OK**.
13. W Menedżer serwera wybierz pozycję **Narzędzia**, a następnie wybierz pozycję **Zarządzanie AD FS**.
14. Wybierz utworzoną relację zaufania jednostki uzależnionej, wybierz pozycję **Aktualizuj z metadanych Federacji**, a następnie kliknij przycisk **Aktualizuj**.

## <a name="test-your-custom-policy"></a>Testowanie zasad niestandardowych

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Na pasku narzędzi portalu wybierz ikonę **katalog i subskrypcję** , a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.
1. W Azure Portal Wyszukaj i wybierz pozycję **Azure AD B2C**.
1. W obszarze **zasady** wybierz pozycję **platforma obsługi tożsamości**
1. Na przykład wybierz zasady jednostek uzależnionych `B2C_1A_signup_signin` .
1. W przypadku **aplikacji** wybierz [wcześniej zarejestrowaną](tutorial-register-applications.md)aplikację sieci Web. Powinien być pokazywany **adres URL odpowiedzi** `https://jwt.ms` .
1. Wybierz przycisk **Uruchom teraz** .

Jeśli proces logowania powiedzie się, przeglądarka zostanie przekierowana do `https://jwt.ms` , która wyświetla zawartość tokenu zwróconego przez Azure AD B2C.
## <a name="troubleshooting-ad-fs-service"></a>Rozwiązywanie problemów z usługą AD FS  

AD FS jest skonfigurowany do korzystania z dziennika aplikacji systemu Windows. W przypadku wystąpienia problemów z konfigurowaniem AD FS jako dostawcy tożsamości SAML przy użyciu zasad niestandardowych w programie Azure AD B2C warto sprawdzić dziennik zdarzeń AD FS:

1. Na **pasku wyszukiwania** systemu Windows wpisz **Podgląd zdarzeń**, a następnie wybierz aplikację klasyczną **Podgląd zdarzeń** .
1. Aby wyświetlić dziennik innego komputera, kliknij prawym przyciskiem myszy **Podgląd zdarzeń (lokalne)**. Wybierz pozycję **Połącz z innym komputerem** i wypełnij pola, aby zakończyć okno dialogowe **Wybieranie komputera** .
1. W **Podgląd zdarzeń** Otwórz **Dzienniki aplikacji i usług**.
1. Wybierz pozycję **AD FS**, a następnie wybierz pozycję **administrator**. 
1. Aby wyświetlić więcej informacji o zdarzeniu, kliknij dwukrotnie zdarzenie.  

### <a name="saml-request-is-not-signed-with-expected-signature-algorithm-event"></a>Żądanie SAML nie jest podpisane przy oczekiwanym zdarzeniu algorytmu podpisywania

Ten błąd wskazuje, że żądanie SAML wysyłane przez Azure AD B2C nie jest podpisane przy użyciu oczekiwanego algorytmu podpisu skonfigurowanego w AD FS. Na przykład żądanie SAML jest podpisane przy użyciu algorytmu podpisu `rsa-sha256` , ale oczekiwano algorytmu podpisu `rsa-sha1` . Aby rozwiązać ten problem, upewnij się, że zarówno Azure AD B2C, jak i AD FS są skonfigurowane przy użyciu tego samego algorytmu podpisu.

#### <a name="option-1-set-the-signature-algorithm-in-azure-ad-b2c"></a>Opcja 1: Ustaw algorytm podpisu w Azure AD B2C  

Można skonfigurować sposób podpisywania żądania SAML w Azure AD B2C. Metadane [XmlSignatureAlgorithm](saml-identity-provider-technical-profile.md#metadata) kontrolują wartość `SigAlg` parametru (ciąg zapytania lub parametr post) w żądaniu SAML. Poniższy przykład konfiguruje Azure AD B2C, aby użyć `rsa-sha256` algorytmu podpisu.

```xml
<Metadata>
  <Item Key="WantsEncryptedAssertions">false</Item>
  <Item Key="PartnerEntity">https://your-AD-FS-domain/federationmetadata/2007-06/federationmetadata.xml</Item>
  <Item Key="XmlSignatureAlgorithm">Sha256</Item>
</Metadata>
```

#### <a name="option-2-set-the-signature-algorithm-in-ad-fs"></a>Opcja 2: Ustaw algorytm podpisu w AD FS 

Alternatywnie można skonfigurować oczekiwany algorytm podpisu żądania SAML w AD FS.

1. W Menedżer serwera wybierz pozycję **Narzędzia**, a następnie wybierz pozycję **Zarządzanie AD FS**.
1. Wybierz utworzoną wcześniej **relację zaufania jednostki uzależnionej** .
1. Wybierz pozycję **Właściwości**, a następnie wybierz pozycję **Zaawansowane** .
1. Skonfiguruj **algorytm Secure Hash** i wybierz **przycisk OK** , aby zapisać zmiany.

::: zone-end
