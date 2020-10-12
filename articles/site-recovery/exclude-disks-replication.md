---
title: Wyklucz dyski z replikacji za pomocą Azure Site Recovery
description: Jak wykluczać dyski z replikacji na platformę Azure przy użyciu Azure Site Recovery.
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: 15989fbfd65f758eb777c5170c217aba8707e0be
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91333668"
---
# <a name="exclude-disks-from-disaster-recovery"></a>Wyklucz dyski z odzyskiwania po awarii

W tym artykule opisano, jak wykluczać dyski z replikacji podczas odzyskiwania po awarii z lokalizacji lokalnej na platformę Azure za pomocą [Azure Site Recovery](site-recovery-overview.md). Dyski można wykluczać z replikacji z kilku powodów:

- Oznacza to, że nieważne dane zmienione na wykluczonym dysku nie są replikowane.
- W celu optymalizacji zużywanej przepustowości replikacji lub zasobów po stronie docelowej.
- Aby zaoszczędzić magazyn i zasoby sieciowe przez nie replikowanie niepotrzebnych danych.
- Maszyny wirtualne platformy Azure osiągnęły Site Recovery limity replikacji.


## <a name="supported-scenarios"></a>Obsługiwane scenariusze

Dyski można wykluczać z replikacji, co zostało podsumowane w tabeli.

**Azure–Azure** | **Z programu VMware do platformy Azure** | **Z funkcji Hyper-V do platformy Azure** | **Serwer fizyczny do platformy Azure**
--- | --- | --- | ---
Tak | Tak | Tak | Tak

## <a name="exclude-limitations"></a>Wyklucz ograniczenia

**Ograniczenie** | **Maszyny wirtualne platformy Azure** | **Maszyny wirtualne VMware** | **Maszyny wirtualne funkcji Hyper-V**
--- | --- | ---
**Typy dysków** | Dyski podstawowe można wykluczyć z replikacji.<br/><br/> Nie można wykluczać dysków systemu operacyjnego ani dysków dynamicznych. Dyski tymczasowe są domyślnie wykluczone. | Dyski podstawowe można wykluczyć z replikacji.<br/><br/> Nie można wykluczać dysków systemu operacyjnego ani dysków dynamicznych. | Dyski podstawowe można wykluczyć z replikacji.<br/><br/> Nie możesz wykluczać dysków systemu operacyjnego. Nie zalecamy wykluczania dysków dynamicznych. Site Recovery nie może zidentyfikować, która VHS jest podstawowa lub dynamiczna na maszynie wirtualnej gościa. Jeśli wszystkie zależne dyski woluminu dynamicznego nie zostaną wykluczone, chroniony dysk dynamiczny będzie uszkodzonym dyskiem maszyny wirtualnej w trybie failover, a dane na tym dysku są niedostępne.
**Replikowanie dysku** | Nie można wykluczyć dysku, który jest replikowany.<br/><br/> Wyłącz i ponownie Włącz replikację maszyny wirtualnej. |  Nie można wykluczyć dysku, który jest replikowany. |  Nie można wykluczyć dysku, który jest replikowany.
**Usługa mobilności (VMware)** | Nie dotyczy | Dyski można wykluczać tylko na maszynach wirtualnych, na których zainstalowano usługę mobilności.<br/><br/> Oznacza to, że trzeba ręcznie zainstalować usługę mobilności na maszynach wirtualnych, dla których mają zostać wykluczone dyski. Nie można użyć mechanizmu instalacji wypychanej, ponieważ instaluje on usługę mobilności tylko po włączeniu replikacji. | Nie dotyczy.
**Dodaj/Usuń** | Dyski zarządzane można dodać na maszynach wirtualnych platformy Azure z włączoną replikacją z dyskami zarządzanymi. Nie można usunąć dysków na maszynach wirtualnych platformy Azure z włączoną replikacją. | Po włączeniu replikacji nie można dodać ani usunąć dysków. Wyłącz i ponownie Włącz replikację, aby dodać dysk. | Po włączeniu replikacji nie można dodać ani usunąć dysków. Wyłącz i ponownie Włącz replikację.
**Tryb failover** | Jeśli aplikacja wymaga wykluczonego dysku, po przejściu w tryb failover musisz ręcznie utworzyć dysk, aby można było uruchomić zreplikowaną aplikację.<br/><br/> Alternatywnie można utworzyć dysk podczas pracy w trybie failover maszyny wirtualnej, integrując usługę Azure Automation z planem odzyskiwania. | Jeśli wykluczasz dysk, którego potrzebuje aplikacja, utwórz go ręcznie na platformie Azure po przejściu w tryb failover. | Jeśli wykluczasz dysk, którego potrzebuje aplikacja, utwórz go ręcznie na platformie Azure po przejściu w tryb failover.
**Lokalne powrotu po awarii — dyski tworzone ręcznie** | Nie dotyczy | **Maszyny wirtualne z systemem Windows**: dyski utworzone ręcznie na platformie Azure nie są wycofywane z powrotem. Jeśli na przykład przeniesiesz trzy dyski do trybu failover i utworzysz dwa dyski bezpośrednio na maszynie wirtualnej platformy Azure, zostaną przywrócone wszystkie trzy dyski, które przechodzą w tryb failover.<br/><br/> **Maszyny wirtualne z systemem Linux**: dyski utworzone ręcznie na platformie Azure nie powiodły się. Jeśli na przykład przejdziesz do trybu failover trzy dyski i utworzysz dwa dyski na maszynie wirtualnej platformy Azure, wszystkie pięć zakończą się niepowodzeniem. Dysków utworzonych ręcznie nie możesz wykluczyć z powrotu po awarii. | Dyski utworzone ręcznie na platformie Azure nie mają powrotu po awarii. Jeśli na przykład przejdziesz do trybu failover trzy dyski i utworzysz dwa dyski bezpośrednio na maszynie wirtualnej platformy Azure, tylko trzy dyski, które przechodzą w tryb failover, zakończą się niepowodzeniem.
**Lokalne powrotu po awarii — dyski wykluczone** | Nie dotyczy | W przypadku powrotu po awarii do oryginalnej maszyny Konfiguracja dysku maszyny wirtualnej powrotu po awarii nie obejmuje wykluczonych dysków. Dyski wykluczone z replikacji z programu VMware do platformy Azure nie są dostępne na maszynie wirtualnej powrotu po awarii. | W przypadku powrotu po awarii do oryginalnej lokalizacji funkcji Hyper-V Konfiguracja dysku maszyny wirtualnej powrotu po awarii pozostaje taka sama jak w przypadku oryginalnego źródłowego dysku maszyny wirtualnej. Dyski wykluczone z lokacji funkcji Hyper-V do replikacji platformy Azure są dostępne na maszynie wirtualnej powrotu po awarii.



## <a name="typical-scenarios"></a>Typowe scenariusze

Przykłady zmian danych, które są doskonałymi kandydatami do wykluczenia, obejmują zapis do pliku stronicowania (pagefile.sys) i zapisuje w pliku tempdb Microsoft SQL Server. W zależności od obciążenia i podsystemu magazynowania Pliki stronicowania i tempdb mogą rejestrować znaczną ilość zmian. Replikowanie tego typu danych na platformę Azure jest czasochłonne.

- Aby zoptymalizować replikację maszyny wirtualnej z pojedynczym dyskiem wirtualnym zawierającym zarówno system operacyjny, jak i plik stronicowania, można:
    1. Podziel pojedynczy dysk wirtualny na dwa dyski wirtualne. Na jednym dysku wirtualnym umieść system operacyjny, a na drugim — plik stronicowania.
    2. Wyklucz dysk z plikiem stronicowania z replikacji.

- Aby zoptymalizować replikację dysku, który zawiera plik Microsoft SQL Server tempdb i plik systemowej bazy danych, można:
    1. Umieść systemową bazę danych i bazę danych tempdb na dwóch różnych dyskach.
    2. Wyklucz dysk z bazą danych tempdb z replikacji.

## <a name="example-1-exclude-the-sql-server-tempdb-disk"></a>Przykład 1. Wykluczanie dysku bazy danych tempdb programu SQL Server

Przyjrzyjmy się sposobom obsługi wykluczenia dysku, trybu failover i trybu failover dla źródła SQL Server maszyny wirtualnej z systemem Windows-* * bazy danych salesdb * * *, dla którego chcemy wykluczyć bazę danych tempdb. 

### <a name="exclude-disks-from-replication"></a>Wykluczanie dysków z replikacji

Te dyski są dostępne w źródłowej bazy danych salesdb maszyny wirtualnej z systemem Windows.

**Nazwa dysku** | **Dysk systemu operacyjnego gościa** | **Litera dysku** | **Typ danych dysku**
--- | --- | --- | ---
DB-Disk0-OS | Disk0 | C:\ | Dysk systemu operacyjnego.
DB-Disk1| Dysk1 | D:\ | Baza danych systemu SQL i database1 użytkownika.
DB-Disk2 (wykluczono dysk z ochrony) | Dysk2 | E:\ | Pliki temp.
DB-Disk3 (wykluczono dysk z ochrony) | Dysk3 | F:\ | Baza danych SQL tempdb.<br/><br/> Ścieżka folderu — F:\MSSQL\Data\. Zanotuj ścieżkę folderu przed przejściem w tryb failover.
DB-Disk4 | Dysk4 |G:\ | Baza danych użytkownika 2

1. Włączamy replikację dla maszyny wirtualnej bazy danych salesdb.
2. Wykluczamy disk2 i Dysk3 z replikacji, ponieważ zmiany danych na tych dyskach są tymczasowe. 


### <a name="handle-disks-during-failover"></a>Obsługa dysków podczas pracy w trybie failover

Ponieważ dyski nie są replikowane, po przełączeniu w tryb failover na platformie Azure dyski te nie znajdują się na maszynie wirtualnej platformy Azure utworzonej po zakończeniu pracy w trybie awaryjnym Ta tabela zawiera podsumowanie dysków dla maszyny wirtualnej platformy Azure.

**Dysk systemu operacyjnego gościa** | **Litera dysku** | **Typ danych dysku**
--- | --- | ---
Disk0 | C:\ | Dysk systemu operacyjnego.
Dysk1 | E:\ | Magazyn tymczasowy<br/><br/>Platforma Azure dodaje ten dysk. Ponieważ disk2 i Dysk3 zostały wykluczone z replikacji, E: jest pierwszą literą dysku z listy dostępnych. Platforma Azure przypisuje literę E: woluminowi magazynu tymczasowego. Inne litery dysku replikowanych dysków pozostają bez zmian.
Dysk2 | D:\ | Systemowa baza danych SQL i baza danych użytkownika 1
Dysk3 | G:\ | Baza danych użytkownika 2

W naszym przykładzie, ponieważ Dysk3, dysk bazy danych SQL tempdb został wykluczony z replikacji i nie jest dostępny na maszynie wirtualnej platformy Azure, usługa SQL jest w stanie zatrzymania i wymaga ścieżki F:\MSSQL\Data. Tę ścieżkę można utworzyć na kilka sposobów: 

- Dodaj nowy dysk po przejściu w tryb failover i przypisz ścieżkę folderu bazy danych tempdb.
- Użyj istniejącego dysku magazynu tymczasowego na potrzeby ścieżki folderu bazy danych tempdb.

#### <a name="add-a-new-disk-after-failover"></a>Dodawanie nowego dysku po przejściu w tryb failover

1. Przed przełączeniem w tryb failover zanotuj ścieżki plików bazy danych SQL tempdb.mdf i tempdb.ldf.
2. Z Azure Portal Dodaj nowy dysk do maszyny wirtualnej platformy Azure w trybie failover. Dysk powinien mieć taki sam rozmiar (lub większy) jak źródłowy dysk bazy danych SQL tempdb (Dysk3).
3. Zaloguj się do maszyny wirtualnej platformy Azure.
4. Z poziomu konsoli zarządzania dyskami (diskmgmt.msc) zainicjuj i sformatuj nowo dodany dysk.
5. Przypisz tę samą literę dysku, która była używana przez dysk bazy danych SQL tempdb (F:)
6. Utwórz folder bazy danych tempdb na woluminie F: (F:\MSSQL\Data).
7. Uruchom usługę SQL z poziomu konsoli usługi.

#### <a name="use-an-existing-temporary-storage-disk"></a>Użyj istniejącego dysku magazynu tymczasowego 

1. Otwórz wiersz polecenia.
2. Z poziomu wiersza polecenia uruchom program SQL Server w trybie odzyskiwania.

    ```console
    Net start MSSQLSERVER /f / T3608
    ```

3. Uruchom następujące polecenie sqlcmd, aby zmienić ścieżkę bazy danych tempdb na nową ścieżkę.

    ```sql
    sqlcmd -A -S SalesDB        **Use your SQL DBname**
    USE master;     
    GO      
    ALTER DATABASE tempdb       
    MODIFY FILE (NAME = tempdev, FILENAME = 'E:\MSSQL\tempdata\tempdb.mdf');
    GO      
    ALTER DATABASE tempdb       
    MODIFY FILE (NAME = templog, FILENAME = 'E:\MSSQL\tempdata\templog.ldf');       
    GO
    ```

4. Zatrzymaj usługę programu Microsoft SQL Server.

    ```console
    Net stop MSSQLSERVER
    ```

5. Uruchom usługę programu Microsoft SQL Server.

    ```console
    Net start MSSQLSERVER
    ```

### <a name="vmware-vms-disks-during-failback-to-original-location"></a>Maszyny wirtualne VMware: dyski podczas powrotu po awarii do oryginalnej lokalizacji

Teraz zobaczmy, jak obsługiwać dyski na maszynach wirtualnych VMware w przypadku powrotu po awarii do oryginalnej lokalizacji lokalnej.

- **Dyski utworzone na platformie Azure**: ponieważ w naszym przykładzie używane są maszyny wirtualne z systemem Windows, dyski tworzone ręcznie na platformie Azure nie są replikowane z powrotem do lokacji po awarii lub ponownym włączeniu ochrony maszyny wirtualnej.
- **Dysk magazynu tymczasowego na platformie Azure**: dysk magazynu tymczasowego nie jest replikowany z powrotem do hostów lokalnych.
- **Wykluczone dyski**: dyski, które zostały wykluczone z replikacji oprogramowania VMware do platformy Azure, nie są dostępne na lokalnej maszynie wirtualnej po powrocie po awarii.

Przed zakończeniem powrotu maszyn wirtualnych VMware do oryginalnej lokalizacji Ustawienia dysku maszyny wirtualnej platformy Azure są następujące.

**Dysk systemu operacyjnego gościa** | **Litera dysku** | **Typ danych dysku**
--- | --- | ---
Disk0 | C:\ | Dysk systemu operacyjnego.
Dysk1 | E:\ | Magazyn tymczasowy.
Dysk2 | D:\ | Baza danych systemu SQL i database1 użytkownika.
Dysk3 | G:\ | Database2 użytkownika.

Po powrocie po awarii maszyna wirtualna VMware w oryginalnej lokalizacji zawiera dyski podsumowane w tabeli.

**Dysk systemu operacyjnego gościa** | **Litera dysku** | **Typ danych dysku**
--- | --- | ---
Disk0 | C:\ | Dysk systemu operacyjnego.
Dysk1 | D:\ | Baza danych systemu SQL i database1 użytkownika.
Dysk2 | G:\ | Database2 użytkownika.


### <a name="hyper-v-vms-disks-during-failback-to-original-location"></a>Maszyny wirtualne funkcji Hyper-V: dyski podczas powrotu po awarii do oryginalnej lokalizacji

Teraz zobaczmy, jak obsługiwać dyski na maszynach wirtualnych funkcji Hyper-V po powrocie po awarii do oryginalnej lokalizacji lokalnej.

- **Dyski utworzone na platformie Azure**: dyski tworzone ręcznie na platformie Azure nie są replikowane z powrotem do lokacji po awarii lub ponownym włączeniu ochrony maszyny wirtualnej.
- **Dysk magazynu tymczasowego na platformie Azure**: dysk magazynu tymczasowego nie jest replikowany z powrotem do hostów lokalnych.
- **Wykluczone dyski**: po powrocie po awarii Konfiguracja dysku maszyny wirtualnej jest taka sama jak konfiguracja oryginalnego dysku maszyny wirtualnej. Dyski wykluczone z replikacji z funkcji Hyper-V do platformy Azure są dostępne na maszynie wirtualnej powrotu po awarii.

Przed zakończeniem powrotu maszyn wirtualnych funkcji Hyper-V do oryginalnej lokalizacji Ustawienia dysku maszyny wirtualnej platformy Azure są następujące.

**Dysk systemu operacyjnego gościa** | **Litera dysku** | **Typ danych dysku**
--- | --- | ---
Disk0 | C:\ | Dysk systemu operacyjnego.
Dysk1 | E:\ | Magazyn tymczasowy.
Dysk2 | D:\ | Baza danych systemu SQL i database1 użytkownika.
Dysk3 | G:\ | Database2 użytkownika.

Po planowanym przejściu w tryb failover (powrót po awarii) z platformy Azure do lokalnej funkcji Hyper-V maszyna wirtualna funkcji Hyper-V w oryginalnej lokalizacji zawiera dyski podsumowane w tabeli.

**Nazwa dysku** | **Numer dysku systemu operacyjnego gościa** | **Litera dysku** | **Typ danych dysku**
 --- | --- | --- | ---
DB-Disk0-OS | Disk0 |   C:\ | Dysk systemu operacyjnego.
DB-Disk1 | Dysk1 | D:\ | Baza danych systemu SQL i database1 użytkownika.
DB-Disk2 (dysk wykluczony) | Dysk2 | E:\ | Pliki temp.
DB-Disk3 (dysk wykluczony) | Dysk3 | F:\ | Baza danych SQL tempdb<br/><br/> Ścieżka folderu (F:\MSSQL\Data \) .
DB-Disk4 | Dysk4 | G:\ | Baza danych użytkownika 2


## <a name="example-2-exclude-the-paging-file-disk"></a>Przykład 2: wykluczanie dysku pliku stronicowania

Przyjrzyjmy się sposobom obsługi wykluczenia dysku, trybu failover i trybu failover dla źródłowej maszyny wirtualnej z systemem Windows, dla której chcemy wykluczyć dysk plików pagefile.sys na dysku D i alternatywny dysk.


### <a name="paging-file-on-the-d-drive"></a>Plik stronicowania na dysku D

Te dyski są dostępne na źródłowej maszynie wirtualnej.

**Nazwa dysku** | **Dysk systemu operacyjnego gościa** | **Litera dysku** | **Typ danych dysku**
--- | --- | --- | ---
DB-Disk0-OS | Disk0 | C:\ | Dysk systemu operacyjnego
DB-Disk1 (Wyklucz z replikacji) | Dysk1 | D:\ | pagefile.sys
DB-Disk2 | Dysk2 | E:\ | Dane użytkowników 1
DB-Disk3 | Dysk3 | F:\ | Dane użytkowników 2

Nasze ustawienia pliku stronicowania na źródłowej maszynie wirtualnej są następujące:

![Zrzut ekranu przedstawiający okno dialogowe pamięć wirtualna z wyróżnioną linią D: Drive [wolumin stronicowania] z wyświetlonym rozmiarem pliku stronicowania (MB) 3000-7000.](./media/exclude-disks-replication/pagefile-d-drive-source-vm.png)

1. Włączamy replikację dla maszyny wirtualnej.
2. Wykluczono DB-Disk1 z replikacji.

#### <a name="disks-after-failover"></a>Dyski po przejściu w tryb failover

Po przejściu do trybu failover na maszynie wirtualnej platformy Azure zostały podsumowane dyski w tabeli.

**Nazwa dysku** | **Nr dysku systemu operacyjnego gościa** | **Litera dysku** | **Typ danych na dysku**
--- | --- | --- | ---
DB-Disk0-OS | Disk0 | C:\ | Dysk systemu operacyjnego
DB-Disk1 | Dysk1 | D:\ | Magazyn tymczasowy/pagefile.sys <br/><br/> Ponieważ DB-Disk1 (D:) został wykluczony, D: jest pierwszą literą dysku z listy dostępnych.<br/><br/> Platforma Azure przypisuje literę D: woluminowi magazynu tymczasowego.<br/><br/> Ponieważ D: jest dostępny, ustawienie pliku stronicowania maszyny wirtualnej pozostaje takie samo.
DB-Disk2 | Dysk2 | E:\ | Dane użytkowników 1
DB-Disk3 | Dysk3 | F:\ | Dane użytkowników 2

Nasze ustawienia pliku stronicowania na maszynie wirtualnej platformy Azure są następujące:

![Ustawienia pliku stronicowania na maszynie wirtualnej platformy Azure](./media/exclude-disks-replication/pagefile-azure-vm-after-failover.png)

### <a name="paging-file-on-another-drive-not-d"></a>Plik stronicowania na innym dysku (nie D:)

Przyjrzyjmy się przykładowi, w którym plik stronicowania nie znajduje się na dysku D.  

Te dyski są dostępne na źródłowej maszynie wirtualnej.

**Nazwa dysku** | **Dysk systemu operacyjnego gościa** | **Litera dysku** | **Typ danych dysku**
--- | --- | --- | ---
DB-Disk0-OS | Disk0 | C:\ | Dysk systemu operacyjnego
DB-Disk1 (Wyklucz z replikacji) | Dysk1 | G:\ | pagefile.sys
DB-Disk2 | Dysk2 | E:\ | Dane użytkowników 1
DB-Disk3 | Dysk3 | F:\ | Dane użytkowników 2

Nasze ustawienia pliku stronicowania na lokalnej maszynie wirtualnej są następujące:

![Ustawienia pliku stronicowania na lokalnej maszynie wirtualnej](./media/exclude-disks-replication/pagefile-g-drive-source-vm.png)

1. Włączamy replikację dla maszyny wirtualnej.
2. Wykluczono DB-Disk1 z replikacji.

#### <a name="disks-after-failover"></a>Dyski po przejściu w tryb failover

Po przejściu do trybu failover na maszynie wirtualnej platformy Azure zostały podsumowane dyski w tabeli.

**Nazwa dysku** | **Numer dysku systemu operacyjnego gościa** | **Litera dysku** | **Typ danych dysku**
--- | --- | --- | ---
DB-Disk0-OS | Disk0  |C:\ | Dysk systemu operacyjnego
DB-Disk1 | Dysk1 | D:\ | Magazyn tymczasowy<br/><br/> Ponieważ litera D: jest pierwszą dostępną literą dysku na liście, platforma Azure przypisuje literę D: do woluminu magazynu tymczasowego.<br/><br/> Litery dysków wszystkich replikowanych dysków pozostają bez zmian.<br/><br/> Ponieważ dysk G: nie jest dostępny, system użyje dysku C: dla pliku stronicowania.
DB-Disk2 | Dysk2 | E:\ | Dane użytkowników 1
DB-Disk3 | Dysk3 | F:\ | Dane użytkowników 2

Nasze ustawienia pliku stronicowania na maszynie wirtualnej platformy Azure są następujące:

![Zrzut ekranu okna dialogowego pamięć wirtualna z wyróżnioną linią C: Drive wyświetlającą ustawienie rozmiaru pliku stronicowania "zarządzane przez system".](./media/exclude-disks-replication/pagefile-azure-vm-after-failover-2.png)


## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat wytycznych dotyczących tymczasowego dysku magazynu:
    - [Dowiedz się więcej o](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/) korzystaniu z dysków SSD na maszynach wirtualnych platformy Azure do przechowywania SQL Server tempdb i rozszerzeń puli buforów
    - [Przejrzyj ](../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md) najlepsze rozwiązania dotyczące wydajności SQL Server na maszynach wirtualnych platformy Azure.
- Po skonfigurowaniu i uruchomieniu wdrożenia [dowiedz się więcej](failover-failback-overview.md) o różnych typach trybu failover.
