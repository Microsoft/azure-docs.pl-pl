---
title: 'Dostęp do Azure SQL Database: Przewodnik migracji'
description: W tym przewodniku nauczysz się migrować bazy danych programu Microsoft Access do Azure SQL Database przy użyciu Asystent migracji do programu SQL Server na potrzeby dostępu (ASYSTENCIE migracji for Access).
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: e323b1c15d78da4e8c1a82ae8848df7f59b0dd87
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104657360"
---
# <a name="migration-guide-access-to-azure-sql-database"></a>Przewodnik migracji: dostęp do Azure SQL Database

W tym przewodniku migracji nauczysz się migrować bazy danych programu Microsoft Access do Azure SQL Database przy użyciu Asystent migracji do programu SQL Server w celu uzyskania dostępu.

W przypadku innych przewodników migracji zapoznaj się z tematem [migracja bazy danych](https://datamigration.microsoft.com/). 

## <a name="prerequisites"></a>Wymagania wstępne

Aby przeprowadzić migrację bazy danych programu Access do Azure SQL Database, potrzebne są:

- Aby sprawdzić, czy środowisko źródłowe jest obsługiwane. 
- [Asystent migracji do programu SQL Server dostępu](https://www.microsoft.com/download/details.aspx?id=54255). 

## <a name="pre-migration"></a>Przed migracją

Po spełnieniu wymagań wstępnych można przystąpić do odnajdywania topologii środowiska i oceny wykonalności migracji.


### <a name="assess"></a>Ocena 

Utwórz ocenę przy użyciu [Asystent migracji do programu SQL Server, aby uzyskać dostęp](https://www.microsoft.com/download/details.aspx?id=54255). 

Aby utworzyć ocenę, wykonaj następujące kroki: 

1. Otwórz Asystent migracji do programu SQL Server, aby uzyskać dostęp. 
1. Wybierz pozycję **plik** , a następnie wybierz pozycję **Nowy projekt**. Podaj nazwę projektu migracji. 

   ![Wybierz nowy projekt](./media/access-to-sql-database-guide/new-project.png)

1. Wybierz pozycję **Dodaj bazy danych** i wybierz bazy danych, które mają zostać dodane do nowego projektu. 

   ![Wybierz pozycję Dodaj bazy danych](./media/access-to-sql-database-guide/add-databases.png)

1. W **Eksploratorze metadanych programu Access** kliknij prawym przyciskiem myszy bazę danych, a następnie wybierz polecenie **Utwórz raport**. 

   ![Kliknij prawym przyciskiem myszy bazę danych, a następnie wybierz polecenie Utwórz raport.](./media/access-to-sql-database-guide/create-report.png)

1. Zapoznaj się z przykładową oceną. Na przykład: 

   ![Zapoznaj się z przykładową oceną raportu](./media/access-to-sql-database-guide/sample-assessment.png)

### <a name="validate-data-types"></a>Sprawdzanie poprawności typów danych

Sprawdź poprawność domyślnych mapowań typu danych i zmień je w zależności od wymagań, jeśli jest to konieczne. W tym celu wykonaj następujące czynności:

1. Wybierz pozycję **Narzędzia** z menu. 
1. Wybierz pozycję **Ustawienia projektu**. 
1. Wybierz kartę **mapowania typu** . 

   ![Mapowania typów](./media/access-to-sql-database-guide/type-mappings.png)

1. Można zmienić mapowanie typu dla każdej tabeli, wybierając tabelę w **Eksploratorze metadanych dostępu**.


### <a name="convert-schema"></a>Konwertuj schemat

Aby skonwertować obiekty bazy danych, wykonaj następujące kroki: 

1. Wybierz pozycję **Połącz z Azure SQL Database** i podaj szczegóły połączenia.

   ![Łączenie z bazą danych Azure SQL Database](./media/access-to-sql-database-guide/connect-to-sqldb.png)

1. Kliknij prawym przyciskiem myszy bazę danych w **Eksploratorze metadanych programu Access** i wybierz polecenie **Konwertuj schemat**. Alternatywnie możesz wybrać opcję **Konwertuj schemat** z górnego paska nawigacyjnego po wybraniu bazy danych.

   ![Kliknij prawym przyciskiem myszy bazę danych i wybierz polecenie Konwertuj schemat](./media/access-to-sql-database-guide/convert-schema.png)

   Porównaj przekonwertowane zapytania z oryginalnymi zapytaniami: 

   ![Skonwertowane zapytania można porównać z kodem źródłowym](./media/access-to-sql-database-guide/query-comparison.png)

   Porównaj przekonwertowane obiekty z oryginalnymi obiektami: 

   ![Skonwertowane obiekty można porównać ze źródłem](./media/access-to-sql-database-guide/table-comparison.png)

1. Obowiązkowe Aby skonwertować pojedynczy obiekt, kliknij prawym przyciskiem myszy obiekt i wybierz polecenie **Konwertuj schemat**. Skonwertowane obiekty są pogrubione w **Eksploratorze metadanych dostępu**: 

   ![Przekonwertowane obiekty pogrubione w Eksploratorze metadanych](./media/access-to-sql-database-guide/converted-items.png)
 
1. Wybierz pozycję **przegląd wyników** w okienku dane wyjściowe i Przejrzyj błędy w okienku **Lista błędów** . 


## <a name="migrate"></a>Migrate

Po zakończeniu oceniania baz danych i rozwiązaniu jakichkolwiek rozbieżności następnym krokiem jest wykonanie procesu migracji. Migrowanie danych jest operacją ładowania zbiorczego, która przenosi wiersze danych do Azure SQL Database w transakcjach. Liczba wierszy, które mają zostać załadowane do Azure SQL Database w każdej transakcji jest skonfigurowana w ustawieniach projektu.

Aby przeprowadzić migrację danych przy użyciu usługi ASYSTENCIE migracji w celu uzyskania dostępu, wykonaj następujące kroki: 

1. Jeśli jeszcze tego nie zrobiono, wybierz pozycję **Połącz z Azure SQL Database** i podaj szczegóły połączenia. 
1. Kliknij prawym przyciskiem myszy bazę danych w **Eksploratorze metadanych Azure SQL Database** i wybierz polecenie **Synchronizuj z bazą danych**. Ta akcja powoduje opublikowanie schematu MySQL w Azure SQL Database.

   ![Synchronizuj z bazą danych](./media/access-to-sql-database-guide/synchronize-with-database.png)

   Przejrzyj mapowanie między projektem źródłowym a obiektem docelowym:

   ![Przegląd synchronizacji z bazą danych](./media/access-to-sql-database-guide/synchronize-with-database-review.png)

1. Użyj **Eksploratora metadanych dostępu** , aby zaznaczyć pola obok elementów, które chcesz zmigrować. Jeśli chcesz migrować całą bazę danych, zaznacz pole wyboru obok bazy danych. 
1. Kliknij prawym przyciskiem myszy bazę danych lub obiekt, który chcesz zmigrować, a następnie wybierz polecenie **Migruj dane**. 
   Aby migrować dane dla całej bazy danych, zaznacz pole wyboru obok nazwy bazy danych. Aby przeprowadzić migrację danych z pojedynczych tabel, rozwiń bazę danych, rozwiń węzeł tabele, a następnie zaznacz pole wyboru obok tabeli. Aby pominąć dane z poszczególnych tabel, wyczyść to pole wyboru.

    ![Migrowanie danych](./media/access-to-sql-database-guide/migrate-data.png)

    Przejrzyj zmigrowane dane: 

    ![Migrowanie przeglądu danych](./media/access-to-sql-database-guide/migrate-data-review.png)

1. Połącz się z Azure SQL Database przy użyciu [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) i sprawdź poprawność migracji, przeglądając dane i schemat.

   ![Weryfikuj w ASYSTENCIE migracji](./media/access-to-sql-database-guide/validate-data.png)



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

| **Tytuł/link**                                                                                                                                          | **Opis**                                                                                                                                                                                                                                                                                                                              |
| ------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Model i narzędzie oceny obciążenia danych](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | To narzędzie zapewnia sugerowane Platformy docelowe, gotowość chmury oraz poziom korygowania aplikacji/bazy danych dla danego obciążenia. Oferuje proste, oparte na jednym kliknięcie Obliczanie i generowanie raportów, które znacznie ułatwiają przyspieszenie oceny dużych ilości, zapewniając i zautomatyzowany i jednolity proces podejmowania decyzji platformy docelowej. |


Te zasoby zostały opracowane w ramach programu SQL Data ninja, który jest sponsorowany przez zespół inżynierów grupy danych platformy Azure. Podstawowa karta programu SQL Data Ninja to odblokowanie i przyspieszenie złożonej modernizacji i konkurowania możliwości migracji platformy danych na platformę danych platformy Microsoft Azure. Jeśli uważasz, że Twoja organizacja będzie chciała uczestniczyć w programie SQL Data ninja, skontaktuj się z zespołem ds. kont i poproś o przesłanie nominacji.

## <a name="next-steps"></a>Następne kroki

- Macierz usług i narzędzi firmy Microsoft i innych firm, które są dostępne w celu ułatwienia pracy z różnymi scenariuszami bazy danych i migracji danych oraz zadaniami specjalistycznymi, można znaleźć w temacie [Usługa i narzędzia do migracji danych](../../../dms/dms-tools-matrix.md).

- Aby dowiedzieć się więcej na temat Azure SQL Database, zobacz:
   - [Omówienie SQL Database](../../database/sql-database-paas-overview.md)
   - [Kalkulator całkowitego kosztu posiadania na platformę Azure](https://azure.microsoft.com/pricing/tco/calculator/) 


- Aby dowiedzieć się więcej na temat platformy i cyklu wdrażania migracji w chmurze, zobacz
   -  [Przewodnik Cloud Adoption Framework dla platformy Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Najlepsze rozwiązania związane z wyceną i ustalaniem wielkości obciążeń migracji na platformę Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Aby ocenić warstwę dostępu do aplikacji, zobacz [Data Access Migration Toolkit (wersja zapoznawcza)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- Aby uzyskać szczegółowe informacje na temat przeprowadzania testów warstwy dostępu do danych A/B, zobacz [Asystent eksperymentowania z bazą danych](/sql/dea/database-experimentation-assistant-overview).

