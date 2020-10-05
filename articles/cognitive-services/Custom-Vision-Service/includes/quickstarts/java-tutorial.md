---
author: areddish
ms.custom: devx-track-java
ms.author: areddish
ms.service: cognitive-services
ms.date: 09/15/2020
ms.openlocfilehash: f4b64c25bff93683c79aa1aa3eb556988c798cb0
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "90604996"
---
Ten przewodnik zawiera instrukcje i przykładowy kod ułatwiający rozpoczęcie pracy przy użyciu Custom Vision biblioteki klienta dla języka Java w celu utworzenia modelu klasyfikacji obrazów. Utworzysz projekt, dodasz Tagi, nauczysz projekt, a następnie użyjesz w adresie URL punktu końcowego przewidywania projektu do programistycznego testowania. Użyj tego przykładu jako szablonu do tworzenia własnej aplikacji rozpoznawania obrazu.

> [!NOTE]
> Jeśli chcesz skompilować i przeszkolić model klasyfikacji _bez_ pisania kodu, zamiast tego zapoznaj się ze [wskazówkami w przeglądarce](../../getting-started-build-a-classifier.md) .

## <a name="prerequisites"></a>Wymagania wstępne

- Wybrane środowisko IDE Java
- Zainstalowany zestaw [JDK 7 lub 8](https://aka.ms/azure-jdks).
- [Maven](https://maven.apache.org/) zainstalowano
- [!INCLUDE [create-resources](../../includes/create-resources.md)]

## <a name="get-the-custom-vision-client-library"></a>Pobierz bibliotekę kliencką Custom Vision

Aby napisać aplikację do analizy obrazów przy użyciu Custom Vision dla języka Java, będziesz potrzebować pakietów Custom Vision Maven. Te pakiety są zawarte w przykładowym projekcie, który zostanie pobrany, ale możesz uzyskać do nich dostęp osobno.

Bibliotekę klienta Custom Vision można znaleźć w Maven centralnym repozytorium:

- [Pakiet SDK do szkolenia](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-training)
- [Pakiet SDK do przewidywania](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-prediction)

Sklonuj lub pobierz projekt [Cognitive Services Java SDK Samples (Przykłady dotyczące zestawu Cognitive Services Java SDK)](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master). Przejdź do folderu **Vision/CustomVision/**.

Ten projekt Java za pomocą usługi Custom Vision tworzy nowy projekt klasyfikacji obrazów o nazwie __Sample Java Project__, do którego można uzyskać dostęp za pośrednictwem [witryny internetowej Custom Vision](https://customvision.ai/). Następnie zostaną przesłane obrazy do szkolenia i testowania klasyfikatora. W tym projekcie klasyfikator jest używany do ustalania, czy drzewo jest __choiną__ (Hemlock), czy __wiśnią japońską__ (Japanese Cherry).

[!INCLUDE [get-keys](../../includes/get-keys.md)]

Program jest skonfigurowany do odwoływania się do danych klucza jako zmiennych środowiskowych. Przejdź do folderu **Vision/CustomVision** i wprowadź następujące polecenia programu PowerShell, aby ustawić zmienne środowiskowe. 

> [!NOTE]
> Jeśli używasz systemu operacyjnego innego niż Windows, zobacz [Konfigurowanie zmiennych środowiskowych](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account?tabs=multiservice%2Cwindows#configure-an-environment-variable-for-authentication) , aby uzyskać instrukcje.

```powershell
$env:AZURE_CUSTOMVISION_TRAINING_API_KEY ="<your training api key>"
$env:AZURE_CUSTOMVISION_PREDICTION_API_KEY ="<your prediction api key>"
```

## <a name="examine-the-code"></a>Analizowanie kodu

Załaduj projekt `Vision/CustomVision` w środowisku IDE Java i otwórz plik _CustomVisionSamples.java_. Znajdź metodę **runSample** i Dodaj komentarz do wywołania metody **ObjectDetection_Sample** &mdash; Ta metoda wykonuje scenariusz wykrywania obiektów, który nie jest uwzględniony w tym przewodniku. Metoda **ImageClassification_Sample** implementuje podstawowe funkcje tego przykładu. Przejdź do jej definicji i przejrzyj kod.

## <a name="create-a-custom-vision-project"></a>Tworzenie projektu Custom Vision

Ten pierwszy fragment kodu tworzy projekt klasyfikacji obrazów. Utworzony projekt będzie widoczny w odwiedzonej wcześniej [witrynie internetowej Custom Vision](https://customvision.ai/). Zobacz przeciążania metod tworzenia [projektu](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.training.trainings.createproject?view=azure-java-stable#com_microsoft_azure_cognitiveservices_vision_customvision_training_Trainings_createProject_String_CreateProjectOptionalParameter_) , aby określić inne opcje podczas tworzenia projektu (wyjaśnione w przewodniku po portalu sieci Web [klasyfikatora](../../getting-started-build-a-classifier.md) ).

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_create)]

## <a name="create-tags-in-the-project"></a>Tworzenie tagów w projekcie

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_tags)]

## <a name="upload-and-tag-images"></a>Przekazywanie i Tagi obrazów

Przykładowe obrazy znajdują się w folderze **src/main/resources** projektu. Są one odczytywane z tego miejsca i przekazywane do usługi wraz z odpowiednimi tagami.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_upload)]

W poprzednim fragmencie kodu są używane dwie funkcje pomocnika, które pobierają obrazy jako strumienie zasobów i przekazują je do usługi (w jednej partii można przekazać do 64 obrazów).

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_helpers)]

## <a name="train-and-publish-the-project"></a>Uczenie i publikowanie projektu

Ten kod tworzy pierwszą iterację modelu predykcyjnego, a następnie publikuje tę iterację w punkcie końcowym przewidywania. Nazwa nadana do publikowanej iteracji może służyć do wysyłania żądań przewidywania. Iteracja nie jest dostępna w punkcie końcowym przewidywania do momentu opublikowania.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_train)]

## <a name="use-the-prediction-endpoint"></a>Korzystanie z punktu końcowego przewidywania

Punkt końcowy przewidywania, reprezentowany tutaj przez obiekt `predictor`, jest odwołaniem umożliwiającym przesyłanie obrazu do bieżącego modelu i uzyskiwanie przewidywania klasyfikacji. W tym przykładzie element `predictor` jest zdefiniowany w innym miejscu przy użyciu zmiennej środowiskowej klucza przewidywania.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_predict)]

## <a name="run-the-application"></a>Uruchamianie aplikacji

Aby skompilować i uruchomić rozwiązanie przy użyciu Maven, przejdź do katalogu projektu (**Vision/CustomVision**) w wierszu polecenia i wykonaj polecenie Run:

```bash
mvn compile exec:java
```

Dane wyjściowe aplikacji w konsoli powinny być podobne do poniższego tekstu:

```console
Creating project...
Adding images...
Adding image: hemlock_1.jpg
Adding image: hemlock_2.jpg
Adding image: hemlock_3.jpg
Adding image: hemlock_4.jpg
Adding image: hemlock_5.jpg
Adding image: hemlock_6.jpg
Adding image: hemlock_7.jpg
Adding image: hemlock_8.jpg
Adding image: hemlock_9.jpg
Adding image: hemlock_10.jpg
Adding image: japanese_cherry_1.jpg
Adding image: japanese_cherry_2.jpg
Adding image: japanese_cherry_3.jpg
Adding image: japanese_cherry_4.jpg
Adding image: japanese_cherry_5.jpg
Adding image: japanese_cherry_6.jpg
Adding image: japanese_cherry_7.jpg
Adding image: japanese_cherry_8.jpg
Adding image: japanese_cherry_9.jpg
Adding image: japanese_cherry_10.jpg
Training...
Training status: Training
Training status: Training
Training status: Completed
Done!
        Hemlock: 93.53%
        Japanese Cherry: 0.01%
```

Następnie możesz sprawdzić, czy przewidywanie dla obrazu testowego (kilka ostatnich wierszy danych wyjściowych) jest poprawne.

[!INCLUDE [clean-ic-project](../../includes/clean-ic-project.md)]

## <a name="next-steps"></a>Następne kroki

Teraz dowiesz się, jak każdy krok procesu wykrywania obiektów można wykonać w kodzie. Ten przykład wykonuje pojedynczą iterację szkoleniową, ale często należy przeprowadzić uczenie i testowanie modelu wiele razy, aby zwiększyć jego dokładność.

> [!div class="nextstepaction"]
> [Testowanie i ponowne szkolenie modelu](../../test-your-model.md)

* [Co to jest usługa Custom Vision?](../../overview.md)
* * [Dokumentacja referencyjna zestawu SDK](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/customvision?view=azure-java-stable)