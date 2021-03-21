---
title: StorSimple Virtual Array Update 0,2 & 0,1 — informacje o wersji
description: Opisuje krytyczne problemy i rozwiązania dla macierzy wirtualnej StorSimple z uruchomioną aktualizacją 0,2 i 0,1.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 3993864d-2ddd-4302-a2f1-8d737fba6eab
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/16/2016
ms.author: alkohli
ms.openlocfilehash: 67d3fafb9b4b93ae52bdb09d673bf65cad30b816
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94966160"
---
# <a name="storsimple-virtual-array-update-02-and-01-release-notes"></a>StorSimple Virtual Array Update 0,2 i 0,1 — informacje o wersji
## <a name="overview"></a>Omówienie
Poniższe informacje o wersji dotyczą krytycznych problemów otwartych i rozwiązanych problemów dotyczących Microsoft Azure StorSimple aktualizacji macierzy wirtualnych. (Microsoft Azure StorSimple tablica wirtualna jest również znana jako StorSimple lokalne urządzenie wirtualne lub StorSimple urządzenie wirtualne). 

Informacje o wersji są stale aktualizowane i wykryto krytyczne problemy wymagające obejścia. Przed wdrożeniem urządzenia wirtualnego StorSimple należy uważnie zapoznać się z informacjami zawartymi w informacjach o wersji.

Aktualizacja 0,2 jest zgodna z wersją oprogramowania **10.0.10280.0**; Aktualizacja 0,1 jest w wersji **10.0.10279.0**. W poniższych sekcjach przedstawiono zmiany dla każdej aktualizacji. 

> [!NOTE]
> Aktualizacje są zakłócane i spowodują ponowne uruchomienie urządzenia. Jeśli operacje we/wy są w toku, na urządzeniu zostanie naliczone przestoje.
> 
> 

## <a name="issues-fixed-in-the-update-02"></a>Problemy rozwiązane w aktualizacji 0,2
Aktualizacja 0,2 obejmuje wszystkie zmiany z aktualizacji 0,1 oprócz poprawki opisanej w poniższej tabeli:

| Cecha | Problem |
| --- | --- |
| Aktualizacje |W ostatniej wersji aktualizacje nie zostały wykryte automatycznie w klasycznym portalu Azure, dlatego należy użyć lokalnego interfejsu użytkownika sieci Web do zainstalowania aktualizacji. Ten problem został rozwiązany w tej wersji. Po zainstalowaniu aktualizacji 0,2 można zainstalować przyszłe aktualizacje przy użyciu klasycznego portalu Azure. |

## <a name="whats-new-in-the-update-01"></a>Co nowego w aktualizacji 0,1
Aktualizacja 0,1 zawiera następujące poprawki błędów i ulepszenia. 

* **Ulepszona odporność na awarie w chmurze**: w tej wersji występuje kilka poprawek błędów dotyczących odzyskiwania po awarii, wykonywania kopii zapasowych, przywracania i obsługi warstw w przypadku przerwania łączności z chmurą. 
* **Ulepszona wydajność przywracania**: Ta wersja zawiera poprawki błędów, które znacząco obcinają czas zakończenia zadań przywracania.
* **Zautomatyzowana optymalizacja przestrzeni odzyskiwania**: po usunięciu danych w woluminach alokowanych elastycznie należy odzyskać nieużywane bloki magazynu. W tej wersji Ulepszono proces odzyskiwania miejsca z chmury, co spowodowało, że nieużywane miejsce staje się dostępne szybciej w porównaniu z poprzednimi wersjami.
* **Nowe obrazy dysków wirtualnych**: nowe pliki VHD, VHDX i VMDK są teraz dostępne za pośrednictwem klasycznego portalu Azure. Możesz pobrać te obrazy, aby udostępnić nowe urządzenia z aktualizacją Update 0,1.
* **Poprawa dokładności stanu zadań w portalu**: w starszej wersji oprogramowania raportowanie stanu zadania w portalu było nieszczegółowe. Ten problem został rozwiązany w tej wersji.
* **Środowisko przyłączania do domeny**: poprawki błędów związanych z przyłączaniem do domeny i zmianami nazw urządzeń.

## <a name="issues-fixed-in-the-update-01"></a>Problemy rozwiązane w aktualizacji 0,1
Poniższa tabela zawiera podsumowanie problemów rozwiązanych w tej wersji.

| Nie. | Cecha | Problem |
| --- | --- | --- |
| 1 |VMDK |W niektórych wersjach programu VMware dysk systemu operacyjnego był traktowany jako rozrzedzony powodujący alerty i zakłócanie normalnych operacji. Ten problem został rozwiązany w tej wersji. |
| 2 |serwer iSCSI |W ostatniej wersji użytkownik musiał określić bramę dla każdego włączonego interfejsu sieciowego urządzenia wirtualnego StorSimple. To zachowanie jest zmieniane w tej wersji, dzięki czemu użytkownik musi skonfigurować co najmniej jedną bramę dla wszystkich włączonych interfejsów sieciowych. |
| 3 |Pakiet pomocy technicznej |W starszej wersji oprogramowania zbieranie pakietów pomocy technicznej nie powiodło się, gdy rozmiar pakietu był większy niż 1 GB. Ten problem został rozwiązany w tej wersji. |
| 4 |Dostęp do chmury |W ostatniej wersji, Jeśli wirtualna macierz StorSimple nie miała łączności sieciowej i została uruchomiona ponownie, lokalny interfejs użytkownika będzie miał problemy z łącznością. Ten problem został rozwiązany w tej wersji. |
| 5 |Wykresy monitorowania |W poprzedniej wersji, po przełączeniu urządzenia w tryb failover, wykresy wykorzystania pojemności chmury wyświetlają nieprawidłowe wartości w klasycznym portalu Azure. Ten problem został rozwiązany w bieżącej wersji. |

## <a name="known-issues-in-the-update-01"></a>Znane problemy z aktualizacją 0,1
Poniższa tabela zawiera podsumowanie znanych problemów dotyczących macierzy wirtualnej StorSimple i obejmuje wydanie wydań z poprzednich wersji. **Wydanie problemów zanotowane w tej wersji jest oznaczone gwiazdką. Prawie wszystkie problemy na tej liście przeprowadzono w porównaniu z wersją StorSimple macierzy wirtualnej.**

| Nie. | Cecha | Problem | Obejście/Komentarze |
| --- | --- | --- | --- |
| **1.** |Aktualizacje |Nie można zaktualizować urządzeń wirtualnych utworzonych w wersji zapoznawczej do obsługiwanej wersji ogólnej dostępności. |Te urządzenia wirtualne muszą zostać przełączone w tryb failover w celu udostępnienia ogólnej wersji dostępności przy użyciu przepływu pracy odzyskiwania po awarii (DR). |
| **2.** |Dysk danych z zainicjowaną obsługą |Po zainicjowaniu obsługi dysku z danymi określonego rozmiaru i utworzeniu odpowiedniego urządzenia wirtualnego StorSimple nie należy rozwijać ani zmniejszać dysku z danymi. Próba wykonania tej czynności spowoduje utratę wszystkich danych w warstwach lokalnych urządzenia. | |
| **3.** |Zasady grupy |Gdy urządzenie jest przyłączone do domeny, zastosowanie zasad grupy może niekorzystnie wpłynąć na działanie urządzenia. |Upewnij się, że tablica wirtualna ma własną jednostkę organizacyjną (OU) dla Active Directory i nie są do niej stosowane obiekty zasad grupy (GPO). |
| **4.** |Lokalny interfejs użytkownika sieci Web |Jeśli ulepszone funkcje zabezpieczeń są włączone w programie Internet Explorer (IE ESC), niektóre strony lokalnego interfejsu użytkownika sieci Web, takie jak Rozwiązywanie problemów lub konserwacja, mogą nie funkcjonować prawidłowo. Przyciski na tych stronach mogą również nie funkcjonować. |Wyłącz ulepszone funkcje zabezpieczeń w programie Internet Explorer. |
| **5000.** |Lokalny interfejs użytkownika sieci Web |W maszynie wirtualnej funkcji Hyper-V interfejsy sieciowe w interfejsie użytkownika sieci Web są wyświetlane jako interfejsy 10 GB/s. |To zachowanie jest odbiciem funkcji Hyper-V. Funkcja Hyper-V zawsze pokazuje 10 GB/s dla wirtualnych kart sieciowych. |
| **ust.** |Woluminy warstwowe lub udziały |Blokowanie zakresu bajtów dla aplikacji współpracujących z woluminami warstwowymi StorSimple nie jest obsługiwane. Jeśli jest włączone blokowanie zakresu bajtów, StorSimple nie będzie działała. |Zalecane miary obejmują: <br></br>Wyłącz blokowanie zakresu bajtów w logice aplikacji.<br></br>Wybierz opcję umieszczenia danych dla tej aplikacji na woluminach przypiętych lokalnie, zamiast woluminów warstwowych.<br></br>*Zastrzeżenie*: w przypadku używania woluminów przypiętych lokalnie i blokowania zakresu bajtów należy pamiętać, że wolumin przypięty lokalnie może być w trybie online nawet przed ukończeniem przywracania. W takich przypadkach, jeśli przywracanie jest w toku, należy poczekać na ukończenie przywracania. |
| **7.** |Udziały warstwowe |Praca z dużymi plikami może spowodować spowolnienie warstwy. |Podczas pracy z dużymi plikami zalecamy, aby największy plik był mniejszy niż 3% rozmiaru udziału. |
| **0,8.** |Używana pojemność dla udziałów |Użycie udostępniania może być widoczne w przypadku braku jakichkolwiek danych w udziale. Wynika to z faktu, że używana pojemność udziałów obejmuje metadane. | |
| **9.** |Odzyskiwanie po awarii |Odzyskiwanie po awarii serwera plików można wykonać tylko w tej samej domenie, w której znajduje się urządzenie źródłowe. Odzyskiwanie po awarii na urządzenie docelowe w innej domenie nie jest obsługiwane w tej wersji. |Ta wartość zostanie zaimplementowana w nowszej wersji. |
| **dziesięć.** |Azure PowerShell |Nie można zarządzać urządzeniami wirtualnymi StorSimple za pomocą Azure PowerShell w tej wersji. |Wszystkie zarządzanie urządzeniami wirtualnymi należy przeprowadzić przy użyciu klasycznego portalu Azure oraz lokalnego interfejsu użytkownika sieci Web. |
| **11.** |Zmiana hasła |Konsola urządzenia macierzy wirtualnej akceptuje tylko dane wejściowe w formacie klawiatury en-US. | |
| **dwunastomiesięcznych.** |CHAP |Nie można usunąć poświadczeń protokołu CHAP po utworzeniu. Ponadto w przypadku zmodyfikowania poświadczeń protokołu CHAP należy przełączyć woluminy do trybu offline, a następnie przełączyć je w tryb online, aby zmiany zaczęły obowiązywać. |Zostaną one rozkierowane do nowszej wersji. |
| **trzynast.** |serwer iSCSI |"Użyty magazyn" wyświetlany dla woluminu iSCSI może się różnić w zależności od usługi StorSimple Manager i hosta iSCSI. |Host iSCSI zawiera widok systemu plików.<br></br>Urządzenie widzi bloki przydzieloną, gdy wolumin był w maksymalnym rozmiarze. |
| **14,5.** |Serwer plików * |Jeśli do pliku w folderze są skojarzone alternatywne strumienie danych (ADS), nie są one tworzone ani przywracane w ramach odzyskiwania po awarii, klonowania i odzyskiwania na poziomie elementu. | |

## <a name="next-step"></a>Następny krok
[Zainstaluj aktualizacje](./storsimple-virtual-array-install-update-06.md) w macierzy wirtualnej StorSimple.