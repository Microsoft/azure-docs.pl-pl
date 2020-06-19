---
title: 'Przykład: Zidentyfikuj twarze na obrazach — twarz'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku pokazano, jak identyfikować nieznane twarze za pomocą obiektów osób, które są tworzone z góry.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 04/10/2019
ms.author: sbowles
ms.openlocfilehash: 248bae81db1bc8cb69bac4618bd7593658336636
ms.sourcegitcommit: 55b2bbbd47809b98c50709256885998af8b7d0c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2020
ms.locfileid: "84986717"
---
# <a name="example-identify-faces-in-images"></a>Przykład: Zidentyfikuj twarze na obrazach

W tym przewodniku pokazano, jak identyfikować nieznane twarze za pomocą obiektów osób, które są tworzone z góry. Przykłady są zapisywane w języku C# przy użyciu biblioteki klienckiej usługi Azure Cognitive Services

## <a name="preparation"></a>Przygotowanie

Ten przykład ilustruje:

- Jak utworzyć osobę. Ten zbiór osób zawiera listę znanych osób.
- Jak przypisać powierzchnie do każdej osoby. Te powierzchnie są używane jako linie bazowe do identyfikowania osób. Zalecamy używanie wyraźnych widoków czołowych twarzy. Przykładem jest identyfikator fotografii. Dobry zestaw zdjęć obejmuje powierzchnie tej samej osoby w różnych ułożeniach, kolorach ubrań lub hairstyles.

Aby przeprowadzić pokaz tego przykładu, Przygotuj:

- Kilka zdjęć twarzy danej osoby. [Pobierz przykładowe zdjęcia](https://github.com/Microsoft/Cognitive-Face-Windows/tree/master/Data) dla Anna, rachunku i Clare.
- Seria zdjęć testowych. Zdjęcia mogą lub nie mogą zawierać twarzy Anna, Bill lub Clare. Są one używane do testowania identyfikacji. Wybierz także przykładowe obrazy z poprzedniego linku.

## <a name="step-1-authorize-the-api-call"></a>Krok 1: Autoryzowanie wywołania interfejsu API

Każde wywołanie do interfejsu API rozpoznawania twarzy wymaga klucza subskrypcji. Ten klucz może być przesłany przez parametr ciągu zapytania lub określony w nagłówku żądania. Aby przekazać klucz subskrypcji za pomocą ciągu zapytania, zapoznaj się z adresem URL żądania w celu [wykrycia](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) wskaźnika na przykład:
```
https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes]
&subscription-key=<Subscription key>
```

Alternatywnie Określ klucz subskrypcji w nagłówku żądania HTTP **OCP-APIM-Subscription-Key: &lt; Key &gt; Subscription**.
W przypadku korzystania z biblioteki klienta klucz subskrypcji jest przenoszona przez Konstruktor klasy FaceClient. Przykład:
 
```csharp 
private readonly IFaceClient faceClient = new FaceClient(
            new ApiKeyServiceClientCredentials("<subscription key>"),
            new System.Net.Http.DelegatingHandler[] { });
```
 
Postępuj zgodnie z tymi instrukcjami, aby uzyskać klucz.

1. Utwórz [konto platformy Azure](https://azure.microsoft.com/free/cognitive-services/). Jeśli masz już taki, możesz przejść do następnego kroku.
2. Utwórz [zasób czołowy](https://portal.azure.com/#create/Microsoft.CognitiveServicesFace) w Azure Portal, aby uzyskać klucz. Upewnij się, że podczas instalacji wybrano warstwę bezpłatna (F0). 
3. Po wdrożeniu zasobów kliknij pozycję **Przejdź do zasobu** , aby zebrać klucz. 

## <a name="step-2-create-the-persongroup"></a>Krok 2: Tworzenie elementu PersonGroup

W tym kroku grupy osób o nazwie "zaprzyjaźnione" zawierają Anna, rachunek i Clare. Dla każdej osoby zarejestrowano kilka twarzy. Powierzchnie muszą zostać wykryte z obrazów. Po wykonaniu tych czynności powinien istnieć element PersonGroup podobny do przedstawionego na ilustracji:

![Przyjaciele](../Images/group.image.1.jpg)

### <a name="step-21-define-people-for-the-persongroup"></a>Krok 2,1: Definiowanie osób dla danej osoby
Osoba jest podstawową jednostką mającą tożsamość. Dla osoby można zarejestrować jedną lub wiele twarzy. Osoba jest kolekcją osób. Każda osoba jest definiowana w obrębie określonej osoby. Identyfikator jest wykonywany względem osoby. Zadaniem jest utworzenie elementu, a następnie utworzenie w nim osób, takich jak Anna, Bill i Clare.

Najpierw utwórz nową osobę, korzystając z interfejsu API [tworzenia użytkowników](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) . Odpowiedni interfejs API biblioteki klienta to metoda CreatePersonGroupAsync klasy FaceClient. Identyfikator grupy określony w celu utworzenia grupy jest unikatowy dla każdej subskrypcji. Możesz również pobrać, zaktualizować lub usunąć grup osób wynosi za pomocą innych interfejsów API innych osób. 

Po zdefiniowaniu grupy można zdefiniować w niej osoby, korzystając z interfejsu API [Tworzenie osoby](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) . Jego odpowiednikiem w bibliotece klienta jest metoda CreatePersonAsync. Po utworzeniu można dodać do każdej osoby.

```csharp 
// Create an empty PersonGroup
string personGroupId = "myfriends";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Friends");
 
// Define Anna
CreatePersonResult friend1 = await faceClient.PersonGroupPerson.CreateAsync(
    // Id of the PersonGroup that the person belonged to
    personGroupId,    
    // Name of the person
    "Anna"            
);
 
// Define Bill and Clare in the same way
```
### <a name="step-22-detect-faces-and-register-them-to-the-correct-person"></a><a name="step2-2"></a>Krok 2,2: wykrywanie twarzy i rejestrowanie ich w poprawnej osobie
Wykrywanie odbywa się przez wysłanie do interfejsu API [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) żądania HTTP „POST” zawierającego w treści plik obrazu. W przypadku korzystania z biblioteki klienta wykrywanie czołowe odbywa się za pomocą jednego z wykrycia. Metody asynchroniczne klasy FaceClient.

Dla każdej wykrytej platformy Wywołaj [osobę z osobną osobą — Dodaj](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) ją, aby dodać ją do właściwej osoby.

Poniższy kod pokazuje sposób wykrywania twarzy na obrazie i dodawania jej do osoby:

```csharp 
// Directory contains image files of Anna
const string friend1ImageDir = @"D:\Pictures\MyFriends\Anna\";
 
foreach (string imagePath in Directory.GetFiles(friend1ImageDir, "*.jpg"))
{
    using (Stream s = File.OpenRead(imagePath))
    {
        // Detect faces in the image and add to Anna
        await faceClient.PersonGroupPerson.AddFaceFromStreamAsync(
            personGroupId, friend1.PersonId, s);
    }
}
// Do the same for Bill and Clare
``` 
Jeśli obraz zawiera więcej niż jedną miarę, zostanie dodana tylko największa wartość. Do osoby możesz dodać inne powierzchnie. Przekaż ciąg w formacie "targetFace = Left, Top, Width, Height" osobie [będącej](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) osobą, Dodaj parametr zapytania targetFace interfejsu API rozpoznawania. Można również użyć opcjonalnego parametru targetFace dla metody AddPersonFaceAsync, aby dodać inne twarzy. Każda Strona dodana do osoby otrzymuje unikatowy identyfikator utrwalonej twarzy. Tego identyfikatora można użyć w ramach osoby, która umożliwia [usuwanie kroju](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523e) i [kroju —](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)od.

## <a name="step-3-train-the-persongroup"></a>Krok 3: Szkolenie elementu PersonGroup

Aby można było przeprowadzić identyfikację przy użyciu tej osoby, należy ją przeszkolić. Po dodaniu lub usunięciu osoby lub w przypadku edytowania zarejestrowanej firmy osoby należy ponownie przeszkolić osobę. Do szkolenia służy interfejs API [PersonGroup – Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249). W przypadku korzystania z biblioteki klienta jest wywoływana metoda TrainPersonGroupAsync:
 
```csharp 
await faceClient.PersonGroup.TrainAsync(personGroupId);
```
 
Szkolenia są procesem asynchronicznym. Nie można jej zakończyć nawet po powrocie metody TrainPersonGroupAsync. Może być konieczne zbadanie stanu szkolenia. Użyj metody, aby uzyskać dostęp do interfejsu API [stanu szkolenia](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247) lub GetPersonGroupTrainingStatusAsync biblioteki klienta. Poniższy kod ilustruje prostą logikę oczekiwania na zakończenie szkolenia w zakresie danej osoby:
 
```csharp 
TrainingStatus trainingStatus = null;
while(true)
{
    trainingStatus = await faceClient.PersonGroup.GetTrainingStatusAsync(personGroupId);
 
    if (trainingStatus.Status != TrainingStatusType.Running)
    {
        break;
    }
 
    await Task.Delay(1000);
} 
``` 

## <a name="step-4-identify-a-face-against-a-defined-persongroup"></a>Krok 4: Identyfikowanie twarzy przy użyciu zdefiniowanego elementu PersonGroup

Gdy usługa twarzy wykonuje identyfikatory, oblicza podobieństwo testu do wszystkich twarzy w grupie. Zwraca najbardziej porównywalne osoby do testowania. Ten proces odbywa się za pomocą interfejsu API rozpoznawania [twarz](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) lub metody IdentifyAsync biblioteki klienta.

Przede wszystkim należy wykryć test, wykonując czynności opisane w poprzednich krokach. Następnie identyfikator kroju jest przenoszona do identyfikatora API identyfikacji jako drugi argument. Jednocześnie można zidentyfikować wiele identyfikatorów. Wynik zawiera wszystkie zidentyfikowane wyniki. Domyślnie proces identyfikacji zwraca tylko jedną osobę, która pasuje do najwyższej jakości testu. Jeśli wolisz, określ opcjonalny parametr maxNumOfCandidatesReturned, aby proces identyfikacji zwracał więcej kandydatów.

Poniższy kod ilustruje proces identyfikacji:

```csharp 
string testImageFile = @"D:\Pictures\test_img1.jpg";

using (Stream s = File.OpenRead(testImageFile))
{
    var faces = await faceClient.Face.DetectWithStreamAsync(s);
    var faceIds = faces.Select(face => face.FaceId.Value).ToArray();
 
    var results = await faceClient.Face.IdentifyAsync(faceIds, personGroupId);
    foreach (var identifyResult in results)
    {
        Console.WriteLine("Result of face: {0}", identifyResult.FaceId);
        if (identifyResult.Candidates.Length == 0)
        {
            Console.WriteLine("No one identified");
        }
        else
        {
            // Get top 1 among all candidates returned
            var candidateId = identifyResult.Candidates[0].PersonId;
            var person = await faceClient.PersonGroupPerson.GetAsync(personGroupId, candidateId);
            Console.WriteLine("Identified as {0}", person.Name);
        }
    }
}
``` 

Po zakończeniu kroków spróbuj zidentyfikować różne twarzy. Sprawdź, czy twarze Anna, Bill lub Clare mogą być prawidłowo identyfikowane zgodnie z obrazami przekazanymi do wykrywania twarzy. Zobacz poniższe przykłady:

![Zidentyfikuj różne twarze](../Images/identificationResult.1.jpg )

## <a name="step-5-request-for-large-scale"></a>Krok 5. żądanie dla dużej skali

Osoba może przechowywać do 10 000 osób w oparciu o poprzednie ograniczenie projektowe.
Aby uzyskać informacje o zastosowaniach obejmujących nawet miliony osób, zobacz [Jak używać funkcji na dużą skalę](how-to-use-large-scale.md).

## <a name="summary"></a>Podsumowanie

W tym przewodniku przedstawiono proces tworzenia i identyfikowania osoby. Objaśniono i przedstawiono następujące funkcje:

- Wykrywaj twarze przy użyciu interfejsu API [wykrywania twarzy](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d) .
- Utwórz grup osób wynosi przy użyciu interfejsu API [tworzenia użytkowników](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) .
- Utwórz osoby za pomocą interfejsu API [osoba-tworzenie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) .
- Nauczenie osoby przy użyciu interfejsu API [uczenia użytkowników](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) .
- Zidentyfikuj nieznane twarzy względem osoby z użyciem interfejsu API rozpoznawania [twarzy](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) .

## <a name="related-topics"></a>Powiązane tematy

- [Pojęcia dotyczące rozpoznawania](../concepts/face-recognition.md)
- [Wykrywanie twarzy na obrazie](HowtoDetectFacesinImage.md)
- [Dodawanie twarzy](how-to-add-faces.md)
- [Korzystanie z funkcji na dużą skalę](how-to-use-large-scale.md)
