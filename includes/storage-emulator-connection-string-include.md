---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/28/2020
ms.author: tamram
ms.openlocfilehash: a9d7f4f77d91abc88ea348e71a3d9c471b26a273
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103622141"
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
TableEndpoint=http://127.0.0.1:10002/devstoreaccount1;
```

Poniższy fragment kodu platformy .NET pokazuje, jak można użyć skrótu z metody, która pobiera parametry połączenia. Na przykład Konstruktor [BlobContainerClient (String, String)](/dotnet/api/azure.storage.blobs.blobcontainerclient.-ctor#Azure_Storage_Blobs_BlobContainerClient__ctor_System_String_System_String_) przyjmuje parametry połączenia.

```csharp
BlobContainerClient blobContainerClient = new BlobContainerClient("UseDevelopmentStorage=true", "sample-container");
blobContainerClient.CreateIfNotExists();
```

Upewnij się, że emulator jest uruchomiony przed wywołaniem kodu w fragmencie.
