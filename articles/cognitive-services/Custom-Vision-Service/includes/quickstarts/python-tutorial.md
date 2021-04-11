---
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-services
ms.date: 10/25/2020
ms.openlocfilehash: c60c0326018e615a0c84d56c98faee58560f1d87
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106113246"
---
Rozpocznij pracę z biblioteką klienta Custom Vision dla języka Python. Wykonaj następujące kroki, aby zainstalować pakiet i wypróbować przykładowy kod służący do tworzenia modelu klasyfikacji obrazu. Utworzysz projekt, dodasz Tagi, nauczysz projekt, a następnie użyjesz w adresie URL punktu końcowego przewidywania projektu do programistycznego testowania. Użyj tego przykładu jako szablonu do tworzenia własnej aplikacji rozpoznawania obrazu.

> [!NOTE]
> Jeśli chcesz skompilować i przeszkolić model klasyfikacji _bez_ pisania kodu, zamiast tego zapoznaj się ze [wskazówkami w przeglądarce](../../getting-started-build-a-classifier.md) .

Użyj biblioteki klienta Custom Vision dla języka Python, aby:

* Tworzenie nowego projektu usługi Custom Vision
* Dodawanie tagów do projektu
* Przekazywanie i Tagi obrazów
* Uczenie projektu
* Opublikuj bieżącą iterację
* Testowanie punktu końcowego przewidywania

[Dokumentacja](/python/api/overview/azure/cognitiveservices/customvision)  |  referencyjna [Kod](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-customvision/azure/cognitiveservices/vision/customvision)  |  źródłowy biblioteki [Pakiet (PyPI)](https://pypi.org/project/azure-cognitiveservices-vision-customvision/)  |  [Przykłady](/samples/browse/?languages=python&products=azure&term=vision&terms=vision)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/cognitive-services/)
* [Python 3.x](https://www.python.org/)
  * Instalacja języka Python powinna obejmować [PIP](https://pip.pypa.io/en/stable/). Aby sprawdzić, czy jest zainstalowany program PIP, należy uruchomić `pip --version` polecenie w wierszu polecenia. Pobierz narzędzie PIP, instalując najnowszą wersję środowiska Python.
* Gdy masz subskrypcję platformy Azure, <a href="https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision"  title=" Utwórz zasób Custom Vision "  target="_blank"> utwórz zasób Custom Vision </a> w Azure Portal, aby utworzyć zasób szkoleniowy i predykcyjny oraz pobrać klucze i punkt końcowy. Zaczekaj na jego wdrożenie i kliknij przycisk **Przejdź do zasobu** .
    * Będziesz potrzebować klucza i punktu końcowego z zasobów, które tworzysz, aby połączyć aplikację z Custom Vision. Twoje klucze i punkt końcowy zostaną wklejone do poniższego kodu w dalszej części przewodnika Szybki Start.
    * Możesz użyć warstwy cenowej bezpłatna ( `F0` ) w celu wypróbowania usługi i później przeprowadzić uaktualnienie do warstwy płatnej dla środowiska produkcyjnego.

## <a name="setting-up"></a>Konfigurowanie

### <a name="install-the-client-library"></a>Zainstaluj bibliotekę kliencką

Aby napisać aplikację do analizy obrazów przy użyciu Custom Vision dla języka Python, potrzebna będzie Custom Vision Biblioteka kliencka. Po zainstalowaniu języka Python Uruchom następujące polecenie w programie PowerShell lub w oknie konsoli:

```powershell
pip install azure-cognitiveservices-vision-customvision
```

### <a name="create-a-new-python-application"></a>Tworzenie nowej aplikacji w języku Python

Utwórz nowy plik w języku Python i zaimportuj następujące biblioteki.

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_imports)]

> [!TIP]
> Chcesz wyświetlić cały plik kodu szybkiego startu jednocześnie? Można je znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/CustomVision/ImageClassification/CustomVisionQuickstart.py), która zawiera przykłady kodu w tym przewodniku Szybki Start.

Utwórz zmienne dla punktu końcowego platformy Azure i kluczy subskrypcji dla zasobu.

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_creds)]

> [!IMPORTANT]
> Przejdź do witryny Azure Portal. Jeśli pomyślnie wdrożono zasoby Custom Vision utworzone w sekcji **wymagania wstępne** , kliknij przycisk **Przejdź do zasobu** w obszarze **następne kroki**. Klucze i punkty końcowe można znaleźć na stronach **klucza i punktu końcowego** zasobów. Musisz pobrać klucze dla zasobów szkoleniowych i predykcyjnych wraz z punktem końcowym interfejsu API dla zasobu szkoleniowego.
>
> Wartość identyfikatora zasobu przewidywania można znaleźć na karcie **Właściwości** zasobu, na liście jako **Identyfikator subskrypcji**.
>
> Pamiętaj, aby usunąć klucze z kodu, gdy skończysz, i nigdy nie Publikuj ich publicznie. W przypadku produkcji należy rozważyć użycie bezpiecznego sposobu przechowywania poświadczeń i uzyskiwania do nich dostępu. Aby uzyskać więcej informacji, zobacz artykuł dotyczący [zabezpieczeń](../../../cognitive-services-security.md) Cognitive Services.

## <a name="object-model"></a>Model obiektów

|Nazwa|Opis|
|---|---|
|[CustomVisionTrainingClient](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.customvisiontrainingclient) | Ta klasa obsługuje tworzenie, uczenie i publikowanie modeli. |
|[CustomVisionPredictionClient](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.prediction.customvisionpredictionclient)| Ta klasa obsługuje zapytania dotyczące modeli na potrzeby prognoz klasyfikacji obrazu.|
|[ImagePrediction](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.prediction.models.imageprediction)| Ta klasa definiuje jednokrotne prognozowanie obiektów na pojedynczym obrazie. Obejmuje ona właściwości identyfikatora obiektu i nazwy, lokalizacji pola ograniczenia obiektu i oceny zaufania.|

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu pokazują, jak wykonać następujące czynności za pomocą biblioteki klienta Custom Vision dla języka Python:

* [Uwierzytelnianie klienta](#authenticate-the-client)
* [Tworzenie nowego projektu usługi Custom Vision](#create-a-new-custom-vision-project)
* [Dodawanie tagów do projektu](#add-tags-to-the-project)
* [Przekazywanie i Tagi obrazów](#upload-and-tag-images)
* [Uczenie projektu](#train-the-project)
* [Opublikuj bieżącą iterację](#publish-the-current-iteration)
* [Testowanie punktu końcowego przewidywania](#test-the-prediction-endpoint)

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

Utwórz wystąpienie klienta szkoleń i prognoz z punktem końcowym i kluczami. Utwórz obiekty **ApiKeyServiceClientCredentials** z kluczami i użyj ich w punkcie końcowym, aby utworzyć obiekt [CustomVisionTrainingClient](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.customvisiontrainingclient) i [CustomVisionPredictionClient](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.prediction.customvisionpredictionclient) .

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_auth)]

## <a name="create-a-new-custom-vision-project"></a>Tworzenie nowego projektu usługi Custom Vision

Dodaj następujący kod do skryptu, aby utworzyć nowy projekt Custom Vision Service. 

Zobacz metodę [create_project](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.operations.customvisiontrainingclientoperationsmixin#create-project-name--description-none--domain-id-none--classification-type-none--target-export-platforms-none--custom-headers-none--raw-false----operation-config-) , aby określić inne opcje podczas tworzenia projektu (wyjaśnione w przewodniku po portalu sieci Web [klasyfikatora](../../getting-started-build-a-classifier.md) ).  

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_create)]

## <a name="add-tags-to-the-project"></a>Dodawanie tagów do projektu

Aby dodać znaczniki klasyfikacji do projektu, Dodaj następujący kod:

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_tags)]


## <a name="upload-and-tag-images"></a>Przekazywanie i Tagi obrazów

Najpierw pobierz przykładowe obrazy dla tego projektu. Zapisz zawartość [folderu Sample images](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/CustomVision/ImageClassification/Images) na urządzeniu lokalnym.

> [!NOTE]
> Potrzebujesz szerszego zestawu obrazów, aby dokończyć szkolenia? Skarbnica, projekt garażu firmy Microsoft, umożliwia zbieranie i kupowanie zestawów obrazów do celów szkoleniowych. Po zebraniu obrazów można je pobrać, a następnie zaimportować do projektu Custom Vision w zwykły sposób. Odwiedź [stronę skarbnica](https://www.microsoft.com/ai/trove?activetab=pivot1:primaryr3) , aby dowiedzieć się więcej.

Aby dodać przykładowe obrazy do projektu, po utworzeniu tagów wstaw następujący kod. Ten kod przekazuje każdy obraz z odpowiednim tagiem. Można przekazać do 64 obrazów w pojedynczej partii.

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_upload)]

> [!NOTE]
> Należy zmienić ścieżkę do obrazów na podstawie lokalizacji, w której pobrano repozytorium przykładów zestawu SDK języka Python Cognitive Services.

## <a name="train-the-project"></a>Uczenie projektu

Ten kod tworzy pierwszą iterację modelu predykcyjnego. 

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_train)]

> [!TIP]
> Uczenie z wybranymi tagami
>
> Opcjonalnie możesz nauczyć tylko podzestaw zastosowanych tagów. Możesz to zrobić, jeśli jeszcze nie zastosowano wystarczającej liczby niektórych tagów, ale masz wystarczającą ilość innych. W wywołaniu **[train_project](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.operations.customvisiontrainingclientoperationsmixin#train-project-project-id--training-type-none--reserved-budget-in-hours-0--force-train-false--notification-email-address-none--selected-tags-none--custom-headers-none--raw-false----operation-config-&preserve-view=true)** Ustaw opcjonalny parametr *selected_tags* na listę ciągów identyfikatorów tagów, których chcesz użyć. Model będzie szkolić, aby rozpoznawał tylko Tagi na tej liście.

## <a name="publish-the-current-iteration"></a>Opublikuj bieżącą iterację

Iteracja nie jest dostępna w punkcie końcowym przewidywania do momentu opublikowania. Poniższy kod powoduje, że bieżąca iteracja modelu jest dostępna na potrzeby wykonywania zapytań. 

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

Następnie można sprawdzić, czy obraz testowy (znaleziony w **<base_image_location>/images/test/**) jest odpowiednio oznakowany. Możesz też wrócić do [witryny internetowej Custom Vision](https://customvision.ai) i wyświetlić bieżący stan nowo utworzonego projektu.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [clean-ic-project](../../includes/clean-ic-project.md)]

## <a name="next-steps"></a>Następne kroki

Teraz dowiesz się, jak każdy krok procesu klasyfikacji obrazu można wykonać w kodzie. Ten przykład wykonuje pojedynczą iterację szkoleniową, ale często należy przeprowadzić uczenie i testowanie modelu wiele razy, aby zwiększyć jego dokładność.

> [!div class="nextstepaction"]
> [Testowanie i ponowne szkolenie modelu](../../test-your-model.md)

* [Co to jest usługa Custom Vision?](../../overview.md)
* Kod źródłowy dla tego przykładu można znaleźć w witrynie [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/CustomVision/ImageClassification/CustomVisionQuickstart.py)
* [Dokumentacja referencyjna zestawu SDK](/python/api/overview/azure/cognitiveservices/customvision)
