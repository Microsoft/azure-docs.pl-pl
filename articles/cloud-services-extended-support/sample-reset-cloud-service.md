---
title: Przykłady Azure PowerShell — Resetowanie Cloud Services platformy Azure (obsługa rozszerzona)
description: Przykładowe skrypty do resetowania wdrożenia usługi w chmurze platformy Azure (obsługa rozszerzona)
ms.topic: sample
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: a2c10e7915b0d293d3193c710885ded26a791f2c
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98745134"
---
# <a name="reset-an-azure-cloud-service-extended-support"></a>Resetowanie usługi w chmurze platformy Azure (obsługa rozszerzona) 
Te przykłady obejmują różne sposoby resetowania istniejącego wdrożenia usługi w chmurze platformy Azure (obsługa rozszerzona).

## <a name="reimage-role-instances-of-cloud-service"></a>Odtwarzanie z obrazu wystąpień roli usługi w chmurze
```powershell
$roleInstances = @("ContosoFrontEnd_IN_0", "ContosoBackEnd_IN_1")
Reset-AzCloudService -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance $roleInstances -Reimage
```
To polecenie powoduje odtworzenie obrazów wystąpień roli **ContosoFrontEnd \_ w \_ 0** i **ContosoBackEnd \_ w \_ 1 usługi w** chmurze o nazwie ContosoCS, która należy do grupy zasobów o nazwie ContosOrg.

## <a name="reimage-all-roles-of-cloud-service"></a>Odtwórz z obrazu wszystkie role usługi w chmurze
```powershell
Reset-AzCloudService -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance "*" -Reimage
```

## <a name="reimage-a-single-role-instance-of-a-cloud-service"></a>Odtwarzanie z obrazu pojedynczego wystąpienia roli usługi w chmurze
```powershell
Reset-AzCloudServiceRoleInstance -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstanceName "ContosoFrontEnd_IN_0" -Reimage
```

## <a name="restart-a-single-role-instance-of-a-cloud-service"></a>Uruchom ponownie pojedyncze wystąpienie roli usługi w chmurze
```powershell
Reset-AzCloudService -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance "*" -Restart
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat platformy Azure Cloud Services (obsługa rozszerzona), zobacz [Omówienie usługi azure Cloud Services (obsługa rozszerzona)](overview.md).