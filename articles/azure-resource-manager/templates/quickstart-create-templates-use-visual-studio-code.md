---
title: Tworzenie szablonu — kod programu Visual Studio
description: Program Visual Studio Code i rozszerzenie Narzędzia usługi Azure Resource Manager służą do pracy z szablonami usługi Resource Manager.
author: neilpeterson
ms.date: 04/17/2020
ms.topic: quickstart
ms.author: nepeters
ms.openlocfilehash: cd107db5220a96d75092a94736e060ae46672926
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686610"
---
# <a name="quickstart-create-azure-resource-manager-templates-with-visual-studio-code"></a>Szybki start: tworzenie szablonów usługi Azure Resource Manager za pomocą kodu programu Visual Studio

Narzędzia usługi Azure Resource Manager dla kodu programu Visual Studio zapewniają obsługę języka, fragmenty kodu zasobów i autouzupełnienie zasobów. Te narzędzia pomagają tworzyć i weryfikować szablony usługi Azure Resource Manager. W tym przewodniku Szybki start można użyć rozszerzenia do utworzenia szablonu usługi Azure Resource Manager od podstaw. W ten sposób występują funkcje rozszerzeń, takie jak fragmenty kodu szablonu ARM, sprawdzanie poprawności, uzupełnienia i obsługa plików parametrów.

Aby ukończyć ten szybki start, potrzebujesz [programu Visual Studio Code](https://code.visualstudio.com/), z zainstalowanym [rozszerzeniem narzędzi usługi Azure Resource Manager.](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) Należy również zainstalować i uwierzytelnić moduł [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) lub usługi Azure [PowerShell.](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.7.0)

Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="create-an-arm-template"></a>Tworzenie szablonu ARM

Utwórz i otwórz za pomocą programu Visual Studio Code nowy plik o nazwie *azuredeploy.json*. Wprowadź `arm` do edytora kodu, który inicjuje fragmenty usługi Azure Resource Manager dla szkieletu szablonu ARM.

Wybierz, `arm!` aby utworzyć szablon o zakresie wdrożenia grupy zasobów platformy Azure.

![Obraz przedstawiający rusztowania usługi Azure Resource Manager](./media/quickstart-create-templates-use-visual-studio-code/1.png)

Ten fragment kodu tworzy podstawowe bloki konstrukcyjne szablonu ARM.

![Obraz przedstawiający w pełni szkieletowy szablon ARM](./media/quickstart-create-templates-use-visual-studio-code/2.png)

Należy zauważyć, że tryb języka kodu programu Visual Studio został zmieniony z *JSON* na *szablon usługi Azure Resource Manager*. Rozszerzenie zawiera serwer języka specyficzny dla szablonów ARM, który zapewnia sprawdzanie poprawności, uzupełnianie i inne usługi językowe specyficzne dla szablonu ARM.

![Obraz przedstawiający usługę Azure Resource Manager jako tryb języka kodu programu Visual Studio](./media/quickstart-create-templates-use-visual-studio-code/3.png)

## <a name="add-an-azure-resource"></a>Dodawanie zasobu platformy Azure

Rozszerzenie zawiera fragmenty kodu dla wielu zasobów platformy Azure. Te fragmenty kodu można użyć do łatwego dodawania zasobów do wdrożenia szablonu.

Umieść kursor w bloku **zasobów** szablonu, wpisz `storage`i wybierz fragment kodu magazynu *arm.*

![Obraz przedstawiający zasób dodawany do szablonu ARM](./media/quickstart-create-templates-use-visual-studio-code/4.png)

Ta akcja dodaje zasób magazynu do szablonu.

![Obraz przedstawiający zasób usługi Azure Storage w szablonie ARM](./media/quickstart-create-templates-use-visual-studio-code/5.png)

Klucz **tabulacji** może służyć do zakładki za pomocą konfigurowalnych właściwości na koncie magazynu.

![Obraz przedstawiający, jak można używać klawisza tab do poruszania się po konfiguracji zasobów](./media/quickstart-create-templates-use-visual-studio-code/6.png)

## <a name="completion-and-validation"></a>Ukończenie i walidacja

Jednym z najbardziej zaawansowanych możliwości rozszerzenia jest jego integracja ze schematami platformy Azure. Schematy platformy Azure zapewniają rozszerzenie z możliwości sprawdzania poprawności i funkcji uzupełniania z uwzględnieniem zasobów. Zmodyfikujmy konto magazynu, aby wyświetlić sprawdzanie poprawności i zakończenie w działaniu. 

Najpierw zaktualizuj rodzaj konta magazynu `megaStorage`do nieprawidłowej wartości, takiej jak . Należy zauważyć, że ta akcja `megaStorage` generuje ostrzeżenie wskazujące, że nie jest prawidłową wartością.

![Obraz przedstawiający nieprawidłową konfigurację magazynu](./media/quickstart-create-templates-use-visual-studio-code/7.png)

Aby skorzystać z możliwości `megaStorage`uzupełniania, usuń , umieść kursor wewnątrz `ctrl`  +  `space`cudzysłowów podwójnych i naciśnij . Ta akcja przedstawia listę uzupełnień prawidłowych wartości.

![Obraz przedstawiający automatyczne uzupełnianie rozszerzenia](./media/quickstart-create-templates-use-visual-studio-code/8.png)

## <a name="add-template-parameters"></a>Dodawanie parametrów szablonu

Teraz utwórz i użyj parametru, aby określić nazwę konta magazynu.

Umieść kursor w bloku parametrów, dodaj powrót `par`karetki, `arm-param-value` wpisz , a następnie wybierz fragment kodu. Ta akcja dodaje parametr ogólny do szablonu.

![Obraz przedstawiający parametr dodawany do szablonu ARM](./media/quickstart-create-templates-use-visual-studio-code/9.png)

Zaktualizuj nazwę `storageAccountName` parametru `Storage Account Name`i opis do .

![Obraz przedstawiający ukończony parametr w szablonie ARM](./media/quickstart-create-templates-use-visual-studio-code/10.png)

Nazwy kont magazynu platformy Azure mają minimalną długość 3 znaków i maksymalnie 24. Dodaj `minLength` zarówno `maxLength` i do parametru i podać odpowiednie wartości.

![Obraz przedstawiający minLength i maxLength dodawanych do parametru szablonu ARM](./media/quickstart-create-templates-use-visual-studio-code/11.png)

Teraz na zasobie magazynu zaktualizuj właściwość name, aby użyć parametru. Aby to zrobić, usuń bieżącą nazwę. Wprowadź podwójny cudzysłów `[`i otwierający nawias kwadratowy, który tworzy listę funkcji szablonu ARM. Wybierz *parametry* z listy. 

![Obraz przedstawiający automatyczne uzupełnianie podczas korzystania z parametrów w zasobach szablonów ARM](./media/quickstart-create-templates-use-visual-studio-code/12.png)

Wprowadzenie pojedynczego `'` cudzysłowu wewnątrz nawiasów okrągłych tworzy listę wszystkich parametrów zdefiniowanych w szablonie, w tym przypadku *storageAccountName*. Wybierz parametr.

![Obraz przedstawiający ukończony parametr w zasobie szablonu ARM](./media/quickstart-create-templates-use-visual-studio-code/13.png)

## <a name="create-a-parameter-file"></a>Tworzenie pliku parametrów

Plik parametru szablonu ARM umożliwia przechowywanie wartości parametrów specyficznych dla środowiska i przekazywanie tych wartości jako grupy w czasie wdrażania. Na przykład może mieć plik parametru z wartościami specyficznymi dla środowiska testowego i inny dla środowiska produkcyjnego.

Rozszerzenie ułatwia tworzenie pliku parametrów z istniejących szablonów. Aby to zrobić, kliknij prawym przyciskiem myszy `Select/Create Parameter File`szablon w edytorze kodu i wybierz opcję .

![Obraz przedstawiający proces kliknięcia prawym przyciskiem myszy w celu utworzenia pliku parametrów z szablonu ARM](./media/quickstart-create-templates-use-visual-studio-code/14.png)

`New`  >  Wybierz `All Parameters` > Wybierz nazwę i lokalizację pliku parametrów.

![Obraz przedstawiający okno dialogowe nazwa i zapisz plik podczas tworzenia pliku parametrów z szablonu ARM](./media/quickstart-create-templates-use-visual-studio-code/15.png)

Ta akcja tworzy nowy plik parametrów i mapuje go za pomocą szablonu, z którego został utworzony. Możesz zobaczyć i zmodyfikować bieżące mapowanie pliku szablonu/parametru na pasku stanu kodu programu Visual Studio, gdy szablon jest zaznaczony.

![](./media/quickstart-create-templates-use-visual-studio-code/16.png)

Teraz, gdy plik parametrów został zamapowany na szablon, rozszerzenie sprawdza zarówno plik szablonu, jak i parametru razem. Aby wyświetlić tę walidację w praktyce, dodaj wartość dwuznakową do parametru `storageAccountName` w pliku parametru i zapisz plik.

![Obraz przedstawiający unieważniony szablon z powodu problemu z plikiem parametru](./media/quickstart-create-templates-use-visual-studio-code/17.png)

Przejdź z powrotem do szablonu ARM i zwróć uwagę, że został zgłoszony błąd wskazujący, że wartość nie spełnia kryteriów parametru.

![Obraz przedstawiający prawidłowy szablon ARM](./media/quickstart-create-templates-use-visual-studio-code/18.png)

Zaktualizuj wartość do czegoś odpowiedniego, zapisz plik i przejdź z powrotem do szablonu. Należy zauważyć, że błąd na parametr został rozwiązany.

## <a name="deploy-the-template"></a>Wdrożenie szablonu

Otwórz terminal kodu zintegrowanego `ctrl`  +  ```` ` ```` programu Visual Studio przy użyciu kombinacji klawiszy i użyj modułu interfejsu wiersza polecenia platformy Azure lub programu Azure PowerShell do wdrożenia szablonu.

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/CLI)

```azurecli
az group create --name arm-vscode --location eastus

az deployment group create --resource-group arm-vscode --template-file azuredeploy.json --parameters azuredeploy.parameters.json
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell
New-AzResourceGroup -Name arm-vscode -Location eastus

New-AzResourceGroupDeployment -ResourceGroupName arm-vscode -TemplateFile ./azuredeploy.json -TemplateParameterFile ./azuredeploy.parameters.json
```
---

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy zasoby platformy Azure nie są już potrzebne, użyj modułu interfejsu wiersza polecenia platformy Azure lub programu Azure PowerShell, aby usunąć grupę zasobów szybki start.

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/CLI)

```azurecli
az group delete --name arm-vscode
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell
Remove-AzResourceGroup -Name arm-vscode
```
---

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczki dla początkujących](./template-tutorial-create-first-template.md)
