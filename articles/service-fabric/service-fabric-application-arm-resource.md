---
title: Wdrażanie i uaktualnianie przy użyciu Azure Resource Manager
description: Dowiedz się, jak wdrażać aplikacje i usługi w klastrze Service Fabric przy użyciu szablonu Azure Resource Manager.
ms.topic: conceptual
ms.date: 12/06/2017
ms.openlocfilehash: bb866eb24fb1b286f496bad9845d1ee557baa221
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94681673"
---
# <a name="manage-applications-and-services-as-azure-resource-manager-resources"></a>Zarządzanie aplikacjami i usługami jako zasobami Azure Resource Manager

Aplikacje i usługi można wdrożyć w klastrze Service Fabric za pośrednictwem Azure Resource Manager. Oznacza to, że zamiast wdrażać aplikacje i zarządzać nimi za pomocą programu PowerShell lub interfejsu wiersza polecenia, a następnie zaczekać, aż klaster będzie gotowy, możesz teraz wyrazić aplikacje i usługi w formacie JSON i wdrożyć je w tym samym szablonie Menedżer zasobów co klaster. Proces rejestracji aplikacji, aprowizacji i wdrożenia odbywa się w jednym kroku.

Jest to zalecany sposób wdrożenia wszelkich aplikacji do zarządzania konfiguracją, nadzorem lub klastrem, które są wymagane w klastrze. Obejmuje to [aplikację aranżacji poprawek](service-fabric-patch-orchestration-application.md), licznik alarmów lub wszystkie aplikacje, które muszą działać w klastrze przed wdrożeniem innych aplikacji lub usług. 

W razie potrzeby Zarządzaj aplikacjami jako zasobami Menedżer zasobów, aby zwiększyć:
* Dziennik inspekcji: Menedżer zasobów przeprowadza inspekcję każdej operacji i utrzymuje szczegółowy *Dziennik aktywności* , który może pomóc w śledzeniu wszelkich zmian wprowadzonych w tych aplikacjach i w klastrze.
* Kontrola dostępu oparta na rolach (Azure RBAC): zarządzanie dostępem do klastrów oraz aplikacji wdrożonych w klastrze można wykonać za pomocą tego samego szablonu Menedżer zasobów.
* Azure Resource Manager (za pośrednictwem Azure Portal) stanie się jednym zatrzymywaniem do zarządzania klastrem i krytycznymi wdrożeniami aplikacji.

Poniższy fragment kodu przedstawia różne rodzaje zasobów, którymi można zarządzać za pomocą szablonu:

```json
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```


## <a name="add-a-new-application-to-your-resource-manager-template"></a>Dodawanie nowej aplikacji do szablonu Menedżer zasobów

1. Przygotuj szablon Menedżer zasobów klastra do wdrożenia. Aby uzyskać więcej informacji na ten temat [, zobacz Tworzenie klastra Service Fabric przy użyciu Azure Resource Manager](service-fabric-cluster-creation-via-arm.md) .
2. Zastanów się nad niektórymi aplikacjami, które planujesz wdrożyć w klastrze. Czy istnieją jakieś, które będą zawsze uruchamiane, czy inne aplikacje mogą przyjmować zależności? Czy planujesz wdrożenie wszelkich aplikacji do zarządzania klastrami lub konfiguracji? Te różne aplikacje są najlepiej zarządzane za pośrednictwem szablonu Menedżer zasobów, jak opisano powyżej. 
3. Po ustaleniu aplikacji, które mają zostać wdrożone w ten sposób, aplikacje muszą być spakowane, spakowane i umieszczone w udziale plików. Udział musi być dostępny za pomocą punktu końcowego REST, aby Azure Resource Manager do użycia podczas wdrażania.
4. W szablonie Menedżer zasobów poniżej deklaracji klastra opisz właściwości poszczególnych aplikacji. Te właściwości obejmują liczbę replik lub wystąpień oraz wszystkie łańcuchy zależności między zasobami (innymi aplikacjami lub usługami). Aby uzyskać listę kompleksowych właściwości, zobacz [specyfikację Swagger interfejsu API REST](https://aka.ms/sfrpswaggerspec). Należy zauważyć, że nie zastępuje to manifestów aplikacji ani usług, ale zamiast tego opisuje niektóre z nich w ramach szablonu Menedżer zasobów klastra. Oto przykładowy szablon, który obejmuje wdrożenie *Service1* usługi bezstanowej i usługi stanowej *Językowej2* w ramach *Application1*:

   ```json
   {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "clusterName": {
        "type": "string",
        "defaultValue": "Cluster",
        "metadata": {
          "description": "Name of your cluster - Between 3 and 23 characters. Letters and numbers only."
        }
      },
      "applicationTypeName": {
        "type": "string",
        "defaultValue": "ApplicationType",
        "metadata": {
          "description": "The application type name."
        }
      },
      "applicationTypeVersion": {
        "type": "string",
        "defaultValue": "1",
        "metadata": {
          "description": "The application type version."
        }
      },
      "appPackageUrl": {
        "type": "string",
        "metadata": {
          "description": "The URL to the application package sfpkg file."
        }
      },
      "applicationName": {
        "type": "string",
        "defaultValue": "Application1",
        "metadata": {
          "description": "The name of the application resource."
        }
      },
      "serviceName": {
        "type": "string",
        "defaultValue": "Application1~Service1",
        "metadata": {
          "description": "The name of the service resource in the format of {applicationName}~{serviceName}."
        }
      },
      "serviceTypeName": {
        "type": "string",
        "defaultValue": "Service1Type",
        "metadata": {
          "description": "The name of the service type."
        }
      },
      "serviceName2": {
        "type": "string",
        "defaultValue": "Application1~Service2",
        "metadata": {
          "description": "The name of the service resource in the format of {applicationName}~{serviceName}."
        }
      },
      "serviceTypeName2": {
        "type": "string",
        "defaultValue": "Service2Type",
        "metadata": {
          "description": "The name of the service type."
        }
      }
    },
    "variables": {
      "clusterLocation": "[resourcegroup().location]"
    },
    "resources": [
      {
        "apiVersion": "2019-03-01",
        "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [],
        "properties": {
          "provisioningState": "Default"
        }
      },
      {
        "apiVersion": "2019-03-01",
        "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [
          "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applicationTypes/', parameters('applicationTypeName'))]"
        ],
        "properties": {
          "provisioningState": "Default",
          "appPackageUrl": "[parameters('appPackageUrl')]"
        }
      },
      {
        "apiVersion": "2019-03-01",
        "type": "Microsoft.ServiceFabric/clusters/applications",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [
          "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applicationTypes/', parameters('applicationTypeName'), '/versions/', parameters('applicationTypeVersion'))]"
        ],
        "properties": {
          "provisioningState": "Default",
          "typeName": "[parameters('applicationTypeName')]",
          "typeVersion": "[parameters('applicationTypeVersion')]",
          "parameters": {},
          "upgradePolicy": {
            "upgradeReplicaSetCheckTimeout": "01:00:00.0",
            "forceRestart": "false",
            "rollingUpgradeMonitoringPolicy": {
              "healthCheckWaitDuration": "00:02:00.0",
              "healthCheckStableDuration": "00:05:00.0",
              "healthCheckRetryTimeout": "00:10:00.0",
              "upgradeTimeout": "01:00:00.0",
              "upgradeDomainTimeout": "00:20:00.0"
            },
            "applicationHealthPolicy": {
              "considerWarningAsError": "false",
              "maxPercentUnhealthyDeployedApplications": "50",
              "defaultServiceTypeHealthPolicy": {
                "maxPercentUnhealthyServices": "50",
                "maxPercentUnhealthyPartitionsPerService": "50",
                "maxPercentUnhealthyReplicasPerPartition": "50"
              }
            }
          }
        }
      },
      {
        "apiVersion": "2019-03-01",
        "type": "Microsoft.ServiceFabric/clusters/applications/services",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [
          "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applications/', parameters('applicationName'))]"
        ],
        "properties": {
          "provisioningState": "Default",
          "serviceKind": "Stateless",
          "serviceTypeName": "[parameters('serviceTypeName')]",
          "instanceCount": "-1",
          "partitionDescription": {
            "partitionScheme": "Singleton"
          },
          "correlationScheme": [],
          "serviceLoadMetrics": [],
          "servicePlacementPolicies": []
        }
      },
      {
        "apiVersion": "2019-03-01",
        "type": "Microsoft.ServiceFabric/clusters/applications/services",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName2'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [
          "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applications/', parameters('applicationName'))]"
        ],
        "properties": {
          "provisioningState": "Default",
          "serviceKind": "Stateful",
          "serviceTypeName": "[parameters('serviceTypeName2')]",
          "targetReplicaSetSize": "3",
          "minReplicaSetSize": "2",
          "replicaRestartWaitDuration": "00:01:00.0",
          "quorumLossWaitDuration": "00:02:00.0",
          "standByReplicaKeepDuration": "00:00:30.0",
          "partitionDescription": {
            "partitionScheme": "UniformInt64Range",
            "count": "5",
            "lowKey": "1",
            "highKey": "5"
          },
          "hasPersistedState": "true",
          "correlationScheme": [],
          "serviceLoadMetrics": [],
          "servicePlacementPolicies": [],
          "defaultMoveCost": "Low"
        }
      }
    ]
   }
   ```

   > [!NOTE] 
   > Wartość *apiVersion* musi być ustawiona na wartość `"2019-03-01"` . Ten szablon można również wdrożyć niezależnie od klastra, o ile klaster został już wdrożony.

5. Wdrażanie! 

## <a name="remove-service-fabric-resource-provider-application-resource"></a>Usuń zasób aplikacji dostawcy zasobów Service Fabric
Poniższe polecenie spowoduje wyzwolenie pakietu aplikacji, który zostanie nieobsługiwany z klastra. spowoduje to wyczyszczenie użytego miejsca na dysku:
```powershell
Get-AzureRmResource -ResourceId /subscriptions/{sid}/resourceGroups/{rg}/providers/Microsoft.ServiceFabric/clusters/{cluster}/applicationTypes/{apptType}/versions/{version} -ApiVersion "2019-03-01" | Remove-AzureRmResource -Force -ApiVersion "2017-07-01-preview"
```
Po prostu usunięcie usługi Microsoft. servicefabric/klastrów/aplikacji z szablonu ARM nie spowoduje anulowania aprowizacji aplikacji

>[!NOTE]
> Po zakończeniu usuwania nie powinna już być widoczna wersja pakietu w SFX lub ARM. Nie można usunąć zasobu wersji typu aplikacji, z którym działa aplikacja; Nie będzie to przeszkadzać w usłudze ARM/SFRP. W przypadku próby cofnięcia aprowizacji uruchomionego pakietu środowisko uruchomieniowe SF uniemożliwi jego obsługę.


## <a name="manage-an-existing-application-via-resource-manager"></a>Zarządzanie istniejącą aplikacją za pośrednictwem Menedżer zasobów

Jeśli klaster jest już włączony i niektóre aplikacje, które mają być zarządzane, jako Menedżer zasobów zasoby są już wdrożone na nim, zamiast usuwać aplikacje i wdrażać je ponownie, można użyć wywołania PUT przy użyciu tych samych interfejsów API, aby aplikacje były potwierdzane jako zasoby Menedżer zasobów. Aby uzyskać dodatkowe informacje, zobacz [temat co to jest model zasobów aplikacji Service Fabric?](./service-fabric-concept-resource-model.md)

> [!NOTE]
> Aby umożliwić uaktualnienie klastra do ignorowania aplikacji w złej kondycji, klient może określić "maxPercentUnhealthyApplications: 100" w sekcji "upgradeDescription/healthPolicy"; szczegółowe opisy wszystkich ustawień znajdują się w [dokumentacji zasad uaktualniania klastra interfejsu API REST usługi Service Fabric](/rest/api/servicefabric/sfrp-model-clusterupgradepolicy).

## <a name="next-steps"></a>Następne kroki

* Użyj [interfejsu wiersza polecenia Service Fabric](service-fabric-cli.md) lub [programu PowerShell](service-fabric-deploy-remove-applications.md) , aby wdrożyć inne aplikacje w klastrze. 
* [Uaktualnianie klastra Service Fabric](service-fabric-cluster-upgrade.md)
