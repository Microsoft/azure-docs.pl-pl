---
title: Migracja serii StorSimple 8000 do Azure File Sync
description: Dowiedz się, jak migrować urządzenie z systemem StorSimple 8100 lub 8600 do Azure File Sync.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 10/16/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: dcb690829aaed1d5a256f53766f05c922f8b2a9a
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2020
ms.locfileid: "92331118"
---
# <a name="storsimple-8100-and-8600-migration-to-azure-file-sync"></a>StorSimple 8100 i 8600 migracji do Azure File Sync

Seria StorSimple 8000 jest reprezentowana przez 8100 lub 8600 urządzenia fizyczne, lokalne i ich składniki usługi w chmurze. Możliwe jest Migrowanie danych z jednego z tych urządzeń do środowiska Azure File Syncowego. Azure File Sync to domyślna i strategiczna usługa platformy Azure, w ramach której można migrować urządzenia StorSimple.

Seria StorSimple 8000 osiągnie [koniec cyklu życia](https://support.microsoft.com/en-us/lifecycle/search?alpha=StorSimple%208000%20Series) w grudniu 2022. Ważne jest, aby rozpocząć planowanie migracji najszybciej, jak to możliwe. Ten artykuł zawiera informacje na temat niezbędnej wiedzy i migracji w celu pomyślnej migracji do Azure File Sync. 

## <a name="phase-1-prepare-for-migration"></a>Faza 1: przygotowanie do migracji

Ta sekcja zawiera kroki, które należy wykonać na początku migracji z woluminów StorSimple do udziałów plików platformy Azure.

### <a name="inventory"></a>Stan zapasów
Po rozpoczęciu planowania migracji należy najpierw zidentyfikować wszystkie urządzenia StorSimple i woluminy, które mają być migrowane. Po wykonaniu tych czynności możesz zdecydować o najlepszej ścieżce migracji.

* Urządzenia fizyczne StorSimple (seria 8000) używają tego przewodnika migracji. 
* Urządzenia wirtualne, [Seria StorSimple 1200, użyj innego przewodnika migracji](storage-files-migration-storsimple-1200.md).

### <a name="migration-cost-summary"></a>Podsumowanie kosztów migracji
Migracje do udziałów plików platformy Azure z woluminów StorSimple za pośrednictwem zadań transformacji danych w ramach zasobu StorSimple Data Manager są bezpłatne. Istnieją jednak inne koszty, które mogą być naliczane podczas migracji:

* Ruch wychodzący z **sieci:** Twoje pliki StorSimple na żywo na koncie magazynu w ramach określonego regionu świadczenia usługi Azure. Jeśli udostępniasz udziały plików platformy Azure migrowane w ramach konta magazynu, które znajduje się w tym samym regionie świadczenia usługi Azure, nie zostanie nadany koszt wychodzący. Pliki można przenieść na konto magazynu w innym regionie w ramach tej migracji. W takim przypadku zostaną zastosowane koszty wychodzące.
* **Transakcje udziału plików platformy Azure:** Gdy pliki są kopiowane do udziału plików platformy Azure (w ramach migracji lub poza jedną), koszty transakcji są naliczane w miarę zapisywania plików i metadanych. Najlepszym rozwiązaniem jest uruchomienie udziału plików platformy Azure w warstwie zoptymalizowanej pod kątem transakcji podczas migracji. Po zakończeniu migracji przejdź do odpowiedniej warstwy. Fazy poniżej spowodują wywołanie tej metody w odpowiednim punkcie.
* **Zmiana warstwy udziału plików platformy Azure:** Zmiana warstwy kosztów transakcji udziałów plików platformy Azure. W większości przypadków będzie tańsza, aby postępować zgodnie z poradą podaną w poprzednim punkcie.
* **Koszt magazynu:** Gdy migracja rozpocznie kopiowanie plików do udziału plików platformy Azure, usługa Azure Files Storage zostanie zużyta i rozliczona. 
* **StorSimple:** Do momentu, gdy nie masz możliwości anulowania aprowizacji urządzeń StorSimple i kont magazynu, StorSimple koszty magazynu, kopii zapasowych i urządzeń będą nadal wykonywane.

### <a name="direct-share-access-vs-azure-file-sync"></a>Bezpośredni dostęp do udostępniania a Azure File Sync
Udziały plików platformy Azure otwierają zupełnie nowy świat sprzedaży do tworzenia struktury wdrożenia usług plików. Udział plików platformy Azure to tylko udział SMB w chmurze, który można skonfigurować w taki sposób, aby użytkownicy mieli dostęp bezpośrednio za pośrednictwem protokołu SMB przy użyciu znanego uwierzytelniania Kerberos i istniejących uprawnień systemu plików NTFS (list ACL plików i folderów) pracujących natywnie. [Dowiedz się więcej na temat dostępu opartego na tożsamościach do udziałów plików platformy Azure](storage-files-active-directory-overview.md).

Alternatywą dla dostępu bezpośredniego jest [Azure File Sync](https://aka.ms/AFS). Azure File Sync to bezpośrednie analogowe StorSimple do buforowania często używanych plików lokalnie. 

Azure File Sync jest usługą firmy Microsoft w chmurze opartą na dwóch głównych składnikach:

* Synchronizacja plików i Obsługa warstw w chmurze.
* Udziały plików jako natywny magazyn na platformie Azure, do którego można uzyskać dostęp za pośrednictwem wielu protokołów, takich jak SMB i File REST. 

Udziały plików platformy Azure zachowują ważne aspekty odtwarzania plików dotyczące przechowywanych plików, takich jak atrybuty, uprawnienia i sygnatury czasowe. W przypadku udziałów plików platformy Azure nie jest już konieczne, aby aplikacja lub usługa mogła interpretować pliki i foldery przechowywane w chmurze. Możesz uzyskiwać dostęp do nich w sposób natywny za pomocą znanych protokołów i klientów, takich jak Eksplorator plików systemu Windows. Udziały plików platformy Azure umożliwiają przechowywanie danych i danych aplikacji serwera plików ogólnego przeznaczenia w chmurze. Tworzenie kopii zapasowej udziału plików platformy Azure jest wbudowaną funkcją i można ją dodatkowo rozszerzyć przez Azure Backup.

Ten artykuł koncentruje się na krokach migracji. Jeśli przed migracją chcesz dowiedzieć się więcej o Azure File Sync, zalecamy następujące artykuły:

* [Azure File Sync — przegląd](https://aka.ms/AFS "Omówienie")
* [Azure File Sync — Przewodnik wdrażania](storage-sync-files-deployment-guide.md)

### <a name="storsimple-service-data-encryption-key"></a>Klucz szyfrowania danych usługi StorSimple
Po raz pierwszy skonfigurujesz urządzenie z systemem StorSimple, wygenerowało "*klucz szyfrowania danych usługi*" i nakazujesz bezpieczne przechowywanie klucza.
Ten klucz służy do szyfrowania wszystkich danych na skojarzonym koncie usługi Azure Storage, na którym Urządzenie StorSimple przechowuje Twoje pliki.

"Klucz szyfrowania danych usługi" jest niezbędny do pomyślnej migracji. Teraz warto pobrać ten klucz z rekordów dla każdego urządzenia w spisie.

Jeśli nie możesz znaleźć kluczy w rekordach, możesz pobrać klucz z urządzenia. Każde urządzenie ma unikatowy klucz szyfrowania.

* Prześlij żądanie pomocy technicznej za pomocą Microsoft Azure przez Azure Portal. Zawartość żądania powinna mieć numery seryjne urządzenia StorSimple oraz żądanie pobrania klucza szyfrowania danych usługi. 
* Inżynier pomocy technicznej StorSimple skontaktuje się z Tobą przy użyciu żądania spotkania dotyczącego udostępniania ekranu.
* Upewnij się, że przed rozpoczęciem spotkania zostanie nawiązane połączenie z urządzeniem StorSimple [za pośrednictwem konsoli szeregowej](../../storsimple/storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console) lub za pośrednictwem [zdalnej sesji programu PowerShell](../../storsimple/storsimple-8000-windows-powershell-administration.md#connect-remotely-to-storsimple-using-windows-powershell-for-storsimple). 

> [!CAUTION]
> Podczas decydowania o sposobie nawiązywania połączenia z urządzeniem StorSimple należy wziąć pod uwagę następujące kwestie:
> * Nawiązywanie połączenia za pośrednictwem sesji HTTPS jest najbezpieczniejszym i zalecanym rozwiązaniem.
> * Bezpośrednie łączenie z konsolą szeregową urządzenia jest bezpieczne, ale połączenie z konsolą szeregową przez przełączniki sieciowe nie jest obsługiwane. 
> * Połączenia sesji HTTP są opcjami, ale **nie są szyfrowane** i dlatego nie są zalecane, chyba że są używane w programie w zamkniętej, zaufanej sieci.

### <a name="storsimple-volume-backups"></a>Kopie zapasowe woluminów StorSimple
StorSimple oferuje różnicowe kopie zapasowe na poziomie woluminu. Udziały plików platformy Azure mają również taką możliwość, nazywaną migawkami udziałów.

Zdecyduj, czy jako część migracji masz również obowiązek przenoszenia wszelkich kopii zapasowych.

> [!CAUTION]
> Zatrzymaj tutaj, jeśli musisz migrować kopie zapasowe z woluminów StorSimple.
>
> Obecnie można migrować tylko najnowszą kopię zapasową woluminu. Obsługa migracji kopii zapasowych zostanie zakończona na końcu 2020.
> Jeśli rozpoczniesz pracę teraz, nie możesz wykonać kopii zapasowych później.
> W nadchodzącej wersji kopie zapasowe muszą być "odtwarzane" w udziałach plików platformy Azure od najstarszych do najnowszych, dzięki czemu migawki udziałów plików platformy Azure są wykonywane między.

Aby przeprowadzić migrację wyłącznie danych na żywo i nie ma wymagań dotyczących tworzenia kopii zapasowych, można kontynuować działanie opisane w tym przewodniku.
Jeśli masz tylko krótkoterminowe wymagania dotyczące przechowywania kopii zapasowych, co miesiąc lub dwa, możesz zdecydować, aby kontynuować migrację teraz i anulować Inicjowanie obsługi zasobów StorSimple po tym okresie. Takie podejście umożliwia utworzenie tak dużej ilości historii kopii zapasowych na stronie udział plików platformy Azure. W czasie, gdy oba systemy są uruchomione, obowiązują dodatkowe koszty, co nie jest podejściem, które należy wziąć pod uwagę, jeśli potrzebujesz więcej niż krótkoterminowe przechowywanie kopii zapasowych.

### <a name="map-your-existing-storsimple-volumes-to-azure-file-shares"></a>Mapuj istniejące woluminy StorSimple do udziałów plików platformy Azure
[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

### <a name="number-of-storage-accounts"></a>Liczba kont magazynu
Migracja będzie prawdopodobnie korzystać z wdrożenia wielu kont magazynu, w przypadku których każda z nich utrzymuje mniejszą liczbę udziałów plików platformy Azure.

Jeśli Twoje udziały plików są wysoce aktywne (wykorzystane przez wielu użytkowników i/lub aplikacje), tylko dwa udziały plików platformy Azure mogą osiągnąć limit wydajności konta magazynu. W związku z tym najlepszym rozwiązaniem jest przeprowadzenie migracji na wiele kont magazynu, z których każdy korzysta z poszczególnych udziałów plików, a zwykle nie więcej niż dwa lub trzy udziały na konto magazynu.

Najlepszym rozwiązaniem jest wdrożenie kont magazynu z jednym udziałem plików. Można umieścić wiele udziałów plików platformy Azure w ramach tego samego konta magazynu, na wypadek gdy w nich są udostępnione udziały.

Te zagadnienia dotyczą tylko [bezpośredniego dostępu do chmury](#direct-share-access-vs-azure-file-sync) (za pośrednictwem maszyny wirtualnej lub usługi platformy Azure), niż Azure File Sync. Jeśli planujesz używać Azure File Sync tylko w tych udziałach, to zgrupowanie kilku na jedno konto usługi Azure Storage jest odpowiednie. Należy również wziąć pod uwagę, że możesz chcieć podnieść i przenieść aplikację do chmury, która następnie będzie mogła uzyskać bezpośredni dostęp do udziału plików. Możesz też zacząć korzystać z usługi na platformie Azure, która również może mieć większe liczby operacji we/wy i przepływności. 

Jeśli utworzono listę udziałów, należy zmapować każdy udział na konto magazynu, w którym będzie się znajdować.

> [!IMPORTANT]
> Upewnij się, że wybierasz region świadczenia usługi Azure, a następnie każde konto magazynu i zasób Azure File Sync są zgodne z wybranym regionem.

### <a name="phase-1-summary"></a>Podsumowanie fazy 1
Na końcu fazy 1:
* Masz dobry przegląd urządzeń i woluminów StorSimple.
* Usługa przekształcania danych jest gotowa do uzyskiwania dostępu do woluminów StorSimple w chmurze, ponieważ klucz szyfrowania danych usługi został pobrany dla każdego urządzenia StorSimple.
* Istnieje plan nie tylko tego, które woluminy muszą zostać zmigrowane, ale również jak mapować woluminy na odpowiednią liczbę udziałów plików platformy Azure i kont magazynu.

> [!CAUTION]
> Jeśli musisz migrować kopie zapasowe z woluminów StorSimple, **Zatrzymaj to tutaj**.
>
> To podejście migracji opiera się na nowych możliwościach usługi przekształcania danych, które obecnie nie mogą migrować kopii zapasowych. Obsługa migracji kopii zapasowych zostanie zakończona na końcu 2020.
> Obecnie można migrować wyłącznie dane na żywo. Jeśli rozpoczniesz pracę teraz, nie możesz wykonać kopii zapasowych później.
> Kopie zapasowe muszą być "odtwarzane" w udziałach plików platformy Azure od najstarszych do najnowszych do danych na żywo, z migawek udziałów plików platformy Azure między.

Aby przeprowadzić migrację wyłącznie danych na żywo i nie ma wymagań dotyczących tworzenia kopii zapasowych, można kontynuować działanie opisane w tym przewodniku.

## <a name="phase-2-deploying-azure-storage-and-migration-resources"></a>Faza 2: wdrażanie zasobów usługi Azure Storage i migracji

W tej sekcji omówiono zagadnienia dotyczące wdrażania różnych typów zasobów, które są zbędne na platformie Azure. Niektóre z nich będą przechowywać dane po migracji, a niektóre są niezbędne wyłącznie do migracji. Nie rozpoczynaj wdrażania zasobów do momentu sfinalizowania planu wdrożenia. Jest trudne, czasami niemożliwe, zmiana niektórych aspektów zasobów platformy Azure po ich wdrożeniu.

### <a name="deploy-storage-accounts"></a>Wdrażanie kont magazynu
Prawdopodobnie trzeba będzie wdrożyć kilka kont usługi Azure Storage. Każda z nich będzie zawierać mniejszą liczbę udziałów plików platformy Azure zgodnie z planem wdrożenia, które zostały wykonane w poprzedniej sekcji tego artykułu. Uzyskano Azure Portal [wdrożenia planowanych kont magazynu](../common/storage-account-create.md#create-a-storage-account). Należy rozważyć przestrzeganie następujących ustawień podstawowych dla każdego nowego konta magazynu:

##### <a name="subscription"></a>Subskrypcja
Możesz użyć tej samej subskrypcji, która została użyta do wdrożenia usługi StorSimple lub innego. Jedynym ograniczeniem jest to, że subskrypcja musi znajdować się w tej samej dzierżawie usługi Azure AD jako subskrypcja StorSimple. Rozważ przeniesienie subskrypcji StorSimple do odpowiedniej dzierżawy przed rozpoczęciem migracji. Możesz przenieść całą subskrypcję, nie można przenieść pojedynczych zasobów StorSimple do innej dzierżawy lub subskrypcji.

##### <a name="resource-group"></a>Grupa zasobów
Grupy zasobów są wspomagane z organizacją zasobów i uprawnieniami do zarządzania administratorami. Dowiedz się więcej o [grupach zasobów na platformie Azure](../../azure-resource-manager/management/manage-resource-groups-portal.md#what-is-a-resource-group).

##### <a name="storage-account-name"></a>Nazwa konta magazynu
Nazwa konta magazynu staje się częścią adresu URL i ma pewne ograniczenia dotyczące znaków. W konwencji nazewnictwa należy wziąć pod uwagę, że nazwy kont magazynu muszą być unikatowe na świecie, tylko małe litery i cyfry, wymagają od 3 do 24 znaków i nie zezwalają na znaki specjalne, takie jak łączniki i podkreślenia. Zobacz również: [reguły nazewnictwa zasobów usługi Azure Storage](../../azure-resource-manager/management/resource-name-rules.md#microsoftstorage).

##### <a name="location"></a>Lokalizacja
"Lokalizacja" lub region platformy Azure konta magazynu jest bardzo ważny. Jeśli używasz Azure File Sync, wszystkie konta magazynu muszą znajdować się w tym samym regionie, co zasób usługi synchronizacji magazynu. Wybrany przez Ciebie region platformy Azure powinien być bliski lub centralny do serwerów lokalnych/użytkowników. Po wdrożeniu zasobu nie można zmienić jego regionu.

Możesz wybrać inny region, w którym znajdują się dane usługi StorSimple (konto magazynu).

> [!IMPORTANT]
> W przypadku wybrania innego regionu z bieżącej lokalizacji konta magazynu StorSimple [będą naliczane opłaty za](https://azure.microsoft.com/pricing/details/bandwidth) ruch wychodzący podczas migracji. Dane będą opuścić region StorSimple i wprowadzić nowy region konta magazynu. W przypadku pozostania w tym samym regionie świadczenia usługi Azure nie są naliczane opłaty za przepustowość.

##### <a name="performance"></a>Wydajność
Dostępna jest opcja pobrania magazynu Premium Storage (SSD) dla udziałów plików platformy Azure lub magazynu w warstwie Standardowa. Magazyn [w warstwie Standardowa obejmuje kilka warstw dla udziału plików](storage-how-to-create-file-share.md#changing-the-tier-of-an-azure-file-share). Standard Storage jest odpowiednią opcją dla większości klientów Migrowanie z StorSimple.

Nadal nie masz pewności?
* Wybierz pozycję Magazyn Premium Storage, jeśli potrzebujesz [wydajności udziału plików platformy Azure w warstwie Premium](storage-files-planning.md#understanding-provisioning-for-premium-file-shares).
* Wybierz pozycję Magazyn standardowy dla obciążeń serwera plików ogólnego przeznaczenia, w tym dane gorąca i dane archiwalne. Wybierz również pozycję Magazyn w warstwie Standardowa, jeśli będzie Azure File Sync tylko obciążenie udziału w chmurze.

##### <a name="account-kind"></a>Rodzaj konta
* W przypadku magazynu w warstwie Standardowa wybierz: *StorageV2 (ogólnego przeznaczenia w wersji 2)*
* W przypadku udziałów plików w warstwie Premium wybierz: *FileStorage*

##### <a name="replication"></a>Replikacja
Dostępne są kilka ustawień replikacji. Dowiedz się więcej o różnych typach replikacji.

Wybierz jedną z następujących dwóch opcji:
* *Magazyn lokalnie nadmiarowy (LRS)*
* *Magazyn strefowo nadmiarowy (ZRS)* , który nie jest dostępny we wszystkich regionach świadczenia usługi Azure.

> [!NOTE]
> Tylko typy nadmiarowości LRS i ZRS są zgodne z dużymi udziałami plików platformy Azure o dużej pojemności.

Magazyn globalnie nadmiarowy (wszystkie odmiany) nie jest obecnie obsługiwany. Typ nadmiarowości można zmienić później i GRS, gdy zostanie on odebrany na platformie Azure.

##### <a name="enable-100tib-capacity-file-shares"></a>Włącz udziały plików 100TiB

:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-how-to-create-large-file-share/large-file-shares-advanced-enable.png" alt-text="Obraz przedstawiający kartę Zaawansowane w Azure Portal na potrzeby tworzenia konta magazynu.":::
    :::column-end:::
    :::column:::
        W sekcji *Zaawansowane* w Kreatorze nowego konta magazynu w Azure Portal można włączyć obsługę *dużych udziałów plików* na tym koncie magazynu. Jeśli ta opcja nie jest dostępna dla Ciebie, prawdopodobnie wybrano niewłaściwy typ nadmiarowości. Upewnij się, że wybrano opcję LRS lub ZRS dla tej opcji, aby była dostępna.
    :::column-end:::
:::row-end:::

Możliwość wyznaczania dla dużych udziałów plików 100TiB ma kilka zalet:
* Wydajność jest znacznie zwiększona w porównaniu do mniejszych udziałów plików 5TiB. (na przykład: 10 razy liczba IOPS)
* Migracja zakończy się znacznie szybciej.
* Upewnij się, że udział plików będzie miał wystarczającą pojemność do przechowywania wszystkich danych, które zostaną zmigrowane do niej.
* Podano przyszłość wzrostu.

### <a name="azure-file-shares"></a>Udziały plików platformy Azure
Po utworzeniu kont magazynu możesz przejść do sekcji *"udział plików"* konta magazynu i wdrożyć odpowiednią liczbę udziałów plików platformy Azure zgodnie z planem migracji z fazy 1. Należy rozważyć przestrzeganie następujących ustawień podstawowych dla nowych udziałów plików na platformie Azure:

:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-new-share.png" alt-text="Obraz przedstawiający kartę Zaawansowane w Azure Portal na potrzeby tworzenia konta magazynu.":::
    :::column-end:::
    :::column:::
        </br>**Nazwa**</br>Małe litery, cyfry i łączniki są obsługiwane.</br></br>**limit przydziału**</br>Przydział w tym miejscu jest porównywalny do limitu przydziału twardego SMB w systemie Windows Server. Najlepszym rozwiązaniem jest nie ustawienie limitu przydziału w tym miejscu, ponieważ migracja i inne usługi zakończą się niepowodzeniem po osiągnięciu limitu przydziału.</br></br>**Warstwa**</br>Wybierz pozycję *transakcja zoptymalizowana pod* kątem nowego udziału plików. Podczas migracji nastąpi wiele transakcji, dzięki czemu można później zmienić warstwę na warstwę najlepiej dopasowaną do obciążenia.
    :::column-end:::
:::row-end:::

### <a name="storsimple-data-manager"></a>StorSimple Data Manager
Zasób platformy Azure, w którym będą przechowywane zadania migracji, nosi nazwę *"StorSimple Data Manager"*. Kliknij pozycję Nowy zasób i wyszukaj go, a następnie kliknij pozycję *Utwórz*.

Jest to zasób tymczasowy używany na potrzeby aranżacji. po zakończeniu migracji zostanie on anulowany. Należy ją wdrożyć w tej samej subskrypcji, grupie zasobów i regionie co konto magazynu StorSimple.

### <a name="azure-file-sync"></a>Azure File Sync
Azure File Sync (AFS) umożliwia dodanie lokalnego buforowania plików najczęściej używanych. Podobnie jak w przypadku możliwości buforowania StorSimple, funkcja obsługi warstw w chmurze AFS oferuje opóźnienie dostępu lokalnego w połączeniu z ulepszoną kontrolą dostępnej pojemności pamięci podręcznej w systemie Windows Server i w ramach synchronizacji między lokacjami. Jeśli ma to miejsce w lokalnej pamięci podręcznej, należy przygotować maszynę wirtualną z systemem Windows Server (serwery fizyczne i klastry w trybie failover również są obsługiwane) z wystarczającą pojemnością "bezpośrednio dołączonego magazynu (DAS)". 

> [!IMPORTANT]
> Nie należy jeszcze konfigurować Azure File Sync, najlepszym rozwiązaniem jest skonfigurowanie Azure File Sync po zakończeniu migracji udziału. Wdrożenie klasy AFS nie powinno zostać rozpoczęte przed fazą 4 migracji.

### <a name="phase-2-summary"></a>Podsumowanie fazy 2
Na koniec fazy 2 zostaną wdrożone konta magazynu, wszystkie udziały plików platformy Azure w ramach tych usług, a także zaStorSimple Data Manager zasób. Ta ostatnia zostanie użyta w fazie 3, gdy w rzeczywistości skonfigurujesz zadania migracji.

## <a name="phase-3-create-and-run-a-migration-job"></a>Faza 3: Tworzenie i uruchamianie zadania migracji
W tej sekcji opisano sposób konfigurowania zadania migracji i dokładnego mapowania katalogów na woluminie StorSimple, które powinny zostać skopiowane do wybranych docelowych udziałów plików platformy Azure. Aby rozpocząć, przejdź do StorSimple Data Manager, Znajdź **definicje zadań** w menu i wybierz pozycję **+ Definicja zadania**. Docelowy typ magazynu jest wartością domyślną: *udział plików platformy Azure*.

![Typy zadań migracji serii StorSimple 8000.](media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-new-job-type.png "Zrzut ekranu definicji zadania Azure Portal przy otwartym oknie dialogowym nowej definicji zadania, który prosi o typ zadania: Kopiuj do udziału plików lub kontenera obiektów BLOB.")

> [!IMPORTANT]
> Przed uruchomieniem dowolnego zadania migracji Zatrzymaj wszystkie automatycznie zaplanowane kopie zapasowe woluminów StorSimple.

:::row:::
    :::column:::
        ![Zadanie migracji serii StorSimple 8000.](media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-new-job.png "Zrzut ekranu przedstawiający formularz tworzenia nowego zadania transformacji danych.")
    :::column-end:::
    :::column:::
        **Nazwa definicji zadania**</br>Ta nazwa powinna mieć charakter wskazujący zestaw plików, które są przenoszone. Przyznaj mu podobną nazwę, ponieważ udział plików platformy Azure jest dobrym zwyczajem. </br></br>**Lokalizacja, w której jest uruchamiane zadanie**</br>W przypadku wybrania regionu należy wybrać ten sam region, w którym znajduje się konto magazynu StorSimple lub, jeśli to nie jest dostępne, a następnie zamknąć region. </br></br><h3>Element źródłowy</h3>**Subskrypcja źródłowa**</br>Wybierz subskrypcję, w ramach której przechowujesz zasób StorSimple Menedżer urządzeń. </br></br>**Zasób StorSimple**</br>Wybierz StorSimple Menedżer urządzeń Twoje urządzenie jest zarejestrowane w usłudze. </br></br>**Klucz szyfrowania danych usługi**</br>Sprawdź tę [wcześniejszą sekcję w tym artykule](#storsimple-service-data-encryption-key), jeśli nie możesz znaleźć klucza w rekordach. </br></br>**Urządzenie**</br>Wybierz urządzenie StorSimple, które zawiera wolumin, na którym chcesz przeprowadzić migrację. </br></br>**Wolumin**</br>Wybierz wolumin źródłowy. Następnie zdecyduj, czy chcesz migrować cały wolumin lub podkatalogi do docelowego udziału plików platformy Azure. </br></br><h3>Cel</h3>Wybierz subskrypcję, konto magazynu i udział plików platformy Azure jako element docelowy tego zadania migracji.
    :::column-end:::
:::row-end:::

> [!IMPORTANT]
> Najnowsza kopia zapasowa woluminu zostanie użyta do przeprowadzenia migracji. Upewnij się, że istnieje co najmniej jedna kopia zapasowa woluminu lub zadanie zakończy się niepowodzeniem. Upewnij się również, że najnowsza kopia zapasowa jest dość mała, aby zachować różnice w udziale na żywo tak jak to możliwe. Może być również warto ręcznie wyzwolić i zakończyć kolejną kopię zapasową woluminu **przed** uruchomieniem właśnie utworzonego zadania.

### <a name="directory-mapping"></a>Mapowanie katalogów
Jest to opcjonalne dla zadania migracji. Jeśli pozostawisz ją puste, **wszystkie** pliki i foldery w katalogu głównym woluminu StorSimple zostaną przeniesione do folderu głównego docelowego udziału plików platformy Azure. W większości przypadków przechowywanie zawartości całego woluminu w udziale plików platformy Azure nie jest najlepszym rozwiązaniem. Często lepiej jest podzielić zawartość woluminu na wiele udziałów plików na platformie Azure. Jeśli jeszcze nie utworzono planu, najpierw zapoznaj się z tą sekcją: [Mapuj wolumin StorSimple do udziałów plików platformy Azure](#map-your-existing-storsimple-volumes-to-azure-file-shares)

W ramach planu migracji można zdecydować, że foldery na woluminie StorSimple muszą być podzielone między wiele udziałów plików platformy Azure. W takim przypadku można wykonać podział według:
1. Zdefiniowanie wielu zadań do migracji folderów na jednym woluminie będzie miało to samo źródło woluminu StorSimple, ale inny udział plików platformy Azure jako element docelowy.
1. Określenie, które foldery z woluminu StorSimple należy migrować do określonego udziału plików, za pomocą sekcji *Mapowanie katalogów* w formularzu tworzenia zadania i zgodnie z określoną [semantyką mapowania](#semantic-elements).

> [!IMPORTANT]
> Nie można zweryfikować ścieżek i wyrażeń mapowania w tym formularzu, gdy formularz zostanie przesłany. Jeśli mapowania są określone nieprawidłowo, zadanie może zakończyć się niepowodzeniem lub utworzyć niepożądany wynik. W takim przypadku zazwyczaj najlepiej jest usunąć udział plików platformy Azure, utworzyć go ponownie, a następnie naprawić instrukcje mapowania w nowym zadaniu migracji dla udziału. Uruchomienie nowego zadania ze stałymi instrukcjami mapowania może poprawić pominięte foldery i przenieść je do istniejącego udziału. Jednak w ten sposób można w ten sposób rozwiązać tylko foldery, które zostały pominięte z powodu błędu ścieżki.

#### <a name="semantic-elements"></a>Elementy semantyczne
Mapowanie jest wyrażone od lewej do prawej: [ścieżka \source] \> [ścieżka \target].

|Znak semantyczny          | Znaczenie  |
|:---------------------------|:---------|
| **\\**                     | wskaźnik poziomu głównego        |
| **\>**                     | [Source] i [target] — operator mapowania        |
|**\|** lub RETURN (nowy wiersz) | Separator dwóch instrukcji dotyczących mapowania folderów. </br>Alternatywnie możesz pominąć ten znak i nacisnąć klawisz ENTER, aby uzyskać następne wyrażenie mapowania w osobnym wierszu.        |

### <a name="examples"></a>Przykłady
Przenosi zawartość folderu "dane użytkownika" do katalogu głównego docelowego udziału plików:
``` console
\User data > \\
```
Przenosi całą zawartość woluminu do nowej ścieżki w docelowym udziale plików:
``` console
\ \> \Apps\HR tracker
```
Przenosi zawartość folderu źródłowego do nowej ścieżki w docelowym udziale plików:
``` console
\HR resumes-Backup \> \Backups\HR\resumes
```
Sortuje wiele lokalizacji źródłowych do nowej struktury katalogów:
``` console
\HR\Candidate Tracker\v1.0 > \Apps\Candidate tracker
\HR\Candidates\Resumes > \HR\Candidates\New
\Archive\HR\Old Resumes > \HR\Candidates\Archived
```

### <a name="semantic-rules"></a>Reguły semantyczne
* Zawsze określaj ścieżki folderów względem poziomu głównego. 
* Rozpocznij każdą ścieżkę folderu z wskaźnikiem poziomu głównego \" . 
* Nie dołączaj liter dysków. 
* W przypadku określania wielu ścieżek ścieżki źródłowe lub docelowe nie mogą się nakładać:</br>
   Nieprawidłowy przykład nakładania się ścieżki źródłowej:</br>
    *\\folder\1 > \\ folder*</br>
    *\\folder \\ 1 \\ 2 > \\ Folder2*</br>
   Nieprawidłowy przykład nakładania się ścieżki docelowej:</br>
   *\\> folderu \\*</br>
   *\\Folder2 > \\*</br>
* Foldery źródłowe, które nie istnieją, zostaną zignorowane. 
* Struktury folderów, które nie istnieją w miejscu docelowym, zostaną utworzone. 
* Podobnie jak w przypadku systemu Windows: nazwy folderów nie uwzględniają wielkości liter, ale z zachowaniem wielkości liter.

> [!NOTE]
> zawartość folderu "*\System Volume Information*" i "*$recycle. bin*" na woluminie StorSimple nie zostanie skopiowana przez zadanie transformacji.

### <a name="phase-3-summary"></a>Podsumowanie fazy 3
Na koniec fazy 3 zostaną uruchomione zadania przekształcania danych z woluminów StorSimple do udziałów plików platformy Azure. Teraz możesz skupić się na konfigurowaniu Azure File Sync dla udziału (po zakończeniu zadań migracji dla udziału) lub udostępnieniu bezpośredniego dostępu do danych i aplikacji w udziale plików platformy Azure.

## <a name="phase-4-accessing-your-azure-file-shares"></a>Faza 4: uzyskiwanie dostępu do udziałów plików platformy Azure

Istnieją dwie główne strategie uzyskiwania dostępu do udziałów plików platformy Azure:

* **Azure File Sync:** [jak wdrożyć Azure File Sync](#deploy-azure-file-sync) na lokalnym serwerze systemu Windows. AFS ma wszystkie zalety lokalnej pamięci podręcznej, podobnie jak StorSimple.
* **Bezpośredni dostęp do udostępniania:** [jak wdrożyć bezpośredni dostęp do udziału](#deploy-direct-share-access). Użyj tej strategii, jeśli scenariusz dostępu dla danego udziału plików platformy Azure nie będzie korzystać z buforowania lokalnego lub nie masz już możliwości hostowania lokalnej instalacji systemu Windows Server. W tym miejscu Użytkownicy i aplikacje będą nadal uzyskiwać dostęp do udziałów SMB za pośrednictwem protokołu SMB, ale te udziały nie znajdują się już na serwerze lokalnym, ale bezpośrednio w chmurze.

Należy już zdecydować, która opcja jest Najlepsza dla Ciebie, w [fazie 1](#phase-1-prepare-for-migration) tego przewodnika.

Pozostała część tej sekcji koncentruje się na instrukcjach dotyczących wdrażania.

### <a name="deploy-azure-file-sync"></a>Wdrażanie usługi Azure File Sync

Jest to czas na wdrożenie części Azure File Sync:
1. Utwórz zasób w chmurze Azure File Sync.
1. Wdróż agenta Azure File Sync na serwerze Premium.
1. Rejestrowanie serwera przy użyciu zasobu chmury

Nie należy jeszcze tworzyć żadnych grup synchronizacji. Konfigurowanie synchronizacji z udziałem plików platformy Azure powinno odbywać się tylko po zakończeniu zadań migracji do udziału plików platformy Azure. Jeśli dopiero zaczynasz korzystać z File Sync przed ukończeniem migracji, proces migracji będzie niekoniecznie trudny, ponieważ nie można łatwo stwierdzić, kiedy jest czas na zainicjowanie przecięcia.

#### <a name="deploy-the-azure-file-sync-cloud-resource"></a>Wdróż zasób w chmurze Azure File Sync

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

> [!TIP]
> Jeśli chcesz zmienić region platformy Azure, w którym znajdują się dane po zakończeniu migracji, wdróż usługę synchronizacji magazynu w tym samym regionie co docelowe konta magazynu dla tej migracji.

#### <a name="deploy-an-on-premises-windows-server"></a>Wdrażanie lokalnego serwera z systemem Windows

* Utwórz system Windows Server 2019 — co najmniej 2012R2 maszyny wirtualnej lub serwera fizycznego. Obsługiwany jest również klaster trybu failover systemu Windows Server. Nie używaj ponownie serwera z StorSimple 8100 lub 8600.
* Udostępnianie lub Dodawanie bezpośredniego dołączonego magazynu (DAS w porównaniu z serwerem NAS, co nie jest obsługiwane).

Najlepszym rozwiązaniem jest nadanie nowemu serwerowi systemu Windows większej lub większej ilości miejsca w magazynie niż Urządzenie StorSimple 8100 lub 8600, które jest lokalnie dostępne do buforowania. Użyjesz systemu Windows Server w taki sam sposób, jak w przypadku użycia urządzenia StorSimple, jeśli ma on taką samą ilość magazynu jak urządzenie, środowisko pamięci podręcznej powinno być podobne, jeśli nie jest takie samo.
Możesz dodać lub usunąć magazyn z systemu Windows Server w systemie. Umożliwia to skalowanie rozmiaru woluminu lokalnego i ilości magazynu lokalnego dostępnego do buforowania.

#### <a name="prepare-the-windows-server-for-file-sync"></a>Przygotowanie systemu Windows Server do synchronizacji plików

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

#### <a name="configure-azure-file-sync-on-the-windows-server"></a>Konfigurowanie Azure File Sync w systemie Windows Server

Zarejestrowany lokalny serwer systemu Windows musi być gotowy i połączony z Internetem w ramach tego procesu.

> [!IMPORTANT]
> Przed kontynuowaniem należy wykonać migrację StorSimple plików i folderów do udziału plików platformy Azure. Upewnij się, że nie ma żadnych zmian wprowadzonych w udziale plików.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!IMPORTANT]
> Upewnij się, że włączona jest obsługa warstw w chmurze! Obsługa warstw w chmurze jest funkcją Azure File Sync, która umożliwia serwerowi lokalnemu przechowywanie mniejszej pojemności niż w chmurze, ale pełna przestrzeń nazw jest dostępna. Lokalnie interesujące dane są również buforowane lokalnie w celu szybkiej, lokalnej wydajności dostępu. Kolejną przyczyną włączenia obsługi warstw w chmurze w tym kroku jest to, że nie chcemy synchronizować zawartości pliku na tym etapie. w tej chwili należy przenieść tylko przestrzeń nazw.

### <a name="deploy-direct-share-access"></a>Wdróż bezpośredni dostęp do udziału

:::row:::
    :::column:::
        [![Przewodnik krok po kroku i demonstracja, jak bezpiecznie ujawniać udziały plików platformy Azure bezpośrednio pracownikom przetwarzającym informacje i aplikacje — kliknij, aby odtworzyć!](./media/storage-files-migration-storsimple-8000/azure-files-direct-access-video-placeholder.png)](https://youtu.be/KG0OX0RgytI)
    :::column-end:::
    :::column:::
        Ten film wideo to przewodnik i demonstracja, jak bezpiecznie ujawniać udziały plików platformy Azure bezpośrednio pracownikom przetwarzającym informacje i aplikacje w pięciu prostych krokach.</br>
        Film wideo odwołuje się do dedykowanej dokumentacji dotyczącej niektórych tematów:
* [Przegląd tożsamości](storage-files-active-directory-overview.md)
* [Dołączanie do domeny do konta magazynu](storage-files-identity-auth-active-directory-enable.md)
* [Omówienie sieci dla udziałów plików platformy Azure](storage-files-networking-overview.md)
* [Jak skonfigurować publiczne i prywatne punkty końcowe](storage-files-networking-endpoints.md)
* [Jak skonfigurować sieć VPN S2S](storage-files-configure-s2s-vpn.md)
* [Jak skonfigurować sieć VPN z systemem Windows P2S](storage-files-configure-p2s-vpn-windows.md)
* [Jak skonfigurować sieć VPN z systemem Linux P2S](storage-files-configure-p2s-vpn-linux.md)
* [Jak skonfigurować przekazywanie DNS](storage-files-networking-dns.md)
* [Konfigurowanie systemu plików DFS-N](https://aka.ms/AzureFiles/Namespaces)
   :::column-end:::
:::row-end:::

### <a name="phase-4-summary"></a>Podsumowanie fazy 4
W tej fazie utworzono i uruchomiono zadania wielu *usług przekształcania danych* (DTS) w *StorSimple Data Manager*. Te zadania przeprowadzono migrację plików i folderów do udziałów plików platformy Azure. Ponadto wdrożono Azure File Sync lub przygotowano konta sieci i magazynu dla bezpośredniego dostępu do udziału.

## <a name="phase-5-user-cut-over"></a>Faza 5: wycinanie użytkownika
W tej fazie wszystkie informacje dotyczące pakowania migracji:
* Zaplanuj przestoje.
* Zapoznaj się ze wszystkimi zmianami użytkowników i aplikacji produkowanymi na StorSimple po stronie, gdy uruchomione są zadania przekształcania danych w fazie 3. 
* Przechodzenie przez użytkowników do nowego serwera z systemem Windows za pomocą Azure File Sync lub udziałów plików platformy Azure za pośrednictwem bezpośredniego udziału.

### <a name="plan-your-downtime"></a>Planowanie przestoju
To podejście migracji wymaga pewnego przestoju dla użytkowników i aplikacji. Celem jest zapewnienie, że czas przestoju jest minimalny i następujące zagadnienia mogą pomóc:

* Zachowaj dostępne woluminy StorSimple podczas wykonywania zadań przekształcania danych.
* Po zakończeniu wykonywania zadań migracji danych dla udziału, czas na usunięcie dostępu użytkownika (co najmniej prawa dostępu do zapisu) z woluminów StorSimple/udziałów. RoboCopy końcowy będzie przechwycić udział plików platformy Azure, a następnie możesz wyciąć użytkowników. Miejsce, w którym uruchamiasz RoboCopy, zależy od tego, czy wybrano opcję użycia Azure File Sync lub bezpośredniego udostępniania. W kolejnej sekcji RoboCopy zawarto te zagadnienia.
* Po zakończeniu przechwytywania RoboCopy można przystąpić do udostępniania użytkownikom nowej lokalizacji. Udział plików platformy Azure bezpośrednio lub udział SMB na serwerze z systemem operacyjnym Windows Server. Często wdrożenie systemu plików DFS-N pomoże w szybkim i wydajnym przecięciu. Dzięki temu istniejące udziały będą spójne i ponownie wskazywały nową lokalizację, zawierającą zmigrowane pliki i foldery.

### <a name="determine-when-your-namespace-has-fully-synced-to-your-server"></a>Określ, kiedy przestrzeń nazw ma być w pełni synchronizowana z serwerem

Jeśli używasz Azure File Sync dla udziału plików platformy Azure, ważne jest, aby określić, że cała przestrzeń nazw zakończyła pobieranie na serwer przed rozpoczęciem RoboCopy lokalnego. Czas potrzebny na pobranie przestrzeni nazw zależy od liczby elementów w udziale plików platformy Azure. Istnieją dwie metody określania, czy obszar nazw został w pełni osiągnięty na serwerze:

#### <a name="azure-portal"></a>Azure Portal
Możesz użyć Azure Portal, aby zobaczyć, kiedy obszar nazw został w pełni osiągnięty.
* Zaloguj się do Azure Portal, przejdź do grupy synchronizacji i sprawdź stan synchronizacji grupy synchronizacji i punktu końcowego serwera. 
* Interesujący kierunek to pobranie: Jeśli punkt końcowy serwera jest nowo zainicjowany, zostanie wyświetlona **synchronizacja początkowa** wskazująca, że przestrzeń nazw nadal się nie wyłączy.
Po wprowadzeniu zmian do wszystkiego, ale *synchronizacji początkowej*, obszar nazw zostanie w pełni wypełniony na serwerze i będzie można kontynuować pracę z lokalnym Robocopy.

#### <a name="windows-server-event-viewer"></a>Podgląd zdarzeń systemu Windows Server
jednostka organizacyjna może również użyć Podgląd zdarzeń na serwerze z systemem Windows, aby określić, kiedy przestrzeń nazw została w pełni dostarczona.

1. Otwórz **Podgląd zdarzeń** i przejdź do **aplikacji i usług**.
1. Przejdź do **Microsoft\FileSync\Agent\Telemetry**i otwórz go.
1. Poszukaj najnowszego **zdarzenia 9102**, które odnosi się do zakończonej sesji synchronizacji.
1. Wybierz pozycję **szczegóły** i Potwierdź, że przeglądasz zdarzenie, w którym jest **pobierana**wartość **SyncDirection** .
1. Na czas, w którym przestrzeń nazw została zakończona do pobrania do serwera, zostanie jedno zdarzenie z **scenariuszem**, wartością **FullGhostedSync** i **wynik HRESULT**  =  **0**.
1. Jeśli to zdarzenie zostanie pominięte, możesz również wyszukać inne **zdarzenia 9102** z **SyncDirection**  =  **pobierania** i **scenariusza**  =  **"RegularSync"**. Znalezienie jednego z tych zdarzeń wskazuje również, że przestrzeń nazw zakończyła pobieranie i synchronizowanie w regularnych sesjach synchronizacji, niezależnie od tego, czy w tej chwili istnieje jakakolwiek synchronizacja, czy nie.

### <a name="a-final-robocopy"></a>Końcowy RoboCopy
W tym momencie istnieją różnice między lokalnym serwerem z systemem Windows i urządzeniem StorSimple 8100 lub 8600:

1. Musisz przechwycić zmiany, które użytkownicy/aplikacje wygenerowały po stronie StorSimple podczas migracji.
1. W przypadku korzystania z Azure File Sync: Urządzenie StorSimple ma wypełnioną pamięć podręczną a system Windows Server tylko przestrzeń nazw bez zawartości pliku przechowywanej lokalnie. W związku z tym końcowy RoboCopy może pomóc w próbie uruchomienia lokalnej pamięci podręcznej AFS przez ściąganie lokalnie buforowanej zawartości plików o ile jest dostępna i może być zgodna z serwerem AFS.
1. Niektóre pliki mogły zostać pozostawione w tle przez zadanie przekształcania danych z powodu nieprawidłowych znaków. Jeśli tak, skopiuj je do Azure File Sync z włączonym systemem Windows Server. Później można je dostosować w taki sposób, aby były synchronizowane. Jeśli nie używasz Azure File Sync dla określonego udziału, lepiej jest zmienić nazwy plików z nieprawidłowymi znakami na woluminie StorSimple, a następnie uruchomić RoboCopy bezpośrednio w udziale plików platformy Azure. 

> [!WARNING]
> Nie można uruchomić RoboCopy, zanim serwer ma przestrzeń nazw dla udziału plików platformy Azure w pełni.
> Wyewidencjonowywanie: "[Określ, kiedy obszar nazw został w pełni pobrany na serwer](#determine-when-your-namespace-has-fully-synced-to-your-server)"

 Chcesz skopiować tylko te pliki, które zostały zmienione po ostatnim uruchomieniu zadania migracji, i pliki, które nie zostały przeniesione przez wymienione zadania przed. Po zakończeniu migracji możesz rozwiązać problemy, dlaczego nie przechodzą później na serwerze. Zobacz [Azure File Sync Rozwiązywanie problemów](storage-sync-files-troubleshoot.md#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing).

RoboCopy ma kilka parametrów. Następujące pokazy zostały zakończone i przedstawiono listę przyczyn wyboru tych parametrów:

```console
Robocopy /MT:16 /UNILOG:<file name> /TEE /B /MIR /COPYALL /DCOPY:DAT <SourcePath> <Dest.Path>
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

Podczas konfigurowania lokalizacji źródłowej i docelowej polecenia RoboCopy upewnij się, że znasz strukturę źródłową i docelową, aby upewnić się, że są one zgodne. Jeśli została użyta funkcja mapowania katalogów w ramach zadania migracji, struktura katalogu głównego może różnić się od struktury woluminu StorSimple. W takim przypadku może być potrzebne wiele zadań RoboCopy, po jednym dla każdego podkatalogu. Ponieważ to polecenie RoboCopy używa/MIR-nie przeniesie plików, które są takie same (pliki warstwowe na wystąpienie), ale jeśli ścieżka źródłowa i docelowa nie jest poprawna,/MIR także Przeczyszcza struktury katalogów w udziale plików systemu Windows Server/Azure, które nie znajdują się na ścieżce źródłowej StorSimple. Muszą one dokładnie pasować do zadania RoboCopy, aby osiągnąć jego zamierzony cel po prostu aktualizację zmigrowanej zawartości przy użyciu najnowszych zmian dokonanych podczas migracji. 

Zapoznaj się z plikiem dziennika RoboCopy, aby zobaczyć, czy pliki zostały pozostawione w tle. Jeśli istnieją problemy, napraw je i ponownie uruchom polecenie RoboCopy. Nie należy cofać aprowizacji żadnych zasobów StorSimple przed usunięciem nieuregulowanych problemów dotyczących plików lub folderów, o których się interesują.

Jeśli nie używasz Azure File Sync do buforowania określonego udziału plików platformy Azure, ale zamiast tego wybrałeś opcję bezpośredniego dostępu do udziału:
1. [Zainstaluj udział plików platformy Azure](storage-how-to-use-files-windows.md#mount-the-azure-file-share) jako dysk sieciowy na lokalnym komputerze z systemem Windows.
1. Wykonaj RoboCopy między StorSimple i zainstalowanym udziałem plików platformy Azure. Jeśli pliki nie są kopiowane, napraw ich nazwy na stronie StorSimple, aby usunąć nieprawidłowe znaki, a następnie ponów próbę RoboCopy. Wymienione wcześniej polecenie RoboCopy można uruchomić wiele razy w/o, powodując niepotrzebne odwołania do StorSimple.

### <a name="user-cut-over"></a>Wytnij użytkownika

Jeśli używasz Azure File Sync, być może trzeba będzie utworzyć udziały SMB na tym serwerze z systemem Windows Server, który jest zgodny z udziałami na woluminach StorSimple. Możesz Załaduj ponownie ten krok i wykonać wcześniejszą próbę, aby nie utracić go w tym miejscu, ale musisz upewnić się, że przed tym punktem nikt nie będzie miał dostępu, aby spowodować zmiany w systemie Windows Server.

Jeśli masz wdrożenie systemu plików DFS-N, możesz wskazać DFN-Namespaces do nowej lokalizacji folderu na serwerze. Jeśli nie masz wdrożenia systemu plików DFS-N, a urządzenie 8100/8600 zostało lokalnie udostępnione w systemie Windows Server, możesz to zrobić, korzystając z tego serwera poza domenę. Następnie przyłącz się do nowej Azure File Sync włączeniu systemu Windows Server. W trakcie tego procesu nadaj serwerowi taką samą nazwę serwera i nazwy udziałów jak stary serwer, aby przecinanie pozostawało niewidoczne dla użytkowników, zasad grupy i skryptów.

[Dowiedz się więcej o systemie plików DFS-N](https://aka.ms/AzureFiles/Namespaces)

## <a name="deprovision"></a>Anulowanie aprowizacji
W przypadku anulowania aprowizacji zasobu utracisz dostęp do konfiguracji tego zasobu, a także jego danych. Cofanie aprowizacji nie może zostać cofnięte, więc nie należy przechodzić do momentu potwierdzenia, że migracja została ukończona i nie ma żadnych zależności w plikach StorSimple, folderach ani kopii zapasowych woluminów, które zamierzasz cofnąć.

Przed rozpoczęciem najlepiej obserwować nowe wdrożenie Azure File Sync w środowisku produkcyjnym. Zapewnia opcje rozwiązania problemów, które mogą wystąpić.
Po zaobserwowanym wdrożeniu Azure File Sync przez co najmniej kilka dni możesz rozpocząć cofanie aprowizacji zasobów w następującej kolejności:

1. Cofaj obsługę administracyjną zasobu StorSimple Data Manager za pośrednictwem Azure Portal.
   Wszystkie zadania DTS zostaną usunięte razem z nim. Nie będziesz w stanie łatwo pobrać dzienników kopiowania. Jeśli są ważne dla rekordów, pobierz je przed cofnięciem aprowizacji.
1. Upewnij się, że urządzenia fizyczne StorSimple zostały zmigrowane, a następnie Wyrejestruj je.
   Jeśli nie masz całkowicie pewności, że zostały zmigrowane, nie należy przechodzić. W przypadku anulowania aprowizacji tych zasobów, gdy są nadal niezbędne, nie będzie można odzyskać danych ani ich konfiguracji.
1. Jeśli nie ma więcej zarejestrowanych urządzeń w StorSimple Menedżer urządzeń, możesz wykonać operację usuwania Menedżer urządzeń samego zasobu.
1. Teraz można usunąć konto magazynu StorSimple na platformie Azure. Ponownie, Zatrzymaj i Potwierdź, że migracja została ukończona i nic nie jest zależne od tych danych, przed kontynuowaniem.
1. Odłącz urządzenie fizyczne StorSimple od centrum danych.
1. Jeśli jesteś posiadaczem urządzenia StorSimple, możesz go odtworzyć na komputerze. 
   Jeśli urządzenie jest dzierżawione, należy poinformować o nim mniej i przywrócić odpowiednie urządzenie.

Migracja została ukończona.

> [!NOTE]
> Nadal masz pytania lub napotykasz jakiekolwiek problemy?</br>
> Jesteśmy tutaj, aby pomóc: AzureFilesMigration@microsoft . com


## <a name="next-steps"></a>Następne kroki

* Poznaj [Azure File Sync: aka.MS/AFS](https://aka.ms/AFS).
* Zapoznaj się z elastycznością zasad obsługi [warstw w chmurze](storage-sync-cloud-tiering.md) .
* [Włącz Azure Backup](../../backup/backup-afs.md#configure-backup-from-the-file-share-pane) w udziałach plików platformy Azure w celu planowania migawek i definiowania harmonogramów przechowywania kopii zapasowych.
* Jeśli widzisz w Azure Portal, że niektóre pliki nie są synchronizowane, zapoznaj się z [przewodnikiem rozwiązywania problemów](storage-sync-files-troubleshoot.md) , aby rozwiązać te problemy.
