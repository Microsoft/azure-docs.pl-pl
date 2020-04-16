---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/02/2020
ms.author: trbye
ms.openlocfilehash: c2b844b3b1aa7a848e2668b3dca5a1cac5b5baaf
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400725"
---
## <a name="prerequisites"></a>Wymagania wstępne

Zanim zaczniesz:

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Tworzenie zasobu mowy platformy Azure<span class="docon docon-navigate-external x-hidden-focus"></span></a>
> * [Konfigurowanie środowiska programistycznego i tworzenie pustego projektu](../../../../quickstarts/setup-platform.md?tabs=jre&pivots=programming-language-java)
> * Upewnij się, że masz dostęp do mikrofonu do przechwytywania dźwięku

## <a name="source-code"></a>Kod źródłowy

Aby dodać nową pustą klasę do projektu Java, wybierz **pozycję Plik** > **nowej** > **klasy**. W oknie **Nowa klasa Java** wprowadź ciąg **speechsdk.quickstart** w polu **Pakiet** i **Main** w polu **Nazwa**.

![Zrzut ekranu okna Nowa klasa Java](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-06-create-main-java.png)

Zastąp zawartość pliku *Main.java* następującym fragmentem kodu:

[!code-java[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-microphone/src/speechsdk/quickstart/Main.java#code)]

[!INCLUDE [replace key and region](../replace-key-and-region.md)]

## <a name="code-explanation"></a>Wyjaśnienie kodu

[!INCLUDE [code explanation](../code-explanation.md)]

## <a name="build-and-run-app"></a>Tworzenie i uruchamianie aplikacji

Naciśnij <kbd>klawisz F11</kbd>lub wybierz pozycję **Uruchom** > **debugowanie**.
Następne 15 sekund mowy z mikrofonu zostanie rozpoznane i zarejestrowane w oknie konsoli.

![Zrzut ekranu przedstawiający dane wyjściowe konsoli po pomyślnym ukończeniu rozpoznawania](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-07-console-output.png)

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]

