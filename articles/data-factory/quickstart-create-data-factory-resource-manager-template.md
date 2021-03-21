---
title: Tworzenie Azure Data Factory przy użyciu szablonu Azure Resource Manager (szablon ARM)
description: Tworzenie przykładowego potoku Azure Data Factory przy użyciu szablonu Azure Resource Manager (szablon ARM).
ms.service: data-factory
tags: azure-resource-manager
author: dcstwh
ms.author: weetok
ms.reviewer: maghan, jingwang
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 07/16/2020
ms.openlocfilehash: f3e76bf16b702adb26183209d36189a53b695c40
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100373848"
---
# <a name="quickstart-create-an-azure-data-factory-using-arm-template"></a>Szybki Start: Tworzenie Azure Data Factory przy użyciu szablonu ARM

> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-build-your-first-pipeline-using-arm.md)
> * [Bieżąca wersja](quickstart-create-data-factory-resource-manager-template.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

W tym przewodniku szybki start opisano, jak utworzyć fabrykę danych Azure przy użyciu szablonu Azure Resource Manager (szablon ARM). Potok tworzony w tej fabryce danych **kopiuje** dane z jednego folderu do innego folderu w usłudze Azure Blob Storage. Aby zapoznać się z samouczkiem dotyczącym **przekształcania** danych przy użyciu Azure Data Factory, zobacz [Samouczek: Przekształcanie danych przy użyciu platformy Spark](transform-data-using-spark.md).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

> [!NOTE]
> Ten artykuł nie zawiera szczegółowego wprowadzenia do usługi Data Factory. Aby zapoznać się z wprowadzeniem do usługi Azure Data Factory, zobacz [Wprowadzenie do usługi Azure Data Factory](introduction.md).

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-data-factory-v2-blob-to-blob-copy%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="azure-subscription"></a>Subskrypcja platformy Azure

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

### <a name="create-a-file"></a>Tworzenie pliku

Otwórz Edytor tekstu, taki jak **Notatnik**, i Utwórz plik o nazwie **emp.txt** z następującą zawartością:

```emp.txt
John, Doe
Jane, Doe
```

Zapisz plik w folderze **C:\ADFv2QuickStartPSH** . (Jeśli folder jeszcze nie istnieje, utwórz go).

## <a name="review-template"></a>Przejrzyj szablon

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-data-factory-v2-blob-to-blob-copy/).

:::code language="json" source="~/quickstart-templates/101-data-factory-v2-blob-to-blob-copy/azuredeploy.json":::

Istnieją zasoby platformy Azure zdefiniowane w szablonie:

- [Microsoft. Storage/storageAccounts](/azure/templates/Microsoft.Storage/storageAccounts): definiuje konto magazynu.
- [Microsoft. DataFactory/fabryki](/azure/templates/microsoft.datafactory/factories): Utwórz Azure Data Factory.
- [Microsoft. DataFactory/Factors/linkedServices](/azure/templates/microsoft.datafactory/factories/linkedservices): Utwórz połączoną usługę Azure Data Factory.
- [Microsoft. DataFactory/Factors/DataSets](/azure/templates/microsoft.datafactory/factories/datasets): Utwórz zestaw danych Azure Data Factory.
- [Microsoft. DataFactory/fabryki/potoki](/azure/templates/microsoft.datafactory/factories/pipelines): Tworzenie potoku Azure Data Factory.

Więcej przykładów szablonów Azure Data Factory można znaleźć w [galerii szablonów szybkiego startu](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Datafactory&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Wdrożenie szablonu

1. Wybierz poniższy obraz, aby zalogować się na platformie Azure i otworzyć szablon. Szablon tworzy konto Azure Data Factory, konto magazynu i kontener obiektów BLOB.

    [![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-data-factory-v2-blob-to-blob-copy%2Fazuredeploy.json)

2. Wybierz lub wprowadź następujące wartości.

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-deploy-arm-template.png" alt-text="Wdróż szablon ARM w usłudze ADF":::

    O ile nie zostanie on określony, użyj wartości domyślnych, aby utworzyć Azure Data Factory zasobów:

    - **Subskrypcja**: wybierz subskrypcję platformy Azure.
    - **Grupa zasobów**: wybierz pozycję **Utwórz nową**, wprowadź unikatową nazwę grupy zasobów, a następnie wybierz przycisk **OK**.
    - **Region**: Wybierz lokalizację.  Na przykład *Wschodnie stany USA*.
    - **Nazwa Data Factory**: Użyj wartości domyślnej.
    - **Lokalizacja**: Użyj wartości domyślnej.
    - **Nazwa konta magazynu**: Użyj wartości domyślnej.
    - **Kontener obiektów BLOB**: Użyj wartości domyślnej.

## <a name="review-deployed-resources"></a>Przejrzyj wdrożone zasoby

1. Wybierz pozycję **Przejdź do grupy zasobów**.

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-go-to-resource-group.png" alt-text="Grupa zasobów":::

2.  Sprawdź, czy Azure Data Factory został utworzony.
    1. Nazwa Azure Data Factory jest w formacie — DataFactory \<uniqueid\> .

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-sample.png" alt-text="Przykład Data Factory":::

2. Sprawdź, czy konto magazynu zostało utworzone.
    1. Nazwa konta magazynu jest w formacie-Storage \<uniqueid\> .

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-storage-account.png" alt-text="Konto magazynu":::

3. Wybierz utworzone konto magazynu, a następnie wybierz pozycję **Containers (kontenery**).
    1. Na stronie **kontenery** wybierz utworzony kontener obiektów BLOB.
        1. Nazwa kontenera obiektów BLOB jest w formacie-BLOB \<uniqueid\> .

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-blob-container.png" alt-text="Kontener obiektów blob":::

### <a name="upload-a-file"></a>Przekazywanie pliku

1. Na stronie **kontenery** wybierz pozycję **Przekaż**.

2. W okienku po prawej stronie zaznacz pole **pliki** , a następnie wyszukaj i wybierz utworzony wcześniej plik **emp.txt** .

3. Rozwiń nagłówek **Zaawansowane** .

4. W polu **Przekaż do folderu** wprowadź *dane wejściowe*.

5. Wybierz przycisk **Przekaż**. Na liście powinien pojawić się plik **emp.txt** i stan przekazywania.

6. Wybierz ikonę **Zamknij** ( **symbol X**), aby zamknąć stronę **przekazywanie obiektu BLOB** .

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-upload-blob-file.png" alt-text="Przekaż plik do folderu wejściowego":::

Pozostaw otwartą stronę kontenera, ponieważ można jej użyć do zweryfikowania danych wyjściowych na końcu tego przewodnika Szybki Start.

### <a name="start-trigger"></a>Uruchom wyzwalacz

1. Przejdź do strony **fabryki danych** i wybierz utworzoną fabrykę danych. 

2. Wybierz kafelek **tworzenie & monitor** . 

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-author-monitor-tile.png" alt-text="Tworzenie monitora &":::

2. Wybierz kartę **autor** :::image type="icon" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-author.png" border="false"::: .

3. Wybierz utworzony potok-ArmtemplateSampleCopyPipeline.

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-pipelines.png" alt-text="Potok szablonu ARM":::

4. Wybierz pozycję **Dodaj wyzwalacz wyzwalacza**  >  **teraz**.

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-trigger-now.png" alt-text="Wyzwalacz":::

5. W prawym okienku w obszarze **uruchomienie potoku** wybierz pozycję **OK**.

### <a name="monitor-the-pipeline"></a>Monitorowanie potoku

1. Wybierz kartę **monitorowanie** :::image type="icon" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-monitor.png" border="false"::: .

2. Zostaną wyświetlone uruchomienia działania skojarzone z uruchomieniem potoku. W tym przewodniku Szybki start potok ma tylko jedno działanie typu Kopiowanie. W związku z tym zostanie wyświetlony przebieg dla tego działania.

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-successful-run.png" alt-text="Pomyślne uruchomienie":::

### <a name="verify-the-output-file"></a>Weryfikowanie pliku wyjściowego

Potok automatycznie tworzy folder wyjściowy w kontenerze obiektów BLOB. Następnie kopiuje plik emp.txt z folderu wejściowego do folderu wyjściowego. 

1. W Azure Portal na stronie **kontenery** wybierz pozycję **Odśwież** , aby wyświetlić folder dane wyjściowe. 

2. Wybierz pozycję **dane wyjściowe** na liście folderów.

3. Upewnij się, że plik **emp.txt** jest kopiowany do folderu wyjściowego. 

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-output.png" alt-text="Dane wyjściowe":::

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Zasoby, które zostały utworzone w ramach tego przewodnika Szybki start, możesz wyczyścić na dwa sposoby. [Grupę zasobów platformy Azure](../azure-resource-manager/management/delete-resource-group.md), która obejmuje wszystkie zasoby w grupie zasobów, można usunąć. Jeśli chcesz zachować inne zasoby bez zmian, usuń tylko fabrykę danych utworzoną w tym samouczku.

Usunięcie grupy zasobów powoduje usunięcie wszystkich zasobów łącznie z fabrykami danych w nich zawartymi. Uruchom poniższe polecenie, aby usunąć całą grupę zasobów: 

```azurepowershell-interactive
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

Jeśli chcesz usunąć tylko fabrykę danych, a nie całą grupę zasobów, uruchom następujące polecenie: 

```azurepowershell-interactive
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono Azure Data Factory przy użyciu szablonu usługi ARM i zweryfikowano wdrożenie. Aby dowiedzieć się więcej na temat Azure Data Factory i Azure Resource Manager, przejdź do artykułu poniżej.

- [Dokumentacja Azure Data Factory](index.yml)
- Dowiedz się więcej o usłudze [Azure Resource Manager](../azure-resource-manager/management/overview.md)
- Pobierz inne [szablony Azure Data Factory ARM](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Datafactory&pageNumber=1&sort=Popular)