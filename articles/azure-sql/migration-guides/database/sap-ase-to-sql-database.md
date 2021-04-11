---
title: 'Oprogramowanie SAP ASE do Azure SQL Database: Przewodnik migracji'
description: W tym przewodniku dowiesz się, jak migrować bazy danych SAP ASE do bazy danych Azure SQL Database przy użyciu Asystent migracji do programu SQL Server dla oprogramowania SAP adapter Server Enterprise.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: f4648c216a0b6d06309c0166aba501d4f3f02a10
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107027520"
---
# <a name="migration-guide-sap-ase-to-azure-sql-database"></a>Przewodnik migracji: oprogramowanie SAP ASE do Azure SQL Database

[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

W tym przewodniku dowiesz się, [jak migrować](https://azure.microsoft.com/migration/migration-journey) bazy danych SAP adapter Server Enterprise (ASE) do bazy danych Azure SQL Database przy użyciu Asystenta [migracji SQL Server](https://azure.microsoft.com/migration/migration-journey) dla oprogramowania SAP adapter Server Enterprise.

Aby poznać inne przewodniki dotyczące migracji, zobacz [Przewodnik migracji usługi Azure Database](https://docs.microsoft.com/data-migration). 

## <a name="prerequisites"></a>Wymagania wstępne 

Przed rozpoczęciem migrowania bazy danych SAP SE do bazy danych SQL wykonaj następujące czynności:

- Sprawdź, czy środowisko źródłowe jest obsługiwane. 
- Pobierz i zainstaluj [Asystent migracji do programu SQL Server dla oprogramowania SAP adaptacyjnego serwera Enterprise (dawniej SAP ASE)](https://www.microsoft.com/en-us/download/details.aspx?id=54256).
- Upewnij się, że masz łączność i wystarczające uprawnienia dostępu do źródła i celu.

## <a name="pre-migration"></a>Przed migracją

Po spełnieniu wymagań wstępnych można przystąpić do odnajdywania topologii środowiska i oceny wykonalności [migracji w chmurze platformy Azure](https://azure.microsoft.com/migration).

### <a name="assess"></a>Ocena

Korzystając z [Asystent migracji do programu SQL Server (Asystencie migracji) dla oprogramowania SAP adaptacyjnego serwera Enterprise (formularz SAP ASE)](https://www.microsoft.com/en-us/download/details.aspx?id=54256), można przeglądać obiekty bazy danych i dane, oceniać bazy danych do migracji, migrować obiekty bazy danych programu Sybase do bazy danych SQL, a następnie migrować dane do bazy danych SQL. Aby dowiedzieć się więcej, zobacz [Asystent migracji do programu SQL Server for Sybase (SybaseToSQL)](/sql/ssma/sybase/sql-server-migration-assistant-for-sybase-sybasetosql).

Aby utworzyć ocenę, wykonaj następujące czynności: 

1. Otwórz ASYSTENCIE migracji dla programu Sybase. 
1. Wybierz pozycję **plik**, a następnie wybierz pozycję **Nowy projekt**. 
1. W okienku **Nowy projekt** wprowadź nazwę i lokalizację projektu, a następnie na liście rozwijanej **Migrowanie do** wybierz pozycję **Azure SQL Database**. 
1. Wybierz przycisk **OK**.
1. W okienku **Połącz z programem Sybase** wprowadź szczegóły połączenia SAP. 
1. Kliknij prawym przyciskiem myszy bazę danych SAP, którą chcesz zmigrować, a następnie wybierz pozycję **Utwórz raport**. Spowoduje to wygenerowanie raportu HTML. Alternatywnie możesz wybrać kartę **Utwórz raport** w prawym górnym rogu.
1. Przejrzyj raport HTML, aby poznać statystyki konwersji oraz błędy lub ostrzeżenia. Możesz również otworzyć raport w programie Excel, aby uzyskać spis obiektów SAP ASE i nakładu pracy wymaganego do wykonania konwersji schematu. Domyślna lokalizacja raportu znajduje się w folderze raportów w SSMAProjects. Na przykład:

   `drive:\<username>\Documents\SSMAProjects\MySAPMigration\report\report_<date>` 

### <a name="validate-the-type-mappings"></a>Weryfikowanie mapowań typów

Przed wykonaniem konwersji schematu Sprawdź poprawność domyślnych mapowań typu danych lub zmień je na podstawie wymagań. Możesz to zrobić, wybierając pozycję **Narzędzia**  >  **Ustawienia projektu** lub można zmienić mapowanie typu dla każdej tabeli, wybierając tabelę w **Eksploratorze metadanych oprogramowania SAP ASE**.

### <a name="convert-the-schema"></a>Konwertuj schemat

Aby skonwertować schemat, wykonaj następujące czynności:

1. Obowiązkowe Aby skonwertować zapytania dynamiczne lub wyspecjalizowane, kliknij prawym przyciskiem myszy węzeł, a następnie wybierz polecenie **Dodaj instrukcję**. 
1. Wybierz kartę **Połącz z Azure SQL Database** , a następnie wprowadź szczegóły bazy danych SQL. Można nawiązać połączenie z istniejącą bazą danych lub podać nową nazwę, w takim przypadku na serwerze docelowym zostanie utworzona baza danych.
1. W okienku **Eksplorator metadanych programu Sybase** kliknij prawym przyciskiem myszy schemat oprogramowania SAP ASE, z którym pracujesz, a następnie wybierz polecenie **Konwertuj schemat**. 
1. Po przekonwertowaniu schematu Porównaj i przejrzyj przekonwertowaną strukturę z oryginalną strukturą, aby zidentyfikować potencjalne problemy. 

   Po konwersji schematu można zapisać ten projekt lokalnie dla ćwiczenia korygowania schematu w trybie offline. W tym celu wybierz pozycję **plik**  >  **Zapisz projekt**. Dzięki temu można oszacować schematy źródłowe i docelowe w trybie offline i przeprowadzić korygowanie przed opublikowaniem schematu w usłudze SQL Database.

1. W okienku **dane wyjściowe** wybierz pozycję **Przejrzyj wyniki** i przejrzyj wszystkie błędy w okienku **Lista błędów** . 
1. Zapisz projekt lokalnie dla ćwiczenia korygowania schematu w trybie offline. W tym celu wybierz pozycję **plik**  >  **Zapisz projekt**. Dzięki temu można oszacować schematy źródłowe i docelowe w trybie offline i przeprowadzić korygowanie przed opublikowaniem schematu w usłudze SQL Database.

## <a name="migrate-the-databases"></a>Migrowanie baz danych 

Po spełnieniu wymaganych wymagań wstępnych i ukończeniu zadań skojarzonych z etapem *sprzed migracji* można uruchomić schemat i migrację danych.

Aby opublikować schemat i przeprowadzić migrację danych, wykonaj następujące czynności: 

1. Opublikuj schemat. W okienku **Azure SQL Database metadane Eksploratora** kliknij prawym przyciskiem myszy bazę danych, a następnie wybierz polecenie **Synchronizuj z bazą danych**. Ta akcja publikuje schemat oprogramowania SAP ASE w bazie danych SQL.

1. Migruj dane. W okienku **Eksplorator metadanych oprogramowania SAP ASE** kliknij prawym przyciskiem myszy bazę danych lub obiekt SAP ASE, który chcesz zmigrować, a następnie wybierz polecenie **Migruj dane**. Alternatywnie możesz wybrać kartę **Migrowanie danych** w prawym górnym rogu. 

   Aby migrować dane dla całej bazy danych, zaznacz pole wyboru obok nazwy bazy danych. Aby przeprowadzić migrację danych z pojedynczych tabel, rozwiń bazę danych, rozwiń węzeł **tabele**, a następnie zaznacz pole wyboru obok tabeli. Aby pominąć dane z poszczególnych tabel, wyczyść to pole wyboru. 
1. Po zakończeniu migracji Wyświetl **raport dotyczący migracji danych**. 
1. Sprawdź poprawność migracji, przeglądając dane i schemat. W tym celu należy nawiązać połączenie z bazą danych SQL przy użyciu [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).

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


## <a name="next-steps"></a>Następne kroki

- Macierz usług i narzędzi firmy Microsoft i innych firm, które są dostępne w celu ułatwienia pracy z różnymi scenariuszami bazy danych i migracji danych oraz zadaniami specjalistycznymi, można znaleźć w temacie [Usługa i narzędzia do migracji danych](../../../dms/dms-tools-matrix.md).

- Aby dowiedzieć się więcej na temat Azure SQL Database, zobacz:
   - [Omówienie SQL Database](../../database/sql-database-paas-overview.md)
   - [Kalkulator całkowitego kosztu posiadania na platformę Azure](https://azure.microsoft.com/pricing/tco/calculator/)  

- Aby dowiedzieć się więcej na temat cyklu i wdrożenia migracji do chmury, zobacz:
   -  [Przewodnik Cloud Adoption Framework dla platformy Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Najlepsze rozwiązania dotyczące kosztów i rozmiarów obciążeń na potrzeby migracji na platformę Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 
   -  [Zasoby Migracja do chmury](https://azure.microsoft.com/migration/resources)

- Aby ocenić warstwę dostępu do aplikacji, zobacz [zestaw narzędzi do migracji dostępu do danych (wersja zapoznawcza)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit).
- Aby uzyskać szczegółowe informacje na temat przeprowadzania testów warstwy dostępu do danych A/B, zobacz [Asystent eksperymentowania z bazą danych](/sql/dea/database-experimentation-assistant-overview).
