---
title: StorSimple 8000 Series Update 2 — informacje o wersji | Microsoft Docs
description: Opisuje nowe funkcje, problemy i obejścia dla StorSimple 8000 serii Update 2.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: e2c8bffd-7fc5-4b77-b632-a4f59edacc3a
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.openlocfilehash: b68e338c2675a3aeefda390d12ab2d82ea1c7a9f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94956657"
---
# <a name="storsimple-8000-series-update-2-release-notes"></a>StorSimple 8000 Series Update 2 — informacje o wersji

## <a name="overview"></a>Omówienie
W poniższych informacjach o wersji opisano nowe funkcje i zidentyfikowanie krytycznych problemów otwartych dla StorSimple 8000 serii Update 2. Znajdują się w nich również listy aktualizacji oprogramowania StorSimple, sterowników i oprogramowanie układowe dysku zawarte w tej wersji. 

Aktualizację Update 2 można zastosować do wszystkich urządzeń StorSimple z systemem lub aktualizacji 0,1 za poorednictwem aktualizacji 1,2. Wersja urządzenia skojarzona z aktualizacją Update 2 to 6.3.9600.17673.

Przed wdrożeniem aktualizacji w rozwiązaniu StorSimple zapoznaj się z informacjami zawartymi w informacjach o wersji.

> [!IMPORTANT]
> * Zainstalowanie tej aktualizacji (w tym aktualizacji systemu Windows) trwa około 4-7 godzin. 
> * Aktualizacja Update 2 ma aktualizacje oprogramowania układowego, sterownika LSI i dysku SSD.
> * W przypadku nowych wersji aktualizacje mogą nie być od razu widoczne, ponieważ przeprowadzamy stopniowe wdrażanie aktualizacji. Poczekaj kilka dni, a następnie ponownie przeprowadź skanowanie w poszukiwaniu aktualizacji, ponieważ staną się one dostępne wkrótce.
> 
> 

## <a name="whats-new-in-update-2"></a>Co nowego w aktualizacji Update 2
Aktualizacja Update 2 wprowadza następujące nowe funkcje.

* **Woluminy przypięte lokalnie** — w poprzednich wersjach serii StorSimple 8000, bloki danych zostały warstwowe w chmurze na podstawie użycia. Nie ma możliwości zagwarantowania, że bloki będą przechowywane lokalnie. W wersji Update 2, podczas tworzenia woluminu można wyznaczyć wolumin jako przypięty lokalnie, a dane podstawowe z tego woluminu nie zostaną warstwowe w chmurze. Migawki woluminów przypiętych lokalnie będą nadal kopiowane do chmury na potrzeby kopii zapasowych, dzięki czemu Chmura może być używana do przenoszenia danych i odzyskiwania po awarii. Ponadto można zmienić typ woluminu (czyli przekonwertować woluminy warstwowe na woluminy przypięte lokalnie i przekonwertować woluminy przypięte lokalnie na warstwowe). 
* **Udoskonalenia urządzenia wirtualnego StorSimple** — wcześniej seria StorSimple 8000 została umieszczona na urządzeniu wirtualnym jako odzyskiwanie po awarii lub rozwiązanie do tworzenia i testowania aplikacji. Istnieje tylko jeden model urządzenia wirtualnego (model 1100). Aktualizacja 2 wprowadza dwa modele urządzeń wirtualnych: 
  
  * 8010 (dawniej nazywany 1100) — bez zmian; ma pojemność 30 TB i używa usługi Azure Standard Storage.
  * 8020 — ma pojemność 64 TB i używa usługi Azure Premium Storage w celu zwiększenia wydajności.
    
    Istnieje jeden wirtualny dysk twardy dla modeli urządzeń wirtualnych (8010/8020). Po pierwszym uruchomieniu urządzenie wirtualne wykrywa parametry platformy i stosuje poprawną wersję modelu.
* **Udoskonalenia sieci** — Aktualizacja Update 2 zawiera następujące udoskonalenia sieci:
  
  * Można włączyć obsługę wielu kart sieciowych w chmurze, aby umożliwić pracę w trybie failover w przypadku awarii karty sieciowej.
  * Ulepszenia routingu ze stałymi metrykami dla bloków obsługujących chmurę.
  * Ponowienie online zasobów zakończonych niepowodzeniem przed przejściem w tryb failover.
  * Nowe alerty dotyczące błędów usługi.
* **Aktualizowanie ulepszeń** — w aktualizacji 1,2 i starszych seria StorSimple 8000 została zaktualizowana za pośrednictwem dwóch kanałów: Windows Update do klastrowania, iSCSI i tak dalej, a Microsoft Update dla plików binarnych i oprogramowania układowego.
    Aktualizacja Update 2 używa Microsoft Update dla wszystkich pakietów aktualizacji. Powinno to prowadzić do mniejszego zastosowania poprawek lub przełączenia w tryb failover. 
* **Aktualizacje oprogramowania układowego** — uwzględniono następujące aktualizacje oprogramowania układowego:
  
  * LSI: lsi_sas2.sys 2.00.72.10 wersji produktu
  * Tylko dysk SSD (bez aktualizacji dysku twardego): XMGG, XGEG, KZ50, F6C2 i VR08
* **Proaktywna pomoc techniczna** — Aktualizacja Update 2 umożliwia firmie Microsoft ściąganie dodatkowych informacji diagnostycznych z urządzenia. Gdy zespół operacyjny zidentyfikuje urządzenia, na których występują problemy, lepiej jest zbierać informacje z urządzenia i diagnozować problemy. **Przyjmując aktualizację Update 2, możesz zapewnić nam tę pomoc techniczną**.    

## <a name="issues-fixed-in-update-2"></a>Problemy rozwiązane w aktualizacji Update 2
W poniższych tabelach przedstawiono podsumowanie problemów, które zostały rozwiązane w aktualizacji Update 2.    

| Nie. | Cecha | Problem | Dotyczy urządzenia fizycznego | Dotyczy urządzenia wirtualnego |
| --- | --- | --- | --- | --- |
| 1 |Interfejsy sieciowe |Po uaktualnieniu do wersji Update 1 Usługa StorSimple Manager zgłosiła, że porty Dane2 i DATA3 nie powiodły się na jednym kontrolerze. Ten problem został rozwiązany. |Tak |Nie |
| 2 |Aktualizacje |Po uaktualnieniu do wersji Update 1 alerty alarmu dźwiękowego wystąpiły w klasycznym portalu Azure na wielu urządzeniach. Ten problem został rozwiązany. |Tak |Nie |
| 3 |Uwierzytelnianie OpenStack |W przypadku korzystania z OpenStack jako dostawcy usług w chmurze może zostać wyświetlony komunikat o błędzie, że ciąg uwierzytelniania w chmurze był zbyt długi. Ten problem został rozwiązany. |Tak |Nie |

## <a name="known-issues-in-update-2"></a>Znane problemy z aktualizacją Update 2
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
| 20 |Lokalnie przypięte woluminy |Jeśli spróbujesz skonwertować wolumin warstwowy (utworzony i sklonowany z aktualizacją 1,2 lub wcześniejszy) na wolumin przypięty lokalnie, a na urządzeniu kończy się wolne miejsce lub wystąpiła awaria w chmurze, klony mogą być uszkodzone. |Ten problem występuje tylko w przypadku woluminów, które zostały utworzone i sklonowane przy użyciu oprogramowania sprzed aktualizacji Update 2. Powinien to być rzadko występujący scenariusz. | | |
| 21 |Konwersja woluminu |Nie należy aktualizować rekordami ACR dołączonego do woluminu, podczas gdy konwersja woluminu jest w toku (warstwowo przypięte lub odwrotnie). Aktualizacja rekordami ACR może spowodować uszkodzenie danych. |W razie potrzeby zaktualizuj rekordami ACR przed konwersją woluminu i nie wykonuj żadnych dalszych aktualizacji ACR, gdy konwersja jest w toku. | | |

## <a name="controller-and-firmware-updates-in-update-2"></a>Aktualizacje kontrolera i oprogramowania układowego w ramach aktualizacji Update 2
W tej wersji Zaktualizowano sterownik i oprogramowanie układowe dysku na urządzeniu.

* Aby uzyskać więcej informacji na temat aktualizacji oprogramowania układowego LSI, zobacz artykuł 3121900 w bazie wiedzy Microsoft Knowledge Base. 
* Aby uzyskać więcej informacji na temat aktualizacji oprogramowania układowego dysku, zobacz artykuł 3121899 w bazie wiedzy Microsoft Knowledge Base.

## <a name="virtual-device-updates-in-update-2"></a>Aktualizacje urządzeń wirtualnych w aktualizacji Update 2
Tej aktualizacji nie można zastosować do urządzenia wirtualnego. Należy utworzyć nowe urządzenia wirtualne. 

## <a name="next-step"></a>Następny krok
Dowiedz się, jak [zainstalować aktualizację Update 2](./storsimple-8000-install-update-5.md) na urządzeniu StorSimple.