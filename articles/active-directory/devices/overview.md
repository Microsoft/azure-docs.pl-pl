---
title: Co to jest tożsamość urządzenia w usłudze Azure Active Directory?
description: Dowiedz się, jak zarządzanie tożsamościami urządzeń może pomóc w zarządzaniu urządzeniami uzyskującymi dostęp do zasobów w twoim środowisku.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: overview
ms.date: 03/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: ba0630474224c34eb96429cd7592028362e03381
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "82024442"
---
# <a name="what-is-a-device-identity"></a>Co to jest tożsamość urządzenia?

Wraz z rozprzestrzenianiem się urządzeń o różnych kształtach i rozmiarach oraz koncepcją Bring Your Own Device (BYOD), informatycy mają do czynienia z dwoma nieco przeciwstawnymi celami:

- Zezwalaj użytkownikom końcowym na produktywność w dowolnym miejscu i czasie
- Ochrona zasobów organizacji

Aby chronić te zasoby, pracownicy IT muszą najpierw zarządzać tożsamościami urządzeń. Pracownicy działu IT mogą chować się na tożsamości urządzenia za pomocą narzędzi, takich jak Microsoft Intune, aby zapewnić zgodność standardów zabezpieczeń i zgodności. Usługa Azure Active Directory (Azure AD) umożliwia logowanie jednokrotne na urządzeniach, aplikacjach i usługach z dowolnego miejsca za pośrednictwem tych urządzeń.

- Użytkownicy uzyskują dostęp do potrzebnych im zasobów organizacji. 
- Pracownicy IT otrzymują kontrole potrzebne do zabezpieczenia organizacji.

Zarządzanie tożsamością urządzeń jest podstawą [dostępu warunkowego opartego na urządzeniach](../conditional-access/require-managed-devices.md). Dzięki zasadom dostępu warunkowego opartego na urządzeniach można zapewnić, że dostęp do zasobów w twoim środowisku jest możliwy tylko na urządzeniach zarządzanych.

## <a name="getting-devices-in-azure-ad"></a>Uzyskiwanie urządzeń w usłudze Azure AD

Aby uzyskać urządzenie w usłudze Azure AD, masz wiele opcji:

- **Zarejestrowane w usłudze Azure AD**
   - Urządzenia zarejestrowane w usłudze Azure AD są zazwyczaj własnością osobiście lub urządzeniami przenośnymi i są zalogowane za pomocą osobistego konta Microsoft lub innego konta lokalnego.
      - Windows 10
      - iOS
      - Android
      - MacOS
- **Dołączone do usługi Azure AD**
   - Urządzenia, które są przyłączone do usługi Azure AD, są własnością organizacji i są zalogowane przy za pomocą konta usługi Azure AD należącego do tej organizacji. Istnieją one tylko w chmurze.
      - Windows 10 
      - Windows Server 2019 (rdzeń serwera nie jest obsługiwany)
- **hybrydowym dołączonym do usługi Azure AD**.
   - Urządzenia, które są przyłączone do usługi Azure AD są własnością organizacji i są zalogowane przy za pomocą konta usługi Azure AD należącego do tej organizacji. Istnieją one w chmurze i lokalnie.
      - Windows 7, 8.1 lub 10
      - Windows Server 2008 lub nowszy

![Urządzenia wyświetlane w bloku Urządzenia usługi Azure AD](./media/overview/azure-active-directory-devices-all-devices.png)

> [!NOTE]
> Stan hybrydowy odnosi się do więcej niż tylko stan urządzenia. Aby stan hybrydowy był prawidłowy, wymagany jest również prawidłowy użytkownik usługi Azure AD.

## <a name="device-management"></a>Zarządzanie urządzeniami

Urządzeniami w usłudze Azure AD można zarządzać za pomocą narzędzi zarządzania urządzeniami przenośnymi (MDM), takich jak Microsoft Intune, Microsoft Endpoint Configuration Manager, Group Policy (hybrid Azure AD join), Mobile Application Management (MAM) lub innych narzędzi innych firm.

## <a name="resource-access"></a>Dostęp do zasobów

Rejestrowanie i łączenie urządzeń z usługą Azure AD zapewnia użytkownikom bezproblemowe logowanie (Logowanie jednokrotne) do zasobów w chmurze. Ten proces umożliwia również administratorom stosowanie zasad dostępu warunkowego do zasobów na podstawie urządzenia, z których są uzyskiwać dostęp. 

> [!NOTE]
> Zasady dostępu warunkowego oparte na urządzeniach wymagają hybrydowych urządzeń przyłączonych do usługi Azure AD lub zgodnych urządzeń przyłączonych do usługi Azure AD lub urządzeń zarejestrowanych w usłudze Azure AD.

Podstawowy token odświeżania (PRT) zawiera informacje o urządzeniu i jest wymagany dla logowania pierwszego przyuśtu. Jeśli masz ustawione zasady dostępu warunkowego oparte na urządzeniu w aplikacji, bez PRT, odmowa dostępu. Zasady hybrydowego dostępu warunkowego wymagają urządzenia stanu hybrydowego i prawidłowego użytkownika, który jest zalogowany.

Urządzenia, które są przyłączone do usługi Azure AD lub hybrydowe usługi Azure AD przyłączone korzyści z jednokrotnego użytkownika do zasobów lokalnych organizacji, a także zasobów w chmurze. Więcej informacji można znaleźć w artykule Jak [samouszeńców do zasobów lokalnych działa na urządzeniach przyłączonych do usługi Azure AD.](azuread-join-sso.md)

## <a name="device-security"></a>Zabezpieczenia urządzeń

- **Urządzenia zarejestrowane w usłudze Azure AD** korzystają z konta zarządzanego przez użytkownika końcowego, to konto jest kontem Microsoft lub innym lokalnie zarządzanym poświadczeniem zabezpieczonym jednym lub kilkoma z poniższych.
   - Hasło
   - Kod PIN
   - Wzorce
   - Windows Hello
- **Urządzenia przyłączone do usługi Azure AD lub hybrydowe przyłączone do usługi Azure AD** wykorzystują konto organizacyjne w usłudze Azure AD zabezpieczone co najmniej jednym z poniższych.
   - Hasło
   - Windows Hello dla firm

## <a name="provisioning"></a>Inicjowanie obsługi

Wprowadzenie urządzeń do usługi Azure AD można wykonać w sposób samoobsługowy lub kontrolowany proces inicjowania obsługi administracyjnej przez administratorów.

## <a name="summary"></a>Podsumowanie

Dzięki zarządzaniu tożsamością urządzeń w usłudze Azure AD można:

- Uprość proces wprowadzania urządzeń i zarządzania nimi w usłudze Azure AD
- Zapewnienie użytkownikom łatwego dostępu do zasobów chmurowych organizacji

## <a name="license-requirements"></a>Wymagania licencyjne

[!INCLUDE [Active Directory P1 license](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [urządzeniach zarejestrowanych w usłudze Azure AD](concept-azure-ad-register.md)
- Dowiedz się więcej o [urządzeniach przyłączonych do usługi Azure AD](concept-azure-ad-join.md)
- Dowiedz się więcej o [hybrydowych urządzeniach przyłączonych do usługi Azure AD](concept-azure-ad-join-hybrid.md)
- Aby uzyskać omówienie sposobu zarządzania tożsamościami urządzeń w witrynie Azure portal, zobacz [Zarządzanie tożsamościami urządzeń przy użyciu witryny Azure portal.](device-management-azure-portal.md)
- Aby dowiedzieć się więcej o dostępie warunkowym opartym na urządzeniach, zobacz [Konfigurowanie zasad dostępu warunkowego opartego na urządzeniach usługi Azure Active Directory](../conditional-access/require-managed-devices.md).
