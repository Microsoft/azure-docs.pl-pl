---
title: 'Szybki Start: Publikowanie bazy wiedzy, REST i QnA Maker'
description: Ten przewodnik Szybki Start oparty na interfejsie REST umożliwia opublikowanie bazy wiedzy i utworzenie punktu końcowego, który można wywołać w aplikacji lub rozmowie bot.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: how-to
ms.openlocfilehash: e2041fe1ea4f79a951cb78dc3f5d36acdfe0b085
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91777527"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-go"></a>Szybki start: publikowanie bazy wiedzy w usłudze QnA Maker przy użyciu języka Go

Ten przewodnik Szybki start oparty na protokole REST przeprowadzi Cię przez programowe publikowanie bazy wiedzy. Publikowanie powoduje wypchnięcie najnowszej wersji bazy wiedzy do dedykowanego indeksu Wyszukiwanie poznawcze platformy Azure i utworzenie punktu końcowego, który można wywołać w aplikacji lub rozmowie bot.

Ten przewodnik Szybki start wywołuje interfejsy API usługi QnA Maker:
* [Publikowanie](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) — ten interfejs API nie wymaga żadnych informacji zawartych w treści żądania.

## <a name="prerequisites"></a>Wymagania wstępne

* [Środowisko Go w wersji 1.10.1](https://golang.org/dl/)
* Musisz mieć [usługę QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Aby pobrać klucz i punkt końcowy (w tym nazwę zasobu), wybierz pozycję **Szybki Start** dla zasobu w Azure Portal.

* Identyfikator bazy wiedzy QnA Maker (KB) znaleziony w adresie URL w `kbid` parametrze ciągu zapytania, jak pokazano poniżej.

    ![Identyfikator bazy wiedzy usługi QnA Maker](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Jeśli nie masz jeszcze bazy wiedzy, możesz utworzyć przykładową bazę na potrzeby tego podręcznika Szybki start: [Tworzenie nowej bazy wiedzy](create-new-kb-csharp.md).

> [!NOTE]
> Pliki kompletnego rozwiązania są dostępne w [repozytorium GitHub **Azure-Samples/cognitive-services-qnamaker-go**](https://github.com/Azure-Samples/cognitive-services-qnamaker-go/tree/master/documentation-samples/quickstarts/publish-knowledge-base).

## <a name="create-a-go-file"></a>Tworzenie pliku Go

Otwórz program VSCode i utwórz nowy plik o nazwie `publish-kb.go`.

## <a name="add-the-required-dependencies"></a>Dodawanie wymaganych zależności

Na początku pliku `publish-kb.go` dodaj następujące wiersze, aby dodać niezbędne zależności do projektu:

:::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/publish-kb.go" id="dependencies":::

## <a name="create-the-main-function"></a>Tworzenie funkcji main

Poniżej wymaganych zależności dodaj następującą klasę:

```Go
package main

func main() {

}
```

## <a name="add-post-request-to-publish-kb"></a>Dodawanie żądania POST w celu opublikowania bazy wiedzy

Dodaj następujący kod, który wysyła żądanie HTTPS do interfejs API usługi QnA Maker w celu opublikowania bazy wiedzy i otrzymuje odpowiedź:

:::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/publish-kb.go" id="main":::

W przypadku pomyślnego publikowania wywołanie interfejsu API zwraca stan 204 bez jakiejkolwiek zawartości w treści odpowiedzi. Ten kod dodaje zawartość dla odpowiedzi stanu 204.

W przypadku wszystkich innych odpowiedzi są one zwracane w niezmienionej postaci.

## <a name="build-and-run-the-program"></a>Kompilowanie i uruchamianie programu

Wprowadź następujące polecenie, aby skompilować plik. Wiersz polecenia nie zwraca żadnej informacji w przypadku pomyślnej kompilacji.

```bash
go build publish-kb.go
```

Wprowadź następujące polecenie w wierszu polecenia, aby uruchomić program. Spowoduje to wysłanie żądania do interfejsu API usługi QnA Maker w celu opublikowania bazy wiedzy. W przypadku powodzenia zwracany jest kod 204. W przeciwnym razie wyświetlane są błędy.

```bash
./publish-kb
```

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Następne kroki

Po opublikowaniu bazy wiedzy potrzebny jest [adres URL punktu końcowego do wygenerowania odpowiedzi](./get-answer-from-knowledge-base-go.md).

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API Reference (Dokumentacja interfejsu API REST usługi QnA Maker w wersji 4)](https://go.microsoft.com/fwlink/?linkid=2092179)
