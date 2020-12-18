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
ms.date: 05/29/2020
ms.openlocfilehash: a87c08d81724a6639fce6b6673b139b3ef94e410
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/18/2020
ms.locfileid: "97680035"
---
# <a name="quickstart-use-python-to-query-a-database-in-azure-sql-database-or-azure-sql-managed-instance"></a>Szybki Start: używanie języka Python do wykonywania zapytań w bazie danych w Azure SQL Database lub wystąpieniu zarządzanym Azure SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi-asa.md)]

W tym przewodniku szybki start używasz języka Python do nawiązywania połączenia z usługą Azure SQL Database lub wystąpieniem zarządzanym usługi Azure SQL i używasz instrukcji T-SQL do wykonywania zapytań dotyczących danych.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania czynności opisanych w tym przewodniku Szybki start potrzebne są następujące elementy:

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

[!INCLUDE[create-configure-database](../includes/create-configure-database.md)]

- Środowisko [Python](https://python.org/downloads) 3 i powiązane oprogramowanie

  # <a name="macos"></a>[macOS](#tab/macos)

  Aby zainstalować oprogramowania Homebrew i Python, sterownik ODBC i Narzędzie SQLCMD oraz sterownik Python dla SQL Server, wykonaj kroki **1,2**, **1,3** i **2,1** w temacie [tworzenie aplikacji w języku Python przy użyciu SQL Server na macOS](https://www.microsoft.com/sql-server/developer-get-started/python/mac/).

  Aby uzyskać więcej informacji, zobacz [Microsoft ODBC Driver on macOS](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server).

  # <a name="ubuntu"></a>[Ubuntu](#tab/ubuntu)

  Aby zainstalować język Python i inne wymagane pakiety, użyj programu `sudo apt-get install python python-pip gcc g++ build-essential` .

  Aby zainstalować sterownik ODBC, SQLCMD i sterownik Python dla SQL Server, zobacz [Konfigurowanie środowiska do tworzenia aplikacji w języku Python moduł pyodbc](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#linux).

  Aby uzyskać więcej informacji, zobacz [Sterownik Microsoft ODBC w systemie Linux](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server).

  # <a name="windows"></a>[Windows](#tab/windows)

  Aby zainstalować język Python, sterownik ODBC i Narzędzie SQLCMD oraz sterownik Python dla SQL Server, zobacz [Konfigurowanie środowiska do tworzenia aplikacji w języku Python moduł pyodbc](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#windows).

  Aby uzyskać więcej informacji, zobacz [Microsoft ODBC Driver](/sql/connect/odbc/microsoft-odbc-driver-for-sql-server).

---
Aby dowiedzieć się więcej o języku Python i bazie danych w Azure SQL Database, zobacz [biblioteki Azure SQL Database dla języka Python](/python/api/overview/azure/sql), [repozytorium moduł pyodbc](https://github.com/mkleehammer/pyodbc/wiki/)i [przykład moduł pyodbc](https://github.com/mkleehammer/pyodbc/wiki/Getting-started).

## <a name="get-server-connection-information"></a>Pobierz informacje o połączeniu z serwerem

Pobierz informacje o połączeniu potrzebne do nawiązania połączenia z bazą danych w Azure SQL Database. W następnych procedurach będą potrzebne w pełni kwalifikowana nazwa serwera lub nazwa hosta, nazwa bazy danych i informacje logowania.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

2. Przejdź do strony **bazy danych SQL**  lub **wystąpienia zarządzane SQL** .

3. Na stronie **Przegląd** Przejrzyj w pełni kwalifikowaną nazwę serwera obok pozycji **Nazwa serwera** dla bazy danych w Azure SQL Database lub w pełni kwalifikowana nazwa serwera (lub adres IP) obok **hosta** dla wystąpienia zarządzanego usługi Azure SQL lub SQL Server na maszynie wirtualnej platformy Azure. Aby skopiować nazwę serwera lub hosta, umieść na niej wskaźnik myszy i wybierz ikonę **Kopiuj**.

> [!NOTE]
> Aby uzyskać informacje o połączeniu dla SQL Server na maszynie wirtualnej platformy Azure, zobacz [nawiązywanie połączenia z wystąpieniem SQL Server](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server).

## <a name="create-code-to-query-your-database"></a>Utwórz kod, aby wykonać zapytanie dotyczące bazy danych 

1. W edytorze tekstów utwórz nowy plik o nazwie *sqltest.py*.  
   
1. Dodaj następujący kod. Podstaw własne wartości dla \<server> , \<database> , \<username> i \<password> .
   
   >[!IMPORTANT]
   >Kod w tym przykładzie używa przykładowych danych AdventureWorksLT, które można wybrać jako źródło podczas tworzenia bazy danych. Jeśli baza danych zawiera różne dane, w zapytaniu SELECT należy użyć tabel z własnej bazy danych. 
   
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

1. Sprawdź, czy są zwracane pierwsze 20 wierszy kategorii/produktów, a następnie zamknij okno wiersza polecenia.

## <a name="next-steps"></a>Następne kroki

- [Projektuj swoją pierwszą bazę danych w Azure SQL Database](design-first-database-tutorial.md)
- [Sterowniki języka Python firmy Microsoft dla SQL Server](/sql/connect/python/python-driver-for-sql-server/)
- [Centrum deweloperów języka Python](https://azure.microsoft.com/develop/python/?v=17.23h)
