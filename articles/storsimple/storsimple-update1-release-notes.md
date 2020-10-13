---
title: StorSimple 8000 Series Update 1,2 — informacje o wersji | Microsoft Docs
description: W tym artykule opisano nowe funkcje, problemy i Obejścia dotyczące aktualizacji z serii StorSimple 8000 1,2.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 6c9aae87-6f77-44b8-b7fa-ebbdc9d8517c
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 17e20048c4cb4de2be6fe36be100b472f0b8ee73
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89459967"
---
# <a name="update-12-release-notes-for-your-storsimple-8000-series-device"></a>Aktualizacja 1,2 — informacje o wersji dla urządzenia z serii StorSimple 8000

## <a name="overview"></a>Omówienie
W poniższych informacjach o wersji opisano nowe funkcje i zidentyfikowanie krytycznych problemów otwartych dla aktualizacji z serii StorSimple 8000 1,2. Zawierają także listę aktualizacji oprogramowania StorSimple, sterowników i oprogramowania układowego dysku zawartego w tej wersji. 

Aktualizacja 1,2 może zostać zastosowana do wszystkich urządzeń StorSimple z systemem (GA), aktualizacji 0,1, Update 0,2 lub aktualizacji oprogramowania 0,3. Aktualizacja 1,2 jest niedostępna, jeśli na urządzeniu jest uruchomiona Aktualizacja Update 1 lub aktualizacja 1,1. Jeśli na urządzeniu jest uruchomiona wersja Release (GA), [skontaktuj się z firmą pomoc techniczna firmy Microsoft](storsimple-contact-microsoft-support.md) , aby uzyskać pomoc w instalacji tej aktualizacji.

W poniższej tabeli wymieniono wersje oprogramowania urządzenia odpowiednie dla aktualizacji 1, 1,1 i 1,2.

| Jeśli uruchomiono aktualizację... | to jest wersja oprogramowania urządzenia. |
| --- | --- |
| Aktualizacja 1.2 |6.3.9600.17584 |
| Aktualizacja 1.1 |6.3.9600.17521 |
| Aktualizacja 1,0 |6.3.9600.17491 |

Przed wdrożeniem aktualizacji w rozwiązaniu StorSimple zapoznaj się z informacjami zawartymi w informacjach o wersji. Aby uzyskać więcej informacji, zobacz jak [zainstalować aktualizację 1,2 na urządzeniu StorSimple](storsimple-install-update-1.md). 

> [!IMPORTANT]
> * Zainstalowanie tej aktualizacji (w tym aktualizacji systemu Windows) trwa około 5-10 godzin. 
> * Aktualizacja 1,2 zawiera oprogramowanie, sterownik LSI i aktualizacje oprogramowania układowego dysku. Aby zainstalować program, postępuj zgodnie z instrukcjami w temacie [Install Update 1,2 na urządzeniu StorSimple](storsimple-install-update-1.md).
> * W przypadku nowych wersji aktualizacje mogą nie być od razu widoczne, ponieważ przeprowadzamy stopniowe wdrażanie aktualizacji. Skanuj w poszukiwaniu aktualizacji w ciągu kilku dni ponownie, ponieważ staną się wkrótce dostępne.
> 
> 

## <a name="whats-new-in-update-12"></a>Co nowego w aktualizacji Update 1,2
Te funkcje zostały po raz pierwszy wydane z aktualizacją Update 1, która została udostępniona dla ograniczonego zestawu użytkowników. W wersji Update 1,2 większość użytkowników StorSimple zobaczy następujące nowe funkcje i ulepszenia:

* **Migracja z serii 5000-7000 do 8000 urządzeń serii** — w tej wersji wprowadzono nową funkcję migracji, która umożliwia użytkownikom urządzenia z serii StorSimple 5000-7000 ich dane na urządzeniu fizycznym z serii StorSimple 8000 lub urządzeniem wirtualnym. Funkcja migracji ma dwie pozycje wartości klucza:                                                                  
  
  * **Ciągłość**działania, umożliwiając Migrowanie istniejących danych z urządzeń z serii 5000-7000 do 8000 urządzeń serii.
  * **Udoskonalone oferty funkcji urządzeń z serii 8000**, takie jak wydajne scentralizowane zarządzanie wieloma urządzeniami za pomocą usługi StorSimple Manager, lepsza Klasa sprzętu i zaktualizowane oprogramowanie układowe, urządzenia wirtualne, mobilność danych i funkcje w przyszłości.
    
    Zapoznaj się z [przewodnikiem migracji](https://gallery.technet.microsoft.com/Azure-StorSimple-50007000-c1a0460b) , aby uzyskać szczegółowe informacje na temat migrowania serii StorSimple 5000-7000 do urządzenia z serii 8000. 
* **Dostępność w portalu Azure Government** — StorSimple jest teraz dostępna w portalu Azure Government. Zobacz jak [wdrożyć urządzenie StorSimple w portalu Azure Government](storsimple-deployment-walkthrough-gov.md).
* **Obsługa innych dostawców usług w chmurze** — inni dostawcy usług w chmurze obsługują usługi Amazon S3, Amazon S3 z rekordami RR, HP i OpenStack (beta).
* **Aktualizacja do najnowszych interfejsów API magazynu** — w tym wydaniu StorSimple został zaktualizowany do najnowszych interfejsów API usługi Azure Storage. Urządzenia z serii 8000 StorSimple, które są uruchomione przed aktualizacją 1 wersje oprogramowania (Release, 0,1, 0,2 i 0,3), korzystają z wersji interfejsów API usługi Azure Storage starszej niż 17 lipca 2009. Zgodnie z opisem w zaktualizowanym [ogłoszeniu dotyczącym usuwania wersji usługi Storage](https://docs.microsoft.com/archive/blogs/windowsazurestorage/microsoft-azure-storage-service-version-removal-update-extension-to-2016), 1 sierpnia 2016, te interfejsy API będą przestarzałe. Konieczne jest zastosowanie aktualizacji StorSimple 8000 serii 1 przed 1 sierpnia 2016. Jeśli wykonanie tej czynności nie powiedzie się, urządzenia StorSimple przestaną działać poprawnie.
* **Obsługa magazynu Strefowo nadmiarowego (ZRS)** — wraz z uaktualnieniem do najnowszej wersji interfejsów API usługi Storage seria StorSimple 8000 będzie obsługiwała magazyn strefowo nadmiarowy (ZRS), a także Magazyn lokalnie nadmiarowy (LRS) i magazyn Geograficznie nadmiarowy (GRS). Zapoznaj się z tym [artykułem dotyczącym opcji nadmiarowości usługi Azure Storage](../storage/common/storage-redundancy.md) , aby uzyskać szczegółowe informacje o ZRS.
* **Ulepszone wstępne wdrożenie i aktualizacja** — w tej wersji udoskonalono procesy instalacji i aktualizacji. Instalacja za pomocą Kreatora instalacji została ulepszona w celu podania opinii użytkownikowi, jeśli ustawienia konfiguracji sieci i zapory są nieprawidłowe. Udostępniono dodatkowe polecenia cmdlet diagnostyczne, które ułatwiają rozwiązywanie problemów dotyczących sieci urządzeń. Aby uzyskać więcej informacji na temat nowych poleceń cmdlet diagnostycznych używanych do rozwiązywania problemów, zobacz artykuł dotyczący [rozwiązywania problemów](storsimple-troubleshoot-deployment.md) .

## <a name="issues-fixed-in-update-12"></a>Problemy rozwiązane w aktualizacji 1,2
Poniższa tabela zawiera podsumowanie problemów, które zostały rozwiązane w aktualizacjach 1,2, 1,1 i 1.    

| Nie. | Cecha | Problem | Rozwiązano w aktualizacji | Dotyczy urządzenia fizycznego | Dotyczy urządzenia wirtualnego |
| --- | --- | --- | --- | --- | --- |
| 1 |program Windows PowerShell dla usługi StorSimple |Gdy użytkownik zdalnie uzyskał dostęp do urządzenia StorSimple przy użyciu program Windows PowerShell dla usługi StorSimple a następnie uruchomił kreatora instalacji, wystąpił błąd, gdy tylko dane wejściowe protokołu IP 0. Ta usterka została teraz naprawiona w aktualizacji Update 1. |Update 1 |Tak |Tak |
| 2 |Resetowanie do ustawień fabrycznych |W niektórych przypadkach po przeprowadzeniu resetowania do ustawień fabrycznych urządzenie StorSimple zostało zablokowane i zostanie wyświetlony komunikat: **Resetowanie do fabryki jest w toku (faza 8)**. Ten proces wystąpił po naciśnięciu klawiszy CTRL + C, gdy polecenie cmdlet było w toku. Ta usterka została naprawiona. |Update 1 |Tak |Nie |
| 3 |Resetowanie do ustawień fabrycznych |Po nieudanym zresetowaniu do ustawień fabrycznych podwójnego kontrolera można kontynuować rejestrację urządzenia. Spowodowało to nieobsługiwaną konfigurację systemu. W aktualizacji Update 1 zostanie wyświetlony komunikat o błędzie, a rejestracja jest blokowana na urządzeniu, na którym wystąpiło niepowodzenie resetowania do ustawień fabrycznych. |Update 1 |Tak |Nie |
| 4 |Resetowanie do ustawień fabrycznych |W niektórych przypadkach zostały zgłoszone fałszywe alerty niezgodności. Nieprawidłowe alerty niezgodności nie będą już generowane na urządzeniach z aktualizacją Update 1. |Update 1 |Tak |Nie |
| 5 |Resetowanie do ustawień fabrycznych |Jeśli Resetowanie do ustawień fabrycznych zostało przerwane przed ukończeniem, urządzenie przeszedł w tryb odzyskiwania i nie zezwolił na dostęp do program Windows PowerShell dla usługi StorSimple. Ta usterka została naprawiona. |Update 1 |Tak |Nie |
| 6 |Odzyskiwanie po awarii |Rozwiązano problem z odzyskiwaniem po awarii (DR), w którym program DR może zakończyć się niepowodzeniem podczas odnajdywania kopii zapasowych na urządzeniu docelowym. |Update 1 |Tak |Tak |
| 7 |Monitorowanie diod LED |W niektórych przypadkach diody monitorowania z tyłu urządzenia nie wskazują prawidłowego stanu. Niebieska dioda LED została wyłączona. Diody LED DATA 0 i DATA 1 zostały migane nawet wtedy, gdy te interfejsy nie zostały skonfigurowane. Problem został rozwiązany, a diody monitorowania są teraz wskazywane na prawidłowy stan. |Update 1 |Tak |Nie |
| 8 |Monitorowanie diod LED |W niektórych wystąpieniach, po zastosowaniu aktualizacji Update 1, niebieskie światło na aktywnym kontrolerze zostało wyłączone, co utrudnia zidentyfikowanie aktywnego kontrolera. Ten problem został rozwiązany w tej wersji poprawki. |Aktualizacja 1.2 |Tak |Nie |
| 9 |Interfejsy sieciowe |W poprzednich wersjach Urządzenie StorSimple skonfigurowane z bramą bez obsługi routingu może przejść do trybu offline. W tej wersji Metryka routingu dla danych 0 była najniższa; w związku z tym nawet jeśli inne interfejsy sieciowe są obsługiwane w chmurze, cały ruch w chmurze z urządzenia będzie kierowany przez dane 0. |Update 1 |Tak |Tak |
| 10 |Tworzenie kopii zapasowych |Usterka w aktualizacji Update 1, która spowodowała niepowodzenie wykonywania kopii zapasowych po 24 dniach w aktualizacji poprawek 1,1. |Aktualizacja 1.1 |Tak |Tak |
| 11 |Tworzenie kopii zapasowych |Usterka w poprzednich wersjach spowodowała niską wydajność migawek w chmurze z niską szybkością zmian. Ta usterka została rozwiązana w tej wersji poprawki. |Aktualizacja 1.2 |Tak |Tak |
| 12 |Aktualizacje |Usterka w aktualizacji Update 1, która zgłosiła nieudane uaktualnienie i spowodowała, że kontrolery przechodzą w tryb odzyskiwania, została rozwiązana w tej wersji poprawki. |Aktualizacja 1.2 |Tak |Tak |

## <a name="known-issues-in-update-12"></a>Znane problemy w aktualizacji 1,2
Poniższa tabela zawiera podsumowanie znanych problemów występujących w tej wersji.

| Nie. | Cecha | Problem | Komentarze/obejście | Dotyczy urządzenia fizycznego | Dotyczy urządzenia wirtualnego |
| --- | --- | --- | --- | --- | --- |
| 1 |Kworum dysku |W rzadkich przypadkach, jeśli większość dysków w obudowie EBOD urządzenia 8600 zostanie odłączona, co spowodowało brak kworum dysku, Pula magazynów będzie w trybie offline. Pozostanie ono w trybie offline, nawet jeśli dyski zostaną ponownie połączone. |Konieczne będzie ponowne uruchomienie urządzenia. Jeśli problem będzie się powtarzać, skontaktuj się z firmą pomoc techniczna firmy Microsoft w celu wykonania następnych kroków. |Tak |Nie |
| 2 |Niepoprawny identyfikator kontrolera |Gdy jest przeprowadzane Zastępowanie kontrolera, kontroler 0 może być wyświetlany jako kontroler 1. Podczas zastępowania kontrolera, gdy obraz jest ładowany z węzła równorzędnego, identyfikator kontrolera może początkowo być wyświetlany jako identyfikator kontrolera równorzędnego. W rzadkich przypadkach takie zachowanie może być również widoczne po ponownym uruchomieniu systemu. |Nie jest wymagane wykonanie jakiejkolwiek czynności przez użytkownika. Ta sytuacja zostanie rozwiązany po zakończeniu zastępowania kontrolera. |Tak |Nie |
| 3 |Konta magazynu |Użycie usługi Storage w celu usunięcia konta magazynu jest nieobsługiwanym scenariuszem. Spowoduje to powstanie sytuacji, w której nie można pobrać danych użytkownika. |Tak |Tak | |
| 4 |Tryb failover urządzenia |Wiele przełączeń w tryb failover kontenera woluminów z tego samego urządzenia źródłowego do różnych urządzeń docelowych nie jest obsługiwane. Przełączenie urządzenia do trybu failover z pojedynczego martwego urządzenia na wielu urządzeniach spowoduje utratę własności danych przez kontenery woluminów na urządzeniu. Po przejściu do trybu failover te kontenery woluminów będą wyświetlane lub zachowywać się inaczej po wyświetleniu ich w klasycznym portalu Azure. | |Tak |Nie |
| 5 |Instalacja |Podczas instalacji programu SharePoint adapter StorSimple należy podać adres IP urządzenia w celu pomyślnego zakończenia instalacji. | |Tak |Nie |
| 6 |Serwer proxy sieci Web |Jeśli konfiguracja serwera proxy sieci Web ma HTTPS jako określony protokół, wpłynie to na komunikację z urządzeniem do usługi, a urządzenie przejdzie w tryb offline. Pakiety pomocy technicznej zostaną również wygenerowane w procesie, zużywając znaczące zasoby na urządzeniu. |Upewnij się, że adres URL serwera proxy sieci Web ma wartość HTTP jako określony protokół. Aby uzyskać więcej informacji, zobacz temat [Konfigurowanie serwera proxy sieci Web dla urządzenia](storsimple-configure-web-proxy.md). |Tak |Nie |
| 7 |Serwer proxy sieci Web |Jeśli skonfigurujesz i włączysz serwer proxy sieci Web na zarejestrowanym urządzeniu, należy ponownie uruchomić kontroler Active na urządzeniu. | |Tak |Nie |
| 8 |Duże opóźnienia w chmurze i duże obciążenie we/wy |Gdy urządzenie StorSimple napotyka kombinację bardzo dużych opóźnień w chmurze (kolejność sekund) i wysokiego obciążenia we/wy, woluminy urządzeń przechodzą w stan obniżonej wydajności, a w przypadku wystąpienia błędu "urządzenie nie jest gotowe" może zakończyć się niepowodzeniem. |Konieczne będzie ręczne ponowne uruchomienie kontrolerów urządzeń lub przełączenie w tryb failover urządzenia w celu odzyskania z tej sytuacji. |Tak |Nie |
| 9 |Azure PowerShell |W przypadku korzystania z polecenia cmdlet **Get-AzureStorSimpleStorageAccountCredential programu StorSimple &#124; Select-Object — pierwsza 1 — poczekaj** na wybranie pierwszego obiektu, aby można było utworzyć nowy obiekt **kontenerem volumecontainer** , polecenie cmdlet zwróci wszystkie obiekty. |Zawiń polecenie cmdlet w nawiasach w następujący sposób: **(Get-Azure-StorSimpleStorageAccountCredential) &#124; Select-Object-pierwsze 1 — oczekiwanie** |Tak |Tak |
| 10 |Migracja |W przypadku przekazywania wielu kontenerów woluminów do migracji, EZT dla najnowszej kopii zapasowej jest dokładne tylko dla pierwszego kontenera woluminów. Ponadto migracja równoległa rozpocznie się po wykonaniu pierwszych 4 kopii zapasowych w pierwszym kontenerze woluminu. |Zalecamy Migrowanie jednego kontenera woluminów jednocześnie. |Tak |Nie |
| 11 |Migracja |Po przywróceniu woluminy nie są dodawane do zasad tworzenia kopii zapasowych ani do grupy dysków wirtualnych. |Aby utworzyć kopie zapasowe, należy dodać te woluminy do zasad kopii zapasowych. |Tak |Tak |
| 12 |Migracja |Po zakończeniu migracji urządzenie serii 5000/7000 nie może uzyskać dostępu do zmigrowanych kontenerów danych. |Zalecamy usunięcie zmigrowanych kontenerów danych po zakończeniu migracji i zatwierdzeniu. |Tak |Nie |
| 13 |Klonowanie i odzyskiwanie po awarii |Urządzenie StorSimple z aktualizacją Update 1 nie może sklonować ani wykonać odzyskiwania po awarii na urządzeniu, na którym jest uruchomione oprogramowanie sprzed aktualizacji 1. |Musisz zaktualizować urządzenie docelowe, aby zaktualizować 1, aby zezwolić na te operacje |Tak |Tak |
| 14 |Migracja |Kopia zapasowa konfiguracji dla migracji może zakończyć się niepowodzeniem na urządzeniu z serii 5000-7000, jeśli istnieją grupy woluminów bez skojarzonych woluminów. |Usuń wszystkie puste grupy woluminów bez skojarzonych woluminów, a następnie ponów próbę wykonania kopii zapasowej konfiguracji. |Tak |Nie |

## <a name="physical-device-updates-in-update-12"></a>Aktualizacje urządzeń fizycznych w aktualizacji 1,2
Jeśli aktualizacja aktualizacji 1,2 zostanie zastosowana do urządzenia fizycznego (z uruchomionymi wersjami wcześniejszymi niż aktualizacja 1), wersja oprogramowania zmieni się na 6.3.9600.17584.

## <a name="controller-and-firmware-updates-in-update-12"></a>Aktualizacje kontrolera i oprogramowania układowego w aktualizacji 1,2
W tej wersji Zaktualizowano sterownik i oprogramowanie układowe dysku na urządzeniu.

* Aby uzyskać więcej informacji na temat aktualizacji kontrolera SAS, zobacz [Aktualizacja Update 1 dla kontrolerów LSI SAS na urządzeniu Microsoft Azure StorSimple](https://support.microsoft.com/kb/3043005). 
* Aby uzyskać więcej informacji na temat aktualizacji oprogramowania układowego dysku, zapoznaj się z artykułem [oprogramowanie układowe aktualizacji 1 dla Microsoft Azure StorSimple urządzenia](https://support.microsoft.com/kb/3063416).

## <a name="virtual-device-updates-in-update-12"></a>Aktualizacje urządzeń wirtualnych w aktualizacji 1,2
Tej aktualizacji nie można zastosować do urządzenia wirtualnego. Należy utworzyć nowe urządzenia wirtualne. 

## <a name="next-steps"></a>Następne kroki
* [Zainstaluj aktualizację 1,2 na urządzeniu](storsimple-install-update-1.md).

