---
title: Dodawanie usługi ADFS jako dostawcy tożsamości SAML przy użyciu zasad niestandardowych
titleSuffix: Azure AD B2C
description: Konfigurowanie usługi ADFS 2016 przy użyciu protokołu SAML i zasad niestandardowych w usłudze Azure Active Directory B2C
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/27/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 000f63ef5f73e77eb22fb539fc6736b929ac6bcc
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81451571"
---
# <a name="add-adfs-as-a-saml-identity-provider-using-custom-policies-in-azure-active-directory-b2c"></a>Dodawanie usługi ADFS jako dostawcy tożsamości SAML przy użyciu zasad niestandardowych w usłudze Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

W tym artykule pokazano, jak włączyć logowanie się do konta użytkownika usługi ADFS przy użyciu [zasad niestandardowych](custom-policy-overview.md) w usłudze Azure Active Directory B2C (Azure AD B2C). Zaloguj się, dodając [profil techniczny SAML](saml-technical-profile.md) do zasad niestandardowych.

## <a name="prerequisites"></a>Wymagania wstępne

- Wykonaj kroki opisane w [wprowadzenie do niestandardowych zasad w usłudze Azure Active Directory B2C](custom-policy-get-started.md).
- Upewnij się, że masz dostęp do pliku .pfx certyfikatu z kluczem prywatnym. Możesz wygenerować własny podpisany certyfikat i przekazać go do usługi Azure AD B2C. Usługa Azure AD B2C używa tego certyfikatu do podpisania żądania SAML wysłanego do dostawcy tożsamości SAML.
- Aby platforma Azure zaakceptowała hasło pliku .pfx, hasło musi być zaszyfrowane za pomocą opcji TripleDES-SHA1 w narzędziu Eksportu magazynu certyfikatów systemu Windows, w przeciwieństwie do narzędzia AES256-SHA256.

## <a name="create-a-policy-key"></a>Tworzenie klucza zasad

Należy przechowywać certyfikat w dzierżawie usługi Azure AD B2C.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Upewnij się, że używasz katalogu, który zawiera dzierżawę usługi Azure AD B2C. Wybierz filtr **Katalog + subskrypcja** w górnym menu i wybierz katalog zawierający dzierżawę.
3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Na stronie Przegląd wybierz pozycję **Identity Experience Framework**.
5. Wybierz pozycję **Klawisze zasad,** a następnie wybierz pozycję **Dodaj**.
6. W **Options**przypadku `Upload`opcji wybierz opcję .
7. Wprowadź **nazwę** klucza zasad. Na przykład `SamlCert`. Prefiks `B2C_1A_` zostanie automatycznie dodany do nazwy klucza.
8. Przejdź do pliku .pfx certyfikatu i wybierz go za pomocą klucza prywatnego.
9. Kliknij przycisk **Utwórz**.

## <a name="add-a-claims-provider"></a>Dodawanie dostawcy oświadczeń

Jeśli chcesz, aby użytkownicy logowali się przy użyciu konta usługi ADFS, musisz zdefiniować konto jako dostawcę oświadczeń, z którego usługa Azure AD B2C może komunikować się za pośrednictwem punktu końcowego. Punkt końcowy zawiera zestaw oświadczeń, które są używane przez usługę Azure AD B2C, aby sprawdzić, czy określony użytkownik uwierzytelnił.

Konto usługi ADFS można zdefiniować jako dostawcę oświadczeń, dodając je do elementu **ClaimsProviders** w pliku rozszerzenia zasad. Aby uzyskać więcej informacji, zobacz [definiowanie profilu technicznego SAML](saml-technical-profile.md).

1. Otwórz *plik TrustFrameworkExtensions.xml*.
1. Znajdź **ClaimsProviders** element. Jeśli nie istnieje, dodaj go w elemencie głównym.
1. Dodaj nowy **ClaimsProvider** w następujący sposób:

    ```xml
    <ClaimsProvider>
      <Domain>contoso.com</Domain>
      <DisplayName>Contoso ADFS</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Contoso-SAML2">
          <DisplayName>Contoso ADFS</DisplayName>
          <Description>Login with your ADFS account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="PartnerEntity">https://your-ADFS-domain/federationmetadata/2007-06/federationmetadata.xml</Item>
            <Item Key="XmlSignatureAlgorithm">Sha256</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_ADFSSamlCert"/>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_ADFSSamlCert"/>
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

1. Zastąp `your-ADFS-domain` nazwą domeny usługi ADFS i zastąp wartość oświadczenia wyjściowego **tożsamościza pomocą** dns (dowolna wartość wskazująca domenę).

1. Znajdź `<ClaimsProviders>` sekcję i dodaj następujący fragment kodu XML. Jeśli zasady zawierają `SM-Saml-idp` już profil techniczny, przejdź do następnego kroku. Aby uzyskać więcej informacji, zobacz [zarządzanie sesjami logowania jednokrotnego](custom-policy-reference-sso.md).

    ```XML
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

### <a name="upload-the-extension-file-for-verification"></a>Prześlij plik rozszerzenia w celu weryfikacji

Do tej pory skonfigurowano zasady, dzięki czemu usługa Azure AD B2C wie, jak komunikować się z kontem usługi ADFS. Spróbuj przesłać plik rozszerzenia zasad, aby potwierdzić, że do tej pory nie ma żadnych problemów.

1. Na stronie **Zasady niestandardowe** w dzierżawie usługi Azure AD B2C wybierz pozycję **Przekaż zasady**.
2. Włącz **zastępowanie zasad, jeśli istnieje**, a następnie przejdź do pliku *TrustFrameworkExtensions.xml.*
3. Kliknij pozycję **Przekaż**.

> [!NOTE]
> Rozszerzenie kodu programu Visual Studio B2C używa "socialIdpUserId". Polityka społeczna jest również wymagana dla ADFS.
>

## <a name="register-the-claims-provider"></a>Zarejestruj dostawcę oświadczeń

W tym momencie dostawca tożsamości został skonfigurowany, ale nie jest dostępny na żadnym z ekranów rejestracji lub logowania. Aby go udostępnić, należy utworzyć duplikat istniejącej podróży użytkownika szablonu, a następnie zmodyfikować go tak, aby miał również dostawcę tożsamości usługi ADFS.

1. Otwórz plik *TrustFrameworkBase.xml* z pakietu startowego.
2. Znajdź i skopiuj całą zawartość elementu `Id="SignUpOrSignIn"` **UserJourney,** który zawiera .
3. Otwórz *trustFrameworkExtensions.xml* i znajdź **UserJourneys** element. Jeśli element nie istnieje, dodaj jeden.
4. Wklej całą zawartość **elementu UserJourney,** który został skopiowany jako element podrzędny elementu **UserJourneys.**
5. Zmień nazwę identyfikatora podróży użytkownika. Na przykład `SignUpSignInADFS`.

### <a name="display-the-button"></a>Wyświetl przycisk

**Element ClaimsProviderSelection** jest analogiczny do przycisku dostawcy tożsamości na ekranie rejestracji lub logowania. Jeśli dodasz element **ClaimsProviderSelection** dla konta usługi ADFS, nowy przycisk pojawi się, gdy użytkownik wyląduje na stronie.

1. Znajdź **OrchestrationStep** element, `Order="1"` który zawiera w podróży użytkownika, który został utworzony.
2. W obszarze **ClaimsProviderSelections**dodaj następujący element. Ustaw wartość **TargetClaimsExchangeId** na odpowiednią wartość, `ContosoExchange`na przykład:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Łączenie przycisku z akcją

Teraz, gdy masz przycisk w miejscu, musisz połączyć go z działaniem. Akcja, w tym przypadku, jest dla usługi Azure AD B2C do komunikowania się z kontem usługi ADFS, aby otrzymać token.

1. Znajdź **OrchestrationStep,** `Order="2"` który zawiera w podróży użytkownika.
2. Dodaj następujący element **ClaimsExchange** upewniając się, że używasz tej samej wartości dla identyfikatora, który był używany dla **obiektu TargetClaimsExchangeId:**

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
    ```

    Zaktualizuj wartość **TechnicalProfileReferenceId** do identyfikatora profilu technicznego utworzonego wcześniej. Na przykład `Contoso-SAML2`.

3. Zapisz plik *TrustFrameworkExtensions.xml* i przekaż go ponownie w celu weryfikacji.


## <a name="configure-an-adfs-relying-party-trust"></a>Konfigurowanie zaufania jednostki uzależniającej usługi ADFS

Aby używać usługi ADFS jako dostawcy tożsamości w usłudze Azure AD B2C, należy utworzyć zaufanie jednostki uzależniającej usługi ADFS z metadanymi SAML usługi Azure AD B2C. W poniższym przykładzie pokazano adres URL do metadanych SAML profilu technicznego usługi Azure AD B2C:

```
https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/your-policy/samlp/metadata?idptp=your-technical-profile
```

Zastąp następujące wartości:

- **dzierżawy** z nazwą dzierżawy, takich jak your-tenant.onmicrosoft.com.
- **z** nazwą zasad. Na przykład B2C_1A_signup_signin_adfs.
- **twój profil techniczny** z nazwą profilu technicznego dostawcy tożsamości SAML. Na przykład Contoso-SAML2.

Otwórz przeglądarkę i przejdź do adresu URL. Upewnij się, że wpisujesz poprawny adres URL i że masz dostęp do pliku metadanych XML. Aby dodać nową jednostkę uzależniającą zaufania przy użyciu przystawki Zarządzanie usługą ADFS i ręcznie skonfigurować ustawienia, wykonaj następującą procedurę na serwerze federacyjnym. Członkostwo w **administratorach** lub równoważne na komputerze lokalnym jest minimum wymagane do wykonania tej procedury.

1. W Menedżerze serwera wybierz pozycję **Narzędzia**, a następnie wybierz pozycję **Zarządzanie usługą ADFS**.
2. Wybierz **pozycję Dodaj zaufanie jednostki uzależniającej**.
3. Na stronie **Powitanie** wybierz pozycję **Świadomość oświadczeń,** a następnie kliknij przycisk **Start**.
4. Na stronie **Wybierz źródło danych** wybierz pozycję **Importuj dane dotyczące strony uzależniającej publikowania w trybie online lub w sieci lokalnej**, podaj adres URL metadanych usługi Azure AD B2C, a następnie kliknij przycisk **Dalej**.
5. Na stronie **Określanie nazwy wyświetlanej** wprowadź **nazwę wyświetlaną**w obszarze **Notatki**wprowadź opis tego zaufania jednostki uzależniającej, a następnie kliknij przycisk **Dalej**.
6. Na stronie **Wybieranie zasad kontroli dostępu** wybierz zasadę, a następnie kliknij przycisk **Dalej**.
7. Na **gotowy do dodawania zaufania** Przejrzyj ustawienia, a następnie kliknij pozycję **Dalej** zapisać swoje jednostki uzależnionej informacje zaufania.
8. Na stronie **Zakończenie** kliknij pozycję **Zamknij**, w tej akcji automatycznie jest wyświetlane okno dialogowe **Edytowanie reguł oświadczeń.**
9. Wybierz **pozycję Dodaj regułę**.
10. W **szablonie reguły oświadczeń**wybierz pozycję **Wyślij atrybuty LDAP jako oświadczenia**.
11. Podaj **nazwę reguły oświadczenia**. W **przypadku magazynu atrybutów**wybierz **pozycję Wybierz usługa Active Directory**, dodaj następujące oświadczenia, a następnie kliknij przycisk **Zakończ** i **OK**.

    | Atrybut LDAP | Typ oświadczenia wychodzącego |
    | -------------- | ------------------- |
    | User-Principal-Name | userPrincipalName |
    | Nazwisko | family_name |
    | Imię i nazwisko | given_name |
    | Adres e-mail | email |
    | Nazwa wyświetlana | name |

    Należy zauważyć, że te nazwy nie będą wyświetlane w menu rozwijanym typu oświadczenia wychodzącego. Należy je ręcznie wpisać. (Rozwijana jest faktycznie edytowalna).

12.  Na podstawie typu certyfikatu może być konieczne ustawienie algorytmu HASH. W oknie właściwości zaufania jednostki uzależniającej (B2C Demo) wybierz kartę **Zaawansowane** i zmień **algorytm bezpiecznego mieszania** na `SHA-256`, a następnie kliknij przycisk **Ok**.
13. W Menedżerze serwera wybierz pozycję **Narzędzia**, a następnie wybierz pozycję **Zarządzanie usługą ADFS**.
14. Wybierz utworzone zaufanie jednostki uzależniającej, wybierz **pozycję Aktualizuj z metadanych federacji,** a następnie kliknij przycisk **Aktualizuj**.

## <a name="create-an-azure-ad-b2c-application"></a>Tworzenie aplikacji usługi Azure AD B2C

Komunikacja z usługą Azure AD B2C odbywa się za pośrednictwem aplikacji, która rejestrujesz się w dzierżawie B2C. W tej sekcji wymieniono opcjonalne kroki, które można wykonać, aby utworzyć aplikację testową, jeśli jeszcze tego nie zrobiono.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

### <a name="update-and-test-the-relying-party-file"></a>Aktualizowanie i testowanie pliku jednostki uzależnianej

Zaktualizuj plik jednostki uzależniającej (RP), który inicjuje proces użytkownika, który został utworzony.

1. Zrób kopię *pliku SignUpOrSignIn.xml* w katalogu roboczym i zmień jego nazwę. Na przykład zmień jego nazwę na *SignUpSignInADFS.xml*.
2. Otwórz nowy plik i zaktualizuj wartość atrybutu **PolicyId** dla **TrustFrameworkPolicy** z unikatową wartością. Na przykład `SignUpSignInADFS`.
3. Zaktualizuj wartość **PublicPolicyUri** za pomocą identyfikatora URI dla zasad. Na przykład,`http://contoso.com/B2C_1A_signup_signin_adfs`
4. Zaktualizuj wartość atrybutu **ReferenceId** w **defaultuserJourney,** aby dopasować identyfikator nowego środowiska podróży użytkownika, który został utworzony (SignUpSignInADFS).
5. Zapisz zmiany, przekaż plik, a następnie wybierz nowe zasady na liście.
6. Upewnij się, że utworzona aplikacja usługi Azure AD B2C jest zaznaczona w polu **Wybierz aplikację,** a następnie przetestuj ją, klikając przycisk **Uruchom teraz**.

