---
title: Migracja lokalnego serwera NAS do Azure File Sync
description: Dowiedz się, jak migrować pliki z lokalizacji magazynu w sieci lokalnej (NAS) do wdrożenia w chmurze hybrydowej z Azure File Sync i udziałami plików platformy Azure.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 03/19/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 86e79302716fa502d8562dd563b0a5c5fb220a67
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102547559"
---
# <a name="migrate-from-network-attached-storage-nas-to-a-hybrid-cloud-deployment-with-azure-file-sync"></a>Migrowanie z magazynu dołączanego do sieci (NAS) do wdrożenia chmury hybrydowej za pomocą Azure File Sync

Ten artykuł migracji jest jednym z kilku obejmujących słowa kluczowe NAS i Azure File Sync. Sprawdź, czy ten artykuł dotyczy Twojego scenariusza:

> [!div class="checklist"]
> * Źródło danych: magazyn dołączony do sieci (NAS)
> * Trasa migracji: &rArr; przekazywanie i synchronizowanie serwera nas systemu Windows Server &rArr; za pomocą udziałów plików platformy Azure
> * Buforowanie plików lokalnie: tak, ostateczny cel to wdrożenie Azure File Sync.

Jeśli Twój scenariusz jest inny, przejrzyj [tabelę przewodników migracji](storage-files-migration-overview.md#migration-guides).

Azure File Sync działa w odniesieniu do lokalizacji magazynu bezpośrednio dołączonego (DAS) i nie obsługuje synchronizacji z lokalizacjami magazynu sieciowego (NAS).
Ten fakt powoduje, że migracja plików jest konieczna, a w tym artykule opisano planowanie i wykonywanie takich migracji.

## <a name="migration-goals"></a>Cele migracji

Celem jest przeniesienie udziałów znajdujących się na urządzeniu NAS do systemu Windows Server. Następnie użyj Azure File Sync do wdrożenia chmury hybrydowej. Na ogół migracje muszą być wykonywane w sposób, który Guaranty integralność danych produkcyjnych i jest dostępny podczas migracji. Druga z nich wymaga utrzymywania przestoju w minimalnym stopniu, dzięki czemu może być dłuższa niż w zwykłych oknach obsługi lub tylko nieco.

## <a name="migration-overview"></a>Omówienie migracji

Jak wspomniano w [artykule Omówienie migracji](storage-files-migration-overview.md)Azure Files przy użyciu prawidłowego narzędzia do kopiowania i podejścia jest ważne. Urządzenie NAS udostępnia udziały SMB bezpośrednio w sieci lokalnej. RoboCopy, wbudowany system Windows Server, jest najlepszym sposobem przenoszenia plików w tym scenariuszu migracji.

- Faza 1: [określenie, ile potrzebnych udziałów plików platformy Azure](#phase-1-identify-how-many-azure-file-shares-you-need)
- Faza 2. [udostępnianie odpowiedniego lokalnego systemu Windows Server](#phase-2-provision-a-suitable-windows-server-on-premises)
- Faza 3: [wdrażanie zasobu chmury Azure File Sync](#phase-3-deploy-the-azure-file-sync-cloud-resource)
- Faza 4. [wdrażanie zasobów usługi Azure Storage](#phase-4-deploy-azure-storage-resources)
- Faza 5. [wdrażanie agenta Azure File Sync](#phase-5-deploy-the-azure-file-sync-agent)
- Faza 6: [konfigurowanie Azure File Sync w systemie Windows Server](#phase-6-configure-azure-file-sync-on-the-windows-server)
- Faza 7: [Robocopy](#phase-7-robocopy)
- Faza 8: [wycinanie użytkownika](#phase-8-user-cut-over)

## <a name="phase-1-identify-how-many-azure-file-shares-you-need"></a>Faza 1: określenie, ile potrzebnych udziałów plików platformy Azure

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## <a name="phase-2-provision-a-suitable-windows-server-on-premises"></a>Faza 2. Udostępnianie odpowiedniego lokalnego systemu Windows Server

* Utwórz system Windows Server 2019 — co najmniej 2012R2 maszyny wirtualnej lub serwera fizycznego. Obsługiwany jest również klaster trybu failover systemu Windows Server.
* Udostępnianie lub Dodawanie bezpośredniego dołączonego magazynu (DAS w porównaniu z serwerem NAS, co nie jest obsługiwane).

    Ilość dostępnego miejsca w magazynie może być mniejsza niż obecnie używane na urządzeniu NAS. Ta konfiguracja wymaga również użycia funkcji obsługi [warstw w chmurze](storage-sync-cloud-tiering-overview.md) usługi Azure File Sync.
    Jednak podczas kopiowania plików z większej ilości miejsca do usługi NAS do mniejszego woluminu systemu Windows Server w późniejszej fazie należy wykonać czynności w partiach:

    1. Przenoszenie zestawu plików, który pasuje do dysku
    2. Zezwalaj na synchronizację plików i obsługę warstw w chmurze
    3. Po utworzeniu większej ilości wolnego miejsca na woluminie przejdź do następnej partii plików. Alternatywnie można przejrzeć polecenie RoboCopy w nadchodzącej [sekcji Robocopy](#phase-7-robocopy) , aby użyć nowego `/LFSM` przełącznika. Korzystanie `/LFSM` z programu może znacznie uprościć zadania Robocopy, ale nie jest zgodne z innymi przełącznikami Robocopy, które mogą być od siebie zależne.
    
    Takie podejście wsadowe można uniknąć, udostępniając odpowiednie miejsce na serwerze z systemem Windows, które zajmują pliki na urządzeniu NAS. Należy rozważyć deduplikację na serwerze NAS/Windows. Jeśli nie chcesz trwale zatwierdzić tej dużej ilości miejsca do magazynowania w systemie Windows Server, możesz zmniejszyć rozmiar woluminu po migracji i zmienić zasady dotyczące warstw w chmurze. Powoduje to utworzenie mniejszej lokalnej pamięci podręcznej udziałów plików platformy Azure.

Konfiguracja zasobów (obliczeniowa i pamięć RAM) wdrażanego systemu Windows zależy przede wszystkim od liczby elementów (plików i folderów), które zostaną zsynchronizowane. W przypadku jakichkolwiek problemów zalecamy przeprowadzenie wyższej konfiguracji wydajności.

[Dowiedz się, jak zmienić rozmiar systemu Windows Server na podstawie liczby elementów (plików i folderów), które mają być synchronizowane.](storage-sync-files-planning.md#recommended-system-resources)

> [!NOTE]
> W wcześniej połączonym artykule przedstawiono tabelę z zakresem dla pamięci serwera (RAM). Można przyróżnić do mniejszej liczby serwerów, ale oczekiwać, że synchronizacja początkowa może trwać znacznie więcej czasu.

## <a name="phase-3-deploy-the-azure-file-sync-cloud-resource"></a>Faza 3: wdrażanie zasobu chmury Azure File Sync

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

## <a name="phase-4-deploy-azure-storage-resources"></a>Faza 4. wdrażanie zasobów usługi Azure Storage

W tej fazie zapoznaj się z tabelą mapowania z fazy 1 i użyj jej do aprowizacji poprawnej liczby kont usługi Azure Storage i udziałów plików w nich.

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

## <a name="phase-5-deploy-the-azure-file-sync-agent"></a>Faza 5. wdrażanie agenta Azure File Sync

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

## <a name="phase-6-configure-azure-file-sync-on-the-windows-server"></a>Faza 6: Konfigurowanie Azure File Sync w systemie Windows Server

Zarejestrowany lokalny serwer systemu Windows musi być gotowy i połączony z Internetem w ramach tego procesu.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!IMPORTANT]
> Obsługa warstw w chmurze to funkcja AFS, która umożliwia serwerowi lokalnemu przechowywanie mniejszej pojemności niż w chmurze, ale pełna przestrzeń nazw jest dostępna. Lokalnie interesujące dane są również buforowane lokalnie w celu zapewnienia wydajności szybkiego dostępu. Obsługa warstw w chmurze jest opcjonalną funkcją dla Azure File Sync "punkt końcowy serwera".

> [!WARNING]
> W przypadku zainicjowania obsługi mniejszej ilości miejsca na woluminach z systemem Windows Server niż dane używane na urządzeniu NAS, warstwy chmur są obowiązkowe. Jeśli nie włączysz warstw w chmurze, serwer nie zwolni miejsca do przechowywania wszystkich plików. Ustaw zasady dotyczące warstw tymczasowo dla migracji na 99% wolnego miejsca na woluminie. Upewnij się, że po zakończeniu migracji nastąpi powrót do ustawień obsługi warstw w chmurze, i ustaw ją na bardziej długoterminowy poziom użyteczności.

Powtórz kroki tworzenia grupy synchronizacji i Dodawanie pasującego folderu serwera jako punktu końcowego serwera dla wszystkich udziałów plików platformy Azure/lokalizacji serwera, które muszą zostać skonfigurowane do synchronizacji.

Po utworzeniu wszystkich punktów końcowych serwera synchronizacja działa. Można utworzyć plik testowy i zobaczyć jego synchronizację z lokalizacji serwera do połączonego udziału plików platformy Azure (zgodnie z opisem w punkcie końcowym w chmurze w grupie synchronizacji).

Obie lokalizacje, foldery na serwerze i udziały plików platformy Azure, są w inny sposób puste i oczekują na dane w jednej lokalizacji. W następnym kroku rozpocznie się kopiowanie plików do systemu Windows Server w celu Azure File Sync, aby przenieść je do chmury. W przypadku włączenia obsługi warstw w chmurze, serwer będzie rozpoczynał się do warstw plików, w przypadku gdy woluminy lokalne nie są uruchamiane.

## <a name="phase-7-robocopy"></a>Faza 7: RoboCopy

Podstawowe podejście migracji to RoboCopy z urządzenia NAS do systemu Windows Server, a Azure File Sync do udziałów plików platformy Azure.

Uruchom pierwszą kopię lokalną w folderze docelowym systemu Windows Server:

* Zidentyfikuj pierwszą lokalizację na urządzeniu NAS.
* Zidentyfikuj pasujący folder na serwerze z systemem Windows, dla którego skonfigurowano już Azure File Sync.
* Rozpocznij kopiowanie przy użyciu RoboCopy

Następujące polecenie RoboCopy skopiuje pliki z magazynu NAS do folderu docelowego systemu Windows Server. System Windows Server zsynchronizuje go z udziałami plików platformy Azure. 

W przypadku zainicjowania obsługi mniejszej ilości miejsca w systemie Windows Server niż pliki na urządzeniu NAS, skonfigurowano obsługę warstw w chmurze. W miarę jak wolumin lokalnego systemu Windows Server jest pełny, obsługa [warstw w chmurze](storage-sync-cloud-tiering-overview.md) zostanie rozpoczęta i pliki warstw, które zostały już pomyślnie zsynchronizowane. Obsługa warstw w chmurze spowoduje wygenerowanie wystarczającej ilości miejsca, aby kontynuować kopiowanie z urządzenia NAS. Obsługa warstw w chmurze jest sprawdzana raz na godzinę, aby zobaczyć, co zostało zsynchronizowane, i zwolnić miejsce na dysku, aby uzyskać dostęp do 99% wolnego miejsca na woluminie.
Jest możliwe, że RoboCopy przenosi pliki szybciej niż można zsynchronizować lokalnie z chmurą i warstwą, w rezultacie kończy się miejsce na dysku lokalnym. RoboCopy zakończy się niepowodzeniem. Zalecane jest, aby wykonać czynności wykonywane przez udziały w sekwencji, która uniemożliwia to. Na przykład nie uruchamiaj zadań RoboCopy dla wszystkich udziałów w tym samym czasie lub przenosi tylko udziały pasujące do bieżącej ilości wolnego miejsca w systemie Windows Server, aby wymienić kilka.

[!INCLUDE [storage-files-migration-robocopy](../../../includes/storage-files-migration-robocopy.md)]

## <a name="phase-8-user-cut-over"></a>Faza 8: wycinanie użytkownika

Po pierwszym uruchomieniu polecenia RoboCopy Użytkownicy i aplikacje nadal uzyskują dostęp do plików na serwerze NAS i mogą je zmienić. Istnieje możliwość, że RoboCopy przetworzył katalog, przechodzi do następnego, a następnie użytkownik w lokalizacji źródłowej (NAS) dodaje, zmienia lub usuwa plik, który nie zostanie przetworzony w bieżącym przebiegu RoboCopy. Takie zachowanie jest oczekiwane.

Pierwszy przebieg polega na przenoszeniu ilości danych do systemu Windows Server i w chmurze za pośrednictwem Azure File Sync. Ta pierwsza kopia może zająć dużo czasu, w zależności od:

* przepustowość pobierania
* przepustowość przekazywania
* szybkość sieci lokalnej i liczba optymalnie dopasowanej liczby wątków RoboCopy
* Liczba elementów (plików i folderów), które muszą być przetwarzane przez RoboCopy i Azure File Sync

Po zakończeniu pierwszego uruchomienia Uruchom polecenie ponownie.

Drugi raz, który zakończy się szybciej, ponieważ wymaga jedynie transportowania zmian, które wystąpiły od momentu ostatniego uruchomienia. W trakcie tego drugiego uruchomienia nadal można zbierać nowe zmiany.

Powtarzaj ten proces do momentu, gdy okaże się, że czas potrzebny na zakończenie RoboCopy dla określonej lokalizacji znajduje się w akceptowalnym oknie do przestoju.

Po rozważeniu niedopuszczalnego przestoju należy usunąć dostęp użytkowników do udziałów opartych na protokole NAS. Można to zrobić, wykonując czynności, które uniemożliwiają użytkownikom zmianę struktury plików i folderów oraz zawartości. Przykładem jest wskazanie nieistniejącej lokalizacji DFS-Namespace lub zmianę głównych list kontroli dostępu w udziale.

Uruchom jeden ostatni RoboCopy zaokrąglenie. Spowoduje to pobranie wszelkich zmian, które mogły zostać pominięte.
Jak długo trwa ten ostatni krok, zależy od szybkości skanowania RoboCopy. Możesz oszacować czas (który jest równy przestoju), mierząc, jak długo trwało wykonywanie poprzedniego uruchomienia.

Utwórz udział w folderze systemu Windows Server i ewentualnie Dostosuj wdrożenie systemu plików DFS-N, aby wskazywało na niego. Pamiętaj, aby ustawić te same uprawnienia na poziomie udziału jak w udziale SMB NAS. Jeśli masz serwer NAS przyłączony do domeny przedsiębiorstwa, identyfikatory SID użytkownika będą automatycznie zgodne, ponieważ użytkownicy znajdują się w Active Directory i RoboCopy kopiuje pliki i metadane z pełną dokładnością. Jeśli używasz lokalnych użytkowników na serwerze NAS, należy ponownie utworzyć tych użytkowników jako lokalnych użytkowników systemu Windows Server i zmapować istniejące identyfikatory SID RoboCopy przenoszone do systemu Windows Server na identyfikatory SID nowych użytkowników lokalnych systemu Windows Server.

Zakończono Migrowanie udziału/grupy udziałów do wspólnego katalogu głównego lub woluminu. (W zależności od mapowania od fazy 1)

Można spróbować uruchomić kilka z tych kopii równolegle. Zalecamy przetwarzanie zakresu jednego udziału plików platformy Azure w danym momencie.

> [!WARNING]
> Po przeniesieniu wszystkich danych z serwera NAS do systemu Windows Server i zakończeniu migracji: Wróć do ***wszystkich***  grup synchronizacji w Azure Portal i Dostosuj wartość procentową ilości wolnego miejsca na woluminie w chmurze do wartości lepiej dopasowanej do wykorzystania pamięci podręcznej, powiedz 20%. 

Zasady wolnego miejsca na woluminie w chmurze działają na poziomie woluminu z potencjalnie wieloma punktami końcowymi serwera. Jeśli zapomnisz o dostosowaniu wolnego miejsca w nawet jednym punkcie końcowym serwera, synchronizacja będzie nadal stosowała najbardziej restrykcyjną regułę i podejmie próbę utrzymania 99% wolnego miejsca na dysku, dzięki czemu lokalna pamięć podręczna nie będzie działała zgodnie z oczekiwaniami. O ile nie jest to cel, aby można było korzystać tylko z przestrzeni nazw dla woluminu, który zawiera tylko rzadko używane dane archiwalne i zachowuje resztę miejsca do magazynowania w innym scenariuszu.

## <a name="troubleshoot"></a>Rozwiązywanie problemów

Najbardziej prawdopodobną przyczyną problemu może być uruchomienie polecenia *Robocopy po stronie* serwera systemu Windows. Obsługa warstw w chmurze jest przeprowadzana co godzinę, aby wypróbować zawartość z lokalnego dysku systemu Windows Server, który został zsynchronizowany. Celem jest osiągnięcie ilości wolnego miejsca na 99% w woluminie.

Zezwalaj na postęp synchronizacji i warstwowanie w chmurze Zwolnij miejsce na dysku. Można obserwować, że w Eksploratorze plików systemu Windows Server.

Gdy system Windows Server ma wystarczającą ilość dostępnej pojemności, polecenie spowoduje rozwiązanie problemu. Nic nie przerywa się w przypadku przechodzenia do tej sytuacji i można ją szybko przenieść do przodu. Jedyną konsekwencją jest to, że w przypadku ponownego uruchomienia polecenia.

Skorzystaj z linku w poniższej sekcji, aby rozwiązywać problemy związane z Azure File Sync.

## <a name="next-steps"></a>Następne kroki

Istnieje więcej informacji na temat udziałów plików platformy Azure i Azure File Sync. Poniższe artykuły ułatwiają zapoznanie się z zaawansowanymi opcjami, najlepszymi rozwiązaniami, a także zawiera pomoc dotyczącą rozwiązywania problemów. Te artykuły zawierają link do [dokumentacji udziału plików platformy Azure](storage-files-introduction.md) zgodnie z potrzebami.

* [Omówienie usługi AFS](./storage-sync-files-planning.md)
* [Podręcznik wdrażania AFS](./storage-how-to-create-file-share.md)
* [Rozwiązywanie problemów z systemem AFS](storage-sync-files-troubleshoot.md)