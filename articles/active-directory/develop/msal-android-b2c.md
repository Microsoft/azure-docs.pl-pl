---
title: Azure AD B2C (MSAL Android) | Azure
titleSuffix: Microsoft identity platform
description: Informacje o określonych kwestiach dotyczących używania Azure AD B2C z biblioteką uwierzytelniania firmy Microsoft dla systemu Android (MSAL. Systemów
services: active-directory
author: iambmelt
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 9/18/2019
ms.author: brianmel
ms.reviewer: rapong
ms.custom: aaddev
ms.openlocfilehash: 1a9b9481d0b4086505bbfd3c2cd654ce228d1ae2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101688879"
---
# <a name="use-msal-for-android-with-b2c"></a>Korzystanie z MSAL dla systemu Android z B2C

Biblioteka Microsoft Authentication Library (MSAL) umożliwia deweloperom aplikacji uwierzytelnianie użytkowników za pomocą tożsamości społecznościowych i lokalnych przy użyciu [Azure Active Directory B2C (Azure AD B2C)](../../active-directory-b2c/index.yml). Azure AD B2C to usługa zarządzania tożsamościami. Służy do dostosowywania i kontrolowania sposobu tworzenia konta, logowania i zarządzania profilami klientów podczas korzystania z aplikacji.

## <a name="choosing-a-compatible-authorization_user_agent"></a>Wybieranie zgodnego authorization_user_agent
System zarządzania tożsamościami B2C obsługuje uwierzytelnianie przy użyciu wielu dostawców kont społecznościowych, takich jak Google, Facebook, Twitter i Amazon. Jeśli planujesz obsługę takich typów kont w aplikacji, zalecamy skonfigurowanie publicznej aplikacji klienckiej MSAL do korzystania z `DEFAULT` lub `BROWSER` wartości podczas określania manifestu [`authorization_user_agent`](msal-configuration.md#authorization_user_agent) z powodu ograniczeń zakazujących używania uwierzytelniania opartego na przeglądarce sieci Web z zewnętrznymi dostawcami tożsamości.

## <a name="configure-known-authorities-and-redirect-uri"></a>Skonfiguruj znane urzędy i identyfikator URI przekierowania

W programie MSAL for Android zasady B2C (podróże użytkowników) są konfigurowane jako indywidualne urzędy.

Dana aplikacja B2C, która ma dwie zasady:
- Rejestrowanie/logowanie
    * Wywołan `B2C_1_SISOPolicy`
- Edytuj profil
    * Wywołan `B2C_1_EditProfile`

Plik konfiguracyjny aplikacji deklaruje dwa `authorities` . Jeden dla każdej zasady. `type`Właściwość każdego urzędu ma wartość `B2C` .

>Uwaga: wartość `account_mode` musi być ustawiona na **wielokrotność** dla aplikacji B2C. Aby uzyskać więcej informacji na temat [publicznych aplikacji klienckich na wielu kontach](./single-multi-account.md#multiple-account-public-client-application), zapoznaj się z dokumentacją.

### `app/src/main/res/raw/msal_config.json`

```json
{
  "client_id": "<your_client_id_here>",
  "redirect_uri": "<your_redirect_uri_here>",
  "account_mode" : "MULTIPLE",
  "authorization_user_agent" : "DEFAULT",
  "authorities": [
    {
      "type": "B2C",
      "authority_url": "https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_SISOPolicy/",
      "default": true
    },
    {
      "type": "B2C",
      "authority_url": "https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_EditProfile/"
    }
  ]
}
```

`redirect_uri`Program musi być zarejestrowany w konfiguracji aplikacji, a także w `AndroidManifest.xml` celu obsługi przekierowywania w ramach [przepływu przydzielenia kodu autoryzacji](../../active-directory-b2c/authorization-code-flow.md).

## <a name="initialize-ipublicclientapplication"></a>Zainicjuj IPublicClientApplication

`IPublicClientApplication` jest konstruowany przez metodę fabryki, aby umożliwić asynchroniczne analizowanie konfiguracji aplikacji.

```java
PublicClientApplication.createMultipleAccountPublicClientApplication(
    context, // Your application Context
    R.raw.msal_config, // Id of app JSON config
    new IPublicClientApplication.ApplicationCreatedListener() {
        @Override
        public void onCreated(IMultipleAccountPublicClientApplication pca) {
            // Application has been initialized.
        }

        @Override
        public void onError(MsalException exception) {
            // Application could not be created.
            // Check Exception message for details.
        }
    }
);
```

## <a name="interactively-acquire-a-token"></a>Interaktywnie pozyskiwanie tokenu

Aby uzyskać token interaktywnie przy użyciu usługi MSAL, skompiluj `AcquireTokenParameters` wystąpienie i podaj je do `acquireToken` metody. Poniższe żądanie tokenu używa `default` urzędu.

```java
IMultipleAccountPublicClientApplication pca = ...; // Initialization not shown

AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(activity)
    .withScopes(Arrays.asList("https://contoso.onmicrosoft.com/contosob2c/read")) // Provide your registered scope here
    .withPrompt(Prompt.LOGIN)
    .callback(new AuthenticationCallback() {
        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            // Token request was successful, inspect the result
        }

        @Override
        public void onError(MsalException exception) {
            // Token request was unsuccessful, inspect the exception
        }

        @Override
        public void onCancel() {
            // The user cancelled the flow
        }
    }).build();

pca.acquireToken(parameters);
```

## <a name="silently-renew-a-token"></a>Odnawianie tokenu w trybie dyskretnym

Aby uzyskać token dyskretnie z MSAL, skompiluj `AcquireTokenSilentParameters` wystąpienie i podaj je do `acquireTokenSilentAsync` metody. W przeciwieństwie do `acquireToken` metody, `authority` należy określić, aby uzyskać token dyskretnie.

```java
IMultipleAccountPublicClientApplication pca = ...; // Initialization not shown
AcquireTokenSilentParameters parameters = new AcquireTokenSilentParameters.Builder()
    .withScopes(Arrays.asList("https://contoso.onmicrosoft.com/contosob2c/read")) // Provide your registered scope here
    .forAccount(account)
    // Select a configured authority (policy), mandatory for silent auth requests
    .fromAuthority("https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_SISOPolicy/")
    .callback(new SilentAuthenticationCallback() {
        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            // Token request was successful, inspect the result
        }

        @Override
        public void onError(MsalException exception) {
            // Token request was unsuccessful, inspect the exception
        }
    })
    .build();

pca.acquireTokenSilentAsync(parameters);
```

## <a name="specify-a-policy"></a>Określanie zasad

Ponieważ zasady w B2C są reprezentowane jako osobne urzędy, wywoływanie zasad innych niż domyślne jest realizowane przez określenie `fromAuthority` klauzuli podczas konstruowania `acquireToken` lub `acquireTokenSilent` parametrów.  Na przykład:

```java
AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(activity)
    .withScopes(Arrays.asList("https://contoso.onmicrosoft.com/contosob2c/read")) // Provide your registered scope here
    .withPrompt(Prompt.LOGIN)
    .callback(...) // provide callback here
    .fromAuthority("<url_of_policy_defined_in_configuration_json>")
    .build();
```

## <a name="handle-password-change-policies"></a>Obsługa zasad zmiany hasła

Przepływ użytkownika rejestracji lub logowania na koncie lokalnym pokazuje "**zapomniane hasło"?** . Kliknięcie tego linku nie powoduje automatycznego wyzwolenia przepływu użytkownika resetowania hasła.

Zamiast tego kod błędu `AADB2C90118` jest zwracany do aplikacji. Aplikacja powinna obsłużyć ten kod błędu przez uruchomienie określonego przepływu użytkownika, który resetuje hasło.

Aby przechwytywać kod błędu resetowania hasła, w programie można używać następującej implementacji `AuthenticationCallback` :

```java
new AuthenticationCallback() {

    @Override
    public void onSuccess(IAuthenticationResult authenticationResult) {
        // ..
    }

    @Override
    public void onError(MsalException exception) {
        final String B2C_PASSWORD_CHANGE = "AADB2C90118";

        if (exception.getMessage().contains(B2C_PASSWORD_CHANGE)) {
            // invoke password reset flow
        }
    }

    @Override
    public void onCancel() {
        // ..
    }
}
```

## <a name="use-iauthenticationresult"></a>Użyj IAuthenticationResult

Pomyślne pozyskiwanie tokenów w `IAuthenticationResult` obiekcie. Zawiera token dostępu, oświadczenia użytkowników i metadane.

### <a name="get-the-access-token-and-related-properties"></a>Pobieranie tokenu dostępu i powiązanych właściwości

```java
// Get the raw bearer token
String accessToken = authenticationResult.getAccessToken();

// Get the scopes included in the access token
String[] accessTokenScopes = authenticationResult.getScope();

// Gets the access token's expiry
Date expiry = authenticationResult.getExpiresOn();

// Get the tenant for which this access token was issued
String tenantId = authenticationResult.getTenantId();
```

### <a name="get-the-authorized-account"></a>Pobieranie autoryzowanego konta

```java
// Get the account from the result
IAccount account = authenticationResult.getAccount();

// Get the id of this account - note for B2C, the policy name is a part of the id
String id = account.getId();

// Get the IdToken Claims
//
// For more information about B2C token claims, see reference documentation
// https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-tokens
Map<String, ?> claims = account.getClaims();

// Get the 'preferred_username' claim through a convenience function
String username = account.getUsername();

// Get the tenant id (tid) claim through a convenience function
String tenantId = account.getTenantId();
```

### <a name="idtoken-claims"></a>IdToken oświadczeń

Oświadczenia zwrócone w IdToken są wypełniane przez usługę tokenu zabezpieczającego (STS), a nie przez MSAL. Niektóre oświadczenia mogą być nieobecne w zależności od używanego dostawcy tożsamości (dostawcy tożsamości). Niektóre dostawców tożsamości nie udostępniają obecnie tego `preferred_username` żądania. Ponieważ to zgłoszenie jest używane przez MSAL do buforowania, `MISSING FROM THE TOKEN RESPONSE` w jego miejsce jest używana wartość symbolu zastępczego. Aby uzyskać więcej informacji na temat oświadczeń usługi B2C IdToken, zobacz [Omówienie tokenów w Azure Active Directory B2C](../../active-directory-b2c/tokens-overview.md#claims).

## <a name="managing-accounts-and-policies"></a>Zarządzanie kontami i zasadami

B2C traktuje każdą zasadę jako oddzielny urząd. W ten sposób tokeny dostępu, tokeny odświeżania i tokeny identyfikatorów zwracane przez poszczególne zasady nie są zamienne. Oznacza to, że każda zasada zwraca oddzielny `IAccount` obiekt, którego tokeny nie mogą być używane do wywoływania innych zasad.

Każda zasada dodaje `IAccount` do pamięci podręcznej dla każdego użytkownika. Jeśli użytkownik zaloguje się do aplikacji i wywoła dwie zasady, będą mieć dwie `IAccount` s. Aby usunąć tego użytkownika z pamięci podręcznej, należy wywołać `removeAccount()` dla każdej zasady.

W przypadku odnowienia tokenów dla zasad w programie `acquireTokenSilent` Podaj takie samo, `IAccount` które zostało zwrócone z poprzednich wywołań zasad do  `AcquireTokenSilentParameters` . Podanie konta zwróconego przez inne zasady spowoduje wystąpienie błędu.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat Azure Active Directory B2C (Azure AD B2C) na [co to jest Azure Active Directory B2C?](../../active-directory-b2c/overview.md)
