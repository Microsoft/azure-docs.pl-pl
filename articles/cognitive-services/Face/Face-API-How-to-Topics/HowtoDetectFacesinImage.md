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
ms.openlocfilehash: 500099753ee4fe47f02e7f09d9732b71aa3bae36
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91856369"
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

Aby znaleźć powierzchnie i uzyskać ich lokalizacje w obrazie, wywołaj metodę [DetectWithUrlAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync?view=azure-dotnet) lub [DetectWithStreamAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync?view=azure-dotnet) z parametrem _returnFaceId_ ustawioną na **wartość true**. Jest to ustawienie domyślne.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="basic1":::

Można wysyłać zapytania do zwracanych obiektów [DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet) dla ich unikatowych identyfikatorów i prostokąta, który zapewnia współrzędne pikseli powierzchni.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="basic2":::

Aby uzyskać informacje na temat analizowania lokalizacji i wymiarów kroju, zobacz [FaceRectangle](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.facerectangle?view=azure-dotnet). Zazwyczaj ten prostokąt zawiera oczy, eyebrows, nos i jamy ustnej. Góra z Ears i Chin nie są uwzględniane. Aby przy użyciu prostokąta czołowego przyciąć kompletną stronę główną lub uzyskać pionowy zrzut, na przykład dla obrazu typu zdjęcia, można rozwinąć prostokąt w każdym kierunku.

## <a name="get-face-landmarks"></a>Pobierz punkty orientacyjne

[Punkty orientacyjne](../concepts/face-detection.md#face-landmarks) są zestawem łatwych do znalezienia punktów na stronie, takich jak uczniowie lub pozostała część nosa. Aby uzyskać dane punktu orientacyjnego, należy ustawić parametr _detectionModel_ na wartość **detectionModel. Detection01** i parametr _returnFaceLandmarks_ na **true**.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="landmarks1":::

Poniższy kod demonstruje, jak można pobrać lokalizacje nosa i uczniów:

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="landmarks2":::

Można również użyć danych punktów orientacyjnych, aby dokładnie obliczyć kierunek działania. Na przykład można zdefiniować rotację kroju jako wektora z środka usta do środka oczu. Poniższy kod oblicza ten wektor:

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="direction":::

Gdy znasz kierunek działania, możesz obrócić prostokątną ramkę czołową, aby odpowiednio dostosować ją. Aby przyciąć twarze na obrazie, możesz programistycznie obrócić obraz, aby powierzchnie były zawsze wyświetlane w poziomie pionowym.

## <a name="get-face-attributes"></a>Pobieranie atrybutów kroju

Oprócz prostokątów i punktów orientacyjnych interfejs API wykrywania powierzchni może analizować kilka atrybutów pojęciowych powierzchni. Aby zapoznać się z pełną listą, zobacz sekcję dotyczącą pojęć dotyczących [atrybutów](../concepts/face-detection.md#attributes) .

Aby analizować atrybuty kroju, należy ustawić parametr _detectionModel_ na **detectionModel. Detection01** i parametr _ReturnFaceAttributes_ na listę wartości [wyliczeniowych FaceAttributeType](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet) .

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="attributes1":::

Następnie Pobierz odwołania do zwracanych danych i wykonaj więcej operacji zgodnie z potrzebami.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="attributes2":::

Aby dowiedzieć się więcej na temat każdego z atrybutów, zobacz Przewodnik dotyczący [wykrywania i atrybutów czołowych](../concepts/face-detection.md) .

## <a name="next-steps"></a>Następne kroki

W tym przewodniku przedstawiono sposób użycia różnych funkcji wykrywania czołowego. Następnie Zintegruj te funkcje z aplikacją, postępując zgodnie z szczegółowym samouczkiem.

- [Samouczek: tworzenie aplikacji WPF do wyświetlania danych twarzy na obrazie](../Tutorials/FaceAPIinCSharpTutorial.md)

## <a name="related-topics"></a>Powiązane tematy

- [Dokumentacja referencyjna (REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [Dokumentacja referencyjna (zestaw SDK dla platformy .NET)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/faceapi?view=azure-dotnet)
