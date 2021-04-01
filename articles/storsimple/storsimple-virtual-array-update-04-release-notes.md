---
title: StorSimple Virtual Array Update 0,4 — Informacje o wersji | Microsoft Docs
description: Opisuje krytyczne problemy i rozwiązania dla StorSimple wirtualnej macierzy z uruchomioną aktualizacją 0,4.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/05/2017
ms.author: alkohli
ms.openlocfilehash: 392e930dcb69392b6f9425abba0efa516d5c82b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94967061"
---
# <a name="storsimple-virtual-array-update-04-release-notes"></a>StorSimple Virtual Array Update 0,4 — Informacje o wersji

## <a name="overview"></a>Omówienie

Poniższe informacje o wersji dotyczą krytycznych problemów otwartych i rozwiązanych problemów dotyczących Microsoft Azure StorSimple aktualizacji macierzy wirtualnych.

Informacje o wersji są stale aktualizowane i wykryto krytyczne problemy wymagające obejścia. Przed wdrożeniem macierzy wirtualnej StorSimple należy uważnie zapoznać się z informacjami znajdującymi się w informacjach o wersji.

Aktualizacja 0,4 jest zgodna z wersją oprogramowania **10.0.10289.0**.

> [!NOTE]
> Aktualizacje są zakłócone i ponownie uruchamiają urządzenie. Jeśli we/wy jest w toku, urządzenie powoduje przestoje.


## <a name="whats-new-in-the-update-04"></a>Co nowego w aktualizacji 0,4
Aktualizacja 0,4 jest przede wszystkim błędem kompilacja poprawki z kilkoma ulepszeniami. W tej wersji zostały rozkierowane pewne błędy powodujące błędy kopii zapasowych w poprzedniej wersji. Główne ulepszenia i poprawki błędów są następujące:

- **Ulepszenia wydajności tworzenia kopii zapasowych** — w tej wersji wprowadzono kilka kluczowych ulepszeń, które zwiększają wydajność tworzenia kopii zapasowych. W związku z tym kopie zapasowe, które obejmują dużą liczbę plików, zobaczą znaczącą redukcję w czasie wykonywania dla pełnych i przyrostowych kopii zapasowych.

- **Ulepszona wydajność przywracania** — ta wersja zawiera ulepszenia, które znacząco zwiększają wydajność przywracania przy użyciu dużej liczby plików. W przypadku korzystania z 2-4 milionów plików zalecamy udostępnienie macierzy wirtualnej z 16 GB pamięci RAM, aby zobaczyć ulepszenia. W przypadku używania mniej niż 2 000 000 plików minimalnym wymaganiem dla maszyny wirtualnej jest 8 GB pamięci RAM.

- **Ulepszenia pakietu** — ulepszenia obejmują rejestrowanie w statystyce dotyczącej dysku, procesora, pamięci, sieci i chmury do pakietu pomocy technicznej w celu usprawnienia procesu diagnozowania/debugowania problemów z urządzeniami.

- **Ogranicz lokalnie przypięte woluminy iSCSI do 200 GB** — dla woluminów przypiętych lokalnie zalecamy ograniczenie do woluminu iSCSI o pojemności 200 GB w macierzy wirtualnej StorSimple. Lokalna rezerwacja dla woluminów warstwowych nadal wynosi 10% rozmiaru woluminu, ale jest ograniczona do 200 GB. 

- **Poprawki dotyczące kopii zapasowej** — w poprzednich wersjach oprogramowania wystąpiły problemy związane z kopiami zapasowymi, które mogłyby spowodować błędy kopii zapasowych. Te usterki zostały rozkierowane w tej wersji.


## <a name="issues-fixed-in-the-update-04"></a>Problemy rozwiązane w aktualizacji 0,4

Poniższa tabela zawiera podsumowanie problemów rozwiązanych w tej wersji.

| Nie. | Cecha | Problem |
| --- | --- | --- |
| 1 |Wydajność kopii zapasowej|We wcześniejszych wersjach wykonywanie kopii zapasowych obejmujących dużą liczbę plików zajmie dużo czasu (w kolejności dni). W tej wersji zarówno pełne, jak i przyrostowe kopie zapasowe zobaczą znaczącą redukcję w czasie wykonywania. |
| 2 |Pakiet pomocy technicznej|Statystyki dysku, procesora CPU, pamięci, sieci i chmury są teraz zalogowane do dzienników pomocy technicznej, co sprawia, że pakiety pomocy technicznej są bardzo skuteczne w rozwiązywaniu problemów z urządzeniami.|
| 3 |Backup |W starszych wersjach, długotrwałe wykonywanie kopii zapasowych może spowodować przetwarzanie miejsca na urządzeniu, co spowodowało błędy kopii zapasowych. Ta usterka jest zadana w tej wersji, co umożliwia jednoczesne przeprowadzenie w kolejce maksymalnie 5 kopii zapasowych.|
| 4 |iSCSI | We wcześniejszych wersjach rezerwacja lokalna dla woluminów przypiętych warstwowo lub lokalnie wynosi 10% rozmiaru woluminu, który został zainicjowany. W tej wersji lokalna rezerwacja wszystkich woluminów iSCSI (przypiętych lokalnie lub warstwowych) jest ograniczona do 10% maksymalnie 200 GB (w przypadku woluminów warstwowych większych niż 2 TB), dzięki czemu Zwolnij więcej miejsca na dysku lokalnym. Zaleca się, aby woluminy przypięte lokalnie w tej wersji były ograniczone do 200 GB.|


## <a name="known-issues-in-the-update-04"></a>Znane problemy z aktualizacją 0,4

Poniższa tabela zawiera podsumowanie znanych problemów dotyczących macierzy wirtualnej StorSimple i obejmuje wydanie wydań z poprzednich wersji. 

| Nie. | Cecha | Problem | Obejście/Komentarze |
| --- | --- | --- | --- |
| **1.** |Aktualizacje |Nie można zaktualizować urządzeń wirtualnych utworzonych w wersji zapoznawczej do obsługiwanej wersji ogólnej dostępności. |Te urządzenia wirtualne muszą zostać przełączone w tryb failover w celu udostępnienia ogólnej wersji dostępności przy użyciu przepływu pracy odzyskiwania po awarii (DR). |
| **2.** |Dysk danych z zainicjowaną obsługą |Po zainicjowaniu obsługi dysku z danymi określonego rozmiaru i utworzeniu odpowiedniego urządzenia wirtualnego StorSimple nie należy rozwijać ani zmniejszać dysku z danymi. Próba wykonania spowoduje utratę wszystkich danych w warstwach lokalnych urządzenia. | |
| **3.** |Zasady grupy |Gdy urządzenie jest przyłączone do domeny, zastosowanie zasad grupy może niekorzystnie wpłynąć na działanie urządzenia. |Upewnij się, że tablica wirtualna ma własną jednostkę organizacyjną (OU) dla Active Directory i nie są do niej stosowane obiekty zasad grupy (GPO). |
| **4.** |Lokalny interfejs użytkownika sieci Web |Jeśli ulepszone funkcje zabezpieczeń są włączone w programie Internet Explorer (IE ESC), niektóre strony lokalnego interfejsu użytkownika sieci Web, takie jak Rozwiązywanie problemów lub konserwacja, mogą nie funkcjonować prawidłowo. Przyciski na tych stronach mogą również nie funkcjonować. |Wyłącz ulepszone funkcje zabezpieczeń w programie Internet Explorer. |
| **5000.** |Lokalny interfejs użytkownika sieci Web |W maszynie wirtualnej funkcji Hyper-V interfejsy sieciowe w interfejsie użytkownika sieci Web są wyświetlane jako interfejsy 10 GB/s. |To zachowanie jest odbiciem funkcji Hyper-V. Funkcja Hyper-V zawsze pokazuje 10 GB/s dla wirtualnych kart sieciowych. |
| **ust.** |Woluminy warstwowe lub udziały |Blokowanie zakresu bajtów dla aplikacji współpracujących z woluminami warstwowymi StorSimple nie jest obsługiwane. W przypadku włączenia blokowania zakresu bajtów nie działa StorSimple. |Zalecane miary obejmują: <br></br>Wyłącz blokowanie zakresu bajtów w logice aplikacji.<br></br>Wybierz opcję umieszczenia danych dla tej aplikacji na woluminach przypiętych lokalnie, zamiast woluminów warstwowych.<br></br>*Zastrzeżenie*: w przypadku używania woluminów przypiętych lokalnie i blokowania zakresu bajtów wolumin przypięty lokalnie może być w trybie online nawet przed ukończeniem przywracania. W takich przypadkach, jeśli przywracanie jest w toku, należy poczekać na ukończenie przywracania. |
| **7.** |Udziały warstwowe |Praca z dużymi plikami może spowodować spowolnienie warstwy. |Podczas pracy z dużymi plikami zalecamy, aby największy plik był mniejszy niż 3% rozmiaru udziału. |
| **0,8.** |Używana pojemność dla udziałów |Użycie udostępniania może być widoczne, gdy nie ma żadnych danych w udziale. Wynika to z faktu, że używana pojemność udziałów obejmuje metadane. | |
| **9.** |Odzyskiwanie po awarii |Odzyskiwanie po awarii serwera plików można wykonać tylko w tej samej domenie, w której znajduje się urządzenie źródłowe. Odzyskiwanie po awarii na urządzenie docelowe w innej domenie nie jest obsługiwane w tej wersji. |Ta implementacja jest zaimplementowana w nowszej wersji. |
| **dziesięć.** |Azure PowerShell |Nie można zarządzać urządzeniami wirtualnymi StorSimple za pomocą Azure PowerShell w tej wersji. |Wszystkie zarządzanie urządzeniami wirtualnymi należy przeprowadzić przy użyciu klasycznego portalu Azure oraz lokalnego interfejsu użytkownika sieci Web. |
| **11.** |Zmiana hasła |Konsola urządzenia macierzy wirtualnej akceptuje tylko dane wejściowe w formacie klawiatury en-US. | |
| **dwunastomiesięcznych.** |CHAP |Nie można usunąć poświadczeń protokołu CHAP po utworzeniu. Ponadto w przypadku modyfikacji poświadczeń protokołu CHAP należy przełączyć woluminy do trybu offline, a następnie przełączyć je w tryb online, aby zmiany zaczęły obowiązywać. |Ten problem został rozwiązany w nowszej wersji. |
| **trzynast.** |serwer iSCSI |"Użyty magazyn" wyświetlany dla woluminu iSCSI może się różnić w zależności od usługi StorSimple Manager i hosta iSCSI. |Host iSCSI zawiera widok systemu plików.<br></br>Urządzenie widzi bloki przydzieloną, gdy wolumin był w maksymalnym rozmiarze. |
| **14,5.** |Serwer plików |Jeśli do pliku w folderze są skojarzone alternatywne strumienie danych (ADS), nie są one tworzone ani przywracane w ramach odzyskiwania po awarii, klonowania i odzyskiwania na poziomie elementu. | |
| **15000.** |Serwer plików |Linki symboliczne nie są obsługiwane. | |
| **16.** |Serwer plików |Pliki chronione przez system Windows system szyfrowania plików (EFS) kopiowane lub przechowywane na serwerze plików macierzy wirtualnej StorSimple powodują nieobsługiwaną konfigurację.  | |

## <a name="next-step"></a>Następny krok
[Zainstaluj aktualizację 0,4](storsimple-virtual-array-install-update-04.md) na wirtualnej macierzy StorSimple.

## <a name="references"></a>Odwołania
Szukasz starszej wersji uwagi? Przejdź do strony: 

* [StorSimple Virtual Array Update 0,3 — informacje o wersji](storsimple-ova-update-03-release-notes.md)
* [StorSimple Virtual Array Update 0,1 i 0,2 — informacje o wersji](storsimple-ova-update-01-release-notes.md)
* [Informacje o wersji ogólnej dostępności macierzy wirtualnej StorSimple](./storsimple-virtual-array-update-06-release-notes.md)