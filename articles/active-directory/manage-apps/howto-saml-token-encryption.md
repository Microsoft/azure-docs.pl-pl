---
title: Szyfrowanie tokenów SAML w Azure Active Directory
description: Dowiedz się, jak skonfigurować Azure Active Directory tokenu SAML.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: iangithinji
ms.reviewer: paulgarn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5c06a499cccb03e6726ee19542d7eb79e0c99b43
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375733"
---
# <a name="how-to-configure-azure-ad-saml-token-encryption"></a>2. Konfigurowanie szyfrowania tokenów SAML usługi Azure AD

> [!NOTE]
> Szyfrowanie tokenów jest funkcją Azure Active Directory (Azure AD) Premium. Aby dowiedzieć się więcej o wersjach, funkcjach i cenach usługi Azure AD, zobacz [Cennik usługi Azure AD.](https://azure.microsoft.com/pricing/details/active-directory/)

Szyfrowanie tokenów SAML umożliwia korzystanie z zaszyfrowanych asercji SAML z aplikacją, która je obsługuje. Po skonfigurowaniu dla aplikacji usługa Azure AD szyfruje asercji SAML emitowanych dla tej aplikacji przy użyciu klucza publicznego uzyskanego z certyfikatu przechowywanego w usłudze Azure AD. Aplikacja musi użyć pasującego klucza prywatnego do odszyfrowania tokenu, zanim będzie można go użyć jako dowodu uwierzytelniania zalogowanego użytkownika.

Szyfrowanie asercji SAML między usługą Azure AD i aplikacją zapewnia dodatkową pewność, że nie można przechwycić zawartości tokenu, a dane osobiste lub firmowe zostaną naruszone.

Nawet bez szyfrowania tokenów tokeny SAML usługi Azure AD nigdy nie są przekazywane w sieci w sposób jasny. Usługa Azure AD wymaga, aby wymiana żądań/odpowiedzi tokenu odbywała się za pośrednictwem szyfrowanych kanałów HTTPS/TLS, tak aby komunikacja między dostawcy tożsamości, przeglądarką i aplikacją odbywała się za pośrednictwem zaszyfrowanych linków. Weź pod uwagę wartość szyfrowania tokenu w swojej sytuacji w porównaniu z obciążeniem zarządzania dodatkowymi certyfikatami.   

Aby skonfigurować szyfrowanie tokenu, należy przekazać plik certyfikatu X.509 zawierający klucz publiczny do obiektu aplikacji usługi Azure AD, który reprezentuje aplikację. Aby uzyskać certyfikat X.509, możesz pobrać go z samej aplikacji lub pobrać od dostawcy aplikacji w przypadkach, gdy dostawca aplikacji udostępnia klucze szyfrowania lub w przypadkach, gdy aplikacja oczekuje podania klucza prywatnego, można go utworzyć przy użyciu narzędzi kryptografii, części klucza prywatnego przekazanego do magazynu kluczy aplikacji i pasującego certyfikatu klucza publicznego przekazanego do usługi Azure AD.

Usługa Azure AD używa protokołu AES-256 do szyfrowania danych asercji SAML.

## <a name="configure-saml-token-encryption"></a>Konfigurowanie szyfrowania tokenu SAML

Aby skonfigurować szyfrowanie tokenu SAML, wykonaj następujące kroki:

1. Uzyskaj certyfikat klucza publicznego, który pasuje do klucza prywatnego skonfigurowanego w aplikacji.

    Utwórz parę kluczy asymetrycznych do użycia na użytek szyfrowania. Jeśli natomiast aplikacja dostarcza klucz publiczny do szyfrowania, postępuj zgodnie z instrukcjami aplikacji, aby pobrać certyfikat X.509.

    Klucz publiczny powinien być przechowywany w pliku certyfikatu X.509 w formacie cer.

    Jeśli aplikacja używa klucza, który utworzysz dla wystąpienia, postępuj zgodnie z instrukcjami dostarczonymi przez aplikację w celu zainstalowania klucza prywatnego, który będzie używany przez aplikację do odszyfrowywania tokenów z dzierżawy usługi Azure AD.

1. Dodaj certyfikat do konfiguracji aplikacji w usłudze Azure AD.

### <a name="to-configure-token-encryption-in-the-azure-portal"></a>Aby skonfigurować szyfrowanie tokenu w Azure Portal

Certyfikat publiczny można dodać do konfiguracji aplikacji w Azure Portal.

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).

1. Przejdź do bloku **Azure Active Directory > aplikacje dla przedsiębiorstw,** a następnie wybierz aplikację, dla których chcesz skonfigurować szyfrowanie tokenu.

1. Na stronie aplikacji wybierz pozycję **Szyfrowanie tokenu.**

    ![Opcja szyfrowania tokenu w Azure Portal](./media/howto-saml-token-encryption/token-encryption-option-small.png)

    > [!NOTE]
    > Opcja **Szyfrowanie tokenu** jest dostępna tylko dla aplikacji SAML, które zostały ustawione w bloku Aplikacje dla przedsiębiorstw w aplikacji Azure Portal z galerii aplikacji lub aplikacji spoza galerii.  W przypadku innych aplikacji ta opcja menu jest wyłączona. W przypadku aplikacji zarejestrowanych za pośrednictwem środowiska **Rejestracje aplikacji** na platformie Azure Portal można skonfigurować szyfrowanie tokenów SAML przy użyciu manifestu aplikacji, za pośrednictwem Microsoft Graph lub programu PowerShell.

1. Na stronie **Szyfrowanie tokenu** wybierz pozycję **Importuj certyfikat,** aby zaimportować plik cer zawierający publiczny certyfikat X.509.

    ![Importowanie pliku cer zawierającego certyfikat X.509](./media/howto-saml-token-encryption/import-certificate-small.png)

1. Po zaimportowaniu certyfikatu i skonfigurowaniu klucza prywatnego do użycia po stronie aplikacji aktywuj szyfrowanie, wybierając pozycję **...** obok stanu odcisku palca, a następnie wybierz pozycję Aktywuj szyfrowanie **tokenu** z opcji z menu rozwijanego.

1. Wybierz **pozycję Tak,** aby potwierdzić aktywację certyfikatu szyfrowania tokenu.

1. Upewnij się, że asercji SAML emitowanych dla aplikacji są szyfrowane.

### <a name="to-deactivate-token-encryption-in-the-azure-portal"></a>Aby dezaktywować szyfrowanie tokenu w Azure Portal

1. W Azure Portal przejdź do aplikacji **Azure Active Directory > Enterprise,** a następnie wybierz aplikację z włączonym szyfrowaniem tokenów SAML.

1. Na stronie aplikacji wybierz pozycję **Szyfrowanie tokenu,** znajdź certyfikat, a następnie wybierz opcję **...,** aby wyświetlić menu rozwijane.

1. Wybierz pozycję **Dezaktywuj szyfrowanie tokenu.**

## <a name="configure-saml-token-encryption-using-graph-api-powershell-or-app-manifest"></a>Konfigurowanie szyfrowania tokenu SAML przy użyciu interfejs Graph API, programu PowerShell lub manifestu aplikacji

Certyfikaty szyfrowania są przechowywane w obiekcie aplikacji w usłudze Azure AD z `encrypt` tagiem użycia. Można skonfigurować wiele certyfikatów szyfrowania, a ten, który jest aktywny do szyfrowania tokenów, jest identyfikowany przez `tokenEncryptionKeyID` atrybut .

Identyfikator obiektu aplikacji będzie potrzebny do skonfigurowania szyfrowania tokenu przy użyciu Microsoft Graph API lub programu PowerShell. Tę wartość można znaleźć programowo lub przechodząc do  strony Właściwości aplikacji w oknie Azure Portal za pomocą wartości **Identyfikator** obiektu.

Podczas konfigurowania kluczacredential przy użyciu programu Graph, programu PowerShell lub w manifeście aplikacji należy wygenerować identyfikator GUID do użycia dla identyfikatora keyId.

### <a name="to-configure-token-encryption-using-microsoft-graph"></a>Aby skonfigurować szyfrowanie tokenu przy użyciu Microsoft Graph

1. Zaktualizuj aplikacje przy `keyCredentials` użyciu certyfikatu X.509 w celu szyfrowania. W przykładzie poniżej pokazano, jak to zrobić.

    ```
    Patch https://graph.microsoft.com/beta/applications/<application objectid>

    { 
       "keyCredentials":[ 
          { 
             "type":"AsymmetricX509Cert","usage":"Encrypt",
             "keyId":"fdf8c5d8-f727-43fd-beaf-0f1521cf3d35",    (Use a GUID generator to obtain a value for the keyId)
             "key": "MIICADCCAW2gAwIBAgIQ5j9/b+n2Q4pDvQUCcy3…"  (Base64Encoded .cer file)
          }
        ]
    }
    ```

1. Zidentyfikuj certyfikat szyfrowania aktywny do szyfrowania tokenów. W przykładzie poniżej pokazano, jak to zrobić.

    ```
    Patch https://graph.microsoft.com/beta/applications/<application objectid> 

    { 
       "tokenEncryptionKeyId":"fdf8c5d8-f727-43fd-beaf-0f1521cf3d35" (The keyId of the keyCredentials entry to use)
    }
    ```

### <a name="to-configure-token-encryption-using-powershell"></a>Aby skonfigurować szyfrowanie tokenu przy użyciu programu PowerShell

1. Użyj najnowszego modułu Azure AD PowerShell, aby nawiązać połączenie z dzierżawą.

1. Ustaw ustawienia szyfrowania tokenu za pomocą **[polecenia Set-AzureApplication.](/powershell/module/azuread/set-azureadapplication?view=azureadps-2.0-preview&preserve-view=true)**

    ```
    Set-AzureADApplication -ObjectId <ApplicationObjectId> -KeyCredentials "<KeyCredentialsObject>"  -TokenEncryptionKeyId <keyID>
    ```

1. Odczytaj ustawienia szyfrowania tokenu przy użyciu następujących poleceń.

    ```powershell
    $app=Get-AzureADApplication -ObjectId <ApplicationObjectId>
    $app.KeyCredentials
    $app.TokenEncryptionKeyId
    ```

### <a name="to-configure-token-encryption-using-the-application-manifest"></a>Aby skonfigurować szyfrowanie tokenu przy użyciu manifestu aplikacji

1. W Azure Portal przejdź do strony **Azure Active Directory > Rejestracje aplikacji**.

1. Wybierz **Wszystkie aplikacje** z listy rozwijanej, aby wyświetlić wszystkie aplikacje, a następnie wybierz aplikację dla przedsiębiorstw, którą chcesz skonfigurować.

1. Na stronie aplikacji wybierz pozycję **Manifest,** aby edytować [manifest aplikacji.](../develop/reference-app-manifest.md)

1. Ustaw wartość `tokenEncryptionKeyId` atrybutu .

    W poniższym przykładzie pokazano manifest aplikacji skonfigurowany z dwoma certyfikatami szyfrowania, a drugi został wybrany jako aktywny przy użyciu tokenEnryptionKeyId.

    ```json
    { 
      "id": "3cca40e2-367e-45a5-8440-ed94edd6cc35",
      "accessTokenAcceptedVersion": null,
      "allowPublicClient": false,
      "appId": "cb2df8fb-63c4-4c35-bba5-3d659dd81bf1",
      "appRoles": [],
      "oauth2AllowUrlPathMatching": false,
      "createdDateTime": "2017-12-15T02:10:56Z",
      "groupMembershipClaims": "SecurityGroup",
      "informationalUrls": { 
         "termsOfService": null, 
         "support": null, 
         "privacy": null, 
         "marketing": null 
      },
      "identifierUris": [ 
        "https://testapp"
      ],
      "keyCredentials": [ 
        { 
          "customKeyIdentifier": "Tog/O1Hv1LtdsbPU5nPphbMduD=", 
          "endDate": "2039-12-31T23:59:59Z", 
          "keyId": "8be4cb65-59d9-404a-a6f5-3d3fb4030351", 
          "startDate": "2018-10-25T21:42:18Z", 
          "type": "AsymmetricX509Cert", 
          "usage": "Encrypt", 
          "value": <Base64EncodedKeyFile> 
          "displayName": "CN=SAMLEncryptTest" 
        }, 
        {
          "customKeyIdentifier": "U5nPphbMduDmr3c9Q3p0msqp6eEI=",
          "endDate": "2039-12-31T23:59:59Z", 
          "keyId": "6b9c6e80-d251-43f3-9910-9f1f0be2e851",
          "startDate": "2018-10-25T21:42:18Z", 
          "type": "AsymmetricX509Cert", 
          "usage": "Encrypt", 
          "value": <Base64EncodedKeyFile> 
          "displayName": "CN=SAMLEncryptTest2" 
        } 
      ], 
      "knownClientApplications": [], 
      "logoUrl": null, 
      "logoutUrl": null, 
      "name": "Test SAML Application", 
      "oauth2AllowIdTokenImplicitFlow": true, 
      "oauth2AllowImplicitFlow": false, 
      "oauth2Permissions": [], 
      "oauth2RequirePostResponse": false, 
      "orgRestrictions": [], 
      "parentalControlSettings": { 
         "countriesBlockedForMinors": [], 
         "legalAgeGroupRule": "Allow" 
        }, 
      "passwordCredentials": [], 
      "preAuthorizedApplications": [], 
      "publisherDomain": null, 
      "replyUrlsWithType": [], 
      "requiredResourceAccess": [], 
      "samlMetadataUrl": null, 
      "signInUrl": "https://127.0.0.1:444/applications/default.aspx?metadata=customappsso|ISV9.1|primary|z" 
      "signInAudience": "AzureADMyOrg",
      "tags": [], 
      "tokenEncryptionKeyId": "6b9c6e80-d251-43f3-9910-9f1f0be2e851" 
    }  
    ```

## <a name="next-steps"></a>Następne kroki

* Dowiedz [się, jak usługa Azure AD używa protokołu SAML](../develop/active-directory-saml-protocol-reference.md)
* Informacje na temat formatu, charakterystyki zabezpieczeń i zawartości [tokenów JĘZYKA SAML w usłudze Azure AD](../develop/reference-saml-tokens.md)