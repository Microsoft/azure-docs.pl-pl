---
title: Protokół SAML logowania jednokrotnego na platformie Azure
titleSuffix: Microsoft identity platform
description: W tym artykule opisano protokół SAML Single Sign-On (SSO) w programie Azure Active Directory
services: active-directory
documentationcenter: .net
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 05/18/2020
ms.author: kenwith
ms.custom: aaddev
ms.reviewer: paulgarn
ms.openlocfilehash: 4990b81d929019b3d201f004176234fa0ea78339
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88118454"
---
# <a name="single-sign-on-saml-protocol"></a>Pojedynczy Sign-On protokół SAML

W tym artykule omówiono żądania uwierzytelniania SAML 2,0 i odpowiedzi, które Azure Active Directory (Azure AD) obsługują pojedyncze Sign-On (SSO).

Poniższy diagram protokołu opisuje sekwencję logowania jednokrotnego. Usługa w chmurze (dostawca usług) używa powiązania przekierowania HTTP w celu przekazania `AuthnRequest` elementu (żądanie uwierzytelnienia) do usługi Azure AD (dostawcy tożsamości). Usługa Azure AD następnie używa powiązania post protokołu HTTP w celu opublikowania `Response` elementu w usłudze w chmurze.

![Single Sign-On (SSO) — przepływ pracy](./media/single-sign-on-saml-protocol/active-directory-saml-single-sign-on-workflow.png)

> [!NOTE]
> W tym artykule omówiono użycie protokołu SAML do logowania jednokrotnego. Aby uzyskać więcej informacji na temat innych sposobów obsługi logowania jednokrotnego (na przykład przy użyciu usługi OpenID Connect Connect lub zintegrowanego uwierzytelniania systemu Windows), zobacz Logowanie jednokrotne [do aplikacji w Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="authnrequest"></a>AuthnRequest

Aby zażądać uwierzytelnienia użytkownika, usługi w chmurze wysyłają `AuthnRequest` element do usługi Azure AD. Przykładowy element SAML 2,0 `AuthnRequest` może wyglądać podobnie do poniższego przykładu:

```
<samlp:AuthnRequest
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="id6c1c178c166d486687be4aaf5e482730"
Version="2.0" IssueInstant="2013-03-18T03:28:54.1839884Z"
xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
</samlp:AuthnRequest>
```

| Parametr | Typ | Opis |
| --- | --- | --- |
| ID (Identyfikator) | Wymagane | Usługa Azure AD używa tego atrybutu do wypełniania `InResponseTo` atrybutu zwróconej odpowiedzi. Identyfikator nie może zaczynać się od liczby, więc typową strategią jest dołączenie ciągu takiego jak "ID" do ciągu reprezentującego identyfikator GUID. Na przykład `id6c1c178c166d486687be4aaf5e482730` jest prawidłowym identyfikatorem. |
| Wersja | Wymagane | Dla tego parametru należy ustawić wartość **2,0**. |
| IssueInstant | Wymagane | Jest to ciąg daty i godziny z wartością czasu UTC i [formatem rundy ("o")](/dotnet/standard/base-types/standard-date-and-time-format-strings). Usługa Azure AD oczekuje wartości daty i godziny tego typu, ale nie oblicza ani nie używa wartości. |
| AssertionConsumerServiceUrl | Opcjonalne | Jeśli ta wartość jest określona, ten parametr musi być zgodny z `RedirectUri` usługą w chmurze w usłudze Azure AD. |
| ForceAuthn | Opcjonalne | Jest to wartość logiczna. W przypadku wartości true oznacza to, że użytkownik zostanie zmuszony do ponownego uwierzytelnienia, nawet jeśli ma prawidłową sesję z usługą Azure AD. |
| Ispassy | Opcjonalne | Jest to wartość logiczna określająca, czy usługa Azure AD powinna uwierzytelniać użytkownika w trybie dyskretnym, bez interakcji z użytkownikiem, jeśli taki istnieje. Jeśli ta wartość jest równa true, usługa Azure AD podejmie próbę uwierzytelnienia użytkownika przy użyciu pliku cookie sesji. |

Wszystkie inne `AuthnRequest` atrybuty, takie jak zgody, Destination, AssertionConsumerServiceIndex, AttributeConsumerServiceIndex i ProviderName, są **ignorowane**.

Usługa Azure AD ignoruje także `Conditions` element w `AuthnRequest` .

### <a name="issuer"></a>Wystawca

`Issuer`Element w elemencie `AuthnRequest` musi dokładnie odpowiadać jednemu z **ServicePrincipalNames** w usłudze w chmurze w usłudze Azure AD. Zwykle jest to **Identyfikator URI aplikacji** określony podczas rejestracji aplikacji.

Fragment SAML zawierający `Issuer` element wygląda podobnie do poniższego przykładu:

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
```

### <a name="nameidpolicy"></a>NameIDPolicy

Ten element żąda określonego formatu identyfikatora nazwy w odpowiedzi i jest opcjonalny w `AuthnRequest` elementach wysyłanych do usługi Azure AD.

`NameIdPolicy`Element wygląda podobnie do poniższego przykładu:

```
<NameIDPolicy Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent"/>
```

Jeśli `NameIDPolicy` jest podany, można uwzględnić jego opcjonalny `Format` atrybut. `Format`Atrybut może mieć tylko jedną z następujących wartości; jakakolwiek inna wartość spowoduje wystąpienie błędu.

* `urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`: Azure Active Directory wystawia żądania NameID jako identyfikator parowania.
* `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`: Azure Active Directory wystawia żądania NameID w formacie adresu e-mail.
* `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`: Ta wartość zezwala Azure Active Directory na wybranie formatu żądania. Azure Active Directory wystawia NameID jako identyfikator parowania.
* `urn:oasis:names:tc:SAML:2.0:nameid-format:transient`: Azure Active Directory wystawia rolę NameID jako generowaną losowo wartość unikatową dla bieżącej operacji logowania jednokrotnego. Oznacza to, że wartość jest tymczasowa i nie można jej użyć do zidentyfikowania użytkownika uwierzytelniającego.

Jeśli `SPNameQualifier` jest określony, usługa Azure AD będzie zawierać takie samo `SPNameQualifier` w odpowiedzi.

Usługa Azure AD ignoruje `AllowCreate` atrybut.

### <a name="requestauthncontext"></a>RequestAuthnContext
`RequestedAuthnContext`Element określa odpowiednie metody uwierzytelniania. Jest ona opcjonalna w `AuthnRequest` elementach wysyłanych do usługi Azure AD. Usługa Azure AD obsługuje `AuthnContextClassRef` wartości takie jak `urn:oasis:names:tc:SAML:2.0:ac:classes:Password` .

### <a name="scoping"></a>Zakresów
`Scoping`Element, który zawiera listę dostawców tożsamości, jest opcjonalny w `AuthnRequest` elementach wysyłanych do usługi Azure AD.

Jeśli jest podany, nie dołączaj `ProxyCount` atrybutu `IDPListOption` lub `RequesterID` elementu, ponieważ nie są one obsługiwane.

### <a name="signature"></a>Podpis
`Signature`Element w `AuthnRequest` elementach jest opcjonalny. Jeśli istnieje podpis, usługa Azure AD nie sprawdza poprawności podpisanych żądań uwierzytelniania. Weryfikacja żądającego jest zapewniana przez nie tylko odpowiadanie na zarejestrowane adresy URL usługi konsumenckej potwierdzenia.

### <a name="subject"></a>Temat
Nie dołączaj `Subject` elementu. Usługa Azure AD nie obsługuje określania tematu dla żądania i zwróci błąd, jeśli został podany.

## <a name="response"></a>Reakcja
Po pomyślnym zakończeniu logowania usługa Azure AD ogłasza odpowiedź do usługi w chmurze. Odpowiedź na pomyślną próbę zalogowania wygląda jak w poniższym przykładzie:

```
<samlp:Response ID="_a4958bfd-e107-4e67-b06d-0d85ade2e76a" Version="2.0" IssueInstant="2013-03-18T07:38:15.144Z" Destination="https://contoso.com/identity/inboundsso.aspx" InResponseTo="id758d0ef385634593a77bdf7e632984b6" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
    ...
  </ds:Signature>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
  </samlp:Status>
  <Assertion ID="_bf9c623d-cc20-407a-9a59-c2d0aee84d12" IssueInstant="2013-03-18T07:38:15.144Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">
    <Issuer>https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
    <ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
      ...
    </ds:Signature>
    <Subject>
      <NameID>Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="id758d0ef385634593a77bdf7e632984b6" NotOnOrAfter="2013-03-18T07:43:15.144Z" Recipient="https://contoso.com/identity/inboundsso.aspx" />
      </SubjectConfirmation>
    </Subject>
    <Conditions NotBefore="2013-03-18T07:38:15.128Z" NotOnOrAfter="2013-03-18T08:48:15.128Z">
      <AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
      </AudienceRestriction>
    </Conditions>
    <AttributeStatement>
      <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
        <AttributeValue>testuser@contoso.com</AttributeValue>
      </Attribute>
      <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
        <AttributeValue>3F2504E0-4F89-11D3-9A0C-0305E82C3301</AttributeValue>
      </Attribute>
      ...
    </AttributeStatement>
    <AuthnStatement AuthnInstant="2013-03-18T07:33:56.000Z" SessionIndex="_bf9c623d-cc20-407a-9a59-c2d0aee84d12">
      <AuthnContext>
        <AuthnContextClassRef> urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
      </AuthnContext>
    </AuthnStatement>
  </Assertion>
</samlp:Response>
```

### <a name="response"></a>Reakcja

`Response`Element zawiera wynik żądania autoryzacji. Usługa Azure AD ustawia `ID` `Version` wartości i `IssueInstant` w `Response` elemencie. Ustawia również następujące atrybuty:

* `Destination`: Po pomyślnym zakończeniu logowania jest to ustawienie `RedirectUri` dostawcy usług (usługa w chmurze).
* `InResponseTo`: Jest to wartość `ID` atrybutu `AuthnRequest` elementu, który zainicjował odpowiedź.

### <a name="issuer"></a>Wystawca

Usługa Azure AD ustawia `Issuer` element, `https://sts.windows.net/<TenantIDGUID>/` który \<TenantIDGUID> jest identyfikatorem dzierżawy dzierżawy usługi Azure AD.

Na przykład odpowiedź z elementem Issuer może wyglądać jak w następującym przykładzie:

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

### <a name="status"></a>Stan

`Status`Element umożliwia przekazanie sukcesu lub niepowodzenia logowania. Zawiera `StatusCode` element, który zawiera kod lub zestaw zagnieżdżonych kodów, które reprezentują stan żądania. Zawiera również `StatusMessage` element, który zawiera niestandardowe komunikaty o błędach, które są generowane podczas procesu logowania.

<!-- TODO: Add an authentication protocol error reference -->

Poniższy przykład jest odpowiedzią SAML na nieudaną próbę logowania.

```
<samlp:Response ID="_f0961a83-d071-4be5-a18c-9ae7b22987a4" Version="2.0" IssueInstant="2013-03-18T08:49:24.405Z" InResponseTo="iddce91f96e56747b5ace6d2e2aa9d4f8c" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Requester">
      <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:RequestUnsupported" />
    </samlp:StatusCode>
    <samlp:StatusMessage>AADSTS75006: An error occurred while processing a SAML2 Authentication request. AADSTS90011: The SAML authentication request property 'NameIdentifierPolicy/SPNameQualifier' is not supported.
Trace ID: 66febed4-e737-49ff-ac23-464ba090d57c
Timestamp: 2013-03-18 08:49:24Z</samlp:StatusMessage>
  </samlp:Status>
```

### <a name="assertion"></a>Asercja

Oprócz `ID` `IssueInstant` `Version` elementów i usługa Azure AD ustawia następujące elementy w `Assertion` elemencie odpowiedzi.

#### <a name="issuer"></a>Wystawca

Jest to wartość `https://sts.windows.net/<TenantIDGUID>/` Where \<TenantIDGUID> , która jest identyfikatorem dzierżawy dzierżawy usługi Azure AD.

```
<Issuer>https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

#### <a name="signature"></a>Podpis

Usługa Azure AD podpisuje potwierdzenie w odpowiedzi na pomyślne logowanie. `Signature`Element zawiera podpis cyfrowy, którego usługa w chmurze może użyć do uwierzytelnienia źródła w celu zweryfikowania integralności potwierdzenia.

Aby wygenerować podpis cyfrowy, usługa Azure AD używa klucza podpisywania w `IDPSSODescriptor` elemencie dokumentu metadanych.

```
<ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
      digital_signature_here
    </ds:Signature>
```

#### <a name="subject"></a>Temat

Określa podmiot zabezpieczeń, który jest podmiotem instrukcji w potwierdzeniu. Zawiera `NameID` element, który reprezentuje uwierzytelnionego użytkownika. `NameID`Wartość jest identyfikatorem ukierunkowanym skierowanym tylko do dostawcy usług, który jest odbiorcą dla tokenu. Jest on trwały — można go odwołać, ale nigdy nie jest ponownie przypisywany. Jest on również nieprzezroczysty, w tym, że nie ujawnia żadnych informacji o użytkowniku i nie może być używany jako identyfikator zapytań dotyczących atrybutów.

`Method`Atrybut `SubjectConfirmation` elementu jest zawsze ustawiony na `urn:oasis:names:tc:SAML:2.0:cm:bearer` .

```
<Subject>
      <NameID>Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="id758d0ef385634593a77bdf7e632984b6" NotOnOrAfter="2013-03-18T07:43:15.144Z" Recipient="https://contoso.com/identity/inboundsso.aspx" />
      </SubjectConfirmation>
</Subject>
```

#### <a name="conditions"></a>Warunki

Ten element określa warunki, które definiują akceptowalne użycie potwierdzeń SAML.

```
<Conditions NotBefore="2013-03-18T07:38:15.128Z" NotOnOrAfter="2013-03-18T08:48:15.128Z">
      <AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
      </AudienceRestriction>
</Conditions>
```

`NotBefore`Atrybuty i `NotOnOrAfter` określają interwał, w którym potwierdzenie jest prawidłowe.

* Wartość `NotBefore` atrybutu jest równa lub nieznacznie (mniejsza niż sekunda) późniejsza niż wartość `IssueInstant` atrybutu `Assertion` elementu. Usługa Azure AD nie uwzględnia żadnej różnicy czasu między nią a usługą w chmurze (dostawcy usługi) i nie dodaje żadnych buforów do tej godziny.
* Wartość `NotOnOrAfter` atrybutu jest 70 minut później niż wartość `NotBefore` atrybutu.

#### <a name="audience"></a>Grupy odbiorców

Zawiera identyfikator URI, który identyfikuje zamierzone odbiorcy. Usługa Azure AD ustawia wartość tego elementu na wartość `Issuer` elementu `AuthnRequest` , który zainicjował logowanie. Aby oszacować `Audience` wartość, użyj wartości, `App ID URI` która została określona podczas rejestracji aplikacji.

```
<AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
</AudienceRestriction>
```

Podobnie jak w przypadku wartości `Issuer` , `Audience` wartość musi być dokładnie zgodna z jedną z głównych nazw usług, która reprezentuje usługę w chmurze w usłudze Azure AD. Jeśli jednak wartość `Issuer` elementu nie jest wartością identyfikatora URI, `Audience` wartość odpowiedzi jest `Issuer` poprzedzona wartością `spn:` .

#### <a name="attributestatement"></a>AttributeStatement

Zawiera ona oświadczenia dotyczące tematu lub użytkownika. Poniższy fragment zawiera przykładowy `AttributeStatement` element. Wielokropek wskazuje, że element może zawierać wiele atrybutów i wartości atrybutów.

```
<AttributeStatement>
      <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
        <AttributeValue>testuser@contoso.com</AttributeValue>
      </Attribute>
      <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
        <AttributeValue>3F2504E0-4F89-11D3-9A0C-0305E82C3301</AttributeValue>
      </Attribute>
      ...
</AttributeStatement>
```        

* **Nazwa** — wartość `Name` atrybutu ( `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` ) to główna nazwa użytkownika uwierzytelnionego użytkownika, na przykład `testuser@managedtenant.com` .
* **Elemencie objectidentifier** — wartość `ObjectIdentifier` atrybutu ( `http://schemas.microsoft.com/identity/claims/objectidentifier` ) jest `ObjectId` obiektem katalogu, który reprezentuje uwierzytelnionego użytkownika w usłudze Azure AD. `ObjectId` jest niezmiennym, globalnie unikatowym i wielokrotnym używaniem bezpiecznego identyfikatora uwierzytelnionego użytkownika.

#### <a name="authnstatement"></a>AuthnStatement

Ten element potwierdza, że podmiot potwierdzający został uwierzytelniony przez określone środki w określonym czasie.

* Ten `AuthnInstant` atrybut określa czas uwierzytelniania użytkownika w usłudze Azure AD.
* `AuthnContext`Element określa kontekst uwierzytelniania używany do uwierzytelniania użytkownika.

```
<AuthnStatement AuthnInstant="2013-03-18T07:33:56.000Z" SessionIndex="_bf9c623d-cc20-407a-9a59-c2d0aee84d12">
      <AuthnContext>
        <AuthnContextClassRef> urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
      </AuthnContext>
</AuthnStatement>
```