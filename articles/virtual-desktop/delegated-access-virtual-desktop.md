---
title: Dostęp delegowany na pulpicie wirtualnym systemu Windows — platforma Azure
description: Jak delegować możliwości administracyjne wdrożenia pulpitu wirtualnego systemu Windows, w tym przykłady.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 91451ff3024a9a5019b3982b0e4471e2c4d80c74
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683916"
---
# <a name="delegated-access-in-windows-virtual-desktop"></a>Dostęp delegowany w usłudze Windows Virtual Desktop

Pulpit wirtualny systemu Windows ma model dostępu delegowanego, który pozwala zdefiniować ilość dostępu, jaką może mieć określony użytkownik, przypisując mu rolę. Przypisanie roli ma trzy składniki: podmiot zabezpieczeń, definicja roli i zakres. Model dostępu delegowanego pulpitu wirtualnego systemu Windows jest oparty na modelu rbac platformy Azure. Aby dowiedzieć się więcej o określonych przydziałach ról i ich składnikach, zobacz [omówienie kontroli dostępu opartej na rolach platformy Azure.](../role-based-access-control/built-in-roles.md)

Dostęp delegowany pulpitu wirtualnego systemu Windows obsługuje następujące wartości dla każdego elementu przypisania roli:

* Podmiot zabezpieczeń
    * Użytkownicy
    * Jednostki usług
* Definicja roli
    * Wbudowane role
* Zakres
    * Grupy dzierżawców
    * Dzierżawy
    * Hosty
    * Grupy aplikacji

## <a name="built-in-roles"></a>Wbudowane role

Dostęp delegowany w programie Windows Virtual Desktop ma kilka wbudowanych definicji ról, które można przypisać do użytkowników i podmiotów korzystających z usług.

* Właściciel usług pulpitu zdalnego może zarządzać wszystkim, w tym dostępem do zasobów.
* Współautor usług pulpitu zdalnego może zarządzać wszystkim, ale nie może uzyskać dostępu do zasobów.
* Czytnik RDS może wyświetlać wszystko, ale nie może wprowadzać żadnych zmian.
* Operator RDS może wyświetlać działania diagnostyczne.

## <a name="powershell-cmdlets-for-role-assignments"></a>Polecenia cmdlet programu PowerShell dla przypisań ról

Aby utworzyć, wyświetlić i usunąć przypisania ról, można uruchamiać następujące polecenia cmdlet:

* **Znak Get-RdsRoleAssignment** wyświetla listę przypisań ról.
* **New-RdsRoleAssignment** tworzy nowe przypisanie roli.
* **Usuń-RdsRoleAssignment** usuwa przypisania ról.

### <a name="accepted-parameters"></a>Akceptowane parametry

Można zmodyfikować podstawowe trzy polecenia cmdlet z następującymi parametrami:

* **AadTenantId**: określa identyfikator dzierżawy usługi Azure Active Directory, z którego podmiotu zabezpieczeń usługi jest członkiem.
* **AppGroupName**: nazwa grupy aplikacji pulpitu zdalnego.
* **Diagnostyka**: wskazuje zakres diagnostyki. (Musi być sparowany z parametrami **infrastruktury** lub **dzierżawy).**
* **HostPoolName**: nazwa puli hostów usług pulpitu zdalnego.
* **Infrastruktura**: wskazuje zakres infrastruktury.
* **RoleDefinitionName**: nazwa roli kontroli dostępu opartej na usługach pulpitu zdalnego przypisanej do użytkownika, grupy lub aplikacji. (Na przykład właściciel usług pulpitu zdalnego, czytnik usług pulpitu zdalnego itd.)
* **ServerPrincipleName**: nazwa aplikacji usługi Azure Active Directory.
* **SignInName**: adres e-mail użytkownika lub nazwa głównego użytkownika.
* **TenantName**: nazwa dzierżawy pulpitu zdalnego.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać pełniejszą listę poleceń cmdlet programu PowerShell, których może używać każda rola, zobacz [odwołanie programu PowerShell](/powershell/windows-virtual-desktop/overview).

Aby uzyskać wskazówki dotyczące konfigurowania środowiska pulpitu wirtualnego systemu Windows, zobacz [Środowisko pulpitu wirtualnego systemu Windows](environment-setup.md).
