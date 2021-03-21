---
title: Rozwiązania Stackify retrace rozszerzenie agenta systemu Linux platformy Azure
description: Wdróż agenta ponownego śledzenia rozwiązania Stackify na maszynie wirtualnej z systemem Linux.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: linux
ms.date: 04/12/2018
ms.openlocfilehash: 1fc437637fde524da125af9191bf9de79a2e9c37
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102559005"
---
# <a name="stackify-retrace-linux-agent-extension"></a>Rozwiązania Stackify retrace rozszerzenie agenta systemu Linux

## <a name="overview"></a>Omówienie

Rozwiązania Stackify zawiera produkty, które śledzą szczegółowe informacje o aplikacji, aby ułatwić szybkie znajdowanie i rozwiązywanie problemów. W przypadku zespołów deweloperów, retrace to w pełni zintegrowana, wielośrodowiskowa, niewydajna wydajność aplikacji. Łączy on kilka narzędzi, których potrzebuje każdy zespół programistyczny.

Retrace to JEDYNe narzędzie, które zapewnia wszystkie poniższe możliwości we wszystkich środowiskach na jednej platformie.

* Zarządzanie wydajnością aplikacji (APM)
* Rejestrowanie aplikacji i serwera
* Śledzenie i monitorowanie błędów
* Serwer, aplikacja i metryki niestandardowe

**Informacje o rozszerzeniu agenta rozwiązania Stackify Linux**

To rozszerzenie zapewnia ścieżkę instalacji agenta systemu Linux do ponownego śledzenia. 

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="operating-system"></a>System operacyjny 

Agenta ponownego śledzenia można uruchomić w odniesieniu do tych dystrybucji systemu Linux.

| Dystrybucja | Wersja |
|---|---|
| Ubuntu | 16,04 LTS, 14,04 LTS, 16,10 i 17,04 |
| Debian | 7,9 + i 8.2 +, 9 |
| Red Hat | 6.7 +, 7.1 + |
| CentOS | 6.3 +, 7.0 + |

### <a name="internet-connectivity"></a>Łączność z Internetem

Rozszerzenie agenta rozwiązania Stackify dla systemu Linux wymaga, aby docelowa maszyna wirtualna była połączona z Internetem. 

Może być konieczne dostosowanie konfiguracji sieci w celu umożliwienia połączeń z usługą rozwiązania Stackify, zobacz https://support.stackify.com/hc/en-us/articles/207891903-Adding-Exceptions-to-a-Firewall . 


## <a name="extension-schema"></a>Schemat rozszerzenia

---

Poniższy kod JSON przedstawia schemat rozszerzenia agenta retrace rozwiązania Stackify. Rozszerzenie wymaga `environment` i `activationKey` .

```json
    {
      "type": "extensions",
      "name": "StackifyExtension",
      "apiVersion": "[variables('apiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.Compute/virtualMachines',variables('vmName'))]"
      ],
      "properties": {
        "publisher": "Stackify.LinuxAgent.Extension",
        "type": "StackifyLinuxAgentExtension",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "environment": "myEnvironment"
        },
        "protectedSettings": {
          "activationKey": "myActivationKey"
        }
      }
    }      
```

## <a name="template-deployment"></a>Wdrażanie na podstawie szablonu 

Rozszerzenia maszyny wirtualnej platformy Azure można wdrażać za pomocą szablonów Azure Resource Manager. Schemat JSON opisany w poprzedniej sekcji można użyć w szablonie Azure Resource Manager, aby uruchomić rozszerzenie rozwiązania Stackify retrace agenta systemu Linux podczas wdrażania szablonu Azure Resource Manager.  

KOD JSON rozszerzenia maszyny wirtualnej może być zagnieżdżony w obrębie zasobu maszyny wirtualnej lub umieszczony na głównym lub najwyższym poziomie szablonu JSON Menedżer zasobów. Położenie pliku JSON wpływa na wartość nazwy zasobu i typu. Aby uzyskać więcej informacji, zobacz Ustawianie nazwy i typu dla zasobów podrzędnych.

W poniższym przykładzie przyjęto założenie, że rozszerzenie rozwiązania Stackify retrace systemu Linux jest zagnieżdżone w ramach zasobu maszyny wirtualnej. Podczas zagnieżdżania zasobu rozszerzenia kod JSON jest umieszczany w obiekcie "Resources": [] maszyny wirtualnej.

Rozszerzenie wymaga `environment` i `activationKey` .

```json
    {
      "type": "extensions",
      "name": "StackifyExtension",
      "apiVersion": "[variables('apiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.Compute/virtualMachines',variables('vmName'))]"
      ],
      "properties": {
        "publisher": "Stackify.LinuxAgent.Extension",
        "type": "StackifyLinuxAgentExtension",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "environment": "myEnvironment"
        },
        "protectedSettings": {
          "activationKey": "myActivationKey"
        }
      }
    }      
```

Podczas umieszczania kodu JSON rozszerzenia w katalogu głównym szablonu nazwa zasobu zawiera odwołanie do nadrzędnej maszyny wirtualnej, a typ odzwierciedla konfigurację zagnieżdżoną.

```json
    {
        "type": "Microsoft.Compute/virtualMachines/extensions",
        "name": "<parentVmResource>/StackifyExtension",
        "apiVersion": "[variables('apiVersion')]",
        "location": "[resourceGroup().location]",
        "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
        ],
        "properties": {
            "publisher": "Stackify.LinuxAgent.Extension",
            "type": "StackifyLinuxAgentExtension",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
              "environment": "myEnvironment"
            },
            "protectedSettings": {
              "activationKey": "myActivationKey"
            }
        }
    }
```


## <a name="powershell-deployment"></a>Wdrażanie przy użyciu programu PowerShell

Za pomocą `Set-AzVMExtension` polecenia można wdrożyć rozszerzenie maszyny wirtualnej agenta rozwiązania Stackify ponownego śledzenia dla istniejącej maszyny wirtualnej. Przed uruchomieniem polecenia należy zapisać konfigurację publiczną i prywatną w tabeli skrótów programu PowerShell.

Rozszerzenie wymaga `environment` i `activationKey` .

```powershell
$PublicSettings = @{"environment" = "myEnvironment"}
$ProtectedSettings = @{"activationKey" = "myActivationKey"}

Set-AzVMExtension -ExtensionName "Stackify.LinuxAgent.Extension" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Stackify.LinuxAgent.Extension" `
    -ExtensionType "StackifyLinuxAgentExtension" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS `
```

## <a name="azure-cli-deployment"></a>Wdrożenie interfejsu wiersza polecenia platformy Azure 

Narzędzia interfejsu wiersza polecenia platformy Azure można użyć do wdrożenia rozszerzenia maszyny wirtualnej rozwiązania Stackify retrace agenta systemu Linux na istniejącej maszynie wirtualnej.  

Rozszerzenie wymaga `environment` i `activationKey` .

```azurecli
az vm extension set --publisher 'Stackify.LinuxAgent.Extension' --version 1.0 --name 'StackifyLinuxAgentExtension' --protected-settings '{"activationKey":"myActivationKey"}' --settings '{"environment":"myEnvironment"}'  --resource-group 'myResourceGroup' --vm-name 'myVmName'
```

## <a name="troubleshoot-and-support"></a>Rozwiązywanie problemów i pomoc techniczna

### <a name="error-codes"></a>Kody błędów

| Kod błędu | Znaczenie | Możliwa akcja |
| :---: | --- | --- |
| 10 | Błąd instalacji | wget jest wymagany |
| 20 | Błąd instalacji | wymagany jest język Python |
| 30 | Błąd instalacji | sudo jest wymagany |
| 40 | Błąd instalacji | activationKey jest wymagany |
| 51 | Błąd instalacji | Dystrybucji systemu operacyjnego nie jest obsługiwana |
| 60 | Błąd instalacji | środowisko jest wymagane |
| 70 | Błąd instalacji | Nieznane |
| 80 | Włącz błąd | Instalacja usługi nie powiodła się |
| 90 | Włącz błąd | Uruchamianie usługi nie powiodło się |
| 100 | Wyłącz błąd | Zatrzymanie usługi nie powiodło się |
| 110 | Wyłącz błąd | Usuwanie usługi nie powiodło się |
| 120 | Błąd dezinstalacji | Zatrzymanie usługi nie powiodło się |

Jeśli potrzebujesz więcej pomocy, możesz skontaktować się z pomocą techniczną rozwiązania Stackify https://support.stackify.com .