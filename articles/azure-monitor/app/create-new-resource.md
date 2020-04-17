---
title: Tworzenie nowego zasobu usługi Azure Application Insights | Dokumenty firmy Microsoft
description: Ręcznie skonfiguruj monitorowanie usługi Application Insights dla nowej aplikacji aktywnej.
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 0c8b9ccaa70a2fd1bf46c6f4537f54d702ecc48f
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537580"
---
# <a name="create-an-application-insights-resource"></a>Tworzenie zasobu usługi Application Insights

Usługa Azure Application Insights wyświetla dane dotyczące aplikacji w *zasobie*platformy Microsoft Azure. Tworzenie nowego zasobu jest zatem częścią [konfigurowania usługi Application Insights w celu monitorowania nowej aplikacji][start]. Po utworzeniu nowego zasobu można uzyskać jego klucz instrumentacji i użyć go do skonfigurowania SDK usługi Application Insights. Klucz instrumentacji łączy dane telemetryczne z zasobem.

## <a name="sign-in-to-microsoft-azure"></a>Logowanie się do platformy Microsoft Azure

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne](https://azure.microsoft.com/free/) konto przed rozpoczęciem.

## <a name="create-an-application-insights-resource"></a>Tworzenie zasobu usługi Application Insights

Zaloguj się do [witryny Azure portal](https://portal.azure.com)i utwórz zasób usługi Application Insights:

![Kliknij znak "+" w lewym górnym rogu. Wybierz pozycję Narzędzia deweloperskie, po których następuje usługa Application Insights](./media/create-new-resource/new-app-insights.png)

   | Ustawienia        |  Wartość           | Opis  |
   | ------------- |:-------------|:-----|
   | **Nazwa**      | Unikatowa wartość | Nazwa identyfikującya monitorowana aplikację. |
   | **Grupa zasobów**     | myResourceGroup      | Nazwa nowej lub istniejącej grupy zasobów do hosta danych usługi App Insights. |
   | **Lokalizacja** | Wschodnie stany USA | Wybierz lokalizację w pobliżu lub w pobliżu miejsca, w którym znajduje się aplikacja. |

> [!NOTE]
> Chociaż można użyć tej samej nazwy zasobu w różnych grupach zasobów, może być korzystne użycie globalnie unikatowej nazwy. Może to być przydatne, jeśli planujesz [wykonywać kwerendy między zasobami,](https://docs.microsoft.com/azure/azure-monitor/log-query/cross-workspace-query#identifying-an-application) ponieważ upraszcza wymaganą składnię.

Wprowadź odpowiednie wartości w wymaganych polach, a następnie wybierz **pozycję Przejrzyj + utwórz**.

![Wprowadź wartości w wymaganych polach, a następnie wybierz "przejrzyj + utwórz".](./media/create-new-resource/review-create.png)

Po utworzeniu aplikacji zostanie otwarte nowe okienko. W tym okienku są widoczne dane dotyczące wydajności i użycia monitorowana aplikacji. 

## <a name="copy-the-instrumentation-key"></a>Kopiowanie klucza oprzyrządowania

Klucz instrumentacji identyfikuje zasób, z którego chcesz skojarzyć dane telemetryczne. Należy skopiować klucz instrumentacji i dodać go do kodu aplikacji.

![Kliknij i skopiuj klucz instrumentacji](./media/create-new-resource/instrumentation-key.png)

## <a name="install-the-sdk-in-your-app"></a>Instalowanie SDK w aplikacji

Zainstaluj sdk usługi Application Insights w aplikacji. Ten krok zależy w dużej mierze od typu aplikacji.

Użyj klucza instrumentacji, aby skonfigurować [zestaw SDK instalowane w aplikacji][start].

Zestaw SDK zawiera standardowe moduły, które wysyłają dane telemetryczne bez konieczności pisania dodatkowego kodu. Aby śledzić akcje użytkownika lub diagnozować problemy bardziej szczegółowo, [użyj interfejsu API,][api] aby wysłać własną dane telemetryczne.

## <a name="creating-a-resource-automatically"></a>Automatyczne tworzenie zasobu

### <a name="powershell"></a>PowerShell

Tworzenie nowego zasobu usługi Application Insights

```powershell
New-AzApplicationInsights [-ResourceGroupName] <String> [-Name] <String> [-Location] <String> [-Kind <String>]
 [-Tag <Hashtable>] [-DefaultProfile <IAzureContextContainer>] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="example"></a>Przykład

```powershell
New-AzApplicationInsights -Kind java -ResourceGroupName testgroup -Name test1027 -location eastus
```
#### <a name="results"></a>Wyniki

```powershell
Id                 : /subscriptions/{subid}/resourceGroups/testgroup/providers/microsoft.insights/components/test1027
ResourceGroupName  : testgroup
Name               : test1027
Kind               : web
Location           : eastus
Type               : microsoft.insights/components
AppId              : 8323fb13-32aa-46af-b467-8355cf4f8f98
ApplicationType    : web
Tags               : {}
CreationDate       : 10/27/2017 4:56:40 PM
FlowType           :
HockeyAppId        :
HockeyAppToken     :
InstrumentationKey : 00000000-aaaa-bbbb-cccc-dddddddddddd
ProvisioningState  : Succeeded
RequestSource      : AzurePowerShell
SamplingPercentage :
TenantId           : {subid}
```

Aby uzyskać pełną dokumentację programu PowerShell dla tego polecenia cmdlet i dowiedzieć się, jak pobrać klucz instrumentacji, zapoznaj się z [dokumentacją programu Azure PowerShell.](https://docs.microsoft.com/powershell/module/az.applicationinsights/new-azapplicationinsights?view=azps-2.5.0)

### <a name="azure-cli-preview"></a>Azure CLI (wersja zapoznawcza)

Aby uzyskać dostęp do poleceń interfejsu wiersza polecenia usługi Azure cli usługi Application Insights w wersji zapoznawczej, należy najpierw uruchomić:

```azurecli
 az extension add -n application-insights
```

Jeśli polecenie nie zostanie `az extension add` uruchomione, zostanie wyświetlony komunikat o błędzie:`az : ERROR: az monitor: 'app-insights' is not in the 'az monitor' command group. See 'az monitor --help'.`

Teraz można uruchomić następujące, aby utworzyć zasób usługi Application Insights:

```azurecli
az monitor app-insights component create --app
                                         --location
                                         --resource-group
                                         [--application-type]
                                         [--kind]
                                         [--tags]
```

#### <a name="example"></a>Przykład

```azurecli
az monitor app-insights component create --app demoApp --location westus2 --kind web -g demoRg --application-type web
```

#### <a name="results"></a>Wyniki

```azurecli
az monitor app-insights component create --app demoApp --location eastus --kind web -g demoApp  --application-type web
{
  "appId": "87ba512c-e8c9-48d7-b6eb-118d4aee2697",
  "applicationId": "demoApp",
  "applicationType": "web",
  "creationDate": "2019-08-16T18:15:59.740014+00:00",
  "etag": "\"0300edb9-0000-0100-0000-5d56f2e00000\"",
  "flowType": "Bluefield",
  "hockeyAppId": null,
  "hockeyAppToken": null,
  "id": "/subscriptions/{subid}/resourceGroups/demoApp/providers/microsoft.insights/components/demoApp",
  "instrumentationKey": "00000000-aaaa-bbbb-cccc-dddddddddddd",
  "kind": "web",
  "location": "eastus",
  "name": "demoApp",
  "provisioningState": "Succeeded",
  "requestSource": "rest",
  "resourceGroup": "demoApp",
  "samplingPercentage": null,
  "tags": {},
  "tenantId": {tenantID},
  "type": "microsoft.insights/components"
}
```

Aby uzyskać pełną dokumentację interfejsu wiersza polecenia platformy Azure dla tego polecenia i dowiedzieć się, jak pobrać klucz instrumentacji, zapoznaj się z [dokumentacją interfejsu wiersza polecenia platformy Azure.](https://docs.microsoft.com/cli/azure/ext/application-insights/monitor/app-insights/component?view=azure-cli-latest#ext-application-insights-az-monitor-app-insights-component-create)

## <a name="next-steps"></a>Następne kroki
* [Wyszukiwanie diagnostyczne](../../azure-monitor/app/diagnostic-search.md)
* [Eksplorowanie metryk](../../azure-monitor/platform/metrics-charts.md)
* [Pisanie zapytań analitycznych](../../azure-monitor/app/analytics.md)

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[metrics]: ../../azure-monitor/platform/metrics-charts.md
[start]: ../../azure-monitor/app/app-insights-overview.md
