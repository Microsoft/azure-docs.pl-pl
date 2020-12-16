---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/17/2020
ms.author: tamram
ms.openlocfilehash: 37fba0101365e425110c2943264c8c0e8c511329
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97582678"
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

#### <a name="connect-to-the-emulator-account-using-a-shortcut"></a>Nawiązywanie połączenia z kontem emulatora przy użyciu skrótu
Najprostszym sposobem nawiązywania połączenia z emulatorem z aplikacji jest skonfigurowanie parametrów połączenia w pliku konfiguracji aplikacji, który odwołuje się do skrótu `UseDevelopmentStorage=true` . Oto przykład parametrów połączenia do emulatora w pliku *app.config* : 

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

Jest to odpowiednik w pełni określający nazwę konta, klucz konta i punkty końcowe dla każdej usługi emulatora, która ma być używana w parametrach połączenia. Jest to konieczne, aby parametry połączenia odwołują się do punktów końcowych emulatora, które są inne niż te dla konta magazynu produkcyjnego. Na przykład wartość parametrów połączenia będzie wyglądać następująco:

```
DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;
AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;
BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;
QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;
```
