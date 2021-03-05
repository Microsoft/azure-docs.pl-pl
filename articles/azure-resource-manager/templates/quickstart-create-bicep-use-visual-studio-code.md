---
title: Tworzenie plików Bicep — Visual Studio Code
description: Użyj Visual Studio Code i rozszerzenia Bicep, aby Bicep pliki do wdrażania zasobów platformy Azure
author: mumian
ms.date: 03/02/2021
ms.topic: quickstart
ms.author: jgao
ms.openlocfilehash: ab1d7b88321ce5959b99423ae2ca1332369ef691
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102179005"
---
# <a name="quickstart-create-bicep-files-with-visual-studio-code"></a>Szybki Start: Tworzenie plików Bicep za pomocą Visual Studio Code

Rozszerzenie Bicep dla Visual Studio Code zapewnia obsługę języka i Autouzupełnianie zasobów. Te narzędzia ułatwiają tworzenie i weryfikowanie plików [Bicep](./bicep-overview.md) . W tym przewodniku szybki start użyjesz rozszerzenia, aby utworzyć plik Bicep od podstaw. Podczas wykonywania tych czynności są dostępne funkcje rozszerzeń, takie jak Walidacja i zakończenia.

Aby ukończyć ten przewodnik Szybki Start, musisz [Visual Studio Code](https://code.visualstudio.com/)z zainstalowanym [rozszerzeniem Bicep](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-bicep) . Wymagany jest również najnowszy [interfejs wiersza polecenia platformy Azure](/cli/azure/) lub najnowszy [moduł Azure PowerShell](/powershell/azure/new-azureps-module-az?view=azps-3.7.0&preserve-view=true) zainstalowany i uwierzytelniony.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="create-a-bicep-file"></a>Utwórz plik Bicep

Utwórz i Otwórz przy użyciu Visual Studio Code nowy plik o nazwie *azuredeploy. Bicep*.

## <a name="add-an-azure-resource"></a>Dodawanie zasobu platformy Azure

Dodaj podstawowy zasób konta magazynu do pliku Bicep.

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: 'storageaccount1' // must be globally unique
  location: 'eastus'
  tags:{
    diplayName: 'storageaccount1'
  }
  sku: {
      name: 'Standard_LRS'
      tier: 'Standard'
  }
  kind: 'Storage'
}
```

Deklaracja zasobu ma cztery składniki:

- **Resource**: słowo kluczowe.
- **Nazwa symboliczna** (STG): Nazwa symboliczna jest identyfikatorem do odwoływania się do zasobu w pliku Bicep. Nie jest to nazwa zasobu, który będzie wdrażany. Nazwa zasobu jest definiowana przez właściwość **name** .  Zapoznaj się z czwartym składnikiem na tej liście.
- **Typ zasobu** ( Microsoft.Storage/storageAccounts@2019-06-01 ): składa się z dostawcy zasobów (Microsoft. Storage), typu zasobu (StorageAccounts) i apiVersion (2019-06-01). Każdy dostawca zasobów publikuje własne wersje interfejsu API, więc ta wartość jest specyficzna dla tego typu. Więcej typów i apiVersions dla różnych zasobów platformy Azure można znaleźć w temacie [odwołanie do szablonu ARM](/azure/templates/).
- **Właściwości** (wszystkie elementy wewnątrz = {...}): Określ właściwości dla typu zasobu. Każdy zasób ma `name` Właściwość. Większość zasobów ma również `location` Właściwość, która ustawia region, w którym zasób jest wdrażany. Inne właściwości różnią się w zależności od typu zasobu i wersji interfejsu API.

## <a name="completion-and-validation"></a>Ukończenie i weryfikacja

Jedną z najbardziej zaawansowanych możliwości rozszerzenia jest integracja ze schematami platformy Azure. Usługa Azure schematy udostępnia rozszerzenie z możliwościami weryfikacji i uzupełniania z uwzględnieniem zasobów. Zmodyfikujmy konto magazynu, aby zobaczyć sprawdzanie poprawności i zakończenie działania.

Najpierw zaktualizuj rodzaj konta magazynu do nieprawidłowej wartości takiej jak `megaStorage` . Należy zauważyć, że ta akcja powoduje ostrzeżenie wskazujące, że `megaStorage` nie jest prawidłową wartością.

![Obraz przedstawiający nieprawidłową konfigurację magazynu](./media/quickstart-create-bicep-use-visual-studio-code/azure-resource-manager-template-bicep-visual-studio-code-validation.png)

Aby skorzystać z możliwości ukończenia, Usuń `megaStorage` , umieść kursor wewnątrz pojedynczego cudzysłowu, a następnie naciśnij klawisz `ctrl`  +  `space` . Ta akcja przedstawia listę uzupełniania prawidłowych wartości.

![Obraz przedstawiający Autouzupełnianie rozszerzeń](./media/quickstart-create-bicep-use-visual-studio-code/azure-resource-manager-template-bicep-visual-studio-code-auto-completion.png)

## <a name="add-parameters"></a>Dodawanie parametrów

Teraz Utwórz i użyj parametru, aby określić nazwę konta magazynu.

Dodaj następujący kod na początku pliku:

```bicep
@minLength(3)
@maxLength(24)
@description('Specify a storage account name.')
param storageAccountName string
```

Nazwy kont usługi Azure Storage mają minimalną długość 3 znaków i maksymalnie 24. Użyj `minLength` i, `maxLength` Aby podać odpowiednie wartości.

Teraz w zasobów magazynu zaktualizuj właściwość Name, aby użyć parametru. W tym celu Usuń bieżącą nazwę zasobu magazynu, w tym pojedyncze cudzysłowy. Naciśnij klawisz `ctrl`  +  `space` . Wybierz z listy parametr **storageAccountName** . Zwróć uwagę, że parametry można przywoływać bezpośrednio przy użyciu ich nazw w Bicep. Szablony JSON wymagają funkcji Parameter ().

![Obraz przedstawiający Autouzupełnianie przy użyciu parametrów w zasobach Bicep](./media/quickstart-create-bicep-use-visual-studio-code/azure-resource-manager-template-bicep-visual-studio-code-valid-param.png)

## <a name="deploy-the-bicep-file"></a>Wdróż plik Bicep

Otwórz Visual Studio Code terminalu zintegrowanego przy użyciu `ctrl`  +  ```` ` ```` kombinacji klawiszy, Zmień bieżący katalog na miejsce, w którym znajduje się plik Bicep, a następnie użyj modułu interfejsu wiersza polecenia platformy Azure lub Azure PowerShell do wdrożenia pliku Bicep.

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/CLI)

```azurecli
az group create --name arm-vscode --location eastus

az deployment group create --resource-group arm-vscode --template-file azuredeploy.bicep --parameters storageAccountName={your-unique-name}
```

# <a name="powershell"></a>[Program PowerShell](#tab/PowerShell)

```azurepowershell
New-AzResourceGroup -Name arm-vscode -Location eastus

New-AzResourceGroupDeployment -ResourceGroupName arm-vscode -TemplateFile ./azuredeploy.bicep -storageAccountName "{your-unique-name}"
```

---

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy zasoby platformy Azure nie są już potrzebne, użyj interfejsu wiersza polecenia platformy Azure lub modułu Azure PowerShell, aby usunąć grupę zasobów szybkiego startu.

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/CLI)

```azurecli
az group delete --name arm-vscode
```

# <a name="powershell"></a>[Program PowerShell](#tab/PowerShell)

```azurepowershell
Remove-AzResourceGroup -Name arm-vscode
```

---

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Początkujące samouczki Bicep](./bicep-tutorial-create-first-bicep.md)
