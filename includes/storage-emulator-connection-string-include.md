---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/28/2020
ms.author: tamram
ms.openlocfilehash: 85cfe3b062d7d9ef3a7bdcf29ef7d2125f8f3ae4
ms.sourcegitcommit: 31d242b611a2887e0af1fc501a7d808c933a6bf6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/29/2020
ms.locfileid: "97812797"
---
Emulator obsługuje pojedyncze stałe konto i dobrze znane klucze uwierzytelniania na potrzeby uwierzytelniania klucza współużytkowanego. To konto i klucz są jedynymi poświadczeniami klucza współużytkowanego, które mogą być używane z emulatorem. Są to:

```
Account name: devstoreaccount1
Account key: Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

> [!NOTE]
> Klucz uwierzytelniania obsługiwany przez emulator jest przeznaczony tylko do testowania funkcji kodu uwierzytelniania klienta. Nie ma żadnego celu zabezpieczeń. Nie można używać konta magazynu produkcyjnego i klucza z emulatorem. Nie należy używać konta deweloperskiego z danymi produkcyjnymi.
>
> Emulator obsługuje tylko połączenia za pośrednictwem protokołu HTTP. Protokół HTTPS jest jednak zalecanym protokołem do uzyskiwania dostępu do zasobów w środowisku produkcyjnym usługi Azure Storage.
>

#### <a name="connect-to-the-emulator-account-using-the-shortcut"></a>Nawiązywanie połączenia z kontem emulatora przy użyciu skrótu

Najprostszym sposobem nawiązywania połączenia z emulatorem z aplikacji jest skonfigurowanie parametrów połączenia w pliku konfiguracji aplikacji, który odwołuje się do skrótu `UseDevelopmentStorage=true` . Skrót jest równoważny z pełnymi parametrami połączenia dla emulatora, który określa nazwę konta, klucz konta i punkty końcowe emulatora dla każdej usługi Azure Storage:

```
DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;
AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;
BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;
QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;
TableEndpoint=http://127.0.0.1:10001/devstoreaccount1;
```

Poniższy fragment kodu platformy .NET pokazuje, jak można użyć skrótu z metody, która pobiera parametry połączenia. Na przykład Konstruktor [BlobContainerClient (String, String)](/dotnet/api/azure.storage.blobs.blobcontainerclient.-ctor#Azure_Storage_Blobs_BlobContainerClient__ctor_System_String_System_String_) przyjmuje parametry połączenia.

```csharp
BlobContainerClient blobContainerClient = new BlobContainerClient("UseDevelopmentStorage=true", "sample-container");
blobContainerClient.CreateIfNotExists();
```

Upewnij się, że emulator jest uruchomiony przed wywołaniem kodu w fragmencie.
