---
title: 'Dostęp do Azure SQL Database: Przewodnik migracji'
description: Z tego przewodnika dowiesz się, jak przeprowadzić migrację baz danych programu Microsoft Access do bazy danych programu Azure SQL przy użyciu programu Asystent migracji do programu SQL Server for Access (SSMA for Access).
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: 137adbb045a4c449193f9029b9c72f09ddc439b1
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388469"
---
# <a name="migration-guide-access-to-azure-sql-database"></a>Przewodnik migracji: dostęp do Azure SQL Database

Z tego przewodnika [](https://azure.microsoft.com/migration/migration-journey) dowiesz się, jak przeprowadzić migrację bazy danych programu Microsoft Access do bazy danych programu Azure SQL przy użyciu programu [SQL Server Migration](https://azure.microsoft.com/en-us/migration/sql-server/) Assistant for Access (SSMA for Access).

Aby uzyskać informacje na temat innych przewodników migracji, [zobacz Przewodnik po migracji bazy danych platformy Azure.](https://docs.microsoft.com/data-migration) 

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem migracji bazy danych programu Access do bazy danych SQL wykonaj następujące czynności:

- Sprawdź, czy środowisko źródłowe jest obsługiwane. 
- Pobierz i zainstaluj [program Asystent migracji do programu SQL Server for Access.](https://www.microsoft.com/download/details.aspx?id=54255)
- Upewnij się, że masz łączność i wystarczające uprawnienia, aby uzyskać dostęp zarówno do źródła, jak i do obiektu docelowego.

## <a name="pre-migration"></a>Przed migracją

Po spełnianiu wymagań wstępnych możesz poznać topologię środowiska i ocenić możliwość migracji do [chmury na platformie Azure.](https://azure.microsoft.com/migration)


### <a name="assess"></a>Ocena 

Użyj programu SSMA for Access, aby przeglądać obiekty i dane bazy danych oraz oceniać bazy danych do migracji. 

Aby utworzyć ocenę, wykonaj następujące czynności: 

1. Otwórz [program SSMA for Access.](https://www.microsoft.com/download/details.aspx?id=54255) 
1. Wybierz **pozycję File**(Plik), a następnie wybierz pozycję New Project **(Nowy projekt).** 
1. Podaj nazwę projektu i lokalizację projektu, a następnie z listy rozwijanej wybierz pozycję **Azure SQL Database** jako cel migracji. 
1. Wybierz przycisk **OK**. 

   ![Zrzut ekranu przedstawiający okienko "Nowy projekt" służące do wprowadzania nazwy i lokalizacji projektu migracji.](./media/access-to-sql-database-guide/new-project.png)

1. Wybierz **pozycję Dodaj bazy** danych, a następnie wybierz bazy danych, które mają zostać dodane do nowego projektu. 

   ![Zrzut ekranu przedstawiający kartę "Dodawanie baz danych" w programie SSMA for Access.](./media/access-to-sql-database-guide/add-databases.png)

1. W **okienku Eksplorator metadanych dostępu** kliknij prawym przyciskiem myszy bazę danych, a następnie wybierz pozycję **Utwórz raport.** Alternatywnie możesz wybrać kartę **Utwórz raport** w prawym górnym rogu.

   ![Zrzut ekranu przedstawiający polecenie "Utwórz raport" w Eksploratorze metadanych dostępu.](./media/access-to-sql-database-guide/create-report.png)

1. Przejrzyj raport HTML, aby poznać statystyki konwersji oraz wszelkie błędy lub ostrzeżenia. Możesz również otworzyć raport w programie Excel, aby uzyskać spis obiektów programu Access i zrozumieć nakład pracy wymagany do przeprowadzenia konwersji schematu. Domyślna lokalizacja raportu znajduje się w folderze raportu w ramach SSMAProjects. Na przykład:

   `drive:\<username>\Documents\SSMAProjects\MyAccessMigration\report\report_<date>`

   ![Zrzut ekranu przedstawiający przykład oceny raportu bazy danych w ramach programu SSMA.](./media/access-to-sql-database-guide/sample-assessment.png)

### <a name="validate-the-data-types"></a>Weryfikowanie typów danych

W razie potrzeby zweryfikuj domyślne mapowania typów danych i zmień je w zależności od wymagań. W tym celu:

1. W narzędziu SSMA for Access wybierz **pozycję Narzędzia**, a następnie wybierz pozycję Project **Settings (Ustawienia projektu).** 
1. Wybierz **kartę Mapowanie** typów. 

   ![Zrzut ekranu przedstawiający okienko "Mapowanie typów" w funkcji SSMA for Access.](./media/access-to-sql-database-guide/type-mappings.png)

1. Mapowanie typów dla każdej tabeli można zmienić, wybierając nazwę tabeli w okienku **Eksplorator metadanych** dostępu.


### <a name="convert-the-schema"></a>Konwertowanie schematu

Aby przekonwertować obiekty bazy danych, wykonaj następujące czynności: 

1. Wybierz **kartę Połącz Azure SQL Database,** a następnie wykonaj następujące czynności:

   a. Wprowadź szczegóły dotyczące nawiązywania połączenia z bazą danych SQL.  
   b. Z listy rozwijanej wybierz docelową bazę danych SQL. Możesz też wprowadzić nową nazwę. W takim przypadku baza danych zostanie utworzona na serwerze docelowym.  
   c. Podaj szczegóły uwierzytelniania.   
   d. Wybierz pozycję **Połącz**.

   ![Zrzut ekranu przedstawiający okienko "Połącz z Azure SQL Database" służące do wprowadzania szczegółów połączenia.](./media/access-to-sql-database-guide/connect-to-sqldb.png)

1. W **okienku Eksplorator metadanych dostępu** kliknij prawym przyciskiem myszy bazę danych, a następnie wybierz polecenie **Konwertuj schemat.** Alternatywnie możesz wybrać bazę danych, a następnie wybrać **kartę Konwertuj** schemat.

   ![Zrzut ekranu przedstawiający polecenie "Konwertuj schemat" w okienku "Uzyskiwanie dostępu do eksploratora metadanych".](./media/access-to-sql-database-guide/convert-schema.png)

1. Po zakończeniu konwersji porównaj przekonwertowane obiekty z oryginalnymi obiektami, aby zidentyfikować potencjalne problemy i rozwiązać problemy na podstawie zaleceń.

   ![Zrzut ekranu przedstawiający porównanie przekonwertowanych obiektów z obiektami źródłowymi.](./media/access-to-sql-database-guide/table-comparison.png)

    Porównaj przekonwertowany tekst transact-SQL z oryginalnym kodem i przejrzyj zalecenia.

   ![Zrzut ekranu przedstawiający porównanie przekonwertowanych zapytań z kodem źródłowym.](./media/access-to-sql-database-guide/query-comparison.png) 

1. (Opcjonalnie) Aby przekonwertować pojedynczy obiekt, kliknij go prawym przyciskiem myszy, a następnie wybierz polecenie **Konwertuj schemat**. Przekonwertowane obiekty są wyświetlane w pogrubionym tekście w **Eksploratorze metadanych dostępu:** 

   ![Zrzut ekranu przedstawiający konwertowanie obiektów w Eksploratorze metadanych dostępu.](./media/access-to-sql-database-guide/converted-items.png)
 
1. W **okienku Dane** wyjściowe wybierz **ikonę Przejrzyj wyniki** i przejrzyj błędy w **okienku Lista błędów.** 
1. Zapisz projekt lokalnie w celu skorygowania schematu w trybie offline. W tym celu wybierz **pozycję Plik Zapisz**  >  **projekt.** Daje to możliwość oceny schematów źródłowych i docelowych w trybie offline i wykonania korygowania przed opublikowaniem ich w bazie danych SQL.

## <a name="migrate-the-databases"></a>Migrowanie baz danych

Po ocenie baz danych i wylieniu wszelkich rozbieżności można uruchomić proces migracji. Migrowanie danych to operacja ładowania zbiorczego, która przenosi wiersze danych do bazy Azure SQL w transakcjach. Liczba wierszy do załadowania do bazy danych SQL w każdej transakcji jest konfigurowana w ustawieniach projektu.

Aby opublikować schemat i przeprowadzić migrację danych przy użyciu usługi SSMA for Access, wykonaj następujące czynności: 

1. Jeśli jeszcze tego nie zrobiono, wybierz pozycję Połącz z Azure SQL Database **i** podaj szczegóły połączenia. 

1. Opublikuj schemat. W **okienku Azure SQL Database Metadata Explorer** (Eksplorator metadanych) kliknij prawym przyciskiem myszy bazę danych, z która pracujesz, a następnie wybierz pozycję **Synchronizuj** z bazą danych . Ta akcja publikuje schemat MySQL w bazie danych SQL.

1. W **okienku Synchronizuj z bazą** danych przejrzyj mapowanie między projektem źródłowym a obiektem docelowym:

   ![Zrzut ekranu przedstawiający okienko "Synchronizuj z bazą danych" służące do przeglądania synchronizacji z bazą danych.](./media/access-to-sql-database-guide/synchronize-with-database-review.png)

1. W **okienku Eksplorator metadanych** dostępu zaznacz pola wyboru obok elementów, które chcesz migrować. Aby przeprowadzić migrację całej bazy danych, zaznacz pole wyboru obok tej bazy danych. 

1. Migrowanie danych. Kliknij prawym przyciskiem myszy bazę danych lub obiekt, który chcesz zmigrować, a następnie wybierz pozycję **Migruj dane.** Alternatywnie możesz wybrać kartę **Migruj dane** w prawym górnym rogu.  

   Aby przeprowadzić migrację danych dla całej bazy danych, zaznacz pole wyboru obok nazwy bazy danych. Aby przeprowadzić migrację danych z poszczególnych tabel, rozwiń bazę danych, rozwiń pozycję **Tabele**, a następnie zaznacz pole wyboru obok tabeli. Aby pominąć dane z poszczególnych tabel, wyczyść pole wyboru.

    ![Zrzut ekranu przedstawiający polecenie "Migruj dane" w okienku "Uzyskiwanie dostępu do eksploratora metadanych".](./media/access-to-sql-database-guide/migrate-data.png)

1. Po zakończeniu migracji wyświetl raport **migracji danych**.  

    ![Zrzut ekranu przedstawiający okienko "Migrowanie raportu danych" z przykładowym raportem do przeglądu.](./media/access-to-sql-database-guide/migrate-data-review.png)

1. Połącz się z bazą Azure SQL danych przy [użyciu SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms), a następnie zweryfikuj migrację, przeglądając dane i schemat.

   ![Zrzut ekranu SQL Server Management Studio Eksplorator obiektów sprawdzania poprawności migracji w programie SSMA.](./media/access-to-sql-database-guide/validate-data.png)

## <a name="post-migration"></a>Po migracji 

Po pomyślnym ukończeniu  etapu migracji należy wykonać serię zadań po migracji, aby upewnić się, że wszystko działa tak bezproblemowo i wydajnie, jak to możliwe.

### <a name="remediate-applications"></a>Korygowanie aplikacji

Po migracji danych do środowiska docelowego wszystkie aplikacje, które wcześniej zużywały źródło, muszą zacząć korzystać z obiektu docelowego. W niektórych przypadkach będzie to wymagało zmian w aplikacjach.

### <a name="perform-tests"></a>Wykonywanie testów

Testowe podejście do migracji bazy danych obejmuje następujące działania:

1. **Opracowywanie testów weryfikacyjnych:** aby przetestować migrację bazy danych, należy użyć zapytań SQL. Należy utworzyć zapytania weryfikacji, aby uruchamiać je zarówno względem źródłowej, jak i docelowej bazy danych. Zapytania weryfikacji powinny obejmować zdefiniowany zakres.

1. **Konfigurowanie środowiska testowego:** środowisko testowe powinno zawierać kopię źródłowej bazy danych i docelowej bazy danych. Pamiętaj, aby odizolować środowisko testowe.

1. **Uruchamianie testów walidacji:** uruchom testy weryfikacyjne względem źródła i obiektu docelowego, a następnie przeanalizuj wyniki.

1. **Uruchamianie testów wydajnościowych:** uruchom testy wydajnościowe względem źródła i obiektu docelowego, a następnie przeanalizuj i porównaj wyniki.


### <a name="optimize"></a>Optymalizacja

Faza po migracji ma kluczowe znaczenie dla uzgadniania wszelkich problemów z dokładnością danych, weryfikowania kompletności i rozwiązywania problemów z wydajnością obciążenia.

Aby uzyskać więcej informacji na temat tych problemów i czynności w celu ich ograniczenia, zobacz Przewodnik po [weryfikacji i optymalizacji](/sql/relational-databases/post-migration-validation-and-optimization-guide)po migracji.

## <a name="migration-assets"></a>Zasoby migracji 

Aby uzyskać więcej pomocy przy ukończeniu tego scenariusza migracji, zobacz następujący zasób. Został on opracowany w celu wspierania rzeczywistego zaangażowania projektu migracji.

| Tytuł | Opis |
| --- | --- |
| [Model i narzędzie do oceny obciążenia danych](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Zapewnia sugerowane "najlepsze dopasowanie" platform docelowych, gotowość do chmury oraz poziomy korygowania aplikacji/bazy danych dla określonych obciążeń. Oferuje proste obliczenia i generowanie raportów jednym kliknięciem, które pomagają przyspieszyć ocenę dużych nieruchomości, zapewniając zautomatyzowany, jednolity proces podejmowania decyzji na platformie docelowej. |

Zespół inżynierów ds. danych SQL opracował te zasoby. Podstawowym procesem tego zespołu jest odblokowanie i przyspieszenie złożonej modernizacji projektów migracji platformy danych na platformę danych Microsoft Azure.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać macierz usług i narzędzi firmy Microsoft oraz innych firm, które są dostępne w celu pomocy w różnych scenariuszach migracji bazy danych i danych oraz zadaniach specjalnych, zobacz Service and tools for data migration (Usługi i narzędzia do migracji [danych).](../../../dms/dms-tools-matrix.md)

- Aby dowiedzieć się więcej o Azure SQL Database zobacz:
   - [Omówienie SQL Database](../../database/sql-database-paas-overview.md)
   - [Kalkulator całkowitego kosztu posiadania platformy Azure](https://azure.microsoft.com/pricing/tco/calculator/) 


- Aby dowiedzieć się więcej na temat struktury i cyklu wdrożenia migracji do chmury, zobacz:
   -  [Przewodnik Cloud Adoption Framework dla platformy Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Najlepsze rozwiązania dotyczące wyceny i rozmiarów obciążeń na potrzeby migracji na platformę Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 
   -  [Migracja do chmury zasobów](https://azure.microsoft.com/migration/resources)


- Aby ocenić warstwę dostępu do aplikacji, zobacz [Data Access Migration Toolkit (wersja zapoznawcza).](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- Aby uzyskać informacje na temat sposobu wykonywania testowania warstwy A/B dostępu do danych, zobacz [Omówienie Asystent eksperymentowania z bazą danych](/sql/dea/database-experimentation-assistant-overview).
