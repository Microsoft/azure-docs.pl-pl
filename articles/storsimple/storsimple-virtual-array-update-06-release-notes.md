---
title: StorSimple Virtual Array Update 0,6 — informacje o wersji | Microsoft Docs
description: Opisuje krytyczne problemy i rozwiązania dla StorSimple wirtualnej macierzy z uruchomioną aktualizacją 0,6.
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
ms.date: 05/24/2017
ms.author: alkohli
ms.openlocfilehash: 24bddb47a32a7b8ad4ea659d52c2d5b2730be9ad
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000916"
---
# <a name="storsimple-virtual-array-update-06-release-notes"></a>StorSimple Virtual Array Update 0,6 — informacje o wersji

## <a name="overview"></a>Omówienie

Poniższe informacje o wersji dotyczą krytycznych problemów otwartych i rozwiązanych problemów dotyczących Microsoft Azure StorSimple aktualizacji macierzy wirtualnych.

Informacje o wersji są stale aktualizowane i wykryto krytyczne problemy wymagające obejścia. Przed wdrożeniem macierzy wirtualnej StorSimple należy uważnie zapoznać się z informacjami znajdującymi się w informacjach o wersji.

Aktualizacja 0,6 jest zgodna z wersją oprogramowania **10.0.10293.0**.

> [!IMPORTANT]
> - Aktualizacje są zakłócone i ponownie uruchamiają urządzenie. Jeśli we/wy jest w toku, urządzenie powoduje przestoje. Aby uzyskać szczegółowe instrukcje dotyczące stosowania aktualizacji, przejdź do [Install update 0,6](storsimple-virtual-array-install-update-06.md).
>
> - Zdecydowanie zalecamy zainstalowanie aktualizacji 0,6 natychmiast, ponieważ zawiera ona krytyczne poprawki zabezpieczeń.


## <a name="whats-new-in-the-update-06"></a>Co nowego w aktualizacji 0,6
Aktualizacja 0,6 jest aktualizacją krytyczną i należy ją wdrożyć od razu. Ta aktualizacja zawiera następujące poprawki: 

- **Poprawki zabezpieczeń systemu Windows** — ta wersja ma **krytyczne poprawki zabezpieczeń systemu Windows**. Aby uzyskać więcej informacji o problemach z zabezpieczeniami i skojarzonych poprawkach, zapoznaj się z następującymi aktualizacjami zabezpieczeń:
    - [Aktualizacja jakości tylko z grudnia 2016 dla Windows 8.1 i systemu Windows Server 2012 R2](https://support.microsoft.com/help/3205400/december-2016-security-only-quality-update-for-windows-8.1-and-windows-server-2012-r2)
    - [Marzec 2017 aktualizacja dotycząca jakości tylko dla Windows 8.1 i systemu Windows Server 2012 R2](https://support.microsoft.com/help/4012213/march-2017-security-only-quality-update-for-windows-8-1-and-windows-server-2012-r23)
    - [9 maja 2017 r. — KB4019213 (aktualizacja tylko do zabezpieczeń)](https://support.microsoft.com/help/4019213/windows-8-update-kb4019213)

- **Przywrócenie poprawki** w starszych wersjach, wystąpił błąd, który mógłby uniemożliwić ukończenie przywracania. Ta usterka została rozwiązana w tej wersji.


## <a name="issues-fixed-in-the-update-06"></a>Problemy rozwiązane w aktualizacji 0,6

Poniższa tabela zawiera podsumowanie problemów rozwiązanych w tej wersji.

| Nie. | Cechy | Problem |
| --- | --- | --- |
| 1 |Zabezpieczenia| Ta wersja zawiera krytyczne aktualizacje zabezpieczeń systemu Windows. Zalecamy natychmiastowe zainstalowanie tej aktualizacji.|
| 2 |Przywracanie| Podczas przywracania wystąpił warunek wyścigu, który uniemożliwia ukończenie zadania przywracania. Poprawka błędu dotyczy tego warunku wyścigu.|


## <a name="known-issues-in-the-update-06"></a>Znane problemy z aktualizacją 0,6

Poniższa tabela zawiera podsumowanie znanych problemów dotyczących macierzy wirtualnej StorSimple i obejmuje wydanie wydań z poprzednich wersji.

| Nie. | Cechy | Problem | Obejście/Komentarze |
| --- | --- | --- | --- |
| **1.** |Aktualizacje |Nie można zaktualizować urządzeń wirtualnych utworzonych w wersji zapoznawczej do obsługiwanej wersji ogólnej dostępności. |Te urządzenia wirtualne muszą zostać przełączone w tryb failover w celu udostępnienia ogólnej wersji dostępności przy użyciu przepływu pracy odzyskiwania po awarii (DR). |
| **2.** |Dysk danych z zainicjowaną obsługą |Po zainicjowaniu obsługi dysku z danymi określonego rozmiaru i utworzeniu odpowiedniego urządzenia wirtualnego StorSimple nie należy rozwijać ani zmniejszać dysku z danymi. Próba wykonania spowoduje utratę wszystkich danych w warstwach lokalnych urządzenia. | |
| **3.** |Zasady grupy |Gdy urządzenie jest przyłączone do domeny, zastosowanie zasad grupy może niekorzystnie wpłynąć na działanie urządzenia. |Upewnij się, że tablica wirtualna ma własną jednostkę organizacyjną (OU) dla Active Directory i nie są do niej stosowane obiekty zasad grupy (GPO). |
| **4.** |Lokalny interfejs użytkownika sieci Web |Jeśli ulepszone funkcje zabezpieczeń są włączone w programie Internet Explorer (IE ESC), niektóre strony lokalnego interfejsu użytkownika sieci Web, takie jak Rozwiązywanie problemów lub konserwacja, mogą nie funkcjonować prawidłowo. Przyciski na tych stronach mogą również nie funkcjonować. |Wyłącz ulepszone funkcje zabezpieczeń w programie Internet Explorer. |
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
| **7.** |Aktualizacje |Jeśli widzisz kod błędu: 2359302 (szesnastkowo 0x240006) podczas próby zainstalowania poprawki za pomocą lokalnego interfejsu użytkownika, oznacza to, że poprawka jest już zainstalowana na urządzeniu.   | |

## <a name="next-step"></a>Następny krok
[Zainstaluj aktualizację 0,6](storsimple-virtual-array-install-update-06.md) na wirtualnej macierzy StorSimple.

## <a name="references"></a>Odwołania
Szukasz starszej wersji uwagi? Przejdź do strony:

* [StorSimple Virtual Array Update 0,5 — informacje o wersji](storsimple-virtual-array-update-05-release-notes.md)
* [StorSimple Virtual Array Update 0,4 — Informacje o wersji](storsimple-virtual-array-update-04-release-notes.md)
* [StorSimple Virtual Array Update 0,3 — informacje o wersji](storsimple-ova-update-03-release-notes.md)
* [StorSimple Virtual Array Update 0,1 i 0,2 — informacje o wersji](storsimple-ova-update-01-release-notes.md)
* [Informacje o wersji ogólnej dostępności macierzy wirtualnej StorSimple]()