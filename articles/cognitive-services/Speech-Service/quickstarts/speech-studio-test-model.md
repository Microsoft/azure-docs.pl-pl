---
title: Testowanie modelu przy użyciu plików audio — Speech Studio
titleSuffix: Azure Cognitive Services
description: W tym instruktażu program Speech Studio służy do testowania rozpoznawania mowy w pliku audio.
services: cognitive-services
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 09/04/2020
ms.author: v-demjoh
ms.openlocfilehash: 9101944a567df5000d3584ed48eff24e4c5e0057
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "89565807"
---
# <a name="test-a-model-using-an-audio-file-in-speech-studio"></a>Testowanie modelu przy użyciu pliku dźwiękowego w programie Speech Studio

W tym instruktażu używasz programu Speech Studio do konwertowania mowy z pliku audio na tekst. Program Speech Studio umożliwia testowanie, porównywanie, ulepszanie i wdrażanie modeli rozpoznawania mowy przy użyciu pokrewnego tekstu, dźwięku z transkrypcjami z etykietami ludzkimi i podano wskazówki dotyczące wymowy.

## <a name="prerequisites"></a>Wymagania wstępne

Przed użyciem portalu mowy [postępuj zgodnie z tymi instrukcjami, aby utworzyć konto platformy Azure i subskrybować usługę mowy](../how-to-custom-speech.md#set-up-your-azure-account). Ta ujednolicona subskrypcja zapewnia dostęp do funkcji zamiany mowy na tekst, zamiany tekstu na mowę, tłumaczenia mowy i portalu Custom Speech.

## <a name="download-an-audio-file"></a>Pobierz plik audio

Wykonaj następujące kroki, aby pobrać plik audio zawierający mowę i spakować go do pliku zip.

1. Pobierz **[przykładowy plik WAV z tego linku](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-speech-sdk/f9807b1079f3a85f07cbb6d762c6b5449d536027/samples/cpp/windows/console/samples/whatstheweatherlike.wav)** , klikając prawym przyciskiem myszy link i wybierając pozycję **Zapisz łącze jako**. Kliknij przycisk **Zapisz** , aby pobrać `whatstheweatherlike.wav` plik.
2. Przy użyciu Eksploratora plików lub okna terminalu za pomocą narzędzia zip Utwórz plik zip o nazwie `whatstheweatherlike.zip` , który zawiera `whatstheweatherlike.wav` pobrany plik. W systemie Windows możesz otworzyć Eksploratora Windows, przejdź do `Downloads` folderu, kliknij prawym przyciskiem myszy `whatstheweatherliike.wav` , kliknij pozycję **Wyślij do**, kliknij **folder skompresowany (spakowany)** i naciśnij klawisz ENTER, aby zaakceptować domyślną nazwę pliku.

## <a name="create-a-project-in-the-custom-speech-portal"></a>Tworzenie projektu w portalu Custom Speech

Wykonaj następujące kroki, aby utworzyć projekt, który zawiera plik zip o jednym pliku audio.

1. Otwórz program [Speech Studio](https://speech.microsoft.com/), a następnie kliknij pozycję **Nowy projekt**. Wpisz nazwę dla tego projektu, a następnie kliknij przycisk **Utwórz**. Twój projekt zostanie wyświetlony na liście Custom Speech.
2. Kliknij nazwę projektu. Na karcie Dane kliknij przycisk **Przekaż dane**.
3. Typ danych mowy jest wartością domyślną **tylko audio**, więc kliknij przycisk **dalej**.
4. Nazwij nowy zestaw danych mowy `MyZipOfAudio` , a następnie kliknij przycisk **dalej**.
5. Kliknij przycisk **Przeglądaj pliki...**, przejdź do `whatstheweatherlike.zip` pliku, a następnie kliknij przycisk **Otwórz**.
6. Kliknij przycisk **Przekaż**. Przeglądarka przekazuje plik zip do programu Speech Studio, a program Speech Studio przetwarza zawartość.

## <a name="test-a-model"></a>Testowanie modelu

Gdy program Speech Studio przetworzy zawartość pliku zip, można odtworzyć źródłowy dźwięk podczas badania transkrypcji w celu wyszukania błędów lub pominięć. Wykonaj następujące kroki, aby ocenić jakość transkrypcji w przeglądarce.

1. Kliknij kartę **testowanie** , a następnie kliknij przycisk **Dodaj test**.
2. W tym teście Sprawdzamy jakość danych tylko audio, dlatego kliknij przycisk **dalej** , aby zaakceptować ten typ testu.
3. Nazwij ten test `MyModelTest` , a następnie kliknij przycisk **dalej**.
4. Kliknij przycisk radiowy po lewej stronie `MyZipOfAudio` , a następnie kliknij przycisk **dalej**.
5. Lista rozwijana **model 1** domyślnie jest ostatnim modelem rozpoznawania, dlatego kliknij przycisk **Utwórz**. Po przetworzeniu zawartości zestawu danych audio stan testu zmieni się na **powodzenie**.
6. Kliknij pozycję **MyModelTest**. Pojawią się wyniki rozpoznawania mowy. Kliknij trójkąt skierowany w prawo w okręgu, aby usłyszeć dźwięk, i Porównaj to, co słyszysz z tekstem przez okrąg.

## <a name="download-detailed-results"></a>Pobierz szczegółowe wyniki

Można pobrać pliki opisujące transkrypcje w bardziej szczegółowy sposób. Pliki obejmują leksykalną postać mowy w plikach audio, a także pliki JSON zawierające przesunięcia, czas trwania i szczegóły zaufania dotyczące każdego wyrazu. Wykonaj następujące kroki, aby wyświetlić te pliki.

1. Kliknij pozycję **Pobierz**.
2. W oknie dialogowym Pobieranie Usuń zaznaczenie opcji **dźwięk**, a następnie kliknij pozycję **Pobierz**.
3. Rozpakuj pobrany plik zip i przejrzyj wyodrębnione pliki.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o zwiększaniu dokładności rozpoznawania mowy przez [uczenie modelu niestandardowego](../how-to-custom-speech-test-and-train.md).