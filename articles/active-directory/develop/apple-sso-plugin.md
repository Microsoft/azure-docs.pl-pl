---
title: Wtyczka logowania jednokrotnego Microsoft Enterprise dla urządzeń firmy Apple
titleSuffix: Microsoft identity platform | Azure
description: Dowiedz się więcej na temat wtyczki Microsoft Azure Active Directory SSO dla urządzeń z systemem iOS i macOS.
services: active-directory
author: brandwe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/31/2020
ms.author: brandwe
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: f98be2ef6a82c099425655fd7e5d25a4358844bf
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/11/2020
ms.locfileid: "88115445"
---
# <a name="microsoft-enterprise-sso-plug-in-for-apple-devices-preview"></a>Wtyczka Microsoft Enterprise SSO dla urządzeń firmy Apple (wersja zapoznawcza)

> [!NOTE]
> Ta funkcja jest dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

*Wtyczka Logowanie jednokrotne w przedsiębiorstwie firmy Microsoft dla urządzeń firmy Apple* zapewnia Logowanie jednokrotne (SSO) dla kont usługi Azure Active Directory (Azure AD) we wszystkich aplikacjach, które obsługują funkcję logowania jednokrotnego [w przedsiębiorstwie](https://developer.apple.com/documentation/authenticationservices) firmy Apple. Firma Microsoft pracowała ściśle z firmą Apple, aby opracować Tę wtyczkę w celu zwiększenia użyteczności aplikacji przy jednoczesnym zapewnieniu najlepszej ochrony zapewnianej przez firmę Apple i firmę Microsoft.

W tej publicznej wersji zapoznawczej wtyczka logowania jednokrotnego w przedsiębiorstwie jest dostępna tylko dla urządzeń z systemem iOS i jest dystrybuowana w określonych aplikacjach firmy Microsoft.

Pierwsze korzystanie z wtyczki logowania jednokrotnego w przedsiębiorstwie odbywa się przy użyciu naszej nowej funkcji [trybu udostępnionego urządzenia](msal-ios-shared-devices.md) .

## <a name="features"></a>Funkcje

Wtyczka logowania jednokrotnego w przedsiębiorstwie firmy Microsoft dla urządzeń firmy Apple oferuje następujące korzyści:

- Zapewnia Logowanie jednokrotne dla kont usługi Azure AD we wszystkich aplikacjach, które obsługują funkcję logowania jednokrotnego w przedsiębiorstwie firmy Apple.
- Dostarczane automatycznie w Microsoft Authenticator i mogą być włączane przez dowolne rozwiązanie do zarządzania urządzeniami przenośnymi (MDM).

## <a name="requirements"></a>Wymagania

Aby skorzystać z wtyczki Microsoft Enterprise SSO dla urządzeń firmy Apple:

- na urządzeniu musi być zainstalowany system iOS w wersji 13,0 lub nowszej.
- Na urządzeniu musi być zainstalowana aplikacja firmy Microsoft udostępniająca dodatek logowania jednokrotnego dla przedsiębiorstwa firmy Microsoft dla urządzeń firmy Apple. W publicznej wersji zapoznawczej te aplikacje zawierają [Microsoft Authenticator aplikację](../user-help/user-help-auth-app-overview.md).
- Urządzenie musi być zarejestrowane w systemie zarządzania urządzeniami przenośnymi (na przykład z Microsoft Intune).
- Aby włączyć wtyczkę logowania jednokrotnego w przedsiębiorstwie dla urządzeń firmy Microsoft na urządzeniu, należy przeprowadzić konfigurację wypychaną na urządzenie. To ograniczenie zabezpieczeń jest wymagane przez firmę Apple.

## <a name="enable-the-sso-extension-with-mobile-device-management-mdm"></a>Włączanie rozszerzenia SSO przy użyciu funkcji zarządzania urządzeniami przenośnymi (MDM)

Aby włączyć wtyczkę logowania jednokrotnego w przedsiębiorstwie firmy Microsoft dla urządzeń firmy Apple, urządzenia muszą wysyłać sygnały za pomocą usługi MDM. Ponieważ firma Microsoft obejmuje wtyczkę logowania jednokrotnego dla przedsiębiorstw w [aplikacji Microsoft Authenticator](..//user-help/user-help-auth-app-overview.md), Użyj urządzenia MDM do skonfigurowania aplikacji w celu włączenia wtyczki Microsoft Enterprise SSO.

Użyj następujących parametrów, aby skonfigurować wtyczkę logowania jednokrotnego w przedsiębiorstwie firmy Microsoft dla urządzeń firmy Apple:

- **Typ**: redirect
- **Identyfikator rozszerzenia**:`com.microsoft.azureauthenticator.ssoextension`
- **Identyfikator zespołu**:`SGGM6D27TK`
- **Adresy URL**:
  - `https://login.microsoftonline.com`
  - `https://login.windows.net`
  - `https://login.microsoft.com`
  - `https://sts.windows.net`
  - `https://login.partner.microsoftonline.cn`
  - `https://login.chinacloudapi.cn`
  - `https://login.microsoftonline.de`
  - `https://login.microsoftonline.us`
  - `https://login.usgovcloudapi.net`
  - `https://login-us.microsoftonline.com`

Microsoft Intune jako usługi MDM można użyć, aby ułatwić konfigurację wtyczki logowania jednokrotnego w przedsiębiorstwie firmy Microsoft. Aby uzyskać więcej informacji, zobacz [dokumentację konfiguracyjną usługi Intune](/intune/configuration/ios-device-features-settings).

## <a name="using-the-sso-extension-in-your-application"></a>Korzystanie z rozszerzenia SSO w aplikacji

[Biblioteka Microsoft Authentication Library (MSAL) dla urządzeń firmy Apple](https://github.com/AzureAD/microsoft-authentication-library-for-objc) w wersji 1.1.0 lub nowszej obsługuje wtyczkę logowania jednokrotnego w przedsiębiorstwie firmy Microsoft dla urządzeń firmy Apple.

Jeśli chcesz obsługiwać tryb udostępnionego urządzenia udostępniony przez wtyczkę logowania jednokrotnego w przedsiębiorstwie firmy Microsoft dla urządzeń firmy Apple, upewnij się, że aplikacje używają określonej minimalnej wymaganej wersji programu MSAL.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat trybu udostępnionego urządzenia w systemie iOS, zobacz [tryb udostępnionego urządzenia dla urządzeń z systemem iOS](msal-ios-shared-devices.md).