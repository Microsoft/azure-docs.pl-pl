---
title: Konfigurowanie opcji dostawcy usługi SAML
title-suffix: Azure Active Directory B2C
description: Jak skonfigurować Azure Active Directory B2C opcje dostawcy usługi SAML
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/03/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: b9a491b639cd1b960ffe3b7164a0940770792148
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102107524"
---
# <a name="options-for-registering-a-saml-application-in-azure-ad-b2c"></a>Opcje rejestrowania aplikacji SAML w Azure AD B2C

W tym artykule opisano opcje konfiguracji, które są dostępne podczas łączenia Azure Active Directory (Azure AD B2C) z aplikacją SAML.

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="encrypted-saml-assertions"></a>Zaszyfrowane potwierdzenia SAML

Gdy aplikacja oczekuje, że potwierdzenia SAML mają być w szyfrowanym formacie, należy upewnić się, że szyfrowanie jest włączone w zasadach Azure AD B2C.

Azure AD B2C używa certyfikatu klucza publicznego dostawcy usługi do szyfrowania potwierdzenia SAML. Klucz publiczny musi znajdować się w punkcie końcowym metadanych aplikacji SAML z deskryptorem klucza "use" ustawionym na "Encryption", jak pokazano w następującym przykładzie:

```xml
<KeyDescriptor use="encryption">
  <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
    <X509Data>
      <X509Certificate>valid certificate</X509Certificate>
    </X509Data>
  </KeyInfo>
</KeyDescriptor>
```

Aby umożliwić Azure AD B2C wysyłania zaszyfrowanych zatwierdzeń, Ustaw element metadanych **WantsEncryptedAssertion** na wartość `true` w [profilu technicznym jednostki uzależnionej](relyingparty.md#technicalprofile). Można również skonfigurować algorytm używany do szyfrowania potwierdzenia SAML.

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="SAML2"/>
    <Metadata>
      <Item Key="WantsEncryptedAssertions">true</Item>
    </Metadata>
   ..
  </TechnicalProfile>
</RelyingParty>
```

## <a name="identity-provider-initiated-flow"></a>Przepływ inicjowany przez dostawcę tożsamości

Gdy aplikacja oczekuje na otrzymanie potwierdzenia SAML bez uprzedniego wysłania żądania SAML AuthN do dostawcy tożsamości, należy skonfigurować Azure AD B2C dla przepływu zainicjowanego przez dostawcę tożsamości.

W przepływie zainicjowanym przez dostawcę tożsamości proces logowania jest inicjowany przez dostawcę tożsamości (Azure AD B2C), co powoduje wysłanie nieżądanej odpowiedzi SAML do dostawcy usług (aplikacja jednostki uzależnionej).

Obecnie nie obsługujemy scenariuszy, w których dostawca tożsamości inicjującej jest federacyjnym zewnętrznym dostawcą tożsamości z Azure AD B2C, na przykład [AD FS](identity-provider-adfs.md)lub [Salesforce](identity-provider-salesforce-saml.md). Jest on obsługiwany tylko w przypadku Azure AD B2C uwierzytelniania konta lokalnego.

Aby włączyć przepływ inicjowany przez dostawcę tożsamości, Ustaw element metadanych **IdpInitiatedProfileEnabled** na `true` w [profilu technicznym jednostki uzależnionej](relyingparty.md#technicalprofile).

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="SAML2"/>
    <Metadata>
      <Item Key="IdpInitiatedProfileEnabled">true</Item>
    </Metadata>
   ..
  </TechnicalProfile>
</RelyingParty>
```

Aby zalogować się lub zarejestrować użytkownika za pomocą przepływu zainicjowanego przez dostawcę tożsamości, użyj następującego adresu URL:

```
https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/generic/login?EntityId=app-identifier-uri 
```

Zastąp następujące wartości:

* **Nazwa dzierżawy** z nazwą dzierżawy
* **Nazwa zasad** z nazwą zasad jednostki UZALEŻNIONej SAML
* **App-Identifier-URI** z `identifierUris` w pliku metadanych, np. `https://contoso.onmicrosoft.com/app-name`

### <a name="sample-policy"></a>Przykładowe zasady

Oferujemy kompletne przykładowe zasady, których można użyć do testowania z aplikacją testową SAML.

1. Pobierz [zasady logowania zainicjowane przez protokół SAML-Sp](https://github.com/azure-ad-b2c/saml-sp/tree/master/policy/SAML-SP-Initiated).
1. Zaktualizuj `TenantId` , aby dopasować nazwę dzierżawy, na przykład *contoso.b2clogin.com*.
1. Zachowaj nazwę zasad *B2C_1A_signup_signin_saml*.

## <a name="saml-response-signature-algorithm"></a>Algorytm podpisu odpowiedzi SAML

Można skonfigurować algorytm podpisu używany do podpisywania potwierdzenia SAML. Możliwe wartości to `Sha256` , `Sha384` , `Sha512` , lub `Sha1` . Upewnij się, że profil techniczny i aplikacja używają tego samego algorytmu podpisu. Używaj tylko algorytmu obsługiwanego przez certyfikat.

Skonfiguruj algorytm podpisu przy użyciu `XmlSignatureAlgorithm` klucza metadanych w węźle metadanych RelyingParty.

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="SAML2"/>
    <Metadata>
      <Item Key="XmlSignatureAlgorithm">Sha256</Item>
    </Metadata>
   ..
  </TechnicalProfile>
</RelyingParty>
```

## <a name="saml-response-lifetime"></a>Okres istnienia odpowiedzi SAML

Można skonfigurować czas, przez który odpowiedź SAML jest prawidłowa. Ustaw okres istnienia przy użyciu `TokenLifeTimeInSeconds` elementu metadanych w profilu technicznym wystawcy tokenów SAML. Ta wartość to liczba sekund, które mogą upłynąć od `NotBefore` sygnatury czasowej obliczonej w czasie wystawiania tokenu. Czas wybrany dla tego czasu to bieżący czas. Domyślny okres istnienia to 300 sekund (5 minut).

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <Item Key="TokenLifeTimeInSeconds">400</Item>
      </Metadata>
      ...
    </TechnicalProfile>
```

## <a name="saml-response-valid-from-skew"></a>Odpowiedź SAML jest ważna z pochylenia

Można skonfigurować pochylenie czasu stosowane do sygnatury czasowej odpowiedzi SAML `NotBefore` . Ta konfiguracja gwarantuje, że jeśli czasy między dwiema platformami nie są zsynchronizowane, potwierdzenie SAML będzie nadal uznawane za prawidłowe w tym czasie.

Ustaw pochylenie czasu przy użyciu `TokenNotBeforeSkewInSeconds` elementu metadanych w profilu technicznym wystawcy tokenów SAML. Wartość skośności jest podawana w sekundach, a wartość domyślna to 0. Wartość maksymalna to 3600 (jedna godzina).

Na przykład, gdy wartość `TokenNotBeforeSkewInSeconds` jest równa `120` sekundom:

- Token jest wystawiany o 13:05:10 czasu UTC
- Token jest prawidłowy od 13:03:10 czasu UTC

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <Item Key="TokenNotBeforeSkewInSeconds">120</Item>
      </Metadata>
      ...
    </TechnicalProfile>
```

## <a name="azure-ad-b2c-issuer-id"></a>Identyfikator wystawcy Azure AD B2C

Jeśli masz wiele aplikacji SAML, które są zależne od różnych `entityID` wartości, możesz zastąpić `issueruri` wartość w pliku jednostki uzależnionej. Aby zastąpić identyfikator URI wystawcy, skopiuj profil techniczny z IDENTYFIKATORem "Saml2AssertionIssuer" z pliku podstawowego i Zastąp `issueruri` wartość.

> [!TIP]
> Skopiuj `<ClaimsProviders>` sekcję z podstawy i Zachowaj te elementy w ramach dostawcy oświadczeń: `<DisplayName>Token Issuer</DisplayName>` , `<TechnicalProfile Id="Saml2AssertionIssuer">` i `<DisplayName>Token Issuer</DisplayName>` .
 
Przykład:

```xml
   <ClaimsProviders>   
    <ClaimsProvider>
      <DisplayName>Token Issuer</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Saml2AssertionIssuer">
          <DisplayName>Token Issuer</DisplayName>
          <Metadata>
            <Item Key="IssuerUri">customURI</Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
  </ClaimsProviders>
  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpInSAML" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="SAML2" />
      <Metadata>
     …
```

## <a name="session-management"></a>Zarządzanie sesjami

Sesją programu można zarządzać między Azure AD B2C a aplikacją jednostki uzależnionej SAML przy użyciu `UseTechnicalProfileForSessionManagement` elementu i [SamlSSOSessionProvider](custom-policy-reference-sso.md#samlssosessionprovider).

## <a name="debug-the-saml-protocol"></a>Debugowanie protokołu SAML

Aby pomóc w konfigurowaniu i debugowaniu integracji z dostawcą usług, można użyć rozszerzenia przeglądarki dla protokołu SAML, na przykład [rozszerzenia SAML devtools](https://chrome.google.com/webstore/detail/saml-devtools-extension/jndllhgbinhiiddokbeoeepbppdnhhio) dla programu Chrome, języka [SAML-Tracer](https://addons.mozilla.org/es/firefox/addon/saml-tracer/) dla przeglądarki Firefox lub [narzędzi programistycznych przeglądarki Edge lub IE](https://techcommunity.microsoft.com/t5/microsoft-sharepoint-blog/gathering-a-saml-token-using-edge-or-ie-developer-tools/ba-p/320957).

Korzystając z tych narzędzi, można sprawdzić integrację aplikacji i Azure AD B2C. Na przykład:

* Sprawdź, czy żądanie SAML zawiera podpis i określ algorytm używany do logowania żądania autoryzacji.
* Sprawdź, czy Azure AD B2C zwraca komunikat o błędzie.
* Sprawdź, czy sekcja potwierdzenie jest zaszyfrowana.

## <a name="next-steps"></a>Następne kroki

- Więcej informacji [na temat protokołu SAML znajduje się w witrynie sieci Web języka Oasis](https://www.oasis-open.org/).
- Pobierz aplikację sieci Web test SAML z [repozytorium społeczności usługi GitHub Azure AD B2C](https://github.com/azure-ad-b2c/saml-sp-tester).

<!-- LINKS - External -->
[samltest]: https://aka.ms/samltestapp

::: zone-end
