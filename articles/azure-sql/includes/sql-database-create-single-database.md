---
author: MashaMSFT
ms.service: sql-database
ms.subservice: single-database
ms.topic: include
ms.date: 03/10/2020
ms.author: sstein
ms.reviewer: vanto
ms.openlocfilehash: 0568860c387aa1239ec56005e404606272ae8275
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107800302"
---
W tym kroku utworzysz serwer [logiczny SQL i](../database/logical-servers.md) pojedynczą [bazę danych,](../database/single-database-overview.md) która korzysta z przykładowych danych AdventureWorksLT. Bazę danych można utworzyć przy użyciu Azure Portal menu i ekranów albo za pomocą interfejsu wiersza polecenia platformy Azure lub skryptu programu PowerShell w Azure Cloud Shell.

Wszystkie metody obejmują skonfigurowanie reguły zapory na poziomie serwera, aby zezwolić na publiczny adres IP komputera, za pomocą których uzyskujesz dostęp do serwera. Aby uzyskać więcej informacji na temat tworzenia reguł zapory na poziomie serwera, zobacz [Tworzenie zapory na poziomie serwera](../database/firewall-create-server-level-portal-quickstart.md). Można również ustawić reguły zapory na poziomie bazy danych. Zobacz [Tworzenie reguły zapory na poziomie bazy danych.](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)

# <a name="portal"></a>[Portal](#tab/azure-portal)

Aby utworzyć grupę zasobów, serwer i pojedynczą bazę danych w Azure Portal:

1. Zaloguj się do [portalu](https://portal.azure.com).
1. Na pasku wyszukiwania wyszukaj i wybierz **pozycję Azure SQL**.
1. Na stronie **Azure SQL** wybierz pozycję **Dodaj**.

   ![Dodaj do Azure SQL](./media/sql-database-create-single-database/sqldbportal.png)

1. Na stronie **Wybierz opcję wdrożenia SQL** wybierz kafelek Bazy danych **SQL** z opcją Pojedyncza **baza danych** w obszarze **Typ zasobu.** Aby wyświetlić więcej informacji o różnych bazach danych, wybierz pozycję **Pokaż szczegóły.**
1. Wybierz przycisk **Utwórz**.

   ![Tworzenie pojedynczej bazy danych](./media/sql-database-create-single-database/create-single-database.png)

1. Na karcie **Podstawy formularza** Tworzenie bazy danych **SQL** w obszarze **Szczegóły** projektu wybierz poprawną subskrypcję platformy **Azure,** jeśli nie została jeszcze wybrana.
1. W **obszarze Grupa zasobów** wybierz pozycję Utwórz **nową,** wprowadź *nazwę myResourceGroup* i wybierz przycisk **OK.**
1. W **obszarze Szczegóły bazy** danych w obszarze Nazwa bazy **danych** wprowadź *wartość mySampleDatabase*.
1. W **przypadku** opcji Serwer **wybierz pozycję** Utwórz nowy i wypełnij formularz Nowy **serwer** w następujący sposób:
   - **Nazwa serwera:** wprowadź *wartość mysqlserver* i niektóre znaki unikatowości.
   - **Identyfikator logowania administratora serwera:** wprowadź *wartość azureuser*.
   - **Hasło:** wprowadź hasło spełniające wymagania i wprowadź je ponownie w **polu Potwierdź** hasło.
   - **Lokalizacja:** Lista rozwijana i wybierz lokalizację, na przykład **(Wschodnie usa).**

   Wybierz przycisk **OK**.

   ![Nowy serwer](./media/sql-database-create-single-database/new-server.png)

   Rejestruj identyfikator logowania i hasło administratora serwera, aby można było zalogować się do serwera i jego baz danych. Jeśli nie pamiętasz nazwy logowania lub hasła, możesz uzyskać nazwę logowania lub zresetować hasło na stronie **programu SQL Server** po utworzeniu bazy danych. Aby otworzyć stronę **sql server,** wybierz nazwę serwera na stronie Przegląd **bazy** danych.

1. Jeśli **chcesz ponownie** skonfigurować wartości domyślne w obszarze Obliczenia i magazyn, wybierz pozycję Konfiguruj bazę **danych.**

   Na **stronie Konfigurowanie** możesz opcjonalnie:
   - Zmień warstwę **Obliczeniowa z** **Aprowizowana na** **Bez serwera.**
   - Przejrzyj i zmień ustawienia rdzeni **wirtualnych** i **maksymalnego rozmiaru danych.**
   - Wybierz **pozycję Zmień konfigurację,** aby zmienić generację sprzętu.

   Po w związku z wprowadzeniem jakichkolwiek zmian wybierz pozycję **Zastosuj.**

1. Wybierz **pozycję Dalej: Sieć** w dolnej części strony.

   ![Nowa baza danych SQL — karta Podstawowa](./media/sql-database-create-single-database/new-sql-database-basics.png)

1. Na karcie **Sieć** w obszarze **Metoda łączności wybierz** pozycję Publiczny punkt **końcowy**.
1. W **obszarze Reguły zapory** ustaw opcję Dodaj bieżący adres IP klienta **na** **wartość Tak.**
1. Wybierz **pozycję Dalej: Dodatkowe ustawienia** w dolnej części strony.

   ![Karta Sieć](./media/sql-database-create-single-database/networking.png)
  
   Aby uzyskać więcej informacji na temat ustawień zapory, zobacz [Zezwalanie](../database/network-access-controls-overview.md) usługom i zasobom platformy Azure na dostęp do tego serwera i [Dodawanie prywatnego punktu końcowego.](../database/private-endpoint-overview.md)

1. Na karcie **Dodatkowe ustawienia** w sekcji **Źródło** danych w obszarze Użyj **istniejących danych** wybierz pozycję **Przykład**.
1. Opcjonalnie możesz [włączyć Azure Defender sql.](../database/azure-defender-for-sql.md)
1. Opcjonalnie możesz ustawić okno [obsługi,](../database/maintenance-window.md) aby planowana konserwacja odbywała się w najlepszym czasie dla bazy danych.
1. Wybierz **pozycję Przejrzyj i utwórz** w dolnej części strony.

   ![Karta Dodatkowe ustawienia](./media/sql-database-create-single-database/additional-settings.png)

1. Po przejrzeniu ustawień wybierz pozycję **Utwórz**.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Grupę zasobów, serwer i pojedynczą bazę danych platformy Azure można utworzyć przy użyciu interfejsu wiersza polecenia platformy Azure . Jeśli nie chcesz używać interfejsu wiersza polecenia Azure Cloud Shell, [zainstaluj interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) na komputerze.

Aby uruchomić poniższy przykładowy kod w Azure Cloud Shell, wybierz pozycję **Wypróbuj na** pasku tytułu przykładowego kodu. Po Cloud Shell wybierz **pozycję** Kopiuj na pasku tytułu przykładowego kodu i wklej przykładowy kod w oknie Cloud Shell kodu. W kodzie zastąp identyfikatorem subskrypcji platformy Azure, a w przypadku i zastąp publicznym adresem IP komputera, `<Subscription ID>` `$startip` z których `$endip` `0.0.0.0` korzystasz.

Postępuj zgodnie z monitami wyświetlanymi na ekranie, aby zalogować się do platformy Azure i uruchomić kod.

Możesz również użyć Azure Cloud Shell z Azure Portal, wybierając ikonę Cloud Shell na górnym pasku.

   ![Azure Cloud Shell](./media/sql-database-create-single-database/cloudshell.png)

Przy pierwszym użyciu witryny Cloud Shell portalu wybierz pozycję **Bash** w oknie **dialogowym Powitanie.** Kolejne sesje będą używać interfejsu wiersza polecenia platformy Azure w środowisku powłoki Bash. Możesz też wybrać pozycję **Bash** Cloud Shell pasku sterowania.

Poniższy kod interfejsu wiersza polecenia platformy Azure tworzy grupę zasobów, serwer, pojedynczą bazę danych i regułę zapory adresów IP na poziomie serwera w celu uzyskania dostępu do serwera. Pamiętaj, aby zarejestrować wygenerowaną grupę zasobów i nazwy serwerów, aby można było później zarządzać tymi zasobami.

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

Powyższy kod używa tych poleceń interfejsu wiersza polecenia platformy Azure:

| Polecenie | Opis |
|---|---|
| [az account set](/cli/azure/account#az_account_set) | Ustawia subskrypcję na bieżącą aktywną subskrypcję. |
| [az group create](/cli/azure/group#az_group_create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az sql server create](/cli/azure/sql/server#az_sql_server_create) | Tworzy serwer, który hostuje bazy danych i elastyczne pule. |
| [az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule##az_sql_server_firewall_rule_create) | Tworzy regułę zapory na poziomie serwera. |
| [az sql db create](/cli/azure/sql/db#az_sql_db_create) | Tworzy bazę danych. |

Aby uzyskać więcej Azure SQL Database przykładów interfejsu wiersza polecenia platformy Azure, zobacz [Przykłady interfejsu wiersza polecenia platformy Azure](../database/az-cli-script-samples-content-guide.md).

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Grupę zasobów, serwer i pojedynczą bazę danych można utworzyć przy użyciu Windows PowerShell. Jeśli nie chcesz używać modułu Azure Cloud Shell, zainstaluj [moduł Azure PowerShell .](/powershell/azure/install-az-ps)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Aby uruchomić poniższy przykład kodu w Azure Cloud Shell, wybierz pozycję **Wypróbuj na** pasku tytułu kodu. Po Cloud Shell wybierz pozycję Kopiuj  na pasku tytułu przykładowego kodu i wklej przykładowy kod w oknie Cloud Shell kodu. W kodzie zastąp identyfikatorem subskrypcji platformy Azure, a dla i zastąp publicznym adresem `<Subscription ID>` `$startIp` IP `$endIp` `0.0.0.0` komputera, z których korzystasz.

Postępuj zgodnie z instrukcjami wyświetlanymi na ekranie, aby zalogować się do platformy Azure i uruchomić kod.

Możesz również użyć Azure Cloud Shell z Azure Portal, wybierając ikonę Cloud Shell na górnym pasku.

   ![Azure Cloud Shell](./media/sql-database-create-single-database/cloudshell.png)

Przy pierwszym użyciu polecenia Cloud Shell portalu wybierz pozycję **PowerShell** w oknie **dialogowym Zapraszamy.** Kolejne sesje będą używać programu PowerShell lub można je wybrać na Cloud Shell sterowania.

Poniższy kod programu PowerShell tworzy grupę zasobów, serwer, pojedynczą bazę danych i regułę zapory platformy Azure w celu uzyskania dostępu do serwera. Pamiętaj, aby zarejestrować wygenerowaną grupę zasobów i nazwy serwerów, aby można było później zarządzać tymi zasobami.

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

Powyższy kod używa tych poleceń cmdlet programu PowerShell:

| Polecenie | Uwagi |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Tworzy serwer, który hostuje bazy danych i pule elastyczne. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Tworzy regułę zapory na poziomie serwera dla serwera. |
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Tworzy bazę danych. |

Aby uzyskać więcej Azure SQL Database przykładów programu PowerShell, [zobacz Azure PowerShell przykłady.](../database/powershell-script-content-guide.md)

---
