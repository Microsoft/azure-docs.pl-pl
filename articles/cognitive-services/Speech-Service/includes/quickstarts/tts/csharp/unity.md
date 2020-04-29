---
title: 'Szybki Start: synteza mowy, C# (Unity) — usługa mowy'
titleSuffix: Azure Cognitive Services
description: Skorzystaj z tego przewodnika, aby utworzyć aplikację zamiany tekstu na mowę przy użyciu aparatu Unity i zestawu Speech SDK dla aparatu Unity. Po zakończeniu można wypróbować mowę z tekstu w czasie rzeczywistym do głośnika urządzenia.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/04/2020
ms.author: yinhew
ms.openlocfilehash: 0934738c557ac6d26867546783797cf5d2b75056
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81275238"
---
> [!NOTE]
> Środowisko Unity obsługuje Pulpity systemu Windows (x86 i x64) lub platforma uniwersalna systemu Windows (x86, x64, ARM/ARM64), Android (x86, ARM32/64) i iOS (x64 symulator, ARM32 i ARM64).

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że:

> [!div class="checklist"]
> * [Tworzenie zasobu usługi Azure Speech](../../../../get-started.md)
> * [Skonfiguruj środowisko deweloperskie i Utwórz pusty projekt](../../../../quickstarts/setup-platform.md?tabs=unity&pivots=programming-language-csharp)

## <a name="add-a-ui"></a>Dodawanie interfejsu użytkownika

Dodamy minimalny interfejs użytkownika do naszej sceny, która składa się z pola wejściowego, aby wprowadzić tekst do syntezy, przycisk służący do wyzwalania syntezy mowy i pola tekstowego, aby wyświetlić wynik.

* W [oknie hierarchia](https://docs.unity3d.com/Manual/Hierarchy.html) (domyślnie po lewej stronie) jest wyświetlana Przykładowa scena utworzona przy użyciu nowego projektu.
* Wybierz przycisk **Utwórz** w górnej części okna **Hierarchia** , a następnie wybierz**pole wejściowy** **interfejs użytkownika** > .
* Ta opcja tworzy trzy obiekty gry, które można wyświetlić w oknie **hierarchii** : obiekt **pola wejściowego** zagnieżdżony w obrębie obiektu **kanwy** i obiektu **EventSystem** .
* [Przejdź do widoku sceny](https://docs.unity3d.com/Manual/SceneViewNavigation.html) , aby mieć dobry widok kanwy i pole wejściowe w [widoku sceny](https://docs.unity3d.com/Manual/UsingTheSceneView.html).
* Wybierz obiekt **pola wejściowego** w oknie **Hierarchia** , aby wyświetlić jego ustawienia w [oknie Inspektora](https://docs.unity3d.com/Manual/UsingTheInspector.html) (domyślnie po prawej).
* Ustaw właściwości **pos X** i **pos** na **wartość 0** , aby pole wejściowe było wyśrodkowane w środku kanwy.
* Ponownie wybierz przycisk **Utwórz** w górnej części okna **Hierarchia** . Wybierz**przycisk** **interfejsu użytkownika** > , aby utworzyć przycisk.
* Wybierz obiekt **Button** w oknie **Hierarchia** , aby wyświetlić jego ustawienia w [oknie Inspektora](https://docs.unity3d.com/Manual/UsingTheInspector.html) (domyślnie po prawej stronie).
* Ustaw właściwości **pos X** i **pos** na **0** i **-48**. Ustaw właściwości **Width** i **Height** na **160** i **30** , aby upewnić się, że przycisk i pole wejściowe nie nakładają się na siebie.
* Ponownie wybierz przycisk **Utwórz** w górnej części okna **Hierarchia** . Wybierz opcję**tekst** **interfejsu użytkownika** > , aby utworzyć pole tekstowe.
* Wybierz obiekt **tekstowy** w oknie **Hierarchia** , aby wyświetlić jego ustawienia w [oknie Inspektora](https://docs.unity3d.com/Manual/UsingTheInspector.html) (domyślnie po prawej).
* Ustaw właściwości **pos X** i **pos** na **0** i **80**. Ustaw właściwości **Width** i **Height** na **320** i **80** , aby upewnić się, że pole tekstowe i pole wejściowe nie nakładają się na siebie.
* Ponownie wybierz przycisk **Utwórz** w górnej części okna **Hierarchia** . Wybierz pozycję **audio** > audio**Źródło** , aby utworzyć źródło audio.

Po zakończeniu interfejs użytkownika powinien wyglądać podobnie do tego zrzutu ekranu:

[![Zrzut ekranu przedstawiający interfejs użytkownika szybkiego startu w edytorze Unity](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-unity-ui-inline.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-unity-ui-expanded.png#lightbox)

## <a name="add-the-sample-code"></a>Dodawanie przykładowego kodu

1. W [oknie projektu](https://docs.unity3d.com/Manual/ProjectView.html) (domyślnie w lewym dolnym rogu) wybierz przycisk **Utwórz** , a następnie wybierz pozycję **skrypt C#**. Nadaj skryptowi nazwę `HelloWorld`.

1. Otwórz skrypt do edycji, klikając go dwukrotnie.

   > [!NOTE]
   > Można skonfigurować Edytor kodu, który jest uruchamiany, wybierając pozycję **Edytuj** > **Preferencje**. Aby uzyskać więcej informacji, zobacz [Podręcznik użytkownika środowiska Unity](https://docs.unity3d.com/Manual/Preferences.html).

1. Zastąp cały kod następującym:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/unity/text-to-speech/Assets/Scripts/HelloWorld.cs#code)]

1. Znajdź i Zamień ciąg `YourSubscriptionKey` na klucz subskrypcji usługi rozpoznawania mowy.

1. Znajdź i zastąp ciąg `YourServiceRegion`[regionem](~/articles/cognitive-services/Speech-Service/regions.md) skojarzonym z Twoją subskrypcją. Na przykład region jest `westus` używany w przypadku korzystania z bezpłatnej wersji próbnej.

1. Zapisz zmiany skryptu.

1. Z powrotem w edytorze aparatu Unity Dodaj skrypt jako składnik do jednego z obiektów gry.

   * Wybierz obiekt **kanwy** w oknie **Hierarchia** , aby otworzyć ustawienie w [oknie Inspektora](https://docs.unity3d.com/Manual/UsingTheInspector.html) (domyślnie po prawej stronie).
   * W oknie **inspektora** wybierz przycisk **Dodaj składnik** . Następnie wyszukaj utworzony wcześniej `HelloWorld` skrypt i dodaj go.
   * Składnik HelloWorld ma cztery niezainicjowane właściwości, **tekst danych wyjściowych**, **pole wejściowe**, **przycisk mów** i **Źródło Audio**, które pasują do właściwości `HelloWorld` publicznych klasy.
     Aby je połączyć, wybierz Selektor obiektów (ikona małego okręgu z prawej strony właściwości). Wybierz utworzone wcześniej obiekty text i Button.

     > [!NOTE]
     > Pole wejściowe i przycisk mają również zagnieżdżony obiekt tekstowy. Upewnij się, że nie przypadkowo wybierzesz go do wyjścia tekstu. Lub można zmienić nazwy obiektów tekstowych, które używają pola **Nazwa** w oknie **inspektora** , aby uniknąć tego nieporozumień.

## <a name="run-the-application-in-the-unity-editor"></a>Uruchamianie aplikacji w edytorze środowiska Unity

* Wybierz przycisk **odtwarzania** na pasku narzędzi edytora aparatu Unity poniżej paska menu.
* Po uruchomieniu aplikacji wprowadź tekst w polu wejściowym, a następnie wybierz przycisk. Twój tekst jest przesyłany do usługi mowy i można go wyrównać do mowy, który jest odtwarzany na głośniku.

  [![Zrzut ekranu przedstawiający uruchomiony przewodnik szybkiego startu w oknie gry Unity](~/articles/cognitive-services/speech-service/media/sdk/qs-tts-csharp-unity-output-inline.png)](~/articles/cognitive-services/speech-service/media/sdk/qs-tts-csharp-unity-output-expanded.png#lightbox)

* Sprawdź [okno konsoli](https://docs.unity3d.com/Manual/Console.html) pod kątem komunikatów debugowania.

## <a name="additional-options-to-run-this-application"></a>Dodatkowe opcje uruchamiania aplikacji

Tę aplikację można również wdrożyć w systemie Android jako autonomiczną aplikację systemu Windows lub aplikację platformy UWP.
Zapoznaj się z [przykładowym repozytorium](https://aka.ms/csspeech/samples) w folderze szybkiego startu/CSharp-Unity opisującym konfigurację tych dodatkowych obiektów docelowych.

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [Speech synthesis basics](../../text-to-speech-next-steps.md)]

## <a name="see-also"></a>Zobacz także

- [Tworzenie niestandardowego głosu](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Rejestruj niestandardowe przykłady głosu](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
