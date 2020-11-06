---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: c26ef5b857d7295b533079a70959f0f1ef1e8206
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2020
ms.locfileid: "93425095"
---
## <a name="create-supporting-azure-resources-for-your-function"></a>Tworzenie obsługi zasobów platformy Azure dla funkcji

Aby można było wdrożyć kod funkcji na platformie Azure, należy utworzyć trzy zasoby:

- Grupa zasobów, która jest kontenerem logicznym dla powiązanych zasobów.
- Konto magazynu, które zachowuje stan i inne informacje o projektach.
- Aplikacja funkcji, która zapewnia środowisko do wykonywania kodu funkcji. Aplikacja funkcji jest mapowana na projekt funkcji lokalnej i umożliwia grupowanie funkcji jako jednostki logicznej, co ułatwia zarządzanie, wdrażanie i udostępnianie zasobów.

Użyj następujących poleceń, aby utworzyć te elementy. Obsługiwane są zarówno interfejs wiersza polecenia platformy Azure, jak i program PowerShell.

1. Jeśli jeszcze tego nie zrobiono, zaloguj się do platformy Azure:

    # <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
    ```azurecli
    az login
    ```

    Polecenie [AZ login umożliwia zalogowanie](/cli/azure/reference-index#az_login) się do konta platformy Azure.

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell) 
    ```azurepowershell
    Connect-AzAccount
    ```

    Polecenie cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) umożliwia zalogowanie się do konta platformy Azure.

    ---

1. Utwórz grupę zasobów o nazwie `AzureFunctionsQuickstart-rg` w `westeurope` regionie:

    # <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
    
    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location westeurope
    ```
 
    Polecenie [AZ Group Create](/cli/azure/group#az_group_create) tworzy grupę zasobów. Zazwyczaj tworzysz grupę zasobów i zasoby w regionie, w którym wiesz, przy użyciu dostępnego regionu zwróconego przez `az account list-locations` polecenie.

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzResourceGroup -Name AzureFunctionsQuickstart-rg -Location westeurope
    ```

    Polecenie [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) tworzy grupę zasobów. Zazwyczaj tworzysz grupę zasobów i zasoby w regionie, w którym wiesz, przy użyciu dostępnego regionu zwróconego przez polecenie cmdlet [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation) .

    ---

1. Utwórz konto magazynu ogólnego przeznaczenia w grupie zasobów i regionie:

    # <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```

    Polecenie [AZ Storage account Create](/cli/azure/storage/account#az_storage_account_create) tworzy konto magazynu. 

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzStorageAccount -ResourceGroupName AzureFunctionsQuickstart-rg -Name <STORAGE_NAME> -SkuName Standard_LRS -Location westeurope
    ```

    Polecenie cmdlet [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) tworzy konto magazynu.

    ---

    W poprzednim przykładzie Zastąp `<STORAGE_NAME>` wartość nazwą, która jest odpowiednia dla Ciebie i unikatową w usłudze Azure Storage. Nazwy muszą zawierać od 3 do 24 znaków cyfry i małe litery. `Standard_LRS` Określa konto ogólnego przeznaczenia, które jest [obsługiwane przez funkcje](../articles/azure-functions/storage-considerations.md#storage-account-requirements).
    


