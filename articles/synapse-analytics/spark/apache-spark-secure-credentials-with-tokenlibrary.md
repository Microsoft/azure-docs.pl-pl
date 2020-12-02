---
title: Zabezpieczanie poświadczeń dostępu za pomocą połączonych usług w Apache Spark usługi Azure Synapse Analytics
description: Ten artykuł zawiera pojęcia dotyczące bezpiecznego integrowania Apache Spark usługi Azure Synapse Analytics z innymi usługami przy użyciu połączonych usług i biblioteki tokenów
services: synapse-analytics
author: mlee3gsd
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 08/26/2020
ms.author: martinle
ms.reviewer: euang
ms.openlocfilehash: d542e6ef784d763e406aad28231431cbc382fbfd
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96450906"
---
# <a name="securing-your-credentials-through-linked-services-with-the-tokenlibrary"></a>Zabezpieczanie poświadczeń za pomocą połączonych usług z TokenLibrary
Dostęp do danych ze źródeł zewnętrznych jest typowym wzorcem. O ile zewnętrzne źródło danych nie zezwala na dostęp anonimowy, należy zabezpieczyć połączenie za pomocą poświadczeń, klucza tajnego lub parametrów połączenia.  

Usługa Azure Synapse Analytics oferuje połączone usługi, które upraszczają proces integracji przez przechowywanie szczegółów połączenia w połączonej usłudze lub Azure Key Vault. Po utworzeniu połączonej usługi Apache Spark może odwoływać się do połączonej usługi, aby zastosować informacje o połączeniu w kodzie. 

Aby uzyskać więcej informacji, zobacz [połączone usługi](../../data-factory/concepts-linked-services.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
> [!NOTE]
> Dostęp do plików z Azure Data Lake Storage w obszarze roboczym odbywa się przy użyciu przekazywania usługi AAD w związku z tym, nie trzeba używać TokenLibrary. 


## <a name="prerequisite"></a>Wymaganie wstępne
* Połączona usługa — należy utworzyć połączoną usługę w zewnętrznym źródle danych i odwołać się do połączonej usługi z biblioteki tokenów. Dowiedz się więcej na temat [połączonych usług](../../data-factory/concepts-linked-services.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).


## <a name="connect-to-adls-gen2-outside-of-synapse-workspace"></a>Nawiązywanie połączenia z ADLS Gen2 poza obszarem roboczym Synapse

Program Synapse zapewnia zintegrowane środowisko połączone usług dla Azure Data Lake Storage Gen2.

```scala
// Scala code
val sc = spark.sparkContext
sc.hadoopConfiguration.set("spark.storage.synapse.linkedServiceName", "<LINKED SERVICE NAME>")
sc.hadoopConfiguration.set("fs.azure.account.auth.type", "SAS")
sc.hadoopConfiguration.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.LinkedServiceBasedSASProvider")

val df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<DIRECTORY PATH>")

df.show()
```

```python
# Python code
sc._jsc.hadoopConfiguration().set("spark.storage.synapse.linkedServiceName", "<lINKED SERVICE NAME>")
sc._jsc.hadoopConfiguration().set("fs.azure.account.auth.type", "SAS")
sc._jsc.hadoopConfiguration().set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.LinkedServiceBasedSASProvider")

df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<DIRECTORY PATH>")

df.show()
```
## <a name="use-the-token-library"></a>Korzystanie z biblioteki tokenów

Aby nawiązać połączenie z innymi połączonymi usługami, możesz wykonać bezpośrednie wywołanie do TokenLibrary.

### <a name="getconnectionstring"></a>GetConnectionString
 Aby pobrać parametry połączenia, użyj funkcji <b>GetConnectionString</b> i przekaż <b>nazwę połączonej usługi</b>.

```scala
// Scala
// retrieve connectionstring from TokenLibrary

import com.microsoft.azure.synapse.tokenlibrary.TokenLibrary

val connectionString: String = TokenLibrary.getConnectionString("<LINKED SERVICE NAME>")
println(connectionString)
```

```python
# Python
# retrieve connectionstring from TokenLibrary

from pyspark.sql import SparkSession

sc = SparkSession.builder.getOrCreate()
token_library = sc._jvm.com.microsoft.azure.synapse.tokenlibrary.TokenLibrary
connection_string = token_library.getConnectionString("<LINKED SERVICE NAME>")
print(connection_string)
```
```csharp
// C#
// retrieve connectionstring from TokenLibrary

using Microsoft.Spark.Extensions.Azure.Synapse.Analytics.Utils;

string connectionString = TokenLibrary.GetConnectionString(<LINKED SERVICE NAME>);
Console.WriteLine(connectionString);
```

### <a name="getconnectionstringasmap"></a>GetConnectionStringAsMap
Aby przeanalizować określone wartości z pary <i>klucz = wartość</i> w parametrach połączenia, takich jak 

<i>DefaultEndpointsProtocol = https; AccountName = \<AccountName> ; AccountKey =\<AccountKey></i>

Użyj funkcji <b>getConnectionStringAsMap</b> i przekaż klucz, aby zwrócić wartość.
```scala
// Linked services can be used for storing and retreiving credentials (e.g, account key)
// Example connection string (for storage): "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
import com.microsoft.azure.synapse.tokenlibrary.TokenLibrary

val accountKey: String = TokenLibrary.getConnectionStringAsMap("<LINKED SERVICE NAME">).get("<KEY NAME>")
println(accountKey)
```

```python
# Linked services can be used for storing and retreiving credentials (e.g, account key)
# Example connection string (for storage): "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
from pyspark.sql import SparkSession

sc = SparkSession.builder.getOrCreate()
token_library = sc._jvm.com.microsoft.azure.synapse.tokenlibrary.TokenLibrary
accountKey = token_library.getConnectionStringAsMap("<LINKED SERVICE NAME>").get("<KEY NAME>")
print(accountKey)
```

## <a name="next-steps"></a>Następne kroki

- [Zapisz w dedykowanej puli SQL](./synapse-spark-sql-pool-import-export.md)

