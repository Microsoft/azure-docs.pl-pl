---
title: Zarządzanie grafem bliźniaczych reprezentacji z relacjami
titleSuffix: Azure Digital Twins
description: Zobacz, jak zarządzać grafem cyfrowego bliźniaczych reprezentacji, łącząc je z relacjami.
author: baanders
ms.author: baanders
ms.date: 4/10/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: a0ab8f8ff3f2134c205338dfe8e6f2e887a5a053
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/12/2020
ms.locfileid: "91949619"
---
# <a name="manage-a-graph-of-digital-twins-using-relationships"></a>Zarządzanie grafem cyfrowego bliźniaczych reprezentacji przy użyciu relacji

Bliźniaczych reprezentacji na platformie Azure Digital to [Wykres dwuosiowy](concepts-twins-graph.md) reprezentujący całe środowisko. Wykres bliźniaczy składa się z pojedynczych bliźniaczych reprezentacji cyfrowych połączonych za pośrednictwem **relacji**.

Gdy korzystasz z działającego [wystąpienia usługi Azure Digital bliźniaczych reprezentacji](how-to-set-up-instance-portal.md) i ustawisz kod [uwierzytelniania](how-to-authenticate-client.md) w aplikacji klienckiej, możesz użyć [**interfejsów API DigitalTwins**](how-to-use-apis-sdks.md) , aby tworzyć, modyfikować i usuwać bliźniaczych reprezentacji cyfrowe oraz ich relacje w wystąpieniu bliźniaczych reprezentacji cyfrowych platformy Azure. Można również użyć [zestawu SDK platformy .NET (C#)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)lub [interfejsu wiersza polecenia usługi Azure Digital bliźniaczych reprezentacji](how-to-use-cli.md).

Ten artykuł koncentruje się na zarządzaniu relacjami i wykresem jako całościowym; Aby korzystać z pojedynczych bliźniaczych reprezentacji cyfrowych, zobacz [*How to: Manage Digital bliźniaczych reprezentacji*](how-to-manage-twin.md).

[!INCLUDE [visualizing with Azure Digital Twins explorer](../../includes/digital-twins-visualization.md)]

## <a name="create-relationships"></a>Utwórz relacje

Relacje opisują sposób, w jaki różne bliźniaczych reprezentacji cyfrowe są połączone ze sobą, które stanowią podstawę grafu sznurka.

Relacje są tworzone przy użyciu `CreateRelationship` wywołania. 

Aby utworzyć relację, należy określić:
* Identyfikator przędzy źródłowej (przędza, z której pochodzi relacja)
* Docelowy identyfikator sznurka (dwuosiowy, do którego nadeszła relacja)
* Nazwa relacji
* Identyfikator relacji

Identyfikator relacji musi być unikatowy w obrębie danej przędzy źródłowej. Nie musi być globalnie unikatowa.
Na przykład dla sznurka *foo*każdy określony identyfikator relacji musi być unikatowy. Jednak inny *pasek* dwuosiowy może mieć relację wychodzącą zgodną z tym samym identyfikatorem relacji *foo* . 

Poniższy przykład kodu ilustruje sposób dodawania relacji do wystąpienia usługi Azure Digital bliźniaczych reprezentacji.

```csharp
public async static Task CreateRelationship(DigitalTwinsClient client, string srcId, string targetId)
{
    var relationship = new BasicRelationship
    {
        TargetId = targetId,
        Name = "contains"
    };

    try
    {
        string relId = $"{srcId}-contains->{targetId}";
        await client.CreateRelationshipAsync(srcId, relId, JsonSerializer.Serialize(relationship));
        Console.WriteLine("Created relationship successfully");
    }
    catch (RequestFailedException rex) {
        Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
    }
}
```

Aby uzyskać więcej informacji na temat klasy pomocnik `BasicRelationship` , zobacz [*How to: Use the Azure Digital bliźniaczych reprezentacji API and SDK*](how-to-use-apis-sdks.md).

### <a name="create-multiple-relationships-between-twins"></a>Tworzenie wielu relacji między bliźniaczych reprezentacji

Nie ma ograniczeń dotyczących liczby relacji, które mogą mieć między dwoma bliźniaczych reprezentacji — można mieć dowolną liczbę relacji między bliźniaczych reprezentacji. 

Oznacza to, że można wyrazić kilka różnych typów relacji między dwoma bliźniaczych reprezentacji jednocześnie. Na przykład *przędza a* może mieć zarówno *przechowywaną* relację, jak i *wytworzoną* relację z *przędzą B*.

W razie potrzeby można nawet utworzyć wiele wystąpień tego samego typu relacji między tymi samymi dwoma bliźniaczych reprezentacji. W tym przykładzie oznacza to, że *dla sznurka A* może istnieć dwie odrębne relacje *przechowywane* z *przędzą B*.

## <a name="list-relationships"></a>Wyświetl relacje

Aby uzyskać dostęp do listy relacji **wychodzących** pochodzących z danego przędzy na grafie, można użyć:

```csharp
await client.GetRelationshipsAsync(id);
```

Zwraca `Azure.Pageable<T>` lub `Azure.AsyncPageable<T>` , w zależności od tego, czy używana jest wersja synchroniczna lub asynchroniczna wywołania.

Oto pełny przykład, który pobiera listę relacji:

```csharp
public async Task<List<BasicRelationship>> FindOutgoingRelationshipsAsync(string dtId)
{
    // Find the relationships for the twin
    try
    {
        // GetRelationshipsAsync will throw if an error occurs
        AsyncPageable<string> relsJson = client.GetRelationshipsAsync(dtId);
        List<BasicRelationship> results = new List<BasicRelationship>();
        await foreach (string relJson in relsJson)
        {
            var rel = System.Text.Json.JsonSerializer.Deserialize<BasicRelationship>(relJson);
            results.Add(rel);
        }
        return results;
    }
    catch (RequestFailedException ex)
    {
        Log.Error($"*** Error {ex.Status}/{ex.ErrorCode} retrieving relationships for {dtId} due to {ex.Message}");
        return null;
    }
}
```

Możesz użyć pobranych relacji, aby przejść do innych bliźniaczych reprezentacji na grafie. Aby to zrobić, Przeczytaj `target` pole z zwróconej relacji i użyj go jako identyfikatora dla następnego wywołania `GetDigitalTwin` . 

### <a name="find-incoming-relationships-to-a-digital-twin"></a>Znajdź przychodzące relacje z dwuosiową cyfrą

Usługa Azure Digital bliźniaczych reprezentacji ma także interfejs API, aby znaleźć wszystkie **przychodzące** relacje z danym przędzą. Jest to często przydatne w przypadku nawigacji odwrotnej lub podczas usuwania sznurka.

Poprzedni przykład kodu koncentruje się na znajdowaniu relacji wychodzących od sznurka. Poniższy przykład ma strukturę podobną, ale zamiast tego znajduje *przychodzące* relacje z przędzą.

Należy zauważyć, że `IncomingRelationship` wywołania nie zwracają pełnej treści relacji.

```csharp
async Task<List<IncomingRelationship>> FindIncomingRelationshipsAsync(string dtId)
{
    // Find the relationships for the twin
    try
    {
        // GetRelationshipsAsync will throw an error if a problem occurs
        AsyncPageable<IncomingRelationship> incomingRels = client.GetIncomingRelationshipsAsync(dtId);

        List<IncomingRelationship> results = new List<IncomingRelationship>();
        await foreach (IncomingRelationship incomingRel in incomingRels)
            results.Add(incomingRel);
    }
    catch (RequestFailedException ex)
    {
        Log.Error($"*** Error {ex.Status}/{ex.ErrorCode} retrieving incoming relationships for {dtId} due to {ex.Message}");
    }
}
```

## <a name="delete-relationships"></a>Usuń relacje

Relacje można usunąć za pomocą `DeleteRelationship(source, relId);` .

Pierwszy parametr określa przędzę źródłową (dwuosiową, z której pochodzi relacja). Drugim parametrem jest identyfikator relacji. Wymagany jest zarówno identyfikator przędzy, jak i identyfikator relacji, ponieważ identyfikatory relacji są unikatowe tylko w zakresie sznurka.

## <a name="create-a-twin-graph"></a>Tworzenie wykresu dwuosiowego 

Poniższy fragment kodu używa operacji relacji z tego artykułu, aby utworzyć wykres bliźniaczy z bliźniaczych reprezentacji cyfrowych i relacji.

```csharp
static async Task CreateTwins()
{
    // Create twins - see utility functions below 
    await CreateRoom("Room01", 68, 50, false, "");
    await CreateRoom("Room02", 70, 66, true, "EId-00124");
    await CreateFloorOrBuilding("Floor01", makeFloor:true);

    // Create relationships
    await AddRelationship("Floor01", "contains", "Floor-to-Room01", "Room01");
    await AddRelationship("Floor01", "contains", "Floor-to-Room02", "Room02");
}

static async Task<bool> AddRelationship(string source, string relationship, string id, string target)
{
    var relationship = new BasicRelationship
    {
        TargetId = target,
        Name = relationship
    };

    try
    {
        string relId = $"{source}-contains->{target}";
        await client.CreateRelationshipAsync(source, relId, JsonSerializer.Serialize(relationship));
        Console.WriteLine("Created relationship successfully");
        return true;
    }
    catch (RequestFailedException rex) {
        Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
        return false;
    }
}

static async Task<bool> CreateRoom(string id, double temperature, double humidity)
{
    BasicDigitalTwin twin = new BasicDigitalTwin();
    twin.Metadata = new DigitalTwinMetadata();
    twin.Metadata.ModelId = "dtmi:com:contoso:Room;2";
    // Initialize properties
    Dictionary<string, object> props = new Dictionary<string, object>();
    props.Add("Temperature", temperature);
    props.Add("Humidity", humidity);
    twin.CustomProperties = props;
    
    try
    {
        client.CreateDigitalTwin(id, JsonSerializer.Serialize<BasicDigitalTwin>(twin)); 
        return true;       
    }
    catch (ErrorResponseException e)
    {
        Console.WriteLine($"*** Error creating twin {id}: {e.Response.StatusCode}"); 
        return false;
    }
}

static async Task<bool> CreateFloorOrBuilding(string id, bool makeFloor=true)
{
    string type = "dtmi:com:contoso:Building;3";
    if (makeFloor==true)
        type = "dtmi:com:contoso:Floor;2";
    BasicDigitalTwin twin = new BasicDigitalTwin();
    twin.Metadata = new DigitalTwinMetadata();
    twin.Metadata.ModelId = type;
    // Initialize properties
    Dictionary<string, object> props = new Dictionary<string, object>();
    props.Add("AverageTemperature", 0);
    twin.CustomProperties = props;
    
    try
    {
        client.CreateDigitalTwin(id, JsonSerializer.Serialize<BasicDigitalTwin>(twin));  
        return true;      
    }
    catch (ErrorResponseException e)
    {
        Console.WriteLine($"*** Error creating twin {id}: {e.Response.StatusCode}"); 
        return false;
    }
}
```

### <a name="create-a-twin-graph-from-a-spreadsheet"></a>Tworzenie wykresu dwuosiowego na podstawie arkusza kalkulacyjnego

W praktyce przypadki użycia hierarchie pojedynczej częściowej są tworzone na podstawie danych przechowywanych w innej bazie danych lub prawdopodobnie w arkuszu kalkulacyjnym. W tej sekcji pokazano, jak można analizować arkusz kalkulacyjny.

Weź pod uwagę poniższą tabelę danych opisującą zestaw bliźniaczych reprezentacji cyfrowych i relacje, które mają zostać utworzone.

| Model    | ID (Identyfikator) | Nadrzędny | Nazwa relacji | Inne dane |
| --- | --- | --- | --- | --- |
| wykładzin    | Floor01 | | | … |
| pokój    | Room10 | Floor01 | zawiera | … |
| pokój    | Room11 | Floor01 | zawiera | … |
| pokój    | Room12 | Floor01 | zawiera | … |
| wykładzin    | Floor02 | | | … |
| pokój    | Room21 | Floor02 | zawiera | … |
| pokój    | Room22 | Floor02 | zawiera | … |

Poniższy kod używa [interfejsu API Microsoft Graph](https://docs.microsoft.com/graph/overview) do odczytywania arkusza kalkulacyjnego i konstruowania grafu bliźniaczych reprezentacji cyfrowej platformy Azure na podstawie wyników.

```csharp
var range = msftGraphClient.Me.Drive.Items["BuildingsWorkbook"].Workbook.Worksheets["Building"].usedRange;
JsonDocument data = JsonDocument.Parse(range.values);
List<BasicRelationship> RelationshipRecordList = new List<BasicRelationship>();
foreach (JsonElement row in data.RootElement.EnumerateArray())
{
    string type = row[0].GetString();
    string id = row[1].GetString();
    string relSource = row[2].GetString();
    string relName = row[3].GetString();
    // Parse spreadsheet extra data into a JSON string to initialize the digital twin
    // Left out for compactness
    Dictionary<string, object> initData = new Dictionary<string, object>() { ... };

    if (relSource != null)
    {
        BasicRelationship br = new BasicRelationship()
        {
            SourceId = relSource,
            TargetId = id,
            Name = relName
        };
        RelationshipRecordList.Add(br);
    }

    BasicDigitalTwin twin = new BasicDigitalTwin();
    twin.CustomProperties = initData;
    // Set the type of twin to be created
    switch (type)
    {
        case "room":
            twin.Metadata = new DigitalTwinMetadata() { ModelId = "dtmi:com:contoso:Room;2" };
            break;
        case "floor":
            twin.Metadata = new DigitalTwinMetadata() { ModelId = "dtmi:com:contoso:Floor;2" };
            break;
        ... handle additional types
    }
    try
    {
        client.CreateDigitalTwin(id, JsonSerializer.Serialize<BasicDigitalTwin>(twin));
    }
    catch (RequestFailedException e)
    {
        Log.Error($"Error {e.Status}: {e.Message}");
    }
    foreach (BasicRelationship rec in RelationshipRecordList)
    { 
        try { 
            client.CreateRelationship(rec.SourceId, Guid.NewGuid().ToString(), JsonSerializer.Serialize<BasicRelationship>(rec));
        }
        catch (RequestFailedException e)
        {
            Log.Error($"Error {e.Status}: {e.Message}");
        }
    }
}
```
## <a name="manage-relationships-with-cli"></a>Zarządzanie relacjami przy użyciu interfejsu wiersza polecenia

Bliźniaczych reprezentacji i ich relacje można także zarządzać za pomocą interfejsu wiersza polecenia usługi Azure Digital bliźniaczych reprezentacji. Polecenia można znaleźć w [*opisie procedury: korzystanie z interfejsu wiersza polecenia usługi Azure Digital bliźniaczych reprezentacji*](how-to-use-cli.md).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat wykonywania zapytań dotyczących grafu wieloosiowego usługi Azure Digital bliźniaczych reprezentacji:
* [*Koncepcje: język zapytań*](concepts-query-language.md)
* [*Instrukcje: zapytanie o wykres bliźniaczy*](how-to-query-graph.md)