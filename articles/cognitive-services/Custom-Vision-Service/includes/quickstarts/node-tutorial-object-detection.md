---
author: areddish
ms.author: areddish
ms.service: cognitive-services
ms.date: 10/26/2020
ms.custom: devx-track-js
ms.openlocfilehash: cfcc5bbc101a37e607fcc1bdb0d374510e7c3825
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102444737"
---
Ten przewodnik zawiera instrukcje i przykładowy kod ułatwiający rozpoczęcie pracy przy użyciu Custom Visionej biblioteki klienta Node.js do kompilowania modelu wykrywania obiektów. Utworzysz projekt, dodasz Tagi, nauczysz projekt, a następnie użyjesz w adresie URL punktu końcowego przewidywania projektu do programistycznego testowania. Użyj tego przykładu jako szablonu do tworzenia własnej aplikacji rozpoznawania obrazu.

> [!NOTE]
> Jeśli chcesz skompilować i przeszkolić model wykrywania obiektów _bez_ pisania kodu, zamiast tego zapoznaj się ze [wskazówkami w przeglądarce](../../get-started-build-detector.md) .

Użyj biblioteki klienta Custom Vision dla platformy .NET, aby:

* Tworzenie nowego projektu usługi Custom Vision
* Dodawanie tagów do projektu
* Przekazywanie i Tagi obrazów
* Uczenie projektu
* Opublikuj bieżącą iterację
* Testowanie punktu końcowego przewidywania

Dokumentacja referencyjna [(szkolenie)](/javascript/api/@azure/cognitiveservices-customvision-training/) [(przewidywania)](/javascript/api/@azure/cognitiveservices-customvision-prediction/) | Kod źródłowy biblioteki [(szkolenie)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-customvision-training) [(przewidywania)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-customvision-prediction) | Przykłady pakietów (npm) [(uczenie](https://www.npmjs.com/package/@azure/cognitiveservices-customvision-training) [się)](https://www.npmjs.com/package/@azure/cognitiveservices-customvision-prediction)  |  [](/samples/browse/?products=azure&terms=custom%20vision&languages=javascript)


## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/cognitive-services/)
* Bieżąca wersja [Node.js](https://nodejs.org/)
* Gdy masz subskrypcję platformy Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesCustomVision"  title=" Utwórz zasób Custom Vision "  target="_blank"> utwórz zasób Custom Vision </a> w Azure Portal, aby utworzyć zasób szkoleniowy i predykcyjny oraz pobrać klucze i punkt końcowy. Zaczekaj na jego wdrożenie i kliknij przycisk **Przejdź do zasobu** .
    * Będziesz potrzebować klucza i punktu końcowego z zasobów, które tworzysz, aby połączyć aplikację z Custom Vision. Klucz i punkt końcowy zostaną wklejone do poniższego kodu w dalszej części przewodnika Szybki Start.
    * Możesz użyć warstwy cenowej bezpłatna ( `F0` ) w celu wypróbowania usługi i później przeprowadzić uaktualnienie do warstwy płatnej dla środowiska produkcyjnego.

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-new-nodejs-application"></a>Tworzenie nowej aplikacji Node.js

W oknie konsoli (na przykład cmd, PowerShell lub bash) Utwórz nowy katalog dla aplikacji i przejdź do niego. 

```console
mkdir myapp && cd myapp
```

Uruchom `npm init` polecenie, aby utworzyć aplikację Node z `package.json` plikiem. 

```console
npm init
```

### <a name="install-the-client-library"></a>Zainstaluj bibliotekę kliencką

Aby napisać aplikację do analizy obrazów przy użyciu Custom Vision dla Node.js, potrzebne są pakiety Custom Vision NPM. Aby je zainstalować, uruchom następujące polecenie w programie PowerShell:

```shell
npm install @azure/cognitiveservices-customvision-training
npm install @azure/cognitiveservices-customvision-prediction
```

`package.json`Plik aplikacji zostanie zaktualizowany z zależnościami.

Utwórz plik o nazwie `index.js` i zaimportuj następujące biblioteki:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_imports)]

> [!TIP]
> Chcesz wyświetlić cały plik kodu szybkiego startu jednocześnie? Można je znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js), która zawiera przykłady kodu w tym przewodniku Szybki Start.

Utwórz zmienne dla punktu końcowego i kluczy usługi Azure Resource. 

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_creds)]

> [!IMPORTANT]
> Przejdź do witryny Azure Portal. Jeśli pomyślnie wdrożono zasoby Custom Vision utworzone w sekcji **wymagania wstępne** , kliknij przycisk **Przejdź do zasobu** w obszarze **następne kroki**. Klucze i punkt końcowy można znaleźć na stronach "klucze zasobów" **i punkt końcowy** w obszarze **Zarządzanie zasobami**. Musisz pobrać klucze szkoleniowe i prognozowe wraz z punktem końcowym zasobów szkoleniowych.
>
> Pamiętaj, aby usunąć klucz z kodu, gdy skończysz, i nigdy nie Publikuj go publicznie. W przypadku produkcji należy rozważyć użycie bezpiecznego sposobu przechowywania poświadczeń i uzyskiwania do nich dostępu. Aby uzyskać więcej informacji, zobacz artykuł dotyczący [zabezpieczeń](../../../../cognitive-services/cognitive-services-security.md) Cognitive Services.

Należy również dodać pola dla nazwy projektu i parametru limitu czasu dla wywołań asynchronicznych.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_vars)]

## <a name="object-model"></a>Model obiektów

|Nazwa|Opis|
|---|---|
|[TrainingAPIClient](/javascript/api/@azure/cognitiveservices-customvision-training/trainingapiclient) | Ta klasa obsługuje tworzenie, uczenie i publikowanie modeli. |
|[PredictionAPIClient](/javascript/api/@azure/cognitiveservices-customvision-prediction/predictionapiclient)| Ta klasa obsługuje zapytania dotyczące modeli na potrzeby przewidywania wykrywania obiektów.|
|[Przewidując](/javascript/api/@azure/cognitiveservices-customvision-prediction/prediction)| Ten interfejs definiuje pojedyncze prognozowanie na pojedynczym obrazie. Zawiera ona właściwości identyfikatora i nazwy obiektu oraz ocenę ufności.|

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu przedstawiają sposób wykonywania następujących zadań za pomocą biblioteki klienta Custom Vision dla języka JavaScript:

* [Uwierzytelnianie klienta](#authenticate-the-client)
* [Tworzenie nowego projektu usługi Custom Vision](#create-a-new-custom-vision-project)
* [Dodawanie tagów do projektu](#add-tags-to-the-project)
* [Przekazywanie i Tagi obrazów](#upload-and-tag-images)
* [Uczenie projektu](#train-the-project)
* [Opublikuj bieżącą iterację](#publish-the-current-iteration)
* [Testowanie punktu końcowego przewidywania](#test-the-prediction-endpoint)

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

Utwórz wystąpienie obiektów klienta z punktem końcowym i kluczem. Utwórz obiekt **ApiKeyCredentials** z kluczem i użyj go w punkcie końcowym, aby utworzyć obiekt [TrainingAPIClient](/javascript/api/@azure/cognitiveservices-customvision-training/trainingapiclient) i [PredictionAPIClient](/javascript/api/@azure/cognitiveservices-customvision-prediction/predictionapiclient) .

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_auth)]

## <a name="add-helper-function"></a>Dodaj funkcję pomocnika

Dodaj następującą funkcję, aby ułatwić wykonywanie wielu wywołań asynchronicznych. Będzie on używany później.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_auth)]

## <a name="create-a-new-custom-vision-project"></a>Tworzenie nowego projektu usługi Custom Vision

Rozpocznij nową funkcję, aby zawierała wszystkie Custom Vision wywołania funkcji. Dodaj następujący kod do, aby utworzyć nowy projekt usługi Custom Vision.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_create)]

## <a name="upload-and-tag-images"></a>Przekazywanie i Tagi obrazów

Najpierw pobierz przykładowe obrazy dla tego projektu. Zapisz zawartość [folderu Sample images](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/CustomVision/ObjectDetection/Images) na urządzeniu lokalnym.

> [!NOTE]
> Potrzebujesz szerszego zestawu obrazów, aby dokończyć szkolenia? Skarbnica, projekt garażu firmy Microsoft, umożliwia zbieranie i kupowanie zestawów obrazów do celów szkoleniowych. Po zebraniu obrazów można je pobrać, a następnie zaimportować do projektu Custom Vision w zwykły sposób. Odwiedź [stronę skarbnica](https://www.microsoft.com/ai/trove?activetab=pivot1:primaryr3) , aby dowiedzieć się więcej.

Aby dodać przykładowe obrazy do projektu, po utworzeniu tagów wstaw następujący kod. Ten kod przekazuje każdy obraz z odpowiednim tagiem. Po oznaczeniu obrazów w projektach wykrywania obiektów należy określić region każdego z otagowanych obiektów przy użyciu znormalizowanych współrzędnych. W tym samouczku regiony są stałe wbudowane z kodem. Regiony określają pole ograniczenia w znormalizowanych współrzędnych, które podaje się w kolejności: lewa krawędź, górna krawędź, szerokość, wysokość. Można przekazać do 64 obrazów w pojedynczej partii.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_upload)]


> [!IMPORTANT]
> Należy zmienić ścieżkę do obrazów (), na `sampleDataRoot` podstawie której pobrano repozytorium przykładów zestawu SDK języka Python Cognitive Services.

> [!NOTE]
> Jeśli nie masz narzędzia do klikania i przeciągania, aby oznaczyć współrzędne regionów, możesz użyć internetowego interfejsu użytkownika w [Customvision.AI](https://www.customvision.ai/). W tym przykładzie współrzędne zostały już podane.


## <a name="train-the-project"></a>Uczenie projektu

Ten kod tworzy pierwszą iterację modelu predykcyjnego. 

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_train)]


## <a name="publish-the-current-iteration"></a>Opublikuj bieżącą iterację

Ten kod publikuje przeszkolony iterację w punkcie końcowym przewidywania. Nazwa nadana do publikowanej iteracji może służyć do wysyłania żądań przewidywania. Iteracja nie jest dostępna w punkcie końcowym przewidywania do momentu opublikowania.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_publish)]


## <a name="test-the-prediction-endpoint"></a>Testowanie punktu końcowego przewidywania

Aby wysłać obraz do punktu końcowego przewidywania i pobrać prognozę, Dodaj następujący kod do funkcji. 

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_test)]

Następnie zamknij funkcję Custom Vision i Wywołaj ją.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_function_close)]

## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację za pomocą `node` polecenia w pliku szybkiego startu.

```shell
node index.js
```

Dane wyjściowe aplikacji powinny pojawić się w konsoli. Następnie można sprawdzić, czy obraz testowy (znaleziony w **<sampleDataRoot> /test/**) jest odpowiednio oznakowany i czy region wykrywania jest poprawny. Możesz też wrócić do [witryny internetowej Custom Vision](https://customvision.ai) i wyświetlić bieżący stan nowo utworzonego projektu.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [clean-od-project](../../includes/clean-od-project.md)]

## <a name="next-steps"></a>Następne kroki

Teraz każdy krok procesu wykrywania obiektów można wykonać w kodzie. Ten przykład wykonuje pojedynczą iterację szkoleniową, ale często należy przeprowadzić uczenie i testowanie modelu wiele razy, aby zwiększyć jego dokładność. Następny przewodnik dotyczy klasyfikacji obrazów. Jej zasady są podobne do wykrywania obiektów.

> [!div class="nextstepaction"]
> [Testowanie i ponowne szkolenie modelu](../../test-your-model.md)

* [Co to jest usługa Custom Vision?](../../overview.md)
* Kod źródłowy dla tego przykładu można znaleźć w witrynie [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js)
* [Dokumentacja referencyjna zestawu SDK (szkolenie)](/javascript/api/@azure/cognitiveservices-customvision-training/)
* [Dokumentacja referencyjna zestawu SDK (przewidywania)](/javascript/api/@azure/cognitiveservices-customvision-prediction/)
