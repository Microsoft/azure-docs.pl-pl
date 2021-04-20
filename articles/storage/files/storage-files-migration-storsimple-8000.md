---
title: Migracja z serii StorSimple 8000 do Azure File Sync
description: Dowiedz się, jak przeprowadzić migrację urządzenia StorSimple 8100 lub 8600 do Azure File Sync.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 10/16/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 7bde8fe404e0839bf14500bff4fb92ce8cc4ea04
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717355"
---
# <a name="storsimple-8100-and-8600-migration-to-azure-file-sync"></a>Migracja z usługi StorSimple 8100 i 8600 do Azure File Sync

Urządzenia StorSimple z serii 8000 są reprezentowane przez fizyczne urządzenia 8100 lub 8600 i ich składniki usług w chmurze. Dane z jednego z tych urządzeń można migrować do środowiska Azure File Sync wirtualnego. Azure File Sync to domyślna i strategiczna, długoterminowa usługa platformy Azure, do których można migrować urządzenia StorSimple.

Seria StorSimple 8000 zakończy [](https://support.microsoft.com/en-us/lifecycle/search?alpha=StorSimple%208000%20Series) się w grudniu 2022 r. Ważne jest, aby jak najszybciej rozpocząć planowanie migracji. Ten artykuł zawiera wiedzę i kroki migracji niezbędne do pomyślnej migracji do Azure File Sync.

## <a name="phase-1-prepare-for-migration"></a>Faza 1: Przygotowanie do migracji

Ta sekcja zawiera kroki, które należy wykonać na początku migracji z woluminów StorSimple do udziałów plików platformy Azure.

### <a name="inventory"></a>Stan zapasów

Po rozpoczęciu planowania migracji należy najpierw zidentyfikować wszystkie urządzenia StorSimple i woluminy, które należy migrować. Po zakończeniu możesz wybrać najlepszą ścieżkę migracji.

* Urządzenia fizyczne StorSimple (z serii 8000) korzystają z tego przewodnika migracji.
* Urządzenia wirtualne z [serii StorSimple 1200 korzystają z innego przewodnika migracji.](storage-files-migration-storsimple-1200.md)

### <a name="migration-cost-summary"></a>Podsumowanie kosztów migracji

Migracje do udziałów plików platformy Azure z woluminów StorSimple za pośrednictwem zadań migracji w zasobie StorSimple Data Manager są bezpłatne. Inne koszty mogą być ponoszone podczas migracji i po jej zakończeniu:

* **Ruch wychodzący sieciowy:** Twoje pliki StorSimple są przechowywane na koncie magazynu w określonym regionie świadczenia usługi Azure. W przypadku aprowizowania migrowanych udziałów plików platformy Azure na konto magazynu znajdujące się w tym samym regionie świadczenia usługi Azure nie wystąpią żadne koszty wychodzące. W ramach tej migracji możesz przenieść pliki na konto magazynu w innym regionie. W takim przypadku będą dla Ciebie stosowane koszty związane z wyjściem.
* **Transakcje udziałów plików platformy Azure:** Gdy pliki są kopiowane do udziału plików platformy Azure (w ramach migracji lub poza nim), koszty transakcji są stosowane podczas dzielenia plików i metadanych. Najlepszym rozwiązaniem jest uruchomienie udziału plików platformy Azure w warstwie zoptymalizowanej pod kątem transakcji podczas migracji. Po zakończeniu migracji przejdź do żądanej warstwy. Poniższe fazy wywołają to w odpowiednim punkcie.
* **Zmienianie warstwy udziału plików platformy Azure:** Zmiana warstwy udziałów plików platformy Azure kosztuje transakcje. W większości przypadków bardziej opłaca się postępować zgodnie z poradami z poprzedniego punktu.
* **Koszt magazynu:** Gdy ta migracja rozpoczyna kopiowanie plików do udziału plików platformy Azure, Azure Files magazyn jest zużywany i rozliczany. Zmigrowane kopie zapasowe staną się [migawkami udziałów plików platformy Azure.](storage-snapshots-files.md) Migawki udziałów plików zużywają pojemność magazynu tylko w przypadku różnic, które zawierają.
* **StorSimple:** Dopóki nie będziesz mieć możliwości coprowizowania urządzeń StorSimple i kont magazynu, nadal będą występować koszty magazynu, kopii zapasowych i urządzeń StorSimple.

### <a name="direct-share-access-vs-azure-file-sync"></a>Bezpośredni dostęp do udziału a Azure File Sync

Udziały plików platformy Azure otwierają zupełnie nowy świat możliwości strukturyzowania wdrożenia usług plików. Udział plików platformy Azure to po prostu udział SMB w chmurze, który można skonfigurować tak, aby użytkownicy mieli bezpośredni dostęp za pośrednictwem protokołu SMB przy użyciu znanego uwierzytelniania Kerberos i istniejących uprawnień systemu plików NTFS (listy ACL plików i folderów) działają natywnie. Dowiedz się więcej o [dostępie opartym na tożsamościach do udziałów plików platformy Azure.](storage-files-active-directory-overview.md)

Alternatywą dla dostępu bezpośredniego jest [Azure File Sync](./storage-sync-files-planning.md). Azure File Sync jest bezpośrednią analogą dla możliwości buforowania często używanych plików w środowisku lokalnym przez usługę StorSimple.

Azure File Sync to usługa w chmurze firmy Microsoft oparta na dwóch głównych składnikach:

* Synchronizacja plików i tworzenie warstw w chmurze w celu utworzenia pamięci podręcznej dostępu do wydajności w dowolnym systemie Windows Server.
* Udziały plików jako magazyn natywny na platformie Azure, do którego można uzyskać dostęp za pośrednictwem wielu protokołów, takich jak SMB i REST plików.

Udziały plików platformy Azure zachowują ważne aspekty wierności plików przechowywanych plików, takie jak atrybuty, uprawnienia i znaczniki czasu. W przypadku udziałów plików platformy Azure nie ma już potrzeby, aby aplikacja lub usługa interpretowała pliki i foldery przechowywane w chmurze. Dostęp do nich można uzyskać natywnie za pośrednictwem znanych protokołów i klientów, takich jak windows Eksplorator plików. Udziały plików platformy Azure umożliwiają przechowywanie danych serwera plików ogólnego przeznaczenia i danych aplikacji w chmurze. Tworzenie kopii zapasowej udziału plików platformy Azure jest wbudowaną funkcjonalnością i można ją dodatkowo ulepszyć Azure Backup.

Ten artykuł koncentruje się na krokach migracji. Jeśli chcesz dowiedzieć się więcej na temat Azure File Sync przed migracją, zobacz następujące artykuły:

* [Azure File Sync omówienie](./storage-sync-files-planning.md "Omówienie")
* [Azure File Sync wdrażania](storage-sync-files-deployment-guide.md)

### <a name="storsimple-service-data-encryption-key"></a>Klucz szyfrowania danych usługi StorSimple

Po pierwszym skonfigurowaniu urządzenia StorSimple został wygenerowany "klucz szyfrowania danych usługi" i poinstruowany o konieczności bezpiecznego przechowywania klucza. Ten klucz jest używany do szyfrowania wszystkich danych na skojarzonym koncie usługi Azure Storage, na którym przechowywane są pliki na urządzeniu StorSimple.

"Klucz szyfrowania danych usługi" jest niezbędny do pomyślnej migracji. Teraz jest dobry czas, aby pobrać ten klucz z rekordów, po jednym dla każdego urządzenia w spisie.

Jeśli nie możesz znaleźć kluczy w rekordach, możesz wygenerować nowy klucz z urządzenia. Każde urządzenie ma unikatowy klucz szyfrowania.

#### <a name="change-the-service-data-encryption-key"></a>Zmienianie klucza szyfrowania danych usługi

[!INCLUDE [storage-files-migration-generate-key](../../../includes/storage-files-migration-generate-key.md)]

> [!CAUTION]
> Podczas podejmowania decyzji o skojarzeniu z urządzeniem StorSimple należy wziąć pod uwagę następujące kwestie:
>
> * Nawiązywanie połączenia za pośrednictwem sesji HTTPS jest najbezpiecznszą i zalecaną opcją.
> * Nawiązywanie bezpośredniego połączenia z konsolą szeregową urządzenia jest bezpieczne, ale łączenie się z konsolą szeregową za pośrednictwem przełączników sieciowych nie jest bezpieczne.
> * Połączenia sesji HTTP są dostępne, ale *nie są szyfrowane.* Nie są one zalecane, chyba że są używane w zamkniętej, zaufanej sieci.

### <a name="storsimple-volume-backups"></a>Kopie zapasowe woluminów StorSimple

Magazyn StorSimple oferuje różnicowe kopie zapasowe na poziomie woluminu. Udziały plików platformy Azure mają również tę możliwość, nazywaną migawkami udziałów.
Zadania migracji mogą przenosić tylko kopie zapasowe, a nie dane z woluminu na żywo. Dlatego najnowsza kopia zapasowa powinna zawsze być na liście kopii zapasowych przeniesionych w migracji.

Zdecyduj, czy musisz przenieść starsze kopie zapasowe podczas migracji.
Najlepszym rozwiązaniem jest utrzymanie możliwie jak najduszej tej listy, aby zadania migracji kończyły się szybciej.

Aby zidentyfikować krytyczne kopie zapasowe, które muszą zostać zmigrowane, należy utworzyć listę kontrolną zasad kopii zapasowych. Przykład:
* Najnowsza kopia zapasowa. (Uwaga: Najnowsza kopia zapasowa powinna zawsze być częścią tej listy).
* Jedna kopia zapasowa miesięcznie przez 12 miesięcy.
* Jedna kopia zapasowa na rok przez trzy lata. 

Później podczas tworzenia zadań migracji można użyć tej listy do zidentyfikowania dokładnych kopii zapasowych woluminów StorSimple, które muszą zostać zmigrowane w celu spełnienia wymagań na liście.

> [!CAUTION]
> Wybieranie więcej niż **50 kopii** zapasowych woluminów StorSimple nie jest obsługiwane.
> Zadania migracji mogą przenosić tylko kopie zapasowe, a nie dane z woluminu na żywo. W związku z tym najnowsza kopia zapasowa znajduje się najbliżej danych na żywo i dlatego zawsze powinna być częścią listy kopii zapasowych, które mają zostać przeniesione w ramach migracji.

### <a name="map-your-existing-storsimple-volumes-to-azure-file-shares"></a>Mapowanie istniejących woluminów StorSimple na udziały plików platformy Azure

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

### <a name="number-of-storage-accounts"></a>Liczba kont magazynu

Migracja będzie prawdopodobnie korzystać z wdrożenia wielu kont magazynu, z których każde zawiera mniejszą liczbę udziałów plików platformy Azure.

Jeśli udziały plików są wysoce aktywne (wykorzystywane przez wielu użytkowników lub aplikacje), dwa udziały plików platformy Azure mogą osiągnąć limit wydajności konta magazynu. W związku z tym najlepszym rozwiązaniem jest przeprowadzenie migracji do wielu kont magazynu, z których każde ma własne indywidualne udziały plików i zazwyczaj nie więcej niż dwa lub trzy udziały na konto magazynu.

Najlepszym rozwiązaniem jest wdrożenie kont magazynu z jednym udziałem plików. Jeśli masz udziały archiwalne, możesz utworzyć pulę wielu udziałów plików platformy Azure w ramach tego samego konta magazynu.

Te zagadnienia dotyczą bardziej bezpośredniego [dostępu do chmury](#direct-share-access-vs-azure-file-sync) (za pośrednictwem maszyny wirtualnej platformy Azure lub usługi) niż do Azure File Sync. Jeśli planujesz używać wyłącznie kont Azure File Sync tych udziałach, grupowanie kilku w ramach jednego konta usługi Azure Storage jest w porządku. W przyszłości może być chcieć migotować i przenosić aplikację do chmury, która miałaby bezpośredni dostęp do udziału plików. W tym scenariuszu korzystne byłoby uzyskanie większej wartości IOPS i przepływności. Możesz też rozpocząć korzystanie z usługi na platformie Azure, która będzie również korzystać z większej wartości IOPS i przepływności.

Jeśli masz listę udziałów, zamapuj każdy udział na konto magazynu, na którym będzie się znajdował.

> [!IMPORTANT]
> Wybierz region świadczenia usługi Azure i upewnij się, że każde konto magazynu Azure File Sync zasobów odpowiada wybranego regionu.
> Nie konfiguruj teraz ustawień sieci i zapory dla kont magazynu. Wprowadzenie tych konfiguracji w tym momencie uniemożliwi migrację. Skonfiguruj te ustawienia usługi Azure Storage po zakończeniu migracji.

### <a name="phase-1-summary"></a>Podsumowanie fazy 1

Na końcu fazy 1:

* Masz dobre omówienie urządzeń StorSimple i woluminów.
* Usługa Data Manager jest gotowa do uzyskiwania dostępu do woluminów StorSimple w chmurze, ponieważ dla każdego urządzenia StorSimple pobrano "klucz szyfrowania danych usługi".
* Masz plan, dla którego należy migrować woluminy i kopie zapasowe (jeśli nie są najnowsze).
* Wiesz, jak zamapować woluminy na odpowiednią liczbę udziałów plików i kont magazynu platformy Azure.

## <a name="phase-2-deploy-azure-storage-and-migration-resources"></a>Faza 2. Wdrażanie zasobów magazynu i migracji platformy Azure

W tej sekcji omówiono zagadnienia dotyczące wdrażania różnych typów zasobów, które są potrzebne na platformie Azure. Niektóre będą przechowywać dane po migracji, a niektóre są potrzebne wyłącznie do migracji. Nie rozpoczynaj wdrażania zasobów, dopóki nie zostanie sfinalizowany plan wdrożenia. Zmiana niektórych aspektów zasobów platformy Azure po ich wdrożeniu jest trudna, czasami niemożliwa.

### <a name="deploy-storage-accounts"></a>Wdrażanie kont magazynu

Prawdopodobnie konieczne będzie wdrożenie kilku kont usługi Azure Storage. Każdy z nich będzie zawierać mniejszą liczbę udziałów plików platformy Azure zgodnie z planem wdrożenia, które zostały ukończone w poprzedniej sekcji tego artykułu. Przejdź do Azure Portal, aby [wdrożyć planowane konta magazynu.](../common/storage-account-create.md#create-a-storage-account) Należy wziąć pod uwagę następujące ustawienia podstawowe dla każdego nowego konta magazynu.

> [!IMPORTANT]
> Nie konfiguruj teraz ustawień sieci i zapory dla kont magazynu. Wykonanie tych konfiguracji w tym momencie uniemożliwiłoby migrację. Skonfiguruj te ustawienia usługi Azure Storage po zakończeniu migracji.

#### <a name="subscription"></a>Subskrypcja

Możesz użyć tej samej subskrypcji, która została użyta dla wdrożenia usługi StorSimple, lub innej. Jedynym ograniczeniem jest to, że subskrypcja musi znajdować się w tej samej Azure Active Directory dzierżawy co subskrypcja usługi StorSimple. Przed rozpoczęciem migracji rozważ przeniesienie subskrypcji usługi StorSimple do odpowiedniej dzierżawy. Możesz przenieść tylko całą subskrypcję. Nie można przenieść pojedynczych zasobów storSimple do innej dzierżawy lub subskrypcji.

#### <a name="resource-group"></a>Grupa zasobów

Grupy zasobów pomagają w organizacji zasobów i uprawnieniach administratora do zarządzania. Dowiedz się więcej o [grupach zasobów na platformie Azure.](../../azure-resource-manager/management/manage-resource-groups-portal.md#what-is-a-resource-group)

#### <a name="storage-account-name"></a>Nazwa konta magazynu

Nazwa konta magazynu stanie się częścią adresu URL i będzie podlegać pewnym ograniczeniom znaków. W konwencji nazewnictwa należy wziąć pod uwagę, że nazwy kont magazynu muszą być unikatowe na świecie, zezwalać tylko na małe litery i cyfry, wymagać od 3 do 24 znaków i nie zezwalać na znaki specjalne, takie jak łączniki lub podkreślenia. Aby uzyskać więcej informacji, zobacz [Reguły nazewnictwa zasobów usługi Azure Storage.](../../azure-resource-manager/management/resource-name-rules.md#microsoftstorage)

#### <a name="location"></a>Lokalizacja

Lokalizacja lub region świadczenia usługi Azure konta magazynu jest bardzo ważna. Jeśli używasz Azure File Sync magazynu, wszystkie konta magazynu muszą znajdować się w tym samym regionie co zasób usługi synchronizacji magazynu. Wybierany region świadczenia usługi Azure powinien być blisko lokalnych serwerów i użytkowników lub powinien być centralny. Po wdrożeniu zasobu nie można zmienić jego regionu.

Możesz wybrać inny region, z którego obecnie znajdują się dane storSimple (konto magazynu).

> [!IMPORTANT]
> W przypadku wyboru innego regionu niż bieżąca [](https://azure.microsoft.com/pricing/details/bandwidth) lokalizacja konta magazynu StorSimple podczas migracji będą naliczane opłaty za ruch wychodzący. Dane opuszczą region storSimple i wejdą do nowego regionu konta magazynu. Jeśli pozostaniesz w tym samym regionie platformy Azure, nie są naliczane opłaty za przepustowość.

#### <a name="performance"></a>Wydajność

Masz możliwość wyboru magazynu w warstwie Premium (SSD) dla udziałów plików platformy Azure lub magazynu w warstwie Standardowa. Magazyn w [warstwie Standardowa zawiera kilka warstw dla udziału plików.](storage-how-to-create-file-share.md#change-the-tier-of-an-azure-file-share) Magazyn w chmurze w chmurze w chmurze to właściwa opcja dla większości klientów migrowania z usługi StorSimple.

Nadal nie masz pewności?

* Wybierz usługę Premium Storage, jeśli potrzebujesz [wydajności udziału plików platformy Azure w witrynie Premium.](understanding-billing.md#provisioned-model)
* Wybierz magazyn w standardowych magazynach dla obciążeń serwera plików ogólnego przeznaczenia, w tym gorące dane i dane archiwum. Wybierz również magazyn w chmurze, jeśli jedynym obciążeniem dla udziału w chmurze będzie Azure File Sync.

#### <a name="account-kind"></a>Rodzaj konta

* W przypadku magazynu w standardowych wersjach wybierz *pozycję StorageV2 (ogólnego przeznaczenia, wersja 2).*
* W przypadku udziałów plików w premium wybierz *pozycję PlikTorage.*

#### <a name="replication"></a>Replikacja

Dostępnych jest kilka ustawień replikacji. Dowiedz się więcej o różnych typach replikacji.

Wybierz tylko jedną z następujących dwóch opcji:

* *Magazyn lokalnie nadmiarowy (LRS).*
* *Magazyn strefowo nadmiarowy (ZRS),* który nie jest dostępny we wszystkich regionach świadczenia usługi Azure.

> [!NOTE]
> Tylko typy nadmiarowości LRS i ZRS są zgodne z dużymi udziałami plików platformy Azure o pojemności 100 TiB.

Magazyn geograficznie nadmiarowy (GRS) we wszystkich odmianach nie jest obecnie obsługiwany. Później możesz przełączyć swój typ nadmiarowości i przełączyć się na magazyn GRS, gdy obsługa zostanie na platformie Azure.

#### <a name="enable-100-tib-capacity-file-shares"></a>Włączanie udziałów plików o pojemności 100 TiB

:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-how-to-create-large-file-share/large-file-shares-advanced-enable.png" alt-text="Obraz przedstawiający kartę Zaawansowane w Azure Portal tworzenia konta magazynu.":::
    :::column-end:::
    :::column:::
        W sekcji **Zaawansowane** kreatora nowego konta magazynu w Azure Portal można  włączyć obsługę dużych udziałów plików na tym koncie magazynu. Jeśli ta opcja nie jest dostępna, najprawdopodobniej wybrano nieprawidłowy typ nadmiarowości. Upewnij się, że wybierasz tylko LRS lub ZRS, aby ta opcja stała się dostępna.
    :::column-end:::
:::row-end:::

Wybór dużych udziałów plików o pojemności 100 TiB ma kilka zalet:

* Wydajność jest znacznie większa w porównaniu z mniejszymi udziałami plików o pojemności 5 TiB (na przykład 10 razy większą niż liczba IOPS).
* Migracja zakończy się znacznie szybciej.
* Zapewnisz, że udział plików będzie miał wystarczającą pojemność do przechowywania wszystkich danych, które zostaną do niego zmigrowane, w tym wymaganych różnicowych kopii zapasowych pojemności magazynu.
* Przyszły wzrost jest objęty.

### <a name="azure-file-shares"></a>Udziały plików platformy Azure

Po utworzeniu kont magazynu przejdź  do sekcji Udział plików na koncie magazynu i wdróć odpowiednią liczbę udziałów plików platformy Azure zgodnie z planem migracji z fazy 1. Rozważ korzystanie z następujących podstawowych ustawień dla nowych udziałów plików na platformie Azure.

:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-new-share.png" alt-text="Zrzut Azure Portal przedstawiający nowy interfejs użytkownika udziału plików.":::
    :::column-end:::
    :::column:::
        </br>**Nazwa**</br>Obsługiwane są małe litery, cyfry i łączniki.</br></br>**Przydziału**</br>Limit przydziału w tym miejscu jest porównywalny z twardym limitem przydziału SMB w wystąpieniu systemu Windows Server. Najlepszym rozwiązaniem jest nieuzysłanie w tym miejscu limitu przydziału, ponieważ migracja i inne usługi nie powiodą się po osiągnięciu limitu przydziału.</br></br>**Warstwy**</br>Wybierz **pozycję Transakcja zoptymalizowana** pod kątem nowego udziału plików. Podczas migracji będzie wykonywanych wiele transakcji. Tańsza zmiana warstwy na warstwę najlepiej dopasowaną do obciążenia jest bardziej opłacana.
    :::column-end:::
:::row-end:::

### <a name="storsimple-data-manager"></a>StorSimple Data Manager

Zasób platformy Azure, który będzie przechowywać zadania migracji, jest nazywany **StorSimple Data Manager**. Wybierz **pozycję Nowy** zasób i wyszukaj go. Następnie wybierz pozycję **Utwórz**.

Ten tymczasowy zasób jest używany do aranżacji. Aprowizuje się go po zakończeniu migracji. Należy ją wdrożyć w tej samej subskrypcji, grupie zasobów i regionie co konto magazynu StorSimple.

### <a name="azure-file-sync"></a>Azure File Sync

Za Azure File Sync można dodać buforowanie najczęściej używanych plików w środowisku lokalnym. Podobnie jak w przypadku możliwości buforowania urządzenia StorSimple, funkcja warstw w chmurze usługi Azure File Sync oferuje opóźnienie dostępu lokalnego w połączeniu z lepszą kontrolą dostępnej pojemności pamięci podręcznej w wystąpieniu systemu Windows Server i synchronizacją wielu lokacji. Jeśli celem jest posiadanie lokalnej pamięci podręcznej, w sieci lokalnej przygotuj maszynę wirtualną z systemem Windows Server (obsługiwane są również serwery fizyczne i klastry trybu failover) z wystarczającą pojemnością magazynu dołączonego bezpośrednio.

> [!IMPORTANT]
> Nie należy jeszcze Azure File Sync. Najlepszym rozwiązaniem jest skonfigurowanie Azure File Sync po zakończeniu migracji udziału. Wdrażanie Azure File Sync nie powinno rozpoczynać się przed fazą 4 migracji.

### <a name="phase-2-summary"></a>Podsumowanie fazy 2

Po zakończeniu fazy 2 wdrożysz konta magazynu i wszystkie udziały plików platformy Azure na tych kontach. Będziesz również mieć zasób StorSimple Data Manager zasobów. Drugiej z nich użyjesz w fazie 3 podczas konfigurowania zadań migracji.

## <a name="phase-3-create-and-run-a-migration-job"></a>Faza 3. Tworzenie i uruchamianie zadania migracji

W tej sekcji opisano sposób skonfigurowania zadania migracji i dokładnego mapowania katalogów na woluminie StorSimple, który powinien zostać skopiowany do wybranego docelowego udziału plików platformy Azure. Aby rozpocząć, przejdź do swojego  StorSimple Data Manager, znajdź definicje zadań w menu i wybierz **pozycję + Definicja zadania.** Domyślnym typem magazynu docelowego jest udział **plików platformy Azure.**

![Typy zadań migracji serii StorSimple 8000.](media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-new-job-type.png "Zrzut ekranu przedstawiający definicje zadań Azure Portal otwarte okno dialogowe Definicje zadań z pytaniem o typ zadania: Kopiowanie do udziału plików lub kontenera obiektów blob.")

:::row:::
    :::column:::
        ![Zadanie migracji serii StorSimple 8000.](media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-new-job.png "Zrzut ekranu przedstawiający formularz tworzenia nowego zadania dla zadania migracji.")
    :::column-end:::
    :::column:::
        **Nazwa definicji zadania**</br>Ta nazwa powinna wskazywać zestaw przenoszących plików. Dobrym rozwiązaniem jest nadanie mu nazwy podobnej do nazwy udziału plików platformy Azure. </br></br>**Lokalizacja, w której jest uruchamiane zadanie**</br>Podczas wybierania regionu musisz wybrać ten sam region co konto magazynu StorSimple lub, jeśli nie jest on dostępny, region blisko niego. </br></br><h3>Element źródłowy</h3>**Subskrypcja źródłowa**</br>Wybierz subskrypcję, w której jest przechowywana twoja Menedżer urządzeń StorSimple. </br></br>**Zasób StorSimple**</br>Wybierz urządzenie StorSimple Menedżer urządzeń urządzenie jest zarejestrowane. </br></br>**Klucz szyfrowania danych usługi**</br>Sprawdź [poprzednią sekcję w tym artykule,](#storsimple-service-data-encryption-key) jeśli nie możesz zlokalizować klucza w rekordach. </br></br>**Urządzenie**</br>Wybierz urządzenie StorSimple przechowujące wolumin, na którym chcesz przeprowadzić migrację. </br></br>**Wolumin**</br>Wybierz wolumin źródłowy. Później zdecydujesz, czy chcesz przeprowadzić migrację całego woluminu lub podkatalogów do docelowego udziału plików platformy Azure.</br></br> **Kopie zapasowe woluminów**</br>Możesz wybrać pozycję *Wybierz kopie zapasowe woluminów,* aby wybrać konkretne kopie zapasowe do przeniesienia w ramach tego zadania. W nadchodzącej, [dedykowanej sekcji tego artykułu](#selecting-volume-backups-to-migrate) szczegółowo opisano ten proces.</br></br><h3>Cel</h3>Wybierz subskrypcję, konto magazynu i udział plików platformy Azure jako element docelowy tego zadania migracji.</br></br><h3>Mapowanie katalogów</h3>[W dedykowanej sekcji tego artykułu](#directory-mapping)omówiono wszystkie istotne szczegóły.
    :::column-end:::
:::row-end:::

### <a name="selecting-volume-backups-to-migrate"></a>Wybieranie kopii zapasowych woluminów do migracji

Istnieją ważne aspekty wyboru kopii zapasowych, które należy zmigrować:

- Zadania migracji mogą przenosić tylko kopie zapasowe, a nie dane z woluminu na żywo. Dlatego najnowsza kopia zapasowa jest najbliżej danych na żywo i powinna zawsze być na liście kopii zapasowych przeniesionych podczas migracji.
- Upewnij się, że najnowsza kopia zapasowa jest najnowsza, aby zapewnić jak najmniejszy rozmiar udziału na żywo. Warto ręcznie wyzwolić i wykonać kopię zapasową innego woluminu przed utworzeniem zadania migracji. Niewielki przyrost udziału na żywo poprawi środowisko migracji. Jeśli ta różnica może być równa zero = po zapisaniu najnowszej kopii zapasowej na liście nie ma już żadnych zmian w woluminie StorSimple — faza 5: przecięcie użytkownika zostanie znacząco uproszczone i może zostać wykonane.
- Kopie zapasowe muszą być odtwarzane w udziałach plików platformy Azure od **najstarszego do najnowszego**. Starszej kopii zapasowej nie można "sortować" na liście kopii zapasowych w udziałach plików platformy Azure po uruchomieniu zadania migracji. Dlatego przed utworzeniem zadania należy upewnić  się, że lista kopii zapasowych jest ukończona. 
- Tej listy kopii zapasowych w zadaniu nie można zmodyfikować po utworzeniu zadania — nawet jeśli zadanie nigdy nie było tworzone. 

:::row:::
    :::column:::        
        :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-select-backups.png" alt-text="Zrzut ekranu przedstawiający formularz tworzenia nowego zadania ze szczegółami części, w której do migracji są wybierane kopie zapasowe usługi StorSimple." lightbox="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-select-backups-expanded.png":::
    :::column-end:::
    :::column:::
        Aby wybrać kopie zapasowe woluminu StorSimple dla zadania migracji, wybierz pozycję Wybierz kopie zapasowe woluminu *w* formularzu tworzenia zadania.
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-select-backups-annotated.png" alt-text="Obraz przedstawiający, że górna połowa bloku wybierania kopii zapasowych zawiera listę wszystkich dostępnych kopii zapasowych. Wybrana kopia zapasowa będzie wyszarzona na tej liście i dodana do drugiej listy w dolnej połowie bloku. Można go również usunąć ponownie." lightbox="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-select-backups-annotated.png":::
    :::column-end:::
    :::column:::
        Gdy zostanie otwarty blok wyboru kopii zapasowej, zostanie on podzielony na dwie listy. Na pierwszej liście są wyświetlane wszystkie dostępne kopie zapasowe. Zestaw wyników można rozwinąć i zawęzić, filtrując według określonego zakresu czasu. (zobacz następną sekcję) </br></br>Wybrana kopia zapasowa będzie wyświetlana jako wyszarzona i zostanie dodana do drugiej listy w dolnej połowie bloku. Druga lista zawiera wszystkie kopie zapasowe wybrane do migracji. Kopię zapasową wybraną w błędzie można również usunąć ponownie.
        > [!CAUTION]
        > Musisz wybrać **wszystkie kopie zapasowe,** które chcesz migrować. Nie można dodać starszych kopii zapasowych później. Nie można zmodyfikować zadania w celu zmiany wyboru po jego utworzeniu.
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-select-backups-time.png" alt-text="Zrzut ekranu przedstawiający wybór zakresu czasu bloku wyboru kopii zapasowej." lightbox="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-select-backups-time-expanded.png":::
    :::column-end:::
    :::column:::
        Domyślnie lista jest filtrowana w celu pokazania kopii zapasowych woluminu StorSimple w ciągu ostatnich siedmiu dni, aby ułatwić wybranie najnowszej kopii zapasowej. W przypadku kopii zapasowych w przeszłości użyj filtru zakresu czasu w górnej części bloku. Możesz wybrać z istniejącego filtru lub ustawić niestandardowy zakres czasu, aby filtrować tylko kopie zapasowe wykonane w tym okresie.
    :::column-end:::
:::row-end:::

> [!CAUTION]
> Wybieranie więcej niż 50 kopii zapasowych woluminów StorSimple nie jest obsługiwane. Zadania z dużą liczbą kopii zapasowych mogą się nie powieść.

### <a name="directory-mapping"></a>Mapowanie katalogów

Mapowanie katalogów jest opcjonalne dla zadania migracji. Jeśli pozostawisz sekcję  pustą, wszystkie pliki i foldery w katalogu głównym woluminu StorSimple zostaną przeniesione do katalogu głównego docelowego udziału plików platformy Azure. W większości przypadków przechowywanie zawartości całego woluminu w udziałach plików platformy Azure nie jest najlepszym rozwiązaniem. Często lepiej jest podzielić zawartość woluminu na wiele udziałów plików na platformie Azure. Jeśli jeszcze nie masz planu, zobacz Najpierw mapowanie woluminu [StorSimple na udziały](#map-your-existing-storsimple-volumes-to-azure-file-shares) plików platformy Azure.

W ramach planu migracji można było zdecydować, że foldery na woluminie StorSimple muszą być podzielone na wiele udziałów plików platformy Azure. W takim przypadku możesz wykonać ten podział przez:

1. Definiowanie wielu zadań migracji folderów na jednym woluminie. Każdy z nich będzie miał to samo źródło woluminu StorSimple, ale inny udział plików platformy Azure jako obiekt docelowy.
1. Określenie dokładnie, które foldery z woluminu StorSimple należy zmigrować  do określonego udziału plików przy użyciu sekcji Mapowanie katalogu w formularzu tworzenia zadania i zgodnie z określoną semantyką [mapowania.](#semantic-elements)

> [!IMPORTANT]
> Nie można zweryfikować ścieżek i wyrażeń mapowania w tym formularzu podczas jego przesyłanego formularza. Jeśli mapowania są określone niepoprawnie, zadanie może się całkowicie nie powieść lub spowodować niepożądany wynik. W takim przypadku zazwyczaj najlepiej jest usunąć udział plików platformy Azure, ponownie go utworzyć, a następnie naprawić instrukcje mapowania w nowym zadaniu migracji dla udziału. Uruchomienie nowego zadania za pomocą stałych instrukcji mapowania może naprawić pominięte foldery i wprowadzić je do istniejącego udziału. W ten sposób można jednak rozwiązać tylko te foldery, które zostały pominięte z powodu błędnych pisowni ścieżki.

#### <a name="semantic-elements"></a>Elementy semantyczne

Mapowanie jest wyrażane od lewej do prawej: [\ścieżka źródłowa] \> [\ścieżka docelowa].

|Znak semantyczny          | Znaczenie  |
|:---------------------------|:---------|
| **\\**                     | Wskaźnik poziomu głównego.       |
| **\>**                     | Operatory [Source] i [target-mapping].     |
|**\|** lub RETURN (nowy wiersz) | Separator dwóch instrukcji mapowania folderów. </br>Alternatywnie możesz pominąć ten znak i wybrać klawisz **Enter,** aby pobrać następne wyrażenie mapowania we własnym wierszu.        |

### <a name="examples"></a>Przykłady
Przenosi zawartość folderu *dane użytkownika* do katalogu głównego docelowego udziału plików:
``` console
\User data > \
```
Przenosi całą zawartość woluminu do nowej ścieżki w docelowym udziału plików:
``` console
\ > \Apps\HR tracker
```
Przenosi zawartość folderu źródłowego do nowej ścieżki w docelowym udziału plików:
``` console
\HR resumes-Backup > \Backups\HR\resumes
```
Sortuje wiele lokalizacji źródłowych w nową strukturę katalogów:
``` console
\HR\Candidate Tracker\v1.0 > \Apps\Candidate tracker
\HR\Candidates\Resumes > \HR\Candidates\New
\Archive\HR\Old Resumes > \HR\Candidates\Archived
```

### <a name="semantic-rules"></a>Reguły semantyczne

* Zawsze określaj ścieżki folderów względem poziomu głównego.
* Rozpocznij każdą ścieżkę folderu od wskaźnika poziomu głównego " \\ ".
* Nie dołączaj liter dysku.
* W przypadku określania wielu ścieżek ścieżki źródłowe lub docelowe nie mogą nakładać się na siebie:</br>
   Przykład nakładania się nieprawidłowej ścieżki źródłowej:</br>
    *\\folder\1 > \\ folder*</br>
    *\\folder \\ 1 \\ 2 > \\ folder2*</br>
   Przykład nakładania się nieprawidłowej ścieżki docelowej:</br>
   *\\folder > \\*</br>
   *\\folder2 > \\*</br>
* Foldery źródłowe, które nie istnieją, zostaną zignorowane.
* Zostaną utworzone struktury folderów, które nie istnieją w folderze docelowym.
* Podobnie jak w systemie Windows, w nazwach folderów nie jest uwzględniania ich litera, ale jest zachowywana.

> [!NOTE]
> Zawartość folderu *\System Volume Information* i pliku *$Recycle.Bin* na woluminie StorSimple nie zostanie skopiowana przez zadanie migracji.

### <a name="run-a-migration-job"></a>Uruchamianie zadania migracji

Zadania migracji są wyświetlane w obszarze *Definicje zadań* Data Manager zasobie wdrożonym w grupie zasobów.
Z listy definicji zadań wybierz zadanie, które chcesz uruchomić.

W bloku zadania, który zostanie otwarty, możesz zobaczyć przebiegi zadań na dolnej liście. Początkowo ta lista będzie pusta. W górnej części bloku znajduje się polecenie o nazwie *Uruchom zadanie*. To polecenie nie spowoduje natychmiastowego uruchomienia zadania. Spowoduje to otwarcie bloku **Uruchamiania** zadania:

:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-run-job.png" alt-text="Obraz przedstawiający blok uruchamiania zadania z otwartą kontrolką listy rozwijanej z wyświetlonymi wybranymi kopiami zapasowym do zmigrowania. Najstarsza kopia zapasowa jest wyróżniona, należy ją najpierw wybrać." lightbox="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-run-job-expanded.png":::
    :::column-end:::
    :::column:::
        W tej wersji każde zadanie musi zostać uruchomione kilka razy. </br></br>**Należy rozpocząć od najstarszej kopii zapasowej z listy kopii zapasowych, które chcesz migrować.** (wyróżnione na obrazie)</br></br>Zadanie jest uruchamiane ponownie, tyle razy, ile masz wybranych kopii zapasowych, za każdym razem względem progresywnie nowszej kopii zapasowej.
        </br></br>
        > [!CAUTION]
        > Konieczne jest, aby najpierw uruchomić zadanie migracji z najstarszą wybraną kopią zapasową, a następnie ponownie, za każdym razem z przyrostowo nowszą kopią zapasową. Zawsze należy zachować kolejność kopii zapasowych ręcznie — od najstarszego do najnowszego.
    :::column-end:::
:::row-end:::

### <a name="phase-3-summary"></a>Podsumowanie fazy 3

Po zakończeniu fazy 3 należy uruchomić co najmniej jedno z zadań migracji z woluminów StorSimple do udziałów plików platformy Azure. To samo zadanie migracji będzie uruchamiane kilka razy, od najstarszych do najnowszych kopii zapasowych, które muszą zostać zmigrowane. Teraz możesz skoncentrować się na konfigurowaniu Azure File Sync udziału (po zakończeniu zadań migracji dla udziału) lub kierowaniu dostępu do udziału dla pracowników i aplikacji do udziału plików platformy Azure.

## <a name="phase-4-access-your-azure-file-shares"></a>Faza 4. Uzyskiwanie dostępu do udziałów plików platformy Azure

Istnieją dwie główne strategie uzyskiwania dostępu do udziałów plików platformy Azure:

* **Azure File Sync:** wdrażanie [Azure File Sync](#deploy-azure-file-sync) w lokalnym wystąpieniu systemu Windows Server. Azure File Sync ma wszystkie zalety lokalnej pamięci podręcznej, podobnie jak w przypadku usługi StorSimple.
* **Bezpośredni dostęp do udziału:** [wdrażanie bezpośredniego dostępu do udziału.](#deploy-direct-share-access) Użyj tej strategii, jeśli scenariusz dostępu do danego udziału plików platformy Azure nie będzie korzystać z lokalnego buforowania lub nie masz już możliwości hostowania lokalnego wystąpienia systemu Windows Server. W tym miejscu użytkownicy i aplikacje będą nadal mieć dostęp do udziałów SMB za pośrednictwem protokołu SMB. Te udziały nie znajdują się już na serwerze lokalnym, ale bezpośrednio w chmurze.

W fazie [1](#phase-1-prepare-for-migration) tego przewodnika należy już zdecydować, która opcja jest dla Ciebie najlepsza.

Pozostała część tej sekcji koncentruje się na instrukcjach wdrażania.

### <a name="deploy-azure-file-sync"></a>Wdrażanie usługi Azure File Sync

Na tym czas wdrożyć część Azure File Sync.

1. Utwórz zasób Azure File Sync w chmurze.
1. Wd Azure File Sync agenta na serwerze lokalnym.
1. Zarejestruj serwer za pomocą zasobu w chmurze.

Nie twórz jeszcze żadnych grup synchronizacji. Konfigurowanie synchronizacji z udziałem plików platformy Azure powinno nastąpić dopiero po zakończeniu zadań migracji do udziału plików platformy Azure. Jeśli rozpoczęto korzystanie z usługi Azure File Sync przed ukończeniem migracji, migracja byłaby niepotrzebnie trudna, ponieważ nie można było łatwo ustalić, kiedy należy zainicjować przecięcie.

#### <a name="deploy-the-azure-file-sync-cloud-resource"></a>Wdrażanie zasobu Azure File Sync w chmurze

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

> [!TIP]
> Jeśli chcesz zmienić region świadczenia usługi Azure, w którym znajdują się dane po zakończeniu migracji, wd wdrażaj usługę synchronizacji magazynu w tym samym regionie, w którym znajdują się docelowe konta magazynu dla tej migracji.

#### <a name="deploy-an-on-premises-windows-server-instance"></a>Wdrażanie lokalnego wystąpienia systemu Windows Server

* Utwórz system Windows Server 2019 (co najmniej 2012 R2) jako maszynę wirtualną lub serwer fizyczny. Obsługiwany jest również klaster trybu failover systemu Windows Server. Nie używaj ponownie serwera frontonu storSimple 8100 ani 8600.
* Aprowizowanie lub dodawanie bezpośrednio dołączonego magazynu. Magazyn dołączony do sieci nie jest obsługiwany.

Najlepszym rozwiązaniem jest nadanie noweowi wystąpieniu systemu Windows Server magazynu o równej lub większej pojemności niż urządzenie StorSimple 8100 lub 8600 dostępne lokalnie do buforowania. Wystąpienie systemu Windows Server będzie używane w taki sam sposób, jak w przypadku urządzenia StorSimple. Jeśli urządzenie ma taką samą ilość miejsca do magazynowania, środowisko buforowania powinno być podobne, jeśli nie jest takie samo. W każdej chwili możesz dodać lub usunąć magazyn z wystąpienia systemu Windows Server. Ta funkcja umożliwia skalowanie rozmiaru woluminu lokalnego i ilości magazynu lokalnego dostępnego do buforowania.

#### <a name="prepare-the-windows-server-instance-for-file-sync"></a>Przygotowywanie wystąpienia systemu Windows Server do synchronizacji plików

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

#### <a name="configure-azure-file-sync-on-the-windows-server-instance"></a>Konfigurowanie Azure File Sync w wystąpieniu systemu Windows Server

Zarejestrowane lokalne wystąpienie systemu Windows Server musi być gotowe i połączone z Internetem na czas tego procesu.

> [!IMPORTANT]
> Przed kontynuowaniem należy ukończyć migrację plików i folderów usługi StorSimple do udziału plików platformy Azure. Upewnij się, że nie są już wprowadzone żadne zmiany udziału plików.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!IMPORTANT]
> Pamiętaj, aby włączyć warstwy w chmurze. Funkcja warstw w chmurze Azure File Sync, która umożliwia serwerowi lokalnemu korzystanie z mniejszej pojemności magazynu niż jest przechowywana w chmurze, ale pełna przestrzeń nazw jest dostępna. Lokalnie interesujące dane są również buforowane lokalnie w celu uzyskania szybkiej wydajności dostępu lokalnego. Innym powodem, dla którego warto w tym kroku włączyć warstwy w chmurze, jest to, że na tym etapie nie chcemy synchronizować zawartości plików. W tej chwili powinna być przenosząca tylko przestrzeń nazw.

### <a name="deploy-direct-share-access"></a>Wdrażanie bezpośredniego dostępu do udziału

:::row:::
    :::column:::
        <iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/jd49W33DxkQ" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
    :::column-end:::
    :::column:::
        Ten film wideo zawiera przewodnik i pokaz bezpiecznego uwidocznienia udziałów plików platformy Azure bezpośrednio dla pracowników i aplikacji z informacjami w pięciu prostych krokach.</br>
        Film wideo zawiera dokumentację dedykowaną dla niektórych tematów:

* [Przegląd tożsamości](storage-files-active-directory-overview.md)
* [Jak dołączyć do domeny konto magazynu](storage-files-identity-auth-active-directory-enable.md)
* [Omówienie sieci dla udziałów plików platformy Azure](storage-files-networking-overview.md)
* [Jak skonfigurować publiczne i prywatne punkty końcowe](storage-files-networking-endpoints.md)
* [Jak skonfigurować sieć VPN S2S](storage-files-configure-s2s-vpn.md)
* [Jak skonfigurować sieć VPN P2S systemu Windows](storage-files-configure-p2s-vpn-windows.md)
* [Jak skonfigurować sieć VPN P2S systemu Linux](storage-files-configure-p2s-vpn-linux.md)
* [Jak skonfigurować przekazywanie DNS](storage-files-networking-dns.md)
* [Konfigurowanie systemu plików DFS-N](/windows-server/storage/dfs-namespaces/dfs-overview)
   :::column-end:::
:::row-end:::

### <a name="phase-4-summary"></a>Podsumowanie fazy 4

W tej fazie utworzono i uruchamiasz wiele zadań migracji w StorSimple Data Manager. Te zadania zmigrowały pliki i foldery do udziałów plików platformy Azure. Wdrożono również usługę Azure File Sync lub przygotowano konta sieci i magazynu do bezpośredniego dostępu do udziału.

## <a name="phase-5-user-cut-over"></a>Faza 5: przecięcie użytkownika

Ta faza dotyczy opakowywania migracji:

* Zaplanuj przestój.
* Nadąż za zmianami wprowadzonymi przez użytkowników i aplikacje po stronie urządzenia StorSimple, gdy zadania migracji w fazie 3 zostały uruchomione.
* Przekłoń użytkowników do nowego wystąpienia systemu Windows Server Azure File Sync lub udziałów plików platformy Azure za pośrednictwem bezpośredniego dostępu do udziału.

### <a name="plan-your-downtime"></a>Planowanie przestoju

Takie podejście do migracji wymaga pewnego przestoju dla użytkowników i aplikacji. Celem jest ograniczenie przestoju do minimum. Pomocne mogą być następujące kwestie:

* Zachowaj dostępne woluminy StorSimple podczas uruchamiania zadań migracji.
* Po zakończeniu uruchamiania zadań migracji danych dla udziału należy usunąć dostęp użytkowników (co najmniej dostęp do zapisu) z woluminów lub udziałów usługi StorSimple. Ostateczna kopia narzędzia RoboCopy nadrobić zaległości w twoim udziałach plików platformy Azure. Następnie możesz wycinać użytkowników. Miejsce uruchomienia programu RoboCopy zależy od tego, czy wybrano opcję Azure File Sync czy bezpośredniego dostępu do udziału. W nadchodzącej sekcji dotyczącej programu RoboCopy ominie się ten temat.
* Po zakończeniu uzupełnienia narzędzia RoboCopy możesz udostępnić nową lokalizację użytkownikom bezpośrednio przez udział plików platformy Azure lub udział SMB w wystąpieniu systemu Windows Server przy użyciu Azure File Sync. Często wdrożenie DFS-N pomoże szybko i wydajnie wykonać przecięcie. Dzięki temu istniejące adresy udziałów będą spójne i zostaną ponownie przeniesione do nowej lokalizacji zawierającej zmigrowane pliki i foldery.

### <a name="determine-when-your-namespace-has-fully-synced-to-your-server"></a>Określanie, kiedy przestrzeń nazw została w pełni zsynchronizowana z serwerem

W przypadku Azure File Sync udziału plików platformy Azure należy ustalić, że cała przestrzeń nazw zakończyła  pobieranie na serwer przed uruchomieniem dowolnego lokalnego narzędzia RoboCopy. Czas pobierania przestrzeni nazw zależy od liczby elementów w twoim udziału plików platformy Azure. Istnieją dwie metody określania, czy przestrzeń nazw została w pełni dotarta na serwer.

#### <a name="azure-portal"></a>Azure Portal

Możesz użyć tej Azure Portal, aby zobaczyć, kiedy przestrzeń nazw została w pełni dotarta.

* Zaloguj się do Azure Portal i przejdź do grupy synchronizacji. Sprawdź stan synchronizacji grupy synchronizacji i punktu końcowego serwera.
* Interesujący kierunek to pobranie. Jeśli punkt końcowy serwera jest nowo aprownizowany, zostanie pokazana synchronizacja początkowa, co oznacza, że przestrzeń nazw nadal spada.
Po zmianie na wszystko oprócz synchronizacji **początkowej** przestrzeń nazw zostanie w pełni wypełniona na serwerze. Teraz możesz kontynuować z lokalnym programem RoboCopy.

#### <a name="windows-server-event-viewer"></a>Windows Server Podgląd zdarzeń

Można również użyć Podgląd zdarzeń wystąpienia systemu Windows Server, aby określić, kiedy przestrzeń nazw została w pełni dotarta.

1. Otwórz okno **Podgląd zdarzeń**, a następnie przejdź do **aplikacji i usług**.
1. Przejdź do folderu i otwórz **plik Microsoft\FileSync\Agent\Telemetry.**
1. Poszukaj najnowszego zdarzenia **9102,** które odpowiada ukończonej sesji synchronizacji.
1. Wybierz **pozycję Szczegóły** i upewnij się, że patrzysz na zdarzenie, w którym wartość **SyncDirection** to **Pobierz.**
1. W przypadku czasu ukończenia pobierania przestrzeni nazw na serwer będzie jedno zdarzenie ze scenariuszem **,** **wartością FullGhostedSync** i **HResult**  =  **0.**
1. Jeśli to zdarzenie nie zostanie pominięte, możesz również poszukać innych zdarzeń **9102** za pomocą plików **SyncDirection** Download i  =     =  **Scenario "RegularSync".** Znalezienie jednego z tych zdarzeń wskazuje również, że obszar nazw zakończył pobieranie i synchronizację, a następnie do regularnych sesji synchronizacji, bez względu na to, czy w tej chwili istnieje coś do zsynchronizowania.

### <a name="a-final-robocopy"></a>Końcowy program RoboCopy

W tym momencie istnieją różnice między lokalnym wystąpieniem systemu Windows Server i urządzeniem StorSimple 8100 lub 8600.

1. Należy nadrobić zaległości dotyczące zmian wprowadzonych przez użytkowników lub aplikacje po stronie urządzenia StorSimple w trakcie migracji.
1. W przypadku, gdy używasz Azure File Sync: urządzenie StorSimple ma wypełnioną pamięć podręczną, a wystąpienie systemu Windows Server ma tylko przestrzeń nazw bez zawartości plików przechowywanej lokalnie w tym czasie. Końcowy plik RoboCopy może pomóc szybko uruchomić lokalną pamięć podręczną Azure File Sync przez ściągnięcie lokalnie buforowanej zawartości plików tak, jak jest dostępna, i może zmieścić się na Azure File Sync pamięci podręcznej.
1. Niektóre pliki mogły zostać pozostawione przez zadanie migracji z powodu nieprawidłowych znaków. Jeśli tak, skopiuj je do Azure File Sync z włączoną obsługą systemu Windows Server. Później można je dostosować tak, aby były synchronizowane. Jeśli nie używasz nazwy Azure File Sync udziału, lepiej jest zmieniać nazwy plików z nieprawidłowymi znakami na woluminie StorSimple. Następnie uruchom narzędzie RoboCopy bezpośrednio względem udziału plików platformy Azure.

> [!WARNING]
> W programie Robocopy w systemie Windows Server 2019 występuje obecnie problem, który powoduje ponowne skopiowanie plików z warstwami przez usługę Azure File Sync na serwerze docelowym ze źródła i ponowne ich przesłania na platformę Azure w przypadku korzystania z funkcji /DNS narzędzia Robocopy. Konieczne jest użycie programu Robocopy w systemie Windows Server innym niż 2019. Preferowanym wyborem jest system Windows Server 2016. Ta uwaga zostanie zaktualizowana, jeśli problem zostanie rozwiązany za pośrednictwem Windows Update.

> [!WARNING]
> Nie *można uruchomić narzędzia* RoboCopy, zanim serwer będzie w pełni mieć przestrzeń nazw dla pobranego udziału plików platformy Azure. Aby uzyskać więcej informacji, [zobacz Określanie, kiedy przestrzeń nazw została w pełni pobrana na serwer.](#determine-when-your-namespace-has-fully-synced-to-your-server)

 Należy skopiować tylko te pliki, które zostały zmienione po ostatnim zakończeniu zadania migracji, oraz pliki, które nie zostały wcześniej przeniesione za pośrednictwem tych zadań. Możesz rozwiązać ten problem, aby określić, dlaczego nie zostały one później przenieść na serwer po zakończeniu migracji. Aby uzyskać więcej informacji, zobacz [Azure File Sync rozwiązywanie problemów.](storage-sync-files-troubleshoot.md#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing)

Program RoboCopy ma kilka parametrów. W poniższym przykładzie przedstawiono gotowe polecenie i listę przyczyn wyboru tych parametrów.

```console
Robocopy /MT:16 /UNILOG:<file name> /TEE /NP /B /MIR /IT /COPYALL /DCOPY:DAT <SourcePath> <Dest.Path>
```

Tle:

:::row:::
   :::column span="1":::
      /MT
   :::column-end:::
   :::column span="1":::
      Umożliwia uruchamianie wielowątkowych za pomocą programu RoboCopy. Wartość domyślna to 8, a wartość maksymalna to 128.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /UNILOG:<file name>
   :::column-end:::
   :::column span="1":::
      Wyprowadza stan do pliku DZIENNIKA jako UNICODE (zastępuje istniejący dziennik).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /TEE
   :::column-end:::
   :::column span="1":::
      Dane wyjściowe w oknie konsoli. Używany w połączeniu z wyjściem do pliku dziennika.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /NP
   :::column-end:::
   :::column span="1":::
      Pomija rejestrowanie postępu, aby dziennik był czytelny.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /B
   :::column-end:::
   :::column span="1":::
      Uruchamia program RoboCopy w tym samym trybie, który będzie używać aplikacja kopii zapasowej. Umożliwia to programowi RoboCopy przenoszenie plików, do których bieżący użytkownik nie ma uprawnień.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /ZAJMIJ SIĘ TYM
   :::column-end:::
   :::column span="1":::
      Umożliwia programowi RoboCopy uwzględnianie różnic między źródłem (urządzeniem StorSimple) a docelowym (katalogiem systemu Windows Server).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /IT
   :::column-end:::
   :::column span="1":::
      Zapewnia zachowywanie wierności w niektórych scenariuszach dublowania.</br>Przykład: między dwoma elementami robocopy plik jest uruchamiany w przypadku zmiany listy ACL i aktualizacji atrybutu, na przykład jest również oznaczony jako *ukryty*. Bez /IT zmiany listy ACL mogą zostać pominięte przez robocopy i w związku z tym nie są przesyłane do lokalizacji docelowej.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPY:copyflag[s]
   :::column-end:::
   :::column span="1":::
      Wierność kopii pliku (wartość domyślna to /COPY:DAT), flagi kopiowania: D=Data, A=Attributes, T=znaczniki czasu, S=Security=NTFS ACL, O=informacje o właścicielu, U=aUditing informacji.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      / COPYALL
   :::column-end:::
   :::column span="1":::
      SKOPIUJ WSZYSTKIE informacje o pliku (równoważne /COPY:DATSOU).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /DCOPY:copyflag[s]
   :::column-end:::
   :::column span="1":::
      Wierność kopii katalogów (wartość domyślna to /DCOPY:DA), flagi kopiowania: D=Data, A=Attributes, T=sygnatury czasowe.
   :::column-end:::
:::row-end:::

Podczas konfigurowania lokalizacji źródłowej i docelowej polecenia RoboCopy upewnij się, że przeglądasz strukturę źródła i obiektu docelowego, aby upewnić się, że są one zgodne. Jeśli używasz funkcji mapowania katalogów zadania migracji, struktura katalogu głównego może być inna niż struktura woluminu StorSimple. W takim przypadku może być konieczne wiele zadań programu RoboCopy, po jednym dla każdego podkatalogu. Jeśli nie masz pewności, czy polecenie będzie działać zgodnie z oczekiwaniami, można użyć */L* parametru, który będzie symulować polecenie bez dokonywania żadnych zmian.

To polecenie robocopy używa /ONE, więc nie przeniesie plików, które są takie same (na przykład plików warstwowych). Jeśli jednak wystąpi nieprawidłowa ścieżka źródłowa i docelowa, /GES przeczyszcza również struktury katalogów w wystąpieniu systemu Windows Server lub w udziałach plików platformy Azure, które nie są obecne w ścieżce źródłowej usługi StorSimple. Muszą one dokładnie odpowiadać zadaniu narzędzia RoboCopy, aby osiągnąć zamierzony cel aktualizacji zmigrowanych zawartości przy użyciu najnowszych zmian wprowadzonych w trakcie migracji.

Zapoznaj się z plikiem dziennika programu RoboCopy, aby sprawdzić, czy pliki zostały pozostawione. Jeśli występują problemy, rozwiąż je i ponownie uruchomić polecenie RoboCopy. Nie wykonuj aprowizowania żadnych zasobów StorSimple przed naprawiniem zaległych problemów dotyczących plików lub folderów, które Cię to podobają.

Jeśli nie używasz usługi Azure File Sync do buforowania określonego udziału plików platformy Azure, ale zamiast tego zdecydujesz się na bezpośredni dostęp do udziału:

1. [Zainstaluj udział plików platformy Azure jako](storage-how-to-use-files-windows.md#mount-the-azure-file-share) dysk sieciowy na lokalnej maszynie z systemem Windows.
1. Wykonaj narzędzie RoboCopy między urządzeniem StorSimple i zainstalowanym udziałem plików platformy Azure. Jeśli pliki nie zostaną skopiowane, popraw ich nazwy po stronie storSimple, aby usunąć nieprawidłowe znaki. Następnie spróbuj ponownie wykonać operację RoboCopy. Polecenie RoboCopy wymienione wcześniej można uruchomić wielokrotnie bez powodowania niepotrzebnego odwoływania do urządzenia StorSimple.

### <a name="user-cut-over"></a>Przecięcie użytkowników

Jeśli używasz Azure File Sync, prawdopodobnie musisz utworzyć udziały SMB w tym wystąpieniu systemu Windows Server z włączoną obsługą usługi Azure File Sync, które pasują do udziałów na woluminach StorSimple. Możesz załadować ten krok z góry i zrobić to wcześniej, aby nie tracić czasu w tym miejscu. Jednak należy upewnić się, że przed tym punktem nikt nie ma dostępu do spowodować zmiany w wystąpieniu systemu Windows Server.

Jeśli masz wdrożenie DFS-N, możesz wskazać DFN-Namespaces do nowych lokalizacji folderów serwera. Jeśli nie masz wdrożenia DFS-N i urządzenie 8100 lub 8600 zostało frontonem lokalnie przy użyciu wystąpienia systemu Windows Server, możesz wyłączyć ten serwer z domeny. Następnie dołącz do domeny nowego Azure File Sync z włączoną obsługą systemu Windows Server. W trakcie tego procesu nadaj serwerowi taką samą nazwę serwera i nazwy udziałów jak stary serwer, aby przecięcie pozostało niewidoczne dla użytkowników, zasad grupy i skryptów.

Dowiedz się więcej o [systemie plików DFS-N.](/windows-server/storage/dfs-namespaces/dfs-overview)

## <a name="deprovision"></a>Coprowizcja

Podczas coprowizować zasób, utracisz dostęp do konfiguracji tego zasobu i jego danych. Nie można cofnąć cofnięcia aprowizowania. Nie kontynuuj, dopóki nie potwierdzisz, że:

* Migracja jest ukończona.
* Nie ma żadnych zależności od plików, folderów ani kopii zapasowych woluminów StorSimple, które mają być cofowane.

Przed rozpoczęciem warto przez jakiś czas obserwować nowe wdrożenie Azure File Sync w środowisku produkcyjnym. Ten czas daje możliwość rozwiązania wszelkich napotkanych problemów. Po zaobserwowaniu wdrożenia Azure File Sync przez co najmniej kilka dni można rozpocząć co najmniej kilka dni co najmniej jedną aprowizować zasoby w tej kolejności:

1. Coprowizować zasób StorSimple Data Manager za pośrednictwem Azure Portal. Wszystkie zadania USŁUGI DTS zostaną usunięte wraz z nim. Nie będzie można łatwo pobrać dzienników kopiowania. Jeśli są one ważne dla Twoich rekordów, pobierz je przed cofrokiem aprowizować.
1. Upewnij się, że urządzenia fizyczne StorSimple zostały zmigrowane, a następnie wyrejestruj je. Jeśli nie masz całkowitej pewności, że zostały one zmigrowane, nie kontynuuj. Jeśli coprowizujesz te zasoby, gdy są one nadal potrzebne, nie będzie można odzyskać danych ani ich konfiguracji.<br>Opcjonalnie możesz najpierw coprowizować zasób woluminu StorSimple, co spowoduje oczyszczenie danych na urządzeniu. Może to potrwać kilka dni **i nie będzie** forensywnie wyzerować danych na urządzeniu. Jeśli jest to ważne dla Ciebie, należy obsługiwać zerowanie dysku niezależnie od coprowizowania zasobów i zgodnie z zasadami.
1. Jeśli nie ma już zarejestrowanych urządzeń w urządzeniu StorSimple Menedżer urządzeń, możesz kontynuować usuwanie tego Menedżer urządzeń zasobów.
1. Teraz czas na usunięcie konta magazynu StorSimple na platformie Azure. Ponownie zatrzymaj i potwierdź, że migracja jest ukończona i że nic i nikt nie zależy od tych danych przed kontynuowaniem.
1. Odłącz urządzenie fizyczne StorSimple od centrum danych.
1. Jeśli jesteś właścicielem urządzenia StorSimple, możesz je odtworzyć na komputerze. Jeśli urządzenie zostanie wydzierżawione, poinformuj dzierżawcę i zwróć urządzenie zgodnie z potrzebami.

Migracja jest ukończona.

> [!NOTE]
> Nadal masz pytania lub napotkasz jakieś problemy?</br>
> Jesteśmy tutaj, aby pomóc na stronie AzureFilesMigration@microsoft.com .

## <a name="next-steps"></a>Następne kroki

* Zapoznaj się z [Azure File Sync: aka.ms/AFS](./storage-sync-files-planning.md).
* Dowiedz się, jak [elastyczne są zasady warstw w chmurze.](storage-sync-cloud-tiering-overview.md)
* [Włącz Azure Backup](../../backup/backup-afs.md#configure-backup-from-the-file-share-pane) udziałach plików platformy Azure, aby zaplanować migawki i zdefiniować harmonogramy przechowywania kopii zapasowych.
* Jeśli na stronie aplikacji Azure Portal że niektóre pliki nie są trwale [](storage-sync-files-troubleshoot.md) synchronizowane, zapoznaj się z przewodnikiem rozwiązywania problemów, aby uzyskać instrukcje rozwiązania tych problemów.
