---
title: PowerShell-HDInsight z Data Lake Storage Gen1-Add-in Storage — Azure
description: Informacje dotyczące konfigurowania klastra usługi HDInsight za pomocą Azure PowerShell Azure Data Lake Storage Gen1 jako dodatkowego magazynu.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: d11d3edc4eec0a006e71ef1787cbbd62a7f5957e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2020
ms.locfileid: "91856980"
---
# <a name="use-azure-powershell-to-create-an-hdinsight-cluster-with-azure-data-lake-storage-gen1-as-additional-storage"></a>Użyj Azure PowerShell, aby utworzyć klaster usługi HDInsight z Azure Data Lake Storage Gen1 (jako dodatkowy magazyn)

> [!div class="op_single_selector"]
> * [Korzystanie z portalu](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Korzystanie z programu PowerShell (dla magazynu domyślnego)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Korzystanie z programu PowerShell (dla dodatkowego magazynu)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Używanie Menedżer zasobów](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Dowiedz się, jak skonfigurować klaster usługi HDInsight Azure Data Lake Storage Gen1 przy użyciu usługi Azure PowerShell **jako dodatkowego magazynu**. Aby uzyskać instrukcje dotyczące sposobu tworzenia klastra usługi HDInsight z Data Lake Storage Gen1 jako magazyn domyślny, zobacz [Tworzenie klastra usługi HDInsight z Data Lake Storage Gen1 jako magazyn domyślny](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md).

> [!NOTE]
> Jeśli zamierzasz używać Data Lake Storage Gen1 jako dodatkowego magazynu dla klastra usługi HDInsight, zdecydowanie zalecamy wykonanie tej czynności podczas tworzenia klastra zgodnie z opisem w tym artykule. Dodawanie Data Lake Storage Gen1 jako dodatkowego magazynu do istniejącego klastra usługi HDInsight to skomplikowany proces i podatny na błędy.
>

W przypadku obsługiwanych typów klastrów Data Lake Storage Gen1 może być używany jako magazyn domyślny lub dodatkowe konto magazynu. Gdy Data Lake Storage Gen1 jest używany jako dodatkowy magazyn, domyślne konto magazynu dla klastrów nadal będzie magazynem Azure Blob Storage (WASB) i plikami związanymi z klastrem (takimi jak dzienniki itp.) są nadal zapisywane w domyślnym magazynie, podczas gdy dane, które mają być przetwarzane, mogą być przechowywane w Data Lake Storage Gen1. Używanie Data Lake Storage Gen1 jako dodatkowego konta magazynu nie ma wpływu na wydajność ani na odczyt/zapis w magazynie z klastra.

## <a name="using-data-lake-storage-gen1-for-hdinsight-cluster-storage"></a>Używanie Data Lake Storage Gen1 dla magazynu klastra usługi HDInsight

Poniżej przedstawiono kilka istotnych kwestii dotyczących używania usługi HDInsight z usługą Data Lake Storage Gen1:

* Opcja tworzenia klastrów usługi HDInsight z dostępem do Data Lake Storage Gen1 jako dodatkowy magazyn jest dostępny dla usługi HDInsight w wersji 3,2, 3,4, 3,5 i 3,6.

Skonfigurowanie usługi HDInsight do pracy z Data Lake Storage Gen1 przy użyciu programu PowerShell obejmuje następujące kroki:

* Tworzenie konta usługi Data Lake Storage Gen1 
* Konfigurowanie uwierzytelniania na potrzeby dostępu opartego na rolach do Data Lake Storage Gen1
* Utwórz klaster usługi HDInsight z uwierzytelnianiem, aby Data Lake Storage Gen1
* Uruchamianie zadania testowego w klastrze

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Przed przystąpieniem do wykonania kroków opisanych w tym samouczku należy dysponować następującymi elementami:

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Program Azure PowerShell 1.0 lub nowszy**. Zobacz artykuł [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/).
* **Windows SDK**. Możesz je zainstalować, klikając [tutaj](https://dev.windows.com/en-us/downloads). Służy do tworzenia certyfikatu zabezpieczeń.
* **Azure Active Directory nazwę główną usługi**. Kroki opisane w tym samouczku zawierają instrukcje dotyczące tworzenia nazwy głównej usługi w usłudze Azure AD. Jednak aby utworzyć jednostkę usługi, należy mieć uprawnienia administratora usługi Azure AD. Jeśli jesteś administratorem usługi Azure AD, możesz pominąć to wymaganie wstępne i kontynuować pracę z samouczkiem.

    **Jeśli nie jesteś administratorem usługi Azure AD**, nie będziesz w stanie wykonać kroków wymaganych do utworzenia nazwy głównej usługi. W takim przypadku administrator usługi Azure AD musi najpierw utworzyć jednostkę usługi, aby można było utworzyć klaster HDInsight z Data Lake Storage Gen1. Ponadto należy utworzyć nazwę główną usługi przy użyciu certyfikatu, zgodnie z opisem w temacie [Tworzenie jednostki usługi z certyfikatem](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-certificate-from-certificate-authority).

## <a name="create-a-data-lake-storage-gen1-account"></a>Tworzenie konta usługi Data Lake Storage Gen1 
Wykonaj następujące kroki, aby utworzyć konto Data Lake Storage Gen1.

1. Na pulpicie Otwórz nowe okno Azure PowerShell a następnie wprowadź Poniższy fragment kodu. Po wyświetleniu monitu o zalogowanie się upewnij się, że logujesz się jako jeden z administratorów/właścicieli subskrypcji:

    ```azurepowershell
    # Log in to your Azure account
    Connect-AzAccount

    # List all the subscriptions associated to your account
    Get-AzSubscription

    # Select a subscription
    Set-AzContext -SubscriptionId <subscription ID>

    # Register for Data Lake Storage Gen1
    Register-AzResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"
    ```

   > [!NOTE]
   > Jeśli wystąpi błąd podobny do `Register-AzResourceProvider : InvalidResourceNamespace: The resource namespace 'Microsoft.DataLakeStore' is invalid` rejestracji dostawcy zasobów Data Lake Storage Gen1, możliwe jest, że subskrypcja nie jest zatwierdzona do Data Lake Storage Gen1. Upewnij się, że możesz włączyć subskrypcję platformy Azure dla Data Lake Storage Gen1, wykonując te [instrukcje](data-lake-store-get-started-portal.md).
   >
   >
2. Konto magazynu z Data Lake Storage Gen1 jest skojarzone z grupą zasobów platformy Azure. Rozpocznij od utworzenia grupy zasobów platformy Azure.

    ```azurepowershell
    $resourceGroupName = "<your new resource group name>"
    New-AzResourceGroup -Name $resourceGroupName -Location "East US 2"
    ```

    Powinny pojawić się następujące dane wyjściowe:

    ```output
    ResourceGroupName : hdiadlgrp
    Location          : eastus2
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/<subscription-id>/resourceGroups/hdiadlgrp
    ```

3. Utwórz konto magazynu za pomocą Data Lake Storage Gen1. Określona nazwa konta może zawierać tylko małe litery i cyfry.

    ```azurepowershell
    $dataLakeStorageGen1Name = "<your new storage account with Data Lake Storage Gen1 name>"
    New-AzDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStorageGen1Name -Location "East US 2"
    ```

    Powinny pojawić się dane wyjściowe podobne do następujących:

    ```output
    ...
    ProvisioningState           : Succeeded
    State                       : Active
    CreationTime                : 5/5/2017 10:53:56 PM
    EncryptionState             : Enabled
    ...
    LastModifiedTime            : 5/5/2017 10:53:56 PM
    Endpoint                    : hdiadlstore.azuredatalakestore.net
    DefaultGroup                :
    Id                          : /subscriptions/<subscription-id>/resourceGroups/hdiadlgrp/providers/Microsoft.DataLakeStore/accounts/hdiadlstore
    Name                        : hdiadlstore
    Type                        : Microsoft.DataLakeStore/accounts
    Location                    : East US 2
    Tags                        : {}
    ```

5. Przekaż przykładowe dane do Data Lake Storage Gen1. Użyjemy tego w dalszej części tego artykułu, aby sprawdzić, czy dane są dostępne z klastra usługi HDInsight. Jeśli szukasz przykładowych danych do przekazania, możesz pobrać folder **Ambulance Data** z [repozytorium Git usługi Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData).

    ```azurepowershell
    $myrootdir = "/"
    Import-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name -Path "C:\<path to data>\vehicle1_09142014.csv" -Destination $myrootdir\vehicle1_09142014.csv
    ```

## <a name="set-up-authentication-for-role-based-access-to-data-lake-storage-gen1"></a>Konfigurowanie uwierzytelniania na potrzeby dostępu opartego na rolach do Data Lake Storage Gen1

Każda subskrypcja platformy Azure jest skojarzona z Azure Active Directory. Użytkownicy i usługi, które uzyskują dostęp do zasobów subskrypcji przy użyciu interfejsu API Azure Portal lub Azure Resource Manager, muszą najpierw uwierzytelniać się za pomocą tego Azure Active Directory. Dostęp do subskrypcji i usług platformy Azure można uzyskać, przypisując im odpowiednią rolę w zasobie platformy Azure.  W przypadku usług jednostka usługi identyfikuje usługę w Azure Active Directory (Azure AD). W tej sekcji przedstawiono sposób udzielania usługi aplikacji, takiej jak HDInsight, dostępu do zasobu platformy Azure (konta magazynu utworzonego wcześniej Data Lake Storage Gen1) przez utworzenie nazwy głównej usługi dla aplikacji i przypisanie ról do programu za pośrednictwem Azure PowerShell.

Aby skonfigurować Active Directory uwierzytelnianie dla Data Lake Storage Gen1, należy wykonać następujące zadania.

* Tworzenie certyfikatu z podpisem własnym
* Tworzenie aplikacji w Azure Active Directory i nazwy głównej usługi

### <a name="create-a-self-signed-certificate"></a>Tworzenie certyfikatu z podpisem własnym

Przed wykonaniem kroków opisanych w tej sekcji upewnij się, że zainstalowano [Windows SDK](https://dev.windows.com/en-us/downloads) . Należy również utworzyć katalog, taki jak **C:\mycertdir**, gdzie zostanie utworzony certyfikat.

1. W oknie programu PowerShell przejdź do lokalizacji, w której zainstalowano Windows SDK (zazwyczaj `C:\Program Files (x86)\Windows Kits\10\bin\x86` i użyj narzędzia [MakeCert][makecert] , aby utworzyć certyfikat z podpisem własnym i klucz prywatny. Użyj następujących poleceń.

    ```azurepowershell
    $certificateFileDir = "<my certificate directory>"
    cd $certificateFileDir

    makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -r -len 2048
    ```

    Zostanie wyświetlony monit o wprowadzenie hasła klucza prywatnego. Po pomyślnym wykonaniu polecenia powinien zostać wyświetlony element **CERTFILE. cer** i **klucze. PVK** w określonym katalogu certyfikatów.
2. Użyj narzędzia [Pvk2pfx][pvk2pfx] , aby przekonwertować pliki. PVK i. cer, które MakeCert utworzone do pliku PFX. Uruchom następujące polecenie.

    ```azurepowershell
    pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po <password>
    ```

    Po wyświetleniu monitu wprowadź określone wcześniej hasło klucza prywatnego. Wartość określona dla parametru **-po** jest hasłem skojarzonym z plikiem pfx. Po pomyślnym zakończeniu działania polecenia powinien być widoczny plik CertFile. pfx w określonym katalogu certyfikatów.

### <a name="create-an-azure-active-directory-and-a-service-principal"></a>Tworzenie Azure Active Directory i nazwy głównej usługi

W tej sekcji opisano kroki tworzenia jednostki usługi dla aplikacji Azure Active Directory, przypisywania roli do jednostki usługi i uwierzytelniania jako jednostki usługi przez podanie certyfikatu. Uruchom następujące polecenia, aby utworzyć aplikację w Azure Active Directory.

1. Wklej poniższe polecenia cmdlet w oknie konsoli programu PowerShell. Upewnij się, że wartość określona dla właściwości **-DisplayName** jest unikatowa. Ponadto wartości parametrów **-Strona główna** i **-IdentiferUris** są wartościami zastępczymi i nie są weryfikowane.

    ```azurepowershell
    $certificateFilePath = "$certificateFileDir\CertFile.pfx"

    $password = Read-Host -Prompt "Enter the password" # This is the password you specified for the .pfx file

    $certificatePFX = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($certificateFilePath, $password)

    $rawCertificateData = $certificatePFX.GetRawCertData()

    $credential = [System.Convert]::ToBase64String($rawCertificateData)

    $application = New-AzADApplication `
        -DisplayName "HDIADL" `
        -HomePage "https://contoso.com" `
        -IdentifierUris "https://mycontoso.com" `
        -CertValue $credential  `
        -StartDate $certificatePFX.NotBefore  `
        -EndDate $certificatePFX.NotAfter

    $applicationId = $application.ApplicationId
    ```

2. Utwórz nazwę główną usługi przy użyciu identyfikatora aplikacji.

    ```azurepowershell
    $servicePrincipal = New-AzADServicePrincipal -ApplicationId $applicationId

     $objectId = $servicePrincipal.Id
    ```

3. Przyznaj jednostce usługi dostęp do folderu Data Lake Storage Gen1 i pliku, do którego będziesz uzyskiwać dostęp z klastra usługi HDInsight. Poniższy fragment kodu zapewnia dostęp do katalogu głównego konta magazynu za pomocą Data Lake Storage Gen1 (do którego skopiowano przykładowy plik danych) i sam plik.

    ```azurepowershell
    Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path / -AceType User -Id $objectId -Permissions All
    Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path /vehicle1_09142014.csv -AceType User -Id $objectId -Permissions All
    ```

## <a name="create-an-hdinsight-linux-cluster-with-data-lake-storage-gen1-as-additional-storage"></a>Tworzenie klastra usługi HDInsight w systemie Linux z Data Lake Storage Gen1 jako dodatkowy magazyn

W tej sekcji utworzysz klaster usługi HDInsight Hadoop Linux z Data Lake Storage Gen1 jako dodatkowy magazyn. W tej wersji klaster usługi HDInsight i konto magazynu z Data Lake Storage Gen1 muszą znajdować się w tej samej lokalizacji.

1. Rozpocznij od pobrania identyfikatora dzierżawy subskrypcji. Będzie ona potrzebna później.

    ```azurepowershell
    $tenantID = (Get-AzContext).Tenant.TenantId
    ```

2. W tej wersji dla klastra Hadoop Data Lake Storage Gen1 może być używany tylko jako dodatkowy magazyn dla klastra. Domyślny magazyn będzie nadal znajdować się w usłudze Azure Blob Storage (WASB). Należy najpierw utworzyć konto magazynu i kontenery magazynu wymagane przez klaster.

    ```azurepowershell
    # Create an Azure storage account
    $location = "East US 2"
    $storageAccountName = "<StorageAccountName>"   # Provide a Storage account name

    New-AzStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -Location $location -Type Standard_GRS

    # Create an Azure Blob Storage container
    $containerName = "<ContainerName>"              # Provide a container name
    $storageAccountKey = (Get-AzStorageAccountKey -Name $storageAccountName -ResourceGroupName $resourceGroupName)[0].Value
    $destContext = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
    New-AzStorageContainer -Name $containerName -Context $destContext
    ```

3. Utwórz klaster usługi HDInsight. Użyj następujących poleceń cmdlet.

    ```azurepowershell
    # Set these variables
    $clusterName = $containerName                   # As a best practice, have the same name for the cluster and container
    $clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster
    $httpCredentials = Get-Credential
    $sshCredentials = Get-Credential

    New-AzHDInsightCluster -ClusterName $clusterName -ResourceGroupName $resourceGroupName -HttpCredential $httpCredentials -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName  -ClusterSizeInNodes $clusterNodes -ClusterType Hadoop -Version "3.4" -OSType Linux -SshCredential $sshCredentials -ObjectID $objectId -AadTenantId $tenantID -CertificateFilePath $certificateFilePath -CertificatePassword $password
    ```

    Po pomyślnym zakończeniu działania polecenia cmdlet powinny zostać wyświetlone dane wyjściowe z listą klastra.


## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-the-data-lake-storage-gen1"></a>Uruchom zadania testowe w klastrze usługi HDInsight, aby użyć Data Lake Storage Gen1
Po skonfigurowaniu klastra usługi HDInsight można uruchomić zadania testowe w klastrze, aby sprawdzić, czy klaster usługi HDInsight ma dostęp do Data Lake Storage Gen1. W tym celu zostanie uruchomione przykładowe zadanie Hive, które tworzy tabelę przy użyciu przykładowych danych, które zostały przekazane wcześniej do konta magazynu za pomocą Data Lake Storage Gen1.

W tej sekcji przeprowadzisz protokół SSH do utworzonego klastra usługi HDInsight Linux i uruchomisz przykładowe zapytanie Hive.

* Jeśli używasz klienta systemu Windows do używania protokołu SSH z klastrem, zobacz [Używanie protokołu SSH z opartą na systemie Linux usługą Hadoop w usłudze HDInsight z systemu Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Jeśli używasz klienta z systemem Linux do używania protokołu SSH z klastrem, zobacz [Używanie protokołu SSH z opartą na systemie Linux usługą Hadoop w usłudze HDInsight z systemu Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)

1. Po nawiązaniu połączenia Uruchom interfejs wiersza polecenia Hive przy użyciu następującego polecenia:

    ```azurepowershell
    hive
    ```

2. Korzystając z interfejsu wiersza polecenia, wprowadź następujące instrukcje, aby utworzyć nową tabelę o nazwie **pojazdy** przy użyciu przykładowych danych w Data Lake Storage Gen1:

    ```azurepowershell
    DROP TABLE vehicles;
    CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://<mydatalakestoragegen1>.azuredatalakestore.net:443/';
    SELECT * FROM vehicles LIMIT 10;
    ```

    Powinny pojawić się dane wyjściowe podobne do następujących:

    ```output
    1,1,2014-09-14 00:00:03,46.81006,-92.08174,51,S,1
    1,2,2014-09-14 00:00:06,46.81006,-92.08174,13,NE,1
    1,3,2014-09-14 00:00:09,46.81006,-92.08174,48,NE,1
    1,4,2014-09-14 00:00:12,46.81006,-92.08174,30,W,1
    1,5,2014-09-14 00:00:15,46.81006,-92.08174,47,S,1
    1,6,2014-09-14 00:00:18,46.81006,-92.08174,9,S,1
    1,7,2014-09-14 00:00:21,46.81006,-92.08174,53,N,1
    1,8,2014-09-14 00:00:24,46.81006,-92.08174,63,SW,1
    1,9,2014-09-14 00:00:27,46.81006,-92.08174,4,NE,1
    1,10,2014-09-14 00:00:30,46.81006,-92.08174,31,N,1
    ```

## <a name="access-data-lake-storage-gen1-using-hdfs-commands"></a>Data Lake Storage Gen1 dostępu przy użyciu systemu plików HDFS
Po skonfigurowaniu klastra usługi HDInsight do używania Data Lake Storage Gen1 można uzyskać dostęp do magazynu za pomocą poleceń powłoki systemu plików HDFS.

W tej sekcji przeprowadzisz protokół SSH do utworzonego klastra usługi HDInsight w systemie Linux i uruchomisz polecenia systemu plików HDFS.

* Jeśli używasz klienta systemu Windows do używania protokołu SSH z klastrem, zobacz [Używanie protokołu SSH z opartą na systemie Linux usługą Hadoop w usłudze HDInsight z systemu Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Jeśli używasz klienta z systemem Linux do używania protokołu SSH z klastrem, zobacz [Używanie protokołu SSH z opartą na systemie Linux usługą Hadoop w usłudze HDInsight z systemu Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)

Po nawiązaniu połączenia użyj następującego polecenia systemu plików HDFS, aby wyświetlić listę plików na koncie magazynu za pomocą Data Lake Storage Gen1.

```azurepowershell
hdfs dfs -ls adl://<storage account with Data Lake Storage Gen1 name>.azuredatalakestore.net:443/
```

Powinno to spowodować wyświetlenie listy plików przekazanych wcześniej do Data Lake Storage Gen1.

```output
15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
Found 1 items
-rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestoragegen1.azuredatalakestore.net:443/mynewfolder
```

Możesz również użyć `hdfs dfs -put` polecenia, aby przekazać pliki do Data Lake Storage Gen1, a następnie użyć `hdfs dfs -ls` do sprawdzenia, czy pliki zostały pomyślnie przekazane.

## <a name="see-also"></a>Zobacz też
* [Używanie Data Lake Storage Gen1 z klastrami usługi Azure HDInsight](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen1.md)
* [Portal: Tworzenie klastra usługi HDInsight do użycia Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx
