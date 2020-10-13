---
title: Topologie Apache Storm przy użyciu programu Visual Studio i języka C# — Azure HDInsight
description: Dowiedz się, jak tworzyć topologie burzy w języku C#. Utwórz topologię zliczania wyrazów w programie Visual Studio przy użyciu narzędzi Hadoop dla programu Visual Studio.
ROBOTS: NOINDEX
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 12/31/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 271f62625433a6651ba0e3230a62be51e5147f3e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89000196"
---
# <a name="develop-c-topologies-for-apache-storm-by-using-the-data-lake-tools-for-visual-studio"></a>Tworzenie topologii języka C# dla Apache Storm przy użyciu narzędzi Data Lake Tools for Visual Studio

Dowiedz się, jak utworzyć topologię Apache Storm w języku C# przy użyciu narzędzi Azure Data Lake (Apache Hadoop) dla programu Visual Studio. Ten dokument zawiera opis procesu tworzenia projektu burzy w programie Visual Studio, testowania go lokalnie i wdrażania go w Apache Storm w klastrze usługi Azure HDInsight.

Dowiesz się również, jak tworzyć topologie hybrydowe używające składników języka C# i języka Java.

Topologie języka C# korzystają z programu .NET 4,5 i używają języka mono do uruchamiania w klastrze usługi HDInsight. Aby uzyskać informacje o potencjalnych niezgodności, zobacz Zgodność z programem [mono](https://www.mono-project.com/docs/about-mono/compatibility/). Aby użyć topologii języka C#, należy zaktualizować `Microsoft.SCP.Net.SDK` pakiet NuGet używany przez projekt do wersji 0.10.0.6 lub nowszej. Wersja pakietu musi być również zgodna z wersją główną systemu Storm zainstalowanego w usłudze HDInsight.

| Wersja usługi HDInsight | Wersja Apache Storm | Wersja SCP.NET | Domyślna wersja narzędzia mono |
|:-----------------:|:-------------:|:---------------:|:--------------------:|
| 3.4 | 0.10.0. x | 0.10.0. x | 3.2.8 |
| 3,5 | 1.0.2. x | 1.0.0. x | 4.2.1 |
| 3,6 | 1.1.0. x | 1.0.0. x | 4.2.8 |

## <a name="prerequisite"></a>Wymaganie wstępne

Klaster Apache Storm w usłudze HDInsight. Zobacz [Tworzenie klastrów Apache Hadoop przy użyciu Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md) i wybierz pozycję **burza** dla **typu klastra**.

## <a name="install-visual-studio"></a>Instalowanie programu Visual Studio

Korzystając z [programu Visual Studio](https://visualstudio.microsoft.com/downloads/), można opracowywać topologie języka C# z SCP.NET. W poniższych instrukcjach użyto programu Visual Studio 2019, ale można również użyć wcześniejszych wersji programu Visual Studio.

## <a name="install-data-lake-tools-for-visual-studio"></a>Zainstaluj narzędzia Data Lake Tools for Visual Studio

Aby zainstalować narzędzia Data Lake Tools for Visual Studio, wykonaj kroki opisane w temacie [wprowadzenie do narzędzia Data Lake Tools for Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#install-data-lake-tools-for-visual-studio).

## <a name="install-java"></a>Instalowanie języka Java

Po przesłaniu topologii burzy z programu Visual Studio SCP.NET generuje plik zip, który zawiera topologię i zależności. Środowisko Java służy do tworzenia tych plików zip, ponieważ używa formatu, który jest bardziej zgodny z klastrami opartymi na systemie Linux.

1. Zainstaluj zestaw Java developer Kit (JDK) 7 lub nowszy w środowisku deweloperskim. Możesz pobrać oprogramowanie Oracle JDK z firmy [Oracle](https://openjdk.java.net/). Można również użyć [innych dystrybucji języka Java](/java/azure/jdk/).

2. Ustaw `JAVA_HOME` zmienną środowiskową na katalog zawierający język Java.

3. Ustaw `PATH` zmienną środowiskową w celu uwzględnienia `%JAVA_HOME%\bin` katalogu.

Możesz skompilować i uruchomić następującą aplikację konsolową w języku C#, aby sprawdzić, czy środowisko Java i JDK są prawidłowo zainstalowane:

```csharp
using System;
using System.IO;
namespace ConsoleApplication2
{
   class Program
   {
       static void Main(string[] args)
       {
           string javaHome = Environment.GetEnvironmentVariable("JAVA_HOME");
           if (!string.IsNullOrEmpty(javaHome))
           {
               string jarExe = Path.Combine(javaHome + @"\bin", "jar.exe");
               if (File.Exists(jarExe))
               {
                   Console.WriteLine("JAVA Is Installed properly");
                    return;
               }
               else
               {
                   Console.WriteLine("A valid JAVA JDK is not found. Looks like JRE is installed instead of JDK.");
               }
           }
           else
           {
             Console.WriteLine("A valid JAVA JDK is not found. JAVA_HOME environment variable is not set.");
           }
       }  
   }
}
```

## <a name="apache-storm-templates"></a>Szablony Apache Storm

Narzędzia Data Lake Tools for Visual Studio oferują następujące szablony:

| Project type (Typ projektu) | Demonstracje |
| --- | --- |
| Aplikacja burza |Pusty projekt topologii burzowej. |
| Przykład składnika zapisywania SQL platformy Azure |Jak pisać do Azure SQL Database. |
| Przykład czytnika Azure Cosmos DB burzy |Jak czytać z Azure Cosmos DB. |
| Przykład składnika zapisywania Azure Cosmos DB burzy |Jak pisać do Azure Cosmos DB. |
| Przykład czytnika burzy EventHub |Jak czytać z usługi Azure Event Hubs. |
| Przykład składnika zapisywania centrów EventHub |Jak pisać do Event Hubs platformy Azure. |
| Przykład czytnika HBase burzy |Jak czytać z HBase w klastrach usługi HDInsight. |
| Przykład składnika zapisywania HBase burzy |Jak pisać do HBase w klastrach usługi HDInsight. |
| Przykład hybrydowy burzy |Jak używać składnika Java. |
| Przykład burzy |Podstawowa topologia zliczania wyrazów. |

> [!WARNING]  
> Nie wszystkie szablony pracują z usługą HDInsight opartą na systemie Linux. Pakiety NuGet używane przez szablony mogą nie być zgodne z programem mono. Aby zidentyfikować potencjalne problemy, zobacz [zgodność](https://www.mono-project.com/docs/about-mono/compatibility/) z usługą mono i Użyj [analizatora przenośności platformy .NET](../hdinsight-hadoop-migrate-dotnet-to-linux.md#automated-portability-analysis).

W procedurach przedstawionych w tym dokumencie używany jest typ projektu podstawowa aplikacja burzowa do utworzenia topologii.

### <a name="apache-hbase-templates"></a>Szablony HBase Apache

Szablony czytnika i składnika zapisywania HBase używają interfejsu API REST HBase, a nie interfejsu API języka HBase Java do komunikowania się z HBase w klastrze usługi HDInsight.

### <a name="eventhub-templates"></a>Szablony EventHub

> [!IMPORTANT]  
> Oparty na języku Java składnik EventHub elementu Spout zawarty w szablonie czytnika centrów EventHub może nie współpracować z burzą w usłudze HDInsight w wersji 3,5 lub nowszej. Zaktualizowana wersja tego składnika jest dostępna w serwisie [GitHub](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/HDI3.5/lib).

Aby zapoznać się z przykładową topologią, która używa tego składnika i współpracuje z usługą HDInsight 3,5, zobacz witrynę [GitHub](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub).

## <a name="create-a-c-topology"></a>Tworzenie topologii języka C#

Aby utworzyć projekt topologii języka C# w programie Visual Studio:

1. Otwórz program Visual Studio.

1. W oknie **uruchamiania** wybierz pozycję **Utwórz nowy projekt**.

1. W oknie **Tworzenie nowego projektu** przewiń do i wybierz **aplikację burza**, a następnie wybierz pozycję **dalej**.

1. W oknie **Konfigurowanie nowego projektu** wprowadź **nazwę projektu** *WORDCOUNT*, przejdź do lub Utwórz ścieżkę katalogu **lokalizacji** dla projektu, a następnie wybierz pozycję **Utwórz**.

    ![Aplikacja burza, Konfigurowanie nowego projektu, okno dialogowe, Visual Studio](./media/apache-storm-develop-csharp-visual-studio-topology/apache-storm-new-project.png)

Po utworzeniu projektu należy mieć następujące pliki:

* *Program.cs*: definicja topologii dla projektu. Domyślna topologia składająca się z jednego elementu spoutu i jednego pioruna jest tworzona domyślnie.

* *Spout.cs*: przykład elementu Spout, który emituje liczby losowe.

* *Bolt.cs*: przykładowy piorun, który przechowuje liczbę liczb emitowanych przez elementu Spout.

Podczas tworzenia projektu NuGet pobiera najnowszy [pakiet SCP.NET](https://www.nuget.org/packages/Microsoft.SCP.Net.SDK/).

### <a name="implement-the-spout"></a>Implementowanie elementu Spout

Następnie Dodaj kod dla elementu Spout, który jest używany do odczytywania danych w topologii ze źródła zewnętrznego. Ta elementu Spout losowo emituje zdanie do topologii.

1. Otwórz *Spout.cs*. Główne składniki elementu Spout są następujące:

   * `NextTuple`: Wywoływane przez burzę, gdy elementu Spout może emitować nowe krotki.

   * `Ack` (tylko topologia transakcyjna): obsługuje potwierdzenia uruchomione przez inne składniki w topologii dla krotek wysłanych z elementu Spout. Potwierdzenie krotki umożliwia elementu Spout, że została przetworzona pomyślnie przez składniki podrzędne.

   * `Fail` (tylko topologia transakcyjna): obsługuje krotki, które nie przetwarzają innych składników w topologii. Implementacja `Fail` metody umożliwia ponowne wyemitowanie spójnej kolekcji, aby można ją było przetworzyć ponownie.

2. Zastąp zawartość `Spout` klasy następującym tekstem:

    ```csharp
    private Context ctx;
    private Random r = new Random();
    string[] sentences = new string[] {
        "the cow jumped over the moon",
        "an apple a day keeps the doctor away",
        "four score and seven years ago",
        "snow white and the seven dwarfs",
        "i am at two with nature"
    };

    public Spout(Context ctx)
    {
        // Set the instance context
        this.ctx = ctx;

        Context.Logger.Info("Generator constructor called");

        // Declare Output schema
        Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        // The schema for the default output stream is
        // a tuple that contains a string field
        outputSchema.Add("default", new List<Type>() { typeof(string) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(null, outputSchema));
    }

    // Get an instance of the spout
    public static Spout Get(Context ctx, Dictionary<string, Object> parms)
    {
        return new Spout(ctx);
    }

    public void NextTuple(Dictionary<string, Object> parms)
    {
        Context.Logger.Info("NextTuple enter");
        // The sentence to be emitted
        string sentence;

        // Get a random sentence
        sentence = sentences[r.Next(0, sentences.Length - 1)];
        Context.Logger.Info("Emit: {0}", sentence);
        // Emit it
        this.ctx.Emit(new Values(sentence));

        Context.Logger.Info("NextTuple exit");
    }

    public void Ack(long seqId, Dictionary<string, Object> parms)
    {
        // Only used for transactional topologies
    }

    public void Fail(long seqId, Dictionary<string, Object> parms)
    {
        // Only used for transactional topologies
    }
    ```

### <a name="implement-the-bolts"></a>Zaimplementuj pioruny

Teraz Utwórz dwa piorunów burzy w tym przykładzie:

1. Usuń istniejący plik *Bolt.cs* z projektu.

2. W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy projekt, a następnie wybierz pozycję **Dodaj**  >  **nowy element**. Z listy wybierz pozycję **piorun**i wprowadź *Splitter.cs* jako nazwę. W nowym pliku kodu Zmień nazwę przestrzeni nazw na `WordCount` . Następnie powtórz ten proces, aby utworzyć drugi piorun o nazwie *Counter.cs*.

   * *Splitter.cs*: implementuje obiekt, który dzieli zdania na poszczególne słowa i emituje nowy strumień słów.

   * *Counter.cs*: implementuje obiekt piorun, który zlicza każdy wyraz, i emituje nowy strumień słów oraz liczbę dla każdego wyrazu.

     > [!NOTE]  
     > Te pioruny odczytują i zapisują strumienie, ale można również używać pioruna do komunikowania się ze źródłami, takimi jak baza danych lub usługa.

3. Otwórz *Splitter.cs*. Domyślnie ma tylko jedną metodę: `Execute` . `Execute`Metoda jest wywoływana, gdy piorun odbiera krotkę do przetwarzania. Tutaj można odczytywać i przetwarzać kolekcje przychodzące oraz emitować kolekcje wychodzące.

4. Zastąp zawartość `Splitter` klasy następującym kodem:

    ```csharp
    private Context ctx;

    // Constructor
    public Splitter(Context ctx)
    {
        Context.Logger.Info("Splitter constructor called");
        this.ctx = ctx;

        // Declare Input and Output schemas
        Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        // Input contains a tuple with a string field (the sentence)
        inputSchema.Add("default", new List<Type>() { typeof(string) });
        Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        // Outbound contains a tuple with a string field (the word)
        outputSchema.Add("default", new List<Type>() { typeof(string) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
    }

    // Get a new instance of the bolt
    public static Splitter Get(Context ctx, Dictionary<string, Object> parms)
    {
        return new Splitter(ctx);
    }

    // Called when a new tuple is available
    public void Execute(SCPTuple tuple)
    {
        Context.Logger.Info("Execute enter");

        // Get the sentence from the tuple
        string sentence = tuple.GetString(0);
        // Split at space characters
        foreach (string word in sentence.Split(' '))
        {
            Context.Logger.Info("Emit: {0}", word);
            //Emit each word
            this.ctx.Emit(new Values(word));
        }

        Context.Logger.Info("Execute exit");
    }
    ```

5. Otwórz *Counter.cs*i Zastąp zawartość klasy następującym kodem:

    ```csharp
    private Context ctx;

    // Dictionary for holding words and counts
    private Dictionary<string, int> counts = new Dictionary<string, int>();

    // Constructor
    public Counter(Context ctx)
    {
        Context.Logger.Info("Counter constructor called");
        // Set instance context
        this.ctx = ctx;

        // Declare Input and Output schemas
        Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        // A tuple containing a string field - the word
        inputSchema.Add("default", new List<Type>() { typeof(string) });

        Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        // A tuple containing a string and integer field - the word and the word count
        outputSchema.Add("default", new List<Type>() { typeof(string), typeof(int) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
    }

    // Get a new instance
    public static Counter Get(Context ctx, Dictionary<string, Object> parms)
    {
        return new Counter(ctx);
    }

    // Called when a new tuple is available
    public void Execute(SCPTuple tuple)
    {
        Context.Logger.Info("Execute enter");

        // Get the word from the tuple
        string word = tuple.GetString(0);
        // Do we already have an entry for the word in the dictionary?
        // If no, create one with a count of 0
        int count = counts.ContainsKey(word) ? counts[word] : 0;
        // Increment the count
        count++;
        // Update the count in the dictionary
        counts[word] = count;

        Context.Logger.Info("Emit: {0}, count: {1}", word, count);
        // Emit the word and count information
        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new List<SCPTuple> { tuple }, new Values(word, count));
        Context.Logger.Info("Execute exit");
    }
    ```

### <a name="define-the-topology"></a>Definiowanie topologii

Elementy Spout i pioruny są rozmieszczone na grafie, który definiuje sposób przepływu danych między składnikami. W przypadku tej topologii wykres jest następujący:

![Diagramy rozmieszczenia składników elementu Spout i piorunów, topologia burzy](./media/apache-storm-develop-csharp-visual-studio-topology/word-count-topology1.png)

Elementu Spout emituje zdania, które są dystrybuowane do wystąpień pioruna rozdzielacza. Piorun rozdzielacza dzieli zdania na wyrazy, które są dystrybuowane do błyskawicy licznika.

Ponieważ wystąpienie licznika przechowuje wyrazy lokalnie, należy upewnić się, że określone słowa przepływają do tego samego wystąpienia obiektu. Każde wystąpienie śledzi określone słowa. Ponieważ piorun rozdzielacza nie ma stanu, naprawdę nie ma znaczenia, które wystąpienie rozdzielacza otrzymuje zdanie.

Otwórz *program.cs*. Ważna Metoda to `GetTopologyBuilder` , która jest używana do definiowania topologii, która jest przesyłana do burzy. Zastąp zawartość `GetTopologyBuilder` następującym kodem, aby zaimplementować opisaną wcześniej topologię:

```csharp
// Create a new topology named 'WordCount'
TopologyBuilder topologyBuilder = new TopologyBuilder(
    "WordCount" + DateTime.Now.ToString("yyyyMMddHHmmss"));

// Add the spout to the topology.
// Name the component 'sentences'
// Name the field that is emitted as 'sentence'
topologyBuilder.SetSpout(
    "sentences",
    Spout.Get,
    new Dictionary<string, List<string>>()
    {
        {Constants.DEFAULT_STREAM_ID, new List<string>(){"sentence"}}
    },
    1);
// Add the splitter bolt to the topology.
// Name the component 'splitter'
// Name the field that is emitted 'word'
// Use suffleGrouping to distribute incoming tuples
//   from the 'sentences' spout across instances
//   of the splitter
topologyBuilder.SetBolt(
    "splitter",
    Splitter.Get,
    new Dictionary<string, List<string>>()
    {
        {Constants.DEFAULT_STREAM_ID, new List<string>(){"word"}}
    },
    1).shuffleGrouping("sentences");

// Add the counter bolt to the topology.
// Name the component 'counter'
// Name the fields that are emitted 'word' and 'count'
// Use fieldsGrouping to ensure that tuples are routed
//   to counter instances based on the contents of field
//   position 0 (the word). This could also have been
//   List<string>(){"word"}.
//   This ensures that the word 'jumped', for example, will always
//   go to the same instance
topologyBuilder.SetBolt(
    "counter",
    Counter.Get,
    new Dictionary<string, List<string>>()
    {
        {Constants.DEFAULT_STREAM_ID, new List<string>(){"word", "count"}}
    },
    1).fieldsGrouping("splitter", new List<int>() { 0 });

// Add topology config
topologyBuilder.SetTopologyConfig(new Dictionary<string, string>()
{
    {"topology.kryo.register","[\"[B\"]"}
});

return topologyBuilder;
```

## <a name="submit-the-topology"></a>Przesyłanie topologii

Teraz można przystąpić do przesyłania topologii do klastra usługi HDInsight.

1. Przejdź do **widoku**  >  **Eksplorator serwera**.

1. Kliknij prawym przyciskiem myszy pozycję **Azure**, wybierz pozycję **Połącz z subskrypcją Microsoft Azure...** i Ukończ proces logowania.

1. W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy projekt, a następnie wybierz polecenie **Prześlij do burzy w usłudze HDInsight**.

1. W oknie dialogowym **przesyłanie topologii** na liście rozwijanej **klaster burzy** wybierz swoją burzę w klastrze usługi HDInsight, a następnie wybierz pozycję **Prześlij**. Możesz sprawdzić, czy przesyłanie zakończy się pomyślnie, wyświetlając okienko **dane wyjściowe** .

    Po pomyślnym przesłaniu topologii należy **wyświetlić okno widok topologii burzy** dla tego klastra. Wybierz topologię **WORDCOUNT** z listy, aby wyświetlić informacje o uruchomionej topologii.

    ![Okno widoku topologii burzy, klaster usługi HDInsight, Visual Studio](./media/apache-storm-develop-csharp-visual-studio-topology/storm-topology-view.png)

    > [!NOTE]  
    > Można również wyświetlić **topologie burzy** w **Eksplorator serwera**. Rozwiń węzeł **Azure**  >  **HDInsight**, kliknij prawym przyciskiem myszy burzę w klastrze usługi HDInsight, a następnie wybierz polecenie **Wyświetl topologie burzy**.

    Aby wyświetlić informacje o składnikach w topologii, wybierz składnik na diagramie.

1. W sekcji **Podsumowanie topologii** wybierz pozycję **Kill** , aby zatrzymać topologię.

    > [!NOTE]  
    > Topologie burzy są nadal uruchamiane, dopóki nie zostaną dezaktywowane lub klaster zostanie usunięty.

## <a name="transactional-topology"></a>Topologia transakcyjna

Poprzednia topologia nie jest transakcyjna. Składniki w topologii nie implementują funkcji w celu odtwarzania komunikatów. Aby zapoznać się z przykładem topologii transakcyjnej, Utwórz projekt i wybierz pozycję **burza** jako typ projektu.

Topologie transakcyjne implementują następujące czynności w celu obsługi ponownego odtwarzania danych:

* **Buforowanie metadanych**: elementu Spout musi przechowywać metadane dotyczące emitowanych danych, aby dane mogły być pobierane i emitowane ponownie w przypadku wystąpienia błędu. Ponieważ dane emitowane przez próbę są małe, dane pierwotne dla każdej krotki są przechowywane w słowniku do odtworzenia.

* **ACK**: każdy piorun w topologii może wywołać `this.ctx.Ack(tuple)` , aby potwierdzić, że pomyślnie przetworzył krotkę. Gdy wszystkie pioruny potwierdzili spójność, `Ack` Metoda elementu Spout jest wywoływana. `Ack`Metoda zezwala elementu Spout na usuwanie danych, które zostały zbuforowane w celu odtworzenia.

* **Niepowodzenie**: każdy piorun może wywołać `this.ctx.Fail(tuple)` , aby wskazać, że przetwarzanie nie powiodło się dla krotki. Błąd jest propagowany do `Fail` metody elementu Spout, gdzie krotka może zostać powtórzona przy użyciu metadanych w pamięci podręcznej.

* **Identyfikator sekwencji**: w przypadku emitowania spójnej kolekcji można określić unikatowy identyfikator sekwencji. Ta wartość identyfikuje krotkę do przetworzenia powtarzania ( `Ack` i `Fail` ). Na przykład elementu Spout w projekcie **przykładowym burzy** używa następującego wywołania metody podczas emitowania danych:

  `this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(sentence), lastSeqId);`

  Ten kod emituje krotkę zawierającą zdanie do domyślnego strumienia, z wartością identyfikatora sekwencji zawartą w `lastSeqId` . W tym przykładzie `lastSeqId` jest zwiększana dla każdej emisji krotek.

Jak pokazano w projekcie **przykładu burzy** , niezależnie od tego, czy składnik jest transakcyjny można ustawić w czasie wykonywania na podstawie konfiguracji.

## <a name="hybrid-topology-with-c-and-java"></a>Topologia hybrydowa z językiem C# i środowiskiem Java

Możesz również użyć narzędzi Data Lake Tools for Visual Studio, aby utworzyć topologie hybrydowe, w których niektóre składniki są C#, a inne to Java.

Przykład topologii hybrydowej, tworzenie projektu i wybieranie **hybrydowego przykładu burzy**. Ten przykładowy typ ilustruje następujące koncepcje:

* **Elementu Spout Java** i **piorun języka C#**: zdefiniowane w `HybridTopology_javaSpout_csharpBolt` klasie.

  W klasie jest zdefiniowana wersja transakcyjna `HybridTopologyTx_javaSpout_csharpBolt` .

* **Elementu Spout** i **środowisko Java**języka C#: zdefiniowane w `HybridTopology_csharpSpout_javaBolt` klasie.

  W klasie jest zdefiniowana wersja transakcyjna `HybridTopologyTx_csharpSpout_javaBolt` .

  > [!NOTE]  
  > W tej wersji pokazano również, jak używać kodu Clojure z pliku tekstowego jako składnika języka Java.

Aby przełączyć topologię używaną podczas przesyłania projektu, Przenieś `[Active(true)]` instrukcję do topologii, której chcesz użyć, przed przesłaniem jej do klastra.

> [!NOTE]  
> Wszystkie wymagane pliki języka Java są dostępne jako część tego projektu w folderze *JavaDependency* .

Podczas tworzenia i przesyłania topologii hybrydowej należy wziąć pod uwagę następujące kwestie:

* Użyj `JavaComponentConstructor` , aby utworzyć wystąpienie klasy Java dla elementu Spout lub pioruna.

* Służy `microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer` do serializacji danych do składników języka Java z obiektów języka Java do formatu JSON lub z nich.

* Podczas przesyłania topologii do serwera należy użyć opcji **dodatkowe konfiguracje** , aby określić **ścieżki plików Java**. Określona ścieżka powinna być katalogiem zawierającym pliki JAR zawierające klasy języka Java.

### <a name="azure-event-hubs"></a>Azure Event Hubs

W wersji SCP.NET 0.9.4.203 wprowadzono nową klasę i metodę przeznaczone do pracy z centrum zdarzeń elementu Spout (Java elementu Spout, który odczytuje z Event Hubs). Podczas tworzenia topologii korzystającej z centrum zdarzeń elementu Spout (na przykład za pomocą przykładowego szablonu **czytnika burzy EventHub** ) Użyj następujących interfejsów API:

* `EventHubSpoutConfig` Klasa: tworzy obiekt, który zawiera konfigurację dla składnika elementu Spout.

* `TopologyBuilder.SetEventHubSpout` Metoda: dodaje składnik elementu Spout centrum zdarzeń do topologii.

> [!NOTE]  
> Nadal należy używać `CustomizedInteropJSONSerializer` do serializacji danych wytwarzanych przez elementu Spout.

## <a name="use-configurationmanager"></a>Użyj ConfigurationManager

Nie należy używać programu **ConfigurationManager** do pobierania wartości konfiguracyjnych ze składników piorun i elementu Spout. Wykonanie tej operacji może spowodować wystąpienie wyjątku wskaźnika o wartości null. Zamiast tego należy przekazać konfigurację projektu do topologii burzy jako parę klucz-wartość w kontekście topologii. Każdy składnik, który opiera się na wartościach konfiguracji, musi pobrać je z kontekstu podczas inicjalizacji.

Poniższy kod ilustruje sposób pobierania następujących wartości:

```csharp
public class MyComponent : ISCPBolt
{
    // To hold configuration information loaded from context
    Configuration configuration;
    ...
    public MyComponent(Context ctx, Dictionary<string, Object> parms)
    {
        // Save a copy of the context for this component instance
        this.ctx = ctx;
        // If it exists, load the configuration for the component
        if(parms.ContainsKey(Constants.USER_CONFIG))
        {
            this.configuration = parms[Constants.USER_CONFIG] 
                as System.Configuration.Configuration;
        }
        // Retrieve the value of "Foo" from configuration
        var foo = this.configuration.AppSettings.Settings["Foo"].Value;
    }
    ...
}
```

Jeśli używasz `Get` metody do zwrócenia wystąpienia składnika, musisz upewnić się, że przekazuje zarówno `Context` parametr, jak i `Dictionary<string, Object>` do konstruktora. Poniższy przykład jest `Get` metodą podstawową, która prawidłowo przekazuje te wartości:

```csharp
public static MyComponent Get(Context ctx, Dictionary<string, Object> parms)
{
    return new MyComponent(ctx, parms);
}
```

## <a name="how-to-update-scpnet"></a>Jak zaktualizować SCP.NET

Najnowsze wersje pakietu SCP.NET support Upgrades za pomocą narzędzia NuGet. Po udostępnieniu nowej aktualizacji otrzymujesz powiadomienie o uaktualnieniu. Aby ręcznie sprawdzić uaktualnienie, wykonaj następujące kroki:

1. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy projekt, a następnie wybierz pozycję **Zarządzanie pakietami NuGet**.

2. W Menedżerze pakietów wybierz pozycję **aktualizacje**. Jeśli dostępna jest aktualizacja pakietu pomocy technicznej SCP.NET, jest on wyświetlany. Wybierz pozycję **Aktualizuj** dla pakietu, a następnie w oknie dialogowym **Podgląd zmian** wybierz pozycję **OK** , aby ją zainstalować.

> [!IMPORTANT]  
> Jeśli projekt został utworzony przy użyciu wcześniejszej wersji programu SCP.NET, która nie korzystała z narzędzia NuGet, należy wykonać następujące kroki, aby zaktualizować do nowszej wersji:
>
> 1. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy projekt, a następnie wybierz pozycję **Zarządzanie pakietami NuGet**.
> 2. Korzystając z pola **wyszukiwania** , Wyszukaj, a następnie Dodaj `Microsoft.SCP.Net.SDK` do projektu.

## <a name="troubleshoot-common-issues-with-topologies"></a>Rozwiązywanie typowych problemów z topologiami

### <a name="null-pointer-exceptions"></a>Wyjątki wskaźnika o wartości null

W przypadku korzystania z topologii języka C# z klastrem usługi HDInsight opartego na systemie Linux, elementy elementu Spout i Components, które używają elementu **ConfigurationManager** do odczytywania ustawień konfiguracji w czasie wykonywania mogą zwracać wyjątki wskaźnika o wartości null.

Konfiguracja projektu jest przenoszona do topologii burzy jako para klucza i wartości w kontekście topologii. Można go pobrać z obiektu dictionary, który jest przesyłany do składników, gdy są one inicjowane.

Aby uzyskać więcej informacji, zobacz sekcję [use ConfigurationManager](#use-configurationmanager) tego dokumentu.

### <a name="systemtypeloadexception"></a>System. TypeLoadException

W przypadku korzystania z topologii języka C# z klastrem usługi HDInsight opartego na systemie Linux może wystąpić następujący błąd:

`System.TypeLoadException: Failure has occurred while loading a type.`

Ten błąd występuje, gdy używany jest plik binarny, który nie jest zgodny z wersją platformy .NET obsługiwaną przez program mono.

W przypadku klastrów usługi HDInsight opartych na systemie Linux upewnij się, że w projekcie są używane pliki binarne skompilowane dla programu .NET 4,5.

### <a name="test-a-topology-locally"></a>Lokalne testowanie topologii

Chociaż można łatwo wdrożyć topologię w klastrze, w niektórych przypadkach może być konieczne lokalne przetestowanie topologii. Wykonaj następujące kroki, aby uruchomić i przetestować przykładową topologię w tym artykule lokalnie w środowisku deweloperskim.

> [!WARNING]  
> Testowanie lokalne działa tylko dla topologii Basic, tylko w języku C#. Nie można użyć lokalnego testowania dla topologii hybrydowej lub topologii, które używają wielu strumieni.

1. W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy projekt, a następnie wybierz polecenie **Właściwości**. We właściwościach projektu. Następnie zmień **Typ danych wyjściowych** na **aplikację konsolową**.

   ![Aplikacja burzowa usługi HDInsight, właściwości projektu, typ danych wyjściowych](./media/apache-storm-develop-csharp-visual-studio-topology/hdi-output-type-window.png)

   > [!NOTE]
   > Pamiętaj, aby zmienić **Typ danych wyjściowych** z powrotem na **bibliotekę klas** przed wdrożeniem topologii w klastrze.

1. W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy projekt, a następnie wybierz polecenie **Dodaj**  >  **nowy element**. Wybierz **klasę**i wprowadź *LocalTest.cs* jako nazwę klasy. Na koniec wybierz pozycję **Dodaj**.

1. Otwórz *LocalTest.cs*i Dodaj następującą `using` instrukcję w górnej części:

    ```csharp
    using Microsoft.SCP;
    ```

1. Użyj następującego kodu jako zawartości `LocalTest` klasy:

    ```csharp
    // Drives the topology components
    public void RunTestCase()
    {
        // An empty dictionary for use when creating components
        Dictionary<string, Object> emptyDictionary = new Dictionary<string, object>();

        #region Test the spout
        {
            Console.WriteLine("Starting spout");
            // LocalContext is a local-mode context that can be used to initialize
            // components in the development environment.
            LocalContext spoutCtx = LocalContext.Get();
            // Get a new instance of the spout, using the local context
            Spout sentences = Spout.Get(spoutCtx, emptyDictionary);

            // Emit 10 tuples
            for (int i = 0; i < 10; i++)
            {
                sentences.NextTuple(emptyDictionary);
            }
            // Use LocalContext to persist the data stream to file
            spoutCtx.WriteMsgQueueToFile("sentences.txt");
            Console.WriteLine("Spout finished");
        }
        #endregion

        #region Test the splitter bolt
        {
            Console.WriteLine("Starting splitter bolt");
            // LocalContext is a local-mode context that can be used to initialize
            // components in the development environment.
            LocalContext splitterCtx = LocalContext.Get();
            // Get a new instance of the bolt
            Splitter splitter = Splitter.Get(splitterCtx, emptyDictionary);

            // Set the data stream to the data created by the spout
            splitterCtx.ReadFromFileToMsgQueue("sentences.txt");
            // Get a batch of tuples from the stream
            List<SCPTuple> batch = splitterCtx.RecvFromMsgQueue();
            // Process each tuple in the batch
            foreach (SCPTuple tuple in batch)
            {
                splitter.Execute(tuple);
            }
            // Use LocalContext to persist the data stream to file
            splitterCtx.WriteMsgQueueToFile("splitter.txt");
            Console.WriteLine("Splitter bolt finished");
        }
        #endregion

        #region Test the counter bolt
        {
            Console.WriteLine("Starting counter bolt");
            // LocalContext is a local-mode context that can be used to initialize
            // components in the development environment.
            LocalContext counterCtx = LocalContext.Get();
            // Get a new instance of the bolt
            Counter counter = Counter.Get(counterCtx, emptyDictionary);

            // Set the data stream to the data created by splitter bolt
            counterCtx.ReadFromFileToMsgQueue("splitter.txt");
            // Get a batch of tuples from the stream
            List<SCPTuple> batch = counterCtx.RecvFromMsgQueue();
            // Process each tuple in the batch
            foreach (SCPTuple tuple in batch)
            {
                counter.Execute(tuple);
            }
            // Use LocalContext to persist the data stream to file
            counterCtx.WriteMsgQueueToFile("counter.txt");
            Console.WriteLine("Counter bolt finished");
        }
        #endregion
    }
    ```

    Poświęć chwilę na odczytanie komentarzy do kodu. Ten kod używa `LocalContext` do uruchamiania składników w środowisku programistycznym. Zachowuje strumień danych między składnikami do plików tekstowych na dysku lokalnym.

1. Otwórz *program.cs*i Dodaj następujący kod do `Main` metody:

    ```csharp
    Console.WriteLine("Starting tests");
    System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "WordCount-LocalTest");
    // Initialize the runtime
    SCPRuntime.Initialize();

    //If we are not running under the local context, throw an error
    if (Context.pluginType != SCPPluginType.SCP_NET_LOCAL)
    {
        throw new Exception(string.Format("unexpected pluginType: {0}", Context.pluginType));
    }
    // Create test instance
    LocalTest tests = new LocalTest();
    // Run tests
    tests.RunTestCase();
    Console.WriteLine("Tests finished");
    Console.ReadKey();
    ```

1. Zapisz zmiany, a następnie wybierz klawisz **F5** lub wybierz **Debuguj**  >  **Rozpocznij debugowanie** , aby uruchomić projekt. Powinno zostać wyświetlone okno konsoli i stan rejestrowania w miarę postępów testów. Gdy `Tests finished` zostanie wyświetlone, zaznacz dowolny klawisz, aby zamknąć okno.

1. Aby zlokalizować katalog zawierający projekt, użyj **Eksploratora Windows** . (Np.: *C: \\ Użytkownicy \\ \<your_user_name> \\ \\ repozytoriów źródłowych \\ WORDCOUNT \\ WORDCOUNT*). Następnie w tym katalogu Otwórz pozycję *bin*, a następnie wybierz pozycję *Debuguj*. Powinny zostać wyświetlone pliki tekstowe, które zostały utworzone podczas testów: *sentences.txt*, *counter.txt*i *splitter.txt*. Otwórz każdy plik tekstowy i sprawdź dane.

   > [!NOTE]  
   > Dane ciągu są utrwalane jako tablica wartości dziesiętnych w tych plikach. Na przykład `[[97,103,111]]` w pliku **splitter.txt** reprezentuje wyraz *temu*.

> [!NOTE]  
> Upewnij się, że **Typ projektu** jest ustawiany z powrotem do **biblioteki klas** we właściwościach projektu przed wdrożeniem do burzy w klastrze usługi HDInsight.

### <a name="log-information"></a>Informacje dziennika

Korzystając z programu, można łatwo rejestrować informacje ze składników topologii `Context.Logger` . Na przykład następujące polecenie tworzy wpis dziennika informacyjnego:

`Context.Logger.Info("Component started");`

Zarejestrowane informacje można wyświetlać z **dziennika usługi Hadoop**, który znajduje się w **Eksplorator serwera**. Rozwiń pozycję burzy w klastrze usługi HDInsight, a następnie rozwiń węzeł **Dziennik usług Hadoop**. Na koniec wybierz plik dziennika do wyświetlenia.

> [!NOTE]  
> Dzienniki są przechowywane na koncie usługi Azure Storage, które jest używane przez klaster. Aby wyświetlić dzienniki w programie Visual Studio, musisz zalogować się do subskrypcji platformy Azure należącej do konta magazynu.

### <a name="view-error-information"></a>Wyświetl informacje o błędzie

Aby wyświetlić błędy, które wystąpiły w uruchomionej topologii, wykonaj następujące czynności:

1. W **Eksplorator serwera**kliknij prawym przyciskiem myszy burzę w klastrze usługi HDInsight, a następnie wybierz pozycję **Wyświetl topologie burzy**.

   W przypadku **elementu Spout** i **piorunów**kolumna **ostatniej błędu** zawiera informacje dotyczące ostatniego błędu.

2. Wybierz **Identyfikator elementu Spout** lub **Identyfikator** obiektu dla składnika, w którym znajduje się błąd. Na stronie Szczegóły są wyświetlane dodatkowe informacje o błędzie w sekcji **Błędy** w dolnej części strony.

3. Aby uzyskać więcej informacji, wybierz **port** z sekcji **wykonawcy** strony, aby zobaczyć dziennik procesów roboczych burzy w ciągu ostatnich kilku minut.

### <a name="errors-submitting-topologies"></a>Błędy przesyłania topologii

W przypadku wystąpienia błędów przesyłania topologii do usługi HDInsight można znaleźć dzienniki dla składników po stronie serwera, które obsługują przesyłanie topologii w klastrze HDInsight. Aby pobrać te dzienniki, użyj następującego polecenia w wierszu polecenia:

```cmd
scp sshuser@clustername-ssh.azurehdinsight.net:/var/log/hdinsight-scpwebapi/hdinsight-scpwebapi.out .
```

Zastąp *sshuser* przy użyciu konta użytkownika SSH dla klastra. Zastąp wartość *ClusterName* nazwą klastra usługi HDInsight. Aby uzyskać więcej informacji na temat używania usługi `scp` i usługi `ssh` HDInsight, zobacz [Używanie protokołu SSH z usługą HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

Zgłoszenia mogą się nie powieść z wielu powodów:

* JDK nie jest zainstalowana lub nie znajduje się w ścieżce.
* Wymagane zależności języka Java nie są uwzględniane w przesyłaniu.
* Zależności są niezgodne.
* Nazwy topologii są zduplikowane.

Jeśli plik dziennika *HDInsight-scpwebapi. out* zawiera `FileNotFoundException` , wyjątek może być spowodowany przez następujące warunki:

* JDK nie znajduje się w ścieżce w środowisku deweloperskim. Sprawdź, czy program JDK jest zainstalowany w środowisku deweloperskim i czy `%JAVA_HOME%/bin` znajduje się w ścieżce.
* Brak zależności Java. Upewnij się, że w ramach przesłania dołączane są wszystkie wymagane pliki jar.

## <a name="next-steps"></a>Następne kroki

Przykład przetwarzania danych z Event Hubs można znaleźć w temacie [zdarzenia przetwarzania z platformy Azure Event Hubs z burzą w usłudze HDInsight](apache-storm-develop-csharp-event-hub-topology.md).

Aby zapoznać się z przykładem topologii języka C#, która dzieli dane strumienia na wiele strumieni, zobacz [przykład burza w języku c#](https://github.com/Blackmist/csharp-storm-example).

Aby uzyskać więcej informacji na temat tworzenia topologii języka C#, zobacz witrynę [GitHub](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/SCPNet-GettingStarted.md).

Aby uzyskać więcej metod pracy z usługą HDInsight i więcej informacji na temat przykładów usługi HDInsight, zobacz następujące dokumenty:

**SCP.NET firmy Microsoft**

* [Przewodnik programowania SCP dla Apache Storm w usłudze Azure HDInsight](apache-storm-scp-programming-guide.md)

**Apache Storm w usłudze HDInsight**

* [Wdrażanie topologii Apache Storm w usłudze Azure HDInsight i zarządzanie nimi](apache-storm-deploy-monitor-topology-linux.md)
* [Przykładowe topologie Apache Storm w usłudze Azure HDInsight](apache-storm-example-topology.md)

**Apache Hadoop w usłudze HDInsight**

* [Co to jest Apache Hive i HiveQL w usłudze Azure HDInsight?](../hadoop/hdinsight-use-hive.md)
* [Korzystanie z technologii MapReduce na platformie Apache Hadoop w usłudze HDInsight](../hadoop/hdinsight-use-mapreduce.md)

**Usługa Apache HBase w usłudze HDInsight**

* [Korzystanie z bazy danych Apache HBase w usłudze Azure HDInsight](../hbase/apache-hbase-tutorial-get-started-linux.md)
