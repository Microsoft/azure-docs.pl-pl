---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: affd55d65ed8454ebfcdf14f697849badf8e5d3c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88658438"
---
<!-- ### Upload files with the CLI -->

Następujące polecenie przekazuje pojedynczy plik.  

Zmień następujące wartości:

1. Zmień `/path/to/file` ścieżkę lokalną pliku.  
1. Zmień `MyContainer` na element zawartości, który został wcześniej utworzony przy użyciu identyfikatora zasobu (nie nazwy).
1. Zmień `MyBlob` na nazwę, która ma być używana dla przekazanego pliku.
1. Zmień `MyStorageAccountName` nazwę konta magazynu, którego używasz.
1. Przejdź `MyStorageAccountKey` do klucza dostępu dla konta magazynu.

    ```azurecli
    az storage blob upload -f /path/to/file -c MyContainer -n MyBlob --acount-name MyStorageAccountName --account-key MyStorageAccountKey
    ```
