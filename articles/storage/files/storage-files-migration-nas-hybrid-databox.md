---
title: Migracja lokalnego serwera NAS do Azure File Sync za pośrednictwem usługi Azure DataBox
description: Dowiedz się, jak migrować pliki z lokalizacji magazynu w sieci lokalnej (NAS) do wdrożenia chmury hybrydowej z Azure File Sync za pośrednictwem usługi Azure DataBox.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 03/5/2021
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 144b2f23e40f315441c3de2482ae8aeffe77ec75
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102584068"
---
# <a name="use-databox-to-migrate-from-network-attached-storage-nas-to-a-hybrid-cloud-deployment-with-azure-file-sync"></a>Użyj DataBox do migracji z magazynu dołączanego do sieci (NAS) do wdrożenia chmury hybrydowej za pomocą Azure File Sync

Ten artykuł migracji jest jednym z kilku obejmujących słowa kluczowe NAS, Azure File Sync i Azure DataBox. Sprawdź, czy ten artykuł dotyczy Twojego scenariusza:

> [!div class="checklist"]
> * Źródło danych: magazyn dołączony do sieci (NAS)
> * Marszruta migracji: &rArr; DataBox &rArr; usługi Azure File Share &rArr; z systemem Windows Server
> * Buforowanie plików lokalnie: tak, ostateczny cel to wdrożenie Azure File Sync.

Jeśli Twój scenariusz jest inny, przejrzyj [tabelę przewodników migracji](storage-files-migration-overview.md#migration-guides).

Azure File Sync działa w odniesieniu do lokalizacji magazynu bezpośrednio dołączonego (DAS) i nie obsługuje synchronizacji z lokalizacjami magazynu sieciowego (NAS).
Ten fakt powoduje, że migracja plików jest konieczna, a w tym artykule opisano planowanie i wykonywanie takich migracji.

## <a name="migration-goals"></a>Cele migracji

Celem jest przeniesienie udziałów znajdujących się na urządzeniu NAS do systemu Windows Server. Następnie użyj Azure File Sync do wdrożenia chmury hybrydowej. Migracja musi być realizowana w sposób gwarantujący integralność danych produkcyjnych i dostępności podczas migracji. Druga z nich wymaga utrzymywania przestoju w minimalnym stopniu, dzięki czemu może być dłuższa niż w zwykłych oknach obsługi lub tylko nieco.

## <a name="migration-overview"></a>Omówienie migracji

Proces migracji składa się z kilku faz. Należy wdrożyć konta usługi Azure Storage i udziały plików, wdrożyć lokalny serwer z systemem Windows, skonfigurować Azure File Sync, przeprowadzić migrację za pomocą RoboCopy, a wreszcie wykonać opcję wycinania. W poniższych sekcjach szczegółowo opisano etapy procesu migracji.

> [!TIP]
> Jeśli powrócisz do tego artykułu, użyj nawigacji po prawej stronie, aby przejść do fazy migracji, w której zostało przerwane.

## <a name="phase-1-identify-how-many-azure-file-shares-you-need"></a>Faza 1: określenie, ile potrzebnych udziałów plików platformy Azure

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## <a name="phase-2-deploy-azure-storage-resources"></a>Faza 2: wdrażanie zasobów usługi Azure Storage

W tej fazie zapoznaj się z tabelą mapowania z fazy 1 i użyj jej do aprowizacji poprawnej liczby kont usługi Azure Storage i udziałów plików w nich.

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

## <a name="phase-3-determine-how-many-azure-databox-appliances-you-need"></a>Faza 3: Określanie, ile urządzeń usługi Azure DataBox jest potrzebnych

Uruchom ten krok tylko po zakończeniu poprzedniej fazy. W tej chwili należy utworzyć zasoby usługi Azure Storage (konta magazynu i udziały plików). W ramach kolejności DataBox należy określić, do których kont magazynu DataBox przenosi dane.

W tej fazie należy zmapować wyniki planu migracji z poprzedniej fazy do limitów dostępnych opcji DataBox. Te zagadnienia pomogą Ci utworzyć plan, dla którego należy wybrać opcje DataBox i ile z nich należy przenieść udziały serwerów NAS do udziałów plików platformy Azure.

Aby określić liczbę urządzeń tego typu, należy wziąć pod uwagę następujące ważne limity:

* Każda usługa Azure DataBox może przenosić dane do maksymalnie 10 kont magazynu. 
* Każda opcja DataBox ma własną użyteczną pojemność. Zobacz [Opcje DataBox](#databox-options).

Zapoznaj się z planem migracji liczby kont magazynu, które zamierzasz utworzyć, oraz udziałów w każdej z nich. Następnie Przyjrzyj się rozmiarowi poszczególnych udziałów na serwerze NAS. Połączenie tych informacji umożliwi optymalizację i decyzję o tym, które urządzenie powinno wysyłać dane do tych kont magazynu. Na tym samym koncie magazynu mogą być przenoszone dwa urządzenia DataBox, ale zawartość pojedynczego udziału plików nie zostanie podzielona na 2 pola danych.

### <a name="databox-options"></a>Opcje DataBox

W przypadku standardowej migracji należy wybrać jedną lub kombinację tych trzech opcji DataBox: 

* DataBox dyski, firma Microsoft wyśle Ci i maksymalnie pięć dysków SSD z pojemnością 8 TiB każdy, dla maksymalnie 40 TiB. Wydajność użyteczną jest około 20% mniej, z powodu narzutu na szyfrowanie i system plików. Aby uzyskać więcej informacji, zobacz [Dokumentacja dysków DataBox](../../databox/data-box-disk-overview.md).
* DataBox to najbardziej typowe rozwiązanie. Urządzenie DataBox, które działa podobnie jak NAS, zostanie wysłane do Ciebie. Ma możliwość użycia pojemności 80 TiB. Aby uzyskać więcej informacji, zobacz [dokumentację DataBox](../../databox/data-box-overview.md).
* DataBox silnie ta opcja zapewnia wzmocnione urządzenie DataBox na kołach, które działa podobnie jak w przypadku serwera NAS o pojemności 1 PiB. Wydajność użyteczną jest około 20% mniej, z powodu narzutu na szyfrowanie i system plików. Aby uzyskać więcej informacji, zobacz [DataBox ciężki dokumentacja](../../databox/data-box-heavy-overview.md).

## <a name="phase-4-provision-a-suitable-windows-server-on-premises"></a>Faza 4. Udostępnianie odpowiedniego systemu Windows Server w środowisku lokalnym

Podczas oczekiwania na dotarcie do usługi Azure DataBox można już rozpocząć przeglądanie potrzeb jednego lub kilku serwerów z systemem Windows, które będą używane z Azure File Sync.

* Utwórz system Windows Server 2019 — co najmniej 2012R2 maszyny wirtualnej lub serwera fizycznego. Obsługiwany jest również klaster trybu failover systemu Windows Server.
* Udostępnianie lub Dodawanie bezpośredniego dołączonego magazynu (DAS w porównaniu z serwerem NAS, co nie jest obsługiwane).

Konfiguracja zasobów (obliczeniowa i pamięć RAM) wdrażanego systemu Windows zależy przede wszystkim od liczby elementów (plików i folderów), które zostaną zsynchronizowane. Wyższa Konfiguracja wydajności jest zalecana, jeśli masz jakieś problemy.

[Dowiedz się, jak zmienić rozmiar systemu Windows Server na podstawie liczby elementów (plików i folderów), które mają być synchronizowane.](storage-sync-files-planning.md#recommended-system-resources)

> [!NOTE]
> W wcześniej połączonym artykule przedstawiono tabelę z zakresem dla pamięci serwera (RAM). Można przyróżnić do mniejszej liczby serwerów, ale oczekiwać, że synchronizacja początkowa może trwać znacznie więcej czasu.

## <a name="phase-5-copy-files-onto-your-databox"></a>Faza 5. Kopiowanie plików na DataBox

Po nadejściu DataBox musisz skonfigurować swoje DataBox w linii wglądu do urządzenia NAS. Postępuj zgodnie z dokumentacją Instalatora dla uporządkowanego typu DataBox.

* [Konfigurowanie usługi Data Box](../../databox/data-box-quickstart-portal.md)
* [Konfigurowanie usługi Data Box Disk](../../databox/data-box-disk-quickstart-portal.md)
* [Konfigurowanie usługi Data Box Heavy](../../databox/data-box-heavy-quickstart-portal.md)

W zależności od typu DataBox dostępne są DataBox narzędzia do kopiowania. W tym momencie nie zaleca się przeprowadzania migracji do udziałów plików platformy Azure, ponieważ nie kopiują one plików z pełną dokładnością do DataBox. Zamiast tego użyj RoboCopy.

Po odebraniu usługi DataBox będą dostępne wstępnie udostępniane udziały SMB dla każdego konta magazynu określonego w momencie jego uporządkowania.

* Jeśli pliki znajdują się w udziale plików platformy Azure w warstwie Premium, na koncie magazynu "Magazyn plików Premium" będzie dostępny jeden udział SMB.
* Jeśli pliki znajdują się na koncie magazynu w warstwie Standardowa, będą dostępne trzy udziały SMB na standardowym koncie magazynu (GPv1 i GPv2). Tylko udziały plików kończące `_AzFiles` się na są istotne dla migracji. Ignoruj wszystkie udziały blokowe i obiekty blob typu Page.

Wykonaj kroki opisane w dokumentacji usługi Azure DataBox:

1. [Nawiązywanie połączenia z urządzeniem Data Box](../../databox/data-box-deploy-copy-data.md)
1. Kopiowanie danych na urządzenie Data Box
1. [Przygotuj DataBox do wyjścia na platformę Azure](../../databox/data-box-deploy-picked-up.md)

Połączona dokumentacja DataBox Określa polecenie RoboCopy. Jednak polecenie nie jest odpowiednie, aby zachować pełną wierność plików i folderów. Użyj tego polecenia zamiast:

[!INCLUDE [storage-files-migration-robocopy](../../../includes/storage-files-migration-robocopy.md)]


## <a name="phase-6-deploy-the-azure-file-sync-cloud-resource"></a>Faza 6. wdrażanie zasobu chmury Azure File Sync

Przed kontynuowaniem pracy z tym przewodnikiem Zaczekaj, aż wszystkie pliki dotarły do poprawnych udziałów plików platformy Azure. Proces wysyłania i pozyskiwania danych DataBox zajmie trochę czasu.

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

## <a name="phase-7-deploy-the-azure-file-sync-agent"></a>Faza 7. wdrażanie agenta Azure File Sync

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

## <a name="phase-8-configure-azure-file-sync-on-the-windows-server"></a>Faza 8: Konfigurowanie Azure File Sync w systemie Windows Server

Zarejestrowany lokalny serwer systemu Windows musi być gotowy i połączony z Internetem w ramach tego procesu.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

Włącz funkcję obsługi warstw w chmurze i wybierz pozycję "tylko przestrzeń nazw" w początkowej sekcji pobierania.

> [!IMPORTANT]
> Obsługa warstw w chmurze to funkcja AFS, która umożliwia serwerowi lokalnemu przechowywanie mniejszej pojemności niż w chmurze, ale pełna przestrzeń nazw jest dostępna. Lokalnie interesujące dane są również buforowane lokalnie w celu zapewnienia wydajności szybkiego dostępu. Obsługa warstw w chmurze jest opcjonalną funkcją dla Azure File Sync "punkt końcowy serwera". Należy użyć tej funkcji, jeśli nie masz wystarczającej pojemności dysku lokalnego na serwerze systemu Windows, aby przechowywać wszystkie dane w chmurze, i jeśli chcesz uniknąć pobierania wszystkich danych z chmury.

Powtórz kroki tworzenia grupy synchronizacji i Dodawanie pasującego folderu serwera jako punktu końcowego serwera dla wszystkich udziałów plików platformy Azure/lokalizacji serwera, które muszą zostać skonfigurowane do synchronizacji. Poczekaj na zakończenie synchronizacji przestrzeni nazw. W poniższej sekcji przedstawiono szczegółowe informacje na temat tego, jak można to zapewnić.

> [!NOTE]
> Po utworzeniu punktu końcowego serwera synchronizacja działa. Jednak synchronizacja musi wyliczyć (odszukać) pliki i foldery przenoszone przez DataBox do udziału plików platformy Azure. W zależności od rozmiaru przestrzeni nazw może to potrwać znaczący czas, zanim przestrzeń nazw chmury zacznie pojawić się na serwerze.

## <a name="phase-9-wait-for-the-namespace-to-fully-appear-on-the-server"></a>Faza 9: Poczekaj, aż przestrzeń nazw będzie w pełni widoczna na serwerze

Należy zastanowić się, że wszystkie następne kroki migracji zostały w pełni pobrane przez serwer z udziału w chmurze. W przypadku zbyt wczesnego przesuwania plików na serwer można ryzykownie niepotrzebne operacje przekazywania, a nawet konflikty synchronizacji plików.

Aby ustalić, czy serwer zakończył początkową synchronizację pobierania, Otwórz Podgląd zdarzeń na serwerze z systemem Windows i użyj Azure File Sync dziennika zdarzeń telemetrii.
Dziennik zdarzeń telemetrii znajduje się w Podgląd zdarzeń w obszarze aplikacje i Services\Microsoft\FileSync\Agent.

Wyszukaj najnowsze zdarzenie 9102. Zdarzenie o IDENTYFIKATORze 9102 jest rejestrowane po zakończeniu sesji synchronizacji. W polu tekstowym zdarzenia, jest polem dla kierunku synchronizacji pobierania. ( `HResult` musi mieć wartość zero i pliki pobrane również).
 
Chcesz zobaczyć dwa kolejne zdarzenia tego typu i zawartości, aby sprawdzić, czy serwer zakończył pobieranie przestrzeni nazw. Jest to prawidłowe, jeśli istnieją różne zdarzenia wyzwalane między zdarzeniami 2 9102.

## <a name="phase-10-catch-up-robocopy-from-your-nas"></a>Faza 10: Przechwytywanie RoboCopy z serwera NAS

Po zakończeniu wstępnej synchronizacji całej przestrzeni nazw z udziału w chmurze przez serwer można wykonać ten krok. Przed kontynuowaniem tego kroku należy wykonać ten krok. Szczegółowe informacje znajdują się w poprzedniej sekcji.

W tym kroku zostaną uruchomione zadania RoboCopy, aby przechwycić udziały w chmurze przy użyciu najnowszych zmian wprowadzonych na serwerze NAS od czasu przetworzenia rozwidlenia udziałów na DataBox.
Ten RoboCopy może zakończyć się szybko lub trochę potrwać, w zależności od liczby zmian, które wystąpiły w udziałach serwera NAS.

> [!WARNING]
> Ze względu na zachowanie uległa pogorszeniu RoboCopy w systemie Windows Server 2019 przełącznik/MIR RoboCopy nie jest zgodny z katalogiem docelowym warstwowym. W tej fazie migracji nie należy używać klienta systemu Windows Server 2019 lub Windows 10. Użyj RoboCopy na pośrednim serwerze Windows Server 2016.

Podstawowe podejście migracji to RoboCopy z urządzenia NAS do systemu Windows Server, a Azure File Sync do udziałów plików platformy Azure.

Uruchom pierwszą kopię lokalną w folderze docelowym systemu Windows Server:

1. Zidentyfikuj pierwszą lokalizację na urządzeniu NAS.
1. Zidentyfikuj pasujący folder na serwerze z systemem Windows, dla którego skonfigurowano już Azure File Sync.
1. Rozpocznij kopiowanie przy użyciu RoboCopy

Następujące polecenie RoboCopy skopiuje tylko różnice (zaktualizowane pliki i foldery) z magazynu NAS do folderu docelowego systemu Windows Server. System Windows Server zsynchronizuje je z udziałami plików platformy Azure. 

[!INCLUDE [storage-files-migration-robocopy](../../../includes/storage-files-migration-robocopy.md)]

W przypadku zainicjowania obsługi mniejszej ilości miejsca w systemie Windows Server niż pliki na urządzeniu NAS, skonfigurowano obsługę warstw w chmurze. W miarę jak wolumin lokalnego systemu Windows Server jest pełny, obsługa [warstw w chmurze](storage-sync-cloud-tiering-overview.md) zostanie rozpoczęta i pliki warstw, które zostały już pomyślnie zsynchronizowane. Obsługa warstw w chmurze spowoduje wygenerowanie wystarczającej ilości miejsca, aby kontynuować kopiowanie z urządzenia NAS. Obsługa warstw w chmurze jest sprawdzana raz na godzinę, aby zobaczyć, co zostało zsynchronizowane, i zwolnić miejsce na dysku, aby uzyskać dostęp do 99% wolnego miejsca na woluminie.
Istnieje możliwość, że RoboCopy musi przenieść wiele plików, więcej niż masz magazyn lokalny dla systemu Windows Server. Średnio można oczekiwać, że RoboCopy przeniesie dużą ilość szybciej niż Azure File Sync mogą przekazywać pliki poza woluminem lokalnym. RoboCopy zakończy się niepowodzeniem. Zalecane jest, aby wykonać czynności wykonywane przez udziały w sekwencji, która uniemożliwia to. Na przykład nie uruchamiaj zadań RoboCopy dla wszystkich udziałów w tym samym czasie lub przenosi tylko udziały pasujące do bieżącej ilości wolnego miejsca w systemie Windows Server, aby wymienić kilka. Dobrą wiadomość polega na tym, że przełącznik/MIR będzie przeniesieł tylko przyrosty, a po przeniesieniu delty ponownie uruchomione zadanie nie będzie wymagało ponownego przeniesienia tego pliku.

### <a name="user-cut-over"></a>Wytnij użytkownika

Po pierwszym uruchomieniu polecenia RoboCopy Użytkownicy i aplikacje nadal uzyskują dostęp do plików na serwerze NAS i mogą je zmienić. Istnieje możliwość, że RoboCopy przetworzył katalog, przechodzi do następnego, a następnie użytkownik w lokalizacji źródłowej (NAS) dodaje, zmienia lub usuwa plik, który nie zostanie przetworzony w bieżącym przebiegu RoboCopy. Takie zachowanie jest oczekiwane.

Pierwszy przebieg polega na przenoszeniu zbiorczych danych do systemu Windows Server i w chmurze za pośrednictwem Azure File Sync. Ta pierwsza kopia może zająć dużo czasu, w zależności od:

* przepustowość przekazywania
* szybkość sieci lokalnej i liczba optymalnie dopasowanej liczby wątków RoboCopy
* Liczba elementów (plików i folderów), które muszą być przetwarzane przez RoboCopy i Azure File Sync

Po zakończeniu pierwszego uruchomienia Uruchom polecenie ponownie.

Drugi raz uruchamiasz RoboCopy dla tego samego udziału, zostanie on szybszy, ponieważ wymaga tylko zmiany, które wystąpiły od momentu ostatniego uruchomienia. Można uruchamiać powtarzające się zadania dla tego samego udziału.

Po rozważeniu niedopuszczalnego przestoju należy usunąć dostęp użytkowników do udziałów opartych na protokole NAS. Można to zrobić, wykonując czynności, które uniemożliwiają użytkownikom zmianę struktury plików i folderów oraz zawartości. Przykładem jest wskazanie nieistniejącej lokalizacji DFS-Namespace lub zmianę głównych list kontroli dostępu w udziale.

Uruchom jeden ostatni RoboCopy zaokrąglenie. Spowoduje to pobranie wszelkich zmian, które mogły zostać pominięte.
Jak długo trwa ten ostatni krok, zależy od szybkości skanowania RoboCopy. Możesz oszacować czas (który jest równy przestoju), mierząc, jak długo trwało wykonywanie poprzedniego uruchomienia.

Utwórz udział w folderze systemu Windows Server i ewentualnie Dostosuj wdrożenie systemu plików DFS-N, aby wskazywało na niego. Pamiętaj, aby ustawić te same uprawnienia na poziomie udziału jak w udziale SMB NAS. Jeśli masz serwer NAS przyłączony do domeny przedsiębiorstwa, identyfikatory SID użytkownika będą automatycznie zgodne, ponieważ użytkownicy znajdują się w Active Directory i RoboCopy kopiuje pliki i metadane z pełną dokładnością. Jeśli używasz lokalnych użytkowników na serwerze NAS, należy ponownie utworzyć tych użytkowników jako lokalnych użytkowników systemu Windows Server i zmapować istniejące identyfikatory SID RoboCopy przenoszone do systemu Windows Server na identyfikatory SID nowych użytkowników lokalnych systemu Windows Server.

Zakończono Migrowanie udziału/grupy udziałów do wspólnego katalogu głównego lub woluminu. (W zależności od mapowania od fazy 1)

Można spróbować uruchomić kilka z tych kopii równolegle. Zalecamy przetwarzanie zakresu jednego udziału plików platformy Azure w danym momencie.

## <a name="troubleshoot"></a>Rozwiązywanie problemów

Najbardziej prawdopodobną przyczyną problemu może być uruchomienie polecenia *Robocopy po stronie* serwera systemu Windows. Obsługa warstw w chmurze jest przeprowadzana co godzinę, aby wypróbować zawartość z lokalnego dysku systemu Windows Server, który został zsynchronizowany. Celem jest osiągnięcie ilości wolnego miejsca na 99% w woluminie.

Zezwalaj na postęp synchronizacji i warstwowanie w chmurze Zwolnij miejsce na dysku. Można obserwować, że w Eksploratorze plików systemu Windows Server.

Gdy system Windows Server ma wystarczającą ilość dostępnej pojemności, polecenie spowoduje rozwiązanie problemu. Nic nie przerywa się w przypadku przechodzenia do tej sytuacji i można ją szybko przenieść do przodu. Jedyną konsekwencją jest to, że w przypadku ponownego uruchomienia polecenia.

Skorzystaj z linku w poniższej sekcji, aby rozwiązywać problemy związane z Azure File Sync.

## <a name="next-steps"></a>Następne kroki

Istnieje więcej informacji na temat udziałów plików platformy Azure i Azure File Sync. Poniższe artykuły ułatwiają zapoznanie się z zaawansowanymi opcjami, najlepszymi rozwiązaniami, a także zawiera pomoc dotyczącą rozwiązywania problemów. Te artykuły zawierają link do [dokumentacji udziału plików platformy Azure](storage-files-introduction.md) zgodnie z potrzebami.

* [Omówienie migracji](storage-files-migration-overview.md)
* [Omówienie usługi AFS](./storage-sync-files-planning.md)
* [Podręcznik wdrażania AFS](./storage-how-to-create-file-share.md)
* [Rozwiązywanie problemów z systemem AFS](storage-sync-files-troubleshoot.md)
