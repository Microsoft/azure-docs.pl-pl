---
title: Warstwa bezserwerowych usług obliczeniowych
description: W tym artykule opisano nową warstwę zasobów obliczeniowych bez serwera i porównano ją z istniejącą warstwą aprowizowanych zasobów obliczeniowych dla Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: test sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: sstein
ms.date: 4/15/2021
ms.openlocfilehash: ea9d5a5c39bf73ede2391c586f09dd95ff79b63c
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531972"
---
# <a name="azure-sql-database-serverless"></a>Azure SQL Database bez serwera
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Warstwa bezserwerowa jest warstwą obliczeniową dla pojedynczych baz danych w usłudze Azure SQL Database, która automatycznie skaluje obliczenia na podstawie zapotrzebowania na obciążenia i rachunków dla ilości używanych obliczeń na sekundę. Ponadto warstwa bezserwerowych usług obliczeniowych automatycznie wstrzymuje bazy danych w okresach nieaktywności, gdy są naliczane opłaty tylko za magazyn, oraz automatycznie wznawia bazy danych po ponownym pojawieniu się aktywności.

## <a name="serverless-compute-tier"></a>Warstwa bezserwerowych usług obliczeniowych

Warstwa obliczeniowa bez serwera dla pojedynczych baz danych w Azure SQL Database jest sparametryzowana przez zakres skalowania automatycznego obliczeń i opóźnienie automatycznego wstrzymywania. Konfiguracja tych parametrów kształtuje środowisko wydajności bazy danych i koszt obliczeń.

![rozliczenia bez serwera](./media/serverless-tier-overview/serverless-billing.png)

### <a name="performance-configuration"></a>Konfiguracja wydajności

- Minimalna **liczba rdzeni wirtualnych i** maksymalna liczba **rdzeni** wirtualnych to konfigurowalne parametry definiujące zakres pojemności obliczeniowej dostępnej dla bazy danych. Limity pamięci i we/wy są proporcjonalne do określonego zakresu rdzeni wirtualnych.  
- Opóźnienie **automatycznego wstrzymywania** jest konfigurowalnym parametrem, który definiuje okres, przez który baza danych musi być nieaktywna, zanim zostanie automatycznie wstrzymana. Baza danych jest automatycznie wznawiana po następnym zalogowaniu lub innym działaniu.  Alternatywnie można wyłączyć automatyczne wsłuchianie.

### <a name="cost"></a>Koszt

- Koszt bez serwera bazy danych to suma kosztów zasobów obliczeniowych i magazynu.
- Gdy użycie zasobów obliczeniowych wynosi między skonfigurowanymi limitami minimalnych i maksymalnych, koszt obliczeń jest oparty na rdzeniu wirtualnych i użytej pamięci.
- Gdy użycie zasobów obliczeniowych jest poniżej minimalnych skonfigurowanych limitów, koszt obliczeń jest oparty na minimalnych skonfigurowanych rdzeniach wirtualnych i minimalnej ilości pamięci.
- Po wstrzymaniu bazy danych koszt zasobów obliczeniowych wynosi zero i są ponoszone tylko koszty magazynowania.
- Koszt magazynu jest określany w taki sam sposób jak w aprowizowanych warstwach obliczeniowych.

Aby uzyskać więcej informacji o kosztach, zobacz [Rozliczenia](serverless-tier-overview.md#billing).

## <a name="scenarios"></a>Scenariusze

Opcja bezserwerowa jest zoptymalizowana pod kątem stosunku ceny do wydajności i jest przeznaczona dla pojedynczych baz danych wykorzystywanych sporadycznie i mających nieprzewidywalne wzorce użycia zezwalające na niewielkie opóźnienia wynikające z konieczności przygotowania do obliczeń po okresach bezczynności. W przeciwieństwie do niej aprowizowana warstwa obliczeniowa jest zoptymalizowana pod kątem stosunku ceny do wydajności i jest przeznaczona dla pojedynczych lub wielu baz danych w pulach elastycznych z wyższym średnim użyciem, w przypadku których nie mogą występować żadne opóźnienia w obliczeniach.

### <a name="scenarios-well-suited-for-serverless-compute"></a>Scenariusze dobrze dopasowane do bez serwera obliczeniowego

- Pojedyncze bazy danych ze sporadycznymi, nieprzewidywalnymi wzorcami użycia przeplatane okresami braku aktywności i niższym średnim wykorzystaniem zasobów obliczeniowych w czasie.
- Pojedyncze bazy danych w aprowizowanych warstwach obliczeniowych, które są często ponownie skalowane, oraz klienci, którzy wolą delegować ponowne skalowanie obliczeń do usługi.
- Nowe pojedyncze bazy danych bez historii użycia, w przypadku których obliczanie rozmiaru jest trudne lub nie jest możliwe do oszacowania przed wdrożeniem w SQL Database.

### <a name="scenarios-well-suited-for-provisioned-compute"></a>Scenariusze dobrze dopasowane do aprowowanych zasobów obliczeniowych

- Pojedyncze bazy danych z bardziej regularnymi, przewidywalnymi wzorcami użycia i wyższym średnim wykorzystaniem zasobów obliczeniowych w czasie.
- Bazy danych, które nie tolerują przerw w wydajności wynikające z częstszego przycinania pamięci lub opóźnień wznawiania ze stanu wstrzymania.
- Wiele baz danych ze sporadycznie nieprzewidywalnymi wzorcami użycia, które można skonsolidować w pulach elastycznych w celu lepszej optymalizacji cen i wydajności.

## <a name="comparison-with-provisioned-compute-tier"></a>Porównanie z aprowizowana warstwą obliczeniową

W poniższej tabeli podsumowano różnice między warstwą zasobów obliczeniowych bez serwera a aprowizowana warstwą obliczeniową:

| | **Bezserwerowe usługi obliczeniowe** | **Aprowizowane zasoby obliczeniowe** |
|:---|:---|:---|
|**Wzorzec użycia bazy danych**| Sporadyczne, nieprzewidywalne użycie przy niższym średnim wykorzystaniu zasobów obliczeniowych w czasie. | Bardziej regularne wzorce użycia z wyższym średnim wykorzystaniem zasobów obliczeniowych w czasie lub wiele baz danych korzystających z elastycznych pul.|
| **Nakład pracy w zakresie zarządzania wydajnością** |Niższa|Wyższa|
|**Skalowanie obliczeń**|Automatyczny|Ręcznie|
|**Czas odpowiedzi obliczeń**|Niższe po okresach nieaktywnych|Natychmiastowe|
|**Poziom szczegółowości rozliczeń**|Sekundę|Godzinę|

## <a name="purchasing-model-and-service-tier"></a>Model zakupów i warstwa usługi

SQL Database bez serwera jest obecnie obsługiwana tylko w warstwie Ogólnego przeznaczenia na sprzęcie generacji 5 w modelu zakupów z rdzeniami wirtualnych.

## <a name="autoscaling"></a>Skalowanie automatyczne

### <a name="scaling-responsiveness"></a>Czas odpowiedzi skalowania

Ogólnie rzecz biorąc, bez użycia serwera bazy danych są uruchamiane na maszynie o wystarczającej pojemności, aby zaspokoić zapotrzebowanie na zasoby bez zakłóceń dla dowolnej ilości zasobów obliczeniowych żądanych w ramach limitów ustawionych przez maksymalną wartość rdzeni wirtualnych. Czasami równoważenie obciążenia odbywa się automatycznie, jeśli maszyna nie może spełnić zapotrzebowania na zasoby w ciągu kilku minut. Jeśli na przykład zapotrzebowanie na zasoby wynosi 4 rdzenie wirtualne, ale dostępne są tylko 2 rdzenie wirtualne, równoważenie obciążenia może potrwać do kilku minut, zanim zostaną udostępnione 4 rdzenie wirtualne. Baza danych pozostaje w trybie online podczas równoważenia obciążenia, z wyjątkiem krótkiego okresu na końcu operacji, gdy połączenia są porzucane.

### <a name="memory-management"></a>Zarządzanie pamięcią

Pamięć dla bez serwera baz danych jest generowana częściej niż w przypadku aprowowanych baz danych obliczeniowych. To zachowanie jest ważne, aby kontrolować koszty bez serwera i może mieć wpływ na wydajność.

#### <a name="cache-reclamation"></a>Odzyskiwanie pamięci podręcznej

W przeciwieństwie do aprowowanych baz danych obliczeniowych pamięć z pamięci podręcznej SQL jest generowana z bez serwera, gdy wykorzystanie procesora CPU lub aktywnej pamięci podręcznej jest niskie.

- Użycie aktywnej pamięci podręcznej jest uznawane za niskie, gdy całkowity rozmiar ostatnio używanych wpisów pamięci podręcznej spadnie poniżej progu przez określony czas.
- Po wyzwoleniu odzyskiwania pamięci podręcznej rozmiar docelowej pamięci podręcznej jest zmniejszany przyrostowo do ułamka poprzedniego rozmiaru, a odzyskiwanie jest kontynuowane tylko wtedy, gdy użycie pozostaje niskie.
- W przypadku odzyskiwania pamięci podręcznej zasady wybierania wpisów pamięci podręcznej do eksmisji są takie same jak w przypadku aprowizacji baz danych obliczeniowych, gdy użycie pamięci jest wysokie.
- Rozmiar pamięci podręcznej nigdy nie jest zmniejszany poniżej minimalnego limitu pamięci zdefiniowanego przez minimalne liczby rdzeni wirtualnych, które można skonfigurować.

W bezużyteczny i aprowizowanych bazach danych obliczeniowych wpisy pamięci podręcznej mogą zostać eksmisje, jeśli jest używana cała dostępna pamięć.

Należy pamiętać, że gdy wykorzystanie procesora CPU jest niskie, aktywne wykorzystanie pamięci podręcznej może pozostawać wysokie w zależności od wzorca użycia i uniemożliwić odzyskiwanie pamięci.  Ponadto mogą być dodatkowe opóźnienia po zatrzymaniu aktywności użytkownika przed odzyskiwaniem pamięci z powodu okresowych procesów w tle, które reagują na działania poprzednich użytkowników.  Na przykład operacje usuwania i zadania oczyszczania QDS generują rekordy odujemne, które są oznaczone do usunięcia, ale nie są fizycznie usuwane, dopóki proces oczyszczania nie zostanie uruchomiony, co może obejmować odczytywanie stron danych do pamięci podręcznej.

#### <a name="cache-hydration"></a>Buforowanie buforowania

Pamięć podręczna SQL rośnie wraz z pobieraniem danych z dysku w taki sam sposób i z taką samą szybkością jak w przypadku aprowowanych baz danych. Gdy baza danych jest zajęta, pamięć podręczna może rosnąć bez ograniczeń do maksymalnego limitu pamięci.

## <a name="auto-pausing-and-auto-resuming"></a>Automatyczne wsumowanie i automatyczne wznawianie

### <a name="auto-pausing"></a>Automatyczne wsłuchianie

Automatyczne wstrzymywanie jest wyzwalane, jeśli dla czasu trwania opóźnienia automatycznego wstrzymywania są spełnione wszystkie następujące warunki:

- Liczba sesji = 0
- Procesor CPU = 0 dla obciążenia użytkownika uruchomionego w puli użytkowników

W razie potrzeby dostępna jest opcja wyłączenia automatycznego wsusowania.

Poniższe funkcje nie obsługują automatycznego wsusowania, ale obsługują automatyczne skalowanie.  Jeśli są używane dowolne z następujących funkcji, automatyczne wsłuchianie musi być wyłączone, a baza danych pozostanie w trybie online niezależnie od czasu braku aktywności bazy danych:

- Replikacja geograficzna (aktywna replikacja geograficzna i grupy automatycznego trybu failover).
- Długoterminowe przechowywanie kopii zapasowych(LTR, Long-Term Backup Retention).
- Baza danych synchronizacji używana podczas synchronizacji danych SQL.  W odróżnieniu od baz danych synchronizacji bazy danych centrum i bazy danych członkowskie obsługują automatyczne wsłuchianie.
- Aliasy DNS
- Baza danych zadań używana w zadaniach elastycznych (wersja zapoznawcza).

Automatyczne wstrzymanie jest tymczasowo uniemożliwiane podczas wdrażania niektórych aktualizacji usługi, które wymagają, aby baza danych było w trybie online.  W takich przypadkach automatyczne wstrzymanie staje się ponownie dozwolone po zakończeniu aktualizacji usługi.

### <a name="auto-resuming"></a>Automatyczne wznawianie

Automatyczne wznawianie jest wyzwalane, jeśli dowolny z następujących warunków jest w dowolnym momencie prawdziwy:

|Cecha|Wyzwalacz automatycznego wznawiania|
|---|---|
|Uwierzytelnianie i autoryzacja|Zaloguj się|
|Wykrywanie zagrożeń|Włączanie/wyłączanie ustawień wykrywania zagrożeń na poziomie bazy danych lub serwera.<br>Modyfikowanie ustawień wykrywania zagrożeń na poziomie bazy danych lub serwera.|
|Odnajdowanie i klasyfikacja danych|Dodawanie, modyfikowanie, usuwanie lub wyświetlanie etykiet czułości|
|Inspekcja|Wyświetlanie rekordów inspekcji.<br>Aktualizowanie lub wyświetlanie zasad inspekcji.|
|Maskowanie danych|Dodawanie, modyfikowanie, usuwanie lub wyświetlanie reguł maskowania danych|
|Transparent Data Encryption|Wyświetlanie stanu lub stanu przezroczystego szyfrowania danych|
|Ocena luk w zabezpieczeniach|Skanowania ad hoc i okresowe skanowania, jeśli są włączone|
|Magazyn danych zapytań (wydajność)|Modyfikowanie lub wyświetlanie ustawień magazynu zapytań|
|Zalecenia dotyczące wydajności|Wyświetlanie lub stosowanie zaleceń dotyczących wydajności|
|Automatyczne dostrajanie|Stosowanie i weryfikacja rekomendacji automatycznego dostrajania, takich jak automatyczne indeksowanie|
|Kopiowanie bazy danych|Utwórz bazę danych jako kopię.<br>Eksportowanie do pliku BACPAC.|
|Synchronizacja danych SQL|Synchronizacja baz danych centrum i członkowskich baz danych uruchamianych zgodnie z konfigurowalnym harmonogramem lub wykonywanych ręcznie|
|Modyfikowanie niektórych metadanych bazy danych|Dodawanie nowych tagów bazy danych.<br>Zmiana maksymalnej wersji rdzeni wirtualnych, minimalnych rdzeni wirtualnych lub opóźnienia automatycznego wsadu.|
|SQL Server Management Studio (SSMS)|Użycie programu SSMS w wersji wcześniejszej niż 18.1 i otwarcie nowego okna zapytania dla dowolnej bazy danych na serwerze spowoduje wznowienie wszystkich automatycznie wstrzymanych baz danych na tym samym serwerze. To zachowanie nie występuje w przypadku korzystania z programu SSMS w wersji 18.1 lub nowszej.|

Monitorowanie, zarządzanie lub inne rozwiązania wykonujące dowolną z wymienionych powyżej operacji wyzwoli automatyczne wznawianie.

Automatyczne wznawianie jest również wyzwalane podczas wdrażania niektórych aktualizacji usługi, które wymagają, aby baza danych było w trybie online.

### <a name="connectivity"></a>Łączność

Jeśli baza danych bez serwera jest wstrzymana, pierwsze logowanie wznowi bazę danych i zwróci błąd informujący, że baza danych jest niedostępna z kodem błędu 40613. Po wznowieniu bazy danych należy ponownie zalogować się, aby nawiązać połączenie. Nie należy modyfikować klientów bazy danych z logiką ponawiania połączenia.

### <a name="latency"></a>Opóźnienie

Opóźnienie automatycznego wznawiania i automatycznego wstrzymywania bez serwera bazy danych wynosi zwykle 1 minutę do automatycznego wznowienia i 1–10 minut na automatyczne wstrzymanie.

### <a name="customer-managed-transparent-data-encryption-byok"></a>Transparent Data Encryption (BYOK) zarządzane przez klienta

Jeśli korzystanie [z funkcji Transparent Data Encryption](transparent-data-encryption-byok-overview.md) (BYOK) zarządzanej przez klienta i bez serwera bazy danych jest automatycznie wstrzymywane w przypadku usunięcia lub odwołania klucza, baza danych pozostaje w stanie automatycznego wstrzymania.  W takim przypadku po następnym wznowieniu bazy danych baza danych stanie się niedostępna w ciągu około 10 minut.  Gdy baza danych stanie się niedostępna, proces odzyskiwania będzie taki sam jak w przypadku aprowowanych obliczeniowych baz danych.  Jeśli baza danych bez serwera jest w trybie online w przypadku usunięcia lub odwołania klucza, baza danych również stanie się niedostępna w ciągu około 10 minut w taki sam sposób jak w przypadku aprowowanych obliczeniowych baz danych.

## <a name="onboarding-into-serverless-compute-tier"></a>Dołączanie do warstwy bez serwera obliczeniowego

Tworzenie nowej bazy danych lub przenoszenie istniejącej bazy danych do warstwy obliczeniowej bez serwera jest takie samo jak tworzenie nowej bazy danych w aprowizowanych warstwach obliczeniowych i obejmuje następujące dwa kroki.

1. Określ cel usługi. Cel usługi określa warstwę usługi, generację sprzętu i maksymalną wartość rdzeni wirtualnych. Aby uzyskać informacje na temat opcji celu usługi, zobacz [Serverless resource limits (Limity zasobów bez serwera)](resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5)


2. Opcjonalnie określ minimalne opóźnienie rdzeni wirtualnych i automatycznego wsadu, aby zmienić ich wartości domyślne. W poniższej tabeli przedstawiono dostępne wartości dla tych parametrów.

   |Parametr|Opcje wartości|Wartość domyślna|
   |---|---|---|---|
   |Minimalna rdzenie wirtualne|Zależy od skonfigurowanej maksymalnej liczby rdzeni wirtualnych — zobacz [limity zasobów.](resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5)|0,5 rdzeni wirtualnych|
   |Opóźnienie automatycznego wsadu|Minimum: 60 minut (1 godzina)<br>Maksymalnie: 10080 minut (7 dni)<br>Przyrosty: 10 minut<br>Wyłącz automatyczne wsad: -1|60 minut|


### <a name="create-a-new-database-in-the-serverless-compute-tier"></a>Tworzenie nowej bazy danych w warstwie obliczeniowej bez serwera

Poniższe przykłady tworzą nową bazę danych w warstwie obliczeniowej bez serwera.

#### <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Zobacz [Szybki start: tworzenie pojedynczej bazy danych w Azure SQL Database użyciu Azure Portal](single-database-create-quickstart.md).


#### <a name="use-powershell"></a>Korzystanie z programu PowerShell

```powershell
New-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName `
  -ComputeModel Serverless -Edition GeneralPurpose -ComputeGeneration Gen5 `
  -MinVcore 0.5 -MaxVcore 2 -AutoPauseDelayInMinutes 720
```
#### <a name="use-the-azure-cli"></a>Używanie interfejsu wiersza polecenia platformy Azure

```azurecli
az sql db create -g $resourceGroupName -s $serverName -n $databaseName `
  -e GeneralPurpose -f Gen5 --min-capacity 0.5 -c 2 --compute-model Serverless --auto-pause-delay 720
```


#### <a name="use-transact-sql-t-sql"></a>Korzystanie z języka Transact-SQL (T-SQL)

W przypadku korzystania z języka T-SQL wartości domyślne są stosowane dla minimalnych rdzeni wirtualnych i opóźnienia automatycznego wsadu.

```sql
CREATE DATABASE testdb
( EDITION = 'GeneralPurpose', SERVICE_OBJECTIVE = 'GP_S_Gen5_1' ) ;
```

Aby uzyskać szczegółowe informacje, zobacz [CREATE DATABASE](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current&preserve-view=true).  

### <a name="move-a-database-from-the-provisioned-compute-tier-into-the-serverless-compute-tier"></a>Przenoszenie bazy danych z aprowizowanych warstw obliczeniowych do warstwy zasobów obliczeniowych bez serwera

W poniższych przykładach baza danych jest przenoszona z aprowizowanych warstw obliczeniowych do warstwy zasobów obliczeniowych bez serwera.

#### <a name="use-powershell"></a>Korzystanie z programu PowerShell


```powershell
Set-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName `
  -Edition GeneralPurpose -ComputeModel Serverless -ComputeGeneration Gen5 `
  -MinVcore 1 -MaxVcore 4 -AutoPauseDelayInMinutes 1440
```

#### <a name="use-the-azure-cli"></a>Używanie interfejsu wiersza polecenia platformy Azure

```azurecli
az sql db update -g $resourceGroupName -s $serverName -n $databaseName `
  --edition GeneralPurpose --min-capacity 1 --capacity 4 --family Gen5 --compute-model Serverless --auto-pause-delay 1440
```


#### <a name="use-transact-sql-t-sql"></a>Korzystanie z języka Transact-SQL (T-SQL)

W przypadku korzystania z języka T-SQL wartości domyślne są stosowane dla minimalnych rdzeni wirtualnych i opóźnienia automatycznego wstrzymywania.

```sql
ALTER DATABASE testdb 
MODIFY ( SERVICE_OBJECTIVE = 'GP_S_Gen5_1') ;
```

Aby uzyskać szczegółowe informacje, zobacz [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current&preserve-view=true).

### <a name="move-a-database-from-the-serverless-compute-tier-into-the-provisioned-compute-tier"></a>Przenoszenie bazy danych z warstwy zasobów obliczeniowych bez serwera do aprowizowanych warstw obliczeniowych

Bez serwera bazy danych można przenieść do aprowizowana warstwa obliczeniowa w taki sam sposób jak przeniesienie aprowizowana baza danych obliczeniowych do warstwy zasobów obliczeniowych bez serwera.

## <a name="modifying-serverless-configuration"></a>Modyfikowanie konfiguracji bez serwera

### <a name="use-powershell"></a>Korzystanie z programu PowerShell

Modyfikowanie maksymalnej lub minimalnej liczby rdzeni wirtualnych oraz opóźnienia automatycznego wsadu jest wykonywane przy użyciu polecenia [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) w programie PowerShell przy użyciu `MaxVcore` argumentów `MinVcore` , i `AutoPauseDelayInMinutes` .

### <a name="use-the-azure-cli"></a>Używanie interfejsu wiersza polecenia platformy Azure

Modyfikowanie maksymalnej lub minimalnej liczby rdzeni wirtualnych i opóźnienia automatycznego wsadu jest wykonywane przy użyciu polecenia [az sql db update](/cli/azure/sql/db#az-sql-db-update) w interfejsie wiersza polecenia platformy Azure przy użyciu `capacity` argumentów , i `min-capacity` `auto-pause-delay` .


## <a name="monitoring"></a>Monitorowanie

### <a name="resources-used-and-billed"></a>Zasoby używane i rozliczane

Zasoby bez serwera bazy danych są hermetyzowane za pomocą pakietu aplikacji, wystąpienia SQL i jednostek puli zasobów użytkownika.

#### <a name="app-package"></a>Pakiet aplikacji

Pakiet aplikacji to zewnętrzna większość granic zarządzania zasobami dla bazy danych, niezależnie od tego, czy baza danych znajduje się w warstwie zasobów obliczeniowych bez użycia serwera, czy aprowizowana. Pakiet aplikacji zawiera wystąpienie SQL i usługi zewnętrzne, które łącznie mają zakres wszystkich zasobów użytkownika i systemu używanych przez bazę danych w SQL Database. Przykłady usług zewnętrznych obejmują wyszukiwanie pełno tekstowe i R. Wystąpienie sql zazwyczaj dominuje w ogólnym wykorzystaniu zasobów w pakiecie aplikacji.

#### <a name="user-resource-pool"></a>Pula zasobów użytkownika

Pula zasobów użytkownika jest wewnętrzną najbardziej wewnętrzną granicą zarządzania zasobami dla bazy danych, niezależnie od tego, czy baza danych znajduje się w warstwie obliczeniowej bez użycia serwera, czy aprowizowana. Pula zasobów użytkownika obejmuje procesor CPU i we/wy dla obciążenia użytkownika generowanego przez zapytania języka DDL, takie jak CREATE i ALTER oraz zapytania DML, takie jak SELECT, INSERT, UPDATE i DELETE. Te zapytania zazwyczaj reprezentują najbardziej znaczący odsetek wykorzystania w pakiecie aplikacji.

### <a name="metrics"></a>Metryki

Metryki monitorowania użycia zasobów pakietu aplikacji i puli użytkowników bez serwera bazy danych zostały wymienione w poniższej tabeli:

|Jednostka|Metric|Opis|Lekcji|
|---|---|---|---|
|Pakiet aplikacji|app_cpu_percent|Procent rdzeni wirtualnych używanych przez aplikację względem maksymalnej dozwolonej dla aplikacji rdzeni wirtualnych.|Procent|
|Pakiet aplikacji|app_cpu_billed|Ilość zasobów obliczeniowych rozliczanych za aplikację w okresie raportowania. Kwota zapłacona w tym okresie to i produkt tej metryki i cena jednostkowa rdzeni wirtualnych. <br><br>Wartości tej metryki są określane przez agregowanie w czasie maksymalnej ilości używanego procesora CPU i pamięci używanej w ciągu sekundy. Jeśli używana kwota jest mniejsza niż minimalna aprowizowana kwota ustawiona przez minimalną liczbę rdzeni wirtualnych i minimalną ilość pamięci, aprowizowana minimalna kwota jest rozliczana.Aby porównać procesor CPU z pamięcią na potrzeby rozliczeń, pamięć jest normalizowana w jednostkach rdzeni wirtualnych przez ponowne skalowanie ilości pamięci w GB o 3 GB na rdzeń wirtualnych.|Sekundy rdzeni wirtualnych|
|Pakiet aplikacji|app_memory_percent|Procent pamięci używanej przez aplikację względem maksymalnej ilości pamięci dozwolonej dla aplikacji.|Procent|
|Pula użytkowników|cpu_percent|Procent rdzeni wirtualnych używanych przez obciążenie użytkownika względem maksymalnej dozwolonej wartości rdzeni wirtualnych dla obciążenia użytkownika.|Procent|
|Pula użytkowników|data_IO_percent|Procent wartości IOPS danych używanych przez obciążenie użytkownika względem maksymalnej dozwolonej wartości we/wy na dane dla obciążenia użytkownika.|Procent|
|Pula użytkowników|log_IO_percent|Procent mb/s dzienników używanych przez obciążenie użytkownika względem maksymalnej dozwolonej ilości MB/s dziennika dla obciążenia użytkownika.|Procent|
|Pula użytkowników|workers_percent|Procent pracowników używanych przez obciążenie użytkownika względem maksymalnej dozwolonej wartości dla obciążenia użytkownika.|Procent|
|Pula użytkowników|sessions_percent|Procent sesji używanych przez obciążenie użytkownika względem maksymalnej dozwolonej sesji dla obciążenia użytkownika.|Procent|

### <a name="pause-and-resume-status"></a>Stan wstrzymywania i wznawiania

W Azure Portal stan bazy danych jest wyświetlany w okienku przeglądu serwera zawierającego listę baz danych, które zawiera. Stan bazy danych jest również wyświetlany w okienku przeglądu bazy danych.

Wykonywanie zapytań o stan wstrzymywania i wznawiania bazy danych za pomocą następujących poleceń:

#### <a name="use-powershell"></a>Korzystanie z programu PowerShell

```powershell
Get-AzSqlDatabase -ResourceGroupName $resourcegroupname -ServerName $servername -DatabaseName $databasename `
  | Select -ExpandProperty "Status"
```

#### <a name="use-the-azure-cli"></a>Używanie interfejsu wiersza polecenia platformy Azure

```azurecli
az sql db show --name $databasename --resource-group $resourcegroupname --server $servername --query 'status' -o json
```


## <a name="resource-limits"></a>Limity zasobów

Aby uzyskać informacje o limitach zasobów, zobacz [bez serwera warstwy obliczeniowej](resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5).

## <a name="billing"></a>Rozliczenia

Ilość rozliczanych zasobów obliczeniowych to maksymalna ilość używanego procesora CPU i pamięci używanej w każdej sekundze. Jeśli ilość używanego procesora CPU i używanej pamięci jest mniejsza niż minimalna ilość aprowizowana dla każdego z nich, aprowizowana kwota jest rozliczana. Aby porównać procesor CPU z pamięcią na potrzeby rozliczeń, pamięć jest normalizowana w jednostkach rdzeni wirtualnych przez ponowne skalowanie ilości pamięci w GB o 3 GB na rdzeń wirtualnych.

- **Zasób rozliczany:** procesor i pamięć
- **Kwota rozliowana:** cena jednostkowa rdzeni wirtualnych * maksymalna (minimalna liczba rdzeni wirtualnych, używane rdzenie wirtualne, minimalna ilość pamięci GB * 1/3, używana pamięć GB * 1/3) 
- **Częstotliwość rozliczeń:** na sekundę

Cena jednostkowa rdzeni wirtualnych to koszt na rdzeń wirtualnych na sekundę. Zapoznaj się ze [stroną Azure SQL Database cenami jednostek](https://azure.microsoft.com/pricing/details/sql-database/single/) dla określonych cen jednostkowych w danym regionie.

Ilość rozliczanych zasobów obliczeniowych jest ujmowana przez następującą metrykę:

- **Metryka:** app_cpu_billed (sekundy rdzeni wirtualnych)
- **Definicja:** maksymalna (minimalna liczba rdzeni wirtualnych, używane rdzenie wirtualne, minimalna pamięć GB * 1/3, używana pamięć GB * 1/3)
- **Częstotliwość raportowania:** na minutę

Ta ilość jest obliczana co sekundę i agregowana przez 1 minutę.

### <a name="minimum-compute-bill"></a>Minimalny rachunek za obliczenia

Jeśli baza danych bez serwera jest wstrzymana, rachunek za obliczenia wynosi zero.  Jeśli baza danych bez serwera nie jest wstrzymana, minimalny rachunek za obliczenia jest nie mniejszy niż liczba rdzeni wirtualnych na podstawie maksymalnej wartości (minimalna liczba rdzeni wirtualnych, minimalna ilość pamięci GB * 1/3).

Przykłady:

- Załóżmy, że bez użycia serwera baza danych nie jest wstrzymana i skonfigurowana przy użyciu 8 maksymalnych rdzeni wirtualnych i 1 min rdzeni wirtualnych odpowiadających pamięci 3,0 GB.  Następnie minimalny rachunek za obliczenia jest oparty na wartości maksymalnej (1 rdzeń wirtualnych, 3,0 GB * 1 rdzeń wirtualnych / 3 GB) = 1 rdzeń wirtualnych.
- Załóżmy, że bez serwera baza danych nie jest wstrzymana i skonfigurowana przy użyciu 4 maksymalnych rdzeni wirtualnych i 0,5 min rdzeni wirtualnych odpowiadających pamięci 2,1 GB.  Następnie minimalny rachunek za obliczenia jest oparty na maksymalnej wartości (0,5 rdzeni wirtualnych, 2,1 GB * 1 rdzeń wirtualnych / 3 GB) = 0,7 rdzeni wirtualnych.

Kalkulator [Azure SQL Database](https://azure.microsoft.com/pricing/calculator/?service=sql-database) cen dla bez serwera może służyć do określania minimalnej konfigurowalnej pamięci na podstawie maksymalnej i minimalnej liczby skonfigurowanych rdzeni wirtualnych.  Zgodnie z regułą, jeśli minimalna liczba skonfigurowanych rdzeni wirtualnych jest większa niż 0,5 rdzeni wirtualnych, minimalny rachunek za obliczenia jest niezależny od minimalnej skonfigurowanej pamięci i zależy tylko od liczby minimalnych skonfigurowanych rdzeni wirtualnych.

### <a name="example-scenario"></a>Przykładowy scenariusz

Rozważmy bez serverless database skonfigurowaną z 1-minutowym rdzeniem wirtualnych i 4 maksymalnymi rdzeniami wirtualnych.  Odpowiada to około 3 GB pamięci minimalnej i maksymalnej pamięci 12 GB.  Załóżmy, że opóźnienie automatycznego wstrzymania jest ustawione na 6 godzin, a obciążenie bazy danych jest aktywne w ciągu pierwszych 2 godzin okresu 24-godzinnego i w inny sposób nieaktywne.    

W takim przypadku w ciągu pierwszych 8 godzin baza danych jest rozliczana za zasoby obliczeniowe i magazynowe.  Mimo że baza danych jest nieaktywna, począwszy od drugiej godziny, nadal jest rozliczana za zasoby obliczeniowe w kolejnych 6 godzinach na podstawie minimalnej aprowizowanych zasobów obliczeniowych, gdy baza danych jest w trybie online.  Tylko magazyn jest rozliczany w pozostałej części 24-godzinnego okresu, gdy baza danych jest wstrzymana.

Dokładniej mówiąc, rachunek za obliczenia w tym przykładzie jest obliczany w następujący sposób:

|Interwał czasu|Rdzenie wirtualne używały każdej sekundy|Gb używane w każdej sekundze|Wymiar obliczeniowy rozliczany|Sekundy rdzeni wirtualnych rozliczane w przedziale czasu|
|---|---|---|---|---|
|0:00-1:00|4|9|Używane rdzenie wirtualne|4 rdzenie wirtualne * 3600 sekund = 14400 rdzeni wirtualnych|
|1:00-2:00|1|12|Użycie pamięci|12 GB * 1/3 * 3600 sekund = 14400 rdzeni wirtualnych|
|2:00-8:00|0|0|Aprowizowana minimalna ilość pamięci|3 GB * 1/3 * 21600 sekund = 21600 rdzeni wirtualnych|
|8:00-24:00|0|0|Nie są naliczane żadne obliczenia po wstrzymaniu|0 rdzeni wirtualnych w sekundach|
|Łączna liczba sekund rdzeni wirtualnych rozliczanych w ciągu 24 godzin||||50400 rdzeni wirtualnych w sekundach|

Załóżmy, że cena jednostki obliczeniowej wynosi 0,000145 USD/rdzeń wirtualnych/sekundę.  Następnie obliczenia rozliczane za ten okres 24-godzinny są imieńcem ceny jednostki obliczeniowej i sekund rdzeni wirtualnych rozliczanych: 0,000145 USD/rdzeń wirtualnych/sekundę * 50400 sekund rdzeni wirtualnych ~ 7,31 USD

### <a name="azure-hybrid-benefit-and-reserved-capacity"></a>Korzyść użycia hybrydowego platformy Azure i pojemność zarezerwowana

Korzyść użycia hybrydowego platformy Azure (AHB) i rabaty na pojemność zarezerwowaną nie mają zastosowania do warstwy zasobów obliczeniowych bez serwera.

## <a name="available-regions"></a>Dostępne regiony

Warstwa obliczeniowa bez serwera jest dostępna na całym świecie z wyjątkiem następujących regionów: Chiny Wschodnie, Chiny Północne, Niemcy Środkowe, Niemcy Północno-Wschodnie i US Gov Środkowe (Iowa).

## <a name="next-steps"></a>Następne kroki

- Aby rozpocząć pracę, zobacz [Szybki start: tworzenie pojedynczej bazy](single-database-create-quickstart.md)danych w Azure SQL Database użyciu Azure Portal .
- Aby uzyskać informacje o limitach zasobów, [zobacz Serverless compute tier resource limits (Limity zasobów warstwy obliczeniowej bez serwera).](resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5)
