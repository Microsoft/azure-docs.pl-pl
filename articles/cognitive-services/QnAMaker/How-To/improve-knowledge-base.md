---
title: Zasugerowane pytania w usłudze Active Learning — QnA Maker
description: Popraw jakość bazy wiedzy dzięki aktywnej uczeniu. Przeglądanie, akceptowanie lub odrzucanie, Dodawanie bez usuwania lub zmieniania istniejących pytań.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 04/06/2020
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 7c477655dfb24eebab9a2669697d9ef610088198
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99592028"
---
# <a name="accept-active-learning-suggested-questions-in-the-knowledge-base"></a>Akceptuj zasugerowane pytania w bazie wiedzy


<a name="accept-an-active-learning-suggestion-in-the-knowledge-base"></a>

Usługa Active Learning zmienia bazę wiedzy lub Search Service po zatwierdzeniu sugestii, a następnie zapisaniu i uczeniu się. Jeśli zatwierdzisz sugestię, zostanie ona dodana jako zapytanie alternatywne.

## <a name="turn-on-active-learning"></a>Włącz uczenie aktywne

Aby wyświetlić sugerowane pytania, musisz [włączyć aktywną naukę](../concepts/active-learning-suggestions.md) dla zasobu QNA Maker.

## <a name="view-suggested-questions"></a>Wyświetlanie sugerowanych pytań

1. Aby wyświetlić sugerowane pytania, na stronie **Edytowanie** bazy wiedzy wybierz opcję **Wyświetl opcje**, a następnie wybierz pozycję **Pokaż aktywne sugestie dotyczące uczenia**. Ta opcja zostanie wyłączona, jeśli nie ma żadnych sugestii dotyczących żadnej pary pytań i odpowiedzi.  

    [![W sekcji Edycja portalu wybierz pozycję Pokaż sugestie, aby zobaczyć alternatywy dla nowych pytań.](../media/improve-knowledge-base/show-suggestions-button.png)](../media/improve-knowledge-base/show-suggestions-button.png#lightbox)

1. Przefiltruj bazę wiedzy przy użyciu par pytań i odpowiedzi, aby wyświetlić tylko sugestie, wybierając pozycję **Filtruj według sugestii**.

    [![Użyj przełącznika Filtruj według sugestii, aby wyświetlić tylko rozwiązania alternatywne dla sugerowanych pytań.](../media/improve-knowledge-base/filter-by-suggestions.png)](../media/improve-knowledge-base/filter-by-suggestions.png#lightbox)

1. Każda para QnA sugeruje nowe alternatywy pytania ze znacznikiem wyboru, `✔` ,,,, aby zaakceptować pytanie lub `x` Aby odrzucić sugestie. Zaznacz znacznik wyboru, aby dodać pytanie.

    [![Zaznacz lub Odrzuć zaproponowane pytania dotyczące aktywnego uczenia, zaznaczając zielony znacznik wyboru lub czerwony znacznik usuwający.](../media/improve-knowledge-base/accept-active-learning-suggestions-small.png)](../media/improve-knowledge-base/accept-active-learning-suggestions.png#lightbox)

    Możesz dodawać lub usuwać _wszystkie sugestie_ , wybierając pozycję **Dodaj wszystko** lub **Odrzuć wszystko** na kontekstowym pasku narzędzi.

1. Wybierz pozycję **Zapisz i pouczenie** , aby zapisać zmiany w bazie wiedzy.

1. Wybierz pozycję **Publikuj** , aby zezwolić na dostęp do zmian z [interfejsu API GenerateAnswer](metadata-generateanswer-usage.md#generateanswer-request-configuration).

    Gdy co najmniej 5 podobnych zapytań jest klastrowane, co 30 minut QnA Maker sugeruje alternatywne pytania, które należy zaakceptować lub odrzucić.


<a name="#score-proximity-between-knowledge-base-questions"></a>

## <a name="active-learning-suggestions-are-saved-in-the-exported-knowledge-base"></a>Aktywne sugestie dotyczące uczenia są zapisywane w wyeksportowanej bazie wiedzy

Gdy aplikacja ma aktywne uczenie i eksportujesz aplikację, `SuggestedQuestions` kolumna w pliku TSV zachowuje aktywne dane szkoleniowe.

`SuggestedQuestions`Kolumna jest obiektem JSON informacji o niejawnych, `autosuggested` i jawnych `usersuggested` informacjach zwrotnych. Przykładem tego obiektu JSON dla pojedynczego pytania przesłanego przez użytkownika `help` jest:

```JSON
[
    {
        "clusterHead": "help",
        "totalAutoSuggestedCount": 1,
        "totalUserSuggestedCount": 0,
        "alternateQuestionList": [
            {
                "question": "help",
                "autoSuggestedCount": 1,
                "userSuggestedCount": 0
            }
        ]
    }
]
```

Po ponownym zaimportowaniu tej aplikacji aktywna nauka nadal zbiera informacje i zaleca sugestie dotyczące bazy wiedzy.


### <a name="architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot"></a>Przepływ architektury do korzystania z GenerateAnswer i uczenia interfejsów API z bot

Bot lub inna aplikacja kliencka powinna używać następującego przepływu architektury do korzystania z usługi Active Learning:

* Bot [Pobiera odpowiedź z bazy wiedzy](#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers) z interfejsem API GenerateAnswer przy użyciu właściwości, `top` Aby uzyskać wiele odpowiedzi.

    #### <a name="use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers"></a>Użyj właściwości Top w żądaniu GenerateAnswer, aby uzyskać kilka pasujących odpowiedzi

    Podczas przesyłania pytania do QnA Maker na potrzeby odpowiedzi `top` Właściwość treści JSON ustawia liczbę odpowiedzi do zwrócenia.

    ```json
    {
        "question": "wi-fi",
        "isTest": false,
        "top": 3
    }
    ```

* Bot określa jawną opinię:
    * Korzystając z własnej [niestandardowej logiki biznesowej](#use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user), odfiltruj niskie wyniki.
    * W bot lub aplikacji klienta Wyświetl listę możliwych odpowiedzi dla użytkownika i uzyskaj odpowiedź wybraną przez użytkownika.
* Bot [wysyła zaznaczoną odpowiedź z powrotem do QNA Maker](#bot-framework-sample-code) za pomocą [interfejsu API uczenia](#train-api).


### <a name="use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user"></a>Korzystanie z właściwości Score wraz z logiką biznesową w celu uzyskania listy odpowiedzi na potrzeby wyświetlania użytkownika

Gdy aplikacja kliencka (na przykład Chat bot) otrzymuje odpowiedź, zwracane są 3 pierwsze pytania. Użyj `score` właściwości, aby przeanalizować bliskość między wynikami. Ten zakres bliskości jest określany przez własną logikę biznesową.

```json
{
    "answers": [
        {
            "questions": [
                "Wi-Fi Direct Status Indicator"
            ],
            "answer": "**Wi-Fi Direct Status Indicator**\n\nStatus bar icons indicate your current Wi-Fi Direct connection status:  \n\nWhen your device is connected to another device using Wi-Fi Direct, '$  \n\n+ *+ ' Wi-Fi Direct is displayed in the Status bar.",
            "score": 74.21,
            "id": 607,
            "source": "Bugbash KB.pdf",
            "metadata": []
        },
        {
            "questions": [
                "Wi-Fi - Connections"
            ],
            "answer": "**Wi-Fi**\n\nWi-Fi is a term used for certain types of Wireless Local Area Networks (WLAN). Wi-Fi communication requires access to a wireless Access Point (AP).",
            "score": 74.15,
            "id": 599,
            "source": "Bugbash KB.pdf",
            "metadata": []
        },
        {
            "questions": [
                "Turn Wi-Fi On or Off"
            ],
            "answer": "**Turn Wi-Fi On or Off**\n\nTurning Wi-Fi on makes your device able to discover and connect to compatible in-range wireless APs.  \n\n1.  From a Home screen, tap ::: Apps > e Settings .\n2.  Tap Connections > Wi-Fi , and then tap On/Off to turn Wi-Fi on or off.",
            "score": 69.99,
            "id": 600,
            "source": "Bugbash KB.pdf",
            "metadata": []
        }
    ]
}
```

## <a name="client-application-follow-up-when-questions-have-similar-scores"></a>Śledzenie aplikacji klienta, gdy pytania mają podobne wyniki

Aplikacja kliencka wyświetli pytania z opcją dla użytkownika, aby wybrać _pojedyncze pytanie_ , które reprezentuje ich zamiar.

Gdy użytkownik wybierze jedno z istniejących pytań, aplikacja kliencka wysyła wybór użytkownika jako opinię za pomocą interfejsu API pouczenia QnA Maker. Ta opinia kończy aktywną pętlę opinii szkoleniowych.

## <a name="train-api"></a>Interfejs API trenowania

Aktywne opinie dotyczące uczenia są wysyłane do QnA Maker za pomocą żądania POST interfejsu API uczenia. Podpis interfejsu API to:

```http
POST https://<QnA-Maker-resource-name>.azurewebsites.net/qnamaker/knowledgebases/<knowledge-base-ID>/train
Authorization: EndpointKey <endpoint-key>
Content-Type: application/json
{"feedbackRecords": [{"userId": "1","userQuestion": "<question-text>","qnaId": 1}]}
```

|Właściwość żądania HTTP|Nazwa|Typ|Przeznaczenie|
|--|--|--|--|
|Parametr trasy adresu URL|Identyfikator bazy wiedzy|ciąg|Identyfikator GUID bazy wiedzy.|
|Niestandardowa poddomena|Nazwa zasobu QnAMaker|ciąg|Nazwa zasobu jest używana jako niestandardowa poddomena dla QnA Maker. Jest on dostępny na stronie Ustawienia po opublikowaniu bazy wiedzy. Jest on wyświetlany jako `host` .|
|Nagłówek|Content-Type|ciąg|Typ nośnika treści wysyłanej do interfejsu API. Wartość domyślna to: `application/json`|
|Nagłówek|Autoryzacja|ciąg|Klucz punktu końcowego (EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx).|
|Opublikuj treść|Obiekt JSON|JSON|Opinie szkoleniowe|

Treść JSON ma kilka ustawień:

|Właściwość treści JSON|Typ|Przeznaczenie|
|--|--|--|--|
|`feedbackRecords`|array|Lista opinii.|
|`userId`|ciąg|Identyfikator użytkownika osoby akceptującej sugerowane pytania. Format identyfikatora użytkownika jest aktualny. Na przykład adres e-mail może być prawidłowym IDENTYFIKATORem użytkownika w danej architekturze. Opcjonalny.|
|`userQuestion`|ciąg|Dokładny tekst zapytania użytkownika. Wymagane.|
|`qnaID`|liczba|Identyfikator pytania znaleziony w [odpowiedzi GenerateAnswer](metadata-generateanswer-usage.md#generateanswer-response-properties). |

Przykładowa treść JSON wygląda następująco:

```json
{
    "feedbackRecords": [
        {
            "userId": "1",
            "userQuestion": "<question-text>",
            "qnaId": 1
        }
    ]
}
```

Pomyślna odpowiedź zwraca stan 204 i bez treści odpowiedzi JSON.

### <a name="batch-many-feedback-records-into-a-single-call"></a>Tworzenie wsadowe wielu rekordów opinii w jednym wywołaniu

W aplikacji po stronie klienta, na przykład bot, można przechowywać dane, a następnie wysyłać wiele rekordów w jednej treści JSON w `feedbackRecords` tablicy.

Przykładowa treść JSON wygląda następująco:

```json
{
    "feedbackRecords": [
        {
            "userId": "1",
            "userQuestion": "How do I ...",
            "qnaId": 1
        },
        {
            "userId": "2",
            "userQuestion": "Where is ...",
            "qnaId": 40
        },
        {
            "userId": "3",
            "userQuestion": "When do I ...",
            "qnaId": 33
        }
    ]
}
```



<a name="active-learning-is-saved-in-the-exported-apps-tsv-file"></a>

## <a name="bot-framework-sample-code"></a>Przykładowy kod bot Framework

Kod struktury bot musi wywoływać interfejs API uczenia, jeśli zapytanie użytkownika powinno być używane na potrzeby aktywnej nauki. Istnieją dwa fragmenty kodu do zapisu:

* Określ, czy zapytanie ma być używane na potrzeby aktywnego uczenia
* Wyślij zapytanie z powrotem do interfejsu API pouczenia QnA Maker na potrzeby aktywnego uczenia

W [przykładzie usługi Azure bot](https://github.com/microsoft/BotBuilder-Samples)zostały zaprogramowane obie te działania.

### <a name="example-c-code-for-train-api-with-bot-framework-4x"></a>Przykładowy kod w języku C# dla interfejsu API uczenia z bot Framework 4. x

Poniższy kod ilustruje sposób wysyłania informacji z powrotem do QnA Maker za pomocą interfejsu API uczenia.

```csharp
public class FeedbackRecords
{
    // <summary>
    /// List of feedback records
    /// </summary>
    [JsonProperty("feedbackRecords")]
    public FeedbackRecord[] Records { get; set; }
}

/// <summary>
/// Active learning feedback record
/// </summary>
public class FeedbackRecord
{
    /// <summary>
    /// User id
    /// </summary>
    public string UserId { get; set; }

    /// <summary>
    /// User question
    /// </summary>
    public string UserQuestion { get; set; }

    /// <summary>
    /// QnA Id
    /// </summary>
    public int QnaId { get; set; }
}

/// <summary>
/// Method to call REST-based QnAMaker Train API for Active Learning
/// </summary>
/// <param name="endpoint">Endpoint URI of the runtime</param>
/// <param name="FeedbackRecords">Feedback records train API</param>
/// <param name="kbId">Knowledgebase Id</param>
/// <param name="key">Endpoint key</param>
/// <param name="cancellationToken"> Cancellation token</param>
public async static void CallTrain(string endpoint, FeedbackRecords feedbackRecords, string kbId, string key, CancellationToken cancellationToken)
{
    var uri = endpoint + "/knowledgebases/" + kbId + "/train/";

    using (var client = new HttpClient())
    {
        using (var request = new HttpRequestMessage())
        {
            request.Method = HttpMethod.Post;
            request.RequestUri = new Uri(uri);
            request.Content = new StringContent(JsonConvert.SerializeObject(feedbackRecords), Encoding.UTF8, "application/json");
            request.Headers.Add("Authorization", "EndpointKey " + key);

            var response = await client.SendAsync(request, cancellationToken);
            await response.Content.ReadAsStringAsync();
        }
    }
}
```

### <a name="example-nodejs-code-for-train-api-with-bot-framework-4x"></a>Przykładowy kod Node.js dla interfejsu API uczenia z bot Framework 4. x

Poniższy kod ilustruje sposób wysyłania informacji z powrotem do QnA Maker za pomocą interfejsu API uczenia.

```javascript
async callTrain(stepContext){

    var trainResponses = stepContext.values[this.qnaData];
    var currentQuery = stepContext.values[this.currentQuery];

    if(trainResponses.length > 1){
        var reply = stepContext.context.activity.text;
        var qnaResults = trainResponses.filter(r => r.questions[0] == reply);

        if(qnaResults.length > 0){

            stepContext.values[this.qnaData] = qnaResults;

            var feedbackRecords = {
                FeedbackRecords:[
                    {
                        UserId:stepContext.context.activity.id,
                        UserQuestion: currentQuery,
                        QnaId: qnaResults[0].id
                    }
                ]
            };

            // Call Active Learning Train API
            this.activeLearningHelper.callTrain(this.qnaMaker.endpoint.host, feedbackRecords, this.qnaMaker.endpoint.knowledgeBaseId, this.qnaMaker.endpoint.endpointKey);

            return await stepContext.next(qnaResults);
        }
        else{

            return await stepContext.endDialog();
        }
    }

    return await stepContext.next(stepContext.result);
}
```

## <a name="best-practices"></a>Najlepsze rozwiązania

Najlepsze rozwiązania dotyczące korzystania z usługi Active Learning można znaleźć w temacie [Best Practices](../Concepts/best-practices.md#active-learning).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Korzystanie z metadanych za pomocą interfejsu API GenerateAnswer](metadata-generateanswer-usage.md)
