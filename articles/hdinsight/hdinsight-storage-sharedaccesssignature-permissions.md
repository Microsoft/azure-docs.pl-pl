---
title: Ograniczanie dostępu przy użyciu sygnatur dostępu współdzielonego — Azure HDInsight
description: Dowiedz się, jak używać sygnatur dostępu współdzielonego w celu ograniczenia dostępu usługi HDInsight do danych przechowywanych w usłudze Azure Blob Storage.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020, devx-track-azurecli
ms.date: 04/28/2020
ms.openlocfilehash: eb8201ea888b98250d452e0b0e1c48f30cbb1efc
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96022738"
---
# <a name="use-azure-blob-storage-shared-access-signatures-to-restrict-access-to-data-in-hdinsight"></a>Używanie sygnatur dostępu współdzielonego usługi Azure Blob Storage w celu ograniczenia dostępu do danych w usłudze HDInsight

Usługa HDInsight ma pełny dostęp do danych na kontach usługi Azure Blob Storage skojarzonych z klastrem. Sygnatury dostępu współdzielonego można użyć w kontenerze obiektów BLOB w celu ograniczenia dostępu do danych. Sygnatury dostępu współdzielonego (SAS) są funkcją kont usługi Azure Blob Storage, która umożliwia ograniczenie dostępu do danych. Na przykład zapewnianie dostępu tylko do odczytu do danych.

> [!IMPORTANT]  
> W przypadku rozwiązania korzystającego z platformy Apache Ranger Rozważ użycie przyłączonej do domeny usługi HDInsight. Aby uzyskać więcej informacji, zobacz [Konfigurowanie przyłączonych do domeny](./domain-joined/apache-domain-joined-configure-using-azure-adds.md) dokumentów usługi HDInsight.

> [!WARNING]  
> Usługa HDInsight musi mieć pełny dostęp do domyślnego magazynu dla klastra.

## <a name="prerequisites"></a>Wymagania wstępne

* Klient SSH. Aby uzyskać więcej informacji, zobacz [Łączenie się z usługą HDInsight (Apache Hadoop) przy użyciu protokołu SSH](./hdinsight-hadoop-linux-use-ssh-unix.md).

* Istniejący [kontener magazynu](../storage/blobs/storage-quickstart-blobs-portal.md).  

* W przypadku korzystania z programu PowerShell należy użyć polecenia [AZ module](/powershell/azure/).

* Jeśli chcesz użyć interfejsu wiersza polecenia platformy Azure i jeszcze go nie zainstalowano, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

* W przypadku korzystania z języka [Python](https://www.python.org/downloads/)w wersji 2,7 lub nowszej.

* W przypadku korzystania z języka C# Program Visual Studio musi mieć wersję 2013 lub nowszą.

* Schemat identyfikatora URI dla konta magazynu. Ten schemat powinien dotyczyć `wasb://` usługi Azure Blob Storage, `abfs://` Azure Data Lake Storage Gen2 lub `adl://` Azure Data Lake Storage Gen1. Jeśli włączono bezpieczny transfer dla usługi Azure Blob Storage, identyfikator URI zostałby `wasbs://` .

* Istniejący klaster usługi HDInsight, do którego ma zostać dodana sygnatura dostępu współdzielonego. W przeciwnym razie można użyć Azure PowerShell do utworzenia klastra i dodania sygnatury dostępu współdzielonego podczas tworzenia klastra.

* Przykładowe pliki z [https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature) . To repozytorium zawiera następujące elementy:

  * Projekt programu Visual Studio, który umożliwia utworzenie kontenera magazynu, przechowywanych zasad i sygnatury dostępu współdzielonego w celu użycia z usługą HDInsight
  * Skrypt języka Python, który umożliwia utworzenie kontenera magazynu, przechowywanych zasad i sygnatury dostępu współdzielonego do użycia z usługą HDInsight
  * Skrypt programu PowerShell, który może utworzyć klaster usługi HDInsight i skonfigurować go do korzystania z sygnatury dostępu współdzielonego. Zaktualizowana wersja jest używana poniżej.
  * Przykładowy plik: `hdinsight-dotnet-python-azure-storage-shared-access-signature-master\sampledata\sample.log`

## <a name="shared-access-signatures"></a>Sygnatury dostępu współdzielonego

Istnieją dwie formy sygnatur dostępu współdzielonego:

* `Ad hoc`: Czas rozpoczęcia, czas wygaśnięcia i uprawnienia dla sygnatury dostępu współdzielonego są określone w identyfikatorze URI sygnatury dostępu współdzielonego.

* `Stored access policy`: Przechowywane zasady dostępu są definiowane w kontenerze zasobów, takim jak kontener obiektów BLOB. Zasady mogą służyć do zarządzania ograniczeniami dla co najmniej jednego sygnatury dostępu współdzielonego. W przypadku kojarzenia sygnatury dostępu współdzielonego z przechowywanymi zasadami dostępnymi przez skojarzenie SAS dziedziczą ograniczenia — czas rozpoczęcia, czas wygaśnięcia i uprawnienia zdefiniowane dla przechowywanych zasad dostępu.

Różnica między dwoma formularzami jest istotna dla jednego scenariusza: odwołanie. Sygnatura dostępu współdzielonego to adres URL, więc każdy, kto uzyskuje sygnaturę dostępu współdzielonego, może go użyć. Nie ma znaczenia, kto prosi o rozpoczęcie od. Jeśli sygnatura dostępu współdzielonego zostanie publicznie opublikowana, może być używana przez każdego na świecie. Dystrybuowane SYGNATURy dostępu współdzielonego są ważne do jednego z czterech rzeczy:

1. Zostanie osiągnięty czas wygaśnięcia określony w ramach sygnatury dostępu współdzielonego.

2. Osiągnięto czas wygaśnięcia określony w przechowywanych zasadach dostępu, do których odwołuje się SAS. Następujące scenariusze powodują osiągnięcie czasu wygaśnięcia:

    * Przedział czasu upłynął.
    * Zasady dostępu przechowywane są modyfikowane tak, aby zawierały czas wygaśnięcia w przeszłości. Zmiana czasu wygaśnięcia jest jednym ze sposobów odwoływania sygnatury dostępu współdzielonego.

3. Zasady dostępu przechowywane, do których odwołuje się ten SYGNATURa Jeśli ponownie utworzysz przechowywane zasady dostępu o tej samej nazwie, wszystkie tokeny sygnatury SAS dla poprzednich zasad są prawidłowe (jeśli nie przeszedł czas wygaśnięcia dla sygnatury dostępu współdzielonego). Jeśli zamierzasz odwołać sygnaturę dostępu współdzielonego, pamiętaj, aby użyć innej nazwy, jeśli w przyszłości ponownie utworzysz zasady dotyczące czasu wygaśnięcia.

4. Klucz konta użyty do utworzenia sygnatury dostępu współdzielonego jest generowany ponownie. Ponowne wygenerowanie klucza powoduje niepowodzenie uwierzytelniania wszystkich aplikacji korzystających z poprzedniego klucza. Zaktualizuj wszystkie składniki do nowego klucza.

> [!IMPORTANT]  
> Identyfikator URI sygnatury dostępu współdzielonego jest skojarzony z kluczem konta użytym do utworzenia podpisu i skojarzonymi zasadami dostępu przechowywanymi (jeśli istnieją). Jeśli nie określono żadnych przechowywanych zasad dostępu, jedynym sposobem odwoływania sygnatury dostępu współdzielonego jest zmiana klucza konta.

Zalecamy, aby zawsze używać przechowywanych zasad dostępu. W przypadku korzystania z przechowywanych zasad można odwołać podpisy lub poszerzyć datę wygaśnięcia zgodnie z wymaganiami. W procedurach przedstawionych w tym dokumencie użyto przechowywanych zasad dostępu do generowania SAS.

Aby uzyskać więcej informacji na temat sygnatur dostępu współdzielonego, zobacz [Omówienie modelu SAS](../storage/common/storage-sas-overview.md).

## <a name="create-a-stored-policy-and-sas"></a>Tworzenie zasad przechowywanych i sygnatury dostępu współdzielonego

Zapisz token sygnatury dostępu współdzielonego, który jest generowany na końcu każdej metody. Token będzie wyglądać podobnie do następującego:

```output
?sv=2018-03-28&sr=c&si=myPolicyPS&sig=NAxefF%2BrR2ubjZtyUtuAvLQgt%2FJIN5aHJMj6OsDwyy4%3D
```

### <a name="using-powershell"></a>Korzystanie z programu PowerShell

Zamień `RESOURCEGROUP` , `STORAGEACCOUNT` i `STORAGECONTAINER` z odpowiednimi wartościami dla istniejącego kontenera magazynu. Zmień katalog na `hdinsight-dotnet-python-azure-storage-shared-access-signature-master` lub skoryguj parametr, `-File` aby zawierał ścieżkę bezwzględną dla `Set-AzStorageblobcontent` . Wprowadź następujące polecenie programu PowerShell:

```powershell
$resourceGroupName = "RESOURCEGROUP"
$storageAccountName = "STORAGEACCOUNT"
$containerName = "STORAGECONTAINER"
$policy = "myPolicyPS"

# Login to your Azure subscription
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

# Get the access key for the Azure Storage account
$storageAccountKey = (Get-AzStorageAccountKey `
                                -ResourceGroupName $resourceGroupName `
                                -Name $storageAccountName)[0].Value

# Create an Azure Storage context
$storageContext = New-AzStorageContext `
                                -StorageAccountName $storageAccountName `
                                -StorageAccountKey $storageAccountKey

# Create a stored access policy for the Azure storage container
New-AzStorageContainerStoredAccessPolicy `
   -Container $containerName `
   -Policy $policy `
   -Permission "rl" `
   -ExpiryTime "12/31/2025 08:00:00" `
   -Context $storageContext

# Get the stored access policy or policies for the Azure storage container
Get-AzStorageContainerStoredAccessPolicy `
    -Container $containerName `
    -Context $storageContext

# Generates an SAS token for the Azure storage container
New-AzStorageContainerSASToken `
    -Name $containerName `
    -Policy $policy `
    -Context $storageContext

<# Removes a stored access policy from the Azure storage container
Remove-AzStorageContainerStoredAccessPolicy `
    -Container $containerName `
    -Policy $policy `
    -Context $storageContext
#>

# upload a file for a later example
Set-AzStorageblobcontent `
    -File "./sampledata/sample.log" `
    -Container $containerName `
    -Blob "samplePS.log" `
    -Context $storageContext
```

### <a name="using-azure-cli"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure

Użycie zmiennych w tej sekcji jest oparte na środowisku systemu Windows. Nieznaczne zmiany będą wymagały bash lub innych środowisk.

1. Zamień `STORAGEACCOUNT` i `STORAGECONTAINER` z odpowiednimi wartościami dla istniejącego kontenera magazynu.

    ```azurecli
    # set variables
    set AZURE_STORAGE_ACCOUNT=STORAGEACCOUNT
    set AZURE_STORAGE_CONTAINER=STORAGECONTAINER

    #Login
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription SUBSCRIPTION

    # Retrieve the primary key for the storage account
    az storage account keys list --account-name %AZURE_STORAGE_ACCOUNT% --query "[0].{PrimaryKey:value}" --output table
    ```

2. Ustaw pobrany klucz podstawowy na zmienną do późniejszego użycia. Zamień na `PRIMARYKEY` pobraną wartość w poprzednim kroku, a następnie wprowadź poniższe polecenie:

    ```console
    #set variable for primary key
    set AZURE_STORAGE_KEY=PRIMARYKEY
    ```

3. Zmień katalog na `hdinsight-dotnet-python-azure-storage-shared-access-signature-master` lub skoryguj parametr, `--file` aby zawierał ścieżkę bezwzględną dla `az storage blob upload` . Wykonaj pozostałe polecenia:

    ```azurecli
    # Create stored access policy on the containing object
    az storage container policy create --container-name %AZURE_STORAGE_CONTAINER% --name myPolicyCLI --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT% --expiry 2025-12-31 --permissions rl

    # List stored access policies on a containing object
    az storage container policy list --container-name %AZURE_STORAGE_CONTAINER% --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT%

    # Generate a shared access signature for the container
    az storage container generate-sas --name myPolicyCLI --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT%

    # Reversal
    # az storage container policy delete --container-name %AZURE_STORAGE_CONTAINER% --name myPolicyCLI --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT%

    # upload a file for a later example
    az storage blob upload --container-name %AZURE_STORAGE_CONTAINER% --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT% --name sampleCLI.log --file "./sampledata/sample.log"
    ```

### <a name="using-python"></a>Korzystanie z języka Python

Otwórz `SASToken.py` plik i Zastąp `storage_account_name` `storage_account_key` wartości, i `storage_container_name` z odpowiednimi wartościami dla istniejącego kontenera magazynu, a następnie uruchom skrypt.

`pip install --upgrade azure-storage`Jeśli zostanie wyświetlony komunikat o błędzie, może być konieczne wykonanie tej operacji `ImportError: No module named azure.storage` .

### <a name="using-c"></a>Korzystanie z języka C\#

1. Otwórz rozwiązanie w programie Visual Studio.

2. W Eksplorator rozwiązań kliknij prawym przyciskiem myszy projekt **SASExample** i wybierz polecenie **Właściwości**.

3. Wybierz pozycję **Ustawienia** i Dodaj wartości dla następujących wpisów:

    |Element |Opis |
    |---|---|
    |StorageConnectionString|Parametry połączenia dla konta magazynu, dla którego chcesz utworzyć zasady przechowywane i sygnaturę dostępu współdzielonego. W tym formacie powinna `DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey` być `myaccount` nazwa konta magazynu oraz `mykey` klucz konta magazynu.|
    |NazwaKontenera|Kontener na koncie magazynu, do którego ma zostać ograniczony dostęp.|
    |SASPolicyName|Nazwa, która ma zostać użyta do utworzenia zasad przechowywanych.|
    |FileToUpload|Ścieżka do pliku, który jest przekazywany do kontenera.|

4. Uruchom projekt. Zapisz token zasad sygnatury dostępu współdzielonego, nazwę konta magazynu i nazwę kontenera. Te wartości są używane podczas kojarzenia konta magazynu z klastrem usługi HDInsight.

## <a name="use-the-sas-with-hdinsight"></a>Używanie sygnatury dostępu współdzielonego z usługą HDInsight

Podczas tworzenia klastra usługi HDInsight należy określić podstawowe konto magazynu. Możesz również określić dodatkowe konta magazynu. Obie te metody dodawania magazynu wymagają pełnego dostępu do kont magazynu i kontenerów, które są używane.

Użyj sygnatury dostępu współdzielonego, aby ograniczyć dostęp do kontenera. Dodaj niestandardowy wpis do konfiguracji **lokacja podstawowa** dla klastra. Można dodać wpis podczas tworzenia klastra przy użyciu programu PowerShell lub po utworzeniu klastra przy użyciu Ambari.

### <a name="create-a-cluster-that-uses-the-sas"></a>Tworzenie klastra używającego sygnatury dostępu współdzielonego

Zamień `CLUSTERNAME` , `RESOURCEGROUP` , `DEFAULTSTORAGEACCOUNT` , `STORAGECONTAINER` , `STORAGEACCOUNT` i `TOKEN` z odpowiednimi wartościami. Wprowadź polecenia programu PowerShell:

```powershell
$clusterName = 'CLUSTERNAME'
$resourceGroupName = 'RESOURCEGROUP'

# Replace with the Azure data center you want to the cluster to live in
$location = 'eastus'

# Replace with the name of the default storage account TO BE CREATED
$defaultStorageAccountName = 'DEFAULTSTORAGEACCOUNT'

# Replace with the name of the SAS container CREATED EARLIER
$SASContainerName = 'STORAGECONTAINER'

# Replace with the name of the SAS storage account CREATED EARLIER
$SASStorageAccountName = 'STORAGEACCOUNT'

# Replace with the SAS token generated earlier
$SASToken = 'TOKEN'

# Default cluster size (# of worker nodes), version, and type
$clusterSizeInNodes = "4"
$clusterVersion = "3.6"
$clusterType = "Hadoop"

# Login to your Azure subscription
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

# Create an Azure Storage account and container
New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $defaultStorageAccountName `
    -Location $location `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableHttpsTrafficOnly 1

$defaultStorageAccountKey = (Get-AzStorageAccountKey `
                                -ResourceGroupName $resourceGroupName `
                                -Name $defaultStorageAccountName)[0].Value

$defaultStorageContext = New-AzStorageContext `
                                -StorageAccountName $defaultStorageAccountName `
                                -StorageAccountKey $defaultStorageAccountKey

# Create a blob container. This holds the default data store for the cluster.
New-AzStorageContainer `
    -Name $clusterName `
    -Context $defaultStorageContext

# Cluster login is used to secure HTTPS services hosted on the cluster
$httpCredential = Get-Credential `
    -Message "Enter Cluster login credentials" `
    -UserName "admin"

# SSH user is used to remotely connect to the cluster using SSH clients
$sshCredential = Get-Credential `
    -Message "Enter SSH user credentials" `
    -UserName "sshuser"

# Create the configuration for the cluster
$config = New-AzHDInsightClusterConfig

$config = $config | Add-AzHDInsightConfigValue `
    -Spark2Defaults @{} `
    -Core @{"fs.azure.sas.$SASContainerName.$SASStorageAccountName.blob.core.windows.net"=$SASToken}

# Create the HDInsight cluster
New-AzHDInsightCluster `
    -Config $config `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $clusterName `
    -Location $location `
    -ClusterSizeInNodes $clusterSizeInNodes `
    -ClusterType $clusterType `
    -OSType Linux `
    -Version $clusterVersion `
    -HttpCredential $httpCredential `
    -SshCredential $sshCredential `
    -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
    -DefaultStorageAccountKey $defaultStorageAccountKey `
    -DefaultStorageContainer $clusterName

<# REVERSAL
Remove-AzHDInsightCluster `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $clusterName

Remove-AzStorageContainer `
    -Name $clusterName `
    -Context $defaultStorageContext

Remove-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $defaultStorageAccountName

Remove-AzResourceGroup `
    -Name $resourceGroupName
#>
```

> [!IMPORTANT]  
> Gdy zostanie wyświetlony monit o podanie nazwy użytkownika i hasła protokołu SSH, należy podać hasło spełniające następujące kryteria:
>
> * Musi składać się z co najmniej 10 znaków.
> * Musi zawierać co najmniej jedną cyfrę.
> * Musi zawierać co najmniej jeden znak inny niż alfanumeryczny.
> * Musi zawierać co najmniej jedną wielką lub małą literę.

Wykonanie tego skryptu zajmuje trochę czasu, zwykle około 15 minut. Gdy skrypt zakończy się bez żadnych błędów, klaster został utworzony.

### <a name="use-the-sas-with-an-existing-cluster"></a>Używanie sygnatury dostępu współdzielonego z istniejącym klastrem

Jeśli masz istniejący klaster, możesz dodać sygnaturę dostępu współdzielonego do konfiguracji **lokacji podstawowej** , wykonując następujące czynności:

1. Otwórz interfejs użytkownika sieci Web Ambari dla klastra. Adres tej strony to `https://YOURCLUSTERNAME.azurehdinsight.net` . Po wyświetleniu monitu Uwierzytelnij się w klastrze przy użyciu nazwy administratora (administratora) i hasła użytego podczas tworzenia klastra.

1. Przejdź do **HDFS**  >  **konfiguracji** systemu plików HDFS  >  **Advanced**  >  **— Zaawansowane niestandardowe podstawowe Lokacje**.

1. Rozwiń sekcję **niestandardowa lokacja podstawowa** , przewiń do końca, a następnie wybierz pozycję **Dodaj właściwość..**.. Użyj następujących wartości **klucza** i **wartości**:

    * **Klucz**: `fs.azure.sas.CONTAINERNAME.STORAGEACCOUNTNAME.blob.core.windows.net`
    * **Wartość**: Sygnatura dostępu współdzielonego zwrócona przez jedną z metod wcześniej wykonanych.

    Zamień na `CONTAINERNAME` nazwę kontenera używaną w aplikacji C# lub SAS. Zamień `STORAGEACCOUNTNAME` na użytą nazwę konta magazynu.

    Wybierz pozycję **Dodaj** , aby zapisać ten klucz i wartość

1. Wybierz przycisk **Zapisz** , aby zapisać zmiany konfiguracji. Po wyświetleniu monitu Dodaj opis zmiany (na przykład "Dodawanie dostępu do magazynu SAS"), a następnie wybierz pozycję **Zapisz**.

    Po zakończeniu wprowadzania zmian wybierz **przycisk OK** .

   > [!IMPORTANT]  
   > Aby zmiana zacznie obowiązywać, należy ponownie uruchomić kilka usług.

1. Zostanie wyświetlona lista rozwijana **ponownego uruchomienia** . Z listy rozwijanej wybierz pozycję **Uruchom ponownie wszystkie, której dotyczy** , a następnie __Potwierdź ponowne uruchomienie wszystkich__.

    Powtórz ten proces dla **MapReduce2** i **przędzy**.

1. Po ponownym uruchomieniu usług wybierz każdą z nich i Wyłącz tryb konserwacji z listy rozwijanej **Akcje usługi** .

## <a name="test-restricted-access"></a>Testowanie ograniczonego dostępu

Wykonaj następujące kroki, aby sprawdzić, czy można tylko odczytywać i wyświetlać elementy na koncie magazynu SAS.

1. Nawiąż połączenie z klastrem. Zamień `CLUSTERNAME` na nazwę klastra, a następnie wprowadź następujące polecenie:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Aby wyświetlić listę zawartości kontenera, użyj następującego polecenia z poziomu wiersza:

    ```bash
    hdfs dfs -ls wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/
    ```

    Zamień `SASCONTAINER` na nazwę kontenera utworzonego dla konta magazynu sygnatury dostępu współdzielonego. Zamień `SASACCOUNTNAME` na nazwę konta magazynu używanego dla sygnatury dostępu współdzielonego.

    Lista zawiera plik przekazany podczas tworzenia kontenera i sygnatury dostępu współdzielonego.

3. Użyj poniższego polecenia, aby sprawdzić, czy można odczytać zawartość pliku. Zamień `SASCONTAINER` i `SASACCOUNTNAME` tak jak w poprzednim kroku. Zamień `sample.log` na nazwę pliku wyświetlanego w poprzednim poleceniu:

    ```bash
    hdfs dfs -text wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/sample.log
    ```

    To polecenie wyświetla zawartość pliku.

4. Użyj następującego polecenia, aby pobrać plik do lokalnego systemu plików:

    ```bash
    hdfs dfs -get wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/sample.log testfile.txt
    ```

    To polecenie umożliwia pobranie pliku do pliku lokalnego o nazwie **testfile.txt**.

5. Użyj poniższego polecenia, aby przekazać plik lokalny do nowego pliku o nazwie **testupload.txt** w magazynie sygnatury dostępu współdzielonego:

    ```bash
    hdfs dfs -put testfile.txt wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/testupload.txt
    ```

    Zostanie wyświetlony komunikat podobny do następującego:

    ```output
    put: java.io.IOException
    ```

    Ten błąd występuje, ponieważ lokalizacja magazynu jest tylko do odczytu i listy. Użyj poniższego polecenia, aby umieścić dane w domyślnym magazynie dla klastra, który można zapisać:

    ```bash
    hdfs dfs -put testfile.txt wasbs:///testupload.txt
    ```

    Tym razem operacja powinna zakończyć się pomyślnie.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy już wiesz, jak dodać magazyn o ograniczonym dostępie do klastra usługi HDInsight, Dowiedz się więcej na temat sposobów pracy z danymi w klastrze:

* [Używanie protokołu SSH z usługą HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Autoryzowanie użytkowników na potrzeby widoków Apache Ambari](hdinsight-authorize-users-to-ambari.md)
