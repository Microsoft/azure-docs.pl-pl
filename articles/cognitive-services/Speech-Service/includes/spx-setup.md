---
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/15/2020
ms.author: v-demjoh
ms.openlocfilehash: a995a110c4f777603e5b0273b6025cd68fe55b67
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102532540"
---
## <a name="download-and-install"></a>Pobieranie i instalowanie

#### <a name="windows-install"></a>[Instalacja systemu Windows](#tab/windowsinstall)

Wykonaj następujące kroki, aby zainstalować interfejs wiersza polecenia mowy w systemie Windows:

1. W systemie Windows wymagany jest [Microsoft Visual C++ redystrybucyjny dla programu Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) dla danej platformy. Zainstalowanie tego po raz pierwszy może wymagać ponownego uruchomienia.
1. Zainstaluj [zestaw SDK platformy .NET Core 3,1](/dotnet/core/install/windows).
2. Zainstaluj interfejs wiersza polecenia mowy przy użyciu NuGet, wprowadzając następujące polecenie:

   ```console
   dotnet tool install --global Microsoft.CognitiveServices.Speech.CLI --version 1.15.0
   ```
Wpisz `spx` , aby wyświetlić pomoc dla interfejsu wiersza polecenia mowy.

> [!NOTE]
> Alternatywą dla programu NuGet jest pobranie i wyodrębnienie [archiwum zip](https://aka.ms/speech/spx-zips.zip)interfejsu wiersza polecenia mowy, znalezienie i wyodrębnienie platformy z `spx-zips` katalogu oraz dodanie `spx` ścieżki do zmiennej **Path** systemu.


### <a name="font-limitations"></a>Ograniczenia dotyczące czcionek

W systemie Windows interfejs wiersza polecenia mowy może wyświetlać tylko czcionki dostępne dla wiersza poleceń na komputerze lokalnym.
[Terminal systemu Windows](https://www.microsoft.com/en-us/p/windows-terminal/9n0dx20hk701) obsługuje wszystkie czcionki opracowane interaktywnie przez interfejs wiersza polecenia mowy.

Jeśli dane wyjściowe są wyprowadzane do pliku, Edytor tekstu, taki jak Notatnik lub przeglądarka sieci Web, np. Microsoft Edge, może również wyświetlać wszystkie czcionki.

#### <a name="linux-install"></a>[Instalacja systemu Linux](#tab/linuxinstall)

Obsługiwane są następujące dystrybucje systemu Linux dla architektury x64 za pomocą interfejsu wiersza polecenia mowy:

* CentOS 7/8
* Debian 9/10 
* Red Hat Enterprise Linux (RHEL) 7/8
* Ubuntu 16.04/18.04/20.04

> [!NOTE]
> Dodatkowe architektury są obsługiwane przez zestaw SDK mowy (nie interfejs wiersza polecenia mowy). Aby uzyskać więcej informacji, zobacz [Informacje o zestawie mowy SDK](../speech-sdk.md).

Wykonaj następujące kroki, aby zainstalować interfejs wiersza polecenia mowy w systemie Linux na PROCESORze x64:

1. Zainstaluj [zestaw SDK platformy .NET Core 3,1](/dotnet/core/install/linux).
2. Zainstaluj interfejs wiersza polecenia mowy przy użyciu NuGet, wprowadzając następujące polecenie:

    `dotnet tool install --global Microsoft.CognitiveServices.Speech.CLI --version 1.15.0`

Wpisz `spx` , aby wyświetlić pomoc dla interfejsu wiersza polecenia mowy.

> [!NOTE]
> Alternatywą dla programu NuGet jest pobranie plików binarnych w [archiwum zip](https://aka.ms/speech/spx-zips.zip), wyodrębnienie ich `spx-netcore-30-linux-x64.zip` do nowego `~/spx` katalogu, wpisanie `sudo chmod +r+x spx` pliku binarnego i dodanie `~/spx` ścieżki do zmiennej systemowej PATH.


#### <a name="docker-install-windows-linux-macos"></a>[Instalacja platformy Docker (Windows, Linux, macOS)](#tab/dockerinstall)

Wykonaj następujące kroki, aby zainstalować interfejs wiersza polecenia mowy w kontenerze platformy Docker:

1. <a href="https://www.docker.com/get-started" target="_blank">Zainstaluj program Docker Desktop</a> dla swojej platformy, jeśli nie jest jeszcze zainstalowany.
2. W nowym wierszu polecenia lub terminalu wpisz następujące polecenie:
   ```console   
   docker pull msftspeech/spx
   ```
3. Wpisz to polecenie. Należy zapoznać się z informacjami pomocy dla interfejsu wiersza polecenia usługi Speech:
   ```console 
   docker run -it --rm msftspeech/spx help
   ```

### <a name="mount-a-directory-in-the-container"></a>Instalowanie katalogu w kontenerze

Narzędzie interfejsu wiersza polecenia mowy zapisuje ustawienia konfiguracji jako pliki i ładuje je podczas wykonywania dowolnego polecenia (z wyjątkiem poleceń pomocy).
W przypadku korzystania z interfejsu wiersza polecenia mowy w kontenerze platformy Docker należy zainstalować katalog lokalny z kontenera, dzięki czemu narzędzie może przechowywać lub wyszukiwać ustawienia konfiguracji, a także umożliwia odczytanie lub zapisanie plików wymaganych przez polecenie, takich jak pliki audio mowy.

W systemie Windows wpisz następujące polecenie, aby utworzyć lokalny katalog interfejsu wiersza polecenia mowy, którego można użyć w kontenerze:

`mkdir c:\spx-data`

Lub w systemie Linux lub macOS wpisz to polecenie w terminalu, aby utworzyć katalog i wyświetlić jego ścieżkę bezwzględną:

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

```console
docker run -it -v c:\spx-data:/data --rm msftspeech/spx
```

W systemie Linux lub macOS polecenia będą wyglądały jak przykład poniżej. Zamień na `ABSOLUTE_PATH` ścieżkę bezwzględną dla zainstalowanego katalogu. Ta ścieżka została zwrócona przez `pwd` polecenie w poprzedniej sekcji. 

W przypadku uruchomienia tego polecenia przed ustawieniem klucza i regionu zostanie wyświetlony komunikat o błędzie z informacją o ustawieniu klucza i regionu:
```console   
sudo docker run -it -v ABSOLUTE_PATH:/data --rm msftspeech/spx
```

Aby użyć `spx` polecenia zainstalowanego w kontenerze, należy zawsze wprowadzić pełne pokazane powyżej polecenie, a następnie parametry żądania.
Na przykład w systemie Windows to polecenie ustawia klucz:

```console
docker run -it -v c:\spx-data:/data --rm msftspeech/spx config @key --set SUBSCRIPTION-KEY
```

Aby uzyskać bardziej rozszerzoną interakcję z narzędziem wiersza polecenia, można uruchomić kontener z interaktywną powłoką bash, dodając parametr punktu wejścia.
W systemie Windows wprowadź to polecenie, aby uruchomić kontener, który udostępnia interaktywny interfejs wiersza polecenia, w którym można wprowadzić wiele `spx` poleceń:
```console
docker run -it --entrypoint=/bin/bash -v c:\spx-data:/data --rm msftspeech/spx
```

> [!WARNING]
> Nie można użyć mikrofonu komputera podczas uruchamiania interfejsu wiersza polecenia mowy w kontenerze platformy Docker. Można jednak odczytywać i zapisywać pliki audio w lokalnym zainstalowanym katalogu. 

<!-- Need to troubleshoot issues with docker pull image

### Optional: Create a command line shortcut

If you're running the the Speech CLI from a Docker container on Linux or macOS you can create a shortcut. 

Follow these instructions to create a shortcut:
1. Open `.bash_profile` with your favorite text editor. For example:
   ```shell
   nano ~/.bash_profile
   ```
2. Next, add this function to your `.bash_profile`. Make sure you update this function with the correct path to your mounted directory:
   ```shell   
   spx(){
       sudo docker run -it -v ABSOLUTE_PATH:/data --rm msftspeech/spx
   }
   ```
3. Source your profile:
   ```shell
   source ~/.bash_profile
   ```
4. Now instead of running `sudo docker run -it -v ABSOLUTE_PATH:/data --rm msftspeech/spx`, you can just type `spx` followed by arguments. For example: 
   ```shell
   // Get some help
   spx help recognize

   // Recognize speech from an audio file 
   spx recognize --file /mounted/directory/file.wav
   ```

> [!WARNING]
> If you change the mounted directory that Docker is referencing, you need to update the function in `.bash_profile`.
--->
***

## <a name="create-subscription-config"></a>Utwórz konfigurację subskrypcji

Aby rozpocząć korzystanie z interfejsu wiersza polecenia mowy, należy wprowadzić klucz subskrypcji mowy i Identyfikator regionu. Pobierz te poświadczenia, wykonując czynności opisane w sekcji [Wypróbuj bezpłatnie usługę Speech](../overview.md#try-the-speech-service-for-free).
Gdy masz klucz subskrypcji i Identyfikator regionu (np. `eastus`, `westus` Uruchom następujące polecenia.

```console
spx config @key --set SUBSCRIPTION-KEY
spx config @region --set REGION
```

Twoje uwierzytelnianie subskrypcji jest teraz przechowywane dla przyszłych żądań SPX. Jeśli musisz usunąć jedną z tych przechowywanych wartości, uruchom polecenie `spx config @region --clear` lub `spx config @key --clear` .
