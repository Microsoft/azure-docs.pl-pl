---
title: Zabezpieczanie poświadczeń dostępu za pomocą połączonych usług w Apache Spark dla usługi Azure Synapse Analytics
description: Ten artykuł zawiera pojęcia dotyczące bezpiecznego integrowania Apache Spark usługi Azure Synapse Analytics z innymi usługami przy użyciu połączonych usług i biblioteki tokenów
services: synapse-analytics
author: mlee3gsd
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 11/19/2020
ms.author: martinle
ms.reviewer: nirav
zone_pivot_groups: programming-languages-spark-all-minus-sql
ms.openlocfilehash: a588b37b270917524453419619fdad6f88f92338
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101693581"
---
# <a name="secure-credentials-with-linked-services-using-the-tokenlibrary"></a>Zabezpieczanie poświadczeń za pomocą połączonych usług przy użyciu TokenLibrary

Dostęp do danych ze źródeł zewnętrznych jest typowym wzorcem. O ile zewnętrzne źródło danych nie zezwala na dostęp anonimowy, należy zabezpieczyć połączenie za pomocą poświadczeń, klucza tajnego lub parametrów połączenia.  

Synapse domyślnie używa przekazywania Azure Active Directory (AAD) do uwierzytelniania między zasobami.  Jeśli musisz nawiązać połączenie z zasobem przy użyciu innych poświadczeń, użyj TokenLibrary bezpośrednio.  TokenLibrary upraszcza proces pobierania tokenów SAS, tokenów usługi AAD, parametrów połączenia i wpisów tajnych przechowywanych w połączonej usłudze lub z Azure Key Vault.

Podczas pobierania wpisów tajnych z Azure Key Vault zalecamy utworzenie połączonej usługi z Azure Key Vault.  Upewnij się, że tożsamość usługi zarządzanej w obszarze roboczym Synapse (MSI) ma tajne uprawnienia pobieranie na Azure Key Vault.  Synapse będzie uwierzytelniać się w celu Azure Key Vault przy użyciu tożsamości usługi zarządzanej w obszarze roboczym Synapse. Jeśli łączysz się bezpośrednio z Azure Key Vault bez połączonej usługi, będziesz uwierzytelniać się przy użyciu poświadczeń Azure Active Directory użytkownika.

Aby uzyskać więcej informacji, zobacz [połączone usługi](../../data-factory/concepts-linked-services.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="usage"></a>Użycie

### <a name="tokenlibraryhelp"></a>TokenLibrary. Help ()
Ta funkcja wyświetla dokumentację pomocy dla TokenLibrary.

::: zone pivot = "programming-language-scala"

```scala
TokenLibrary.help()
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
TokenLibrary.help()
```

::: zone-end

::: zone pivot = "programming-language-csharp"

```csharp
Console.WriteLine(TokenLibrary.help());
```

::: zone-end

## <a name="tokenlibrary-for-azure-data-lake-storage-gen2"></a>TokenLibrary Azure Data Lake Storage Gen2

#### <a name="adls-gen2-primary-storage"></a>ADLS Gen2 magazynu podstawowego

Uzyskiwanie dostępu do plików z podstawowego Azure Data Lake Storage domyślnie korzysta z Azure Active Directory przekazywania do uwierzytelniania i nie wymaga jawnego użycia TokenLibrary.

::: zone pivot = "programming-language-scala"

```scala
val df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>")
display(df.limit(10))
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
df = spark.read.csv('abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>')
display(df.limit(10))
```

::: zone-end

#### <a name="adls-gen2-storage-with-linked-services"></a>ADLS Gen2 magazynu za pomocą połączonych usług

Synapse zapewnia zintegrowane środowisko połączonej usługi podczas nawiązywania połączenia z Azure Data Lake Storage Gen2.  Połączone usługi można skonfigurować do uwierzytelniania przy użyciu **klucza konta**, **nazwy głównej usługi**, **tożsamości zarządzanej** lub **poświadczenia**.

Gdy metoda uwierzytelniania połączonej usługi jest ustawiona na **klucz konta**, połączona usługa zostanie uwierzytelniona przy użyciu podanego klucza konta magazynu, Zażądaj klucza SAS i automatycznie zastosuje ją do żądania magazynu za pomocą **LinkedServiceBasedSASProvider**.

::: zone pivot = "programming-language-scala"

```scala
val sc = spark.sparkContext
spark.conf.set("spark.storage.synapse.linkedServiceName", "<LINKED SERVICE NAME>")
spark.conf.set("fs.azure.account.auth.type", "SAS")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.LinkedServiceBasedSASProvider")

val df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>")

display(df.limit(10))
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
%%pyspark
# Python code
spark.conf.set("spark.storage.synapse.linkedServiceName", "<lINKED SERVICE NAME>")
spark.conf.set("fs.azure.account.auth.type", "SAS")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.LinkedServiceBasedSASProvider")

df = spark.read.csv('abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<DIRECTORY PATH>')

df.show()
```

::: zone-end

Gdy metoda uwierzytelniania połączonej usługi jest ustawiona na **tożsamość zarządzaną** lub **nazwę główną usługi**, połączona usługa będzie używać tokenu zarządzanej tożsamości lub nazwy głównej usługi z dostawcą **LinkedServiceBasedTokenProvider** .  


::: zone pivot = "programming-language-scala"

```scala
val sc = spark.sparkContext
spark.conf.set("spark.storage.synapse.linkedServiceName", "<LINKED SERVICE NAME>")
spark.conf.set("fs.azure.account.oauth.provider.type", "com.microsoft.azure.synapse.tokenlibrary.LinkedServiceBasedTokenProvider") 
val df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>")

display(df.limit(10))
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
%%pyspark
# Python code
spark.conf.set("spark.storage.synapse.linkedServiceName", "<lINKED SERVICE NAME>")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.LinkedServiceBasedTokenProvider")

df = spark.read.csv('abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<DIRECTORY PATH>')

df.show()
```

::: zone-end

#### <a name="adls-gen2-storage-without-linked-services"></a>ADLS Gen2 Storage (bez połączonych usług)

Połącz się z usługą ADLS Gen2 Storage bezpośrednio przy użyciu klucza SYGNATURy dostępu współdzielonego Użyj **ConfBasedSASProvider** i Podaj klucz sygnatury dostępu współdzielonego w ustawieniach konfiguracji **platformy Spark. Storage. Synapse. SAS** .

::: zone pivot = "programming-language-scala"

```scala
%%spark
spark.conf.set("fs.azure.account.auth.type", "SAS")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.ConfBasedSASProvider")
spark.conf.set("spark.storage.synapse.sas", "<SAS KEY>")

val df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>")

display(df.limit(10))
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
%%pyspark

spark.conf.set("fs.azure.account.auth.type", "SAS")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.ConfBasedSASProvider")
spark.conf.set("spark.storage.synapse.sas", "<SAS KEY>")

df = spark.read.csv('abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>')

display(df.limit(10))
```

::: zone-end

#### <a name="adls-gen2-storage-with-azure-key-vault"></a>ADLS Gen2 magazyn z Azure Key Vault

Nawiązywanie połączenia z magazynem ADLS Gen2 przy użyciu tokenu SAS przechowywanego w Azure Key Vault Secret.  

::: zone pivot = "programming-language-scala"

```scala
%%spark
spark.conf.set("fs.azure.account.auth.type", "SAS")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.AkvBasedSASProvider")
spark.conf.set("spark.storage.synapse.akv", "<AZURE KEY VAULT NAME>")
spark.conf.set("spark.storage.akv.secret", "<SECRET KEY>")

val df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>")

display(df.limit(10))
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
%%pyspark
spark.conf.set("fs.azure.account.auth.type", "SAS")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.AkvBasedSASProvider")
spark.conf.set("spark.storage.synapse.akv", "<AZURE KEY VAULT NAME>")
spark.conf.set("spark.storage.akv.secret", "<SECRET KEY>")

df = spark.read.csv('abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>')

display(df.limit(10))
```

::: zone-end

## <a name="tokenlibrary-for-other-linked-services"></a>TokenLibrary dla innych połączonych usług

Aby nawiązać połączenie z innymi połączonymi usługami, możesz wykonać bezpośrednie wywołanie do TokenLibrary.

#### <a name="getconnectionstring"></a>GetConnectionString ()

 Aby pobrać parametry połączenia, użyj funkcji **GetConnectionString** i przekaż **nazwę połączonej usługi**.

::: zone pivot = "programming-language-scala"

```scala
%%spark
// retrieve connectionstring from TokenLibrary

import com.microsoft.azure.synapse.tokenlibrary.TokenLibrary

val connectionString: String = TokenLibrary.getConnectionString("<LINKED SERVICE NAME>")
println(connectionString)
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
%%pyspark
# retrieve connectionstring from TokenLibrary

from pyspark.sql import SparkSession

sc = SparkSession.builder.getOrCreate()
token_library = sc._jvm.com.microsoft.azure.synapse.tokenlibrary.TokenLibrary
connection_string = token_library.getConnectionString("<LINKED SERVICE NAME>")
print(connection_string)
```

::: zone-end

::: zone pivot = "programming-language-csharp"

```csharp
%%csharp
// retrieve connectionstring from TokenLibrary

using Microsoft.Spark.Extensions.Azure.Synapse.Analytics.Utils;

string connectionString = TokenLibrary.GetConnectionString(<LINKED SERVICE NAME>);
Console.WriteLine(connectionString);
```

::: zone-end

#### <a name="getconnectionstringasmap"></a>getConnectionStringAsMap()

GetConnectionStringAsMap jest funkcją pomocnika dostępną w Scala i Python w celu przeanalizowania określonych wartości z pary _klucz = wartość_ w parametrach połączenia, takich jak

_`DefaultEndpointsProtocol=https;AccountName=<ACCOUNT NAME>;AccountKey=<ACCOUNT KEY>`_

Użyj funkcji **getConnectionStringAsMap** i przekaż klucz, aby zwrócić wartość.  W powyższym przykładzie parametrów połączenia 

_**TokenLibrary. getConnectionStringAsMap ("DefaultEndpointsProtocol")**_

zwróci

**_Schemat_**

::: zone pivot = "programming-language-scala"

```scala
// Linked services can be used for storing and retrieving credentials (e.g, account key)
// Example connection string (for storage): "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
import com.microsoft.azure.synapse.tokenlibrary.TokenLibrary

val accountKey: String = TokenLibrary.getConnectionStringAsMap("<LINKED SERVICE NAME">).get("<KEY NAME>")
println(accountKey)
```
::: zone-end

::: zone pivot = "programming-language-python"

```python
# Linked services can be used for storing and retrieving credentials (e.g, account key)
# Example connection string (for storage): "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
from pyspark.sql import SparkSession

sc = SparkSession.builder.getOrCreate()
token_library = sc._jvm.com.microsoft.azure.synapse.tokenlibrary.TokenLibrary
accountKey = token_library.getConnectionStringAsMap("<LINKED SERVICE NAME>").get("<KEY NAME>")
print(accountKey)
```

::: zone-end

#### <a name="getsecret"></a>getsecret ()

Aby pobrać wpis tajny przechowywany z Azure Key Vault, zalecamy utworzenie połączonej usługi do Azure Key Vault w obszarze roboczym Synapse. Tożsamość usługi zarządzanej w obszarze roboczym Synapse musi mieć przyznane uprawnienie **Pobierz** klucze tajne do Azure Key Vault.  Połączona usługa będzie używać tożsamości usługi zarządzanej do nawiązywania połączenia z usługą Azure Key Vault w celu pobrania klucza tajnego.  W przeciwnym razie bezpośrednie połączenie z Azure Key Vault będzie używać poświadczeń Azure Active Directory użytkownika (AAD).  W takim przypadku użytkownik musi mieć przyznane uprawnienia Get Secret w Azure Key Vault.

`TokenLibrary.getSecret("<AZURE KEY VAULT NAME>", "<SECRET KEY>" [, <LINKED SERVICE NAME>])`

Aby pobrać klucz tajny z Azure Key Vault, użyj funkcji **TokenLibrary. getsecret ()** .

::: zone pivot = "programming-language-scala"

```scala
import com.microsoft.azure.synapse.tokenlibrary.TokenLibrary

val connectionString: String = TokenLibrary.getSecret("<AZURE KEY VAULT NAME>", "<SECRET KEY>", "<LINKED SERVICE NAME>")
println(connectionString)
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
import sys
from pyspark.sql import SparkSession

sc = SparkSession.builder.getOrCreate()
token_library = sc._jvm.com.microsoft.azure.synapse.tokenlibrary.TokenLibrary

connection_string = token_library.getSecret("<AZURE KEY VAULT NAME>", "<SECRET KEY>", "<LINKED SERVICE NAME>")
print(connection_string)
```

::: zone-end

::: zone pivot = "programming-language-csharp"

```csharp
using Microsoft.Spark.Extensions.Azure.Synapse.Analytics.Utils;

string connectionString = TokenLibrary.getSecret("<AZURE KEY VAULT NAME>", "<SECRET KEY>", "<LINKED SERVICE NAME>");
Console.WriteLine(connectionString);
```

::: zone-end

## <a name="next-steps"></a>Następne kroki

- [Zapisz w dedykowanej puli SQL](./synapse-spark-sql-pool-import-export.md)
