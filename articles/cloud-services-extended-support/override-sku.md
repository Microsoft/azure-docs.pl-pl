---
title: Przesłanianie informacji o sku za pośrednictwem zasad CSCFG/CSDEF Azure Cloud Services (rozszerzona obsługa)
description: Przesłanianie informacji o sku za pośrednictwem zasad CSCFG/CSDEF Azure Cloud Services (rozszerzona obsługa)
ms.topic: how-to
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachandw
ms.date: 04/05/2021
ms.custom: ''
ms.openlocfilehash: d5dfae4b5cfee8f61e11e418a05e86017d119410
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739264"
---
# <a name="override-sku-information-over-cscfgcsdef-in-cloud-services-extended-support"></a>Przesłanianie informacji o sku za pośrednictwem zasad CSCFG/CSDEF w Cloud Services (rozszerzona obsługa) 

Ta funkcja umożliwi użytkownikowi aktualizowanie rozmiaru roli i liczby wystąpień w usłudze w chmurze przy użyciu właściwości **allowModelOverride** bez konieczności aktualizowania plików konfiguracji usługi i definicji usługi, dzięki czemu usługa w chmurze może skalować w górę/w dół/w dół/w poziomie bez konieczności ponownego pakowania i ponownego pakowania.

## <a name="set-allowmodeloverride-property"></a>Ustawianie właściwości allowModelOverride
Właściwość allowModelOverride można ustawić w następujący sposób:
* Gdy allowModelOverride = true, wywołanie interfejsu API zaktualizuje rozmiar roli i liczbę wystąpień dla usługi w chmurze bez sprawdzania poprawności wartości za pomocą plików csdef i cscfg. 
> [!Note]
> Cscfg zostanie zaktualizowany w celu odzwierciedlenia liczby wystąpień roli, ale csdef (w ramach cspkg) zachowa stare wartości
* Gdy allowModelOverride = false, wywołanie interfejsu API zgłasza błąd, gdy wartości rozmiaru roli i liczby wystąpień nie są zgodne odpowiednio z plikami csdef i cscfg

Wartość domyślna to false. Jeśli właściwość zostanie zresetowana do wartości false z wartości true, pliki csdef i cscfg zostaną ponownie sprawdzone pod kątem walidacji.

Zapoznaj się z poniższymi przykładami, aby zastosować właściwość w programie PowerShell, szablonie i zestawie SDK

### <a name="azure-resource-manager-template"></a>Szablon usługi Azure Resource Manager
Ustawienie właściwości "allowModelOverride" = true spowoduje zaktualizowanie usługi w chmurze przy użyciu właściwości roli zdefiniowanych w sekcji roleProfile
```json
"properties": {
        "packageUrl": "[parameters('packageSasUri')]",
        "configurationUrl": "[parameters('configurationSasUri')]",
        "upgradeMode": "[parameters('upgradeMode')]",
        “allowModelOverride” : true,
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
Ustawienie przełącznika "AllowModelOverride" w nowym New-AzCloudService cmdlet spowoduje zaktualizowanie usługi w chmurze przy użyciu właściwości SKU zdefiniowanych w pliku RoleProfile
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
Ustawienie zmiennej AllowModelOverride= true spowoduje zaktualizowanie usługi w chmurze przy użyciu właściwości SKU zdefiniowanych w pliku RoleProfile

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
Portal nie zezwala powyższej właściwości na zastępowanie rozmiaru roli i liczby wystąpień w csdef i cscfg. 


## <a name="next-steps"></a>Następne kroki 
- Zapoznaj się z [warunkami wstępnymi wdrażania](deploy-prerequisite.md) Cloud Services (rozszerzona pomoc techniczna).
- Przejrzyj [często zadawane pytania dotyczące](faq.md) Cloud Services (rozszerzona pomoc techniczna).
