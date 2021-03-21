---
title: 'Oracle do SQL Database: Przewodnik migracji'
description: W tym przewodniku nauczysz się migrować schemat Oracle do Azure SQL Database przy użyciu Asystent migracji do programu SQL Server for Oracle (ASYSTENCIE migracji for Oracle).
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.date: 08/25/2020
ms.openlocfilehash: f00740de5a327858fd250a0cb561b07c32f3b726
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104655575"
---
# <a name="migration-guide-oracle-to-azure-sql-database"></a>Przewodnik migracji: Oracle do Azure SQL Database
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

W tym przewodniku pokazano, jak migrować schematy programu Oracle do Azure SQL Database przy użyciu Asystent migracji do programu SQL Server dla programu Oracle.

W przypadku innych przewodników migracji zapoznaj się z tematem [migracja bazy danych](https://datamigration.microsoft.com/). 

## <a name="prerequisites"></a>Wymagania wstępne

Aby migrować schemat Oracle do SQL Database są potrzebne: 

- Aby sprawdzić, czy środowisko źródłowe jest obsługiwane. 
- Aby pobrać [Asystent migracji do programu SQL Server (Asystencie migracji) dla programu Oracle](https://www.microsoft.com/en-us/download/details.aspx?id=54258). 
- [Azure SQL Database](../../database/single-database-create-quickstart.md)docelowy. 
- Odpowiednie [uprawnienia dla Asystencie migracji dla programu Oracle](/sql/ssma/oracle/connecting-to-oracle-database-oracletosql) i [dostawcy](/sql/ssma/oracle/connect-to-oracle-oracletosql).
 

## <a name="pre-migration"></a>Przed migracją

Po spełnieniu wymagań wstępnych można przystąpić do odnajdywania topologii środowiska i oceny wykonalności migracji. Ta część procesu obejmuje przeprowadzenie spisu baz danych, które należy zmigrować, ocenianie tych baz danych pod kątem potencjalnych problemów z migracją lub blokowaniem, a następnie rozwiązanie wszystkich elementów, które mogły zostać odkryte.



### <a name="assess"></a>Ocena 


Użyj Asystent migracji do programu SQL Server (ASYSTENCIE migracji) dla programu Oracle do przeglądania obiektów i danych bazy danych, oceniania baz danych na potrzeby migracji, migrowania obiektów bazy danych do Azure SQL Database, a następnie na koniec migracji danych do bazy danych. 


Aby utworzyć ocenę, wykonaj następujące kroki: 


1. Otwórz [Asystent migracji do programu SQL Server dla programu Oracle](https://www.microsoft.com/en-us/download/details.aspx?id=54258). 
1. Wybierz pozycję **plik** , a następnie wybierz pozycję **Nowy projekt**. 
1. Podaj nazwę projektu, lokalizację do zapisania projektu, a następnie wybierz Azure SQL Database jako cel migracji z listy rozwijanej. Wybierz przycisk **OK**.

   ![Nowy projekt](./media/oracle-to-sql-database-guide/new-project.png)


1. Wybierz pozycję **Połącz z bazą danych Oracle**. Wprowadź wartości w polach szczegóły połączenia Oracle w oknie dialogowym **łączenie z bazą danych Oracle** .

   ![Łączenie z bazą danych Oracle](./media/oracle-to-sql-database-guide/connect-to-oracle.png)

   Wybierz schematy programu Oracle, które chcesz zmigrować: 

   ![Wybierz schemat Oracle](./media/oracle-to-sql-database-guide/select-schema.png)

1. Kliknij prawym przyciskiem myszy schemat Oracle, który chcesz zmigrować, w **Eksploratorze metadanych Oracle**, a następnie wybierz polecenie **Utwórz raport**. Spowoduje to wygenerowanie raportu HTML. Alternatywnie możesz wybrać opcję **Utwórz raport** na pasku nawigacyjnym po wybraniu bazy danych.

   ![Utwórz raport](./media/oracle-to-sql-database-guide/create-report.png)

1. Przejrzyj raport HTML, aby poznać statystyki konwersji oraz błędy lub ostrzeżenia. Możesz również otworzyć raport w programie Excel, aby uzyskać spis obiektów Oracle i nakład pracy wymagany do przeprowadzenia konwersji schematu. Domyślna lokalizacja raportu znajduje się w folderze raportów w SSMAProjects.

   Na przykład: `drive:\<username>\Documents\SSMAProjects\MyOracleMigration\report\report_2020_11_12T02_47_55\`

   ![Raport z oceny](./media/oracle-to-sql-database-guide/assessment-report.png) 



### <a name="validate-data-types"></a>Sprawdzanie poprawności typów danych

Sprawdź poprawność domyślnych mapowań typu danych i zmień je w zależności od wymagań, jeśli jest to konieczne. W tym celu wykonaj następujące czynności:

1. Wybierz pozycję **Narzędzia** z menu. 
1. Wybierz pozycję **Ustawienia projektu**. 
1. Wybierz kartę **mapowania typu** . 

   ![Mapowania typów](./media/oracle-to-sql-database-guide/type-mappings.png)

1. Można zmienić mapowanie typu dla każdej tabeli, wybierając tabelę w **Eksploratorze metadanych Oracle**.

### <a name="convert-schema"></a>Konwertuj schemat

Aby przekonwertować schemat, wykonaj następujące kroki: 

1. Obowiązkowe Dodaj zapytania dynamiczne lub ad-hoc do instrukcji. Kliknij prawym przyciskiem myszy węzeł, a następnie wybierz polecenie **Dodaj instrukcje**.
1. Wybierz pozycję **Połącz z Azure SQL Database**. 
    1. Wprowadź szczegóły połączenia, aby połączyć swoją bazę danych w Azure SQL Database.
    1. Wybierz SQL Database docelowy z listy rozwijanej.
    1. Wybierz pozycję **Połącz**.

    ![Łączenie z usługą SQL Database](./media/oracle-to-sql-database-guide/connect-to-sql-database.png)


1. Kliknij prawym przyciskiem myszy schemat Oracle w **Eksploratorze metadanych Oracle** , a następnie wybierz polecenie **Konwertuj schemat**. Alternatywnie możesz wybrać opcję **Konwertuj schemat** z górnego paska nawigacyjnego po zaznaczeniu schematu.

   ![Konwertuj schemat](./media/oracle-to-sql-database-guide/convert-schema.png)

1. Po zakończeniu konwersji Porównaj i przejrzyj przekonwertowane obiekty do oryginalnych obiektów, aby zidentyfikować potencjalne problemy i rozwiązać je na podstawie zaleceń.

   ![Zapoznaj się ze schematem zaleceń](./media/oracle-to-sql-database-guide/table-mapping.png)

   Porównaj przekonwertowany tekst języka Transact-SQL z oryginalnymi procedurami składowanymi i zapoznaj się z zaleceniami. 

   ![Zapoznaj się z zaleceniami](./media/oracle-to-sql-database-guide/procedure-comparison.png)

1. Zapisz projekt lokalnie dla ćwiczenia korygowania schematu w trybie offline. Wybierz pozycję **Zapisz projekt** z menu **plik** .

## <a name="migrate"></a>Migrate

Po zakończeniu oceniania baz danych i rozwiązaniu jakichkolwiek rozbieżności następnym krokiem jest wykonanie procesu migracji. Migracja obejmuje dwa kroki — opublikowanie schematu i Migrowanie danych. 

Aby opublikować schemat i przeprowadzić migrację danych, wykonaj następujące kroki:

1. Opublikuj schemat: kliknij prawym przyciskiem myszy bazę danych w węźle **bazy danych** w **Eksploratorze metadanych Azure SQL Database** i wybierz polecenie **Synchronizuj z bazą danych**.

   ![Synchronizuj z bazą danych](./media/oracle-to-sql-database-guide/synchronize-with-database.png)

   Przejrzyj mapowanie między projektem źródłowym a obiektem docelowym:

   ![Synchronizuj z przeglądem bazy danych](./media/oracle-to-sql-database-guide/synchronize-with-database-review.png)


1. Migruj dane: kliknij prawym przyciskiem myszy schemat w **Eksploratorze metadanych Oracle** i wybierz polecenie **Migruj dane**. Alternatywnie możesz wybrać opcję **Migruj dane** z górnego paska nawigacyjnego po zaznaczeniu schematu. 

   ![Migrowanie danych](./media/oracle-to-sql-database-guide/migrate-data.png)

1. Podaj szczegóły połączenia zarówno dla programu Oracle, jak i Azure SQL Database.
1. Wyświetl **raport dotyczący migracji danych**.

   ![Raport dotyczący migracji danych](./media/oracle-to-sql-database-guide/data-migration-report.png)

1. Połącz się z Azure SQL Database przy użyciu [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) i sprawdź poprawność migracji, przeglądając dane i schemat.

   ![Weryfikuj w ASYSTENCIE migracji](./media/oracle-to-sql-database-guide/validate-data.png)

Możesz również użyć SQL Server Integration Services (SSIS) do przeprowadzenia migracji. Aby dowiedzieć się więcej, zobacz: 

- [Asystent migracji do programu SQL Server: jak oceniać i migrować dane z platform danych innych niż firmy Microsoft do SQL Server](https://blogs.msdn.microsoft.com/datamigration/2016/11/16/sql-server-migration-assistant-how-to-assess-and-migrate-databases-from-non-microsoft-data-platforms-to-sql-server/)
- [Wprowadzenie z SQL Server Integration Services](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services)
- [SQL Server Integration Services: SSIS na platformie Azure i hybrydowe przenoszenie danych](https://download.microsoft.com/download/D/2/0/D20E1C5F-72EA-4505-9F26-FEF9550EFD44/SSIS%20Hybrid%20and%20Azure.docx)


## <a name="post-migration"></a>Po migracji 

Po pomyślnym zakończeniu etapu **migracji** należy przejść przez serię zadań po migracji, aby upewnić się, że wszystko działa jak najszybciej i efektywnie.

### <a name="remediate-applications"></a>Koryguj aplikacje

Po przeprowadzeniu migracji danych do środowiska docelowego wszystkie aplikacje, które wcześniej korzystały ze źródła, muszą zacząć zużywać miejsce docelowe. W niektórych przypadkach będzie wymagane wprowadzenie zmian w aplikacjach.

[Zestaw narzędzi do migracji dostępu do danych](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) to rozszerzenie Visual Studio Code, które pozwala analizować kod źródłowy Java oraz wykrywać wywołania interfejsu API i zapytania dotyczące dostępu do danych, dostarczając widok z jednym okienkiem, co należy rozwiązać, aby zapewnić obsługę nowego zaplecza bazy danych. Aby dowiedzieć się więcej, zobacz blog [Migrowanie naszej aplikacji Java z firmy Oracle](https://techcommunity.microsoft.com/t5/microsoft-data-migration/migrate-your-java-applications-from-oracle-to-sql-server-with/ba-p/368727) . 



### <a name="perform-tests"></a>Wykonaj testy

Podejście testowe do migracji bazy danych obejmuje wykonywanie następujących czynności:

1.  **Opracowuj testy weryfikacyjne**. Aby przetestować migrację bazy danych, należy użyć zapytań SQL. Należy utworzyć zapytania walidacji do uruchomienia względem źródłowej i docelowej bazy danych. Zapytania weryfikacyjne powinny obejmować zdefiniowany zakres.

2.  **Konfigurowanie środowiska testowego**. Środowisko testowe powinno zawierać kopię źródłowej bazy danych i docelowej bazy danych. Należy pamiętać o odizolowaniu środowiska testowego.

3.  **Uruchom testy weryfikacyjne**. Uruchom testy weryfikacyjne względem źródła i celu, a następnie Przeanalizuj wyniki.

4.  **Uruchom testy wydajnościowe**. Uruchom test wydajności na źródłowym i docelowym, a następnie Przeanalizuj i Porównaj wyniki.


### <a name="optimize"></a>Optymalizacja

Faza po migracji jest kluczowa dla uzgadniania wszelkich problemów z dokładnością danych i weryfikowania kompletności, a także do rozwiązywania problemów z wydajnością w ramach obciążenia.

> [!NOTE]
> Aby uzyskać dodatkowe informacje o tych problemach i konkretnych krokach, które należy rozwiązać, zobacz [Przewodnik po weryfikacji i optymalizacji po migracji](/sql/relational-databases/post-migration-validation-and-optimization-guide).


## <a name="migration-assets"></a>Zasoby migracji 

Aby uzyskać dodatkową pomoc dotyczącą wykonywania tego scenariusza migracji, zobacz następujące zasoby, które zostały opracowane w ramach obsługi projektu migracji rzeczywistej.

| **Tytuł/link**                                                                                                                                          | **Opis**                                                                                                                                                                                                                                                                                                                                                                                       |
| ------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Model i narzędzie oceny obciążenia danych](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | To narzędzie zapewnia sugerowane Platformy docelowe, gotowość chmury oraz poziom korygowania aplikacji/bazy danych dla danego obciążenia. Oferuje proste, oparte na jednym kliknięcie Obliczanie i generowanie raportów, które znacznie ułatwiają przyspieszenie oceny dużych ilości, zapewniając i zautomatyzowany i jednolity proces podejmowania decyzji platformy docelowej.                                                          |
| [Artefakty skryptu spisu firmy Oracle](https://github.com/Microsoft/DataMigrationTeam/tree/master/Oracle%20Inventory%20Script%20Artifacts)                 | Ten element zawartości zawiera zapytanie PL/SQL, które trafi w tabele systemowe Oracle i udostępnia liczbę obiektów według typu schematu, typu obiektu i stanu. Zapewnia także przybliżone oszacowanie "nieprzetworzonych danych" w każdym schemacie oraz zmianę rozmiarów tabel w każdym schemacie, z wynikami przechowywanymi w formacie CSV.                                                                                                               |
| [Automatyzowanie & konsolidowania danych oceny rozwiązań firmy Oracle ASYSTENCIE migracji](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Automate%20SSMA%20Oracle%20Assessment%20Collection%20%26%20Consolidation)                                             | Ten zestaw zasobów używa pliku CSV jako wpisu (sources.csv w folderach projektu) do tworzenia plików XML, które są konieczne do uruchamiania oceny ASYSTENCIE migracji w trybie konsoli. source.csv jest udostępniana przez klienta w oparciu o spis istniejących wystąpień programu Oracle. Pliki wyjściowe to AssessmentReportGeneration_source_1.xml, ServersConnectionFile.xml i VariableValueFile.xml.|
| [ASYSTENCIE migracji dla typowych błędów Oracle i sposoby ich naprawiania](https://aka.ms/dmj-wp-ssma-oracle-errors)                                                           | Za pomocą programu Oracle można przypisać warunek nieskalarny w klauzuli WHERE. Jednak SQL Server nie obsługuje tego typu warunku. W związku z tym Asystent migracji do programu SQL Server (ASYSTENCIE migracji) dla programu Oracle nie konwertuje zapytań z warunkiem nieskalarnym w klauzuli WHERE zamiast generowania błędu O2SS0001. Ten oficjalny dokument zawiera więcej informacji o problemie i sposobach jego rozwiązania.          |
| [Podręcznik migracji firmy Oracle do SQL Server](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20SQL%20Server%20Migration%20Handbook.pdf)                | Ten dokument koncentruje się na zadaniach skojarzonych z migrowaniem schematu programu Oracle do najnowszej wersji programu SQL Server Base. Jeśli migracja wymaga zmiany funkcji/funkcjonalności, można uważnie rozważyć ewentualne konsekwencje każdej zmiany w aplikacjach korzystających z bazy danych.                                                     |

Te zasoby zostały opracowane w ramach programu SQL Data ninja, który jest sponsorowany przez zespół inżynierów grupy danych platformy Azure. Podstawowa karta programu SQL Data Ninja to odblokowanie i przyspieszenie złożonej modernizacji i konkurowania możliwości migracji platformy danych na platformę danych platformy Microsoft Azure. Jeśli uważasz, że Twoja organizacja będzie chciała uczestniczyć w programie SQL Data ninja, skontaktuj się z zespołem ds. kont i poproś o przesłanie nominacji.

## <a name="next-steps"></a>Następne kroki

- Macierz usług i narzędzi firmy Microsoft i innych firm, które są dostępne w celu ułatwienia pracy z różnymi scenariuszami bazy danych i migracji danych oraz zadaniami specjalistycznymi, można znaleźć w artykule [Usługa i narzędzia do migracji danych](https://docs.microsoft.com/azure/dms/dms-tools-matrix).

- Aby dowiedzieć się więcej na temat Azure SQL Database, zobacz: 
  - [Omówienie Azure SQL Database](../../database/sql-database-paas-overview.md)
  - [Kalkulator całkowitego kosztu posiadania (TCO) na platformie Azure](https://azure.microsoft.com/en-us/pricing/tco/calculator/)


- Aby dowiedzieć się więcej na temat platformy i cyklu wdrażania migracji w chmurze, zobacz
   -  [Przewodnik Cloud Adoption Framework dla platformy Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Najlepsze rozwiązania związane z wyceną i ustalaniem wielkości obciążeń migracji na platformę Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Aby uzyskać zawartość wideo, zobacz: 
    - [Przegląd podróży migracji i narzędzi/usług zalecanych do przeprowadzania oceny i migracji](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)


