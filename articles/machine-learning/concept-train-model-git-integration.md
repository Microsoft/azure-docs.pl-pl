---
title: Integracja z usługą git dla Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Dowiedz się, w jaki sposób Azure Machine Learning integruje się z lokalnym repozytorium git, aby śledzić repozytorium, gałąź i bieżące informacje o zatwierdzaniu w ramach przebiegu szkoleniowego.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.date: 11/16/2020
ms.openlocfilehash: cc844cbd2518bb131f6902d1da3e7653951224b5
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102547853"
---
# <a name="git-integration-for-azure-machine-learning"></a>Integracja z usługą git dla Azure Machine Learning

[Git](https://git-scm.com/) to popularny system kontroli wersji, który umożliwia udostępnianie i współpracę nad projektami. 

Azure Machine Learning w pełni obsługuje repozytoria Git na potrzeby śledzenia pracy — można klonować repozytoria bezpośrednio do udostępnionego systemu plików obszaru roboczego, korzystać z narzędzia Git na lokalnej stacji roboczej lub używać narzędzia Git z potoku ciągłej integracji/ciągłego dostarczania.

Podczas przesyłania zadania do Azure Machine Learning, jeśli pliki źródłowe są przechowywane w lokalnym repozytorium git, informacje o repozytorium są śledzone w ramach procesu szkoleniowego.

Ponieważ Azure Machine Learning śledzi informacje z lokalnego repozytorium git, nie jest ono powiązane z żadnym określonym centralnym repozytorium. Repozytorium można sklonować z witryny GitHub, GitLab, BitBucket, Azure DevOps lub dowolnej innej usługi zgodnej z Git.

## <a name="clone-git-repositories-into-your-workspace-file-system"></a>Klonowanie repozytoriów Git do systemu plików obszaru roboczego
Azure Machine Learning udostępnia współużytkowany system plików dla wszystkich użytkowników w obszarze roboczym.
Aby sklonować repozytorium git do tego udziału plików, zalecamy utworzenie wystąpienia obliczeniowego & [Otwarcie terminalu](how-to-access-terminal.md).
Po otwarciu terminalu masz dostęp do pełnego klienta git i można klonować i współdziałać z usługą Git za pośrednictwem interfejsu wiersza polecenia usługi git.

Zalecamy klonowanie repozytorium do katalogu użytkowników, tak aby inne osoby nie miały kolizji bezpośrednio w gałęzi roboczej.

Można sklonować dowolne repozytorium git, do których można się uwierzytelnić (GitHub, Azure Repos, BitBucket itp.).

Aby uzyskać więcej informacji na temat klonowania, zobacz Przewodnik dotyczący [korzystania z interfejsu wiersza polecenia usługi git](https://guides.github.com/introduction/git-handbook/).

## <a name="authenticate-your-git-account-with-ssh"></a>Uwierzytelnianie konta git przy użyciu protokołu SSH
### <a name="generate-a-new-ssh-key"></a>Generuj nowy klucz SSH
1) [Otwórz okno terminalu](./how-to-access-terminal.md) na karcie Azure Machine Learning Notes.

2) Wklej tekst poniżej, zastępując swój adres e-mail.

```bash
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```

Spowoduje to utworzenie nowego klucza SSH przy użyciu podanej wiadomości e-mail jako etykiety.

```
> Generating public/private rsa key pair.
```

3) Po wyświetleniu monitu o podanie klucza "Wprowadź plik, w którym ma zostać zapisany klucz" naciśnij klawisz ENTER. Spowoduje to zaakceptowanie domyślnej lokalizacji pliku.

4) Sprawdź, czy lokalizacja domyślna to "/Home/azureuser/.ssh", i naciśnij klawisz ENTER. W przeciwnym razie Określ lokalizację "/Home/azureuser/.ssh".

> [!TIP]
> Upewnij się, że klucz SSH został zapisany w "/Home/azureuser/.ssh". Ten plik jest zapisywany w wystąpieniu obliczeniowym jest dostępny tylko dla właściciela wystąpienia obliczeniowego

```
> Enter a file in which to save the key (/home/azureuser/.ssh/id_rsa): [Press enter]
```

5) W wierszu polecenia wpisz bezpieczne hasło. Zalecamy dodanie hasła do klucza SSH w celu zwiększenia bezpieczeństwa

```
> Enter passphrase (empty for no passphrase): [Type a passphrase]
> Enter same passphrase again: [Type passphrase again]
```

### <a name="add-the-public-key-to-git-account"></a>Dodaj klucz publiczny do konta git
1) W oknie terminalu Skopiuj zawartość pliku klucza publicznego. Jeśli zmieniono nazwę klucza, Zastąp id_rsa. pub nazwą pliku klucza publicznego.

```bash
cat ~/.ssh/id_rsa.pub
```
> [!TIP]
> **Kopiowanie i wklejanie w terminalu**
> * Windows: `Ctrl-Insert` do kopiowania i używania `Ctrl-Shift-v` lub `Shift-Insert` do wklejania.
> * Mac OS: `Cmd-c` kopiowanie i `Cmd-v` wklejanie.
> * Program FireFox/IE może prawidłowo obsługiwać uprawnienia do Schowka.

2) Wybierz i skopiuj dane wyjściowe klucza w Schowku.

+ [GitHub](https://docs.github.com/github/authenticating-to-github/adding-a-new-ssh-key-to-your-github-account)

+ [GitLab](https://docs.gitlab.com/ee/ssh/#adding-an-ssh-key-to-your-gitlab-account)

+ [Azure DevOps](/azure/devops/repos/git/use-ssh-keys-to-authenticate#step-2--add-the-public-key-to-azure-devops-servicestfs)  Zacznij od **kroku 2**.

+ [BitBucket](https://support.atlassian.com/bitbucket-cloud/docs/set-up-an-ssh-key/#SetupanSSHkey-ssh2). Zacznij od **kroku 4**.

### <a name="clone-the-git-repository-with-ssh"></a>Klonowanie repozytorium git przy użyciu protokołu SSH

1) Skopiuj adres URL klonowania git SSH z repozytorium git.

2) Wklej adres URL do `git clone` poniższego polecenia, aby użyć adresu URL repozytorium git SSH. Będzie to wyglądać następująco:

```bash
git clone git@example.com:GitUser/azureml-example.git
Cloning into 'azureml-example'...
```

Zobaczysz odpowiedź podobną do:

```bash
The authenticity of host 'example.com (192.30.255.112)' can't be established.
RSA key fingerprint is SHA256:nThbg6kXUpJWGl7E1IGOCspRomTxdCARLviKw6E5SY8.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'github.com,192.30.255.112' (RSA) to the list of known hosts.
```

Protokół SSH może wyświetlać odcisk palca SSH serwera i poprosił o jego zweryfikowanie. Należy sprawdzić, czy wyświetlane odciski palca odpowiadają jednemu z odcisków palców na stronie kluczy publicznych SSH.

SSH wyświetla ten odcisk palca, gdy nawiązuje połączenie z nieznanym hostem w celu ochrony przed [atakami typu man-in-the-Middle](/previous-versions/windows/it-pro/windows-2000-server/cc959354(v=technet.10)). Po zaakceptowaniu odcisku palca hosta protokół SSH nie wyświetli monitu ponownie, chyba że odcisk palca zostanie zmieniony.

3) Po wyświetleniu monitu, jeśli chcesz kontynuować łączenie, wpisz polecenie `yes` . Narzędzie git spowoduje sklonowanie repozytorium i skonfigurowanie źródła pochodzenia do łączenia się z protokołem SSH na potrzeby przyszłych poleceń usługi git.

## <a name="track-code-that-comes-from-git-repositories"></a>Śledź kod pochodzący z repozytoriów Git

Po przesłaniu szkolenia z zestawu SDK języka Python lub interfejsu wiersza polecenia Machine Learning pliki potrzebne do uczenia modelu są przekazywane do obszaru roboczego. Jeśli `git` polecenie jest dostępne w środowisku deweloperskim, proces przekazywania używa go do sprawdzenia, czy pliki są przechowywane w repozytorium git. Jeśli tak, to w ramach przebiegu szkoleniowego zostanie również przekazane informacje z repozytorium git. Te informacje są przechowywane w następujących właściwościach przebiegu szkoleniowego:

| Właściwość | Polecenie git użyte do pobrania wartości | Opis |
| ----- | ----- | ----- |
| `azureml.git.repository_uri` | `git ls-remote --get-url` | Identyfikator URI, z którego zostało Sklonowane repozytorium. |
| `mlflow.source.git.repoURL` | `git ls-remote --get-url` | Identyfikator URI, z którego zostało Sklonowane repozytorium. |
| `azureml.git.branch` | `git symbolic-ref --short HEAD` | Aktywna gałąź podczas przesyłania przebiegu. |
| `mlflow.source.git.branch` | `git symbolic-ref --short HEAD` | Aktywna gałąź podczas przesyłania przebiegu. |
| `azureml.git.commit` | `git rev-parse HEAD` | Skrót zatwierdzenia kodu, który został przesłany dla uruchomienia. |
| `mlflow.source.git.commit` | `git rev-parse HEAD` | Skrót zatwierdzenia kodu, który został przesłany dla uruchomienia. |
| `azureml.git.dirty` | `git status --porcelain .` | `True`, jeśli gałąź/zatwierdzenie są zanieczyszczone; w przeciwnym razie `false` . |

Te informacje są wysyłane do przebiegów korzystających z szacowania, potoku uczenia maszynowego lub uruchamiania skryptów.

Jeśli pliki szkoleniowe nie znajdują się w repozytorium Git w środowisku deweloperskim lub `git` polecenie jest niedostępne, nie są śledzone żadne informacje dotyczące usługi git.

> [!TIP]
> Aby sprawdzić, czy polecenie git jest dostępne w środowisku deweloperskim, Otwórz sesję powłoki, wiersz polecenia, program PowerShell lub inny interfejs wiersza polecenia i wpisz następujące polecenie:
>
> ```
> git --version
> ```
>
> Jeśli jest zainstalowany, a w ścieżce otrzymujesz odpowiedź podobną do `git version 2.4.1` . Aby uzyskać więcej informacji na temat instalowania usługi Git w środowisku deweloperskim, zobacz [witrynę sieci Web usługi git](https://git-scm.com/).

## <a name="view-the-logged-information"></a>Wyświetlanie zarejestrowanych informacji

Informacje dotyczące usługi git są przechowywane we właściwościach przebiegu szkoleniowego. Te informacje można wyświetlić przy użyciu Azure Portal, zestawu Python SDK i interfejsu wiersza polecenia. 

### <a name="azure-portal"></a>Azure Portal

1. W [portalu Studio](https://ml.azure.com)wybierz swój obszar roboczy.
1. Wybierz pozycję __eksperymenty__, a następnie wybierz jedno z eksperymentów.
1. Wybierz jeden z przebiegów z kolumny __numer uruchomienia__ .
1. Wybierz pozycję dane __wyjściowe + dzienniki__, a następnie rozwiń pozycję __dzienniki__ i wpisy w usłudze __Azure__ . Wybierz łącze zaczynające się od __### \_ platformy Azure__.

Zarejestrowane informacje zawierają tekst podobny do następującego:

```json
"properties": {
    "_azureml.ComputeTargetType": "batchai",
    "ContentSnapshotId": "5ca66406-cbac-4d7d-bc95-f5a51dd3e57e",
    "azureml.git.repository_uri": "git@github.com:azure/machinelearningnotebooks",
    "mlflow.source.git.repoURL": "git@github.com:azure/machinelearningnotebooks",
    "azureml.git.branch": "master",
    "mlflow.source.git.branch": "master",
    "azureml.git.commit": "4d2b93784676893f8e346d5f0b9fb894a9cf0742",
    "mlflow.source.git.commit": "4d2b93784676893f8e346d5f0b9fb894a9cf0742",
    "azureml.git.dirty": "True",
    "AzureML.DerivedImageName": "azureml/azureml_9d3568242c6bfef9631879915768deaf",
    "ProcessInfoFile": "azureml-logs/process_info.json",
    "ProcessStatusFile": "azureml-logs/process_status.json"
}
```

### <a name="python-sdk"></a>Zestaw SDK dla języka Python

Po przesłaniu przebiegu szkoleniowego zwracany jest obiekt [Run](/python/api/azureml-core/azureml.core.run%28class%29) . `properties`Atrybut tego obiektu zawiera zarejestrowane informacje usługi git. Na przykład poniższy kod pobiera skrót zatwierdzenia:

```python
run.properties['azureml.git.commit']
```

### <a name="cli"></a>Interfejs wiersza polecenia

`az ml run`Polecenie CLI może służyć do pobierania właściwości z przebiegu. Na przykład następujące polecenie zwraca właściwości dla ostatniego uruchomienia w eksperymentie o nazwie `train-on-amlcompute` :

```azurecli-interactive
az ml run list -e train-on-amlcompute --last 1 -w myworkspace -g myresourcegroup --query '[].properties'
```

Aby uzyskać więcej informacji, zapoznaj się z dokumentacją [AZ ml Run](/cli/azure/ext/azure-cli-ml/ml/run) Reference.

## <a name="next-steps"></a>Następne kroki

* [Użyj obiektów docelowych obliczeń do szkolenia modelu](how-to-set-up-training-targets.md)