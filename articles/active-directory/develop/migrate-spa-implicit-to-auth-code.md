---
title: Migrowanie aplikacji jednostronicowej JavaScript z niejawnego przydzielenia do przepływu kodu autoryzacji | Azure
titleSuffix: Microsoft identity platform
description: Jak zaktualizować SPA w języku JavaScript przy użyciu MSAL.js 1. x i niejawnego przepływu dotacji do MSAL.js 2. x i przepływu kodu autoryzacji z obsługą PKCE i CORS.
services: active-directory
author: hahamil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 07/17/2020
ms.author: hahamil
ms.custom: aaddev, devx-track-js
ms.openlocfilehash: 2c03e6940d392e3e2c03bf6508de41a7a19aef3b
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/11/2021
ms.locfileid: "98063777"
---
# <a name="migrate-a-javascript-single-page-app-from-implicit-grant-to-auth-code-flow"></a>Migrowanie aplikacji jednostronicowej JavaScript z niejawnego przydzielenia do przepływu kodu uwierzytelniania

Biblioteka Microsoft Authentication Library for JavaScript (MSAL.js) v 2.0 obsługuje przepływ kodu autoryzacji z PKCE i CORS do aplikacji jednostronicowych na platformie tożsamości firmy Microsoft. Wykonaj kroki opisane w poniższych sekcjach, aby przeprowadzić migrację aplikacji MSAL.js 1. x przy użyciu niejawnego przydzielenia do MSAL.js 2.0 + (poniżej *2. x*) i przepływu kodu uwierzytelniania.

MSAL.js 2. x ulepsza MSAL.js 1. x przez obsługę przepływu kodu autoryzacji w przeglądarce zamiast niejawnego przepływu dotacji. MSAL.js 2. x nie **obsługuje przepływu** niejawnego.

## <a name="migration-steps"></a>Kroki migracji

Aby zaktualizować aplikację do MSAL.js 2. x i przepływ kodu uwierzytelniania, należy wykonać trzy podstawowe czynności:

1. Przełącz identyfikatory URI przekierowania [rejestracji aplikacji](#switch-redirect-uris-to-spa-platform) z platformy **sieci Web** na **jednostronicową** platformę aplikacji.
1. Zaktualizuj [kod](#switch-redirect-uris-to-spa-platform) z MSAL.js 1. x do **2. x**.
1. Wyłącz [niejawne przyznanie](#disable-implicit-grant-settings) w rejestracji aplikacji, gdy wszystkie aplikacje, które współużytkują rejestrację, zostały zaktualizowane do MSAL.js 2. x i przepływ kodu uwierzytelniania.

W poniższych sekcjach opisano każdy krok z dodatkowymi szczegółami.

## <a name="switch-redirect-uris-to-spa-platform"></a>Przełącz identyfikatory URI przekierowania na platformę SPA

Jeśli chcesz kontynuować korzystanie z istniejącej rejestracji aplikacji dla aplikacji, użyj Azure Portal, aby zaktualizować identyfikatory URI przekierowania rejestracji na platformę SPA. Umożliwi to przepływ kodu autoryzacji z obsługą PKCE i CORS dla aplikacji korzystających z rejestracji (nadal trzeba zaktualizować kod aplikacji do MSAL.js v2. x).

Wykonaj następujące kroki w przypadku rejestracji aplikacji skonfigurowanych obecnie przy użyciu identyfikatorów URI przekierowań platformy **sieci Web** :

1. Zaloguj się do <a href="https://portal.azure.com/" target="_blank">Azure Portal <span class="docon docon-navigate-external x-hidden-focus"></span></a> i wybierz dzierżawę **Azure Active Directory** .
1. W **rejestracje aplikacji** wybierz aplikację, a następnie pozycję **uwierzytelnianie**.
1. Na kafelku Platforma **sieci Web** w obszarze **identyfikatory URI przekierowania** wybierz transparent ostrzegawczy wskazujący, że chcesz migrować identyfikatory URI.

    :::image type="content" source="media/migrate-spa-implicit-to-auth-code/portal-01-implicit-warning-banner.png" alt-text="Transparent ostrzeżenia o niejawnym przepływie na kafelku aplikacji sieci Web w Azure Portal":::
1. Wybierz *tylko* te identyfikatory URI przekierowania, których aplikacje będą używać MSAL.js 2. x, a następnie wybierz pozycję **Konfiguruj**.

    :::image type="content" source="media/migrate-spa-implicit-to-auth-code/portal-02-select-redirect-uri.png" alt-text="Wybieranie okienka URI przekierowania w okienku SPA w Azure Portal":::

Te identyfikatory URI przekierowania powinny teraz pojawić się na kafelku platformy **aplikacji jednostronicowej** , pokazując, że obsługa mechanizmu CORS z przepływem kodu autoryzacji i PKCE jest włączona dla tych identyfikatorów URI.

:::image type="content" source="media/migrate-spa-implicit-to-auth-code/portal-03-spa-redirect-uri-tile.png" alt-text="Kafelek aplikacji jednostronicowej w ramach rejestracji aplikacji w Azure Portal":::

Możesz również [utworzyć nową rejestrację aplikacji](scenario-spa-app-registration.md) zamiast aktualizować identyfikatory URI przekierowania w istniejącej rejestracji.

## <a name="update-your-code-to-msaljs-2x"></a>Aktualizowanie kodu do MSAL.js 2. x

W MSAL 1. x utworzono wystąpienie aplikacji, inicjując [UserAgentApplication][msal-js-useragentapplication] w następujący sposób:

```javascript
// MSAL 1.x
import * as msal from "msal";

const msalInstance = new msal.UserAgentApplication(config);
```

W MSAL 2. x zainicjuj zamiast [PublicClientApplication][msal-js-publicclientapplication]:

```javascript
// MSAL 2.x
import * as msal from "@azure/msal-browser";

const msalInstance = new msal.PublicClientApplication(config);
```

Aby dowiedzieć się, jak dodać MSAL 2. x do aplikacji, zobacz [Samouczek: Logowanie użytkowników i wywoływanie interfejsu API Microsoft Graph z aplikacji jednostronicowej JavaScript (Spa) przy użyciu przepływu kodu uwierzytelniania](tutorial-v2-javascript-auth-code.md).

Dodatkowe zmiany, które należy wykonać w kodzie, można znaleźć w [przewodniku migracji](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/v1-migration.md) w witrynie GitHub.

## <a name="disable-implicit-grant-settings"></a>Wyłącz ustawienia niejawnego przydzielenia

Po zaktualizowaniu wszystkich aplikacji produkcyjnych, które używają tej rejestracji aplikacji i identyfikatora klienta do MSAL 2. x i przepływu kodu autoryzacji, należy usunąć zaznaczenie niejawnych ustawień uprawnień w rejestracji aplikacji.

Po odinstalowaniu niejawnych ustawień uprawnień w rejestracji aplikacji niejawny przepływ zostanie wyłączony dla wszystkich aplikacji korzystających z rejestracji i identyfikatora klienta.

**Nie** należy wyłączać niejawnego przepływu dotacji przed zaktualizowaniem wszystkich aplikacji do MSAL.js 2. x i [PublicClientApplication][msal-js-publicclientapplication].

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat przepływu kodu autoryzacji, w tym różnic między przepływami kodu niejawnego i uwierzytelniania, zobacz [przepływ kodu autoryzacji Microsoft Identity platform i OAuth 2,0](v2-oauth2-auth-code-flow.md).

Jeśli chcesz szczegółowe bardziej szczegółowe Programowanie aplikacji w języku JavaScript na platformie tożsamości firmy Microsoft, scenariusz wieloetapowy [: jednostronicowa seria aplikacji](scenario-spa-overview.md) może pomóc Ci rozpocząć pracę.

<!-- LINKS - external -->
[msal-js-useragentapplication]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-core/modules/_useragentapplication_.html
[msal-js-publicclientapplication]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-browser/classes/_src_app_publicclientapplication_.publicclientapplication.html
