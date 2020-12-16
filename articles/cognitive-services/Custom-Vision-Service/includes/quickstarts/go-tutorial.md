---
author: areddish
ms.author: areddish
ms.service: cognitive-services
ms.date: 09/15/2020
ms.openlocfilehash: 27eb4383084e9a782e0c2cb8ff734fba8496c9f1
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97582506"
---
Ten przewodnik zawiera instrukcje i przykładowy kod ułatwiający rozpoczęcie pracy przy użyciu biblioteki klienta Custom Vision dla języka go w celu utworzenia modelu klasyfikacji obrazów. Utworzysz projekt, dodasz Tagi, nauczysz projekt, a następnie użyjesz w adresie URL punktu końcowego przewidywania projektu do programistycznego testowania. Użyj tego przykładu jako szablonu do tworzenia własnej aplikacji rozpoznawania obrazu.

> [!NOTE]
> Jeśli chcesz skompilować i przeszkolić model klasyfikacji _bez_ pisania kodu, zamiast tego zapoznaj się ze [wskazówkami w przeglądarce](../../getting-started-build-a-classifier.md) .

## <a name="prerequisites"></a>Wymagania wstępne

- [Go 1.8+](https://golang.org/doc/install)
- [!INCLUDE [create-resources](../../includes/create-resources.md)]

## <a name="install-the-custom-vision-client-library"></a>Zainstaluj bibliotekę kliencką Custom Vision

Aby napisać aplikację do analizy obrazów przy użyciu Custom Vision for go, potrzebna jest Biblioteka klienta usługi Custom Vision. Uruchom następujące polecenie w programie PowerShell:

```shell
go get -u github.com/Azure/azure-sdk-for-go/...
```

lub jeśli używasz `dep` w repozytorium, uruchom:
```shell
dep ensure -add github.com/Azure/azure-sdk-for-go
```

[!INCLUDE [get-keys](../../includes/get-keys.md)]

[!INCLUDE [python-get-images](../../includes/python-get-images.md)]

## <a name="add-the-code"></a>Dodawanie kodu

Utwórz nowy plik o nazwie *sample.go* w preferowanym katalogu projektu.

## <a name="create-the-custom-vision-project"></a>Tworzenie projektu Custom Vision

Dodaj następujący kod do skryptu, aby utworzyć nowy projekt Custom Vision Service. Wstaw klucze subskrypcji w odpowiednich definicjach. Ponadto Pobierz adres URL punktu końcowego ze strony Ustawienia w witrynie sieci Web Custom Vision.

Aby określić inne opcje podczas tworzenia projektu (wyjaśnione w przewodniku tworzenia portalu sieci Web [klasyfikatora](../../getting-started-build-a-classifier.md) ), zobacz metodę " [setproject](/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.training.trainings.createproject?view=azure-java-stable#com_microsoft_azure_cognitiveservices_vision_customvision_training_Trainings_createProject_String_CreateProjectOptionalParameter_) ".

```go
import(
    "context"
    "bytes"
    "fmt"
    "io/ioutil"
    "path"
    "log"
    "time"
    "github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v3.0/customvision/training"
    "github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v3.0/customvision/prediction"
)

var (
    training_key string = "<your training key>"
    prediction_key string = "<your prediction key>"
    prediction_resource_id = "<your prediction resource id>"
    endpoint string = "<your endpoint URL>"
    project_name string = "Go Sample Project"
    iteration_publish_name = "classifyModel"
    sampleDataDirectory = "<path to sample images>"
)

func main() {
    fmt.Println("Creating project...")

    ctx = context.Background()

    trainer := training.New(training_key, endpoint)

    project, err := trainer.CreateProject(ctx, project_name, "sample project", nil, string(training.Multilabel))
    if (err != nil) {
        log.Fatal(err)
    }
```

## <a name="create-tags-in-the-project"></a>Tworzenie tagów w projekcie

Aby utworzyć tagi klasyfikacji dla projektu, dodaj następujący kod na końcu pliku *sample.go*:

```go
// Make two tags in the new project
hemlockTag, _ := trainer.CreateTag(ctx, *project.ID, "Hemlock", "Hemlock tree tag", string(training.Regular))
cherryTag, _ := trainer.CreateTag(ctx, *project.ID, "Japanese Cherry", "Japanese cherry tree tag", string(training.Regular))
```

## <a name="upload-and-tag-images"></a>Przekazywanie i Tagi obrazów

Aby dodać przykładowe obrazy do projektu, po utworzeniu tagów wstaw następujący kod. Ten kod przekazuje każdy obraz z odpowiednim tagiem. Można przekazać do 64 obrazów w pojedynczej partii.

> [!NOTE]
> Należy zmienić ścieżkę do obrazów, zależnie od tego, gdzie został wcześniej pobrany projekt z przykładami dotyczącymi zestawu SDK dla języka Go dla usług Cognitive Services.

```go
fmt.Println("Adding images...")
japaneseCherryImages, err := ioutil.ReadDir(path.Join(sampleDataDirectory, "Japanese Cherry"))
if err != nil {
    fmt.Println("Error finding Sample images")
}

hemLockImages, err := ioutil.ReadDir(path.Join(sampleDataDirectory, "Hemlock"))
if err != nil {
    fmt.Println("Error finding Sample images")
}

for _, file := range hemLockImages {
    imageFile, _ := ioutil.ReadFile(path.Join(sampleDataDirectory, "Hemlock", file.Name()))
    imageData := ioutil.NopCloser(bytes.NewReader(imageFile))

    trainer.CreateImagesFromData(ctx, *project.ID, imageData, []string{ hemlockTag.ID.String() })
}

for _, file := range japaneseCherryImages {
    imageFile, _ := ioutil.ReadFile(path.Join(sampleDataDirectory, "Japanese Cherry", file.Name()))
    imageData := ioutil.NopCloser(bytes.NewReader(imageFile))
    trainer.CreateImagesFromData(ctx, *project.ID, imageData, []string{ cherryTag.ID.String() })
}
```

## <a name="train-and-publish-the-project"></a>Uczenie i publikowanie projektu

Ten kod tworzy pierwszą iterację modelu predykcyjnego, a następnie publikuje tę iterację w punkcie końcowym przewidywania. Nazwa nadana do publikowanej iteracji może służyć do wysyłania żądań przewidywania. Iteracja nie jest dostępna w punkcie końcowym przewidywania do momentu opublikowania.

```go
fmt.Println("Training...")
iteration, _ := trainer.TrainProject(ctx, *project.ID)
for {
    if *iteration.Status != "Training" {
        break
    }
    fmt.Println("Training status: " + *iteration.Status)
    time.Sleep(1 * time.Second)
    iteration, _ = trainer.GetIteration(ctx, *project.ID, *iteration.ID)
}
fmt.Println("Training status: " + *iteration.Status)

trainer.PublishIteration(ctx, *project.ID, *iteration.ID, iteration_publish_name, prediction_resource_id))
```

## <a name="use-the-prediction-endpoint"></a>Korzystanie z punktu końcowego przewidywania

Aby wysłać obraz do punktu końcowego przewidywania i uzyskać przewidywanie, dodaj na końcu pliku następujący kod:

```go
    fmt.Println("Predicting...")
    predictor := prediction.New(prediction_key, endpoint)

    testImageData, _ := ioutil.ReadFile(path.Join(sampleDataDirectory, "Test", "test_image.jpg"))
    results, _ := predictor.ClassifyImage(ctx, *project.ID, iteration_publish_name, ioutil.NopCloser(bytes.NewReader(testImageData)), "")

    for _, prediction := range *results.Predictions    {
        fmt.Printf("\t%s: %.2f%%", *prediction.TagName, *prediction.Probability * 100)
        fmt.Println("")
    }
}
```

## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom plik *sample.go*.

```shell
go run sample.go
```

Dane wyjściowe aplikacji powinny być podobne do poniższego tekstu:

```console
Creating project...
Adding images...
Training...
Training status: Training
Training status: Training
Training status: Training
Training status: Completed
Done!
        Hemlock: 93.53%
        Japanese Cherry: 0.01%
```

Możesz następnie sprawdzić, czy obraz testowy (znajdujący się w folderze **<bazowy_adres_url_obrazów>/Images/Test/**) został odpowiednio otagowany. Możesz też wrócić do [witryny internetowej Custom Vision](https://customvision.ai) i wyświetlić bieżący stan nowo utworzonego projektu.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [clean-ic-project](../../includes/clean-ic-project.md)]

## <a name="next-steps"></a>Następne kroki

Teraz dowiesz się, jak każdy krok procesu wykrywania obiektów można wykonać w kodzie. Ten przykład wykonuje pojedynczą iterację szkoleniową, ale często należy przeprowadzić uczenie i testowanie modelu wiele razy, aby zwiększyć jego dokładność.

> [!div class="nextstepaction"]
> [Testowanie i ponowne szkolenie modelu](../../test-your-model.md)

* [Co to jest usługa Custom Vision?](../../overview.md)
* [Dokumentacja referencyjna zestawu SDK (szkolenie)](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/customvision/training)
* [Dokumentacja referencyjna zestawu SDK (przewidywania)](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.1/customvision/prediction)