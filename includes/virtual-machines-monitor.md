---
title: Plik dyrektywy include
description: Plik dyrektywy include
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 09/30/2019
ms.author: cynthn
ms.openlocfilehash: feea9696316723a2750be6fc1e13001224320324
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81865626"
---
Ze znacznym wzrostem maszyn wirtualnych hostowanych na platformie Azure ważne jest identyfikowanie problemów z wydajnością i kondycją, które mają wpływ na aplikacje i usługi infrastruktury, które obsługują. Podstawowe monitorowanie jest dostarczane domyślnie z platformą Azure przez typy metryki użycia procesora CPU, wykorzystania dysku, wykorzystania pamięci i ruchu sieciowego zebrane przez hipernadzorca hosta. Dodatkowe dane metryki i dziennika mogą być zbierane przy użyciu [rozszerzeń](../articles/virtual-machines/windows/extensions-features.md) do konfigurowania diagnostyki na maszynach wirtualnych z systemu operacyjnego gościa.

Aby wykryć i pomóc w diagnozowaniu problemów z wydajnością i kondycją w systemie operacyjnym gościa, składnikach aplikacji sieci Web opartych na platformie .NET lub Java działających wewnątrz maszyny wirtualnej, usługa Azure Monitor zapewnia scentralizowane monitorowanie za pomocą kompleksowych funkcji, takich jak usługa Azure Monitor dla maszyn wirtualnych i usługa Application Insights.

## <a name="diagnostics-and-metrics"></a>Diagnostyka i metryki 

Można skonfigurować i monitorować zbieranie [danych diagnostycznych](https://docs.microsoft.com/cli/azure/vm/diagnostics) przy użyciu [metryki](../articles/monitoring-and-diagnostics/monitoring-overview-metrics.md) w witrynie Azure portal, interfejsu wiersza polecenia platformy Azure, platformy Azure PowerShell i programowania interfejsów programowania aplikacji (API). Można na przykład:

- **Obserwuj podstawowe metryki dla maszyny Wirtualnej.** Na ekranie Przegląd witryny Azure portal wyświetlane podstawowe metryki obejmują użycie procesora CPU, użycie sieci, całkowitą liczbę bajtów dysku i operacje dysku na sekundę.

- **Włącz kolekcję diagnostyki rozruchu i wyświetl ją za pomocą witryny Azure portal.** Podczas wprowadzania własnego obrazu na platformę Azure lub nawet uruchamiania jednego z obrazów platformy, może istnieć wiele powodów, dla których maszyna wirtualna przechodzi w stan nie rozruchowy. Diagnostykę rozruchu można łatwo włączyć podczas tworzenia maszyny Wirtualnej, klikając **pozycję Włącz dla** diagnostyki rozruchu w sekcji Monitorowanie na ekranie Ustawienia.

    Podczas rozruchu maszyn wirtualnych agent diagnostyczny rozruchu przechwytuje dane wyjściowe rozruchu i przechowuje je w magazynie platformy Azure. Dane te mogą posłużyć do rozwiązywania problemów związanych z rozruchem maszyny wirtualnej. Diagnostyka rozruchu nie jest automatycznie włączona podczas tworzenia maszyny Wirtualnej z narzędzi wiersza polecenia. Przed włączeniem diagnostyki rozruchu należy utworzyć konto magazynu do przechowywania dzienników rozruchu. Jeśli włączysz diagnostykę rozruchu w witrynie Azure portal, konto magazynu jest automatycznie tworzone dla Ciebie.

    Jeśli diagnostyka rozruchu nie została włączona podczas tworzenia maszyny wirtualnej, zawsze można ją włączyć później przy użyciu [interfejsu wiersza polecenia platformy Azure,](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics) [programu Azure PowerShell](https://docs.microsoft.com/powershell/module/az.compute/set-azvmbootdiagnostic)lub [szablonu usługi Azure Resource Manager.](../articles/virtual-machines/windows/extensions-diagnostics-template.md)

- **Włącz zbieranie danych diagnostycznych systemu operacyjnego gościa.** Podczas tworzenia maszyny Wirtualnej, masz możliwość na ekranie ustawień, aby włączyć diagnostykę systemu operacyjnego gościa. Po włączeniu zbierania danych diagnostycznych [rozszerzenie IaaSDiagnostics dla systemu Linux](../articles/virtual-machines/linux/diagnostic-extension.md) lub rozszerzenie [IaaSDiagnostics dla systemu Windows](../articles/virtual-machines/windows/ps-extensions-diagnostics.md) jest dodawane do maszyny Wirtualnej, co umożliwia zbieranie dodatkowych danych dysku, procesora i pamięci.

    Korzystając z zebranych danych diagnostycznych, można skonfigurować skalowanie automatyczne dla maszyn wirtualnych. Można również skonfigurować [dzienniki usługi Azure Monitor](../articles/azure-monitor/platform/data-platform-logs.md) do przechowywania danych i skonfigurować alerty, aby poinformować, gdy wydajność nie jest odpowiednia.

## <a name="alerts"></a>Alerty

Alerty [alerts](../articles/azure-monitor/platform/alerts-overview.md) można tworzyć na podstawie określonych metryk wydajności. Przykłady problemów, o których można otrzymywać alerty, obejmują, gdy średnie użycie procesora CPU przekracza określony próg lub dostępne wolne miejsce na dysku spada poniżej określonej ilości. Alerty można konfigurować w [witrynie Azure portal](../articles/azure-monitor/platform/alerts-metric.md#create-with-azure-portal), przy użyciu [szablonów usługi Azure Resource Manager](../articles/azure-monitor/platform/alerts-metric-create-templates.md)lub interfejsu [wiersza polecenia platformy Azure.](../articles/azure-monitor/platform/alerts-metric.md#with-azure-cli)

## <a name="azure-service-health"></a>Azure Service Health

[Usługa Azure Service Health](../articles/service-health/service-health-overview.md) zapewnia spersonalizowane wskazówki i pomoc techniczną, gdy problemy w usługach platformy Azure wpływają na Ciebie i pomaga przygotować się do nadchodzącej planowanej konserwacji. Usługa Azure Service Health ostrzega użytkownika i twoje zespoły przy użyciu ukierunkowanych i elastycznych powiadomień.

## <a name="azure-resource-health"></a>Azure Resource Health

[Kondycja zasobów platformy Azure](../articles/service-health/resource-health-overview.md) pomaga diagnozować i utrzymywania pomocy technicznej, gdy problem z platformą Azure wpływa na zasoby. Informuje o bieżącej i wcześniejszej kondycji zasobów oraz pomaga uniknąć problemów. Usługa Resource Health oferuje pomoc techniczną w przypadku problemów z usługą platformy Azure.

## <a name="azure-activity-log"></a>Dziennik aktywności platformy Azure

[Dziennik aktywności platformy Azure](../articles/azure-monitor/platform/platform-logs-overview.md) to dziennik subskrypcji, który zapewnia wgląd w zdarzenia na poziomie subskrypcji, które wystąpiły na platformie Azure. Dziennik zawiera zakres danych, od danych operacyjnych usługi Azure Resource Manager do aktualizacji zdarzeń kondycji usługi. Możesz kliknąć dziennik aktywności w witrynie Azure portal, aby wyświetlić dziennik maszyny Wirtualnej.

Oto niektóre czynności, które można wykonać za pomocą dziennika aktywności:

- Tworzenie [alertu w przypadku zdarzenia Dziennika aktywności](../articles/azure-monitor/platform/platform-logs-overview.md).
- [Przesyłaj strumieniowo go do Centrum zdarzeń](../articles/azure-monitor/platform/activity-logs-stream-event-hubs.md) w celu pozyskiwania przez usługę innej firmy lub niestandardowe rozwiązanie analityczne, takie jak usługa Power BI.
- Analizuj go w usłudze Power BI przy użyciu [pakietu zawartości usługi Power BI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/).
- [Zapisz go na koncie magazynu w celu](../articles/azure-monitor/platform/archive-activity-log.md) kontroli archiwanej lub ręcznej. Można określić czas przechowywania (w dniach) przy użyciu profilu dziennika.

Można również uzyskać dostęp do danych dziennika aktywności przy użyciu [usługi Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.insights/), [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/monitor)lub [monitoruj interfejsy API ODPOCZYNKU](https://docs.microsoft.com/rest/api/monitor/).

[Dzienniki zasobów platformy Azure](../articles/azure-monitor/platform/platform-logs-overview.md) są dzienniki emitowane przez maszynę wirtualną, które zapewniają bogate, częste dane o jego operacji. Dzienniki zasobów różnią się od dziennika aktywności, zapewniając wgląd w operacje, które zostały wykonane w ramach maszyny Wirtualnej.

Oto niektóre czynności, które można wykonać za pomocą dzienników diagnostycznych:

- [Zapisz je na koncie magazynu w celu](../articles/azure-monitor/platform/archive-diagnostic-logs.md) inspekcji lub ręcznej inspekcji. Można określić czas przechowywania (w dniach) przy użyciu ustawień diagnostycznych zasobów.
- [Przesyłaj je strumieniowo do centrów zdarzeń](../articles/azure-monitor/platform/resource-logs-stream-event-hubs.md) w celu pozyskiwania przez usługę innej firmy lub niestandardowe rozwiązanie analityczne, takie jak usługa Power BI.
- Analizuj je za pomocą [usługi Log Analytics](../articles/log-analytics/log-analytics-azure-storage.md).

## <a name="advanced-monitoring"></a>Zaawansowane monitorowanie

Aby uzyskać wgląd w aplikację lub usługę obsługiwaną przez zestawy skalowania maszyny wirtualnej platformy Azure i maszyny wirtualnej, identyfikacja problemów z systemem operacyjnym gościa lub obciążeniem uruchomionym na maszynie wirtualnej, aby zrozumieć, czy ma to wpływ na dostępność lub wydajność aplikacji lub jest to problem z aplikacją, włącz zarówno [usługę Azure Monitor dla maszyn wirtualnych,](../articles/azure-monitor/insights/vminsights-overview.md) jak i usługę Application [Insights.](../articles/azure-monitor/app/app-insights-overview.md)

Usługa Azure Monitor dla maszyn wirtualnych monitoruje maszyny wirtualne platformy Azure na dużą skalę, analizując wydajność i kondycję maszyn wirtualnych z systemem Windows i Linux, w tym różne procesy i wzajemnie powiązane zależności od innych zasobów i procesów zewnętrznych, które odkrywa. Zawiera kilka wykresów skuteczności trendu, aby pomóc podczas badania problemów i oceny pojemności maszyn wirtualnych. Mapa zależności pokazuje monitorowane i niemonitorowane maszyny, nieudane i aktywne połączenia sieciowe między procesami a tymi komputerami oraz pokazuje wykresy trendów ze standardowymi metrykami połączenia sieciowego. W połączeniu ze aplikacją Insights można monitorować aplikację i przechwytywać dane telemetryczne, takie jak żądania HTTP, wyjątki itp., dzięki czemu można skorelować problemy między maszynami wirtualnymi a aplikacją. Skonfiguruj [alerty usługi Azure Monitor,](../articles/azure-monitor/platform/alerts-overview.md) aby ostrzegać o ważnych warunkach wykrytych na podstawie danych monitorowania zebranych przez usługę Azure Monitor dla maszyn wirtualnych.

## <a name="next-steps"></a>Następne kroki

- Przejdź przez kroki opisane w [obszarze Monitoruj maszynę wirtualną systemu Windows za pomocą programu Azure PowerShell](../articles/virtual-machines/windows/tutorial-monitoring.md) lub [monitoruj maszynę wirtualną systemu Linux za pomocą interfejsu wiersza polecenia platformy Azure.](../articles/virtual-machines/linux/tutorial-monitoring.md)

- Dowiedz się więcej o najlepszych praktykach dotyczących [monitorowania i diagnostyki.](https://docs.microsoft.com/azure/architecture/best-practices/monitoring)
