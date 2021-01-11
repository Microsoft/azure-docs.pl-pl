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
ROBOTS: NOINDEX
ms.openlocfilehash: 62f4f629e44d317d36e182adb48f8f00b9f1c2b3
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/11/2021
ms.locfileid: "98063063"
---
# <a name="how-to-configure-single-sign-on-for-an-application"></a>Jak skonfigurować Logowanie jednokrotne dla aplikacji

Włączenie federacyjnego logowania jednokrotnego w aplikacji jest automatycznie włączane, gdy federowanie za pomocą usługi Azure AD dla OpenID Connect Connect, SAML 2,0 lub WS-karmione. Jeśli użytkownicy końcowi będą mogli się zalogować pomimo posiadania już istniejącej sesji z usługą Azure AD, prawdopodobnie aplikacja może być błędnie skonfigurowana.

* Jeśli używasz biblioteki ADAL/MSAL, upewnij się, że masz **PromptBehavior** ustawioną na wartość **Autostart** , a nie **zawsze**.

* W przypadku kompilowania aplikacji mobilnej może być konieczne dodanie dodatkowych konfiguracji w celu włączenia obsługi rejestracji Jednokrotnej dla brokera lub nieobsługiwanego przez brokera.

W przypadku systemu Android zobacz [Włączanie logowania jednokrotnego aplikacji w systemie Android](../azuread-dev/howto-v1-enable-sso-android.md).<br>

W przypadku systemu iOS zobacz [Włączanie logowania jednokrotnego aplikacji w systemie iOS](../azuread-dev/howto-v1-enable-sso-ios.md).

## <a name="next-steps"></a>Następne kroki

[Logowanie jednokrotne usługi Azure AD](../manage-apps/what-is-single-sign-on.md)<br>

[Włączanie logowania jednokrotnego aplikacji w systemie Android](../azuread-dev/howto-v1-enable-sso-android.md)<br>

[Włączanie logowania jednokrotnego aplikacji w systemie iOS](../azuread-dev/howto-v1-enable-sso-ios.md)<br>

[Integrowanie aplikacji z usługą AzureAD](./quickstart-register-app.md)<br>

[Uprawnienia i zgoda w punkcie końcowym platformy tożsamości firmy Microsoft](./v2-permissions-and-consent.md)<br>

[AzureAD StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)