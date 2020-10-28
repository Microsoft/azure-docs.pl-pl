---
title: Odzyskiwanie po awarii i tryb failover konta magazynu
titleSuffix: Azure Storage
description: Usługa Azure Storage obsługuje tryb failover dla konta magazynu geograficznie nadmiarowego. Korzystając z trybu failover konta, można zainicjować proces trybu failover dla konta magazynu, jeśli podstawowy punkt końcowy stanie się niedostępny.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 05/05/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: f7d7bff1bc85e0dec78a69422d126b86f61b7704
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92783984"
---
# <a name="disaster-recovery-and-storage-account-failover"></a>Odzyskiwanie po awarii i tryb failover konta magazynu

Firma Microsoft dąży do zapewnienia, że usługi platformy Azure są zawsze dostępne. Może jednak wystąpić nieplanowana awaria usługi. Jeśli aplikacja wymaga odporności, firma Microsoft zaleca korzystanie z magazynu geograficznie nadmiarowego, dzięki czemu dane są kopiowane do drugiego regionu. Ponadto klienci powinni mieć plan odzyskiwania po awarii na potrzeby obsługi regionalnej awarii usługi. Ważna część planu odzyskiwania po awarii jest przygotowywana do przełączenia w tryb failover do pomocniczego punktu końcowego w przypadku, gdy podstawowy punkt końcowy stał się niedostępny.

Usługa Azure Storage obsługuje tryb failover dla konta magazynu geograficznie nadmiarowego. Korzystając z trybu failover konta, można zainicjować proces trybu failover dla konta magazynu, jeśli podstawowy punkt końcowy stanie się niedostępny. Tryb failover aktualizuje pomocniczy punkt końcowy, aby stał się podstawowym punktem końcowym dla konta magazynu. Po zakończeniu pracy w trybie failover klienci mogą rozpocząć zapisywanie do nowego podstawowego punktu końcowego.

Tryb failover konta jest dostępny dla typów kont ogólnego przeznaczenia w wersji 1, w wersji 2 i dla usługi BLOB Storage z wdrożeniami Azure Resource Manager. Tryb failover konta jest obsługiwany przez wszystkie regiony publiczne, ale nie jest w tej chwili dostępna w chmurach suwerennych lub krajowych.

W tym artykule opisano koncepcje i procesy związane z trybem failover konta oraz omówiono sposób przygotowania konta magazynu do odzyskania przy minimalnym wpływie na klienta. Aby dowiedzieć się, jak zainicjować tryb failover konta w Azure Portal lub PowerShell, zobacz [inicjowanie trybu failover konta](storage-initiate-account-failover.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="choose-the-right-redundancy-option"></a>Wybierz opcję z odpowiednią nadmiarowością

Usługa Azure Storage obsługuje wiele kopii konta magazynu w celu zapewnienia trwałości i wysokiej dostępności. Wybrana opcja nadmiarowości dla konta zależy od stopnia odporności. Aby chronić przed awarią regionalną, skonfiguruj konto dla magazynu geograficznie nadmiarowego, z opcją dostępu do odczytu z regionu pomocniczego lub bez niej:  

**Magazyn Geograficznie nadmiarowy (GRS) lub strefa Geograficznie nadmiarowy (GZRS)** kopiuje dane asynchronicznie w dwóch regionach geograficznych, które znajdują się w odległości co najmniej setki kilometrów. Jeśli region podstawowy cierpi na awarię, region pomocniczy służy jako nadmiarowe źródło danych. Możesz zainicjować tryb failover, aby przekształcić pomocniczy punkt końcowy w podstawowy punkt końcowy.

**Magazyn Geograficznie nadmiarowy dostępny do odczytu (RA-GRS) lub strefa geograficzna z dostępem do odczytu — nadmiarowy magazyn (Ra-GZRS)** zapewnia Geograficznie nadmiarowy magazyn z dodatkową korzyścią dostępu do odczytu do pomocniczego punktu końcowego. Jeśli wystąpi awaria w podstawowym punkcie końcowym, aplikacje skonfigurowane na potrzeby dostępu do odczytu do pomocniczego i zaprojektowane na potrzeby wysokiej dostępności mogą nadal czytać z pomocniczego punktu końcowego. Firma Microsoft zaleca usługi RA-GZRS, aby uzyskać maksymalną dostępność i trwałość aplikacji.

Aby uzyskać więcej informacji o nadmiarowości w usłudze Azure Storage, zobacz [nadmiarowość usługi Azure Storage](storage-redundancy.md).

> [!WARNING]
> Magazyn Geograficznie nadmiarowy wykonuje ryzyko utraty danych. Dane są kopiowane do regionu pomocniczego asynchronicznie, co oznacza, że istnieje opóźnienie między zapisaniem danych w regionie podstawowym do regionu pomocniczego. W przypadku awarii operacje zapisu w podstawowym punkcie końcowym, które nie zostały jeszcze skopiowane do pomocniczego punktu końcowego, zostaną utracone.

## <a name="design-for-high-availability"></a>Projektowanie na potrzeby wysokiej dostępności

Ważne jest, aby zaprojektować aplikację pod kątem wysokiej dostępności od początku. Zapoznaj się z tymi zasobami platformy Azure, aby uzyskać wskazówki dotyczące projektowania aplikacji i planowania odzyskiwania po awarii:

- [Projektowanie odpornych aplikacji na platformę Azure](/azure/architecture/framework/resiliency/app-design): przegląd najważniejszych pojęć dotyczących tworzenia aplikacji o wysokiej dostępności na platformie Azure.
- [Lista kontrolna odporności](/azure/architecture/checklist/resiliency-per-service): Lista kontrolna służąca do sprawdzania, czy aplikacja implementuje najlepsze rozwiązania w zakresie projektowania wysokiej dostępności.
- [Używaj nadmiarowości geograficznej do projektowania aplikacji o wysokiej](geo-redundant-design.md)dostępności: wskazówki dotyczące projektowania do tworzenia aplikacji, aby korzystać z magazynu geograficznie nadmiarowego.
- [Samouczek: Tworzenie aplikacji o wysokiej dostępności przy użyciu magazynu obiektów BLOB](../blobs/storage-create-geo-redundant-storage.md): samouczek pokazujący sposób tworzenia aplikacji o wysokiej dostępności, która automatycznie przełącza się między punktami końcowymi w miarę awarii i odzyskiwania. 

Ponadto należy pamiętać o najlepszych rozwiązaniach dotyczących utrzymania wysokiej dostępności dla danych usługi Azure Storage:

- **Dyski:** Użyj [Azure Backup](https://azure.microsoft.com/services/backup/) , aby utworzyć kopię zapasową dysków maszyny wirtualnej używanych przez maszyny wirtualne platformy Azure. Należy również rozważyć użycie [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) do ochrony maszyn wirtualnych w przypadku awarii regionalnej.
- **Blokowe obiekty blob:** Włącz opcję [usuwania nietrwałego](../blobs/soft-delete-blob-overview.md) , aby chronić przed usuwaniem na poziomie obiektów i zastępowaniem, lub kopiować blokowe obiekty blob na inne konto magazynu w innym regionie przy użyciu [AzCopy](./storage-use-azcopy-v10.md), [Azure PowerShell](/powershell/module/az.storage/)lub [biblioteki przenoszenia danych platformy Azure](storage-use-data-movement-library.md).
- **Pliki:** Użyj [AzCopy](./storage-use-azcopy-v10.md) lub [Azure PowerShell](/powershell/module/az.storage/) , aby skopiować pliki do innego konta magazynu w innym regionie.
- **Tabele:** Użyj [AzCopy](./storage-use-azcopy-v10.md) , aby wyeksportować dane tabeli do innego konta magazynu w innym regionie.

## <a name="track-outages"></a>Śledź przerwy

Klienci mogą subskrybować [pulpit nawigacyjny Azure Service Health](https://azure.microsoft.com/status/) , aby śledzić kondycję i stan usługi Azure Storage i innych usług platformy Azure.

Firma Microsoft zaleca również zaprojektowanie aplikacji w celu przygotowania się do wystąpienia błędów zapisu. Aplikacja powinna ujawniać błędy zapisu w taki sposób, aby ostrzegał o możliwości przestoju w regionie podstawowym.

## <a name="understand-the-account-failover-process"></a>Opis procesu przełączania do trybu failover dla konta

Tryb failover konta zarządzanego przez klienta umożliwia przełączenie całego konta magazynu do regionu pomocniczego, jeśli podstawowy serwer stał się niedostępny z jakiegokolwiek powodu. Po wymuszeniu przejścia w tryb failover do regionu pomocniczego klienci mogą rozpocząć zapisywanie danych do pomocniczego punktu końcowego po zakończeniu pracy w trybie failover. Przełączenie w tryb failover trwa zwykle o godzinę.

### <a name="how-an-account-failover-works"></a>Na czym polega przełączanie konta w tryb failover

W normalnych warunkach klient zapisuje dane na koncie usługi Azure Storage w regionie podstawowym, a dane są kopiowane asynchronicznie do regionu pomocniczego. Na poniższej ilustracji przedstawiono scenariusz, w którym jest dostępny region podstawowy:

![Klienci zapisują dane na koncie magazynu w regionie podstawowym](media/storage-disaster-recovery-guidance/primary-available.png)

Jeśli podstawowy punkt końcowy jest niedostępny z jakiegoś powodu, klient nie będzie już mógł zapisywać na koncie magazynu. Na poniższej ilustracji przedstawiono scenariusz, w którym podstawowy stał się niedostępny, ale nie wystąpiło jeszcze odzyskiwanie:

![Podstawowa jest niedostępna, dlatego klienci nie mogą zapisywać danych](media/storage-disaster-recovery-guidance/primary-unavailable-before-failover.png)

Klient inicjuje przejście w tryb failover konta do pomocniczego punktu końcowego. Proces trybu failover aktualizuje wpis DNS udostępniony przez usługę Azure Storage, aby pomocniczy punkt końcowy stał się nowym podstawowym punktem końcowym dla konta magazynu, jak pokazano na poniższej ilustracji:

![Klient inicjuje przejście w tryb failover konta do pomocniczego punktu końcowego](media/storage-disaster-recovery-guidance/failover-to-secondary.png)

Dostęp do zapisu jest przywracany dla kont geograficznie nadmiarowych po aktualizacji wpisu DNS, a żądania są przekierowywane do nowego podstawowego punktu końcowego. Istniejące punkty końcowe usługi magazynu dla obiektów blob, tabel, kolejek i plików pozostają takie same po przejściu w tryb failover.

> [!IMPORTANT]
> Po zakończeniu pracy w trybie failover konto magazynu zostanie skonfigurowane jako lokalnie nadmiarowy w nowym podstawowym punkcie końcowym. Aby wznowić replikację do nowej dodatkowej, należy ponownie skonfigurować konto dla nadmiarowości geograficznej.
>
> Należy pamiętać, że przekonwertowanie konta LRS na korzystanie z nadmiarowości geograficznej wiąże się z kosztem. Ten koszt dotyczy aktualizacji konta magazynu w nowym regionie podstawowym po przejściu w tryb failover.  

### <a name="anticipate-data-loss"></a>Oczekiwanie na utratę danych

> [!CAUTION]
> Przełączenie w tryb failover jest zazwyczaj związane z utratą danych. Ważne jest, aby zrozumieć implikacje inicjowania trybu failover konta.  

Ponieważ dane są zapisywane asynchronicznie z regionu podstawowego do regionu pomocniczego, zawsze jest opóźnienie, zanim zapis do regionu podstawowego zostanie skopiowany do regionu pomocniczego. Jeśli region podstawowy przestanie być dostępny, najnowsze zapisy mogły jeszcze nie zostać skopiowane do regionu pomocniczego.

Po wymuszeniu przejścia w tryb failover wszystkie dane w regionie podstawowym zostaną utracone, ponieważ region pomocniczy stał się nowym regionem podstawowym, a konto magazynu jest skonfigurowane do lokalnego nadmiarowego. Wszystkie dane, które zostały już skopiowane do pomocniczej, są utrzymywane w przypadku przejścia w tryb failover. Jednak wszystkie dane zapisywane w podstawowym, które nie zostały również skopiowane do pomocniczej, zostaną trwale utracone.

Właściwość **czas ostatniej synchronizacji** wskazuje, że ostatni czas, w którym dane z regionu podstawowego ma zostać zapisany w regionie pomocniczym. Wszystkie dane zapisywane przed upływem czasu ostatniej synchronizacji są dostępne na serwerze pomocniczym, a dane zapisywane po ostatniej synchronizacji mogą nie zostać zapisaną na pomocniczą i mogą zostać utracone. Użyj tej właściwości w przypadku przestoju, aby oszacować ilość utraconych danych, które mogą zostać naliczone przez zainicjowanie konta w trybie failover.

Najlepszym rozwiązaniem jest zaprojektowanie aplikacji tak, aby można było użyć czasu ostatniej synchronizacji do oszacowania oczekiwanej utraty danych. Na przykład, Jeśli rejestrujesz wszystkie operacje zapisu, można porównać czas ostatniej operacji zapisu w czasie ostatniej synchronizacji, aby określić, które zapisy nie zostały zsynchronizowane z serwerem pomocniczym.

Aby uzyskać więcej informacji na temat sprawdzania właściwości **czas ostatniej synchronizacji** , zobacz [Sprawdzanie właściwości ostatniej synchronizacji dla konta magazynu](last-sync-time-get.md).

### <a name="use-caution-when-failing-back-to-the-original-primary"></a>Należy zachować ostrożność w przypadku powrotu po awarii do oryginalnego podstawowego

Po przełączeniu w tryb failover z poziomu podstawowego do regionu pomocniczego konto magazynu zostanie skonfigurowane jako lokalnie nadmiarowy w nowym regionie podstawowym. Następnie można ponownie skonfigurować konto pod kątem nadmiarowości geograficznej. Gdy konto zostanie ponownie skonfigurowane pod kątem nadmiarowości geograficznej po przejściu w tryb failover, nowy region podstawowy natychmiast rozpocznie kopiowanie danych do nowego regionu pomocniczego, który był podstawą przed pierwotnym trybem failover. Jednak może upłynąć trochę czasu, zanim istniejące dane w podstawowym są w całości kopiowane do nowej pomocniczej.

Po ponownym skonfigurowaniu konta magazynu pod kątem nadmiarowości geograficznej możliwe jest zainicjowanie kolejnej pracy w trybie failover z powrotem do nowego elementu pomocniczego. W takim przypadku pierwotny region podstawowy przed przełączeniem w tryb failover będzie ponownie regionem podstawowym i zostanie skonfigurowany do lokalnego nadmiarowego. Wszystkie dane w regionie podstawowym w trybie failover (oryginalna wersja pomocnicza) są następnie tracone. Jeśli większość danych na koncie magazynu nie została skopiowana do nowego elementu pomocniczego przed powrotem po awarii, możesz ponieść znaczną utratę danych.

Aby uniknąć poważnej utraty danych, należy sprawdzić wartość właściwości **czas ostatniej synchronizacji** przed powrotem po awarii. Porównaj czas ostatniej synchronizacji z ostatnim czasem zapisania danych w nowym podstawowym celu oszacowania oczekiwanej utraty danych. 

## <a name="initiate-an-account-failover"></a>Inicjowanie trybu failover konta

Konto można zainicjować w trybie failover z poziomu Azure Portal, programu PowerShell, interfejsu wiersza polecenia platformy Azure lub interfejsu API dostawcy zasobów usługi Azure Storage. Aby uzyskać więcej informacji na temat inicjowania trybu failover, zobacz [Inicjowanie konta trybu failover](storage-initiate-account-failover.md).

## <a name="additional-considerations"></a>Dodatkowe zagadnienia

Zapoznaj się z dodatkowymi zagadnieniami opisanymi w tej sekcji, aby zrozumieć, w jaki sposób aplikacje i usługi mogą mieć wpływ po wymuszeniu przejścia w tryb failover.

### <a name="storage-account-containing-archived-blobs"></a>Konto magazynu zawierające zarchiwizowane obiekty blob

Konta magazynu zawierające zarchiwizowane obiekty blob obsługują tryb failover konta. Po zakończeniu pracy w trybie failover wszystkie zarchiwizowane obiekty blob muszą zostać odwodne do warstwy online, aby można było skonfigurować konto pod kątem nadmiarowości geograficznej.

### <a name="storage-resource-provider"></a>Dostawca zasobów magazynu

Po zakończeniu pracy w trybie failover klienci mogą ponownie odczytywać i zapisywać dane usługi Azure Storage w nowym regionie podstawowym. Jednak dostawca zasobów usługi Azure Storage nie przełączy się w tryb failover, więc operacje zarządzania zasobami muszą być nadal wykonywane w regionie podstawowym. Jeśli region podstawowy jest niedostępny, nie będzie możliwe wykonywanie operacji zarządzania na koncie magazynu.

Ponieważ dostawca zasobów usługi Azure Storage nie przejdzie w tryb failover, właściwość [Location](/dotnet/api/microsoft.azure.management.storage.models.trackedresource.location) zwróci oryginalną lokalizację główną po zakończeniu pracy awaryjnej.

### <a name="azure-virtual-machines"></a>Maszyny wirtualne platformy Azure

Usługi Azure Virtual Machines (VM) nie są przełączane w tryb failover w ramach pracy awaryjnej konta. Jeśli region podstawowy stał się niedostępny i przejdziesz w tryb failover do regionu pomocniczego, należy ponownie utworzyć wszystkie maszyny wirtualne po przejściu w tryb pracy awaryjnej. Ponadto istnieje potencjalna utrata danych skojarzona z trybem failover konta. Firma Microsoft zaleca następujące wskazówki dotyczące [wysokiej dostępności](../../virtual-machines/manage-availability.md) i [odzyskiwania po awarii](../../virtual-machines/backup-recovery.md) dotyczące maszyn wirtualnych na platformie Azure.

### <a name="azure-unmanaged-disks"></a>Dyski niezarządzane platformy Azure

Najlepszym rozwiązaniem jest to, że firma Microsoft zaleca konwersję dysków niezarządzanych na dyski zarządzane. Jeśli jednak zachodzi potrzeba przełączenia konta zawierającego dyski niezarządzane dołączone do maszyn wirtualnych platformy Azure, należy zamknąć maszynę wirtualną przed zainicjowaniem trybu failover.

Dyski niezarządzane są przechowywane jako stronicowe obiekty blob w usłudze Azure Storage. Gdy maszyna wirtualna jest uruchomiona na platformie Azure, wszystkie dyski niezarządzane dołączone do maszyny wirtualnej są dzierżawione. Nie można przeprowadzić przejścia w tryb failover dla konta, gdy istnieje dzierżawa w obiekcie blob. Aby przeprowadzić przejście w tryb failover, wykonaj następujące kroki:

1. Przed rozpoczęciem należy zwrócić uwagę na nazwy wszystkich dysków niezarządzanych, ich numerów jednostek logicznych (LUN) oraz maszynę wirtualną, do której są dołączone. Wykonanie tej czynności ułatwi ponowne dołączenie dysków po przejściu do trybu failover.
2. Zamknij maszynę wirtualną.
3. Usuń maszynę wirtualną, ale Zachowaj pliki VHD dla dysków niezarządzanych. Zanotuj godzinę usunięcia maszyny wirtualnej.
4. Poczekaj na zaktualizowanie **czasu ostatniej synchronizacji** i jest późniejsza niż godzina usunięcia maszyny wirtualnej. Ten krok jest ważny, ponieważ jeśli pomocniczy punkt końcowy nie został w pełni zaktualizowany przy użyciu plików VHD w przypadku przełączenia w tryb failover, maszyna wirtualna może nie działać poprawnie w nowym regionie podstawowym.
5. Zainicjuj tryb failover konta.
6. Poczekaj na zakończenie pracy w trybie failover konta i region pomocniczy staje się nowym regionem podstawowym.
7. Utwórz maszynę wirtualną w nowym regionie podstawowym i ponownie podłącz wirtualne dyski twarde.
8. Uruchom nową maszynę wirtualną.

Należy pamiętać, że wszystkie dane przechowywane na dysku tymczasowym zostaną utracone podczas zamykania maszyny wirtualnej.

## <a name="unsupported-features-and-services"></a>Nieobsługiwane funkcje i usługi

Następujące funkcje i usługi nie są obsługiwane w przypadku trybu failover dla konta:

- Azure File Sync nie obsługuje trybu failover dla konta magazynu. Kont magazynu zawierających udziały plików platformy Azure używane jako punkty końcowe w chmurze w usłudze Azure File Sync nie należy przełączać w tryb failover. Wykonanie tej operacji spowoduje, że synchronizacja przestanie działać, a także może spowodować nieoczekiwaną utratę danych w przypadku nowych plików warstwowych.
- ADLS Gen2 konta magazynu (konta z włączoną hierarchiczną przestrzenią nazw) nie są w tej chwili obsługiwane.
- Nie można przełączyć konta magazynu zawierającego blokowe obiekty blob w warstwie Premium. Konta magazynu obsługujące blokowe obiekty blob w warstwie Premium nie obsługują obecnie nadmiarowości geograficznej.
- Nie można przełączyć do trybu failover konta magazynu zawierającego wszystkie kontenery z włączonymi [zasadami niezmiennościymi](../blobs/storage-blob-immutable-storage.md) . Odblokowanie/blokowanie oparte na czasie lub zasady wstrzymania w trybie failover w celu zachowania zgodności.

## <a name="copying-data-as-an-alternative-to-failover"></a>Kopiowanie danych jako alternatywa dla przechodzenia w tryb failover

Jeśli konto magazynu jest skonfigurowane pod kątem dostępu do odczytu do elementu pomocniczego, można zaprojektować aplikację do odczytu z pomocniczego punktu końcowego. Jeśli wolisz przejść do trybu failover w przypadku awarii w regionie podstawowym, możesz użyć narzędzi, takich jak [AzCopy](./storage-use-azcopy-v10.md), [Azure PowerShell](/powershell/module/az.storage/)lub [Biblioteka przenoszenia danych platformy Azure](../common/storage-use-data-movement-library.md) , aby skopiować dane z konta magazynu w regionie pomocniczym do innego konta magazynu w nienaruszonym regionie. Następnie możesz wskazać swoje aplikacje na tym koncie magazynu, aby uzyskać dostęp do odczytu i zapisu.

> [!CAUTION]
> Nie należy używać konta w trybie failover w ramach strategii migracji danych.

## <a name="microsoft-managed-failover"></a>Tryb failover zarządzany przez firmę Microsoft

W skrajnych okolicznościach, gdy region zostanie utracony ze względu na znaczną awarię, firma Microsoft może zainicjować regionalną pracę w trybie failover. W takim przypadku nie jest wymagana żadna akcja z Twojej strony. Do momentu ukończenia pracy w trybie failover zarządzanej przez firmę Microsoft nie będziesz mieć dostępu do zapisu na koncie magazynu. Aplikacje mogą odczytywać z regionu pomocniczego, jeśli konto magazynu jest skonfigurowane dla usługi RA-GRS lub RA-GZRS.

## <a name="see-also"></a>Zobacz także

- [Projektowanie aplikacji o wysokiej dostępności przy użyciu nadmiarowości geograficznej](geo-redundant-design.md)
- [Inicjowanie trybu failover konta](storage-initiate-account-failover.md)
- [Sprawdź Właściwość godzina ostatniej synchronizacji dla konta magazynu](last-sync-time-get.md)
- [Samouczek: Tworzenie aplikacji o wysokiej dostępności przy użyciu magazynu obiektów BLOB](../blobs/storage-create-geo-redundant-storage.md)