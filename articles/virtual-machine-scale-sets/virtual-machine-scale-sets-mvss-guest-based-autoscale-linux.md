---
title: Korzystanie z funkcji automatycznego skalowania platformy Azure z metrykami gościa w szablonie zestawu skalowania systemu Linux
description: Dowiedz się, jak automatyczne skalowanie przy użyciu metryk gościa w szablonie zestawu skalowania maszyn wirtualnych z systemem Linux
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 04/26/2019
ms.reviewer: avverma
ms.custom: avverma
ms.openlocfilehash: 0605780651e1a3c54ae53d13a3f99e1124fa76db
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100585012"
---
# <a name="autoscale-using-guest-metrics-in-a-linux-scale-set-template"></a>Skalowanie automatyczne przy użyciu metryk gościa w szablonie zestawu skalowania systemu Linux

Na platformie Azure są dostępne dwa różne typy metryk, które są zbierane z maszyn wirtualnych i zestawów skalowania: metryki hostów i metryki gościa. Na wysokim poziomie, jeśli chcesz użyć standardowych metryk procesora, dysku i sieci, metryki hosta są dobrym dopasowaniem. Jeśli jednak potrzebujesz większej liczby metryk, należy przeszukać metryki gościa.

Metryki hosta nie wymagają dodatkowej konfiguracji, ponieważ są zbierane przez maszynę wirtualną hosta, a metryki gościa wymagają zainstalowania [rozszerzenia Windows Diagnostyka Azure](../virtual-machines/extensions/diagnostics-template.md) lub [rozszerzenia systemu Linux Diagnostyka Azure](../virtual-machines/extensions/diagnostics-linux.md) na maszynie wirtualnej gościa. Jednym z typowych przyczyn użycia metryk Gości zamiast metryk hosta jest to, że metryki gościa zapewniają większy wybór metryk niż metryki hosta. Jeden z takich przykładów to metryki zużycia pamięci, które są dostępne tylko za pośrednictwem metryk gościa. Obsługiwane metryki hosta są wymienione [tutaj](../azure-monitor/essentials/metrics-supported.md)i często używane metryki gościa są wymienione [tutaj](../azure-monitor/autoscale/autoscale-common-metrics.md). W tym artykule pokazano, jak zmodyfikować [podstawowy, żywotny szablon zestawu skalowania](virtual-machine-scale-sets-mvss-start.md) , aby użyć reguł skalowania automatycznego na podstawie metryk gościa dla zestawów skalowania systemu Linux.

## <a name="change-the-template-definition"></a>Zmiana definicji szablonu

W [poprzednim artykule](virtual-machine-scale-sets-mvss-start.md) został utworzony podstawowy szablon zestawu skalowania. Teraz użyjemy tego wcześniejszego szablonu i zmodyfikujesz go, aby utworzyć szablon, który wdraża zestaw skalowania systemu Linux z funkcją automatycznego skalowania opartego na metrykach gościa.

Najpierw Dodaj parametry dla `storageAccountName` i `storageAccountSasToken` . Agent diagnostyki przechowuje dane metryk w [tabeli](../cosmos-db/tutorial-develop-table-dotnet.md) na tym koncie magazynu. W przypadku agenta diagnostyki systemu Linux w wersji 3,0 użycie klucza dostępu do magazynu nie jest już obsługiwane. Zamiast tego należy użyć [tokenu sygnatury dostępu współdzielonego](../storage/common/storage-sas-overview.md).

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

Następnie zmodyfikuj zestaw skalowania, `extensionProfile` Aby uwzględnić rozszerzenie diagnostyki. W tej konfiguracji określ identyfikator zasobu zestawu skalowania, z którego mają być zbierane metryki, a także konto magazynu i token sygnatury dostępu współdzielonego, które mają być używane do przechowywania metryk. Określ, jak często metryki są agregowane (w tym przypadku co minutę) i które metryki mają być śledzone (w tym przypadku używana pamięć to procent). Więcej szczegółowych informacji na temat tej konfiguracji i metryk innych niż procent używanej pamięci można znaleźć w [tej dokumentacji](../virtual-machines/extensions/diagnostics-linux.md).

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

Na koniec Dodaj `autoscaleSettings` zasób, aby skonfigurować automatyczne skalowanie na podstawie tych metryk. Ten zasób ma `dependsOn` klauzulę, która odwołuje się do zestawu skalowania, aby upewnić się, że zestaw skalowania istnieje przed próbą automatycznego skalowania. Jeśli wybierzesz inną metrykę do automatycznego skalowania, użyj `counterSpecifier` z konfiguracji rozszerzenia diagnostyki jako `metricName` w konfiguracji skalowania automatycznego. Aby uzyskać więcej informacji na temat konfiguracji skalowania automatycznego, zobacz [najlepsze rozwiązania dotyczące skalowania automatycznego](../azure-monitor/autoscale/autoscale-best-practices.md) i [Dokumentacja interfejsu API REST Azure monitor](/rest/api/monitor/autoscalesettings).

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
