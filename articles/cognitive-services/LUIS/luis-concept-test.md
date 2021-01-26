---
title: Testowanie aplikacji LUIS
titleSuffix: Azure Cognitive Services
description: Testowanie jest procesem zapewniania przykładowej wyrażenia długości do LUIS i uzyskiwania odpowiedzi na LUIS, które są uznawane za intencje i jednostki.
manager: nitinme
services: cognitive-services
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/10/2019
ms.openlocfilehash: b33f765e936d7c0db301a5b2fcf38ccaf137f771
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98787557"
---
# <a name="testing-example-utterances-in-luis"></a>Przykład testowania wyrażenia długości w LUIS

Testowanie jest procesem zapewniania przykładowej wyrażenia długości do LUIS i uzyskiwania odpowiedzi na LUIS, które są uznawane za intencje i jednostki. 

Możesz testować LUIS interaktywnie, jeden wypowiedź w danym momencie lub udostępnić zestaw wyrażenia długości. Podczas testowania można porównać bieżącą odpowiedź predykcyjną aktywnego modelu z odpowiedzią przewidywania opublikowanego modelu. 

<a name="A-test-score"></a>
<a name="Score-all-intents"></a>
<a name="E-(exponent)-notation"></a>

## <a name="what-is-a-score-in-testing"></a>Co to jest ocena w testowaniu?
Zobacz koncepcje [oceny przewidywania](luis-concept-prediction-score.md) , aby dowiedzieć się więcej na temat przewidywanych ocen.

## <a name="interactive-testing"></a>Testowanie interakcyjne
Testowanie interaktywne odbywa się z poziomu panelu **testów** portalu Luis. Możesz wprowadzić wypowiedź, aby zobaczyć, jak intencje i jednostki są identyfikowane i oceniane. Jeśli LUIS nie przewidywalna intencji i jednostek, które są oczekiwane w wypowiedź w panelu testowania, skopiuj je na stronę **intencji** jako nową wypowiedź. Następnie Oznacz części tego wypowiedź dla jednostek i pouczenie LUIS. 

## <a name="batch-testing"></a>Testowanie wsadowe
Zobacz [test wsadowy](./luis-how-to-batch-test.md) , jeśli testujesz więcej niż jeden wypowiedź w danym momencie.

## <a name="endpoint-testing"></a>Testowanie punktu końcowego
Możesz testować przy użyciu [punktu końcowego](luis-glossary.md#endpoint) z maksymalnie dwiema wersjami aplikacji. Korzystając z swojej wersji głównej lub działającej jako punkt końcowy **produkcji** , należy dodać drugą wersję do punktu końcowego **przemieszczania** . Takie podejście daje trzy wersje wypowiedź: bieżący model w okienku test w witrynie sieci Web [Luis](luis-reference-regions.md) oraz dwie wersje w dwóch różnych punktach końcowych. 

Wszystkie testy punktów końcowych są wliczane do limitu przydziału użycia. 

## <a name="do-not-log-tests"></a>Nie rejestruj testów
Jeśli testujesz względem punktu końcowego i nie chcesz rejestrować wypowiedź, pamiętaj, aby użyć `logging=false` konfiguracji ciągu zapytania.

## <a name="where-to-find-utterances"></a>Gdzie można znaleźć wyrażenia długości
LUIS przechowuje wszystkie zarejestrowane wyrażenia długości w dzienniku zapytań, dostępne do pobrania w portalu LUIS na stronie listy **aplikacje** , a także [interfejsy API tworzenia](https://go.microsoft.com/fwlink/?linkid=2092087)Luis. 

Wszystkie wyrażenia długości LUIS nie znajdują się na liście na stronie **[Przegląd punktu końcowego wyrażenia długości](luis-how-to-review-endpoint-utterances.md)** witryny sieci Web [Luis](luis-reference-regions.md) . 

## <a name="remember-to-train"></a>Pamiętaj, aby szkolić
Pamiętaj, aby [szkolić](luis-how-to-train.md) Luis po wprowadzeniu zmian w modelu. Zmiany w aplikacji LUIS nie są widoczne w testowaniu, dopóki aplikacja nie zostanie przeszkolone. 

## <a name="best-practices"></a>Najlepsze rozwiązania
Poznaj [najlepsze rozwiązania](luis-concept-best-practices.md).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat [testowania](luis-interactive-test.md) wyrażenia długości.