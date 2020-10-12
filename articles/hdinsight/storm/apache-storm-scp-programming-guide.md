---
title: Przewodnik programowania SCP.NET na potrzeby burzy w usłudze Azure HDInsight
description: Dowiedz się, jak utworzyć program przy użyciu programu SCP.NET. Topologie burzowe oparte na sieci, które są używane z działaniem burzy w usłudze Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-csharp
ms.date: 01/13/2020
ms.openlocfilehash: d54a06c457451fc5323ae37b34b53411cdd6abda
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89000145"
---
# <a name="scp-programming-guide-for-apache-storm-in-azure-hdinsight"></a>Przewodnik programowania SCP dla Apache Storm w usłudze Azure HDInsight

SCP to platforma do tworzenia aplikacji do przetwarzania danych w czasie rzeczywistym, niezawodnej, spójnej i wysokiej wydajności. Jest on oparty na [Apache Storm](https://storm.incubator.apache.org/), który jest systemem przetwarzania strumieniowego przeznaczonym dla społeczności oprogramowania typu open source. Nathana Marzaa. Został on opublikowany jako "open source" w serwisie Twitter. Burza używa [Apache ZooKeeper](https://zookeeper.apache.org/), który jest innym projektem Apache, który umożliwia wysoce niezawodne rozpowszechnianie rozproszonej koordynacji i zarządzania stanami.

Projekt SCP przełączył się nie tylko w systemie Windows, ale również dodaje rozszerzenia i dostosowania dla środowiska systemu Windows. Rozszerzenia obejmują środowisko deweloperskie platformy .NET i biblioteki platformy .NET. Dostosowanie obejmuje wdrożenie oparte na systemie Windows.

Przy użyciu rozszerzeń i dostosowań nie trzeba rozwidlenia projektów oprogramowania open source. Można używać środowisk pochodnych, które są zbudowane na podstawie burzy.

## <a name="processing-model"></a>Model przetwarzania

Dane w usłudze SCP są modelowane jako ciągłe strumienie krotek. Zazwyczaj krotki:

1. Przepływ do kolejki.
1. Są pobierane i przekształcane przez logikę biznesową hostowaną w topologii burzy.
1. Są one przekazywane jako krotki do innego systemu SCP lub są przeznaczone do przechowywania, takich jak rozproszone systemy plików i bazy danych, takie jak SQL Server.

![Diagram przedstawiający dane do przetworzenia w kolejce, które z kolei pobierają strumieniowo magazyn danych](./media/apache-storm-scp-programming-guide/queue-feeding-data-to-processing-to-data-store.png)

W obszarze burza Topologia aplikacji definiuje wykres obliczeniowy. Każdy węzeł w topologii zawiera logikę przetwarzania. Linki między węzłami wskazują przepływ danych.

Węzły, które wprowadzają dane wejściowe do topologii, są nazywane _elementy Spout_. Można ich użyć do sekwencjonowania danych. Dane wejściowe mogą pochodzić ze źródła, takiego jak dzienniki plików, transakcyjna baza danych lub licznik wydajności systemu.

Węzły, które mają przepływy danych wejściowych i wyjściowych, nazywają się _piorunami_. Służą one do rzeczywistego filtrowania, zaznaczania i agregacji danych.

Punkt połączenia usługi obsługuje najlepsze wysiłki, co najmniej raz i dokładnie po przetwarzaniu danych.

W aplikacji rozproszonego przetwarzania strumieniowego mogą wystąpić błędy podczas przetwarzania danych. Takie błędy obejmują awarię sieci, awarię komputera lub błąd w kodzie. Co najmniej jednokrotne przetwarzanie gwarantuje, że wszystkie dane są przetwarzane co najmniej raz przez automatyczne odtwarzanie tych samych danych po wystąpieniu błędu.

Przetwarzanie co najmniej raz jest proste i niezawodne i ma wiele aplikacji. Jednak jeśli aplikacja wymaga dokładnego zliczania, przetwarzanie co najmniej raz jest niewystarczające, ponieważ te same dane mogą być odtwarzane w topologii aplikacji. W takim przypadku dokładnie raz przetwarzanie sprawdza, czy wynik jest poprawny nawet wtedy, gdy dane są odtwarzane i przetwarzane wiele razy.

Usługa SCP umożliwia deweloperom platformy .NET Tworzenie aplikacji do przetwarzania danych w czasie rzeczywistym przy użyciu wirtualna maszyna Java (JVM) z burzą. JVM i .NET komunikują się za pośrednictwem lokalnych gniazd TCP. Każdy elementu Spout/piorun to para procesów .NET/Java, w której logika użytkownika jest uruchamiana w ramach procesu .NET jako wtyczki.

Aby utworzyć aplikację do przetwarzania danych na serwerze SCP, wykonaj następujące kroki:

1. Projektuj i wdrażaj elementy Spout w celu ściągania danych z kolejek.
1. Zaprojektowanie i zaimplementowanie piorunów, które przetwarzają dane wejściowe i zapisuje je w sklepach zewnętrznych, takich jak baza danych.
1. Zaprojektuj topologię, a następnie prześlij ją i Uruchom.

Topologia definiuje wierzchołki i dane, które przechodzą między nimi. Punkt połączenia usługi przyjmuje specyfikację topologii i wdraża ją w klastrze burzy, gdzie każdy wierzchołek jest uruchamiany w jednym węźle logicznym. Harmonogram zadań burzy dba o pracę w trybie failover i skalowanie.

W tym artykule przedstawiono przykładowe przykłady umożliwiające tworzenie aplikacji do przetwarzania danych przy użyciu punktu połączenia usługi.

## <a name="scp-plug-in-interface"></a>Interfejs wtyczki SCP

Wtyczki SCP są aplikacjami autonomicznymi. Mogą one działać w programie Visual Studio podczas tworzenia i być podłączone do potoku burzy po wdrożeniu produkcyjnym.

Pisanie wtyczki SCP jest taka sama jak w przypadku pisania innej aplikacji konsolowej systemu Windows. Platforma SCP.NET deklaruje niektóre interfejsy dla elementu Spout/piorun. Kod wtyczki implementuje te interfejsy. Głównym celem tego projektu jest umożliwienie skoncentrowania się na logice biznesowej, dzięki czemu platforma SCP.NET obsługuje inne rzeczy.

Kod wtyczki implementuje jeden z następujących interfejsów. Który interfejs zależy od tego, czy topologia jest transakcyjna, czy nietransakcyjna oraz czy składnik jest elementu Spout czy piorunem.

* **ISCPSpout**
* **ISCPBolt**
* **ISCPTxSpout**
* **ISCPBatchBolt**

### <a name="iscpplugin"></a>ISCPPlugin

**ISCPPlugin** jest wspólnym interfejsem dla wielu wtyczek. Obecnie jest to fikcyjny interfejs.

```csharp
public interface ISCPPlugin
{
}
```

### <a name="iscpspout"></a>ISCPSpout

**ISCPSpout** jest interfejsem dla nietransakcyjnych elementu Spout.

```csharp
public interface ISCPSpout : ISCPPlugin
{
    void NextTuple(Dictionary<string, Object> parms);
    void Ack(long seqId, Dictionary<string, Object> parms); 
    void Fail(long seqId, Dictionary<string, Object> parms);
}
```

Gdy **NextTuple** jest wywoływana, kod języka C# może emitować jedną lub kilka krotek. Jeśli nie ma niczego do emisji, ta metoda powinna zwracać bez emitowania żadnych elementów.

Metody **NextTuple**, **ACK**i **FAIL** są wywoływane w ścisłej pętli w pojedynczym wątku procesu języka C#. Gdy nie ma krotek do emisji, **NextTuple** Uśpij przez krótki czas, taki jak 10 milisekund. To uśpienie pomaga uniknąć marnowania dostępności procesora CPU.

Metody **ACK** i **FAIL** są wywoływane tylko wtedy, gdy plik specyfikacji włącza mechanizm potwierdzania. Parametr *identyfikatora SeqID* identyfikuje krotkę, która została potwierdzona lub zakończyła się niepowodzeniem. Jeśli potwierdzenie jest włączone w topologii nietransakcyjnej, należy użyć następującej funkcji **emisji** w elementu Spout:

```csharp
public abstract void Emit(string streamId, List<object> values, long seqId);
```

Jeśli topologia nietransakcyjna nie obsługuje potwierdzenia, **ACK** i **Niepowodzenie** można pozostawić jako puste funkcje.

Parametr wejściowy *Parametry* w tych funkcjach Określa pusty słownik i jest zarezerwowany do użytku w przyszłości.

### <a name="iscpbolt"></a>ISCPBolt

**ISCPBolt** jest interfejsem dla pioruna nietransakcyjnego.

```csharp
public interface ISCPBolt : ISCPPlugin
{
void Execute(SCPTuple tuple);
}
```

Po udostępnieniu nowej spójnej kolekcji funkcja **Execute** jest wywoływana, aby ją przetworzyć.

### <a name="iscptxspout"></a>ISCPTxSpout

**ISCPTxSpout** jest interfejsem dla transakcyjnego elementu spoutu.

```csharp
public interface ISCPTxSpout : ISCPPlugin
{
    void NextTx(out long seqId, Dictionary<string, Object> parms);  
    void Ack(long seqId, Dictionary<string, Object> parms);         
    void Fail(long seqId, Dictionary<string, Object> parms);        
}
```

Podobnie jak w przypadku nietransakcyjnych odpowiedników, **NextTx**, **ACK**i **Niepowodzenie** są wywoływane w ścisłej pętli w pojedynczym wątku procesu języka C#. Gdy nie ma krotek do emisji, **NextTx** Uśpij przez krótki czas, taki jak 10 milisekund. To uśpienie pomaga uniknąć marnowania dostępności procesora CPU.

Gdy **NextTx** jest wywoływana w celu rozpoczęcia nowej transakcji, parametr wyjściowy *identyfikatora SeqID* identyfikuje transakcję. Transakcja jest również używana w **ACK** i **Niepowodzenie**. Metoda **NextTx** może emitować dane po stronie Java. Dane są przechowywane w dozorcy, aby umożliwić odtwarzanie. Ponieważ dozorcy ma ograniczoną pojemność, kod powinien emitować tylko metadane, a nie dane zbiorcze w transakcyjnym elementu Spout.

Ponieważ burza automatycznie odtwarza nieudaną transakcję, **błąd** zwykle nie zostanie wywołany. Ale jeśli punkt połączenia usługi może sprawdzić metadane emitowane przez elementu Spout transakcyjny, może wystąpić **błąd** , gdy metadane są nieprawidłowe.

Parametr wejściowy *Parametry* w tych funkcjach Określa pusty słownik i jest zarezerwowany do użytku w przyszłości.

### <a name="iscpbatchbolt"></a>ISCPBatchBolt

**ISCPBatchBolt** jest interfejsem dla błyskawicy transakcyjnej.

```csharp
public interface ISCPBatchBolt : ISCPPlugin
{
    void Execute(SCPTuple tuple);
    void FinishBatch(Dictionary<string, Object> parms);  
}
```

Metoda **Execute** jest wywoływana, gdy nowa krotka dociera do błyskawicy. Metoda **FinishBatch** jest wywoływana, gdy ta transakcja zostanie zakończona. Parametr wejściowy *Parametry* jest zarezerwowany do użytku w przyszłości.

W przypadku topologii transakcyjnej **StormTxAttempt** jest ważną klasą. Ma dwa elementy członkowskie: **TxId** i **AttemptId**. Element członkowski **TxId** identyfikuje konkretną transakcję. Transakcja może być podejmowana wiele razy, jeśli nie powiedzie się i zostanie odtworzony.

SCP.NET tworzy nowy obiekt **ISCPBatchBolt** , aby przetwarzać każdy obiekt **StormTxAttempt** , podobnie jak w przypadku środowiska Java. Ten projekt ma na celu obsługę równoległego przetwarzania transakcji. Po zakończeniu próby transakcji odpowiedni obiekt **ISCPBatchBolt** zostaje zniszczony i wyrzucony.

## <a name="object-model"></a>Model obiektów

Program SCP.NET udostępnia również prosty zestaw obiektów kluczowych dla deweloperów, którzy mają być używani. Obiekty to **Context**, **stan klientów**i **SCPRuntime**. Są one omówione w tej sekcji.

### <a name="context"></a>Kontekst

Obiekt **kontekstu** zapewnia uruchomione środowisko dla aplikacji. Każde wystąpienie **ISCPPlugin** **ISCPSpout**, **ISCPBolt**, **ISCPTxSpout**lub **ISCPBatchBolt** ma odpowiednie wystąpienie **kontekstu** . Funkcje udostępnione przez **kontekst** są podzielone na te dwie części:

* Część statyczna, która jest dostępna w całym procesie języka C#
* Część dynamiczna, która jest dostępna tylko dla określonego wystąpienia **kontekstu**

### <a name="static-part"></a>Część statyczna

```csharp
public static ILogger Logger = null;
public static SCPPluginType pluginType;
public static Config Config { get; set; }
public static TopologyContext TopologyContext { get; set; }  
```

Obiekt **rejestratora** jest dostępny do celów rejestrowania.

Obiekt **plugintype** wskazuje typ wtyczki procesu języka C#. Jeśli proces jest uruchamiany w trybie testowania lokalnego bez języka Java, typ wtyczki jest **SCP_NET_LOCAL**.

```csharp
public enum SCPPluginType 
{
    SCP_NET_LOCAL = 0,
    SCP_NET_SPOUT = 1,
    SCP_NET_BOLT = 2,
    SCP_NET_TX_SPOUT = 3,
    SCP_NET_BATCH_BOLT = 4  
    }
```

Właściwość **config** pobiera parametry konfiguracji ze strony Java, która przekazuje je po zainicjowaniu wtyczki języka C#. Parametry **konfiguracji** są podzielone na dwie części: **stormConf** i **pluginConf**.

```csharp
public Dictionary<string, Object> stormConf { get; set; }  
public Dictionary<string, Object> pluginConf { get; set; }  
```

Częścią **stormConf** są parametry zdefiniowane przez burzę, a część **pluginConf** jest parametrami ZDEFINIOWANYmi przez punkt połączenia usługi. Oto przykład:

```csharp
public class Constants
{
    … …

    // constant string for pluginConf
    public static readonly String NONTRANSACTIONAL_ENABLE_ACK = "nontransactional.ack.enabled";  

    // constant string for stormConf
    public static readonly String STORM_ZOOKEEPER_SERVERS = "storm.zookeeper.servers";
    public static readonly String STORM_ZOOKEEPER_PORT = "storm.zookeeper.port";
}
```

Typ **TopologyContext** Pobiera kontekst topologii. Jest to najbardziej przydatne w przypadku wielu składników równoległych. Oto przykład:

```csharp
//demo how to get TopologyContext info
if (Context.pluginType != SCPPluginType.SCP_NET_LOCAL)
{
    Context.Logger.Info("TopologyContext info:");
    TopologyContext topologyContext = Context.TopologyContext;
    Context.Logger.Info("taskId: {0}", topologyContext.GetThisTaskId());
    taskIndex = topologyContext.GetThisTaskIndex();
    Context.Logger.Info("taskIndex: {0}", taskIndex);
    string componentId = topologyContext.GetThisComponentId();
    Context.Logger.Info("componentId: {0}", componentId);
    List<int> componentTasks = topologyContext.GetComponentTasks(componentId);  
    Context.Logger.Info("taskNum: {0}", componentTasks.Count);
}
```

### <a name="dynamic-part"></a>Część dynamiczna

Następujące interfejsy są powiązane z określonym wystąpieniem **kontekstu** utworzonym przez platformę SCP.NET i przekazaną do kodu:

```csharp
// Declare the Output and Input Stream Schemas

public void DeclareComponentSchema(ComponentStreamSchema schema);

// Emit tuple to default stream.
public abstract void Emit(List<object> values);

// Emit tuple to the specific stream.
public abstract void Emit(string streamId, List<object> values);  
```

W przypadku nietransakcyjnych elementu Spout, które obsługują potwierdzenie, zapewniona jest następująca metoda:

```csharp
// for nontransactional spout that supports ack
public abstract void Emit(string streamId, List<object> values, long seqId);  
```

Obiekt nietransakcyjny, który obsługuje potwierdzenie, powinien jawnie wywołać metodę **ACK** lub **zakończyć się niepowodzeniem** przy użyciu spójnej kolekcji. Podczas emitowania nowej spójnej kolekcji, piorun musi również określać kotwice krotki. Dostępne są następujące metody:

```csharp
public abstract void Emit(string streamId, IEnumerable<SCPTuple> anchors, List<object> values);
public abstract void Ack(SCPTuple tuple);
public abstract void Fail(SCPTuple tuple);
```

### <a name="statestore"></a>Stan klientów

Obiekt **stan klientów** zapewnia usługi metadanych, generowanie sekwencji monotoniczny i niezależną koordynację. Można tworzyć rozproszone abstrakcje współbieżności na poziomie **stan klientów**. Te streszczenia obejmują blokady rozproszone, kolejki rozproszone, bariery i usługi transakcyjne.

Aplikacje SCP mogą używać obiektu **State** do serializacji informacji w [Apache ZooKeeper](https://zookeeper.apache.org/). Ta możliwość jest szczególnie przydatna w przypadku topologii transakcyjnej. Jeśli transakcyjna elementu Spout przestaje odpowiadać i ponownego uruchomienia, **stan** może pobrać niezbędne informacje z dozorcy i ponownie uruchomić potok.

Obiekt **stan klientów** ma następujące metody podstawowe:

```csharp
/// <summary>
/// Static method to retrieve a state store of the given path and connStr 
/// </summary>
/// <param name="storePath">StateStore path</param>
/// <param name="connStr">StateStore address</param>
/// <returns>Instance of StateStore</returns>
public static StateStore Get(string storePath, string connStr);

/// <summary>
/// Create a new state object in this state store instance
/// </summary>
/// <returns>State from StateStore</returns>
public State Create();

/// <summary>
/// Retrieve all states that were previously uncommitted, excluding all exited states
/// </summary>
/// <returns>Uncommitted states</returns>
public IEnumerable<State> GetUnCommitted();

/// <summary>
/// Get all the states in the StateStore
/// </summary>
/// <returns>All the states</returns>
public IEnumerable<State> States();

/// <summary>
/// Get state or registry object
/// </summary>
/// <param name="info">Registry name (registry only)</param>
/// <typeparam name="T">Type, registry or state</typeparam>
/// <returns>Return registry or state</returns>
public T Get<T>(string info = null);

/// <summary>
/// List all the committed states
/// </summary>
/// <returns>Registries containing the committed state </returns>
public IEnumerable<Registry> Committed();

/// <summary>
/// List all the exited states in the StateStore
/// </summary>
/// <returns>Registries containing the exited states</returns>
public IEnumerable<Registry> Aborted();

/// <summary>
/// Retrieve an existing state object from this state store instance 
/// </summary>
/// <returns>State from StateStore</returns>
/// <typeparam name="T">stateId, id of the State</typeparam>
public State GetState(long stateId)
```

Obiekt **State** ma następujące metody podstawowe:

```csharp
/// <summary>
/// Set the status of the state object to commit
/// </summary>
public void Commit(bool simpleMode = true);

/// <summary>
/// Set the status of the state object to exit
/// </summary>
public void Abort();

/// <summary>
/// Put an attribute value under the given key
/// </summary>
/// <param name="key">Key</param>
/// <param name="attribute">State attribute</param>
    public void PutAttribute<T>(string key, T attribute);

/// <summary>
/// Get the attribute value associated with the given key
/// </summary>
/// <param name="key">Key</param>
/// <returns>State attribute</returns>
    public T GetAttribute<T>(string key);
```

Gdy **simplemode** ma wartość **true**, Metoda **commit** usuwa odpowiednie ZNode w dozorcy. W przeciwnym razie metoda usuwa bieżącą ZNode i dodaje nowy węzeł w ścieżce ZATWIERDZONEj \_ .

### <a name="scpruntime"></a>SCPRuntime

Klasa **SCPRuntime** udostępnia dwie następujące metody:

```csharp
public static void Initialize();

public static void LaunchPlugin(newSCPPlugin createDelegate);  
```

Metoda **Initialize** inicjuje środowisko URUCHOMIENIOWE punktu połączenia usługi. W tej metodzie proces C# nawiązuje połączenie ze stroną Java w celu uzyskania parametrów konfiguracji i kontekstu topologii.

Metoda **LaunchPlugin** uruchamia pętlę przetwarzania komunikatów. W tej pętli wtyczka C# odbiera komunikaty ze strony języka Java. Te komunikaty obejmują krotki i sygnały sterujące. Wtyczka następnie przetwarza komunikaty, na przykład przez wywołanie metody interfejsu dostarczonej przez kod.

Parametr wejściowy **LaunchPlugin** jest delegatem. Metoda może zwrócić obiekt, który implementuje interfejs **ISCPSpout**, **ISCPBolt**, **ISCPTxSpout**lub **ISCPBatchBolt** .

```csharp
public delegate ISCPPlugin newSCPPlugin(Context ctx, Dictionary<string, Object> parms);
```

W przypadku **ISCPBatchBolt**można uzyskać obiekt **StormTxAttempt** z parametru *Parametry* i użyć go do oceny, czy próba jest powtórzoną próbą. Sprawdzanie dla próby powtórzenia jest często wykonywane przy użyciu pioruna. W tym artykule przedstawiono przykład HelloWorldTx w dalszej części tego artykułu.

Wtyczki SCP można zwykle uruchomić w dwóch trybach: tryb testowania lokalnego i tryb regularny.

#### <a name="local-test-mode"></a>Tryb testu lokalnego

W tym trybie wtyczki usługi SCP w kodzie C# są uruchamiane w programie Visual Studio podczas fazy tworzenia. W tym trybie można użyć interfejsu **ILocalContext** . Interfejs zapewnia metody serializacji emitowanych krotek do plików lokalnych i odczytywanie ich z powrotem do pamięci RAM.

```csharp
public interface ILocalContext
{
    List<SCPTuple> RecvFromMsgQueue();
    void WriteMsgQueueToFile(string filepath, bool append = false);  
    void ReadFromFileToMsgQueue(string filepath);
}
```

#### <a name="regular-mode"></a>Tryb regularny

W tym trybie proces burzy języka Java uruchamia wtyczki usługi SCP. Oto przykład:

```csharp
namespace Scp.App.HelloWorld
{
public class Generator : ISCPSpout
{
    … …
    public static Generator Get(Context ctx, Dictionary<string, Object> parms)
    {
    return new Generator(ctx);
    }
}

class HelloWorld
{
    static void Main(string[] args)
    {
    /* Setting the environment variable here can change the log file name */
    System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "HelloWorld");

    SCPRuntime.Initialize();
    SCPRuntime.LaunchPlugin(new newSCPPlugin(Generator.Get));
    }
}
}
```

## <a name="topology-specification-language"></a>Język specyfikacji topologii

Specyfikacja topologii punktu połączenia usługi to język specyficzny dla domeny (DSL) służący do opisywania i konfigurowania topologii punktu połączenia. Jest on oparty na [CLOJURE DSL](https://storm.incubator.apache.org/documentation/Clojure-DSL.html) i jest przedłużony przez punkt połączenia usługi.

Specyfikacje topologii można przesłać bezpośrednio do klastra burzy do wykonania za pomocą polecenia **runSpec** .

SCP.NET dodał następujące funkcje, aby zdefiniować topologie transakcyjne:

| Nowa funkcja | Parametry | Opis |
| --- | --- | --- |
| **TX-topolopy** |*Topologia — nazwa*<br />*elementu Spout — Mapa*<br />*Mapa błyskawicy* |Definiuje topologię transakcyjną z nazwą topologii, mapą definicji elementy Spout i obiektem mapy definicji. |
| **SCP-TX-elementu Spout** |*exec-Name*<br />*argumentów*<br />*pola* |Definiuje transakcyjny elementu Spout. Funkcja uruchamia aplikację, która jest określona przez *exec-Name* i używa *argumentów*.<br /><br />*Pola* parametr określa pola danych wyjściowych dla elementu Spout. |
| **SCP-TX-Batch** |*exec-Name*<br />*argumentów*<br />*pola* |Definiuje transakcyjny obiekt do przetwarzania wsadowego. Funkcja uruchamia aplikację, która jest określona przez *exec-Name* i używa *argumentów.*<br /><br />*Pola* parametr określa pola danych wyjściowych dla pioruna. |
| **SCP-TX-commit-Piorun** |*exec-Name*<br />*argumentów*<br />*pola* |Definiuje obiekt do zatwierdzeń transakcyjnych. Funkcja uruchamia aplikację, która jest określona przez *exec-Name* i używa *argumentów*.<br /><br />*Pola* parametr określa pola danych wyjściowych dla pioruna. |
| **nontx — topologia** |*Topologia — nazwa*<br />*elementu Spout — Mapa*<br />*Mapa błyskawicy* |Definiuje topologię nietransakcyjną z nazwą topologii, mapą definicji elementy Spout i obiektem mapy definicji. |
| **SCP — elementu Spout** |*exec-Name*<br />*argumentów*<br />*pola*<br />*wejściowe* |Definiuje nietransakcyjny elementu Spout. Funkcja uruchamia aplikację, która jest określona przez *exec-Name* i używa *argumentów*.<br /><br />*Pola* parametr określa pola danych wyjściowych dla elementu Spout.<br /><br />Parametr *Parameters* jest opcjonalny. Służy do określania parametrów, takich jak "nietransakcyjny. ACK. Enabled". |
| **punkt połączenia usługi** |*exec-Name*<br />*argumentów*<br />*pola*<br />*wejściowe* |Definiuje piorun nietransakcyjny. Funkcja uruchamia aplikację, która jest określona przez *exec-Name* i używa *argumentów*.<br /><br />Pole *parametr określa* pola wyjściowe dla pioruna<br /><br />Parametr *Parameters* jest opcjonalny. Służy do określania parametrów, takich jak "nietransakcyjny. ACK. Enabled". |

SCP.NET definiuje następujące słowa kluczowe:

| Słowo kluczowe | Opis |
| --- | --- |
| **: Nazwa** |Nazwa topologii |
| **: topologia** |Topologia korzystająca z funkcji w poprzedniej tabeli i wbudowanych funkcjach |
| **:p** |Warunek równoległości dla każdego elementu spoutu lub pioruna |
| **: config** |Czy należy skonfigurować parametry, czy zaktualizować istniejące |
| **: schemat** |Schemat strumienia |

SCP.NET definiuje również te często używane parametry:

| Parametr | Opis |
| --- | --- |
| "plugin.name" |Nazwa pliku. exe wtyczki języka C# |
| "wtyczka. args" |Argumenty wtyczki |
| "Output. Schema" |Schemat danych wyjściowych |
| "nietransakcyjny. ACK. Enabled" |Czy jest włączone potwierdzenie dla topologii nietransakcyjnej |

Polecenie **runSpec** jest wdrażane razem z usługą BITS. Oto użycie polecenia:

```csharp
.\bin\runSpec.cmd
usage: runSpec [spec-file target-dir [resource-dir] [-cp classpath]]
ex: runSpec examples\HelloWorld\HelloWorld.spec specs examples\HelloWorld\Target
```

Parametr *Resource-dir* jest opcjonalny. Określ ją, gdy chcesz podłączyć aplikację w języku C#. Określony katalog zawiera aplikację, zależności i konfiguracje.

Parametr *ścieżki klas* jest również opcjonalny. Określa ścieżkę klasy Java, jeśli plik specyfikacji zawiera elementu Spout lub piorun języka Java.

## <a name="miscellaneous-features"></a>Różne funkcje

### <a name="input-and-output-schema-declarations"></a>Deklaracje schematu danych wejściowych i wyjściowych

Procesy języka C# mogą emitować krotki. W tym celu platforma serializować kolekcje do obiektów **Byte []** i przesyła obiekty do strony Java. Następnie burzy przenosi te krotki do obiektów docelowych.

W składnikach podrzędnych procesy C# przetwarzają krotki z powrotem po stronie Java i konwertują je na oryginalne typy platformy. Wszystkie te operacje są ukrywane przez platformę.

Aby zapewnić obsługę serializacji i deserializacji, kod musi deklarować schemat danych wejściowych i wyjściowych. Schemat jest zdefiniowany jako słownik. IDENTYFIKATORem strumienia jest klucz słownika. Wartość klucza jest typami kolumn. Składnik może deklarować wiele strumieni.

```csharp
public class ComponentStreamSchema
{
    public Dictionary<string, List<Type>> InputStreamSchema { get; set; }
    public Dictionary<string, List<Type>> OutputStreamSchema { get; set; }
    public ComponentStreamSchema(Dictionary<string, List<Type>> input, Dictionary<string, List<Type>> output)
    {
        InputStreamSchema = input;
        OutputStreamSchema = output;
    }
}
```

Następująca funkcja jest dodawana do obiektu **kontekstu** :

```csharp
public void DeclareComponentSchema(ComponentStreamSchema schema)
```

Deweloperzy muszą upewnić się, że emitowane krotki przestrzegają schematu zdefiniowanego dla strumienia. W przeciwnym razie system zgłosi wyjątek czasu wykonywania.

### <a name="multistream-support"></a>Obsługa WIELOSTRUMIENIOWA

Punkt połączenia usługi umożliwia emitowanie lub odbieranie z wielu odrębnych strumieni w tym samym czasie. Obiekt **kontekstu** odzwierciedla tę obsługę jako opcjonalny parametr identyfikatora strumienia metody **emisji** .

Dodano dwie metody z obiektu **kontekstu** SCP.NET. Emitują co najmniej jedną krotkę do określonych strumieni. Parametr *obecny streamid* jest ciągiem. Jego wartość musi być taka sama w kodzie C# i specyfikacjach definicji topologii.

```csharp
/* Emit tuple to the specific stream. */
public abstract void Emit(string streamId, List<object> values);

/* for nontransactional spout only */
public abstract void Emit(string streamId, List<object> values, long seqId);
```

Emitowanie do nieistniejącego strumienia powoduje wyjątki środowiska uruchomieniowego.

### <a name="fields-grouping"></a>Grupowanie pól

Wbudowane grupowanie pól w obszarze burza nie działa prawidłowo w SCP.NET. Po stronie serwera proxy Java typ danych wszystkich pól jest w rzeczywistości **bajt []**. Grupowanie pól używa kodu skrótu obiektu **Byte []** do wykonania grupowania. Kod skrótu jest adresem tego obiektu w pamięci RAM. Dlatego grupowanie będzie niewłaściwe dla obiektów wielobajtowych, które współużytkują tę samą zawartość, ale nie tego samego adresu.

SCP.NET dodaje dostosowaną metodę grupowania i używa zawartości obiektu **Byte []** do grupowania. W pliku specyfikacji składnia wygląda podobnie do tego przykładu:

```csharp
(bolt-spec
    {
        "spout_test" (scp-field-group :non-tx [0,1])
    }
    …
)
```

W poprzednim pliku specyfikacji:

* `scp-field-group` Określa, że grupowanie jest dostosowanym grupowaniem pól implementowanym przez punkt połączenia usługi.
* `:tx` lub `:non-tx` określa, czy topologia jest transakcyjna. Te informacje są potrzebne, ponieważ indeks początkowy różni się od topologii transakcyjnej i nietransakcyjnej.
* `[0,1]` Określa zestaw skrótów identyfikatorów pól, które zaczynają się od zera.

### <a name="hybrid-topology"></a>Topologia hybrydowa

Natywny kod burzy jest pisany w języku Java. SCP.NET ma ulepszoną burzę, aby umożliwić pisanie kodu w języku C# w celu obsługi logiki biznesowej. Ale SCP.NET obsługuje również topologie hybrydowe, które zawierają nie tylko elementy Spout w języku C#, ale również elementy Spout/pioruny języka Java.

### <a name="specify-java-spoutbolt-in-a-specification-file"></a>Określanie języka Java elementu Spout/piorun w pliku specyfikacji

Aby określić elementy Spout i pioruny języka Java, można użyć **punktu SCP-elementu Spout** i **punktu połączenia** usługi w pliku specyfikacji. Oto przykład:

```csharp
(spout-spec 
  (microsoft.scp.example.HybridTopology.Generator.)
  :p 1)
```

Oto `microsoft.scp.example.HybridTopology.Generator` Nazwa klasy elementu Spout języka Java.

### <a name="specify-the-java-classpath-in-a-runspec-command"></a>Określanie ścieżki klas Java w runSpec polecenia

Aby przesłać topologię zawierającą elementy Spout lub piorunów języka Java, należy najpierw skompilować je w celu utworzenia plików JAR. Następnie określ ścieżkę klasy Java, która zawiera pliki JAR podczas przesyłania topologii. Oto przykład:

```csharp
bin\runSpec.cmd examples\HybridTopology\HybridTopology.spec specs examples\HybridTopology\net\Target -cp examples\HybridTopology\java\target\*
```

W tym miejscu `examples\HybridTopology\java\target\` jest folder zawierający plik JAR elementu Spout/piorun języka Java.

### <a name="serialization-and-deserialization-between-java-and-c"></a>Serializacja i deserializacja między językami Java i C #

Składnik punktu połączenia usługi zawiera stronę Java i stronę języka C#. Aby można było korzystać z natywnych elementy spoutów języka Java, serializacja i deserializacja musi następować między stroną Java a stroną języka C#, jak pokazano na poniższym wykresie:

![Diagram składnika Java służący do wysyłania do składnika SCP, który następnie wysyła do innego składnika Java](./media/apache-storm-scp-programming-guide/java-compent-sending-to-scp-component-sending-to-java-component.png)

#### <a name="serialization-in-the-java-side-and-deserialization-in-the-c-side"></a>Serializacja po stronie Java i deserializacji w stronie C#

Najpierw Podaj domyślną implementację serializacji po stronie Java i deserializacji w stronie C#.

Określ metodę serializacji strony Java w pliku specyfikacji.

```csharp
(scp-bolt
    {
        "plugin.name" "HybridTopology.exe"
        "plugin.args" ["displayer"]
        "output.schema" {}
        "customized.java.serializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer"]
    })
```

Określ metodę deserializacji strony C# w kodzie C#.

```csharp
Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
inputSchema.Add("default", new List<Type>() { typeof(Person) });
this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));
this.ctx.DeclareCustomizedDeserializer(new CustomizedInteropJSONDeserializer());
```  

Jeśli typ danych nie jest zbyt skomplikowany, ta domyślna implementacja powinna obsługiwać większości przypadków. Poniżej przedstawiono przypadki, w których można podłączyć własną implementację:

* Typ danych jest zbyt złożony dla implementacji domyślnej.
* Wydajność domyślnej implementacji nie spełnia wymagań użytkownika.

Interfejs serializacji w stronie Java jest zdefiniowany jako:

```csharp
public interface ICustomizedInteropJavaSerializer {
    public void prepare(String[] args);
    public List<ByteBuffer> serialize(List<Object> objectList);
}
```

Interfejs deserializacji w stronie C# jest zdefiniowany jako:

```csharp
public interface ICustomizedInteropCSharpDeserializer
{
    List<Object> Deserialize(List<byte[]> dataList, List<Type> targetTypes);
}
```

#### <a name="serialization-in-the-c-side-and-deserialization-in-the-java-side"></a>Serializacja po stronie C# i deserializacji na stronie Java

Określ metodę serializacji strony C# w kodzie C#.

```csharp
this.ctx.DeclareCustomizedSerializer(new CustomizedInteropJSONSerializer()); 
```

Określ metodę deserializacji strony Java w pliku specyfikacji.

```csharp
(scp-spout
   {
     "plugin.name" "HybridTopology.exe"
     "plugin.args" ["generator"]
     "output.schema" {"default" ["person"]}
     "customized.java.deserializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer" "microsoft.scp.example.HybridTopology.Person"]
   }
)
```

W tym miejscu `"microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer"` jest nazwą deserializatora i `"microsoft.scp.example.HybridTopology.Person"` jest klasą docelową, do której są deserializowane dane.

Możesz również podłączyć własną implementację serializatora C# i deserializacji języka Java.

Ten kod jest interfejsem dla serializatora C#:

```csharp
public interface ICustomizedInteropCSharpSerializer
{
    List<byte[]> Serialize(List<object> dataList);
}
```

Ten kod jest interfejsem dla deserializacji Java:

```csharp
public interface ICustomizedInteropJavaDeserializer {
    public void prepare(String[] targetClassNames);
    public List<Object> Deserialize(List<ByteBuffer> dataList);
}
```

## <a name="scp-host-mode"></a>Tryb hosta SCP

W tym trybie można skompilować swój kod jako bibliotekę DLL i użyć SCPHost.exe, jak zostało to dostarczone przez punkt połączenia usługi, aby przesłać topologię. Plik specyfikacji wygląda podobnie do tego kodu:

```csharp
(scp-spout
  {
    "plugin.name" "SCPHost.exe"
    "plugin.args" ["HelloWorld.dll" "Scp.App.HelloWorld.Generator" "Get"]
    "output.schema" {"default" ["sentence"]}
  })
```

W tym miejscu `"plugin.name"` określono jako `"SCPHost.exe"` , który jest dostarczany przez zestaw SDK punktu połączenia usługi. SCPHost.exe akceptuje trzy parametry w następującej kolejności:

1. Nazwa biblioteki DLL, która jest `"HelloWorld.dll"` w tym przykładzie.
1. Nazwa klasy, która jest `"Scp.App.HelloWorld.Generator"` w tym przykładzie.
1. Nazwa publicznej metody statycznej, która może być wywoływana w celu uzyskania wystąpienia **ISCPPlugin**.

W obszarze Tryb hosta Skompiluj swój kod jako bibliotekę DLL dla wywołania przez platformę SCP. Ponieważ platforma może następnie uzyskać pełną kontrolę nad całą logiką przetwarzania, zalecamy przesłanie topologii w trybie hosta SCP. Upraszcza to środowisko programistyczne. Zapewnia również większą elastyczność i lepszą zgodność z poprzednimi wersjami w nowszych wersjach.

## <a name="scp-programming-examples"></a>Przykłady programowania usługi SCP

### <a name="helloworld"></a>HelloWorld

Poniższy prosty przykład HelloWorld przedstawia smak SCP.NET. Używa ona topologii nietransakcyjnej z elementu Spout o nazwie **generatora** i dwóch piorunów o nazwie **rozdzielacza** i **licznika**. **Generator** elementu Spout losowo generuje zdania i emituje te zdania do **rozdzielacza**. Piorun **rozdzielacza** dzieli zdania na słowa i emituje te słowa do błyskawicy **licznika** . Piorun **używa** słownika do rejestrowania wystąpień każdego wyrazu.

Ten przykład ma dwa pliki specyfikacji: HelloWorld. spec i HelloWorld \_ EnableAck. spec. Kod w języku C# może dowiedzieć się, czy potwierdzenie jest włączone, pobierając `pluginConf` obiekt ze strony Java.

```csharp
/* demo how to get pluginConf info */
if (Context.Config.pluginConf.ContainsKey(Constants.NONTRANSACTIONAL_ENABLE_ACK))
{
    enableAck = (bool)(Context.Config.pluginConf[Constants.NONTRANSACTIONAL_ENABLE_ACK]);
}
Context.Logger.Info("enableAck: {0}", enableAck);
```

Jeśli w elementu Spout jest włączona funkcja potwierdzenie, słownik buforuje kolekcje, które nie zostały potwierdzone. Jeśli `Fail` jest wywoływana, niepomyślna Krotka jest odtwarzana.

```csharp
public void Fail(long seqId, Dictionary<string, Object> parms)
{
    Context.Logger.Info("Fail, seqId: {0}", seqId);
    if (cachedTuples.ContainsKey(seqId))
    {
        /* get the cached tuple */
        string sentence = cachedTuples[seqId];

        /* replay the failed tuple */
        Context.Logger.Info("Re-Emit: {0}, seqId: {1}", sentence, seqId);
        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(sentence), seqId);
    }
    else
    {
        Context.Logger.Warn("Fail(), can't find cached tuple for seqId {0}!", seqId);
    }
}
```

### <a name="helloworldtx"></a>HelloWorldTx

W poniższym przykładzie HelloWorldTx pokazano, jak wdrożyć topologię transakcyjną. Przykład ma jeden elementu Spout o nazwie **generatora**, obiekt wsadowy o nazwie **częściowa liczba**i obiekt zatwierdzający o nazwie **Count-sum**. Przykład zawiera również trzy istniejące pliki tekstowe: DataSource0.txt, DataSource1.txt i DataSource2.txt.

W poszczególnych transakcjach **Generator** elementu Spout losowo wybiera dwa pliki z istniejących trzech plików i emituje dwie nazwy plików **do błyskawicy** . Piorun **częściowej liczby** :

1. Pobiera nazwę pliku z odebranej krotki.
1. Otwiera odpowiedni plik.
1. Zlicza liczbę słów w pliku.
1. Emituje liczbę słów do wartości **licznika-suma** .

Wartość **licznika-suma** jest podsumowanie łącznej liczby.

Aby osiągnąć dokładnie jednokrotne semantykę **, należy ocenić** , czy jest to powtórzona transakcja. W tym przykładzie ma następującą statyczną zmienną członkowską:

```csharp
public static long lastCommittedTxId = -1; 
```

Po utworzeniu wystąpienia **ISCPBatchBolt** Pobiera wartość `txAttempt` obiektu z parametrów wejściowych.

```csharp
public static CountSum Get(Context ctx, Dictionary<string, Object> parms)
{
    /* for transactional topology, we can get txAttempt from the input parms */
    if (parms.ContainsKey(Constants.STORM_TX_ATTEMPT))
    {
        StormTxAttempt txAttempt = (StormTxAttempt)parms[Constants.STORM_TX_ATTEMPT];
        return new CountSum(ctx, txAttempt);
    }
    else
    {
        throw new Exception("null txAttempt");
    }
}
```

Gdy `FinishBatch` jest wywoływana, `lastCommittedTxId` jest aktualizowana, jeśli nie jest to powtórzona transakcja.

```csharp
public void FinishBatch(Dictionary<string, Object> parms)
{
    /* judge whether it is a replayed transaction */
    bool replay = (this.txAttempt.TxId <= lastCommittedTxId);

    if (!replay)
    {
        /* If it is not replayed, update the totalCount and lastCommittedTxId value */
        totalCount = totalCount + this.count;
        lastCommittedTxId = this.txAttempt.TxId;
    }
    … …
}
```

### <a name="hybridtopology"></a>HybridTopology

Ta topologia zawiera elementu Spout Java i piorun języka C#. Używa domyślnej serializacji i deserializacji wdrożenia dostarczonej przez platformę SCP. Zobacz plik HybridTopology. spec w \\ folderze przykłady HybridTopology, aby uzyskać szczegółowe informacje dotyczące pliku specyfikacji. Zobacz również SubmitTopology.bat, jak określić ścieżkę klasy Java.

### <a name="scphostdemo"></a>SCPHostDemo

Ten przykład jest w istocie taki sam jak HelloWorld. Jedyną różnicą jest to, że kod jest kompilowany jako biblioteka DLL, a topologia jest przesyłana przy użyciu SCPHost.exe. Aby uzyskać bardziej szczegółowy opis, zobacz sekcję Tryb hosta punktu połączenia usługi.

## <a name="next-steps"></a>Następne kroki

Przykłady topologii Apache Storm utworzonych za pomocą usługi SCP można znaleźć w następujących artykułach:

* [Tworzenie topologii języka C# dla Apache Storm w usłudze HDInsight przy użyciu programu Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md)
* [Przetwarzanie zdarzeń z usługi Azure Event Hubs przy użyciu Apache Storm w usłudze HDInsight](apache-storm-develop-csharp-event-hub-topology.md)
* [Przetwarzanie danych z czujnika pojazdu z Event Hubs przy użyciu Apache Storm w usłudze HDInsight](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/IotExample)
* [Wyodrębnianie, przekształcanie i ładowanie (ETL) z usługi Azure Event Hubs do platformy Apache HBase](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample)
