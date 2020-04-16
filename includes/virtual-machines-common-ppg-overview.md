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
ms.openlocfilehash: fb2eb2d237a1245627bbdb6f4f2eacbb9966a2c6
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81421961"
---
Umieszczanie maszyn wirtualnych w jednym regionie zmniejsza fizyczną odległość między wystąpieniami. Umieszczenie ich w jednej strefie dostępności również zbliży je fizycznie do siebie. Jednak wraz ze wzrostem śladu platformy Azure pojedyncza strefa dostępności może obejmować wiele fizycznych centrów danych, co może spowodować opóźnienie sieciowe wpływające na aplikację. 

Aby maszyny wirtualne były jak najbliżej, osiągając najniższe możliwe opóźnienie, należy wdrożyć je w grupie miejsc docelowych w pobliżu.

Grupa miejsc docelowych zbliżeniowych jest logicznym grupowaniem używanym w celu upewnienia się, że zasoby obliczeniowe platformy Azure są fizycznie zlokalizowane blisko siebie. Grupy miejsc docelowych zbliżeniowych są przydatne w przypadku obciążeń, w których wymagane jest małe opóźnienia.


- Małe opóźnienia między autonomicznymi maszynami wirtualnymi.
- Małe opóźnienia między maszynami wirtualnymi w jednym zestawie dostępności lub w zestawie skalowania maszyny wirtualnej. 
- Małe opóźnienia między autonomicznymi maszynami wirtualnymi, maszynami wirtualnymi w wielu zestawach dostępności lub wieloma zestawami skalowania. Można mieć wiele zasobów obliczeniowych w jednej grupie umieszczania, aby połączyć wielowarstwową aplikację. 
- Małe opóźnienia między wieloma warstwami aplikacji przy użyciu różnych typów sprzętu. Na przykład uruchamianie wewnętrznej bazy danych przy użyciu serii M w zestawie dostępności i frontonia w wystąpieniu serii D, w zestawie skalowania, w jednej grupie miejsc docelowych zbliżeniowych.


![Grafika dla grup miejsc docelowych zbliżeniowych](./media/virtual-machines-common-ppg/ppg.png)

## <a name="using-proximity-placement-groups"></a>Korzystanie z grup miejsc docelowych zbliżeniowych 

Grupa miejsc docelowych zbliżeniowych to nowy typ zasobu na platformie Azure. Musisz utworzyć jeden przed użyciem go z innymi zasobami. Po utworzeniu może być używany z maszynami wirtualnymi, zestawami dostępności lub zestawami skalowania maszyny wirtualnej. Podczas tworzenia zasobów obliczeniowych należy określić grupę miejsc docelowych zbliżeniową, podając identyfikator grupy miejsc docelowych zbliżeniowo. 

Istniejący zasób można również przenieść do grupy miejsc docelowych zbliżeniowych. Podczas przenoszenia zasobu do grupy miejsc docelowych zbliżeniowych należy najpierw zatrzymać (przydzielić) zasób, ponieważ zostanie on ponownie wprowadzony do innego centrum danych w regionie, aby spełnić ograniczenie kolokacji. 

W przypadku zestawów dostępności i zestawów skalowania maszyny wirtualnej należy ustawić grupę miejsc docelowych zbliżeniowych na poziomie zasobu, a nie na poszczególnych maszynach wirtualnych. 

Grupa miejsc docelowych zbliżeniowych jest ograniczeniem kolokacji, a nie mechanizmem przypinania. Jest przypięty do określonego centrum danych z wdrożeniem pierwszego zasobu, aby go użyć. Po zatrzymaniu lub usunięciu wszystkich zasobów korzystających z grupy miejsc docelowych w pobliżu nie zostaną ony przypięte. W związku z tym podczas korzystania z grupy miejsc docelowych zbliżeniami z wielu serii maszyn wirtualnych, ważne jest, aby określić wszystkie wymagane typy z góry w szablonie, gdy jest to możliwe lub wykonaj sekwencję wdrażania, które zwiększą swoje szanse na pomyślne wdrożenie. Jeśli wdrożenie nie powiedzie się, uruchom ponownie wdrożenie z rozmiarem maszyny Wirtualnej, który nie powiódł się jako pierwszy rozmiar do wdrożenia.

## <a name="what-to-expect-when-using-proximity-placement-groups"></a>Czego można się spodziewać podczas korzystania z grup miejsc docelowych zbliżeniowych 
Grupy miejsc docelowych zbliżeniowych oferują kolokowanie w tym samym centrum danych. Jednak ponieważ grupy miejsc docelowych zbliżeniowych stanowią dodatkowe ograniczenie wdrażania, mogą wystąpić błędy alokacji. Istnieje kilka przypadków użycia, w których podczas korzystania z grup miejsc docelowych zbliżeniowych mogą wystąpić błędy alokacji:

- Gdy poprosisz o pierwszą maszynę wirtualną w grupie miejsc docelowych zbliżeniowych, centrum danych jest automatycznie wybierane. W niektórych przypadkach drugie żądanie dla innej jednostki SKU maszyny wirtualnej może zakończyć się niepowodzeniem, jeśli nie istnieje w tym centrum danych. W takim przypadku zwracany jest błąd **OverconstrainedAllocationRequest.** Aby tego uniknąć, spróbuj zmienić kolejność wdrażania jednostek SKU lub wdrożyć oba zasoby przy użyciu jednego szablonu ARM.
-   W przypadku obciążeń elastycznych, gdzie można dodać i usunąć wystąpienia maszyn wirtualnych, o ograniczenia grupy umieszczania w pobliżu na wdrożenie może spowodować niepowodzenie spełnienia żądania powodujące błąd **AlokacjaFailure.** 
- Zatrzymanie (cofnięta alokacja) i uruchomienie maszyn wirtualnych w razie potrzeby to inny sposób na osiągnięcie elastyczności. Ponieważ pojemność nie jest zachowywana po zatrzymaniu (cofnięto alokację) maszyny Wirtualnej, uruchomienie go ponownie może spowodować błąd **AllocationFailure.**


## <a name="best-practices"></a>Najlepsze rozwiązania 
- W przypadku najniższego opóźnienia należy używać grup miejsc docelowych zbliżeniowych wraz z przyspieszoną siecią. Aby uzyskać więcej informacji, zobacz [Tworzenie maszyny wirtualnej systemu Linux z przyspieszoną siecią](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) lub [Tworzenie maszyny wirtualnej systemu Windows z przyspieszoną siecią](/azure/virtual-network/create-vm-accelerated-networking-powershell?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- Wdrażanie wszystkich rozmiarów maszyn wirtualnych w jednym szablonie. Aby uniknąć lądowania na sprzęcie, który nie obsługuje wszystkich jednostek SKU maszyn wirtualnych i rozmiarów, które są wymagane, należy uwzględnić wszystkie warstwy aplikacji w jednym szablonie, tak aby wszystkie zostaną wdrożone w tym samym czasie.
- W przypadku skryptów wdrażania przy użyciu programu PowerShell, interfejsu wiersza `OverconstrainedAllocationRequest`polecenia lub modułu SDK może zostać wyświetlony błąd alokacji. W takim przypadku należy zatrzymać/zmienić alokację wszystkich istniejących maszyn wirtualnych i zmienić sekwencję w skrypcie wdrażania, aby rozpocząć od jednostki SKU/rozmiary maszyny Wirtualnej, która nie powiodła się. 
- Podczas ponownego użytku istniejącej grupy miejsc docelowych, z której maszyny wirtualne zostały usunięte, poczekaj na całkowite usunięcie przed dodaniem do niej maszyn wirtualnych.
- Jeśli opóźnienie jest twoim priorytetem, umieść maszyny wirtualne w grupie miejsc docelowych zbliżeniami i całe rozwiązanie w strefie dostępności. Jeśli jednak priorytetem jest odporność, rozłóż wystąpienia na wiele stref dostępności (pojedyncza grupa miejsc docelowych zbliżeniowych nie może obejmować stref).
