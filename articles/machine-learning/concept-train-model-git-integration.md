---
title: Integracja z usługą Git dla Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Dowiedz się, Azure Machine Learning integruje się z lokalnym repozytorium Git w celu śledzenia repozytorium, gałęzi i bieżących informacji o zatwierdzeniu w ramach przebiegów szkoleniowych.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.date: 04/08/2021
ms.openlocfilehash: 60dca43f95b190791c8fb593042ed612340a3af5
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874553"
---
# <a name="git-integration-for-azure-machine-learning"></a>Integracja z usługą Git dla Azure Machine Learning

[Git](https://git-scm.com/) to popularny system kontroli wersji, który umożliwia udostępnianie projektów i współpracę nad nimi. 

Azure Machine Learning w pełni obsługuje repozytoria Git do śledzenia pracy — repozytoria można klonować bezpośrednio do systemu plików udostępnionego obszaru roboczego, używać usługi Git na lokalnej stacji roboczej lub używać usługi Git z potoku ci/CD.

Jeśli podczas przesyłania zadania do usługi Azure Machine Learning pliki źródłowe są przechowywane w lokalnym repozytorium Git, informacje o repozytorium są śledzone w ramach procesu trenowania.

Ponieważ Azure Machine Learning śledzi informacje z lokalnego repozytorium Git, nie jest ono powiązane z żadnym konkretnym centralnym repozytorium. Repozytorium można sklonować z repozytorium GitHub, GitLab, Bitbucket, Azure DevOps lub dowolnej innej usługi zgodnej z usługą Git.

> [!TIP]
> Użyj Visual Studio Code, aby korzystać z usługi Git za pośrednictwem graficznego interfejsu użytkownika. Aby nawiązać połączenie ze zdalnym wystąpieniem obliczeniowym Azure Machine Learning przy użyciu usługi Visual Studio Code, zobacz Connect to an Azure Machine Learning compute instance in Visual Studio Code (preview) (Nawiązywanie połączenia z wystąpieniem obliczeniowym usługi Azure Machine Learning w Visual Studio Code [(wersja zapoznawcza)](how-to-set-up-vs-code-remote.md)
>
> Aby uzyskać więcej informacji na temat Visual Studio Code kontroli wersji, zobacz Using Version Control in VS Code and Working with GitHub in VS Code (Korzystanie z kontroli wersji w programie [VS Code)](https://code.visualstudio.com/docs/editor/versioncontrol) i Working with GitHub in VS Code (Praca z usługą [GitHub w usłudze VS Code).](https://code.visualstudio.com/docs/editor/github)

## <a name="clone-git-repositories-into-your-workspace-file-system"></a>Klonowanie repozytoriów Git do systemu plików obszaru roboczego
Azure Machine Learning udostępnia udostępniony system plików dla wszystkich użytkowników w obszarze roboczym.
Aby sklonować repozytorium Git do tego udziału plików, zalecamy utworzenie wystąpienia obliczeniowego & [otwarcie terminalu](how-to-access-terminal.md).
Po otwarciu terminalu będziesz mieć dostęp do pełnego klienta Usługi Git oraz możesz klonować i pracować z usługą Git za pośrednictwem interfejsu wiersza polecenia usługi Git.

Zalecamy sklonowanie repozytorium do katalogu użytkowników, aby inne osoby nie zderzyły się bezpośrednio z gałęzią roboczą.

Możesz sklonować dowolne repozytorium Git, w których możesz się uwierzytelnić (GitHub, Azure Repos, BitBucket itp.)

Aby uzyskać więcej informacji na temat klonowania, zobacz przewodnik dotyczący [używania interfejsu wiersza polecenia usługi Git.](https://guides.github.com/introduction/git-handbook/)

## <a name="authenticate-your-git-account-with-ssh"></a>Uwierzytelnianie konta Usługi Git za pomocą połączenia SSH
### <a name="generate-a-new-ssh-key"></a>Generowanie nowego klucza SSH
1) [Otwórz okno terminalu](./how-to-access-terminal.md) na karcie Azure Machine Learning Notes.

2) Wklej poniższy tekst, zastępując swój adres e-mail.

```bash
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```

Powoduje to utworzenie nowego klucza SSH przy użyciu podanej wiadomości e-mail jako etykiety.

```
> Generating public/private rsa key pair.
```

3) Po wyświetleniu monitu "Wprowadź plik, w którym ma być zapisywany klucz", naciśnij klawisz Enter. Akceptuje to domyślną lokalizację pliku.

4) Sprawdź, czy domyślna lokalizacja to "/home/azureuser/.ssh", a następnie naciśnij klawisz Enter. W przeciwnym razie określ lokalizację "/home/azureuser/.ssh".

> [!TIP]
> Upewnij się, że klucz SSH został zapisany w pliku "/home/azureuser/.ssh". Ten plik jest zapisywany w wystąpieniu obliczeniowym i jest dostępny tylko dla właściciela wystąpienia obliczeniowego

```
> Enter a file in which to save the key (/home/azureuser/.ssh/id_rsa): [Press enter]
```

5) W wierszu polecenia wpisz bezpieczne hasło. Zalecamy dodanie hasła do klucza SSH w celu dodania zabezpieczeń

```
> Enter passphrase (empty for no passphrase): [Type a passphrase]
> Enter same passphrase again: [Type passphrase again]
```

### <a name="add-the-public-key-to-git-account"></a>Dodawanie klucza publicznego do konta usługi Git
1) W oknie terminalu skopiuj zawartość pliku klucza publicznego. Jeśli zmieniono nazwę klucza, zastąp id_rsa.pub nazwą pliku klucza publicznego.

```bash
cat ~/.ssh/id_rsa.pub
```
> [!TIP]
> **Kopiowanie i wklejanie w terminalu**
> * Windows: `Ctrl-Insert` do skopiowania i użycia `Ctrl-Shift-v` lub do `Shift-Insert` wklejenia.
> * Mac OS: `Cmd-c` aby skopiować i `Cmd-v` wkleić.
> * FireFox/IE może nie obsługiwać prawidłowo uprawnień schowka.

2) Wybierz i skopiuj dane wyjściowe klucza do schowka.

+ [GitHub](https://docs.github.com/github/authenticating-to-github/adding-a-new-ssh-key-to-your-github-account)

+ [GitLab](https://docs.gitlab.com/ee/ssh/#adding-an-ssh-key-to-your-gitlab-account)

+ [Azure DevOps](/azure/devops/repos/git/use-ssh-keys-to-authenticate#step-2--add-the-public-key-to-azure-devops-servicestfs)  Zacznij od **kroku 2.**

+ [BitBucket](https://support.atlassian.com/bitbucket-cloud/docs/set-up-an-ssh-key/#SetupanSSHkey-ssh2). Zacznij od **kroku 4.**

### <a name="clone-the-git-repository-with-ssh"></a>Klonowanie repozytorium Git przy użyciu SSH

1) Skopiuj adres URL klonowania usługi Git SSH z repozytorium Git.

2) Wklej adres URL w `git clone` poleceniu poniżej, aby użyć adresu URL repozytorium Git SSH. Będzie to wyglądać podobnie do:

```bash
git clone git@example.com:GitUser/azureml-example.git
Cloning into 'azureml-example'...
```

Pojawi się odpowiedź, na przykład:

```bash
The authenticity of host 'example.com (192.30.255.112)' can't be established.
RSA key fingerprint is SHA256:nThbg6kXUpJWGl7E1IGOCspRomTxdCARLviKw6E5SY8.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'github.com,192.30.255.112' (RSA) to the list of known hosts.
```

SSH może wyświetlić odcisk palca SSH serwera i poprosić o jego zweryfikowanie. Upewnij się, że wyświetlony odcisk palca jest taki, jak jeden z odcisków palców na stronie kluczy publicznych SSH.

SSH wyświetla ten odcisk palca podczas połączenia z nieznanym hostem w celu ochrony przed atakami typu [man-in-the-middle.](/previous-versions/windows/it-pro/windows-2000-server/cc959354(v=technet.10)) Po zaakceptowaniu odcisku palca hosta, SSH nie wyświetli ponownie monitu, chyba że odcisk palca zmieni się.

3) Gdy zostanie pytanie, czy chcesz kontynuować nawiązywanie połączenia, wpisz `yes` . Usługa Git sklonuje repozytorium i skonfiguruje zdalne źródło w celu nawiązania połączenia z SSH na przyszłe polecenia Git.

## <a name="track-code-that-comes-from-git-repositories"></a>Śledzenie kodu, który pochodzi z repozytoriów Git

Po przesłaniu uruchomienia szkoleniowego z zestawu SDK języka Python lub interfejsu wiersza Machine Learning wiersza polecenia pliki potrzebne do trenowania modelu są przekazywane do obszaru roboczego. Jeśli polecenie jest dostępne w środowisku dewelopera, proces przekazywania używa go do sprawdzenia, czy pliki są `git` przechowywane w repozytorium Git. Jeśli tak, informacje z repozytorium git również są przekazywane w ramach przebiegów trenowania. Te informacje są przechowywane w następujących właściwościach dla przebiegów trenowania:

| Właściwość | Polecenie git używane do uzyskania wartości | Opis |
| ----- | ----- | ----- |
| `azureml.git.repository_uri` | `git ls-remote --get-url` | Adres URI, z których zostało sklonowane repozytorium. |
| `mlflow.source.git.repoURL` | `git ls-remote --get-url` | Adres URI, z których zostało sklonowane repozytorium. |
| `azureml.git.branch` | `git symbolic-ref --short HEAD` | Gałąź aktywna podczas przesłania uruchomienia. |
| `mlflow.source.git.branch` | `git symbolic-ref --short HEAD` | Gałąź aktywna podczas przesłania uruchomienia. |
| `azureml.git.commit` | `git rev-parse HEAD` | Skrót zatwierdzenia kodu przesłanego dla uruchomienia. |
| `mlflow.source.git.commit` | `git rev-parse HEAD` | Skrót zatwierdzenia kodu przesłanego dla uruchomienia. |
| `azureml.git.dirty` | `git status --porcelain .` | `True`, jeśli gałąź/zatwierdzenie jest zanieczyszczone; w przeciwnym `false` razie . |

Te informacje są wysyłane dla przebiegów, które korzystają z estymatora, potoku uczenia maszynowego lub uruchomienia skryptu.

Jeśli pliki szkoleniowe nie znajdują się w repozytorium git w środowisku dewelopera lub polecenie nie jest dostępne, nie są śledzone żadne informacje związane z `git` usługą Git.

> [!TIP]
> Aby sprawdzić, czy polecenie git jest dostępne w środowisku dewelopera, otwórz sesję powłoki, wiersz polecenia, program PowerShell lub inny interfejs wiersza polecenia i wpisz następujące polecenie:
>
> ```
> git --version
> ```
>
> Jeśli program jest zainstalowany, a w ścieżce otrzymasz odpowiedź podobną do `git version 2.4.1` . Aby uzyskać więcej informacji na temat instalowania narzędzia git w środowisku projektowym, zobacz [witrynę internetową usługi Git.](https://git-scm.com/)

## <a name="view-the-logged-information"></a>Wyświetlanie zarejestrowanych informacji

Informacje o usłudze Git są przechowywane we właściwościach dla uruchomienia trenowania. Te informacje można wyświetlić przy użyciu zestawu Azure Portal, zestawu SDK języka Python i interfejsu wiersza polecenia. 

### <a name="azure-portal"></a>Azure Portal

1. W portalu [studio wybierz](https://ml.azure.com)swój obszar roboczy.
1. Wybierz __pozycję Experiments__(Eksperymenty), a następnie wybierz jeden z eksperymentów.
1. Wybierz jeden z przebiegów z __kolumny NUMER__ URUCHOMIENIA.
1. Wybierz __pozycję Outputs + logs (Dane__ wyjściowe + dzienniki), a następnie rozwiń __dzienniki__ i __wpisy azureml.__ Wybierz link, który rozpoczyna się od __### \_ azure__.

Zarejestrowane informacje zawierają tekst podobny do następującego JSON:

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

Po przesłaniu uruchomienia trenowania [zwracany](/python/api/azureml-core/azureml.core.run%28class%29) jest obiekt Run. Atrybut `properties` tego obiektu zawiera zarejestrowane informacje git. Na przykład poniższy kod pobiera skrót zatwierdzenia:

```python
run.properties['azureml.git.commit']
```

### <a name="cli"></a>Interfejs wiersza polecenia

Polecenia `az ml run` interfejsu wiersza polecenia można użyć do pobrania właściwości z uruchomienia. Na przykład następujące polecenie zwraca właściwości dla ostatniego uruchomienia w eksperymencie o nazwie `train-on-amlcompute` :

```azurecli-interactive
az ml run list -e train-on-amlcompute --last 1 -w myworkspace -g myresourcegroup --query '[].properties'
```

Aby uzyskać więcej informacji, zobacz dokumentację [az ml run](/cli/azure/ml/run) reference.

## <a name="next-steps"></a>Następne kroki

* [Używanie docelowych obiektów obliczeniowych do trenowania modelu](how-to-set-up-training-targets.md)