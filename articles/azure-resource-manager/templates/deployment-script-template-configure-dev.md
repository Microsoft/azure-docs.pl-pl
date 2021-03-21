---
title: Konfigurowanie środowiska deweloperskiego na potrzeby skryptów wdrażania w szablonach | Microsoft Docs
description: Konfigurowanie środowiska deweloperskiego na potrzeby skryptów wdrażania w szablonach Azure Resource Manager (szablony ARM).
services: azure-resource-manager
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 12/14/2020
ms.author: jgao
ms.openlocfilehash: b2e1ffb3cbd513766945864e33589c46284bf942
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102200941"
---
# <a name="configure-development-environment-for-deployment-scripts-in-arm-templates"></a>Konfigurowanie środowiska deweloperskiego na potrzeby skryptów wdrażania w szablonach ARM

Dowiedz się, jak utworzyć środowisko programistyczne na potrzeby opracowywania i testowania skryptów wdrażania szablonów ARM przy użyciu obrazu skryptu wdrożenia. Można utworzyć [wystąpienie kontenera platformy Azure](../../container-instances/container-instances-overview.md) lub użyć platformy [Docker](https://docs.docker.com/get-docker/). Obie opcje zostały omówione w tym artykule.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="azure-powershell-container"></a>Kontener Azure PowerShell

Jeśli nie masz skryptu wdrażania Azure PowerShell, możesz utworzyć plik *hello.ps1* za pomocą następującej zawartości:

```powershell
param([string] $name)
$output = 'Hello {0}' -f $name
Write-Output $output
$DeploymentScriptOutputs = @{}
$DeploymentScriptOutputs['text'] = $output
```

### <a name="azure-cli-container"></a>Kontener interfejsu wiersza polecenia platformy Azure

W przypadku obrazu kontenera interfejsu wiersza polecenia platformy Azure można utworzyć plik *Hello.sh* za pomocą następującej zawartości:

```bash
firstname=$1
lastname=$2
output="{\"name\":{\"displayName\":\"$firstname $lastname\",\"firstName\":\"$firstname\",\"lastName\":\"$lastname\"}}"
echo -n "Hello "
echo $output | jq -r '.name.displayName'
```

> [!NOTE]
> Po uruchomieniu skryptu wdrożenia interfejsu wiersza polecenia platformy Azure zmienna środowiskowa o nazwie `AZ_SCRIPTS_OUTPUT_PATH` przechowuje lokalizację pliku wyjściowego skryptu. Zmienna środowiskowa nie jest dostępna w kontenerze środowiska deweloperskiego. Aby uzyskać więcej informacji na temat pracy z wynikami interfejsu wiersza polecenia platformy Azure, zobacz [Praca z wynikami z poziomu skryptu interfejsu wiersza polecenia](deployment-script-template.md#work-with-outputs-from-cli-script).

## <a name="use-azure-powershell-container-instance"></a>Użyj Azure PowerShell wystąpienia kontenera

Aby tworzyć skrypty na komputerze, należy utworzyć konto magazynu i zainstalować konto magazynu do wystąpienia kontenera. Dzięki temu można przekazać skrypt na konto magazynu i uruchomić skrypt w wystąpieniu kontenera.

> [!NOTE]
> Konto magazynu utworzone w celu przetestowania skryptu nie jest tym samym kontem magazynu, które jest używane przez usługę skryptu wdrażania do wykonywania skryptu. Usługa skryptów wdrażania tworzy unikatową nazwę jako udział plików na każdym wykonaniu.

### <a name="create-an-azure-powershell-container-instance"></a>Utwórz wystąpienie kontenera Azure PowerShell

Poniższy szablon Azure Resource Manager (szablon ARM) tworzy wystąpienie kontenera i udział plików, a następnie instaluje udział plików na obrazie kontenera.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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
      "defaultValue": "mcr.microsoft.com/azuredeploymentscripts-powershell:az5.2",
      "metadata": {
        "description": "Specify the container image."
      }
    },
    "mountPath": {
      "type": "string",
      "defaultValue": "/mnt/azscripts/azscriptinput",
      "metadata": {
        "description": "Specify the mount path."
      }
    }
  },
  "variables": {
    "storageAccountName": "[tolower(concat(parameters('projectName'), 'store'))]",
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

Wartość domyślna dla ścieżki instalacji to `/mnt/azscripts/azscriptinput` . Jest to ścieżka do wystąpienia kontenera, w którym jest on instalowany w udziale plików.

Domyślny obraz kontenera określony w szablonie to **MCR.Microsoft.com/azuredeploymentscripts-PowerShell:az5.2**. Zobacz listę wszystkich [obsługiwanych wersji Azure PowerShell](https://mcr.microsoft.com/v2/azuredeploymentscripts-powershell/tags/list).

Szablon zawiesza wystąpienie kontenera po 1 800 sekundach. Masz 30 minut, zanim wystąpienie kontenera przejdzie do stanu przerwania, a sesja zakończy się.

Aby wdrożyć szablon:

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used to generate resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateFile = Read-Host -Prompt "Enter the template file path and file name"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Location $location -name $resourceGroupName
New-AzResourceGroupDeployment -resourceGroupName $resourceGroupName -TemplateFile $templatefile -projectName $projectName
```

### <a name="upload-the-deployment-script"></a>Przekaż skrypt wdrożenia

Przekaż skrypt wdrożenia na konto magazynu. Oto przykład skryptu programu PowerShell:

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name that you used earlier"
$fileName = Read-Host -Prompt "Enter the deployment script file name with the path"

$resourceGroupName = "${projectName}rg"
$storageAccountName = "${projectName}store"
$fileShareName = "${projectName}share"

$context = (Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName).Context
Set-AzStorageFileContent -Context $context -ShareName $fileShareName -Source $fileName -Force
```

Możesz również przekazać plik za pomocą Azure Portal lub interfejsu wiersza polecenia platformy Azure.

### <a name="test-the-deployment-script"></a>Testowanie skryptu wdrożenia

1. W Azure Portal Otwórz grupę zasobów, w której wdrożono wystąpienie kontenera i konto magazynu.
2. Otwórz grupę kontenerów. Domyślną nazwą grupy kontenerów jest nazwa projektu dołączona z elementem *CG*. Wystąpienie kontenera jest w stanie **uruchomienia** .
3. W menu zasób wybierz pozycję **kontenery**. Nazwa wystąpienia kontenera jest nazwą projektu dołączoną z *kontenerem*.

    ![Zrzut ekranu wystąpienia kontenera Connect skryptu wdrożenia w Azure Portal.](./media/deployment-script-template-configure-dev/deployment-script-container-instance-connect.png)

4. Wybierz pozycję **Połącz**, a następnie wybierz pozycję **Połącz**. Jeśli nie możesz połączyć się z wystąpieniem kontenera, uruchom ponownie grupę kontenerów i spróbuj ponownie.
5. W okienku konsoli Uruchom następujące polecenia:

    ```console
    cd /mnt/azscripts/azscriptinput
    ls
    pwsh ./hello.ps1 "John Dole"
    ```

    Dane wyjściowe to **Hello John dole**.

    ![Zrzut ekranu przedstawiający dane wyjściowe testu wystąpienia kontenera programu Scripting skryptu wdrażania w konsoli programu.](./media/deployment-script-template-configure-dev/deployment-script-container-instance-test.png)

## <a name="use-an-azure-cli-container-instance"></a>Korzystanie z wystąpienia kontenera interfejsu wiersza polecenia platformy Azure

Aby tworzyć skrypty na komputerze, należy utworzyć konto magazynu i zainstalować konto magazynu do wystąpienia kontenera. Następnie możesz przekazać skrypt na konto magazynu i uruchomić skrypt w wystąpieniu kontenera.

> [!NOTE]
> Konto magazynu utworzone w celu przetestowania skryptu nie jest tym samym kontem magazynu, które jest używane przez usługę skryptu wdrażania do wykonywania skryptu. Usługa skryptów wdrażania tworzy unikatową nazwę jako udział plików na każdym wykonaniu.

### <a name="create-an-azure-cli-container-instance"></a>Tworzenie wystąpienia kontenera interfejsu wiersza polecenia platformy Azure

Poniższy szablon ARM tworzy wystąpienie kontenera i udział plików, a następnie instaluje udział plików w obrazie kontenera:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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
      "defaultValue": "mcr.microsoft.com/azure-cli:2.9.1",
      "metadata": {
        "description": "Specify the container image."
      }
    },
    "mountPath": {
      "type": "string",
      "defaultValue": "/mnt/azscripts/azscriptinput",
      "metadata": {
        "description": "Specify the mount path."
      }
    }
  },
  "variables": {
    "storageAccountName": "[tolower(concat(parameters('projectName'), 'store'))]",
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
                "/bin/bash",
                "-c",
                "echo hello; sleep 1800"
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

Wartość domyślna dla ścieżki instalacji to `/mnt/azscripts/azscriptinput` . Jest to ścieżka do wystąpienia kontenera, w którym jest on instalowany w udziale plików.

Domyślny obraz kontenera określony w szablonie to **MCR.Microsoft.com/Azure-CLI:2.9.1**. Zapoznaj się z listą [obsługiwanych wersji interfejsu wiersza polecenia platformy Azure](https://mcr.microsoft.com/v2/azure-cli/tags/list).

> [!IMPORTANT]
> Skrypt wdrażania używa dostępnych obrazów interfejsu wiersza polecenia firmy Microsoft Container Registry (MCR). Zaświadczanie obrazu interfejsu wiersza polecenia dla skryptu wdrożenia trwa około miesiąca. Nie używaj wersji interfejsu wiersza polecenia, które zostały wydane w ciągu 30 dni. Aby znaleźć daty wydania dla obrazów, zobacz informacje o [wersji interfejsu wiersza polecenia platformy Azure](/cli/azure/release-notes-azure-cli). W przypadku korzystania z nieobsługiwanej wersji komunikat o błędzie zawiera listę obsługiwanych wersji.

Szablon zawiesza wystąpienie kontenera po 1 800 sekundach. Użytkownik ma 30 minut, zanim wystąpienie kontenera przejdzie do stanu terminalu, a sesja zakończy się.

Aby wdrożyć szablon:

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used to generate resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateFile = Read-Host -Prompt "Enter the template file path and file name"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Location $location -name $resourceGroupName
New-AzResourceGroupDeployment -resourceGroupName $resourceGroupName -TemplateFile $templatefile -projectName $projectName
```

### <a name="upload-the-deployment-script"></a>Przekaż skrypt wdrożenia

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

Możesz również przekazać plik za pomocą Azure Portal lub interfejsu wiersza polecenia platformy Azure.

### <a name="test-the-deployment-script"></a>Testowanie skryptu wdrożenia

1. W Azure Portal Otwórz grupę zasobów, w której wdrożono wystąpienie kontenera i konto magazynu.
1. Otwórz grupę kontenerów. Domyślną nazwą grupy kontenerów jest nazwa projektu dołączona z elementem *CG*. Wystąpienie kontenera jest wyświetlane w stanie **uruchomienia** .
1. W menu zasób wybierz pozycję **kontenery**. Nazwa wystąpienia kontenera jest nazwą projektu dołączoną z *kontenerem*.

    ![wystąpienie kontenera łączenia skryptu wdrażania](./media/deployment-script-template-configure-dev/deployment-script-container-instance-connect.png)

1. Wybierz pozycję **Połącz**, a następnie wybierz pozycję **Połącz**. Jeśli nie możesz połączyć się z wystąpieniem kontenera, uruchom ponownie grupę kontenerów i spróbuj ponownie.
1. W okienku konsoli Uruchom następujące polecenia:

    ```console
    cd /mnt/azscripts/azscriptinput
    ls
    ./hello.sh John Dole
    ```

    Dane wyjściowe to **Hello John dole**.

    ![Test wystąpienia kontenera skryptu wdrożenia](./media/deployment-script-template-configure-dev/deployment-script-container-instance-test-cli.png)

## <a name="use-docker"></a>Korzystanie z platformy Docker

Możesz użyć wstępnie skonfigurowanego obrazu kontenera Docker jako środowiska programistycznego skryptu wdrożenia. Aby zainstalować platformę Docker, zobacz [Pobierz platformę Docker](https://docs.docker.com/get-docker/).
Należy również skonfigurować udostępnianie plików, aby zainstalować katalog, który zawiera skrypty wdrażania do kontenera Docker.

1. Pobierz obraz kontenera skryptu wdrożenia na komputer lokalny:

    ```command
    docker pull mcr.microsoft.com/azuredeploymentscripts-powershell:az4.3
    ```

    W przykładzie używane jest 4.3.0 wersji programu PowerShell.

    Aby ściągnąć obraz interfejsu wiersza polecenia z MCR:

    ```command
    docker pull mcr.microsoft.com/azure-cli:2.0.80
    ```

    W tym przykładzie zastosowano interfejs wiersza polecenia w wersji 2.0.80. Skrypt wdrażania używa domyślnych obrazów kontenerów interfejsu wiersza polecenia znalezionych w [tym miejscu](https://hub.docker.com/_/microsoft-azure-cli).

1. Uruchom lokalnie obraz platformy Docker.

    ```command
    docker run -v <host drive letter>:/<host directory name>:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az4.3
    ```

    Zastąp **&lt; literę>sterownika hosta** i **&lt; nazwę katalogu hosta>** z istniejącym folderem na dysku udostępnionym. Mapuje folder do folderu _/Data_ w kontenerze. Na przykład, aby zmapować _D:\docker_:

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az4.3
    ```

    **-oznacza to** utrzymywanie obrazu kontenera.

    Przykład interfejsu wiersza polecenia:

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azure-cli:2.0.80
    ```

1. Poniższy zrzut ekranu przedstawia sposób uruchomienia skryptu programu PowerShell z uwzględnieniem pliku *helloworld.ps1* na dysku udostępnionym.

    ![Skrypt wdrażania szablonu Menedżer zasobów — polecenie Docker](./media/deployment-script-template/resource-manager-deployment-script-docker-cmd.png)

Po pomyślnym przetestowaniu skryptu można go użyć jako skryptu wdrożenia w szablonach.

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób korzystania ze skryptów wdrażania. Aby zapoznać się z samouczkiem dotyczącym skryptu wdrożenia:

> [!div class="nextstepaction"]
> [Samouczek: używanie skryptów wdrażania w szablonach ARM](./template-tutorial-deployment-script.md)
