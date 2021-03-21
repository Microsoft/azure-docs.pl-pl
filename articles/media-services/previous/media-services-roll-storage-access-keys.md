---
title: Aktualizowanie Media Services po przejściu między klawiszami dostępu do magazynu Microsoft Docs
description: W tym artykule przedstawiono wskazówki dotyczące sposobu aktualizacji Media Services po przejściu między klawiszami dostępu do magazynu.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: a892ebb0-0ea0-4fc8-b715-60347cc5c95b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.reviewer: milanga;cenkdin
ms.openlocfilehash: 12732171f774e6ce010f722cde4a27bb298275b9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103007938"
---
# <a name="update-media-services-after-rolling-storage-access-keys"></a>Aktualizowanie usługi Media Services po stopniowym uaktualnieniu kluczy dostępu do magazynu

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Podczas tworzenia nowego konta usługi Azure Media Services (AMS) zostanie również wyświetlony monit o wybranie konta magazynu platformy Azure używanego do przechowywania zawartości multimedialnej. Do konta Media Services można dodać więcej niż jedno konto magazynu. W tym artykule pokazano, jak obrócić klucze magazynu. Przedstawiono w nim również sposób dodawania kont magazynu do konta multimediów. 

Aby wykonać czynności opisane w tym artykule, należy używać [Azure Resource Manager interfejsów API](/rest/api/media/operations/azure-media-services-rest-api-reference) i [programu PowerShell](/powershell/module/az.media).  Aby uzyskać więcej informacji, zobacz [jak zarządzać zasobami platformy Azure za pomocą programu PowerShell i Menedżer zasobów](../../azure-resource-manager/management/manage-resource-groups-powershell.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Omówienie

Po utworzeniu nowego konta magazynu platforma Azure generuje 2 512-bitowe klucze dostępu do magazynu, które są używane do uwierzytelniania dostępu do konta magazynu. Aby zapewnić lepszą ochronę połączeń magazynu, zaleca się okresowe ponowne wygenerowanie i obrócenie klucza dostępu do magazynu. Dwa klucze dostępu (podstawowa i pomocnicza) są dostarczane w celu umożliwienia utrzymania połączeń z kontem magazynu przy użyciu jednego klucza dostępu podczas ponownego generowania drugiego klucza dostępu. Ta procedura jest również nazywana "kluczami dostępu równoległego".

Media Services zależy od dostarczonego do niego klucza magazynu. Lokalizatory używane do przesyłania strumieniowego lub pobierania zasobów zależą od określonego klucza dostępu do magazynu. Po utworzeniu konta AMS domyślnie jest ono zależne od podstawowego klucza dostępu do magazynu, ale jako użytkownik można zaktualizować klucz magazynu, którego dotyczy. Musisz się upewnić, że Media Services wiedzieć, który klucz ma być używany, wykonując czynności opisane w tym artykule.  

>[!NOTE]
> Jeśli masz wiele kont magazynu, tę procedurę należy wykonać przy użyciu poszczególnych kont magazynu. Kolejność, w której nie zostały naprawione klucze magazynu. Możesz najpierw obrócić klucz pomocniczy, a następnie klucz podstawowy lub odwrotnie.
>
> Przed wykonaniem kroków opisanych w tym artykule na koncie produkcyjnym należy przetestować je na koncie przedprodukcyjnym.
>

## <a name="steps-to-rotate-storage-keys"></a>Procedura rotacji kluczy magazynu 
 
 1. Zmień klucz podstawowy konta magazynu za pomocą polecenia cmdlet programu PowerShell lub witryny [Azure](https://portal.azure.com/) Portal.
 2. Wywołaj Sync-AzMediaServiceStorageKeys polecenie cmdlet z odpowiednimi parametrami, aby wymusić konto multimediów w celu pobrania kluczy konta magazynu
 
    Poniższy przykład pokazuje, jak zsynchronizować klucze z kontami magazynu.
  
    `Sync-AzMediaServiceStorageKeys -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccountId $storageAccountId`
  
 3. Zaczekaj godzinę lub tak. Sprawdź, czy scenariusze przesyłania strumieniowego działają.
 4. Zmień klucz pomocniczy konta magazynu za pomocą polecenia cmdlet programu PowerShell lub Azure Portal.
 5. Wywołaj Sync-AzMediaServiceStorageKeys PowerShell z odpowiednimi paramsmi, aby wymusić konto multimediów w celu pobrania nowych kluczy konta magazynu. 
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

W poniższym artykule pokazano, jak dodać konta magazynu do konta AMS: [Dołącz wiele kont magazynu do konta Media Services](./media-services-managing-multiple-storage-accounts.md).

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Wyraź opinię
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

### <a name="acknowledgments"></a>Podziękowania
Chcemy potwierdzić następujące osoby, które przyczyniają się do tworzenia tego dokumentu: Cenk Dingiloglu, Mediolan gada, Seva Titov.
