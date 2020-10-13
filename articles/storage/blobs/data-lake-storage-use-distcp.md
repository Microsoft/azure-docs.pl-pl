---
title: Kopiowanie danych do Azure Data Lake Storage Gen2 przy użyciu pomocą distcp | Microsoft Docs
description: Skopiuj dane do i z Azure Data Lake Storage Gen2 przy użyciu narzędzia do kopiowania rozproszonego Apache Hadoop (pomocą distcp).
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 12/06/2018
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 734ad2d45dbb27894e5da4fbeb11c0e8b60df8bd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88035675"
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-azure-data-lake-storage-gen2"></a>Użyj pomocą distcp do kopiowania danych między obiektami BLOB usługi Azure Storage a Azure Data Lake Storage Gen2

Za pomocą [pomocą distcp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) można kopiować dane między kontem magazynu ogólnego przeznaczenia w wersji 2 a kontem magazynu ogólnego przeznaczenia w wersji 2 z włączoną hierarchiczną przestrzenią nazw. Ten artykuł zawiera instrukcje dotyczące korzystania z narzędzia pomocą distcp.

Pomocą distcp udostępnia wiele parametrów wiersza polecenia i zdecydowanie zachęcamy do przeczytania tego artykułu w celu zoptymalizowania jego użycia. W tym artykule przedstawiono podstawowe funkcje podczas skoncentrowania się na używaniu do kopiowania danych do konta z włączoną obsługą hierarchicznej przestrzeni nazw.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* Istniejące konto usługi Azure Storage bez możliwości Data Lake Storage Gen2 (hierarchiczny obszar nazw) jest włączone.
* Konto usługi Azure Storage z włączonymi możliwościami Data Lake Storage Gen2 (hierarchiczny obszar nazw). Aby uzyskać instrukcje dotyczące sposobu tworzenia jednego z nich, zobacz [Tworzenie konta usługi Azure Storage](../common/storage-account-create.md) .
* Kontener, który został utworzony na koncie magazynu z włączoną hierarchiczną przestrzenią nazw.
* Klaster usługi Azure HDInsight z dostępem do konta magazynu z włączoną funkcją hierarchicznej przestrzeni nazw. Zobacz [Korzystanie z usługi Azure Data Lake Storage Gen2 w połączeniu z klastrami usługi Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). Upewnij się, że Pulpit zdalny dla klastra są włączone.

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>Korzystanie z pomocą distcp z klastra usługi HDInsight w systemie Linux

Klaster HDInsight An jest dostarczany z narzędziem pomocą distcp, które może służyć do kopiowania danych z różnych źródeł do klastra usługi HDInsight. Jeśli klaster usługi HDInsight został skonfigurowany tak, aby korzystał z platformy Azure Blob Storage i Azure Data Lake Storage razem, można użyć narzędzia pomocą distcp do kopiowania danych. W tej sekcji zawarto informacje na temat korzystania z narzędzia pomocą distcp.

1. Utwórz sesję SSH z klastrem HDI. Zobacz [nawiązywanie połączenia z klastrem usługi HDInsight opartej](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)na systemie Linux.

2. Sprawdź, czy możesz uzyskać dostęp do istniejącego konta ogólnego przeznaczenia w wersji 2 (bez włączonej hierarchicznej przestrzeni nazw).

    ```bash
    hdfs dfs –ls wasbs://<container-name>@<storage-account-name>.blob.core.windows.net/
    ```

   Dane wyjściowe powinny dostarczyć listę zawartości w kontenerze.

3. Podobnie Sprawdź, czy możesz uzyskać dostęp do konta magazynu z włączoną hierarchiczną przestrzenią nazw z poziomu klastra. Uruchom następujące polecenie:

    ```bash
    hdfs dfs -ls abfss://<container-name>@<storage-account-name>.dfs.core.windows.net/
    ```

    Dane wyjściowe powinny dostarczyć listę plików/folderów na koncie magazynu Data Lake.

4. Użyj pomocą distcp, aby skopiować dane z WASB do konta Data Lake Storage.

    ```bash
    hadoop distcp wasbs://<container-name>@<storage-account-name>.blob.core.windows.net/example/data/gutenberg abfss://<container-name>@<storage-account-name>.dfs.core.windows.net/myfolder
    ```

    Polecenie kopiuje zawartość folderu **/example/Data/Gutenberg/** w usłudze BLOB Storage do **/myfolder** na koncie Data Lake Storage.

5. Podobnie należy używać pomocą distcp do kopiowania danych z konta Data Lake Storage do Blob Storage (WASB).

    ```bash
    hadoop distcp abfss://<container-name>@<storage-account-name>.dfs.core.windows.net/myfolder wasbs://<container-name>@<storage-account-name>.blob.core.windows.net/example/data/gutenberg
    ```

    Polecenie kopiuje zawartość **/MyFolder** na koncie Data Lake Store do folderu **/example/Data/Gutenberg/** w WASB.

## <a name="performance-considerations-while-using-distcp"></a>Zagadnienia dotyczące wydajności podczas korzystania z pomocą distcp

Ponieważ najniższy stopień szczegółowości pomocą distcp jest pojedynczym plikiem, ustawienie maksymalnej liczby równoczesnych kopii jest najważniejszym parametrem, który optymalizuje go względem Data Lake Storage. Liczba równoczesnych kopii jest równa liczbie parametrów mapowania (**m**) w wierszu polecenia. Ten parametr określa maksymalną liczbę odwzorów, które są używane do kopiowania danych. Wartość domyślna to 20.

**Przykład**

```bash
hadoop distcp -m 100 wasbs://<container-name>@<storage-account-name>.blob.core.windows.net/example/data/gutenberg abfss://<container-name>@<storage-account-name>.dfs.core.windows.net/myfolder
```

### <a name="how-do-i-determine-the-number-of-mappers-to-use"></a>Jak mogę określić liczbę odwzorowań do użycia?

Oto kilka użytecznych wskazówek.

* **Krok 1. określenie całkowitej ilości pamięci dostępnej dla kolejki aplikacji przędzy domyślnej** — pierwszy krok to określenie pamięci dostępnej dla kolejki aplikacji przędzy "default". Te informacje są dostępne w portalu Ambari skojarzonym z klastrem. Przejdź do PRZĘDZy i Wyświetl kartę konfiguracje, aby wyświetlić dostępną pamięć PRZĘDZy do kolejki aplikacji "default". Jest to całkowita dostępna pamięć dla zadania pomocą distcp (w rzeczywistości jest to zadanie MapReduce).

* **Krok 2. Obliczanie liczby odwzorowań** — wartość **m** jest równa ilorazowi ŁĄCZnej ilości pamięci przędzy podzielonej przez rozmiar kontenera przędzy. Informacje o rozmiarze kontenera PRZĘDZy są również dostępne w portalu Ambari. Przejdź do PRZĘDZy i Wyświetl kartę konfiguracje. Rozmiar kontenera PRZĘDZy jest wyświetlany w tym oknie. Równanie, które ma dotrzeć do liczby odwzorowań (**m**) to

    m = (liczba węzłów * pamięć PRZĘDZy dla każdego węzła)/rozmiar kontenera PRZĘDZy

**Przykład**

Załóżmy, że masz klaster 4x D14v2s i próbujesz przenieść 10 TB danych z 10 różnych folderów. Każdy z folderów zawiera różne ilości danych, a rozmiary plików w poszczególnych folderach są inne.

* **Łączna ilość pamięci przędzy**: w portalu Ambari należy określić, że pamięć przędzy jest 96 GB dla węzła D14. W związku z tym Łączna ilość pamięci PRZĘDZy dla czterech węzłów klastra to: 

    Pamięć PRZĘDZy = 4 * 96GB = 384GB

* **Liczba odwzorowań**: w portalu Ambari należy określić, że rozmiar kontenera przędzy wynosi 3 072 MB dla węzła klastra D14. Dlatego liczba mapera jest:

    m = (4 węzły * 96GB)/3072MB = 128 mapowania

Jeśli inne aplikacje używają pamięci, można wybrać opcję użycia części pamięci PRZĘDZy klastra dla pomocą distcp.

### <a name="copying-large-datasets"></a>Kopiowanie dużych zestawów danych

Gdy rozmiar zestawu danych, który ma zostać przeniesiony, jest duży (na przykład >1 TB) lub jeśli masz wiele różnych folderów, należy rozważyć użycie wielu zadań pomocą distcp. Prawdopodobnie nie ma żadnego wzmocnienia wydajności, ale rozwiąże zadania w taki sposób, aby w przypadku niepowodzenia jakiegokolwiek zadania było wymagane ponowne uruchomienie danego zadania, a nie całego zadania.

### <a name="limitations"></a>Ograniczenia

* Pomocą distcp próbuje utworzyć maperów o rozmiarze podobnym do optymalizacji wydajności. Zwiększenie liczby mapera może nie zawsze zwiększyć wydajność.

* Pomocą distcp jest ograniczone tylko do jednego mapowania na plik. W związku z tym nie należy mieć więcej odwzorowań niż pliki. Ponieważ pomocą distcp może przypisywać tylko jeden Maper do pliku, ogranicza to stopień współbieżności, którego można użyć do kopiowania dużych plików.

* Jeśli masz niewielką liczbę dużych plików, należy podzielić je na fragmenty plików 256 MB, aby zapewnić większą możliwość współbieżności.
