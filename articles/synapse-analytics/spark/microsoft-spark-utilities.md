---
title: Wprowadzenie do narzędzi Microsoft Spark Utilities
description: 'Samouczek: MSSparkutils w notesach usługi Azure Synapse Analytics'
author: ruxu
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: reference
ms.subservice: spark
ms.date: 09/10/2020
ms.author: ruxu
ms.reviewer: ''
zone_pivot_groups: programming-languages-spark-all-minus-sql
ms.openlocfilehash: d36086052f4e5719fd17989e3326a4b5728ee3ca
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954297"
---
# <a name="introduction-to-microsoft-spark-utilities"></a>Wprowadzenie do narzędzi Microsoft Spark Utilities

Microsoft Spark Utilities (MSSparkUtils) to pakiet wbudowane, który ułatwia wykonywanie typowych zadań. Programu MSSparkUtils można używać do pracy z systemami plików, uzyskiwania zmiennych środowiskowych oraz do pracy z wpisami tajnymi. MSSparkUtils są dostępne w `PySpark (Python)` , `Scala` , i w `.NET Spark (C#)` potokach Synapse.

## <a name="pre-requisites"></a>Wymagania wstępne

### <a name="configure-access-to-azure-data-lake-storage-gen2"></a>Konfigurowanie dostępu do Azure Data Lake Storage Gen2 

Notesy Synapse korzystają z usługi Azure Active Directory (Azure AD) w celu uzyskania dostępu do kont ADLS Gen2. Musisz być **Współautorem BLOB Storage** , aby uzyskać dostęp do konta ADLS Gen2 (lub folderu). 

Potoki Synapse używają tożsamości obszaru roboczego (MSI) do uzyskiwania dostępu do kont magazynu. Aby można było używać MSSparkUtils w działaniach potoku, tożsamość obszaru roboczego musi być **BLOB Storage współautor** , aby uzyskać dostęp do konta ADLS Gen2 (lub folderu).

Wykonaj następujące kroki, aby upewnić się, że usługa Azure AD i plik MSI obszaru roboczego mają dostęp do konta ADLS Gen2:
1. Otwórz [Azure Portal](https://portal.azure.com/) i konto magazynu, do którego chcesz uzyskać dostęp. Możesz przejść do określonego kontenera, do którego chcesz uzyskać dostęp.
2. Wybierz pozycję **Kontrola dostępu (IAM)** w okienku po lewej stronie.
3. Przypisz **swoje konto usługi Azure AD** i **swoją tożsamość obszaru roboczego** (tak samo jak nazwa obszaru roboczego) do roli **współautor danych obiektów blob magazynu** na koncie magazynu, jeśli nie została jeszcze przypisana. 
4. Wybierz pozycję **Zapisz**.

Dostęp do danych na ADLS Gen2 za pomocą Synapse Spark można uzyskać za pomocą następującego adresu URL:

<code>abfss://<container_name>@<storage_account_name>.dfs.core.windows.net/<path></code>

### <a name="configure-access-to-azure-blob-storage"></a>Konfigurowanie dostępu do usługi Azure Blob Storage  

Synapse wykorzystać **sygnaturę dostępu współdzielonego (SAS)** , aby uzyskać dostęp do usługi Azure Blob Storage. Aby uniknąć udostępniania kluczy SAS w kodzie, zalecamy utworzenie nowej połączonej usługi w obszarze roboczym Synapse na konto usługi Azure Blob Storage, do którego chcesz uzyskać dostęp.

Wykonaj następujące kroki, aby dodać nową połączoną usługę dla konta usługi Azure Blob Storage:

1. Otwórz [usługę Azure Synapse Studio](https://web.azuresynapse.net/).
2. Wybierz pozycję **Zarządzaj** w lewym panelu i wybierz pozycję **połączone usługi** w obszarze **połączenia zewnętrzne**.
3. Przeszukaj **BLOB Storage platformy Azure** w panelu **nowej połączonej usługi** po prawej stronie.
4. Wybierz opcję **Kontynuuj**.
5. Wybierz konto usługi Azure Blob Storage, aby uzyskać dostęp do nazwy połączonej usługi i skonfigurować ją. Zaproponuj użycie **klucza konta** dla **metody uwierzytelniania**.
6. Wybierz **Test connection** , aby sprawdzić poprawność ustawień.
7. Wybierz pozycję **Utwórz** pierwszy, a następnie kliknij pozycję **Opublikuj wszystko** , aby zapisać zmiany. 

Dostęp do danych w usłudze Azure Blob Storage przy użyciu usługi Synapse Spark można uzyskać za pomocą następującego adresu URL:

<code>wasb[s]://<container_name>@<storage_account_name>.blob.core.windows.net/<path></code>

Oto przykład kodu:

:::zone pivot = "programming-language-python"

```python
from pyspark.sql import SparkSession

# Azure storage access info
blob_account_name = 'Your account name' # replace with your blob name
blob_container_name = 'Your container name' # replace with your container name
blob_relative_path = 'Your path' # replace with your relative folder path
linked_service_name = 'Your linked service name' # replace with your linked service name

blob_sas_token = mssparkutils.credentials.getConnectionStringOrCreds(linked_service_name)

# Allow SPARK to access from Blob remotely

wasb_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)

spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name), blob_sas_token)
print('Remote blob path: ' + wasb_path)
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val blob_account_name = "" // replace with your blob name
val blob_container_name = "" //replace with your container name
val blob_relative_path = "/" //replace with your relative folder path
val linked_service_name = "" //replace with your linked service name


val blob_sas_token = mssparkutils.credentials.getConnectionStringOrCreds(linked_service_name)

val wasbs_path = f"wasbs://$blob_container_name@$blob_account_name.blob.core.windows.net/$blob_relative_path"
spark.conf.set(f"fs.azure.sas.$blob_container_name.$blob_account_name.blob.core.windows.net",blob_sas_token)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var blob_account_name = "";  // replace with your blob name
var blob_container_name = "";     // replace with your container name
var blob_relative_path = "";  // replace with your relative folder path
var linked_service_name = "";    // replace with your linked service name
var blob_sas_token = Credentials.GetConnectionStringOrCreds(linked_service_name);

spark.SparkContext.GetConf().Set($"fs.azure.sas.{blob_container_name}.{blob_account_name}.blob.core.windows.net", blob_sas_token);

var wasbs_path = $"wasbs://{blob_container_name}@{blob_account_name}.blob.core.windows.net/{blob_relative_path}";

Console.WriteLine(wasbs_path);

```

::: zone-end 
 
###  <a name="configure-access-to-azure-key-vault"></a>Konfigurowanie dostępu do Azure Key Vault

Możesz dodać Azure Key Vault jako połączoną usługę, aby zarządzać poświadczeniami w usłudze Synapse. Wykonaj następujące kroki, aby dodać Azure Key Vault jako połączoną usługę Synapse:
1. Otwórz [usługę Azure Synapse Studio](https://web.azuresynapse.net/).
2. Wybierz pozycję **Zarządzaj** w lewym panelu i wybierz pozycję **połączone usługi** w obszarze **połączenia zewnętrzne**.
3. Przeszukaj **Azure Key Vault** w panelu **nowe połączone usługi** po prawej stronie.
4. Wybierz konto Azure Key Vault, aby uzyskać dostęp do nazwy połączonej usługi i skonfigurować ją.
5. Wybierz **Test connection** , aby sprawdzić poprawność ustawień.
6. Wybierz pozycję **Utwórz** pierwszy, a następnie kliknij pozycję **Opublikuj wszystko** , aby zapisać zmiany. 

Notesy Synapse umożliwiają dostęp do Azure Key Vault za pomocą usługi Azure Active Directory (Azure AD). Potoki Synapse używają tożsamości obszaru roboczego (MSI) do uzyskiwania dostępu do Azure Key Vault. Aby upewnić się, że Twój kod będzie działał zarówno w notesie, jak i w potoku Synapse, zalecamy udzielenie uprawnień dostępu tajnego zarówno dla konta usługi Azure AD, jak i dla tożsamości obszaru roboczego.

Wykonaj następujące kroki, aby udzielić poufnego dostępu do tożsamości obszaru roboczego:
1. Otwórz [Azure Portal](https://portal.azure.com/) i Azure Key Vault, do których chcesz uzyskać dostęp. 
2. Wybierz **zasady dostępu** z lewego panelu.
3. Wybierz pozycję **Dodaj zasady dostępu**: 
    - Wybierz pozycję **klucz, klucz tajny, & zarządzanie certyfikatami** jako szablon konfiguracji.
    - Wybierz **konto usługi Azure AD** i **swoją tożsamość obszaru roboczego** (taką samą jak nazwa obszaru roboczego) w obszarze Wybierz podmiot zabezpieczeń lub upewnij się, że została już przypisana. 
4. Wybierz pozycję **Wybierz** i **Dodaj**.
5. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.  

## <a name="file-system-utilities"></a>Narzędzia systemu plików

`mssparkutils.fs` oferuje narzędzia do pracy z różnymi systemami plików, w tym Azure Data Lake Storage Gen2 (ADLS Gen2) i Blob Storage platformy Azure. Upewnij się, że masz odpowiednio skonfigurowany dostęp do [Azure Data Lake Storage Gen2](#configure-access-to-azure-data-lake-storage-gen2) i [platformy Azure Blob Storage](#configure-access-to-azure-blob-storage) .

Uruchom następujące polecenia, aby zapoznać się z omówieniem dostępnych metod:

:::zone pivot = "programming-language-python"

```python
from notebookutils import mssparkutils
mssparkutils.fs.help()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.help()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
using Microsoft.Spark.Extensions.Azure.Synapse.Analytics.Notebook.MSSparkUtils;
FS.Help()
```

::: zone-end

Wyniki w:
```
mssparkutils.fs provides utilities for working with various FileSystems.

Below is overview about the available methods:

cp(from: String, to: String, recurse: Boolean = false): Boolean -> Copies a file or directory, possibly across FileSystems
mv(from: String, to: String, recurse: Boolean = false): Boolean -> Moves a file or directory, possibly across FileSystems
ls(dir: String): Array -> Lists the contents of a directory
mkdirs(dir: String): Boolean -> Creates the given directory if it does not exist, also creating any necessary parent directories
put(file: String, contents: String, overwrite: Boolean = false): Boolean -> Writes the given String out to a file, encoded in UTF-8
head(file: String, maxBytes: int = 1024 * 100): String -> Returns up to the first 'maxBytes' bytes of the given file as a String encoded in UTF-8
append(file: String, content: String, createFileIfNotExists: Boolean): Boolean -> Append the content to a file
rm(dir: String, recurse: Boolean = false): Boolean -> Removes a file or directory

Use mssparkutils.fs.help("methodName") for more info about a method.

```

### <a name="list-files"></a>Lista plików
Wyświetl listę zawartości katalogu.


:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.ls('Your directory path')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.ls("Your directory path")
```
::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Ls("Your directory path")
```

::: zone-end


### <a name="view-file-properties"></a>Wyświetl właściwości pliku
Zwraca właściwości pliku, w tym nazwę pliku, ścieżkę pliku, rozmiar pliku i określa, czy jest to katalog i plik.

:::zone pivot = "programming-language-python"

```python
files = mssparkutils.fs.ls('Your directory path')
for file in files:
    print(file.name, file.isDir, file.isFile, file.path, file.size)
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val files = mssparkutils.fs.ls("/")
files.foreach{
    file => println(file.name,file.isDir,file.isFile,file.size)
}
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var Files = FS.Ls("/");
foreach(var File in Files) {
    Console.WriteLine(File.Name+" "+File.IsDir+" "+File.IsFile+" "+File.Size);
}
```

::: zone-end

### <a name="create-new-directory"></a>Utwórz nowy katalog

Tworzy dany katalog, jeśli nie istnieje, i wszystkie niezbędne katalogi nadrzędne.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.mkdirs('new directory name')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.mkdirs("new directory name")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Mkdirs("new directory name")
```

::: zone-end

### <a name="copy-file"></a>Kopiuj plik

Kopiuje plik lub katalog. Obsługuje kopiowanie w systemach plików.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.cp('source file or directory', 'destination file or directory', True)# Set the third parameter as True to copy all files and directories recursively
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.cp("source file or directory", "destination file or directory", true) // Set the third parameter as True to copy all files and directories recursively
```
::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Cp("source file or directory", "destination file or directory", true) // Set the third parameter as True to copy all files and directories recursively
```

::: zone-end

### <a name="preview-file-content"></a>Podgląd zawartości pliku

Zwraca do pierwszych "maxBytes" bajtów danego pliku jako ciąg zakodowany w UTF-8.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.head('file path', maxBytes to read)
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.head("file path", maxBytes to read)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Head("file path", maxBytes to read)
```

::: zone-end

### <a name="move-file"></a>Przenieś plik

Przenosi plik lub katalog. Obsługuje przenoszenie między systemami plików.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.mv('source file or directory', 'destination directory', True) # Set the last parameter as True to firstly create the parent directory if it does not exist
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.mv("source file or directory", "destination directory", true) // Set the last parameter as True to firstly create the parent directory if it does not exist
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Mv("source file or directory", "destination directory", true)
```

::: zone-end

### <a name="write-file"></a>Zapisz plik

Zapisuje dane określonego ciągu do pliku zakodowanego w formacie UTF-8.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.put("file path", "content to write", True) # Set the last parameter as True to overwrite the file if it existed already
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.put("file path", "content to write", true) // Set the last parameter as True to overwrite the file if it existed already
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Put("file path", "content to write", true) // Set the last parameter as True to overwrite the file if it existed already
```

::: zone-end

### <a name="append-content-to-a-file"></a>Dołącz zawartość do pliku

Dołącza dany ciąg do pliku zakodowanego w formacie UTF-8.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.append('file path','content to append',True) # Set the last parameter as True to create the file if it does not exist
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.append("file path","content to append",true) // Set the last parameter as True to create the file if it does not exist
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Append("file path","content to append",true) // Set the last parameter as True to create the file if it does not exist
```

::: zone-end

### <a name="delete-file-or-directory"></a>Usuń plik lub katalog

Usuwa plik lub katalog.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.rm('file path', True) # Set the last parameter as True to remove all files and directories recursively 
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.rm("file path", true) // Set the last parameter as True to remove all files and directories recursively 
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Rm("file path", true) // Set the last parameter as True to remove all files and directories recursively 
```

::: zone-end


## <a name="credentials-utilities"></a>Narzędzia poświadczeń

Za pomocą narzędzi poświadczeń MSSparkUtils można uzyskać tokeny dostępu połączonych usług i zarządzać wpisami tajnymi w Azure Key Vault. 

Uruchom następujące polecenie, aby zapoznać się z dostępnymi metodami:

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.help()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.help()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Credentials.Help()
```

::: zone-end

Pobierz wynik:

```
getToken(audience, name): returns AAD token for a given audience, name (optional)
isValidToken(token): returns true if token hasn't expired
getConnectionStringOrCreds(linkedService): returns connection string or credentials for linked service
getSecret(akvName, secret, linkedService): returns AKV secret for a given AKV linked service, akvName, secret key
getSecret(akvName, secret): returns AKV secret for a given akvName, secret key
putSecret(akvName, secretName, secretValue, linkedService): puts AKV secret for a given akvName, secretName
putSecret(akvName, secretName, secretValue): puts AKV secret for a given akvName, secretName
```

### <a name="get-token"></a>Pobierz token

Zwraca token usługi Azure AD dla danego odbiorcy, nazwę (opcjonalnie). W poniższej tabeli wymieniono wszystkie dostępne typy odbiorców: 

|Typ odbiorców|Klucz odbiorców|
|--|--|
|Typ rozpoznawania odbiorców|Publiczn|
|Zasób odbiorców magazynu|Chowan|
|Zasób odbiorców hurtowni danych|MAGAZYNU|
|Data Lake zasobów odbiorców|'AzureManagement'|
|Zasób odbiorców magazynu|Kontach datalakestore|
|Zasób odbiorcy usługi Azure OSSDB|'AzureOSSDB'|
|Zasób usługi Azure Synapse|'Synapse'|
|Zasób Azure Data Factory|ADF|

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.getToken('audience Key')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.getToken("audience Key")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Credentials.GetToken("audience Key")
```

::: zone-end


### <a name="validate-token"></a>Weryfikuj token

Zwraca wartość PRAWDA, jeśli token nie wygasł.

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.isValidToken('your token')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.isValidToken("your token")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Credentials.IsValidToken("your token")
```

::: zone-end


### <a name="get-connection-string-or-credentials-for-linked-service"></a>Pobierz parametry połączenia lub poświadczenia dla połączonej usługi

Zwraca parametry połączenia lub poświadczenia dla połączonej usługi. 

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.getConnectionStringOrCreds('linked service name')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.getConnectionStringOrCreds("linked service name")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Credentials.GetConnectionStringOrCreds("linked service name")
```

::: zone-end


### <a name="get-secret-using-workspace-identity"></a>Pobierz wpis tajny przy użyciu tożsamości obszaru roboczego

Zwraca Azure Key Vault wpis tajny dla danej nazwy Azure Key Vault, nazwy wpisu tajnego i nazwy połączonej usługi przy użyciu tożsamości obszaru roboczego. Upewnij się, że masz odpowiednio skonfigurowany dostęp do [Azure Key Vault](#configure-access-to-azure-key-vault) .

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.getSecret('azure key vault name','secret name','linked service name')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.getSecret("azure key vault name","secret name","linked service name")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Credentials.GetSecret("azure key vault name","secret name","linked service name")
```

::: zone-end


### <a name="get-secret-using-user-credentials"></a>Pobierz wpis tajny przy użyciu poświadczeń użytkownika

Zwraca Azure Key Vault klucz tajny dla danej nazwy Azure Key Vault, nazwy wpisu tajnego i nazwy połączonej usługi przy użyciu poświadczeń użytkownika. 

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.getSecret('azure key vault name','secret name')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.getSecret("azure key vault name","secret name")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Credentials.GetSecret("azure key vault name","secret name")
```

::: zone-end

<!-- ### Put secret using workspace identity

Puts Azure Key Vault secret for a given Azure Key Vault name, secret name, and linked service name using workspace identity. Make sure you configure the access to [Azure Key Vault](#configure-access-to-azure-key-vault) appropriately. -->

:::zone pivot = "programming-language-python"

### <a name="put-secret-using-workspace-identity"></a>Umieść wpis tajny przy użyciu tożsamości obszaru roboczego

Umieszcza Azure Key Vault wpis tajny dla danej nazwy Azure Key Vault, nazwy wpisu tajnego i nazwy połączonej usługi przy użyciu tożsamości obszaru roboczego. Upewnij się, że dostęp do [Azure Key Vault](#configure-access-to-azure-key-vault) jest odpowiednio skonfigurowany.

```python
mssparkutils.credentials.putSecret('azure key vault name','secret name','secret value','linked service name')
```
::: zone-end

:::zone pivot = "programming-language-scala"

### <a name="put-secret-using-workspace-identity"></a>Umieść wpis tajny przy użyciu tożsamości obszaru roboczego

Umieszcza Azure Key Vault wpis tajny dla danej nazwy Azure Key Vault, nazwy wpisu tajnego i nazwy połączonej usługi przy użyciu tożsamości obszaru roboczego. Upewnij się, że dostęp do [Azure Key Vault](#configure-access-to-azure-key-vault) jest odpowiednio skonfigurowany.

```scala
mssparkutils.credentials.putSecret("azure key vault name","secret name","secret value","linked service name")
```

::: zone-end

<!-- :::zone pivot = "programming-language-csharp"

```csharp

```

::: zone-end -->


<!-- ### Put secret using user credentials

Puts Azure Key Vault secret for a given Azure Key Vault name, secret name, and linked service name using user credentials.  -->

:::zone pivot = "programming-language-python"

### <a name="put-secret-using-user-credentials"></a>Umieść wpis tajny przy użyciu poświadczeń użytkownika

Umieszcza Azure Key Vault klucz tajny dla danej nazwy Azure Key Vault, nazwy wpisu tajnego i nazwy połączonej usługi przy użyciu poświadczeń użytkownika. 

```python
mssparkutils.credentials.putSecret('azure key vault name','secret name','secret value')
```
::: zone-end

:::zone pivot = "programming-language-scala"

### <a name="put-secret-using-user-credentials"></a>Umieść wpis tajny przy użyciu poświadczeń użytkownika

Umieszcza Azure Key Vault klucz tajny dla danej nazwy Azure Key Vault, nazwy wpisu tajnego i nazwy połączonej usługi przy użyciu poświadczeń użytkownika. 

```scala
mssparkutils.credentials.putSecret("azure key vault name","secret name","secret value")
```

::: zone-end

<!-- :::zone pivot = "programming-language-csharp"

```csharp

```

::: zone-end -->


## <a name="environment-utilities"></a>Narzędzia środowiska 

Uruchom następujące polecenia, aby zapoznać się z dostępnymi metodami:

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.help()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.help()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.Help()
```

::: zone-end

Pobierz wynik:
```
GetUserName(): returns user name
GetUserId(): returns unique user id
GetJobId(): returns job id
GetWorkspaceName(): returns workspace name
GetPoolName(): returns Spark pool name
GetClusterId(): returns cluster id
```

### <a name="get-user-name"></a>Pobierz nazwę użytkownika

Zwraca bieżącą nazwę użytkownika.

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getUserName()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getUserName()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.GetUserName()
```

::: zone-end

### <a name="get-user-id"></a>Pobierz identyfikator użytkownika

Zwraca bieżący identyfikator użytkownika.

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getUserId()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getUserId()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.GetUserId()
```

::: zone-end

### <a name="get-job-id"></a>Pobierz identyfikator zadania

Zwraca identyfikator zadania.

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getJobId()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getJobId()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.GetJobId()
```

::: zone-end

### <a name="get-workspace-name"></a>Pobierz nazwę obszaru roboczego

Zwraca nazwę obszaru roboczego.

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getWorkspaceName()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getWorkspaceName()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.GetWorkspaceName()
```

::: zone-end

### <a name="get-pool-name"></a>Pobierz nazwę puli

Zwraca nazwę puli platformy Spark.

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getPoolName()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getPoolName()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.GetPoolName()
```

::: zone-end

### <a name="get-cluster-id"></a>Pobierz identyfikator klastra

Zwraca bieżący identyfikator klastra.

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getClusterId()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getClusterId()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.GetClusterId()
```

::: zone-end

## <a name="next-steps"></a>Następne kroki

- [Zapoznaj się z przykładowymi notesami Synapse](https://github.com/Azure-Samples/Synapse/tree/master/Notebooks)
- [Szybki Start: Tworzenie puli Apache Spark w usłudze Azure Synapse Analytics przy użyciu narzędzi sieci Web](../quickstart-apache-spark-notebook.md)
- [Co to jest Apache Spark w usłudze Azure Synapse Analytics](apache-spark-overview.md)
- [Azure Synapse Analytics](../index.yml)
