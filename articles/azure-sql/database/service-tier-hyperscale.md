---
title: Co to jest warstwa usługi Hiperskala?
description: W tym artykule opisano warstwę usługi Hiperskala w modelu zakupów opartym na rdzeniu wirtualnych w usłudze Azure SQL Database i wyjaśniono, czym różni się ona od warstwy usług Ogólnego przeznaczenia i Krytyczne dla działania firmy wirtualnej.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 1/13/2021
ms.openlocfilehash: e0982b4a43a931552574e447d5639d3fa92402d8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773776"
---
# <a name="hyperscale-service-tier"></a>Warstwa usługi Hiperskala

Azure SQL Database jest oparta na architekturze aparatu bazy danych SQL Server, która jest dostosowywana do środowiska chmury w celu zapewnienia dostępności na poziomie 99,99% nawet w przypadku awarii infrastruktury. Istnieją trzy modele architektoniczne, które są używane w Azure SQL Database:

- Ogólnego przeznaczenia/Standardowa
- Hiperskala
- Krytyczne dla działania firmy/Premium

Warstwa usługi Hiperskala w Azure SQL Database to najnowsza warstwa usługi w modelu zakupów opartym na rdzeniu wirtualnych. Ta warstwa usługi to wysoce skalowalna warstwa wydajności magazynu i zasobów obliczeniowych, która wykorzystuje architekturę platformy Azure do skalowania w poziomie zasobów magazynu i zasobów obliczeniowych dla maszyny wirtualnej Azure SQL Database znacznie wykraczających poza limity dostępne dla warstw usług Ogólnego przeznaczenia i Krytyczne dla działania firmy.

> [!NOTE]
>
> - Aby uzyskać szczegółowe informacje na temat Ogólnego przeznaczenia i Krytyczne dla działania firmy usług w modelu zakupów opartym na rdzeniu wirtualnych, zobacz Ogólnego przeznaczenia [i](service-tier-general-purpose.md) [Krytyczne dla działania firmy](service-tier-business-critical.md) usług. Aby porównać model zakupów oparty na rdzeniu wirtualnych z modelem zakupów opartym na jednostce DTU, zobacz Azure SQL Database modele zakupów [i zasoby.](purchasing-models.md)
> - Warstwa usługi Hiperskala jest obecnie dostępna tylko dla Azure SQL Database, a nie Azure SQL Managed Instance.

## <a name="what-are-the-hyperscale-capabilities"></a>Jakie są możliwości hiperskali

Warstwa usługi Hiperskala w Azure SQL Database zapewnia następujące dodatkowe możliwości:

- Obsługa do 100 TB rozmiaru bazy danych
- Niemal natychmiastowe kopie zapasowe bazy danych (na podstawie migawek plików przechowywanych w usłudze Azure Blob Storage) niezależnie od rozmiaru bez wpływu operacji we/wy na zasoby obliczeniowe  
- Szybkie przywracanie bazy danych (na podstawie migawek plików) w ciągu kilku minut, a nie godzin lub dni (a nie rozmiaru operacji na danych)
- Wyższa ogólna wydajność ze względu na wyższą przepływność dziennika i krótszy czas zatwierdzania transakcji niezależnie od woluminów danych
- Szybkie skalowanie w zewnątrz — można aprowizować co najmniej jeden węzeł tylko do odczytu na potrzeby odciążania obciążenia odczytu i do użycia jako rezerwy w trybie rezerwy gorącej
- Szybkie skalowanie w górę — w stałym czasie można skalować w górę zasoby obliczeniowe w celu obsługi dużych obciążeń w razie potrzeby, a następnie skalować zasoby obliczeniowe z powrotem w dół, gdy nie są potrzebne.

Warstwa usługi Hiperskala usuwa wiele praktycznych limitów tradycyjnie istniejących w bazach danych w chmurze. W przypadku większości innych baz danych ograniczonych zasobami dostępnymi w jednym węźle bazy danych w warstwie usługi Hiperskala nie mają takich limitów. Dzięki elastycznej architekturze magazynu magazyn rośnie w miarę potrzeb. W rzeczywistości bazy danych w hiperskali nie są tworzone ze zdefiniowanym maksymalnym rozmiarem. Baza danych w hiperskali rośnie w miarę potrzeb i są naliczane tylko za używaną pojemność. W przypadku obciążeń intensywnie obciążających odczyt warstwa usługi Hiperskala zapewnia szybkie skalowanie w poziomie przez aprowizowanie dodatkowych replik do odczytu w razie potrzeby w celu odciążania obciążeń odczytu.

Ponadto czas wymagany do utworzenia kopii zapasowych bazy danych lub skalowania w górę lub w dół nie jest już powiązany z woluminem danych w bazie danych. Kopię zapasową baz danych w hiperskali można utworzyć praktycznie natychmiast. Bazę danych można również skalować w dziesiątki terabajtów w górę lub w dół w ciągu kilku minut. Ta funkcja pozwala nie obaw o wstępne opcje konfiguracji.

Aby uzyskać więcej informacji na temat rozmiarów obliczeniowych dla warstwy usługi Hiperskala, zobacz [Charakterystyka warstwy usługi](service-tiers-vcore.md#service-tiers).

## <a name="who-should-consider-the-hyperscale-service-tier"></a>Kto powinien rozważyć warstwę usługi Hiperskala

Warstwa usługi Hiperskala jest przeznaczona dla większości obciążeń biznesowych, ponieważ zapewnia dużą elastyczność i wysoką wydajność dzięki niezależnie skalowalnym zasobom obliczeniowym i magazynowym. Dzięki możliwości automatycznego skalowania magazynu do 100 TB jest to doskonały wybór dla klientów, którzy:

- Lokalne duże bazy danych i chcesz zmodernizować swoje aplikacje, przenosząc je do chmury
- Znajdują się już w chmurze i są ograniczone przez maksymalne ograniczenia rozmiaru bazy danych innych warstw usług (1–4 TB)
- Mają mniejsze bazy danych, ale wymagają szybkiego skalowania obliczeń w pionie i poziomie, wysokiej wydajności, błyskawicznej kopii zapasowej i szybkiego przywracania bazy danych.

Warstwa usługi Hiperskala obsługuje szeroką gamę obciążeń SQL Server, od czystego przetwarzania OLTP po czystą analizę, ale jest zoptymalizowana głównie pod kątem przetwarzania OLTP oraz hybrydowych obciążeń przetwarzania transakcji i analiz (HTAP).

> [!IMPORTANT]
> Elastyczne pule nie obsługują warstwy usługi Hiperskala.

## <a name="hyperscale-pricing-model"></a>Model cen hiperskali

Warstwa usługi Hiperskala jest dostępna tylko w modelu [z rdzeniem wirtualnych.](service-tiers-vcore.md) Aby dostosować go do nowej architektury, model cenowy różni się nieco od Ogólnego przeznaczenia lub Krytyczne dla działania firmy usług:

- **Obliczenia:**

  Cena jednostki obliczeniowej hiperskali to za replikę. Cena [Korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) jest stosowana do replik skalowania odczytu automatycznie. Domyślnie tworzymy replikę podstawową i jedną replikę tylko do odczytu na bazę danych w hiperskali.  Użytkownicy mogą dostosować łączną liczbę replik, w tym replikę podstawową z 1–5.

- **Magazyn:**

  Podczas konfigurowania bazy danych w hiperskali nie trzeba określać maksymalnego rozmiaru danych. W warstwie Hiperskala opłata jest naliczana za magazyn bazy danych na podstawie rzeczywistej alokacji. Magazyn jest przydzielany automatycznie z przyrostami od 40 GB do 100 TB. W razie potrzeby można powiększać wiele plików danych w tym samym czasie. Baza danych w hiperskali jest tworzona z rozmiarem początkowym 10 GB i zaczyna rosnąć o 10 GB co 10 minut, dopóki nie osiągnie rozmiaru 40 GB.

Aby uzyskać więcej informacji na temat cen hiperskali, zobacz [cennik Azure SQL Database Hiperskala](https://azure.microsoft.com/pricing/details/sql-database/single/)

## <a name="distributed-functions-architecture"></a>Architektura funkcji rozproszonych

W przeciwieństwie do tradycyjnych aparatów baz danych, które scentralizowały wszystkie funkcje zarządzania danymi w jednej lokalizacji/procesie (nawet tak zwane rozproszone bazy danych w środowisku produkcyjnym mają obecnie wiele kopii monolitycznego aparatu danych), baza danych w chmurze w chmurze oddziela aparat przetwarzania zapytań, w którym semantyka różnych aparatów danych różni się od składników, które zapewniają długoterminowe przechowywanie i trwałość danych. W ten sposób pojemność magazynu można bezproblemowo skalować w miarę potrzeb (początkowy cel to 100 TB). Repliki tylko do odczytu współdzielą te same składniki magazynu, więc do udostępnienia nowej repliki możliwej do odczytu nie jest wymagana żadna kopia danych.

Na poniższym diagramie przedstawiono różne typy węzłów w bazie danych w hiperskali:

![architektura](./media/service-tier-hyperscale/hyperscale-architecture.png)

Baza danych w hiperskali zawiera następujące typy składników:

### <a name="compute"></a>Compute

Węzeł obliczeniowy to miejsce, w którym znajduje się aparat relacyjnych. Jest to miejsce, w którym odbywa się przetwarzanie języka, zapytania i transakcji. Wszystkie interakcje użytkownika z bazą danych w hiperskali odbywa się za pośrednictwem tych węzłów obliczeniowych. Węzły obliczeniowe mają pamięci podręczne oparte na dyskach SSD (z etykietą RBPEX — rozszerzenie puli buforów odpornego na błędy na powyższym diagramie), aby zminimalizować liczbę rund sieciowych wymaganych do pobrania strony danych. Istnieje jeden podstawowy węzeł obliczeniowy, w którym są przetwarzane wszystkie obciążenia i transakcje odczytu i zapisu. Istnieje co najmniej jeden pomocniczy węzeł obliczeniowy, który działa jako węzły rezerwy gorącej na potrzeby trybu failover, a także pełni funkcję węzłów obliczeniowych tylko do odczytu w celu odciążania obciążeń odczytu (jeśli ta funkcja jest pożądana).

Aparat bazy danych uruchomiony w węzłach obliczeniowych w warstwie Hiperskala jest taki sam jak w innych Azure SQL Database usług. Gdy użytkownicy wchodzą w interakcję z aparatem bazy danych w węzłach obliczeniowych w warstwie Hiperskala, obsługiwany obszar powierzchni i działanie aparatu są takie same jak w innych warstwach usług, z wyjątkiem [znanych ograniczeń.](#known-limitations)

### <a name="page-server"></a>Serwer stron

Serwery stronicowania to systemy reprezentujące skalowany aparat magazynu.  Każdy serwer stron jest odpowiedzialny za podzestaw stron w bazie danych.  Nominalnie każdy serwer stron kontroluje do 128 GB lub do 1 TB danych. Żadne dane nie są udostępniane na więcej niż jednym serwerze stronicowania (poza replikami serwerów stron, które są przechowywane w celu zapewnienia nadmiarowości i dostępności). Zadaniem serwera stron jest podsieć stron bazy danych do węzłów obliczeniowych na żądanie oraz aktualizowanie stron w przypadku aktualizowania danych transakcji. Serwery stronicowania są na bieżąco przechowywane przez odtwarzanie rekordów dziennika z usługi dziennika. Serwery stronicowane utrzymują również pokrycie pamięci podręcznych opartych na dyskach SSD w celu zwiększenia wydajności. Długoterminowe przechowywanie stron danych jest przechowywane w usłudze Azure Storage w celu zwiększenia niezawodności.

### <a name="log-service"></a>Usługa dzienników

Usługa dzienników akceptuje rekordy dzienników z podstawowej repliki obliczeniowej, utrwala je w trwałej pamięci podręcznej i przekazuje rekordy dziennika do pozostałych replik obliczeniowych (aby można było aktualizować ich pamięci podręczne), a także odpowiednie serwera stronicowania, aby dane można było tam zaktualizować. W ten sposób wszystkie zmiany danych z podstawowej repliki obliczeniowej są propagowane przez usługę dziennika do wszystkich pomocniczych replik obliczeniowych i serwerów stronicowania. Na koniec rekordy dziennika są wypychane do magazynu długoterminowego w usłudze Azure Storage, która jest praktycznie nieskończonym repozytorium magazynu. Ten mechanizm usuwa potrzebę częstego przycinania dzienników. Usługa dziennika ma również pamięć lokalną i pamięci podręczne SSD, aby przyspieszyć dostęp do rekordów dziennika.

### <a name="azure-storage"></a>Azure Storage

Usługa Azure Storage zawiera wszystkie pliki danych w bazie danych. Serwery stronicowanie przechowują pliki danych w usłudze Azure Storage na bieżąco. Ten magazyn jest używany do tworzenia kopii zapasowych, a także do replikacji między regionami świadczenia usługi Azure. Kopie zapasowe są implementowane przy użyciu migawek magazynu plików danych. Operacje przywracania przy użyciu migawek są szybkie niezależnie od rozmiaru danych. Dane można przywrócić do dowolnego punktu w czasie w okresie przechowywania kopii zapasowej bazy danych.

## <a name="backup-and-restore"></a>Tworzenie kopii zapasowej i przywracanie

Kopie zapasowe są oparte na migawkach plików, dlatego są niemal natychmiastowe. Separacja magazynu i zasobów obliczeniowych umożliwia wypychanie operacji tworzenia/przywracania kopii zapasowej do warstwy magazynu w celu zmniejszenia obciążenia związanego z przetwarzaniem podstawowej repliki obliczeniowej. W związku z tym kopia zapasowa bazy danych nie ma wpływu na wydajność podstawowego węzła obliczeniowego. Podobnie odzyskiwanie do punktu w czasie odbywa się przez przywrócenie migawek plików i w związku z tym nie jest to rozmiar operacji danych. Przywracanie bazy danych w witrynie Hiperskala w tym samym regionie świadczenia usługi Azure jest operacją w stałym czasie, a nawet bazy danych o wielu terabajtach można przywrócić w ciągu kilku minut, a nie godzin lub dni. Tworzenie nowych baz danych przez przywrócenie istniejącej kopii zapasowej wykorzystuje również tę funkcję: tworzenie kopii bazy danych na potrzeby testowania lub testowania, nawet wieloterabajtowych baz danych, jest dobowe w ciągu kilku minut.

Aby uzyskać informacje na temat przywracania geograficznego baz danych w hiperskali, zobacz Przywracanie bazy danych w [hiperskali do innego regionu.](#restoring-a-hyperscale-database-to-a-different-region)

## <a name="scale-and-performance-advantages"></a>Zalety skalowania i wydajności

Dzięki możliwości szybkiego rozkręcania/zwalniania dodatkowych węzłów obliczeniowych tylko do odczytu architektura Hiperskala umożliwia znaczne możliwości skalowania odczytu i może również zwalniać podstawowy węzeł obliczeniowy do obsługi większej liczby żądań zapisu. Ponadto węzły obliczeniowe można szybko skalować w górę/w dół ze względu na architekturę magazynu udostępnionego w architekturze Hiperskala.

## <a name="create-a-hyperscale-database"></a>Tworzenie bazy danych w hiperskali

Bazę danych w hiperskali można utworzyć przy użyciu języka [Azure Portal,](https://portal.azure.com) [T-SQL,](/sql/t-sql/statements/create-database-transact-sql) [PowerShell](/powershell/module/azurerm.sql/new-azurermsqldatabase)lub [interfejsu wiersza polecenia.](/cli/azure/sql/db#az_sql_db_create) Bazy danych w hiperskali są dostępne tylko przy użyciu modelu zakupów [opartego na rdzeniu rdzeni wirtualnych.](service-tiers-vcore.md)

Następujące polecenie języka T-SQL tworzy bazę danych w hiperskali. W instrukcji należy określić zarówno wersję, jak i cel `CREATE DATABASE` usługi. Zapoznaj się z [limitami zasobów,](./resource-limits-vcore-single-databases.md#hyperscale---provisioned-compute---gen4) aby uzyskać listę prawidłowych celów usługi.

```sql
-- Create a Hyperscale Database
CREATE DATABASE [HyperscaleDB1] (EDITION = 'Hyperscale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```

Spowoduje to utworzenie bazy danych w hiperskali na sprzęcie Gen5 z czterema rdzeniami.

## <a name="upgrade-existing-database-to-hyperscale"></a>Uaktualnianie istniejącej bazy danych do hiperskali

Istniejące bazy danych w programie Azure SQL Database hiperskali można przenieść przy użyciu poleceń [Azure Portal,](https://portal.azure.com) [T-SQL,](/sql/t-sql/statements/alter-database-transact-sql) [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase)lub [CLI.](/cli/azure/sql/db#az_sql_db_update) W tej chwili jest to migracja jednokierunkowa. Nie można przenosić baz danych z warstwy Hiperskala do innej warstwy usługi, niż przez eksportowanie i importowanie danych. W przypadku weryfikacji koncepcji zalecamy wykonanie kopii produkcyjnych baz danych i zmigrowanie jej do hiperskali. Migrowanie istniejącej bazy danych Azure SQL Database do warstwy Hiperskala jest rozmiarem operacji na danych.

Następujące polecenie języka T-SQL przenosi bazę danych do warstwy usługi Hiperskala. W instrukcji należy określić zarówno wersję, jak i cel `ALTER DATABASE` usługi.

```sql
-- Alter a database to make it a Hyperscale Database
ALTER DATABASE [DB2] MODIFY (EDITION = 'Hyperscale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```

## <a name="connect-to-a-read-scale-replica-of-a-hyperscale-database"></a>Nawiązywanie połączenia z repliką w skali odczytu bazy danych w hiperskali

W bazach danych w hiperskali argument w ciągu połączenia dostarczonym przez klienta określa, czy połączenie jest kierowane do repliki zapisu, czy do repliki pomocniczej `ApplicationIntent` tylko do odczytu. Jeśli wartość ustawiona na , a baza danych nie ma repliki pomocniczej, połączenie zostanie kierowane do repliki podstawowej i domyślnie `ApplicationIntent` `READONLY` będzie `ReadWrite` zachowanie.

```cmd
-- Connection string with application intent
Server=tcp:<myserver>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

Repliki pomocnicze w hiperskali są identyczne, używając tego samego celu poziomu usługi co replika podstawowa. Jeśli istnieje więcej niż jedna replika pomocnicza, obciążenie jest dystrybuowane między wszystkie dostępne pomocnicze bazy danych. Każda replika pomocnicza jest aktualizowana niezależnie. W związku z tym różne repliki mogą mieć różne opóźnienia danych względem repliki podstawowej.

## <a name="database-high-availability-in-hyperscale"></a>Wysoka dostępność bazy danych w hiperskali

Podobnie jak w przypadku wszystkich innych warstw usług, funkcja Hiperskala gwarantuje trwałość danych dla zatwierdzonej transakcji niezależnie od dostępności repliki obliczeniowej. Zakres przestoju spowodowany niedostępną repliką podstawową zależy od typu trybu failover (planowanego i nieplanowanego) oraz obecności co najmniej jednej repliki pomocniczej. W przypadku planowanego trybu failover (tj. zdarzenia konserwacji) system tworzy nową replikę podstawową przed zainicjowaniem trybu failover lub używa istniejącej repliki pomocniczej jako obiektu docelowego trybu failover. W przypadku nieplanowanego trybu failover (tj. awarii sprzętowej repliki podstawowej) system używa repliki pomocniczej jako miejsca docelowego trybu failover, jeśli istnieje, lub tworzy nową replikę podstawową z puli dostępnej pojemności obliczeniowej. W drugim przypadku czas przestoju jest dłuższy z powodu dodatkowych kroków wymaganych do utworzenia nowej repliki podstawowej.

Aby uzyskać informacje na temat umowy SLA w hiperskali, zobacz [temat Umowa SLA Azure SQL Database](https://azure.microsoft.com/support/legal/sla/sql-database/).

## <a name="disaster-recovery-for-hyperscale-databases"></a>Odzyskiwanie po awarii dla baz danych w hiperskali

### <a name="restoring-a-hyperscale-database-to-a-different-region"></a>Przywracanie bazy danych w hiperskali do innego regionu

Jeśli musisz przywrócić bazę danych w hiperskali w programie Azure SQL Database do regionu innego niż ten, w którego obecnie jest hostowana, w ramach operacji odzyskiwania po awarii, przechodzenia do szczegółów, relokacji lub z innej przyczyny, podstawową metodą jest przywracanie geograficzne bazy danych. Obejmuje to dokładnie te same kroki co w przypadku przywracania dowolnej innej bazy danych w SQL Database do innego regionu:

1. Utwórz serwer [w](logical-servers.md) regionie docelowym, jeśli nie masz jeszcze tam odpowiedniego serwera.  Ten serwer powinien być własnością tej samej subskrypcji co oryginalny (źródłowy) serwer.
2. Postępuj zgodnie z instrukcjami w [temacie dotyczącym przywracania geograficznego](./recovery-using-backups.md#geo-restore) na stronie dotyczącej przywracania bazy danych Azure SQL Database z automatycznych kopii zapasowych.

> [!NOTE]
> Ponieważ źródło i obiekt docelowy znajdują się w oddzielnych regionach, baza danych nie może współdzielić magazynu migawek ze źródłową bazą danych, tak jak w przypadku przywracania poza obszarem geograficznym, które są szybko ukończone niezależnie od rozmiaru bazy danych. W przypadku przywracania geograficznego bazy danych w hiperskali będzie to operacja rozmiaru danych, nawet jeśli obiekt docelowy znajduje się w sparowanym regionie magazynu z replikacją geograficzną. Dlatego przywracanie geograficzne będzie proporcjonalnie do rozmiaru przywracanej bazy danych. Jeśli obiekt docelowy znajduje się w sparowanych regionach, transfer danych będzie w regionie, który będzie znacznie szybszy niż transfer danych między regionami, ale nadal będzie operacją rozmiaru danych.

## <a name="available-regions"></a><a name=regions></a>Dostępne regiony

Warstwa Azure SQL Database Hiperskala jest dostępna we wszystkich regionach, ale domyślnie w poniższych regionach wymienionych poniżej. Jeśli chcesz utworzyć bazę danych w hiperskali w regionie, w którym funkcja Hiperskala nie jest domyślnie włączona, możesz wysłać żądanie do Azure Portal. Aby uzyskać instrukcje, [zobacz Request quota increases for Azure SQL Database](quota-increase-request.md) (Żądanie zwiększenia limitu przydziału dla instrukcje). Podczas przesyłania żądania należy stosować się do następujących wytycznych:

- Użyj typu [limitu przydziału](quota-increase-request.md#region) dostęp SQL Database Region.
- W opisie dodaj jednostkę SKU/łączną liczbę rdzeni obliczeniowych, w tym repliki z możliwością odczytu, i wskaż, że żądasz pojemności Hiperskala.
- Określ również projekcję całkowitego rozmiaru wszystkich baz danych w czasie w TB.

Włączone regiony:
- Australia Wschodnia
- Australia Południowo-Wschodnia
- Australia Środkowa
- Brazylia Południowa
- Kanada Środkowa
- Kanada Wschodnia
- Central US
- Chiny Wschodnie 2
- Chiny Północne 2
- Azja Wschodnia
- East US
- Wschodnie us 2
- Francja Środkowa
- Niemcy Zachodnio-środkowe
- Japonia Wschodnia
- Japonia Zachodnia
- Korea Środkowa
- Korea Południowa
- Północno-środkowe stany USA
- Europa Północna
- Holandia Wschodnia
- Holandia Zachodnia
- Północna Republika Południowej Afryki
- South Central US
- Southeast Asia
- Szwajcaria Zachodnia
- Południowe Zjednoczone Królestwo
- Zachodnie Zjednoczone Królestwo
- US DoD (region środkowy)
- US DoD (region wschodni)
- Us Govt Arizona
- US Govt Teksas
- Zachodnio-środkowe stany USA
- West Europe
- Zachodnie stany USA
- Zachodnie stany USA 2

## <a name="known-limitations"></a>Znane ograniczenia

Są to aktualne ograniczenia dotyczące warstwy usługi Hiperskala od wersji GA.  Aktywnie pracujemy nad usunięciem jak najwięcej z tych ograniczeń.

| Problem | Description |
| :---- | :--------- |
| W okienku Zarządzanie kopiami zapasami dla serwera nie są wyświetlane bazy danych w hiperskali. Zostaną one odfiltrowane z widoku.  | Hiperskala ma oddzielną metodę zarządzania kopiami zapasami, więc ustawienia przechowywania Long-Term i przechowywania kopii zapasowych w punkcie w czasie nie mają zastosowania. W związku z tym bazy danych w hiperskali nie są wyświetlane w okienku Zarządzanie kopiami zapasami.<br><br>W przypadku baz danych migrowanych do warstwy Hiperskala z innych Azure SQL Database usług [](automated-backups-overview.md#backup-retention) kopie zapasowe przed migracją są przechowywane przez czas przechowywania kopii zapasowych źródłowej bazy danych. Te kopie zapasowe mogą służyć do [przywracania źródłowej](recovery-using-backups.md#programmatic-recovery-using-automated-backups) bazy danych do punktu w czasie przed migracją.|
| Przywracanie do określonego momentu | Bazy danych spoza hiperskali nie można przywrócić jako bazy danych w hiperskali, a bazy danych w hiperskali nie można przywrócić jako bazy danych spoza hiperskali. W przypadku bazy danych spoza hiperskali, która została zmigrowana do warstwy Hiperskala przez zmianę jej warstwy usługi, przywracanie do punktu w czasie przed migracją i w okresie przechowywania kopii zapasowej bazy danych jest obsługiwane [programowo.](recovery-using-backups.md#programmatic-recovery-using-automated-backups) Przywrócona baza danych będzie spoza hiperskali. |
| W przypadku Azure SQL Database usługi na hiperskalowanie operacja kończy się niepowodzeniem, jeśli baza danych zawiera jakiekolwiek pliki danych większe niż 1 TB | W niektórych przypadkach może być możliwe omianie tego problemu przez zmniejszenie ilości dużych plików do mniej niż 1 TB przed podjęciem próby zmiany warstwy usługi na Hiperskala. [](file-space-manage.md#shrinking-data-files) Użyj następującego zapytania, aby określić bieżący rozmiar plików bazy danych. `SELECT file_id, name AS file_name, size * 8. / 1024 / 1024 AS file_size_GB FROM sys.database_files WHERE type_desc = 'ROWS'`;|
| Wystąpienie zarządzane SQL | Azure SQL Managed Instance nie jest obecnie obsługiwana w przypadku baz danych w hiperskali. |
| Pule elastyczne |  Elastyczne pule nie są obecnie obsługiwane w przypadku hiperskali.|
| Migracja do hiperskali jest obecnie operacją jednokierunkową | Po migracji bazy danych do warstwy Hiperskala nie można jej migrować bezpośrednio do warstwy usługi spoza hiperskali. Obecnie jedynym sposobem migracji bazy danych z hiperskali do innej niż Hiperskala jest eksportowanie/importowanie przy użyciu pliku bacpac lub innych technologii przenoszenia danych (kopiowanie zbiorcze, Azure Data Factory, Azure Databricks, SSIS itp.) Eksportowanie/importowanie pliku Bacpac z programu Azure Portal, z programu PowerShell przy użyciu [polecenia New-AzSqlDatabaseExport](/powershell/module/az.sql/new-azsqldatabaseexport) lub [New-AzSqlDatabaseImport](/powershell/module/az.sql/new-azsqldatabaseimport)z interfejsu wiersza polecenia platformy Azure przy użyciu poleceń [az sql db export](/cli/azure/sql/db#az_sql_db_export) i az sql db [import](/cli/azure/sql/db#az_sql_db_import)oraz z interfejsu [API REST](/rest/api/sql/) nie jest obsługiwane. Importowanie/eksportowanie pliku Bacpac dla mniejszych baz danych w hiperskali (do 200 GB) jest obsługiwane przy użyciu programu SSMS i [pakietu SqlPackage](/sql/tools/sqlpackage) w wersji 18.4 lub nowszej. W przypadku większych baz danych eksportowanie/importowanie pliku bacpac może zająć dużo czasu i może się nie powieść z różnych powodów.|
| Migracja baz danych z In-Memory OLTP | Hiperskala obsługuje podzbiór In-Memory OLTP, w tym typy tabel zoptymalizowane pod kątem pamięci, zmienne tabel i natywnie skompilowane moduły. Jeśli jednak w migrowanych bazach danych In-Memory obiektów OLTP dowolnego rodzaju, migracja z warstw usług Premium i Krytyczne dla działania firmy do hiperskali nie jest obsługiwana. Aby przeprowadzić migrację takiej bazy danych do hiperskalowania, wszystkie In-Memory OLTP i ich zależności muszą zostać usunięte. Po migracji bazy danych można ponownie utworzyć te obiekty. Tabele trwałe i nietrwałych zoptymalizowane pod kątem pamięci nie są obecnie obsługiwane w hiperskali i należy je zmienić na tabele dysków.|
| Replikacja geograficzna  | Nie można jeszcze skonfigurować replikacji geograficznej dla Azure SQL Database Hiperskala. |
| Kopiowanie bazy danych | Kopiowanie bazy danych w hiperskali jest teraz dostępne w publicznej wersji zapoznawczej. |
| Funkcje inteligentnej bazy danych | Z wyjątkiem opcji "Wymuszaj plan", wszystkie inne opcje automatycznego dostrajania nie są jeszcze obsługiwane w hiperskali: opcje mogą wydawać się włączone, ale nie zostaną wykonane żadne zalecenia ani akcje. |
| Analiza wydajności zapytań | Szczegółowe informacje o wydajności zapytań nie są obecnie obsługiwane w przypadku baz danych w hiperskali. |
| Zmniejszanie bazy danych | Baza danych DBCC SHRINKDATABASE DBCC SHRINKFILE nie jest obecnie obsługiwana w przypadku baz danych w hiperskali. |
| Sprawdzanie integralności bazy danych | Baza danych DBCC CHECKDB nie jest obecnie obsługiwana w przypadku baz danych w hiperskali. Aby obejść ten problem, można użyć tabel DBCC CHECKFILEGROUP i DBCC CHECKTABLE. Zobacz [Integralność danych w Azure SQL Database,](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/) aby uzyskać szczegółowe informacje na temat zarządzania integralnością danych w Azure SQL Database. |

## <a name="next-steps"></a>Następne kroki

- Często zadawane pytania dotyczące hiperskalowania można znaleźć w [artykule Często zadawane pytania dotyczące hiperskali.](service-tier-hyperscale-frequently-asked-questions-faq.md)
- Aby uzyskać informacje o warstwach usług, zobacz [Warstwy usług](purchasing-models.md)
- Aby [uzyskać informacje o limitach](resource-limits-logical-server.md) na poziomie serwera i subskrypcji, zobacz Overview of resource limits on a server (Omówienie limitów zasobów na serwerze).
- Aby uzyskać informacje na temat limitów modelu zakupów dla pojedynczej bazy danych, zobacz Azure SQL Database limity modelu zakupów oparte na rdzeniu [wirtualnych dla pojedynczej bazy danych.](resource-limits-vcore-single-databases.md)
- Aby uzyskać listę funkcji i porównań, zobacz [Sql common features (Typowe funkcje sql).](features-comparison.md)
