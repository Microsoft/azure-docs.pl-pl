---
title: Konfigurowanie logowania jednokrotnego aplikacji
description: Jak skonfigurować Logowanie jednokrotne dla aplikacji niestandardowej, którą tworzysz i rejestrujesz w usłudze Azure AD.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 07/15/2019
ms.author: ryanwi
ms.openlocfilehash: 9b6451d29a3a874e09d74cbe664662a395b647f9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "82890463"
---
# <a name="how-to-configure-single-sign-on-for-an-application"></a>Jak skonfigurować Logowanie jednokrotne dla aplikacji

Włączenie federacyjnego logowania jednokrotnego w aplikacji jest automatycznie włączane, gdy federowanie za pomocą usługi Azure AD dla OpenID Connect Connect, SAML 2,0 lub WS-karmione. Jeśli użytkownicy końcowi będą mogli się zalogować pomimo posiadania już istniejącej sesji z usługą Azure AD, prawdopodobnie aplikacja może być błędnie skonfigurowana.

* Jeśli używasz biblioteki ADAL/MSAL, upewnij się, że masz **PromptBehavior** ustawioną na wartość **Autostart** , a nie **zawsze**.

* W przypadku kompilowania aplikacji mobilnej może być konieczne dodanie dodatkowych konfiguracji w celu włączenia obsługi rejestracji Jednokrotnej dla brokera lub nieobsługiwanego przez brokera.

W przypadku systemu Android zobacz [Włączanie logowania jednokrotnego aplikacji w systemie Android](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-android).<br>

W przypadku systemu iOS zobacz [Włączanie logowania jednokrotnego aplikacji w systemie iOS](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-ios).

## <a name="next-steps"></a>Następne kroki

[Logowanie jednokrotne usługi Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)<br>

[Włączanie logowania jednokrotnego aplikacji w systemie Android](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-android)<br>

[Włączanie logowania jednokrotnego aplikacji w systemie iOS](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-ios)<br>

[Integrowanie aplikacji z usługą AzureAD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)<br>

[Uprawnienia i zgoda w punkcie końcowym platformy tożsamości firmy Microsoft](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[AzureAD StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
