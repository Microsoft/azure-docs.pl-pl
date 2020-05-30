---
title: Przenoszenie konta usługi Azure Storage do innego regionu | Microsoft Docs
description: Pokazuje, w jaki sposób przenieść konto usługi Azure Storage do innego regionu.
services: storage
author: normesta
ms.service: storage
ms.subservice: common
ms.topic: article
ms.date: 05/11/2020
ms.author: normesta
ms.reviewer: dineshm
ms.openlocfilehash: 64d987ff01e596eefa98e8086788546c465e2d83
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/29/2020
ms.locfileid: "84195391"
---
# <a name="move-an-azure-storage-account-to-another-region"></a>Przenoszenie konta usługi Azure Storage do innego regionu

Aby przenieść konto magazynu, Utwórz kopię konta magazynu w innym regionie. Następnie Przenieś dane do tego konta za pomocą AzCopy lub innego wybranego narzędzia.

W tym artykule dowiesz się, jak:

> [!div class="checklist"]
> 
> * Wyeksportuj szablon.
> * Zmodyfikuj szablon, dodając region docelowy i nazwę konta magazynu.
> * Wdróż szablon, aby utworzyć nowe konto magazynu.
> * Skonfiguruj nowe konto magazynu.
> * Przenieś dane na nowe konto magazynu.
> * Usuń zasoby w regionie źródłowym.

## <a name="prerequisites"></a>Wymagania wstępne

- Upewnij się, że usługi i funkcje używane przez konto są obsługiwane w regionie docelowym.

- W przypadku funkcji w wersji zapoznawczej upewnij się, że subskrypcja jest listy dozwolonych dla regionu docelowego.

<a id="prepare"></a>

## <a name="prepare"></a>Przygotowanie

Aby rozpocząć, wyeksportować, a następnie zmodyfikować szablon Menedżer zasobów. 

### <a name="export-a-template"></a>Eksportowanie szablonu

Ten szablon zawiera ustawienia opisujące Twoje konto magazynu. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

Aby wyeksportować szablon przy użyciu Azure Portal:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Wybierz pozycję **wszystkie zasoby** , a następnie wybierz swoje konto magazynu.

3. Wybierz pozycję > **Ustawienia**  >  **Eksportuj szablon**.

4. Wybierz pozycję **Pobierz** w bloku **Eksportuj szablon** .

5. Znajdź plik zip pobrany z portalu i rozpakuj ten plik do wybranego folderu.

   Ten plik zip zawiera pliki. JSON, które składają się na szablon i skrypty do wdrożenia szablonu.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Aby wyeksportować szablon przy użyciu programu PowerShell:

1. Zaloguj się do subskrypcji platformy Azure za pomocą polecenia [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) i postępuj zgodnie z instrukcjami wyświetlanymi na ekranie:

   ```azurepowershell-interactive
   Connect-AzAccount
   ```
2. Jeśli Twoja tożsamość jest skojarzona z więcej niż jedną subskrypcją, ustaw aktywną subskrypcję na subskrypcję konta magazynu, które chcesz przenieść.

   ```azurepowershell-interactive
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

3. Wyeksportuj szablon źródłowego konta magazynu. Te polecenia zapisują szablon JSON w bieżącym katalogu.

   ```azurepowershell-interactive
   $resource = Get-AzResource `
     -ResourceGroupName <resource-group-name> `
     -ResourceName <storage-account-name> `
     -ResourceType Microsoft.Storage/storageAccounts
   Export-AzResourceGroup `
     -ResourceGroupName <resource-group-name> `
     -Resource $resource.ResourceId
   ```

---

### <a name="modify-the-template"></a>Modyfikowanie szablonu 

Zmodyfikuj szablon, zmieniając nazwę i region konta magazynu.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Aby wdrożyć szablon przy użyciu Azure Portal:

1. W witrynie Azure Portal wybierz polecenie **Utwórz zasób**.

2. W obszarze **Przeszukaj witrynę Marketplace** wpisz **wdrożenie szablonu**, a następnie naciśnij klawisz **ENTER**.

3. Wybierz pozycję **Wdrożenie szablonu**.

    ![Biblioteka szablonów usługi Azure Resource Manager](./media/storage-account-move/azure-resource-manager-template-library.png)

4. Wybierz pozycję **Utwórz**.

5. Wybierz pozycję **Utwórz własny szablon w edytorze**.

6. Wybierz pozycję **Załaduj plik**, a następnie postępuj zgodnie z instrukcjami, aby załadować plik **Template. JSON** pobrany z ostatniej sekcji.

7. W pliku **Template. JSON** Nazwij docelowe konto magazynu, ustawiając wartość domyślną nazwy konta magazynu. W tym przykładzie ustawiono wartość domyślną nazwy konta magazynu na `mytargetaccount` .
    
    ```json
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccounts_mysourceaccount_name": {
            "defaultValue": "mytargetaccount",
            "type": "String"
        }
    },
 
8. Edit the **location** property in the **template.json** file to the target region. This example sets the target region to `centralus`.

    ```json
    "resources": [{
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2019-04-01",
         "name": "[parameters('storageAccounts_mysourceaccount_name')]",
         "location": "centralus"
         }]          
    ```
    Aby uzyskać kody lokalizacji regionu, zobacz [lokalizacje platformy Azure](https://azure.microsoft.com/global-infrastructure/locations/).  Kod regionu to nazwa regionu bez spacji, **środkowe stany USA**  =  **centralus**.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Aby wdrożyć szablon przy użyciu programu PowerShell:

1. W pliku **Template. JSON** Nazwij docelowe konto magazynu, ustawiając wartość domyślną nazwy konta magazynu. W tym przykładzie ustawiono wartość domyślną nazwy konta magazynu na `mytargetaccount` .
    
    ```json
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccounts_mysourceaccount_name": {
            "defaultValue": "mytargetaccount",
            "type": "String"
        }
    },
    ``` 

2. Edytuj Właściwość **Location** w pliku **Template. JSON** w regionie docelowym. Ten przykład ustawia region docelowy na `eastus` .

    ```json
    "resources": [{
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2019-04-01",
         "name": "[parameters('storageAccounts_mysourceaccount_name')]",
         "location": "eastus"
         }]          
    ```

    Kody regionów można uzyskać, uruchamiając polecenie [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) .

    ```azurepowershell-interactive
    Get-AzLocation | format-table 
    ```
---

<a id="move"></a>

## <a name="move"></a>Move

Wdróż szablon, aby utworzyć nowe konto magazynu w regionie docelowym. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Zapisz plik **Template. JSON** .

2. Wprowadź lub wybierz wartości właściwości:

- **Subskrypcja**: wybierz subskrypcję platformy Azure.

- **Grupa zasobów**: wybierz pozycję **Utwórz nową** i nadaj nazwę grupie zasobów.

- **Lokalizacja**: Wybierz lokalizację platformy Azure.

3. Kliknij pole wyboru **Zgadzam się na powyższe warunki i postanowienia** , a następnie kliknij przycisk **Wybierz zakup** .

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

1. Uzyskaj identyfikator subskrypcji, w której chcesz wdrożyć docelowy publiczny adres IP za pomocą [Get-AzSubscription](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-2.5.0):

   ```azurepowershell-interactive
   Get-AzSubscription
   ```

2. Użyj tych poleceń do wdrożenia szablonu:

   ```azurepowershell-interactive
   $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
   $location = Read-Host -Prompt "Enter the location (i.e. centralus)"

   New-AzResourceGroup -Name $resourceGroupName -Location "$location"
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "<name of your local template file>"  
   ```
---

### <a name="configure-the-new-storage-account"></a>Konfigurowanie nowego konta magazynu

Niektóre funkcje nie będą eksportowane do szablonu, dlatego musisz dodać je do nowego konta magazynu. 

W poniższej tabeli wymieniono te funkcje wraz ze wskazówkami dotyczącymi dodawania ich do nowego konta magazynu.

| Cechy    | Wskazówki    |
|--------|-----------|
| **Zasady zarządzania cyklem życia** | [Zarządzanie cyklem życia magazynu usługi Azure Blob Storage](../blobs/storage-lifecycle-management-concepts.md) |
| **Statyczne witryny internetowe** | [Hostowanie statycznej witryny sieci Web w usłudze Azure Storage](../blobs/storage-blob-static-website-how-to.md) |
| **Subskrypcje zdarzeń** | [Reagowanie na zdarzenia usługi Blob Storage](../blobs/storage-blob-event-overview.md) |
| **Alerty** | [Tworzenie i wyświetlanie alertów dziennika aktywności oraz zarządzanie nimi za pomocą Azure Monitor](../../azure-monitor/platform/alerts-activity-log.md) |
| **Content Delivery Network (CDN)** | [Użyj Azure CDN, aby uzyskać dostęp do obiektów BLOB za pomocą domen niestandardowych za pośrednictwem protokołu HTTPS](../blobs/storage-https-custom-domain-cdn.md) |

> [!NOTE] 
> Jeśli skonfigurujesz sieć CDN dla konta magazynu źródłowego, po prostu zmień źródło istniejącej sieci CDN na podstawowy punkt końcowy usługi BLOB Service (lub podstawowy punkt końcowy witryny sieci Web) nowego konta. 

### <a name="move-data-to-the-new-storage-account"></a>Przenoszenie danych do nowego konta magazynu

AzCopy jest preferowanym narzędziem do przenoszenia danych. Jest zoptymalizowany pod kątem wydajności.  Jednym ze sposobów jest to, że dane są kopiowane bezpośrednio między serwerami magazynu, więc AzCopy nie korzysta z przepustowości sieci komputera. Użyj AzCopy w wierszu polecenia lub jako część niestandardowego skryptu. Zobacz Rozpoczynanie [pracy z usługą AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

Możesz również użyć Azure Data Factory, aby przenieść dane. Zapewnia intuicyjny interfejs użytkownika. Aby użyć Azure Data Factory, zobacz dowolne z tych linków:. 

  - [Kopiowanie danych do lub z usługi Azure Blob Storage za pomocą Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
  - [Kopiowanie danych do lub z Azure Data Lake Storage Gen2 przy użyciu Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
  - [Kopiowanie danych z lub do File Storage platformy Azure przy użyciu Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-file-storage)
  - [Kopiowanie danych do i z usługi Azure Table Storage przy użyciu Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-table-storage)

---

## <a name="discard-or-clean-up"></a>Odrzuć lub wyczyść

Jeśli po wdrożeniu chcesz zacząć od początku, możesz usunąć docelowe konto magazynu i powtórzyć kroki opisane w sekcjach [przygotowanie](#prepare) i [przeniesienie](#move) tego artykułu.

Aby zatwierdzić zmiany i zakończyć Przenoszenie konta magazynu, Usuń konto magazynu źródłowego.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Aby usunąć konto magazynu przy użyciu Azure Portal:

1. W Azure Portal rozwiń menu po lewej stronie, aby otworzyć menu usług, a następnie wybierz pozycję **konta magazynu** , aby wyświetlić listę kont magazynu.

2. Znajdź docelowe konto magazynu do usunięcia, a następnie kliknij prawym przyciskiem myszy przycisk **więcej** (**...**) po prawej stronie listy.

3. Wybierz pozycję **Usuń**i potwierdź.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Aby usunąć grupę zasobów i skojarzone z nią zasoby, w tym nowe konto magazynu, użyj polecenia [Remove-AzStorageAccount](/powershell/module/az.storage/remove-azstorageaccount) :

```powershell
Remove-AzStorageAccount -ResourceGroupName  $resourceGroup -AccountName $storageAccount
```
---

## <a name="next-steps"></a>Następne kroki

W tym samouczku przeniesiono konto usługi Azure Storage z jednego regionu do innego i wyczyszczono zasoby źródłowe.  Aby dowiedzieć się więcej o przenoszeniu zasobów między regionami i odzyskiwaniem po awarii na platformie Azure, zobacz:


- [Przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Przenoszenie maszyn wirtualnych platformy Azure do innego regionu](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
