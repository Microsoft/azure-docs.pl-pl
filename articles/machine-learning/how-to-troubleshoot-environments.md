---
title: Rozwiązywanie problemów z obrazami środowiska
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak rozwiązywać problemy z kompilacjami obrazu środowiska i instalacjami pakietów.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: saachigopal
ms.author: sagopal
ms.date: 12/3/2020
ms.topic: troubleshooting
ms.custom: devx-track-python
ms.openlocfilehash: da3e5bd9efcdd7d82dd3d3937918c4a64f69edbd
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102215650"
---
# <a name="troubleshoot-environment-image-builds"></a>Rozwiązywanie problemów z kompilacjami obrazu środowiska

Dowiedz się, jak rozwiązywać problemy z kompilacjami obrazów środowiska Docker i instalacjami pakietów.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree).
* [Zestaw SDK Azure Machine Learning](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py).
* [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).
* [Rozszerzenie interfejsu wiersza polecenia dla Azure Machine Learning](reference-azure-machine-learning-cli.md).
* Aby debugować lokalnie, musisz mieć działającą instalację platformy Docker w systemie lokalnym.

## <a name="docker-image-build-failures"></a>Błędy kompilacji obrazu platformy Docker
 
W przypadku większości błędów kompilacji obrazu można znaleźć główną przyczynę w dzienniku kompilacji obrazu.
Znajdź dziennik kompilacji obrazu z portalu Azure Machine Learning (20 \_ \_log.txt kompilacji obrazu \_ ) lub z dzienników uruchamiania zadania Azure Container Registry.
 
Zwykle łatwiej jest odtworzyć błędy lokalnie. Sprawdź rodzaj błędu i spróbuj wykonać jedną z następujących czynności `setuptools` :

- Zainstaluj zależność Conda lokalnie: `conda install suspicious-dependency==X.Y.Z` .
- Lokalnie Zainstaluj zależność PIP: `pip install suspicious-dependency==X.Y.Z` .
- Spróbuj zmaterializowania całe środowisko: `conda create -f conda-specification.yml` .

> [!IMPORTANT]
> Upewnij się, że platforma i interpreter w lokalnym klastrze obliczeniowym są zgodne z tymi w zdalnym klastrze obliczeniowym. 

### <a name="timeout"></a>Limit czasu 
 
Następujące problemy z siecią mogą powodować błędy limitu czasu:

- Niska przepustowość internetowa
- Problemy z serwerem
- Duże zależności, których nie można pobrać przy użyciu danego ustawienia limitu czasu Conda lub PIP
 
Komunikaty podobne do następujących przykładów wskazują na problem:
 
```
('Connection broken: OSError("(104, \'ECONNRESET\')")', OSError("(104, 'ECONNRESET')"))
```
```
ReadTimeoutError("HTTPSConnectionPool(host='****', port=443): Read timed out. (read timeout=15)",)
```

Jeśli zostanie wyświetlony komunikat o błędzie, Wypróbuj jedno z następujących możliwych rozwiązań:
 
- Dla zależności spróbuj użyć innego źródła, takiego jak duplikaty, platforma Azure Blob Storage lub inne źródła danych języka Python.
- Aktualizowanie Conda lub PIP. Jeśli używasz niestandardowego pliku platformy Docker, zaktualizuj ustawienia limitu czasu.
- Niektóre wersje PIP mają znane problemy. Rozważ dodanie określonej wersji programu PIP do zależności środowiska.

### <a name="package-not-found"></a>Nie znaleziono pakietu

Następujące błędy są najczęstsze dla błędów kompilacji obrazu:

- Nie można znaleźć pakietu Conda:

   ```
   ResolvePackageNotFound: 
   - not-existing-conda-package
   ```

- Nie można znaleźć określonego pakietu lub wersji PIP:

   ```
   ERROR: Could not find a version that satisfies the requirement invalid-pip-package (from versions: none)
   ERROR: No matching distribution found for invalid-pip-package
   ```

- Zła zagnieżdżona zależność PIP:

   ```
   ERROR: No matching distribution found for bad-package==0.0 (from good-package==1.0)
   ```

Sprawdź, czy pakiet istnieje w określonych źródłach. Aby sprawdzić zależności PIP, użyj [wyszukiwania PIP](https://pip.pypa.io/en/stable/reference/pip_search/) :

- `pip search azureml-core`

W przypadku zależności Conda Użyj [wyszukiwania Conda](https://docs.conda.io/projects/conda/en/latest/commands/search.html):

- `conda search conda-forge::numpy`

Aby uzyskać więcej opcji, wypróbuj:
- `pip search -h`
- `conda search -h`

#### <a name="installer-notes"></a>Uwagi dotyczące Instalatora

Upewnij się, że wymagana dystrybucja istnieje dla określonej platformy i wersji interpretera języka Python.

W przypadku zależności PIP, przejdź do, `https://pypi.org/project/[PROJECT NAME]/[VERSION]/#files` Aby sprawdzić, czy wymagana wersja jest dostępna. Przejdź do, https://pypi.org/project/azureml-core/1.11.0/#files Aby zobaczyć przykład.

W przypadku zależności Conda Sprawdź pakiet w repozytorium kanałów.
W przypadku kanałów obsługiwanych przez Anaconda, Inc. Sprawdź [stronę pakiety Anaconda](https://repo.anaconda.com/pkgs/).

### <a name="pip-package-update"></a>Aktualizacja pakietu PIP

Podczas instalacji lub aktualizacji pakietu PIP może być konieczne zaktualizowanie już zainstalowanego pakietu w celu spełnienia nowych wymagań.
Dezinstalacja może zakończyć się niepowodzeniem z różnych powodów dotyczących wersji PIP lub sposobu instalacji zależności.
Typowy scenariusz polega na tym, że nie można odinstalować zależności zainstalowanej przez Conda za pomocą narzędzia PIP.
W tym scenariuszu Rozważ odinstalowanie zależności przy użyciu polecenia `conda remove mypackage` .

```
  Attempting uninstall: mypackage
    Found existing installation: mypackage X.Y.Z
ERROR: Cannot uninstall 'mypackage'. It is a distutils installed project and thus we cannot accurately determine which files belong to it which would lead to only a partial uninstall.
``` 
### <a name="installer-issues"></a>Problemy z instalatorem

Niektóre wersje Instalatora mają problemy w rozpoznawaniu pakietów, które mogą prowadzić do niepowodzenia kompilacji.

Jeśli używasz niestandardowego obrazu podstawowego lub pliku dockerfile, zalecamy użycie Conda w wersji 4.5.4 lub nowszej.

Aby zainstalować zależności PIP, wymagany jest pakiet PIP. Jeśli w środowisku nie określono wersji, zostanie użyta Najnowsza wersja.
Zalecamy używanie znanej wersji programu PIP, aby uniknąć przejściowych problemów lub zmiany, które mogą spowodować Najnowsza wersja narzędzia.

Rozważ Przypinanie wersji PIP w środowisku, Jeśli zobaczysz następujący komunikat:

   ```
   Warning: you have pip-installed dependencies in your environment file, but you do not list pip itself as one of your conda dependencies. Conda may not use the correct pip to install your packages, and they may end up in the wrong place. Please add an explicit pip dependency. I'm adding one for you, but still nagging you.
   ```

Błąd podprocesu PIP:
   ```
   ERROR: THESE PACKAGES DO NOT MATCH THE HASHES FROM THE REQUIREMENTS FILE. If you have updated the package versions, update the hashes as well. Otherwise, examine the package contents carefully; someone may have tampered with them.
   ```

Instalację PIP można zapełnić w nieskończoną pętlę, jeśli występują konflikty nierozpoznawalne w zależnościach. Jeśli pracujesz lokalnie, Obniż wersję oprogramowania PIP do < 20,3. W środowisku Conda utworzonym na podstawie pliku YAML zobaczysz ten problem tylko wtedy, gdy Conda-fałszowanie jest kanałem o najwyższym priorytecie. Aby wyeliminować problem, jawnie określ wartość PIP < 20,3 (! = 20,3 lub = 20.2.4 pin do innej wersji) jako zależność Conda w pliku specyfikacji Conda.

## <a name="service-side-failures"></a>Błędy po stronie usługi

Zapoznaj się z następującymi scenariuszami, aby rozwiązać problemy z potencjalnymi awariami po stronie usług.

### <a name="youre-unable-to-pull-an-image-from-a-container-registry-or-the-address-couldnt-be-resolved-for-a-container-registry"></a>Nie można ściągnąć obrazu z rejestru kontenerów lub nie można rozpoznać adresu dla rejestru kontenerów

Możliwe problemy:
- Nazwa ścieżki do rejestru kontenerów może nie być rozpoznawana poprawnie. Sprawdź, czy nazwy obrazów używają podwójnych ukośników i kierunku ukośników w systemie Linux i na hostach z systemem Windows są poprawne.
- Jeśli rejestr kontenerów za siecią wirtualną używa prywatnego punktu końcowego w [nieobsługiwanym regionie](../private-link/private-link-overview.md#availability), skonfiguruj rejestr kontenerów za pomocą punktu końcowego usługi (dostęp publiczny) z portalu i ponów próbę.
- Po umieszczeniu rejestru kontenerów za siecią wirtualną, uruchom [szablon Azure Resource Manager](./how-to-network-security-overview.md) , aby obszar roboczy mógł komunikować się z wystąpieniem rejestru kontenerów.

### <a name="you-get-a-401-error-from-a-workspace-container-registry"></a>Wystąpił błąd 401 z rejestru kontenerów obszaru roboczego

Ponownie zsynchronizuj klucze magazynu za pomocą [WS.sync_keys ()](/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#sync-keys--).

### <a name="the-environment-keeps-throwing-a-waiting-for-other-conda-operations-to-finish-error"></a>Środowisko ciągle zgłasza "Oczekiwanie na zakończenie innych operacji Conda..." Porn

Gdy kompilacja obrazu jest ciągła, Conda jest zablokowany przez klienta zestawu SDK. Jeśli proces uległ awarii lub został niepoprawnie anulowany przez użytkownika, Conda pozostaje w stanie zablokowanym. Aby rozwiązać ten problem, należy ręcznie usunąć plik blokady. 

### <a name="your-custom-docker-image-isnt-in-the-registry"></a>Niestandardowy obraz platformy Docker nie znajduje się w rejestrze

Sprawdź, czy jest używany [poprawny tag](./how-to-use-environments.md#create-an-environment) `user_managed_dependencies = True` . `Environment.python.user_managed_dependencies = True` wyłącza Conda i używa zainstalowanych pakietów użytkownika.

### <a name="you-get-one-of-the-following-common-virtual-network-issues"></a>Zostanie wyświetlony jeden z następujących typowych problemów z siecią wirtualną

- Sprawdź, czy konto magazynu, klaster obliczeniowy i rejestr kontenerów znajdują się w tej samej podsieci sieci wirtualnej.
- Gdy rejestr kontenerów znajduje się za siecią wirtualną, nie może być bezpośrednio używany do kompilowania obrazów. Do kompilowania obrazów należy użyć klastra obliczeniowego.
- Może być konieczne przemieszczenie magazynu za siecią wirtualną, jeśli:
    - Użyj koła inferencing lub prywatnego.
    - Zobacz błędy usługi 403 (nieautoryzowane).
    - Nie można pobrać szczegółów obrazu z Azure Container Registry.

### <a name="the-image-build-fails-when-youre-trying-to-access-network-protected-storage"></a>Kompilacja obrazu kończy się niepowodzeniem w przypadku próby uzyskania dostępu do chronionego magazynu sieciowego

- Azure Container Registry zadania nie działają za siecią wirtualną. Jeśli użytkownik ma swój rejestr kontenerów za siecią wirtualną, musi użyć klastra obliczeniowego do skompilowania obrazu.
- Magazyn powinien znajdować się za siecią wirtualną, aby można było ściągnąć z niego zależności.

### <a name="you-cant-run-experiments-when-storage-has-network-security-enabled"></a>Nie można uruchomić eksperymentów, gdy w magazynie włączono zabezpieczenia sieciowe

Jeśli używasz domyślnych obrazów platformy Docker i włączasz zależności zarządzane przez użytkownika, użyj [tagów usługi](./how-to-network-security-overview.md) MicrosoftContainerRegistry i AzureFrontDoor. FirstParty, aby dozwolonych Azure Container Registry i jej zależności.

 Aby uzyskać więcej informacji, zobacz [Włączanie sieci wirtualnych](./how-to-network-security-overview.md).

### <a name="you-need-to-create-an-icm"></a>Należy utworzyć ICM

Podczas tworzenia/przypisywania ICM do magazynu metadanych należy uwzględnić bilet pomocy technicznej CSS, aby lepiej zrozumieć problem.

## <a name="next-steps"></a>Następne kroki

- [Uczenie modelu uczenia maszynowego do kategoryzacji kwiatów](how-to-train-scikit-learn.md)
- [Uczenie modelu uczenia maszynowego przy użyciu niestandardowego obrazu platformy Docker](how-to-train-with-custom-image.md)
