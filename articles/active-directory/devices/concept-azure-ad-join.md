---
title: Co to jest urządzenie przyłączone do usługi Azure AD?
description: Informacje o urządzeniach przyłączonych do usługi Azure AD oraz sposobie zarządzania tożsamościami urządzeń ułatwiają zarządzanie urządzeniami, które uzyskują dostęp do zasobów w danym środowisku.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 07/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2b7cc0535549f2dbcdef4ab043ee506527fdbc5f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90601456"
---
# <a name="azure-ad-joined-devices"></a>Urządzenia dołączone do usługi Azure AD

Usługa Azure AD Join jest przeznaczona dla organizacji, które chcą być w chmurze lub tylko w chmurze. Każda organizacja może wdrożyć urządzenia przyłączone do usługi Azure AD niezależnie od wielkości lub branży. Funkcja Azure AD Join działa nawet w środowisku hybrydowym, umożliwiając dostęp do aplikacji i zasobów zarówno w chmurze, jak i lokalnych.

| Sprzężenia Azure AD | Opis |
| --- | --- |
| **Definicja** | Przyłączone do usługi Azure AD wymagające konta organizacyjnego do logowania się na urządzeniu |
| **Odbiorcy podstawowe** | Odpowiednie dla organizacji tylko w chmurze i hybrydowych. |
|   | Dotyczy wszystkich użytkowników w organizacji |
| **Własność urządzeń** | Organizacja |
| **Systemy operacyjne** | Wszystkie urządzenia z systemem Windows 10 z wyjątkiem systemu Windows 10 Home |
|   | [System Windows Server 2019 Virtual Machines uruchomiony na platformie Azure](howto-vm-sign-in-azure-ad-windows.md) (Server Core nie jest obsługiwany) |
| **Inicjowanie obsługi** | Samoobsługowe: OOBE lub ustawienia systemu Windows |
|   | Rejestrowanie zbiorcze |
|   | Windows Autopilot |
| **Opcje logowania urządzenia** | Konta organizacyjne przy użyciu: |
|   | Hasło |
|   | Windows Hello for Business |
|   | Klucze zabezpieczeń FIDO 2.0 (wersja zapoznawcza) |
| **Zarządzanie urządzeniami** | Zarządzanie urządzeniami przenośnymi (przykład: Microsoft Intune) |
|   | Współzarządzanie z Microsoft Intune i Microsoft Endpoint Configuration Manager |
| **Najważniejsze możliwości** | Logowanie jednokrotne do zasobów w chmurze i lokalnych |
|   | Dostęp warunkowy za poorednictwem rejestracji MDM i oceny zgodności z urządzeniami przenośnymi |
|   | Samoobsługowe resetowanie haseł i funkcja resetowania numeru PIN funkcji Windows Hello na ekranie blokady |
|   | Enterprise State Roaming między urządzeniami |

Urządzenia przyłączone do usługi Azure AD są zalogowane do korzystania z konta organizacji usługi Azure AD. Dostęp do zasobów w organizacji może być dodatkowo ograniczony w zależności od tego, czy konto usługi Azure AD i [zasady dostępu warunkowego](../conditional-access/howto-conditional-access-policy-compliant-device.md) są stosowane do tożsamości urządzenia.

Administratorzy mogą bezpiecznie i bardziej kontrolować urządzenia przyłączone do usługi Azure AD za pomocą narzędzi do zarządzania urządzeniami przenośnymi (MDM), takich jak Microsoft Intune lub w scenariuszach współzarządzania przy użyciu usługi Microsoft Endpoint Configuration Manager. Dzięki tym narzędziom można wymusić konfiguracje wymagane przez organizację, takie jak wymaganie zaszyfrowania magazynu, złożoność haseł, instalacje oprogramowania i aktualizacje oprogramowania. Administratorzy mogą udostępniać aplikacje organizacji dla urządzeń przyłączonych do usługi Azure AD przy użyciu Configuration Manager do [zarządzania aplikacjami z Microsoft Store dla firm i edukacji](/configmgr/apps/deploy-use/manage-apps-from-the-windows-store-for-business).

Funkcję Azure AD Join można wykonać przy użyciu opcji samoobsługowych, takich jak środowisko OOBE (out of Box Experience), rejestracja zbiorcza lub automatyczna [pilotaż systemu Windows](/intune/enrollment-autopilot).

Urządzenia przyłączone do usługi Azure AD nadal mogą obsługiwać Logowanie jednokrotne do zasobów lokalnych, gdy znajdują się one w sieci organizacji. Urządzenia, które są przyłączone do usługi Azure AD, mogą nadal być uwierzytelniane na serwerach lokalnych, takich jak pliki, drukowanie i inne aplikacje.

## <a name="scenarios"></a>Scenariusze

Chociaż dołączanie do usługi Azure AD jest przeznaczone głównie dla organizacji, które nie mają lokalnej infrastruktury usługi Active Directory systemu Windows Server, możesz bez obaw użyć tej funkcji w następujących scenariuszach:

- Chcesz przenieść zasoby do infrastruktury opartej na chmurze przy użyciu usługi Azure AD oraz funkcji zarządzania urządzeniami przenośnymi, np. usługi Intune.
- Nie możesz zastosować dołączania do domeny lokalnej, np. jeśli potrzebujesz kontrolować urządzenia przenośne, takie jak tablety i telefony.
- Użytkownicy muszą głównie uzyskać dostęp do Microsoft 365 lub innych aplikacji SaaS zintegrowanych z usługą Azure AD.
- Chcesz zarządzać grupą użytkowników w usłudze Azure AD zamiast w usłudze Active Directory. Ten scenariusz może dotyczyć na przykład pracowników sezonowych, wykonawców lub studentów.
- Chcesz zapewnić możliwość dołączania pracownikom w odległych oddziałach z ograniczoną infrastrukturą lokalną.

Urządzenia przyłączone do usługi Azure AD można skonfigurować dla wszystkich urządzeń z systemem Windows 10 z wyjątkiem systemu Windows 10 Home.

Celem urządzeń dołączonych do usługi Azure AD jest uproszczenie następujących kwestii:

- Wdrożenia urządzeń należących do firmy w systemie Windows
- Uzyskiwanie dostępu do aplikacji i zasobów organizacji z dowolnego urządzenia z systemem Windows
- Oparte na chmurze zarządzanie urządzeniami należącymi do firmy
- Użytkownicy mogą logować się na swoich urządzeniach przy użyciu usługi Azure AD lub zsynchronizowane Active Directory kontami służbowymi.

![Urządzenia dołączone do usługi Azure AD](./media/concept-azure-ad-join/azure-ad-joined-device.png)

Dołączenie do usługi Azure AD można wdrożyć przy użyciu dowolnej z następujących metod:

- [Windows Autopilot](/windows/deployment/windows-autopilot/windows-10-autopilot)
- [Wdrożenie zbiorcze](/intune/windows-bulk-enroll)
- [Środowisko samoobsługowe](azuread-joined-devices-frx.md)

## <a name="next-steps"></a>Następne kroki

- [Planowanie implementacji dołączania do usługi Azure AD](azureadjoin-plan.md)
- [Jak zarządzać lokalną grupą administratorów na urządzeniach dołączonych do usługi Azure AD](assign-local-admin.md)
- [Zarządzanie tożsamościami urządzeń przy użyciu witryny Azure Portal](device-management-azure-portal.md)
- [Zarządzanie nieaktywnymi urządzeniami w usłudze Azure AD](manage-stale-devices.md)
