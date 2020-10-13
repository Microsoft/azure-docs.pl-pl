---
title: PowerShell — klaster HDInsight z Data Lake Storage Gen1 — Azure
description: Użyj Azure PowerShell, aby tworzyć klastry usługi Azure HDInsight i korzystać z nich przy użyciu Azure Data Lake Storage Gen1.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: db29b6743458a4a3ec87dfec9e367bef0c946a15
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91857014"
---
# <a name="create-hdinsight-clusters-with-azure-data-lake-storage-gen1-as-default-storage-by-using-powershell"></a>Tworzenie klastrów usługi HDInsight z Azure Data Lake Storage Gen1 jako magazyn domyślny przy użyciu programu PowerShell

> [!div class="op_single_selector"]
> * [Korzystanie z witryny Azure Portal](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Korzystanie z programu PowerShell (dla magazynu domyślnego)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Korzystanie z programu PowerShell (do dodatkowego magazynu)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Użyj Menedżer zasobów](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

Dowiedz się, jak używać Azure PowerShell do konfigurowania klastrów usługi Azure HDInsight przy użyciu Azure Data Lake Storage Gen1, jako magazynu domyślnego. Aby uzyskać instrukcje dotyczące tworzenia klastra usługi HDInsight z Data Lake Storage Gen1 jako dodatkowy magazyn, zobacz [Tworzenie klastra usługi HDInsight z Data Lake Storage Gen1 jako dodatkowego magazynu](data-lake-store-hdinsight-hadoop-use-powershell.md).

Poniżej przedstawiono kilka istotnych kwestii dotyczących używania usługi HDInsight z usługą Data Lake Storage Gen1:

* Opcja tworzenia klastrów usługi HDInsight z dostępem do Data Lake Storage Gen1 jako magazyn domyślny jest dostępna dla usługi HDInsight w wersji 3,5 i 3,6.

* Opcja tworzenia klastrów usługi HDInsight z dostępem do Data Lake Storage Gen1 jako magazyn domyślny nie jest *dostępna* dla klastrów HDInsight Premium.

Aby skonfigurować usługi HDInsight do pracy z Data Lake Storage Gen1 przy użyciu programu PowerShell, postępuj zgodnie z instrukcjami w kolejnych pięciu sekcjach.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Przed rozpoczęciem pracy z tym samouczkiem upewnij się, że spełniasz następujące wymagania:

* **Subskrypcja platformy Azure**: Przejdź do [bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Azure PowerShell 1,0 lub więcej**: zobacz [jak zainstalować i skonfigurować program PowerShell](/powershell/azure/).
* **Windows Software Development Kit (SDK)**: aby zainstalować Windows SDK, przejdź do [sekcji pliki do pobrania i narzędzia dla systemu Windows 10](https://dev.windows.com/downloads). Zestaw SDK służy do tworzenia certyfikatu zabezpieczeń.
* **Azure Active Directory Nazwa główna usługi**: w tym samouczku opisano sposób tworzenia nazwy głównej usługi w usłudze Azure Active Directory (Azure AD). Jednak aby utworzyć nazwę główną usługi, musisz być administratorem usługi Azure AD. Jeśli jesteś administratorem, możesz pominąć to wymaganie wstępne i przejść do samouczka.

    >[!NOTE]
    >Nazwę główną usługi można utworzyć tylko wtedy, gdy jesteś administratorem usługi Azure AD. Aby można było utworzyć klaster usługi HDInsight z Data Lake Storage Gen1, administrator usługi Azure AD musi utworzyć nazwę główną. Nazwa główna usługi musi być utworzona przy użyciu certyfikatu, zgodnie z opisem w temacie [Tworzenie jednostki usługi z certyfikatem](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-certificate-from-certificate-authority).
    >

## <a name="create-an-azure-data-lake-storage-gen1-account"></a>Utwórz konto Azure Data Lake Storage Gen1

Aby utworzyć konto Data Lake Storage Gen1, wykonaj następujące czynności:

1. Na pulpicie Otwórz okno programu PowerShell, a następnie wprowadź Poniższe fragmenty kodu. Gdy zostanie wyświetlony monit o zalogowanie się, zaloguj się jako jeden z administratorów subskrypcji lub właścicieli. 

    ```azurepowershell
    # Sign in to your Azure account
    Connect-AzAccount

    # List all the subscriptions associated to your account
    Get-AzSubscription

    # Select a subscription
    Set-AzContext -SubscriptionId <subscription ID>

    # Register for Data Lake Storage Gen1
    Register-AzResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"
    ```

    > [!NOTE]
    > Jeśli zarejestruje dostawcę zasobów Data Lake Storage Gen1 i otrzymasz błąd podobny do `Register-AzResourceProvider : InvalidResourceNamespace: The resource namespace 'Microsoft.DataLakeStore' is invalid` , Twoja subskrypcja może nie zostać zatwierdzona do Data Lake Storage Gen1. Aby włączyć subskrypcję platformy Azure dla Data Lake Storage Gen1, postępuj zgodnie z instrukcjami w temacie [wprowadzenie do Azure Data Lake Storage Gen1 przy użyciu Azure Portal](data-lake-store-get-started-portal.md).
    >

2. Konto Data Lake Storage Gen1 jest skojarzone z grupą zasobów platformy Azure. Zacznij od utworzenia grupy zasobów.

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

3. Utwórz konto Data Lake Storage Gen1. Określona nazwa konta może zawierać tylko małe litery i cyfry.

    ```azurepowershell
    $dataLakeStorageGen1Name = "<your new Data Lake Storage Gen1 name>"
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

4. Używanie Data Lake Storage Gen1 jako magazynu domyślnego wymaga określenia ścieżki głównej, do której pliki specyficzne dla klastra są kopiowane podczas tworzenia klastra. Aby utworzyć ścieżkę katalogu głównego, która jest **/Clusters/hdiadlcluster** w tym fragmencie kodu, użyj następujących poleceń cmdlet:

    ```azurepowershell
    $myrootdir = "/"
    New-AzDataLakeStoreItem -Folder -AccountName $dataLakeStorageGen1Name -Path $myrootdir/clusters/hdiadlcluster
    ````

## <a name="set-up-authentication-for-role-based-access-to-data-lake-storage-gen1"></a>Konfigurowanie uwierzytelniania na potrzeby dostępu opartego na rolach do Data Lake Storage Gen1
Każda subskrypcja platformy Azure jest skojarzona z jednostką usługi Azure AD. Użytkownicy i usługi, które uzyskują dostęp do zasobów subskrypcji przy użyciu Azure Portal lub interfejsu API Azure Resource Manager, muszą najpierw uwierzytelniać się w usłudze Azure AD. Dostęp do subskrypcji i usług platformy Azure można uzyskać, przypisując im odpowiednią rolę w zasobie platformy Azure. W przypadku usług jednostka usługi identyfikuje usługę w usłudze Azure AD.

W tej sekcji przedstawiono sposób udzielania usługi aplikacji, takiej jak HDInsight, dostępu do zasobu platformy Azure (utworzonego wcześniej konta Data Lake Storage Gen1). Można to zrobić, tworząc nazwę główną usługi dla aplikacji i przypisując do niej role za pośrednictwem programu PowerShell.

Aby skonfigurować Active Directory uwierzytelnianie dla Data Lake Storage Gen1, wykonaj zadania w poniższych dwóch sekcjach.

### <a name="create-a-self-signed-certificate"></a>Tworzenie certyfikatu z podpisem własnym
Przed wykonaniem kroków opisanych w tej sekcji upewnij się, że zainstalowano [Windows SDK](https://dev.windows.com/en-us/downloads) . Należy również utworzyć katalog, taki jak *C:\mycertdir*, w którym tworzony jest certyfikat.

1. W oknie programu PowerShell przejdź do lokalizacji, w której zainstalowano Windows SDK (zazwyczaj *C:\Program Files (x86) \Windows Kits\10\bin\x86*), a następnie użyj narzędzia [MakeCert][makecert] , aby utworzyć certyfikat z podpisem własnym i klucz prywatny. Użyj następujących poleceń:

    ```azurepowershell
    $certificateFileDir = "<my certificate directory>"
    cd $certificateFileDir

    makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -r -len 2048
    ```

    Zostanie wyświetlony monit o wprowadzenie hasła klucza prywatnego. Po pomyślnym wykonaniu polecenia zobaczysz **CERTFILE. cer** i **klucze. PVK** w określonym katalogu certyfikatów.
2. Użyj narzędzia [Pvk2pfx][pvk2pfx] , aby przekonwertować pliki. PVK i. cer, które MakeCert utworzone do pliku PFX. Uruchom następujące polecenie:

    ```azurepowershell
    pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po <password>
    ```

    Po wyświetleniu monitu wprowadź określone wcześniej hasło klucza prywatnego. Wartość określona dla parametru **-po** jest hasłem skojarzonym z plikiem pfx. Po pomyślnym zakończeniu polecenia powinien być widoczny plik **CERTFILE. pfx** w katalogu certyfikatu, który został określony.

### <a name="create-an-azure-ad-and-a-service-principal"></a>Tworzenie usługi Azure AD i nazwy głównej usługi
W tej sekcji utworzysz jednostkę usługi dla aplikacji usługi Azure AD, przypiszesz rolę do jednostki usługi i uwierzytelniasz ją jako nazwę główną usługi przez dostarczenie certyfikatu. Aby utworzyć aplikację w usłudze Azure AD, uruchom następujące polecenia:

1. Wklej poniższe polecenia cmdlet w oknie konsoli programu PowerShell. Upewnij się, że wartość określona dla właściwości **-DisplayName** jest unikatowa. Wartości parametrów **-Strona główna** i **-IdentiferUris** są wartościami zastępczymi i nie są weryfikowane.

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

3. Przyznaj jednostce usługi dostęp do katalogu głównego Data Lake Storage Gen1 i wszystkich folderów w ścieżce katalogu głównego określonej wcześniej. Użyj następujących poleceń cmdlet:

    ```azurepowershell
    Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path / -AceType User -Id $objectId -Permissions All
    Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path /clusters -AceType User -Id $objectId -Permissions All
    Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path /clusters/hdiadlcluster -AceType User -Id $objectId -Permissions All
    ```

## <a name="create-an-hdinsight-linux-cluster-with-data-lake-storage-gen1-as-the-default-storage"></a>Tworzenie klastra usługi HDInsight w systemie Linux z Data Lake Storage Gen1 jako magazyn domyślny

W tej sekcji utworzysz klaster usługi HDInsight Hadoop Linux z Data Lake Storage Gen1 jako magazyn domyślny. W tej wersji klaster usługi HDInsight i Data Lake Storage Gen1 muszą znajdować się w tej samej lokalizacji.

1. Pobierz identyfikator dzierżawy subskrypcji i Zapisz go do późniejszego użycia.

    ```azurepowershell
    $tenantID = (Get-AzContext).Tenant.TenantId
    ```

2. Utwórz klaster usługi HDInsight przy użyciu następujących poleceń cmdlet:

    ```azurepowershell
    # Set these variables

    $location = "East US 2"
    $storageAccountName = $dataLakeStorageGen1Name                         # Data Lake Storage Gen1 account name
        $storageRootPath = "<Storage root path you specified earlier>" # E.g. /clusters/hdiadlcluster
        $clusterName = "<unique cluster name>"
    $clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster
    $httpCredentials = Get-Credential
    $sshCredentials = Get-Credential

    New-AzHDInsightCluster `
           -ClusterType Hadoop `
           -OSType Linux `
           -ClusterSizeInNodes $clusterNodes `
           -ResourceGroupName $resourceGroupName `
           -ClusterName $clusterName `
           -HttpCredential $httpCredentials `
           -Location $location `
           -DefaultStorageAccountType AzureDataLakeStore `
           -DefaultStorageAccountName "$storageAccountName.azuredatalakestore.net" `
           -DefaultStorageRootPath $storageRootPath `
           -Version "3.6" `
           -SshCredential $sshCredentials `
           -AadTenantId $tenantId `
           -ObjectId $objectId `
           -CertificateFilePath $certificateFilePath `
           -CertificatePassword $password
    ```

    Po pomyślnym ukończeniu tego polecenia cmdlet powinny zostać wyświetlone dane wyjściowe zawierające szczegółowe informacje o klastrze.

## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-data-lake-storage-gen1"></a>Uruchom zadania testowe w klastrze usługi HDInsight, aby używać Data Lake Storage Gen1
Po skonfigurowaniu klastra usługi HDInsight można uruchomić na nim zadania testowe, aby upewnić się, że może on uzyskać dostęp do Data Lake Storage Gen1. Aby to zrobić, uruchom przykładowe zadanie Hive w celu utworzenia tabeli korzystającej z przykładowych danych, które są już dostępne w Data Lake Storage Gen1 w * \<cluster root> /example/Data/Sample.log*.

W tej sekcji utworzysz połączenie Secure Shell (SSH) z utworzonym klastrem usługi HDInsight Linux, a następnie uruchamiasz przykładowe zapytanie Hive.

* Jeśli używasz klienta systemu Windows do nawiązywania połączenia SSH z klastrem, zobacz [Używanie protokołu SSH z opartą na systemie Linux usługą Hadoop w usłudze HDInsight z systemu Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Jeśli używasz klienta systemu Linux do nawiązywania połączenia SSH z klastrem, zobacz [Używanie protokołu SSH z opartą na systemie Linux usługą Hadoop w usłudze HDInsight z poziomu systemu Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

1. Po nawiązaniu połączenia Uruchom interfejs wiersza polecenia Hive (CLI) za pomocą następującego polecenia:

    ```powershell
    hive
    ```

2. Użyj interfejsu wiersza polecenia, aby wprowadzić następujące instrukcje w celu utworzenia nowej tabeli o nazwie **pojazdy** przy użyciu przykładowych danych w Data Lake Storage Gen1:

    ```azurepowershell
    DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'adl:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
    ```

Powinno zostać wyświetlone dane wyjściowe zapytania w konsoli SSH.

>[!NOTE]
>Ścieżka do przykładowych danych w poprzednim poleceniu CREATE TABLE to `adl:///example/data/` , gdzie `adl:///` jest katalogiem głównym klastra. Zgodnie z przykładem katalogu głównego klastra określonego w tym samouczku polecenie to `adl://hdiadlstore.azuredatalakestore.net/clusters/hdiadlcluster` . Możesz użyć krótszej alternatywy lub podać pełną ścieżkę do katalogu głównego klastra.
>

## <a name="access-data-lake-storage-gen1-by-using-hdfs-commands"></a>Dostęp do Data Lake Storage Gen1 przy użyciu systemu plików HDFS
Po skonfigurowaniu klastra usługi HDInsight pod kątem używania Data Lake Storage Gen1 można uzyskać dostęp do magazynu za pomocą poleceń powłoki rozproszony system plików usługi Hadoop (HDFS).

W tej sekcji nawiążesz połączenie SSH z utworzonym klastrem usługi HDInsight Linux, a następnie uruchamiasz polecenia systemu plików HDFS.

* Jeśli używasz klienta systemu Windows do nawiązywania połączenia SSH z klastrem, zobacz [Używanie protokołu SSH z opartą na systemie Linux usługą Hadoop w usłudze HDInsight z systemu Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Jeśli używasz klienta systemu Linux do nawiązywania połączenia SSH z klastrem, zobacz [Używanie protokołu SSH z opartą na systemie Linux usługą Hadoop w usłudze HDInsight z poziomu systemu Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

Po nawiązaniu połączenia należy wyświetlić listę plików w Data Lake Storage Gen1 przy użyciu następującego polecenia systemu plików HDFS.

```azurepowershell
hdfs dfs -ls adl:///
```

Możesz również użyć `hdfs dfs -put` polecenia, aby przekazać pliki do Data Lake Storage Gen1, a następnie użyć `hdfs dfs -ls` do sprawdzenia, czy pliki zostały pomyślnie przekazane.

## <a name="see-also"></a>Zobacz też
* [Używanie Data Lake Storage Gen1 z klastrami usługi Azure HDInsight](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen1.md)
* [Azure Portal: Tworzenie klastra usługi HDInsight do użycia Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx