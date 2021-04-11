---
title: Aktualizacja Media Services v3 po przejściu między klawiszami dostępu do magazynu Microsoft Docs
description: W tym artykule przedstawiono wskazówki dotyczące aktualizowania Media Services v3 po przejściu do magazynu stopniowego dostępu.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2021
ms.author: inhenkel
ms.openlocfilehash: 385e6109a80c8e1e455f98b5d02ca5762f8051c7
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106282000"
---
# <a name="update-media-services-v3-after-rolling-storage-access-keys"></a>Aktualizacja Media Services v3 po przejściu do stopniowego dostępu do magazynu

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Zostanie wyświetlony monit o wybranie konta usługi Azure Storage podczas tworzenia nowego konta Azure Media Services (AMS).  Do konta Media Services możesz dodać więcej niż jedno konto magazynu. W tym artykule pokazano, jak obrócić klucze magazynu. Przedstawiono w nim również sposób dodawania kont magazynu do konta multimediów.

Aby wykonać czynności opisane w tym artykule, należy używać [Azure Resource Manager interfejsów API](/rest/api/media/operations/azure-media-services-rest-api-reference) i [programu PowerShell](/powershell/module/az.media).  Aby uzyskać więcej informacji, zobacz [jak zarządzać zasobami platformy Azure za pomocą programu PowerShell i Menedżer zasobów](../../azure-resource-manager/management/manage-resource-groups-powershell.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="storage-access-key-generation"></a>Generowanie klucza dostępu do magazynu

Po utworzeniu nowego konta magazynu platforma Azure generuje 2 512-bitowe klucze dostępu do magazynu, które są używane do uwierzytelniania dostępu do konta magazynu. Aby zapewnić bezpieczniejsze połączenia magazynu, należy okresowo ponownie wygenerować i obrócić klucz dostępu do magazynu. Dostępne są dwa klucze dostępu (podstawowe i pomocnicze), które umożliwiają zachowanie połączeń z kontem magazynu przy użyciu jednego klucza dostępu podczas ponownego generowania drugiego klucza dostępu. Ta procedura jest również nazywana "kluczami dostępu równoległego".

Media Services zależy od dostarczonego do niego klucza magazynu. Lokalizatory używane do przesyłania strumieniowego lub pobierania zasobów zależą od określonego klucza dostępu do magazynu. Po utworzeniu konta AMS domyślnie jest ono zależne od podstawowego klucza dostępu do magazynu. Jednak użytkownik może zaktualizować klucz magazynu, który ma wartość AMS. Musisz dać Media Services wiedzieć, którego klucza użyć, wykonując następujące czynności:

>[!NOTE]
> Jeśli masz wiele kont magazynu, tę procedurę należy wykonać przy użyciu poszczególnych kont magazynu. Kolejność, w której nie zostały naprawione klucze magazynu. Możesz najpierw obrócić klucz pomocniczy, a następnie klucz podstawowy lub odwrotnie.
>
> Przed wykonaniem kroków na koncie produkcyjnym upewnij się, że zostały one przetestowane na koncie przedprodukcyjnego.
>

## <a name="steps-to-rotate-storage-keys"></a>Procedura rotacji kluczy magazynu
 
 1. Zmień klucz podstawowy konta magazynu za pomocą polecenia cmdlet programu PowerShell lub witryny [Azure](https://portal.azure.com/) Portal.
 2. Wywołaj `Sync-AzMediaServiceStorageKeys` polecenie cmdlet z odpowiednimi parametrami, aby wymusić na koncie multimediów wybranie kluczy konta magazynu
 
    Poniższy przykład pokazuje, jak zsynchronizować klucze z kontami magazynu.
  
    `Sync-AzMediaServiceStorageKeys -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccountId $storageAccountId`
  
 3. Zaczekaj godzinę lub tak. Sprawdź, czy scenariusze przesyłania strumieniowego działają.
 4. Zmień klucz pomocniczy konta magazynu za pomocą polecenia cmdlet programu PowerShell lub Azure Portal.
 5. Wywołaj `Sync-AzMediaServiceStorageKeys` program PowerShell z odpowiednimi parametrami, aby wymusić na koncie multimediów pobieranie nowych kluczy konta magazynu.
 6. Zaczekaj godzinę lub tak. Sprawdź, czy scenariusze przesyłania strumieniowego działają.
 
### <a name="a-powershell-cmdlet-example"></a>Przykład polecenia cmdlet programu PowerShell

W poniższym przykładzie pokazano, jak uzyskać konto magazynu i zsynchronizować je z kontem AMS.

```console
$regionName = "West US"
$resourceGroupName = "SkyMedia-USWest-App"
$mediaAccountName = "sky"
$storageAccountName = "skystorage"
$storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

Sync-AzMediaServiceStorageKeys -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccountId $storageAccountId
```

## <a name="steps-to-add-storage-accounts-to-your-ams-account"></a>Kroki dodawania kont magazynu do konta AMS

W poniższym artykule pokazano, jak dodać konta magazynu do konta AMS: [Dołącz wiele kont magazynu do konta Media Services](storage-managing-multiple-storage-accounts-how-to.md).
