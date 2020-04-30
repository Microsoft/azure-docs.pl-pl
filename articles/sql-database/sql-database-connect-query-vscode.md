---
title: Używanie VS Code do nawiązywania połączeń i zapytań
description: Dowiedz się, jak łączyć się z bazą danych SQL Database na platformie Azure przy użyciu programu Visual Studio Code. Następnie uruchom instrukcje Transact-SQL (T-SQL), aby wykonać zapytanie i edytować dane.
keywords: nawiązywanie połączenia z bazą danych SQL
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/25/2019
ms.openlocfilehash: 28b35f273783b2e4d0b8f59c5bc5be384b933ba2
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "73826890"
---
# <a name="quickstart-use-visual-studio-code-to-connect-and-query-an-azure-sql-database"></a>Szybki Start: używanie Visual Studio Code do nawiązywania połączenia i wysyłania zapytań do Azure SQL Database

[Visual Studio Code](https://code.visualstudio.com/docs) jest graficznym edytorem kodu dla systemów Linux, macOS i Windows. Obsługuje rozszerzenia, w tym [rozszerzenie mssql](https://aka.ms/mssql-marketplace), używane do wysyłania zapytań do programów Microsoft SQL Server oraz usług Azure SQL Database i SQL Data Warehouse. W tym przewodniku Szybki start użyjesz edytora Visual Studio Code w celu nawiązania połączenia z bazą danych Azure SQL Database, a następnie uruchomisz instrukcje języka Transact-SQL, aby wysyłać zapytania o dane, a także wstawiać, aktualizować i usuwać dane.

## <a name="prerequisites"></a>Wymagania wstępne

- Baza danych Azure SQL Database. Aby utworzyć, a następnie skonfigurować bazę danych w usłudze Azure SQL Database, można użyć instrukcji z jednego z tych przewodników Szybki start:

  || Pojedyncza baza danych | Wystąpienie zarządzane |
  |:--- |:--- |:---|
  | Utwórz| [Portal](sql-database-single-database-get-started.md) | [Portal](sql-database-managed-instance-get-started.md) |
  || [Interfejs wiersza polecenia](scripts/sql-database-create-and-configure-database-cli.md) | [Interfejs wiersza polecenia](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | Konfigurowanie | [Reguła zapory bazująca na adresach IP na poziomie serwera](sql-database-server-level-firewall-rule.md)| [Łączność z maszyny wirtualnej](sql-database-managed-instance-configure-vm.md)|
  |||[Łączność ze środowiska lokalnego](sql-database-managed-instance-configure-p2s.md)
  |Ładowanie danych|Ładowanie bazy danych Adventure Works na potrzeby samouczka Szybki start|[Przywracanie bazy danych Wide World Importers](sql-database-managed-instance-get-started-restore.md)
  |||Przywróć lub zaimportuj Adventure Works z pliku [BACPAC](sql-database-import.md) z usługi [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > Skrypty zamieszczone w tym artykule korzystają z bazy danych Adventure Works. Za pomocą wystąpienia zarządzanego należy zaimportować bazę danych Adventure Works do bazy danych wystąpienia lub zmodyfikować skrypty znajdujące się w tym artykule, aby korzystały z bazy danych Wide World Importers.

## <a name="install-visual-studio-code"></a>Instalowanie programu Visual Studio Code

Upewnij się, że masz zainstalowaną najnowszą wersję programu [Visual Studio Code](https://code.visualstudio.com/Download) i załadowane [rozszerzenie mssql](https://aka.ms/mssql-marketplace). Aby uzyskać wskazówki dotyczące instalacji rozszerzenia mssql, zobacz artykuły [Install VS Code](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-vscode#install-and-start-visual-studio-code) (Instalacja programu VS Code) i [mssql for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-mssql.mssql) (Rozszerzenie mssql dla programu Visual Studio Code).

## <a name="configure-visual-studio-code"></a>Konfigurowanie programu Visual Studio Code

### <a name="mac-os"></a>**Mac OS**

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

## <a name="get-sql-server-connection-information"></a>Uzyskiwanie informacji o połączeniu z serwerem SQL

Uzyskaj parametry połączenia potrzebne do nawiązania połączenia z bazą danych Azure SQL Database. W następnych procedurach będą potrzebne w pełni kwalifikowana nazwa serwera lub nazwa hosta, nazwa bazy danych i informacje logowania.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

2. Otwórz stronę **Bazy danych SQL** lub **Wystąpienia zarządzane SQL**.

3. Na stronie **Przegląd** znajdź w pełni kwalifikowaną nazwę serwera obok pola **Nazwa serwera** (w przypadku pojedynczej bazy danych) lub w pełni kwalifikowaną nazwę serwera obok pola **Host** (w przypadku wystąpienia zarządzanego). Aby skopiować nazwę serwera lub hosta, umieść na niej wskaźnik myszy i wybierz ikonę **Kopiuj**.

## <a name="set-language-mode-to-sql"></a>Ustawianie trybu języka na SQL

W programie Visual Studio Code ustaw tryb języka na **SQL**, aby włączyć polecenia mssql i T-SQL IntelliSense.

1. Otwórz nowe okno programu Visual Studio Code.

2. Naciśnij **klawisze CTRL**+**N**. Zostanie otwarty nowy plik w formacie zwykłego tekstu.

3. Wybierz pozycję **Zwykły tekst** w prawym dolnym rogu paska stanu.

4. Z menu rozwijanego **Wybierz tryb języka**, które zostanie otwarte, wybierz pozycję **SQL**.

## <a name="connect-to-your-database"></a>Nawiązywanie połączenia z bazą danych

Użyj programu Visual Studio Code, aby nawiązać połączenie z serwerem usługi Azure SQL Database.

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
   | **Nazwa użytkownika** | Nazwa użytkownika | Nazwa użytkownika konta administratora serwera, którego użyto do utworzenia serwera. |
   | **Hasło (identyfikator logowania SQL)** | Hasło | Hasło użytkownika konta administratora serwera, którego użyto do utworzenia serwera. |
   | **Zapisać hasło?** | Tak lub Nie | Wybierz opcję **Tak**, jeśli nie chcesz wprowadzać hasła za każdym razem. |
   | **Wprowadź nazwę dla tego profilu** | Nazwa profilu, np. **mojPrzykladowyProfil** | Zapisany profil przyspiesza połączenie podczas kolejnych logowań. |

   W przypadku powodzenia zostanie wyświetlone powiadomienie z informacją o utworzeniu i połączeniu profilu.

## <a name="query-data"></a>Zapytania o dane

Uruchom następującą instrukcję [SELECT](https://msdn.microsoft.com/library/ms189499.aspx) języka Transact-SQL, aby wykonać zapytanie o 20 najpopularniejszych produktów według kategorii.

1. W oknie edytora wklej następujące zapytanie SQL.

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

2. Naciśnij **klawisze CTRL**+**SHIFT**+**E** , aby uruchomić zapytanie i wyświetlić wyniki z `Product` tabel `ProductCategory` i.

    ![Zapytanie do pobierania danych z 2 tabel](./media/sql-database-connect-query-vscode/query.png)

## <a name="insert-data"></a>Wstawianie danych

Uruchom następującą instrukcję [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) języka Transact-SQL, aby dodać nowy produkt do tabeli `SalesLT.Product`.

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

2. Naciśnij **klawisze CTRL**+**SHIFT**+**E** , aby wstawić nowy wiersz w `Product` tabeli.

## <a name="update-data"></a>Aktualizowanie danych

Uruchom następującą instrukcję [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx) języka Transact-SQL, aby zaktualizować dodany produkt.

1. Zastąp poprzednie zapytanie następującym:

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Naciśnij **klawisze CTRL**+**SHIFT**+**E** , aby zaktualizować określony wiersz w `Product` tabeli.

## <a name="delete-data"></a>Usuwanie danych

Uruchom następującą instrukcję [DELETE](https://docs.microsoft.com/sql/t-sql/statements/delete-transact-sql) języka Transact-SQL, aby usunąć nowy produkt.

1. Zastąp poprzednie zapytanie następującym:

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Naciśnij **klawisze CTRL**+**SHIFT**+**E** , aby usunąć określony wiersz w `Product` tabeli.

## <a name="next-steps"></a>Następne kroki

- Aby nawiązać połączenie i wykonać zapytanie przy użyciu SQL Server Management Studio, zobacz [Szybki Start: użyj SQL Server Management Studio, aby nawiązać połączenie z danymi Azure SQL Database i zapytań](sql-database-connect-query-ssms.md).
- Aby nawiązać połączenie i wykonać zapytanie przy użyciu Azure Portal, zobacz [Szybki Start: Użyj edytora zapytań SQL w Azure Portal, aby nawiązać połączenie i wykonać zapytania dotyczące danych](sql-database-connect-query-portal.md).
- Aby zapoznać się z artykułem w magazynie MSDN dotyczącym programu Visual Studio Code, zobacz temat [Create a database IDE with MSSQL extension blog post](https://msdn.microsoft.com/magazine/mt809115) (Tworzenie bazy danych w środowisku IDE, korzystając z wpisu na blogu dotyczącym rozszerzenia MSSQL).
