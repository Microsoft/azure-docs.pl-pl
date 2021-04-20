---
author: areddish
ms.author: areddish
ms.service: cognitive-services
ms.date: 10/26/2020
ms.custom: devx-track-js
ms.openlocfilehash: 617db5594af682bcdb67101e3317ec184e874d73
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725358"
---
Ten przewodnik zawiera instrukcje i przykładowy kod, które ułatwiają rozpoczynanie pracy z biblioteką Custom Vision klienta programu Node.js tworzenia modelu klasyfikacji obrazów. Utworzysz projekt, dodasz tagi, przeszkolisz projekt i użyjesz adresu URL punktu końcowego przewidywania projektu, aby go przetestować programowo. Użyj tego przykładu jako szablonu do tworzenia własnej aplikacji do rozpoznawania obrazów.

> [!NOTE]
> Jeśli chcesz utworzyć i wytszkolić _model_ klasyfikacji bez pisania kodu, zapoznaj się ze wskazówkami opartymi [na przeglądarce.](../../getting-started-build-a-classifier.md)

Użyj biblioteki Custom Vision klienta dla programu .NET, aby:

* Tworzenie nowego projektu usługi Custom Vision
* Dodawanie tagów do projektu
* Przekazywanie i Tagi obrazów
* Uczenie projektu
* Opublikuj bieżącą iterację
* Testowanie punktu końcowego przewidywania

Dokumentacja [referencyjna (trenowania)](/javascript/api/@azure/cognitiveservices-customvision-training/) [(przewidywanie)](/javascript/api/@azure/cognitiveservices-customvision-prediction/) | Kod źródłowy [biblioteki (trenowania)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-customvision-training) [(przewidywanie) |](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-customvision-prediction) Przykłady pakietu [(npm) (trenowania)](https://www.npmjs.com/package/@azure/cognitiveservices-customvision-training) [(przewidywanie)](https://www.npmjs.com/package/@azure/cognitiveservices-customvision-prediction)  |  [](/samples/browse/?products=azure&terms=custom%20vision&languages=javascript)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure [— tworzenie bezpłatnej subskrypcji](https://azure.microsoft.com/free/cognitive-services/)
* Bieżąca wersja [Node.js](https://nodejs.org/)
* Po utworzeniu subskrypcji platformy Azure utwórz zasób usługi Custom Vision, aby utworzyć zasób usługi Custom Vision w usłudze Azure Portal, aby utworzyć zasób trenowania i przewidywania oraz pobrać klucze i punkt <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesCustomVision"  title=" "  target="_blank"> </a> końcowy. Poczekaj na wdrożenie i kliknij przycisk **Przejdź do** zasobu.
    * Klucz i punkt końcowy będą potrzebne z zasobów, które utworzysz, aby połączyć aplikację z Custom Vision. Klucz i punkt końcowy wkleisz do poniższego kodu w dalszej części tego przewodnika Szybki start.
    * Możesz użyć bezpłatnej warstwy cenowej ( ), aby wypróbować usługę, a następnie uaktualnić ją do warstwy `F0` płatnej w środowisku produkcyjnym.

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-new-nodejs-application"></a>Tworzenie nowej aplikacji Node.js

W oknie konsoli (takim jak cmd, PowerShell lub Bash) utwórz nowy katalog dla aplikacji i przejdź do niego. 

```console
mkdir myapp && cd myapp
```

Uruchom polecenie `npm init` , aby utworzyć aplikację node z `package.json` plikiem . 

```console
npm init
```

### <a name="install-the-client-library"></a>Instalowanie biblioteki klienta

Aby napisać aplikację analizy obrazów z Custom Vision dla Node.js, musisz mieć Custom Vision npm. Aby je zainstalować, uruchom następujące polecenie w programie PowerShell:

```shell
npm install @azure/cognitiveservices-customvision-training
npm install @azure/cognitiveservices-customvision-prediction
```

Plik aplikacji `package.json` zostanie zaktualizowany o zależności.

Utwórz plik o nazwie `index.js` i zaimportuj następujące biblioteki:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ImageClassification/CustomVisionQuickstart.js?name=snippet_imports)]


> [!TIP]
> Chcesz wyświetlić cały plik kodu szybkiego startu jednocześnie? Znajdziesz go w witrynie [GitHub,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/CustomVision/ImageClassification/CustomVisionQuickstart.js)która zawiera przykłady kodu z tego przewodnika Szybki start.

Utwórz zmienne dla punktu końcowego i kluczy platformy Azure zasobu. 

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ImageClassification/CustomVisionQuickstart.js?name=snippet_creds)]

> [!IMPORTANT]
> Przejdź do witryny Azure Portal. Jeśli zasób Custom Vision Training utworzony w sekcji **Wymagania** wstępne został pomyślnie wdrożony, kliknij przycisk Przejdź do zasobu **w** obszarze Następne **kroki.** Klucz i punkt końcowy można znaleźć na stronie klucza i punktu **końcowego** zasobu. 
>
>Wartość identyfikatora zasobu przewidywania można znaleźć na  karcie Właściwości zasobu, która jest wymieniona jako **identyfikator subskrypcji**.
>
> Pamiętaj, aby usunąć klucz z kodu, gdy wszystko będzie gotowe, i nigdy nie publikować go publicznie. W środowisku produkcyjnym rozważ użycie bezpiecznego sposobu przechowywania poświadczeń i uzyskiwania do nich dostępu. Aby uzyskać więcej informacji, zobacz Cognitive Services [zabezpieczeń.](../../../cognitive-services-security.md)

Dodaj również pola dla nazwy projektu i parametr limitu czasu dla wywołań asynchronicznych.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ImageClassification/CustomVisionQuickstart.js?name=snippet_vars)]


## <a name="object-model"></a>Model obiektów

|Nazwa|Opis|
|---|---|
|[TrainingAPIClient](/javascript/api/@azure/cognitiveservices-customvision-training/trainingapiclient) | Ta klasa obsługuje tworzenie, szkolenie i publikowanie modeli. |
|[PredictionAPIClient](/javascript/api/@azure/cognitiveservices-customvision-prediction/predictionapiclient)| Ta klasa obsługuje wykonywanie zapytań o modele w celu przewidywania klasyfikacji obrazów.|
|[Przewidywania](/javascript/api/@azure/cognitiveservices-customvision-prediction/prediction)| Ten interfejs definiuje pojedyncze przewidywanie na pojedynczym obrazie. Zawiera właściwości identyfikatora obiektu i nazwy oraz ocenę ufności.|

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu pokazują, jak wykonać następujące zadania przy użyciu Custom Vision klienta dla języka JavaScript:

* [Uwierzytelnianie klienta](#authenticate-the-client)
* [Tworzenie nowego projektu usługi Custom Vision](#create-a-new-custom-vision-project)
* [Dodawanie tagów do projektu](#add-tags-to-the-project)
* [Przekazywanie i Tagi obrazów](#upload-and-tag-images)
* [Uczenie projektu](#train-the-project)
* [Opublikuj bieżącą iterację](#publish-the-current-iteration)
* [Testowanie punktu końcowego przewidywania](#test-the-prediction-endpoint)

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

Wystąpienia obiektów klienta z punktem końcowym i kluczem. Utwórz obiekt **ApiKeyCredentials** przy użyciu klucza i użyj go z punktem końcowym, aby utworzyć obiekt [TrainingAPIClient](/javascript/api/@azure/cognitiveservices-customvision-training/trainingapiclient) i [PredictionAPIClient.](/javascript/api/@azure/cognitiveservices-customvision-prediction/predictionapiclient)

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ImageClassification/CustomVisionQuickstart.js?name=snippet_auth)]


## <a name="create-a-new-custom-vision-project"></a>Tworzenie nowego projektu usługi Custom Vision

Uruchom nową funkcję, aby zawierała wszystkie Custom Vision wywołań funkcji. Dodaj następujący kod do , aby utworzyć nowy projekt Custom Vision usługi.


[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ImageClassification/CustomVisionQuickstart.js?name=snippet_create)]


## <a name="add-tags-to-the-project"></a>Dodawanie tagów do projektu

Aby utworzyć tagi klasyfikacji do projektu, dodaj następujący kod do funkcji:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ImageClassification/CustomVisionQuickstart.js?name=snippet_tags)]


## <a name="upload-and-tag-images"></a>Przekazywanie i Tagi obrazów

Najpierw pobierz przykładowe obrazy dla tego projektu. Zapisz zawartość przykładowego [folderu Images na](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/CustomVision/ImageClassification/Images) urządzeniu lokalnym.

> [!NOTE]
> Czy potrzebujesz szerszego zestawu obrazów do ukończenia szkolenia? Trove, Microsoft Garage projektu, umożliwia zbieranie i kupowanie zestawów obrazów do celów szkoleniowych. Po zebraniu obrazów możesz je pobrać, a następnie zaimportować do projektu Custom Vision w zwykły sposób. Odwiedź stronę [Trove, aby](https://www.microsoft.com/ai/trove?activetab=pivot1:primaryr3) dowiedzieć się więcej.

Aby dodać przykładowe obrazy do projektu, po utworzeniu tagów wstaw następujący kod. Ten kod przekazuje każdy obraz z odpowiednim tagiem.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ImageClassification/CustomVisionQuickstart.js?name=snippet_upload)]

> [!IMPORTANT]
> Musisz zmienić ścieżkę do obrazów ( ) w zależności od tego, dokąd pobrano `sampleDataRoot` Cognitive Services przykładów zestawu SDK dla języka Python.

## <a name="train-the-project"></a>Uczenie projektu

Ten kod tworzy pierwszą iterację modelu przewidywania. 

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ImageClassification/CustomVisionQuickstart.js?name=snippet_train)]

## <a name="publish-the-current-iteration"></a>Opublikuj bieżącą iterację

Ten kod publikuje wytrenowane iteracje w punkcie końcowym przewidywania. Nazwa nadana do publikowanej iteracji może służyć do wysyłania żądań przewidywania. Iteracja nie jest dostępna w punkcie końcowym przewidywania, dopóki nie zostanie opublikowana.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ImageClassification/CustomVisionQuickstart.js?name=snippet_publish)]


## <a name="test-the-prediction-endpoint"></a>Testowanie punktu końcowego przewidywania

Aby wysłać obraz do punktu końcowego przewidywania i pobrać przewidywanie, dodaj następujący kod do funkcji. 

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ImageClassification/CustomVisionQuickstart.js?name=snippet_test)]

Następnie zamknij funkcję Custom Vision i wywołaj ją.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ImageClassification/CustomVisionQuickstart.js?name=snippet_function_close)]


## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację za pomocą `node` polecenia w pliku szybkiego startu.

```console
node index.js
```

Dane wyjściowe aplikacji powinny być podobne do poniższego tekstu:

```console
Creating project...
Adding images...
Training...
Training started...
Training status: Training
Training status: Training
Training status: Training
Training status: Completed
Results:
         Hemlock: 94.97%
         Japanese Cherry: 0.01%
```

Następnie możesz sprawdzić, czy obraz testowy (znaleziony w **<sampleDataRoot> /Test/**) został odpowiednio otagowany. Możesz też wrócić do [witryny internetowej Custom Vision](https://customvision.ai) i wyświetlić bieżący stan nowo utworzonego projektu.

[!INCLUDE [clean-ic-project](../../includes/clean-ic-project.md)]

## <a name="next-steps"></a>Następne kroki

Teraz już wiesz, jak można wykonać każdy krok procesu wykrywania obiektów w kodzie. Ten przykład wykonuje pojedynczą iterację trenowania, ale często trzeba będzie wielokrotnie trenować i testować model, aby był bardziej dokładny.

> [!div class="nextstepaction"]
> [Testowanie i ponowne szkolenie modelu](../../test-your-model.md)

* [Co to jest usługa Custom Vision?](../../overview.md)
* Kod źródłowy tego przykładu można znaleźć w witrynie [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/CustomVision/ImageClassification/CustomVisionQuickstart.js)
* [Dokumentacja referencyjna zestawu SDK (szkolenie)](/javascript/api/@azure/cognitiveservices-customvision-training/)
* [Dokumentacja referencyjna zestawu SDK (przewidywanie)](/javascript/api/@azure/cognitiveservices-customvision-prediction/)
