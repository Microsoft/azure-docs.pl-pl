---
title: Użyj MSAL.js z Azure AD B2C
titleSuffix: Microsoft identity platform
description: Biblioteka Microsoft Authentication Library for JavaScript (MSAL.js) umożliwia aplikacjom współpracę z Azure AD B2C i uzyskiwanie tokenów w celu wywołania zabezpieczonych interfejsów API sieci Web. Te interfejsy API sieci Web mogą być Microsoft Graph, inne interfejsy API firmy Microsoft, interfejsy API sieci Web od innych, lub własny internetowy interfejs API.
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 06/05/2020
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 13b478e85278827258ea2fc25a0ee4298039fb1c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88119794"
---
# <a name="use-microsoft-authentication-library-for-javascript-to-work-with-azure-ad-b2c"></a>Użyj biblioteki uwierzytelniania firmy Microsoft dla języka JavaScript do pracy z Azure AD B2C

[Biblioteka Microsoft Authentication Library for JavaScript (MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js) umożliwia deweloperom języka JavaScript uwierzytelnianie użytkowników za pomocą tożsamości społecznościowych i lokalnych przy użyciu [Azure Active Directory B2C](../../active-directory-b2c/overview.md) (Azure AD B2C).

Korzystając z Azure AD B2C jako usługi zarządzania tożsamościami, można dostosowywać i kontrolować sposób tworzenia konta, logowania i zarządzania swoimi profilami przy użyciu aplikacji. Azure AD B2C umożliwia również markę i dostosowanie interfejsu użytkownika, który aplikacja wyświetla podczas procesu uwierzytelniania.

W poniższych sekcjach pokazano, jak:

- Ochrona Node.js internetowego interfejsu API
- Obsługa logowania w aplikacji jednostronicowej (SPA) i Wywołaj *ten* chroniony internetowy interfejs API
- Włącz obsługę resetowania hasła

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli jeszcze tego nie zrobiono, Utwórz [dzierżawę Azure AD B2C](../../active-directory-b2c/tutorial-create-tenant.md).

## <a name="nodejs-web-api"></a>Interfejs API sieci Web platformy Node.js

Poniższe kroki przedstawiają sposób, w jaki **interfejs API sieci Web** może używać Azure AD B2C do ochrony samej siebie i uwidaczniania wybranych zakresów w aplikacji klienckiej.

MSAL.js węzła jest obecnie w trakcie opracowywania. Aby uzyskać więcej informacji, zobacz [Plan](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki#roadmap) w witrynie GitHub. Obecnie zalecamy korzystanie z [usługi Passport-Azure-AD](https://github.com/AzureAD/passport-azure-ad), biblioteki uwierzytelniania dla Node.js opracowanej i obsługiwanej przez firmę Microsoft.

### <a name="step-1-register-your-application"></a>Krok 1. Rejestrowanie aplikacji

Aby chronić internetowy interfejs API za pomocą Azure AD B2C, należy najpierw go zarejestrować. Aby uzyskać szczegółowe instrukcje, zobacz artykuł [Rejestrowanie aplikacji](../../active-directory-b2c/add-web-api-application.md) .

### <a name="step-2-download-the-sample-application"></a>Krok 2. Pobieranie przykładowej aplikacji

Pobierz przykład jako plik zip lub Sklonuj go z witryny GitHub:

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

### <a name="step-3-configure-authentication"></a>Krok 3. Konfigurowanie uwierzytelniania

1. Otwórz plik `config.js` w przykładowej aplikacji.

2. Skonfiguruj przykład za pomocą poświadczeń aplikacji, które zostały uzyskane wcześniej podczas rejestrowania aplikacji. Zmień następujące wiersze kodu, zastępując wartości nazwami clientID, hosta, tenantId i zasad.

```JavaScript
const clientID = "<Application ID for your Node.js web API - found on Properties page in Azure portal e.g. 93733604-cc77-4a3c-a604-87084dd55348>";
const b2cDomainHost = "<Domain of your B2C host eg. fabrikamb2c.b2clogin.com>";
const tenantId = "<your-tenant-ID>.onmicrosoft.com"; // Alternatively, you can use your Directory (tenant) ID (GUID)
const policyName = "<Name of your sign in / sign up policy, e.g. B2C_1_signupsignin1>";
```

Aby uzyskać więcej informacji, zapoznaj się z tym [Node.js przykładowym interfejsem API sieci Web B2C](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi).

## <a name="javascript-spa"></a>JavaScript SPA

Poniższe kroki przedstawiają sposób, w jaki **aplikacja jednostronicowa** może używać Azure AD B2C do rejestrowania, logowania i wywoływania chronionego internetowego interfejsu API.

### <a name="step-1-register-your-application"></a>Krok 1. Rejestrowanie aplikacji

Aby zaimplementować uwierzytelnianie, należy najpierw zarejestrować aplikację. Aby uzyskać szczegółowe instrukcje, zobacz artykuł [Rejestrowanie aplikacji](../../active-directory-b2c/tutorial-register-applications.md) .

### <a name="step-2-download-the-sample-application"></a>Krok 2. Pobieranie przykładowej aplikacji

Pobierz przykładowy kod [. Archiwum ZIP](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) lub klonowanie repozytorium GitHub:

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

### <a name="step-3-configure-authentication"></a>Krok 3. Konfigurowanie uwierzytelniania

Aby skonfigurować aplikację, istnieją dwa kwestie:

- Konfigurowanie punktu końcowego interfejsu API i uwidocznionych zakresów
- Konfigurowanie parametrów uwierzytelniania i zakresów tokenów

1. Otwórz plik *apiConfig.js* w przykładzie.

2. Skonfiguruj przykład za pomocą parametrów uzyskanych wcześniej podczas rejestrowania internetowego interfejsu API. Zmień następujące wiersze kodu, zastępując wartości adresami internetowego interfejsu API i uwidocznionych zakresów.

   ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    const apiConfig = {
        b2cScopes: ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"], //API scopes you exposed during api registration
        webApi: "https://fabrikamb2chello.azurewebsites.net/hello"
    };
   ```

1. Otwórz plik *authConfig.js* w przykładzie.

1. Skonfiguruj przykład za pomocą parametrów uzyskanych wcześniej podczas rejestrowania aplikacji jednostronicowej. Zmień następujące wiersze kodu, zastępując wartości z ClientId, metadanych urzędu i zakresów żądania tokenu.

   ```javascript
    // Config object to be passed to Msal on creation.
    const msalConfig = {
        auth: {
            clientId: "e760cab2-b9a1-4c0d-86fb-ff7084abd902",
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/B2C_1_signupsignin1",
            validateAuthority: false
        },
        cache: {
            cacheLocation: "localStorage", // This configures where your cache will be stored
            storeAuthStateInCookie: false // Set this to "true" to save cache in cookies
        }
    };

    // Add here scopes for id token to be used at the MS Identity Platform endpoint
    const loginRequest = {
        scopes: ["openid", "profile"],
    };
   ```

Aby uzyskać więcej informacji, zapoznaj się z tą [przykładową aplikacją B2C w języku JavaScript](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp).

## <a name="support-password-reset"></a>Obsługa resetowania hasła

W tej sekcji rozszerzono aplikację jednostronicową w celu użycia przepływu użytkownika Azure AD B2C resetowania hasła. Chociaż MSAL.js nie obsługuje obecnie wielu przepływów użytkowników lub zasad niestandardowych, można użyć biblioteki do obsługi typowych przypadków użycia, takich jak resetowanie hasła.

W poniższych krokach przyjęto założenie, że zostały wykonane kroki opisane w poprzedniej sekcji [Spa JavaScript](#javascript-spa) .

### <a name="step-1-define-the-authority-string-for-password-reset-user-flow"></a>Krok 1. Definiowanie ciągu urzędu dla przepływu użytkownika resetowania hasła

1. Najpierw Utwórz obiekt, w którym są przechowywane identyfikatory URI urzędu:

    ```javascript
        const b2cPolicies = {
            names: {
                signUpSignIn: "b2c_1_susi",
                forgotPassword: "b2c_1_reset"
            },
            authorities: {
                signUpSignIn: {
                    authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_susi",
                },
                forgotPassword: {
                    authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_reset",
                },
            },
        }
    ```

1. Następnie zainicjuj obiekt MSAL przy użyciu `signInSignUp` zasad domyślnych (zobacz poprzedni fragment kodu). Gdy użytkownik próbuje zalogować się, zostanie wyświetlony następujący ekran:

    :::image type="content" source="media/msal-b2c-overview/user-journey-01-signin.png" alt-text="Ekran logowania wyświetlany przez Azure AD B2C":::

### <a name="step-2-catch-and-handle-authentication-errors-in-your-login-method"></a>Krok 2. catch i obsługuj błędy uwierzytelniania w metodzie logowania

Gdy użytkownik wybierze **zapomniane hasło**, aplikacja zgłosi błąd, który należy wychwycić w kodzie, a następnie obsłużyć przez przedprezentację odpowiedniego przepływu użytkownika. W takim przypadku `b2c_1_reset` przepływ resetowania hasła.

1. Zwiększ metodę logowania w następujący sposób:

    ```javascript
    function signIn() {
      myMSALObj.loginPopup(loginRequest)
        .then(loginResponse => {
            console.log("id_token acquired at: " + new Date().toString());

            if (myMSALObj.getAccount()) {
              updateUI();
            }

        }).catch(function (error) {
          console.log(error);

          // error handling
          if (error.errorMessage) {
            // check for forgot password error
            if (error.errorMessage.indexOf("AADB2C90118") > -1) {

              //call login method again with the password reset user flow
              myMSALObj.loginPopup(b2cPolicies.authorities.forgotPassword)
                .then(loginResponse => {
                  console.log(loginResponse);
                  window.alert("Password has been reset successfully. \nPlease sign-in with your new password.");
                })
            }
          }
        });
    }
    ```

1. Poprzedni fragment kodu przedstawia sposób wyświetlania ekranu resetowania hasła po przechwyceniu błędu w kodzie `AADB2C90118` .

    Po zresetowaniu hasła użytkownik wraca do aplikacji, aby ponownie się zalogować.

    :::image type="content" source="media/msal-b2c-overview/user-journey-02-password-reset.png" alt-text="Ekran logowania wyświetlany przez Azure AD B2C" border="false":::

    Aby uzyskać więcej informacji o kodach błędów i obsłudze wyjątków, zobacz [MSAL Error and Exception Codes](msal-handling-exceptions.md).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat tych Azure AD B2C pojęć:

- [Przepływy użytkowników](../../active-directory-b2c/tutorial-create-user-flows.md)
- [Zasady niestandardowe](../../active-directory-b2c/custom-policy-get-started.md)
- [Dostosowywanie interfejsu użytkownika](../../active-directory-b2c/custom-policy-configure-user-input.md)