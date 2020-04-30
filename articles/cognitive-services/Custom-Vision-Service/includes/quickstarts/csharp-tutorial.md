---
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-services
ms.date: 04/14/2020
ms.openlocfilehash: de0c8fd674872a48a302a76a3f0951df13041206
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82134110"
---
Ten artykuł zawiera informacje i przykładowy kod, dzięki którym można łatwiej rozpocząć tworzenie modelu klasyfikacji obrazów za pomocą zestawu Custom Vision SDK i języka C#. Po jej utworzeniu możesz dodać tagi, przesłać obrazy, wyszkolić projekt, uzyskać adres URL punktu końcowego domyślnego przewidywania projektu i użyć punktu końcowego do programowego przetestowania obrazu. Użyj tego przykładu jako szablonu do tworzenia własnej aplikacji platformy .NET. Jeśli chcesz przejść przez proces kompilowania i korzystania z modelu klasyfikacji _bez_ kodu, zamiast tego zapoznaj się ze [wskazówkami w przeglądarce](../../getting-started-build-a-classifier.md) .

## <a name="prerequisites"></a>Wymagania wstępne

- Dowolna wersja programu [Visual Studio 2015 lub 2017](https://www.visualstudio.com/downloads/)
- [!INCLUDE [create-resources](../../includes/create-resources.md)]

## <a name="get-the-custom-vision-sdk-and-sample-code"></a>Pobierz zestaw Custom Vision SDK i przykładowy kod

Aby napisać aplikację platformy .NET, która używa Custom Vision, potrzebne będą Custom Vision pakiety NuGet. Te pakiety są zawarte w przykładowym projekcie, który zostanie pobrany, ale możesz uzyskać do nich dostęp osobno.

- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/)
- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/)

Sklonuj lub pobierz projekt [Cognitive Services .NET Samples (Przykłady dotyczące platformy .NET dla usług Cognitive Services)](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples). Przejdź do folderu **CustomVision/ImageClassification** i otwórz plik _ImageClassification.csproj_ w programie Visual Studio.

Ten projekt programu Visual Studio tworzy nowy projekt Custom Vision o nazwie __My New Project__, który będzie dostępny za pośrednictwem [witryny internetowej Custom Vision](https://customvision.ai/). Następnie zostaną przesłane obrazy do szkolenia i testowania klasyfikatora. W tym projekcie klasyfikator jest używany do ustalania, czy drzewo jest __choiną__ (Hemlock), czy __wiśnią japońską__ (Japanese Cherry).

[!INCLUDE [get-keys](../../includes/get-keys.md)]

## <a name="understand-the-code"></a>Zrozumienie kodu

Otwórz plik _Program.cs_ i przejrzyj kod. [Utwórz zmienne środowiskowe](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) dla kluczy szkoleniowych i prognoz `CUSTOM_VISION_TRAINING_KEY` o `CUSTOM_VISION_PREDICTION_KEY`nazwach i, odpowiednio. Skrypt będzie wyglądał na te zmienne.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_keys)]

Ponadto Pobierz adres URL punktu końcowego ze strony Ustawienia w witrynie sieci Web Custom Vision. Zapisz ją w zmiennej środowiskowej o nazwie `CUSTOM_VISION_ENDPOINT`. Skrypt zapisuje odwołanie do niego w katalogu głównym klasy.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_endpoint)]

Następujące wiersze kodu wykonują podstawową funkcjonalność projektu.

### <a name="create-a-new-custom-vision-service-project"></a>Tworzenie nowego projektu usługi Custom Vision

Utworzony projekt będzie widoczny w odwiedzonej wcześniej [witrynie internetowej Custom Vision](https://customvision.ai/). Aby określić inne opcje podczas tworzenia projektu (wyjaśnione w przewodniku tworzenia portalu sieci Web [klasyfikatora](../../getting-started-build-a-classifier.md) ), zobacz metodę " [setproject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.createproject?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_CreateProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_String_System_String_System_Nullable_System_Guid__System_String_System_Collections_Generic_IList_System_String__) ".   

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_create)]

### <a name="create-tags-in-the-project"></a>Tworzenie tagów w projekcie

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_tags)]

### <a name="upload-and-tag-images"></a>Przekazywanie i Tagi obrazów

Obrazy dla tego projektu są dołączone. Są one przywoływane w metodzie **LoadImagesFromDisk** w pliku _Program.cs_. Można przekazać do 64 obrazów w pojedynczej partii.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_upload)]

### <a name="train-the-classifier-and-publish"></a>Uczenie klasyfikatora i publikowanie

Ten kod tworzy pierwszą iterację modelu predykcyjnego, a następnie publikuje tę iterację w punkcie końcowym przewidywania. Możesz użyć nazwy iteracji, aby wysyłać żądania przewidywania. Iteracja nie jest dostępna w punkcie końcowym przewidywania do momentu opublikowania.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_train)]

### <a name="set-the-prediction-endpoint"></a>Ustawianie punktu końcowego przewidywania

Punkt końcowy przewidywania jest odwołaniem umożliwiającym przesłanie obrazu do bieżącego modelu i uzyskanie przewidywania klasyfikacji.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_prediction_endpoint)]

### <a name="submit-an-image-to-the-default-prediction-endpoint"></a>Przesyłanie obrazu do domyślnego punktu końcowego przewidywania

W tym skrypcie obraz testowy jest ładowany w metodzie **LoadImagesFromDisk**, a dane wyjściowe przewidywania modelu są wyświetlane w konsoli. Wartość `publishedModelName` zmiennej powinna odpowiadać wartości "opublikowany jako" znajdującej się na karcie **wydajność** portalu Custom Vision. 

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_prediction)]

## <a name="run-the-application"></a>Uruchamianie aplikacji

Gdy aplikacja jest uruchomiona, należy otworzyć okno konsoli i napisać następujące dane wyjściowe:

```console
Creating new project:
        Uploading images
        Training
Done!

Making a prediction:
        Hemlock: 95.0%
        Japanese Cherry: 0.0%
```

Następnie możesz sprawdzić, czy obraz testowy (znajdujący się w folderze **Images/Test/**) został odpowiednio otagowany. Naciśnij dowolny klawisz, aby zakończyć działanie aplikacji. Możesz też wrócić do [witryny internetowej Custom Vision](https://customvision.ai) i wyświetlić bieżący stan nowo utworzonego projektu.

[!INCLUDE [clean-ic-project](../../includes/clean-ic-project.md)]

## <a name="next-steps"></a>Następne kroki

Teraz wiesz, jak wykonać każdy krok procesu klasyfikacji obrazu w kodzie. W tym przykładzie jest wykonywana jedna iteracja szkolenia, ale często trzeba szkolić i testować model wiele razy, aby zwiększyć jego dokładność.

> [!div class="nextstepaction"]
> [Testowanie i ponowne szkolenie modelu](../../test-your-model.md)
