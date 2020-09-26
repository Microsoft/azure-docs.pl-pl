---
title: 'Przykład: korzystanie z funkcji dużej skali'
titleSuffix: Azure Cognitive Services
description: Ten przewodnik to artykuł dotyczący sposobu skalowania w górę od istniejącej osoby i FaceList obiektów do obiektów LargePersonGroup i LargeFaceList.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 05/01/2019
ms.author: sbowles
ms.custom: devx-track-csharp
ms.openlocfilehash: 5341c2613624c6a52f1649dcd8a64b6746b84f67
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91332393"
---
# <a name="example-use-the-large-scale-feature"></a>Przykład: korzystanie z funkcji dużej skali

Ten przewodnik to zaawansowany artykuł dotyczący sposobu skalowania w górę od istniejącej osoby i FaceList obiektów do obiektów LargePersonGroup i LargeFaceList. Ten przewodnik demonstruje proces migracji. Założono podstawową znajomość z obiektami Person i FaceList, operacją [uczenia](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4) i funkcjami rozpoznawania. Aby dowiedzieć się więcej o tych tematach, zobacz Przewodnik dotyczący pojęć dotyczących [rozpoznawania](../concepts/face-recognition.md) .

LargePersonGroup i LargeFaceList są określane zbiorczo jako operacje na dużą skalę. LargePersonGroup może zawierać do 1 000 000 osób, z których każdy ma maksymalnie 248 twarzy. LargeFaceList może zawierać do 1 000 000 twarzy. Operacje na dużą skalę są podobne do konwencjonalnych osób i FaceList, ale mają pewne różnice ze względu na nową architekturę. 

Przykłady są zapisywane w języku C# przy użyciu biblioteki klienckiej usługi Azure Cognitive Services

> [!NOTE]
> Aby włączyć wydajność wyszukiwania czołowego na potrzeby identyfikacji i FindSimilar w dużej skali, należy wprowadzić operację uczenia w celu wstępnego przetworzenia LargeFaceList i LargePersonGroup. Czas uczenia różni się od sekund do około pół godziny na podstawie rzeczywistej wydajności. W trakcie okresu szkoleniowego możliwe jest przeprowadzenie identyfikacji i FindSimilar, jeśli zakończono pomyślne szkolenia. Wadą jest to, że nowe dodane osoby i powierzchnie nie pojawiają się w wyniku do momentu ukończenia nowej migracji po przekształceniu na dużą skalę.

## <a name="step-1-initialize-the-client-object"></a>Krok 1. inicjowanie obiektu klienta

W przypadku korzystania z biblioteki klienta programu Front Key i punkt końcowy subskrypcji są przesyłane za pomocą konstruktora klasy FaceClient. Na przykład:

```csharp
string SubscriptionKey = "<Subscription Key>";
// Use your own subscription endpoint corresponding to the subscription key.
string SubscriptionEndpoint = "https://westus.api.cognitive.microsoft.com";
private readonly IFaceClient faceClient = new FaceClient(
            new ApiKeyServiceClientCredentials(subscriptionKey),
            new System.Net.Http.DelegatingHandler[] { });
faceClient.Endpoint = SubscriptionEndpoint
```

Aby uzyskać klucz subskrypcji do odpowiadającego mu punktu końcowego, przejdź do witryny Azure Marketplace z poziomu Azure Portal.
Aby uzyskać więcej informacji, zobacz [subskrypcje](https://azure.microsoft.com/services/cognitive-services/directory/vision/).

## <a name="step-2-code-migration"></a>Krok 2. Migracja kodu

Ta sekcja koncentruje się na sposobie migrowania implementacji osoby lub FaceList do LargePersonGroup lub LargeFaceList. Chociaż LargePersonGroup lub LargeFaceList różni się od osoby lub FaceList w projekcie i wewnętrznej implementacji, interfejsy interfejsu API są podobne do zgodności z poprzednimi wersjami.

Migracja danych nie jest obsługiwana. Zamiast tego utworzysz ponownie LargePersonGroup lub LargeFaceList.

### <a name="migrate-a-persongroup-to-a-largepersongroup"></a>Migrowanie elementu osoby do LargePersonGroup

Migracja z LargePersonGroup jest prosta. Współużytkują one dokładnie te same operacje na poziomie grupy.

W przypadku implementacji osoby lub osoby odnoszącej się do osób konieczna jest zmiana tylko ścieżek interfejsu API lub klasy/modułu zestawu SDK na LargePersonGroup i LargePersonGroup.

Dodaj wszystkie powierzchnie i osoby z tej osoby do nowego LargePersonGroup. Aby uzyskać więcej informacji, zobacz [Dodawanie](how-to-add-faces.md)twarzy.

### <a name="migrate-a-facelist-to-a-largefacelist"></a>Migrowanie elementu FaceList do LargeFaceList

| Interfejsy API listy FaceList | Interfejsy API listy LargeFaceList |
|:---:|:---:|
| Utwórz | Utwórz |
| Usuń | Usuń |
| Get | Get |
| Lista | Lista |
| Aktualizacja | Aktualizacja |
| - | Szkolenie |
| - | Pobieranie stanu szkolenia |

W powyższej tabeli znajduje się porównanie z operacji na poziomie listy dla kolekcji FaceList i LargeFaceList. Jak widać, LargeFaceList zawiera nowe operacje, uczenie i pobieranie stanu szkoleń w porównaniu z FaceList. Szkolenie LargeFaceList jest warunkiem wstępnym operacji [FindSimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) . Szkolenie nie jest wymagane w przypadku FaceList. Poniższy fragment kodu jest funkcją pomocnika, która oczekuje na szkolenie LargeFaceList:

```csharp
/// <summary>
/// Helper function to train LargeFaceList and wait for finish.
/// </summary>
/// <remarks>
/// The time interval can be adjusted considering the following factors:
/// - The training time which depends on the capacity of the LargeFaceList.
/// - The acceptable latency for getting the training status.
/// - The call frequency and cost.
///
/// Estimated training time for LargeFaceList in different scale:
/// -     1,000 faces cost about  1 to  2 seconds.
/// -    10,000 faces cost about  5 to 10 seconds.
/// -   100,000 faces cost about  1 to  2 minutes.
/// - 1,000,000 faces cost about 10 to 30 minutes.
/// </remarks>
/// <param name="largeFaceListId">The Id of the LargeFaceList for training.</param>
/// <param name="timeIntervalInMilliseconds">The time interval for getting training status in milliseconds.</param>
/// <returns>A task of waiting for LargeFaceList training finish.</returns>
private static async Task TrainLargeFaceList(
    string largeFaceListId,
    int timeIntervalInMilliseconds = 1000)
{
    // Trigger a train call.
    await FaceClient.LargeTrainLargeFaceListAsync(largeFaceListId);

    // Wait for training finish.
    while (true)
    {
        Task.Delay(timeIntervalInMilliseconds).Wait();
        var status = await faceClient.LargeFaceList.TrainAsync(largeFaceListId);

        if (status.Status == Status.Running)
        {
            continue;
        }
        else if (status.Status == Status.Succeeded)
        {
            break;
        }
        else
        {
            throw new Exception("The train operation is failed!");
        }
    }
}
```

Wcześniej typowe użycie FaceList z dodanymi twarzami i FindSimilar wyglądały następująco:

```csharp
// Create a FaceList.
const string FaceListId = "myfacelistid_001";
const string FaceListName = "MyFaceListDisplayName";
const string ImageDir = @"/path/to/FaceList/images";
faceClient.FaceList.CreateAsync(FaceListId, FaceListName).Wait();

// Add Faces to the FaceList.
Parallel.ForEach(
    Directory.GetFiles(ImageDir, "*.jpg"),
    async imagePath =>
        {
            using (Stream stream = File.OpenRead(imagePath))
            {
                await faceClient.FaceList.AddFaceFromStreamAsync(FaceListId, stream);
            }
        });

// Perform FindSimilar.
const string QueryImagePath = @"/path/to/query/image";
var results = new List<SimilarPersistedFace[]>();
using (Stream stream = File.OpenRead(QueryImagePath))
{
    var faces = faceClient.Face.DetectWithStreamAsync(stream).Result;
    foreach (var face in faces)
    {
        results.Add(await faceClient.Face.FindSimilarAsync(face.FaceId, FaceListId, 20));
    }
}
```

Podczas migrowania go do LargeFaceList:

```csharp
// Create a LargeFaceList.
const string LargeFaceListId = "mylargefacelistid_001";
const string LargeFaceListName = "MyLargeFaceListDisplayName";
const string ImageDir = @"/path/to/FaceList/images";
faceClient.LargeFaceList.CreateAsync(LargeFaceListId, LargeFaceListName).Wait();

// Add Faces to the LargeFaceList.
Parallel.ForEach(
    Directory.GetFiles(ImageDir, "*.jpg"),
    async imagePath =>
        {
            using (Stream stream = File.OpenRead(imagePath))
            {
                await faceClient.LargeFaceList.AddFaceFromStreamAsync(LargeFaceListId, stream);
            }
        });

// Train() is newly added operation for LargeFaceList.
// Must call it before FindSimilarAsync() to ensure the newly added faces searchable.
await TrainLargeFaceList(LargeFaceListId);

// Perform FindSimilar.
const string QueryImagePath = @"/path/to/query/image";
var results = new List<SimilarPersistedFace[]>();
using (Stream stream = File.OpenRead(QueryImagePath))
{
    var faces = faceClient.Face.DetectWithStreamAsync(stream).Result;
    foreach (var face in faces)
    {
        results.Add(await faceClient.Face.FindSimilarAsync(face.FaceId, largeFaceListId: LargeFaceListId));
    }
}
```

Jak pokazano wcześniej, zarządzanie danymi i część FindSimilar są niemal takie same. Jedyny wyjątek polega na tym, że operacja potoku przed przetworzeniem wstępnego przetwarzania musi zostać zakończona w LargeFaceList przed wykonaniem FindSimilar.

## <a name="step-3-train-suggestions"></a>Krok 3. szkolenie dotyczące uczenia

Mimo że operacja uczenia przyspiesza [FindSimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) i [identyfikację](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), to czas uczenia się, szczególnie gdy jest przychodzący do dużej skali. Szacowany czas uczenia w różnych skalach jest wymieniony w poniższej tabeli.

| Skalowanie dla twarzy lub osób | Szacowany czas uczenia |
|:---:|:---:|
| 1000 | 1-2 s |
| 10 000 | 5-10 s |
| 100 000 | 1-2 min |
| 1 000 000 | 10-30 min |

Aby lepiej wykorzystać funkcję dużego skalowania, zalecamy zastosowanie następujących strategii.

### <a name="step-31-customize-time-interval"></a>Krok 3,1: Dostosowywanie interwału czasu

Jak pokazano w `TrainLargeFaceList()` , istnieje interwał czasu w milisekundach, w którym można opóźnić nieskończony proces sprawdzania stanu szkolenia. W przypadku listy LargeFaceList z większą liczbą twarzy użycie dłuższego interwału zmniejsza liczby wywołań i koszt. Dostosuj przedział czasu zgodnie z oczekiwaną pojemnością LargeFaceList.

Ta sama strategia ma zastosowanie również do LargePersonGroup. Na przykład w przypadku uczenia LargePersonGroup z 1 000 000 osobami `timeIntervalInMilliseconds` może być 60 000, czyli 1-minutowy interwał.

### <a name="step-32-small-scale-buffer"></a>Krok 3,2: bufor małych skali

Osoby lub powierzchnie w LargePersonGroup lub LargeFaceList są przeszukiwane tylko po przeszkoleniu. W scenariuszu dynamicznym nowe osoby lub powierzchnie są ciągle dodawane i muszą być natychmiast przeszukiwane, ale szkolenie może trwać dłużej niż to konieczne. 

Aby wyeliminować ten problem, należy użyć bardzo małego LargePersonGroup lub LargeFaceList jako buforu tylko dla nowo dodanych wpisów. Ten bufor trwa krótszy czas uczenia się z powodu mniejszego rozmiaru. Możliwość natychmiastowego wyszukiwania w tym tymczasowym buforze powinna być zadziałała. Ten bufor jest używany w połączeniu z uczeniem na głównym LargePersonGroup lub LargeFaceList przez uruchomienie szkolenia głównego na podstawie interwału rozrzedzenia. Przykłady są w połowie nocne i codziennie.

Przykładowy przepływ pracy:

1. Utwórz wzorzec LargePersonGroup lub LargeFaceList, który jest kolekcją główną. Utwórz bufor LargePersonGroup lub LargeFaceList, który jest kolekcją buforów. Kolekcja buforów jest tylko dla nowo dodanych osób lub twarzy.
1. Dodaj nowe osoby lub powierzchnie do kolekcji głównej i kolekcji buforów.
1. Przeszkol kolekcję buforów tylko z krótkim interwałem czasu, aby upewnić się, że nowo dodane wpisy zaczęły obowiązywać.
1. Identyfikatory wywołań lub FindSimilar względem kolekcji głównej i kolekcji buforów. Scal wyniki.
1. Gdy rozmiar kolekcji buforów zwiększy się do progu lub czasu bezczynności systemu, Utwórz nową kolekcję buforów. Wyzwól operację uczenia w kolekcji głównej.
1. Usuń starą kolekcję buforów po zakończeniu operacji uczenia w kolekcji głównej.

### <a name="step-33-standalone-training"></a>Krok 3,3: szkolenie autonomiczne

Jeśli akceptowalne jest stosunkowo długie opóźnienie, nie trzeba wyzwolić operacji uczenia bezpośrednio po dodaniu nowych danych. W zamian operację szkolenia można oddzielić od logiki głównej i wyzwalać ją regularnie. Ta strategia jest odpowiednia dla scenariuszy dynamicznych z akceptowalnym opóźnieniem. Można go zastosować do scenariuszy statycznych, aby bardziej ograniczyć częstotliwość pociągów.

Załóżmy, że istnieje `TrainLargePersonGroup` funkcja podobna do `TrainLargeFaceList` . Typowa implementacja szkolenia samodzielnego na LargePersonGroup przez wywołanie [`Timer`](https://msdn.microsoft.com/library/system.timers.timer(v=vs.110).aspx) klasy w `System.Timers` jest:

```csharp
private static void Main()
{
    // Create a LargePersonGroup.
    const string LargePersonGroupId = "mylargepersongroupid_001";
    const string LargePersonGroupName = "MyLargePersonGroupDisplayName";
    faceClient.LargePersonGroup.CreateAsync(LargePersonGroupId, LargePersonGroupName).Wait();

    // Set up standalone training at regular intervals.
    const int TimeIntervalForStatus = 1000 * 60; // 1-minute interval for getting training status.
    const double TimeIntervalForTrain = 1000 * 60 * 60; // 1-hour interval for training.
    var trainTimer = new Timer(TimeIntervalForTrain);
    trainTimer.Elapsed += (sender, args) => TrainTimerOnElapsed(LargePersonGroupId, TimeIntervalForStatus);
    trainTimer.AutoReset = true;
    trainTimer.Enabled = true;

    // Other operations like creating persons, adding faces, and identification, except for Train.
    // ...
}

private static void TrainTimerOnElapsed(string largePersonGroupId, int timeIntervalInMilliseconds)
{
    TrainLargePersonGroup(largePersonGroupId, timeIntervalInMilliseconds).Wait();
}
```

Aby uzyskać więcej informacji na temat zarządzania danymi i implementacji związanych z identyfikacją, zobacz [Dodawanie](how-to-add-faces.md)twarzy.

## <a name="summary"></a>Podsumowanie

W tym przewodniku przedstawiono sposób migrowania istniejącej osoby lub kodu FaceList, a nie danych do LargePersonGroup lub LargeFaceList:

- LargePersonGroup i LargeFaceList działają podobnie do osób lub FaceList, z tą różnicą, że operacja uczenia jest wymagana przez LargeFaceList.
- Zapoznaj się z odpowiednią strategią uczenia, aby uzyskać dynamiczną aktualizację danych dla zestawów danych na dużą skalę.

## <a name="next-steps"></a>Następne kroki

Postępuj zgodnie z przewodnikiem, aby dowiedzieć się, jak dodać twarzy do osoby lub napisać skrypt, aby wykonać operację identyfikowania w ramach danej osoby.

- [Dodawanie twarzy](how-to-add-faces.md)
- [Przewodnik Szybki Start dotyczący biblioteki klienta](../Quickstarts/client-libraries.md)