---
title: Monitorowanie wzorców zmian na maszynach wirtualnych
description: Dowiedz się, jak monitorować wzorce zmian na Virtual Machines chronionych przy użyciu Azure Site Recovery
author: Sharmistha-Rai
manager: gaggupta
ms.topic: how-to
ms.date: 09/09/2020
ms.author: sharrai
ms.openlocfilehash: 51f45b5c0b8a2729f64eb376e06875719474cbfc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89664180"
---
# <a name="monitoring-churn-patterns-on-virtual-machines"></a>Monitorowanie wzorców zmian na maszynach wirtualnych

Ten artykuł zawiera omówienie różnych narzędzi, których można użyć do monitorowania wzorców zmian na maszynie wirtualnej. Korzystając z odpowiednich narzędzi, można łatwo sprawdzić, które aplikacje powodują duże zmiany, a następnie wykonać dalsze akcje dla tej aplikacji.

## <a name="for-azure-virtual-machines-windows-or-linux"></a>W przypadku maszyn wirtualnych platformy Azure (system Windows lub Linux)

Jeśli maszyna jest hostowana na platformie Azure i korzysta z dysku zarządzanego lub niezarządzanego do przechowywania, możesz łatwo śledzić wydajność, śledząc metryki dysków. Pozwala to na ścisłe monitorowanie i wybór odpowiedniego dysku do wzorca użycia aplikacji. Można go również użyć do tworzenia alertów, diagnostyki i automatyzacji kompilowania. [Dowiedz się więcej](https://azure.microsoft.com/blog/per-disk-metrics-managed-disks/).

Po włączeniu ochrony maszyn przy użyciu Azure Site Recovery można monitorować maszyny przy użyciu dzienników Azure Monitor i Log Analytics. [Dowiedz się więcej](https://docs.microsoft.com/azure/site-recovery/monitor-log-analytics).

Dostępne są również pewne narzędzia specyficzne dla systemu operacyjnego, których można użyć.

## <a name="for-windows-machines"></a>Dla maszyn z systemem Windows

Na wypadek, gdy masz maszynę lokalną lub niedziałającą w systemie operacyjnym Windows, istnieje kilka dostępnych narzędzi.

Oprócz sprawdzania użycia dysku w Menedżerze zadań zawsze możesz odwoływać się do **Monitor zasobów** i **monitora wydajności**. Te narzędzia już znajdują się na maszynach z systemem Windows.

### <a name="resource-monitor"></a>Monitor zasobów

**Monitor zasobów** wyświetla informacje o użyciu zasobów sprzętowych i programowych w czasie rzeczywistym. Aby uruchomić Monitor zasobów na komputerze z systemem Windows, wykonaj poniższe czynności:

1. Naciśnij klawisze Win + R i wpisz _ResMon_.
1. Po ResMon, czyli Monitor zasobów, zostanie otwarte okno przełączenia na kartę dysk. Zapewnia następujący widok:

    ![Karta Monitor zasobów dysku](./media/monitoring-high-churn/resmon-disk-tab.png)

1. Ta karta musi być stale monitorowana przez jakiś czas, aby uzyskać jasne zdjęcie. W powyższym przykładzie widzimy, że _wmiprv.exe_ jest wysoka.

Po zidentyfikowaniu aplikacji powodujących duże zmiany na maszynie można podjąć niezbędne działania w celu zapoznaniu się z tymi aplikacjami.

### <a name="performance-monitor"></a>Monitorowanie wydajności

**Monitor wydajności** monitoruje różne działania na komputerze, takie jak użycie procesora CPU lub pamięci. Aby uruchomić Monitor wydajności na komputerze z systemem Windows, wykonaj poniższe czynności:

1. Naciśnij pozycję Win + R i wpisz _perfmon_.
1. Po wykonaniu tych czynności Monitor wydajności, który zostanie otwarty, zostanie wyświetlony następujący widok:

    ![Monitor wydajności krok 1](./media/monitoring-high-churn/perfmon-step1.png)

1. Rozwiń folder **narzędzia monitorowania** po prawej stronie i kliknij pozycję Monitor wydajności. Spowoduje to otwarcie poniższego widoku dostarczającego informacje o bieżącej wydajności w czasie rzeczywistym —

    ![Monitor wydajności step2](./media/monitoring-high-churn/perfmon-step1.png)

1. Ten Graf służy do monitorowania tylko jednego monitora, czyli "czas procesora" (%) — zgodnie z opisem w tabeli bezpośrednio poniżej grafu. Możesz dodać więcej elementów do monitorowania, klikając **"+"** na górze narzędzia.
1. Poniżej przedstawiono wizualizację wyglądu monitora wydajności po dodaniu do niego więcej liczników

    ![Monitor wydajności step3](./media/monitoring-high-churn/perfmon-step3.png)

Więcej informacji na temat monitora wydajności [znajdziesz tutaj](https://docs.microsoft.com/dynamics365/business-central/dev-itpro/administration/monitor-use-performance-monitor-collect-event-trace-data).

## <a name="for-linux-machines"></a>Dla maszyn z systemem Linux

Na wypadek, gdy masz maszynę lokalną lub niedziałającą w systemie operacyjnym Linux, istnieje kilka dostępnych narzędzi do monitorowania wzorców zmian.

### <a name="iotop"></a>Iotop

Jednym z najczęściej używanych narzędzi jest _iotop_. Jest to narzędzie służące do wyświetlania aktywności dyskowej w czasie rzeczywistym. Można wyświetlić listę procesów, które są wykonywane we/wy, wraz z używaną przepustowością dysku.

Otwórz wiersz polecenia i uruchom polecenie `iotop` .

### <a name="iostat"></a>IoStat

IoStat to proste narzędzie, które będzie zbierać i wyświetlać statystyki wejściowego i wyjściowego urządzenia magazynującego systemu. To narzędzie jest często używane do śledzenia problemów z wydajnością urządzeń pamięci masowej, w tym urządzeń, dysków lokalnych, dysków zdalnych.

Otwórz wiersz polecenia i uruchom polecenie `iostat` .

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak monitorować przy użyciu [Azure monitor](monitor-log-analytics.md).
