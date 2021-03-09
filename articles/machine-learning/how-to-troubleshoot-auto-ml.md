---
title: Rozwiązywanie problemów dotyczących zautomatyzowanych eksperymentów ML
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak rozwiązywać problemy z zautomatyzowanymi eksperymentami w usłudze Machine Learning.
author: nibaccam
ms.author: nibaccam
ms.reviewer: nibaccam
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 03/08/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, automl, references_regions
ms.openlocfilehash: b66c768011c05e1105f1351ebe4ed7c3c9700b70
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102519167"
---
# <a name="troubleshoot-automated-ml-experiments-in-python"></a>Rozwiązywanie problemów dotyczących zautomatyzowanych eksperymentów ML w języku Python

W tym przewodniku dowiesz się, jak identyfikować i rozwiązywać znane problemy w zautomatyzowanym doświadczeniu uczenia maszynowego za pomocą [zestawu SDK Azure Machine Learning](/python/api/overview/azure/ml/intro).

## <a name="version-dependencies"></a>Zależności wersji

**`AutoML` zależności w przypadku nowszych wersji pakietu powoduje przerwanie zgodności**. Po 1.13.0 wersji zestawu SDK modele nie są ładowane w starszych zestawach SDK ze względu na niezgodność między starszymi wersjami przypiętymi w poprzednich `AutoML` pakietach, a nowsze wersje są przypięte dzisiaj.

Oczekiwano błędów takich jak:

* Nie odnaleziono modułu z błędami takimi jak,

  `No module named 'sklearn.decomposition._truncated_svd`

* Błędy importowania, takie jak,

  `ImportError: cannot import name 'RollingOriginValidator'`,
* Błędy atrybutów, takie jak,

  `AttributeError: 'SimpleImputer' object has no attribute 'add_indicator`

Rozwiązania zależą od `AutoML` wersji szkoleniowej dotyczącej zestawu SDK:

* Jeśli `AutoML` wersja szkoleniowa zestawu SDK jest większa niż 1.13.0, potrzebujesz `pandas == 0.25.1` i `scikit-learn==0.22.1` .

    * Jeśli występuje niezgodność wersji, Uaktualnij scikit-uczenie i/lub Pandas, aby poprawić wersję z następującymi,

      ```bash
          pip install --upgrade pandas==0.25.1
          pip install --upgrade scikit-learn==0.22.1
      ```

* Jeśli `AutoML` wersja szkoleniowa zestawu SDK jest mniejsza lub równa 1.12.0, potrzebne są `pandas == 0.23.4` i `sckit-learn==0.20.3` .
  * W przypadku niezgodności wersji, obniżenia poziomu scikit — uczenie i/lub Pandas w celu poprawienia wersji, należy wykonać następujące czynności:
  
    ```bash
      pip install --upgrade pandas==0.23.4
      pip install --upgrade scikit-learn==0.20.3
    ```

## <a name="setup"></a>Konfigurowanie

`AutoML` zmiany pakietu od momentu, gdy wersja 1.0.76 wymaga odinstalowania poprzedniej wersji przed zaktualizowaniem do nowej wersji.

* **`ImportError: cannot import name AutoMLConfig`**

    Jeśli ten błąd wystąpi po uaktualnieniu z wersji zestawu SDK przed v 1.0.76 do v 1.0.76 lub nowszej, usuń błąd, uruchamiając polecenie: `pip uninstall azureml-train automl` , a następnie `pip install azureml-train-automl` . Skrypt automl_setup. cmd robi to automatycznie.

* **Niepowodzenie automl_setup**

  * W systemie Windows uruchom automl_setup z poziomu wiersza polecenia Anaconda. [Zainstaluj Miniconda](https://docs.conda.io/en/latest/miniconda.html).

  * Upewnij się, że zainstalowano wersję Conda 64-bitową 4.4.10 lub nowszą. Bit można sprawdzić za pomocą `conda info` polecenia. `platform`Powinien być `win-64` dla systemu Windows lub `osx-64` dla komputerów Mac. Aby sprawdzić wersję, użyj polecenia `conda -V` . Jeśli masz zainstalowaną poprzednią wersję, możesz ją zaktualizować za pomocą polecenia: `conda update conda` . Aby sprawdzić 32-bitowe przez uruchomienie 

  * Upewnij się, że zainstalowano Conda. 

  * System `gcc: error trying to exec 'cc1plus'`

    1. Jeśli wystąpi `gcc: error trying to exec 'cc1plus': execvp: No such file or directory` błąd, zainstaluj narzędzia kompilacji w wersji zabudowanej dla dystrybucji systemu Linux. Na przykład w Ubuntu Użyj polecenia `sudo apt-get install build-essential` .

    1. Przekaż nową nazwę jako pierwszy parametr do automl_setup, aby utworzyć nowe środowisko Conda. Wyświetlanie istniejących środowisk Conda `conda env list` i usuwanie ich z programu `conda env remove -n <environmentname>` .

* **automl_setup_linux. sh nie powiodło się**: Jeśli automl_setup_linus. sh kończy się niepowodzeniem na Ubuntu Linux z powodu błędu: `unable to execute 'gcc': No such file or directory`

  1. Upewnij się, że porty wychodzące 53 i 80 są włączone. Na maszynie wirtualnej platformy Azure możesz wykonać tę czynność z poziomu Azure Portal, wybierając maszynę wirtualną, a następnie klikając pozycję **Sieć**.
  1. Uruchom polecenie: `sudo apt-get update`
  1. Uruchom polecenie: `sudo apt-get install build-essential --fix-missing`
  1. Uruchom `automl_setup_linux.sh` ponownie

* **Konfiguracja. ipynb kończy się niepowodzeniem**:

  * W przypadku lokalnego Conda upewnij się, że program `automl_setup` został pomyślnie uruchomiony.
  * Upewnij się, że subscription_ida jest poprawna. Znajdź subscription_id w Azure Portal, wybierając pozycję Wszystkie usługi, a następnie pozycję subskrypcje. Znaki "<" i ">" nie powinny być uwzględnione w wartości subscription_id. Na przykład `subscription_id = "12345678-90ab-1234-5678-1234567890abcd"` ma prawidłowy format.
  * Upewnij się, że współautor lub właściciel ma dostęp do subskrypcji.
  * Sprawdź, czy region jest jednym z obsługiwanych regionów:,,,,,, `eastus2` `eastus` `westcentralus` `southeastasia` `westeurope` `australiaeast` `westus2` , `southcentralus` .
  * Zapewnij dostęp do regionu przy użyciu Azure Portal.
  
* **Niepowodzenie Workspace.from_config**:

  Jeśli wywołanie `ws = Workspace.from_config()` nie powiedzie się:

  1. Upewnij się, że Notes Configuration. ipynb został uruchomiony pomyślnie.
  1. Jeśli Notes jest uruchamiany z folderu, który nie znajduje się w folderze, w którym `configuration.ipynb` został uruchomiony, skopiuj folder aml_config a plik config.js, który zawiera do nowego folderu. Workspace.from_config odczytuje config.jsna potrzeby folderu notesu lub jego folderu nadrzędnego.
  1. Jeśli jest używana nowa subskrypcja, Grupa zasobów, obszar roboczy lub region, pamiętaj, aby `configuration.ipynb` ponownie uruchomić Notes. Zmiana config.jsna bezpośrednio będzie działała tylko wtedy, gdy obszar roboczy już istnieje w grupie zasobów określonej w ramach określonej subskrypcji.
  1. Jeśli chcesz zmienić region, Zmień obszar roboczy, grupę zasobów lub subskrypcję. `Workspace.create` Program nie utworzy ani nie zaktualizuje obszaru roboczego, jeśli już istnieje, nawet jeśli określony region jest inny.

## <a name="tensorflow"></a>TensorFlow

Począwszy od wersji 1.5.0 zestawu SDK, automatyczne Uczenie maszynowe nie instaluje modeli TensorFlow domyślnie. Aby zainstalować TensorFlow i używać go z zautomatyzowanymi eksperymentami ML, zainstaluj `tensorflow==1.12.0` za pośrednictwem programu `CondaDependencies` .

```python
  from azureml.core.runconfig import RunConfiguration
  from azureml.core.conda_dependencies import CondaDependencies
  run_config = RunConfiguration()
  run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['tensorflow==1.12.0'])
```

## <a name="numpy-failures"></a>Błędy numpy

* **`import numpy` Niepowodzenie w systemie Windows**: niektóre środowiska systemu Windows Zapoznaj się z błędem ładowania numpy z najnowszą wersją języka Python 3.6.8. Jeśli widzisz ten problem, Wypróbuj wersję Python 3.6.7.

* **`import numpy` Niepowodzenie**: Sprawdź wersję TensorFlow w środowisku zautomatyzowanej Conda ml. Obsługiwane wersje to < 1,13. Odinstaluj TensorFlow ze środowiska, jeśli wersja jest >= 1,13.

Wersję TensorFlow i Odinstaluj można sprawdzić w następujący sposób:

  1. Uruchom powłokę poleceń, Aktywuj środowisko Conda, w którym są zainstalowane zautomatyzowane pakiety ml.
  1. Wprowadź `pip freeze` i Wyszukaj `tensorflow` , jeśli znaleziono, wyświetlana wersja powinna być < 1,13
  1. Jeśli wyświetlana wersja nie jest obsługiwaną wersją, `pip uninstall tensorflow` w powłoce poleceń i wprowadź y w celu potwierdzenia.

## `jwt.exceptions.DecodeError`

Dokładny komunikat o błędzie: `jwt.exceptions.DecodeError: It is required that you pass in a value for the "algorithms" argument when calling decode()` .

W przypadku wersji zestawu SDK <= 1.17.0 instalacja może spowodować nieobsługiwaną wersję PyJWT. Sprawdź, czy wersja PyJWT w środowisku zautomatyzowanej sieci Conda jest obsługiwana. To jest wersja PyJWT < 2.0.0.

Wersję PyJWT można sprawdzić w następujący sposób:

1. Uruchom powłokę poleceń i aktywuj środowisko Conda, w którym są zainstalowane zautomatyzowane pakiety ML.

1. Wprowadź `pip freeze` i Wyszukaj `PyJWT` , jeśli znaleziono, wyświetlana wersja powinna być < 2.0.0

Jeśli wyświetlana wersja nie jest obsługiwaną wersją:

1. Rozważ uaktualnienie do najnowszej wersji zestawu SDK programu AutoML: `pip install -U azureml-sdk[automl]`

1. Jeśli to nie jest możliwe, Odinstaluj PyJWT ze środowiska i zainstaluj odpowiednią wersję w następujący sposób:

    1. `pip uninstall PyJWT` w powłoce poleceń i wprowadź `y` potwierdzenie.
    1. Zainstaluj przy użyciu `pip install 'PyJWT<2.0.0'` .
  
## <a name="databricks"></a>Databricks
Zapoznaj się z tematem [Konfigurowanie zautomatyzowanego eksperymentu ml z kostkami](how-to-configure-databricks-automl-environment.md#troubleshooting).

## <a name="forecasting-r2-score-is-always-zero"></a>Ocena prognozy R2 jest zawsze zerowa

 Ten problem występuje, jeśli podane dane szkoleniowe mają szeregi czasowe, które zawierają tę samą wartość dla ostatnich `n_cv_splits`  +  `forecasting_horizon` punktów danych.

Jeśli ten wzorzec jest oczekiwany w szeregach czasowych, można przełączyć podstawową metrykę na **znormalizowany błąd średnika głównego**.

## <a name="failed-deployment"></a>Niepowodzenie wdrożenia

 W przypadku wersji <= 1.18.0 zestawu SDK obraz podstawowy utworzony na potrzeby wdrożenia może zakończyć się niepowodzeniem z powodu następującego błędu: `ImportError: cannot import name cached_property from werkzeug` .

  Następujące kroki mogą obejść problem:

  1. Pobierz pakiet modelu
  1. Rozpakuj pakiet
  1. Wdrażanie przy użyciu niespakowanych zasobów

## <a name="sample-notebook-failures"></a>Błędy notesu przykładowego

 Jeśli przykładowy Notes kończy się niepowodzeniem z powodu błędu, że właściwość, metoda lub biblioteka nie istnieje:

* Upewnij się, że wybrano poprawne jądro w Jupyter Notebook. Jądro jest wyświetlane w prawym górnym rogu strony Notes. Wartość domyślna to *azure_automl*. Jądro jest zapisywane jako część notesu. Jeśli przełączysz się do nowego środowiska Conda, musisz wybrać nowe jądro w notesie.

  * W przypadku Azure Notebooks powinna być to Python 3,6.
  * W przypadku lokalnych środowisk Conda powinna to być nazwa środowiska Conda określona w automl_setup.

* Aby zapewnić, że Notes dotyczy używanej wersji zestawu SDK,
  * Sprawdź wersję zestawu SDK, wykonując `azureml.core.VERSION` w komórce Jupyter Notebook.
  * Możesz pobrać poprzednią wersję przykładowych notesów z usługi GitHub, wykonując następujące czynności:
    1. Wybierz `Branch` przycisk
    1. Przejdź do `Tags` karty
    1. Wybierz wersję

## <a name="next-steps"></a>Następne kroki

+ Dowiedz się więcej o tym, [Jak szkolić model regresji z automatycznym uczeniem maszynowym](tutorial-auto-train-models.md) lub [jak korzystać z funkcji automatycznego uczenia maszynowego w ramach zasobu zdalnego](how-to-auto-train-remote.md).

+ Dowiedz się więcej o tym [, jak i gdzie wdrożyć model](how-to-deploy-and-where.md).