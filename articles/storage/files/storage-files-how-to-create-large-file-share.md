---
title: Włączanie i tworzenie dużych udziałów plików — Azure Files
description: Z tego artykułu dowiesz się, jak włączyć i utworzyć duże udziały plików.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/20/2021
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: a53f964020583c41e2400d97ad244bacd33813bc
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107818264"
---
# <a name="enable-and-create-large-file-shares"></a>Włączanie i tworzenie dużych udziałów plików
Udziały plików platformy Azure można skalować do 100 TiB po włączeniu dużych udziałów plików na koncie magazynu. Włączenie dużych udziałów plików może również zwiększyć limity liczby IOPS i przepływności udziału plików. Możesz również włączyć to skalowanie na istniejących kontach magazynu dla istniejących i nowych udziałów plików. Aby uzyskać szczegółowe informacje na temat różnic w wydajności, zobacz [udział plików i cele skalowania plików.](storage-files-scale-targets.md#azure-files-scale-targets)

## <a name="prerequisites"></a>Wymagania wstępne
- Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).
- Jeśli zamierzasz używać interfejsu wiersza polecenia platformy Azure, [zainstaluj najnowszą wersję .](/cli/azure/install-azure-cli)
- Jeśli zamierzasz używać modułu Azure PowerShell, [zainstaluj najnowszą wersję](/powershell/azure/install-az-ps).

## <a name="restrictions"></a>Ograniczenia
Na razie można używać tylko magazynu lokalnie nadmiarowego (LRS) lub magazynu strefowo nadmiarowego (ZRS) na kontach magazynu z włączonymi dużymi udziałami plików. Nie można używać magazynu geograficznie strefowo nadmiarowego (GZRS), magazynu geograficznie nadmiarowego (GRS), magazynu geograficznie nadmiarowego dostępnego do odczytu (RA-GRS) ani magazynu geograficznie strefowo nadmiarowego dostępnego do odczytu (RA-GZRS).

Włączanie dużych udziałów plików na koncie jest procesem nieodwracalnym. Po włączeniu tej funkcji nie będzie można przekonwertować konta na GZRS, GRS, RA-GRS ani RA-GZRS.

## <a name="create-a-new-storage-account"></a>Tworzenie nowego konta magazynu

# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. W Azure Portal wybierz pozycję **Wszystkie usługi.** 
1. Na liście zasobów wprowadź wartość **Konta magazynu.** Podczas wpisywania lista filtruje się na podstawie danych wejściowych. Wybierz **pozycję Konta magazynu.**
1. W **wyświetlonym bloku Konta** magazynu wybierz pozycję **+ Nowy.**
1. W bloku podstawowe wypełnij wybrane opcje zgodnie z swoimi potrzebyami.
1. Upewnij się, **że dla ustawienia Wydajność** ustawiono wartość **Standardowa.**
1. Ustaw **nadmiarowość na** magazyn **lokalnie nadmiarowy** lub **Magazyn strefowo nadmiarowy.**
1. Wybierz blok **Zaawansowane,** a następnie wybierz **przycisk** Włączone po prawej stronie opcji **Duże udziały plików.**
1. Wybierz pozycję **Przejrzyj i utwórz**, aby przejrzeć ustawienia konta magazynu i utworzyć konto.
1. Wybierz przycisk **Utwórz**.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)
Aby utworzyć konto magazynu z włączonymi dużymi udziałami plików, użyj następującego polecenia. Zastąp `<yourStorageAccountName>` , `<yourResourceGroup>` i `<yourDesiredRegion>` swoimi informacjami.

```powershell
# This command creates a large file share–enabled account. It will not support changing the 
# redundancy to GRS, GZRS, RA-GRS, or RA-GZRS.
New-AzStorageAccount `
    -ResourceGroupName <yourResourceGroup> `
    -Name <yourStorageAccountName> `
    -Location <yourDesiredRegion> `
    -SkuName Standard_LRS `
    -EnableLargeFileShare
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
Aby utworzyć konto magazynu z włączonymi dużymi udziałami plików, użyj następującego polecenia. Zastąp `<yourStorageAccountName>` , `<yourResourceGroup>` i `<yourDesiredRegion>` swoimi informacjami.

```azurecli-interactive
# This command creates a large file share–enabled account. It will not support changing the 
# redundancy to GRS, GZRS, RA-GRS, or RA-GZRS.
az storage account create \
    --name <yourStorageAccountName> \
    -g <yourResourceGroup> \
    -l <yourDesiredRegion> \
    --sku Standard_LRS \
    --kind StorageV2 \
    --enable-large-file-share
```

---

Po utworzeniu konta magazynu z włączonym dużym udziałem plików można utworzyć udział plików, który wykorzystuje limity dużej pojemności i skali. Aby uzyskać więcej informacji na temat tworzenia kont magazynu i udziałów plików platformy Azure, zobacz [Tworzenie udziału plików platformy Azure.](storage-how-to-create-file-share.md)

## <a name="enable-large-files-shares-on-an-existing-account"></a>Włączanie dużych udziałów plików na istniejącym koncie
Możesz również włączyć duże udziały plików na istniejących kontach magazynu LRS i ZRS. Jeśli masz konto GRS, GZRS, RA-GRS lub RA-GZRS, przed przystąpieniem należy przekonwertować je na konto magazynu LRS.

# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Otwórz [Azure Portal](https://portal.azure.com)i przejdź do konta magazynu, na którym chcesz włączyć duże udziały plików.
1. Otwórz konto magazynu i wybierz **pozycję Udziały plików.**
1. Wybierz **pozycję Włączone** w dużych **udziałach plików,** a następnie wybierz pozycję **Zapisz.**
1. Wybierz **pozycję Przegląd** i wybierz pozycję **Odśwież.**
1. Wybierz **pozycję Udostępnij pojemność,** a **następnie wybierz pozycję 100 TiB** i zapisz **.**

    :::image type="content" source="media/storage-files-how-to-create-large-file-share/files-enable-large-file-share-existing-account.png" alt-text="Zrzut ekranu przedstawiający blok udziały plików konta usługi Azure Storage z wyróżnionymi 100 udziałami ib.":::

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)
Aby włączyć duże udziały plików na istniejącym koncie, użyj następującego polecenia. Zastąp `<yourStorageAccountName>` i `<yourResourceGroup>` swoimi informacjami.

```powershell
Set-AzStorageAccount `
    -ResourceGroupName <yourResourceGroup> `
    -Name <yourStorageAccountName> `
    -EnableLargeFileShare
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
Aby włączyć duże udziały plików na istniejącym koncie, użyj następującego polecenia. Zastąp `<yourStorageAccountName>` `<yourResourceGroup>` i swoimi informacjami.

```azurecli-interactive
az storage account update --name <yourStorageAccountName> -g <yourResourceGroup> --enable-large-file-share
```

---

Masz teraz włączone duże udziały plików na koncie magazynu. Następnie należy [zaktualizować limit przydziału istniejącego udziału,](#expand-existing-file-shares) aby skorzystać ze zwiększonej pojemności i skali. 

> [!Important]  
> Istniejące udziały plików nie będą skalowane w górę do anonsowanych limitów dla dużych udziałów plików, chyba że limit przydziału został zmieniony.

## <a name="expand-existing-file-shares"></a>Rozszerzanie istniejących udziałów plików
Po włączeniu dużych udziałów plików na koncie magazynu należy rozszerzyć istniejące udziały plików na tym koncie magazynu, aby skorzystać ze zwiększonej pojemności i skali. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Na koncie magazynu wybierz pozycję **Udziały plików.**
1. Kliknij prawym przyciskiem myszy udział plików, a następnie wybierz pozycję **Limit przydziału.**
1. Wprowadź nowy rozmiar, a następnie wybierz przycisk **OK.**

![Interfejs Azure Portal z limitem przydziału istniejących udziałów plików](media/storage-files-how-to-create-large-file-share/update-large-file-share-quota.png)

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)
Aby ustawić maksymalny rozmiar limitu przydziału, użyj następującego polecenia. Zastąp `<YourResourceGroupName>` , `<YourStorageAccountName>` i `<YourStorageAccountFileShareName>` swoimi informacjami.

```powershell
$resourceGroupName = "<YourResourceGroupName>"
$storageAccountName = "<YourStorageAccountName>"
$shareName="<YourStorageAccountFileShareName>"

# update quota
Set-AzRmStorageShare `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -Name $shareName `
    -QuotaGiB 102400
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
Aby ustawić maksymalny rozmiar limitu przydziału, użyj następującego polecenia. Zastąp `<yourResourceGroupName>` , `<yourStorageAccountName>` i `<yourFileShareName>` swoimi informacjami.

```azurecli-interactive
az storage share-rm update \
    --resource-group <yourResourceGroupName> \
    --storage-account <yourStorageAccountName> \
    --name <yourFileShareName> \
    --quota 102400
```

---

## <a name="next-steps"></a>Następne kroki
* [Łączenie i montowania udziału plików w systemie Windows](storage-how-to-use-files-windows.md)
* [Łączenie i montowania udziału plików w systemie Linux](storage-how-to-use-files-linux.md)
* [Łączenie i montowania udziału plików w systemie macOS](storage-how-to-use-files-mac.md)