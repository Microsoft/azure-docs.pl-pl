---
title: Czym jest usługa rozpoznawania mowy?
titleSuffix: Azure Cognitive Services
description: Usługa Mowy to ujednolicenie mowy na tekst, zamiany tekstu na mowę i tłumaczenia mowy na jedną subskrypcję platformy Azure. Dodawanie mowy do aplikacji, narzędzi i urządzeń za pomocą zestawu SDK mowy, zestawu SDK urządzeń mowy lub interfejsów API REST.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: overview
ms.date: 03/12/2020
ms.author: trbye
ms.openlocfilehash: 464e21caf3105b12f0fbf44dc2aa3674a252d3c6
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81401038"
---
# <a name="what-is-the-speech-service"></a>Czym jest usługa rozpoznawania mowy?

Usługa Mowy to ujednolicenie zamiany mowy na tekst, zamiany tekstu na mowę i tłumaczenia mowy na jedną subskrypcję platformy Azure. Dzięki zestawowi [SDK,](speech-sdk-reference.md) [Speech Devices SDK](https://aka.ms/sdsdk-quickstart)lub [REST API](rest-apis.md)można łatwo włączyć mowę, aby umożliwić korzystanie z aplikacji, narzędzi i urządzeń.

> [!IMPORTANT]
> Usługa mowy zastąpiła interfejs API mowy Bing i mowę tłumacza. Aby uzyskać instrukcje dotyczące migracji, zobacz _Instrukcje dotyczące obsługi > migracji._

Te funkcje składają się na usługę mowy. Użyj łączy w tej tabeli, aby dowiedzieć się więcej na temat typowych przypadków użycia dla każdej funkcji lub przeglądać odwołanie interfejsu API.

| Usługa | Funkcja | Opis | SDK | REST |
|---------|---------|-------------|-----|------|
| [Zamiana mowy na tekst](speech-to-text.md) | Zamiana mowy w czasie rzeczywistym na tekst | Zamiana mowy na tekst jest transkrybować lub tłumaczyć strumienie audio lub pliki lokalne na tekst w czasie rzeczywistym, który mogą wykorzystywać lub wyświetlać aplikacje, narzędzia lub urządzenia. Użyj zamiany mowy na tekst z [funkcją rozumienia języka (LUIS),](https://docs.microsoft.com/azure/cognitive-services/luis/) aby wyprowadzić intencje użytkownika z transkrybowanego mowę i działać na poleceniach głosowych. | [Tak](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Tak](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Wsadowa zamiana mowy na tekst](batch-transcription.md) | Wsadowa zamiana mowy na tekst umożliwia asynchronizowaną transkrypcję mowy na tekst dużych ilości danych audio mowy przechowywanych w usłudze Azure Blob Storage. Oprócz konwersji dźwięku mowy do tekstu, Batch Speech-to-text umożliwia również diarization i analizy tonacji. | Nie | [Tak](https://westus.cris.ai/swagger/ui/index) |
| | [Konwersacja na wielu urządzeniach](multi-device-conversation.md) | Łączenie wielu urządzeń lub klientów w konwersacji w celu wysyłania wiadomości opartych na mowie lub tekście, z łatwą obsługą transkrypcji i tłumaczenia| Tak | Nie |
| | [Transkrypcja konwersacji](conversation-transcription-service.md) | Umożliwia rozpoznawanie mowy w czasie rzeczywistym, identyfikację prelegentów i diarization. Idealnie nadaje się do transkrypcji spotkań osobiście z możliwością wyróżniania mówców. | Tak | Nie |
| | [Tworzenie niestandardowych modeli mowy](#customize-your-speech-experience) | Jeśli używasz zamiany mowy na tekst do rozpoznawania i transkrypcji w unikatowym środowisku, można tworzyć i szkolić niestandardowe modele akustyczne, językowe i wymowy w celu rozwiązania problemu otoczenia lub słownictwa specyficznego dla branży. | Nie | [Tak](https://westus.cris.ai/swagger/ui/index) |
| [Text-to-speech](text-to-speech.md) | Zamiana tekstu na mowę | Zamiana tekstu na mowę konwertuje tekst wejściowy na mowę syntetyzowaną przypominającą człowieka za pomocą [języka SSML (Speech Synthesis Markup Language).](speech-synthesis-markup.md) Wybieraj spośród standardowych głosów i głosów neuronowych (patrz [Obsługa języka](language-support.md)). | [Tak](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Tak](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Tworzenie niestandardowych głosów](#customize-your-speech-experience) | Twórz niestandardowe czcionki głosowe unikatowe dla Twojej marki lub produktu. | Nie | [Tak](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Tłumaczenie mowy](speech-translation.md) | Tłumaczenie mowy | Tłumaczenie mowy umożliwia w czasie rzeczywistym, wielojęzyczne tłumaczenie mowy na aplikacje, narzędzia i urządzenia. Ta usługa służy do tłumaczenia zamiany mowy na mowę i zamiany mowy na tekst. | [Tak](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | Nie |
| [Asystenci głosowi](voice-assistants.md) | Asystenci głosowi | Asystenci głosowi korzystający z usługi Mowy umożliwiają deweloperom tworzenie naturalnych, podobnych do człowieka interfejsów konwersacyjnych dla swoich aplikacji i środowisk. Usługa asystenta głosowego zapewnia szybką i niezawodną interakcję między urządzeniem a implementacją asystenta, która używa kanału direct line speech programu Bot Framework lub zintegrowanej usługi Polecenia niestandardowe (Wersja zapoznawcza) do wykonywania zadań. | [Tak](voice-assistants.md) | Nie |


[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

## <a name="try-the-speech-service"></a>Wypróbuj usługę mowy

Oferujemy szybkie starty w najpopularniejszych językach programowania, z których każdy ma na celu uruchomienie kodu w mniej niż 10 minut. Ta tabela zawiera najpopularniejsze przewodniki Szybki start dla każdej funkcji. Użyj nawigacji po lewej stronie, aby eksplorować dodatkowe języki i platformy.

| Zamiana mowy na tekst (SDK) | Zamiana tekstu na mowę (SDK) | Tłumaczenie pisemne (SDK) |
|----------------------|----------------------|-------------------|
| [Rozpoznawanie mowy z pliku dźwiękowego](quickstarts/speech-to-text-from-file.md) | [Syntetyzowanie mowy do pliku dźwiękowego](quickstarts/text-to-speech-audio-file.md) | [Tłumaczenie mowy na tekst](quickstarts/translate-speech-to-text.md) |
| [Rozpoznawanie mowy przy użyciu mikrofonu](quickstarts/speech-to-text-from-microphone.md) | [Syntetyzowanie mowy przy użyciu głośnika](quickstarts/text-to-speech.md) | [Tłumaczenie mowy na wiele języków docelowych](quickstarts/translate-speech-to-text-multiple-languages.md) |
| [Rozpoznawanie mowy przechowywanej w usłudze Blob Storage](quickstarts/from-blob.md) | [Synteza asynchroniczna dla długich form audio](quickstarts/text-to-speech/async-synthesis-long-form-audio.md) | [Tłumaczenie mowy na mowę](quickstarts/translate-speech-to-speech.md) |

> [!NOTE]
> Zamiana mowy na tekst i zamiana tekstu na mowę mają również punkty końcowe REST i skojarzone przewodniki Szybki start.

Po miał okazję korzystać z usługi mowy, wypróbuj nasz samouczek, który uczy, jak rozpoznać intencje z mowy przy użyciu zestawu SDK mowy i usługi LUIS.

- [Samouczek: Rozpoznawanie intencji z mowy za pomocą zestawu SDK mowy i usługi LUIS, C #](how-to-recognize-intents-from-speech-csharp.md)
- [Samouczek: Voice włączyć bot z speech SDK, C #](tutorial-voice-enable-your-bot-speech-sdk.md)
- [Samouczek: Tworzenie aplikacji Flask do tłumaczenia tekstu, analizowania tonacji i syntezy przetłumaczonego tekstu na mowę, REST](https://docs.microsoft.com/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?toc=%2fazure%2fcognitive-services%2fspeech-service%2ftoc.json&bc=%2fazure%2fcognitive-services%2fspeech-service%2fbreadcrumb%2ftoc.json&toc=%2Fen-us%2Fazure%2Fcognitive-services%2Fspeech-service%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)

## <a name="get-sample-code"></a>Pobieranie przykładowego kodu

Przykładowy kod jest dostępny w usłudze GitHub dla usługi mowy. Te przykłady obejmują typowe scenariusze, takie jak odczyt dźwięku z pliku lub strumienia, ciągłe i pojedyncze ujęcie rozpoznawania i pracy z modelami niestandardowymi. Użyj tych łączy, aby wyświetlić próbki SDK i REST:

- [Przykłady mowy na tekst, zamiany tekstu na mowę i tłumaczenia mowy (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Próbki transkrypcji partii (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
- [Próbki zamiany tekstu na mowę (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)
- [Próbki asystenta głosowego (SDK)](https://aka.ms/csspeech/samples)

## <a name="customize-your-speech-experience"></a>Dostosowywanie funkcji mowy

Usługa Mowy działa dobrze z wbudowanymi modelami, jednak można jeszcze bardziej dostosować i dostroić środowisko dla produktu lub środowiska. Opcje dostosowywania obejmują zarówno dostrajanie modelu akustycznego, jak i unikalne czcionki głosowe dla twojej marki.

| Usługa rozpoznawania mowy | Platforma | Opis |
| -------------- | -------- | ----------- |
| Zamiana mowy na tekst | [Mowa niestandardowa](https://aka.ms/customspeech) | Dostosuj modele rozpoznawania mowy do swoich potrzeb i dostępnych danych. Pokonaj bariery rozpoznawania mowy, takie jak styl mówienia, słownictwo i hałas w tle. |
| Zamiana tekstu na mowę | [Niestandardowy głos](https://aka.ms/customvoice) | Utwórz rozpoznawalny, unikatowy głos dla aplikacji zamieniających tekst na mowę, które korzystają z Twoich dostępnych danych mowy. Można dodatkowo dostosować wyjścia głosowe, dostosowując zestaw parametrów głosowych. |

## <a name="reference-docs"></a>Dokumenty referencyjne

- [Zestaw SDK rozpoznawania mowy](speech-sdk-reference.md)
- [Zestaw Speech Devices SDK](speech-devices-sdk.md)
- [INTERFEJS API REST: Zamiana mowy na tekst](rest-speech-to-text.md)
- [INTERFEJS API REST: Zamiana tekstu na mowę](rest-text-to-speech.md)
- [INTERFEJS API REST: transkrypcja i dostosowywanie partii](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Uzyskaj bezpłatny klucz subskrypcji usługi mowy](get-started.md)
