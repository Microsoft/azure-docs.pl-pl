---
title: Omówienie hiperskali bazy danych SQL platformy Azure | Dokumenty firmy Microsoft
description: W tym artykule opisano warstwę usług hiperskali w modelu zakupów opartym na wynikach wirtualnych w usłudze Azure SQL Database i wyjaśniono, jak różni się od warstw usług ogólnego przeznaczenia i krytycznych dla firm.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 10/01/2019
ms.openlocfilehash: 074a28af8c80c109dbe97306900e8f00618e435a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81411698"
---
# <a name="hyperscale-service-tier"></a>Warstwa usługi Hiperskala

Usługa Azure SQL Database jest oparta na architekturze aparatu bazy danych programu SQL Server, która jest dostosowana do środowiska w chmurze, aby zapewnić dostępność 99,99% nawet w przypadku awarii infrastruktury. Istnieją trzy modele architektury, które są używane w usłudze Azure SQL Database:
- Ogólnego przeznaczenia/standardu 
-  Hiperskala
-  Krytyczna dla biznesu/Premium

Warstwa usługi hiperskali w usłudze Azure SQL Database to najnowsza warstwa usług w modelu zakupów opartym na wynikach wirtualnych. Ta warstwa usług jest wysoce skalowalną warstwą wydajności magazynu i wydajności obliczeniowej, która wykorzystuje architekturę platformy Azure do skalowania w poziomie zasobów magazynu i zasobów obliczeniowych dla bazy danych SQL azure znacznie przekraczających limity dostępne dla warstw usług ogólnego przeznaczenia i krytycznych dla firmy.

> 
> [!NOTE]
> Aby uzyskać szczegółowe informacje na temat warstw usług ogólnego przeznaczenia i krytycznych dla firmy w modelu zakupów opartym na wynikach wirtualnych, zobacz warstwy usług [ogólnego przeznaczenia](sql-database-service-tier-general-purpose.md) i krytyczne [dla firmy.](sql-database-service-tier-business-critical.md) Aby zapoznać się z porównaniem modelu zakupów opartego na procesorach vCore z modelem zakupów opartym na USŁUDZE DTU, zobacz [Tworzenie modeli i zasobów w bazie danych SQL usługi Azure .](sql-database-service-tiers.md)


## <a name="what-are-the-hyperscale-capabilities"></a>Jakie są możliwości hiperskali

Warstwa usługi hiperskali w usłudze Azure SQL Database zapewnia następujące dodatkowe możliwości:

- Obsługa do 100 TB rozmiaru bazy danych
- Niemal natychmiastowe kopie zapasowe bazy danych (oparte na migawkach plików przechowywanych w magazynie obiektów Blob platformy Azure) niezależnie od rozmiaru bez wpływu we/wy na zasoby obliczeniowe  
- Szybkie przywracanie bazy danych (na podstawie migawek plików) w minutach, a nie godzinach lub dniach (nie jest to rozmiar operacji danych)
- Wyższa ogólna wydajność dzięki wyższej przepływności dziennika i krótszym czasom zatwierdzania transakcji niezależnie od ilości danych
- Szybkie skalowanie w poziomie — można aprowizować jeden lub więcej węzłów tylko do odczytu w celu odciążenia obciążenia odczytu i użycia jako hot-standbys
- Szybkie skalowanie w górę — w stałym czasie można skalować zasoby obliczeniowe w górę, aby w razie potrzeby dostosować do dużych obciążeń, a następnie skalować zasoby obliczeniowe z powrotem w dół, gdy nie są potrzebne.

Warstwa usług hiperskali usuwa wiele praktycznych limitów tradycyjnie widocznych w bazach danych w chmurze. W przypadku, gdy większość innych baz danych są ograniczone przez zasoby dostępne w jednym węźle, bazy danych w warstwie usługi hiperskali nie mają takich limitów. Dzięki elastycznej architekturze pamięci masowej pamięć masowa rośnie w razie potrzeby. W rzeczywistości bazy danych hiperskali nie są tworzone przy zdefiniowanym maksymalnym rozmiarze. Baza danych na dużą skalę rośnie w razie potrzeby — i są rozliczane tylko dla pojemności, której używasz. W przypadku obciążeń intensywnie korzystających z odczytu warstwa usługi hiperskali zapewnia szybkie skalowanie w poziomie przez inicjowanie obsługi administracyjnej dodatkowych replik odczytu w razie potrzeby do odciążania obciążeń odczytu.

Ponadto czas wymagany do utworzenia kopii zapasowych bazy danych lub skalowania w górę lub w dół nie jest już powiązany z ilością danych w bazie danych. Tworzenie kopii zapasowych baz danych w hiperskali można utworzyć praktycznie natychmiast. Można również skalować bazę danych w dziesiątkach terabajtów w górę lub w dół w ciągu kilku minut. Ta funkcja uwalnia cię od obaw o to, że zostaniesz zapakowany przez początkowe wybory konfiguracji.

Aby uzyskać więcej informacji na temat rozmiarów obliczeń dla warstwy usług hiperskalał, zobacz [Charakterystyka warstwy usługi](sql-database-service-tiers-vcore.md#service-tiers).

## <a name="who-should-consider-the-hyperscale-service-tier"></a>Kto powinien wziąć pod uwagę warstwę usług hiperskali

Warstwa usług hiperskali jest przeznaczona dla większości obciążeń biznesowych, ponieważ zapewnia dużą elastyczność i wysoką wydajność dzięki niezależnie skalowalnym zasobom obliczeniowym i magazynowym. Dzięki możliwości automatycznego skalowania pamięci masowej do 100 TB jest to świetny wybór dla klientów, którzy:

- Mieć duże bazy danych lokalnie i chcesz zmodernizować swoje aplikacje, przechodząc do chmury
- Są już w chmurze i są ograniczone przez maksymalne ograniczenia rozmiaru bazy danych innych warstw usług (1-4 TB)
- Mają mniejsze bazy danych, ale wymagają szybkiego pionowego i poziomego skalowania obliczeń, wysokiej wydajności, natychmiastowej kopii zapasowej i szybkiego przywracania bazy danych.

Warstwa usług hiperskali obsługuje szeroki zakres obciążeń programu SQL Server, od czystego OLTP do czystej analizy, ale jest zoptymalizowany przede wszystkim dla obciążeń OLTP i transakcji hybrydowych i przetwarzania analitycznego (HTAP).

> [!IMPORTANT]
> Pule elastyczne nie obsługują warstwy usługi hiperskali.

## <a name="hyperscale-pricing-model"></a>Model cenowy na dużą skalę

Warstwa usług hyperscale jest dostępna tylko w [modelu vCore](sql-database-service-tiers-vcore.md). Aby wyrównać z nową architekturą, model cenowy różni się nieco od warstw usług ogólnego przeznaczenia lub usługi Krytyczne dla firmy:

- **Obliczenie:**

  Obliczona cena jednostkowa w skali hiperskali jest za replikę. Cena [korzyści hybrydowej platformy Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) jest stosowana do odczytu replik skalowania automatycznie. Domyślnie tworzymy replikę podstawową i jedną replikę tylko do odczytu na bazę danych w hiperskali.  Użytkownicy mogą dostosować całkowitą liczbę replik, w tym podstawowych od 1 do 5.

- **Przechowywanie**:

  Podczas konfigurowania bazy danych w skali hiperskali nie trzeba określać maksymalnego rozmiaru danych. W warstwie hiperskali są naliczane opłaty za magazyn bazy danych na podstawie rzeczywistej alokacji. Magazyn jest automatycznie przydzielany od 40 GB do 100 TB, w odstępach co 10 GB. Wiele plików danych może rosnąć w tym samym czasie, jeśli to konieczne. Baza danych w skali hiperskali jest tworzona z rozmiarem początkowym 10 GB i zaczyna rosnąć o 10 GB co 10 minut, aż osiągnie rozmiar 40 GB.

Aby uzyskać więcej informacji na temat cen hiperskali, zobacz [Cennik bazy danych SQL azure](https://azure.microsoft.com/pricing/details/sql-database/single/)

## <a name="distributed-functions-architecture"></a>Architektura funkcji rozproszonych

W przeciwieństwie do tradycyjnych aparatów baz danych, które scentralizowały wszystkie funkcje zarządzania danymi w jednej lokalizacji /procesie (nawet tak zwane rozproszone bazy danych w produkcji mają obecnie wiele kopii monolitycznego aparatu danych), baza danych hyperscale oddziela aparat przetwarzania zapytań, gdzie semantyka różnych silników danych odbiega od komponentów, które zapewniają długoterminowe przechowywanie i trwałość danych. W ten sposób pojemność magazynu można płynnie skalować w poziomie w razie potrzeby (początkowy cel to 100 TB). Repliki tylko do odczytu współużytkują te same składniki magazynu, więc do rozkręcenia nowej repliki czytelnej nie jest wymagana żadna kopia danych. 

Na poniższym diagramie przedstawiono różne typy węzłów w bazie danych hiperskali:

![architektura](./media/sql-database-hyperscale/hyperscale-architecture.png)

Baza danych na dużą skalę zawiera następujące różne typy składników:

### <a name="compute"></a>Wystąpienia obliczeniowe

Węzeł obliczeniowy jest, gdzie działa aparat relacji, więc wszystkie elementy języka, przetwarzania zapytań i tak dalej, występują. Wszystkie interakcje użytkownika z bazy danych hiperskali odbywa się za pośrednictwem tych węzłów obliczeniowych. Węzły obliczeniowe mają bufory oparte na ssd (oznaczone jako RBPEX — rozszerzenie puli buforów odpornych na poprzednim diagramie), aby zminimalizować liczbę rund sieciowych wymaganych do pobrania strony danych. Istnieje jeden podstawowy węzeł obliczeniowy, w którym przetwarzane są wszystkie obciążenia odczytu i zapisu. Istnieje jeden lub więcej pomocniczych węzłów obliczeniowych, które działają jako gorące węzły wstrzymania do celów pracy awaryjnej, a także działają jako węzły obliczeniowe tylko do odczytu do odciążania obciążeń odczytu (jeśli ta funkcja jest pożądana).

### <a name="page-server"></a>Serwer strony

Serwery stron to systemy reprezentujące aparat magazynu skalowany w poziomie.  Każdy serwer strony jest odpowiedzialny za podzbiór stron w bazie danych.  Nominalnie każdy serwer strony kontroluje od 128 GB do 1 TB danych. Żadne dane nie są udostępniane na więcej niż jednym serwerze strony (poza replikami, które są przechowywane w celu nadmiarowości i dostępności). Zadaniem serwera strony jest obsługa stron bazy danych do węzłów obliczeniowych na żądanie i aktualizowanie stron w miarę aktualizowania danych przez transakcje. Serwery stron są aktualizowane, odtwarzając rekordy dziennika z usługi dziennika. Serwery page'ów obsługują również pamięci podręczne oparte na ssd, aby zwiększyć wydajność. Długoterminowe przechowywanie stron danych jest przechowywane w usłudze Azure Storage w celu zwiększenia niezawodności.

### <a name="log-service"></a>Usługa dzienników

Usługa dziennika akceptuje rekordy dziennika z podstawowej repliki obliczeniowej, utrzymuje je w trwałej pamięci podręcznej i przekazuje rekordy dziennika do pozostałych replik obliczeniowych (aby mogły aktualizować swoje pamięci podręczne), a także odpowiednie serwery stron, dzięki czemu można tam aktualizować dane. W ten sposób wszystkie zmiany danych z podstawowej repliki obliczeniowej są propagowane za pośrednictwem usługi dziennika do wszystkich pomocniczych replik obliczeniowych i serwerów stron. Na koniec rekordy dziennika są wypychane do magazynu długoterminowego w usłudze Azure Storage, która jest praktycznie nieskończone repozytorium magazynu. Mechanizm ten eliminuje potrzebę częstego obcinania dziennika. Usługa dziennika ma również lokalną pamięć podręczną, aby przyspieszyć dostęp do rekordów dziennika.

### <a name="azure-storage"></a>Azure Storage

Usługa Azure Storage zawiera wszystkie pliki danych w bazie danych. Serwery stron aktualizują pliki danych w usłudze Azure Storage. Ten magazyn jest używany do celów tworzenia kopii zapasowych, a także do replikacji między regionami platformy Azure. Kopie zapasowe są implementowane przy użyciu migawek magazynu plików danych. Operacje przywracania przy użyciu migawek są szybkie niezależnie od rozmiaru danych. Dane można przywrócić do dowolnego punktu w czasie w okresie przechowywania kopii zapasowej bazy danych.

## <a name="backup-and-restore"></a>Tworzenie kopii zapasowej i przywracanie

Kopie zapasowe są oparte na migawkach plików i dlatego są niemal natychmiastowe. Separacja magazynu i obliczeń umożliwia naciśnięcie w dół operacji tworzenia kopii zapasowej/przywracania warstwy magazynu w celu zmniejszenia obciążenia przetwarzania podstawowej repliki obliczeniowej. W rezultacie kopia zapasowa bazy danych nie wpływa na wydajność podstawowego węzła obliczeniowego; podobnie przywraca odbywa się przez powrót do migawek plików i jako takie nie są rozmiar operacji danych. Przywracanie jest operacją o stałej w czasie, a nawet bazy danych z wieloma terabajtami można przywrócić w minutach, a nie w godzinach lub dniach. Tworzenie nowych baz danych przez przywracanie istniejącej kopii zapasowej również korzysta z tej funkcji: tworzenie kopii bazy danych do celów rozwoju lub testowania, nawet terabajtów baz danych, jest wykonalne w ciągu kilku minut.

## <a name="scale-and-performance-advantages"></a>Skala i wydajność korzyści

Dzięki możliwości szybkiego obracania dodatkowych węzłów obliczeniowych tylko do odczytu, architektura hiperskali umożliwia znaczne możliwości skalowania odczytu, a także może zwolnić podstawowy węzeł obliczeniowy do obsługi większej liczby żądań zapisu. Ponadto węzły obliczeniowe mogą być skalowane w górę/w dół szybko ze względu na architekturę magazynu udostępnionego architektury hiperskali.

## <a name="create-a-hyperscale-database"></a>Tworzenie bazy danych z hiperskali

Bazę danych hiperskali można utworzyć za pomocą [portalu Azure,](https://portal.azure.com) [T-SQL,](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current) [programu PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/new-azurermsqldatabase) lub [interfejsu wiersza polecenia.](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create) Bazy danych w hiperskali są dostępne tylko przy użyciu [modelu zakupów opartego na językach wirtualnych.](sql-database-service-tiers-vcore.md)

Następujące polecenie T-SQL tworzy bazę danych w hiperskali. Należy określić zarówno edition i `CREATE DATABASE` cel usługi w instrukcji. Zapoznaj się z [limitami zasobów,](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-single-databases#hyperscale---provisioned-compute---gen4) aby wyświetlić listę prawidłowych celów usługi.

```sql
-- Create a Hyperscale Database
CREATE DATABASE [HyperscaleDB1] (EDITION = 'Hyperscale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```
Spowoduje to utworzenie bazy danych hyperscale na sprzęcie Gen5 z 4 rdzeniami.

## <a name="migrate-an-existing-azure-sql-database-to-the-hyperscale-service-tier"></a>Migrowanie istniejącej bazy danych SQL azure do warstwy usług hiperskali

Istniejące bazy danych SQL platformy Azure można przenieść do hiperskali za pomocą [portalu Azure](https://portal.azure.com), [T-SQL,](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) [programu PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqldatabase) lub interfejsu [wiersza polecenia.](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update) W tej chwili jest to migracja jednokierunkowa. Nie można przenieść baz danych z hiperskali do innej warstwy usług, innych niż eksportowanie i importowanie danych. W przypadku weryfikacji koncepcji (POC) zaleca się wykonanie kopii produkcyjnych baz danych i migrację kopii do hiperskali. Migrowanie istniejącej bazy danych SQL platformy Azure do warstwy hiperskali jest rozmiar operacji danych.

Następujące polecenie T-SQL przenosi bazę danych do warstwy usług hiperskali. Należy określić zarówno edition i `ALTER DATABASE` cel usługi w instrukcji.

```sql
-- Alter a database to make it a Hyperscale Database
ALTER DATABASE [DB2] MODIFY (EDITION = 'Hyperscale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```

## <a name="connect-to-a-read-scale-replica-of-a-hyperscale-database"></a>Łączenie się z repliką w skali odczytu bazy danych w hiperskali

W bazach danych `ApplicationIntent` hiperskali argument w ciągu połączenia dostarczonym przez klienta określa, czy połączenie jest kierowane do repliki zapisu lub do repliki pomocniczej tylko do odczytu. Jeśli `ApplicationIntent` ustawiona `READONLY` i baza danych nie ma repliki pomocniczej, połączenie zostanie przekierowane do repliki podstawowej i domyślnie `ReadWrite` będzie to zachowanie.

```cmd
-- Connection string with application intent
Server=tcp:<myserver>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

Repliki pomocnicze hiperskali są identyczne, przy użyciu tego samego celu poziomu usług co replika podstawowa. Jeśli istnieje więcej niż jedna replika pomocnicza, obciążenie jest dystrybuowane we wszystkich dostępnych pomocniczych. Każda replika pomocnicza jest aktualizowana niezależnie, w związku z tym różne repliki mogą mieć różne opóźnienia danych względem repliki podstawowej.

## <a name="database-high-availability-in-hyperscale"></a>Wysoka dostępność bazy danych w hiperskali

Podobnie jak we wszystkich innych warstwach usług, hiperskala gwarantuje trwałość danych dla transakcji zatwierdzonych, niezależnie od dostępności repliki obliczeniowej. Zakres przestojów spowodowany niedostępniem repliki podstawowej zależy od typu pracy awaryjnej (planowanej a nieplanowanej) oraz od obecności co najmniej jednej repliki pomocniczej. W planowanej pracy awaryjnej (tj. zdarzenie konserwacji) system tworzy nową replikę podstawową przed rozpoczęciem pracy awaryjnej lub używa istniejącej repliki pomocniczej jako obiektu docelowego trybu failover. W nieplanowanym uchybieniu w pracy awaryjnej (tj. awarii sprzętu w replice podstawowej) system używa repliki pomocniczej jako obiektu docelowego trybu failover, jeśli istnieje, lub tworzy nową replikę podstawową z puli dostępnej pojemności obliczeniowej. W tym ostatnim przypadku czas przestoju jest dłuższy ze względu na dodatkowe kroki wymagane do utworzenia nowej repliki podstawowej.

W przypadku umowy SLA w hiperskali zobacz [SLA dla usługi Azure SQL Database](https://azure.microsoft.com/support/legal/sla/sql-database/).

## <a name="disaster-recovery-for-hyperscale-databases"></a>Odzyskiwanie po awarii dla baz danych hiperskali

### <a name="restoring-a-hyperscale-database-to-a-different-geography"></a>Przywracanie bazy danych w hiperskali do innej lokalizacji geograficznej
Jeśli chcesz przywrócić usługę Azure SQL Database Hyperscale DB do regionu innego niż ten, w jakim jest obecnie hostowany, w ramach operacji odzyskiwania po awarii lub drążenia, przenoszenia lub innego powodu, podstawową metodą jest wykonać przywracanie geograficzne bazy danych.  Obejmuje to dokładnie te same kroki, co służy do przywracania innych usług Azure SQL DB do innego regionu:
1. Utwórz serwer bazy danych SQL azure w regionie docelowym, jeśli nie masz jeszcze odpowiedniego serwera.  Ten serwer powinien być własnością tej samej subskrypcji co oryginalny serwer (źródłowy).
2. Postępuj zgodnie z instrukcjami w temacie [przywracania geograficznego](https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups#geo-restore) strony na przywracanie bazy danych SQL azure z automatycznych kopii zapasowych.

> [!NOTE]
> Ponieważ źródło i miejsce docelowe znajdują się w oddzielnych regionach, baza danych nie może współużytkować magazynu migawek w źródłowej bazie danych, tak jak w przywracaniu innym niż geo, które kończą się bardzo szybko. W przypadku przywracania geograficznego bazy danych w hiperskali będzie to operacja o rozmiarze danych, nawet jeśli obiekt docelowy znajduje się w sparowanym regionie magazynu replikowanego geograficznie.  Oznacza to, że wykonanie przywracania geograficznego zajmie czas proporcjonalnie do rozmiaru przywracanej bazy danych.  Jeśli obiekt docelowy znajduje się w regionie sparowanym, kopia będzie znajdować się w obrębie regionu, który będzie znacznie szybszy niż kopia między regionami, ale nadal będzie operacją rozmiaru danych.

## <a name="available-regions"></a><a name=regions></a>Dostępne regiony

Warstwa hyperscale bazy danych SQL azure jest obecnie dostępna w następujących regionach:

- Australia Wschodnia
- Australia Południowo-Wschodnia
- Brazylia Południowa
- Kanada Środkowa
- Środkowe stany USA
- Chiny Wschodnie 2
- Chiny Północne 2
- Azja Wschodnia
- Wschodnie stany USA
- Wschodnie Stany Zjednoczone 2
- Francja Środkowa
- Japonia Wschodnia
- Japonia Zachodnia
- Korea Środkowa
- Korea Południowa
- Północno-środkowe stany USA
- Europa Północna
- Republika Południowej Afryki Północ
- Południowo-środkowe stany USA
- Azja Południowo-Wschodnia
- Południowe Zjednoczone Królestwo
- Zachodnie Zjednoczone Królestwo
- Europa Zachodnia
- Zachodnie stany USA
- Zachodnie stany USA 2

Jeśli chcesz utworzyć bazę danych na dużą skalę w regionie, który nie jest wymieniony jako obsługiwany, możesz wysłać żądanie dołączania za pośrednictwem witryny Azure portal. Aby uzyskać instrukcje, zobacz [Żądania zwiększenia przydziału dla usługi Azure SQL Database](quota-increase-request.md) instrukcji. Przesyłając wniosek, skorzystaj z następujących wskazówek:

- Użyj innego typu przydziału bazy danych [żądania przydziału](quota-increase-request.md#other) SQL.
- W szczegółach tekstu dodaj obliczoną jednostkę SKU/całkowitą rdzenie, w tym repliki czytelne.
- Należy również określić szacowaną wartość TB.

## <a name="known-limitations"></a>Znane ograniczenia

Są to bieżące ograniczenia warstwy usług hiperskali w ga.  Aktywnie pracujemy nad usunięciem jak największej liczby tych ograniczeń.

| Problem | Opis |
| :---- | :--------- |
| Okienko Zarządzanie kopiami zapasowymi dla serwera logicznego nie pokazuje baz danych hiperskali, zostaną one odfiltrowane z widoku  | Hiperskala ma oddzielną metodę zarządzania kopiami zapasowymi i jako takie ustawienia przechowywania długoterminowego przechowywania i przechowywania kopii zapasowej w czasie nie mają zastosowania / są unieważnione. W związku z tym bazy danych hiperskali nie są wyświetlane w okienku Zarządzanie zapasem. |
| Przywracanie do określonego momentu | Bazy danych na dużą skalę można przywrócić do bazy danych nie hiperskalowej w okresie przechowywania bazy danych innych niż hiperskali. Nie można przywrócić bazy danych nie hiperskalowej do bazy danych na dużą skalę.|
| Jeśli baza danych ma jeden lub więcej plików danych większych niż 1 TB, migracja nie powiedzie się | W niektórych przypadkach może być możliwe obejść ten problem, zmniejszając dużych plików, aby być mniej niż 1 TB. W przypadku migracji bazy danych używanej podczas procesu migracji upewnij się, że żaden plik nie jest większy niż 1 TB. Użyj następującej kwerendy, aby określić rozmiar plików bazy danych. `SELECT *, name AS file_name, size * 8. / 1024 / 1024 AS file_size_GB FROM sys.database_files WHERE type_desc = 'ROWS'`;|
| Wystąpienie zarządzane | Wystąpienie zarządzanej bazy danych SQL usługi Azure nie jest obecnie obsługiwane w bazach danych w hiperskali. |
| Pule elastyczne |  Pule elastyczne nie są obecnie obsługiwane z hiperskali bazy danych SQL.|
| Migracja do hiperskali jest obecnie operacją jednokierunkową | Po migracji bazy danych do hiperskali nie można jej przeprowadzić do warstwy usług innych niż hiperskali. Obecnie jedynym sposobem migracji bazy danych z hiperskali do nieprzebudowy jest eksportowanie/importowanie przy użyciu pliku BACPAC lub innych technologii przenoszenia danych (kopiowanie zbiorcze, fabryka danych azure, azure databricks, SSIS itp.)|
| Migracja baz danych z trwałymi obiektami w pamięci | Hiperskala obsługuje tylko nietrwałe obiekty w pamięci (typy tabel, natywne pliki IP i funkcje).  Trwałe tabele w pamięci i inne obiekty muszą zostać usunięte i odtworzone jako obiekty inne niż w pamięci przed migracją bazy danych do warstwy usług hiperskali.|
| Replikacja geograficzna  | Nie można jeszcze skonfigurować replikacji geograficznej dla hiperskali bazy danych SQL azure. |
| Kopiowanie bazy danych | Nie można jeszcze użyć kopii bazy danych do utworzenia nowej bazy danych w programie Azure SQL Hyperscale. |
| Integracja TDE/AKV | Szyfrowanie przezroczystej bazy danych przy użyciu usługi Azure Key Vault (powszechnie określane jako Bring-Your-Own-Key lub BYOK) nie jest jeszcze obsługiwane dla usługi Azure SQL Database Hyperscale, jednak TDE z kluczami zarządzanymi usługi jest w pełni obsługiwane. |
|Inteligentne funkcje bazy danych | Z wyjątkiem opcji "Plan wymuszenia" wszystkie inne opcje automatycznego dostrajania nie są jeszcze obsługiwane w hiperskali: opcje mogą wydawać się włączone, ale nie będą żadnych zaleceń ani działań. |
|Analiza wydajności zapytań | Usługi Query Performance Insights nie są obecnie obsługiwane w bazach danych w hiperskali. |
| Zmniejszanie bazy danych | DBCC SHRINKDATABASE lub DBCC SHRINKFILE nie jest obecnie obsługiwany dla baz danych hiperskali. |
| Sprawdzanie integralności bazy danych | Baza danych DBCC CHECKDB nie jest obecnie obsługiwana w bazach danych hiperskali. Zobacz [integralność danych w usłudze Azure SQL Database, aby](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/) uzyskać szczegółowe informacje na temat zarządzania integralnością danych w bazie danych SQL azure. |

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać często zadawane pytania dotyczące hiperskali, zobacz [Często zadawane pytania dotyczące hiperskali](sql-database-service-tier-hyperscale-faq.md).
- Aby uzyskać informacje o warstwach usług, zobacz [Warstwy usług](sql-database-service-tiers.md)
- Zobacz [Omówienie limitów zasobów na serwerze logicznym,](sql-database-resource-limits-logical-server.md) aby uzyskać informacje o limitach na poziomie serwera i subskrypcji.
- Aby uzyskać limity modelu dla pojedynczej bazy danych, zobacz [Limity modelu zakupu oparte na parcie wirtualnym usługi Azure SQL Database dla pojedynczej bazy danych.](sql-database-vcore-resource-limits-single-databases.md)
- Aby uzyskać funkcje i listę porównawczą, zobacz [wspólne funkcje SQL](sql-database-features.md).
