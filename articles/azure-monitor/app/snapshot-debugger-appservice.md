---
title: Włącz Snapshot Debugger dla aplikacji .NET w Azure App Service | Microsoft Docs
description: Włącz Snapshot Debugger dla aplikacji .NET w programie Azure App Service
ms.topic: conceptual
author: brahmnes
ms.author: bfung
ms.date: 03/26/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 35653840c5ddd6f5ae2d5dc078513f0fa35ab34a
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97560937"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-app-service"></a>Włącz Snapshot Debugger dla aplikacji .NET w programie Azure App Service

Snapshot Debugger obecnie działa dla aplikacji ASP.NET i ASP.NET Core, które działają w Azure App Service planach usług systemu Windows. Zalecamy uruchomienie aplikacji w warstwie Podstawowa usługi lub wyższej w przypadku korzystania z debugera migawek. W przypadku większości aplikacji warstwy Bezpłatna i współdzielona nie mają wystarczającej ilości pamięci do zapisania migawek.

## <a name="enable-snapshot-debugger"></a><a id="installation"></a> Włącz Snapshot Debugger
Aby włączyć Snapshot Debugger dla aplikacji, postępuj zgodnie z poniższymi instrukcjami.

Jeśli używasz innego typu usługi platformy Azure, poniżej przedstawiono instrukcje dotyczące włączania Snapshot Debugger na innych obsługiwanych platformach:
* [usług Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Usługi Service Fabric platformy Azure](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines i zestawy skalowania maszyn wirtualnych](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Lokalne maszyny wirtualne lub fizyczne](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

> [!NOTE]
> Jeśli używasz wersji zapoznawczej programu .NET Core lub aplikacja odwołuje się do Application Insights SDK bezpośrednio lub pośrednio za pośrednictwem zestawu zależnego, postępuj zgodnie z instrukcjami dotyczącymi [włączania Snapshot debugger dla innych środowisk](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) , aby dołączyć pakiet NuGet [Microsoft. ApplicationInsights. SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) do aplikacji, a następnie wykonaj pozostałe instrukcje poniżej. 

Snapshot Debugger jest wstępnie zainstalowana jako część środowiska uruchomieniowego App Services, ale musisz ją włączyć, aby uzyskać migawki dla aplikacji App Service.

Po wdrożeniu aplikacji postępuj zgodnie z poniższymi instrukcjami, aby włączyć debuger migawek:

1. Przejdź do panelu sterowania platformy Azure, aby uzyskać App Service.
2. Przejdź do strony **ustawienia > Application Insights** .

   ![Włączanie usługi App Insights w portalu App Services](./media/snapshot-debugger/applicationinsights-appservices.png)

3. Postępuj zgodnie z instrukcjami na stronie, aby utworzyć nowy zasób, lub wybierz istniejący zasób usługi App Insights, aby monitorować aplikację. Upewnij się również, że oba przełączniki Snapshot Debugger są **włączone**.

   ![Dodaj rozszerzenie witryny usługi App Insights][Enablement UI]

4. Snapshot Debugger jest teraz włączona przy użyciu ustawienia aplikacji App Services.

    ![Ustawienie aplikacji dla Snapshot Debugger][snapshot-debugger-app-setting]

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

