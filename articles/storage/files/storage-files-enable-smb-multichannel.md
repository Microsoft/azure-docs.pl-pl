---
title: Włącz Wielokanałowość protokołu SMB
description: Dowiedz się, jak włączyć Wielokanałowość protokołu SMB w udziałach plików platformy Azure w warstwie Premium.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 11/16/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 2f867fa6d4b7e1d864a85106b5d957a53d38eb76
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101732556"
---
# <a name="enable-smb-multichannel-on-a-filestorage-account-preview"></a>Włączanie wielokanałowości SMB na koncie FileStorage (wersja zapoznawcza) 

Konta usługi Azure FileStorage obsługują Wielokanałowość SMB (wersja zapoznawcza), która zwiększa wydajność klienta SMB 3. x, ustanawiając wiele połączeń sieciowych z udziałami plików w warstwie Premium. Ten artykuł zawiera wskazówki krok po kroku dotyczące włączania wielokanałowości SMB na istniejącym koncie magazynu. Aby uzyskać szczegółowe informacje na temat Azure Files protokole SMB, zobacz wydajność wielokanałowości SMB.

## <a name="limitations"></a>Ograniczenia

[!INCLUDE [storage-files-smb-multi-channel-restrictions](../../../includes/storage-files-smb-multi-channel-restrictions.md)]

### <a name="regional-availability"></a>Dostępność regionalna

[!INCLUDE [storage-files-smb-multi-channel-regions](../../../includes/storage-files-smb-multi-channel-regions.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- [Utwórz konto FileStorage](./storage-how-to-create-file-share.md).
- Jeśli zamierzasz użyć modułu Azure PowerShell, [Zainstaluj wersję 3.0.1-Preview modułu](https://www.powershellgallery.com/packages/Az.Storage/3.0.1-preview).

## <a name="getting-started"></a>Wprowadzenie

Otwórz okno programu PowerShell i zaloguj się do subskrypcji platformy Azure. Z tego miejsca możesz zarejestrować się w wersji zapoznawczej protokołu SMB przy użyciu następujących poleceń.

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

### <a name="verify-that-feature-registration-is-complete"></a>Sprawdź, czy rejestracja funkcji została zakończona

Ponieważ włączenie funkcji na koncie magazynu może potrwać do godziny, możesz użyć następującego polecenia, aby sprawdzić, czy jest on zarejestrowany dla Twojej subskrypcji:

```azurepowershell
Get-AzProviderFeature -FeatureName AllowSMBMultichannel -ProviderNamespace Microsoft.Storage
```


## <a name="enable-smb-multichannel"></a>Włączanie funkcji SMB Multichannel 
Po utworzeniu konta usługi FileStorage można postępować zgodnie z instrukcjami, aby zaktualizować ustawienia wielokanałowe protokołu SMB dla konta magazynu.

# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Zaloguj się do Azure Portal i przejdź do konta magazynu FileStorage, dla którego chcesz skonfigurować Wielokanałowość protokołu SMB.
1. Wybierz pozycję **udziały plików** w obszarze **Usługa plików**, a następnie wybierz pozycję **Ustawienia udziału plików**.
1. Przełącz **funkcję** **wielokanałowe protokołu SMB** na włączone (lub **Wyłącz** , aby wyłączyć) i wybierz pozycję **Zapisz**.

:::image type="content" source="media/storage-files-enable-smb-multichannel/enable-smb-multichannel-on-storage-account.png" alt-text="Zrzut ekranu konta magazynu, jest włączony tryb wielokanałowe protokołu SMB.":::

Jeśli opcja wielokanałowe protokołu SMB nie jest widoczna w obszarze **Ustawienia udziału plików** lub nie można zaktualizować błędu ustawienia podczas aktualizowania konfiguracji, upewnij się, że Twoja subskrypcja jest zarejestrowana, a Twoje konto należy do jednego z [obsługiwanych regionów](#regional-availability) z obsługiwanym typem konta i replikacją.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Aby włączyć Wielokanałowość protokołu SMB przy użyciu modułu Azure PowerShell, należy [zainstalować wersję 3.0.1-Preview](https://www.powershellgallery.com/packages/Az.Storage/3.0.1-preview) modułu.

`$resourceGroupName` `$storageAccountName` Przed uruchomieniem tych poleceń programu PowerShell Ustaw zmienne i grupę zasobów oraz konto magazynu.

```azurepowershell
# Enable SMB Multichannel on the premium storage account that's in one of the supported regions
Update-AzStorageFileServiceProperty -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -EnableSmbMultichannel $true 
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
Interfejs wiersza polecenia platformy Azure nie obsługuje jeszcze konfigurowania wielokanałowości SMB. Zapoznaj się z instrukcjami w portalu w celu skonfigurowania wielokanałowego protokołu SMB na koncie magazynu.

---

> [!NOTE]
> Wszystkie zmiany ustawień konfiguracji wielokanałowości SMB zostaną zastosowane do wszystkich udziałów plików na koncie magazynu. Aby zmiany zaczęły obowiązywać, należy ponownie zainstalować udział na kliencie.


## <a name="next-steps"></a>Następne kroki 

- [Zainstaluj ponownie udział plików](storage-how-to-use-files-windows.md) , aby korzystać ze wielokanałowości SMB.
- [Rozwiązywanie problemów związanych ze wielokanałowością SMB](storage-troubleshooting-files-performance.md#smb-multichannel-option-not-visible-under-file-share-settings).
- Aby dowiedzieć się więcej na temat ulepszeń, zobacz [wydajność wielokanałowe protokołu SMB](storage-files-smb-multichannel-performance.md)
 - Aby dowiedzieć się więcej na temat funkcji wielokanałowego protokołu SMB systemu Windows, zobacz [Manage SMB Mulitchannel](/azure-stack/hci/manage/manage-smb-multichannel).