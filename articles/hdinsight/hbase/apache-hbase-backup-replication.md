---
title: Kopia zapasowa & replikacja dla oprogramowania Apache HBase, Phoenix — Azure HDInsight
description: Konfigurowanie kopii zapasowej i replikacji dla oprogramowania Apache HBase i Apache Phoenix w usłudze Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/19/2019
ms.openlocfilehash: fdd43a017e584a07d61d41e1af06d30db2f30ac7
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2020
ms.locfileid: "92542781"
---
# <a name="set-up-backup-and-replication-for-apache-hbase-and-apache-phoenix-on-hdinsight"></a>Konfigurowanie kopii zapasowych i replikacji dla oprogramowania Apache HBase i Apache Phoenix w usłudze HDInsight

Program Apache HBase obsługuje kilka metod ochrony przed utratą danych:

* Kopiuj `hbase` folder
* Eksportuj, a następnie Importuj
* Kopiuj tabele
* Migawki
* Replikacja

> [!NOTE]  
> Apache Phoenix przechowuje metadane w tabelach HBase, co umożliwia utworzenie kopii zapasowej metadanych podczas tworzenia kopii zapasowej tabel wykazu systemu HBase.

W poniższych sekcjach opisano scenariusz użycia dla każdego z tych metod.

## <a name="copy-the-hbase-folder"></a>Skopiuj folder HBase

To podejście polega na skopiowaniu wszystkich danych HBase, bez możliwości wyboru podzbioru tabel lub kolumn. Kolejne podejścia zapewniają większą kontrolę.

HBase w usłudze HDInsight używa domyślnego magazynu wybranego podczas tworzenia klastra, obiektów BLOB usługi Azure Storage lub Azure Data Lake Storage. W obu przypadkach HBase przechowuje dane i pliki metadanych w następującej ścieżce:

`/hbase`

* Na koncie usługi Azure Storage folder znajduje się w `hbase` katalogu głównym kontenera obiektów blob:

  `wasbs://<containername>@<accountname>.blob.core.windows.net/hbase`

* W Azure Data Lake Storage `hbase` folder znajduje się w ścieżce katalogu głównego określonego podczas aprowizacji klastra. Ta ścieżka katalogu głównego zazwyczaj ma `clusters` folder z podfolderem o nazwie po klastrze usługi HDInsight:

  `/clusters/<clusterName>/hbase`

W obu przypadkach `hbase` folder zawiera wszystkie dane, które HBase zostały opróżnione na dysk, ale nie mogą zawierać danych znajdujących się w pamięci. Zanim będzie można polegać na tym folderze jako dokładnej reprezentacji danych HBase, należy zamknąć klaster.

Po usunięciu klastra można pozostawić dane na miejscu lub skopiować dane do nowej lokalizacji:

* Utwórz nowe wystąpienie usługi HDInsight wskazujące bieżącą lokalizację magazynu. Nowe wystąpienie jest tworzone ze wszystkimi istniejącymi danymi.

* Skopiuj `hbase` folder do innego kontenera obiektów BLOB usługi Azure Storage lub lokalizacji Data Lake Storage, a następnie uruchom nowy klaster z tymi danymi. W przypadku usługi Azure Storage Użyj [AzCopy](../../storage/common/storage-use-azcopy-v10.md), a w przypadku Data Lake Storage Użyj [AdlCopy](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md).

## <a name="export-then-import"></a>Eksportuj, a następnie Importuj

W źródłowym klastrze usługi HDInsight Użyj [narzędzia eksportu](https://hbase.apache.org/book.html#export) (dołączonego do HBase) do eksportowania danych z tabeli źródłowej do domyślnego dołączonego magazynu. Następnie można skopiować wyeksportowany folder do lokalizacji magazynu docelowego i uruchomić [Narzędzie import](https://hbase.apache.org/book.html#import) w docelowym klastrze usługi HDInsight.

Aby wyeksportować dane tabeli, należy najpierw SSH do węzła głównego źródłowego klastra usługi HDInsight, a następnie uruchomić następujące `hbase` polecenie:

```console
hbase org.apache.hadoop.hbase.mapreduce.Export "<tableName>" "/<path>/<to>/<export>"
```

Katalog eksportu nie może już istnieć. W nazwie tabeli jest rozróżniana wielkość liter.

Aby zaimportować dane tabeli, Użyj protokołu SSH do węzła głównego docelowego klastra usługi HDInsight, a następnie uruchom następujące `hbase` polecenie:

```console
hbase org.apache.hadoop.hbase.mapreduce.Import "<tableName>" "/<path>/<to>/<export>"
```

Tabela musi już istnieć.

Określ pełną ścieżkę eksportu do magazynu domyślnego lub dowolną z dołączonych opcji magazynu. Na przykład w usłudze Azure Storage:

`wasbs://<containername>@<accountname>.blob.core.windows.net/<path>`

W Azure Data Lake Storage Gen2 składnia jest następująca:

`abfs://<containername>@<accountname>.dfs.core.windows.net/<path>`

W Azure Data Lake Storage Gen1 składnia jest następująca:

`adl://<accountName>.azuredatalakestore.net:443/<path>`

Takie podejście oferuje stopień szczegółowości poziomu tabeli. Możesz również określić zakres dat do uwzględnienia w wierszach, co pozwala na przyrostowe wykonywanie procesu. Każda Data jest w milisekundach od czasu epoki systemu UNIX.

```console
hbase org.apache.hadoop.hbase.mapreduce.Export "<tableName>" "/<path>/<to>/<export>" <numberOfVersions> <startTimeInMS> <endTimeInMS>
```

Należy pamiętać, że należy określić liczbę wersji każdego wiersza do wyeksportowania. Aby uwzględnić wszystkie wersje w zakresie dat, ustaw `<numberOfVersions>` wartość większą niż maksymalna możliwa wersja wiersza, taka jak 100000.

## <a name="copy-tables"></a>Kopiuj tabele

[Narzędzie kopiowania](https://hbase.apache.org/book.html#copy.table) kopiuje dane z tabeli źródłowej, wiersz po wierszu do istniejącej tabeli docelowej z tym samym schematem co źródło. Tabela docelowa może znajdować się w tym samym klastrze lub innym klastrze HBase. W nazwach tabel jest rozróżniana wielkość liter.

Aby używać kopiowania w obrębie klastra, należy użyć protokołu SSH do węzła głównego źródłowego klastra usługi HDInsight, a następnie uruchomić to `hbase` polecenie:

```console
hbase org.apache.hadoop.hbase.mapreduce.CopyTable --new.name=<destTableName> <srcTableName>
```

Aby użyć kopiowania do skopiowania do tabeli w innym klastrze, należy dodać `peer` przełącznik z adresem klastra docelowego:

```console
hbase org.apache.hadoop.hbase.mapreduce.CopyTable --new.name=<destTableName> --peer.adr=<destinationAddress> <srcTableName>
```

Adres docelowy składa się z następujących trzech części:

`<destinationAddress> = <ZooKeeperQuorum>:<Port>:<ZnodeParent>`

* `<ZooKeeperQuorum>` jest rozdzielaną przecinkami listą węzłów Apache ZooKeeper, na przykład:

    zk0-hdizc 2.54 o2oqawzlwevlfxgay2500xtg. DX. Internal. cloudapp. NET, zk4-hdizc 2.54 o2oqawzlwevlfxgay2500xtg. DX. Internal. cloudapp. NET, zk3-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net

* `<Port>` w przypadku usługi HDInsight wartość domyślna to 2181, a `<ZnodeParent>` `/hbase-unsecure` więc kompletność `<destinationAddress>` :

    zk0-hdizc 2.54 o2oqawzlwevlfxgay2500xtg. DX. Internal. cloudapp. NET, zk4-hdizc 2.54 o2oqawzlwevlfxgay2500xtg. DX. Internal. cloudapp. NET, zk3-hdizc 2.54 o2oqawzlwevlfxgay2500xtg. DX. Internal. cloudapp. NET: 2181:/HBase-Unsecure

Aby uzyskać szczegółowe informacje o tym, jak pobrać te wartości dla klastra usługi HDInsight, zobacz temat [ręczne zbieranie listy Apache ZooKeeper kworum](#manually-collect-the-apache-zookeeper-quorum-list) w tym artykule.

Narzędzie kopiowania obsługuje również parametry, aby określić zakres czasu do skopiowania i określić podzestaw rodzin kolumn w tabeli do skopiowania. Aby wyświetlić pełną listę parametrów obsługiwanych przez program kopiujący, uruchom polecenie KopiujObiekt bez żadnych parametrów:

```console
hbase org.apache.hadoop.hbase.mapreduce.CopyTable
```

Program copys skanuje całą zawartość tabeli źródłowej, która zostanie skopiowana do tabeli docelowej. Może to zmniejszyć wydajność klastra HBase podczas wykonywania kopiowania.

> [!NOTE]  
> Aby zautomatyzować kopiowanie danych między tabelami, zobacz `hdi_copy_table.sh` skrypt w repozytorium narzędzia [Azure HBase](https://github.com/Azure/hbase-utils/tree/master/replication) w witrynie GitHub.

### <a name="manually-collect-the-apache-zookeeper-quorum-list"></a>Ręcznie Zbierz listę Apache ZooKeeper kworum

Gdy oba klastry usługi HDInsight znajdują się w tej samej sieci wirtualnej, zgodnie z wcześniejszym opisem, wewnętrzne rozpoznawanie nazw hostów jest automatyczne. Aby użyć programu copys dla klastrów usługi HDInsight w dwóch oddzielnych sieciach wirtualnych połączonych VPN Gateway, należy podać adresy IP hostów węzłów dozorcy w kworum.

Aby uzyskać nazwy hostów kworum, uruchom następujące polecenie:

```console
curl -u admin:<password> -X GET -H "X-Requested-By: ambari" "https://<clusterName>.azurehdinsight.net/api/v1/clusters/<clusterName>/configurations?type=hbase-site&tag=TOPOLOGY_RESOLVED" | grep "hbase.zookeeper.quorum"
```

Polecenie zwinięcie pobiera dokument JSON z informacjami o konfiguracji HBase, a polecenie grep zwraca tylko wpis "HBase. dozorcy. kworum", na przykład:

```output
"hbase.zookeeper.quorum" : "zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk4-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk3-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net"
```

Wartość nazwy hosta kworum to cały ciąg z prawej strony dwukropka.

Aby pobrać adresy IP dla tych hostów, użyj następującego polecenia zwinięcie dla każdego hosta na poprzedniej liście:

```console
curl -u admin:<password> -X GET -H "X-Requested-By: ambari" "https://<clusterName>.azurehdinsight.net/api/v1/clusters/<clusterName>/hosts/<zookeeperHostFullName>" | grep "ip"
```

W tym zaleceniu `<zookeeperHostFullName>` jest pełną nazwą DNS hosta dozorcy, na przykład `zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net` . Dane wyjściowe polecenia zawierają adres IP dla określonego hosta, na przykład:

`100    "ip" : "10.0.0.9",`

Po zebraniu adresów IP dla wszystkich węzłów dozorcy w kworum należy ponownie skompilować adres docelowy:

`<destinationAddress>  = <Host_1_IP>,<Host_2_IP>,<Host_3_IP>:<Port>:<ZnodeParent>`

W naszym przykładzie:

`<destinationAddress> = 10.0.0.9,10.0.0.8,10.0.0.12:2181:/hbase-unsecure`

## <a name="snapshots"></a>Migawki

[Migawki](https://hbase.apache.org/book.html#ops.snapshots) umożliwiają tworzenie kopii zapasowych danych z punktu widzenia w czasie do magazynu HBase. Migawki mają minimalne obciążenie i ukończone w ciągu kilku sekund, ponieważ operacja migawki jest efektywnie operacją metadanych przechwytującą nazwy wszystkich plików znajdujących się w magazynie. W czasie wykonywania migawki nie są kopiowane rzeczywiste dane. Migawki polegają na niezmiennej naturze danych przechowywanych w systemie plików HDFS, gdzie aktualizacje, usunięcia i wstawienia są reprezentowane jako nowe dane. Można przywrócić ( *klonować* ) migawkę w tym samym klastrze lub wyeksportować migawkę do innego klastra.

Aby utworzyć migawkę, SSH do węzła głównego klastra usługi HDInsight HBase i uruchom `hbase` powłokę:

```console
hbase shell
```

W obrębie powłoki HBase Użyj polecenia Snapshot z nazwami tabeli i tej migawki:

```console
snapshot '<tableName>', '<snapshotName>'
```

Aby przywrócić migawkę według nazwy w ramach `hbase` powłoki, najpierw wyłącz tabelę, a następnie Przywróć migawkę i ponownie włącz tabelę:

```console
disable '<tableName>'
restore_snapshot '<snapshotName>'
enable '<tableName>'
```

Aby przywrócić migawkę do nowej tabeli, użyj clone_snapshot:

```console
clone_snapshot '<snapshotName>', '<newTableName>'
```

Aby wyeksportować migawkę do systemu plików HDFS w celu użycia przez inny klaster, najpierw utwórz migawkę zgodnie z opisem wcześniej, a następnie użyj narzędzia ExportSnapshot. Uruchom to narzędzie z poziomu sesji SSH do węzła głównego, a nie w ramach `hbase` powłoki:

```console
hbase org.apache.hadoop.hbase.snapshot.ExportSnapshot -snapshot <snapshotName> -copy-to <hdfsHBaseLocation>
```

`<hdfsHBaseLocation>`Może to być dowolna lokalizacja magazynu dostępna dla klastra źródłowego i powinna wskazywać folder HBase używany przez klaster docelowy. Jeśli na przykład masz dołączone konto magazynu platformy Azure do klastra źródłowego i to konto zapewnia dostęp do kontenera używanego przez domyślny magazyn klastra docelowego, można użyć tego polecenia:

```console
hbase org.apache.hadoop.hbase.snapshot.ExportSnapshot -snapshot 'Snapshot1' -copy-to 'wasbs://secondcluster@myaccount.blob.core.windows.net/hbase'
```

Jeśli nie masz dołączonego pomocniczego konta usługi Azure Storage do klastra źródłowego lub Jeśli źródłowy klaster jest klastrem lokalnym (lub klastrem innym niż HDI), możesz napotkać problemy z autoryzacją podczas próby uzyskania dostępu do konta magazynu klastra HDI. Aby rozwiązać ten problem, określ klucz dla konta magazynu jako parametr wiersza polecenia, jak pokazano w poniższym przykładzie. Klucz można uzyskać na koncie magazynu w Azure Portal.

```console
hbase org.apache.hadoop.hbase.snapshot.ExportSnapshot -Dfs.azure.account.key.myaccount.blob.core.windows.net=mykey -snapshot 'Snapshot1' -copy-to 'wasbs://secondcluster@myaccount.blob.core.windows.net/hbase'
```

Po wyeksportowaniu migawki należy użyć protokołu SSH do węzła głównego klastra docelowego i przywrócić migawkę przy użyciu `restore_snapshot` polecenia zgodnie z wcześniejszym opisem.

Migawki zapewniają kompletną kopię zapasową tabeli w czasie wykonywania `snapshot` polecenia. Migawki nie zapewniają możliwości wykonywania migawek przyrostowych w systemie Windows, ani nie określają podzbiorów rodzin kolumn do uwzględnienia w migawce.

## <a name="replication"></a>Replikacja

[Replikacja HBase](https://hbase.apache.org/book.html#_cluster_replication) automatycznie wypychanie transakcji z klastra źródłowego do klastra docelowego przy użyciu mechanizmu asynchronicznego o minimalnym obciążeniu w klastrze źródłowym. W usłudze HDInsight można skonfigurować replikację między klastrami, gdzie:

* Klastry źródłowe i docelowe znajdują się w tej samej sieci wirtualnej.
* Klastry źródłowe i docelowe znajdują się w różnych sieciach wirtualnych połączonych przez bramę sieci VPN, ale oba te klastry znajdują się w tej samej lokalizacji geograficznej.
* Klastry źródłowe i docelowe znajdują się w różnych sieciach wirtualnych połączonych przez bramę sieci VPN, a każdy klaster istnieje w innej lokalizacji geograficznej.

Poniżej przedstawiono ogólne kroki konfigurowania replikacji:

1. W klastrze źródłowym Utwórz tabele i wypełnij dane.
2. W klastrze docelowym Utwórz puste tabele docelowe ze schematem tabeli źródłowej.
3. Zarejestruj klaster docelowy jako element równorzędny w klastrze źródłowym.
4. Włącz replikację dla żądanych tabel źródłowych.
5. Skopiuj istniejące dane z tabel źródłowych do tabel docelowych.
6. Replikacja automatycznie kopiuje nowe modyfikacje danych do tabel źródłowych do tabel docelowych.

Aby włączyć replikację w usłudze HDInsight, Zastosuj akcję skryptu do uruchomionego źródłowego klastra usługi HDInsight. Aby zapoznać się z przewodnikiem dotyczącym włączania replikacji w klastrze, a także do eksperymentowania z replikacją w przykładowych klastrach utworzonych w sieciach wirtualnych przy użyciu szablonów usługi Azure Resource Management, zobacz [Konfigurowanie replikacji Apache HBase](apache-hbase-replication.md). Ten artykuł zawiera również instrukcje dotyczące włączania replikacji metadanych w Phoenix.

## <a name="next-steps"></a>Następne kroki

* [Konfigurowanie replikacji Apache HBase](apache-hbase-replication.md)
* [Praca z narzędziem importu i eksportu HBase](/archive/blogs/data_otaku/working-with-the-hbase-import-and-export-utility)