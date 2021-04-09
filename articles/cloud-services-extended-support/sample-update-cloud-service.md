---
title: Przykłady dla programu Azure PowerShell — Aktualizowanie usługi w chmurze platformy Azure (wsparcie dodatkowe)
description: Przykładowe skrypty do aktualizowania wdrożeń usługi w chmurze platformy Azure (obsługa rozszerzona)
ms.topic: sample
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: fcb92ec3aa8f360107f0e7220415c57344c6d83a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98881472"
---
# <a name="update-an-azure-cloud-service-extended-support"></a>Aktualizowanie usługi w chmurze platformy Azure (wsparcie dodatkowe)

Te przykłady obejmują różne sposoby aktualizowania istniejącego wdrożenia usługi w chmurze platformy Azure (obsługa rozszerzona).

## <a name="add-an-extension-to-existing-cloud-service"></a>Dodawanie rozszerzenia do istniejącej usługi w chmurze
```powershell
# Create RDP extension object
$rdpExtension = New-AzCloudServiceRemoteDesktopExtensionObject -Name "RDPExtension" -Credential $credential -Expiration $expiration -TypeHandlerVersion "1.2.1"
# Get existing Cloud Service
$cloudService = Get-AzCloudService -ResourceGroup "ContosOrg" -CloudServiceName "ContosoCS"
# Add RDP extension to existing Cloud Service extension object
$cloudService.ExtensionProfileExtension = $cloudService.ExtensionProfileExtension + $rdpExtension
# Update Cloud Service
$cloudService | Update-AzCloudService
```

## <a name="remove-all-extensions-from-a-cloud-service"></a>Usuń wszystkie rozszerzenia z usługi w chmurze
```powershell
# Get existing Cloud Service
$cloudService = Get-AzCloudService -ResourceGroup "ContosOrg" -CloudServiceName "ContosoCS"
# Set extension to empty list
$cloudService.ExtensionProfileExtension = @()
# Update Cloud Service
$cloudService | Update-AzCloudService
```

## <a name="remove-the-remote-desktop-extension-from-cloud-service"></a>Usuń rozszerzenie pulpitu zdalnego z usługi w chmurze
```powershell
# Get existing Cloud Service
$cloudService = Get-AzCloudService -ResourceGroup "ContosOrg" -CloudServiceName "ContosoCS"
# Remove extension by name RDPExtension
$cloudService.ExtensionProfileExtension = $cloudService.ExtensionProfileExtension | Where-Object { $_.Name -ne "RDPExtension" }
# Update Cloud Service
$cloudService | Update-AzCloudService
```

## <a name="scale-out--scale-in-role-instances"></a>Skalowanie w poziomie/skalowanie w poziomie roli
```powershell
# Get existing Cloud Service
$cloudService = Get-AzCloudService -ResourceGroup "ContosOrg" -CloudServiceName "ContosoCS"

# Scale-out all role instance count by 1
$cloudService.RoleProfileRole | ForEach-Object {$_.SkuCapacity += 1}

# Scale-in ContosoFrontend role instance count by 1
$role = $cloudService.RoleProfileRole | Where-Object {$_.Name -eq "ContosoFrontend"}
 $role.SkuCapacity -= 1

# Update Cloud Service configuration as per the new role instance count
$cloudService.Configuration = $configuration

# Update Cloud Service
$cloudService | Update-AzCloudService
```

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat platformy Azure Cloud Services (obsługa rozszerzona), zobacz [Omówienie usługi azure Cloud Services (obsługa rozszerzona)](overview.md).
- Odwiedź [repozytorium przykładów Cloud Services (obsługa rozszerzona)](https://github.com/Azure-Samples/cloud-services-extended-support)