---
title: Używanie Azure Toolkit for IntelliJ z piaskownicą Hortonworks
description: Dowiedz się, jak używać narzędzi HDInsight w Azure Toolkit for IntelliJ z piaskownicą Hortonworks.
keywords: narzędzia Hadoop, zapytanie Hive, IntelliJ, Piaskownica Hortonworks, zestaw narzędzi platformy Azure dla IntelliJ
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 05/16/2018
ms.openlocfilehash: 986c2783f3d130114da93406bfaf70d5c038d5ed
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2020
ms.locfileid: "82856152"
---
# <a name="use-hdinsight-tools-for-intellij-with-hortonworks-sandbox"></a>Korzystanie z narzędzi HDInsight Tools for IntelliJ z piaskownicą Hortonworks

Dowiedz się, jak tworzyć aplikacje Apache Scala przy użyciu narzędzi HDInsight Tools for IntelliJ, a następnie testować aplikacje na [Hortonworks piaskownicy](https://hortonworks.com/products/sandbox/) działającej na komputerze. 

[Pomysł IntelliJ](https://www.jetbrains.com/idea/) to zintegrowane środowisko programistyczne (IDE) języka Java przeznaczone do tworzenia oprogramowania komputerowego. Po opracowaniu i przetestowaniu aplikacji w piaskownicy Hortonworks możesz przenieść aplikacje do [usługi Azure HDInsight](apache-hadoop-introduction.md).

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem pracy z tym artykułem należy dysponować następującymi elementami:

- Hortonworks Data Platform (HDP) 2,4 w piaskownicy Hortonworks działającej na komputerze lokalnym. Aby skonfigurować HDP, zobacz [wprowadzenie do ekosystemu Apache Hadoop przy użyciu piaskownicy usługi Hadoop na maszynie wirtualnej](apache-hadoop-emulator-get-started.md). 
    > [!NOTE]
    > Narzędzia HDInsight Tools for IntelliJ zostały przetestowane tylko z HDP 2,4. Aby uzyskać HDP 2,4, rozwiń węzeł **archiwum piaskownicy Hortonworks** w [witrynie plików piaskownicy Hortonworks](https://hortonworks.com/downloads/#sandbox).

- [Zestaw Java developer Kit (JDK) w wersji 1,8 lub nowszej](https://aka.ms/azure-jdks). Azure Toolkit for IntelliJ wymaga JDK.

- [INTELLIJ pomysł Community Edition](https://www.jetbrains.com/idea/download) z wtyczką [Scala](https://plugins.jetbrains.com/idea/plugin/1347-scala) i wtyczką [Azure Toolkit for IntelliJ](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij) . Narzędzia HDInsight Tools for IntelliJ są dostępne w ramach Azure Toolkit for IntelliJ.

Aby zainstalować wtyczki:

  1. Otwórz środowisko IntelliJ IDEA.
  2. Na stronie **powitalnej** wybierz pozycję **Konfiguruj**, a następnie wybierz pozycję **wtyczki**.
  3. W lewym dolnym rogu wybierz pozycję **Zainstaluj wtyczkę JetBrains**.
  4. Użyj funkcji wyszukiwania, aby wyszukać **Scala**, a następnie wybierz pozycję **Zainstaluj**.
  5. Aby ukończyć instalację, wybierz pozycję **Uruchom ponownie INTELLIJ pomysł**.
  6. Powtórz kroki 4 i 5, aby zainstalować **Azure Toolkit for IntelliJ**. Aby uzyskać więcej informacji, zobacz [Install Azure Toolkit for IntelliJ](https://docs.microsoft.com/azure/azure-toolkit-for-intellij-installation).

## <a name="create-an-apache-spark-scala-application"></a>Tworzenie Apache Spark aplikacji Scala

W tej sekcji utworzysz przykładowy projekt Scala za pomocą POMYSŁu IntelliJ. W następnej sekcji utworzysz IntelliJ pomysł do piaskownicy Hortonworks (emulator) przed przesłaniem projektu.

1. Otwórz pomysł IntelliJ na komputerze. W oknie dialogowym **Nowy projekt** wykonaj następujące kroki:

   1. Wybierz pozycję **HDInsight** > **Spark w usłudze HDInsight (Scala)**.
   2. Na liście **narzędzia kompilacji** wybierz jedną z następujących opcji, w zależności od scenariusza:

      * **Maven**: na potrzeby obsługi Kreatora tworzenia projektów Scala.
      * **SBT**: na potrzeby zarządzania zależnościami i kompilowania dla projektu Scala.

   ![IntelliJ Utwórz nowy projekt Scala](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project.png)

2. Wybierz pozycję **Dalej**.
3. W następnym oknie dialogowym **Nowy projekt** wykonaj następujące czynności:

   1. W polu **Nazwa projektu** wprowadź nazwę projektu.
   2. W polu **Lokalizacja projektu** wprowadź lokalizację projektu.
   3. Obok listy rozwijanej **zestaw SDK projektu** wybierz pozycję **Nowy**, wybierz pozycję **JDK**, a następnie określ folder dla języka Java JDK w wersji 1,7 lub nowszej. Wybierz pozycję **Java 1,8** dla klastra Spark 2. x. Wybierz pozycję **Java 1,7** dla klastra Spark 1. x. Domyślna lokalizacja to C:\Program Files\Java\jdk1.8. x_xxx.
   4. Z listy rozwijanej **wersja platformy Spark** Kreator tworzenia projektu Scala integruje poprawną wersję zestawu SDK Spark i zestawu SDK Scala. Jeśli wersja klastra Spark jest starsza niż 2.0, wybierz wartość **Spark 1.x**. W przeciwnym razie wybierz **Spark2.x**. W tym przykładzie jest stosowane rozwiązanie Spark 1.6.2 (Scala 2.10.5). Upewnij się, że używasz repozytorium oznaczonego jako **Scala 2.10. x**. Nie należy używać repozytorium oznaczonego jako Scala 2.11. x.
    
      ![Tworzenie właściwości projektu IntelliJ Scala](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project-properties.png)


4. Wybierz pozycję **Zakończ**.
5. Jeśli widok **projektu** nie jest jeszcze otwarty, naciśnij klawisze **Alt + 1** , aby go otworzyć.
6. W **Eksploratorze projektów**rozwiń projekt, a następnie wybierz pozycję **src**.
7. Kliknij prawym przyciskiem myszy pozycję **src**, wskaż polecenie **Nowy**, a następnie wybierz pozycję **Klasa Scala**.
8. W polu **Nazwa** wprowadź nazwę. W polu **rodzaj** wybierz pozycję **obiekt**. Następnie wybierz przycisk **OK**.

    ![Okno dialogowe Tworzenie nowej klasy Scala](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-new-scala-class.png)

9. W pliku. scala wklej następujący kod:

        import java.util.Random
        import org.apache.spark.{SparkConf, SparkContext}
        import org.apache.spark.SparkContext._

        /**
        * Usage: GroupByTest [numMappers] [numKVPairs] [valSize] [numReducers]
        */
        object GroupByTest {
            def main(args: Array[String]) {
                val sparkConf = new SparkConf().setAppName("GroupBy Test")
                var numMappers = 3
                var numKVPairs = 10
                var valSize = 10
                var numReducers = 2

                val sc = new SparkContext(sparkConf)

                val pairs1 = sc.parallelize(0 until numMappers, numMappers).flatMap { p =>
                val ranGen = new Random
                var arr1 = new Array[(Int, Array[Byte])](numKVPairs)
                for (i <- 0 until numKVPairs) {
                    val byteArr = new Array[Byte](valSize)
                    ranGen.nextBytes(byteArr)
                    arr1(i) = (ranGen.nextInt(Int.MaxValue), byteArr)
                }
                arr1
                }.cache
                // Enforce that everything has been calculated and in cache.
                pairs1.count

                println(pairs1.groupByKey(numReducers).count)
            }
        }

10. W menu **kompilacja** wybierz polecenie **Kompiluj projekt**. Upewnij się, że kompilacja zakończyła się pomyślnie.


## <a name="link-to-the-hortonworks-sandbox"></a>Link do piaskownicy Hortonworks

Aby można było połączyć się z piaskownicą Hortonworks (emulator), musisz mieć istniejącą aplikację IntelliJ.

Aby utworzyć link do emulatora:

1. Otwórz projekt w IntelliJ.
2. W menu **Widok** wybierz pozycję **narzędzia okna**, a następnie wybierz pozycję **Eksplorator platformy Azure**.
3. Rozwiń węzeł **Azure**, kliknij prawym przyciskiem myszy pozycję **HDInsight**, a następnie wybierz pozycję **Połącz emulator**.
4. W oknie dialogowym **łączenie nowego emulatora** wprowadź hasło, które zostało ustawione dla konta głównego piaskownicy Hortonworks. Następnie wprowadź wartości podobne do tych, które są używane na poniższym zrzucie ekranu. Następnie wybierz przycisk **OK**. 

   ![Okno dialogowe łączenie nowego emulatora](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-link-an-emulator.png)

5. Aby skonfigurować emulator, wybierz pozycję **tak**.

Po pomyślnym nawiązaniu połączenia emulatora (piaskownicy Hortonworks) zostanie wyświetlona w węźle usługi HDInsight.

## <a name="submit-the-spark-scala-application-to-the-hortonworks-sandbox"></a>Przesyłanie aplikacji platformy Spark Scala do piaskownicy Hortonworks

Po dołączeniu POMYSŁu IntelliJ do emulatora można przesłać projekt.

Aby przesłać projekt do emulatora:

1. W **Eksploratorze projektów**kliknij prawym przyciskiem myszy projekt, a następnie wybierz polecenie **Prześlij aplikację Spark do usługi HDInsight**.
2. Wykonaj następujące czynności:

    1. Na liście rozwijanej **klaster Spark (tylko system Linux)** wybierz lokalną piaskownicę Hortonworks.
    2. W polu **Nazwa klasy głównej** wybierz lub wprowadź nazwę klasy głównej. W tym artykule nazwa to **GroupByTest**.

3. Wybierz pozycję **Prześlij**. Dzienniki przesłania zadania są wyświetlane w oknie narzędzia do przesłaniania Spark.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak za [pomocą narzędzi usługi HDInsight w Azure Toolkit for IntelliJ utworzyć aplikacje Apache Spark dla klastra usługi HDInsight Spark Linux](../spark/apache-spark-intellij-tool-plugin.md).

- Film wideo dotyczący narzędzi HDInsight Tools for IntelliJ można znaleźć w temacie [wprowadzenie do narzędzia HDInsight Tools for IntelliJ for Apache Spark Development](https://www.youtube.com/watch?v=YTZzYVgut6c).

- Dowiedz się, jak [zdalnie debugować aplikacje Apache Spark w klastrze usługi HDInsight przy użyciu Azure Toolkit for IntelliJ za pośrednictwem protokołu SSH](../spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md).

- Dowiedz się, jak za [pomocą narzędzi usługi HDInsight w Azure Toolkit for IntelliJ debugować aplikacje Apache Spark zdalnie w klastrze usługi HDInsight Spark Linux](../spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md).

- Dowiedz się, jak [tworzyć aplikacje Apache Spark przy użyciu narzędzi usługi HDInsight w Azure Toolkit for Eclipse](../spark/apache-spark-eclipse-tool-plugin.md).

