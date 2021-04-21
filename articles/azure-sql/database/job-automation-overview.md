---
title: Omówienie automatyzacji zadań za pomocą zadań elastycznych
description: Używanie zadań elastycznych do automatyzacji zadań w celu uruchamiania skryptów języka Transact-SQL (T-SQL) w zestawie co najmniej jednej bazy danych
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: sqldbrb=1, contperf-fy21q3
ms.devlang: ''
dev_langs:
- TSQL
ms.topic: conceptual
author: williamdassafMSFT
ms.author: wiassaf
ms.reviewer: ''
ms.date: 2/1/2021
ms.openlocfilehash: 295889cf64d27761021dd09549a3366ea142516e
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752034"
---
# <a name="automate-management-tasks-using-elastic-jobs-preview"></a>Automatyzowanie zadań zarządzania przy użyciu zadań elastycznych (wersja zapoznawcza)

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Można tworzyć i planować zadania elastyczne, które mogą być okresowo wykonywane względem jednej lub wielu baz danych Azure SQL w celu uruchamiania zapytań języka Transact-SQL (T-SQL) i wykonywania zadań konserwacji. 

Można określić docelową bazę danych lub grupy baz danych, w których zadania będą wykonywane, oraz zdefiniować harmonogramy uruchamiania zadania.
Zadanie obsługuje rejestrowanie w docelowej bazie danych. Możesz również definiować, obsługiwać i utrwalać skrypty języka Transact-SQL do wykonania w ramach grupy baz danych.

Każde zadanie rejestruje stan wykonania, a także automatycznie ponawia próbę wykonania operacji w przypadku awarii.

## <a name="when-to-use-elastic-jobs"></a>Kiedy używać zadań elastycznych

Istnieje kilka scenariuszy, w których można użyć automatyzacji zadań elastycznych:

- Automatyzuj zadania zarządzania i planuj ich uruchamianie codziennie, po godzinach pracy itp.
  - Wdrażanie zmian schematu, zarządzanie poświadczeniami i zbieranie danych o wydajności lub danych telemetrycznych dotyczących dzierżaw (klientów).
  - Aktualizowanie danych referencyjnych (informacje wspólne dla wszystkich baz danych) i ładowanie danych z usługi Azure Blob Storage.
- Konfigurowanie zadań w celu cyklicznego wykonywania w kolekcji baz danych, na przykład poza godzinami szczytu.
  - Zbieranie na bieżąco wyników zapytań z zestawu baz danych w centralnej tabeli. Zapytania dotyczące wydajności mogą być nieprzerwanie wykonywane i skonfigurowane do wyzwalania dodatkowych zadań do wykonania.
- Zbieranie danych na potrzeby raportowania
  - Agregowanie danych z kolekcji baz danych do pojedynczej tabeli docelowej.
  - Wykonywanie dłużej działających zapytań dotyczących przetwarzania danych w ramach dużego zestawu baz danych, na przykład w celu zbierania telemetrii klienta. Wyniki są zbierane w pojedynczej tabeli docelowej na potrzeby dalszej analizy.
- Operacje przenoszenia danych 

### <a name="automation-on-other-platforms"></a>Automatyzacja na innych platformach

Rozważ następujące technologie planowania zadań na różnych platformach:

- **Zadania elastyczne** to usługi planowania zadań, które wykonują zadania niestandardowe w jednej lub wielu bazach danych w Azure SQL Database.
- **Zadania agenta SQL są** wykonywane przez usługę SQL Agent, która w dalszym ciągu jest używana do automatyzacji zadań w programie SQL Server i jest również dołączona do Azure SQL wystąpieniach zarządzanych. Zadania agenta SQL nie są dostępne w Azure SQL Database.

Zadania elastyczne mogą być Azure SQL [bazami](sql-database-paas-overview.md)danych, [Azure SQL Database elastycznymi](elastic-pool-overview.md)pulami i Azure SQL bazami danych w [mapach fragmentów.](elastic-scale-shard-map-management.md)

W przypadku automatyzacji zadań skryptu T-SQL w SQL Server i Azure SQL Managed Instance należy rozważyć [usługę SQL Agent.](job-automation-managed-instances.md) 

W przypadku automatyzacji zadań skryptu języka T-SQL w Azure Synapse Analytics należy wziąć pod uwagę potoki z wyzwalaczami cyklicznych [opartymi](../../synapse-analytics/data-integration/concepts-data-factory-differences.md)na Azure Data Factory [.](../../synapse-analytics/data-integration/concepts-data-factory-differences.md)

Warto zauważyć różnice między agentem SQL (dostępnym w usługach SQL Server i w ramach programu SQL Managed Instance) i agentem zadań elastycznych bazy danych (który może wykonywać język T-SQL w bazach danych programu Azure SQL lub bazach danych w usługach SQL Server i Azure SQL Managed Instance, Azure Synapse Analytics).

| |Zadania elastyczne |Agent SQL |
|---------|---------|---------|
|**Zakres** | Dowolna liczba baz danych w Azure SQL Database i/lub magazynach danych w tej samej chmurze platformy Azure co agent zadań. Obiekty docelowe mogą być w różnych serwerach, subskrypcjach i/lub regionach. <br><br>Grupy docelowe mogą składać się z pojedynczych baz danych lub magazynów danych albo wszystkich baz danych na serwerze, w puli lub w mapie fragmentów (dynamicznie wyliczanych w czasie wykonywania zadania). | Dowolna pojedyncza baza danych w tym samym wystąpieniu co agent SQL. Funkcja Administracja wieloma serwerami agenta SQL Server umożliwia koordynowanie wykonywania zadań przez wystąpienia główne/docelowe, chociaż ta funkcja nie jest dostępna w wystąpieniu zarządzanym SQL. |
|**Obsługiwane interfejsy API i narzędzia** | Witryna Azure Portal, program PowerShell, język T-SQL, usługa Azure Resource Manager | Język T-SQL, program SQL Server Management Studio (SSMS) |
 
## <a name="elastic-job-targets"></a>Obiekty docelowe zadań elastycznych

**Zadania elastyczne** zapewniają możliwość równoległego uruchamiania co najmniej jednego skryptu T-SQL w dużej liczbie baz danych, zgodnie z harmonogramem lub na żądanie.

Zaplanowane zadania można uruchamiać dla dowolnej kombinacji baz danych: co najmniej jednej bazy danych, wszystkich baz danych na serwerze, wszystkich baz danych w elastycznej puli lub mapy fragmentów, dzięki większej elastyczności dołączania lub wykluczania dowolnej określonej bazy danych. Zadania można wykonywać na wielu serwerach, w wielu pulach, a nawet w obrębie baz danych w ramach różnych subskrypcji. Serwery i pule są dynamicznie wyliczane w czasie wykonywania, więc zadania są uruchamiane względem wszystkich baz danych, które istnieją w docelowej grupie w czasie wykonywania.

Na poniższej ilustracji przedstawiono agenta zadań wykonującego zadania w ramach różnych typów grup docelowych:

![Model koncepcyjny agenta zadań elastycznych](./media/job-automation-overview/conceptual-diagram.png)

### <a name="elastic-job-components"></a>Składniki zadań elastycznych

|Składnik | Opis (dodatkowe szczegóły znajdują się pod tabelą) |
|---------|---------|
|[**Agent zadań elastycznych**](#elastic-job-agent) | Zasób platformy Azure tworzony w celu uruchamiania zadań i zarządzania nimi. |
|[**Baza danych zadań**](#elastic-job-database) | Baza danych w Azure SQL Database używana przez agenta zadań do przechowywania danych związanych z zadaniami, definicji zadań itp. |
|[**Grupa docelowa**](#target-group) | Zestaw serwerów, pul, baz danych i map fragmentów, względem których ma być uruchamiane zadanie. |
|[**Zadanie**](#elastic-jobs-and-job-steps) | Zadanie to jednostka pracy, która składa się z co najmniej jednego kroku zadania. Kroki zadania określają skrypty T-SQL do uruchomienia, a także inne szczegóły wymagane do wykonania skryptu. |

#### <a name="elastic-job-agent"></a>Agent zadań elastycznych

Agent zadań elastycznych jest zasobem platformy Azure służącym do tworzenia i uruchamiania zadań oraz do zarządzania nimi. Agent zadań elastycznych jest zasobem platformy Azure tworzonym w witrynie Azure Portal (obsługiwany jest również program [PowerShell](elastic-jobs-powershell-create.md) i interfejs REST).

Tworzenie agenta **zadań elastycznych wymaga** istniejącej bazy danych w Azure SQL Database. Agent konfiguruje ten istniejący Azure SQL Database jako bazę [*danych zadań.*](#elastic-job-database)

Agent zadań elastycznych jest bezpłatny. Baza danych zadań jest rozliczana za tę samą stawkę co każda baza danych w Azure SQL Database.

#### <a name="elastic-job-database"></a>Baza danych zadań elastycznych

*Baza danych zadań* służy do definiowania zadań oraz śledzenia stanu i historii wykonywania zadań. Baza *danych zadań* jest również używana do przechowywania metadanych agenta, dzienników, wyników i definicji zadań, a także zawiera wiele przydatnych procedur składowanych i innych obiektów bazy danych do tworzenia i uruchamiania zadań oraz zarządzania nimi przy użyciu języka T-SQL.

W bieżącej wersji zapoznawczej do utworzenia agenta zadań elastycznych jest wymagana istniejąca baza danych w programie Azure SQL Database (S0 lub wyższa wersja).

Baza *danych zadań powinna* być czystą, pustą wartością celu usługi S0 lub wyższą Azure SQL Database. Zalecany cel usługi  bazy danych zadań to S1 lub wyższa, ale optymalny wybór zależy od potrzeb w zakresie wydajności zadań: liczby kroków zadania, liczby celów zadań i częstotliwości uruchamiania zadań. 

Jeśli operacje względem bazy danych zadań [](monitor-tune-overview.md#azure-sql-database-and-azure-sql-managed-instance-resource-monitoring) są wolniejsze niż oczekiwano, monitoruj wydajność bazy danych i wykorzystanie zasobów w bazie danych zadań w okresach powolnego działania przy użyciu Azure Portal lub sys.dm_db_resource_stats [DMV.](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) Jeśli wykorzystanie zasobu, takiego jak procesor CPU, we/wy danych lub zapis dziennika zbliża się do 100% i jest skorelowane z okresami powolnego działania, rozważ przyrostowe skalowanie bazy danych do wyższych celów usługi (w modelu [DTU](service-tiers-dtu.md) lub modelu rdzeni [wirtualnych),](service-tiers-vcore.md)dopóki wydajność bazy danych zadań nie zostanie wystarczająco zwiększona.

##### <a name="elastic-job-database-permissions"></a>Uprawnienia bazy danych zadań elastycznych

Podczas tworzenia agenta zadań ma miejsce tworzenie schematu, tabel i roli o nazwie *jobs_reader* w *bazie danych zadań*. Ta rola jest tworzona z następującymi uprawnieniami i została zaprojektowana w celu umożliwienia administratorom bardziej precyzyjnej kontroli dostępu na potrzeby monitorowania zadań:

|Nazwa roli |Uprawnienia do schematu „jobs” |Uprawnienia do schematu „jobs_internal” |
|---------|---------|---------|
|**jobs_reader** | SELECT | Brak |

> [!IMPORTANT]
> Przed udzieleniem dostępu do *bazy danych zadań* na poziomie administratora bazy danych rozważ konsekwencje takiego działania w zakresie zabezpieczeń. Złośliwy użytkownik z uprawnieniami do tworzenia lub edytowania zadań może utworzyć lub edytować zadanie, które używa przechowywanych poświadczeń do nawiązania połączenia z bazą danych pod kontrolą złośliwego użytkownika, co może umożliwić złośliwemu użytkownikowi określenie hasła poświadczenia.

#### <a name="target-group"></a>Grupa docelowa

*Grupa docelowa* definiuje zestaw baz danych, względem którego będzie wykonywany krok zadania. Grupa docelowa może zawierać dowolną liczbę i kombinację następujących elementów:

- **Serwer logiczny SQL** — jeśli serwer jest określony, wszystkie bazy danych, które istnieją na serwerze w czasie wykonywania zadania, są częścią grupy. Konieczne jest podanie poświadczeń bazy danych master, aby możliwe było wyliczenie i zaktualizowanie grupy przed wykonaniem zadania. Aby uzyskać więcej informacji na temat serwerów logicznych, zobacz Co to jest serwer w Azure SQL Database [i Azure Synapse Analytics?](logical-servers.md).
- **Elastyczna pula** — jeśli elastyczna pula jest określona, wszystkie bazy danych istniejące w niej w czasie wykonywania zadania są częścią grupy. Podobnie jak w przypadku serwera, konieczne jest podanie poświadczeń bazy danych master, aby możliwe było wyliczenie i zaktualizowanie grupy przed wykonaniem zadania.
- **Pojedyncza baza danych** — określ co najmniej jedną pojedynczą bazę danych, która będzie należeć do grupy.
- **Mapa fragmentów** — bazy danych mapy fragmentów.

> [!TIP]
> W momencie wykonywania zadania za pomocą *dynamicznego wyliczania* ma miejsce ponowne obliczenie zestawu baz danych w grupach docelowych, które zawierają serwery lub pule. Pozwala to zapewnić, że **zadania są uruchamiane we wszystkich bazach danych, które istnieją na serwerze lub w puli w czasie wykonywania zadania**. Ponowne obliczanie listy baz danych w czasie wykonywania jest szczególnie przydatne w scenariuszach, w których często zmienia się członkostwo w puli lub na serwerze.

Pule i pojedyncze bazy danych można określić jako uwzględnione lub wykluczone z grupy. Umożliwia to tworzenie grupy docelowej z dowolną kombinacją baz danych. Na przykład możesz dodać serwer do grupy docelowej, ale wykluczyć określone bazy danych znajdujące się w elastycznej puli (lub wykluczyć całą pulę).

Grupa docelowa może zawierać bazy danych znajdujące się w wielu subskrypcjach i regionach. Należy pamiętać, że operacje wykonywane między regionami mają większe opóźnienia niż te wykonywane w tym samym regionie.

W poniższych przykładach pokazano, jak różne definicje grup docelowych są dynamicznie wyliczane w momencie wykonania zadania w celu określenia baz danych do uruchomienia w ramach zadania:

![Przykłady grup docelowych](./media/job-automation-overview/targetgroup-examples1.png)

**Przykład 1** przedstawia grupę docelową, która zawiera listę poszczególnych baz danych. Gdy krok zadania jest wykonywany przy użyciu tej grupy docelowej, akcja kroku zadania zostanie wykonana w każdej z tych baz danych.<br>
**Przykład 2** przedstawia grupę docelową, która zawiera serwer jako obiekt docelowy. Gdy krok zadania jest wykonywany przy użyciu tej grupy docelowej, serwer jest dynamicznie wyliczany w celu określenia listy baz danych, które obecnie znajdują się na serwerze. Akcja kroku zadania zostanie wykonana w każdej z tych baz danych.<br>
**Przykład 3** zawiera grupę docelową podobną do *przykładu 2* z wykluczeniem pojedynczej bazy danych. Akcja kroku zadania *nie* zostanie wykonana w wykluczonej bazie danych.<br>
**Przykład 4** przedstawia grupę docelową, która zawiera elastyczną pulę jako obiekt docelowy. Podobnie jak w *przykładzie 2*, pula będzie dynamicznie wyliczana w momencie uruchomienia zadania w celu określenia listy baz danych w puli.
<br><br>

![Dodatkowe przykłady grup docelowych](./media/job-automation-overview/targetgroup-examples2.png)

**Przykład 5** i **przykład 6** pokazują zaawansowane scenariusze, w których serwery, elastyczne pule i bazy danych można łączyć przy użyciu reguł dołączania i wykluczania.<br>
**Przykład 7** pokazuje, że w trakcie uruchamiania zadania można również wyliczać fragmenty na mapie fragmentów.

> [!NOTE]
> Baza danych zadań może być obiektem docelowym zadania. W tym scenariuszu baza danych zadań jest traktowana tak jak każda inna docelowa baza danych. Użytkownik zadania musi zostać utworzony i ma przyznane wystarczające uprawnienia w bazie danych zadań, a poświadczenia w zakresie bazy danych dla użytkownika zadania muszą również istnieć w bazie danych zadań, podobnie jak w przypadku każdej innej docelowej bazy danych.

#### <a name="elastic-jobs-and-job-steps"></a>Zadania elastyczne i kroki zadania

*Zadanie* to jednostka pracy, która jest wykonywana na podstawie harmonogramu lub jako zadanie jednorazowe. Zadanie składa się z co najmniej jednego *kroku zadania*.

Każdy krok zadania określa skrypt T-SQL do wykonania, co najmniej jedną grupę docelową, względem której ten skrypt będzie uruchamiany, oraz poświadczenia wymagane dla agenta zadań do nawiązania połączenia z docelową bazą danych. Każdy krok zadania posiada możliwy do dostosowania limit czasu i zasady ponawiania oraz pozwala na opcjonalne określenie parametrów wyjściowych.

#### <a name="job-output"></a>Dane wyjściowe zadania

Wyniki wykonywania kroków zadania w każdej docelowej bazie danych są szczegółowo rejestrowane, a dane wyjściowe skryptu mogą zostać przechwycone w określonej tabeli. Możesz określić bazę danych, aby zapisać wszystkie dane zwrócone z zadania.

#### <a name="job-history"></a>Historia zadania

Wyświetl historię wykonywania zadań elastycznych w bazie *danych zadań,* [odpytując tabelę jobs.job_executions](elastic-jobs-tsql-create-manage.md#monitor-job-execution-status). Zadanie oczyszczania systemu czyści historię wykonywania, która jest starsza niż 45 dni. Aby usunąć historię poniżej 45 dni, wywołaj procedurę **sp_purge_jobhistory** w bazie *danych zadań*.

#### <a name="job-status"></a>Stan zadania

Wykonywanie zadań elastycznych w  bazie danych zadań można monitorować, [odpytując tabelę jobs.job_executions](elastic-jobs-tsql-create-manage.md#monitor-job-execution-status). 

### <a name="agent-performance-capacity-and-limitations"></a>Wydajność agenta, pojemność i ograniczenia

Zadania elastyczne używają minimalnych zasobów obliczeniowych podczas oczekiwania na zakończenie długotrwałych zadań.

W zależności od rozmiaru grupy docelowej baz danych i żądanego czasu wykonywania zadania (liczba równoczesnych procesów roboczych) agent wymaga różnej ilości zasobów obliczeniowych i wydajności *bazy danych zadań* (im więcej elementów docelowych i większa liczba zadań, tym wymagana jest większa ilość zasobów obliczeniowych).

Obecnie limit wynosi 100 współbieżnych zadań.

#### <a name="prevent-jobs-from-reducing-target-database-performance"></a>Zapobieganie zmniejszania wydajności docelowej bazy danych przez zadania

Aby zapewnić, że zasoby nie będą przeciążone podczas uruchamiania zadań w ramach baz danych w elastycznej puli SQL, możliwe jest skonfigurowanie zadań w taki sposób, aby ograniczana była liczba baz danych, w ramach których mogą one być jednocześnie uruchamiane.

## <a name="next-steps"></a>Następne kroki

- [How to create and manage elastic jobs (Jak tworzyć zadania elastyczne i zarządzać nimi)](elastic-jobs-overview.md)
- [Tworzenie zadań elastycznych i zarządzanie nimi przy użyciu programu PowerShell](elastic-jobs-powershell-create.md)
- [Tworzenie zadań elastycznych i zarządzanie nimi za pomocą języka Transact-SQL (T-SQL)](elastic-jobs-tsql-create-manage.md)
