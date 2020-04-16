---
title: Direct Line Speech - Usługa mowy
titleSuffix: Azure Cognitive Services
description: Omówienie funkcji, możliwości i ograniczeń dla asystentów głosowych korzystających z direct line speech z zestawem SDK (Speech Software Development Kit).
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: trbye
ms.openlocfilehash: 6dc125b68af8f9a07731aaae9d858c19c51f934f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81402391"
---
# <a name="what-is-direct-line-speech"></a>Co to jest mowa Direct Line?

**Direct Line Speech** to solidne, kompleksowe rozwiązanie do tworzenia elastycznego, rozszerzalnego asystenta głosowego. Jest zasilany przez Bot Framework i jego kanał Direct Line Speech, który jest zoptymalizowany pod kątem interakcji głosowych, voice-out z botami.

[Asystenci głosowi](voice-assistants.md) słuchają użytkowników i podejmują działania w odpowiedzi, często odwzchłają. Używają [mowy do tekstu](speech-to-text.md) do transkrypcji mowy użytkownika, a następnie podjąć działania w języku naturalnym zrozumienia tekstu. Ta akcja często obejmuje dane wyjściowe wypowiedziane z asystenta wygenerowane za [pomocą zamiany tekstu na mowę.](text-to-speech.md)

Direct Line Speech oferuje najwyższy poziom personalizacji i wyrafinowania dla asystentów głosowych. Jest przeznaczony do konwersacji scenariuszy, które są otwarte, naturalne lub hybrydy z dwóch z zakończenia zadania lub polecenia i kontroli użytkowania. Ten wysoki stopień elastyczności ma większą złożoność, a scenariusze, które są ograniczone do dobrze zdefiniowanych zadań przy użyciu danych wejściowych języka naturalnego, mogą chcieć rozważyć [polecenia niestandardowe (Wersja zapoznawcza)](custom-commands.md) dla usprawnionego środowiska rozwiązania.

## <a name="getting-started-with-direct-line-speech"></a>Wprowadzenie do direct line speech

Pierwszymi krokami tworzenia asystenta głosowego przy użyciu direct line speech jest [uzyskanie klucza subskrypcji mowy,](get-started.md)utworzenie nowego bota skojarzonego z tą subskrypcją i połączenie bota z kanałem Direct Line Speech.

   ![Diagram koncepcyjny przepływu usługi aranżacji mowy wiersza bezpośredniego](media/voice-assistants/overview-directlinespeech.png "Przepływ kanału mowy")

Aby uzyskać kompletny, krok po kroku przewodnik dotyczący tworzenia prostego asystenta głosowego za pomocą direct line speech, zobacz [samouczek umożliwiający obsługę mowy bota za pomocą zestawu SDK mowy i kanału Direct Line Speech](tutorial-voice-enable-your-bot-speech-sdk.md).

Oferujemy również przewodniki Szybki start zaprojektowany, aby szybko uruchomić kod i szybko nauczyć się interfejsów API. Ta tabela zawiera listę przewodników Szybki start asystenta głosowego uporządkowanych według języka i platformy.

| Szybki start | Platforma | Dokumentacja interfejsu API |
|------------|----------|---------------|
| C#, UWP | Windows | [Przeglądaj](https://aka.ms/csspeech/csharpref) |
| Java | Windows, macOS, Linux | [Przeglądaj](https://aka.ms/csspeech/javaref) |
| Java | Android | [Przeglądaj](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>Przykładowy kod

Przykładowy kod do tworzenia asystenta głosowego jest dostępny w usłudze GitHub. Te przykłady obejmują aplikację kliencką do łączenia się z asystentem w kilku popularnych językach programowania.

* [Próbki asystenta głosowego (SDK)](https://aka.ms/csspeech/samples)
* [Samouczek: Głos włącz asystenta za pomocą zestawu SDK mowy, C #](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="customization"></a>Dostosowywanie

Asystenci głosowi zbudowani przy użyciu usługi Mowy mogą korzystać z pełnego zakresu opcji dostosowywania dostępnych dla [zamiany mowy na tekst,](speech-to-text.md) [zamiany tekstu na mowę](text-to-speech.md)i [niestandardowego wyboru słów kluczowych.](speech-devices-sdk-create-kws.md)

> [!NOTE]
> Opcje dostosowywania różnią się w zależności od języka/ustawień regionalnych (patrz [Obsługiwane języki).](supported-languages.md)

Direct Line Speech i związane z nią funkcje dla asystentów głosowych są idealnym uzupełnieniem [rozwiązania Virtual Assistant i szablonu Enterprise.](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview) Chociaż Direct Line Speech może współpracować z dowolnym zgodnym botem, te zasoby zapewniają bazę odniesienia wielokrotnego użytku dla wysokiej jakości konwersacyjnych, a także typowe umiejętności i modele pomocnicze, aby szybko rozpocząć pracę.

## <a name="reference-docs"></a>Dokumenty referencyjne

* [Zestaw SDK rozpoznawania mowy](speech-sdk-reference.md)
* [Usługa Azure Bot](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)

## <a name="next-steps"></a>Następne kroki

* [Uzyskaj bezpłatny klucz subskrypcji usługi mowy](get-started.md)
* [Pobierz sdk mowy](speech-sdk.md)
* [Tworzenie i wdrażanie podstawowego bota](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)
* [Pobierz rozwiązanie Virtual Assistant i szablon Enterprise](https://github.com/Microsoft/AI)
