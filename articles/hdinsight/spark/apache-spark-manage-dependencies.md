---
title: Zarządzanie zależnościami aplikacji platformy Spark w usłudze Azure HDInsight
description: Ten artykuł zawiera wprowadzenie do zarządzania zależnościami między platformami Spark w klastrze usługi HDInsight Spark dla aplikacji PySpark i Scala.
author: yanancai
ms.author: yanacai
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 09/09/2020
ms.openlocfilehash: dafb4485ae9b10d89fa36bd790dcf3a799054de3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90064178"
---
# <a name="manage-spark-application-dependencies"></a>Zarządzanie zależnościami aplikacji platformy Spark

W tym artykule dowiesz się, jak zarządzać zależnościami dla aplikacji Spark działających w usłudze HDInsight. Obejmujemy zarówno Scala, jak i PySpark w zakresie aplikacji Spark i klastra.

Użyj szybkich linków, aby przejść do sekcji na podstawie przypadku użytkownika:
* [Konfigurowanie zależności jar zadań platformy Spark za pomocą notesu Jupyter](#use-jupyter-notebook)
* [Skonfiguruj zależności jar zadań platformy Spark za pomocą Azure Toolkit for IntelliJ użycia](#use-azure-toolkit-for-intellij)
* [Konfigurowanie zależności jar dla klastra Spark](#jar-libs-for-cluster)
* [Bezpieczne zarządzanie zależnościami plików jar](#safely-manage-jar-dependencies)
* [Konfigurowanie pakietów języka Python zadań platformy Spark za pomocą notesu Jupyter](#use-jupyter-notebook-1)
* [Bezpieczne zarządzanie pakietami języka Python dla klastra Spark](#python-packages-for-cluster)

## <a name="jar-libs-for-one-spark-job"></a>Libs jar dla jednego zadania platformy Spark
### <a name="use-jupyter-notebook"></a>Korzystanie z notesu Jupyter
Po rozpoczęciu sesji platformy Spark w Jupyter Notebook na jądrze Spark dla Scala można skonfigurować pakiety z:

* [Repozytorium Maven](https://search.maven.org/)lub pakiety utworzone przez społeczność w [pakietach Spark](https://spark-packages.org/).
* Pliki JAR przechowywane w magazynie podstawowym klastra.

Użyjemy Magic, `%%configure` Aby skonfigurować Notes do korzystania z pakietu zewnętrznego. W notesach korzystających z pakietów zewnętrznych upewnij się, że `%%configure` w pierwszej komórce kodu jest wywoływana magiczna. Dzięki temu jądro jest skonfigurowane do korzystania z pakietu przed rozpoczęciem sesji.

>
>[!IMPORTANT]  
>Jeśli zapomnisz skonfigurować jądro w pierwszej komórce, możesz użyć `%%configure` z `-f` parametrem, ale to spowoduje ponowne uruchomienie sesji i cały postęp zostanie utracony.

**Przykład pakietów z repozytorium Maven lub pakietów platformy Spark**

Po znalezieniu pakietu z repozytorium Maven należy zebrać wartości dla **identyfikatora GroupID**, **ArtifactId**i **Version**. Połącz trzy wartości rozdzielone dwukropkiem (**:**).

   ![Połącz schemat pakietu](./media/apache-spark-manage-dependencies/spark-package-schema.png "Połącz schemat pakietu")

Upewnij się, że zebrane wartości są zgodne z klastrem. W takim przypadku używany jest pakiet łącznika Spark Cosmos DB dla Scala 2,11 i Spark 2,3 dla klastra usługi HDInsight 3,6 Spark. Jeśli nie masz pewności, uruchom `scala.util.Properties.versionString` w komórce kodu jądra platformy Spark, aby uzyskać wersję Scala klastra. Uruchom `sc.version` , aby pobrać wersję platformy Spark klastra.

```
%%configure { "conf": {"spark.jars.packages": "com.microsoft.azure:azure-cosmosdb-spark_2.3.0_2.11:1.3.3" }}
```

**Przykład dla Jars przechowywanych w magazynie podstawowym**

Użyj [schematu identyfikatora URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme) dla plików jar w podstawowym magazynie klastrów. Będzie to możliwe `wasb://` w przypadku usługi Azure Storage, `abfs://` Azure Data Lake Storage Gen2 lub `adl://` Azure Data Lake Storage Gen1. Jeśli włączono bezpieczny transfer dla usługi Azure Storage lub Data Lake Storage Gen2, identyfikator URI może być `wasbs://` lub `abfss://` . Zobacz [bezpieczny transfer](../../storage/common/storage-require-secure-transfer.md).

Użyj rozdzielanej przecinkami listy ścieżek jar dla wielu plików jar, elementy globalne są dozwolone. Jars są zawarte w sterownikach i ścieżkach klas wykonywania.

```
%%configure { "conf": {"spark.jars": "wasb://mycontainer@mystorageaccount.blob.core.windows.net/libs/azure-cosmosdb-spark_2.3.0_2.11-1.3.3.jar" }}
```

Po skonfigurowaniu pakietów zewnętrznych można uruchomić polecenie Importuj w komórce kodu, aby sprawdzić, czy pakiety zostały umieszczone prawidłowo.

```scala
import com.microsoft.azure.cosmosdb.spark._
```

### <a name="use-azure-toolkit-for-intellij"></a>Użyj Azure Toolkit for IntelliJ
[Wtyczka Azure Toolkit for IntelliJ](./apache-spark-intellij-tool-plugin.md) udostępnia środowisko interfejsu użytkownika do przesyłania aplikacji platformy Spark Scala do klastra usługi HDInsight. Udostępnia `Referenced Jars` i `Referenced Files` właściwości do konfigurowania ścieżek libs jar podczas przesyłania aplikacji platformy Spark. Zobacz więcej szczegółowych informacji na temat [korzystania z wtyczki Azure Toolkit for IntelliJ dla usługi HDInsight](./apache-spark-intellij-tool-plugin.md#run-a-spark-scala-application-on-an-hdinsight-spark-cluster).

![Okno dialogowe przesyłanie danych platformy Spark](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-02.png)

## <a name="jar-libs-for-cluster"></a>Libs jar dla klastra
W niektórych przypadkach może być konieczne skonfigurowanie zależności jar na poziomie klastra, tak aby każdą aplikację można było skonfigurować domyślnie z tymi samymi zależnościami. Podejście polega na dodaniu ścieżek jar do sterownika platformy Spark i ścieżki klas programu wykonującego.

1. Uruchom Poniższe przykładowe akcje skryptu, aby skopiować pliki jar z magazynu podstawowego `wasb://mycontainer@mystorageaccount.blob.core.windows.net/libs/*` do lokalnego systemu plików klastra `/usr/libs/sparklibs` . Ten krok jest niezbędny, ponieważ system Linux używa `:` do oddzielenia listy ścieżek klas, ale Usługa HDInsight obsługuje tylko ścieżki magazynu z takim schematem `wasb://` . Ścieżka magazynu zdalnego nie będzie działała poprawnie, jeśli zostanie bezpośrednio dodana do ścieżki klasy.

    ```bash
    sudo mkdir -p /usr/libs/sparklibs
    sudo hadoop fs -copyToLocal wasb://mycontainer@mystorageaccount.blob.core.windows.net/libs/*.* /usr/libs/sparklibs
    ```

2. Zmień konfigurację usługi platformy Spark z Ambari, aby zaktualizować ścieżkę klasy. Przejdź do **Ambari > Spark > config > Custom Spark2-Defaults**. **Dodaj właściwość** w następujący sposób. Użyj `:` do oddzielania ścieżek, jeśli masz więcej niż jedną ścieżkę do dodania. Elementy globalne są dozwolone.

    ```
    spark.driver.extraClassPath=/usr/libs/sparklibs/*
    spark.executor.extraClassPath=/usr/libs/sparklibs/*
    ```

   ![Zmień domyślną konfigurację platformy Spark](./media/apache-spark-manage-dependencies/change-spark-default-config.png "Zmień domyślną konfigurację platformy Spark")

3. Zapisz zmienione konfiguracje i ponownie uruchomione usługi.

   ![Ponownie uruchomione usługi](./media/apache-spark-manage-dependencies/restart-impacted-services.png "Ponownie uruchomione usługi")

Możesz zautomatyzować kroki przy użyciu [akcji skryptu](../hdinsight-hadoop-customize-cluster-linux.md). Akcja skryptu służąca do [dodawania bibliotek niestandardowych Hive](https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh) jest dobrym odwołaniem. Podczas zmieniania konfiguracji usługi platformy Spark upewnij się, że używasz interfejsów API Ambari zamiast bezpośrednio modyfikować pliki konfiguracji. 

## <a name="safely-manage-jar-dependencies"></a>Bezpieczne zarządzanie zależnościami plików jar
Klaster usługi HDInsight ma wbudowane zależności jar, a aktualizacje dla tych wersji jar są wykonywane od czasu do czasu. Aby uniknąć konfliktu wersji między wbudowanym Jars i jarsem, należy rozważyć [cieniowanie zależności aplikacji](./safely-manage-jar-dependency.md).

## <a name="python-packages-for-one-spark-job"></a>Pakiety języka Python dla jednego zadania platformy Spark
### <a name="use-jupyter-notebook"></a>Korzystanie z notesu Jupyter
Jądro PySpark Notes usługi HDInsight Jupyter nie obsługuje bezpośredniego instalowania pakietów języka Python z repozytorium pakietu PyPi lub Anaconda. Jeśli masz `.zip` , `.egg` , lub `.py` zależności i chcesz odwołać się do nich dla jednej sesji platformy Spark, wykonaj następujące czynności:

1. Uruchom poniżej przykładowe akcje skryptu, aby `.zip` skopiować `.egg` `.py` pliki z magazynu podstawowego `wasb://mycontainer@mystorageaccount.blob.core.windows.net/libs/*` do lokalnego systemu plików klastra lub z nich `/usr/libs/pylibs` . Ten krok jest wymagany, ponieważ system Linux używa `:` do oddzielenia listy ścieżek wyszukiwania, ale Usługa HDInsight obsługuje tylko ścieżki magazynu z takim schematem `wasb://` . Ścieżka do magazynu zdalnego nie będzie działała prawidłowo w przypadku użycia programu `sys.path.insert` .

    ```bash
    sudo mkdir -p /usr/libs/pylibs
    sudo hadoop fs -copyToLocal wasb://mycontainer@mystorageaccount.blob.core.windows.net/libs/*.* /usr/libs/pylibs
    ```

2. W notesie uruchom poniższy kod w komórce kodu przy użyciu jądra PySpark:

   ```python
   import sys
   sys.path.insert(0, "/usr/libs/pylibs/pypackage.zip")
   ```

3. Uruchom `import` , aby sprawdzić, czy pakiety zostały dołączone pomyślnie.  

## <a name="python-packages-for-cluster"></a>Pakiety języka Python dla klastra
Pakiety języka Python można instalować z Anaconda do klastra za pomocą polecenia Conda za pośrednictwem akcji skryptu. Zainstalowane pakiety są na poziomie klastra i są stosowane do wszystkich aplikacji. 

Klaster HDInsight Spark ma dwie wbudowane instalacje języka Python, Anaconda Python 2,7 i Anaconda Python 3,5. Aby dowiedzieć się więcej o domyślnych ustawieniach języka Python dla usług oraz jak bezpiecznie instalować zewnętrzne pakiety języka Python bez przerywania klastra, Zobacz więcej szczegółów w temacie [bezpieczne zarządzanie zależnościami w języku Python dla klastra](./apache-spark-python-package-installation.md).
