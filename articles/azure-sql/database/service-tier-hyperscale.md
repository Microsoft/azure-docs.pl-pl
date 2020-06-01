---
title: Co to jest warstwa usługi do skalowania?
description: W tym artykule opisano warstwę usługi w modelu zakupów rdzeń wirtualny w Azure SQL Database i wyjaśniono, jak różni się ona od Ogólnego przeznaczenia i Krytyczne dla działania firmy warstw usług.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 05/29/2020
ms.openlocfilehash: 65d7cb60d0d3df43323833f254278c20abacc9d1
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/31/2020
ms.locfileid: "84231214"
---
# <a name="hyperscale-service-tier"></a>Warstwa usługi Hiperskala
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL Database jest oparta na architekturze SQL Server Database Engine, która jest dostosowywana do środowiska chmury w celu zapewnienia dostępności 99,99% nawet w przypadku awarii infrastruktury. Istnieją trzy modele architektury, które są używane w Azure SQL Database:

- Ogólnego przeznaczenia/Standard
- Hiperskala
- Krytyczne dla działania firmy/Premium

Warstwa usługi do skalowania w Azure SQL Database to najnowsza warstwa usługi w modelu zakupu opartego na rdzeń wirtualny. Ta warstwa usług jest wysoce skalowalną warstwą wydajności magazynu i obliczeń, która wykorzystuje architekturę platformy Azure do skalowania w poziomie magazynu i zasobów obliczeniowych dla Azure SQL Database znacznie przekraczające limity dostępne dla Ogólnego przeznaczenia i Krytyczne dla działania firmy warstw usług.

> [!NOTE]
>
> - Aby uzyskać szczegółowe informacje na temat Ogólnego przeznaczenia i Krytyczne dla działania firmy warstw usług w modelu zakupu opartego na rdzeń wirtualny, zobacz [ogólnego przeznaczenia](service-tier-general-purpose.md) i [krytyczne dla działania firmy](service-tier-business-critical.md) warstwy usług. Aby zapoznać się z porównaniem modelu zakupu opartego na rdzeń wirtualny z modelem zakupów opartym na jednostkach DTU, zobacz [Azure SQL Database kupowanie modeli i zasobów](purchasing-models.md).
> - Warstwa usługi do skalowania jest obecnie dostępna tylko dla Azure SQL Database, a nie wystąpienia zarządzanego usługi Azure SQL.

## <a name="what-are-the-hyperscale-capabilities"></a>Jakie są możliwości skalowania

Warstwa usługi w ramach skalowania w Azure SQL Database zapewnia następujące dodatkowe możliwości:

- Obsługa nawet 100 TB rozmiaru bazy danych
- Niemal natychmiastowe kopie zapasowe bazy danych (oparte na migawkach plików przechowywanych w usłudze Azure Blob Storage) bez względu na rozmiar bez wpływu we/wy na zasoby obliczeniowe  
- Szybka baza danych przywraca (w oparciu o migawki plików) w ciągu kilku minut, a nie godzin lub dni (nie jest to rozmiar operacji na danych)
- Wyższa ogólna wydajność z powodu większej przepływności dzienników i krótszych czasów zatwierdzania transakcji niezależnie od woluminów danych
- Szybkie skalowanie w poziomie — można udostępnić co najmniej jeden węzeł tylko do odczytu w celu odciążenia obciążenia odczytu i użycia jako rezerwy gorącą.
- Szybkie skalowanie w górę — możesz w stałym czasie skalować zasoby obliczeniowe w celu uwzględnienia dużych obciążeń, gdy jest to potrzebne, a następnie skalować zasoby obliczeniowe w razie braku potrzeby.

Warstwa usługi do skalowania usuwa wiele praktycznych ograniczeń tradycyjnie widzianych w bazach danych w chmurze. W przypadku, gdy większość innych baz danych jest ograniczona przez zasoby dostępne w jednym węźle, bazy danych w warstwie usługi w ramach skalowania nie mają takich ograniczeń. W przypadku elastycznej architektury pamięci masowej magazyn rośnie w razie konieczności. W rzeczywistości bazy danych nie są tworzone ze zdefiniowanym maksymalnym rozmiarem. Baza danych w ramach skalowania rośnie w miarę potrzeby — a opłaty są naliczane tylko za używane zasoby. W przypadku obciążeń intensywnie korzystających z odczytu warstwa usługi w ramach skalowania umożliwia szybkie skalowanie w poziomie przez inicjowanie obsługi dodatkowych replik odczytu w razie potrzeby w celu odciążenia operacji odczytu.

Ponadto czas wymagany do utworzenia kopii zapasowej bazy danych lub skalowania w górę lub w dół nie jest już związany z ilością danych w bazie danych. Kopie zapasowe baz danych można wykonać w sposób niemal natychmiastowy. Bazę danych można także skalować w ciągu kilku minut w górę lub w dół. Ta funkcja umożliwia zwolnienie Cię z obaw związanych z zapisaniem konfiguracji początkowej.

Aby uzyskać więcej informacji na temat rozmiarów obliczeń dla warstwy usługi w ramach skalowania, zobacz [Charakterystyka warstwy usług](service-tiers-vcore.md#service-tiers).

## <a name="who-should-consider-the-hyperscale-service-tier"></a>Kto powinien wziąć pod uwagę warstwę usługi do skalowania

Warstwa usługi do skalowania jest przeznaczona dla większości obciążeń firmowych, ponieważ zapewnia dużą elastyczność i wysoką wydajność dzięki niezależnej skalowalnemu zasobom obliczeniowym i magazynu. Dzięki możliwości automatycznego skalowania magazynu do 100 TB jest to doskonały wybór dla klientów, którzy:

- W przypadku dużych baz danych w środowisku lokalnym i chcesz przeprowadzić modernizację swoich aplikacji, przechodząc do chmury
- Znajdują się już w chmurze i są ograniczone przez maksymalne ograniczenia rozmiaru bazy danych innych warstw usług (1-4 TB)
- Mają mniejsze bazy danych, ale wymagają szybkiego skalowania w pionie i w poziomie, wysokiej wydajności, natychmiastowej kopii zapasowej i szybkiego przywracania bazy danych.

Warstwa usługi do skalowania obsługuje szeroką gamę obciążeń SQL Server, od czystej OLTP do czystej analizy, ale jest głównie zoptymalizowana pod kątem obciążeń OLTP i transakcji hybrydowych i przetwarzania analitycznego (HTAP).

> [!IMPORTANT]
> Pule elastyczne nie obsługują warstwy usługi skalowania w górę.

## <a name="hyperscale-pricing-model"></a>Model cen w ramach skalowania

Warstwa usługi do skalowania jest dostępna tylko w [modelu rdzeń wirtualny](service-tiers-vcore.md). Aby dostosować się do nowej architektury, model cen jest nieco różny od Ogólnego przeznaczenia lub Krytyczne dla działania firmy warstw usług:

- **Obliczenia**:

  Cena jednostkowa obliczeń w ramach skalowania jest przypadana na replikę. [Korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) cena jest stosowana do automatycznego odczytu replik w skali. Utworzymy replikę podstawową i jedną replikę tylko do odczytu dla bazy danych w ramach jednej skali.  Użytkownicy mogą dostosowywać łączną liczbę replik z uwzględnieniem elementu podstawowego z 1-5.

- **Magazyn**:

  Nie trzeba określać maksymalnego rozmiaru danych podczas konfigurowania bazy danych w ramach skalowania. W warstwie skalowania jest naliczana opłata za magazyn bazy danych na podstawie rzeczywistej alokacji. Magazyn jest automatycznie przypisywany do zakresu od 40 GB do 100 TB, w przyrostach 10 GB. W razie konieczności można zwiększyć wiele plików danych w tym samym czasie. Baza danych wieloskalowego jest tworzona z rozmiarem rozpoczynającym 10 GB i rośnie o 10 GB co 10 minut, aż osiągnie rozmiar 40 GB.

Aby uzyskać więcej informacji na temat cen ze skalowaniem, zobacz [Cennik usługi Azure SQL Database](https://azure.microsoft.com/pricing/details/sql-database/single/)

## <a name="distributed-functions-architecture"></a>Architektura funkcji rozproszonych

W przeciwieństwie do tradycyjnych aparatów baz danych, które mają scentralizowane wszystkie funkcje zarządzania danymi w jednej lokalizacji/procesie (podobnie jak w przypadku rozproszonych baz danych w środowisku produkcyjnym dzisiaj mają wiele kopii aparatu danych monolitycznych), baza danych wieloskalowania oddziela aparat przetwarzania zapytań, w którym semantyka różnych aparatów danych jest niezależna od składników, które zapewniają długoterminowy magazyn i trwałość danych. Dzięki temu pojemność magazynu może być bezproblemowo skalowana w miarę potrzeb (początkowa wartość docelowa to 100 TB). Repliki tylko do odczytu współużytkują te same składniki magazynu, więc żadne kopie danych nie są wymagane do uruchomienia nowej repliki możliwej do odczytu.

Na poniższym diagramie przedstawiono różne typy węzłów w bazie danych w skali:

![architektura](./media/service-tier-hyperscale/hyperscale-architecture.png)

Baza danych wieloskali zawiera następujące różne typy składników:

### <a name="compute"></a>Wystąpienia obliczeniowe

Węzeł obliczeniowy to miejsce, w którym działa silnik relacyjny, więc wszystkie elementy języka, przetwarzanie zapytań i tak dalej występują. Wszystkie interakcje użytkownika z bazą danych w ramach skalowania są wykonywane za pomocą tych węzłów obliczeniowych. Węzły obliczeniowe mają pamięć podręczną opartą na dyskach SSD (z etykietami RBPEX-odporny na błędy w powyższym diagramie), aby zminimalizować liczbę podróży w sieci wymaganych do pobrania strony danych. Istnieje jeden podstawowy węzeł obliczeniowy, w którym są przetwarzane wszystkie obciążenia odczytu i zapisu. Istnieje co najmniej jeden pomocniczy węzeł obliczeniowy działający jako węzły rezerwy aktywnej do pracy w trybie failover, a także działający jako węzeł obliczeniowy tylko do odczytu do odciążania obciążeń odczytu (Jeśli ta funkcja jest wymagana).

### <a name="page-server"></a>Serwer stronicowy

Serwery stron to systemy reprezentujące skalowalny w poziomie aparat magazynu.  Każdy serwer stron jest odpowiedzialny za podzbiór stron w bazie danych.  W sposób nominalny każdy serwer stronicowania kontroluje między 128 GB i 1 TB danych. Żadne dane nie są udostępniane na więcej niż jednym serwerze stronicowania (poza replikami, które są przechowywane pod kątem nadmiarowości i dostępności). Zadaniem serwera stronicowania jest obsługiwanie stron bazy danych do węzłów obliczeniowych na żądanie i aktualizowanie stron jako transakcji, które aktualizują dane. Serwery stronicowania są aktualne przez odtworzenie rekordów dziennika z usługi log. Serwery stron obsługują również pamięć podręczną opartą na dyskach SSD w celu zwiększenia wydajności. Długoterminowe przechowywanie stron danych jest przechowywane w usłudze Azure Storage w celu zapewnienia dodatkowej niezawodności.

### <a name="log-service"></a>Usługa log

Usługa log przyjmuje rekordy dziennika z podstawowej repliki obliczeniowej, utrzymuje je w trwałej pamięci podręcznej i przekazuje rekordy dziennika do pozostałych replik obliczeniowych (aby można było zaktualizować ich pamięć podręczną) oraz odpowiednie serwery stron, aby można było je zaktualizować. W ten sposób wszystkie zmiany danych z podstawowej repliki obliczeniowej są propagowane za pośrednictwem usługi log na wszystkie pomocnicze repliki obliczeniowe i serwery stron. Na koniec rekordy dziennika są wypychane do długoterminowego przechowywania w usłudze Azure Storage, co jest praktycznie nieskończonym repozytorium magazynu. Ten mechanizm eliminuje konieczność częstego obcinania dzienników. Usługa log ma również lokalną pamięć podręczną umożliwiającą przyspieszenie dostępu do rekordów dzienników.

### <a name="azure-storage"></a>Azure Storage

Usługa Azure Storage zawiera wszystkie pliki danych w bazie danych. Serwery stron zachowują aktualne pliki danych w usłudze Azure Storage. Ten magazyn jest używany na potrzeby tworzenia kopii zapasowych, a także do replikacji między regionami platformy Azure. Kopie zapasowe są implementowane przy użyciu migawek magazynu plików danych. Operacje przywracania przy użyciu migawek są szybkie, niezależnie od rozmiaru danych. Dane można przywrócić do dowolnego punktu w czasie w ramach okresu przechowywania kopii zapasowej bazy danych.

## <a name="backup-and-restore"></a>Tworzenie kopii zapasowej i przywracanie

Kopie zapasowe są tworzone na podstawie migawek plików, a tym samym prawie chwilowo. Rozdzielenie magazynu i obliczeń umożliwia wypychanie operacji tworzenia kopii zapasowej/przywracania do warstwy magazynowania w celu zmniejszenia obciążenia związanego z przetwarzaniem w podstawowej replice obliczeniowej. W związku z tym kopia zapasowa bazy danych nie ma wpływu na wydajność podstawowego węzła obliczeniowego; Analogicznie, przywracanie odbywa się przez przywrócenie migawek plików, co nie jest rozmiarem operacji danych. Przywracanie jest operacją o stałym czasie, a nawet kilka baz danych można przywrócić w ciągu kilku minut, a nie godzin lub dni. Tworzenie nowych baz danych przez przywrócenie istniejącej kopii zapasowej obejmuje również korzystanie z tej funkcji: Tworzenie kopii bazy danych do celów deweloperskich i testowych, nawet w przypadku baz danych o rozmiarze terabajtów, jest doable w ciągu kilku minut.

## <a name="scale-and-performance-advantages"></a>Zalety skalowania i wydajności

Dzięki możliwości szybkiej zmiany w górę i w dół dodatkowych węzłów obliczeniowych tylko do odczytu architektura skalowania zapewnia znaczące możliwości skalowania odczytu, a także zwalnia podstawowy węzeł obliczeniowy do obsługi większej liczby żądań zapisu. Ponadto węzły obliczeniowe można szybko skalować w górę i w dół ze względu na architekturę magazynu udostępnionego w architekturze.

## <a name="create-a-hyperscale-database"></a>Tworzenie bazy danych w ramach skalowania

Bazę danych ze skalą można utworzyć przy użyciu [Azure Portal](https://portal.azure.com), [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current), [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/new-azurermsqldatabase) lub [interfejsu wiersza polecenia](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create). Bazy danych ze skalowaniem są dostępne tylko przy użyciu [modelu zakupu opartego na rdzeń wirtualny](service-tiers-vcore.md).

Następujące polecenie T-SQL tworzy bazę danych w skali. W instrukcji należy określić zarówno cel wersji, jak i usługi `CREATE DATABASE` . Zapoznaj się z [limitami zasobów](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-single-databases#hyperscale---provisioned-compute---gen4) , aby uzyskać listę prawidłowych celów usługi.

```sql
-- Create a Hyperscale Database
CREATE DATABASE [HyperscaleDB1] (EDITION = 'Hyperscale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```

Spowoduje to utworzenie bazy danych w ramach skalowania na 5 rdzeń sprzęt z 4 rdzeniami.

## <a name="upgrade-existing-database-to-hyperscale"></a>Uaktualnianie istniejącej bazy danych do funkcji skalowania

Istniejące bazy danych można przenosić w Azure SQL Database do skalowania przy użyciu [Azure Portal](https://portal.azure.com), [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current), [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqldatabase) lub [interfejsu wiersza polecenia](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update). W tej chwili jest to jednokierunkowa migracja. Nie można przenieść baz danych ze skalowania do innej warstwy usług, poza eksportowaniem i importowaniem danych. W przypadku dowodu koncepcji (POCs) zalecamy utworzenie kopii produkcyjnych baz danych i migrowanie kopii do obszaru skalowania. Migrowanie istniejącej bazy danych w Azure SQL Database do warstwy skalowania jest rozmiarem operacji na danych.

Następujące polecenie T-SQL przenosi bazę danych do warstwy usługi. W instrukcji należy określić zarówno cel wersji, jak i usługi `ALTER DATABASE` .

```sql
-- Alter a database to make it a Hyperscale Database
ALTER DATABASE [DB2] MODIFY (EDITION = 'Hyperscale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```

## <a name="connect-to-a-read-scale-replica-of-a-hyperscale-database"></a>Nawiązywanie połączenia z repliką w skali odczytu bazy danych

W bazach danych w ramach skalowania `ApplicationIntent` argument w parametrach połączenia dostarczonych przez klienta wskazuje, czy połączenie jest kierowane do repliki zapisu, czy do repliki pomocniczej tylko do odczytu. Jeśli `ApplicationIntent` zestaw do `READONLY` i baza danych nie mają repliki pomocniczej, połączenie zostanie przekazane do repliki podstawowej i domyślnie zostanie `ReadWrite` zachowane.

```cmd
-- Connection string with application intent
Server=tcp:<myserver>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

Repliki pomocnicze w ramach skalowania są identyczne, przy użyciu tego samego celu poziomu usługi co replika podstawowa. Jeśli istnieje więcej niż jedna replika pomocnicza, obciążenie jest dystrybuowane między wszystkie dostępne pomocnicze. Każda replika pomocnicza jest aktualizowana niezależnie, więc różne repliki mogą mieć różne opóźnienia danych względem repliki podstawowej.

## <a name="database-high-availability-in-hyperscale"></a>Wysoka dostępność bazy danych w ramach skalowania

Podobnie jak w przypadku wszystkich innych warstw usług, funkcja Moja Skala gwarantuje trwałość danych dla zatwierdzonych transakcji niezależnie od dostępności repliki obliczeniowej. Zakres przestoju spowodowany przez replikę podstawową staje się niedostępny, zależy od typu trybu failover (zaplanowanego a nieplanowanego) oraz od obecności co najmniej jednej repliki pomocniczej. W planowanej pracy w trybie failover (tj. zdarzeniu konserwacji) system tworzy nową replikę podstawową przed zainicjowaniem trybu failover lub używa istniejącej repliki pomocniczej jako lokalizacji docelowej trybu failover. W przypadku nieplanowanego przejścia w tryb failover (tj. awaria sprzętowa repliki podstawowej) system używa repliki pomocniczej jako miejsca docelowego trybu failover, jeśli taki istnieje, lub tworzy nową replikę podstawową z puli dostępnej pojemności obliczeniowej. W tym drugim przypadku czas przestoju jest dłuższy z powodu dodatkowych kroków wymaganych do utworzenia nowej repliki podstawowej.

Aby zapoznać się z umową SLA, zobacz [Umowa SLA dla Azure SQL Database](https://azure.microsoft.com/support/legal/sla/sql-database/).

## <a name="disaster-recovery-for-hyperscale-databases"></a>Odzyskiwanie po awarii dla baz danych w ramach skalowania

### <a name="restoring-a-hyperscale-database-to-a-different-geography"></a>Przywracanie bazy danych ze skalowaniem do innej lokalizacji geograficznej

Jeśli trzeba przywrócić Azure SQL Database przeskalować bazę danych do regionu innego niż ten, w którym jest obecnie hostowana, w ramach operacji odzyskiwania po awarii lub przechodzenia do szczegółów, relokacji lub z innego powodu, podstawowa metoda polega na wykonaniu przywracania geograficznego bazy danych.  Obejmuje to dokładnie te same kroki jak w przypadku przywracania innych SQL Database w innym regionie:

1. Utwórz [serwer](logical-servers.md) w regionie docelowym, jeśli jeszcze nie masz odpowiedniego serwera.  Ten serwer powinien należeć do tej samej subskrypcji co oryginalny serwer (źródłowy).
2. Postępuj zgodnie z instrukcjami w temacie dotyczącym [przywracania geograficznego](https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups#geo-restore) strony na przywracaniu baz danych Azure SQL z automatycznych kopii zapasowych.

> [!NOTE]
> Ze względu na to, że źródło i cel znajdują się w oddzielnych regionach, baza danych nie może współużytkować magazynu migawek ze źródłową bazą danych jako do przywracania geograficznego, co kończy się niezwykle szybko. W przypadku przywracania geograficznego bazy danych w ramach skalowania jest to operacja o rozmiarze danych, nawet jeśli obiekt docelowy znajduje się w sparowanym regionie magazynu z replikacją geograficzną.  Oznacza to, że wykonanie operacji przywracania geograficznego będzie trwać proporcjonalnie do rozmiaru przywracanej bazy danych.  Jeśli obiekt docelowy znajduje się w sparowanym regionie, kopia będzie znajdować się w regionie, który będzie znacznie szybszy niż kopiowanie między regionami, ale nadal będzie operacją typu danych.

## <a name="available-regions"></a><a name=regions></a>Dostępne regiony

Warstwa skalowania Azure SQL Database jest obecnie dostępna w następujących regionach:

- Australia Wschodnia
- Australia Południowo-Wschodnia
- Brazylia Południowa
- Kanada Środkowa
- Środkowe stany USA
- Chiny Wschodnie 2
- Chiny Północne 2
- Azja Wschodnia
- Wschodnie stany USA
- Wschodnie stany USA 2
- Francja Środkowa
- Japonia Wschodnia
- Japonia Zachodnia
- Korea Środkowa
- Korea Południowa
- Północno-środkowe stany USA
- Europa Północna
- Północna Republika Południowej Afryki
- Południowo-środkowe stany USA
- Azja Południowo-Wschodnia
- Południowe Zjednoczone Królestwo
- Zachodnie Zjednoczone Królestwo
- Europa Zachodnia
- Zachodnie stany USA
- Zachodnie stany USA 2

Jeśli chcesz utworzyć bazę danych w formie wieloskali w regionie, który nie jest wymieniony jako obsługiwany, możesz wysłać żądanie dołączania za pośrednictwem Azure Portal. Aby uzyskać instrukcje, zobacz temat [żądanie zwiększania limitu przydziału dla Azure SQL Database](quota-increase-request.md) , aby uzyskać instrukcje. Podczas przesyłania żądania należy przestrzegać następujących wytycznych:

- Użyj limitu przydziału bazy danych SQL [żądania limitu przydziału](quota-increase-request.md#other) .
- W polu Szczegóły tekstu Dodaj jednostki SKU obliczeń/całkowite rdzenie, w tym odczytane repliki.
- Określ także szacowaną wartość TB.

## <a name="known-limitations"></a>Znane ograniczenia

Są to bieżące ograniczenia dotyczące warstwy usług w ramach skalowania na poziomie.  Aktywnie pracujemy nad usunięciem możliwie największej liczby ograniczeń.

| Problem | Opis |
| :---- | :--------- |
| Okienko zarządzanie kopiami zapasowymi dla serwera nie pokazuje baz danych ze skalowaniem, zostaną one przefiltrowane z widoku  | Funkcja wieloskalowania ma oddzielną metodę zarządzania kopiami zapasowymi, a w związku z tym ustawienia przechowywania kopii zapasowych długoterminowego przechowywania i punktu w czasie nie są stosowane. W związku z tym bazy danych nie są wyświetlane w okienku zarządzanie kopią zapasową.|
| Przywracanie do określonego momentu | Baza danych bez skalowania nie może zostać przywrócona jako baza danych ze skalą i nie może zostać przywrócona jako baza danych bez skalowania. W przypadku bazy danych bez skalowania, która została zmigrowana do skalowania przez zmianę jej warstwy usług, Przywróć do punktu w czasie przed migracją i w ramach okresu przechowywania kopii zapasowej bazy danych można [programowo](recovery-using-backups.md#programmatically-performing-recovery-by-using-automated-backups). Przywrócona baza danych nie będzie skalowana. |
| Jeśli baza danych ma co najmniej jeden plik danych o rozmiarze większym niż 1 TB, migracja nie powiedzie się | W niektórych przypadkach może być możliwe obejście tego problemu, zmniejszając duże ilości plików poniżej 1 TB. W przypadku migrowania bazy danych używanej podczas procesu migracji upewnij się, że żaden plik nie będzie większy niż 1 TB. Użyj następującego zapytania, aby określić rozmiar plików bazy danych. `SELECT *, name AS file_name, size * 8. / 1024 / 1024 AS file_size_GB FROM sys.database_files WHERE type_desc = 'ROWS'`;|
| Wystąpienie zarządzane SQL | Wystąpienie zarządzane Azure SQL nie jest obecnie obsługiwane w bazach danych. |
| Pule elastyczne |  Pule elastyczne nie są obecnie obsługiwane ze skalą.|
| Migracja do funkcji Moje skalowanie jest obecnie operacją jednokierunkową | Po przeprowadzeniu migracji bazy danych do warstwy usługi nie można migrować jej bezpośrednio na warstwę usług, która nie jest w skali. W obecnym czasie jedynym sposobem migrowania bazy danych z Azure Databricks Azure Data Factory funkcji ze skalowaniem do poziomu non------------------------------------------- BACPAC Export/Import z Azure Portal, z programu PowerShell przy użyciu polecenia [New-AzSqlDatabaseExport](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabaseexport) lub [New-AzSqlDatabaseImport](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabaseimport), z interfejsu wiersza polecenia platformy Azure przy użyciu polecenia [AZ SQL DB Export](https://docs.microsoft.com/cli/azure/sql/db?view=azure-cli-latest#az-sql-db-export) i [AZ SQL DB import](https://docs.microsoft.com/cli/azure/sql/db?view=azure-cli-latest#az-sql-db-import)i from [API REST](https://docs.microsoft.com/rest/api/sql/databases%20-%20import%20export) nie jest obsługiwana. BACPAC Import/Export w przypadku mniejszych baz danych w postaci większej skali (do 200 GB) jest obsługiwana przy użyciu programu SSMS i [sqlpackage](https://docs.microsoft.com/sql/tools/sqlpackage) w wersji 18,4 lub nowszej. W przypadku większych baz danych eksport/import BACPAC może zająć dużo czasu i może się nie powieść z różnych powodów.|
| Migracja baz danych z trwałymi obiektami OLTP w pamięci | Funkcja przeskalowania obsługuje tylko nietrwałe obiekty OLTP w pamięci (typy tabel, natywne SPs i funkcje).  Trwałe tabele OLTP w pamięci i inne obiekty muszą zostać porzucone i ponownie utworzone jako obiekty oparte na dyskach przed migracją bazy danych do warstwy usługi w ramach skalowania.|
| Replikacja geograficzna  | Nie można jeszcze skonfigurować replikacji geograficznej na potrzeby Azure SQL Database skalowania. |
| Kopia bazy danych | Nie można jeszcze użyć kopii bazy danych w celu utworzenia nowej bazy danych w funkcji wieloskalowania SQL platformy Azure. |
| Integracja TDE/AKV | Nieprzezroczyste szyfrowanie bazy danych przy użyciu Azure Key Vault (nazywanego również przenoszeniem własnym kluczem lub BYOK) jest obecnie dostępne w wersji zapoznawczej. |
| Funkcje inteligentnej bazy danych | Z wyjątkiem opcji "Wymuś plan" wszystkie inne opcje dostrajania automatycznego nie są jeszcze obsługiwane w obszarze skalowanie: opcje mogą być dostępne, ale nie zostaną wykonane żadne zalecenia ani działania. |
| Analiza wydajności zapytań | Szczegółowe informacje o wydajności zapytań nie są obecnie obsługiwane w przypadku baz danych w skali. |
| Zmniejsz bazę danych | Polecenia DBCC SHRINKDATABASE lub DBCC SHRINKFILE nie są obecnie obsługiwane w przypadku baz danych w ramach skalowania. |
| Sprawdzanie integralności bazy danych | Polecenie DBCC CHECKDB nie jest obecnie obsługiwane w przypadku baz danych w skali. Polecenia DBCC CHECKFILEGROUP i DBCC CHECKTABLE mogą służyć jako obejście problemu. Aby uzyskać szczegółowe informacje na temat zarządzania integralnością danych w programie Azure SQL Database, zobacz temat [integralność danych w Azure SQL Database](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/) . |

## <a name="next-steps"></a>Następne kroki

- Często zadawane pytania dotyczące skalowania można znaleźć na [często zadawanych pytaniach dotyczących skalowania](service-tier-hyperscale-frequently-asked-questions-faq.md).
- Aby uzyskać informacje o warstwach usług, zobacz [warstwy usług](purchasing-models.md)
- Zobacz [Omówienie limitów zasobów na serwerze](resource-limits-logical-server.md) , aby uzyskać informacje na temat limitów na poziomach serwera i subskrypcji.
- Aby uzyskać ograniczenia modelu zakupów dla pojedynczej bazy danych, zobacz [Azure SQL Database limity modelu zakupu opartego na rdzeń wirtualny dla jednej bazy danych](resource-limits-vcore-single-databases.md).
- Aby zapoznać się z funkcjami i listą porównania, zobacz [funkcje wspólne SQL](features-comparison.md).
