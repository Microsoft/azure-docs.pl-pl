---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/30/2019
ms.author: zivr
ms.custom: include file
ms.openlocfilehash: e2be62180907e94401548774b3403db0f36caca3
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96488475"
---
Umieszczanie maszyn wirtualnych w jednym regionie zmniejsza odległość fizyczną między wystąpieniami. Umieszczenie ich w ramach pojedynczej strefy dostępności spowoduje również, że zostaną one fizycznie bliżej siebie. Jednak w miarę zwiększania się rozmiaru platformy Azure jedna strefa dostępności może obejmować wiele fizycznych centrów danych, co może skutkować opóźnieniami sieciowymi wpływającymi na aplikację. 

Aby zapewnić, że maszyny wirtualne będą możliwie jak najbliżej, osiągając najniższe możliwe opóźnienie, należy wdrożyć je w obrębie grupy umieszczania sąsiedztwa.

Grupa umieszczania bliskości jest grupą logiczną używaną w celu upewnienia się, że zasoby obliczeniowe platformy Azure znajdują się fizycznie blisko siebie. Grupy umieszczania zbliżeniowe są przydatne w przypadku obciążeń, w których jest wymagane małe opóźnienia.


- Małe opóźnienia między samodzielnymi maszynami wirtualnymi.
- Małe opóźnienia między maszynami wirtualnymi w jednym zestawie dostępności lub zestawem skalowania maszyn wirtualnych. 
- Małe opóźnienia między samodzielnymi maszynami wirtualnymi, maszynami wirtualnymi w wielu zestawach dostępności lub wieloma zestawami skalowania. W jednej grupie umieszczania można korzystać z wielu zasobów obliczeniowych w celu zebrania aplikacji wielowarstwowej. 
- Małe opóźnienia między wieloma warstwami aplikacji przy użyciu różnych typów sprzętu. Na przykład uruchomienie zaplecza przy użyciu serii M w zestawie dostępności i frontonu w wystąpieniu serii D w zestawie skalowania w pojedynczej grupie umieszczania sąsiedztwa.


![Ilustracja przedstawiająca grupy położenia zbliżeniowe](./media/virtual-machines-common-ppg/ppg.png)

## <a name="using-proximity-placement-groups"></a>Używanie grup umieszczania w sąsiedztwie 

Grupa umieszczania bliskości jest nowym typem zasobów na platformie Azure. Należy utworzyć je przed użyciem z innymi zasobami. Po utworzeniu można go używać z maszynami wirtualnymi, zestawami dostępności lub zestawami skalowania maszyn wirtualnych. Należy określić grupę umieszczania bliskości podczas tworzenia zasobów obliczeniowych, podając identyfikator grupy umieszczania sąsiedztwa. 

Możesz również przenieść istniejący zasób do grupy umieszczania sąsiedztwa. Podczas przenoszenia zasobu do grupy umieszczania bliskości należy najpierw zatrzymać (cofnąć przydział) element zawartości, ponieważ zostanie on ponownie wdrożony w innym centrum danych w regionie w celu spełnienia ograniczenia wspólnej lokalizacji. 

W przypadku zestawów dostępności i zestawów skalowania maszyn wirtualnych należy ustawić grupę umieszczania sąsiedztwa na poziomie zasobu, a nie na poszczególnych maszynach wirtualnych. 

Grupa położenia sąsiedztwa to ograniczenie między lokalizacjami, a nie mechanizm przypinania. Jest przypięty do określonego centrum danych z wdrożeniem pierwszego zasobu, aby go użyć. Po zatrzymaniu lub usunięciu wszystkich zasobów przy użyciu grupy umieszczania w sąsiedztwie nie jest już przypięty. W związku z tym podczas korzystania z grupy umieszczania bliskości z wieloma seriami maszyn wirtualnych ważne jest, aby określić wszystkie wymagane typy z góry w szablonie, gdy jest to możliwe, lub postępować zgodnie z sekwencją wdrożenia, która poprawi szansę na pomyślne wdrożenie. Jeśli wdrożenie nie powiedzie się, uruchom ponownie wdrożenie z rozmiarem maszyny wirtualnej, który nie powiódł się, jako pierwszy rozmiar do wdrożenia.

## <a name="what-to-expect-when-using-proximity-placement-groups"></a>Oczekiwane użycie grup umieszczania w sąsiedztwie 
Grupy umieszczania w sąsiedztwie oferują wspólną lokalizację w tym samym centrum danych. Jednak ponieważ grupy umieszczania w sąsiedztwie reprezentują dodatkowe ograniczenie wdrażania, mogą wystąpić błędy alokacji. Istnieje kilka przypadków użycia, w których podczas korzystania z grup umieszczania w sąsiedztwie można zobaczyć błędy alokacji:

- Po poproszeniu pierwszej maszyny wirtualnej w grupie umieszczania sąsiedztwa centrum danych jest automatycznie wybierane. W niektórych przypadkach drugie żądanie dotyczące innej jednostki SKU maszyny wirtualnej może się nie powieść, jeśli nie istnieje w tym centrum danych. W takim przypadku zwracany jest błąd **OverconstrainedAllocationRequest** . Aby tego uniknąć, spróbuj zmienić kolejność wdrażania jednostek SKU lub mieć wdrożone oba zasoby przy użyciu jednego szablonu ARM.
-   W przypadku obciążeń elastycznych, w których Dodawanie i usuwanie wystąpień maszyn wirtualnych, które mają ograniczenie grupy umieszczania bliskości w danym wdrożeniu, może spowodować niepowodzenie spełnienia żądania w wyniku błędu **AllocationFailure** . 
- Zatrzymywanie (cofanie alokacji) i uruchamianie maszyn wirtualnych zgodnie z potrzebami jest innym sposobem osiągnięcia elastyczności. Ponieważ pojemność nie jest zachowywana po zatrzymaniu (cofnięciu alokacji) maszyny wirtualnej, uruchomienie jej ponownie może spowodować wystąpienie błędu **AllocationFailure** .

## <a name="planned-maintenance-and-proximity-placement-groups"></a>Planowana konserwacja i grupy umieszczania w sąsiedztwie

Zdarzenia planowanej konserwacji, takie jak likwidowanie sprzętu w centrum danych platformy Azure, mogą mieć wpływ na wyrównanie zasobów w grupach umieszczania sąsiedztwa. Zasoby mogą być przenoszone do innego centrum danych, zakłócając oczekiwania i oczekiwania na opóźnienia związane z grupą położenia sąsiedztwa.

### <a name="check-the-alignment-status"></a>Sprawdź stan wyrównania

Aby sprawdzić stan wyrównania dla grup umieszczania sąsiedztwa, można wykonać następujące czynności.


- Stan wspólnej lokalizacji grupy umieszczania sąsiedztwa można wyświetlić przy użyciu portalu, interfejsu wiersza polecenia i programu PowerShell.

    -   W przypadku korzystania z programu PowerShell stan wspólnej lokalizacji można uzyskać za pomocą polecenia cmdlet Get-AzProximityPlacementGroup przez dołączenie opcjonalnego parametru "-ColocationStatus".

    -   W przypadku korzystania z interfejsu wiersza polecenia stan wspólnej lokalizacji można uzyskać za pomocą polecenia, `az ppg show` dołączając opcjonalny parametr "--include-sublocation-status".

- Dla każdej grupy położenia zbliżeniowe Właściwość **stanu wspólnej lokalizacji** zawiera podsumowanie bieżącego stanu wyrównania pogrupowanych zasobów. 

    - **Wyrównany**: zasób znajduje się w tym samym przedziale czasu oczekiwania dla grupy umieszczania sąsiedztwa.

    - **Nieznany**: cofnięto przydział co najmniej jednej z zasobów maszyny wirtualnej. Po pomyślnym uruchomieniu tych stanów należy wrócić do pozycji **wyrównany**.

    - **Niewyrównane**: co najmniej jeden zasób maszyny wirtualnej nie jest wyrównany do grupy umieszczania sąsiedztwa. Określone zasoby, które nie są wyrównane, również zostaną wywołane osobno w sekcji członkostwo

- W przypadku zestawów dostępności można wyświetlić informacje o wyrównaniu poszczególnych maszyn wirtualnych na stronie Przegląd zestawu dostępności.

- W przypadku zestawów skalowania informacje o wyrównaniu poszczególnych wystąpień można zobaczyć na karcie **wystąpienia** na stronie **Przegląd** zestawu skalowania. 


### <a name="re-align-resources"></a>Ponowne wyrównywanie zasobów 

Jeśli grupa umieszczania jest bliska `Not Aligned` , można stop\deallocate, a następnie ponownie uruchomić zasoby, których to dotyczy. Jeśli maszyna wirtualna znajduje się w zestawie dostępności lub zestawu skalowania, przed ponownym uruchomieniem programu należy najpierw stopped\deallocated wszystkie maszyny wirtualne w zestawie dostępności lub w zestawie skalowania.

W przypadku niepowodzenia alokacji ze względu na ograniczenia wdrożenia może być konieczne stop\deallocate wszystkich zasobów w grupie umieszczania, w których dotyczy problem, a następnie ich ponowne uruchomienie w celu przywrócenia wyrównania.

## <a name="best-practices"></a>Najlepsze rozwiązania 
- W przypadku najmniejszego opóźnienia należy używać grup umieszczania sąsiedztwa wraz z przyspieszoną siecią. Aby uzyskać więcej informacji, zobacz [Tworzenie maszyny wirtualnej z systemem Linux przy użyciu przyspieszonej sieci](../articles/virtual-network/create-vm-accelerated-networking-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) lub [Tworzenie maszyny wirtualnej z systemem Windows przy użyciu przyspieszonej sieci](../articles/virtual-network/create-vm-accelerated-networking-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- Wdróż wszystkie rozmiary maszyn wirtualnych w jednym szablonie. Aby uniknąć lądowania na sprzęcie, który nie obsługuje wszystkich potrzebnych jednostek SKU i rozmiarów maszyn wirtualnych, należy uwzględnić wszystkie warstwy aplikacji w jednym szablonie, tak aby wszystkie te aplikacje były wdrażane w tym samym czasie.
- Jeśli tworzysz skrypty do wdrożenia przy użyciu programu PowerShell, interfejsu wiersza polecenia lub zestawu SDK, może wystąpić błąd alokacji `OverconstrainedAllocationRequest` . W takim przypadku należy zatrzymać/cofnąć przydział wszystkich istniejących maszyn wirtualnych i zmienić sekwencję w skrypcie wdrażania, aby rozpocząć od jednostki SKU/rozmiaru maszyny wirtualnej, która nie powiodła się. 
- Podczas ponownie korzystania z istniejącej grupy umieszczania, z której usunięto maszyny wirtualne, poczekaj na pełne zakończenie operacji usuwania przed dodaniem do niej maszyn wirtualnych.
- Jeśli opóźnienie jest pierwszym priorytetem, umieść maszyny wirtualne w grupie umieszczania bliskości i całe rozwiązanie w strefie dostępności. Ale jeśli odporność ma najwyższy priorytet, rozłożenie wystąpień w wielu strefach dostępności (pojedyncza Grupa położenia sąsiedztwa nie może obejmować stref).