---
title: 'Zestaw narzędzi platformy Azure: zdalnie Debuguj Apache Spark aplikacje — Azure HDInsight'
description: Dowiedz się, jak za pomocą narzędzi usługi HDInsight w Azure Toolkit for IntelliJ zdalnie debugować aplikacje Spark działające w klastrach usługi HDInsight za pośrednictwem sieci VPN.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 11/28/2017
ms.openlocfilehash: 2e6da1783c3bec4958783494cb6928f5a6a69a58
ms.sourcegitcommit: 28c93f364c51774e8fbde9afb5aa62f1299e649e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/30/2020
ms.locfileid: "97822356"
---
# <a name="use-azure-toolkit-for-intellij-to-debug-apache-spark-applications-remotely-in-hdinsight-through-vpn"></a>Używanie Azure Toolkit for IntelliJ do zdalnego debugowania aplikacji Apache Spark w usłudze HDInsight za pośrednictwem sieci VPN

Zalecamy zdalne debugowanie [Apache Spark](https://spark.apache.org/) aplikacji za pośrednictwem protokołu SSH. Aby uzyskać instrukcje, zobacz [zdalne debugowanie Apache Spark aplikacji w klastrze usługi HDInsight z Azure Toolkit for IntelliJ za pośrednictwem protokołu SSH](./apache-spark-intellij-tool-debug-remotely-through-ssh.md).

Ten artykuł zawiera wskazówki krok po kroku dotyczące sposobu korzystania z narzędzi usługi HDInsight w Azure Toolkit for IntelliJ do przesyłania zadań platformy Spark w klastrze usługi HDInsight Spark, a następnie debugowania go zdalnie z komputera stacjonarnego. Aby wykonać te zadania, należy wykonać następujące czynności wysokiego poziomu:

1. Utwórz sieć wirtualną platformy Azure typu lokacja-lokacja lub punkt-lokacja. W procedurach przedstawionych w tym dokumencie przyjęto założenie, że używasz sieci typu lokacja-lokacja.
1. Utwórz klaster Spark w usłudze HDInsight, który jest częścią sieci wirtualnej lokacja-lokacja.
1. Sprawdź łączność między węzłem głównym klastra a pulpitem.
1. Utwórz aplikację Scala w IntelliJ pomysł, a następnie skonfiguruj ją na potrzeby debugowania zdalnego.
1. Uruchom i Debuguj aplikację.

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**. Aby uzyskać więcej informacji, zobacz artykuł [Uzyskaj bezpłatną wersję próbną platformy Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Klaster Apache Spark w usłudze HDInsight**. Aby uzyskać instrukcje, zobacz [Tworzenie klastra platformy Apache Spark w usłudze Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* **Zestaw Oracle Java Development Kit**. Można go zainstalować z [witryny sieci Web Oracle](/azure/developer/java/fundamentals/java-jdk-long-term-support).
* **INTELLIJ pomysł**. W tym artykule jest używane wersja 2017,1. Można go zainstalować z [witryny sieci Web JetBrains](https://www.jetbrains.com/idea/download/).
* **Narzędzia usługi HDInsight w Azure Toolkit for IntelliJ**. Narzędzia HDInsight Tools for IntelliJ są dostępne w ramach Azure Toolkit for IntelliJ. Aby uzyskać instrukcje dotyczące sposobu instalowania zestawu narzędzi platformy Azure, zobacz [install Azure Toolkit for IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij-installation).
* **Zaloguj się do subskrypcji platformy Azure z pomysłu IntelliJ**. Postępuj zgodnie z instrukcjami w temacie [Use Azure Toolkit for IntelliJ, aby utworzyć aplikacje Apache Spark dla klastra usługi HDInsight](apache-spark-intellij-tool-plugin.md).
* **Obejście wyjątku**. Podczas uruchamiania aplikacji Spark Scala na potrzeby zdalnego debugowania na komputerze z systemem Windows może wystąpić wyjątek. Ten wyjątek jest wyjaśniony w platformie [Spark-2356](https://issues.apache.org/jira/browse/SPARK-2356) i występuje z powodu braku pliku WinUtils.exe w systemie Windows. Aby obejść ten błąd, należy pobrać [Winutils.exe](https://github.com/steveloughran/winutils) do lokalizacji takiej jak **C:\WinUtils\bin**. Dodaj zmienną środowiskową **HADOOP_HOME** , a następnie ustaw wartość zmiennej na **C\WinUtils**.

## <a name="step-1-create-an-azure-virtual-network"></a>Krok 1. Tworzenie sieci wirtualnej platformy Azure

Postępuj zgodnie z instrukcjami podanymi w poniższych linkach, aby utworzyć sieć wirtualną platformy Azure, a następnie sprawdź łączność między komputerem stacjonarnym i siecią wirtualną:

* [Tworzenie sieci wirtualnej z połączeniem sieci VPN typu lokacja-lokacja przy użyciu Azure Portal](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [Tworzenie sieci wirtualnej z połączeniem sieci VPN typu lokacja-lokacja przy użyciu programu PowerShell](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* [Konfigurowanie połączenia typu punkt-lokacja z siecią wirtualną przy użyciu programu PowerShell](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

## <a name="step-2-create-an-hdinsight-spark-cluster"></a>Krok 2. Tworzenie klastra usługi HDInsight Spark

Zalecamy również utworzenie klastra Apache Spark w usłudze Azure HDInsight, który jest częścią utworzonej sieci wirtualnej platformy Azure. Skorzystaj z informacji dostępnych w temacie [Tworzenie klastrów opartych na systemie Linux w usłudze HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). W ramach konfiguracji opcjonalnej wybierz sieć wirtualną platformy Azure utworzoną w poprzednim kroku.

## <a name="step-3-verify-the-connectivity-between-the-cluster-head-node-and-your-desktop"></a>Krok 3. Weryfikowanie łączności między węzłem głównym klastra a pulpitem

1. Pobierz adres IP węzła głównego. Otwórz interfejs użytkownika Ambari dla klastra. W bloku klaster wybierz pozycję **pulpit nawigacyjny**.

    ![Wybieranie pulpitu nawigacyjnego w usłudze Apache Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/launch-apache-ambari.png)

1. W interfejsie użytkownika Ambari wybierz pozycję **hosty**.

    ![Wybieranie hostów w programie Apache Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/apache-ambari-hosts1.png)

1. Zostanie wyświetlona lista węzłów głównych, węzłów procesu roboczego i węzłów dozorcy. Węzły główne mają prefiks **HN** _. Wybierz pierwszy węzeł główny.

    ![Znajdowanie węzła głównego w programie Apache Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/ambari-cluster-headnodes.png)

1. W okienku *podsumowujące** w dolnej części strony, która zostanie otwarta, skopiuj **adres IP** węzła głównego i **nazwę hosta**.

    ![Znajdowanie adresu IP w programie Apache Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/headnode-ip-address1.png)

1. Dodaj adres IP i nazwę hosta węzła głównego do pliku **hosts** na komputerze, na którym chcesz uruchomić, i zdalne debugowanie zadania Spark. Dzięki temu można komunikować się z węzłem głównym przy użyciu adresu IP, a także nazwy hosta.

   a. Otwórz plik Notatnik z podniesionymi uprawnieniami. Z menu **plik** wybierz polecenie **Otwórz**, a następnie Znajdź lokalizację pliku Hosts. Na komputerze z systemem Windows lokalizacja to **C:\Windows\System32\Drivers\etc\hosts**.

   b. Dodaj następujące informacje do pliku **hosts** :

    ```
    # For headnode0
    192.xxx.xx.xx nitinp
    192.xxx.xx.xx nitinp.lhwwghjkpqejawpqbwcdyp3.gx.internal.cloudapp.net
    
    # For headnode1
    192.xxx.xx.xx nitinp
    192.xxx.xx.xx nitinp.lhwwghjkpqejawpqbwcdyp3.gx.internal.cloudapp.net
    ```

1. Na komputerze połączonym z siecią wirtualną platformy Azure, który jest używany przez klaster usługi HDInsight, sprawdź, czy można wysyłać polecenia ping do węzłów głównych przy użyciu adresu IP, a także nazwy hosta.

1. Użyj protokołu SSH, aby nawiązać połączenie z węzłem głównym klastra, postępując zgodnie z instrukcjami podanymi w temacie [łączenie z klastrem usługi HDInsight przy użyciu protokołu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md). W węźle głównym klastra Wyślij polecenie ping do adresu IP komputera stacjonarnego. Przetestuj łączność z obydwoma adresami IP przypisanymi do komputera:

    - Jeden dla połączenia sieciowego
    - Jeden dla sieci wirtualnej platformy Azure

1. Powtórz kroki dla drugiego węzła głównego.

## <a name="step-4-create-an-apache-spark-scala-application-by-using-hdinsight-tools-in-azure-toolkit-for-intellij-and-configure-it-for-remote-debugging"></a>Krok 4. utworzenie Apache Spark aplikacji Scala przy użyciu narzędzi usługi HDInsight w programie Azure Toolkit for IntelliJ i skonfigurowanie jej do zdalnego debugowania

1. Otwórz pomysł IntelliJ i Utwórz nowy projekt. W oknie dialogowym **Nowy projekt** wykonaj następujące czynności:

    ![Wybierz nowy szablon projektu w IntelliJ pomysł](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-hdi-scala-app.png)

    a. Wybierz pozycję **HDInsight**  >  **Spark w usłudze HDInsight (Scala)**.

    b. Wybierz pozycję **Dalej**.
1. W następnym oknie dialogowym **Nowy projekt** wykonaj następujące czynności, a następnie wybierz pozycję **Zakończ**:

    - Wprowadź nazwę i lokalizację projektu.

    - Z listy rozwijanej **Zestaw SDK projektu** wybierz pozycję **Java 1.8** dla klastra Spark 2.x lub pozycję **Java 1.7** dla klastra Spark 1.x.

    - Z listy rozwijanej **wersja platformy Spark** Kreator tworzenia projektu Scala integruje odpowiednią wersję zestawu Spark SDK i scala SDK. Jeśli wersja klastra Spark jest starsza niż 2.0, wybierz wartość **Spark 1.x**. W przeciwnym razie wybierz **Spark2.x**. W tym przykładzie używana jest wersja **Spark 2.0.2 (Scala 2.11.8)**.
  
   ![Wybierz zestaw SDK projektu i wersję platformy Spark](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-scala-project-details.png)
  
1. Projekt Spark automatycznie tworzy artefakt. Aby wyświetlić artefakt, wykonaj następujące czynności:

    a. Z menu **plik** wybierz pozycję **Struktura projektu**.

    b. W oknie dialogowym **Struktura projektu** wybierz pozycję **artefakty** , aby wyświetlić domyślny artefakt, który został utworzony. Możesz również utworzyć własny artefakt, wybierając znak plus ( **+** ).

   ![Artefakty pomysłów IntelliJ tworzenie jar](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-default-artifact.png)

1. Dodaj biblioteki do projektu. Aby dodać bibliotekę, wykonaj następujące czynności:

    a. Kliknij prawym przyciskiem myszy nazwę projektu w drzewie projektu, a następnie wybierz pozycję **Otwórz ustawienia modułu**.

    b. W oknie dialogowym **Struktura projektu** wybierz pozycję **biblioteki**, wybierz **+** symbol (), a następnie wybierz pozycję **z Maven**.

    ![Biblioteka pobierania POMYSŁu IntelliJ](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/intellij-add-library.png)

    c. W oknie dialogowym **pobieranie biblioteki z repozytorium Maven** Wyszukaj i Dodaj następujące biblioteki:

   * `org.scalatest:scalatest_2.10:2.2.1`
   * `org.apache.hadoop:hadoop-azure:2.7.1`

1. Skopiuj `yarn-site.xml` i `core-site.xml` z węzła głównego klastra i Dodaj je do projektu. Użyj następujących poleceń, aby skopiować pliki. Można użyć [Cygwin](https://cygwin.com/install.html) do uruchomienia następujących `scp` poleceń w celu skopiowania plików z węzłów głównych klastra:

    ```bash
    scp <ssh user name>@<headnode IP address or host name>://etc/hadoop/conf/core-site.xml .
    ```

    Ponieważ do pliku Hosts na pulpicie został już dodany adres IP i nazwy hostów węzła głównego klastra, można użyć `scp` poleceń w następujący sposób:

    ```bash
    scp sshuser@nitinp:/etc/hadoop/conf/core-site.xml .
    scp sshuser@nitinp:/etc/hadoop/conf/yarn-site.xml .
    ```

    Aby dodać te pliki do projektu, skopiuj je do folderu **/src** w drzewie projektu, na przykład `<your project directory>\src` .

1. Zaktualizuj `core-site.xml` plik, aby wprowadzić następujące zmiany:

   a. Zastąp zaszyfrowany klucz. `core-site.xml`Plik zawiera zaszyfrowany klucz do konta magazynu skojarzonego z klastrem. W `core-site.xml` pliku, który został dodany do projektu, Zastąp zaszyfrowany klucz własnym kluczem magazynu skojarzonym z domyślnym kontem magazynu. Aby uzyskać więcej informacji, zobacz [Zarządzanie kluczami dostępu do konta magazynu](../../storage/common/storage-account-keys-manage.md).

    ```xml
    <property>
            <name>fs.azure.account.key.hdistoragecentral.blob.core.windows.net</name>
            <value>access-key-associated-with-the-account</value>
    </property>
    ```

   b. Usuń następujące wpisy z `core-site.xml` :

    ```xml
    <property>
            <name>fs.azure.account.keyprovider.hdistoragecentral.blob.core.windows.net</name>
            <value>org.apache.hadoop.fs.azure.ShellDecryptionKeyProvider</value>
    </property>

    <property>
            <name>fs.azure.shellkeyprovider.script</name>
            <value>/usr/lib/python2.7/dist-packages/hdinsight_common/decrypt.sh</value>
    </property>

    <property>
            <name>net.topology.script.file.name</name>
            <value>/etc/hadoop/conf/topology_script.py</value>
    </property>
    ```

   c. Zapisz plik.

1. Dodaj klasę główną dla aplikacji. W **Eksploratorze projektów** kliknij prawym przyciskiem myszy **pozycję src**, wskaż polecenie **Nowy**, a następnie wybierz pozycję **Klasa Scala**.

    ![IntelliJ pomysł wybierz klasę główną](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-spark-scala-code.png)

1. W oknie dialogowym **Utwórz nową klasę Scala** Podaj nazwę, wybierz pozycję **obiekt** w polu **rodzaj** , a następnie wybierz przycisk **OK**.

    ![IntelliJ pomysł tworzenia nowej klasy Scala](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-spark-scala-code-object.png)

1. W `MyClusterAppMain.scala` pliku wklej poniższy kod. Ten kod tworzy kontekst platformy Spark i otwiera `executeJob` metodę z `SparkSample` obiektu.

    ```scala
    import org.apache.spark.{SparkConf, SparkContext}

    object SparkSampleMain {
        def main (arg: Array[String]): Unit = {
        val conf = new SparkConf().setAppName("SparkSample")
                                    .set("spark.hadoop.validateOutputSpecs", "false")
        val sc = new SparkContext(conf)

        SparkSample.executeJob(sc,
                            "wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv",
                            "wasb:///HVACOut")
        }
    }
    ```

1. Powtórz kroki 8 i 9, aby dodać nowy obiekt Scala o nazwie `*SparkSample` . Dodaj następujący kod do tej klasy. Ten kod odczytuje dane z HVAC.csv (dostępne we wszystkich klastrach usługi HDInsight Spark). Pobiera wiersze, które mają tylko jedną cyfrę w siódmej kolumnie w pliku CSV, a następnie zapisuje dane wyjściowe do **/HVACOut** w domyślnym kontenerze magazynu dla klastra.

    ```scala
    import org.apache.spark.SparkContext

    object SparkSample {
        def executeJob (sc: SparkContext, input: String, output: String): Unit = {
        val rdd = sc.textFile(input)

        //find the rows which have only one digit in the 7th column in the CSV
        val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)

        val s = sc.parallelize(rdd.take(5)).cartesian(rdd).count()
        println(s)

        rdd1.saveAsTextFile(output)
        //rdd1.collect().foreach(println)
         }
    }
    ```

1. Powtórz kroki od 8 do 9, aby dodać nową klasę o nazwie `RemoteClusterDebugging` . Ta klasa implementuje platformę testową Spark, która jest używana do debugowania aplikacji. Dodaj następujący kod do `RemoteClusterDebugging` klasy:

    ```scala
        import org.apache.spark.{SparkConf, SparkContext}
        import org.scalatest.FunSuite

        class RemoteClusterDebugging extends FunSuite {

         test("Remote run") {
           val conf = new SparkConf().setAppName("SparkSample")
                                     .setMaster("yarn-client")
                                     .set("spark.yarn.am.extraJavaOptions", "-Dhdp.version=2.4")
                                     .set("spark.yarn.jar", "wasb:///hdp/apps/2.4.2.0-258/spark-assembly-1.6.1.2.4.2.0-258-hadoop2.7.1.2.4.2.0-258.jar")
                                     .setJars(Seq("""C:\workspace\IdeaProjects\MyClusterApp\out\artifacts\MyClusterApp_DefaultArtifact\default_artifact.jar"""))
                                     .set("spark.hadoop.validateOutputSpecs", "false")
           val sc = new SparkContext(conf)

           SparkSample.executeJob(sc,
             "wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv",
             "wasb:///HVACOut")
         }
        }
    ```

     Istnieje kilka ważnych kwestii, które należy wziąć pod uwagę:

      * W przypadku programu upewnij się `.set("spark.yarn.jar", "wasb:///hdp/apps/2.4.2.0-258/spark-assembly-1.6.1.2.4.2.0-258-hadoop2.7.1.2.4.2.0-258.jar")` , że zestaw Spark jest dostępny w magazynie klastra w określonej ścieżce.
      * W `setJars` polu Określ lokalizację, w której zostanie utworzony plik JAR. Zwykle jest to `<Your IntelliJ project directory>\out\<project name>_DefaultArtifact\default_artifact.jar` .

1. W `*RemoteClusterDebugging` klasie, kliknij prawym przyciskiem myszy `test` słowo kluczowe, a następnie wybierz polecenie **Utwórz konfigurację RemoteClusterDebugging**.

    ![POMYSŁ IntelliJ Utwórz konfigurację zdalną](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-remote-config.png)

1. W oknie dialogowym **Tworzenie konfiguracji RemoteClusterDebugging** Podaj nazwę konfiguracji, a następnie wybierz pozycję **rodzaj testu** jako **nazwę testu**. Pozostaw wszystkie pozostałe wartości jako ustawienia domyślne. Wybierz pozycję **Apply** (Zastosuj), a następnie wybierz przycisk **OK**.

    ![Utwórz konfigurację RemoteClusterDebugging](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/provide-config-value.png)

1. Na pasku menu powinna zostać wyświetlona lista rozwijana **Zdalna konfiguracja uruchomienia** .

    ![IntelliJ listy rozwijanej uruchamiania zdalnego](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/intellij-config-remote-run.png)

## <a name="step-5-run-the-application-in-debug-mode"></a>Krok 5. uruchomienie aplikacji w trybie debugowania

1. W projekcie POMYSŁu IntelliJ Otwórz `SparkSample.scala` i Utwórz punkt przerwania obok elementu `val rdd1` . W menu podręcznym **Utwórz punkt przerwania** wybierz pozycję **wiersz w funkcji executeJob**.

    ![POMYSŁ IntelliJ Dodaj punkt przerwania](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/intellij-create-breakpoint.png)

1. Aby uruchomić aplikację, wybierz przycisk **Uruchom** ponownie obok listy rozwijanej konfiguracja **uruchomienia zdalnego** .

    ![POMYSŁ IntelliJ wybierz przycisk Uruchom jako Debug](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-run-mode-button.png)

1. Gdy wykonanie programu osiągnie punkt przerwania, w dolnym okienku zostanie wyświetlona karta **debuger** .

    ![Widok POMYSŁu IntelliJ na karcie debuger](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/intellij-debugger-tab.png)

1. Aby dodać czujkę, wybierz ikonę ( **+** ).

    ![IntelliJ Debug-Add-Watch-Variable](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch-variable.png)

    W tym przykładzie aplikacja uległa zerwaniu przed utworzeniem zmiennej `rdd1` . Korzystając z tego czujki, zobaczymy pięć pierwszych wierszy w zmiennej `rdd` . Naciśnij klawisz **Enter**.

    ![IntelliJ uruchamiaj program w trybie debugowania](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch-variable-value.png)

    Elementy wyświetlane w poprzednim obrazie to środowisko uruchomieniowe, które umożliwia wykonywanie zapytań dotyczących terabajtów danych i debugowanie sposobu postępu aplikacji. Na przykład, w danych wyjściowych przedstawionych na poprzednim obrazie, można zobaczyć, że pierwszy wiersz danych wyjściowych jest nagłówkiem. W oparciu o te dane wyjściowe można zmodyfikować kod aplikacji, aby pominąć wiersz nagłówka, w razie potrzeby.

1. Teraz możesz wybrać ikonę **Wznów program** , aby kontynuować uruchamianie aplikacji.

    ![IntelliJ pomysł — wybierz pozycję Wznów program](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-continue-remote-run.png)

1. Jeśli aplikacja zakończy się pomyślnie, powinny pojawić się dane wyjściowe podobne do następujących:

    ![Dane wyjściowe konsoli debugera POMYSŁu IntelliJ](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-complete-window.png)

## <a name="next-steps"></a><a name="seealso"></a>Następne kroki

* [Przegląd: platforma Apache Spark w usłudze Azure HDInsight](apache-spark-overview.md)

### <a name="demo"></a>Demonstracja

* Tworzenie projektu Scala (wideo): [tworzenie Apache Spark Scala aplikacji](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Debugowanie zdalne (wideo): [użyj Azure Toolkit for IntelliJ do zdalnego debugowania aplikacji Apache Spark w klastrze usługi HDInsight](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Scenariusze

* [Apache Spark z usługą BI: wykonywanie interakcyjnej analizy danych przy użyciu platformy Spark w usłudze HDInsight przy użyciu narzędzi analizy biznesowej](apache-spark-use-bi-tools.md)
* [Apache Spark z Machine Learning: korzystanie z platformy Spark w usłudze HDInsight do analizy temperatury kompilacji przy użyciu danych HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark z Machine Learning: korzystanie z platformy Spark w usłudze HDInsight do przewidywania wyników inspekcji żywności](apache-spark-machine-learning-mllib-ipython.md)
* [Analiza dzienników witryny sieci Web przy użyciu Apache Spark w usłudze HDInsight](./apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Tworzenie i uruchamianie aplikacji

* [Tworzenie autonomicznych aplikacji przy użyciu języka Scala](./apache-spark-create-standalone-application.md)
* [Zdalne uruchamianie zadań w klastrze Apache Spark przy użyciu programu Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Narzędzia i rozszerzenia

* [Tworzenie aplikacji Apache Spark dla klastra usługi HDInsight za pomocą Azure Toolkit for IntelliJ](apache-spark-intellij-tool-plugin.md)
* [Używanie Azure Toolkit for IntelliJ do zdalnego debugowania aplikacji Apache Spark za pośrednictwem protokołu SSH](apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Tworzenie aplikacji Apache Spark przy użyciu narzędzi usługi HDInsight w Azure Toolkit for Eclipse](./apache-spark-eclipse-tool-plugin.md)
* [Korzystanie z notesów Apache Zeppelin z klastrem Apache Spark w usłudze HDInsight](apache-spark-zeppelin-notebook.md)
* [Jądra dostępne dla Jupyter Notebook w klastrze Apache Spark dla usługi HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Korzystanie z zewnętrznych pakietów z notesami Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalacja oprogramowania Jupyter na komputerze i nawiązywanie połączenia z klastrem Spark w usłudze HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Zarządzanie zasobami

* [Zarządzanie zasobami klastra Apache Spark w usłudze Azure HDInsight](apache-spark-resource-manager.md)
* [Śledzenie i debugowanie zadań uruchamianych w klastrze Apache Spark w usłudze HDInsight](apache-spark-job-debugging.md)