---
title: Uaktualnianie konfiguracji klastra usługi Azure Service Fabric
description: Dowiedz się, jak uaktualnić konfigurację, w której działa klaster Service Fabric na platformie Azure przy użyciu szablonu Menedżer zasobów.
author: dkkapur
ms.topic: conceptual
ms.date: 11/09/2018
ms.author: dekapur
ms.openlocfilehash: c2973428354f101b5b546128b08bf67587923a8e
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/05/2020
ms.locfileid: "82793078"
---
# <a name="upgrade-the-configuration-of-a-cluster-in-azure"></a>Uaktualnianie konfiguracji klastra na platformie Azure 

W tym artykule opisano sposób dostosowywania różnych ustawień sieci szkieletowej dla klastra Service Fabric. W przypadku klastrów hostowanych na platformie Azure można dostosować ustawienia za pomocą [Azure Portal](https://portal.azure.com) lub szablonu Azure Resource Manager.

> [!NOTE]
> Nie wszystkie ustawienia są dostępne w portalu i [najlepszym rozwiązaniem jest dostosowanie go przy użyciu szablonu Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code). Portal jest przeznaczony wyłącznie dla Service Fabric scenariusza Dev\Test.
> 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="customize-cluster-settings-using-resource-manager-templates"></a>Dostosowywanie ustawień klastra przy użyciu szablonów Menedżer zasobów
Klastry platformy Azure można skonfigurować za pomocą szablonu Menedżer zasobów JSON. Aby dowiedzieć się więcej na temat różnych ustawień, zobacz [Ustawienia konfiguracji klastrów](service-fabric-cluster-fabric-settings.md). Przykładowo w poniższych krokach pokazano, jak dodać nowe ustawienie *MaxDiskQuotaInMB* do sekcji *diagnostyka* przy użyciu Azure Resource Explorer.

1. Przejdź do strony https://resources.azure.com
2. Przejdź **do subskrypcji, zwiększając** -> **\< **subskrypcję>**resourceGroups** ->   -> resourceGroups**\<grupę zasobów>**  ->  **dostawcy** -> **Microsoft. servicefabric** -> **klastrów** -> **\<Nazwa klastra>**
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

- Użyj [Azure Portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template) , aby wyeksportować i zaktualizować szablon Menedżera zasobów.
- Użyj [programu PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-powershell) , aby wyeksportować i zaktualizować szablon Menedżer zasobów.
- Użyj [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-cli) , aby wyeksportować i zaktualizować szablon Menedżer zasobów.
- Aby zmodyfikować ustawienie bezpośrednio, Użyj poleceń Azure PowerShell [Set-AzServiceFabricSetting](https://docs.microsoft.com/powershell/module/az.servicefabric/Set-azServiceFabricSetting) i [Remove-AzServiceFabricSetting](https://docs.microsoft.com/powershell/module/az.servicefabric/Remove-azServiceFabricSetting) .
- Aby zmodyfikować ustawienie bezpośrednio, użyj interfejsu wiersza polecenia platformy Azure [AZ SF Cluster Setting](https://docs.microsoft.com/cli/azure/sf/cluster/setting) .

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [ustawieniach klastra Service Fabric](service-fabric-cluster-fabric-settings.md).
* Dowiedz się [, jak skalować klaster w programie i na zewnątrz](service-fabric-cluster-scale-in-out.md).
