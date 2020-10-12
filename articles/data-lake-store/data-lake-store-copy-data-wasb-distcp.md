---
title: Kopiowanie danych do i z programu WASB do Azure Data Lake Storage Gen1 przy użyciu pomocą distcp
description: Za pomocą narzędzia pomocą distcp skopiuj dane do i z obiektów BLOB usługi Azure Storage do Azure Data Lake Storage Gen1
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 01/03/2020
ms.author: twooley
ms.openlocfilehash: c608f357eb1eff9fd36e583b98d26250a71cb923
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85515681"
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-azure-data-lake-storage-gen1"></a>Użyj pomocą distcp do kopiowania danych między obiektami BLOB usługi Azure Storage a Azure Data Lake Storage Gen1

> [!div class="op_single_selector"]
> * [Korzystanie z narzędzia DistCp](data-lake-store-copy-data-wasb-distcp.md)
> * [Korzystanie z narzędzia AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
>
>

Jeśli masz klaster usługi HDInsight z dostępem do Azure Data Lake Storage Gen1, możesz użyć narzędzi ekosystemu usługi Hadoop, takich jak pomocą distcp, do kopiowania danych do i z magazynu klastrów usługi HDInsight (WASB) do konta Data Lake Storage Gen1. W tym artykule pokazano, jak używać narzędzia pomocą distcp.

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Konto Azure Data Lake Storage Gen1**. Aby uzyskać instrukcje dotyczące sposobu tworzenia takiego elementu, zobacz Wprowadzenie [do Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md).
* **Klaster usługi Azure HDInsight** z dostępem do konta Data Lake Storage Gen1. Zobacz [Tworzenie klastra usługi HDInsight przy użyciu Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md). Upewnij się, że Pulpit zdalny dla klastra są włączone.

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>Korzystanie z pomocą distcp z klastra usługi HDInsight w systemie Linux

Klaster HDInsight An jest dostarczany z narzędziem pomocą distcp, które może służyć do kopiowania danych z różnych źródeł do klastra usługi HDInsight. Jeśli klaster usługi HDInsight został skonfigurowany tak, aby używał Data Lake Storage Gen1 jako dodatkowego magazynu, możesz użyć wbudowanej pomocą distcp do kopiowania danych do i z konta Data Lake Storage Gen1. W tej sekcji zawarto informacje na temat korzystania z narzędzia pomocą distcp.

1. Z poziomu pulpitu Użyj protokołu SSH, aby nawiązać połączenie z klastrem. Zobacz [nawiązywanie połączenia z klastrem usługi HDInsight opartej](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)na systemie Linux. Uruchom polecenia z wiersza polecenia SSH.

1. Sprawdź, czy możesz uzyskać dostęp do obiektów BLOB usługi Azure Storage (WASB). Uruchom następujące polecenie:

   ```
   hdfs dfs –ls wasb://<container_name>@<storage_account_name>.blob.core.windows.net/
   ```

   Dane wyjściowe zawierają listę zawartości w obiekcie blob magazynu.

1. Podobnie Sprawdź, czy możesz uzyskać dostęp do konta Data Lake Storage Gen1 z klastra. Uruchom następujące polecenie:

   ```
   hdfs dfs -ls adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/
   ```

    Dane wyjściowe zawierają listę plików i folderów na koncie Data Lake Storage Gen1.

1. Użyj pomocą distcp, aby skopiować dane z WASB do konta Data Lake Storage Gen1.

   ```
   hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder
   ```

    Polecenie kopiuje zawartość folderu **/example/Data/Gutenberg/** w WASB do **/myfolder** na koncie Data Lake Storage Gen1.

1. Podobnie należy używać pomocą distcp do kopiowania danych z konta Data Lake Storage Gen1 do WASB.

   ```
   hadoop distcp adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg
   ```

    Polecenie kopiuje zawartość **/MyFolder** na koncie Data Lake Storage Gen1 do folderu **/example/Data/Gutenberg/** w WASB.

## <a name="performance-considerations-while-using-distcp"></a>Zagadnienia dotyczące wydajności podczas korzystania z pomocą distcp

Ponieważ najniższy stopień szczegółowości narzędzia pomocą distcp to pojedynczy plik, ustawienie maksymalnej liczby jednoczesnych kopii jest najważniejszym parametrem umożliwiającym jego optymalizację względem Data Lake Storage Gen1. Liczbę równoczesnych kopii można kontrolować przez ustawienie parametru liczba mapera ('m) w wierszu polecenia. Ten parametr określa maksymalną liczbę odwzorów, które są używane do kopiowania danych. Wartość domyślna to 20.

Przykład:

```
 hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder -m 100
```

### <a name="how-to-determine-the-number-of-mappers-to-use"></a>Jak określić liczbę mapera do użycia

Oto kilka użytecznych wskazówek.

* **Krok 1. określenie łącznej ilości pamięci przędzy** — pierwszy krok polega na określeniu dostępnej pamięci przędzy dla klastra, w którym uruchamiane jest zadanie pomocą distcp. Te informacje są dostępne w portalu Ambari skojarzonym z klastrem. Przejdź do PRZĘDZy i Wyświetl **kartę konfiguracje, aby** wyświetlić pamięć przędzy. Aby uzyskać łączną ilość pamięci PRZĘDZy, należy pomnożyć ilość pamięci PRZĘDZy na węzeł z liczbą węzłów w klastrze.

* **Krok 2. Obliczanie liczby odwzorowań** — wartość **m** jest równa ilorazowi ŁĄCZnej ilości pamięci przędzy podzielonej przez rozmiar kontenera przędzy. Informacje o rozmiarze kontenera PRZĘDZy są również dostępne w portalu Ambari. Przejdź do PRZĘDZy i Wyświetl **kartę konfiguracje** . Rozmiar kontenera PRZĘDZy jest wyświetlany w tym oknie. Równanie, które ma dotrzeć do liczby odwzorowań (**m**):

   `m = (number of nodes * YARN memory for each node) / YARN container size`

Przykład:

Załóżmy, że masz cztery węzły D14v2s w klastrze i chcesz przenieść 10 TB danych z 10 różnych folderów. Każdy z folderów zawiera różne ilości danych, a rozmiary plików w poszczególnych folderach są inne.

* Łączna ilość pamięci PRZĘDZy — w portalu Ambari należy określić, że pamięć PRZĘDZy jest 96 GB dla węzła D14. W związku z tym Łączna ilość pamięci PRZĘDZy dla czterech węzłów klastra to:

   `YARN memory = 4 * 96GB = 384GB`

* Liczba odwzorowań — w portalu Ambari należy określić, że rozmiar kontenera PRZĘDZy jest 3072 dla węzła klastra D14. Liczba odwzorowań to:

   `m = (4 nodes * 96GB) / 3072MB = 128 mappers`

Jeśli inne aplikacje używają pamięci, można użyć tylko części pamięci PRZĘDZy klastra dla pomocą distcp.

### <a name="copying-large-datasets"></a>Kopiowanie dużych zestawów danych

Gdy rozmiar zestawu danych do przeniesienia jest duży (na przykład > 1 TB) lub jeśli masz wiele różnych folderów, rozważ użycie wielu zadań pomocą distcp. Prawdopodobnie nie ma żadnego wzmocnienia wydajności, ale rozwiąże zadania w taki sposób, że w przypadku niepowodzenia jakiegokolwiek zadania należy ponownie uruchomić to konkretne zadanie zamiast całego zadania.

### <a name="limitations"></a>Ograniczenia

* Pomocą distcp próbuje utworzyć maperów o rozmiarze podobnym do optymalizacji wydajności. Zwiększenie liczby mapera może nie zawsze zwiększyć wydajność.

* Pomocą distcp jest ograniczone tylko do jednego mapowania na plik. W związku z tym nie należy mieć więcej odwzorowań niż pliki. Ponieważ pomocą distcp może przypisać tylko jeden Maper do pliku, ogranicza to stopień współbieżności, którego można użyć do kopiowania dużych plików.

* Jeśli masz małą liczbę dużych plików, Podziel je na fragmenty plików 256-MB, aby zapewnić większą możliwość współbieżności.

* W przypadku kopiowania z konta usługi Azure Blob Storage zadanie kopiowania może być ograniczone na stronie usługi BLOB Storage. Stanowi to spadek wydajności zadania kopiowania. Aby dowiedzieć się więcej na temat limitów usługi Azure Blob Storage, zobacz limity usługi Azure Storage w obszarze [limity subskrypcji i usług platformy Azure](../azure-resource-manager/management/azure-subscription-service-limits.md).

## <a name="see-also"></a>Zobacz też

* [Kopiowanie danych z obiektów BLOB usługi Azure Storage do Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)
* [Zabezpieczanie danych w usłudze Data Lake Storage 1. generacji](data-lake-store-secure-data.md)
* [Użyj Azure Data Lake Analytics z Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Korzystanie z usługi Azure HDInsight z usługą Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
