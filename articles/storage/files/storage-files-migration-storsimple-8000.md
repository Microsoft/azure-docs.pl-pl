---
title: Migracja serii StorSimple 8000 do Azure File Sync
description: Dowiedz się, jak migrować urządzenie z systemem StorSimple 8100 lub 8600 do Azure File Sync.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 03/09/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: be61a6e75c4aa9b5714ffbf3b4f19656b347c493
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2020
ms.locfileid: "91653251"
---
# <a name="storsimple-8100-and-8600-migration-to-azure-file-sync"></a>StorSimple 8100 i 8600 migracji do Azure File Sync

Seria StorSimple 8000 jest reprezentowana przez 8100 lub 8600 urządzenia fizyczne, lokalne i ich składniki usługi w chmurze. Możliwe jest Migrowanie danych z jednego z tych urządzeń do środowiska Azure File Syncowego. Azure File Sync to domyślna i strategiczna usługa platformy Azure, w ramach której można migrować urządzenia StorSimple.

Seria StorSimple 8000 osiągnie [koniec cyklu życia](https://support.microsoft.com/en-us/lifecycle/search?alpha=StorSimple%208000%20Series) w grudniu 2022. Ważne jest, aby rozpocząć planowanie migracji najszybciej, jak to możliwe. Ten artykuł zawiera informacje na temat niezbędnej wiedzy i migracji w celu pomyślnej migracji do Azure File Sync. 

## <a name="azure-file-sync"></a>Azure File Sync

> [!IMPORTANT]
> Firma Microsoft dokłada starań, aby pomóc klientom w migracji. Wyślij wiadomość e-mail AzureFilesMigration@microsoft . com dla niestandardowego planu migracji, a także pomoc podczas migracji.

Azure File Sync jest usługą firmy Microsoft w chmurze opartą na dwóch głównych składnikach:

* Synchronizacja plików i Obsługa warstw w chmurze.
* Udziały plików jako natywny magazyn na platformie Azure, do którego można uzyskać dostęp za pośrednictwem wielu protokołów, takich jak SMB i File REST. Udział plików platformy Azure jest porównywalny z udziałem plików w systemie Windows Server, który można natywnie zainstalować jako dysk sieciowy. Obsługuje ona ważne aspekty odtwarzania plików, takie jak atrybuty, uprawnienia i sygnatury czasowe. W przypadku udziałów plików platformy Azure nie jest już konieczne, aby aplikacja lub usługa mogła interpretować pliki i foldery przechowywane w chmurze. Możesz uzyskiwać dostęp do nich w sposób natywny za pomocą znanych protokołów i klientów, takich jak Eksplorator plików systemu Windows. Dzięki temu usługa Azure File udostępnia idealne i najbardziej elastyczne podejście do przechowywania danych serwera plików ogólnego przeznaczenia, a także niektórych danych aplikacji w chmurze.

Ten artykuł koncentruje się na krokach migracji. Jeśli przed migracją chcesz dowiedzieć się więcej o Azure File Sync, zalecamy następujące artykuły:

* [Azure File Sync — przegląd](https://aka.ms/AFS "Omówienie")
* [Azure File Sync — Przewodnik wdrażania](storage-sync-files-deployment-guide.md)

## <a name="migration-goals"></a>Cele migracji

Celem jest zagwarantowanie integralności danych produkcyjnych, a także zagwarantowania dostępności. Druga z nich wymaga utrzymywania przestoju w minimalnym stopniu, dzięki czemu może być dłuższa niż w zwykłych oknach obsługi lub tylko nieco.

## <a name="storsimple-8000-series-migration-path-to-azure-file-sync"></a>Ścieżka migracji serii StorSimple 8000 do Azure File Sync

Do uruchomienia agenta Azure File Sync jest wymagany lokalny serwer systemu Windows. System Windows Server może być co najmniej serwerem 2012R2, ale najlepiej jest w systemie Windows Server 2019.

Istnieje wiele alternatywnych ścieżek migracji, które mogłyby utworzyć zbyt dużo artykułu, aby udokumentować wszystkie z nich i zilustrować, dlaczego są one ryzykowne lub wady w stosunku do trasy zalecamy jako najlepsze rozwiązanie w tym artykule.

![Fazy migracji z serii StorSimple 8000](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-overview.png "Marszruta migracji z serii StorSimple 8000 — Omówienie faz w dalszej części tego artykułu.")

Poprzedni obraz przedstawia fazy, które odnoszą się do sekcji w tym artykule.
Używamy migracji po stronie chmury, aby uniknąć niepotrzebnych operacji odwoływania plików na lokalnym urządzeniu StorSimple. Takie podejście pozwala uniknąć wpływu na zachowanie lokalnej pamięci podręcznej lub wykorzystanie przepustowości sieci, co może mieć wpływ na obciążenia produkcyjne.
Migracja po stronie chmury działa na migawce (klonowanie woluminu) danych. W związku z tym dane produkcyjne są odizolowane od tego procesu — do momentu przecięcia na koniec migracji. Poza tym, co to jest kopia zapasowa, umożliwia bezpieczne i łatwe powtarzanie migracji w przypadku wystąpienia problemów.

## <a name="considerations-around-existing-storsimple-backups"></a>Zagadnienia dotyczące istniejących kopii zapasowych StorSimple

StorSimple umożliwia wykonywanie kopii zapasowych w formie klonów woluminów. W tym artykule jest używany nowy klon woluminu do migrowania plików na żywo.
Jeśli chcesz migrować kopie zapasowe oprócz danych na żywo, nadal będą obowiązywać wszystkie wskazówki zawarte w tym artykule. Jedyną różnicą jest to, że zamiast rozpoczynać się od nowego klonowania woluminu, należy rozpocząć od najstarszego klonu woluminu kopii zapasowej, który należy zmigrować.

Sekwencja jest następująca:

* Określ minimalny zestaw klonów woluminów, które należy zmigrować. Zalecamy pozostawienie tej listy minimalnym, jeśli jest to możliwe, ponieważ im więcej kopii zapasowych migrujesz dłuższy proces migracji.
* Podczas przechodzenia przez proces migracji należy zacząć od najstarszego klonu woluminu, który ma zostać zmigrowany, i w każdej kolejnej migracji użyć następnej najstarszej.
* Po zakończeniu migracji klonów woluminów należy wykonać migawkę udziału plików platformy Azure. [Migawki udziału plików platformy Azure](storage-snapshots-files.md) to sposób przechowywania kopii zapasowych plików i struktury folderów dla udziałów plików platformy Azure. Te migawki będą potrzebne po zakończeniu migracji, aby upewnić się, że wszystkie klony woluminów zostały zachowane w trakcie migracji.
* Upewnij się, że tworzysz migawki udziałów plików platformy Azure dla wszystkich udziałów plików platformy Azure, które są obsługiwane przez ten sam wolumin StorSimple. Klony woluminów znajdują się na poziomie woluminu, migawki udziałów plików platformy Azure znajdują się na poziomie udziału. Po zakończeniu migracji klonu woluminu należy wykonać migawkę udziału (w każdym udziale plików platformy Azure).
* Powtórz proces migracji woluminu i Utwórz migawki udziałów po każdym klonze woluminu, dopóki nie zostaną przechwycone do migawki danych na żywo. Proces migrowania klonu woluminu jest opisany w poniższych fazach. 

Jeśli nie ma potrzeby przenoszenia kopii zapasowych w ogóle i można uruchomić nowy łańcuch kopii zapasowych na stronie udział plików platformy Azure po zakończeniu migracji tylko danych na żywo, to jest korzystne, aby zmniejszyć złożoność migracji i czas trwania migracji. Możesz podjąć decyzję, czy przenieść kopie zapasowe i ile dla każdego woluminu (nie poszczególnych udziałów) masz w StorSimple.

## <a name="phase-1-get-ready"></a>Faza 1: przygotowanie

:::row:::
    :::column:::
        ![Obraz przedstawiający część wcześniejszego obrazu z omówieniem, który ułatwia skoncentrowanie się na tej podsekcji artykułu.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-1.png)
    :::column-end:::
    :::column:::
        Podstawą migracji jest klon woluminu i wirtualne urządzenie w chmurze o nazwie StorSimple 8020.
Ta faza koncentruje się na wdrażaniu tych zasobów na platformie Azure.
    :::column-end:::
:::row-end:::

### <a name="deploy-a-storsimple-8020-virtual-appliance"></a>Wdrażanie urządzenia wirtualnego z systemem StorSimple 8020

Wdrażanie urządzenia w chmurze to proces wymagający zabezpieczeń, sieci i kilku innych zagadnień.

> [!IMPORTANT]
> Poniższy przewodnik zawiera niektóre zbędne sekcje. Przeczytaj i wykonaj artykuł od początku do "krok 3". Następnie wróć do tego artykułu. W tej chwili nie trzeba wykonywać "kroku 3" ani niczego poza nim w tym przewodniku.

[Wdrażanie urządzenia wirtualnego z systemem StorSimple 8020](../../storsimple/storsimple-8000-cloud-appliance-u2.md)

### <a name="determine-a-volume-clone-to-use"></a>Określ klon woluminu do użycia

Gdy wszystko będzie gotowe do rozpoczęcia migracji, pierwszym krokiem jest utworzenie nowego klonu woluminu — podobnie jak w przypadku tworzenia kopii zapasowej — który przechwytuje bieżący stan magazynu w chmurze StorSimple. Dla każdego z woluminów StorSimple należy zastosować klon.
Jeśli potrzebujesz przenosić kopie zapasowe, to pierwszy użyty klon woluminu nie jest nowo utworzonym klonem, ale najstarszym woluminem klonowania (najstarszą kopią zapasową) należy migrować.
Szczegółowe wskazówki można znaleźć w sekcji ["zagadnienia dotyczące istniejących kopii zapasowych StorSimple"](#considerations-around-existing-storsimple-backups) .

> [!IMPORTANT]
> Poniższy przewodnik zawiera niektóre zbędne sekcje. Przeczytaj i postępuj zgodnie z krokami opisanymi w sekcji połączone z. Następnie wróć do tego artykułu. Nie trzeba wykonywać czynności opisanych w sekcji "następne kroki".

[Tworzenie klonu woluminu](../../storsimple/storsimple-8000-clone-volume-u2.md#create-a-clone-of-a-volume)

### <a name="use-the-volume-clone"></a>Używanie klonu woluminu

Ostatnim etapem fazy 1 jest dokonanie wybranego klonu woluminu dostępnego na urządzeniu wirtualnym 8020 na platformie Azure.

> [!IMPORTANT]
> Poniższy przewodnik zawiera kroki niezbędne do wykonania, ale również-na końcu — instrukcję formatowania woluminu. **nie Formatuj woluminu** Przeczytaj i postępuj zgodnie z sekcją "sekcja 7", która jest połączona z instrukcją rozpoczynania pracy aż do instrukcji: "10. Aby sformatować wolumin prosty,...  Zatrzymaj przed wykonaniem tego kroku i wróć do tego artykułu.

[Instalowanie klonu woluminu na urządzeniu wirtualnym 8020 na platformie Azure](../../storsimple/storsimple-8000-deployment-walkthrough-u2.md#step-7-mount-initialize-and-format-a-volume)

### <a name="phase-1-summary"></a>Podsumowanie fazy 1

Po zakończeniu fazy 1 wykonasz następujące czynności:

* Wdrożono urządzenie wirtualne z systemem StorSimple 8020 na platformie Azure.
* Określono, który klon woluminu ma rozpocząć migrację.
* Zainstalowano klony woluminów (po jednym dla każdego woluminu na żywo) do urządzenia wirtualnego StorSimple na platformie Azure z danymi, które są dostępne do dalszej pracy.

## <a name="phase-2-cloud-vm"></a>Faza 2: maszyna wirtualna w chmurze

:::row:::
    :::column:::
        ![Ilustracja przedstawiająca teraz czas udostępniania maszyny wirtualnej i Uwidacznianie klonu woluminu (lub wielu) dla tej maszyny wirtualnej za pośrednictwem protokołu iSCSI.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-2.png)
    :::column-end:::
    :::column:::
        Po udostępnieniu początkowego klonu na urządzeniu wirtualnym z systemem StorSimple 8020 na platformie Azure czas na zainicjowanie obsługi administracyjnej maszyny wirtualnej i uwidocznienie klonu woluminu (lub wielu) dla tej maszyny wirtualnej za pośrednictwem protokołu iSCSI.
    :::column-end:::
:::row-end:::

### <a name="deploy-an-azure-vm"></a>Wdróż maszynę wirtualną platformy Azure

Maszyna wirtualna z systemem Windows Server na platformie Azure jest taka sama jak StorSimple 8020, tymczasowy element infrastruktury, który jest niezbędny podczas migracji.
Konfiguracja wdrożonej maszyny wirtualnej zależy przede wszystkim od liczby elementów (plików i folderów), które zostaną zsynchronizowane. W przypadku jakichkolwiek problemów zalecamy przeprowadzenie wyższej konfiguracji wydajności.

Pojedynczy serwer systemu Windows może synchronizować do 30 udziałów plików platformy Azure.
Specyfikacje, które należy podjąć, muszą obejmować każdy udział/ścieżkę lub katalog główny woluminu StorSimple oraz liczbę elementów (plików i folderów).

Całkowity rozmiar danych jest mniejszy niż wąskie gardło — jest to liczba elementów potrzebnych do dopasowania specyfikacji maszyn.

* [Dowiedz się, jak zmienić rozmiar systemu Windows Server na podstawie liczby elementów (plików i folderów), które mają być synchronizowane.](storage-sync-files-planning.md#recommended-system-resources)

    **Uwaga:** W wcześniej połączonym artykule przedstawiono tabelę z zakresem dla pamięci serwera (RAM). Orientacja na dużą liczbę dla maszyny wirtualnej platformy Azure. Możesz przyróżnić do mniejszej liczby dla maszyny lokalnej.

* [Dowiedz się, jak wdrożyć maszynę wirtualną z systemem Windows Server.](../../virtual-machines/windows/quick-create-portal.md)

> [!IMPORTANT]
> Upewnij się, że maszyna wirtualna jest wdrożona w tym samym regionie platformy Azure co urządzenie wirtualne w systemie StorSimple 8020. Jeśli w ramach tej migracji trzeba również zmienić region danych w chmurze z regionu, w którym jest on przechowywany dzisiaj, można to zrobić na późniejszym etapie, gdy inicjujesz udziały plików platformy Azure.

> [!IMPORTANT]
> Często lokalny serwer systemu Windows jest używany do naprzód do lokalnego urządzenia StorSimple. W takiej konfiguracji można włączyć funkcję[deduplikacji danych](https://docs.microsoft.com/windows-server/storage/data-deduplication/install-enable)w systemie Windows Server. **W przypadku użycia deduplikacji danych z danymi StorSimple upewnij się, że włączono funkcję deduplikacji danych na tej maszynie wirtualnej platformy Azure.** Nie należy mylić deduplikacji na poziomie plików przy użyciu wbudowanej deduplikacji na poziomie bloku StorSimples, dla którego nie jest wymagana żadna akcja.

> [!IMPORTANT]
> Aby zoptymalizować wydajność, wdróż **szybki dysk systemu operacyjnego** dla maszyny wirtualnej w chmurze. Baza danych synchronizacji będzie przechowywana na dysku systemu operacyjnego dla wszystkich woluminów danych. Ponadto upewnij się, że tworzysz **duży dysk systemu operacyjnego**. W zależności od liczby elementów (plików i folderów) na woluminach StorSimple, dysk systemu operacyjnego może potrzebować **więcej setek GIB** miejsca, aby pomieścić bazę danych synchronizacji.

### <a name="expose-the-storsimple-8020-volumes-to-the-azure-vm"></a>Uwidacznianie woluminów StorSimple 8020 na maszynie wirtualnej platformy Azure

W tej fazie jest nawiązywane połączenie jednego lub kilku woluminów StorSimple z urządzenia wirtualnego 8020 za pośrednictwem protokołu iSCSI z zainicjowaną maszyną wirtualną z systemem Windows Server.

> [!IMPORTANT]
> W poniższych artykułach Uzupełnij tylko **prywatny adres IP dla urządzenia w chmurze** i **łącz się przez sekcje iSCSI** i wróć do tego artykułu.

1. [Uzyskiwanie prywatnego adresu IP urządzenia w chmurze](../../storsimple/storsimple-8000-cloud-appliance-u2.md#get-private-ip-for-the-cloud-appliance)
2. [Nawiązywanie połączenia za pośrednictwem protokołu iSCSI](../../storsimple/storsimple-8000-deployment-walkthrough-u2.md#step-7-mount-initialize-and-format-a-volume)

### <a name="phase-2-summary"></a>Podsumowanie fazy 2

Teraz, gdy faza 2 została ukończona, masz następujące: 

* Obsługa administracyjna maszyny wirtualnej z systemem Windows Server w tym samym regionie co 8020 wirtualne urządzenie StorSimple
* Wszystkie odpowiednie woluminy z 8020 do maszyny wirtualnej z systemem Windows Server za pośrednictwem protokołu iSCSI.
* Zawartość plików i folderów powinna być teraz wyświetlana, gdy Eksplorator plików jest używany na maszynie wirtualnej serwera na zainstalowanych woluminach.

Przechodzenie do fazy 3 należy wykonać dopiero po wykonaniu tych kroków dla wszystkich woluminów, które wymagają migracji.

## <a name="phase-3-set-up-azure-file-shares-and-get-ready-for-azure-file-sync"></a>Faza 3: Konfigurowanie udziałów plików platformy Azure i przygotowanie do Azure File Sync

:::row:::
    :::column:::
        ![Ilustracja przedstawiająca potrzebę ustalenia i aprowizacji kilku udziałów plików platformy Azure oraz utworzenia lokalnego systemu Windows Server jako StorSimple urządzenia.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-3.png)
    :::column-end:::
    :::column:::
        W tej fazie będzie można określić i zainicjować obsługę wielu udziałów plików platformy Azure, tworząc lokalnie system Windows Server jako zamiennik urządzenia StorSimple, a następnie skonfigurować ten serwer do Azure File Sync. 
    :::column-end:::
:::row-end:::

### <a name="map-your-existing-namespaces-to-azure-file-shares"></a>Mapowanie istniejących przestrzeni nazw do udziałów plików platformy Azure

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

### <a name="deploy-azure-file-shares"></a>Wdróż udziały plików platformy Azure

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

> [!TIP]
> Jeśli musisz zmienić region platformy Azure z bieżącego regionu, w którym znajdują się dane programu StorSimple, następnie Zainicjuj obsługę administracyjną udziałów plików platformy Azure w nowym regionie, którego chcesz użyć. Możesz określić region, wybierając go podczas aprowizacji kont magazynu, które przechowują udziały plików platformy Azure. Upewnij się, że Azure File Sync zasobu, który ma zostać udostępniony, jest w tym samym nowym regionie.

### <a name="deploy-the-azure-file-sync-cloud-resource"></a>Wdróż zasób w chmurze Azure File Sync

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

> [!TIP]
> Jeśli konieczna będzie zmiana regionu platformy Azure z bieżącego regionu, w którym znajdują się dane programu StorSimple, Zainicjowano obsługę kont magazynu dla udziałów plików platformy Azure w nowym regionie. Upewnij się, że wybrano ten sam region podczas wdrażania tej usługi synchronizacji magazynu.

### <a name="deploy-an-on-premises-windows-server"></a>Wdrażanie lokalnego serwera z systemem Windows

* Utwórz system Windows Server 2019 — co najmniej 2012R2 maszyny wirtualnej lub serwera fizycznego. Obsługiwany jest również klaster trybu failover systemu Windows Server. Nie używaj ponownie serwera, który może pozostawać z StorSimple 8100 lub 8600.
* Udostępnianie lub Dodawanie bezpośredniego dołączonego magazynu (DAS w porównaniu z serwerem NAS, co nie jest obsługiwane).

Najlepszym rozwiązaniem jest nadanie nowemu serwerowi systemu Windows większej lub większej ilości miejsca w magazynie niż Urządzenie StorSimple 8100 lub 8600, które jest lokalnie dostępne do buforowania. Użyjesz systemu Windows Server w taki sam sposób, jak w przypadku użycia urządzenia StorSimple, jeśli ma on taką samą ilość magazynu jak urządzenie, środowisko pamięci podręcznej powinno być podobne, jeśli nie jest takie samo.
Możesz dodać lub usunąć magazyn z systemu Windows Server w systemie. Umożliwia to skalowanie rozmiaru woluminu lokalnego i ilości magazynu lokalnego dostępnego do buforowania.

### <a name="prepare-the-windows-server-for-file-sync"></a>Przygotowanie systemu Windows Server do synchronizacji plików

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

### <a name="configure-azure-file-sync-on-the-windows-server"></a>Konfigurowanie Azure File Sync w systemie Windows Server

Zarejestrowany lokalny serwer systemu Windows musi być gotowy i połączony z Internetem w ramach tego procesu.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!WARNING]
> **Upewnij się, że włączona jest obsługa warstw w chmurze!** Obsługa warstw w chmurze to funkcja AFS, która umożliwia serwerowi lokalnemu przechowywanie mniejszej pojemności niż w chmurze, ale pełna przestrzeń nazw jest dostępna. Lokalnie interesujące dane są również buforowane lokalnie w celu szybkiej, lokalnej wydajności dostępu. Kolejną przyczyną włączenia obsługi warstw w chmurze w tym kroku jest to, że nie chcemy synchronizować zawartości pliku na tym etapie. w tej chwili należy przenieść tylko przestrzeń nazw.

## <a name="phase-4-configure-the-azure-vm-for-sync"></a>Faza 4: Konfigurowanie maszyny wirtualnej platformy Azure do synchronizacji

:::row:::
    :::column:::
        ![Ilustracja przedstawiająca sposób uzyskania maszyny wirtualnej połączonej za pośrednictwem Azure File Sync i rozpoczęcie pierwszego przenoszonego pliku z klonów woluminów StorSimple.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-4.png)
    :::column-end:::
    :::column:::
        Ta faza dotyczy maszyny wirtualnej platformy Azure z zainstalowanym interfejsem iSCSI, a pierwsze klony woluminów. W tej fazie zostanie nawiązane połączenie z maszyną wirtualną za pośrednictwem Azure File Sync i rozpocznie się pierwsze przechodzenie plików z klonów woluminów StorSimple.
        
    :::column-end:::
:::row-end:::

Skonfigurowano już serwer lokalny, który zamieni urządzenie z systemem StorSimple 8100 lub 8600 w celu Azure File Sync. 

Konfigurowanie maszyny wirtualnej platformy Azure jest niemal identycznym procesem z jednym dodatkowym krokiem. Poniższe kroki przeprowadzą Cię przez proces.

> [!IMPORTANT]
> Należy pamiętać, że maszyna wirtualna platformy Azure **nie jest skonfigurowana z włączoną obsługą warstw w chmurze** . Program będzie wymieniał wolumin tego serwera za pomocą nowszych klonów woluminów podczas migracji. Obsługa warstw w chmurze nie wiąże się z żadną korzyścią dla użycia procesora CPU.

1. [Wdróż agenta AFS. (zobacz poprzednią sekcję)](#prepare-the-windows-server-for-file-sync)
2. [Przygotowywanie maszyny wirtualnej do Azure File Sync.](#get-the-vm-ready-for-azure-file-sync)
3. [Konfigurowanie synchronizacji](#configure-azure-file-sync-on-the-azure-vm)

### <a name="get-the-vm-ready-for-azure-file-sync"></a>Przygotuj maszynę wirtualną do Azure File Sync

Azure File Sync służy do przenoszenia plików z zainstalowanych woluminów StorSimple iSCSI do docelowych udziałów plików platformy Azure.
W trakcie tego procesu migracji kilka klonów woluminów zostanie zainstalowanych na maszynie wirtualnej, pod tą samą literą dysku. Azure File Sync należy skonfigurować w taki sposób, aby zobaczyć następny sklonowany wolumin, który został zainstalowany jako nowszą wersję plików i folderów, i zaktualizować udziały plików platformy Azure połączone za pośrednictwem Azure File Sync. 

> [!IMPORTANT]
> Aby to umożliwić, należy ustawić na serwerze klucz rejestru przed skonfigurowaniem Azure File Sync.

1. Utwórz nowy katalog na dysku systemowym maszyny wirtualnej. Informacje Azure File Sync muszą zostać utrwalone w tym miejscu zamiast na woluminach klonów zainstalowanych woluminów. Na przykład: `"C:\syncmetadata"`
2. Otwórz regedit i znajdź następującą gałąź rejestru: `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync`
3. Utwórz nowy klucz typu String o nazwie: ***MetadataRootPath***
4. Ustaw pełną ścieżkę do katalogu utworzonego na woluminie systemowym, na przykład: `C:\syncmetadata"`

### <a name="configure-azure-file-sync-on-the-azure-vm"></a>Konfigurowanie Azure File Sync na maszynie wirtualnej platformy Azure

Ten krok jest podobny do poprzedniej sekcji, w której opisano sposób konfigurowania programu AFS na serwerze lokalnym.

Różnica polega na tym, że nie należy włączać obsługi warstw w chmurze na tym serwerze i należy upewnić się, że odpowiednie foldery są połączone z właściwymi udziałami plików platformy Azure. W przeciwnym razie nazwy udziałów plików platformy Azure i zawartości danych nie będą zgodne i nie ma możliwości zmiany nazwy zasobów w chmurze lub folderów lokalnych bez ponownego skonfigurowania synchronizacji.

Zapoznaj się z [poprzednią sekcją dotyczącą konfigurowania Azure File Sync na serwerze z systemem Windows](#configure-azure-file-sync-on-the-windows-server).

### <a name="step-4-summary"></a>Krok 4 — podsumowanie

Na tym etapie pomyślnie skonfigurowano Azure File Sync na maszynie wirtualnej platformy Azure, w której zainstalowano klony woluminów StorSimple za pośrednictwem protokołu iSCSI.
Dane są teraz przepływane z maszyny wirtualnej platformy Azure do różnych udziałów plików platformy Azure, a z tego miejsca jest wyświetlana w pełni niepełna przestrzeń nazw na lokalnym serwerze Windows.

> [!IMPORTANT]
> Upewnij się, że w tej chwili nie wprowadzono żadnych zmian lub dostęp użytkownika do systemu Windows Server.

Początkowe woluminy klonowania danych przechodzących przez maszynę wirtualną platformy Azure do udziałów plików platformy Azure może zająć dużo czasu, co potencjalnie tygodnie. Oszacowanie czasu trwania tej operacji jest czasochłonne i zależy od wielu czynników. W szczególności szybkość, z jaką maszyna wirtualna platformy Azure może uzyskać dostęp do plików na woluminach StorSimple oraz jak szybkie Azure File Sync mogą przetwarzać pliki i foldery wymagające synchronizacji. 

Ze środowiska można założyć, że przepustowość — w związku z rzeczywistym rozmiarem danych — odgrywa rolę podrzędną. Czas trwania tej operacji lub wszelkie kolejne operacje dokonywania migracji są zwykle zależne od liczby elementów, które mogą być przetwarzane na sekundę. Więc przykładowo 1 TiB z 100 000 plikami i folderami najprawdopodobniej zakończy się wolniej niż 1 TiB z tylko 50 000 plikami i folderami.

## <a name="phase-5-iterate-through-multiple-volume-clones"></a>Faza 5: iteracja wielu klonów woluminów

:::row:::
    :::column:::
        ![Ilustracja przedstawiająca sposób minimalizowania przestojów przy użyciu wielu klonów woluminów i informujących o zakończeniu synchronizacji.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-5.png)
    :::column-end:::
    :::column:::
        Zgodnie z opisem w poprzedniej fazie synchronizacja początkowa może zająć dużo czasu. Użytkownicy i aplikacje nadal uzyskują dostęp do lokalnego urządzenia StorSimple 8100 lub 8600. Oznacza to, że zmiany są kumulowane i każdy dzień jest większą różnicą między danymi na żywo i początkowym klonem woluminu, obecnie migracji, formularzy. Ta sekcja zawiera informacje na temat minimalizowania przestojów przy użyciu wielu klonów woluminów i informujących o zakończeniu synchronizacji.
    :::column-end:::
:::row-end:::

Niestety, proces migracji nie jest chwilowo. Oznacza to, że wyżej wymienione zmiany danych na żywo są nieuniknione. Dobra wiadomość polega na tym, że można powtórzyć proces instalowania nowych klonów woluminów. Przyrosty poszczególnych klonów woluminów będą stopniowo mniejsze. W związku z tym synchronizacja zakończy się w czasie, w którym można rozważyć zaakceptowanie, aby przełączać użytkowników i aplikacje do trybu offline na lokalnym serwerze systemu Windows.

Wykonaj następujące kroki, dopóki synchronizacja nie zakończy się w wystarczająco szybkim czasie, że użytkownicy i aplikacje są w trybie offline:

1. [Należy określić synchronizację dla danego klonu woluminu.](#determine-when-sync-is-done)
2. [Utwórz nowe klony woluminów i zainstaluj je na urządzeniu wirtualnym 8020.](#the-next-volume-clones)
3. [Określ, kiedy synchronizacja została ukończona.](#determine-when-sync-is-done)
4. [Strategia wycinania](#cut-over-strategy)

### <a name="the-next-volume-clones"></a>Kolejne klony woluminów

W tym artykule omówiono tworzenie klonów woluminów wcześniej.
Ta faza obejmuje dwie akcje:

1. [Utwórz klon woluminu](../../storsimple/storsimple-8000-clone-volume-u2.md#create-a-clone-of-a-volume)
2. [Zainstaluj ten klon woluminu (patrz powyżej)](#use-the-volume-clone)

### <a name="determine-when-sync-is-done"></a>Ustalanie, kiedy synchronizacja jest wykonywana

Po zakończeniu synchronizacji można zatrzymać pomiar czasu i określić, czy należy powtórzyć proces tworzenia klonowania woluminu i instalowania go, czy też gdy synchronizacja czasu trwała z ostatnim klonem woluminu była wystarczająco mała.

Aby można było ustalić synchronizację, należy wykonać następujące czynności:

1. Otwórz Podgląd zdarzeń i przejdź do **aplikacji i usług**
2. Nawigowanie i otwieranie **Microsoft\FileSync\Agent\Telemetry**
3. Wyszukaj najnowsze **zdarzenie 9102**, które odnosi się do zakończonej sesji synchronizacji
4. Wybierz pozycję **szczegóły** i upewnij się, że wartość **SyncDirection** jest **przekazywana**
5. Sprawdź **wynik HRESULT** i Potwierdź, że jest wyświetlana **wartość 0**. Oznacza to, że sesja synchronizacji zakończyła się pomyślnie. Jeśli HResult ma wartość różną od zera, podczas synchronizacji Wystąpił błąd. Jeśli wartość **PerItemErrorCount** jest większa od 0, niektóre pliki lub foldery nie zostały prawidłowo zsynchronizowane. Istnieje możliwość, że wynik HResult równy 0, ale PerItemErrorCount jest większy niż 0. W tym momencie nie trzeba martwić się o PerItemErrorCount. Te pliki będą przechwytywane później. Jeśli ta liczba błędów jest istotna, tysiące elementów, skontaktuj się z działem pomocy technicznej i poproś o połączenie z grupą produktów Azure File Sync, aby uzyskać bezpośrednie wskazówki dotyczące najlepszych, następnych faz.
6. Zaznacz, aby zobaczyć wiele zdarzeń 9102 z wartością HResult 0 w wierszu. Wskazuje to, że ukończono synchronizację dla tego klonu woluminu.

### <a name="cut-over-strategy"></a>Strategia wycinania

1. Ustal, czy synchronizacja z sklonowanego woluminu jest teraz wystarczająco szybka. (Wystarczająco małe różnice).
2. Przełącz Urządzenie StorSimple do trybu offline.
3. Końcowy RoboCopy.

Zmierz czas i ustal, czy synchronizacja z ostatniego klonu woluminu może zakończyć się w przedziale czasowym wystarczająco małym, co pozwala na zapewnienie przestoju w systemie.

Teraz można wyłączyć dostęp użytkownika do urządzenia StorSimple. Nie ma więcej zmian. Rozpoczęto przestoje.
Należy pozostawić urządzenie w trybie online i połączyć się z nim, ale muszą one teraz uniemożliwiać wprowadzanie na nim zmian.

W fazie 6 przechwytuje się wszystkie zmiany danych na żywo od ostatniego klonowania woluminu.

## <a name="phase-6-a-final-robocopy"></a>Faza 6: RoboCopy końcowy

W tym momencie istnieją dwie różnice między lokalnym serwerem Windows i urządzeniem StorSimple 8100 lub 8600:

1. Mogą istnieć pliki, które nie zostały zsynchronizowane (zobacz **PerItemErrors** z dziennika zdarzeń powyżej)
2. Urządzenie StorSimple ma wypełnioną pamięć podręczną a w systemie Windows Server tylko przestrzeń nazw bez zawartości pliku przechowywanej lokalnie.

![Ilustracja przedstawiająca sposób, w jaki pamięć podręczna systemu Windows Server była przenoszona do stanu urządzenia i nie gwarantuje, że żaden plik nie został pozostawiony w końcowej RoboCopy.](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-6.png)

Możemy przełączyć pamięć podręczną systemu Windows Server do stanu urządzenia i upewnić się, że żaden plik nie został pozostawiony w RoboCopy końcowym.

> [!CAUTION]
> Jest to konieczne, aby poniższe polecenie RoboCopy zostało dokładnie opisane poniżej. Chcemy skopiować pliki, które są lokalne i pliki, które nie zostały przeniesione przez metodę klonowania i synchronizacji woluminu. Możemy rozwiązać problemy, dlaczego nie zostały zsynchronizowane później, po zakończeniu migracji. (Zobacz [Azure File Sync Rozwiązywanie problemów](storage-sync-files-troubleshoot.md#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing). Najprawdopodobniej nie można drukować znaków w nazwach plików, które nie zostaną pominięte po ich usunięciu.

RoboCopy polecenie:

```console
Robocopy /MT:32 /UNILOG:<file name> /TEE /B /MIR /COPYALL /DCOPY:DAT <SourcePath> <Dest.Path>
```

Tle

:::row:::
   :::column span="1":::
      /MT
   :::column-end:::
   :::column span="1":::
      Zezwala na uruchamianie wielowątkowości przez RoboCopy. Wartość domyślna to 8, wartość maksymalna to 128.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /UNILOG:<file name>
   :::column-end:::
   :::column span="1":::
      Wyprowadza stan do pliku dziennika jako UNICODE (Zastępuje istniejący dziennik).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /TEE
   :::column-end:::
   :::column span="1":::
      Dane wyjściowe do okna konsoli. Używane w połączeniu z danymi wyjściowymi do pliku dziennika.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /B
   :::column-end:::
   :::column span="1":::
      Uruchamia RoboCopy w tym samym trybie, w którym będzie używana aplikacja kopii zapasowej. Umożliwia RoboCopy przenoszenie plików, do których bieżący użytkownik nie ma uprawnień.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /MIR
   :::column-end:::
   :::column span="1":::
      Zezwala RoboCopy na uwzględnienie różnic między źródłem (urządzeniem StorSimple) a celem (katalogiem systemu Windows Server).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPY: copyflag [s]
   :::column-end:::
   :::column span="1":::
      wierność kopiowania plików (wartość domyślna to/COPY: DAT), Copy flags: D = Data, A = Attributes, T = Timestamps, S = Security = NTFS list ACL, O = Owner info, U = informacje o inspekcji
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      / COPYALL
   :::column-end:::
   :::column span="1":::
      Kopiuj wszystkie informacje o pliku (równoważne do/COPY: DATSOU)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /DCOPY: copyflag [s]
   :::column-end:::
   :::column span="1":::
      wierność kopiowania katalogów (wartość domyślna to/DCOPY: DA), Copy flags: D = Data, A = Attributes, T = Timestamps
   :::column-end:::
:::row-end:::

Należy uruchomić to polecenie RoboCopy dla każdego katalogu w systemie Windows Server jako element docelowy, który został skonfigurowany z synchronizacją plików do pliku platformy Azure.

Można uruchomić wiele z tych poleceń równolegle.
Po ukończeniu tego kroku RoboCopy można zezwolić użytkownikom i aplikacjom na dostęp do systemu Windows Server, takich jak urządzenie StorSimple.

Zapoznaj się z plikiem dziennika Robocopy, aby zobaczyć, czy pliki zostały pozostawione w tle. Jeśli występują problemy, w większości przypadków można je rozwiązać po zakończeniu migracji, a użytkownicy i aplikacje zostały odinstalowane na serwerze z systemem Windows. Jeśli trzeba rozwiązać wszelkie problemy, zrób to przed fazą 7.

Prawdopodobnie trzeba będzie utworzyć udziały SMB na serwerze z systemem Windows, który wcześniej miał dane StorSimple. Możesz Załaduj ponownie ten krok i zrobić go wcześniej, aby nie stracić czasu w tym miejscu, ale musisz upewnić się, że przed tym punktem nie wystąpią żadne zmiany w plikach w systemie Windows Server.

Jeśli masz wdrożenie systemu plików DFS-N, możesz wskazać DFN przestrzeni nazw do nowych lokalizacji folderu serwera. Jeśli nie masz wdrożenia systemu plików DFS-N i masz lokalne urządzenie 8100 8600 z systemem Windows Server, możesz posłużyć się tym serwerem poza domeną, a domena przyłączyć się do nowego serwera z systemem Windows przy użyciu platformy AFS z domeną, nadać tej samej nazwie serwera co stary serwer i te same nazwy udziałów, a następnie przecinanie do nowego serwera pozostanie niewidoczne dla użytkowników. , zasady grupy lub skrypty.

## <a name="phase-7-deprovision"></a>Faza 7: anulowanie aprowizacji

W ostatniej fazie powtarzanej przez wiele klonów woluminów, a ostatecznie było możliwe wycinanie dostępu użytkowników do nowego serwera systemu Windows po przełączeniu urządzenia w tryb offline.

Teraz możesz rozpocząć cofanie aprowizacji niepotrzebnych zasobów.
Przed rozpoczęciem najlepiej obserwować nowe wdrożenie Azure File Sync w środowisku produkcyjnym. Zapewnia opcje rozwiązania problemów, które mogą wystąpić.

Gdy zaobserwujesz wdrożenie AFS przez co najmniej kilka dni, możesz rozpocząć cofanie aprowizacji zasobów w następującej kolejności:

1. Wyłącz maszynę wirtualną platformy Azure, która była używana do przenoszenia danych z klonów woluminów do udziałów plików platformy Azure za pośrednictwem funkcji synchronizacji plików.
2. Przejdź do zasobu usługi synchronizacji magazynu na platformie Azure i Wyrejestruj maszynę wirtualną platformy Azure. Spowoduje to usunięcie go ze wszystkich grup synchronizacji.

    > [!WARNING]
    > **Upewnij się, że wybrano odpowiednią maszynę.** Wyłączono maszynę wirtualną w chmurze, co oznacza, że powinna być wyświetlana jako jedyny serwer offline na liście zarejestrowanych serwerów. W tym kroku nie wolno wybierać lokalnego systemu Windows Server, więc spowoduje to Wyrejestrowanie.

3. Usuń maszynę wirtualną platformy Azure i jej zasoby.
4. Wyłącz urządzenie wirtualnej StorSimple 8020.
5. Anulowanie aprowizacji wszystkich zasobów StorSimple na platformie Azure.
6. Odłącz urządzenie fizyczne StorSimple od centrum danych.

Migracja została ukończona.

## <a name="next-steps"></a>Następne kroki

Poznaj Azure File Sync. Szczególnie w przypadku elastyczności zasad obsługi warstw w chmurze.

Jeśli zobaczysz w Azure Portal lub z wcześniejszych zdarzeń, że niektóre pliki nie są synchronizowane, zapoznaj się z przewodnikiem rozwiązywania problemów, aby rozwiązać te problemy.

* [Przegląd Azure File Sync: aka.ms/AFS](https://aka.ms/AFS)
* [Obsługa warstw w chmurze](storage-sync-cloud-tiering.md) 
* [Przewodnik rozwiązywania problemów Azure File Sync](storage-sync-files-troubleshoot.md)
