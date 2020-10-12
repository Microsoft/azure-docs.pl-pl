---
title: Usługa Azure Service Fabric Konfigurowanie zwrotnego serwera proxy
description: Dowiedz się, jak skonfigurować i skonfigurować usługę zwrotnego serwera proxy dla aplikacji Service Fabric platformy Azure.
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: pepogors
ms.openlocfilehash: f8a9025a50b2815f0e6030e7baf317b261c8c462
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86256335"
---
# <a name="set-up-and-configure-reverse-proxy-in-azure-service-fabric"></a>Konfigurowanie i Konfigurowanie zwrotnego serwera proxy na platformie Azure Service Fabric
Zwrotny serwer proxy to opcjonalna usługa Service Fabric platformy Azure, która pomaga mikrousługom działającym w klastrze Service Fabric odnajdywania i komunikowania się z innymi usługami, które mają punkty końcowe http. Aby dowiedzieć się więcej, zobacz [zwrotny serwer proxy na platformie Azure Service Fabric](service-fabric-reverseproxy.md). W tym artykule opisano sposób konfigurowania i konfigurowania zwrotnego serwera proxy w klastrze. 

## <a name="enable-reverse-proxy-using-azure-portal"></a>Włącz zwrotny serwer proxy przy użyciu Azure Portal

Azure Portal udostępnia opcję włączania zwrotnego serwera proxy podczas tworzenia nowego klastra Service Fabric. Nie można uaktualnić istniejącego klastra do korzystania z zwrotnego serwera proxy za pomocą portalu. 

Aby skonfigurować zwrotny serwer proxy podczas [tworzenia klastra przy użyciu Azure Portal](./service-fabric-cluster-creation-via-portal.md), należy wykonać następujące czynności:

1. W **kroku 2: Konfiguracja klastra**, w obszarze **Konfiguracja typu węzła**, zaznacz opcję **Włącz zwrotny serwer proxy**.

   ![Włącz zwrotny serwer proxy w portalu](./media/service-fabric-reverseproxy-setup/enable-rp-portal.png)
2. Obowiązkowe Aby skonfigurować bezpieczny zwrotny serwer proxy, należy skonfigurować certyfikat TLS/SSL. W **kroku 3: zabezpieczenia**, w obszarze **Konfigurowanie ustawień zabezpieczeń klastra**w obszarze **Typ konfiguracji**wybierz opcję **niestandardowy**. Następnie w obszarze **certyfikat protokołu SSL zwrotnego serwera**, wybierz opcję **Dołącz certyfikat SSL dla zwrotnego serwera proxy** i wprowadź szczegóły certyfikatu.

   ![Konfigurowanie bezpiecznego zwrotnego serwera proxy w portalu](./media/service-fabric-reverseproxy-setup/configure-rp-certificate-portal.png)

   Jeśli nie skonfigurujesz zwrotnego serwera proxy przy użyciu certyfikatu podczas tworzenia klastra, możesz zrobić to później za pomocą szablonu Menedżer zasobów dla grupy zasobów klastra. Aby dowiedzieć się więcej, zobacz [Włączanie zwrotnego serwera proxy za pomocą szablonów Azure Resource Manager](#enable-reverse-proxy-via-azure-resource-manager-templates).

## <a name="enable-reverse-proxy-via-azure-resource-manager-templates"></a>Włącz zwrotny serwer proxy za pomocą szablonów Azure Resource Manager

W przypadku klastrów na platformie Azure można użyć szablonu Azure Resource Manager, aby włączyć zwrotny serwer proxy w programie Service Fabric. Zwrotny serwer proxy można włączyć podczas tworzenia klastra lub włączania go przez aktualizację klastra w późniejszym czasie. 

W przypadku nowego klastra można [utworzyć niestandardowy szablon Menedżer zasobów](service-fabric-cluster-creation-via-arm.md) lub użyć przykładowego szablonu. 

Możesz znaleźć przykładowe szablony Menedżer zasobów, które mogą ułatwić skonfigurowanie bezpiecznego zwrotnego serwera proxy dla klastra platformy Azure w [przykładowych szablonach bezpiecznych zwrotnych serwerów proxy](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Reverse-Proxy-Sample) w witrynie GitHub. Zapoznaj się z tematem [Konfigurowanie zwrotnego serwera proxy HTTPS w zabezpieczonym klastrze](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Reverse-Proxy-Sample/README.md#configure-https-reverse-proxy-in-a-secure-cluster) w pliku Readme, aby uzyskać instrukcje i szablony służące do konfigurowania bezpiecznego zwrotnego serwera proxy z certyfikatem i do obsługi przerzucania certyfikatów.

W przypadku istniejącego klastra można wyeksportować szablon Menedżer zasobów dla grupy zasobów klastra przy użyciu [Azure Portal](../azure-resource-manager/templates/export-template-portal.md), [programu PowerShell](../azure-resource-manager/management/manage-resources-powershell.md)lub [interfejsu wiersza polecenia platformy Azure](../azure-resource-manager/management/manage-resources-cli.md).

Po utworzeniu szablonu Menedżer zasobów można włączyć zwrotny serwer proxy, wykonując następujące czynności:

1. Zdefiniuj port dla zwrotnego serwera proxy w [sekcji Parametry](../azure-resource-manager/templates/template-syntax.md) szablonu.

    ```json
    "SFReverseProxyPort": {
        "type": "int",
        "defaultValue": 19081,
        "metadata": {
            "description": "Endpoint for Service Fabric Reverse proxy"
        }
    },
    ```
2. Określ port dla każdego z obiektów NodeType w [sekcji Typ zasobu](../azure-resource-manager/templates/template-syntax.md) [**Microsoft. servicefabric/klastrów**](/azure/templates/microsoft.servicefabric/clusters) .

    Port jest identyfikowany przez nazwę parametru, reverseProxyEndpointPort.

    ```json
    {
        "apiVersion": "2016-09-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
       "nodeTypes": [
          {
           ...
           "reverseProxyEndpointPort": "[parameters('SFReverseProxyPort')]",
           ...
          },
        ...
        ],
        ...
    }
    ```
3. Aby skonfigurować certyfikaty TLS/SSL na porcie dla zwrotnego serwera proxy, Dodaj certyfikat do właściwości ***reverseProxyCertificate*** w [sekcji Typ zasobu](../azure-resource-manager/templates/template-syntax.md) **Microsoft. servicefabric/** Reports.

    ```json
    {
        "apiVersion": "2016-09-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            "[concat('Microsoft.Storage/storageAccounts/', parameters('supportLogStorageAccountName'))]"
        ],
        "properties": {
            ...
            "reverseProxyCertificate": {
                "thumbprint": "[parameters('sfReverseProxyCertificateThumbprint')]",
                "x509StoreName": "[parameters('sfReverseProxyCertificateStoreName')]"
            },
            ...
            "clusterState": "Default",
        }
    }
    ```

### <a name="supporting-a-reverse-proxy-certificate-thats-different-from-the-cluster-certificate"></a>Obsługa certyfikatu zwrotnego serwera proxy, który różni się od certyfikatu klastra
 Jeśli certyfikat zwrotnego serwera proxy różni się od certyfikatu, który zabezpiecza klaster, wówczas wcześniej określony certyfikat powinien zostać zainstalowany na maszynie wirtualnej i dodany do listy kontroli dostępu (ACL), tak aby Service Fabric mógł uzyskać do niej dostęp. Można to zrobić w [sekcji Typ zasobu](../azure-resource-manager/templates/template-syntax.md) [**Microsoft. COMPUTE/virtualMachineScaleSets**](/azure/templates/microsoft.compute/virtualmachinescalesets) . Na potrzeby instalacji Dodaj certyfikat do osProfile. Sekcja rozszerzenia szablonu może zaktualizować certyfikat na liście ACL.

  ```json
  {
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    ....
      "osProfile": {
          "adminPassword": "[parameters('adminPassword')]",
          "adminUsername": "[parameters('adminUsername')]",
          "computernamePrefix": "[parameters('vmNodeType0Name')]",
          "secrets": [
            {
              "sourceVault": {
                "id": "[parameters('sfReverseProxySourceVaultValue')]"
              },
              "vaultCertificates": [
                {
                  "certificateStore": "[parameters('sfReverseProxyCertificateStoreValue')]",
                  "certificateUrl": "[parameters('sfReverseProxyCertificateUrlValue')]"
                }
              ]
            }
          ]
        }
   ....
   "extensions": [
          {
              "name": "[concat(parameters('vmNodeType0Name'),'_ServiceFabricNode')]",
              "properties": {
                      "type": "ServiceFabricNode",
                      "autoUpgradeMinorVersion": false,
                      ...
                      "publisher": "Microsoft.Azure.ServiceFabric",
                      "settings": {
                        "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                        "nodeTypeRef": "[parameters('vmNodeType0Name')]",
                        "dataPath": "D:\\\\SvcFab",
                        "durabilityLevel": "Bronze",
                        "testExtension": true,
                        "reverseProxyCertificate": {
                          "thumbprint": "[parameters('sfReverseProxyCertificateThumbprint')]",
                          "x509StoreName": "[parameters('sfReverseProxyCertificateStoreValue')]"
                        },
                  },
                  "typeHandlerVersion": "1.0"
              }
          },
      ]
    }
  ```
> [!NOTE]
> W przypadku używania certyfikatów, które różnią się od certyfikatu klastra w celu włączenia zwrotnego serwera proxy w istniejącym klastrze, należy zainstalować certyfikat zwrotnego serwera proxy i zaktualizować listę kontroli dostępu w klastrze przed włączeniem zwrotnego serwera proxy. Ukończ wdrożenie [szablonu Azure Resource Manager](service-fabric-cluster-creation-via-arm.md) przy użyciu powyższych ustawień przed rozpoczęciem wdrażania, aby włączyć zwrotny serwer proxy w krokach 1-3.

## <a name="enable-reverse-proxy-on-standalone-clusters"></a>Włącz zwrotny serwer proxy w klastrach autonomicznych

W przypadku klastrów autonomicznych należy włączyć zwrotny serwer proxy w ClusterConfig.jspliku. Można włączyć zwrotny serwer proxy podczas tworzenia klastra lub uaktualnić konfigurację dla istniejącego klastra. Aby dowiedzieć się więcej o ustawieniach dostępnych w ClusterConfig.jsna plikach, zobacz [autonomiczne ustawienia klastra](./service-fabric-cluster-manifest.md).

Poniższe kroki pokazują ustawienia umożliwiające włączenie zwrotnego serwera proxy oraz, opcjonalnie, aby zabezpieczyć zwrotny serwer proxy za pomocą certyfikatu X. 509. 

1. Aby włączyć zwrotny serwer proxy, należy ustawić wartość **reverseProxyEndpointPort** dla typu węzła w obszarze **Właściwości** w konfiguracji klastra. Poniższy kod JSON przedstawia ustawienie portu punktu końcowego zwrotnego serwera proxy na 19081 dla węzłów o typie "NodeType0":

   ```json
       "properties": {
          ... 
           "nodeTypes": [
               {
                   "name": "NodeType0",
                   ...
                   "reverseProxyEndpointPort": "19081",
                   ...
               }
           ],
          ...
       }
   ```
2. Obowiązkowe W przypadku bezpiecznego zwrotnego serwera proxy Skonfiguruj certyfikat w sekcji **zabezpieczenia** w obszarze **Właściwości**. 
   - W przypadku środowiska programistycznego lub testowego można użyć ustawienia **ReverseProxyCertificate** :

      ```json
          "properties": {
              ...
              "security": {
                  ...
                  "CertificateInformation": {
                      ...
                      "ReverseProxyCertificate": {
                          "Thumbprint": "[Thumbprint]",
                          "ThumbprintSecondary": "[Thumbprint]",
                          "X509StoreName": "My"
                      },
                      ...
                  }
              },
              ...
          }
      ```
   - Dla środowiska produkcyjnego zalecane jest ustawienie **ReverseProxyCertificateCommonNames** :

      ```json
          "properties": {
              ...
              "security": {
                  ...
                  "CertificateInformation": {
                      ...
                      "ReverseProxyCertificateCommonNames": {
                        "CommonNames": [
                            {
                              "CertificateCommonName": "[CertificateCommonName]"
                            }
                          ],
                          "X509StoreName": "My"
                      },
                      ...
                  }
              },
              ...
          }
      ```

   Aby dowiedzieć się więcej na temat konfigurowania i zarządzania certyfikatami dla klastra autonomicznego, a także bardziej szczegółowych informacji na temat konfigurowania certyfikatów używanych do zabezpieczenia zwrotnego serwera proxy, zobacz [zabezpieczenia oparte na certyfikatach x509](./service-fabric-windows-cluster-x509-security.md).

Po zmodyfikowaniu ClusterConfig.jsw pliku, aby włączyć zwrotny serwer proxy, postępuj zgodnie z instrukcjami w temacie [Uaktualnij konfigurację klastra](service-fabric-cluster-config-upgrade-windows-server.md) , aby wypchnąć zmiany do klastra.


## <a name="expose-reverse-proxy-on-a-public-port-through-azure-load-balancer"></a>Uwidaczniaj zwrotny serwer proxy na porcie publicznym za pomocą Azure Load Balancer

Aby rozwiązać zwrotny serwer proxy spoza klastra platformy Azure, skonfiguruj reguły Azure Load Balancer i sondę kondycji platformy Azure dla portu zwrotnego serwera proxy. Kroki te można wykonać przy użyciu Azure Portal lub szablonu Menedżer zasobów w dowolnym momencie po utworzeniu klastra. 

> [!WARNING]
> Po skonfigurowaniu portu zwrotnego serwera proxy w Load Balancer wszystkie mikrousługi w klastrze, które uwidaczniają punkt końcowy HTTP, są adresowane spoza klastra. Oznacza to, że mikrousługi mające być wewnętrzne mogą być odnajdywane przez określonego złośliwego użytkownika. Jest to potencjalnie przyczyną poważnych luk w zabezpieczeniach, które mogą zostać wykorzystane; na przykład:
>
> * Złośliwy użytkownik może uruchomić atak typu "odmowa usługi", wielokrotnie wywołując usługę wewnętrzną, która nie ma wystarczająco niezawodnej powierzchni ataku.
> * Złośliwy użytkownik może dostarczyć wadliwe pakiety do usługi wewnętrznej, powodując niezamierzone zachowanie.
> * Usługa, która ma być wewnętrzna, może zwracać informacje prywatne lub poufne, które nie są przeznaczone do ujawnienia usług poza klastrem, dzięki czemu ujawniają te poufne informacje złośliwemu użytkownikowi. 
>
> Pamiętaj, aby w pełni zrozumieć i wyeliminować potencjalne konsekwencje dotyczące zabezpieczeń klastra i uruchomione na nim aplikacje przed przystąpieniem do publicznego przywróceniu portu serwera proxy. 
>

Jeśli chcesz uwidocznić odwrotny serwer proxy publicznie dla klastra autonomicznego, sposób, w jaki to zrobisz, będzie zależny od systemu obsługującego klaster i wykraczający poza zakres tego artykułu. Powyższe ostrzeżenie dotyczące ujawniania publicznie zwrotnego serwera proxy nadal ma zastosowanie.

### <a name="expose-the-reverse-proxy-using-azure-portal"></a>Uwidacznianie zwrotnego serwera proxy przy użyciu Azure Portal 

1. Na Azure Portal kliknij grupę zasobów klastra, a następnie kliknij moduł równoważenia obciążenia dla klastra.
2. Aby dodać sondę kondycji dla portu zwrotnego serwera proxy, w lewym okienku okna modułu równoważenia obciążenia w obszarze **Ustawienia**kliknij pozycję **sondy kondycji**. Następnie kliknij pozycję **Dodaj** w górnej części okna sondy kondycji i wprowadź szczegóły portu zwrotnego serwera proxy, a następnie kliknij przycisk **OK**. Domyślnie port zwrotnego serwera proxy to 19081, chyba że został zmieniony podczas tworzenia klastra.

   ![Konfigurowanie sondy kondycji zwrotnego serwera proxy](./media/service-fabric-reverseproxy-setup/lb-rp-probe.png)
3. Aby dodać regułę Load Balancer, aby uwidocznić port zwrotnego serwera proxy, w lewym okienku okna modułu równoważenia obciążenia w obszarze **Ustawienia**kliknij pozycję **reguły równoważenia obciążenia**. Następnie kliknij pozycję **Dodaj** w górnej części okna reguły równoważenia obciążenia i wprowadź szczegóły dla portu zwrotnego serwera proxy. Upewnij się, że wartość **portu** jest ustawiona na port, na którym ma być uwidoczniony zwrotny serwer proxy, wartość **portu zaplecza** do portu ustawionego po włączeniu zwrotnego serwera proxy oraz wartość **sondy** kondycji do sondy kondycji skonfigurowanej w poprzednim kroku. W razie potrzeby ustaw inne pola i kliknij przycisk **OK**.

   ![Skonfiguruj regułę modułu równoważenia obciążenia dla zwrotnego serwera proxy](./media/service-fabric-reverseproxy-setup/lb-rp-rule.png)

### <a name="expose-the-reverse-proxy-via-resource-manager-templates"></a>Uwidacznianie zwrotnego serwera proxy za pomocą szablonów Menedżer zasobów

Poniższy kod JSON odwołuje się do tego samego szablonu, który jest używany w funkcji [Włącz zwrotny serwer proxy za pomocą szablonów Azure Resource Manager](#enable-reverse-proxy-via-azure-resource-manager-templates). Zapoznaj się z tą sekcją dokumentu, aby uzyskać informacje na temat sposobu tworzenia szablonu Menedżer zasobów lub eksportowania szablonu dla istniejącego klastra.  Zmiany są wprowadzane w [sekcji Typ zasobu](../azure-resource-manager/templates/template-syntax.md) [**Microsoft. Network/loadBalancers**](/azure/templates/microsoft.network/loadbalancers) .

```json
{
    "apiVersion": "[variables('lbApiVersion')]",
    "type": "Microsoft.Network/loadBalancers",
    ...
    ...
    "loadBalancingRules": [
        ...
        {
            "name": "LBSFReverseProxyRule",
            "properties": {
                "backendAddressPool": {
                    "id": "[variables('lbPoolID0')]"
                },
                "backendPort": "[parameters('SFReverseProxyPort')]",
                "enableFloatingIP": "false",
                "frontendIPConfiguration": {
                    "id": "[variables('lbIPConfig0')]"
                },
                "frontendPort": "[parameters('SFReverseProxyPort')]",
                "idleTimeoutInMinutes": "5",
                "probe": {
                    "id": "[concat(variables('lbID0'),'/probes/SFReverseProxyProbe')]"
                },
                "protocol": "tcp"
            }
        }
    ],
    "probes": [
        ...
        {
            "name": "SFReverseProxyProbe",
            "properties": {
                "intervalInSeconds": 5,
                "numberOfProbes": 2,
                "port":     "[parameters('SFReverseProxyPort')]",
                "protocol": "tcp"
            }
        }  
    ]
}
```


## <a name="customize-reverse-proxy-behavior-using-fabric-settings"></a>Dostosowywanie zachowania zwrotnego serwera proxy przy użyciu ustawień sieci szkieletowej

Można dostosować zachowanie zwrotnego serwera proxy za pomocą ustawień sieci szkieletowej w szablonie Menedżer zasobów dla klastrów hostowanych na platformie Azure lub w pliku ClusterConfig.jsw przypadku klastrów autonomicznych. Ustawienia kontrolujące zachowanie zwrotnego serwera proxy znajdują się w sekcji [**elementu applicationgateway/http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) w sekcji **fabricSettings** w sekcji **Właściwości** klastra. 

Na przykład można ustawić wartość **DefaultHttpRequestTimeout** , aby ustawić limit czasu żądań na zwrotny serwer proxy do 180 sekund, jak w poniższym kodzie JSON:

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http",
               "parameters": [
                 {
                   "name": "DefaultHttpRequestTimeout",
                   "value": "180"
                 }
               ]
             }
           ],
           ...
   }
   ``` 

Aby uzyskać więcej informacji na temat aktualizowania ustawień sieci szkieletowej dla klastrów platformy Azure, zobacz [Dostosowywanie ustawień klastra przy użyciu szablonów Menedżer zasobów](service-fabric-cluster-config-upgrade-azure.md). W przypadku klastrów autonomicznych zobacz [Dostosowywanie ustawień klastra dla klastrów autonomicznych](service-fabric-cluster-config-upgrade-windows-server.md). 

Kilka ustawień sieci szkieletowej służy do nawiązywania bezpiecznej komunikacji między zwrotnym serwerem proxy a usługami. Aby uzyskać szczegółowe informacje na temat tych ustawień, zobacz [nawiązywanie połączenia z bezpieczną usługą przy użyciu zwrotnego serwera proxy](service-fabric-reverseproxy-configure-secure-communication.md).

## <a name="next-steps"></a>Następne kroki
* [Skonfiguruj przekazywanie do bezpiecznej usługi HTTP przy użyciu zwrotnego serwera proxy](service-fabric-reverseproxy-configure-secure-communication.md)
* Aby zapoznać się z opcjami konfiguracji zwrotnego serwera proxy, zobacz [sekcję elementu applicationgateway/http w temacie Dostosowywanie ustawień klastra Service Fabric](service-fabric-cluster-fabric-settings.md#applicationgatewayhttp).
