---
title: Seria B — Virtual Machines platformy Azure
description: Opisuje serię B pojemności maszyn wirtualnych platformy Azure z możliwością przełożenia.
services: virtual-machines
ms.subservice: sizes
author: styli365
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: sttsinar
ms.openlocfilehash: 31a65cab7dfdd478560b7babba156cec7645cf33
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98917255"
---
# <a name="b-series-burstable-virtual-machine-sizes"></a>Rozmiary maszyn wirtualnych z serii B

Maszyny wirtualne z serii B są idealnym rozwiązaniem w przypadku obciążeń, które nie wymagają pełnej wydajności procesora CPU, takich jak serwery sieci Web, Weryfikacja koncepcji, małe bazy danych i środowiska kompilacji programistycznej. Te obciążenia zwykle mają wymagania dotyczące wydajności. Seria B umożliwia zakupienie rozmiaru maszyny wirtualnej z wydajnością bazową, która może kompilować kredyty, gdy jest ona mniejsza niż jego linia bazowa. Gdy maszyna wirtualna ma kredyty skumulowane, maszyna wirtualna może przekroczyć poziom do 100% vCPU, gdy aplikacja wymaga wyższych wydajności procesora CPU.

Seria B obejmuje następujące rozmiary maszyn wirtualnych:

[Jednostka obliczeniowa platformy Azure (ACU)](./acu.md): różne *<br>
[Premium Storage](premium-storage-performance.md): obsługiwane<br>
[Buforowanie Premium Storage](premium-storage-performance.md): nieobsługiwane<br>
[Migracja na żywo](maintenance-and-updates.md): obsługiwane<br>
[Aktualizacje z zachowaniem pamięci](maintenance-and-updates.md): obsługiwane<br>
[Obsługa generacji maszyny wirtualnej](generation-2.md): generacja 1 i 2<br>
[Przyspieszona sieć](../virtual-network/create-vm-accelerated-networking-cli.md): obsługiwane * *<br>

* Maszyny wirtualne z serii B są szeregowe, a tym samym liczby ACU są różne w zależności od obciążeń i podstawowych zastosowań.<br>
* * Przyspieszona sieć jest obsługiwana tylko w przypadku *Standard_B12ms*, *Standard_B16ms* i *Standard_B20ms*.
<br>
<br>

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Podstawowa wydajność procesora CPU maszyny wirtualnej | Maksymalna wydajność procesora dla maszyny wirtualnej | Kredyty początkowe | Środki na kredyt/godzinę | Maksymalna liczba kredytów w banku | Maks. liczba dysków danych | Maksymalna przepływność magazynu w pamięci podręcznej i tymczasowej: IOPS/MB/s | Maksymalna przepływność dysku w pamięci podręcznej: liczba operacji we/wy na sekundę | Maksymalna liczba kart sieciowych |
|---|---|---|---|---|---|---|---|---|---|---|---|---|
| Standard_B1ls<sup>1</sup> | 1  | 0,5 | 4   | 5%   | 100%  | 30  | 3   | 72   | 2  | 200/10    | 160/10    | 2  |
| Standard_B1s              | 1  | 1   | 4   | 10%  | 100%  | 30  | 6   | 144  | 2  | 400/10    | 320/10    | 2  |
| Standard_B1ms             | 1  | 2   | 4   | 20%  | 100%  | 30  | 12  | 288  | 2  | 800/10    | 640/10    | 2  |
| Standard_B2s              | 2  | 4   | 8   | 40%  | 200%  | 60  | 24  | 576  | 4  | 1600/15   | 1280/15   | 3  |
| Standard_B2ms             | 2  | 8   | 16  | 60%  | 200%  | 60  | 36  | 864  | 4  | 2400/22.5 | 1920/22.5 | 3  |
| Standard_B4ms             | 4  | 16  | 32  | 90%  | 400%  | 120 | 54  | 1296 | 8  | 3600/35   | 2880/35   | 4  |
| Standard_B8ms             | 8  | 32  | 64  | 135% | 800%  | 240 | 81  | 1944 | 16 | 4320/50   | 4320/50   | 4  |
| Standard_B12ms            | 12 | 48  | 96  | 202% | 1200% | 360 | 121 | 2909 | 16 | 6480/75   | 4320/50   | 6  |
| Standard_B16ms            | 16 | 64  | 128 | 270% | 1600% | 480 | 162 | 3888 | 32 | 8640/100  | 4320/50   | 8  |
| Standard_B20ms            | 20 | 80  | 160 | 337% | 2000% | 600 | 203 | 4860 | 32 | 10800/125 | 4320/50   | 8  |

<sup>1</sup> B1ls jest obsługiwany tylko w systemie Linux

## <a name="workload-example"></a>Przykład obciążenia

Weź pod uwagę aplikację zaewidencjonowania/wyewidencjonowania pakietu Office. Aplikacja wymaga obciążeń procesora CPU w godzinach pracy, ale nie dużej mocy obliczeniowej w godzinach. W tym przykładzie obciążenie wymaga maszyny wirtualnej 16vCPU z 64GiB pamięci RAM do wydajnej pracy.

W tabeli przedstawiono dane dotyczące godzinowego ruchu, a wykres jest wizualną reprezentacją tego ruchu.

Charakterystyki B16:

Maksymalna wydajność procesora: 16vCPU * 100% = 1600%

Linia bazowa: 270%

![Wykres danych ruchu godzinowego](./media/b-series-burstable/office-workload.png)

| Scenariusz | Godzina | Użycie procesora CPU (%) | Środki skumulowane<sup>1</sup> | Dostępne kredyty |
| --- | --- | --- | --- | --- |
| Wdrożenie B16ms | Wdrożenie | Wdrożenie  | 480 (końcowe środki) | 480 |
| Brak ruchu | 0:00 | 0 | 162 | 642 |
| Brak ruchu | 1:00 | 0 | 162 | 804 |
| Brak ruchu | 2:00 | 0 | 162 | 966 |
| Brak ruchu | 3:00 | 0 | 162 | 1128 |
| Brak ruchu | 4:00 | 0 | 162 | 1290 |
| Brak ruchu | 5:00 | 0 | 162 | 1452 |
| Niski ruch | 6:00 | 270 | 0 | 1452 |
| Pracownicy korzystają z pakietu Office (zapotrzebowanie aplikacji 80% vCPU) | 7:00 | 1280 | -606 | 846 |
| Pracownicy kontynuują pracę z pakietem Office (zapotrzebowanie aplikacji 80% vCPU) | 8:00 | 1280 | -606 | 240 |
| Niski ruch | 9:00 | 270 | 0 | 240 |
| Niski ruch | 10:00 | 100 | 102 | 342 |
| Niski ruch | 11:00 | 50 | 132 | 474 |
| Niski ruch | 12:00 | 100 | 102 | 576 |
| Niski ruch | 13:00 | 100 | 102 | 678 |
| Niski ruch | 14:00 | 50 | 132 | 810 |
| Niski ruch | 15:00 | 100 | 102 | 912 |
| Niski ruch | 16:00 | 100 | 102 | 1014 |
| Wyewidencjonowanie pracowników (zapotrzebowanie aplikacji 100% vCPU) | 17:00 | 1600 | -798 | 216 |
| Niski ruch | 18:00 | 270 | 0 | 216 |
| Niski ruch | 19:00 | 270 | 0 | 216 |
| Niski ruch | 20:00 | 50 | 132 | 348 |
| Niski ruch | 21:00 | 50 | 132 | 480 |
| Brak ruchu | 22:00 | 0 | 162 | 642 |
| Brak ruchu | 23:00 | 0 | 162 | 804 |

<sup>1</sup> zakumulowane kredyty/kredyty używane w ciągu godziny są równoważne: `((Base CPU perf of VM - CPU Usage) / 100) * 60 minutes` .  

Dla D16s_v3, która ma 16 procesorów wirtualnych vCPU i 64 GiB pamięci, stawka godzinowa wynosi $0,936 za godzinę (miesięcznie $673,92) i dla B16ms z 16 procesorów wirtualnych vCPU i 64 $0,794 GiB <b> Spowoduje to 15% oszczędności!</b>

## <a name="q--a"></a>Pytania i odpowiedzi

### <a name="q-what-happens-when-my-credits-run-out"></a>P: co się stanie po wyczerpaniu środków?
Odp **.: gdy** środki są wyczerpane, maszyna wirtualna wraca do wydajności linii bazowej.

### <a name="q-how-do-you-get-135-baseline-performance-from-a-vm"></a>P: jak uzyskać wydajność bazową 135% z maszyny wirtualnej?

Odp **.: 135**% jest współużytkowany przez 8 vCPU, które tworzą rozmiar maszyny wirtualnej. Jeśli na przykład aplikacja korzysta z 4 rdzeni w ramach przetwarzania wsadowego, a każda z tych 4 vCPU jest uruchomiona o 30%, wykorzystanie całkowitej wydajności procesora maszyn wirtualnych będzie równe 120%.  Oznacza to, że maszyna wirtualna będzie kompilować czas kredytowy na podstawie przyrostu 15% od wydajności linii bazowej.  Oznacza to również, że gdy masz dostępne środki na korzystanie z tej samej maszyny wirtualnej, można użyć 100% wszystkich 8 vCPU, co zapewnia, że maszyna wirtualna ma maksymalną wydajność procesora CPU równą 800%.

### <a name="q-how-can-i-monitor-my-credit-balance-and-consumption"></a>P: Jak mogę monitorować saldo środków i zużycie?

Odp.: Metryka **kredytowa** umożliwia wyświetlenie liczby **środków, które** zostały zgłoszone przez maszynę wirtualną, a Metryka **CONSUMEDCREDIT** pokazuje, ile kredytów CPU zużywał przez maszynę wirtualną od banku.    Te metryki będą widoczne w okienku metryki w portalu lub programowo za pośrednictwem interfejsów API Azure Monitor.

Aby uzyskać więcej informacji na temat uzyskiwania dostępu do danych metryk dla platformy Azure, zobacz [Omówienie metryk w Microsoft Azure](../azure-monitor/platform/data-platform.md).

### <a name="q-how-are-credits-accumulated-and-consumed"></a>P: w jaki sposób są sumowane i zużywane środki?

Odp **.: opłaty za gromadzenie** i zużycie maszyn wirtualnych są ustawiane w taki sposób, że maszyna wirtualna działająca na tym samym poziomie wydajności nie będzie mogła ani korzystać z obciążeń sieciowych.  Na maszynie wirtualnej zostanie osiągnięty wzrost środków w wysokości netto, gdy jest ona niższa od bazowego poziomu wydajności i będzie miała spadek netto środków, gdy maszyna wirtualna korzysta z procesora CPU więcej niż jego podstawowy poziom wydajności.

**Przykład**: WDRAŻAm maszynę wirtualną przy użyciu B1ms rozmiaru dla aplikacji bazy danych o małym czasie i obecności. Ten rozmiar umożliwia mojej aplikacji używanie do 20% vCPU jako mojego planu bazowego, czyli 0,2 kredytów na minutę, których można użyć lub banku.

Moja aplikacja jest zajęta na początku i na końcu dnia roboczego pracowników, między 7:00-9:00 AM i 4:00-6:13:00. W pozostałych 20 godzinach dnia moja aplikacja jest zwykle w stanie bezczynności, przy użyciu 10% vCPU. W przypadku godzin poza szczytem otrzymuję 0,2 kredytów na minutę, ale zużywamy tylko 0,1 kredytów na minutę, więc moja maszyna wirtualna będzie bankiem 0,1 x 60 = 6 kredytów na godzinę.  W przypadku 20 godzin, które są poza szczytem, będę mieć Bank 120 kredytów.  

W godzinach szczytu moja aplikacja jest 60 średnia na vCPU użycie, nadal uzyskuję 0,2 kredytów na minutę, ale zużywamy 0,6 kredyty na minutę, za koszt netto usługi 0,4 na minutę lub 0,4 x 60 = 24 kredyty na godzinę. Mam 4 godziny na dzień szczytowego użycia, więc koszty IT to 4 x 24 = 96 kredytów dla mojego szczytowego użycia.

Jeśli przyniesiem do 120 kredytów, które zostały uzyskane poza szczytem, i odjęcie kredytów 96, które były używane w czasie szczytu, I banku można użyć dodatkowych 24 kredytów dziennie, które mogę wykorzystać w przypadku innych serii działań.

### <a name="q-how-can-i-calculate-credits-accumulated-and-used"></a>P: jak można obliczyć kwoty i użycie kredytów?

Odp **.: można** użyć następującej formuły:

(Podstawowa wydajność procesora CPU w przypadku użycia procesora CPU)/100 = kredyty lub użycie na minutę

na przykład w powyższym wystąpieniu linia bazowa jest 20%, a jeśli używasz 10% obciążenia procesora (20%-10%)/100 = 0,1 kredytu na minutę.

### <a name="q-does-the-b-series-support-premium-storage-data-disks"></a>P: czy Seria B obsługuje Premium Storage dysków z danymi?

Odp **.: tak**, wszystkie rozmiary serii B obsługują Premium Storage dysków z danymi.

### <a name="q-why-is-my-remaining-credit-set-to-0-after-a-redeploy-or-a-stopstart"></a>P: Dlaczego pozostałe środki mają ustawioną wartość 0 po ponownym wdrożeniu lub zatrzymaniu/rozpoczęciu?

Odp **.: po** ponownym WDROŻENIu maszyny wirtualnej, gdy maszyna wirtualna zostanie przeniesiona do innego węzła, Zakumulowany kredyt zostanie utracony. Jeśli maszyna wirtualna jest zatrzymana/uruchomiona, ale pozostaje w tym samym węźle, maszyna wirtualna zachowuje skumulowany kredyt. Za każdym razem, gdy maszyna wirtualna zacznie być odświeżana w węźle, otrzymuje początkowe środki na Standard_B8ms jest 240.

### <a name="q-what-happens-if-i-deploy-an-unsupported-os-image-on-b1ls"></a>P: co się stanie w przypadku wdrożenia nieobsługiwanego obrazu systemu operacyjnego w systemie B1ls?

Odp **.: B1ls** obsługuje tylko obrazy systemu Linux i po wdrożeniu innego obrazu z systemem operacyjnego można nie uzyskać najlepszego środowiska klienta.

## <a name="other-sizes-and-information"></a>Inne rozmiary i informacje

- [Ogólnego przeznaczenia](sizes-general.md)
- [Optymalizacja pod kątem obliczeń](sizes-compute.md)
- [Optymalizacja pod kątem pamięci](sizes-memory.md)
- [Optymalizacja pod kątem magazynu](sizes-storage.md)
- [Optymalizacja pod kątem procesora GPU](sizes-gpu.md)
- [Obliczenia o wysokiej wydajności](sizes-hpc.md)

Kalkulator cen: [Kalkulator cen](https://azure.microsoft.com/pricing/calculator/)

Więcej informacji na temat typów dysków: [typy dysków](./disks-types.md#ultra-disk)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o tym, jak [usługa Azure COMPUTE units (ACU)](acu.md) może pomóc w porównaniu wydajności obliczeniowej w ramach jednostek SKU platformy Azure.
