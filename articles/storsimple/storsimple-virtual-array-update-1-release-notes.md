---
title: StorSimple Virtual Array Update 1,0 — informacje o wersji
description: Opisuje krytyczne problemy i rozwiązania dla StorSimple wirtualnej macierzy z uruchomioną aktualizacją 1,0.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: alkohli
ms.openlocfilehash: 48dec3a87ab540af224ae4ac59dd37cee7c9d0ee
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "76271336"
---
# <a name="storsimple-virtual-array-update-10-release-notes"></a>StorSimple Virtual Array Update 1,0 — informacje o wersji

## <a name="overview"></a>Omówienie

Poniższe informacje o wersji dotyczą krytycznych problemów otwartych i rozwiązanych problemów dotyczących Microsoft Azure StorSimple aktualizacji macierzy wirtualnych.

Informacje o wersji są stale aktualizowane i wykryto krytyczne problemy wymagające obejścia. Przed wdrożeniem macierzy wirtualnej StorSimple należy uważnie zapoznać się z informacjami znajdującymi się w informacjach o wersji.

Aktualizacja 1,0 jest zgodna z wersją oprogramowania **10.0.10296.0**.

> [!IMPORTANT]
> - Aktualizacje są zakłócone i ponownie uruchamiają urządzenie. Jeśli we/wy jest w toku, urządzenie powoduje przestoje. Aby uzyskać szczegółowe instrukcje dotyczące stosowania aktualizacji, przejdź do [Install update 1,0](storsimple-virtual-array-install-update-1.md).
>
> - Aktualizacja 1 jest dostępna tylko dla użytkownika za pośrednictwem Azure Portal, jeśli na urządzeniu jest uruchomiona Aktualizacja 0,6.

## <a name="whats-new-in-update-10"></a>Co nowego w aktualizacji Update 1,0

**Aktualizacja 1,0 zawiera zmiany związane z uwierzytelnianiem usługi StorSimple Menedżer urządzeń i należy je wdrożyć najwcześniej.** Ta aktualizacja zawiera następujące ulepszenia i poprawki błędów:

 - **Korzystanie z Azure Active Directory (AAD) do uwierzytelniania za pomocą usługi StorSimple Menedżer urządzeń** — od aktualizacji 1,0, Azure Active Directory służy do uwierzytelniania za pomocą usługi StorSimple Menedżer urządzeń. Stary mechanizm uwierzytelniania będzie przestarzały w grudniu 2017. Wszyscy użytkownicy muszą zawierać nowe adresy URL uwierzytelniania w regułach zapory. Aby uzyskać więcej informacji, przejdź do adresów URL uwierzytelniania wymienionych w temacie [wymagania dotyczące sieci dla macierzy wirtualnej StorSimple](storsimple-ova-system-requirements.md).
 
    Jeśli adres URL uwierzytelniania nie jest uwzględniony w regułach zapory, użytkownicy zobaczą alert krytyczny, że urządzenie StorSimple nie może uwierzytelnić się w usłudze. Jeśli użytkownicy zobaczą ten alert, muszą uwzględnić nowy adres URL uwierzytelniania. Aby uzyskać więcej informacji, przejdź do [StorSimple alerty sieciowe](storsimple-virtual-array-manage-alerts.md).

 - **Poprawa wydajności** — wprowadzono kilka poprawek błędów w celu zwiększenia szybkości operacji odczytu, warstw i warstw w chmurze. W efekcie wydajność tworzenia kopii zapasowych i przywracania została ulepszona w przypadku urządzeń iSCSI i serwera plików.

 - **Poprawa wyrzucania elementów bezużytecznych** — ta wersja zawiera poprawki błędów, które zwiększają wydajność cyklu odzyskiwania pamięci, gdy urządzenie i konto magazynu znajdują się w dwóch odległych regionach.

 - **Poprawianie rejestrowania** — ta wersja zawiera ulepszenia dotyczące rejestrowania związanego z odzyskiwaniem pamięci i ścieżką we/wy.


## <a name="issues-fixed-in-update-10"></a>Problemy rozwiązane w aktualizacji 1,0

Poniższa tabela zawiera podsumowanie problemów rozwiązanych w tej wersji.

| Nie. | Cecha | Problem |
| --- | --- | --- |
| 1 |Uwierzytelnianie oparte na usłudze AAD| Ta wersja zawiera zmiany, które umożliwiają usłudze AAD uwierzytelnianie przy użyciu Menedżer urządzeń StorSimple.|
| 2 |Wyrzucanie elementów bezużytecznych| Ten problem został zgłoszony w witrynie klienta, w której konta urządzeń i magazynu znajdują się w różnych regionach, a klient zgłosił sporadyczne błędy sieci, co wpływa na rozliczenia. W tej wersji ten problem został rozwiązany. |
| 3 |Wydajność| Ta wersja zawiera zmiany powodujące zwiększenie wydajności operacji przywracania/odczytu w chmurze/warstwy na poziomie.|
| 4 |Aktualizacja| Wystąpił problem z aktualizacją we wcześniejszej wersji, co spowodowało błędy kopii zapasowej w lokacji klienta. Ten problem został rozwiązany w tej wersji.|

## <a name="known-issues-in-update-10"></a>Znane problemy w aktualizacji 1,0

Poniższa tabela zawiera podsumowanie znanych problemów dotyczących macierzy wirtualnej StorSimple i obejmuje wydanie wydań z poprzednich wersji.

| Nie. | Cecha | Problem | Obejście/Komentarze |
| --- | --- | --- | --- |
| **1.** |Aktualizacje |Nie można zaktualizować macierzy wirtualnych utworzonych w wersji zapoznawczej do obsługiwanej ogólnej wersji dostępności. |Te tablice wirtualne muszą zostać przełączone w tryb failover w celu udostępnienia ogólnej wersji dostępności przy użyciu przepływu pracy odzyskiwania po awarii (DR). |
| **2.** |Dysk danych z zainicjowaną obsługą |Po zainicjowaniu obsługi dysku danych o określonym określonym rozmiarze i utworzeniu odpowiedniej macierzy wirtualnej StorSimple nie należy rozwijać ani zmniejszać dysku z danymi. Próba wykonania spowoduje utratę wszystkich danych w warstwach lokalnych urządzenia. | |
| **3.** |Zasady grupy |Gdy urządzenie jest przyłączone do domeny, zastosowanie zasad grupy może niekorzystnie wpłynąć na działanie urządzenia. |Upewnij się, że tablica wirtualna ma własną jednostkę organizacyjną (OU) dla Active Directory i nie są do niej stosowane obiekty zasad grupy (GPO). |
| **czwart.** |Lokalny interfejs użytkownika sieci Web |Jeśli ulepszone funkcje zabezpieczeń są włączone w programie Internet Explorer (IE ESC), niektóre strony lokalnego interfejsu użytkownika sieci Web, takie jak Rozwiązywanie problemów lub konserwacja, mogą nie funkcjonować prawidłowo. Przyciski na tych stronach mogą również nie funkcjonować. |Wyłącz ulepszone funkcje zabezpieczeń w programie Internet Explorer. |
| **5000.** |Lokalny interfejs użytkownika sieci Web |W maszynie wirtualnej funkcji Hyper-V interfejsy sieciowe w interfejsie użytkownika sieci Web są wyświetlane jako interfejsy 10 GB/s. |To zachowanie jest odbiciem funkcji Hyper-V. Funkcja Hyper-V zawsze pokazuje 10 GB/s dla wirtualnych kart sieciowych. |
| **ust.** |Woluminy warstwowe lub udziały |Blokowanie zakresu bajtów dla aplikacji współpracujących z woluminami warstwowymi StorSimple nie jest obsługiwane. W przypadku włączenia blokowania zakresu bajtów nie działa StorSimple. |Zalecane miary obejmują: <br></br>Wyłącz blokowanie zakresu bajtów w logice aplikacji.<br></br>Wybierz opcję umieszczenia danych dla tej aplikacji na woluminach przypiętych lokalnie, zamiast woluminów warstwowych.<br></br>*Zastrzeżenie*: w przypadku używania woluminów przypiętych lokalnie i blokowania zakresu bajtów wolumin przypięty lokalnie może być w trybie online nawet przed ukończeniem przywracania. W takich przypadkach, jeśli przywracanie jest w toku, należy poczekać na ukończenie przywracania. |
| **7.** |Udziały warstwowe |Praca z dużymi plikami może spowodować spowolnienie warstwy. |Podczas pracy z dużymi plikami zalecamy, aby największy plik był mniejszy niż 3% rozmiaru udziału. |
| **0,8.** |Używana pojemność dla udziałów |Użycie udostępniania może być widoczne, gdy nie ma żadnych danych w udziale. To zużycie wynika z faktu, że używana pojemność udziałów obejmuje metadane. | |
| **9.** |Odzyskiwanie po awarii |Odzyskiwanie po awarii serwera plików można wykonać tylko w tej samej domenie, w której znajduje się urządzenie źródłowe. Odzyskiwanie po awarii na urządzenie docelowe w innej domenie nie jest obsługiwane w tej wersji. |Ta implementacja jest zaimplementowana w nowszej wersji. Aby uzyskać więcej informacji, przejdź do [trybu failover i odzyskiwania po awarii dla macierzy wirtualnej StorSimple](storsimple-virtual-array-failover-dr.md) |
| **dziesięć.** |Azure PowerShell |Nie można zarządzać macierzami wirtualnymi StorSimple za pomocą Azure PowerShell w tej wersji. |Wszystkie zarządzanie urządzeniami wirtualnymi powinno odbywać się za pomocą Azure Portal i lokalnego interfejsu użytkownika sieci Web. |
| **11.** |Zmiana hasła |Konsola urządzenia macierzy wirtualnej akceptuje tylko dane wejściowe w formacie klawiatury en-us. | |
| **dwunastomiesięcznych.** |CHAP |Nie można usunąć poświadczeń protokołu CHAP po utworzeniu. Ponadto w przypadku modyfikacji poświadczeń protokołu CHAP należy przełączyć woluminy do trybu offline, a następnie przełączyć je w tryb online, aby zmiany zaczęły obowiązywać. |Ten problem został rozwiązany w nowszej wersji. |
| **trzynast.** |serwer iSCSI |"Użyty magazyn" wyświetlany dla woluminu iSCSI może się różnić w StorSimple usługi Menedżer urządzeń i hoście iSCSI. |Host iSCSI zawiera widok systemu plików.<br></br>Urządzenie widzi bloki przydzieloną, gdy wolumin był w maksymalnym rozmiarze. |
| **14,5.** |Serwer plików |Jeśli do pliku w folderze są skojarzone alternatywne strumienie danych (ADS), nie są one tworzone ani przywracane w ramach odzyskiwania po awarii, klonowania i odzyskiwania na poziomie elementu. | |
| **15000.** |Serwer plików |Linki symboliczne nie są obsługiwane. | |
| **16.** |Serwer plików |Pliki chronione przez system Windows system szyfrowania plików (EFS) kopiowane lub przechowywane na serwerze plików macierzy wirtualnej StorSimple powodują nieobsługiwaną konfigurację.  | |
| **7.** |Aktualizacje |Jeśli widzisz kod błędu: 2359302 (szesnastkowo 0x240006) podczas próby zainstalowania poprawki za pomocą lokalnego interfejsu użytkownika, oznacza to, że poprawka jest już zainstalowana na urządzeniu.   | |
| **postanowienia.** |Aktualizacje |Jeśli używasz lokalnego interfejsu użytkownika sieci Web do zainstalowania aktualizacji Update 1 w macierzy wirtualnej, musisz upewnić się, że jest uruchomiona Aktualizacja 0,6. W przypadku korzystania z wersji niższej niż aktualizacja 0,6 należy najpierw zainstalować aktualizację 0,6, a następnie zastosować aktualizację Update 1. W przypadku bezpośredniej instalacji aktualizacji 1,0 z wersji sprzed aktualizacji 0,6 wszystkie aktualizacje zostaną pominięte, a wykresy monitorowania nie będą działały.   | |


## <a name="next-steps"></a>Następne kroki
[Zainstaluj aktualizację 1,0](storsimple-virtual-array-install-update-1.md) na wirtualnej macierzy StorSimple.

## <a name="references"></a>Odwołania
Szukasz starszej wersji uwagi? Przejdź do strony:
*  [StorSimple Virtual Array Update 0,6 — informacje o wersji](storsimple-virtual-array-update-06-release-notes.md)
* [StorSimple Virtual Array Update 0,5 — informacje o wersji](storsimple-virtual-array-update-05-release-notes.md)
* [StorSimple Virtual Array Update 0,4 — Informacje o wersji](storsimple-virtual-array-update-04-release-notes.md)
* [StorSimple Virtual Array Update 0,3 — informacje o wersji](storsimple-ova-update-03-release-notes.md)
* [StorSimple Virtual Array Update 0,1 i 0,2 — informacje o wersji](storsimple-ova-update-01-release-notes.md)
* [Informacje o wersji ogólnej dostępności macierzy wirtualnej StorSimple](storsimple-ova-pp-release-notes.md)
