---
title: Włączanie funkcji SMB Multichannel
description: Dowiedz się, jak włączyć funkcję SMB Multichannel w udziałach plików platformy Azure w chmurze Premium.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/15/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: da4e1a58aef28e5c47100a0311ff81a5af04a918
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718985"
---
# <a name="enable-smb-multichannel-on-a-filestorage-account-preview"></a>Włączanie funkcji SMB Multichannel na koncie FileStorage (wersja zapoznawcza) 

Konta usługi Azure FileStorage obsługują usługę SMB Multichannel (wersja zapoznawcza), która zwiększa wydajność klienta SMB 3.x przez ustanowienie wielu połączeń sieciowych z udziałami plików w witrynie Premium. Ten artykuł zawiera szczegółowe wskazówki dotyczące włączania funkcji SMB Multichannel na istniejącym koncie magazynu. Aby uzyskać szczegółowe informacje na Azure Files SMB Multichannel, zobacz SMB Multichannel performance (Wydajność wielu kanałów SMB).

## <a name="limitations"></a>Ograniczenia

[!INCLUDE [storage-files-smb-multi-channel-restrictions](../../../includes/storage-files-smb-multi-channel-restrictions.md)]

### <a name="regional-availability"></a>Dostępność w regionach

[!INCLUDE [storage-files-smb-multi-channel-regions](../../../includes/storage-files-smb-multi-channel-regions.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- [Utwórz konto FileStorage.](./storage-how-to-create-file-share.md)
- Jeśli zamierzasz używać modułu Azure PowerShell, zainstaluj wersję [3.0.1-preview modułu](https://www.powershellgallery.com/packages/Az.Storage/3.0.1-preview).

## <a name="getting-started"></a>Wprowadzenie

Otwórz okno programu PowerShell i zaloguj się do subskrypcji platformy Azure. W tym miejscu możesz zarejestrować się, aby korzystać z wersji zapoznawczej funkcji SMB Multichannel, za pomocą następujących poleceń.

```azurepowershell
Connect-AzAccount
# Setting your active subscription to the one you want to register for the preview. 
# Replace the <subscription-id> placeholder with your subscription id. 
$context = Get-AzSubscription -SubscriptionId <your-subscription-id> 
Set-AzContext $context

Register-AzProviderFeature -FeatureName AllowSMBMultichannel -ProviderNamespace Microsoft.Storage 
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage 
```

> [!NOTE]
> Rejestracja może potrwać do godziny.

### <a name="verify-that-feature-registration-is-complete"></a>Sprawdzanie, czy rejestracja funkcji została ukończona

Włączenie funkcji na koncie magazynu może potrwać godzinę, dlatego możesz użyć następującego polecenia, aby sprawdzić, czy jest ona zarejestrowana dla Twojej subskrypcji:

```azurepowershell
Get-AzProviderFeature -FeatureName AllowSMBMultichannel -ProviderNamespace Microsoft.Storage
```


## <a name="enable-smb-multichannel"></a>Włączanie funkcji SMB Multichannel 
Po utworzeniu konta FileStorage możesz wykonać instrukcje aktualizowania ustawień usługi SMB Multichannel dla konta magazynu.

# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Zaloguj się do Azure Portal i przejdź do konta magazynu FileStorage, na którym chcesz skonfigurować usługę SMB Multichannel.
1. Wybierz **pozycję Udziały plików** w obszarze Usługa **plików,** a następnie **wybierz pozycję Ustawienia udziału plików.**
1. Przełącz opcję **SMB Multichannel** na **włącz** **(lub** wyłącz ją), a następnie wybierz pozycję **Zapisz.**

:::image type="content" source="media/storage-files-enable-smb-multichannel/enable-smb-multichannel-on-storage-account.png" alt-text="Zrzut ekranu przedstawiający konto magazynu z włączoną wielokanałową siecią SMB."  lightbox="media/storage-files-enable-smb-multichannel/enable-smb-multichannel-on-storage-account.png":::

Jeśli opcja SMB Multichannel nie  jest widoczna w ustawieniach udziału plików lub podczas aktualizowania konfiguracji występuje błąd ustawienia nie powiodło się, upewnij się, że Twoja subskrypcja jest zarejestrowana, a Twoje konto znajduje się w jednym z obsługiwanych regionów z obsługiwanym typem konta i replikacją. [](#regional-availability)

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Aby włączyć funkcję SMB Multichannel przy użyciu Azure PowerShell, musisz zainstalować wersję [3.0.1-preview](https://www.powershellgallery.com/packages/Az.Storage/3.0.1-preview) modułu.

Przed uruchomieniem tych poleceń programu PowerShell ustaw zmienne i na grupę zasobów i konto `$resourceGroupName` `$storageAccountName` magazynu.

```azurepowershell
# Enable SMB Multichannel on the premium storage account that's in one of the supported regions
Update-AzStorageFileServiceProperty -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -EnableSmbMultichannel $true 
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
Interfejs wiersza polecenia platformy Azure nie obsługuje jeszcze konfigurowania usługi SMB Multichannel. Zapoznaj się z instrukcjami w portalu dotyczącymi konfigurowania usługi SMB Multichannel na koncie magazynu.

---

> [!NOTE]
> Wszelkie zmiany ustawień konfiguracji SMB Multichannel będą stosowane do wszystkich udziałów plików w ramach konta magazynu. Należy jednak ponownie odinstalować udział na kliencie, aby zmiany weszły w życie.


## <a name="next-steps"></a>Następne kroki 

- [Ponownie odinstaluj udział plików,](storage-how-to-use-files-windows.md) aby skorzystać z usługi SMB Multichannel.
- [Rozwiąż wszelkie problemy związane z usługą SMB Multichannel.](storage-troubleshooting-files-performance.md#smb-multichannel-option-not-visible-under-file-share-settings)
- Aby dowiedzieć się więcej na temat ulepszeń, zobacz [Wydajność funkcji SMB Multichannel](storage-files-smb-multichannel-performance.md)
 - Aby dowiedzieć się więcej na temat funkcji Windows SMB Multichannel, zobacz [Zarządzanie usługą SMB Mulitchannel.](/azure-stack/hci/manage/manage-smb-multichannel)