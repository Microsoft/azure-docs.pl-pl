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
ms.openlocfilehash: d59a4fc2cbf52d95e6b5a5100b5ffe06c5dbed7e
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103565086"
---
# <a name="migration-guide--mysql-to-azure-sql-database"></a>Przewodnik migracji: MySQL do Azure SQL Database
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

W tym przewodniku pokazano, jak migrować bazę danych MySQL do Azure SQL Database przy użyciu Asystent migracji do programu SQL Server for MySQL (ASYSTENCIE migracji for MySQL). 

W przypadku innych przewodników migracji zapoznaj się z tematem [migracja bazy danych](https://datamigration.microsoft.com/). 

## <a name="prerequisites"></a>Wymagania wstępne

Aby przeprowadzić migrację bazy danych MySQL do Azure SQL Database, potrzebne są:

- Aby sprawdzić, czy środowisko źródłowe jest obsługiwane. Obecnie obsługiwane są produkty MySQL 5,6 i 5,7. 
- [Asystent migracji do programu SQL Server dla programu MySQL](https://www.microsoft.com/download/confirmation.aspx?id=54257)


## <a name="pre-migration"></a>Przed migracją 

Po spełnieniu wymagań wstępnych można przystąpić do odnajdywania topologii środowiska i oceny wykonalności migracji.

### <a name="assess"></a>Ocena 

Za pomocą [Asystent migracji do programu SQL Server dla programu MySQL](https://www.microsoft.com/download/confirmation.aspx?id=54257)można przeglądać obiekty bazy danych i dane oraz oceniać bazy danych do migracji.

Aby utworzyć ocenę, wykonaj następujące czynności.

1. Otwórz Asystent migracji do programu SQL Server dla programu MySQL. 
1. Z menu wybierz pozycję **plik** , a następnie wybierz pozycję **Nowy projekt**. Podaj nazwę projektu, lokalizację do zapisania projektu.
1. Wybierz **Azure SQL Database** jako cel migracji. 
1. Wybierz pozycję **Połącz z bazą danych MySQL** i podaj szczegóły połączenia w celu połączenia z serwerem MySQL. 
1. Kliknij prawym przyciskiem myszy schemat MySQL w **Eksploratorze metadanych MySQL** , a następnie wybierz polecenie **Utwórz raport**. Alternatywnie możesz wybrać opcję **Utwórz raport** z paska nawigacyjnego górnego wiersza. 
1. Przejrzyj raport HTML dotyczący statystyk konwersji, a także błędy i ostrzeżenia. Analizuj go, aby zrozumieć problemy z konwersją i rozwiązania. 

   Dostęp do tego raportu można również uzyskać z folderu projektów ASYSTENCIE migracji, wybranego na pierwszym ekranie. W powyższym przykładzie zlokalizuj plik report.xml z:
 
   `drive:\Users\<username>\Documents\SSMAProjects\MySQLMigration\report\report_2016_11_12T02_47_55\`
 
   Otwórz go w programie Excel, aby uzyskać spis obiektów MySQL i nakład pracy wymagany do przeprowadzenia konwersji schematu.

### <a name="validate-data-types"></a>Sprawdzanie poprawności typów danych

Przed wykonaniem konwersji schematu Sprawdź poprawność domyślnych mapowań typów danych lub zmień je na podstawie wymagań. Można to zrobić, przechodząc do menu "narzędzia" i wybierając pozycję "Ustawienia projektu" lub zmienić mapowanie typu dla każdej tabeli, wybierając tabelę w "Eksploratorze metadanych MySQL".

### <a name="convert-schema"></a>Konwertuj schemat 

Aby przekonwertować schemat, wykonaj następujące kroki: 

1. Obowiązkowe Aby skonwertować kwerendy dynamiczne lub ad hoc, kliknij prawym przyciskiem myszy węzeł i wybierz polecenie **Dodaj instrukcję**. 
1. Wybierz opcję **Połącz z Azure SQL Database** z górnego paska nawigacyjnego i podaj szczegóły połączenia. Można nawiązać połączenie z istniejącą bazą danych lub podać nową nazwę, w takim przypadku na serwerze docelowym zostanie utworzona baza danych.
1. Kliknij prawym przyciskiem myszy schemat i wybierz polecenie **Konwertuj schemat**. 
1. Po zakończeniu konwertowania schematu Porównaj przekonwertowany kod na oryginalny kod, aby zidentyfikować potencjalne problemy. 



## <a name="migrate"></a>Migrate 

Po zakończeniu oceniania baz danych i rozwiązaniu jakichkolwiek rozbieżności następnym krokiem jest wykonanie procesu migracji. Migracja obejmuje dwa kroki — opublikowanie schematu i Migrowanie danych. 

Aby opublikować schemat i przeprowadzić migrację danych, wykonaj następujące kroki: 

1. Kliknij prawym przyciskiem myszy bazę danych w **Eksploratorze metadanych Azure SQL Database** i wybierz polecenie **Synchronizuj z bazą danych**. Ta akcja powoduje opublikowanie schematu MySQL w Azure SQL Database.
1. Kliknij prawym przyciskiem myszy schemat MySQL w **Eksploratorze metadanych MySQL** , a następnie wybierz polecenie **Migruj dane**. Alternatywnie możesz wybrać opcję **Migruj dane** z nawigacji górnej linii. 
1. Po zakończeniu migracji Wyświetl raport dotyczący **migracji danych** : 
1.  Sprawdź poprawność migracji, przeglądając dane i schemat na Azure SQL Database przy użyciu SQL Server Management Studio (SSMS).


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

- Macierz usług i narzędzi firmy Microsoft i innych firm, które są dostępne w celu ułatwienia pracy z różnymi scenariuszami bazy danych i migracji danych oraz zadaniami specjalistycznymi, można znaleźć w artykule [Usługa i narzędzia do migracji danych](https://docs.microsoft.com/azure/dms/dms-tools-matrix).

- W przypadku innych przewodników migracji zapoznaj się z tematem [migracja bazy danych](https://datamigration.microsoft.com/). 

Aby uzyskać wideo, zobacz: 
- [Przegląd podróży migracji i narzędzi/usług zalecanych do przeprowadzania oceny i migracji](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)
