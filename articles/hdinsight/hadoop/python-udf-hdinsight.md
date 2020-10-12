---
title: Python UDF z Apache Hive i Apache chlewnej — Azure HDInsight
description: Dowiedz się, jak używać funkcji języka Python zdefiniowanej przez użytkownika (UDF) z Apache Hive i Apache świni w usłudze HDInsight, stosu Apache Hadoop technologii na platformie Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 11/15/2019
ms.custom: H1Hack27Feb2017,hdinsightactive, devx-track-python
ms.openlocfilehash: 9c16b3ff013c2985ea381ed4bb002276b1c3fdb8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89462245"
---
# <a name="use-python-user-defined-functions-udf-with-apache-hive-and-apache-pig-in-hdinsight"></a>Używanie funkcji języka Python zdefiniowanej przez użytkownika (UDF) z Apache Hive i Apache chlewnej w usłudze HDInsight

Dowiedz się, jak używać funkcji języka Python zdefiniowanej przez użytkownika (UDF) z Apache Hive i Apache chlewnej w Apache Hadoop w usłudze Azure HDInsight.

## <a name="python-on-hdinsight"></a><a name="python"></a>Język Python w usłudze HDInsight

Środowisko Python 2.7 jest instalowane domyślnie w usłudze HDInsight 3,0 i nowszych. Apache Hive można używać w tej wersji języka Python do przetwarzania strumieniowego. Przetwarzanie strumienia używa STDOUT i STDIN do przekazywania danych między Hive i UDF.

Usługa HDInsight zawiera również Jython, która jest implementacją języka Python zapisaną w języku Java. Jython działa bezpośrednio na wirtualna maszyna Java i nie używa przesyłania strumieniowego. Jython jest zalecanym interpreterem języka Python w przypadku korzystania z języka Python z świnią.

## <a name="prerequisites"></a>Wymagania wstępne

* **Klaster usługi Hadoop w usłudze HDInsight**. Zobacz Rozpoczynanie [pracy z usługą HDInsight w systemie Linux](apache-hadoop-linux-tutorial-get-started.md).
* **Klient SSH**. Aby uzyskać więcej informacji, zobacz [Łączenie się z usługą HDInsight (Apache Hadoop) przy użyciu protokołu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).
* [Schemat identyfikatora URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme) magazynu podstawowego klastrów. Dotyczy to `wasb://` usługi Azure Storage, `abfs://` Azure Data Lake Storage Gen2 lub adl://dla Azure Data Lake Storage Gen1. Jeśli w usłudze Azure Storage włączono opcję bezpiecznego transferu, identyfikator URI zostałby wasbs://.  Zobacz również [bezpieczny transfer](../../storage/common/storage-require-secure-transfer.md).
* **Możliwa zmiana konfiguracji magazynu.**  Zobacz [Konfiguracja magazynu](#storage-configuration) , jeśli używany jest typ konta magazynu `BlobStorage` .
* Opcjonalny.  Jeśli planujesz korzystanie z programu PowerShell, musisz mieć zainstalowaną pozycję [AZ module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az) .

> [!NOTE]  
> Konto magazynu używane w tym artykule było magazynem platformy Azure z włączonym [bezpiecznym transferem](../../storage/common/storage-require-secure-transfer.md) i w tym przypadku `wasbs` jest używany w całym artykule.

## <a name="storage-configuration"></a>Konfiguracja usługi Storage

Jeśli używane konto magazynu ma charakter lub, nie jest wymagana żadna `Storage (general purpose v1)` Akcja `StorageV2 (general purpose v2)` .  Proces w tym artykule spowoduje wygenerowanie danych wyjściowych do co najmniej `/tezstaging` .  Domyślna konfiguracja usługi Hadoop będzie zawierać `/tezstaging` w `fs.azure.page.blob.dir` zmiennej konfiguracyjnej programu w programie `core-site.xml` for `HDFS` .  Ta konfiguracja powoduje, że dane wyjściowe do katalogu będą stronicowymi obiektami BLOB, które nie są obsługiwane dla rodzaju konta magazynu `BlobStorage` .  Aby użyć `BlobStorage` tego artykułu, Usuń `/tezstaging` ze `fs.azure.page.blob.dir` zmiennej konfiguracyjnej.  Dostęp do konfiguracji można uzyskać za pomocą [interfejsu użytkownika Ambari](../hdinsight-hadoop-manage-ambari.md).  W przeciwnym razie zostanie wyświetlony komunikat o błędzie: `Page blob is not supported for this account type.`

> [!WARNING]  
> Kroki opisane w tym dokumencie składają się z następujących założeń:  
>
> * Tworzysz skrypty języka Python w lokalnym środowisku programistycznym.
> * Skrypty są przekazywane do usługi HDInsight przy użyciu `scp` polecenia lub dostarczonego skryptu programu PowerShell.
>
> Jeśli chcesz użyć [Azure Cloud Shell (bash)](https://docs.microsoft.com/azure/cloud-shell/overview) do pracy z usługą HDInsight, musisz:
>
> * Utwórz skrypty w środowisku usługi Cloud Shell.
> * Służy `scp` do przekazywania plików z usługi Cloud Shell do usługi HDInsight.
> * Użyj usługi `ssh` Cloud Shell, aby nawiązać połączenie z usługą HDInsight i uruchomić przykłady.

## <a name="apache-hive-udf"></a><a name="hivepython"></a>Apache Hive UDF

Język Python może być używany jako UDF z usługi Hive za pośrednictwem `TRANSFORM` instrukcji HiveQL. Na przykład następujący HiveQL wywołuje `hiveudf.py` plik przechowywany na domyślnym koncie usługi Azure Storage dla klastra.

```hiveql
add file wasbs:///hiveudf.py;

SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'python hiveudf.py' AS
    (clientid string, phoneLabel string, phoneHash string)
FROM hivesampletable
ORDER BY clientid LIMIT 50;
```

Oto co to jest w tym przykładzie:

1. `add file`Instrukcja na początku pliku dodaje `hiveudf.py` plik do rozproszonej pamięci podręcznej, dzięki czemu jest dostępny dla wszystkich węzłów w klastrze.
2. `SELECT TRANSFORM ... USING`Instrukcja wybiera dane z `hivesampletable` . Przekazuje także wartości ClientID, devicemake i devicemodel do `hiveudf.py` skryptu.
3. `AS`Klauzula opisuje pola zwracane z `hiveudf.py` .

<a name="streamingpy"></a>

### <a name="create-file"></a>Utwórz plik

W środowisku deweloperskim Utwórz plik tekstowy o nazwie `hiveudf.py` . Użyj następującego kodu jako zawartości pliku:

```python
#!/usr/bin/env python
import sys
import string
import hashlib

while True:
    line = sys.stdin.readline()
    if not line:
        break

    line = string.strip(line, "\n ")
    clientid, devicemake, devicemodel = string.split(line, "\t")
    phone_label = devicemake + ' ' + devicemodel
    print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])
```

Ten skrypt wykonuje następujące czynności:

1. Odczytuje wiersz danych z STDIN.
2. Końcowy znak nowego wiersza jest usuwany przy użyciu `string.strip(line, "\n ")` .
3. Podczas przetwarzania strumieniowego pojedynczy wiersz zawiera wszystkie wartości z znakiem tabulacji między każdą wartością. `string.split(line, "\t")`Można go użyć do podziału danych wejściowych na każdej karcie, zwracając tylko pola.
4. Po zakończeniu przetwarzania dane wyjściowe muszą być zapisywane w strumieniu STDOUT jako jeden wiersz, z kartą między każdym polem. Na przykład `print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])`.
5. `while`Pętla powtarza się, dopóki nie `line` zostanie odczytana.

Dane wyjściowe skryptu są połączeniem wartości wejściowych dla `devicemake` i i `devicemodel` skrótu połączonej wartości.

### <a name="upload-file-shell"></a>Przekaż plik (Shell)

W poniższych poleceniach Zastąp `sshuser` wartość rzeczywistą nazwą użytkownika, jeśli jest inna.  Zamień `mycluster` na rzeczywistą nazwę klastra.  Upewnij się, że w katalogu roboczym znajduje się plik.

1. Użyj `scp` , aby skopiować pliki do klastra usługi HDInsight. Edytuj i wprowadź poniższe polecenie:

    ```cmd
    scp hiveudf.py sshuser@mycluster-ssh.azurehdinsight.net:
    ```

2. Połącz się z klastrem przy użyciu protokołu SSH.  Edytuj i wprowadź poniższe polecenie:

    ```cmd
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

3. W sesji SSH Dodaj pliki języka Python przekazane wcześniej do magazynu klastra.

    ```bash
    hdfs dfs -put hiveudf.py /hiveudf.py
    ```

### <a name="use-hive-udf-shell"></a>Korzystanie z funkcji UDF (Shell)

1. Aby nawiązać połączenie z usługą Hive, użyj następującego polecenia z otwartej sesji SSH:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

    To polecenie uruchamia klienta programu Z usługi Beeline.

2. W wierszu polecenia wprowadź następujące zapytanie `0: jdbc:hive2://headnodehost:10001/>` :

   ```hive
   add file wasbs:///hiveudf.py;
   SELECT TRANSFORM (clientid, devicemake, devicemodel)
       USING 'python hiveudf.py' AS
       (clientid string, phoneLabel string, phoneHash string)
   FROM hivesampletable
   ORDER BY clientid LIMIT 50;
   ```

3. Po wprowadzeniu ostatniego wiersza zadanie powinno zostać uruchomione. Po zakończeniu zadania zwraca dane wyjściowe podobne do poniższego przykładu:

    ```output
    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    ```

4. Aby wyjść z Z usługi Beeline, wprowadź następujące polecenie:

    ```hive
    !q
    ```

### <a name="upload-file-powershell"></a>Przekaż plik (PowerShell)

Program PowerShell może również służyć do zdalnego uruchamiania zapytań Hive. Upewnij się, że katalog roboczy znajduje się w lokalizacji `hiveudf.py` .  Użyj poniższego skryptu programu PowerShell, aby uruchomić zapytanie programu Hive korzystające ze `hiveudf.py` skryptu:

```PowerShell
# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

# Revise file path as needed
$pathToStreamingFile = ".\hiveudf.py"

# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$clusterInfo = Get-AzHDInsightCluster -ClusterName $clusterName
$resourceGroup = $clusterInfo.ResourceGroup
$storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
$container=$clusterInfo.DefaultStorageContainer
$storageAccountKey=(Get-AzStorageAccountKey `
   -ResourceGroupName $resourceGroup `
   -Name $storageAccountName)[0].Value

# Create an Azure Storage context
$context = New-AzStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey $storageAccountKey

# Upload local files to an Azure Storage blob
Set-AzStorageBlobContent `
    -File $pathToStreamingFile `
    -Blob "hiveudf.py" `
    -Container $container `
    -Context $context
```

> [!NOTE]  
> Aby uzyskać więcej informacji na temat przekazywania plików, zobacz sekcję [przekazywanie danych dla Apache Hadoop zadań w usłudze HDInsight](../hdinsight-upload-data.md) .

#### <a name="use-hive-udf"></a>Korzystanie z funkcji UDF w programie Hive

```PowerShell
# Script should stop on failures
$ErrorActionPreference = "Stop"

# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$creds=Get-Credential -UserName "admin" -Message "Enter the login for the cluster"

$HiveQuery = "add file wasbs:///hiveudf.py;" +
                "SELECT TRANSFORM (clientid, devicemake, devicemodel) " +
                "USING 'python hiveudf.py' AS " +
                "(clientid string, phoneLabel string, phoneHash string) " +
                "FROM hivesampletable " +
                "ORDER BY clientid LIMIT 50;"

# Create Hive job object
$jobDefinition = New-AzHDInsightHiveJobDefinition `
    -Query $HiveQuery

# For status bar updates
$activity="Hive query"

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Starting query..."

# Start defined Azure HDInsight job on specified cluster.
$job = Start-AzHDInsightJob `
    -ClusterName $clusterName `
    -JobDefinition $jobDefinition `
    -HttpCredential $creds

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Waiting on query to complete..."

# Wait for completion or failure of specified job
Wait-AzHDInsightJob `
    -JobId $job.JobId `
    -ClusterName $clusterName `
    -HttpCredential $creds

# Uncomment the following to see stderr output
<#
Get-AzHDInsightJobOutput `
   -Clustername $clusterName `
   -JobId $job.JobId `
   -HttpCredential $creds `
   -DisplayOutputType StandardError
#>

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Retrieving output..."

# Gets the log output
Get-AzHDInsightJobOutput `
    -Clustername $clusterName `
    -JobId $job.JobId `
    -HttpCredential $creds
```

Dane wyjściowe zadania **Hive** powinny wyglądać podobnie do poniższego przykładu:

```output
100041    RIM 9650    d476f3687700442549a83fac4560c51c
100041    RIM 9650    d476f3687700442549a83fac4560c51c
100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
```

## <a name="apache-pig-udf"></a><a name="pigpython"></a>UDF świni Apache

Skrypt w języku Python może być używany jako UDF ze świń za pośrednictwem `GENERATE` instrukcji. Skrypt można uruchomić przy użyciu języka Python Jython lub C.

* Jython działa na JVM i może być natywnie wywoływany ze świni.
* Język C Python jest procesem zewnętrznym, dlatego dane ze świń w JVM są wysyłane do skryptu działającego w procesie języka Python. Dane wyjściowe skryptu języka Python są wysyłane z powrotem do świni.

Aby określić interpreter języka Python, użyj go `register` podczas odwoływania się do skryptu języka Python. Poniższe przykłady rejestrują skrypty ze świnią jako `myfuncs` :

* **Aby użyć Jython**: `register '/path/to/pigudf.py' using jython as myfuncs;`
* **Aby użyć języka C Python**: `register '/path/to/pigudf.py' using streaming_python as myfuncs;`

> [!IMPORTANT]  
> W przypadku korzystania z Jython ścieżka do pliku pig_jython może być ścieżką lokalną lub ścieżką WASBS://. Jednak w przypadku korzystania z języka C Python należy odwołać się do pliku w lokalnym systemie plików węzła, który jest używany do przesyłania danego zadania.

Po zakończeniu rejestracji świnia jest taka sama dla obu następujących elementów:

```pig
LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
LOG = FILTER LOGS by LINE is not null;
DETAILS = FOREACH LOG GENERATE myfuncs.create_structure(LINE);
DUMP DETAILS;
```

Oto co to jest w tym przykładzie:

1. Pierwszy wiersz ładuje przykładowy plik danych `sample.log` do `LOGS` . Definiuje również każdy rekord jako `chararray` .
2. Następny wiersz filtruje wartości null, przechowując wynik operacji w `LOG` .
3. Następnie wykonuje iterację w odniesieniu do rekordów w `LOG` i używa `GENERATE` do wywołania `create_structure` metody zawartej w skrypcie Python/Jython załadowanym jako `myfuncs` . `LINE` służy do przekazywania bieżącego rekordu do funkcji.
4. Na koniec dane wyjściowe są zrzucane do strumienia STDOUT przy użyciu `DUMP` polecenia. To polecenie wyświetla wyniki po zakończeniu operacji.

### <a name="create-file"></a>Utwórz plik

W środowisku deweloperskim Utwórz plik tekstowy o nazwie `pigudf.py` . Użyj następującego kodu jako zawartości pliku:

<a name="streamingpy"></a>

```python
# Uncomment the following if using C Python
#from pig_util import outputSchema


@outputSchema("log: {(date:chararray, time:chararray, classname:chararray, level:chararray, detail:chararray)}")
def create_structure(input):
    if (input.startswith('java.lang.Exception')):
        input = input[21:len(input)] + ' - java.lang.Exception'
    date, time, classname, level, detail = input.split(' ', 4)
    return date, time, classname, level, detail
```

W przykładzie dla świni, `LINE` dane wejściowe są zdefiniowane jako CharArray, ponieważ nie istnieje spójny schemat danych wejściowych. Skrypt języka Python przekształca dane w spójny schemat dla danych wyjściowych.

1. `@outputSchema`Instrukcja definiuje format danych, które są zwracane do świni. W tym przypadku jest to **zbiór danych**, który jest typem danych świni. Zbiór zawiera następujące pola, z których wszystkie są CharArray (ciągi):

   * Data — Data utworzenia wpisu dziennika.
   * godzina — godzina utworzenia wpisu dziennika.
   * ClassName — nazwa klasy, dla której utworzono wpis
   * poziom — poziom rejestrowania
   * Szczegóły — szczegóły wpisu dziennika

2. Następnie `def create_structure(input)` definiuje funkcję, do której świnie przechodzą elementy wiersza.

3. Przykładowe dane, `sample.log` , głównie zgodne ze schematem daty, godziny, ClassName, poziomu i szczegółów. Zawiera on jednak kilka wierszy, które zaczynają się od `*java.lang.Exception*` . Te wiersze należy zmodyfikować, aby pasowały do schematu. `if`Instrukcja sprawdza dla tych, a następnie komunikaty dane wejściowe w celu przeniesienia ciągu do końca, co powoduje przełączenie `*java.lang.Exception*` danych w wierszu o oczekiwanym schemacie danych wyjściowych.

4. Następnie `split` polecenie jest używane do dzielenia danych na pierwsze cztery znaki spacji. Dane wyjściowe są przypisywane do,,, `date` `time` `classname` `level` , i `detail` .

5. Na koniec wartości są zwracane do świni.

Gdy dane są zwracane do świni, ma spójny schemat, zgodnie z definicją w `@outputSchema` instrukcji.

### <a name="upload-file-shell"></a>Przekaż plik (Shell)

W poniższych poleceniach Zastąp `sshuser` wartość rzeczywistą nazwą użytkownika, jeśli jest inna.  Zamień `mycluster` na rzeczywistą nazwę klastra.  Upewnij się, że w katalogu roboczym znajduje się plik.

1. Użyj `scp` , aby skopiować pliki do klastra usługi HDInsight. Edytuj i wprowadź poniższe polecenie:

    ```cmd
    scp pigudf.py sshuser@mycluster-ssh.azurehdinsight.net:
    ```

2. Połącz się z klastrem przy użyciu protokołu SSH.  Edytuj i wprowadź poniższe polecenie:

    ```cmd
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

3. W sesji SSH Dodaj pliki języka Python przekazane wcześniej do magazynu klastra.

    ```bash
    hdfs dfs -put pigudf.py /pigudf.py
    ```

### <a name="use-pig-udf-shell"></a>Korzystanie z funkcji UDF (Shell)

1. Aby nawiązać połączenie z świnią, użyj następującego polecenia z otwartej sesji SSH:

    ```bash
    pig
    ```

2. W wierszu polecenia wprowadź następujące instrukcje `grunt>` :

   ```pig
   Register wasbs:///pigudf.py using jython as myfuncs;
   LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

3. Po wprowadzeniu poniższego wiersza zadania powinny zostać uruchomione. Po zakończeniu zadania zwraca dane wyjściowe podobne do następujących:

    ```output
    ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
    ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
    ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
    ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
    ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))
    ```

4. Użyj polecenia `quit` , aby wyjść z powłoki grunt, a następnie użyć następujących elementów do edytowania pliku pigudf.py w lokalnym systemie plików:

    ```bash
    nano pigudf.py
    ```

5. W edytorze Usuń komentarz z poniższego wiersza, usuwając `#` znak z początku wiersza:

    ```bash
    #from pig_util import outputSchema
    ```

    Ten wiersz modyfikuje skrypt języka Python, aby działał z językiem Python języka C zamiast Jython. Po dokonaniu zmiany **naciśnij klawisze Ctrl + X** , aby wyjść z edytora. Wybierz pozycję **Y**, a następnie **wprowadź** polecenie, aby zapisać zmiany.

6. Użyj `pig` polecenia, aby ponownie uruchomić powłokę. Po wyświetleniu `grunt>` monitu użyj poniższego polecenia, aby uruchomić skrypt języka Python przy użyciu interpretera języka C Python.

   ```pig
   Register 'pigudf.py' using streaming_python as myfuncs;
   LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

    Po zakończeniu tego zadania powinny zostać wyświetlone te same dane wyjściowe jak w przypadku, gdy skrypt został wcześniej uruchomiony przy użyciu Jython.

### <a name="upload-file-powershell"></a>Przekaż plik (PowerShell)

Program PowerShell może również służyć do zdalnego uruchamiania zapytań Hive. Upewnij się, że katalog roboczy znajduje się w lokalizacji `pigudf.py` .  Użyj poniższego skryptu programu PowerShell, aby uruchomić zapytanie programu Hive korzystające ze `pigudf.py` skryptu:

```PowerShell
# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

# Revise file path as needed
$pathToJythonFile = ".\pigudf.py"


# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$clusterInfo = Get-AzHDInsightCluster -ClusterName $clusterName
$resourceGroup = $clusterInfo.ResourceGroup
$storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
$container=$clusterInfo.DefaultStorageContainer
$storageAccountKey=(Get-AzStorageAccountKey `
   -ResourceGroupName $resourceGroup `
   -Name $storageAccountName)[0].Value

# Create an Azure Storage context
$context = New-AzStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey $storageAccountKey

# Upload local files to an Azure Storage blob
Set-AzStorageBlobContent `
    -File $pathToJythonFile `
    -Blob "pigudf.py" `
    -Container $container `
    -Context $context
```

### <a name="use-pig-udf-powershell"></a>Korzystanie z funkcji UDF (PowerShell) dla trzody chlewnej

> [!NOTE]  
> Podczas zdalnego przesyłania zadania przy użyciu programu PowerShell nie jest możliwe używanie języka C Python jako interpretera.

Program PowerShell może również służyć do uruchamiania zadań w postaci surówki. Aby uruchomić zadanie w postaci wieprzowiny, które używa `pigudf.py` skryptu, użyj następującego skryptu programu PowerShell:

```PowerShell
# Script should stop on failures
$ErrorActionPreference = "Stop"

# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$creds=Get-Credential -UserName "admin" -Message "Enter the login for the cluster"


$PigQuery = "Register wasbs:///pigudf.py using jython as myfuncs;" +
            "LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);" +
            "LOG = FILTER LOGS by LINE is not null;" +
            "DETAILS = foreach LOG generate myfuncs.create_structure(LINE);" +
            "DUMP DETAILS;"

# Create Pig job object
$jobDefinition = New-AzHDInsightPigJobDefinition -Query $PigQuery

# For status bar updates
$activity="Pig job"

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Starting job..."

# Start defined Azure HDInsight job on specified cluster.
$job = Start-AzHDInsightJob `
    -ClusterName $clusterName `
    -JobDefinition $jobDefinition `
    -HttpCredential $creds

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Waiting for the Pig job to complete..."

# Wait for completion or failure of specified job
Wait-AzHDInsightJob `
    -Job $job.JobId `
    -ClusterName $clusterName `
    -HttpCredential $creds

# Uncomment the following to see stderr output
<#
Get-AzHDInsightJobOutput `
    -Clustername $clusterName `
    -JobId $job.JobId `
    -HttpCredential $creds `
    -DisplayOutputType StandardError
#>

# Progress bar (optional)
Write-Progress -Activity $activity "Retrieving output..."

# Gets the log output
Get-AzHDInsightJobOutput `
    -Clustername $clusterName `
    -JobId $job.JobId `
    -HttpCredential $creds
```

Dane wyjściowe dla zadania **świni** powinny wyglądać podobnie do następujących:

```output
((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))
```

## <a name="troubleshooting"></a><a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="errors-when-running-jobs"></a>Błędy podczas uruchamiania zadań

Podczas uruchamiania zadania programu Hive może wystąpić błąd podobny do następującego:

```output
Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: [Error 20001]: An error occurred while reading or writing to your custom script. It may have crashed with an error.
```

Przyczyną tego problemu może być zakończenie wierszy w pliku języka Python. Wiele edytorów systemu Windows domyślnie używa CRLF jako końca wiersza, ale aplikacje systemu Linux zazwyczaj oczekują LF.

Aby usunąć znaki CR przed przekazaniem pliku do usługi HDInsight, można użyć następujących instrukcji programu PowerShell:

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=148-150)]

### <a name="powershell-scripts"></a>Skrypty środowiska PowerShell

Oba przykładowe skrypty programu PowerShell służące do uruchamiania przykładów zawierają wiersz z komentarzem, który wyświetla dane wyjściowe dla tego zadania. Jeśli nie widzisz oczekiwanych danych wyjściowych dla zadania, Usuń komentarz z poniższego wiersza i sprawdź, czy informacje o błędzie wskazują na problem.

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=135-139)]

Informacje o błędzie (STDERR) i wynik zadania (STDOUT) są również rejestrowane w magazynie usługi HDInsight.

| Dla tego zadania... | Przyjrzyj się tym plikom w kontenerze obiektów BLOB |
| --- | --- |
| Hive |/HivePython/stderr<p>/HivePython/stdout |
| Pig |/PigPython/stderr<p>/PigPython/stdout |

## <a name="next-steps"></a><a name="next"></a>Następne kroki

Jeśli zachodzi potrzeba załadowania modułów języka Python, które nie są udostępniane domyślnie, zobacz [jak wdrożyć moduł w usłudze Azure HDInsight](https://docs.microsoft.com/archive/blogs/benjguin/how-to-deploy-a-python-module-to-windows-azure-hdinsight).

Aby dowiedzieć się więcej na temat korzystania z programu MapReduce, zobacz następujące dokumenty:

* [Korzystanie z programu Apache Hive z usługą HDInsight](hdinsight-use-hive.md)
* [Korzystanie z MapReduce z usługą HDInsight](hdinsight-use-mapreduce.md)
