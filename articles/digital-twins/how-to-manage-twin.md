---
title: Zarządzanie usługą Digital Twins
titleSuffix: Azure Digital Twins
description: Zobacz, jak pobierać, aktualizować i usuwać poszczególne bliźniaczych reprezentacji i relacje.
author: baanders
ms.author: baanders
ms.date: 10/21/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 558e03e698d184aa9b5914f7d494ea61b5a6b18e
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97616936"
---
# <a name="manage-digital-twins"></a>Zarządzanie usługą Digital Twins

Jednostki w Twoim środowisku są reprezentowane przez [Digital bliźniaczych reprezentacji](concepts-twins-graph.md). Zarządzanie cyfrowym bliźniaczych reprezentacji może obejmować tworzenie, modyfikowanie i usuwanie. Aby wykonać te operacje, można użyć [**interfejsów API DigitalTwins**](/rest/api/digital-twins/dataplane/twins), [zestawu SDK platformy .NET (C#)](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true)lub [interfejsu wiersza polecenia usługi Azure Digital bliźniaczych reprezentacji](how-to-use-cli.md).

Ten artykuł koncentruje się na zarządzaniu cyfrowym bliźniaczych reprezentacji; aby współpracować z relacjami i [wykresem bliźniaczym](concepts-twins-graph.md) jako całość, zobacz [*How to: Manage The bliźniaczy Graph with Relationships*](how-to-manage-graph.md).

> [!TIP]
> Wszystkie funkcje zestawu SDK są w wersji synchronicznej i asynchronicznej.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

## <a name="ways-to-manage-twins"></a>Sposoby zarządzania usługą bliźniaczych reprezentacji

[!INCLUDE [digital-twins-ways-to-manage.md](../../includes/digital-twins-ways-to-manage.md)]

## <a name="create-a-digital-twin"></a>Tworzenie dwucyfrowej dwuosiowej

Aby utworzyć dwuosiowy, należy użyć `CreateOrReplaceDigitalTwinAsync()` metody na kliencie usługi w następujący sposób:

```csharp
await client.CreateOrReplaceDigitalTwinAsync<BasicDigitalTwin>("myTwinId", initData);
```

Aby utworzyć dwucyfrowe sznurki, należy podać:
* Żądany identyfikator dwuosiowego
* [Model](concepts-models.md) , który ma być używany

Opcjonalnie można podać wartości początkowe dla wszystkich właściwości dwucyfrowej dwuosiowej. Właściwości są traktowane jako opcjonalne i można je ustawić w późniejszym czasie, ale **nie będą wyświetlane jako część sznura, dopóki nie zostaną ustawione.**

>[!NOTE]
>Gdy nie trzeba inicjować właściwości bliźniaczych, wszystkie [składniki](concepts-models.md#elements-of-a-model) **na przędze** muszą być ustawione podczas tworzenia sznurka. Mogą być pustymi obiektami, ale same składniki muszą istnieć.

Model i wszystkie początkowe wartości właściwości są udostępniane za pomocą `initData` parametru, który jest CIĄGIEM JSON zawierającym odpowiednie dane. Aby uzyskać więcej informacji na temat tworzenia struktury tego obiektu, przejdź do następnej sekcji.

> [!TIP]
> Po utworzeniu lub zaktualizowaniu sznurka może wystąpić opóźnienie do 10 sekund, po upływie którego zmiany zostaną odzwierciedlone w [zapytaniach](how-to-query-graph.md). `GetDigitalTwin`Interfejs API (opisany [w dalszej części tego artykułu](#get-data-for-a-digital-twin)) nie występuje w tym opóźnieniu, dlatego jeśli potrzebujesz natychmiastowej odpowiedzi, użyj wywołania interfejsu API zamiast zapytania, aby zobaczyć nowo utworzone bliźniaczych reprezentacji. 

### <a name="initialize-model-and-properties"></a>Zainicjuj model i właściwości

Możesz inicjować właściwości sznurka w momencie utworzenia sznurka. 

Interfejs API tworzenia przędzy akceptuje obiekt, który jest serializowany do prawidłowego opisu JSON właściwości przędzy. Zobacz [*pojęcia: Digital bliźniaczych reprezentacji i wykres bliźniaczy*](concepts-twins-graph.md) dla opisu formatu JSON dla sznurka. 

Najpierw można utworzyć obiekt danych do reprezentowania sznurka i jego danych właściwości. Można utworzyć obiekt parametru ręcznie lub przy użyciu dostarczonej klasy pomocnika. Oto przykład każdego z nich.

#### <a name="create-twins-using-manually-created-data"></a>Tworzenie bliźniaczych reprezentacji przy użyciu ręcznie utworzonych danych

Bez użycia żadnych niestandardowych klas pomocniczych, można reprezentować właściwości przędzy w `Dictionary<string, object>` , gdzie `string` jest nazwą właściwości i `object` jest obiektem reprezentującym Właściwość i jego wartość.

[!INCLUDE [Azure Digital Twins code: create twin](../../includes/digital-twins-code-create-twin.md)]

#### <a name="create-twins-with-the-helper-class"></a>Utwórz bliźniaczych reprezentacji z klasą pomocnika

Klasa pomocnika `BasicDigitalTwin` pozwala na bezpośrednie przechowywanie pól właściwości w obiekcie "bliźniaczy". Możesz nadal chcieć utworzyć listę właściwości przy użyciu `Dictionary<string, object>` , które można następnie dodać do obiektu przędzy jako jego `CustomProperties` bezpośredniego.

```csharp
BasicDigitalTwin twin = new BasicDigitalTwin();
twin.Metadata = new DigitalTwinMetadata();
twin.Metadata.ModelId = "dtmi:example:Room;1";
// Initialize properties
Dictionary<string, object> props = new Dictionary<string, object>();
props.Add("Temperature", 25.0);
props.Add("Humidity", 50.0);
twin.Contents = props;

client.CreateOrReplaceDigitalTwinAsync<BasicDigitalTwin>("myRoomId", twin);
Console.WriteLine("The twin is created successfully");
```

>[!NOTE]
> `BasicDigitalTwin` obiekty są dołączone do `Id` pola. To pole można pozostawić puste, ale jeśli dodasz wartość identyfikatora, musi ona odpowiadać parametrowi identyfikatora przesłanemu do `CreateOrReplaceDigitalTwinAsync()` wywołania. Przykład:
>
>```csharp
>twin.Id = "myRoomId";
>```

## <a name="get-data-for-a-digital-twin"></a>Pobieranie danych dla dwuosiowej sieci

Możesz uzyskać dostęp do szczegółów dowolnych cyfr cyfrowych poprzez wywołanie `GetDigitalTwin()` metody podobnej do:

```csharp
object result = await client.GetDigitalTwin(id);
```
To wywołanie zwraca dane z sznurka jako typ obiektu o jednoznacznie określonym typie, taki jak `BasicDigitalTwin` . `BasicDigitalTwin` jest klasą pomocniczą serializacji zawartą w zestawie SDK, która zwróci podstawowe metadane i właściwości w formie wstępnie przeanalizowanej. Oto przykład sposobu użycia tego do wyświetlania szczegółów bliźniaczych:

```csharp
Response<BasicDigitalTwin> twin = client.GetDigitalTwin("myRoomId");
Console.WriteLine($"Model id: {twin.Metadata.ModelId}");
foreach (string prop in twin.Contents.Keys)
{
  if (twin.Contents.TryGetValue(prop, out object value))
  Console.WriteLine($"Property '{prop}': {value}");
}
```
Podczas pobierania sznurka przy użyciu metody są zwracane tylko właściwości, które zostały ustawione co najmniej raz `GetDigitalTwin()` .

>[!TIP]
>`displayName`Dla sznurka jest częścią metadanych modelu, więc nie będzie pokazywana podczas pobierania danych dla wystąpienia z przędzą. Aby wyświetlić tę wartość, można [pobrać ją z modelu](how-to-manage-model.md#retrieve-models).

Aby pobrać wiele bliźniaczych reprezentacji przy użyciu jednego wywołania interfejsu API, zobacz przykłady interfejsu API zapytań w artykule [*How to: Query The bliźniaczy Graf*](how-to-query-graph.md).

Rozważmy następujący model (zapisany w [języku Digital bliźniaczych reprezentacji Definition Language](https://github.com/Azure/opendigitaltwins-dtdl/tree/master/DTDL)), który definiuje *księżyc*:

```json
{
    "@id": "dtmi:example:Moon;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2",
    "contents": [
        {
            "@type": "Property",
            "name": "radius",
            "schema": "double",
            "writable": true
        },
        {
            "@type": "Property",
            "name": "mass",
            "schema": "double",
            "writable": true
        }
    ]
}
```
Wynik wywołania `object result = await client.GetDigitalTwinAsync("my-moon");` na sznurze typu *księżyca* może wyglądać następująco:

```json
{
  "$dtId": "myMoon-001",
  "$etag": "W/\"e59ce8f5-03c0-4356-aea9-249ecbdc07f9\"",
  "radius": 1737.1,
  "mass": 0.0734,
  "$metadata": {
    "$model": "dtmi:example:Moon;1",
    "radius": {
      "desiredValue": 1737.1,
      "desiredVersion": 5,
      "ackVersion": 4,
      "ackCode": 200,
      "ackDescription": "OK"
    },
    "mass": {
      "desiredValue": 0.0734,
      "desiredVersion": 8,
      "ackVersion": 8,
      "ackCode": 200,
      "ackDescription": "OK"
    }
  }
}
```

Zdefiniowane właściwości dwucyfrowej dwuosiowej są zwracane jako właściwości najwyższego poziomu na dwuosiowej cyfrowej. Metadane lub informacje o systemie, które nie są częścią definicji DTDL, są zwracane z `$` prefiksem. Właściwości metadanych obejmują:
* Identyfikator wielocyfrowej dwuosiowej w tym wystąpieniu usługi Azure Digital bliźniaczych reprezentacji `$dtId` .
* `$etag`Standardowe pole HTTP przypisane przez serwer sieci Web.
* Inne właściwości w `$metadata` sekcji. Są one następujące:
    - DTMI modelu dwuosiowy cyfrowo.
    - Stan synchronizacji dla każdej właściwości zapisywalnej. Jest to najbardziej przydatne w przypadku urządzeń, w których możliwe jest, że usługa i urządzenie mają rozbieżność stanu (na przykład gdy urządzenie jest w trybie offline). Obecnie ta właściwość dotyczy tylko urządzeń fizycznych podłączonych do IoT Hub. Za pomocą danych w sekcji metadanych można zrozumieć pełny stan właściwości, a także sygnaturę czasową ostatniej modyfikacji. Aby uzyskać więcej informacji na temat stanu synchronizacji, zobacz [ten IoT Hub samouczek](../iot-hub/tutorial-device-twins.md) dotyczący synchronizowania stanu urządzenia.
    - Metadane dotyczące usługi, takie jak IoT Hub lub Azure Digital bliźniaczych reprezentacji. 

Więcej informacji na temat klas pomocnika serializacji, takich `BasicDigitalTwin` jak [*: korzystanie z interfejsów API i zestawów SDK usługi Azure Digital bliźniaczych reprezentacji*](how-to-use-apis-sdks.md).

## <a name="view-all-digital-twins"></a>Wyświetl wszystkie bliźniaczych reprezentacji cyfrowe

Aby wyświetlić wszystkie bliźniaczych reprezentacji cyfrowe w wystąpieniu, użyj [zapytania](how-to-query-graph.md). Zapytanie można uruchomić za pomocą [interfejsów API zapytań](/rest/api/digital-twins/dataplane/query) lub [poleceń interfejsu wiersza polecenia](how-to-use-cli.md).

Poniżej znajduje się treść zapytania podstawowego, które zwróci listę wszystkich bliźniaczych reprezentacji cyfrowych w wystąpieniu:

```sql
SELECT *
FROM DIGITALTWINS
``` 

## <a name="update-a-digital-twin"></a>Aktualizowanie wieloosiowej cyfrowej

Aby zaktualizować właściwości dwucyfrowego sznurka, należy napisać informacje, które mają zostać zamienione w formacie [poprawek JSON](http://jsonpatch.com/) . W ten sposób można zastąpić wiele właściwości jednocześnie. Następnie można przekazać dokument poprawki JSON do `UpdateDigitalTwin()` metody:

```csharp
await client.UpdateDigitalTwin(id, patch);
```

Wywołanie patch może zaktualizować dowolną liczbę właściwości w pojedynczej bliźniaczyej formie (nawet wszystkie). Jeśli trzeba zaktualizować właściwości w wielu bliźniaczych reprezentacji, konieczne będzie oddzielne wywołanie aktualizacji dla każdej z nich.

> [!TIP]
> Po utworzeniu lub zaktualizowaniu sznurka może wystąpić opóźnienie do 10 sekund, po upływie którego zmiany zostaną odzwierciedlone w [zapytaniach](how-to-query-graph.md). `GetDigitalTwin`Interfejs API (opisany [wcześniej w tym artykule](#get-data-for-a-digital-twin)) nie występuje w tym opóźnieniu, dlatego użyj wywołania interfejsu API zamiast zapytania, aby zobaczyć nowo zaktualizowane bliźniaczych reprezentacji, jeśli potrzebujesz natychmiastowej odpowiedzi. 

Oto przykład kodu poprawki JSON. Ten dokument zastępuje wartości właściwości *masy* i *promienia* dwuosiowy, do którego jest stosowana.

```json
[
  {
    "op": "replace",
    "path": "/mass",
    "value": 0.0799
  },
  {
    "op": "replace",
    "path": "/radius",
    "value": 0.800
  }
]
```
Poprawki można tworzyć przy użyciu `JsonPatchDocument` [zestawu SDK](how-to-use-apis-sdks.md)programu. Oto przykład:

```csharp
var updateTwinData = new JsonPatchDocument();
updateTwinData.AppendAddOp("/Temperature", temperature.Value<double>());
await client.UpdateDigitalTwinAsync(twin_Id, updateTwinData);
```

### <a name="update-properties-in-digital-twin-components"></a>Aktualizowanie właściwości w składnikach Digital bliźniaczy

Odwołaj, że model może zawierać składniki, umożliwiając jego składanie z innych modeli. 

Aby zastosować poprawki do właściwości w składnikach Digital bliźniaczy, można użyć składni ścieżki w ramach poprawki JSON:

```json
[
  {
    "op": "replace",
    "path": "/mycomponentname/mass",
    "value": 0.0799
  }
]
```

### <a name="update-a-digital-twins-model"></a>Aktualizowanie modelu przędzy cyfrowej

`UpdateDigitalTwin()`Funkcja może również służyć do migrowania dwucyfrowego przędzy do innego modelu. 

Rozważmy na przykład następujący dokument poprawki JSON, który zastępuje pole metadanych cyfrowej przędzy `$model` :

```json
[
  {
    "op": "replace",
    "path": "/$metadata/$model",
    "value": "dtmi:example:foo;1"
  }
]
```

Ta operacja powiedzie się tylko wtedy, gdy cyfra cyfrowa modyfikowana przez poprawkę jest zgodna z nowym modelem. 

Rozpatrzmy następujący przykład:
1. Wyobraź sobie, że jest to dwucyfrowy dwuosiowy modelem *foo_old*. *foo_old* definiuje wymaganą *masę* właściwości.
2. Nowy model *foo_new* definiuje masę właściwości i dodaje nową wymaganą *temperaturę* właściwości.
3. Po zastosowaniu poprawki cyfrowe sznurki muszą mieć zarówno Właściwość masy, jak i temperatury. 

W tej sytuacji poprawka musi zaktualizować zarówno model, jak i Właściwość temperatury przędzy, jak to:

```json
[
  {
    "op": "replace",
    "path": "/$metadata/$model",
    "value": "dtmi:example:foo_new;1"
  },
  {
    "op": "add",
    "path": "/temperature",
    "value": 60
  }
]
```

### <a name="handle-conflicting-update-calls"></a>Obsługa sprzecznych wywołań aktualizacji

Usługa Azure Digital bliźniaczych reprezentacji gwarantuje, że wszystkie żądania przychodzące są przetwarzane jeden po drugim. Oznacza to, że nawet jeśli wiele funkcji próbuje zaktualizować tę samą właściwość na sznurze w tym samym czasie, **nie ma potrzeby** pisania jawnego kodu blokowania w celu obsłużenia konfliktu.

To zachowanie jest zależne od jednej pojedynczej. 

Przykładowo Wyobraź sobie scenariusz, w którym te trzy wywołania docierają w tym samym czasie: 
*   Zapisz Właściwość A w *Twin1*
*   Zapis właściwości B w *Twin1*
*   Zapisz Właściwość A w *Twin2*

Dwa wywołania, które modyfikują *Twin1* są wykonywane jeden po drugim, a dla każdej zmiany są generowane komunikaty o zmianach. Wywołanie modyfikacji *Twin2* może być wykonywane współbieżnie bez konfliktu, zaraz po nadejściu.

## <a name="delete-a-digital-twin"></a>Usuń dwuosiową cyfrę

Bliźniaczych reprezentacji można usunąć za pomocą `DeleteDigitalTwin()` metody. Można jednak usunąć sznurek tylko wtedy, gdy nie ma więcej relacji. Należy więc najpierw usunąć relacje przychodzące i wychodzące.

Oto przykład kodu do usunięcia bliźniaczych reprezentacji i ich relacji:

```csharp
static async Task DeleteTwin(string id)
{
    await FindAndDeleteOutgoingRelationshipsAsync(id);
    await FindAndDeleteIncomingRelationshipsAsync(id);
    try
    {
        await client.DeleteDigitalTwin(id);
    } catch (RequestFailedException exc)
    {
        Console.WriteLine($"*** Error:{exc.Error}/{exc.Message}");
    }
}

public async Task FindAndDeleteOutgoingRelationshipsAsync(string dtId)
{
    // Find the relationships for the twin

    try
    {
        // GetRelationshipsAsync will throw an error if a problem occurs
        AsyncPageable<BasicRelationship> rels = client.GetRelationshipsAsync<BasicRelationship>(dtId);

        await foreach (BasicRelationship rel in rels)
        {
            await client.DeleteRelationshipAsync(dtId, rel.Id).ConfigureAwait(false);
            Log.Ok($"Deleted relationship {rel.Id} from {dtId}");
        }
    }
    catch (RequestFailedException ex)
    {
        Log.Error($"**_ Error {ex.Status}/{ex.ErrorCode} retrieving or deleting relationships for {dtId} due to {ex.Message}");
    }
}

async Task FindAndDeleteIncomingRelationshipsAsync(string dtId)
{
    // Find the relationships for the twin

    try
    {
        // GetRelationshipsAsync will throw an error if a problem occurs
        AsyncPageable<IncomingRelationship> incomingRels = client.GetIncomingRelationshipsAsync(dtId);

        await foreach (IncomingRelationship incomingRel in incomingRels)
        {
            await client.DeleteRelationshipAsync(incomingRel.SourceId, incomingRel.RelationshipId).ConfigureAwait(false);
            Log.Ok($"Deleted incoming relationship {incomingRel.RelationshipId} from {dtId}");
        }
    }
    catch (RequestFailedException ex)
    {
        Log.Error($"_*_ Error {ex.Status}/{ex.ErrorCode} retrieving or deleting incoming relationships for {dtId} due to {ex.Message}");
    }
}
```
### <a name="delete-all-digital-twins"></a>Usuń wszystkie bliźniaczych reprezentacji cyfrowe

Aby zapoznać się z przykładem sposobu usuwania wszystkich bliźniaczych reprezentacji jednocześnie, należy pobrać przykładową aplikację używaną w [_Tutorial: Poznaj podstawy z przykładową aplikacją kliencką *](tutorial-command-line-app.md). Plik *CommandLoop.cs* wykonuje tę `CommandDeleteAllTwins()` funkcję w funkcji.

## <a name="runnable-digital-twin-code-sample"></a>Przykład kodu Digital bliźniaczy możliwy do uruchomienia

Możesz użyć poniższego przykładu kodu możliwy do uruchomienia, aby utworzyć dwuosiowy, zaktualizować jego szczegóły i usunąć sznurek. 

### <a name="set-up-the-runnable-sample"></a>Konfigurowanie przykładu możliwy do uruchomienia

Fragment kodu używa [Room.jsw](https://github.com/Azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Room.json) definicji modelu z [*samouczka: Eksplorowanie usługi Azure Digital bliźniaczych reprezentacji za pomocą przykładowej aplikacji klienckiej*](tutorial-command-line-app.md). Możesz użyć tego linku, aby przejść bezpośrednio do pliku lub pobrać go w ramach pełnego przykładowego [projektu.](/samples/azure-samples/digital-twins-samples/digital-twins-samples/)

Przed uruchomieniem przykładu należy wykonać następujące czynności:
1. Pobierz plik modelu, umieść go w projekcie i Zastąp `<path-to>` symbol zastępczy w kodzie poniżej, aby poinformować program, gdzie go znaleźć.
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
            string adtInstanceUrl = "https://<your-instance-hostname>";
            var credentials = new DefaultAzureCredential();
            DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credentials);
            Console.WriteLine($"Service client created – ready to go");
            Console.WriteLine();

            //Upload models
            Console.WriteLine($"Upload a model");
            Console.WriteLine();
            string dtdl = File.ReadAllText("<path-to>/Room.json");
            var typeList = new List<string>();
            typeList.Add(dtdl);
            // Upload the model to the service
            await client.CreateModelsAsync(typeList);

            //Create new digital twin
            BasicDigitalTwin twin = new BasicDigitalTwin();
            string twin_Id = "myRoomId";
            twin.Metadata = new DigitalTwinMetadata();
            twin.Metadata.ModelId = "dtmi:example:Room;1";
            // Initialize properties
            Dictionary<string, object> props = new Dictionary<string, object>();
            props.Add("Temperature", 35.0);
            props.Add("Humidity", 55.0);
            twin.Contents = props;
            await client.CreateOrReplaceDigitalTwinAsync<BasicDigitalTwin>(twin_Id, twin);
            Console.WriteLine("Twin created successfully");
            Console.WriteLine();

            //Print twin
            Console.WriteLine("--- Printing twin details:");
            twin = FetchAndPrintTwin(twin_Id, client);
            Console.WriteLine("--------");
            Console.WriteLine();

            //Update twin data
            var updateTwinData = new JsonPatchDocument();
            updateTwinData.AppendAdd("/Temperature", 25.0);
            await client.UpdateDigitalTwinAsync(twin_Id, updateTwinData);
            Console.WriteLine("Twin properties updated");
            Console.WriteLine();

            //Print twin again
            Console.WriteLine("--- Printing twin details (after update):");
            FetchAndPrintTwin(twin_Id, client);
            Console.WriteLine("--------");
            Console.WriteLine();

            //Delete twin
            await DeleteTwin(client, twin_Id);
        }

        private static BasicDigitalTwin FetchAndPrintTwin(string twin_Id, DigitalTwinsClient client)
        {
            BasicDigitalTwin twin;
            Response<BasicDigitalTwin> twin = client.GetDigitalTwin(twin_Id);
            Console.WriteLine($"Model id: {twin.Metadata.ModelId}");
            foreach (string prop in twin.Contents.Keys)
            {
                if (twin.Contents.TryGetValue(prop, out object value))
                    Console.WriteLine($"Property '{prop}': {value}");
            }

            return twin;
        }
        private static async Task DeleteTwin(DigitalTwinsClient client, string id)
        {
            await FindAndDeleteOutgoingRelationshipsAsync(client, id);
            await FindAndDeleteIncomingRelationshipsAsync(client, id);
            try
            {
                await client.DeleteDigitalTwinAsync(id);
                Console.WriteLine("Twin deleted successfully");
            }
            catch (RequestFailedException exc)
            {
                Console.WriteLine($"*** Error:{exc.Message}");
            }
        }

        private static async Task FindAndDeleteOutgoingRelationshipsAsync(DigitalTwinsClient client, string dtId)
        {
            // Find the relationships for the twin

            try
            {
                // GetRelationshipsAsync will throw an error if a problem occurs
                AsyncPageable<BasicRelationship> rels = client.GetRelationshipsAsync<BasicRelationship>(dtId);

                await foreach (BasicRelationship rel in rels)
                {
                    await client.DeleteRelationshipAsync(dtId, rel.Id).ConfigureAwait(false);
                    Console.WriteLine($"Deleted relationship {rel.Id} from {dtId}");
                }
            }
            catch (RequestFailedException ex)
            {
                Console.WriteLine($"**_ Error {ex.Status}/{ex.ErrorCode} retrieving or deleting relationships for {dtId} due to {ex.Message}");
            }
        }

       private static async Task FindAndDeleteIncomingRelationshipsAsync(DigitalTwinsClient client, string dtId)
        {
            // Find the relationships for the twin

            try
            {
                // GetRelationshipsAsync will throw an error if a problem occurs
                AsyncPageable<IncomingRelationship> incomingRels = client.GetIncomingRelationshipsAsync(dtId);

                await foreach (IncomingRelationship incomingRel in incomingRels)
                {
                    await client.DeleteRelationshipAsync(incomingRel.SourceId, incomingRel.RelationshipId).ConfigureAwait(false);
                    Console.WriteLine($"Deleted incoming relationship {incomingRel.RelationshipId} from {dtId}");
                }
            }
            catch (RequestFailedException ex)
            {
                Console.WriteLine($"_*_ Error {ex.Status}/{ex.ErrorCode} retrieving or deleting incoming relationships for {dtId} due to {ex.Message}");
            }
        }

    }
}

```
Oto dane wyjściowe konsoli powyższego programu: 

:::image type="content" source="./media/how-to-manage-twin/console-output-manage-twins.png" alt-text="Dane wyjściowe konsoli pokazujące, że dwuosiowa została utworzona, zaktualizowana i usunięta" lightbox="./media/how-to-manage-twin/console-output-manage-twins.png":::

## <a name="next-steps"></a>Następne kroki

Zobacz, jak tworzyć relacje i zarządzać nimi między cyfrowym bliźniaczych reprezentacji: _, [ *jak to zrobić: Zarządzanie wykresem bliźniaczym z relacjami*](how-to-manage-graph.md)