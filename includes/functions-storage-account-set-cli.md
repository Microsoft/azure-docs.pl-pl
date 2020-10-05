---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 4ace70abe0112e0fe27d177c02bcb697746c92cc
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "78262372"
---
### <a name="set-the-storage-account-connection"></a>Ustawianie połączenia konta magazynu

Otwórz local.settings.jsw pliku i skopiuj wartość `AzureWebJobsStorage` , która jest ciągiem połączenia konta magazynu. Ustaw `AZURE_STORAGE_CONNECTION_STRING` zmienną środowiskową na parametry połączenia za pomocą tego polecenia bash:

```bash
AZURE_STORAGE_CONNECTION_STRING="<STORAGE_CONNECTION_STRING>"
```

Po ustawieniu parametrów połączenia w `AZURE_STORAGE_CONNECTION_STRING` zmiennej środowiskowej można uzyskać dostęp do konta magazynu bez konieczności podawania uwierzytelniania za każdym razem.