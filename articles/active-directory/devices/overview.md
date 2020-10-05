---
title: Co to jest tożsamość urządzenia w Azure Active Directory?
description: Dowiedz się, jak zarządzanie tożsamościami urządzeń może ułatwić zarządzanie urządzeniami, które uzyskują dostęp do zasobów w danym środowisku.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: overview
ms.date: 07/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8399f7101697af429b8c073c101dbfea203e98ea
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "87025595"
---
# <a name="what-is-a-device-identity"></a>Co to jest tożsamość urządzenia?

Dzięki rozłącznym urządzeniom wszystkich kształtów i rozmiarów oraz koncepcji "Dobierz własne urządzenie" (BYOD), specjaliści IT są podłączeni do dwóch zamierzonych celów:

- Zezwalaj użytkownikom końcowym na produktywność wszędzie tam, gdzie i za każdym razem
- Ochrona zasobów organizacji

Aby chronić te zasoby, pracownicy działu IT muszą najpierw zarządzać tożsamościami urządzeń. Pracownicy IT mogą kompilować tożsamość urządzenia przy użyciu narzędzi, takich jak Microsoft Intune, aby zapewnić zgodność ze standardami bezpieczeństwa i zgodności. Usługa Azure Active Directory (Azure AD) umożliwia logowanie jednokrotne do urządzeń, aplikacji i usług z dowolnego miejsca za pośrednictwem tych urządzeń.

- Użytkownicy uzyskują dostęp do potrzebnych zasobów organizacji. 
- Pracownicy działu IT uzyskują kontrolki potrzebne do zabezpieczenia organizacji.

Zarządzanie tożsamościami urządzeń jest podstawą dla [dostępu warunkowego opartego na urządzeniach](../conditional-access/require-managed-devices.md). Za pomocą zasad dostępu warunkowego opartego na urządzeniach można zapewnić, że dostęp do zasobów w środowisku jest możliwy tylko przy użyciu zarządzanych urządzeń.

## <a name="getting-devices-in-azure-ad"></a>Pobieranie urządzeń w usłudze Azure AD

Aby uzyskać urządzenie w usłudze Azure AD, masz wiele opcji:

- **Zarejestrowane w usłudze Azure AD**
   - Urządzenia, które są zarejestrowane w usłudze Azure AD, są zazwyczaj urządzeniami prywatnymi lub przenośnymi, a są zalogowani przy użyciu osobistego konto Microsoft lub innego konta lokalnego.
      - Windows 10
      - iOS
      - Android
      - MacOS
- **Dołączone do usługi Azure AD**
   - Urządzenia, które są przyłączone do usługi Azure AD, należą do organizacji i są zalogowane przy użyciu konta usługi Azure AD należącego do tej organizacji. Istnieją one tylko w chmurze.
      - Windows 10 
      - [System Windows Server 2019 Virtual Machines uruchomiony na platformie Azure](howto-vm-sign-in-azure-ad-windows.md) (Server Core nie jest obsługiwany)
- **hybrydowym dołączonym do usługi Azure AD**.
   - Urządzenia, które są dołączone do hybrydowej usługi Azure AD, są własnością organizacji i są zalogowane przy użyciu konta Active Directory Domain Services należącego do tej organizacji. Istnieją one w chmurze i lokalnie.
      - Windows 7, 8,1 lub 10
      - System Windows Server 2008 lub nowszy

![Urządzenia wyświetlane w bloku urządzenia usługi Azure AD](./media/overview/azure-active-directory-devices-all-devices.png)

> [!NOTE]
> Stan hybrydowy odnosi się tylko do stanu urządzenia. Aby stan hybrydowy był prawidłowy, wymagany jest również prawidłowy użytkownik usługi Azure AD.

## <a name="device-management"></a>Zarządzanie urządzeniami

Urządzeniami w usłudze Azure AD można zarządzać przy użyciu narzędzi do zarządzania urządzeniami przenośnymi (MDM), takich jak Microsoft Intune, Microsoft Endpoint Configuration Manager, zasady grupy (dołączanie hybrydowej usługi Azure AD), narzędzia do zarządzania aplikacjami mobilnymi (MAM) lub innych narzędzi innych firm.

## <a name="resource-access"></a>Dostęp do zasobów

Rejestrowanie i dołączanie urządzeń do usługi Azure AD zapewnia użytkownikom bezproblemowe logowanie do zasobów w chmurze. Ten proces umożliwia także administratorom stosowanie zasad dostępu warunkowego do zasobów w oparciu o urządzenie, z którego są uzyskiwane dostęp. 

> [!NOTE]
> Zasady dostępu warunkowego opartego na urządzeniach wymagają urządzeń przyłączonych do hybrydowej usługi Azure AD lub zgodnych urządzeń z usługą Azure AD, które są zarejestrowane.

Podstawowy token odświeżania (PRT) zawiera informacje o urządzeniu i jest wymagany w przypadku logowania jednokrotnego. Jeśli masz ustawione zasady dostępu warunkowego opartego na urządzeniach dla aplikacji, bez PRT, odmowa dostępu. Hybrydowe zasady dostępu warunkowego wymagają hybrydowego urządzenia stanu i prawidłowego zalogowanego użytkownika.

Urządzenia, które są przyłączone do usługi Azure AD lub korzystają z hybrydowej usługi Azure AD z logowaniem jednokrotnym do zasobów lokalnych organizacji, a także zasobów w chmurze. Więcej informacji można znaleźć w artykule, w [jaki sposób logowanie JEDNOkrotne do zasobów lokalnych działa na urządzeniach przyłączonych do usługi Azure AD](azuread-join-sso.md).

## <a name="device-security"></a>Zabezpieczenia urządzeń

- **Zarejestrowane urządzenia usługi Azure AD** wykorzystują konto zarządzane przez użytkownika końcowego, to konto jest konto Microsoft lub innym lokalnym zarządzanym poświadczeniami zabezpieczonymi przy użyciu co najmniej jednej z poniższych opcji.
   - Hasło
   - Kod PIN
   - Wzorce
   - Windows Hello
- **Przyłączone do usługi Azure AD lub hybrydowe urządzenia z usługą Azure AD** wykorzystują konto organizacyjne w usłudze Azure AD zabezpieczone przy użyciu co najmniej jednego z następujących elementów.
   - Hasło
   - Windows Hello for Business

## <a name="provisioning"></a>Inicjowanie obsługi

Uzyskiwanie urządzeń w usłudze Azure AD może odbywać się w sposób samoobsługowy lub kontrolowany proces aprowizacji przez administratorów.

## <a name="summary"></a>Podsumowanie

Za pomocą zarządzania tożsamościami urządzeń w usłudze Azure AD można:

- Uproszczenie procesu przenoszenia urządzeń i zarządzania nimi w usłudze Azure AD
- Zapewnianie użytkownikom łatwego dostępu do zasobów opartych na chmurze w organizacji

## <a name="license-requirements"></a>Wymagania licencyjne

[!INCLUDE [Active Directory P1 license](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [zarejestrowanych urządzeniach usługi Azure AD](concept-azure-ad-register.md)
- Dowiedz się więcej o [urządzeniach przyłączonych do usługi Azure AD](concept-azure-ad-join.md)
- Dowiedz się więcej o [urządzeniach przyłączonych do hybrydowej usługi Azure AD](concept-azure-ad-join-hybrid.md)
- Aby dowiedzieć się, jak zarządzać tożsamościami urządzeń w Azure Portal, zobacz [Zarządzanie tożsamościami urządzeń przy użyciu Azure Portal](device-management-azure-portal.md).
- Aby dowiedzieć się więcej o dostępie warunkowym opartym na urządzeniach, zobacz [Konfigurowanie zasad dostępu warunkowego opartego na urządzeniach Azure Active Directory](../conditional-access/require-managed-devices.md).
