---
title: Transkrypcja konwersacji (wersja zapoznawcza) — usługa mowy
titleSuffix: Azure Cognitive Services
description: Transkrypcja konwersacji to rozwiązanie dla spotkań, które łączy rozpoznawanie, identyfikator prelegenta i diarization, aby zapewnić transkrypcję każdej konwersacji.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: trbye
ms.openlocfilehash: dcc7721aec067c4de309e3fdd926245a9d240f0d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81402514"
---
# <a name="what-is-conversation-transcription-in-meetings-preview"></a>Co to jest transkrypcja konwersacji podczas spotkań (wersja zapoznawcza)?

Transkrypcja konwersacji to rozwiązanie [zamiany mowy na tekst,](speech-to-text.md) które łączy rozpoznawanie mowy, identyfikację prelegenta i przypisanie zdania każdemu prelegentowi (znane również jako _diarization),_ aby zapewnić transkrypcję w czasie rzeczywistym i/lub asynchronikę każdej konwersacji. Transkrypcja konwersacji odróżnia prelegentów w rozmowie, aby ustalić, kto powiedział, co i kiedy, i ułatwia programistom dodawanie mowy do tekstu do swoich aplikacji, które wykonują diarization wielu głośników.

## <a name="key-features"></a>Najważniejsze funkcje

- **Sygnatury czasowe** — każda wypowiedź prelegenta ma sygnaturę czasową, dzięki czemu można łatwo znaleźć, gdy wyrażenie zostało powiedziane.
- **Czytelne transkrypcje** - transkrypcje mają formatowanie i znaki interpunkcyjne dodawane automatycznie, aby upewnić się, że tekst jest ściśle zgodny z tym, co zostało powiedziane.
- **Profile użytkowników** — profile użytkowników są generowane przez zbieranie próbek głosu użytkownika i wysyłanie ich do generowania podpisu.
- **Identyfikacja prelegenta** - głośniki są identyfikowane za pomocą profili użytkownika, a identyfikator _głośnika_ jest przypisany do każdego z nich.
- **Diarization wielu głośników** - określić, kto powiedział, co syntetyzując strumień audio z każdym identyfikatorem głośnika.
- **Transkrypcja w czasie rzeczywistym** - zapewnić stenogramy na żywo, kto mówi, co i kiedy podczas rozmowy się dzieje.
- **transkrypcja asynchroniza -** zapewniają transkrypcje z większą dokładnością przy użyciu wielokanałowego strumienia audio.

> [!NOTE]
> Chociaż transkrypcja konwersacji nie ogranicza liczby głośników w pomieszczeniu, jest zoptymalizowana pod kątem 2-10 głośników na sesję.

## <a name="use-cases"></a>Przypadki zastosowań

### <a name="inclusive-meetings"></a>Spotkania integracyjne

Aby spotkania były integracyjne dla wszystkich, takich jak uczestnicy niesłyszący i niedosłyszący, ważne jest, aby transkrypcja odbywała się w czasie rzeczywistym. Transkrypcja konwersacji w trybie czasu rzeczywistego wymaga audio spotkania i określa, kto co mówi, pozwalając wszystkim uczestnikom spotkania śledzić transkrypcję i uczestniczyć w spotkaniu bez zwłoki.

### <a name="improved-efficiency"></a>Zwiększona wydajność

Uczestnicy spotkania mogą skupić się na spotkaniu i pozostawić notatki do transkrypcji konwersacji. Uczestnicy mogą aktywnie zaangażować się w spotkanie i szybko śledzić kolejne kroki, używając transkrypcji zamiast notatek i potencjalnie czegoś brakuje podczas spotkania.

## <a name="how-it-works"></a>Jak to działa

Jest to omówienie wysokiego poziomu, jak transkrypcji konwersacji działa.

![Diagram transkrypcji konwersacji importu](media/scenarios/conversation-transcription-service.png)

## <a name="expected-inputs"></a>Oczekiwane dane wejściowe

- **Wielokanałowy strumień audio** — szczegółowe informacje dotyczące specyfikacji i projektu można znaleźć w części [Mikrofon SDK urządzenia mowy firmy Microsoft](https://aka.ms/cts/microphone). Aby dowiedzieć się więcej lub kupić zestaw deweloperski, zobacz [Zestaw Microsoft Speech Device SDK](https://aka.ms/cts/getsdk).
- **Próbki głosu użytkownika** — transkrypcja konwersacji wymaga profili użytkowników przed konwersacją. Należy zbierać nagrania audio od każdego użytkownika, a następnie wysyłać nagrania do [usługi generowania podpisu,](https://aka.ms/cts/signaturegenservice) aby zweryfikować dźwięk i wygenerować profile użytkowników.

## <a name="real-time-vs-asynchronous"></a>W czasie rzeczywistym a asynchroniczne

Transkrypcja konwersacji oferuje trzy tryby transkrypcji:

### <a name="real-time"></a>Przesyłanie w czasie rzeczywistym

Dane audio są przetwarzane na żywo, aby przywrócić identyfikator głośnika + transkrypcję. Wybierz ten tryb, jeśli wymaganie rozwiązania transkrypcji jest zapewnienie uczestnikom konwersacji podglądu na żywo ich trwającej konwersacji. Na przykład tworzenie aplikacji, aby spotkania były bardziej dostępne dla osób niesłyszących i niedosłyszących, jest idealnym przypadkiem użycia transkrypcji w czasie rzeczywistym.

### <a name="asynchronous"></a>Asynchroniczne

Dane audio są przetwarzane wsadowe w celu zwrócenia identyfikatora głośnika i transkrypcji. Wybierz ten tryb, jeśli wymagane rozwiązanie transkrypcji ma zapewnić większą dokładność bez podglądu na żywo. Na przykład jeśli chcesz utworzyć aplikację, aby umożliwić uczestnikom spotkania łatwo nadrobić zaległości w nieodebranych spotkaniach, a następnie użyj trybu transkrypcji asynchronii, aby uzyskać wyniki transkrypcji o wysokiej dokładności.

### <a name="real-time-plus-asynchronous"></a>W czasie rzeczywistym plus asynchroniczne

Dane audio są przetwarzane na żywo, aby zwrócić identyfikator głośnika + transkrypcję, a ponadto żądanie jest tworzone, aby uzyskać transkrypcję o wysokiej dokładności za pośrednictwem przetwarzania asynchroniiowego. Wybierz ten tryb, jeśli aplikacja ma potrzebę transkrypcji w czasie rzeczywistym, ale wymaga również większej dokładności transkrypcji do użycia po wystąpieniu konwersacji lub spotkania.

## <a name="language-support"></a>Obsługa języków

Obecnie Transkrypcja konwersacji obsługuje "en-US" i "zh-CN" w następujących regionach: *centralus* i *eastasia*. Jeśli potrzebujesz dodatkowej pomocy technicznej ustawień regionalnych, skontaktuj się z [personelem funkcji transkrypcji konwersacji.](mailto:CTSFeatureCrew@microsoft.com)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Transkrypcja rozmów w czasie rzeczywistym](how-to-use-conversation-transcription-service.md)
