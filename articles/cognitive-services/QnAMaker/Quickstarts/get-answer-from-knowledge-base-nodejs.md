---
title: 'Szybki start: uzyskiwanie odpowiedzi z bazy wiedzy — środowisko REST, Node.js — QnA Maker'
description: W tym samouczku Szybki start opartym na protokole REST i środowisku Node.js opisano sposób programowego uzyskiwania odpowiedzi z bazy wiedzy.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: how-to
ms.openlocfilehash: f14a2f4ffcc0c136e0770235003cbb2b6f4ff9f8
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91777583"
---
# <a name="quickstart-get-answers-to-a-question-from-a-knowledge-base-with-nodejs"></a>Szybki Start: uzyskiwanie odpowiedzi na pytanie z bazy wiedzy z Node.js

Ten przewodnik Szybki start przeprowadzi Cię przez programowe uzyskiwanie odpowiedzi z opublikowanej bazy wiedzy usługi QnA Maker. Baza wiedzy zawiera pytania i odpowiedzi ze [źródeł danych](../Concepts/knowledge-base.md) , takich jak często zadawane pytania. [Pytanie](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) jest wysyłane do usługi QNA Maker. [Odpowiedź](../how-to/metadata-generateanswer-usage.md#generateanswer-response-properties) obejmuje odpowiedzi przewidziane w górnej części.

[Dokumentacja](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime)  |  referencyjna [Przykład](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/blob/master/documentation-samples/quickstarts/get-answer/get-answer.js)

## <a name="prerequisites"></a>Wymagania wstępne

* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/)
* Musisz mieć [usługę QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Aby uzyskać klucz, wybierz pozycję **Klucze** w obszarze **Zarządzanie zasobami** na pulpicie nawigacyjnym platformy Azure dla zasobu usługi QnA Maker.
* Ustawienia na stronie **Publikowanie**. Jeśli nie masz opublikowanej bazy wiedzy, utwórz pustą bazę wiedzy, zaimportuj bazę wiedzy na stronie **Ustawienia**, a następnie opublikuj. Możesz pobrać [tę podstawową bazę wiedzy](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv) i używać jej.

    Ustawienia strony publikowania obejmują wartość trasy POST, wartość hosta i wartość elementu EndpointKey.

    ![Ustawienia publikowania](../media/qnamaker-quickstart-get-answer/publish-settings.png)

## <a name="create-a-nodejs-file"></a>Tworzenie pliku środowiska Node.js

Otwórz program VSCode i utwórz nowy plik o nazwie `get-answer.js`.

## <a name="add-the-required-dependencies"></a>Dodawanie wymaganych zależności

Na początku pliku `get-answer.js` dodaj wymagane zależności do projektu:

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/query-kb.js" id="dependencies":::

## <a name="add-the-required-constants"></a>Dodawanie wymaganych stałych

Następnie dodaj stałe wymagane w celu uzyskiwania dostępu do usługi QnA Maker. Te wartości znajdują się na stronie **Publikowanie** po opublikowaniu bazy wiedzy.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/query-kb.js" id="constants":::

## <a name="add-a-post-request-to-send-question-and-get-an-answer"></a>Dodawanie żądania POST wysyłającego zapytanie i uzyskującego odpowiedź

Poniższy kod umożliwia wysłanie żądania HTTPS do interfejsu API usługi QnA Maker w celu wysłania zapytania do bazy wiedzy oraz odebrania odpowiedzi:

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/query-kb.js" id="query":::

## <a name="run-the-program"></a>Uruchamianie programu

Uruchom program z wiersza polecenia. Automatycznie wyśle on żądanie do interfejsu API usługi QnA Maker, a następnie wyświetli informacje w oknie konsoli.

Uruchom plik:

```bash
node get-answer.js
```

[!INCLUDE [JSON request and response](../../../../includes/cognitive-services-qnamaker-quickstart-get-answer-json.md)]

Dowiedz się więcej na temat [żądania](../how-to/metadata-generateanswer-usage.md#generateanswer-request) i [odpowiedzi](../how-to/metadata-generateanswer-usage.md#generateanswer-response).

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API Reference (Dokumentacja interfejsu API REST usługi QnA Maker w wersji 4)](https://go.microsoft.com/fwlink/?linkid=2092179)
