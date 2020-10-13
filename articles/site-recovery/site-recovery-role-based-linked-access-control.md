---
title: Zarządzanie kontrolą dostępu opartą na rolach na platformie Azure w Azure Site Recovery
description: W tym artykule opisano sposób stosowania kontroli dostępu opartej na rolach (RBAC) do zarządzania dostępem Azure Site Recovery.
ms.service: site-recovery
ms.date: 04/08/2019
author: mayurigupta13
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: 95ea8b61b01b17512d99d0316073835326e6d7bd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87927168"
---
# <a name="manage-site-recovery-access-with-role-based-access-control-rbac"></a>Zarządzanie dostępem Site Recovery przy użyciu kontroli dostępu opartej na rolach (RBAC)

Kontrola dostępu oparta na rolach (Azure RBAC) na platformie Azure umożliwia precyzyjne zarządzanie dostępem na platformie Azure. Za pomocą RBAC można rozdzielić obowiązki w zespole i udzielić użytkownikom tylko określonych uprawnień dostępu w celu wykonywania określonych zadań.

Azure Site Recovery udostępnia 3 wbudowane role do kontrolowania operacji zarządzania Site Recovery. Dowiedz się więcej o [rolach wbudowanych platformy Azure](../role-based-access-control/built-in-roles.md)

* [Współautor usługi Site Recovery](../role-based-access-control/built-in-roles.md#site-recovery-contributor) — ta rola ma wszystkie uprawnienia wymagane do zarządzania operacjami usługi Azure Site Recovery w magazynie usługi Recovery Services. Użytkownik z tą rolą nie może jednak tworzyć ani usuwać magazynu usługi Recovery Services, ani przypisywać praw dostępu innym użytkownikom. Ta rola jest najbardziej odpowiednia dla administratorów odzyskiwania po awarii, którzy mogą włączać i zarządzać odzyskiwaniem po awarii dla aplikacji lub całych organizacji, jak to możliwe.
* [Operator usługi Site Recovery](../role-based-access-control/built-in-roles.md#site-recovery-operator) — ta rola ma uprawnienia do uruchamiania operacji trybu failover i powrotu po awarii oraz zarządzania nimi. Użytkownik z tą rolą nie może włączać ani wyłączać replikacji, tworzyć ani usuwać magazynów, rejestrować nowej infrastruktury ani przypisywać praw dostępu innym użytkownikom. Ta rola jest najbardziej odpowiednia dla operatora odzyskiwania po awarii, który może przechodzić do trybu failover maszyn wirtualnych lub aplikacji, gdy są one przekazywane przez właścicieli aplikacji i administratorów IT w rzeczywistej lub symulowanej sytuacji awaryjnej, takiej jak odzyskiwanie po awarii. Po rozwiązaniu awarii operator DR może ponownie chronić maszyny wirtualne i przewracać je do końca.
* [Czytelnik usługi Site Recovery](../role-based-access-control/built-in-roles.md#site-recovery-reader) — ta rola ma uprawnienia do wyświetlania wszystkich operacji zarządzania usługą Site Recovery. Ta rola jest najbardziej odpowiednia dla kierownika monitorowania IT, który może monitorować bieżący stan ochrony i zgłaszać bilety pomocy technicznej, jeśli jest to wymagane.

Jeśli chcesz zdefiniować własne role, aby jeszcze bardziej kontrolować, zobacz jak [tworzyć role niestandardowe](../role-based-access-control/custom-roles.md) na platformie Azure.

## <a name="permissions-required-to-enable-replication-for-new-virtual-machines"></a>Uprawnienia wymagane do włączenia replikacji dla nowych maszyn wirtualnych
Gdy nowa maszyna wirtualna jest replikowana na platformę Azure przy użyciu Azure Site Recovery, poziomy dostępu skojarzonych użytkowników są weryfikowane w celu upewnienia się, że użytkownik ma wymagane uprawnienia do korzystania z zasobów platformy Azure udostępnionych do Site Recovery.

Aby włączyć replikację dla nowej maszyny wirtualnej, użytkownik musi mieć:
* Uprawnienie do tworzenia maszyny wirtualnej w wybranej grupie zasobów
* Uprawnienie do tworzenia maszyny wirtualnej w wybranej sieci wirtualnej
* Uprawnienie do zapisu na wybranym koncie magazynu

Użytkownik musi mieć następujące uprawnienia, aby ukończyć replikację nowej maszyny wirtualnej.

> [!IMPORTANT]
>Upewnij się, że odpowiednie uprawnienia są dodawane dla modelu wdrażania (Menedżer zasobów/klasyczny) używanego do wdrażania zasobów.

> [!NOTE]
> Jeśli włączasz replikację maszyny wirtualnej platformy Azure i chcesz zezwolić na Site Recovery zarządzania aktualizacjami, a następnie Włącz replikację, możesz również utworzyć nowe konto usługi Automation, w którym to przypadku trzeba mieć uprawnienia do tworzenia konta usługi Automation w tej samej subskrypcji co magazyn.

| **Typ zasobu** | **Model wdrażania** | **Uprawnienie** |
| --- | --- | --- |
| Compute | Resource Manager | Microsoft. COMPUTE/availabilitySets/Read |
|  |  | Microsoft. COMPUTE/virtualMachines/Read |
|  |  | Microsoft. COMPUTE/virtualMachines/Write |
|  |  | Microsoft. COMPUTE/virtualMachines/Delete |
|  | Klasyczny | Microsoft. ClassicCompute/domainNames/odczyt |
|  |  | Microsoft. ClassicCompute/domainNames/Write |
|  |  | Microsoft. ClassicCompute/domainNames/Delete |
|  |  | Microsoft. ClassicCompute/virtualMachines/odczyt |
|  |  | Microsoft. ClassicCompute/virtualMachines/Write |
|  |  | Microsoft. ClassicCompute/virtualMachines/Delete |
| Sieć | Resource Manager | Microsoft. Network/networkInterfaces/odczyt |
|  |  | Microsoft. Network/networkInterfaces/Write |
|  |  | Microsoft. Network/networkInterfaces/Delete |
|  |  | Microsoft. Network/networkInterfaces/Join/Action |
|  |  | Microsoft. Network/virtualNetworks/odczyt |
|  |  | Microsoft.Network/virtualNetworks/subnets/read |
|  |  | Microsoft.Network/virtualNetworks/subnets/join/action |
|  | Klasyczny | Microsoft. ClassicNetwork/virtualNetworks/odczyt |
|  |  | Microsoft. ClassicNetwork/virtualNetworks/Join/Action |
| Magazyn | Resource Manager | Microsoft. Storage/storageAccounts/Read |
|  |  | Microsoft. Storage/storageAccounts/ListKeys/akcja |
|  | Klasyczny | Microsoft. ClassicStorage/storageAccounts/odczyt |
|  |  | Microsoft. ClassicStorage/storageAccounts/listKeys/akcja |
| Grupa zasobów | Resource Manager | Microsoft. resources/Deployments/* |
|  |  | Microsoft. resources/subscriptions/resourceGroups/Read |

Rozważ użycie [wbudowanych ról](../role-based-access-control/built-in-roles.md) "Współautor maszyny wirtualnej" i "Współautor klasycznej maszyny wirtualnej" dla Menedżer zasobów i klasycznych modeli wdrażania.

## <a name="next-steps"></a>Następne kroki
* [Kontrola dostępu oparta na rolach (Azure RBAC)](../role-based-access-control/role-assignments-portal.md): wprowadzenie do kontroli rbac w Azure Portal.
* Dowiedz się, jak zarządzać dostępem przy użyciu:
  * [Program PowerShell](../role-based-access-control/role-assignments-powershell.md)
  * [Interfejs wiersza polecenia platformy Azure](../role-based-access-control/role-assignments-cli.md)
  * [Interfejs API REST](../role-based-access-control/role-assignments-rest.md)
* [Rozwiązywanie problemów Access Control opartych na rolach](../role-based-access-control/troubleshooting.md): Uzyskaj sugestie dotyczące rozwiązywania typowych problemów.
