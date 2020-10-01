---
title: Samouczek — wdrażanie lokalnego szablonu Azure Resource Manager
description: Dowiedz się, jak wdrożyć szablon Azure Resource Manager z komputera lokalnego
ms.date: 05/20/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: fe13376ced428713703f2bd5cf33941129dec1d9
ms.sourcegitcommit: 06ba80dae4f4be9fdf86eb02b7bc71927d5671d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2020
ms.locfileid: "91611626"
---
# <a name="tutorial-deploy-a-local-azure-resource-manager-template"></a>Samouczek: Wdrażanie szablonu Azure Resource Manager lokalnego

Dowiedz się, jak wdrożyć szablon Azure Resource Manager na komputerze lokalnym. Ukończenie może zająć około **8 minut** .

Ten samouczek jest pierwszą częścią serii. W miarę postępów przez serię modularyzacji szablon, tworząc połączony szablon, przechowujesz połączony szablon na koncie magazynu i zabezpieczasz połączony szablon przy użyciu tokenu sygnatury dostępu współdzielonego i dowiesz się, jak utworzyć potok DevOp w celu wdrożenia szablonów. Ta seria koncentruje się na wdrożeniu szablonu.  Jeśli chcesz poznać programowanie szablonów, zobacz [samouczki początkującego](./template-tutorial-create-first-template.md)oprogramowania.

## <a name="get-tools"></a>Pobierz narzędzia

Zacznijmy od zagwarantowania, że masz narzędzia potrzebne do wdrożenia szablonów.

### <a name="command-line-deployment"></a>Wdrożenie wiersza polecenia

Do wdrożenia szablonu wymagane są Azure PowerShell lub interfejs wiersza polecenia platformy Azure. Instrukcje instalacji znajdują się w temacie:

- [Instalowanie programu Azure PowerShell](/powershell/azure/install-az-ps)
- [Instalowanie interfejsu wiersza polecenia platformy Azure w systemie Windows](/cli/azure/install-azure-cli-windows)
- [Instalowanie interfejsu wiersza polecenia platformy Azure w systemie Linux](/cli/azure/install-azure-cli-linux)

Po zainstalowaniu Azure PowerShell lub interfejsu wiersza polecenia platformy Azure upewnij się, że logujesz się po raz pierwszy. Aby uzyskać pomoc, zobacz artykuł [Logowanie — PowerShell](/powershell/azure/install-az-ps#sign-in) lub [Logowanie się do interfejsu wiersza polecenia platformy Azure](/cli/azure/get-started-with-azure-cli#sign-in).

### <a name="editor-optional"></a>Edytor (opcjonalnie)

Szablony są plikami JSON. Aby przeglądać/edytować szablony, potrzebny jest dobry Edytor JSON. Zalecamy Visual Studio Code z rozszerzeniem narzędzi Menedżer zasobów Tools. Jeśli zachodzi potrzeba zainstalowania tych narzędzi, zobacz [Szybki Start: tworzenie Azure Resource Manager szablonów z Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md).

## <a name="review-template"></a>Przejrzyj szablon

Szablon wdraża konto magazynu, plan usługi App Service i aplikację internetową. Jeśli interesuje Cię Tworzenie szablonu, możesz przejść przez samouczek dotyczący [szablonów szybkiego startu](template-tutorial-quickstart-template.md). Nie jest to jednak wymagane do ukończenia tego samouczka.

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/local-template/azuredeploy.json":::

> [!IMPORTANT]
> Nazwy kont magazynu muszą mieć długość od 3 do 24 znaków i używać tylko cyfr i małych liter. Nazwa musi być unikatowa. W szablonie nazwa konta magazynu jest nazwą projektu z dołączonym elementem "Store", a nazwa projektu musi zawierać od 3 do 11 znaków. Nazwa projektu musi być zgodna z wymaganiami dotyczącymi nazwy konta magazynu i ma mniej niż 11 znaków.

Zapisz kopię szablonu na komputerze lokalnym przy użyciu rozszerzenia JSON, na przykład azuredeploy.json. Ten szablon zostanie wdrożony w dalszej części tego samouczka.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Aby rozpocząć pracę z interfejsem wiersza polecenia Azure PowerShell/Azure w celu wdrożenia szablonu, zaloguj się przy użyciu poświadczeń platformy Azure.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli
az login
```

---

Jeśli masz wiele subskrypcji platformy Azure, wybierz subskrypcję, której chcesz użyć:

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell
Set-AzContext [SubscriptionID/SubscriptionName]
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli
az account set --subscription [SubscriptionID/SubscriptionName]
```

---

## <a name="create-resource-group"></a>Tworzenie grupy zasobów

Podczas wdrażania szablonu należy określić grupę zasobów, która będzie zawierać zasoby. Przed uruchomieniem polecenia wdrożenia Utwórz grupę zasobów przy użyciu interfejsu wiersza polecenia platformy Azure lub Azure PowerShell. Wybierz karty w poniższej sekcji kodu, aby wybrać między Azure PowerShell i interfejsem wiersza polecenia platformy Azure. Przykłady interfejsu wiersza polecenia w tym artykule są przeznaczone dla powłoki bash.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used to generate resource and resource group names"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroup `
  -Name $resourceGroupName `
  -Location "Central US"
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli
echo "Enter a project name that is used to generate resource and resource group names:"
read projectName
resourceGroupName="${projectName}rg"

az group create \
  --name $resourceGroupName \
  --location "Central US"
```

---

## <a name="deploy-template"></a>Wdrażanie szablonu

Użyj jednej lub obu opcji wdrażania, aby wdrożyć szablon.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name"
$templateFile = Read-Host -Prompt "Enter the template file path and file name"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroupDeployment `
  -Name DeployLocalTemplate `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile $templateFile `
  -projectName $projectName `
  -verbose
```

Aby dowiedzieć się więcej na temat wdrażania szablonu przy użyciu Azure PowerShell, zobacz [wdrażanie zasobów za pomocą szablonów Menedżer zasobów i Azure PowerShell](./deploy-powershell.md).

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli
echo "Enter the same project name:"
read projectName
echo "Enter the template file path and file name:"
read templateFile
resourceGroupName="${projectName}rg"

az deployment group create \
  --name DeployLocalTemplate \
  --resource-group $resourceGroupName \
  --template-file $templateFile \
  --parameters projectName=$projectName \
  --verbose
```

Aby dowiedzieć się więcej o wdrażaniu szablonu przy użyciu interfejsu wiersza polecenia platformy Azure, zobacz [wdrażanie zasobów za pomocą szablonów Menedżer zasobów i interfejsu wiersza polecenia platformy Azure](./deploy-cli.md).

---

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Wyczyść wdrożone zasoby, usuwając grupę zasobów.

1. Z Azure Portal z menu po lewej stronie wybierz pozycję **Grupa zasobów** .
2. Wprowadź nazwę grupy zasobów w polu **Filtruj według nazwy**.
3. Wybierz nazwę grupy zasobów.
4. W górnym menu wybierz pozycję **Usuń grupę zasobów** .

## <a name="next-steps"></a>Następne kroki

Wiesz już, jak wdrożyć szablon lokalny. W następnym samouczku można rozdzielić szablon na szablon główny i połączony szablon oraz dowiedzieć się, jak przechowywać i zabezpieczać połączony szablon.

> [!div class="nextstepaction"]
> [Wdróż połączony szablon](./deployment-tutorial-linked-template.md)
