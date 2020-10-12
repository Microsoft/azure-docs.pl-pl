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
ms.openlocfilehash: 72fecbdc173a6174e54a28e48f983965f397ba6a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86224591"
---
# <a name="improve-synthesis-with-the-audio-content-creation-tool"></a>Popraw syntezę przy użyciu narzędzia do tworzenia zawartości audio

[Tworzenie zawartości audio](https://aka.ms/audiocontentcreation) to narzędzie online, które pozwala dostosowywać i dostrajać dane wyjściowe zamiany tekstu na mowę firmy Microsoft dla aplikacji i produktów. Za pomocą tego narzędzia można dostrajać publiczne i niestandardowe głosy na potrzeby dokładniejszych wyrażeń naturalnych i zarządzać danymi wyjściowymi w chmurze.

Narzędzie tworzenia zawartości audio jest oparte na [języku SSML (Speech syntezing Language)](speech-synthesis-markup.md). Aby uprościć Dostosowywanie i dostrajanie, tworzenie zawartości audio pozwala wizualnie zbadać wyniki zamiany tekstu na mowę w czasie rzeczywistym.

Zobacz [Samouczek wideo](https://www.youtube.com/watch?v=O1wIJ7mts_w) dotyczący tworzenia zawartości audio.

## <a name="how-does-it-work"></a>Jak to działa?

Na tym diagramie przedstawiono kroki, które należy wykonać, aby dostosować wyniki zamiany tekstu na mowę. Skorzystaj z poniższych linków, aby dowiedzieć się więcej o każdym z kroków.

![Diagram kroków, które należy wykonać, aby dostosować wyniki zamiany tekstu na mowę.](media/audio-content-creation/audio-content-creation-diagram.jpg)

1. [Skonfiguruj swoje konto platformy Azure i zasób mowy](#set-up-your-azure-account-and-speech-resource) , aby rozpocząć pracę.
2. [Utwórz plik dostrajania audio](#create-an-audio-tuning-file) przy użyciu zwykłego tekstu lub skryptów SSML.
3. Wybierz głos i język zawartości skryptu. Tworzenie zawartości audio obejmuje wszystkie [głosy zamiany tekstu na mowę firmy Microsoft](language-support.md#text-to-speech). Możesz użyć standardowego, neuronowych lub własnego niestandardowego głosu.
   >[!NOTE]
   > Dostęp warunkowy jest dostępny dla niestandardowych głosów neuronowych, które umożliwiają tworzenie głosów o wysokiej rozdzielczości, podobnie jak w przypadku mowy dźwięku naturalnego. Aby uzyskać więcej informacji, zobacz [kontroli Process](https://aka.ms/ignite2019/speech/ethics).

4. Przejrzyj domyślne dane wyjściowe syntezy. Następnie Popraw dane wyjściowe przez dostosowanie wymowy, przerwania, skoku, stawki, intonation, stylu głosu i innych. Aby zapoznać się z pełną listą opcji, zobacz Language [syntezowania mowy](speech-synthesis-markup.md). Oto [film wideo](https://youtu.be/mUvf2NbfuYU) , który pokazuje, jak dostosować dane wyjściowe mowy przy tworzeniu zawartości audio. 
5. Zapisz i [wyeksportuj dostosowany dźwięk](#export-tuned-audio). Po zapisaniu ścieżki dostrajania w systemie można kontynuować pracę i iterację danych wyjściowych. Gdy dane wyjściowe są zadowalające, można utworzyć zadanie tworzenia dźwięku za pomocą funkcji eksportowania. Można obserwować stan zadania eksportu i pobrać dane wyjściowe do użycia z aplikacjami i produktami.

## <a name="set-up-your-azure-account-and-speech-resource"></a>Skonfiguruj swoje konto platformy Azure i zasób mowy

1. Aby móc korzystać z tworzenia zawartości audio, musisz mieć konto platformy Azure. Konto platformy Azure można utworzyć przy użyciu konta Microsoft. Postępuj zgodnie z tymi instrukcjami, aby [skonfigurować konto platformy Azure](get-started.md#new-resource). 
2. [Utwórz zasób mowy](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#create-the-resource) na koncie platformy Azure. Upewnij się, że warstwa cenowa jest ustawiona na **S0**. Jeśli używasz jednego z głosów neuronowych, upewnij się, że zasób jest tworzony w [obsługiwanym regionie](regions.md#standard-and-neural-voices).
2. Po otrzymaniu konta platformy Azure i zasobu mowy możesz użyć usług mowy i uzyskać dostęp do [tworzenia zawartości audio](https://aka.ms/audiocontentcreation).
3. Wybierz zasób mowy, nad którym chcesz korzystać. W tym miejscu możesz również utworzyć nowy zasób mowy. 
4. Możesz w dowolnym momencie zmodyfikować zasób mowy przy użyciu opcji **Ustawienia** , która znajduje się w górnym obszarze nawigacji.

## <a name="create-an-audio-tuning-file"></a>Utwórz plik strojenia audio

Istnieją dwa sposoby na uzyskanie zawartości do narzędzia tworzenia zawartości audio.

**Opcja 1:**

1. Kliknij pozycję **nowy plik** , aby utworzyć nowy plik strojenia audio.
2. Wpisz lub wklej zawartość do okna Edycja. Znaki dla każdego pliku są do 20 000. Jeśli Twój skrypt zawiera więcej niż 20 000 znaków, możesz użyć opcji 2, aby automatycznie podzielić zawartość na wiele plików. 
3. Nie zapomnij zapisać.

**Opcja 2:**

1. Kliknij przycisk **Przekaż** , aby zaimportować jeden lub więcej plików tekstowych. Obsługiwane są zarówno zwykły tekst, jak i SSML.
2. Jeśli plik skryptu ma więcej niż 20 000 znaków, Podziel plik przez akapity, znak lub wyrażenia regularne. 
3. Podczas przekazywania plików tekstowych upewnij się, że plik spełnia te wymagania.

   | Właściwość | Wartość/uwagi |
   |----------|---------------|
   | Format pliku | Zwykły tekst (. txt)<br/> Tekst SSML (. txt)<br/> Pliki zip nie są obsługiwane |
   | Format kodowania | UTF-8 |
   | Nazwa pliku | Każdy plik musi mieć unikatową nazwę. Duplikaty nie są obsługiwane. |
   | Długość tekstu | Pliki tekstowe nie mogą zawierać więcej niż 20 000 znaków. |
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

1. Kliknij przycisk **Eksportuj** , aby utworzyć zadanie tworzenia dźwięku. **Eksportowanie do biblioteki audio** jest zalecane, ponieważ obsługuje długie dane wyjściowe audio i pełne środowisko wyjścia audio. Możesz również bezpośrednio pobrać dźwięk na dysk lokalny, ale dostępne są tylko pierwsze 10 minut. 
2. Wybierz format danych wyjściowych dla dostrajania audio. Poniżej znajduje się lista obsługiwanych formatów i częstotliwości próbkowania.
3. Stan zadania można wyświetlić na karcie **Eksportuj zadanie** . Jeśli zadanie nie powiedzie się, zobacz stronę ze szczegółowymi informacjami, aby uzyskać pełny raport.
4. Po zakończeniu zadania dźwięk jest dostępny do pobrania na karcie **biblioteka audio** .
5. Kliknij pozycję **Pobierz**. Teraz możesz przystąpić do używania niestandardowego, dostrojonego dźwięku w aplikacjach lub produktach.

### <a name="supported-audio-formats"></a>Obsługiwane formaty audio

| Format | szybkość próbkowania 16 kHz | częstotliwość próbkowania 24 kHz |
|--------|--------------------|--------------------|
| pliki | RIFF-16khz-16bit-mono-PCM | RIFF-24khz-16bit-mono-PCM |
| formatu | audio-16khz-128kbitrate-mono-MP3 | audio-24khz-160kbitrate-mono-MP3 |

## <a name="see-also"></a>Zobacz też

* [Długi interfejs API audio](https://aka.ms/long-audio-api)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Speech Studio](https://speech.microsoft.com)
