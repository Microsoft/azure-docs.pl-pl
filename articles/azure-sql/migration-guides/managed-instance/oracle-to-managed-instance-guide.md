---
title: 'Wystąpienie zarządzane Oracle do usługi Azure SQL: Przewodnik migracji'
description: W tym przewodniku nauczysz się migrować schematy programu Oracle do wystąpienia zarządzanego usługi Azure SQL przy użyciu Asystent migracji do programu SQL Server dla programu Oracle.
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: 4343359e17203fcae538558ebeaa967cfde1540d
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2021
ms.locfileid: "105640506"
---
# <a name="migration-guide-oracle-to-azure-sql-managed-instance"></a>Przewodnik migracji: wystąpienie zarządzane programu Oracle do usługi Azure SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqlmi.md)]

W tym przewodniku nauczysz się migrować schematy programu Oracle do wystąpienia zarządzanego usługi Azure SQL przy użyciu Asystent migracji do programu SQL Server dla programu Oracle. 

W przypadku innych przewodników migracji zapoznaj się z tematem [migracja bazy danych](https://docs.microsoft.com/data-migration). 

## <a name="prerequisites"></a>Wymagania wstępne

Aby migrować schemat Oracle do wystąpienia zarządzanego SQL, potrzebujesz: 

- Aby sprawdzić, czy środowisko źródłowe jest obsługiwane. 
- Aby pobrać [Asystent migracji do programu SQL Server (Asystencie migracji) dla programu Oracle](https://www.microsoft.com/en-us/download/details.aspx?id=54258). 
- Docelowe [wystąpienie zarządzane Azure SQL](../../managed-instance/instance-create-quickstart.md). 
- Odpowiednie [uprawnienia dla Asystencie migracji dla programu Oracle](/sql/ssma/oracle/connecting-to-oracle-database-oracletosql) i [dostawcy](/sql/ssma/oracle/connect-to-oracle-oracletosql).
 

## <a name="pre-migration"></a>Przed migracją

Po spełnieniu wymagań wstępnych można przystąpić do odnajdywania topologii środowiska i oceny wykonalności migracji. Ta część procesu obejmuje przeprowadzenie spisu baz danych, które należy zmigrować, ocenianie tych baz danych pod kątem potencjalnych problemów z migracją lub blokowaniem, a następnie rozwiązanie wszystkich elementów, które mogły zostać odkryte.



### <a name="assess"></a>Ocena 

Użyj Asystent migracji do programu SQL Server (ASYSTENCIE migracji) dla programu Oracle do przeglądania obiektów i danych bazy danych, oceniania baz danych do migracji, migrowania obiektów bazy danych do wystąpienia zarządzanego usługi Azure SQL, a następnie na koniec migracji danych do bazy danych. 

Aby utworzyć ocenę, wykonaj następujące kroki: 


1. Otwórz [Asystent migracji do programu SQL Server dla programu Oracle](https://www.microsoft.com/en-us/download/details.aspx?id=54258). 
1. Wybierz pozycję **plik** , a następnie wybierz pozycję **Nowy projekt**. 
1. Podaj nazwę projektu, lokalizację, w której ma zostać zapisany projekt, a następnie wybierz wystąpienie zarządzane Azure SQL jako miejsce docelowe migracji z listy rozwijanej. Wybierz **przycisk OK**:

   ![Nowy projekt](./media/oracle-to-managed-instance-guide/new-project.png)

1. Wybierz pozycję **Połącz z bazą danych Oracle**. Wprowadź wartości w polach szczegóły połączenia Oracle w oknie dialogowym **łączenie z bazą danych Oracle** :

   ![Łączenie z bazą danych Oracle](./media/oracle-to-managed-instance-guide/connect-to-oracle.png)

   Wybierz schematy programu Oracle, które chcesz zmigrować: 

   ![Wybierz schemat Oracle](./media/oracle-to-managed-instance-guide/select-schema.png)

1. Kliknij prawym przyciskiem myszy schemat Oracle, który chcesz zmigrować, w **Eksploratorze metadanych Oracle**, a następnie wybierz polecenie **Utwórz raport**. Spowoduje to wygenerowanie raportu HTML. Alternatywnie możesz wybrać opcję **Utwórz raport** na pasku nawigacyjnym po wybraniu bazy danych:

   ![Utwórz raport](./media/oracle-to-managed-instance-guide/create-report.png)

1. Przejrzyj raport HTML, aby poznać statystyki konwersji oraz błędy lub ostrzeżenia. Możesz również otworzyć raport w programie Excel, aby uzyskać spis obiektów Oracle i nakład pracy wymagany do przeprowadzenia konwersji schematu. Domyślna lokalizacja raportu znajduje się w folderze raportów w SSMAProjects.

   Na przykład: `drive:\<username>\Documents\SSMAProjects\MyOracleMigration\report\report_2020_11_12T02_47_55\`

   ![Raport z oceny](./media/oracle-to-managed-instance-guide/assessment-report.png)


### <a name="validate-data-types"></a>Sprawdzanie poprawności typów danych

Sprawdź poprawność domyślnych mapowań typu danych i zmień je w zależności od wymagań, jeśli jest to konieczne. W tym celu wykonaj następujące czynności:

1. Wybierz pozycję **Narzędzia** z menu. 
1. Wybierz pozycję **Ustawienia projektu**. 
1. Wybierz kartę **mapowania typów** :

   ![Mapowania typów](./media/oracle-to-managed-instance-guide/type-mappings.png)

1. Można zmienić mapowanie typu dla każdej tabeli, wybierając tabelę w **Eksploratorze metadanych Oracle**.

### <a name="convert-schema"></a>Konwertuj schemat

Aby przekonwertować schemat, wykonaj następujące kroki: 

1. Obowiązkowe Dodaj zapytania dynamiczne lub ad-hoc do instrukcji. Kliknij prawym przyciskiem myszy węzeł, a następnie wybierz polecenie **Dodaj instrukcje**.
1. Wybierz pozycję **Połącz z wystąpieniem zarządzanym usługi Azure SQL**. 
    1. Wprowadź szczegóły połączenia w celu połączenia bazy danych w wystąpieniu zarządzanym usługi Azure SQL.
    1. Wybierz docelową bazę danych z listy rozwijanej lub podaj nową nazwę. w takim przypadku baza danych zostanie utworzona na serwerze docelowym. 
    1. Podaj szczegóły uwierzytelniania. 
    1. Wybierz pozycję **Połącz**:

    ![Łączenie z wystąpieniem zarządzanym SQL](./media/oracle-to-managed-instance-guide/connect-to-sql-managed-instance.png)

1. Kliknij prawym przyciskiem myszy schemat Oracle w **Eksploratorze metadanych Oracle** , a następnie wybierz polecenie **Konwertuj schemat**. Alternatywnie możesz wybrać opcję **Konwertuj schemat** z górnego paska nawigacyjnego po wybraniu schematu:

   ![Konwertuj schemat](./media/oracle-to-managed-instance-guide/convert-schema.png)

1. Po zakończeniu konwersji Porównaj i przejrzyj przekonwertowane obiekty do oryginalnych obiektów, aby zidentyfikować potencjalne problemy i rozwiązać je na podstawie zaleceń:

   ![Porównaj zalecenia dotyczące tabeli](./media/oracle-to-managed-instance-guide/table-comparison.png)

   Porównaj przekonwertowany tekst języka Transact-SQL z oryginalnym kodem i zapoznaj się z zaleceniami: 

   ![Porównaj zalecenia dotyczące procedur](./media/oracle-to-managed-instance-guide/procedure-comparison.png)

1. Wybierz pozycję **przegląd wyników** w okienku dane wyjściowe i Przejrzyj błędy w okienku **Lista błędów** . 
1. Zapisz projekt lokalnie dla ćwiczenia korygowania schematu w trybie offline. Wybierz pozycję **Zapisz projekt** z menu **plik** . Dzięki temu można oszacować schematy źródłowe i docelowe w trybie offline i przeprowadzić korygowanie, zanim będzie można opublikować schemat w wystąpieniu zarządzanym SQL.

## <a name="migrate"></a>Migrate

Po zakończeniu oceniania baz danych i rozwiązaniu jakichkolwiek rozbieżności następnym krokiem jest wykonanie procesu migracji. Migracja obejmuje dwa kroki — opublikowanie schematu i Migrowanie danych. 

Aby opublikować schemat i przeprowadzić migrację danych, wykonaj następujące kroki:

1. Publikuj schemat: kliknij prawym przyciskiem myszy schemat lub obiekt, który chcesz zmigrować w **Eksploratorze metadanych Oracle**, a następnie wybierz polecenie **Migruj dane**. Alternatywnie możesz wybrać opcję **Migruj dane** z górnego paska nawigacyjnego. Aby migrować dane dla całej bazy danych, zaznacz pole wyboru obok nazwy bazy danych. Aby przeprowadzić migrację danych z pojedynczych tabel, rozwiń bazę danych, rozwiń węzeł tabele, a następnie zaznacz pole wyboru obok tabeli. Aby pominąć dane z poszczególnych tabel, wyczyść pole wyboru:

   ![Synchronizuj z bazą danych](./media/oracle-to-managed-instance-guide/synchronize-with-database.png)

   Przejrzyj mapowanie między projektem źródłowym a obiektem docelowym:

   ![Synchronizuj z przeglądem bazy danych](./media/oracle-to-managed-instance-guide/synchronize-with-database-review.png)

1. Migruj dane: kliknij prawym przyciskiem myszy schemat w **Eksploratorze metadanych Oracle** i wybierz polecenie **Migruj dane**. 

   ![Migrowanie danych](./media/oracle-to-managed-instance-guide/migrate-data.png)

1. Podaj szczegóły połączenia zarówno dla programu Oracle, jak i wystąpienia zarządzanego Azure SQL.
1. Po zakończeniu migracji Wyświetl **raport dotyczący migracji danych**:  

   ![Raport dotyczący migracji danych](./media/oracle-to-managed-instance-guide/data-migration-report.png)

1. Połącz się z wystąpieniem zarządzanym usługi Azure SQL przy użyciu [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) i Zweryfikuj migrację, przeglądając dane i schemat:

   ![Weryfikuj w ASYSTENCIE migracji](./media/oracle-to-managed-instance-guide/validate-data.png)


Możesz również użyć SQL Server Integration Services (SSIS) do przeprowadzenia migracji. Aby dowiedzieć się więcej, zobacz: 

- [Wprowadzenie z SQL Server Integration Services](/sql/integration-services/sql-server-integration-services)
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

Zespół inżynierów danych SQL Data opracował te zasoby. Podstawowa karta tego zespołu ma odblokować i przyspieszyć kompleksową modernizację projektów migracji platformy danych do platformy danych platformy Microsoft Azure.

## <a name="next-steps"></a>Następne kroki

- Macierz usług i narzędzi firmy Microsoft i innych firm, które są dostępne w celu ułatwienia pracy z różnymi scenariuszami bazy danych i migracji danych oraz zadaniami specjalistycznymi, można znaleźć w artykule [Usługa i narzędzia do migracji danych](../../../dms/dms-tools-matrix.md).

- Aby dowiedzieć się więcej na temat wystąpienia zarządzanego usługi Azure SQL, zobacz: 
  - [Omówienie wystąpienia zarządzanego usługi Azure SQL](../../managed-instance/sql-managed-instance-paas-overview.md)
  - [Kalkulator całkowitego kosztu posiadania (TCO) na platformie Azure](https://azure.microsoft.com/en-us/pricing/tco/calculator/)


- Aby dowiedzieć się więcej na temat platformy i cyklu wdrażania migracji w chmurze, zobacz
   -  [Podręcznik Cloud Adoption Framework dla platformy Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Najlepsze rozwiązania związane z wyceną i ustalaniem wielkości obciążeń migracji do platformy Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs)

- Aby uzyskać zawartość wideo, zobacz: 
    - [Przegląd podróży migracji i narzędzi/usług zalecanych do przeprowadzania oceny i migracji](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)