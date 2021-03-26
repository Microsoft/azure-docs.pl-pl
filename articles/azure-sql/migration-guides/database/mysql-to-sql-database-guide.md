---
title: 'Baza danych MySQL do Azure SQL Database: Przewodnik migracji'
description: W tym przewodniku nauczysz się migrować bazy danych MySQL do Azure SQL Database przy użyciu Asystent migracji do programu SQL Server for MySQL (ASYSTENCIE migracji for MySQL).
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: 6b8d3afc214f6b78fcc11b3592cd51dadf37bf96
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105564164"
---
# <a name="migration-guide--mysql-to-azure-sql-database"></a>Przewodnik migracji: MySQL do Azure SQL Database
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

W tym przewodniku pokazano, jak migrować bazę danych MySQL do Azure SQL Database przy użyciu Asystent migracji do programu SQL Server for MySQL (ASYSTENCIE migracji for MySQL). 

W przypadku innych przewodników migracji zapoznaj się z tematem [migracja bazy danych](https://docs.microsoft.com/data-migration). 

## <a name="prerequisites"></a>Wymagania wstępne

Aby przeprowadzić migrację bazy danych MySQL do Azure SQL Database, potrzebne są:

- Aby sprawdzić, czy środowisko źródłowe jest obsługiwane. Obecnie obsługiwane są produkty MySQL 5,6 i 5,7. 
- [Asystent migracji do programu SQL Server dla programu MySQL](https://www.microsoft.com/download/details.aspx?id=54257)
- Łączność i wystarczające uprawnienia dostępu do źródła i celu. 


## <a name="pre-migration"></a>Przed migracją 

Po spełnieniu wymagań wstępnych można przystąpić do odnajdywania topologii środowiska i oceny wykonalności migracji.

### <a name="assess"></a>Ocena 

Użyj Asystent migracji do programu SQL Server (ASYSTENCIE migracji) dla programu MySQL, aby przeglądać obiekty bazy danych i dane oraz oceniać bazy danych do migracji. 

Aby utworzyć ocenę, wykonaj następujące czynności: 

1. Otwórz [Asystent migracji do programu SQL Server dla programu MySQL](https://www.microsoft.com/download/details.aspx?id=54257). 
1. Z menu wybierz pozycję **plik** , a następnie wybierz pozycję **Nowy projekt**. 
1. Podaj nazwę projektu, lokalizację do zapisania projektu. Wybierz **Azure SQL Database** jako cel migracji. Wybierz **przycisk OK**:

   ![Nowy projekt](./media/mysql-to-sql-database-guide/new-project.png)

1. Wybierz pozycję **Połącz z bazą danych MySQL** i podaj szczegóły połączenia w celu połączenia z serwerem MySQL:

   ![Łączenie z bazą danych MySQL](./media/mysql-to-sql-database-guide/connect-to-mysql.png)

1. Kliknij prawym przyciskiem myszy schemat MySQL w **Eksploratorze metadanych MySQL** , a następnie wybierz polecenie **Utwórz raport**. Alternatywnie możesz wybrać pozycję **Utwórz raport** na pasku nawigacyjnym najwyższego wiersza:

   ![Utwórz raport](./media/mysql-to-sql-database-guide/create-report.png)

1. Przejrzyj raport HTML, aby poznać statystyki konwersji oraz błędy lub ostrzeżenia. Możesz również otworzyć raport w programie Excel, aby uzyskać spis obiektów MySQL i nakład pracy wymagany do przeprowadzenia konwersji schematu. Domyślna lokalizacja raportu znajduje się w folderze raportów w SSMAProjects.
 
   Na przykład: `drive:\Users\<username>\Documents\SSMAProjects\MySQLMigration\report\report_2016_11_12T02_47_55\`
 
   ![Raport konwersji](./media/mysql-to-sql-database-guide/conversion-report.png)

### <a name="validate-data-types"></a>Sprawdzanie poprawności typów danych

Sprawdź poprawność domyślnych mapowań typu danych i zmień je w zależności od wymagań, jeśli jest to konieczne. W tym celu wykonaj następujące czynności: 

1. Wybierz pozycję **Narzędzia** z menu. 
1. Wybierz pozycję **Ustawienia projektu**. 
1. Wybierz kartę **mapowania typów** :

   ![Mapowania typów](./media/mysql-to-sql-database-guide/type-mappings.png)

1. Można zmienić mapowanie typu dla każdej tabeli, wybierając tabelę w **Eksploratorze metadanych MySQL**. 

### <a name="convert-schema"></a>Konwertuj schemat 

Aby przekonwertować schemat, wykonaj następujące kroki: 

1. Obowiązkowe Aby skonwertować kwerendy dynamiczne lub ad hoc, kliknij prawym przyciskiem myszy węzeł i wybierz polecenie **Dodaj instrukcję**. 
1. Wybierz pozycję **Połącz z Azure SQL Database**. 
    1. Wprowadź szczegóły połączenia, aby połączyć swoją bazę danych w Azure SQL Database.
    1. Wybierz SQL Database docelowy z listy rozwijanej lub podaj nową nazwę, w takim przypadku baza danych zostanie utworzona na serwerze docelowym. 
    1. Podaj szczegóły uwierzytelniania. 
    1. Wybierz pozycję **Połącz**:

   ![Nawiązywanie połączenia z serwerem SQL](./media/mysql-to-sql-database-guide/connect-to-sqldb.png)
 
1. Kliknij prawym przyciskiem myszy schemat i wybierz polecenie **Konwertuj schemat**. Alternatywnie możesz wybrać opcję **Konwertuj schemat** z górnego paska nawigacyjnego po wybraniu bazy danych:

   ![Konwertuj schemat](./media/mysql-to-sql-database-guide/convert-schema.png)

1. Po zakończeniu konwersji Porównaj i przejrzyj przekonwertowane obiekty do oryginalnych obiektów, aby zidentyfikować potencjalne problemy i rozwiązać je na podstawie zaleceń:

   ![Skonwertowane obiekty można porównać ze źródłem](./media/mysql-to-sql-database-guide/table-comparison.png)

   Porównaj przekonwertowany tekst języka Transact-SQL z oryginalnym kodem i zapoznaj się z zaleceniami:

   ![Skonwertowane zapytania można porównać z kodem źródłowym](./media/mysql-to-sql-database-guide/procedure-comparison.png)

1. Wybierz pozycję **przegląd wyników** w okienku dane wyjściowe i Przejrzyj błędy w okienku **Lista błędów** . 
1. Zapisz projekt lokalnie dla ćwiczenia korygowania schematu w trybie offline. Wybierz pozycję **Zapisz projekt** z menu **plik** . Dzięki temu można oszacować schematy źródłowe i docelowe w trybie offline i przeprowadzić korygowanie, zanim będzie można opublikować schemat w SQL Database.



## <a name="migrate"></a>Migrate 

Po zakończeniu oceniania baz danych i rozwiązaniu jakichkolwiek rozbieżności następnym krokiem jest wykonanie procesu migracji. Migracja obejmuje dwa kroki — opublikowanie schematu i Migrowanie danych. 

Aby opublikować schemat i przeprowadzić migrację danych, wykonaj następujące kroki: 

1. Opublikuj schemat: kliknij prawym przyciskiem myszy bazę danych w **Eksploratorze metadanych Azure SQL Database** i wybierz polecenie **Synchronizuj z bazą danych**. Ta akcja publikuje schemat MySQL w Azure SQL Database:

   ![Synchronizuj z bazą danych](./media/mysql-to-sql-database-guide/synchronize-database.png)

   Przejrzyj mapowanie między projektem źródłowym a obiektem docelowym:

   ![Synchronizuj z przeglądem bazy danych](./media/mysql-to-sql-database-guide/synchronize-database-review.png)

1. Migruj dane: kliknij prawym przyciskiem myszy bazę danych lub obiekt, który chcesz zmigrować w **Eksploratorze metadanych MySQL**, a następnie wybierz polecenie **Migruj dane**. Alternatywnie możesz wybrać opcję **Migruj dane** z górnego paska nawigacyjnego. Aby migrować dane dla całej bazy danych, zaznacz pole wyboru obok nazwy bazy danych. Aby przeprowadzić migrację danych z pojedynczych tabel, rozwiń bazę danych, rozwiń węzeł tabele, a następnie zaznacz pole wyboru obok tabeli. Aby pominąć dane z poszczególnych tabel, wyczyść pole wyboru:

   ![Migrowanie danych](./media/mysql-to-sql-database-guide/migrate-data.png)

1. Po zakończeniu migracji Wyświetl raport dotyczący **migracji danych** : 

   ![Raport dotyczący migracji danych](./media/mysql-to-sql-database-guide/data-migration-report.png)

1. Połącz się z Azure SQL Database przy użyciu [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) i sprawdź poprawność migracji, przeglądając dane i schemat:

    ![Weryfikuj w ASYSTENCIE migracji](./media/mysql-to-sql-database-guide/validate-in-ssms.png)



## <a name="post-migration"></a>Po migracji 

Po pomyślnym zakończeniu etapu **migracji** należy przejść przez serię zadań po migracji, aby upewnić się, że wszystko działa jak najszybciej i efektywnie.

### <a name="remediate-applications"></a>Koryguj aplikacje

Po przeprowadzeniu migracji danych do środowiska docelowego wszystkie aplikacje, które wcześniej korzystały ze źródła, muszą zacząć zużywać miejsce docelowe. W niektórych przypadkach będzie wymagane wprowadzenie zmian w aplikacjach.

### <a name="perform-tests"></a>Wykonaj testy

Podejście testowe do migracji bazy danych obejmuje wykonywanie następujących czynności:

1. **Opracowuj testy weryfikacyjne**. Aby przetestować migrację bazy danych, należy użyć zapytań SQL. Należy utworzyć zapytania walidacji do uruchomienia względem źródłowej i docelowej bazy danych. Zapytania weryfikacyjne powinny obejmować zdefiniowany zakres.

2. **Konfigurowanie środowiska testowego**. Środowisko testowe powinno zawierać kopię źródłowej bazy danych i docelowej bazy danych. Należy pamiętać o odizolowaniu środowiska testowego.

3. **Uruchom testy weryfikacyjne**. Uruchom testy weryfikacyjne względem źródła i celu, a następnie Przeanalizuj wyniki.

4. **Uruchom testy wydajnościowe**. Uruchom test wydajności na źródłowym i docelowym, a następnie Przeanalizuj i Porównaj wyniki.

### <a name="optimize"></a>Optymalizacja

Faza po migracji jest kluczowa dla uzgadniania wszelkich problemów z dokładnością danych i weryfikowania kompletności, a także do rozwiązywania problemów z wydajnością w ramach obciążenia.

Aby uzyskać dodatkowe informacje o tych problemach i konkretnych krokach, które należy rozwiązać, zobacz [Przewodnik po weryfikacji i optymalizacji po migracji](/sql/relational-databases/post-migration-validation-and-optimization-guide).

## <a name="migration-assets"></a>Zasoby migracji

Aby uzyskać dodatkową pomoc dotyczącą wykonywania tego scenariusza migracji, zobacz następujące zasoby, które zostały opracowane w ramach obsługi projektu migracji rzeczywistej.

| Tytuł/link     | Opis    |
| ---------------------------------------------- | ---------------------------------------------------- |
| [Model i narzędzie oceny obciążenia danych](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | To narzędzie zapewnia sugerowane Platformy docelowe, gotowość chmury oraz poziom korygowania aplikacji/bazy danych dla danego obciążenia. Oferuje proste, oparte na jednym kliknięcie Obliczanie i generowanie raportów, które znacznie ułatwiają przyspieszenie oceny dużych ilości, zapewniając i zautomatyzowany i jednolity proces podejmowania decyzji platformy docelowej. |

Te zasoby zostały opracowane w ramach programu SQL Data ninja, który jest sponsorowany przez zespół inżynierów grupy danych platformy Azure. Podstawowa karta programu SQL Data Ninja to odblokowanie i przyspieszenie złożonej modernizacji i konkurowania możliwości migracji platformy danych na platformę danych platformy Microsoft Azure. Jeśli uważasz, że Twoja organizacja będzie chciała uczestniczyć w programie SQL Data ninja, skontaktuj się z zespołem ds. kont i poproś o przesłanie nominacji.

## <a name="next-steps"></a>Następne kroki 

- Zapoznaj się z [kalkulatorem całkowitego kosztu posiadania (TCO) na platformie Azure](https://aka.ms/azure-tco) , aby ułatwić oszacowanie oszczędności kosztów, które możesz zrealizować przez Migrowanie obciążeń do platformy Azure.

- Macierz usług i narzędzi firmy Microsoft i innych firm, które są dostępne w celu ułatwienia pracy z różnymi scenariuszami bazy danych i migracji danych oraz zadaniami specjalistycznymi, można znaleźć w artykule [Usługa i narzędzia do migracji danych](../../../dms/dms-tools-matrix.md).

- W przypadku innych przewodników migracji zapoznaj się z tematem [migracja bazy danych](https://datamigration.microsoft.com/). 

Aby uzyskać wideo, zobacz: 
- [Przegląd podróży migracji i narzędzi/usług zalecanych do przeprowadzania oceny i migracji](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)