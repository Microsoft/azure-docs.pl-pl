---
title: Tworzenie nowego zasobu Application Insights Azure | Microsoft Docs
description: Ręcznie skonfiguruj monitorowanie Application Insights nowej aplikacji na żywo.
ms.topic: conceptual
ms.date: 02/10/2021
ms.openlocfilehash: 6158b5604046897e20053c67321f26d650c21b7f
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566226"
---
# <a name="create-an-application-insights-resource"></a>Tworzenie zasobu usługi Application Insights

Usługa Azure Application Insights wyświetla dane dotyczące aplikacji w zasobie Microsoft Azure *zasobów.* Tworzenie nowego zasobu jest w związku z tym częścią [konfigurowania Application Insights do monitorowania nowej aplikacji.][start] Po utworzeniu nowego zasobu można pobrać jego klucz instrumentacji i użyć go do skonfigurowania zestawu Application Insights SDK. Klucz instrumentacji łączy telemetrię z zasobem.

> [!IMPORTANT]
> [Wersja Application Insights została wycofana.](https://azure.microsoft.com/updates/we-re-retiring-classic-application-insights-on-29-february-2024/) Postępuj zgodnie z tymi [instrukcjami dotyczącymi uaktualniania do](convert-classic-resource.md)wersji opartej na Application Insights .

## <a name="sign-in-to-microsoft-azure"></a>Zaloguj się do Microsoft Azure

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="create-an-application-insights-resource"></a>Tworzenie zasobu usługi Application Insights

Zaloguj się do [Azure Portal](https://portal.azure.com)i utwórz zasób Application Insights zasobów:

![Kliknij znak "+" w lewym górnym rogu. Wybierz Narzędzia deweloperskie a następnie pozycję Application Insights](./media/create-new-resource/new-app-insights.png)

   | Ustawienia        |  Wartość           | Opis  |
   | ------------- |:-------------|:-----|
   | **Nazwa**      | `Unique value` | Nazwa identyfikująca monitorowaną aplikację. |
   | **Grupa zasobów**     | `myResourceGroup`      | Nazwa nowej lub istniejącej grupy zasobów do hostowania danych usługi App Insights. |
   | **Region** | `East US` | Wybierz lokalizację w pobliżu lub w pobliżu miejsca hostowanej aplikacji. |
   | **Tryb zasobu** | `Classic` lub `Workspace-based` | Zasoby oparte na obszarze roboczym umożliwiają wysyłanie danych telemetrycznych Application Insights do wspólnego obszaru roboczego usługi Log Analytics. Aby uzyskać więcej informacji, zobacz [artykuł na temat zasobów opartych na obszarze roboczym.](create-workspace-resource.md)

> [!NOTE]
> Chociaż można użyć tej samej nazwy zasobu w różnych grupach zasobów, korzystne może być użycie globalnie unikatowej nazwy. Może to być przydatne, jeśli planujesz [wykonywać zapytania między zasobami,](../logs/cross-workspace-query.md#identifying-an-application) ponieważ upraszcza to wymaganą składnię.

Wprowadź odpowiednie wartości w wymaganych polach, a następnie wybierz **pozycję Przejrzyj i utwórz**.

> [!div class="mx-imgBorder"]
> ![Wprowadź wartości w wymaganych polach, a następnie wybierz pozycję "Przejrzyj i utwórz".](./media/create-new-resource/review-create.png)

Po utworzeniu aplikacji zostanie otwarte nowe okienko. W tym okienku zobaczysz dane dotyczące wydajności i użycia monitorowanej aplikacji. 

## <a name="copy-the-instrumentation-key"></a>Kopiowanie klucza instrumentacji

Klucz instrumentacji identyfikuje zasób, z którym chcesz skojarzyć dane telemetryczne. Musisz skopiować klucz instrumentacji i dodać go do kodu aplikacji.

> [!IMPORTANT]
> Nowe regiony platformy Azure **wymagają** użycia ciągów połączenia zamiast kluczy instrumentacji. [Parametrów połączenia](./sdk-connection-string.md?tabs=net) identyfikuje zasób, z którym chcesz skojarzyć dane telemetryczne. Umożliwia również modyfikowanie punktów końcowych, które będą przez zasób jako miejsce docelowe telemetrii. Konieczne będzie skopiowanie parametrów połączenia i dodanie ich do kodu aplikacji lub do zmiennej środowiskowej.

## <a name="install-the-sdk-in-your-app"></a>Instalowanie zestawu SDK w aplikacji

Zainstaluj Application Insights SDK w swojej aplikacji. Ten krok w dużym stopniu zależy od typu aplikacji.

Użyj klucza instrumentacji, aby skonfigurować [zestaw SDK instalowany w aplikacji.][start]

Zestaw SDK zawiera moduły standardowe, które wysyłają dane telemetryczne bez konieczności pisania dodatkowego kodu. Aby śledzić akcje użytkownika lub diagnozować problemy bardziej szczegółowo, użyj [interfejsu API][api] do wysyłania własnych danych telemetrycznych.

## <a name="creating-a-resource-automatically"></a>Automatyczne tworzenie zasobu

### <a name="powershell"></a>PowerShell

Tworzenie nowego zasobu Application Insights zasobów

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

Aby uzyskać pełną dokumentację programu PowerShell dotyczącą tego polecenia cmdlet i dowiedzieć się, jak pobrać klucz instrumentacji, zapoznaj się z [Azure PowerShell dokumentacją.](/powershell/module/az.applicationinsights/new-azapplicationinsights)

### <a name="azure-cli-preview"></a>Interfejs wiersza polecenia platformy Azure (wersja zapoznawcza)

Aby uzyskać dostęp do wersji Application Insights poleceniach interfejsu wiersza polecenia platformy Azure, należy najpierw uruchomić polecenie:

```azurecli
 az extension add -n application-insights
```

Jeśli nie uruchamiasz polecenia , zostanie wyświetlony komunikat o błędzie `az extension add` z komunikatem: `az : ERROR: az monitor: 'app-insights' is not in the 'az monitor' command group. See 'az monitor --help'.`

Teraz możesz uruchomić następujące czynności, aby utworzyć zasób Application Insights zasobów:

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

Aby uzyskać pełną dokumentację interfejsu wiersza polecenia platformy Azure dla tego polecenia i dowiedzieć się, jak pobrać klucz instrumentacji, zapoznaj się z dokumentacją interfejsu wiersza [polecenia platformy Azure.](/cli/azure/ext/application-insights/monitor/app-insights/component#ext-application-insights-az-monitor-app-insights-component-create)

## <a name="next-steps"></a>Następne kroki
* [Wyszukiwanie diagnostyczne](./diagnostic-search.md)
* [Eksploruj metryki](../essentials/metrics-charts.md)
* [Pisanie zapytań analitycznych](../logs/log-query-overview.md)

<!--Link references-->

[api]: ./api-custom-events-metrics.md
[diagnostic]: ./diagnostic-search.md
[metrics]: ../essentials/metrics-charts.md
[start]: ./app-insights-overview.md

