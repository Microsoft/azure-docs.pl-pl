---
title: Skonfiguruj konto i zaloguj się przy użyciu dostawcy tożsamości SAML
titleSuffix: Azure Active Directory B2C
description: Skonfiguruj konto i zaloguj się za pomocą dowolnego dostawcy tożsamości SAML (dostawcy tożsamości) w Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/08/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 357ea903ed4bbc87717dfefc1c542722f5bd40c0
ms.sourcegitcommit: f6193c2c6ce3b4db379c3f474fdbb40c6585553b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/08/2021
ms.locfileid: "102448408"
---
# <a name="set-up-sign-up-and-sign-in-with-saml-identity-provider-using-azure-active-directory-b2c"></a>Skonfiguruj konto i zaloguj się za pomocą dostawcy tożsamości SAML przy użyciu Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) obsługuje Federacji z dostawcami tożsamości SAML 2,0. W tym artykule pokazano, jak włączyć logowanie przy użyciu konta użytkownika dostawcy tożsamości SAML, umożliwiając użytkownikom logowanie się przy użyciu istniejących tożsamości społecznościowych lub firmowych, takich jak [ADFS](identity-provider-adfs2016-custom.md) i [Salesforce](identity-provider-salesforce-saml.md).

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="scenario-overview"></a>Omówienie scenariusza

Można skonfigurować Azure AD B2C, aby umożliwić użytkownikom logowanie się do aplikacji przy użyciu poświadczeń z zewnętrznych dostawców tożsamości (dostawcy tożsamości) usługi Social or Enterprise (SAML). Gdy Azure AD B2C tworzy federację z dostawcą tożsamości SAML, działa jako **dostawca usług** inicjujący żądanie SAML do **dostawcy tożsamości** SAML i oczekując na odpowiedź SAML. Na poniższym diagramie:

1. Aplikacja inicjuje żądanie autoryzacji w celu Azure AD B2C. Może to być aplikacja typu [OAuth 2,0](protocols-overview.md) lub [OpenID Connect Connect](openid-connect.md) lub [Dostawca usługi SAML](saml-service-provider.md). 
1. Na stronie logowania Azure AD B2C użytkownik zdecyduje się na zalogowanie się przy użyciu konta dostawcy tożsamości SAML (na przykład *contoso*). Azure AD B2C inicjuje żądanie autoryzacji SAML i przełącza użytkownika do dostawcy tożsamości SAML, aby zakończyć logowanie.
1. Dostawca tożsamości SAML zwraca odpowiedź SAML.
1. Azure AD B2C sprawdza poprawność tokenu SAML, wyodrębnia oświadczenia, wystawia swój własny token i pobiera użytkownika z powrotem do aplikacji.  

![Logowanie za pomocą przepływu dostawcy tożsamości SAML](./media/identity-provider-generic-saml/sign-in-with-saml-identity-provider-flow.png)

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="components-of-the-solution"></a>Składniki rozwiązania

W tym scenariuszu wymagane są następujące składniki:

* **Dostawca tożsamości** SAML z możliwością odbierania, dekodowania i reagowania na żądania SAML z Azure AD B2C.
* Publicznie dostępny **punkt końcowy metadanych** SAML dla dostawcy tożsamości.
* [Dzierżawa Azure AD B2C](tutorial-create-tenant.md).
 

## <a name="create-a-policy-key"></a>Tworzenie klucza zasad

Aby ustanowić relację zaufania między Azure AD B2C i dostawcą tożsamości SAML, należy podać prawidłowy certyfikat x509 z kluczem prywatnym. Azure AD B2C podpisać żądania SAML przy użyciu klucza prywatnego certyfikatu. Dostawca tożsamości sprawdza poprawność żądania przy użyciu klucza publicznego certyfikatu. Klucz publiczny jest dostępny za pomocą metadanych profilu technicznego. Alternatywnie można ręcznie przekazać plik CER do dostawcy tożsamości SAML.

Certyfikat z podpisem własnym jest akceptowalny dla większości scenariuszy. W przypadku środowisk produkcyjnych zaleca się użycie certyfikatu x509 wystawionego przez urząd certyfikacji. Ponadto, zgodnie z opisem w dalszej części tego dokumentu, dla środowiska nieprodukcyjnego można wyłączyć podpisywanie SAML po obu stronach.

### <a name="obtain-a-certificate"></a>Uzyskaj certyfikat

[!INCLUDE [active-directory-b2c-create-self-signed-certificate](../../includes/active-directory-b2c-create-self-signed-certificate.md)]

### <a name="upload-the-certificate"></a>Przekazywanie certyfikatu

Musisz przechowywać certyfikat w dzierżawie Azure AD B2C.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
1. Upewnij się, że używasz katalogu zawierającego dzierżawcę Azure AD B2C. W górnym menu wybierz pozycję **katalog i subskrypcja** , a następnie wybierz katalog, w którym znajduje się Twoja dzierżawa.
1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. Na stronie Przegląd wybierz pozycję **Struktura środowiska tożsamości**.
1. Wybierz pozycję **klucze zasad** , a następnie wybierz pozycję **Dodaj**.
1. W obszarze **Opcje** wybierz opcję `Upload` .
1. Wprowadź **nazwę** klucza zasad. Na przykład `SAMLSigningCert`. Prefiks `B2C_1A_` jest automatycznie dodawany do nazwy klucza.
1. Przejdź do pliku certyfikatu PFX z kluczem prywatnym i wybierz go.
1. Kliknij pozycję **Utwórz**.

## <a name="configure-the-saml-technical-profile"></a>Skonfiguruj profil techniczny SAML

Zdefiniuj dostawcę tożsamości SAML przez dodanie go do elementu **ClaimsProviders** w pliku rozszerzenia zasad. Dostawcy oświadczeń zawierają profil techniczny SAML, który określa punkty końcowe i protokoły, które są konieczne do komunikowania się z dostawcą tożsamości SAML. Aby dodać dostawcę oświadczeń z profilem technicznym SAML:

1. Otwórz *TrustFrameworkExtensions.xml*.
1. Znajdź element **ClaimsProviders** . Jeśli nie istnieje, Dodaj ją do elementu głównego.
1. Dodaj nową **ClaimsProvider** w następujący sposób:

    ```xml
    <ClaimsProvider>
      <Domain>Contoso.com</Domain>
      <DisplayName>Contoso</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Contoso-SAML2">
          <DisplayName>Contoso</DisplayName>
          <Description>Login with your SAML identity provider account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="PartnerEntity">https://your-AD-FS-domain/federationmetadata/2007-06/federationmetadata.xml</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="assertionSubjectName" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="first_name" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="last_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="http://schemas.microsoft.com/identity/claims/displayname" />
            <OutputClaim ClaimTypeReferenceId="email"  />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
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

Zaktualizuj następujące elementy XML przy użyciu odpowiedniej wartości:

|Element XML  |Wartość  |
|---------|---------|
|ClaimsProvider\Domain  | Nazwa domeny używana do [bezpośredniego logowania](direct-signin.md?pivots=b2c-custom-policy#redirect-sign-in-to-a-social-provider). Wprowadź nazwę domeny, która ma być używana podczas logowania bezpośredniego. Na przykład *contoso.com*. |
|TechnicalProfile\DisplayName|Ta wartość będzie wyświetlana na przycisku logowania na ekranie logowania. Na przykład *contoso*. |
|Metadata\PartnerEntity|Adres URL metadanych dostawcy tożsamości SAML. Można też skopiować metadane dostawcy tożsamości i dodać je wewnątrz elementu CDATA `<![CDATA[Your IDP metadata]]>` .|

## <a name="map-the-claims"></a>Mapowanie oświadczeń

Element **OutputClaims** zawiera listę oświadczeń zwracanych przez dostawcę tożsamości SAML. Mapuj nazwę żądania zdefiniowanego w zasadach na nazwę potwierdzenia zdefiniowaną w dostawcy tożsamości. Sprawdź dostawcę tożsamości, aby uzyskać listę oświadczeń (potwierdzenia). Aby uzyskać więcej informacji, zobacz temat [Mapowanie oświadczeń](identity-provider-generic-saml-options.md#claims-mapping).

W powyższym przykładzie *firma Contoso-SAML2* obejmuje oświadczenia zwrócone przez dostawcę tożsamości SAML:

* **IssuerUserId** jest mapowany na zastrzeżenie **assertionSubjectName** .
* **First_name** jest mapowany na **daną** wartość.
* **Last_name** jest mapowany do żądania **nazwisko** .
* Wartość **DisplayName** jest zamapowana na to `http://schemas.microsoft.com/identity/claims/displayname` zastrzeżenie.
* Bez  mapowania nazwy.

Profil techniczny zwraca również oświadczenia, które nie są zwracane przez dostawcę tożsamości:

* **IdentityProvider** , który zawiera nazwę dostawcy tożsamości.
* **AuthenticationSource** z wartością domyślną **socialIdpAuthentication**.
 
### <a name="add-the-saml-session-technical-profile"></a>Dodaj profil techniczny sesji SAML

Jeśli nie masz jeszcze `SM-Saml-idp` profilu technicznego sesji SAML, Dodaj go do zasad rozszerzenia. Znajdź `<ClaimsProviders>` sekcję i Dodaj następujący fragment kodu XML. Jeśli zasady zawierają już `SM-Saml-idp` profil techniczny, przejdź do następnego kroku. Aby uzyskać więcej informacji, zobacz temat [Zarządzanie sesjami logowania](custom-policy-reference-sso.md)jednokrotnego.

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

[!INCLUDE [active-directory-b2c-create-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="configure-your-saml-identity-provider"></a>Konfigurowanie dostawcy tożsamości SAML

Po skonfigurowaniu zasad należy skonfigurować dostawcę tożsamości SAML przy użyciu metadanych SAML Azure AD B2C. Metadane SAML są informacje używane w protokole SAML do ujawnienia konfiguracji zasad, **dostawcy usług**. Definiuje ona lokalizację usług, takich jak logowanie i wylogowywanie, certyfikaty, metoda logowania i wiele innych.

Każdy dostawca tożsamości SAML ma inne kroki do ustawienia dostawcy usług. Niektórzy dostawcy tożsamości SAML zwracają pytania dotyczące Azure AD B2C metadanych, podczas gdy inne wymagają ręcznego przechodzenia przez plik metadanych i podania informacji. Wskazówki można znaleźć w dokumentacji dostawcy tożsamości.

Poniższy przykład pokazuje adres URL metadanych SAML dla Azure AD B2C profilu technicznego:

```
https://<your-tenant-name>.b2clogin.com/<your-tenant-name>.onmicrosoft.com/<your-policy>/samlp/metadata?idptp=<your-technical-profile>
```

Zastąp następujące wartości:

- **dzierżawy** z nazwą dzierżawy, taką jak Your-tenant.onmicrosoft.com.
- **Twoje zasady** z nazwą zasad. Na przykład B2C_1A_signup_signin_adfs.
- **Twój profil techniczny** o nazwie profilu technicznego dostawcy tożsamości SAML. Na przykład contoso-SAML2.

Otwórz przeglądarkę i przejdź do adresu URL. Upewnij się, że wpisany adres URL jest poprawny i że masz dostęp do pliku metadanych XML.

## <a name="test-your-custom-policy"></a>Testowanie zasad niestandardowych

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Na pasku narzędzi portalu wybierz ikonę **katalog i subskrypcję** , a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.
1. W Azure Portal Wyszukaj i wybierz pozycję **Azure AD B2C**.
1. W obszarze **zasady** wybierz pozycję **platforma obsługi tożsamości**
1. Na przykład wybierz zasady jednostek uzależnionych `B2C_1A_signup_signin` .
1. W przypadku **aplikacji** wybierz [wcześniej zarejestrowaną](troubleshoot-custom-policies.md#troubleshoot-the-runtime)aplikację sieci Web. Powinien być pokazywany **adres URL odpowiedzi** `https://jwt.ms` .
1. Wybierz przycisk **Uruchom teraz** .
1. Na stronie rejestracji lub logowania wybierz pozycję **contoso** , aby zalogować się przy użyciu konta Contoso.

Jeśli proces logowania powiedzie się, przeglądarka zostanie przekierowana do `https://jwt.ms` , która wyświetla zawartość tokenu zwróconego przez Azure AD B2C.

## <a name="next-steps"></a>Następne kroki

- [Skonfiguruj opcje dostawcy tożsamości SAML za pomocą Azure Active Directory B2C](identity-provider-generic-saml-options.md)

::: zone-end
