---
title: 'Szybki Start: Publikowanie bazy wiedzy, REST i Python — QnA Maker'
description: Ten przewodnik Szybki Start oparty na interfejsie Python umożliwia opublikowanie bazy wiedzy i utworzenie punktu końcowego, który można wywołać w aplikacji lub rozmowie bot.
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: conceptual
ms.openlocfilehash: 6f053ecbd58d3c2527c1b904437dcc4715c76af1
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "78851653"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-python"></a>Szybki start: publikowanie bazy wiedzy w usłudze QnA Maker przy użyciu języka Python

Ten przewodnik Szybki start oparty na protokole REST przeprowadzi Cię przez programowe publikowanie bazy wiedzy. Publikowanie powoduje wypchnięcie najnowszej wersji bazy wiedzy do dedykowanego indeksu Wyszukiwanie poznawcze platformy Azure i utworzenie punktu końcowego, który można wywołać w aplikacji lub rozmowie bot.

To wywołanie narzędzia do szybkiego startu QnA Maker interfejsy API REST:
* [Publikowanie](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) — ten interfejs API nie wymaga żadnych informacji zawartych w treści żądania.

## <a name="prerequisites"></a>Wymagania wstępne

* [Środowisko Python w wersji 3.7](https://www.python.org/downloads/)
* Musisz mieć [usługę QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Aby pobrać klucz i punkt końcowy (w tym nazwę zasobu), wybierz pozycję **Szybki Start** dla zasobu w Azure Portal.
* Identyfikator bazy wiedzy QnA Maker (KB) znaleziony w adresie URL w `kbid` parametrze ciągu zapytania, jak pokazano poniżej.

    ![Identyfikator bazy wiedzy usługi QnA Maker](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Jeśli nie masz jeszcze bazy wiedzy, możesz utworzyć przykładową bazę na potrzeby tego podręcznika Szybki start: [Tworzenie nowej bazy wiedzy](../how-to/create-knowledge-base.md).

> [!NOTE]
> Kompletne pliki rozwiązania są dostępne w [repozytorium GitHub **Azure-Samples/poznawcze-Services-qnamaker-Python** ](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/tree/master/documentation-samples/quickstarts/publish-knowledge-base).

## <a name="create-a-knowledge-base-python-file"></a>Tworzenie pliku Python bazy wiedzy

Utwórz plik o nazwie `publish-kb-3x.py`.

## <a name="add-the-required-dependencies"></a>Dodawanie wymaganych zależności

Na początku pliku `publish-kb-3x.py` dodaj następujące wiersze, aby dodać niezbędne zależności do projektu:

[!code-python[Add the required dependencies](~/samples-qnamaker-python/documentation-samples/quickstarts/publish-knowledge-base/publish-kb-3x.py?range=1-1 "Add the required dependencies")]

## <a name="add-required-constants"></a>Dodawanie wymaganych stałych

Po poprzednich wymaganych zależnościach dodaj wymagane stałe umożliwiające dostęp do usługi QnA Maker. Przedstawione wartości zastąp własnymi.

[!code-python[Add the required constants](~/samples-qnamaker-python/documentation-samples/quickstarts/publish-knowledge-base/publish-kb-3x.py?range=5-15 "Add the required constants")]

## <a name="add-post-request-to-publish-knowledge-base"></a>Dodawanie żądania POST w celu publikowania bazy wiedzy

Poniżej wymaganych stałych dodaj następujący kod, który wysyła żądanie HTTPS do interfejsu API usługi QnA Maker, aby opublikować bazę wiedzy, oraz odbiera odpowiedź:

[!code-python[Add a POST request to publish knowledge base](~/samples-qnamaker-python/documentation-samples/quickstarts/publish-knowledge-base/publish-kb-3x.py?range=17-26 "Add a POST request to publish knowledge base")]

W przypadku pomyślnego publikowania wywołanie interfejsu API zwraca stan 204 bez jakiejkolwiek zawartości w treści odpowiedzi. Ten kod dodaje zawartość dla odpowiedzi stanu 204.

W przypadku wszystkich innych odpowiedzi są one zwracane w niezmienionej postaci.

## <a name="build-and-run-the-program"></a>Kompilowanie i uruchamianie programu

Wprowadź następujące polecenie w wierszu polecenia, aby uruchomić program. Spowoduje to wysłanie żądania do interfejsu API usługi QnA Maker w celu opublikowania bazy wiedzy. W przypadku powodzenia zwracany jest kod 204. W przeciwnym razie wyświetlane są błędy.

```bash
python publish-kb-3x.py
```

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Następne kroki

Po opublikowaniu bazy wiedzy potrzebny jest [adres URL punktu końcowego do wygenerowania odpowiedzi](./get-answer-from-knowledge-base-python.md).

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API Reference (Dokumentacja interfejsu API REST usługi QnA Maker w wersji 4)](https://go.microsoft.com/fwlink/?linkid=2092179)

[Omówienie usługi QnA Maker](../Overview/overview.md)