---
title: Koncepcje przedsiębiorstwa — LUIS
titleSuffix: Azure Cognitive Services
description: Poznaj koncepcje projektowe dla dużych aplikacji LUIS lub wielu aplikacji, w tym LUIS i QnA Maker ze sobą.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.openlocfilehash: 2e2165b81c7cd634fe79ec4438a550ad365f5a30
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95019181"
---
# <a name="enterprise-strategies-for-a-luis-app"></a>Strategie przedsiębiorstwa dla aplikacji LUIS
Zapoznaj się z tymi strategiami projektowania aplikacji dla przedsiębiorstw.

## <a name="when-you-expect-luis-requests-beyond-the-quota"></a>Gdy oczekuje się, że żądania LUIS przekraczają limit przydziału

LUIS ma miesięczny limit przydziału, a także przydział na sekundę na podstawie warstwy cenowej zasobu platformy Azure. 

Jeśli częstotliwość żądań aplikacji LUIS przekracza dozwoloną [liczbę limitów przydziału](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/), możesz:

* Rozłożenie obciążenia na więcej aplikacji LUIS z [tą samą definicją aplikacji](#use-multiple-apps-with-same-app-definition). Obejmuje to, opcjonalnie, uruchamianie LUIS z [kontenera](luis-container-howto.md). 
* Utwórz i [Przypisz wiele kluczy](#assign-multiple-luis-keys-to-same-app) do aplikacji. 

### <a name="use-multiple-apps-with-same-app-definition"></a>Używanie wielu aplikacji z tą samą definicją aplikacji
Wyeksportuj oryginalną aplikację LUIS, a następnie zaimportuj aplikację z powrotem do oddzielnych aplikacji. Każda aplikacja ma swój identyfikator aplikacji. Podczas publikowania, zamiast korzystać z tego samego klucza we wszystkich aplikacjach, należy utworzyć osobny klucz dla każdej aplikacji. Należy zrównoważyć obciążenie dla wszystkich aplikacji, aby nie było przeciążać żadnej pojedynczej aplikacji. Dodaj [Application Insights](./luis-csharp-tutorial-bf-v4.md) , aby monitorować użycie. 

Aby uzyskać te same najważniejsze zamierzenia między wszystkimi aplikacjami, upewnij się, że przewidywanie zamiaru między pierwszym i drugim zamiarem jest wystarczająco szerokie, że LUIS nie jest mylić, dając różne wyniki między aplikacjami w przypadku niewielkich zmian w wyrażenia długości. 

W przypadku szkolenia tych elementów równorzędnych upewnij się, że [nauczysz się ze wszystkimi danymi](luis-how-to-train.md#train-with-all-data).

Wyznaczanie pojedynczej aplikacji jako głównej. Wszystkie wyrażenia długości, które są sugerowane do przeglądu, powinny zostać dodane do aplikacji głównej, a następnie przeniesione z powrotem do wszystkich innych aplikacji. Jest to pełny eksport aplikacji lub załadowanie wyrażenia długości z etykietą z elementu głównego do elementów podrzędnych. Ładowanie można wykonać z witryny sieci Web [Luis](luis-reference-regions.md) lub interfejsu API tworzenia dla [jednej wypowiedź](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c08) lub dla [partii](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09). 

Zaplanuj okresowe przeglądy, na przykład co dwa tygodnie, [punktu końcowego wyrażenia długości](luis-how-to-review-endpoint-utterances.md) na potrzeby aktywnego uczenia, a następnie ponownie nauczenie i ponowne opublikowanie. 

### <a name="assign-multiple-luis-keys-to-same-app"></a>Przypisywanie wielu kluczy LUIS do tej samej aplikacji
Jeśli aplikacja LUIS uzyska więcej trafień z punktu końcowego, niż zezwala na to limit przydziału pojedynczego klucza, Utwórz i przypisz więcej kluczy do aplikacji LUIS. Utwórz usługę Traffic Manager lub moduł równoważenia obciążenia, aby zarządzać kwerendami punktów końcowych w ramach kluczy punktu końcowego. 

## <a name="when-your-monolithic-app-returns-wrong-intent"></a>Gdy aplikacja monolityczna zwróci niewłaściwy cel
Jeśli aplikacja jest przeznaczona do przewidywania różnorodnych wyrażenia długości użytkowników, rozważ zaimplementowanie [modelu wysyłania](#dispatch-tool-and-model). Rozdzielenie aplikacji monolitycznej pozwala LUIS wykrywać wykrywanie między intencjami, a nie pomylić między intencjami w aplikacji nadrzędnej i podrzędnej. 

Zaplanuj okresowe [przeglądy punktu końcowego wyrażenia długości](luis-how-to-review-endpoint-utterances.md) na potrzeby aktywnego uczenia, na przykład co dwa tygodnie, a następnie ponownie nauczenie i ponowne opublikowanie. 

## <a name="when-you-need-to-have-more-than-500-intents"></a>Jeśli potrzebujesz więcej niż 500 intencji
Załóżmy, że opracowujesz Asystenta pakietu Office, który ma ponad 500 intencji. Jeśli 200 intencje odnoszą się do spotkań dotyczących planowania, 200 są wyświetlane przypomnienia, 200 są informacje o współpracownikach, a 200 są do wysyłania wiadomości e-mail, założeń grupowych, tak aby każda grupa była w jednej aplikacji, a następnie utworzyć aplikację najwyższego poziomu zawierającą każde zamiar. Użyj [modelu wysyłania](#dispatch-tool-and-model) , aby skompilować aplikację najwyższego poziomu. Następnie zmień bot tak, aby korzystał z wywołania kaskadowego, jak pokazano w [samouczku modelu wysyłania](/azure/bot-service/bot-builder-tutorial-dispatch?branch=master&tabs=cs&view=azure-bot-service-4.0). 

## <a name="when-you-need-to-combine-several-luis-and-qna-maker-apps"></a>Gdy musisz połączyć kilka aplikacji LUIS i QnA Maker
Jeśli masz kilka aplikacji LUIS i QnA Maker, które muszą odpowiedzieć na bot, użyj [modelu wysyłania](#dispatch-tool-and-model) , aby skompilować aplikację najwyższego poziomu.  Następnie zmień bot tak, aby korzystał z wywołania kaskadowego, jak pokazano w [samouczku modelu wysyłania](/azure/bot-service/bot-builder-tutorial-dispatch?branch=master&tabs=cs&view=azure-bot-service-4.0). 

## <a name="dispatch-tool-and-model"></a>Narzędzie i model wysyłania
Za pomocą narzędzia wiersza polecenia [wysyłania][dispatch-tool] dostępnego w [BotBuilder-Tools](https://github.com/Microsoft/botbuilder-tools) można łączyć wiele aplikacji LUIS i/lub QNA Maker w aplikacji nadrzędnej Luis. Takie podejście umożliwia posiadanie domeny nadrzędnej obejmującej wszystkie tematy i różne podrzędne domeny podmiotu w oddzielnych aplikacjach. 

![Obraz przedstawiający koncepcję architektury wysyłania](./media/luis-concept-enterprise/dispatch-architecture.png)

Domena nadrzędna jest zapisywana w LUIS z wersją nazwaną `Dispatch` na liście aplikacji. 

Usługa Chat bot odbiera wypowiedź, a następnie wysyła do aplikacji nadrzędnej LUIS w celu przewidywania. Najczęściej przewidywane zamierzenie z aplikacji nadrzędnej Określa, która aplikacja podrzędna LUIS jest wywoływana dalej. Bot rozmowy wysyła wypowiedź do aplikacji podrzędnej w celu uzyskania bardziej szczegółowego przewidywania.

Dowiedz się, w jaki sposób ta hierarchia wywołań jest wykonywana z poziomu programu Dyspozytor Bot Builder v4 [— samouczek aplikacji](/azure/bot-service/bot-builder-tutorial-dispatch?branch=master&tabs=cs&view=azure-bot-service-4.0).  

### <a name="intent-limits-in-dispatch-model"></a>Limity zamierzeń w modelu wysyłania
Aplikacja do wysyłania ma 500 źródłowe źródła, równoważne z intencjami 500, jako maksymalne. 

## <a name="more-information"></a>Więcej informacji

* [Bot Framework SDK](https://github.com/Microsoft/botframework)
* [Samouczek dotyczący modelu wysyłania](/azure/bot-service/bot-builder-tutorial-dispatch?branch=master&tabs=cs&view=azure-bot-service-4.0)
* [Interfejs wiersza polecenia wysyłania](https://github.com/Microsoft/botbuilder-tools)
* Przykład bot model wysyłania — [.NET](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/14.nlp-with-dispatch), [Node.js](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/javascript_nodejs/14.nlp-with-dispatch)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [przetestować partię](luis-how-to-batch-test.md)

[dispatcher-application-tutorial]: /azure/bot-service/bot-builder-tutorial-dispatch?branch=master
[dispatch-tool]: https://aka.ms/dispatch-tool