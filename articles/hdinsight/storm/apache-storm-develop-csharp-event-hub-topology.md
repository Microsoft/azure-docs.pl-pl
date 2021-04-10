---
title: Przetwarzanie zdarzeń z Event Hubs przy użyciu burzy — Azure HDInsight
description: Dowiedz się, jak przetwarzać dane z usługi Azure Event Hubs przy użyciu topologii burzy języka C# utworzonej w programie Visual Studio przy użyciu narzędzi HDInsight Tools for Visual Studio.
ms.service: hdinsight
ms.topic: how-to
ms.date: 01/14/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: ee483872535c58bde521cf0a751058fdc2fecd40
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104871456"
---
# <a name="process-events-from-azure-event-hubs-with-apache-storm-on-hdinsight-c"></a>Przetwarzanie zdarzeń z usługi Azure Event Hubs przy użyciu Apache Storm w usłudze HDInsight (C#)

Dowiedz się, jak korzystać z usługi Azure Event Hubs [Apache Storm](https://storm.apache.org/) w usłudze HDInsight. Ten dokument używa topologii burzy języka C# do odczytywania i zapisywania danych z Event Hubs

> [!NOTE]  
> W przypadku wersji języka Java tego projektu zobacz [zdarzenia przetwarzania z platformy Azure Event Hubs przy użyciu Apache Storm w usłudze HDInsight (Java)](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub).

## <a name="scpnet"></a>SCP.NET

W procedurach przedstawionych w tym dokumencie używany jest pakiet NuGet SCP.NET, który ułatwia tworzenie topologii i składników języka C# do użycia z burzą w usłudze HDInsight.

Usługa HDInsight 3,4 i więcej używają narzędzi mono do uruchamiania topologii języka C#. Przykład użyty w tym dokumencie współdziała z usługą HDInsight 3,6. Jeśli planujesz tworzenie własnych rozwiązań .NET dla usługi HDInsight, Sprawdź dokument [zgodności mono](https://www.mono-project.com/docs/about-mono/compatibility/) , aby uzyskać potencjalne niezgodności.

### <a name="cluster-versioning"></a>Przechowywanie wersji klastra

Pakiet NuGet Microsoft. SCP. NET. SDK używany dla projektu musi być zgodny z wersją główną burzy w usłudze HDInsight. Usługi HDInsight w wersji 3,5 i 3,6 używają burzy 1. x, dlatego należy użyć SCP.NET w wersji 1.0. x. x z tymi klastrami.

Topologie języka C# muszą również kierować platformą .NET 4,5.

## <a name="how-to-work-with-event-hubs"></a>Jak korzystać z Event Hubs

Firma Microsoft udostępnia zestaw składników języka Java, których można użyć do komunikowania się z Event Hubs z topologii burzy. Plik archiwum Java (JAR) zawierający zgodną wersję usługi HDInsight 3,6 można znaleźć pod adresem [https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar](https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar) .

> [!IMPORTANT]  
> Chociaż składniki są zapisywane w języku Java, można je łatwo wykorzystać z poziomu topologii języka C#.

W tym przykładzie używane są następujące składniki:

* __EventHubSpout__: odczytuje dane z Event Hubs.
* __EventHubBolt__: zapisuje dane do Event Hubs.
* __EventHubSpoutConfig__: służy do konfigurowania EventHubSpout.
* __EventHubBoltConfig__: służy do konfigurowania EventHubBolt.

### <a name="example-spout-usage"></a>Przykładowe użycie elementu Spout

SCP.NET zapewnia metody dodawania EventHubSpout do topologii. Te metody ułatwiają dodawanie elementu Spout niż przy użyciu metod ogólnych do dodawania składnika Java. Poniższy przykład ilustruje sposób tworzenia elementu Spout przy użyciu metod __SetEventHubSpout__ i **EventHubSpoutConfig** dostarczonych przez SCP.NET:

```csharp
 topologyBuilder.SetEventHubSpout(
    "EventHubSpout",
    new EventHubSpoutConfig(
        ConfigurationManager.AppSettings["EventHubSharedAccessKeyName"],
        ConfigurationManager.AppSettings["EventHubSharedAccessKey"],
        ConfigurationManager.AppSettings["EventHubNamespace"],
        ConfigurationManager.AppSettings["EventHubEntityPath"],
        eventHubPartitions),
    eventHubPartitions);
```

Poprzedni przykład tworzy nowy składnik elementu Spout o nazwie __EventHubSpout__ i konfiguruje go do komunikacji z centrum zdarzeń. Warunek równoległości dla składnika jest ustawiony na liczbę partycji w centrum zdarzeń. To ustawienie umożliwia burzę w celu utworzenia wystąpienia składnika dla każdej partycji.

### <a name="example-bolt-usage"></a>Przykładowe użycie pioruna

Użyj metody **JavaComponmentConstructor** , aby utworzyć wystąpienie obiektu. W poniższym przykładzie pokazano, jak utworzyć i skonfigurować nowe wystąpienie **EventHubBolt**:

```csharp
// Java construcvtor for the Event Hub Bolt
JavaComponentConstructor constructor = JavaComponentConstructor.CreateFromClojureExpr(
    String.Format(@"(org.apache.storm.eventhubs.bolt.EventHubBolt. (org.apache.storm.eventhubs.bolt.EventHubBoltConfig. " +
        @"""{0}"" ""{1}"" ""{2}"" ""{3}"" ""{4}"" {5}))",
        ConfigurationManager.AppSettings["EventHubPolicyName"],
        ConfigurationManager.AppSettings["EventHubPolicyKey"],
        ConfigurationManager.AppSettings["EventHubNamespace"],
        "servicebus.windows.net",
        ConfigurationManager.AppSettings["EventHubName"],
        "true"));

// Set the bolt to subscribe to data from the spout
topologyBuilder.SetJavaBolt(
    "eventhubbolt",
    constructor,
    partitionCount)
        .shuffleGrouping("Spout");
```

> [!NOTE]  
> W tym przykładzie użyto wyrażenia Clojure przekazaną jako ciąg, a nie za pomocą **JavaComponentConstructor** do utworzenia **EventHubBoltConfig**, jako przykładu elementu Spout. Każda metoda działa. Użyj metody, która jest najbardziej przydatna.

## <a name="download-the-completed-project"></a>Pobierz ukończony projekt

Możesz pobrać kompletną wersję projektu utworzoną w tym artykule z usługi [GitHub](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub). Jednak nadal trzeba podać ustawienia konfiguracji, wykonując kroki opisane w tym artykule.

### <a name="prerequisites"></a>Wymagania wstępne

* Klaster Apache Storm w usłudze HDInsight. Zobacz [Tworzenie klastrów Apache Hadoop przy użyciu Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md) i wybierz pozycję **burza** dla **typu klastra**.

* [Centrum zdarzeń platformy Azure](../../event-hubs/event-hubs-create.md).

* [Zestaw Azure .NET SDK](https://azure.microsoft.com/downloads/).

* [Narzędzia HDInsight Tools for Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

* Java JDK 1,8 lub nowszy w środowisku deweloperskim. Pliki do pobrania JDK są dostępne w [bazie danych Oracle](/azure/developer/java/fundamentals/java-jdk-long-term-support).

  * Zmienna środowiskowa **JAVA_HOME** musi wskazywać katalog zawierający język Java.
  * Katalog **% JAVA_HOME%/bin.** musi znajdować się w ścieżce.

## <a name="download-the-event-hubs-components"></a>Pobierz składniki Event Hubs

Pobierz Event Hubs i składnik elementu Spout z [https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar](https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar) .

Utwórz katalog o nazwie `eventhubspout` i Zapisz plik w katalogu.

## <a name="configure-event-hubs"></a>Konfigurowanie Event Hubs

Event Hubs jest źródłem danych dla tego przykładu. Skorzystaj z informacji w sekcji "Tworzenie centrum zdarzeń" tematu wprowadzenie [do Event Hubs](../../event-hubs/event-hubs-create.md).

1. Po utworzeniu centrum zdarzeń Sprawdź ustawienia **centrum EventHub** w Azure Portal i wybierz pozycję **zasady dostępu współdzielonego**. Wybierz pozycję **+ Dodaj** , aby utworzyć następujące zasady:

   | Nazwa | Uprawnienia |
   | --- | --- |
   | Usługa |Wysyłanie |
   | czytnika |Nasłuchiwanie |

    :::image type="content" source="./media/apache-storm-develop-csharp-event-hub-topology/share-access-policies.png" alt-text="Zrzut ekranu przedstawiający okno zasady dostępu do udziału" border="true":::

2. Wybierz zasady **czytnika** i **składnika zapisywania** . Skopiuj i Zapisz wartość klucza podstawowego dla obu zasad, ponieważ te wartości są używane później.

## <a name="configure-the-eventhubwriter"></a>Konfigurowanie EventHubWriter

1. Jeśli nie zainstalowano jeszcze najnowszej wersji narzędzi HDInsight Tools for Visual Studio, zobacz [Rozpoczynanie pracy przy użyciu narzędzi HDInsight Tools for Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

2. Pobierz rozwiązanie z [centrum eventhub — burze hybrydowe](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub).

3. Otwórz **EventHubExample. sln**. W projekcie **EventHubWriter** otwórz plik **App.config** . Użyj informacji z centrum zdarzeń, które zostały wcześniej skonfigurowane, aby wypełnić wartość następujących kluczy:

   | Klucz | Wartość |
   | --- | --- |
   | EventHubPolicyName |składnik zapisywania (Jeśli użyto innej nazwy dla zasad z uprawnieniami do *wysyłania* , Użyj zamiast niej). |
   | EventHubPolicyKey |Klucz dla zasad składnika zapisywania. |
   | EventHubNamespace |Przestrzeń nazw, która zawiera centrum zdarzeń. |
   | EventHubName |Nazwa centrum zdarzeń. |
   | EventHubPartitionCount |Liczba partycji w centrum zdarzeń. |

4. Zapisz i zamknij plik **App.config** .

## <a name="configure-the-eventhubreader"></a>Konfigurowanie EventHubReader

1. Otwórz projekt **EventHubReader** .

2. Otwórz plik **App.config** dla **EventHubReader**. Użyj informacji z centrum zdarzeń, które zostały wcześniej skonfigurowane, aby wypełnić wartość następujących kluczy:

   | Klucz | Wartość |
   | --- | --- |
   | EventHubPolicyName |czytnik (Jeśli użyto innej nazwy dla zasad z uprawnieniami *nasłuchiwania* , Użyj zamiast niej). |
   | EventHubPolicyKey |Klucz dla zasad czytnika. |
   | EventHubNamespace |Przestrzeń nazw, która zawiera centrum zdarzeń. |
   | EventHubName |Nazwa centrum zdarzeń. |
   | EventHubPartitionCount |Liczba partycji w centrum zdarzeń. |

3. Zapisz i zamknij plik **App.config** .

## <a name="deploy-the-topologies"></a>Wdrażanie topologii

1. W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy projekt **EventHubReader** , a następnie wybierz pozycję **Prześlij do burzy w usłudze HDInsight**.

    :::image type="content" source="./media/apache-storm-develop-csharp-event-hub-topology/submit-to-apache-storm.png" alt-text="Zrzut ekranu przedstawiający Eksplorator rozwiązań, z opcją Prześlij do burzy w usłudze HDInsight" border="true":::

2. W oknie dialogowym **przesyłanie topologii** wybierz **klaster burzy**. Rozwiń węzeł **dodatkowe konfiguracje**, wybierz pozycję **ścieżki plików w języku Java**, wybierz pozycję **...**, a następnie wybierz katalog zawierający pobrany wcześniej plik JAR. Na koniec kliknij przycisk **Prześlij**.

    :::image type="content" source="./media/apache-storm-develop-csharp-event-hub-topology/submit-storm-topology.png" alt-text="Zrzut ekranu przedstawiający okno dialogowe topologia przesyłania" border="true":::

3. Po przesłaniu topologii pojawia się **Podgląd topologii burzy** . Aby wyświetlić informacje o topologii, wybierz topologię **EventHubReader** w lewym okienku.

    :::image type="content" source="./media/apache-storm-develop-csharp-event-hub-topology/storm-topology-viewer.png" alt-text="Zrzut ekranu przedstawiający przeglądarkę topologii burzy" border="true":::

4. W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy projekt **EventHubWriter** , a następnie wybierz pozycję **Prześlij do burzy w usłudze HDInsight**.

5. W oknie dialogowym **przesyłanie topologii** wybierz **klaster burzy**. Rozwiń węzeł **dodatkowe konfiguracje**, wybierz pozycję **ścieżki plików w języku Java**, wybierz pozycję **...**, a następnie wybierz katalog zawierający pobrany wcześniej plik JAR. Na koniec kliknij przycisk **Prześlij**.

6. Po przesłaniu topologii Odśwież listę topologii w **podglądzie topologii burzy** , aby sprawdzić, czy obie topologie są uruchomione w klastrze.

7. W obszarze **Podgląd topologii burzy** wybierz topologię **EventHubReader** .

8. Aby otworzyć podsumowanie składnika dla pioruna, kliknij dwukrotnie składnik **LogBolt** na diagramie.

9. W sekcji **wykonawcy** wybierz jedno z łączy w kolumnie **port** . Spowoduje to wyświetlenie informacji zarejestrowanych przez składnik. Zarejestrowane informacje są podobne do następujących:

    ```output
    2017-03-02 14:51:29.255 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,255 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1830978598,"deviceId":"8566ccbc-034d-45db-883d-d8a31f34068e"}
    2017-03-02 14:51:29.283 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,283 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1756413275,"deviceId":"647a5eff-823d-482f-a8b4-b95b35ae570b"}
    2017-03-02 14:51:29.313 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,312 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1108478910,"deviceId":"206a68fa-8264-4d61-9100-bfdb68ee8f0a"}
    ```

## <a name="stop-the-topologies"></a>Zatrzymywanie topologii

Aby zatrzymać topologie, zaznacz każdą topologię w **przeglądarce topologii burzy**, a następnie kliknij przycisk **Kill**.

:::image type="content" source="./media/apache-storm-develop-csharp-event-hub-topology/kill-storm-topology1.png" alt-text="Zrzut ekranu przedstawiający przeglądarkę topologii burzy z wyróżnionym przyciskiem Kill" border="true":::

## <a name="delete-your-cluster"></a>Usuwanie klastra

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono sposób używania elementu Spout Java Event Hubs i pioruna z topologii języka C# do pracy z danymi w usłudze Azure Event Hubs. Aby dowiedzieć się więcej na temat tworzenia topologii języka C#, zobacz następujące tematy:

* [Tworzenie topologii języka C# dla Apache Storm w usłudze HDInsight przy użyciu programu Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md)
* [Przewodnik programowania SCP](apache-storm-scp-programming-guide.md)
* [Przykładowe topologie dla systemu Apache Storm w usłudze HDInsight](apache-storm-example-topology.md)