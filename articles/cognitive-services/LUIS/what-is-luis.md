---
title: Co to jest usługa Language Understanding (LUIS)?
description: Language Understanding (LUIS) — usługa interfejsu API oparta na chmurze, która korzysta z uczenia maszynowego, w celu przewidywania znaczenia i wyodrębnienia informacji.
keywords: Azure, sztuczna inteligencja, AI, przetwarzanie języka naturalnego, NLP, interpretacja języka naturalnego, NLU, LUIS, konwersacje AI, AI chatbot, NLP AI, Azure Luis
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: overview
ms.date: 03/22/2021
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: f32f58bebc0a7d64443259981590e368b109b19b
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106278953"
---
# <a name="what-is-language-understanding-luis"></a>Co to jest usługa Language Understanding (LUIS)?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Language Understanding (LUIS) to oparta na chmurze usługa AI, która stosuje niestandardowe analizy maszynowe do konwersacji użytkownika, tekst w języku naturalnym do przewidywania ogólnego znaczenia i pobiera odpowiednie szczegółowe informacje.

Aplikacją kliencką dla usługi LUIS może być dowolna aplikacja konwersacyjna, która komunikuje się z użytkownikiem w naturalnym języku, aby wykonać zadanie. Przykładami aplikacji klienckich są m.in. aplikacje Media społecznościowe, AI rozszerzenie czatbotów i aplikacje komputerowe z obsługą mowy.

![Obraz przedstawiający koncepcję 3 aplikacji klienckich pracujących z Cognitive Services Language Understanding (LUIS)](./media/luis-overview/luis-entry-point.png "Obraz przedstawiający koncepcję 3 aplikacji klienckich pracujących z Cognitive Services Language Understanding (LUIS)")

Ta dokumentacja zawiera następujące typy artykułów:  

* Przewodniki [**Szybki Start**](luis-get-started-create-app.md) to instrukcje umożliwiające wykonywanie żądań do usługi.  
* [**Przewodniki z**](luis-how-to-start-new-app.md) instrukcjami dotyczącymi używania usługi w bardziej specyficzny lub dostosowany sposób.  
* [**Koncepcje**](artificial-intelligence.md) zawierają szczegółowe wyjaśnienia funkcji i funkcji usługi.  
* [**Samouczki**](tutorial-intents-only.md) są więcej przewodnikami, które pokazują, jak korzystać z usługi jako składnika w szerszym zakresie rozwiązań dla biznesu.  

## <a name="use-luis-in-a-chat-bot"></a>Korzystanie z usługi LUIS z czatbotami

<a name="Accessing-LUIS"></a>

Po opublikowaniu aplikacji platformy Azure LUIS aplikacja kliencka wysyła wyrażenia długości (tekst) do [interfejsu API][endpoint-apis] LUISego programu Endpoint Processing języka naturalnego i odbiera wyniki jako odpowiedzi JSON. Typową aplikacją kliencką dla usługi LUIS jest czatbot.


![Obraz przedstawiający koncepcje LUIS pracy z usługą Chat bot w celu przewidywania tekstu użytkownika za pomocą interpretacji języka naturalnego (NLP)](./media/luis-overview/LUIS-chat-bot-request-response.svg "Obraz przedstawiający koncepcje LUIS pracy z usługą Chat bot w celu przewidywania tekstu użytkownika przy użyciu funkcji interpretacji języka naturalnego (NLP")

|Krok|Akcja|
|:--|:--|
|1|Aplikacja kliencka wysyła _wypowiedź_ użytkownika (tekst wypowiadany własnymi słowami): „I want to call my HR rep” („Chcę zadzwonić do mojego przedstawiciela działu kadr”) do punktu końcowego usługi LUIS jako żądanie HTTP.|
|2|LUIS umożliwia tworzenie niestandardowych modeli języka w celu dodania analizy do aplikacji. Oparte na maszynach modele języka pobierają tekst wejściowy bez struktury użytkownika i zwracają odpowiedź sformatowaną w formacie JSON z zamiarem najwyższego poziomu `HRContact` . Minimalna odpowiedź punktu końcowego w formacie JSON zawiera wypowiedź zapytania oraz najwyżej ocenioną intencję. Może również wyodrębnić dane, takie jak jednostka _typu kontaktu_ .|
|3|Aplikacja kliencka używa odpowiedzi w formacie JSON do podejmowania decyzji dotyczących sposobu realizacji żądania użytkownika. Decyzje te mogą obejmować drzewo decyzyjne w kodzie bot Framework i wywołania do innych usług. |

Aplikacja usługi LUIS przeprowadza analizy, dzięki którym aplikacja kliencka może dokonać inteligentnego wyboru. Sama usługa LUIS nie dokonuje tych wyborów.

<a name="Key-LUIS-concepts"></a>
<a name="what-is-a-luis-model"></a>

## <a name="natural-language-understanding-nlu"></a>Interpretacja języka naturalnego (NLU)

[Luis zapewnia sztuczną inteligencję (AI)](artificial-intelligence.md "LUIS zapewnia sztuczną inteligencję (AI)") w postaci NLU, podzestawu przetwarzania języka naturalnego AI.

Twoja aplikacja LUIS zawiera model języka naturalnego specyficzny dla domeny. Możesz uruchomić aplikację LUIS ze wstępnie utworzonym modelem domeny, utworzyć własny model lub połączyć elementy wstępnie utworzonej domeny z własnymi informacjami.

* **Model wstępnie utworzony** usługi LUIS zawiera wiele wstępnie utworzonych modeli domeny, w tym intencje, wypowiedzi i wstępnie utworzone jednostki. Możesz skorzystać z wstępnie utworzonych jednostek bez konieczności korzystania z intencji i wypowiedzi wstępnie utworzonego modelu. [Wstępnie utworzone modele domeny](./howto-add-prebuilt-models.md "Wstępnie utworzone modele domen") zawierają kompletny projekt i są świetnym sposobem, aby szybko rozpocząć korzystanie z usługi LUIS.

* **Model niestandardowy** LUIS oferuje kilka sposobów identyfikacji własnych niestandardowych modeli, w tym intencje i jednostki. Jednostki obejmują jednostki uczenia maszynowego, określone lub literałowe jednostki oraz kombinację uczenia maszynowego i literału.

Dowiedz się więcej na temat [NLP AI](artificial-intelligence.md "NLP")i obszaru Luis dla NLU.

## <a name="step-1-design-and-build-your-model"></a>Krok 1. Projektowanie i kompilowanie modelu

Zaprojektuj model przy użyciu kategorii zamiarów użytkownika o nazwie **[intencje](luis-concept-intent.md "intencji")**. Każda intencja musi zawierać przykłady **[wypowiedzi](luis-concept-utterance.md "wyrażenia długości")** użytkownika. Każdy wypowiedź może dostarczyć dane, które muszą zostać wyodrębnione za pomocą [jednostek uczenia maszynowego](luis-concept-entity-types.md#effective-machine-learned-entities "jednostki uczenia maszynowego").

|Przykładowa wypowiedź użytkownika|Zamiar|Wyodrębnione dane|
|-----------|-----------|-----------|
|`Book a flight to Seattle?`|BookFlight|Seattle|
|`When does your store open?`|StoreHoursAndLocation|open|
|`Schedule a meeting at 1pm with Bob in Distribution`|ScheduleMeeting|1pm, Bob|

Skompiluj model przy użyciu interfejsów API [tworzenia](https://go.microsoft.com/fwlink/?linkid=2092087 "authoring") lub **[portalu Luis](https://www.luis.ai "Portal usługi LUIS")** lub obu. Dowiedz się więcej o tym, jak skompilować przy użyciu [portalu](get-started-portal-build-app.md "portal") i [bibliotek klienckich SDK](./client-libraries-rest-api.md?pivots=rest-api "Biblioteki klienckie SDK").

## <a name="step-2-get-the-query-prediction"></a>Krok 2. Uzyskiwanie prognozowania zapytań

Gdy model aplikacji jest szkolony i publikowany w punkcie końcowym, aplikacja kliencka (na przykład Chat bot) wysyła wyrażenia długości [do interfejsu API](https://go.microsoft.com/fwlink/?linkid=2092356 "endpoint") usługi przewidywania. Interfejs API stosuje model do wypowiedź na potrzeby analizy i reaguje na wyniki prognozowania w formacie JSON.

Minimalna odpowiedź punktu końcowego w formacie JSON zawiera wypowiedź zapytania oraz najwyżej ocenioną intencję. Może również wyodrębnić dane, takie jak następująca jednostka **typu kontaktu** i ogólna tonacji.

```JSON
{
    "query": "I want to call my HR rep",
    "prediction": {
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
            "label": "neutral",
            "score": 0.5
        }
    }
}
```

## <a name="step-3-improve-model-prediction"></a>Krok 3. udoskonalenie prognozowania modelu

Po opublikowaniu aplikacji LUIS i otrzymaniu rzeczywistej wyrażenia długości użytkownika, LUIS zapewnia [aktywną naukę](luis-concept-review-endpoint-utterances.md "aktywna nauka") punktu końcowego wyrażenia długości, aby poprawić dokładność przewidywania. Zapoznaj się z tymi sugestiami w ramach zwykłych czynności konserwacyjnych w cyklu życia.

<a name="using-luis"></a>

## <a name="development-lifecycle-and-tools"></a>Cykl życia i narzędzia deweloperskie
LUIS oferuje narzędzia, przechowywanie wersji i współpracę z innymi autorami LUIS w celu zintegrowania z pełnym [cyklem życia](luis-concept-app-iteration.md "cykl życia opracowywania").

Language Understanding (LUIS) jako interfejs API REST może być używany z dowolnym produktem, usługą lub platformą z żądaniem HTTP. LUIS udostępnia również biblioteki klienckie (SDK) dla kilku najpopularniejszych języków programowania. Dowiedz się więcej o udostępnionych [zasobach dewelopera](developer-reference-resource.md "zasoby dla deweloperów") .

Narzędzia do szybkiego i łatwego korzystania z usługi LUIS z botem:
* [interfejs wiersza polecenia Luis](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUIS "INTERFEJS WIERSZA POLECENIA LUIS") Pakiet NPM zapewnia tworzenie i prognozowanie za pomocą autonomicznego narzędzia wiersza polecenia lub jako importu.
* [LUISGen](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUISGen "LUISGen") — to narzędzie do generowania silnie typizowanego języka C# i kodu źródłowego języka Typescript z wyeksportowanego modelu usługi LUIS.
* [Dispatch](https://aka.ms/dispatch-tool "Wysyłanie") — umożliwia używanie kilku aplikacji LUIS lub QnA Maker z poziomu aplikacji nadrzędnej za pomocą modelu dyspozytora.
* [LUDown](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Ludown "LUDown") LUDown to narzędzie wiersza polecenia, które ułatwia zarządzanie modelami języka dla bot.

## <a name="integrate-with-a-bot"></a>Integracja z usługą bot

Użyj [usługi Azure bot](/azure/bot-service/ "Usługa Azure bot") z [platformą Microsoft bot Framework](https://dev.botframework.com/ "Microsoft Bot Framework") , aby skompilować i wdrożyć aplikację Chat bot. Projektuj i opracowuj przy użyciu narzędzia interfejsu graficznego, [kompozytora](/composer/ "Program Composer")lub [roboczych przykładów bot](https://github.com/microsoft/BotBuilder-Samples "Przykłady pracy z bot") zaprojektowanych dla najważniejszych scenariuszy bot.

## <a name="integrate-with-other-cognitive-services"></a>Integracja z innymi Cognitive Services

Inne usługi Cognitive Services używane z usługą LUIS:
* [QnA Maker](../QnAMaker/overview/overview.md "QnA Maker") umożliwia łączenie kilku rodzajów tekstu w bazę wiedzy w formie pytań i odpowiedzi.
* [Usługa rozpoznawania mowy](../Speech-Service/overview.md "Usługa rozpoznawania mowy") konwertuje żądania w języku mówionym na tekst.

LUIS zapewnia funkcjonalność analiza tekstu w ramach istniejących zasobów LUIS. Ta funkcja obejmuje [analizę tonacji](luis-how-to-publish-app.md#configuring-publish-settings "Analiza tonacji") i [wyodrębnianie kluczowych fraz](luis-reference-prebuilt-keyphrase.md "Wyodrębnianie kluczowych fraz") z wbudowaną jednostką keyPhrase.

## <a name="learn-with-the-quickstarts"></a>Zapoznaj się z przewodnikami Szybki Start

Dowiedz się więcej na temat LUIS z przewodnikami Szybki Start przy użyciu [portalu](get-started-portal-build-app.md "portal") i [bibliotek klienckich SDK](./client-libraries-rest-api.md?pivots=rest-api "Biblioteki klienckie SDK").


## <a name="deploy-on-premises-using-docker-containers"></a>Wdrażanie lokalnie przy użyciu kontenerów platformy Docker

[Używanie kontenerów Luis](luis-container-howto.md) do wdrażania funkcji interfejsu API w środowisku lokalnym. Te kontenery platformy Docker umożliwiają przełączenie usługi bliżej danych pod kątem zgodności, bezpieczeństwa lub innych przyczyn operacyjnych.

## <a name="next-steps"></a>Następne kroki

* [Nowości dotyczące usługi](whats-new.md "Co nowego") i dokumentacji
* [Zaplanuj swoją aplikację](luis-how-plan-your-app.md "Planowanie aplikacji") za pomocą [intencji](luis-concept-intent.md "intencji") i [jednostek](luis-concept-entity-types.md "obiekty").

[bot-framework]: /bot-framework/
[flow]: /connectors/luis/
[authoring-apis]: https://go.microsoft.com/fwlink/?linkid=2092087
[endpoint-apis]: https://go.microsoft.com/fwlink/?linkid=2092356
[qnamaker]: https://qnamaker.ai/