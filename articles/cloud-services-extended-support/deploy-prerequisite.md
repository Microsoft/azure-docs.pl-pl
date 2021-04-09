---
title: Wymagania wstępne dotyczące wdrażania usługi Azure Cloud Services (obsługa rozszerzona)
description: Wymagania wstępne dotyczące wdrażania usługi Azure Cloud Services (obsługa rozszerzona)
ms.topic: article
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 21433e1a0441ef458dd5f8ea4b968211ef82cd46
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104865608"
---
# <a name="prerequisites-for-deploying-azure-cloud-services-extended-support"></a>Wymagania wstępne dotyczące wdrażania usługi Azure Cloud Services (obsługa rozszerzona)

> [!IMPORTANT]
> Cloud Services (obsługa rozszerzona) jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Aby zapewnić pomyślne wdrożenie Cloud Services (obsługa rozszerzona), zapoznaj się z poniższymi krokami i Ukończ każdy element przed podjęciem próby wdrożenia. 

## <a name="register-the-cloudservices-feature"></a>Rejestrowanie funkcji CloudServices
Zarejestruj funkcję dla subskrypcji. Rejestracja może potrwać kilka minut. 

```powershell
Register-AzProviderFeature -FeatureName CloudServices -ProviderNamespace Microsoft.Compute
```

Sprawdź stan rejestracji, korzystając z następujących elementów:  
```powershell
Get-AzProviderFeature 

#Sample output
FeatureName               ProviderName      RegistrationState
CloudServices           Microsoft.Compute    Registered
```

## <a name="required-service-configuration-cscfg-file-updates"></a>Aktualizacje wymaganego pliku konfiguracji usługi (. cscfg)

### <a name="1-virtual-network"></a>1) Virtual Network
Wdrożenia usługi w chmurze (obsługa rozszerzona) muszą znajdować się w sieci wirtualnej. Sieć wirtualną można utworzyć za pomocą [Azure Portal](../virtual-network/quick-create-portal.md), [programu PowerShell](../virtual-network/quick-create-powershell.md), [interfejsu wiersza polecenia platformy Azure](../virtual-network/quick-create-cli.md) lub [szablonu ARM](../virtual-network/quick-create-template.md). Do sieci wirtualnej i podsieci muszą być również przywoływane w konfiguracji usługi (. cscfg) w sekcji [networkconfiguration](schema-cscfg-networkconfiguration.md) . 

W przypadku sieci wirtualnych należących do tej samej grupy zasobów co usługa w chmurze wystarcza tylko nazwa sieci wirtualnej w pliku konfiguracji usługi (. cscfg). Jeśli sieć wirtualna i usługa w chmurze znajdują się w dwóch różnych grupach zasobów, należy określić pełny identyfikator Azure Resource Manager sieci wirtualnej w pliku konfiguracji usługi (cscfg).
 
#### <a name="virtual-network-located-in-same-resource-group"></a>Virtual Network znajdujący się w tej samej grupie zasobów
```xml
<VirtualNetworkSite name="<vnet-name>"/> 
  <AddressAssignments> 
    <InstanceAddress roleName="<role-name>"> 
     <Subnets> 
       <Subnet name="<subnet-name>"/> 
     </Subnets> 
    </InstanceAddress> 
```

#### <a name="virtual-network-located-in-different-resource-group"></a>Sieć wirtualna znajdująca się w innej grupie zasobów
```xml
<VirtualNetworkSite name="/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.Network/virtualNetworks/<vnet-name>"/> 
   <AddressAssignments> 
     <InstanceAddress roleName="<role-name>"> 
       <Subnets> 
        <Subnet name="<subnet-name>"/> 
       </Subnets> 
     </InstanceAddress> 
```
### <a name="2-remove-the-old-plugins"></a>2) usuń stare wtyczki

Usuń stare ustawienia pulpitu zdalnego z pliku konfiguracji usługi (. cscfg).  

```xml
<Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.Enabled" value="true" /> 
<Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountUsername" value="gachandw" /> 
<Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountEncryptedPassword" value="XXXX" /> 
<Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountExpiration" value="2021-12-17T23:59:59.0000000+05:30" /> 
<Setting name="Microsoft.WindowsAzure.Plugins.RemoteForwarder.Enabled" value="true" /> 
```
Usuń stare ustawienia diagnostyki dla każdej roli w pliku konfiguracji usługi (. cscfg).

```xml
<Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
```

## <a name="required-service-definition-file-csdef-updates"></a>Wymagane aktualizacje pliku definicji usługi (. csdef)

> [!NOTE]
> Zmiany w pliku definicji usługi (. csdef) wymagają ponownego wygenerowania pliku pakietu (. cspkg). Skompiluj i ponownie utwórz pakiet. cspkg po wprowadzeniu następujących zmian w pliku. csdef w celu uzyskania najnowszych ustawień usługi w chmurze

### <a name="1-virtual-machine-sizes"></a>1) rozmiary maszyn wirtualnych
Poniższe rozmiary są przestarzałe w Azure Resource Manager. Jeśli jednak chcesz kontynuować korzystanie z nich, zaktualizuj `vmsize` nazwę za pomocą skojarzonej konwencji nazewnictwa Azure Resource Manager.  

| Nazwa poprzedniego rozmiaru | Nazwa zaktualizowanego rozmiaru | 
|---|---|
| ExtraSmall | Standardowa_A0 | 
| Mały | Standardowa_A1 |
| Śred. | Standardowa_A2 | 
| Duży | Standardowa_A3 | 
| ExtraLarge | Standardowa_A4 | 
| A5 | Standardowa_A5 | 
| A6 | Standardowa_A6 | 
| A7 | Standardowa_A7 |  
| A8 | Standard_A8 | 
| A9 | Standard_A9 |
| A10 | Standardowa_A10 | 
| A11 | Standardowa_A11 | 
| MSODSG5 | Standard_MSODSG5 | 

 Na przykład `<WorkerRole name="WorkerRole1" vmsize="Medium"` `<WorkerRole name="WorkerRole1" vmsize="Standard_A2"` .
 
> [!NOTE]
> Aby pobrać listę dostępnych rozmiarów, zobacz [jednostki SKU zasobów — listę](/rest/api/compute/resourceskus/list) i Zastosuj następujące filtry: <br>
`ResourceType = virtualMachines ` <br>
`VMDeploymentTypes = PaaS `


### <a name="2-remove-old-remote-desktop-plugins"></a>2) usuń stare Dodatki pulpitu zdalnego
Wdrożenia korzystające ze starych wtyczek pulpitu zdalnego muszą mieć usunięte moduły z pliku definicji usługi (. csdef) i skojarzonych certyfikatów. 

```xml
<Imports> 
<Import moduleName="RemoteAccess" /> 
<Import moduleName="RemoteForwarder" /> 
</Imports> 
```
Wdrożenia, które używały starych wtyczek diagnostyki, wymagają ustawień usuniętych dla każdej roli z pliku definicji usługi (. csdef)

```xml
<Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
```

## <a name="key-vault-creation"></a>Tworzenie Key Vault 

Key Vault jest używany do przechowywania certyfikatów skojarzonych z Cloud Services (obsługa rozszerzona). Dodaj certyfikaty do Key Vault, a następnie odwołuje się do odcisków palców certyfikatu w pliku konfiguracji usługi. Należy również włączyć Key Vault "zasady dostępu" (w portalu) dla platformy "Azure Virtual Machines do wdrożenia", aby zasób Cloud Services (obsługa rozszerzona) mógł pobrać certyfikat zapisany jako wpisy tajne z Key Vault. Magazyn kluczy można utworzyć w [Azure Portal](../key-vault/general/quick-create-portal.md) lub przy użyciu [programu PowerShell](../key-vault/general/quick-create-powershell.md). Magazyn kluczy musi być utworzony w tym samym regionie i w ramach subskrypcji co usługa w chmurze. Aby uzyskać więcej informacji, zobacz [Korzystanie z certyfikatów przy użyciu usługi Azure Cloud Services (obsługa rozszerzona)](certificates-and-key-vault.md).

## <a name="next-steps"></a>Następne kroki 
- Zapoznaj się z [wymaganiami wstępnymi](deploy-prerequisite.md) dotyczącymi wdrażania Cloud Services (obsługa rozszerzona).
- Wdróż usługę w chmurze (obsługę rozszerzoną) przy użyciu [Azure Portal](deploy-portal.md), [programu PowerShell](deploy-powershell.md), [szablonu](deploy-template.md) lub [programu Visual Studio](deploy-visual-studio.md).
- Zapoznaj się z [często zadawanymi pytaniami](faq.md) dotyczącymi Cloud Services (obsługa rozszerzona).
- Odwiedź [repozytorium przykładów Cloud Services (obsługa rozszerzona)](https://github.com/Azure-Samples/cloud-services-extended-support)
