---
author: MashaMSFT
ms.service: sql-database
ms.subservice: single-database
ms.topic: include
ms.date: 03/10/2020
ms.author: mathoma
ms.reviewer: vanto
ms.openlocfilehash: 563ac8748e9d4f8a254a151814491113b9c816e6
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81685134"
---
W tym kroku utworzysz serwer bazy danych SQL Azure i pojedynczą bazę danych, która używa przykładowych danych AdventureWorksLT. Bazę danych można utworzyć przy użyciu menu i ekranów witryny Azure portal lub przy użyciu skryptu interfejsu wiersza polecenia platformy Azure lub programu PowerShell w usłudze Azure Cloud Shell.

Wszystkie metody obejmują konfigurowanie reguły zapory na poziomie serwera, aby zezwolić na publiczny adres IP komputera, którego używasz, aby uzyskać dostęp do serwera. Aby uzyskać więcej informacji na temat tworzenia reguł zapory serwera, zobacz [Tworzenie zapory na poziomie serwera](../sql-database-server-level-firewall-rule.md). Można również ustawić reguły zapory na poziomie bazy danych. Zobacz [Tworzenie reguły zapory na poziomie bazy danych](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database). 

# <a name="portal"></a>[Portal](#tab/azure-portal)

Aby utworzyć grupę zasobów, serwer SQL i pojedynczą bazę danych w witrynie Azure portal:

1. Zaloguj się do [portalu](https://portal.azure.com).
1. Na pasku wyszukiwania wyszukaj i wybierz pozycję **Azure SQL**.
1. Na stronie **Sql platformy Azure** wybierz pozycję **Dodaj**. 
   
   ![Dodaj do usługi Azure SQL](../media/sql-database-single-database-get-started/sqldbportal.png)
   
1. Na stronie **opcji Wybierz wdrożenie SQL** wybierz kafelek bazy danych **SQL** z **pojedynczą bazą danych** w obszarze **Typ zasobu**. Więcej informacji o różnych bazach danych można wyświetlić, wybierając pozycję **Pokaż szczegóły**.
1. Wybierz pozycję **Utwórz**.
   
   ![Tworzenie pojedynczej bazy danych](../media/sql-database-single-database-get-started/create-single-database.png)
   
1. Na karcie **Podstawy** formularza **Tworzenie bazy danych SQL** w obszarze Szczegóły **projektu**wybierz poprawną **subskrypcję** platformy Azure, jeśli nie jest jeszcze zaznaczona.
1. W obszarze **Grupa zasobów**wybierz pozycję **Utwórz nowy**, wprowadź *myResourceGroup*i wybierz przycisk **OK**.
1. W obszarze **Szczegóły bazy danych**dla nazwy bazy danych wprowadź *mySampleDatabase*. **Database name**
1. W przypadku **opcji Serwer**wybierz **pozycję Utwórz nowy**i wypełnij formularz Nowy **serwer** w następujący sposób:
   - **Nazwa serwera**: Wprowadź *mysqlserver*, a niektóre znaki dla unikatowości.
   - **Logowanie administratora serwera**: Wprowadź *azureuser*.
   - **Hasło:** Wprowadź hasło spełniające wymagania i wprowadź go ponownie w polu **Potwierdź hasło.**
   - **Lokalizacja**: Rozwijana i wybierz lokalizację, na przykład **(USA) Wschodnie stany USA**.
   
   Kliknij przycisk **OK**.
   
   ![Nowy serwer](../media/sql-database-single-database-get-started/new-server.png)
   
   Zarejestruj login i hasło administratora serwera, aby można było zalogować się do serwera i baz danych. Jeśli zapomnisz loginu lub hasła, możesz uzyskać nazwę logowania lub zresetować hasło na stronie **serwera SQL** po utworzeniu bazy danych. Aby otworzyć stronę **serwera SQL,** wybierz nazwę serwera na stronie **Przegląd** bazy danych.
   
1. W obszarze **Obliczaj + magazyn**, jeśli chcesz ponownie skonfigurować ustawienia domyślne, wybierz pozycję **Konfiguruj bazę danych**.
   
   Na stronie **Konfigurowanie** można opcjonalnie:
   - Zmień **warstwę obliczeniową** z **Aprowizowana** na **Bezserwerową**.
   - Przejrzyj i zmień ustawienia dla **rów wirtualnych** i **maksymalnego rozmiaru danych**.
   - Wybierz **zmień konfigurację,** aby zmienić generowanie sprzętu.
   
   Po dokonaniu jakichkolwiek zmian wybierz pozycję **Zastosuj**.
   
1. **Wybierz dalej: Sieć** u dołu strony.
   
   ![Nowa baza danych SQL — karta Podstawowe](../media/sql-database-single-database-get-started/new-sql-database-basics.png)
   
1. Na karcie **Sieć** w obszarze **Metoda Łączność**wybierz pozycję **Publiczny punkt końcowy**. 
1. W obszarze **Reguły zapory**ustaw **dodaj bieżący adres IP klienta** do **Tak**.
1. **Wybierz dalej: Dodatkowe ustawienia** u dołu strony.
   
   ![Karta Sieć](../media/sql-database-single-database-get-started/networking.png)
   
   Aby uzyskać więcej informacji na temat ustawień zapory, zobacz [Zezwalanie usługom i zasobom platformy Azure na dostęp do tego serwera](../sql-database-networkaccess-overview.md) i dodawanie prywatnego punktu [końcowego](../../private-link/private-endpoint-overview.md).
   
1. Na karcie **Ustawienia dodatkowe** w sekcji **Źródło danych** w obszarze Korzystanie z **istniejących danych**wybierz pozycję **Przykładowy**.
1. Wybierz **pozycję Recenzja + utwórz** u dołu strony.
   
   ![Karta Ustawienia dodatkowe](../media/sql-database-single-database-get-started/additional-settings.png)
   
1. Po przejrzeniu ustawień wybierz pozycję **Utwórz**.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Można utworzyć grupę zasobów platformy Azure, serwer SQL i pojedynczą bazę danych przy użyciu interfejsu wiersza polecenia platformy Azure (Azure CLI). Jeśli nie chcesz używać usługi Azure Cloud Shell, [zainstaluj narzędzie Azure CLI](/cli/azure/install-azure-cli) na swoim komputerze.

Aby uruchomić poniższy przykład kodu w usłudze Azure Cloud Shell, wybierz pozycję **Wypróbuj na** pasku tytułu przykładowego kodu. Po otwarciu powłoki chmury wybierz **pozycję Kopiuj** na pasku tytułu przykładowego kodu i wklej przykładowy kod do okna Powłoki chmury. W kodzie `<Subscription ID>` zastąp identyfikatorem subskrypcji `$startip` `$endip`platformy `0.0.0.0` Azure oraz za i , za pomocą publicznego adresu IP używanego komputera.

Postępuj zgodnie z monitami wyświetlanymi na ekranie, aby zalogować się na platformę Azure i uruchomić kod. 

Możesz również użyć usługi Azure Cloud Shell z witryny Azure portal, wybierając ikonę powłoki chmury na górnym pasku. 
   
   ![Azure Cloud Shell](../media/sql-database-single-database-get-started/cloudshell.png)
   
Przy pierwszym użyciu powłoki chmury w portalu wybierz pozycję **Bash** w oknie dialogowym **Powitanie.** Kolejne sesje będą używać interfejsu wiersza polecenia platformy Azure w środowisku bash lub można wybrać **bash** z paska sterowania powłoki chmury. 

Poniższy kod interfejsu wiersza polecenia platformy Azure tworzy grupę zasobów platformy Azure, serwer SQL, pojedynczą bazę danych i regułę zapory w celu uzyskania dostępu do serwera. Pamiętaj, aby rejestrować wygenerowane nazwy grup zasobów i serwerów, aby później zarządzać tymi zasobami.

```azurecli-interactive
#!/bin/bash

# Sign in to Azure and set execution context (if necessary)
az login
az account set --subscription <Subscription ID>

# Set the resource group name and location for your server
resourceGroupName=myResourceGroup-$RANDOM
location=westus2

# Set an admin login and password for your database
adminlogin=azureuser
password=Azure1234567

# Set a logical server name that is unique to Azure DNS (<server_name>.database.windows.net)
servername=server-$RANDOM

# Set the ip address range that can access your database
startip=0.0.0.0
endip=0.0.0.0

# Create a resource group
az group create \
    --name $resourceGroupName \
    --location $location

# Create a logical server in the resource group
az sql server create \
    --name $servername \
    --resource-group $resourceGroupName \
    --location $location  \
    --admin-user $adminlogin \
    --admin-password $password

# Configure a firewall rule for the server
az sql server firewall-rule create \
    --resource-group $resourceGroupName \
    --server $servername \
    -n AllowYourIp \
    --start-ip-address $startip \
    --end-ip-address $endip

# Create a gen5 2 vCore database in the server
az sql db create \
    --resource-group $resourceGroupName \
    --server $servername \
    --name mySampleDatabase \
    --sample-name AdventureWorksLT \
    --edition GeneralPurpose \
    --family Gen5 \
    --capacity 2 \
```

Poprzedni kod używa tych poleceń interfejsu wiersza polecenia platformy Azure:

| Polecenie | Opis |
|---|---|
| [az zestaw kont](/cli/azure/account?view=azure-cli-latest#az-account-set) | Ustawia subskrypcję jako bieżącą aktywną subskrypcję. | 
| [az group create](/cli/azure/group#az-group-create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | Tworzy serwer usługi SQL Database hostujący pojedyncze bazy danych i elastyczne pule. |
| [az sql server firewall-rule create az sql server firewall-rule create az sql server firewall-rule create az](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-create) | Tworzy reguły zapory serwera. | 
| [az sql db create](/cli/azure/sql/db#az-sql-db-create?view=azure-cli-latest) | Tworzy bazę danych. | 

Aby uzyskać więcej przykładów interfejsu wiersza polecenia platformy Azure bazy danych SQL, zobacz [przykłady interfejsu wiersza polecenia platformy Azure.](../sql-database-cli-samples.md)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Za pomocą programu Windows PowerShell można utworzyć grupę zasobów platformy Azure, serwer SQL i pojedynczą bazę danych. Jeśli nie chcesz używać usługi Azure Cloud Shell, [zainstaluj moduł programu Azure PowerShell.](/powershell/azure/install-az-ps)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Aby uruchomić poniższy przykład kodu w usłudze Azure Cloud Shell, wybierz pozycję **Wypróbuj na** pasku tytułu kodu. Po otwarciu powłoki chmury wybierz **pozycję Kopiuj** na pasku tytułu przykładowego kodu i wklej przykładowy kod do okna Powłoki chmury. W kodzie `<Subscription ID>` zastąp identyfikatorem subskrypcji `$startIp` `$endIp`platformy `0.0.0.0` Azure oraz za i , za pomocą publicznego adresu IP używanego komputera. 

Postępuj zgodnie z monitami wyświetlanymi na ekranie, aby zalogować się na platformę Azure i uruchomić kod. 

Możesz również użyć usługi Azure Cloud Shell z witryny Azure portal, wybierając ikonę powłoki chmury na górnym pasku. 
   
   ![Azure Cloud Shell](../media/sql-database-single-database-get-started/cloudshell.png)
   
Przy pierwszym użyciu powłoki chmury z portalu wybierz pozycję **PowerShell** w oknie dialogowym **Powitalny.** Kolejne sesje będą używać programu PowerShell lub można go wybrać z paska sterowania powłoki chmury. 

Poniższy kod programu PowerShell tworzy grupę zasobów platformy Azure, serwer SQL, pojedynczą bazę danych i regułę zapory w celu uzyskania dostępu do serwera. Pamiętaj, aby rejestrować wygenerowane nazwy grup zasobów i serwerów, aby później zarządzać tymi zasobami.

   ```powershell-interactive
   # Set variables for your server and database
   $subscriptionId = '<SubscriptionID>'
   $resourceGroupName = "myResourceGroup-$(Get-Random)"
   $location = "West US"
   $adminLogin = "azureuser"
   $password = "Azure1234567"
   $serverName = "mysqlserver-$(Get-Random)"
   $databaseName = "mySampleDatabase"

   # The ip address range that you want to allow to access your server 
   $startIp = "0.0.0.0"
   $endIp = "0.0.0.0"

   # Show randomized variables
   Write-host "Resource group name is" $resourceGroupName 
   Write-host "Server name is" $serverName 

   # Connect to Azure
   Connect-AzAccount

   # Set subscription ID
   Set-AzContext -SubscriptionId $subscriptionId 

   # Create a resource group
   Write-host "Creating resource group..."
   $resourceGroup = New-AzResourceGroup -Name $resourceGroupName -Location $location -Tag @{Owner="SQLDB-Samples"}
   $resourceGroup

   # Create a server with a system wide unique server name
   Write-host "Creating primary logical server..."
   $server = New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -Location $location `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
      -ArgumentList $adminLogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $server

   # Create a server firewall rule that allows access from the specified IP range
   Write-host "Configuring firewall for primary logical server..."
   $serverFirewallRule = New-AzSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
   $serverFirewallRule

   # Create General Purpose Gen4 database with 1 vCore
   Write-host "Creating a gen5 2 vCore database..."
   $database = New-AzSqlDatabase  -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName `
      -Edition GeneralPurpose `
      -VCore 2 `
      -ComputeGeneration Gen5 `
      -MinimumCapacity 2 `
      -SampleName "AdventureWorksLT"
   $database
   ```

Poprzedni kod używa tych poleceń cmdlet programu PowerShell:

| Polecenie | Uwagi |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [Nowy serwer AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Tworzy serwer usługi SQL Database hostujący pojedyncze bazy danych i elastyczne pule. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Tworzy regułę zapory dla serwera logicznego. | 
| [Nowa baza danych AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Tworzy pojedynczą bazę danych usługi Azure SQL Database. | 

Aby uzyskać więcej przykładów programu Azure SQL Database PowerShell, zobacz [przykłady programu Azure PowerShell.](../sql-database-powershell-samples.md)

---
