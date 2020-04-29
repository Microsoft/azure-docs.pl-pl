---
title: Użyj MSAL z Azure Active Directory B2CLearn | Azure
titleSuffix: Microsoft identity platform
description: Biblioteka Microsoft Authentication Library for JavaScript (MSAL. js) umożliwia aplikacjom współpracują z Azure AD B2C i pozyskiwanie tokenów w celu wywoływania zabezpieczonych interfejsów API sieci Web. Te interfejsy API sieci Web mogą być Microsoft Graph, inne interfejsy API firmy Microsoft, interfejsy API sieci Web od innych, lub własny internetowy interfejs API.
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/16/2019
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 8e076dfd6670265d458eb35d8e1b3e4500009a12
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81534486"
---
# <a name="use-microsoft-authentication-library-for-javascript-to-work-with-azure-active-directory-b2c"></a>Użyj biblioteki uwierzytelniania firmy Microsoft dla języka JavaScript do pracy z Azure Active Directory B2C

[Biblioteka Microsoft Authentication Library for JavaScript (MSAL. js)](https://github.com/AzureAD/microsoft-authentication-library-for-js) umożliwia deweloperom języka JavaScript uwierzytelnianie użytkowników za pomocą tożsamości społecznościowych i lokalnych przy użyciu [Azure Active Directory B2C (Azure AD B2C)](https://docs.microsoft.com/azure/active-directory-b2c/). Korzystając z Azure AD B2C jako usługi zarządzania tożsamościami, można dostosowywać i kontrolować sposób tworzenia konta, logowania i zarządzania profilami klientów podczas korzystania z aplikacji.

Azure AD B2C umożliwia również markę i dostosowanie interfejsu użytkownika aplikacji podczas procesu uwierzytelniania, aby zapewnić klientom bezproblemowe środowisko.

W tym artykule pokazano, jak używać MSAL. js do pracy z Azure AD B2C i podsumowywania najważniejszych punktów, z którymi należy się zapoznać. Aby uzyskać pełną dyskusję i samouczek, zapoznaj się z [dokumentacją Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/overview).

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie utworzono jeszcze własnej [dzierżawy Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant), Zacznij od jej utworzenia (możesz też użyć istniejącej dzierżawy Azure AD B2C, jeśli już istnieje).

Ta Demonstracja zawiera dwie części:

- Jak chronić internetowy interfejs API.
- Jak zarejestrować jednostronicową aplikację w celu uwierzytelnienia i wywołania *tego* internetowego interfejsu API.

## <a name="nodejs-web-api"></a>Interfejs API sieci Web platformy Node.js

> [!NOTE]
> W tej chwili MSAL. js dla węzła nadal jest opracowywany (zobacz [Plan](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki#roadmap)). W międzyczasie sugerujemy korzystanie z [usługi Passport-Azure-AD](https://github.com/AzureAD/passport-azure-ad), biblioteki uwierzytelniania dla środowiska Node. js opracowanego i obsługiwanego przez firmę Microsoft.

Poniższe kroki przedstawiają sposób, w jaki **interfejs API sieci Web** może używać Azure AD B2C do ochrony samej siebie i uwidaczniania wybranych zakresów w aplikacji klienckiej.

### <a name="step-1-register-your-application"></a>Krok 1. Rejestrowanie aplikacji

Aby chronić internetowy interfejs API za pomocą Azure AD B2C, należy najpierw go zarejestrować. Aby uzyskać szczegółowe instrukcje, zobacz artykuł [Rejestrowanie aplikacji](https://docs.microsoft.com/azure/active-directory-b2c/add-web-application?tabs=applications) .

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

Aby uzyskać więcej informacji, zapoznaj się z [przykładem interfejsu API sieci Web B2C środowiska Node. js](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi).

---

## <a name="javascript-spa"></a>JavaScript SPA

Poniższe kroki przedstawiają sposób, w jaki **aplikacja jednostronicowa** może używać Azure AD B2C do rejestrowania, logowania i wywoływania chronionego internetowego interfejsu API.

### <a name="step-1-register-your-application"></a>Krok 1. Rejestrowanie aplikacji

Aby zaimplementować uwierzytelnianie, należy najpierw zarejestrować aplikację. Aby uzyskać szczegółowe instrukcje, zobacz artykuł [Rejestrowanie aplikacji](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-register-applications) .

### <a name="step-2-download-the-sample-application"></a>Krok 2. Pobieranie przykładowej aplikacji

Pobierz przykład jako plik zip lub Sklonuj go z witryny GitHub:

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

### <a name="step-3-configure-authentication"></a>Krok 3. Konfigurowanie uwierzytelniania

Aby skonfigurować aplikację, istnieją dwa kwestie:

- Konfigurowanie punktu końcowego interfejsu API i uwidocznionych zakresów
- Konfigurowanie parametrów uwierzytelniania i zakresów tokenów

1. Otwórz plik `apiConfig.js` w przykładowej aplikacji.

2. Skonfiguruj przykład za pomocą parametrów uzyskanych wcześniej podczas rejestrowania internetowego interfejsu API. Zmień następujące wiersze kodu, zastępując wartości adresami internetowego interfejsu API i uwidocznionych zakresów.

   ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    const apiConfig = {
        b2cScopes: ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"], //API scopes you exposed during api registration
        webApi: "https://fabrikamb2chello.azurewebsites.net/hello"
    };
   ```

3. Otwórz plik `authConfig.js` w przykładowej aplikacji.

4. Skonfiguruj przykład za pomocą parametrów uzyskanych wcześniej podczas rejestrowania aplikacji jednostronicowej. Zmień następujące wiersze kodu, zastępując wartości z ClientId, metadanych urzędu i zakresów żądania tokenu.

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

---

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o usługach:
- [Przepływy użytkownika](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-user-flows)
- [Zasady niestandardowe](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started)
- [Dostosowywanie interfejsu użytkownika](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-configure-user-input)
