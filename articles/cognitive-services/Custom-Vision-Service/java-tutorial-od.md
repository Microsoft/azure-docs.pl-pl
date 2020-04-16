---
title: 'Szybki start: tworzenie projektu wykrywania obiektów przy użyciu zestawu Custom Vision SDK dla języka Java'
titleSuffix: Azure Cognitive Services
description: Utwórz projekt, dodaj tagi, przekaż obrazy, wyszkol projekt i wykrywaj obiekty przy użyciu zestawu Java SDK.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: areddish
ms.openlocfilehash: 086e67d1058443a6f4afd615f2d2725aaf19a259
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81403821"
---
# <a name="quickstart-create-an-object-detection-project-with-the-custom-vision-sdk-for-java"></a>Szybki start: tworzenie projektu wykrywania obiektów przy użyciu zestawu Custom Vision SDK dla języka Java

W tym artykule pokazano, jak rozpocząć korzystanie z niestandardowego sdk vision w języku Java do tworzenia modelu wykrywania obiektów. Po jego utworzeniu można dodać otagowane regiony, przekazać obrazy, szkolić projekt, uzyskać domyślny adres URL prognozowanego projektu, a następnie użyć punktu końcowego do programistycznego testowania obrazu. Użyj tego przykładu jako szablonu do utworzenia własnej aplikacji języka Java.

## <a name="prerequisites"></a>Wymagania wstępne

- Wybrane środowisko IDE Java
- Zainstalowany zestaw [JDK 7 lub 8](https://aka.ms/azure-jdks).
- [Maven](https://maven.apache.org/) zainstalowany
- [!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="get-the-custom-vision-sdk-and-sample-code"></a>Pobierz zestaw Custom Vision SDK i przykładowy kod

Do napisania aplikacji języka Java używającej usługi Custom Vision potrzebne są pakiety Maven usługi Custom Vision. Te pakiety są zawarte w przykładowym projekcie, który pobierzesz, ale możesz uzyskać do nich dostęp indywidualnie tutaj.

SDK Custom Vision można znaleźć w centralnym repozytorium maven:
- [Pakiet SDK do szkolenia](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-training)
- [Pakiet SDK do przewidywania](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-prediction)

Sklonuj lub pobierz projekt [Cognitive Services Java SDK Samples (Przykłady dotyczące zestawu Cognitive Services Java SDK)](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master). Przejdź do folderu **Vision/CustomVision/**.

Ten projekt języka Java tworzy nowy projekt wykrywania obiektów za pomocą usługi Custom Vision, mający nazwę __Sample Java OD Project__ (Przykładowy projekt wykrywania obiektów w języku Java), do którego można uzyskać dostęp za pośrednictwem [witryny internetowej Custom Vision](https://customvision.ai/). Następnie zostaną przesłane obrazy do szkolenia i testowania klasyfikatora. W tym projekcie klasyfikator ma na celu określenie, czy obiekt jest **widelcem** lub **nożyczkami.**

[!INCLUDE [get-keys](includes/get-keys.md)]

Program jest skonfigurowany do odwoływania się do kluczowych danych jako zmiennych środowiskowych. Przejdź do folderu **Vision/CustomVision** i wprowadź następujące polecenia programu PowerShell, aby ustawić zmienne środowiskowe. 

> [!NOTE]
> Jeśli używasz systemu operacyjnego nienawiązanego do systemu Windows, zobacz [Konfigurowanie zmiennych środowiskowych,](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account?tabs=multiservice%2Cwindows#configure-an-environment-variable-for-authentication) aby uzyskać instrukcje.

```powershell
$env:AZURE_CUSTOMVISION_TRAINING_API_KEY ="<your training api key>"
$env:AZURE_CUSTOMVISION_PREDICTION_API_KEY ="<your prediction api key>"
```

## <a name="understand-the-code"></a>Zrozumienie kodu

Załaduj projekt `Vision/CustomVision` w środowisku IDE Java i otwórz plik _CustomVisionSamples.java_. Znajdź **runSample** metody i **ImageClassification_Sample** skomentuj&mdash;ImageClassification_Sample wywołanie metody tej metody wykonuje scenariusz klasyfikacji obrazu, który nie jest omówiony w tym przewodniku. Metoda **ObjectDetection_Sample** implementuje podstawowe funkcje tego przewodnika Szybki start. Przejdź do jej definicji i przejrzyj kod. 

### <a name="create-a-new-custom-vision-service-project"></a>Utwórz nowy projekt Custom Vision Service

Przejdź do bloku kodu, który tworzy klienta szkolenia i projekt wykrywania obiektów. Utworzony projekt będzie widoczny w odwiedzonej wcześniej [witrynie internetowej Custom Vision](https://customvision.ai/). Zobacz [CreateProject](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.training.trainings.createproject?view=azure-java-stable#com_microsoft_azure_cognitiveservices_vision_customvision_training_Trainings_createProject_String_CreateProjectOptionalParameter_) przeciążenia metody, aby określić inne opcje podczas tworzenia projektu (wyjaśnione w [Kompilacji detektora](get-started-build-detector.md) portalu sieci web przewodnik).

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_create_od)]

### <a name="add-tags-to-your-project"></a>Dodanie tagów do projektu

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_tags_od)]

### <a name="upload-and-tag-images"></a>Przekazywanie i Tagi obrazów

Po oznaczeniu obrazów w projektach wykrywania obiektów należy określić region każdego z otagowanych obiektów przy użyciu znormalizowanych współrzędnych. Przejdź do elementu Map o nazwie `regionMap`. Ten kod kojarzy poszczególne przykładowe obrazy z ich otagowanymi regionami.

> [!NOTE]
> Jeśli nie masz narzędzia "kliknij i przeciągnij", aby oznaczyć współrzędne regionów, możesz użyć internetowego interfejsu użytkownika w [Customvision.ai](https://www.customvision.ai/). W tym przykładzie współrzędne są już dostarczone.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_od_mapping)]

Następnie przejdź do bloku kodu, który dodaje obrazy do projektu. Obrazy są odczytywane z folderu projektu **src/main/resources** oraz są przekazywane do usługi z odpowiednimi tagami i współrzędnymi regionów.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_upload_od)]

Poprzedni fragment kodu korzysta z dwóch funkcji pomocnika, które pobierają obrazy jako strumienie zasobów i przekazać je do usługi (można przekazać maksymalnie 64 obrazów w jednej partii).

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_helpers)]

### <a name="train-the-project-and-publish"></a>Trenuj projekt i publikuj

Ten kod tworzy pierwszą iterację modelu przewidywania, a następnie publikuje tę iterację do punktu końcowego przewidywania. Nazwa nadana do publikowanej iteracji może służyć do wysyłania żądań przewidywania. Iteracja nie jest dostępna w punkcie końcowym przewidywania, dopóki nie zostanie opublikowana.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_train_od)]

### <a name="use-the-prediction-endpoint"></a>Korzystanie z punktu końcowego przewidywania

Punkt końcowy przewidywania, reprezentowany tutaj przez obiekt `predictor`, jest odwołaniem umożliwiającym przesyłanie obrazu do bieżącego modelu i uzyskiwanie przewidywania klasyfikacji. W tym przykładzie element `predictor` jest zdefiniowany w innym miejscu przy użyciu zmiennej środowiskowej klucza przewidywania.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_prediction_od)]

## <a name="run-the-application"></a>Uruchamianie aplikacji

Aby skompilować i uruchomić rozwiązanie za pomocą maven, przejdź do katalogu projektu **(Vision/CustomVision)** w wierszu polecenia i wykonaj polecenie run:

```bash
mvn compile exec:java
```

Wyświetl dane wyjściowe konsoli, aby uzyskać wyniki rejestrowania i przewidywania. Możesz następnie sprawdzić, czy obraz testowy został odpowiednio otagowany i czy region wykrywania jest poprawny.

[!INCLUDE [clean-od-project](includes/clean-od-project.md)]

## <a name="next-steps"></a>Następne kroki

Teraz zobaczysz, jak każdy krok procesu wykrywania obiektów można wykonać w kodzie. W tym przykładzie wykonuje jedną iterację szkolenia, ale często trzeba trenować i testować model wiele razy, aby uczynić go bardziej dokładne. Poniższy przewodnik szkoleniowy dotyczy klasyfikacji obrazów, ale jego zasady są podobne do wykrywania obiektów.

> [!div class="nextstepaction"]
> [Testowanie i ponowne szkolenie modelu](test-your-model.md)
