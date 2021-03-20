---
title: StorSimple 8000 Series Update 2,2 — informacje o wersji | Microsoft Docs
description: W tym artykule opisano nowe funkcje, problemy i Obejścia dotyczące aktualizacji z serii StorSimple 8000 2,2.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 5cf03ea8-2a0f-4552-b6dc-7ea517783d7b
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.openlocfilehash: 73b9ecd03875b60ed2d9b9d4c8e8a3a0c8de3cfa
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94956606"
---
# <a name="storsimple-8000-series-update-22-release-notes"></a>StorSimple 8000 Series Update 2,2 — informacje o wersji

## <a name="overview"></a>Omówienie
W poniższych informacjach o wersji opisano nowe funkcje i zidentyfikowanie krytycznych problemów otwartych dla aktualizacji z serii StorSimple 8000 2,2. Zawierają także listę StorSimple aktualizacji oprogramowania zawartych w tej wersji.

Aktualizacja 2,2 może zostać zastosowana do wszystkich urządzeń StorSimple z systemem lub aktualizacji 0,1 za poorednictwem aktualizacji Update 2,1. Wersja urządzenia skojarzona z aktualizacją 2,2 to 6.3.9600.17708.

Przed wdrożeniem aktualizacji w rozwiązaniu StorSimple zapoznaj się z informacjami zawartymi w informacjach o wersji.

> [!IMPORTANT]
> * Aktualizacja 2,2 ma aktualizacje oprogramowania. Zainstalowanie tej aktualizacji wymaga około 1,5 – 2 godzin. 
> * W przypadku korzystania z aktualizacji 2,1 zalecamy, aby jak najszybciej zastosować aktualizację 2,2.
> * W przypadku nowych wersji aktualizacje mogą nie być od razu widoczne, ponieważ przeprowadzamy stopniowe wdrażanie aktualizacji. Poczekaj kilka dni, a następnie ponownie przeprowadź skanowanie w poszukiwaniu aktualizacji, ponieważ staną się one dostępne wkrótce.
> 
> 

## <a name="whats-new-in-update-22"></a>Co nowego w aktualizacji Update 2,2
W aktualizacji 2,2 wprowadzono następujące najważniejsze ulepszenia.

* **Zautomatyzowana optymalizacja przestrzeni** dyskowej — po usunięciu danych w woluminach alokowanych elastycznie należy odzyskać nieużywane bloki magazynu. W tej wersji Ulepszono proces odzyskiwania miejsca z chmury, co spowodowało, że nieużywane miejsce staje się dostępne szybciej w porównaniu z poprzednimi wersjami.
* **Udoskonalenia wydajności migawek** — aktualizacja 2,2 poprawiła czas przetwarzania migawek w chmurze w niektórych scenariuszach, w których są używane duże woluminy i nie ma możliwości zmiany danych. Scenariusz, który mógłby skorzystać z tego rozszerzenia, to woluminy archiwum.
* **Zabezpieczanie zbierania pakietów pomocy technicznej** — wprowadzono ulepszenia w sposobie zbierania i przekazywania pakietu pomocy technicznej w tej wersji. 
* **Aktualizowanie ulepszeń dotyczących niezawodności** — ta wersja zawiera poprawki błędów, które powodują poprawę niezawodności aktualizacji.

## <a name="issues-fixed-in-update-22"></a>Problemy rozwiązane w aktualizacji 2,2
W poniższych tabelach przedstawiono podsumowanie problemów, które zostały rozwiązane w aktualizacjach 2,2 i 2,1.    

| Nie | Promowanie | Problem | Dotyczy urządzenia fizycznego | Dotyczy urządzenia wirtualnego |
| --- | --- | --- | --- | --- |
| 1 |Wydajność hosta |W starszej wersji zaobserwowano problemy z wydajnością po stronie hosta podczas tworzenia woluminu przypiętego lokalnie i podczas konwersji woluminu warstwowego na wolumin przypięty lokalnie. Te problemy zostały rozwiązane w tej wersji, co spowodowało zwiększenie wydajności hosta podczas procedur tworzenia i konwersji woluminów. |Tak |Nie |
| 2 |Lokalnie przypięte woluminy |W rzadkich przypadkach system mógłby ulec awarii podczas tworzenia woluminu przypiętego lokalnie. Ta usterka została rozwiązana w tej wersji. |Tak |Nie |
| 3 |Warstw |Wystąpiły Sporadyczne awarie, gdy metadane dla urządzeń w chmurze StorSimple (8010 i 8020) są warstwowe w chmurze. Ten problem został rozwiązany w tej wersji. |Nie |Tak |
| 4 |Tworzenie migawki |Wystąpiły problemy związane z tworzeniem migawek przyrostowych w scenariuszach z dużymi woluminami i minimalnym niezmianą danych. Te problemy zostały rozwiązane w tej wersji. |Tak |Tak |
| 5 |Uwierzytelnianie OpenStack |W przypadku korzystania z OpenStack jako dostawcy usług w chmurze, użytkownik będzie uruchamiał się w nieczęstej usterki związanej z uwierzytelnianiem, gdzie Analizator JSON spowodował awarię. Ta usterka została rozwiązana w tej wersji. |Tak |Nie |
| 6 |Kopia po stronie hosta |We wcześniejszych wersjach oprogramowania podczas kopiowania danych z jednego woluminu na inny występuje nierzadko występujący błąd związany z ODX chronometrażu. Spowoduje to przejście do trybu failover kontrolera, a system może być w stanie przejść do trybu odzyskiwania. Ta usterka została rozwiązana w tej wersji. |Tak |Nie |
| 7 |Instrumentacja zarządzania Windows (WMI) |W poprzednich wersjach oprogramowania Wystąpił błąd serwera proxy sieci Web z wyjątkiem " \<ManagementException> niepowodzenie ładowania dostawcy". Ta usterka została przypisana do przecieku pamięci usługi WMI i została teraz naprawiona. |Tak |Nie |
| 8 |Aktualizacja |W niektórych rzadkich wystąpieniach w poprzednich wersjach oprogramowania użytkownik otrzymał komunikat "CisPowershellHcsscripterror" podczas próby skanowania lub instalowania aktualizacji. Ten problem został rozwiązany w tej wersji. |Tak |Tak |
| 9 |Pakiet pomocy technicznej |W tej wersji wprowadzono ulepszenia sposobu, w jaki pakiet pomocy technicznej jest zbierany i przekazywany. |Tak |Tak |

## <a name="known-issues-in-update-22"></a>Znane problemy w aktualizacji 2,2
Poniższa tabela zawiera podsumowanie znanych problemów występujących w tej wersji.

| Nie. | Cecha | Problem | Komentarze/obejście | Dotyczy urządzenia fizycznego | Dotyczy urządzenia wirtualnego |
| --- | --- | --- | --- | --- | --- |
| 1 |Kworum dysku |W rzadkich przypadkach, jeśli większość dysków w obudowie EBOD urządzenia 8600 zostanie odłączona, co spowodowało brak kworum dysku, Pula magazynów przejdzie w tryb offline. Pozostanie ono w trybie offline, nawet jeśli dyski zostaną ponownie połączone. |Konieczne będzie ponowne uruchomienie urządzenia. Jeśli problem będzie się powtarzać, skontaktuj się z firmą pomoc techniczna firmy Microsoft w celu wykonania następnych kroków. |Tak |Nie |
| 2 |Niepoprawny identyfikator kontrolera |Gdy jest przeprowadzane Zastępowanie kontrolera, kontroler 0 może być wyświetlany jako kontroler 1. Podczas zastępowania kontrolera, gdy obraz jest ładowany z węzła równorzędnego, identyfikator kontrolera może początkowo być wyświetlany jako identyfikator kontrolera równorzędnego. W rzadkich przypadkach takie zachowanie może być również widoczne po ponownym uruchomieniu systemu. |Nie jest wymagane wykonanie jakiejkolwiek czynności przez użytkownika. Ta sytuacja zostanie rozwiązany po zakończeniu zastępowania kontrolera. |Tak |Nie |
| 3 |Konta magazynu |Użycie usługi Storage w celu usunięcia konta magazynu jest nieobsługiwanym scenariuszem. Spowoduje to powstanie sytuacji, w której nie można pobrać danych użytkownika. | |Tak |Tak |
| 4 |Tryb failover urządzenia |Wiele przełączeń w tryb failover kontenera woluminów z tego samego urządzenia źródłowego do różnych urządzeń docelowych nie jest obsługiwane. Przełączenie w tryb failover z pojedynczego martwego urządzenia do wielu urządzeń spowoduje utratę własności danych przez kontenery woluminów na urządzeniu z systemem. Po przejściu do trybu failover te kontenery woluminów będą wyświetlane lub zachowywać się inaczej po wyświetleniu ich w klasycznym portalu Azure. | |Tak |Nie |
| 5 |Instalacja |Podczas instalacji programu SharePoint adapter StorSimple należy podać adres IP urządzenia w celu pomyślnego zakończenia instalacji. | |Tak |Nie |
| 6 |Serwer proxy sieci Web |Jeśli konfiguracja serwera proxy sieci Web ma HTTPS jako określony protokół, wpłynie to na komunikację z urządzeniem do usługi, a urządzenie przejdzie w tryb offline. Pakiety pomocy technicznej zostaną również wygenerowane w procesie, zużywając znaczące zasoby na urządzeniu. |Upewnij się, że adres URL serwera proxy sieci Web ma wartość HTTP jako określony protokół. Aby uzyskać więcej informacji, zobacz temat [Konfigurowanie serwera proxy sieci Web dla urządzenia](./storsimple-8000-configure-web-proxy.md). |Tak |Nie |
| 7 |Serwer proxy sieci Web |Jeśli skonfigurujesz i włączysz serwer proxy sieci Web na zarejestrowanym urządzeniu, należy ponownie uruchomić kontroler Active na urządzeniu. | |Tak |Nie |
| 8 |Duże opóźnienia w chmurze i duże obciążenie we/wy |Gdy urządzenie StorSimple napotyka kombinację bardzo dużych opóźnień w chmurze (kolejność sekund) i wysokiego obciążenia we/wy, woluminy urządzeń przechodzą w stan obniżonej wydajności, a w przypadku wystąpienia błędu "urządzenie nie jest gotowe" może zakończyć się niepowodzeniem. |Konieczne będzie ręczne ponowne uruchomienie kontrolerów urządzeń lub przełączenie w tryb failover urządzenia w celu odzyskania z tej sytuacji. |Tak |Nie |
| 9 |Azure PowerShell |W przypadku korzystania z polecenia cmdlet **Get-AzureStorSimpleStorageAccountCredential programu StorSimple &#124; Select-Object — pierwsza 1 — poczekaj** na wybranie pierwszego obiektu, aby można było utworzyć nowy obiekt **kontenerem volumecontainer** , polecenie cmdlet zwróci wszystkie obiekty. |Zawiń polecenie cmdlet w nawiasach w następujący sposób: **(Get-Azure-StorSimpleStorageAccountCredential) &#124; Select-Object-pierwsze 1 — oczekiwanie** |Tak |Tak |
| 10 |Migracja |W przypadku przekazywania wielu kontenerów woluminów do migracji, EZT dla najnowszej kopii zapasowej jest dokładne tylko dla pierwszego kontenera woluminów. Ponadto migracja równoległa rozpocznie się po wykonaniu pierwszych 4 kopii zapasowych w pierwszym kontenerze woluminu. |Zalecamy Migrowanie jednego kontenera woluminów jednocześnie. |Tak |Nie |
| 11 |Migracja |Po przywróceniu woluminy nie są dodawane do zasad tworzenia kopii zapasowych ani do grupy dysków wirtualnych. |Aby utworzyć kopie zapasowe, należy dodać te woluminy do zasad kopii zapasowych. |Tak |Tak |
| 12 |Migracja |Po zakończeniu migracji urządzenie serii 5000/7000 nie może uzyskać dostępu do zmigrowanych kontenerów danych. |Zalecamy usunięcie zmigrowanych kontenerów danych po zakończeniu migracji i zatwierdzeniu. |Tak |Nie |
| 13 |Klonowanie i odzyskiwanie po awarii |Urządzenie StorSimple z aktualizacją Update 1 nie może sklonować ani wykonać odzyskiwania po awarii na urządzeniu, na którym jest uruchomione oprogramowanie sprzed aktualizacji 1. |Musisz zaktualizować urządzenie docelowe, aby zaktualizować 1, aby zezwolić na te operacje |Tak |Tak |
| 14 |Migracja |Kopia zapasowa konfiguracji dla migracji może zakończyć się niepowodzeniem na urządzeniu z serii 5000-7000, jeśli istnieją grupy woluminów bez skojarzonych woluminów. |Usuń wszystkie puste grupy woluminów bez skojarzonych woluminów, a następnie ponów próbę wykonania kopii zapasowej konfiguracji. |Tak |Nie |
| 15 |Azure PowerShell polecenia cmdlet i woluminy przypięte lokalnie |Nie można utworzyć woluminu przypiętego lokalnie za pomocą poleceń cmdlet Azure PowerShell. (Każdy wolumin tworzony za pośrednictwem Azure PowerShell będzie warstwowy). |Należy zawsze używać usługi StorSimple Manager, aby konfigurować woluminy przypięte lokalnie. |Tak |Nie |
| 16 |Dostępne miejsce dla woluminów przypiętych lokalnie |Jeśli usuniesz wolumin przypięty lokalnie, miejsce dostępne dla nowych woluminów może nie zostać natychmiast zaktualizowane. Usługa StorSimple Manager aktualizuje miejsce lokalne dostępne co godzinę. |Poczekaj godzinę przed podjęciem próby utworzenia nowego woluminu. |Tak |Nie |
| 17 |Lokalnie przypięte woluminy |Zadanie przywracania udostępnia tymczasową kopię zapasową migawki w wykazie kopii zapasowych, ale tylko przez czas trwania zadania przywracania. Ponadto udostępnia grupę dysków wirtualnych z prefiksem **tmpCollection** na stronie **zasady tworzenia kopii zapasowych** , ale tylko w przypadku czasu trwania zadania przywracania. |Takie zachowanie może wystąpić, jeśli zadanie przywracania ma tylko woluminy przypięte lokalnie lub mieszaninę przypiętą lokalnie i woluminy warstwowe. Jeśli zadanie przywracania obejmuje tylko woluminy warstwowe, to zachowanie nie zostanie wykonane. Interwencja użytkownika nie jest wymagana. |Tak |Nie |
| 18 |Lokalnie przypięte woluminy |W przypadku anulowania zadania przywracania i przejścia w tryb failover kontrolera następuje natychmiast później, zadanie przywracania wyświetli **błąd** , a nie **anulowane**. Jeśli zadanie przywracania zakończy się niepowodzeniem, a przejście w tryb failover kontrolera następuje natychmiast po raz, zadanie przywracania będzie wyświetlało **anulowane** , a **nie zakończone powodzeniem**. |Takie zachowanie może wystąpić, jeśli zadanie przywracania ma tylko woluminy przypięte lokalnie lub mieszaninę przypiętą lokalnie i woluminy warstwowe. Jeśli zadanie przywracania obejmuje tylko woluminy warstwowe, to zachowanie nie zostanie wykonane. Interwencja użytkownika nie jest wymagana. |Tak |Nie |
| 19 |Lokalnie przypięte woluminy |Jeśli anulujesz zadanie przywracania lub jeśli przywracanie nie powiedzie się, a następnie nastąpi przejście do trybu failover kontrolera, na stronie **zadania** zostanie wyświetlone dodatkowe zadanie przywracania. |Takie zachowanie może wystąpić, jeśli zadanie przywracania ma tylko woluminy przypięte lokalnie lub mieszaninę przypiętą lokalnie i woluminy warstwowe. Jeśli zadanie przywracania obejmuje tylko woluminy warstwowe, to zachowanie nie zostanie wykonane. Interwencja użytkownika nie jest wymagana. |Tak |Nie |
| 20 |Lokalnie przypięte woluminy |Jeśli spróbujesz skonwertować wolumin warstwowy (utworzony i sklonowany z aktualizacją 1,2 lub wcześniejszy) na wolumin przypięty lokalnie, a na urządzeniu kończy się wolne miejsce lub wystąpiła awaria w chmurze, klony mogą być uszkodzone. |Ten problem występuje tylko w przypadku woluminów, które zostały utworzone i sklonowane przy użyciu oprogramowania sprzed aktualizacji 2,1. Powinien to być rzadko występujący scenariusz. | | |
| 21 |Konwersja woluminu |Nie należy aktualizować rekordami ACR dołączonego do woluminu, podczas gdy konwersja woluminu jest w toku (warstwowo przypięte lub odwrotnie). Aktualizacja rekordami ACR może spowodować uszkodzenie danych. |W razie potrzeby zaktualizuj rekordami ACR przed konwersją woluminu i nie wykonuj żadnych dalszych aktualizacji ACR, gdy konwersja jest w toku. | | |

## <a name="controller-and-firmware-updates-in-update-22"></a>Aktualizacje kontrolera i oprogramowania układowego w aktualizacji 2,2
Ta wersja ma aktualizacje wyłącznie oprogramowania. Jeśli jednak aktualizujesz wersję programu przed aktualizacją Update 2, należy zainstalować sterowniki, Storport, Spaceport i (w niektórych przypadkach) aktualizacje oprogramowania układowego dysku na urządzeniu.

Aby uzyskać więcej informacji na temat instalowania aktualizacji sterowników, sterownika Storport, Spaceport i oprogramowania układowego dysku, zobacz [Instalowanie aktualizacji 2,2](./storsimple-8000-install-update-5.md) na urządzeniu StorSimple.

## <a name="virtual-device-updates-in-update-22"></a>Aktualizacje urządzeń wirtualnych w aktualizacji 2,2
Tej aktualizacji nie można zastosować do urządzenia wirtualnego. Należy utworzyć nowe urządzenia wirtualne. 

## <a name="next-step"></a>Następny krok
Dowiedz się, jak [zainstalować aktualizację 2,2](./storsimple-8000-install-update-5.md) na urządzeniu StorSimple.