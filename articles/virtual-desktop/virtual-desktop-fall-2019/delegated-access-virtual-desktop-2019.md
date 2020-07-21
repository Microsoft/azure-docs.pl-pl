---
title: Delegowany dostęp w pulpicie wirtualnym systemu Windows 2019 — Azure
description: Jak delegować możliwości administracyjne w ramach wdrożenia pulpitu wirtualnego systemu Windows, łącznie z przykładami.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 91be20de49f549f3530e6c817906f1abf8f106b6
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86527561"
---
# <a name="delegated-access-in-windows-virtual-desktop-fall-2019"></a>Delegowany dostęp w klasycznym pulpicie systemu Windows pokrywa 2019

>[!IMPORTANT]
>Ta zawartość dotyczy wersji 2019, która nie Azure Resource Manager obsługuje obiektów pulpitu wirtualnego systemu Windows. Jeśli próbujesz zarządzać Azure Resource Manager obiektami pulpitu wirtualnego systemu Windows wprowadzonymi w ramach aktualizacji wiosną 2020, zobacz [ten artykuł](../delegated-access-virtual-desktop.md).

Pulpit wirtualny systemu Windows ma model dostępu delegowanego, który umożliwia zdefiniowanie dostępu określonego użytkownika przez przypisanie ich do roli. Przypisanie roli ma trzy składniki: podmiot zabezpieczeń, definicja roli i zakres. Model dostępu delegowanego pulpitu wirtualnego systemu Windows jest oparty na modelu RBAC platformy Azure. Aby dowiedzieć się więcej o konkretnych przypisaniach ról i ich składnikach, zobacz [Omówienie kontroli dostępu opartej na rolach na platformie Azure](../../role-based-access-control/built-in-roles.md).

Dostęp delegowany pulpitu wirtualnego systemu Windows obsługuje następujące wartości dla każdego elementu przypisania roli:

* Podmiot zabezpieczeń
    * Użytkownicy
    * Jednostki usług
* Definicja roli
    * Wbudowane role
* Zakres
    * Grupy dzierżaw
    * Dzierżawy
    * Pule hostów
    * Grupy aplikacji

## <a name="built-in-roles"></a>Wbudowane role

Delegowany dostęp w programie Virtual Desktop systemu Windows ma kilka wbudowanych definicji ról, które można przypisać do użytkowników i jednostek usługi.

* Właściciel pulpitu zdalnego może zarządzać wszystkimi, w tym dostępem do zasobów.
* Współautor usług pulpitu zdalnego może zarządzać wszystko, ale nie może uzyskać dostępu do zasobów.
* Czytnik usług pulpitu zdalnego może wyświetlać wszystko, ale nie może wprowadzać żadnych zmian.
* Operator RDS może wyświetlać działania diagnostyczne.

## <a name="powershell-cmdlets-for-role-assignments"></a>Polecenia cmdlet programu PowerShell dla przypisań ról

Poniższe polecenia cmdlet umożliwiają tworzenie, wyświetlanie i usuwanie przypisań ról:

* **Get-RdsRoleAssignment** wyświetla listę przypisań ról.
* **New-RdsRoleAssignment** tworzy nowe przypisanie roli.
* **Remove-RdsRoleAssignment** usuwa przypisania ról.

### <a name="accepted-parameters"></a>Akceptowane parametry

Można modyfikować podstawowe trzy polecenia cmdlet o następujących parametrach:

* **AadTenantId**: Określa identyfikator dzierżawy Azure Active Directory, z której jest członkiem jednostki usługi.
* **AppGroupName**: Nazwa grupy aplikacji Pulpit zdalny.
* **Diagnostyka**: wskazuje zakres diagnostyki. (Musi być sparowany z parametrami **infrastruktury** lub **dzierżawy** ).
* **HostPoolName**: Nazwa puli hostów pulpit zdalny.
* **Infrastruktura**: wskazuje zakres infrastruktury.
* **RoleDefinitionName**: Nazwa roli kontroli dostępu opartej na rolach usługi pulpitu zdalnego przypisanej do użytkownika, grupy lub aplikacji. (Na przykład Usługi pulpitu zdalnego Owner, Usługi pulpitu zdalnego Reader itd.)
* **ServerPrincipleName**: nazwa aplikacji Azure Active Directory.
* **SignInName**: adres e-mail użytkownika lub główna nazwa użytkownika.
* **Dzierżawca**: Nazwa dzierżawy pulpit zdalny.

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z bardziej kompletną listą poleceń cmdlet programu PowerShell, które mogą być używane przez poszczególne role, zobacz [informacje dotyczące programu PowerShell](/powershell/windows-virtual-desktop/overview).

Aby uzyskać wskazówki dotyczące sposobu konfigurowania środowiska pulpitu wirtualnego systemu Windows, zobacz [Środowisko pulpitu wirtualnego systemu Windows](environment-setup-2019.md).
