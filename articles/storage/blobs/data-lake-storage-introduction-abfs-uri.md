---
title: Korzystanie z identyfikatora URI usługi Azure Data Lake Storage Gen2
description: Poznaj składnię identyfikatora URI dla identyfikatora schematu ABFS, który reprezentuje sterownik systemu plików obiektów blob platformy Azure dla Azure Data Lake Storage Gen2).
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.date: 12/06/2018
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.reviewer: jamesbak
ms.openlocfilehash: 948b5aa0ad015f9f3c693e13219ec034724687c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95913168"
---
# <a name="use-the-azure-data-lake-storage-gen2-uri"></a>Korzystanie z identyfikatora URI usługi Azure Data Lake Storage Gen2

Sterownik systemu [plików Hadoop](https://www.aosabook.org/en/hdfs.html) zgodny z Azure Data Lake Storage Gen2 jest znany przez jego identyfikator schematu `abfs` (system plików obiektów blob platformy Azure). W przypadku innych sterowników systemu plików Hadoop sterownik ABFS korzysta z formatu identyfikatora URI do adresowania plików i katalogów w ramach konta z możliwością Data Lake Storage Gen2.

## <a name="uri-syntax"></a>Składnia identyfikatora URI

Składnia identyfikatora URI dla Data Lake Storage Gen2 jest zależna od tego, czy konto magazynu zostało skonfigurowane w taki sposób, że Data Lake Storage Gen2 jako domyślny system plików.

Jeśli konto obsługujące Data Lake Storage Gen2, które ma zostać rozadresowane, **nie jest** ustawione jako domyślny system plików podczas tworzenia konta, składnia identyfikatora URI w postaci skróconej jest następująca:

<pre>abfs[s]<sup>1</sup>://&lt;file_system&gt;<sup>2</sup>@&lt;account_name&gt;<sup>3</sup>.dfs.core.windows.net/&lt;path&gt;<sup>4</sup>/&lt;file_name&gt;<sup>5</sup></pre>

1. **Identyfikator schematu**: `abfs` protokół jest używany jako identyfikator schematu. Istnieje możliwość nawiązywania połączenia z usługą lub bez Transport Layer Security (TLS), wcześniej znanej jako Secure Sockets Layer (SSL). Użyj `abfss` , aby nawiązać połączenie z połączeniem TLS.

2. **System plików**: Lokalizacja nadrzędna, w której znajdują się pliki i foldery. Są one takie same jak kontenery w usłudze Azure Storage BLOB Service.

3. **Nazwa konta**: nazwa nadana kontu magazynu podczas tworzenia.

4. **Ścieżki**: przesunięty do przodu ukośnik ( `/` ) reprezentacja struktury katalogów.

5. **Nazwa pliku**: nazwa pojedynczego pliku. Ten parametr jest opcjonalny w przypadku adresowania katalogu.

Jeśli jednak konto, które ma zostać rozadresowane, jest ustawione jako domyślny system plików podczas tworzenia konta, składnia identyfikatora URI w postaci skróconej jest następująca:

<pre>/&lt;path&gt;<sup>1</sup>/&lt;file_name&gt;<sup>2</sup></pre>

1. **Ścieżka**: rozdzielone ukośniki odwrotne ( `/` ) reprezentacja struktury katalogów.

2. **Nazwa pliku**: nazwa pojedynczego pliku.


## <a name="next-steps"></a>Następne kroki

- [Korzystanie z usługi Azure Data Lake Storage Gen2 w połączeniu z klastrami usługi Azure HDInsight](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)