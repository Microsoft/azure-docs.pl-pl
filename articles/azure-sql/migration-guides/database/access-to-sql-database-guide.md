---
title: 'Dostęp do Azure SQL Database: Przewodnik migracji'
description: W tym przewodniku dowiesz się, jak migrować bazy danych programu Microsoft Access do bazy danych Azure SQL Database przy użyciu Asystent migracji do programu SQL Server dostępu (ASYSTENCIE migracji for Access).
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: 48fe734b382d661f96a86ede181a1258e38120a1
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2021
ms.locfileid: "105626541"
---
# <a name="migration-guide-access-to-azure-sql-database"></a>Przewodnik migracji: dostęp do Azure SQL Database

W tym przewodniku dowiesz się, jak przeprowadzić migrację bazy danych programu Microsoft Access do bazy danych Azure SQL Database przy użyciu Asystent migracji do programu SQL Server dostępu (ASYSTENCIE migracji for Access).

Aby poznać inne przewodniki dotyczące migracji, zobacz [Przewodnik migracji usługi Azure Database](https://docs.microsoft.com/data-migration). 

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem migrowania bazy danych programu Access do bazy danych SQL wykonaj następujące czynności:

- Sprawdź, czy środowisko źródłowe jest obsługiwane. 
- Pobierz i zainstaluj [Asystent migracji do programu SQL Server, aby uzyskać dostęp](https://www.microsoft.com/download/details.aspx?id=54255).
- Upewnij się, że masz łączność i wystarczające uprawnienia dostępu do źródła i celu.

## <a name="pre-migration"></a>Przed migracją

Po spełnieniu wymagań wstępnych można przystąpić do odnajdywania topologii środowiska i oceny wykonalności migracji.


### <a name="assess"></a>Ocena 

Za pomocą programu ASYSTENCIE migracji można przeglądać obiekty bazy danych i dane oraz oceniać bazy danych do migracji. 

Aby utworzyć ocenę, wykonaj następujące czynności: 

1. Otwórz [Asystencie migracji, aby uzyskać dostęp](https://www.microsoft.com/download/details.aspx?id=54255). 
1. Wybierz pozycję **plik**, a następnie wybierz pozycję **Nowy projekt**. 
1. Podaj nazwę projektu i lokalizację projektu, a następnie na liście rozwijanej wybierz **Azure SQL Database** jako cel migracji. 
1. Wybierz przycisk **OK**. 

   ![Zrzut ekranu przedstawiający okienko "nowy projekt" służący do wprowadzania nazwy i lokalizacji projektu migracji.](./media/access-to-sql-database-guide/new-project.png)

1. Wybierz pozycję **Dodaj bazy danych**, a następnie wybierz bazy danych, które mają zostać dodane do nowego projektu. 

   ![Zrzut ekranu przedstawiający kartę "Dodawanie baz danych" w programie ASYSTENCIE migracji, aby uzyskać dostęp.](./media/access-to-sql-database-guide/add-databases.png)

1. W okienku **dostęp do Eksploratora metadanych** kliknij prawym przyciskiem myszy bazę danych, a następnie wybierz polecenie **Utwórz raport**. Alternatywnie możesz wybrać kartę **Utwórz raport** w prawym górnym rogu.

   ![Zrzut ekranu przedstawiający polecenie "Utwórz raport" w Eksploratorze metadanych dostępu.](./media/access-to-sql-database-guide/create-report.png)

1. Przejrzyj raport HTML, aby poznać statystyki konwersji oraz błędy lub ostrzeżenia. Możesz również otworzyć raport w programie Excel, aby uzyskać spis obiektów dostępu i zrozumieć nakład pracy wymagany do wykonania konwersji schematu. Domyślna lokalizacja raportu znajduje się w folderze raportów w SSMAProjects. Na przykład:

   `drive:\<username>\Documents\SSMAProjects\MyAccessMigration\report\report_<date>`

   ![Zrzut ekranu przykładowej oceny raportu bazy danych w ASYSTENCIE migracji.](./media/access-to-sql-database-guide/sample-assessment.png)

### <a name="validate-the-data-types"></a>Sprawdzanie poprawności typów danych

Sprawdź poprawność domyślnych mapowań typów danych i zmień je w zależności od potrzeb, w razie potrzeby. W tym celu:

1. W programie ASYSTENCIE migracji for Access wybierz pozycję **Narzędzia**, a następnie wybierz pozycję **Ustawienia projektu**. 
1. Wybierz kartę **Mapowanie typu** . 

   ![Zrzut ekranu okienka "Mapowanie typów" w ASYSTENCIE migracji, aby uzyskać dostęp.](./media/access-to-sql-database-guide/type-mappings.png)

1. Można zmienić mapowanie typu dla każdej tabeli, wybierając nazwę tabeli w okienku **Eksploratora metadanych dostępu** .


### <a name="convert-the-schema"></a>Konwertuj schemat

Aby skonwertować obiekty bazy danych, wykonaj następujące czynności: 

1. Wybierz kartę **Połącz z Azure SQL Database** , a następnie wykonaj następujące czynności:

   a. Wprowadź szczegóły dotyczące łączenia się z bazą danych SQL.  
   b. Z listy rozwijanej wybierz docelową bazę danych SQL. Możesz też wprowadzić nową nazwę, w której przypadku zostanie utworzona baza danych na serwerze docelowym.  
   c. Podaj szczegóły uwierzytelniania.   
   d. Wybierz pozycję **Połącz**.

   ![Zrzut ekranu okienka "łączenie z Azure SQL Database", aby wprowadzić szczegóły połączenia.](./media/access-to-sql-database-guide/connect-to-sqldb.png)

1. W okienku **dostęp do Eksploratora metadanych** kliknij prawym przyciskiem myszy bazę danych, a następnie wybierz polecenie **Konwertuj schemat**. Alternatywnie możesz wybrać swoją bazę danych, a następnie wybrać kartę **Konwertuj schemat** .

   ![Zrzut ekranu przedstawiający polecenie "Konwertuj schemat" w okienku "Eksplorator metadanych dostępu".](./media/access-to-sql-database-guide/convert-schema.png)

1. Po zakończeniu konwersji Porównaj przekonwertowane obiekty z oryginalnymi obiektami, aby zidentyfikować potencjalne problemy i rozwiązać problemy na podstawie zaleceń.

   ![Zrzut ekranu przedstawiający porównanie przekonwertowanych obiektów do obiektów źródłowych.](./media/access-to-sql-database-guide/table-comparison.png)

    Porównaj przekonwertowany tekst języka Transact-SQL z oryginalnym kodem i zapoznaj się z zaleceniami.

   ![Zrzut ekranu przedstawiający porównanie przekonwertowanych zapytań do kodu źródłowego.](./media/access-to-sql-database-guide/query-comparison.png) 

1. Obowiązkowe Aby skonwertować pojedynczy obiekt, kliknij prawym przyciskiem myszy obiekt, a następnie wybierz polecenie **Konwertuj schemat**. Skonwertowane obiekty są wyświetlane w postaci pogrubionego tekstu w **Eksploratorze metadanych programu Access**: 

   ![Zrzut ekranu przedstawiający, że obiekty w Eksploratorze metadanych dostępu są konwertowane.](./media/access-to-sql-database-guide/converted-items.png)
 
1. W okienku **danych wyjściowych** wybierz ikonę **przegląd wyników** i Przejrzyj błędy w okienku **Lista błędów** . 
1. Zapisz projekt lokalnie dla ćwiczenia korygowania schematu w trybie offline. W tym celu wybierz pozycję **plik**  >  **Zapisz projekt**. Dzięki temu można oszacować schematy źródłowe i docelowe w trybie offline i przeprowadzić korygowanie przed opublikowaniem ich w bazie danych SQL.

## <a name="migrate-the-databases"></a>Migrowanie baz danych

Po przeprowadzeniu oceny baz danych i rozpoczęciu wszelkich rozbieżności można uruchomić proces migracji. Migrowanie danych jest operacją ładowania zbiorczego, która przenosi wiersze danych do usługi Azure SQL Database w ramach transakcji. Liczba wierszy do załadowania do bazy danych SQL w każdej transakcji jest konfigurowana w ustawieniach projektu.

Aby opublikować schemat i przeprowadzić migrację danych przy użyciu usługi ASYSTENCIE migracji w celu uzyskania dostępu, wykonaj następujące czynności: 

1. Jeśli jeszcze tego nie zrobiono, wybierz pozycję **Połącz z Azure SQL Database** i podaj szczegóły połączenia. 

1. Opublikuj schemat. W okienku **Azure SQL Database Metadata Explorer** kliknij prawym przyciskiem myszy bazę danych, z którą pracujesz, a następnie wybierz polecenie **Synchronizuj z bazą danych**. Ta akcja powoduje opublikowanie schematu MySQL w bazie danych SQL.

1. W okienku **Synchronizuj z bazą danych** Przejrzyj mapowanie między projektem źródłowym a obiektem docelowym:

   ![Zrzut ekranu przedstawiający okienko "Synchronizuj z bazą danych" w celu przejrzenia synchronizacji z bazą danych.](./media/access-to-sql-database-guide/synchronize-with-database-review.png)

1. W okienku **dostęp do Eksploratora metadanych** zaznacz pola wyboru obok elementów, które chcesz zmigrować. Aby przeprowadzić migrację całej bazy danych, zaznacz pole wyboru obok bazy danych. 

1. Migruj dane. Kliknij prawym przyciskiem myszy bazę danych lub obiekt, który chcesz zmigrować, a następnie wybierz polecenie **Migruj dane**. Alternatywnie możesz wybrać kartę **Migrowanie danych** w prawym górnym rogu.  

   Aby migrować dane dla całej bazy danych, zaznacz pole wyboru obok nazwy bazy danych. Aby przeprowadzić migrację danych z pojedynczych tabel, rozwiń bazę danych, rozwiń węzeł **tabele**, a następnie zaznacz pole wyboru obok tabeli. Aby pominąć dane z poszczególnych tabel, wyczyść to pole wyboru.

    ![Zrzut ekranu przedstawiający polecenie "Migrowanie danych" w okienku "Eksplorator metadanych dostępu".](./media/access-to-sql-database-guide/migrate-data.png)

1. Po zakończeniu migracji Wyświetl **raport dotyczący migracji danych**.  

    ![Zrzut ekranu okienka "Migrowanie danych raportu" pokazujący przykładowy raport do przeglądu.](./media/access-to-sql-database-guide/migrate-data-review.png)

1. Nawiąż połączenie z bazą danych Azure SQL Database przy użyciu [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)i Zweryfikuj migrację, przeglądając dane i schemat.

   ![Zrzut ekranu przedstawiający SQL Server Management Studio Eksplorator obiektów do sprawdzania poprawności migracji w ASYSTENCIE migracji.](./media/access-to-sql-database-guide/validate-data.png)

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

Zespół inżynierów danych SQL Data opracował ten zasób. Podstawowa karta zespołu polega na odblokowaniu i przyspieszeniu złożonej modernizacji projektów migracji platformy danych do platformy danych platformy Microsoft Azure.

## <a name="next-steps"></a>Następne kroki

- Macierz usług i narzędzi firmy Microsoft i innych firm, które są dostępne w celu ułatwienia pracy z różnymi scenariuszami bazy danych i migracji danych oraz zadaniami specjalistycznymi, można znaleźć w temacie [Usługa i narzędzia do migracji danych](../../../dms/dms-tools-matrix.md).

- Aby dowiedzieć się więcej na temat Azure SQL Database, zobacz:
   - [Omówienie SQL Database](../../database/sql-database-paas-overview.md)
   - [Kalkulator całkowitego kosztu posiadania na platformę Azure](https://azure.microsoft.com/pricing/tco/calculator/) 


- Aby dowiedzieć się więcej na temat cyklu i wdrożenia migracji do chmury, zobacz:
   -  [Podręcznik Cloud Adoption Framework dla platformy Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Najlepsze rozwiązania dotyczące kosztów i rozmiarów obciążeń na potrzeby migracji na platformę Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Aby ocenić warstwę dostępu do aplikacji, zobacz [zestaw narzędzi do migracji dostępu do danych (wersja zapoznawcza)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit).
- Aby uzyskać informacje o sposobie wykonywania testów warstwy dostępu do danych A/B, zobacz [omówienie asystent eksperymentowania z bazą danych](/sql/dea/database-experimentation-assistant-overview).
