---
title: Samouczek — Eksportowanie szablonu z Azure Portal
description: Dowiedz się, jak za pomocą wyeksportowanego szablonu ukończyć tworzenie szablonu.
author: mumian
ms.date: 09/09/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: ba1797da5a78eeebd25f5df1b6e37eb92470f584
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97106924"
---
# <a name="tutorial-use-exported-template-from-the-azure-portal"></a>Samouczek: korzystanie z wyeksportowanego szablonu z Azure Portal

W tej serii samouczków utworzono szablon służący do wdrażania konta usługi Azure Storage. W następnych dwóch samouczkach dodasz *plan App Service* i *witrynę sieci Web*. Zamiast tworzyć szablony od podstaw, dowiesz się, jak eksportować szablony z Azure Portal oraz jak używać przykładowych szablonów z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/). Te szablony można dostosować do własnych potrzeb. Ten samouczek koncentruje się na eksportowaniu szablonów i dostosowywaniu wyników dla szablonu. Ukończenie może potrwać około **14 minut** .

## <a name="prerequisites"></a>Wymagania wstępne

Zalecamy ukończenie [samouczka dotyczącego danych wyjściowych](template-tutorial-add-outputs.md), ale nie jest to wymagane.

Musisz mieć Visual Studio Code z rozszerzeniem narzędzi Menedżer zasobów i Azure PowerShell lub interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [Narzędzia szablonu](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Przejrzyj szablon

Na końcu poprzedniego samouczka szablon zawierał następujący kod JSON:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-outputs/azuredeploy.json":::

Ten szablon działa dobrze w przypadku wdrażania kont magazynu, ale możesz chcieć dodać do niego więcej zasobów. Możesz wyeksportować szablon z istniejącego zasobu, aby szybko uzyskać kod JSON dla tego zasobu.

## <a name="create-app-service-plan"></a>Tworzenie planu usługi App Service

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wybierz pozycję **Utwórz zasób**.
1. W obszarze **Wyszukaj w portalu Marketplace** wprowadź **App Service plan**, a następnie wybierz pozycję **App Service plan**.  Nie wybieraj **planu App Service (klasyczny)**
1. Wybierz przycisk **Utwórz**.
1. Wprowadź:

    - **Subskrypcja**: wybierz subskrypcję platformy Azure.
    - **Grupa zasobów**: wybierz pozycję **Utwórz nową** , a następnie określ nazwę. Podaj inną nazwę grupy zasobów niż ta, która była używana w tej serii samouczków.
    - **Nazwa**: Wprowadź nazwę planu usługi App Service.
    - **System operacyjny**: wybierz pozycję **Linux**.
    - **Region**: Wybierz lokalizację platformy Azure. Na przykład **Środkowe stany USA**.
    - **Warstwa cenowa**: aby zaoszczędzić koszty, Zmień jednostkę SKU na **podstawową B1** (w obszarze Tworzenie/testowanie).

    ![Portal szablonu Menedżer zasobów szablonu eksportu](./media/template-tutorial-export-template/resource-manager-template-export.png)
1. Wybierz pozycję **Przejrzyj i Utwórz**.
1. Wybierz przycisk **Utwórz**. Utworzenie zasobu trwa kilka chwil.

## <a name="export-template"></a>Eksportowanie szablonu

1. Wybierz pozycję **Przejdź do zasobu**.

    ![Przechodzenie do zasobu](./media/template-tutorial-export-template/resource-manager-template-export-go-to-resource.png)

1. Wybierz pozycję **Eksportuj szablon**.

    ![Szablon eksportu Menedżer zasobów szablonu](./media/template-tutorial-export-template/resource-manager-template-export-template.png)

   Funkcja eksportowania szablonu Pobiera bieżący stan zasobu i generuje szablon w celu jego wdrożenia. Eksportowanie szablonu może być przydatnym sposobem na szybkie pobranie kodu JSON potrzebnego do wdrożenia zasobu.

1. Przyjrzyj się `Microsoft.Web/serverfarms` definicji i definicji parametru w wyeksportowanym szablonie. Nie musisz kopiować tych sekcji. Tego wyeksportowanego szablonu można użyć jako przykładu sposobu dodawania tego zasobu do szablonu.

    ![Szablon wyeksportowanego szablonu Menedżer zasobów szablonu eksportu](./media/template-tutorial-export-template/resource-manager-template-exported-template.png)

> [!IMPORTANT]
> Zwykle wyeksportowany szablon jest bardziej szczegółowy niż podczas tworzenia szablonu. Na przykład obiekt SKU w wyeksportowanym szablonie ma pięć właściwości. Ten szablon działa, ale można tylko użyć `name` właściwości. Możesz rozpocząć od wyeksportowanego szablonu, a następnie zmodyfikować go w taki sposób, aby odpowiadał Twoim wymaganiom.

## <a name="revise-existing-template"></a>Popraw istniejący szablon

Wyeksportowany szablon zapewnia większość potrzebnych danych JSON, ale należy go dostosować do szablonu. Należy zwrócić szczególną uwagę na różnice między parametrami i zmiennymi między szablonem i wyeksportowanym szablonem. Oczywiście proces eksportowania nie wie o parametry i zmienne, które zostały już zdefiniowane w szablonie.

Poniższy przykład wyróżnia Dodatki do szablonu. Zawiera eksportowany kod i pewne zmiany. Najpierw zmienia nazwę parametru zgodnie z konwencją nazewnictwa. Następnie używa parametru Location dla lokalizacji planu usługi App Service. Po trzecie usuwa niektóre właściwości, w których wartość domyślna jest dobra.

Skopiuj cały plik i Zastąp jego zawartość.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/export-template/azuredeploy.json" range="1-77" highlight="28-31,50-69":::

## <a name="deploy-template"></a>Wdrażanie szablonu

Użyj interfejsu wiersza polecenia platformy Azure lub Azure PowerShell, aby wdrożyć szablon.

Jeśli grupa zasobów nie została utworzona, zobacz [Tworzenie grupy zasobów](template-tutorial-create-first-template.md#create-resource-group). W przykładzie przyjęto założenie, że ustawiono `templateFile` zmienną na ścieżkę do pliku szablonu, jak pokazano w [pierwszym samouczku](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addappserviceplan `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby uruchomić to polecenie wdrażania, musisz mieć [najnowszą wersję](/cli/azure/install-azure-cli) interfejsu wiersza polecenia platformy Azure.

```azurecli
az deployment group create \
  --name addappserviceplan \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS
```

---

> [!NOTE]
> Jeśli wdrożenie nie powiodło się, użyj `verbose` przełącznika, aby uzyskać informacje o tworzonych zasobach. Użyj `debug` przełącznika, aby uzyskać więcej informacji na potrzeby debugowania.

## <a name="verify-deployment"></a>Weryfikowanie wdrożenia

Można zweryfikować wdrożenie, przeeksplorowanie grupy zasobów z Azure Portal.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Z menu po lewej stronie wybierz pozycję **grupy zasobów**.
1. Wybierz grupę zasobów, do której została wdrożona.
1. Grupa zasobów zawiera konto magazynu i plan App Service.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli przeniesiesz się do następnego samouczka, nie musisz usuwać grupy zasobów.

Jeśli zatrzymasz się teraz, możesz chcieć wyczyścić wdrożone zasoby, usuwając grupę zasobów.

1. Z Azure Portal z menu po lewej stronie wybierz pozycję **Grupa zasobów** .
2. Wprowadź nazwę grupy zasobów w polu **Filtruj według nazwy**.
3. Wybierz nazwę grupy zasobów.
4. W górnym menu wybierz pozycję **Usuń grupę zasobów** .

## <a name="next-steps"></a>Następne kroki

Wiesz już, jak wyeksportować szablon z Azure Portal i jak używać wyeksportowanego szablonu do tworzenia szablonu. Możesz również użyć szablonów szybkiego startu platformy Azure, aby uprościć tworzenie szablonów.

> [!div class="nextstepaction"]
> [Korzystanie z szablonów szybkiego startu platformy Azure](template-tutorial-quickstart-template.md)
