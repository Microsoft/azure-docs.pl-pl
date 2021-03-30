---
title: Delegowany dostęp w programie Virtual Desktop systemu Windows — Azure
description: Jak delegować możliwości administracyjne w ramach wdrożenia pulpitu wirtualnego systemu Windows, łącznie z przykładami.
author: Heidilohr
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f2aa2c74704cf89c082d2837b39e82902efa0a62
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88010059"
---
# <a name="delegated-access-in-windows-virtual-desktop"></a>Dostęp delegowany w usłudze Windows Virtual Desktop

>[!IMPORTANT]
>Ta zawartość dotyczy pulpitu wirtualnego systemu Windows z Azure Resource Manager obiektów pulpitu wirtualnego systemu Windows. Jeśli używasz pulpitu wirtualnego systemu Windows (klasycznego) bez Azure Resource Manager obiektów, zobacz [ten artykuł](./virtual-desktop-fall-2019/delegated-access-virtual-desktop-2019.md).

Pulpit wirtualny systemu Windows ma model dostępu delegowanego, który umożliwia zdefiniowanie dostępu określonego użytkownika przez przypisanie ich do roli. Przypisanie roli ma trzy składniki: podmiot zabezpieczeń, definicja roli i zakres. Model dostępu delegowanego pulpitu wirtualnego systemu Windows jest oparty na modelu RBAC platformy Azure. Aby dowiedzieć się więcej o konkretnych przypisaniach ról i ich składnikach, zobacz [Omówienie kontroli dostępu opartej na rolach na platformie Azure](../role-based-access-control/built-in-roles.md).

Dostęp delegowany pulpitu wirtualnego systemu Windows obsługuje następujące wartości dla każdego elementu przypisania roli:

* Podmiot zabezpieczeń
    * Użytkownicy
    * Grupy użytkowników
    * Jednostki usługi
* Definicja roli
    * Wbudowane role
    * Role niestandardowe
* Zakres
    * Pule hostów
    * Grupy aplikacji
    * Obszary robocze

## <a name="powershell-cmdlets-for-role-assignments"></a>Polecenia cmdlet programu PowerShell dla przypisań ról

Przed rozpoczęciem upewnij się, że postępuj zgodnie z instrukcjami w temacie [Konfigurowanie modułu programu PowerShell](powershell-module.md) w celu skonfigurowania modułu programu PowerShell dla pulpitu wirtualnego systemu Windows, jeśli jeszcze tego nie zrobiono.

Pulpit wirtualny systemu Windows korzysta z kontroli dostępu opartej na rolach (Azure RBAC) podczas publikowania grup aplikacji dla użytkowników lub grup użytkowników. Rola użytkownika wirtualizacji pulpitu jest przypisana do grupy użytkowników lub użytkowników, a zakres jest grupą aplikacji. Ta rola daje użytkownikowi specjalny dostęp do danych w grupie aplikacji.

Uruchom następujące polecenie cmdlet, aby dodać Azure Active Directory użytkowników do grupy aplikacji:

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

Uruchom następujące polecenie cmdlet, aby dodać Azure Active Directory grupę użytkowników do grupy aplikacji:

```powershell
New-AzRoleAssignment -ObjectId <usergroupobjectid> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z bardziej kompletną listą poleceń cmdlet programu PowerShell, które mogą być używane przez poszczególne role, zobacz [informacje dotyczące programu PowerShell](/powershell/windows-virtual-desktop/overview).

Pełną listę ról obsługiwanych przez usługę Azure RBAC można znaleźć w temacie [role wbudowane platformy Azure](../role-based-access-control/built-in-roles.md).

Aby uzyskać wskazówki dotyczące sposobu konfigurowania środowiska pulpitu wirtualnego systemu Windows, zobacz [Środowisko pulpitu wirtualnego systemu Windows](environment-setup.md).
