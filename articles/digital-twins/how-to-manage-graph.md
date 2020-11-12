---
title: Zarządzanie grafem bliźniaczych reprezentacji z relacjami
titleSuffix: Azure Digital Twins
description: Zobacz, jak zarządzać grafem cyfrowego bliźniaczych reprezentacji, łącząc je z relacjami.
author: baanders
ms.author: baanders
ms.date: 11/03/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 73aa6f8f6ee36aeeb41fbc54afe217ac776a4ebc
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94533897"
---
# <a name="manage-a-graph-of-digital-twins-using-relationships"></a>Zarządzanie grafem cyfrowego bliźniaczych reprezentacji przy użyciu relacji

Bliźniaczych reprezentacji na platformie Azure Digital to [Wykres dwuosiowy](concepts-twins-graph.md) reprezentujący całe środowisko. Wykres dwuosiowy jest wykonany z pojedynczej bliźniaczych reprezentacji cyfrowej połączonej za pośrednictwem **relacji**. 

Gdy korzystasz z działającego [wystąpienia usługi Azure Digital bliźniaczych reprezentacji](how-to-set-up-instance-portal.md) i ustawisz kod [uwierzytelniania](how-to-authenticate-client.md) w aplikacji klienckiej, możesz użyć [**interfejsów API DigitalTwins**](/rest/api/digital-twins/dataplane/twins) , aby tworzyć, modyfikować i usuwać bliźniaczych reprezentacji cyfrowe oraz ich relacje w wystąpieniu bliźniaczych reprezentacji cyfrowych platformy Azure. Można również użyć [zestawu SDK platformy .NET (C#)](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true)lub [interfejsu wiersza polecenia usługi Azure Digital bliźniaczych reprezentacji](how-to-use-cli.md).

Ten artykuł koncentruje się na zarządzaniu relacjami i wykresem jako całościowym; Aby korzystać z pojedynczych bliźniaczych reprezentacji cyfrowych, zobacz [*How to: Manage Digital bliźniaczych reprezentacji*](how-to-manage-twin.md).

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

## <a name="ways-to-manage-graph"></a>Sposoby zarządzania programem Graph

[!INCLUDE [digital-twins-ways-to-manage.md](../../includes/digital-twins-ways-to-manage.md)]

Możesz również wprowadzać zmiany w grafie przy użyciu przykładu Eksploratora Digital bliźniaczych reprezentacji (ADT), który pozwala na wizualizację bliźniaczych reprezentacji i grafu, i używa zestawu SDK w tle. W następnej sekcji opisano ten przykład szczegółowo.

[!INCLUDE [visualizing with Azure Digital Twins explorer](../../includes/digital-twins-visualization.md)]

## <a name="create-relationships"></a>Utwórz relacje

Relacje opisują sposób, w jaki różne bliźniaczych reprezentacji cyfrowe są połączone ze sobą, które stanowią podstawę grafu sznurka.

Relacje są tworzone przy użyciu `CreateOrReplaceRelationshipAsync()` wywołania. 

Aby utworzyć relację, należy określić:
* Identyfikator przędzy źródłowej ( `srcId` w poniższym przykładzie kodu): Identyfikator przędzy, z której pochodzi relacja.
* Identyfikator przędzy docelowej ( `targetId` w poniższym przykładzie kodu): Identyfikator dwuosiowy, do którego nadeszła relacja.
* Nazwa relacji ( `relName` w poniższym przykładzie kodu): ogólny typ relacji, co _zawiera_.
* Identyfikator relacji ( `relId` w poniższym przykładzie kodu): Nazwa określona dla tej relacji, taka jak _Relationship1_.

Identyfikator relacji musi być unikatowy w obrębie danej przędzy źródłowej. Nie musi być globalnie unikatowa.
Na przykład dla sznurka *foo* każdy określony identyfikator relacji musi być unikatowy. Jednak inny *pasek* dwuosiowy może mieć relację wychodzącą zgodną z tym samym identyfikatorem relacji *foo* .

Poniższy przykład kodu ilustruje sposób tworzenia relacji w wystąpieniu usługi Azure Digital bliźniaczych reprezentacji.

```csharp
public async static Task CreateRelationship(DigitalTwinsClient client, string srcId, string targetId, string relName)
        {
            var relationship = new BasicRelationship
            {
                TargetId = targetId,
                Name = relName
            };

            try
            {
                string relId = $"{srcId}-{relName}->{targetId}";
                await client.CreateOrReplaceRelationshipAsync<BasicRelationship>(srcId, relId, relationship);
                Console.WriteLine($"Created {relName} relationship successfully");
            }
            catch (RequestFailedException rex)
            {
                Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
            }
            
        }
```
W metodzie Main można teraz wywołać `CreateRelationship()` funkcję, aby utworzyć relację _zawierającą_ , taką jak: 

```csharp
await CreateRelationship(client, srcId, targetId, "contains");
```
Jeśli chcesz utworzyć wiele relacji, możesz powtórzyć wywołania do tej samej metody, przekazując różne typy relacji do argumentu. 

Aby uzyskać więcej informacji na temat klasy pomocnik `BasicRelationship` , zobacz [*How to: Use the Azure Digital bliźniaczych reprezentacji API and SDK*](how-to-use-apis-sdks.md#serialization-helpers).

### <a name="create-multiple-relationships-between-twins"></a>Tworzenie wielu relacji między bliźniaczych reprezentacji

Relacje mogą być klasyfikowane jako: 

* Relacje wychodzące: relacje należące do tego sznurka, które wskazują na zewnątrz, aby połączyć je z innymi bliźniaczych reprezentacji. `GetRelationshipsAsync()`Metoda jest używana do uzyskiwania wychodzących relacji dla sznurka.
* Relacje przychodzące: relacje należące do innych bliźniaczych reprezentacji, które wskazują na ten dwuosiowy, aby utworzyć link "przychodzący". `GetIncomingRelationshipsAsync()`Metoda jest używana do uzyskiwania ruchu przychodzącego z sznurka.

Nie ma ograniczeń dotyczących liczby relacji, które mogą mieć między dwoma bliźniaczych reprezentacji — można mieć dowolną liczbę relacji między bliźniaczych reprezentacji. 

Oznacza to, że można wyrazić kilka różnych typów relacji między dwoma bliźniaczych reprezentacji jednocześnie. Na przykład *przędza a* może mieć zarówno *przechowywaną* relację, jak i *wytworzoną* relację z *przędzą B*.

W razie potrzeby można nawet utworzyć wiele wystąpień tego samego typu relacji między tymi samymi dwoma bliźniaczych reprezentacji. W tym przykładzie *sznurek A* może mieć dwie różne *przechowywane* relacje z *przędzą B* , o ile relacje mają różne identyfikatory relacji.

## <a name="list-relationships"></a>Wyświetl relacje

Aby uzyskać dostęp do listy relacji **wychodzących** dla danego przędzy na grafie, można użyć `GetRelationships()` metody podobnej do tego:

```csharp
await client.GetRelationships()
```

To zwraca `Azure.Pageable<T>` lub `Azure.AsyncPageable<T>` , w zależności od tego, czy używana jest wersja synchroniczna lub asynchroniczna wywołania.

Oto przykład, który pobiera listę relacji:

```csharp
public static async Task<List<BasicRelationship>> FindOutgoingRelationshipsAsync(DigitalTwinsClient client, string dtId)
        {
            // Find the relationships for the twin
            try
            {
                // GetRelationshipsAsync will throw if an error occurs
                AsyncPageable<BasicRelationship> rels = client.GetRelationshipsAsync<BasicRelationship>(dtId);
                List<BasicRelationship> results = new List<BasicRelationship>();
                await foreach (BasicRelationship rel in rels)
                {
                    results.Add(rel);
                    Console.WriteLine($"Found relationship-{rel.Name}->{rel.TargetId}");
                }

                return results;
            }
            catch (RequestFailedException ex)
            {
                Console.WriteLine($"**_ Error {ex.Status}/{ex.ErrorCode} retrieving relationships for {dtId} due to {ex.Message}");
                return null;
            }
        }

```
Teraz można wywołać tę metodę, aby zobaczyć wychodzące relacje bliźniaczych reprezentacji w następujący sposób:

```csharp
await FindOutgoingRelationshipsAsync(client, twin_Id);
```
Możesz użyć pobranych relacji, aby przejść do innych bliźniaczych reprezentacji na grafie. Aby to zrobić, Przeczytaj `target` pole z zwróconej relacji i użyj go jako identyfikatora dla następnego wywołania `GetDigitalTwin()` .

### <a name="find-incoming-relationships-to-a-digital-twin"></a>Znajdź przychodzące relacje z dwuosiową cyfrą

Usługa Azure Digital bliźniaczych reprezentacji ma także interfejs API, który umożliwia znalezienie wszystkich *przychodzących* * relacji do danej przędzy. Jest to często przydatne w przypadku nawigacji odwrotnej lub podczas usuwania sznurka.

Poprzedni przykład kodu koncentruje się na znajdowaniu relacji wychodzących od sznurka. Poniższy przykład ma strukturę podobną, ale zamiast tego znajduje *przychodzące* relacje z przędzą.

Należy zauważyć, że `IncomingRelationship` wywołania nie zwracają pełnej treści relacji.

```csharp
public static async Task<List<IncomingRelationship>> FindIncomingRelationshipsAsync(DigitalTwinsClient client, string dtId)
        {
            // Find the relationships for the twin
            try
            {
                // GetRelationshipsAsync will throw an error if a problem occurs
                AsyncPageable<IncomingRelationship> incomingRels = client.GetIncomingRelationshipsAsync(dtId);

                List<IncomingRelationship> results = new List<IncomingRelationship>();
                await foreach (IncomingRelationship incomingRel in incomingRels)
                {
                    results.Add(incomingRel);
                    Console.WriteLine($"Found incoming relationship-{incomingRel.RelationshipId}");

                }
                return results;
            }
            catch (RequestFailedException ex)
            {
                Console.WriteLine($"*** Error {ex.Status}/{ex.ErrorCode} retrieving incoming relationships for {dtId} due to {ex.Message}");
                return null;
            }
        }
```

Teraz można wywołać tę metodę, aby zobaczyć przychodzące relacje bliźniaczych reprezentacji w następujący sposób:

```csharp
await FindIncomingRelationshipsAsync(client, twin_Id);
```
### <a name="list-all-twin-properties-and-relationships"></a>Wyświetl wszystkie właściwości i relacje z przędzą

Korzystając z podanych powyżej metod wyświetlania wychodzących i przychodzących relacji do sznurka, można utworzyć metodę, która drukuje pełne informacje o pojedynczej bliźniaczyej, w tym właściwości przędzy i oba typy relacji. Poniżej przedstawiono przykładową metodę o nazwie `FetchAndPrintTwinAsync()` , która pokazuje, jak to zrobić.

```csharp  
private static async Task FetchAndPrintTwinAsync(DigitalTwinsClient client, string twin_Id)
        {
            BasicDigitalTwin twin;
            Response<BasicDigitalTwin> res = client.GetDigitalTwin(twin_Id);
            
            await FindOutgoingRelationshipsAsync(client, twin_Id);
            await FindIncomingRelationshipsAsync(client, twin_Id);

            return;
        }
```

Teraz można wywołać tę funkcję w metodzie Main w następujący sposób: 

```csharp
await FetchAndPrintTwinAsync(client, targetId);
```
## <a name="delete-relationships"></a>Usuń relacje

Pierwszy parametr określa przędzę źródłową (dwuosiową, z której pochodzi relacja). Drugim parametrem jest identyfikator relacji. Wymagany jest zarówno identyfikator przędzy, jak i identyfikator relacji, ponieważ identyfikatory relacji są unikatowe tylko w zakresie sznurka.

```csharp
private static async Task DeleteRelationship(DigitalTwinsClient client, string srcId, string relId)
        {
            try
            {
                Response response = await client.DeleteRelationshipAsync(srcId, relId);
                await FetchAndPrintTwinAsync(srcId, client);
                Console.WriteLine("Deleted relationship successfully");
            }
            catch (RequestFailedException Ex)
            {
                Console.WriteLine($"Error {Ex.ErrorCode}");
            }
        }
```

Teraz można wywołać tę metodę, aby usunąć relację podobną do tej:

```csharp
await DeleteRelationship(client, srcId, relId);
```

## <a name="runnable-twin-graph-sample"></a>Przykład grafu możliwy do uruchomienia bliźniaczy

Poniższy fragment kodu możliwy do uruchomienia używa operacji relacji z tego artykułu, aby utworzyć wykres Wieloosiowy z bliźniaczych reprezentacjiami i relacjami cyfrowymi.

### <a name="set-up-the-runnable-sample"></a>Konfigurowanie przykładu możliwy do uruchomienia

Fragment kodu używa [*Room.json*](https://github.com/Azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Room.json) i [*Floor.jsw*](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json) definicjach modelu z [*samouczka: Poznaj usługę Azure Digital bliźniaczych reprezentacji z przykładową aplikacją kliencką*](tutorial-command-line-app.md). Możesz użyć tych linków, aby przejść bezpośrednio do plików lub pobrać je jako część pełnego, kompleksowego [projektu](/samples/azure-samples/digital-twins-samples/digital-twins-samples/)przykładu. 

Przed uruchomieniem przykładu należy wykonać następujące czynności:
1. Pobierz pliki modelu, umieść je w projekcie i Zastąp `<path-to>` symbole zastępcze w poniższym kodzie, aby poinformować program, gdzie go znaleźć.
2. Zastąp symbol zastępczy `<your-instance-hostname>` nazwą hosta usługi Azure Digital bliźniaczych reprezentacji.
3. Dodaj te pakiety do projektu:
    ```cmd/sh
    dotnet add package Azure.DigitalTwins.Core --version 1.0.0-preview.3
    dotnet add package Azure.identity
    ```

Musisz również skonfigurować poświadczenia lokalne, jeśli chcesz uruchomić próbkę bezpośrednio. Następna sekcja przeprowadzi Cię przez ten temat.
[!INCLUDE [Azure Digital Twins: local credentials prereq (outer)](../../includes/digital-twins-local-credentials-outer.md)]

### <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

Po wykonaniu powyższych kroków można bezpośrednio uruchomić następujący przykładowy kod.

```csharp 
using System;
using Azure.DigitalTwins.Core;
using Azure.Identity;
using System.Threading.Tasks;
using System.IO;
using System.Collections.Generic;
using Azure;
using Azure.DigitalTwins.Core.Serialization;
using System.Text.Json;

namespace minimal
{
    class Program
    {

        public static async Task Main(string[] args)
        {
            Console.WriteLine("Hello World!");

            //Create the Azure Digital Twins client for API calls
            DigitalTwinsClient client = createDTClient();
            Console.WriteLine($"Service client created – ready to go");
            Console.WriteLine();

            //Upload models
            Console.WriteLine($"Upload models");
            Console.WriteLine();
            string dtdl = File.ReadAllText("<path-to>/Room.json");
            string dtdl1 = File.ReadAllText("<path-to>/Floor.json");
            var typeList = new List<string>();
            typeList.Add(dtdl);
            typeList.Add(dtdl1);
            // Upload the models to the service
            await client.CreateModelsAsync(typeList);

            //Create new (Floor) digital twin
            BasicDigitalTwin floorTwin = new BasicDigitalTwin();
            string srcId = "myFloorID";
            floorTwin.Metadata = new DigitalTwinMetadata();
            floorTwin.Metadata.ModelId = "dtmi:example:Floor;1";
            //Floor twins have no properties, so nothing to initialize
            //Create the twin
            await client.CreateOrReplaceDigitalTwinAsync<BasicDigitalTwin>(srcId, floorTwin);
            Console.WriteLine("Twin created successfully");

            //Create second (Room) digital twin
            BasicDigitalTwin roomTwin = new BasicDigitalTwin();
            string targetId = "myRoomID";
            roomTwin.Metadata = new DigitalTwinMetadata();
            roomTwin.Metadata.ModelId = "dtmi:example:Room;1";
            // Initialize properties
            Dictionary<string, object> props = new Dictionary<string, object>();
            props.Add("Temperature", 35.0);
            props.Add("Humidity", 55.0);
            roomTwin.Contents = props;
            //Create the twin
            await client.CreateOrReplaceDigitalTwinAsync<BasicDigitalTwin>(targetId, roomTwin);
            
            //Create relationship between them
            await CreateRelationship(client, srcId, targetId, "contains");
            Console.WriteLine();

            //Print twins and their relationships
            Console.WriteLine("--- Printing details:");
            Console.WriteLine("Outgoing relationships from source twin:");
            await FetchAndPrintTwinAsync(srcId, client);
            Console.WriteLine();
            Console.WriteLine("Incoming relationships to target twin:");
            await FetchAndPrintTwinAsync(targetId, client);
            Console.WriteLine("--------");
            Console.WriteLine();

            //Delete the relationship
            Console.WriteLine("Deleting the relationship");
            await DeleteRelationship(client, srcId, $"{srcId}-contains->{targetId}");
            Console.WriteLine();

            //Print twins and their relationships again
            Console.WriteLine("--- Printing details:");
            Console.WriteLine("Outgoing relationships from source twin:");
            await FetchAndPrintTwinAsync(srcId, client);
            Console.WriteLine();
            Console.WriteLine("Incoming relationships to target twin:");
            await FetchAndPrintTwinAsync(targetId, client);
            Console.WriteLine("--------");
            Console.WriteLine();
        }

        private static DigitalTwinsClient createDTClient()
        {
            string adtInstanceUrl = "https://<your-instance-hostname>";
            var credentials = new DefaultAzureCredential();
            DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credentials);
            return client;
        }
        private async static Task CreateRelationship(DigitalTwinsClient client, string srcId, string targetId, string relName)
        {
            // Create relationship between twins
            var relationship = new BasicRelationship
            {
                TargetId = targetId,
                Name = relName
            };

            try
            {
                string relId = $"{srcId}-{relName}->{targetId}";
                await client.CreateOrReplaceRelationshipAsync<BasicRelationship>(srcId, relId, relationship);
                Console.WriteLine($"Created {relName} relationship successfully");
            }
            catch (RequestFailedException rex)
            {
                Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
            }

        }

        private static async Task FetchAndPrintTwinAsync(string twin_Id, DigitalTwinsClient client)
        {
            BasicDigitalTwin twin;
            Response<BasicDigitalTwin> res = client.GetDigitalTwin(twin_Id);
            await FindOutgoingRelationshipsAsync(client, twin_Id);
            await FindIncomingRelationshipsAsync(client, twin_Id);

            return;
        }

        private static async Task<List<BasicRelationship>> FindOutgoingRelationshipsAsync(DigitalTwinsClient client, string dtId)
        {
            // Find the relationships for the twin
            
            try
            {
                // GetRelationshipsAsync will throw if an error occurs
                AsyncPageable<BasicRelationship> rels = client.GetRelationshipsAsync<BasicRelationship>(dtId);
                List<BasicRelationship> results = new List<BasicRelationship>();
                await foreach (BasicRelationship rel in rels)
                {
                    results.Add(rel);
                    Console.WriteLine($"Found relationship-{rel.Name}->{rel.TargetId}");
                }

                return results;
            }
            catch (RequestFailedException ex)
            {
                Console.WriteLine($"*** Error {ex.Status}/{ex.ErrorCode} retrieving relationships for {dtId} due to {ex.Message}");
                return null;
            }
        }

        private static async Task<List<IncomingRelationship>> FindIncomingRelationshipsAsync(DigitalTwinsClient client, string dtId)
        {
            // Find the relationships for the twin
            
            try
            {
                // GetRelationshipsAsync will throw an error if a problem occurs
                AsyncPageable<IncomingRelationship> incomingRels = client.GetIncomingRelationshipsAsync(dtId);

                List<IncomingRelationship> results = new List<IncomingRelationship>();
                await foreach (IncomingRelationship incomingRel in incomingRels)
                {
                    results.Add(incomingRel);
                    Console.WriteLine($"Found incoming relationship-{incomingRel.RelationshipId}");
                }
                return results;
            }
            catch (RequestFailedException ex)
            {
                Console.WriteLine($"**_ Error {ex.Status}/{ex.ErrorCode} retrieving incoming relationships for {dtId} due to {ex.Message}");
                return null;
            }
        }

        private static async Task DeleteRelationship(DigitalTwinsClient client, string srcId, string relId)
        {
            try
            {
                Response response = await client.DeleteRelationshipAsync(srcId, relId);
                await FetchAndPrintTwinAsync(srcId, client);
                Console.WriteLine("Deleted relationship successfully");
            }
            catch (RequestFailedException Ex)
            {
                Console.WriteLine($"Error {Ex.ErrorCode}");
            }
        }
    }
}
```

Oto dane wyjściowe konsoli powyższego programu: 

:::image type="content" source="./media/how-to-manage-graph/console-output-twin-graph.png" alt-text="Dane wyjściowe konsoli pokazujące szczegóły sznurka, przychodzące i wychodzące relacje bliźniaczych reprezentacji." lightbox="./media/how-to-manage-graph/console-output-twin-graph.png":::

> [!TIP]
> Wykres bliźniaczy jest koncepcją tworzenia relacji między bliźniaczych reprezentacji. Jeśli chcesz wyświetlić wizualną reprezentację grafu bliźniaczyego, zobacz sekcję [_Visualization *](how-to-manage-graph.md#visualization) w tym artykule. 

### <a name="create-a-twin-graph-from-a-csv-file"></a>Tworzenie wykresu dwuosiowego na podstawie pliku CSV

W praktyce przypadki użycia hierarchie bliźniaczych są często tworzone na podstawie danych przechowywanych w innej bazie danych lub w arkuszu kalkulacyjnym lub pliku CSV. W tej sekcji przedstawiono sposób odczytywania danych z pliku CSV i tworzenia wykresu dwuosiowego.

Rozważmy poniższą tabelę danych opisującą zestaw bliźniaczych reprezentacji cyfrowych i relacji.

|  Identyfikator modelu    | Identyfikator przędzy (musi być unikatowy) | Nazwa relacji  | Identyfikator przędzy docelowej  | Dane inicjowania przędzy |
| --- | --- | --- | --- | --- |
| dtmi: przykład: Floor; 1    | Floor1 | zawiera | Room1 | |
| dtmi: przykład: Floor; 1    | Floor0 | zawiera | Room0 | |
| dtmi: przykład: Pokój; 1    | Room1 | | | {"Temperatura": 80} |
| dtmi: przykład: Pokój; 1    | Room0 | | | {"Temperatura": 70} |

Jednym ze sposobów uzyskania tych danych w usłudze Azure Digital bliźniaczych reprezentacji jest przekonwertowanie tabeli do pliku CSV i zapisanie kodu w celu zinterpretowania pliku w poleceniach w celu utworzenia bliźniaczych reprezentacji i relacji. Poniższy przykład kodu ilustruje odczytywanie danych z pliku CSV i tworzenie wykresu dwuosiowego w usłudze Azure Digital bliźniaczych reprezentacji.

W poniższym kodzie plik CSV jest nazywany *data.csv* i istnieje symbol zastępczy reprezentujący **nazwę hosta** wystąpienia usługi Azure Digital bliźniaczych reprezentacji. Przykład korzysta również z kilku pakietów, które można dodać do projektu, aby pomóc w tym procesie.

```csharp
using System;
using System.Collections.Generic;
using System.Text.Json;
using System.Threading.Tasks;
using Azure;
using Azure.DigitalTwins.Core;
using Azure.Identity;

namespace creating_twin_graph_from_csv
{
    class Program
    {
        static async Task Main(string[] args)
        {
            List<BasicRelationship> RelationshipRecordList = new List<BasicRelationship>();
            List<BasicDigitalTwin> TwinList = new List<BasicDigitalTwin>();
            List<List<string>> data = ReadData();
            DigitalTwinsClient client = createDTClient();

            // Interpret the CSV file data, by each row
            foreach (List<string> row in data)
            {
                string modelID = row.Count > 0 ? row[0].Trim() : null;
                string srcID = row.Count > 1 ? row[1].Trim() : null;
                string relName = row.Count > 2 ? row[2].Trim() : null;
                string targetID = row.Count > 3 ? row[3].Trim() : null;
                string initProperties = row.Count > 4 ? row[4].Trim() : null;
                Console.WriteLine($"ModelID: {modelID}, TwinID: {srcID}, RelName: {relName}, TargetID: {targetID}, InitData: {initProperties}");
                Dictionary<string, object> props = new Dictionary<string, object>();
                // Parse properties into dictionary (left out for compactness)
                // ...

                // Null check for source and target ID's
                if (srcID != null && srcID.Length > 0 && targetID != null && targetID.Length > 0)
                {
                    BasicRelationship br = new BasicRelationship()
                    {
                        SourceId = srcID,
                        TargetId = targetID,
                        Name = relName
                    };
                    RelationshipRecordList.Add(br);
                }
                BasicDigitalTwin srcTwin = new BasicDigitalTwin();
                srcTwin.Id = srcID;
                srcTwin.Metadata = new DigitalTwinMetadata();
                srcTwin.Metadata.ModelId = modelID;
                srcTwin.Contents = props;
                TwinList.Add(srcTwin);
            }

            // Create digital twins 
            foreach (BasicDigitalTwin twin in TwinList)
            {
                try
                {
                    await client.CreateOrReplaceDigitalTwinAsync<BasicDigitalTwin>(twin.Id, twin);
                    Console.WriteLine("Twin is created");
                }
                catch (RequestFailedException e)
                {
                    Console.WriteLine($"Error {e.Status}: {e.Message}");
                }
            }
            // Create relationships between the twins
            foreach (BasicRelationship rec in RelationshipRecordList)
            {
                try
                {
                    string relId = $"{rec.SourceId}-{rec.Name}->{rec.TargetId}";
                    await client.CreateOrReplaceRelationshipAsync<BasicRelationship>(rec.SourceId, relId, rec);
                    Console.WriteLine("Relationship is created");
                }
                catch (RequestFailedException e)
                {
                    Console.WriteLine($"Error {e.Status}: {e.Message}");
                }
            }
        }

        // Method to ingest data from the CSV file
        public static List<List<string>> ReadData()
        {
            string path = "<path-to>/data.csv";
            string[] lines = System.IO.File.ReadAllLines(path);
            List<List<string>> data = new List<List<string>>();
            int count = 0;
            foreach (string line in lines)
            {
                if (count++ == 0)
                    continue;
                List<string> cols = new List<string>();
                data.Add(cols);
                string[] columns = line.Split(',');
                foreach (string column in columns)
                {
                    cols.Add(column);
                }
            }
            return data;
        }
        // Method to create the digital twins client
        private static DigitalTwinsClient createDTClient()
        {

            string adtInstanceUrl = "https://<your-instance-hostname>";
            var credentials = new DefaultAzureCredential();
            DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credentials);
            return client;
        }
    }
}

```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat wykonywania zapytań dotyczących grafu wieloosiowego usługi Azure Digital bliźniaczych reprezentacji:
* [*Koncepcje: język zapytań*](concepts-query-language.md)
* [*Instrukcje: zapytanie o wykres bliźniaczy*](how-to-query-graph.md)