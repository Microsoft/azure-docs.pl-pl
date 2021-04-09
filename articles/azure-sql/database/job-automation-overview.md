---
title: Omówienie automatyzacji zadań z zadaniami elastycznymi
description: Korzystaj z elastycznych zadań do automatyzacji zadań, aby uruchamiać skrypty Transact-SQL (T-SQL) w zestawie co najmniej jednej bazy danych
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
ms.openlocfilehash: 1f4bd28d2b95aeebe07fcad84d757327622d51f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101690434"
---
# <a name="automate-management-tasks-using-elastic-jobs-preview"></a>Automatyzowanie zadań zarządzania przy użyciu zadań elastycznych (wersja zapoznawcza)

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Można tworzyć i planować elastyczne zadania, które mogą być okresowo wykonywane dla jednej lub wielu baz danych SQL Azure w celu uruchamiania zapytań Transact-SQL (T-SQL) i wykonywania zadań konserwacyjnych. 

Można określić docelową bazę danych lub grupy baz danych, w których zadania będą wykonywane, oraz zdefiniować harmonogramy uruchamiania zadania.
Zadanie obsługuje rejestrowanie w docelowej bazie danych. Możesz również definiować, obsługiwać i utrwalać skrypty języka Transact-SQL do wykonania w ramach grupy baz danych.

Każde zadanie rejestruje stan wykonania, a także automatycznie ponawia próbę wykonania operacji w przypadku awarii.

## <a name="when-to-use-elastic-jobs"></a>Kiedy korzystać z zadań elastycznych

Istnieje kilka scenariuszy, w których można użyć automatyzacji zadań elastycznych:

- Automatyzowanie zadań zarządzania i planowanie ich uruchamiania każdego dnia tygodnia, po godzinach itd.
  - Wdrażanie zmian schematu, zarządzanie poświadczeniami i zbieranie danych o wydajności lub danych telemetrycznych dotyczących dzierżaw (klientów).
  - Aktualizowanie danych referencyjnych (informacje wspólne dla wszystkich baz danych) i ładowanie danych z usługi Azure Blob Storage.
- Konfigurowanie zadań w celu cyklicznego wykonywania w kolekcji baz danych, na przykład poza godzinami szczytu.
  - Zbieranie na bieżąco wyników zapytań z zestawu baz danych w centralnej tabeli. Zapytania dotyczące wydajności mogą być nieprzerwanie wykonywane i skonfigurowane do wyzwalania dodatkowych zadań do wykonania.
- Zbieranie danych na potrzeby raportowania
  - Agregowanie danych z kolekcji baz danych do pojedynczej tabeli docelowej.
  - Wykonywanie dłużej działających zapytań dotyczących przetwarzania danych w ramach dużego zestawu baz danych, na przykład w celu zbierania telemetrii klienta. Wyniki są zbierane w pojedynczej tabeli docelowej na potrzeby dalszej analizy.
- Operacje przenoszenia danych 

### <a name="automation-on-other-platforms"></a>Automatyzacja na innych platformach

Należy wziąć pod uwagę następujące technologie planowania zadań na różnych platformach:

- **Zadania elastyczne** to usługi planowania zadań, które wykonują zadania niestandardowe dla jednej lub wielu baz danych w Azure SQL Database.
- **Zadania programu SQL Agent** są wykonywane przez usługę SQL Agent, która jest nadal używana do automatyzacji zadań w SQL Server i jest również uwzględniona w wystąpieniach zarządzanych usługi Azure SQL. Zadania programu SQL Agent nie są dostępne w Azure SQL Database.

Zadania elastyczne mogą kierować do [baz danych SQL Azure](sql-database-paas-overview.md), [Azure SQL Database pul elastycznych](elastic-pool-overview.md)i baz danych Azure SQL w [fragmentu Maps](elastic-scale-shard-map-management.md).

W przypadku automatyzacji zadań skryptów T-SQL w SQL Server i wystąpieniu zarządzanym usługi Azure SQL należy wziąć pod uwagę [program SQL Agent](job-automation-managed-instances.md). 

W przypadku automatyzacji zadań skryptów T-SQL w usłudze Azure Synapse Analytics należy wziąć pod uwagę [potoki z wyzwalaczami cyklicznymi](../../synapse-analytics/data-integration/concepts-data-factory-differences.md), które są [oparte na Azure Data Factory](../../synapse-analytics/data-integration/concepts-data-factory-differences.md).

Warto zauważyć, że różnice między programem SQL Agent (dostępnym w SQL Server i w ramach wystąpienia zarządzanego SQL) oraz agentem elastycznego zadania bazy danych (który może wykonywać T-SQL w bazach danych Azure SQL lub SQL Server bazach danych usługi Azure Synapse Analytics).

| |Zadania elastyczne |Agent SQL |
|---------|---------|---------|
|**Zakres** | Dowolna liczba baz danych w Azure SQL Database i/lub magazynów danych w tej samej chmurze platformy Azure jako Agent zadania. Elementy docelowe mogą znajdować się na różnych serwerach, subskrypcjach i/lub regionach. <br><br>Grupy docelowe mogą składać się z pojedynczych baz danych lub magazynów danych albo wszystkich baz danych w serwerze, puli lub mapie fragmentu (dynamicznie wyliczane w czasie wykonywania zadania). | Każda pojedyncza baza danych w tym samym wystąpieniu co program SQL Agent. Funkcja administrowania wieloma serwerami agenta SQL Server zezwala na wystąpienia główne/docelowe w celu koordynowania wykonywania zadań, chociaż ta funkcja nie jest dostępna w wystąpieniu zarządzanym SQL. |
|**Obsługiwane interfejsy API i narzędzia** | Witryna Azure Portal, program PowerShell, język T-SQL, usługa Azure Resource Manager | Język T-SQL, program SQL Server Management Studio (SSMS) |
 
## <a name="elastic-job-targets"></a>Elastyczne cele zadań

**Zadania elastyczne** umożliwiają równoległe uruchamianie co najmniej jednego skryptu T-SQL w wielu bazach danych zgodnie z harmonogramem lub na żądanie.

Zaplanowane zadania można uruchamiać dla dowolnej kombinacji baz danych: co najmniej jedna z pojedynczych baz danych, wszystkich baz danych na serwerze, wszystkich baz danych w puli elastycznej lub fragmentu, z dodatkową elastycznością dołączania lub wykluczania każdej konkretnej bazy danych. Zadania można wykonywać na wielu serwerach, w wielu pulach, a nawet w obrębie baz danych w ramach różnych subskrypcji. Serwery i pule są dynamicznie wyliczane w czasie wykonywania, więc zadania są uruchamiane względem wszystkich baz danych, które istnieją w docelowej grupie w czasie wykonywania.

Na poniższej ilustracji przedstawiono agenta zadań wykonującego zadania w ramach różnych typów grup docelowych:

![Model koncepcyjny agenta zadań elastycznych](./media/job-automation-overview/conceptual-diagram.png)

### <a name="elastic-job-components"></a>Elastyczne zadania

|Składnik | Opis (dodatkowe szczegóły znajdują się pod tabelą) |
|---------|---------|
|[**Agent zadań elastycznych**](#elastic-job-agent) | Zasób platformy Azure tworzony w celu uruchamiania zadań i zarządzania nimi. |
|[**Baza danych zadań**](#elastic-job-database) | Baza danych w Azure SQL Database, której Agent zadań używa do przechowywania danych związanych z zadaniami, definicji zadań itp. |
|[**Grupa docelowa**](#target-group) | Zestaw serwerów, pul, baz danych i map fragmentów, względem których ma być uruchamiane zadanie. |
|[**Zadanie**](#elastic-jobs-and-job-steps) | Zadanie to jednostka pracy, która składa się z co najmniej jednego kroku zadania. Kroki zadania określają skrypty T-SQL do uruchomienia, a także inne szczegóły wymagane do wykonania skryptu. |

#### <a name="elastic-job-agent"></a>Agent zadań elastycznych

Agent zadań elastycznych jest zasobem platformy Azure służącym do tworzenia i uruchamiania zadań oraz do zarządzania nimi. Agent zadań elastycznych jest zasobem platformy Azure tworzonym w witrynie Azure Portal (obsługiwany jest również program [PowerShell](elastic-jobs-powershell-create.md) i interfejs REST).

Utworzenie **agenta elastycznego zadania** wymaga istniejącej bazy danych w Azure SQL Database. Agent skonfiguruje ten istniejący Azure SQL Database jako [*bazę danych zadań*](#elastic-job-database).

Agent zadań elastycznych jest bezpłatny. Ta baza danych zadań jest rozliczana według stawki co każda baza danych w Azure SQL Database.

#### <a name="elastic-job-database"></a>Elastyczna baza danych zadań

*Baza danych zadań* służy do definiowania zadań oraz śledzenia stanu i historii wykonywania zadań. *Baza danych zadań* służy również do przechowywania metadanych agentów, dzienników, wyników, definicji zadań, a także zawiera wiele przydatnych procedur składowanych i innych obiektów baz danych służących do tworzenia i uruchamiania zadań oraz zarządzania nimi przy użyciu języka T-SQL.

W przypadku bieżącej wersji zapoznawczej istniejąca baza danych w Azure SQL Database (S0 lub nowsza) jest wymagana do utworzenia agenta zadań elastycznych.

*Baza danych zadań* powinna być Azure SQL Databasem czystym, pustym, S0 lub wyższym. Zalecany cel usługi *bazy danych zadań* ma wartość S1 lub wyższą, ale optymalny wybór zależy od potrzeb związanych z wydajnością zadań: liczby kroków zadań, liczby elementów docelowych zadania i częstotliwości uruchamiania zadań. 

Jeśli operacje dotyczące bazy danych zadań są wolniejsze niż oczekiwane, [Monitoruj](monitor-tune-overview.md#azure-sql-database-and-azure-sql-managed-instance-resource-monitoring) wydajność bazy danych i wykorzystanie zasobów w bazie danych zadań w okresach spowolnienia przy użyciu Azure Portal lub [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) DMV. Jeśli użycie zasobu, takiego jak procesor CPU, we/wy danych lub zapis w dzienniku, zbliża się do 100% i jest skorelowane z okresami spowolnienia, należy rozważyć przyrostowe skalowanie bazy danych do wyższych celów usługi (w [modelu DTU](service-tiers-dtu.md) lub w [modelu rdzeń wirtualny](service-tiers-vcore.md)), dopóki wydajność bazy danych zadań zostanie dostatecznie zwiększona.

##### <a name="elastic-job-database-permissions"></a>Uprawnienia do Elastic Database zadań

Podczas tworzenia agenta zadań ma miejsce tworzenie schematu, tabel i roli o nazwie *jobs_reader* w *bazie danych zadań*. Ta rola jest tworzona z następującymi uprawnieniami i została zaprojektowana w celu umożliwienia administratorom bardziej precyzyjnej kontroli dostępu na potrzeby monitorowania zadań:

|Nazwa roli |Uprawnienia do schematu „jobs” |Uprawnienia do schematu „jobs_internal” |
|---------|---------|---------|
|**jobs_reader** | SELECT | Brak |

> [!IMPORTANT]
> Przed udzieleniem dostępu do *bazy danych zadań* na poziomie administratora bazy danych rozważ konsekwencje takiego działania w zakresie zabezpieczeń. Złośliwy użytkownik z uprawnieniami do tworzenia lub edytowania zadań może utworzyć lub edytować zadanie używające zapisanego poświadczenia do łączenia się z bazą danych w ramach kontroli złośliwego użytkownika, co może umożliwić złośliwemu użytkownikowi ustalenie hasła poświadczenia.

#### <a name="target-group"></a>Grupa docelowa

*Grupa docelowa* definiuje zestaw baz danych, względem którego będzie wykonywany krok zadania. Grupa docelowa może zawierać dowolną liczbę i kombinację następujących elementów:

- **Logiczny serwer SQL** — w przypadku określenia serwera wszystkie bazy danych istniejące na serwerze w momencie wykonywania zadania są częścią grupy. Konieczne jest podanie poświadczeń bazy danych master, aby możliwe było wyliczenie i zaktualizowanie grupy przed wykonaniem zadania. Aby uzyskać więcej informacji na temat serwerów logicznych, zobacz [co to jest serwer w Azure SQL Database i usługa Azure Synapse Analytics?](logical-servers.md).
- **Elastyczna pula** — jeśli elastyczna pula jest określona, wszystkie bazy danych istniejące w niej w czasie wykonywania zadania są częścią grupy. Podobnie jak w przypadku serwera, konieczne jest podanie poświadczeń bazy danych master, aby możliwe było wyliczenie i zaktualizowanie grupy przed wykonaniem zadania.
- **Pojedyncza baza danych** — określ co najmniej jedną pojedynczą bazę danych, która będzie należeć do grupy.
- **Mapa fragmentu** — bazy danych mapy fragmentu.

> [!TIP]
> W momencie wykonywania zadania za pomocą *dynamicznego wyliczania* ma miejsce ponowne obliczenie zestawu baz danych w grupach docelowych, które zawierają serwery lub pule. Pozwala to zapewnić, że **zadania są uruchamiane we wszystkich bazach danych, które istnieją na serwerze lub w puli w czasie wykonywania zadania**. Ponowne obliczanie listy baz danych w czasie wykonywania jest szczególnie przydatne w scenariuszach, w których często zmienia się członkostwo w puli lub na serwerze.

Pule i pojedyncze bazy danych można określić jako uwzględnione lub wykluczone z grupy. Umożliwia to tworzenie grupy docelowej z dowolną kombinacją baz danych. Na przykład możesz dodać serwer do grupy docelowej, ale wykluczyć określone bazy danych znajdujące się w elastycznej puli (lub wykluczyć całą pulę).

Grupa docelowa może zawierać bazy danych znajdujące się w wielu subskrypcjach i regionach. Należy pamiętać, że operacje wykonywane między regionami mają większe opóźnienia niż te wykonywane w tym samym regionie.

W poniższych przykładach pokazano, jak różne definicje grup docelowych są dynamicznie wyliczane w momencie wykonania zadania w celu określenia baz danych do uruchomienia w ramach zadania:

![Przykłady grup docelowych](./media/job-automation-overview/targetgroup-examples1.png)

**Przykład 1** przedstawia grupę docelową, która zawiera listę poszczególnych baz danych. Gdy krok zadania jest wykonywany przy użyciu tej grupy docelowej, akcja kroku zadania zostanie wykonana w każdej z tych baz danych.<br>
**Przykład 2** pokazuje grupę docelową, która zawiera serwer jako element docelowy. Gdy krok zadania jest wykonywany przy użyciu tej grupy docelowej, serwer jest dynamicznie wyliczany w celu określenia listy baz danych, które obecnie znajdują się na serwerze. Akcja kroku zadania zostanie wykonana w każdej z tych baz danych.<br>
**Przykład 3** zawiera grupę docelową podobną do *przykładu 2* z wykluczeniem pojedynczej bazy danych. Akcja kroku zadania *nie* zostanie wykonana w wykluczonej bazie danych.<br>
**Przykład 4** przedstawia grupę docelową, która zawiera elastyczną pulę jako obiekt docelowy. Podobnie jak w *przykładzie 2*, pula będzie dynamicznie wyliczana w momencie uruchomienia zadania w celu określenia listy baz danych w puli.
<br><br>

![Dodatkowe przykłady grupy docelowej](./media/job-automation-overview/targetgroup-examples2.png)

**Przykład 5** i **przykład 6** zawierają zaawansowane scenariusze, w których serwery, pule elastyczne i bazy danych można łączyć za pomocą reguł dołączania i wykluczania.<br>
**Przykład 7** pokazuje, że w trakcie uruchamiania zadania można również wyliczać fragmenty na mapie fragmentów.

> [!NOTE]
> Sama baza danych zadań może być obiektem docelowym zadania. W tym scenariuszu baza danych zadań jest traktowana jak każda inna docelowa baza danych. Użytkownik zadania musi być utworzony i ma przyznane odpowiednie uprawnienia w bazie danych zadań, a poświadczenia zakresu bazy danych dla użytkownika zadania muszą także istnieć w bazie danych zadań, podobnie jak w przypadku każdej innej docelowej bazy danych.

#### <a name="elastic-jobs-and-job-steps"></a>Zadania elastyczne i kroki zadań

*Zadanie* to jednostka pracy, która jest wykonywana na podstawie harmonogramu lub jako zadanie jednorazowe. Zadanie składa się z co najmniej jednego *kroku zadania*.

Każdy krok zadania określa skrypt T-SQL do wykonania, co najmniej jedną grupę docelową, względem której ten skrypt będzie uruchamiany, oraz poświadczenia wymagane dla agenta zadań do nawiązania połączenia z docelową bazą danych. Każdy krok zadania posiada możliwy do dostosowania limit czasu i zasady ponawiania oraz pozwala na opcjonalne określenie parametrów wyjściowych.

#### <a name="job-output"></a>Dane wyjściowe zadania

Wyniki wykonywania kroków zadania w każdej docelowej bazie danych są szczegółowo rejestrowane, a dane wyjściowe skryptu mogą zostać przechwycone w określonej tabeli. Możesz określić bazę danych, aby zapisać wszystkie dane zwrócone z zadania.

#### <a name="job-history"></a>Historia zadania

Wyświetl historię wykonywania zadań elastycznych w *bazie danych zadań* , [badając jobs.job_executions tabeli](elastic-jobs-tsql-create-manage.md#monitor-job-execution-status). Zadanie oczyszczania systemu czyści historię wykonywania, która jest starsza niż 45 dni. Aby usunąć historię, która ma mniej niż 45 dni, wywołaj procedurę składowaną **sp_purge_history** w *bazie danych zadań*.

#### <a name="job-status"></a>Stan zadania

Można monitorować wykonywanie zadań elastycznych w *bazie danych zadań* , [badając jobs.job_executions tabeli](elastic-jobs-tsql-create-manage.md#monitor-job-execution-status). 

### <a name="agent-performance-capacity-and-limitations"></a>Wydajność agenta, pojemność i ograniczenia

Zadania elastyczne używają minimalnych zasobów obliczeniowych podczas oczekiwania na zakończenie długotrwałych zadań.

W zależności od rozmiaru grupy docelowej baz danych i żądanego czasu wykonywania zadania (liczba równoczesnych procesów roboczych) agent wymaga różnej ilości zasobów obliczeniowych i wydajności *bazy danych zadań* (im więcej elementów docelowych i większa liczba zadań, tym wymagana jest większa ilość zasobów obliczeniowych).

Obecnie limitem jest 100 zadań współbieżnych.

#### <a name="prevent-jobs-from-reducing-target-database-performance"></a>Zapobieganie zmniejszania wydajności docelowej bazy danych przez zadania

Aby zapewnić, że zasoby nie będą przeciążone podczas uruchamiania zadań w ramach baz danych w elastycznej puli SQL, możliwe jest skonfigurowanie zadań w taki sposób, aby ograniczana była liczba baz danych, w ramach których mogą one być jednocześnie uruchamiane.

## <a name="next-steps"></a>Następne kroki

- [How to create and manage elastic jobs (Jak tworzyć zadania elastyczne i zarządzać nimi)](elastic-jobs-overview.md)
- [Tworzenie zadań elastycznych i zarządzanie nimi przy użyciu programu PowerShell](elastic-jobs-powershell-create.md)
- [Tworzenie zadań elastycznych i zarządzanie nimi za pomocą języka Transact-SQL (T-SQL)](elastic-jobs-tsql-create-manage.md)