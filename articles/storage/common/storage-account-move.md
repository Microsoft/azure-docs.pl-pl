---
title: Przenoszenie konta usługi Azure Storage do innego regionu | Dokumenty firmy Microsoft
description: Pokazuje, jak przenieść konto usługi Azure Storage do innego regionu.
services: storage
author: normesta
ms.service: storage
ms.subservice: common
ms.topic: article
ms.date: 09/27/2019
ms.author: normesta
ms.reviewer: dineshm
ms.openlocfilehash: c8578c518ac45bea147790028c2904c7ce36fffb
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81459036"
---
# <a name="move-an-azure-storage-account-to-another-region"></a>Przenoszenie konta usługi Azure Storage do innego regionu

Aby przenieść konto magazynu, utwórz kopię konta magazynu w innym regionie. Następnie przenieś dane na to konto za pomocą AzCopy lub innego wybranego narzędzia.

W tym artykule dowiesz się, jak:

> [!div class="checklist"]
> 
> * Eksportowanie szablonu.
> * Zmodyfikuj szablon, dodając region docelowy i nazwę konta magazynu.
> * Wdrożenie szablonu w celu utworzenia nowego konta magazynu.
> * Skonfiguruj nowe konto magazynu.
> * Przenoszenie danych na nowe konto magazynu.
> * Usuń zasoby w regionie źródłowym.

## <a name="prerequisites"></a>Wymagania wstępne

- Upewnij się, że usługi i funkcje używane przez Twoje konto są obsługiwane w regionie docelowym.

- W przypadku funkcji w wersji zapoznawczej upewnij się, że subskrypcja znajduje się na białej liście regionu docelowego.

<a id="prepare" />

## <a name="prepare"></a>Przygotowanie

Aby rozpocząć, wyeksportuj, a następnie zmodyfikuj szablon Menedżera zasobów. 

### <a name="export-a-template"></a>Eksportowanie szablonu

Ten szablon zawiera ustawienia opisujące twoje konto magazynu. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

Aby wyeksportować szablon przy użyciu witryny Azure portal:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Wybierz **pozycję Wszystkie zasoby,** a następnie wybierz konto magazynu.

3. Wybierz**szablon eksportu** **> Ustawienia** > .

4. Wybierz **pozycję Pobierz** w bloku **Eksportuj szablon.**

5. Znajdź plik zip pobrany z portalu i rozpaj ten plik do wybranego folderu.

   Ten plik zip zawiera pliki .json, które składają się na szablon i skrypty do wdrożenia szablonu.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Aby wyeksportować szablon przy użyciu programu PowerShell:

1. Zaloguj się do subskrypcji platformy Azure za pomocą polecenia [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) i postępuj zgodnie ze wskazówkami wyświetlanymi na ekranie:

   ```azurepowershell-interactive
   Connect-AzAccount
   ```
2. Jeśli Twoja tożsamość jest skojarzona z więcej niż jedną subskrypcją, ustaw aktywną subskrypcję na subskrypcję konta magazynu, które chcesz przenieść.

   ```azurepowershell-interactive
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

3. Eksportuj szablon źródłowego konta magazynu. Te polecenia zapisują szablon json w bieżącym katalogu.

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

Zmodyfikuj szablon, zmieniając nazwę konta magazynu i region.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Aby wdrożyć szablon przy użyciu witryny Azure portal:

1. W witrynie Azure Portal wybierz polecenie **Utwórz zasób**.

2. W obszarze **Przeszukaj witrynę Marketplace** wpisz **wdrożenie szablonu**, a następnie naciśnij klawisz **ENTER**.

3. Wybierz pozycję **Wdrożenie szablonu**.

    ![Biblioteka szablonów usługi Azure Resource Manager](./media/storage-account-move/azure-resource-manager-template-library.png)

4. Wybierz pozycję **Utwórz**.

5. Wybierz pozycję **Utwórz własny szablon w edytorze**.

6. Wybierz **pozycję Załaduj plik**, a następnie postępuj zgodnie z instrukcjami, aby załadować plik **template.json** pobrany w ostatniej sekcji.

7. W pliku **template.json** nazwij docelowe konto magazynu, ustawiając domyślną wartość nazwy konta magazynu. W tym przykładzie ustawia domyślną `mytargetaccount`wartość nazwy konta magazynu na .
    
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
    Aby uzyskać kody lokalizacji regionu, zobacz [Lokalizacje platformy Azure](https://azure.microsoft.com/global-infrastructure/locations/).  Kod dla regionu to nazwa regionu bez spacji, **Central US** = **centralus**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Aby wdrożyć szablon przy użyciu programu PowerShell:

1. W pliku **template.json** nazwij docelowe konto magazynu, ustawiając domyślną wartość nazwy konta magazynu. W tym przykładzie ustawia domyślną `mytargetaccount`wartość nazwy konta magazynu na .
    
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

2. Edytuj właściwość **lokalizacji** w pliku **template.json** w regionie docelowym. W tym przykładzie `eastus`ustawia region docelowy na .

    ```json
    "resources": [{
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2019-04-01",
         "name": "[parameters('storageAccounts_mysourceaccount_name')]",
         "location": "eastus"
         }]          
    ```

    Kody regionów można uzyskać, uruchamiając polecenie [Get-AzLocation.](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0)

    ```azurepowershell-interactive
    Get-AzLocation | format-table 
    ```
---

<a id="move" />

## <a name="move"></a>Move

Wdrożenie szablonu w celu utworzenia nowego konta magazynu w regionie docelowym. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Zapisz plik **template.json.**

2. Wprowadź lub wybierz wartości właściwości:

- **Subskrypcja**: Wybierz subskrypcję platformy Azure.

- **Grupa zasobów**: wybierz pozycję **Utwórz nową** i nadaj nazwę grupie zasobów.

- **Lokalizacja:** Wybierz lokalizację platformy Azure.

3. Kliknij pole **wyboru Zgadzam się z warunkami podanymi powyżej,** a następnie kliknij przycisk Wybierz **zakup.**

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Uzyskaj identyfikator subskrypcji, w którym chcesz wdrożyć docelowy publiczny adres IP za pomocą [get-AzSubscription:](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-2.5.0)

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

Niektóre funkcje nie będą eksportowane do szablonu, więc musisz dodać je do nowego konta magazynu. 

W poniższej tabeli wymieniono te funkcje wraz ze wskazówkami dotyczącymi dodawania ich do nowego konta magazynu.

| Funkcja    | Wskazówki    |
|--------|-----------|
| **Zasady zarządzania cyklem życia** | [Zarządzanie cyklem życia magazynu usługi Azure Blob Storage](../blobs/storage-lifecycle-management-concepts.md) |
| **Statyczne witryny internetowe** | [Hostowanie statycznej witryny sieci Web w usłudze Azure Storage](../blobs/storage-blob-static-website-how-to.md) |
| **Subskrypcje zdarzeń** | [Reagowanie na zdarzenia usługi Blob Storage](../blobs/storage-blob-event-overview.md) |
| **Alerty** | [Tworzenie, wyświetlanie i zarządzanie alertami dziennika aktywności przy użyciu usługi Azure Monitor](../../azure-monitor/platform/alerts-activity-log.md) |
| **Sieć dostarczania zawartości (CDN)** | [Dostęp do obiektów blob z domenami niestandardowymi za pomocą usługi Azure CDN za pośrednictwem protokołu HTTPS](../blobs/storage-https-custom-domain-cdn.md) |

> [!NOTE] 
> Jeśli skonfigurujesz usługę CDN dla źródłowego konta magazynu, po prostu zmień początek istniejącej sieci CDN na podstawowy punkt końcowy usługi obiektów blob (lub podstawowy statyczny punkt końcowy witryny sieci Web) nowego konta. 

### <a name="move-data-to-the-new-storage-account"></a>Przenoszenie danych na nowe konto magazynu

Oto kilka sposobów przenoszenia danych.

:heavy_check_mark: **Eksplorator usługi Azure Storage**

  Jest łatwy w użyciu i nadaje się do małych zestawów danych. Można skopiować kontenery i udziały plików, a następnie wkleić je do konta docelowego.

  Zobacz [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/);

:heavy_check_mark: **AzCopy**

  Jest to preferowane podejście. Jest zoptymalizowany pod kątem wydajności.  Jednym ze sposobów, że jest to szybsze, jest to, że dane są kopiowane bezpośrednio między serwerami magazynu, więc AzCopy nie korzysta z przepustowości sieci komputera. Użyj AzCopy w wierszu polecenia lub jako część skryptu niestandardowego.

  Zobacz [Wprowadzenie do AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

:heavy_check_mark: **Fabryka danych platformy Azure** 

  Tego narzędzia należy używać tylko wtedy, gdy potrzebujesz funkcji, które nie są obsługiwane w bieżącej wersji programu AzCopy. Na przykład w bieżącej wersji AzCopy nie można kopiować obiektów blob między kontami, które mają hierarchiczną przestrzeń nazw. Również AzCopy nie zachowuje list kontroli dostępu do plików ani sygnatur czasowych plików (na przykład: tworzenie i modyfikowanie sygnatur czasowych). 

  Zobacz następujące linki:
  - [Kopiowanie danych do lub z magazynu obiektów Blob platformy Azure przy użyciu usługi Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
  - [Kopiowanie danych do lub z usługi Azure Data Lake Storage Gen2 przy użyciu usługi Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
  - [Kopiowanie danych z usługi Azure File Storage lub do niego przy użyciu usługi Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-file-storage)
  - [Kopiowanie danych do i z magazynu tabel platformy Azure przy użyciu usługi Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-table-storage)

---

## <a name="discard-or-clean-up"></a>Wyrzucić lub oczyścić

Po wdrożeniu, jeśli chcesz rozpocząć od nowa, można usunąć konto magazynu docelowego i powtórzyć kroki opisane w [sekcji Przygotowanie](#prepare) i [przenoszenie](#move) tego artykułu.

Aby zatwierdzić zmiany i zakończyć przenoszenie konta magazynu, usuń konto magazynu źródłowego.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Aby usunąć konto magazynu przy użyciu witryny Azure portal:

1. W witrynie Azure portal rozwiń menu po lewej stronie, aby otworzyć menu usług, a następnie wybierz pozycję **Konta magazynu,** aby wyświetlić listę kont magazynu.

2. Znajdź docelowe konto magazynu do usunięcia, a następnie kliknij prawym przyciskiem myszy przycisk **Więcej** (**...**) po prawej stronie aukcji.

3. Wybierz **pozycję Usuń**i potwierdź.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Aby usunąć grupę zasobów i skojarzone z nią zasoby, w tym nowe konto magazynu, użyj polecenia [Usuń konto AzStorageAccount:](/powershell/module/az.storage/remove-azstorageaccount)

```powershell
Remove-AzStorageAccount -ResourceGroupName  $resourceGroup -AccountName $storageAccount
```
---

## <a name="next-steps"></a>Następne kroki

W tym samouczku przeniesiono konto magazynu platformy Azure z jednego regionu do drugiego i oczyściłeś zasoby źródłowe.  Aby dowiedzieć się więcej na temat przenoszenia zasobów między regionami i odzyskiwania po awarii na platformie Azure, zobacz:


- [Przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Przenoszenie maszyn wirtualnych platformy Azure do innego regionu](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
