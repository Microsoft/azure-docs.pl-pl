---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/04/2020
ms.author: trbye
ms.openlocfilehash: 885dc178c6c0bfed5c49b8d9e88792a1072b0f10
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81400506"
---
## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że:

> [!div class="checklist"]
> * [Tworzenie zasobu usługi Azure Speech](../../../../get-started.md)
> * [Skonfiguruj środowisko deweloperskie i Utwórz pusty projekt](../../../../quickstarts/setup-platform.md?tabs=windows&pivots=programming-language-cpp)

[!INCLUDE [Audio input format](~/articles/cognitive-services/speech-service/includes/audio-input-format-chart.md)]

## <a name="add-sample-code"></a>Dodawanie przykładowego kodu

1. Otwórz plik źródłowy **helloworld.cpp**.

1. Zastąp cały kod następującym fragmentem kodu:
   
   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-file/helloworld/helloworld.cpp#code)]

1. W tym samym pliku zastąp ciąg `YourSubscriptionKey` kluczem subskrypcji.

1. Zastąp ciąg `YourServiceRegion` **identyfikatorem regionu** z [regionu](https://aka.ms/speech/sdkregion) skojarzonego z subskrypcją (na przykład w `westus` przypadku subskrypcji bezpłatnej wersji próbnej).

1. Zamień ciąg `whatstheweatherlike.wav` na własną nazwę pliku.

1. Na pasku menu wybierz kolejno opcje **plik** > **Zapisz wszystko**.

> [!NOTE]
> Zestaw Speech SDK będzie domyślnie rozpoznawał użycie języka en-us w celu uzyskania informacji na temat wybierania [języka źródłowego.](../../../../how-to-specify-source-language.md)

## <a name="build-and-run-the-application"></a>Kompilowanie i uruchamianie aplikacji

1. Na pasku menu wybierz kolejno opcje **Kompiluj** > kompilacje**rozwiązanie** , aby skompilować aplikację. Kod powinien teraz zostać skompilowany bez błędów.

1. Wybierz **Debuguj** > **Rozpocznij debugowanie** (lub naciśnij klawisz **F5**), aby uruchomić aplikację **HelloWorld** .

1. Plik audio jest przesyłany do usługi mowy, a pierwszy wypowiedź w pliku jest uzyskanego do tekstu, który pojawia się w tym samym oknie.

   ```text
   Recognizing first result...
   We recognized: What's the weather like?
   ```

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]