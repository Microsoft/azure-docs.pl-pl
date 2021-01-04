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
ms.openlocfilehash: b452c24b4b2ed6021910f267b9941f3829acccd8
ms.sourcegitcommit: a89a517622a3886b3a44ed42839d41a301c786e0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/22/2020
ms.locfileid: "97733336"
---
# <a name="troubleshoot-environment-image-builds"></a>Rozwiązywanie problemów z kompilacjami obrazu środowiska
Dowiedz się, jak rozwiązywać problemy z kompilacjami obrazów środowiska Docker i instalacjami pakietów.

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree).
* [Zestaw SDK Azure Machine Learning](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py).
* [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest).
* [Rozszerzenie interfejsu wiersza polecenia dla Azure Machine Learning](reference-azure-machine-learning-cli.md).
* Aby debugować lokalnie, musisz mieć działającą instalację platformy Docker w systemie lokalnym.

## <a name="docker-image-build-failures"></a>Błędy kompilacji obrazu platformy Docker
 
W przypadku większości błędów kompilacji obrazu główną przyczynę można znaleźć w dzienniku kompilacji obrazu.
Dziennik kompilacji obrazu można znaleźć w portalu Azure Machine Learning (20 \_ \_log.txt kompilacji obrazu \_ ) lub z dzienników uruchamiania zadań ACR
 
W większości przypadków łatwiejsze jest odtwarzanie błędów lokalnie. Sprawdź rodzaj błędu i spróbuj wykonać jedną z następujących czynności `setuptools` :

- Zainstaluj lokalnie zależność Conda `conda install suspicious-dependency==X.Y.Z`
- Lokalnie Zainstaluj zależność PIP `pip install suspicious-dependency==X.Y.Z`
- Spróbuj zmaterializowania całe środowisko `conda create -f conda-specification.yml`

> [!IMPORTANT]
> Upewnij się, że platforma i interpretery na lokalnym obliczeniu są zgodne z tymi na urządzeniu zdalnym. 

### <a name="timeout"></a>Limit czasu 
 
Mogą wystąpić problemy z przekroczeniem limitu czasu dla różnych problemów z siecią:
- Niska przepustowość internetowa
- Problemy z serwerem
- Duża zależność, której nie można pobrać przy użyciu danego ustawienia limitu czasu Conda lub PIP
 
Komunikaty podobne do poniższych wskazują na problem:
 
```
('Connection broken: OSError("(104, \'ECONNRESET\')")', OSError("(104, 'ECONNRESET')"))
```
```
ReadTimeoutError("HTTPSConnectionPool(host='****', port=443): Read timed out. (read timeout=15)",)
```

Możliwe rozwiązania:
 
- Wypróbuj inne źródło dla zależności, jeśli są dostępne, takie jak duplikaty, Magazyn obiektów blob lub inne źródła języka Python.
- Aktualizowanie Conda lub PIP. Jeśli jest używany niestandardowy plik platformy Docker, zaktualizuj ustawienia limitu czasu.
- Niektóre wersje PIP mają znane problemy. Rozważ dodanie określonej wersji programu PIP do zależności środowiska.

### <a name="package-not-found"></a>Nie znaleziono pakietu

Jest to najbardziej typowy przypadek niepowodzeń kompilacji obrazu.

- Nie można znaleźć pakietu Conda
        ```
        ResolvePackageNotFound: 
          - not-existing-conda-package
        ```

- Nie można znaleźć określonego pakietu lub wersji PIP
        ```
        ERROR: Could not find a version that satisfies the requirement invalid-pip-package (from versions: none)
        ERROR: No matching distribution found for invalid-pip-package
        ```

- Zła zagnieżdżona zależność PIP
        ```
        ERROR: No matching distribution found for bad-backage==0.0 (from good-package==1.0)
        ```

Sprawdź, czy pakiet istnieje w określonych źródłach. Użyj [wyszukiwania PIP](https://pip.pypa.io/en/stable/reference/pip_search/) , aby sprawdzić zależności PIP.

`pip search azureml-core`

W przypadku zależności Conda Użyj [wyszukiwania Conda](https://docs.conda.io/projects/conda/en/latest/commands/search.html).

`conda search conda-forge::numpy`

Aby uzyskać więcej opcji:
- `pip search -h`
- `conda search -h`

#### <a name="installer-notes"></a>Uwagi dotyczące Instalatora

Upewnij się, że wymagana dystrybucja istnieje dla określonej platformy i wersji interpretera języka Python.

W przypadku zależności PIP przejdź do, aby `https://pypi.org/project/[PROJECT NAME]/[VERSION]/#files` sprawdzić, czy jest dostępna wersja wymagana. Na przykład https://pypi.org/project/azureml-core/1.11.0/#files

W przypadku zależności Conda Sprawdź pakiet w repozytorium kanałów.
W przypadku kanałów obsługiwanych przez Anaconda, Inc., zaznacz [tutaj](https://repo.anaconda.com/pkgs/).

### <a name="pip-package-update"></a>Aktualizacja pakietu PIP

Podczas instalacji lub aktualizacji pakietu PIP może być konieczne zaktualizowanie zainstalowanego pakietu przez program rozpoznawania w celu spełnienia nowych wymagań.
Dezinstalacja może się nie powieść z różnych powodów dotyczących wersji PIP lub sposobu instalacji zależności.
Typowy scenariusz polega na tym, że zależność zainstalowana przez Conda nie mogła zostać odinstalowana przez narzędzie PIP.
W tym scenariuszu Rozważ odinstalowanie zależności przy użyciu `conda remove mypackage` .

```
  Attempting uninstall: mypackage
    Found existing installation: mypackage X.Y.Z
ERROR: Cannot uninstall 'mypackage'. It is a distutils installed project and thus we cannot accurately determine which files belong to it which would lead to only a partial uninstall.
``` 
### <a name="installer-issues"></a>Problemy z instalatorem

Niektóre wersje Instalatora mają problemy w rozpoznawaniu pakietów, które mogą prowadzić do niepowodzenia kompilacji.

Jeśli jest używany niestandardowy obraz podstawowy lub pliku dockerfile, zalecamy użycie Conda w wersji 4.5.4 lub nowszej.

Pakiet PIP jest wymagany do zainstalowania zależności PIP i jeśli w środowisku nie zostanie określona wersja, zostanie użyta Najnowsza wersja.
Zalecamy używanie znanej wersji programu PIP, aby uniknąć przejściowych problemów lub zmiany, które mogą być spowodowane najnowszą wersją tego narzędzia.

Rozważ Przypinanie wersji PIP w środowisku, Jeśli zobaczysz dowolny z poniższych komunikatów:

`Warning: you have pip-installed dependencies in your environment file, but you do not list pip itself as one of your conda dependencies. Conda may not use the correct pip to install your packages, and they may end up in the wrong place. Please add an explicit pip dependency. I'm adding one for you, but still nagging you.`

`Pip subprocess error:
ERROR: THESE PACKAGES DO NOT MATCH THE HASHES FROM THE REQUIREMENTS FILE. If you have updated the package versions, update the hashes as well. Otherwise, examine the package contents carefully; someone may have tampered with them.`

Ponadto instalacja PIP może być zablokowana w pętli nieskończonej, jeśli występują konflikty nierozpoznawalne w zależnościach. Jeśli pracujesz lokalnie, Obniż wersję oprogramowania PIP do < 20,3. W środowisku Conda utworzonym na podstawie pliku YAML ten problem będzie widoczny tylko w przypadku, gdy Conda-fałszowanie ma najwyższy priorytet. Aby wyeliminować problem, jawnie określ wartość PIP < 20,3 (! = 20,3 lub = 20.2.4 pin do innej wersji) jako zależność Conda w pliku specyfikacji Conda.

## <a name="service-side-failures"></a>Błędy po stronie usługi

### <a name="unable-to-pull-image-from-mcraddress-could-not-be-resolved-for-container-registry"></a>Nie można rozpoznać obrazu z MCR/Address dla Container Registry.
Możliwe problemy:
- Nazwa ścieżki do rejestru kontenerów może nie być rozpoznawana poprawnie. Sprawdź, czy nazwy obrazów używają podwójnych ukośników, a kierunek ukośników na hostach z systemem Windows i Linux są poprawne.
- Jeśli ACR za siecią wirtualną używa prywatnego punktu końcowego w [nieobsługiwanym regionie](https://docs.microsoft.com/azure/private-link/private-link-overview#availability), skonfiguruj ACR za siecią wirtualną przy użyciu punktu końcowego usługi (dostęp publiczny) z portalu i ponów próbę.
- Po wprowadzeniu ACR za siecią wirtualną upewnij się, że [szablon ARM](https://docs.microsoft.com/azure/machine-learning/how-to-enable-virtual-network#azure-container-registry) jest uruchomiony. Dzięki temu obszar roboczy może komunikować się z wystąpieniem ACR.

### <a name="401-error-from-workspace-acr"></a>Błąd 401 z obszaru roboczego ACR
Synchronizuj ponownie klucze magazynu przy użyciu [WS.sync_keys ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#sync-keys--)

### <a name="environment-keeps-throwing-waiting-for-other-conda-operations-to-finish-error"></a>Środowisko zachowuje "Oczekiwanie na zakończenie innych operacji Conda..." Porn
Gdy kompilacja obrazu jest ciągła, Conda jest zablokowany przez klienta zestawu SDK. Jeśli proces uległ awarii lub został niepoprawnie anulowany przez użytkownika-Conda pozostaje w stanie zablokowanym. Aby rozwiązać ten problem, należy ręcznie usunąć plik blokady. 

### <a name="custom-docker-image-not-in-registry"></a>Niestandardowy obraz platformy Docker nie jest w rejestrze
Sprawdź, czy jest używany [poprawny tag](https://docs.microsoft.com/azure/machine-learning/how-to-use-environments#create-an-environment) `user_managed_dependencies = True` . `Environment.python.user_managed_dependencies = True` wyłącza Conda i używa zainstalowanych pakietów użytkownika.

### <a name="common-vnet-issues"></a>Typowe problemy z siecią wirtualną

1. Sprawdź, czy konto magazynu, klaster obliczeniowy i Azure Container Registry znajdują się w tej samej podsieci sieci wirtualnej.
2. Gdy ACR znajduje się za siecią wirtualną, nie można go bezpośrednio użyć do kompilowania obrazów. Klaster obliczeniowy musi być używany do kompilowania obrazów.
3. Magazyn może być umieszczony za siecią wirtualną, gdy:
    - Używanie koła inferencing lub prywatnego
    - Wyświetlanie błędów usługi 403 (nieautoryzowanych)
    - Nie można pobrać szczegółów obrazu z ACR/MCR

### <a name="image-build-fails-when-trying-to-access-network-protected-storage"></a>Kompilacja obrazu kończy się niepowodzeniem podczas próby uzyskania dostępu do chronionego magazynu sieciowego
- Zadania ACR nie działają za siecią wirtualną. Jeśli użytkownik ma swoją ACR za siecią wirtualną, musi użyć klastra obliczeniowego do skompilowania obrazu.
- Magazyn powinien znajdować się za siecią wirtualną, aby można było ściągnąć z niego zależności. 

### <a name="cannot-run-experiments-when-storage-has-network-security-enabled"></a>Nie można uruchomić eksperymentów, gdy w magazynie włączono zabezpieczenia sieciowe
W przypadku korzystania z domyślnych obrazów platformy Docker i włączania zależności zarządzanych przez użytkownika należy użyć [tagów usługi](https://docs.microsoft.com/azure/machine-learning/how-to-enable-virtual-network) MicrosoftContainerRegistry i AzureFrontDoor. FirstParty, aby dozwolonych MCR i jego zależności.

 Aby uzyskać więcej informacji, zobacz [Włączanie sieci wirtualnej](https://docs.microsoft.com/azure/machine-learning/how-to-enable-virtual-network#azure-container-registry) .

### <a name="creating-an-icm"></a>Tworzenie obrazu ICM

Podczas tworzenia/przypisywania ICM do magazynu metadanych należy uwzględnić bilet pomocy technicznej CSS, aby lepiej zrozumieć problem.

## <a name="next-steps"></a>Następne kroki

- [Uczenie modelu uczenia maszynowego do kategoryzacji kwiatów](how-to-train-scikit-learn.md)
- [Uczenie modelu uczenia maszynowego przy użyciu niestandardowego obrazu platformy Docker](how-to-train-with-custom-image.md)