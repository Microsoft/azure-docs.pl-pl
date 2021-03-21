---
title: Używanie języka Python do wykonywania zapytań w bazie danych
description: W tym temacie przedstawiono sposób użycia języka Python do utworzenia programu, który nawiązuje połączenie z bazą danych w Azure SQL Database i wykonuje zapytania przy użyciu instrukcji Transact-SQL.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: seo-python-october2019, sqldbrb=2, devx-track-python
ms.devlang: python
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/19/2020
ms.openlocfilehash: f21e11e33d3ddf1489dba3419766a8adaa878d5f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103491966"
---
# <a name="quickstart-use-python-to-query-a-database"></a>Szybki Start: używanie języka Python do wykonywania zapytań w bazie danych

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi-asa.md)]

W tym przewodniku szybki start użyjesz języka Python do łączenia się z Azure SQL Database, wystąpieniem zarządzanym Azure SQL lub Synapse bazą danych SQL, a także Użyj instrukcji języka T-SQL do wykonywania zapytań dotyczących danych.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania czynności opisanych w tym przewodniku Szybki start potrzebne są następujące elementy:

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- Baza danych, w której zostanie uruchomione zapytanie.

  [!INCLUDE[create-configure-database](../includes/create-configure-database.md)]

- Środowisko [Python](https://python.org/downloads) 3 i powiązane oprogramowanie
    

    |**Akcja**|**macOS**|**Ubuntu**|**Windows**|
    |----------|-----------|------------|---------|
    |Zainstaluj sterownik ODBC, SQLCMD i sterownik Python dla SQL Server|Wykonaj kroki **1,2**, **1,3** i **2,1** w temacie [tworzenie aplikacji w języku Python przy użyciu SQL Server w macOS](https://www.microsoft.com/sql-server/developer-get-started/python/mac/). Spowoduje to również zainstalowanie instalacji oprogramowania Homebrew i języka Python.       |[Konfigurowanie środowiska do programowania w języku Python moduł pyodbc](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#linux)|[Skonfiguruj środowisko do programowania w języku Python moduł pyodbc](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#windows).|
    |Zainstaluj Język Python i inne wymagane pakiety|    |Użyj polecenia `sudo apt-get install python python-pip gcc g++ build-essential`.|    |
    |Dodatkowe informacje|[Sterownik Microsoft ODBC w systemie macOS](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server)  |[Sterownik Microsoft ODBC w systemie Linux](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server)|[Sterownik Microsoft ODBC w systemie Linux](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server)|



Aby dowiedzieć się więcej o języku Python i bazie danych w Azure SQL Database, zobacz [biblioteki Azure SQL Database dla języka Python](/python/api/overview/azure/sql), [repozytorium moduł pyodbc](https://github.com/mkleehammer/pyodbc/wiki/)i [przykład moduł pyodbc](https://github.com/mkleehammer/pyodbc/wiki/Getting-started).

## <a name="create-code-to-query-your-database"></a>Utwórz kod, aby wykonać zapytanie dotyczące bazy danych 

1. W edytorze tekstów utwórz nowy plik o nazwie *sqltest.py*.  
   
1. Dodaj następujący kod. Pobierz informacje o połączeniu z sekcji wymagania wstępne i Zastąp własne wartości dla \<server> , \<database> , \<username> i \<password> .
   
   ```python
   import pyodbc
   server = '<server>.database.windows.net'
   database = '<database>'
   username = '<username>'
   password = '<password>'   
   driver= '{ODBC Driver 17 for SQL Server}'
   
   with pyodbc.connect('DRIVER='+driver+';SERVER='+server+';PORT=1433;DATABASE='+database+';UID='+username+';PWD='+ password) as conn:
       with conn.cursor() as cursor:
           cursor.execute("SELECT TOP 3 name, collation_name FROM sys.databases")
           row = cursor.fetchone()
           while row:
               print (str(row[0]) + " " + str(row[1]))
               row = cursor.fetchone()
   ```
   

## <a name="run-the-code"></a>Uruchamianie kodu

1. W wierszu polecenia uruchom następujące polecenie:

   ```cmd
   python sqltest.py
   ```

1. Sprawdź, czy bazy danych i ich sortowania są zwracane, a następnie zamknij okno wiersza polecenia.

## <a name="next-steps"></a>Następne kroki

- [Projektuj swoją pierwszą bazę danych w Azure SQL Database](design-first-database-tutorial.md)
- [Sterowniki języka Python firmy Microsoft dla SQL Server](/sql/connect/python/python-driver-for-sql-server/)
- [Centrum deweloperów języka Python](https://azure.microsoft.com/develop/python/?v=17.23h)
