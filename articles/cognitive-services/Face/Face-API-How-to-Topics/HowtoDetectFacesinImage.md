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
ms.date: 02/23/2021
ms.author: sbowles
ms.custom: devx-track-csharp
ms.openlocfilehash: 3a15cce45c527a92c99e0488661e0b67bb8e2371
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101713069"
---
# <a name="get-face-detection-data"></a>Pobieranie danych wykrywania kroju

W tym przewodniku przedstawiono sposób korzystania z wykrywania czołowego w celu wyodrębnienia atrybutów, takich jak płeć, wiek lub ułożenie z danego obrazu. Fragmenty kodu w tym przewodniku są zapisywane w języku C# przy użyciu biblioteki klienckiej usługi Azure Cognitive Services Ta sama funkcja jest dostępna za pomocą [interfejsu API REST](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

W tym przewodniku pokazano, jak:

- Pobierz lokalizacje i wymiary twarzy na obrazie.
- Zapoznaj się z lokalizacjami różnych punktów orientacyjnych, na przykład uczniów, nos i jamy ustnej.
- Odgadnięcie płci, wieku, rozpoznawania emocji i innych atrybutów wykrytej czołowej.

## <a name="setup"></a>Konfiguracja

W tym przewodniku przyjęto założenie, że został już skonstruowany obiekt [FaceClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient) o nazwie `faceClient` z kluczem subskrypcji i adresem URL punktu końcowego. W tym miejscu możesz użyć funkcji wykrywania kroju przez wywołanie [DetectWithUrlAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync), która jest używana w tym przewodniku, lub [DetectWithStreamAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync). Aby uzyskać instrukcje dotyczące sposobu konfigurowania tej funkcji, wykonaj jedną z przewodników Szybki Start.

Ten przewodnik koncentruje się na konkretnych wywołaniach wykrywania, takich jak argumenty, które można przekazać, i co można zrobić z zwracanymi danymi. Zalecamy, aby wykonywać zapytania dotyczące tylko potrzebnych funkcji. Każda operacja zajmuje dodatkowy czas.

## <a name="get-basic-face-data"></a>Pobieranie danych podstawowych

Aby znaleźć powierzchnie i uzyskać ich lokalizacje w obrazie, wywołaj metodę [DetectWithUrlAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync) lub [DetectWithStreamAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync) z parametrem _returnFaceId_ ustawioną na **wartość true**. Jest to ustawienie domyślne.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="basic1":::

Można wysyłać zapytania do zwracanych obiektów [DetectedFace](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface) dla ich unikatowych identyfikatorów i prostokąta, który zapewnia współrzędne pikseli powierzchni.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="basic2":::

Aby uzyskać informacje na temat analizowania lokalizacji i wymiarów kroju, zobacz [FaceRectangle](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.facerectangle). Zazwyczaj ten prostokąt zawiera oczy, eyebrows, nos i jamy ustnej. Góra z Ears i Chin nie są uwzględniane. Aby przy użyciu prostokąta czołowego przyciąć kompletną stronę główną lub uzyskać pionowy zrzut, na przykład dla obrazu typu zdjęcia, można rozwinąć prostokąt w każdym kierunku.

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

Aby analizować atrybuty kroju, należy ustawić parametr _detectionModel_ na **detectionModel. Detection01** i parametr _ReturnFaceAttributes_ na listę wartości [wyliczeniowych FaceAttributeType](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype) .

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="attributes1":::

Następnie Pobierz odwołania do zwracanych danych i wykonaj więcej operacji zgodnie z potrzebami.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="attributes2":::

Aby dowiedzieć się więcej na temat każdego z atrybutów, zobacz Przewodnik dotyczący [wykrywania i atrybutów czołowych](../concepts/face-detection.md) .

## <a name="next-steps"></a>Następne kroki

W tym przewodniku przedstawiono sposób użycia różnych funkcji wykrywania czołowego. Następnie Zintegruj te funkcje z aplikacją, postępując zgodnie z szczegółowym samouczkiem.

- [Samouczek: tworzenie aplikacji WPF do wyświetlania danych twarzy na obrazie](../Tutorials/FaceAPIinCSharpTutorial.md)

## <a name="related-topics"></a>Powiązane tematy

- [Dokumentacja referencyjna (REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [Dokumentacja referencyjna (zestaw SDK dla platformy .NET)](/dotnet/api/overview/azure/cognitiveservices/client/faceapi)
