---
title: Dokumentacja poleceń cmdlet programu PowerShell
description: Informacje o poleceniach cmdlet programu PowerShell dla usługi Azure Scheduler
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: ccc9f709348d961e49bced00946658a6997837c0
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2020
ms.locfileid: "92368115"
---
# <a name="powershell-cmdlets-reference-for-azure-scheduler"></a>Dokumentacja poleceń cmdlet programu PowerShell dla usługi Azure Scheduler

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) zastępuje usługę Azure Scheduler, która jest [wycofywana](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date). Aby kontynuować pracę z zadaniami skonfigurowanymi w usłudze Scheduler, [Przeprowadź migrację do Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md) najszybciej, jak to możliwe. 
>
> Usługa Scheduler nie jest już dostępna w Azure Portal, ale polecenia cmdlet programu PowerShell dla [interfejsu API REST](/rest/api/scheduler) i [usługi Azure Scheduler](scheduler-powershell-reference.md) pozostają dostępne w tym momencie, aby można było zarządzać zadaniami i kolekcjami zadań.

Aby tworzyć skrypty do tworzenia zadań harmonogramu i kolekcji zadań oraz zarządzania nimi, można użyć poleceń cmdlet programu PowerShell. Ten artykuł zawiera listę głównych poleceń cmdlet programu PowerShell dla usługi Azure Scheduler z linkami do artykułów referencyjnych. Aby zainstalować Azure PowerShell dla subskrypcji platformy Azure, zobacz [jak zainstalować i skonfigurować Azure PowerShell](/powershell/azure/). Więcej informacji o [poleceniach cmdlet Azure Resource Manager](/powershell/azure/)można znaleźć w temacie [Using Azure PowerShell with Azure Resource Manager](../azure-resource-manager/management/manage-resources-powershell.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

| Polecenie cmdlet | Opis |
|--------|-------------|
| [Disable-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/disable-azurermschedulerjobcollection) |Wyłącza kolekcję zadań. |
| [Enable-AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/enable-azurermschedulerjobcollection) |Włącza kolekcję zadań. |
| [Get-AzSchedulerJob](/powershell/module/azurerm.scheduler/get-azurermschedulerjob) |Pobiera zadania usługi Scheduler. |
| [Get-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/get-azurermschedulerjobcollection) |Pobiera kolekcje zadań. |
| [Get-AzSchedulerJobHistory](/powershell/module/azurerm.scheduler/get-azurermschedulerjobhistory) |Pobiera historię zadania. |
| [New-AzSchedulerHttpJob](/powershell/module/azurerm.scheduler/new-azurermschedulerhttpjob) |Tworzy zadanie HTTP. |
| [New-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/new-azurermschedulerjobcollection) |Tworzy kolekcję zadań. |
| [New-AzSchedulerServiceBusQueueJob](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebusqueuejob) | Tworzy zadanie kolejki Service Bus. |
| [New-AzSchedulerServiceBusTopicJob](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebustopicjob) |Tworzy zadanie tematu Service Bus. |
| [New-AzSchedulerStorageQueueJob](/powershell/module/azurerm.scheduler/new-azurermschedulerstoragequeuejob) |Tworzy zadanie kolejki magazynu. |
| [Remove-AzSchedulerJob](/powershell/module/azurerm.scheduler/remove-azurermschedulerjob) |Usuwa zadanie usługi Scheduler. |
| [Remove-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/remove-azurermschedulerjobcollection) |Usuwa kolekcję zadań. |
| [Set-AzSchedulerHttpJob](/powershell/module/azurerm.scheduler/set-azurermschedulerhttpjob) |Modyfikuje zadanie HTTP usługi Scheduler. |
| [Set-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/set-azurermschedulerjobcollection) |Modyfikuje kolekcję zadań. |
| [Set-AzSchedulerServiceBusQueueJob](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebusqueuejob) |Modyfikuje zadanie Service Bus kolejki. |
| [Set-AzSchedulerServiceBusTopicJob](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebustopicjob) |Modyfikuje zadanie tematu Service Bus. |
| [Set-AzSchedulerStorageQueueJob](/powershell/module/azurerm.scheduler/set-azurermschedulerstoragequeuejob) |Modyfikuje zadanie kolejki magazynu. |
||| 

Aby uzyskać więcej informacji, możesz uruchomić dowolne z następujących poleceń cmdlet: 

```text
Get-Help <cmdlet name> -Detailed
Get-Help <cmdlet name> -Examples
Get-Help <cmdlet name> -Full
```

## <a name="next-steps"></a>Następne kroki

* [Pojęcia i terminologia dotyczące usługi Azure Scheduler oraz hierarchia jednostek](scheduler-concepts-terms.md)
* [Limity, wartości domyślne i kody błędów usługi Azure Scheduler](scheduler-limits-defaults-errors.md)
* [Dokumentacja interfejsu API REST usługi Azure Scheduler](/rest/api/scheduler)