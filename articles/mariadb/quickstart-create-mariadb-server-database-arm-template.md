---
title: 'Szybki start: tworzenie aplikacji Azure DB for MariaDB — szablon usługi ARM'
description: W tym artykule Szybki start dowiesz się, jak utworzyć serwer Azure Database for MariaDB przy użyciu szablonu Azure Resource Manager szablonu.
author: savjani
ms.author: pariks
ms.date: 05/14/2020
ms.topic: quickstart
ms.service: mariadb
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: 6bf7f4d30f2ad4f9e0181aed332e3f6cb9265ca0
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531333"
---
# <a name="quickstart-use-an-arm-template-to-create-an-azure-database-for-mariadb-server"></a>Szybki start: tworzenie serwera Azure Database for MariaDB ARM przy użyciu szablonu usługi ARM

Azure Database for MariaDB to usługa zarządzana, która umożliwia uruchamianie i skalowanie w chmurze baz danych MariaDB o wysokiej dostępie oraz zarządzanie nimi. W tym przewodniku Szybki start użyjemy szablonu usługi Azure Resource Manager (arm) do utworzenia serwera Azure Database for MariaDB w interfejsie Azure Portal, programie PowerShell lub interfejsie wiersza polecenia platformy Azure.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-managed-mariadb-with-vnet%2fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

# <a name="portal"></a>[Portal](#tab/azure-portal)

Konto platformy Azure z aktywną subskrypcją. [Utwórz je bezpłatnie.](https://azure.microsoft.com/free/)

# <a name="powershell"></a>[Program PowerShell](#tab/PowerShell)

* Konto platformy Azure z aktywną subskrypcją. [Utwórz je bezpłatnie.](https://azure.microsoft.com/free/)
* Jeśli chcesz uruchomić kod lokalnie, uruchom [Azure PowerShell](/powershell/azure/).

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/CLI)

* Konto platformy Azure z aktywną subskrypcją. [Utwórz je bezpłatnie.](https://azure.microsoft.com/free/)
* Jeśli chcesz uruchomić kod lokalnie, interfejs wiersza [polecenia platformy Azure.](/cli/azure/)

---

## <a name="review-the-template"></a>Przegląd szablonu

Serwer usługi Azure Database for MariaDB jest tworzony za pomocą zdefiniowanego zestawu zasobów obliczeniowych i przestrzeni dyskowej. Aby dowiedzieć się więcej, [zobacz Azure Database for MariaDB cenowych](concepts-pricing-tiers.md). Serwer jest tworzony w ramach [grupy zasobów Azure](../azure-resource-manager/management/overview.md).

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-managed-mariadb-with-vnet/).

:::code language="json" source="~/quickstart-templates/101-managed-mariadb-with-vnet/azuredeploy.json":::

Szablon definiuje pięć zasobów platformy Azure:

* [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
* [**Microsoft.Network/virtualNetworks/subnets**](/azure/templates/microsoft.network/virtualnetworks/subnets)
* [**Microsoft.DBforMariaDB/servers**](/azure/templates/microsoft.dbformariadb/servers)
* [**Microsoft.DBforMariaDB/servers/virtualNetworkRules**](/azure/templates/microsoft.dbformariadb/servers/virtualnetworkrules)
* [**Microsoft.DBforMariaDB/servers/firewallRules**](/azure/templates/microsoft.dbformariadb/servers/firewallrules)

Więcej Azure Database for MariaDB szablonów można znaleźć na stronie [Szablony szybkiego startu platformy Azure.](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Dbformariadb&pageNumber=1&sort=Popular)

## <a name="deploy-the-template"></a>Wdrożenie szablonu

# <a name="portal"></a>[Portal](#tab/azure-portal)

Wybierz następujący link, aby wdrożyć Azure Database for MariaDB szablonu serwera w Azure Portal:

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-managed-mariadb-with-vnet%2fazuredeploy.json)

Na stronie **Deploy Azure Database for MariaDB with VNet (Wdrażanie** aplikacji za pomocą sieci wirtualnej):

1. W **przypadku grupy zasobów** wybierz pozycję Utwórz **nową,** wprowadź nazwę nowej grupy zasobów, a następnie wybierz przycisk **OK.**

2. Jeśli utworzono nową grupę zasobów, wybierz **lokalizację** dla grupy zasobów i nowego serwera.

3. Wprowadź nazwę **serwera,** **nazwę logowania administratora** i hasło logowania **administratora**.

    ![Wdrażanie Azure Database for MariaDB sieci wirtualnej, szablon Szybkiego startu platformy Azure, Azure Portal](./media/quickstart-create-mariadb-server-database-arm-template/deploy-azure-database-mariadb-vnet.png)

4. Jeśli chcesz, zmień inne ustawienia domyślne:

    * **Subskrypcja:** subskrypcja platformy Azure, której chcesz użyć dla serwera.
    * **Pojemność SKU:** pojemność rdzeni wirtualnych, która może mieć wartość *2* (wartość domyślna), *4,* *8,* *16,* *32* lub *64.*
    * **Nazwa SKU:** prefiks warstwy SKU, rodzina SKU i pojemność SKU połączone za pomocą znaków podkreślenia, takich jak *B_Gen5_1,* *GP_Gen5_2* (ustawienie domyślne) lub *MO_Gen5_32*.
    * **Rozmiar SKU MB:** rozmiar magazynu w megabajtach serwera Azure Database for MariaDB (domyślnie *51200).*
    * **Warstwa SKU:** warstwa wdrożenia, taka jak *Podstawowa,* *Ogólnegozapozycja* (ustawienie domyślne) lub *MemoryOptimized*.
    * **Rodzina SKU:** *Gen4* lub *Gen5* (ustawienie domyślne), które wskazuje generację sprzętu do wdrożenia serwera.
    * **Mariadb Version**: wersja serwera MariaDB do wdrożenia, na przykład *10.2* lub *10.3* (wartość domyślna).
    * **Dni przechowywania kopii zapasowych:** żądany okres przechowywania geograficznie nadmiarowej kopii zapasowej w dniach (domyślnie *7).*
    * **Geograficznie nadmiarowa kopia** *zapasowa: włączona* lub *wyłączona* (ustawienie domyślne), w zależności od wymagań dotyczących geograficznego odzyskiwania po awarii (Geo-DR).
    * **Virtual Network nazwa:** nazwa sieci wirtualnej (domyślnie jest *azure_mariadb_vnet*).
    * **Nazwa podsieci:** nazwa podsieci (domyślnie jest *azure_mariadb_subnet*).
    * **Virtual Network nazwa reguły:** nazwa reguły sieci wirtualnej zezwalające na podsieć (domyślnie *AllowSubnet).*
    * **Prefiks adresu sieci** wirtualnej: prefiks adresu sieci wirtualnej (domyślnie *10.0.0.0/16).*
    * **Prefiks podsieci:** prefiks adresu podsieci (domyślnie *10.0.0.0/16).*

5. Przeczytaj warunki i postanowienia, a następnie wybierz pozycję Wyrażam zgodę na **powyższe warunki i postanowienia.**

6. Wybierz pozycję **Kup**.

# <a name="powershell"></a>[Program PowerShell](#tab/PowerShell)

Użyj następującego kodu interaktywnego, aby utworzyć nowy Azure Database for MariaDB serwera przy użyciu szablonu. Kod wyświetla monit o nazwę nowego serwera, nazwę i lokalizację nowej grupy zasobów oraz nazwę konta administratora i hasło.

Aby uruchomić kod w Azure Cloud Shell, wybierz pozycję **Wypróbuj** w górnym rogu dowolnego bloku kodu.

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter a name for the new Azure Database for MariaDB server"
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group where the server will exist"
$location = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"
$adminUser = Read-Host -Prompt "Enter the Azure Database for MariaDB server's administrator account name"
$adminPassword = Read-Host -Prompt "Enter the administrator password" -AsSecureString

New-AzResourceGroup -Name $resourceGroupName -Location $location # Use this command when you need to create a new resource group for your deployment
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-managed-mariadb-with-vnet/azuredeploy.json `
    -serverName $serverName `
    -administratorLogin $adminUser `
    -administratorLoginPassword $adminPassword

Read-Host -Prompt "Press [ENTER] to continue: "
```

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/CLI)

Użyj następującego kodu interaktywnego, aby utworzyć nowy Azure Database for MariaDB serwera przy użyciu szablonu. Kod wyświetla monit o nazwę nowego serwera, nazwę i lokalizację nowej grupy zasobów oraz nazwę konta administratora i hasło.

Aby uruchomić kod w Azure Cloud Shell, wybierz pozycję **Wypróbuj** w górnym rogu dowolnego bloku kodu.

```azurecli-interactive
read -p "Enter a name for the new Azure Database for MariaDB server:" serverName &&
read -p "Enter a name for the new resource group where the server will exist:" resourceGroupName &&
read -p "Enter an Azure region (for example, centralus) for the resource group:" location &&
read -p "Enter the Azure Database for MariaDB server's administrator account name:" adminUser &&
read -p "Enter the administrator password:" adminPassword &&
params='serverName='$serverName' administratorLogin='$adminUser' administratorLoginPassword='$adminPassword &&
az group create --name $resourceGroupName --location $location &&
az deployment group create --resource-group $resourceGroupName --parameters $params --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-managed-mariadb-with-vnet/azuredeploy.json &&
read -p "Press [ENTER] to continue: "
```

---

## <a name="review-deployed-resources"></a>Przeglądanie wdrożonych zasobów

# <a name="portal"></a>[Portal](#tab/azure-portal)

Wykonaj następujące kroki, aby wyświetlić omówienie nowego Azure Database for MariaDB serwera:

1. W [Azure Portal](https://portal.azure.com)wyszukaj i wybierz pozycję **Azure Database for MariaDB serwery .**

2. Na liście baz danych wybierz nowy serwer. Zostanie **wyświetlona** strona Przegląd Azure Database for MariaDB serwera.

# <a name="powershell"></a>[Program PowerShell](#tab/PowerShell)

Uruchom następujący kod interaktywny, aby wyświetlić szczegółowe informacje o Azure Database for MariaDB serwera. Musisz wprowadzić nazwę nowego serwera.

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter the name of your Azure Database for MariaDB server"
Get-AzResource -ResourceType "Microsoft.DbForMariaDB/servers" -Name $serverName | ft
Read-Host -Prompt "Press [ENTER] to continue: "
```

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/CLI)

Uruchom następujący kod interaktywny, aby wyświetlić szczegółowe informacje o Azure Database for MariaDB serwera. Musisz wprowadzić nazwę i grupę zasobów nowego serwera.

```azurecli-interactive
read -p "Enter your Azure Database for MariaDB server name: " serverName &&
read -p "Enter the resource group where the Azure Database for MariaDB server exists: " resourcegroupName &&
az resource show --resource-group $resourcegroupName --name $serverName --resource-type "Microsoft.DbForMariaDB/servers" &&
read -p "Press [ENTER] to continue: "
```

---

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów nie będzie już potrzebna, usuń ją, co spowoduje usunięcie zasobów w grupie zasobów.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. W polu [Azure Portal](https://portal.azure.com)wyszukaj i wybierz **pozycję Grupy zasobów.**

2. Na liście grupy zasobów wybierz nazwę grupy zasobów.

3. Na stronie **Przegląd** grupy zasobów wybierz pozycję **Usuń grupę zasobów.**

4. W oknie dialogowym potwierdzenia wpisz nazwę grupy zasobów, a następnie wybierz pozycję **Usuń.**

# <a name="powershell"></a>[Program PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Read-Host -Prompt "Press [ENTER] to continue: "
```

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/CLI)

```azurecli-interactive
read -p "Enter the Resource Group name: " resourceGroupName &&
az group delete --name $resourceGroupName &&
read -p "Press [ENTER] to continue: "
```

---

## <a name="next-steps"></a>Następne kroki

Aby uzyskać samouczek krok po kroku, który przeprowadzi Cię przez proces tworzenia szablonu usługi ARM, zobacz:

> [!div class="nextstepaction"]
> [ Samouczek: tworzenie i wdrażanie pierwszego szablonu usługi ARM](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
