---
title: Typy problemów z wydajnością zapytań w Azure SQL Database
description: Ten artykuł zawiera informacje na temat typów problemów z wydajnością zapytań w Azure SQL Database, a także informacje na temat identyfikowania i rozwiązywania zapytań z tymi problemami.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: troubleshooting
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: wiassaf, sstein
ms.date: 1/14/2021
ms.openlocfilehash: 4d0f5404a64eae99ced0dd797954ba042b50060f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98217230"
---
# <a name="detectable-types-of-query-performance-bottlenecks-in-azure-sql-database"></a>Wykrywalne typy wąskich gardeł wydajności zapytań w usłudze Azure SQL Database
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

Próbując rozwiązać wąskie gardła wydajności, Zacznij od określenia, czy wąskie gardło występuje, gdy zapytanie jest w stanie uruchomienia lub w stanie oczekiwania. Różne rozdzielczości są stosowane w zależności od tego ustalenia. Na poniższym diagramie przedstawiono informacje o czynnikach, które mogą spowodować problemy związane z działaniem lub problem związany z oczekiwaniem. Problemy i rozwiązania dotyczące poszczególnych typów problemów zostały omówione w tym artykule.

Aby wykryć te typy wąskich gardeł wydajności, można użyć Azure SQL Database [Intelligent Insights](database/intelligent-insights-troubleshoot-performance.md#detectable-database-performance-patterns) lub SQL Server [widoków DMV](database/monitoring-with-dmvs.md) .

![Stany obciążeń](./media/identify-query-performance-issues/workload-states.png)

**Problemy związane z uruchamianiem**: problemy związane z działaniem są zwykle związane z problemami z kompilacją, które wykonują nieoptymalny plan zapytania lub problemy z wykonywaniem związane z niewystarczającymi lub niewykorzystanymi zasobami.
**Problemy** zależne: problemy związane z oczekiwaniem są zwykle powiązane z:

- Blokady (blokowanie)
- WE/WY
- Rywalizacja o użycie bazy danych TempDB
- Oczekiwanie na przydzielenie pamięci

## <a name="compilation-problems-resulting-in-a-suboptimal-query-plan"></a>Problemy z kompilacją powodujące nieoptymalny plan zapytania

Nieoptymalny plan wygenerowany przez optymalizator zapytań SQL może być przyczyną powolnej wydajności zapytań. Optymalizator zapytań SQL może utworzyć nieoptymalny plan z powodu braku indeksu, nieodświeżonych statystyk, nieprawidłowego oszacowania liczby wierszy do przetworzenia lub nieprawidłowego oszacowania wymaganej pamięci. Jeśli wiesz, że zapytanie zostało wykonane szybciej w przeszłości lub w innym wystąpieniu, porównaj rzeczywiste plany wykonywania, aby zobaczyć, czy są różne.

- Zidentyfikuj wszystkie brakujące indeksy przy użyciu jednej z następujących metod:

  - Użyj [Intelligent Insights](database/intelligent-insights-troubleshoot-performance.md#missing-index).
  - [Database Advisor](database/database-advisor-implement-performance-recommendations.md) dla baz danych o pojedynczej i puli.
  - Widoków DMV. Ten przykład pokazuje wpływ brakującego indeksu, sposób wykrywania [brakujących indeksów](database/performance-guidance.md#identifying-and-adding-missing-indexes) przy użyciu widoków DMV oraz wpływ implementacji brakującego zalecenia dotyczącego indeksu.
- Spróbuj zastosować [wskazówki dotyczące zapytania](/sql/t-sql/queries/hints-transact-sql-query), [zaktualizować statystyki](/sql/t-sql/statements/update-statistics-transact-sql)lub [ponownie skompilować indeksy](/sql/relational-databases/indexes/reorganize-and-rebuild-indexes) , aby uzyskać lepszy plan. Włącz [automatyczną korektę planu](../azure-sql/database/automatic-tuning-overview.md) w Azure SQL Database, aby automatycznie wyeliminować te problemy.

  Ten [przykład](database/performance-guidance.md#query-tuning-and-hinting) pokazuje wpływ nieoptymalnego planu zapytania ze względu na zapytanie parametryczne, sposób wykrywania tego warunku i sposób użycia wskazówki zapytania do rozwiązania.

- Spróbuj zmienić poziom zgodności bazy danych i zaimplementować inteligentne przetwarzanie zapytań. Optymalizator zapytań SQL może generować inny plan zapytania w zależności od poziomu zgodności bazy danych. Wyższe poziomy zgodności zapewniają bardziej [inteligentne możliwości przetwarzania zapytań](/sql/relational-databases/performance/intelligent-query-processing).

  - Aby uzyskać więcej informacji na temat przetwarzania zapytań, zobacz [Przewodnik po architekturze przetwarzania zapytań](/sql/relational-databases/query-processing-architecture-guide).
  - Aby zmienić poziomy zgodności bazy danych i dowiedzieć się więcej na temat różnic między poziomami zgodności, zobacz [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level).
  - Aby dowiedzieć się więcej na temat oszacowania kardynalności, zobacz [oszacowanie kardynalności](/sql/relational-databases/performance/cardinality-estimation-sql-server)

## <a name="resolving-queries-with-suboptimal-query-execution-plans"></a>Rozpoznawanie zapytań przy użyciu nieoptymalnych planów wykonywania zapytań

W poniższych sekcjach omówiono sposób rozwiązywania zapytań z nieoptymalnym planem wykonywania zapytań.

### <a name="queries-that-have-parameter-sensitive-plan-psp-problems"></a><a name="ParamSniffing"></a> Zapytania, które mają problemy z planem z uwzględnieniem parametrów (PSP)

Problem z planem wrażliwym na parametry (PSP) występuje, gdy optymalizator zapytań generuje plan wykonywania zapytania, który jest optymalny tylko dla konkretnej wartości parametru (lub zestawu wartości), a w pamięci podręcznej nie jest optymalny dla wartości parametrów, które są używane podczas wykonywania kolejnych operacji. Plany, które nie są optymalne, mogą powodować problemy z wydajnością zapytań i obniżać ogólną przepływność obciążeń.

Aby uzyskać więcej informacji na temat wykrywania parametrów i przetwarzania zapytań, zobacz [Przewodnik po architekturze przetwarzania zapytań](/sql/relational-databases/query-processing-architecture-guide#ParamSniffing).

Kilka obejść może rozwiązać problemy związane z PSP. Każde obejście ma powiązane kompromisy i wady:

- Użyj wskazówki dotyczącej ponownej [kompilacji](/sql/t-sql/queries/hints-transact-sql-query) w każdym wykonaniu zapytania. To obejście umożliwia wymianę czasu kompilacji i zwiększenie procesora w celu zapewnienia lepszej jakości planu. `RECOMPILE`Opcja jest często niemożliwa w przypadku obciążeń wymagających dużej przepływności.
- Użyj [opcji zapytania (Optymalizacja dla...)](/sql/t-sql/queries/hints-transact-sql-query) , aby zastąpić rzeczywistą wartość parametru wartością typowego parametru, która generuje plan, który jest wystarczająco dobry dla większości wartości parametrów. Ta opcja wymaga dobrego poznania optymalnych wartości parametrów i skojarzonych cech planu.
- Użyj [opcji (Optymalizacja dla NIEznanej)](/sql/t-sql/queries/hints-transact-sql-query) wskazówki, aby zastąpić rzeczywistą wartość parametru, a zamiast tego użyć średniej gęstości wektora. Można to zrobić również, przechwytując wartości parametrów przychodzących w zmiennych lokalnych, a następnie używając zmiennych lokalnych w predykatach zamiast używać samych parametrów. W przypadku tej poprawki średnia gęstość musi być *wystarczająca*.
- Wyłącz wykrywanie parametrów, używając wskazówki zapytania [DISABLE_PARAMETER_SNIFFING](/sql/t-sql/queries/hints-transact-sql-query) .
- Użyj wskazówki zapytania [KEEPFIXEDPLAN](/sql/t-sql/queries/hints-transact-sql-query) , aby uniemożliwić ponowne kompilacje w pamięci podręcznej. W tym rozwiązaniu przyjęto założenie, że jest to ten sam plan, który jest już w pamięci podręcznej. Możesz również wyłączyć automatyczne aktualizacje statystyk, aby zmniejszyć prawdopodobieństwo wykluczenia dobrego planu i skompilowania nowego nieprawidłowego planu.
- Wymuś planowanie jawnie za pomocą wskazówki zapytania [use plan](/sql/t-sql/queries/hints-transact-sql-query) przez ponowne zapisanie zapytania i dodanie wskazówki w tekście zapytania. Lub ustawić konkretny plan przy użyciu magazynu zapytań lub przez włączenie [dostrajania automatycznego](../azure-sql/database/automatic-tuning-overview.md).
- Zastąp pojedynczą procedurę z zagnieżdżonym zestawem procedur, które mogą być używane na podstawie logiki warunkowej i skojarzonych wartości parametrów.
- Utwórz alternatywny sposób wykonywania ciągów dynamicznych do definicji procedury statycznej.

Aby uzyskać więcej informacji na temat rozwiązywania problemów z PSP, zobacz następujące wpisy w blogu:

- [Zapach I parametr](/archive/blogs/queryoptteam/i-smell-a-parameter)
- [Conor a dynamiczne SQL a procedury i planowanie jakości zapytań parametrycznych](/archive/blogs/conor_cunningham_msft/conor-vs-dynamic-sql-vs-procedures-vs-plan-quality-for-parameterized-queries)
- [Techniki optymalizacji zapytań SQL w SQL Server: wykrywanie parametrów](https://www.sqlshack.com/query-optimization-techniques-in-sql-server-parameter-sniffing/)

### <a name="compile-activity-caused-by-improper-parameterization"></a>Działanie kompilowania spowodowane przez niewłaściwe parametryzacja

Gdy zapytanie zawiera literały, aparat bazy danych automatycznie parameterizes instrukcję lub użytkownik jawnie parameterizes instrukcji w celu zmniejszenia liczby kompilacji. Duża liczba kompilacji dla zapytania korzystającego z tego samego wzorca, ale różne wartości literałów mogą spowodować wysokie użycie procesora CPU. Podobnie, jeśli tylko częściowo Sparametryzuj zapytanie, które nadal ma literały, aparat bazy danych nie Sparametryzuj zapytania dalej.

Oto przykład zapytania częściowo sparametryzowanego:

```sql
SELECT *
FROM t1 JOIN t2 ON t1.c1 = t2.c1
WHERE t1.c1 = @p1 AND t2.c2 = '961C3970-0E54-4E8E-82B6-5545BE897F8F';
```

W tym przykładzie `t1.c1` przyjmuje `@p1` , ale `t2.c2` nadal przyjmuje identyfikator GUID jako literał. W takim przypadku, jeśli zmienisz wartość dla `c2` , zapytanie jest traktowane jako inne zapytanie i zostanie wykonana Nowa kompilacja. Aby zmniejszyć kompilacje w tym przykładzie, należy również Sparametryzuj identyfikator GUID.

Następujące zapytanie pokazuje liczbę zapytań według skrótu zapytania, aby określić, czy zapytanie jest prawidłowo sparametryzowane:

```sql
SELECT TOP 10
  q.query_hash
  , count (distinct p.query_id ) AS number_of_distinct_query_ids
  , min(qt.query_sql_text) AS sampled_query_text
FROM sys.query_store_query_text AS qt
  JOIN sys.query_store_query AS q
     ON qt.query_text_id = q.query_text_id
  JOIN sys.query_store_plan AS p
     ON q.query_id = p.query_id
  JOIN sys.query_store_runtime_stats AS rs
     ON rs.plan_id = p.plan_id
  JOIN sys.query_store_runtime_stats_interval AS rsi
     ON rsi.runtime_stats_interval_id = rs.runtime_stats_interval_id
WHERE
  rsi.start_time >= DATEADD(hour, -2, GETUTCDATE())
  AND query_parameterization_type_desc IN ('User', 'None')
GROUP BY q.query_hash
ORDER BY count (distinct p.query_id) DESC;
```

### <a name="factors-that-affect-query-plan-changes"></a>Czynniki wpływające na zmiany planu zapytania

Ponowna kompilacja planu wykonania zapytania może spowodować wygenerowanie planu zapytania, który różni się od oryginalnego buforowanego planu. Istniejący oryginalny plan może zostać automatycznie ponownie skompilowany z różnych powodów:

- Do zmian w schemacie odwołuje się zapytanie
- Do zmian danych w tabelach odwołuje się zapytanie
- Opcje kontekstu zapytania zostały zmienione

Skompilowany plan może zostać wysunięty z pamięci podręcznej z różnych powodów, takich jak:

- Ponowne uruchomienie wystąpienia
- Zmiany konfiguracji w zakresie bazy danych
- Wykorzystanie pamięci
- Jawne żądania wyczyszczenia pamięci podręcznej

Jeśli używasz wskazówki RECOMPILE, plan nie zostanie zapisany w pamięci podręcznej.

Ponowna kompilacja (lub Nowa kompilacja po wykluczeniu pamięci podręcznej) nadal może skutkować generowaniem planu wykonywania zapytania, który jest identyczny z oryginałem. W przypadku zmiany planu z planu poprzedniego lub oryginalnego te wyjaśnienia mogą być następujące:

- **Zmieniony projekt fizyczny**: na przykład nowo utworzone indeksy bardziej efektywnie obejmują wymagania zapytania. Nowe indeksy mogą być używane w nowej kompilacji, jeśli optymalizator zapytań zdecyduje, że użycie tego nowego indeksu jest bardziej optymalne niż użycie struktury danych, która została pierwotnie wybrana dla pierwszej wersji wykonywania zapytania. Wszelkie zmiany fizyczne obiektów, do których istnieją odwołania, mogą spowodować powstanie nowego planu w czasie kompilacji.

- **Różnice zasobów serwera**: gdy plan w jednym systemie różni się od planu w innym systemie, dostępność zasobów, taka jak liczba dostępnych procesorów, może mieć wpływ na sposób generowania planu. Na przykład jeśli jeden system ma więcej procesorów, można wybrać plan równoległy.

- **Różne statystyki**: Statystyka skojarzona z obiektami, do których istnieją odwołania, mogła ulec zmianie lub może być istotnie różna od statystyk oryginalnego systemu. W przypadku zmiany statystyk i ponownej kompilacji optymalizator zapytań używa statystyk rozpoczynających się od momentu zmiany. Dane statystyczne i częstotliwości dla zaktualizowanych statystyk mogą się różnić od tych, które zostały oryginalnie skompilowane. Te zmiany są używane do tworzenia oszacowań kardynalności. (*Oszacowania kardynalności* to liczba wierszy, które powinny przepływać przez drzewo zapytania logicznego). Zmiany w oszacowaniach kardynalności mogą prowadzić do wyboru różnych operatorów fizycznych i skojarzonych z nimi zamówień. Nawet niewielkie zmiany w statystyce mogą spowodować zmianę planu wykonywania zapytania.

- **Zmieniony poziom zgodności bazy danych lub wersja szacowaniaa Kardynalność**: zmiany poziomu zgodności bazy danych mogą umożliwić nowe strategie i funkcje, które mogą spowodować powstanie innego planu wykonywania zapytań. Poza poziomem zgodności bazy danych wyłączona lub włączona flaga śledzenia 4199 lub zmieniony stan QUERY_OPTIMIZER_HOTFIXES konfiguracji z zakresem bazy danych może również mieć wpływ na Opcje planu wykonywania zapytania w czasie kompilacji. Flagi śledzenia 9481 (Wymuś starszą wersję CE) i 2312 (Wymuś ustawienie domyślne CE) wpływają również na plan.

## <a name="resource-limits-issues"></a>Problemy z ograniczeniami zasobów

Niska wydajność zapytań nie jest związana z nieoptymalnymi planami zapytań i brakujące indeksy są zwykle związane z niewystarczającymi lub nieużywanymi zasobami. Jeśli plan zapytania jest optymalny, zapytanie (i baza danych) może powodować limity zasobów dla bazy danych, puli elastycznej lub wystąpienia zarządzanego. Przykładem może być nadmiarowa przepustowość zapisu w dzienniku dla poziomu usługi.

- Wykrywanie problemów dotyczących zasobów przy użyciu Azure Portal: Aby sprawdzić, czy są to problemy, zobacz [SQL Database monitorowania zasobów](database/monitor-tune-overview.md#azure-sql-database-and-azure-sql-managed-instance-resource-monitoring). W przypadku pojedynczych baz danych i pul elastycznych zapoznaj się z tematem [Database Advisor zalecenia dotyczące wydajności](database/database-advisor-implement-performance-recommendations.md) i [szczegółowe informacje o wydajności zapytań](database/query-performance-insight-use.md).
- Wykrywanie limitów zasobów przy użyciu [Intelligent Insights](database/intelligent-insights-troubleshoot-performance.md#reaching-resource-limits)
- Wykrywanie problemów z zasobami przy użyciu [widoków DMV](database/monitoring-with-dmvs.md):

  - [Sys.dm_db_resource_stats](database/monitoring-with-dmvs.md#monitor-resource-use) DMV zwraca procesor, we/wy i użycie pamięci dla bazy danych. Jeden wiersz istnieje dla każdego 15-sekundowego interwału, nawet jeśli w bazie danych nie ma żadnych działań. Dane historyczne są przechowywane przez jedną godzinę.
  - [Sys.resource_stats](database/monitoring-with-dmvs.md#monitor-resource-use) DMV zwraca dane użycia procesora CPU i magazynu dla Azure SQL Database. Dane są zbierane i agregowane w odstępach 5-minutowych.
  - [Wiele indywidualnych zapytań, które łącznie zużywają duże użycie procesora CPU](database/monitoring-with-dmvs.md#many-individual-queries-that-cumulatively-consume-high-cpu)

W przypadku zidentyfikowania problemu jako niewystarczającego zasobu można uaktualnić zasoby, aby zwiększyć pojemność bazy danych w celu zaabsorbowania wymagań procesora. Aby uzyskać więcej informacji, zobacz [skalowanie zasobów pojedynczej bazy danych w Azure SQL Database](database/single-database-scale.md) i [skalowanie zasobów puli elastycznej w Azure SQL Database](database/elastic-pool-scale.md). Aby uzyskać informacje na temat skalowania wystąpienia zarządzanego, zobacz [limity zasobów w warstwie usług](managed-instance/resource-limits.md#service-tier-characteristics)

## <a name="performance-problems-caused-by-increased-workload-volume"></a>Problemy z wydajnością spowodowane przez zwiększony wolumin obciążeń

Zwiększenie natężenia ruchu i obciążenia aplikacji może spowodować zwiększenie użycia procesora CPU. Jednak należy zachować ostrożność, aby prawidłowo zdiagnozować ten problem. Gdy widzisz problem z dużym procesorem CPU, Odpowiedz na te pytania, aby określić, czy wzrost jest spowodowany przez zmiany w woluminie obciążeń:

- Czy zapytania z aplikacji są przyczyną problemu z dużym procesorem CPU?
- Dla [najważniejszych zapytań zużywających procesor CPU, które można zidentyfikować](database/monitoring-with-dmvs.md#the-cpu-issue-occurred-in-the-past):

  - Czy wiele planów wykonywania jest skojarzonych z tym samym zapytaniem? Jeśli tak, dlaczego?
  - Czy w przypadku zapytań z tym samym planem wykonania były spójne czasy wykonywania? Czy Liczba wykonań została zwiększona? W takim przypadku zwiększenie obciążenia prawdopodobnie spowoduje problemy z wydajnością.

Podsumowując, jeśli plan wykonywania zapytania nie został wykonany inaczej, ale użycie procesora CPU wzrosło wraz z licznikiem wykonania, problem z wydajnością jest prawdopodobnie związany z wzrostem obciążenia.

Nie zawsze można łatwo zidentyfikować zmiany woluminu obciążenia, który zapewnia problem z procesorem CPU. Należy wziąć pod uwagę następujące czynniki:

- **Zmieniono użycie zasobów**: na przykład Rozważmy scenariusz, w którym użycie procesora CPU wzrosło do 80% przez dłuższy czas. Samo użycie procesora CPU nie oznacza, że wolumin obciążenia został zmieniony. Regresje w planie wykonywania zapytań oraz zmiany w dystrybucji danych mogą również przyczynić się do większej ilości zasobów, nawet jeśli aplikacja wykonuje to samo obciążenie.

- **Wygląd nowej kwerendy**: aplikacja może uzyskać nowy zestaw zapytań w różnym czasie.

- **Zwiększenie lub zmniejszenie liczby żądań**: ten scenariusz jest najbardziej oczywistą miarą obciążenia. Liczba zapytań nie zawsze odpowiada większej ilości zasobów. Jednak ten pomiar jest nadal znaczący, przy założeniu, że inne czynniki nie są zmieniane.

Użyj Intelligent Insights, aby wykrywać [wzrost obciążeń](database/intelligent-insights-troubleshoot-performance.md#workload-increase) i [planować regresje](database/intelligent-insights-troubleshoot-performance.md#plan-regression).

## <a name="waiting-related-problems"></a>Problemy związane z oczekiwaniem

Gdy zostanie wyeliminowany nieoptymalny plan i problemy *związane* z uruchamianiem, które są związane z problemami z wykonywaniem, problem z wydajnością zazwyczaj jest oczekiwany w przypadku niektórych zasobów. Problemy zależne mogą być spowodowane przez:

- **Blokowanie**:

  Jedno zapytanie może blokować blokowanie obiektów w bazie danych, podczas gdy inne próbują uzyskać dostęp do tych samych obiektów. Można zidentyfikować blokowanie zapytań za pomocą [widoków DMV](database/monitoring-with-dmvs.md#monitoring-blocked-queries) lub [Intelligent Insights](database/intelligent-insights-troubleshoot-performance.md#locking). Aby uzyskać więcej informacji, zobacz [Omówienie i rozwiązywanie problemów z blokowaniem usługi Azure SQL](database/understand-resolve-blocking.md).
- **Problemy we/wy**

  Zapytania mogą oczekiwać na zapisanie stron w plikach danych lub dziennika. W takim przypadku sprawdź `INSTANCE_LOG_RATE_GOVERNOR` `WRITE_LOG` statystyki, lub `PAGEIOLATCH_*` Zaczekaj na DMV. Zobacz Korzystanie z widoków DMV, aby [identyfikować problemy z wydajnością we/wy](database/monitoring-with-dmvs.md#identify-io-performance-issues).
- **Problemy z bazą danych TempDB**

  Jeśli obciążenie używa tabel tymczasowych lub w planach występuje wyciek bazy danych TempDB, zapytania mogą mieć problem z przepływem pracy w bazie danych TempDB. Zobacz Używanie widoków DMV do [tożsamości bazy danych tempdb](database/monitoring-with-dmvs.md#identify-tempdb-performance-issues).
- **Problemy związane z pamięcią**

  Jeśli obciążenie nie ma wystarczającej ilości pamięci, może zostać pobrana stron życia strony lub zapytania mogą uzyskać mniejszą ilość pamięci niż jest to potrzebne. W niektórych przypadkach wbudowana analiza w optymalizatora zapytań naprawi problemy związane z pamięcią. Zobacz Używanie widoków DMV, aby [identyfikować problemy z przydzielaniem pamięci](database/monitoring-with-dmvs.md#identify-memory-grant-wait-performance-issues).

### <a name="methods-to-show-top-wait-categories"></a>Metody wyświetlania najważniejszych kategorii oczekiwania

Te metody są często używane do wyświetlania najważniejszych kategorii typów oczekiwania:

- Użyj Intelligent Insights, aby identyfikować zapytania z obniżeniem wydajności z powodu [zwiększonego oczekiwania](database/intelligent-insights-troubleshoot-performance.md#increased-wait-statistic)
- Użyj [magazynu zapytań](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) , aby znaleźć statystyki oczekiwania dla każdego zapytania w czasie. W magazynie zapytań typy oczekiwania są łączone w kategorie oczekiwania. Możesz znaleźć Mapowanie kategorii oczekiwania na typy oczekiwania w [sys.query_store_wait_stats](/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql#wait-categories-mapping-table).
- Użyj [sys.dm_db_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) , aby zwrócić informacje o wszystkich oczekiwaniach napotkanych przez wątki wykonywane w trakcie operacji zapytania. Ten Zagregowany widok służy do diagnozowania problemów z wydajnością Azure SQL Database a także z określonymi kwerendami i partiami. Zapytania oczekują na zasoby, czekają na kolejki lub czekają na zewnątrz.
- Użyj [sys.dm_os_waiting_tasks](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) , aby zwrócić informacje o kolejce zadań oczekujących na jakiś zasób.

W scenariuszach z wysokim procesorem CPU magazyn zapytań i statystyki oczekiwania mogą nie odzwierciedlać użycia procesora, jeśli:

- Nadal są wykonywane zapytania o wysokim zużyciu procesora CPU.
- W przypadku przełączenia w tryb failover działały zapytania o wysokim zużyciu procesora CPU.

Widoków DMV, który śledzi magazyn zapytań i statystyki oczekiwania, pokazują wyniki tylko dla zapytań zakończonych pomyślnie i przekroczenia limitu czasu. Nie wyświetlają one danych dla aktualnie wykonywanych instrukcji do momentu zakończenia instrukcji. Użyj dynamicznego widoku zarządzania [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) , aby śledzić aktualnie wykonywane zapytania i czas skojarzonego pracownika.

> [!TIP]
> Dodatkowe narzędzia:
>
> - [TigerToolbox czeka i Zamky](https://github.com/Microsoft/tigertoolbox/tree/master/Waits-and-Latches)
> - [TigerToolbox usp_whatsup](https://github.com/Microsoft/tigertoolbox/tree/master/usp_WhatsUp)

## <a name="next-steps"></a>Następne kroki

[Przegląd SQL Database monitorowania i dostrajania](database/monitor-tune-overview.md)