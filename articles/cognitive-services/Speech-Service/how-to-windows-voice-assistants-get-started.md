---
title: Asystenci głosu w systemie Windows — wprowadzenie
titleSuffix: Azure Cognitive Services
description: Kroki umożliwiające rozpoczęcie opracowywania agenta głosowego systemu Windows, w tym odwołania do przykładowego przewodnika Szybki Start.
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.openlocfilehash: 4ea2f88b02738645af3f8fc32d5fdb99168a1122
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "82997382"
---
# <a name="getting-started-with-voice-assistants-on-windows"></a>Wprowadzenie do asystentów głosowych w systemie Windows

Ten przewodnik przeprowadzi Cię przez kroki umożliwiające rozpoczęcie tworzenia asystenta głosowego w systemie Windows.

## <a name="set-up-your-development-environment"></a>Konfigurowanie środowiska projektowego

Aby rozpocząć tworzenie asystenta głosowego dla systemu Windows, musisz upewnić się, że masz odpowiednie środowisko programistyczne.

- **Program Visual Studio:**   Konieczne będzie zainstalowanie [Microsoft Visual Studio 2017](https://visualstudio.microsoft.com/), wersja Community lub nowsza
- **Wersja systemu Windows**: komputer z kompilacją szybkiego pierścienia z niejawnym testerem systemu Windows i wersjami niejawnego programu testów systemu Windows Windows SDK.Ten przykładowy kod jest weryfikowany jako praca z kompilacjami niejawnego testera systemu Windows 19025.vb_release_analog .191112-1600 przy użyciu Windows SDK 19018.Wszystkie kompilacje lub zestawy SDK powyżej określonych wersji powinny być zgodne.
- **Platformy UWP Development Tools**: obciążenie programowanie platforma uniwersalna systemu Windows w programie Visual Studio.Zapoznaj się ze stroną platformy UWP [Get Up](https://docs.microsoft.com/windows/uwp/get-started/get-set-up) , aby przygotować maszynę do tworzenia aplikacji platformy UWP.
- **Działający mikrofon i wyjście audio**

## <a name="obtain-resources-from-microsoft"></a>Uzyskaj zasoby od firmy Microsoft

Niektóre zasoby wymagane przez całkowicie dostosowany agenta głosu w systemie Windows będą wymagały zasobów od firmy Microsoft. [Przykład asystenta głosowego platformy UWP](windows-voice-assistants-faq.md#the-uwp-voice-assistant-sample) zawiera przykładowe wersje tych zasobów na potrzeby wstępnego tworzenia i testowania, dlatego ta sekcja nie jest konieczna do tworzenia początkowego.

- **Model słów kluczowych:**   Aktywacja głosowa wymaga modelu słowo kluczowe od firmy Microsoft w postaci pliku bin. Plik bin podany w próbce asystenta głosowego platformy UWP jest szkolony na słowie kluczowym "contoso".
- **Token funkcji ograniczonego dostępu:** Ponieważ interfejsy API ConversationalAgent zapewniają dostęp do dźwięku mikrofonu, są one chronione w ramach ograniczeń funkcji ograniczonego dostępu.Aby można było korzystać z funkcji ograniczonego dostępu, należy uzyskać token funkcji ograniczonego dostępu połączonego z tożsamością pakietu aplikacji od firmy Microsoft.

## <a name="establish-a-dialog-service"></a>Ustanów usługę okna dialogowego

W przypadku pełnego środowiska asystenta głosowego aplikacja będzie potrzebować usługi okna dialogowego, która

- Wykryj słowo kluczowe w danym pliku audio
- Nasłuchiwanie danych wejściowych użytkownika i konwertowanie go na tekst
- Podaj tekst do bot
- Przetłumacz tekst odpowiedzi Bot na dane wyjściowe audio

Są to wymagania, które należy wykonać, aby utworzyć podstawową usługę dialogową za pomocą polecenia Direct line Speech.

- **Subskrypcja usługi Speech Services:** Subskrypcja usługi poznawczej mowy na potrzeby konwersji mowy na tekst i zamiany tekstu na mowę. Wypróbuj bezpłatnie usługi mowy w [tym miejscu](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started).
- **Bot Framework bot:**  Bot utworzony przy użyciu programu bot Framework w wersji 4,2 lub nowszej, który subskrybuje funkcję [Direct line mowę](https://docs.microsoft.com/azure/cognitive-services/speech-service/direct-line-speech) w celu włączenia głosu i danych wyjściowych. [Ten przewodnik](https://docs.microsoft.com/azure/cognitive-services/speech-service/tutorial-voice-enable-your-bot-speech-sdk) zawiera instrukcje krok po kroku, które umożliwiają "Echo bot" i subskrybują go w celu przeprowadzenia bezpośredniej zamiany wiersza. Możesz również przejść [tutaj](https://blog.botframework.com/2018/05/07/build-a-microsoft-bot-framework-bot-with-the-bot-builder-sdk-v4/) , aby zapoznać się z instrukcjami dotyczącymi tworzenia dostosowanych bot, a następnie wykonać te [same kroki,](https://docs.microsoft.com/azure/cognitive-services/speech-service/tutorial-voice-enable-your-bot-speech-sdk) Aby zasubskrybować funkcję bezpośredniej zamiany wiersza, ale z nowym bot, a nie "Echo bot".

## <a name="try-out-the-sample-app"></a>Wypróbuj przykładową aplikację

Korzystając z klucza subskrypcji usług mowy i identyfikatora bot bot, możesz wypróbować [przykład asystenta głosowego platformy UWP](windows-voice-assistants-faq.md#the-uwp-voice-assistant-sample). Postępuj zgodnie z instrukcjami w pliku Readme, aby uruchomić aplikację, a następnie wprowadź swoje poświadczenia.

## <a name="create-your-own-voice-assistant-for-windows"></a>Tworzenie własnego asystenta głosowego dla systemu Windows

Po odebraniu tokenu funkcji dostępu ograniczonego i pliku bin od firmy Microsoft można zacząć korzystać z własnego asystenta głosowego w systemie Windows.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Przeczytaj przewodnik implementacji asystenta głosowego](windows-voice-assistants-implementation-guide.md)
