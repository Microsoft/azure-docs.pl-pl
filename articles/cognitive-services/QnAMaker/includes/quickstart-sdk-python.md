---
ms.openlocfilehash: 13dcdec4eff72f97e1af8ecb26e32c0ef5f881fd
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81637064"
---

Użyj biblioteki klienta QnA Maker dla języka Python, aby:

* Tworzenie bazy wiedzy
* Zarządzanie bazą wiedzy
* Publikowanie bazy wiedzy

[Dokumentacja](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker?view=azure-python) | [dokumentacja dokumentacji Biblioteka kod](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-knowledge-qnamaker) | [źródłowy Pakiet (pypi)](https://pypi.org/project/azure-cognitiveservices-knowledge-qnamaker/) | Python[przykłady](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/blob/master/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [utwórz bezpłatną subskrypcję platformy Azure](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-qna-maker-azure-resource"></a>Tworzenie zasobu platformy Azure programu QnA Maker

Usługi Azure Cognitive Services są reprezentowane przez zasoby platformy Azure, które subskrybujesz. Utwórz zasób dla programu QnA Maker przy użyciu [witryny Azure portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) lub [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na komputerze lokalnym.

Po otrzymaniu klucza z zasobu [utwórz zmienne środowiskowe](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) dla zasobu o nazwie `QNAMAKER_KEY` i `QNAMAKER_HOST`. Użyj wartości klucza i punktu końcowego w witrynie Azure portal.

### <a name="install-the-python-library-for-qna-maker"></a>Instalowanie biblioteki języka Python dla programu QnA Maker

Po zainstalowaniu języka Python można zainstalować bibliotekę klienta za pomocą:

```console
pip install azure-cognitiveservices-knowledge-qnamaker
```

## <a name="object-model"></a>Model obiektu

Utwórz [obiekt CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) za pomocą klucza i użyj go z punktem końcowym, aby utworzyć obiekt [QnAMakerClient.](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.qn_amaker_client?view=azure-python)

Po utworzeniu klienta użyj [bazy wiedzy,](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.operations.knowledgebase_operations?view=azure-python) aby utworzyć bazę wiedzy, zarządzać nią i publikować je.

W przypadku operacji natychmiastowych metoda zwykle zwraca obiekt JSON wskazujący stan. W przypadku długotrwałych operacji odpowiedzią jest identyfikator operacji. Zadzwoń do [klienta. Operations.getDetails](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.models.operationstatetype?view=azure-python) metoda z identyfikatorem operacji w celu określenia [stanu żądania](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.operationstatetype?view=azure-python).


## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu pokazują, jak wykonać następujące czynności za pomocą biblioteki klienta QnA Maker dla języka Python:

* [Tworzenie bazy wiedzy](#create-a-knowledge-base)
* [Aktualizowanie bazy wiedzy](#update-a-knowledge-base)
* [Publikowanie bazy wiedzy](#publish-a-knowledge-base)
* [Pobieranie bazy wiedzy](#download-a-knowledge-base)
* [Usuwanie bazy wiedzy](#delete-a-knowledge-base)
* [Uzyskaj stan operacji](#get-status-of-an-operation)

## <a name="create-a-new-python-application"></a>Tworzenie nowej aplikacji języka Python

Utwórz nową aplikację Języka Python w preferowanym edytorze lub IDE. Następnie zaimportuj następujące biblioteki.

[!code-python[Dependencies](~/samples-qnamaker-python/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py?name=dependencies)]

Tworzenie zmiennych dla punktu końcowego i klucza platformy Azure zasobu. Jeśli po uruchomieniu aplikacji utworzono zmienną środowiskową, należy zamknąć i ponownie otworzyć edytor, IDE lub powłokę uruchamianą ją, aby uzyskać dostęp do zmiennej.

|Zmienna środowiskowa|zmienna|Przykład|
|--|--|--|
|`QNAMAKER_KEY`|`subscription_key`|Klucz jest ciągiem znaków 32 i jest dostępny w witrynie Azure portal, w zasobie QnA Maker, na stronie Szybki start. To nie jest taka sama jak klucz punktu końcowego przewidywania.|
|`QNAMAKER_HOST`|`host`| Punkt końcowy tworzenia w formacie `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`, zawiera **nazwę zasobu**. Nie jest to ten sam adres URL, który jest używany do wykonywania zapytań o punkt końcowy przewidywania.|

[!code-python[Azure resource variables](~/samples-qnamaker-python/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py?name=resourcekeys)]

## <a name="authenticate-the-client"></a>Uwierzytelnij klienta

Następnie utwórz obiekt CognitiveServicesCredentials za pomocą klucza i użyj go z punktem końcowym, aby utworzyć obiekt [QnAMakerClient.](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-python)


[!code-python[Authorization to resource key](~/samples-qnamaker-python/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py?name=authorization)]

## <a name="create-a-knowledge-base"></a>Tworzenie bazy wiedzy

 Użyj obiektu klienta, aby uzyskać obiekt [operacji bazy wiedzy.](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.operations.knowledgebase_operations?view=azure-python)

Baza wiedzy przechowuje pary pytań i odpowiedzi dla obiektu [CreateKbDTO](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.models.create_kb_dto?view=azure-python) z trzech źródeł:

* W przypadku **zawartości redakcyjnej**użyj obiektu [QnADTO.](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.models.qn_adto?view=azure-python)
* W przypadku **plików**użyj obiektu [FileDTO.](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.models.file_dto?view=azure-python)
* W przypadku **adresów URL**użyj listy ciągów.

Wywołanie metody [tworzenia](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.operations.knowledgebase_operations.knowledgebaseoperations?view=azure-python#create-create-kb-payload--custom-headers-none--raw-false----operation-config-) następnie przekazać zwrócony identyfikator operacji do [Operations.getDetails](#get-status-of-an-operation) metody sondowania stanu.

[!code-python[Create a knowledge base](~/samples-qnamaker-python/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py?name=createkb&highlight=15)]

Upewnij się, [`_monitor_operation`](#get-status-of-an-operation) że funkcja dołączyć się w powyższym kodzie, aby pomyślnie utworzyć bazę wiedzy.

## <a name="update-a-knowledge-base"></a>Aktualizowanie bazy wiedzy

Bazę wiedzy można zaktualizować, przekazując identyfikator bazy wiedzy i [updateKbOperationDTO](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.models.update_kb_operation_dto?view=azure-python) zawierającą [dodawanie,](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.models.update_kb_operation_dto_add?view=azure-python) [aktualizowanie](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.models.update_kb_operation_dto_update?view=azure-python)i [usuwanie](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.models.update_kb_operation_dto_delete?view=azure-python) obiektów DTO do metody [aktualizacji.](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.operations.knowledgebase_operations.knowledgebaseoperations?view=azure-python#update-kb-id--update-kb--custom-headers-none--raw-false----operation-config-) Użyj [Operation.getDetail](#get-status-of-an-operation) metody, aby ustalić, czy aktualizacja powiodła się.

[!code-python[Update a knowledge base](~/samples-qnamaker-python/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py?name=updatekb&highlight=2)]

Upewnij się, [`_monitor_operation`](#get-status-of-an-operation) że funkcja, o której mowa w powyższym kodzie, w celu pomyślnej aktualizacji bazy wiedzy.

## <a name="publish-a-knowledge-base"></a>Publikowanie bazy wiedzy

Opublikuj bazę wiedzy przy użyciu metody [publikowania.](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.operations.knowledgebase_operations.knowledgebaseoperations?view=azure-python#publish-kb-id--custom-headers-none--raw-false----operation-config-) Spowoduje to uwzględnione bieżące zapisane i przeszkolony model, do którego odwołuje się identyfikator bazy wiedzy, i publikuje go w punkcie końcowym.

[!code-python[Publish a knowledge base](~/samples-qnamaker-python/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py?name=publishkb&highlight=2)]

## <a name="download-a-knowledge-base"></a>Pobieranie bazy wiedzy

Użyj metody [pobierania,](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.operations.knowledgebase_operations.knowledgebaseoperations?view=azure-python#download-kb-id--environment--custom-headers-none--raw-false----operation-config-) aby pobrać bazę danych jako listę [QnADocumentsDTO](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.models.qn_adocuments_dto?view=azure-python). _Nie_ jest to równoważne eksportowi portalu QnA Maker ze strony **Ustawienia,** ponieważ wynikiem tej metody nie jest plik TSV.

[!code-python[Download a knowledge base](~/samples-qnamaker-python/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py?name=downloadkb&highlight=2)]

## <a name="delete-a-knowledge-base"></a>Usuwanie bazy wiedzy

Usuń bazę wiedzy przy użyciu metody [usuwania](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.operations.knowledgebase_operations.knowledgebaseoperations?view=azure-python#delete-kb-id--custom-headers-none--raw-false----operation-config-) z parametrem identyfikatora bazy wiedzy.

[!code-python[Delete a knowledge base](~/samples-qnamaker-python/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py?name=deletekb&highlight=2)]

## <a name="get-status-of-an-operation"></a>Uzyskaj stan operacji

Niektóre metody, takie jak tworzenie i aktualizowanie, może zająć wystarczająco dużo czasu, że zamiast czekać na zakończenie procesu, zwracana jest [operacja.](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.models.operation.operation?view=azure-python) Użyj identyfikatora operacji z operacji do sondowania (z logiką ponawiania próby), aby określić stan oryginalnej metody.

Wywołanie _setTimeout_ w następującym bloku kodu służy do symulowania kodu asynchroniowego. Zamień to na logikę ponawiania próby.

[!code-python[Monitor an operation](~/samples-qnamaker-python/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py?name=monitorOperation&highlight=7)]

## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację `python knowledgebase_quickstart.py` za pomocą polecenia z katalogu aplikacji.

Wszystkie fragmenty kodu w tym artykule są [dostępne](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/blob/master/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py) i mogą być uruchamiane jako pojedynczy plik.

```console
python knowledgebase_quickstart.py
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję usług Cognitive Services, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów powoduje również usunięcie innych skojarzonych z nią zasobów.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfejs wiersza polecenia platformy Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)
