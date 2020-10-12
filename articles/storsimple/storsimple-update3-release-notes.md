---
title: StorSimple 8000 Series Update 3 — informacje o wersji
description: Opisuje nowe funkcje, problemy i obejścia dla StorSimple 8000 serii Update 3.
author: alkohli
ms.assetid: 2158aa7a-4ac3-42ba-8796-610d1adb984d
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4b61caecd67881eb08c82ea0c26522c63c3e8396
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84712058"
---
# <a name="update-3-release-notes-for-your-storsimple-8000-series-device"></a>Update 3 — informacje o wersji dla urządzenia z serii StorSimple 8000

## <a name="overview"></a>Omówienie
W poniższych informacjach o wersji opisano nowe funkcje i zidentyfikowanie krytycznych problemów otwartych dla StorSimple 8000 serii Update 3. Zawierają także listę StorSimple aktualizacji oprogramowania zawartych w tej wersji. 

Aktualizację Update 3 można zastosować do wszystkich urządzeń StorSimple z systemem lub aktualizacji 0,1 za poorednictwem aktualizacji 2,2. Wersja urządzenia skojarzona z aktualizacją Update 3 to 6.3.9600.17759.

Przed wdrożeniem aktualizacji w rozwiązaniu StorSimple zapoznaj się z informacjami zawartymi w informacjach o wersji.

> [!IMPORTANT]
> * Aktualizacja Update 3 zawiera oprogramowanie urządzenia, sterownik LSI i oprogramowanie układowe oraz aktualizacje Storport i Spaceport. Zainstalowanie tej aktualizacji wymaga około 1,5 – 2 godzin. 
> * W przypadku nowych wersji aktualizacje mogą nie być od razu widoczne, ponieważ przeprowadzamy stopniowe wdrażanie aktualizacji. Poczekaj kilka dni, a następnie ponownie przeprowadź skanowanie w poszukiwaniu aktualizacji, ponieważ staną się one dostępne wkrótce.
> 
> 

## <a name="whats-new-in-update-3"></a>Co nowego w aktualizacji Update 3
W ramach aktualizacji Update 3 wprowadzono następujące ulepszenia dotyczące kluczy i poprawki błędów.

* **Automatyczne zmiany odzyskiwania miejsca** — począwszy od aktualizacji Update 3, algorytmy odzyskiwania miejsca są uruchamiane na kontrolerze gotowości systemu, co przyspiesza wykonanie. Aby uzyskać więcej informacji na temat portów, które są wymagane do pracy z odzyskiwaniem miejsca, zapoznaj się z [wymaganiami dotyczącymi sieci StorSimple](storsimple-8000-system-requirements.md#networking-requirements-for-your-storsimple-device).
* **Ulepszenia wydajności** — Aktualizacja Update 3 poprawiła wydajność odczytu i zapisu do chmury.
* **Ulepszenia związane z migracją** — w tej wersji wprowadzono kilka poprawek i ulepszeń błędów dla funkcji migracji z urządzeń z serii 5000/7000 do 8000. Aby uzyskać więcej informacji na temat korzystania z funkcji migracji, przejdź do obszaru [migracja z urządzenia z serii 5000/7000 do 8000](https://gallery.technet.microsoft.com/Azure-StorSimple-50007000-c1a0460b). 
* **Monitorowanie powiązanych poprawek** — w tej wersji rozwiązano błędy związane z wykresami monitorowania, pulpitem nawigacyjnym usług i pulpitem nawigacyjnym urządzeń.

## <a name="issues-fixed-in-update-3"></a>Problemy rozwiązane w aktualizacji Update 3
W poniższych tabelach przedstawiono podsumowanie problemów, które zostały rozwiązane w aktualizacji Update 3.    

| Nie | Promowanie | Problem | Dotyczy urządzenia fizycznego | Dotyczy urządzenia wirtualnego |
| --- | --- | --- | --- | --- |
| 1 |Migracja danych po stronie hosta |W starszej wersji urządzenie w chmurze StorSimple przechodzą w tryb offline podczas migracji danych po stronie hosta. Ten problem został rozwiązany w tej wersji. |Nie |Tak |
| 2 |Lokalnie przypięte woluminy |W poprzedniej wersji wystąpiły problemy związane z błędami we/wy, niepowodzeniami konwersji woluminów oraz błędami datapath dla woluminów przypiętych lokalnie. Te problemy były główne i rozwiązane w tej wersji. |Tak |Nie |
| 3 |Monitorowanie |Wystąpiły liczne problemy związane z jednostkami raportowania i monitorowaniem, a także wykresy pulpitów nawigacyjnych urządzeń, w których wyświetlane są nieprawidłowe informacje dla woluminów przypiętych lokalnie. Te problemy zostały rozwiązane w tej wersji. |Tak |Nie |
| 4 |Duże zapisy we/wy |W przypadku korzystania z StorSimple na potrzeby obciążeń obejmujących duże zapisy, użytkownik może działać w nierzadko występującej usterce, w której zestaw roboczy jest podzielony na dane w chmurze. Ta usterka została rozwiązana w tej wersji. |Tak |Tak |
| 5 |Backup |W niektórych rzadkich wystąpieniach w poprzednich wersjach oprogramowania, gdy użytkownik wykonał kopię zapasową klona zdalnego, może zostać uruchomiony na błędy w chmurze, a operacja spowodowałaby błąd. W tej wersji problem został rozwiązany, a operacja zostanie zakończona pomyślnie. |Tak |Tak |
| 6 |Zasady tworzenia kopii zapasowej |W niektórych rzadkich wystąpieniach we wcześniejszych wersjach oprogramowania Wystąpił błąd związany z usuwaniem zasad tworzenia kopii zapasowych. Ten problem został rozwiązany w tej wersji. |Tak |Tak |

## <a name="known-issues-in-update-3"></a>Znane problemy w aktualizacji Update 3
Poniższa tabela zawiera podsumowanie znanych problemów występujących w tej wersji.

| Nie. | Cecha | Problem | Komentarze/obejście | Dotyczy urządzenia fizycznego | Dotyczy urządzenia wirtualnego |
| --- | --- | --- | --- | --- | --- |
| 1 |Kworum dysku |W rzadkich przypadkach, jeśli większość dysków w obudowie EBOD urządzenia 8600 zostanie odłączona, co spowodowało brak kworum dysku, Pula magazynów przejdzie w tryb offline. Pozostanie ono w trybie offline, nawet jeśli dyski zostaną ponownie połączone. |Konieczne będzie ponowne uruchomienie urządzenia. Jeśli problem będzie się powtarzać, skontaktuj się z firmą pomoc techniczna firmy Microsoft w celu wykonania następnych kroków. |Tak |Nie |
| 2 |Niepoprawny identyfikator kontrolera |Gdy jest przeprowadzane Zastępowanie kontrolera, kontroler 0 może być wyświetlany jako kontroler 1. Podczas zastępowania kontrolera, gdy obraz jest ładowany z węzła równorzędnego, identyfikator kontrolera może początkowo być wyświetlany jako identyfikator kontrolera równorzędnego. W rzadkich przypadkach takie zachowanie może być również widoczne po ponownym uruchomieniu systemu. |Nie jest wymagane wykonanie jakiejkolwiek czynności przez użytkownika. Ta sytuacja zostanie rozwiązany po zakończeniu zastępowania kontrolera. |Tak |Nie |
| 3 |Konta magazynu |Użycie usługi Storage w celu usunięcia konta magazynu jest nieobsługiwanym scenariuszem. Spowoduje to powstanie sytuacji, w której nie można pobrać danych użytkownika. | |Tak |Tak |
| 4 |Tryb failover urządzenia |Wiele przełączeń w tryb failover kontenera woluminów z tego samego urządzenia źródłowego do różnych urządzeń docelowych nie jest obsługiwane. Przełączenie w tryb failover z pojedynczego martwego urządzenia do wielu urządzeń spowoduje utratę własności danych przez kontenery woluminów na urządzeniu z systemem. Po przejściu do trybu failover te kontenery woluminów będą wyświetlane lub zachowywać się inaczej po wyświetleniu ich w klasycznym portalu Azure. | |Tak |Nie |
| 5 |Instalacja |Podczas instalacji programu SharePoint adapter StorSimple należy podać adres IP urządzenia w celu pomyślnego zakończenia instalacji. | |Tak |Nie |
| 6 |Serwer proxy sieci Web |Jeśli konfiguracja serwera proxy sieci Web ma HTTPS jako określony protokół, wpłynie to na komunikację z urządzeniem do usługi, a urządzenie przejdzie w tryb offline. Pakiety pomocy technicznej zostaną również wygenerowane w procesie, zużywając znaczące zasoby na urządzeniu. |Upewnij się, że adres URL serwera proxy sieci Web ma wartość HTTP jako określony protokół. Aby uzyskać więcej informacji, zobacz temat [Konfigurowanie serwera proxy sieci Web dla urządzenia](storsimple-8000-configure-web-proxy.md). |Tak |Nie |
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
| 22 |Aktualizacje |W przypadku zastosowania aktualizacji Update 3 na stronie **Obsługa** w klasycznym portalu Azure zostanie wyświetlony następujący komunikat związany z aktualizacją Update 2 — "StorSimple 8000 Series Update 2" obejmuje możliwość, aby firma Microsoft mogła aktywnie zbierać informacje dziennika z urządzenia po wykryciu potencjalnych problemów ". Jest to mylące, ponieważ oznacza to, że urządzenie jest aktualizowane do aktualizacji Update 2. Po pomyślnym zaktualizowaniu urządzenia do aktualizacji 3 ten komunikat zniknie. |To zachowanie zostanie naprawione w przyszłym wydaniu. |Tak |Nie |

## <a name="controller-and-firmware-updates-in-update-3"></a>Aktualizacje kontrolera i oprogramowania układowego w ramach aktualizacji Update 3
Ta wersja ma aktualizacje sterownika LSI i oprogramowania układowego. Aby uzyskać więcej informacji na temat instalowania sterownika LSI i aktualizacji oprogramowania układowego, zobacz [Instalowanie aktualizacji Update 3](storsimple-install-update-3.md) na urządzeniu StorSimple.

## <a name="virtual-device-updates-in-update-3"></a>Aktualizacje urządzeń wirtualnych w aktualizacji Update 3
Tej aktualizacji nie można zastosować do urządzenia w chmurze StorSimple (nazywanego również urządzeniem wirtualnym). Należy utworzyć nowe urządzenia wirtualne. 

## <a name="next-step"></a>Następny krok
Dowiedz się, jak [zainstalować aktualizację Update 3](storsimple-install-update-3.md) na urządzeniu StorSimple.

