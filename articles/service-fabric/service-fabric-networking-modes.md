---
title: Konfigurowanie trybów sieciowych dla usługi Container Services
description: Dowiedz się, jak skonfigurować różne tryby sieci obsługiwane przez usługę Azure Service Fabric.
author: athinanthny
ms.topic: conceptual
ms.date: 2/23/2018
ms.author: atsenthi
ms.openlocfilehash: e6174f35bd54b3ca0b2c5240a663369350b30ce8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86241900"
---
# <a name="service-fabric-container-networking-modes"></a>Service Fabric tryby sieci kontenera

Klaster Service Fabric platformy Azure dla usług kontenerów domyślnie używa trybu sieci **NAT** . Gdy więcej niż jedna usługa kontenera nasłuchuje na tym samym porcie, a tryb NAT jest używany, mogą wystąpić błędy wdrażania. Aby obsługiwać wiele usług kontenerów nasłuchujących na tym samym porcie, Service Fabric oferuje tryb **otwartej** sieci (wersje 5,7 i nowsze). W trybie otwartym każda usługa kontenera ma wewnętrzny, dynamicznie przypisany adres IP, który obsługuje wiele usług nasłuchujących na tym samym porcie.  

Jeśli masz jedną usługę kontenera ze statycznym punktem końcowym w manifeście usługi, możesz tworzyć i usuwać nowe usługi przy użyciu trybu otwartego bez błędów wdrażania. Ten sam plik Docker-Compose. yml może być również używany z mapowaniami portów statycznych do tworzenia wielu usług.

Po ponownym uruchomieniu lub przeniesieniu usługi kontenera do innego węzła w klastrze adres IP ulega zmianie. Z tego powodu nie zaleca się używania dynamicznie przypisanego adresu IP do odnajdowania usług kontenerów. Do odnajdowania usług należy używać tylko Usługa nazewnictwa Service Fabric lub usługi DNS. 

>[!WARNING]
>System Azure umożliwia łączną liczbę adresów IP 65 356 dla każdej sieci wirtualnej. Suma liczby węzłów i liczby wystąpień usługi kontenera (używających trybu otwierania) nie może przekraczać 65 356 adresów IP w ramach sieci wirtualnej. W przypadku scenariuszy o dużej gęstości zalecamy użycie trybu sieci NAT. Ponadto inne zależności, takie jak moduł równoważenia obciążenia, będą miały inne [ograniczenia](../azure-resource-manager/management/azure-subscription-service-limits.md) , które należy wziąć pod uwagę. Obecnie testy do 50 adresów IP na węzeł zostały przetestowane i sprawdzone jako stabilne. 
>

## <a name="set-up-open-networking-mode"></a>Konfigurowanie trybu otwierania sieci

1. Skonfiguruj szablon Azure Resource Manager. W sekcji **fabricSettings** zasobu klastra Włącz usługę DNS i dostawcę IP: 

    ```json
    "fabricSettings": [
                {
                    "name": "DnsService",
                    "parameters": [
                       {
                            "name": "IsEnabled",
                            "value": "true"
                      }
                    ]
                },
                {
                    "name": "Hosting",
                    "parameters": [
                      { 
                            "name": "IPProviderEnabled",
                            "value": "true"
                      }
                    ]
                },
                {
                    "name": "Setup",
                    "parameters": [
                    {
                            "name": "ContainerNetworkSetup",
                            "value": "true"
                    }
                    ]
                }
            ],
    ```
    
2. Skonfiguruj sekcję profil sieciowy zasobu zestawu skalowania maszyn wirtualnych. Dzięki temu można skonfigurować wiele adresów IP w każdym węźle klastra. W poniższym przykładzie ustawiono pięć adresów IP na węzeł dla klastra Service Fabric systemu Windows/Linux. W każdym węźle można nasłuchiwać pięciu wystąpień usługi. Aby można było uzyskać dostęp do pięciu adresów IP z Azure Load Balancer, należy zarejestrować pięć adresów IP w puli adresów zaplecza Azure Load Balancer, jak pokazano poniżej.  Należy również dodać zmienne na początku szablonu w sekcji zmienne.

    Dodaj tę sekcję do zmiennych:

    ```json
    "variables": {
        "nicName": "NIC",
        "vmName": "vm",
        "virtualNetworkName": "VNet",
        "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
        "vmNodeType0Name": "[toLower(concat('NT1', variables('vmName')))]",
        "subnet0Name": "Subnet-0",
        "subnet0Prefix": "10.0.0.0/24",
        "subnet0Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet0Name'))]",
        "lbID0": "[resourceId('Microsoft.Network/loadBalancers',concat('LB','-', parameters('clusterName'),'-',variables('vmNodeType0Name')))]",
        "lbIPConfig0": "[concat(variables('lbID0'),'/frontendIPConfigurations/LoadBalancerIPConfig')]",
        "lbPoolID0": "[concat(variables('lbID0'),'/backendAddressPools/LoadBalancerBEAddressPool')]",
        "lbProbeID0": "[concat(variables('lbID0'),'/probes/FabricGatewayProbe')]",
        "lbHttpProbeID0": "[concat(variables('lbID0'),'/probes/FabricHttpGatewayProbe')]",
        "lbNatPoolID0": "[concat(variables('lbID0'),'/inboundNatPools/LoadBalancerBEAddressNatPool')]"
    }
    ```
    
    Dodaj tę sekcję do zasobu zestawu skalowania maszyn wirtualnych:

    ```json   
    "networkProfile": {
                "networkInterfaceConfigurations": [
                  {
                    "name": "[concat(parameters('nicName'), '-0')]",
                    "properties": {
                      "ipConfigurations": [
                        {
                          "name": "[concat(parameters('nicName'),'-',0)]",
                          "properties": {
                            "primary": "true",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "loadBalancerInboundNatPools": [
                              {
                                "id": "[variables('lbNatPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 1)]",
                          "properties": {
                            "primary": "false",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 2)]",
                          "properties": {
                            "primary": "false",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 3)]",
                          "properties": {
                            "primary": "false",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 4)]",
                          "properties": {
                            "primary": "false",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 5)]",
                          "properties": {
                            "primary": "false",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        }
                      ],
                      "primary": true
                    }
                  }
                ]
              }
   ```
 
3. W przypadku klastrów systemu Windows należy skonfigurować regułę sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń) platformy Azure otwierającą port UDP/53 dla sieci wirtualnej przy użyciu następujących wartości:

   |Ustawienie |Wartość |
   | --- | --- |
   |Priorytet |2000 |
   |Nazwa |Custom_Dns  |
   |Element źródłowy |VirtualNetwork |
   |Miejsce docelowe | VirtualNetwork |
   |Usługa | DNS (UDP/53) |
   |Akcja | Zezwalaj  |

4. Określ tryb sieci w manifeście aplikacji dla każdej usługi: `<NetworkConfig NetworkType="Open">` . **Otwórz** tryb sieci wyniki w usłudze przy użyciu dedykowanego adresu IP. Jeśli tryb nie jest określony, domyślnie jest używany tryb **translatora adresów sieciowych** . W poniższym przykładzie manifestu `NodeContainerServicePackage1` `NodeContainerServicePackage2` usługi i mogą nasłuchiwać każdego nasłuchiwania na tym samym porcie (obie usługi nasłuchują na `Endpoint1` ). W przypadku określenia trybu otwierania sieci należy `PortBinding` określić konfiguracje.

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <ApplicationManifest ApplicationTypeName="NodeJsApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
      <Description>Calculator Application</Description>
      <Parameters>
        <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
        <Parameter Name="MyCpuShares" DefaultValue="3"></Parameter>
      </Parameters>
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeContainerServicePackage1" ServiceManifestVersion="1.0"/>
        <Policies>
          <ContainerHostPolicies CodePackageRef="NodeContainerService1.Code" Isolation="hyperv">
           <NetworkConfig NetworkType="Open"/>
          </ContainerHostPolicies>
        </Policies>
      </ServiceManifestImport>
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeContainerServicePackage2" ServiceManifestVersion="1.0"/>
        <Policies>
          <ContainerHostPolicies CodePackageRef="NodeContainerService2.Code" Isolation="default">
            <NetworkConfig NetworkType="Open"/>
          </ContainerHostPolicies>
        </Policies>
      </ServiceManifestImport>
    </ApplicationManifest>
    ```

    Można mieszać i dopasowywać różne tryby sieci między usługami w aplikacji dla klastra systemu Windows. Niektóre usługi mogą korzystać z trybu otwartego, podczas gdy inne korzystają z trybu translatora adresów sieciowych. Gdy usługa jest skonfigurowana do korzystania z trybu NAT, port, na którym nasłuchuje usługa, musi być unikatowa.

    >[!NOTE]
    >W klastrach systemu Linux Mieszanie trybów sieciowych dla różnych usług nie jest obsługiwane. 
    >

5. Po wybraniu trybu **otwartego** definicja **punktu końcowego** w manifeście usługi powinna jawnie wskazywać pakiet kodu odpowiadający punktowi końcowemu, nawet jeśli pakiet usługi zawiera tylko jeden pakiet kodu. 
   
   ```xml
   <Resources>
     <Endpoints>
       <Endpoint Name="ServiceEndpoint" Protocol="http" Port="80" CodePackageRef="Code"/>
     </Endpoints>
   </Resources>
   ```
   
6. W przypadku systemu Windows ponowne uruchomienie maszyny wirtualnej spowoduje odtworzenie otwartej sieci. Jest to ograniczenie podstawowego problemu w stosie sieci. Domyślnym zachowaniem jest ponowne utworzenie sieci. Jeśli takie zachowanie musi zostać wyłączone, można użyć poniższej konfiguracji, a następnie uaktualnić konfigurację.

```json
"fabricSettings": [
                {
                    "name": "Setup",
                    "parameters": [
                    {
                            "name": "SkipContainerNetworkResetOnReboot",
                            "value": "true"
                    }
                    ]
                }
            ],          
 ``` 
 
## <a name="next-steps"></a>Następne kroki
* [Informacje o modelu aplikacji usługi Service Fabric](service-fabric-application-model.md)
* [Dowiedz się więcej o zasobach manifestu usługi Service Fabric](./service-fabric-service-manifest-resources.md)
* [Wdrażanie kontenera systemu Windows w celu Service Fabric w systemie Windows Server 2016](service-fabric-get-started-containers.md)
* [Wdrażanie kontenera platformy Docker w celu Service Fabric w systemie Linux](service-fabric-get-started-containers-linux.md)
