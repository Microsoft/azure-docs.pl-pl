---
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-services
ms.date: 10/25/2020
ms.openlocfilehash: 2ac00ddf419dc06ff2b330423cd4025499329b33
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725316"
---
Wprowadzenie do biblioteki Custom Vision klienta dla języka Python. Wykonaj następujące kroki, aby zainstalować pakiet i wypróbować przykładowy kod tworzenia modelu klasyfikacji obrazów. Utworzysz projekt, dodasz tagi, przeszkolisz projekt i użyjesz adresu URL punktu końcowego przewidywania projektu, aby go przetestować programowo. Użyj tego przykładu jako szablonu do tworzenia własnej aplikacji do rozpoznawania obrazów.

> [!NOTE]
> Jeśli chcesz utworzyć i wytszkolić _model_ klasyfikacji bez pisania kodu, zapoznaj się ze wskazówkami opartymi [na przeglądarce.](../../getting-started-build-a-classifier.md)

Użyj biblioteki Custom Vision klienta dla języka Python, aby:

* Tworzenie nowego projektu usługi Custom Vision
* Dodawanie tagów do projektu
* Przekazywanie i Tagi obrazów
* Uczenie projektu
* Opublikuj bieżącą iterację
* Testowanie punktu końcowego przewidywania

[Dokumentacja referencyjna](/python/api/overview/azure/cognitiveservices/customvision)  |  [Kod źródłowy biblioteki](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-customvision/azure/cognitiveservices/vision/customvision)  |  [Pakiet (PyPI)](https://pypi.org/project/azure-cognitiveservices-vision-customvision/)  |  [Przykłady](/samples/browse/?languages=python&products=azure&term=vision&terms=vision)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure [— tworzenie bezpłatnej subskrypcji](https://azure.microsoft.com/free/cognitive-services/)
* [Python 3.x](https://www.python.org/)
  * Instalacja języka Python powinna zawierać [pip](https://pip.pypa.io/en/stable/). Możesz sprawdzić, czy masz zainstalowany program pip, `pip --version` uruchamiając polecenie w wierszu polecenia. Pobierz program pip, instalując najnowszą wersję języka Python.
* Po utworzeniu subskrypcji platformy Azure utwórz zasób usługi Custom Vision, aby utworzyć zasób usługi Custom Vision w usłudze Azure Portal, aby utworzyć zasób trenowania i przewidywania oraz pobrać klucze i punkt <a href="https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision"  title=" "  target="_blank"> </a> końcowy. Poczekaj na wdrożenie i kliknij przycisk **Przejdź do** zasobu.
    * Klucz i punkt końcowy będą potrzebne z zasobów, które utworzysz, aby połączyć aplikację z Custom Vision. Klucze i punkt końcowy wkleisz do poniższego kodu w dalszej części tego przewodnika Szybki start.
    * Możesz użyć bezpłatnej warstwy cenowej ( ), aby wypróbować usługę, a następnie uaktualnić ją do warstwy `F0` płatnej w środowisku produkcyjnym.

## <a name="setting-up"></a>Konfigurowanie

### <a name="install-the-client-library"></a>Instalowanie biblioteki klienta

Aby napisać aplikację do analizy obrazów Custom Vision dla języka Python, musisz mieć Custom Vision klienta. Po zainstalowaniu języka Python uruchom następujące polecenie w programie PowerShell lub w oknie konsoli:

```powershell
pip install azure-cognitiveservices-vision-customvision
```

### <a name="create-a-new-python-application"></a>Tworzenie nowej aplikacji w języku Python

Utwórz nowy plik języka Python i zaimportuj następujące biblioteki.

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_imports)]

> [!TIP]
> Chcesz wyświetlić cały plik kodu szybkiego startu jednocześnie? Znajdziesz go w witrynie [GitHub,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/CustomVision/ImageClassification/CustomVisionQuickstart.py)która zawiera przykłady kodu z tego przewodnika Szybki start.

Utwórz zmienne dla punktu końcowego platformy Azure i kluczy subskrypcji zasobu.

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_creds)]

> [!IMPORTANT]
> Przejdź do witryny Azure Portal. Jeśli zasoby Custom Vision utworzone w sekcji  Wymagania wstępne zostały pomyślnie wdrożone, kliknij przycisk Przejdź do zasobu **w** obszarze **Następne kroki.** Klucze i punkt końcowy można znaleźć na stronach kluczy i punktów **końcowych** zasobów. Musisz uzyskać klucze dla zasobów trenowania i przewidywania wraz z punktem końcowym interfejsu API dla zasobu trenowania.
>
> Wartość identyfikatora zasobu przewidywania można znaleźć na  karcie Właściwości zasobu, która jest wymieniona jako **identyfikator subskrypcji**.
>
> Pamiętaj, aby usunąć klucze z kodu, gdy wszystko będzie gotowe, i nigdy nie publikować ich publicznie. W środowisku produkcyjnym rozważ użycie bezpiecznego sposobu przechowywania poświadczeń i uzyskiwania do nich dostępu. Aby uzyskać więcej informacji, zobacz Cognitive Services [zabezpieczeń.](../../../cognitive-services-security.md)

## <a name="object-model"></a>Model obiektów

|Nazwa|Opis|
|---|---|
|[CustomVisionTrainingClient](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.customvisiontrainingclient) | Ta klasa obsługuje tworzenie, szkolenie i publikowanie modeli. |
|[CustomVisionPredictionClient](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.prediction.customvisionpredictionclient)| Ta klasa obsługuje wykonywanie zapytań o modele w celu przewidywania klasyfikacji obrazów.|
|[ImagePrediction](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.prediction.models.imageprediction)| Ta klasa definiuje przewidywanie pojedynczego obiektu na pojedynczym obrazie. Zawiera właściwości identyfikatora i nazwy obiektu, lokalizację pola granicy obiektu oraz ocenę ufności.|

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu pokazują, jak wykonać następujące czynności przy użyciu Custom Vision klienta dla języka Python:

* [Uwierzytelnianie klienta](#authenticate-the-client)
* [Tworzenie nowego projektu usługi Custom Vision](#create-a-new-custom-vision-project)
* [Dodawanie tagów do projektu](#add-tags-to-the-project)
* [Przekazywanie i Tagi obrazów](#upload-and-tag-images)
* [Uczenie projektu](#train-the-project)
* [Opublikuj bieżącą iterację](#publish-the-current-iteration)
* [Testowanie punktu końcowego przewidywania](#test-the-prediction-endpoint)

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

Za pomocą punktu końcowego i kluczy należy utworzyć wystąpienia klienta trenowania i przewidywania. Utwórz **obiekty ApiKeyServiceClientCredentials** przy użyciu kluczy i użyj ich z punktem końcowym, aby utworzyć obiekt [CustomVisionTrainingClient](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.customvisiontrainingclient) i [CustomVisionPredictionClient.](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.prediction.customvisionpredictionclient)

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_auth)]

## <a name="create-a-new-custom-vision-project"></a>Tworzenie nowego projektu usługi Custom Vision

Dodaj następujący kod do skryptu, aby utworzyć nowy projekt Custom Vision Service. 

Zobacz metodę [create_project,](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.operations.customvisiontrainingclientoperationsmixin#create-project-name--description-none--domain-id-none--classification-type-none--target-export-platforms-none--custom-headers-none--raw-false----operation-config-) aby określić inne opcje podczas tworzenia projektu (wyjaśniono w przewodniku po portalu internetowym Tworzenie klasyfikatora). [](../../getting-started-build-a-classifier.md)  

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_create)]

## <a name="add-tags-to-the-project"></a>Dodawanie tagów do projektu

Aby dodać tagi klasyfikacji do projektu, dodaj następujący kod:

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_tags)]


## <a name="upload-and-tag-images"></a>Przekazywanie i Tagi obrazów

Najpierw pobierz przykładowe obrazy dla tego projektu. Zapisz zawartość przykładowego [folderu Images na](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/CustomVision/ImageClassification/Images) urządzeniu lokalnym.

> [!NOTE]
> Czy potrzebujesz szerszego zestawu obrazów do ukończenia szkolenia? Trove, Microsoft Garage projektu, umożliwia zbieranie i kupowanie zestawów obrazów do celów szkoleniowych. Po zebraniu obrazów możesz je pobrać, a następnie zaimportować do projektu Custom Vision w zwykły sposób. Odwiedź stronę [Trove, aby](https://www.microsoft.com/ai/trove?activetab=pivot1:primaryr3) dowiedzieć się więcej.

Aby dodać przykładowe obrazy do projektu, po utworzeniu tagów wstaw następujący kod. Ten kod przekazuje każdy obraz z odpowiednim tagiem. W jednej partii można przekazać maksymalnie 64 obrazy.

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_upload)]

> [!NOTE]
> Musisz zmienić ścieżkę do obrazów w zależności od tego, dokąd pobrano Cognitive Services przykładów zestawu PYTHON SDK.

## <a name="train-the-project"></a>Uczenie projektu

Ten kod tworzy pierwszą iterację modelu przewidywania. 

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_train)]

> [!TIP]
> Trenuj przy użyciu wybranych tagów
>
> Opcjonalnie możesz trenować tylko podzestaw zastosowanych tagów. Możesz to zrobić, jeśli nie zastosowano jeszcze wystarczającej ilości niektórych tagów, ale masz wystarczająco dużo innych tagów. W **[train_project](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.operations.customvisiontrainingclientoperationsmixin#train-project-project-id--training-type-none--reserved-budget-in-hours-0--force-train-false--notification-email-address-none--selected-tags-none--custom-headers-none--raw-false----operation-config-&preserve-view=true)** ustaw opcjonalny parametr *selected_tags* na listę ciągów identyfikatorów tagów, których chcesz użyć. Model będzie trenować, aby rozpoznawał tylko tagi na tej liście.

## <a name="publish-the-current-iteration"></a>Opublikuj bieżącą iterację

Iteracja nie jest dostępna w punkcie końcowym przewidywania, dopóki nie zostanie opublikowana. Poniższy kod udostępnia bieżącą iterację modelu do wykonywania zapytań. 

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_publish)]


## <a name="test-the-prediction-endpoint"></a>Testowanie punktu końcowego przewidywania

Aby wysłać obraz do punktu końcowego przewidywania i uzyskać przewidywanie, dodaj na końcu pliku następujący kod:

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_test)]

## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom *CustomVisionQuickstart.py*.

```powershell
python CustomVisionQuickstart.py
```

Dane wyjściowe aplikacji powinny być podobne do poniższego tekstu:

```console
Creating project...
Adding images...
Training...
Training status: Training
Training status: Completed
Done!
        Hemlock: 93.53%
        Japanese Cherry: 0.01%
```

Następnie możesz sprawdzić, czy obraz testowy (znaleziony w pliku **<base_image_location>/images/Test/**) został odpowiednio otagowany. Możesz też wrócić do [witryny internetowej Custom Vision](https://customvision.ai) i wyświetlić bieżący stan nowo utworzonego projektu.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [clean-ic-project](../../includes/clean-ic-project.md)]

## <a name="next-steps"></a>Następne kroki

Teraz już wiesz, jak można wykonać każdy krok procesu klasyfikacji obrazów w kodzie. Ten przykład wykonuje pojedynczą iterację trenowania, ale często trzeba będzie wielokrotnie trenować i testować model, aby był bardziej dokładny.

> [!div class="nextstepaction"]
> [Testowanie i ponowne szkolenie modelu](../../test-your-model.md)

* [Co to jest usługa Custom Vision?](../../overview.md)
* Kod źródłowy tego przykładu można znaleźć w witrynie [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/CustomVision/ImageClassification/CustomVisionQuickstart.py)
* [Dokumentacja referencyjna zestawu SDK](/python/api/overview/azure/cognitiveservices/customvision)
