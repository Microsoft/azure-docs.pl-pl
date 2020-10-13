---
title: Szablon — klaster usługi HDInsight z Data Lake Storage Gen1
description: Użyj szablonów Azure Resource Manager, aby tworzyć klastry usługi Azure HDInsight i korzystać z nich przy użyciu Azure Data Lake Storage Gen1.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 2a0471055e4648944aa07d10fef67f5e7235a76b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91856928"
---
# <a name="create-an-hdinsight-cluster-with-azure-data-lake-storage-gen1-using-azure-resource-manager-template"></a>Tworzenie klastra usługi HDInsight z Azure Data Lake Storage Gen1 przy użyciu szablonu Azure Resource Manager
> [!div class="op_single_selector"]
> * [Korzystanie z portalu](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Korzystanie z programu PowerShell (dla magazynu domyślnego)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Korzystanie z programu PowerShell (dla dodatkowego magazynu)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Używanie Menedżer zasobów](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Dowiedz się, jak skonfigurować klaster usługi HDInsight Azure Data Lake Storage Gen1 przy użyciu usługi Azure PowerShell **jako dodatkowego magazynu**.

W przypadku obsługiwanych typów klastrów Data Lake Storage Gen1 może być używany jako magazyn domyślny lub jako dodatkowe konto magazynu. Gdy Data Lake Storage Gen1 jest używany jako dodatkowy magazyn, domyślne konto magazynu dla klastrów nadal będzie magazynem Azure Blob Storage (WASB) i plikami związanymi z klastrem (takimi jak dzienniki itp.) są nadal zapisywane w magazynie domyślnym, podczas gdy dane, które mają być przetwarzane, mogą być przechowywane w Data Lake Storage Gen1 koncie. Używanie Data Lake Storage Gen1 jako dodatkowego konta magazynu nie ma wpływu na wydajność ani na odczyt/zapis w magazynie z klastra.

## <a name="using-data-lake-storage-gen1-for-hdinsight-cluster-storage"></a>Używanie Data Lake Storage Gen1 dla magazynu klastra usługi HDInsight

Poniżej przedstawiono kilka istotnych kwestii dotyczących używania usługi HDInsight z usługą Data Lake Storage Gen1:

* Opcja tworzenia klastrów usługi HDInsight z dostępem do Data Lake Storage Gen1 jako magazyn domyślny jest dostępny dla usługi HDInsight w wersji 3,5 i 3,6.

* Opcja tworzenia klastrów usługi HDInsight z dostępem do Data Lake Storage Gen1 jako dodatkowy magazyn jest dostępny dla usługi HDInsight w wersji 3,2, 3,4, 3,5 i 3,6.

W tym artykule opisano obsługę klastra Hadoop z Data Lake Storage Gen1 jako dodatkowy magazyn. Aby uzyskać instrukcje dotyczące sposobu tworzenia klastra Hadoop z Data Lake Storage Gen1 jako magazyn domyślny, zobacz [Tworzenie klastra usługi HDInsight za pomocą Data Lake Storage Gen1 przy użyciu Azure Portal](data-lake-store-hdinsight-hadoop-use-portal.md).

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Przed przystąpieniem do wykonania kroków opisanych w tym samouczku należy dysponować następującymi elementami:

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Program Azure PowerShell 1.0 lub nowszy**. Zobacz artykuł [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/).
* **Azure Active Directory nazwę główną usługi**. Kroki opisane w tym samouczku zawierają instrukcje dotyczące tworzenia nazwy głównej usługi w usłudze Azure AD. Jednak aby utworzyć jednostkę usługi, należy mieć uprawnienia administratora usługi Azure AD. Jeśli jesteś administratorem usługi Azure AD, możesz pominąć to wymaganie wstępne i kontynuować pracę z samouczkiem.

    **Jeśli nie jesteś administratorem usługi Azure AD**, nie będziesz w stanie wykonać kroków wymaganych do utworzenia nazwy głównej usługi. W takim przypadku administrator usługi Azure AD musi najpierw utworzyć jednostkę usługi, aby można było utworzyć klaster HDInsight z Data Lake Storage Gen1. Ponadto należy utworzyć nazwę główną usługi przy użyciu certyfikatu, zgodnie z opisem w temacie [Tworzenie jednostki usługi z certyfikatem](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-certificate-from-certificate-authority).

## <a name="create-an-hdinsight-cluster-with-data-lake-storage-gen1"></a>Tworzenie klastra usługi HDInsight z Data Lake Storage Gen1
Szablon Menedżer zasobów i wymagania wstępne dotyczące korzystania z tego szablonu są dostępne w witrynie GitHub w temacie [wdrażanie klastra usługi HDInsight w systemie Linux przy użyciu nowych Data Lake Storage Gen1](https://github.com/Azure/azure-quickstart-templates/tree/master/201-hdinsight-datalake-store-azure-storage). Postępuj zgodnie z instrukcjami podanymi w tym linku, aby utworzyć klaster usługi HDInsight z Data Lake Storage Gen1 jako dodatkowy magazyn.

Instrukcje podane powyżej linku wymagają programu PowerShell. Przed rozpoczęciem pracy z tymi instrukcjami upewnij się, że logujesz się do konta platformy Azure. Na pulpicie Otwórz nowe okno Azure PowerShell i wprowadź następujące fragmenty kodu. Po wyświetleniu monitu o zalogowanie się upewnij się, że logujesz się jako jeden z administratorów subskrypcji/właściciela:

```
# Log in to your Azure account
Connect-AzAccount

# List all the subscriptions associated to your account
Get-AzSubscription

# Select a subscription
Set-AzContext -SubscriptionId <subscription ID>
```

Szablon wdraża następujące typy zasobów:

* [Microsoft. kontach datalakestore/konta](/azure/templates/microsoft.datalakestore/accounts)
* [Microsoft. Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
* [Microsoft. HDInsight/Klastry](/azure/templates/microsoft.hdinsight/clusters)

## <a name="upload-sample-data-to-data-lake-storage-gen1"></a>Przekaż przykładowe dane do Data Lake Storage Gen1
Szablon Menedżer zasobów tworzy nowe konto magazynu z Data Lake Storage Gen1 i kojarzy je z klastrem usługi HDInsight. Musisz teraz przekazać przykładowe dane do Data Lake Storage Gen1. Te dane będą potrzebne później w samouczku do uruchamiania zadań z klastra usługi HDInsight, który uzyskuje dostęp do danych na koncie magazynu za pomocą Data Lake Storage Gen1. Aby uzyskać instrukcje dotyczące przekazywania danych, zobacz [przekazywanie pliku do Data Lake Storage Gen1](data-lake-store-get-started-portal.md#uploaddata). Jeśli szukasz przykładowych danych do przekazania, możesz pobrać folder **Ambulance Data** z [repozytorium Git usługi Azure Data Lake](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData).

## <a name="set-relevant-acls-on-the-sample-data"></a>Ustaw odpowiednie listy ACL dla przykładowych danych
Aby upewnić się, że przekazywane dane przykładowe są dostępne z klastra usługi HDInsight, należy upewnić się, że aplikacja Azure AD, która jest używana do ustanowienia tożsamości między klastrem HDInsight a Data Lake Storage Gen1, ma dostęp do pliku/folderu, do którego próbujesz uzyskać dostęp. Aby to zrobić, wykonaj następujące czynności.

1. Znajdź nazwę aplikacji usługi Azure AD, która jest skojarzona z klastrem HDInsight i konto magazynu z Data Lake Storage Gen1. Jednym ze sposobów poszukania nazwy jest otwarcie bloku klaster usługi HDInsight utworzonego przy użyciu szablonu Menedżer zasobów, kliknij kartę **tożsamość klastra Azure AD** , a następnie wyszukaj wartość **Nazwa wyświetlana nazwy głównej**.
2. Teraz możesz zapewnić dostęp do tej aplikacji usługi Azure AD w pliku/folderze, do którego chcesz uzyskać dostęp z klastra HDInsight. Aby ustawić odpowiednie listy ACL dla pliku/folderu w Data Lake Storage Gen1, zobacz [Zabezpieczanie danych w Data Lake Storage Gen1](data-lake-store-secure-data.md#filepermissions).

## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-data-lake-storage-gen1"></a>Uruchom zadania testowe w klastrze usługi HDInsight, aby używać Data Lake Storage Gen1
Po skonfigurowaniu klastra usługi HDInsight można uruchomić zadania testowe w klastrze, aby sprawdzić, czy klaster usługi HDInsight ma dostęp do Data Lake Storage Gen1. W tym celu zostanie uruchomione przykładowe zadanie Hive, które tworzy tabelę przy użyciu przykładowych danych, które zostały przekazane wcześniej do konta magazynu za pomocą Data Lake Storage Gen1.

W tej sekcji poznasz protokół SSH w klastrze usługi HDInsight Linux i uruchomiono zapytanie przykładowego programu Hive. W przypadku korzystania z klienta systemu Windows zalecamy korzystanie z polecenia wyszukania, które można pobrać **z programu** [https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) .

Aby uzyskać więcej informacji na temat korzystania z tego polecenia, zobacz [Używanie protokołu SSH z opartą na systemie Linux usługą Hadoop w usłudze HDInsight z systemu Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).

1. Po nawiązaniu połączenia Uruchom interfejs wiersza polecenia Hive przy użyciu następującego polecenia:

   ```
   hive
   ```
2. Korzystając z interfejsu wiersza polecenia, wprowadź następujące instrukcje, aby utworzyć nową tabelę o nazwie **pojazdy** przy użyciu przykładowych danych w Data Lake Storage Gen1:

   ```
   DROP TABLE vehicles;
   CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://<mydatalakestoragegen1>.azuredatalakestore.net:443/';
   SELECT * FROM vehicles LIMIT 10;
   ```

   Wyświetlone dane wyjściowe powinny przypominać następujące dane:

   ```
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

W tej sekcji nawiążesz połączenie SSH z klastrem usługi HDInsight Linux i uruchomisz polecenia HDFS. W przypadku korzystania z klienta systemu Windows zalecamy korzystanie z polecenia wyszukania, które można pobrać **z programu** [https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) .

Aby uzyskać więcej informacji na temat korzystania z tego polecenia, zobacz [Używanie protokołu SSH z opartą na systemie Linux usługą Hadoop w usłudze HDInsight z systemu Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).

Po nawiązaniu połączenia użyj następującego polecenia systemu plików HDFS, aby wyświetlić listę plików na koncie magazynu za pomocą Data Lake Storage Gen1.

```
hdfs dfs -ls adl://<storage account with Data Lake Storage Gen1 name>.azuredatalakestore.net:443/
```

Powinno to spowodować wyświetlenie listy plików przekazanych wcześniej do Data Lake Storage Gen1.

```
15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
Found 1 items
-rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestoragegen1.azuredatalakestore.net:443/mynewfolder
```

Możesz również użyć `hdfs dfs -put` polecenia, aby przekazać pliki do Data Lake Storage Gen1, a następnie użyć `hdfs dfs -ls` do sprawdzenia, czy pliki zostały pomyślnie przekazane.


## <a name="next-steps"></a>Następne kroki
* [Kopiowanie danych z obiektów BLOB usługi Azure Storage do Data Lake Storage Gen1](data-lake-store-copy-data-wasb-distcp.md)
* [Używanie Data Lake Storage Gen1 z klastrami usługi Azure HDInsight](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen1.md)
