---
title: Uzyskaj analizę kosztów i ustaw budżety dla Azure Batch
description: Dowiedz się, jak uzyskać analizę kosztów, ustawić budżet i obniżyć koszty związane z zasobami obliczeniowymi i licencjami na oprogramowanie używane do uruchamiania obciążeń usługi Batch.
ms.topic: how-to
ms.date: 01/29/2021
ms.openlocfilehash: d1fc2d15a7037e56a8056efa67d2017badb77ffd
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/30/2021
ms.locfileid: "99091331"
---
# <a name="get-cost-analysis-and-set-budgets-for-azure-batch"></a>Uzyskaj analizę kosztów i ustaw budżety dla Azure Batch

Ten temat pomoże Ci zrozumieć koszty, które mogą być powiązane z Azure Batch, jak ustawić budżet dla puli lub konta usługi Batch, a także sposoby zmniejszania kosztów obciążeń usługi Batch.

## <a name="understand-costs-associated-with-batch-resources"></a>Informacje o kosztach związanych z zasobami wsadowymi

Nie ma kosztów używania samego Azure Batch, chociaż mogą istnieć opłaty za bazowe zasoby obliczeniowe i licencje na oprogramowanie używane do uruchamiania obciążeń usługi Batch. Koszty mogą być naliczane z maszyn wirtualnych w puli, transferu danych z maszyny wirtualnej lub wszelkich danych wejściowych lub wyjściowych przechowywanych w chmurze.

### <a name="virtual-machines"></a>Maszyny wirtualne

Maszyny wirtualne są najbardziej znaczącym zasobem używanym do przetwarzania wsadowego. Koszt korzystania z maszyn wirtualnych na potrzeby usługi Batch jest obliczany na podstawie typu, ilości i okresu użytkowania. Opcje rozliczeń maszyn wirtualnych to [płatność zgodnie z rzeczywistym](https://azure.microsoft.com/offers/ms-azr-0003p/) użyciem lub [rezerwacja](../cost-management-billing/reservations/save-compute-costs-reservations.md) (płatność z góry). Obie opcje płatności mają różne zalety w zależności od obciążenia obliczeń i wpłyną na rozliczenia w różny sposób.

Każda maszyna wirtualna w puli utworzonej przy użyciu [konfiguracji maszyny wirtualnej](nodes-and-pools.md#virtual-machine-configuration) ma skojarzony dysk systemu operacyjnego, który korzysta z dysków zarządzanych przez platformę Azure. Dyski zarządzane przez platformę Azure mają dodatkowe koszty, a inne warstwy wydajności dysków mają również inne koszty.

### <a name="storage"></a>Storage

Gdy aplikacje są wdrażane w węzłach usługi Batch (VM) przy użyciu [pakietów aplikacji](batch-application-packages.md), opłaty są naliczane za zasoby magazynu platformy Azure zużywane przez pakiety aplikacji. Są również naliczane opłaty za magazyn wszystkich plików wejściowych lub wyjściowych, takich jak pliki zasobów i inne dane dziennika.

Ogólnie rzecz biorąc, koszt danych magazynu związanych z usługą Batch jest znacznie niższy niż koszt zasobów obliczeniowych.

### <a name="networking-resources"></a>Zasoby sieciowe

Pule wsadowe korzystają z zasobów sieciowych, z których niektóre są powiązane. W szczególności w przypadku pul konfiguracji maszyny wirtualnej są używane standardowe usługi równoważenia obciążenia, które wymagają statycznych adresów IP. Usługi równoważenia obciążenia używane w usłudze Batch są widoczne dla [kont](accounts.md#batch-accounts) skonfigurowanych w trybie subskrypcji użytkownika, ale nie w trybie usługi Batch.

W przypadku standardowych modułów równoważenia obciążenia naliczane są opłaty za wszystkie dane przekazane do i z maszyn wirtualnych puli usługi Batch. Wybierz interfejsy API usługi Batch, które pobierają dane z węzłów puli (takich jak pobieranie pliku zadania/węzła), pakiety aplikacji zadań, pliki zasobów/plików wyjściowych i obrazy kontenerów również będą naliczane opłaty.

### <a name="additional-services"></a>Usługi dodatkowe

W zależności od usług używanych w ramach rozwiązania usługi Batch mogą zostać naliczone dodatkowe opłaty. Zapoznaj się z [kalkulatorem cen](https://azure.microsoft.com/pricing/calculator/) , aby określić koszt każdej dodatkowej usługi. Usługi często używane z usługą Batch, które mogą mieć powiązane koszty, obejmują:

- Application Insights
- Data Factory
- Azure Monitor
- Virtual Network
- Maszyny wirtualne z aplikacjami graficznymi

## <a name="view-cost-analysis-and-create-a-budget-for-a-pool"></a>Wyświetlanie analizy kosztów i Tworzenie budżetu dla puli

W Azure Portal można tworzyć budżety i alerty wydatków dla pul usługi Batch lub kont wsadowych. Budżety i alerty są przydatne w przypadku powiadamiania uczestników projektu o wszelkich ryzykach związanych z przekazaniem, chociaż istnieje możliwość opóźnienia w wykorzystaniu alertów i nieco przekroczenia budżetu.

> [!NOTE]
> Pula w tym przykładzie używa **konfiguracji maszyny wirtualnej**, która jest zalecana w przypadku większości pul i ma opłaty oparte na Virtual Machines strukturze cenowej. Pule korzystające z **konfiguracji Cloud Services** są rozliczone na podstawie Cloud Servicesj struktury cenowej.

### <a name="view-cost-analysis-for-a-batch-pool"></a>Wyświetlanie analizy kosztów dla puli usługi Batch

1. W Azure Portal wpisz lub wybierz pozycję **Cost Management + rozliczanie** .
1. Wybierz swoją subskrypcję w sekcji **zakresy rozliczeń** .
1. W obszarze **Zarządzanie kosztami** wybierz pozycję **Analiza kosztów**.
1. Wybierz pozycję **Dodaj filtr**. Z pierwszej listy rozwijanej wybierz pozycję **zasób**
1. Z drugiej listy rozwijanej wybierz pulę wsadową. Po wybraniu puli zostanie wyświetlona analiza kosztów dla puli, podobnie jak w przykładzie pokazano poniżej.
    ![Zrzut ekranu przedstawiający analizę kosztów puli w Azure Portal.](./media/batch-budget/pool-cost-analysis.png)

Uzyskana analiza kosztów pokazuje koszt puli, a także zasoby, które przyczyniają się do tego kosztu. W tym przykładzie maszyny wirtualne używane w puli to najbardziej kosztowne zasoby.

### <a name="create-a-budget-for-a-batch-pool"></a>Tworzenie budżetu dla puli usługi Batch

1. Na stronie **Analiza kosztów** wybierz pozycję **budżet: brak**.
1. Wybierz pozycję **Utwórz nowy budżet >**.
1. Skorzystaj z okna wyników, aby skonfigurować budżet przeznaczony dla puli. Aby uzyskać więcej informacji, zobacz [Samouczek: Tworzenie budżetów platformy Azure i zarządzanie nimi](../cost-management-billing/costs/tutorial-acm-create-budgets.md).

## <a name="minimize-costs-associated-with-azure-batch"></a>Minimalizuj koszty związane z Azure Batch

W zależności od danego scenariusza warto obniżyć koszty tak długo, jak to możliwe. Rozważ użycie co najmniej jednej z tych strategii w celu zmaksymalizowania wydajności obciążeń i obniżenia potencjalnych kosztów.

### <a name="use-low-priority-virtual-machines"></a>Korzystanie z maszyn wirtualnych o niskim priorytecie

[Maszyny wirtualne o niskim priorytecie](batch-low-pri-vms.md) zmniejszają koszt obciążeń związanych z przetwarzaniem wsadowym dzięki wykorzystaniu nadwyżkowej wydajności obliczeniowej na platformie Azure. W przypadku określenia maszyn wirtualnych o niskim priorytecie w pulach program Batch używa tej nadwyżki do uruchomienia obciążenia. Korzystanie z maszyn wirtualnych o niskim priorytecie zamiast dedykowanych maszyn wirtualnych może mieć znaczne oszczędności.

### <a name="select-a-standard-virtual-machine-os-disk-type"></a>Wybierz standardowy typ dysku systemu operacyjnego maszyny wirtualnej

Platforma Azure oferuje wiele [typów dysków systemu operacyjnego dla maszyn wirtualnych](../virtual-machines/disks-types.md). Większość serii maszyn wirtualnych ma rozmiary obsługujące magazyny w warstwie Premium i standardowa. Po wybraniu rozmiaru maszyny wirtualnej dla puli usługa Batch konfiguruje dyski systemu operacyjnego SSD w warstwie Premium. Gdy wybrano rozmiar maszyny wirtualnej "inny niż s", zostanie użyty tańszy, standardowy typ dysku twardego. Na przykład dyski SSD systemu operacyjnego w warstwie Premium są używane dla `Standard_D2s_v3` dysków z systemem operacyjnym i standardowego dysku systemu operacyjnego `Standard_D2_v3` .

SSD w warstwie Premium dyski systemu operacyjnego są droższe, ale mają wyższą wydajność. Maszyny wirtualne z dyskami w warstwie Premium mogą być uruchamiane nieco szybciej niż maszyny wirtualne ze standardowymi dyskami systemu operacyjnego. W usłudze Batch dysk systemu operacyjnego często nie jest używany, ponieważ pliki aplikacji i zadań znajdują się na tymczasowym dysku SSD maszyny wirtualnej. W związku z tym można często wybrać rozmiar maszyny wirtualnej "non-s", aby uniknąć ponoszenia zwiększonego kosztu dysku SSD Premium, który jest inicjowany w przypadku określenia rozmiaru maszyny wirtualnej.

### <a name="purchase-reservations-for-virtual-machine-instances"></a>Zakup rezerwacji wystąpień maszyn wirtualnych

Jeśli zamierzasz używać usługi Batch przez długi czas, możesz zmniejszyć koszty maszyn wirtualnych, korzystając z [Azure Reservations](../cost-management-billing/reservations/save-compute-costs-reservations.md) dla obciążeń. Stawka rezerwacji jest znacznie niższa od stawki płatność zgodnie z rzeczywistym użyciem. Wystąpienia maszyn wirtualnych używane bez rezerwacji są obciążane opłatami według stawki płatność zgodnie z rzeczywistym użyciem. W przypadku zakupu rezerwacji jest stosowany rabat rezerwacji.

### <a name="use-automatic-scaling"></a>Użyj skalowania automatycznego

[Automatyczne skalowanie](batch-automatic-scaling.md) dynamicznie skaluje liczbę maszyn wirtualnych w puli wsadowej na podstawie wymagań bieżącego zadania. Skalowanie puli na podstawie okresu istnienia zadania powoduje automatyczne skalowanie, dzięki czemu maszyny wirtualne są skalowane i używane tylko wtedy, gdy istnieje zadanie do wykonania. Po zakończeniu zadania lub braku zadań maszyny wirtualne są automatycznie skalowane w celu zapisania zasobów obliczeniowych. Skalowanie umożliwia obniżenie całkowitego kosztu rozwiązania usługi Batch przy użyciu tylko potrzebnych zasobów.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat [Azure Cost Management i rozliczeń](../cost-management-billing/cost-management-billing-overview.md)
- Dowiedz się więcej o [korzystaniu z maszyn wirtualnych o niskim priorytecie w usłudze Batch](batch-low-pri-vms.md).
