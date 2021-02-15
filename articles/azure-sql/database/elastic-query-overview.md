---
title: Elastyczne zapytanie — omówienie
description: Elastyczne zapytanie umożliwia uruchomienie zapytania Transact-SQL obejmującego wiele baz danych.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: overview
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein
ms.date: 12/05/2019
ms.openlocfilehash: cac17bbac96d44d8d9bfce2e168de4ea6d4c5c08
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100364957"
---
# <a name="azure-sql-database-elastic-query-overview-preview"></a>Azure SQL Database Elastic Query — omówienie (wersja zapoznawcza)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Funkcja zapytania elastycznego (w wersji zapoznawczej) umożliwia uruchomienie zapytania Transact-SQL obejmującego wiele baz danych usługi Azure SQL Database. Umożliwia wykonywanie zapytań między bazami danych w celu uzyskiwania dostępu do tabel zdalnych oraz łączenie narzędzi firmy Microsoft i innych firm (Excel, Power BI, Tableau itp.) w celu wykonywania zapytań w warstwach danych z wieloma bazami danych. Korzystając z tej funkcji, można skalować zapytania w poziomie do dużych warstw danych i wizualizować wyniki w raportach analizy biznesowej (BI).

## <a name="why-use-elastic-queries"></a>Dlaczego warto używać zapytań elastycznych

### <a name="azure-sql-database"></a>Azure SQL Database

Wykonaj zapytania między bazami danych w Azure SQL Database całkowicie w języku T-SQL. Pozwala to na wykonywanie zapytań tylko do odczytu zdalnych baz danych i udostępnia opcję dla bieżących SQL Server klientów do migrowania aplikacji przy użyciu nazw składających się z trzech i czterech części lub połączonego serwera w celu SQL Database.

### <a name="available-on-standard-tier"></a>Dostępne w warstwie Standardowa

Elastyczne zapytanie jest obsługiwane zarówno w warstwach usługi standardowa, jak i Premium. Zapoznaj się z sekcją dotyczącą ograniczeń dotyczących wydajności w przypadku niższych warstw usług.

### <a name="push-parameters-to-remote-databases"></a>Wypychanie parametrów do zdalnych baz danych

Zapytania elastyczne mogą teraz wypchnąć parametry SQL do zdalnych baz danych w celu wykonania.

### <a name="stored-procedure-execution"></a>Wykonywanie procedury składowanej

Wykonywanie zdalnych wywołań procedur składowanych lub funkcji zdalnych przy użyciu programu [SP \_ Execute \_ Remote](/sql/relational-databases/system-stored-procedures/sp-execute-remote-azure-sql-database).

### <a name="flexibility"></a>Elastyczność

Tabele zewnętrzne z zapytaniem elastycznym mogą odwoływać się do tabel zdalnych z inną nazwą schematu lub tabeli.

## <a name="elastic-query-scenarios"></a>Scenariusze zapytań elastycznych

Celem jest umożliwienie wykonywania zapytań o scenariusze, w których wiele baz danych tworzy wiersze w pojedynczym ogólnym wyniku. Zapytanie może być składane bezpośrednio przez użytkownika lub aplikację albo pośrednio przez narzędzia, które są połączone z bazą danych. Jest to szczególnie przydatne podczas tworzenia raportów przy użyciu komercyjnych narzędzi analizy biznesowej lub integracji danych lub wszelkich aplikacji, których nie można zmienić. Elastyczne zapytanie umożliwia wykonywanie zapytań w kilku bazach danych przy użyciu znanych SQL Server łączności w narzędziach, takich jak Excel, Power BI, Tableau lub Cognos.
Elastyczne zapytanie umożliwia łatwy dostęp do całej kolekcji baz danych za pośrednictwem zapytań wydawanych przez SQL Server Management Studio lub program Visual Studio, a także ułatwia wykonywanie zapytań między bazami danych z Entity Framework lub innych środowisk ORM. Na rysunku nr 1 przedstawiono scenariusz, w którym istniejąca aplikacja w chmurze (która korzysta z [biblioteki klienta Elastic Database](elastic-database-client-library.md)) kompiluje się w warstwie danych skalowanych w poziomie i jest używana do raportowania między bazami danych.

**Rysunek 1** Elastyczne zapytanie używane w warstwie danych skalowanych w poziomie

![Elastyczne zapytanie używane w warstwie danych skalowanych w poziomie][1]

Scenariusze klientów dla elastycznego zapytania są scharakteryzowane przez następujące topologie:

* **Partycjonowanie pionowe — zapytania obejmujące wiele baz danych** (topologia 1): dane są partycjonowane w pionie między liczbą baz danych w warstwie danych. Zwykle różne zestawy tabel znajdują się w różnych bazach danych. Oznacza to, że schemat różni się w różnych bazach danych. Na przykład wszystkie tabele dla spisu znajdują się w jednej bazie danych, podczas gdy wszystkie tabele związane z ewidencjonowanie aktywności znajdują się w drugiej bazie danych. Typowe przypadki użycia z tą topologią wymagają jednego do wykonywania zapytań między tabelami lub do kompilowania raportów w wielu bazach danych.
* **Partycjonowanie poziome — fragmentowania** (topologia 2): dane są podzielone na partycje w celu rozłożenia wierszy w warstwie danych skalowanych w poziomie. W tym podejściu schemat jest identyczny we wszystkich uczestniczących bazach danych. Takie podejście jest również nazywane "fragmentowania". Fragmentowania można wykonywać i zarządzać nimi za pomocą (1) bibliotek narzędzi elastycznych baz danych lub (2) fragmentowania. Elastyczne zapytanie służy do wykonywania zapytań lub kompilowania raportów w wielu fragmentów. Fragmentów są zwykle bazami danych w puli elastycznej. Elastyczne zapytanie można traktować jako wydajny sposób wykonywania zapytań dotyczących wszystkich baz danych w puli elastycznej, o ile bazy danych współużytkują wspólny schemat.

> [!NOTE]
> Elastyczne zapytanie działa najlepiej w scenariuszach raportowania, w których większość przetwarzania (filtrowanie, agregacja) można wykonać na stronie zewnętrznego źródła. Nie jest to odpowiednie dla operacji ETL, w których duża ilość danych jest transferowana z zdalnych baz danych. W przypadku dużych obciążeń raportowania lub scenariuszy magazynowania danych z bardziej złożonymi zapytaniami warto również rozważyć użycie [usługi Azure Synapse Analytics](https://azure.microsoft.com/services/synapse-analytics).
>  

## <a name="vertical-partitioning---cross-database-queries"></a>Partycjonowanie pionowe — zapytania między bazami danych

Aby rozpocząć kodowanie, zobacz [wprowadzenie do zapytania między bazami danych (partycjonowanie pionowe)](elastic-query-getting-started-vertical.md).

Elastyczne zapytanie może służyć do tworzenia danych znajdujących się w bazie danych w SQL Database dostępne dla innych baz danych w SQL Database. Dzięki temu zapytania z jednej bazy danych mogą odwoływać się do tabel w dowolnej innej zdalnej bazie danych w SQL Database. Pierwszym krokiem jest zdefiniowanie zewnętrznego źródła danych dla każdej zdalnej bazy danych. Zewnętrzne źródło danych jest zdefiniowane w lokalnej bazie danych, z której chcesz uzyskać dostęp do tabel znajdujących się w zdalnej bazie danych. Nie są wymagane żadne zmiany w zdalnej bazie danych. W przypadku typowych scenariuszy partycjonowania pionowego, w których różne bazy danych mają różne schematy, elastyczne zapytania mogą służyć do implementowania typowych przypadków użycia, takich jak dostęp do danych referencyjnych i zapytań między bazami danych.

> [!IMPORTANT]
> Musisz mieć uprawnienie Zmień każde zewnętrzne źródło danych. To uprawnienie jest dołączone do uprawnienia ALTER DATABASE. Aby odwołać się do bazowego źródła danych, należy zmienić wszystkie uprawnienia zewnętrznych źródeł danych.
>

**Dane referencyjne**: topologia służy do zarządzania danymi referencyjnymi. Na poniższej ilustracji dwie tabele (T1 i T2) z danymi referencyjnymi są przechowywane w dedykowanej bazie danych. Przy użyciu zapytania elastycznego można teraz uzyskiwać dostęp do tabel T1 i T2 zdalnie z innych baz danych, jak pokazano na rysunku. Użyj topologii 1, jeśli tabele odwołań są małymi lub zdalnymi zapytania w tabeli referencyjnej mają predykaty selektywne.

**Rysunek 2** Partycjonowanie pionowe — używanie elastycznego zapytania do wykonywania zapytań dotyczących danych referencyjnych

![Partycjonowanie pionowe — używanie elastycznego zapytania do wykonywania zapytań dotyczących danych referencyjnych][3]

**Zapytania między bazami danych**: zapytania elastyczne umożliwiają używanie przypadków użycia wymagających wykonywania zapytań w kilku bazach danych w SQL Database. Rysunek 3 przedstawia cztery różne bazy danych: CRM, spis, HR i produkty. Zapytania wykonywane w jednej z baz danych muszą również mieć dostęp do jednej lub wszystkich innych baz danych. Przy użyciu zapytania elastycznego można skonfigurować bazę danych w tym przypadku, uruchamiając kilka prostych instrukcji języka DDL dla każdej z czterech baz danych. Po tej konfiguracji jednorazowej dostęp do tabeli zdalnej jest prosty w odniesieniu do tabeli lokalnej z zapytań T-SQL lub z narzędzi analizy biznesowej. Ta metoda jest zalecana, jeśli zapytania zdalne nie zwracają dużych wyników.

**Rysunek 3** Partycjonowanie pionowe — używanie elastycznego zapytania do wykonywania zapytań w różnych bazach danych

![Partycjonowanie pionowe — używanie elastycznego zapytania do wykonywania zapytań w różnych bazach danych][4]

Poniższe kroki umożliwiają skonfigurowanie zapytań Elastic Database dla scenariuszy partycjonowania pionowego, które wymagają dostępu do tabeli znajdującej się w zdalnych bazach danych w SQL Database z tym samym schematem:

* [Tworzenie klucza głównego](/sql/t-sql/statements/create-master-key-transact-sql) mymasterkey
* [Utwórz poświadczenia dla poświadczeń w zakresie bazy danych](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)
* [Utwórz/upuść zewnętrzne źródło danych](/sql/t-sql/statements/create-external-data-source-transact-sql) o typie **RDBMS**
* [Utwórz/upuść tabelę zewnętrzną](/sql/t-sql/statements/create-external-table-transact-sql) MyTable

Po uruchomieniu instrukcji języka DDL można uzyskać dostęp do zdalnej tabeli "MyTable", tak jakby była to tabela lokalna. Azure SQL Database automatycznie otwiera połączenie ze zdalną bazą danych, przetwarza żądanie w zdalnej bazie danych i zwraca wyniki.

## <a name="horizontal-partitioning---sharding"></a>Partycjonowanie poziome — fragmentowania

Używanie Elastic Query do wykonywania zadań raportowania w podzielonej na fragmenty, czyli w poziomie partycjonowanym, warstwa danych wymaga [mapy fragmentu Elastic Database](elastic-scale-shard-map-management.md) do reprezentowania baz danych warstwy danych. Zwykle w tym scenariuszu jest używana tylko jedna Mapa fragmentu, a dedykowana baza danych z możliwościami zapytań elastycznych (węzeł główny) służy jako punkt wejścia dla zapytań raportowania. Tylko ta dedykowana baza danych musi mieć dostęp do mapy fragmentu. Rysunek 4 przedstawia tę topologię i jej konfigurację przy użyciu Elastic Database zapytań i mapy fragmentu. Aby uzyskać więcej informacji na temat biblioteki klienta Elastic Database i tworzenia map fragmentu, zobacz [Zarządzanie mapami fragmentu](elastic-scale-shard-map-management.md).

**Rysunek 4** Partycjonowanie poziome — Używanie zapytania elastycznego do raportowania w warstwach danych podzielonej na fragmenty

![Partycjonowanie poziome — Używanie zapytania elastycznego do raportowania w warstwach danych podzielonej na fragmenty][5]

> [!NOTE]
> Elastyczna baza danych zapytań (węzeł główny) może być oddzielną bazą danych lub może być tą samą bazą danych, która hostuje mapę fragmentu.
> Niezależnie od wybranej konfiguracji upewnij się, że warstwa usług i rozmiar obliczeniowy tej bazy danych są wystarczająco duże, aby obsługiwać oczekiwaną ilość żądań logowania/zapytań.

Poniższe kroki umożliwiają skonfigurowanie zapytań Elastic Database dla scenariuszy partycjonowania poziomego, które wymagają dostępu do zestawu tabel znajdującego się na (zazwyczaj) kilka zdalnych baz danych w SQL Database:

* [Tworzenie klucza głównego](/sql/t-sql/statements/create-master-key-transact-sql) mymasterkey
* [Utwórz poświadczenia dla poświadczeń w zakresie bazy danych](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)
* Utwórz [mapę fragmentu](elastic-scale-shard-map-management.md) reprezentującą warstwę danych przy użyciu biblioteki klienta Elastic Database.
* [Utwórz/upuść zewnętrzne źródło danych](/sql/t-sql/statements/create-external-data-source-transact-sql) o typie **SHARD_MAP_MANAGER**
* [Utwórz/upuść tabelę zewnętrzną](/sql/t-sql/statements/create-external-table-transact-sql) MyTable

Po wykonaniu tych kroków można uzyskać dostęp do tabeli partycjonowanej w poziomie "MyTable", tak jakby była to tabela lokalna. Azure SQL Database automatycznie otwiera wiele połączeń równoległych do zdalnych baz danych, w których tabele są przechowywane fizycznie, przetwarza żądania w zdalnych bazach danych i zwraca wyniki.
Więcej informacji na temat kroków wymaganych dla scenariusza partycjonowania poziomego można znaleźć w temacie [elastyczne zapytanie na potrzeby partycjonowania poziomego](elastic-query-horizontal-partitioning.md).

Aby rozpocząć kodowanie, zobacz [wprowadzenie do elastycznego zapytania na potrzeby partycjonowania poziomego (fragmentowania)](elastic-query-getting-started.md).

> [!IMPORTANT]
> Pomyślne wykonanie zapytania elastycznego w dużym zestawie baz danych opiera się na dostępności poszczególnych baz danych podczas wykonywania zapytania. Jeśli jedna z baz danych jest niedostępna, całe zapytanie zakończy się niepowodzeniem. Jeśli planujesz wysyłać zapytania do setek lub tysięcy baz danych jednocześnie, upewnij się, że aplikacja kliencka ma wbudowaną logikę ponowień, lub Rozważ użycie [zadań Elastic Database](./job-automation-overview.md) (wersja zapoznawcza) i zbadanie mniejszych podzestawów baz danych, skonsolidowanie wyników każdego zapytania w jednym miejscu docelowym.

## <a name="t-sql-querying"></a>Zapytania T-SQL

Po zdefiniowaniu zewnętrznych źródeł danych i tabel zewnętrznych można używać zwykłych parametrów połączenia SQL Server do łączenia się z bazami danych, w których zdefiniowano tabele zewnętrzne. Następnie można uruchomić instrukcje języka T-SQL względem tabel zewnętrznych w tym połączeniu z ograniczeniami opisanymi poniżej. Więcej informacji i przykłady zapytań T-SQL można znaleźć w tematach dotyczących [partycjonowania w poziomie](elastic-query-horizontal-partitioning.md) i [partycjonowania pionowego](elastic-query-vertical-partitioning.md).

## <a name="connectivity-for-tools"></a>Łączność dla narzędzi

Za pomocą regularnych SQL Server parametrów połączenia można połączyć swoje aplikacje i narzędzia integracji danych i analizy biznesowej z bazami danych zawierającymi tabele zewnętrzne. Upewnij się, że SQL Server jest obsługiwane jako źródło danych dla narzędzia. Po nawiązaniu połączenia zapoznaj się z bazą danych zapytań elastycznych i tabelami zewnętrznymi w tej bazie danych tak samo jak w przypadku każdej innej SQL Serverj bazy danych, z którą chcesz nawiązać połączenie za pomocą narzędzia.

> [!IMPORTANT]
> Uwierzytelnianie przy użyciu Azure Active Directory z elastycznymi zapytaniami nie jest obecnie obsługiwane.

## <a name="cost"></a>Koszt

Koszt zapytania elastycznego jest uwzględniany w koszcie Azure SQL Database. Należy zwrócić uwagę na to, że topologie, w których zdalne bazy danych znajdują się w innym centrum danych niż obsługiwane są elastyczne punkty końcowe zapytania, ale dane wychodzące ze zdalnych baz danych są często obciążane [stawkami platformy Azure](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="preview-limitations"></a>Ograniczenia wersji zapoznawczej

* Uruchomienie pierwszego zapytania elastycznego może potrwać kilka minut w standardowej warstwie usług. Ten czas jest konieczny do załadowania funkcji zapytania elastycznego. Ładowanie wydajności zwiększa się o wyższe warstwy usług i rozmiary obliczeniowe.
* Skrypty zewnętrznych źródeł danych lub tabel zewnętrznych z programu SSMS lub SSDT nie są jeszcze obsługiwane.
* Usługa Import/Export dla SQL Database nie obsługuje jeszcze zewnętrznych źródeł danych i tabel zewnętrznych. Jeśli musisz użyć polecenia Import/Export, Porzuć te obiekty przed eksportem, a następnie utwórz je ponownie po zaimportowaniu.
* Elastyczne zapytanie obecnie obsługuje tylko dostęp tylko do odczytu do tabel zewnętrznych. Można jednak używać pełnej funkcjonalności języka T-SQL w bazie danych, w której zdefiniowano tabelę zewnętrzną. Może to być przydatne, np. utrwalanie tymczasowych wyników przy użyciu, na przykład, wybierz <column_list> w <local_table> lub Zdefiniuj procedury składowane w bazie danych zapytań elastycznych odwołujących się do tabel zewnętrznych.
* Typy obiektów LOB (w tym typy przestrzenne) nie są obsługiwane w definicjach tabel zewnętrznych. Aby obejść ten element, można utworzyć w zdalnej bazie danych widok, który rzutuje typ LOB na nvarchar (max), zdefiniować tabelę zewnętrzną w widoku zamiast tabeli bazowej, a następnie rzutować ją z powrotem na oryginalny typ LOB w zapytaniach.
* Kolumny typu danych nvarchar (max) w zestawie wyników Wyłącz zaawansowane Technics wsadowe używane w implementacji zapytania elastycznego, które mogą wpływać na wydajność zapytania dla kolejności wielkości, a nawet dwa zamówienia wielkości w przypadku niekanonicznych przypadków użycia, w których duża ilość danych niezagregowanych jest transferowana w wyniku zapytania.
* Statystyki kolumn dla tabel zewnętrznych nie są obecnie obsługiwane. Statystyki tabeli są obsługiwane, ale należy je utworzyć ręcznie.
* Zapytanie elastyczne działa tylko z Azure SQL Database. Nie można jej użyć do wykonywania zapytań o wystąpienie SQL Server.

## <a name="share-your-feedback"></a>Podziel się swoją opinią

Zapoznaj się ze swoją opinią, korzystając z elastycznych zapytań z poniższymi przykładami, na forach MSDN lub na Stack Overflow. Interesuję się wszystkimi rodzajami opinii na temat usługi (wady, przybliżone krawędzie, luki w funkcji).

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z pionowym samouczkiem partycjonowania, zobacz [Rozpoczynanie pracy z kwerendą między bazami danych (partycjonowanie pionowe)](elastic-query-getting-started-vertical.md).
* Aby poznać składnię i przykładowe zapytania dotyczące danych partycjonowanych pionowo, zobacz [wykonywanie zapytań dotyczących partycjonowanych danych w pionie.](elastic-query-vertical-partitioning.md)
* Aby zapoznać się z samouczkiem dotyczącym partycjonowania poziomego (fragmentowania), zobacz [wprowadzenie do elastycznego zapytania na potrzeby partycjonowania poziomego (fragmentowania)](elastic-query-getting-started.md).
* Aby poznać składnię i przykładowe zapytania dla danych z podziałem na partycje, zobacz [wykonywanie zapytań o dane partycjonowane w poziomie.](elastic-query-horizontal-partitioning.md)
* Zapoznaj się z artykułem [SP \_ Execute \_ Remote](/sql/relational-databases/system-stored-procedures/sp-execute-remote-azure-sql-database) dla procedury składowanej, która wykonuje instrukcję języka Transact-SQL w ramach jednego zdalnego Azure SQL Database lub zestawu baz danych służących jako fragmentów w poziomym schemacie partycjonowania.

<!--Image references-->
[1]: ./media/elastic-query-overview/overview.png
[2]: ./media/elastic-query-overview/topology1.png
[3]: ./media/elastic-query-overview/vertpartrrefdata.png
[4]: ./media/elastic-query-overview/verticalpartitioning.png
[5]: ./media/elastic-query-overview/horizontalpartitioning.png

<!--anchors-->