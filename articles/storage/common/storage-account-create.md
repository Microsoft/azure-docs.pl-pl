---
title: Tworzenie konta magazynu
titleSuffix: Azure Storage
description: Dowiedz się, jak utworzyć konto magazynu do przechowywania obiektów blob, plików, kolejek i tabel. Konto usługi Azure Storage zapewnia unikatową przestrzeń nazw w Microsoft Azure do odczytywania i zapisywania danych.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/11/2021
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: b8f5932985f90ce042d7b0df0d01e7c685098670
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104576598"
---
# <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Konto usługi Azure Storage zawiera wszystkie obiekty danych usługi Azure Storage: obiektów blob, plików, kolejek, tabel i dysków. Konto magazynu zapewnia unikatową przestrzeń nazw dla danych usługi Azure Storage, która jest dostępna z dowolnego miejsca na świecie za pośrednictwem protokołu HTTP lub HTTPS. Dane na koncie usługi Azure Storage są trwałe i wysoce dostępne, bezpieczne i skalowalne.

W tym artykule z tego artykułu dowiesz się, jak utworzyć konto magazynu przy użyciu [Azure Portal](https://portal.azure.com/), [Azure PowerShell](/powershell/azure/), [interfejsu wiersza polecenia platformy Azure](/cli/azure)lub [szablonu Azure Resource Manager](../../azure-resource-manager/management/overview.md).  

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

# <a name="portal"></a>[Portal](#tab/azure-portal)

Brak.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Aby utworzyć konto usługi Azure Storage przy użyciu programu PowerShell, upewnij się, że zainstalowano moduł Azure PowerShell module AZ w wersji 0,7 lub nowszej. Aby uzyskać więcej informacji, zobacz [wprowadzenie do Azure PowerShell AZ module](/powershell/azure/new-azureps-module-az).

Aby znaleźć bieżącą wersję, uruchom następujące polecenie:

```powershell
Get-InstalledModule -Name "Az"
```

Aby zainstalować lub uaktualnić Azure PowerShell, zobacz [install Azure PowerShell module](/powershell/azure/install-Az-ps).

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Możesz zalogować się do platformy Azure i uruchamiać polecenia interfejsu wiersza poleceń platformy Azure na jeden z dwóch sposobów:

- Poleceń interfejsu wiersza polecenia można uruchomić z poziomu Azure Portal w Azure Cloud Shell.
- Interfejs wiersza polecenia można zainstalować i uruchomić polecenie CLI lokalnie.

### <a name="use-azure-cloud-shell"></a>Używanie usługi Azure Cloud Shell

Usługa Azure Cloud Shell jest bezpłatną powłoką Bash, którą można uruchamiać bezpośrednio w witrynie Azure Portal. Interfejs wiersza polecenia platformy Azure jest wstępnie zainstalowany i skonfigurowany do użycia z Twoim kontem. Kliknij przycisk **Cloud Shell** w menu w prawym górnym rogu Azure Portal:

[![Cloud Shell](./media/storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

Przycisk uruchamia interaktywną powłokę, której można użyć, aby wykonać kroki opisane w tym artykule:

[![Zrzut ekranu przedstawiający okno Cloud Shell w portalu](./media/storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>Instalowanie interfejsu wiersza polecenia lokalnie

Interfejs wiersza polecenia platformy Azure możesz również zainstalować i używać lokalnie. Przykłady w tym artykule wymagają interfejsu wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Uruchom `az --version` , aby znaleźć zainstalowaną wersję. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

# <a name="template"></a>[Szablon](#tab/template)

Brak.

---

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

# <a name="portal"></a>[Portal](#tab/azure-portal)

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Zaloguj się do subskrypcji platformy Azure za pomocą `Connect-AzAccount` polecenia i postępuj zgodnie z instrukcjami wyświetlanymi na ekranie w celu uwierzytelnienia.

```powershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby uruchomić Azure Cloud Shell, zaloguj się do [Azure Portal](https://portal.azure.com).

Aby zalogować się do lokalnej instalacji interfejsu wiersza polecenia, uruchom polecenie [AZ login](/cli/azure/reference-index#az-login) :

```azurecli-interactive
az login
```

# <a name="template"></a>[Szablon](#tab/template)

Nie dotyczy

---

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Każde konto magazynu musi należeć do grupy zasobów platformy Azure. Grupa zasobów to logiczny kontener przeznaczony do grupowania usług platformy Azure. Podczas tworzenia konta magazynu masz możliwość utworzenia nowej grupy zasobów lub użycia istniejącej grupy zasobów. W tym artykule pokazano, jak utworzyć nową grupę zasobów.

Konto magazynu **ogólnego przeznaczenia, wersja 2** zapewnia dostęp do wszystkich usług magazynu Azure Storage: obiektów blob, plików, kolejek, tabel i dysków. Kroki opisane tutaj tworzą konto magazynu ogólnego przeznaczenia w wersji 2, ale kroki tworzenia dowolnego typu konta magazynu są podobne. Aby uzyskać więcej informacji na temat typów kont magazynu i innych ustawień konta magazynu, zobacz [Azure storage account overview (Omówienie kont usługi Azure Storage)](storage-account-overview.md).

# <a name="portal"></a>[Portal](#tab/azure-portal)

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Najpierw użyj polecenia [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) w programie PowerShell, aby utworzyć nową grupę zasobów:

```powershell
# put resource group in a variable so you can use the same group name going forward,
# without hard-coding it repeatedly
$resourceGroup = "storage-resource-group"
$location = "westus"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

Jeśli nie masz pewności, który region należy określić dla parametru `-Location`, za pomocą polecenia [Get-AzLocation](/powershell/module/az.resources/get-azlocation) możesz pobrać listę obsługiwanych regionów dla swojej subskrypcji:

```powershell
Get-AzLocation | select Location
```

Następnie utwórz konto magazynu ogólnego przeznaczenia w wersji 2 z magazynem Geograficznie nadmiarowy z dostępem do odczytu (RA-GRS) za pomocą polecenia [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount) . Pamiętaj, że nazwa konta magazynu musi być unikatowa w obrębie platformy Azure, więc Zastąp wartość symbolu zastępczego w nawiasach własnym unikatowymi wartościami:

```powershell
New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name <account-name> `
  -Location $location `
  -SkuName Standard_RAGRS `
  -Kind StorageV2
```

> [!IMPORTANT]
> Jeśli planujesz używać [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/), Dołącz `-EnableHierarchicalNamespace $True` do tej listy parametrów.

Aby utworzyć konto magazynu ogólnego przeznaczenia w wersji 2 z inną opcją replikacji, należy zastąpić żądaną wartość w poniższej tabeli dla parametru **SkuName** .

|Opcja replikacji  |Parametr SkuName  |
|---------|---------|
|Magazyn lokalnie nadmiarowy (LRS)     |Standard_LRS         |
|Magazyn strefowo nadmiarowy (ZRS)     |Standard_ZRS         |
|Magazyn geograficznie nadmiarowy (GRS)     |Standard_GRS         |
|Magazyn geograficznie nadmiarowy dostępny do odczytu (GRS)     |Standard_RAGRS         |
|Magazyn geograficznie i strefowo nadmiarowy (GZRS)    |Standard_GZRS         |
|Magazyn strefowo i geograficznie nadmiarowy z dostępem do odczytu (RA-GZRS)    |Standard_RAGZRS         |

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Najpierw użyj polecenia [az group create](/cli/azure/group#az_group_create) interfejsu wiersza polecenia platformy Azure, aby utworzyć nową grupę zasobów.

```azurecli-interactive
az group create \
    --name storage-resource-group \
    --location westus
```

Jeśli nie masz pewności, który region należy określić dla parametru `--location`, za pomocą polecenia [az account list-locations](/cli/azure/account#az_account_list) możesz pobrać listę obsługiwanych regionów dla swojej subskrypcji.

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

Następnie utwórz konto magazynu ogólnego przeznaczenia w wersji 2 z magazynem geograficznie nadmiarowym z dostępem do odczytu przy użyciu polecenia [AZ Storage account Create](/cli/azure/storage/account#az_storage_account_create) . Pamiętaj, że nazwa konta magazynu musi być unikatowa w obrębie platformy Azure, więc Zastąp wartość symbolu zastępczego w nawiasach własnym unikatowymi wartościami:

```azurecli-interactive
az storage account create \
    --name <account-name> \
    --resource-group storage-resource-group \
    --location westus \
    --sku Standard_RAGRS \
    --kind StorageV2
```

> [!IMPORTANT]
> Jeśli planujesz używać [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/), Dołącz `--enable-hierarchical-namespace true` do tej listy parametrów.

Aby utworzyć konto magazynu ogólnego przeznaczenia w wersji 2 z inną opcją replikacji, należy zastąpić żądaną wartość w poniższej tabeli dla parametru **SKU** .

|Opcja replikacji  |parametr sku  |
|---------|---------|
|Magazyn lokalnie nadmiarowy (LRS)     |Standard_LRS         |
|Magazyn strefowo nadmiarowy (ZRS)     |Standard_ZRS         |
|Magazyn geograficznie nadmiarowy (GRS)     |Standard_GRS         |
|Magazyn geograficznie nadmiarowy dostępny do odczytu (GRS)     |Standard_RAGRS         |
|Magazyn geograficznie i strefowo nadmiarowy (GZRS)    |Standard_GZRS         |
|Magazyn strefowo i geograficznie nadmiarowy z dostępem do odczytu (RA-GZRS)    |Standard_RAGZRS         |

# <a name="template"></a>[Szablon](#tab/template)

Aby wdrożyć szablon Menedżer zasobów w celu utworzenia konta magazynu, można użyć Azure PowerShell lub interfejsu wiersza polecenia platformy Azure. Szablon używany w tym artykule z [przewodnikiem pochodzi z Azure Resource Manager szablonów szybkiego startu](https://azure.microsoft.com/resources/templates/101-storage-account-create/). Aby uruchomić skrypty, wybierz opcję **Wypróbuj** , aby otworzyć Azure Cloud Shell. Aby wkleić skrypt, kliknij prawym przyciskiem myszy powłokę, a następnie wybierz polecenie **Wklej**.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

> [!NOTE]
> Ten szablon służy tylko jako przykład. Istnieje wiele ustawień konta magazynu, które nie są skonfigurowane jako część tego szablonu. Na przykład jeśli chcesz użyć [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/), zmodyfikuj ten szablon, ustawiając `isHnsEnabledad` Właściwość `StorageAccountPropertiesCreateParameters` obiektu na `true` . 

Aby dowiedzieć się, jak zmodyfikować ten szablon lub utworzyć nowe, zobacz:

- [Dokumentacja Azure Resource Manager](../../azure-resource-manager/index.yml).
- [Odwołanie do szablonu konta magazynu](/azure/templates/microsoft.storage/allversions).
- [Dodatkowe przykłady szablonów kont magazynu](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Storage).

---

## <a name="delete-a-storage-account"></a>Usuwanie konta magazynu

Usunięcie konta magazynu spowoduje usunięcie całego konta, w tym wszystkich danych na koncie, i nie może zostać cofnięte.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Przejdź do konta magazynu w [Azure Portal](https://portal.azure.com).
1. Kliknij polecenie **Usuń**.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Aby usunąć konto magazynu, użyj polecenia [Remove-AzStorageAccount](/powershell/module/az.storage/remove-azstorageaccount) :

```powershell
Remove-AzStorageAccount -Name <storage-account> -ResourceGroupName <resource-group>
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby usunąć konto magazynu, użyj polecenia [AZ Storage account Delete](/cli/azure/storage/account#az-storage-account-delete) :

```azurecli-interactive
az storage account delete --name <storage-account> --resource-group <resource-group>
```

# <a name="template"></a>[Szablon](#tab/template)

Aby usunąć konto magazynu, użyj Azure PowerShell lub interfejsu wiersza polecenia platformy Azure.

```azurepowershell-interactive
$storageResourceGroupName = Read-Host -Prompt "Enter the resource group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"
Remove-AzStorageAccount -Name $storageAccountName -ResourceGroupName $storageResourceGroupName
```

```azurecli-interactive
echo "Enter the resource group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az storage account delete --name storageAccountName --resource-group resourceGroupName
```

---

Alternatywnie można usunąć grupę zasobów, która spowoduje usunięcie konta magazynu i innych zasobów w tej grupie zasobów. Aby uzyskać więcej informacji na temat usuwania grupy zasobów, zobacz [Usuwanie grupy zasobów i zasobów](../../azure-resource-manager/management/delete-resource-group.md).

> [!WARNING]
> Nie można przywrócić usuniętego konta magazynu ani odzyskać żadnej zawartości znajdującej się na koncie przed usunięciem. Zanim usuniesz konto, wykonaj kopię zapasową wszystkich danych, które chcesz zapisać. Dotyczy to również wszystkich zasobów na koncie — po usunięciu obiektu Blob, tabeli, kolejki lub pliku elementy te są trwale usuwane.
>
> Podczas próby usunięcia konta magazynu skojarzonego z maszyną wirtualną platformy Azure może zostać wyświetlony komunikat o błędzie dotyczący wciąż używanego konta magazynu. Aby uzyskać pomoc w rozwiązywaniu tego błędu, zobacz [Rozwiązywanie problemów podczas usuwania kont magazynu](../../virtual-machines/troubleshooting/index.yml).

## <a name="next-steps"></a>Następne kroki

- [Omówienie kont magazynu](storage-account-overview.md)
- [Uaktualnienie konta magazynu ogólnego przeznaczenia do wersji 2](storage-account-upgrade.md)
- [Przenoszenie konta usługi Azure Storage do innego regionu](storage-account-move.md)
- [Odzyskiwanie usuniętego konta magazynu](storage-account-recover.md)
