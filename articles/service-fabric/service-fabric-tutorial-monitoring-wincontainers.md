---
title: Monitorowanie i diagnozowanie kontenerów systemu Windows
description: W tym samouczku przedstawiono Konfigurowanie dzienników Azure Monitor na potrzeby monitorowania i diagnostyki kontenerów systemu Windows w usłudze Azure Service Fabric.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc
ms.openlocfilehash: b7689d6e259055137a8d1d3c61552790ab9f28d3
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100588233"
---
# <a name="tutorial-monitor-windows-containers-on-service-fabric-using-azure-monitor-logs"></a>Samouczek: monitorowanie kontenerów systemu Windows na Service Fabric przy użyciu dzienników Azure Monitor

Jest to trzecia część samouczka i przeprowadzi Cię przez proces konfigurowania dzienników Azure Monitor do monitorowania kontenerów systemu Windows zorganizowanych w Service Fabric.

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Konfigurowanie dzienników Azure Monitor dla klastra Service Fabric
> * Używanie obszaru roboczego usługi Log Analytics w celu wyświetlania dzienników i wykonywania o nie zapytań z poziomu kontenerów i węzłów
> * Konfigurowanie agenta usługi Log Analytics na potrzeby pobierania metryk kontenerów i węzłów

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka należy:

* Mieć klaster na platformie Azure lub [utworzyć go za pomocą tego samouczka](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* [Wdrożyć w nim konteneryzowaną aplikację](service-fabric-host-app-in-a-container.md)

## <a name="setting-up-azure-monitor-logs-with-your-cluster-in-the-resource-manager-template"></a>Konfigurowanie Azure Monitor dzienników przy użyciu klastra w szablonie Menedżer zasobów

Jeśli używasz [szablonu](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-OMS-UnSecure) dostarczonego w pierwszej części tego samouczka, powinien on zawierać następujące dodatki do ogólnego szablonu usługi Azure Resource Manager w ramach usługi Service Fabric. Jeśli masz swój klaster, który chcesz skonfigurować w celu monitorowania kontenerów z Azure Monitor dzienników:

* Wprowadź następujące zmiany w szablonie usługi Resource Manager.
* Wdróż je przy użyciu programu PowerShell w celu uaktualnienia klastra przez [wdrożenie szablonu](./service-fabric-cluster-creation-via-arm.md). Usługa Azure Resource Manager potrafi rozpoznać istniejący zasób, więc wdroży go jako uaktualnienie.

### <a name="adding-azure-monitor-logs-to-your-cluster-template"></a>Dodawanie dzienników Azure Monitor do szablonu klastra

Wprowadź następujące zmiany w szablonie *template.json*:

1. Dodaj lokalizację i nazwę obszaru roboczego usługi Log Analytics w sekcji *parameters*:

    ```json
    "omsWorkspacename": {
      "type": "string",
      "defaultValue": "[toLower(concat('sf',uniqueString(resourceGroup().id)))]",
      "metadata": {
        "description": "Name of your Log Analytics Workspace"
      }
    },
    "omsRegion": {
      "type": "string",
      "defaultValue": "East US",
      "allowedValues": [
        "West Europe",
        "East US",
        "Southeast Asia"
      ],
      "metadata": {
        "description": "Specify the Azure Region for your Log Analytics workspace"
      }
    }
    ```

    Aby zmienić wartość dla dowolnego z tych elementów, dodaj te same parametry do pliku *template.parameters.json* i zmień używane w nim wartości.

2. Dodaj nazwę rozwiązania oraz rozwiązanie do sekcji *variables*:

    ```json
    "omsSolutionName": "[Concat('ServiceFabric', '(', parameters('omsWorkspacename'), ')')]",
    "omsSolution": "ServiceFabric"
    ```

3. Dodaj program Microsoft Monitoring Agent jako rozszerzenie maszyny wirtualnej. Znajdź zasób zestawu skalowania maszyn wirtualnych: *zasoby*  >  *"apiVersion": "[zmienne (" vmssApiVersion ")]"*. W obszarze *Właściwości*  >  *rozszerzenia virtualMachineProfile*  >  *extensionProfile*  >  Dodaj następujący Opis rozszerzenia w rozszerzeniu *ServiceFabricNode* : 
    
    ```json
    {
        "name": "[concat(variables('vmNodeType0Name'),'OMS')]",
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
    ```

4. Dodaj obszar roboczy usługi Log Analytics jako pojedynczy zasób. W obszarze *resources* po zasobie zestawów skalowania maszyn wirtualnych dodaj następujący kod:

    ```json
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[parameters('omsWorkspacename')]",
        "type": "Microsoft.OperationalInsights/workspaces",
        "properties": {
            "sku": {
                "name": "Free"
            }
        },
        "resources": [
            {
                "apiVersion": "2015-11-01-preview",
                "name": "[concat(variables('applicationDiagnosticsStorageAccountName'),parameters('omsWorkspacename'))]",
                "type": "storageinsightconfigs",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]",
                    "[concat('Microsoft.Storage/storageAccounts/', variables('applicationDiagnosticsStorageAccountName'))]"
                ],
                "properties": {
                    "containers": [ ],
                    "tables": [
                        "WADServiceFabric*EventTable",
                        "WADWindowsEventLogsTable",
                        "WADETWEventTable"
                    ],
                    "storageAccount": {
                        "id": "[resourceId('Microsoft.Storage/storageaccounts/', variables('applicationDiagnosticsStorageAccountName'))]",
                        "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('applicationDiagnosticsStorageAccountName')),'2015-06-15').key1]"
                    }
                }
            },
            {
                "apiVersion": "2015-11-01-preview",
                "name": "System",
                "type": "datasources",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
                ],
                "kind": "WindowsEvent",
                "properties": {
                    "eventLogName": "System",
                    "eventTypes": [
                        {
                            "eventType": "Error"
                        },
                        {
                            "eventType": "Warning"
                        },
                        {
                            "eventType": "Information"
                        }
                    ]
                }
            }
        ]
    },
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[variables('omsSolutionName')]",
        "type": "Microsoft.OperationsManagement/solutions",
        "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('OMSWorkspacename'))]"
        ],
        "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
        },
        "plan": {
            "name": "[variables('omsSolutionName')]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('omsSolution'))]",
            "promotionCode": ""
        }
    },
    ```

[W tym miejscu](https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/d2ffa318581fc23ac7f1b0ab2b52db1a0d7b4ba7/5-VM-Windows-OMS-UnSecure/sfclusteroms.json) znajduje się przykładowy szablon (używany w pierwszej części tego samouczka) zawierający wszystkie wprowadzone zmiany, do których można się odwołać w razie potrzeby. Te zmiany spowodują dodanie obszaru roboczego usługi Log Analytics do grupy zasobów. Obszar roboczy zostanie skonfigurowany w celu korzystania ze zdarzeń platformy usługi Service Fabric z poziomu tabel magazynu skonfigurowanych za pomocą agenta [Microsoft Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md). Agent usługi Log Analytics (Microsoft Monitoring Agent) został również dodany do każdego węzła w klastrze jako rozszerzenie maszyny wirtualnej. Oznacza to, że podczas skalowania klastra agent jest automatycznie konfigurowany na każdej maszynie i podłączany do tego samego obszaru roboczego.

Wdróż szablon z wprowadzonymi zmianami, aby uaktualnić bieżący klaster. Po zakończeniu tej operacji w grupie zasobów powinny zostać wyświetlone zasoby usługi log Analytics. Gdy klaster będzie gotowy, wdróż w nim konteneryzowaną aplikację. W następnym kroku zostanie skonfigurowane monitorowanie kontenerów.

## <a name="add-the-container-monitoring-solution-to-your-log-analytics-workspace"></a>Dodawanie rozwiązania do monitorowania kontenerów do obszaru roboczego usługi Log Analytics

Aby skonfigurować rozwiązanie kontenera w obszarze roboczym, wyszukaj wyrażenie *Rozwiązanie do monitorowania kontenerów* i utwórz zasób kontenerów (w kategorii Monitorowanie + zarządzanie).

![Dodawanie rozwiązania kontenerów](./media/service-fabric-tutorial-monitoring-wincontainers/containers-solution.png)

Po wyświetleniu monitu o *obszar roboczy log Analytics* wybierz obszar roboczy, który został utworzony w grupie zasobów, a następnie wybierz pozycję **Utwórz**. Spowoduje to dodanie *rozwiązania do monitorowania kontenerów* do obszaru roboczego, co spowoduje zainicjowanie agenta log Analytics wdrożonego przez szablon w celu rozpoczęcia zbierania dzienników platformy Docker i statystyk.

Przejdź z powrotem do *grupy zasobów*, gdzie powinno zostać wyświetlone nowo dodane rozwiązanie do monitorowania. W przypadku wybrania tej opcji strona docelowa powinna zawierać liczbę uruchomionych obrazów kontenerów.

*Zwróć uwagę na to, że uruchomiono pięć wystąpień kontenera Fabrikam z [części dwóch](service-fabric-host-app-in-a-container.md) samouczka*

![Strona docelowa rozwiązania kontenera](./media/service-fabric-tutorial-monitoring-wincontainers/solution-landing.png)

Wybranie **rozwiązania do monitorowania kontenerów** spowoduje przejście do bardziej szczegółowego pulpitu nawigacyjnego, który umożliwia przewijanie wielu paneli oraz uruchamianie zapytań w dziennikach Azure monitor.

W związku z tym, że agent pobiera dzienniki platformy Docker, domyślnie wyświetla elementy *stdout* i *stderr*. Jeśli przewiniesz w poziomie, zobaczysz spis obrazu kontenera, stan, metryki i przykładowe zapytania, które można uruchomić, aby uzyskać bardziej przydatne dane.

![Pulpit nawigacyjny rozwiązania kontenera](./media/service-fabric-tutorial-monitoring-wincontainers/container-metrics.png)

Kliknięcie dowolnego z tych paneli spowoduje przejście do zapytania Kusto, które generuje wartość wyświetlaną. Zmień zapytanie na, aby *\** zobaczyć wszystkie różne rodzaje dzienników, które są pobierane. W tym miejscu można wysyłać zapytania dotyczące wydajności kontenerów i dzienników lub filtrować je albo wyszukiwać zdarzenia platformy usługi Service Fabric. Poza tym agenci stale emitują puls z każdego widocznego węzła, aby upewnić się, że dane ze wszystkich maszyn są zbierane nawet w przypadku zmiany konfiguracji klastra.

![Zapytanie dotyczące kontenera](./media/service-fabric-tutorial-monitoring-wincontainers/query-sample.png)

## <a name="configure-log-analytics-agent-to-pick-up-performance-counters"></a>Konfigurowanie agenta usługi Log Analytics w celu zbierania liczników wydajności

Kolejną zaletą korzystania z agenta Log Analytics jest możliwość zmiany liczników wydajności, które mają zostać pobrane za pośrednictwem interfejsu użytkownika usługi log Analytics, a nie konieczności konfigurowania agenta diagnostyki Azure i przeprowadzania uaktualnienia na podstawie szablonu Menedżer zasobów za każdym razem. W tym celu wybierz pozycję w **obszarze roboczym OMS** na stronie miejsce docelowe rozwiązania do monitorowania kontenerów (lub Service Fabric).

Spowoduje to przejście do obszaru roboczego usługi Log Analytics, w którym można wyświetlać rozwiązania, tworzyć niestandardowe pulpity nawigacyjne, a także konfigurować agenta usługi Log Analytics. 
* Wybierz pozycję **Ustawienia zaawansowane** , aby otworzyć menu Ustawienia zaawansowane.
* Wybierz pozycję **połączone źródła**  >  **serwery z systemem Windows** , aby sprawdzić, czy są *połączone komputery z systemem Windows*.
* Wybierz pozycję **dane**  >  **liczniki wydajności systemu Windows** , aby wyszukać i dodać nowe liczniki wydajności. W tym miejscu zostanie wyświetlona lista zaleceń z dzienników Azure Monitor dotyczących liczników wydajności, które można zbierać, a także opcji wyszukiwania innych liczników. Sprawdź, czy zbierane są liczniki **Processor(_Total)\%Processor Time** i **\Memory(*)\Available MBytes**.

Po kilku minutach **odśwież** rozwiązanie do monitorowania kontenerów, aby wyświetlić dane przychodzące dotyczące *wydajności komputera*. Pomoże to zrozumieć, w jaki sposób zasoby są używane. Tych metryk można również używać na potrzeby podejmowania odpowiednich decyzji dotyczących skalowania klastra lub potwierdzania, że klaster równoważy obciążenie zgodnie z oczekiwaniami.

*Uwaga: upewnij się, że filtry czasu zostały odpowiednio ustawione i umożliwiają korzystanie z tych metryk.*

![Liczniki wydajności 2](./media/service-fabric-tutorial-monitoring-wincontainers/perf-counters2.png)

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie dzienników Azure Monitor dla klastra Service Fabric
> * Używanie obszaru roboczego usługi Log Analytics w celu wyświetlania dzienników i wykonywania o nie zapytań z poziomu kontenerów i węzłów
> * Konfigurowanie agenta usługi Log Analytics na potrzeby pobierania metryk kontenerów i węzłów

Teraz, po skonfigurowaniu monitorowania aplikacji w kontenerze, spróbuj wykonać następujące polecenie:

* Konfigurowanie dzienników Azure Monitor dla klastra systemu Linux, wykonując podobne kroki, jak w tym samouczku. Utwórz odwołanie do [tego szablonu](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeType-Secure-OMS), aby wprowadzić zmiany w szablonie usługi Resource Manager.
* Skonfiguruj dzienniki Azure Monitor, aby skonfigurować [Automatyczne alerty](../azure-monitor/alerts/alerts-overview.md) w celu ułatwienia wykrywania i diagnostyki.
* Zapoznaj się z listą [zalecanych liczników wydajności](service-fabric-diagnostics-event-generation-perf.md) usługi Service Fabric w celu skonfigurowania ich na potrzeby klastrów.
* Zapoznaj się z funkcjami [przeszukiwania dzienników i wykonywania zapytań](../azure-monitor/logs/log-query-overview.md) , które są oferowane w ramach dzienników Azure monitor.
