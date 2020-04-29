---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: 3613d190ef079d0e477d42b426a224d8e4dda7e6
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81400658"
---
## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem:

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Tworzenie zasobu usługi Azure Speech<span class="docon docon-navigate-external x-hidden-focus"></span></a>
> * [Skonfiguruj środowisko deweloperskie i Utwórz pusty projekt](../../../../quickstarts/setup-platform.md?pivots=programming-language-python)
> * Upewnij się, że masz dostęp do mikrofonu do przechwytywania audio

## <a name="source-code"></a>Kod źródłowy

Utwórz plik o nazwie *quickstart.py* i wklej do niego następujący kod języka Python.

[!code-python[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/python/from-microphone/quickstart.py#code)]

[!INCLUDE [replace key and region](../replace-key-and-region.md)]

## <a name="code-explanation"></a>Wyjaśnienie kodu

[!INCLUDE [code explanation](../code-explanation.md)]

## <a name="build-and-run-app"></a>Kompilowanie i uruchamianie aplikacji

Teraz wszystko jest gotowe do przetestowania aplikacji i sprawdzenia funkcji rozpoznawania mowy przy użyciu usługi Speech.

1. **Uruchom aplikację** — w wierszu polecenia wpisz:
    ```bash
    python quickstart.py
    ```
2. **Rozpocznij rozpoznawanie** — spowoduje to wyświetlenie monitu o rozmowę w języku angielskim. Twoja mowa jest wysyłana do usługi mowy, uzyskanego jako tekst i renderowany w konsoli programu.

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]

