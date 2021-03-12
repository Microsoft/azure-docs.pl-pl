---
title: Agregacja zdarzeń za pomocą Diagnostyka Azure systemu Windows
description: Dowiedz się więcej na temat agregowania i zbierania zdarzeń przy użyciu funkcji wad do monitorowania i diagnostyki klastrów Service Fabric platformy Azure.
author: srrengar
ms.topic: conceptual
ms.date: 04/03/2018
ms.author: srrengar
ms.openlocfilehash: eaf40bafb68820b56fc9bf00dd4c11fbb337406f
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102613463"
---
# <a name="event-aggregation-and-collection-using-windows-azure-diagnostics"></a>Agregacja i zbieranie zdarzeń przy użyciu Diagnostyka Azure systemu Windows
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
> * [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
>
>

Gdy uruchamiasz klaster usługi Azure Service Fabric, dobrym pomysłem jest zebranie dzienników ze wszystkich węzłów w centralnej lokalizacji. Przechowywanie dzienników w centralnej lokalizacji ułatwia analizowanie i rozwiązywanie problemów z klastrem lub problemów z aplikacjami i usługami uruchomionymi w tym klastrze.

Jednym ze sposobów przekazywania i zbierania dzienników jest użycie rozszerzenia Windows Diagnostyka Azure (funkcji wad), które przekazuje dzienniki do usługi Azure Storage, a także oferuje opcję wysyłania dzienników do usługi Azure Application Insights lub Event Hubs. Możesz również użyć procesu zewnętrznego, aby odczytać zdarzenia z magazynu i umieścić je w produkcie platformy analizy, takim jak [dzienniki Azure monitor](./service-fabric-diagnostics-oms-setup.md) lub inne rozwiązanie do analizy dzienników.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule są używane następujące narzędzia:

* [Azure Resource Manager](../azure-resource-manager/management/overview.md)
* [Azure PowerShell](/powershell/azure/)
* [Szablon usługi Azure Resource Manager](../virtual-machines/extensions/diagnostics-template.md?toc=/azure/virtual-machines/windows/toc.json)

## <a name="service-fabric-platform-events"></a>Zdarzenia platformy Service Fabric
Service Fabric konfiguruje kilka gotowych [kanałów rejestrowania](service-fabric-diagnostics-event-generation-infra.md), których następujące kanały są wstępnie skonfigurowane z rozszerzeniem, aby wysyłać dane monitorowania i diagnostyki do tabeli magazynu lub w innym miejscu:
  * [Zdarzenia operacyjne](service-fabric-diagnostics-event-generation-operational.md): operacje wyższego poziomu wykonywane przez platformę Service Fabric. Przykłady obejmują tworzenie aplikacji i usług, zmiany stanu węzła i informacje o uaktualnianiu. Są one emitowane jako dzienniki śledzenia zdarzeń systemu Windows (ETW)
  * [Zdarzenia modelu programowania Reliable Actors](service-fabric-reliable-actors-diagnostics.md)
  * [Zdarzenia modelu programowania Reliable Services](service-fabric-reliable-services-diagnostics.md)

## <a name="deploy-the-diagnostics-extension-through-the-portal"></a>Wdrażanie rozszerzenia diagnostyki za pomocą portalu
Pierwszym krokiem zbierania dzienników jest wdrożenie rozszerzenia diagnostyki w węzłach zestawu skalowania maszyn wirtualnych w klastrze Service Fabric. Rozszerzenie diagnostyki zbiera dzienniki na poszczególnych maszynach wirtualnych i przekazuje je do określonego konta magazynu. Poniższe kroki przedstawiają, jak to zrobić w przypadku nowych i istniejących klastrów za pomocą szablonów Azure Portal i Azure Resource Manager.

### <a name="deploy-the-diagnostics-extension-as-part-of-cluster-creation-through-azure-portal"></a>Wdróż rozszerzenie diagnostyki w ramach tworzenia klastra za pomocą Azure Portal
Podczas tworzenia klastra w kroku Konfiguracja klastra Rozwiń Ustawienia opcjonalne i upewnij się, że opcja Diagnostyka jest **włączona** (ustawienie domyślne).

![Ustawienia Diagnostyka Azure w portalu w celu utworzenia klastra](media/service-fabric-diagnostics-event-aggregation-wad/azure-enable-diagnostics-new.png)

Zdecydowanie zalecamy pobranie szablonu **przed kliknięciem przycisku Utwórz** w ostatnim kroku. Aby uzyskać szczegółowe informacje, zapoznaj się z tematem [Konfigurowanie klastra Service Fabric przy użyciu szablonu Azure Resource Manager](service-fabric-cluster-creation-via-arm.md). Musisz mieć szablon, aby wprowadzić zmiany dotyczące kanałów (wymienionych powyżej) do zbierania danych.

![Szablon klastra](media/service-fabric-diagnostics-event-aggregation-wad/download-cluster-template.png)

Teraz, gdy agregujesz zdarzenia w usłudze Azure Storage, [Skonfiguruj dzienniki Azure monitor](service-fabric-diagnostics-oms-setup.md) , aby uzyskać wgląd w szczegółowe dane i wysyłać do nich zapytania w portalu Azure monitor Logs

>[!NOTE]
>Obecnie nie ma możliwości filtrowania lub pielęgnacji zdarzeń wysyłanych do tabel. Jeśli proces usuwania zdarzeń z tabeli nie zostanie wdrożony, tabela będzie nadal rośnie (wartość domyślna to 50 GB). Instrukcje dotyczące zmiany tego artykułu znajdują się [poniżej w tym artykule](service-fabric-diagnostics-event-aggregation-wad.md#update-storage-quota). Ponadto istnieje przykład usługi pielęgnacji danych działającej w [próbce licznika alarmowego](https://github.com/Azure-Samples/service-fabric-watchdog-service)i zaleca się napisać jeden dla siebie, chyba że istnieje dobry powód przechowywania dzienników poza okresem 30-lub 90 dni.



## <a name="deploy-the-diagnostics-extension-through-azure-resource-manager"></a>Wdróż rozszerzenie diagnostyki za pomocą Azure Resource Manager

### <a name="create-a-cluster-with-the-diagnostics-extension"></a>Tworzenie klastra z rozszerzeniem diagnostyki
Aby utworzyć klaster przy użyciu Menedżer zasobów, musisz dodać kod JSON konfiguracji diagnostyki do szablonu pełny Menedżer zasobów. Udostępniamy przykładowy szablon Menedżer zasobów klastra z pięcioma MASZYNami wirtualnymi z dodaną konfiguracją diagnostyki w ramach naszych przykładów szablonu Menedżer zasobów. Zobaczysz ją w tej lokalizacji w galerii przykładów platformy Azure: [klaster z pięcioma węzłami z przykładem szablonu Menedżer zasobów diagnostyki](https://azure.microsoft.com/resources/templates/service-fabric-secure-cluster-5-node-1-nodetype/).

Aby wyświetlić ustawienia diagnostyki w szablonie Menedżer zasobów, Otwórz azuredeploy.jspliku i Wyszukaj **IaaSDiagnostics**. Aby utworzyć klaster przy użyciu tego szablonu, wybierz przycisk **Wdróż na platformie Azure** dostępny przy poprzednim łączu.

Alternatywnie można pobrać przykład Menedżer zasobów, wprowadzić w nim zmiany i utworzyć klaster ze zmodyfikowanym szablonem za pomocą `New-AzResourceGroupDeployment` polecenia w oknie Azure PowerShell. Zapoznaj się z poniższym kodem parametrów przekazywanych do polecenia. Aby uzyskać szczegółowe informacje na temat sposobu wdrażania grupy zasobów przy użyciu programu PowerShell, zobacz artykuł [wdrażanie grupy zasobów z szablonem Azure Resource Manager](../azure-resource-manager/templates/deploy-powershell.md).

### <a name="add-the-diagnostics-extension-to-an-existing-cluster"></a>Dodawanie rozszerzenia diagnostyki do istniejącego klastra
Jeśli masz istniejący klaster, który nie ma wdrożonej diagnostyki, możesz go dodać lub zaktualizować za pomocą szablonu klastra. Zmodyfikuj szablon Menedżer zasobów używany do tworzenia istniejącego klastra lub Pobierz szablon z portalu zgodnie z wcześniejszym opisem. Zmodyfikuj template.jspliku, wykonując następujące zadania:

Dodaj nowy zasób magazynu do szablonu poprzez dodanie do sekcji Resources.

```json
{
    "apiVersion": "2018-07-01",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[parameters('applicationDiagnosticsStorageAccountName')]",
    "location": "[parameters('computeLocation')]",
    "sku": {
    "name": "[parameters('applicationDiagnosticsStorageAccountType')]"
    "tier": "standard"
  },
    "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
  }
},
```

 Następnie Dodaj do sekcji Parameters tuż po definicjach konta magazynu, między `supportLogStorageAccountName` . Zastąp symbol zastępczy Text *Storage wpisz tutaj* nazwę konta magazynu, którego potrzebujesz.

```json
    "applicationDiagnosticsStorageAccountType": {
      "type": "string",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS"
      ],
      "defaultValue": "Standard_LRS",
      "metadata": {
        "description": "Replication option for the application diagnostics storage account"
      }
    },
    "applicationDiagnosticsStorageAccountName": {
      "type": "string",
      "defaultValue": "**STORAGE ACCOUNT NAME GOES HERE**",
      "metadata": {
        "description": "Name for the storage account that contains application diagnostics data from the cluster"
      }
    },
```
Następnie należy zaktualizować `VirtualMachineProfile` sekcję template.jsw pliku, dodając następujący kod w tablicy rozszerzeń. Pamiętaj, aby dodać przecinek na początku lub na końcu, w zależności od tego, gdzie został wstawiony.

```json
{
    "name": "[concat(parameters('vmNodeType0Name'),'_Microsoft.Insights.VMDiagnosticsSettings')]",
    "properties": {
        "type": "IaaSDiagnostics",
        "autoUpgradeMinorVersion": true,
        "protectedSettings": {
        "storageAccountName": "[parameters('applicationDiagnosticsStorageAccountName')]",
        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
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
                },
                {
                    "provider": "02d06793-efeb-48c8-8f7f-09713309a810",
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
        "StorageAccount": "[parameters('applicationDiagnosticsStorageAccountName')]"
        },
        "typeHandlerVersion": "1.5"
    }
}
```

Po zmodyfikowaniu template.jsw pliku zgodnie z opisem, należy ponownie opublikować szablon Menedżer zasobów. Jeśli szablon został wyeksportowany, uruchomienie pliku deploy.ps1 ponownie publikuje szablon. Po wdrożeniu programu upewnij się, że **ProvisioningState** **zakończyło się pomyślnie**.

> [!TIP]
> Jeśli zamierzasz wdrożyć kontenery w klastrze, Włącz funkcję funkcji wad, aby pobrać statystyki platformy Docker, dodając ją do sekcji **WadCfg > DiagnosticMonitorConfiguration** .

```json
"DockerSources": {
    "Stats": {
        "enabled": true,
        "sampleRate": "PT1M"
    }
},
```

### <a name="update-storage-quota"></a>Aktualizowanie przydziału magazynu

Ponieważ tabele wypełnione przez rozszerzenie zwiększają się do momentu osiągnięcia limitu przydziału, warto rozważyć zmniejszenie rozmiaru przydziału. Wartość domyślna to 50 GB i można ją skonfigurować w szablonie pod `overallQuotaInMB` polem poniżej `DiagnosticMonitorConfiguration`

```json
"overallQuotaInMB": "50000",
```

## <a name="log-collection-configurations"></a>Konfiguracje kolekcji dzienników
Dzienniki z dodatkowych kanałów są również dostępne dla kolekcji, poniżej przedstawiono kilka typowych konfiguracji, które można wprowadzić w szablonie dla klastrów działających na platformie Azure.

* Obsługa kanałów operacyjnych: domyślnie włączone, operacje wysokiego poziomu wykonywane przez Service Fabric i klaster, w tym zdarzenia dla węzła, nowo wdrożone aplikacje lub wycofania uaktualnienia itp. Aby uzyskać listę zdarzeń, zapoznaj się z tematem [zdarzenia dotyczące kanału operacyjnego](./service-fabric-diagnostics-event-generation-operational.md).
  
```json
      scheduledTransferKeywordFilter: "4611686018427387904"
  ```
* Kanał operacyjny — szczegóły: obejmuje to raporty kondycji i decyzje dotyczące równoważenia obciążenia, a także wszystko w podstawowym kanale operacyjnym. Te zdarzenia są generowane przez system lub kod przy użyciu interfejsów API raportowania kondycji lub obciążenia, takich jak [ReportPartitionHealth](/previous-versions/azure/reference/mt645153(v=azure.100)) lub [ReportLoad](/previous-versions/azure/reference/mt161491(v=azure.100)). Aby wyświetlić te zdarzenia w Podgląd zdarzeń diagnostycznym programu Visual Studio, Dodaj "Microsoft-servicefabric: 4:0x4000000000000008" do listy dostawców ETW.

```json
      scheduledTransferKeywordFilter: "4611686018427387912"
  ```

* Kanał danych i obsługi komunikatów — podstawowy: dzienniki krytyczne i zdarzenia generowane w wiadomości (obecnie tylko elementu reverseproxy) i ścieżka danych, oprócz szczegółowych dzienników kanałów operacyjnych. Te zdarzenia to błędy przetwarzania żądań i inne krytyczne problemy w elementu reverseproxy, a także przetwarzane żądania. **Jest to nasze zalecenie dotyczące kompleksowego rejestrowania**. Aby wyświetlić te zdarzenia w Podgląd zdarzeń diagnostycznym programu Visual Studio, należy dodać "Microsoft-servicefabric: 4:0x4000000000000010" do listy dostawców ETW.

```json
      scheduledTransferKeywordFilter: "4611686018427387928"
  ```

* Informacje o kanale & komunikatów — szczegóły: kanał pełny zawierający wszystkie niekrytyczne dzienniki z danych i komunikatów w klastrze oraz szczegółowy kanał operacyjny. Szczegółowe Rozwiązywanie problemów dotyczących wszystkich zdarzeń zwrotnych serwera proxy można znaleźć w [przewodniku diagnostyki zwrotnego serwera proxy](service-fabric-reverse-proxy-diagnostics.md).  Aby wyświetlić te zdarzenia w Podglądzie zdarzeń diagnostycznych programu Visual Studio, Dodaj "Microsoft-servicefabric: 4:0x4000000000000020" do listy dostawców ETW.

```json
      scheduledTransferKeywordFilter: "4611686018427387944"
  ```

>[!NOTE]
>Ten kanał ma bardzo duże ilości zdarzeń, co umożliwia zbieranie zdarzeń z tego kanału szczegółowego, co pozwala na szybkie generowanie wielu śladów i zużywanie pojemności magazynu. Włącz tę opcję tylko wtedy, gdy jest to absolutnie konieczne.


Aby włączyć **podstawowy kanał operacyjny** naszym rekomendacją dotyczącą kompleksowego rejestrowania z najmniejszą ilością szumu, `EtwManifestProviderConfiguration` w `WadCfg` szablonie będzie wyglądać następujący przykład:

```json
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
              },
              {
                "provider": "02d06793-efeb-48c8-8f7f-09713309a810",
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
```

## <a name="collect-from-new-eventsource-channels"></a>Zbierz z nowych kanałów EventSource

Aby zaktualizować diagnostykę w celu zbierania dzienników z nowych kanałów EventSource, które reprezentują nową aplikację, którą zamierzasz wdrożyć, wykonaj te same czynności, które zostały opisane wcześniej w celu skonfigurowania diagnostyki dla istniejącego klastra.

Zaktualizuj `EtwEventSourceProviderConfiguration` sekcję w template.jsna pliku, aby dodać wpisy dla nowych kanałów EventSource przed zastosowaniem aktualizacji konfiguracji za pomocą `New-AzResourceGroupDeployment` polecenia programu PowerShell. Nazwa źródła zdarzenia jest definiowana jako część kodu w pliku ServiceEventSource.cs wygenerowanego przez program Visual Studio.

Na przykład jeśli źródło zdarzenia ma nazwę my-EventSource, Dodaj następujący kod, aby umieścić zdarzenia z My-Eventsource w tabeli o nazwie MyDestinationTableName.

```json
        {
            "provider": "My-Eventsource",
            "scheduledTransferPeriod": "PT5M",
            "DefaultEvents": {
            "eventDestination": "MyDestinationTableName"
            }
        }
```

W celu zbierania liczników wydajności lub dzienników zdarzeń należy zmodyfikować szablon Menedżer zasobów przy użyciu przykładów podanych w temacie [Tworzenie maszyny wirtualnej z systemem Windows z funkcją monitorowania i diagnostyki przy użyciu szablonu Azure Resource Manager](../virtual-machines/extensions/diagnostics-template.md?toc=/azure/virtual-machines/windows/toc.json). Następnie ponownie Opublikuj szablon Menedżer zasobów.

## <a name="collect-performance-counters"></a>Zbieranie liczników wydajności

Aby zbierać metryki wydajności z klastra, Dodaj liczniki wydajności do "WadCfg > DiagnosticMonitorConfiguration" w szablonie Menedżer zasobów dla klastra. Zobacz [monitorowanie wydajności w programie funkcji wad](service-fabric-diagnostics-perf-wad.md) , aby poznać procedurę modyfikowania programu `WadCfg` w celu zbierania określonych liczników wydajności. Informacje dotyczące [liczników wydajności Service Fabric](service-fabric-diagnostics-event-generation-perf.md) , aby uzyskać listę liczników wydajności, które zalecamy zbieranie.
  
Jeśli używasz ujścia Application Insights, zgodnie z opisem w sekcji poniżej, i chcesz, aby te metryki były wyświetlane w Application Insights, a następnie upewnij się, że nazwa ujścia została dodana w sekcji "ujścia", jak pokazano powyżej. Spowoduje to automatyczne wysłanie liczników wydajności, które są indywidualnie skonfigurowane dla zasobu Application Insights.


## <a name="send-logs-to-application-insights"></a>Wyślij dzienniki do Application Insights

### <a name="configuring-application-insights-with-wad"></a>Konfigurowanie Application Insights przy użyciu funkcji wad

>[!NOTE]
>Jest to możliwe tylko w przypadku klastrów systemu Windows.

Istnieją dwa podstawowe sposoby wysyłania danych z funkcji wad do platformy Azure Application Insights, które są osiągane przez dodanie Application Insights ujścia do konfiguracji funkcji wad, za pośrednictwem Azure Portal lub przez szablon Azure Resource Manager.

#### <a name="add-an-application-insights-instrumentation-key-when-creating-a-cluster-in-azure-portal"></a>Dodaj klucz Instrumentacji Application Insights podczas tworzenia klastra w programie Azure Portal

![Dodawanie elementu AIKey](media/service-fabric-diagnostics-event-analysis-appinsights/azure-enable-diagnostics.png)

W przypadku tworzenia klastra, jeśli Diagnostyka jest włączona, w polu jest wyświetlane opcjonalne pole umożliwiające wprowadzenie Application Insights klucza Instrumentacji. Jeśli wkleisz tutaj klucz Application Insights, ujścia Application Insights zostanie automatycznie skonfigurowane dla Ciebie w Menedżer zasobów szablonu, który jest używany do wdrożenia klastra.

#### <a name="add-the-application-insights-sink-to-the-resource-manager-template"></a>Dodawanie ujścia Application Insights do szablonu Menedżer zasobów

W "WadCfg" szablonu Menedżer zasobów Dodaj "ujścia", uwzględniając następujące dwie zmiany:

1. Dodaj konfigurację ujścia bezpośrednio po zakończeniu deklarowania `DiagnosticMonitorConfiguration` :

    ```json
    "SinksConfig": {
        "Sink": [
            {
                "name": "applicationInsights",
                "ApplicationInsights": "***ADD INSTRUMENTATION KEY HERE***"
            }
        ]
    }

    ```

2. Uwzględnij ujścia w elemencie, `DiagnosticMonitorConfiguration` dodając następujący wiersz w `DiagnosticMonitorConfiguration` `WadCfg` (po prawej stronie przed `EtwProviders` zadeklarowanymi):

    ```json
    "sinks": "applicationInsights"
    ```

W przypadku powyższych fragmentów kodu nazwa "applicationInsights" została użyta do opisania ujścia. Nie jest to wymagane i tak długo, jak nazwa ujścia jest zawarta w "ujścia", można ustawić nazwę na dowolny ciąg.

Obecnie dzienniki z klastra są wyświetlane jako **ślady** w przeglądarce dzienników Application Insights. Ponieważ większość śladów pochodzących z platformy ma poziom "informacyjny", można również rozważyć zmianę konfiguracji ujścia tak, aby tylko wysyłali dzienniki typu "ostrzeżenie" lub "błąd". Można to zrobić, dodając "kanały" do ujścia, jak pokazano w [tym artykule](../azure-monitor/agents/diagnostics-extension-to-application-insights.md).

>[!NOTE]
>Jeśli używasz nieprawidłowego klucza Application Insights w portalu lub w szablonie Menedżer zasobów, musisz ręcznie zmienić klucz i zaktualizować klaster/wdrożyć go ponownie.

## <a name="next-steps"></a>Następne kroki

Po poprawnym skonfigurowaniu diagnostyki platformy Azure dane będą widoczne w tabelach magazynu z dzienników ETW i EventSource. Jeśli zdecydujesz się użyć dzienników Azure Monitor, Kibana lub innej platformy do analizy danych i wizualizacji, która nie jest bezpośrednio skonfigurowana w szablonie Menedżer zasobów, pamiętaj, aby skonfigurować wybraną platformę do odczytywania danych z tych tabel magazynu. Ta operacja w przypadku dzienników Azure Monitor jest stosunkowo prosta i wyjaśniona jest [Analiza zdarzeń i dzienników](service-fabric-diagnostics-event-analysis-oms.md). Application Insights w tym sensie jest to bit specjalnego przypadku, ponieważ można go skonfigurować w ramach konfiguracji rozszerzenia diagnostyki, dlatego należy zapoznać się z [odpowiednim artykułem](service-fabric-diagnostics-event-analysis-appinsights.md) , jeśli zdecydujesz się używać systemu AI.

>[!NOTE]
>Obecnie nie ma możliwości filtrowania lub pielęgnacji zdarzeń wysyłanych do tabeli. Jeśli nie zaimplementujesz procesu usuwania zdarzeń z tabeli, będzie ona nadal rosnąć. Obecnie istnieje przykład usługi pielęgnacji danych działającej w [próbce licznika alarmowego](https://github.com/Azure-Samples/service-fabric-watchdog-service)i zaleca się napisać jeden dla siebie, chyba że istnieje dobry powód przechowywania dzienników poza okresem 30-lub 90 dni.

* [Informacje na temat zbierania liczników wydajności lub dzienników przy użyciu rozszerzenia diagnostyki](../virtual-machines/extensions/diagnostics-template.md?toc=/azure/virtual-machines/windows/toc.json)
* [Analiza zdarzeń i wizualizacja przy użyciu Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md)
* [Analiza zdarzeń i wizualizacja z dziennikami Azure Monitor](service-fabric-diagnostics-event-analysis-oms.md)
* [Analiza zdarzeń i wizualizacja przy użyciu Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md)
* [Analiza zdarzeń i wizualizacja z dziennikami Azure Monitor](service-fabric-diagnostics-event-analysis-oms.md)
