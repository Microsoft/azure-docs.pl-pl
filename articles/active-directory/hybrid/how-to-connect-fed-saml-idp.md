---
title: 'Azure AD Connect: Użyj dostawcy tożsamości SAML 2,0 do logowania jednokrotnego — Azure'
description: W tym dokumencie opisano użycie dostawcy tożsamości języka SAML 2,0 do logowania jednokrotnego.
services: active-directory
author: billmath
manager: daveba
ms.custom: it-pro
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: cfe3d995cef888d2f0e973a6a6b2a06e0dd6cb54
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97563215"
---
#  <a name="use-a-saml-20-identity-provider-idp-for-single-sign-on"></a>Korzystanie z dostawcy tożsamości SAML 2.0 na potrzeby logowania jednokrotnego

Ten dokument zawiera informacje na temat używania dostawcy tożsamości usługi SAML 2,0 SP-Lite zgodnego z profilem jako preferowanego tokenu zabezpieczającego (STS)/dostawcy tożsamości. Ten scenariusz jest przydatny, jeśli masz już lokalny katalog użytkownika i Magazyn haseł, do których można uzyskać dostęp za pomocą protokołu SAML 2,0. Istniejący katalog użytkownika może służyć do logowania się do Microsoft 365 i innych zasobów zabezpieczonych za pomocą usługi Azure AD. Profil SP-Lite SAML 2,0 jest oparty na powszechnie używanym SAML (SAML) federacyjnym standardem tożsamości w celu udostępnienia środowiska rejestracji i atrybutów wymiany.

>[!NOTE]
>Aby zapoznać się z listą dostawców tożsamości innych firm, które zostały przetestowane do użycia z usługą Azure AD, zobacz [listę zgodności Federacji usługi Azure AD](how-to-connect-fed-compatibility.md)

Firma Microsoft obsługuje to Logowanie jako integrację usługi firmy Microsoft w chmurze, takiej jak Microsoft 365, z prawidłowo skonfigurowanym dostawcy tożsamości opartym na profilach SAML 2,0. Dostawcy tożsamości SAML 2,0 są produktami innych firm, w związku z czym firma Microsoft nie zapewnia pomocy technicznej dotyczącej wdrażania, konfiguracji i rozwiązywania problemów z najlepszymi rozwiązaniami. Po poprawnym skonfigurowaniu integracji z dostawcą tożsamości SAML 2,0 można sprawdzić w celu uzyskania prawidłowej konfiguracji przy użyciu narzędzia Microsoft Connectivity Analyzer, które zostało opisane bardziej szczegółowo. Aby uzyskać więcej informacji na temat dostawcy tożsamości opartego na profilach SAML 2,0 SP-Lite, poprosimy o podawanie tej organizacji.

> [!IMPORTANT]
> Tylko ograniczony zestaw klientów jest dostępny w tym scenariuszu logowania z dostawcami tożsamości SAML 2,0. obejmuje to:
> 
> - Klienci sieci Web, np. Outlook Dostęp w sieci Web i SharePoint Online
> - Klienci z rozbudowanymi pocztą e-mail, którzy korzystają z uwierzytelniania podstawowego i obsługiwanej metody dostępu do programu Exchange, takiej jak IMAP, POP, Active Sync, MAPI itd. (do wdrożenia jest wymagany punkt końcowy rozszerzonego protokołu klienta), w tym:
>     - Microsoft Outlook 2010/Outlook 2013/Outlook 2016, Apple iPhone (różne wersje systemu iOS)
>     - Różne urządzenia Google Android
>     - Windows Phone 7, Windows Phone 7,8 i Windows Phone 8,0
>     - Klient poczty E-mail systemu Windows 8 i klient poczty Windows 8.1
>     - Klient poczty systemu Windows 10

Wszyscy pozostali klienci nie są dostępni w tym scenariuszu logowania za pomocą dostawcy tożsamości SAML 2,0. Na przykład klient programu Lync 2010 Desktop nie może zalogować się do usługi za pomocą dostawcy tożsamości SAML 2,0 skonfigurowanego do logowania jednokrotnego.

## <a name="azure-ad-saml-20-protocol-requirements"></a>Wymagania dotyczące protokołu SAML 2,0 usługi Azure AD
Ten dokument zawiera szczegółowe wymagania dotyczące formatu protokołu i komunikatów, które dostawca tożsamości SAML 2,0 musi zaimplementować, aby sfederować się z usługą Azure AD w celu włączenia logowania do co najmniej jednej usługi w chmurze firmy Microsoft (na przykład Microsoft 365). Jednostka uzależniona SAML 2,0 (SP-STS) dla usługi firmy Microsoft w chmurze używanej w tym scenariuszu jest usługą Azure AD.

Zalecane jest upewnienie się, że komunikaty wyjściowe dostawcy tożsamości SAML 2,0 są podobne do dostarczonych przykładowych śladów. Należy również użyć określonych wartości atrybutów z dostarczonych metadanych usługi Azure AD, jeśli to możliwe. Gdy będziesz zadowolony z wiadomości wyjściowych, możesz przeprowadzić test za pomocą analizatora łączności firmy Microsoft zgodnie z poniższym opisem.

Metadane usługi Azure AD można pobrać z tego adresu URL: [https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml](https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml) .
W przypadku klientów z Chin przy użyciu wystąpienia Microsoft 365 określonego dla Chin należy użyć następującego punktu końcowego Federacji: [https://nexus.partner.microsoftonline-p.cn/federationmetadata/saml20/federationmetadata.xml](https://nexus.partner.microsoftonline-p.cn/federationmetadata/saml20/federationmetadata.xml) .

## <a name="saml-protocol-requirements"></a>Wymagania dotyczące protokołu SAML
W tej sekcji szczegółowo opisano, jak pary komunikatów żądania i odpowiedzi są umieszczane w celu poprawnego formatowania komunikatów.

Usługę Azure AD można skonfigurować do współpracy z dostawcami tożsamości, które używają profilu SAML 2,0 SP Lite z określonymi wymaganiami wymienionymi poniżej. Przy użyciu przykładowych żądań SAML i komunikatów odpowiedzi wraz z automatycznym i ręcznym testowaniem można współpracować, aby osiągnąć współdziałanie z usługą Azure AD.

## <a name="signature-block-requirements"></a>Wymagania bloku podpisu
W komunikacie odpowiedzi SAML węzeł podpisu zawiera informacje o podpisie cyfrowym samego komunikatu. Blok podpisu ma następujące wymagania:

1. Sam węzeł potwierdzenia musi być podpisany
2.  Algorytm RSA-SHA1 musi być używany jako DigestMethod. Inne algorytmy podpisu cyfrowego nie są akceptowane.
   `<ds:DigestMethod Algorithm="https://www.w3.org/2000/09/xmldsig#sha1"/>`
3.  Możesz również podpisać dokument XML. 
4.  Algorytm przekształcania musi pasować do wartości w następującym przykładzie:    `<ds:Transform Algorithm="https://www.w3.org/2000/09/xmldsig#enveloped-signature"/>
       <ds:Transform Algorithm="https://www.w3.org/2001/10/xml-exc-c14n#"/>`
9.  Algorytm SignatureMethod musi być zgodny z następującym przykładem:   `<ds:SignatureMethod Algorithm="https://www.w3.org/2000/09/xmldsig#rsa-sha1"/>`

## <a name="supported-bindings"></a>Obsługiwane powiązania
Powiązania to parametry komunikacji związane z transportem, które są wymagane. Poniższe wymagania dotyczą powiązań

1. Port HTTPS jest wymagany.
2.  Usługa Azure AD będzie wymagała WYSŁANia protokołu HTTP w celu przesłania tokenu podczas logowania.
3.  Usługa Azure AD będzie używać wpisu HTTP POST do żądania uwierzytelnienia do dostawcy tożsamości i PRZEKIEROWUJe komunikat o wylogowaniu do dostawcy tożsamości.

## <a name="required-attributes"></a>Wymagane atrybuty
W tej tabeli przedstawiono wymagania dotyczące określonych atrybutów w komunikacie SAML 2,0.
 
|Atrybut|Opis|
| ----- | ----- |
|NameID|Wartość tego potwierdzenia musi być taka sama jak ImmutableID użytkownika usługi Azure AD. Może to być maksymalnie 64 znaków alfanumerycznych. Wszelkie bezpieczne znaki niebędące w formacie HTML należy zakodować, na przykład znak "+" jest wyświetlany jako ". 2B".|
|IDPEmail|Główna nazwa użytkownika (UPN) jest wymieniona w odpowiedzi SAML jako element o nazwie IDPEmail użytkownika (UPN) w usłudze Azure AD/Microsoft 365. Nazwa UPN jest w formacie adresu e-mail. Wartość nazwy UPN w systemie Windows Microsoft 365 (Azure Active Directory).|
|Wystawca|Wymagany jako identyfikator URI dostawcy tożsamości. Nie używaj ponownie wystawcy z komunikatów przykładowych. Jeśli masz wiele domen najwyższego poziomu w dzierżawach usługi Azure AD, wystawca musi być zgodny z określonym ustawieniem URI skonfigurowanym dla każdej domeny.|

>[!IMPORTANT]
>Usługa Azure AD obecnie obsługuje następujący identyfikator URI NameID formatu dla protokołu SAML 2.0: urn: języka Oasis: names: TC: SAML: 2.0: NameID-format: persistent.

## <a name="sample-saml-request-and-response-messages"></a>Przykładowe komunikaty żądania SAML i odpowiedzi
Para komunikatów żądanie i odpowiedź jest pokazywana w przypadku wymiany komunikatów logowania.
Poniżej przedstawiono przykładowy komunikat żądania, który jest wysyłany z usługi Azure AD do przykładowego dostawcy tożsamości SAML 2,0. Przykładowy dostawca tożsamości SAML 2,0 to Active Directory Federation Services (AD FS) skonfigurowany do korzystania z protokołu SAML-P. Testy współdziałania zostały również wykonane z innymi dostawcami tożsamości SAML 2,0.

```xml
    <samlp:AuthnRequest 
        xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol" 
        xmlns:saml="urn:oasis:names:tc:SAML:2.0:assertion" 
        ID="_7171b0b2-19f2-4ba2-8f94-24b5e56b7f1e" 
        IssueInstant="2014-01-30T16:18:35Z" 
        Version="2.0" 
        AssertionConsumerServiceIndex="0" >
            <saml:Issuer>urn:federation:MicrosoftOnline</saml:Issuer>
            <samlp:NameIDPolicy Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent"/>
    </samlp:AuthnRequest>
```

Poniżej znajduje się przykładowy komunikat odpowiedzi, który jest wysyłany z przykładowego dostawcy tożsamości protokołu SAML 2,0 do usługi Azure AD/Microsoft 365.

```xml
    <samlp:Response ID="_592c022f-e85e-4d23-b55b-9141c95cd2a5" Version="2.0" IssueInstant="2014-01-31T15:36:31.357Z" Destination="https://login.microsoftonline.com/login.srf" Consent="urn:oasis:names:tc:SAML:2.0:consent:unspecified" InResponseTo="_049917a6-1183-42fd-a190-1d2cbaf9b144" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
    <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">http://WS2012R2-0.contoso.com/adfs/services/trust</Issuer>
    <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
    </samlp:Status>
    <Assertion ID="_7e3c1bcd-f180-4f78-83e1-7680920793aa" IssueInstant="2014-01-31T15:36:31.279Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">
    <Issuer>http://WS2012R2-0.contoso.com/adfs/services/trust</Issuer>
    <ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
      <ds:SignedInfo>
        <ds:CanonicalizationMethod Algorithm="https://www.w3.org/2001/10/xml-exc-c14n#" />
        <ds:SignatureMethod Algorithm="https://www.w3.org/2000/09/xmldsig#rsa-sha1" />
        <ds:Reference URI="#_7e3c1bcd-f180-4f78-83e1-7680920793aa">
          <ds:Transforms>
            <ds:Transform Algorithm="https://www.w3.org/2000/09/xmldsig#enveloped-signature" />
            <ds:Transform Algorithm="https://www.w3.org/2001/10/xml-exc-c14n#" />
          </ds:Transforms>
          <ds:DigestMethod Algorithm="https://www.w3.org/2000/09/xmldsig#sha1" />
          <ds:DigestValue>CBn/5YqbheaJP425c0pHva9PhNY=</ds:DigestValue>
        </ds:Reference>
      </ds:SignedInfo>
      <ds:SignatureValue>TciWMyHW2ZODrh/2xrvp5ggmcHBFEd9vrp6DYXp+hZWJzmXMmzwmwS8KNRJKy8H7XqBsdELA1Msqi8I3TmWdnoIRfM/ZAyUppo8suMu6Zw+boE32hoQRnX9EWN/f0vH6zA/YKTzrjca6JQ8gAV1ErwvRWDpyMcwdYCiWALv9ScbkAcebOE1s1JctZ5RBXggdZWrYi72X+I4i6WgyZcIGai/rZ4v2otoWAEHS0y1yh1qT7NDPpl/McDaTGkNU6C+8VfjD78DrUXEcAfKvPgKlKrOMZnD1lCGsViimGY+LSuIdY45MLmyaa5UT4KWph6dA==</ds:SignatureValue>
      <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
        <ds:X509Data>
          <ds:X509Certificate>MIIC7jCCAdagAwIBAgIQRrjsbFPaXIlOG3GTv50fkjANBgkqhkiG9w0BAQsFADAzMTEwLwYDVQQDEyhBREZTIFNpZ25pbmcgLSBXUzIwMTJSMi0wLnN3aW5mb3JtZXIuY29tMB4XDTE0MDEyMDE1MTY0MFoXDTE1MDEyMDE1MTY0MFowMzExMC8GA1UEAxMoQURGUyBTaWduaW5nIC0gV1MyMDEyUjItMC5zd2luZm9ybWVyLmNvbTCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAKe+rLVmXy1QwCwZwqgbbp1/+3ZWxd9T/jV0hpLIIWr+LCOHqq8n8beJvlivgLmDJo8f+EITnAxWcsJUvVai/35AhHCUq9tc9sqMp5PWtabAEMb2AU72/QlX/72D2/NbGQq1BWYbqUpgpCZ2nSgvlWDHlCiUo//UGsvfox01kjTFlmqQInsJVfRxF5AcCAwEAATANBgkqhkiG9w0BAQsFAAOCAQEAi8c6C4zaTEc7aQiUgvnGQgCbMZbhUXXLGRpjvFLKaQzkwa9eq7WLJibcSNyGXBa/SfT5wJgsm3TPKgSehGAOTirhcqHheZyvBObAScY7GOT+u9pVYp6raFrc7ez3c+CGHeV/tNvy1hJNs12FYH4X+ZCNFIT9tprieR25NCdi5SWUbPZL0tVzJsHc1y92b2M2FxqRDohxQgJvyJOpcg2mSBzZZIkvDg7gfPSUXHVS1MQs0RHSbwq/XdQocUUhl9/e/YWCbNNxlM84BxFsBUok1dH/gzBySx+Fc8zYi7cOq9yaBT3RLT6cGmFGVYZJW4FyhPZOCLVNsLlnPQcX3dDg9A==</ds:X509Certificate>
        </ds:X509Data>
      </KeyInfo>
    </ds:Signature>
    <Subject>
      <NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent">ABCDEG1234567890</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="_049917a6-1183-42fd-a190-1d2cbaf9b144" NotOnOrAfter="2014-01-31T15:41:31.357Z" Recipient="https://login.microsoftonline.com/login.srf" />
      </SubjectConfirmation>
    </Subject>
    <Conditions NotBefore="2014-01-31T15:36:31.263Z" NotOnOrAfter="2014-01-31T16:36:31.263Z">
      <AudienceRestriction>
        <Audience>urn:federation:MicrosoftOnline</Audience>
      </AudienceRestriction>
    </Conditions>
    <AttributeStatement>
      <Attribute Name="IDPEmail">
        <AttributeValue>administrator@contoso.com</AttributeValue>
      </Attribute>
    </AttributeStatement>
    <AuthnStatement AuthnInstant="2014-01-31T15:36:30.200Z" SessionIndex="_7e3c1bcd-f180-4f78-83e1-7680920793aa">
      <AuthnContext>
        <AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport</AuthnContextClassRef>
      </AuthnContext>
    </AuthnStatement>
    </Assertion>
    </samlp:Response>
```

## <a name="configure-your-saml-20-compliant-identity-provider"></a>Konfigurowanie dostawcy tożsamości zgodnego z protokołem SAML 2,0
Ta sekcja zawiera wskazówki dotyczące sposobu konfigurowania dostawcy tożsamości SAML 2,0 do sfederować za pomocą usługi Azure AD w celu umożliwienia logowania jednokrotnego do co najmniej jednej usługi w chmurze firmy Microsoft (na przykład Microsoft 365) przy użyciu protokołu SAML 2,0. Jednostką uzależnioną SAML 2,0 dla usługi firmy Microsoft w chmurze używanej w tym scenariuszu jest usługa Azure AD.

## <a name="add-azure-ad-metadata"></a>Dodawanie metadanych usługi Azure AD
Dostawca tożsamości SAML 2,0 musi być zgodny z informacjami o jednostce uzależnionej usługi Azure AD. Usługa Azure AD publikuje metadane pod adresem https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml .

Zaleca się, aby zawsze zaimportować najnowsze metadane usługi Azure AD podczas konfigurowania dostawcy tożsamości SAML 2,0.

>[!NOTE]
>Usługa Azure AD nie odczytuje metadanych od dostawcy tożsamości.

## <a name="add-azure-ad-as-a-relying-party"></a>Dodaj usługę Azure AD jako jednostkę uzależnioną
Musisz włączyć komunikację między dostawcą tożsamości SAML 2,0 i usługą Azure AD. Ta konfiguracja będzie zależna od konkretnego dostawcy tożsamości i należy zapoznać się z dokumentacją. Identyfikator jednostki uzależnionej jest zazwyczaj ustawiany na taki sam jak entityID z metadanych usługi Azure AD.

>[!NOTE]
>Sprawdź, czy zegar serwera dostawcy tożsamości SAML 2,0 został zsynchronizowany ze szczegółowym źródłem czasu. Niedokładny czas zegara może spowodować niepowodzenie logowań federacyjnych.

## <a name="install-windows-powershell-for-sign-on-with-saml-20-identity-provider"></a>Instalowanie programu Windows PowerShell na potrzeby logowania za pomocą dostawcy tożsamości SAML 2,0
Po skonfigurowaniu dostawcy tożsamości SAML 2,0 do użycia z logowaniem w usłudze Azure AD następnym krokiem jest pobranie i zainstalowanie modułu Azure Active Directory programu Windows PowerShell. Za pomocą tych poleceń cmdlet można skonfigurować domeny usługi Azure AD jako domeny federacyjne.

Moduł Azure Active Directory środowiska Windows PowerShell jest pobierany do zarządzania danymi organizacji w usłudze Azure AD. Ten moduł instaluje zestaw poleceń cmdlet programu Windows PowerShell. te polecenia cmdlet umożliwiają skonfigurowanie dostępu do usługi Azure AD przy użyciu logowania jednokrotnego i z kolei do wszystkich subskrybowanych usług w chmurze. Aby uzyskać instrukcje dotyczące pobierania i instalowania poleceń cmdlet, zobacz [/Previous-Versions/Azure/jj151815 (v = Azure. 100)](/previous-versions/azure/jj151815(v=azure.100))

## <a name="set-up-a-trust-between-your-saml-identity-provider-and-azure-ad"></a>Konfigurowanie zaufania między dostawcą tożsamości SAML i usługą Azure AD
Przed skonfigurowaniem Federacji w domenie usługi Azure AD musi mieć skonfigurowaną domenę niestandardową. Nie można sfederować domeny domyślnej dostarczonej przez firmę Microsoft. Domena domyślna od firmy Microsoft zostaje zakończona ciągiem "onmicrosoft.com".
W interfejsie wiersza polecenia programu Windows PowerShell zostanie uruchomiony szereg poleceń cmdlet służących do dodawania lub konwertowania domen w celu logowania jednokrotnego.

Każda domena Azure Active Directory, która ma zostać sfederować przy użyciu dostawcy tożsamości SAML 2,0, musi być dodana jako domena logowania jednokrotnego lub przekonwertowana jako domena jednokrotnego logowania z domeny standardowej. Dodanie lub przekonwertowanie domeny powoduje skonfigurowanie relacji zaufania między dostawcą tożsamości SAML 2,0 i usługą Azure AD.

Poniższa procedura przeprowadzi Cię przez konwersję istniejącej domeny standardowej do domeny federacyjnej przy użyciu protokołu SAML 2,0 SP-Lite. 

>[!NOTE]
>W domenie może wystąpić awaria, która ma wpływ na użytkowników przez maksymalnie 2 godziny po wykonaniu tego kroku.

## <a name="configuring-a-domain-in-your-azure-ad-directory-for-federation"></a>Konfigurowanie domeny w katalogu usługi Azure AD dla Federacji


1. Połącz się z katalogiem usługi Azure AD jako Administrator dzierżawy:

    ```powershell
    Connect-MsolService
    ```
    
2. Skonfiguruj żądaną domenę Microsoft 365, aby użyć Federacji z SAML 2,0:

    ```powershell
    $dom = "contoso.com" 
    $BrandName - "Sample SAML 2.0 IDP" 
    $LogOnUrl = "https://WS2012R2-0.contoso.com/passiveLogon" 
    $LogOffUrl = "https://WS2012R2-0.contoso.com/passiveLogOff" 
    $ecpUrl = "https://WS2012R2-0.contoso.com/PAOS" 
    $MyURI = "urn:uri:MySamlp2IDP" 
    $MySigningCert = "MIIC7jCCAdagAwIBAgIQRrjsbFPaXIlOG3GTv50fkjANBgkqhkiG9w0BAQsFADAzMTEwLwYDVQQDEyh BREZTIFNpZ25pbmcgLSBXUzIwMTJSMi0wLnN3aW5mb3JtZXIuY29tMB4XDTE0MDEyMDE1MTY0MFoXDT E1MDEyMDE1MTY0MFowMzExMC8GA1UEAxMoQURGUyBTaWduaW5nIC0gV1MyMDEyUjItMC5zd2luZm9yb WVyLmNvbTCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAKe+rLVmXy1QwCwZwqgbbp1/kupQ VcjKuKLitVDbssFyqbDTjP7WRjlVMWAHBI3kgNT7oE362Gf2WMJFf1b0HcrsgLin7daRXpq4Qi6OA57 sW1YFMj3sqyuTP0eZV3S4+ZbDVob6amsZIdIwxaLP9Zfywg2bLsGnVldB0+XKedZwDbCLCVg+3ZWxd9 T/jV0hpLIIWr+LCOHqq8n8beJvlivgLmDJo8f+EITnAxWcsJUvVai/35AhHCUq9tc9sqMp5PWtabAEM b2AU72/QlX/72D2/NbGQq1BWYbqUpgpCZ2nSgvlWDHlCiUo//UGsvfox01kjTFlmqQInsJVfRxF5AcC AwEAATANBgkqhkiG9w0BAQsFAAOCAQEAi8c6C4zaTEc7aQiUgvnGQgCbMZbhUXXLGRpjvFLKaQzkwa9 eq7WLJibcSNyGXBa/SfT5wJgsm3TPKgSehGAOTirhcqHheZyvBObAScY7GOT+u9pVYp6raFrc7ez3c+ CGHeV/tNvy1hJNs12FYH4X+ZCNFIT9tprieR25NCdi5SWUbPZL0tVzJsHc1y92b2M2FxqRDohxQgJvy JOpcg2mSBzZZIkvDg7gfPSUXHVS1MQs0RHSbwq/XdQocUUhl9/e/YWCbNNxlM84BxFsBUok1dH/gzBy Sx+Fc8zYi7cOq9yaBT3RLT6cGmFGVYZJW4FyhPZOCLVNsLlnPQcX3dDg9A==" 
    $uri = "http://WS2012R2-0.contoso.com/adfs/services/trust" 
    $Protocol = "SAMLP" 
    Set-MsolDomainAuthentication `
        -DomainName $dom `
        -FederationBrandName $BrandName `
        -Authentication Federated `
        -PassiveLogOnUri $LogOnUrl `
        -ActiveLogOnUri $ecpUrl `
        -SigningCertificate $MySigningCert `
        -IssuerUri $MyURI `
        -LogOffUri $LogOffUrl `
        -PreferredAuthenticationProtocol $Protocol
    ``` 

3.  W pliku metadanych dostawcy tożsamości można uzyskać zakodowany ciąg Base64 certyfikatu podpisywania. Przedstawiono przykład tej lokalizacji, ale może się nieco różnić w zależności od implementacji.

    ```xml
    <IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
        <KeyDescriptor use="signing">
          <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
             <X509Data>
                 <X509Certificate> MIIC5jCCAc6gAwIBAgIQLnaxUPzay6ZJsC8HVv/QfTANBgkqhkiG9w0BAQsFADAvMS0wKwYDVQQDEyRBREZTIFNpZ25pbmcgLSBmcy50ZWNobGFiY2VudHJhbC5vcmcwHhcNMTMxMTA0MTgxMzMyWhcNMTQxMTA0MTgxMzMyWjAvMS0wKwYDVQQDEyRBREZTIFNpZ25pbmcgLSBmcy50ZWNobGFiY2VudHJhbC5vcmcwggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQCwMdVLTr5YTSRp+ccbSpuuFeXMfABD9mVCi2wtkRwC30TIyPdORz642MkurdxdPCWjwgJ0HW6TvXwcO9afH3OC5V//wEGDoNcI8PV4enCzTYFe/h//w51uqyv48Fbb3lEXs+aVl8155OAj2sO9IX64OJWKey82GQWK3g7LfhWWpp17j5bKpSd9DBH5pvrV+Q1ESU3mx71TEOvikHGCZYitEPywNeVMLRKrevdWI3FAhFjcCSO6nWDiMqCqiTDYOURXIcHVYTSof1YotkJ4tG6mP5Kpjzd4VQvnR7Pjb47nhIYG6iZ3mR1F85Ns9+hBWukQWNN2hcD/uGdPXhpdMVpBAgMBAAEwDQYJKoZIhvcNAQELBQADggEBAK7h7jF7wPzhZ1dPl4e+XMAr8I7TNbhgEU3+oxKyW/IioQbvZVw1mYVCbGq9Rsw4KE06eSMybqHln3w5EeBbLS0MEkApqHY+p68iRpguqa+W7UHKXXQVgPMCpqxMFKonX6VlSQOR64FgpBme2uG+LJ8reTgypEKspQIN0WvtPWmiq4zAwBp08hAacgv868c0MM4WbOYU0rzMIR6Q+ceGVRImlCwZ5b7XKp4mJZ9hlaRjeuyVrDuzBkzROSurX1OXoci08yJvhbtiBJLf3uPOJHrhjKRwIt2TnzS9ElgFZlJiDIA26Athe73n43CT0af2IG6yC7e6sK4L3NEXJrwwUZk=</X509Certificate>
              </X509Data>
            </KeyInfo>
        </KeyDescriptor>
    </IDPSSODescriptor>
    ``` 

Aby uzyskać więcej informacji na temat elementu "Set-MsolDomainAuthentication", zobacz: [/Previous-Versions/Azure/dn194112 (v = Azure. 100)](/previous-versions/azure/dn194112(v=azure.100)).

>[!NOTE]
>Należy używać `$ecpUrl = "https://WS2012R2-0.contoso.com/PAOS"` tylko wtedy, gdy skonfigurowano rozszerzenie ECP dla dostawcy tożsamości. Klienci usługi Exchange Online, z wyłączeniem aplikacji sieci Web programu Outlook (OWA), bazują na aktywnym punkcie końcowym. Jeśli usługa STS protokołu SAML 2,0 implementuje aktywny punkt końcowy podobny do implementacji ECP Shibboleth w aktywnym punkcie końcowym, może być możliwe, aby Ci rozbudowani klienci mogli korzystać z usługi Exchange Online.

Po skonfigurowaniu Federacji można przełączyć się z powrotem do "niefederacyjnych" (lub "zarządzane"), jednak ta zmiana będzie trwała do dwóch godzin i wymaga przypisywania nowych losowych haseł dla logowania opartego na chmurze dla każdego użytkownika. Przełączenie z powrotem do "zarządzanego" może być wymagane w niektórych scenariuszach w celu zresetowania błędu w ustawieniach. Aby uzyskać więcej informacji na temat konwersji domen, zobacz: [/Previous-Versions/Azure/dn194122 (v = Azure. 100)](/previous-versions/azure/dn194122(v=azure.100)).

## <a name="provision-user-principals-to-azure-ad--microsoft-365"></a>Inicjowanie obsługi administracyjnej podmiotów użytkownika w usłudze Azure AD/Microsoft 365
Aby można było uwierzytelnić użytkowników w Microsoft 365, należy zainicjować obsługę administracyjną usługi Azure AD z podmiotami zabezpieczeń użytkownika odpowiadającymi potwierdzeniom w twierdzeniu SAML 2,0. Jeśli te podmioty użytkownika nie są z wyprzedzeniem znane z usługi Azure AD, nie mogą być używane do logowania federacyjnego. Do aprowizacji podmiotów zabezpieczeń można użyć programu Azure AD Connect lub Windows PowerShell.

Azure AD Connect może służyć do aprowizacji podmiotów zabezpieczeń w domenach w katalogu usługi Azure AD z Active Directory lokalnych. Aby uzyskać bardziej szczegółowe informacje, zobacz [integrowanie katalogów lokalnych z Azure Active Directory](whatis-hybrid-identity.md).

Program Windows PowerShell może być również używany do automatyzowania dodawania nowych użytkowników do usługi Azure AD i synchronizowania zmian z katalogu lokalnego. Aby użyć poleceń cmdlet programu Windows PowerShell, należy pobrać [moduły Azure Active Directory](/powershell/azure/active-directory/install-adv2).

Ta procedura pokazuje, jak dodać jednego użytkownika do usługi Azure AD.


1. Połącz się z katalogiem usługi Azure AD jako Administrator dzierżawy: Connect-MsolService.
2. Utwórz nowego podmiotu użytkownika:

    ```powershell
    New-MsolUser `
      -UserPrincipalName elwoodf1@contoso.com `
      -ImmutableId ABCDEFG1234567890 `
      -DisplayName "Elwood Folk" `
      -FirstName Elwood `
      -LastName Folk `
      -AlternateEmailAddresses "Elwood.Folk@contoso.com" `
      -LicenseAssignment "samlp2test:ENTERPRISEPACK" `
      -UsageLocation "US" 
    ```

Aby uzyskać więcej informacji na temat wyewidencjonowania "New-MsolUser", [/Previous-Versions/Azure/dn194096 (v = Azure. 100)](/previous-versions/azure/dn194096(v=azure.100))

>[!NOTE]
>Wartość "UserPrincipalName" musi być zgodna z wartością, która zostanie wysłana dla elementu "IDPEmail" w ramach żądania SAML 2,0, a wartość "ImmutableID" musi być zgodna z wartością wysłaną w potwierdzeniu "NameID".

## <a name="verify-single-sign-on-with-your-saml-20-idp"></a>Weryfikowanie logowania jednokrotnego przy użyciu protokołu SAML 2,0 dostawcy tożsamości
Jako administrator przed sprawdzeniem i zarządzaniem logowaniem jednokrotnym (nazywanego również Federacją tożsamością) zapoznaj się z informacjami i wykonaj kroki opisane w poniższych artykułach, aby skonfigurować Logowanie jednokrotne za pomocą dostawcy tożsamości na podstawie SP-Lite SAML 2,0:


1.  Zawarto przegląd wymagań dotyczących protokołu SAML 2,0 usługi Azure AD
2.  Skonfigurowano dostawcę tożsamości SAML 2,0
3.  Instalowanie programu Windows PowerShell na potrzeby logowania jednokrotnego przy użyciu dostawcy tożsamości SAML 2,0
4.  Konfigurowanie zaufania między dostawcą tożsamości SAML 2,0 i usługą Azure AD
5.  Poznanie znanego podmiotu użytkownika testowego do Azure Active Directory (Microsoft 365) za pomocą programu Windows PowerShell lub Azure AD Connect.
6.  Skonfiguruj synchronizację katalogów za pomocą [Azure AD Connect](whatis-hybrid-identity.md).

Po skonfigurowaniu logowania jednokrotnego za pomocą dostawcy tożsamości opartego na protokole SAML 2,0 SP-Lite należy sprawdzić, czy działa poprawnie.

>[!NOTE]
>Jeśli przeprowadzono konwersję domeny, a nie dodasz jej, może upłynąć nawet 24 godziny, aby skonfigurować Logowanie jednokrotne.
Przed zweryfikowaniem rejestracji jednokrotnej należy zakończyć konfigurowanie Active Directory synchronizacji, zsynchronizować katalogi i aktywować użytkowników, którzy zostały zsynchronizowani.

### <a name="use-the-tool-to-verify-that-single-sign-on-has-been-set-up-correctly"></a>Użyj narzędzia, aby sprawdzić, czy logowanie jednokrotne zostało skonfigurowane prawidłowo
Aby sprawdzić, czy logowanie jednokrotne zostało skonfigurowane prawidłowo, możesz wykonać poniższą procedurę w celu potwierdzenia, że możesz zalogować się do usługi w chmurze przy użyciu poświadczeń firmowych.

Firma Microsoft udostępniła narzędzie, którego można użyć do przetestowania dostawcy tożsamości opartego na protokole SAML 2,0. Przed uruchomieniem narzędzia testowego musisz mieć skonfigurowaną dzierżawę usługi Azure AD do sfederować z dostawcą tożsamości.

>[!NOTE]
>Analizator łączności wymaga programu Internet Explorer 10 lub nowszego.



1. Pobierz Analizator łączności z, [https://testconnectivity.microsoft.com/?tabid=Client](https://testconnectivity.microsoft.com/?tabid=Client) .
2.  Kliknij przycisk Zainstaluj teraz, aby rozpocząć pobieranie i Instalowanie narzędzia.
3.  Wybierz pozycję "nie mogę skonfigurować Federacji z pakietem Office 365, platformą Azure ani innymi usługami korzystającymi z Azure Active Directory".
4.  Po pobraniu i uruchomieniu narzędzia zostanie wyświetlone okno Diagnostyka łączności. Narzędzie przeprowadzi Cię przez testowanie połączenia federacyjnego.
5.  Analizator łączności otworzy dostawcy tożsamości SAML 2,0, aby móc się zalogować, wprowadzić poświadczenia dla podmiotu zabezpieczeń, które są testowane: ![ zrzut ekranu przedstawiający okno logowania dla dostawcy tożsamości SAML 2,0.](./media/how-to-connect-fed-saml-idp/saml1.png)
6.  W oknie logowania do testu Federacji należy wprowadzić nazwę konta i hasło dla dzierżawy usługi Azure AD skonfigurowanej do federacyjnego z dostawcą tożsamości SAML 2,0. Narzędzie podejmie próbę zalogowania się przy użyciu tych poświadczeń i szczegółowe wyniki testów wykonywanych podczas próby logowania będą przekazywane jako dane wyjściowe.
![SAML](./media/how-to-connect-fed-saml-idp/saml2.png)
7. To okno pokazuje wynik testu zakończony niepowodzeniem. Kliknięcie pozycji Przejrzyj szczegółowe wyniki spowoduje wyświetlenie informacji na temat wyników dla każdego wykonanego testu. Możesz również zapisać wyniki na dysku, aby je udostępnić.
 
>[!NOTE]
>Analizator łączności testuje również aktywną Federacji przy użyciu protokołów opartych na protokole WS * i ECP/PAOS. Jeśli nie używasz tych funkcji, możesz zignorować następujący błąd: testowanie aktywnego przepływu logowania przy użyciu aktywnego federacyjnego Federacji dostawcy tożsamości.

### <a name="manually-verify-that-single-sign-on-has-been-set-up-correctly"></a>Ręcznie sprawdź, czy logowanie jednokrotne zostało skonfigurowane prawidłowo
Weryfikacja ręczna zapewnia dodatkowe czynności, które można wykonać, aby zapewnić, że dostawca tożsamości SAML 2,0 działa prawidłowo w wielu scenariuszach.
Aby sprawdzić, czy logowanie jednokrotne zostało skonfigurowane prawidłowo, wykonaj następujące czynności:


1. Na komputerze przyłączonym do domeny Zaloguj się do usługi w chmurze przy użyciu tej samej nazwy logowania, która jest używana dla poświadczeń firmowych.
2.  Kliknij wewnątrz pola hasło. Jeśli skonfigurowano Logowanie jednokrotne, hasło zostanie zacieniowane i zostanie wyświetlony następujący komunikat: "teraz wymagane jest zalogowanie się w &lt; firmie &gt; ".
3.  Kliknij link Zaloguj się w &lt; firmie &gt; . Jeśli można się zalogować, zostanie skonfigurowane Logowanie jednokrotne.

## <a name="next-steps"></a>Następne kroki


- [Active Directory Federation Services zarządzanie i dostosowywanie za pomocą Azure AD Connect](how-to-connect-fed-management.md)
- [Lista zgodności usługi Azure AD z usługami federacyjnymi](how-to-connect-fed-compatibility.md)
- [Azure AD Connect instalacji niestandardowej](how-to-connect-install-custom.md)
