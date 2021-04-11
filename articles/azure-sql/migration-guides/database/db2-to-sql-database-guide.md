---
title: 'DB2 do Azure SQL Database: Przewodnik migracji'
description: W tym przewodniku nauczysz się migrować bazy danych IMB DB2 do Azure SQL Database przy użyciu Asystent migracji do programu SQL Server dla programu DB2 (ASYSTENCIE migracji for DB2).
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: f20af8c61bbfbbbbc20c29470648c3df6a272396
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107285500"
---
# <a name="migration-guide-ibm-db2-to-azure-sql-database"></a>Przewodnik migracji: IBM DB2 do Azure SQL Database
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

W tym przewodniku dowiesz się, [jak migrować](https://azure.microsoft.com/migration/migration-journey) bazy danych programu IBM Db2 do Azure SQL Database przy użyciu SQL Server asystenta [migracji](https://azure.microsoft.com/en-us/migration/sql-server/) dla programu DB2. 

Aby poznać inne przewodniki dotyczące migracji, zobacz [przewodniki dotyczące migracji bazy danych Azure](https://docs.microsoft.com/data-migration). 

## <a name="prerequisites"></a>Wymagania wstępne 

Aby przeprowadzić migrację bazy danych DB2 do SQL Database, potrzebne są:

- Aby sprawdzić, czy [środowisko źródłowe jest obsługiwane](/sql/ssma/db2/installing-ssma-for-db2-client-db2tosql#prerequisites).
- Aby pobrać [Asystent migracji do programu SQL Server (Asystencie migracji) dla bazy danych DB2](https://www.microsoft.com/download/details.aspx?id=54254).
- Docelowa baza danych w [Azure SQL Database](../../database/single-database-create-quickstart.md).
- Łączność i wystarczające uprawnienia dostępu do źródła i celu. 

## <a name="pre-migration"></a>Przed migracją

Po spełnieniu wymagań wstępnych można przystąpić do odnajdywania topologii środowiska i oceny wykonalności [migracji w chmurze platformy Azure](https://azure.microsoft.com/migration).

### <a name="assess-and-convert"></a>Oceń i Konwertuj

Za pomocą ASYSTENCIE migracji dla programu DB2 można przeglądać obiekty i dane bazy danych oraz oceniać bazy danych do migracji. 

Aby utworzyć ocenę, wykonaj następujące kroki:

1. Otwórz [Asystencie migracji dla bazy danych DB2](https://www.microsoft.com/download/details.aspx?id=54254). 
1. Wybierz pozycję **plik**  >  **Nowy projekt**. 
1. Podaj nazwę projektu i lokalizację, w której ma być zapisany projekt. Następnie wybierz Azure SQL Database jako cel migracji z listy rozwijanej, a następnie wybierz **przycisk OK**.

   :::image type="content" source="media/db2-to-sql-database-guide/new-project.png" alt-text="Zrzut ekranu pokazujący szczegóły projektu do określenia.":::


1. W obszarze **Połącz z bazą danych DB2** wprowadź wartości w polu Szczegóły połączenia z bazą danych DB2. 

   :::image type="content" source="media/db2-to-sql-database-guide/connect-to-db2.png" alt-text="Zrzut ekranu przedstawiający Opcje łączenia się z wystąpieniem bazy danych DB2.":::


1. Kliknij prawym przyciskiem myszy schemat DB2, który chcesz zmigrować, a następnie wybierz polecenie **Utwórz raport**. Spowoduje to wygenerowanie raportu HTML. Alternatywnie możesz wybrać opcję **Utwórz raport** na pasku nawigacyjnym po zaznaczeniu schematu.

   :::image type="content" source="media/db2-to-sql-database-guide/create-report.png" alt-text="Zrzut ekranu pokazujący sposób tworzenia raportu.":::

1. Przejrzyj raport HTML, aby poznać statystyki konwersji oraz błędy lub ostrzeżenia. Możesz również otworzyć raport w programie Excel, aby uzyskać spis obiektów DB2 i nakład pracy wymagany do przeprowadzenia konwersji schematu. Domyślna lokalizacja raportu znajduje się w folderze raportów w *SSMAProjects*.

   Na przykład: `drive:\<username>\Documents\SSMAProjects\MyDb2Migration\report\report_<date>`. 

   :::image type="content" source="media/db2-to-sql-database-guide/report.png" alt-text="Zrzut ekranu przedstawiający raport, który służy do identyfikowania błędów lub ostrzeżeń.":::


### <a name="validate-data-types"></a>Sprawdzanie poprawności typów danych

Sprawdź poprawność domyślnych mapowań typów danych i w razie potrzeby zmień je na podstawie wymagań. W tym celu wykonaj następujące czynności: 

1. Wybierz pozycję **Narzędzia** z menu. 
1. Wybierz pozycję **Ustawienia projektu**. 
1. Wybierz kartę **mapowania typu** .

   :::image type="content" source="media/db2-to-sql-database-guide/type-mapping.png" alt-text="Zrzut ekranu pokazujący wybór schematu i mapowania typu.":::

1. Można zmienić mapowanie typu dla każdej tabeli, wybierając tabelę w **Eksploratorze metadanych programu DB2**. 

### <a name="convert-schema"></a>Konwertuj schemat

Aby przekonwertować schemat, wykonaj następujące kroki:

1. Obowiązkowe Dodaj zapytania dynamiczne lub ad-hoc do instrukcji. Kliknij prawym przyciskiem myszy węzeł, a następnie wybierz polecenie **Dodaj instrukcje**. 
1. Wybierz pozycję **Połącz z Azure SQL Database**. 
    1. Wprowadź szczegóły połączenia, aby połączyć swoją bazę danych w Azure SQL Database.
    1. Wybierz SQL Database docelowy z listy rozwijanej lub podaj nową nazwę, w takim przypadku baza danych zostanie utworzona na serwerze docelowym. 
    1. Podaj szczegóły uwierzytelniania. 
    1. Wybierz pozycję **Połącz**.

   :::image type="content" source="media/db2-to-sql-database-guide/connect-to-sql-database.png" alt-text="Zrzut ekranu pokazujący szczegóły, które są konieczne do nawiązania połączenia z serwerem logicznym na platformie Azure.":::


1. Kliknij prawym przyciskiem myszy schemat, a następnie wybierz polecenie **Konwertuj schemat**. Alternatywnie możesz wybrać opcję **Konwertuj schemat** z górnego paska nawigacyjnego po zaznaczeniu schematu.

   :::image type="content" source="media/db2-to-sql-database-guide/convert-schema.png" alt-text="Zrzut ekranu, który pokazuje Wybieranie schematu i konwertowanie go.":::

1. Po zakończeniu konwersji Porównaj i przejrzyj strukturę schematu, aby zidentyfikować potencjalne problemy. Rozwiązywanie problemów na podstawie zaleceń.

   :::image type="content" source="media/db2-to-sql-database-guide/compare-review-schema-structure.png" alt-text="Zrzut ekranu pokazujący porównanie i przeglądanie struktury schematu w celu zidentyfikowania potencjalnych problemów.":::

1. W okienku **danych wyjściowych** wybierz pozycję **Przejrzyj wyniki**. W okienku **Lista błędów** Przejrzyj błędy. 
1. Zapisz projekt lokalnie dla ćwiczenia korygowania schematu w trybie offline. Z menu **plik** wybierz polecenie **Zapisz projekt**. Dzięki temu można oszacować schematy źródłowe i docelowe w trybie offline oraz przeprowadzić korygowanie, zanim będzie można opublikować schemat w SQL Database.

## <a name="migrate"></a>Migrate

Po zakończeniu oceniania baz danych i rozwiązaniu jakichkolwiek rozbieżności następnym krokiem jest wykonanie procesu migracji.

Aby opublikować schemat i przeprowadzić migrację danych, wykonaj następujące kroki:

1. Opublikuj schemat. W **Eksploratorze metadanych Azure SQL Database** w węźle **bazy danych** kliknij prawym przyciskiem myszy bazę danych. Następnie wybierz pozycję **Synchronizuj z bazą danych**.

   :::image type="content" source="media/db2-to-sql-database-guide/synchronize-with-database.png" alt-text="Zrzut ekranu przedstawiający opcję synchronizowania z bazą danych.":::

1. Migruj dane. Kliknij prawym przyciskiem myszy bazę danych lub obiekt, który chcesz zmigrować w **Eksploratorze metadanych programu DB2**, a następnie wybierz polecenie **Migruj dane**. Alternatywnie możesz wybrać opcję **Migruj dane** z paska nawigacyjnego. Aby migrować dane dla całej bazy danych, zaznacz pole wyboru obok nazwy bazy danych. Aby przeprowadzić migrację danych z pojedynczych tabel, rozwiń bazę danych, rozwiń węzeł **tabele**, a następnie zaznacz pole wyboru obok tabeli. Aby pominąć dane z poszczególnych tabel, wyczyść to pole wyboru.

   :::image type="content" source="media/db2-to-sql-database-guide/migrate-data.png" alt-text="Zrzut ekranu pokazujący wybór schematu i wybór migracji danych.":::

1. Podaj szczegóły połączenia dla bazy danych DB2 i Azure SQL Database. 
1. Po zakończeniu migracji Wyświetl **raport dotyczący migracji danych**.  

   :::image type="content" source="media/db2-to-sql-database-guide/data-migration-report.png" alt-text="Zrzut ekranu pokazujący, gdzie przejrzeć raport dotyczący migracji danych.":::

1. Nawiąż połączenie z bazą danych w Azure SQL Database przy użyciu [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms). Sprawdź poprawność migracji, przeglądając dane i schemat.

   :::image type="content" source="media/db2-to-sql-database-guide/compare-schema-in-ssms.png" alt-text="Zrzut ekranu pokazujący Porównanie schematu w SQL Server Management Studio.":::

## <a name="post-migration"></a>Po migracji 

Po zakończeniu migracji należy przejść przez serię zadań po migracji, aby upewnić się, że wszystko działa jak najszybciej i efektywnie.

### <a name="remediate-applications"></a>Koryguj aplikacje 

Po przeprowadzeniu migracji danych do środowiska docelowego wszystkie aplikacje, które wcześniej korzystały ze źródła, muszą zacząć zużywać miejsce docelowe. W niektórych przypadkach będzie wymagane wprowadzenie zmian w aplikacjach.

### <a name="perform-tests"></a>Wykonaj testy

Testowanie obejmuje następujące działania:

1. **Opracowywanie testów weryfikacyjnych**: Aby przetestować migrację bazy danych, należy użyć zapytań SQL. Należy utworzyć zapytania walidacji do uruchomienia względem źródłowej i docelowej bazy danych. Zapytania weryfikacyjne powinny obejmować zdefiniowany zakres.
1. **Konfigurowanie środowiska testowego**: środowisko testowe powinno zawierać kopię źródłowej bazy danych i docelowej bazy danych. Należy pamiętać o odizolowaniu środowiska testowego.
1. **Uruchom testy weryfikacyjne**: Uruchom testy weryfikacyjne względem źródła i celu, a następnie Przeanalizuj wyniki.
1. **Uruchom testy wydajnościowe**: Uruchom testy wydajności względem źródła i celu, a następnie Przeanalizuj i Porównaj wyniki.

## <a name="advanced-features"></a>Funkcje zaawansowane 

Pamiętaj, aby korzystać z zaawansowanych funkcji opartych na chmurze oferowanych przez SQL Database, takich jak [wbudowana wysoka dostępność](../../database/high-availability-sla.md), [wykrywanie zagrożeń](../../database/azure-defender-for-sql.md)i [monitorowanie i dostrajanie obciążenia](../../database/monitor-tune-overview.md). 

Niektóre funkcje SQL Server są dostępne tylko wtedy, gdy [poziom zgodności bazy danych](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database) zostanie zmieniony na najnowszy poziom zgodności. 

## <a name="migration-assets"></a>Zasoby migracji 

Aby uzyskać dodatkową pomoc, zobacz następujące zasoby, które zostały opracowane z myślą o obsłudze projektu migracji w rzeczywistości:

|Zasób  |Opis  |
|---------|---------|
|[Model i narzędzie oceny obciążenia danych](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| To narzędzie zapewnia sugerowane Platformy docelowe, gotowość chmury oraz poziom korygowania aplikacji/bazy danych dla danego obciążenia. Oferuje proste, oparte na jednym kliknięcie Obliczanie i generowanie raportów, które ułatwiają przyspieszenie oceny dużych ilości, zapewniając i zautomatyzowany i jednolity proces podejmowania decyzji platformy docelowej.|
|[Pakiet odnajdywania i oceny zasobów danych DB2 zOS](https://github.com/microsoft/DataMigrationTeam/tree/master/DB2%20zOS%20Data%20Assets%20Discovery%20and%20Assessment%20Package)|Po uruchomieniu skryptu SQL w bazie danych można eksportować wyniki do pliku w systemie plików. Obsługiwane są różne formaty plików, w tym *. csv, dzięki czemu można przechwytywać wyniki w zewnętrznych narzędziach, takich jak arkusze kalkulacyjne. Ta metoda może być przydatna, jeśli chcesz łatwo współdzielić wyniki z zespołami, które nie mają zainstalowanego Workbench.|
|[Skrypty i artefakty spisu IBM DB2 LUW](https://github.com/microsoft/DataMigrationTeam/blob/master/IBM%20DB2%20LUW%20Inventory%20Scripts%20and%20Artifacts)|Ten element zawartości obejmuje zapytanie SQL, które trafią z tabeli systemowej programu IBM DB2 LUW w wersji 11,1 i udostępnia liczbę obiektów według schematu i typu obiektu, przybliżone oszacowanie "nieprzetworzonych danych" w każdym schemacie oraz zmianę wielkości tabel w każdym schemacie, z wynikami przechowywanymi w formacie CSV.|
|[Platforma DB2 LUW w czystej skali na platformie Azure — przewodnik konfigurowania](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/DB2%20PureScale%20on%20Azure.pdf)|Ten przewodnik służy jako punkt wyjścia dla planu implementacji programu DB2. Chociaż wymagania biznesowe różnią się w zależności od tego, stosuje się ten sam wzorzec podstawowy. Ten wzorzec architektoniczny może być również używany w przypadku aplikacji OLAP na platformie Azure.|

Zespół inżynierów danych SQL Data opracował te zasoby. Podstawowa karta tego zespołu ma odblokować i przyspieszyć kompleksową modernizację projektów migracji platformy danych do platformy danych platformy Microsoft Azure.



## <a name="next-steps"></a>Następne kroki

- W przypadku usług i narzędzi firmy Microsoft i innych firm, które pomagają w różnych scenariuszach migracji bazy danych i danych, można znaleźć w temacie [Usługa i narzędzia do migracji danych](../../../dms/dms-tools-matrix.md).

- Aby dowiedzieć się więcej na temat Azure SQL Database, zobacz:
   - [Omówienie SQL Database](../../database/sql-database-paas-overview.md)
   - [Kalkulator całkowitego kosztu posiadania na platformę Azure](https://azure.microsoft.com/pricing/tco/calculator/) 

- Aby dowiedzieć się więcej na temat cyklu i wdrożenia migracji do chmury, zobacz:
   -  [Przewodnik Cloud Adoption Framework dla platformy Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Najlepsze rozwiązania dotyczące określania kosztów i rozmiarów obciążeń migrowanych na platformę Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs)
   -  [Zasoby Migracja do chmury](https://azure.microsoft.com/migration/resources) 

- Aby ocenić warstwę dostępu do aplikacji, zobacz [zestaw narzędzi do migracji dostępu do danych](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit).
- Aby uzyskać szczegółowe informacje na temat przeprowadzania testów warstwy A/B dostępu do danych, zobacz [Asystent eksperymentowania z bazą danych](/sql/dea/database-experimentation-assistant-overview).
