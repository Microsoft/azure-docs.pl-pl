---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 8c577db3e9f2bff9e86c3a7c37274630f90dd680
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "67183435"
---
Emulator magazynu obsługuje pojedyncze stałe konto i dobrze znane klucze uwierzytelniania na potrzeby uwierzytelniania klucza współużytkowanego. To konto i klucz są jedynymi poświadczeniami klucza współużytkowanego, które mogą być używane z emulatorem magazynu. Oto one:

```
Account name: devstoreaccount1
Account key: Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

> [!NOTE]
> Klucz uwierzytelniania obsługiwany przez emulator magazynu jest przeznaczony tylko do testowania funkcji kodu uwierzytelniania klienta. Nie ma żadnego celu zabezpieczeń. Nie można używać konta magazynu produkcyjnego i klucza z emulatorem magazynu. Nie należy używać konta deweloperskiego z danymi produkcyjnymi.
> 
> Emulator magazynu obsługuje tylko połączenia za pośrednictwem protokołu HTTP. Protokół HTTPS jest jednak zalecanym protokołem do uzyskiwania dostępu do zasobów w środowisku produkcyjnym usługi Azure Storage.
> 

#### <a name="connect-to-the-emulator-account-using-a-shortcut"></a>Nawiązywanie połączenia z kontem emulatora przy użyciu skrótu
Najprostszym sposobem nawiązywania połączenia z emulatorem magazynu z aplikacji jest skonfigurowanie parametrów połączenia w pliku konfiguracji aplikacji, który odwołuje się do skrótu `UseDevelopmentStorage=true` . Oto przykład parametrów połączenia do emulatora magazynu w pliku *app.config* : 

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
TableEndpoint=http://127.0.0.1:10002/devstoreaccount1;
QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;
```

Ta wartość jest identyczna z skrótem pokazanym powyżej, `UseDevelopmentStorage=true` .

#### <a name="specify-an-http-proxy"></a>Określ serwer proxy HTTP
Możesz również określić serwer proxy HTTP, który ma być używany podczas testowania usługi względem emulatora magazynu. Może to być przydatne w obserwowanie żądań HTTP i odpowiedzi podczas debugowania w ramach usług magazynu. Aby określić serwer proxy, Dodaj `DevelopmentStorageProxyUri` opcję do parametrów połączenia i ustaw jej wartość na identyfikator URI serwera proxy. Załóżmy na przykład, że parametry połączenia wskazują emulator magazynu i konfigurują serwer proxy HTTP:

```
UseDevelopmentStorage=true;DevelopmentStorageProxyUri=http://myProxyUri
```

