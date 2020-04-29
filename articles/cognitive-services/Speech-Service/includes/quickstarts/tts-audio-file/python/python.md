---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/04/2020
ms.author: trbye
ms.openlocfilehash: 10a11168b8046dbcc877f45141571fccdca879f0
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81400331"
---
## <a name="prerequisites"></a>Wymagania wstępne

* Klucz subskrypcji platformy Azure dla usługi Mowa. [Uzyskaj bezpłatnie](~/articles/cognitive-services/Speech-Service/get-started.md).
* Środowisko [Python 3,5 do 3,8](https://www.python.org/downloads/).
* Pakiet zestawu Speech SDK dla języka Python jest dostępny dla tych systemów operacyjnych:
    * Windows: x64 i x86.
    * Mac: macOS X w wersji 10.12 lub nowszej.
    * Linux: Ubuntu 16.04/18.04, Debian 9, RHEL 7/8, CentOS 7/8 w x64.
* W systemie Linux Uruchom następujące polecenia, aby zainstalować wymagane pakiety:

# <a name="ubuntu"></a>[Ubuntu](#tab/ubuntu)

```Bash
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libasound2
```

# <a name="debian-9"></a>[Debian 9](#tab/debian)

```Bash
sudo apt-get update
sudo apt-get install build-essential libssl1.0.2 libasound2
```

# <a name="rhel--centos-8"></a>[RHEL/CentOS 8](#tab/rhel-centos)

```Bash
sudo yum update
sudo yum install alsa-lib openssl python3
```

> [!NOTE]
> W systemie RHEL/CentOS 7/8 postępuj zgodnie z instrukcjami dotyczącymi [sposobu konfigurowania OpenSSL dla systemu Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

---

* W systemie Windows wymagany jest [Microsoft Visual C++ redystrybucyjny dla programu Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) dla danej platformy.

## <a name="install-the-speech-sdk"></a>Instalowanie zestawu SDK usługi Mowa

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

To polecenie instaluje pakiet języka Python z witryny [PyPI](https://pypi.org/) dla zestawu Speech SDK:

```Bash
pip install azure-cognitiveservices-speech
```

## <a name="support-and-updates"></a>Pomoc techniczna i aktualizacje

Aktualizacje pakietu zestawu Speech SDK dla języka Python będą rozpowszechniane za pośrednictwem witryny PyPI i ogłaszane w [informacjach o wersji](~/articles/cognitive-services/Speech-Service/releasenotes.md).
Gdy będzie dostępna nowa wersja, możesz przeprowadzić aktualizację do niej, używając polecenia `pip install --upgrade azure-cognitiveservices-speech`.
Ustal, która wersja jest obecnie zainstalowana, sprawdzając zmienną `azure.cognitiveservices.speech.__version__`.

W przypadku problemu lub braku funkcji zobacz [opcje pomocy i obsługi technicznej](~/articles/cognitive-services/Speech-Service/support.md).

## <a name="create-a-python-application-that-uses-the-speech-sdk"></a>Tworzenie aplikacji języka Python za pomocą zestawu Speech SDK

### <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

Możesz skopiować [kod przykładowy](#sample-code) z tego przewodnika Szybki start do pliku źródłowego `quickstart.py` i uruchomić go w środowisku IDE lub w konsoli:

```Bash
python quickstart.py
```

Ewentualnie możesz pobrać ten samouczek Szybki start jako notes programu [Jupyter](https://jupyter.org) z [repozytorium przykładów zestawu Speech SDK](https://github.com/Azure-Samples/cognitive-services-speech-sdk/) i uruchomić go jako notes.

### <a name="sample-code"></a>Przykładowy kod

````python
import azure.cognitiveservices.speech as speechsdk

# Replace with your own subscription key and region identifier from here: https://aka.ms/speech/sdkregion
speech_key, service_region = "YourSubscriptionKey", "YourServiceRegion"
speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)

# Creates an audio configuration that points to an audio file.
# Replace with your own audio filename.
audio_filename = "helloworld.wav"
audio_output = speechsdk.audio.AudioOutputConfig(filename=audio_filename)

# Creates a synthesizer with the given settings
speech_synthesizer = speechsdk.SpeechSynthesizer(speech_config=speech_config, audio_config=audio_output)

# Synthesizes the text to speech.
# Replace with your own text.
text = "Hello world!"
result = speech_synthesizer.speak_text_async(text).get()

# Checks result.
if result.reason == speechsdk.ResultReason.SynthesizingAudioCompleted:
    print("Speech synthesized to [{}] for text [{}]".format(audio_filename, text))
elif result.reason == speechsdk.ResultReason.Canceled:
    cancellation_details = result.cancellation_details
    print("Speech synthesis canceled: {}".format(cancellation_details.reason))
    if cancellation_details.reason == speechsdk.CancellationReason.Error:
        if cancellation_details.error_details:
            print("Error details: {}".format(cancellation_details.error_details))
    print("Did you update the subscription info?")
````

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
   W przeciwnym razie możesz wyświetlić listę dostępnych interpreterów języka Python. Otwórz paletę poleceń (<kbd>Ctrl + Shift + P</kbd>) i wprowadź **Python: SELECT interpreter**. Wybierz odpowiedni.
1. Możesz zainstalować pakiet języka Python zestawu Speech SDK z poziomu programu Visual Studio Code. Zrób to, jeśli nie został on jeszcze zainstalowany dla wybranego interpretera języka Python.
   Aby zainstalować pakiet zestawu Speech SDK, otwórz terminal. Ponownie wywołaj paletę poleceń (<kbd>Ctrl + Shift + P</kbd>) i wprowadź **Terminal: Utwórz nowy, zintegrowany terminal**.
   W wyświetlonym terminalu wprowadź polecenie `python -m pip install azure-cognitiveservices-speech` lub polecenie odpowiednie dla swojego systemu.
1. Aby uruchomić przykładowy kod, kliknij prawym przyciskiem myszy w dowolnym miejscu w edytorze. Wybierz pozycję **Uruchom plik języka Python w terminalu**.
   Tekst zostanie przekonwertowany na mowę i zapisany w określonych danych audio.

   ```console
   Speech synthesized to [helloworld.wav] for text [Hello world!]
   ```

Jeśli masz problemy z wykonaniem tych instrukcji, zapoznaj się z obszerniejszym [samouczkiem języka Python dla programu Visual Studio Code](https://code.visualstudio.com/docs/python/python-tutorial).

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [Speech synthesis basics](../../text-to-speech-next-steps.md)]

## <a name="see-also"></a>Zobacz także

- [Tworzenie niestandardowego głosu](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Rejestruj niestandardowe przykłady głosu](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
