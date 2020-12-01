---
title: Wdróż szablon — Azure Portal
description: Dowiedz się, jak utworzyć pierwszy szablon Azure Resource Manager (szablon ARM) przy użyciu Azure Portal i jak go wdrożyć.
author: mumian
ms.date: 06/29/2020
ms.topic: quickstart
ms.author: jgao
ms.openlocfilehash: bdbcecc247a4b5318ba44b92befa7e90ac47aa8c
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96349626"
---
# <a name="quickstart-create-and-deploy-arm-templates-by-using-the-azure-portal"></a>Szybki Start: Tworzenie i wdrażanie szablonów ARM przy użyciu Azure Portal

Dowiedz się, jak wygenerować szablon Azure Resource Manager (szablon ARM) przy użyciu Azure Portal i proces edytowania i wdrażania szablonu z poziomu portalu. Szablony ARM to pliki JSON definiujące zasoby, które należy wdrożyć dla rozwiązania. Aby zrozumieć koncepcje związane z wdrażaniem rozwiązań platformy Azure i zarządzaniem nimi, zobacz [Omówienie wdrażania szablonów](overview.md).

![Diagram portalu szybkiego startu szablonu Menedżer zasobów](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-export-deploy-template-portal.png)

Po ukończeniu tego samouczka zostanie wdrożone konto usługi Azure Storage. Ten sam proces umożliwia wdrażanie innych zasobów platformy Azure.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="generate-a-template-using-the-portal"></a>Generowanie szablonu przy użyciu witryny Azure Portal

Tworzenie szablonu ARM od podstaw nie jest łatwym zadaniem, szczególnie w przypadku, gdy jesteś nowym wdrożeniem platformy Azure i nie masz doświadczenia z formatem JSON. W witrynie Azure Portal możesz skonfigurować zasób, na przykład konto usługi Azure Storage. Przed wdrożeniem zasobu można wyeksportować konfigurację do szablonu. Szablon możesz zapisać i użyć go ponownie w przyszłości.

Wielu doświadczonych deweloperów szablonów używa tej metody do generowania szablonów podczas próby wdrożenia zasobów platformy Azure, których nie znają. Aby uzyskać więcej informacji na temat eksportowania szablonów przy użyciu portalu, zobacz [Eksportowanie grup zasobów do szablonów](../management/manage-resource-groups-portal.md#export-resource-groups-to-templates). Innym sposobem znalezienia szablonu roboczego jest korzystanie z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/).

1. W przeglądarce internetowej przejdź do [Azure Portal](https://portal.azure.com) i zaloguj się.
1. W menu witryny Azure Portal wybierz pozycję **Utwórz zasób**.

    ![Menu wybierz pozycję Utwórz zasób z Azure Portal](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-a-resource.png)

1. Wybierz pozycję **Magazyn** > **Konto magazynu**.

    ![Tworzenie konta usługi Azure Storage](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account-portal.png)
1. Wprowadź następujące informacje:

    |Nazwa|Wartość|
    |----|----|
    |**Grupa zasobów**|Wybierz pozycję **Utwórz nową** i podaj wybraną nazwę grupy zasobów. Na zrzucie ekranu nazwa grupy zasobów to *mystorage1016rg*. Grupa zasobów jest kontenerem zasobów platformy Azure. Grupa zasobów ułatwia zarządzanie zasobami platformy Azure. |
    |**Nazwa**|nadaj unikatową nazwę kontu magazynu. Nazwa konta magazynu musi być unikatowa we wszystkich wersjach systemu Azure i zawierać tylko małe litery i cyfry. Nazwa musi mieć długość od 3 do 24 znaków. Jeśli zostanie wyświetlony komunikat o błędzie informujący o tym, że "nazwa konta magazynu" mystorage1016 "jest już zajęta, spróbuj użyć **&lt; swojej nazwy>magazynu &lt; dzisiejszego w MMDD>**, na przykład **johndolestorage1016**. Aby uzyskać więcej informacji, zobacz [reguły nazewnictwa i ograniczenia](/azure/architecture/best-practices/resource-naming).|

    Dla pozostałych właściwości możesz użyć wartości domyślnych.

    ![Tworzenie konfiguracji konta usługi Azure Storage za pomocą witryny Azure Portal](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account.png)

    > [!NOTE]
    > W niektórych z wyeksportowanych szablonów należy wprowadzić pewne zmiany przed ich wdrożeniem.

1. Wybierz pozycję **Przeglądanie + tworzenie** w dolnej części ekranu. Nie wybieraj opcji **Utwórz** w następnym kroku.
1. Wybierz pozycję **Pobierz szablon do automatyzacji** w dolnej części ekranu. W portalu zostanie wyświetlony wygenerowany szablon:

    ![Generowanie szablonu z poziomu witryny Azure Portal](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account-template.png)

    W okienku głównym wyświetlany jest szablon. Jest to plik JSON z sześcioma elementami najwyższego poziomu — `schema`, `contentVersion`, `parameters`, `variables`, `resources` i `output`. Aby uzyskać więcej informacji, zobacz [Omówienie struktury i składni szablonów ARM](./template-syntax.md)

    Zdefiniowano osiem parametrów. Jeden z nich jest nazywany **storageAccountName**. Druga wyróżniona część na poprzednim zrzucie ekranu pokazuje, jak odwoływać się do tego parametru w szablonie. W następnej sekcji będziesz edytować szablon w celu użycia wygenerowanej nazwy konta magazynu.

    W szablonie zdefiniowany jest jeden zasób platformy Azure. Typ to `Microsoft.Storage/storageAccounts` . Zapoznaj się z definicją zasobu i strukturą definicji.
1. Wybierz pozycję **Pobierz** w górnej części ekranu.
1. Otwórz pobrany plik zip, a następnie Zapisz **template.jsna** komputerze. W następnej sekcji użyjesz narzędzia do wdrażania szablonu do edycji szablonu.
1. Wybierz kartę **Parametr**, aby zobaczyć wartości podane dla parametrów. Zapisz te wartości, ponieważ będą potrzebne w następnej sekcji podczas wdrażania szablonu.

    ![Zrzut ekranu, który podświetla kartę parametru, która zawiera podane wartości.](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account-template-parameters.png)

    Korzystając z pliku szablonu i pliku parametrów, można utworzyć zasób, w tym samouczku, koncie usługi Azure Storage.

## <a name="edit-and-deploy-the-template"></a>Edytowanie i wdrażanie szablonu

Witryna Azure Portal może służyć do wykonywania niektórych podstawowych czynności edycji szablonu. W tym przewodniku Szybki start użyjesz narzędzia portalu o nazwie *Wdrożenie szablonu*. Narzędzie *Wdrożenie szablonu* jest używane w tym samouczku, aby umożliwić ukończenie całego samouczka przy użyciu jednego interfejsu — witryny Azure Portal. Aby edytować bardziej skomplikowany szablon, należy rozważyć użycie [Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md), który zapewnia bardziej zaawansowane funkcje edycji.

> [!IMPORTANT]
> Wdrożenie szablonu udostępnia interfejs do testowania prostych szablonów. Nie zaleca się korzystania z tej funkcji w środowisku produkcyjnym. Zamiast tego należy przechowywać szablony na koncie usługi Azure Storage lub w repozytorium kodu źródłowego, takim jak GitHub.

Platforma Azure wymaga, aby każda usługa miała unikatową nazwę. Wdrożenie może zakończyć się niepowodzeniem, jeśli zostanie wprowadzona już istniejąca nazwa konta magazynu. W celu uniknięcia tego problemu zmodyfikuj szablon, aby użyć wywołania funkcji szablonu `uniquestring()` do wygenerowania unikatowej nazwy konta magazynu.

1. W menu Azure Portal w polu wyszukiwania wpisz polecenie **Deploy**, a następnie wybierz pozycję **Wdróż szablon niestandardowy**.

    ![Biblioteka szablonów usługi Azure Resource Manager](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-library.png)

1. Wybierz opcję **Kompiluj własny szablon w edytorze**.
1. Wybierz pozycję **Załaduj plik**, a następnie postępuj zgodnie z instrukcjami, aby załadować plik template.json pobrany w poprzedniej sekcji.
1. Wprowadź następujące trzy zmiany w szablonie:

    ![Szablony usługi Azure Resource Manager](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-edit-storage-account-template-revised.png)

   - Usuń parametr **storageAccountName** , jak pokazano na poprzednim zrzucie ekranu.
   - Dodaj jedną zmienną o nazwie **storageAccountName** , jak pokazano na poprzednim zrzucie ekranu:

       ```json
       "storageAccountName": "[concat(uniqueString(subscription().subscriptionId), 'storage')]"
       ```

       W tym miejscu są używane dwie funkcje szablonów: `concat()` i `uniqueString()`.
   - Zaktualizuj element name zasobu **Microsoft.Storage/storageAccounts**, aby użyta została nowo zdefiniowana zmienna zamiast parametru:

       ```json
       "name": "[variables('storageAccountName')]",
       ```

     Ostateczny szablon powinien wyglądać następująco:

     ```json
     {
       "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
         "location": {
           "type": "string"
         },
         "accountType": {
           "type": "string"
         },
         "kind": {
           "type": "string"
         },
         "accessTier": {
           "type": "string"
         },
         "minimumTlsVersion": {
           "type": "string"
         },
         "supportsHttpsTrafficOnly": {
          "type": "bool"
         },
         "allowBlobPublicAccess": {
           "type": "bool"
         }
       },
       "variables": {
         "storageAccountName": "[concat(uniqueString(subscription().subscriptionId), 'storage')]"
       },
       "resources": [
         {
           "name": "[variables('storageAccountName')]",
           "type": "Microsoft.Storage/storageAccounts",
           "apiVersion": "2019-06-01",
           "location": "[parameters('location')]",
           "properties": {
             "accessTier": "[parameters('accessTier')]",
             "minimumTlsVersion": "[parameters('minimumTlsVersion')]",
             "supportsHttpsTrafficOnly": "[parameters('supportsHttpsTrafficOnly')]",
             "allowBlobPublicAccess": "[parameters('allowBlobPublicAccess')]"
           },
           "dependsOn": [],
           "sku": {
             "name": "[parameters('accountType')]"
           },
           "kind": "[parameters('kind')]",
           "tags": {}
         }
       ],
       "outputs": {}
     }
     ```

1. Wybierz pozycję **Zapisz**.
1. Podaj następujące wartości:

    |Nazwa|Wartość|
    |----|----|
    |**Grupa zasobów**|Wybierz nazwę grupy zasobów utworzoną w ostatniej sekcji. |
    |**Region**|Wybierz lokalizację dla grupy zasobów. Na przykład **Środkowe stany USA**. |
    |**Lokalizacja**|Wybierz lokalizację dla konta magazynu. Na przykład **Środkowe stany USA**. |
    |**Typ konta**|wprowadź wartość **Standard_LRS** na potrzeby tego przewodnika Szybki start. |
    |**Rodzaj**|wprowadź wartość **StorageV2** na potrzeby tego przewodnika Szybki start. |
    |**Warstwa dostępu**|wprowadź wartość **Gorąca** na potrzeby tego przewodnika Szybki start. |
    |**Minimalna wersja protokołu TLS**|Wprowadź **TLS1_0**. |
    |**Obsługuje tylko ruch https**| Wybierz wartość **true** na potrzeby tego przewodnika Szybki start. |
    |**Zezwalaj na dostęp publiczny do obiektów BLOB**| Wybierz wartość **false** na potrzeby tego przewodnika Szybki start. |

1. Wybierz pozycję **Przeglądanie + tworzenie**.
1. Wybierz pozycję **Utwórz**.
1. Wybierz ikonę dzwonka (powiadomienia) w górnej części ekranu, aby wyświetlić stan wdrożenia. Powinien zostać wyświetlony komunikat **Wdrożenie jest w toku**. Poczekaj na zakończenie wdrożenia.

    ![Powiadomienie dotyczące wdrażania szablonów usługi Azure Resource Manager](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-portal-notification.png)

1. W okienku powiadomień wybierz pozycję **Przejdź do grupy zasobów**. Zobaczysz ekran podobny do poniższego:

    ![Grupa zasobów w ramach wdrażania szablonów usługi Azure Resource Manager](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-portal-deployment-resource-group.png)

    Możesz zobaczyć, że stan wdrożenia został oznaczony jako zakończony pomyślnie, natomiast w grupie zasobów jest tylko jedno konto magazynu. Nazwa konta magazynu jest unikatowym ciągiem wygenerowanym przez szablon. Aby dowiedzieć się więcej o korzystaniu z kont magazynu platformy Azure, zobacz przewodnik [Szybki start — przekazywanie, pobieranie i wyświetlanie listy obiektów blob za pomocą witryny Azure Portal](../../storage/blobs/storage-quickstart-blobs-portal.md).

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy zasoby platformy Azure nie będą już potrzebne, wyczyść wdrożone zasoby, usuwając grupę zasobów.

1. W witrynie Azure Portal wybierz pozycję **Grupy zasobów** w menu po lewej stronie.
1. Wprowadź nazwę grupy zasobów w polu **Filtruj według nazwy**.
1. Wybierz nazwę grupy zasobów.  Zostanie wyświetlone konto magazynu w grupie zasobów.
1. Wybierz pozycję **Usuń grupę zasobów** w górnym menu.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób generowania i wdrażania szablonu w witrynie Azure Portal. Szablon używany w tym przewodniku Szybki start to prosty szablon z jednym zasobem platformy Azure. Gdy szablon jest złożony, do jego tworzenia łatwiej jest użyć programu Visual Studio Code lub Visual Studio. Aby dowiedzieć się więcej na temat tworzenia szablonów, zobacz naszą nową serię samouczków dla początkujących:

> [!div class="nextstepaction"]
> [Samouczki dla początkujących](./template-tutorial-create-first-template.md)
