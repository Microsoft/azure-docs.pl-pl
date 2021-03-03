---
title: Migrowanie danych czołowych między subskrypcjami — głowa
titleSuffix: Azure Cognitive Services
description: W tym przewodniku pokazano, jak migrować przechowywane dane ze swojej firmy z jednej subskrypcji czołowej do innej.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: nitinme
ms.custom: devx-track-csharp
ms.openlocfilehash: c8d3c5b10c670e7aa4f1fd00f47ef47e772416cc
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101706864"
---
# <a name="migrate-your-face-data-to-a-different-face-subscription"></a>Migrowanie danych własnych do innej subskrypcji programu Marketo

W tym przewodniku pokazano, jak przenosić dane twarzy, takie jak zapisany obiekt z obiektu z twarzy, do innej subskrypcji platformy Azure Cognitive Services. Aby przenieść dane, użyj funkcji Snapshot. W ten sposób można uniknąć wielokrotnego kompilowania i uczenia obiektu osoby lub FaceList podczas przenoszenia lub rozszerzania operacji. Może to być na przykład, że utworzono obiekt z bezpłatną subskrypcją i teraz chcesz przeprowadzić migrację go do subskrypcji płatnej. Lub może być konieczne zsynchronizowanie danych czołowych w różnych regionach w ramach dużej operacji przedsiębiorstwa.

Ta sama Strategia migracji dotyczy również obiektów LargePersonGroup i LargeFaceList. Jeśli nie znasz koncepcji z tego przewodnika, zobacz ich definicje w przewodniku dotyczą [pojęć dotyczących rozpoznawania](../concepts/face-recognition.md) . W tym przewodniku jest stosowana Biblioteka kliencka .NET programu Front with C#.

## <a name="prerequisites"></a>Wymagania wstępne

Potrzebne są następujące elementy:

- Dwa klucze subskrypcji — jeden z istniejącymi danymi i jeden do migracji. Aby subskrybować usługę kroju i uzyskać klucz, postępuj zgodnie z instrukcjami w temacie [Tworzenie konta Cognitive Services](../../cognitive-services-apis-create-account.md).
- Ciąg identyfikatora subskrypcji tarczy, który odpowiada subskrypcji docelowej. Aby go znaleźć, wybierz pozycję **Przegląd** w Azure Portal. 
- Dowolna wersja programu [Visual Studio 2015 lub 2017](https://www.visualstudio.com/downloads/).

## <a name="create-the-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

W tym przewodniku zastosowano prostą aplikację konsolową do uruchomienia migracji danych programu. Aby uzyskać pełną implementację, zobacz [przykład Snapshot](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample) Retail w witrynie GitHub.

1. W programie Visual Studio Utwórz nową aplikację konsolową .NET Framework projektu. Nadaj mu nazwę **FaceApiSnapshotSample**.
1. Pobierz wymagane pakiety NuGet. Kliknij prawym przyciskiem myszy projekt w Eksplorator rozwiązań i wybierz polecenie **Zarządzaj pakietami NuGet**. Wybierz kartę **Przeglądaj** , a następnie wybierz pozycję **Uwzględnij wersję wstępną**. Znajdź i zainstaluj następujący pakiet:
    - [Microsoft. Azure. CognitiveServices. Vision. 2.3.0 — wersja zapoznawcza](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview)

## <a name="create-face-clients"></a>Tworzenie klientów z czołową

W metodzie **Main** w programie *program.cs* Utwórz dwa wystąpienia [FaceClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient) dla subskrypcji źródłowej i docelowej. W tym przykładzie w regionie Azja Wschodnia jako źródło i zachodnie stany USA jako element docelowy jest stosowana subskrypcja czołowa. W tym przykładzie pokazano, jak migrować dane z jednego regionu świadczenia usługi Azure do innego. 

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

```csharp
var FaceClientEastAsia = new FaceClient(new ApiKeyServiceClientCredentials("<East Asia Subscription Key>"))
    {
        Endpoint = "https://southeastasia.api.cognitive.microsoft.com/>"
    };

var FaceClientWestUS = new FaceClient(new ApiKeyServiceClientCredentials("<West US Subscription Key>"))
    {
        Endpoint = "https://westus.api.cognitive.microsoft.com/"
    };
```

Wypełnij wartości klucza subskrypcji i adresy URL punktów końcowych dla subskrypcji źródłowej i docelowej.


## <a name="prepare-a-persongroup-for-migration"></a>Przygotowywanie osoby do migracji

Potrzebujesz identyfikatora osoby w Twojej subskrypcji źródłowej, aby przeprowadzić migrację do subskrypcji docelowej. Użyj metody [PersonGroupOperationsExtensions. ListAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperationsextensions.listasync) , aby pobrać listę obiektów osoby. Następnie Pobierz Właściwość [Persons. PersonGroupId](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.persongroup.persongroupid#Microsoft_Azure_CognitiveServices_Vision_Face_Models_PersonGroup_PersonGroupId) . Proces ten wygląda różnie w zależności od tego, jakie obiekty są dostępne. W tym przewodniku identyfikator źródła osoby źródłowej jest przechowywany w `personGroupId` .

> [!NOTE]
> [Przykładowy kod](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample) tworzy i pociąga za niego nową osobę do migracji. W większości przypadków powinna istnieć już osoba, która ma być używana.

## <a name="take-a-snapshot-of-a-persongroup"></a>Utwórz migawkę osoby

Migawka jest tymczasowym magazynem zdalnym dla określonych typów danych czołowych. Działa jako rodzaj schowka do kopiowania danych z jednej subskrypcji do innej. Najpierw należy wykonać migawkę danych w subskrypcji źródłowej. Następnie należy zastosować ją do nowego obiektu danych w subskrypcji docelowej.

Użyj wystąpienia FaceClient subskrypcji źródłowej, aby wykonać migawkę tej osoby. Użyj [TakeAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperationsextensions.takeasync) z identyfikatorem osoby i identyfikatorem subskrypcji docelowej. Jeśli masz wiele subskrypcji docelowych, Dodaj je jako wpisy tablicowe w trzecim parametrze.

```csharp
var takeSnapshotResult = await FaceClientEastAsia.Snapshot.TakeAsync(
    SnapshotObjectType.PersonGroup,
    personGroupId,
    new[] { "<Azure West US Subscription ID>" /* Put other IDs here, if multiple target subscriptions wanted */ });
```

> [!NOTE]
> Proces tworzenia i stosowania migawek nie zakłóca żadnych zwykłych wywołań elementu Source ani Target grup osób wynosi lub FaceLists. Nie należy wykonywać jednoczesnych wywołań, które zmieniają obiekt źródłowy, takich jak [wywołania zarządzania FaceList](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.facelistoperations) lub wywołania [szkolenia z zespołu osób](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperations) , na przykład. Operacja migawki może zostać uruchomiona przed lub po tych operacjach lub może napotkać błędy.

## <a name="retrieve-the-snapshot-id"></a>Pobierz identyfikator migawki

Metoda używana do tworzenia migawek jest asynchroniczna, dlatego należy poczekać na jego zakończenie. Nie można anulować operacji migawek. W tym kodzie `WaitForOperation` Metoda monitoruje wywołanie asynchroniczne. Sprawdza stan co 100 ms. Po zakończeniu operacji Pobierz identyfikator operacji, przeanalizowanie `OperationLocation` pola. 

```csharp
var takeOperationId = Guid.Parse(takeSnapshotResult.OperationLocation.Split('/')[2]);
var operationStatus = await WaitForOperation(FaceClientEastAsia, takeOperationId);
```

Typowa `OperationLocation` wartość wygląda następująco:

```csharp
"/operations/a63a3bdd-a1db-4d05-87b8-dbad6850062a"
```

`WaitForOperation`Metoda pomocnika jest tutaj:

```csharp
/// <summary>
/// Waits for the take/apply operation to complete and returns the final operation status.
/// </summary>
/// <returns>The final operation status.</returns>
private static async Task<OperationStatus> WaitForOperation(IFaceClient client, Guid operationId)
{
    OperationStatus operationStatus = null;
    do
    {
        if (operationStatus != null)
        {
            Thread.Sleep(TimeSpan.FromMilliseconds(100));
        }

        // Get the status of the operation.
        operationStatus = await client.Snapshot.GetOperationStatusAsync(operationId);

        Console.WriteLine($"Operation Status: {operationStatus.Status}");
    }
    while (operationStatus.Status != OperationStatusType.Succeeded
            && operationStatus.Status != OperationStatusType.Failed);

    return operationStatus;
}
```

Po wyświetleniu stanu operacji `Succeeded` Pobierz identyfikator migawki, przenosząc `ResourceLocation` pole zwróconego wystąpienia OperationStatus.

```csharp
var snapshotId = Guid.Parse(operationStatus.ResourceLocation.Split('/')[2]);
```

Typowa `resourceLocation` wartość wygląda następująco:

```csharp
"/snapshots/e58b3f08-1e8b-4165-81df-aa9858f233dc"
```

## <a name="apply-a-snapshot-to-a-target-subscription"></a>Zastosuj migawkę do subskrypcji docelowej

Następnie utwórz nową osobę w subskrypcji docelowej przy użyciu losowo wygenerowanego identyfikatora. Następnie użyj wystąpienia FaceClient subskrypcji docelowej, aby zastosować migawkę do tej osoby. Przekaż identyfikator migawki i nowy identyfikator osoby.

```csharp
var newPersonGroupId = Guid.NewGuid().ToString();
var applySnapshotResult = await FaceClientWestUS.Snapshot.ApplyAsync(snapshotId, newPersonGroupId);
```


> [!NOTE]
> Obiekt migawki jest prawidłowy tylko przez 48 godzin. Utwórz migawkę tylko wtedy, gdy zamierzasz używać jej do migracji danych wkrótce.

Żądanie zastosowania migawki zwraca inny identyfikator operacji. Aby uzyskać ten identyfikator, Przeanalizuj `OperationLocation` pole zwróconego wystąpienia applySnapshotResult. 

```csharp
var applyOperationId = Guid.Parse(applySnapshotResult.OperationLocation.Split('/')[2]);
```

Proces aplikacji Snapshot jest również asynchroniczny, dlatego należy ponownie użyć, `WaitForOperation` aby poczekać na zakończenie.

```csharp
operationStatus = await WaitForOperation(FaceClientWestUS, applyOperationId);
```

## <a name="test-the-data-migration"></a>Testowanie migracji danych

Po zastosowaniu migawki nowa osoba w ramach subskrypcji docelowej wypełnia pierwotne dane. Domyślnie są również kopiowane wyniki szkolenia. Nowy użytkownik jest gotowy do identyfikacji tożsamości, bez konieczności ponownego szkolenia.

Aby przetestować migrację danych, uruchom następujące operacje i Porównaj wyniki, które drukują w konsoli:

```csharp
await DisplayPersonGroup(FaceClientEastAsia, personGroupId);
await IdentifyInPersonGroup(FaceClientEastAsia, personGroupId);

await DisplayPersonGroup(FaceClientWestUS, newPersonGroupId);
// No need to retrain the person group before identification,
// training results are copied by snapshot as well.
await IdentifyInPersonGroup(FaceClientWestUS, newPersonGroupId);
```

Użyj następujących metod pomocnika:

```csharp
private static async Task DisplayPersonGroup(IFaceClient client, string personGroupId)
{
    var personGroup = await client.PersonGroup.GetAsync(personGroupId);
    Console.WriteLine("Person Group:");
    Console.WriteLine(JsonConvert.SerializeObject(personGroup));

    // List persons.
    var persons = await client.PersonGroupPerson.ListAsync(personGroupId);

    foreach (var person in persons)
    {
        Console.WriteLine(JsonConvert.SerializeObject(person));
    }

    Console.WriteLine();
}
```

```csharp
private static async Task IdentifyInPersonGroup(IFaceClient client, string personGroupId)
{
    using (var fileStream = new FileStream("data\\PersonGroup\\Daughter\\Daughter1.jpg", FileMode.Open, FileAccess.Read))
    {
        var detectedFaces = await client.Face.DetectWithStreamAsync(fileStream);

        var result = await client.Face.IdentifyAsync(detectedFaces.Select(face => face.FaceId.Value).ToList(), personGroupId);
        Console.WriteLine("Test identify against PersonGroup");
        Console.WriteLine(JsonConvert.SerializeObject(result));
        Console.WriteLine();
    }
}
```

Teraz możesz użyć nowej osoby w subskrypcji docelowej. 

Aby ponownie zaktualizować docelową osobę w przyszłości, Utwórz nową osobę, która ma otrzymać migawkę. Aby to zrobić, wykonaj kroki opisane w tym przewodniku. Do jednej z obiektów jednej osoby może być zastosowana migawka tylko jeden raz.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po zakończeniu migracji danych czołowych Usuń ręcznie obiekt Snapshot.

```csharp
await FaceClientEastAsia.Snapshot.DeleteAsync(snapshotId);
```

## <a name="next-steps"></a>Następne kroki

Następnie zapoznaj się z dokumentacją dotyczącą interfejsów API, zobacz przykładową aplikację korzystającą z funkcji Snapshot lub wykonaj instrukcje, aby zacząć korzystać z innych operacji interfejsu API wymienionych tutaj:

- [Dokumentacja referencyjna migawek (zestaw SDK dla platformy .NET)](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperations)
- [Przykładowa migawka czołowa](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample)
- [Dodawanie twarzy](how-to-add-faces.md)
- [Wykrywanie twarzy na obrazie](HowtoDetectFacesinImage.md)
