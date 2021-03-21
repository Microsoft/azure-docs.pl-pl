---
title: Migracja systemu Linux do Azure File Sync
description: Dowiedz się, jak migrować pliki z lokalizacji serwera z systemem Linux do wdrożenia w chmurze hybrydowej przy użyciu Azure File Sync i udziałów plików platformy Azure.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 03/19/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: ff26318cafdf493579961fc718643f831ae9efeb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102564258"
---
# <a name="migrate-from-linux-to-a-hybrid-cloud-deployment-with-azure-file-sync"></a>Migrowanie z systemu Linux do wdrożenia chmury hybrydowej za pomocą Azure File Sync

Ten artykuł migracji jest jednym z kilku obejmujących słowa kluczowe systemu plików NFS i Azure File Sync. Sprawdź, czy ten artykuł dotyczy Twojego scenariusza:

> [!div class="checklist"]
> * Źródło danych: magazyn dołączony do sieci (NAS)
> * Marszruta migracji: serwer z systemem Linux z &rArr; systemem Windows Server 2012R2 lub nowszym &rArr; Synchronizacja z udziałami plików platformy Azure
> * Buforowanie plików lokalnie: tak, ostateczny cel to wdrożenie Azure File Sync.

Jeśli Twój scenariusz jest inny, przejrzyj [tabelę przewodników migracji](storage-files-migration-overview.md#migration-guides).

Azure File Sync działa w wystąpieniach systemu Windows Server z bezpośrednio dołączonym magazynem (DAS). Nie obsługuje synchronizacji z i od klientów systemu Linux ani udziałów zdalnego bloku komunikatów serwera (SMB) ani udziału sieciowego systemu plików (NFS).

W związku z tym przekształcenia usług plików w wdrożenie hybrydowe powodują konieczność migracji do systemu Windows Server. W tym artykule opisano planowanie i wykonywanie takich migracji.

## <a name="migration-goals"></a>Cele migracji

Celem jest przeniesienie udziałów znajdujących się na serwerze z systemem Linux Samba do wystąpienia systemu Windows Server. Następnie użyj Azure File Sync do wdrożenia chmury hybrydowej. Migracja musi być realizowana w sposób gwarantujący integralność danych produkcyjnych i dostępności podczas migracji. Druga z nich wymaga utrzymywania przestoju w minimalnym stopniu, dzięki czemu może być dłuższa niż w zwykłych oknach obsługi lub tylko nieco.

## <a name="migration-overview"></a>Omówienie migracji

Jak wspomniano w [artykule Omówienie migracji](storage-files-migration-overview.md)Azure Files przy użyciu prawidłowego narzędzia do kopiowania i podejścia jest ważne. Serwer z systemem Linux Samba udostępnia udziały SMB bezpośrednio w sieci lokalnej. Robocopy wbudowane w system Windows Server to najlepszy sposób na przeniesienie plików w tym scenariuszu migracji.

Jeśli nie korzystasz z programu Samba na serwerze z systemem Linux i nie chcesz migrować folderów do wdrożenia hybrydowego w systemie Windows Server, możesz użyć narzędzi do kopiowania systemu Linux zamiast Robocopy. Należy pamiętać o możliwościach dokładności narzędzia kopiowania. Zapoznaj się z [sekcją informacje o migracji](storage-files-migration-overview.md#migration-basics) w artykule Omówienie migracji, aby dowiedzieć się, co należy szukać w narzędziu do kopiowania.

## <a name="phase-1-identify-how-many-azure-file-shares-you-need"></a>Faza 1: określenie, ile potrzebnych udziałów plików platformy Azure

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## <a name="phase-2-provision-a-suitable-windows-server-instance-on-premises"></a>Faza 2. Udostępnianie odpowiedniego wystąpienia systemu Windows Server w środowisku lokalnym

* Utwórz wystąpienie systemu Windows Server 2019 jako maszynę wirtualną lub serwer fizyczny. System Windows Server 2012 R2 jest minimalnym wymaganiem. Jest również obsługiwany klaster trybu failover z systemem Windows Server.
* Inicjowanie obsługi administracyjnej lub Dodawanie bezpośredniego dołączonego magazynu (DAS). Magazyn dołączony do sieci (NAS) nie jest obsługiwany.

  Ilość dostępnego miejsca do magazynowania może być mniejsza niż obecnie używane na serwerze z systemem Linux Samba, jeśli używasz funkcji obsługi [warstw w chmurze](storage-sync-cloud-tiering-overview.md) Azure File Sync. 

Ilość dostępnego miejsca w magazynie może być mniejsza niż obecnie używane na serwerze z systemem Linux Samba. Ta konfiguracja wymaga również użycia funkcji obsługi [warstw w chmurze](storage-sync-cloud-tiering-overview.md) usługi Azure File Sync. Jednak w przypadku kopiowania plików z większej ilości wolnego miejsca serwera z systemem Linux do mniejszego woluminu systemu Windows Server w późniejszej fazie należy wykonać następujące czynności:

  1. Przenieś zestaw plików, który mieści się na dysku.
  2. Zezwalaj na synchronizację plików i obsługę warstw w chmurze.
  3. Po utworzeniu większej ilości wolnego miejsca na woluminie przejdź do następnej partii plików. Alternatywnie można przejrzeć polecenie RoboCopy w nadchodzącej [sekcji Robocopy](#phase-7-robocopy) , aby użyć nowego `/LFSM` przełącznika. Korzystanie `/LFSM` z programu może znacznie uprościć zadania Robocopy, ale nie jest zgodne z innymi przełącznikami Robocopy, które mogą być od siebie zależne.
    
  Możesz uniknąć tego podejścia wsadowego, udostępniając odpowiednie miejsce w wystąpieniu systemu Windows Server, że pliki zajmują na serwerze z systemem Linux. Rozważ włączenie deduplikacji w systemie Windows. Jeśli nie chcesz trwale zatwierdzić tej dużej ilości miejsca w magazynie w wystąpieniu systemu Windows Server, możesz zmniejszyć rozmiar woluminu po migracji i przed dopasowaniem zasad obsługi warstw w chmurze. Powoduje to utworzenie mniejszej lokalnej pamięci podręcznej udziałów plików platformy Azure.

Konfiguracja zasobów (obliczeniowa i pamięć RAM) wdrażanego wystąpienia systemu Windows Server zależy przede wszystkim od liczby elementów (plików i folderów), które będą synchronizowane. Jeśli masz jakieś problemy, zalecamy konfigurację wyższej wydajności.

[Dowiedz się, jak zmienić rozmiar wystąpienia systemu Windows Server na podstawie liczby elementów (plików i folderów), które mają być synchronizowane.](storage-sync-files-planning.md#recommended-system-resources)

> [!NOTE]
> W wcześniej połączonym artykule przedstawiono tabelę z zakresem dla pamięci serwera (RAM). Można zorientowanie się na mniejszą liczbę dla serwera, ale oczekuje się, że synchronizacja początkowa może trwać znacznie więcej czasu.

## <a name="phase-3-deploy-the-azure-file-sync-cloud-resource"></a>Faza 3: wdrażanie zasobu chmury Azure File Sync

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

## <a name="phase-4-deploy-azure-storage-resources"></a>Faza 4. wdrażanie zasobów usługi Azure Storage

W tej fazie zapoznaj się z tabelą mapowania z fazy 1 i użyj jej do aprowizacji poprawnej liczby kont usługi Azure Storage i udziałów plików w nich.

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

## <a name="phase-5-deploy-the-azure-file-sync-agent"></a>Faza 5. wdrażanie agenta Azure File Sync

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

## <a name="phase-6-configure-azure-file-sync-on-the-windows-server-deployment"></a>Faza 6: Konfigurowanie Azure File Sync w ramach wdrażania systemu Windows Server

Zarejestrowane lokalne wystąpienie systemu Windows Server musi być gotowe i połączone z Internetem dla tego procesu.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!IMPORTANT]
> Obsługa warstw w chmurze jest funkcją Azure File Sync, która umożliwia serwerowi lokalnemu przechowywanie mniejszej pojemności niż w chmurze, ale pełna przestrzeń nazw jest dostępna. Lokalnie interesujące dane są również buforowane lokalnie w celu zapewnienia wydajności szybkiego dostępu. Obsługa warstw w chmurze to opcjonalna funkcja dla każdego punktu końcowego serwera Azure File Sync.

> [!WARNING]
> W przypadku zainicjowania obsługi mniejszej ilości miejsca na woluminach z systemem Windows Server niż dane używane na serwerze z systemem Linux, warstwy chmury są obowiązkowe. Jeśli nie włączysz usługi Cloud warstw, serwer nie zwolni miejsca do przechowywania wszystkich plików. Ustaw zasady dotyczące warstw, tymczasowo dla migracji, na 99% wolnego miejsca dla woluminu. Upewnij się, że po zakończeniu migracji nastąpi powrót do ustawień warstw w chmurze, i ustaw bardziej przydatny poziom zasad na dłuższy okres.

Powtórz kroki tworzenia grupy synchronizacji i Dodawanie pasującego folderu serwera jako punktu końcowego serwera dla wszystkich udziałów plików platformy Azure i lokalizacji serwera, które muszą zostać skonfigurowane do synchronizacji.

Po utworzeniu wszystkich punktów końcowych serwera synchronizacja działa. Można utworzyć plik testowy i zobaczyć jego synchronizację z lokalizacji serwera do połączonego udziału plików platformy Azure (zgodnie z opisem w punkcie końcowym w chmurze w grupie synchronizacji).

Obie lokalizacje, foldery na serwerze i udziały plików platformy Azure, są w inny sposób puste i oczekują na dane. W następnym kroku rozpocznie się kopiowanie plików do wystąpienia systemu Windows Server w celu Azure File Sync przenoszenia ich do chmury. W przypadku włączenia obsługi warstw w chmurze serwer rozpocznie pracę w warstwach w przypadku braku pojemności na woluminach lokalnych.

## <a name="phase-7-robocopy"></a>Faza 7: Robocopy

Podstawowym podejściem do migracji jest użycie Robocopy do kopiowania plików i używania Azure File Sync do synchronizacji.

Uruchom pierwszą kopię lokalną w folderze docelowym systemu Windows Server:

1. Zidentyfikuj pierwszą lokalizację na serwerze z systemem Linux Samba.
1. Zidentyfikuj pasujący folder w wystąpieniu systemu Windows Server, dla którego skonfigurowano już Azure File Sync.
1. Rozpocznij kopiowanie za pomocą Robocopy.

Następujące polecenie Robocopy skopiuje pliki z magazynu serwera z systemem Linux Samba do folderu docelowego systemu Windows Server. System Windows Server zsynchronizuje go z udziałami plików platformy Azure. 

W przypadku obsługi mniejszej ilości miejsca w magazynie w wystąpieniu systemu Windows Server, gdy pliki zostaną wdrożone na serwerze z systemem Linux Samba, skonfigurowano obsługę warstw w chmurze. W miarę jak wolumin lokalnego systemu Windows Server jest zapełniony, obsługa [warstw w chmurze](storage-sync-cloud-tiering-overview.md) zostanie uruchomiona i pliki warstw, które zostały pomyślnie zsynchronizowane. Obsługa warstw w chmurze spowoduje wygenerowanie wystarczającej ilości miejsca, aby kontynuować kopiowanie z serwera z systemem Linux Samba. Obsługa warstw w chmurze jest sprawdzana raz na godzinę, aby sprawdzić, co zostało zsynchronizowane i zwolnić miejsce na dysku w celu osiągnięcia zasad o 99% wolnego miejsca na woluminie.

Możliwe jest, że Robocopy przenosi pliki szybciej, niż można zsynchronizować lokalnie z chmurą i warstwą, co powoduje brak miejsca na dysku lokalnym. Robocopy zakończy się niepowodzeniem. Firma Microsoft zaleca, aby wykonać czynności wykonywane przez udziały w sekwencji, która uniemożliwia problem. Rozważmy na przykład nieuruchamianie zadań Robocopy dla wszystkich udziałów w tym samym czasie. Lub Rozważ przeniesienie udziałów pasujących do bieżącej ilości wolnego miejsca w wystąpieniu systemu Windows Server. Jeśli zadanie Robocopy zakończy się niepowodzeniem, można zawsze ponownie uruchomić polecenie, o ile jest używana następująca opcja dublowania/przeczyszczania:

[!INCLUDE [storage-files-migration-robocopy](../../../includes/storage-files-migration-robocopy.md)]

## <a name="phase-8-user-cut-over"></a>Faza 8: wycinanie użytkownika

Po uruchomieniu polecenia Robocopy po raz pierwszy Użytkownicy i aplikacje nadal uzyskują dostęp do plików na serwerze z systemem Linux Samba i mogą je zmieniać. Istnieje możliwość, że Robocopy przetworzył katalog i przechodzi do następnego, a następnie użytkownik w lokalizacji źródłowej (Linux) doda, zmieni lub usunie plik, który teraz nie zostanie przetworzony w tym bieżącym przebiegu Robocopy. Takie zachowanie jest oczekiwane.

Pierwszy przebieg polega na przenoszeniu ilości danych do wystąpienia systemu Windows Server i w chmurze za pośrednictwem Azure File Sync. Ta pierwsza kopia może zająć dużo czasu, w zależności od:

* Przepustowość pobierania.
* Przepustowość przekazywania.
* Szybkość sieci lokalnej oraz liczba optymalnych dla nich liczby wątków Robocopy.
* Liczba elementów (plików i folderów), które Robocopy i Azure File Sync wymagają przetworzenia.

Po zakończeniu pierwszego uruchomienia Uruchom polecenie ponownie.

Kończy się to szybciej, ponieważ wymaga jedynie transportowania zmian, które wystąpiły od momentu ostatniego uruchomienia. W tej sekundzie można nadal zbierać nowe zmiany.

Powtarzaj ten proces do momentu, gdy okaże się, że czas potrzebny na ukończenie operacji Robocopy dla określonej lokalizacji znajduje się w akceptowalnym oknie do przestoju.

Jeśli rozważasz niedopuszczalne przestoje i przygotowasz lokalizację systemu Linux w trybie offline, możesz zmienić listy ACL w katalogu głównym udziału, aby użytkownicy nie mieli już dostępu do tej lokalizacji. Można też wykonać inne czynności, które uniemożliwiają zmianę zawartości w tym folderze na serwerze z systemem Linux.

Uruchom jeden ostatni Robocopy zaokrąglenie. Spowoduje to pobranie wszelkich zmian, które mogły zostać pominięte. Czas trwania tego końcowego kroku zależy od szybkości skanowania Robocopy. Możesz oszacować czas (który jest równy przestoju), mierząc, jak długo trwało wykonywanie poprzedniego uruchomienia.

Utwórz udział w folderze systemu Windows Server i ewentualnie Dostosuj wdrożenie systemu plików DFS-N, aby wskazywało na niego. Pamiętaj, aby ustawić te same uprawnienia na poziomie udziału co w udziałach SMB serwera z systemem Linux Samba. Jeśli używasz lokalnych użytkowników na serwerze z systemem Linux Samba, należy ponownie utworzyć tych użytkowników jako lokalnych użytkowników systemu Windows Server. Należy również zmapować istniejące identyfikatory SID, które Robocopy przenoszone do wystąpienia systemu Windows Server na identyfikatory SID nowych użytkowników lokalnych systemu Windows Server. Jeśli używane są konta Active Directory i listy ACL, Robocopy przeniesie je w taki sposób, że nie są wymagane żadne dalsze działania.

Zakończono Migrowanie udziału lub grupy udziałów do wspólnego katalogu głównego lub woluminu (w zależności od mapowania z fazy 1).

Można spróbować uruchomić kilka z tych kopii równolegle. Zalecamy przetwarzanie zakresu jednego udziału plików platformy Azure w danym momencie.

> [!WARNING]
> Po przeniesieniu wszystkich danych z serwera z systemem Linux Samba do wystąpienia systemu Windows Server, a migracja zostanie zakończona, Wróć do *wszystkich*  grup synchronizacji w Azure Portal. Dostosuj wartość procentową wolnego miejsca dla woluminu warstwowego chmury do lepszego dopasowania do użycia pamięci podręcznej, na przykład 20 procent. 

Zasady wolnego miejsca w woluminie warstwowym w chmurze działają na poziomie woluminu z potencjalnie wieloma punktami końcowymi serwera. Jeśli zapomnisz o dostosowaniu wolnego miejsca w nawet jednym punkcie końcowym serwera, synchronizacja będzie nadal stosowała najbardziej restrykcyjną regułę i podejmie próbę utrzymania wolnego miejsca na dysku o 99%. Lokalna pamięć podręczna może nie działać zgodnie z oczekiwaniami. Wydajność może być akceptowalna, jeśli celem jest posiadanie przestrzeni nazw dla woluminu, który zawiera tylko rzadko używane dane archiwalne, i zachowuje resztę miejsca do magazynowania w innym scenariuszu.

## <a name="troubleshoot"></a>Rozwiązywanie problemów

Najbardziej typowym problemem jest to, że polecenie Robocopy kończy się niepowodzeniem z **pełnymi woluminami** po stronie serwera systemu Windows. Obsługa warstw w chmurze jest przeprowadzana co godzinę, aby wypróbować zawartość z lokalnego dysku systemu Windows Server, który został zsynchronizowany. Celem jest osiągnięcie wolnego miejsca na 99% w woluminie.

Zezwalaj na postęp synchronizacji i warstwowanie w chmurze Zwolnij miejsce na dysku. Można obserwować, że w Eksploratorze plików w systemie Windows Server.

Gdy wystąpienie systemu Windows Server ma wystarczającą ilość dostępnej pojemności, wykonanie polecenia spowoduje rozwiązanie problemu. Nic nie przerywa, gdy w tej sytuacji możesz przejść do przodu. Jest to jedyna konsekwencja, aby ponownie uruchomić polecenie.

Skorzystaj z linku w poniższej sekcji, aby rozwiązać problemy związane z Azure File Sync.

## <a name="next-steps"></a>Następne kroki

Nie ma więcej informacji o udziałach plików platformy Azure i Azure File Sync. Poniższe artykuły zawierają zaawansowane opcje, najlepsze rozwiązania i pomoc dotyczącą rozwiązywania problemów. Te artykuły zawierają link do [dokumentacji udziału plików platformy Azure](storage-files-introduction.md) zgodnie z potrzebami.

* [Przegląd Azure File Sync](./storage-sync-files-planning.md)
* [Przewodnik wdrażania Azure File Sync](./storage-how-to-create-file-share.md)
* [Rozwiązywanie problemów z usługą Azure File Sync](storage-sync-files-troubleshoot.md)