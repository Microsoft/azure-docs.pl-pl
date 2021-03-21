---
title: StorSimple Virtual Array Update 1,1 — informacje o wersji | Microsoft Docs
description: Opisuje krytyczne problemy i rozwiązania dla StorSimple wirtualnej macierzy z uruchomioną aktualizacją 1,1.
services: storsimple
documentationcenter: ''
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/22/2018
ms.author: alkohli
ms.openlocfilehash: df4722a4232686b0d981c190bdc154f0d1bec62e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94960208"
---
# <a name="storsimple-virtual-array-update-11-release-notes"></a>StorSimple Virtual Array Update 1,1 — informacje o wersji

## <a name="overview"></a>Omówienie

Poniższe informacje o wersji dotyczą krytycznych problemów otwartych i rozwiązanych problemów dotyczących Microsoft Azure StorSimple aktualizacji macierzy wirtualnych.

Informacje o wersji są stale aktualizowane i wykryto krytyczne problemy wymagające obejścia. Przed wdrożeniem macierzy wirtualnej StorSimple należy uważnie zapoznać się z informacjami znajdującymi się w informacjach o wersji.

Aktualizacja 1,1 jest zgodna z wersją oprogramowania **10.0.10307.0**.

> [!IMPORTANT]
> - Aktualizacje są zakłócone i ponownie uruchamiają urządzenie. Jeśli we/wy jest w toku, urządzenie powoduje przestoje. Aby uzyskać szczegółowe instrukcje dotyczące stosowania aktualizacji, przejdź do [Install update 1,1](storsimple-virtual-array-install-update-11.md).
>
> - Aktualizacja 1,1 jest dostępna za pośrednictwem Azure Portal tylko wtedy, gdy na urządzeniu jest uruchomiona aktualizacja 1,0.

## <a name="whats-new-in-update-11"></a>Co nowego w aktualizacji Update 1,1

Ta aktualizacja zawiera następujące poprawki dotyczące ulepszeń i błędów:

 - **Błędy kopii zapasowej** — udoskonalone przez zwiększenie odporności na awarie chmury i duże użycie procesora CPU.
 - **Rejestrowanie** — wprowadzono zmiany w rejestrowaniu w trybie pełnym, gdy urządzenie jest w sesji obsługi.


## <a name="issues-fixed-in-update-11"></a>Problemy rozwiązane w aktualizacji 1,1

Poniższa tabela zawiera podsumowanie problemów rozwiązanych w tej wersji.

| Nie. | Cecha | Problem |
| --- | --- | --- |
| 1 |Tworzenie kopii zapasowych| Ta wersja zawiera zmiany, które poprawiły błędy kopii zapasowej przez zwiększenie odporności na awarie chmury i duże użycie procesora CPU.|
| 2 |Rejestrowanie| Ta wersja zawiera zmiany w rejestrowaniu, gdy urządzenie jest w trybie pełnej obsługi.|


## <a name="known-issues-in-update-11"></a>Znane problemy w aktualizacji 1,1

Poniższa tabela zawiera podsumowanie znanych problemów dotyczących macierzy wirtualnej StorSimple i obejmuje wydanie wydań z poprzednich wersji.

| Nie. | Cecha | Problem | Obejście/Komentarze |
| --- | --- | --- | --- |
| **1.** |Aktualizacje |Nie można zaktualizować macierzy wirtualnych utworzonych w wersji zapoznawczej do obsługiwanej ogólnej wersji dostępności. |Te tablice wirtualne muszą zostać przełączone w tryb failover w celu udostępnienia ogólnej wersji dostępności przy użyciu przepływu pracy odzyskiwania po awarii (DR). |
| **2.** |Dysk danych z zainicjowaną obsługą |Po zainicjowaniu obsługi dysku danych o określonym określonym rozmiarze i utworzeniu odpowiedniej macierzy wirtualnej StorSimple nie należy rozwijać ani zmniejszać dysku z danymi. Próba wykonania spowoduje utratę wszystkich danych w warstwach lokalnych urządzenia. | |
| **3.** |Zasady grupy |Gdy urządzenie jest przyłączone do domeny, zastosowanie zasad grupy może niekorzystnie wpłynąć na działanie urządzenia. |Upewnij się, że tablica wirtualna ma własną jednostkę organizacyjną (OU) dla Active Directory i nie są do niej stosowane obiekty zasad grupy (GPO). |
| **4.** |Lokalny interfejs użytkownika sieci Web |Jeśli ulepszone funkcje zabezpieczeń są włączone w programie Internet Explorer (IE ESC), niektóre strony lokalnego interfejsu użytkownika sieci Web, takie jak Rozwiązywanie problemów lub konserwacja, mogą nie funkcjonować prawidłowo. Przyciski na tych stronach mogą również nie funkcjonować. |Wyłącz ulepszone funkcje zabezpieczeń w programie Internet Explorer. |
| **5000.** |Lokalny interfejs użytkownika sieci Web |W maszynie wirtualnej funkcji Hyper-V interfejsy sieciowe w interfejsie użytkownika sieci Web są wyświetlane jako interfejsy 10 GB/s. |To zachowanie jest odbiciem funkcji Hyper-V. Funkcja Hyper-V zawsze pokazuje 10 GB/s dla wirtualnych kart sieciowych. |
| **ust.** |Woluminy warstwowe lub udziały |Blokowanie zakresu bajtów dla aplikacji współpracujących z woluminami warstwowymi StorSimple nie jest obsługiwane. W przypadku włączenia blokowania zakresu bajtów nie działa StorSimple. |Zalecane miary obejmują: <br></br>Wyłącz blokowanie zakresu bajtów w logice aplikacji.<br></br>Wybierz opcję umieszczenia danych dla tej aplikacji na woluminach przypiętych lokalnie, zamiast woluminów warstwowych.<br></br>*Zastrzeżenie*: w przypadku używania woluminów przypiętych lokalnie i blokowania zakresu bajtów wolumin przypięty lokalnie może być w trybie online nawet przed ukończeniem przywracania. W takich przypadkach, jeśli przywracanie jest w toku, należy poczekać na ukończenie przywracania. |
| **7.** |Udziały warstwowe |Praca z dużymi plikami może spowodować spowolnienie warstwy. |Podczas pracy z dużymi plikami zalecamy, aby największy plik był mniejszy niż 3% rozmiaru udziału. |
| **0,8.** |Używana pojemność dla udziałów |Użycie udostępniania może być widoczne, gdy nie ma żadnych danych w udziale. To zużycie wynika z faktu, że używana pojemność udziałów obejmuje metadane. | |
| **9.** |Odzyskiwanie po awarii |Odzyskiwanie po awarii serwera plików można wykonać tylko w tej samej domenie, w której znajduje się urządzenie źródłowe. Odzyskiwanie po awarii na urządzenie docelowe w innej domenie nie jest obsługiwane w tej wersji. |Ta implementacja jest zaimplementowana w nowszej wersji. Aby uzyskać więcej informacji, przejdź do [trybu failover i odzyskiwania po awarii dla macierzy wirtualnej StorSimple](storsimple-virtual-array-failover-dr.md) |
| **dziesięć.** |Azure PowerShell |Nie można zarządzać macierzami wirtualnymi StorSimple za pomocą Azure PowerShell w tej wersji. |Wszystkie zarządzanie urządzeniami wirtualnymi powinno odbywać się za pomocą Azure Portal i lokalnego interfejsu użytkownika sieci Web. |
| **11.** |Zmiana hasła |Konsola urządzenia macierzy wirtualnej akceptuje tylko dane wejściowe w formacie klawiatury en-us. | |
| **dwunastomiesięcznych.** |CHAP |Nie można usunąć poświadczeń protokołu CHAP po utworzeniu. Ponadto w przypadku modyfikacji poświadczeń protokołu CHAP należy przełączyć woluminy do trybu offline, a następnie przełączyć je w tryb online, aby zmiany zaczęły obowiązywać. |Ten problem został rozwiązany w nowszej wersji. |
| **trzynast.** |serwer iSCSI |"Użyty magazyn" wyświetlany dla woluminu iSCSI może się różnić w StorSimple usługi Device Manager i hoście iSCSI. |Host iSCSI zawiera widok systemu plików.<br></br>Urządzenie widzi bloki przydzieloną, gdy wolumin był w maksymalnym rozmiarze. |
| **14,5.** |Serwer plików |Jeśli do pliku w folderze są skojarzone alternatywne strumienie danych (ADS), nie są one tworzone ani przywracane w ramach odzyskiwania po awarii, klonowania i odzyskiwania na poziomie elementu. | |
| **15000.** |Serwer plików |Linki symboliczne nie są obsługiwane. | |
| **16.** |Serwer plików |Pliki chronione przez system Windows system szyfrowania plików (EFS) kopiowane lub przechowywane na serwerze plików macierzy wirtualnej StorSimple powodują nieobsługiwaną konfigurację.  | |
| **7.** |Aktualizacje |Jeśli widzisz kod błędu: 2359302 (szesnastkowo 0x240006) podczas próby zainstalowania poprawki za pomocą lokalnego interfejsu użytkownika, oznacza to, że poprawka jest już zainstalowana na urządzeniu.   | |
| **postanowienia.** |Aktualizacje |Jeśli używasz lokalnego interfejsu użytkownika sieci Web do zainstalowania aktualizacji Update 1 w macierzy wirtualnej, musisz upewnić się, że jest uruchomiona Aktualizacja 0,6. W przypadku korzystania z wersji niższej niż aktualizacja 0,6 należy najpierw zainstalować aktualizację 0,6, a następnie zastosować aktualizację Update 1. W przypadku bezpośredniej instalacji aktualizacji 1,0 z wersji sprzed aktualizacji 0,6 wszystkie aktualizacje zostaną pominięte, a wykresy monitorowania nie będą działały.   | |


## <a name="next-steps"></a>Następne kroki
[Zainstaluj aktualizację 1,1](storsimple-virtual-array-install-update-11.md) na wirtualnej macierzy StorSimple.

## <a name="references"></a>Odwołania
Szukasz starszej wersji uwagi? Przejdź do strony:
* [StorSimple Virtual Array Update 1,0 — informacje o wersji](storsimple-virtual-array-update-1-release-notes.md)
* [StorSimple Virtual Array Update 0,6 — informacje o wersji](storsimple-virtual-array-update-06-release-notes.md)
* [StorSimple Virtual Array Update 0,5 — informacje o wersji](storsimple-virtual-array-update-05-release-notes.md)
* [StorSimple Virtual Array Update 0,4 — Informacje o wersji](storsimple-virtual-array-update-04-release-notes.md)
* [StorSimple Virtual Array Update 0,3 — informacje o wersji](storsimple-ova-update-03-release-notes.md)
* [StorSimple Virtual Array Update 0,1 i 0,2 — informacje o wersji](storsimple-ova-update-01-release-notes.md)
* [Informacje o wersji ogólnej dostępności macierzy wirtualnej StorSimple](./storsimple-virtual-array-update-06-release-notes.md)