---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: eeea7b1fed0c2a1f805e21b4dec9ec3cad7fc976
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95996824"
---
## <a name="configure-your-application-to-access-azure-storage"></a>Konfigurowanie aplikacji w celu uzyskiwania dostępu do usługi Azure Storage
Istnieją dwa sposoby uwierzytelniania aplikacji w celu uzyskania dostępu do usług magazynu:

* Klucz współużytkowany: Użyj klucza współużytkowanego tylko do celów testowych
* Sygnatura dostępu współdzielonego (SAS): Użyj SAS dla aplikacji produkcyjnych

### <a name="shared-key"></a>Klucz wspólny
Uwierzytelnianie klucza wspólnego oznacza, że aplikacja będzie używać nazwy konta i klucza konta do uzyskiwania dostępu do usług magazynu. W celu szybkiego pokazania sposobu korzystania z tej biblioteki w tym temacie rozpoczynamy korzystanie z uwierzytelniania klucza współużytkowanego.

> [!WARNING] 
> **Do celów testowych Używaj tylko uwierzytelniania klucza współużytkowanego.** Nazwa konta i klucz konta, które zapewniają pełny dostęp do odczytu i zapisu do skojarzonego konta magazynu, będą dystrybuowane do każdej osoby, która pobiera aplikację. **Nie** jest to dobre rozwiązanie w przypadku ryzyka, że klucz został złamany przez niezaufanych klientów.
> 
> 

W przypadku korzystania z uwierzytelniania za pomocą klucza wspólnego utworzysz [Parametry połączenia](../articles/storage/common/storage-configure-connection-string.md). Parametry połączenia składają się z:  

* **DefaultEndpointsProtocol** — możesz wybrać http lub https. Jednak użycie protokołu HTTPS jest zdecydowanie zalecane.
* **Nazwa konta** — nazwa konta magazynu
* **Klucz konta** — w witrynie [Azure Portal](https://portal.azure.com)przejdź do swojego konta magazynu, a następnie kliknij ikonę **klucze** , aby znaleźć te informacje.
* Obowiązkowe **EndpointSuffix** — jest to używane w przypadku usług magazynu w regionach z różnymi sufiksami punktów końcowych, takimi jak Azure Chiny czy zarządzanie platformą Azure.

Oto przykład parametrów połączenia przy użyciu uwierzytelniania klucza wspólnego:

`"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here"`

### <a name="shared-access-signatures-sas"></a>Sygnatury dostępu współdzielonego (SAS)
W przypadku aplikacji mobilnej zalecana metoda uwierzytelniania żądania przez klienta w usłudze Azure Storage polega na użyciu sygnatury dostępu współdzielonego (SAS). Sygnatura dostępu współdzielonego umożliwia przyznanie klientowi dostęp do zasobu przez określony czas, z określonym zestawem uprawnień.
Jako właściciel konta magazynu musisz wygenerować sygnaturę dostępu współdzielonego dla klientów mobilnych do użycia. Aby wygenerować SYGNATURę dostępu współdzielonego, prawdopodobnie warto napisać oddzielną usługę generującą SYGNATURę dostępu współdzielonego do klientów. Do celów testowych można użyć [Eksplorator usługi Microsoft Azure Storage](https://storageexplorer.com) lub witryny [Azure Portal](https://portal.azure.com) do wygenerowania sygnatury dostępu współdzielonego. Podczas tworzenia sygnatury dostępu współdzielonego można określić przedział czasu, w którym sygnatury dostępu współdzielonego są prawidłowe, oraz uprawnienia, które są przydzielane do klienta przez sygnaturę dostępu współdzielonego.

Poniższy przykład pokazuje, jak używać Eksplorator usługi Microsoft Azure Storage do generowania sygnatury dostępu współdzielonego.

1. Jeśli jeszcze tego nie zrobiono, [zainstaluj Eksplorator usługi Microsoft Azure Storage](https://storageexplorer.com)
2. Nawiąż połączenie ze swoją subskrypcją.
3. Kliknij konto magazynu i kliknij kartę "akcje" w lewym dolnym rogu. Kliknij pozycję "Uzyskaj sygnaturę dostępu współdzielonego", aby wygenerować "parametry połączenia" dla SAS.
4. Poniżej znajduje się przykład parametrów połączenia SAS, które przyznają uprawnienia do odczytu i zapisu na poziomie usługi, kontenera i obiektu dla usługi BLOB na koncie magazynu.
   
   `"SharedAccessSignature=sv=2015-04-05&ss=b&srt=sco&sp=rw&se=2016-07-21T18%3A00%3A00Z&sig=3ABdLOJZosCp0o491T%2BqZGKIhafF1nlM3MzESDDD3Gg%3D;BlobEndpoint=https://youraccount.blob.core.windows.net"`

Podobnie jak w przypadku korzystania z sygnatury dostępu współdzielonego, nie ujawniasz klucza konta w aplikacji. Aby dowiedzieć się więcej na temat SYGNATURy dostępu współdzielonego i najlepszych rozwiązań dotyczących używania SAS, można wyewidencjonować [podpisy dostępowe: zrozumieć model SAS](../articles/storage/common/storage-sas-overview.md).