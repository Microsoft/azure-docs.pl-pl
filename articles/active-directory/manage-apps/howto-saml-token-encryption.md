---
title: Szyfrowanie tokenu SAML w Azure Active Directory
description: Dowiedz się, jak skonfigurować Azure Active Directory szyfrowanie tokenów SAML.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: kenwith
ms.reviewer: paulgarn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 820e2cb0d422597f0e649e6934fd8bb11c1521db
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95997481"
---
# <a name="how-to-configure-azure-ad-saml-token-encryption"></a>Instrukcje: Konfigurowanie szyfrowania tokenów SAML usługi Azure AD

> [!NOTE]
> Szyfrowanie tokenu jest funkcją usługi Azure Active Directory (Azure AD) Premium. Aby dowiedzieć się więcej o wersjach, funkcjach i cenach usługi Azure AD, zobacz [Cennik usługi Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

Szyfrowanie tokenów SAML umożliwia korzystanie z zaszyfrowanego potwierdzenia SAML z aplikacją, która go obsługuje. Po skonfigurowaniu dla aplikacji usługa Azure AD będzie szyfrować potwierdzenia SAML, które emitują dla tej aplikacji, przy użyciu klucza publicznego uzyskanego z certyfikatu przechowywanego w usłudze Azure AD. Aplikacja musi użyć zgodnego klucza prywatnego do odszyfrowania tokenu, zanim będzie mogła zostać użyta jako dowód uwierzytelniania dla zalogowanego użytkownika.

Szyfrowanie potwierdzeń SAML między usługą Azure AD a aplikacją zapewnia dodatkową gwarancję, że nie można przechwycić zawartości tokenu, a dane osobowe lub firmowe zostały naruszone.

Nawet bez szyfrowania tokenu tokeny SAML usługi Azure AD nigdy nie są domyślnie przesyłane do sieci. Usługa Azure AD wymaga wymiany żądania tokenu/odpowiedzi na zaszyfrowane kanały HTTPS/TLS, dzięki czemu komunikacja między usługą dostawcy tożsamości, przeglądarką i aplikacją odbywa się za pośrednictwem linków szyfrowanych. Należy wziąć pod uwagę wartość szyfrowania tokenu dla danej sytuacji w porównaniu z kosztami związanymi z zarządzaniem dodatkowymi certyfikatami.   

Aby skonfigurować szyfrowanie tokenu, należy przekazać plik certyfikatu X. 509 zawierający klucz publiczny do obiektu aplikacji usługi Azure AD, który reprezentuje aplikację. Aby uzyskać certyfikat X. 509, możesz pobrać go z aplikacji lub uzyskać od dostawcy aplikacji w przypadku, gdy dostawca aplikacji dostarcza klucze szyfrowania lub w przypadkach, gdy aplikacja oczekuje klucza prywatnego, można go utworzyć przy użyciu narzędzi kryptograficznych, część klucza prywatnego przekazywana do magazynu kluczy aplikacji i zgodnego certyfikatu klucza publicznego przekazanego do usługi Azure AD.

Usługa Azure AD używa algorytmu AES-256 do szyfrowania danych potwierdzenia SAML.

## <a name="configure-saml-token-encryption"></a>Konfigurowanie szyfrowania tokenu SAML

Aby skonfigurować szyfrowanie tokenu SAML, wykonaj następujące kroki:

1. Uzyskaj certyfikat klucza publicznego, który jest zgodny z kluczem prywatnym skonfigurowanym w aplikacji.

    Utwórz parę kluczy asymetrycznych do użycia na potrzeby szyfrowania. Jeśli jednak aplikacja dostarcza klucz publiczny do szyfrowania, postępuj zgodnie z instrukcjami aplikacji, aby pobrać certyfikat X. 509.

    Klucz publiczny powinien być przechowywany w pliku certyfikatu X. 509 w formacie CER.

    Jeśli aplikacja używa klucza utworzonego dla danego wystąpienia, postępuj zgodnie z instrukcjami dostarczonymi przez aplikację w celu zainstalowania klucza prywatnego, który będzie używany przez aplikację do odszyfrowywania tokenów z dzierżawy usługi Azure AD.

1. Dodaj certyfikat do konfiguracji aplikacji w usłudze Azure AD.

### <a name="to-configure-token-encryption-in-the-azure-portal"></a>Aby skonfigurować szyfrowanie tokenu w Azure Portal

Możesz dodać publiczny certyfikat do konfiguracji aplikacji w ramach Azure Portal.

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).

1. Przejdź do bloku **Azure Active Directory > aplikacje przedsiębiorstwa** , a następnie wybierz aplikację, dla której chcesz skonfigurować szyfrowanie tokenu.

1. Na stronie aplikacji wybierz pozycję **szyfrowanie tokenu**.

    ![Opcja szyfrowania tokenu w Azure Portal](./media/howto-saml-token-encryption/token-encryption-option-small.png)

    > [!NOTE]
    > Opcja **szyfrowania tokenu** jest dostępna tylko dla aplikacji SAML, które zostały skonfigurowane za pomocą bloku **aplikacje dla przedsiębiorstw** w Azure Portal, z galerii aplikacji lub aplikacji spoza galerii. W przypadku innych aplikacji ta opcja menu jest wyłączona. W przypadku aplikacji zarejestrowanych za pośrednictwem **rejestracje aplikacji** środowiska w Azure Portal można skonfigurować szyfrowanie tokenów SAML przy użyciu manifestu aplikacji za pośrednictwem Microsoft Graph lub za pośrednictwem programu PowerShell.

1. Na stronie **szyfrowanie tokenu** wybierz pozycję **Importuj certyfikat** , aby zaimportować plik CER zawierający publiczny certyfikat X. 509.

    ![Zaimportuj plik. cer zawierający certyfikat X. 509](./media/howto-saml-token-encryption/import-certificate-small.png)

1. Po zaimportowaniu certyfikatu, a klucz prywatny jest skonfigurowany do użycia na stronie aplikacji, Aktywuj szyfrowanie, wybierając przycisk **...** obok stanu odcisku palca, a następnie wybierz pozycję **Aktywuj szyfrowanie tokenu** z opcji w menu rozwijanym.

1. Wybierz pozycję **tak** , aby potwierdzić aktywację certyfikatu szyfrowania tokenu.

1. Upewnij się, że potwierdzenia SAML emitowane dla aplikacji są szyfrowane.

### <a name="to-deactivate-token-encryption-in-the-azure-portal"></a>Aby dezaktywować szyfrowanie tokenu w Azure Portal

1. W Azure Portal przejdź do pozycji **Azure Active Directory > aplikacje dla przedsiębiorstw**, a następnie wybierz aplikację z włączonym szyfrowaniem tokenu SAML.

1. Na stronie aplikacji wybierz pozycję **szyfrowanie tokenu**, Znajdź certyfikat, a następnie wybierz opcję **...** , aby wyświetlić menu rozwijane.

1. Wybierz opcję **Dezaktywuj szyfrowanie tokenu**.

## <a name="configure-saml-token-encryption-using-graph-api-powershell-or-app-manifest"></a>Konfigurowanie szyfrowania tokenów SAML przy użyciu interfejs API programu Graph, programu PowerShell lub manifestu aplikacji

Certyfikaty szyfrowania są przechowywane w obiekcie aplikacji w usłudze Azure AD za pomocą `encrypt` tagu Usage. Można skonfigurować wiele certyfikatów szyfrowania, a te, które są aktywne na potrzeby szyfrowania tokenów, są identyfikowane przez `tokenEncryptionKeyID` atrybut.

Aby skonfigurować szyfrowanie tokenu przy użyciu interfejsu API Microsoft Graph lub PowerShell, musisz mieć identyfikator obiektu aplikacji. Tę wartość można znaleźć programowo lub przechodząc do strony **Właściwości** aplikacji w Azure Portal i zwracając wartość **identyfikatora obiektu** .

Podczas konfigurowania poświadczeń klucza przy użyciu programu Graph, PowerShell lub w manifeście aplikacji należy wygenerować identyfikator GUID, który ma być używany dla keyId.

### <a name="to-configure-token-encryption-using-microsoft-graph"></a>Aby skonfigurować szyfrowanie tokenu przy użyciu Microsoft Graph

1. Zaktualizuj aplikację `keyCredentials` przy użyciu certyfikatu X. 509 na potrzeby szyfrowania. W przykładzie poniżej pokazano, jak to zrobić.

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

1. Zidentyfikuj certyfikat szyfrowania, który jest aktywny do szyfrowania tokenów. W przykładzie poniżej pokazano, jak to zrobić.

    ```
    Patch https://graph.microsoft.com/beta/applications/<application objectid> 

    { 
       "tokenEncryptionKeyId":"fdf8c5d8-f727-43fd-beaf-0f1521cf3d35" (The keyId of the keyCredentials entry to use)
    }
    ```

### <a name="to-configure-token-encryption-using-powershell"></a>Aby skonfigurować szyfrowanie tokenu przy użyciu programu PowerShell

1. Użyj najnowszego modułu programu PowerShell usługi Azure AD, aby nawiązać połączenie z dzierżawcą.

1. Ustaw ustawienia szyfrowania tokenu za pomocą polecenia **[Set-AzureApplication](/powershell/module/azuread/set-azureadapplication?view=azureadps-2.0-preview)** .

    ```
    Set-AzureADApplication -ObjectId <ApplicationObjectId> -KeyCredentials "<KeyCredentialsObject>"  -TokenEncryptionKeyId <keyID>
    ```

1. Przeczytaj ustawienia szyfrowania tokenu przy użyciu następujących poleceń.

    ```powershell
    $app=Get-AzureADApplication -ObjectId <ApplicationObjectId>
    $app.KeyCredentials
    $app.TokenEncryptionKeyId
    ```

### <a name="to-configure-token-encryption-using-the-application-manifest"></a>Aby skonfigurować szyfrowanie tokenu przy użyciu manifestu aplikacji

1. W Azure Portal przejdź do **Azure Active Directory > rejestracje aplikacji**.

1. Wybierz pozycję **wszystkie aplikacje** z listy rozwijanej, aby wyświetlić wszystkie aplikacje, a następnie wybierz aplikację dla przedsiębiorstw, którą chcesz skonfigurować.

1. Na stronie aplikacji wybierz pozycję **manifest** , aby edytować [manifest aplikacji](../develop/reference-app-manifest.md).

1. Ustaw wartość `tokenEncryptionKeyId` atrybutu.

    Poniższy przykład pokazuje manifest aplikacji skonfigurowany przy użyciu dwóch certyfikatów szyfrowania i drugi wybrany jako aktywny przy użyciu tokenEnryptionKeyId.

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

* Dowiedz [się, w jaki sposób usługa Azure AD używa protokołu SAML](../develop/active-directory-saml-protocol-reference.md)
* Informacje o formacie, charakterystyce zabezpieczeń i zawartości [tokenów SAML w usłudze Azure AD](../develop/reference-saml-tokens.md)