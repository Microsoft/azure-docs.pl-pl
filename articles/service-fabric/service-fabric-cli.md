---
title: Wprowadzenie do interfejsu wiersza polecenia usługi Azure Service Fabric
description: Dowiedz się, jak korzystać z interfejsu wiersza polecenia usługi Azure Service Fabric. Dowiedz się, jak nawiązać połączenie z klastrem i zarządzać aplikacjami.
author: jeffj6123
ms.topic: conceptual
ms.date: 5/19/2020
ms.author: jejarry
ms.openlocfilehash: a1b0598f34fe894e078dc647149cff4ac74e1fea
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104802152"
---
# <a name="azure-service-fabric-cli"></a>Interfejs wiersza polecenia usługi Azure Service Fabric

Interfejs wiersza polecenia usługi Azure Service Fabric jest narzędziem wiersza polecenia służącym do interakcji z jednostkami usługi Service Fabric i zarządzania nimi. Interfejs wiersza polecenia usługi Service Fabric może być używany z klastrami systemu Windows lub Linux. Interfejs wiersza polecenia usługi Service Fabric działa na dowolnej platformie, która obsługuje język Python.

[!INCLUDE [links to azure cli and service fabric cli](../../includes/service-fabric-sfctl.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Przed instalacją upewnij się, że w środowisku zainstalowano zarówno język Python, jak i narzędzie pip. Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją Szybki start dotyczącą narzędzia pip](https://pip.pypa.io/en/latest/quickstart/) i oficjalną [dokumentacją dotyczącą instalowania języka Python](https://wiki.python.org/moin/BeginnersGuide/Download).

Interfejs wiersza polecenia obsługuje środowisko Python w wersji 2,7 i 3.6 +, w tym zalecane środowisko Python 3. x.

### <a name="service-fabric-target-runtime"></a>Docelowe środowisko uruchomieniowe usługi Service Fabric

Interfejs wiersza polecenia usługi Service Fabric jest przeznaczony do obsługi najnowszej wersji środowiska uruchomieniowego zestawu Service Fabric SDK. Skorzystaj z poniższej tabeli, aby określić, którą wersję interfejsu wiersza polecenia zainstalować:

| Wersja interfejsu wiersza polecenia   | Obsługiwana wersja środowiska uruchomieniowego |
|---------------|---------------------------|
| Najnowsze (~ = 10) | Najnowsza (~ = 7,1)            |
| 9.0.0         | 7.1                       |
| 8.0.0         | 6.5                       |
| 7.1.0         | 6.4                       |
| 6.0.0         | 6.3                       |
| 5.0.0         | 6,2                       |
| 4.0.0         | 6.1                       |
| 3.0.0         | 6.0                       |
| 1.1.0         | 5.6, 5.7                  |

Opcjonalnie można określić docelową wersję interfejsu wiersza polecenia do zainstalowania, dodając do polecenia `pip install` sufiks z wersją `==<version>`. Na przykład składnia dla wersji 1.1.0 będzie następująca:

```shell
pip install -I sfctl==1.1.0
```

W razie potrzeby zastąp następujące polecenie `pip install` przy użyciu polecenia opisanego powyżej.

Aby uzyskać więcej informacji o wersjach interfejsu wiersza polecenia usługi Service Fabric, zobacz [dokumentację usługi GitHub](https://github.com/Azure/service-fabric-cli/releases).

## <a name="install-pip-python-and-the-service-fabric-cli"></a>Instalowanie narzędzia pip, środowiska Python i interfejsu wiersza polecenia usługi Service Fabric

Dostępnych jest szereg sposobów instalowania narzędzia pip i środowiska Python na używanej platformie. Poniżej przedstawiono kroki konfiguracji narzędzia pip i środowiska Python 3 w najważniejszych systemach operacyjnych.

### <a name="windows"></a>Windows

W przypadku systemów Windows 10, Windows Server 2016 i Windows Server 2012 R2 należy postępować zgodnie z oficjalnymi instrukcjami standardowej instalacji. Instalator środowiska Python domyślnie instaluje też narzędzie pip.

1. Przejdź do oficjalnej [strony pobierania środowiska Python](https://www.python.org/downloads/)i Pobierz najnowszą wersję środowiska Python 3,7.

2. Uruchom instalatora.

3. W dolnej części monitu wybierz pozycję **Dodaj język Python 3,7 do ścieżki**.

4. Wybierz pozycję **Install Now (Zainstaluj teraz)** i dokończ instalację.

Możesz teraz otworzyć nowe okno polecenia i pobrać odpowiednią wersję środowiska Python i narzędzia pip.

```shell
python --version
pip --version
```

Następnie uruchom następujące polecenie, aby zainstalować interfejs wiersza polecenia platformy Azure Service Fabric (sfctl) i wyświetlić stronę pomocy interfejsu wiersza polecenia:

```shell
pip install sfctl
sfctl -h
```

### <a name="ubuntu-and-windows-subsystem-for-linux"></a>Podsystem Ubuntu i Windows dla systemu Linux

Aby zainstalować interfejs wiersza polecenia usługi Service Fabric, uruchom następujące polecenie:

```bash
sudo apt-get install python3
sudo apt-get install python3-pip
pip3 install sfctl
```

Następnie możesz przetestować instalację przy użyciu następującego polecenia:

```bash
sfctl -h
```

Jeśli zostanie wyświetlony komunikat o błędzie wskazujący na to, że nie znaleziono polecenia, taki jak:

`sfctl: command not found`

Upewnij się, że element `~/.local/bin` jest dostępny w lokalizacji `$PATH`:

```bash
export PATH=$PATH:~/.local/bin
echo "export PATH=$PATH:~/.local/bin" >> .shellrc
```

W przypadku niepowodzenia instalacji w podsystemie systemu Windows dla systemu Linux z powodu nieprawidłowych uprawnień folderu być może trzeba będzie spróbować ponownie z podwyższonym poziomem uprawnień:

```bash
sudo pip3 install sfctl
```

### <a name="red-hat-enterprise-linux-74-service-fabric-preview-support"></a>Red Hat Enterprise Linux 7.4 (obsługa wersji zapoznawczej usługi Service Fabric)

Aby zainstalować interfejs wiersza polecenia Service Fabric na Red Hat, uruchom następujące polecenia:

```bash
sudo yum install -y python34
sudo yum install python34-setuptools
sudo easy_install-3.4 pip
sudo pip3 install sfctl
```

W celu przetestowania instalacji można zapoznać się z krokami opisanymi w sekcji **Ubuntu i podsystem Windows dla systemu Linux** .

<a name = "cli-mac"></a>
### <a name="macos"></a>MacOS

W przypadku systemu MacOS zaleca się użycie [menedżera pakietów HomeBrew](https://brew.sh). Zainstaluj program HomeBrew, jeśli jeszcze go nie zainstalowano. W tym celu uruchom następujące polecenie:

```bash
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

Następnie z terminalu zainstaluj środowisko Python 3,7, PIP i interfejs wiersza polecenia Service Fabric, uruchamiając następujące polecenia:

```bash
brew install python3
pip3 install sfctl
sfctl -h
```

## <a name="cli-syntax"></a>Składnia interfejsu wiersza polecenia

Polecenia mają zawsze prefiks `sfctl`. Aby uzyskać ogólne informacje na temat wszystkich dostępnych poleceń, użyj polecenia `sfctl -h`. Aby uzyskać pomoc dotyczącą pojedynczego polecenia, użyj polecenia `sfctl <command> -h`.

Polecenia mają powtarzalną strukturę, w której element docelowy poprzedza zlecenie (akcję).

```shell
sfctl <object> <action>
```

W tym przykładzie `<object>` to element docelowy akcji `<action>`.

## <a name="select-a-cluster"></a>Wybieranie klastra

Przed wykonaniem jakiejkolwiek operacji musisz wybrać klaster, z którym chcesz nawiązać połączenie. Aby na przykład wybrać klaster o nazwie `testcluster.com` i nawiązać z nim połączenie, uruchom następujące polecenie:

> [!WARNING]
> Nie używaj niezabezpieczonych klastrów usługi Service Fabric w środowisku produkcyjnym.

```shell
sfctl cluster select --endpoint http://testcluster.com:19080
```

Punkt końcowy klastra musi mieć prefiks `http` lub `https`. Musi on zawierać port bramy HTTP. Port i adres są takie same jak adres URL programu Service Fabric Explorer.

W przypadku klastrów zabezpieczonych certyfikatem możesz określić certyfikat zakodowany w formacie PEM. Certyfikat można określić jako pojedynczy plik lub jako parę obejmującą certyfikat i klucz. W przypadku certyfikatu z podpisem własnym, który nie jest podpisany przez urząd certyfikacji, można użyć opcji `--no-verify`, aby pominąć weryfikację urzędu certyfikacji.

```shell
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem --no-verify
```

Aby uzyskać więcej informacji, zobacz temat [Nawiązywanie połączenia z zabezpieczonym klastrem usługi Azure Service Fabric](service-fabric-connect-to-secure-cluster.md).

## <a name="basic-operations"></a>Operacje podstawowe

Informacje o połączeniu klastra są utrwalane w wielu sesjach interfejsu wiersza polecenia usługi Service Fabric. Po wybraniu klastra usługi Service Fabric można uruchomić dowolne polecenie usługi Service Fabric w klastrze.

Aby na przykład uzyskać informacje o kondycji klastra usługi Service Fabric, uruchom następujące polecenie:

```shell
sfctl cluster health
```

Polecenie powoduje zwrócenie następujących danych wyjściowych:

```json
{
  "aggregatedHealthState": "Ok",
  "applicationHealthStates": [
    {
      "aggregatedHealthState": "Ok",
      "name": "fabric:/System"
    }
  ],
  "healthEvents": [],
  "nodeHealthStates": [
    {
      "aggregatedHealthState": "Ok",
      "id": {
        "id": "66aa824a642124089ee474b398d06a57"
      },
      "name": "_Test_0"
    }
  ],
  "unhealthyEvaluations": []
}
```

## <a name="tips-and-troubleshooting"></a>Porady i rozwiązywanie problemów

Kilka sugestii i porad dotyczących rozwiązywania typowych problemów.

### <a name="convert-a-certificate-from-pfx-to-pem-format"></a>Konwertowanie certyfikatu z formatu PFX na PEM

Interfejs wiersza polecenia usługi Service Fabric obsługuje certyfikaty po stronie klienta w postaci plików PEM (rozszerzenie pem). Jeśli używasz plików PFX z systemu Windows, musisz konwertować te certyfikaty na format PEM. Aby konwertować plik PFX na plik PEM, użyj następującego polecenia:

```shell
openssl pkcs12 -in certificate.pfx -out mycert.pem -nodes
```

Podobnie aby przekonwertować plik PEM do pliku PFX, można użyć następującego polecenia (nie wymaga ono podawania hasła):

```shell
openssl  pkcs12 -export -out Certificates.pfx -inkey Certificates.pem -in Certificates.pem -passout pass:'' 
```

Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją dotyczącą protokołu OpenSSL](https://www.openssl.org/docs/).

### <a name="connection-problems"></a>Problemy z połączeniem

Niektóre operacje mogą generować następujący komunikat:

`Failed to establish a new connection`

Sprawdź, czy punkt końcowy określonego klastra jest dostępny i przeprowadza nasłuchiwanie. Sprawdź również, czy interfejs użytkownika programu Service Fabric Explorer jest dostępny na tym hoście i porcie. Aby zaktualizować punkt końcowy, użyj polecenia `sfctl cluster select`.

### <a name="detailed-logs"></a>Szczegółowe dzienniki

Szczegółowe dzienniki często bywają przydatne w przypadku debugowania lub zgłaszania problemu. `--debug`Flaga zwiększa poziom szczegółowości danych wyjściowych.

### <a name="command-help-and-syntax"></a>Polecenia — pomoc i składnia

Aby uzyskać pomoc dotyczącą określonego polecenia lub grupy poleceń, użyj flagi `-h`.

```shell
sfctl application -h
```

Oto inny przykład:

```shell
sfctl application create -h
```

## <a name="updating-the-service-fabric-cli"></a>Aktualizowanie interfejsu wiersza polecenia usługi Service Fabric 

Aby zaktualizować interfejs wiersza polecenia usługi Service Fabric, uruchom następujące polecenia (element `pip` zastąp elementem `pip3` w zależności od opcji wybranej podczas oryginalnej instalacji):

```shell
pip uninstall sfctl
pip install sfctl
```

## <a name="next-steps"></a>Następne kroki

* [Deploy an application with the Azure Service Fabric CLI (Wdrażanie aplikacji przy użyciu interfejsu wiersza polecenia usługi Azure Service Fabric)](service-fabric-application-lifecycle-sfctl.md)
* [Wprowadzenie do usługi Service Fabric w systemie Linux](service-fabric-get-started-linux.md)
