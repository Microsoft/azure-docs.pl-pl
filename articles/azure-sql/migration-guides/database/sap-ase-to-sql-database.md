---
title: 'Oprogramowanie SAP ASE do Azure SQL Database: Przewodnik migracji'
description: W tym przewodniku przedstawiono sposób migrowania baz danych SAP ASE do Azure SQL Database przy użyciu Asystent migracji do programu SQL Server dla oprogramowania SAP adapter Server Enterprise.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: fd03ebc87a1c0ef0a55b0e6ac0be6d841fee4b0a
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105027299"
---
# <a name="migration-guide-sap-ase-to-azure-sql-database"></a>Przewodnik migracji: oprogramowanie SAP ASE do Azure SQL Database
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

W tym przewodniku przedstawiono sposób migrowania baz danych SAP ASE do Azure SQL Database przy użyciu Asystent migracji do programu SQL Server dla oprogramowania SAP adapter Server Enterprise.

W przypadku innych przewodników migracji zapoznaj się z tematem [migracja bazy danych](https://docs.microsoft.com/data-migration). 

## <a name="prerequisites"></a>Wymagania wstępne 

Aby przeprowadzić migrację bazy danych SAP SE do Azure SQL Database, potrzebne są:

- Aby sprawdzić, czy środowisko źródłowe jest obsługiwane. 
- [Asystent migracji do programu SQL Server dla oprogramowania SAP adaptacyjnego Server Enterprise (dawniej SAP Sybase ASE)](https://www.microsoft.com/en-us/download/details.aspx?id=54256). 
- Łączność i wystarczające uprawnienia dostępu do źródła i celu. 


## <a name="pre-migration"></a>Przed migracją

Po spełnieniu wymagań wstępnych można przystąpić do odnajdywania topologii środowiska i oceny wykonalności migracji.

### <a name="assess"></a>Ocena

Użyj [Asystent migracji do programu SQL Server (Asystencie migracji) dla oprogramowania SAP adaptacyjnego serwera Enterprise (formularz SAP ASE)](https://www.microsoft.com/en-us/download/details.aspx?id=54256) do przeglądania obiektów i danych bazy danych, oceniania baz danych do migracji, migrowania obiektów bazy danych programu Sybase do Azure SQL Database, a następnie migrowania danych do Azure SQL Database. Aby dowiedzieć się więcej, zobacz [Asystent migracji do programu SQL Server for Sybase (SybaseToSQL)](/sql/ssma/sybase/sql-server-migration-assistant-for-sybase-sybasetosql).

Aby utworzyć ocenę, wykonaj następujące kroki: 

1. Otwórz **Asystencie migracji dla programu Sybase**. 
1. Wybierz pozycję **plik** , a następnie wybierz pozycję **Nowy projekt**. 
1. Podaj nazwę projektu, lokalizację do zapisania projektu, a następnie wybierz Azure SQL Database jako cel migracji z listy rozwijanej. Wybierz przycisk **OK**.
1. Wprowadź wartości w polach szczegóły połączenia SAP w oknie dialogowym **łączenie z programem Sybase** . 
1. Kliknij prawym przyciskiem myszy bazę danych SAP, którą chcesz zmigrować, a następnie wybierz polecenie **Utwórz raport**. Spowoduje to wygenerowanie raportu HTML. Alternatywnie możesz wybrać opcję **Utwórz raport** na pasku nawigacyjnym po wybraniu bazy danych:
1. Przejrzyj raport HTML, aby poznać statystyki konwersji oraz błędy lub ostrzeżenia. Możesz również otworzyć raport w programie Excel, aby uzyskać spis obiektów SAP ASE i nakład pracy wymagany do przeprowadzenia konwersji schematu. Domyślna lokalizacja raportu znajduje się w folderze raportów w SSMAProjects.

   Na przykład: `drive:\<username>\Documents\SSMAProjects\MySAPMigration\report\report_<date>`. 


### <a name="validate-type-mappings"></a>Weryfikuj mapowania typów

Przed wykonaniem konwersji schematu Sprawdź poprawność domyślnych mapowań typów danych lub zmień je na podstawie wymagań. Można to zrobić, przechodząc do menu **Narzędzia** i wybierając pozycję **Ustawienia projektu** lub można zmienić mapowanie typu dla każdej tabeli, wybierając tabelę w **Eksploratorze metadanych oprogramowania SAP ASE**.


### <a name="convert-schema"></a>Konwertuj schemat

Aby przekonwertować schemat, wykonaj następujące kroki:

1. Obowiązkowe Aby skonwertować kwerendy dynamiczne lub ad hoc, kliknij prawym przyciskiem myszy węzeł i wybierz polecenie **Dodaj instrukcję**. 
1. Wybierz pozycję **Połącz z Azure SQL Database** na pasku nawigacyjnym najwyższego wiersza i podaj Azure SQL Database szczegóły. Można nawiązać połączenie z istniejącą bazą danych lub podać nową nazwę, w takim przypadku na serwerze docelowym zostanie utworzona baza danych.
1. Kliknij prawym przyciskiem myszy schemat SAP ASE w **Eksploratorze metadanych programu Sybase** i wybierz polecenie **Konwertuj schemat**. Alternatywnie możesz wybrać opcję **Konwertuj schemat** z górnego paska nawigacyjnego. 
1. Porównaj i przejrzyj strukturę schematu, aby zidentyfikować potencjalne problemy. 

   Po konwersji schematu można zapisać ten projekt lokalnie dla ćwiczenia korygowania schematu w trybie offline. Wybierz pozycję **Zapisz projekt** z menu **plik** . Dzięki temu można oszacować schematy źródłowe i docelowe w trybie offline i przeprowadzić korygowanie, zanim będzie można opublikować schemat w Azure SQL Database.

1. Wybierz pozycję **przegląd wyników** w okienku dane wyjściowe i Przejrzyj błędy w okienku **Lista błędów** . 
1. Zapisz projekt lokalnie dla ćwiczenia korygowania schematu w trybie offline. Wybierz pozycję **Zapisz projekt** z menu **plik** . Dzięki temu można oszacować schematy źródłowe i docelowe w trybie offline i przeprowadzić korygowanie, zanim będzie można opublikować schemat w SQL Database.

## <a name="migrate"></a>Migrate 

Po spełnieniu wymaganych wymagań wstępnych i ukończeniu zadań skojarzonych z etapem **sprzed migracji** można przystąpić do wykonania migracji schematu i danych.

Aby opublikować schemat i przeprowadzić migrację danych, wykonaj następujące kroki: 

1. Opublikuj schemat: kliknij prawym przyciskiem myszy bazę danych w **Eksploratorze metadanych Azure SQL Database** i wybierz polecenie **Synchronizuj z bazą danych**.  Ta akcja powoduje opublikowanie schematu SAP ASE w wystąpieniu Azure SQL Database.
1. Migruj dane: kliknij prawym przyciskiem myszy bazę danych lub obiekt, który chcesz zmigrować w **Eksploratorze metadanych oprogramowania SAP ASE**, a następnie wybierz polecenie **Migruj dane**. Alternatywnie możesz wybrać opcję **Migruj dane** z górnego paska nawigacyjnego. Aby migrować dane dla całej bazy danych, zaznacz pole wyboru obok nazwy bazy danych. Aby przeprowadzić migrację danych z pojedynczych tabel, rozwiń bazę danych, rozwiń węzeł tabele, a następnie zaznacz pole wyboru obok tabeli. Aby pominąć dane z poszczególnych tabel, wyczyść pole wyboru: 
1. Po zakończeniu migracji Wyświetl **raport dotyczący migracji danych**: 
1. Połącz się z Azure SQL Database przy użyciu [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) i sprawdź poprawność migracji, przeglądając dane i schemat. 


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

> [!NOTE]
> Aby uzyskać dodatkowe informacje o tych problemach i konkretnych krokach, które należy rozwiązać, zobacz [Przewodnik po weryfikacji i optymalizacji po migracji](/sql/relational-databases/post-migration-validation-and-optimization-guide).


## <a name="next-steps"></a>Następne kroki

- Macierz usług i narzędzi firmy Microsoft i innych firm, które są dostępne w celu ułatwienia pracy z różnymi scenariuszami bazy danych i migracji danych oraz zadaniami specjalistycznymi, można znaleźć w temacie [Usługa i narzędzia do migracji danych](../../../dms/dms-tools-matrix.md).

- Aby dowiedzieć się więcej na temat Azure SQL Database, zobacz:
   - [Omówienie SQL Database](../../database/sql-database-paas-overview.md)
   - [Kalkulator całkowitego kosztu posiadania na platformę Azure](https://azure.microsoft.com/pricing/tco/calculator/) 


- Aby dowiedzieć się więcej na temat platformy i cyklu wdrażania migracji w chmurze, zobacz
   -  [Podręcznik Cloud Adoption Framework dla platformy Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Najlepsze rozwiązania związane z wyceną i ustalaniem wielkości obciążeń migracji na platformę Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Aby ocenić warstwę dostępu do aplikacji, zobacz [Data Access Migration Toolkit (wersja zapoznawcza)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- Aby uzyskać szczegółowe informacje na temat przeprowadzania testów warstwy dostępu do danych A/B, zobacz [Asystent eksperymentowania z bazą danych](/sql/dea/database-experimentation-assistant-overview).
