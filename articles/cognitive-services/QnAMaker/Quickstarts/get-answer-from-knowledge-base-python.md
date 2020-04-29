---
title: 'Szybki start: uzyskiwanie odpowiedzi z bazy wiedzy — środowisko REST, Python — QnA Maker'
description: W tym samouczku Szybki start opartym na protokole REST i języku Python opisano sposób programowego uzyskiwania odpowiedzi z bazy wiedzy.
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: conceptual
ms.openlocfilehash: e290b97446d441a8218a40b528f9833d45960580
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "78851719"
---
# <a name="quickstart-get-answers-to-a-question-from-a-knowledge-base-with-python"></a>Szybki Start: uzyskiwanie odpowiedzi na pytanie z bazy wiedzy przy użyciu języka Python

Ten przewodnik Szybki start przeprowadzi Cię przez programowe uzyskiwanie odpowiedzi z opublikowanej bazy wiedzy usługi QnA Maker. Baza wiedzy zawiera pytania i odpowiedzi ze [źródeł danych](../Concepts/knowledge-base.md) , takich jak często zadawane pytania. [Pytanie](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) jest wysyłane do usługi QNA Maker. [Odpowiedź](../how-to/metadata-generateanswer-usage.md#generateanswer-response-properties) obejmuje odpowiedzi przewidziane w górnej części.

[Reference documentation](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime) | [Przykład](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/blob/master/documentation-samples/quickstarts/get-answer/get-answer-3x.py) dokumentacji referencyjnej

## <a name="prerequisites"></a>Wymagania wstępne

* [Python w wersji 3.6 lub nowszej](https://www.python.org/downloads/)
* [Visual Studio Code](https://code.visualstudio.com/)
* Musisz mieć [usługę QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Aby uzyskać klucz, wybierz pozycję **Klucze** w obszarze **Zarządzanie zasobami** na pulpicie nawigacyjnym platformy Azure dla zasobu usługi QnA Maker.
* Ustawienia na stronie **Publikowanie**. Jeśli nie masz opublikowanej bazy wiedzy, utwórz pustą bazę wiedzy, zaimportuj bazę wiedzy na stronie **Ustawienia**, a następnie opublikuj. Możesz pobrać [tę podstawową bazę wiedzy](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv) i używać jej.

    Ustawienia strony publikowania obejmują wartość trasy POST, wartość hosta i wartość elementu EndpointKey.

    ![Ustawienia publikowania](../media/qnamaker-quickstart-get-answer/publish-settings.png)

## <a name="create-a-python-file"></a>Tworzenie pliku języka Python

Otwórz program VSCode i utwórz nowy plik o nazwie `get-answer-3x.py`.

## <a name="add-the-required-dependencies"></a>Dodawanie wymaganych zależności

Na początku pliku `get-answer-3x.py` dodaj wymagane zależności do projektu:

[!code-python[Add the required dependencies](~/samples-qnamaker-python/documentation-samples/quickstarts/get-answer/get-answer-3x.py?range=1-2 "Add the required dependencies")]

<!--TBD - reword this following paragraph -->

Host i trasa są inne niż te wyświetlane na stronie **Publikowanie**. Jest to spowodowane tym, że biblioteka języka Python nie zezwala na routing na hoście. Routing, który pojawia się na stronie **Publikowanie** jako część hosta, został przeniesiony do trasy.

## <a name="add-the-required-constants"></a>Dodawanie wymaganych stałych

Dodaj stałe wymagane w celu uzyskiwania dostępu do usługi QnA Maker. Te wartości znajdują się na stronie **Publikowanie** po opublikowaniu bazy wiedzy.

[!code-python[Add the required constants](~/samples-qnamaker-python/documentation-samples/quickstarts/get-answer/get-answer-3x.py?range=5-25 "Add the required constants")]

## <a name="add-a-post-request-to-send-question-and-get-an-answer"></a>Dodawanie żądania POST wysyłającego zapytanie i uzyskującego odpowiedź

Poniższy kod umożliwia wysłanie żądania HTTPS do interfejsu API usługi QnA Maker w celu wysłania zapytania do bazy wiedzy oraz odebrania odpowiedzi:

[!code-python[Add a POST request to send question to knowledge base](~/samples-qnamaker-python/documentation-samples/quickstarts/get-answer/get-answer-3x.py?range=27-48 "Add a POST request to send question to knowledge base")]

Wartość nagłówka `Authorization` zawiera ciąg `EndpointKey`.

## <a name="run-the-program"></a>Uruchamianie programu

Uruchom program z poziomu wiersza polecenia. Automatycznie wyśle on żądanie do interfejsu API usługi QnA Maker, a następnie wyświetli informacje w oknie konsoli.

Uruchom plik:

```bash
python get-answer-3x.py
```

[!INCLUDE [JSON request and response](../../../../includes/cognitive-services-qnamaker-quickstart-get-answer-json.md)]

Dowiedz się więcej na temat [żądania](../how-to/metadata-generateanswer-usage.md#generateanswer-request) i [odpowiedzi](../how-to/metadata-generateanswer-usage.md#generateanswer-response).

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API Reference (Dokumentacja interfejsu API REST usługi QnA Maker w wersji 4)](https://go.microsoft.com/fwlink/?linkid=2092179)