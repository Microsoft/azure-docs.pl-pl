---
title: 'Samouczek: Tworzenie szablonu Azure Database for MySQL-Azure Resource Manager'
description: W tym samouczku wyjaśniono, w jaki sposób aprowizować i zautomatyzować wdrożenia serwera usługi Azure Database for MySQL przy użyciu szablonu usługi Azure Resource Manager.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: json
ms.topic: tutorial
ms.date: 12/02/2019
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: fd5fa4e09dd3f73aa7a8f044ded04d504bd2f3dd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97705476"
---
# <a name="tutorial-provision-an-azure-database-for-mysql-server-using-azure-resource-manager-template"></a>Samouczek: Inicjowanie obsługi administracyjnej serwera Azure Database for MySQL przy użyciu szablonu Azure Resource Manager

[Interfejs API REST usługi Azure Database for MySQL](/rest/api/mysql/) umożliwia inżynierom DevOps automatyzowanie i integrowanie aprowizacji, konfiguracji i operacji zarządzanych serwerów i baz danych MySQL na platformie Azure.  Interfejs API umożliwia tworzenie, wyliczanie i usuwanie serwerów i baz danych MySQL oraz zarządzanie nimi w usłudze Azure Database for MySQL.

Azure Resource Manager korzysta z bazowego interfejsu API REST do deklarowania i zaprogramowania zasobów platformy Azure wymaganych do wdrożeń na dużą skalę, co pozwala na dostosowanie infrastruktury jako koncepcji kodu. Szablon parametryzuje nazwę zasobu platformy Azure, jednostkę SKU, sieć, konfigurację zapory i ustawienia, co pozwala na jego wielokrotne używanie po jednorazowym utworzeniu.  Szablony usługi Azure Resource Manager można łatwo utworzyć za pomocą [witryny Azure Portal](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) lub [programu Visual Studio Code](../azure-resource-manager/templates/quickstart-create-templates-use-visual-studio-code.md?tabs=CLI). Umożliwiają one tworzenie pakietów aplikacji i standaryzację oraz automatyzację wdrażania, a operacje te można zintegrować w potoku ciągłej integracji/ciągłego wdrażania metodyki DevOps.  Jeśli na przykład szukasz sposobu szybkiego wdrożenia aplikacji internetowej z wewnętrzną bazą danych Azure Database for MySQL, możesz zrealizować wdrożenie typu end-to-end za pomocą tego [szablonu szybkiego startu](https://azure.microsoft.com/resources/templates/101-webapp-managed-mysql/) z galerii GitHub.

Z tego samouczka dowiesz się, jak wykonywać następujące czynności, używając szablonu usługi Azure Resource Manager i innych narzędzi:

> [!div class="checklist"]
> * Tworzenie serwera usługi Azure Database for MySQL za pomocą punktu końcowego usługi sieci wirtualnej przy użyciu szablonu usługi Azure Resource Manager
> * Tworzenie bazy danych za pomocą [narzędzia wiersza polecenia MySQL](https://dev.mysql.com/doc/refman/5.6/en/mysql.html)
> * Ładowanie przykładowych danych
> * Zapytania o dane
> * Aktualizowanie danych

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/) .

## <a name="create-an-azure-database-for-mysql-server-with-vnet-service-endpoint-using-azure-resource-manager-template"></a>Tworzenie serwera usługi Azure Database for MySQL za pomocą punktu końcowego usługi sieci wirtualnej przy użyciu szablonu usługi Azure Resource Manager

Aby uzyskać odwołanie do szablonu JSON dla serwera usługi Azure Database for MySQL, przejdź do odwołania do szablonu [serwerów Microsoft.DBforMySQL](/azure/templates/microsoft.dbformysql/servers). Poniżej przedstawiono przykładowy szablon JSON, który może służyć do tworzenia nowego serwera z działającą usługą Azure Database for MySQL przy użyciu punktu końcowego usługi sieci wirtualnej.
```json
{
  "apiVersion": "2017-12-01",
  "type": "Microsoft.DBforMySQL/servers",
  "name": "string",
  "location": "string",
  "tags": "string",
  "properties": {
    "version": "string",
    "sslEnforcement": "string",
    "administratorLogin": "string",
    "administratorLoginPassword": "string",
    "storageProfile": {
      "storageMB": "string",
      "backupRetentionDays": "string",
      "geoRedundantBackup": "string"
    }
  },
  "sku": {
    "name": "string",
    "tier": "string",
    "capacity": "string",
    "family": "string"
  },
  "resources": [
    {
      "name": "AllowSubnet",
      "type": "virtualNetworkRules",
      "apiVersion": "2017-12-01",
      "properties": {
        "virtualNetworkSubnetId": "[resourceId('Microsoft.Network/virtualNetworks/subnets', parameters('virtualNetworkName'), parameters('subnetName'))]",
        "ignoreMissingVnetServiceEndpoint": true
      },
      "dependsOn": [
        "[concat('Microsoft.DBforMySQL/servers/', parameters('serverName'))]"
      ]
    }
  ]
}
```
W tym żądaniu należy dostosować następujące wartości:
+   `name` — podaj nazwę serwera MySQL (bez nazwy domeny).
+   `location` — określ prawidłowy region centrum danych platformy Azure dla serwera MySQL. Na przykład westus2.
+   `properties/version` — określ wersję serwera MySQL, która ma zostać wdrożona. Na przykład 5.6 lub 5.7.
+   `properties/administratorLogin` — podaj identyfikator logowania administratora MySQL dla serwera. Nazwą logowania administratora nie może być azure_superuser, admin, administrator, root, guest ani public.
+   `properties/administratorLoginPassword` — podaj hasło dla określonego powyżej administratora MySQL.
+   `properties/sslEnforcement` — określ wartość Enabled/Disabled (Włączone/wyłączone), aby włączyć/wyłączyć opcję sslEnforcement.
+   `storageProfile/storageMB` — określ w megabajtach maksymalny rozmiar aprowizowanego magazynu wymagany przez serwer. Na przykład 5120.
+   `storageProfile/backupRetentionDays` — określ żądany okres przechowywania kopii zapasowych w dniach. Na przykład 7. 
+   `storageProfile/geoRedundantBackup` — określ wartość Enabled/Disabled (Włączone/wyłączone) w zależności od wymagań Geo-DR.
+   `sku/tier` — określ warstwę Basic, GeneralPurpose lub MemoryOptimized dla wdrożenia.
+   `sku/capacity` — określ ilość rdzeni wirtualnych. Możliwe wartości to 2, 4, 8, 16, 32 lub 64.
+   `sku/family` -Określ 5 rdzeń, aby wybrać generowanie sprzętu na potrzeby wdrażania serwera.
+   `sku/name` — określ wartość TierPrefix_family_capacity. Na przykład B_Gen5_1, GP_Gen5_16, MO_Gen5_32. Aby ustalić prawidłowe wartości dla poszczególnych regionów i warstw, zapoznaj się z [dokumentacją warstw cenowych](./concepts-pricing-tiers.md).
+   `resources/properties/virtualNetworkSubnetId` — określ identyfikator platformy Azure dla podsieci w sieci wirtualnej, w której ma zostać umieszczony serwer Azure MySQL. 
+   `tags(optional)` — określ opcjonalne tagi, stanowiące pary klucz-wartość, z których można korzystać do kategoryzowania zasobów w celach rozliczeniowych itd.

Jeśli szukasz sposobu utworzenia szablonu usługi Azure Resource Manager do automatyzacji wdrożeń usługi Azure Database for MySQL dla organizacji, zalecane jest rozpoczęcie od przykładowego [szablonu usługi Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-managed-mysql-with-vnet) w galerii GitHub przewodników Szybki start platformy Azure i budowanie na jego podstawie. 

Jeśli nie masz doświadczenia z szablonami usługi Azure Resource Manager i chcesz je wypróbować, możesz rozpocząć, wykonując następujące czynności:
+   Sklonuj lub pobierz przykładowy [szablon usługi Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-managed-mysql-with-vnet) z galerii przewodników Szybki start platformy Azure.  
+   Zmodyfikuj plik azuredeploy.parameters.json, aby zaktualizować wartości parametrów w oparciu o swoje preferencje, a następnie zapisz plik. 
+   Skorzystaj z interfejsu wiersza polecenia platformy Azure w celu utworzenia serwera MySQL na platformie Azure za pomocą następujących poleceń

Aby uruchamiać bloki kodu z tego samouczka, możesz skorzystać z usługi Azure Cloud Shell w przeglądarce lub zainstalować na swoim komputerze interfejs wiersza polecenia platformy Azure.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

```azurecli-interactive
az login
az group create -n ExampleResourceGroup  -l "West US2"
az deployment group create -g $ ExampleResourceGroup   --template-file $ {templateloc} --parameters $ {parametersloc}
```

## <a name="get-the-connection-information"></a>Pobieranie informacji o połączeniu
Aby nawiązać połączenie z serwerem, musisz podać informacje o hoście i poświadczenia dostępu.
```azurecli-interactive
az mysql server show --resource-group myresourcegroup --name mydemoserver
```

Wynik jest w formacie JSON. Zanotuj wartości **fullyQualifiedDomainName** i **administratorLogin**.
```json
{
  "administratorLogin": "myadmin",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "mydemoserver.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMySQL/servers/mydemoserver",
  "location": "westus2",
  "name": "mydemoserver",
  "resourceGroup": "myresourcegroup",
 "sku": {
    "capacity": 2,
    "family": "Gen5",
    "name": "GP_Gen5_2",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "sslEnforcement": "Enabled",
  "storageProfile": {
    "backupRetentionDays": 7,
    "geoRedundantBackup": "Disabled",
    "storageMb": 5120
  },
  "tags": null,
  "type": "Microsoft.DBforMySQL/servers",
  "userVisibleState": "Ready",
  "version": "5.7"
}
```

## <a name="connect-to-the-server-using-mysql"></a>Nawiązywanie połączenia z serwerem za pomocą narzędzia wiersza polecenia mysql
Nawiąż połączenie z serwerem usługi Azure Database for MySQL za pomocą [narzędzia wiersza polecenia mysql](https://dev.mysql.com/doc/refman/5.6/en/mysql.html). W tym przykładzie polecenie to:
```cmd
mysql -h mydemoserver.database.windows.net -u myadmin@mydemoserver -p
```

## <a name="create-a-blank-database"></a>Tworzenie pustej bazy danych
Po nawiązaniu połączenia z serwerem utwórz pustą bazę danych.
```sql
mysql> CREATE DATABASE mysampledb;
```

W wierszu polecenia uruchom poniższe polecenie, aby przełączyć połączenie na tę nowo utworzoną bazę danych:
```sql
mysql> USE mysampledb;
```

## <a name="create-tables-in-the-database"></a>Tworzenie tabel w bazie danych
Teraz, gdy wiesz, jak nawiązać połączenie z bazą danych Azure Database for MySQL, wykonaj niektóre podstawowe zadania.

Najpierw utwórz tabelę i załaduj do niej dane. Utwórz tabelę z informacjami o spisie.
```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

## <a name="load-data-into-the-tables"></a>Ładowanie danych do tabel
Teraz, gdy masz tabelę, wstaw do niej dane. W otwartym oknie wiersza polecenia uruchom następujące zapytanie, aby wstawić wiersze danych.
```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

W utworzonej wcześniej tabeli masz teraz dwa wiersze przykładowych danych.

## <a name="query-and-update-the-data-in-the-tables"></a>Wykonywanie zapytania względem danych w tabelach i aktualizowanie tych danych
Wykonaj następujące zapytanie, aby pobrać informacje z tabeli bazy danych.
```sql
SELECT * FROM inventory;
```

Możesz także zaktualizować dane w tabelach.
```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

Wiersz jest aktualizowany podczas pobierania danych.
```sql
SELECT * FROM inventory;
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
Write-Host "Press [ENTER] to continue..."
```

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

---

## <a name="next-steps"></a>Następne kroki
W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Tworzenie serwera usługi Azure Database for MySQL za pomocą punktu końcowego usługi sieci wirtualnej przy użyciu szablonu usługi Azure Resource Manager
> * Tworzenie bazy danych za pomocą narzędzia wiersza polecenia mysql
> * Ładowanie przykładowych danych
> * Zapytania o dane
> * Aktualizowanie danych

> [!div class="nextstepaction"]
> [Jak połączyć aplikacje z usługą Azure Database for MySQL](./howto-connection-string.md)