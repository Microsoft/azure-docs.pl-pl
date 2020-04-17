---
title: Włączanie i tworzenie dużych udziałów plików — usługi Azure Files
description: W tym artykule dowiesz się, jak włączyć i utworzyć duże udziały plików.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: bd7726d2bbf2830d18d78b5f0b0d7202b734124d
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537682"
---
# <a name="enable-and-create-large-file-shares"></a>Włączanie i tworzenie dużych udziałów plików

Po włączeniu dużych udziałów plików na koncie magazynu udziały plików można skalować do 100 TiB. Można włączyć to skalowanie na istniejących kontach magazynu dla istniejących udziałów plików.

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.
- Jeśli zamierzasz korzystać z interfejsu wiersza polecenia platformy Azure, [zainstaluj najnowszą wersję](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
- Jeśli zamierzasz korzystać z programu Azure PowerShell, [zainstaluj najnowszą wersję](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0).

## <a name="restrictions"></a>Ograniczenia

Na razie można używać tylko magazynu lokalnie nadmiarowego (LRS) lub magazynu nadmiarowego strefy (ZRS) na dużych kontach z włączonym udziałem plików. Nie można używać magazynu geograficzno-nadmiarowego (GZRS), magazynu geograficznie nadmiarowego (GRS) ani magazynu geograficznego dostępu do odczytu (RA-GRS).
Włączanie dużych udziałów plików na koncie jest procesem nieodwracalnym. Po włączeniu nie będzie można przekonwertować konta na GZRS, GRS lub RA-GRS.

## <a name="create-a-new-storage-account"></a>Tworzenie nowego konta magazynu

### <a name="portal"></a>Portal

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. W witrynie Azure portal wybierz pozycję **Wszystkie usługi**. 
1. Na liście zasobów wprowadź **wpisać konta magazynu**. Podczas pisania lista filtruje się na podstawie danych wejściowych. Wybierz pozycję **Konta magazynu**.
1. W wyświetlonym oknie **Konta magazynu** wybierz pozycję **Dodaj**.
1. Wybierz subskrypcję, której użyjesz do utworzenia konta magazynu.
1. W polu **Grupa zasobów** wybierz pozycję **Utwórz nową**. Wprowadź nazwę nowej grupy zasobów.

    ![Zrzut ekranu przedstawiający sposób tworzenia grupy zasobów w portalu](media/storage-files-how-to-create-large-file-share/create-large-file-share.png)

1. Następnie wprowadź nazwę konta magazynu. Nazwa musi być unikatowa na platformie Azure. Nazwa musi mieć również długość od 3 do 24 znaków i może zawierać tylko cyfry i małe litery.
1. Wybierz lokalizację dla swojego konta magazynu.
1. Ustaw replikację na **magazyn lokalnie nadmiarowy** lub **Magazyn strefowy**.
1. Pozostaw te pola przy wartościach domyślnych:

   |Pole  |Wartość  |
   |---------|---------|
   |Model wdrażania     |Resource Manager         |
   |Wydajność     |Standardowa         |
   |Rodzaj konta     |StorageV2 (ogólnego przeznaczenia wersja 2)         |
   |Warstwa dostępu     |Gorąca         |

1. Wybierz **pozycję Zaawansowane**, a następnie wybierz przycisk opcji **Włączone** po prawej stronie **udziałów w dużych plikach**.
1. Wybierz pozycję **Przejrzyj i utwórz**, aby przejrzeć ustawienia konta magazynu i utworzyć konto.

    ![Zrzut ekranu z przyciskiem opcji "włączono" na nowym koncie magazynu w witrynie Azure portal](media/storage-files-how-to-create-large-file-share/large-file-shares-advanced-enable.png)

1. Wybierz pozycję **Utwórz**.

### <a name="cli"></a>Interfejs wiersza polecenia

Najpierw [zainstaluj najnowszą wersję interfejsu wiersza polecenia platformy Azure,](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) aby włączyć duże udziały plików.

Aby utworzyć konto magazynu z włączonymi dużymi udziałami plików, użyj następującego polecenia. Zastąp `<yourStorageAccountName>`, `<yourResourceGroup>`i `<yourDesiredRegion>` z informacjami.

```azurecli-interactive
## This command creates a large file share–enabled account. It will not support GZRS, GRS, or RA-GRS.
az storage account create --name <yourStorageAccountName> -g <yourResourceGroup> -l <yourDesiredRegion> --sku Standard_LRS --kind StorageV2 --enable-large-file-share
```

### <a name="powershell"></a>PowerShell

Najpierw [zainstaluj najnowszą wersję programu PowerShell,](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0) aby włączyć duże udziały plików.

Aby utworzyć konto magazynu z włączonymi dużymi udziałami plików, użyj następującego polecenia. Zastąp `<yourStorageAccountName>`, `<yourResourceGroup>`i `<yourDesiredRegion>` z informacjami.

```powershell
## This command creates a large file share–enabled account. It will not support GZRS, GRS, or RA-GRS.
New-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -Location <yourDesiredRegion> -SkuName Standard_LRS -EnableLargeFileShare;
```

## <a name="enable-large-files-shares-on-an-existing-account"></a>Włączanie udziałów dużych plików na istniejącym koncie

Można również włączyć duże udziały plików na istniejących kontach. Jeśli włączysz duże udziały plików, nie będzie można konwertować na GZRS, GRS lub RA-GRS. Włączenie dużych udziałów plików jest nieodwracalne na tym koncie magazynu.

### <a name="portal"></a>Portal

1. Otwórz [witrynę Azure portal](https://portal.azure.com)i przejdź do konta magazynu, na którym chcesz włączyć duże udziały plików.
1. Otwórz konto magazynu i wybierz pozycję **Konfiguracja**.
1. Wybierz **pozycję Włączone** w dużych **udziałach plików**, a następnie wybierz pozycję **Zapisz**.
1. Wybierz **pozycję Przegląd** i wybierz pozycję **Odśwież**.

![Wybieranie przycisku opcji Włączone na istniejącym koncie magazynu w witrynie Azure portal](media/storage-files-how-to-create-large-file-share/enable-large-file-shares-on-existing.png)

Masz teraz włączone duże udziały plików na koncie magazynu. Następnie należy zaktualizować przydział istniejącego udziału, aby korzystać ze zwiększonej pojemności i skali.

Jeśli zostanie wyświetlony komunikat o błędzie "Duże udziały plików nie są jeszcze dostępne dla konta", region może być w trakcie realizacji. Jeśli masz pilną potrzebę dużych udziałów plików, skontaktuj się z pomocą techniczną.

### <a name="cli"></a>Interfejs wiersza polecenia

Aby włączyć duże udziały plików na istniejącym koncie, użyj następującego polecenia. Zastąp `<yourStorageAccountName>` i `<yourResourceGroup>` z informacjami.

```azurecli-interactive
az storage account update --name <yourStorageAccountName> -g <yourResourceGroup> --enable-large-file-share
```

### <a name="powershell"></a>PowerShell

Aby włączyć duże udziały plików na istniejącym koncie, użyj następującego polecenia. Zastąp `<yourStorageAccountName>` i `<yourResourceGroup>` z informacjami.

```powershell
Set-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -EnableLargeFileShare
```

## <a name="create-a-large-file-share"></a>Tworzenie dużego udziału plików

Po włączeniu dużych udziałów plików na koncie magazynu można utworzyć udziały plików na tym koncie z wyższymi przydziałami. 

### <a name="portal"></a>Portal

Tworzenie dużego udziału plików jest prawie identyczne z tworzeniem standardowego udziału plików. Główną różnicą jest to, że można ustawić przydział do 100 TiB.

1. Z konta magazynu wybierz pozycję **Udziały plików**.
1. Wybierz **+ Udział plików**.
1. Wprowadź nazwę udziału plików. Można również ustawić rozmiar przydziału, który chcesz, do 100 TiB. Następnie wybierz pozycję **Utwórz**. 

![Interfejs użytkownika witryny portalu platformy Azure z polami Nazwa i Przydział](media/storage-files-how-to-create-large-file-share/large-file-shares-create-share.png)

### <a name="cli"></a>Interfejs wiersza polecenia

Aby utworzyć duży udział plików, użyj następującego polecenia. Zastąp `<yourStorageAccountName>`, `<yourStorageAccountKey>`i `<yourFileShareName>` z informacjami.

```azurecli-interactive
az storage share create --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName>
```

### <a name="powershell"></a>PowerShell

Aby utworzyć duży udział plików, użyj następującego polecenia. Zastąp `<YourStorageAccountName>`, `<YourStorageAccountKey>`i `<YourStorageAccountFileShareName>` z informacjami.

```powershell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
New-AzStorageShare -Name $shareName -Context $ctx
```

## <a name="expand-existing-file-shares"></a>Rozwijanie istniejących udziałów plików

Po włączeniu dużych udziałów plików na koncie magazynu można również rozszerzyć istniejące udziały plików na tym koncie do wyższego przydziału. 

### <a name="portal"></a>Portal

1. Z konta magazynu wybierz pozycję **Udziały plików**.
1. Kliknij prawym przyciskiem myszy udział plików, a następnie wybierz pozycję **Przydział**.
1. Wprowadź nowy rozmiar, a następnie wybierz **przycisk OK**.

![Interfejs użytkownika portalu platformy Azure z przydziałem istniejących udziałów plików](media/storage-files-how-to-create-large-file-share/update-large-file-share-quota.png)

### <a name="cli"></a>Interfejs wiersza polecenia

Aby ustawić maksymalny rozmiar przydziału, użyj następującego polecenia. Zastąp `<yourStorageAccountName>`, `<yourStorageAccountKey>`i `<yourFileShareName>` z informacjami.

```azurecli-interactive
az storage share update --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName> --quota 102400
```

### <a name="powershell"></a>PowerShell

Aby ustawić maksymalny rozmiar przydziału, użyj następującego polecenia. Zastąp `<YourStorageAccountName>`, `<YourStorageAccountKey>`i `<YourStorageAccountFileShareName>` z informacjami.

```powershell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
# update quota
Set-AzStorageShareQuota -ShareName $shareName -Context $ctx -Quota 102400
```

## <a name="next-steps"></a>Następne kroki

* [Łączenie i instalowanie udziału plików w systemie Windows](storage-how-to-use-files-windows.md)
* [Łączenie i instalowanie udziału plików w systemie Linux](storage-how-to-use-files-linux.md)
* [Łączenie i instalowanie udziału plików w systemie macOS](storage-how-to-use-files-mac.md)
