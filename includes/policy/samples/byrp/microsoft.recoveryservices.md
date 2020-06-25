---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 06/23/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 8a8527e2916baa1619e4649a3273d42169c8fcbb
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/24/2020
ms.locfileid: "85322760"
---
|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Azure Backup powinna być włączona dla Virtual Machines](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Te zasady ułatwiają inspekcję, jeśli usługa Azure Backup jest włączona dla wszystkich maszyn wirtualnych. Azure Backup to ekonomiczne rozwiązanie do tworzenia kopii zapasowych, które upraszcza odzyskiwanie danych i jest łatwiejsze do włączenia niż inne usługi kopii zapasowych w chmurze. |AuditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |
|[Konfigurowanie kopii zapasowych na maszynach wirtualnych lokalizacji do istniejącego magazynu centralnego w tej samej lokalizacji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09ce66bc-1220-4153-8104-e3f51c936913) |Te zasady umożliwiają skonfigurowanie ochrony Azure Backup na maszynach wirtualnych w danej lokalizacji do istniejącego magazynu centralnego w tej samej lokalizacji. Dotyczy tylko tych maszyn wirtualnych, które nie zostały jeszcze skonfigurowane do tworzenia kopii zapasowych. Zaleca się, aby te zasady zostały przypisane do nie więcej niż 200 maszyn wirtualnych. Jeśli zasady są przypisane ponad 200 maszyn wirtualnych, może to spowodować, że wykonywanie kopii zapasowej zostanie wyzwolone kilka godzin poza zdefiniowanym harmonogramem. Te zasady zostaną rozszerzone w celu obsługi większej liczby obrazów maszyn wirtualnych. |deployIfNotExists, auditIfNotExists, wyłączone |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachineBackup_Backup_DeployIfNotExists.json) |
|[Wdróż ustawienia diagnostyczne magazynu Recovery Services do Log Analytics obszaru roboczego dla kategorii określonych zasobów.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc717fb0c-d118-4c43-ab3d-ece30ac81fb3) |Wdróż ustawienia diagnostyczne magazynu Recovery Services do przesyłania strumieniowego do Log Analytics obszaru roboczego dla kategorii określonych zasobów. Jeśli dowolna z kategorii określonych zasobów nie jest włączona, zostanie utworzone nowe ustawienie diagnostyczne. |deployIfNotExists |[1.0.0 — wersja zapoznawcza](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/EnableRecoveryServiceVaultDiagnosticSetting_Backup_DeployIfNotExist.json) |
