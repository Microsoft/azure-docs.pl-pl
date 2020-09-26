---
title: 'Szybki Start: Rozpoznawanie mowy z mikrofonu, C# (platformy UWP) — usługa mowy'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/02/2020
ms.author: erhopf
ms.custom: devx-track-csharp
ms.openlocfilehash: a28a35bb57c759c58105c12673cb233d5b6f2e75
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91377272"
---
## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem:

> [!div class="checklist"]
> * [Tworzenie zasobu usługi Azure Speech](../../../../overview.md#try-the-speech-service-for-free)
> * [Skonfiguruj środowisko deweloperskie i Utwórz pusty projekt](../../../../quickstarts/setup-platform.md?tabs=uwp&pivots=programming-language-csharp)
> * Upewnij się, że masz dostęp do mikrofonu do przechwytywania audio

Jeśli to już zrobione, świetnie. Kontynuujmy.

## <a name="open-your-project-in-visual-studio"></a>Otwieranie projektu w programie Visual Studio

Pierwszym krokiem jest upewnienie się, że projekt jest otwarty w programie Visual Studio.

## <a name="start-with-some-boilerplate-code"></a>Zacznij od pewnego kodu standardowego

Dodajmy kod, który działa jako szkielet dla projektu.

1. W **Eksplorator rozwiązań**Otwórz program `MainPage.xaml` .

2. W widoku XAML projektanta Wstaw następujący fragment kodu XAML do tagu **Grid** (między `<Grid>` i `</Grid>` ):

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml#StackPanel)]

3. W **Eksplorator rozwiązań**Otwórz plik źródłowy związany z kodem `MainPage.xaml.cs` . (Jest ono pogrupowane w `MainPage.xaml` .)

4. Zastąp kod następującym kodem podstawowym:

   [!code-csharp[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=6-50,55-56,94-154)]

## <a name="create-a-speech-configuration"></a>Tworzenie konfiguracji mowy

Aby można było zainicjować `SpeechRecognizer` obiekt, należy utworzyć konfigurację korzystającą z klucza subskrypcji i regionu subskrypcji. Wstaw ten kod w `SpeechRecognitionFromMicrophone_ButtonClicked()` metodzie.

> [!NOTE]
> Ten przykład używa `FromSubscription()` metody do skompilowania `SpeechConfig` . Aby uzyskać pełną listę dostępnych metod, zobacz [SpeechConfig Class](https://docs.microsoft.com/dotnet/api/)[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=51-53)]

## <a name="initialize-a-speechrecognizer"></a>Inicjowanie elementu SpeechRecognizer

Teraz Utwórzmy `SpeechRecognizer` . Ten obiekt jest tworzony wewnątrz instrukcji using, aby zapewnić odpowiednią wersję niezarządzanych zasobów. Wstaw ten kod w `SpeechRecognitionFromMicrophone_ButtonClicked()` metodzie bezpośrednio poniżej konfiguracji mowy.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=58,59,93)]

## <a name="recognize-a-phrase"></a>Rozpoznawanie frazy

Z `SpeechRecognizer` obiektu wywoływana jest `RecognizeOnceAsync()` Metoda. Ta metoda pozwala usłudze rozpoznawania mowy wysyłać pojedyncze frazy do rozpoznawania, a po zidentyfikowaniu frazy, aby zatrzymać rozpoznawanie mowy.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=66)]

## <a name="display-the-recognition-results-or-errors"></a>Wyświetlanie wyników rozpoznawania (lub błędów)

Gdy usługa mowy zwróci wynik rozpoznawania, należy wykonać coś z nim. Zajmiemy się tym, że będzie on prosty i będzie drukował wynik do panelu stanu.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=68-93)]

## <a name="build-and-run-the-application"></a>Kompilowanie i uruchamianie aplikacji

Teraz możesz przystąpić do kompilowania i testowania aplikacji.

1. Na pasku menu wybierz polecenie **Kompiluj**  >  **kompilację rozwiązania** , aby skompilować aplikację. Kod powinien teraz zostać skompilowany bez błędów.

1. Wybierz **Debuguj**  >  **Rozpocznij debugowanie** (lub naciśnij klawisz **F5**), aby uruchomić aplikację. Zostanie wyświetlone okno **HelloWorld** .

   ![Przykładowa aplikacja rozpoznawania mowy platformy UWP w języku C# — Szybki Start](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-helloworld-window.png)

1. Wybierz pozycję **Włącz mikrofon**i po włączeniu żądania uprawnienia dostępu wybierz pozycję **tak**.

   ![Żądanie uprawnienia dostępu do mikrofonu](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. Wybierz opcję **Speech recognition with microphone input** (Rozpoznawanie mowy z użyciem danych wejściowych z mikrofonu) i wypowiedz zwrot lub zdanie w języku angielskim do mikrofonu urządzenia. Mowa zostanie przesłana do usługi rozpoznawania mowy i transkrybowana na tekst, który zostanie wyświetlony w tym oknie.

   ![Interfejs użytkownika rozpoznawania mowy](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-11-ui-result.png)

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]

