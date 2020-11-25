---
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/15/2020
ms.author: v-demjoh
ms.openlocfilehash: 6f80d41001d11c52a00454ea2a593f3f1fce32db
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96026932"
---
## <a name="download-and-install"></a>Pobieranie i instalowanie

#### <a name="windows-install"></a>[Instalacja systemu Windows](#tab/windowsinstall)

Wykonaj następujące kroki, aby zainstalować interfejs wiersza polecenia mowy w systemie Windows:

1. W systemie Windows wymagany jest [Microsoft Visual C++ redystrybucyjny dla programu Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) dla danej platformy. Zainstalowanie tego po raz pierwszy może wymagać ponownego uruchomienia.
2. Pobierz [archiwum zip](https://aka.ms/speech/spx-zips.zip)interfejsu wiersza polecenia mowy, a następnie wyodrębnij je.
3. Przejdź do katalogu, w którym został wyodrębniony `spx-zips` . Ten folder zawiera pliki programu dla interfejsu wiersza polecenia mowy na różnych platformach. 
4. Wyodrębnij pliki dla swojej platformy ( `spx-net471` dla .NET Framework 4,7 lub `spx-netcore-win-x64` dla platformy .net Core 3,0 na procesorze x64). Pamiętaj, że będziesz uruchamiać `spx` z tego katalogu.

### <a name="run-the-speech-cli"></a>Uruchamianie interfejsu wiersza polecenia mowy

1. Otwórz wiersz polecenia lub program PowerShell, a następnie przejdź do katalogu, w którym został wyodrębniony interfejs użytkownika funkcji rozpoznawania mowy.  
2. Wpisz `spx` , aby wyświetlić polecenia pomocy dla interfejsu wiersza polecenia mowy.

> [!NOTE]
> Program PowerShell nie sprawdza katalogu lokalnego podczas wyszukiwania polecenia. W programie PowerShell Zmień katalog na lokalizację `spx` i Wywołaj narzędzie, wprowadzając polecenie `.\spx` .
> Jeśli dodasz ten katalog do ścieżki, program PowerShell i wiersz polecenia systemu Windows znajdą się `spx` z dowolnego katalogu bez `.\` prefiksu.

### <a name="font-limitations"></a>Ograniczenia dotyczące czcionek

W systemie Windows interfejs wiersza polecenia mowy może wyświetlać tylko czcionki dostępne dla wiersza poleceń na komputerze lokalnym.
[Terminal systemu Windows](https://www.microsoft.com/en-us/p/windows-terminal/9n0dx20hk701) obsługuje wszystkie czcionki opracowane interaktywnie przez interfejs wiersza polecenia mowy.

Jeśli dane wyjściowe są wyprowadzane do pliku, Edytor tekstu, taki jak Notatnik lub przeglądarka sieci Web, np. Microsoft Edge, może również wyświetlać wszystkie czcionki.

#### <a name="linux-install"></a>[Instalacja systemu Linux](#tab/linuxinstall)

Wykonaj następujące kroki, aby zainstalować interfejs wiersza polecenia mowy w systemie Linux na PROCESORze x64:

1. Zainstaluj program [.NET Core 3,0](https://dotnet.microsoft.com/download/dotnet-core/3.0).
2. Pobierz [archiwum zip](https://aka.ms/speech/spx-zips.zip)interfejsu wiersza polecenia mowy, a następnie wyodrębnij je.
3. Przejdź do katalogu głównego `spx-zips` wyodrębnionego z pobierania i Wyodrębnij `spx-netcore-30-linux-x64` go do nowego `~/spx` katalogu.
4. W terminalu wpisz następujące polecenia:
   1. `cd ~/spx`
   2. `sudo chmod +r+x spx`
   3. `PATH=~/spx:$PATH`

Wpisz `spx` , aby wyświetlić pomoc dla interfejsu wiersza polecenia mowy.

#### <a name="docker-install"></a>[Instalacja platformy Docker](#tab/dockerinstall)

> [!NOTE]
> <a href="https://www.docker.com/get-started" target="_blank">Pulpit Docker dla Twojej platformy <span class="docon docon-navigate-external x-hidden-focus"></span> </a> musi być zainstalowany.

Wykonaj następujące kroki, aby zainstalować interfejs wiersza polecenia mowy w kontenerze platformy Docker:

1. W nowym wierszu polecenia lub terminalu wpisz następujące polecenie:  `docker pull msftspeech/spx`
2. Wpisz to polecenie. Należy zapoznać się z informacjami pomocy dla interfejsu wiersza polecenia usługi Speech: `docker run -it --rm msftspeech/spx help`

### <a name="mount-a-directory-in-the-container"></a>Instalowanie katalogu w kontenerze

Narzędzie interfejsu wiersza polecenia mowy zapisuje ustawienia konfiguracji jako pliki i ładuje je podczas wykonywania dowolnego polecenia (z wyjątkiem poleceń pomocy).
W przypadku korzystania z interfejsu wiersza polecenia mowy w kontenerze platformy Docker należy zainstalować katalog lokalny z kontenera, dzięki czemu narzędzie może przechowywać lub wyszukiwać ustawienia konfiguracji, a także umożliwia odczytanie lub zapisanie plików wymaganych przez polecenie, takich jak pliki audio mowy.

W systemie Windows wpisz następujące polecenie, aby utworzyć lokalny katalog interfejsu wiersza polecenia mowy, którego można użyć w kontenerze:

`mkdir c:\spx-data`

Lub w systemie Linux lub Mac wpisz to polecenie w terminalu, aby utworzyć katalog i wyświetlić jego ścieżkę bezwzględną:

```bash
mkdir ~/spx-data
cd ~/spx-data
pwd
```

Ścieżka bezwzględna zostanie użyta podczas wywoływania interfejsu wiersza polecenia mowy.

### <a name="run-speech-cli-in-the-container"></a>Uruchamianie interfejsu wiersza polecenia mowy w kontenerze

W tej dokumentacji przedstawiono polecenie interfejsu wiersza `spx` polecenia mowy używane w instalacjach programów innych niż Docker.
Podczas wywoływania `spx` polecenia w kontenerze platformy Docker należy zainstalować katalog w kontenerze w systemie plików, w którym interfejs wiersza polecenia mowy może przechowywać i znajdować wartości konfiguracji oraz pliki odczytu i zapisu.
W systemie Windows polecenia będą wyglądać następująco:

`docker run -it -v c:\spx-data:/data --rm msftspeech/spx`

W systemie Linux lub Mac polecenia zaczną wyglądać podobnie do tego:

`sudo docker run -it -v /ABSOLUTE_PATH:/data --rm msftspeech/spx`

> [!NOTE]
> Zamień na `/ABSOLUTE_PATH` ścieżkę bezwzględną pokazaną przez `pwd` polecenie w powyższej sekcji.

Aby użyć `spx` polecenia zainstalowanego w kontenerze, należy zawsze wprowadzić pełne pokazane powyżej polecenie, a następnie parametry żądania.
Na przykład w systemie Windows to polecenie ustawia klucz:

`docker run -it -v c:\spx-data:/data --rm msftspeech/spx config @key --set SUBSCRIPTION-KEY`

> [!NOTE]
> Nie można użyć mikrofonu lub głośników komputera podczas uruchamiania interfejsu wiersza polecenia mowy w kontenerze platformy Docker.
> Aby korzystać z tych urządzeń, Przekaż pliki audio do i z interfejsu wiersza polecenia mowy do nagrywania/odtwarzania poza kontenerem platformy Docker.
> Narzędzie interfejsu wiersza polecenia mowy może uzyskać dostęp do katalogu lokalnego, który został skonfigurowany w powyższych krokach.

***

## <a name="create-subscription-config"></a>Utwórz konfigurację subskrypcji

Aby rozpocząć korzystanie z interfejsu wiersza polecenia mowy, należy wprowadzić klucz subskrypcji mowy i Identyfikator regionu. Pobierz te poświadczenia, wykonując czynności opisane w sekcji [Wypróbuj bezpłatnie usługę Speech](../overview.md#try-the-speech-service-for-free).
Gdy masz klucz subskrypcji i Identyfikator regionu (np. `eastus`, `westus` Uruchom następujące polecenia.

```shell
spx config @key --set SUBSCRIPTION-KEY
spx config @region --set REGION
```

Twoje uwierzytelnianie subskrypcji jest teraz przechowywane dla przyszłych żądań SPX. Jeśli musisz usunąć jedną z tych przechowywanych wartości, uruchom polecenie `spx config @region --clear` lub `spx config @key --clear` .
