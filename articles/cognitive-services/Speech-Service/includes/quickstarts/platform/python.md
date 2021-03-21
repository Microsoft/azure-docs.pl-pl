---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: trbye
ms.openlocfilehash: cb2aac1b068c92752a7924ada7b0a2eadbc91eef
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104612337"
---
W tym przewodniku przedstawiono sposób instalowania [zestawu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) dla języka Python. Jeśli chcesz, aby nazwa pakietu była już uruchomiona, uruchom polecenie `pip install azure-cognitiveservices-speech` .

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne

- Pakiet zestawu Speech SDK dla języka Python jest dostępny dla tych systemów operacyjnych:
  - Windows: x64 i x86
  - Mac: macOS X w wersji 10,12 lub nowszej
  - System Zapoznaj się z listą [obsługiwanych dystrybucji systemu Linux i architektur docelowych](~/articles/cognitive-services/speech-service/speech-sdk.md).

## <a name="prerequisites"></a>Wymagania wstępne

- W systemie Windows wymagany jest [Microsoft Visual C++ redystrybucyjny dla programu Visual Studio 2019](https://support.microsoft.com/en-us/topic/the-latest-supported-visual-c-downloads-2647da03-1eea-4433-9aff-95f26a218cc0) dla danej platformy. Zainstalowanie tego po raz pierwszy może wymagać ponownego uruchomienia.

- Na obsługiwanych platformach systemu Linux wymagane są pewne biblioteki ( `libssl` na potrzeby obsługi protokołu Secure Sockets Layer i `libasound2` obsługi dźwięku). Zapoznaj się z dystrybucją poniżej, aby zapoznać się z poleceniami wymaganymi do zainstalowania odpowiednich wersji tych bibliotek.

  - W systemie Ubuntu/Debian Uruchom następujące polecenia, aby zainstalować wymagane pakiety:

    ```sh
    sudo apt-get update
    sudo apt-get install build-essential libssl1.0.0 libasound2
    ```

    Jeśli libssl 1.0.0 jest niedostępny, zainstaluj libssl 1.0. x (gdzie x jest większe niż 0) lub zamiast tego libssl 1.1.

  - W systemie RHEL/CentOS Uruchom następujące polecenia, aby zainstalować wymagane pakiety:

    ```sh
    sudo yum update
    sudo yum install alsa-lib openssl python3
    ```

> [!NOTE]
> - W systemie RHEL/CentOS 7 postępuj zgodnie z instrukcjami dotyczącymi [KONFIGUROWANIA RHEL/CentOS 7 dla zestawu Speech SDK](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md).
> - W systemie RHEL/CentOS 8 postępuj zgodnie z instrukcjami dotyczącymi [sposobu konfigurowania OpenSSL dla systemu Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

- W systemie Windows wymagany jest [Microsoft Visual C++ redystrybucyjny dla programu Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) dla danej platformy. Należy pamiętać, że zainstalowanie tego programu po raz pierwszy może wymagać ponownego uruchomienia systemu Windows przed kontynuowaniem pracy z tym przewodnikiem.
- Na koniec należy potrzebować języka [Python 3,6 do 3,9](https://www.python.org/downloads/). Aby sprawdzić instalację, Otwórz wiersz polecenia i wpisz polecenie `python --version` i sprawdź wynik. Jeśli jest zainstalowana prawidłowo, otrzymasz odpowiedź "Python 3.6.1" lub podobną.

## <a name="install-the-speech-sdk-from-pypi"></a>Instalowanie zestawu Speech SDK z PyPI

Jeśli używasz własnego środowiska lub narzędzi do kompilacji, uruchom następujące polecenie, aby zainstalować zestaw Speech SDK z [PyPI](https://pypi.org/). W przypadku użytkowników z Visual Studio Code przejdź do następnej podsekcji instalacji z przewodnikiem.

```sh
pip install azure-cognitiveservices-speech
```

W przypadku korzystania z programu macOS może być konieczne uruchomienie następującego polecenia w celu uzyskania `pip` powyższego polecenia:

```sh
python3 -m pip install --upgrade pip
```

Po pomyślnym `pip` zainstalowaniu programu `azure-cognitiveservices-speech` można użyć zestawu Speech SDK przez zaimportowanie przestrzeni nazw do projektów języka Python.

```py
import azure.cognitiveservices.speech as speechsdk
```

## <a name="install-the-speech-sdk-using-visual-studio-code"></a>Instalowanie zestawu Speech SDK przy użyciu Visual Studio Code

1. Pobierz i zainstaluj najnowszą obsługiwaną wersję języka [Python](https://www.python.org/downloads/) dla danej platformy, 3,6 do 3,9.
   - Użytkownicy systemu Windows pamiętaj o wybraniu opcji "Dodaj język Python do ścieżki" podczas procesu instalacji.
1. Pobierz i zainstaluj program [Visual Studio Code](https://code.visualstudio.com/Download).
1. Otwórz program Visual Studio Code i zainstaluj rozszerzenie języka Python. Wybierz opcję  >    >  **rozszerzenia** preferencji plików z menu. Wyszukaj środowisko **Python** i kliknij przycisk **Instaluj**.

   ![Instalowanie rozszerzenia języka Python](~/articles/cognitive-services/speech-service/media/sdk/qs-python-vscode-python-extension.png)

1. Ponadto z poziomu Visual Studio Code Zainstaluj pakiet języka Python zestawu mowy SDK z poziomu wiersza polecenia zintegrowanego:
   1. Otwórz Terminal (z menu rozwijanego **,**  >  **Nowy terminal** terminalu)
   1. W otwartym terminalu wprowadź polecenie `python -m pip install azure-cognitiveservices-speech`

Jeśli jesteś nowym do Visual Studio Code, zapoznaj się z bardziej obszerną [dokumentacją Visual Studio Code](https://code.visualstudio.com/docs). Aby uzyskać więcej informacji na temat Visual Studio Code i języka Python, zobacz [Przewodnik po Visual Studio Code Python](https://code.visualstudio.com/docs/python/python-tutorial).

## <a name="support-and-updates"></a>Pomoc techniczna i aktualizacje

Aktualizacje pakietu zestawu Speech SDK dla języka Python będą rozpowszechniane za pośrednictwem witryny PyPI i ogłaszane w [informacjach o wersji](~/articles/cognitive-services/speech-service/releasenotes.md).
Gdy będzie dostępna nowa wersja, możesz przeprowadzić aktualizację do niej, używając polecenia `pip install --upgrade azure-cognitiveservices-speech`.
Ustal, która wersja jest obecnie zainstalowana, sprawdzając zmienną `azure.cognitiveservices.speech.__version__`.

W przypadku problemu lub braku funkcji zobacz [opcje pomocy i obsługi technicznej](../../../../cognitive-services-support-options.md?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext%253fcontext%253d%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext).

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [windows](../quickstart-list.md)]