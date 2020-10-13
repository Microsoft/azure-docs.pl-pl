---
title: Automatyczne skalowanie aplikacji działającej na platformie Azure Service Fabric siatką
description: Dowiedz się, jak skonfigurować zasady automatycznego skalowania dla usług aplikacji siatki Service Fabric.
author: georgewallace
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: gwallace
ms.custom: mvc, devcenter
ms.openlocfilehash: f65fcfa76069a3de37fd3a76e38e38fba40e04ac
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91843060"
---
# <a name="create-autoscale-policies-for-a-service-fabric-mesh-application"></a>Tworzenie zasad automatycznego skalowania dla aplikacji siatki Service Fabric
Jedną z głównych zalet wdrażania aplikacji do Service Fabric siatki jest możliwość łatwego skalowania usług do lub wyprowadzenia. Ta usługa powinna być używana do obsługi różnych ilości obciążeń w usługach lub do zwiększenia dostępności. Możesz ręcznie skalować usługi w lub wychodzące lub skonfigurować zasady skalowania automatycznego.

[Skalowanie automatyczne](service-fabric-mesh-scalability.md#autoscaling-service-instances) pozwala na dynamiczne skalowanie liczby wystąpień usługi (skalowanie w poziomie). Skalowanie automatyczne zapewnia doskonałą elastyczność i umożliwia inicjowanie obsługi lub usuwanie wystąpień usługi na podstawie użycia procesora lub pamięci.

## <a name="options-for-creating-an-auto-scaling-policy-trigger-and-mechanism"></a>Opcje tworzenia zasad automatycznego skalowania, wyzwalacza i mechanizmu
Zasady skalowania automatycznego są definiowane dla każdej usługi, która ma zostać przeskalowana. Zasady są zdefiniowane w pliku zasobów usługi YAML lub szablonie wdrożenia JSON. Każda zasada skalowania składa się z dwóch części: wyzwalacza i mechanizmu skalowania.

Wyzwalacz definiuje, kiedy są wywoływane zasady skalowania automatycznego.  Określ rodzaj wyzwalacza (średnie obciążenie) i metrykę do monitorowania (procesor CPU lub pamięć).  Górny i dolny próg obciążenia określony jako wartość procentowa. Interwał skalowania określa, jak często należy sprawdzać (w sekundach) określone użycie (na przykład średnie obciążenie procesora CPU) we wszystkich aktualnie wdrożonych wystąpieniach usługi.  Mechanizm jest wyzwalany, gdy monitorowane metryki spadnie poniżej dolnego progu lub rosną powyżej górnego progu.  

Mechanizm skalowania definiuje sposób wykonywania operacji skalowania po wyzwoleniu zasad.  Określ rodzaj mechanizmu (Dodaj/Usuń replikę), minimalną i maksymalną liczbę replik (jako liczby całkowite).  Liczba replik usługi nigdy nie będzie skalowana poniżej wartości minimalnej lub większej niż maksymalna.  Należy również określić przyrost skali jako liczbę całkowitą, która jest liczbą replik, które zostaną dodane lub usunięte podczas operacji skalowania.  

## <a name="define-an-auto-scaling-policy-in-a-json-template"></a>Definiowanie zasad skalowania automatycznego w szablonie JSON

W poniższym przykładzie przedstawiono zasady skalowania automatycznego w szablonie wdrożenia JSON.  Zasady skalowania automatycznego są deklarowane we właściwości usługi do skalowania.  W tym przykładzie zdefiniowano wyzwalacz obciążenia średniego procesora CPU.  Mechanizm zostanie wyzwolony, jeśli średnie obciążenie procesora CPU wszystkich wdrożonych wystąpień spadnie poniżej 0,2 (20%) lub spadnie powyżej 0,8 (80%).  Obciążenie procesora CPU jest sprawdzane co 60 sekund.  Mechanizm skalowania jest zdefiniowany w celu dodawania lub usuwania wystąpień, jeśli zasady są wyzwalane.  Wystąpienia usługi zostaną dodane lub usunięte przyrostowo.  Zdefiniowano również minimalną liczbę wystąpień z jedną i maksymalną liczbę wystąpień wynoszącą 40.

```json
{
"apiVersion": "2018-09-01-preview",
"name": "WorkerApp",
"type": "Microsoft.ServiceFabricMesh/applications",
"location": "[parameters('location')]",
"dependsOn": [
"Microsoft.ServiceFabricMesh/networks/worker-app-network"
],
"properties": {
"services": [   
    { ... },       
    {
    "name": "WorkerService",
    "properties": {
        "description": "Worker Service",
        "osType": "linux",
        "codePackages": [
        {  ...              }
        ],
        "replicaCount": 1,
        "autoScalingPolicies": [
        {
            "name": "AutoScaleWorkerRule",
            "trigger": {
                "kind": "AverageLoad",
                "metric": {
                    "kind": "Resource",
                    "name": "cpu"
                },
                "lowerLoadThreshold": "0.2",
                "upperLoadThreshold": "0.8",
                "scaleIntervalInSeconds": "60"
            },
            "mechanism": {
                "kind": "AddRemoveReplica",
                "minCount": "1",
                "maxCount": "40",
                "scaleIncrement": "1"
            }
        }
        ],        
        ...
    }
    }
]
}
}
```

## <a name="define-an-autoscale-policy-in-a-serviceyaml-resource-file"></a>Zdefiniuj zasady automatycznego skalowania w pliku zasobów usługi Service. YAML
W poniższym przykładzie przedstawiono zasady skalowania automatycznego w pliku zasobów usługi (YAML).  Zasady skalowania automatycznego są zadeklarowane jako właściwość usługi do skalowania.  W tym przykładzie zdefiniowano wyzwalacz obciążenia średniego procesora CPU.  Mechanizm zostanie wyzwolony, jeśli średnie obciążenie procesora CPU wszystkich wdrożonych wystąpień spadnie poniżej 0,2 (20%) lub spadnie powyżej 0,8 (80%).  Obciążenie procesora CPU jest sprawdzane co 60 sekund.  Mechanizm skalowania jest zdefiniowany w celu dodawania lub usuwania wystąpień, jeśli zasady są wyzwalane.  Wystąpienia usługi zostaną dodane lub usunięte przyrostowo.  Zdefiniowano również minimalną liczbę wystąpień z jedną i maksymalną liczbę wystąpień wynoszącą 40.

```yaml
## Service definition ##
application:
  schemaVersion: 1.0.0-preview2
  name: WorkerApp
  properties:
    services:
      - name: WorkerService
        properties:
          description: WorkerService description.
          osType: Linux
          codePackages:
            ...
          replicaCount: 1
          autoScalingPolicies:
            - name: AutoScaleWorkerRule
              trigger:
                kind: AverageLoad
                metric:
                  kind: Resource
                  name: cpu
                lowerLoadThreshold: 0.2
                upperLoadThreshold: 0.8
                scaleIntervalInSeconds: 60
              mechanism:
                kind: AddRemoveReplica
                minCount: 1
                maxCount: 40
                scaleIncrement: 1
          ...
```

## <a name="next-steps"></a>Następne kroki
Dowiedz się, jak [ręcznie skalować usługę](service-fabric-mesh-tutorial-template-scale-services.md)
