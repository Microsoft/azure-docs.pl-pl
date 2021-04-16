---
title: 'MySQL to Azure SQL Database: Przewodnik migracji'
description: Z tego przewodnika dowiesz się, jak przeprowadzić migrację baz danych MySQL do bazy danych programu Azure SQL przy użyciu programu Asystent migracji do programu SQL Server for MySQL (SSMA for MySQL).
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: d4510aa5cda61dac88102c89b3e03da231380bd6
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389455"
---
# <a name="migration-guide-mysql-to-azure-sql-database"></a>Przewodnik migracji: MySQL do Azure SQL Database
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

Z tego przewodnika [](https://azure.microsoft.com/migration/migration-journey) dowiesz się, jak przeprowadzić migrację bazy danych MySQL do bazy danych programu Azure SQL przy użyciu programu [SQL Server Migration](https://azure.microsoft.com/en-us/migration/sql-server/) Assistant for MySQL (SSMA for MySQL). 

Aby uzyskać informacje na temat innych przewodników migracji, zobacz [Przewodnik po migracji bazy danych platformy Azure.](https://docs.microsoft.com/data-migration) 

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem migracji bazy danych MySQL do bazy danych SQL wykonaj następujące czynności:

- Sprawdź, czy środowisko źródłowe jest obsługiwane. Obecnie obsługiwane są program MySQL 5.6 i 5.7. 
- Pobierz i zainstaluj [Asystent migracji do programu SQL Server dla programu MySQL.](https://www.microsoft.com/download/details.aspx?id=54257)
- Upewnij się, że masz łączność i wystarczające uprawnienia, aby uzyskać dostęp zarówno do źródła, jak i obiektu docelowego.

## <a name="pre-migration"></a>Przed migracją 

Po spełnianiu wymagań wstępnych możesz poznać topologię środowiska i ocenić możliwość migracji do [chmury platformy Azure.](https://azure.microsoft.com/migration)

### <a name="assess"></a>Ocena 

Użyj Asystent migracji do programu SQL Server (SSMA) for MySQL, aby przeglądać obiekty i dane bazy danych oraz oceniać bazy danych do migracji. 

Aby utworzyć ocenę, wykonaj następujące czynności:

1. Otwórz [program SSMA for MySQL.](https://www.microsoft.com/download/details.aspx?id=54257) 
1. Wybierz **pozycję File**(Plik), a następnie wybierz pozycję New Project **(Nowy projekt).** 
1. W **okienku Nowy** projekt wprowadź nazwę i lokalizację projektu, a następnie z listy rozwijanej Migruj do wybierz **pozycję Azure SQL Database**.  
1. Wybierz przycisk **OK**.

   ![Zrzut ekranu przedstawiający okienko "Nowy projekt" służące do wprowadzania nazwy, lokalizacji i celu projektu migracji.](./media/mysql-to-sql-database-guide/new-project.png)

1. Wybierz **kartę Połącz z programem MySQL,** a następnie podaj szczegóły dotyczące nawiązywania połączenia z serwerem MySQL. 

   ![Zrzut ekranu przedstawiający okienko "Łączenie z programem MySQL" służące do określania połączeń ze źródłem.](./media/mysql-to-sql-database-guide/connect-to-mysql.png)

1. W **okienku MySQL Metadata Explorer (Eksplorator metadanych MySQL)** kliknij prawym przyciskiem myszy schemat MySQL, a następnie wybierz **pozycję Create Report (Utwórz raport).** Alternatywnie możesz wybrać kartę **Utwórz raport** w prawym górnym rogu.

   ![Zrzut ekranu przedstawiający linki "Utwórz raport" w programie SSMA for MySQL.](./media/mysql-to-sql-database-guide/create-report.png)

1. Przejrzyj raport HTML, aby poznać statystyki konwersji, błędy i ostrzeżenia. Przeanalizuj go, aby zrozumieć problemy z konwersją i rozwiązania. 
   Możesz również otworzyć raport w programie Excel, aby uzyskać spis obiektów MySQL i zrozumieć nakład pracy wymagany do przeprowadzenia konwersji schematu. Domyślna lokalizacja raportu znajduje się w folderze raportu w ramach projektu SSMAProjects. Na przykład: 
   
   `drive:\Users\<username>\Documents\SSMAProjects\MySQLMigration\report\report_2016_11_12T02_47_55\`
 
   ![Zrzut ekranu przedstawiający przykładowy raport konwersji w programie SSMA.](./media/mysql-to-sql-database-guide/conversion-report.png)

### <a name="validate-the-data-types"></a>Weryfikowanie typów danych

W razie potrzeby zweryfikuj domyślne mapowania typów danych i zmień je na podstawie wymagań. W tym celu: 

1. Wybierz **pozycję Narzędzia,** a następnie wybierz **pozycję Project Settings (Ustawienia projektu).**  
1. Wybierz **kartę Mapowania** typów. 

   ![Zrzut ekranu przedstawiający okienko "Mapowanie typów" w programie SSMA for MySQL.](./media/mysql-to-sql-database-guide/type-mappings.png)

1. Mapowanie typów dla każdej tabeli można zmienić, wybierając nazwę tabeli w **okienku Eksplorator metadanych MySQL.** 

### <a name="convert-the-schema"></a>Konwertowanie schematu 

Aby przekonwertować schemat, wykonaj następujące czynności: 

1. (Opcjonalnie) Aby przekonwertować zapytania dynamiczne lub wyspecjalizowane, kliknij prawym przyciskiem myszy węzeł, a następnie wybierz polecenie **Dodaj instrukcje**. 

1. Wybierz **kartę Połącz Azure SQL Database,** a następnie wykonaj następujące czynności:

   a. Wprowadź szczegóły dotyczące nawiązywania połączenia z bazą danych SQL.  
   b. Z listy rozwijanej wybierz docelową bazę danych SQL. Możesz też podać nową nazwę. W takim przypadku baza danych zostanie utworzona na serwerze docelowym.  
   c. Podaj szczegóły uwierzytelniania.  
   d. Wybierz pozycję **Połącz**.

   ![Zrzut ekranu przedstawiający okienko "Łączenie Azure SQL Database" w programie SSMA for MySQL.](./media/mysql-to-sql-database-guide/connect-to-sqldb.png)
 
1. Kliknij prawym przyciskiem myszy schemat, z który pracujesz, a następnie wybierz polecenie **Konwertuj schemat.** Alternatywnie możesz wybrać kartę **Konwertuj schemat** w prawym górnym rogu.

   ![Zrzut ekranu przedstawiający polecenie "Konwertuj schemat" w okienku "Eksplorator metadanych MySQL".](./media/mysql-to-sql-database-guide/convert-schema.png)

1. Po zakończeniu konwersji przejrzyj i porównaj przekonwertowane obiekty z oryginalnymi obiektami, aby zidentyfikować potencjalne problemy i rozwiązać je na podstawie zaleceń. 

   ![Zrzut ekranu przedstawiający porównanie przekonwertowanych obiektów z oryginalnymi obiektami.](./media/mysql-to-sql-database-guide/table-comparison.png)

   Porównaj przekonwertowany tekst języka Transact-SQL z oryginalnym kodem i zapoznaj się z zaleceniami.

   ![Zrzut ekranu przedstawiający porównanie przekonwertowanych zapytań z kodem źródłowym.](./media/mysql-to-sql-database-guide/procedure-comparison.png)

1. W **okienku Dane** wyjściowe wybierz pozycję **Przejrzyj wyniki,** a następnie przejrzyj wszelkie błędy w **okienku Lista** błędów. 
1. Zapisz projekt lokalnie w celu skorygowania schematu w trybie offline. W tym celu wybierz pozycję **Plik**  >  **Zapisz projekt.** Daje to możliwość oceny schematów źródłowych i docelowych w trybie offline i wykonania korygowania przed opublikowaniem schematu w bazie danych SQL.

   Porównaj przekonwertowane procedury z oryginalnymi procedurami, jak pokazano poniżej: 

   ![Zrzut ekranu przedstawiający porównanie przekonwertowanych procedur z oryginalnymi procedurami.](./media/mysql-to-sql-database-guide/procedure-comparison.png)


## <a name="migrate-the-databases"></a>Migrowanie baz danych 

Po ocenie baz danych i wylieniu wszelkich rozbieżności można uruchomić proces migracji. Migracja obejmuje dwa kroki: opublikowanie schematu i migrację danych. 

Aby opublikować schemat i przeprowadzić migrację danych, wykonaj następujące czynności: 

1. Opublikuj schemat. W **okienku Azure SQL Database Metadata Explorer** kliknij prawym przyciskiem myszy bazę danych, a następnie wybierz pozycję **Synchronizuj z bazą danych**. Ta akcja publikuje schemat MySQL w bazie danych SQL.

   ![Zrzut ekranu przedstawiający okienko "Synchronizuj z bazą danych" służące do przeglądania mapowania bazy danych.](./media/mysql-to-sql-database-guide/synchronize-database-review.png)

1. Migrowanie danych. W **okienku MySQL Metadata Explorer (Eksplorator metadanych MySQL)** kliknij prawym przyciskiem myszy schemat MySQL, który chcesz zmigrować, a następnie wybierz pozycję **Migrate Data (Migruj dane).** Alternatywnie możesz wybrać kartę **Migruj** dane w prawym górnym rogu.

   Aby przeprowadzić migrację danych dla całej bazy danych, zaznacz pole wyboru obok nazwy bazy danych. Aby przeprowadzić migrację danych z poszczególnych tabel, rozwiń bazę danych, rozwiń pozycję **Tabele**, a następnie zaznacz pole wyboru obok tabeli. Aby pominąć dane z poszczególnych tabel, wyczyść pole wyboru.

   ![Zrzut ekranu przedstawiający polecenie "Migruj dane" w okienku "Eksplorator metadanych MySQL".](./media/mysql-to-sql-database-guide/migrate-data.png)

1. Po zakończeniu migracji wyświetl raport **migracji danych**.
   
   ![Zrzut ekranu przedstawiający raport migracji danych.](./media/mysql-to-sql-database-guide/data-migration-report.png)

1. Połącz się z bazą danych SQL przy [użyciu SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) i zweryfikuj migrację, przeglądając dane i schemat.

   ![Zrzut ekranu przedstawiający SQL Server Management Studio.](./media/mysql-to-sql-database-guide/validate-in-ssms.png)

## <a name="post-migration"></a>Po migracji 

Po pomyślnym zakończeniu  etapu migracji należy wykonać serię zadań po migracji, aby upewnić się, że wszystko działa tak bezproblemowo i wydajnie, jak to możliwe.

### <a name="remediate-applications"></a>Korygowanie aplikacji

Po migracji danych do środowiska docelowego wszystkie aplikacje, które wcześniej zużywały źródło, muszą zacząć korzystać z obiektu docelowego. W niektórych przypadkach będzie to wymagało zmian w aplikacjach.

### <a name="perform-tests"></a>Wykonywanie testów

Testowe podejście do migracji bazy danych obejmuje następujące działania:

1. **Opracowywanie testów weryfikacyjnych:** aby przetestować migrację bazy danych, należy użyć zapytań SQL. Należy utworzyć zapytania weryfikacji do uruchamiania zarówno dla źródłowej, jak i docelowej bazy danych. Zapytania weryfikacji powinny obejmować zdefiniowany zakres.

1. **Konfigurowanie środowiska testowego:** środowisko testowe powinno zawierać kopię źródłowej bazy danych i docelowej bazy danych. Pamiętaj, aby odizolować środowisko testowe.

1. **Uruchamianie testów walidacji:** uruchom testy walidacji względem źródła i obiektu docelowego, a następnie przeanalizuj wyniki.

1. **Uruchamianie testów wydajnościowych:** uruchom testy wydajnościowe względem źródła i obiektu docelowego, a następnie przeanalizuj i porównaj wyniki.

### <a name="optimize"></a>Optymalizacja

Faza po migracji ma kluczowe znaczenie dla uzgadniania wszelkich problemów z dokładnością danych, weryfikowania kompletności i rozwiązywania problemów z wydajnością obciążenia.

Aby uzyskać więcej informacji na temat tych problemów i czynności w celu ich ograniczenia, zobacz Przewodnik po [weryfikacji i optymalizacji](/sql/relational-databases/post-migration-validation-and-optimization-guide)po migracji.

## <a name="migration-assets"></a>Zasoby migracji

Aby uzyskać więcej pomocy przy ukończeniu tego scenariusza migracji, zobacz następujący zasób. Opracowano go w celu wspierania zaangażowania w rzeczywisty projekt migracji.

| Tytuł | Opis |
| --- | --- |
| [Model i narzędzie do oceny obciążenia danych](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Zawiera sugerowane "najlepsze dopasowanie" platform docelowych, gotowość do chmury oraz poziomy korygowania aplikacji/bazy danych dla określonych obciążeń. Oferuje proste obliczenia jednym kliknięciem i generowanie raportów, które pomagają przyspieszyć ocenę dużych nieruchomości, zapewniając zautomatyzowany, jednolity proces podejmowania decyzji na platformie docelowej. |

Zespół inżynierów ds. danych SQL opracował te zasoby. Podstawowym elementem tego zespołu jest odblokowanie i przyspieszenie złożonej modernizacji projektów migracji platformy danych na platformę danych Microsoft Azure.

## <a name="next-steps"></a>Następne kroki 

- Aby ułatwić oszacowanie oszczędności kosztów, które można uzyskać, migrując obciążenia na platformę Azure, zobacz Kalkulator całkowitego kosztu [posiadania platformy Azure.](https://aka.ms/azure-tco)

- Aby uzyskać macierz usług i narzędzi firmy Microsoft oraz innych firm, które są dostępne, aby pomóc w różnych scenariuszach migracji baz danych i danych oraz zadaniach specjalnych, zobacz Usługi i narzędzia do [migracji danych.](../../../dms/dms-tools-matrix.md)

- Aby uzyskać informacje na temat innych przewodników migracji, zobacz [Przewodnik po migracji bazy danych platformy Azure.](https://datamigration.microsoft.com/) 

- Aby uzyskać wideo dotyczące migracji, zobacz Omówienie podróży migracji oraz zalecane narzędzia i usługi do [migracji i oceny.](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)

- Aby uzyskać więcej [zasobów migracji do chmury,](https://azure.microsoft.com/migration/resources/)zobacz rozwiązania do migracji [do chmury](https://azure.microsoft.com/migration).

