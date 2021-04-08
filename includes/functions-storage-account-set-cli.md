---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 4ace70abe0112e0fe27d177c02bcb697746c92cc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "78262372"
---
### <a name="set-the-storage-account-connection"></a>Ustawianie połączenia konta magazynu

Otwórz local.settings.jsw pliku i skopiuj wartość `AzureWebJobsStorage` , która jest ciągiem połączenia konta magazynu. Ustaw `AZURE_STORAGE_CONNECTION_STRING` zmienną środowiskową na parametry połączenia za pomocą tego polecenia bash:

```bash
AZURE_STORAGE_CONNECTION_STRING="<STORAGE_CONNECTION_STRING>"
```

Po ustawieniu parametrów połączenia w `AZURE_STORAGE_CONNECTION_STRING` zmiennej środowiskowej można uzyskać dostęp do konta magazynu bez konieczności podawania uwierzytelniania za każdym razem.