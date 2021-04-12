---
title: Przesłoń informacje o jednostce SKU za pośrednictwem CSCFG/CSDEF dla platformy Azure Cloud Services (obsługa rozszerzona)
description: Przesłoń informacje o jednostce SKU za pośrednictwem CSCFG/CSDEF dla platformy Azure Cloud Services (obsługa rozszerzona)
ms.topic: how-to
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachandw
ms.date: 04/05/2021
ms.custom: ''
ms.openlocfilehash: 17e47b562c52ffce631a01cf03004d77053ea647
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2021
ms.locfileid: "106387334"
---
# <a name="override-sku-information-over-cscfgcsdef-in-cloud-services-extended-support"></a>Przesłoń informacje o jednostkach SKU za pośrednictwem CSCFG/CSDEF w Cloud Services (obsługa rozszerzona) 

Ta funkcja umożliwi użytkownikowi zaktualizowanie rozmiaru roli i liczby wystąpień w usłudze w chmurze przy użyciu właściwości **allowModelOverride** bez konieczności aktualizacji plików definicji usługi i usług, dzięki czemu usługa w chmurze może skalować w górę/w dół/w dół bez konieczności ponownego pakowania i wdrażania.

## <a name="set-allowmodeloverride-property"></a>Ustaw właściwość allowModelOverride
Właściwość allowModelOverride można ustawić w następujący sposób:
* Gdy allowModelOverride = true, wywołanie interfejsu API spowoduje zaktualizowanie rozmiaru roli i liczby wystąpień dla usługi w chmurze bez weryfikowania wartości przy użyciu plików csdef i cscfg. 
> [!Note]
> Cscfg zostanie zaktualizowany w celu odzwierciedlenia liczby wystąpień roli, ale csdef (w ramach cspkg) spowoduje zachowanie starych wartości
* Gdy allowModelOverride = false, wywołanie interfejsu API mógłby zgłosić błąd, jeśli rozmiar roli i wartości liczby wystąpień nie są zgodne z plikami csdef i cscfg odpowiednio

Wartość domyślna to false. Jeśli właściwość zostanie zresetowana do wartości FAŁSZ z wartości true, pliki csdef i cscfg byłyby ponownie sprawdzone pod kątem walidacji.

Zapoznaj się z poniższymi przykładami, aby zastosować właściwość w programie PowerShell, szablonie i zestawie SDK.

### <a name="azure-resource-manager-template"></a>Szablon usługi Azure Resource Manager
Ustawienie właściwości "allowModelOverride" = true w tym miejscu spowoduje zaktualizowanie usługi w chmurze przy użyciu właściwości roli zdefiniowanych w sekcji roleProfile
```json
"properties": {
        "packageUrl": "[parameters('packageSasUri')]",
        "configurationUrl": "[parameters('configurationSasUri')]",
        "upgradeMode": "[parameters('upgradeMode')]",
        “**allowModelOverride**” : true,
        "roleProfile": {
          "roles": [
            {
              "name": "WebRole1",
              "sku": {
                "name": "Standard_D1_v2",
                "capacity": "1"
              }
            },
            {
              "name": "WorkerRole1",
              "sku": {
                "name": "Standard_D1_v2",
                "capacity": "1"
              }
            }
          ]
        },

```
### <a name="powershell"></a>PowerShell
Ustawienie przełącznika "AllowModelOverride" w nowym poleceniu cmdlet New-AzCloudService spowoduje zaktualizowanie usługi w chmurze za pomocą właściwości jednostki SKU zdefiniowanej w RoleProfile
```powershell
New-AzCloudService ` 
-Name “ContosoCS” ` 
-ResourceGroupName “ContosOrg” ` 
-Location “East US” `
-AllowModelOverride  ` 
-PackageUrl $cspkgUrl ` 
-ConfigurationUrl $cscfgUrl ` 
-UpgradeMode 'Auto' ` 
-RoleProfile $roleProfile ` 
-NetworkProfile $networkProfile  ` 
-ExtensionProfile $extensionProfile ` 
-OSProfile $osProfile `
-Tag $tag
```
### <a name="sdk"></a>SDK
Ustawienie zmiennej AllowModelOverride = true spowoduje zaktualizowanie usługi w chmurze za pomocą właściwości jednostki SKU zdefiniowanej w RoleProfile

```csharp
CloudService cloudService = new CloudService
    {
        Properties = new CloudServiceProperties
            {
                RoleProfile = cloudServiceRoleProfile
                Configuration = < Add Cscfg xml content here>
                PackageUrl = <Add cspkg SAS url here>,
                ExtensionProfile = cloudServiceExtensionProfile,
                OsProfile= cloudServiceOsProfile,
                NetworkProfile = cloudServiceNetworkProfile,
                UpgradeMode = 'Auto',
                AllowModelOverride = true
            },
                Location = m_location
            };
CloudService createOrUpdateResponse = m_CrpClient.CloudServices.CreateOrUpdate(“ContosOrg”, “ContosoCS”, cloudService);
```
### <a name="azure-portal"></a>Azure Portal
Portal nie zezwala na powyższą właściwość w celu zastąpienia rozmiaru roli i liczby wystąpień w csdef i cscfg. 


## <a name="next-steps"></a>Następne kroki 
- Zapoznaj się z [wymaganiami wstępnymi](deploy-prerequisite.md) dotyczącymi wdrażania Cloud Services (obsługa rozszerzona).
- Zapoznaj się z [często zadawanymi pytaniami](faq.md) dotyczącymi Cloud Services (obsługa rozszerzona).
