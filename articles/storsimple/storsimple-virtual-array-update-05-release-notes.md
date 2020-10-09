---
title: StorSimple Virtual Array Update 0,5 — informacje o wersji | Microsoft Docs
description: Opisuje krytyczne problemy i rozwiązania dla StorSimple wirtualnej macierzy z uruchomioną aktualizacją 0,5.
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
ms.date: 05/08/2017
ms.author: alkohli
ms.openlocfilehash: 385d9126d578250064659153f6f0f54eec696790
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "60870676"
---
# <a name="storsimple-virtual-array-update-05-release-notes"></a>StorSimple Virtual Array Update 0,5 — informacje o wersji

## <a name="overview"></a>Omówienie

Poniższe informacje o wersji dotyczą krytycznych problemów otwartych i rozwiązanych problemów dotyczących Microsoft Azure StorSimple aktualizacji macierzy wirtualnych.

Informacje o wersji są stale aktualizowane i wykryto krytyczne problemy wymagające obejścia. Przed wdrożeniem macierzy wirtualnej StorSimple należy uważnie zapoznać się z informacjami znajdującymi się w informacjach o wersji.

Aktualizacja 0,5 jest zgodna z wersją oprogramowania **10.0.10290.0**.

> [!NOTE]
> Aktualizacje są zakłócone i ponownie uruchamiają urządzenie. Jeśli we/wy jest w toku, urządzenie powoduje przestoje. Aby uzyskać szczegółowe instrukcje dotyczące stosowania aktualizacji, przejdź do [Install update 0,5](storsimple-virtual-array-install-update-05.md).


## <a name="whats-new-in-the-update-05"></a>Co nowego w aktualizacji 0,5
Aktualizacja 0,5 jest przede wszystkim błędem kompilacją poprawki. Główne ulepszenia i poprawki błędów są następujące:

- **Ulepszenia odporności kopii zapasowych** — ta wersja ma poprawki, które zwiększają odporność kopii zapasowych. We wcześniejszych wersjach kopie zapasowe zostały ponowione tylko dla niektórych wyjątków. Ta wersja ponawia próbę wszystkich wyjątków kopii zapasowych i zwiększa odporność kopii zapasowych.

- **Aktualizacje dotyczące monitorowania użycia magazynu** — począwszy od 30 czerwca 2017, monitorowanie użycia magazynu dla serii urządzeń wirtualnych StorSimple zostanie wycofane. Dotyczy to wykresów monitorowania na wszystkich macierzach wirtualnych z aktualizacją 0,4 lub niższą. Ta aktualizacja zawiera zmiany wymagane do kontynuowania korzystania z monitorowania użycia magazynu w Azure Portal. Zainstaluj tę aktualizację krytyczną przed 30 czerwca 2017, aby kontynuować korzystanie z funkcji monitorowania.


## <a name="issues-fixed-in-the-update-05"></a>Problemy rozwiązane w aktualizacji 0,5

Poniższa tabela zawiera podsumowanie problemów rozwiązanych w tej wersji.

| Nie. | Cecha | Problem |
| --- | --- | --- |
| 1 |Odporność kopii zapasowych| We wcześniejszych wersjach kopie zapasowe zostały ponowione tylko dla niektórych wyjątków. Ta wersja zawiera poprawkę, aby kopie zapasowe były bardziej odporne przez ponowną próbę wszystkich wyjątków kopii zapasowych.|
| 2 |Monitorowanie| Monitorowanie użycia magazynu dla serii urządzeń wirtualnych StorSimple będzie przestarzałe od 30 czerwca 2017. Ta akcja ma wpływ na wykresy monitorowania w usłudze StorSimple Menedżer urządzeń działającej na macierzach wirtualnych StorSimple (model 1200). W tej wersji znajdują się aktualizacje zezwalające użytkownikowi na kontynuowanie korzystania z monitorowania użycia magazynu w macierzach wirtualnych poza 30 czerwca 2017.|
| 3 |Serwer plików| We wcześniejszych wersjach użytkownik mógł w sposób niezajęty skopiować zaszyfrowane pliki do macierzy wirtualnej. Ta wersja zawiera poprawkę, która nie zezwala na kopiowanie zaszyfrowanych plików do macierzy wirtualnej. Jeśli urządzenie ma istniejące zaszyfrowane pliki przed aktualizacją, kopie zapasowe będą nadal kończyć się niepowodzeniem, dopóki wszystkie zaszyfrowane pliki nie zostaną usunięte z systemu. |


## <a name="known-issues-in-the-update-05"></a>Znane problemy z aktualizacją 0,5

Poniższa tabela zawiera podsumowanie znanych problemów dotyczących macierzy wirtualnej StorSimple i obejmuje wydanie wydań z poprzednich wersji.

| Nie. | Cecha | Problem | Obejście/Komentarze |
| --- | --- | --- | --- |
| **1.** |Aktualizacje |Nie można zaktualizować urządzeń wirtualnych utworzonych w wersji zapoznawczej do obsługiwanej wersji ogólnej dostępności. |Te urządzenia wirtualne muszą zostać przełączone w tryb failover w celu udostępnienia ogólnej wersji dostępności przy użyciu przepływu pracy odzyskiwania po awarii (DR). |
| **2.** |Dysk danych z zainicjowaną obsługą |Po zainicjowaniu obsługi dysku z danymi określonego rozmiaru i utworzeniu odpowiedniego urządzenia wirtualnego StorSimple nie należy rozwijać ani zmniejszać dysku z danymi. Próba wykonania spowoduje utratę wszystkich danych w warstwach lokalnych urządzenia. | |
| **3.** |Zasady grupy |Gdy urządzenie jest przyłączone do domeny, zastosowanie zasad grupy może niekorzystnie wpłynąć na działanie urządzenia. |Upewnij się, że tablica wirtualna ma własną jednostkę organizacyjną (OU) dla Active Directory i nie są do niej stosowane obiekty zasad grupy (GPO). |
| **czwart.** |Lokalny interfejs użytkownika sieci Web |Jeśli ulepszone funkcje zabezpieczeń są włączone w programie Internet Explorer (IE ESC), niektóre strony lokalnego interfejsu użytkownika sieci Web, takie jak Rozwiązywanie problemów lub konserwacja, mogą nie funkcjonować prawidłowo. Przyciski na tych stronach mogą również nie funkcjonować. |Wyłącz ulepszone funkcje zabezpieczeń w programie Internet Explorer. |
| **5000.** |Lokalny interfejs użytkownika sieci Web |W maszynie wirtualnej funkcji Hyper-V interfejsy sieciowe w interfejsie użytkownika sieci Web są wyświetlane jako interfejsy 10 GB/s. |To zachowanie jest odbiciem funkcji Hyper-V. Funkcja Hyper-V zawsze pokazuje 10 GB/s dla wirtualnych kart sieciowych. |
| **ust.** |Woluminy warstwowe lub udziały |Blokowanie zakresu bajtów dla aplikacji współpracujących z woluminami warstwowymi StorSimple nie jest obsługiwane. W przypadku włączenia blokowania zakresu bajtów nie działa StorSimple. |Zalecane miary obejmują: <br></br>Wyłącz blokowanie zakresu bajtów w logice aplikacji.<br></br>Wybierz opcję umieszczenia danych dla tej aplikacji na woluminach przypiętych lokalnie, zamiast woluminów warstwowych.<br></br>*Zastrzeżenie*: w przypadku używania woluminów przypiętych lokalnie i blokowania zakresu bajtów wolumin przypięty lokalnie może być w trybie online nawet przed ukończeniem przywracania. W takich przypadkach, jeśli przywracanie jest w toku, należy poczekać na ukończenie przywracania. |
| **7.** |Udziały warstwowe |Praca z dużymi plikami może spowodować spowolnienie warstwy. |Podczas pracy z dużymi plikami zalecamy, aby największy plik był mniejszy niż 3% rozmiaru udziału. |
| **0,8.** |Używana pojemność dla udziałów |Użycie udostępniania może być widoczne, gdy nie ma żadnych danych w udziale. To zużycie wynika z faktu, że używana pojemność udziałów obejmuje metadane. | |
| **9.** |Odzyskiwanie po awarii |Odzyskiwanie po awarii serwera plików można wykonać tylko w tej samej domenie, w której znajduje się urządzenie źródłowe. Odzyskiwanie po awarii na urządzenie docelowe w innej domenie nie jest obsługiwane w tej wersji. |Ta implementacja jest zaimplementowana w nowszej wersji. Aby uzyskać więcej informacji, przejdź do [trybu failover i odzyskiwania po awarii dla macierzy wirtualnej StorSimple](storsimple-virtual-array-failover-dr.md) |
| **dziesięć.** |Azure PowerShell |Nie można zarządzać urządzeniami wirtualnymi StorSimple za pomocą Azure PowerShell w tej wersji. |Wszystkie zarządzanie urządzeniami wirtualnymi powinno odbywać się za pomocą Azure Portal i lokalnego interfejsu użytkownika sieci Web. |
| **11.** |Zmiana hasła |Konsola urządzenia macierzy wirtualnej akceptuje tylko dane wejściowe w formacie klawiatury en-us. | |
| **dwunastomiesięcznych.** |CHAP |Nie można usunąć poświadczeń protokołu CHAP po utworzeniu. Ponadto w przypadku modyfikacji poświadczeń protokołu CHAP należy przełączyć woluminy do trybu offline, a następnie przełączyć je w tryb online, aby zmiany zaczęły obowiązywać. |Ten problem został rozwiązany w nowszej wersji. |
| **trzynast.** |serwer iSCSI |"Użyty magazyn" wyświetlany dla woluminu iSCSI może się różnić w StorSimple usługi Menedżer urządzeń i hoście iSCSI. |Host iSCSI zawiera widok systemu plików.<br></br>Urządzenie widzi bloki przydzieloną, gdy wolumin był w maksymalnym rozmiarze. |
| **14,5.** |Serwer plików |Jeśli do pliku w folderze są skojarzone alternatywne strumienie danych (ADS), nie są one tworzone ani przywracane w ramach odzyskiwania po awarii, klonowania i odzyskiwania na poziomie elementu. | |
| **15000.** |Serwer plików |Linki symboliczne nie są obsługiwane. | |
| **16.** |Serwer plików |Pliki chronione przez system Windows system szyfrowania plików (EFS) kopiowane lub przechowywane na serwerze plików macierzy wirtualnej StorSimple powodują nieobsługiwaną konfigurację.  | |

## <a name="next-step"></a>Następny krok
[Zainstaluj aktualizację 0,5](storsimple-virtual-array-install-update-05.md) na wirtualnej macierzy StorSimple.

## <a name="references"></a>Odwołania
Szukasz starszej wersji uwagi? Przejdź do strony:

* [StorSimple Virtual Array Update 0,4 — Informacje o wersji](storsimple-virtual-array-update-04-release-notes.md)
* [StorSimple Virtual Array Update 0,3 — informacje o wersji](storsimple-ova-update-03-release-notes.md)
* [StorSimple Virtual Array Update 0,1 i 0,2 — informacje o wersji](storsimple-ova-update-01-release-notes.md)
* [Informacje o wersji ogólnej dostępności macierzy wirtualnej StorSimple](storsimple-ova-pp-release-notes.md)

