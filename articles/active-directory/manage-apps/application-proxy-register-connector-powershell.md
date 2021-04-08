---
title: Instalacja dyskretna aplikacja usługi Azure AD łącznika serwera proxy | Microsoft Docs
description: W tym artykule opisano sposób przeprowadzania nienadzorowanej instalacji łącznika serwer proxy aplikacji usługi Azure AD platformy Azure w celu zapewnienia bezpiecznego dostępu zdalnego do aplikacji lokalnych.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/24/2020
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro, has-adal-ref
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0ac442e895850de04bedf7673ffe267ac8697d26
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99258154"
---
# <a name="create-an-unattended-installation-script-for-the-azure-ad-application-proxy-connector"></a>Tworzenie skryptu instalacji nienadzorowanej dla łącznika usługi Azure serwer proxy aplikacji usługi Azure AD

Ten temat ułatwia utworzenie skryptu programu Windows PowerShell, który umożliwia instalację nienadzorowaną i rejestrację łącznika usługi Azure serwer proxy aplikacji usługi Azure AD.

Ta funkcja jest przydatna, gdy chcesz:

* Zainstaluj łącznik na serwerach z systemem Windows, na których nie jest włączony interfejs użytkownika, lub nie możesz uzyskać do niego dostępu za pomocą Pulpit zdalny.
* Zainstaluj i zarejestruj wiele łączników jednocześnie.
* Zintegruj instalację i rejestrację łącznika w ramach innej procedury.
* Utwórz standardowy obraz serwera zawierający bity łącznika, ale nie jest zarejestrowany.

Aby [łącznik serwera proxy](application-proxy-connectors.md) działał prawidłowo, musi być zarejestrowany w katalogu usługi Azure AD przy użyciu administratora aplikacji i hasła. Zwykle te informacje są wprowadzane podczas instalacji łącznika w wyskakującym oknie dialogowym, ale w celu zautomatyzowania tego procesu można użyć programu PowerShell.

Istnieją dwa kroki instalacji nienadzorowanej. Najpierw zainstaluj łącznik. Następnie zarejestruj łącznik w usłudze Azure AD.

> [!IMPORTANT]
> Jeśli instalujesz łącznik dla Azure Government w chmurze, zapoznaj się z [wymaganiami wstępnymi](../hybrid/reference-connect-government-cloud.md#allow-access-to-urls) i [procedurą instalacji](../hybrid/reference-connect-government-cloud.md#install-the-agent-for-the-azure-government-cloud). Wymaga to włączenia dostępu do innego zestawu adresów URL i dodatkowego parametru do uruchomienia instalacji.

## <a name="install-the-connector"></a>Instalowanie łącznika
Wykonaj następujące kroki, aby zainstalować łącznik bez rejestracji:

1. Otwórz wiersz polecenia.
2. Uruchom następujące polecenie, w którym/q oznacza instalację cichą. W przypadku instalacji dyskretnej nie jest wyświetlany monit o zaakceptowanie umowy licencyjnej End-User.

   ```
   AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q
   ```

## <a name="register-the-connector-with-azure-ad"></a>Rejestrowanie łącznika w usłudze Azure AD
Istnieją dwie metody, których można użyć do zarejestrowania łącznika:

* Rejestrowanie łącznika przy użyciu obiektu poświadczeń programu Windows PowerShell
* Rejestrowanie łącznika przy użyciu tokenu utworzonego w trybie offline

### <a name="register-the-connector-using-a-windows-powershell-credential-object"></a>Rejestrowanie łącznika przy użyciu obiektu poświadczeń programu Windows PowerShell
1. Utwórz obiekt poświadczeń programu Windows PowerShell `$cred` , który zawiera administracyjną nazwę użytkownika i hasło dla katalogu. Uruchom następujące polecenie, zastępując *\<username\>* i *\<password\>* :

   ```powershell
   $User = "<username>"
   $PlainPassword = '<password>'
   $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
   $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword
   ```
2. Przejdź do **folderu C:\Program Files\Microsoft AAD proxy App Connector** i uruchom następujący skrypt przy użyciu `$cred` utworzonego obiektu:

   ```powershell
   .\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred -Feature ApplicationProxy
   ```

### <a name="register-the-connector-using-a-token-created-offline"></a>Rejestrowanie łącznika przy użyciu tokenu utworzonego w trybie offline
1. Utwórz token offline przy użyciu klasy AuthenticationContext, korzystając z wartości w tym fragmencie kodu lub poniższych poleceń cmdlet programu PowerShell:

   **Przy użyciu języka C#:**

   ```csharp
   using System;
   using System.Linq;
   using System.Collections.Generic;
   using Microsoft.Identity.Client;

   class Program
   {
   #region constants
   /// <summary>
   /// The AAD authentication endpoint uri
   /// </summary>
   static readonly string AadAuthenticationEndpoint = "https://login.microsoftonline.com/common/oauth2/v2.0/authorize";

   /// <summary>
   /// The application ID of the connector in AAD
   /// </summary>
   static readonly string ConnectorAppId = "55747057-9b5d-4bd4-b387-abf52a8bd489";
 
   /// <summary>
   /// The AppIdUri of the registration service in AAD
   /// </summary>
   static readonly string RegistrationServiceAppIdUri = "https://proxy.cloudwebappproxy.net/registerapp/user_impersonation";

   #endregion

   #region private members
   private string token;
   private string tenantID;
   #endregion

   public void GetAuthenticationToken()
   {
    
   IPublicClientApplication clientApp = PublicClientApplicationBuilder
      .Create(ConnectorAppId)
      .WithDefaultRedirectUri() // will automatically use the default Uri for native app
      .WithAuthority(AadAuthenticationEndpoint)
      .Build();

      AuthenticationResult authResult = null;
            
      IAccount account = null;

      IEnumerable<string> scopes = new string[] { RegistrationServiceAppIdUri };

      try
      {
       authResult = await clientApp.AcquireTokenSilent(scopes, account).ExecuteAsync();
      }
       catch (MsalUiRequiredException ex)
      {
       authResult = await clientApp.AcquireTokenInteractive(scopes).ExecuteAsync();
      }


      if (authResult == null || string.IsNullOrEmpty(authResult.AccessToken) || string.IsNullOrEmpty(authResult.TenantId))
      {
       Trace.TraceError("Authentication result, token or tenant id returned are null");
       throw new InvalidOperationException("Authentication result, token or tenant id returned are null");
      }

      token = authResult.AccessToken;
      tenantID = authResult.TenantId;
      }
      ```

   **Przy użyciu programu PowerShell:**

   ```powershell
   # Load MSAL (Tested with version 4.7.1) 

   Add-Type -Path "..\MSAL\Microsoft.Identity.Client.dll" 
        
   # The AAD authentication endpoint uri
        
   $authority = "https://login.microsoftonline.com/common/oauth2/v2.0/authorize"

   #The application ID of the connector in AAD

   $connectorAppId = "55747057-9b5d-4bd4-b387-abf52a8bd489";

   #The AppIdUri of the registration service in AAD
   $registrationServiceAppIdUri = "https://proxy.cloudwebappproxy.net/registerapp/user_impersonation"

   # Define the resources and scopes you want to call 

   $scopes = New-Object System.Collections.ObjectModel.Collection["string"] 

   $scopes.Add($registrationServiceAppIdUri)

   $app = [Microsoft.Identity.Client.PublicClientApplicationBuilder]::Create($connectorAppId).WithAuthority($authority).WithDefaultRedirectUri().Build()

   [Microsoft.Identity.Client.IAccount] $account = $null

   # Acquiring the token 

   $authResult = $null

   $authResult = $app.AcquireTokenInteractive($scopes).WithAccount($account).ExecuteAsync().ConfigureAwait($false).GetAwaiter().GetResult()

   # Check AuthN result
   If (($authResult) -and ($authResult.AccessToken) -and ($authResult.TenantId)) {
        
   $token = $authResult.AccessToken
   $tenantId = $authResult.TenantId

   Write-Output "Success: Authentication result returned."
        
   }
   Else {
         
   Write-Output "Error: Authentication result, token or tenant id returned with null."
        
   } 
   ```

2. Po utworzeniu tokenu Utwórz element SecureString przy użyciu tokenu:

   ```powershell
   $SecureToken = $Token | ConvertTo-SecureString -AsPlainText -Force
   ```

3. Uruchom następujące polecenie programu Windows PowerShell, zastępując \<tenant GUID\> je identyfikatorem katalogu:

   ```powershell
   .\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Token -Token $SecureToken -TenantId <tenant GUID> -Feature ApplicationProxy
   ```

## <a name="next-steps"></a>Następne kroki
* [Publikowanie aplikacji przy użyciu własnej nazwy domeny](application-proxy-configure-custom-domain.md)
* [Włączanie logowania jednokrotnego](application-proxy-configure-single-sign-on-with-kcd.md)
* [Rozwiązywanie problemów z serwerem proxy aplikacji](application-proxy-troubleshoot.md)