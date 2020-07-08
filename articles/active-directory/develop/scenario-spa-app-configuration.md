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
ms.openlocfilehash: f159105046231ba5fb4e458cdd70d930a411a920
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "80882339"
---
# <a name="single-page-application-code-configuration"></a>Aplikacja jednostronicowa: Konfiguracja kodu

Dowiedz się, jak skonfigurować kod dla aplikacji jednostronicowej (SPA).

## <a name="msal-libraries-that-support-implicit-flow"></a>Biblioteki MSAL obsługujące niejawny przepływ

Platforma tożsamości firmy Microsoft udostępnia następujące biblioteki Microsoft Authentication Library (MSAL), które umożliwiają obsługę niejawnego przepływu przy użyciu zalecanych przez branżę praktyk zabezpieczeń:

| Biblioteka MSAL | Opis |
|--------------|--------------|
| ![MSAL.js](media/sample-v2-code/logo_js.png) <br/> [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)  | Zwykła biblioteka języka JavaScript do użycia w dowolnej aplikacji sieci Web po stronie klienta, która jest skompilowana za pomocą języka JavaScript lub SPA platform, takich jak kątowy, Vue.js i React.js. |
| ![MSAL kątowy](media/sample-v2-code/logo_angular.png) <br/> [MSAL kątowy](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | Otoka podstawowej biblioteki MSAL.js, która upraszcza korzystanie z aplikacji jednostronicowych zbudowanych za pomocą struktury kątowej. |

## <a name="application-code-configuration"></a>Konfiguracja kodu aplikacji

W bibliotece MSAL informacje o rejestracji aplikacji są przesyłane jako Konfiguracja podczas inicjowania biblioteki.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
// Configuration object constructed.
const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri" //defaults to application start page
    }
}

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
