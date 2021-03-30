---
title: Co to jest urządzenie dołączone do hybrydowej usługi Azure AD?
description: Dowiedz się, jak zarządzanie tożsamościami urządzeń może ułatwić zarządzanie urządzeniami, które uzyskują dostęp do zasobów w danym środowisku.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 259a1324c412dad40d32a8b8e026d84e6f5aa066
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85554914"
---
# <a name="hybrid-azure-ad-joined-devices"></a>Urządzenia dołączone hybrydowo do usługi Azure AD

Przez ponad dekadę wiele organizacji używało przyłączania do domeny lokalnej usługi Active Directory, aby umożliwić wykonywanie następujących czynności:

- Zarządzanie urządzeniami firmowymi z centralnej lokalizacji przez działy IT.
- Logowanie się przez użytkowników na urządzeniach przy użyciu kont służbowych usługi Active Directory.

Zazwyczaj Organizacje korzystające z zasobów lokalnych korzystają z metod tworzenia obrazu w celu aprowizacji urządzeń i często używają **Configuration Manager** lub **zasad grupy (GP)** do zarządzania nimi.

Jeśli środowisko ma lokalną infrastrukturę usługi AD i chcesz również wykorzystać możliwości zapewniane przez usługę Azure Active Directory, możesz wdrożyć urządzenia dołączone hybrydowo do usługi Azure AD. Urządzenia te są przyłączone do lokalnego Active Directory i rejestrowane przy użyciu Azure Active Directory.

| Sprzężenie hybrydowe usługi Azure AD | Opis |
| --- | --- |
| **Definicja** | Przyłączone do lokalnej usługi AD i usługi Azure AD wymagające konta organizacyjnego do logowania się na urządzeniu |
| **Odbiorcy podstawowe** | Odpowiednie dla organizacji hybrydowych z istniejącą lokalną infrastrukturą usługi AD |
|   | Dotyczy wszystkich użytkowników w organizacji |
| **Własność urządzeń** | Organizacja |
| **Systemy operacyjne** | Windows 10, 8,1 i 7 |
|   | Windows Server 2008/R2, 2012/R2, 2016 i 2019 |
| **Inicjowanie obsługi** | Windows 10, Windows Server 2016/2019 |
|   | Przyłączanie do domeny i autosprzęganie za pośrednictwem Azure AD Connect lub z konfiguracji ADFS |
|   | Przyłączanie do domeny za pomocą funkcji autopilotażu systemu Windows i autosprzęganie za pośrednictwem Azure AD Connect lub z |
|   | Windows 8.1, Windows 7, Windows Server 2012 R2, Windows Server 2012 i Windows Server 2008 R2-Wymagaj MSI |
| **Opcje logowania urządzenia** | Konta organizacyjne przy użyciu: |
|   | Hasło |
|   | Funkcja Windows Hello dla firm dla usługi Win10 |
| **Zarządzanie urządzeniami** | Zasady grupy |
|   | Configuration Manager autonomiczną lub współzarządzaną z Microsoft Intune |
| **Najważniejsze możliwości** | Logowanie jednokrotne do zasobów w chmurze i lokalnych |
|   | Dostęp warunkowy przez przyłączenie do domeny lub przez usługę Intune, jeśli współzarządzana |
|   | Samoobsługowe resetowanie haseł i funkcja resetowania numeru PIN funkcji Windows Hello na ekranie blokady |
|   | Enterprise State Roaming między urządzeniami |

![Urządzenia dołączone hybrydowo do usługi Azure AD](./media/concept-azure-ad-join-hybrid/azure-ad-hybrid-joined-device.png)

## <a name="scenarios"></a>Scenariusze

Użyj hybrydowych urządzeń usługi Azure AD, jeśli:

- Masz aplikacje Win32 wdrożone na tych urządzeniach, które polegają na uwierzytelnianiu maszynowym usługi Active Directory.
- Chcesz nadal używać zasady grupy do zarządzania konfiguracją urządzeń.
- Chcesz nadal używać istniejących rozwiązań do tworzenia obrazu do wdrażania i konfigurowania urządzeń.
- Oprócz systemu Windows 10 należy obsługiwać urządzenia z systemem Windows 7 i 8,1 na poziomie niższego poziomu.

## <a name="next-steps"></a>Następne kroki

- [Planowanie implementacji dołączania do hybrydowej usługi Azure AD](hybrid-azuread-join-plan.md)
- [Zarządzanie tożsamościami urządzeń przy użyciu witryny Azure Portal](device-management-azure-portal.md)
- [Zarządzanie nieaktywnymi urządzeniami w usłudze Azure AD](manage-stale-devices.md)
