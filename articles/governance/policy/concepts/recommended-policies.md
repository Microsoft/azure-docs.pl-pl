---
title: Zalecane zasady dla usług platformy Azure
description: Opisuje sposób znajdowania i stosowania zalecanych zasad dla usług platformy Azure, takich jak Azure Virtual Machines.
ms.date: 03/31/2021
ms.topic: conceptual
ms.customer: generated
ms.openlocfilehash: ef028dc61b10117509fdd27e815230ff27dbadfa
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106090005"
---
# <a name="recommended-policies-for-azure-services"></a>Zalecane zasady dla usług platformy Azure

Klienci, którzy są nowym programem do Azure Policy często szukają typowych definicji zasad, aby zarządzać swoimi zasobami i podlegać ich. **Zalecane zasady** Azure Policy zawierają skoncentrowaną listę wspólnych definicji zasad, które mają zostać uruchomione. **Zalecane zasady** dotyczące obsługiwanych zasobów są osadzone w ramach środowiska portalu dla tego zasobu.

Aby uzyskać dodatkowe Azure Policy wbudowane, zobacz [Azure Policy wbudowane definicje](../samples/built-in-policies.md).

## <a name="azure-virtual-machines"></a>Azure Virtual Machines

**Zalecane zasady** dotyczące [usługi Azure Virtual Machines](../../../virtual-machines/index.yml) znajdują się na stronie **Przegląd** dla maszyn wirtualnych i na karcie **możliwości** . Na karcie _Azure Policy_ wybierz tekst "nieskonfigurowane" lub "# przypisany", aby otworzyć okienko boczne z zalecanymi zasadami. Każda definicja zasad już przypisana do zakresu, do którego należy maszyna wirtualna, jest wyszarzona. Wybierz zalecane zasady, które mają zostać zastosowane do tej maszyny wirtualnej, a następnie wybierz pozycję **Przypisz zasady** , aby utworzyć przypisanie dla każdego z nich.

Gdy organizacja osiągnie okres zapadalności [i organizuje zasoby i hierarchię zasobów](/azure/cloud-adoption-framework/ready/azure-best-practices/organize-subscriptions), zaleca się przeniesienie tych przypisań zasad z jednego na zasób na poziom subskrypcji lub [grupy zarządzania](../../management-groups/index.yml) .

### <a name="azure-virtual-machines-recommended-policies"></a>Zalecane zasady dotyczące platformy Azure Virtual Machines

|Nazwa<br /><sub>(Azure Portal)</sub> |Opis |Efekt (s) |Wersja<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Inspekcja maszyn wirtualnych bez skonfigurowanego odzyskiwania po awarii](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0015ea4d-51ff-4ce3-8d8c-f3f8f0179a56) |Inspekcja maszyn wirtualnych, dla których nie skonfigurowano odzyskiwania po awarii. Aby dowiedzieć się więcej na temat odzyskiwania po awarii, odwiedź stronę [https://aka.ms/asr-doc](../../../site-recovery/index.yml) . |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/RecoveryServices_DisasterRecovery_Audit.json) |
|[Inspekcja maszyn wirtualnych, które nie korzystają z dysków zarządzanych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F06a78e20-9358-41c9-923c-fb736d382a4d) |Ta zasada przeprowadza inspekcję maszyn wirtualnych, które nie korzystają z dysków zarządzanych |wizyjn |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VMRequireManagedDisk_Audit.json) |
|[Azure Backup powinna być włączona dla Virtual Machines](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Aby zapewnić ochronę Virtual Machines platformy Azure, należy włączyć Azure Backup. Azure Backup to bezpieczne i ekonomiczne rozwiązanie do ochrony danych dla systemu Azure. |AuditIfNotExists, wyłączone |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z przykładami w [Azure Policy Samples](../samples/index.md).
- Przejrzyj [wyjaśnienie działania zasad](./effects.md).
- Dowiedz się, jak [skorygować niezgodne zasoby](../how-to/remediate-resources.md).