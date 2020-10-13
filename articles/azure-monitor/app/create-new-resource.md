---
title: Tworzenie nowego zasobu usługi Azure Application Insights | Microsoft Docs
description: Ręcznie skonfiguruj Application Insights monitorowania dla nowej aplikacji na żywo.
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: d2e367e84aed7abac70d803f28d26070f7b0a85e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87323132"
---
# <a name="create-an-application-insights-resource"></a>Tworzenie zasobu usługi Application Insights

Usługa Azure Application Insights wyświetla dane dotyczące aplikacji w Microsoft Azure *zasobu*. Tworzenie nowego zasobu jest w związku z tym częścią [konfigurowania Application Insights do monitorowania nowej aplikacji][start]. Po utworzeniu nowego zasobu można pobrać jego klucz Instrumentacji i użyć go do skonfigurowania zestawu SDK Application Insights. Klucz Instrumentacji łączy dane telemetryczne z zasobem.

## <a name="sign-in-to-microsoft-azure"></a>Zaloguj się do Microsoft Azure

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="create-an-application-insights-resource"></a>Tworzenie zasobu usługi Application Insights

Zaloguj się do [Azure Portal](https://portal.azure.com)i utwórz zasób Application Insights:

![Kliknij znak "+" w lewym górnym rogu. Wybierz Narzędzia deweloperskie a następnie Application Insights](./media/create-new-resource/new-app-insights.png)

   | Ustawienia        |  Wartość           | Opis  |
   | ------------- |:-------------|:-----|
   | **Nazwa**      | `Unique value` | Nazwa identyfikująca monitorowaną aplikację. |
   | **Grupa zasobów**     | `myResourceGroup`      | Nazwa nowej lub istniejącej grupy zasobów do hostowania danych usługi App Insights. |
   | **Region** | `East US` | Wybierz lokalizację znajdującą się w sąsiedztwie lub w miejscu, w którym znajduje się aplikacja. |
   | **Tryb zasobów** | `Classic` lub `Workspace-based` | Zasoby oparte na obszarze roboczym są obecnie dostępne w publicznej wersji zapoznawczej i umożliwiają wysyłanie danych telemetrycznych Application Insights do wspólnego Log Analytics obszaru roboczego. Aby uzyskać więcej informacji, zobacz [artykuł dotyczący zasobów obszaru roboczego](create-workspace-resource.md).

> [!NOTE]
> Chociaż można używać tej samej nazwy zasobu w różnych grupach zasobów, może być korzystne użycie globalnie unikatowej nazwy. Może to być przydatne, jeśli planujesz [wykonywać zapytania między zasobami](../log-query/cross-workspace-query.md#identifying-an-application) , ponieważ upraszczają one wymaganą składnię.

Wprowadź odpowiednie wartości w wymaganych polach, a następnie wybierz pozycję **Przegląd + Utwórz**.

![Wprowadź wartości w wymaganych polach, a następnie wybierz pozycję "Przejrzyj + Utwórz".](./media/create-new-resource/review-create.png)

Po utworzeniu aplikacji zostanie otwarte nowe okienko. To okienko służy do wyświetlania danych dotyczących wydajności i użycia dotyczących monitorowanej aplikacji. 

## <a name="copy-the-instrumentation-key"></a>Kopiowanie klucza Instrumentacji

Klucz Instrumentacji identyfikuje zasób, z którym chcesz skojarzyć dane telemetryczne. Konieczne będzie skopiowanie klucza Instrumentacji i dodanie go do kodu aplikacji.

![Kliknij i skopiuj klucz Instrumentacji](./media/create-new-resource/instrumentation-key.png)

## <a name="install-the-sdk-in-your-app"></a>Instalowanie zestawu SDK w aplikacji

Zainstaluj zestaw SDK Application Insights w aplikacji. Ten krok zależy w dużym stopniu od typu aplikacji.

Użyj klucza Instrumentacja, aby skonfigurować [zestaw SDK, który zostanie zainstalowany w aplikacji][start].

Zestaw SDK zawiera standardowe moduły wysyłające dane telemetryczne bez konieczności pisania dodatkowego kodu. Aby śledzić akcje użytkownika lub diagnozować problemy bardziej szczegółowo, [Użyj interfejsu API][api] do wysyłania własnej telemetrii.

## <a name="creating-a-resource-automatically"></a>Automatyczne tworzenie zasobu

### <a name="powershell"></a>Program PowerShell

Tworzenie nowego zasobu Application Insights

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

Pełna Dokumentacja programu PowerShell dla tego polecenia cmdlet i Dowiedz się, jak pobrać klucz instrumentacji, zapoznaj się z [dokumentacją Azure PowerShell](/powershell/module/az.applicationinsights/new-azapplicationinsights?view=azps-2.5.0).

### <a name="azure-cli-preview"></a>Interfejs wiersza polecenia platformy Azure (wersja zapoznawcza)

Aby uzyskać dostęp do Application Insights poleceń interfejsu wiersza polecenia platformy Azure w wersji zapoznawczej, należy najpierw wykonać następujące działania:

```azurecli
 az extension add -n application-insights
```

Jeśli polecenie nie zostanie uruchomione `az extension add` , zostanie wyświetlony komunikat o błędzie informujący o tym, że: `az : ERROR: az monitor: 'app-insights' is not in the 'az monitor' command group. See 'az monitor --help'.`

Teraz można wykonać następujące czynności, aby utworzyć zasób Application Insights:

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

Aby zapoznać się z pełną dokumentacją interfejsu wiersza polecenia platformy Azure dotyczącymi tego poleceń i dowiedzieć się, jak pobrać klucz instrumentacji, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure/ext/application-insights/monitor/app-insights/component?view=azure-cli-latest#ext-application-insights-az-monitor-app-insights-component-create).

## <a name="next-steps"></a>Następne kroki
* [Wyszukiwanie diagnostyczne](./diagnostic-search.md)
* [Eksploruj metryki](../platform/metrics-charts.md)
* [Pisanie zapytań analitycznych](../log-query/log-query-overview.md)

<!--Link references-->

[api]: ./api-custom-events-metrics.md
[diagnostic]: ./diagnostic-search.md
[metrics]: ../platform/metrics-charts.md
[start]: ./app-insights-overview.md

