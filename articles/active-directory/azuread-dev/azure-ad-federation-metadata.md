---
title: Metadane federacji usługi Azure AD | Microsoft Docs
description: W tym artykule opisano dokument metadanych Federacji, który Azure Active Directory publikuje dla usług akceptujących tokeny Azure Active Directory.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 01/07/2017
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: bcc44f61ccb7b4a19e7df39ab979669c5aa37da1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "80154903"
---
# <a name="federation-metadata"></a>Metadane federacji

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Azure Active Directory (Azure AD) publikuje dokument metadanych Federacji dla usług skonfigurowanych do akceptowania tokenów zabezpieczających, które są problemy z usługą Azure AD. Format dokumentu metadanych Federacji został opisany w programie [Web Services Federation Language (WS-Federation) w wersji 1,2](https://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html), który rozszerza [metadane dla języka OASIS SAML (SAML) v 2.0](https://docs.oasis-open.org/security/saml/v2.0/saml-metadata-2.0-os.pdf).

## <a name="tenant-specific-and-tenant-independent-metadata-endpoints"></a>Punkty końcowe metadanych specyficzne dla dzierżawy i niezależnej dzierżawy
Usługa Azure AD publikuje punkty końcowe specyficzne dla dzierżawy i niezależne od dzierżawców.

Punkty końcowe specyficzne dla dzierżawy są przeznaczone dla określonej dzierżawy. Metadane federacji specyficzne dla dzierżawy zawierają informacje o dzierżawie, w tym wystawcy i informacje o punkcie końcowym specyficznym dla dzierżawy. Aplikacje, które ograniczają dostęp do pojedynczej dzierżawy, korzystają z punktów końcowych specyficznych dla dzierżawy.

Punkty końcowe niezależne od dzierżawców dostarczają informacji wspólnych dla wszystkich dzierżawców usługi Azure AD. Te informacje dotyczą dzierżawców hostowanych w usłudze *login.microsoftonline.com* i są współdzielone przez dzierżawców. Punkty końcowe niezależne od dzierżawców są zalecane w przypadku aplikacji wielodostępnych, ponieważ nie są one skojarzone z żadną określoną dzierżawą.

## <a name="federation-metadata-endpoints"></a>Punkty końcowe metadanych Federacji
Usługa Azure AD publikuje metadane federacji pod adresem `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml` .

Dla **punktów końcowych specyficznych dla dzierżawy** `TenantDomainName` może to być jeden z następujących typów:

* Zarejestrowana nazwa domeny dzierżawy usługi Azure AD, np.: `contoso.onmicrosoft.com` .
* Niezmienny identyfikator dzierżawy domeny, na przykład `72f988bf-86f1-41af-91ab-2d7cd011db45` .

W przypadku **punktów końcowych niezależnych od dzierżawców** `TenantDomainName` jest to `common` . Ten dokument zawiera listę tylko elementów metadanych Federacji, które są wspólne dla wszystkich dzierżawców usługi Azure AD hostowanych w witrynie login.microsoftonline.com.

Na przykład punkt końcowy specyficzny dla dzierżawy może być `https://login.microsoftonline.com/contoso.onmicrosoft.com/FederationMetadata/2007-06/FederationMetadata.xml` . Punkt końcowy niezależny od dzierżawy to [https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml](https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml) . Możesz wyświetlić dokument metadanych Federacji, wpisując ten adres URL w przeglądarce.

## <a name="contents-of-federation-metadata"></a>Zawartość metadanych Federacji
W poniższej sekcji znajdują się informacje dotyczące usług korzystających z tokenów wystawionych przez usługę Azure AD.

### <a name="entity-id"></a>Identyfikator jednostki
`EntityDescriptor`Element zawiera `EntityID` atrybut. Wartość `EntityID` atrybutu reprezentuje wystawcę, czyli usługę tokenu zabezpieczającego (STS), która wystawiła token. Ważne jest, aby sprawdzić poprawność wystawcy w przypadku otrzymania tokenu.

Poniższe metadane przedstawiają przykładowy element specyficzny dla dzierżawy `EntityDescriptor` z `EntityID` elementem.

```
<EntityDescriptor
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="_b827a749-cfcb-46b3-ab8b-9f6d14a1294b"
entityID="https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db45/">
```
Identyfikator dzierżawy można zastąpić w punkcie końcowym niezależnym od dzierżawcy IDENTYFIKATORem dzierżawy, aby utworzyć wartość specyficzną dla dzierżawy `EntityID` . Wartość wyników będzie taka sama jak Wystawca tokenu. Strategia umożliwia aplikacji wielodostępnej Weryfikowanie wystawcy dla danej dzierżawy.

Poniższe metadane przedstawiają przykładowy element niezależny od dzierżawy `EntityID` . Pamiętaj, że `{tenant}` jest to literał, a nie symbol zastępczy.

```
<EntityDescriptor
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="="_0e5bd9d0-49ef-4258-bc15-21ce143b61bd"
entityID="https://sts.windows.net/{tenant}/">
```

### <a name="token-signing-certificates"></a>Certyfikaty podpisywania tokenu
Gdy usługa otrzymuje token wystawiony przez dzierżawę usługi Azure AD, podpis tokenu musi być zweryfikowany za pomocą klucza podpisywania, który jest publikowany w dokumencie metadanych Federacji. Metadane federacji obejmują publiczną część certyfikatów, które są używane przez dzierżawców do podpisywania tokenu. Bajty RAW certyfikatu pojawiają się w `KeyDescriptor` elemencie. Certyfikat podpisywania tokenu jest ważny do podpisywania tylko wtedy, gdy wartość `use` atrybutu to `signing` .

Dokument metadanych Federacji Opublikowany przez usługę Azure AD może mieć wiele kluczy podpisywania, na przykład gdy usługa Azure AD jest przygotowana do aktualizacji certyfikatu podpisywania. Gdy dokument metadanych Federacji zawiera więcej niż jeden certyfikat, usługa, która sprawdza tokeny, powinna obsługiwać wszystkie certyfikaty w dokumencie.

Poniższe metadane przedstawiają przykładowy `KeyDescriptor` element z kluczem podpisywania.

```
<KeyDescriptor use="signing">
<KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
<X509Data>
<X509Certificate>
MIIDPjCCAiqgAwIBAgIQVWmXY/+9RqFA/OG9kFulHDAJBgUrDgMCHQUAMC0xKzApBgNVBAMTImFjY291bnRzLmFjY2Vzc2NvbnRyb2wud2luZG93cy5uZXQwHhcNMTIwNjA3MDcwMDAwWhcNMTQwNjA3MDcwMDAwWjAtMSswKQYDVQQDEyJhY2NvdW50cy5hY2Nlc3Njb250cm9sLndpbmRvd3MubmV0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEArCz8Sn3GGXmikH2MdTeGY1D711EORX/lVXpr+ecGgqfUWF8MPB07XkYuJ54DAuYT318+2XrzMjOtqkT94VkXmxv6dFGhG8YZ8vNMPd4tdj9c0lpvWQdqXtL1TlFRpD/P6UMEigfN0c9oWDg9U7Ilymgei0UXtf1gtcQbc5sSQU0S4vr9YJp2gLFIGK11Iqg4XSGdcI0QWLLkkC6cBukhVnd6BCYbLjTYy3fNs4DzNdemJlxGl8sLexFytBF6YApvSdus3nFXaMCtBGx16HzkK9ne3lobAwL2o79bP4imEGqg+ibvyNmbrwFGnQrBc1jTF9LyQX9q+louxVfHs6ZiVwIDAQABo2IwYDBeBgNVHQEEVzBVgBCxDDsLd8xkfOLKm4Q/SzjtoS8wLTErMCkGA1UEAxMiYWNjb3VudHMuYWNjZXNzY29udHJvbC53aW5kb3dzLm5ldIIQVWmXY/+9RqFA/OG9kFulHDAJBgUrDgMCHQUAA4IBAQAkJtxxm/ErgySlNk69+1odTMP8Oy6L0H17z7XGG3w4TqvTUSWaxD4hSFJ0e7mHLQLQD7oV/erACXwSZn2pMoZ89MBDjOMQA+e6QzGB7jmSzPTNmQgMLA8fWCfqPrz6zgH+1F1gNp8hJY57kfeVPBiyjuBmlTEBsBlzolY9dd/55qqfQk6cgSeCbHCy/RU/iep0+UsRMlSgPNNmqhj5gmN2AFVCN96zF694LwuPae5CeR2ZcVknexOWHYjFM0MgUSw0ubnGl0h9AJgGyhvNGcjQqu9vd1xkupFgaN+f7P3p3EVN5csBg5H94jEcQZT7EKeTiZ6bTrpDAnrr8tDCy8ng
</X509Certificate>
</X509Data>
</KeyInfo>
</KeyDescriptor>
  ```

`KeyDescriptor`Element pojawia się w dwóch miejscach w dokumencie metadanych Federacji, w sekcji dotyczącej protokołu WS-Federation i w sekcji dotyczącej języka SAML. Certyfikaty publikowane w obu sekcjach będą takie same.

W sekcji dotyczącej protokołu WS-Federation czytnik metadanych WS-Federation odczytuje certyfikaty z `RoleDescriptor` elementu z `SecurityTokenServiceType` typem.

Poniższe metadane przedstawiają przykładowy `RoleDescriptor` element.

```
<RoleDescriptor xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:fed="https://docs.oasis-open.org/wsfed/federation/200706" xsi:type="fed:SecurityTokenServiceType"protocolSupportEnumeration="https://docs.oasis-open.org/wsfed/federation/200706">
```

W sekcji dotyczącej protokołu SAML czytnik metadanych WS-Federation odczytuje certyfikaty z `IDPSSODescriptor` elementu.

Poniższe metadane przedstawiają przykładowy `IDPSSODescriptor` element.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
```
Nie ma żadnych różnic w formacie certyfikatów specyficznych dla dzierżawy i niezależnych od dzierżawców.

### <a name="ws-federation-endpoint-url"></a>Adres URL punktu końcowego WS-Federation
Metadane federacji obejmują adres URL, który jest wykorzystywany przez usługę Azure AD do logowania jednokrotnego i logowania jednokrotnego w protokole WS-Federation. Ten punkt końcowy jest wyświetlany w `PassiveRequestorEndpoint` elemencie.

Poniższe metadane przedstawiają przykładowy `PassiveRequestorEndpoint` element dla punktu końcowego określonego dla dzierżawy.

```
<fed:PassiveRequestorEndpoint>
<EndpointReference xmlns="https://www.w3.org/2005/08/addressing">
<Address>
https://login.microsoftonline.com/72f988bf-86f1-41af-91ab-2d7cd011db45/wsfed
</Address>
</EndpointReference>
</fed:PassiveRequestorEndpoint>
```
W przypadku punktu końcowego niezależnego od dzierżawy adres URL WS-Federation pojawia się w punkcie końcowym WS-Federation, jak pokazano w poniższym przykładzie.

```
<fed:PassiveRequestorEndpoint>
<EndpointReference xmlns="https://www.w3.org/2005/08/addressing">
<Address>
https://login.microsoftonline.com/common/wsfed
</Address>
</EndpointReference>
</fed:PassiveRequestorEndpoint>
```

### <a name="saml-protocol-endpoint-url"></a>Adres URL punktu końcowego protokołu SAML
Metadane federacji obejmują adres URL, którego usługa Azure AD używa do logowania jednokrotnego i logowania jednokrotnego w protokole SAML 2,0. Te punkty końcowe są wyświetlane w `IDPSSODescriptor` elemencie.

Adresy URL logowania i wylogowywania są wyświetlane w `SingleSignOnService` `SingleLogoutService` elementach i.

Poniższe metadane przedstawiają przykład `PassiveResistorEndpoint` dla punktu końcowego określonego dla dzierżawy.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
…
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/contoso.onmicrosoft.com/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/contoso.onmicrosoft.com /saml2" />
  </IDPSSODescriptor>
```

Podobnie punkty końcowe dla typowych punktów końcowych protokołu SAML 2,0 są publikowane w metadanych Federacji niezależnych od dzierżawców, jak pokazano w poniższym przykładzie.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
…
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/common/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/common/saml2" />
  </IDPSSODescriptor>
```
