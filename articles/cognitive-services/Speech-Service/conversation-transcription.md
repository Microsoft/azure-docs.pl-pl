---
title: Transkrypcja konwersacji (wersja zapoznawcza) — usługa mowy
titleSuffix: Azure Cognitive Services
description: Transkrypcja konwersacji to rozwiązanie dla spotkań, które łączy funkcje rozpoznawania, głosu i diarization, aby zapewnić transkrypcję dowolnej rozmowy.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: trbye
ms.openlocfilehash: dcc7721aec067c4de309e3fdd926245a9d240f0d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "81402514"
---
# <a name="what-is-conversation-transcription-in-meetings-preview"></a>Co to jest transkrypcja konwersacji w trakcie spotkań (wersja zapoznawcza)?

Transkrypcja konwersacji to rozwiązanie typu ["Zamiana mowy na tekst](speech-to-text.md) ", które łączy rozpoznawanie mowy, identyfikację osoby mówiącej i wyrażenie zdania z każdym głośnikem (znanym także jako _diarization_), aby zapewnić w czasie rzeczywistym i/lub asynchroniczne transkrypcję każdej konwersacji. Transkrypcja konwersacji odróżnia głośniki w konwersacji, aby określić, którzy z nich korzystają i kiedy ułatwiają deweloperom Dodawanie mowy do tekstu do aplikacji, które wykonują wiele diarizationów.

## <a name="key-features"></a>Najważniejsze funkcje

- **Sygnatury czasowe** — każdy głośnik wypowiedź ma sygnaturę czasową, dzięki czemu można łatwo znaleźć, kiedy wyraz został podany.
- **Czytelne transkrypcje** — transkrypcje są automatycznie dodawane do elementów formatowania i interpunkcji w celu zapewnienia, że tekst jest ściśle zgodny z informacjami.
- **Profile użytkowników** — profile użytkowników są generowane przez zbieranie próbek głosu użytkownika i wysyłanie ich do generacji sygnatur.
- **Identyfikacja osoby mówiącej** — głośniki są identyfikowane przy użyciu profilów użytkowników i do każdego z nich jest przypisany _Identyfikator osoby mówiącej_ .
- Diarization z obsługą **wielodostępności** — Określ, kto i co z nich ma za pomocą syntezy strumienia audio przy użyciu każdego identyfikatora osoby mówiącej.
- **Transkrypcja w czasie rzeczywistym** — udostępniaj transkrypcje na żywo, które mówią, co i kiedy się dzieje w trakcie konwersacji.
- **asynchroniczne transkrypcje** — zapewniają transkrypcje o większej dokładności przy użyciu wielokanałowego strumienia audio.

> [!NOTE]
> Chociaż transkrypcja konwersacji nie ogranicza liczby głośników w pokoju, jest zoptymalizowana pod kątem 2-10 głośników na sesję.

## <a name="use-cases"></a>Przypadki zastosowań

### <a name="inclusive-meetings"></a>Załączne spotkania

Aby zapewnić, że dla wszystkich osób, takich jak uczestnicy niesłyszący i słabo słyszący, ważne jest, aby mieć transkrypcję w czasie rzeczywistym. Transkrypcja konwersacji w trybie czasu rzeczywistego zabiera dźwięk i określa, kto jest w trakcie, co pozwala wszystkim uczestnikom spotkania śledzić transkrypcję i uczestniczyć w spotkaniu bez opóźnień.

### <a name="improved-efficiency"></a>Zwiększona wydajność

Uczestnicy spotkania mogą skoncentrować się na spotkaniu i pozostawiać uwagę na transkrypcję. Uczestnicy mogą aktywnie uczestniczyć w spotkaniu i szybko postępować zgodnie z kolejnymi krokami, korzystając z transkrypcji, a nie Robienie notatek i potencjalnie brakujących coś podczas spotkania.

## <a name="how-it-works"></a>Jak to działa

Jest to ogólny przegląd sposobu działania transkrypcji konwersacji.

![Diagram transkrypcji importowania konwersacji](media/scenarios/conversation-transcription-service.png)

## <a name="expected-inputs"></a>Oczekiwane dane wejściowe

- **Wielokanałowy strumień audio** — Aby uzyskać szczegółowe informacje dotyczące specyfikacji i projektowania, zobacz [mikrofon zestawu SDK urządzenia Microsoft Speech](https://aka.ms/cts/microphone). Aby dowiedzieć się więcej lub zakupić zestaw deweloperski, zobacz temat [pobieranie zestawu SDK urządzenia Microsoft Speech](https://aka.ms/cts/getsdk).
- **Przykłady głosu użytkownika** — transkrypcja konwersacji wymaga podania profilów użytkowników przed konwersacją. Należy zebrać nagrania audio od poszczególnych użytkowników, a następnie wysłać nagrań do [usługi generacji podpisów](https://aka.ms/cts/signaturegenservice) , aby zweryfikować dźwięk i wygenerować profile użytkowników.

## <a name="real-time-vs-asynchronous"></a>W czasie rzeczywistym a asynchronicznie

Transkrypcja konwersacji oferuje trzy tryby transkrypcji:

### <a name="real-time"></a>Przesyłanie w czasie rzeczywistym

Dane audio są przetwarzane na żywo w celu zwrócenia identyfikatora głośnika + transkrypcji. Wybierz ten tryb, jeśli wymaganie dotyczące rozwiązania transkrypcji ma dostarczyć uczestnikom konwersacji dynamiczny widok transkrypcji trwającej konwersacji. Na przykład utworzenie aplikacji w celu zapewnienia większej dostępności spotkań niesłyszących i słabo-słyszących jest idealnym rozwiązaniem w przypadku transkrypcji w czasie rzeczywistym.

### <a name="asynchronous"></a>Asynchroniczny

Dane audio to przetwarzanie wsadowe w celu zwrócenia identyfikatora i transkrypcji osoby mówiącej. Wybierz ten tryb, jeśli wymagania dotyczące rozwiązania transkrypcji mają zapewnić większą dokładność bez aktywnego widoku transkrypcji. Jeśli na przykład chcesz skompilować aplikację, aby umożliwić uczestnikom spotkania łatwe przechwycenie nieodebranych spotkań, użyj trybu transkrypcji asynchronicznej, aby uzyskać wyniki transkrypcji o wysokiej dokładności.

### <a name="real-time-plus-asynchronous"></a>W czasie rzeczywistym i asynchronicznie

Dane audio są przetwarzane na żywo, aby zwracały identyfikator i transkrypcję prezentera, a także utworzyć żądanie, aby uzyskać transkrypcję o wysokiej dokładności poprzez asynchroniczne przetwarzanie. Wybierz ten tryb, jeśli aplikacja ma potrzebę transkrypcji w czasie rzeczywistym, ale wymaga również większego transkrypcji dokładności do użycia po wystąpieniu konwersacji lub spotkania.

## <a name="language-support"></a>Obsługa języków

Obecnie transkrypcja konwersacji obsługuje "en-US" i "zh-CN" w następujących regionach: *środkowe* i *eastasia*. Jeśli potrzebujesz dodatkowej obsługi ustawień regionalnych, skontaktuj się z [członkiem załogi funkcji transkrypcji](mailto:CTSFeatureCrew@microsoft.com).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Transkrypcja konwersacje w czasie rzeczywistym](how-to-use-conversation-transcription-service.md)
