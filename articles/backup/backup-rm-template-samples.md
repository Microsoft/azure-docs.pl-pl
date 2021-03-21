---
title: Szablony usługi Azure Resource Manager
description: Azure Resource Manager szablonów do użycia z magazynami Recovery Services i funkcjami Azure Backup
ms.topic: sample
ms.date: 01/31/2019
ms.custom: mvc
ms.openlocfilehash: a4c2f444cb821f7979571b9d777895a59450e7c2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96309583"
---
# <a name="azure-resource-manager-templates-for-azure-backup"></a>Szablony usługi Azure Resource Manager dla usługi Azure Backup

Poniższa tabela zawiera linki do szablonów usługi Azure Resource Manager używanych dla magazynów usługi Recovery Services i funkcji usługi Azure Backup. Aby poznać składnię i właściwości języka JSON, zobacz [Typy zasobów Microsoft.RecoveryServices](/azure/templates/microsoft.recoveryservices/allversions).

| Template | Opis |
|---|---|
|**Magazyn usługi Recovery Services** | |
| [Tworzenie magazynu Recovery Services](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vault-create)| Utwórz magazyn usługi Recovery Services. Magazynu można użyć dla usług Azure Backup i Azure Site Recovery. |
|**Tworzenie kopii zapasowych maszyn wirtualnych**| |
| [Tworzenie kopii zapasowych maszyn wirtualnych usługi Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-backup-vms) | Za pomocą istniejącego magazynu usługi Recovery Services i zasad usługi Backup można tworzyć kopie zapasowe maszyn wirtualnych usługi Resource Manager w tej samej grupie zasobów.|
| [Tworzenie kopii zapasowych maszyn wirtualnych infrastruktury IaaS w magazynie usługi Recovery Services](https://github.com/Azure/azure-quickstart-templates/tree/master/201-recovery-services-backup-classic-resource-manager-vms) | Szablon tworzenia kopii zapasowej klasycznych maszyn wirtualnych i maszyn wirtualnych usługi Resource Manager. |
| [Tworzenie zasad cotygodniowego tworzenia kopii zapasowej dla maszyn wirtualnych infrastruktury IaaS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-weekly-backup-policy-create) | Szablon tworzy magazyn Recovery Services i cotygodniowe zasady tworzenia kopii zapasowych, które są używane do tworzenia kopii zapasowych maszyn wirtualnych klasycznych i Menedżer zasobówowych.|
| [Tworzenie zasad codziennego tworzenia kopii zapasowej dla maszyn wirtualnych infrastruktury IaaS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-daily-backup-policy-create) | Szablon tworzy magazyn Recovery Services i codzienne zasady tworzenia kopii zapasowych, które są używane do tworzenia kopii zapasowych maszyn wirtualnych klasycznych i Menedżer zasobówowych.|
| [Wdrażanie maszyny wirtualnej z systemem Windows Server i włączoną kopią zapasową](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-create-vm-and-configure-backup) | Szablon umożliwia utworzenie maszyny wirtualnej z systemem Windows Server i magazynu usługi Recovery Services z włączonymi domyślnymi zasadami kopii zapasowej.|
|**Monitoruj zadania tworzenia kopii zapasowej** |  |
| [Używanie dzienników usługi Azure Monitor za pomocą usługi Azure Backup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-backup-oms-monitoring) | Szablon służy do wdrażania dzienników Azure Monitor przy użyciu Azure Backup, co pozwala monitorować zadania tworzenia kopii zapasowej i przywracania, alerty kopii zapasowych oraz magazyn w chmurze używany w magazynach Recovery Services.|  
|**Tworzenie kopii zapasowej SQL Server na maszynie wirtualnej platformy Azure** |  |
| [Tworzenie kopii zapasowej SQL Server na maszynie wirtualnej platformy Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vm-workload-backup) | Szablon tworzy magazyn Recovery Services i zasady tworzenia kopii zapasowych specyficzne dla obciążenia. Rejestruje maszynę wirtualną za pomocą usługi Azure Backup i konfiguruje ochronę na tej maszynie wirtualnej. Obecnie działa tylko w przypadku obrazów galerii SQL. |
|**Tworzenie kopii zapasowej udziałów plików platformy Azure** |  |
| [Tworzenie kopii zapasowej udziałów plików platformy Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-backup-file-share) | Ten szablon służy do konfigurowania ochrony istniejącego udziału plików platformy Azure przez określenie odpowiednich szczegółów dotyczących magazynu Recovery Services i zasad tworzenia kopii zapasowych. Opcjonalnie tworzy nowy magazyn Recovery Services i zasady tworzenia kopii zapasowych oraz rejestruje konto magazynu zawierające udział plików w magazynie Recovery Services. |
|   |   |
