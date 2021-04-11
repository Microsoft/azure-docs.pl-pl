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
ms.openlocfilehash: 931528ec415cabde8e862db17b9f8f26502f6788
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105968938"
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

PostgreSQL jest jednym z najbardziej zaawansowanych baz danych typu "open source". W tym artykule opisano sposób użycia bezpłatnego narzędzia ora2pg do migrowania bazy danych Oracle do programu PostgreSQL. Można użyć ora2pg do migracji bazy danych Oracle lub bazy danych MySQL do schematu zgodnego z PostgreSQL. 

Narzędzie ora2pg nawiązuje połączenie z bazą danych Oracle, skanuje ją automatycznie i wyodrębnia jej strukturę lub dane. Następnie ora2pg generuje skrypty SQL, które można załadować do bazy danych PostgreSQL. Można użyć ora2pg do wykonywania zadań, takich jak odtwarzanie bazy danych Oracle Database, migrowanie ogromnej bazy danych przedsiębiorstwa lub po prostu replikowanie niektórych danych Oracle do bazy danych PostgreSQL. Narzędzie jest łatwe w użyciu i nie wymaga znajomości bazy danych Oracle, poza możliwością zapewnienia parametrów wymaganych do nawiązania połączenia z bazą danych programu Oracle.

> [!NOTE]
> Aby uzyskać więcej informacji na temat korzystania z najnowszej wersji programu ora2pg, zapoznaj się z [dokumentacją ora2pg](https://ora2pg.darold.net/documentation.html).

### <a name="typical-ora2pg-migration-architecture"></a>Typowa architektura migracji ora2pg

![Zrzut ekranu architektury migracji ora2pg.](media/howto-migrate-from-oracle/ora2pg-migration-architecture.png)

Po udostępnieniu maszyny wirtualnej i Azure Database for PostgreSQL potrzebne są dwie konfiguracje umożliwiające włączenie łączności między nimi: **Zezwalaj na dostęp do usług platformy Azure** i **Wymuszaj połączenie SSL**: 

- Blok **zabezpieczeń połączeń** > **Zezwalanie na dostęp do usług platformy Azure**  >  **na**

- Blok **zabezpieczeń połączeń** > **Ustawienia protokołu SSL**  >  **Wymuś wyłączenie połączenia SSL**  >  

### <a name="recommendations"></a>Zalecenia

- Aby poprawić wydajność operacji oceny lub eksportu na serwerze Oracle, Zbierz statystyki:

   ```
   BEGIN
   
     DBMS_STATS.GATHER_SCHEMA_STATS
     DBMS_STATS.GATHER_DATABASE_STATS
     DBMS_STATS.GATHER_DICTIONARY_STATS
     END;
   ```

- Eksportuj dane przy użyciu `COPY` polecenia zamiast `INSERT` .

- Unikaj eksportowania tabel z użyciem ich kluczy obcych (FKs), ograniczeń i indeksów. Te elementy spowalniają proces importowania danych do PostgreSQL.

- Utwórz widoki z materiałami przy użyciu *klauzuli No Data*. Następnie Odśwież widoki później.

- Jeśli to możliwe, użyj unikatowych indeksów w widokach z materiałami. Te indeksy mogą przyspieszyć odświeżanie przy użyciu składni `REFRESH MATERIALIZED VIEW CONCURRENTLY` .


## <a name="premigration"></a>Premigracja 

Po sprawdzeniu, czy środowisko źródłowe jest obsługiwane i czy zostały spełnione wszystkie wymagania wstępne, możesz rozpocząć etap premigracji. Aby rozpocząć: 

1. Sporządź spis baz danych, które należy zmigrować. 
1. Oceń te bazy danych pod kątem potencjalnych problemów z migracją lub zablokowanych.
1. Rozwiąż wszystkie elementy, które nie zostały pokryte. 
 
W przypadku migracji heterogenicznych, takich jak Oracle do Azure Database for PostgreSQL, ten etap obejmuje również tworzenie źródłowych schematów bazy danych zgodnych ze środowiskiem docelowym.

### <a name="discover"></a>Odnajdywanie

Celem fazy odnajdywania jest zidentyfikowanie istniejących źródeł danych i szczegółowych informacji o używanych funkcjach. Ta faza ułatwia zrozumienie i zaplanowanie migracji. Proces polega na skanowaniu sieci w celu zidentyfikowania wszystkich wystąpień programu Oracle w organizacji wraz z używaną wersją i funkcjami.

Skrypty preoceny firmy Microsoft dla programu Oracle działają w bazie danych Oracle. Skrypty oceny przedniej badają metadane programu Oracle. Skrypty zapewniają:

- Spis bazy danych, w tym zliczenia obiektów według schematu, typu i stanu.
- Przybliżone oszacowanie danych pierwotnych w każdym schemacie w oparciu o dane statystyczne.
- Rozmiar tabel w każdym schemacie.
- Liczba wierszy kodu na pakiet, funkcję, procedurę i tak dalej.

Pobierz powiązane skrypty z [witryny sieci Web ora2pg](https://ora2pg.darold.net/).

### <a name="assess"></a>Ocena

Po przeprowadzeniu spisu baz danych programu Oracle będziesz mieć pomysł dotyczący rozmiaru bazy danych i potencjalnych wyzwań. Następnym krokiem jest uruchomienie oceny.

Oszacowanie kosztów migracji z programu Oracle do PostgreSQL nie jest proste. Aby ocenić koszt migracji, ora2pg sprawdza wszystkie obiekty bazy danych, funkcje i procedury składowane dla obiektów i kodu PL/SQL, które nie mogą być automatycznie konwertowane.

Narzędzie ora2pg ma tryb analizy zawartości, który sprawdza bazę danych Oracle w celu wygenerowania raportu tekstowego. Raport opisuje, co zawiera baza danych Oracle, i czego nie można wyeksportować.

Aby aktywować tryb *analizy i raportu* , użyj wyeksportowanego typu, `SHOW_REPORT` jak pokazano w następującym poleceniu:

```
ora2pg -t SHOW_REPORT
```

Narzędzie ora2pg umożliwia konwertowanie kodu SQL i PL/SQL ze składni Oracle na PostgreSQL. Po przeanalizowaniu bazy danych ora2pg może oszacować problemy związane z kodem i czas potrzebny do przeprowadzenia migracji pełnej bazy danych.

Aby oszacować koszt migracji w dniach ludzkich, ora2pg umożliwia użycie dyrektywy konfiguracyjnej o nazwie `ESTIMATE_COST` . Tę dyrektywę można również włączyć w wierszu polecenia:

```
ora2pg -t SHOW_REPORT --estimate_cost
```

Domyślna jednostka migracji reprezentuje około pięciu minut dla eksperta PostgreSQL. Jeśli migracja jest w pierwszej kolejności, można zwiększyć domyślną jednostkę migracji za pomocą dyrektywy konfiguracyjnej `COST_UNIT_VALUE` lub `--cost_unit_value` opcji wiersza polecenia.

Ostatni wiersz raportu przedstawia całkowity szacowany kod migracji w dniach ludzkich. Oszacowanie następuje po liczbie jednostek migracji szacowanych dla każdego obiektu.

Ta jednostka migracji reprezentuje około pięciu minut dla eksperta PostgreSQL. Jeśli migracja jest w pierwszej kolejności, można zwiększyć wartość domyślną przy użyciu dyrektywy konfiguracyjnej `COST_UNIT_VALUE` lub opcji wiersza polecenia `--cost_unit_value` . 

W poniższym przykładzie kodu zobaczysz niektóre odmiany oceny: 
* Ocena tabel
* Ocena kolumn
* Ocena schematu korzystająca z domyślnej jednostki kosztu wynoszącej 5 minut
* Ocena schematu korzystająca z jednostki kosztowej wynoszącej 10 minut

```
ora2pg -t SHOW_TABLE -c c:\ora2pg\ora2pg_hr.conf > c:\ts303\hr_migration\reports\tables.txt ora2pg -t SHOW_COLUMN -c c:\ora2pg\ora2pg_hr.conf > c:\ts303\hr_migration\reports\columns.txt
ora2pg -t SHOW_REPORT -c c:\ora2pg\ora2pg_hr.conf --dump_as_html --estimate_cost > c:\ts303\hr
_migration\reports\report.html
ora2pg -t SHOW_REPORT -c c:\ora2pg\ora2pg_hr.conf –-cost_unit_value 10 --dump_as_html --estimate_cost > c:\ts303\hr_migration\reports\report2.html
```

Oto dane wyjściowe oceny schematu na poziomie migracji B-5:

* Poziomy migracji:

  * A — migracja, która może być uruchamiana automatycznie
    
  * B — migracja z kodem do ponownego zapisu i kosztem ludzkim do 5 dni
    
  * C — migracja z kodem ponowne zapisywanie i kosztem ludzkim przez 5 dni
    
* Poziomy techniczne:

   * 1 = uproszczone: brak przechowywanych funkcji i wyzwalaczy

   * 2 = łatwo: brak przechowywanych funkcji, ale wyzwalacze; bez ręcznego zapisywania

   * 3 = proste: przechowywane funkcje i/lub wyzwalacze; bez ręcznego zapisywania

   * 4 = ręczne: brak przechowywanych funkcji, ale wyzwalacze lub widoki z ponownym pisaniem kodu

   * 5 = trudne: przechowywane funkcje i/lub wyzwalacze z zapisaniem kodu

Ocena składa się z: 
* Litera (A lub B) określająca, czy migracja wymaga ręcznego zapisywania.

* Liczba z przedziału od 1 do 5 wskazująca trudności techniczne. 

Inna opcja, `-human_days_limit` określa limit liczby dni ludzkich. W tym miejscu ustaw poziom migracji na C, aby wskazać, że migracja wymaga dużej ilości pracy, pełnego zarządzania projektami i obsługi migracji. Wartość domyślna to 10 dni. Możesz użyć dyrektywy konfiguracji, `HUMAN_DAYS_LIMIT` Aby trwale zmienić tę wartość domyślną.

Ta ocena schematu została opracowana w celu ułatwienia użytkownikom zdecydowania, która baza danych powinna zostać poddana migracji w pierwszej kolejności i które zespoły mają być rozprowadzone.

### <a name="convert"></a>Convert

W przypadku migracji z minimalnym przestojem zmiany źródła migracji są zmieniane. Jest to dryf z elementu docelowego pod względem danych i schematu po przeprowadzeniu migracji jednorazowej. W fazie *synchronizacji danych* upewnij się, że wszystkie zmiany w źródle są przechwytywane i stosowane w celu niemal w czasie rzeczywistym. Po sprawdzeniu, czy wszystkie zmiany są stosowane do obiektu docelowego, można *wyciąć* ze źródła do środowiska docelowego.

W tym kroku migracji kod Oracle i skrypty języka DDL są konwertowane lub tłumaczone na PostgreSQL. Narzędzie ora2pg automatycznie eksportuje obiekty Oracle w formacie PostgreSQL. Niektóre z wygenerowanych obiektów nie mogą być kompilowane w bazie danych PostgreSQL bez ręcznej zmiany.  

Aby zrozumieć, które elementy wymagają ręcznej interwencji, należy najpierw skompilować pliki wygenerowane przez ora2pg względem bazy danych PostgreSQL. Sprawdź dziennik, a następnie wprowadź wszelkie niezbędne zmiany, dopóki struktura schematu nie będzie zgodna ze składnią PostgreSQL.


#### <a name="create-a-migration-template"></a>Tworzenie szablonu migracji 

Zalecamy użycie szablonu migracji, który ora2pg zapewnia. Korzystając z opcji `--project_base` i `--init_project` , ora2pg tworzy szablon projektu z drzewem roboczym, plikiem konfiguracji i skryptem do eksportowania wszystkich obiektów z bazy danych programu Oracle. Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją ora2pg](https://ora2pg.darold.net/documentation.html).

Użyj następującego polecenia: 

```
ora2pg --project_base /app/migration/ --init_project test_project

ora2pg --project_base /app/migration/ --init_project test_project
```

Oto przykładowe dane wyjściowe: 
   
```
Creating project test_project. /app/migration/test_project/ schema/ dblinks/ directories/ functions/ grants/ mviews/ packages/ partitions/ procedures/ sequences/ synonyms/    tables/ tablespaces/ triggers/ types/ views/ sources/ functions/ mviews/ packages/ partitions/ procedures/ triggers/ types/ views/ data/ config/ reports/

Generating generic configuration file

Creating script export_schema.sh to automate all exports.

Creating script import_all.sh to automate all imports.
```

`sources/`Katalog zawiera kod firmy Oracle. `schema/`Katalog zawiera kod port do PostgreSQL. `reports/`Katalog zawiera raporty HTML i ocenę kosztów migracji.


Po utworzeniu struktury projektu zostanie utworzony plik konfiguracji ogólnej. Zdefiniuj połączenie z bazą danych Oracle i odpowiednie parametry konfiguracji w pliku konfiguracji. Aby uzyskać więcej informacji na temat pliku konfiguracji, zapoznaj się z [dokumentacją ora2pg](https://ora2pg.darold.net/documentation.html).


#### <a name="export-oracle-objects"></a>Eksportowanie obiektów Oracle

Następnie wyeksportuj obiekty Oracle jako obiekty PostgreSQL, uruchamiając plik *export_schema. sh*.

```
cd /app/migration/mig_project
./export_schema.sh
```

Uruchom ręcznie następujące polecenie.

```
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

Aby wyodrębnić dane, użyj następującego polecenia.

```
ora2pg -t COPY -o data.sql -b %namespace/data -c %namespace/config/ora2pg.conf
```

#### <a name="compile-files"></a>Kompiluj pliki

Na koniec Skompiluj wszystkie pliki na serwerze Azure Database for PostgreSQL. Możesz załadować ręcznie wygenerowane pliki DDL lub użyć drugiego skryptu *import_all. sh* , aby zaimportować te pliki w sposób interaktywny.

```
psql -f %namespace%\schema\sequences\sequence.sql -h server1-

server.postgres.database.azure.com -p 5432 -U username@server1-server -d database -l

%namespace%\ schema\sequences\create_sequences.log

psql -f %namespace%\schema\tables\table.sql -h server1-server.postgres.database.azure.com p 5432 -U username@server1-server -d database -l    %namespace%\schema\tables\create_table.log
```

Oto polecenie importowania danych:

```
psql -f %namespace%\data\table1.sql -h server1-server.postgres.database.azure.com -p 5432 -U username@server1-server -d database -l %namespace%\data\table1.log

psql -f %namespace%\data\table2.sql -h server1-server.postgres.database.azure.com -p 5432 -U username@server1-server -d database -l %namespace%\data\table2.log
```

Podczas kompilowania plików Sprawdź dzienniki i popraw każdą składnię, która ora2pg nie mogła samodzielnie wykonać konwersji.

Aby uzyskać więcej informacji, zobacz [Obejścia dotyczące migracji z programu Oracle do Azure Database for PostgreSQL](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20Database%20for%20PostgreSQL%20Migration%20Workarounds.pdf).

## <a name="migrate"></a>Migrate 

Po spełnieniu wymagań wstępnych i wykonaniu wstępne kroki migracji można uruchomić schemat i migrację danych.

### <a name="migrate-schema-and-data"></a>Migrowanie schematu i danych

Po wprowadzeniu niezbędnych poprawek stabilna kompilacja bazy danych jest gotowa do wdrożenia. Uruchom `psql` polecenia importowania, wskazując pliki, które zawierają zmodyfikowany kod. To zadanie kompiluje obiekty bazy danych względem bazy danych PostgreSQL i importuje dane.

W tym kroku można zaimplementować poziom równoległości przy importowaniu danych.

### <a name="sync-data-and-cut-over"></a>Synchronizuj dane i obcinaj

W przypadku migracji w trybie online (z minimalnym przestojem) Źródło migracji nadal zmienia się. Jest to dryf z elementu docelowego pod względem danych i schematu po przeprowadzeniu migracji jednorazowej. 

W fazie *synchronizacji danych* upewnij się, że wszystkie zmiany w źródle są przechwytywane i stosowane w celu niemal w czasie rzeczywistym. Po sprawdzeniu, czy wszystkie zmiany są stosowane, można wyciąć ze źródła do środowiska docelowego.

Aby przeprowadzić migrację online, skontaktuj się z AskAzureDBforPostgreSQL@service.microsoft.com pomocą techniczną.

W przypadku migracji *różnicowej/przyrostowej* , która używa ora2pg, dla każdej tabeli należy użyć zapytania, które filtruje (*wycięcia*) według daty, godziny lub innego parametru. Następnie Zakończ migrację, używając drugiego zapytania, które migruje pozostałe dane.

W tabeli dane źródłowe najpierw Migruj wszystkie dane historyczne. Oto przykład:

```
select * from table1 where filter_data < 01/01/2019
```

Możesz badać zmiany od czasu migracji początkowej, uruchamiając polecenie podobne do tego:

```
select * from table1 where filter_data >= 01/01/2019
```

W tym przypadku zalecamy podwyższenie poziomu walidacji, sprawdzając parzystość danych po obu stronach, źródłowym i docelowym.

## <a name="postmigration"></a>Postmigration 

Po zakończeniu *migracji* wykonaj zadania postmigration, aby upewnić się, że wszystko działa jak najszybciej i efektywnie.

### <a name="remediate-applications"></a>Koryguj aplikacje

Po przeprowadzeniu migracji danych do środowiska docelowego wszystkie aplikacje, które wcześniej korzystały ze źródła, muszą zacząć zużywać miejsce docelowe. Instalator czasami wymaga wprowadzenia zmian w aplikacjach.

### <a name="test"></a>Testowanie

Po przeprowadzeniu migracji danych do obiektu docelowego Uruchom testy względem baz danych, aby sprawdzić, czy aplikacje działają prawidłowo z elementem docelowym. Upewnij się, że źródło i cel zostały prawidłowo zmigrowane przez uruchomienie skryptów ręcznego sprawdzania poprawności danych dla źródła Oracle i docelowych baz danych PostgreSQL.

W idealnym przypadku, jeśli źródłowa i docelowa baza danych mają ścieżkę sieciową, ora2pg powinny być używane do sprawdzania poprawności danych. Możesz użyć akcji, `TEST` Aby upewnić się, że wszystkie obiekty z bazy danych programu Oracle zostały utworzone w PostgreSQL. 

Uruchom następujące polecenie:

```
ora2pg -t TEST -c config/ora2pg.conf > migration_diff.txt
```

### <a name="optimize"></a>Optymalizacja

Faza postmigrationa jest kluczowa dla uzgadniania problemów z dokładnością danych i weryfikowania kompletności. W tej fazie są również rozwiązywane problemy z wydajnością związane z obciążeniem.

## <a name="migration-assets"></a>Zasoby migracji 

Aby uzyskać więcej informacji na temat tego scenariusza migracji, zobacz następujące zasoby. Obsługują one rzeczywiste zaangażowanie projektu migracji.

| Zasób | Opis    |
| -------------- | ------------------ |
| [PostgreSQL migracji oprogramowania Oracle na platformę Azure Cookbook](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf) | Ten dokument ułatwia architektom, konsultantom, administratorom baz danych i pokrewnym rolom szybkie Migrowanie obciążeń z programu Oracle do Azure Database for PostgreSQL przy użyciu ora2pg. |
| [Obejścia dotyczące migracji z programu Oracle do platformy Azure PostgreSQL](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20Database%20for%20PostgreSQL%20Migration%20Workarounds.pdf) | Ten dokument pomaga architektom, konsultantom, administratorom baz danych i powiązanym rolom szybko rozwiązywać problemy lub obejść je podczas migrowania obciążeń z programu Oracle do Azure Database for PostgreSQL. |
| [Kroki instalacji ora2pg w systemie Windows lub Linux](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Windows%20and%20Linux.pdf)                       | Ten dokument zawiera krótki przewodnik instalacji migracji schematu i danych z programu Oracle do Azure Database for PostgreSQL przy użyciu ora2pg w systemie Windows lub Linux. Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją ora2pg](http://ora2pg.darold.net/documentation.html). |

Zespół inżynierów danych SQL Data opracował te zasoby. Podstawowa karta tego zespołu ma na celu odblokowanie i przyspieszenie złożonej modernizacji projektów migracji platformy danych do platformy danych Microsoft Azure.

## <a name="more-support"></a>Więcej pomocy

Aby uzyskać pomoc dotyczącą migracji poza zakresem narzędzi ora2pg, należy skontaktować się z [ @Ask usługą Azure DB dla PostgreSQL](mailto:AskAzureDBforPostgreSQL@service.microsoft.com).

## <a name="next-steps"></a>Następne kroki

Macierz usług i narzędzi do migracji baz danych i danych oraz zadań specjalnych można znaleźć w temacie [usługi i narzędzia do migracji danych](https://docs.microsoft.com/azure/dms/dms-tools-matrix).

Dokumentacja: 
- [Dokumentacja usługi Azure Database for PostgreSQL](https://docs.microsoft.com/azure/postgresql/)
- [Dokumentacja ora2pg](https://ora2pg.darold.net/documentation.html)
- [Witryna sieci Web PostgreSQL](https://www.postgresql.org/)
- [Obsługa transakcji autonomicznych w programie PostgreSQL](http://blog.dalibo.com/2016/08/19/Autonoumous_transactions_support_in_PostgreSQL.html) 
