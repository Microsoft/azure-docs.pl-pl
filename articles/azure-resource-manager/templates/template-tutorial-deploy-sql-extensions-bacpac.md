---
title: Importuj pliki BACPAC SQL z szablonami
description: Dowiedz się, jak za pomocą rozszerzeń Azure SQL Database importować pliki SQL BACPAC z szablonami Azure Resource Manager (szablony ARM).
author: mumian
ms.date: 12/09/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 2d50903f464c03157ee393787af6ddfdad975aed
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97588050"
---
# <a name="tutorial-import-sql-bacpac-files-with-arm-templates"></a>Samouczek: Importowanie plików BACPAC SQL za pomocą szablonów ARM

Dowiedz się, jak za pomocą rozszerzeń Azure SQL Database zaimportować plik [BACPAC](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) z szablonami Azure Resource Manager (szablony ARM). Artefakty wdrożenia to dowolne pliki, oprócz plików szablonów głównych, które są potrzebne do ukończenia wdrożenia. Plik BACPAC jest tu artefaktem.

W tym samouczku utworzysz szablon służący do wdrożenia [logicznego serwera SQL](../../azure-sql/database/logical-servers.md) i pojedynczej bazy danych i zaimportowania pliku BACPAC. Aby uzyskać informacje o sposobie wdrażania rozszerzeń maszyn wirtualnych platformy Azure przy użyciu szablonów ARM, zobacz [Samouczek: Wdrażanie rozszerzeń maszyn wirtualnych przy użyciu szablonów usługi ARM](./template-tutorial-deploy-vm-extensions.md).

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
>
> * Przygotuj plik BACPAC.
> * Otwórz szablon szybkiego startu.
> * Edytuj szablon.
> * Wdrażanie szablonu.
> * Sprawdź wdrożenie.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć pracę z tym artykułem, potrzebne są następujące zasoby:

* Program Visual Studio Code z rozszerzeniem Resource Manager Tools. Zobacz [Szybki Start: Tworzenie szablonów ARM przy użyciu Visual Studio Code](./quickstart-create-templates-use-visual-studio-code.md).
* Aby zwiększyć bezpieczeństwo, należy użyć wygenerowanego hasła dla konta administratora serwera. Oto przykład, którego można użyć do wygenerowania hasła:

    ```console
    openssl rand -base64 32
    ```

    Usługa Azure Key Vault została zaprojektowana w celu ochrony kluczy kryptograficznych i innych wpisów tajnych. Aby uzyskać więcej informacji, zobacz [Samouczek: integrowanie Azure Key Vault w wdrożeniu szablonu ARM](./template-tutorial-use-key-vault.md). Zalecamy również aktualizowanie hasła co trzy miesiące.

## <a name="prepare-a-bacpac-file"></a>Przygotowywanie pliku BACPAC

Plik BACPAC jest udostępniany w serwisie [GitHub](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac). Aby utworzyć własne, zobacz [Eksportowanie bazy danych z Azure SQL Database do pliku BACPAC](../../azure-sql/database/database-export.md). W przypadku wybrania publikowania pliku do własnej lokalizacji musisz zaktualizować szablon w dalszej części tego samouczka.

Plik BACPAC musi być przechowywany na koncie usługi Azure Storage, aby można go było zaimportować przy użyciu szablonu ARM. Poniższy skrypt programu PowerShell przygotowuje plik BACPAC z następującymi krokami:

* Pobieranie pliku BACPAC.
* Tworzenie konta usługi Azure Storage.
* Utwórz kontener obiektów BLOB konta magazynu.
* Przekazywanie pliku BACPAC do kontenera.
* Wyświetl klucz konta magazynu i adres URL obiektu BLOB.

1. Wybierz pozycję **Wypróbuj** , aby otworzyć powłokę. Następnie wklej następujący skrypt programu PowerShell do okna powłoki.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate Azure resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"

    $resourceGroupName = "${projectName}rg"
    $storageAccountName = "${projectName}store"
    $containerName = "bacpacfiles"
    $bacpacFileName = "SQLDatabaseExtension.bacpac"
    $bacpacUrl = "https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac"

    # Download the bacpac file
    Invoke-WebRequest -Uri $bacpacUrl -OutFile "$HOME/$bacpacFileName"

    # Create a resource group
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a storage account
    $storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroupName `
                                           -Name $storageAccountName `
                                           -SkuName Standard_LRS `
                                           -Location $location
    $storageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName `
                                                  -Name $storageAccountName).Value[0]

    # Create a container
    New-AzStorageContainer -Name $containerName -Context $storageAccount.Context

    # Upload the BACPAC file to the container
    Set-AzStorageBlobContent -File $HOME/$bacpacFileName `
                             -Container $containerName `
                             -Blob $bacpacFileName `
                             -Context $storageAccount.Context

    Write-Host "The storage account key is $storageAccountKey"
    Write-Host "The BACPAC file URL is https://$storageAccountName.blob.core.windows.net/$containerName/$bacpacFileName"
    Write-Host "Press [ENTER] to continue ..."
    ```

1. Zapisz klucz konta magazynu i adres URL pliku BACPAC. Te wartości są wymagane podczas wdrażania szablonu.

## <a name="open-a-quickstart-template"></a>Otwieranie szablonu szybkiego startu

Szablon używany w tym samouczku jest przechowywany w serwisie [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json).

1. W obszarze Visual Studio Code wybierz pozycję **plik**  >  **Otwórz plik**.
1. W polu **File name (Nazwa pliku)** wklej następujący adres URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json
    ```

1. Wybierz pozycję **Open (Otwórz)**, aby otworzyć plik.

    Istnieją dwa zasoby zdefiniowane w szablonie:

   * `Microsoft.Sql/servers`. Zobacz [dokumentację szablonu](/azure/templates/microsoft.sql/servers).
   * `Microsoft.SQL.servers/databases`. Zobacz [dokumentację szablonu](/azure/templates/microsoft.sql/servers/databases).

        Warto uzyskać podstawową wiedzę na temat szablonu przed rozpoczęciem jego dostosowywania.
1. Wybierz pozycję **plik**  >  **Zapisz jako,** aby zapisać kopię pliku na komputerze lokalnym o nazwie *azuredeploy.jsna*.

## <a name="edit-the-template"></a>Edytowanie szablonu

1. Dodaj dwa więcej parametrów na końcu `parameters` sekcji, aby ustawić klucz konta magazynu i adres URL BACPAC.

    ```json
        "storageAccountKey": {
          "type":"string",
          "metadata":{
            "description": "Specifies the key of the storage account where the BACPAC file is stored."
          }
        },
        "bacpacUrl": {
          "type":"string",
          "metadata":{
            "description": "Specifies the URL of the BACPAC file."
          }
        }
    ```

    Dodaj przecinek po `adminPassword` zamykającym nawiasie klamrowym właściwości ( `}` ). Aby sformatować plik JSON z Visual Studio Code, wybierz polecenie Shift + Alt + F.

    Aby uzyskać te dwie wartości, zobacz [Przygotowywanie pliku BACPAC](#prepare-a-bacpac-file).

1. Dodaj do szablonu dwa dodatkowe zasoby.

    * Aby zezwolić na rozszerzenie SQL Database na importowanie plików BACPAC, należy zezwolić na ruch z usług platformy Azure. Dodaj następującą definicję reguły zapory w obszarze definicja serwera:

        ```json
        "resources": [
          {
            "type": "firewallrules",
            "apiVersion": "2015-05-01-preview",
            "name": "AllowAllAzureIps",
            "location": "[parameters('location')]",
            "dependsOn": [
              "[parameters('databaseServerName')]"
            ],
            "properties": {
              "startIpAddress": "0.0.0.0",
              "endIpAddress": "0.0.0.0"
            }
          }
        ]
        ```

        Szablon wygląda następująco:

        ![Szablon z definicją reguły zapory](./media/template-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-firewall.png)

    * Dodaj zasób rozszerzenia usługi SQL Database do definicji bazy danych za pomocą następującego kodu JSON:

        ```json
        "resources": [
          {
            "type": "extensions",
            "apiVersion": "2014-04-01",
            "name": "Import",
            "dependsOn": [
              "[resourceId('Microsoft.Sql/servers/databases', parameters('databaseServerName'), parameters('databaseName'))]"
            ],
            "properties": {
              "storageKeyType": "StorageAccessKey",
              "storageKey": "[parameters('storageAccountKey')]",
              "storageUri": "[parameters('bacpacUrl')]",
              "administratorLogin": "[parameters('adminUser')]",
              "administratorLoginPassword": "[parameters('adminPassword')]",
              "operationMode": "Import"
            }
          }
        ]
        ```

        Szablon wygląda następująco:

        ![Szablon z rozszerzeniem SQL Database](./media/template-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac.png)

        Aby poznać definicję zasobu, zapoznaj się z [dokumentacją rozszerzenia usługi SQL Database](/azure/templates/microsoft.sql/servers/databases/extensions). Poniżej przedstawiono niektóre ważne elementy:

        * `dependsOn`: Należy utworzyć zasób rozszerzenia po utworzeniu bazy danych.
        * `storageKeyType`: Określ typ klucza magazynu, który ma być używany. Wartością może być `StorageAccessKey` lub `SharedAccessKey`. Użyj `StorageAccessKey` w tym samouczku.
        * `storageKey`: Określ klucz dla konta magazynu, w którym przechowywany jest plik BACPAC. Jeśli typ klucza magazynu to `SharedAccessKey` , musi być poprzedzony znakiem "?".
        * `storageUri`: Określ adres URL pliku BACPAC przechowywanego na koncie magazynu.
        * `administratorLoginPassword`: Hasło administratora SQL. Użyj wygenerowanego hasła. Zobacz [Wymagania wstępne](#prerequisites).

Ukończony szablon wygląda następująco:

[!code-json[](~/resourcemanager-templates/tutorial-sql-extension/azuredeploy2.json?range=1-106&highlight=38-49,62-76,86-103)]

## <a name="deploy-the-template"></a>Wdrożenie szablonu

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Zapoznaj się sekcją [Wdrażanie szablonu](./template-tutorial-create-templates-with-dependent-resources.md#deploy-the-template), gdzie znajduje się procedura wdrażania. Alternatywnie możesz też użyć następującego skryptu wdrażania programu PowerShell:

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that is used earlier"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the SQL admin username"
$adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
$storageAccountKey = Read-Host -Prompt "Enter the storage account key"
$bacpacUrl = Read-Host -Prompt "Enter the URL of the BACPAC file"
$resourceGroupName = "${projectName}rg"

#New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -adminUser $adminUsername `
    -adminPassword $adminPassword `
    -TemplateFile "$HOME/azuredeploy.json" `
    -storageAccountKey $storageAccountKey `
    -bacpacUrl $bacpacUrl

Write-Host "Press [ENTER] to continue ..."
```

Rozważ użycie tej samej nazwy projektu, która była używana podczas przygotowywania pliku BACPAC, tak aby wszystkie zasoby były przechowywane w ramach tej samej grupy zasobów. W ten sposób łatwiej jest zarządzać zadaniami zasobów, takimi jak czyszczenie zasobów. Jeśli używasz tej samej nazwy projektu, możesz usunąć `New-AzResourceGroup` polecenie ze skryptu lub odpowiedzieć tak (y) lub nie (n), gdy zostanie wyświetlony monit o zaktualizowanie istniejącej grupy zasobów.

Użyj wygenerowanego hasła. Zobacz [Wymagania wstępne](#prerequisites).

## <a name="verify-the-deployment"></a>Weryfikowanie wdrożenia

Aby uzyskać dostęp do serwera z komputera klienckiego, należy dodać dodatkową regułę zapory. Aby uzyskać więcej informacji, zobacz [Tworzenie reguł zapory IP i zarządzanie nimi](../../azure-sql/database/firewall-configure.md#create-and-manage-ip-firewall-rules).

W Azure Portal wybierz bazę danych z nowo wdrożonej grupy zasobów. Wybierz pozycję **Edytor zapytań (wersja zapoznawcza)**, a następnie wprowadź poświadczenia administratora. Zobaczysz dwie tabele zaimportowane do bazy danych.

![Edytor zapytań (wersja zapoznawcza)](./media/template-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-query-editor.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy zasoby platformy Azure nie będą już potrzebne, wyczyść wdrożone zasoby, usuwając grupę zasobów.

1. W Azure Portal wybierz pozycję **Grupa zasobów** z menu po lewej stronie.
1. Wprowadź nazwę grupy zasobów w polu **Filtruj według nazwy**.
1. Wybierz nazwę grupy zasobów. Zobaczysz łącznie sześć zasobów w grupie zasobów.
1. W górnym menu wybierz pozycję **Usuń grupę zasobów** .

## <a name="next-steps"></a>Następne kroki

W tym samouczku wdrożono serwer i bazę danych oraz zaimportowano plik BACPAC. Aby dowiedzieć się, jak rozwiązywać problemy z wdrażaniem szablonów, zobacz:

> [!div class="nextstepaction"]
> [Rozwiązywanie problemów z wdrażaniem szablonów usługi ARM](./template-tutorial-troubleshoot.md)
