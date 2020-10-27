---
title: Używanie Data Lake Storage Gen1 z usługą Hadoop w usłudze Azure HDInsight
description: Dowiedz się, jak wykonywać zapytania dotyczące danych z Azure Data Lake Storage Gen1 i przechowywać wyniki analizy.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 04/24/2020
ms.openlocfilehash: 5949bab7bdf11b11e0ff71f9054098ed83d95ab4
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2020
ms.locfileid: "92539840"
---
# <a name="use-data-lake-storage-gen1-with-azure-hdinsight-clusters"></a>Używanie Data Lake Storage Gen1 z klastrami usługi Azure HDInsight

> [!Note]
> Wdróż nowe klastry usługi HDInsight przy użyciu [Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md) , aby zwiększyć wydajność i nowe funkcje.

Aby analizować dane w klastrze usługi HDInsight, możesz przechowywać dane w [`Azure Blob storage`](../storage/common/storage-introduction.md) , [Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md)lub [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md). Wszystkie opcje magazynu umożliwiają bezpieczne usuwanie klastrów usługi HDInsight, które są używane do obliczeń bez utraty danych użytkownika.

W tym artykule dowiesz się, jak Data Lake Storage Gen1 współpracuje z klastrami usługi HDInsight. Aby dowiedzieć się, jak usługa Azure Blob Storage współpracuje z klastrami HDInsight, zobacz [Korzystanie z usługi Azure Blob Storage z klastrami usługi Azure HDInsight](hdinsight-hadoop-use-blob-storage.md). Aby uzyskać więcej informacji na temat tworzenia klastra usługi HDInsight, zobacz [Tworzenie klastrów Apache Hadoop w usłudze HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

> [!NOTE]  
> Dostęp do Data Lake Storage Gen1 jest zawsze uzyskiwany za pośrednictwem bezpiecznego kanału, dlatego nie istnieje `adls` Nazwa schematu systemu plików. Zawsze używaj nazwy `adl`.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="availability-for-hdinsight-clusters"></a>Dostępność klastrów usługi HDInsight

Apache Hadoop obsługuje pojęcie domyślnego systemu plików. Domyślny system plików wyznacza domyślny schemat i element authority. Może również służyć do rozpoznawania ścieżek względnych. Podczas procesu tworzenia klastra usługi HDInsight jako domyślny system plików należy określić kontener obiektów BLOB w usłudze Azure Storage. Lub z usługą HDInsight 3,5 i nowszymi wersjami można wybrać usługę Azure Blob Storage lub Azure Data Lake Storage Gen1 jako domyślny system plików z kilkoma wyjątkami. Klaster i konto magazynu muszą być hostowane w tym samym regionie.

Klastry usługi HDInsight mogą używać Data Lake Storage Gen1 na dwa sposoby:

* Jako magazyn domyślny
* Jako dodatkowy magazyn z magazynem obiektów blob platformy Azure jako magazynem domyślnym.

Obecnie tylko niektóre typy/wersje klastrów usługi HDInsight obsługują używanie Data Lake Storage Gen1 jako magazynu domyślnego i dodatkowych kont magazynu:

| Typ klastra usługi HDInsight | Data Lake Storage Gen1 jako magazyn domyślny | Data Lake Storage Gen1 jako dodatkowy magazyn| Uwagi |
|------------------------|------------------------------------|---------------------------------------|------|
| HDInsight w wersji 4,0 | Nie | Nie |ADLS Gen1 nie jest obsługiwana w usłudze HDInsight 4,0 |
| HDInsight w wersji 3.6 | Tak | Tak | Z wyjątkiem HBase|
| HDInsight w wersji 3.5 | Tak | Tak | Z wyjątkiem HBase|
| HDInsight w wersji 3.4 | Nie | Tak | |
| HDInsight w wersji 3.3 | Nie | Nie | |
| HDInsight w wersji 3.2 | Nie | Tak | |
| Storm | | |Za pomocą Data Lake Storage Gen1 można pisać dane z topologii burzowej. Data Lake Storage Gen1 można również użyć do danych referencyjnych, które mogą być następnie odczytywane przez topologię burzy.|

> [!WARNING]  
> Usługa HDInsight HBase nie jest obsługiwana w przypadku Azure Data Lake Storage Gen1

Używanie Data Lake Storage Gen1 jako dodatkowego konta magazynu nie ma wpływu na wydajność. Lub możliwość odczytu lub zapisu w magazynie obiektów blob platformy Azure z klastra.

## <a name="use-data-lake-storage-gen1-as-default-storage"></a>Użyj Data Lake Storage Gen1 jako magazynu domyślnego

Po wdrożeniu usługi HDInsight z Data Lake Storage Gen1 jako magazynem domyślnym pliki związane z klastrem są przechowywane w `adl://mydatalakestore/<cluster_root_path>/` lokalizacji, gdzie `<cluster_root_path>` jest nazwą folderu utworzonego w Data Lake Storage. Określając ścieżkę główną dla każdego klastra, możesz użyć tego samego konta Data Lake Storage dla więcej niż jednego klastra. Dlatego jest możliwa następująca konfiguracja:

* Klaster1 może używać ścieżki `adl://mydatalakestore/cluster1storage`
* Klaster2 może używać ścieżki `adl://mydatalakestore/cluster2storage`

Zwróć uwagę, że oba klastry używają tego samego konta Data Lake Storage Gen1 **mydatalakestore** . Każdy klaster ma dostęp do własnego głównego systemu plików w Data Lake Storage. W środowisku wdrażania Azure Portal zostanie wyświetlony komunikat z prośbą o użycie nazwy folderu, takiej jak **/Clusters/ \<clustername>** , jako ścieżki głównej.

Aby użyć Data Lake Storage Gen1 jako magazynu domyślnego, należy przyznać jednostce usługi dostęp do następujących ścieżek:

* Katalog główny konta Data Lake Storage Gen1.  na przykład: adl://mydatalakestore/.
* Folder zawierający wszystkie foldery klastra,  na przykład: adl://mydatalakestore/clusters.
* Folder klastra,  na przykład: adl://mydatalakestore/clusters/cluster1storage.

Aby uzyskać więcej informacji na temat tworzenia nazwy głównej usługi i udzielania dostępu, zobacz Konfigurowanie dostępu Data Lake Storage.

### <a name="extracting-a-certificate-from-azure-keyvault-for-use-in-cluster-creation"></a>Wyodrębnianie certyfikatu z magazynu kluczy platformy Azure do użycia podczas tworzenia klastra

Jeśli certyfikat dla jednostki usługi jest przechowywany w Azure Key Vault, należy przekonwertować certyfikat do poprawnego formatu. Poniższe fragmenty kodu pokazują, jak wykonać konwersję.

Najpierw Pobierz certyfikat z Key Vault i Wyodrębnij `SecretValueText` .

```powershell
$certPassword = Read-Host "Enter Certificate Password"
$cert = (Get-AzureKeyVaultSecret -VaultName 'MY-KEY-VAULT' -Name 'MY-SECRET-NAME')
$certValue = [System.Convert]::FromBase64String($cert.SecretValueText)
```

Następnie przekonwertuj `SecretValueText` na certyfikat.

```powershell
$certObject = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList $certValue,$null,"Exportable, PersistKeySet"
$certBytes = $certObject.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword.SecretValueText);
$identityCertificate = [System.Convert]::ToBase64String($certBytes)
```

Następnie można użyć programu, `$identityCertificate` Aby wdrożyć nowy klaster jak w poniższym fragmencie kodu:

```powershell
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $pathToArmTemplate `
    -identityCertificate $identityCertificate `
    -identityCertificatePassword $certPassword.SecretValueText `
    -clusterName  $clusterName `
    -clusterLoginPassword $SSHpassword `
    -sshPassword $SSHpassword `
    -servicePrincipalApplicationId $application.ApplicationId
```

## <a name="use-data-lake-storage-gen1-as-additional-storage"></a>Użyj Data Lake Storage Gen1 jako dodatkowego magazynu

Dla klastra można również użyć Data Lake Storage Gen1 jako dodatkowego magazynu. W takich przypadkach domyślnym magazynem klastra może być usługa Azure Blob Storage lub konto Azure Data Lake Storage Gen1. Podczas uruchamiania zadań usługi HDInsight względem danych przechowywanych w Azure Data Lake Storage Gen1 jako dodatkowy magazyn należy użyć w pełni kwalifikowanej ścieżki. Na przykład:

`adl://mydatalakestore.azuredatalakestore.net/<file_path>`

W adresie URL nie ma teraz **cluster_root_path** . Dzieje się tak, ponieważ w tym przypadku Data Lake Storage nie jest domyślnym magazynem. Dlatego wszystko, co należy zrobić, zapewnia ścieżkę do plików.

Aby użyć Data Lake Storage Gen1 jako dodatkowego magazynu, należy przyznać jednostce usługi dostęp do ścieżek, w których są przechowywane pliki.  Na przykład:

`adl://mydatalakestore.azuredatalakestore.net/<file_path>`

Aby uzyskać więcej informacji na temat tworzenia nazwy głównej usługi i udzielania dostępu, zobacz Konfigurowanie dostępu Data Lake Storage.

## <a name="use-more-than-one-data-lake-storage-gen1-account"></a>Użyj więcej niż jednego konta Data Lake Storage Gen1

Dodanie konta Data Lake Storage jako dodatkowego i dodanie więcej niż jednego konta Data Lake Storage może być wykonywane. Nadaj klastrowi usługi HDInsight uprawnienia do danych na co najmniej jednym koncie Data Lake Storage. Zobacz Konfigurowanie dostępu Data Lake Storage Gen1 poniżej.

## <a name="configure-data-lake-storage-gen1-access"></a>Konfigurowanie dostępu Data Lake Storage Gen1

Aby skonfigurować dostęp Azure Data Lake Storage Gen1 z klastra usługi HDInsight, musisz mieć nazwę główną usług Azure Active Directory (Azure AD). Tylko administrator usługi Azure AD może utworzyć jednostkę usługi. Jednostkę usługi należy utworzyć przy użyciu certyfikatu. Aby uzyskać więcej informacji, zobacz [Szybki start: konfigurowanie klastrów w usłudze HDInsight](./hdinsight-hadoop-provision-linux-clusters.md) i [Tworzenie jednostki usługi przy użyciu certyfikatu z podpisem własnym](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate).

> [!NOTE]  
> Jeśli zamierzasz używać Azure Data Lake Storage Gen1 jako dodatkowego magazynu dla klastra usługi HDInsight, zdecydowanie zalecamy wykonanie tej czynności podczas tworzenia klastra zgodnie z opisem w tym artykule. Dodawanie Azure Data Lake Storage Gen1 jako dodatkowego magazynu do istniejącego klastra usługi HDInsight nie jest obsługiwanym scenariuszem.

Aby uzyskać więcej informacji na temat modelu kontroli dostępu, zobacz [Kontrola dostępu w Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md).

## <a name="access-files-from-the-cluster"></a>Dostęp do plików z klastra

Istnieje kilka sposobów uzyskiwania dostępu do plików w Data Lake Storage z klastra usługi HDInsight.

* **Przy użyciu w pełni kwalifikowanej nazwy** . W przypadku tej metody należy podać pełną ścieżkę do pliku, do którego chcesz uzyskać dostęp.

    ```
    adl://<data_lake_account>.azuredatalakestore.net/<cluster_root_path>/<file_path>
    ```

* **Przy użyciu skróconego formatu ścieżki** . To podejście zastępuje ścieżkę do katalogu głównego klastra przy użyciu:

    ```
    adl:///<file path>
    ```

* **Przy użyciu ścieżki względnej** . W przypadku tej metody należy podać tylko względną ścieżkę do pliku, do którego chcesz uzyskać dostęp.

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>Przykłady dostępu do danych

Przykłady są oparte na [połączeniu SSH](./hdinsight-hadoop-linux-use-ssh-unix.md) z węzłem głównym klastra. W przykładach użyto wszystkich trzech schematów URI. Zamień `DATALAKEACCOUNT` i `CLUSTERNAME` z odpowiednimi wartościami.

#### <a name="a-few-hdfs-commands"></a>Kilka poleceń systemu plików HDFS

1. Utwórz plik w magazynie lokalnym.

    ```bash
    touch testFile.txt
    ```

1. Utwórz katalogi w magazynie klastra.

    ```bash
    hdfs dfs -mkdir adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/sampledata1/
    hdfs dfs -mkdir adl:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. Skopiuj dane z magazynu lokalnego do magazynu klastra.

    ```bash
    hdfs dfs -copyFromLocal testFile.txt adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt adl:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt /sampledata3/
    ```

1. Wyświetlanie zawartości katalogu w magazynie klastra.

    ```bash
    hdfs dfs -ls adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/sampledata1/
    hdfs dfs -ls adl:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

#### <a name="creating-a-hive-table"></a>Tworzenie tabeli programu Hive

Trzy lokalizacje plików są pokazane w celach ilustracyjnych. W przypadku rzeczywistego wykonywania należy użyć tylko jednego z `LOCATION` wpisów.

```hql
DROP TABLE myTable;
CREATE EXTERNAL TABLE myTable (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE
LOCATION 'adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/example/data/';
LOCATION 'adl:///example/data/';
LOCATION '/example/data/';
```

## <a name="identify-storage-path-from-ambari"></a>Zidentyfikuj ścieżkę magazynu z Ambari

Aby zidentyfikować pełną ścieżkę do skonfigurowanego domyślnego magazynu, przejdź do konfiguracji systemu plików **HDFS**  >  **Configs** i wprowadź `fs.defaultFS` w polu Filtruj dane wejściowe.

## <a name="create-hdinsight-clusters-with-access-to-data-lake-storage-gen1"></a>Tworzenie klastrów usługi HDInsight z dostępem do Data Lake Storage Gen1

Skorzystaj z poniższych linków, aby uzyskać szczegółowe instrukcje dotyczące tworzenia klastrów usługi HDInsight z dostępem do Data Lake Storage Gen1.

* [Korzystanie z portalu](./hdinsight-hadoop-provision-linux-clusters.md)
* [Korzystanie z programu PowerShell (z Data Lake Storage Gen1 jako magazyn domyślny)](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [Korzystanie z programu PowerShell (z Data Lake Storage Gen1 jako dodatkowego magazynu)](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [Korzystanie z szablonów platformy Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

## <a name="refresh-the-hdinsight-certificate-for-data-lake-storage-gen1-access"></a>Odświeżanie certyfikatu usługi HDInsight na potrzeby dostępu usługi Data Lake Storage Gen1

Poniższy przykładowy kod programu PowerShell odczytuje certyfikat z pliku lokalnego lub Azure Key Vault i aktualizuje klaster usługi HDInsight przy użyciu nowego certyfikatu w celu uzyskania dostępu do Azure Data Lake Storage Gen1. Podaj własną nazwę klastra usługi HDInsight, nazwę grupy zasobów, Identyfikator subskrypcji, `app ID` ścieżkę lokalną do certyfikatu. Wpisz hasło po wyświetleniu monitu.

```powershell-interactive
$clusterName = '<clustername>'
$resourceGroupName = '<resourcegroupname>'
$subscriptionId = '01234567-8a6c-43bc-83d3-6b318c6c7305'
$appId = '01234567-e100-4118-8ba6-c25834f4e938'
$addNewCertKeyCredential = $true
$certFilePath = 'C:\localfolder\adls.pfx'
$KeyVaultName = "my-key-vault-name"
$KeyVaultSecretName = "my-key-vault-secret-name"
$certPassword = Read-Host "Enter Certificate Password"
# certSource
# 0 - create self signed cert
# 1 - read cert from file path
# 2 - read cert from key vault
$certSource = 0

Login-AzAccount
Select-AzSubscription -SubscriptionId $subscriptionId

if($certSource -eq 0)
{
    Write-Host "Generating new SelfSigned certificate"

    $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=hdinsightAdlsCert" -KeySpec KeyExchange
    $certBytes = $cert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword);
    $certString = [System.Convert]::ToBase64String($certBytes)
}
elseif($certSource -eq 1)
{

    Write-Host "Reading the cert file from path $certFilePath"

    $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($certFilePath, $certPassword)
    $certString = [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes($certFilePath))
}
elseif($certSource -eq 2)
{

    Write-Host "Reading the cert file from Azure Key Vault $KeyVaultName"

    $cert = (Get-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName)
    $certValue = [System.Convert]::FromBase64String($cert.SecretValueText)
    $certObject = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList $certValue, $null,"Exportable, PersistKeySet"

    $certBytes = $certObject.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword.SecretValueText);

    $certString =[System.Convert]::ToBase64String($certBytes)
}

if($addNewCertKeyCredential)
{
    Write-Host "Creating new KeyCredential for the app"
    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    New-AzADAppCredential -ApplicationId $appId -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
    Write-Host "Waiting for 7 minutes for the permissions to get propagated"
    Start-Sleep -s 420 #7 minutes
}

Write-Host "Updating the certificate on HDInsight cluster..."

Invoke-AzResourceAction `
    -ResourceGroupName $resourceGroupName `
    -ResourceType 'Microsoft.HDInsight/clusters' `
    -ResourceName $clusterName `
    -ApiVersion '2015-03-01-preview' `
    -Action 'updateclusteridentitycertificate' `
    -Parameters @{ ApplicationId = $appId; Certificate = $certString; CertificatePassword = $certPassword.ToString() } `
    -Force
```

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób używania Azure Data Lake Storage Gen1 zgodnych z systemem plików HDFS z usługą HDInsight. Ten magazyn umożliwia tworzenie dostosowywalnych, długoterminowych rozwiązań do pozyskiwania danych. I Użyj usługi HDInsight do odblokowania informacji w przechowywanych danych ze strukturą i bez struktury.

Aby uzyskać więcej informacji, zobacz:

* [Szybki start: konfigurowanie klastrów w usłudze HDInsight](./hdinsight-hadoop-provision-linux-clusters.md)
* [Tworzenie klastra usługi HDInsight do użycia Data Lake Storage Gen1 przy użyciu Azure PowerShell](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [Przekazywanie danych do usługi HDInsight](hdinsight-upload-data.md)
* [Używanie sygnatur dostępu współdzielonego usługi Azure Blob Storage w celu ograniczenia dostępu do danych w usłudze HDInsight](hdinsight-storage-sharedaccesssignature-permissions.md)