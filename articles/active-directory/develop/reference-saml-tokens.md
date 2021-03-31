---
title: Odwołania do oświadczeń tokenów SAML 2,0 | Azure
titleSuffix: Microsoft identity platform
description: Odwołanie oświadczeń ze szczegółowymi informacjami dotyczącymi oświadczeń zawartych w tokenach SAML 2,0 wystawionych przez platformę tożsamości firmy Microsoft, w tym ich odpowiedników JWT.
author: kenwith
services: active-directory
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: reference
ms.workload: identity
ms.date: 09/09/2020
ms.author: kenwith
ms.reviewer: paulgarn
ms.custom: aaddev
ms.openlocfilehash: 9c3132985866a4c245984ef632107c05ca1b3350
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96348386"
---
# <a name="saml-token-claims-reference"></a>Odwołanie do oświadczeń tokenów SAML

Platforma tożsamości firmy Microsoft emituje kilka typów tokenów zabezpieczających w przetwarzaniu poszczególnych przepływów uwierzytelniania. W tym dokumencie opisano format, charakterystykę zabezpieczeń i zawartość tokenów SAML 2,0.

## <a name="claims-in-saml-tokens"></a>Oświadczenia w tokenach SAML

> [!div class="mx-codeBreakAll"]
> | Nazwa | Równoważnego odszkodowania JWT | Opis | Przykład |
> | --- | --- | --- | ------------|
> |Grupy odbiorców | `aud` |Zamierzony odbiorca tokenu. Aplikacja, która otrzymuje token musi sprawdzić, czy wartość odbiorców jest poprawna i odrzucać tokeny przeznaczone dla różnych odbiorców. | `<AudienceRestriction>`<br>`<Audience>`<br>`https://contoso.com`<br>`</Audience>`<br>`</AudienceRestriction>`  |
> | Błyskawiczne uwierzytelnianie | |Rejestruje datę i godzinę wystąpienia uwierzytelniania. | `<AuthnStatement AuthnInstant="2011-12-29T05:35:22.000Z">` |
> |Metoda uwierzytelniania | `amr` |Określa sposób uwierzytelniania podmiotu tokenu. | `<AuthnContextClassRef>`<br>`http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod/password`<br>`</AuthnContextClassRef>` |
> |Imię | `given_name` |Zawiera imię i nazwisko użytkownika, zgodnie z ustawieniem obiektu użytkownika usługi Azure AD. | `<Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname">`<br>`<AttributeValue>Frank<AttributeValue>`  |
> |Grupy | `groups` |Dostarcza identyfikatory obiektów, które reprezentują członkostwo w grupach podmiotu. Te wartości są unikatowe (zobacz identyfikator obiektu) i mogą być bezpiecznie używane do zarządzania dostępem, takie jak wymuszanie autoryzacji dostępu do zasobu. Grupy zawarte w ramach roszczeń grup są konfigurowane dla poszczególnych aplikacji, przez właściwość "groupMembershipClaims" manifestu aplikacji. Wartość null spowoduje wykluczenie wszystkich grup, a wartość "Security Group" będzie zawierać tylko Active Directory przynależności do grupy zabezpieczeń, a wartość "All" będzie obejmować zarówno grupy zabezpieczeń, jak i listy dystrybucyjne Microsoft 365. <br><br> **Uwagi**: <br> Jeśli liczba grup, do których należy użytkownik, przekracza limit (150 dla protokołu SAML, 200 dla tokenu JWT), zostanie dodane zdarzenie nadwyżkowe, które wskazują na punkt końcowy grafu zawierający listę grup dla użytkownika. | `<Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/groups">`<br>`<AttributeValue>07dd8a60-bf6d-4e17-8844-230b77145381</AttributeValue>` |
> | Wskaźnik nadwyżki grup | `groups:src1` | W przypadku żądań tokenów, które nie mają ograniczonej długości, ale nadal są za duże dla tokenu, zostanie uwzględniony link do listy pełnych grup dla użytkownika. W przypadku protokołu SAML to pole jest dodawane jako nowe zgłoszenie zamiast `groups` żądania. | `<Attribute Name=" http://schemas.microsoft.com/claims/groups.link">`<br>`<AttributeValue>https://graph.windows.net/{tenantID}/users/{userID}/getMemberObjects<AttributeValue>` |
> |Dostawca tożsamości | `idp` |Rejestruje dostawcę tożsamości, który uwierzytelnił podmiot tokenu. Ta wartość jest taka sama jak wartość tego żądania wystawcy, chyba że konto użytkownika znajduje się w innej dzierżawie niż wystawcy. | `<Attribute Name=" http://schemas.microsoft.com/identity/claims/identityprovider">`<br>`<AttributeValue>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/<AttributeValue>` |
> |IssuedAt | `iat` |Przechowuje czas wystawienia tokenu. Często służy do mierzenia Aktualności tokenu. | `<Assertion ID="_d5ec7a9b-8d8f-4b44-8c94-9812612142be" IssueInstant="2014-01-06T20:20:23.085Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">` |
> |Wystawca | `iss` |Identyfikuje usługę tokenu zabezpieczającego (STS), która konstruuje i zwraca token. W tokenach zwracanych przez usługę Azure AD wystawcy jest sts.windows.net. Identyfikator GUID w polu wartość żądania wystawcy jest IDENTYFIKATORem dzierżawy katalogu usługi Azure AD. Identyfikator dzierżawy jest niezmiennym i niezawodnym identyfikatorem katalogu. | `<Issuer>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/</Issuer>` |
> |Nazwisko | `family_name` |Zawiera nazwisko, nazwisko lub nazwę rodziny użytkownika, zgodnie z definicją w obiekcie użytkownika usługi Azure AD. | `<Attribute Name=" http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname">`<br>`<AttributeValue>Miller<AttributeValue>` |
> |Nazwa | `unique_name` |Udostępnia zrozumiałą wartość identyfikującą podmiot tokenu. Ta wartość nie gwarantuje, że jest unikatowa w dzierżawie i jest przeznaczona do użycia tylko w celach wyświetlania. | `<Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">`<br>`<AttributeValue>frankm@contoso.com<AttributeValue>`|
> |Identyfikator obiektu | `oid` |Zawiera unikatowy identyfikator obiektu w usłudze Azure AD. Ta wartość jest niezmienna i nie można jej ponownie przypisać ani ponownie użyć. Identyfikator obiektu służy do identyfikowania obiektu w zapytaniach do usługi Azure AD. | `<Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">`<br>`<AttributeValue>528b2ac2-aa9c-45e1-88d4-959b53bc7dd0<AttributeValue>` |
> |Role | `roles` |Reprezentuje wszystkie role aplikacji, których podmiot został przyznany zarówno bezpośrednio, jak i pośrednio za pośrednictwem członkostwa w grupie i może służyć do wymuszania kontroli dostępu opartej na rolach. Role aplikacji są definiowane dla poszczególnych aplikacji, przez `appRoles` Właściwość manifestu aplikacji. `value`Właściwość każdej roli aplikacji jest wartością, która pojawia się w ramach żądania role. | `<Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/role">`|
> |Temat | `sub` |Identyfikuje podmiot zabezpieczeń, w którym token potwierdza informacje, takie jak użytkownik aplikacji. Ta wartość jest niezmienna i nie może zostać ponownie przypisana ani ponownie użyta, dlatego może być używana do bezpiecznego wykonywania kontroli autoryzacji. Ponieważ podmiot jest zawsze obecny w tokenach, których dotyczy problem z usługą Azure AD, zalecamy użycie tej wartości w systemie autoryzacji ogólnego przeznaczenia. <br> `SubjectConfirmation` nie jest zgłoszeniem. Opisano sposób weryfikacji tematu tokenu. `Bearer` wskazuje, że podmiot został potwierdzony przez ich posiadanie tokenu. | `<Subject>`<br>`<NameID>S40rgb3XjhFTv6EQTETkEzcgVmToHKRkZUIsJlmLdVc</NameID>`<br>`<SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />`<br>`</Subject>`|
> |Identyfikator dzierżawy | `tid` |Niezmienny identyfikator, który nie jest wielokrotnego użytku, który identyfikuje dzierżawcę katalogu, który wystawił token. Możesz użyć tej wartości, aby uzyskać dostęp do zasobów katalogu specyficznych dla dzierżawy w aplikacji wielodostępnej. Na przykład możesz użyć tej wartości, aby zidentyfikować dzierżawcę w wywołaniu interfejs API programu Graph. | `<Attribute Name="http://schemas.microsoft.com/identity/claims/tenantid">`<br>`<AttributeValue>cbb1a5ac-f33b-45fa-9bf5-f37db0fed422<AttributeValue>`|
> |Czas życia tokenu | `nbf`, `exp` |Definiuje przedział czasu, przez który token jest prawidłowy. Usługa, która sprawdza poprawność tokenu, powinna sprawdzić, czy bieżąca data jest w okresie istnienia tokenu, w przeciwnym razie należy odrzucić token. Usługa może zezwalać na maksymalnie pięć minut poza zakresem czasu istnienia tokenu, aby uwzględnić różnice w czasie zegara ("pochylenie") między usługą Azure AD i usługami. | `<Conditions`<br>`NotBefore="2013-03-18T21:32:51.261Z"`<br>`NotOnOrAfter="2013-03-18T22:32:51.261Z"`<br>`>` <br>|

## <a name="sample-saml-token"></a>Przykładowy token SAML

Jest to przykład typowego tokenu SAML.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<t:RequestSecurityTokenResponse xmlns:t="http://schemas.xmlsoap.org/ws/2005/02/trust">
    <t:Lifetime>
        <wsu:Created xmlns:wsu="https://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">2014-12-24T05:15:47.060Z</wsu:Created>
        <wsu:Expires xmlns:wsu="https://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">2014-12-24T06:15:47.060Z</wsu:Expires>
    </t:Lifetime>
    <wsp:AppliesTo xmlns:wsp="http://schemas.xmlsoap.org/ws/2004/09/policy">
        <EndpointReference xmlns="https://www.w3.org/2005/08/addressing">
            <Address>https://contoso.onmicrosoft.com/MyWebApp</Address>
        </EndpointReference>
    </wsp:AppliesTo>
    <t:RequestedSecurityToken>
        <Assertion xmlns="urn:oasis:names:tc:SAML:2.0:assertion" ID="_3ef08993-846b-41de-99df-b7f3ff77671b" IssueInstant="2014-12-24T05:20:47.060Z" Version="2.0">
            <Issuer>https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/</Issuer>
            <ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
                <ds:SignedInfo>
                    <ds:CanonicalizationMethod Algorithm="https://www.w3.org/2001/10/xml-exc-c14n#" />
                    <ds:SignatureMethod Algorithm="https://www.w3.org/2001/04/xmldsig-more#rsa-sha256" />
                    <ds:Reference URI="#_3ef08993-846b-41de-99df-b7f3ff77671b">
                        <ds:Transforms>
                            <ds:Transform Algorithm="https://www.w3.org/2000/09/xmldsig#enveloped-signature" />
                            <ds:Transform Algorithm="https://www.w3.org/2001/10/xml-exc-c14n#" />
                        </ds:Transforms>
                        <ds:DigestMethod Algorithm="https://www.w3.org/2001/04/xmlenc#sha256" />
                        <ds:DigestValue>cV1J580U1pD24hEyGuAxrbtgROVyghCqI32UkER/nDY=</ds:DigestValue>
                    </ds:Reference>
                </ds:SignedInfo>
                <ds:SignatureValue>j+zPf6mti8Rq4Kyw2NU2nnu0pbJU1z5bR/zDaKaO7FCTdmjUzAvIVfF8pspVR6CbzcYM3HOAmLhuWmBkAAk6qQUBmKsw+XlmF/pB/ivJFdgZSLrtlBs1P/WBV3t04x6fRW4FcIDzh8KhctzJZfS5wGCfYw95er7WJxJi0nU41d7j5HRDidBoXgP755jQu2ZER7wOYZr6ff+ha+/Aj3UMw+8ZtC+WCJC3yyENHDAnp2RfgdElJal68enn668fk8pBDjKDGzbNBO6qBgFPaBT65YvE/tkEmrUxdWkmUKv3y7JWzUYNMD9oUlut93UTyTAIGOs5fvP9ZfK2vNeMVJW7Xg==</ds:SignatureValue>
                <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
                    <X509Data>
                        <X509Certificate>MIIDPjCCAabcAwIBAgIQsRiM0jheFZhKk49YD0SK1TAJBgUrDgMCHQUAMC0xKzApBgNVBAMTImFjY291bnRzLmFjY2Vzc2NvbnRyb2wud2luZG93cy5uZXQwHhcNMTQwMTAxMDcwMDAwWhcNMTYwMTAxMDcwMDAwWjAtMSswKQYDVQQDEyJhY2NvdW50cy5hY2Nlc3Njb250cm9sLndpbmRvd3MubmV0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAkSCWg6q9iYxvJE2NIhSyOiKvqoWCO2GFipgH0sTSAs5FalHQosk9ZNTztX0ywS/AHsBeQPqYygfYVJL6/EgzVuwRk5txr9e3n1uml94fLyq/AXbwo9yAduf4dCHTP8CWR1dnDR+Qnz/4PYlWVEuuHHONOw/blbfdMjhY+C/BYM2E3pRxbohBb3x//CfueV7ddz2LYiH3wjz0QS/7kjPiNCsXcNyKQEOTkbHFi3mu0u13SQwNddhcynd/GTgWN8A+6SN1r4hzpjFKFLbZnBt77ACSiYx+IHK4Mp+NaVEi5wQtSsjQtI++XsokxRDqYLwus1I1SihgbV/STTg5enufuwIDAQABo2IwYDBeBgNVHQEEVzBVgBDLebM6bK3BjWGqIBrBNFeNoS8wLTErMCkGA1UEAxMiYWNjb3VudHMuYWNjZXNzY29udHJvbC53aW5kb3dzLm5ldIIQsRiM0jheFZhKk49YD0SK1TAJBgUrDgMCHQUAA4IBAQCJ4JApryF77EKC4zF5bUaBLQHQ1PNtA1uMDbdNVGKCmSp8M65b8h0NwlIjGGGy/unK8P6jWFdm5IlZ0YPTOgzcRZguXDPj7ajyvlVEQ2K2ICvTYiRQqrOhEhZMSSZsTKXFVwNfW6ADDkN3bvVOVbtpty+nBY5UqnI7xbcoHLZ4wYD251uj5+lo13YLnsVrmQ16NCBYq2nQFNPuNJw6t3XUbwBHXpF46aLT1/eGf/7Xx6iy8yPJX4DyrpFTutDz882RWofGEO5t4Cw+zZg70dJ/hH/ODYRMorfXEW+8uKmXMKmX2wyxMKvfiPbTy5LmAU8Jvjs2tLg4rOBcXWLAIarZ</X509Certificate>
                    </X509Data>
                </KeyInfo>
            </ds:Signature>
            <Subject>
                <NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent">m_H3naDei2LNxUmEcWd0BZlNi_jVET1pMLR6iQSuYmo</NameID>
                <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />
            </Subject>
            <Conditions NotBefore="2014-12-24T05:15:47.060Z" NotOnOrAfter="2014-12-24T06:15:47.060Z">
                <AudienceRestriction>
                    <Audience>https://contoso.onmicrosoft.com/MyWebApp</Audience>
                </AudienceRestriction>
            </Conditions>
            <AttributeStatement>
                <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
                    <AttributeValue>a1addde8-e4f9-4571-ad93-3059e3750d23</AttributeValue>
                </Attribute>
                <Attribute Name="http://schemas.microsoft.com/identity/claims/tenantid">
                    <AttributeValue>b9411234-09af-49c2-b0c3-653adc1f376e</AttributeValue>
                </Attribute>
                <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
                    <AttributeValue>sample.admin@contoso.onmicrosoft.com</AttributeValue>
                </Attribute>
                <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname">
                    <AttributeValue>Admin</AttributeValue>
                </Attribute>
                <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname">
                    <AttributeValue>Sample</AttributeValue>
                </Attribute>
                <Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/groups">
                    <AttributeValue>5581e43f-6096-41d4-8ffa-04e560bab39d</AttributeValue>
                    <AttributeValue>07dd8a89-bf6d-4e81-8844-230b77145381</AttributeValue>
                    <AttributeValue>0e129f4g-6b0a-4944-982d-f776000632af</AttributeValue>
                    <AttributeValue>3ee07328-52ef-4739-a89b-109708c22fb5</AttributeValue>
                    <AttributeValue>329k14b3-1851-4b94-947f-9a4dacb595f4</AttributeValue>
                    <AttributeValue>6e32c650-9b0a-4491-b429-6c60d2ca9a42</AttributeValue>
                    <AttributeValue>f3a169a7-9a58-4e8f-9d47-b70029v07424</AttributeValue>
                    <AttributeValue>8e2c86b2-b1ad-476d-9574-544d155aa6ff</AttributeValue>
                    <AttributeValue>1bf80264-ff24-4866-b22c-6212e5b9a847</AttributeValue>
                    <AttributeValue>4075f9c3-072d-4c32-b542-03e6bc678f3e</AttributeValue>
                    <AttributeValue>76f80527-f2cd-46f4-8c52-8jvd8bc749b1</AttributeValue>
                    <AttributeValue>0ba31460-44d0-42b5-b90c-47b3fcc48e35</AttributeValue>
                    <AttributeValue>edd41703-8652-4948-94a7-2d917bba7667</AttributeValue>
                </Attribute>
                <Attribute Name="http://schemas.microsoft.com/identity/claims/identityprovider">
                    <AttributeValue>https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/</AttributeValue>
                </Attribute>
            </AttributeStatement>
            <AuthnStatement AuthnInstant="2014-12-23T18:51:11.000Z">
                <AuthnContext>
                    <AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
                </AuthnContext>
            </AuthnStatement>
        </Assertion>
    </t:RequestedSecurityToken>
    <t:RequestedAttachedReference>
        <SecurityTokenReference xmlns="https://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd" xmlns:d3p1="https://docs.oasis-open.org/wss/oasis-wss-wssecurity-secext-1.1.xsd" d3p1:TokenType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0">
            <KeyIdentifier ValueType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLID">_3ef08993-846b-41de-99df-b7f3ff77671b</KeyIdentifier>
        </SecurityTokenReference>
    </t:RequestedAttachedReference>
    <t:RequestedUnattachedReference>
        <SecurityTokenReference xmlns="https://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd" xmlns:d3p1="https://docs.oasis-open.org/wss/oasis-wss-wssecurity-secext-1.1.xsd" d3p1:TokenType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0">
            <KeyIdentifier ValueType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLID">_3ef08993-846b-41de-99df-b7f3ff77671b</KeyIdentifier>
        </SecurityTokenReference>
    </t:RequestedUnattachedReference>
    <t:TokenType>http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0</t:TokenType>
    <t:RequestType>http://schemas.xmlsoap.org/ws/2005/02/trust/Issue</t:RequestType>
    <t:KeyType>http://schemas.xmlsoap.org/ws/2005/05/identity/NoProofKey</t:KeyType>
</t:RequestSecurityTokenResponse>
```

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej na temat zarządzania zasadami okresu istnienia tokenu za pomocą interfejsu API Microsoft Graph, zobacz [Omówienie zasobów zasad usługi Azure AD](/graph/api/resources/policy).
* Dodawanie [niestandardowych i opcjonalnych oświadczeń](active-directory-optional-claims.md) do tokenów aplikacji.
* Użyj [jednego Sign-On (SSO) z użyciem protokołu SAML](single-sign-on-saml-protocol.md).
* Korzystanie z [protokołu SAML Single Sign-Out platformy Azure](single-sign-out-saml-protocol.md)
