---
title: Tworzenie pojedynczej bazy danych
description: Utwórz pojedynczą bazę danych w Azure SQL Database przy użyciu Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: contperf-fy21q1, devx-track-azurecli
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/27/2021
ms.openlocfilehash: c0e86cad252da1b5484944c12351d4ee1e39edbe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101656321"
---
# <a name="quickstart-create-an-azure-sql-database-single-database"></a>Szybki Start: Tworzenie Azure SQL Database pojedynczej bazy danych

W tym przewodniku szybki start utworzysz [pojedynczą bazę danych](single-database-overview.md) w Azure SQL Database przy użyciu Azure Portal, skryptu programu PowerShell lub skryptu interfejsu wiersza polecenia platformy Azure. Następnie należy wykonać zapytanie do bazy danych za pomocą **edytora zapytań** w Azure Portal.



## <a name="prerequisite"></a>Wymaganie wstępne

- Aktywna subskrypcja platformy Azure. Jeśli jej nie masz, [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="create-a-single-database"></a>Tworzenie pojedynczej bazy danych

Ten przewodnik Szybki Start tworzy pojedynczą bazę danych w [warstwie obliczeń bezserwerowych](serverless-tier-overview.md).

# <a name="portal"></a>[Portal](#tab/azure-portal)

Aby utworzyć pojedynczą bazę danych w Azure Portal ten przewodnik Szybki Start rozpocznie się na stronie Azure SQL.

1. Przejdź do strony [Wybieranie opcji wdrożenia SQL](https://portal.azure.com/#create/Microsoft.AzureSQL) .
1. W obszarze **bazy danych SQL** pozostaw **Typ zasobu** ustawiony na **pojedynczą bazę danych**, a następnie wybierz pozycję **Utwórz**.

   ![Dodawanie do usługi Azure SQL](./media/single-database-create-quickstart/select-deployment.png)

1. Na karcie **podstawy** formularza **tworzenia SQL Database** w obszarze **szczegóły projektu** wybierz żądaną **subskrypcję** platformy Azure.
1. W obszarze **Grupa zasobów** wybierz pozycję **Utwórz nową**, wprowadź pozycję Grupa *zasobów*, a następnie wybierz pozycję **OK**.
1. W obszarze **Nazwa bazy danych** wprowadź *mySampleDatabase*.
1. W polu **serwer** wybierz pozycję **Utwórz nowy** i wypełnij formularz **nowy serwer** z następującymi wartościami:
   - **Nazwa serwera** *: Wprowadź nazwę programu,* a następnie Dodaj kilka znaków. Nie możemy podać dokładnej nazwy serwera, która ma być używana, ponieważ nazwy serwerów muszą być globalnie unikatowe dla wszystkich serwerów na platformie Azure, a nie tylko unikatowe w ramach subskrypcji. Wprowadź takie jak mysqlserver12345, a Portal pozwala sprawdzić, czy jest on dostępny.
   - **Identyfikator logowania administratora serwera**: wprowadź *azureuser*.
   - **Hasło**: wprowadź hasło spełniające wymagania i wprowadź je ponownie w polu **Potwierdź hasło** .
   - **Lokalizacja**: Wybierz lokalizację z listy rozwijanej.

   Wybierz przycisk **OK**.

1. Dla opcji **nie** **należy używać puli elastycznej SQL** .
1. W obszarze **Środowisko obliczeniowe i magazyn** wybierz pozycję **Skonfiguruj bazę danych**.
1. Ten przewodnik Szybki Start używa bezserwerowej bazy danych, więc wybierz opcję **Bezserwerowa**, a następnie wybierz pozycję **Zastosuj**. 

      ![Konfigurowanie bezserwerowej bazy danych](./media/single-database-create-quickstart/configure-database.png)

1. Wybierz pozycję **Dalej: sieci** w dolnej części strony.

   ![Nowa baza danych SQL — Karta podstawowa](./media/single-database-create-quickstart/new-sql-database-basics.png)

1. Na karcie **Sieć** w obszarze **Metoda połączenia** wybierz pozycję **publiczny punkt końcowy**.
1. W przypadku **reguł zapory** ustaw opcję **Dodaj bieżący adres IP klienta** na **wartość tak**. Pozostaw opcję **Zezwól usługom i zasobom platformy Azure na dostęp do tego serwera** jako " **nie**".
1. Wybierz pozycję **Dalej: dodatkowe ustawienia** w dolnej części strony.

   ![Karta Sieć](./media/single-database-create-quickstart/networking.png)
  

1. Na karcie **Ustawienia dodatkowe** w sekcji **Źródło danych** wybierz pozycję **przykład**, aby **użyć istniejących danych**. Spowoduje to utworzenie przykładowej bazy danych AdventureWorksLT, dzięki czemu istnieje kilka tabel i danych do zbadania i eksperymentowania, w przeciwieństwie do pustej pustej bazy danych.
1. Opcjonalnie można włączyć [usługę Azure Defender dla serwera SQL](../database/azure-defender-for-sql.md).
1. Opcjonalnie Ustaw [przedział czasu obsługi](../database/maintenance-window.md) tak, aby planowana konserwacja była wykonywana z najlepszymi dla bazy danych.
1. Wybierz pozycję **Przegląd + Utwórz** u dołu strony:

   ![Karta Ustawienia dodatkowe](./media/single-database-create-quickstart/additional-settings.png)

1. Na stronie **Recenzja i tworzenie** po przejrzeniu wybierz pozycję **Utwórz**.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

## <a name="launch-azure-cloud-shell"></a>Uruchamianie usługi Azure Cloud Shell

Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. 

Aby otworzyć usługę Cloud Shell, wybierz pozycję **Wypróbuj** w prawym górnym rogu bloku kodu. Cloud Shell można również uruchomić na osobnej karcie przeglądarki, przechodząc do [https://shell.azure.com](https://shell.azure.com) . Wybierz pozycję **Kopiuj** , aby skopiować bloki kodu, wkleić je do Cloud Shell i naciśnij klawisz **Enter** , aby go uruchomić.

## <a name="set-parameter-values"></a>Ustaw wartości parametrów

Poniższe wartości są używane w kolejnych poleceniach do tworzenia bazy danych i wymaganych zasobów. Nazwy serwerów muszą być globalnie unikatowe na całym platformie Azure, dzięki czemu do tworzenia nazwy serwera jest używana funkcja $RANDOM. Zastąp wartości 0.0.0.0 w zakresie adresów IP, aby dopasować je do określonego środowiska.

```azurecli-interactive
# Set the resource group name and location for your server
resourceGroupName=myResourceGroup
location=eastus

# Set an admin login and password for your database
adminlogin=azureuser
password=Azure1234567!

# Set a server name that is unique to Azure DNS (<server_name>.database.windows.net)
serverName=server-$RANDOM

# Set the ip address range that can access your database
startip=0.0.0.0
endip=0.0.0.0
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Poniższy przykład tworzy grupę zasobów o nazwie Moja *zasobów* w lokalizacji *Wschodnie* :

```azurecli-interactive
az group create --name $resourceGroupName --location $location
```

## <a name="create-a-server"></a>Tworzenie serwera

Utwórz serwer za pomocą polecenia [AZ SQL Server Create](/cli/azure/sql/server) .

```azurecli-interactive
az sql server create \
    --name $serverName \
    --resource-group $resourceGroupName \
    --location $location  \
    --admin-user $adminlogin \
    --admin-password $password
```


## <a name="configure-a-firewall-rule-for-the-server"></a>Konfigurowanie reguły zapory dla serwera

Utwórz regułę zapory za pomocą polecenia [AZ SQL Server firewall-Rule Create](/cli/azure/sql/server/firewall-rule) .

```azurecli-interactive
az sql server firewall-rule create \
    --resource-group $resourceGroupName \
    --server $serverName \
    -n AllowYourIp \
    --start-ip-address $startip \
    --end-ip-address $endip
```


## <a name="create-a-single-database-with-azure-cli"></a>Tworzenie pojedynczej bazy danych za pomocą interfejsu wiersza polecenia platformy Azure

Utwórz bazę danych za pomocą polecenia [AZ SQL DB Create](/cli/azure/sql/db) . Poniższy kod tworzy


```azurecli-interactive
az sql db create \
    --resource-group $resourceGroupName \
    --server $serverName \
    --name mySampleDatabase \
    --sample-name AdventureWorksLT \
    --edition GeneralPurpose \
    --compute-model Serverless \
    --family Gen5 \
    --capacity 2
```


# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Korzystając z programu Windows PowerShell, można utworzyć grupę zasobów, serwer i pojedynczą bazę danych.

## <a name="launch-azure-cloud-shell"></a>Uruchamianie usługi Azure Cloud Shell

Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. 

Aby otworzyć usługę Cloud Shell, wybierz pozycję **Wypróbuj** w prawym górnym rogu bloku kodu. Cloud Shell można również uruchomić na osobnej karcie przeglądarki, przechodząc do [https://shell.azure.com](https://shell.azure.com) . Wybierz pozycję **Kopiuj** , aby skopiować bloki kodu, wkleić je do Cloud Shell i naciśnij klawisz **Enter** , aby go uruchomić.

## <a name="set-parameter-values"></a>Ustaw wartości parametrów

Poniższe wartości są używane w kolejnych poleceniach do tworzenia bazy danych i wymaganych zasobów. Nazwy serwerów muszą być globalnie unikatowe na wszystkich platformie Azure, aby utworzyć nazwę serwera za pomocą polecenia cmdlet Get-Random. Zastąp wartości 0.0.0.0 w zakresie adresów IP, aby dopasować je do określonego środowiska.

```azurepowershell-interactive
   # Set variables for your server and database
   $resourceGroupName = "myResourceGroup"
   $location = "eastus"
   $adminLogin = "azureuser"
   $password = "Azure1234567!"
   $serverName = "mysqlserver-$(Get-Random)"
   $databaseName = "mySampleDatabase"

   # The ip address range that you want to allow to access your server
   $startIp = "0.0.0.0"
   $endIp = "0.0.0.0"

   # Show randomized variables
   Write-host "Resource group name is" $resourceGroupName
   Write-host "Server name is" $serverName
```


## <a name="create-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów platformy Azure za pomocą polecenia [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

```azurepowershell-interactive
   Write-host "Creating resource group..."
   $resourceGroup = New-AzResourceGroup -Name $resourceGroupName -Location $location -Tag @{Owner="SQLDB-Samples"}
   $resourceGroup
```


## <a name="create-a-server"></a>Tworzenie serwera

Utwórz serwer przy użyciu polecenia cmdlet [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) .

```azurepowershell-interactive
  Write-host "Creating primary server..."
   $server = New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -Location $location `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
      -ArgumentList $adminLogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $server
```

## <a name="create-a-firewall-rule"></a>Tworzenie reguły zapory

Utwórz regułę zapory serwera za pomocą polecenia cmdlet [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) .

```azurepowershell-interactive
   Write-host "Configuring server firewall rule..."
   $serverFirewallRule = New-AzSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
   $serverFirewallRule
```


## <a name="create-a-single-database-with-powershell"></a>Tworzenie pojedynczej bazy danych przy użyciu programu PowerShell

Utwórz pojedynczą bazę danych za pomocą polecenia cmdlet [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) .

```azurepowershell-interactive
   Write-host "Creating a gen5 2 vCore serverless database..."
   $database = New-AzSqlDatabase  -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName `
      -Edition GeneralPurpose `
      -ComputeModel Serverless `
      -ComputeGeneration Gen5 `
      -VCore 2 `
      -MinimumCapacity 2 `
      -SampleName "AdventureWorksLT"
   $database
```

---



## <a name="query-the-database"></a>Wykonywanie zapytań względem bazy danych

Po utworzeniu bazy danych można użyć **edytora zapytań (wersja zapoznawcza)** w Azure Portal, aby nawiązać połączenie z bazą danych i danymi zapytań.

1. W portalu Wyszukaj i wybierz pozycję **bazy danych SQL**, a następnie wybierz bazę danych z listy.
1. Na stronie bazy danych wybierz pozycję **Edytor zapytań (wersja zapoznawcza)** w menu po lewej stronie.
1. Wprowadź dane logowania administratora serwera, a następnie wybierz **przycisk OK**.

   ![Logowanie w Edytorze zapytań](./media/single-database-create-quickstart/query-editor-login.png)

1. Wprowadź następujące zapytanie w okienku **Edytora zapytań**.

   ```sql
   SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
   FROM SalesLT.ProductCategory pc
   JOIN SalesLT.Product p
   ON pc.productcategoryid = p.productcategoryid;
   ```

1. Wybierz opcję **Uruchom**, a następnie przejrzyj wyniki zapytania w okienku **Wyniki**.

   ![Wyniki Edytora zapytań](./media/single-database-create-quickstart/query-editor-results.png)

1. Zamknij stronę **Edytor zapytań**, a następnie kliknij przycisk **OK** po wyświetleniu monitu o odrzucenie niezapisanych zmian.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Należy zachować grupę zasobów, serwer i pojedynczą bazę danych, aby przejść do następnych kroków i dowiedzieć się, jak nawiązać połączenie z bazą danych i wysyłać do niej zapytania przy użyciu różnych metod.

Po zakończeniu korzystania z tych zasobów można usunąć utworzoną grupę zasobów, która spowoduje również usunięcie serwera i pojedynczej bazy danych w ramach tej grupy.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Aby usunąć **zasób** i wszystkie jego zasoby przy użyciu Azure Portal:

1. W portalu Wyszukaj i wybierz pozycję **grupy zasobów**, a następnie **Wybierz z listy pozycję Lista zasobów.**
1. Na stronie Grupa zasobów wybierz pozycję **Usuń grupę zasobów**.
1. W obszarze **wpisz nazwę grupy zasobów** *, wprowadź,* a następnie wybierz pozycję **Usuń**.

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby usunąć grupę zasobów i wszystkie jej zasoby, uruchom następujące polecenie interfejsu wiersza polecenia platformy Azure, używając nazwy grupy zasobów:

```azurecli-interactive
az group delete --name $resourceGroupName
```

### <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Aby usunąć grupę zasobów i wszystkie jej zasoby, uruchom następujące polecenie cmdlet programu PowerShell, używając nazwy grupy zasobów:

```azurepowershell-interactive
Remove-AzResourceGroup -Name $resourceGroupName
```

---

## <a name="next-steps"></a>Następne kroki

[Łączenie i wykonywanie zapytań](connect-query-content-reference-guide.md) dotyczących bazy danych przy użyciu różnych narzędzi i języków:
> [!div class="nextstepaction"]
> [Nawiązywanie połączeń i wykonywanie zapytań przy użyciu programu SQL Server Management Studio](connect-query-ssms.md)
>
> [Nawiązywanie połączeń i wykonywanie zapytań za pomocą usługi Azure Data Studio](/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)

Chcesz zoptymalizować i zapisać wydatki na chmurę?

> [!div class="nextstepaction"]
> [Rozpocznij analizowanie kosztów za pomocą Cost Management](../../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)