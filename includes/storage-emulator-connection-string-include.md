---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/17/2020
ms.author: tamram
ms.openlocfilehash: 85e7cb86217340e77a6f597a357c3de1f91fb8d0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87070534"
---
Program azurite obsługuje pojedyncze stałe konto i dobrze znane klucze uwierzytelniania na potrzeby uwierzytelniania klucza współużytkowanego. To konto i klucz są jedynymi poświadczeniami klucza współużytkowanego, które mogą być używane z azurite. Są to:

```
Account name: devstoreaccount1
Account key: Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

> [!NOTE]
> Klucz uwierzytelniania obsługiwany przez azurite jest przeznaczony tylko do testowania funkcji kodu uwierzytelniania klienta. Nie ma żadnego celu zabezpieczeń. Nie można użyć konta magazynu produkcyjnego i klucza z azurite. Nie należy używać konta deweloperskiego z danymi produkcyjnymi.
> 
> Azurite obsługuje połączenie wyłącznie za pośrednictwem protokołu HTTP. Protokół HTTPS jest jednak zalecanym protokołem do uzyskiwania dostępu do zasobów w środowisku produkcyjnym usługi Azure Storage.
> 

#### <a name="connect-to-the-emulator-account-using-a-shortcut"></a>Nawiązywanie połączenia z kontem emulatora przy użyciu skrótu
Najprostszym sposobem nawiązywania połączenia z usługą azurite z poziomu aplikacji jest skonfigurowanie parametrów połączenia w pliku konfiguracji aplikacji, który odwołuje się do skrótu `UseDevelopmentStorage=true` . Oto przykład parametrów połączenia do azurite w pliku *app.config* : 

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

#### <a name="connect-to-the-emulator-account-using-the-well-known-account-name-and-key"></a>Nawiązywanie połączenia z kontem emulatora przy użyciu dobrze znanej nazwy konta i klucza
Aby utworzyć parametry połączenia odwołujące się do nazwy i klucza konta emulatora, należy określić punkty końcowe dla każdej usługi, która ma być używana z emulatora w parametrach połączenia. Jest to konieczne, aby parametry połączenia odwołują się do punktów końcowych emulatora, które są inne niż te dla konta magazynu produkcyjnego. Na przykład wartość parametrów połączenia będzie wyglądać następująco:

```
DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;
AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;
BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;
QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;
```

Ta wartość jest identyczna z skrótem pokazanym powyżej, `UseDevelopmentStorage=true` .
