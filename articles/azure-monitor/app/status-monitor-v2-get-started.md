---
title: Agent usługi Azure Application Insights — wprowadzenie | Dokumenty firmy Microsoft
description: Przewodnik szybki start dla agenta usługi Application Insights. Monitoruj działanie witryny bez ponownego rozmieszczania witryny. Współpracuje ze ASP.NET aplikacjami sieci web hostowanymi lokalnie, na maszynach wirtualnych lub na platformie Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 4cfa136585611e81a4060c5544d5dc464b32f12c
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537444"
---
# <a name="get-started-with-azure-monitor-application-insights-agent-for-on-premises-servers"></a>Wprowadzenie do usługi Azure Monitor Application Insights Agent dla serwerów lokalnych

Ten artykuł zawiera polecenia szybkiego startu, które mają działać w większości środowisk.
Instrukcje zależą od Galerii programu PowerShell do dystrybucji aktualizacji.
Te polecenia obsługują parametr `-Proxy` Programu PowerShell.

Aby uzyskać wyjaśnienie tych poleceń, instrukcje dostosowywania i informacje dotyczące rozwiązywania problemów, zobacz [szczegółowe instrukcje](status-monitor-v2-detailed-instructions.md).

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="download-and-install-via-powershell-gallery"></a>Pobieranie i instalowanie za pośrednictwem programu PowerShell Gallery

### <a name="install-prerequisites"></a>Instalacja wymagań wstępnych
Uruchom program PowerShell jako administrator.
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
Install-Module -Name PowerShellGet -Force
``` 
Zamknij program PowerShell.

### <a name="install-application-insights-agent"></a>Instalowanie agenta usługi Application Insights
Uruchom program PowerShell jako administrator.
```powershell   
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-Module -Name Az.ApplicationMonitor -AllowPrerelease -AcceptLicense
``` 

### <a name="enable-monitoring"></a>Włączanie monitorowania
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```
    
        
## <a name="download-and-install-manually-offline-option"></a>Pobieranie i instalowanie ręcznie (opcja offline)
### <a name="download-the-module"></a>Pobierz moduł
Ręcznie pobierz najnowszą wersję modułu z [programu PowerShell Gallery](https://www.powershellgallery.com/packages/Az.ApplicationMonitor).

### <a name="unzip-and-install-application-insights-agent"></a>Rozpaj i zainstaluj agenta usługi Application Insights
```powershell
$pathToNupkg = "C:\Users\t\Desktop\Az.ApplicationMonitor.0.3.0-alpha.nupkg"
$pathToZip = ([io.path]::ChangeExtension($pathToNupkg, "zip"))
$pathToNupkg | rename-item -newname $pathToZip
$pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\Az.ApplicationMonitor"
Expand-Archive -LiteralPath $pathToZip -DestinationPath $pathInstalledModule
```
### <a name="enable-monitoring"></a>Włączanie monitorowania
```powershell
Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```



## <a name="next-steps"></a>Następne kroki

 Wyświetlanie telemetrii:

- [Eksploruj metryki,](../../azure-monitor/platform/metrics-charts.md) aby monitorować wydajność i użycie.
- [Szukaj zdarzeń i dzienników](../../azure-monitor/app/diagnostic-search.md) w celu zdiagnozowania problemów.
- [Korzystaj z analizy](../../azure-monitor/app/analytics.md) w przypadku bardziej zaawansowanych zapytań.
- [Tworzenie pulpitów nawigacyjnych](../../azure-monitor/app/overview-dashboard.md).

 Dodawanie kolejnych funkcji telemetrii:

- [Tworzenie testów sieci Web](monitor-web-app-availability.md), aby upewnić się, że witryna pozostaje aktywna.
- [Dodaj dane telemetryczne klienta sieci Web,](../../azure-monitor/app/javascript.md) aby wyświetlić wyjątki od kodu strony sieci web i włączyć śledzenie wywołań.
- [Dodaj SDK usługi Application Insights do kodu, dzięki](../../azure-monitor/app/asp-net.md) czemu można wstawić śledzenie i rejestrowanie wywołań.

Więcej informacji za pomocą agenta usługi Application Insights:

- Przejrzyj [szczegółowe instrukcje,](status-monitor-v2-detailed-instructions.md) aby uzyskać wyjaśnienie poleceń znalezionych tutaj.
- Skorzystaj z naszego [przewodnika,](status-monitor-v2-troubleshoot.md) aby rozwiązać problem z agentem aplikacji Insights.
