---
title: Używanie Visual Studio Code do nawiązywania połączeń i zapytań
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Dowiedz się, jak nawiązać połączenie z usługą Azure SQL Database lub wystąpieniem zarządzanym SQL na platformie Azure przy użyciu Visual Studio Code. Następnie uruchom instrukcje Transact-SQL (T-SQL), aby wykonać zapytanie i edytować dane.
keywords: nawiązywanie połączenia z bazą danych SQL
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 05/29/2020
ms.openlocfilehash: f823b6d04a217328fe2e825e64906460cd9cbae9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92672479"
---
# <a name="quickstart-use-visual-studio-code-to-connect-and-query"></a>Szybki Start: używanie Visual Studio Code do nawiązywania połączeń i zapytań 
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

[Visual Studio Code](https://code.visualstudio.com/docs) jest graficznym edytorem kodu dla systemów Linux, macOS i Windows. Obsługuje ona rozszerzenia, w tym [rozszerzenie MSSQL](https://aka.ms/mssql-marketplace) służące do wysyłania zapytań do wystąpienia SQL Server, Azure SQL Database, wystąpienia zarządzanego usługi Azure SQL i bazy danych w usłudze Azure Synapse Analytics. W tym przewodniku szybki start będziesz używać Visual Studio Code do nawiązywania połączenia z Azure SQL Database lub wystąpienia zarządzanego Azure SQL, a następnie uruchamiamy instrukcje języka Transact-SQL w celu wykonywania zapytań, wstawiania, aktualizowania i usuwania danych.

## <a name="prerequisites"></a>Wymagania wstępne

- Baza danych programu Azure SQL Database lub wystąpienia zarządzanego Azure SQL. Aby utworzyć, a następnie skonfigurować bazę danych w usłudze Azure SQL Database, można użyć instrukcji z jednego z tych przewodników Szybki start:

  | Akcja | Azure SQL Database | Wystąpienie zarządzane Azure SQL |
  |:--- |:--- |:---|
  | Utwórz| [Portal](single-database-create-quickstart.md) | [Portal](../managed-instance/instance-create-quickstart.md) |
  || [Interfejs wiersza polecenia](scripts/create-and-configure-database-cli.md) | [Interfejs wiersza polecenia](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [Program PowerShell](scripts/create-and-configure-database-powershell.md) | [Program PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) |
  | Konfigurowanie | [Reguła zapory adresów IP na poziomie serwera](firewall-create-server-level-portal-quickstart.md))| [Łączność z maszyny wirtualnej (VM)](../managed-instance/connect-vm-instance-configure.md)|
  |||[Łączność z lokalnego](../managed-instance/point-to-site-p2s-configure.md)
  |Ładowanie danych|Ładowanie bazy danych Adventure Works na potrzeby samouczka Szybki start|[Przywracanie bazy danych Wide World Importers](../managed-instance/restore-sample-database-quickstart.md)
  |||Przywróć lub zaimportuj Adventure Works z pliku [BACPAC](database-import.md) z usługi [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > Skrypty zamieszczone w tym artykule korzystają z bazy danych Adventure Works. W przypadku wystąpienia zarządzanego SQL należy zaimportować bazę danych firmy Adventure Works do bazy danych wystąpienia lub zmodyfikować skrypty w tym artykule, aby użyć bazy danych Wide World Imports.

## <a name="install-visual-studio-code"></a>Instalacja programu Visual Studio Code

Upewnij się, że masz zainstalowaną najnowszą wersję programu [Visual Studio Code](https://code.visualstudio.com/Download) i załadowane [rozszerzenie mssql](https://aka.ms/mssql-marketplace). Aby uzyskać wskazówki dotyczące instalowania rozszerzenia MSSQL, zobacz [Install Visual Studio Code](/sql/linux/sql-server-linux-develop-use-vscode#install-and-start-visual-studio-code) and [MSSQL for Visual Studio Code ](https://marketplace.visualstudio.com/items?itemName=ms-mssql.mssql).

## <a name="configure-visual-studio-code"></a>Konfigurowanie programu Visual Studio Code

### <a name="macos"></a>**macOS**

W przypadku macOS należy zainstalować OpenSSL, który jest wymaganiem wstępnym dla platformy .NET Core używanej przez rozszerzenie MSSQL. Otwórz terminal i wprowadź następujące polecenia, aby zainstalować rozwiązania **brew** i **OpenSSL**.

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew update
brew install openssl
mkdir -p /usr/local/lib
ln -s /usr/local/opt/openssl/lib/libcrypto.1.0.0.dylib /usr/local/lib/
ln -s /usr/local/opt/openssl/lib/libssl.1.0.0.dylib /usr/local/lib/
```

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**

Nie jest potrzebna specjalna konfiguracja.

### <a name="windows"></a>**Windows**

Nie jest potrzebna specjalna konfiguracja.

## <a name="get-server-connection-information"></a>Pobierz informacje o połączeniu z serwerem

Pobierz informacje o połączeniu potrzebne do nawiązania połączenia z Azure SQL Database. W następnych procedurach będą potrzebne w pełni kwalifikowana nazwa serwera lub nazwa hosta, nazwa bazy danych i informacje logowania.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

2. Przejdź do strony **bazy danych SQL**  lub **wystąpienia zarządzane SQL** .

3. Na stronie **Przegląd** Przejrzyj w pełni kwalifikowaną nazwę serwera obok pozycji **nazwa serwera** dla SQL Database lub w pełni kwalifikowana nazwa serwera obok pozycji **host** dla wystąpienia zarządzanego SQL. Aby skopiować nazwę serwera lub hosta, umieść na niej wskaźnik myszy i wybierz ikonę **Kopiuj**.

## <a name="set-language-mode-to-sql"></a>Ustawianie trybu języka na SQL

W programie Visual Studio Code ustaw tryb języka na **SQL**, aby włączyć polecenia mssql i T-SQL IntelliSense.

1. Otwórz nowe okno programu Visual Studio Code.

2. Naciśnij **klawisze CTRL** + **N**. Zostanie otwarty nowy plik w formacie zwykłego tekstu.

3. Wybierz pozycję **Zwykły tekst** w prawym dolnym rogu paska stanu.

4. Z menu rozwijanego **Wybierz tryb języka**, które zostanie otwarte, wybierz pozycję **SQL**.

## <a name="connect-to-your-database"></a>Nawiązywanie połączenia z bazą danych

Użyj Visual Studio Code, aby nawiązać połączenie z serwerem.

> [!IMPORTANT]
> Przed kontynuowaniem upewnij się, że masz przygotowany serwer i informacje logowania. Jeśli zmienisz fokus z programu Visual Studio Code po rozpoczęciu wprowadzania informacji o profilu połączenia, konieczne będzie ponowne rozpoczęcie procesu tworzenia profilu.

1. W programie Visual Studio Code naciśnij klawisze **Ctrl + Shift + P** (lub klawisz **F1**), aby otworzyć paletę poleceń.

2. Wybierz pozycję **MS SQL:Connect** (MS SQL: Połącz) i naciśnij klawisz **Enter**.

3. Wybierz pozycję **Utwórz profil połączenia**.

4. Postępuj zgodnie z monitami, aby określić właściwości nowego profilu. Po określeniu każdej wartości naciśnij klawisz **Enter**, aby kontynuować.

   | Właściwość       | Sugerowana wartość | Opis |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Nazwa serwera** | W pełni kwalifikowana nazwa serwera | Na przykład: **mojnowyserwer20170313.database.windows.net**. |
   | **Nazwa bazy danych** | mySampleDatabase | Baza danych, z którą ma zostać nawiązane połączenie. |
   | **Authentication** | Identyfikator logowania SQL| W tym samouczku używane jest uwierzytelnianie SQL. |
   | **User name** (Nazwa użytkownika) | Nazwa użytkownika | Nazwa użytkownika konta administratora serwera, którego użyto do utworzenia serwera. |
   | **Hasło (identyfikator logowania SQL)** | Hasło | Hasło użytkownika konta administratora serwera, którego użyto do utworzenia serwera. |
   | **Zapisać hasło?** | Tak lub Nie | Wybierz opcję **Tak**, jeśli nie chcesz wprowadzać hasła za każdym razem. |
   | **Wprowadź nazwę dla tego profilu** | Nazwa profilu, np. **mojPrzykladowyProfil** | Zapisany profil przyspiesza połączenie podczas kolejnych logowań. |

   W przypadku powodzenia zostanie wyświetlone powiadomienie z informacją o utworzeniu i połączeniu profilu.

## <a name="query-data"></a>Zapytania o dane

Uruchom następującą instrukcję [SELECT](/sql/t-sql/queries/select-transact-sql) języka Transact-SQL, aby wykonać zapytanie o 20 najpopularniejszych produktów według kategorii.

1. W oknie edytora wklej następujące zapytanie SQL.

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

2. Naciśnij **klawisze CTRL** + **SHIFT** + **E** , aby uruchomić zapytanie i wyświetlić wyniki z `Product` `ProductCategory` tabel i.

    ![Zapytanie do pobierania danych z 2 tabel](./media/connect-query-vscode/query.png)

## <a name="insert-data"></a>Wstawianie danych

Uruchom następującą instrukcję [INSERT](/sql/t-sql/statements/insert-transact-sql) języka Transact-SQL, aby dodać nowy produkt do tabeli `SalesLT.Product`.

1. Wpisz to zapytanie w miejsce poprzedniego.

   ```sql
   INSERT INTO [SalesLT].[Product]
        ( [Name]
        , [ProductNumber]
        , [Color]
        , [ProductCategoryID]
        , [StandardCost]
        , [ListPrice]
        , [SellStartDate]
        )
     VALUES
        ('myNewProduct'
        ,123456789
        ,'NewColor'
        ,1
         ,100
         ,100
         ,GETDATE() );
   ```

2. Naciśnij **klawisze CTRL** + **SHIFT** + **E** , aby wstawić nowy wiersz w `Product` tabeli.

## <a name="update-data"></a>Aktualizowanie danych

Uruchom następującą instrukcję [UPDATE](/sql/t-sql/queries/update-transact-sql) języka Transact-SQL, aby zaktualizować dodany produkt.

1. Zastąp poprzednie zapytanie następującym:

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Naciśnij **klawisze CTRL** + **SHIFT** + **E** , aby zaktualizować określony wiersz w `Product` tabeli.

## <a name="delete-data"></a>Usuwanie danych

Uruchom następującą instrukcję [DELETE](/sql/t-sql/statements/delete-transact-sql) języka Transact-SQL, aby usunąć nowy produkt.

1. Zastąp poprzednie zapytanie następującym:

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Naciśnij **klawisze CTRL** + **SHIFT** + **E** , aby usunąć określony wiersz w `Product` tabeli.

## <a name="next-steps"></a>Następne kroki

- Aby nawiązać połączenie i wykonać zapytanie przy użyciu SQL Server Management Studio, zobacz [Szybki Start: użyj SQL Server Management Studio, aby nawiązać połączenie z bazą danych w Azure SQL Database i dane zapytań](connect-query-ssms.md).
- Aby nawiązać połączenie i wykonać zapytanie przy użyciu Azure Portal, zobacz [Szybki Start: Użyj edytora zapytań SQL w Azure Portal, aby nawiązać połączenie i wykonać zapytania dotyczące danych](connect-query-portal.md).
- Aby zapoznać się z artykułem w magazynie MSDN dotyczącym programu Visual Studio Code, zobacz temat [Create a database IDE with MSSQL extension blog post](/archive/msdn-magazine/2017/june/data-points-visual-studio-code-create-a-database-ide-with-mssql-extension) (Tworzenie bazy danych w środowisku IDE, korzystając z wpisu na blogu dotyczącym rozszerzenia MSSQL).