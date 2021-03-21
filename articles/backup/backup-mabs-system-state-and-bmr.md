---
title: System i ochrona przed odzyskiwaniem bez systemu operacyjnego
description: Użyj Azure Backup Server, aby utworzyć kopię zapasową stanu systemu i zapewnić ochronę przed odzyskiwaniem od zera (BMR).
ms.topic: conceptual
ms.date: 05/15/2017
ms.openlocfilehash: c5096158ca0e76ca03577347d8dd3e1419a33ca0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96021626"
---
# <a name="back-up-system-state-and-restore-to-bare-metal-by-using-azure-backup-server"></a>Tworzenie kopii zapasowej stanu systemu i przywracanie na komputerach bez systemu operacyjnego przy użyciu Azure Backup Server

Azure Backup Server Tworzenie kopii zapasowej stanu systemu i zapewnia ochronę bez systemu operacyjnego (BMR).

* **Kopia zapasowa stanu systemu**: tworzy kopie zapasowe plików systemu operacyjnego. Ta kopia zapasowa umożliwia odzyskanie, gdy komputer zostanie uruchomiony, ale pliki systemowe i rejestr zostaną utracone. Kopia zapasowa stanu systemu obejmuje następujące elementy:
  * Element członkowski domeny: pliki rozruchowe, baza danych rejestracji klasy COM+, rejestr
  * Kontroler domeny: Windows Server Active Directory (NTDS), pliki rozruchowe, baza danych rejestracji klasy COM+, rejestr, wolumin systemowy (SYSVOL)
  * Komputer z uruchomionymi usługami klastra: metadane serwera klastra
  * Komputer z uruchomionymi usługami certyfikatów: dane certyfikatu
* **Kopia zapasowa bez systemu** operacyjnego: tworzy kopie zapasowe plików systemów operacyjnych i wszystkich danych na woluminach krytycznych, z wyjątkiem danych użytkownika. Zgodnie z definicją kopia zapasowa BMR obejmuje kopię zapasową stanu systemu. Zapewnia ochronę, gdy komputer nie zostanie uruchomiony i konieczne będzie odzyskanie wszystkiego.

Poniższa tabela zawiera podsumowanie informacji o tym, co można utworzyć i odzyskać. Aby uzyskać informacje o wersjach aplikacji, których stan systemu i BMR mogą być chronione, zobacz [co to jest Azure Backup Server kopia zapasowa?](backup-mabs-protection-matrix.md).

|Backup|Problem|Odzyskaj z kopii zapasowej Azure Backup Server|Odzyskiwane z kopii zapasowej stanu systemu|BMR|
|----------|---------|---------------------------|------------------------------------|-------|
|**Dane pliku**<br /><br />Regularne tworzenie kopii zapasowej danych<br /><br />Kopia zapasowa stanu systemu/kompletnego stanu systemu|Utracone dane pliku|Y|N|N|
|**Dane pliku**<br /><br />Azure Backup Server kopii zapasowej danych plików<br /><br />Kopia zapasowa stanu systemu/kompletnego stanu systemu|Uszkodzony lub utracony system operacyjny|N|Y|Y|
|**Dane pliku**<br /><br />Azure Backup Server kopii zapasowej danych plików<br /><br />Kopia zapasowa stanu systemu/kompletnego stanu systemu|Utracony serwer (nienaruszone woluminy danych)|N|N|Y|
|**Dane pliku**<br /><br />Azure Backup Server kopii zapasowej danych plików<br /><br />Kopia zapasowa stanu systemu/kompletnego stanu systemu|Utracony serwer (utracone woluminy danych)|Y|N|Y<br /><br />BMR, a następnie regularne odzyskiwanie kopii zapasowej danych plików|
|**Dane programu SharePoint**<br /><br />Azure Backup Server kopii zapasowej danych farmy<br /><br />Kopia zapasowa stanu systemu/kompletnego stanu systemu|Utracona witryna, listy, elementy listy, dokumenty|Y|N|N|
|**Dane programu SharePoint**<br /><br />Azure Backup Server kopii zapasowej danych farmy<br /><br />Kopia zapasowa stanu systemu/kompletnego stanu systemu|Uszkodzony lub utracony system operacyjny|N|Y|Y|
|**Dane programu SharePoint**<br /><br />Azure Backup Server kopii zapasowej danych farmy<br /><br />Kopia zapasowa stanu systemu/kompletnego stanu systemu|Odzyskiwanie po awarii|N|N|N|
|Windows Server 2012 R2 Hyper-V<br /><br />Azure Backup Server kopii zapasowej hosta lub gościa funkcji Hyper-V<br /><br />Kopia zapasowa stanu systemu/kompletnego stanu systemu hosta|Utracona maszyna wirtualna|Y|N|N|
|Hyper-V<br /><br />Azure Backup Server kopii zapasowej hosta lub gościa funkcji Hyper-V<br /><br />Kopia zapasowa stanu systemu/kompletnego stanu systemu hosta|Uszkodzony lub utracony system operacyjny|N|Y|Y|
|Hyper-V<br /><br />Azure Backup Server kopii zapasowej hosta lub gościa funkcji Hyper-V<br /><br />Kopia zapasowa stanu systemu/kompletnego stanu systemu hosta|Utracony host funkcji Hyper-V (nieuszkodzone maszyny wirtualne)|N|N|Y|
|Hyper-V<br /><br />Azure Backup Server kopii zapasowej hosta lub gościa funkcji Hyper-V<br /><br />Kopia zapasowa stanu systemu/kompletnego stanu systemu hosta|Utracony host funkcji Hyper-V (utracone maszyny wirtualne)|N|N|Y<br /><br />BMR, po którym następuje regularne odzyskiwanie Azure Backup Server|
|Program SQL Server/Exchange<br /><br />Kopia zapasowa aplikacji Azure Backup Server<br /><br />Kopia zapasowa stanu systemu/kompletnego stanu systemu|Utracone dane aplikacji|Y|N|N|
|Program SQL Server/Exchange<br /><br />Kopia zapasowa aplikacji Azure Backup Server<br /><br />Kopia zapasowa stanu systemu/kompletnego stanu systemu|Uszkodzony lub utracony system operacyjny|N|Y|Y|
|Program SQL Server/Exchange<br /><br />Kopia zapasowa aplikacji Azure Backup Server<br /><br />Kopia zapasowa stanu systemu/kompletnego stanu systemu|Utracony serwer (nieuszkodzone dzienniki transakcji i baz danych)|N|N|Y|
|Program SQL Server/Exchange<br /><br />Kopia zapasowa aplikacji Azure Backup Server<br /><br />Kopia zapasowa stanu systemu/kompletnego stanu systemu|Utracony serwer (utracone dzienniki transakcji i baz danych)|N|N|Y<br /><br />Odzyskiwanie BMR, po którym następuje regularne odzyskiwanie Azure Backup Server|

## <a name="how-system-state-backup-works"></a>Jak działa tworzenie kopii zapasowej stanu systemu

Po uruchomieniu kopii zapasowej stanu systemu serwer zapasowy komunikuje się z Kopia zapasowa systemu Windows Server, aby zażądać kopii zapasowej stanu systemu serwera. Domyślnie serwer zapasowy i Kopia zapasowa systemu Windows Server korzystają z dysku z największą ilością wolnego miejsca. Informacje o tym dysku są zapisywane w pliku *PSDataSourceConfig.xml* .

Można dostosować dysk wykorzystywany przez serwer kopii zapasowej do tworzenia kopii zapasowej stanu systemu:

1. Na serwerze chronionym przejdź do *folderu C:\Program Files\Microsoft Data Protection Manager\MABS\Datasources*.
1. Otwórz plik *PSDataSourceConfig.xml* do edycji.
1. Zmień wartość \<FilesToProtect\> na literę dysku.
1. Zapisz i zamknij plik.

Jeśli grupa ochrony jest ustawiona na ochronę stanu systemu komputera, Uruchom sprawdzanie spójności. W przypadku wygenerowania alertu wybierz pozycję **Modyfikuj grupę ochrony** w alercie, a następnie wypełnij strony kreatora. Następnie uruchom kolejną kontrolę spójności.

Jeśli serwer ochrony znajduje się w klastrze, dysk klastra może być wybrany jako dysk z największą ilością wolnego miejsca. Jeśli własność stacji jest przełączana do innego węzła i uruchomienia kopii zapasowej stanu systemu, dysk jest niedostępny i wykonywanie kopii zapasowej nie powiedzie się. W tym scenariuszu zmodyfikuj *PSDataSourceConfig.xml* tak, aby wskazywał na dysk lokalny.

Następnie Kopia zapasowa systemu Windows Server tworzy folder o nazwie *WindowsImageBackup* w katalogu głównym folderu przywracania. Ponieważ Kopia zapasowa systemu Windows Server tworzy kopię zapasową, wszystkie dane zostaną umieszczone w tym folderze. Po zakończeniu tworzenia kopii zapasowej plik jest przesyłany do komputera serwera kopii zapasowej. Zanotuj następujące informacje:

* Ten folder i jego zawartość nie są czyszczone po zakończeniu wykonywania kopii zapasowej lub transferu. Najlepszym sposobem na to, że miejsce jest zarezerwowane przy następnym zakończeniu tworzenia kopii zapasowej.
* Folder jest tworzony dla każdej kopii zapasowej. Sygnatura czasowa i daty odzwierciedlają czas ostatniej kopii zapasowej stanu systemu.

## <a name="how-bmr-backup-works"></a>Jak działa kopia zapasowa BMR

W przypadku BMR (w tym kopii zapasowej stanu systemu) zadanie tworzenia kopii zapasowej jest zapisywane bezpośrednio w udziale na komputerze serwera kopii zapasowej. Nie jest on zapisywany w folderze na serwerze chronionym.

Wywołania serwera kopii zapasowej Kopia zapasowa systemu Windows Server i udostępniają wolumin repliki dla tej kopii zapasowej BMR. W takim przypadku nie wymaga Kopia zapasowa systemu Windows Server na dysku z największą ilością wolnego miejsca. Zamiast tego używa udziału, który został utworzony dla tego zadania.

Po zakończeniu tworzenia kopii zapasowej plik jest przesyłany do komputera serwera kopii zapasowej. Dzienniki są przechowywane w *C:\Windows\Logs\WindowsServerBackup*.

## <a name="prerequisites-and-limitations"></a>Wymagania wstępne i ograniczenia

* BMR nie jest obsługiwane w przypadku komputerów z systemem Windows Server 2003 lub na komputerach z systemem operacyjnym klienta.

* Nie można używać jednocześnie ochrony BMR i stanu systemu dla komputera znajdującego się w różnych grupach ochrony.

* Komputer serwera kopii zapasowej nie może chronić samego siebie dla BMR.

* Krótkoterminowa ochrona na taśmie (dysk na taśmę lub D2T) nie jest obsługiwana dla BMR. Obsługa magazynu długoterminowego na taśmie (dysk na taśmę lub D2D2T) jest obsługiwana.

* W przypadku ochrony BMR należy zainstalować na komputerze chronionym Kopia zapasowa systemu Windows Server.

* Dla ochrony BMR, w przeciwieństwie do ochrony stanu systemu, serwer kopii zapasowych nie ma wymagań dotyczących miejsca na komputerze chronionym. Kopia zapasowa systemu Windows Server bezpośrednio przesyła kopie zapasowe do komputera serwera kopii zapasowej. Zadanie transferu kopii zapasowej nie jest wyświetlane w widoku **zadań** serwera kopii zapasowej.

* Serwer kopii zapasowych rezerwuje 30 GB miejsca na woluminie repliki dla BMR. Przydział tego miejsca można zmienić na stronie **alokacja dysku** w Kreatorze modyfikowania grupy ochrony. Można też użyć poleceń cmdlet programu PowerShell Get-DatasourceDiskAllocation i Set-DatasourceDiskAllocation. Na woluminie punktu odzyskiwania ochrona BMR wymaga około 6 GB do przechowywania przez pięć dni.
  * Nie można zmniejszyć rozmiaru woluminu repliki do mniej niż 15 GB.
  * Serwer kopii zapasowej nie oblicza rozmiaru źródła danych BMR. Założono 30 GB dla wszystkich serwerów. Zmień wartość na podstawie rozmiaru BMR kopii zapasowych oczekiwanych w danym środowisku. Można w przybliżeniu obliczyć rozmiar kopii zapasowej BMR jako sumę zajętego miejsca na wszystkich woluminach krytycznych. Woluminy krytyczne = wolumin rozruchowy + wolumin systemowy + dane stanu systemu hostingu woluminu, takie jak Active Directory.

* W przypadku zmiany z ochrony stanu systemu na ochronę BMR ochrona BMR będzie wymagać mniejszej ilości miejsca na *woluminie punktu odzyskiwania*. Jednak dodatkowe miejsce na woluminie nie jest odzyskiwane. Rozmiar woluminu można zmniejszyć ręcznie na stronie **Modyfikuj przydział dysku** w Kreatorze modyfikowania grupy ochrony. Można też użyć poleceń cmdlet programu PowerShell Get-DatasourceDiskAllocation i Set-DatasourceDiskAllocation.

    W przypadku zmiany z ochrony stanu systemu na ochronę BMR ochrona BMR będzie wymagać więcej miejsca na *woluminie repliki*. Wolumin zostanie automatycznie rozszerzony. Jeśli chcesz zmienić domyślne alokacje miejsca, użyj polecenia cmdlet programu Modify-DiskAllocation PowerShell.

* W przypadku zmiany z ochrony BMR na ochronę stanu systemu należy zwiększyć ilość miejsca na woluminie punktu odzyskiwania. Serwer kopii zapasowej może próbować automatycznie zwiększyć wolumin. Jeśli pula magazynów nie ma wystarczającej ilości miejsca, wystąpi błąd.

    W przypadku zmiany z ochrony BMR na ochronę stanu systemu należy mieć miejsce na komputerze chronionym. Musisz mieć miejsce, ponieważ ochrona stanu systemu najpierw zapisuje replikę na komputerze lokalnym, a następnie przesyła replikę na komputer serwera kopii zapasowej.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

1. **Wdróż Azure Backup Server**. Sprawdź, czy serwer kopii zapasowej został poprawnie wdrożony. Aby uzyskać więcej informacji, zobacz:
    * [Wymagania systemowe Azure Backup Server](/system-center/dpm/install-dpm#setup-prerequisites)
    * [Macierz ochrony serwera kopii zapasowych](backup-mabs-protection-matrix.md)

1. **Skonfiguruj magazyn**. Dane kopii zapasowej można przechowywać na dysku, na taśmie i w chmurze przy użyciu platformy Azure. Aby uzyskać więcej informacji, zobacz [Przygotowywanie magazynu danych](/system-center/dpm/plan-long-and-short-term-data-storage).

1. **Skonfiguruj agenta ochrony**. Zainstaluj agenta ochrony na komputerze, którego kopię zapasową chcesz utworzyć. Aby uzyskać więcej informacji, zobacz [wdrażanie agenta ochrony programu DPM](/system-center/dpm/deploy-dpm-protection-agent).

## <a name="back-up-system-state-and-bare-metal"></a>Tworzenie kopii zapasowej stanu systemu i kompletnego stanu systemu

Aby utworzyć kopię zapasową stanu systemu i bez systemu operacyjnego:

1. Aby otworzyć Kreator nowej grupy ochrony tworzenia, w Konsola administratora serwer zapasowy wybierz pozycję akcje **ochrony**  >    >  **Utwórz grupę ochrony**.

1. Na stronie **Wybierz typ grupy ochrony** wybierz pozycję **serwery**, a następnie wybierz przycisk **dalej**.

1. Na stronie **Wybierz członków grupy** rozwiń komputer, a następnie wybierz pozycję **BMR** lub **stan systemu**.

    Należy pamiętać, że nie można włączyć ochrony zarówno BMR, jak i stanu systemu na tym samym komputerze w różnych grupach. Ponadto w przypadku wybrania opcji BMR stan systemu jest automatycznie włączany. Aby uzyskać więcej informacji, zobacz [wdrażanie grup ochrony](/system-center/dpm/create-dpm-protection-groups).

1. Na stronie **Wybierz metodę ochrony danych** wybierz sposób obsługi krótkoterminowej kopii zapasowej i długoterminowej kopii zapasowej.

    Krótkoterminowe kopie zapasowe zawsze są na dysku, z opcją tworzenia kopii zapasowych z dysku na platformę Azure przy użyciu Azure Backup (krótkoterminowe lub długoterminowe). Alternatywą dla długoterminowej kopii zapasowej w chmurze jest skonfigurowanie długoterminowej kopii zapasowej na autonomicznym urządzeniu taśmowym lub w bibliotece taśm połączonej z serwerem kopii zapasowych.

1. Na stronie **Wybierz cele Short-Term** wybierz sposób tworzenia kopii zapasowej do krótkoterminowego przechowywania na dysku:
    * W obszarze **Zakres przechowywania** Określ, jak długo mają być przechowywane dane na dysku.
    * W obszarze **częstotliwość synchronizacji** Określ, jak często mają być uruchamiane przyrostowe kopie zapasowe na dysku. Jeśli nie chcesz ustawiać interwału kopii zapasowych, możesz wybrać opcję **tuż przed punktem odzyskiwania**. Serwer kopii zapasowej będzie uruchamiał ekspresową pełną kopię zapasową tuż przed zaplanowaniem każdego punktu odzyskiwania.

1. Jeśli chcesz przechowywać dane na taśmie w celu przechowywania długoterminowego, na stronie **określ Long-Term cele** wybierz, jak długo chcesz przechowywać dane na taśmie (od 1 do 99 lat).
    1. Aby uzyskać **częstotliwość tworzenia kopii zapasowych**, wybierz częstotliwość wykonywania kopii zapasowej na taśmie. Częstotliwość zależy od wybranego zakresu przechowywania:
        * Gdy zakres przechowywania wynosi od 1 do 99 lat, można utworzyć kopię zapasową codziennie, co tydzień, co dwa tygodnie, co miesiąc, co kwartał, co pół roku lub co rok.
        * Gdy zakres przechowywania wynosi od 1 do 11 miesięcy, można utworzyć kopię zapasową codziennie, co tydzień, co dwa tygodnie lub co miesiąc.
        * Gdy zakres przechowywania wynosi od 1 do 4 tygodni, można utworzyć kopię zapasową codziennie lub co tydzień.

    1. Na stronie **Wybieranie szczegółów taśmy i biblioteki** wybierz taśmę i bibliotekę do użycia. Należy również określić, czy dane mają być kompresowane i szyfrowane.

1. Na stronie **Przejrzyj przydział dysku** Przejrzyj miejsce na dysku w puli magazynów dostępne dla grupy ochrony.

    * **Łączny rozmiar danych** to rozmiar danych, dla których chcesz utworzyć kopię zapasową.
    * **Miejsce na dysku, które ma zostać zainicjowane w Azure Backup Server** jest miejscem zalecanym dla grupy ochrony przez serwer kopii zapasowych. Serwer kopii zapasowych używa tych ustawień, aby wybrać idealny wolumin kopii zapasowej. Można edytować opcje woluminu kopii zapasowej w obszarze **szczegóły alokacji dysku**.
    * W przypadku obciążeń, w menu rozwijanym wybierz preferowany magazyn. Edycja zmienia wartości pozycji **Całkowita ilość miejsca dla magazynu** i **Wolne miejsce w magazynie** w okienku **Dostępny magazyn dyskowy**. Zajęte miejsce to ilość miejsca w magazynie, którą serwer kopii zapasowej sugeruje do dodania do woluminu, aby zapewnić płynne kopie zapasowe.

1. Na stronie **Wybierz metodę tworzenia repliki** wybierz sposób obsługi początkowej replikacji pełnej danych.

    Jeśli zdecydujesz się na replikację za pośrednictwem sieci, zalecamy wybranie czasu poza godzinami szczytu. W przypadku dużych ilości danych lub warunków sieci, które są mniejsze niż optymalne, należy rozważyć replikację danych w trybie offline za pomocą nośników wymiennych.

1. Na stronie **Wybierz opcje sprawdzania spójności** wybierz sposób automatyzacji sprawdzania spójności.

    Można uruchomić sprawdzanie tylko wtedy, gdy dane repliki staną się niespójne lub zgodnie z harmonogramem. Jeśli nie chcesz konfigurować automatycznego sprawdzania spójności, w dowolnym momencie możesz uruchomić sprawdzanie ręczne. Aby uruchomić sprawdzanie ręczne, w obszarze **Ochrona** serwera zapasowego Konsola administratora kliknij prawym przyciskiem myszy grupę ochrony, a następnie wybierz polecenie **Przeprowadź sprawdzanie spójności**.

1. Jeśli chcesz utworzyć kopię zapasową w chmurze przy użyciu Azure Backup, na stronie **Określ dane ochrony w trybie online** wybierz obciążenia, których kopię zapasową chcesz utworzyć na platformie Azure.

1. Na stronie **Określanie harmonogramu tworzenia kopii zapasowych online** wybierz, jak często mają być wykonywane przyrostowe kopie zapasowe na platformie Azure.

    Można zaplanować uruchamianie kopii zapasowych codziennie, tygodnia, miesiąca i roku. Możesz również wybrać godzinę i datę, z których mają być uruchamiane kopie zapasowe. Kopie zapasowe mogą być tworzone maksymalnie dwa razy dziennie. Za każdym razem, gdy wykonywana jest kopia zapasowa, punkt odzyskiwania danych jest tworzony na platformie Azure na podstawie kopii kopii zapasowej przechowywanych na dysku serwera zapasowego.

1. Na stronie **Określanie zasad przechowywania online** wybierz, jak punkty odzyskiwania tworzone na podstawie kopii zapasowych codziennie, co tydzień, co miesiąc i co roku są przechowywane na platformie Azure.

1. Na stronie **Wybierz replikację online** wybierz, jak następuje początkowa pełna replikacja danych.

    Można replikować za pośrednictwem sieci lub tworzyć kopie zapasowe w trybie offline (w trybie offline). Kopia zapasowa offline używa funkcji importowania platformy Azure. Aby uzyskać więcej informacji, zobacz [przepływ pracy kopii zapasowej offline w Azure Backup](offline-backup-azure-data-box.md).

1. Na stronie  **Podsumowanie** przejrzyj ustawienia. Po wybraniu opcji **Utwórz grupę** następuje Replikacja początkowa danych. Po zakończeniu replikacji danych na stronie **stan** Grupa ochrony ma stan **OK**. Kopie zapasowe są następnie wykonywane zgodnie z ustawieniami grupy ochrony.

## <a name="recover-system-state-or-bmr"></a>Odzyskiwanie stanu systemu lub odzyskiwanie systemu od zera (BMR)

Możliwe jest odzyskanie systemu od zera (BMR) lub odzyskanie stanu systemu w lokalizacji sieciowej. Jeśli utworzono kopię zapasową BMR, użyj środowiska odzyskiwania systemu Windows (WinRE) do uruchomienia systemu i nawiązania połączenia z siecią. Użyj narzędzia Kopia zapasowa systemu Windows Server, aby odzyskać informacje z lokalizacji sieciowej. Jeśli utworzono kopię zapasową stanu systemu, po prostu Użyj Kopia zapasowa systemu Windows Server do odzyskania z lokalizacji sieciowej.

### <a name="restore-bmr"></a>Przywracanie systemu od zera (BMR)

Aby uruchomić odzyskiwanie na komputerze serwera kopii zapasowej:

1. W okienku **odzyskiwanie** Znajdź komputer, który chcesz odzyskać. Następnie wybierz pozycję **odzyskiwanie systemu od zera**.

1. Dostępne punkty odzyskiwania są pogrubione w kalendarzu. Wybierz datę i godzinę dla punktu odzyskiwania, którego chcesz użyć.

1. Na stronie  **Wybierz typ odzyskiwania** wybierz opcję **Kopiuj do folderu sieciowego**.

1. Na stronie **Określanie miejsca docelowego** wybierz lokalizację docelową dla kopiowanych danych.

    Należy pamiętać, że miejsce docelowe musi mieć wystarczającą ilość miejsca na dane. Zalecamy utworzenie nowego folderu dla miejsca docelowego.

1. Na stronie **Określanie opcji odzyskiwania** wybierz ustawienia zabezpieczeń. Następnie wybierz, czy chcesz korzystać z migawek sprzętowych opartych na sieci magazynowania (SAN), aby szybciej odzyskiwać dane. Ta opcja jest dostępna tylko wtedy, gdy:
    * Masz dostęp do sieci SAN, która zapewnia tę funkcjonalność.
    * Można utworzyć i podzielić klon, aby umożliwić jego zapisywanie.
    * Komputer chroniony i serwer zapasowy są połączone z tą samą siecią.

1. Skonfiguruj opcje powiadomień.
1. Na stronie **potwierdzenie** wybierz pozycję **Odzyskaj**.

Aby skonfigurować lokalizację udziału:

1. W lokalizacji przywracania przejdź do folderu, w którym znajduje się kopia zapasowa.

1. Udostępnij folder, który znajduje się na poziomie powyżej *WindowsImageBackup* , tak aby katalogiem głównym folderu udostępnionego był folder *WindowsImageBackup* .

    Jeśli ten folder nie jest udostępniany, polecenie Restore nie odnajdzie kopii zapasowej. Aby nawiązać połączenie przy użyciu środowiska WinRE, musisz mieć udział, do którego można uzyskać dostęp w programie WinRE przy użyciu poprawnego adresu IP i poświadczeń.

Aby przywrócić system:

1. Uruchom komputer, na którym chcesz przywrócić obraz, przy użyciu dysku DVD systemu Windows dla przywracanego systemu.

1. Na pierwszej stronie Sprawdź ustawienia języka i ustawień regionalnych. Na stronie **Instalacja** wybierz opcję **Napraw komputer**.

1. Na stronie **Opcje odzyskiwania systemu** wybierz pozycję **Przywróć komputer przy użyciu utworzonego wcześniej obrazu systemu**.

1. Na stronie **Wybierz kopię zapasową obrazu systemu** wybierz pozycję **Wybierz obraz systemu**  >  **Zaawansowane**  >  **Wyszukiwanie obrazu systemu w sieci**. Jeśli zostanie wyświetlone ostrzeżenie, wybierz pozycję **tak**. Przejdź do ścieżki udziału, wprowadź poświadczenia, a następnie wybierz punkt odzyskiwania. System skanuje w poszukiwaniu określonych kopii zapasowych, które są dostępne w tym punkcie odzyskiwania. Wybierz punkt odzyskiwania, którego chcesz użyć.

1. Na stronie **Wybierz sposób przywracania kopii zapasowej** wybierz opcję **Formatuj dyski i ponownie Podziel na partycje**. Na następnej stronie Sprawdź ustawienia.

1. Aby rozpocząć przywracanie, wybierz pozycję **Zakończ**. Wymagane jest ponowne uruchomienie.

### <a name="restore-system-state"></a>Przywracanie stanu systemu

Aby uruchomić odzyskiwanie na serwerze kopii zapasowej:

1. W okienku **odzyskiwanie** Znajdź komputer, który chcesz odzyskać, a następnie wybierz pozycję **odzyskiwanie systemu od zera**.

1. Dostępne punkty odzyskiwania są pogrubione w kalendarzu. Wybierz datę i godzinę dla punktu odzyskiwania, którego chcesz użyć.

1. Na stronie **Wybierz typ odzyskiwania** wybierz opcję **Kopiuj do folderu sieciowego**.

1. Na stronie **określ miejsce docelowe** wybierz miejsce, do którego mają zostać skopiowane dane.

    Należy pamiętać, że wybrana lokalizacja docelowa musi mieć wystarczającą ilość miejsca na dane. Zalecamy utworzenie nowego folderu dla miejsca docelowego.

1. Na stronie **Określanie opcji odzyskiwania** wybierz ustawienia zabezpieczeń. Następnie wybierz, czy chcesz korzystać z migawek sprzętowych opartych na sieci SAN, aby szybciej odzyskiwać dane. Ta opcja jest dostępna tylko wtedy, gdy:
    * Masz dostęp do sieci SAN, która zapewnia tę funkcjonalność.
    * Można utworzyć i podzielić klon, aby umożliwić jego zapisywanie.
    * Komputer chroniony i serwer zapasowy są połączone z tą samą siecią.

1. Skonfiguruj opcje powiadomień.
1. Na stronie **potwierdzenie** wybierz pozycję **Odzyskaj**.

Aby uruchomić Kopia zapasowa systemu Windows Server:

1. Wybierz pozycję **Akcje**  >  **Odzyskaj**  >  **ten serwer**  >  **dalej**.

1. Wybierz **inny serwer**, wybierz stronę **Określ typ lokalizacji** , a następnie wybierz pozycję **zdalny folder udostępniony**. Wprowadź ścieżkę do folderu, który zawiera punkt odzyskiwania.

1. Na stronie **Wybieranie typu odzyskiwania** wybierz pozycję **stan systemu**.

1. Na stronie **Wybieranie lokalizacji dla odzyskiwania stanu systemu** wybierz opcję  **Oryginalna lokalizacja**.

1. Na stronie **potwierdzenie** wybierz pozycję **Odzyskaj**.

1. Po przywróceniu należy ponownie uruchomić serwer.

Można również uruchomić przywracanie stanu systemu w wierszu polecenia:

1. Uruchom Kopia zapasowa systemu Windows Server na komputerze, który chcesz odzyskać.

1. Aby uzyskać wersję identyfikator, w wierszu polecenia wpisz:

   ```wbadmin get versions -backuptarget \<servername\sharename\>```

1. Użyj identyfikatora wersji, aby uruchomić przywracanie stanu systemu. W wierszu polecenia wprowadź:

    ```wbadmin start systemstaterecovery -version:<versionidentified> -backuptarget:<servername\sharename>```

1. Potwierdź, że chcesz rozpocząć odzyskiwanie. Proces można zobaczyć w oknie wiersza polecenia. Tworzony jest dziennik przywracania.

1. Po przywróceniu należy ponownie uruchomić serwer.
