---
title: Polecenia niestandardowe (wersja zapoznawcza) — usługa mowy
titleSuffix: Azure Cognitive Services
description: Omówienie funkcji, możliwości i ograniczeń dla poleceń niestandardowych (wersja zapoznawcza), rozwiązanie do tworzenia aplikacji głosowych.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: travisw
ms.openlocfilehash: 3c02e823202aa848a4de94885276835899562e4b
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2020
ms.locfileid: "84266019"
---
# <a name="what-is-custom-commands-preview"></a>Co to są polecenia niestandardowe (wersja zapoznawcza)?

Aplikacje głosowe, takie jak [Asystenci głosu](voice-assistants.md) , nasłuchują użytkowników i podejmują działania w odpowiedzi, często mówiąc z powrotem. Używają [zamiany mowy na tekst](speech-to-text.md) , aby transkrypcja mowę użytkownika, a następnie podejmować działania dotyczące interpretacji języka naturalnego tekstu. Ta akcja często obejmuje głosowanie danych wyjściowych wygenerowanego przez asystenta z funkcją [zamiany tekstu na mowę](text-to-speech.md). Urządzenia nawiązują połączenie z asystentami z obiektem zestawu Speech SDK `DialogServiceConnector` .

**Polecenia niestandardowe (wersja zapoznawcza)** to usprawnione rozwiązanie do tworzenia aplikacji głosowych. Zapewnia ujednolicone środowisko tworzenia, automatyczny model hostingu i stosunkowo niższą złożoność, a także inne opcje, takie jak [bezpośrednie rozpoznawanie linii](direct-line-speech.md). Upraszcza to jednak zmniejszenie elastyczności. W związku z tym polecenia niestandardowe (wersja zapoznawcza) najlepiej nadają się do wykonywania zadań lub scenariuszy kontroli poleceń. Jest to szczególnie dobrze dopasowane do Internet rzeczy (IoT) i urządzeń bezobsługowych.

W przypadku złożonej interakcji z konwersacjami i integracji z innymi rozwiązaniami, takimi jak [rozwiązanie dla Asystenta wirtualnego i szablon przedsiębiorstwa](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview) , zaleca się korzystanie z bezpośredniej mowy wiersza.

Dobre kandydaci dla poleceń niestandardowych (wersja zapoznawcza) mają stałe słownictwo ze zdefiniowanymi zestawami zmiennych. Na przykład doskonałe zadania automatyzacji, takie jak kontrolowanie termostatu, są idealne.

   ![Przykłady scenariuszy uzupełniania zadań](media/voice-assistants/task-completion-examples.png "Przykłady uzupełniania zadań")

## <a name="getting-started-with-custom-commands-preview"></a>Wprowadzenie do poleceń niestandardowych (wersja zapoznawcza)

Pierwszym krokiem korzystania z poleceń niestandardowych (wersja zapoznawcza) w celu utworzenia aplikacji głosowej jest [uzyskanie klucza subskrypcji mowy](get-started.md) i uzyskanie dostępu do konstruktora poleceń niestandardowych (wersja zapoznawcza) w programie [Speech Studio](https://speech.microsoft.com). Z tego miejsca można utworzyć nową aplikację poleceń niestandardowych (wersja zapoznawcza) i opublikować ją, po której aplikacja na urządzeniu może komunikować się z nią przy użyciu zestawu Speech SDK.

   ![Przepływ tworzenia dla poleceń niestandardowych (wersja zapoznawcza)](media/voice-assistants/custom-commands-flow.png "Przepływ tworzenia poleceń niestandardowych (wersja zapoznawcza)")

Oferujemy Przewodniki Szybki Start zaprojektowane do uruchamiania kodu w mniej niż 10 minut.

* [Tworzenie aplikacji poleceń niestandardowych (wersja zapoznawcza)](quickstart-custom-speech-commands-create-new.md)
* [Tworzenie aplikacji poleceń niestandardowych (wersja zapoznawcza) z parametrami](quickstart-custom-speech-commands-create-parameters.md)
* [Nawiązywanie połączenia z aplikacją Custom Commands (wersja zapoznawcza) za pomocą zestawu Speech SDK, C #](quickstart-custom-speech-commands-speech-sdk.md)

Po zakończeniu pracy z przewodnikami szybki start Poznaj nasze porady.

- [Dodawanie walidacji do parametrów polecenia niestandardowego](./how-to-custom-speech-commands-validations.md)
- [Spełnienie poleceń na kliencie przy użyciu zestawu Speech SDK](./how-to-custom-speech-commands-fulfill-sdk.md)
- [Dodawanie potwierdzenia do polecenia niestandardowego](./how-to-custom-speech-commands-confirmations.md)
- [Dodawanie korekty jednoetapowej do polecenia niestandardowego](./how-to-custom-speech-commands-one-step-correction.md)

## <a name="next-steps"></a>Następne kroki

* [Uzyskaj bezpłatnie klucz subskrypcji usługi mowy](get-started.md)
* [Przejdź do narzędzia Speech Studio, aby wypróbować polecenia niestandardowe](https://speech.microsoft.com)
* [Pobieranie zestawu Speech SDK](speech-sdk.md)
