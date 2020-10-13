---
title: Uaktualnianie konfiguracji klastra usługi Azure Service Fabric
description: Dowiedz się, jak uaktualnić konfigurację, w której działa klaster Service Fabric na platformie Azure przy użyciu szablonu Menedżer zasobów.
ms.topic: conceptual
ms.date: 11/09/2018
ms.openlocfilehash: 692dc2162159ab61a3ac527e12dac43438084a60
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91842720"
---
# <a name="upgrade-the-configuration-of-a-cluster-in-azure"></a>Uaktualnianie konfiguracji klastra na platformie Azure 

W tym artykule opisano sposób dostosowywania różnych ustawień sieci szkieletowej dla klastra Service Fabric. W przypadku klastrów hostowanych na platformie Azure można dostosować ustawienia za pomocą [Azure Portal](https://portal.azure.com) lub szablonu Azure Resource Manager.

> [!NOTE]
> Nie wszystkie ustawienia są dostępne w portalu i [najlepszym rozwiązaniem jest dostosowanie go przy użyciu szablonu Azure Resource Manager](./service-fabric-best-practices-infrastructure-as-code.md). Portal jest przeznaczony wyłącznie dla Service Fabric scenariusza Dev\Test.
> 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="customize-cluster-settings-using-resource-manager-templates"></a>Dostosowywanie ustawień klastra przy użyciu szablonów Menedżer zasobów
Klastry platformy Azure można skonfigurować za pomocą szablonu Menedżer zasobów JSON. Aby dowiedzieć się więcej na temat różnych ustawień, zobacz [Ustawienia konfiguracji klastrów](service-fabric-cluster-fabric-settings.md). Przykładowo w poniższych krokach pokazano, jak dodać nowe ustawienie *MaxDiskQuotaInMB* do sekcji *diagnostyka* przy użyciu Azure Resource Explorer.

1. Przejdź do strony https://resources.azure.com
2. Przejdź do subskrypcji przez zwiększenie **subskrypcji**  ->  **\<Your Subscription>**  ->  **resourceGroups**  ->  **\<Your Resource Group>**  ->  **dostawców**  ->  **Microsoft. servicefabric**  ->  **klastrów** -> **\<Your Cluster Name>**
3. W prawym górnym rogu wybierz pozycję **Odczyt/zapis.**
4. Wybierz pozycję **Edytuj** i zaktualizuj `fabricSettings` element JSON i Dodaj nowy element:

```json
      {
        "name": "Diagnostics",
        "parameters": [
          {
            "name": "MaxDiskQuotaInMB",
            "value": "65536"
          }
        ]
      }
```

Ustawienia klastra można również dostosować w jeden z następujących Azure Resource Manager sposobów:

- Użyj [Azure Portal](../azure-resource-manager/templates/export-template-portal.md) , aby wyeksportować i zaktualizować szablon Menedżera zasobów.
- Użyj [programu PowerShell](../azure-resource-manager/management/manage-resources-powershell.md) , aby wyeksportować i zaktualizować szablon Menedżer zasobów.
- Użyj [interfejsu wiersza polecenia platformy Azure](../azure-resource-manager/management/manage-resources-cli.md) , aby wyeksportować i zaktualizować szablon Menedżer zasobów.
- Aby zmodyfikować ustawienie bezpośrednio, Użyj poleceń Azure PowerShell [Set-AzServiceFabricSetting](/powershell/module/az.servicefabric/set-azservicefabricsetting) i [Remove-AzServiceFabricSetting](/powershell/module/az.servicefabric/remove-azservicefabricsetting) .
- Aby zmodyfikować ustawienie bezpośrednio, użyj interfejsu wiersza polecenia platformy Azure [AZ SF Cluster Setting](/cli/azure/sf/cluster/setting) .

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [ustawieniach klastra Service Fabric](service-fabric-cluster-fabric-settings.md).
* Dowiedz się [, jak skalować klaster w programie i na zewnątrz](service-fabric-cluster-scale-in-out.md).
