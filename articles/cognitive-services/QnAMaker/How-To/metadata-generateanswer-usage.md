---
title: Metadane z interfejsem API GenerateAnswer — QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker umożliwia dodawanie metadanych w formie par klucz/wartość do par pytań/odpowiedzi. Można filtrować wyniki do zapytań użytkowników i przechowywać dodatkowe informacje, które mogą być używane w konwersacjach z monitami.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 9d2100dbc2c5f24742a949778a1b7450bf303c5f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103232209"
---
# <a name="get-an-answer-with-the-generateanswer-api"></a>Uzyskaj odpowiedź przy użyciu interfejsu API GenerateAnswer

Aby uzyskać przewidywaną odpowiedź na pytanie użytkownika, użyj interfejsu API GenerateAnswer. Po opublikowaniu bazy wiedzy można wyświetlić informacje na temat sposobu korzystania z tego interfejsu API na stronie **Publikowanie** . Możesz również skonfigurować interfejs API do filtrowania odpowiedzi na podstawie znaczników metadanych i przetestować bazę wiedzy z punktu końcowego za pomocą parametru test ciągu zapytania.

<a name="generateanswer-api"></a>

## <a name="get-answer-predictions-with-the-generateanswer-api"></a>Uzyskiwanie prognoz odpowiedzi przy użyciu interfejsu API GenerateAnswer

W celu uzyskania najlepszego dopasowania od par pytań i odpowiedzi używasz [interfejsu API GenerateAnswer](/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer) w bot lub aplikacji w celu zbadania bazy wiedzy przy użyciu pytania użytkownika.

<a name="generateanswer-endpoint"></a>

## <a name="publish-to-get-generateanswer-endpoint"></a>Opublikuj, aby uzyskać punkt końcowy GenerateAnswer

Po opublikowaniu bazy wiedzy z poziomu [portalu QNA Maker](https://www.qnamaker.ai)lub przy użyciu [interfejsu API](/rest/api/cognitiveservices/qnamaker/knowledgebase/publish)można uzyskać szczegółowe informacje o punkcie końcowym usługi GenerateAnswer.

Aby uzyskać szczegółowe informacje o punkcie końcowym:
1. Zaloguj się do witryny [https://www.qnamaker.ai](https://www.qnamaker.ai).
1. W obszarze **Moje bazy wiedzy** wybierz pozycję **Wyświetl kod** dla bazy wiedzy.
    ![Zrzut ekranu przedstawiający moje bazy wiedzy](../media/qnamaker-how-to-metadata-usage/my-knowledge-bases.png)
1. Pobierz szczegóły punktu końcowego GenerateAnswer.

    # <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/v1)

    ![Zrzut ekranu przedstawiający szczegóły punktu końcowego](../media/qnamaker-how-to-metadata-usage/view-code.png)

    # <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/v2)

    ![Zrzut ekranu z zarządzanymi szczegółami punktu końcowego](../media/qnamaker-how-to-metadata-usage/view-code-managed.png)

    ---

Możesz również uzyskać szczegółowe informacje o punkcie końcowym z karty **Ustawienia** bazy wiedzy.

<a name="generateanswer-request"></a>

## <a name="generateanswer-request-configuration"></a>Konfiguracja żądania GenerateAnswer

Wywołanie GenerateAnswer z żądaniem HTTP POST. Przykładowy kod, który pokazuje, jak wywoływać GenerateAnswer, można znaleźć w [przewodnikach szybki start](../quickstarts/quickstart-sdk.md#generate-an-answer-from-the-knowledge-base).

Żądanie POST używa:

* Wymagane [Parametry identyfikatora URI](/rest/api/cognitiveservices/qnamakerruntime/runtime/train#uri-parameters)
* Wymagana właściwość nagłówka, w `Authorization` przypadku zabezpieczeń
* Wymagane [właściwości treści](/rest/api/cognitiveservices/qnamakerruntime/runtime/train#feedbackrecorddto).

Adres URL GenerateAnswer ma następujący format:

```
https://{QnA-Maker-endpoint}/knowledgebases/{knowledge-base-ID}/generateAnswer
```

Należy pamiętać, aby ustawić właściwość nagłówka HTTP z `Authorization` wartością ciągu `EndpointKey` z końcowym miejscem, a następnie na stronie **Ustawienia** znajdował się klucz punktu końcowego.

Przykładowa treść JSON wygląda następująco:

```json
{
    "question": "qna maker and luis",
    "top": 6,
    "isTest": true,
    "scoreThreshold": 30,
    "rankerType": "" // values: QuestionOnly
    "strictFilters": [
    {
        "name": "category",
        "value": "api"
    }],
    "userId": "sd53lsY="
}
```

Dowiedz się więcej o [rankerType](../concepts/best-practices.md#choosing-ranker-type).

Poprzedni kod JSON zażądał tylko odpowiedzi o wartości co najmniej 30% lub wyższej.

<a name="generateanswer-response"></a>

## <a name="generateanswer-response-properties"></a>Właściwości odpowiedzi GenerateAnswer

[Odpowiedź](/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#successful-query) to obiekt JSON obejmujący wszystkie informacje potrzebne do wyświetlenia odpowiedzi i następnego włączenia konwersacji, jeśli jest dostępny.

```json
{
    "answers": [
        {
            "score": 38.54820341616869,
            "Id": 20,
            "answer": "There is no direct integration of LUIS with QnA Maker. But, in your bot code, you can use LUIS and QnA Maker together. [View a sample bot](https://github.com/Microsoft/BotBuilder-CognitiveServices/tree/master/Node/samples/QnAMaker/QnAWithLUIS)",
            "source": "Custom Editorial",
            "questions": [
                "How can I integrate LUIS with QnA Maker?"
            ],
            "metadata": [
                {
                    "name": "category",
                    "value": "api"
                }
            ]
        }
    ]
}
```

Powyższy kod JSON odpowiedział z odpowiedzią z wynikiem 38,5%.

## <a name="match-questions-only-by-text"></a>Dopasuj tylko pytania według tekstu

Domyślnie QnA Maker przeszukuje pytania i odpowiedzi. Jeśli chcesz wyszukać tylko pytania, aby wygenerować odpowiedź, użyj `RankerType=QuestionOnly` wpisu w treści post żądania GenerateAnswer.

Możesz przeszukiwać opublikowaną KB, używając `isTest=false` lub test KB przy użyciu `isTest=true` .

```json
{
  "question": "Hi",
  "top": 30,
  "isTest": true,
  "RankerType":"QuestionOnly"
}

```
## <a name="use-qna-maker-with-a-bot-in-c"></a>Używanie QnA Maker z bot w języku C #

Bot Framework zapewnia dostęp do właściwości QnA Maker za pomocą [interfejsu API getanswer](/dotnet/api/microsoft.bot.builder.ai.qna.qnamaker.getanswersasync#Microsoft_Bot_Builder_AI_QnA_QnAMaker_GetAnswersAsync_Microsoft_Bot_Builder_ITurnContext_Microsoft_Bot_Builder_AI_QnA_QnAMakerOptions_System_Collections_Generic_Dictionary_System_String_System_String__System_Collections_Generic_Dictionary_System_String_System_Double__):

```csharp
using Microsoft.Bot.Builder.AI.QnA;
var metadata = new Microsoft.Bot.Builder.AI.QnA.Metadata();
var qnaOptions = new QnAMakerOptions();

metadata.Name = Constants.MetadataName.Intent;
metadata.Value = topIntent;
qnaOptions.StrictFilters = new Microsoft.Bot.Builder.AI.QnA.Metadata[] { metadata };
qnaOptions.Top = Constants.DefaultTop;
qnaOptions.ScoreThreshold = 0.3F;
var response = await _services.QnAServices[QnAMakerKey].GetAnswersAsync(turnContext, qnaOptions);
```

Poprzedni kod JSON zażądał tylko odpowiedzi o wartości co najmniej 30% lub wyższej.

## <a name="use-qna-maker-with-a-bot-in-nodejs"></a>Użyj QnA Maker z bot w Node.js

Bot Framework zapewnia dostęp do właściwości QnA Maker za pomocą [interfejsu API getanswer](/javascript/api/botbuilder-ai/qnamaker?preserve-view=true&view=botbuilder-ts-latest#generateanswer-string---undefined--number--number-):

```javascript
const { QnAMaker } = require('botbuilder-ai');
this.qnaMaker = new QnAMaker(endpoint);

// Default QnAMakerOptions
var qnaMakerOptions = {
    ScoreThreshold: 0.30,
    Top: 3
};
var qnaResults = await this.qnaMaker.getAnswers(stepContext.context, qnaMakerOptions);
```

Poprzedni kod JSON zażądał tylko odpowiedzi o wartości co najmniej 30% lub wyższej.

## <a name="get-precise-answers-with-generateanswer-api"></a>Uzyskaj precyzyjne odpowiedzi przy użyciu interfejsu API GenerateAnswer

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/v1)

Oferujemy precyzyjną funkcję odpowiedzi tylko w przypadku wersji zarządzanej QnA Maker.

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/v2)

Użytkownik może włączyć [precyzyjne odpowiedzi](../reference-precise-answering.md) podczas korzystania z zarządzanego zasobu QNA Maker. Parametr answerSpanRequest musi zostać zaktualizowany dla tego samego elementu.

```json
{
    "question": "How long it takes to charge surface pro 4?",
    "top": 3,
    "answerSpanRequest": {
        "enable": true,
        "topAnswersWithSpan": 1
    }
}
```

Podobnie użytkownicy mogą wyłączyć precyzyjne odpowiedzi, nie ustawiając parametru answerSpanRequest.

```json
{
    "question": "How long it takes to charge surface pro 4?",
    "top": 3
}
```
### <a name="bot-settings"></a>Ustawienia bot

Jeśli chcesz skonfigurować precyzyjne ustawienia odpowiedzi dla usługi bot, przejdź do zasobu usługi App Service, który bot. Następnie należy zaktualizować konfiguracje, dodając następujące ustawienie.

- EnablePreciseAnswer
- DisplayPreciseAnswerOnly

|Konfiguracja ekranu|EnablePreciseAnswer|DisplayPreciseAnswerOnly|
|:--|--|--|
|Precyzyjne odpowiedzi|true|true|
|Tylko długie odpowiedzi|fałsz|fałsz|
|Odpowiedzi na długie i precyzyjne|true|fałsz|

---

## <a name="common-http-errors"></a>Typowe błędy HTTP

|Kod|Wyjaśnienie|
|:--|--|
|2xx|Powodzenie|
|400|Parametry żądania są niepoprawne, co oznacza, że brakuje wymaganych parametrów, są źle sformułowane lub zbyt duże|
|400|Treść żądania jest niepoprawna, co oznacza, że brakuje pliku JSON, został źle sformułowany lub jest zbyt duży|
|401|Nieprawidłowy klucz|
|403|Zabronione — nie masz poprawnych uprawnień|
|404|KB nie istnieje|
|410|Ten interfejs API jest przestarzały i nie jest już dostępny|

## <a name="next-steps"></a>Następne kroki

Strona **Publikowanie** zawiera również informacje umożliwiające [wygenerowanie odpowiedzi](../Quickstarts/get-answer-from-knowledge-base-using-url-tool.md) z elementem Poster lub zwinięciem.

> [!div class="nextstepaction"]
> [Uzyskiwanie danych analitycznych na potrzeby bazy wiedzy](../how-to/get-analytics-knowledge-base.md)
> [!div class="nextstepaction"]
> [Wynik pewności](../Concepts/confidence-score.md)
