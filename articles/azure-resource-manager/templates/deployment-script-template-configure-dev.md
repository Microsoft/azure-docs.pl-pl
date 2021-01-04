---
title: Konfigurowanie środowiska deweloperskiego na potrzeby skryptów wdrażania w szablonach | Microsoft Docs
description: Skonfiguruj środowisko deweloperskie na potrzeby skryptów wdrażania w szablonach Azure Resource Manager.
services: azure-resource-manager
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 12/14/2020
ms.author: jgao
ms.openlocfilehash: d12ec5e3fef45429741fff1665f435d68e6c83f6
ms.sourcegitcommit: f7084d3d80c4bc8e69b9eb05dfd30e8e195994d8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/22/2020
ms.locfileid: "97734185"
---
# <a name="configure-development-environment-for-deployment-scripts-in-templates"></a>Konfigurowanie środowiska deweloperskiego na potrzeby skryptów wdrażania w szablonach

Dowiedz się, jak utworzyć środowisko programistyczne na potrzeby tworzenia i testowania skryptów wdrażania przy użyciu obrazu skryptu wdrażania. Można utworzyć [wystąpienie kontenera platformy Azure](../../container-instances/container-instances-overview.md) lub użyć platformy [Docker](https://docs.docker.com/get-docker/). Oba te zagadnienia zostały omówione w tym artykule.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz skryptu wdrożenia, możesz utworzyć plik **hello.ps1** z następującą zawartością:

```powershell
param([string] $name)
$output = 'Hello {0}' -f $name
Write-Output $output
$DeploymentScriptOutputs = @{}
$DeploymentScriptOutputs['text'] = $output
```

## <a name="use-azure-container-instance"></a>Użyj wystąpienia kontenera platformy Azure

Aby tworzyć skrypty na komputerze, należy utworzyć konto magazynu i zainstalować konto magazynu do wystąpienia kontenera. Dzięki temu można przekazać skrypt na konto magazynu i uruchomić skrypt w wystąpieniu kontenera.

> [!NOTE]
> Konto magazynu utworzone w celu przetestowania skryptu nie jest tym samym kontem magazynu, które jest używane przez usługę skryptu wdrażania do wykonywania skryptu. Usługa skryptów wdrażania tworzy unikatową nazwę jako udział plików na każdym wykonaniu.

### <a name="create-an-azure-container-instance"></a>Tworzenie wystąpienia kontenera platformy Azure

Poniższy szablon ARM tworzy wystąpienie kontenera i udział plików, a następnie instaluje udział plików na obrazie kontenera.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "projectName": {
      "type": "string",
      "metadata": {
        "description": "Specify a project name that is used for generating resource names."
      }
    },
    "containerImage": {
      "type": "string",
      "defaultValue": "mcr.microsoft.com/azuredeploymentscripts-powershell:az4.3",
      "metadata": {
        "description": "Specify the container image."
      }
    },
    "mountPath": {
      "type": "string",
      "defaultValue": "deploymentScript",
      "metadata": {
        "description": "Specify the mount path."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(parameters('projectName'), 'store')]",
    "fileShareName": "[concat(parameters('projectName'), 'share')]",
    "containerGroupName": "[concat(parameters('projectName'), 'cg')]",
    "containerName": "[concat(parameters('projectName'), 'container')]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-06-01",
      "name": "[variables('storageAccountName')]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
      },
      "kind": "StorageV2",
      "properties": {
        "accessTier": "Hot"
      }
    },
    {
      "type": "Microsoft.Storage/storageAccounts/fileServices/shares",
      "apiVersion": "2019-06-01",
      "name": "[concat(variables('storageAccountName'), '/default/', variables('fileShareName'))]",
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
      ]
    },
    {
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2019-12-01",
      "name": "[variables('containerGroupName')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
      ],
      "properties": {
        "containers": [
          {
            "name": "[variables('containerName')]",
            "properties": {
              "image": "[parameters('containerImage')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              },
              "ports": [
                {
                  "protocol": "TCP",
                  "port": 80
                }
              ],
              "volumeMounts": [
                {
                  "name": "filesharevolume",
                  "mountPath": "[parameters('mountPath')]"
                }
              ],
              "command": [
                "/bin/sh",
                "-c",
                "pwsh -c 'Start-Sleep -Seconds 1800'"
              ]
            }
          }
        ],
        "osType": "Linux",
        "volumes": [
          {
            "name": "filesharevolume",
            "azureFile": {
              "readOnly": false,
              "shareName": "[variables('fileShareName')]",
              "storageAccountName": "[variables('storageAccountName')]",
              "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').keys[0].value]"
            }
          }
        ]
      }
    }
  ]
}
```
Wartość domyślna dla ścieżki instalacji to **deploymentScript**.  Jest to ścieżka do wystąpienia kontenera, w którym jest on instalowany w udziale plików.

Domyślny obraz kontenera określony w szablonie to **MCR.Microsoft.com/azuredeploymentscripts-PowerShell:az4.3 "**.   Zapoznaj się z listą [obsługiwanych wersji Azure PowerShell](https://mcr.microsoft.com/v2/azuredeploymentscripts-powershell/tags/list). Zapoznaj się z listą [obsługiwanych wersji interfejsu wiersza polecenia platformy Azure](https://mcr.microsoft.com/v2/azure-cli/tags/list).

  >[!IMPORTANT]
  > Skrypt wdrażania używa dostępnych obrazów interfejsu wiersza polecenia firmy Microsoft Container Registry (MCR). Zaświadczanie obrazu interfejsu wiersza polecenia dla skryptu wdrożenia trwa około miesiąca. Nie używaj wersji interfejsu wiersza polecenia, które zostały wydane w ciągu 30 dni. Aby znaleźć daty wydania dla obrazów, zobacz informacje o [wersji interfejsu wiersza polecenia platformy Azure](/cli/azure/release-notes-azure-cli?view=azure-cli-latest&preserve-view=true). Jeśli używana jest nieobsługiwana wersja, komunikat o błędzie zawiera listę obsługiwanych wersji.

Szablon zawiesza wystąpienie kontenera 1800 sekund. Przed przejściem wystąpienia kontenera do stanu terminalu przez 30 minut następuje zakończenie sesji.

Aby wdrożyć szablon:

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used to generate resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateFile = Read-Host -Prompt "Enter the template file path and file name"
$resourceGroupName = "${projectName}rg"

New-azResourceGroup -Location $location -name $resourceGroupName
New-AzResourceGroupDeployment -resourceGroupName $resourceGroupName -TemplateFile $templatefile -projectName $projectName
```

### <a name="upload-deployment-script"></a>Przekaż skrypt wdrożenia

Przekaż skrypt wdrożenia na konto magazynu. Oto przykład środowiska PowerShell:

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name that you used earlier"
$fileName = Read-Host -Prompt "Enter the deployment script file name with the path"

$resourceGroupName = "${projectName}rg"
$storageAccountName = "${projectName}store"
$fileShareName = "${projectName}share"

$context = (Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName).Context
Set-AzStorageFileContent -Context $context -ShareName $fileShareName -Source $fileName -Force
```

Możesz również przekazać plik przy użyciu Azure Portal i interfejsu wiersza polecenia platformy Azure.

### <a name="test-the-deployment-script"></a>Testowanie skryptu wdrożenia

1. W Azure Portal Otwórz grupę zasobów, w której wdrożono wystąpienie kontenera i konto magazynu.
1. Otwórz grupę kontenerów. Domyślną nazwą grupy kontenerów jest nazwa projektu z dołączonym elementem **CG** . Zobaczysz, że wystąpienie kontenera jest w stanie **uruchomienia** .
1. Z menu po lewej stronie wybierz pozycję **kontenery** . Zobaczysz wystąpienie kontenera.  Nazwa wystąpienia kontenera jest nazwą projektu z dołączonym **kontenerem** .

    ![wystąpienie kontenera łączenia skryptu wdrażania](./media/deployment-script-template-configure-dev/deployment-script-container-instance-connect.png)

1. Wybierz pozycję **Połącz**, a następnie wybierz pozycję **Połącz**. Jeśli nie możesz połączyć się z wystąpieniem kontenera, uruchom ponownie grupę kontenerów i spróbuj ponownie.
1. W okienku konsoli Uruchom następujące polecenia:

    ```console
    cd deploymentScript
    ls
    pwsh ./hello.ps1 "John Dole"
    ```

    Dane wyjściowe to **Hello John dole**.

    ![Test wystąpienia kontenera skryptu wdrożenia](./media/deployment-script-template-configure-dev/deployment-script-container-instance-test.png)

1. Jeśli używasz obrazu polecenia AZ CLI Container Image, uruchom następujący kod:

   ```console
   cd /mnt/azscripts/azscriptinput
   ls
   ./userscript.sh
   ```

## <a name="use-docker"></a>Korzystanie z platformy Docker

Możesz użyć wstępnie skonfigurowanego obrazu kontenera Docker jako środowiska programistycznego skryptu wdrożenia. Aby zainstalować platformę Docker, zobacz [Pobierz platformę Docker](https://docs.docker.com/get-docker/).
Należy również skonfigurować udostępnianie plików, aby zainstalować katalog, który zawiera skrypty wdrażania do kontenera Docker.

1. Pobierz obraz kontenera skryptu wdrożenia na komputer lokalny:

    ```command
    docker pull mcr.microsoft.com/azuredeploymentscripts-powershell:az4.3
    ```

    W przykładzie używane jest 4.3.0 wersji programu PowerShell.

    Aby ściągnąć obraz interfejsu wiersza polecenia z Container Registry firmy Microsoft (MCR):

    ```command
    docker pull mcr.microsoft.com/azure-cli:2.0.80
    ```

    W tym przykładzie zastosowano interfejs wiersza polecenia w wersji 2.0.80. Skrypt wdrażania używa domyślnych obrazów kontenerów interfejsu wiersza polecenia znalezionych w [tym miejscu](https://hub.docker.com/_/microsoft-azure-cli).

1. Uruchom lokalnie obraz platformy Docker.

    ```command
    docker run -v <host drive letter>:/<host directory name>:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az4.3
    ```

    Zastąp **&lt; literę>sterownika hosta** i **&lt; nazwę katalogu hosta>** z istniejącym folderem na dysku udostępnionym.  Mapuje folder do folderu **/Data** w kontenerze. Aby uzyskać przykłady mapowania D:\docker:

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az4.3
    ```

    **-oznacza to** utrzymywanie obrazu kontenera.

    Przykład interfejsu wiersza polecenia:

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azure-cli:2.0.80
    ```

1. Poniższy zrzut ekranu przedstawia sposób uruchomienia skryptu programu PowerShell z uwzględnieniem pliku helloworld.ps1 na dysku udostępnionym.

    ![Skrypt wdrażania szablonu Menedżer zasobów — polecenie Docker](./media/deployment-script-template/resource-manager-deployment-script-docker-cmd.png)

Po pomyślnym przetestowaniu skryptu można go użyć jako skryptu wdrożenia w szablonach.

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób korzystania ze skryptów wdrażania. Aby zapoznać się z samouczkiem dotyczącym skryptu wdrożenia:

> [!div class="nextstepaction"]
> [Samouczek: używanie skryptów wdrażania w szablonach Azure Resource Manager](./template-tutorial-deployment-script.md)
