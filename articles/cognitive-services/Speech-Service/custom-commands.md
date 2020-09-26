---
title: Polecenia niestandardowe — usługa mowy
titleSuffix: Azure Cognitive Services
description: Omówienie funkcji, możliwości i ograniczeń dla poleceń niestandardowych, rozwiązanie do tworzenia aplikacji głosowych.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: travisw
ms.openlocfilehash: c9f7168bc0524b483413ade9792af18ff9cfebfa
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91358546"
---
# <a name="what-is-custom-commands"></a>Co to są polecenia niestandardowe?

Aplikacje, takie jak [Asystenci głosu](voice-assistants.md) , nasłuchują użytkowników i podejmują działania w odpowiedzi, często mówiąc z powrotem. Używają [zamiany mowy na tekst](speech-to-text.md) , aby transkrypcja mowę użytkownika, a następnie podejmować działania dotyczące interpretacji języka naturalnego tekstu. Ta akcja często obejmuje głosowanie danych wyjściowych wygenerowanego przez asystenta z funkcją [zamiany tekstu na mowę](text-to-speech.md). Urządzenia nawiązują połączenie z asystentami z obiektem zestawu Speech SDK `DialogServiceConnector` .

**Polecenia niestandardowe** ułatwiają tworzenie zaawansowanych poleceń głosowych zoptymalizowanych pod kątem interakcji głosowych. Zapewnia ujednolicone środowisko tworzenia, automatyczny model hostingu i stosunkowo niższą złożoność, ułatwiając skoncentrowanie się na tworzeniu najlepszego rozwiązania dla scenariuszy poleceń głosowych.

Polecenia niestandardowe najlepiej nadają się do wykonywania zadań lub scenariuszy kontroli i kontrolowania poleceń, szczególnie dobrze dopasowane dla urządzeń Internet rzeczy (IoT), otoczenia i urządzeń bezobsługowych. Przykłady obejmują rozwiązania dla branż Hotelarstwo, detalicznych i samochodowych, co pozwala na tworzenie najlepszych środowisk sterowanych głosami dla Gości, zarządzanie zapasami w sklepie oraz sterowanie funkcjami w samodziałach w trakcie przenoszenia.

> [!TIP]
> Obejrzyj nasze przykładowe pokazy na naszej stronie docelowej pod adresem [https://speech.microsoft.com/customcommands](https://speech.microsoft.com/customcommands) .

Jeśli interesuje Cię tworzenie złożonych aplikacji do konwersacji, zachęcasz do wypróbowania platformy bot przy użyciu [rozwiązania Virtual Assistant](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview). Możesz dodać głos do dowolnych bot Framework bot przy użyciu bezpośredniej mowy wiersza.

Dobre kandydaci dla poleceń niestandardowych mają stałe słownictwo ze zdefiniowanymi zestawami zmiennych. Na przykład doskonałe zadania automatyzacji, takie jak kontrolowanie termostatu, są idealne.

   ![Przykłady scenariuszy uzupełniania zadań](media/voice-assistants/task-completion-examples.png "Przykłady uzupełniania zadań")

## <a name="getting-started-with-custom-commands"></a>Wprowadzenie do poleceń niestandardowych

Naszym celem z poleceniami niestandardowymi jest zredukowanie obciążeń, aby poznać różne technologie i skoncentrować się na tworzeniu aplikacji z poleceniami głosowymi. Pierwszy krok dotyczący używania poleceń niestandardowych do <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">tworzenia zasobów <span class="docon docon-navigate-external x-hidden-focus"></span> usługi Azure Speech </a>. Możesz utworzyć swoją aplikację poleceń niestandardowych w programie Speech Studio i opublikować ją, po której aplikacja na urządzeniu może komunikować się z nią przy użyciu zestawu Speech SDK.

#### <a name="authoring-flow-for-custom-commands"></a>Przepływ tworzenia dla poleceń niestandardowych
   ![Przepływ tworzenia dla poleceń niestandardowych](media/voice-assistants/custom-commands-flow.png "Przepływ tworzenia poleceń niestandardowych")

Skorzystaj z naszego przewodnika Szybki Start, aby korzystać z pierwszej niestandardowej aplikacji poleceń wykonywanych w ciągu mniej niż 10 minut.

* [Tworzenie asystenta głosowego przy użyciu poleceń niestandardowych](quickstart-custom-commands-application.md)

Po zakończeniu korzystania z przewodnika Szybki Start zapoznaj się z przewodnikami z instrukcjami dotyczącymi projektowania, opracowywania, debugowania, wdrażania i integrowania aplikacji poleceń niestandardowych.

## <a name="building-voice-assistants-with-custom-commands"></a>Tworzenie asystentów głosowych przy użyciu poleceń niestandardowych
> [!VIDEO https://www.youtube.com/embed/1zr0umHGFyc]

## <a name="next-steps"></a>Następne kroki

* [Uzyskaj bezpłatnie klucz subskrypcji usługi mowy](overview.md#try-the-speech-service-for-free)
* [Wyświetl nasze repozytorium asystentów głosowych w serwisie GitHub na potrzeby przykładów](https://aka.ms/speech/cc-samples)
* [Przejdź do narzędzia Speech Studio, aby wypróbować polecenia niestandardowe](https://speech.microsoft.com/customcommands)
* [Pobieranie zestawu Speech SDK](speech-sdk.md)
