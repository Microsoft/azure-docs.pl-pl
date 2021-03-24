---
title: 'Azure Toolkit for Eclipse: Tworzenie aplikacji Scala dla usługi HDInsight Spark'
description: Użyj narzędzi usługi HDInsight w Azure Toolkit for Eclipse, aby opracowywać aplikacje platformy Spark zapisane w Scala i przesyłać je do klastra usługi HDInsight Spark bezpośrednio z poziomu środowiska IDE.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/13/2019
ms.openlocfilehash: e4f19afc0edf0f43b64548ae1c93ed5ff8dfa8a1
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104866577"
---
# <a name="use-azure-toolkit-for-eclipse-to-create-apache-spark-applications-for-an-hdinsight-cluster"></a>Tworzenie aplikacji Apache Spark dla klastra usługi HDInsight za pomocą Azure Toolkit for Eclipse

Użyj narzędzi HDInsight dostępnych w zestawie narzędzi platformy Azure [, aby przełożyć na](https://www.eclipse.org/) [Apache Spark](https://spark.apache.org/) aplikacje zapisane w [Scala](https://www.scala-lang.org/) i przesłać je do klastra Azure HDInsight Spark bezpośrednio z poziomu środowiska IDE. Możesz użyć wtyczki narzędzi HDInsight na kilka różnych sposobów:

* Aby opracować i przesłać aplikację Scala Spark w klastrze usługi HDInsight Spark.
* Aby uzyskać dostęp do zasobów klastra Azure HDInsight Spark.
* Aby lokalnie opracowywać i uruchamiać aplikację Scala Spark.

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster Apache Spark w usłudze HDInsight. Aby uzyskać instrukcje, zobacz [Tworzenie klastra platformy Apache Spark w usłudze Azure HDInsight](apache-spark-jupyter-spark-sql.md).

* [Java developer Kit (JDK) w wersji 8](/azure/developer/java/fundamentals/java-jdk-long-term-support).

* [Zaćmienie IDE](https://www.eclipse.org/downloads/). W tym artykule jest stosowane środowisko IDE w języku zaćmienie dla deweloperów języka Java.

## <a name="install-required-plug-ins"></a>Zainstaluj wymagane wtyczki

### <a name="install-azure-toolkit-for-eclipse"></a>Instalowanie zestawu Azure Toolkit for Eclipse

Instrukcje instalacji znajdują się w temacie [installing Azure Toolkit for Eclipse](/azure/developer/java/toolkit-for-eclipse/installation).

### <a name="install-the-scala-plug-in"></a>Instalowanie wtyczki Scala

Po otwarciu okna zaćmienie narzędzia usługi HDInsight automatycznie wykrywają, czy zainstalowano wtyczkę Scala. Wybierz **przycisk OK** , aby kontynuować, a następnie postępuj zgodnie z instrukcjami, aby zainstalować wtyczkę z portalu Marketplace. Po zakończeniu instalacji ponownie uruchom środowisko IDE.

:::image type="content" source="./media/apache-spark-eclipse-tool-plugin/auto-installation-scala1.png" alt-text="Automatyczna instalacja wtyczki Scala" border="true":::

### <a name="confirm-plug-ins"></a>Potwierdź wtyczki

1. Przejdź do   >  **witryny help zaćmienie Marketplace.**..

1. Wybierz kartę **Zainstalowano**.

1. Powinna zostać wyświetlona co najmniej:
    * Azure Toolkit for Eclipse \<version> .
    * Scala IDE \<version> .

## <a name="sign-in-to-your-azure-subscription"></a>Zaloguj się do subskrypcji platformy Azure

1. Rozpocznij przezaćmienie środowiska IDE.

1. Przejdź do **okna**  >   **Pokaż widok**  >  **inne...**  >  **Zaloguj się..**.

1. W oknie dialogowym **Pokaż widok** przejdź do Eksploratora **Azure** Azure  >  , a następnie wybierz pozycję **Otwórz**.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer1.png" alt-text="Apache Spark zaćmienie — Pokaż widok" border="true":::

1. W programie **Azure Explorer** kliknij prawym przyciskiem myszy węzeł **Azure** , a następnie wybierz pozycję **Zaloguj się**.

1. W oknie dialogowym **Logowanie do platformy Azure** wybierz metodę uwierzytelniania, wybierz pozycję **Zaloguj** i Ukończ proces logowania.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer2.png" alt-text="Apache Spark zaćmienie na platformie Azure" border="true":::

1. Po zalogowaniu się okno dialogowe **subskrypcje** zawiera listę wszystkich subskrypcji platformy Azure skojarzonych z poświadczeniami. Naciśnij przycisk **Wybierz** , aby zamknąć okno dialogowe.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/Select-Subscriptions.png" alt-text="Okno dialogowe Wybieranie subskrypcji" border="true":::

1. W **Eksploratorze Azure** przejdź do **usługi Azure**  >   **HDInsight** , aby zobaczyć klastry HDInsight Spark w ramach subskrypcji.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer3.png" alt-text="Klastry HDInsight Spark na platformie Azure Explorer3" border="true":::

1. Można dodatkowo rozwinąć węzeł Nazwa klastra, aby wyświetlić zasoby (na przykład konta magazynu) skojarzone z klastrem.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer4.png" alt-text="Rozszerzanie nazwy klastra w celu wyświetlenia zasobów" border="true":::

## <a name="link-a-cluster"></a>Łączenie klastra

Normalny klaster można połączyć za pomocą Ambari zarządzanej nazwy użytkownika. Podobnie w przypadku klastra usługi HDInsight przyłączonego do domeny można łączyć się za pomocą domeny i nazwy użytkownika, takich jak `user1@contoso.com` .

1. W programie **Azure Explorer** kliknij prawym przyciskiem myszy pozycję **HDInsight**, a następnie wybierz pozycję **Połącz klaster**.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/link-a-cluster-context-menu.png" alt-text="Menu klastra łącza programu Azure Explorer" border="true":::

1. Wprowadź **nazwę klastra**, **nazwę użytkownika** i **hasło**, a następnie wybierz przycisk **OK**. Opcjonalnie wprowadź konto magazynu, klucz magazynu, a następnie wybierz pozycję kontener magazynu dla Eksploratora usługi Storage, aby rozpocząć działanie w widoku drzewa po lewej stronie

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/link-cluster-dialog1.png" alt-text="Okno dialogowe łączenie nowego klastra usługi HDInsight" border="true":::

   > [!NOTE]  
   > Jeśli klaster jest zarejestrowany w ramach subskrypcji platformy Azure i połączony z klastrem, użyjemy podanego klucza magazynu, nazwy użytkownika i hasła.
   > :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/storage-explorer-in-Eclipse.png" alt-text="Konta usługi Azure Explorer Storage" border="true":::
   >
   > Tylko dla użytkownika klawiatury, gdy bieżący fokus jest w **kluczu magazynu**, należy użyć **klawiszy CTRL + TAB** , aby skoncentrować się na następnym polu w oknie dialogowym.

1. Połączony klaster można zobaczyć w usłudze **HDInsight**. Teraz można przesłać aplikację do tego połączonego klastra.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/hdinsight-linked-cluster.png" alt-text="Połączony klaster programu Azure Explorer HDI" border="true":::

1. Możesz również odłączyć klaster od programu **Azure Explorer**.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/hdi-unlinked-cluster.png" alt-text="Niepołączony klaster programu Azure Explorer" border="true":::

## <a name="set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster"></a>Konfigurowanie projektu Spark Scala dla klastra usługi HDInsight Spark

1. W obszarze roboczym zaćmienie IDE wybierz pozycję **plik**  >  **Nowy**  >  **projekt..**..

1. W kreatorze **nowego projektu** wybierz pozycję **projekt HDInsight**  >  **Spark w usłudze HDInsight (Scala)**. Następnie wybierz przycisk **Dalej**.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/create-hdi-scala-app-2.png" alt-text="Wybieranie projektu platformy Spark w usłudze HDInsight (Scala)" border="true":::

1. W oknie dialogowym **Nowy projekt usługi HDInsight Scala** podaj następujące wartości, a następnie wybierz pozycję **dalej**:
   * Wprowadź nazwę dla projektu.
   * W obszarze **środowiska JRE** upewnij się, że w obszarze **środowisko wykonawcze** jest ustawiona wartość **Java-1,7** lub nowsza.
   * W obszarze **Biblioteka platformy Spark** możesz wybrać opcję **Użyj Maven do skonfigurowania opcji zestawu SDK platformy Spark** .  Nasze narzędzie integruje odpowiednią wersję zestawu Spark SDK i scala SDK. Można również wybrać opcję **Dodaj ręcznie zestaw SDK platformy Spark** , pobrać i ręcznie dodać zestaw Spark SDK.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/create-hdi-scala-app-3.png" alt-text="Okno dialogowe Nowy projekt usługi HDInsight Scala" border="true":::

1. W następnym oknie dialogowym Przejrzyj szczegóły, a następnie wybierz pozycję **Zakończ**.

## <a name="create-a-scala-application-for-an-hdinsight-spark-cluster"></a>Tworzenie aplikacji Scala dla klastra usługi HDInsight Spark

1. W **Eksploratorze pakietów** rozwiń utworzony wcześniej projekt. Kliknij prawym przyciskiem myszy pozycję **src**, wybierz pozycję **Nowy**  >  **inne...**.

1. W oknie dialogowym **Wybierz kreatora** wybierz pozycję **Scala kreatorzy**  >  **Scala obiekt**. Następnie wybierz przycisk **Dalej**.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/create-scala-project1.png" alt-text="Wybieranie Kreatora tworzenia obiektu Scala" border="true":::

1. W oknie dialogowym **Utwórz nowy plik** wprowadź nazwę obiektu, a następnie wybierz pozycję **Zakończ**. Zostanie otwarty Edytor tekstu.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/create-scala-project2.png" alt-text="Kreator nowego pliku — Utwórz nowy plik" border="true":::

1. W edytorze tekstów Zastąp bieżącą zawartość następującym kodem:

    ```scala
    import org.apache.spark.SparkConf
    import org.apache.spark.SparkContext

    object MyClusterApp{
        def main (arg: Array[String]): Unit = {
        val conf = new SparkConf().setAppName("MyClusterApp")
        val sc = new SparkContext(conf)

        val rdd = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

        //find the rows that have only one digit in the seventh column in the CSV
        val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)

        rdd1.saveAsTextFile("wasbs:///HVACOut")
        }
    }
    ```

1. Uruchom aplikację w klastrze usługi HDInsight Spark:

   a. W Eksploratorze pakietów kliknij prawym przyciskiem myszy nazwę projektu, a następnie wybierz polecenie **Prześlij aplikację Spark do usługi HDInsight**.

   b. W oknie dialogowym Przesyłanie w usłudze **Spark** podaj następujące wartości, a następnie wybierz pozycję **Prześlij**:

   * W polu **Nazwa klastra** wybierz klaster usługi HDInsight Spark, na którym chcesz uruchomić aplikację.
   * Wybierz artefakt z projektu zaćmienie lub wybierz go z dysku twardego. Wartość domyślna zależy od elementu, który można kliknąć prawym przyciskiem myszy w Eksploratorze pakietów.
   * Na liście rozwijanej **Nazwa klasy głównej** Kreator wysyłania wyświetla wszystkie nazwy obiektów z projektu. Wybierz lub wprowadź jeden, który chcesz uruchomić. W przypadku wybrania artefaktu z dysku twardego należy ręcznie wprowadzić nazwę klasy głównej. 
   * Ponieważ kod aplikacji w tym przykładzie nie wymaga żadnych argumentów wiersza polecenia lub JARs odniesienia lub plików, można pozostawić pozostałe pola tekstowe puste.

     :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/create-scala-project3.png" alt-text="Okno dialogowe przesyłanie Apache Spark" border="true":::

1. Karta **przesyłanie** na platformie Spark powinna rozpocząć wyświetlanie postępu. Możesz zatrzymać aplikację, wybierając czerwony przycisk w oknie **przesyłanie platformy Spark** . Możesz również wyświetlić dzienniki dla tego konkretnej aplikacji, wybierając ikonę globusa (oznaczaną niebieską ramką na obrazie).

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/create-scala-project4.png" alt-text="Okno przesłania Apache Spark" border="true":::

## <a name="access-and-manage-hdinsight-spark-clusters-by-using-hdinsight-tools-in-azure-toolkit-for-eclipse"></a>Dostęp do klastrów usługi HDInsight Spark i zarządzanie nimi przy użyciu narzędzi usługi HDInsight w Azure Toolkit for Eclipse

Możesz wykonywać różne operacje przy użyciu narzędzi usługi HDInsight, w tym do uzyskiwania dostępu do danych wyjściowych zadania.

### <a name="access-the-job-view"></a>Dostęp do widoku zadania

1. W **Eksploratorze Azure** rozwiń węzeł **HDInsight**, a następnie nazwę klastra Spark, a następnie wybierz pozycję **zadania**.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-job-view-node.png" alt-text="Węzeł widoku zadania w programie Azure Explorer zaćmienie" border="true":::

1. Wybierz węzeł **zadania** . Jeśli wersja języka Java jest niższa niż **1,8**, narzędzia usługi HDInsight automatycznie przypomnią, że zainstalowano wtyczkę **klipu E (FX)** . Wybierz **przycisk OK** , aby kontynuować, a następnie postępuj zgodnie z instrukcjami kreatora, aby zainstalować go w witrynie zaćmienie Marketplace i ponownie uruchom program zaćmienie.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/auto-install-efxclipse.png" alt-text="Zainstaluj brakujące klipy wtyczki E (FX)" border="true":::

1. Otwórz widok zadania w węźle **zadania** . W okienku po prawej stronie karta **widok zadania platformy Spark** wyświetla wszystkie aplikacje, które zostały uruchomione w klastrze. Wybierz nazwę aplikacji, dla której chcesz zobaczyć więcej szczegółów.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-job-logs.png" alt-text="Szczegóły dzienników zadań w widoku Apache zaćmienie" border="true":::

   Następnie można wykonać jedną z następujących czynności:

   * Umieść kursor na grafie zadania. Są w nim wyświetlane podstawowe informacje o uruchomionym zadaniu. Wybierz wykres zadania i możesz zobaczyć etapy i informacje, które generuje każde zadanie.

     :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/Job-graph-stage-info.png" alt-text="Informacje o etapie grafu zadania Apache Spark" border="true":::

   * Wybierz kartę **Dziennik** , aby wyświetlić często używane dzienniki, w tym **sterowniki stderr**, **Sterownik stdout** i **Informacje o katalogu**.

     :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-job-log-info.png" alt-text="Apache Spark zaćmienie — informacje o dzienniku zadań" border="true":::

   * Otwórz interfejs użytkownika historii platformy Spark i interfejs użytkownika Apache Hadoop PRZĘDZy (na poziomie aplikacji), zaznaczając hiperlinki w górnej części okna.

### <a name="access-the-storage-container-for-the-cluster"></a>Dostęp do kontenera magazynu dla klastra

1. W Eksploratorze Azure rozwiń węzeł główny usługi **HDInsight** , aby wyświetlić listę dostępnych klastrów usługi HDInsight Spark.

1. Rozwiń nazwę klastra, aby wyświetlić konto magazynu i domyślny kontener magazynu dla klastra.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer5.png" alt-text="Konto magazynu i domyślny kontener magazynu" border="true":::

1. Wybierz nazwę kontenera magazynu skojarzonego z klastrem. W prawym okienku kliknij dwukrotnie folder **HVACOut** . Otwórz jeden z plików **części** , aby wyświetlić dane wyjściowe aplikacji.

### <a name="access-the-spark-history-server"></a>Dostęp do serwera historii platformy Spark

1. W Eksploratorze Azure kliknij prawym przyciskiem myszy nazwę klastra Spark, a następnie wybierz pozycję **Otwórz interfejs użytkownika historii platformy Spark**. Po wyświetleniu monitu wprowadź poświadczenia administratora dla klastra. Określono je podczas aprowizacji klastra.

1. Na pulpicie nawigacyjnym serwera historii platformy Spark Użyj nazwy aplikacji, aby wyszukać aplikację, która właśnie została uruchomiona. W powyższym kodzie, należy ustawić nazwę aplikacji przy użyciu `val conf = new SparkConf().setAppName("MyClusterApp")` . Dlatego nazwa aplikacji Spark została **MyClusterApp**.

### <a name="start-the-apache-ambari-portal"></a>Uruchamianie portalu Apache Ambari

1. W Eksploratorze Azure kliknij prawym przyciskiem myszy nazwę klastra Spark, a następnie wybierz pozycję **Otwórz klaster Portal zarządzania (Ambari)**.

1. Po wyświetleniu monitu wprowadź poświadczenia administratora dla klastra. Określono je podczas aprowizacji klastra.

### <a name="manage-azure-subscriptions"></a>Zarządzanie subskrypcjami platformy Azure

Domyślnie narzędzie HDInsight w Azure Toolkit for Eclipse wyświetla listę klastrów Spark ze wszystkich subskrypcji platformy Azure. W razie potrzeby możesz określić subskrypcje, dla których chcesz uzyskać dostęp do klastra.

1. W Eksploratorze Azure kliknij prawym przyciskiem myszy węzeł główny **platformy Azure** , a następnie wybierz pozycję **Zarządzaj subskrypcjami**.

1. W oknie dialogowym Usuń zaznaczenie pól wyboru dla subskrypcji, do której nie chcesz uzyskiwać dostępu, a następnie wybierz pozycję **Zamknij**. Możesz również wybrać opcję **Wyloguj się** , jeśli chcesz wylogować się z subskrypcji platformy Azure.

## <a name="run-a-spark-scala-application-locally"></a>Uruchamianie aplikacji platformy Spark Scala lokalnie

Za pomocą narzędzi usługi HDInsight w Azure Toolkit for Eclipse można uruchamiać aplikacje platformy Spark Scala lokalnie na stacji roboczej. Zazwyczaj te aplikacje nie potrzebują dostępu do zasobów klastra, takich jak kontener magazynu, i można je uruchamiać i testować lokalnie.

### <a name="prerequisite"></a>Wymaganie wstępne

Podczas uruchamiania lokalnej aplikacji platformy Spark Scala na komputerze z systemem Windows może wystąpić wyjątek opisany w temacie [Spark-2356](https://issues.apache.org/jira/browse/SPARK-2356). Ten wyjątek występuje z powodu braku **WinUtils.exe** w systemie Windows.

Aby rozwiązać ten problem, należy [Winutils.exe](https://github.com/steveloughran/winutils) do lokalizacji takiej jak **C:\WinUtils\bin**, a następnie dodać zmienną środowiskową **HADOOP_HOME** i ustawić wartość zmiennej na **C\WinUtils**.

### <a name="run-a-local-spark-scala-application"></a>Uruchamianie lokalnej aplikacji platformy Spark Scala

1. Zacznij przezaćmienie i Utwórz projekt. W oknie dialogowym **Nowy projekt** wprowadź następujące opcje, a następnie wybierz przycisk **dalej**.

1. W kreatorze **nowego projektu** wybierz pozycję **projekt HDInsight**  >  **Spark w usłudze HDInsight Local Run Sample (Scala)**. Następnie wybierz przycisk **Dalej**.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run.png" alt-text="Nowy projekt wybierz okno dialogowe kreatora" border="true":::

1. Aby podać szczegóły projektu, wykonaj kroki od 3 do 6 z wcześniejszej sekcji [Konfigurowanie projektu Spark Scala dla klastra usługi HDInsight Spark](#set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster).

1. Szablon dodaje przykładowy kod (**LogQuery**) w folderze **src** , który można uruchomić lokalnie na komputerze.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/local-scala-application.png" alt-text="Lokalizacja aplikacji LogQuery Local Scala" border="true":::

1. Kliknij prawym przyciskiem myszy **LogQuery. scala** i wybierz polecenie **Uruchom jako**  >  **1 aplikację Scala**. Dane wyjściowe podobne do tego pojawiają się na karcie **konsoli** :

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run-result.png" alt-text="Wynik uruchomienia lokalnego aplikacji platformy Spark" border="true":::

## <a name="reader-only-role"></a>Rola tylko do odczytu

Gdy użytkownicy przesyłają zadanie do klastra z uprawnieniami roli tylko do odczytu, wymagane są poświadczenia Ambari.

### <a name="link-cluster-from-context-menu"></a>Połącz klaster z menu kontekstowego

1. Zaloguj się przy użyciu konta roli tylko czytelnik.

2. W **Eksploratorze Azure** rozwiń węzeł **HDInsight** , aby wyświetlić klastry usługi HDInsight, które znajdują się w Twojej subskrypcji. Klastry oznaczone jako **"role: Reader"** mają uprawnienia roli tylko do odczytu.

    :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer6.png" alt-text="Klastry HDInsight Spark w czytniku roli programu Azure Explorer" border="true":::

3. Kliknij prawym przyciskiem myszy klaster z uprawnieniami roli tylko czytelnik. Wybierz opcję **Połącz ten klaster** z menu kontekstowego, aby połączyć klaster. Wprowadź nazwę użytkownika i hasło Ambari.

    :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer7.png" alt-text="Linki do usługi HDInsight Spark w programie Azure Explorer" border="true":::

4. Jeśli klaster zostanie połączony pomyślnie, Usługa HDInsight zostanie odświeżona.
   Etap klastra zostanie połączony.
  
    :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer8.png" alt-text="Klastry HDInsight Spark w programie Azure Explorer połączone" border="true":::

### <a name="link-cluster-by-expanding-jobs-node"></a>Łączenie klastra przez rozwijanie węzła zadania

1. Kliknij pozycję **zadania** węzeł, okna **odmowa dostępu do zadania klastra** .

2. Kliknij przycisk **Połącz ten klaster** , aby dołączyć klaster.

    :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer9.png" alt-text="Klastry HDInsight Spark na platformie Azure Explorer9" border="true":::

### <a name="link-cluster-from-spark-submission-window"></a>Połącz klaster z okna przekazywania platformy Spark

1. Utwórz projekt usługi HDInsight.

2. Kliknij prawym przyciskiem myszy pakiet. Następnie wybierz pozycję **Prześlij aplikację platformy Spark do usługi HDInsight**.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer11.png" alt-text="Przesyłanie klastrów usługi HDInsight Spark w Eksploratorze Azure" border="true":::

3. Wybierz klaster, który ma uprawnienia roli tylko do odczytu dla **nazwy klastra**. Komunikat ostrzegawczy jest wyświetlany. Możesz kliknąć przycisk **Połącz ten klaster** , aby połączyć klaster.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer15.png" alt-text="Klastry HDInsight Spark w programie Azure Explorer Połącz to" border="true":::

### <a name="view-storage-accounts"></a>Wyświetlanie kont magazynu

* W przypadku klastrów z uprawnieniem tylko do odczytu, kliknij węzeł **konta magazynu** , zostanie wystawione okno **odmowa dostępu do magazynu** .

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer13.png" alt-text="Klastry usługi HDInsight Spark w usłudze Azure Explorer Storage" border="true":::

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer12.png" alt-text="Odmówiono realizacji klastrów usługi HDInsight Spark w Eksploratorze Azure" border="true":::

* W przypadku połączonych klastrów kliknij węzeł **konta magazynu** , w którym znajduje się okno **odmowa dostępu do magazynu** .

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer14.png" alt-text="Klastry HDInsight Spark w Eksploratorze Azure denied2" border="true":::

## <a name="known-problems"></a>Znane problemy

W przypadku korzystania z **linku klastra** zaleca się podanie poświadczeń magazynu.

:::image type="content" source="./media/apache-spark-eclipse-tool-plugin/link-cluster-with-storage-credential-eclipse.png" alt-text="Łączenie klastra z zastąpeniem poświadczeń magazynu" border="true":::

Istnieją dwa tryby przesyłania zadań. Jeśli podano poświadczenie magazynu, do przesłania zadania zostanie użyty tryb wsadowy. W przeciwnym razie zostanie użyty tryb interaktywny. Jeśli klaster jest zajęty, może wystąpić błąd poniżej.

:::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-interactive-cluster-busy-upload.png " alt-text="Zapoznaj się z błędem pobierania, gdy klaster jest zajęty" border="true":::

:::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-interactive-cluster-busy-submit.png " alt-text="Zapoznaj się z błędem pobierania w przypadku, gdy klaster jest zajęty" border="true":::

## <a name="see-also"></a>Zobacz też

* [Przegląd: platforma Apache Spark w usłudze Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenariusze

* [Apache Spark z usługą BI: wykonywanie interaktywnej analizy danych przy użyciu platformy Spark w usłudze HDInsight przy użyciu narzędzi analizy biznesowej](apache-spark-use-bi-tools.md)
* [Apache Spark z Machine Learning: korzystanie z platformy Spark w usłudze HDInsight do analizowania temperatury kompilacji przy użyciu danych HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark z Machine Learning: korzystanie z platformy Spark w usłudze HDInsight do przewidywania wyników inspekcji żywności](apache-spark-machine-learning-mllib-ipython.md)
* [Analiza dzienników witryny sieci Web przy użyciu Apache Spark w usłudze HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>Tworzenie i uruchamianie aplikacji

* [Tworzenie autonomicznych aplikacji przy użyciu języka Scala](apache-spark-create-standalone-application.md)
* [Zdalne uruchamianie zadań w klastrze Apache Spark przy użyciu programu Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Narzędzia i rozszerzenia

* [Tworzenie i przesyłanie aplikacji platformy Spark Scala za pomocą Azure Toolkit for IntelliJ](apache-spark-intellij-tool-plugin.md)
* [Używanie Azure Toolkit for IntelliJ do zdalnego debugowania aplikacji Apache Spark za pośrednictwem sieci VPN](./apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Używanie Azure Toolkit for IntelliJ do zdalnego debugowania aplikacji Apache Spark za pośrednictwem protokołu SSH](./apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Korzystanie z notesów Apache Zeppelin z klastrem Apache Spark w usłudze HDInsight](apache-spark-zeppelin-notebook.md)
* [Jądra dostępne dla Jupyter Notebook w klastrze Apache Spark dla usługi HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Korzystanie z zewnętrznych pakietów z notesami Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalacja oprogramowania Jupyter na komputerze i nawiązywanie połączenia z klastrem Spark w usłudze HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>Zarządzanie zasobami

* [Zarządzanie zasobami klastra Apache Spark w usłudze Azure HDInsight](apache-spark-resource-manager.md)
* [Śledzenie i debugowanie zadań uruchamianych w klastrze Apache Spark w usłudze HDInsight](apache-spark-job-debugging.md)