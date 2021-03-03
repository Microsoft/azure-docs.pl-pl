---
author: MashaMSFT
ms.service: sql-database
ms.subservice: single-database
ms.topic: include
ms.date: 03/10/2020
ms.author: sstein
ms.reviewer: vanto
ms.openlocfilehash: 57f504b15c0a9c72a2cb0f17b486846f44171a25
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101682389"
---
W tym kroku utworzysz [logiczny serwer SQL](../database/logical-servers.md) i [pojedynczą bazę danych](../database/single-database-overview.md) , która korzysta z przykładowych danych AdventureWorksLT. Bazę danych można utworzyć za pomocą menu Azure Portal i ekranów, a także za pomocą interfejsu wiersza polecenia platformy Azure lub skryptu programu PowerShell w Azure Cloud Shell.

Wszystkie metody obejmują skonfigurowanie reguły zapory na poziomie serwera, aby zezwolić na publiczny adres IP komputera, którego używasz do uzyskiwania dostępu do serwera. Aby uzyskać więcej informacji na temat tworzenia reguł zapory na poziomie serwera, zobacz [Tworzenie zapory na poziomie serwera](../database/firewall-create-server-level-portal-quickstart.md). Można również ustawić reguły zapory na poziomie bazy danych. Zobacz [Tworzenie reguły zapory na poziomie bazy danych](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database).

# <a name="portal"></a>[Portal](#tab/azure-portal)

Aby utworzyć grupę zasobów, serwer i pojedynczą bazę danych w Azure Portal:

1. Zaloguj się do [portalu](https://portal.azure.com).
1. Na pasku wyszukiwania Wyszukaj i wybierz pozycję **Azure SQL**.
1. Na stronie **Azure SQL** wybierz pozycję **Dodaj**.

   ![Dodawanie do usługi Azure SQL](./media/sql-database-create-single-database/sqldbportal.png)

1. Na stronie **Wybierz opcję wdrożenia SQL** wybierz kafelek **bazy danych SQL** z **pojedynczą bazą danych** w obszarze **Typ zasobu**. Aby wyświetlić więcej informacji na temat różnych baz danych, wybierz pozycję **Pokaż szczegóły**.
1. Wybierz przycisk **Utwórz**.

   ![Tworzenie pojedynczej bazy danych](./media/sql-database-create-single-database/create-single-database.png)

1. Na karcie **podstawowe** w formularzu **Tworzenie bazy danych SQL** w obszarze **szczegóły projektu** Wybierz poprawną **subskrypcję** platformy Azure, jeśli nie została jeszcze wybrana.
1. W obszarze **Grupa zasobów** wybierz pozycję **Utwórz nową**, wprowadź pozycję Grupa *zasobów*, a następnie wybierz pozycję **OK**.
1. W obszarze **szczegóły bazy danych** w polu **Nazwa bazy danych** wprowadź *mySampleDatabase*.
1. W polu **serwer** wybierz pozycję **Utwórz nowy** i wypełnij formularz **nowy serwer** w następujący sposób:
   - **Nazwa serwera**: wprowadź dla elementu *SqlServer*, a niektóre znaki są unikatowe.
   - **Identyfikator logowania administratora serwera**: wprowadź *azureuser*.
   - **Hasło**: wprowadź hasło spełniające wymagania i wprowadź je ponownie w polu **Potwierdź hasło** .
   - **Lokalizacja**: Lista rozwijana i wybierz lokalizację, na przykład **(US) Wschodnie stany USA**.

   Wybierz przycisk **OK**.

   ![Nowy serwer](./media/sql-database-create-single-database/new-server.png)

   Zarejestruj nazwę logowania administratora serwera i hasło, aby można było zalogować się do serwera i jego baz danych. Jeśli zapomnisz o logowaniu lub haśle, możesz uzyskać nazwę logowania lub zresetować hasło na stronie **programu SQL Server** po utworzeniu bazy danych. Aby otworzyć stronę **programu SQL Server** , wybierz nazwę serwera na stronie **Przegląd** bazy danych.

1. Jeśli chcesz zmienić ustawienia domyślne w obszarze **obliczeniowy + magazyn**, wybierz pozycję **Konfiguruj bazę danych**.

   Na stronie **Konfiguracja** można opcjonalnie:
   - Zmień **warstwę obliczeń** z **aprowizacji** na **bezserwerowe**.
   - Przejrzyj i Zmień ustawienia dla **rdzeni wirtualnych** i **Maksymalny rozmiar danych**.
   - Wybierz pozycję **Zmień konfigurację** , aby zmienić generowanie sprzętu.

   Po wprowadzeniu zmian wybierz pozycję **Zastosuj**.

1. Wybierz pozycję **Dalej: sieci** w dolnej części strony.

   ![Nowa baza danych SQL — Karta podstawowa](./media/sql-database-create-single-database/new-sql-database-basics.png)

1. Na karcie **Sieć** w obszarze **Metoda połączenia** wybierz pozycję **publiczny punkt końcowy**.
1. W obszarze **reguły zapory** ustaw opcję **Dodaj bieżący adres IP klienta** na **wartość tak**.
1. Wybierz pozycję **Dalej: dodatkowe ustawienia** w dolnej części strony.

   ![Karta Sieć](./media/sql-database-create-single-database/networking.png)
  
   Aby uzyskać więcej informacji na temat ustawień zapory, zobacz [Zezwalanie usługom i zasobom platformy Azure na dostęp do tego serwera](../database/network-access-controls-overview.md) i [Dodawanie prywatnego punktu końcowego](../database/private-endpoint-overview.md).

1. Na karcie **Ustawienia dodatkowe** w sekcji **Źródło danych** wybierz pozycję **przykład**, aby **użyć istniejących danych**.
1. Opcjonalnie można włączyć [usługę Azure Defender dla serwera SQL](../database/azure-defender-for-sql.md).
1. Opcjonalnie Ustaw [przedział czasu obsługi](../database/maintenance-window.md) tak, aby planowana konserwacja była wykonywana z najlepszymi dla bazy danych.
1. Wybierz pozycję **Recenzja + Utwórz** w dolnej części strony.

   ![Karta Ustawienia dodatkowe](./media/sql-database-create-single-database/additional-settings.png)

1. Po przejrzeniu ustawień wybierz pozycję **Utwórz**.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Można utworzyć grupę zasobów platformy Azure, serwer i pojedynczą bazę danych przy użyciu interfejsu wiersza polecenia platformy Azure (Azure CLI). Jeśli nie chcesz używać Azure Cloud Shell, [Zainstaluj interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) na komputerze.

Aby uruchomić następujący przykład kodu w Azure Cloud Shell, wybierz opcję **Wypróbuj** na pasku tytułu przykładu kodu. Po otwarciu Cloud Shell wybierz pozycję **Kopiuj** na pasku tytułu przykład kodu, a następnie wklej przykładowy kod do okna Cloud Shell. W kodzie Zastąp ciąg `<Subscription ID>` identyfikatorem subskrypcji platformy Azure, a dla `$startip` i `$endip` Zastąp ciąg `0.0.0.0` adresem IP używanego komputera.

Postępuj zgodnie z monitami na ekranie, aby zalogować się do platformy Azure i uruchomić kod.

Możesz również użyć Azure Cloud Shell z Azure Portal, wybierając ikonę Cloud Shell na górnym pasku.

   ![Azure Cloud Shell](./media/sql-database-create-single-database/cloudshell.png)

Przy pierwszym użyciu Cloud Shell w portalu wybierz pozycję **bash** w oknie dialogowym **powitalnym** . Kolejne sesje będą używać interfejsu wiersza polecenia platformy Azure w środowisku bashu lub można wybrać **bash** z paska sterowania Cloud Shell.

Poniższy kod interfejsu wiersza polecenia platformy Azure tworzy grupę zasobów, serwer, pojedynczą bazę danych i regułę zapory adresów IP na poziomie serwera na potrzeby dostępu do serwera. Upewnij się, że zarejestrowano wytworzoną grupę zasobów i nazwy serwerów, aby można było później zarządzać tymi zasobami.

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

# Set a server name that is unique to Azure DNS (<server_name>.database.windows.net)
servername=server-$RANDOM

# Set the ip address range that can access your database
startip=0.0.0.0
endip=0.0.0.0

# Create a resource group
az group create \
    --name $resourceGroupName \
    --location $location

# Create a server in the resource group
az sql server create \
    --name $servername \
    --resource-group $resourceGroupName \
    --location $location  \
    --admin-user $adminlogin \
    --admin-password $password

# Configure a server-level firewall rule for the server
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
| [AZ Account Set](/cli/azure/account?view=azure-cli-latest#az-account-set&preserve-view=true) | Ustawia subskrypcję jako bieżącą aktywną subskrypcję. |
| [az group create](/cli/azure/group#az-group-create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | Tworzy serwer, który hostuje bazy danych i pule elastyczne. |
| [AZ SQL Server firewall-Rule Create](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-create) | Tworzy regułę zapory na poziomie serwera. |
| [az sql db create](/cli/azure/sql/db#az-sql-db-create?view=azure-cli-latest&preserve-view=true) | Tworzy bazę danych. |

Aby uzyskać więcej Azure SQL Database przykładów interfejsu wiersza polecenia platformy Azure, zobacz [przykłady interfejsu wiersza polecenia platformy Azure](../database/az-cli-script-samples-content-guide.md).

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Korzystając z programu Windows PowerShell, można utworzyć grupę zasobów, serwer i pojedynczą bazę danych. Jeśli nie chcesz używać Azure Cloud Shell, [Zainstaluj moduł Azure PowerShell](/powershell/azure/install-az-ps).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Aby uruchomić Poniższy przykład kodu w Azure Cloud Shell, wybierz opcję **Wypróbuj** na pasku tytułu kodu. Po otwarciu Cloud Shell wybierz pozycję **Kopiuj** na pasku tytułu przykład kodu, a następnie wklej przykładowy kod do okna Cloud Shell. W kodzie Zastąp ciąg `<Subscription ID>` identyfikatorem subskrypcji platformy Azure, a dla `$startIp` i `$endIp` Zastąp ciąg `0.0.0.0` adresem IP używanego komputera.

Postępuj zgodnie z monitami na ekranie, aby zalogować się do platformy Azure i uruchomić kod.

Możesz również użyć Azure Cloud Shell z Azure Portal, wybierając ikonę Cloud Shell na górnym pasku.

   ![Azure Cloud Shell](./media/sql-database-create-single-database/cloudshell.png)

Przy pierwszym użyciu Cloud Shell z portalu wybierz opcję **PowerShell** w oknie dialogowym **powitalnym** . Kolejne sesje będą korzystać z programu PowerShell lub można je wybrać z paska sterowania Cloud Shell.

Poniższy kod programu PowerShell tworzy grupę zasobów platformy Azure, serwer, pojedynczą bazę danych i regułę zapory na potrzeby dostępu do serwera. Upewnij się, że zarejestrowano wytworzoną grupę zasobów i nazwy serwerów, aby można było później zarządzać tymi zasobami.

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
   Write-host "Creating primary server..."
   $server = New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -Location $location `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
      -ArgumentList $adminLogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $server

   # Create a server firewall rule that allows access from the specified IP range
   Write-host "Configuring firewall for primary server..."
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

Poprzedni kod używa następujących poleceń cmdlet programu PowerShell:

| Polecenie | Uwagi |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Tworzy serwer, który hostuje bazy danych i pule elastyczne. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Tworzy regułę zapory na poziomie serwera dla serwera. |
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Tworzy bazę danych. |

Aby uzyskać więcej Azure SQL Database przykładów programu PowerShell, zobacz [Azure PowerShell Samples](../database/powershell-script-content-guide.md).

---
