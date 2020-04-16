---
title: Tworzenie zawartości audio — usługa mowy
titleSuffix: Azure Cognitive Services
description: Tworzenie zawartości audio to narzędzie online, które pozwala dostosować i dostosować dane wyjściowe tekstu na mowę firmy Microsoft dla aplikacji i produktów.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: trbye
ms.openlocfilehash: a263e7e17cda64a8519bab215f97fdf26e88d9d2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81402238"
---
# <a name="improve-synthesis-with-audio-content-creation"></a>Poprawa syntezy dzięki tworzeniu zawartości audio

[Tworzenie zawartości audio](https://aka.ms/audiocontentcreation) to narzędzie online, które pozwala dostosować i dostosować dane wyjściowe tekstu na mowę firmy Microsoft dla aplikacji i produktów. Za pomocą tego narzędzia można precyzyjnie dostroić głosy publiczne i niestandardowe, aby uzyskać dokładniejsze wyrażenia naturalne, oraz zarządzać danymi wyjściowymi w chmurze.

Narzędzie Do tworzenia zawartości audio jest oparte na [języku znaczników syntezy mowy (SSML).](speech-synthesis-markup.md) Aby uprościć dostosowywanie i dostrajanie, tworzenie zawartości audio umożliwia wizualną kontrolę wyjść zamiany tekstu na mowę w czasie rzeczywistym.

## <a name="how-does-it-work"></a>Jak to działa?

Ten diagram przedstawia kroki, które należy wykonać w celu dostrojenia i wyeksportowania niestandardowych wyjść zamiany mowy na tekst. Skorzystaj z poniższych linków, aby dowiedzieć się więcej o każdym kroku.

![](media/audio-content-creation/audio-content-creation-diagram.jpg)

1. Pierwszym krokiem jest [utworzenie konta platformy Azure, zarejestrowanie zasobu mowy i uzyskanie klucza subskrypcji.](#create-a-speech-resource) Po utworzeniu klucza subskrypcji można go używać do wywoływania usługi Mowy i uzyskiwania dostępu do [tworzenia zawartości audio.](https://aka.ms/audiocontentcreation)
2. [Utwórz plik dostrajania dźwięku](#create-an-audio-tuning-file) przy użyciu zwykłego tekstu lub SSML.
3. Wybierz głos i język, który chcesz dostroić. Tworzenie zawartości audio obejmuje wszystkie [głosy zamiany tekstu na mowę firmy Microsoft.](language-support.md#text-to-speech) Możesz użyć standardowego, neuronowego lub własnego niestandardowego głosu.
   >[!NOTE]
   > Gated dostęp jest dostępny dla niestandardowych głosów neuronowych, które pozwalają na tworzenie głosów wysokiej rozdzielczości podobnych do naturalnie brzmiącej mowy. Aby uzyskać więcej informacji, zobacz [Proces gating](https://aka.ms/ignite2019/speech/ethics).

4. Przejrzyj wynik domyślny. Następnie użyj narzędzia do strojenia, aby dostosować wymowę, wysokość, szybkość, intonację, styl głosu i inne. Aby uzyskać pełną listę opcji, zobacz [Język znaczników syntezy mowy](speech-synthesis-markup.md).
5. Zapisz i [wyeksportuj dostrojony dźwięk](#export-tuned-audio). Po zapisaniu ścieżki strojenia w systemie można kontynuować pracę i iterować na wyjściu. Gdy dane wyjściowe są zadowalające, można utworzyć zadanie tworzenia dźwięku za pomocą funkcji eksportu. Możesz obserwować stan zadania eksportu i pobrać dane wyjściowe do użytku z aplikacjami i produktami.
6. Ostatnim krokiem jest użycie niestandardowego dostrojonego głosu w aplikacjach i produktach.

## <a name="create-a-speech-resource"></a>Tworzenie zasobu Mowy

Wykonaj następujące kroki, aby utworzyć zasób mowy i połączyć go z Speech Studio.

1. Postępuj zgodnie z tymi instrukcjami, aby [utworzyć konto platformy Azure](get-started.md#new-resource) i utworzyć [zasób mowy.](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#create-the-resource) Upewnij się, że warstwa cenowa jest ustawiona na **S0**. Jeśli używasz jednego z głosów neuronalnych, upewnij się, że utworzysz zasób w [obsługiwanym regionie](regions.md#standard-and-neural-voices).
2. Zaloguj się do [tworzenia zawartości audio](https://aka.ms/audiocontentcreation).
3. Zaznacz istniejący projekt lub kliknij przycisk **Utwórz nowy**.
4. Subskrypcję można zmodyfikować w dowolnym momencie za pomocą opcji **Ustawienia,** znajdującej się w górnej części urządzenia nawigacyjnego.

## <a name="create-an-audio-tuning-file"></a>Tworzenie pliku dostrajania dźwięku

Istnieją dwa sposoby, aby uzyskać zawartość do narzędzia Tworzenie zawartości audio.

**Opcja 1.**

1. Po zalogowaniu się do [tworzenia zawartości audio](https://aka.ms/audiocontentcreation)kliknij przycisk **Dostrajanie dźwięku,** aby utworzyć nowy plik dostrajania dźwięku.
2. Po wyświetleniu okna edycji można wprowadzić maksymalnie 10 000 znaków.
3. Nie zapomnij zapisać.

**Wariant 2:**

1. Po zalogowaniu się do [tworzenia zawartości audio](https://aka.ms/audiocontentcreation)kliknij przycisk **Przekaż,** aby zaimportować jeden lub więcej plików tekstowych. Obsługiwane są zarówno zwykły tekst, jak i SSML.
2. Podczas przekazywania plików tekstowych upewnij się, że zawartość spełnia te wymagania.

   | Właściwość | Wartość / Uwagi |
   |----------|---------------|
   | Format pliku | Zwykły tekst (txt)<br/> Tekst SSML (txt)<br/> Pliki zip nie są obsługiwane |
   | Format kodowania | UTF-8 |
   | Nazwa pliku | Każdy plik musi mieć unikatową nazwę. Duplikaty nie są obsługiwane. |
   | Długość tekstu | Pliki tekstowe nie mogą przekraczać 10 000 znaków. |
   | Ograniczenia SSML | Każdy plik SSML może zawierać tylko jeden fragment SSML. |

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

## <a name="export-tuned-audio"></a>Eksportowanie dostrojonego dźwięku

Po przejrzeniu wyjścia audio i spełnieniu warunków dostrajania i regulacji można wyeksportować dźwięk.

1. W narzędziu [Tworzenie zawartości audio](https://aka.ms/audiocontentcreation) kliknij pozycję **Eksportuj,** aby utworzyć zadanie tworzenia dźwięku.
2. Wybierz format wyjściowy dostrojonego dźwięku. Poniżej znajduje się lista obsługiwanych formatów i przykładowych stawek.
3. Stan zadania można wyświetlić na karcie **Eksportowanie zadania.** Jeśli zadanie nie powiedzie się, zobacz stronę szczegółowych informacji dla pełnego raportu.
4. Po zakończeniu zadania dźwięk będzie dostępny do pobrania na karcie **Biblioteka audio.**
5. Kliknij **pozycję Pobierz**. Teraz możesz korzystać z niestandardowego dostrojonego dźwięku w aplikacjach lub produktach.

### <a name="supported-audio-formats"></a>Obsługiwane formaty audio

| Format | Częstotliwość próbkowania 16 kHz | Częstotliwość próbkowania 24 kHz |
|--------|--------------------|--------------------|
| Wav | riff-16khz-16bit-mono-pcm | riff-24khz-16bit-mono-pcm |
| Mp3 | audio-16khz-128kbitrate-mono-mp3 | audio-24khz-160kbitrate-mono-mp3 |

## <a name="see-also"></a>Zobacz też

* [Długi interfejs API audio](https://aka.ms/long-audio-api)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Studio mowy](https://speech.microsoft.com)
