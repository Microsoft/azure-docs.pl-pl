---
title: Tworzenie udziału plików platformy Azure w warstwie Premium
description: Dowiedz się, jak utworzyć udział plików platformy Azure w warstwie Premium przy użyciu modułu Azure Portal, Azure PowerShell module lub interfejsu wiersza polecenia platformy Azure.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 08/26/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 1ec8e4a945f8b8277d05c11bf3673d2e4ab15f9a
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2020
ms.locfileid: "94626796"
---
# <a name="how-to-create-an-azure-premium-file-share"></a>Jak utworzyć udział plików platformy Azure w warstwie Premium

Udziały plików w warstwie Premium są oferowane na nośnikach magazynowania SSD i są przydatne w przypadku obciążeń intensywnie korzystających z operacji we/wy, w tym hostingu baz danych i obliczeń o wysokiej wydajności (HPC). Udziały plików w warstwie Premium są hostowane w specjalnym rodzaju koncie magazynu o nazwie konto FileStorage. Udziały plików w warstwie Premium są przeznaczone dla aplikacji o wysokiej wydajności i skalowania w przedsiębiorstwie, zapewniając spójne małe opóźnienia, duże liczby operacji we/wy i duże przepływność.

W tym artykule opisano sposób tworzenia nowego typu konta przy użyciu [Azure Portal](https://portal.azure.com/), Azure PowerShell module i interfejsu wiersza polecenia platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).
- Jeśli zamierzasz korzystać z interfejsu wiersza polecenia platformy Azure, [Zainstaluj najnowszą wersję](/cli/azure/install-azure-cli?view=azure-cli-latest).
- Jeśli zamierzasz użyć modułu Azure PowerShell, [Zainstaluj najnowszą wersję](/powershell/azure/install-az-ps?view=azps-4.6.0).

## <a name="create-a-filestorage-storage-account"></a>Utwórz konto magazynu FileStorage

Każde konto magazynu musi należeć do grupy zasobów platformy Azure. Grupa zasobów to logiczny kontener przeznaczony do grupowania usług platformy Azure. Podczas tworzenia konta magazynu masz możliwość utworzenia nowej grupy zasobów lub użycia istniejącej grupy zasobów. Udziały plików w warstwie Premium wymagają konta FileStorage.

# <a name="portal"></a>[Portal](#tab/azure-portal)

### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

Teraz możesz przystąpić do utworzenia konta magazynu.

1. W Azure Portal wybierz pozycję **konta magazynu** w menu po lewej stronie.

    ![Strona główna Azure Portal Wybieranie konta magazynu](media/storage-how-to-create-premium-fileshare/azure-portal-storage-accounts.png)

1. W oknie **Konta magazynu** , które zostanie wyświetlone, wybierz pozycję **Dodaj**.
1. Wybierz subskrypcję, w ramach której chcesz utworzyć konto magazynu.
1. W polu **Grupa zasobów** wybierz pozycję **Utwórz nową**. Wprowadź nazwę nowej grupy zasobów, jak pokazano na poniższej ilustracji.

1. Następnie wprowadź nazwę konta magazynu. Wybrana nazwa musi być unikatowa w obrębie całej platformy Azure. Ponadto nazwa musi mieć długość od 3 do 24 znaków i może zawierać tylko cyfry i małe litery.
1. Wybierz lokalizację konta magazynu lub użyj lokalizacji domyślnej.
1. W obszarze **wydajność** wybierz opcję **Premium**.

    Musisz wybrać opcję **Premium** for **FileStorage** , aby była dostępna opcja na liście rozwijanej **rodzaj konta** .

1. Wybierz pozycję **rodzaj konta** i wybierz pozycję **FileStorage**.
1. Dla opcji **replikacja** ustaw wartość domyślną **Magazyn lokalnie nadmiarowy (LRS)**.

    ![Jak utworzyć konto magazynu dla udziału plików w warstwie Premium](media/storage-how-to-create-premium-fileshare/create-filestorage-account.png)

1. Wybierz pozycję **Przejrzyj i utwórz** , aby przejrzeć ustawienia konta magazynu i utworzyć konto.
1. Wybierz pozycję **Utwórz**.

Po utworzeniu zasobu konta magazynu przejdź do niego.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Użyj polecenia `Connect-AzAccount`, a następnie postępuj zgodnie z instrukcjami wyświetlanymi na ekranie w celu uwierzytelnienia.

```powershell
Connect-AzAccount
```

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Aby utworzyć nową grupę zasobów za pomocą programu PowerShell, użyj polecenia [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup):

```powershell
# put resource group in a variable so you can use the same group name going forward,
# without hardcoding it repeatedly
$resourceGroup = "storage-how-to-resource-group"
$location = "westus2"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

### <a name="create-a-filestorage-storage-account"></a>Utwórz konto magazynu FileStorage

Aby utworzyć konto magazynu FileStorage z poziomu programu PowerShell, użyj polecenia [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount) :

```powershell
$storageAcct = New-AzStorageAccount -ResourceGroupName $resourceGroup -Name "fileshowto" -SkuName "Premium_LRS" -Location "westus2" -Kind "FileStorage"
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby rozpocząć Azure Cloud Shell, zaloguj się do [Azure Portal](https://portal.azure.com).

Jeśli chcesz zalogować się do lokalnej instalacji interfejsu wiersza polecenia, upewnij się, że masz najnowszą wersję, a następnie zaloguj się:

```azurecli
az login
```

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Aby utworzyć nową grupę zasobów za pomocą interfejsu wiersza polecenia platformy Azure, użyj polecenia [az group create](/cli/azure/group).

```azurecli-interactive
az group create `
    --name files-howto-resource-group `
    --location westus2
```

### <a name="create-a-filestorage-storage-account"></a>Utwórz konto magazynu FileStorage

Aby utworzyć konto magazynu FileStorage z poziomu interfejsu wiersza polecenia platformy Azure, użyj polecenie [AZ Storage account Create](/cli/azure/storage/account) .

```azurecli-interactive
az storage account create `
    --name fileshowto `
    --resource-group files-howto-resource-group `
    --location westus `
    --sku Premium_LRS `
    --kind FileStorage
```

### <a name="get-the-storage-account-key"></a>Pobieranie klucza konta magazynu

Klucze konta magazynu kontrolują dostęp do zasobów na koncie magazynu, w tym artykule używamy klucza w celu utworzenia udziału plików w warstwie Premium. Klucze są tworzone automatycznie podczas tworzenia konta magazynu. Klucze konta magazynu możesz pobrać przy użyciu polecenia [az storage account keys list](/cli/azure/storage/account/keys):

```azurecli-interactive
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```
---

## <a name="create-a-premium-file-share"></a>Tworzenie udziału plików w warstwie Premium

Po utworzeniu konta FileStorage można utworzyć udział plików w warstwie Premium w ramach tego konta magazynu.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. W menu po lewej stronie konta magazynu przewiń do sekcji **Usługa plików** , a następnie wybierz pozycję **pliki**.
1. Wybierz pozycję **udział plików** , aby utworzyć udział plików w warstwie Premium.
1. Wprowadź nazwę i żądany przydział udziału plików, a następnie wybierz pozycję **Utwórz**.

> [!NOTE]
> Rozmiar udostępnianych udziałów jest określany przez przydział udziału, a udziały plików są rozliczane zgodnie z przydziałem. Aby uzyskać więcej informacji, odwiedź [stronę cennika](https://azure.microsoft.com/pricing/details/storage/files/).

   ![Tworzenie udziału plików w warstwie Premium](media/storage-how-to-create-premium-fileshare/create-premium-file-share.png)

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Aby utworzyć udział plików w warstwie Premium przy użyciu modułu Azure PowerShell, należy użyć polecenia cmdlet [New-AzStorageShare](/powershell/module/az.storage/New-AzStorageShare) .

> [!NOTE]
> Rozmiar udostępnianych udziałów jest określany przez przydział udziału, a udziały plików są rozliczane zgodnie z przydziałem. Aby uzyskać więcej informacji, odwiedź [stronę cennika](https://azure.microsoft.com/pricing/details/storage/files/).

```powershell
New-AzStorageShare `
   -Name myshare `
   -EnabledProtocol SMB `
   -Context $storageAcct.Context
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby utworzyć udział plików w warstwie Premium za pomocą interfejsu wiersza polecenia platformy Azure, użyj polecenie [AZ Storage Share Create](/cli/azure/storage/share) .

> [!NOTE]
> Rozmiar udostępnianych udziałów jest określany przez przydział udziału, a udziały plików są rozliczane zgodnie z przydziałem. Aby uzyskać więcej informacji, odwiedź [stronę cennika](https://azure.microsoft.com/pricing/details/storage/files/).

```azurecli-interactive
az storage share create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --enabled-protocol SMB \
    --name "myshare" 
```
---

## <a name="clean-up-resources"></a>Czyszczenie zasobów

# <a name="portal"></a>[Portal](#tab/azure-portal)

Jeśli chcesz wyczyścić zasoby utworzone w tym artykule, Usuń grupę zasobów. Usunięcie grupy zasobów powoduje również usunięcie skojarzonego konta magazynu oraz innych zasobów skojarzonych z tą grupą zasobów.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Jeśli chcesz wyczyścić zasoby utworzone w tym artykule, Usuń grupę zasobów. Usunięcie grupy zasobów powoduje również usunięcie skojarzonego konta magazynu oraz innych zasobów skojarzonych z tą grupą zasobów.

Aby usunąć grupę zasobów i skojarzone z nią zasoby, w tym nowe konto magazynu, użyj polecenia [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup): 

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Jeśli chcesz wyczyścić zasoby utworzone w tym artykule, Usuń grupę zasobów. Usunięcie grupy zasobów powoduje również usunięcie skojarzonego konta magazynu oraz innych zasobów skojarzonych z tą grupą zasobów.

Aby usunąć grupę zasobów i skojarzone z nią zasoby, w tym nowe konto magazynu, użyj polecenia [az group delete](/cli/azure/group).

```azurecli-interactive
az group delete --name myResourceGroup
```
---

## <a name="next-steps"></a>Następne kroki

W tym artykule opisano tworzenie udziału plików w warstwie Premium. Aby dowiedzieć się więcej o wydajności tej oferty, przejdź do sekcji warstwa wydajności przewodnika planowania.

> [!div class="nextstepaction"]
> [Warstwy udziałów plików](storage-files-planning.md#storage-tiers)