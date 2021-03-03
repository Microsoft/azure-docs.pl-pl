---
title: Samouczek — korzystanie z szablonów szybkiego startu na potrzeby tworzenia Azure Resource Manager Bicep
description: Dowiedz się, jak korzystać z szablonów szybkiego startu platformy Azure, aby dokończyć programowanie Bicep.
author: mumian
ms.date: 03/01/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 12dcf4bfc00c299d94d45a5bd02bf9eea576cc73
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101748118"
---
# <a name="tutorial-use-azure-quickstart-templates-for-azure-resource-manager-bicep-development"></a>Samouczek: korzystanie z szablonów szybkiego startu platformy Azure na potrzeby opracowywania Azure Resource Manager Bicep

[Szablony szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/) to REPOZYTORIUM szablonów JSON utworzonych przez społeczność. Możesz użyć przykładowych szablonów w Bicep rozwoju. W tym samouczku znajdziesz definicję zasobu witryny sieci Web, dekompilowasz ją do Bicep i dodasz ją do pliku Bicep. Ukończenie może potrwać około **12 minut** .

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Zalecamy ukończenie [samouczka dotyczącego wyeksportowanych szablonów](bicep-tutorial-export-template.md), ale nie jest to wymagane.

Musisz mieć Visual Studio Code z rozszerzeniem Bicep i Azure PowerShell lub interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [Narzędzia Bicep](bicep-tutorial-create-first-bicep.md#get-tools).

## <a name="review-bicep-file"></a>Przejrzyj plik Bicep

Na końcu poprzedniego samouczka plik Bicep miał następującą zawartość:

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/export-template/azuredeploy.bicep":::

Ten plik Bicep działa na potrzeby wdrażania kont magazynu i planów usługi App Service, ale można dodać do niej witrynę sieci Web. Możesz użyć wstępnie skompilowanych szablonów, aby szybko wykryć kod JSON wymagany do wdrożenia zasobu. Zanim Szablony szybkiego startu platformy Azure oferują przykłady Bicep, można użyć narzędzi do konwersji do przekonwertowania szablonów JSON na pliki Bicep.

## <a name="find-template"></a>Znajdź szablon

Obecnie Szablony szybkiego startu platformy Azure udostępniają tylko szablony JSON. Istnieją narzędzia, których można użyć do dekompilowania szablonów JSON do szablonów Bicep.

1. Otwórz [Szablony szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/)
1. W obszarze **wyszukiwania** wprowadź polecenie _Wdróż aplikację sieci Web systemu Linux_.
1. Wybierz kafelek z tytułem **Wdróż podstawową aplikację sieci Web systemu Linux**. Jeśli masz problemy z znalezieniem go, Oto [link bezpośredni](https://azure.microsoft.com/resources/templates/101-webapp-basic-linux/).
1. Wybierz pozycję **Przeglądaj w witrynie GitHub**.
1. Wybierz _azuredeploy.js_. Jest to szablon, którego można użyć.
1. Wybierz pozycję **RAW**, a następnie utwórz kopię adresu URL.
1. Przejdź do **https://bicepdemo.z22.web.core.windows.net/** strony, wybierz opcję **dekompilacja**, a następnie podaj adres URL szablonu RAW.
1. Zapoznaj się z szablonem Bicep. W szczególności poszukaj `Microsoft.Web/sites` zasobu.

    ![Witryna sieci Web szybkiego startu szablonu Menedżer zasobów](./media/bicep-tutorial-quickstart-template/resource-manager-template-quickstart-template-web-site.png)

    Istnieje kilka ważnych funkcji Bicep, które należy wziąć pod uwagę w tym nowym zasobie, jeśli pracujesz nad szablonami JSON.

    W szablonach JSON w usłudze ARM należy ręcznie określić zależności zasobów przy użyciu właściwości _dependsOn_ . Zasób witryny sieci Web zależy od zasobu planu usługi App Service. W przypadku Bicep, jeśli odwołujesz się do dowolnej właściwości zasobu przy użyciu nazwy symbolicznej, właściwość dependsOn jest automatycznie dodawana.

    Można łatwo odwołać się do identyfikatora zasobu z nazwy symbolicznej planu usługi App Service (appServicePlanName.id), który zostanie przetłumaczony do funkcji resourceId (...) w skompilowanym szablonie JSON.

## <a name="revise-existing-bicep-file"></a>Popraw istniejący plik Bicep

Scal dekompilowany szablon szybkiego startu z istniejącym plikiem Bicep. Tak samo jak w poprzednim samouczku, zaktualizuj nazwę symboliczną zasobu i nazwę zasobu w celu dopasowania do konwencji nazewnictwa.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/quickstart-template/azuredeploy.bicep" range="1-73" highlight="20-25,28,61-71":::

Nazwa aplikacji sieci Web musi być unikatowa na platformie Azure. Aby zapobiec duplikowaniu nazw, `webAppPortalName` zmienna została zaktualizowana z `var webAppPortalName_var = '${webAppName}-webapp'` do `var webAppPortalName = '${webAppName}${uniqueString(resourceGroup().id)}'` .

## <a name="deploy-bicep-file"></a>Wdróż plik Bicep

Użyj interfejsu wiersza polecenia platformy Azure lub Azure PowerShell, aby wdrożyć szablon Bicep.

Jeśli grupa zasobów nie została utworzona, zobacz [Tworzenie grupy zasobów](bicep-tutorial-create-first-bicep.md#create-resource-group). W przykładzie założono, że ustawiono zmienną **bicepFile** na ścieżkę do pliku Bicep, jak pokazano w [pierwszym samouczku](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Aby uruchomić to polecenie cmdlet wdrażania, musisz mieć [najnowszą wersję](/powershell/azure/install-az-ps) Azure PowerShell.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addwebapp `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS `
  -webAppName demoapp
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby uruchomić to polecenie wdrażania, musisz mieć [najnowszą wersję](/cli/azure/install-azure-cli) interfejsu wiersza polecenia platformy Azure.

```azurecli
az deployment group create \
  --name addwebapp \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS webAppName=demoapp
```

---

> [!NOTE]
> Jeśli wdrożenie nie powiodło się, użyj `verbose` przełącznika, aby uzyskać informacje o tworzonych zasobach. Użyj `debug` przełącznika, aby uzyskać więcej informacji na potrzeby debugowania.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli przeniesiesz się do następnego samouczka, nie musisz usuwać grupy zasobów.

Jeśli zatrzymasz się teraz, możesz chcieć wyczyścić wdrożone zasoby, usuwając grupę zasobów.

1. Z Azure Portal z menu po lewej stronie wybierz pozycję **Grupa zasobów** .
2. Wprowadź nazwę grupy zasobów w polu **Filtruj według nazwy**.
3. Wybierz nazwę grupy zasobów.
4. W górnym menu wybierz pozycję **Usuń grupę zasobów** .

## <a name="next-steps"></a>Następne kroki

Wiesz już, jak używać szablonu szybkiego startu do programowania Bicep. W następnym samouczku dodasz Tagi do zasobów.

> [!div class="nextstepaction"]
> [Dodawanie tagów](bicep-tutorial-add-tags.md)
