---
title: Korzystanie z funkcji automatycznego skalowania platformy Azure z metrykami gościa w szablonie zestawu skalowania systemu Linux
description: Dowiedz się, jak automatyczne skalowanie przy użyciu metryk gościa w szablonie zestawu skalowania maszyn wirtualnych z systemem Linux
author: mimckitt
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: mimckitt
ms.openlocfilehash: 8021b7b8feb6dc06fb2e48bc4e825200a1baad33
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81273651"
---
# <a name="autoscale-using-guest-metrics-in-a-linux-scale-set-template"></a>Skalowanie automatyczne przy użyciu metryk gościa w szablonie zestawu skalowania systemu Linux

Na platformie Azure są dostępne dwa różne typy metryk, które są zbierane z maszyn wirtualnych i zestawów skalowania: metryki hostów i metryki gościa. Na wysokim poziomie, jeśli chcesz użyć standardowych metryk procesora, dysku i sieci, metryki hosta są dobrym dopasowaniem. Jeśli jednak potrzebujesz większej liczby metryk, należy przeszukać metryki gościa.

Metryki hosta nie wymagają dodatkowej konfiguracji, ponieważ są zbierane przez maszynę wirtualną hosta, a metryki gościa wymagają zainstalowania [rozszerzenia Windows Diagnostyka Azure](../virtual-machines/windows/extensions-diagnostics-template.md) lub [rozszerzenia systemu Linux Diagnostyka Azure](../virtual-machines/linux/diagnostic-extension.md) na maszynie wirtualnej gościa. Jednym z typowych przyczyn użycia metryk Gości zamiast metryk hosta jest to, że metryki gościa zapewniają większy wybór metryk niż metryki hosta. Jeden z takich przykładów to metryki zużycia pamięci, które są dostępne tylko za pośrednictwem metryk gościa. Obsługiwane metryki hosta są wymienione [tutaj](../azure-monitor/platform/metrics-supported.md)i często używane metryki gościa są wymienione [tutaj](../azure-monitor/platform/autoscale-common-metrics.md). W tym artykule pokazano, jak zmodyfikować [podstawowy, żywotny szablon zestawu skalowania](virtual-machine-scale-sets-mvss-start.md) , aby użyć reguł skalowania automatycznego na podstawie metryk gościa dla zestawów skalowania systemu Linux.

## <a name="change-the-template-definition"></a>Zmiana definicji szablonu

W [poprzednim artykule](virtual-machine-scale-sets-mvss-start.md) został utworzony podstawowy szablon zestawu skalowania. Teraz użyjemy tego wcześniejszego szablonu i zmodyfikujesz go, aby utworzyć szablon, który wdraża zestaw skalowania systemu Linux z funkcją automatycznego skalowania opartego na metrykach gościa.

Najpierw Dodaj parametry dla `storageAccountName` i. `storageAccountSasToken` Agent diagnostyki przechowuje dane metryk w [tabeli](../cosmos-db/table-storage-how-to-use-dotnet.md) na tym koncie magazynu. W przypadku agenta diagnostyki systemu Linux w wersji 3,0 użycie klucza dostępu do magazynu nie jest już obsługiwane. Zamiast tego należy użyć [tokenu sygnatury dostępu współdzielonego](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

```diff
     },
     "adminPassword": {
       "type": "securestring"
+    },
+    "storageAccountName": {
+      "type": "string"
+    },
+    "storageAccountSasToken": {
+      "type": "securestring"
     }
   },
```

Następnie zmodyfikuj zestaw `extensionProfile` skalowania, aby uwzględnić rozszerzenie diagnostyki. W tej konfiguracji określ identyfikator zasobu zestawu skalowania, z którego mają być zbierane metryki, a także konto magazynu i token sygnatury dostępu współdzielonego, które mają być używane do przechowywania metryk. Określ, jak często metryki są agregowane (w tym przypadku co minutę) i które metryki mają być śledzone (w tym przypadku używana pamięć to procent). Więcej szczegółowych informacji na temat tej konfiguracji i metryk innych niż procent używanej pamięci można znaleźć w [tej dokumentacji](../virtual-machines/linux/diagnostic-extension.md).

```diff
                 }
               }
             ]
+          },
+          "extensionProfile": {
+            "extensions": [
+              {
+                "name": "LinuxDiagnosticExtension",
+                "properties": {
+                  "publisher": "Microsoft.Azure.Diagnostics",
+                  "type": "LinuxDiagnostic",
+                  "typeHandlerVersion": "3.0",
+                  "settings": {
+                    "StorageAccount": "[parameters('storageAccountName')]",
+                    "ladCfg": {
+                      "diagnosticMonitorConfiguration": {
+                        "performanceCounters": {
+                          "sinks": "WADMetricJsonBlob",
+                          "performanceCounterConfiguration": [
+                            {
+                              "unit": "percent",
+                              "type": "builtin",
+                              "class": "memory",
+                              "counter": "percentUsedMemory",
+                              "counterSpecifier": "/builtin/memory/percentUsedMemory",
+                              "condition": "IsAggregate=TRUE"
+                            }
+                          ]
+                        },
+                        "metrics": {
+                          "metricAggregation": [
+                            {
+                              "scheduledTransferPeriod": "PT1M"
+                            }
+                          ],
+                          "resourceId": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]"
+                        }
+                      }
+                    }
+                  },
+                  "protectedSettings": {
+                    "storageAccountName": "[parameters('storageAccountName')]",
+                    "storageAccountSasToken": "[parameters('storageAccountSasToken')]",
+                    "sinksConfig": {
+                      "sink": [
+                        {
+                          "name": "WADMetricJsonBlob",
+                          "type": "JsonBlob"
+                        }
+                      ]
+                    }
+                  }
+                }
+              }
+            ]
           }
         }
       }
```

Na koniec Dodaj `autoscaleSettings` zasób, aby skonfigurować automatyczne skalowanie na podstawie tych metryk. Ten zasób ma `dependsOn` klauzulę, która odwołuje się do zestawu skalowania, aby upewnić się, że zestaw skalowania istnieje przed próbą automatycznego skalowania. Jeśli wybierzesz inną metrykę do automatycznego skalowania, użyj `counterSpecifier` z konfiguracji rozszerzenia diagnostyki jako `metricName` w konfiguracji skalowania automatycznego. Aby uzyskać więcej informacji na temat konfiguracji skalowania automatycznego, zobacz [najlepsze rozwiązania dotyczące skalowania automatycznego](../azure-monitor/platform/autoscale-best-practices.md) i [Dokumentacja interfejsu API REST Azure monitor](/rest/api/monitor/autoscalesettings).

```diff
+    },
+    {
+      "type": "Microsoft.Insights/autoscaleSettings",
+      "apiVersion": "2015-04-01",
+      "name": "guestMetricsAutoscale",
+      "location": "[resourceGroup().location]",
+      "dependsOn": [
+        "Microsoft.Compute/virtualMachineScaleSets/myScaleSet"
+      ],
+      "properties": {
+        "name": "guestMetricsAutoscale",
+        "targetResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]",
+        "enabled": true,
+        "profiles": [
+          {
+            "name": "Profile1",
+            "capacity": {
+              "minimum": "1",
+              "maximum": "10",
+              "default": "3"
+            },
+            "rules": [
+              {
+                "metricTrigger": {
+                  "metricName": "/builtin/memory/percentUsedMemory",
+                  "metricNamespace": "",
+                  "metricResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]",
+                  "timeGrain": "PT1M",
+                  "statistic": "Average",
+                  "timeWindow": "PT5M",
+                  "timeAggregation": "Average",
+                  "operator": "GreaterThan",
+                  "threshold": 60
+                },
+                "scaleAction": {
+                  "direction": "Increase",
+                  "type": "ChangeCount",
+                  "value": "1",
+                  "cooldown": "PT1M"
+                }
+              },
+              {
+                "metricTrigger": {
+                  "metricName": "/builtin/memory/percentUsedMemory",
+                  "metricNamespace": "",
+                  "metricResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]",
+                  "timeGrain": "PT1M",
+                  "statistic": "Average",
+                  "timeWindow": "PT5M",
+                  "timeAggregation": "Average",
+                  "operator": "LessThan",
+                  "threshold": 30
+                },
+                "scaleAction": {
+                  "direction": "Decrease",
+                  "type": "ChangeCount",
+                  "value": "1",
+                  "cooldown": "PT1M"
+                }
+              }
+            ]
+          }
+        ]
+      }
     }
   ]
 }
```





## <a name="next-steps"></a>Następne kroki

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
