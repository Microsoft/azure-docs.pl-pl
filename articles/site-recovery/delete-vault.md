---
title: Usuwanie magazynu Azure Site Recovery
description: Dowiedz się, jak usunąć magazyn Recovery Services skonfigurowany dla Azure Site Recovery
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/05/2019
ms.author: sideeksh
ms.openlocfilehash: a33e04a24013d5450c98b91048fa418958d16886
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89426388"
---
# <a name="delete-a-site-recovery-services-vault"></a>Usuwanie magazynu usług Site Recovery

W tym artykule opisano sposób usuwania Recovery Services magazynu dla Site Recovery. Aby usunąć magazyn używany w Azure Backup, zobacz [Usuwanie magazynu kopii zapasowych na platformie Azure](../backup/backup-azure-delete-vault.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


## <a name="before-you-start"></a>Przed rozpoczęciem

Aby można było usunąć magazyn, należy usunąć zarejestrowane serwery i elementy w magazynie. Co należy usunąć zależy od wdrożonych scenariuszy replikacji. 


## <a name="delete-a-vault-azure-vm-to-azure"></a>Usuwanie magazynu — maszyna wirtualna platformy Azure na platformę Azure

1. Postępuj zgodnie z [tymi instrukcjami](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-azure-vm-azure-to-azure) , aby usunąć wszystkie chronione maszyny wirtualne.
2. Następnie usuń magazyn.

## <a name="delete-a-vault-vmware-vm-to-azure"></a>Usuwanie magazynu z maszyną wirtualną VMware do platformy Azure

1. Postępuj zgodnie z [tymi instrukcjami](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) , aby usunąć wszystkie chronione maszyny wirtualne.
2. Wykonaj następujące [kroki](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) , aby usunąć wszystkie zasady replikacji.
3. Usuń odwołania do programu vCenter, wykonując [następujące kroki](vmware-azure-manage-vcenter.md#delete-a-vcenter-server).
4. Postępuj zgodnie z [tymi instrukcjami](vmware-azure-manage-configuration-server.md#delete-or-unregister-a-configuration-server) , aby zlikwidować serwer konfiguracji.
5. Następnie usuń magazyn.


## <a name="delete-a-vault-hyper-v-vm-with-vmm-to-azure"></a>Usuwanie magazynu — maszyny wirtualnej funkcji Hyper-V (z programem VMM) do platformy Azure

1. Wykonaj następujące [kroki](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario) , aby usunąć maszyny wirtualne funkcji Hyper-V zarządzane przez program System Center VMM.
2. Usuń skojarzenie i usunąć wszystkie zasady replikacji. Zrób to w magazynie > **Site Recovery infrastruktura**  >  **dla zasad replikacji programu System Center VMM**  >  **Replication Policies**.
3. Wykonaj następujące [kroki](site-recovery-manage-registration-and-protection.md#unregister-a-vmm-server) , aby wyrejestrować połączony serwer programu VMM.
4. Następnie usuń magazyn.

## <a name="delete-a-vault-hyper-v-vm-to-azure"></a>Usuwanie magazynu z maszyną wirtualną funkcji Hyper-V do platformy Azure

1. Wykonaj następujące [kroki](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-hyper-v-to-azure) , aby usunąć wszystkie chronione maszyny wirtualne.
2. Usuń skojarzenie i usunąć wszystkie zasady replikacji. Zrób to w magazynie > **Site Recovery infrastrukturze**  >  **dla zasad replikacji lokacji funkcji Hyper-V**  >  **Replication Policies**.
3. Postępuj zgodnie z [tymi instrukcjami](site-recovery-manage-registration-and-protection.md#unregister-a-hyper-v-host-in-a-hyper-v-site) , aby wyrejestrować hosta funkcji Hyper-V.
4. Usuń lokację funkcji Hyper-V.
5. Następnie usuń magazyn.


## <a name="use-powershell-to-force-delete-the-vault"></a>Wymuś usunięcie magazynu za pomocą programu PowerShell 

> [!Important]
> Jeśli testujesz produkt i nie obawiasz się o utracie danych, użyj metody Force Delete, aby szybko usunąć magazyn i wszystkie jego zależności.
> Polecenie programu PowerShell usuwa całą zawartość magazynu i **nie jest odwracalne**.

Aby usunąć magazyn Site Recovery, nawet jeśli są chronione elementy, użyj następujących poleceń:

```azurepowershell
Connect-AzAccount

Select-AzSubscription -SubscriptionName "XXXXX"

$vault = Get-AzRecoveryServicesVault -Name "vaultname"

Remove-AzRecoveryServicesVault -Vault $vault
```

Dowiedz się więcej na temat [Get-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/get-azrecoveryservicesvault)i [Remove-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/remove-azrecoveryservicesvault).
