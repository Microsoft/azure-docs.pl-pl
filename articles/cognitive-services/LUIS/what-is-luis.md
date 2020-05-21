---
title: Co to jest usługa Language Understanding (LUIS)?
description: Language Understanding (LUIS) to oparta na chmurze usługa interfejsu API, która stosuje dostosowane techniki analizy i uczenia maszynowego do wypowiedzi użytkownika w języku naturalnym, aby rozpoznać ich ogólne znaczenie i wydobyć istotne szczegółowe informacje.
ms.topic: overview
ms.date: 05/05/2020
ms.openlocfilehash: 26e1dfd5a610be9470d05080ca1559eec47196ab
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83675983"
---
# <a name="what-is-language-understanding-luis"></a>Co to jest usługa Language Understanding (LUIS)?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Language Understanding (LUIS) to oparta na chmurze usługa interfejsu API, która stosuje dostosowane techniki analizy i uczenia maszynowego do wypowiedzi użytkownika w języku naturalnym, aby rozpoznać ich ogólne znaczenie i wydobyć istotne szczegółowe informacje.

Aplikacją kliencką dla usługi LUIS może być dowolna aplikacja konwersacyjna, która komunikuje się z użytkownikiem w naturalnym języku, aby wykonać zadanie. Przykładami aplikacji klienckich są aplikacje mediów społecznościowych, czatboty i aplikacje klasyczne z funkcją rozpoznawania mowy.

![Obraz przedstawiający koncepcję 3 aplikacji klienckich pracujących z Cognitive Services Language Understanding (LUIS)](./media/luis-overview/luis-entry-point.png "Obraz przedstawiający koncepcję 3 aplikacji klienckich pracujących z Cognitive Services Language Understanding (LUIS)")

## <a name="use-luis-in-a-chat-bot"></a>Korzystanie z usługi LUIS z czatbotami

<a name="Accessing-LUIS"></a>

Po opublikowaniu aplikacji usługi LUIS aplikacja kliencka wysyła wypowiedzi (tekst) do [interfejsu API][endpoint-apis] punktu końcowego przetwarzania języka naturalnego usługi LUIS i otrzymuje wyniki w formie odpowiedzi w formacie JSON. Typową aplikacją kliencką dla usługi LUIS jest czatbot.


![Obraz przedstawiający koncepcje LUIS pracy z usługą Chat bot w celu przewidywania tekstu użytkownika za pomocą interpretacji języka naturalnego (NLP)](./media/luis-overview/LUIS-chat-bot-request-response.svg "Obraz przedstawiający koncepcje LUIS pracy z usługą Chat bot w celu przewidywania tekstu użytkownika przy użyciu funkcji interpretacji języka naturalnego (NLP")

|Krok|Akcja|
|:--|:--|
|1|Aplikacja kliencka wysyła _wypowiedź_ użytkownika (tekst wypowiadany własnymi słowami): „I want to call my HR rep” („Chcę zadzwonić do mojego przedstawiciela działu kadr”) do punktu końcowego usługi LUIS jako żądanie HTTP.|
|2|LUIS umożliwia tworzenie niestandardowych modeli języka w celu dodania analizy do aplikacji. Oparte na maszynach modele języka pobierają tekst wejściowy bez struktury użytkownika i zwracają odpowiedź sformatowaną w formacie JSON z zamiarem najwyższego poziomu `HRContact` . Minimalna odpowiedź punktu końcowego w formacie JSON zawiera wypowiedź zapytania oraz najwyżej ocenioną intencję. Może również wyodrębnić dane, takie jak jednostka _typu kontaktu_ .|
|3|Aplikacja kliencka używa odpowiedzi w formacie JSON do podejmowania decyzji dotyczących sposobu realizacji żądania użytkownika. Decyzje te mogą obejmować drzewo decyzyjne w kodzie bot Framework i wywołania do innych usług. |

Aplikacja usługi LUIS przeprowadza analizy, dzięki którym aplikacja kliencka może dokonać inteligentnego wyboru. Sama usługa LUIS nie dokonuje tych wyborów.

<a name="Key-LUIS-concepts"></a>
<a name="what-is-a-luis-model"></a>

## <a name="natural-language-processing"></a>Przetwarzanie języka naturalnego

Twoja aplikacja LUIS zawiera model języka naturalnego specyficzny dla domeny. Możesz uruchomić aplikację LUIS ze wstępnie utworzonym modelem domeny, utworzyć własny model lub połączyć elementy wstępnie utworzonej domeny z własnymi informacjami.

* **Model wstępnie utworzony** usługi LUIS zawiera wiele wstępnie utworzonych modeli domeny, w tym intencje, wypowiedzi i wstępnie utworzone jednostki. Możesz skorzystać z wstępnie utworzonych jednostek bez konieczności korzystania z intencji i wypowiedzi wstępnie utworzonego modelu. [Wstępnie utworzone modele domeny](luis-how-to-use-prebuilt-domains.md) zawierają kompletny projekt i są świetnym sposobem, aby szybko rozpocząć korzystanie z usługi LUIS.

* **Model niestandardowy** LUIS oferuje kilka sposobów identyfikacji własnych niestandardowych modeli, w tym intencje i jednostki. Jednostki obejmują jednostki uczenia maszynowego, określone lub literałowe jednostki oraz kombinację uczenia maszynowego i literału.

## <a name="build-the-luis-model"></a>Tworzenie modelu w usłudze LUIS
Utwórz model przy użyciu interfejsów API [tworzenia](https://go.microsoft.com/fwlink/?linkid=2092087) lub [portalu Luis](https://www.luis.ai).

Tworzenie modelu w usłudze LUIS rozpoczyna się od kategorii intencji użytkownika o nazwie **[Intencje](luis-concept-intent.md)**. Każda intencja musi zawierać przykłady **[wypowiedzi](luis-concept-utterance.md)** użytkownika. Każdy wypowiedź może dostarczyć dane, które muszą zostać wyodrębnione.

|Przykładowa wypowiedź użytkownika|Intencja|Wyodrębnione dane|
|-----------|-----------|-----------|
|`Book a flight to Seattle?`|BookFlight|Seattle|
|`When does your store open?`|StoreHoursAndLocation|open|
|`Schedule a meeting at 1pm with Bob in Distribution`|ScheduleMeeting|1pm, Bob|

## <a name="query-prediction-endpoint"></a>Punkt końcowy przewidywania zapytania

Gdy aplikacja zostanie przeszkolone i opublikowana w punkcie końcowym, aplikacja kliencka wysyła wyrażenia długości do [interfejsu API](https://go.microsoft.com/fwlink/?linkid=2092356) usługi przewidywania. Interfejs API stosuje aplikację do wypowiedź na potrzeby analizy i reaguje na wyniki prognozowania w formacie JSON.

Minimalna odpowiedź punktu końcowego w formacie JSON zawiera wypowiedź zapytania oraz najwyżej ocenioną intencję. Może również wyodrębnić dane, takie jak następująca jednostka **typu kontaktu** i ogólna tonacji.

```JSON
{
    "query": "I want to call my HR rep",
    "prediction": {
        "normalizedQuery": "i want to call my hr rep",
        "topIntent": "HRContact",
        "intents": {
            "HRContact": {
                "score": 0.8582669
            }
        },
        "entities": {
            "Contact Type": [
                "call"
            ]
        },
        "sentiment": {
            "label": "negative",
            "score": 0.103343368
        }
    }
}
```

## <a name="improve-model-prediction"></a>Doskonalenie przewidywania z użyciem modelu

Po opublikowaniu aplikacji LUIS i otrzymaniu rzeczywistej wyrażenia długości użytkownika, LUIS zapewnia [aktywną naukę](luis-concept-review-endpoint-utterances.md) punktu końcowego wyrażenia długości, aby poprawić dokładność przewidywania.

<a name="using-luis"></a>

## <a name="development-lifecycle"></a>Cykl projektowania oprogramowania
LUIS oferuje narzędzia, przechowywanie wersji i współpracę z innymi autorami LUIS w celu zintegrowania z pełnym [cyklem życia](luis-concept-app-iteration.md).

## <a name="implementing-luis"></a>Wdrażanie usługi LUIS
Language Understanding (LUIS) jako interfejs API REST może być używany z dowolnym produktem, usługą lub platformą z żądaniem HTTP. Poniższa lista zawiera główne produkty i usługi firmy Microsoft, których można używać z usługą LUIS.

Najważniejszą aplikacją kliencką dla usługi LUIS jest:
* [Bot usługi Web Apps](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0) szybko tworzy czatbota obsługującego usługę LUIS, który rozmawia z użytkownikiem za pośrednictwem tekstowych danych wejściowych. Używa programu [bot Framework][bot-framework] w wersji [4. x](https://github.com/Microsoft/botbuilder-dotnet) , aby uzyskać pełny bot środowisko.

Narzędzia do szybkiego i łatwego korzystania z usługi LUIS z botem:
* [interfejs wiersza polecenia Luis](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUIS) Pakiet NPM zapewnia tworzenie i prognozowanie za pomocą autonomicznego narzędzia wiersza polecenia lub jako importu.
* [LUISGen](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUISGen) — to narzędzie do generowania silnie typizowanego języka C# i kodu źródłowego języka Typescript z wyeksportowanego modelu usługi LUIS.
* [Dispatch](https://aka.ms/dispatch-tool) — umożliwia używanie kilku aplikacji LUIS lub QnA Maker z poziomu aplikacji nadrzędnej za pomocą modelu dyspozytora.
* [LUDown](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Ludown) LUDown to narzędzie wiersza polecenia, które ułatwia zarządzanie modelami języka dla bot.
* [Bot Framework — układacz](https://github.com/microsoft/BotFramework-Composer) — zintegrowane narzędzie programistyczne dla deweloperów i zespołów z wielodyscyplinarnymi kompilacjami botów i konwersacjami w środowisku Microsoft bot Framework

Inne usługi Cognitive Services używane z usługą LUIS:
* [QnA Maker][qnamaker] umożliwia łączenie kilku rodzajów tekstu w bazę wiedzy w formie pytań i odpowiedzi.
* [Usługa rozpoznawania mowy](../Speech-Service/overview.md) konwertuje żądania w języku mówionym na tekst.
* [Uczeń konwersacji](https://docs.microsoft.com/azure/cognitive-services/labs/conversation-learner/overview) umożliwia stworzenie rozmów z botem szybciej niż w usłudze LUIS.

Przykłady korzystające z usługi LUIS:
* Repozytorium GitHub [Conversational AI](https://github.com/Microsoft/AI)
* [Bot Framework — przykłady bot](https://github.com/microsoft/BotBuilder-Samples)

## <a name="next-steps"></a>Następne kroki

* [Co nowego](whats-new.md)
* Utwórz nową aplikację usługi LUIS za pomocą domeny [wstępnie utworzonej](luis-get-started-create-app.md) lub [niestandardowej](luis-quickstart-intents-only.md).
* [Wyślij zapytanie do punktu końcowego przewidywania](luis-get-started-get-intent-from-browser.md) publicznej aplikacji IoT.
* [Zasoby dla deweloperów](developer-reference-resource.md) dla Luis.

[bot-framework]: https://docs.microsoft.com/bot-framework/
[flow]: https://docs.microsoft.com/connectors/luis/
[authoring-apis]: https://go.microsoft.com/fwlink/?linkid=2092087
[endpoint-apis]: https://go.microsoft.com/fwlink/?linkid=2092356
[qnamaker]: https://qnamaker.ai/
