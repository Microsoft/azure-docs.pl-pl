---
title: Analiza kosztów i budżet
description: Dowiedz się, jak uzyskać analizę kosztów i ustawić budżet dla podstawowych zasobów obliczeniowych i licencji na oprogramowanie używane do uruchamiania obciążeń usługi Batch.
ms.topic: how-to
ms.date: 07/19/2019
ms.openlocfilehash: 50ca1ecfd0a973ff39dabfcb62175ce820d0a0d6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88654246"
---
# <a name="cost-analysis-and-budgets-for-azure-batch"></a>Analiza kosztów i budżety dla Azure Batch

Nie jest naliczana opłata za Azure Batch, tylko bazowe zasoby obliczeniowe i licencje na oprogramowanie używane do uruchamiania obciążeń usługi Batch. Na wysokim poziomie koszty są naliczane z maszyn wirtualnych w puli, transferu danych z maszyny wirtualnej lub wszelkich danych wejściowych lub wyjściowych przechowywanych w chmurze. Zapoznaj się z pewnymi przykładowymi składnikami usługi Batch, aby zrozumieć, w jaki sposób pochodziły koszty, jak ustawić budżet dla puli lub konta, a także kilka technik zwiększania wydajności obciążeń usługi Batch.

## <a name="batch-resources"></a>Zasoby w usłudze Batch

Maszyny wirtualne są najbardziej znaczącym zasobem używanym do przetwarzania wsadowego. Koszt korzystania z maszyn wirtualnych na potrzeby usługi Batch jest obliczany na podstawie typu, ilości i okresu użytkowania. Opcje rozliczeń maszyn wirtualnych to [płatność zgodnie z rzeczywistym](https://azure.microsoft.com/offers/ms-azr-0003p/) użyciem lub [rezerwacja](../cost-management-billing/reservations/save-compute-costs-reservations.md) (płatność z góry). Obie opcje płatności mają różne zalety, w zależności od obciążenia obliczeń, a oba modele płatności wpłyną na rozliczenia w różny sposób.

Gdy aplikacje są wdrażane w węzłach usługi Batch (VM) przy użyciu [pakietów aplikacji](batch-application-packages.md), opłaty są naliczane za zasoby magazynu platformy Azure zużywane przez pakiety aplikacji. Są również naliczane opłaty za magazyn wszystkich plików wejściowych lub wyjściowych, takich jak pliki zasobów i inne dane dziennika. Ogólnie rzecz biorąc, koszt danych magazynu związanych z usługą Batch jest znacznie niższy niż koszt zasobów obliczeniowych. Każda maszyna wirtualna w puli utworzonej za pomocą **VirtualMachineConfiguration** ma skojarzony dysk systemu operacyjnego, który korzysta z dysków zarządzanych przez platformę Azure. Dyski zarządzane przez platformę Azure mają dodatkowe koszty, a inne warstwy wydajności dysków mają również inne koszty.

Pule wsadowe używają zasobów sieciowych. W szczególności są używane usługi równoważenia obciążenia **VirtualMachineConfiguration** w warstwie Standardowa, które wymagają statycznych adresów IP. Moduły równoważenia obciążenia używane w usłudze Batch są widoczne dla kont **subskrypcji użytkowników** , ale nie są widoczne dla kont **usługi Batch** . W przypadku standardowych modułów równoważenia obciążenia naliczane są opłaty za wszystkie dane przekazane do i z maszyn wirtualnych puli usługi Batch. Wybierz interfejsy API usługi Batch, które pobierają dane z węzłów puli (takich jak pobieranie pliku zadań/węzłów), pakiety aplikacji zadań, pliki zasobów/plików wyjściowych i obrazy kontenerów będą naliczane opłaty.

### <a name="additional-services"></a>Usługi dodatkowe

Usługi, które nie obejmują maszyn wirtualnych i magazynu, mogą być uwzględniane w kosztach konta usługi Batch.

Inne usługi często używane z usługą Batch mogą obejmować:

- Application Insights
- Fabryka danych
- Azure Monitor
- Virtual Network
- Maszyny wirtualne z aplikacjami graficznymi

W zależności od usług używanych w ramach rozwiązania usługi Batch mogą zostać naliczone dodatkowe opłaty. Zapoznaj się z [kalkulatorem cen](https://azure.microsoft.com/pricing/calculator/) , aby określić koszt każdej dodatkowej usługi.

## <a name="cost-analysis-and-budget-for-a-pool"></a>Analiza kosztów i budżet dla puli

Za pomocą Azure Portal można tworzyć budżety i wydawania alertów dla pul lub kont usługi Batch. Budżety i alerty są przydatne do powiadamiania uczestników projektu o wszelkich ryzykach związanych z przekroczeniem. Istnieje możliwość, że jest to opóźnienie dotyczące wydatków i nieznacznie przekraczające budżet. W tym przykładzie zostanie wyświetlona analiza kosztów dla pojedynczej puli partii.

1. W Azure Portal wybierz pozycję **Cost Management + rozliczanie** na lewym pasku nawigacyjnym.
1. Wybierz subskrypcję z sekcji **My subscriptions (Moje subskrypcje** )
1. Przejdź do **analizy kosztów** poniżej sekcji **Cost Management** lewego paska nawigacyjnego, która będzie zawierać następujący widok:
1. Wybierz pozycję **Dodaj filtr**. Z pierwszej listy rozwijanej wybierz pozycję **zasób** ![ Wybierz filtr zasobów](./media/batch-budget/resource-filter.png)
1. Z drugiej listy rozwijanej wybierz pulę wsadową. Po wybraniu puli analiza kosztów będzie wyglądać podobnie do poniższej analizy.
    ![Analiza kosztów puli](./media/batch-budget/pool-cost-analysis.png)

Uzyskana analiza kosztów pokazuje koszt puli, a także zasoby, które przyczyniają się do tego kosztu. W tym przykładzie maszyny wirtualne używane w puli to najbardziej kosztowne zasoby.

Aby utworzyć budżet dla puli wybierz pozycję **budżet: brak**, a następnie wybierz pozycję **utwórz nowy budżet >**. Teraz Użyj tego okna, aby skonfigurować budżet przeznaczony dla puli.

Aby uzyskać więcej informacji na temat konfigurowania budżetu, zobacz [Tworzenie budżetów platformy Azure i zarządzanie nimi](../cost-management-billing/costs/tutorial-acm-create-budgets.md).

> [!NOTE]
> Azure Batch jest oparta na platformie Azure Cloud Services i technologii Azure Virtual Machines. Po wybraniu **konfiguracji Cloud Services**zostanie naliczona opłata oparta na Cloud Services strukturze cenowej. Po wybraniu opcji **Konfiguracja maszyny wirtualnej**opłata jest naliczana na podstawie Virtual Machinesj struktury cenowej. Przykład na tej stronie używa **konfiguracji maszyny wirtualnej**.

## <a name="minimize-cost"></a>Minimalizuj koszt

Korzystanie z kilku maszyn wirtualnych i usług platformy Azure przez dłuższy czas może być kosztowne. Na szczęście dostępne są usługi ułatwiające zmniejszenie wydatków, a także strategie maksymalizowania wydajności obciążeń.

### <a name="low-priority-virtual-machines"></a>Maszyny wirtualne o niskim priorytecie

Maszyny wirtualne o niskim priorytecie zmniejszają koszt obciążeń związanych z przetwarzaniem wsadowym dzięki wykorzystaniu nadwyżkowej wydajności obliczeniowej na platformie Azure. W przypadku określenia maszyn wirtualnych o niskim priorytecie w pulach program Batch używa tej nadwyżki do uruchomienia obciążenia. Istnieje znaczny koszt oszczędności przy użyciu maszyn wirtualnych o niskim priorytecie zamiast dedykowanych maszyn wirtualnych.

Dowiedz się więcej o konfigurowaniu maszyn wirtualnych o niskim priorytecie dla obciążenia przy [użyciu maszyn wirtualnych o niskim priorytecie przy użyciu usługi Batch](batch-low-pri-vms.md).

### <a name="virtual-machine-os-disk-type"></a>Typ dysku systemu operacyjnego maszyny wirtualnej

Istnieje wiele [typów dysków systemu operacyjnego maszyny wirtualnej](../virtual-machines/disks-types.md). Większość serii maszyn wirtualnych ma rozmiary obsługujące magazyny w warstwie Premium i standardowa. Po wybraniu rozmiaru maszyny wirtualnej dla puli usługa Batch konfiguruje dyski systemu operacyjnego SSD w warstwie Premium. Gdy wybrano rozmiar maszyny wirtualnej "inny niż s", zostanie użyty tańszy, standardowy typ dysku twardego. Na przykład dyski SSD systemu operacyjnego w warstwie Premium są używane dla `Standard_D2s_v3` dysków z systemem operacyjnym i standardowego dysku systemu operacyjnego `Standard_D2_v3` .

SSD w warstwie Premium dyski systemu operacyjnego są droższe, ale większa wydajność i maszyny wirtualne z dyskami w warstwie Premium mogą być uruchamiane nieco szybciej niż maszyny wirtualne ze standardowymi dyskami systemu operacyjnego. W usłudze Batch dysk systemu operacyjnego często nie jest używany, ponieważ aplikacje i pliki zadań znajdują się na tymczasowym dysku SSD maszyn wirtualnych. W związku z tym w wielu przypadkach nie trzeba płacić za większy koszt dysku SSD Premium, który jest inicjowany po określeniu rozmiaru maszyny wirtualnej.

### <a name="reserved-virtual-machine-instances"></a>Zarezerwowane wystąpienia maszyn wirtualnych

Jeśli zamierzasz używać usługi Batch przez długi czas, możesz zaoszczędzić na kosztach maszyn wirtualnych, korzystając z [Azure Reservations](../cost-management-billing/reservations/save-compute-costs-reservations.md) dla obciążeń. Stawka rezerwacji jest znacznie niższa od stawki płatność zgodnie z rzeczywistym użyciem. Wystąpienia maszyn wirtualnych używane bez rezerwacji są obciążane opłatami według stawki płatność zgodnie z rzeczywistym użyciem. W przypadku zakupienia rezerwacji rabat zostanie zastosowany i nie będą już naliczane opłaty według stawek płatność zgodnie z rzeczywistym użyciem.

### <a name="automatic-scaling"></a>Automatyczne skalowanie

[Automatyczne skalowanie](batch-automatic-scaling.md) dynamicznie skaluje liczbę maszyn wirtualnych w puli wsadowej na podstawie wymagań bieżącego zadania. Dzięki skalowaniu puli na podstawie okresu istnienia zadania automatyczne skalowanie zapewnia, że maszyny wirtualne są skalowane i używane tylko wtedy, gdy istnieje zadanie do wykonania. Gdy zadanie zostało ukończone lub nie ma żadnych zadań, maszyny wirtualne są automatycznie skalowane w celu zapisania zasobów obliczeniowych. Skalowanie umożliwia obniżenie całkowitego kosztu rozwiązania usługi Batch przy użyciu tylko potrzebnych zasobów.

Aby uzyskać więcej informacji na temat automatycznego skalowania, zobacz [Automatyczne skalowanie węzłów obliczeniowych w puli Azure Batch](batch-automatic-scaling.md).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat [interfejsów API usługi Batch i narzędzi](batch-apis-tools.md) dostępnych do kompilowania i monitorowania rozwiązań usługi Batch.  

- Dowiedz się więcej o [maszynach wirtualnych o niskim priorytecie w usłudze Batch](batch-low-pri-vms.md).
