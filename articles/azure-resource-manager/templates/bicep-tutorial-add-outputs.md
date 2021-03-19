---
title: Samouczek — Dodawanie danych wyjściowych do pliku Azure Resource Manager Bicep
description: Dodaj dane wyjściowe do pliku Bicep, aby uprościć składnię.
author: mumian
ms.date: 03/17/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 4b7d7a1414091c516dba2c474e1681ba357b55a1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104594312"
---
# <a name="tutorial-add-outputs-to-azure-resource-manager-bicep-file"></a>Samouczek: Dodawanie danych wyjściowych do pliku Azure Resource Manager Bicep

W tym samouczku dowiesz się, jak zwrócić wartość z wdrożenia. Dane wyjściowe są używane, gdy potrzebna jest wartość ze wdrożonego zasobu. Ukończenie tego samouczka trwa **7 minut** .

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Zalecamy ukończenie [samouczka dotyczącego zmiennych](bicep-tutorial-add-variables.md), ale nie jest to wymagane.

Musisz mieć Visual Studio Code z rozszerzeniem Bicep i Azure PowerShell lub interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [Narzędzia Bicep](bicep-tutorial-create-first-bicep.md#get-tools).

## <a name="review-bicep-file"></a>Przejrzyj plik Bicep

Na końcu poprzedniego samouczka plik Bicep miał następującą zawartość:

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-variable/azuredeploy.bicep":::

Wdraża konto magazynu, ale nie zwraca żadnych informacji o koncie magazynu. Może być konieczne przechwycenie właściwości z nowego zasobu, aby były one później dostępne do odwołania.

## <a name="add-outputs"></a>Dodawanie danych wyjściowych

Możesz użyć danych wyjściowych, aby zwrócić wartości z wdrożenia. Może być na przykład przydatne, aby uzyskać punkty końcowe dla nowego konta magazynu.

Poniższy przykład pokazuje zmianę pliku Bicep, aby dodać wartość wyjściową. Skopiuj cały plik i Zastąp plik Bicep jego zawartością.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-outputs/azuredeploy.bicep" range="1-33" highlight="33":::

Istnieją pewne ważne elementy, na które należy pamiętać o dodanej wartości wyjściowej.

Typ zwracanej wartości jest ustawiony na `object` , co oznacza, że zwraca obiekt szablonu.

Aby uzyskać `primaryEndpoints` Właściwość z konta magazynu, należy użyć nazwy symbolicznej konta magazynu. Funkcja Autouzupełnianie w Visual Studio Code przedstawia pełną listę właściwości:

   ![Visual Studio Code właściwości obiektu symbolicznej nazwy Bicep](./media/bicep-tutorial-add-outputs/visual-studio-code-bicep-output-properties.png)

## <a name="deploy-bicep-file"></a>Wdróż plik Bicep

Możesz przystąpić do wdrożenia pliku Bicep i przyjrzeć się zwracanej wartości.

Jeśli grupa zasobów nie została utworzona, zobacz [Tworzenie grupy zasobów](bicep-tutorial-create-first-bicep.md#create-resource-group). W przykładzie założono, że ustawiono `bicepFile` zmienną na ścieżkę do pliku Bicep, jak pokazano w [pierwszym samouczku](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addoutputs `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby uruchomić to polecenie wdrażania, musisz mieć [najnowszą wersję](/cli/azure/install-azure-cli) interfejsu wiersza polecenia platformy Azure.

```azurecli
az deployment group create \
  --name addoutputs \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS
```

---

W danych wyjściowych polecenia Deployment zobaczysz obiekt podobny do poniższego przykładu tylko wtedy, gdy dane wyjściowe są w formacie JSON:

```json
{
  "dfs": "https://storeluktbfkpjjrkm.dfs.core.windows.net/",
  "web": "https://storeluktbfkpjjrkm.z19.web.core.windows.net/",
  "blob": "https://storeluktbfkpjjrkm.blob.core.windows.net/",
  "queue": "https://storeluktbfkpjjrkm.queue.core.windows.net/",
  "table": "https://storeluktbfkpjjrkm.table.core.windows.net/",
  "file": "https://storeluktbfkpjjrkm.file.core.windows.net/"
}
```

> [!NOTE]
> Jeśli wdrożenie nie powiodło się, użyj `verbose` przełącznika, aby uzyskać informacje o tworzonych zasobach. Użyj `debug` przełącznika, aby uzyskać więcej informacji na potrzeby debugowania.

## <a name="review-your-work"></a>Przejrzyj swoją służbę

Wykonano wiele ostatnich sześciu samouczków. Poświęć chwilę na zapoznanie się z tym, co zostało zrobione. Utworzono plik Bicep z parametrami, które są łatwe w udostępnianiu. Plik Bicep jest wielokrotnego użytku w różnych środowiskach, ponieważ umożliwia dostosowanie i dynamiczne tworzenie wymaganych wartości. Zwraca również informacje o koncie magazynu, których można użyć w skrypcie.

Teraz przyjrzyjmy się grupie zasobów i historii wdrażania.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Z menu po lewej stronie wybierz pozycję **grupy zasobów**.
1. Wybierz grupę zasobów, do której została wdrożona.
1. W zależności od wykonywanych czynności należy mieć co najmniej jedno konto magazynu w grupie zasobów.
1. W historii należy również znaleźć kilka pomyślnych wdrożeń. Wybierz ten link.

   ![Wybierz wdrożenia](./media/bicep-tutorial-add-outputs/select-deployments.png)

1. Wszystkie wdrożenia są widoczne w historii. Wybierz wdrożenie o nazwie **addoutputs**.

   ![Pokaż historię wdrożenia](./media/bicep-tutorial-add-outputs/show-history.png)

1. Możesz przejrzeć dane wejściowe.

   ![Pokaż dane wejściowe](./media/bicep-tutorial-add-outputs/show-inputs.png)

1. Możesz przejrzeć dane wyjściowe.

   ![Pokaż dane wyjściowe](./media/bicep-tutorial-add-outputs/show-outputs.png)

1. Możesz przejrzeć szablon JSON.

   ![Pokaż szablon](./media/bicep-tutorial-add-outputs/show-template.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli przeniesiesz się do następnego samouczka, nie musisz usuwać grupy zasobów.

Jeśli zatrzymasz się teraz, możesz chcieć wyczyścić wdrożone zasoby, usuwając grupę zasobów.

1. Z Azure Portal z menu po lewej stronie wybierz pozycję **Grupa zasobów** .
2. Wprowadź nazwę grupy zasobów w polu **Filtruj według nazwy**.
3. Wybierz nazwę grupy zasobów.
4. W górnym menu wybierz pozycję **Usuń grupę zasobów** .

## <a name="next-steps"></a>Następne kroki

W tym samouczku dodano wartość zwracaną do pliku Bicep. W następnym samouczku dowiesz się, jak wyeksportować szablon JSON i użyć części tego wyeksportowanego szablonu w pliku Bicep.

> [!div class="nextstepaction"]
> [Korzystanie z wyeksportowanego szablonu](bicep-tutorial-export-template.md)
