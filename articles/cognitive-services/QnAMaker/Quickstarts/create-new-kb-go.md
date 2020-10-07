---
title: 'Szybki start: tworzenie bazy wiedzy — środowisko REST, Go — QnA Maker'
description: Ten oparty na protokole REST przewodnik Szybki start dla języka Go zawiera omówienie programowego tworzenia przykładowej bazy wiedzy usługi QnA Maker, która będzie wyświetlana na pulpicie nawigacyjnym platformy Azure w ramach konta interfejsu API usługi Cognitive Services.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.date: 12/16/2019
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: how-to
ms.openlocfilehash: be314ad74fbb58709f5707e84b193f8ca69b47d9
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91777708"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-go"></a>Szybki start: tworzenie bazy wiedzy w usłudze QnA Maker przy użyciu języka Go

Ten przewodnik Szybki start przeprowadzi Cię przez programowe tworzenie przykładowej bazy wiedzy usługi QnA Maker. Usługa QnA Maker automatycznie wyodrębnia pytania i odpowiedzi z częściowo ustrukturyzowanej zawartości, na przykład często zadawanych pytań, ze [źródeł danych](../Concepts/knowledge-base.md). Model bazy wiedzy jest zdefiniowany w formacie JSON wysyłanym w treści żądania interfejsu API.

Ten przewodnik Szybki start wywołuje interfejsy API usługi QnA Maker:
* [Tworzenie bazy wiedzy](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create)
* [Pobieranie szczegółów operacji](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/operations/getdetails)

[Dokumentacja](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase)  |  referencyjna [Przejdź przykład](https://github.com/Azure-Samples/cognitive-services-qnamaker-go/blob/master/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* [Środowisko Go w wersji 1.10.1](https://golang.org/dl/)
* Musisz mieć [usługę QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Aby pobrać klucz i punkt końcowy (w tym nazwę zasobu), wybierz pozycję **Szybki Start** dla zasobu w Azure Portal.

## <a name="create-a-knowledge-base-go-file"></a>Tworzenie pliku Go bazy wiedzy

Utwórz plik o nazwie `create-new-knowledge-base.go`.

## <a name="add-the-required-dependencies"></a>Dodawanie wymaganych zależności

Na początku pliku `create-new-knowledge-base.go` dodaj następujące wiersze, aby dodać niezbędne zależności do projektu:

:::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/create-kb.go" id="dependencies":::

## <a name="add-the-kb-model-definition"></a>Dodawanie definicji modelu bazy wiedzy
Po stałych dodaj następującą definicję modelu bazy wiedzy. Po definicji model jest konwertowany na ciąg.

:::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/create-kb.go" id="model":::

## <a name="add-supporting-structures-and-functions"></a>Dodawanie pomocniczych struktur i funkcji

Następnie dodaj poniższe funkcje pomocnicze.

1. Dodaj strukturę dla odpowiedzi HTTP:

    :::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/create-kb.go" id="response":::

1. Dodaj następującą metodę w celu obsługi żądań POST wysyłanych do interfejsów API usługi QnA Maker. W tym przewodniku Szybki start żądanie POST służy do wysłania definicji bazy wiedzy do usługi QnA Maker.

    :::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/create-kb.go" id="post":::

1. Dodaj następującą metodę w celu obsługi żądań GET wysyłanych do interfejsów API usługi QnA Maker. W tym przewodniku Szybki start żądanie GET służy do sprawdzenia stanu operacji tworzenia.

    :::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/create-kb.go" id="get":::

## <a name="add-function-to-create-kb"></a>Dodawanie funkcji tworzącej bazę wiedzy

Dodaj następujące funkcje, aby spowodować, że żądanie HTTP POST utworzy bazę wiedzy. **Identyfikator operacji** _tworzenia_ jest zwracany w **lokalizacji**pola nagłówka odpowiedzi post, a następnie używany jako część trasy w żądaniu get. `Ocp-Apim-Subscription-Key` to klucz usługi QnA Maker używany do uwierzytelniania.

:::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/create-kb.go" id="create_kb":::

To wywołanie interfejsu API zwraca odpowiedź w formacie JSON, która zawiera identyfikator operacji. Użyj tego identyfikatora operacji, aby określić, czy baza wiedzy została pomyślnie utworzona.

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:19:01Z",
  "lastActionTimestamp": "2018-09-26T05:19:01Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "8dfb6a82-ae58-4bcb-95b7-d1239ae25681"
}
```

## <a name="add-function-to-get-status"></a>Dodawanie funkcji pobierającej stan

Dodaj następującą funkcję, aby spowodować, że żądanie HTTP GET sprawdzi stan operacji. `Ocp-Apim-Subscription-Key` to klucz usługi QnA Maker używany do uwierzytelniania.

:::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/create-kb.go" id="get_status":::

Powtarzaj wywołanie do momentu uzyskania stanu powodzenia lub niepowodzenia:

```JSON
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:23:08Z",
  "resourceLocation": "/knowledgebases/XXX7892b-10cf-47e2-a3ae-e40683adb714",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "177e12ff-5d04-4b73-b594-8575f9787963"
}
```
## <a name="add-main-function"></a>Dodawanie funkcji main

Następująca funkcja jest główną funkcją i tworzy bazę wiedzy oraz powtarza sprawdzenie stanu. Tworzenie bazy wiedzy może nieco potrwać, dlatego musisz powtarzać wywołania sprawdzenia stanu do czasu uzyskania stanu powodzenia lub niepowodzenia.

:::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/create-kb.go" id="main":::


## <a name="compile-the-program"></a>Kompilowanie programu
Wprowadź następujące polecenie, aby skompilować plik. Wiersz polecenia nie zwraca żadnej informacji w przypadku pomyślnej kompilacji.

```bash
go build create-new-knowledge-base.go
```

## <a name="run-the-program"></a>Uruchamianie programu

Wprowadź następujące polecenie w wierszu polecenia, aby uruchomić program. Program wyśle żądanie do interfejsu API usługi QnA Maker, aby utworzyć bazę wiedzy, a następnie będzie sondować wyniki co 30 sekund. Każda odpowiedź jest wypisywana w oknie konsoli.

```bash
go run create-new-knowledge-base
```

Utworzoną bazę wiedzy można wyświetlić w portalu usługi QnA Maker, na stronie [My knowledge bases (Moje bazy wiedzy)](https://www.qnamaker.ai/Home/MyServices).

[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API Reference (Dokumentacja interfejsu API REST usługi QnA Maker w wersji 4)](https://go.microsoft.com/fwlink/?linkid=2092179)
