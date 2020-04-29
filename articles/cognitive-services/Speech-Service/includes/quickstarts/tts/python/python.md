---
title: 'Szybki Start: synteza mowy, środowisko Python-Speech'
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak przeprowadzić funkcję syntezy mowy w języku Python przy użyciu zestawu Speech SDK
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/04/2020
ms.author: trbye
ms.openlocfilehash: e6daa0aaaca283dec11937d961886f95f1972b49
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81400420"
---
## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że:

> [!div class="checklist"]
> * [Tworzenie zasobu usługi Azure Speech](../../../../get-started.md)
> * [Skonfiguruj środowisko deweloperskie i Utwórz pusty projekt](../../../../quickstarts/setup-platform.md?pivots=programming-language-python)

## <a name="support-and-updates"></a>Pomoc techniczna i aktualizacje

Aktualizacje pakietu zestawu Speech SDK dla języka Python będą rozpowszechniane za pośrednictwem witryny PyPI i ogłaszane w [informacjach o wersji](~/articles/cognitive-services/Speech-Service/releasenotes.md).
Gdy będzie dostępna nowa wersja, możesz przeprowadzić aktualizację do niej, używając polecenia `pip install --upgrade azure-cognitiveservices-speech`.
Ustal, która wersja jest obecnie zainstalowana, sprawdzając zmienną `azure.cognitiveservices.speech.__version__`.

W przypadku problemu lub braku funkcji zobacz [opcje pomocy i obsługi technicznej](~/articles/cognitive-services/Speech-Service/support.md).

## <a name="create-a-python-application-that-uses-the-speech-sdk"></a>Tworzenie aplikacji języka Python za pomocą zestawu Speech SDK

### <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

Możesz skopiować [kod przykładowy](#sample-code) z tego przewodnika Szybki start do pliku źródłowego `quickstart.py` i uruchomić go w środowisku IDE lub w konsoli:

```sh
python quickstart.py
```

Ewentualnie możesz pobrać ten samouczek Szybki start jako notes programu [Jupyter](https://jupyter.org) z [repozytorium przykładów zestawu Speech SDK](https://aka.ms/csspeech/samples) i uruchomić go jako notes.

### <a name="sample-code"></a>Przykładowy kod

[!code-python[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/python/text-to-speech/quickstart.py#code)]

### <a name="install-and-use-the-speech-sdk-with-visual-studio-code"></a>Instalowanie i używanie zestawu SDK usługi Mowa za pomocą programu Visual Studio Code

1. Pobierz i zainstaluj 64-bitową wersję języka [Python](https://www.python.org/downloads/), 3,5 do 3,8 na komputerze.
1. Pobierz i zainstaluj program [Visual Studio Code](https://code.visualstudio.com/Download).
1. Otwórz program Visual Studio Code i zainstaluj rozszerzenie języka Python. Wybierz opcję**rozszerzenia** **preferencji** >  **plików** > z menu. Wyszukaj pozycję **Python**.

   ![Instalowanie rozszerzenia języka Python](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-extension.png)

1. Utwórz folder do przechowywania projektu. Na przykład za pomocą Eksploratora Windows.
1. W programie Visual Studio Code wybierz ikonę **Plik**. Następnie otwórz utworzony przez siebie folder.

   ![Otwieranie folderu](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-open-folder.png)

1. Utwórz nowy plik źródłowy języka Python `speechsdk.py`, wybierając ikonę nowego pliku.

   ![Tworzenie pliku](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-newfile.png)

1. Skopiuj, wklej i zapisz [kod języka Python](#sample-code) w nowo utworzonym pliku.
1. Wstaw informacje dotyczące subskrypcji usługi rozpoznawania mowy.
1. Jeśli został wybrany interpreter języka Python, zostanie wyświetlony po lewej stronie paska stanu u dołu okna.
   W przeciwnym razie możesz wyświetlić listę dostępnych interpreterów języka Python. Otwórz paletę poleceń (Ctrl + Shift + P) i wprowadź **Python: SELECT interpreter**. Wybierz odpowiedni.
1. Możesz zainstalować pakiet języka Python zestawu Speech SDK z poziomu programu Visual Studio Code. Zrób to, jeśli nie został on jeszcze zainstalowany dla wybranego interpretera języka Python.
   Aby zainstalować pakiet zestawu Speech SDK, otwórz terminal. Ponownie wywołaj paletę poleceń (Ctrl + Shift + P) i wprowadź **Terminal: Utwórz nowy, zintegrowany terminal**.
   W wyświetlonym terminalu wprowadź polecenie `python -m pip install azure-cognitiveservices-speech` lub polecenie odpowiednie dla swojego systemu.
1. Aby uruchomić przykładowy kod, kliknij prawym przyciskiem myszy w dowolnym miejscu w edytorze. Wybierz pozycję **Uruchom plik języka Python w terminalu**.
   Po wyświetleniu monitu wpisz tekst. Dźwięk z syntezy jest odtwarzany wkrótce.

   ![Uruchamianie przykładu](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-run-tts.png)

Jeśli masz problemy z wykonaniem tych instrukcji, zapoznaj się z obszerniejszym [samouczkiem języka Python dla programu Visual Studio Code](https://code.visualstudio.com/docs/python/python-tutorial).

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [Speech synthesis basics](../../text-to-speech-next-steps.md)]

## <a name="see-also"></a>Zobacz także

- [Tworzenie niestandardowego głosu](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Rejestruj niestandardowe przykłady głosu](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
