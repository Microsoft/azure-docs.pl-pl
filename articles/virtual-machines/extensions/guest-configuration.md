---
title: Azure Policy konfiguracji gościa
description: Dowiedz się więcej o rozszerzeniu używanym do inspekcji/konfigurowania ustawień wewnątrz maszyn wirtualnych
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: mgreenegit
ms.author: migreene
ms.date: 04/15/2021
ms.openlocfilehash: 2fda3cc2cf9adc3a734780209a0c9cc06a04e7cf
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107368664"
---
# <a name="overview-of-the-azure-policy-guest-configuration-extension"></a>Omówienie rozszerzenia Azure Policy konfiguracji gościa

Rozszerzenie konfiguracja gościa jest składnikiem, Azure Policy wykonuje operacje inspekcji i konfiguracji wewnątrz maszyn wirtualnych.
Zasady, takie jak definicje punktów odniesienia zabezpieczeń dla [systemów Linux](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffc9b3da7-8347-4380-8e70-0a0361d8dedd) i [Windows,](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F72650e9f-97bc-4b2a-ab5f-9781a9fcecbc) nie mogą sprawdzać ustawień wewnątrz maszyn, dopóki rozszerzenie nie zostanie zainstalowane.

## <a name="prerequisites"></a>Wymagania wstępne

Aby maszyna uwierzytelniła się w usłudze konfiguracji gościa, musi mieć przypisaną przez [system tożsamość zarządzaną](../../active-directory/managed-identities-azure-resources/overview.md).
Jeśli ustawiono następującą właściwość, spełnione jest wymaganie dotyczące tożsamości na maszynie wirtualnej.

  ```json
  "identity": {
    "type": "SystemAssigned"
  }
  ```

### <a name="operating-systems"></a>Systemy operacyjne

Obsługa rozszerzenia konfiguracji gościa jest taka sama jak obsługa systemu operacyjnego [udokumentowana dla rozwiązania end-to-end.](../../governance/policy/concepts/guest-configuration.md#supported-client-types)

### <a name="internet-connectivity"></a>Łączność z Internetem

Agent zainstalowany przez rozszerzenie konfiguracja gościa musi mieć możliwość dotarcia do pakietów zawartości wymienionych w przypisaniach konfiguracji gościa i zgłoszenia stanu do usługi konfiguracji gościa.
Maszyna może nawiązać połączenie przy użyciu wychodzącego protokołu HTTPS przez port TCP 443 lub jeśli połączenie jest nawiązywane za pośrednictwem sieci prywatnej.
Aby dowiedzieć się więcej na temat sieci prywatnych, zobacz następujące artykuły:

- [Konfiguracja gościa, komunikacja za pośrednictwem linku prywatnego na platformie Azure](../../governance/policy/concepts/guest-configuration.md#communicate-over-private-link-in-azure)
- [Używanie prywatnych punktów końcowych dla usługi Azure Storage](../../storage/common/storage-private-endpoints.md)

## <a name="how-can-i-install-the-extension"></a>Jak zainstalować rozszerzenie?

Aby wdrożyć najnowszą wersję rozszerzenia na dużą skalę, w tym wymagania dotyczące tożsamości, Azure Policy, Wdrażanie wymagań wstępnych w celu włączenia zasad konfiguracji gościa [na maszynach wirtualnych.](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_Prerequisites.json)
W przypadku poszczególnych maszyn rozszerzenie można wdrożyć przy użyciu interfejsu wiersza polecenia platformy Azure, programu PowerShell Resource Manager szablonów aplikacji lub narzędzi innych firm.

Nazwa wystąpienia rozszerzenia musi być ustawiona na "AzurePolicyforWindows" lub "AzurePolicyforLinux", ponieważ zasady, do których odwołuje się powyżej, wymagają tych określonych ciągów.

Domyślnie wszystkie wdrożenia są aktualizowane do najnowszej wersji. Wartość właściwości _autoUpgradeMinorVersion_ ma wartość domyślną "true", chyba że określono inaczej. Nie musisz martwić się o aktualizowanie kodu, gdy zostaną wydane nowe wersje rozszerzenia.

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby wdrożyć rozszerzenie dla systemu Linux:


```azurecli
az vm extension set  --publisher Microsoft.GuestConfiguration --name ConfigurationforLinux --extension-instance-name AzurePolicyforLinux --resource-group myResourceGroup --vm-name myVM
```

Aby wdrożyć rozszerzenie dla systemu Windows:

```azurecli
az vm extension set  --publisher Microsoft.GuestConfiguration --name ConfigurationforWindows --extension-instance-name AzurePolicyforWindows --resource-group myResourceGroup --vm-name myVM
```

### <a name="powershell"></a>PowerShell

Aby wdrożyć rozszerzenie dla systemu Linux:

```powershell
Set-AzVMExtension -Publisher 'Microsoft.GuestConfiguration' -Type 'ConfigurationforLinux' -Name 'AzurePolicyforLinux' -TypeHandlerVersion 1.0 -ResourceGroupName 'myResourceGroup' -Location 'myLocation' -VMName 'myVM'
```

Aby wdrożyć rozszerzenie dla systemu Windows:

```powershell
Set-AzVMExtension -Publisher 'Microsoft.GuestConfiguration' -Type 'ConfigurationforWindows' -Name 'AzurePolicyforWindows' -TypeHandlerVersion 1.0 -ResourceGroupName 'myResourceGroup' -Location 'myLocation' -VMName 'myVM'
```

### <a name="resource-manager-template"></a>Szablon usługi Resource Manager

Aby wdrożyć rozszerzenie dla systemu Linux:

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "[concat(parameters('VMName'), '/AzurePolicyforLinux')]",
  "apiVersion": "2019-07-01",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', parameters('VMName'))]"
  ],
  "properties": {
    "publisher": "Microsoft.GuestConfiguration",
    "type": "ConfigurationforLinux",
    "typeHandlerVersion": "1.0"
    "autoUpgradeMinorVersion": true,
    "settings": {},
    "protectedSettings": {}
  }
}
```

Aby wdrożyć rozszerzenie dla systemu Windows:

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "[concat(parameters('VMName'), '/AzurePolicyforWindows')]",
  "apiVersion": "2019-07-01",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', parameters('VMName'))]"
  ],
  "properties": {
    "publisher": "Microsoft.GuestConfiguration",
    "type": "ConfigurationforWindows",
    "typeHandlerVersion": "1.0"
    "autoUpgradeMinorVersion": true,
    "settings": {},
    "protectedSettings": {}
  }
}
```

### <a name="terraform"></a>Terraform

Aby wdrożyć rozszerzenie dla systemu Linux:

```terraform
resource "azurerm_virtual_machine_extension" "gc" {
  name                  = "AzurePolicyforLinux"
  virtual_machine_id    = "myVMID"
  publisher             = "Microsoft.GuestConfiguration"
  type                  = "ConfigurationforLinux"
  type_handler_version  = "1.0"
}
```

Aby wdrożyć rozszerzenie dla systemu Windows:

```terraform
resource "azurerm_virtual_machine_extension" "gc" {
  name                  = "AzurePolicyforWindows"
  virtual_machine_id    = "myVMID"
  publisher             = "Microsoft.GuestConfiguration"
  type                  = "ConfigurationforWindows"
  type_handler_version  = "1.0"
}
```

## <a name="settings"></a>Ustawienia

Nie ma potrzeby dołączania żadnych ustawień ani właściwości chronionych ustawień w rozszerzeniu.
Wszystkie takie informacje są pobierane przez agenta z zasobów [przypisania konfiguracji gościa.](/rest/api/guestconfiguration/guestconfigurationassignments) Na przykład właściwości [ConfigurationUri,](/rest/api/guestconfiguration/guestconfigurationassignments/createorupdate#guestconfigurationnavigation) [Mode](/rest/api/guestconfiguration/guestconfigurationassignments/createorupdate#configurationmode)i [ConfigurationSetting](/rest/api/guestconfiguration/guestconfigurationassignments/createorupdate#configurationsetting) są zarządzane dla poszczególnych konfiguracji, a nie dla rozszerzenia maszyny wirtualnej.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji Azure Policy konfiguracji gościa, zobacz [Understand Azure Policy's Guest Configuration](../../governance/policy/concepts/guest-configuration.md) (Opis konfiguracji gościa usługi Azure Policy)
* Aby uzyskać więcej informacji na temat działania agenta i rozszerzeń systemu Linux, zobacz Rozszerzenia i funkcje maszyn wirtualnych platformy [Azure dla systemu Linux.](features-linux.md)
* Aby uzyskać więcej informacji na temat działania agenta gościa systemu Windows i rozszerzeń, zobacz Rozszerzenia i funkcje maszyn wirtualnych platformy [Azure dla systemu Windows.](features-windows.md)  
* Aby zainstalować agenta gościa systemu Windows, zobacz Azure Windows Virtual Machine Agent Overview (Omówienie agenta maszyny [wirtualnej z systemem Windows na platformie Azure).](agent-windows.md)  
* Aby zainstalować agenta systemu Linux, zobacz Azure Linux Virtual Machine Agent Overview (Omówienie agenta maszyny [wirtualnej z systemem Linux na platformie Azure).](agent-linux.md)  
