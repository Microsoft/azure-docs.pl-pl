---
title: 'Samouczek: jak przeprowadzić migrację bazy danych programu SQLite do Azure SQL Database bezserwerowej'
description: Dowiedz się, jak przeprowadzić migrację w trybie offline z programu SQLite do Azure SQL Database serwera bezserwerowego przy użyciu Azure Data Factory.
services: sql-database
author: joplum
ms.author: joplum
manager: prda
ms.service: sql-database
ms.workload: data-services
ms.topic: article
ms.date: 01/08/2020
ms.openlocfilehash: 2aed3a66f70864b3445418a51b0e79280e6884bd
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/01/2020
ms.locfileid: "82629205"
---
# <a name="how-to-migrate-your-sqlite-database-to-azure-sql-database-serverless"></a>Jak przeprowadzić migrację bazy danych programu SQLite do Azure SQL Database bezserwerowego
W przypadku wielu osób oprogramowanie SQLite zapewnia swoje pierwsze środowisko baz danych i programowanie SQL. W wielu systemach operacyjnych i popularnych aplikacjach aplikacja SQLite jest jednym z najpopularniejszych aparatów baz danych wdrożonych i używanych na świecie. Z tego powodu prawdopodobnie pierwszy aparat bazy danych jest używany przez wiele osób, ale często może się zakończyć jako centralna część projektów lub aplikacji. W takich przypadkach, gdy projekt lub aplikacja rozszerzają początkową implementację oprogramowania SQLite, deweloperzy mogą potrzebować migracji swoich danych do niezawodnego, scentralizowanego magazynu danych.

Azure SQL Database bezserwerowy jest warstwą obliczeniową dla pojedynczych baz danych, która automatycznie skaluje obliczenia na podstawie zapotrzebowania na obciążenia, a następnie obciąża liczbę użytych obliczeń na sekundę. Warstwa obliczeniowa bezserwerowa również automatycznie wstrzymuje bazy danych w trakcie okresów nieaktywnych, gdy są naliczane opłaty za magazyn i automatycznie wznawiają bazy danych po powrocie działania.

Po wykonaniu poniższych kroków baza danych zostanie zmigrowana do Azure SQL Database bezserwerowym, co umożliwi udostępnienie bazy danych innym użytkownikom lub aplikacjom w chmurze, a tylko za to, czego używasz, przy minimalnych zmianach w kodzie aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne
- Subskrypcja platformy Azure
- Baza danych SQLite2 lub SQLite3, która ma zostać zmigrowana
- Środowisko systemu Windows
  - Jeśli nie masz lokalnego środowiska systemu Windows, możesz przeprowadzić migrację za pomocą maszyny wirtualnej z systemem Windows na platformie Azure. Przenieś i Udostępnij plik bazy danych programu SQLite na maszynie wirtualnej przy użyciu Azure Files i Eksplorator usługi Storage.

## <a name="steps"></a>Kroki

1. Zainicjuj obsługę nowego Azure SQL Database w warstwie obliczeniowej bezserwerowej.

    ![zrzut ekranu przedstawiający Azure Portal pokazujący przykład aprowizacji dla serwera usługi Azure SQL Database](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/provision-serverless.png)

2. Upewnij się, że plik bazy danych programu SQLite jest dostępny w środowisku systemu Windows. Zainstaluj sterownik ODBC programu SQLite, jeśli jeszcze go nie masz (istnieje wiele dostępnych w programie Open Source, na przykład http://www.ch-werner.de/sqliteodbc/).

3. Utwórz systemową nazwę DSN dla bazy danych programu. Upewnij się, że używasz aplikacji administratora źródła danych zgodnej z architekturą systemu (32-bitową a 64-bitową). Możesz sprawdzić, która wersja jest uruchomiona w ustawieniach systemu.

    - Otwórz administratora źródła danych ODBC w danym środowisku.
    - Kliknij kartę systemowa DSN i kliknij pozycję "Dodaj".
    - Wybierz zainstalowany łącznik ODBC programu SQLite i nadaj mu nazwę zrozumiałą, na przykład sqlitemigrationsource
    - Ustaw nazwę bazy danych na plik. DB
    - Zapisz i wyjdź

4. Pobierz i zainstaluj własne środowisko Integration Runtime. Najprostszym sposobem wykonania tej czynności jest opcja instalacji ekspresowej, zgodnie z opisem w dokumentacji. W przypadku wybrania opcji ręcznej instalacji należy podać aplikację z kluczem uwierzytelniania, który może znajdować się w wystąpieniu Data Factory przez:

    - Uruchamianie ADF (Tworzenie i monitorowanie z usługi w Azure Portal)
    - Kliknij kartę "autor" (niebieski ołówek) po lewej stronie
    - Kliknij pozycję połączenia (z lewej strony), a następnie środowiska Integration Runtime
    - Dodaj nowe Integration Runtime samodzielne, nadaj mu nazwę, wybierz *opcję 2*.

5. Utwórz nową połączoną usługę dla źródłowej bazy danych programu SQLite w Data Factory.

    ![zrzut ekranu przedstawiający blok puste usługi połączone w Azure Data Factory](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/linked-services-create.png)

6. W obszarze połączenia w obszarze połączona usługa kliknij pozycję Nowy.

7. Wyszukaj i wybierz łącznik "ODBC"


    ![zrzut ekranu przedstawiający logo łącznika ODBC w bloku połączone usługi w Azure Data Factory](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/linked-services-odbc.png)

8. Nadaj połączoną usługę zrozumiałą nazwę, na przykład "sqlite_odbc". Wybierz swoje środowisko Integration Runtime z listy rozwijanej "Połącz za pośrednictwem środowiska Integration Runtime". Wprowadź poniższe polecenie do parametrów połączenia, zastępując początkową zmienną katalogu identyfikatorem pliku. DB, a nazwę DSN nazwą systemowego połączenia DSN: 

    ```
    Connection string: Provider=MSDASQL.1;Persist Security Info=False;Mode=ReadWrite;Initial Catalog=C:\sqlitemigrationsource.db;DSN=sqlitemigrationsource
    ```

9. Ustaw typ uwierzytelniania na anonimowy

10. Testowanie połączenia

    ![zrzut ekranu przedstawiający pomyślne połączenie w Azure Data Factory](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/linked-services-test-successful.png)

11. Utwórz kolejną połączoną usługę dla docelowego programu SQL Server. Wybierz bazę danych za pomocą Kreatora połączonej usługi, a następnie podaj poświadczenia uwierzytelniania SQL.

    ![zrzut ekranu przedstawiający Azure SQL Database wybrany w Azure Data Factory](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/linked-services-create-target.png)

12. Wyodrębnij instrukcje CREATE TABLE z bazy danych programu SQLite. Można to zrobić, wykonując Poniższy skrypt w języku Python w pliku bazy danych.

    ```
    #!/usr/bin/python
    import sqlite3
    conn = sqlite3.connect("sqlitemigrationsource.db")
    c = conn.cursor()

    print("Starting extract job..")
    with open('CreateTables.sql', 'w') as f:
        for tabledetails in c.execute("SELECT * FROM sqlite_master WHERE type='table'"):
            print("Extracting CREATE statement for " + (str(tabledetails[1])))
            print(tabledetails)
            f.write(str(tabledetails[4].replace('\n','') + ';\n'))
    c.close()
    ```

13. Utwórz tabele wyładunkowe w środowisku docelowym SQL bezserwerowym przez skopiowanie instrukcji CREATE TABLE z pliku xmltables. SQL i uruchomienie instrukcji SQL w edytorze zapytań w Azure Portal.

14. Wróć do ekranu głównego Data Factory i kliknij przycisk "Kopiowanie danych", aby uruchomić Kreatora tworzenia zadań.

    ![zrzut ekranu przedstawiający logo kreatora Kopiowanie danych w programie Azure Data Factory](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/copy-data.png)

15. Zaznacz wszystkie tabele ze źródłowej bazy danych programu SQLite przy użyciu pól wyboru i zmapuj je do tabel docelowych w usłudze Azure SQL. Po uruchomieniu zadania pomyślnie przeprowadzono migrację danych z oprogramowania SQLite do usługi Azure SQL.

## <a name="next-steps"></a>Następne kroki

- Aby rozpocząć, zobacz [Szybki Start: Tworzenie pojedynczej bazy danych w Azure SQL Database przy użyciu Azure Portal](sql-database-single-database-get-started.md).
- W przypadku limitów zasobów zobacz [limity zasobów warstwy obliczeń Bezserwerowych](sql-database-vCore-resource-limits-single-databases.md#general-purpose---serverless-compute---gen5).
