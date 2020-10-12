---
title: Wzorce sieciowe dla Service Fabric platformy Azure
description: Opisuje typowe wzorce sieci dla Service Fabric i sposób tworzenia klastra przy użyciu funkcji sieciowych platformy Azure.
ms.topic: conceptual
ms.date: 01/19/2018
ms.openlocfilehash: 20bd5e931307725016c3e2ad69dae91214b2caab
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87421471"
---
# <a name="service-fabric-networking-patterns"></a>Service Fabric wzorców sieci
Możesz zintegrować klaster usługi Azure Service Fabric z innymi funkcjami sieciowymi platformy Azure. W tym artykule przedstawiono sposób tworzenia klastrów, które korzystają z następujących funkcji:

- [Istniejąca sieć wirtualna lub podsieć](#existingvnet)
- [Statyczny publiczny adres IP](#staticpublicip)
- [Moduł równoważenia obciążenia wyłącznie wewnętrznie](#internallb)
- [Wewnętrzny i zewnętrzny moduł równoważenia obciążenia](#internalexternallb)

Service Fabric działa w standardowym zestawie skalowania maszyn wirtualnych. Wszystkie funkcje, których można użyć w zestawie skalowania maszyn wirtualnych, można użyć z klastrem Service Fabric. Sekcje dotyczące sieci w szablonach Azure Resource Manager dla zestawów skalowania maszyn wirtualnych i Service Fabric są identyczne. Po wdrożeniu w istniejącej sieci wirtualnej można łatwo włączyć inne funkcje sieciowe, takie jak Azure ExpressRoute, Azure VPN Gateway, sieciowa Grupa zabezpieczeń i Komunikacja równorzędna sieci wirtualnej.

### <a name="allowing-the-service-fabric-resource-provider-to-query-your-cluster"></a>Zezwalanie dostawcy zasobów Service Fabric na wysyłanie zapytań do klastra

Service Fabric jest unikatowy z innych funkcji sieciowych w jednym z aspektów. [Azure Portal](https://portal.azure.com) wewnętrznie używa dostawcy zasobów Service Fabric do wywołania klastra w celu uzyskania informacji o węzłach i aplikacjach. Dostawca zasobów Service Fabric wymaga publicznie dostępnego dostępu przychodzącego do portu bramy HTTP (domyślnie port 19080) w punkcie końcowym zarządzania. [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) używa punktu końcowego zarządzania do zarządzania klastrem. Dostawca zasobów Service Fabric używa również tego portu do wykonywania zapytań dotyczących informacji o klastrze, aby wyświetlić je w Azure Portal. 

Jeśli port 19080 nie jest dostępny z poziomu dostawcy zasobów Service Fabric, w portalu pojawi się komunikat, na przykład *nie znaleziono węzłów* , a lista węzłów i aplikacji jest pusta. Jeśli klaster ma być widoczny w Azure Portal, moduł równoważenia obciążenia musi ujawniać publiczny adres IP, a sieciowa Grupa zabezpieczeń musi zezwalać na ruch przychodzący portu 19080. Jeśli Instalator nie spełnia tych wymagań, Azure Portal nie wyświetla stanu klastra.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="templates"></a>Szablony

Wszystkie szablony Service Fabric znajdują się w serwisie [GitHub](https://github.com/Azure/service-fabric-scripts-and-templates/tree/master/templates/networking). Szablony można wdrażać w taki sam sposób, jak w przypadku następujących poleceń programu PowerShell. Jeśli wdrażasz istniejący szablon Virtual Network platformy Azure lub szablon statycznego publicznego adresu IP, najpierw Przeczytaj sekcję [wstępną konfigurację](#initialsetup) tego artykułu.

<a id="initialsetup"></a>
## <a name="initial-setup"></a>Początkowa konfiguracja

### <a name="existing-virtual-network"></a>Istniejąca sieć wirtualna

W poniższym przykładzie rozpoczynamy od istniejącej sieci wirtualnej o nazwie ExistingRG-VNET w grupie zasobów **ExistingRG** . Podsieć nosi nazwę domyślną. Te zasoby domyślne są tworzone podczas tworzenia standardowej maszyny wirtualnej (VM) przy użyciu Azure Portal. Można utworzyć sieć wirtualną i podsieć bez tworzenia maszyny wirtualnej, ale głównym celem dodania klastra do istniejącej sieci wirtualnej jest zapewnienie łączności sieciowej z innymi maszynami wirtualnymi. Tworzenie maszyny wirtualnej zapewnia dobry przykład użycia istniejącej sieci wirtualnej. Jeśli klaster Service Fabric używa tylko wewnętrznego modułu równoważenia obciążenia, bez publicznego adresu IP, możesz użyć maszyny wirtualnej i jej publicznego protokołu IP jako bezpiecznego *pola skoku*.

### <a name="static-public-ip-address"></a>Statyczny publiczny adres IP

Statyczny publiczny adres IP ogólnie jest zasobem dedykowanym zarządzanym niezależnie od maszyny wirtualnej lub maszyn wirtualnych, do których jest przypisana. Jest on inicjowany w dedykowanej grupie zasobów sieciowych (w przeciwieństwie do samej grupy zasobów klastra Service Fabric). Utwórz statyczny publiczny adres IP o nazwie staticIP1 w tej samej grupie zasobów ExistingRG, w Azure Portal lub przy użyciu programu PowerShell:

```powershell
PS C:\Users\user> New-AzPublicIpAddress -Name staticIP1 -ResourceGroupName ExistingRG -Location westus -AllocationMethod Static -DomainNameLabel sfnetworking

Name                     : staticIP1
ResourceGroupName        : ExistingRG
Location                 : westus
Id                       : /subscriptions/1237f4d2-3dce-1236-ad95-123f764e7123/resourceGroups/ExistingRG/providers/Microsoft.Network/publicIPAddresses/staticIP1
Etag                     : W/"fc8b0c77-1f84-455d-9930-0404ebba1b64"
ResourceGuid             : 77c26c06-c0ae-496c-9231-b1a114e08824
ProvisioningState        : Succeeded
Tags                     :
PublicIpAllocationMethod : Static
IpAddress                : 40.83.182.110
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : null
DnsSettings              : {
                             "DomainNameLabel": "sfnetworking",
                             "Fqdn": "sfnetworking.westus.cloudapp.azure.com"
                           }
```

### <a name="service-fabric-template"></a>Szablon Service Fabric

W przykładach w tym artykule używamy template.jsService Fabric w systemie. Przed utworzeniem klastra można użyć Kreatora portalu standardowego w celu pobrania szablonu z portalu. Można również użyć jednego z [przykładowych szablonów](https://github.com/Azure-Samples/service-fabric-cluster-templates), takich jak [bezpieczny Service Fabric klaster z pięcioma węzłami](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure).

<a id="existingvnet"></a>
## <a name="existing-virtual-network-or-subnet"></a>Istniejąca sieć wirtualna lub podsieć

1. Zmień parametr podsieci na nazwę istniejącej podsieci, a następnie Dodaj dwa nowe parametry, aby odwołać się do istniejącej sieci wirtualnej:

    ```json
        "subnet0Name": {
                "type": "string",
                "defaultValue": "default"
            },
            "existingVNetRGName": {
                "type": "string",
                "defaultValue": "ExistingRG"
            },

            "existingVNetName": {
                "type": "string",
                "defaultValue": "ExistingRG-vnet"
            },
            /*
            "subnet0Name": {
                "type": "string",
                "defaultValue": "Subnet-0"
            },
            "subnet0Prefix": {
                "type": "string",
                "defaultValue": "10.0.0.0/24"
            },*/
    ```

   Można również dodać komentarz do parametru o nazwie "virtualNetworkName", aby nie monitował o wprowadzenie nazwy sieci wirtualnej dwa razy w bloku Wdrożenie klastra w Azure Portal.

2. Atrybut komentarza `nicPrefixOverride` do elementu `Microsoft.Compute/virtualMachineScaleSets` , ponieważ jest używana istniejąca podsieć i ta zmienna została wyłączona w kroku 1.

    ```json
            /*"nicPrefixOverride": "[parameters('subnet0Prefix')]",*/
    ```

3. Zmień `vnetID` zmienną, aby wskazywała na istniejącą sieć wirtualną:

    ```json
            /*old "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",*/
            "vnetID": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', parameters('existingVNetRGName'), '/providers/Microsoft.Network/virtualNetworks/', parameters('existingVNetName'))]",
    ```

4. Usuń `Microsoft.Network/virtualNetworks` z zasobów, aby platforma Azure nie utworzyła nowej sieci wirtualnej:

    ```json
    /*{
    "apiVersion": "[variables('vNetApiVersion')]",
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[parameters('virtualNetworkName')]",
    "location": "[parameters('computeLocation')]",
    "properties": {
        "addressSpace": {
            "addressPrefixes": [
                "[parameters('addressPrefix')]"
            ]
        },
        "subnets": [
            {
                "name": "[parameters('subnet0Name')]",
                "properties": {
                    "addressPrefix": "[parameters('subnet0Prefix')]"
                }
            }
        ]
    },
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    }
    },*/
    ```

5. Dodaj komentarz do sieci wirtualnej z `dependsOn` atrybutu `Microsoft.Compute/virtualMachineScaleSets` , więc nie zależą od tworzenia nowej sieci wirtualnej:

    ```json
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Computer/virtualMachineScaleSets",
    "name": "[parameters('vmNodeType0Name')]",
    "location": "[parameters('computeLocation')]",
    "dependsOn": [
        /*"[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]",
        */
        "[Concat('Microsoft.Storage/storageAccounts/', variables('uniqueStringArray0')[0])]",

    ```

6. Wdróż szablon:

    ```powershell
    New-AzResourceGroup -Name sfnetworkingexistingvnet -Location westus
    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkingexistingvnet -TemplateFile C:\SFSamples\Final\template\_existingvnet.json
    ```

    Po wdrożeniu Sieć wirtualna powinna zawierać nowe maszyny wirtualne zestawu skalowania. Typ węzła zestawu skalowania maszyn wirtualnych powinien zawierać istniejącą sieć wirtualną i podsieć. Możesz również użyć Remote Desktop Protocol (RDP), aby uzyskać dostęp do maszyny wirtualnej, która została już w sieci wirtualnej, i aby wysłać polecenie ping do nowych maszyn wirtualnych zestawu skalowania:

    ```
    C:>\Users\users>ping 10.0.0.5 -n 1
    C:>\Users\users>ping NOde1000000 -n 1
    ```

Aby uzyskać inny przykład, zobacz [jeden, który nie jest specyficzny dla Service Fabric](https://github.com/gbowerman/azure-myriad/tree/main/existing-vnet).


<a id="staticpublicip"></a>
## <a name="static-public-ip-address"></a>Statyczny publiczny adres IP

1. Dodaj parametry dla nazwy istniejącej statycznej grupy zasobów IP, nazwy i w pełni kwalifikowanej nazwy domeny (FQDN):

    ```json
    "existingStaticIPResourceGroup": {
                "type": "string"
            },
            "existingStaticIPName": {
                "type": "string"
            },
            "existingStaticIPDnsFQDN": {
                "type": "string"
    }
    ```

2. Usuń `dnsName` parametr. (Statyczny adres IP już istnieje).

    ```json
    /*
    "dnsName": {
        "type": "string"
    },
    */
    ```

3. Dodaj zmienną, aby odwołać się do istniejącego statycznego adresu IP:

    ```json
    "existingStaticIP": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', parameters('existingStaticIPResourceGroup'), '/providers/Microsoft.Network/publicIPAddresses/', parameters('existingStaticIPName'))]",
    ```

4. Usuń `Microsoft.Network/publicIPAddresses` z zasobów, aby platforma Azure nie utworzyła nowego adresu IP:

    ```json
    /*
    {
        "apiVersion": "[variables('publicIPApiVersion')]",
        "type": "Microsoft.Network/publicIPAddresses",
        "name": "[concat(parameters('lbIPName'),)'-', '0')]",
        "location": "[parameters('computeLocation')]",
        "properties": {
            "dnsSettings": {
                "domainNameLabel": "[parameters('dnsName')]"
            },
            "publicIPAllocationMethod": "Dynamic"        
        },
        "tags": {
            "resourceType": "Service Fabric",
            "clusterName": "[parameters('clusterName')]"
        }
    }, */
    ```

5. Dodaj komentarz do adresu IP z `dependsOn` atrybutu `Microsoft.Network/loadBalancers` , więc nie zależą od tworzenia nowego adresu IP:

    ```json
    "apiVersion": "[variables('lbIPApiVersion')]",
    "type": "Microsoft.Network/loadBalancers",
    "name": "[concat('LB', '-', parameters('clusterName'), '-', parameters('vmNodeType0Name'))]",
    "location": "[parameters('computeLocation')]",
    /*
    "dependsOn": [
        "[concat('Microsoft.Network/publicIPAddresses/', concat(parameters('lbIPName'), '-', '0'))]"
    ], */
    "properties": {
    ```

6. W `Microsoft.Network/loadBalancers` zasobie Zmień element, `publicIPAddress` `frontendIPConfigurations` Aby odwoływać się do istniejącego statycznego adresu IP zamiast nowo utworzonego:

    ```json
                "frontendIPConfigurations": [
                        {
                            "name": "LoadBalancerIPConfig",
                            "properties": {
                                "publicIPAddress": {
                                    /*"id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(parameters('lbIPName'),'-','0'))]"*/
                                    "id": "[variables('existingStaticIP')]"
                                }
                            }
                        }
                    ],
    ```

7. W `Microsoft.ServiceFabric/clusters` zasobie przejdź `managementEndpoint` do nazwy FQDN DNS statycznego adresu IP. Jeśli używasz bezpiecznego klastra, upewnij się, że zmieniasz *http://* na *https://*. (Należy zauważyć, że ten krok dotyczy tylko klastrów Service Fabric. Jeśli używasz zestawu skalowania maszyn wirtualnych, Pomiń ten krok.

    ```json
                    "fabricSettings": [],
                    /*"managementEndpoint": "[concat('http://',reference(concat(parameters('lbIPName'),'-','0')).dnsSettings.fqdn,':',parameters('nt0fabricHttpGatewayPort'))]",*/
                    "managementEndpoint": "[concat('http://',parameters('existingStaticIPDnsFQDN'),':',parameters('nt0fabricHttpGatewayPort'))]",
    ```

8. Wdróż szablon:

    ```powershell
    New-AzResourceGroup -Name sfnetworkingstaticip -Location westus

    $staticip = Get-AzPublicIpAddress -Name staticIP1 -ResourceGroupName ExistingRG

    $staticip

    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkingstaticip -TemplateFile C:\SFSamples\Final\template\_staticip.json -existingStaticIPResourceGroup $staticip.ResourceGroupName -existingStaticIPName $staticip.Name -existingStaticIPDnsFQDN $staticip.DnsSettings.Fqdn
    ```

Po wdrożeniu można zobaczyć, że usługa równoważenia obciążenia jest powiązana z publicznym statycznym adresem IP z innej grupy zasobów. Punkt końcowy połączenia klienta Service Fabric i punkt końcowy [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) wskazują nazwę FQDN statycznego adresu IP w systemie DNS.

<a id="internallb"></a>
## <a name="internal-only-load-balancer"></a>Moduł równoważenia obciążenia wyłącznie wewnętrznie

Ten scenariusz zastępuje zewnętrzny moduł równoważenia obciążenia w domyślnym szablonie Service Fabric z modułem równoważenia obciążenia wyłącznie wewnętrznym. Zapoznaj się z [artykułami](#allowing-the-service-fabric-resource-provider-to-query-your-cluster) dotyczącymi Azure Portal i Service Fabric dostawcy zasobów.

1. Usuń `dnsName` parametr. (Nie jest to konieczne).

    ```json
    /*
    "dnsName": {
        "type": "string"
    },
    */
    ```

2. Opcjonalnie, jeśli używasz statycznej metody alokacji, możesz dodać parametr statycznego adresu IP. W przypadku używania dynamicznej metody alokacji nie trzeba wykonywać tego kroku.

    ```json
            "internalLBAddress": {
                "type": "string",
                "defaultValue": "10.0.0.250"
            }
    ```

3. Usuń `Microsoft.Network/publicIPAddresses` z zasobów, aby platforma Azure nie utworzyła nowego adresu IP:

    ```json
    /*
    {
        "apiVersion": "[variables('publicIPApiVersion')]",
        "type": "Microsoft.Network/publicIPAddresses",
        "name": "[concat(parameters('lbIPName'),)'-', '0')]",
        "location": "[parameters('computeLocation')]",
        "properties": {
            "dnsSettings": {
                "domainNameLabel": "[parameters('dnsName')]"
            },
            "publicIPAllocationMethod": "Dynamic"        
        },
        "tags": {
            "resourceType": "Service Fabric",
            "clusterName": "[parameters('clusterName')]"
        }
    }, */
    ```

4. Usuń atrybut adresu IP `dependsOn` `Microsoft.Network/loadBalancers` , dlatego nie zależą od tworzenia nowego adresu IP. Dodaj atrybut Sieć wirtualna `dependsOn` , ponieważ usługa równoważenia obciążenia jest teraz zależna od podsieci z sieci wirtualnej:

    ```json
                "apiVersion": "[variables('lbApiVersion')]",
                "type": "Microsoft.Network/loadBalancers",
                "name": "[concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType0Name'))]",
                "location": "[parameters('computeLocation')]",
                "dependsOn": [
                    /*"[concat('Microsoft.Network/publicIPAddresses/',concat(parameters('lbIPName'),'-','0'))]"*/
                    "[concat('Microsoft.Network/virtualNetworks/',parameters('virtualNetworkName'))]"
                ],
    ```

5. Zmień ustawienie usługi równoważenia obciążenia `frontendIPConfigurations` z using na `publicIPAddress` , aby używać podsieci i `privateIPAddress` . `privateIPAddress` używa wstępnie zdefiniowanego statycznego wewnętrznego adresu IP. Aby użyć dynamicznego adresu IP, Usuń `privateIPAddress` element, a następnie zmień `privateIPAllocationMethod` na **dynamiczny**.

    ```json
                "frontendIPConfigurations": [
                        {
                            "name": "LoadBalancerIPConfig",
                            "properties": {
                                /*
                                "publicIPAddress": {
                                    "id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(parameters('lbIPName'),'-','0'))]"
                                } */
                                "subnet" :{
                                    "id": "[variables('subnet0Ref')]"
                                },
                                "privateIPAddress": "[parameters('internalLBAddress')]",
                                "privateIPAllocationMethod": "Static"
                            }
                        }
                    ],
    ```

6. W `Microsoft.ServiceFabric/clusters` zasobie Zmień `managementEndpoint` wartość, aby wskazywała adres wewnętrznego modułu równoważenia obciążenia. Jeśli używasz bezpiecznego klastra, upewnij się, że zmieniasz *http://* na *https://*. (Należy zauważyć, że ten krok dotyczy tylko klastrów Service Fabric. Jeśli używasz zestawu skalowania maszyn wirtualnych, Pomiń ten krok.

    ```json
                    "fabricSettings": [],
                    /*"managementEndpoint": "[concat('http://',reference(concat(parameters('lbIPName'),'-','0')).dnsSettings.fqdn,':',parameters('nt0fabricHttpGatewayPort'))]",*/
                    "managementEndpoint": "[concat('http://',reference(variables('lbID0')).frontEndIPConfigurations[0].properties.privateIPAddress,':',parameters('nt0fabricHttpGatewayPort'))]",
    ```

7. Wdróż szablon:

    ```powershell
    New-AzResourceGroup -Name sfnetworkinginternallb -Location westus

    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkinginternallb -TemplateFile C:\SFSamples\Final\template\_internalonlyLB.json
    ```

Po wdrożeniu moduł równoważenia obciążenia używa prywatnego statycznego adresu IP 10.0.0.250. Jeśli masz inną maszynę w tej samej sieci wirtualnej, możesz przejść do wewnętrznego punktu końcowego [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) . Należy pamiętać, że łączy się on z jednym z węzłów za modułem równoważenia obciążenia.

<a id="internalexternallb"></a>
## <a name="internal-and-external-load-balancer"></a>Wewnętrzny i zewnętrzny moduł równoważenia obciążenia

W tym scenariuszu zaczynasz od istniejącego zewnętrznego modułu równoważenia obciążenia z jednym węzłem, a następnie dodasz wewnętrzny moduł równoważenia obciążenia dla tego samego typu węzła. Port zaplecza dołączony do puli adresów zaplecza może być przypisany tylko do jednego modułu równoważenia obciążenia. Wybierz, który moduł równoważenia obciążenia będzie miał porty aplikacji, a moduł równoważenia obciążenia będzie miał punkty końcowe zarządzania (porty 19000 i 19080). Jeśli punkty końcowe zarządzania są umieszczane w wewnętrznym module równoważenia obciążenia, należy wziąć pod uwagę ograniczenia dostawcy zasobów Service Fabric omówione [wcześniej w artykule](#allowing-the-service-fabric-resource-provider-to-query-your-cluster). W używanym przykładzie punkty końcowe zarządzania pozostają w zewnętrznym module równoważenia obciążenia. Należy również dodać port 80 aplikacji port i umieścić go w wewnętrznym module równoważenia obciążenia.

W klastrze typu dwuwęzłowego jeden typ węzła znajduje się w zewnętrznym module równoważenia obciążenia. Inny typ węzła znajduje się w wewnętrznym module równoważenia obciążenia. Aby można było użyć klastra typu dwuwęzłowego, w utworzonym przez portal dwuwęzłowym szablonie typu (który zawiera dwa moduły równoważenia obciążenia), należy przełączyć drugi moduł równoważenia obciążenia do wewnętrznego modułu równoważenia obciążenia. Aby uzyskać więcej informacji, zobacz sekcję [wewnętrzna usługa równoważenia obciążenia](#internallb) .

1. Dodaj statyczny adres IP wewnętrznego modułu równoważenia obciążenia. (Aby uzyskać uwagi dotyczące korzystania z dynamicznego adresu IP, zobacz wcześniejsze sekcje tego artykułu).

    ```json
            "internalLBAddress": {
                "type": "string",
                "defaultValue": "10.0.0.250"
            }
    ```

2. Dodaj parametr portu 80 aplikacji.

3. Aby dodać wewnętrzne wersje istniejących zmiennych sieciowych, skopiuj i wklej je, a następnie Dodaj "-int" do nazwy:

    ```json
    /* Add internal load balancer networking variables */
            "lbID0-Int": "[resourceId('Microsoft.Network/loadBalancers', concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType0Name'), '-Internal'))]",
            "lbIPConfig0-Int": "[concat(variables('lbID0-Int'),'/frontendIPConfigurations/LoadBalancerIPConfig')]",
            "lbPoolID0-Int": "[concat(variables('lbID0-Int'),'/backendAddressPools/LoadBalancerBEAddressPool')]",
            "lbProbeID0-Int": "[concat(variables('lbID0-Int'),'/probes/FabricGatewayProbe')]",
            "lbHttpProbeID0-Int": "[concat(variables('lbID0-Int'),'/probes/FabricHttpGatewayProbe')]",
            "lbNatPoolID0-Int": "[concat(variables('lbID0-Int'),'/inboundNatPools/LoadBalancerBEAddressNatPool')]",
            /* Internal load balancer networking variables end */
    ```

4. Po rozpoczęciu pracy z szablonem wygenerowanym przez portal, który używa portu aplikacji 80, domyślny szablon portalu dodaje AppPort1 (port 80) w zewnętrznym module równoważenia obciążenia. W takim przypadku Usuń AppPort1 z zewnętrznego modułu równoważenia obciążenia `loadBalancingRules` i sond, aby można było dodać go do wewnętrznego modułu równoważenia obciążenia:

    ```json
    "loadBalancingRules": [
        {
            "name": "LBHttpRule",
            "properties":{
                "backendAddressPool": {
                    "id": "[variables('lbPoolID0')]"
                },
                "backendPort": "[parameters('nt0fabricHttpGatewayPort')]",
                "enableFloatingIP": "false",
                "frontendIPConfiguration": {
                    "id": "[variables('lbIPConfig0')]"            
                },
                "frontendPort": "[parameters('nt0fabricHttpGatewayPort')]",
                "idleTimeoutInMinutes": "5",
                "probe": {
                    "id": "[variables('lbHttpProbeID0')]"
                },
                "protocol": "tcp"
            }
        } /* Remove AppPort1 from the external load balancer.
        {
            "name": "AppPortLBRule1",
            "properties": {
                "backendAddressPool": {
                    "id": "[variables('lbPoolID0')]"
                },
                "backendPort": "[parameters('loadBalancedAppPort1')]",
                "enableFloatingIP": "false",
                "frontendIPConfiguration": {
                    "id": "[variables('lbIPConfig0')]"            
                },
                "frontendPort": "[parameters('loadBalancedAppPort1')]",
                "idleTimeoutInMinutes": "5",
                "probe": {
                    "id": "[concate(variables('lbID0'), '/probes/AppPortProbe1')]"
                },
                "protocol": "tcp"
            }
        }*/

    ],
    "probes": [
        {
            "name": "FabricGatewayProbe",
            "properties": {
                "intervalInSeconds": 5,
                "numberOfProbes": 2,
                "port": "[parameters('nt0fabricTcpGatewayPort')]",
                "protocol": "tcp"
            }
        },
        {
            "name": "FabricHttpGatewayProbe",
            "properties": {
                "intervalInSeconds": 5,
                "numberOfProbes": 2,
                "port": "[parameters('nt0fabricHttpGatewayPort')]",
                "protocol": "tcp"
            }
        } /* Remove AppPort1 from the external load balancer.
        {
            "name": "AppPortProbe1",
            "properties": {
                "intervalInSeconds": 5,
                "numberOfProbes": 2,
                "port": "[parameters('loadBalancedAppPort1')]",
                "protocol": "tcp"
            }
        } */

    ],
    "inboundNatPools": [
    ```

5. Dodaj drugi `Microsoft.Network/loadBalancers` zasób. Wygląda podobnie do wewnętrznego modułu równoważenia obciążenia utworzonego w sekcji [modułu równoważenia obciążenia tylko wewnętrznego](#internallb) , ale używa zmiennych modułu równoważenia obciążenia "-int" i implementuje tylko port 80 aplikacji. Spowoduje to również usunięcie `inboundNatPools` , aby zachować punkty końcowe protokołu RDP w publicznym module równoważenia obciążenia. Jeśli chcesz, aby protokół RDP był włączony w wewnętrznym module równoważenia obciążenia, przejdź `inboundNatPools` z zewnętrznego modułu równoważenia obciążenia do tego wewnętrznego modułu równoważenia obciążenia:

    ```json
            /* Add a second load balancer, configured with a static privateIPAddress and the "-Int" load balancer variables. */
            {
                "apiVersion": "[variables('lbApiVersion')]",
                "type": "Microsoft.Network/loadBalancers",
                /* Add "-Internal" to the name. */
                "name": "[concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType0Name'), '-Internal')]",
                "location": "[parameters('computeLocation')]",
                "dependsOn": [
                    /* Remove public IP dependsOn, add vnet dependsOn
                    "[concat('Microsoft.Network/publicIPAddresses/',concat(parameters('lbIPName'),'-','0'))]"
                    */
                    "[concat('Microsoft.Network/virtualNetworks/',parameters('virtualNetworkName'))]"
                ],
                "properties": {
                    "frontendIPConfigurations": [
                        {
                            "name": "LoadBalancerIPConfig",
                            "properties": {
                                /* Switch from Public to Private IP address
                                */
                                "publicIPAddress": {
                                    "id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(parameters('lbIPName'),'-','0'))]"
                                }
                                */
                                "subnet" :{
                                    "id": "[variables('subnet0Ref')]"
                                },
                                "privateIPAddress": "[parameters('internalLBAddress')]",
                                "privateIPAllocationMethod": "Static"
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
                        /* Add the AppPort rule. Be sure to reference the "-Int" versions of backendAddressPool, frontendIPConfiguration, and the probe variables. */
                        {
                            "name": "AppPortLBRule1",
                            "properties": {
                                "backendAddressPool": {
                                    "id": "[variables('lbPoolID0-Int')]"
                                },
                                "backendPort": "[parameters('loadBalancedAppPort1')]",
                                "enableFloatingIP": "false",
                                "frontendIPConfiguration": {
                                    "id": "[variables('lbIPConfig0-Int')]"
                                },
                                "frontendPort": "[parameters('loadBalancedAppPort1')]",
                                "idleTimeoutInMinutes": "5",
                                "probe": {
                                    "id": "[concat(variables('lbID0-Int'),'/probes/AppPortProbe1')]"
                                },
                                "protocol": "tcp"
                            }
                        }
                    ],
                    "probes": [
                    /* Add the probe for the app port. */
                    {
                            "name": "AppPortProbe1",
                            "properties": {
                                "intervalInSeconds": 5,
                                "numberOfProbes": 2,
                                "port": "[parameters('loadBalancedAppPort1')]",
                                "protocol": "tcp"
                            }
                        }
                    ],
                    "inboundNatPools": [
                    ]
                },
                "tags": {
                    "resourceType": "Service Fabric",
                    "clusterName": "[parameters('clusterName')]"
                }
            },
    ```

6. W obszarze `networkProfile` dla `Microsoft.Compute/virtualMachineScaleSets` zasobu Dodaj wewnętrzną pulę adresów zaplecza:

    ```json
    "loadBalancerBackendAddressPools": [
                                                        {
                                                            "id": "[variables('lbPoolID0')]"
                                                        },
                                                        {
                                                            /* Add internal BE pool */
                                                            "id": "[variables('lbPoolID0-Int')]"
                                                        }
    ],
    ```

7. Wdróż szablon:

    ```powershell
    New-AzResourceGroup -Name sfnetworkinginternalexternallb -Location westus

    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkinginternalexternallb -TemplateFile C:\SFSamples\Final\template\_internalexternalLB.json
    ```

Po wdrożeniu można wyświetlić dwie usługi równoważenia obciążenia w grupie zasobów. W przypadku przeglądania modułów równoważenia obciążenia można zobaczyć publiczny adres IP i punkty końcowe zarządzania (porty 19000 i 19080) przypisane do publicznego adresu IP. Można też zobaczyć statyczny wewnętrzny adres IP i punkt końcowy aplikacji (port 80) przypisany do wewnętrznego modułu równoważenia obciążenia. Oba moduły równoważenia obciążenia używają tej samej puli zaplecza zestawu skalowania maszyn wirtualnych.

## <a name="notes-for-production-workloads"></a>Uwagi dotyczące obciążeń produkcyjnych

Powyższe szablony GitHub są przeznaczone do pracy z domyślną jednostką SKU dla platformy Azure usługa Load Balancer w warstwie Standardowa (program do obsługi modułu równoważenia obciążenia) — podstawowa jednostka SKU. Ten program nie ma umowy SLA, dlatego w przypadku obciążeń produkcyjnych należy używać standardowej jednostki SKU. Aby uzyskać więcej informacji na ten temat, zobacz [Omówienie usługi Azure usługa Load Balancer w warstwie Standardowa](../load-balancer/load-balancer-overview.md). Każdy klaster Service Fabric używający standardowej jednostki SKU dla modułu równoważenia obciążenia musi zapewnić, że każdy typ węzła ma regułę zezwalającą na ruch wychodzący na porcie 443. Jest to niezbędne do ukończenia instalacji klastra, a wszelkie wdrożenia bez takiej reguły zakończą się niepowodzeniem. W powyższym przykładzie usługi równoważenia obciążenia "tylko wewnętrznie" do szablonu należy dodać dodatkowy zewnętrzny moduł równoważenia obciążenia z regułą zezwalającą na ruch wychodzący dla portu 443.

## <a name="next-steps"></a>Następne kroki
[Tworzenie klastra](service-fabric-cluster-creation-via-arm.md)

Po wdrożeniu można wyświetlić dwie usługi równoważenia obciążenia w grupie zasobów. W przypadku przeglądania modułów równoważenia obciążenia można zobaczyć publiczny adres IP i punkty końcowe zarządzania (porty 19000 i 19080) przypisane do publicznego adresu IP. Można też zobaczyć statyczny wewnętrzny adres IP i punkt końcowy aplikacji (port 80) przypisany do wewnętrznego modułu równoważenia obciążenia. Oba moduły równoważenia obciążenia używają tej samej puli zaplecza zestawu skalowania maszyn wirtualnych.
