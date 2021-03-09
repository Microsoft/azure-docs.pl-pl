---
title: Utwórz szablon — Visual Studio Code
description: Użyj Visual Studio Code i rozszerzenia narzędzi do Azure Resource Manager, aby korzystać z szablonów Azure Resource Manager (szablony ARM).
author: neilpeterson
ms.date: 08/09/2020
ms.topic: quickstart
ms.author: nepeters
ms.openlocfilehash: 3e41d4f6f8b8aeb2be376ae1385189fe02e9b897
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102504661"
---
# <a name="quickstart-create-arm-templates-with-visual-studio-code"></a>Szybki Start: Tworzenie szablonów ARM przy użyciu Visual Studio Code

Azure Resource Manager narzędzia dla Visual Studio Code zapewniają obsługę języków, fragmenty zasobów i Autouzupełnianie zasobów. Te narzędzia ułatwiają tworzenie i weryfikowanie szablonów Azure Resource Manager (szablony ARM). W tym przewodniku szybki start użyjesz rozszerzenia, aby utworzyć szablon ARM od podstaw. Podczas wykonywania tych czynności są dostępne funkcje rozszerzeń, takie jak fragmenty szablonów ARM, Walidacja, uzupełnianie i obsługa plików parametrów.

Aby ukończyć ten przewodnik Szybki Start, musisz [Visual Studio Code](https://code.visualstudio.com/)z zainstalowanym [rozszerzeniem narzędzi Azure Resource Manager Tools](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) . Wymagany jest również [interfejs wiersza polecenia platformy Azure](/cli/azure/) lub [moduł Azure PowerShell](/powershell/azure/new-azureps-module-az) zainstalowany i uwierzytelniony.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="create-an-arm-template"></a>Tworzenie szablonu usługi Resource Manager

Utwórz i Otwórz przy użyciu Visual Studio Code nowy plik o nazwie *azuredeploy.jsna*. Przejdź `arm` do edytora kodu, który inicjuje Azure Resource Manager fragmenty kodu umożliwiające tworzenie szkieletu szablonu usługi ARM.

Wybierz `arm!` , aby utworzyć szablon dla wdrożenia grupy zasobów platformy Azure.

![Obraz przedstawiający fragmenty kodu Azure Resource Manager tworzenia szkieletów](./media/quickstart-create-templates-use-visual-studio-code/1.png)

Ten fragment kodu tworzy podstawowe bloki konstrukcyjne dla szablonu ARM.

![Obraz przedstawiający w pełni szkieletowy szablon ARM](./media/quickstart-create-templates-use-visual-studio-code/2.png)

Należy zauważyć, że tryb języka Visual Studio Code został zmieniony z *JSON* na *szablon Azure Resource Manager*. Rozszerzenie zawiera serwer języka specyficzny dla szablonów ARM, który zapewnia sprawdzanie poprawności, uzupełnianie i inne usługi językowe specyficzne dla szablonu ARM.

![Obraz przedstawiający Azure Resource Manager jako tryb języka Visual Studio Code](./media/quickstart-create-templates-use-visual-studio-code/3.png)

## <a name="add-an-azure-resource"></a>Dodawanie zasobu platformy Azure

Rozszerzenie zawiera fragmenty kodu dla wielu zasobów platformy Azure. Te fragmenty kodu mogą służyć do łatwego dodawania zasobów do wdrożenia szablonu.

Umieść kursor w bloku **zasoby** szablonu, wpisz tekst `storage` i wybierz fragment *magazynu ARM* .

![Obraz przedstawiający zasób dodawany do szablonu ARM](./media/quickstart-create-templates-use-visual-studio-code/4.png)

Ta akcja powoduje dodanie zasobu magazynu do szablonu.

![Obraz przedstawiający zasób usługi Azure Storage w szablonie ARM](./media/quickstart-create-templates-use-visual-studio-code/5.png)

Klawisz **Tab** może służyć do przechodzenia przez konfigurowalne właściwości konta magazynu.

![Obraz przedstawiający sposób nawigowania po konfiguracji zasobów przy użyciu klawisza Tab](./media/quickstart-create-templates-use-visual-studio-code/6.png)

## <a name="completion-and-validation"></a>Ukończenie i weryfikacja

Jedną z najbardziej zaawansowanych możliwości rozszerzenia jest integracja ze schematami platformy Azure. Usługa Azure schematy udostępnia rozszerzenie z możliwościami weryfikacji i uzupełniania z uwzględnieniem zasobów. Zmodyfikujmy konto magazynu, aby zobaczyć sprawdzanie poprawności i zakończenie działania.

Najpierw zaktualizuj rodzaj konta magazynu do nieprawidłowej wartości takiej jak `megaStorage` . Należy zauważyć, że ta akcja powoduje ostrzeżenie wskazujące, że `megaStorage` nie jest prawidłową wartością.

![Obraz przedstawiający nieprawidłową konfigurację magazynu](./media/quickstart-create-templates-use-visual-studio-code/7.png)

Aby skorzystać z możliwości ukończenia, Usuń `megaStorage` , umieść kursor wewnątrz podwójnych cudzysłowów, a następnie naciśnij klawisz `ctrl`  +  `space` . Ta akcja przedstawia listę uzupełniania prawidłowych wartości.

![Obraz przedstawiający Autouzupełnianie rozszerzeń](./media/quickstart-create-templates-use-visual-studio-code/8.png)

## <a name="add-template-parameters"></a>Dodaj parametry szablonu

Teraz Utwórz i użyj parametru, aby określić nazwę konta magazynu.

Umieść kursor w bloku parametry, Dodaj znak powrotu karetki, wpisz `"` , a następnie wybierz `new-parameter` fragment kodu. Ta akcja dodaje parametr generyczny do szablonu.

![Obraz przedstawiający parametr dodawany do szablonu ARM](./media/quickstart-create-templates-use-visual-studio-code/9.png)

Zaktualizuj nazwę parametru do `storageAccountName` i opis `Storage Account Name` .

![Obraz przedstawiający ukończony parametr w szablonie ARM](./media/quickstart-create-templates-use-visual-studio-code/10.png)

Nazwy kont usługi Azure Storage mają minimalną długość 3 znaków i maksymalnie 24. Dodaj oba `minLength` `maxLength` Parametry i do parametru i podaj odpowiednie wartości.

![Obraz przedstawiający element minLength i element maxLength dodawane do parametru szablonu ARM](./media/quickstart-create-templates-use-visual-studio-code/11.png)

Teraz w zasobów magazynu zaktualizuj właściwość Name, aby użyć parametru. Aby to zrobić, Usuń bieżącą nazwę. Wprowadź podwójny cudzysłów i otwierający nawias kwadratowy `[` , który tworzy listę funkcji szablonu ARM. Z listy wybierz *Parametry* .

![Obraz przedstawiający Autouzupełnianie przy użyciu parametrów w zasobach szablonów ARM](./media/quickstart-create-templates-use-visual-studio-code/12.png)

Wprowadzenie pojedynczego cudzysłowu `'` w nawiasach okrągłych powoduje utworzenie listy wszystkich parametrów zdefiniowanych w szablonie, w tym przypadku *storageAccountName*. Wybierz parametr.

![Obraz przedstawiający ukończony parametr w zasobie szablonu ARM](./media/quickstart-create-templates-use-visual-studio-code/13.png)

## <a name="create-a-parameter-file"></a>Utwórz plik parametrów

Plik parametrów szablonu ARM umożliwia przechowywanie wartości parametrów specyficznych dla środowiska i przekazywanie tych wartości jako grupy w czasie wdrażania. Na przykład może istnieć plik parametrów z wartościami specyficznymi dla środowiska testowego i innego dla środowiska produkcyjnego.

Rozszerzenie ułatwia tworzenie pliku parametrów z istniejących szablonów. Aby to zrobić, kliknij prawym przyciskiem myszy szablon w edytorze kodu i wybierz polecenie `Select/Create Parameter File` .

![Obraz przedstawiający proces kliknij prawym przyciskiem myszy w celu utworzenia pliku parametrów z szablonu ARM](./media/quickstart-create-templates-use-visual-studio-code/14.png)

Wybierz `New`  >  `All Parameters` > wybierz nazwę i lokalizację pliku parametrów.

![Obraz przedstawiający okno dialogowe Nazwa i Zapisz plik podczas tworzenia pliku parametrów z szablonu ARM](./media/quickstart-create-templates-use-visual-studio-code/15.png)

Ta akcja tworzy nowy plik parametrów i mapuje go z szablonem, z którego został utworzony. Podczas zaznaczania szablonu można zobaczyć i zmodyfikować bieżące mapowanie pliku szablonu/parametru na pasku stanu Visual Studio Code.

![Obraz przedstawiający mapowanie pliku szablonu/parametru na pasku stanu Visual Studio Code.](./media/quickstart-create-templates-use-visual-studio-code/16.png)

Teraz, gdy plik parametrów został zmapowany do szablonu, rozszerzenie weryfikuje jednocześnie szablon i plik parametrów. Aby wyświetlić tę weryfikację w ćwiczeniach, Dodaj wartość dwuznakową do `storageAccountName` parametru w pliku parametrów i Zapisz plik.

![Obraz przedstawiający unieważniony szablon z powodu problemu z plikiem parametru](./media/quickstart-create-templates-use-visual-studio-code/17.png)

Wróć do szablonu ARM i zwróć uwagę, że został zgłoszony błąd wskazujący, że wartość nie spełnia kryteriów parametrów.

![Obraz przedstawiający prawidłowy szablon ARM](./media/quickstart-create-templates-use-visual-studio-code/18.png)

Zaktualizuj wartość do odpowiedniej wartości, Zapisz plik i przejdź z powrotem do szablonu. Zwróć uwagę, że błąd w parametrze został rozwiązany.

## <a name="deploy-the-template"></a>Wdrożenie szablonu

Otwórz Visual Studio Code terminalu zintegrowanego przy użyciu `ctrl`  +  ```` ` ```` kombinacji klawiszy, a następnie wdróż szablon przy użyciu interfejsu wiersza polecenia platformy Azure lub modułu Azure PowerShell.

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/CLI)

```azurecli
az group create --name arm-vscode --location eastus

az deployment group create --resource-group arm-vscode --template-file azuredeploy.json --parameters azuredeploy.parameters.json
```

# <a name="powershell"></a>[Program PowerShell](#tab/PowerShell)

```azurepowershell
New-AzResourceGroup -Name arm-vscode -Location eastus

New-AzResourceGroupDeployment -ResourceGroupName arm-vscode -TemplateFile ./azuredeploy.json -TemplateParameterFile ./azuredeploy.parameters.json
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
> [Samouczki dla początkujących](./template-tutorial-create-first-template.md)
