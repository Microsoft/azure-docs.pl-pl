---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 4ace70abe0112e0fe27d177c02bcb697746c92cc
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "78262372"
---
### <a name="set-the-storage-account-connection"></a>Ustawianie połączenia konta magazynu

Otwórz plik Local. Settings. JSON i skopiuj wartość `AzureWebJobsStorage`, która jest ciągiem połączenia konta magazynu. Ustaw zmienną `AZURE_STORAGE_CONNECTION_STRING` środowiskową na parametry połączenia za pomocą tego polecenia bash:

```bash
AZURE_STORAGE_CONNECTION_STRING="<STORAGE_CONNECTION_STRING>"
```

Po ustawieniu parametrów połączenia w zmiennej `AZURE_STORAGE_CONNECTION_STRING` środowiskowej można uzyskać dostęp do konta magazynu bez konieczności podawania uwierzytelniania za każdym razem.