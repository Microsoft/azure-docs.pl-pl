---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 91a52e7eac40c0ac2ab682f251a2ae0013259b25
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "77013859"
---
## <a name="what-is-table-storage"></a>Co to jest usługa Table Storage
Usługa Azure Table Storage służy do przechowywania dużych ilości danych strukturalnych. Usługa jest magazynem danych NoSQL, który przyjmuje uwierzytelnione wywołania z chmury Azure i spoza niej. Tabele Azure idealnie nadają się do przechowywania strukturalnych danych nierelacyjnych. Najczęstsze zastosowania usługi Table Storage to:

* Przechowywanie tabel danych strukturalnych umożliwiających obsługę aplikacji w skali sieci Web
* Zapisywanie zestawów danych, które nie wymagają złożonych sprzężeń, kluczy obcych lub przechowywanych procedur i mogą być nieznormalizowane w celu zapewniania szybkiego dostępu
* Szybkie wykonywanie zapytań o dane przy użyciu indeksu klastrowanego
* Uzyskiwanie dostępu do danych przy użyciu protokołu OData i zapytań LINQ z bibliotekami .NET usługi danych WCF

Usługa Table Storage służy do przechowywania i wykonywania zapytań dotyczących dużych zestawów strukturalnych danych nierelacyjnych. Tabele mogą być skalowane wraz ze wzrostem wymagań.

## <a name="table-storage-concepts"></a>Pojęcia związane z usługą Table Storage
Usługa Table Storage zawiera następujące składniki:

![Diagram składników usługi Table Storage][Table1]

* **Format adresu URL:** Konta usługi Azure Table Storage używają następującego formatu: `http://<storage account>.table.core.windows.net/<table>`

  Konta interfejsu API tabel usługi Azure Cosmos DB używają następującego formatu: `http://<storage account>.table.cosmosdb.azure.com/<table>`  

  Możesz odwołać się do tabel platformy Azure bezpośrednio przy użyciu tego adresu i protokołu OData. Aby uzyskać więcej informacji, zobacz [OData.org][OData.org].
* **Konta:** Cały dostęp do usługi Azure Storage odbywa się przez konto magazynu. Aby uzyskać więcej informacji na temat kont magazynu, zobacz [Omówienie konta magazynu](../articles/storage/common/storage-account-overview.md).

    Cały dostęp do usługi Azure Cosmos DB odbywa się przez konto interfejsu API tabel. Zobacz [Tworzenie konta interfejsu API tabel](../articles/cosmos-db/create-table-dotnet.md#create-a-database-account), aby uzyskać szczegółowe informacje na temat tworzenia konta interfejsu API tabel.
* **Tabela**: tabela jest kolekcją obiektów. Tabele nie wymuszają schematu na obiektach, co oznacza, że jedna tabela może zawierać obiekty o różnych zestawach właściwości.  
* **Obiekt**: obiekt jest zestawem właściwości podobnym do wiersza bazy danych. Maksymalny rozmiar jednostki w usłudze Azure Storage to 1 MB. Maksymalny rozmiar jednostki w usłudze Azure Cosmos DB to 2 MB.
* **Właściwości**: właściwość to połączenie nazwy i wartości. Każdy obiekt może zawierać maksymalnie 252 właściwości do przechowywania danych. Każdy obiekt ma również trzy właściwości systemowe, które określają klucz partycji, klucz wiersza i znacznik czasu. Obiekty o tym samym kluczu partycji mogą być szybciej badane oraz wstawiane/aktualizowane w operacjach niepodzielnych. Klucz wiersza obiektu jest jego unikatowym identyfikatorem w partycji.

Szczegółowe informacje na temat nazewnictwa tabel i właściwości można znaleźć w temacie [Omówienie modelu danych usługi Table service](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model).

[Table1]: ./media/storage-table-concepts-include/table1.png
[OData.org]: http://www.odata.org/
