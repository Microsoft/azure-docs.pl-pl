---
title: Przenoszenie konta usługi Azure Storage do innego regionu | Microsoft Docs
description: Pokazuje, jak przenieść konto usługi Azure Storage do innego regionu.
services: storage
author: normesta
ms.service: storage
ms.subservice: common
ms.topic: how-to
ms.date: 05/11/2020
ms.author: normesta
ms.reviewer: dineshm
ms.openlocfilehash: 1900326bf03c6a32f25c7a019d8bd1e460735bd6
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107505602"
---
# <a name="move-an-azure-storage-account-to-another-region"></a>Przenoszenie konta usługi Azure Storage do innego regionu

Aby przenieść konto magazynu, utwórz kopię konta magazynu w innym regionie. Następnie przenieś dane na to konto przy użyciu narzędzia AzCopy lub innego wybranego narzędzia.

Ten artykuł obejmuje następujące zagadnienia:

> [!div class="checklist"]
> 
> * Wyeksportuj szablon.
> * Zmodyfikuj szablon, dodając region docelowy i nazwę konta magazynu.
> * Wd wdrażaj szablon, aby utworzyć nowe konto magazynu.
> * Skonfiguruj nowe konto magazynu.
> * Przenieś dane na nowe konto magazynu.
> * Usuń zasoby w regionie źródłowym.

## <a name="prerequisites"></a>Wymagania wstępne

- Upewnij się, że usługi i funkcje używane przez konto są obsługiwane w regionie docelowym.

- W przypadku funkcji w wersji zapoznawczej upewnij się, że Twoja subskrypcja znajduje się na liście zezwalania dla regionu docelowego.

<a id="prepare"></a>

## <a name="prepare"></a>Przygotowywanie

Aby rozpocząć, wyeksportować, a następnie zmodyfikować Resource Manager szablonu. 

### <a name="export-a-template"></a>Eksportowanie szablonu

Ten szablon zawiera ustawienia opisujące Twoje konto magazynu. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

Aby wyeksportować szablon przy użyciu witryny Azure Portal:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Wybierz **pozycję Wszystkie zasoby,** a następnie wybierz konto magazynu.

3. Wybierz > **eksportu usługi Automation.**  >  

4. Wybierz **pozycję Pobierz** w bloku **Eksportuj** szablon.

5. Znajdź plik zip pobrany z portalu i rozpakować go do wybranego folderu.

   Ten plik zip zawiera pliki JSON, które składają się na szablon i skrypty służące do wdrażania szablonu.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Aby wyeksportować szablon przy użyciu programu PowerShell:

1. Zaloguj się do subskrypcji platformy Azure za pomocą polecenia [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) i postępuj zgodnie z instrukcjami na ekranie:

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

1. W Azure Portal wybierz pozycję **Utwórz zasób.**

2. W **polu Wyszukaj w witrynie Marketplace** wpisz wdrożenie **szablonu,** a następnie naciśnij klawisz **ENTER.**

3. Wybierz **Template deployment**.

    ![Biblioteka szablonów usługi Azure Resource Manager](./media/storage-account-move/azure-resource-manager-template-library.png)

4. Wybierz przycisk **Utwórz**.

5. Wybierz **pozycję Build your own template in the editor (Skompilowanie własnego szablonu w edytorze).**

6. Wybierz **pozycję Załaduj** plik , a następnie postępuj zgodnie z instrukcjami,template.js **załadować** plik pobrany w ostatniej sekcji.

7. W **template.jspliku** nazwij docelowe konto magazynu, ustawiając wartość domyślną nazwy konta magazynu. W tym przykładzie wartość domyślna nazwy konta magazynu jest ustawiana na `mytargetaccount` .
    
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
    Aby uzyskać kody lokalizacji regionów, zobacz [Lokalizacje platformy Azure](https://azure.microsoft.com/global-infrastructure/locations/).  Kodem dla regionu jest nazwa regionu bez spacji, **Środkowe centrum**  =  **USA**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Aby wdrożyć szablon przy użyciu programu PowerShell:

1. W **template.jspliku** nazwij docelowe konto magazynu, ustawiając wartość domyślną nazwy konta magazynu. W tym przykładzie wartość domyślna nazwy konta magazynu jest ustawiana na `mytargetaccount` .
    
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

2. Edytuj właściwość **location** w pliku **template.jsdo** regionu docelowego. W tym przykładzie region docelowy jest ustawiany na `eastus` wartość .

    ```json
    "resources": [{
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2019-04-01",
         "name": "[parameters('storageAccounts_mysourceaccount_name')]",
         "location": "eastus"
         }]          
    ```

    Kody regionów można uzyskać, uruchamiając [polecenie Get-AzLocation.](/powershell/module/az.resources/get-azlocation)

    ```azurepowershell-interactive
    Get-AzLocation | format-table 
    ```
---

<a id="move"></a>

## <a name="move"></a>Move

Wdróż szablon, aby utworzyć nowe konto magazynu w regionie docelowym. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Zapisz plik **template.jspliku.**

2. Wprowadź lub wybierz wartości właściwości:

- **Subskrypcja:** wybierz subskrypcję platformy Azure.

- **Grupa zasobów**: wybierz pozycję **Utwórz nową** i nadaj nazwę grupie zasobów.

- **Lokalizacja:** wybierz lokalizację platformy Azure.

3. Kliknij pole **wyboru Wyrażam zgodę na powyższe warunki** i postanowienia, a następnie kliknij przycisk **Wybierz** zakup.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Uzyskaj identyfikator subskrypcji, w której chcesz wdrożyć docelowy publiczny adres IP, za pomocą [get-AzSubscription:](/powershell/module/az.accounts/get-azsubscription)

   ```azurepowershell-interactive
   Get-AzSubscription
   ```

2. Użyj tych poleceń, aby wdrożyć szablon:

   ```azurepowershell-interactive
   $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
   $location = Read-Host -Prompt "Enter the location (i.e. centralus)"

   New-AzResourceGroup -Name $resourceGroupName -Location "$location"
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "<name of your local template file>"  
   ```
---

### <a name="configure-the-new-storage-account"></a>Konfigurowanie nowego konta magazynu

Niektóre funkcje nie są eksportowane do szablonu, dlatego konieczne będzie dodanie ich do nowego konta magazynu. 

W poniższej tabeli wymieniono te funkcje wraz ze wskazówkami dotyczącymi dodania ich do nowego konta magazynu.

| Cecha    | Wskazówki    |
|--------|-----------|
| **Zasady zarządzania cyklem życia** | [Zarządzanie cyklem życia magazynu usługi Azure Blob Storage](../blobs/storage-lifecycle-management-concepts.md) |
| **Statyczne witryny internetowe** | [Hostowanie statycznej witryny internetowej w usłudze Azure Storage](../blobs/storage-blob-static-website-how-to.md) |
| **Subskrypcje zdarzeń** | [Reagowanie na zdarzenia usługi Blob Storage](../blobs/storage-blob-event-overview.md) |
| **Alerty** | [Tworzenie i wyświetlanie alertów dziennika aktywności oraz zarządzanie nimi przy użyciu Azure Monitor](../../azure-monitor/alerts/alerts-activity-log.md) |
| **Content Delivery Network (CDN)** | [Używanie Azure CDN do uzyskiwania dostępu do obiektów blob z domenami niestandardowymi za pośrednictwem protokołu HTTPS](../blobs/storage-https-custom-domain-cdn.md) |

> [!NOTE] 
> W przypadku skonfigurowania sieci CDN dla źródłowego konta magazynu wystarczy zmienić źródło istniejącej sieci CDN na podstawowy punkt końcowy usługi obiektów blob (lub podstawowy punkt końcowy statycznej witryny internetowej) nowego konta. 

### <a name="move-data-to-the-new-storage-account"></a>Przenoszenie danych do nowego konta magazynu

Narzędzie AzCopy jest preferowanym narzędziem do przenoszenia danych. Program jest zoptymalizowany pod kątem wydajności.  Jednym ze sposobów na przyspieszenie jest kopiowanie danych bezpośrednio pomiędzy serwerami magazynu, dzięki czemu program AzCopy nie wykorzystuje przepustowości sieci Twojego komputera. Użyj polecenia AzCopy w wierszu polecenia lub jako część niestandardowego skryptu. Zobacz [Wprowadzenie do programu AzCopy.](/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

Możesz również użyć Azure Data Factory, aby przenieść dane. Zapewnia intuicyjny interfejs użytkownika. Aby użyć Azure Data Factory, zobacz dowolny z tych linków:. 

  - [Kopiowanie danych do lub z usługi Azure Blob Storage za pomocą usługi Azure Data Factory](/azure/data-factory/connector-azure-blob-storage)
  - [Kopiowanie danych do lub z usługi Data Lake Storage Gen2 za pomocą usługi Azure Data Factory](/azure/data-factory/connector-azure-data-lake-storage)
  - [Kopiowanie danych z lub do usługi Azure File Storage za pomocą usługi Azure Data Factory](/azure/data-factory/connector-azure-file-storage)
  - [Kopiowanie danych do i z usługi Azure Table Storage za pomocą usługi Azure Data Factory](/azure/data-factory/connector-azure-table-storage)

---

## <a name="discard-or-clean-up"></a>Odrzucanie lub czyszczenie

Jeśli chcesz rozpocząć od początku wdrożenia, możesz usunąć docelowe konto magazynu i [](#prepare) powtórzyć [](#move) kroki opisane w sekcjach Przygotowywanie i przenoszenie tego artykułu.

Aby zatwierdzić zmiany i zakończyć przenoszenie konta magazynu, usuń źródłowe konto magazynu.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Aby usunąć konto magazynu przy użyciu witryny Azure Portal:

1. W Azure Portal rozwiń menu po lewej stronie, aby otworzyć menu  usług, a następnie wybierz pozycję Konta magazynu, aby wyświetlić listę kont magazynu.

2. Znajdź docelowe konto magazynu do usunięcia, a następnie kliknij prawym przyciskiem myszy przycisk **Więcej** (**...**) po prawej stronie listy.

3. Wybierz **pozycję Usuń** i potwierdź.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Aby usunąć grupę zasobów i skojarzone z nią zasoby, w tym nowe konto magazynu, użyj polecenia [Remove-AzStorageAccount:](/powershell/module/az.storage/remove-azstorageaccount)

```powershell
Remove-AzStorageAccount -ResourceGroupName  $resourceGroup -AccountName $storageAccount
```
---

## <a name="next-steps"></a>Następne kroki

W tym samouczku przeniesiono konto usługi Azure Storage z jednego regionu do innego i wyczyszczone zasoby źródłowe.  Aby dowiedzieć się więcej na temat przenoszenia zasobów między regionami i odzyskiwania po awarii na platformie Azure, zobacz:


- [Przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Przenoszenie maszyn wirtualnych platformy Azure do innego regionu](../../site-recovery/azure-to-azure-tutorial-migrate.md)