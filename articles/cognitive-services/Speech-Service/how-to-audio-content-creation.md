---
title: Tworzenie zawartości audio — usługa mowy
titleSuffix: Azure Cognitive Services
description: Tworzenie zawartości audio to narzędzie online, które pozwala dostosowywać i dostrajać dane wyjściowe zamiany tekstu na mowę firmy Microsoft dla aplikacji i produktów.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: trbye
ms.openlocfilehash: a263e7e17cda64a8519bab215f97fdf26e88d9d2
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81402238"
---
# <a name="improve-synthesis-with-audio-content-creation"></a>Poprawa syntezy przy użyciu tworzenia zawartości audio

[Tworzenie zawartości audio](https://aka.ms/audiocontentcreation) to narzędzie online, które pozwala dostosowywać i dostrajać dane wyjściowe zamiany tekstu na mowę firmy Microsoft dla aplikacji i produktów. Za pomocą tego narzędzia można dostrajać publiczne i niestandardowe głosy na potrzeby dokładniejszych wyrażeń naturalnych i zarządzać danymi wyjściowymi w chmurze.

Narzędzie tworzenia zawartości audio jest oparte na [języku SSML (Speech syntezing Language)](speech-synthesis-markup.md). Aby uprościć Dostosowywanie i dostrajanie, tworzenie zawartości audio pozwala wizualnie zbadać wyniki zamiany tekstu na mowę w czasie rzeczywistym.

## <a name="how-does-it-work"></a>Jak to działa?

Na tym diagramie przedstawiono kroki, które należy wykonać, aby dostroić i wyeksportować niestandardowe wyjścia zamiany mowy na tekst. Skorzystaj z poniższych linków, aby dowiedzieć się więcej o każdym z kroków.

![](media/audio-content-creation/audio-content-creation-diagram.jpg)

1. Pierwszym krokiem jest [utworzenie konta platformy Azure, zarejestrowanie zasobu mowy i uzyskanie klucza subskrypcji](#create-a-speech-resource). Po utworzeniu klucza subskrypcji można użyć go do wywołania usługi mowy i uzyskania dostępu do [tworzenia zawartości audio](https://aka.ms/audiocontentcreation).
2. [Utwórz plik dostrajania audio](#create-an-audio-tuning-file) przy użyciu zwykłego tekstu lub SSML.
3. Wybierz głos i język, który chcesz dostosować. Tworzenie zawartości audio obejmuje wszystkie [głosy zamiany tekstu na mowę firmy Microsoft](language-support.md#text-to-speech). Możesz użyć standardowego, neuronowych lub własnego niestandardowego głosu.
   >[!NOTE]
   > Dostęp warunkowy jest dostępny dla niestandardowych głosów neuronowych, które umożliwiają tworzenie głosów o wysokiej rozdzielczości, podobnie jak w przypadku mowy dźwięku naturalnego. Aby uzyskać więcej informacji, zobacz [kontroli Process](https://aka.ms/ignite2019/speech/ethics).

4. Przejrzyj wynik domyślny. Następnie użyj narzędzia dostrajania, aby dopasować wymowę, gęstość, stawkę, intonation, styl głosu itd. Aby zapoznać się z pełną listą opcji, zobacz Language [syntezowania mowy](speech-synthesis-markup.md).
5. Zapisz i [wyeksportuj dostosowany dźwięk](#export-tuned-audio). Po zapisaniu ścieżki dostrajania w systemie można kontynuować pracę i iterację danych wyjściowych. Gdy dane wyjściowe są zadowalające, można utworzyć zadanie tworzenia dźwięku za pomocą funkcji eksportowania. Można obserwować stan zadania eksportu i pobrać dane wyjściowe do użycia z aplikacjami i produktami.
6. Ostatnim krokiem jest użycie niestandardowego, dostrojonego głosu w aplikacjach i produktach.

## <a name="create-a-speech-resource"></a>Tworzenie zasobu mowy

Wykonaj następujące kroki, aby utworzyć zasób mowy i połączyć go z usługą Speech Studio.

1. Postępuj zgodnie z tymi instrukcjami, aby [zarejestrować się w celu utworzenia konta platformy Azure](get-started.md#new-resource) i [utworzyć zasób mowy](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#create-the-resource). Upewnij się, że warstwa cenowa jest ustawiona na **S0**. Jeśli używasz jednego z głosów neuronowych, upewnij się, że zasób jest tworzony w [obsługiwanym regionie](regions.md#standard-and-neural-voices).
2. Zaloguj się do [tworzenia zawartości audio](https://aka.ms/audiocontentcreation).
3. Wybierz istniejący projekt lub kliknij pozycję **Utwórz nowy**.
4. Subskrypcję można zmienić w dowolnym momencie za pomocą opcji **Ustawienia** , która znajduje się w górnym obszarze nawigacji.

## <a name="create-an-audio-tuning-file"></a>Utwórz plik strojenia audio

Istnieją dwa sposoby na uzyskanie zawartości do narzędzia tworzenia zawartości audio.

**Opcja 1:**

1. Po zalogowaniu się do [tworzenia zawartości audio](https://aka.ms/audiocontentcreation)kliknij pozycję **dostrajanie audio** , aby utworzyć nowy plik strojenia audio.
2. Po wyświetleniu okna edycji można wprowadzić do 10 000 znaków.
3. Nie zapomnij zapisać.

**Opcja 2:**

1. Po zalogowaniu się do [tworzenia zawartości audio](https://aka.ms/audiocontentcreation)kliknij przycisk **Przekaż** , aby zaimportować jeden lub więcej plików tekstowych. Obsługiwane są zarówno zwykły tekst, jak i SSML.
2. Po przekazaniu plików tekstowych upewnij się, że zawartość spełnia te wymagania.

   | Właściwość | Wartość/uwagi |
   |----------|---------------|
   | Format pliku | Zwykły tekst (. txt)<br/> Tekst SSML (. txt)<br/> Pliki zip nie są obsługiwane |
   | Format kodowania | UTF-8 |
   | Nazwa pliku | Każdy plik musi mieć unikatową nazwę. Duplikaty nie są obsługiwane. |
   | Długość tekstu | Pliki tekstowe nie mogą zawierać więcej niż 10 000 znaków. |
   | Ograniczenia SSML | Każdy plik SSML może zawierać tylko jedną część SSML. |

### <a name="plain-text-example"></a>Przykład zwykłego tekstu

```txt
Welcome to use Audio Content Creation to customize audio output for your products.
```

### <a name="ssml-text-example"></a>Przykład tekstu SSML

```xml
<speak xmlns="http://www.w3.org/2001/10/synthesis" xmlns:mstts="http://www.w3.org/2001/mstts" version="1.0" xml:lang="en-US">
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, AriaNeural)">
    Welcome to use Audio Content Creation <break time="10ms" />to customize audio output for your products.
    </voice>
</speak>
```

## <a name="export-tuned-audio"></a>Eksportuj dostrojony dźwięk

Po przejrzeniu danych wyjściowych audio i spełnieniu ich dostrajania można wyeksportować dźwięk.

1. W narzędziu do [tworzenia zawartości audio](https://aka.ms/audiocontentcreation) kliknij przycisk **Eksportuj** , aby utworzyć zadanie tworzenia dźwięku.
2. Wybierz format danych wyjściowych dla dostrajania audio. Poniżej znajduje się lista obsługiwanych formatów i częstotliwości próbkowania.
3. Stan zadania można wyświetlić na karcie **Eksportuj zadanie** . Jeśli zadanie nie powiedzie się, zobacz stronę ze szczegółowymi informacjami, aby uzyskać pełny raport.
4. Po zakończeniu zadania dźwięk jest dostępny do pobrania na karcie **biblioteka audio** .
5. Kliknij pozycję **Pobierz**. Teraz możesz przystąpić do używania niestandardowego, dostrojonego dźwięku w aplikacjach lub produktach.

### <a name="supported-audio-formats"></a>Obsługiwane formaty audio

| Format | szybkość próbkowania 16 kHz | częstotliwość próbkowania 24 kHz |
|--------|--------------------|--------------------|
| pliki | RIFF-16khz-16bit-mono-PCM | RIFF-24khz-16bit-mono-PCM |
| formatu | audio-16khz-128kbitrate-mono-MP3 | audio-24khz-160kbitrate-mono-MP3 |

## <a name="see-also"></a>Zobacz także

* [Długi interfejs API audio](https://aka.ms/long-audio-api)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Speech Studio](https://speech.microsoft.com)
