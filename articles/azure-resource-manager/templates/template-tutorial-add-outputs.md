---
title: Samouczek — Dodawanie danych wyjściowych do szablonu
description: Dodaj dane wyjściowe do szablonu Azure Resource Manager (szablon ARM), aby uprościć składnię.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 7e022822f1306a5351cef43498f00a0ac0f2ef63
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101707289"
---
# <a name="tutorial-add-outputs-to-your-arm-template"></a>Samouczek: Dodawanie danych wyjściowych do szablonu ARM

W tym samouczku dowiesz się, jak zwrócić wartość z szablonu Azure Resource Manager (szablon ARM). Dane wyjściowe są używane, gdy potrzebna jest wartość ze wdrożonego zasobu. Ukończenie tego samouczka trwa **7 minut** .

## <a name="prerequisites"></a>Wymagania wstępne

Zalecamy ukończenie [samouczka dotyczącego zmiennych](template-tutorial-add-variables.md), ale nie jest to wymagane.

Musisz mieć Visual Studio Code z rozszerzeniem narzędzi Menedżer zasobów i Azure PowerShell lub interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [Narzędzia szablonu](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Przejrzyj szablon

Na końcu poprzedniego samouczka szablon zawierał następujący kod JSON:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-variable/azuredeploy.json":::

Wdraża konto magazynu, ale nie zwraca żadnych informacji o koncie magazynu. Może być konieczne przechwycenie właściwości z nowego zasobu, aby były one później dostępne do odwołania.

## <a name="add-outputs"></a>Dodawanie danych wyjściowych

Możesz użyć danych wyjściowych, aby zwrócić wartości z szablonu. Może być na przykład przydatne, aby uzyskać punkty końcowe dla nowego konta magazynu.

Poniższy przykład podświetla zmianę szablonu, aby dodać wartość wyjściową. Skopiuj cały plik i Zastąp jego zawartość.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-outputs/azuredeploy.json" range="1-53" highlight="47-52":::

Istnieją pewne ważne elementy, na które należy pamiętać o dodanej wartości wyjściowej.

Typ zwracanej wartości jest ustawiony na `object` , co oznacza, że zwraca obiekt JSON.

Używa funkcji [Reference](template-functions-resource.md#reference) do uzyskiwania stanu środowiska uruchomieniowego konta magazynu. Aby uzyskać stan środowiska uruchomieniowego zasobu, należy przekazać nazwę lub identyfikator zasobu. W takim przypadku należy użyć tej samej zmiennej, która została użyta do utworzenia nazwy konta magazynu.

Na koniec zwraca `primaryEndpoints` Właściwość z konta magazynu.

## <a name="deploy-template"></a>Wdrażanie szablonu

Wszystko jest gotowe do wdrożenia szablonu i przeszukania zwracanej wartości.

Jeśli grupa zasobów nie została utworzona, zobacz [Tworzenie grupy zasobów](template-tutorial-create-first-template.md#create-resource-group). W przykładzie przyjęto założenie, że ustawiono `templateFile` zmienną na ścieżkę do pliku szablonu, jak pokazano w [pierwszym samouczku](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addoutputs `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby uruchomić to polecenie wdrażania, musisz mieć [najnowszą wersję](/cli/azure/install-azure-cli) interfejsu wiersza polecenia platformy Azure.

```azurecli
az deployment group create \
  --name addoutputs \
  --resource-group myResourceGroup \
  --template-file $templateFile \
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

Wykonano wiele ostatnich sześciu samouczków. Poświęć chwilę na zapoznanie się z tym, co zostało zrobione. Utworzono szablon z parametrami, które są łatwe do dostarczenia. Szablon jest wielokrotnego użytku w różnych środowiskach, ponieważ umożliwia dostosowanie i dynamiczne tworzenie wymaganych wartości. Zwraca również informacje o koncie magazynu, których można użyć w skrypcie.

Teraz przyjrzyjmy się grupie zasobów i historii wdrażania.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Z menu po lewej stronie wybierz pozycję **grupy zasobów**.
1. Wybierz grupę zasobów, do której została wdrożona.
1. W zależności od wykonywanych czynności należy mieć co najmniej jedno konto magazynu w grupie zasobów.
1. W historii należy również znaleźć kilka pomyślnych wdrożeń. Wybierz ten link.

   ![Wybierz wdrożenia](./media/template-tutorial-add-outputs/select-deployments.png)

1. Wszystkie wdrożenia są widoczne w historii. Wybierz wdrożenie o nazwie **addoutputs**.

   ![Pokaż historię wdrożenia](./media/template-tutorial-add-outputs/show-history.png)

1. Możesz przejrzeć dane wejściowe.

   ![Pokaż dane wejściowe](./media/template-tutorial-add-outputs/show-inputs.png)

1. Możesz przejrzeć dane wyjściowe.

   ![Pokaż dane wyjściowe](./media/template-tutorial-add-outputs/show-outputs.png)

1. Możesz przejrzeć szablon.

   ![Pokaż szablon](./media/template-tutorial-add-outputs/show-template.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli przeniesiesz się do następnego samouczka, nie musisz usuwać grupy zasobów.

Jeśli zatrzymasz się teraz, możesz chcieć wyczyścić wdrożone zasoby, usuwając grupę zasobów.

1. Z Azure Portal z menu po lewej stronie wybierz pozycję **Grupa zasobów** .
2. Wprowadź nazwę grupy zasobów w polu **Filtruj według nazwy**.
3. Wybierz nazwę grupy zasobów.
4. W górnym menu wybierz pozycję **Usuń grupę zasobów** .

## <a name="next-steps"></a>Następne kroki

W tym samouczku dodano wartość zwracaną do szablonu. W następnym samouczku dowiesz się, jak wyeksportować szablon i użyć części tego wyeksportowanego szablonu w szablonie.

> [!div class="nextstepaction"]
> [Korzystanie z wyeksportowanego szablonu](template-tutorial-export-template.md)
