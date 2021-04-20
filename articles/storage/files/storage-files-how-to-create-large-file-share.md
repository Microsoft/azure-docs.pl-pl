---
title: Włączanie i tworzenie dużych udziałów plików — Azure Files
description: Z tego artykułu dowiesz się, jak włączyć i utworzyć duże udziały plików.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 05/29/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 20f9aaf73fe0cb30b136254d57e6c9b960c16af4
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717004"
---
# <a name="enable-and-create-large-file-shares"></a>Włączanie i tworzenie dużych udziałów plików

Udziały plików platformy Azure można skalować w górę do 100 TiB po włączeniu dużych udziałów plików na koncie magazynu. Włączenie dużych udziałów plików może również zwiększyć limity liczby IOPS i przepływności udziału plików. To skalowanie można również włączyć na istniejących kontach magazynu dla istniejących i nowych udziałów plików. Aby uzyskać szczegółowe informacje na temat różnic w wydajności, zobacz [Udział plików i cele skalowania plików.](storage-files-scale-targets.md#azure-files-scale-targets)

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).
- Jeśli zamierzasz używać interfejsu wiersza polecenia platformy Azure, [zainstaluj najnowszą wersję .](/cli/azure/install-azure-cli)
- Jeśli zamierzasz używać modułu Azure PowerShell, [zainstaluj najnowszą wersję .](/powershell/azure/install-az-ps)

## <a name="restrictions"></a>Ograniczenia

Obecnie na kontach magazynu z włączonymi dużymi udziałami plików można używać tylko magazynu lokalnie nadmiarowego (LRS) lub magazynu strefowo nadmiarowego (ZRS). Nie można używać magazynu geograficznie i strefowo nadmiarowego (GZRS), magazynu geograficznie nadmiarowego (GRS), magazynu geograficznie nadmiarowego dostępnego do odczytu (RA-GRS) ani magazynu geograficznie strefowo nadmiarowego dostępnego do odczytu (RA-GZRS).

Włączenie dużych udziałów plików na koncie jest procesem nieodwracalnym. Po włączeniu tej funkcji nie będzie można przekonwertować konta na GZRS, GRS, RA-GRS ani RA-GZRS.

## <a name="create-a-new-storage-account"></a>Tworzenie nowego konta magazynu

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. W Azure Portal wybierz pozycję **Wszystkie usługi.** 
1. Na liście zasobów wprowadź wartość **Konta magazynu.** Podczas wpisywania lista filtruje się na podstawie danych wejściowych. Wybierz **pozycję Konta magazynu.**
1. W **wyświetlonym bloku Konta** magazynu wybierz pozycję **+ Nowy.**
1. W bloku podstawowe wypełnij wybrane opcje zgodnie z swoimi potrzebyami.
1. Upewnij się, że **dla ustawienia Wydajność** ustawiono wartość **Standardowa.**
1. Ustaw **nadmiarowość na** magazyn **lokalnie nadmiarowy** lub **magazyn strefowo nadmiarowy.**
1. Wybierz blok **Zaawansowane,** a  następnie wybierz przycisk Włączone po prawej stronie opcji **Duże udziały plików.**
1. Wybierz pozycję **Przejrzyj i utwórz**, aby przejrzeć ustawienia konta magazynu i utworzyć konto.
1. Wybierz przycisk **Utwórz**.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Najpierw zainstaluj [najnowszą wersję interfejsu wiersza](/cli/azure/install-azure-cli) polecenia platformy Azure, aby włączyć duże udziały plików.

Aby utworzyć konto magazynu z włączonymi dużymi udziałami plików, użyj następującego polecenia. Zastąp `<yourStorageAccountName>` , `<yourResourceGroup>` i `<yourDesiredRegion>` swoimi informacjami.

```azurecli-interactive
## This command creates a large file share–enabled account. It will not support GZRS, GRS, RA-GRS, or RA-GZRS.
az storage account create --name <yourStorageAccountName> -g <yourResourceGroup> -l <yourDesiredRegion> --sku Standard_LRS --kind StorageV2 --enable-large-file-share
```

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Najpierw zainstaluj [najnowszą wersję programu PowerShell,](/powershell/azure/install-az-ps) aby włączyć duże udziały plików.

Aby utworzyć konto magazynu z włączonymi dużymi udziałami plików, użyj następującego polecenia. Zastąp `<yourStorageAccountName>` , `<yourResourceGroup>` i `<yourDesiredRegion>` swoimi informacjami.

```powershell
## This command creates a large file share–enabled account. It will not support GZRS, GRS, RA-GRS, or RA-GZRS.
New-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -Location <yourDesiredRegion> -SkuName Standard_LRS -EnableLargeFileShare;
```
---

## <a name="enable-large-files-shares-on-an-existing-account"></a>Włączanie udziałów dużych plików na istniejącym koncie

Można również włączyć duże udziały plików na istniejących kontach. Jeśli włączysz duże udziały plików, nie będzie można konwertować na GZRS, GRS, RA-GRS ani RA-GZRS. Włączenie dużych udziałów plików jest nieodwracalne na tym koncie magazynu.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Otwórz [Azure Portal](https://portal.azure.com)i przejdź do konta magazynu, na którym chcesz włączyć duże udziały plików.
1. Otwórz konto magazynu i wybierz **pozycję Udziały plików.**
1. Wybierz **pozycję Włączone** w dużych **udziałach plików,** a następnie wybierz pozycję **Zapisz.**
1. Wybierz **pozycję Przegląd** i wybierz pozycję **Odśwież.**
1. Wybierz **pozycję Udostępnij pojemność,** a następnie wybierz **pozycję 100 TiB** i zapisz **.**

    :::image type="content" source="media/storage-files-how-to-create-large-file-share/files-enable-large-file-share-existing-account.png" alt-text="Zrzut ekranu przedstawiający blok udziałów plików konta usługi Azure Storage z wyróżnione 100 tys. udziałów.":::

Masz teraz włączone duże udziały plików na koncie magazynu. Następnie należy [zaktualizować limit przydziału istniejącego udziału,](#expand-existing-file-shares) aby skorzystać ze zwiększonej pojemności i skali.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby włączyć duże udziały plików na istniejącym koncie, użyj następującego polecenia. Zastąp `<yourStorageAccountName>` `<yourResourceGroup>` i swoimi informacjami.

```azurecli-interactive
az storage account update --name <yourStorageAccountName> -g <yourResourceGroup> --enable-large-file-share
```

Masz teraz włączone duże udziały plików na koncie magazynu. Następnie należy [zaktualizować limit przydziału istniejącego udziału,](#expand-existing-file-shares) aby skorzystać ze zwiększonej pojemności i skali.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Aby włączyć duże udziały plików na istniejącym koncie, użyj następującego polecenia. Zastąp `<yourStorageAccountName>` `<yourResourceGroup>` i swoimi informacjami.

```powershell
Set-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -EnableLargeFileShare
```

Masz teraz włączone duże udziały plików na koncie magazynu. Następnie należy [zaktualizować limit przydziału istniejącego udziału,](#expand-existing-file-shares) aby skorzystać ze zwiększonej pojemności i skali.

---

## <a name="create-a-large-file-share"></a>Tworzenie dużego udziału plików

Po włączeniu dużych udziałów plików na koncie magazynu można tworzyć udziały plików na tym koncie z wyższymi limitami przydziału. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

Tworzenie dużego udziału plików jest niemal identyczne z tworzeniem standardowego udziału plików. Główna różnica polega na tym, że można ustawić limit przydziału do 100 TiB.

1. Na koncie magazynu wybierz pozycję **Udziały plików.**
1. Wybierz **pozycję + Udział plików.**
1. Wprowadź nazwę udziału plików. Możesz również ustawić rozmiar przydziału do 100 TiB. Następnie wybierz pozycję **Utwórz**. 

![Interfejs Azure Portal z polami Nazwa i Limit przydziału](media/storage-files-how-to-create-large-file-share/large-file-shares-create-share.png)

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby utworzyć duży udział plików, użyj następującego polecenia. Zastąp `<yourStorageAccountName>` , `<yourStorageAccountKey>` i `<yourFileShareName>` swoimi informacjami.

```azurecli-interactive
az storage share create --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName>
```

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Aby utworzyć duży udział plików, użyj następującego polecenia. Zastąp `<YourStorageAccountName>` , `<YourStorageAccountKey>` i `<YourStorageAccountFileShareName>` swoimi informacjami.

```powershell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
New-AzStorageShare -Name $shareName -Context $ctx
```
---

## <a name="expand-existing-file-shares"></a>Rozszerzanie istniejących udziałów plików

Po włączeniu dużych udziałów plików na koncie magazynu można również rozszerzyć istniejące udziały plików na tym koncie do wyższego limitu przydziału. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Na koncie magazynu wybierz pozycję **Udziały plików.**
1. Kliknij prawym przyciskiem myszy udział plików, a następnie wybierz pozycję **Limit przydziału.**
1. Wprowadź nowy rozmiar, a następnie wybierz przycisk **OK.**

![Interfejs Azure Portal użytkownika z limitem przydziału istniejących udziałów plików](media/storage-files-how-to-create-large-file-share/update-large-file-share-quota.png)

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby ustawić maksymalny rozmiar limitu przydziału, użyj następującego polecenia. Zastąp `<yourStorageAccountName>` , `<yourStorageAccountKey>` i `<yourFileShareName>` swoimi informacjami.

```azurecli-interactive
az storage share update --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName> --quota 102400
```

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Aby ustawić maksymalny rozmiar limitu przydziału, użyj następującego polecenia. Zastąp `<YourStorageAccountName>` , `<YourStorageAccountKey>` i `<YourStorageAccountFileShareName>` swoimi informacjami.

```powershell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
# update quota
Set-AzStorageShareQuota -ShareName $shareName -Context $ctx -Quota 102400
```
---

## <a name="next-steps"></a>Następne kroki

* [Łączenie i montowania udziału plików w systemie Windows](storage-how-to-use-files-windows.md)
* [Nawiązywanie połączenia i montowania udziału plików w systemie Linux](storage-how-to-use-files-linux.md)
* [Łączenie i montowania udziału plików w systemie macOS](storage-how-to-use-files-mac.md)