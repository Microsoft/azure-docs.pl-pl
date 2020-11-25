---
title: Tryb failover i odzyskiwanie po awarii dla urządzenia z serii StorSimple 8000
description: Dowiedz się, jak przełączać Urządzenie StorSimple do trybu failover, z innego urządzenia fizycznego lub urządzenia w chmurze.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/03/2017
ms.author: alkohli
ms.openlocfilehash: dffa059b18e159d04b5e3bb8555dabf801ede692
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017121"
---
# <a name="failover-and-disaster-recovery-for-your-storsimple-8000-series-device"></a>Przechodzenie w tryb failover i odzyskiwanie po awarii dla urządzenia StorSimple z serii 8000

## <a name="overview"></a>Omówienie

W tym artykule opisano funkcję przełączenia urządzenia w tryb failover dla urządzeń z serii StorSimple 8000 i sposobu jej użycia w celu odzyskania urządzeń StorSimple w przypadku wystąpienia awarii. StorSimple używa trybu failover urządzenia do migrowania danych z urządzenia źródłowego w centrum danych do innego urządzenia docelowego. Wskazówki zawarte w tym artykule dotyczą urządzeń fizycznych z serii StorSimple 8000 i urządzeń w chmurze z uruchomionymi wersjami oprogramowania Update 3 i nowszymi.

StorSimple używa bloku **Devices** do uruchomienia funkcji przełączania do trybu failover urządzenia w przypadku awarii. Ten blok zawiera listę wszystkich urządzeń StorSimple podłączonych do usługi StorSimple Menedżer urządzeń.

![Blok urządzeń](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev1.png)


## <a name="disaster-recovery-dr-and-device-failover"></a>Odzyskiwanie po awarii (DR) i tryb failover urządzenia

W scenariuszu odzyskiwania po awarii urządzenie podstawowe przestaje działać. StorSimple używa urządzenia podstawowego jako _źródła_ i przenosi skojarzone dane w chmurze na inne urządzenie _docelowe_ . Ten proces jest określany jako *tryb failover*. Poniższa ilustracja ilustruje proces trybu failover.

![Co się dzieje w przypadku przełączenia urządzenia w tryb failover?](./media/storsimple-8000-device-failover-disaster-recovery/failover-dr-flow.png)

Urządzenie docelowe dla trybu failover może być urządzeniem fizycznym, a nawet urządzeniem w chmurze. Urządzenie docelowe może znajdować się w tej samej lub innej lokalizacji geograficznej niż urządzenie źródłowe.

Podczas pracy w trybie failover można wybrać kontenery woluminów do migracji. StorSimple następnie zmienia własność tych kontenerów woluminów z urządzenia źródłowego na urządzenie docelowe. Gdy kontenery woluminów zmienią własność, StorSimple usuwa te kontenery z urządzenia źródłowego. Po zakończeniu usuwania można wrócić do urządzenia docelowego. _Powrót po awarii_ przenosi własność z powrotem na oryginalne urządzenie źródłowe.

### <a name="cloud-snapshot-used-during-device-failover"></a>Migawka w chmurze używana podczas przełączania do trybu failover urządzenia

Po uruchomieniu programu DR najnowsza kopia zapasowa w chmurze jest używana do przywracania danych na urządzeniu docelowym. Aby uzyskać więcej informacji na temat migawek w chmurze, zobacz [Ręczne tworzenie kopii zapasowej za pomocą usługi StorSimple Menedżer urządzeń](storsimple-8000-manage-backup-policies-u2.md#take-a-manual-backup).

W serii StorSimple 8000 zasady tworzenia kopii zapasowych są skojarzone z kopiami zapasowymi. Jeśli istnieje wiele zasad tworzenia kopii zapasowych dla tego samego woluminu, StorSimple wybiera zasady tworzenia kopii zapasowych z największą liczbą woluminów. StorSimple następnie używa najnowszej kopii zapasowej z wybranych zasad tworzenia kopii zapasowej w celu przywrócenia danych na urządzeniu docelowym.

Załóżmy, że istnieją dwie zasady tworzenia kopii zapasowych, *defaultPol* i *customPol*:

* *defaultPol*: jeden wolumin, *vol1*, działa codziennie, zaczynając od 10:30 PM.
* *customPol*: cztery woluminy, *vol1*, *VOL2*, *vol3*, *VOL4*, uruchamiane codziennie, zaczynając od 10:00 PM.

W tym przypadku StorSimple priorytety dla spójności awaryjnej i używa *customPol* , ponieważ zawiera więcej woluminów. Najnowsza kopia zapasowa z tych zasad jest używana do przywracania danych. Aby uzyskać więcej informacji na temat tworzenia zasad tworzenia kopii zapasowych i zarządzania nimi, przejdź do, aby [zarządzać zasadami tworzenia kopii zapasowych za pomocą usługi StorSimple Menedżer urządzeń](storsimple-8000-manage-backup-policies-u2.md).

## <a name="common-considerations-for-device-failover"></a>Typowe zagadnienia dotyczące trybu failover urządzenia

Przed przejściem do trybu failover na urządzeniu zapoznaj się z następującymi informacjami:

* Przed rozpoczęciem pracy w trybie failover urządzenia wszystkie woluminy w kontenerach woluminów muszą być w trybie offline. W przypadku nieplanowanego przejścia w tryb failover woluminy StotSimple będą przełączane do trybu offline. Ale jeśli wykonujesz planowaną pracę w trybie failover (w celu przetestowania programu DR), musisz przełączyć wszystkie woluminy w tryb offline.
* Na potrzeby odzyskiwania po awarii są wyświetlane tylko kontenery woluminów ze skojarzoną migawką w chmurze. Aby odzyskać dane, musi istnieć co najmniej jeden kontener woluminów ze skojarzoną migawką w chmurze.
* W przypadku migawek chmurowych obejmujących wiele kontenerów woluminów StorSimple przejdzie w tryb failover w ramach zestawu. W rzadkich przypadkach, jeśli istnieją migawki lokalne, które obejmują wiele kontenerów woluminów, ale skojarzone migawki chmury nie StorSimple, przechodzą w tryb failover migawek lokalnych, a dane lokalne są tracone po rozpoczęciu odzyskiwania po awarii.
* Dostępne urządzenia docelowe dla odzyskiwania po awarii to urządzenia z wystarczającą ilością miejsca, aby pomieścić wybrane kontenery woluminów. Wszystkie urządzenia, które nie mają wystarczającej ilości miejsca, nie są wyświetlane jako urządzenia docelowe.
* Po rozpoczęciu odzyskiwania po awarii (przez ograniczony czas) wydajność dostępu do danych może być istotna, ponieważ urządzenie musi uzyskać dostęp do danych z chmury i przechowywać je lokalnie.

#### <a name="device-failover-across-software-versions"></a>Tryb failover urządzenia w różnych wersjach oprogramowania

Usługa StorSimple Menedżer urządzeń w ramach wdrożenia może mieć wiele urządzeń, zarówno fizycznych, jak i w chmurze, a wszystkie uruchomione różne wersje oprogramowania.

Skorzystaj z poniższej tabeli, aby określić, czy można przełączyć się w tryb failover lub wrócić do trybu failover na innym urządzeniu z uruchomioną inną wersją oprogramowania i jak typy woluminów zachowują się podczas odzyskiwania po awarii.

#### <a name="failover-and-failback-across-software-versions"></a>Przełączanie do trybu failover i powrotu po awarii w różnych wersjach oprogramowania

| Przełączenie w tryb failover/powrót po awarii z | Urządzenie fizyczne | Urządzenie w chmurze |
| --- | --- | --- |
| Aktualizacja Update 4 |Woluminy warstwowe są przełączane w tryb failover jako warstwowe. <br></br>Woluminy przypięte lokalnie są bezawaryjnie przypięte lokalnie. <br></br> Po przejściu w tryb failover podczas tworzenia migawki na urządzeniu z aktualizacją Update 4 [śledzenie mapę cieplną](storsimple-update4-release-notes.md#whats-new-in-update-4) jest uruchamiane w systemie. |Woluminy przypięte lokalnie są przełączane do trybu failover. |
| Aktualizacja Update 4 |Woluminy warstwowe są przełączane w tryb failover jako warstwowe. <br></br>Woluminy przypięte lokalnie są bezawaryjnie przypięte lokalnie. <br></br> Kopie zapasowe używane do przywracania przechowują metadane mapę cieplną. <br></br>Śledzenie na podstawie mapę cieplną nie jest dostępne w Update 3 po awarii. |Woluminy przypięte lokalnie są przełączane do trybu failover. |


## <a name="device-failover-scenarios"></a>Scenariusze trybu failover urządzenia

Jeśli wystąpi awaria, możesz zdecydować się na przełączenie urządzenia z systemem StorSimple do trybu failover:

* [Na urządzenie fizyczne](storsimple-8000-device-failover-physical-device.md).
* [Do samego siebie](storsimple-8000-device-failover-same-device.md).
* [Z urządzeniem w chmurze](storsimple-8000-device-failover-cloud-appliance.md).

Powyższe artykuły zawierają szczegółowe instrukcje dla każdego z powyższych przypadków pracy awaryjnej.


## <a name="failback"></a>Powrót po awarii

W przypadku wersji Update 3 i nowszych StorSimple obsługuje również powrót po awarii. Powrót po awarii to przełączenie w tryb failover, a obiekt docelowy stanie się źródłem, a oryginalne urządzenie źródłowe w trakcie pracy w trybie failover stanie się urządzeniem docelowym. 

Podczas powrotu po awarii program StorSimple ponownie zsynchronizuje dane z powrotem z lokalizacją podstawową, zatrzymuje działanie we/wy i aplikacji, a następnie przechodzi z powrotem do oryginalnej lokalizacji.

Po zakończeniu pracy w trybie failover StorSimple wykonuje następujące czynności:

* StorSimple czyści kontenery woluminów przełączone do trybu failover z urządzenia źródłowego.
* StorSimple inicjuje zadanie w tle dla kontenera woluminów (przełączone do trybu failover) na urządzeniu źródłowym. Jeśli podjęto próbę powrotu po awarii, gdy zadanie jest w toku, zostanie wyświetlone powiadomienie. Poczekaj na zakończenie zadania w celu rozpoczęcia powrotu po awarii.
* Czas potrzebny do ukończenia usuwania kontenerów woluminów zależy od różnych czynników, takich jak ilość danych, wiek danych, liczba kopii zapasowych i przepustowość sieci dostępna dla operacji.

Jeśli planujesz test pracy w trybie failover lub test failbacks, zalecamy przetestowanie kontenerów woluminów z mniejszą ilością danych (GB). Zwykle można uruchomić 24 godziny powrotu po awarii po zakończeniu pracy w trybie failover.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

PYTANIE: **Co się stanie w przypadku niepowodzenia odzyskiwania po awarii lub jego częściowego sukcesu?**

A. Jeśli odzyskiwanie zakończy się niepowodzeniem, zalecamy ponowną próbę. Drugie zadanie trybu failover urządzenia jest świadome postępu pierwszego zadania i zaczyna się od tego momentu.

PYTANIE: **Czy mogę usunąć urządzenie, gdy trwa przełączanie do trybu failover urządzenia?**

A. Nie można usunąć urządzenia, gdy trwa odzyskiwanie po awarii. Urządzenie można usunąć tylko po zakończeniu odzyskiwania po awarii. Postęp zadania trybu failover urządzenia można monitorować w bloku **zadania** .

PYTANIE: **Kiedy odzyskiwanie pamięci zostanie rozpoczęte na urządzeniu źródłowym, aby dane lokalne na urządzeniu źródłowym zostały usunięte?**

A. Odzyskiwanie pamięci jest włączone na urządzeniu źródłowym dopiero po całkowitym oczyszczeniu urządzenia. Oczyszczanie obejmuje czyszczenie obiektów, które zostały przełączone w tryb failover z urządzenia źródłowego, takie jak woluminy, obiekty kopii zapasowej (nie dane), kontenery woluminów i zasady.

PYTANIE: **Co się stanie, jeśli zadanie usuwania skojarzone z kontenerami woluminów na urządzeniu źródłowym ulegnie awarii?**

A.  Jeśli zadanie usuwania zakończy się niepowodzeniem, można ręcznie usunąć kontenery woluminów. W bloku **urządzenia** wybierz urządzenie źródłowe, a następnie kliknij pozycję **kontenery woluminów**. Wybierz kontenery woluminów w trybie failover, a następnie w dolnej części bloku kliknij przycisk **Usuń**. Po usunięciu wszystkich kontenerów przełączenia w tryb failover na urządzeniu źródłowym można uruchomić powrót po awarii. Aby uzyskać więcej informacji, przejdź do obszaru [usuwanie kontenera woluminów](storsimple-8000-manage-volume-containers.md#delete-a-volume-container).

## <a name="business-continuity-disaster-recovery-bcdr"></a>Ciągłość działania — odzyskiwanie po awarii (BCDR)

Scenariusz ciągłości działania odzyskiwania po awarii (BCDR) występuje, gdy całe centrum danych platformy Azure przestanie działać. Ten scenariusz może mieć wpływ na usługę StorSimple Menedżer urządzeń i skojarzone urządzenia StorSimple.

Jeśli urządzenie StorSimple zostało zarejestrowane tuż przed wystąpieniem awarii, może być konieczne przeprowadzenie resetowania do ustawień fabrycznych. Po awarii Urządzenie StorSimple zostanie wyświetlone w Azure Portal jako offline. To urządzenie musi zostać usunięte z portalu. Zresetuj urządzenie do domyślnych ustawień fabrycznych i zarejestruj je ponownie w usłudze.

## <a name="next-steps"></a>Następne kroki

Jeśli wszystko jest gotowe do przełączenia urządzenia w tryb failover, aby uzyskać szczegółowe instrukcje, wybierz jeden z następujących scenariuszy:

- [Przełączanie do trybu failover na innym urządzeniu fizycznym](storsimple-8000-device-failover-physical-device.md)
- [Przechodzenie w tryb failover na tym samym urządzeniu](storsimple-8000-device-failover-same-device.md)
- [Przechodzenie w tryb failover do urządzenia w chmurze StorSimple](storsimple-8000-device-failover-cloud-appliance.md)

Jeśli urządzenie zostało przełączone w tryb failover, wybierz jedną z następujących opcji:

* [Dezaktywuj lub Usuń urządzenie StorSimple](storsimple-8000-deactivate-and-delete-device.md).
* [Do administrowania urządzeniem StorSimple służy Usługa StorSimple Menedżer urządzeń](storsimple-8000-manager-service-administration.md).

