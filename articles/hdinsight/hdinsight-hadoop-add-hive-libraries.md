---
title: Apache Hive bibliotek podczas tworzenia klastra — Azure HDInsight
description: Dowiedz się, jak dodać biblioteki Apache Hive (pliki jar) do klastra usługi HDInsight podczas tworzenia klastra.
ms.service: hdinsight
ms.topic: how-to
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.date: 02/14/2020
ms.openlocfilehash: b6695e5e985a30d6f912095225c4899e1c910e34
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98945961"
---
# <a name="add-custom-apache-hive-libraries-when-creating-your-hdinsight-cluster"></a>Dodawanie niestandardowych bibliotek Apache Hive podczas tworzenia klastra usługi HDInsight

Dowiedz się, jak wstępnie ładować biblioteki [Apache Hive](https://hive.apache.org/) w usłudze HDInsight. Ten dokument zawiera informacje na temat używania akcji skryptu do wstępnego ładowania bibliotek podczas tworzenia klastra. Biblioteki dodane przy użyciu kroków opisanych w tym dokumencie są globalnie dostępne w programie Hive — nie ma potrzeby [dodawania pliku JAR](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Cli) w celu ich załadowania.

## <a name="how-it-works"></a>Jak to działa

Podczas tworzenia klastra można użyć akcji skryptu, aby zmodyfikować węzły klastra w miarę ich tworzenia. Skrypt w tym dokumencie przyjmuje jeden parametr, który jest lokalizacją bibliotek. Ta lokalizacja musi znajdować się na koncie usługi Azure Storage, a biblioteki muszą być przechowywane jako pliki jar.

Podczas tworzenia klastra skrypt wylicza pliki, kopiuje je do `/usr/lib/customhivelibs/` katalogu w węzłach głównych i procesów roboczych, a następnie dodaje je do `hive.aux.jars.path` właściwości w `core-site.xml` pliku. W klastrach opartych na systemie Linux plik zostanie również zaktualizowany `hive-env.sh` przy użyciu lokalizacji plików.

Użycie akcji skryptu w tym artykule sprawia, że biblioteki są dostępne w przypadku korzystania z klienta programu Hive dla **WebHCat** i **serwera hiveserver2**.

## <a name="the-script"></a>Skrypt

**Lokalizacja skryptu**

[https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1](https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1)

### <a name="requirements"></a>Wymagania

* Skrypty muszą być stosowane zarówno do **węzłów głównych** , jak i **węzłów procesu roboczego**.

* Jars, które chcesz zainstalować, muszą być przechowywane w usłudze Azure Blob Storage w **jednym kontenerze**.

* Podczas tworzenia konta magazynu zawierającego bibliotekę plików jar **należy** połączyć z klastrem usługi HDInsight. Musi to być domyślne konto magazynu lub konto dodane za pomocą __ustawień konta magazynu__.

* Ścieżka WASB do kontenera musi być określona jako parametr akcji skryptu. Na przykład, jeśli Jars są przechowywane w kontenerze o nazwie **libs** na koncie magazynu o nazwie Moja **Magazyn**, parametr będzie `wasbs://libs@mystorage.blob.core.windows.net/` .

  > [!NOTE]  
  > W tym dokumencie przyjęto założenie, że utworzono już konto magazynu, kontener obiektów blob i przekazano do niego pliki.
  >
  > Jeśli nie utworzono konta magazynu, możesz to zrobić za pomocą [Azure Portal](https://portal.azure.com). Następnie można użyć narzędzia, takiego jak [Eksplorator usługi Azure Storage](https://storageexplorer.com/) , aby utworzyć kontener na koncie i przekazać do niego pliki.

## <a name="create-a-cluster-using-the-script"></a>Tworzenie klastra przy użyciu skryptu

1. Rozpocznij Inicjowanie obsługi klastra, wykonując czynności opisane w temacie [Inicjowanie obsługi klastrów usługi HDInsight w systemie Linux](hdinsight-hadoop-provision-linux-clusters.md), ale nie Ukończ aprowizacji. Do utworzenia klastra korzystającego z tego skryptu można również użyć Azure PowerShell lub zestawu .NET SDK usługi HDInsight. Aby uzyskać więcej informacji na temat korzystania z tych metod, zobacz [Dostosowywanie klastrów usługi HDInsight za pomocą akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md). W przypadku Azure Portal na karcie **Konfiguracja i Cennik** wybierz **akcję + Dodaj skrypt**.

1. W przypadku **magazynu**, jeśli konto magazynu zawierające bibliotekę plików jar będzie inne niż konto używane przez klaster, uzupełnij **dodatkowe konta magazynu**.

1. W przypadku **akcji skryptu** podaj następujące informacje:

    |Właściwość |Wartość |
    |---|---|
    |Typ skryptu|-Niestandardowe|
    |Nazwa|Biblioteki |
    |Identyfikator URI skryptu bash|`https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh`|
    |Typy węzłów|Kierownik, proces roboczy|
    |Parametry|Wprowadź adres WASB do kontenera i konta magazynu, które zawiera Jars. Na przykład `wasbs://libs@mystorage.blob.core.windows.net/`.|

    > [!NOTE]
    > W przypadku Apache Spark 2,1 Użyj tego identyfikatora URI skryptu bash: `https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v00.sh` .

1. Kontynuuj Inicjowanie obsługi klastra zgodnie z opisem w temacie [Inicjowanie obsługi klastrów usługi HDInsight w systemie Linux](hdinsight-hadoop-provision-linux-clusters.md).

Po zakończeniu tworzenia klastra możesz użyć Jars dodanego za pośrednictwem tego skryptu z Hive, bez konieczności używania `ADD JAR` instrukcji.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat pracy z usługą Hive, zobacz [Korzystanie z Apache Hive z usługą HDInsight](hadoop/hdinsight-use-hive.md)
