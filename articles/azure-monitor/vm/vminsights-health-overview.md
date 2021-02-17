---
title: Azure Monitor dla maszyn wirtualnych kondycja gościa (wersja zapoznawcza)
description: Omówienie funkcji kondycji w Azure Monitor dla maszyn wirtualnych, w tym sposób wyświetlania kondycji maszyn wirtualnych i otrzymywania alertów w przypadku nieprawidłowej kondycji maszyny wirtualnej.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/27/2020
ms.openlocfilehash: 96bed9f3b04e54e2e9a5234d78f9a2660126742e
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100614893"
---
# <a name="azure-monitor-for-vms-guest-health-preview"></a>Azure Monitor dla maszyn wirtualnych kondycja gościa (wersja zapoznawcza)
Azure Monitor dla maszyn wirtualnych kondycja gościa umożliwia wyświetlanie kondycji maszyn wirtualnych w oparciu o zestaw pomiarów wydajności, które są próbkowane w regularnych odstępach czasu od systemu operacyjnego gościa. Można szybko sprawdzać kondycję wszystkich maszyn wirtualnych w ramach subskrypcji lub grupy zasobów, przełączać się do szczegółowej kondycji konkretnej maszyny wirtualnej lub aktywnie powiadamiać o nieprawidłowym stanie maszyny wirtualnej. 

## <a name="enable-virtual-machine-health"></a>Włącz kondycję maszyny wirtualnej
Aby uzyskać szczegółowe informacje na temat włączania funkcji kondycji gościa i dołączania maszyn wirtualnych, zobacz [włączanie Azure monitor dla maszyn wirtualnych kondycji gościa (wersja zapoznawcza)](vminsights-health-enable.md) .

## <a name="pricing"></a>Cennik
Nie ma bezpośredniego kosztu dla funkcji kondycji gościa, ale koszt pozyskiwania i przechowywania danych stanu kondycji jest dostępny w obszarze roboczym Log Analytics. Wszystkie dane są przechowywane w tabeli *HealthStateChangeEvent* . Aby uzyskać szczegółowe informacje dotyczące modeli cenowych i kosztów, zobacz temat [Zarządzanie użyciem i kosztami za pomocą dzienników Azure monitor](../platform/manage-cost-storage.md) .

## <a name="view-virtual-machine-health"></a>Wyświetl kondycję maszyny wirtualnej
Kolumna **kondycja maszyny wirtualnej gościa** na stronie **wprowadzenie** umożliwia szybki wgląd w kondycję każdej maszyny wirtualnej w określonej subskrypcji lub grupie zasobów. Bieżąca kondycja każdej maszyny wirtualnej jest wyświetlana, gdy ikony dla każdej grupy pokazują liczbę maszyn wirtualnych aktualnie w poszczególnych stanach w tej grupie.

[![Strona wprowadzenie z kondycją maszyny wirtualnej gościa](media/vminsights-health-overview/get-started-page.png)](media/vminsights-health-overview/get-started-page.png#lightbox)


## <a name="monitors"></a>Monitory
Kliknij stan kondycji maszyny wirtualnej, aby wyświetlić szczegółowy stan poszczególnych monitorów. Każdy monitor mierzy kondycję określonego składnika. Ogólna kondycja maszyny wirtualnej jest określana na podstawie kondycji poszczególnych monitorów. 

![Przykład monitorów](media/vminsights-health-overview/monitors.png)

W poniższej tabeli wymieniono monitory zagregowane i jednostkowe aktualnie dostępne dla każdej maszyny wirtualnej. 

| Monitor | Typ | Opis |
|:---|:---|:---|
| Wykorzystanie procesora | Jednostka | Procent użycia procesora. |
| Systemy plików | Agregacja | Zagregowana kondycja wszystkich systemów plików na maszynie wirtualnej z systemem Linux. |
| System plików  | Agregacja | Kondycja każdego pojedynczego systemu plików na maszynie wirtualnej z systemem Linux. Nazwa monitora to nazwa systemu plików. |
| Wolne miejsce | Jednostka | Procent wolnego miejsca w systemie plików. |
| Dyski logiczne | Agregacja | Agregowanie kondycji wszystkich dysków logicznych w maszynie wirtualnej z systemem Windows. |
| Dysk logiczny  | Agregacja | Kondycja każdego pojedynczego dysku logicznego w maszynie wirtualnej z systemem Windows. Nazwa monitora to nazwa dysku. |
| Pamięć | Agregacja | Zagregowana kondycja pamięci na maszynie wirtualnej. |
| Dostępna pamięć | Jednostka | Dostępne megabajty na maszynie wirtualnej. |

## <a name="health-states"></a>Stany kondycji
Każdy monitor pobiera wartości co minutę i porównuje wartości próbkowane z kryteriami dla poszczególnych stanów kondycji. Jeśli kryteria dla określonego stanu mają wartość PRAWDA, monitor jest ustawiany na ten stan. Jeśli żaden z kryteriów nie ma wartości true, Monitor zostanie ustawiony w dobrej kondycji. Dane są wysyłane z agenta do Azure Monitor co trzy minuty lub natychmiast, jeśli istnieje zmiana stanu.

Każdy monitor ma okno lookback i analizuje wszystkie próbki zebrane w tym czasie. Na przykład monitor może mieć okno lookback o 240 sekund szukające maksymalnej wartości z co najmniej 2 wartości próbkowanych, ale nie więcej niż w ciągu ostatnich 3. Podczas gdy wartości są próbkowane w regularnych odstępach czasu, liczba próbkowana w konkretnym oknie może się nieco różnić w przypadku wystąpienia przerwy w operacji agenta.

Każdy z tych monitorów ma potencjalne Stany kondycji w poniższej tabeli i będzie tylko jeden w danym momencie. Po zainicjowaniu monitora jest on uruchamiany w dobrej kondycji.

| Stan kondycji | Opis |
|:---|:---|
| Dobra kondycja  | Monitor nie przekracza obecnie progu ostrzegawczego ani krytycznego. |
| Ostrzeżenie  | Monitor przekroczył próg ostrzegawczy (jeśli został zdefiniowany). |
| Krytyczne | Monitor przekroczył próg krytyczny (jeśli został zdefiniowany). |
| Nieznane  | Nie zebrano wystarczającej ilości danych w celu określenia stanu kondycji. |
| Disabled | Monitor jest obecnie wyłączony. |
| Brak     | Monitor jest właśnie uruchamiany i jeszcze nie został oceniony lub monitorowany obiekt już nie istnieje. |



Istnieją dwa typy monitorów, jak pokazano w poniższej tabeli.

| Monitor | Opis |
|:---|:---|
| Monitor jednostkowy | Mierzy pewien aspekt zasobu lub aplikacji. Może to spowodować sprawdzenie licznika wydajności w celu określenia wydajności zasobu lub jego dostępności. |
| Monitor agregacji | Grupuje wiele monitorów, aby zapewnić pojedynczy zagregowany stan kondycji. Monitor agregacji może zawierać co najmniej jeden monitor jednostkowy i inne monitory agregacji. |


  
### <a name="health-rollup-policy"></a>Zasady dotyczące kondycji
Stan kondycji maszyny wirtualnej jest określany przez zestawienie kondycji z każdej jednostki i monitorów agregujących. Każdy monitor zagregowany używa zasad rzutowania kondycji najgorszego stanu, w których stan monitora zagregowanego jest zgodny ze stanem monitora podrzędnego o najgorszym stanie kondycji.  

W poniższym przykładzie monitor **wolnego miejsca** jest w stanie krytycznym, który ma role do agregacji dla jego wystąpienia, a następnie do **systemów plików**. Mimo że **użycie procesora CPU** w programie w stanie ostrzeżenie, maszyna wirtualna jest ustawiona na wartość krytyczna, ponieważ jest to najgorszy stan poniżej. Jeśli ilość wolnego miejsca powróci do stanu dobrej kondycji, maszyna wirtualna zmieni stan na ostrzegawczy, ponieważ użycie procesora CPU stanie się monitorem najgorszym stanem.

![Przykład rzutowania kondycji](media/vminsights-health-overview/health-rollup-example.png)


## <a name="monitor-details"></a>Szczegóły monitora
Wybierz monitor, aby wyświetlić jego szczegóły, w tym następujące karty.

**Przegląd** zawiera opis monitora, czas ostatniej oceny, a także wartości, aby określić bieżący stan kondycji. Liczba próbek może się różnić w zależności od definicji monitora i lotności wartości.

[![Przegląd szczegółów monitora](media/vminsights-health-overview/monitor-details-overview.png)](media/vminsights-health-overview/monitor-details-overview.png#lightbox)

**Historia** przedstawia historię zmian stanu monitora. Można rozwinąć wszystkie zmiany stanu, aby wyświetlić wartości oceniane w celu określenia stanu kondycji. Kliknij przycisk **Wyświetl konfigurację zastosowana** , aby wyświetlić konfigurację monitora w momencie zmiany stanu kondycji.



[![Historia szczegółów monitora](media/vminsights-health-overview/monitor-details-history.png)](media/vminsights-health-overview/monitor-details-history.png#lightbox)

### <a name="configuration"></a>Konfigurowanie
Wyświetl i zmodyfikuj konfigurację monitora dla wybranej maszyny wirtualnej. Aby uzyskać szczegółowe informacje, zobacz [konfigurowanie monitorowania w Azure monitor dla maszyn wirtualnych kondycji gościa (wersja zapoznawcza)](vminsights-health-enable.md) .

[![Konfiguracja szczegółów monitora](media/vminsights-health-overview/monitor-details-configuration.png)](media/vminsights-health-overview/monitor-details-configuration.png#lightbox)




## <a name="next-steps"></a>Następne kroki

- [Włączaj kondycję gościa w Azure Monitor dla maszyn wirtualnych i dołączaj agentów.](vminsights-health-enable.md)
- [Skonfiguruj monitory przy użyciu Azure Portal.](vminsights-health-configure.md)
- [Skonfiguruj monitory przy użyciu reguł zbierania danych.](vminsights-health-configure-dcr.md)