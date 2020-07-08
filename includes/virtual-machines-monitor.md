---
title: dołączanie pliku
description: dołączanie pliku
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 09/30/2019
ms.author: cynthn
ms.openlocfilehash: feea9696316723a2750be6fc1e13001224320324
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "81865626"
---
Mając znaczny wzrost liczby maszyn wirtualnych hostowanych na platformie Azure, ważne jest, aby zidentyfikować problemy dotyczące wydajności i kondycji, które mają wpływ na aplikacje i obsługiwane przez nich usługi infrastruktury. Podstawowe monitorowanie jest domyślnie dostarczane z platformą Azure przez typy metryk użycie procesora, wykorzystanie dysku, wykorzystanie pamięci i ruch sieciowy zebrany przez funkcję hypervisor hosta. Dodatkowe dane dotyczące metryk i dzienników można zbierać przy użyciu [rozszerzeń](../articles/virtual-machines/windows/extensions-features.md) do konfigurowania diagnostyki na maszynach wirtualnych z systemu operacyjnego gościa.

Aby wykrywać i pomagać w diagnozowaniu problemów z wydajnością i kondycją w systemie operacyjnym gościa, składnikach aplikacji sieci Web opartych na platformie .NET lub w języku Java uruchomionym w ramach maszyny wirtualnej, Azure Monitor oferuje scentralizowane monitorowanie z kompleksowymi funkcjami, takimi jak Azure Monitor dla maszyn wirtualnych Application Insights i

## <a name="diagnostics-and-metrics"></a>Diagnostyka i metryki 

Można skonfigurować i monitorować zbieranie [danych diagnostycznych](https://docs.microsoft.com/cli/azure/vm/diagnostics) przy użyciu [metryk](../articles/monitoring-and-diagnostics/monitoring-overview-metrics.md) w Azure Portal, interfejsu wiersza polecenia platformy Azure, Azure PowerShell i programowania aplikacji programistycznych (API). Możesz na przykład:

- **Obserwuj Podstawowe metryki dla maszyny wirtualnej.** Na ekranie przeglądu Azure Portal są wyświetlane podstawowe metryki obejmują użycie procesora, użycie sieci, całkowitą liczbę bajtów dysku i operacje na dyskach na sekundę.

- **Włącz zbieranie danych diagnostycznych rozruchu i wyświetlanie ich przy użyciu Azure Portal.** W przypadku przenoszenia własnego obrazu na platformę Azure lub nawet uruchamiania jednego z obrazów platformy może istnieć wiele przyczyn, dla których maszyna wirtualna przechodzi w stan rozruchowy. Możesz łatwo włączyć diagnostykę rozruchu podczas tworzenia maszyny wirtualnej, klikając pozycję **włączone** dla diagnostyki rozruchu w sekcji Monitorowanie ekranu ustawienia.

    W przypadku rozruchu maszyn wirtualnych agent diagnostyki rozruchu przechwytuje dane wyjściowe rozruchu i przechowuje je w usłudze Azure Storage. Dane te mogą posłużyć do rozwiązywania problemów związanych z rozruchem maszyny wirtualnej. Diagnostyka rozruchu nie jest włączana automatycznie podczas tworzenia maszyny wirtualnej przy użyciu narzędzi wiersza polecenia. Przed włączeniem diagnostyki rozruchu należy utworzyć konto magazynu do przechowywania dzienników rozruchu. Po włączeniu diagnostyki rozruchu w Azure Portal konto magazynu zostanie automatycznie utworzone.

    Jeśli nie włączono diagnostyki rozruchu podczas tworzenia maszyny wirtualnej, zawsze można ją włączyć później przy użyciu [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics), [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.compute/set-azvmbootdiagnostic)lub [szablonu Azure Resource Manager](../articles/virtual-machines/windows/extensions-diagnostics-template.md).

- **Włącz zbieranie danych diagnostycznych systemu operacyjnego gościa.** Podczas tworzenia maszyny wirtualnej możesz korzystać z ekranu ustawienia, aby włączyć diagnostykę systemu operacyjnego gościa. Po włączeniu zbierania danych diagnostycznych [rozszerzenie IaaSDiagnostics dla systemu Linux](../articles/virtual-machines/linux/diagnostic-extension.md) lub [rozszerzenie IaaSDiagnostics dla Windows](../articles/virtual-machines/windows/ps-extensions-diagnostics.md) jest dodawane do maszyny wirtualnej, co umożliwia zbieranie dodatkowych danych dotyczących dysku, procesora i pamięci.

    Korzystając z zebranych danych diagnostycznych, można skonfigurować Skalowanie automatyczne dla maszyn wirtualnych. Możesz również skonfigurować [dzienniki Azure monitor](../articles/azure-monitor/platform/data-platform-logs.md) do przechowywania danych i skonfigurować alerty, aby poinformować o tym, gdy wydajność nie jest odpowiednia.

## <a name="alerts"></a>Alerty

Możesz tworzyć [alerty](../articles/azure-monitor/platform/alerts-overview.md) na podstawie określonych metryk wydajności. Przykłady problemów, dla których można otrzymywać alerty w przypadku, gdy średnie użycie procesora CPU przekracza określony próg lub ilość wolnego miejsca na dysku spadnie poniżej określonej ilości. Alerty można skonfigurować w [Azure Portal](../articles/azure-monitor/platform/alerts-metric.md#create-with-azure-portal)przy użyciu [szablonów Azure Resource Manager](../articles/azure-monitor/platform/alerts-metric-create-templates.md)lub [interfejsu wiersza polecenia platformy Azure](../articles/azure-monitor/platform/alerts-metric.md#with-azure-cli).

## <a name="azure-service-health"></a>Azure Service Health

[Azure Service Health](../articles/service-health/service-health-overview.md) udostępnia spersonalizowane wskazówki i pomoc techniczną w przypadku problemów z usługami platformy Azure i ułatwia przygotowanie się do nadchodzącej planowanej konserwacji. Azure Service Health alerty użytkownika i Twoich zespołów przy użyciu funkcji i elastycznych powiadomień.

## <a name="azure-resource-health"></a>Azure Resource Health

[Usługa Azure Resource Health](../articles/service-health/resource-health-overview.md) pomaga diagnozować i uzyskiwać pomoc techniczną, gdy problem z platformą Azure ma wpływ na zasoby. Informuje o bieżącej i wcześniejszej kondycji zasobów oraz pomaga uniknąć problemów. Usługa Resource Health oferuje pomoc techniczną w przypadku problemów z usługą platformy Azure.

## <a name="azure-activity-log"></a>Dziennik aktywności platformy Azure

[Dziennik aktywności platformy Azure](../articles/azure-monitor/platform/platform-logs-overview.md) to dziennik subskrypcji, który zapewnia wgląd w zdarzenia na poziomie subskrypcji, które wystąpiły na platformie Azure. Dziennik zawiera szereg danych, z Azure Resource Manager dane operacyjne do aktualizacji Service Health zdarzeń. Możesz kliknąć pozycję Dziennik aktywności w Azure Portal, aby wyświetlić dziennik dla maszyny wirtualnej.

W przypadku dziennika aktywności można wykonać następujące czynności:

- Utwórz alert dotyczący [zdarzenia dziennika aktywności](../articles/azure-monitor/platform/platform-logs-overview.md).
- [Przesyłaj strumieniowo do centrum zdarzeń](../articles/azure-monitor/platform/activity-logs-stream-event-hubs.md) w celu pozyskiwania przez usługę innej firmy lub rozwiązanie do analizy niestandardowej, takie jak Power BI.
- Analizuj ją w Power BI przy użyciu [pakietu zawartości Power BI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/).
- [Zapisz go na koncie magazynu](../articles/azure-monitor/platform/archive-activity-log.md) , aby przeprowadzić archiwizację lub inspekcję ręczną. Możesz określić czas przechowywania (w dniach) przy użyciu profilu dziennika.

Możesz również uzyskać dostęp do danych dziennika aktywności przy użyciu [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.insights/), [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/monitor)lub [monitorować interfejsy API REST](https://docs.microsoft.com/rest/api/monitor/).

[Dzienniki zasobów platformy Azure](../articles/azure-monitor/platform/platform-logs-overview.md) to dzienniki emitowane przez MASZYNę wirtualną, które zapewniają rozbudowane, częste dane dotyczące operacji. Dzienniki zasobów różnią się od dziennika aktywności, zapewniając wgląd w operacje wykonywane w ramach maszyny wirtualnej.

Poniżej przedstawiono niektóre czynności, które można wykonać za pomocą dzienników diagnostycznych:

- [Zapisz je na koncie magazynu](../articles/azure-monitor/platform/archive-diagnostic-logs.md) , aby przeprowadzić inspekcję lub inspekcję ręczną. Możesz określić czas przechowywania (w dniach) przy użyciu ustawień diagnostycznych zasobu.
- [Przesyłaj strumieniowo do Event Hubs](../articles/azure-monitor/platform/resource-logs-stream-event-hubs.md) na potrzeby pozyskiwania przez usługę innej firmy lub rozwiązanie do analizy niestandardowej, takie jak Power BI.
- Analizuj je za pomocą [log Analytics](../articles/log-analytics/log-analytics-azure-storage.md).

## <a name="advanced-monitoring"></a>Zaawansowane monitorowanie

Aby uzyskać wgląd w aplikacje lub usługę obsługiwaną przez MASZYNę wirtualną platformy Azure i zestawy skalowania maszyn wirtualnych, można zidentyfikować problemy z systemem operacyjnym gościa lub obciążeniem działającym na maszynie wirtualnej, aby zrozumieć, czy ma to wpływ na dostępność lub wydajność aplikacji, czy też problem z aplikacją, Włącz zarówno [Azure monitor dla maszyn wirtualnych](../articles/azure-monitor/insights/vminsights-overview.md) , jak i [Application Insights](../articles/azure-monitor/app/app-insights-overview.md).

Azure Monitor dla maszyn wirtualnych monitoruje maszyny wirtualne platformy Azure na dużą skalę, analizując wydajność i kondycję maszyn wirtualnych z systemami Windows i Linux, w tym różne procesy i połączone zależności od innych zasobów i procesów zewnętrznych, które wykrywa. Zawiera kilka wykresów wydajności trendów, które ułatwiają badanie problemów i ocenianie pojemności maszyn wirtualnych. Mapa zależności zawiera monitorowane i niemonitorowane maszyny, Niepowodzenie i aktywne połączenia sieciowe między procesami i tymi maszynami oraz pokazuje wykresy trendów ze standardowymi metrykami połączenia sieciowego. W połączeniu z Application Insights można monitorować aplikację i przechwytywać dane telemetryczne, takie jak żądania HTTP, wyjątki itp., aby można było skorelować problemy między maszynami wirtualnymi i aplikacją. Skonfiguruj [alerty Azure monitor](../articles/azure-monitor/platform/alerts-overview.md) , aby otrzymywać alerty dotyczące ważnych warunków wykrytych w ramach monitorowania danych zebranych przez Azure monitor dla maszyn wirtualnych.

## <a name="next-steps"></a>Następne kroki

- Wykonaj kroki opisane w sekcji [monitorowanie maszyny wirtualnej z systemem Windows przy użyciu Azure PowerShell](../articles/virtual-machines/windows/tutorial-monitoring.md) lub [monitorowanie maszyny wirtualnej z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure](../articles/virtual-machines/linux/tutorial-monitoring.md).

- Dowiedz się więcej o najlepszych rozwiązaniach dotyczących [monitorowania i diagnostyki](https://docs.microsoft.com/azure/architecture/best-practices/monitoring).
