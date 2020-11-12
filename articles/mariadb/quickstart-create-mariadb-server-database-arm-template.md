---
title: 'Szybki Start: Tworzenie szablonu usługi Azure DB dla MariaDB — ARM'
description: W tym artykule dotyczącym szybkiego startu dowiesz się, jak utworzyć serwer Azure Database for MariaDB przy użyciu szablonu Azure Resource Manager.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 05/14/2020
ms.openlocfilehash: de6df8349025c3e87e5b005196008053039fa49f
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94537147"
---
# <a name="quickstart-use-an-arm-template-to-create-an-azure-database-for-mariadb-server"></a>Szybki Start: Tworzenie serwera Azure Database for MariaDB przy użyciu szablonu ARM

Azure Database for MariaDB to zarządzana usługa, która umożliwia uruchamianie i skalowanie baz danych MariaDB o wysokiej dostępności w chmurze oraz zarządzanie nimi. W tym przewodniku szybki start użyjesz szablonu Azure Resource Manager (szablon ARM), aby utworzyć serwer Azure Database for MariaDB w Azure Portal, programie PowerShell lub interfejsie wiersza polecenia platformy Azure.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-managed-mariadb-with-vnet%2fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

# <a name="portal"></a>[Portal](#tab/azure-portal)

Konto platformy Azure z aktywną subskrypcją. [Utwórz je bezpłatnie](https://azure.microsoft.com/free/).

# <a name="powershell"></a>[Program PowerShell](#tab/PowerShell)

* Konto platformy Azure z aktywną subskrypcją. [Utwórz je bezpłatnie](https://azure.microsoft.com/free/).
* Jeśli chcesz uruchomić kod lokalnie, [Azure PowerShell](/powershell/azure/).

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/CLI)

* Konto platformy Azure z aktywną subskrypcją. [Utwórz je bezpłatnie](https://azure.microsoft.com/free/).
* Jeśli chcesz uruchomić kod lokalnie, [interfejs wiersza polecenia platformy Azure](/cli/azure/).

---

## <a name="review-the-template"></a>Przegląd szablonu

Serwer usługi Azure Database for MariaDB jest tworzony za pomocą zdefiniowanego zestawu zasobów obliczeniowych i przestrzeni dyskowej. Aby dowiedzieć się więcej, zobacz [Azure Database for MariaDB warstwy cenowe](concepts-pricing-tiers.md). Serwer jest tworzony w ramach [grupy zasobów Azure](../azure-resource-manager/management/overview.md).

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-managed-mariadb-with-vnet/).

:::code language="json" source="~/quickstart-templates/101-managed-mariadb-with-vnet/azuredeploy.json":::

Szablon definiuje pięć zasobów platformy Azure:

* [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
* [**Microsoft. Network/virtualNetworks/podsieci**](/azure/templates/microsoft.network/virtualnetworks/subnets)
* [**Microsoft. DBforMariaDB/serwery**](/azure/templates/microsoft.dbformariadb/servers)
* [**Microsoft. DBforMariaDB/serwery/virtualNetworkRules**](/azure/templates/microsoft.dbformariadb/servers/virtualnetworkrules)
* [**Microsoft. DBforMariaDB/serwery/firewallRules**](/azure/templates/microsoft.dbformariadb/servers/firewallrules)

Więcej przykładów szablonów Azure Database for MariaDB można znaleźć w [szablonach szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Dbformariadb&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Wdrażanie szablonu

# <a name="portal"></a>[Portal](#tab/azure-portal)

Wybierz następujący link, aby wdrożyć szablon Azure Database for MariaDB Server w Azure Portal:

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-managed-mariadb-with-vnet%2fazuredeploy.json)

Na stronie **wdrażanie Azure Database for MariaDB przy użyciu sieci wirtualnej** :

1. W obszarze **Grupa zasobów** wybierz pozycję **Utwórz nową** , wprowadź nazwę nowej grupy zasobów, a następnie wybierz pozycję **OK**.

2. Jeśli utworzono nową grupę zasobów, wybierz **lokalizację** dla grupy zasobów i nowy serwer.

3. Wprowadź **nazwę serwera** , **Identyfikator logowania administratora** i **hasło logowania administratora**.

    ![Wdrażanie Azure Database for MariaDB przy użyciu okna Sieć wirtualna, szablon szybkiego startu platformy Azure, Azure Portal](./media/quickstart-create-mariadb-server-database-arm-template/deploy-azure-database-mariadb-vnet.png)

4. Zmień inne ustawienia domyślne, jeśli chcesz:

    * **Subskrypcja** : subskrypcja platformy Azure, która ma być używana dla serwera.
    * **Pojemność jednostki SKU** : pojemność rdzeń wirtualny, która może być *2* (wartość domyślna), *4* , *8* , *16* , *32* lub *64*.
    * **Nazwa jednostki SKU** : prefiks warstwy SKU, Rodzina SKU i pojemność jednostki SKU, dołączone przez podkreślenia, takie jak *B_Gen5_1* , *GP_Gen5_2* (domyślne) lub *MO_Gen5_32*.
    * **Rozmiar jednostki SKU MB** : rozmiar magazynu (w megabajtach) serwera Azure Database for MariaDB (domyślnie *51200* ).
    * **Warstwa SKU** : warstwa wdrożenia, taka jak *podstawowa* , *GeneralPurpose* (domyślna) lub *MemoryOptimized*.
    * **Rodzina SKU** : *obliczenia* lub *5 rdzeń* (domyślnie), która wskazuje na generowanie sprzętu na potrzeby wdrażania serwera.
    * **Wersja MariaDB** : wersja serwera MariaDB do wdrożenia, na przykład *10,2* lub *10,3* (wartość domyślna).
    * **Liczba dni przechowywania kopii zapasowych** : żądany okres przechowywania kopii zapasowych z Geograficznie nadmiarowy w dniach (domyślnie *7* ).
    * **Geograficznie nadmiarowa kopia zapasowa** : *włączone* lub *wyłączone* (wartość domyślna), w zależności od wymagań geograficznych odzyskiwania po awarii (GEO-dr).
    * **Nazwa Virtual Network** : Nazwa sieci wirtualnej (domyślna *azure_mariadb_vnet* ).
    * **Nazwa podsieci** : Nazwa podsieci (domyślna *azure_mariadb_subnet* ).
    * **Nazwa reguły Virtual Network** : Nazwa reguły sieci wirtualnej zezwalającej na podsieć (domyślnie *AllowSubnet* ).
    * **Prefiks adresu** sieci wirtualnej: prefiks adresu dla usługi Virtual Network (domyślnie *10.0.0.0/16* ).
    * **Prefiks podsieci** : prefiks adresu podsieci (domyślnie *10.0.0.0/16* ).

5. Przeczytaj warunki i postanowienia, a następnie wybierz **Akceptuję warunki i postanowienia podane powyżej**.

6. Wybierz pozycję **Kup**.

# <a name="powershell"></a>[Program PowerShell](#tab/PowerShell)

Użyj poniższego kodu interaktywnego, aby utworzyć nowy serwer Azure Database for MariaDB przy użyciu szablonu. W kodzie zostanie wyświetlony komunikat z prośbą o nową nazwę serwera, nazwę i lokalizację nowej grupy zasobów oraz nazwę i hasło konta administratora.

Aby uruchomić kod w Azure Cloud Shell, wybierz opcję **Wypróbuj** w górnym rogu dowolnego bloku kodu.

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

Użyj poniższego kodu interaktywnego, aby utworzyć nowy serwer Azure Database for MariaDB przy użyciu szablonu. W kodzie zostanie wyświetlony komunikat z prośbą o nową nazwę serwera, nazwę i lokalizację nowej grupy zasobów oraz nazwę i hasło konta administratora.

Aby uruchomić kod w Azure Cloud Shell, wybierz opcję **Wypróbuj** w górnym rogu dowolnego bloku kodu.

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

## <a name="review-deployed-resources"></a>Przejrzyj wdrożone zasoby

# <a name="portal"></a>[Portal](#tab/azure-portal)

Wykonaj następujące kroki, aby zobaczyć przegląd nowego serwera Azure Database for MariaDB:

1. W [Azure Portal](https://portal.azure.com)Wyszukaj i wybierz pozycję **serwery Azure Database for MariaDB**.

2. Na liście baza danych wybierz nowy serwer. Zostanie wyświetlona strona **Przegląd** dla nowego serwera Azure Database for MariaDB.

# <a name="powershell"></a>[Program PowerShell](#tab/PowerShell)

Uruchom Poniższy kod interaktywny, aby wyświetlić szczegółowe informacje o serwerze Azure Database for MariaDB. Musisz wprowadzić nazwę nowego serwera.

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter the name of your Azure Database for MariaDB server"
Get-AzResource -ResourceType "Microsoft.DbForMariaDB/servers" -Name $serverName | ft
Read-Host -Prompt "Press [ENTER] to continue: "
```

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/CLI)

Uruchom Poniższy kod interaktywny, aby wyświetlić szczegółowe informacje o serwerze Azure Database for MariaDB. Musisz wprowadzić nazwę i grupę zasobów nowego serwera.

```azurecli-interactive
read -p "Enter your Azure Database for MariaDB server name: " serverName &&
read -p "Enter the resource group where the Azure Database for MariaDB server exists: " resourcegroupName &&
az resource show --resource-group $resourcegroupName --name $serverName --resource-type "Microsoft.DbForMariaDB/servers" &&
read -p "Press [ENTER] to continue: "
```

---

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy nie jest już potrzebne, Usuń grupę zasobów, która spowoduje usunięcie zasobów z grupy zasobów.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. W [Azure Portal](https://portal.azure.com)Wyszukaj i wybierz pozycję **grupy zasobów**.

2. Na liście Grupa zasobów wybierz nazwę grupy zasobów.

3. Na stronie **Przegląd** w grupie zasobów wybierz pozycję **Usuń grupę zasobów**.

4. W oknie dialogowym potwierdzenia wpisz nazwę grupy zasobów, a następnie wybierz pozycję **Usuń**.

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

Aby zapoznać się z samouczkiem krok po kroku, który przeprowadzi Cię przez proces tworzenia szablonu usługi ARM, zobacz:

> [!div class="nextstepaction"]
> [ Samouczek: Tworzenie i wdrażanie pierwszego szablonu ARM](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
