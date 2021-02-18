---
title: Dodawanie metryk rozszerzonych dla maszyn wirtualnych platformy Azure
description: Ten artykuł omawia włączanie i konfigurowanie metryk rozszerzonych diagnostyki dla maszyn wirtualnych platformy Azure.
author: bandersmsft
ms.reviewer: vitavor
ms.author: banders
ms.date: 03/12/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cloudyn
ms.custom: seodec18
ROBOTS: NOINDEX
ms.openlocfilehash: da2e20e333fe499998fff72b175442650f5f1e28
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100595310"
---
# <a name="add-extended-metrics-for-azure-virtual-machines"></a>Dodawanie metryk rozszerzonych dla maszyn wirtualnych platformy Azure

Usługa Cloudyn używa danych metryk platformy Azure z maszyn wirtualnych platformy Azure do prezentowania szczegółowych informacji na temat ich zasobów. Dane metryk, nazywane także licznikami wydajności, są używane przez usługę Cloudyn do generowania raportów. Jednak usługa Cloudyn nie zbiera automatycznie wszystkich danych metryk platformy Azure z maszyn wirtualnych gościa — należy włączyć zbieranie metryk. Ten artykuł omawia włączanie i konfigurowanie dodatkowych metryk diagnostyki dla maszyn wirtualnych platformy Azure.

Włączenie zbierania metryk umożliwia:

- Uzyskiwanie informacji o zbliżaniu się maszyn wirtualnych do limitów pamięci, dysku i procesora
- Wykrywanie trendów użycia i anomalii
- Kontrolowanie kosztów przez stosowanie rozmiarów odpowiednich do użycia
- Uzyskiwanie rekomendacji dotyczących ekonomicznej optymalizacji rozmiarów z usługi Cloudyn

Na przykład można monitorować procent użycia procesora i pamięci dla maszyn wirtualnych platformy Azure. Metryki maszyny wirtualnej platformy Azure odpowiadają wartościom _Procent użycia procesora CPU_ i _\Pamięć\% Zadeklarowane bajty w użyciu_.

> [!NOTE]
> Zbieranie danych metryk rozszerzonych jest obsługiwane tylko w przypadku monitorowania na poziomie gościa platformy Azure. Usługa Cloudyn nie jest zgodna z [agentem usługi Log Analytics](../../azure-monitor/agents/agents-overview.md).

[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

## <a name="determine-whether-extended-metrics-are-enabled"></a>Określanie, czy metryki rozszerzone są włączone

1. Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).
2. W obszarze **Maszyny wirtualne** wybierz maszynę wirtualną, a następnie w obszarze **Monitorowanie** wybierz pozycję **Metryki**. Zostanie wyświetlona lista dostępnych metryk.
3. Wybierz niektóre metryki — ich dane zostaną wyświetlone w postaci wykresu.  
    ![Przykładowa metryka — procent procesora hosta](./media/azure-vm-extended-metrics/metric01.png)

W poprzednim przykładzie dla hostów jest dostępny ograniczony zestaw metryk standardowych, przy czym metryki dotyczące pamięci nie są dostępne. Metryki dotyczące pamięci są częścią metryk rozszerzonych. W tym przypadku metryki rozszerzone nie są włączone dla maszyny wirtualnej. Aby włączyć metryki rozszerzone, należy wykonać kilka dodatkowych kroków. Poniższy przewodnik przeprowadzi Cię przez proces włączania metryk.

## <a name="enable-extended-metrics-in-the-azure-portal"></a>Włączanie metryk rozszerzonych w witrynie Azure Portal

Metryki standardowe to metryki komputera hosta. Przykładowa metryka standardowa to _Procent użycia procesora CPU_. Są także dostępne podstawowe metryki maszyn wirtualnych gościa i są one także nazywane metrykami rozszerzonymi. Przykłady metryk rozszerzonych to: _\Pamięć\% Zadeklarowane bajty w użyciu_ i _\Pamięć\Dostępne bajty_.

Włączanie metryk rozszerzonych jest proste. Dla każdej maszyny wirtualnej należy włączyć monitorowania na poziomie gościa. Po włączeniu monitorowania na poziomie gościa na maszynie wirtualnej jest instalowany agent diagnostyki platformy Azure. Domyślnie jest dodawany podstawowy zestaw metryk rozszerzonych. Następujący proces jest taki sam dla klasycznych i zwykłych maszyn wirtualnych oraz maszyn wirtualnych z systemami Windows i Linux.

Pamiętaj, że monitorowanie na poziomie gościa zarówno w systemie Windows, jak i Linux wymaga konta magazynu. Jeśli włączysz monitorowanie na poziomie gościa i nie wybierzesz istniejącego konta magazynu, zostanie ono utworzone.

### <a name="enable-guest-level-monitoring-on-existing-vms"></a>Włączanie monitorowania na poziomie gościa na istniejących maszynach wirtualnych

1. W obszarze **Maszyny wirtualne** wyświetl listę maszyn wirtualnych i wybierz jedną z nich.
2. W obszarze **Monitorowanie** wybierz pozycję **Ustawienia diagnostyczne**.
3. Na stronie Ustawienia diagnostyczne kliknij pozycję **Włącz monitorowanie na poziomie gościa**.  
    ![Włączanie monitorowania na poziomie gościa na stronie Przegląd](./media/azure-vm-extended-metrics/enable-guest-monitoring.png)
4. Po kilku minutach agent diagnostyki platformy Azure zostanie zainstalowany na maszynie wirtualnej. Zostanie dodany podstawowy zestaw metryk. Odśwież stronę. Dodane liczniki wydajności są wyświetlane na karcie Przegląd.
5. W obszarze Monitorowanie wybierz pozycję **Metryki**.
6. Na wykresie metryk w obszarze **Przestrzeń nazw metryki** wybierz pozycję **Gość (klasyczny)** .
7. Na liście Metryki można zobaczyć wszystkie dostępne liczniki wydajności dla maszyny wirtualnej gościa.  
    ![Lista przykładowych metryk rozszerzonych](./media/azure-vm-extended-metrics/extended-metrics.png)

### <a name="enable-guest-level-monitoring-on-new-vms"></a>Włączanie monitorowania na poziomie gościa na nowej maszynie wirtualnej

Podczas tworzenia nowych maszyn wirtualnych na karcie Zarządzanie wybierz opcję **Włącz** dla pozycji **Diagnostyka gościa systemu operacyjnego**.

![Ustawianie opcji Włącz dla pozycji Diagnostyka gościa systemu operacyjnego](./media/azure-vm-extended-metrics/new-enable-diag.png)

Aby uzyskać więcej informacji na temat włączania metryk rozszerzonych dla maszyn wirtualnych platformy Azure, zobacz [Omówienie agenta platformy Azure dla systemu Linux i informacje na temat korzystania z niego](../../virtual-machines/extensions/agent-linux.md) i [Omówienie agenta maszyny wirtualnej platformy Azure](../../virtual-machines/extensions/agent-windows.md).

## <a name="resource-manager-credentials"></a>Poświadczenia usługi Resource Manager

Po włączeniu metryk rozszerzonych upewnij się, że usługa Cloudyn ma dostęp do [poświadczeń usługi Resource Manager](./activate-subs-accounts.md). Usługa Cloudyn wymaga poświadczeń na potrzeby zbierania i wyświetlania danych wydajności dla maszyn wirtualnych. Są one także używane do tworzenia rekomendacji dotyczących optymalizacji kosztów. Usługa Cloudyn potrzebuje danych wydajności dla wystąpienia z co najmniej trzech dni, aby określić, czy jest ono kandydatem do rekomendacji dotyczącej zmniejszenia rozmiaru.

## <a name="enable-vm-metrics-with-a-script"></a>Włączanie metryk maszyny wirtualnej za pomocą skryptu

Metryki maszyny wirtualnej można włączyć za pomocą skryptów programu Azure PowerShell. Jeśli chcesz włączyć metryki dla wielu maszyn wirtualnych, możesz użyć skryptu w celu zautomatyzowania tego procesu. Przykładowe skrypty znajdują się w witrynie GitHub na stronie dotyczącej [włączania diagnostyki dla platformy Azure](https://github.com/Cloudyn/azure-enable-diagnostics).

## <a name="view-azure-performance-metrics"></a>Wyświetlanie metryk wydajności platformy Azure

Aby wyświetlić metryki wydajności dla wystąpień platformy Azure w portalu usługi Cloudyn, przejdź do pozycji **Zasoby** > **Obliczenia** > **Eksplorator wystąpienia**. Na liście wystąpień maszyn wirtualnych rozwiń wystąpienie, a następnie rozwiń zasób, aby wyświetlić szczegóły.

![Przykładowe informacje wyświetlane w eksploratorze wystąpienia](./media/azure-vm-extended-metrics/instance-explorer.png)

## <a name="next-steps"></a>Następne kroki

- Jeśli dostęp do interfejsu API usługi Azure Resource Manager nie został jeszcze włączony dla Twoich kont, przejdź do artykułu [Aktywowanie subskrypcji i kont](./activate-subs-accounts.md).