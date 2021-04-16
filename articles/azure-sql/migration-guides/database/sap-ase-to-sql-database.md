---
title: 'Sap ASE to Azure SQL Database: Migration guide (Przewodnik migracji środowiska SAP ASE do środowiska Azure SQL Database: migracja)'
description: Z tego przewodnika dowiesz się, jak przeprowadzić migrację baz danych środowiska SAP ASE do bazy danych Azure SQL przy użyciu programu Asystent migracji do programu SQL Server dla rozwiązania SAP Adapter Server Enterprise.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: 0538071ffb9d244fb8b3493d6b63b27c6b56a726
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388537"
---
# <a name="migration-guide-sap-ase-to-azure-sql-database"></a>Przewodnik migracji: środowiska SAP ASE do środowiska Azure SQL Database

[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

Z tego przewodnika [](https://azure.microsoft.com/migration/migration-journey) dowiesz się, jak przeprowadzić migrację baz danych SAP Adapter Server Enterprise (ASE) do bazy danych programu Azure SQL przy użyciu programu [SQL Server Migration](https://azure.microsoft.com/en-us/migration/sql-server/) Assistant dla systemu SAP Adapter Server Enterprise.

Aby uzyskać inne przewodniki dotyczące migracji, zobacz [Przewodnik po migracji bazy danych platformy Azure.](https://docs.microsoft.com/data-migration) 

## <a name="prerequisites"></a>Wymagania wstępne 

Przed rozpoczęciem migracji bazy danych SAP SE do bazy danych SQL database wykonaj następujące czynności:

- Sprawdź, czy środowisko źródłowe jest obsługiwane. 
- Pobierz i zainstaluj [Asystent migracji do programu SQL Server dla rozwiązania SAP Adaptive Server Enterprise (dawniej SAP Sybase ASE).](https://www.microsoft.com/en-us/download/details.aspx?id=54256)
- Upewnij się, że masz łączność i wystarczające uprawnienia, aby uzyskać dostęp do źródła i obiektu docelowego.

## <a name="pre-migration"></a>Przed migracją

Po spełnianiu wymagań wstępnych możesz wykryć topologię środowiska i ocenić możliwości migracji do [chmury platformy Azure.](https://azure.microsoft.com/migration)

### <a name="assess"></a>Ocena

Za pomocą programu [Asystent migracji do programu SQL Server (SSMA) dla rozwiązania SAP Adaptive Server Enterprise (formalnie SAP Sybase ASE)](https://www.microsoft.com/en-us/download/details.aspx?id=54256)można przeglądać obiekty i dane bazy danych, oceniać bazy danych pod celu migracji, migrować obiekty bazy danych Sybase do bazy danych SQL, a następnie migrować dane do bazy danych SQL. Aby dowiedzieć się więcej, [zobacz Asystent migracji do programu SQL Server dla programu Sybase (SybaseToSQL).](/sql/ssma/sybase/sql-server-migration-assistant-for-sybase-sybasetosql)

Aby utworzyć ocenę, wykonaj następujące czynności: 

1. Otwórz program SSMA dla bazy danych Sybase. 
1. Wybierz **pozycję File**(Plik), a następnie wybierz pozycję New Project **(Nowy projekt).** 
1. W **okienku Nowy** projekt wprowadź nazwę i lokalizację projektu, a następnie na liście rozwijanej Migruj do wybierz pozycję  **Azure SQL Database**. 
1. Wybierz przycisk **OK**.
1. W **okienku Łączenie z bazą danych Sybase** wprowadź szczegóły połączenia SAP. 
1. Kliknij prawym przyciskiem myszy bazę danych SAP, którą chcesz zmigrować, a następnie wybierz **pozycję Utwórz raport.** W ten sposób generowany jest raport HTML. Alternatywnie możesz wybrać kartę **Utwórz raport** w prawym górnym rogu.
1. Przejrzyj raport HTML, aby poznać statystyki konwersji i wszelkie błędy lub ostrzeżenia. Możesz również otworzyć raport w programie Excel, aby uzyskać spis obiektów środowiska SAP ASE i nakład pracy wymagany do przeprowadzenia konwersji schematu. Domyślna lokalizacja raportu znajduje się w folderze raportu w ramach SSMAProjects. Na przykład:

   `drive:\<username>\Documents\SSMAProjects\MySAPMigration\report\report_<date>` 

### <a name="validate-the-type-mappings"></a>Weryfikowanie mapowań typów

Przed przeprowadzeniem konwersji schematu zweryfikuj domyślne mapowania typów danych lub zmień je na podstawie wymagań. Możesz to zrobić, wybierając pozycję **Narzędzia** Ustawienia projektu lub zmieniając mapowanie typów dla każdej tabeli, wybierając tabelę w Eksploratorze metadanych środowiska  >   **SAP ASE.**

### <a name="convert-the-schema"></a>Konwertowanie schematu

Aby przekonwertować schemat, wykonaj następujące czynności:

1. (Opcjonalnie) Aby przekonwertować zapytania dynamiczne lub wyspecjalizowane, kliknij prawym przyciskiem myszy węzeł, a następnie wybierz polecenie **Dodaj instrukcje**. 
1. Wybierz **kartę Połącz Azure SQL Database,** a następnie wprowadź szczegóły bazy danych SQL. Możesz nawiązać połączenie z istniejącą bazą danych lub podać nową nazwę. W takim przypadku baza danych zostanie utworzona na serwerze docelowym.
1. W **okienku Eksplorator metadanych Sybase** kliknij prawym przyciskiem myszy schemat ŚRODOWISKA SAP ASE, z który pracujesz, a następnie wybierz polecenie **Konwertuj schemat.** 
1. Po przekonwertowaniu schematu porównaj i przejrzyj przekonwertowaną strukturę z oryginalną strukturą i zidentyfikuj potencjalne problemy. 

   Po konwersji schematu możesz zapisać ten projekt lokalnie na przykład w celu skorygowania schematu w trybie offline. W tym celu wybierz **pozycję Plik Zapisz**  >  **projekt.** Daje to możliwość oceny schematów źródłowych i docelowych w trybie offline i wykonania korygowania przed opublikowaniem schematu w bazie danych SQL.

1. W **okienku Dane** wyjściowe wybierz pozycję **Przejrzyj wyniki** i przejrzyj wszystkie błędy w **okienku Lista błędów.** 
1. Zapisz projekt lokalnie w celu skorygowania schematu w trybie offline. W tym celu wybierz **pozycję Plik Zapisz**  >  **projekt.** Daje to możliwość oceny schematów źródłowych i docelowych w trybie offline i wykonania korygowania przed opublikowaniem schematu w bazie danych SQL.

## <a name="migrate-the-databases"></a>Migrowanie baz danych 

Po przygotowaniu niezbędnych wymagań wstępnych i ukończeniu  zadań skojarzonych z etapem przed migracją można rozpocząć uruchamianie migracji schematu i danych.

Aby opublikować schemat i przeprowadzić migrację danych, wykonaj następujące czynności: 

1. Opublikuj schemat. W **okienku Azure SQL Database Metadata Explorer kliknij** prawym przyciskiem myszy bazę danych, a następnie wybierz pozycję **Synchronizuj z bazą danych**. Ta akcja publikuje schemat środowiska SAP ASE w bazie danych SQL.

1. Migrowanie danych. W **okienku Eksplorator** metadanych środowiska SAP ASE kliknij prawym przyciskiem myszy bazę danych lub obiekt ŚRODOWISKA SAP ASE, który chcesz zmigrować, a następnie wybierz pozycję **Migruj dane.** Alternatywnie możesz wybrać kartę **Migruj dane** w prawym górnym rogu. 

   Aby przeprowadzić migrację danych dla całej bazy danych, zaznacz pole wyboru obok nazwy bazy danych. Aby przeprowadzić migrację danych z poszczególnych tabel, rozwiń bazę danych, rozwiń pozycję **Tabele**, a następnie zaznacz pole wyboru obok tabeli. Aby pominąć dane z poszczególnych tabel, wyczyść pole wyboru. 
1. Po zakończeniu migracji wyświetl raport **migracji danych**. 
1. Zweryfikuj migrację, przeglądając dane i schemat. Aby to zrobić, połącz się z bazą danych SQL database przy użyciu [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).

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


## <a name="next-steps"></a>Następne kroki

- Aby uzyskać macierz usług i narzędzi firmy Microsoft oraz innych firm, które są dostępne w celu pomocy w różnych scenariuszach migracji bazy danych i danych oraz zadaniach specjalnych, zobacz Service and tools for data migration (Usługi i narzędzia do migracji [danych).](../../../dms/dms-tools-matrix.md)

- Aby dowiedzieć się więcej o Azure SQL Database, zobacz:
   - [Omówienie SQL Database](../../database/sql-database-paas-overview.md)
   - [Kalkulator całkowitego kosztu posiadania platformy Azure](https://azure.microsoft.com/pricing/tco/calculator/)  

- Aby dowiedzieć się więcej na temat struktury i cyklu wdrożenia migracji do chmury, zobacz:
   -  [Przewodnik Cloud Adoption Framework dla platformy Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Najlepsze rozwiązania dotyczące kosztów i rozmiarów obciążeń na potrzeby migracji na platformę Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 
   -  [Migracja do chmury zasobów](https://azure.microsoft.com/migration/resources)

- Aby ocenić warstwę dostępu do aplikacji, zobacz [Data Access Migration Toolkit (wersja zapoznawcza).](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- Aby uzyskać szczegółowe informacje na temat sposobu wykonywania testów A/B warstwy dostępu do danych, [zobacz Asystent eksperymentowania z bazą danych](/sql/dea/database-experimentation-assistant-overview).
