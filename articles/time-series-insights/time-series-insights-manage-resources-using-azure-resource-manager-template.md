---
title: Zarządzanie środowiskiem przy użyciu szablonów usługi Azure Resource Manager — usługa Azure Time Series Insights | Dokumenty firmy Microsoft
description: Dowiedz się, jak programowo zarządzać środowiskiem usługi Azure Time Series Insights przy użyciu usługi Azure Resource Manager.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/16/2020
ms.custom: seodec18
ms.openlocfilehash: a670e32058794daeaa233464ba7d054f45ef25e3
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536322"
---
# <a name="create-time-series-insights-resources-using-azure-resource-manager-templates"></a>Tworzenie zasobów usługi Time Series Insights przy użyciu szablonów usługi Azure Resource Manager

W tym artykule opisano sposób tworzenia i wdrażania zasobów usługi Time Series Insights przy użyciu [szablonów usługi Azure Resource Manager,](https://docs.microsoft.com/azure/azure-resource-manager/)programu PowerShell i dostawcy zasobów usługi Time Series Insights.

Aplikacja Time Series Insights obsługuje następujące zasoby:

   | Zasób | Opis |
   | --- | --- |
   | Środowisko | Środowisko usługi Time Series Insights to logiczne grupowanie zdarzeń odczytywanych z brokerów zdarzeń, przechowywanych i udostępnianych do kwerendy. Aby uzyskać więcej informacji, przeczytaj [artykuł Planowanie środowiska usługi Azure Time Series Insights](time-series-insights-environment-planning.md) |
   | Źródło zdarzeń | Źródło zdarzeń jest połączenie z brokerem zdarzeń, z którego usługa Time Series Insights odczytuje i pozyskuje zdarzenia w środowisku. Obecnie obsługiwane źródła zdarzeń to Centrum IoT i Centrum zdarzeń. |
   | Zestaw danych referencyjnych | Zestawy danych referencyjnych zawierają metadane dotyczące zdarzeń w środowisku. Metadane w zestawach danych referencyjnych zostaną połączone ze zdarzeniami podczas transferu danych przychodzących. Zestawy danych referencyjnych są definiowane jako zasoby przez ich właściwości klucza zdarzeń. Rzeczywiste metadane, które tworzą zestaw danych referencyjnych jest przekazywał lub modyfikowany za pośrednictwem interfejsów API płaszczyzny danych. |
   | Zasady dostępu | Zasady dostępu udzielają uprawnień do wystawiania zapytań o dane, manipulowania danymi referencyjnymi w środowisku i udostępniania zapisanych zapytań i perspektyw skojarzonych ze środowiskiem. Aby uzyskać więcej informacji, zobacz [Udzielanie dostępu do danych do środowiska usługi Time Series Insights przy użyciu witryny Azure portal](time-series-insights-data-access.md) |

Szablon Menedżera zasobów to plik JSON definiujący infrastrukturę i konfigurację zasobów w grupie zasobów. W poniższych dokumentach opisano pliki szablonów bardziej szczegółowo:

- [Wdrażanie szablonu usługi Azure Resource Manager](../azure-resource-manager/templates/overview.md)
- [Deploy resources with Resource Manager templates and Azure PowerShell (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i programu Azure PowerShell)](../azure-resource-manager/templates/deploy-powershell.md)
- [Typy zasobów Microsoft.TimeSeriesInsights](/azure/templates/microsoft.timeseriesinsights/allversions)

Szablon szybkiego startu [201-timeseriesinsights-environment-with-eventhub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-timeseriesinsights-environment-with-eventhub) jest publikowany w usłudze GitHub. Ten szablon tworzy środowisko usługi Time Series Insights, podrzędne źródło zdarzeń skonfigurowane do korzystania ze zdarzeń z Centrum zdarzeń i zasady dostępu, które udzielają dostępu do danych środowiska. Jeśli istniejące Centrum zdarzeń nie zostanie określony, jeden zostanie utworzony z wdrożeniem.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="specify-deployment-template-and-parameters"></a>Określanie szablonu wdrożenia i parametrów

W poniższej procedurze opisano sposób używania programu PowerShell do wdrażania szablonu usługi Azure Resource Manager, który tworzy środowisko usługi Time Series Insights, podrzędne źródło zdarzeń skonfigurowane do korzystania ze zdarzeń z Centrum zdarzeń i zasady dostępu, które udzielają dostępu do danych środowiska. Jeśli istniejące Centrum zdarzeń nie zostanie określony, jeden zostanie utworzony z wdrożeniem.

1. Zainstaluj program Azure PowerShell, postępując zgodnie z instrukcjami w [programie Wprowadzenie do programu Azure PowerShell.](https://docs.microsoft.com/powershell/azure/get-started-azureps)

1. Klonuj lub kopiuj szablon [201-timeseriesinsights-environment-with-eventhub](https://github.com/Azure/azure-quickstart-templates/blob/master/201-timeseriesinsights-environment-with-eventhub/azuredeploy.json) z gitHub.

   * Tworzenie pliku parametrów

     Aby utworzyć plik parametrów, skopiuj plik [201-timeseriesinsights-environment-with-eventhub.](https://github.com/Azure/azure-quickstart-templates/blob/master/201-timeseriesinsights-environment-with-eventhub/azuredeploy.parameters.json)

      [!code-json[deployment-parameters](~/quickstart-templates/201-timeseriesinsights-environment-with-eventhub/azuredeploy.parameters.json)]

    <div id="required-parameters"></div>

   * Parametry wymagane

     | Parametr | Opis |
     | --- | --- |
     | eventHubNamespaceName | Obszar nazw centrum zdarzeń źródłowych. |
     | eventHubName | Nazwa centrum zdarzeń źródłowych. |
     | consumerGroupName (Nazwa grupy konsumentów) | Nazwa grupy konsumentów, która usługa Time Series Insights będzie używać do odczytu danych z centrum zdarzeń. **UWAGA:** Aby uniknąć rywalizacji o zasoby, ta grupa konsumentów musi być dedykowana do usługi Time Series Insights i nie jest udostępniana innym czytelnikom. |
     | nazwa środowiska | Nazwa środowiska. Nazwa nie może `<` `>`zawierać: , `\\` `?`, `/` `%`, `&` `:`, , , , i żadnych znaków kontrolnych. Wszystkie inne znaki są dozwolone.|
     | nazwa źródła zdarzenia | Nazwa źródła zdarzenia podrzędnego zasobu. Nazwa nie może `<` `>`zawierać: , `\\` `?`, `/` `%`, `&` `:`, , , , i żadnych znaków kontrolnych. Wszystkie inne znaki są dozwolone. |

    <div id="optional-parameters"></div>

   * Parametry opcjonalne

     | Parametr | Opis |
     | --- | --- |
     | existingEventHubResourceId | Opcjonalny identyfikator zasobu istniejącego Centrum zdarzeń, który będzie połączony ze środowiskiem usługi Time Series Insights za pośrednictwem źródła zdarzeń. **UWAGA:** Użytkownik wdrażający szablon musi mieć uprawnienia do wykonywania operacji klawiszy listkeys w Centrum zdarzeń. Jeśli żadna wartość nie zostanie przekazana, nowy centrum zdarzeń zostanie utworzony przez szablon. |
     | środowiskoWyświetlajname | Opcjonalna przyjazna nazwa do wyświetlenia w narzędziach lub interfejsach użytkownika zamiast nazwy środowiska. |
     | środowiskoSkuName | Nazwa jednostki SKU. Aby uzyskać więcej informacji, przeczytaj [stronę Cennik wglądu](https://azure.microsoft.com/pricing/details/time-series-insights/)w szczegóły dotyczące szeregów czasowych .  |
     | środowiskoSkuCapacity | Pojemność jednostki Sku. Aby uzyskać więcej informacji, przeczytaj [stronę Cennik wglądu](https://azure.microsoft.com/pricing/details/time-series-insights/)w szczegóły dotyczące szeregów czasowych .|
     | środowiskoDataRetentionCzas | Minimalny czas zdarzenia środowiska będą dostępne dla kwerendy. Wartość musi być określona w formacie ISO 8601, na przykład `P30D` dla zasad przechowywania 30 dni. |
     | eventSourceDisplayName | Opcjonalna przyjazna nazwa do wyświetlenia w narzędziach lub interfejsach użytkownika zamiast nazwy źródła zdarzenia. |
     | nazwa właściwości eventSourceTimestampPropertyName | Właściwość zdarzenia, która będzie używana jako sygnatura czasowa źródła zdarzenia. Jeśli wartość nie jest określona dla timestampPropertyName lub jeśli określono wartość null lub pusty ciąg, zostanie użyty czas tworzenia zdarzenia. |
     | eventSourceKeyName | Nazwa klucza dostępu udostępnionego, który usługa Usługi Time Series Insights będzie używać do łączenia się z centrum zdarzeń. |
     | identyfikatory accessPolicyReaderObjectIds | Lista identyfikatorów obiektów użytkowników lub aplikacji w usłudze Azure AD, które powinny mieć dostęp programu Reader do środowiska. Obiekt podmiotu usługi można uzyskać, wywołując polecenia cmdlet **Get-AzADUser** lub **Get-AzADServicePrincipal.** Tworzenie zasad dostępu dla grup usługi Azure AD nie jest jeszcze obsługiwane. |
     | accessPolicyContributorObjectIds | Lista identyfikatorów obiektów użytkowników lub aplikacji w usłudze Azure AD, które powinny mieć dostęp współautora do środowiska. Obiekt podmiotu usługi można uzyskać, wywołując polecenia cmdlet **Get-AzADUser** lub **Get-AzADServicePrincipal.** Tworzenie zasad dostępu dla grup usługi Azure AD nie jest jeszcze obsługiwane. |

   * Na przykład następujący plik parametrów będzie używany do tworzenia środowiska i źródła zdarzeń, które odczytuje zdarzenia z istniejącego centrum zdarzeń. Tworzy również dwie zasady dostępu, które zapewniają współautorowi dostęp do środowiska.

     ```JSON
     {
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
             "eventHubNamespaceName": {
                 "value": "tsiTemplateTestNamespace"
             },
             "eventHubName": {
                 "value": "tsiTemplateTestEventHub"
             },
             "consumerGroupName": {
                 "value": "tsiTemplateTestConsumerGroup"
             },
             "environmentName": {
                 "value": "tsiTemplateTestEnvironment"
             },
             "eventSourceName": {
                 "value": "tsiTemplateTestEventSource"
             },
             "existingEventHubResourceId": {
                 "value": "/subscriptions/{yourSubscription}/resourceGroups/MyDemoRG/providers/Microsoft.EventHub/namespaces/tsiTemplateTestNamespace/eventhubs/tsiTemplateTestEventHub"
             },
             "accessPolicyContributorObjectIds": {
                 "value": [
                     "AGUID001-0000-0000-0000-000000000000",
                     "AGUID002-0000-0000-0000-000000000000"
                 ]
             }    
         }
     }
     ```

    * Aby uzyskać więcej informacji, przeczytaj [parametrów](../azure-resource-manager/templates/parameter-files.md) artykułu.

## <a name="deploy-the-quickstart-template-locally-using-powershell"></a>Wdrażanie szablonu przewodnika Szybki start lokalnie przy użyciu programu PowerShell

> [!IMPORTANT]
> Operacje wiersza polecenia wyświetlane poniżej opisują [moduł Az PowerShell](https://docs.microsoft.com/powershell/azure/overview).

1. W programie PowerShell zaloguj się do swojego konta platformy Azure.

    * Z monitu programu PowerShell uruchom następujące polecenie:

      ```powershell
      Connect-AzAccount
      ```

    * Zostanie wyświetlony monit o zalogowanie się do konta platformy Azure. Po zalogowaniu się uruchom następujące polecenie, aby wyświetlić dostępne subskrypcje:

      ```powershell
      Get-AzSubscription
      ```

    * To polecenie zwraca listę dostępnych subskrypcji platformy Azure. Wybierz subskrypcję dla bieżącej sesji, uruchamiając następujące polecenie. Zamień `<YourSubscriptionId>` identyfikator GUID dla subskrypcji platformy Azure, której chcesz użyć:

      ```powershell
      Set-AzContext -SubscriptionID <YourSubscriptionId>
      ```

1. Utwórz nową grupę zasobów, jeśli jej nie ma.

   * Jeśli nie masz istniejącej grupy zasobów, utwórz nową grupę zasobów za pomocą polecenia **New-AzResourceGroup.** Podaj nazwę grupy zasobów i lokalizację, której chcesz użyć. Przykład:

     ```powershell
     New-AzResourceGroup -Name MyDemoRG -Location "West US"
     ```

   * Jeśli się powiedzie, zostanie wyświetlone podsumowanie nowej grupy zasobów.

     ```powershell
     ResourceGroupName : MyDemoRG
     Location          : westus
     ProvisioningState : Succeeded
     Tags              :
     ResourceId        : /subscriptions/<GUID>/resourceGroups/MyDemoRG
     ```

1. Przetestuj wdrożenie.

   * Sprawdź poprawność wdrożenia, `Test-AzResourceGroupDeployment` uruchamiając polecenie cmdlet. Podczas testowania wdrożenia należy podać parametry dokładnie tak, jak podczas wykonywania wdrożenia.

     ```powershell
     Test-AzResourceGroupDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
     ```

1. Tworzenie wdrożenia

    * Aby utworzyć nowe wdrożenie, `New-AzResourceGroupDeployment` uruchom polecenie cmdlet i podaj niezbędne parametry po wyświetleniu monitu. Parametry obejmują nazwę wdrożenia, nazwę grupy zasobów oraz ścieżkę lub adres URL do pliku szablonu. Jeśli parametr **Mode** nie jest określony, używana jest wartość domyślna **przyrostowa.** Aby uzyskać więcej informacji, przeczytaj artykuł [Przyrostowe i kompletne wdrożenia](../azure-resource-manager/templates/deployment-modes.md).

    * Następujące polecenie monituje o pięć wymaganych parametrów w oknie programu PowerShell:

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
      ```

    * Zamiast tego należy określić plik parametrów, należy użyć następującego polecenia:

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
      ```

    * Po uruchomieniu polecenia cmdlet wdrożenia można również użyć parametrów wbudowanych. Polecenie to wygląda następująco:

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -parameterName "parameterValue"
      ```

    * Aby uruchomić [pełne](../azure-resource-manager/templates/deployment-modes.md) wdrożenie, ustaw parametr **Tryb** na **Zakończ:**

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -Mode Complete -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
      ```

1. Weryfikowanie wdrożenia

    * Jeśli zasoby zostaną pomyślnie wdrożone, podsumowanie wdrożenia jest wyświetlane w oknie programu PowerShell:

      ```powershell
       DeploymentName          : MyDemoDeployment
       ResourceGroupName       : MyDemoRG
       ProvisioningState       : Succeeded
       Timestamp               : 10/11/2019 3:20:37 AM
       Mode                    : Incremental
       TemplateLink            :
       Parameters              :
                                 Name                                Type                       Value
                                 ==================================  =========================  ==========
                                 eventHubNewOrExisting               String                     new
                                 eventHubResourceGroup               String                     MyDemoRG
                                 eventHubNamespaceName               String                     tsiquickstartns
                                 eventHubName                        String                     tsiquickstarteh
                                 consumerGroupName                   String                     tsiquickstart
                                 environmentName                     String                     tsiquickstart
                                 environmentDisplayName              String                     tsiquickstart
                                 environmentSkuName                  String                     S1
                                 environmentSkuCapacity              Int                        1
                                 environmentDataRetentionTime        String                     P30D
                                 eventSourceName                     String                     tsiquickstart
                                 eventSourceDisplayName              String                     tsiquickstart
                                 eventSourceTimestampPropertyName    String
                                 eventSourceKeyName                  String                     manage
                                 accessPolicyReaderObjectIds         Array                      []
                                 accessPolicyContributorObjectIds    Array                      []
                                 location                            String                     westus

       Outputs                 :
                                  Name              Type                       Value
                                  ================  =========================  ==========
                                  dataAccessFQDN    String
                                  11aa1aa1-a1aa-1a1a-a11a-aa111a111a11.env.timeseries.azure.com

       DeploymentDebugLogLevel :
      ```

1. Wdrażanie szablonu przewodnika Szybki start za pośrednictwem witryny Azure Portal

   * Strona główna szablonu przewodnika Szybki start w usłudze GitHub zawiera również przycisk **Wdrażanie na platformie Azure.** Kliknięcie przycisku powoduje otwarcie strony wdrożenia niestandardowego w witrynie Azure portal. Na tej stronie można wprowadzić lub wybrać wartości dla każdego z parametrów z [wymaganych parametrów](#required-parameters) lub tabel [parametrów opcjonalnych.](#optional-parameters) Po wypełnieniu ustawień kliknięcie przycisku **Zakup** spowoduje zainicjowanie wdrożenia szablonu.
    </br>
    </br>
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-timeseriesinsights-environment-with-eventhub%2Fazuredeploy.json" target="_blank">
       <img src="https://azuredeploy.net/deploybutton.png"/>
    </a>

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać informacje na temat programowego zarządzania zasobami usługi Time Series Insights przy użyciu interfejsów API REST, przeczytaj [artykuł Zarządzanie analizą szeregów czasowych](https://docs.microsoft.com/rest/api/time-series-insights-management/).
