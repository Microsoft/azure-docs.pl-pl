---
title: 'Baza danych MySQL do Azure SQL Database: Przewodnik migracji'
description: W tym przewodniku dowiesz się, jak migrować bazy danych MySQL do bazy danych Azure SQL Database przy użyciu Asystent migracji do programu SQL Server for MySQL (ASYSTENCIE migracji for MySQL).
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: 32c56df5bafa9439fc559edf137c1080920cfb32
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107284378"
---
# <a name="migration-guide-mysql-to-azure-sql-database"></a>Przewodnik migracji: MySQL do Azure SQL Database
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

W tym przewodniku dowiesz się, [Jak przeprowadzić migrację](https://azure.microsoft.com/migration/migration-journey) bazy danych MySQL do bazy danych Azure SQL Database za pomocą [SQL Server](https://azure.microsoft.com/en-us/migration/sql-server/) Assistant (Asystencie migracji for MySQL). 

Aby poznać inne przewodniki dotyczące migracji, zobacz [Przewodnik migracji usługi Azure Database](https://docs.microsoft.com/data-migration). 

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem migrowania bazy danych MySQL do bazy danych SQL wykonaj następujące czynności:

- Sprawdź, czy środowisko źródłowe jest obsługiwane. Obecnie są obsługiwane produkty MySQL 5,6 i 5,7. 
- Pobierz i zainstaluj [Asystent migracji do programu SQL Server dla programu MySQL](https://www.microsoft.com/download/details.aspx?id=54257).
- Upewnij się, że masz łączność i wystarczające uprawnienia dostępu do źródła i celu.

## <a name="pre-migration"></a>Przed migracją 

Po spełnieniu wymagań wstępnych można przystąpić do odnajdywania topologii środowiska i oceny wykonalności [migracji w chmurze platformy Azure](https://azure.microsoft.com/migration).

### <a name="assess"></a>Ocena 

Użyj Asystent migracji do programu SQL Server (ASYSTENCIE migracji) dla programu MySQL, aby przeglądać obiekty bazy danych i dane oraz oceniać bazy danych do migracji. 

Aby utworzyć ocenę, wykonaj następujące czynności:

1. Otwórz [Asystencie migracji dla programu MySQL](https://www.microsoft.com/download/details.aspx?id=54257). 
1. Wybierz pozycję **plik**, a następnie wybierz pozycję **Nowy projekt**. 
1. W okienku **Nowy projekt** wprowadź nazwę i lokalizację projektu, a następnie na liście rozwijanej **Migrowanie do** wybierz pozycję **Azure SQL Database**. 
1. Wybierz przycisk **OK**.

   ![Zrzut ekranu przedstawiający okienko "nowy projekt" służący do wprowadzania nazwy, lokalizacji i celu projektu migracji.](./media/mysql-to-sql-database-guide/new-project.png)

1. Wybierz kartę **Połącz z bazą danych MySQL** , a następnie podaj szczegóły dotyczące łączenia serwera MySQL. 

   ![Zrzut ekranu przedstawiający okienko "łączenie z bazą danych MySQL" służący do określania połączeń ze źródłem.](./media/mysql-to-sql-database-guide/connect-to-mysql.png)

1. W okienku **Eksplorator metadanych MySQL** kliknij prawym przyciskiem myszy schemat MySQL, a następnie wybierz polecenie **Utwórz raport**. Alternatywnie możesz wybrać kartę **Utwórz raport** w prawym górnym rogu.

   ![Zrzut ekranu przedstawiający linki "Utwórz raport" w programie ASYSTENCIE migracji for MySQL.](./media/mysql-to-sql-database-guide/create-report.png)

1. Przejrzyj raport HTML, aby poznać statystyki, błędy i ostrzeżenia dotyczące konwersji. Analizuj go, aby zrozumieć problemy z konwersją i rozwiązania. 
   Możesz również otworzyć raport w programie Excel, aby uzyskać spis obiektów MySQL i zrozumieć nakłady pracy, które są wymagane do wykonania konwersji schematu. Domyślna lokalizacja raportu znajduje się w folderze raportów w SSMAProjects. Na przykład: 
   
   `drive:\Users\<username>\Documents\SSMAProjects\MySQLMigration\report\report_2016_11_12T02_47_55\`
 
   ![Zrzut ekranu przykładowego raportu konwersji w ASYSTENCIE migracji.](./media/mysql-to-sql-database-guide/conversion-report.png)

### <a name="validate-the-data-types"></a>Sprawdzanie poprawności typów danych

Sprawdź poprawność domyślnych mapowań typu danych i zmień je w zależności od wymagań, w razie potrzeby. W tym celu: 

1. Wybierz pozycję **Narzędzia**, a następnie wybierz pozycję **Ustawienia projektu**.  
1. Wybierz kartę **mapowania typu** . 

   ![Zrzut ekranu okienka "Mapowanie typów" w ASYSTENCIE migracji for MySQL.](./media/mysql-to-sql-database-guide/type-mappings.png)

1. Można zmienić mapowanie typu dla każdej tabeli, wybierając nazwę tabeli w okienku **Eksploratora metadanych programu MySQL** . 

### <a name="convert-the-schema"></a>Konwertuj schemat 

Aby skonwertować schemat, wykonaj następujące czynności: 

1. Obowiązkowe Aby skonwertować zapytania dynamiczne lub wyspecjalizowane, kliknij prawym przyciskiem myszy węzeł, a następnie wybierz polecenie **Dodaj instrukcję**. 

1. Wybierz kartę **Połącz z Azure SQL Database** , a następnie wykonaj następujące czynności:

   a. Wprowadź szczegóły dotyczące łączenia się z bazą danych SQL.  
   b. Z listy rozwijanej wybierz docelową bazę danych SQL. Można też podać nową nazwę, w takim przypadku na serwerze docelowym zostanie utworzona baza danych.  
   c. Podaj szczegóły uwierzytelniania.  
   d. Wybierz pozycję **Połącz**.

   ![Zrzut ekranu okienka "łączenie z Azure SQL Database" w programie ASYSTENCIE migracji for MySQL.](./media/mysql-to-sql-database-guide/connect-to-sqldb.png)
 
1. Kliknij prawym przyciskiem myszy schemat, z którym pracujesz, a następnie wybierz polecenie **Konwertuj schemat**. Alternatywnie możesz wybrać kartę **Konwertuj schemat** w prawym górnym rogu.

   ![Zrzut ekranu przedstawiający polecenie "Konwertuj schemat" w okienku "Eksplorator metadanych MySQL".](./media/mysql-to-sql-database-guide/convert-schema.png)

1. Po zakończeniu konwersji Przejrzyj przekonwertowane obiekty i porównaj je z oryginalnymi obiektami, aby zidentyfikować potencjalne problemy i rozwiązać je na podstawie zaleceń. 

   ![Zrzut ekranu przedstawiający porównanie przekonwertowanych obiektów do oryginalnych obiektów.](./media/mysql-to-sql-database-guide/table-comparison.png)

   Porównaj przekonwertowany tekst języka Transact-SQL z oryginalnym kodem i zapoznaj się z zaleceniami.

   ![Zrzut ekranu przedstawiający porównanie przekonwertowanych zapytań do kodu źródłowego.](./media/mysql-to-sql-database-guide/procedure-comparison.png)

1. W okienku **danych wyjściowych** wybierz pozycję **Przejrzyj wyniki**, a następnie przejrzyj wszystkie błędy w okienku **Lista błędów** . 
1. Zapisz projekt lokalnie dla ćwiczenia korygowania schematu w trybie offline. W tym celu wybierz pozycję **plik**  >  **Zapisz projekt**. Dzięki temu można oszacować schematy źródłowe i docelowe w trybie offline i przeprowadzić korygowanie przed opublikowaniem schematu w usłudze SQL Database.

   Porównaj przekonwertowane procedury z oryginalnymi procedurami, jak pokazano poniżej: 

   ![Zrzut ekranu przedstawiający porównanie przekonwertowane procedury z oryginalnymi procedurami.](./media/mysql-to-sql-database-guide/procedure-comparison.png)


## <a name="migrate-the-databases"></a>Migrowanie baz danych 

Po przeprowadzeniu oceny baz danych i rozpoczęciu wszelkich rozbieżności można uruchomić proces migracji. Migracja obejmuje dwie czynności: publikowanie schematu i Migrowanie danych. 

Aby opublikować schemat i przeprowadzić migrację danych, wykonaj następujące czynności: 

1. Opublikuj schemat. W okienku **Azure SQL Database metadane Eksploratora** kliknij prawym przyciskiem myszy bazę danych, a następnie wybierz polecenie **Synchronizuj z bazą danych**. Ta akcja powoduje opublikowanie schematu MySQL w bazie danych SQL.

   ![Zrzut ekranu przedstawiający okienko "Synchronizuj z bazą danych" w celu przejrzenia mapowania bazy danych.](./media/mysql-to-sql-database-guide/synchronize-database-review.png)

1. Migruj dane. W okienku **Eksplorator metadanych MySQL** kliknij prawym przyciskiem myszy schemat MySQL, który chcesz zmigrować, a następnie wybierz polecenie **Migruj dane**. Alternatywnie możesz wybrać kartę **Migrowanie danych** w prawym górnym rogu.

   Aby migrować dane dla całej bazy danych, zaznacz pole wyboru obok nazwy bazy danych. Aby przeprowadzić migrację danych z pojedynczych tabel, rozwiń bazę danych, rozwiń węzeł **tabele**, a następnie zaznacz pole wyboru obok tabeli. Aby pominąć dane z poszczególnych tabel, wyczyść to pole wyboru.

   ![Zrzut ekranu przedstawiający polecenie "Migrowanie danych" w okienku "Eksplorator metadanych MySQL".](./media/mysql-to-sql-database-guide/migrate-data.png)

1. Po zakończeniu migracji Wyświetl **raport dotyczący migracji danych**.
   
   ![Zrzut ekranu przedstawiający raport dotyczący migracji danych.](./media/mysql-to-sql-database-guide/data-migration-report.png)

1. Nawiąż połączenie z bazą danych SQL przy użyciu [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) i sprawdź poprawność migracji, przeglądając dane i schemat.

   ![Zrzut ekranu przedstawiający SQL Server Management Studio.](./media/mysql-to-sql-database-guide/validate-in-ssms.png)

## <a name="post-migration"></a>Po migracji 

Po pomyślnym zakończeniu etapu *migracji* należy wykonać serię zadań po migracji, aby upewnić się, że wszystko działa jak najszybciej i efektywnie.

### <a name="remediate-applications"></a>Koryguj aplikacje

Po przeprowadzeniu migracji danych do środowiska docelowego wszystkie aplikacje, które wcześniej korzystały ze źródła, muszą zacząć zużywać miejsce docelowe. W niektórych przypadkach będzie wymagane wprowadzenie zmian w aplikacjach.

### <a name="perform-tests"></a>Wykonaj testy

Podejście testowe do migracji bazy danych obejmuje następujące działania:

1. **Opracowywanie testów weryfikacyjnych**: Aby przetestować migrację bazy danych, należy użyć zapytań SQL. Należy utworzyć zapytania walidacji do uruchomienia zarówno dla źródłowej, jak i docelowej bazy danych. Zapytania weryfikacyjne powinny obejmować zdefiniowany zakres.

1. **Konfigurowanie środowiska testowego**: środowisko testowe powinno zawierać kopię źródłowej bazy danych i docelowej bazy danych. Należy pamiętać o odizolowaniu środowiska testowego.

1. **Uruchom testy weryfikacyjne**: Uruchom testy weryfikacyjne względem źródła i celu, a następnie Przeanalizuj wyniki.

1. **Uruchom testy wydajnościowe**: Uruchom testy wydajności względem źródła i celu, a następnie Przeanalizuj i Porównaj wyniki.

### <a name="optimize"></a>Optymalizacja

Faza po migracji jest kluczowa do uzgadniania problemów z dokładnością danych, sprawdzania kompletności i rozwiązywania problemów z wydajnością w ramach obciążenia.

Więcej informacji o tych problemach i krokach, które należy rozwiązać, można znaleźć w [przewodniku po sprawdzeniu poprawności po migracji i optymalizacji](/sql/relational-databases/post-migration-validation-and-optimization-guide).

## <a name="migration-assets"></a>Zasoby migracji

Aby uzyskać więcej pomocy przy wykonywaniu tego scenariusza migracji, zobacz następujący zasób. Opracowano w ramach obsługi rzeczywistego zaangażowania projektu migracji.

| Tytuł | Opis |
| --- | --- |
| [Model i narzędzie oceny obciążenia danych](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Oferuje sugerowane "najlepsze dopasowania" platform docelowych, gotowość chmury oraz poziomy korygowania aplikacji/bazy danych dla określonych obciążeń. Oferuje proste, oparte na jednym kliknięcie Obliczanie i generowanie raportów, które ułatwiają przyspieszenie oceny dużych ilości, zapewniając zautomatyzowany, jednolity proces podejmowania decyzji na platformie docelowej. |

Zespół inżynierów danych SQL Data opracował te zasoby. Podstawowa karta tego zespołu ma odblokować i przyspieszyć kompleksową modernizację projektów migracji platformy danych do platformy danych platformy Microsoft Azure.

## <a name="next-steps"></a>Następne kroki 

- Aby ułatwić oszacowanie oszczędności kosztów, możesz zrealizować swoje obciążenia na platformie Azure, zapoznaj się z [kalkulatorem całkowitego kosztu posiadania na platformę Azure](https://aka.ms/azure-tco).

- Macierz usług i narzędzi firmy Microsoft i innych firm, które są dostępne w celu ułatwienia pracy z różnymi scenariuszami bazy danych i migracji danych oraz zadaniami specjalistycznymi, można znaleźć w temacie [Usługa i narzędzia do migracji danych](../../../dms/dms-tools-matrix.md).

- Aby poznać inne przewodniki dotyczące migracji, zobacz [Przewodnik migracji usługi Azure Database](https://datamigration.microsoft.com/). 

- W przypadku filmów wideo dotyczących migracji zobacz [Omówienie podróży migracji oraz zalecane narzędzia i usługi do oceny](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/).

- Aby uzyskać więcej [zasobów migracji w chmurze](https://azure.microsoft.com/migration/resources/), zobacz [rozwiązania do migracji do chmury](https://azure.microsoft.com/migration).

