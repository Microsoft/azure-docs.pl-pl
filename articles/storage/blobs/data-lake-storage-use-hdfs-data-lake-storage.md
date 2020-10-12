---
title: Korzystanie z interfejsu wiersza polecenia systemu plików HDFS z Azure Data Lake Storage Gen2
description: Użyj interfejsu wiersza polecenia rozproszony system plików Hadoop (HDFS) dla Azure Data Lake Storage Gen2. Utwórz kontener, uzyskaj listę plików lub katalogów i nie tylko.
services: storage
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 12/06/2018
ms.author: normesta
ms.subservice: data-lake-storage-gen2
ms.reviewer: artek
ms.openlocfilehash: 1d720aed44358dd314bc4226adb9ad517139cd18
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87836313"
---
# <a name="using-the-hdfs-cli-with-data-lake-storage-gen2"></a>Korzystanie z interfejsu wiersza polecenia systemu plików HDFS z Data Lake Storage Gen2

Możesz uzyskiwać dostęp do danych na koncie magazynu i zarządzać nimi za pomocą interfejsu wiersza polecenia tak samo jak w przypadku [rozproszony system plików Hadoop (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). W tym artykule przedstawiono przykłady, które pomogą Ci rozpocząć pracę.

Usługa HDInsight zapewnia dostęp do kontenera rozproszonego, który jest lokalnie dołączony do węzłów obliczeniowych. Dostęp do tego kontenera można uzyskać przy użyciu powłoki, która bezpośrednio współdziała z systemem plików HDFS i innymi systemami, które obsługuje usługa Hadoop.

Aby uzyskać więcej informacji na temat interfejsu systemu plików HDFS, zobacz [oficjalną dokumentację](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html) i [Podręcznik uprawnień systemu HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)

>[!NOTE]
>Jeśli używasz Azure Databricks zamiast usługi HDInsight, a chcesz korzystać z danych przy użyciu interfejsu wiersza polecenia, możesz użyć obiektu CLI, aby móc korzystać z systemu plików datakostki. Zobacz [interfejs wiersza polecenia datakosteks](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html).

## <a name="use-the-hdfs-cli-with-an-hdinsight-hadoop-cluster-on-linux"></a>Korzystanie z interfejsu wiersza polecenia systemu plików HDFS z klastrem Hadoop usługi HDInsight w systemie Linux

Najpierw Ustanów [dostęp zdalny do usług](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-information#remote-access-to-services). W przypadku wybrania protokołu [SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) przykładowy kod programu PowerShell będzie wyglądać następująco:

```powershell
#Connect to the cluster via SSH.
ssh sshuser@clustername-ssh.azurehdinsight.net
#Execute basic HDFS commands. Display the hierarchy.
hdfs dfs -ls /
#Create a sample directory.
hdfs dfs -mkdir /samplefolder
```
Parametry połączenia można znaleźć w sekcji "SSH + logowanie do klastra" w bloku klastra usługi HDInsight w Azure Portal. Podczas tworzenia klastra określono poświadczenia SSH.

>[!IMPORTANT]
>Rozliczanie klastra usługi HDInsight rozpoczyna się po utworzeniu klastra i zostaje zatrzymane po usunięciu klastra. Opłaty są naliczane za minutę, więc jeśli klaster nie jest używany, należy go usunąć. Aby dowiedzieć się, jak usunąć klaster, zapoznaj się z [artykułem w temacie](../../hdinsight/hdinsight-delete-cluster.md). Jednak dane przechowywane na koncie magazynu z włączonym Data Lake Storage Gen2 są utrwalane nawet po usunięciu klastra usługi HDInsight.

## <a name="create-a-container"></a>Tworzenie kontenera

`hdfs dfs -D "fs.azure.createRemoteFileSystemDuringInitialization=true" -ls abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/`

* Zastąp `<container-name>` symbol zastępczy nazwą, którą chcesz przypisać do kontenera.

* Zastąp symbol zastępczy `<storage-account-name>` nazwą konta magazynu.

## <a name="get-a-list-of-files-or-directories"></a>Pobierz listę plików lub katalogów

`hdfs dfs -ls <path>`

Zastąp `<path>` symbol zastępczy identyfikatorem URI kontenera lub folderu kontenerów.

Na przykład: `hdfs dfs -ls abfs://my-file-system@mystorageaccount.dfs.core.windows.net/my-directory-name`

## <a name="create-a-directory"></a>Tworzenie katalogu

`hdfs dfs -mkdir [-p] <path>`

Zastąp `<path>` symbol zastępczy nazwą kontenera głównego lub folderem w kontenerze.

Na przykład: `hdfs dfs -mkdir abfs://my-file-system@mystorageaccount.dfs.core.windows.net/`

## <a name="delete-a-file-or-directory"></a>Usuwanie pliku lub katalogu

`hdfs dfs -rm <path>`

Zastąp `<path>` symbol zastępczy identyfikatorem URI pliku lub folderu, który chcesz usunąć.

Na przykład: `hdfs dfs -rmdir abfs://my-file-system@mystorageaccount.dfs.core.windows.net/my-directory-name/my-file-name`

## <a name="display-the-access-control-lists-acls-of-files-and-directories"></a>Wyświetlanie list Access Control (ACL) plików i katalogów

`hdfs dfs -getfacl [-R] <path>`

Przykład:

`hdfs dfs -getfacl -R /dir`

Zobacz [getfacl](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#getfacl)

## <a name="set-acls-of-files-and-directories"></a>Ustawianie list ACL plików i katalogów

`hdfs dfs -setfacl [-R] [-b|-k -m|-x <acl_spec> <path>]|[--set <acl_spec> <path>]`

Przykład:

`hdfs dfs -setfacl -m user:hadoop:rw- /file`

Zobacz [setfacl](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#setfacl)

## <a name="change-the-owner-of-files"></a>Zmień właściciela plików

`hdfs dfs -chown [-R] <new_owner>:<users_group> <URI>`

Zobacz [chown](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chown)

## <a name="change-group-association-of-files"></a>Zmień skojarzenie grupy plików

`hdfs dfs -chgrp [-R] <group> <URI>`

Zobacz [chgrp](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chgrp)

## <a name="change-the-permissions-of-files"></a>Zmień uprawnienia plików

`hdfs dfs -chmod [-R] <mode> <URI>`

Zobacz [chmod](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chmod)

Pełną listę poleceń można wyświetlić w witrynie sieci Web [podręcznika powłoki systemu plików w Apache Hadoop 2.4.1](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html) .

## <a name="next-steps"></a>Następne kroki

* [Używanie Azure Data Lake Storage Gen2 konta obsługującego Azure Databricks](./data-lake-storage-quickstart-create-databricks-account.md)

* [Informacje o listach kontroli dostępu do plików i katalogów](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)
