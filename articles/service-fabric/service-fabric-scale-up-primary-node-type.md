---
title: Skalowanie w górę podstawowego typu węzła Service Fabric platformy Azure
description: Dowiedz się, jak skalować klaster Service Fabric, dodając typ węzła.
ms.topic: article
ms.date: 08/06/2020
ms.author: pepogors
ms.openlocfilehash: a18a40cc9e467b089ea9d6be3d0ca81a21d2c474
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89228719"
---
# <a name="scale-up-a-service-fabric-cluster-primary-node-type-by-adding-a-node-type"></a>Skaluj w górę typ węzła podstawowego klastra Service Fabric, dodając typ węzła
W tym artykule opisano sposób skalowania w górę typu węzła podstawowego klastra Service Fabric przez dodanie dodatkowego typu węzła do klastra. Klaster Service Fabric jest połączonym z siecią zestawem maszyn wirtualnych lub fizycznych, w którym są wdrażane i zarządzane mikrousługi. Maszyna lub maszyna wirtualna będąca częścią klastra nazywa się węzłem. Zestawy skalowania maszyn wirtualnych to zasób obliczeniowy platformy Azure, który służy do wdrażania kolekcji maszyn wirtualnych jako zestawu i zarządzania nią. Każdy typ węzła, który jest zdefiniowany w klastrze platformy Azure [, jest ustawiany jako oddzielny zestaw skalowania](service-fabric-cluster-nodetypes.md). Każdy typ węzła może być następnie zarządzany osobno.

Przykładowe szablony w następującym samouczku można znaleźć tutaj: [Service Fabric podstawowe skalowanie typu węzła](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Primary-NodeType-Scaling-Sample)

> [!WARNING]
> Nie należy podejmować próby przeprowadzenia skalowania w górę podstawowego typu węzła, jeśli stan klastra jest niepoprawny, co spowoduje dalsze niestabilne działanie klastra.
>

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="process-to-upgrade-the-size-and-operating-system-of-the-primary-node-type"></a>Proces uaktualniania rozmiaru i systemu operacyjnego typu węzła podstawowego
Poniżej przedstawiono proces aktualizowania rozmiaru maszyny wirtualnej i systemu operacyjnego dla maszyn wirtualnych typu węzła podstawowego.  Po uaktualnieniu maszyny wirtualne typu węzła podstawowego mają rozmiar standardowy D4_V2 i uruchomiono system Windows Server 2019 Datacenter z kontenerami.

> [!WARNING]
> Przed podjęciem próby wykonania tej procedury w klastrze produkcyjnym zalecamy zbadanie przykładowych szablonów i zweryfikowanie procesu względem klastra testowego. Klaster może być również niedostępny przez krótki czas. 

### <a name="deploy-the-initial-service-fabric-cluster"></a>Wdróż początkowy klaster Service Fabric 
Jeśli chcesz postępować zgodnie z przykładem, wdróż początkowy klaster z jednym typem węzła podstawowego i z pojedynczym zestawem skalowania [Service Fabric-początkowym klastrze](https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/Primary-NodeType-Scaling-Sample/AzureDeploy-1.json). Możesz pominąć ten krok, jeśli masz już wdrożony klaster Service Fabric. 

1. Zaloguj się do konta platformy Azure. 
```powershell
# sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId "<your subscription ID>"
```
2. Tworzenie nowej grupy zasobów 
```powershell
# create a resource group for your cluster deployment
$resourceGroupName = "myResourceGroup"
$location = "WestUS"

New-AzResourceGroup `
    -Name $resourceGroupName `
    -Location $location
```
3. Wypełnij wartości parametrów w plikach szablonów. 
4. Wdróż klaster w grupie zasobów utworzonej w kroku 2. 
```powershell
# deploy the template files to the resource group created above
$templateFilePath = "C:\AzureDeploy-1.json"
$parameterFilePath = "C:\AzureDeploy.Parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath
```

### <a name="add-a-new-primary-node-type-to-the-cluster"></a>Dodawanie nowego typu węzła podstawowego do klastra
> [!Note]
> Zasoby utworzone w następujących krokach staną się nowym typem węzła podstawowego w klastrze po zakończeniu operacji skalowania. Upewnij się, że używasz nazw unikatowych z podsieci początkowej, publicznego adresu IP, Load Balancer, zestawu skalowania maszyn wirtualnych i typu węzła. 

Szablon zawierający wszystkie poniższe kroki można znaleźć tutaj: [Service Fabric-nowy klaster typu węzeł](https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/Primary-NodeType-Scaling-Sample/AzureDeploy-2.json). Poniższe kroki zawierają częściowe fragmenty zasobów, które wyróżnią zmiany w nowych zasobach.  

1. Utwórz nową podsieć w istniejącym Virtual Network.
```json
{
    "name": "[variables('subnet1Name')]",
    "properties": {
        "addressPrefix": "[variables('subnet1Prefix')]"
    }
}
```
2. Utwórz nowy publiczny zasób IP z unikatowym wartość domainnamelabel. 
```json
{
    "apiVersion": "[variables('publicIPApiVersion')]",
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[concat(variables('lbIPName'),'-',variables('vmNodeType1Name'))]",
    "location": "[variables('computeLocation')]",
    "properties": {
    "dnsSettings": {
        "domainNameLabel": "[concat(variables('dnsName'),'-','nt2')]"
    },
    "publicIPAllocationMethod": "Dynamic"
    },
    "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
    }
}
```
3. Utwórz nowy zasób Load Balancer, który zależy od publicznego adresu IP utworzonego powyżej. 
```json
"dependsOn": [
    "[concat('Microsoft.Network/publicIPAddresses/',concat(variables('lbIPName'),'-',variables('vmNodeType1Name')))]"
]
```
4. Utwórz nowy zestaw skalowania maszyn wirtualnych, który używa nowej jednostki SKU maszyny wirtualnej i jednostki SKU systemu operacyjnego, do której chcesz skalować w górę. 

Typ węzła ref 
```json
"nodeTypeRef": "[variables('vmNodeType1Name')]"
```

Jednostka SKU maszyny wirtualnej
```json
"sku": {
    "name": "[parameters('vmNodeType1Size')]",
    "capacity": "[parameters('nt1InstanceCount')]",
    "tier": "Standard"
}
```

JEDNOSTKA SKU SYSTEMU OPERACYJNEGO 
```json
"imageReference": {
    "publisher": "[parameters('vmImagePublisher1')]",
    "offer": "[parameters('vmImageOffer1')]",
    "sku": "[parameters('vmImageSku1')]",
    "version": "[parameters('vmImageVersion1')]"
}
```

Poniższy fragment kodu stanowi przykład nowego zasobu zestawu skalowania maszyn wirtualnych, który jest używany do tworzenia nowego typu węzła dla klastra Service Fabric. Należy upewnić się, że zawarto dodatkowe rozszerzenia, które są wymagane dla obciążenia. 

```json
    {
      "apiVersion": "[variables('vmssApiVersion')]",
      "type": "Microsoft.Compute/virtualMachineScaleSets",
      "name": "[variables('vmNodeType1Name')]",
      "location": "[variables('computeLocation')]",
      "dependsOn": [
        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]",
        "[concat('Microsoft.Network/loadBalancers/', concat('LB','-', parameters('clusterName'),'-',variables('vmNodeType1Name')))]",
        "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]",
        "[concat('Microsoft.Storage/storageAccounts/', variables('applicationDiagnosticsStorageAccountName'))]"
      ],
      "properties": {
        "overprovision": "[variables('overProvision')]",
        "upgradePolicy": {
          "mode": "Automatic"
        },
        "virtualMachineProfile": {
          "extensionProfile": {
            "extensions": [
              {
                "name": "[concat('ServiceFabricNodeVmExt_',variables('vmNodeType1Name'))]",
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
                    "nodeTypeRef": "[variables('vmNodeType1Name')]",
                    "dataPath": "D:\\SvcFab",
                    "durabilityLevel": "Bronze",
                    "enableParallelJobs": true,
                    "nicPrefixOverride": "[variables('subnet1Prefix')]",
                    "certificate": {
                      "thumbprint": "[parameters('certificateThumbprint')]",
                      "x509StoreName": "[parameters('certificateStoreValue')]"
                    }
                  },
                  "typeHandlerVersion": "1.0"
                }
              }
            ]
          },
          "networkProfile": {
            "networkInterfaceConfigurations": [
              {
                "name": "[concat(variables('nicName'), '-1')]",
                "properties": {
                  "ipConfigurations": [
                    {
                      "name": "[concat(variables('nicName'),'-',1)]",
                      "properties": {
                        "loadBalancerBackendAddressPools": [
                          {
                            "id": "[variables('lbPoolID1')]"
                          }
                        ],
                        "loadBalancerInboundNatPools": [
                          {
                            "id": "[variables('lbNatPoolID1')]"
                          }
                        ],
                        "subnet": {
                          "id": "[variables('subnet1Ref')]"
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
            "computernamePrefix": "[variables('vmNodeType1Name')]",
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
              "publisher": "[parameters('vmImagePublisher1')]",
              "offer": "[parameters('vmImageOffer1')]",
              "sku": "[parameters('vmImageSku1')]",
              "version": "[parameters('vmImageVersion1')]"
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
      "sku": {
        "name": "[parameters('vmNodeType1Size')]",
        "capacity": "[parameters('nt1InstanceCount')]",
        "tier": "Standard"
      },
      "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
      }
    },

```

5. Dodaj nowy typ węzła do klastra, który odwołuje się do zestawu skalowania maszyn wirtualnych, który został utworzony powyżej. Właściwość **isprimary** tego typu węzła powinna mieć wartość true. 
```json
"name": "[variables('vmNodeType1Name')]",
"applicationPorts": {
    "endPort": "[variables('nt0applicationEndPort')]",
    "startPort": "[variables('nt0applicationStartPort')]"
},
"clientConnectionEndpointPort": "[variables('nt0fabricTcpGatewayPort')]",
"durabilityLevel": "Bronze",
"ephemeralPorts": {
    "endPort": "[variables('nt0ephemeralEndPort')]",
    "startPort": "[variables('nt0ephemeralStartPort')]"
},
"httpGatewayEndpointPort": "[variables('nt0fabricHttpGatewayPort')]",
"isPrimary": true,
"reverseProxyEndpointPort": "[variables('nt0reverseProxyEndpointPort')]",
"vmInstanceCount": "[parameters('nt1InstanceCount')]"
```
6. Wdróż zaktualizowany szablon ARM. 
```powershell
# deploy the updated template files to the existing resource group
$templateFilePath = "C:\AzureDeploy-2.json"
$parameterFilePath = "C:\AzureDeploy.Parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
```

Klaster Service Fabric będzie miał teraz dwa typy węzłów po zakończeniu wdrażania. 

### <a name="remove-the-existing-node-type"></a>Usuń istniejący typ węzła 
Po zakończeniu wdrażania zasobów można rozpocząć wyłączanie węzłów w oryginalnym typie węzła podstawowego. Po wyłączeniu węzłów usługi systemowe zostaną zmigrowane do nowego typu węzła podstawowego, który został wdrożony w powyższym kroku.

1. Ustaw właściwość Typ węzła podstawowego w Service Fabric zasób klastra na wartość false. 
```json
{
    "name": "[variables('vmNodeType0Name')]",
    "applicationPorts": {
        "endPort": "[variables('nt0applicationEndPort')]",
        "startPort": "[variables('nt0applicationStartPort')]"
    },
    "clientConnectionEndpointPort": "[variables('nt0fabricTcpGatewayPort')]",
    "durabilityLevel": "Bronze",
    "ephemeralPorts": {
        "endPort": "[variables('nt0ephemeralEndPort')]",
        "startPort": "[variables('nt0ephemeralStartPort')]"
    },
    "httpGatewayEndpointPort": "[variables('nt0fabricHttpGatewayPort')]",
    "isPrimary": false,
    "reverseProxyEndpointPort": "[variables('nt0reverseProxyEndpointPort')]",
    "vmInstanceCount": "[parameters('nt0InstanceCount')]"
}
```
2. Wdróż szablon ze zaktualizowaną właściwością isprimary dla oryginalnego typu węzła. Możesz znaleźć szablon z flagą Primary ustawioną na wartość false w oryginalnym typie węzła w tym miejscu: [Service Fabric-typ węzła podstawowego false](https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/Primary-NodeType-Scaling-Sample/AzureDeploy-3.json).

```powershell
# deploy the updated template files to the existing resource group
$templateFilePath = "C:\AzureDeploy-3.json"
$parameterFilePath = "C:\AzureDeploy.Parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
```

3. Wyłącz węzły w węźle typu 0. 
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint $ClusterConnectionEndpoint `
    -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $thumb  `
    -FindType FindByThumbprint `
    -FindValue $thumb `
    -StoreLocation CurrentUser `
    -StoreName My 

Write-Host "Connected to cluster"


$nodeType = "nt1vm" # specify the name of node type
$nodes = Get-ServiceFabricNode 

Write-Host "Disabling nodes..."
foreach($node in $nodes)
{
  if ($node.NodeType -eq $nodeType)
  {
    $node.NodeName

    Disable-ServiceFabricNode -Intent RemoveNode -NodeName $node.NodeName -Force
  }
}
```
* W przypadku trwałości Bronze poczekaj, aż wszystkie węzły zostaną wyłączone.
* W przypadku trwałości srebra i złota niektóre węzły zostaną wyłączone, a reszta będzie w stanie wyłączać. Sprawdź kartę Szczegóły węzłów w polu wyłączanie stanu, jeśli są one zablokowane w celu zapewnienia kworum dla partycji usługi infrastruktury, a następnie Kontynuuj, aby kontynuować.

> [!Note]
> Wykonanie tego kroku może potrwać trochę czasu. 

4. Zatrzymaj dane w węźle typu 0. 
```powershell
foreach($node in $nodes)
{
  if ($node.NodeType -eq $nodeType)
  {
    $node.NodeName

    Start-ServiceFabricNodeTransition -Stop -OperationId (New-Guid) -NodeInstanceId $node.NodeInstanceId -NodeName $node.NodeName -StopDurationInSeconds 10000
  }
}
```
5. Cofnij przydział węzłów w oryginalnym zestawie skalowania maszyn wirtualnych 
```powershell
$scaleSetName="nt1vm"
$scaleSetResourceType="Microsoft.Compute/virtualMachineScaleSets"

Remove-AzResource -ResourceName $scaleSetName -ResourceType $scaleSetResourceType -ResourceGroupName $resourceGroupName -Force
```
> [!Note]
> Kroki 6 i 7 są opcjonalne, jeśli używasz już publicznego adresu IP jednostki SKU i usługi równoważenia obciążenia w warstwie Standardowa. W takim przypadku można mieć wiele zestawów skalowania maszyn wirtualnych/węzłów w ramach tego samego modułu równoważenia obciążenia. 

6. Możesz teraz usunąć oryginalny adres IP, a Load Balancer zasoby. W tym kroku zostanie również zaktualizowana nazwa DNS. 

```powershell
$lbname="LB-cluster-name-nt1vm"
$lbResourceType="Microsoft.Network/loadBalancers"
$ipResourceType="Microsoft.Network/publicIPAddresses"
$oldPublicIpName="PublicIP-LB-FE-nt1vm"
$newPublicIpName="PublicIP-LB-FE-nt2vm"

$oldprimaryPublicIP = Get-AzPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $resourceGroupName
$primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel
$primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn

Remove-AzResource -ResourceName $lbname -ResourceType $lbResourceType -ResourceGroupName $resourceGroupName -Force
Remove-AzResource -ResourceName $oldPublicIpName -ResourceType $ipResourceType -ResourceGroupName $resourceGroupName -Force

$PublicIP = Get-AzPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $resourceGroupName
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzPublicIpAddress -PublicIpAddress $PublicIP
``` 

7. Zaktualizuj punkt końcowy zarządzania w klastrze, aby odwołać się do nowego adresu IP. 
```json
  "managementEndpoint": "[concat('https://',reference(concat(variables('lbIPName'),'-',variables('vmNodeType1Name'))).dnsSettings.fqdn,':',variables('nt0fabricHttpGatewayPort'))]",
```
8. Usuń stan węzła z typu węzła 0.
```powershell
foreach($node in $nodes)
{
  if ($node.NodeType -eq $nodeType)
  {
    $node.NodeName

    Remove-ServiceFabricNodeState -NodeName $node.NodeName -Force
  }
}
```
9. Usuń pierwotne odwołanie do typu węzła z zasobu Service Fabric w szablonie ARM. 
```json
"name": "[variables('vmNodeType0Name')]",
"applicationPorts": {
    "endPort": "[variables('nt0applicationEndPort')]",
    "startPort": "[variables('nt0applicationStartPort')]"
},
"clientConnectionEndpointPort": "[variables('nt0fabricTcpGatewayPort')]",
"durabilityLevel": "Bronze",
"ephemeralPorts": {
    "endPort": "[variables('nt0ephemeralEndPort')]",
    "startPort": "[variables('nt0ephemeralStartPort')]"
},
"httpGatewayEndpointPort": "[variables('nt0fabricHttpGatewayPort')]",
"isPrimary": true,
"reverseProxyEndpointPort": "[variables('nt0reverseProxyEndpointPort')]",
"vmInstanceCount": "[parameters('nt0InstanceCount')]"
```
W przypadku klastrów Silver i wyższych trwałości należy zaktualizować zasób klastra w szablonie i skonfigurować zasady kondycji w celu ignorowania kondycji aplikacji sieci szkieletowej:/systemu przez dodanie applicationDeltaHealthPolicies w obszarze właściwości zasobów klastra zgodnie z poniższym opisem. Poniższe zasady powinny ignorować istniejące błędy, ale nie pozwalają na nowe błędy kondycji.
```json
"upgradeDescription":  
{ 
 "forceRestart": false, 
 "upgradeReplicaSetCheckTimeout": "10675199.02:48:05.4775807", 
 "healthCheckWaitDuration": "00:05:00", 
 "healthCheckStableDuration": "00:05:00", 
 "healthCheckRetryTimeout": "00:45:00", 
 "upgradeTimeout": "12:00:00", 
 "upgradeDomainTimeout": "02:00:00", 
 "healthPolicy": { 
   "maxPercentUnhealthyNodes": 100, 
   "maxPercentUnhealthyApplications": 100 
 }, 
 "deltaHealthPolicy":  
 { 
   "maxPercentDeltaUnhealthyNodes": 0, 
   "maxPercentUpgradeDomainDeltaUnhealthyNodes": 0, 
   "maxPercentDeltaUnhealthyApplications": 0, 
   "applicationDeltaHealthPolicies":  
   { 
       "fabric:/System":  
       { 
           "defaultServiceTypeDeltaHealthPolicy":  
           { 
                   "maxPercentDeltaUnhealthyServices": 0 
           } 
       } 
   } 
 } 
}
```
10. Usuń wszystkie inne zasoby związane z oryginalnym typem węzła z szablonu ARM. Zapoznaj się z tematem [Service Fabric-nowy węzeł typu węzła](https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/Primary-NodeType-Scaling-Sample/AzureDeploy-4.json) dla szablonu z usuniętymi wszystkimi oryginalnymi zasobami.

11. Wdróż zmodyfikowany szablon Azure Resource Manager. * * Ten krok zajmie trochę czasu, zwykle maksymalnie dwie godziny. To uaktualnienie spowoduje zmianę ustawień na InfrastructureService; w związku z tym wymagane jest ponowne uruchomienie węzła. W tym przypadku forceRestart jest ignorowana. Parametr upgradeReplicaSetCheckTimeout określa maksymalny czas, który Service Fabric czeka, aż partycja będzie w stanie bezpiecznym, jeśli nie jest jeszcze w stanie bezpiecznym. Gdy sprawdzanie bezpieczeństwa zostanie zakończone dla wszystkich partycji w węźle, Service Fabric kontynuuje uaktualnianie w tym węźle. Wartość parametru upgradeTimeout można zmniejszyć do 6 godzin, ale w celu zapewnienia maksymalnego poziomu bezpieczeństwa 12 godzin powinno być używane.
Następnie sprawdź, czy zasób Service Fabric w portalu jest wyświetlany jako gotowy. 

```powershell
# deploy the updated template files to the existing resource group
$templateFilePath = "C:\AzureDeploy-4.json"
$parameterFilePath = "C:\AzureDeploy.Parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
```

Typ węzła podstawowego klastra został teraz uaktualniony. Sprawdź, czy wdrożone aplikacje działają prawidłowo i kondycja klastra jest prawidłowa.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak [dodać typ węzła do klastra](virtual-machine-scale-set-scale-node-type-scale-out.md)
* Dowiedz się więcej o [skalowalności aplikacji](service-fabric-concepts-scalability.md).
* [Skalowanie klastra platformy Azure w poziomie lub na zewnątrz](service-fabric-tutorial-scale-cluster.md).
* [Programowo Skaluj klaster platformy Azure](service-fabric-cluster-programmatic-scaling.md) przy użyciu zestawu Azure COMPUTE SDK.
* [Skalowanie klastra autonomicznego w poziomie lub na zewnątrz](service-fabric-cluster-windows-server-add-remove-nodes.md).
