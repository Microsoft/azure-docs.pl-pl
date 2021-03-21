---
title: Zastosuj rozszerzenie diagnostyki systemu Windows Azure w Cloud Services (obsługa rozszerzona)
description: Zastosuj rozszerzenie diagnostyki Microsoft Azure dla Cloud Services (obsługa rozszerzona)
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: ad2a27d1e41ba8e589aa98542c4a0cb3d92afbea
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99430869"
---
# <a name="apply-the-windows-azure-diagnostics-extension-in-cloud-services-extended-support"></a>Zastosuj rozszerzenie diagnostyki systemu Windows Azure w Cloud Services (obsługa rozszerzona) 
Kluczowe metryki wydajności można monitorować dla każdej usługi w chmurze. Każda rola usługi w chmurze zbiera minimalne dane: użycie procesora, użycie sieci i użycie dysku. Jeśli usługa w chmurze ma rozszerzenie Microsoft. Azure. Diagnostics zastosowane do roli, ta rola może zbierać dodatkowe punkty danych. Aby uzyskać więcej informacji, zobacz [Omówienie rozszerzeń](extensions.md)

Rozszerzenie systemu Windows Diagnostyka Azure można włączyć dla Cloud Services (obsługa rozszerzona) za pomocą [programu PowerShell](deploy-powershell.md) lub [szablonu ARM](deploy-template.md)

## <a name="apply-windows-azure-diagnostics-extension-using-powershell"></a>Stosowanie rozszerzenia Diagnostyka Azure systemu Windows przy użyciu programu PowerShell

```powershell
# Create WAD extension object
$storageAccountKey = Get-AzStorageAccountKey -ResourceGroupName "ContosOrg" -Name "contosostorageaccount"
$configFile = "<WAD public configuration file path>"
$wadExtension = New-AzCloudServiceDiagnosticsExtension -Name "WADExtension" -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -StorageAccountName "contosostorageaccount" -StorageAccountKey $storageAccountKey[0].Value -DiagnosticsConfigurationPath $configFile -TypeHandlerVersion "1.5" -AutoUpgradeMinorVersion $true 

# Get existing Cloud Service
$cloudService = Get-AzCloudService -ResourceGroup "ContosOrg" -CloudServiceName "ContosoCS"

# Add WAD extension to existing Cloud Service extension object
$cloudService.ExtensionProfile.Extension = $cloudService.ExtensionProfile.Extension + $wadExtension

# Update Cloud Service
$cloudService | Update-AzCloudService
```

## <a name="apply-windows-azure-diagnostics-extension-using-arm-template"></a>Stosowanie rozszerzenia Diagnostyka Azure systemu Windows przy użyciu szablonu ARM
```json
"extensionProfile": { 
          "extensions": [ 
            { 
              "name": "Microsoft.Insights.VMDiagnosticsSettings_WebRole1", 
              "properties": { 
                "autoUpgradeMinorVersion": true, 
                "publisher": "Microsoft.Azure.Diagnostics", 
                "type": "PaaSDiagnostics", 
                "typeHandlerVersion": "1.5", 
                "settings": "Include PublicConfig XML as a raw string", 
                "protectedSettings": "Include PrivateConfig XML as a raw string”", 
                "rolesAppliedTo": [ 
                  "WebRole1" 
                ] 
              } 
            }
          ]
        },

```

## <a name="next-steps"></a>Następne kroki 
- Zapoznaj się z [wymaganiami wstępnymi](deploy-prerequisite.md) dotyczącymi wdrażania Cloud Services (obsługa rozszerzona).
- Zapoznaj się z [często zadawanymi pytaniami](faq.md) dotyczącymi Cloud Services (obsługa rozszerzona).
- Wdróż usługę w chmurze (obsługę rozszerzoną) przy użyciu [Azure Portal](deploy-portal.md), [programu PowerShell](deploy-powershell.md), [szablonu](deploy-template.md) lub [programu Visual Studio](deploy-visual-studio.md).
