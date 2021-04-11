---
title: Wymagania licencyjne dotyczące używania Privileged Identity Management-Azure Active Directory | Microsoft Docs
description: Opisuje wymagania licencyjne dotyczące korzystania z Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: markwahl-msft
ms.assetid: 34367721-8b42-4fab-a443-a2e55cdbf33d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: pim
ms.date: 04/05/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d0428f73c4df3d97002def03823ff8cced1d647
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106552635"
---
# <a name="license-requirements-to-use-privileged-identity-management"></a>Wymagania licencyjne dotyczące używania Privileged Identity Management

Aby można było użyć usługi Azure Active Directory (Azure AD) Privileged Identity Management (PIM), katalog musi mieć ważną licencję. Ponadto licencje muszą być przypisane do administratorów i odpowiednich użytkowników. W tym artykule opisano wymagania licencyjne dotyczące korzystania z Privileged Identity Management.

## <a name="valid-licenses"></a>Prawidłowe licencje

Konieczne będzie [!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)] użycie PIM i wszystkich ustawień. Obecnie można ograniczyć przegląd dostępu do jednostek usługi przy użyciu dostępu do usługi Azure AD i ról zasobów platformy Azure (wersja zapoznawcza) z aktywną Azure Active Directory — wersja Premium P2 w dzierżawie. Model licencjonowania dla jednostek usługi zostanie sfinalizowany do ogólnej dostępności tej funkcji, a dodatkowe licencje mogą być wymagane.

## <a name="licenses-you-must-have"></a>Licencje, które musisz dysponować

Upewnij się, że katalog ma co najmniej tyle licencji na Azure AD — wersja Premium P2, ponieważ masz pracowników, którzy będą wykonywać następujące zadania:

- Użytkownicy przypisani jako uprawnieni do usługi Azure AD lub ról platformy Azure zarządzanych przy użyciu programu PIM
- Użytkownicy przypisani jako uprawnieni członkowie lub właściciele uprzywilejowanych grup dostępu
- Użytkownicy mogli zatwierdzać lub odrzucać żądania aktywacji w usłudze PIM
- Użytkownicy przypisani do przeglądu dostępu
- Użytkownicy, którzy wykonują przeglądy dostępu

Licencje na Azure AD — wersja Premium P2 **nie** są wymagane dla następujących zadań:

- Dla użytkowników konfigurujących usługę PIM nie są wymagane żadne licencje, konfiguruje zasady, odbierają alerty i konfiguruje przeglądy dostępu.

Aby uzyskać więcej informacji na temat licencji, zobacz [przypisywanie lub usuwanie licencji przy użyciu portalu Azure Active Directory](../fundamentals/license-users-groups.md).

## <a name="example-license-scenarios"></a>Przykładowe scenariusze licencji

Poniżej przedstawiono kilka przykładowych scenariuszy licencjonowania, które mogą pomóc w ustaleniu liczby posiadanych licencji.

| Scenariusz | Obliczenia | Liczba licencji |
| --- | --- | --- |
| Bank Woodgrove ma 10 administratorów dla różnych działów i 2 administratorów globalnych, którzy konfigurują PIM i zarządzają nimi. Mogą oni mieć pięciu administratorów. | Pięć licencji dla administratorów, którzy są uprawnieni | 5 |
| Instytut projektowania grafiki ma 25 administratorów, których 14 są zarządzane za poorednictwem programu PIM. Aktywacja roli wymaga zatwierdzenia, a w organizacji znajdują się trzy różne użytkowników, którzy mogą zatwierdzać aktywacje. | 14 licencji dla kwalifikujących się ról + trzy osoby zatwierdzające | 17 |
| Firma Contoso ma 50 administratorów, których 42 są zarządzane za poorednictwem programu PIM. Aktywacja roli wymaga zatwierdzenia, a w organizacji istnieje pięciu różnych użytkowników, którzy mogą zatwierdzać aktywacje. Firma Contoso wykonuje także comiesięczne przeglądy użytkowników przypisanych do ról administratorów i recenzentów. są to kierownicy użytkowników, których sześć nie znajduje się w rolach administratora zarządzanych przez program PIM. | 42 licencji dla kwalifikujących się ról + pięciu osób zatwierdzających i sześciu recenzentów | 53 |

## <a name="when-a-license-expires"></a>Po wygaśnięciu licencji

Jeśli licencja na Azure AD — wersja Premium P2, EMS E5 lub wersja próbna wygaśnie, funkcje Privileged Identity Management nie będą już dostępne w Twoim katalogu:

- Nie wpłynie to na trwałe przypisania roli do ról usługi Azure AD.
- Usługa Privileged Identity Management w Azure Portal, jak również polecenia cmdlet interfejs API programu Graph i interfejsy programu PowerShell dla programu Privileged Identity Management, nie będą już dostępne dla użytkowników w celu aktywowania ról uprzywilejowanych, zarządzania dostępem uprzywilejowanym lub przeprowadzania przeglądów dostępu do ról uprzywilejowanych.
- Uprawnione przypisania ról usługi Azure AD zostaną usunięte, ponieważ użytkownicy nie będą już mogli aktywować uprzywilejowanych ról.
- Wszystkie bieżące przeglądy dostępu ról usługi Azure AD zostaną zakończone, a ustawienia konfiguracji Privileged Identity Management zostaną usunięte.
- Privileged Identity Management nie będą już wysyłać wiadomości e-mail w ramach zmian przypisania roli.

## <a name="next-steps"></a>Następne kroki

- [Wdrażanie usługi Privileged Identity Management](pim-deployment-plan.md)
- [Rozpoczęcie korzystania z usługi Privileged Identity Management](pim-getting-started.md)
- [Role, którymi nie można zarządzać w Privileged Identity Management](pim-roles.md)
- [Tworzenie przeglądu dostępu do ról zasobów platformy Azure w usłudze PIM](pim-resource-roles-start-access-review.md)
- [Tworzenie przeglądu dostępu ról usługi Azure AD w usłudze PIM](pim-how-to-start-security-review.md)
