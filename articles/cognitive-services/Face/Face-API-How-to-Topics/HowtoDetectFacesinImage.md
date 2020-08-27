---
title: Wykrywaj twarze na obrazie
titleSuffix: Azure Cognitive Services
description: W tym przewodniku przedstawiono sposób korzystania z wykrywania czołowego w celu wyodrębnienia atrybutów, takich jak płeć, wiek lub ułożenie z danego obrazu.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: sbowles
ms.custom: devx-track-csharp
ms.openlocfilehash: 231f30f5532d0934ba41e591aa821d56b11d5856
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/26/2020
ms.locfileid: "88928007"
---
# <a name="get-face-detection-data"></a>Pobieranie danych wykrywania kroju

W tym przewodniku przedstawiono sposób korzystania z wykrywania czołowego w celu wyodrębnienia atrybutów, takich jak płeć, wiek lub ułożenie z danego obrazu. Fragmenty kodu w tym przewodniku są zapisywane w języku C# przy użyciu biblioteki klienckiej usługi Azure Cognitive Services Ta sama funkcja jest dostępna za pomocą [interfejsu API REST](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

W tym przewodniku pokazano, jak:

- Pobierz lokalizacje i wymiary twarzy na obrazie.
- Zapoznaj się z lokalizacjami różnych punktów orientacyjnych, na przykład uczniów, nos i jamy ustnej.
- Odgadnięcie płci, wieku, rozpoznawania emocji i innych atrybutów wykrytej czołowej.

## <a name="setup"></a>Konfigurowanie

W tym przewodniku przyjęto założenie, że został już skonstruowany obiekt [FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) o nazwie `faceClient` z kluczem subskrypcji i adresem URL punktu końcowego. W tym miejscu możesz użyć funkcji wykrywania kroju przez wywołanie [DetectWithUrlAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync?view=azure-dotnet), która jest używana w tym przewodniku, lub [DetectWithStreamAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync?view=azure-dotnet). Aby uzyskać instrukcje dotyczące sposobu konfigurowania tej funkcji, wykonaj jedną z przewodników Szybki Start.

Ten przewodnik koncentruje się na konkretnych wywołaniach wykrywania, takich jak argumenty, które można przekazać, i co można zrobić z zwracanymi danymi. Zalecamy, aby wykonywać zapytania dotyczące tylko potrzebnych funkcji. Każda operacja zajmuje dodatkowy czas.

## <a name="get-basic-face-data"></a>Pobieranie danych podstawowych

Aby znaleźć powierzchnie i uzyskać ich lokalizacje w obrazie, wywołaj metodę z parametrem _returnFaceId_ ustawionym na **wartość true**. Jest to ustawienie domyślne.

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, false, null);
```

Można wysyłać zapytania do zwracanych obiektów [DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet) dla ich unikatowych identyfikatorów i prostokąta, który zapewnia współrzędne pikseli powierzchni.

```csharp
foreach (var face in faces)
{
    string id = face.FaceId.ToString();
    FaceRectangle rect = face.FaceRectangle;
}
```

Aby uzyskać informacje na temat analizowania lokalizacji i wymiarów kroju, zobacz [FaceRectangle](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.facerectangle?view=azure-dotnet). Zazwyczaj ten prostokąt zawiera oczy, eyebrows, nos i jamy ustnej. Góra z Ears i Chin nie są uwzględniane. Aby przy użyciu prostokąta czołowego przyciąć kompletną stronę główną lub uzyskać pionowy zrzut, na przykład dla obrazu typu zdjęcia, można rozwinąć prostokąt w każdym kierunku.

## <a name="get-face-landmarks"></a>Pobierz punkty orientacyjne

[Punkty orientacyjne](../concepts/face-detection.md#face-landmarks) są zestawem łatwych do znalezienia punktów na stronie, takich jak uczniowie lub pozostała część nosa. Aby uzyskać dane punktu orientacyjnego, ustaw _returnFaceLandmarks_ dla parametru returnFaceLandmarks **wartość true**.

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, null);
```

Poniższy kod demonstruje, jak można pobrać lokalizacje nosa i uczniów:

```csharp
foreach (var face in faces)
{
    var landmarks = face.FaceLandmarks;

    double noseX = landmarks.NoseTip.X;
    double noseY = landmarks.NoseTip.Y;

    double leftPupilX = landmarks.PupilLeft.X;
    double leftPupilY = landmarks.PupilLeft.Y;

    double rightPupilX = landmarks.PupilRight.X;
    double rightPupilY = landmarks.PupilRight.Y;
}
```

Można również użyć danych punktów orientacyjnych, aby dokładnie obliczyć kierunek działania. Na przykład można zdefiniować rotację kroju jako wektora z środka usta do środka oczu. Poniższy kod oblicza ten wektor:

```csharp
var upperLipBottom = landmarks.UpperLipBottom;
var underLipTop = landmarks.UnderLipTop;

var centerOfMouth = new Point(
    (upperLipBottom.X + underLipTop.X) / 2,
    (upperLipBottom.Y + underLipTop.Y) / 2);

var eyeLeftInner = landmarks.EyeLeftInner;
var eyeRightInner = landmarks.EyeRightInner;

var centerOfTwoEyes = new Point(
    (eyeLeftInner.X + eyeRightInner.X) / 2,
    (eyeLeftInner.Y + eyeRightInner.Y) / 2);

Vector faceDirection = new Vector(
    centerOfTwoEyes.X - centerOfMouth.X,
    centerOfTwoEyes.Y - centerOfMouth.Y);
```

Gdy znasz kierunek działania, możesz obrócić prostokątną ramkę czołową, aby odpowiednio dostosować ją. Aby przyciąć twarze na obrazie, możesz programistycznie obrócić obraz, aby powierzchnie były zawsze wyświetlane w poziomie pionowym.

## <a name="get-face-attributes"></a>Pobieranie atrybutów kroju

Oprócz prostokątów i punktów orientacyjnych interfejs API wykrywania powierzchni może analizować kilka atrybutów pojęciowych powierzchni. Aby zapoznać się z pełną listą, zobacz sekcję dotyczącą pojęć dotyczących [atrybutów](../concepts/face-detection.md#attributes) .

Aby analizować atrybuty kroju, ustaw parametr _returnFaceAttributes_ na listę wartości [wyliczenia FaceAttributeType](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet) .

```csharp
var requiredFaceAttributes = new FaceAttributeType[] {
    FaceAttributeType.Age,
    FaceAttributeType.Gender,
    FaceAttributeType.Smile,
    FaceAttributeType.FacialHair,
    FaceAttributeType.HeadPose,
    FaceAttributeType.Glasses,
    FaceAttributeType.Emotion
};
var faces = await faceClient.DetectWithUrlAsync(imageUrl, true, false, requiredFaceAttributes);
```

Następnie Pobierz odwołania do zwracanych danych i wykonaj więcej operacji zgodnie z potrzebami.

```csharp
foreach (var face in faces)
{
    var attributes = face.FaceAttributes;
    var age = attributes.Age;
    var gender = attributes.Gender;
    var smile = attributes.Smile;
    var facialHair = attributes.FacialHair;
    var headPose = attributes.HeadPose;
    var glasses = attributes.Glasses;
    var emotion = attributes.Emotion;
}
```

Aby dowiedzieć się więcej na temat każdego z atrybutów, zobacz Przewodnik dotyczący [wykrywania i atrybutów czołowych](../concepts/face-detection.md) .

## <a name="next-steps"></a>Następne kroki

W tym przewodniku przedstawiono sposób użycia różnych funkcji wykrywania czołowego. Następnie Zintegruj te funkcje z aplikacją, postępując zgodnie z szczegółowym samouczkiem.

- [Samouczek: tworzenie aplikacji WPF do wyświetlania danych twarzy na obrazie](../Tutorials/FaceAPIinCSharpTutorial.md)

## <a name="related-topics"></a>Powiązane tematy

- [Dokumentacja referencyjna (REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [Dokumentacja referencyjna (zestaw SDK dla platformy .NET)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/faceapi?view=azure-dotnet)