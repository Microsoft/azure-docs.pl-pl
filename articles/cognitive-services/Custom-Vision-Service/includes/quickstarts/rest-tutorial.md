---
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-services
ms.date: 12/09/2020
ms.topic: include
ms.openlocfilehash: cea3db080865727ab9b425f14a172041a54a5414
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98947435"
---
Rozpocznij pracę z interfejsem API REST Custom Vision. Wykonaj następujące kroki, aby wywołać interfejs API i utworzyć model klasyfikacji obrazów. Utworzysz projekt, dodasz Tagi, nauczysz projekt, a następnie użyjesz w adresie URL punktu końcowego przewidywania projektu do programistycznego testowania. Użyj tego przykładu jako szablonu do tworzenia własnej aplikacji rozpoznawania obrazu.

> [!NOTE]
> Custom Vision najłatwiej używać za pośrednictwem zestawu SDK biblioteki klienta lub [wskazówek opartych na przeglądarce](../../get-started-build-detector.md).

Użyj biblioteki klienta Custom Vision dla platformy .NET, aby:

* Tworzenie nowego projektu usługi Custom Vision
* Dodawanie tagów do projektu
* Przekazywanie i Tagi obrazów
* Uczenie projektu
* Opublikuj bieżącą iterację
* Testowanie punktu końcowego przewidywania

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/cognitive-services/)
* Gdy masz subskrypcję platformy Azure, <a href="https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision"  title=" Utwórz zasób Custom Vision "  target="_blank"> utwórz zasób Custom Vision <span class="docon docon-navigate-external x-hidden-focus"></span> </a> w Azure Portal, aby utworzyć zasób szkoleniowy i predykcyjny oraz pobrać klucze i punkt końcowy. Zaczekaj na jego wdrożenie i kliknij przycisk **Przejdź do zasobu** .
    * Będziesz potrzebować klucza i punktu końcowego z zasobów, które tworzysz, aby połączyć aplikację z Custom Vision. Klucz i punkt końcowy zostaną wklejone do poniższego kodu w dalszej części przewodnika Szybki Start.
    * Możesz użyć warstwy cenowej bezpłatna ( `F0` ) w celu wypróbowania usługi i później przeprowadzić uaktualnienie do warstwy płatnej dla środowiska produkcyjnego.


## <a name="create-a-new-custom-vision-project"></a>Tworzenie nowego projektu usługi Custom Vision

Użyjesz poniższego polecenia, aby utworzyć projekt klasyfikacji obrazu. Utworzony projekt będzie widoczny w [witrynie sieci web Custom Vision](https://customvision.ai/).


:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="createproject":::

Skopiuj polecenie do edytora tekstów i wprowadź następujące zmiany:

* Zastąp wartość `{subscription key}` prawidłowym kluczem subskrypcji interfejsu API rozpoznawania twarzy.
* Zamień na `{endpoint}` punkt końcowy, który odpowiada kluczowi subskrypcji.
   [!INCLUDE [subdomains-note](../../../../../includes/cognitive-services-custom-subdomains-note.md)]
* Zamień `{name}` na nazwę projektu.
* Opcjonalnie można ustawić inne parametry adresu URL, aby skonfigurować typ modelu, który będzie używany przez projekt. Zobacz [interfejs API CreatProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeae) , aby uzyskać opcje.

Otrzymasz odpowiedź JSON podobną do następującej. Zapisz `"id"` wartość projektu w lokalizacji tymczasowej.

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "string",
  "description": "string",
  "settings": {
    "domainId": "00000000-0000-0000-0000-000000000000",
    "classificationType": "Multiclass",
    "targetExportPlatforms": [
      "CoreML"
    ],
    "useNegativeSet": true,
    "detectionParameters": "string",
    "imageProcessingSettings": {
      "augmentationMethods": {}
    }
  },
  "created": "string",
  "lastModified": "string",
  "thumbnailUri": "string",
  "drModeEnabled": true,
  "status": "Succeeded"
}
```

## <a name="add-tags-to-the-project"></a>Dodawanie tagów do projektu

Użyj poniższego polecenia, aby zdefiniować Tagi, w których będziesz przeszkolić model.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="createtag":::

* Ponownie Wstaw własny klucz i adres URL punktu końcowego.
* Zamień `{projectId}` na własny identyfikator projektu.
* Zamień `{name}` na nazwę tagu, którego chcesz użyć.

Powtórz ten proces dla wszystkich tagów, których chcesz użyć w projekcie. Jeśli używasz przykładowych obrazów, Dodaj Tagi `"Hemlock"` i `"Japanese Cherry"` .

Otrzymasz odpowiedź JSON podobną do poniższej. Zapisz `"id"` wartość każdego tagu w lokalizacji tymczasowej.

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "string",
  "description": "string",
  "type": "Regular",
  "imageCount": 0
}
```

## <a name="upload-and-tag-images"></a>Przekazywanie i Tagi obrazów

Następnie pobierz przykładowe obrazy dla tego projektu. Zapisz zawartość [folderu Sample images](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/CustomVision/ImageClassification/Images) na urządzeniu lokalnym.

> [!NOTE]
> Skarbnica, projekt garażu firmy Microsoft, umożliwia zbieranie i kupowanie zestawów obrazów do celów szkoleniowych. Po zebraniu obrazów można je pobrać, a następnie zaimportować do projektu Custom Vision w zwykły sposób. Odwiedź [stronę skarbnica](https://www.microsoft.com/en-us/ai/trove?activetab=pivot1:primaryr3) , aby dowiedzieć się więcej.

Użyj następującego polecenia, aby przekazać obrazy i zastosować Tagi; jeden raz dla obrazów "Hemlock" i oddzielnie dla obrazów "Japońska wiśni". Więcej opcji można znaleźć w temacie [Tworzenie obrazów z](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb5) interfejsu API danych.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="uploadimages":::

* Ponownie Wstaw własny klucz i adres URL punktu końcowego.
* Zamień `{projectId}` na własny identyfikator projektu.
* Zamień `{tagArray}` na identyfikator tagu.
* Następnie wypełnij treść żądania danymi binarnymi obrazów, które chcesz oznaczyć tagami.

## <a name="train-the-project"></a>Uczenie projektu

Ta metoda pociąga za siebie model na załadowanych obrazach i zwraca identyfikator dla bieżącej iteracji projektu.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="trainproject":::

* Ponownie Wstaw własny klucz i adres URL punktu końcowego.
* Zamień `{projectId}` na własny identyfikator projektu.
* Zamień `{tagArray}` na identyfikator tagu.
* Następnie wypełnij treść żądania danymi binarnymi obrazów, które chcesz oznaczyć tagami.
* Opcjonalnie użyj innych parametrów adresu URL. Zobacz interfejs API [uczenia projektu](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc7548b571998fddee1) , aby uzyskać opcje.

> [!TIP]
> Uczenie z wybranymi tagami
>
> Opcjonalnie możesz nauczyć tylko podzestaw zastosowanych tagów. Możesz to zrobić, jeśli jeszcze nie zastosowano wystarczającej liczby niektórych tagów, ale masz wystarczającą ilość innych. Dodaj opcjonalną zawartość JSON do treści żądania. Wypełnij `"selectedTags"` tablicę identyfikatorami tagów, których chcesz użyć.
> ```json
> {
>   "selectedTags": [
>     "00000000-0000-0000-0000-000000000000"
>   ]
> }
> ```

Odpowiedź JSON zawiera informacje o przeszkolonym projekcie, łącznie z IDENTYFIKATORem iteracji ( `"id"` ). Zapisz tę wartość w następnym kroku.

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "string",
  "status": "string",
  "created": "string",
  "lastModified": "string",
  "trainedAt": "string",
  "projectId": "00000000-0000-0000-0000-000000000000",
  "exportable": true,
  "exportableTo": [
    "CoreML"
  ],
  "domainId": "00000000-0000-0000-0000-000000000000",
  "classificationType": "Multiclass",
  "trainingType": "Regular",
  "reservedBudgetInHours": 0,
  "trainingTimeInMinutes": 0,
  "publishName": "string",
  "originalPublishResourceId": "string"
}
```

## <a name="publish-the-current-iteration"></a>Opublikuj bieżącą iterację

Ta metoda powoduje, że bieżąca iteracja modelu jest dostępna na potrzeby wykonywania zapytań. Nazwa zwracanego modelu jest używana jako odwołanie do wysyłania żądań przewidywania. 

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="publish":::

* Ponownie Wstaw własny klucz i adres URL punktu końcowego.
* Zamień `{projectId}` na własny identyfikator projektu.
* Zamień na `{iterationId}` Identyfikator zwrócony w poprzednim kroku.
* Zamień na `{publishedName}` nazwę, którą chcesz przypisać do modelu predykcyjnego.
* Zamień `{predictionId}` na własny identyfikator zasobu predykcyjnego. Można je znaleźć na karcie **Przegląd** zasobów predykcyjnych w Azure Portal, na liście jako **Identyfikator subskrypcji**.
* Opcjonalnie użyj innych parametrów adresu URL. Zapoznaj się z interfejsem API [publikowania iteracji](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc7548b571998fdded5) .

## <a name="test-the-prediction-endpoint"></a>Testowanie punktu końcowego przewidywania

Na koniec Użyj tego polecenia, aby przetestować szkolony model przez przekazanie nowego obrazu do klasyfikowania przy użyciu tagów. Możesz użyć obrazu w folderze "test" pobranych wcześniej plików przykładowych.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="publish":::

* Ponownie Wstaw własny klucz i adres URL punktu końcowego.
* Zamień `{projectId}` na własny identyfikator projektu.
* Zamień `{publishedName}` na nazwę użytą w poprzednim kroku.
* Dodaj dane binarne lokalnego obrazu do treści żądania.
* Opcjonalnie użyj innych parametrów adresu URL. Zobacz API [klasyfikowanie obrazu](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.1/operations/5eb37d24548b571998fde5f3) .

Zwrócona odpowiedź JSON będzie mieć co najmniej każdy tag, który został zastosowany do obrazu, wraz z wynikami prawdopodobieństwa dla każdego znacznika. 

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "project": "00000000-0000-0000-0000-000000000000",
  "iteration": "00000000-0000-0000-0000-000000000000",
  "created": "string",
  "predictions": [
    {
      "probability": 0.0,
      "tagId": "00000000-0000-0000-0000-000000000000",
      "tagName": "string",
      "boundingBox": {
        "left": 0.0,
        "top": 0.0,
        "width": 0.0,
        "height": 0.0
      },
      "tagType": "Regular"
    }
  ]
}
```

[!INCLUDE [clean-ic-project](../../includes/clean-ic-project.md)]

## <a name="next-steps"></a>Następne kroki

Teraz każdy krok procesu klasyfikacji obrazu można wykonać przy użyciu interfejsu API REST. Ten przykład wykonuje pojedynczą iterację szkoleniową, ale często należy przeprowadzić uczenie i testowanie modelu wiele razy, aby zwiększyć jego dokładność.

> [!div class="nextstepaction"]
> [Testowanie i ponowne szkolenie modelu](../../test-your-model.md)

* [Co to jest usługa Custom Vision?](../../overview.md)
* [Dokumentacja interfejsu API (szkolenie)](/dotnet/api/overview/azure/cognitiveservices/client/customvision)
* [Dokumentacja interfejsu API (przewidywania)](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeae)
