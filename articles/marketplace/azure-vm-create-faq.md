---
title: Często zadawane pytania dotyczące maszyn wirtualnych w portalu Azure Marketplace
description: Często zadawane pytania występujące podczas tworzenia maszyny wirtualnej w portalu Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: guide
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 03/10/2021
ms.openlocfilehash: a74170af61c05d07a189b5ceb61dc0c9b7e14298
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2021
ms.locfileid: "103200420"
---
# <a name="common-questions-about-vm-in-azure-marketplace"></a>Często zadawane pytania dotyczące maszyn wirtualnych w portalu Azure Marketplace

Te często zadawane pytania obejmują typowe problemy, które można napotkać podczas tworzenia oferty maszyny wirtualnej w portalu Azure Marketplace.

## <a name="how-do-i-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>Jak mogę skonfigurować wirtualną sieć prywatną (VPN) do pracy z maszynami wirtualnymi?

Jeśli używasz modelu wdrażania Azure Resource Manager, masz trzy opcje:

- [Tworzenie bramy sieci VPN opartej na trasach za pomocą Azure Portal](../vpn-gateway/tutorial-create-gateway-portal.md)
- [Tworzenie bramy sieci VPN opartej na trasach za pomocą Azure PowerShell](../vpn-gateway/create-routebased-vpn-gateway-powershell.md)
- [Tworzenie bramy sieci VPN opartej na trasach za pomocą interfejsu wiersza polecenia](../vpn-gateway/create-routebased-vpn-gateway-cli.md)

## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>Co to są zasady pomocy technicznej firmy Microsoft dotyczące uruchamiania oprogramowania serwerowego firmy Microsoft na maszynach wirtualnych opartych na platformie Azure?

Szczegóły można znaleźć w [pomocy technicznej oprogramowania serwera firmy Microsoft dla Microsoft Azure maszyn wirtualnych](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).

## <a name="in-a-vm-how-do-i-manage-the-custom-script-extension-in-the-startup-task"></a>Jak zarządzać rozszerzeniem niestandardowego skryptu w maszynie wirtualnej?

Aby uzyskać szczegółowe informacje na temat używania niestandardowego rozszerzenia skryptu przy użyciu modułu Azure PowerShell, szablonów Azure Resource Manager i kroków rozwiązywania problemów w systemach Windows, zobacz [rozszerzenie niestandardowego skryptu dla systemu Windows](../virtual-machines/extensions/custom-script-windows.md).

## <a name="are-32-bit-applications-or-services-supported-in-azure-marketplace"></a>Czy obsługiwane są 32-bitowe aplikacje lub usługi w portalu Azure Marketplace?

Nie. Obsługiwane systemy operacyjne i standardowe usługi dla maszyn wirtualnych platformy Azure to wszystkie 64-bitowe. Chociaż większość 64-bitowych systemów operacyjnych obsługuje 32-bitowe wersje aplikacji w celu zapewnienia zgodności z poprzednimi wersjami, używanie aplikacji 32-bitowych jako części rozwiązania maszyny wirtualnej jest nieobsługiwane i zdecydowanie odradzane. Utwórz ponownie aplikację jako projekt 64-bitowy.

Więcej informacji można znaleźć w następujących artykułach:

- [Uruchamianie aplikacji 32-bitowych](/windows/desktop/WinProg64/running-32-bit-applications)
- [Obsługa 32-bitowych systemów operacyjnych na maszynach wirtualnych platformy Azure](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)
- [Pomoc techniczna dotycząca oprogramowania serwerowego firmy Microsoft dla maszyn wirtualnych platformy Microsoft Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)

## <a name="error-vhd-is-already-registered-with-image-repository-as-the-resource"></a>Błąd: wirtualny dysk twardy jest już zarejestrowany w repozytorium obrazów jako zasób

Za każdym razem, gdy próbuję utworzyć obraz z moich wirtualnych dysków twardych, otrzymuję komunikat o błędzie "wirtualny dysk twardy jest już zarejestrowany w repozytorium obrazów jako zasób" w Azure PowerShell. Nie utworzono żadnego obrazu wcześniej ani nie znaleziono żadnego obrazu o tej nazwie na platformie Azure. Jak rozwiązać ten problem?

Ten problem zwykle występuje, gdy utworzono maszynę wirtualną z dysku VHD, który ma blokadę. Upewnij się, że maszyna wirtualna nie została przypisana z tego wirtualnego dysku twardego, a następnie spróbuj ponownie wykonać operację. Jeśli ten problem będzie się powtarzać, Otwórz bilet pomocy technicznej. Zobacz [Pomoc techniczna dla Centrum partnerskiego](support.md).

## <a name="how-do-i-create-a-vm-from-a-generalized-vhd"></a>Jak mogę utworzyć maszynę wirtualną na podstawie uogólnionego wirtualnego dysku twardego?

### <a name="prepare-an-azure-resource-manager-template"></a>Przygotowywanie szablonu Azure Resource Manager

W tej sekcji opisano sposób tworzenia i wdrażania obrazu maszyny wirtualnej (VM) dostarczonej przez użytkownika. Można to zrobić przez udostępnienie obrazów dysków VHD systemu operacyjnego i danych z wirtualnego dysku twardego wdrożonego na platformie Azure. W tych krokach wdrożono maszynę wirtualną przy użyciu uogólnionego wirtualnego dysku twardego.

1. Zaloguj się do witryny Azure Portal.
2. Przekaż uogólniony wirtualny dysk twardy systemu operacyjnego i dyski danych do konta usługi Azure Storage.
3. Na stronie głównej wybierz pozycję Utwórz zasób, Wyszukaj pozycję "wdrożenie szablonu" i wybierz pozycję Utwórz.
4. Wybierz opcję Kompiluj własny szablon w edytorze.

   :::image type="content" source="media/vm/template-deployment.png" alt-text="Pokazuje wybór szablonu":::

5. Wklej następujący szablon JSON do edytora i wybierz pozycję Zapisz.
 ```json
  {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
           "userStorageAccountName": {
               "type": "String"
           },
           "userStorageContainerName": {
               "defaultValue": "vhds",
               "type": "String"
           },
           "dnsNameForPublicIP": {
               "type": "String"
           },
           "adminUserName": {
               "defaultValue": "isv",
               "type": "String"
           },
           "adminPassword": {
               "defaultValue": "",
               "type": "SecureString"
           },
           "osType": {
               "defaultValue": "windows",
               "allowedValues": [
                   "windows",
                   "linux"
               ],
               "type": "String"
           },
           "subscriptionId": {
               "type": "String"
           },
           "location": {
               "type": "String"
           },
           "vmSize": {
               "type": "String"
           },
           "publicIPAddressName": {
               "type": "String"
           },
           "vmName": {
               "type": "String"
           },
           "virtualNetworkName": {
               "type": "String"
           },
           "nicName": {
               "type": "String"
           },
           "vhdUrl": {
               "type": "String",
               "metadata": {
                   "description": "VHD Url..."
               }
           }
       },
       "variables": {
           "addressPrefix": "10.0.0.0/16",
           "subnet1Name": "Subnet-1",
           "subnet2Name": "Subnet-2",
           "subnet1Prefix": "10.0.0.0/24",
           "subnet2Prefix": "10.0.1.0/24",
           "publicIPAddressType": "Dynamic",
           "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
           "subnet1Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]",
           "hostDNSNameScriptArgument": "[concat(parameters('dnsNameForPublicIP'),'.',parameters('location'),'.cloudapp.azure.com')]",
           "osDiskVhdName": "[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]"
       },
       "resources": [
           {
               "type": "Microsoft.Network/publicIPAddresses",
               "apiVersion": "2015-06-15",
               "name": "[parameters('publicIPAddressName')]",
               "location": "[parameters('location')]",
               "properties": {
                   "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
                   "dnsSettings": {
                       "domainNameLabel": "[parameters('dnsNameForPublicIP')]"
                   }
               }
           },
           {
               "type": "Microsoft.Network/virtualNetworks",
               "apiVersion": "2015-06-15",
               "name": "[parameters('virtualNetworkName')]",
               "location": "[parameters('location')]",
               "properties": {
                   "addressSpace": {
                       "addressPrefixes": [
                           "[variables('addressPrefix')]"
                       ]
                   },
                   "subnets": [
                       {
                           "name": "[variables('subnet1Name')]",
                           "properties": {
                               "addressPrefix": "[variables('subnet1Prefix')]"
                           }
                       },
                       {
                           "name": "[variables('subnet2Name')]",
                           "properties": {
                               "addressPrefix": "[variables('subnet2Prefix')]"
                           }
                       }
                   ]
               }
           },
           {
               "type": "Microsoft.Network/networkInterfaces",
               "apiVersion": "2015-06-15",
               "name": "[parameters('nicName')]",
               "location": "[parameters('location')]",
               "dependsOn": [
                   "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]",
                   "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]"
               ],
               "properties": {
                   "ipConfigurations": [
                       {
                           "name": "ipconfig1",
                           "properties": {
                               "privateIPAllocationMethod": "Dynamic",
                               "publicIPAddress": {
                                   "id": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]"
                               },
                               "subnet": {
                                   "id": "[variables('subnet1Ref')]"
                               }
                           }
                       }
                   ]
               }
           },
           {
               "type": "Microsoft.Compute/virtualMachines",
               "apiVersion": "2015-06-15",
               "name": "[parameters('vmName')]",
               "location": "[parameters('location')]",
               "dependsOn": [
                   "[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]"
               ],
               "properties": {
                   "hardwareProfile": {
                       "vmSize": "[parameters('vmSize')]"
                   },
                   "osProfile": {
                       "computername": "[parameters('vmName')]",
                       "adminUsername": "[parameters('adminUsername')]",
                       "adminPassword": "[parameters('adminPassword')]"
                   },
                   "storageProfile": {
                       "osDisk": {
                           "name": "[concat(parameters('vmName'),'-osDisk')]",
                           "osType": "[parameters('osType')]",
                           "caching": "ReadWrite",
                           "image": {
                               "uri": "[parameters('vhdUrl')]"
                           },
                           "vhd": {
                               "uri": "[variables('osDiskVhdName')]"
                           },
                           "createOption": "FromImage"
                       }
                   },
                   "networkProfile": {
                       "networkInterfaces": [
                           {
                               "id": "[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
                           }
                       ]
                   }
               }
           }
       ]
   }
   ```


6. Podaj wartości parametrów dla wyświetlanych stron właściwości wdrożenia niestandardowego.

 **TABELA 1**

| **ResourceGroupName** | **Istniejąca nazwa grupy zasobów platformy Azure. Zazwyczaj należy używać tego samego RG, co Magazyn kluczy.** |
| --- | --- |
| TemplateFile | Pełna nazwa ścieżki do pliku VHDtoImage.jsna. |
| userStorageAccountName | Nazwa konta magazynu. |
| dnsNameForPublicIP | Nazwa DNS publicznego adresu IP; musi być małymi literami. |
| subscriptionId | Identyfikator subskrypcji platformy Azure. |
| Lokalizacja | Standardowa lokalizacja geograficzna platformy Azure grupy zasobów. |
| vmName | Nazwa maszyny wirtualnej. |
| vhdUrl | Adres internetowy wirtualnego dysku twardego. |
| vmSize | Rozmiar wystąpienia maszyny wirtualnej. |
| publicIPAddressName | Nazwa publicznego adresu IP. |
| virtualNetworkName | Nazwa sieci wirtualnej. |
| nicName | Nazwa karty sieciowej sieci wirtualnej. |
| adminUserName | Nazwa użytkownika konta administratora. |
| adminPassword | Hasło administratora. |
|

7. Po podaniu tych wartości wybierz pozycję Kup.

Platforma Azure rozpocznie wdrażanie. Tworzy nową maszynę wirtualną z określonym niezarządzanym wirtualnym dyskiem twardym w określonej ścieżce konta magazynu. Postęp można śledzić w Azure Portal, wybierając pozycję Virtual Machines po lewej stronie portalu. Po utworzeniu maszyny wirtualnej stan zmieni się z Start na uruchomiony.

W przypadku wdrożenia maszyny wirtualnej generacji 2 Użyj tego szablonu:
 ```json
 {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
          "userStorageAccountName": {
              "type": "String"
          },
          "userStorageContainerName": {
              "defaultValue": "vhds",
              "type": "String"
          },
          "dnsNameForPublicIP": {
              "type": "String"
          },
          "adminUserName": {
              "defaultValue": "isv",
              "type": "String"
          },
          "adminPassword": {
              "defaultValue": "",
              "type": "SecureString"
          },
          "osType": {
              "defaultValue": "windows",
              "allowedValues": [
                  "windows",
                  "linux"
              ],
              "type": "String"
          },
          "subscriptionId": {
              "type": "String"
          },
          "location": {
              "type": "String"
          },
          "vmSize": {
              "type": "String"
          },
          "publicIPAddressName": {
              "type": "String"
          },
          "vmName": {
              "type": "String"
          },
          "virtualNetworkName": {
              "type": "String"
          },
          "nicName": {
              "type": "String"
          },
          "vhdUrl": {
              "type": "String",
              "metadata": {
                  "description": "VHD Url..."
              }
          }
      },
      "variables": {
          "addressPrefix": "10.0.0.0/16",
          "subnet1Name": "Subnet-1",
          "subnet2Name": "Subnet-2",
          "subnet1Prefix": "10.0.0.0/24",
          "subnet2Prefix": "10.0.1.0/24",
          "publicIPAddressType": "Dynamic",
          "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
          "subnet1Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]",
          "hostDNSNameScriptArgument": "[concat(parameters('dnsNameForPublicIP'),'.',parameters('location'),'.cloudapp.azure.com')]",
          "osDiskVhdName": "[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]"
      },
      "resources": [
          {
              "type": "Microsoft.Network/publicIPAddresses",
              "apiVersion": "2015-06-15",
              "name": "[parameters('publicIPAddressName')]",
              "location": "[parameters('location')]",
              "properties": {
                  "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
                  "dnsSettings": {
                      "domainNameLabel": "[parameters('dnsNameForPublicIP')]"
                  }
              }
          },
          {
              "type": "Microsoft.Network/virtualNetworks",
              "apiVersion": "2015-06-15",
              "name": "[parameters('virtualNetworkName')]",
              "location": "[parameters('location')]",
              "properties": {
                  "addressSpace": {
                      "addressPrefixes": [
                          "[variables('addressPrefix')]"
                      ]
                  },
                  "subnets": [
                      {
                          "name": "[variables('subnet1Name')]",
                          "properties": {
                              "addressPrefix": "[variables('subnet1Prefix')]"
                          }
                      },
                      {
                          "name": "[variables('subnet2Name')]",
                          "properties": {
                              "addressPrefix": "[variables('subnet2Prefix')]"
                          }
                      }
                  ]
              }
          },
          {
              "type": "Microsoft.Network/networkInterfaces",
              "apiVersion": "2015-06-15",
              "name": "[parameters('nicName')]",
              "location": "[parameters('location')]",
              "dependsOn": [
                  "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]",
                  "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]"
              ],
              "properties": {
                  "ipConfigurations": [
                      {
                          "name": "ipconfig1",
                          "properties": {
                              "privateIPAllocationMethod": "Dynamic",
                              "publicIPAddress": {
                                  "id": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]"
                              },
                              "subnet": {
                                  "id": "[variables('subnet1Ref')]"
                              }
                          }
                      }
                  ]
              }
          },
          {
              "type": "Microsoft.Compute/virtualMachines",
              "apiVersion": "2015-06-15",
              "name": "[parameters('vmName')]",
              "location": "[parameters('location')]",
              "dependsOn": [
                  "[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]"
              ],
              "properties": {
                  "hardwareProfile": {
                      "vmSize": "[parameters('vmSize')]"
                  },
                  "osProfile": {
                      "computername": "[parameters('vmName')]",
                      "adminUsername": "[parameters('adminUsername')]",
                      "adminPassword": "[parameters('adminPassword')]"
                  },
                  "storageProfile": {
                      "osDisk": {
                          "name": "[concat(parameters('vmName'),'-osDisk')]",
                          "osType": "[parameters('osType')]",
                          "caching": "ReadWrite",
                          "image": {
                              "uri": "[parameters('vhdUrl')]"
                          },
                          "vhd": {
                              "uri": "[variables('osDiskVhdName')]"
                          },
                          "createOption": "FromImage"
                      }
                  },
                  "networkProfile": {
                      "networkInterfaces": [
                          {
                              "id": "[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
                          }
                      ]
                  }
              }
          }
      ]
  }
  ```


### <a name="deploy-an-azure-vm-using-powershell"></a>Wdrażanie maszyny wirtualnej platformy Azure przy użyciu programu PowerShell

Skopiuj i edytuj następujący skrypt, aby podać wartości `$storageaccount` `$vhdUrl` zmiennych i. Wykonaj tę operację, aby utworzyć zasób maszyny wirtualnej platformy Azure na podstawie istniejącego uogólnionego wirtualnego dysku twardego.

```powershell
# storage account of existing generalized VHD
$storageaccount = "testwinrm11815"
# generalized VHD URL
$vhdUrl = "https://testwinrm11815.blob.core.windows.net/vhds/testvm1234562016651857.vhd"

echo "New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl
$objAzureKeyVaultSecret.Id -vhdUrl "$vhdUrl" -vmSize "Standard\_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd"

# deploying VM with existing VHD
New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName"
```


## <a name="next-steps"></a>Następne kroki

- [Rozwiązywanie problemów z certyfikacją maszyn wirtualnych](azure-vm-create-certification-faq.md)
