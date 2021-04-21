---
title: Samouczek — monitorowanie maszyny hybrydowej za pomocą Azure Monitor szczegółowych informacji o maszynie wirtualnej
description: Dowiedz się, jak zbierać i analizować dane z maszyny hybrydowej w Azure Monitor.
ms.topic: tutorial
ms.date: 04/21/2021
ms.openlocfilehash: f59ad448440110e2c5e6dd1fa1b2858d9cf42e91
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834268"
---
# <a name="tutorial-monitor-a-hybrid-machine-with-vm-insights"></a>Samouczek: monitorowanie maszyny hybrydowej za pomocą szczegółowych informacji o maszynie wirtualnej

[Azure Monitor](../../../azure-monitor/overview.md) zbierać dane bezpośrednio z maszyn hybrydowych do obszaru roboczego usługi Log Analytics w celu szczegółowej analizy i korelacji. Zazwyczaj wiąże się to z zainstalowaniem agenta [usługi Log Analytics](../../../azure-monitor/agents/agents-overview.md#log-analytics-agent) na maszynie przy użyciu skryptu, ręcznie lub automatycznie zgodnie ze standardami zarządzania konfiguracją. Serwery z obsługą usługi Arc niedawno wprowadzono [](../manage-vm-extensions.md) obsługę instalowania rozszerzeń maszyn wirtualnych usługi Log Analytics i agenta zależności dla systemów Windows i Linux, umożliwiając usłudze [VM Insights](../../../azure-monitor/vm/vminsights-overview.md) zbieranie danych z maszyn wirtualnych spoza platformy Azure.

W tym samouczku pokazano, jak skonfigurować i zbierać dane z maszyn z systemem Linux lub Windows, włączając szczegółowe informacje o maszynach wirtualnych, korzystając z uproszczonego zestawu kroków, co usprawnia środowisko pracy i zajmuje krótszy czas.  

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Funkcja rozszerzenia maszyny wirtualnej jest dostępna tylko na liście [obsługiwanych regionów.](../overview.md#supported-regions)

* Zobacz [Obsługiwane systemy operacyjne,](../../../azure-monitor/vm/vminsights-enable-overview.md#supported-operating-systems) aby upewnić się, że włączanie systemu operacyjnego serwerów jest obsługiwane przez szczegółowe informacje o maszynach wirtualnych.

* Zapoznaj się z wymaganiami zapory dla agenta usługi Log Analytics dostępnymi w [przeglądzie agenta usługi Log Analytics.](../../../azure-monitor/agents/log-analytics-agent.md#network-requirements) Agent zależności mapowania szczegółowych informacji o maszynie wirtualnej nie przesyła żadnych danych i nie wymaga żadnych zmian w zaporach ani portach.

## <a name="sign-in-to-azure-portal"></a>Logowanie do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

## <a name="enable-vm-insights"></a>Włączanie szczegółowych informacji o maszynach wirtualnych

1. Uruchom usługę Azure Arc w chmurze, Azure Portal pozycję **Wszystkie** usługi, a następnie wyszukując i wybierając pozycję Maszyny **— Azure Arc**.

    :::image type="content" source="./media/quick-enable-hybrid-vm/search-machines.png" alt-text="Wyszukiwanie serwerów z obsługą usługi Arc we wszystkich usługach" border="false":::

1. Na stronie **Maszyny — Azure Arc** wybierz połączną maszynę utworzoną w artykule [Szybki](quick-enable-hybrid-vm.md) start.

1. W lewym okienku w sekcji **Monitorowanie** wybierz pozycję **Szczegółowe informacje,** a następnie pozycję **Włącz.**

    :::image type="content" source="./media/tutorial-enable-vm-insights/insights-option.png" alt-text="Wybieranie opcji Szczegółowe informacje z menu po lewej stronie" border="false":::

1. Na stronie Azure Monitor **Insights Onboarding** (Dołączanie usługi Insights) zostanie wyświetlony monit o utworzenie obszaru roboczego. W tym samouczku nie zalecamy wybierania istniejącego obszaru roboczego usługi Log Analytics, jeśli już go masz. Wybierz wartość domyślną, czyli obszar roboczy o unikatowej nazwie w tym samym regionie, w którym znajduje się zarejestrowana połączona maszyna. Ten obszar roboczy zostanie utworzony i skonfigurowany automatycznie.

    :::image type="content" source="./media/tutorial-enable-vm-insights/enable-vm-insights.png" alt-text="Strona włączania szczegółowych informacji o maszynie wirtualnej" border="false":::

1. Podczas konfigurowania są wyświetlane komunikaty o stanie. Ten proces trwa kilka minut, ponieważ rozszerzenia są instalowane na połączonej maszynie.

    :::image type="content" source="./media/tutorial-enable-vm-insights/onboard-vminsights-vm-portal-status.png" alt-text="Komunikat o stanie włączania wglądu w szczegółowe dane maszyny wirtualnej" border="false":::

    Po zakończeniu zostanie wyświetlony komunikat informujący o tym, że maszyna została pomyślnie do dostania i szczegółowe informacje zostały pomyślnie wdrożone.

## <a name="view-data-collected"></a>Wyświetlanie zebranych danych

Po zakończeniu wdrażania i konfiguracji wybierz pozycję **Szczegółowe informacje,** a następnie wybierz **kartę** Wydajność. Na karcie Wydajność znajduje się grupa wybranych liczników wydajności zebranych z systemu operacyjnego gościa maszyny. Przewiń w dół, aby wyświetlić więcej liczników, i przesuń wskaźnik myszy na graf, aby wyświetlić średnie i percentyły wykonane od czasu zainstalowania rozszerzenia maszyny wirtualnej usługi Log Analytics na maszynie.

:::image type="content" source="./media/tutorial-enable-vm-insights/insights-performance-charts.png" alt-text="Wykresy wydajności szczegółowych informacji o maszynie wirtualnej dla wybranej maszyny" border="false":::

Wybierz **pozycję Mapuj,** aby otworzyć funkcję mapy, która pokazuje procesy uruchomione na maszynie i ich zależności. Wybierz **pozycję** Właściwości, aby otworzyć okienko właściwości, jeśli nie zostało jeszcze otwarte.

:::image type="content" source="./media/tutorial-enable-vm-insights/insights-map.png" alt-text="Mapa szczegółowych informacji o maszynie wirtualnej dla wybranej maszyny" border="false":::

Rozwiń procesy dla swojej maszyny. Wybierz jeden z procesów, aby wyświetlić jego szczegóły i wyróżnić jego zależności.

Wybierz ponownie maszynę, a następnie wybierz pozycję **Zdarzenia dziennika.** Zostanie wyświetlona lista tabel przechowywanych w obszarze roboczym usługi Log Analytics dla maszyny. Ta lista będzie się różnić w zależności od tego, czy używasz maszyny z systemem Windows, czy Linux. Wybierz **tabelę** Event (Zdarzenia). Tabela **Event** zawiera wszystkie zdarzenia z dziennika zdarzeń systemu Windows. Zostanie otwarta usługa Log Analytics z prostym zapytaniem w celu pobrania zebranych wpisów dziennika zdarzeń.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o Azure Monitor, zapoznaj się z następującym artykułem:

> [!div class="nextstepaction"]
> [Omówienie usługi Azure Monitor](../../../azure-monitor/overview.md)