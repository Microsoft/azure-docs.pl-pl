---
title: Start Utwórz projekt wykrywania obiektów z zestawem SDK dla C# Custom Vision
titleSuffix: Azure Cognitive Services
description: Tworzenie projektu, dodawanie tagów, przekazywanie obrazów, uczenie projektu i wykrywanie obiektów przy użyciu zestawu .NET SDK z C#.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: d56953932a92eb4d9e39c8fe5f0d709a83ba3eaa
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81404199"
---
# <a name="quickstart-create-an-object-detection-project-with-the-custom-vision-net-sdk"></a>Start Tworzenie projektu wykrywania obiektów przy użyciu zestawu SDK Custom Vision .NET

W tym artykule pokazano, jak rozpocząć pracę z zestawem SDK Custom Vision C# w programie w celu utworzenia modelu wykrywania obiektów. Po jego utworzeniu można dodać otagowane regiony, przekazać obrazy, szkolić projekt, uzyskać domyślny adres URL prognozowanego projektu, a następnie użyć punktu końcowego do programistycznego testowania obrazu. Użyj tego przykładu jako szablonu do tworzenia własnej aplikacji platformy .NET. 

## <a name="prerequisites"></a>Wymagania wstępne

- Dowolna wersja programu [Visual Studio 2015 lub 2017](https://www.visualstudio.com/downloads/)
- [!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="get-the-custom-vision-sdk-and-sample-code"></a>Pobierz zestaw Custom Vision SDK i przykładowy kod

Aby napisać aplikację platformy .NET, która używa Custom Vision, potrzebne będą Custom Vision pakiety NuGet. Te pakiety są zawarte w przykładowym projekcie, który zostanie pobrany, ale możesz uzyskać do nich dostęp osobno.

- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/)
- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/)

Sklonuj lub pobierz projekt [Cognitive Services .NET Samples (Przykłady dotyczące platformy .NET dla usług Cognitive Services)](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples). Przejdź do folderu **CustomVision/ObjectDetection** i otwórz _plik ObjectDetection.csproj_ w programie Visual Studio.

Ten projekt programu Visual Studio tworzy nowy projekt Custom Vision o nazwie __My New Project__, który będzie dostępny za pośrednictwem [witryny internetowej Custom Vision](https://customvision.ai/). Następnie przekazuje obrazy do szkolenia i testowania modelu wykrywania obiektów. W tym projekcie model jest szkolony do wykrywania widelców i nożyczek na obrazach.

[!INCLUDE [get-keys](includes/get-keys.md)]

## <a name="understand-the-code"></a>Zrozumienie kodu

Otwórz plik _Program.cs_ i przejrzyj kod. [Tworzenie zmiennych środowiskowych](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) dla kluczy `CUSTOM_VISION_TRAINING_KEY` `CUSTOM_VISION_PREDICTION_KEY`szkolenia i przewidywania o nazwie i , odpowiednio. Skrypt będzie szukać tych zmiennych.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_keys)]

Ponadto Pobierz adres URL punktu końcowego ze strony Ustawienia w witrynie sieci Web Custom Vision. Zapisz ją w zmiennej środowiskowej o nazwie `CUSTOM_VISION_ENDPOINT`. Skrypt zapisuje odwołanie do niego w katalogu głównym klasy.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_endpoint)]

### <a name="create-a-new-custom-vision-service-project"></a>Utwórz nowy projekt Custom Vision Service

Następny fragment kodu tworzy projekt wykrywania obiektów. Utworzony projekt będzie widoczny w odwiedzonej wcześniej [witrynie internetowej Custom Vision](https://customvision.ai/). Zobacz [CreateProject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.createproject?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_CreateProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_String_System_String_System_Nullable_System_Guid__System_String_System_Collections_Generic_IList_System_String__) metody, aby określić inne opcje podczas tworzenia projektu (wyjaśnione w [Kompilacji detektora](get-started-build-detector.md) portalu sieci web przewodnik).  

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_create)]


### <a name="add-tags-to-the-project"></a>Dodawanie tagów do projektu

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_tags)]

### <a name="upload-and-tag-images"></a>Przekazywanie i Tagi obrazów

Po oznaczeniu obrazów w projektach wykrywania obiektów należy określić region każdego z otagowanych obiektów przy użyciu znormalizowanych współrzędnych. Poniższy kod kojarzy każdy z przykładowych obrazów ze oznaczonym regionem.

> [!NOTE]
> Jeśli nie masz narzędzia "kliknij i przeciągnij", aby oznaczyć współrzędne regionów, możesz użyć internetowego interfejsu użytkownika w [Customvision.ai](https://www.customvision.ai/). W tym przykładzie współrzędne są już dostarczone.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_upload_regions)]

Następnie to mapowanie skojarzeń jest używane, aby przekazać każdy przykładowy obraz z jego współrzędnymi regionu. W jednej partii można przesłać maksymalnie 64 obrazy.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_upload)]

W tym momencie zostały przekazane wszystkie obrazy próbek i otagowane każde z nich (**rozwidlenie** lub **nożyczki**) za pomocą skojarzonego prostokąta pikseli.

### <a name="train-the-project"></a>Uczenie projektu

Ten kod tworzy pierwszą iterację szkoleniową w projekcie.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_train)]

### <a name="publish-the-current-iteration"></a>Opublikuj bieżącą iterację

Nazwa nadana do publikowanej iteracji może służyć do wysyłania żądań przewidywania. Iteracja nie jest dostępna w punkcie końcowym przewidywania do momentu opublikowania.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_publish)]

### <a name="create-a-prediction-endpoint"></a>Tworzenie punktu końcowego przewidywania

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_prediction_endpoint)]

### <a name="use-the-prediction-endpoint"></a>Korzystanie z punktu końcowego przewidywania

Ta część skryptu ładuje obraz testowy, wysyła zapytanie do punktu końcowego modelu i wyprowadza dane prognoz do konsoli.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_prediction)]

## <a name="run-the-application"></a>Uruchamianie aplikacji

Gdy aplikacja jest uruchomiona, należy otworzyć okno konsoli i napisać następujące dane wyjściowe:

```console
Creating new project:
        Training
Done!

Making a prediction:
        fork: 98.2% [ 0.111609578, 0.184719115, 0.6607002, 0.6637112 ]
        scissors: 1.2% [ 0.112389535, 0.119195729, 0.658031344, 0.7023591 ]
```

Następnie można sprawdzić, czy obraz testowy (znaleziony w **obrazach/teście/** ) jest odpowiednio oznakowany i czy region wykrywania jest poprawny. W tym momencie można nacisnąć dowolny klawisz, aby zamknąć aplikację.

[!INCLUDE [clean-od-project](includes/clean-od-project.md)]

## <a name="next-steps"></a>Następne kroki

Teraz już wiesz, jak wykonać każdy krok procesu wykrywania obiektów w kodzie. W tym przykładzie wykonuje jedną iterację szkolenia, ale często trzeba trenować i testować model wiele razy, aby uczynić go bardziej dokładne. Następny przewodnik dotyczy klasyfikacji obrazów. Jej zasady są podobne do wykrywania obiektów.

> [!div class="nextstepaction"]
> [Testowanie i ponowne szkolenie modelu](test-your-model.md)
