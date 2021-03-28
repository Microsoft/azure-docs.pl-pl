---
title: 'Oracle do Azure Database for PostgreSQL: Przewodnik migracji'
titleSuffix: Azure Database for PostgreSQL
description: W tym przewodniku pokazano, jak migrować schemat Oracle do Azure Database for PostgreSQL.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.subservice: migration-guide
ms.topic: how-to
ms.date: 03/18/2021
ms.openlocfilehash: 1a20ffd7150ac75721b2affc2f4375301c4754c8
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2021
ms.locfileid: "105643566"
---
# <a name="migrate-oracle-to-azure-database-for-postgresql"></a>Migrowanie bazy danych Oracle do Azure Database for PostgreSQL

W tym przewodniku pokazano, jak migrować schemat Oracle do Azure Database for PostgreSQL. 

Aby uzyskać szczegółowe i kompleksowe wskazówki dotyczące migracji, zobacz [zasoby przewodnika migracji](https://github.com/microsoft/OrcasNinjaTeam/blob/master/Oracle%20to%20PostgreSQL%20Migration%20Guide/Oracle%20to%20Azure%20Database%20for%20PostgreSQL%20Migration%20Guide.pdf). 

## <a name="prerequisites"></a>Wymagania wstępne

Aby migrować schemat Oracle do Azure Database for PostgreSQL, musisz: 

- Sprawdź, czy środowisko źródłowe jest obsługiwane. 
- Pobierz najnowszą wersję programu [ora2pg](https://ora2pg.darold.net/). 
- Najnowsza wersja [modułu DBD](https://www.cpan.org/modules/by-module/DBD/). 


## <a name="overview"></a>Omówienie

PostgreSQL jest jednym z najbardziej zaawansowanych baz danych typu "open source". W tym artykule opisano, jak za pomocą narzędzia ora2pg Free przeprowadzić migrację bazy danych Oracle do programu PostgreSQL. Możesz użyć ora2pg, bezpłatnego narzędzia, aby przeprowadzić migrację bazy danych Oracle lub MySQL do schematu zgodnego z PostgreSQL. Narzędzie nawiązuje połączenie z bazą danych Oracle, skanuje ją automatycznie i wyodrębnia jej strukturę lub dane. Następnie ora2pg generuje skrypty SQL, które można załadować do bazy danych PostgreSQL. ora2pg można używać do zadań z odtwarzania bazy danych Oracle, wykonywania ogromnej migracji bazy danych przedsiębiorstwa lub po prostu replikowania niektórych danych Oracle do bazy danych PostgreSQL. Jest to łatwe w użyciu i nie wymaga żadnej wiedzy dotyczącej bazy danych Oracle innej niż możliwość zapewnienia parametrów potrzebnych do łączenia się z bazą danych programu Oracle.

> [!NOTE]
> Aby uzyskać więcej informacji na temat korzystania z najnowszej wersji programu ora2pg, zapoznaj się z [dokumentacją ora2pg](https://ora2pg.darold.net/documentation.html).

### <a name="typical-ora2pg-migration-architecture"></a>Typowa architektura migracji ora2pg

![Zrzut ekranu architektury migracji ora2pg.](media/howto-migrate-from-oracle/ora2pg-migration-architecture.png)

Po zainicjowaniu obsługi maszyny wirtualnej i Azure Database for PostgreSQL do włączenia łączności między nimi są potrzebne dwie konfiguracje: **Zezwalaj na usługi platformy Azure** i **Wymuszaj połączenie SSL**, wymienione w następujący sposób:

- Blok **zabezpieczeń połączeń** — > **Zezwalanie na dostęp do usług platformy Azure** — > włączona

- Blok **zabezpieczeń połączeń** — > **Ustawienia protokołu SSL**  ->  **wymuszają połączenie SSL** — > wyłączone

### <a name="recommendations"></a>Zalecenia

- Aby zwiększyć wydajność operacji oceny lub eksportu na serwerze Oracle, Zbierz dane statystyczne w następujący sposób:

   ```
   BEGIN
   
     DBMS_STATS.GATHER_SCHEMA_STATS
     DBMS_STATS.GATHER_DATABASE_STATS
     DBMS_STATS.GATHER_DICTIONARY_STATS
     END;
   ```

- Eksportuj dane przy użyciu polecenia COPY zamiast INSERT.

- Unikaj eksportowania tabel z ich FKs, ograniczeniami i indeksami — spowoduje to wolniejsze Importowanie danych do PostgreSQL.

- Utwórz materiałowe widoki przy użyciu **klauzuli No Data** i Odśwież je później.

- Jeśli to możliwe, zaimplementuj unikatowe indeksy w widokach z materiałami, spowoduje to szybsze odświeżanie przy użyciu składni `REFRESH MATERIALIZED VIEW CONCURRENTLY` .



## <a name="pre-migration"></a>Przed migracją 

Po sprawdzeniu, czy środowisko źródłowe jest obsługiwane i upewnienia się, że zostały spełnione wszystkie wymagania wstępne, możesz rozpocząć etap przed migracją. Ta część procesu obejmuje przeprowadzenie spisu baz danych, które należy zmigrować, ocenianie tych baz danych pod kątem potencjalnych problemów z migracją lub blokowaniem, a następnie rozwiązanie wszystkich elementów, które mogły zostać odkryte. W przypadku migracji heterogenicznych, takich jak Oracle do Azure Database for PostgreSQL, ten etap obejmuje również konwertowanie schematów w źródłowych bazach danych, aby były zgodne ze środowiskiem docelowym.

### <a name="discover"></a>Odnajdywanie

Celem fazy odnajdywania jest zidentyfikowanie istniejących źródeł danych i szczegółowe informacje o funkcjach, które są używane do lepszego zrozumienia i zaplanowania migracji. Ten proces polega na skanowaniu sieci w celu zidentyfikowania wszystkich wystąpień programu Oracle w organizacji wraz z używaną wersją i funkcjami.

Skrypty przed oceną programu Microsoft Oracle są uruchamiane w bazie danych Oracle. Skrypty przed oceną są zestawem zapytań, które trafią na metadane Oracle i oferują następujące informacje:

- Spis baz danych, w tym liczba obiektów według schematu, typu i stanu.

- Przybliżone oszacowanie danych pierwotnych w każdym schemacie (w oparciu o dane statystyczne).

- Ustalanie wielkości tabel w każdym schemacie.

- Liczba wierszy kodu na pakiet, funkcja, procedura, itp.

Pobierz powiązane skrypty z [witryny sieci Web ora2pg](http://ora2pg.darold.net/).

### <a name="assess"></a>Ocena

Po zakończeniu spisu baz danych Oracle, aby uzyskać pomysł dotyczący rozmiaru bazy danych i jakie są wyzwania, następnym krokiem jest uruchomienie oceny.

Oszacowanie kosztów procesu migracji z programu Oracle do PostgreSQL nie jest proste. Aby uzyskać dobrą ocenę tego kosztu migracji, ora2pg sprawdzi wszystkie obiekty bazy danych, wszystkie funkcje i procedury składowane w celu wykrycia, czy nadal istnieją pewne obiekty i kod PL/SQL, które nie mogą być automatycznie konwertowane przez ora2pg.

ora2pg ma tryb analizy zawartości, który sprawdza bazę danych Oracle w celu wygenerowania raportu tekstowego zawierającego dane zawarte w bazie danych Oracle i czego nie można wyeksportować.

Aby aktywować tryb **analizy i raportu** , użyj wyeksportowanego typu, `SHOW_REPORT` jak pokazano w następującym poleceniu:

```
ora2pg -t SHOW_REPORT
```

Po przeanalizowaniu bazy danych ora2pg z możliwością konwertowania kodu SQL i PL/SQL z składni Oracle na PostgreSQL, można kontynuować, szacując problemy z kodem i czas potrzebny na przeprowadzenie pełnej migracji bazy danych.

Aby oszacować koszt migracji w dniach Man, ora2pg umożliwia użycie dyrektywy konfiguracyjnej o nazwie ESTIMATE_COST, która może być również włączona w wierszu polecenia:

```
ora2pg -t SHOW_REPORT --estimate_cost
```

Domyślna jednostka migracji reprezentuje około pięciu minut dla eksperta PostgreSQL. Jeśli jest to Twoja pierwsza migracja, możesz ją zwiększyć przy użyciu dyrektywy konfiguracyjnej COST_UNIT_VALUE lub opcji wiersza polecenia--cost_unit_value.

Ostatni wiersz raportu przedstawia całkowity szacowany kod migracji w dniach Man po liczbie jednostek migracji szacowanych dla każdego obiektu.

Ta jednostka migracji reprezentuje około pięciu minut dla eksperta PostgreSQL. Jeśli jest to Twoja pierwsza migracja, można zwiększyć wartość domyślną przy użyciu dyrektywy Configuration COST_UNIT_VALUE lub opcji wiersza polecenia--cost_unit_value. Znajdź poniżej pewne wahania oceny a) tabeli; b) Ocena schematu c) przy użyciu domyślnej oceny schematu cost_unit (5 min) d) przy użyciu 10 minut jako jednostki kosztów.

```
ora2pg -t SHOW_TABLE -c c:\ora2pg\ora2pg_hr.conf > c:\ts303\hr_migration\reports\tables.txt ora2pg -t SHOW_COLUMN -c c:\ora2pg\ora2pg_hr.conf > c:\ts303\hr_migration\reports\columns.txt
ora2pg -t SHOW_REPORT -c c:\ora2pg\ora2pg_hr.conf --dump_as_html --estimate_cost > c:\ts303\hr
_migration\reports\report.html
ora2pg -t SHOW_REPORT -c c:\ora2pg\ora2pg_hr.conf –-cost_unit_value 10 --dump_as_html --estimate_cost > c:\ts303\hr_migration\reports\report2.html
```

Dane wyjściowe oceny schematu są zilustrowane w następujący sposób:

**Poziom migracji: B-5**

Poziomy migracji:

A — migracja, która może być uruchamiana automatycznie

B — migracja z kodem do ponownego zapisu i kosztem ludzkim do 5 dni

C — migracja z kodem ponowne zapisywanie i kosztem ludzkim powyżej 5 dni

Poziomy techniczne:

1 = uproszczone: brak przechowywanych funkcji i wyzwalaczy

2 = łatwe: brak przechowywanych funkcji, ale z wyzwalaczami, bez ręcznego zapisywania

3 = proste: przechowywane funkcje i/lub wyzwalacze, bez ręcznego zapisywania

4 = ręczne: brak przechowywanych funkcji, ale z wyzwalaczami lub widokami z ponownym pisaniem kodu

5 = trudne: przechowywane funkcje i/lub wyzwalacze z zapisaniem kodu

Ocena składa się z litery (A lub B) w celu określenia, czy migracja wymaga ręcznego zapisu, czy nie, a liczba z przedziału od 1 do 5 wskazuje poziom trudności technicznych. Dostępna jest dodatkowa opcja — human_days_limit, aby określić liczbę dni, w których poziom migracji powinien być ustawiony na wartość C, aby wskazać, że potrzebuje ogromnych nakładów pracy i pełnego zarządzania projektami z obsługą migracji. Wartość domyślna to 10 dni. Aby trwale zmienić tę wartość domyślną, można użyć dyrektywy Configuration HUMAN_DAYS_LIMIT.

Ta funkcja została opracowana w celu ułatwienia decydowania, która baza danych może być migrowana w pierwszej kolejności, i co to jest zespół, który musi zostać pozyskiwany.

### <a name="convert"></a>Convert

Migracja z minimalnym przestojem polega na tym, że migrowane Źródło nadal zmienia się, dryfowanie od obiektu docelowego pod względem danych i schematu po przeprowadzeniu migracji jednorazowej. Podczas fazy **synchronizacji danych** należy upewnić się, że wszystkie zmiany w źródle są przechwytywane i stosowane w celu niemal w czasie rzeczywistym. Po sprawdzeniu, czy wszystkie zmiany w źródle zostały zastosowane do obiektu docelowego, można uruchomienie produkcyjne ze źródła do środowiska docelowego.

W tym kroku migracji wystąpi konwersja lub tłumaczenie kodu Oracle + DDLS na PostgreSQL. Narzędzie ora2pg automatycznie eksportuje obiekty Oracle w formacie PostgreSQL. Dla tych generowanych obiektów niektóre nie będą kompilowane w bazie danych PostgreSQL bez ręcznej zmiany.  
Proces analizowania, które elementy wymagają ręcznej interwencji, polega na kompilowaniu plików wygenerowanych przez ora2pg względem bazy danych PostgreSQL, sprawdzając dziennik i wprowadzając niezbędne zmiany, dopóki cała struktura schematu nie będzie zgodna z składnią PostgreSQL.


#### <a name="create-migration-template"></a>Utwórz szablon migracji 

Najpierw zaleca się utworzenie szablonu migracji, który jest dostarczany z ora2pg. Dwie opcje--project_base i--init_project, gdy są używane, wskazują, że musi utworzyć szablon projektu z drzewem roboczym, plikiem konfiguracji i skryptem do eksportowania wszystkich obiektów z bazy danych Oracle. Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją ora2pg](https://ora2pg.darold.net/documentation.html).

   Użyj następującego polecenia: 

   ```
   ora2pg --project_base /app/migration/ --init_project test_project
   
   ora2pg --project_base /app/migration/ --init_project test_project
   ```

Przykładowe dane wyjściowe: 
   
   ```
   Creating project test_project. /app/migration/test_project/ schema/ dblinks/ directories/ functions/ grants/ mviews/ packages/ partitions/ procedures/ sequences/ synonyms/    tables/ tablespaces/ triggers/ types/ views/ sources/ functions/ mviews/ packages/ partitions/ procedures/ triggers/ types/ views/ data/ config/ reports/
   
   Generating generic configuration file
   
   Creating script export_schema.sh to automate all exports.
   
   Creating script import_all.sh to automate all imports.
   ```

Źródło/katalog zawiera kod Oracle, schemat/katalog zawiera kod przyPostgreSQL. Raporty/katalog zawiera raporty HTML z oceną kosztów migracji.


Po utworzeniu struktury projektu zostanie utworzony plik konfiguracji ogólnej. Zdefiniuj połączenie z bazą danych Oracle oraz odpowiednie parametry konfiguracji w konfiguracji.  Zapoznaj się z dokumentacją ora2pg, aby zrozumieć, co można skonfigurować w pliku konfiguracji, oraz jak to zrobić.


#### <a name="export-oracle-objects"></a>Eksportowanie obiektów Oracle

Następnie wyeksportuj obiekty Oracle jako obiekty PostgreSQL, uruchamiając plik export_schema. sh.

   ```
   cd /app/migration/mig_project
   ./export_schema.sh
   
   Run the following command manually:
   
   SET namespace="/app/migration/mig_project"
   
   ora2pg -t DBLINK -p -o dblink.sql -b %namespace%/schema/dblinks -c
   %namespace%/config/ora2pg.conf
   ora2pg -t DIRECTORY -p -o directory.sql -b %namespace%/schema/directories -c
   %namespace%/config/ora2pg.conf
   ora2pg -p -t FUNCTION -o functions2.sql -b %namespace%/schema/functions -c
   %namespace%/config/ora2pg.conf ora2pg -t GRANT -o grants.sql -b %namespace%/schema/grants -c %namespace%/config/ora2pg.conf ora2pg -t MVIEW -o mview.sql -b %namespace%/schema/   mviews -c %namespace%/config/ora2pg.conf
   ora2pg -p -t PACKAGE -o packages.sql
   %namespace%/config/ora2pg.conf -b %namespace%/schema/packages -c
   ora2pg -p -t PARTITION -o partitions.sql %namespace%/config/ora2pg.conf -b %namespace%/schema/partitions -c
   ora2pg -p -t PROCEDURE -o procs.sql
   %namespace%/config/ora2pg.conf -b %namespace%/schema/procedures -c
   ora2pg -t SEQUENCE -o sequences.sql
   %namespace%/config/ora2pg.conf -b %namespace%/schema/sequences -c
   ora2pg -p -t SYNONYM -o synonym.sql -b %namespace%/schema/synonyms -c
   %namespace%/config/ora2pg.conf
   ora2pg -t TABLE -o table.sql -b %namespace%/schema/tables -c %namespace%/config/ora2pg.conf ora2pg -t TABLESPACE -o tablespaces.sql -b %namespace%/schema/tablespaces -c
   %namespace%/config/ora2pg.conf
   ora2pg -p -t TRIGGER -o triggers.sql -b %namespace%/schema/triggers -c
   %namespace%/config/ora2pg.conf ora2pg -p -t TYPE -o types.sql -b %namespace%/schema/types -c %namespace%/config/ora2pg.conf ora2pg -p -t VIEW -o views.sql -b %namespace%/   schema/views -c %namespace%/config/ora2pg.conf
   ```

   Aby wyodrębnić dane, użyj następującego polecenia:

   ```
   ora2pg -t COPY -o data.sql -b %namespace/data -c %namespace/config/ora2pg.conf
   ```

#### <a name="compile-files"></a>Kompiluj pliki

Na koniec Kompiluj wszystkie pliki na serwer Azure Database for PostgreSQL. Teraz można wybrać opcję ładowania plików DDL generowanych ręcznie lub użyć drugiego skryptu import_all. sh, aby zaimportować te pliki w sposób interaktywny.

   ```
   psql -f %namespace%\schema\sequences\sequence.sql -h server1-
   
   server.postgres.database.azure.com -p 5432 -U username@server1-server -d database -l
   
   %namespace%\ schema\sequences\create_sequences.log
   
   psql -f %namespace%\schema\tables\table.sql -h server1-server.postgres.database.azure.com p 5432 -U username@server1-server -d database -l    %namespace%\schema\tables\create_table.log
   ```

   Polecenie importu danych:

   ```
   psql -f %namespace%\data\table1.sql -h server1-server.postgres.database.azure.com -p 5432 -U username@server1-server -d database -l %namespace%\data\table1.log
   
   psql -f %namespace%\data\table2.sql -h server1-server.postgres.database.azure.com -p 5432 -U username@server1-server -d database -l %namespace%\data\table2.log
   ```

Podczas kompilowania plików Sprawdź dzienniki i popraw wymagane składnie, których ora2pg nie mógł wykonać konwersji z pola.

Aby uzyskać pomoc techniczną dotyczącą problemów, zapoznaj się z oficjalnym dokumentem firmy [Oracle w celu Azure Database for PostgreSQL obejść migracji](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20Database%20for%20PostgreSQL%20Migration%20Workarounds.pdf) .

## <a name="migrate"></a>Migrate 

Po spełnieniu wymaganych wymagań wstępnych i ukończeniu zadań skojarzonych z etapem **sprzed migracji** można przystąpić do wykonania migracji schematu i danych.

### <a name="migrate-schema-and-data"></a>Migrowanie schematu i danych

Po zastosowaniu poprawek stabilna kompilacja bazy danych jest gotowa do wdrożenia.

W tym momencie wszystkie wymagane jest wykonanie poleceń importu *PSQL* , wskazujących na pliki zawierające zmodyfikowany kod w celu skompilowania obiektów bazy danych względem bazy danych PostgreSQL i zaimportowania danych.

W tym kroku można zaimplementować pewien poziom równoległości importowania danych.

### <a name="data-sync-and-cutover"></a>Synchronizacja danych i uruchomienie produkcyjne

W przypadku migracji w trybie online (z minimalnym przestojem) migrowane źródło jest nadal zmieniane, dryfowanie względem obiektu docelowego pod względem danych i schematu po przeprowadzeniu migracji jednokrotnej. Podczas fazy **synchronizacji danych** należy upewnić się, że wszystkie zmiany w źródle są przechwytywane i stosowane w celu niemal w czasie rzeczywistym. Po sprawdzeniu, czy wszystkie zmiany w źródle zostały zastosowane do obiektu docelowego, można uruchomienie produkcyjne ze źródła do środowiska docelowego.

Od 2019 marca, jeśli chcesz przeprowadzić migrację online, rozważ użycie replikacji Attunity na potrzeby migracji firmy Microsoft lub Striim.

W przypadku migracji *różnicowej/przyrostowej* przy użyciu ora2pg Technika polega na zastosowaniu dla każdej tabeli zapytania, która stosuje filtr (Wytnij) według daty lub godziny itd., i po zakończeniu migracji, stosując drugie zapytanie, które przeprowadzi migrację reszty danych.

W tabeli dane źródłowe najpierw Migruj wszystkie dane historyczne. Przykład:

```
select * from table1 where filter_data < 01/01/2019
```

Możesz badać zmiany wprowadzone od początkowej migracji, uruchamiając polecenie podobne do poniższego:

```
select * from table1 where filter_data >= 01/01/2019
```

W tym przypadku zaleca się, aby Walidacja była ulepszona przez sprawdzanie parzystości danych po obu stronach, źródłowym i docelowym.

## <a name="post-migration"></a>Po migracji 

Po pomyślnym zakończeniu etapu **migracji** należy przejść przez serię zadań po migracji, aby upewnić się, że wszystko działa jak najszybciej i efektywnie.

### <a name="remediate-applications"></a>Koryguj aplikacje

Po przeprowadzeniu migracji danych do środowiska docelowego wszystkie aplikacje, które wcześniej korzystały ze źródła, muszą zacząć zużywać miejsce docelowe. W niektórych przypadkach będzie wymagane wprowadzenie zmian w aplikacjach.

### <a name="perform-tests"></a>Wykonaj testy

Po przeprowadzeniu migracji danych do lokalizacji docelowej wykonaj testy dotyczące baz danych, aby sprawdzić, czy aplikacje działają prawidłowo po migracji.

Aby zagwarantować, że źródło i cel są prawidłowo migrowane, Uruchom skrypty sprawdzania poprawności danych ręcznie dla źródłowej bazy danych Oracle i PostgreSQL.

W idealnym przypadku, jeśli źródłowa i docelowa baza danych mają ścieżkę sieciową, ora2pg powinny być używane do sprawdzania poprawności danych. Za pomocą akcji TEST można sprawdzić, czy wszystkie obiekty z bazy danych programu Oracle zostały utworzone w obszarze PostgreSQL. Uruchom polecenie, jak pokazano poniżej:

```
ora2pg -t TEST -c config/ora2pg.conf > migration_diff.txt
```

### <a name="optimize"></a>Optymalizacja

Faza po migracji jest kluczowa dla uzgadniania wszelkich problemów z dokładnością danych i weryfikowania kompletności, a także do rozwiązywania problemów z wydajnością w ramach obciążenia.

## <a name="migration-assets"></a>Zasoby migracji 

Aby uzyskać dodatkową pomoc dotyczącą wykonywania tego scenariusza migracji, zobacz następujące zasoby, które zostały opracowane w ramach obsługi projektu migracji rzeczywistej.

| **Link do tytułu** | **Opis**    |
| -------------- | ------------------ |
| [PostgreSQL migracji oprogramowania Oracle na platformę Azure Cookbook](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf) | Celem tego dokumentu jest zapewnienie architektów, konsultantów, przetwarzający i powiązanych ról z przewodnikiem umożliwiającym szybkie Migrowanie obciążeń z programu Oracle do Azure Database for PostgreSQL przy użyciu narzędzia ora2pg. |
| [Obejścia dotyczące migracji z programu Oracle do platformy Azure PostgreSQL](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20Database%20for%20PostgreSQL%20Migration%20Workarounds.pdf) | Celem tego dokumentu jest zapewnienie architektów, konsultantów, przetwarzający i powiązanych ról z przewodnikiem dotyczącym szybkiego rozwiązywania problemów i pracy z nimi w trakcie migrowania obciążeń z programu Oracle do Azure Database for PostgreSQL. |
| [Kroki instalacji ora2pg w systemie Windows lub Linux](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Windows%20and%20Linux.pdf)                       | Ten dokument jest przeznaczony do użycia jako krótki przewodnik po instalacji w celu włączenia migracji danych & schematu z programu Oracle do Azure Database for PostgreSQL przy użyciu narzędzia ora2pg w systemie Windows lub Linux. Pełne szczegóły dotyczące narzędzia można znaleźć pod adresem http://ora2pg.darold.net/documentation.html . |

Zespół inżynierów danych SQL Data opracował te zasoby. Podstawowa karta tego zespołu ma odblokować i przyspieszyć kompleksową modernizację projektów migracji platformy danych do platformy danych platformy Microsoft Azure.


### <a name="contact-support"></a>Kontakt z pomocą techniczną

Jeśli potrzebujesz pomocy dotyczącej migracji poza narzędzia ora2pg, skontaktuj się z aliasem [ @Ask usługi Azure DB dla PostgreSQL](mailto:AskAzureDBforPostgreSQL@service.microsoft.com) , aby uzyskać informacje o innych opcjach migracji.

## <a name="next-steps"></a>Następne kroki

- W przypadku macierzy usług/narzędzi firmy Microsoft i innych firm dostępnych w celu ułatwienia pracy z różnymi scenariuszami migracji danych i danych (oraz zadaniami specjalistycznymi) zapoznaj się z artykułem [Usługa i narzędzia do migracji danych](https://docs.microsoft.com/azure/dms/dms-tools-matrix).

Aby dowiedzieć się więcej, zobacz: 
- [Dokumentacja usługi Azure Database for PostgreSQL](https://docs.microsoft.com/azure/postgresql/)
- [Dokumentacja ora2pg](https://ora2pg.darold.net/documentation.html)
- [Witryna sieci Web PostgreSQL](https://www.postgresql.org/)
- [Obsługa transakcji autonomicznych w programie PostgreSQL](http://blog.dalibo.com/2016/08/19/Autonoumous_transactions_support_in_PostgreSQL.html) 

Dla zawartości wideo: 
- [Przegląd podróży migracji i narzędzi/usług zalecanych do przeprowadzania oceny i migracji](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/).