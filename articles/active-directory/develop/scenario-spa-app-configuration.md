---
title: Konfigurowanie aplikacji jednostronicowej — platforma tożsamości firmy Microsoft | Azure
description: Dowiedz się, jak utworzyć aplikację jednostronicową (Konfiguracja kodu aplikacji)
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/11/2020
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: b42d720a425b92ec9002f7c2b9797a91f70dafe2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89003031"
---
# <a name="single-page-application-code-configuration"></a>Aplikacja jednostronicowa: Konfiguracja kodu

Dowiedz się, jak skonfigurować kod dla aplikacji jednostronicowej (SPA).

## <a name="msal-libraries-for-spas-and-supported-authentication-flows"></a>MSAL biblioteki aplikacji jednostronicowych i obsługiwane przepływy uwierzytelniania

Platforma tożsamości firmy Microsoft udostępnia następujące biblioteki uwierzytelniania firmy Microsoft dla języka JavaScript (MSAL.js) do obsługi niejawnego przepływu kodu przepływu i autoryzacji z PKCE przy użyciu zalecanych przez branżę praktyk związanych z bezpieczeństwem:

| Biblioteka MSAL | Przepływ | Opis |
|--------------|------|-------------|
| ![MSAL.js](media/sample-v2-code/logo_js.png) <br/> [MSAL.js (2. x)](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser) | Przepływ kodu autoryzacji (PKCE) | Zwykła biblioteka języka JavaScript do użycia w dowolnej aplikacji sieci Web po stronie klienta, która jest skompilowana za pomocą języka JavaScript lub SPA platform, takich jak kątowy, Vue.js i React.js. |
| ![MSAL.js](media/sample-v2-code/logo_js.png) <br/> [MSAL.js (1. x)](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-core) | Niejawny przepływ | Zwykła biblioteka języka JavaScript do użycia w dowolnej aplikacji sieci Web po stronie klienta, która jest skompilowana za pomocą języka JavaScript lub SPA platform, takich jak kątowy, Vue.js i React.js. |
| ![MSAL kątowy](media/sample-v2-code/logo_angular.png) <br/> [MSAL kątowy](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | Niejawny przepływ | Otoka podstawowej biblioteki MSAL.js, która upraszcza korzystanie z aplikacji jednostronicowych zbudowanych za pomocą struktury kątowej. |

## <a name="application-code-configuration"></a>Konfiguracja kodu aplikacji

W bibliotece MSAL informacje o rejestracji aplikacji są przesyłane jako Konfiguracja podczas inicjowania biblioteki.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
// Configuration object constructed.
const config = {
    auth: {
        clientId: 'your_client_id'
    }
};

// create UserAgentApplication instance
const userAgentApplication = new UserAgentApplication(config);
```

Aby uzyskać więcej informacji na temat konfigurowalnych opcji, zobacz [Inicjowanie aplikacji przy użyciu MSAL.js](msal-js-initializing-client-applications.md).

# <a name="angular"></a>[Angular](#tab/angular)

```javascript
// App.module.ts
import { MsalModule } from '@azure/msal-angular';

@NgModule({
    imports: [
        MsalModule.forRoot({
            auth: {
                clientId: 'your_app_id'
            }
        })
    ]
})

export class AppModule { }
```

---

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Logowanie i wylogowywanie](scenario-spa-sign-in.md)
