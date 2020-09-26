---
title: 'Przykład: Dodawanie twarzy do danej osoby'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku pokazano, jak dodać dużą liczbę osób i twarzy do obiektu osoby tworzącej usługę Azure Cognitive Services twarzy.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 04/10/2019
ms.author: sbowles
ms.custom: devx-track-csharp
ms.openlocfilehash: 3643fad1c9e821a78df6d78edeede2341ec79ea8
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91303765"
---
# <a name="add-faces-to-a-persongroup"></a>Dodawanie twarzy do osoby

W tym przewodniku pokazano, jak dodać dużą liczbę osób i twarzy do obiektu. Ta sama strategia dotyczy również obiektów LargePersonGroup, FaceList i LargeFaceList. Ten przykład jest zapisywana w języku C# przy użyciu biblioteki klienckiej .NET usługi Azure Cognitive Services

## <a name="step-1-initialization"></a>Krok 1: Inicjowanie

Poniższy kod deklaruje kilka zmiennych i implementuje funkcję pomocnika, aby zaplanować żądania dodania do niej:

- `PersonCount` jest łączną liczbą osób.
- `CallLimitPerSecond` jest maksymalną liczbą wywołań na sekundę, zgodnie z warstwą subskrypcji.
- `_timeStampQueue` jest kolejką do rejestrowania znaczników czasu żądań.
- `await WaitCallLimitPerSecondAsync()` czeka, aż będzie można wysłać kolejne żądanie.

```csharp
const int PersonCount = 10000;
const int CallLimitPerSecond = 10;
static Queue<DateTime> _timeStampQueue = new Queue<DateTime>(CallLimitPerSecond);

static async Task WaitCallLimitPerSecondAsync()
{
    Monitor.Enter(_timeStampQueue);
    try
    {
        if (_timeStampQueue.Count >= CallLimitPerSecond)
        {
            TimeSpan timeInterval = DateTime.UtcNow - _timeStampQueue.Peek();
            if (timeInterval < TimeSpan.FromSeconds(1))
            {
                await Task.Delay(TimeSpan.FromSeconds(1) - timeInterval);
            }
            _timeStampQueue.Dequeue();
        }
        _timeStampQueue.Enqueue(DateTime.UtcNow);
    }
    finally
    {
        Monitor.Exit(_timeStampQueue);
    }
}
```

## <a name="step-2-authorize-the-api-call"></a>Krok 2: Autoryzowanie wywołania interfejsu API

W przypadku korzystania z biblioteki klienta należy przekazać klucz subskrypcji do konstruktora klasy **FaceClient** . Na przykład:

```csharp
private readonly IFaceClient faceClient = new FaceClient(
    new ApiKeyServiceClientCredentials("<SubscriptionKey>"),
    new System.Net.Http.DelegatingHandler[] { });
```

Aby uzyskać klucz subskrypcji, przejdź do witryny Azure Marketplace z poziomu Azure Portal. Aby uzyskać więcej informacji, zobacz [subskrypcje](https://www.microsoft.com/cognitive-services/sign-up).

## <a name="step-3-create-the-persongroup"></a>Krok 3: Tworzenie obiektu PersonGroup

Tworzony jest obiekt PersonGroup o nazwie „MyPersonGroup” w celu zapisania osób.
Czas żądania jest umieszczany w kolejce `_timeStampQueue` w celu zapewnienia ogólnej walidacji.

```csharp
const string personGroupId = "mypersongroupid";
const string personGroupName = "MyPersonGroup";
_timeStampQueue.Enqueue(DateTime.UtcNow);
await faceClient.LargePersonGroup.CreateAsync(personGroupId, personGroupName);
```

## <a name="step-4-create-the-persons-for-the-persongroup"></a>Krok 4. Tworzenie osób dla osoby

Osoby są tworzone współbieżnie i `await WaitCallLimitPerSecondAsync()` są stosowane również w celu uniknięcia przekroczenia limitu wywołań.

```csharp
Person[] persons = new Person[PersonCount];
Parallel.For(0, PersonCount, async i =>
{
    await WaitCallLimitPerSecondAsync();

    string personName = $"PersonName#{i}";
    persons[i] = await faceClient.PersonGroupPerson.CreateAsync(personGroupId, personName);
});
```

## <a name="step-5-add-faces-to-the-persons"></a>Krok 5: Dodawanie twarzy do osób

Powierzchnie dodawane do różnych osób są przetwarzane współbieżnie. Powierzchnie dodane dla jednej konkretnej osoby są przetwarzane sekwencyjnie.
Ponownie `await WaitCallLimitPerSecondAsync()` jest wywoływana w celu zapewnienia, że częstotliwość żądań jest w zakresie ograniczeń.

```csharp
Parallel.For(0, PersonCount, async i =>
{
    Guid personId = persons[i].PersonId;
    string personImageDir = @"/path/to/person/i/images";

    foreach (string imagePath in Directory.GetFiles(personImageDir, "*.jpg"))
    {
        await WaitCallLimitPerSecondAsync();

        using (Stream stream = File.OpenRead(imagePath))
        {
            await faceClient.PersonGroupPerson.AddFaceFromStreamAsync(personGroupId, personId, stream);
        }
    }
});
```

## <a name="summary"></a>Podsumowanie

W tym przewodniku przedstawiono proces tworzenia przez siebie osób z dużą liczbą osób i twarzy. Kilka przypomnień:

- Ta strategia ma zastosowanie również do FaceLists i LargePersonGroups.
- Dodawanie lub usuwanie twarzy do różnych FaceListsów lub osób w LargePersonGroups jest przetwarzanych współbieżnie.
- Dodawanie lub usuwanie twarzy do jednego określonego FaceListu lub osoby w LargePersonGroup jest wykonywane sekwencyjnie.
- Dla uproszczenia, jak obsłużyć potencjalny wyjątek, został pominięty w tym przewodniku. Jeśli chcesz zwiększyć niezawodność, zastosuj odpowiednie zasady ponawiania.

Objaśniono i przedstawiono następujące funkcje:

- Utwórz grup osób wynosi przy użyciu interfejsu API [tworzenia użytkowników](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) .
- Utwórz osoby za pomocą interfejsu API [osoba-tworzenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) .
- Dodaj twarze do osób przy użyciu interfejsu API [dodawania twarzy osoby](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) z danej osoby.

## <a name="related-topics"></a>Powiązane tematy

- [Wykrywanie twarzy na obrazie](HowtoDetectFacesinImage.md)
- [Korzystanie z funkcji na dużą skalę](how-to-use-large-scale.md)
