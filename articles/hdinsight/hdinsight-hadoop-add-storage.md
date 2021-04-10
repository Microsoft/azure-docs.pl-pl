---
title: Dodawanie dodatkowych kont magazynu platformy Azure do usługi HDInsight
description: Dowiedz się, jak dodać dodatkowe konta magazynu platformy Azure do istniejącego klastra usługi HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/27/2020
ms.openlocfilehash: 32757fef131c5e443350f032c0ac987d7f491396
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104864316"
---
# <a name="add-additional-storage-accounts-to-hdinsight"></a>Dodawanie dodatkowych kont magazynu do usługi HDInsight

Dowiedz się, jak za pomocą akcji skryptu dodać dodatkowe *konta* magazynu platformy Azure do usługi HDInsight. Kroki opisane w tym dokumencie umożliwiają dodanie *konta* magazynu do istniejącego klastra usługi HDInsight. Ten artykuł dotyczy *kont* magazynu (nie domyślnego konta magazynu klastra), a nie dodatkowego magazynu, takiego jak [`Azure Data Lake Storage Gen1`](hdinsight-hadoop-use-data-lake-storage-gen1.md) i [`Azure Data Lake Storage Gen2`](hdinsight-hadoop-use-data-lake-storage-gen2.md) .

> [!IMPORTANT]  
> Informacje przedstawione w tym dokumencie dotyczą dodawania dodatkowych kont magazynu do klastra po jego utworzeniu. Aby uzyskać informacje na temat dodawania kont magazynu podczas tworzenia klastra, zobacz [Konfigurowanie klastrów w usłudze HDInsight przy użyciu Apache Hadoop, Apache Spark, Apache Kafka i innych](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster usługi Hadoop w usłudze HDInsight. Zobacz Rozpoczynanie [pracy z usługą HDInsight w systemie Linux](./hadoop/apache-hadoop-linux-tutorial-get-started.md).
* Nazwa i klucz konta magazynu. Zobacz [Zarządzanie kluczami dostępu do konta magazynu](../storage/common/storage-account-keys-manage.md).
* W przypadku korzystania z programu PowerShell należy użyć polecenia AZ module.  Zobacz [omówienie Azure PowerShell](/powershell/azure/).

## <a name="how-it-works"></a>Jak to działa

Podczas przetwarzania skrypt wykonuje następujące czynności:

* Jeśli konto magazynu istnieje już w konfiguracji core-site.xml klastra, skrypt zostanie zakończony i nie zostaną wykonane żadne dalsze akcje.

* Sprawdza, czy konto magazynu istnieje i czy można uzyskać do niego dostęp przy użyciu klucza.

* Szyfruje klucz przy użyciu poświadczeń klastra.

* Dodaje konto magazynu do pliku core-site.xml.

* Powoduje zatrzymanie i ponowne uruchomienie usługi Apache Oozie, Apache Hadoop PRZĘDZy, Apache Hadoop MapReduce2 i Apache Hadoop HDFS. Zatrzymywanie i uruchamianie tych usług pozwala im na korzystanie z nowego konta magazynu.

> [!WARNING]  
> Użycie konta magazynu w innej lokalizacji niż klaster usługi HDInsight nie jest obsługiwane.

## <a name="add-storage-account"></a>Dodaj konto magazynu

Użyj [akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster) , aby zastosować zmiany z uwzględnieniem następujących zagadnień:

|Właściwość | Wartość |
|---|---|
|Identyfikator URI skryptu bash|`https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh`|
|Typy węzłów|Head|
|Parametry|`ACCOUNTNAME``ACCOUNTKEY` `-p` (opcjonalnie)|

* `ACCOUNTNAME` jest nazwą konta magazynu, które ma zostać dodane do klastra usługi HDInsight.
* `ACCOUNTKEY` jest kluczem dostępu dla `ACCOUNTNAME` .
* Parametr `-p` jest opcjonalny. Jeśli ta wartość jest określona, klucz nie jest szyfrowany i jest przechowywany w pliku core-site.xml jako zwykły tekst.

## <a name="verification"></a>Weryfikacja

Podczas wyświetlania klastra usługi HDInsight w Azure Portal wybranie pozycji __konta magazynu__ w obszarze __Właściwości__ nie powoduje wyświetlenia kont magazynu dodanych za pomocą tej akcji skryptu. W przypadku Azure PowerShell i interfejsu wiersza polecenia platformy Azure nie jest wyświetlane dodatkowe konto magazynu. Informacje o magazynie nie są wyświetlane, ponieważ skrypt modyfikuje `core-site.xml` konfigurację klastra. Te informacje nie są używane podczas pobierania informacji o klastrze przy użyciu interfejsów API zarządzania platformy Azure.

Aby sprawdzić, czy magazyn dodatkowy wykorzystuje jedną z metod przedstawionych poniżej:

### <a name="powershell"></a>PowerShell

Skrypt zwróci nazwy kont magazynu skojarzone z danym klastrem. Zamień na `CLUSTERNAME` rzeczywistą nazwę klastra, a następnie uruchom skrypt.

```powershell
# Update values
$clusterName = "CLUSTERNAME"

$creds = Get-Credential -UserName "admin" -Message "Enter the cluster login credentials"

$clusterName = $clusterName.ToLower();

# getting service_config_version
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_service_config_versions/HDFS" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content

$configVersion=$respObj.Clusters.desired_service_config_versions.HDFS.service_config_version

$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=$configVersion" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content

# extract account names
$value = ($respObj.items.configurations | Where type -EQ "core-site").properties | Get-Member -membertype properties | Where Name -Like "fs.azure.account.key.*"
foreach ($name in $value ) { $name.Name.Split(".")[4]}
```

### <a name="apache-ambari"></a>Apache Ambari

1. W przeglądarce sieci Web przejdź do `https://CLUSTERNAME.azurehdinsight.net` lokalizacji, gdzie `CLUSTERNAME` jest nazwą klastra.

1. Przejdź do   >  **konfiguracji** systemu plików HDFS  >    >  **— Zaawansowane niestandardowe podstawowe Lokacje**.

1. Obserwuj klucze, które zaczynają się od `fs.azure.account.key` . Nazwa konta będzie częścią klucza, jak pokazano w tym przykładowym obrazie:

   :::image type="content" source="./media/hdinsight-hadoop-add-storage/apache-ambari-verification.png" alt-text="Weryfikacja za poorednictwem oprogramowania Apache Ambari":::

## <a name="remove-storage-account"></a>Usuń konto magazynu

1. W przeglądarce sieci Web przejdź do `https://CLUSTERNAME.azurehdinsight.net` lokalizacji, gdzie `CLUSTERNAME` jest nazwą klastra.

1. Przejdź do   >  **konfiguracji** systemu plików HDFS  >    >  **— Zaawansowane niestandardowe podstawowe Lokacje**.

1. Usuń następujące klucze:
    * `fs.azure.account.key.<STORAGE_ACCOUNT_NAME>.blob.core.windows.net`
    * `fs.azure.account.keyprovider.<STORAGE_ACCOUNT_NAME>.blob.core.windows.net`

Po usunięciu tych kluczy i zapisaniu konfiguracji należy ponownie uruchomić Oozie, przędzę, MapReduce2, HDFS i Hive jedną.

## <a name="known-issues"></a>Znane problemy

### <a name="storage-firewall"></a>Zapora magazynu

Jeśli wybierzesz opcję zabezpieczenia konta magazynu za pomocą ograniczeń **zapory i sieci wirtualnych** w **wybranych sieciach**, należy włączyć wyjątek **Zezwalaj na zaufane usługi firmy Microsoft...** , aby Usługa HDInsight mogła uzyskać dostęp do konta magazynu`.`

### <a name="unable-to-access-storage-after-changing-key"></a>Nie można uzyskać dostępu do magazynu po zmianie klucza

Jeśli zmienisz klucz dla konta magazynu, Usługa HDInsight nie będzie już uzyskiwać dostępu do konta magazynu. Usługa HDInsight używa zapisanej w pamięci podręcznej kopii klucza w core-site.xml klastra. Tę kopię w pamięci podręcznej należy zaktualizować, aby odpowiadała nowemu kluczowi.

Ponowne uruchomienie akcji skryptu **nie powoduje** aktualizacji klucza, ponieważ skrypt sprawdza, czy wpis dla konta magazynu już istnieje. Jeśli wpis już istnieje, nie wprowadza żadnych zmian.

Aby obejść ten problem:  
1. Usuń konto magazynu.
1. Dodaj konto magazynu.

> [!IMPORTANT]  
> Obracanie klucza magazynu dla konta magazynu podstawowego dołączonego do klastra nie jest obsługiwane.

## <a name="next-steps"></a>Następne kroki

Dowiesz się, jak dodać dodatkowe konta magazynu do istniejącego klastra usługi HDInsight. Aby uzyskać więcej informacji na temat akcji skryptu, zobacz [Dostosowywanie klastrów usługi HDInsight opartych na systemie Linux za pomocą akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md)