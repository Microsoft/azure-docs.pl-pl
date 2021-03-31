---
title: StorSimple Virtual Array Update 0,3 — informacje o wersji
description: Opisuje krytyczne problemy i rozwiązania dla StorSimple wirtualnej macierzy z uruchomioną aktualizacją 0,3.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: b197651a-3c40-4185-b23d-4c8f22cfa8f4
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/15/2016
ms.author: alkohli
ms.openlocfilehash: 8301b45fe778bd3df7fc665db2662ba81d3e644f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94966109"
---
# <a name="storsimple-virtual-array-update-03-release-notes"></a>StorSimple Virtual Array Update 0,3 — informacje o wersji
## <a name="overview"></a>Omówienie
Poniższe informacje o wersji dotyczą krytycznych problemów otwartych i rozwiązanych problemów dotyczących Microsoft Azure StorSimple aktualizacji macierzy wirtualnych.

Informacje o wersji są stale aktualizowane i wykryto krytyczne problemy wymagające obejścia. Przed wdrożeniem macierzy wirtualnej StorSimple należy uważnie zapoznać się z informacjami znajdującymi się w informacjach o wersji.

Aktualizacja 0,3 jest zgodna z wersją oprogramowania **10.0.10288.0**.

> [!NOTE]
> Aktualizacje są zakłócone i ponownie uruchamiają urządzenie. Jeśli we/wy jest w toku, urządzenie powoduje przestoje.
> 
> 

## <a name="whats-new-in-the-update-03"></a>Co nowego w aktualizacji 0,3
Aktualizacja 0,3 jest przede wszystkim błędem kompilacją poprawki. W tej wersji zostały rozkierowane pewne błędy powodujące błędy kopii zapasowych w poprzedniej wersji.

## <a name="issues-fixed-in-the-update-03"></a>Problemy rozwiązane w aktualizacji 0,3
Poniższa tabela zawiera podsumowanie problemów rozwiązanych w tej wersji.

| Nie. | Cecha | Problem |
| --- | --- | --- |
| 1 |Tworzenie kopii zapasowych |Wystąpił problem w starszej wersji, w której wykonywanie kopii zapasowych dla udziału plików nie powiedzie się. W przypadku wystąpienia tego problemu zadanie tworzenia kopii zapasowej zakończy się niepowodzeniem, a w usłudze StorSimple Manager zgłoszono alert krytyczny, aby powiadomić użytkownika. Ten problem nie miał wpływu na dane w udziałach ani na dostęp do danych. Główna przyczyna została zidentyfikowana i naprawiona w tej wersji. <br></br> Poprawka nie jest stosowana z mocą wsteczną do udziałów, które już widzą ten problem. Klienci, którzy widzą ten problem, powinni najpierw zastosować aktualizację 0,3, a następnie skontaktować się z pomoc techniczna firmy Microsoft w celu wykonania pełnej kopii zapasowej systemu w celu rozwiązania problemu. Zamiast kontaktowania się z pomoc techniczna firmy Microsoft klienci mogą również przywrócić nowe udziały z dobrej kopii zapasowej dla udziałów, których to dotyczy. |
| 2 |iSCSI |Pojawił się problem w starszej wersji, w której woluminy znikną podczas kopiowania danych do woluminu w macierzy wirtualnej StorSimple. Ten problem został rozwiązany w tej wersji. <br></br> Poprawki zaczną obowiązywać tylko na nowo utworzonych woluminach. Poprawki nie są stosowane z mocą wsteczną do woluminów, które już widzą ten problem. Aby klienci mogli przenieść te woluminy w tryb online za pośrednictwem klasycznego portalu Azure, należy wykonać kopię zapasową tych woluminów, a następnie przywrócić te woluminy na nowe woluminy. |

## <a name="known-issues-in-the-update-03"></a>Znane problemy z aktualizacją 0,3
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

## <a name="next-step"></a>Następny krok
[Zainstaluj aktualizację 0,3](./storsimple-virtual-array-install-update-06.md) na wirtualnej macierzy StorSimple.

## <a name="references"></a>Odwołania
Szukasz starszej wersji uwagi? Przejdź do strony: 

* [StorSimple Virtual Array Update 0,1 i 0,2 — informacje o wersji](storsimple-ova-update-01-release-notes.md)
* [Informacje o wersji ogólnej dostępności macierzy wirtualnej StorSimple](./storsimple-virtual-array-update-06-release-notes.md)