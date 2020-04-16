---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/04/2020
ms.author: trbye
ms.openlocfilehash: 924532318fc6638059bbe9eef0065349463523ae
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400872"
---
W tym przewodniku pokazano, jak zainstalować [pakiet SDK mowy](~/articles/cognitive-services/speech-service/speech-sdk.md) dla języka Python. Jeśli chcesz, aby nazwa pakietu zaczęła się `pip install azure-cognitiveservices-speech`samodzielnie, uruchom program .

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne

- Pakiet zestawu Speech SDK dla języka Python jest dostępny dla tych systemów operacyjnych:
  - Windows: x64 i x86
  - Mac: macOS X w wersji 10.12 lub nowszej
  - Linux: Ubuntu 16.04/18.04, Debian 9, RHEL 7/8, CentOS 7/8 na x64

## <a name="prerequisites"></a>Wymagania wstępne

- Obsługiwane platformy Linux będą wymagały`libssl` zainstalowania niektórych bibliotek `libasound2` (do obsługi warstwy bezpiecznych gniazd i do obsługi dźwięku). Zapoznaj się z poniższą dystrybucją, aby uzyskać polecenia potrzebne do zainstalowania poprawnych wersji tych bibliotek.

  - Na Ubuntu uruchom następujące polecenia, aby zainstalować wymagane pakiety:

        ```sh
        sudo apt-get update
        sudo apt-get install build-essential libssl1.0.0 libasound2
        ```

  - W Debianie 9 uruchom następujące polecenia, aby zainstalować wymagane pakiety:

        ```sh
        sudo apt-get update
        sudo apt-get install build-essential libssl1.0.2 libasound2
        ```

  - W programie RHEL/CentOS 8 uruchom następujące polecenia, aby zainstalować wymagane pakiety:

        ```sh
        sudo yum update
        sudo yum install alsa-lib openssl python3
        ```

> [!NOTE]
> Na RHEL/CentOS 8 postępuj zgodnie z instrukcjami [dotyczącymi konfigurowania OpenSSL dla systemu Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

- W systemie Windows potrzebujesz [programu Microsoft Visual C++ redystrybucyjny dla programu Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) dla swojej platformy. Należy pamiętać, że zainstalowanie tego po raz pierwszy może wymagać ponownego uruchomienia systemu Windows przed kontynuowaniem tego przewodnika.
- I wreszcie, trzeba [Python 3.5 do 3.8](https://www.python.org/downloads/). Aby sprawdzić instalację, otwórz wiersz polecenia, wpisz polecenie `python --version` i sprawdź wynik. Jeśli jest poprawnie zainstalowany, otrzymasz odpowiedź "Python 3.5.1" lub podobny.

## <a name="install-the-speech-sdk-from-pypi"></a>Instalowanie pakietu SDK mowy z usługi PyPI

Jeśli używasz własnego środowiska lub narzędzi do tworzenia, uruchom następujące polecenie, aby zainstalować zestaw SDK mowy z [PyPI](https://pypi.org/). Dla użytkowników programu Visual Studio Code przejdź do następnej podsekcji dla instalacji z przewodnikiem.

```sh
pip install azure-cognitiveservices-speech
```

Jeśli korzystasz z systemu macOS, może być konieczne `pip` uruchomienie następującego polecenia, aby polecenie powyżej działało:

```sh
python3 -m pip install --upgrade pip
```

Po pomyślnym zainstalowaniu `pip` `azure-cognitiveservices-speech`można użyć pakietu SDK mowy, importując obszar nazw do projektów języka Python.

```py
import azure.cognitiveservices.speech as speechsdk
```

## <a name="install-the-speech-sdk-using-visual-studio-code"></a>Instalowanie pakietu SDK mowy przy użyciu kodu programu Visual Studio

1. Pobierz i zainstaluj najnowszą obsługiwana wersję [Pythona](https://www.python.org/downloads/) dla swojej platformy, 3.5 do 3.8.
   - Użytkownicy systemu Windows upewnij się, aby wybrać "Dodaj Python do ścieżki" podczas procesu instalacji.
1. Pobierz i zainstaluj program [Visual Studio Code](https://code.visualstudio.com/Download).
1. Otwórz program Visual Studio Code i zainstaluj rozszerzenie języka Python. Z menu **wybierz polecenie** > **Rozszerzenia** **preferencji** > plików. Wyszukaj **Pythona** i kliknij przycisk **Zainstaluj**.

   ![Instalowanie rozszerzenia języka Python](~/articles/cognitive-services/speech-service/media/sdk/qs-python-vscode-python-extension.png)

1. Również z poziomu kodu programu Visual Studio zainstaluj pakiet Języka Python zestawu SDK mowy ze zintegrowanego wiersza polecenia:
   1. Otwórz terminal (z menu rozwijanego **Terminal** > **New Terminal**)
   1. W otwieranym terminalu wprowadź polecenie`python -m pip install azure-cognitiveservices-speech`

Jeśli jesteś nowy w programie Visual Studio Code, zapoznaj się z bardziej rozbudowaną [dokumentacją kodu programu Visual Studio.](https://code.visualstudio.com/docs) Aby uzyskać więcej informacji na temat kodu programu Visual Studio i języka Python, zobacz [Samouczek pythona kodu programu Visual Studio](https://code.visualstudio.com/docs/python/python-tutorial).

## <a name="support-and-updates"></a>Pomoc techniczna i aktualizacje

Aktualizacje pakietu zestawu Speech SDK dla języka Python będą rozpowszechniane za pośrednictwem witryny PyPI i ogłaszane w [informacjach o wersji](~/articles/cognitive-services/speech-service/releasenotes.md).
Gdy będzie dostępna nowa wersja, możesz przeprowadzić aktualizację do niej, używając polecenia `pip install --upgrade azure-cognitiveservices-speech`.
Ustal, która wersja jest obecnie zainstalowana, sprawdzając zmienną `azure.cognitiveservices.speech.__version__`.

W przypadku problemu lub braku funkcji zobacz [opcje pomocy i obsługi technicznej](~/articles/cognitive-services/speech-service/support.md).

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [windows](../quickstart-list.md)]
