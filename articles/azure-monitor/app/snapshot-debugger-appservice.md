---
title: Włącz Snapshot Debugger dla aplikacji .NET w Azure App Service | Microsoft Docs
description: Włącz Snapshot Debugger dla aplikacji .NET w programie Azure App Service
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 03/26/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 26538f48213d025c6fe71fb55abb17a025a23b45
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105025683"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-app-service"></a>Włącz Snapshot Debugger dla aplikacji .NET w programie Azure App Service

Snapshot Debugger obecnie obsługuje aplikacje ASP.NET i ASP.NET Core, które działają w Azure App Service planach usług systemu Windows.

W przypadku korzystania z debugera migawek zalecamy uruchomienie aplikacji w warstwie Podstawowa usługi lub wyższej.

W przypadku większości aplikacji warstwy Bezpłatna i współdzielona nie mają wystarczającej ilości pamięci lub miejsca na dysku do zapisania migawek.

## <a name="enable-snapshot-debugger"></a><a id="installation"></a> Włącz Snapshot Debugger
Aby włączyć Snapshot Debugger dla aplikacji, postępuj zgodnie z poniższymi instrukcjami.

Jeśli używasz innego typu usługi platformy Azure, poniżej przedstawiono instrukcje dotyczące włączania Snapshot Debugger na innych obsługiwanych platformach:
* [Funkcja platformy Azure](snapshot-debugger-function-app.md?toc=/azure/azure-monitor/toc.json)
* [usług Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Usługi Service Fabric platformy Azure](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines i zestawy skalowania maszyn wirtualnych](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Lokalne maszyny wirtualne lub fizyczne](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

> [!NOTE]
> Jeśli używasz wersji zapoznawczej programu .NET Core lub aplikacja odwołuje się do zestawu SDK Application Insights, bezpośrednio lub pośrednio za pośrednictwem zestawu zależnego, postępuj zgodnie z instrukcjami dotyczącymi [włączania Snapshot debugger dla innych środowisk](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) , aby uwzględnić pakiet NuGet [Microsoft. ApplicationInsights. SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) z aplikacją, a następnie wykonaj pozostałe instrukcje poniżej. 
>
> Instalacja bezkodowa Snapshot Debugger Application Insights jest zgodna z zasadami obsługi .NET Core.
> Aby uzyskać więcej informacji na temat obsługiwanych środowisk uruchomieniowych, zobacz temat [zasady obsługi platformy .NET Core](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).

Snapshot Debugger jest wstępnie zainstalowana jako część środowiska uruchomieniowego App Services, ale musisz ją włączyć, aby uzyskać migawki dla aplikacji App Service.

Po wdrożeniu aplikacji postępuj zgodnie z poniższymi instrukcjami, aby włączyć debuger migawek:

1. Przejdź do panelu sterowania platformy Azure, aby uzyskać App Service.
2. Przejdź do strony **ustawienia > Application Insights** .

   ![Włączanie usługi App Insights w portalu App Services](./media/snapshot-debugger/applicationinsights-appservices.png)

3. Postępuj zgodnie z instrukcjami na stronie, aby utworzyć nowy zasób, lub wybierz istniejący zasób usługi App Insights, aby monitorować aplikację. Upewnij się również, że oba przełączniki Snapshot Debugger są **włączone**.

   ![Dodaj rozszerzenie witryny usługi App Insights][Enablement UI]

4. Snapshot Debugger jest teraz włączona przy użyciu ustawienia aplikacji App Services.

    ![Ustawienie aplikacji dla Snapshot Debugger][snapshot-debugger-app-setting]

## <a name="enable-snapshot-debugger-for-other-clouds"></a>Włącz Snapshot Debugger dla innych chmur

Obecnie jedynymi regionami, które wymagają modyfikacji punktów końcowych, są [Azure Government](../../azure-government/compare-azure-government-global-azure.md#application-insights) i [Chiny platformy Azure](/azure/china/resources-developer-guide) za pomocą parametrów połączenia Application Insights.

|Właściwość parametrów połączenia    | Chmura dla instytucji rządowych USA | Chmura Chińska |   
|---------------|---------------------|-------------|
|SnapshotEndpoint         | `https://snapshot.monitor.azure.us`    | `https://snapshot.monitor.azure.cn` |

Aby uzyskać więcej informacji na temat innych zastąpień połączeń, zobacz [dokumentację Application Insights](./sdk-connection-string.md?tabs=net#connection-string-with-explicit-endpoint-overrides).

## <a name="disable-snapshot-debugger"></a>Wyłącz Snapshot Debugger

Wykonaj te same czynności co w przypadku **Snapshot Debugger Włącz**, ale Przełącz oba przełączniki Snapshot Debugger na **wyłączone**.

Zalecamy, aby na wszystkich aplikacjach została włączona Snapshot Debugger, co ułatwi diagnostykę wyjątków aplikacji.

## <a name="azure-resource-manager-template"></a>Szablon usługi Azure Resource Manager

Aby uzyskać Azure App Service, można skonfigurować ustawienia aplikacji w ramach szablonu Azure Resource Manager, aby włączyć Snapshot Debugger i profilera, zobacz następujący fragment kodu szablonu:

```json
{
  "apiVersion": "2015-08-01",
  "name": "[parameters('webSiteName')]",
  "type": "Microsoft.Web/sites",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[variables('hostingPlanName')]"
  ],
  "tags": { 
    "[concat('hidden-related:', resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName')))]": "empty",
    "displayName": "Website"
  },
  "properties": {
    "name": "[parameters('webSiteName')]",
    "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"
  },
  "resources": [
    {
      "apiVersion": "2015-08-01",
      "name": "appsettings",
      "type": "config",
      "dependsOn": [
        "[parameters('webSiteName')]",
        "[concat('AppInsights', parameters('webSiteName'))]"
      ],
      "properties": {
        "APPINSIGHTS_INSTRUMENTATIONKEY": "[reference(resourceId('Microsoft.Insights/components', concat('AppInsights', parameters('webSiteName'))), '2014-04-01').InstrumentationKey]",
        "APPINSIGHTS_PROFILERFEATURE_VERSION": "1.0.0",
        "APPINSIGHTS_SNAPSHOTFEATURE_VERSION": "1.0.0",
        "DiagnosticServices_EXTENSION_VERSION": "~3",
        "ApplicationInsightsAgent_EXTENSION_VERSION": "~2"
      }
    }
  ]
},
```

## <a name="next-steps"></a>Następne kroki

- Generuj ruch do aplikacji, która może wyzwolić wyjątek. Następnie odczekaj od 10 do 15 minut na wysłanie migawek do wystąpienia Application Insights.
- Zobacz [migawki](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal) w Azure Portal.
- Aby uzyskać pomoc dotyczącą rozwiązywania problemów Snapshot Debugger, zobacz [Snapshot Debugger Rozwiązywanie problemów](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json).

[Enablement UI]: ./media/snapshot-debugger/enablement-ui.png
[snapshot-debugger-app-setting]:./media/snapshot-debugger/snapshot-debugger-app-setting.png