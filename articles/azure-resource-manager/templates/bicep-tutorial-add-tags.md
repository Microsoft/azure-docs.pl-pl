---
title: Samouczek — Dodawanie tagów do zasobów w pliku Azure Resource Manager Bicep
description: Dodawanie tagów do zasobów wdrażanych w plikach Bicep. Tagi pozwalają logicznie organizować zasoby.
author: mumian
ms.date: 03/01/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 05bb68609705ac4c13ad77d2c74db7c105ba23fb
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101748166"
---
# <a name="tutorial-add-tags-in-azure-resource-manager-bicep-files"></a>Samouczek: Dodawanie tagów w Azure Resource Manager Bicep plików

W tym samouczku dowiesz się, jak dodać tagi do zasobów w plikach Bicep. [Tagi](../management/tag-resources.md) ułatwiają logiczne organizowanie zasobów. Wartości tagów są wyświetlane w raportach kosztów. Ten samouczek trwa **8 minut** .

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Zalecamy ukończenie [samouczka dotyczącego szablonów szybkiego startu](bicep-tutorial-quickstart-template.md), ale nie jest to wymagane.

Musisz mieć Visual Studio Code z rozszerzeniem Bicep i Azure PowerShell lub interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [Narzędzia Bicep](bicep-tutorial-create-first-bicep.md#get-tools).

## <a name="review-bicep-file"></a>Przejrzyj plik Bicep

Poprzedni plik Bicep wdrożono konto magazynu, plan App Service i aplikację sieci Web.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/quickstart-template/azuredeploy.bicep":::

Po wdrożeniu tych zasobów może być konieczne śledzenie kosztów i znajdowanie zasobów należących do kategorii. Możesz dodać tagi, aby pomóc w rozwiązaniu tych problemów.

## <a name="add-tags"></a>Dodawanie tagów

Zasoby taguje się, aby dodać wartości, które ułatwiają identyfikację ich zastosowania. Na przykład można dodać tagi, które wyświetlają środowisko i projekt. Można dodać tagi, które identyfikują centrum kosztów lub zespół, który jest właścicielem zasobu. Dodaj dowolne wartości, które są istotne dla Twojej organizacji.

Poniższy przykład wyróżnia zmiany w pliku Bicep. Skopiuj cały plik i Zastąp plik Bicep jego zawartością.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.bicep" range="1-81" highlight="27-30,38,51,71":::

## <a name="deploy-bicep-file"></a>Wdróż plik Bicep

Czas na wdrożenie pliku Bicep i sprawdzenie wyników.

Jeśli grupa zasobów nie została utworzona, zobacz [Tworzenie grupy zasobów](bicep-tutorial-create-first-bicep.md#create-resource-group). W przykładzie założono, że ustawiono `bicepFile` zmienną na ścieżkę do pliku Bicep, jak pokazano w [pierwszym samouczku](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Aby uruchomić to polecenie cmdlet wdrażania, musisz mieć [najnowszą wersję](/powershell/azure/install-az-ps) Azure PowerShell.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addtags `
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
  --name addtags \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS webAppName=demoapp
```

---

> [!NOTE]
> Jeśli wdrożenie nie powiodło się, użyj `verbose` przełącznika, aby uzyskać informacje o tworzonych zasobach. Użyj `debug` przełącznika, aby uzyskać więcej informacji na potrzeby debugowania.

## <a name="verify-deployment"></a>Weryfikowanie wdrożenia

Można zweryfikować wdrożenie, przeeksplorowanie grupy zasobów z Azure Portal.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Z menu po lewej stronie wybierz pozycję **grupy zasobów**.
1. Wybierz grupę zasobów, do której została wdrożona.
1. Wybierz jeden z zasobów, na przykład zasób konta magazynu. Zobaczysz, że ma teraz Tagi.

   ![Pokaż Tagi](./media/bicep-tutorial-add-tags/show-tags.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli przeniesiesz się do następnego samouczka, nie musisz usuwać grupy zasobów.

Jeśli zatrzymasz się teraz, możesz chcieć wyczyścić wdrożone zasoby, usuwając grupę zasobów.

1. Z Azure Portal z menu po lewej stronie wybierz pozycję **Grupa zasobów** .
2. Wprowadź nazwę grupy zasobów w polu **Filtruj według nazwy**.
3. Wybierz nazwę grupy zasobów.
4. W górnym menu wybierz pozycję **Usuń grupę zasobów** .

## <a name="next-steps"></a>Następne kroki

W tym samouczku dodano znaczniki do zasobów. W następnym samouczku dowiesz się, jak używać plików parametrów, aby uprościć przekazywanie wartości do wdrożenia.

> [!div class="nextstepaction"]
> [Użyj pliku parametrów](bicep-tutorial-use-parameter-file.md)
