---
title: Samouczek — monitorowanie maszyny hybrydowej za pomocą Azure Monitor dla maszyn wirtualnych
description: Dowiedz się, jak zbierać i analizować dane z maszyny hybrydowej w Azure Monitor.
ms.topic: tutorial
ms.date: 09/23/2020
ms.openlocfilehash: 97ab390570f434295a5aa836ef994640f6dc14f8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91335419"
---
# <a name="tutorial-monitor-a-hybrid-machine-with-azure-monitor-for-vms"></a>Samouczek: monitorowanie maszyny hybrydowej za pomocą Azure Monitor dla maszyn wirtualnych

[Azure monitor](../overview.md) może zbierać dane bezpośrednio z hybrydowych maszyn wirtualnych do log Analytics obszaru roboczego w celu uzyskania szczegółowej analizy i korelacji. Zazwyczaj pociąga to za sobą zainstalowanie [agenta log Analytics](../../../azure-monitor/platform/agents-overview.md#log-analytics-agent) na maszynie przy użyciu skryptu, ręcznie lub zautomatyzowanej metody zgodnie ze standardami zarządzania konfiguracją. Serwery z włączonymi łukiemmi zostały ostatnio wprowadzone, aby zainstalować Log Analytics i [rozszerzenia maszyny wirtualnej](../manage-vm-extensions.md) agenta zależności dla systemów Windows i Linux, umożliwiając Azure monitor zbieranie danych z maszyn wirtualnych spoza platformy Azure.

W tym samouczku pokazano, jak skonfigurować i zbierać dane z maszyn wirtualnych z systemem Linux lub Windows przez włączenie Azure Monitor dla maszyn wirtualnych po uproszczonym zestawie kroków, co usprawni pracę i trwa krótszy czas.  

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Funkcjonalność rozszerzenia maszyny wirtualnej jest dostępna tylko na liście [obsługiwanych regionów](../overview.md#supported-regions).

* Zobacz [obsługiwane systemy operacyjne](../../../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) , aby upewnić się, że system operacyjny maszyn wirtualnych, który jest włączany, jest obsługiwany przez Azure monitor dla maszyn wirtualnych.

* Zapoznaj się z wymaganiami dotyczącymi zapory dla agenta Log Analytics dostępnego w [omówieniu log Analytics Agent](../../../azure-monitor/platform/log-analytics-agent.md#network-requirements). Agent Azure Monitor dla maszyn wirtualnych zależności mapy nie przesyła samych danych i nie wymaga żadnych zmian w zaporach ani portach.

## <a name="sign-in-to-azure-portal"></a>Logowanie do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

## <a name="enable-azure-monitor-for-vms"></a>Włącz Azure Monitor dla maszyn wirtualnych

1. Uruchom usługę Azure Arc w Azure Portal, klikając pozycję **wszystkie usługi**, a następnie wyszukując i wybierając pozycję **maszyny — Azure Arc**.

    :::image type="content" source="./media/quick-enable-hybrid-vm/search-machines.png" alt-text="Wyszukaj serwery z obsługą łuku we wszystkich usługach" border="false":::

1. Na stronie **automaty usługi Azure Arc** wybierz przyłączoną maszynę utworzoną w artykule [Szybki Start](quick-enable-hybrid-vm.md) .

1. W okienku po lewej stronie w sekcji **monitorowanie** wybierz pozycję **szczegółowe informacje** , a następnie **Włącz**.

    :::image type="content" source="./media/tutorial-enable-vm-insights/insights-option.png" alt-text="Wyszukaj serwery z obsługą łuku we wszystkich usługach" border="false":::

1. Na stronie dołączania do usługi Azure Monitor **Insights** zostanie wyświetlony monit o utworzenie obszaru roboczego. W tym samouczku nie zalecamy wybierania istniejącego obszaru roboczego Log Analytics, jeśli już istnieje. Wybierz wartość domyślną, która jest obszarem roboczym z unikatową nazwą w tym samym regionie, w którym zarejestrowano maszynę dołączoną. Ten obszar roboczy został utworzony i skonfigurowany.

    :::image type="content" source="./media/tutorial-enable-vm-insights/enable-vm-insights.png" alt-text="Wyszukaj serwery z obsługą łuku we wszystkich usługach" border="false":::

1. Komunikaty o stanie są odbierane podczas konfigurowania. Ten proces trwa kilka minut, ponieważ rozszerzenia są zainstalowane na podłączonej maszynie.

    :::image type="content" source="./media/tutorial-enable-vm-insights/onboard-vminsights-vm-portal-status.png" alt-text="Wyszukaj serwery z obsługą łuku we wszystkich usługach" border="false":::

    Po zakończeniu otrzymasz komunikat informujący, że maszyna została pomyślnie dołączona, a szczegółowe informacje zostały pomyślnie wdrożone.

## <a name="view-data-collected"></a>Wyświetlanie zebranych danych

Po zakończeniu wdrażania i konfiguracji wybierz pozycję **szczegółowe informacje**, a następnie wybierz kartę **wydajność** . Na karcie wydajność zostanie wyświetlona wybrana grupa liczników wydajności zebranych z systemu operacyjnego gościa maszyny wirtualnej. Przewiń w dół, aby wyświetlić więcej liczników, a następnie przesuń wskaźnik myszy na wykres, aby wyświetlić średnią i percentylość wykonywaną od momentu, kiedy rozszerzenie maszyny wirtualnej Log Analytics zostało zainstalowane na komputerze.

:::image type="content" source="./media/tutorial-enable-vm-insights/insights-performance-charts.png" alt-text="Wyszukaj serwery z obsługą łuku we wszystkich usługach" border="false":::

Wybierz pozycję **Mapuj** , aby otworzyć funkcję Maps, która pokazuje procesy działające na maszynie wirtualnej i ich zależności. Wybierz pozycję **Właściwości** , aby otworzyć okienko właściwości, jeśli nie jest jeszcze otwarte.

:::image type="content" source="./media/tutorial-enable-vm-insights/insights-map.png" alt-text="Wyszukaj serwery z obsługą łuku we wszystkich usługach" border="false":::

Rozwiń procesy dla maszyny wirtualnej. Wybierz jeden z procesów, aby wyświetlić jego szczegóły i wyróżnić jego zależności.

Ponownie wybierz maszynę wirtualną, a następnie wybierz pozycję **zdarzenia dziennika**. Zostanie wyświetlona lista tabel, które są przechowywane w obszarze roboczym Log Analytics dla maszyny wirtualnej. Ta lista będzie się różnić w zależności od tego, czy korzystasz z maszyny wirtualnej z systemem Windows lub Linux. Wybierz tabelę **zdarzeń** . Tabela **zdarzeń** zawiera wszystkie zdarzenia z dziennika zdarzeń systemu Windows. Log Analytics otwiera z prostym zapytaniem do pobrania zebranych wpisów dziennika zdarzeń.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat Azure Monitor, zapoznaj się z następującym artykułem:

> [!div class="nextstepaction"]
> [Omówienie usługi Azure Monitor](../../../azure-monitor/overview.md)