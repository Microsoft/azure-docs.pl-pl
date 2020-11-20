---
title: Co to jest StorSimple Snapshot Manager? | Microsoft Docs
description: Opisuje Snapshot Manager StorSimple, jego architekturę i jej funkcje.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 6094c31e-e2d9-4592-8a15-76bdcf60a754
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2214f085c30419cefb3f6f84139d5592873173f7
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94960125"
---
# <a name="an-introduction-to-storsimple-snapshot-manager"></a>Wprowadzenie do StorSimple Snapshot Manager

## <a name="overview"></a>Omówienie
StorSimple Snapshot Manager jest przystawką programu Microsoft Management Console (MMC), która upraszcza ochronę danych i zarządzanie kopiami zapasowymi w środowisku Microsoft Azure StorSimple. Dzięki StorSimple Snapshot Manager możesz zarządzać danymi Microsoft Azure StorSimple w centrum danych i w chmurze jako jednym zintegrowanym rozwiązaniu magazynu, co upraszcza proces tworzenia kopii zapasowych i obniżanie kosztów.

W tym przeglądzie wprowadzono Snapshot Manager StorSimple, opisano jej funkcje i wyjaśniono jej rolę w Microsoft Azure StorSimple. 

Aby zapoznać się z omówieniem całego systemu Microsoft Azure StorSimple, w tym urządzenia StorSimple, StorSimple Manager Service, StorSimple Snapshot Manager i StorSimple adapter for SharePoint, zobacz [StorSimple 8000 Series: rozwiązanie hybrydowego magazynu w chmurze](storsimple-overview.md). 

> [!NOTE]
> * Nie można użyć Snapshot Manager StorSimple do zarządzania Microsoft Azure StorSimplemi macierzami wirtualnymi (znanymi również jako StorSimple lokalne urządzenia wirtualne).
> * Jeśli planujesz zainstalować aktualizację StorSimple Update 2 na urządzeniu StorSimple, pamiętaj, aby pobrać najnowszą wersję StorSimple Snapshot Manager i zainstalować ją **przed zainstalowaniem StorSimple Update 2**. Najnowsza wersja StorSimple Snapshot Manager jest zgodna z poprzednią wersją i współpracuje ze wszystkimi wersjami Microsoft Azure StorSimple. Jeśli używasz wcześniejszej wersji programu StorSimple Snapshot Manager, musisz ją zaktualizować (nie musisz odinstalować poprzedniej wersji przed zainstalowaniem nowej wersji).
> 
> 

## <a name="storsimple-snapshot-manager-purpose-and-architecture"></a>StorSimple Snapshot Manager cel i architektura
StorSimple Snapshot Manager udostępnia centralną konsolę zarządzania, która służy do tworzenia spójnych kopii zapasowych danych lokalnych i w danym momencie. Można na przykład użyć konsoli programu, aby:

* Konfigurowanie, tworzenie kopii zapasowych i usuwanie woluminów.
* Skonfiguruj grupy woluminów, aby upewnić się, że kopia zapasowa danych jest spójna z aplikacją.
* Zarządzanie zasadami tworzenia kopii zapasowych, aby utworzyć kopię zapasową danych zgodnie z ustalonym harmonogramem.
* Twórz migawki lokalne i w chmurze, które mogą być przechowywane w chmurze i używane na potrzeby odzyskiwania po awarii.

StorSimple Snapshot Manager pobiera listę aplikacji zarejestrowanych z dostawcą usługi VSS na hoście. Następnie, aby utworzyć kopie zapasowe spójne z aplikacją, sprawdza woluminy używane przez aplikację i sugeruje grupy woluminów do skonfigurowania. StorSimple Snapshot Manager używa tych grup woluminów do generowania kopii zapasowych, które są spójne z aplikacjami. (Spójność aplikacji istnieje, gdy wszystkie powiązane pliki i bazy danych są zsynchronizowane i reprezentują prawdziwą kondycję aplikacji w określonym momencie). 

StorSimple Snapshot Manager backups przyjmują postać migawek przyrostowych, które przechwytują tylko zmiany od momentu utworzenia ostatniej kopii zapasowej. W związku z tym kopie zapasowe wymagają mniejszej ilości miejsca do magazynowania i można je szybko utworzyć i przywrócić. StorSimple Snapshot Manager używa Usługa kopiowania woluminów w tle systemu Windows (VSS), aby upewnić się, że migawki przechwytują dane spójne na poziomie aplikacji. (Aby uzyskać więcej informacji, przejdź do sekcji integracja z Usługa kopiowania woluminów w tle systemu Windows). Za pomocą StorSimple Snapshot Manager można tworzyć harmonogramy tworzenia kopii zapasowych lub wykonywać natychmiastowe kopie zapasowe zgodnie z wymaganiami. Aby przywrócić dane z kopii zapasowej, StorSimple Snapshot Manager umożliwia wybranie z wykazu migawek lokalnych lub w chmurze. Usługa Azure StorSimple przywraca tylko te dane, które są niezbędne, co jest niezbędne, co zapobiega opóźnieniu dostępności danych podczas operacji przywracania.

![Architektura Snapshot Manager StorSimple](./media/storsimple-what-is-snapshot-manager/HCS_SSM_Overview.png)

**Architektura Snapshot Manager StorSimple** 

## <a name="support-for-multiple-volume-types"></a>Obsługa wielu typów woluminów
Za pomocą Snapshot Manager StorSimple można konfigurować i tworzyć kopie zapasowe następujących typów woluminów: 

* **Woluminy podstawowe** — wolumin podstawowy to pojedyncza partycja na dysku podstawowym. 
* Woluminy **proste** — wolumin prosty to wolumin dynamiczny, który zawiera miejsce na dysku z pojedynczego dysku dynamicznego. Wolumin prosty składa się z jednego regionu na dysku lub wielu regionów połączonych ze sobą na tym samym dysku. (Woluminy proste można tworzyć tylko na dyskach dynamicznych). Woluminy proste nie są odporne na uszkodzenia.
* **Woluminy dynamiczne** — wolumin dynamiczny to wolumin utworzony na dysku dynamicznym. Dyski dynamiczne używają bazy danych do śledzenia informacji o woluminach, które są zawarte na dyskach dynamicznych na komputerze. 
* **Woluminy dynamiczne ze dublowaniem** — woluminy dynamiczne ze dublowaniem są oparte na architekturze RAID 1. W przypadku macierzy RAID 1 identyczne dane są zapisywane na co najmniej dwóch dyskach, co powoduje wygenerowanie dublowanego zestawu. Żądanie odczytu można następnie obsługiwać na dowolnym dysku zawierającym żądane dane.
* **Woluminy udostępnione klastra** — z udostępnionymi woluminami klastra (CSV) wiele węzłów w klastrze trybu failover może jednocześnie odczytywać lub zapisywać dane na tym samym dysku. Przejście w tryb failover z jednego węzła do innego może odbywać się szybko, bez konieczności zmiany prawa własności dysku lub instalowania, odinstalowywania i usuwania woluminu. 

> [!IMPORTANT]
> W tej samej migawce nie należy mieszać CSV i innych niż CSV. Mieszanie CSV i innych niż CSV w migawce nie jest obsługiwane. 
> 
> 

StorSimple Snapshot Manager służy do przywracania całej grupy woluminów lub klonowania poszczególnych woluminów i odzyskiwania pojedynczych plików.

* [Woluminy i grupy woluminów](#volumes-and-volume-groups) 
* [Typy kopii zapasowych i zasady tworzenia kopii zapasowych](#backup-types-and-backup-policies) 

Aby uzyskać więcej informacji na temat funkcji StorSimple Snapshot Manager i sposobu ich używania, zobacz [StorSimple Snapshot Manager interfejsu użytkownika](storsimple-use-snapshot-manager.md).

## <a name="volumes-and-volume-groups"></a>Woluminy i grupy woluminów
Program StorSimple Snapshot Manager tworzy woluminy, a następnie konfiguruje je w grupach woluminów. 

StorSimple Snapshot Manager używa grup woluminów do tworzenia kopii zapasowych, które są spójne z aplikacjami. Spójność aplikacji istnieje, gdy wszystkie powiązane pliki i bazy danych są zsynchronizowane i reprezentują prawdziwą kondycję aplikacji w określonym punkcie czasu. Grupy woluminów (które są również znane jako *grupy spójności*) stanowią podstawę zadania tworzenia kopii zapasowej lub przywracania.

Grupy woluminów nie są takie same jak kontenery woluminów. Kontener woluminów zawiera jeden lub więcej woluminów, które współużytkują konto magazynu w chmurze i inne atrybuty, takie jak szyfrowanie i użycie przepustowości. Kontener pojedynczego woluminu może zawierać maksymalnie 256 alokowanych elastycznie woluminów StorSimple. Aby uzyskać więcej informacji na temat kontenerów woluminów, przejdź do obszaru [Zarządzanie kontenerami woluminów](./storsimple-8000-manage-volume-containers.md). Grupy woluminów są kolekcjami woluminów skonfigurowanych w celu ułatwienia operacji tworzenia kopii zapasowych. W przypadku wybrania dwóch woluminów należących do różnych kontenerów woluminów należy umieścić je w pojedynczej grupie woluminów, a następnie utworzyć zasady tworzenia kopii zapasowej dla tej grupy woluminów. kopia zapasowa każdego woluminu zostanie utworzona w odpowiednim kontenerze woluminów przy użyciu odpowiedniego konta magazynu.

> [!NOTE]
> Wszystkie woluminy w grupie woluminów muszą pochodzić od jednego dostawcy usług w chmurze.
> 
> 

## <a name="integration-with-windows-volume-shadow-copy-service"></a>Integracja z systemem Windows Usługa kopiowania woluminów w tle
StorSimple Snapshot Manager używa Usługa kopiowania woluminów w tle systemu Windows (VSS) do przechwytywania danych spójnych z zastosowaniem aplikacji. Usługa VSS ułatwia spójność aplikacji przez komunikowanie się z aplikacjami obsługującymi usługę VSS w celu koordynowania tworzenia migawek przyrostowych. Usługa VSS zapewnia, że aplikacje są tymczasowo nieaktywne lub quiescent, gdy są wykonywane migawki. 

Implementacja StorSimple Snapshot Manager usługi VSS współdziała z SQL Server i ogólnymi woluminami NTFS. Przebieg procesu: 

1. Obiekt żądający, który jest zazwyczaj rozwiązaniem do zarządzania danymi i ich ochrony (na przykład StorSimple Snapshot Manager) lub aplikacji do tworzenia kopii zapasowych, wywołuje usługę VSS i prosi o zebranie informacji z oprogramowania zapisywania w aplikacji docelowej.
2. Usługa VSS kontaktuje się ze składnikiem składnika zapisywania w celu pobrania opisu danych. Moduł zapisujący zwraca opis danych, których kopia zapasowa ma zostać utworzona. 
3. Usługa VSS sygnalizuje składnik zapisywania, aby przygotować aplikację do tworzenia kopii zapasowych. Moduł zapisujący przygotowuje dane do kopii zapasowej przez wykonanie otwartych transakcji, zaktualizowanie dzienników transakcji itd., a następnie powiadamia o usłudze VSS.
4. Usługa VSS instruuje moduł zapisujący, aby tymczasowo zatrzymał magazyny danych aplikacji, i upewnić się, że podczas tworzenia kopii w tle nie są zapisywane żadne dane na woluminie. Ten krok zapewnia spójność danych i nie przekracza 60 sekund.
5. Usługa VSS instruuje dostawcę o utworzenie kopii w tle. Dostawcy, którzy mogą być oprogramowaniem lub sprzętowym, zarządzać aktualnie uruchomionymi woluminami i tworzyć na żądanie kopie w tle. Dostawca tworzy kopię w tle i powiadamia usługę VSS po jej zakończeniu.
6. Usługa VSS kontaktuje się z modułem zapisywania w celu powiadomienia aplikacji o tym, że operacje we/wy można wznowić, a także potwierdzić pomyślne wstrzymanie operacji we/wy podczas tworzenia kopii w tle. 
7. Jeśli kopia zakończyła się pomyślnie, usługa VSS zwraca lokalizację kopii do osoby żądającej. 
8. Jeśli podczas tworzenia kopii w tle zapisano dane, kopia zapasowa nie będzie spójna. Usługa VSS usuwa kopię w tle i powiadamia obiekt żądający. Obiekt żądający może powtórzyć proces tworzenia kopii zapasowej automatycznie lub poprosić administratora o ponowne ponowienie próby.

Zapoznaj się z poniższą ilustracją.

![Proces VSS](./media/storsimple-what-is-snapshot-manager/HCS_SSM_VSS_process.png)

**Proces Usługa kopiowania woluminów w tle systemu Windows** 

## <a name="backup-types-and-backup-policies"></a>Typy kopii zapasowych i zasady tworzenia kopii zapasowych
Za pomocą StorSimple Snapshot Manager można tworzyć kopie zapasowe danych i przechowywać je lokalnie i w chmurze. Można użyć Snapshot Manager StorSimple do natychmiastowego utworzenia kopii zapasowej danych lub użyć zasad tworzenia kopii zapasowej w celu utworzenia harmonogramu automatycznego wykonywania kopii zapasowych. Zasady tworzenia kopii zapasowych umożliwiają również określenie liczby zachowywanych migawek. 

### <a name="backup-types"></a>Typy kopii zapasowych
Za pomocą Snapshot Manager StorSimple można tworzyć następujące typy kopii zapasowych:

* **Migawki lokalne** — migawki lokalne to kopie danych woluminu do punktu w czasie przechowywane na urządzeniu StorSimple. Zazwyczaj ten typ kopii zapasowej można utworzyć i przywrócić szybko. Możesz użyć lokalnej migawki w taki sam sposób, jak w przypadku lokalnej kopii zapasowej.
* **Migawki w chmurze** — migawki w chmurze to kopie danych woluminu w danym momencie, które są przechowywane w chmurze. Migawka w chmurze jest równoważna z migawką replikowaną w innym systemie magazynu poza lokacją. Migawki w chmurze są szczególnie przydatne w scenariuszach odzyskiwania po awarii.

### <a name="on-demand-and-scheduled-backups"></a>Kopie zapasowe na żądanie i zaplanowane
Za pomocą StorSimple Snapshot Manager można zainicjować jednorazową kopię zapasową do natychmiastowego utworzenia lub można użyć zasad tworzenia kopii zapasowych, aby zaplanować cykliczne operacje tworzenia kopii zapasowych.

Zasady tworzenia kopii zapasowych to zestaw zautomatyzowanych reguł, których można użyć do planowania zwykłych kopii zapasowych. Zasady tworzenia kopii zapasowych umożliwiają zdefiniowanie częstotliwości i parametrów w celu utworzenia migawek określonej grupy woluminów. Za pomocą zasad można określić daty rozpoczęcia i wygaśnięcia, czasy, częstotliwości i wymagania dotyczące przechowywania, zarówno dla migawek lokalnych, jak i w chmurze. Zasady są stosowane natychmiast po jego zdefiniowaniu. 

W razie potrzeby można użyć Snapshot Manager StorSimple do konfigurowania lub ponownego konfigurowania zasad tworzenia kopii zapasowych. 

Należy skonfigurować następujące informacje dla każdej utworzonej zasady tworzenia kopii zapasowych:

* **Nazwa** — unikatowa nazwa wybranych zasad tworzenia kopii zapasowych.
* **Typ** — typ zasad tworzenia kopii zapasowych; migawka lokalna lub migawka w chmurze.
* **Grupa woluminów** — Grupa woluminów, do której przypisane są wybrane zasady tworzenia kopii zapasowych.
* **Przechowywanie** — liczba kopii zapasowych zachowywanych. Po zaznaczeniu pola **wszystkie** kopie zapasowe są zachowywane do momentu osiągnięcia maksymalnej liczby kopii zapasowych na wolumin, a w tym momencie zasady zakończą się niepowodzeniem i generują komunikat o błędzie. Alternatywnie możesz określić liczbę kopii zapasowych do zachowania (od 1 do 64).
* **Data** — Data utworzenia zasad tworzenia kopii zapasowych.

Aby uzyskać informacje o konfigurowaniu zasad tworzenia kopii zapasowych, przejdź do [StorSimple Snapshot Manager do tworzenia zasad tworzenia kopii zapasowych i zarządzania nimi](storsimple-snapshot-manager-manage-backup-policies.md).

### <a name="backup-job-monitoring-and-management"></a>Monitorowanie i zarządzanie zadaniami tworzenia kopii zapasowej
Snapshot Manager StorSimple służy do monitorowania nadchodzących, zaplanowanych i zakończonych zadań tworzenia kopii zapasowych oraz zarządzania nimi. Ponadto StorSimple Snapshot Manager udostępnia wykaz do 64 ukończonych kopii zapasowych. Wykazu można używać do znajdowania i przywracania woluminów lub pojedynczych plików. 

Aby uzyskać informacje o monitorowaniu zadań tworzenia kopii zapasowych, przejdź do [StorSimple Snapshot Manager do wyświetlania zadań tworzenia kopii zapasowych i zarządzania nimi](storsimple-snapshot-manager-manage-backup-jobs.md).

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [korzystaniu z usługi StorSimple Snapshot Manager do administrowania rozwiązaniem StorSimple](storsimple-snapshot-manager-admin.md).
* Pobierz [Snapshot Manager StorSimple](https://www.microsoft.com/download/details.aspx?id=44220).