---
title: 'Oracle to Azure SQL Database: Migration guide (Oracle do Azure SQL Database: Przewodnik migracji)'
description: Z tego przewodnika dowiesz się, jak przeprowadzić migrację schematu Oracle do programu Azure SQL Database przy użyciu programu Asystent migracji do programu SQL Server for Oracle.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: MashaMSFT
ms.author: mathoma
ms.date: 08/25/2020
ms.openlocfilehash: 45fbc1f85c5d7f66716fbf69deb430ce74575435
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388503"
---
# <a name="migration-guide-oracle-to-azure-sql-database"></a>Przewodnik migracji: Oracle do Azure SQL Database

[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

Z tego przewodnika [](https://azure.microsoft.com/migration/migration-journey) dowiesz się, jak przeprowadzić migrację schematów oracle do programu Azure SQL Database przy użyciu programu [SQL Server Migration](https://azure.microsoft.com/en-us/migration/sql-server/) Assistant for Oracle (SSMA for Oracle).

Aby uzyskać inne przewodniki dotyczące migracji, zobacz [Przewodniki po migracji bazy danych platformy Azure.](https://docs.microsoft.com/data-migration)

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem migracji schematu Oracle do programu SQL Database:

- Sprawdź, czy środowisko źródłowe jest obsługiwane.
- Pobierz [program SSMA for Oracle.](https://www.microsoft.com/download/details.aspx?id=54258)
- Mieć docelowe [SQL Database](../../database/single-database-create-quickstart.md) wystąpienie.
- Uzyskaj niezbędne [uprawnienia dla usługi SSMA dla programu Oracle](/sql/ssma/oracle/connecting-to-oracle-database-oracletosql) i [dostawcy](/sql/ssma/oracle/connect-to-oracle-oracletosql).
 
## <a name="pre-migration"></a>Przed migracją

Po spełnianiu wymagań wstępnych możesz wykryć topologię środowiska i ocenić możliwości migracji do [chmury platformy Azure.](https://azure.microsoft.com/migration) Ta część procesu obejmuje przeprowadzenie spisu baz danych, które należy zmigrować, ocenę tych baz danych pod uwagę potencjalnych problemów z migracją lub blokad, a następnie rozwiązanie wszelkich niepokrytych elementów.

### <a name="assess"></a>Ocena

Korzystając z funkcji SSMA for Oracle, można przeglądać obiekty i dane bazy danych, oceniać bazy danych do migracji, migrować obiekty baz danych do programu SQL Database, a na koniec migrować dane do bazy danych.

Aby utworzyć ocenę:

1. Otwórz [program SSMA for Oracle](https://www.microsoft.com/download/details.aspx?id=54258).
1. Wybierz **pozycję File**(Plik), a następnie wybierz pozycję New Project **(Nowy projekt).**
1. Wprowadź nazwę projektu i lokalizację do zapisania projektu. Następnie wybierz **Azure SQL Database** jako miejsce docelowe migracji z listy rozwijanej i wybierz przycisk **OK.**

   ![Zrzut ekranu przedstawiający nawiązywanie połączenia z bazą danych Oracle.](./media/oracle-to-sql-database-guide/connect-to-oracle.png)

1. Wybierz pozycję **Połącz z bazą danych Oracle.** Wprowadź wartości szczegółów połączenia Oracle w **oknie dialogowym Łączenie z bazą** danych Oracle.

1. Wybierz schematy Oracle, które chcesz zmigrować.

   ![Zrzut ekranu przedstawiający wybieranie schematu Oracle.](./media/oracle-to-sql-database-guide/select-schema.png)

1. W **Eksploratorze metadanych Oracle** kliknij prawym przyciskiem myszy schemat Oracle, który chcesz zmigrować, a następnie wybierz pozycję **Utwórz** raport, aby wygenerować raport HTML. Alternatywnie możesz wybrać bazę danych, a następnie wybrać **kartę Utwórz** raport.

   ![Zrzut ekranu przedstawiający tworzenie raportu.](./media/oracle-to-sql-database-guide/create-report.png)

1. Przejrzyj raport HTML, aby poznać statystyki konwersji i wszelkie błędy lub ostrzeżenia. Możesz również otworzyć raport w programie Excel, aby uzyskać spis obiektów Oracle i nakład pracy wymagany do przeprowadzenia konwersji schematów. Domyślna lokalizacja raportu znajduje się w folderze raportu w ramach SSMAProjects.

   Na przykład zobacz `drive:\<username>\Documents\SSMAProjects\MyOracleMigration\report\report_2020_11_12T02_47_55\`.

   ![Zrzut ekranu przedstawiający raport oceny.](./media/oracle-to-sql-database-guide/assessment-report.png)

### <a name="validate-the-data-types"></a>Weryfikowanie typów danych

W razie potrzeby zweryfikuj domyślne mapowania typów danych i zmień je na podstawie wymagań. W tym celu wykonaj następujące czynności:

1. W programie SSMA for Oracle wybierz pozycję **Narzędzia**, a następnie wybierz **pozycję Project Settings (Ustawienia projektu).**
1. Wybierz **kartę Mapowanie** typów.

   ![Zrzut ekranu przedstawiający mapowanie typów.](./media/oracle-to-sql-database-guide/type-mappings.png)

1. Mapowanie typów dla każdej tabeli można zmienić, wybierając tabelę w **Eksploratorze metadanych Oracle.**

### <a name="convert-the-schema"></a>Konwertowanie schematu

Aby przekonwertować schemat:

1. (Opcjonalnie) Dodawanie zapytań dynamicznych lub ad hoc do instrukcji. Kliknij prawym przyciskiem myszy węzeł, a następnie wybierz **polecenie Dodaj instrukcje**.
1. Wybierz **kartę Połącz z Azure SQL Database** siecią.
    1. W **SQL Database** wprowadź szczegóły połączenia, aby połączyć bazę danych.
    1. Wybierz docelowe SQL Database z listy rozwijanej lub wprowadź nową nazwę— w takim przypadku baza danych zostanie utworzona na serwerze docelowym.
    1. Wprowadź szczegóły uwierzytelniania, a następnie wybierz **pozycję Połącz**.

    ![Zrzut ekranu przedstawiający nawiązywanie połączenia z Azure SQL Database.](./media/oracle-to-sql-database-guide/connect-to-sql-database.png)

1. W **Eksploratorze metadanych Oracle** kliknij prawym przyciskiem myszy schemat Oracle, a następnie wybierz polecenie **Konwertuj schemat**. Alternatywnie możesz wybrać schemat, a następnie wybrać **kartę Konwertuj** schemat.

   ![Zrzut ekranu przedstawiający konwertowanie schematu.](./media/oracle-to-sql-database-guide/convert-schema.png)

1. Po zakończeniu konwersji porównaj i przejrzyj przekonwertowane obiekty na oryginalne obiekty, aby zidentyfikować potencjalne problemy i rozwiązać je na podstawie zaleceń.

   ![Zrzut ekranu przedstawiający schemat Przeglądanie zaleceń.](./media/oracle-to-sql-database-guide/table-mapping.png)

1. Porównaj przekonwertowany tekst transact-SQL z oryginalnymi procedurami składowanym i zapoznaj się z zaleceniami.

   ![Zrzut ekranu przedstawiający rekomendacje dotyczące przeglądu.](./media/oracle-to-sql-database-guide/procedure-comparison.png)

1. W okienku danych wyjściowych wybierz **pozycję Przejrzyj wyniki** i przejrzyj błędy w **okienku Lista** błędów.
1. Zapisz projekt lokalnie w celu skorygowania schematu w trybie offline. W menu **File (Plik)** wybierz pozycję **Save Project (Zapisz projekt).** Ten krok umożliwia ocenę schematów źródłowych i docelowych w trybie offline oraz przeprowadzenie korygowania przed opublikowaniem schematu w SQL Database.

## <a name="migrate"></a>Migrate

Po ocenie baz danych i wylieniu wszelkich niezgodności następnym krokiem jest uruchomienie procesu migracji. Migracja obejmuje dwa kroki: opublikowanie schematu i migrację danych.

Aby opublikować schemat i przeprowadzić migrację danych:

1. Opublikuj schemat, klikając prawym przyciskiem  myszy bazę  danych w węźle Bazy danych w Eksploratorze metadanych Azure SQL Database i wybierając polecenie **Synchronizuj z bazą danych**.

   ![Zrzut ekranu przedstawiający synchronizowanie z bazą danych.](./media/oracle-to-sql-database-guide/synchronize-with-database.png)

1. Przejrzyj mapowanie między projektem źródłowym i docelowym.

   ![Zrzut ekranu przedstawiający synchronizację z przeglądem bazy danych.](./media/oracle-to-sql-database-guide/synchronize-with-database-review.png)

1. Przemigruj dane, klikając prawym przyciskiem myszy bazę danych lub obiekt, który chcesz migrować, w Eksploratorze metadanych **Oracle** i wybierając **polecenie Migruj dane.** Alternatywnie możesz wybrać kartę **Migruj** dane. Aby przeprowadzić migrację danych dla całej bazy danych, zaznacz pole wyboru obok nazwy bazy danych. Aby przeprowadzić migrację danych z poszczególnych tabel, rozwiń bazę danych, rozwiń pozycję **Tabele**, a następnie zaznacz pola wyboru obok tabel. Aby pominąć dane z poszczególnych tabel, wyczyść pola wyboru.

   ![Zrzut ekranu przedstawiający migrację danych.](./media/oracle-to-sql-database-guide/migrate-data.png)

1. Wprowadź szczegóły połączenia dla programu Oracle i SQL Database.
1. Po zakończeniu migracji wyświetl raport **migracji danych**.

   ![Zrzut ekranu przedstawiający raport migracji danych.](./media/oracle-to-sql-database-guide/data-migration-report.png)

1. Połącz się z wystąpieniem SQL Database przy [użyciu SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms), a następnie zweryfikuj migrację, przeglądając dane i schemat.

   ![Zrzut ekranu przedstawiający weryfikację w SQL Server Management Studio.](./media/oracle-to-sql-database-guide/validate-data.png)

Alternatywnie można również użyć SQL Server Integration Services do przeprowadzenia migracji. Aby dowiedzieć się więcej, zobacz:

- [Wprowadzenie do SQL Server Integration Services](/sql/integration-services/sql-server-integration-services)
- [SQL Server Integration Services platformy Azure i hybrydowego ruchu danych](https://download.microsoft.com/download/D/2/0/D20E1C5F-72EA-4505-9F26-FEF9550EFD44/SSIS%20Hybrid%20and%20Azure.docx)

## <a name="post-migration"></a>Po migracji

Po pomyślnym ukończeniu  etapu migracji należy wykonać serię zadań po migracji, aby upewnić się, że wszystko działa tak bezproblemowo i wydajnie, jak to możliwe.

### <a name="remediate-applications"></a>Korygowanie aplikacji

Po migracji danych do środowiska docelowego wszystkie aplikacje, które wcześniej zużywały źródło, muszą zacząć korzystać z obiektu docelowego. W niektórych przypadkach osiągnięcie tego zadania będzie wymagało zmian w aplikacjach.

Rozszerzenie [Data Access Migration Toolkit](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) to rozszerzenie interfejsu Visual Studio Code, które umożliwia analizowanie kodu źródłowego Java i wykrywanie wywołań i zapytań interfejsu API dostępu do danych. Zestaw narzędzi udostępnia widok z jednym okienkiem, co należy rozwiązać w celu obsługi nowego serwera końcowego bazy danych. Aby dowiedzieć się więcej, zobacz wpis w blogu [Migrate your Java applications from Oracle (Migrowanie](https://techcommunity.microsoft.com/t5/microsoft-data-migration/migrate-your-java-applications-from-oracle-to-sql-server-with/ba-p/368727) aplikacji Java z bazy danych Oracle).

### <a name="perform-tests"></a>Wykonywanie testów

Testowe podejście do migracji bazy danych obejmuje następujące działania:

1. **Opracowywanie testów weryfikacyjnych:** aby przetestować migrację bazy danych, należy użyć zapytań SQL. Należy utworzyć zapytania weryfikacji, aby uruchamiać je zarówno względem źródłowej, jak i docelowej bazy danych. Zapytania weryfikacji powinny obejmować zdefiniowany zakres.
1. **Konfigurowanie środowiska testowego:** środowisko testowe powinno zawierać kopię źródłowej bazy danych i docelowej bazy danych. Pamiętaj, aby odizolować środowisko testowe.
1. **Uruchamianie testów walidacji:** uruchom testy weryfikacyjne względem źródła i obiektu docelowego, a następnie przeanalizuj wyniki.
1. **Uruchamianie testów wydajnościowych:** uruchom testy wydajnościowe względem źródła i obiektu docelowego, a następnie przeanalizuj i porównaj wyniki.

### <a name="optimize"></a>Optymalizacja

Faza po migracji ma kluczowe znaczenie dla uzgadniania wszelkich problemów z dokładnością danych, weryfikowania kompletności i rozwiązywania problemów z wydajnością obciążenia.

> [!NOTE]
> Aby uzyskać więcej informacji na temat tych problemów i czynności w celu ich ograniczenia, zobacz Przewodnik po [weryfikacji i optymalizacji](/sql/relational-databases/post-migration-validation-and-optimization-guide)po migracji.

## <a name="migration-assets"></a>Zasoby migracji

Aby uzyskać więcej pomocy przy ukończeniu tego scenariusza migracji, zobacz następujące zasoby. Zostały one opracowane w celu wspierania rzeczywistego zaangażowania projektu migracji.

| **Tytuł/link**                                                                                                                                          | **Opis**                                                                                                                                                                                                                                                                                                                                                                                       |
| ------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Model i narzędzie do oceny obciążenia danych](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | To narzędzie zapewnia sugerowane "najlepsze dopasowanie" platform docelowych, gotowość do chmury oraz poziom korygowania aplikacji lub bazy danych dla danego obciążenia. Oferuje ona proste obliczenia i generowanie raportów jednym kliknięciem, które pomagają przyspieszyć ocenę dużych nieruchomości przez zapewnienie zautomatyzowanego i jednolitego procesu podejmowania decyzji na platformie docelowej.                                                          |
| [Artefakty skryptów spisu Oracle](https://github.com/Microsoft/DataMigrationTeam/tree/master/Oracle%20Inventory%20Script%20Artifacts)                 | Ten zasób zawiera zapytanie PL/SQL, które trafia do tabel systemowych Oracle i udostępnia liczbę obiektów według typu schematu, typu obiektu i stanu. Udostępnia również przybliżone oszacowanie danych pierwotnych w każdym schemacie i rozmiarów tabel w każdym schemacie, a wyniki są przechowywane w formacie CSV.                                                                                                               |
| [Automatyzowanie konsolidacji kolekcji oracle assessment & SSMA](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Automate%20SSMA%20Oracle%20Assessment%20Collection%20%26%20Consolidation)                                             | Ten zestaw zasobów używa pliku csv jako wpisu (sources.csv w folderach projektu) do tworzenia plików XML potrzebnych do uruchomienia oceny SSMA w trybie konsoli. Klient source.csv na podstawie spisu istniejących wystąpień oracle. Pliki wyjściowe są AssessmentReportGeneration_source_1.xml, ServersConnectionFile.xml i VariableValueFile.xml.|
| [SSMA for Oracle Common Errors and How to Fix Them](https://aka.ms/dmj-wp-ssma-oracle-errors)                                                           | W programie Oracle można przypisać nieskalarny warunek w klauzuli WHERE. Jednak SQL Server nie obsługuje tego typu warunku. W związku z tym SSMA for Oracle nie konwertuje zapytań z warunkiem nieskalarnych w klauzuli WHERE. Zamiast tego generuje błąd O2SS0001. Ten oficjalny dokument zawiera więcej szczegółów na temat problemu i sposobów jego rozwiązania.          |
| [Oracle to SQL Server Migration Handbook](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20SQL%20Server%20Migration%20Handbook.pdf)                | Ten dokument koncentruje się na zadaniach związanych z migracją schematu Oracle do najnowszej wersji programu SQL Server Database. Jeśli migracja wymaga zmian w funkcjach lub funkcjach, możliwy wpływ każdej zmiany na aplikacje, które korzystają z bazy danych, należy dokładnie rozważyć.                                                     |

Zespół inżynierów ds. danych SQL opracował te zasoby. Podstawowym elementem tego zespołu jest odblokowanie i przyspieszenie złożonej modernizacji projektów migracji platformy danych na platformę danych Microsoft Azure.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać informacje o macierzy usług i narzędzi firmy Microsoft oraz innych firm, które są dostępne w celu pomocy w różnych scenariuszach migracji bazy danych i danych oraz zadaniach specjalnych, zobacz Usługi i narzędzia do [migracji danych.](../../../dms/dms-tools-matrix.md)

- Aby dowiedzieć się więcej o SQL Database, zobacz:
  - [Omówienie Azure SQL Database](../../database/sql-database-paas-overview.md)
  - [Kalkulator całkowitego kosztu posiadania (TCO) platformy Azure](https://azure.microsoft.com/pricing/tco/calculator/)

- Aby dowiedzieć się więcej na temat struktury i cyklu wdrożenia migracji do chmury, zobacz:
   -  [Przewodnik Cloud Adoption Framework dla platformy Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Najlepsze rozwiązania dotyczące kosztów i rozmiarów obciążeń na potrzeby migracji na platformę Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs)
   -  [Migracja do chmury zasobów](https://azure.microsoft.com/migration/resources)

- Aby uzyskać informacje o zawartości wideo, zobacz:
    - [Omówienie podróży migracji oraz narzędzi i usług zalecanych do przeprowadzania oceny i migracji](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)
