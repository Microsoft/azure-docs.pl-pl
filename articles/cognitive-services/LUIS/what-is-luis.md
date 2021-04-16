---
title: Language Understanding (LUIS) — omówienie
description: Language Understanding (LUIS) — oparta na chmurze usługa interfejsu API używająca uczenia maszynowego do konwersacji w języku naturalnym do przewidywania znaczenia i wyodrębniania informacji.
keywords: Azure, artificial intelligence, ai, natural language processing, nlp, natural language understanding, nlu, LUIS, conversational AI, ai chatbot, nlp ai, azure luis
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: overview
ms.date: 04/13/2021
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: f46586b3f120cf191d88b7de9cf8686ca9b16cca
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503776"
---
# <a name="what-is-language-understanding-luis"></a>Co to jest usługa Language Understanding (LUIS)?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Language Understanding (LUIS) to oparta na chmurze usługa konwersacyjna sztucznej inteligencji, która stosuje niestandardową inteligencję uczenia maszynowego do konwersacyjnego tekstu w języku naturalnym użytkownika w celu przewidywania ogólnego znaczenia i ściągania odpowiednich szczegółowych informacji. Usługa LUIS zapewnia dostęp za [pośrednictwem portalu niestandardowego,](https://www.luis.ai) [interfejsów API][endpoint-apis] i [bibliotek klienckich zestawu SDK.](client-libraries-rest-api.md)

W przypadku użytkowników po raz pierwszy wykonaj następujące kroki, aby zalogować się do portalu [usługi LUIS.](sign-in-luis-portal.md "zaloguj się do portalu usługi LUIS") Aby rozpocząć pracę, możesz wypróbować wstępnie utworzone aplikacje domeny usługi [LUIS](luis-get-started-create-app.md) lub utworzyć [aplikację](get-started-portal-build-app.md).

Ta dokumentacja zawiera następujące typy artykułów:  

* [**Przewodniki Szybki start to**](luis-get-started-create-app.md) instrukcje z wprowadzeniem, które pokierują Cię przez proces tworzenia żądań do usługi.  
* [**Przewodniki z instrukcjami**](luis-how-to-start-new-app.md) zawierają instrukcje dotyczące korzystania z usługi w bardziej szczegółowe lub dostosowane sposoby.  
* [**Pojęcia**](artificial-intelligence.md) zawierają szczegółowe wyjaśnienia dotyczące funkcjonalności i funkcji usługi.  
* [**Samouczki**](tutorial-intents-only.md) to dłuższe przewodniki, które pokazują, jak używać usługi jako składnika w szerszych rozwiązaniach biznesowych.  

## <a name="what-does-luis-offer"></a>Co oferuje usługa LUIS 

* **Prostota:** usługa LUIS odciąża cię od konieczności posiadania wiedzy z zakresu AI w zakresie AI w zakresie firmy lub z wcześniejszej wiedzy na temat uczenia maszynowego. Wystarczy kilka kliknięć, aby utworzyć własną aplikację konwersacyjną AI. Możesz utworzyć aplikację niestandardową, korzystając [](get-started-portal-build-app.md)z jednego z naszych przewodników Szybki start, lub użyć jednej ze wstępnie [utworzonych aplikacji domeny.](luis-get-started-create-app.md)
* **Zabezpieczenia, ochrona prywatności i zgodność:** usługa LUIS, zapasowa w ramach infrastruktury platformy Azure, oferuje zabezpieczenia, prywatność i zgodność klasy korporacyjnej. Twoje dane pozostają Twoje; Dane można usunąć w dowolnym momencie. Dane są szyfrowane, gdy są przechowywane w magazynie. Więcej informacji na ten temat można znaleźć [tutaj](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy).
* **Integracja:** z łatwością zintegruj aplikację usługi LUIS z innymi usługi firmy Microsoft, np. [platformą Microsoft Bot,](https://docs.microsoft.com/composer/tutorial/tutorial-luis) [usługą QnA Maker](../QnAMaker/choose-natural-language-processing-service.md)i [usługą rozpoznawania mowy.](../Speech-Service/quickstarts/intent-recognition.md)


## <a name="luis-scenarios"></a>Scenariusze usługi LUIS
* [Tworzenie bota](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/conversational-bot)konwersacyjnego klasy korporacyjnej: W tej architekturze referencyjnej opisano sposób tworzenia bota konwersacyjnego klasy korporacyjnej (czatbota) przy użyciu usługi Azure Bot Framework.
* [Commerce Chatbot:](https://docs.microsoft.com/azure/architecture/solution-ideas/articles/commerce-chatbot)razem usługa Azure Bot Service i Language Understanding umożliwiają deweloperom tworzenie interfejsów do konwersacji dla różnych scenariuszy, takich jak bankowość, podróże i animacja.
* [Kontrolowanie urządzeń IoT](https://docs.microsoft.com/azure/architecture/solution-ideas/articles/iot-controlling-devices-with-voice-assistant)przy użyciu asystenta głosowego: twórz bezproblemowe interfejsy konwersacyjne ze wszystkimi urządzeniami dostępnymi z Internetu — od połączonej tv lub przez urządzenia w połączonej stacji elektrycznej.


## <a name="application-development-life-cycle"></a>Cykl życia tworzenia aplikacji

![Cykl życia tworzenia aplikacji usługi LUIS](./media/luis-overview/luis-dev-lifecycle.png "Cykl życia develooment aplikacji usługi LUIS")

-   **Planowanie:** zidentyfikuj scenariusze, w których użytkownicy mogą używać Twojej aplikacji. Zdefiniuj akcje i odpowiednie informacje, które muszą zostać rozpoznane.
-   **Kompilacja:** użyj zasobu tworzenia, aby opracowywać aplikację. Rozpocznij od zdefiniowania [intencji](luis-concept-intent.md) i [jednostek](luis-concept-entity-types.md). Następnie dodaj wypowiedzi [szkoleniowe dla](luis-concept-utterance.md) każdej intencji. 
-   **Testowanie i** ulepszanie: rozpocznij testowanie modelu za pomocą innych wypowiedzi, aby dowiedzieć się, jak działa aplikacja, i możesz zdecydować, czy potrzebne jest jakiekolwiek ulepszenie. Możesz ulepszyć aplikację, korzystając z [tych najlepszych rozwiązań.](luis-concept-best-practices.md) 
-   **Publikowanie:** Wdróż aplikację do przewidywania i odpytuj punkt końcowy przy użyciu zasobu przewidywania. Dowiedz się więcej o zasobach tworzenia i przewidywania [tutaj.](luis-how-to-azure-subscription.md#luis-resources) 
-   **Połącz:** połącz się z innymi usługami, takimi jak [Microsoft Bot Framework,](https://docs.microsoft.com/composer/tutorial/tutorial-luis) [QnA Maker](../QnAMaker/choose-natural-language-processing-service.md)i [Speech Service.](../Speech-Service/quickstarts/intent-recognition.md) 
-   **Uściślij:** [Przejrzyj wypowiedzi punktu końcowego,](luis-concept-review-endpoint-utterances.md) aby ulepszyć aplikację za pomocą rzeczywistych przykładów

Dowiedz się więcej o planowaniu i budowania aplikacji [tutaj.](luis-how-plan-your-app.md)

## <a name="next-steps"></a>Następne kroki

* [Co nowego w usłudze](whats-new.md "Co nowego") i dokumentacji
* [Tworzenie aplikacji usługi LUIS](tutorial-intents-only.md)
* [Dokumentacja interfejsu API][endpoint-apis]
* [Najlepsze praktyki](luis-concept-best-practices.md)
* [Zasoby dla deweloperów](developer-reference-resource.md "Zasoby dla deweloperów") dotyczące usługi LUIS.
* [Zaplanuj aplikację przy](luis-how-plan-your-app.md "Planowanie aplikacji") użyciu [intencji](luis-concept-intent.md "Zamiarów") i [jednostek.](luis-concept-entity-types.md "Podmioty")

[bot-framework]: /bot-framework/
[flow]: /connectors/luis/
[authoring-apis]: https://go.microsoft.com/fwlink/?linkid=2092087
[endpoint-apis]: https://go.microsoft.com/fwlink/?linkid=2092356
[qnamaker]: https://qnamaker.ai/
