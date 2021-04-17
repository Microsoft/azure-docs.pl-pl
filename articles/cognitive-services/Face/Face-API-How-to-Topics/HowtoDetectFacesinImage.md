---
title: Wykrywanie twarzy na obrazie — twarz
titleSuffix: Azure Cognitive Services
description: W tym przewodniku pokazano, jak za pomocą wykrywania twarzy wyodrębniać atrybuty, takie jak płeć, wiek lub ułożenie z danego obrazu.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: sbowles
ms.custom: devx-track-csharp
ms.openlocfilehash: 71e98b735b4aa4631d73f8730a48c56a8c7585ab
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107497646"
---
# <a name="get-face-detection-data"></a>Uzyskiwanie danych wykrywania twarzy

W tym przewodniku pokazano, jak za pomocą wykrywania twarzy wyodrębniać atrybuty, takie jak płeć, wiek lub ułożenie z danego obrazu. Fragmenty kodu w tym przewodniku są napisane w języku C# przy użyciu Azure Cognitive Services klienta rozpoznawania twarzy. Te same funkcje są dostępne za pośrednictwem interfejsu [API REST.](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)

W tym przewodniku pokazano, jak:

- Pobierz lokalizacje i wymiary twarzy na obrazie.
- Pobierz lokalizacje różnych punktów charakterystycznych twarzy, takich jak źrenicy, nos i uste, na obrazie.
- Zgadnij płeć, wiek, emocje i inne atrybuty wykrytej twarzy.

## <a name="setup"></a>Konfiguracja

W tym przewodniku przyjęto założenie, że obiekt [FaceClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient) o nazwie został już skonstruowany z kluczem subskrypcji rozpoznawania `faceClient` twarzy i adresem URL punktu końcowego. W tym miejscu możesz użyć funkcji wykrywania twarzy, wywołując funkcję [DetectWithUrlAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync), która jest używana w tym przewodniku, lub [detectWithStreamAsync.](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync) Aby uzyskać instrukcje dotyczące sposobu skonfigurowania tej funkcji, postępuj zgodnie z jednym z przewodników Szybki start.

Ten przewodnik koncentruje się na specyfiki wywołania Detect, takich jak argumenty, które można przekazać, i co można zrobić z zwróconych danych. Zalecamy, aby zapytanie dotyczyło tylko potrzebnych funkcji. Każda operacja zajmuje dodatkowy czas.

## <a name="get-basic-face-data"></a>Uzyskiwanie podstawowych danych twarzy

Aby znaleźć twarze i uzyskać ich lokalizacje na obrazie, wywołaj metodę [DetectWithUrlAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync) lub [DetectWithStreamAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync) z parametrem _returnFaceId_ ustawionym na **wartość true**. Jest to ustawienie domyślne.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="basic1":::

Możesz odpytać zwrócone obiekty [DetectedFace](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface) o ich unikatowe identyfikatory i prostokąt, który zapewnia współrzędne pikseli twarzy.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="basic2":::

Aby uzyskać informacje na temat analizowania lokalizacji i wymiarów twarzy, zobacz [FaceRectangle](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.facerectangle). Zazwyczaj ten prostokąt zawiera oczy, nos i usty. Nie trzeba uwzględniać górnej części głowy, głowy i podbędka. Aby użyć prostokąta twarzy w celu przycięcia pełnej głowy lub uzyskania pionowego zdjęcia w połowie, na przykład w przypadku obrazu typu identyfikator zdjęcia, możesz rozwinąć prostokąt w każdym kierunku.

## <a name="get-face-landmarks"></a>Uzyskiwanie punktów orientacyjnych twarzy

[Punkty charakterystyczne](../concepts/face-detection.md#face-landmarks) twarzy to zestaw łatwych do znalezienia punktów na twarzy, takich jak źrenicy lub nos. Aby uzyskać dane dotyczące charakterystycznych obiektów krajobrazu twarzy, ustaw parametr _detectionModel_ na **DetectionModel.Detection01,** a parametr _returnFaceLandmarks_ na **wartość true**.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="landmarks1":::

Poniższy kod pokazuje, jak można pobrać lokalizacje nosów i źrenic:

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="landmarks2":::

Możesz również użyć danych punktów orientacyjnych twarzy, aby dokładnie obliczyć kierunek twarzy. Na przykład można zdefiniować obrót twarzy jako wektor od środka ust do środka oczu. Poniższy kod oblicza ten wektor:

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="direction":::

Gdy znasz kierunek twarzy, możesz obrócić prostokątną ramkę twarzy, aby bardziej prawidłowo wyrównać. Aby przycinać twarze na obrazie, można programowo obrócić obraz, tak aby twarze zawsze były wyświetlane w górę.

## <a name="get-face-attributes"></a>Uzyskiwanie atrybutów twarzy

Oprócz prostokątów i charakterystycznych obiektów krajobrazu interfejs API wykrywania twarzy może analizować kilka koncepcyjnych atrybutów twarzy. Aby uzyskać pełną listę, zobacz [sekcję Atrybuty twarzy](../concepts/face-detection.md#attributes) w sekcji koncepcyjnej.

Aby analizować atrybuty twarzy, ustaw parametr _detectionModel_ na **DetectionModel.Detection01,** a parametr _returnFaceAttributes_ na listę wartości [FaceAttributeType Enum.](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype)

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="attributes1":::

Następnie pobierz odwołania do zwróconych danych i wykonaj więcej operacji zgodnie z potrzebami.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="attributes2":::

Aby dowiedzieć się więcej na temat każdego z atrybutów, zobacz przewodnik koncepcyjny dotyczący wykrywania [twarzy i](../concepts/face-detection.md) atrybutów.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku opisano sposób korzystania z różnych funkcji wykrywania twarzy. Następnie zintegruj te funkcje z aplikacją, aby dodać dane twarzy od użytkowników.

- [Samouczek: dodawanie użytkowników do usługi rozpoznawania twarzy](../enrollment-overview.md)

## <a name="related-topics"></a>Powiązane tematy

- [Dokumentacja referencyjna (REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [Dokumentacja referencyjna (.NET SDK)](/dotnet/api/overview/azure/cognitiveservices/client/faceapi)
