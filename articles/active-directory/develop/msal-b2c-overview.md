---
title: Korzystanie z usługi MSAL w usłudze Azure Active Directory B2CLearn | Azure
titleSuffix: Microsoft identity platform
description: Biblioteka uwierzytelniania firmy Microsoft dla języka JavaScript (MSAL.js) umożliwia aplikacjom pracę z usługą Azure AD B2C i uzyskiwanie tokenów do wywoływania zabezpieczonych interfejsów API sieci Web. Te internetowe interfejsy API mogą być microsoft graph, inne interfejsy API firmy Microsoft, interfejsy API sieci web od innych lub własnego interfejsu API sieci web.
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
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81534486"
---
# <a name="use-microsoft-authentication-library-for-javascript-to-work-with-azure-active-directory-b2c"></a>Korzystanie z biblioteki uwierzytelniania Firmy Microsoft w języku JavaScript w celu współpracy z usługą Azure Active Directory B2C

[Biblioteka uwierzytelniania firmy Microsoft dla języka JavaScript (MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js) umożliwia deweloperom javascript uwierzytelnianie użytkowników przy użyciu tożsamości społecznościowych i lokalnych przy użyciu [usługi Azure Active Directory B2C (Azure AD B2C).](https://docs.microsoft.com/azure/active-directory-b2c/) Korzystając z usługi Azure AD B2C jako usługi zarządzania tożsamościami, można dostosować i kontrolować sposób, w jaki klienci się logują, logują się i zarządzają swoimi profilami podczas korzystania z aplikacji.

Usługa Azure AD B2C umożliwia również znakowanie i dostosowywanie interfejsu użytkownika aplikacji podczas procesu uwierzytelniania w celu zapewnienia klientom bezproblemowego korzystania z nich.

W tym artykule pokazano, jak używać msal.js do pracy z usługą Azure AD B2C i podsumowuje kluczowych punktów, które należy pamiętać. Aby uzyskać pełną dyskusję i samouczek, zapoznaj się z [dokumentacją usługi Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/overview).

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie utworzono jeszcze własnej [dzierżawy usługi Azure AD B2C,](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant)zacznij od utworzenia jej teraz (możesz również użyć istniejącej dzierżawy usługi Azure AD B2C, jeśli już ją masz).

Ta demonstracja składa się z dwóch części:

- jak chronić internetowy interfejs API.
- jak zarejestrować aplikację jednostronicową do uwierzytelniania i wywołania *tego* internetowego interfejsu API.

## <a name="nodejs-web-api"></a>Interfejs API sieci Web platformy Node.js

> [!NOTE]
> W tej chwili MSAL.js dla Node jest nadal w fazie rozwoju (patrz [mapa drogowa](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki#roadmap)). W międzyczasie zalecamy użycie [passport-azure-ad](https://github.com/AzureAD/passport-azure-ad), biblioteki uwierzytelniania dla node.js opracowanej i obsługiwanej przez firmę Microsoft.

Poniższe kroki pokazują, jak **internetowy interfejs API** może używać usługi Azure AD B2C do ochrony i udostępnienia wybranych zakresów aplikacji klienckiej.

### <a name="step-1-register-your-application"></a>Krok 1. Rejestrowanie aplikacji

Aby chronić internetowy interfejs API za pomocą usługi Azure AD B2C, należy go najpierw zarejestrować. Zobacz [Rejestrowanie aplikacji, aby](https://docs.microsoft.com/azure/active-directory-b2c/add-web-application?tabs=applications) uzyskać szczegółowe kroki.

### <a name="step-2-download-the-sample-application"></a>Krok 2: Pobierz przykładową aplikację

Pobierz próbkę jako plik zip lub sklonuj go z GitHub:

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

### <a name="step-3-configure-authentication"></a>Krok 3: Konfigurowanie uwierzytelniania

1. Otwórz plik `config.js` w przykładowej aplikacji.

2. Skonfiguruj przykład z poświadczeniami aplikacji, które zostały uzyskane wcześniej podczas rejestrowania aplikacji. Zmień następujące wiersze kodu, zastępując wartości nazwami identyfikatora klienta, hosta, identyfikatora dzierżawcy i nazwy zasad.

```JavaScript
const clientID = "<Application ID for your Node.js web API - found on Properties page in Azure portal e.g. 93733604-cc77-4a3c-a604-87084dd55348>";
const b2cDomainHost = "<Domain of your B2C host eg. fabrikamb2c.b2clogin.com>";
const tenantId = "<your-tenant-ID>.onmicrosoft.com"; // Alternatively, you can use your Directory (tenant) ID (GUID)
const policyName = "<Name of your sign in / sign up policy, e.g. B2C_1_signupsignin1>";
```

Aby uzyskać więcej informacji, zapoznaj się z tym [przykładem internetowego interfejsu API Node.js B2C](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi).

---

## <a name="javascript-spa"></a>JavaScript SPA

Poniższe kroki pokazują, jak **aplikacja jednostronicowa** może używać usługi Azure AD B2C do rejestrowania się, logowania i wywoływania chronionego interfejsu API sieci Web.

### <a name="step-1-register-your-application"></a>Krok 1. Rejestrowanie aplikacji

Aby zaimplementować uwierzytelnianie, należy najpierw zarejestrować aplikację. Zobacz [Rejestrowanie aplikacji, aby](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-register-applications) uzyskać szczegółowe kroki.

### <a name="step-2-download-the-sample-application"></a>Krok 2: Pobierz przykładową aplikację

Pobierz próbkę jako plik zip lub sklonuj go z GitHub:

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

### <a name="step-3-configure-authentication"></a>Krok 3: Konfigurowanie uwierzytelniania

Konfigurowanie aplikacji mają dwa punkty szczególne:

- Konfigurowanie punktu końcowego interfejsu API i ujawnionych zakresów
- Konfigurowanie parametrów uwierzytelniania i zakresów tokenów

1. Otwórz plik `apiConfig.js` w przykładowej aplikacji.

2. Skonfiguruj przykład z parametrami uzyskanymi wcześniej podczas rejestrowania internetowego interfejsu API. Zmień następujące wiersze kodu, zastępując wartości adresem internetowego interfejsu API i ujawnionych zakresów.

   ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    const apiConfig = {
        b2cScopes: ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"], //API scopes you exposed during api registration
        webApi: "https://fabrikamb2chello.azurewebsites.net/hello"
    };
   ```

3. Otwórz plik `authConfig.js` w przykładowej aplikacji.

4. Skonfiguruj przykład z parametrami uzyskanymi wcześniej podczas rejestrowania aplikacji jednostronicowej. Zmień następujące wiersze kodu, zastępując wartości identyfikatorem klienta, metadanymi uprawnień i zakresami żądań tokenu.

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

Aby uzyskać więcej informacji, zapoznaj się z tym [javascript B2C próbka aplikacji jednostronicowej](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp).

---

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o usługach:
- [Przepływy użytkownika](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-user-flows)
- [Zasady niestandardowe](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started)
- [Dostosowywanie interfejsu użytkownika](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-configure-user-input)
