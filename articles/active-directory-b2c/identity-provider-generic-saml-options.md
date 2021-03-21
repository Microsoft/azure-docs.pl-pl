---
title: Ustaw Logowanie przy użyciu opcji dostawcy tożsamości SAML
titleSuffix: Azure Active Directory B2C
description: Skonfiguruj opcje logowania dostawcy tożsamości SAML (dostawcy tożsamości) w Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/15/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 43c57950d317de42df666ddd25cbcb2e9a4c9611
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103488877"
---
# <a name="configure-saml-identity-provider-options-with-azure-active-directory-b2c"></a>Skonfiguruj opcje dostawcy tożsamości SAML za pomocą Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) obsługuje Federacji z dostawcami tożsamości SAML 2,0. W tym artykule opisano opcje konfiguracji, które są dostępne podczas włączania logowania za pomocą dostawcy tożsamości SAML.

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="claims-mapping"></a>Mapowanie oświadczeń

Element **OutputClaims** zawiera listę oświadczeń zwracanych przez dostawcę tożsamości SAML. Należy zmapować nazwę żądania zdefiniowanego w zasadach na nazwę zdefiniowaną w dostawcy tożsamości. Sprawdź dostawcę tożsamości, aby uzyskać listę oświadczeń (potwierdzenia). Możesz również sprawdzić zawartość odpowiedzi SAML zwracanej przez dostawcę tożsamości. Aby uzyskać więcej informacji, zobacz [debugowanie komunikatów SAML](#debug-saml-protocol). Aby dodać oświadczenie, najpierw [Zdefiniuj oświadczenie](claimsschema.md), a następnie Dodaj oświadczenie do kolekcji oświadczeń wychodzących.

Można również uwzględnić oświadczenia, które nie są zwracane przez dostawcę tożsamości, o ile atrybut ten jest ustawiony `DefaultValue` . Wartość domyślna może być statyczna lub dynamiczna przy użyciu [oświadczeń kontekstu](#enable-use-of-context-claims).

Element Claims Output zawiera następujące atrybuty:

- **ClaimTypeReferenceId** jest odwołaniem do typu zgłoszenia. 
- **PartnerClaimType** jest nazwą właściwości, która wyświetla potwierdzenie SAML. 
- **DefaultValue** jest wstępnie zdefiniowaną wartością domyślną. Jeśli to zgłoszenie jest puste, zostanie użyta wartość domyślna. Można również użyć [resolverów roszczeń](claim-resolver-overview.md) z wartością kontekstową, taką jak identyfikator korelacji lub adres IP użytkownika.

### <a name="subject-name"></a>Nazwa podmiotu

Aby odczytać **NameID** potwierdzenia SAML w **podmiotu** jako znormalizowana, ustaw wartość atrybutu **PartnerClaimType** w `SPNameQualifier` atrybucie. Jeśli `SPNameQualifier` atrybut nie jest prezentowany, ustaw wartość atrybutu **PartnerClaimType** na Value `NameQualifier` .

Potwierdzenie SAML:

```xml
<saml:Subject>
  <saml:NameID SPNameQualifier="http://your-idp.com/unique-identifier" Format="urn:oasis:names:tc:SAML:2.0:nameid-format:transient">david@contoso.com</saml:NameID>
  <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
    <SubjectConfirmationData InResponseTo="_cd37c3f2-6875-4308-a9db-ce2cf187f4d1" NotOnOrAfter="2020-02-15T16:23:23.137Z" Recipient="https://<your-tenant>.b2clogin.com/<your-tenant>.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer" />
    </SubjectConfirmation>
  </saml:SubjectConfirmation>
</saml:Subject>
```

Zgłoszenie wyjściowe:

```xml
<OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="http://your-idp.com/unique-identifier" />
```

Jeśli `SPNameQualifier` `NameQualifier` w potwierdzeniu SAML nie są podane oba lub atrybuty, ustaw wartość **PartnerClaimType** dla tego elementu `assertionSubjectName` . Upewnij się, że **NameID** jest pierwszą wartością w pliku XML potwierdzenia. Podczas definiowania więcej niż jednego potwierdzenia, Azure AD B2C wybiera wartość podmiotu z ostatniego potwierdzenia.


## <a name="configure-saml-protocol-bindings"></a>Konfigurowanie powiązań protokołu SAML

Żądania SAML są wysyłane do dostawcy tożsamości, jak określono w elemencie metadanych dostawcy tożsamości `SingleSignOnService` . Większość żądań autoryzacji dostawców tożsamości odbywa się bezpośrednio w ciągu zapytania adresu URL w żądaniu HTTP GET (ponieważ komunikaty są stosunkowo krótkie). Zapoznaj się z dokumentacją dostawcy tożsamości, aby uzyskać informacje na temat konfigurowania powiązań dla obu żądań SAML.

Poniżej znajduje się przykład usługi logowania jednokrotnego w usłudze Azure AD z dwoma powiązaniami. `HTTP-Redirect`Ma pierwszeństwo przed, `HTTP-POST` ponieważ pojawia się najpierw w metadanych dostawcy tożsamości SAML.

```xml
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
  ...
  <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000/saml2"/>
  <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST" Location="https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000/saml2"/>
</IDPSSODescriptor>
```

Odpowiedzi SAML są przesyłane do Azure AD B2C za pośrednictwem powiązań POST protokołu HTTP. Metadane zasad Azure AD B2C są ustawiane `AssertionConsumerService` dla powiązania `urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST` .

Poniżej znajduje się przykład Assertion Consumer Service elementu metadanych zasad Azure AD B2C.

```xml
<SPSSODescriptor AuthnRequestsSigned="true" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
  ...
  <AssertionConsumerService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST" Location="https://your-tenant.b2clogin.com/your-tenant/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer" index="0" isDefault="true"/>
</SPSSODescriptor>
```

## <a name="configure-the-saml-request-signature"></a>Konfigurowanie podpisu żądania SAML

Azure AD B2C podpisać wszystkie wychodzące żądania uwierzytelniania przy użyciu klucza kryptograficznego **SamlMessageSigning** . Aby wyłączyć sygnaturę żądania SAML, ustaw wartość **WantsSignedRequests** na `false` . Jeśli metadane są ustawione na `false` , parametry **SigAlg** i **Signature** (ciąg zapytania lub parametr post) są pomijane w żądaniu.

Te metadane również kontrolują atrybut **AuthnRequestsSigned** , który jest dołączony do metadanych profilu technicznego Azure AD B2C, który jest udostępniony dostawcy tożsamości. Azure AD B2C nie podpisać żądania, jeśli wartość **WantsSignedRequests** w metadanych profilu technicznego jest ustawiona na `false` , a **WantAuthnRequestsSigned** metadanych dostawcy tożsamości jest ustawiona na `false` lub nie została określona.

Poniższy przykład usuwa sygnaturę z żądania SAML.

```xml
<Metadata>
  ...
  <Item Key="WantsSignedRequests">false</Item>
</Metadata>
```

### <a name="signature-algorithm"></a>Algorytm podpisu

Azure AD B2C używa `Sha1` do podpisywania żądania SAML. Użyj metadanych **XmlSignatureAlgorithm** , aby skonfigurować algorytm do użycia. Możliwe wartości to `Sha256` , `Sha384` , `Sha512` , lub `Sha1` (domyślne). To metadane określa wartość parametru  **SigAlg** (ciąg zapytania lub parametr post) w żądaniu SAML. Upewnij się, że algorytm podpisu został skonfigurowany na obu stronach z tą samą wartością. Używaj tylko algorytmu obsługiwanego przez certyfikat.

### <a name="include-key-info"></a>Dołącz informacje o kluczu

Gdy dostawca tożsamości wskazuje, że dla powiązania Azure AD B2C jest ustawiona wartość `HTTP-POST` , Azure AD B2C obejmuje sygnaturę i algorytm w treści żądania SAML. Możesz również skonfigurować usługę Azure AD w taki sposób, aby zawierała klucz publiczny certyfikatu, gdy powiązanie jest ustawione na `HTTP-POST` . Użyj metadanych **IncludeKeyInfo** do `true` , lub `false` . W poniższym przykładzie usługa Azure AD nie będzie uwzględniać klucza publicznego certyfikatu.

```xml
<Metadata>
  ...
  <Item Key="IncludeKeyInfo">false</Item>
</Metadata>
```

## <a name="configure-saml-request-name-id"></a>Skonfiguruj identyfikator nazwy żądania SAML

Element żądania autoryzacji SAML `<NameID>` wskazuje format identyfikatora nazwy SAML. W tej sekcji opisano konfigurację domyślną i sposób dostosowywania elementu ID nazwy.

## <a name="preferred-username"></a>Preferowana nazwa użytkownika

Podczas podróży użytkownika w trakcie logowania aplikacja jednostki uzależnionej może kierować określonego użytkownika. Azure AD B2C umożliwia wysłanie preferowanej nazwy użytkownika do dostawcy tożsamości SAML. Element **InputClaims** jest używany do wysyłania **NameID** **w ramach żądania** autoryzacji SAML.

Aby uwzględnić identyfikator nazwy podmiotu w żądaniu autoryzacji, Dodaj następujący `<InputClaims>` element bezpośrednio po `<CryptographicKeys>` . Wartość **PartnerClaimType** musi być ustawiona na wartość `subject` .

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="signInName" PartnerClaimType="subject" />
</InputClaims>
```

W tym przykładzie Azure AD B2C wysyła wartość żądania **signInName** z **NameID** **w ramach żądania** autoryzacji SAML.

```xml
<samlp:AuthnRequest ... >
  ...
  <saml:Subject>
    <saml:NameID>sam@contoso.com</saml:NameID>
  </saml:Subject>
</samlp:AuthnRequest>
```

Możesz użyć [oświadczeń kontekstu](claim-resolver-overview.md), takich jak `{OIDC:LoginHint}` Aby wypełnić wartość oświadczenia.

```xml
<Metadata>
  ...
  <Item Key="IncludeClaimResolvingInClaimsHandling">true</Item>
</Metadata>
  ...
<InputClaims>
  <InputClaim ClaimTypeReferenceId="signInName" PartnerClaimType="subject" DefaultValue="{OIDC:LoginHint}" AlwaysUseDefaultValue="true" />
</InputClaims>
```

### <a name="name-id-policy-format"></a>Format nazwy zasad identyfikatora

Domyślnie żądanie autoryzacji SAML określa `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` zasady. Oznacza to, że można użyć dowolnego typu identyfikatora obsługiwanego przez dostawcę tożsamości dla żądanego tematu.

Aby zmienić to zachowanie, zapoznaj się z dokumentacją dostawcy tożsamości, aby uzyskać wskazówki dotyczące tego, które zasady identyfikatorów nazw są obsługiwane. Następnie Dodaj `NameIdPolicyFormat` metadane w odpowiednim formacie zasad. Na przykład:

```xml
<Metadata>
  ...
  <Item Key="NameIdPolicyFormat">urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress</Item>
</Metadata>
```

Poniższe żądanie autoryzacji SAML zawiera zasady identyfikatora nazwy.

```xml
<samlp:AuthnRequest ... >
  ...
  <samlp:NameIDPolicy Format="urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress" />
</samlp:AuthnRequest>
```

### <a name="allow-creating-new-accounts"></a>Zezwalaj na tworzenie nowych kont

Jeśli określisz [Format nazwy zasad identyfikatora](#name-id-policy-format), można również określić `AllowCreate` Właściwość **NameIDPolicy** , aby wskazać, czy dostawca tożsamości może utworzyć nowe konto w ramach przepływu logowania. Wskazówki można znaleźć w dokumentacji dostawcy tożsamości.

Azure AD B2C `AllowCreate` Domyślnie pomija właściwość. To zachowanie można zmienić przy użyciu `NameIdPolicyAllowCreate` metadanych. Wartość tych metadanych to `true` lub `false` .

W poniższym przykładzie pokazano, jak ustawić `AllowCreate` Właściwość `NameIDPolicy` na `true` .

```xml
<Metadata>
  ...
  <Item Key="NameIdPolicyFormat">urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress</Item>
  <Item Key="NameIdPolicyAllowCreate">true</Item>
</Metadata>
```


Poniższy przykład ilustruje żądanie autoryzacji z **AllowCreate** elementu **NameIDPolicy** w żądaniu autoryzacji.


```xml
<samlp:AuthnRequest ... >
  ...
  <samlp:NameIDPolicy 
      Format="urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress" 
      AllowCreate="true" />
</samlp:AuthnRequest>
```

### <a name="include-authentication-context-class-references"></a>Uwzględnij odwołania do klas kontekstu uwierzytelniania

Żądanie autoryzacji SAML może zawierać element **AuthnContext** , który określa kontekst żądania autoryzacji. Element może zawierać odwołanie do klasy kontekstu uwierzytelniania, które informuje dostawcę tożsamości SAML o mechanizmie uwierzytelniania, który ma być obecny dla użytkownika.

Aby skonfigurować odwołania do klas kontekstu uwierzytelniania, Dodaj metadane **IncludeAuthnContextClassReferences** . W polu wartość określ co najmniej jeden identyfikator URI identyfikujący klasy kontekstu uwierzytelniania. Określ wiele identyfikatorów URI jako listę rozdzielaną przecinkami. Zapoznaj się z dokumentacją dostawcy tożsamości w celu uzyskania wskazówek dotyczących obsługiwanych identyfikatorów URI **AuthnContextClassRef** .

Poniższy przykład umożliwia użytkownikom zalogowanie się przy użyciu nazwy użytkownika i hasła oraz zalogowanie się przy użyciu nazwy użytkownika i hasła w chronionej sesji (SSL/TLS).

```xml
<Metadata>
  ...
  <Item Key="IncludeAuthnContextClassReferences">urn:oasis:names:tc:SAML:2.0:ac:classes:Password,urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport</Item>
</Metadata>
```

Poniższe żądanie autoryzacji SAML zawiera odwołania do klas kontekstu uwierzytelniania.

```xml
<samlp:AuthnRequest ... >
  ...
  <samlp:RequestedAuthnContext>
    <saml:AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:Password</saml:AuthnContextClassRef>
    <saml:AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport</saml:AuthnContextClassRef>
  </samlp:RequestedAuthnContext>
</samlp:AuthnRequest>
```

## <a name="include-custom-data-in-the-authorization-request"></a>Dołącz dane niestandardowe do żądania autoryzacji

Opcjonalnie możesz dołączyć elementy rozszerzeń komunikatów protokołu, które są uzgodnione przez usługę Azure AD BC i dostawcę tożsamości. Rozszerzenie jest prezentowane w formacie XML. Elementy rozszerzeń można dołączać, dodając dane XML wewnątrz elementu CDATA `<![CDATA[Your IDP metadata]]>` . Sprawdź dokumentację dostawcy tożsamości, aby sprawdzić, czy element rozszerzeń jest obsługiwany.

Poniższy przykład ilustruje użycie danych rozszerzenia:

```xml
<Metadata>
  ...
  <Item Key="AuthenticationRequestExtensions"><![CDATA[
            <ext:MyCustom xmlns:ext="urn:ext:custom">
              <ext:AssuranceLevel>1</ext:AssuranceLevel>
              <ext:AssuranceDescription>Identity verified to level 1.</ext:AssuranceDescription>
            </ext:MyCustom>]]></Item>
</Metadata>
```

W przypadku korzystania z rozszerzenia komunikatu protokołu SAML odpowiedź SAML będzie wyglądać podobnie do poniższego przykładu:

```xml
<samlp:AuthnRequest ... >
  ...
  <samlp:Extensions>
    <ext:MyCustom xmlns:ext="urn:ext:custom">
      <ext:AssuranceLevel>1</ext:AssuranceLevel>
      <ext:AssuranceDescription>Identity verified to level 1.</ext:AssuranceDescription>
    </ext:MyCustom>
  </samlp:Extensions>
</samlp:AuthnRequest>
```

## <a name="require-signed-saml-responses"></a>Wymagaj podpisanych odpowiedzi SAML

Azure AD B2C wymaga podpisania wszystkich zatwierdzeń przychodzących. Aby usunąć to wymaganie, należy ustawić **WantsSignedAssertions** na `false` . Dostawca tożsamości nie powinien podpisać potwierdzeń w tym przypadku, ale nawet wtedy, gdy jest to możliwe, Azure AD B2C nie sprawdza poprawności sygnatury.

Metadane **WantsSignedAssertions** kontrolują flagę metadanych SAML **WantAssertionsSigned**, która jest zawarta w metadanych profilu technicznego Azure AD B2C, który jest udostępniany dostawcy tożsamości.

```xml
<SPSSODescriptor AuthnRequestsSigned="true" WantAssertionsSigned="true" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
```

Jeśli wyłączysz weryfikację potwierdzeń, możesz również wyłączyć weryfikację podpisu komunikatu odpowiedzi. Ustaw metadane **ResponsesSigned** `false` . Dostawca tożsamości nie powinien podpisać komunikatu odpowiedzi SAML w tym przypadku, ale nawet jeśli tak, Azure AD B2C nie sprawdza podpisu.

Poniższy przykład usuwa komunikat i podpis potwierdzenia:

```xml
<Metadata>
  ...
  <Item Key="WantsSignedAssertions">false</Item>
  <Item Key="ResponsesSigned">false</Item>
</Metadata>
```

## <a name="require-encrypted-saml-responses"></a>Wymagaj szyfrowanych odpowiedzi SAML

Aby wymagać szyfrowania wszystkich zatwierdzeń przychodzących, należy ustawić metadane **WantsEncryptedAssertions** . Gdy wymagane jest szyfrowanie, dostawca tożsamości używa klucza publicznego certyfikatu szyfrowania w profilu technicznym Azure AD B2C. Azure AD B2C odszyfrowuje potwierdzenie odpowiedzi przy użyciu prywatnej części certyfikatu szyfrowania.

Jeśli włączysz szyfrowanie potwierdzeń, może być również konieczne wyłączenie walidacji podpisu odpowiedzi (Aby uzyskać więcej informacji, zobacz [Wymagaj podpisanych odpowiedzi SAML](#require-signed-saml-responses).

Gdy metadane **WantsEncryptedAssertions** są ustawione na `true` , metadane profilu technicznego Azure AD B2C zawierają sekcję **szyfrowanie** . Dostawca tożsamości odczytuje metadane i szyfruje potwierdzenie odpowiedzi SAML przy użyciu klucza publicznego dostarczonego w metadanych profilu technicznego Azure AD B2C.

Poniższy przykład przedstawia sekcję deskryptora klucza metadanych SAML używanych do szyfrowania:

```xml
<SPSSODescriptor AuthnRequestsSigned="true"  protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
  <KeyDescriptor use="encryption">
    <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
      <X509Data>
        <X509Certificate>valid certificate</X509Certificate>
      </X509Data>
    </KeyInfo>
  </KeyDescriptor>
  ...
</SPSSODescriptor>
```

Aby zaszyfrować potwierdzenie odpowiedzi SAML:

1. [Utwórz klucz zasad](identity-provider-generic-saml.md#create-a-policy-key) z unikatowym identyfikatorem. Na przykład `B2C_1A_SAMLEncryptionCert`.
2. W kolekcji **CryptographicKeys** z profilem technicznym SAML. Ustaw **identyfikatorze storagereferenceid** na nazwę klucza zasad utworzonego w pierwszym kroku. `SamlAssertionDecryption`Identyfikator wskazuje użycie klucza kryptograficznego do szyfrowania i odszyfrowywania potwierdzenia odpowiedzi SAML.

    ```xml
    <CryptographicKeys>
            ...
      <Key Id="SamlAssertionDecryption" StorageReferenceId="B2C_1A_SAMLEncryptionCert"/>
    </CryptographicKeys>
    ```

3. Ustaw **WantsEncryptedAssertions** metadanych profilu technicznego na `true` .
    
    ```xml
    <Metadata>
      ...
      <Item Key="WantsEncryptedAssertions">true</Item>
    </Metadata>
    ```

4. Zaktualizuj dostawcę tożsamości za pomocą nowych Azure AD B2C metadanych profilu technicznego. Powinien zostać wyświetlony **deskryptor** klucza z właściwością **use** ustawioną na wartość `encryption` zawierającą klucz publiczny certyfikatu.

## <a name="enable-use-of-context-claims"></a>Włącz korzystanie z oświadczeń kontekstu

W kolekcji oświadczeń wejściowych i wyjściowych można uwzględnić oświadczenia, które nie są zwracane przez dostawcę tożsamości, dopóki atrybut ten jest ustawiony `DefaultValue` . Można również użyć [oświadczeń kontekstu](claim-resolver-overview.md) do uwzględnienia w profilu technicznym. Aby użyć roszczeń kontekstu:

1. Dodaj typ roszczeń do elementu [ClaimsSchema](claimsschema.md) w [BuildingBlocks](buildingblocks.md).
2. Dodaj zgłoszenie wyjściowe do kolekcji wejściowej lub wyjściowej. W poniższym przykładzie pierwsze zgłoszenie ustawia wartość dostawcy tożsamości. Drugie oświadczenie używa [oświadczeń kontekstu](claim-resolver-overview.md)adresu IP użytkownika.
    
    ```xml
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" AlwaysUseDefaultValue="true" />
      <OutputClaim ClaimTypeReferenceId="IpAddress" DefaultValue="{Context:IPAddress}" AlwaysUseDefaultValue="true" />
    </OutputClaims>
    ```

## <a name="disable-single-logout"></a>Wyłącz logowanie jednokrotne

W przypadku żądania wylogowania aplikacji Azure AD B2C próbuje wylogować się z dostawcy tożsamości SAML. Aby uzyskać więcej informacji, zobacz [Azure AD B2C wylogowania sesji](session-behavior.md#sign-out).  Aby wyłączyć zachowanie logowania jednokrotnego, ustaw  dla metadanych SingleLogoutEnabled `false` .

```xml
<Metadata>
  ...
  <Item Key="SingleLogoutEnabled">false</Item>
</Metadata>
```

## <a name="debug-saml-protocol"></a>Debuguj protokół SAML

Aby pomóc w konfigurowaniu i debugowaniu Federacji z dostawcą tożsamości SAML, można użyć rozszerzenia przeglądarki dla protokołu SAML, takiego jak [rozszerzenie SAML devtools](https://chrome.google.com/webstore/detail/saml-devtools-extension/jndllhgbinhiiddokbeoeepbppdnhhio) dla programu Chrome, [SAML-Tracer](https://addons.mozilla.org/es/firefox/addon/saml-tracer/) dla przeglądarki Firefox lub [Narzędzia deweloperskie przeglądarki Edge lub IE](https://techcommunity.microsoft.com/t5/microsoft-sharepoint-blog/gathering-a-saml-token-using-edge-or-ie-developer-tools/ba-p/320957).

Korzystając z tych narzędzi, można sprawdzić integrację między Azure AD B2C i dostawcą tożsamości SAML. Na przykład:

* Sprawdź, czy żądanie SAML zawiera podpis i określ algorytm używany do logowania żądania autoryzacji.
* Pobierz oświadczenia (potwierdzenia) poniżej `AttributeStatement` sekcji.
* Sprawdź, czy dostawca tożsamości zwraca komunikat o błędzie.
* Sprawdź, czy sekcja potwierdzenia jest zaszyfrowana.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak zdiagnozować problemy z zasadami niestandardowymi przy użyciu [Application Insights](troubleshoot-with-application-insights.md). 

::: zone-end
