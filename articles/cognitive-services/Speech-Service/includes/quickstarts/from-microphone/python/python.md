---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: f26f0ab6da398dcdee307f89b27cca780d08af85
ms.sourcegitcommit: 374d1533ea2f2d9d3f8b6e6a8e65c6a5cd4aea47
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/01/2020
ms.locfileid: "85838959"
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

Teraz wszystko jest gotowe do testowania rozpoznawania mowy przy użyciu usługi Speech. 

Jeśli uruchamiasz ten program na macOS i jest to pierwsza utworzona aplikacja w języku Python, która korzysta z mikrofonu, prawdopodobnie trzeba będzie zapewnić dostęp terminalu do mikrofonu. Otwórz okno **Ustawienia systemu** i wybierz pozycję **zabezpieczenia & prywatność**. Następnie wybierz pozycję **prywatność** i Znajdź **mikrofon** na liście. Na koniec wybierz pozycję **Terminal** i Zapisz. 

1. **Uruchom aplikację** — w wierszu polecenia wpisz:
    ```bash
    python quickstart.py
    ```
2. **Rozpocznij rozpoznawanie** — spowoduje to wyświetlenie monitu o rozmowę w języku angielskim. Twoja mowa jest wysyłana do usługi mowy, uzyskanego jako tekst i renderowany w konsoli programu.

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]
