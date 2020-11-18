---
title: skalowanie klastra usługi Service Fabric na platformie Azure
description: W tym samouczku dowiesz się, jak skalować klaster Service Fabric na platformie Azure i w systemie oraz jak czyścić pozostałe zasoby.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 7f92ca28afd9d1894867aaa2c18df3a02ee0bd79
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94842689"
---
# <a name="tutorial-scale-a-service-fabric-cluster-in-azure"></a>Samouczek: skalowanie klastra usługi Service Fabric na platformie Azure

Ten samouczek jest trzecią częścią serii i pokazuje, jak skalować istniejący klaster poza i w programie. Ukończenie tego samouczka pozwoli Ci uzyskać wiedzę na temat skalowania klastra i czyszczenia pozostałych zasobów.  Aby uzyskać więcej informacji na temat skalowania klastra działającego na platformie Azure, Przeczytaj [Service Fabric skalowanie klastrów](service-fabric-cluster-scaling.md).

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Dodawanie i usuwanie węzłów (skalowanie w poziomie i skalowanie w górę)
> * Dodawanie i usuwanie typów węzłów (skalowanie w poziomie i skalowanie w górę)
> * Zwiększ zasoby węzła (Skaluj w górę)

Ta seria samouczków zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Tworzenie bezpiecznego [klastra systemu Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) na platformie Azure przy użyciu szablonu
> * [Monitorowanie klastra](service-fabric-tutorial-monitor-cluster.md)
> * Skalowanie klastra w poziomie lub w pionie
> * [Uaktualnianie środowiska uruchomieniowego klastra](service-fabric-tutorial-upgrade-cluster.md)
> * [Usuwanie klastra](service-fabric-tutorial-delete-cluster.md)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka:

* Jeśli nie masz subskrypcji platformy Azure, Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Zainstaluj [Azure PowerShell](/powershell/azure/install-az-ps) lub [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).
* Tworzenie bezpiecznego [klastra systemu Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) na platformie Azure

## <a name="important-considerations-and-guidelines"></a>Ważne zagadnienia i wytyczne

Obciążenia aplikacji zmieniają się z upływem czasu, czy istniejące usługi potrzebują więcej zasobów (lub mniej)?  [Dodaj lub Usuń węzły](#add-nodes-to-or-remove-nodes-from-a-node-type) z typu węzła, aby zwiększyć lub zmniejszyć zasoby klastra.

Czy musisz dodać więcej niż 100 węzłów do klastra?  Jeden Service Fabric typu węzła/zestawu skalowania nie może zawierać więcej niż 100 węzłów/maszyn wirtualnych.  Aby skalować klaster poza 100 węzłów, [Dodaj dodatkowe typy węzłów](#add-nodes-to-or-remove-nodes-from-a-node-type).

Czy Twoja aplikacja ma wiele usług i czy wszystkie z nich muszą być dostępne publicznie czy z Internetu?  Typowe aplikacje zawierają usługę bramy frontonu, która odbiera dane wejściowe od klienta i co najmniej jednej usługi zaplecza, która komunikuje się z usługami frontonu. W takim przypadku zalecamy dodanie do klastra [co najmniej dwóch typów węzłów](#add-nodes-to-or-remove-nodes-from-a-node-type) .  

Czy usługi mają różne potrzeby związane z infrastrukturą, takie jak większa ilość pamięci RAM czy więcej cykli procesora CPU? Na przykład aplikacja zawiera usługę frontonu i usługę zaplecza. Usługa frontonu może być uruchamiana na mniejszych maszynach wirtualnych (takich jak D2), które mają otwarte porty w Internecie. Usługa zaplecza, jednak jest intensywnie obliczeniowa i musi działać na większych maszynach wirtualnych (z rozmiarami maszyn wirtualnych takimi jak D4, D6, D15), które nie są połączone z Internetem. W tym przypadku zalecamy [dodanie dwóch lub więcej typów węzłów](#add-nodes-to-or-remove-nodes-from-a-node-type) do klastra. Dzięki temu każdy typ węzła może mieć odrębne właściwości, takie jak łączność z Internetem lub rozmiar maszyny wirtualnej. Liczba maszyn wirtualnych może być również skalowana niezależnie.

Podczas skalowania klastra platformy Azure należy pamiętać o następujących kwestiach:

* Jeden Service Fabric typu węzła/zestawu skalowania nie może zawierać więcej niż 100 węzłów/maszyn wirtualnych.  Aby skalować klaster poza 100 węzłów, Dodaj dodatkowe typy węzłów.
* Typy węzła podstawowego z uruchomionymi obciążeniami produkcyjnymi powinny mieć [poziom trwałości][durability] Gold lub Silver i zawsze mieć pięć lub więcej węzłów.
* Typy węzłów innych niż podstawowe działające stanowe obciążenia produkcyjne powinny mieć zawsze pięć lub więcej węzłów.
* Typy węzłów innych niż podstawowe, które działają bezstanowe obciążenia produkcyjne, powinny zawsze mieć co najmniej dwa węzły.
* Każdy typ węzła [poziomu trwałości][durability] Gold lub Silver powinien mieć zawsze pięć lub więcej węzłów.
* Jeśli skalowanie w (usuwanie węzłów z) typu węzła podstawowego, nigdy nie należy zmniejszać liczby wystąpień do wartości mniejszej niż wymagana przez [poziom niezawodności][reliability] .

Aby uzyskać więcej informacji, zapoznaj się z tematem [wskazówki dotyczące pojemności klastra](service-fabric-cluster-capacity.md).

## <a name="export-the-template-for-the-resource-group"></a>Eksportowanie szablonu dla grupy zasobów

Po pomyślnym utworzeniu bezpiecznego [klastra systemu Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) i skonfigurowaniu grupy zasobów wyeksportuj szablon Menedżer zasobów dla grupy zasobów. Eksportowanie szablonu pozwala zautomatyzować przyszłe wdrożenia klastra i jego zasobów, ponieważ szablon zawiera całą kompletną infrastrukturę.  Aby uzyskać więcej informacji na temat eksportowania szablonów, zobacz [Zarządzanie grupami zasobów Azure Resource Manager przy użyciu Azure Portal](../azure-resource-manager/management/manage-resource-groups-portal.md).

1. W [Azure Portal](https://portal.azure.com)przejdź do grupy zasobów zawierającej klaster (**sfclustertutorialgroup**, jeśli korzystasz z tego samouczka). 

2. W lewym okienku wybierz pozycję **wdrożenia** lub wybierz link w obszarze **wdrożenia**. 

3. Z listy wybierz najnowsze pomyślne wdrożenie.

4. W lewym okienku wybierz pozycję **szablon** , a następnie wybierz pozycję **Pobierz** , aby wyeksportować szablon jako plik zip.  Zapisz szablon i parametry na komputerze lokalnym.

## <a name="add-nodes-to-or-remove-nodes-from-a-node-type"></a>Dodawanie węzłów do lub usuwanie węzłów z typu węzła

Skalowanie do wewnątrz i na zewnątrz lub skalowanie w poziomie powoduje zmianę liczby węzłów w klastrze. Skalowanie w poziomie lub na zewnątrz polega na dodaniu większej liczby wystąpień maszyn wirtualnych do zestawu skalowania. Wystąpienia te stają się węzłami używanymi przez usługę Service Fabric. Usługa Service Fabric wykrywa zwiększenie liczby wystąpień (efekt skalowania w poziomie) i reaguje automatycznie. Klaster można skalować w dowolnym momencie, nawet w przypadku uruchamiania obciążeń w klastrze.

### <a name="update-the-template"></a>Aktualizowanie szablonu

[Wyeksportuj plik szablonu i parametrów](#export-the-template-for-the-resource-group) z grupy zasobów dla najnowszego wdrożenia.  Otwórz *parameters.js* pliku.  Jeśli klaster został wdrożony przy użyciu [przykładowego szablonu][template] w tym samouczku, istnieją trzy typy węzłów w klastrze i trzy parametry ustawiające liczbę węzłów dla każdego typu węzła: *nt0InstanceCount*, *nt1InstanceCount* i *nt2InstanceCount*.  Parametr *nt1InstanceCount* , na przykład, ustawia liczbę wystąpień dla drugiego typu węzła, a następnie ustawia liczba maszyn wirtualnych w skojarzonym zestawie skalowania maszyn wirtualnych.

Aby więc zaktualizować wartość *nt1InstanceCount* , należy zmienić liczbę węzłów w drugim typie węzła.  Należy pamiętać, że nie można skalować typu węzła do więcej niż 100 węzłów.  Typy węzłów innych niż podstawowe działające stanowe obciążenia produkcyjne powinny mieć zawsze pięć lub więcej węzłów. Typy węzłów innych niż podstawowe, które działają bezstanowe obciążenia produkcyjne, powinny zawsze mieć co najmniej dwa węzły.

W przypadku skalowania w programie usuwanie węzłów z, typ węzła na [poziomie trwałości][durability] brony, należy [ręcznie usunąć stan tych węzłów](service-fabric-cluster-scale-in-out.md#manually-remove-vms-from-a-node-typevirtual-machine-scale-set).  W przypadku warstwy trwałości Silver i Gold te kroki są wykonywane automatycznie przez platformę.

### <a name="deploy-the-updated-template"></a>Wdróż zaktualizowany szablon
Zapisz wszelkie zmiany *template.js* i *parameters.jsna* plikach.  Aby wdrożyć zaktualizowany szablon, uruchom następujące polecenie:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName sfclustertutorialgroup -TemplateFile c:\temp\template.json -TemplateParameterFile c:\temp\parameters.json -Name "ChangingInstanceCount"
```
Lub następujące polecenie interfejsu wiersza polecenia platformy Azure:
```azurecli
az group deployment create --resource-group sfclustertutorialgroup --template-file c:\temp\template.json --parameters c:\temp\parameters.json
```

## <a name="add-a-node-type-to-the-cluster"></a>Dodawanie typu węzła do klastra

Każdy typ węzła zdefiniowany w klastrze Service Fabric uruchomionym na platformie Azure jest ustawiany jako [oddzielny zestaw skalowania maszyn wirtualnych](service-fabric-cluster-nodetypes.md). Każdy typ węzła może być następnie zarządzany osobno. Można niezależnie skalować każdy typ węzła w górę lub w dół, mieć otwarte różne zestawy portów i korzystać z różnych metryk pojemności. Można również niezależnie zmienić jednostkę SKU systemu operacyjnego działającą w każdym węźle klastra, ale należy pamiętać, że w przykładowym klastrze nie można używać kombinacji systemów Windows i Linux. Typ pojedynczego węzła/zestaw skalowania nie może zawierać więcej niż 100 węzłów.  Klaster można skalować w poziomie do więcej niż 100 węzłów przez dodanie dodatkowych typów węzłów/zestawów skalowania. Klaster można skalować w dowolnym momencie, nawet w przypadku uruchamiania obciążeń w klastrze.

### <a name="update-the-template"></a>Aktualizowanie szablonu

[Wyeksportuj plik szablonu i parametrów](#export-the-template-for-the-resource-group) z grupy zasobów dla najnowszego wdrożenia.  Otwórz *parameters.js* pliku.  Jeśli klaster został wdrożony przy użyciu [przykładowego szablonu][template] w tym samouczku, w klastrze istnieją trzy typy węzłów.  W tej sekcji należy dodać czwarty typ węzła przez zaktualizowanie i wdrożenie szablonu Menedżer zasobów. 

Oprócz nowego typu węzła, należy również dodać skojarzony zestaw skalowania maszyn wirtualnych (który jest uruchamiany w oddzielnej podsieci sieci wirtualnej) i sieciowej grupy zabezpieczeń.  Możesz dodać nowy lub istniejący publiczny adres IP i zasoby modułu równoważenia obciążenia platformy Azure dla nowego zestawu skalowania.  Nowy typ węzła ma [poziom trwałości][durability] o wartości Silver i size "Standard_D2_V2".

W *template.jsna* pliku Dodaj następujące nowe parametry:
```json
"nt3InstanceCount": {
    "defaultValue": 5,
    "type": "Int",
    "metadata": {
        "description": "Instance count for node type"
    }
},
"vmNodeType3Size": {
    "defaultValue": "Standard_D2_V2",
    "type": "String"
},
```

W *template.jsna* pliku Dodaj następujące nowe zmienne:
```json
"lbID3": "[resourceId('Microsoft.Network/loadBalancers',concat('LB','-', parameters('clusterName'),'-',variables('vmNodeType3Name')))]",
"lbIPConfig3": "[concat(variables('lbID3'),'/frontendIPConfigurations/LoadBalancerIPConfig')]",
"lbPoolID3": "[concat(variables('lbID3'),'/backendAddressPools/LoadBalancerBEAddressPool')]",
"lbProbeID3": "[concat(variables('lbID3'),'/probes/FabricGatewayProbe')]",
"lbHttpProbeID3": "[concat(variables('lbID3'),'/probes/FabricHttpGatewayProbe')]",
"lbNatPoolID3": "[concat(variables('lbID3'),'/inboundNatPools/LoadBalancerBEAddressNatPool')]",
"vmNodeType3Name": "[toLower(concat('NT4', variables('vmName')))]",
"vmStorageAccountName3": "[toLower(concat(uniqueString(resourceGroup().id), '1', '3' ))]",
"nt3applicationStartPort": "20000",
"nt3applicationEndPort": "30000",
"nt3ephemeralStartPort": "49152",
"nt3ephemeralEndPort": "65534",
"nt3fabricTcpGatewayPort": "19000",
"nt3fabricHttpGatewayPort": "19080",
"nt3reverseProxyEndpointPort": "19081",
"subnet3Name": "Subnet-3",
"subnet3Prefix": "10.0.3.0/24",
"subnet3Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet3Name'))]",
```

W *template.jsw* pliku Dodaj nową podsieć do zasobu sieci wirtualnej:
```json
{
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[variables('virtualNetworkName')]",
    "apiVersion": "2018-08-01",
    "location": "[variables('computeLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "addressSpace": {
            "addressPrefixes": [
                "[variables('addressPrefix')]"
            ]
        },
        "subnets": [
            ...
            {
                "name": "[variables('subnet3Name')]",
                "properties": {
                    "addressPrefix": "[variables('subnet3Prefix')]",
                    "networkSecurityGroup": {
                        "id": "[resourceId('Microsoft.Network/networkSecurityGroups', concat('nsg', variables('subnet3Name')))]"
                    }
                }
            }
        ]
    },
    "dependsOn": [
        ...
        "[concat('Microsoft.Network/networkSecurityGroups/', concat('nsg', variables('subnet3Name')))]"
    ]
},
```

W *template.jsna* pliku Dodaj nowy publiczny adres IP i zasoby modułu równoważenia obciążenia:
```json
{
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[concat(variables('lbIPName'),'-',variables('vmNodeType3Name'))]",
    "apiVersion": "2018-08-01",
    "location": "[variables('computeLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "dnsSettings": {
            "domainNameLabel": "[concat(variables('dnsName'),'-','nt4')]"
        },
        "publicIPAllocationMethod": "Dynamic"
    }
},
        {
    "type": "Microsoft.Network/loadBalancers",
    "name": "[concat('LB','-', parameters('clusterName'),'-',variables('vmNodeType3Name'))]",
    "apiVersion": "2018-08-01",
    "location": "[variables('computeLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "frontendIPConfigurations": [
            {
                "name": "LoadBalancerIPConfig",
                "properties": {
                    "publicIPAddress": {
                        "id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(variables('lbIPName'),'-',variables('vmNodeType3Name')))]"
                    }
                }
            }
        ],
        "backendAddressPools": [
            {
                "name": "LoadBalancerBEAddressPool",
                "properties": {}
            }
        ],
        "loadBalancingRules": [
            {
                "name": "LBRule",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID3')]"
                    },
                    "backendPort": "[variables('nt3fabricTcpGatewayPort')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPort": "[variables('nt3fabricTcpGatewayPort')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[variables('lbProbeID3')]"
                    },
                    "protocol": "tcp"
                }
            },
            {
                "name": "LBHttpRule",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID3')]"
                    },
                    "backendPort": "[variables('nt3fabricHttpGatewayPort')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPort": "[variables('nt3fabricHttpGatewayPort')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[variables('lbHttpProbeID3')]"
                    },
                    "protocol": "tcp"
                }
            },
            {
                "name": "AppPortLBRule1",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID3')]"
                    },
                    "backendPort": "[parameters('loadBalancedAppPort1')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPort": "[parameters('loadBalancedAppPort1')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[concat(variables('lbID3'),'/probes/AppPortProbe1')]"
                    },
                    "protocol": "tcp"
                }
            },
            {
                "name": "AppPortLBRule2",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID3')]"
                    },
                    "backendPort": "[parameters('loadBalancedAppPort2')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPort": "[parameters('loadBalancedAppPort2')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[concat(variables('lbID3'),'/probes/AppPortProbe2')]"
                    },
                    "protocol": "tcp"
                }
            }
        ],
        "probes": [
            {
                "name": "FabricGatewayProbe",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port": "[variables('nt3fabricTcpGatewayPort')]",
                    "protocol": "tcp"
                }
            },
            {
                "name": "FabricHttpGatewayProbe",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port": "[variables('nt3fabricHttpGatewayPort')]",
                    "protocol": "tcp"
                }
            },
            {
                "name": "AppPortProbe1",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port": "[parameters('loadBalancedAppPort1')]",
                    "protocol": "tcp"
                }
            },
            {
                "name": "AppPortProbe2",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port": "[parameters('loadBalancedAppPort2')]",
                    "protocol": "tcp"
                }
            }
        ],
        "inboundNatPools": [
            {
                "name": "LoadBalancerBEAddressNatPool",
                "properties": {
                    "backendPort": "3389",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPortRangeEnd": "4500",
                    "frontendPortRangeStart": "3389",
                    "protocol": "tcp"
                }
            }
        ]
    },
    "dependsOn": [
        "[concat('Microsoft.Network/publicIPAddresses/',concat(variables('lbIPName'),'-',variables('vmNodeType3Name')))]"
    ]
},
```

W *template.jsna* pliku Dodaj nową grupę zabezpieczeń sieci i zasoby zestawu skalowania maszyn wirtualnych.  Właściwość NodeTypeRef we właściwościach rozszerzenia Service Fabric zestawu skalowania maszyn wirtualnych mapuje określony typ węzła do zestawu skalowania.

```json
{
    "type": "Microsoft.Network/networkSecurityGroups",
    "name": "[concat('nsg', variables('subnet3Name'))]",
    "apiVersion": "2018-08-01",
    "location": "[resourceGroup().location]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "securityRules": [
            {
                "name": "allowSvcFabSMB",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "445",
                    "direction": "Inbound",
                    "priority": 3950,
                    "protocol": "*",
                    "sourceAddressPrefix": "VirtualNetwork",
                    "sourcePortRange": "*",
                    "description": "allow SMB traffic within the net, used by fabric to move packages around"
                }
            },
            {
                "name": "allowSvcFabCluser",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "1025-1027",
                    "direction": "Inbound",
                    "priority": 3920,
                    "protocol": "*",
                    "sourceAddressPrefix": "VirtualNetwork",
                    "sourcePortRange": "*",
                    "description": "allow ports within vnet that are used by the fabric to talk between nodes"
                }
            },
            {
                "name": "allowSvcFabEphemeral",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[concat(variables('nt3ephemeralStartPort'), '-', variables('nt3ephemeralEndPort'))]",
                    "direction": "Inbound",
                    "priority": 3930,
                    "protocol": "*",
                    "sourceAddressPrefix": "VirtualNetwork",
                    "sourcePortRange": "*",
                    "description": "allow fabric ephemeral ports within the vnet"
                }
            },
            {
                "name": "allowSvcFabPortal",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[variables('nt3fabricHttpGatewayPort')]",
                    "direction": "Inbound",
                    "priority": 3900,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow port used to access the fabric cluster web portal"
                }
            },
            {
                "name": "allowSvcFabClient",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[variables('nt3fabricTcpGatewayPort')]",
                    "direction": "Inbound",
                    "priority": 3910,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow port used by the fabric client (includes powershell)"
                }
            },
            {
                "name": "allowSvcFabApplication",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[concat(variables('nt3applicationStartPort'), '-', variables('nt3applicationEndPort'))]",
                    "direction": "Inbound",
                    "priority": 3940,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow fabric application ports within the vnet"
                }
            },
            {
                "name": "blockAll",
                "properties": {
                    "access": "Deny",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "*",
                    "direction": "Inbound",
                    "priority": 4095,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "block all traffic except what we've explicitly allowed"
                }
            },
            {
                "name": "allowVNetRDP",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "3389",
                    "direction": "Inbound",
                    "priority": 3960,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow RDP within the net"
                }
            },
            {
                "name": "allowSvcFabReverseProxy",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[variables('nt3reverseProxyEndpointPort')]",
                    "direction": "Inbound",
                    "priority": 3980,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow port used to access the fabric cluster using reverse proxy"
                }
            },
            {
                "name": "allowAppPort1",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[parameters('loadBalancedAppPort1')]",
                    "direction": "Inbound",
                    "priority": 2001,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow public application port 1"
                }
            },
            {
                "name": "allowAppPort2",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[parameters('loadBalancedAppPort2')]",
                    "direction": "Inbound",
                    "priority": 2002,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow public application port 2"
                }
            }
        ]
    }
},
{
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "sku": {
        "name": "[parameters('vmNodeType3Size')]",
        "capacity": "[parameters('nt3InstanceCount')]",
        "tier": "Standard"
    },
    "name": "[variables('vmNodeType3Name')]",
    "apiVersion": "2018-10-01",
    "location": "[variables('computeLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "overprovision": "[variables('overProvision')]",
        "upgradePolicy": {
            "mode": "Automatic"
        },
        "virtualMachineProfile": {
            "extensionProfile": {
                "extensions": [
                    {
                        "name": "[concat(variables('vmNodeType3Name'),'OMS')]",
                        "properties": {
                            "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                            "type": "MicrosoftMonitoringAgent",
                            "typeHandlerVersion": "1.0",
                            "autoUpgradeMinorVersion": true,
                            "settings": {
                                "workspaceId": "[reference(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')), '2015-11-01-preview').customerId]"
                            },
                            "protectedSettings": {
                                "workspaceKey": "[listKeys(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')),'2015-11-01-preview').primarySharedKey]"
                            }
                        }
                    },
                    {
                        "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType3Name')]",
                        "properties": {
                            "type": "ServiceFabricNode",
                            "autoUpgradeMinorVersion": true,
                            "protectedSettings": {
                                "StorageAccountKey1": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key1]",
                                "StorageAccountKey2": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key2]"
                            },
                            "publisher": "Microsoft.Azure.ServiceFabric",
                            "settings": {
                                "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                                "nodeTypeRef": "[variables('vmNodeType3Name')]",
                                "dataPath": "D:\\SvcFab",
                                "durabilityLevel": "Silver",
                                "enableParallelJobs": true,
                                "nicPrefixOverride": "[variables('subnet3Prefix')]",
                                "certificate": {
                                    "thumbprint": "[parameters('certificateThumbprint')]",
                                    "x509StoreName": "[parameters('certificateStoreValue')]"
                                }
                            },
                            "typeHandlerVersion": "1.0"
                        }
                    },
                    {
                        "name": "[concat('VMDiagnosticsVmExt','_vmNodeType3Name')]",
                        "properties": {
                            "type": "IaaSDiagnostics",
                            "autoUpgradeMinorVersion": true,
                            "protectedSettings": {
                                "storageAccountName": "[variables('applicationDiagnosticsStorageAccountName')]",
                                "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
                                "storageAccountEndPoint": "https://core.windows.net/"
                            },
                            "publisher": "Microsoft.Azure.Diagnostics",
                            "settings": {
                                "WadCfg": {
                                    "DiagnosticMonitorConfiguration": {
                                        "overallQuotaInMB": "50000",
                                        "EtwProviders": {
                                            "EtwEventSourceProviderConfiguration": [
                                                {
                                                    "provider": "Microsoft-ServiceFabric-Actors",
                                                    "scheduledTransferKeywordFilter": "1",
                                                    "scheduledTransferPeriod": "PT5M",
                                                    "DefaultEvents": {
                                                        "eventDestination": "ServiceFabricReliableActorEventTable"
                                                    }
                                                },
                                                {
                                                    "provider": "Microsoft-ServiceFabric-Services",
                                                    "scheduledTransferPeriod": "PT5M",
                                                    "DefaultEvents": {
                                                        "eventDestination": "ServiceFabricReliableServiceEventTable"
                                                    }
                                                }
                                            ],
                                            "EtwManifestProviderConfiguration": [
                                                {
                                                    "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                                                    "scheduledTransferLogLevelFilter": "Information",
                                                    "scheduledTransferKeywordFilter": "4611686018427387904",
                                                    "scheduledTransferPeriod": "PT5M",
                                                    "DefaultEvents": {
                                                        "eventDestination": "ServiceFabricSystemEventTable"
                                                    }
                                                }
                                            ]
                                        }
                                    }
                                },
                                "StorageAccount": "[variables('applicationDiagnosticsStorageAccountName')]"
                            },
                            "typeHandlerVersion": "1.5"
                        }
                    },
                    {
                        "name": "[concat('VMIaaSAntimalware','_vmNodeType3Name')]",
                        "properties": {
                            "publisher": "Microsoft.Azure.Security",
                            "type": "IaaSAntimalware",
                            "typeHandlerVersion": "1.5",
                            "settings": {
                                "AntimalwareEnabled": "true",
                                "Exclusions": {
                                    "Paths": "D:\\SvcFab;D:\\SvcFab\\Log;C:\\Program Files\\Microsoft Service Fabric",
                                    "Processes": "Fabric.exe;FabricHost.exe;FabricInstallerService.exe;FabricSetup.exe;FabricDeployer.exe;ImageBuilder.exe;FabricGateway.exe;FabricDCA.exe;FabricFAS.exe;FabricUOS.exe;FabricRM.exe;FileStoreService.exe"
                                },
                                "RealtimeProtectionEnabled": "true",
                                "ScheduledScanSettings": {
                                    "isEnabled": "true",
                                    "scanType": "Quick",
                                    "day": "7",
                                    "time": "120"
                                }
                            },
                            "protectedSettings": null
                        }
                    }
                ]
            },
            "networkProfile": {
                "networkInterfaceConfigurations": [
                    {
                        "name": "[concat(variables('nicName'), '-2')]",
                        "properties": {
                            "ipConfigurations": [
                                {
                                    "name": "[concat(variables('nicName'),'-',2)]",
                                    "properties": {
                                        "loadBalancerBackendAddressPools": [
                                            {
                                                "id": "[variables('lbPoolID3')]"
                                            }
                                        ],
                                        "loadBalancerInboundNatPools": [
                                            {
                                                "id": "[variables('lbNatPoolID3')]"
                                            }
                                        ],
                                        "subnet": {
                                            "id": "[variables('subnet3Ref')]"
                                        }
                                    }
                                }
                            ],
                            "primary": true
                        }
                    }
                ]
            },
            "osProfile": {
                "adminPassword": "[parameters('adminPassword')]",
                "adminUsername": "[parameters('adminUsername')]",
                "computernamePrefix": "[variables('vmNodeType3Name')]",
                "secrets": [
                    {
                        "sourceVault": {
                            "id": "[parameters('sourceVaultValue')]"
                        },
                        "vaultCertificates": [
                            {
                                "certificateStore": "[parameters('certificateStoreValue')]",
                                "certificateUrl": "[parameters('certificateUrlValue')]"
                            }
                        ]
                    }
                ]
            },
            "storageProfile": {
                "imageReference": {
                    "publisher": "[parameters('vmImagePublisher')]",
                    "offer": "[parameters('vmImageOffer')]",
                    "sku": "[parameters('vmImageSku')]",
                    "version": "[parameters('vmImageVersion')]"
                },
                "osDisk": {
                    "caching": "ReadOnly",
                    "createOption": "FromImage",
                    "managedDisk": {
                        "storageAccountType": "[parameters('storageAccountType')]"
                    }
                }
            }
        }
    },
    "dependsOn": [
        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]",
        "[concat('Microsoft.Network/loadBalancers/', concat('LB','-', parameters('clusterName'),'-',variables('vmNodeType3Name')))]",
        "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]",
        "[concat('Microsoft.Storage/storageAccounts/', variables('applicationDiagnosticsStorageAccountName'))]"
    ]
},
```

W *template.js* pliku, zaktualizuj zasób klastra i Dodaj nowy typ węzła:
```json
{
    "type": "Microsoft.ServiceFabric/clusters",
    "name": "[parameters('clusterName')]",
    "apiVersion": "2018-02-01",
    "location": "[parameters('clusterLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "nodeTypes": [
            ...
            {
                "name": "[variables('vmNodeType3Name')]",
                "applicationPorts": {
                    "endPort": "[variables('nt3applicationEndPort')]",
                    "startPort": "[variables('nt3applicationStartPort')]"
                },
                "clientConnectionEndpointPort": "[variables('nt3fabricTcpGatewayPort')]",
                "durabilityLevel": "Silver",
                "ephemeralPorts": {
                    "endPort": "[variables('nt3ephemeralEndPort')]",
                    "startPort": "[variables('nt3ephemeralStartPort')]"
                },
                "httpGatewayEndpointPort": "[variables('nt3fabricHttpGatewayPort')]",
                "isPrimary": false,
                "reverseProxyEndpointPort": "[variables('nt3reverseProxyEndpointPort')]",
                "vmInstanceCount": "[parameters('nt3InstanceCount')]"
            }
        ],    
    }
}                
```

W *parameters.jsna* pliku Dodaj następujące nowe parametry i wartości:
```json
"nt3InstanceCount": {
    "Value": 5    
},
"vmNodeType3Size": {
    "Value": "Standard_D2_V2"
},
```

### <a name="deploy-the-updated-template"></a>Wdróż zaktualizowany szablon
Zapisz wszelkie zmiany *template.js* i *parameters.jsna* plikach.  Aby wdrożyć zaktualizowany szablon, uruchom następujące polecenie:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName sfclustertutorialgroup -TemplateFile c:\temp\template.json -TemplateParameterFile c:\temp\parameters.json -Name "AddingNodeType"
```
Lub następujące polecenie interfejsu wiersza polecenia platformy Azure:
```azurecli
az group deployment create --resource-group sfclustertutorialgroup --template-file c:\temp\template.json --parameters c:\temp\parameters.json
```

## <a name="remove-a-node-type-from-the-cluster"></a>Usuń typ węzła z klastra
Po utworzeniu klastra Service Fabric można skalować klaster w poziomie, usuwając typ węzła (zestaw skalowania maszyn wirtualnych) i wszystkie jego węzły. Klaster można skalować w dowolnym momencie, nawet w przypadku uruchamiania obciążeń w klastrze. W miarę skalowania klastra aplikacje są automatycznie skalowane.

> [!WARNING]
> Nie zaleca się częstego używania Remove-AzServiceFabricNodeType do usuwania typu węzła z klastra produkcyjnego. Jest to niebezpieczne polecenie, ponieważ usuwa zasób zestawu skalowania maszyn wirtualnych za typem węzła. 

Aby usunąć typ węzła, uruchom polecenie cmdlet [Remove-AzServiceFabricNodeType](/powershell/module/az.servicefabric/remove-azservicefabricnodetype) .  Typ węzła musi być [poziomem trwałości][durability]  Silver lub Gold polecenie cmdlet usuwa zestaw skalowania skojarzony z typem węzła i trwa jakiś czas.  Następnie uruchom polecenie cmdlet [Remove-ServiceFabricNodeState](/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) w każdym z węzłów do usunięcia, co spowoduje usunięcie stanu węzła i usunięcie węzłów z klastra. Jeśli w węzłach znajdują się usługi, usługi są najpierw przenoszone do innego węzła. Jeśli Menedżer klastra nie może znaleźć węzła dla repliki/usługi, operacja zostanie opóźniona/zablokowana.

```powershell
$groupname = "sfclustertutorialgroup"
$nodetype = "nt4vm"
$clustername = "mysfcluster123"

Remove-AzServiceFabricNodeType -Name $clustername  -NodeType $nodetype -ResourceGroupName $groupname

Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster123.eastus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint <thumbprint> `
          -FindType FindByThumbprint -FindValue <thumbprint> `
          -StoreLocation CurrentUser -StoreName My

$nodes = Get-ServiceFabricNode | Where-Object {$_.NodeType -eq $nodetype} | Sort-Object { $_.NodeName.Substring($_.NodeName.LastIndexOf('_') + 1) } -Descending

Foreach($node in $nodes)
{
    Remove-ServiceFabricNodeState -NodeName $node.NodeName -TimeoutSec 300 -Force 
}
```

## <a name="increase-node-resources"></a>Zwiększ zasoby węzła 
Po utworzeniu klastra Service Fabric można skalować typ węzła klastra w pionie (zmienić zasoby węzłów) lub uaktualnić system operacyjny maszyn wirtualnych typu węzła, zastępując oryginalny typ węzła nowym typem węzła (ze zaktualizowaną jednostką SKU maszyny wirtualnej lub obrazem systemu operacyjnego). Aby uzyskać więcej informacji, zobacz [skalowanie w górę typu węzła Service Fabric platformy Azure](service-fabric-scale-up-primary-node-type.md).

> [!IMPORTANT]
> Nigdy nie należy próbować zmiany w miejscu jednostki SKU maszyny wirtualnej lub obrazu systemu operacyjnego, który jest niebezpieczną operacją i nie jest obsługiwany.

Jeśli to nie jest możliwe, można utworzyć nowy klaster i [przywrócić stan aplikacji](service-fabric-reliable-services-backup-restore.md) (jeśli dotyczy) ze starego klastra. Nie trzeba przywracać żadnego stanu usługi systemowej; są one tworzone ponownie po wdrożeniu aplikacji w nowym klastrze. Jeśli w klastrze uruchomiono tylko bezstanowe aplikacje, wszystkie te aplikacje są wdrażane w nowym klastrze, nie ma niczego do przywrócenia.

### <a name="update-the-template"></a>Aktualizowanie szablonu

[Wyeksportuj plik szablonu i parametrów](#export-the-template-for-the-resource-group) z grupy zasobów dla najnowszego wdrożenia.  Otwórz *parameters.js* pliku.  Jeśli klaster został wdrożony przy użyciu [przykładowego szablonu][template] w tym samouczku, w klastrze istnieją trzy typy węzłów.  

Rozmiar maszyn wirtualnych w drugim typie węzła jest ustawiany w parametrze *vmNodeType1Size* .  Zmień wartość parametru *vmNodeType1Size* z Standard_D2_V2 na [Standard_D3_V2](../virtual-machines/dv2-dsv2-series.md), co podwaja zasoby poszczególnych wystąpień maszyn wirtualnych.

Jednostka SKU maszyny wirtualnej dla wszystkich trzech typów węzłów jest ustawiana za pomocą parametru *vmImageSku* .  W przypadku zmiany jednostki SKU maszyny wirtualnej typu węzła należy zachować ostrożność i nie jest to zalecane w przypadku podstawowego typu węzła.

### <a name="deploy-the-updated-template"></a>Wdróż zaktualizowany szablon
Zapisz wszelkie zmiany *template.js* i *parameters.jsna* plikach.  Aby wdrożyć zaktualizowany szablon, uruchom następujące polecenie:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName sfclustertutorialgroup -TemplateFile c:\temp\template.json -TemplateParameterFile c:\temp\parameters.json -Name "ScaleUpNodeType"
```
Lub następujące polecenie interfejsu wiersza polecenia platformy Azure:
```azurecli
az group deployment create --resource-group sfclustertutorialgroup --template-file c:\temp\template.json --parameters c:\temp\parameters.json
```

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dodawanie i usuwanie węzłów (skalowanie w poziomie i skalowanie w górę)
> * Dodawanie i usuwanie typów węzłów (skalowanie w poziomie i skalowanie w górę)
> * Zwiększ zasoby węzła (Skaluj w górę)

Przejdź do kolejnego samouczka, aby dowiedzieć się, jak uaktualnić środowisko uruchomieniowe klastra.
> [!div class="nextstepaction"]
> [Uaktualnianie środowiska uruchomieniowego klastra](service-fabric-tutorial-upgrade-cluster.md)

[durability]: service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster
[reliability]: service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster
[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.Parameters.json
