---
title: 'Szybki Start: Tworzenie szablonu usługi Azure DB dla PostgreSQL — ARM'
description: W tym przewodniku szybki start dowiesz się, jak utworzyć Azure Database for PostgreSQL pojedynczy serwer przy użyciu szablonu Azure Resource Manager.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 02/11/2021
ms.openlocfilehash: db65a4bb5137da23f125d67920a2ff9a1a801141
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105606726"
---
# <a name="quickstart-use-an-arm-template-to-create-an-azure-database-for-postgresql---single-server"></a>Szybki Start: używanie szablonu ARM do tworzenia Azure Database for PostgreSQL-pojedynczego serwera

Azure Database for PostgreSQL to usługa zarządzana, która służy do uruchamiania i skalowania w chmurze baz danych PostgreSQL o wysokiej dostępności, a także zarządzania nimi. W tym przewodniku szybki start użyjesz szablonu Azure Resource Manager (szablon ARM) do utworzenia serwera Azure Database for PostgreSQL-pojedynczego w Azure Portal, programie PowerShell lub interfejsie wiersza polecenia platformy Azure.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Wdrażanie na platformie Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-managed-postgresql-with-vnet%2fazuredeploy.json)

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

Tworzysz serwer Azure Database for PostgreSQL ze skonfigurowanym zestawem zasobów obliczeniowych i magazynu. Aby dowiedzieć się więcej, zobacz [warstwy cenowe w Azure Database for PostgreSQL — pojedynczy serwer](concepts-pricing-tiers.md). Serwer jest tworzony w ramach [grupy zasobów Azure](../azure-resource-manager/management/overview.md).

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-managed-postgresql-with-vnet/).

:::code language="json" source="~/quickstart-templates/101-managed-postgresql-with-vnet/azuredeploy.json":::

Szablon definiuje pięć zasobów platformy Azure:

* [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
* [**Microsoft. Network/virtualNetworks/podsieci**](/azure/templates/microsoft.network/virtualnetworks/subnets)
* [**Microsoft. DBforPostgreSQL/serwery**](/azure/templates/microsoft.dbforpostgresql/servers)
* [**Microsoft. DBforPostgreSQL/serwery/virtualNetworkRules**](/azure/templates/microsoft.dbforpostgresql/servers/virtualnetworkrules)
* [**Microsoft. DBforPostgreSQL/serwery/firewallRules**](/azure/templates/microsoft.dbforpostgresql/servers/firewallrules)

Więcej przykładów szablonów Azure Database for PostgreSQL można znaleźć w [szablonach szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Dbforpostgresql&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Wdrożenie szablonu

# <a name="portal"></a>[Portal](#tab/azure-portal)

Wybierz następujący link, aby wdrożyć szablon Azure Database for PostgreSQL Server w Azure Portal:

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Wdróż na platformie Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-managed-postgresql-with-vnet%2fazuredeploy.json)

Na stronie **wdrażanie Azure Database for PostgreSQL przy użyciu sieci wirtualnej** :

1. W obszarze **Grupa zasobów** wybierz pozycję **Utwórz nową**, wprowadź nazwę nowej grupy zasobów, a następnie wybierz pozycję **OK**.

2. Jeśli utworzono nową grupę zasobów, wybierz **lokalizację** dla grupy zasobów i nowy serwer.

3. Wprowadź **nazwę serwera**, **Identyfikator logowania administratora** i **hasło logowania administratora**.

    :::image type="content" source="./media/quickstart-create-postgresql-server-database-using-arm-template/deploy-azure-database-for-postgresql-with-vnet.png" alt-text="Wdrażanie Azure Database for PostgreSQL przy użyciu okna Sieć wirtualna, szablon szybkiego startu platformy Azure, Azure Portal":::

4. Zmień inne ustawienia domyślne, jeśli chcesz:

    * **Subskrypcja**: subskrypcja platformy Azure, która ma być używana dla serwera.
    * **Pojemność jednostki SKU**: pojemność rdzeń wirtualny, która może być *2* (wartość domyślna), *4*, *8*, *16*, *32* lub *64*.
    * **Nazwa jednostki SKU**: prefiks warstwy SKU, Rodzina SKU i pojemność jednostki SKU, dołączone przez podkreślenia, takie jak *B_Gen5_1*, *GP_Gen5_2* (domyślne) lub *MO_Gen5_32*.
    * **Rozmiar jednostki SKU MB**: rozmiar magazynu (w megabajtach) serwera Azure Database for PostgreSQL (domyślnie *51200*).
    * **Warstwa SKU**: warstwa wdrożenia, taka jak *podstawowa*, *GeneralPurpose* (domyślna) lub *MemoryOptimized*.
    * **Rodzina SKU**: *obliczenia* lub *5 rdzeń* (domyślnie), która wskazuje na generowanie sprzętu na potrzeby wdrażania serwera.
    * **Wersja PostgreSQL**: wersja serwera PostgreSQL do wdrożenia, na przykład *9,5*, *9,6*, *10* lub *11* (wartość domyślna).
    * **Liczba dni przechowywania kopii zapasowych**: żądany okres przechowywania kopii zapasowych z Geograficznie nadmiarowy w dniach (domyślnie *7*).
    * **Geograficznie nadmiarowa kopia zapasowa**: *włączone* lub *wyłączone* (wartość domyślna), w zależności od wymagań geograficznych odzyskiwania po awarii (GEO-dr).
    * **Nazwa Virtual Network**: Nazwa sieci wirtualnej (domyślna *azure_postgresql_vnet*).
    * **Nazwa podsieci**: Nazwa podsieci (domyślna *azure_postgresql_subnet*).
    * **Nazwa reguły Virtual Network**: Nazwa reguły sieci wirtualnej zezwalającej na podsieć (domyślnie *AllowSubnet*).
    * **Prefiks adresu** sieci wirtualnej: prefiks adresu dla usługi Virtual Network (domyślnie *10.0.0.0/16*).
    * **Prefiks podsieci**: prefiks adresu podsieci (domyślnie *10.0.0.0/16*).

5. Przeczytaj warunki i postanowienia, a następnie wybierz **Akceptuję warunki i postanowienia podane powyżej**.

6. Wybierz pozycję **Kup**.

# <a name="powershell"></a>[Program PowerShell](#tab/PowerShell)

Użyj poniższego kodu interaktywnego, aby utworzyć nowy serwer Azure Database for PostgreSQL przy użyciu szablonu. W kodzie zostanie wyświetlony komunikat z prośbą o nową nazwę serwera, nazwę i lokalizację nowej grupy zasobów oraz nazwę i hasło konta administratora.

Aby uruchomić kod w Azure Cloud Shell, wybierz opcję **Wypróbuj** w górnym rogu dowolnego bloku kodu.

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter a name for the new Azure Database for PostgreSQL server"
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group where the server will exist"
$location = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"
$adminUser = Read-Host -Prompt "Enter the Azure Database for PostgreSQL server's administrator account name"
$adminPassword = Read-Host -Prompt "Enter the administrator password" -AsSecureString

New-AzResourceGroup -Name $resourceGroupName -Location $location # Use this command when you need to create a new resource group for your deployment
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-managed-postgresql-with-vnet/azuredeploy.json `
    -serverName $serverName `
    -administratorLogin $adminUser `
    -administratorLoginPassword $adminPassword

Read-Host -Prompt "Press [ENTER] to continue: "
```

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/CLI)

Użyj poniższego kodu interaktywnego, aby utworzyć nowy serwer Azure Database for PostgreSQL przy użyciu szablonu. W kodzie zostanie wyświetlony komunikat z prośbą o nową nazwę serwera, nazwę i lokalizację nowej grupy zasobów oraz nazwę i hasło konta administratora.

Aby uruchomić kod w Azure Cloud Shell, wybierz opcję **Wypróbuj** w górnym rogu dowolnego bloku kodu.

```azurecli-interactive
read -p "Enter a name for the new Azure Database for PostgreSQL server:" serverName &&
read -p "Enter a name for the new resource group where the server will exist:" resourceGroupName &&
read -p "Enter an Azure region (for example, centralus) for the resource group:" location &&
read -p "Enter the Azure Database for PostgreSQL server's administrator account name:" adminUser &&
read -p "Enter the administrator password:" adminPassword &&
params='serverName='$serverName' administratorLogin='$adminUser' administratorLoginPassword='$adminPassword &&
az group create --name $resourceGroupName --location $location &&
az deployment group create --resource-group $resourceGroupName --parameters $params --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-managed-postgresql-with-vnet/azuredeploy.json &&
read -p "Press [ENTER] to continue: "
```

---

## <a name="review-deployed-resources"></a>Przejrzyj wdrożone zasoby

# <a name="portal"></a>[Portal](#tab/azure-portal)

Wykonaj następujące kroki, aby zobaczyć przegląd nowego serwera Azure Database for PostgreSQL:

1. W [Azure Portal](https://portal.azure.com)Wyszukaj i wybierz pozycję **serwery Azure Database for PostgreSQL**.

2. Na liście baza danych wybierz nowy serwer. Zostanie wyświetlona strona **Przegląd** dla nowego serwera Azure Database for PostgreSQL.

# <a name="powershell"></a>[Program PowerShell](#tab/PowerShell)

Uruchom Poniższy kod interaktywny, aby wyświetlić szczegółowe informacje o serwerze Azure Database for PostgreSQL. Musisz wprowadzić nazwę nowego serwera.

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter the name of your Azure Database for PostgreSQL server"
Get-AzResource -ResourceType "Microsoft.DbForPostgreSQL/servers" -Name $serverName | ft
Read-Host -Prompt "Press [ENTER] to continue: "
```

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/CLI)

Uruchom Poniższy kod interaktywny, aby wyświetlić szczegółowe informacje o serwerze Azure Database for PostgreSQL. Musisz wprowadzić nazwę i grupę zasobów nowego serwera.

```azurecli-interactive
read -p "Enter your Azure Database for PostgreSQL server name: " serverName &&
read -p "Enter the resource group where the Azure Database for PostgreSQL server exists: " resourcegroupName &&
az resource show --resource-group $resourcegroupName --name $serverName --resource-type "Microsoft.DbForPostgreSQL/servers" &&
read -p "Press [ENTER] to continue: "
```

---

## <a name="exporting-arm-template-from-the-portal"></a>Eksportowanie szablonu ARM z portalu
[Szablon ARM można wyeksportować](../azure-resource-manager/templates/export-template-portal.md) z Azure Portal. Istnieją dwa sposoby eksportowania szablonu:

- [Eksportuj z grupy zasobów lub zasobu](../azure-resource-manager/templates/export-template-portal.md#export-template-from-a-resource). Ta opcja generuje nowy szablon z istniejących zasobów. Wyeksportowany szablon jest "migawką" bieżącego stanu grupy zasobów. Można wyeksportować całą grupę zasobów lub konkretne zasoby w ramach tej grupy zasobów.
- [Eksportuj przed wdrożeniem lub z historii](../azure-resource-manager/templates/export-template-portal.md#export-template-before-deployment). Ta opcja pobiera dokładną kopię szablonu użytego do wdrożenia.

Podczas eksportowania szablonu, w ```"properties":{ }```  sekcji zasobu serwera PostgreSQL należy zauważyć, że ```administratorLogin``` nie zostaną ```administratorLoginPassword``` one uwzględnione ze względów bezpieczeństwa. **Należy** dodać te parametry do szablonu przed wdrożeniem szablonu lub szablon zakończy się niepowodzeniem.

```
"resources": [
    {
      "type": "Microsoft.DBforPostgreSQL/servers",
      "apiVersion": "2017-12-01",
      "name": "[parameters('servers_name')]",
      "location": "southcentralus",
      "sku": {
                "name": "B_Gen5_1",
                "tier": "Basic",
                "family": "Gen5",
                "capacity": 1
            },
      "properties": {
        "administratorLogin": "[parameters('administratorLogin')]",
        "administratorLoginPassword": "[parameters('administratorLoginPassword')]",
```



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

Aby zapoznać się z samouczkiem krok po kroku, który przeprowadzi Cię przez proces tworzenia szablonu, zobacz:

> [!div class="nextstepaction"]
> [ Samouczek: Tworzenie i wdrażanie pierwszego szablonu ARM](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
