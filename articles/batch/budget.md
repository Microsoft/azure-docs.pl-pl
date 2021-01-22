---
title: Analiza kosztów i budżety
description: Dowiedz się, jak uzyskać analizę kosztów, ustawić budżet i obniżyć koszty związane z zasobami obliczeniowymi i licencjami na oprogramowanie używane do uruchamiania obciążeń usługi Batch.
ms.topic: how-to
ms.date: 01/21/2021
ms.openlocfilehash: 1a950f23b7ecee11dd7da5414b7eed9e87277850
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98679321"
---
# <a name="cost-analysis-and-budgets-for-azure-batch"></a>Analiza kosztów i budżety dla Azure Batch

Nie ma kosztów używania samego Azure Batch, chociaż mogą istnieć opłaty za bazowe zasoby obliczeniowe i licencje na oprogramowanie używane do uruchamiania obciążeń usługi Batch. Koszty mogą być naliczane z maszyn wirtualnych w puli, transferu danych z maszyny wirtualnej lub wszelkich danych wejściowych lub wyjściowych przechowywanych w chmurze. Ten temat pomoże Ci zrozumieć, w jaki sposób pochodziły z kosztów, jak ustawić budżet dla puli lub konta usługi Batch oraz jak zmniejszyć koszty obciążeń usługi Batch.

## <a name="batch-resources"></a>Zasoby w usłudze Batch

Maszyny wirtualne są najbardziej znaczącym zasobem używanym do przetwarzania wsadowego. Koszt korzystania z maszyn wirtualnych na potrzeby usługi Batch jest obliczany na podstawie typu, ilości i okresu użytkowania. Opcje rozliczeń maszyn wirtualnych to [płatność zgodnie z rzeczywistym](https://azure.microsoft.com/offers/ms-azr-0003p/) użyciem lub [rezerwacja](../cost-management-billing/reservations/save-compute-costs-reservations.md) (płatność z góry). Obie opcje płatności mają różne zalety w zależności od obciążenia obliczeń i wpłyną na rozliczenia w różny sposób.

Gdy aplikacje są wdrażane w węzłach usługi Batch (VM) przy użyciu [pakietów aplikacji](batch-application-packages.md), opłaty są naliczane za zasoby magazynu platformy Azure zużywane przez pakiety aplikacji. Są również naliczane opłaty za magazyn wszystkich plików wejściowych lub wyjściowych, takich jak pliki zasobów i inne dane dziennika. Ogólnie rzecz biorąc, koszt danych magazynu związanych z usługą Batch jest znacznie niższy niż koszt zasobów obliczeniowych. Każda maszyna wirtualna w puli utworzonej przy użyciu [konfiguracji maszyny wirtualnej](nodes-and-pools.md#virtual-machine-configuration) ma skojarzony dysk systemu operacyjnego, który korzysta z dysków zarządzanych przez platformę Azure. Dyski zarządzane przez platformę Azure mają dodatkowe koszty, a inne warstwy wydajności dysków mają również inne koszty.

Pule wsadowe używają zasobów sieciowych. W szczególności w przypadku pul **VirtualMachineConfiguration** są używane standardowe usługi równoważenia obciążenia, które wymagają statycznych adresów IP. Moduły równoważenia obciążenia używane w usłudze Batch są widoczne dla kont **subskrypcji użytkowników** , ale nie są widoczne dla kont **usługi Batch** . W przypadku standardowych modułów równoważenia obciążenia naliczane są opłaty za wszystkie dane przekazane do i z maszyn wirtualnych puli usługi Batch. Wybierz interfejsy API usługi Batch, które pobierają dane z węzłów puli (takich jak pobieranie pliku zadań/węzłów), pakiety aplikacji zadań, pliki zasobów/plików wyjściowych i obrazy kontenerów będą naliczane opłaty.

### <a name="additional-services"></a>Usługi dodatkowe

Usługi, które nie obejmują maszyn wirtualnych i magazynu, mogą być uwzględniane w kosztach konta usługi Batch.

Inne usługi często używane z usługą Batch mogą obejmować:

- Application Insights
- Data Factory
- Azure Monitor
- Virtual Network
- Maszyny wirtualne z aplikacjami graficznymi

W zależności od usług używanych w ramach rozwiązania usługi Batch mogą zostać naliczone dodatkowe opłaty. Zapoznaj się z [kalkulatorem cen](https://azure.microsoft.com/pricing/calculator/) , aby określić koszt każdej dodatkowej usługi.

## <a name="cost-analysis-and-budget-for-a-pool"></a>Analiza kosztów i budżet dla puli

W Azure Portal można tworzyć budżety i alerty wydatków dla pul usługi Batch lub kont wsadowych. Budżety i alerty są przydatne w przypadku powiadamiania uczestników projektu o wszelkich ryzykach związanych z przekazaniem, chociaż istnieje możliwość opóźnienia w wykorzystaniu alertów i nieco przekroczenia budżetu.

W tym przykładzie zostanie wyświetlona analiza kosztów dla pojedynczej puli partii.

1. W Azure Portal wpisz lub wybierz pozycję **Cost Management + rozliczanie** .
1. Wybierz swoją subskrypcję w sekcji **zakresy rozliczeń** .
1. W obszarze **Zarządzanie kosztami** wybierz pozycję **Analiza kosztów**.
1. Wybierz pozycję **Dodaj filtr**. Z pierwszej listy rozwijanej wybierz pozycję **zasób**
1. Z drugiej listy rozwijanej wybierz pulę wsadową. Po wybraniu puli zostanie wyświetlona analiza kosztów dla puli, podobnie jak w przykładzie pokazano poniżej.
    ![Zrzut ekranu przedstawiający analizę kosztów puli w Azure Portal.](./media/batch-budget/pool-cost-analysis.png)

Uzyskana analiza kosztów pokazuje koszt puli, a także zasoby, które przyczyniają się do tego kosztu. W tym przykładzie maszyny wirtualne używane w puli to najbardziej kosztowne zasoby.

Aby utworzyć budżet dla puli wybierz pozycję **budżet: brak**, a następnie wybierz pozycję **utwórz nowy budżet >**. Teraz Użyj tego okna, aby [skonfigurować budżet](../cost-management-billing/costs/tutorial-acm-create-budgets.md) przeznaczony dla puli.

> [!NOTE]
> Azure Batch jest oparta na platformie Azure Cloud Services i technologii Azure Virtual Machines. Po wybraniu **konfiguracji Cloud Services** zostanie naliczona opłata oparta na Cloud Services strukturze cenowej. Po wybraniu opcji **Konfiguracja maszyny wirtualnej** opłata jest naliczana na podstawie Virtual Machinesj struktury cenowej. Przykład na tej stronie używa **konfiguracji maszyny wirtualnej**, która jest zalecana w przypadku większości pul usługi Batch.

## <a name="minimize-cost"></a>Minimalizuj koszt

Korzystanie z kilku maszyn wirtualnych i usług platformy Azure przez dłuższy czas może być kosztowne. Rozważ użycie tych strategii w celu zmaksymalizowania wydajności obciążeń i obniżenia kosztów.

### <a name="low-priority-virtual-machines"></a>Maszyny wirtualne o niskim priorytecie

[Maszyny wirtualne o niskim priorytecie](batch-low-pri-vms.md) zmniejszają koszt obciążeń związanych z przetwarzaniem wsadowym dzięki wykorzystaniu nadwyżkowej wydajności obliczeniowej na platformie Azure. W przypadku określenia maszyn wirtualnych o niskim priorytecie w pulach program Batch używa tej nadwyżki do uruchomienia obciążenia. Korzystanie z maszyn wirtualnych o niskim priorytecie zamiast dedykowanych maszyn wirtualnych może mieć znaczne oszczędności.

### <a name="virtual-machine-os-disk-type"></a>Typ dysku systemu operacyjnego maszyny wirtualnej

Platforma Azure oferuje wiele [typów dysków systemu operacyjnego dla maszyn wirtualnych](../virtual-machines/disks-types.md). Większość serii maszyn wirtualnych ma rozmiary obsługujące magazyny w warstwie Premium i standardowa. Po wybraniu rozmiaru maszyny wirtualnej dla puli usługa Batch konfiguruje dyski systemu operacyjnego SSD w warstwie Premium. Gdy wybrano rozmiar maszyny wirtualnej "inny niż s", zostanie użyty tańszy, standardowy typ dysku twardego. Na przykład dyski SSD systemu operacyjnego w warstwie Premium są używane dla `Standard_D2s_v3` dysków z systemem operacyjnym i standardowego dysku systemu operacyjnego `Standard_D2_v3` .

SSD w warstwie Premium dyski systemu operacyjnego są droższe, ale mają wyższą wydajność. Maszyny wirtualne z dyskami w warstwie Premium mogą być uruchamiane nieco szybciej niż maszyny wirtualne ze standardowymi dyskami systemu operacyjnego. W usłudze Batch dysk systemu operacyjnego często nie jest używany, ponieważ pliki aplikacji i zadań znajdują się na tymczasowym dysku SSD maszyny wirtualnej. W związku z tym można często wybrać rozmiar maszyny wirtualnej "non-s", aby uniknąć ponoszenia zwiększonego kosztu dysku SSD Premium, który jest inicjowany w przypadku określenia rozmiaru maszyny wirtualnej.

### <a name="reserved-virtual-machine-instances"></a>Zarezerwowane wystąpienia maszyn wirtualnych

Jeśli zamierzasz używać usługi Batch przez długi czas, możesz zmniejszyć koszty maszyn wirtualnych, korzystając z [Azure Reservations](../cost-management-billing/reservations/save-compute-costs-reservations.md) dla obciążeń. Stawka rezerwacji jest znacznie niższa od stawki płatność zgodnie z rzeczywistym użyciem. Wystąpienia maszyn wirtualnych używane bez rezerwacji są obciążane opłatami według stawki płatność zgodnie z rzeczywistym użyciem. W przypadku zakupu rezerwacji jest stosowany rabat rezerwacji.

### <a name="automatic-scaling"></a>Automatyczne skalowanie

[Automatyczne skalowanie](batch-automatic-scaling.md) dynamicznie skaluje liczbę maszyn wirtualnych w puli wsadowej na podstawie wymagań bieżącego zadania. Skalowanie puli na podstawie okresu istnienia zadania powoduje automatyczne skalowanie, dzięki czemu maszyny wirtualne są skalowane i używane tylko wtedy, gdy istnieje zadanie do wykonania. Po zakończeniu zadania lub braku zadań maszyny wirtualne są automatycznie skalowane w celu zapisania zasobów obliczeniowych. Skalowanie umożliwia obniżenie całkowitego kosztu rozwiązania usługi Batch przy użyciu tylko potrzebnych zasobów.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat [interfejsów API usługi Batch i narzędzi](batch-apis-tools.md) dostępnych do kompilowania i monitorowania rozwiązań usługi Batch.  
- Dowiedz się więcej o [korzystaniu z maszyn wirtualnych o niskim priorytecie w usłudze Batch](batch-low-pri-vms.md).
