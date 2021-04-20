---
author: areddish
ms.author: areddish
ms.service: cognitive-services
ms.date: 10/26/2020
ms.custom: devx-track-js
ms.openlocfilehash: de8ca0a9410479b4d166a47e5c56742955b7853f
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725196"
---
Ten przewodnik zawiera instrukcje i przykładowy kod, które pomogą Ci rozpocząć korzystanie z biblioteki Custom Vision klienta usługi Node.js do tworzenia modelu wykrywania obiektów. Utworzysz projekt, dodasz tagi, przeszkolisz projekt i użyjesz adresu URL punktu końcowego przewidywania projektu, aby go przetestować programowo. Użyj tego przykładu jako szablonu do tworzenia własnej aplikacji do rozpoznawania obrazów.

> [!NOTE]
> Jeśli chcesz utworzyć i wytszkolić _model_ wykrywania obiektów bez pisania kodu, zapoznaj się ze wskazówkami opartymi [na przeglądarce.](../../get-started-build-detector.md)

Użyj biblioteki Custom Vision klienta dla programu .NET, aby:

* Tworzenie nowego projektu usługi Custom Vision
* Dodawanie tagów do projektu
* Przekazywanie i Tagi obrazów
* Uczenie projektu
* Opublikuj bieżącą iterację
* Testowanie punktu końcowego przewidywania

Dokumentacja [referencyjna (trenowania)](/javascript/api/@azure/cognitiveservices-customvision-training/) [(przewidywanie)](/javascript/api/@azure/cognitiveservices-customvision-prediction/) | Kod źródłowy [biblioteki (trenowania)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-customvision-training) [(przewidywanie) |](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-customvision-prediction) Przykłady pakietu (npm) [(trenowania)](https://www.npmjs.com/package/@azure/cognitiveservices-customvision-training) [(przewidywanie)](https://www.npmjs.com/package/@azure/cognitiveservices-customvision-prediction)  |  [](/samples/browse/?products=azure&terms=custom%20vision&languages=javascript)


## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure [— utwórz subskrypcję bezpłatnie](https://azure.microsoft.com/free/cognitive-services/)
* Bieżąca wersja [Node.js](https://nodejs.org/)
* Po utworzeniu subskrypcji platformy Azure utwórz zasób usługi Custom Vision, aby utworzyć zasób usługi Custom Vision w usłudze Azure Portal, aby utworzyć zasób trenowania i przewidywania oraz uzyskać klucze i punkt <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesCustomVision"  title=" "  target="_blank"> </a> końcowy. Poczekaj na wdrożenie, a następnie kliknij **przycisk Przejdź do** zasobu.
    * Będziesz potrzebować klucza i punktu końcowego z zasobów, które utworzysz, aby połączyć aplikację z Custom Vision. Klucz i punkt końcowy wkleisz do poniższego kodu w dalszej części tego przewodnika Szybki start.
    * Możesz użyć warstwy cenowej Bezpłatna ( ), aby wypróbować usługę, i przejść później na warstwę płatną `F0` na użytek produkcji.

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-new-nodejs-application"></a>Tworzenie nowej aplikacji Node.js

W oknie konsoli (takim jak cmd, PowerShell lub Bash) utwórz nowy katalog dla aplikacji i przejdź do niego. 

```console
mkdir myapp && cd myapp
```

Uruchom polecenie `npm init` , aby utworzyć aplikację node z `package.json` plikiem. 

```console
npm init
```

### <a name="install-the-client-library"></a>Instalowanie biblioteki klienta

Aby napisać aplikację do analizy obrazów Custom Vision do Node.js, musisz mieć Custom Vision npm. Aby je zainstalować, uruchom następujące polecenie w programie PowerShell:

```shell
npm install @azure/cognitiveservices-customvision-training
npm install @azure/cognitiveservices-customvision-prediction
```

Plik aplikacji `package.json` zostanie zaktualizowany o zależności.

Utwórz plik o nazwie `index.js` i zaimportuj następujące biblioteki:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_imports)]

> [!TIP]
> Chcesz wyświetlić cały plik kodu szybkiego startu jednocześnie? Można go znaleźć w witrynie [GitHub,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js)która zawiera przykłady kodu w tym przewodniku Szybki start.

Utwórz zmienne dla punktu końcowego i kluczy platformy Azure zasobu. 

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_creds)]

> [!IMPORTANT]
> Przejdź do witryny Azure Portal. Jeśli zasoby Custom Vision utworzone w sekcji  Wymagania wstępne zostały wdrożone pomyślnie, kliknij przycisk Przejdź do **zasobu** w obszarze **Następne kroki.** Klucze i punkt końcowy można znaleźć na stronach kluczy i **punktów** końcowych zasobów w obszarze **zarządzanie zasobami.** Musisz uzyskać klucze dla zasobów trenowania i przewidywania wraz z punktem końcowym interfejsu API dla zasobu trenowania.
>
> Pamiętaj, aby usunąć klucz z kodu, gdy wszystko będzie gotowe, i nigdy nie publikować go publicznie. W środowisku produkcyjnym rozważ użycie bezpiecznego sposobu przechowywania poświadczeń i uzyskiwania do nich dostępu. Aby uzyskać więcej informacji, zobacz artykuł Cognitive Services [zabezpieczeń.](../../../cognitive-services-security.md)

Dodaj również pola dla nazwy projektu i parametr limitu czasu dla wywołań asynchronicznych.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_vars)]

## <a name="object-model"></a>Model obiektu

|Nazwa|Opis|
|---|---|
|[TrainingAPIClient](/javascript/api/@azure/cognitiveservices-customvision-training/trainingapiclient) | Ta klasa obsługuje tworzenie, szkolenie i publikowanie modeli. |
|[PredictionAPIClient](/javascript/api/@azure/cognitiveservices-customvision-prediction/predictionapiclient)| Ta klasa obsługuje wykonywanie zapytań o modele w celu przewidywania wykrywania obiektów.|
|[Przewidywania](/javascript/api/@azure/cognitiveservices-customvision-prediction/prediction)| Ten interfejs definiuje pojedyncze przewidywanie na pojedynczym obrazie. Zawiera właściwości identyfikatora obiektu i nazwy oraz ocenę ufności.|

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu pokazują, jak wykonać następujące zadania za pomocą biblioteki Custom Vision klienta dla języka JavaScript:

* [Uwierzytelnianie klienta](#authenticate-the-client)
* [Tworzenie nowego projektu usługi Custom Vision](#create-a-new-custom-vision-project)
* [Dodawanie tagów do projektu](#add-tags-to-the-project)
* [Przekazywanie i Tagi obrazów](#upload-and-tag-images)
* [Uczenie projektu](#train-the-project)
* [Opublikuj bieżącą iterację](#publish-the-current-iteration)
* [Testowanie punktu końcowego przewidywania](#test-the-prediction-endpoint)

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

Za pomocą punktu końcowego i klucza należy utworzyć wystąpienia obiektów klienta. Utwórz obiekt **ApiKeyCredentials** przy użyciu klucza i użyj go z punktem końcowym, aby utworzyć obiekt [TrainingAPIClient](/javascript/api/@azure/cognitiveservices-customvision-training/trainingapiclient) i [PredictionAPIClient.](/javascript/api/@azure/cognitiveservices-customvision-prediction/predictionapiclient)

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_auth)]

## <a name="add-helper-function"></a>Dodawanie funkcji pomocnika

Dodaj następującą funkcję, aby ułatwić wiele wywołań asynchronicznych. Użyjesz go później.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_auth)]

## <a name="create-a-new-custom-vision-project"></a>Tworzenie nowego projektu usługi Custom Vision

Uruchom nową funkcję, która będzie zawierać wszystkie Custom Vision wywołań funkcji. Dodaj następujący kod do , aby utworzyć nowy projekt Custom Vision usługi.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_create)]

## <a name="upload-and-tag-images"></a>Przekazywanie i Tagi obrazów

Najpierw pobierz przykładowe obrazy dla tego projektu. Zapisz zawartość przykładowego [folderu Images na](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/CustomVision/ObjectDetection/Images) urządzeniu lokalnym.

> [!NOTE]
> Czy potrzebujesz szerszego zestawu obrazów do ukończenia szkolenia? Trove, Microsoft Garage projektu, umożliwia zbieranie i kupowanie zestawów obrazów na potrzeby szkolenia. Po zebraniu obrazów możesz je pobrać, a następnie zaimportować do projektu Custom Vision w zwykły sposób. Odwiedź stronę [Trove, aby](https://www.microsoft.com/ai/trove?activetab=pivot1:primaryr3) dowiedzieć się więcej.

Aby dodać przykładowe obrazy do projektu, po utworzeniu tagów wstaw następujący kod. Ten kod przekazuje każdy obraz z odpowiednim tagiem. Po oznaczeniu obrazów w projektach wykrywania obiektów należy określić region każdego z otagowanych obiektów przy użyciu znormalizowanych współrzędnych. W tym samouczku regiony są zakodowane na hardcode w tekście z kodem. Regiony określają pole ograniczenia w znormalizowanych współrzędnych, które podaje się w kolejności: lewa krawędź, górna krawędź, szerokość, wysokość. W jednej partii można przekazać maksymalnie 64 obrazy.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_upload)]


> [!IMPORTANT]
> Musisz zmienić ścieżkę do obrazów ( ) w zależności od tego, gdzie pobrano Cognitive Services `sampleDataRoot` przykładów zestawu SDK dla języka Python.

> [!NOTE]
> Jeśli nie masz narzędzia do klikania i przeciągania do oznaczania współrzędnych regionów, możesz użyć internetowego interfejsu użytkownika pod [Customvision.ai](https://www.customvision.ai/). W tym przykładzie współrzędne są już podane.


## <a name="train-the-project"></a>Uczenie projektu

Ten kod tworzy pierwszą iterację modelu przewidywania. 

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_train)]


## <a name="publish-the-current-iteration"></a>Opublikuj bieżącą iterację

Ten kod publikuje wytrenowane iteracje w punkcie końcowym przewidywania. Nazwa nadana do publikowanej iteracji może służyć do wysyłania żądań przewidywania. Iteracja nie jest dostępna w punkcie końcowym przewidywania, dopóki nie zostanie opublikowana.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_publish)]


## <a name="test-the-prediction-endpoint"></a>Testowanie punktu końcowego przewidywania

Aby wysłać obraz do punktu końcowego przewidywania i pobrać przewidywanie, dodaj następujący kod do funkcji. 

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_test)]

Następnie zamknij funkcję Custom Vision i wywołaj ją.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_function_close)]

## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację za pomocą `node` polecenia w pliku szybkiego startu.

```shell
node index.js
```

Dane wyjściowe aplikacji powinny pojawić się w konsoli. Następnie możesz sprawdzić, czy obraz testowy (znaleziony w pliku **<sampleDataRoot> /Test/**) został odpowiednio otagowany i czy region wykrywania jest poprawny. Możesz też wrócić do [witryny internetowej Custom Vision](https://customvision.ai) i wyświetlić bieżący stan nowo utworzonego projektu.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [clean-od-project](../../includes/clean-od-project.md)]

## <a name="next-steps"></a>Następne kroki

Teraz zostały wykonane wszystkie kroki procesu wykrywania obiektów w kodzie. Ten przykład wykonuje pojedynczą iterację trenowania, ale często trzeba będzie wielokrotnie trenować i testować model, aby był bardziej dokładny. Następny przewodnik dotyczy klasyfikacji obrazów. Jej zasady są podobne do wykrywania obiektów.

> [!div class="nextstepaction"]
> [Testowanie i ponowne szkolenie modelu](../../test-your-model.md)

* [Co to jest usługa Custom Vision?](../../overview.md)
* Kod źródłowy tego przykładu można znaleźć w witrynie [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js)
* [Dokumentacja referencyjna zestawu SDK (szkolenie)](/javascript/api/@azure/cognitiveservices-customvision-training/)
* [Dokumentacja referencyjna zestawu SDK (przewidywanie)](/javascript/api/@azure/cognitiveservices-customvision-prediction/)
