---
title: Konfigurowanie środowiska programistycznego języka Python
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak skonfigurować środowisko programistyczne dla usługi Azure Machine Learning. Używaj środowisk Conda, twórz pliki konfiguracyjne i konfiguruj własny serwer notebooków opartych na chmurze, notesy Jupyter, usługi Azure Databricks, środowiska ied, edytory kodu i maszynę wirtualną do nauki o danych.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.topic: conceptual
ms.date: 12/27/2019
ms.custom: seodec18
ms.openlocfilehash: 10ea0002b2e99c4675f56e48a638f3c1cb87e6c0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399007"
---
# <a name="configure-a-development-environment-for-azure-machine-learning"></a>Konfigurowanie środowiska programistycznego dla usługi Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym artykule dowiesz się, jak skonfigurować środowisko programistyczne do pracy z usługą Azure Machine Learning. Usługa Azure Machine Learning jest niezależna od platformy. Jedynym trudnym wymaganiem dla środowiska programistycznego jest Python 3. Zalecane jest również izolowane środowisko, takie jak Anaconda lub Virtualenv.

W poniższej tabeli przedstawiono każde środowisko programistyczne omówione w tym artykule, wraz z zaletami i wadami.

| Środowisko | Zalety | Wady |
| --- | --- | --- |
| [Wystąpienie obliczeniowe usługi Azure Machine Learning oparte na chmurze (wersja zapoznawcza)](#compute-instance) | Najprostszy sposób na rozpoczęcie pracy. Cały pakiet SDK jest już zainstalowany w maszynie wirtualnej obszaru roboczego, a samouczki notesu są wstępnie sklonowane i gotowe do uruchomienia. | Brak kontroli nad środowiskiem programistycznym i zależności. Dodatkowe koszty poniesione dla maszyny Wirtualnej systemu Linux (maszyna wirtualna może zostać zatrzymana, gdy nie jest używana, aby uniknąć opłat). Zobacz [szczegóły cen .](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) |
| [Środowisko lokalne](#local) | Pełna kontrola nad środowiskiem programistycznym i zależnościami. Uruchom za pomocą dowolnego narzędzia kompilacji, środowiska lub IDE do wyboru. | Trwa dłużej, aby rozpocząć. Niezbędne pakiety SDK muszą być zainstalowane, a środowisko musi być również zainstalowany, jeśli jeszcze go nie masz. |
| [Azure Databricks](#aml-databricks) | Idealne rozwiązanie do uruchamiania dużych intensywnych procesów uczenia maszynowego na skalowalnej platformie Apache Spark. | Przesada w eksperymentalnym uczeniu maszynowym lub eksperymentach i przepływach pracy na mniejszą skalę. Dodatkowe koszty poniesione dla usługi Azure Databricks. Zobacz [szczegóły cen .](https://azure.microsoft.com/pricing/details/databricks/) |
| [Maszyna wirtualna do nauki o danych (DSVM)](#dsvm) | Podobnie jak w przypadku wystąpienia obliczeniowego opartego na chmurze (Python i zestaw SDK są wstępnie zainstalowane), ale z dodatkowymi narzędziami do nauki o danych i uczenia maszynowego preinstalowanych. Łatwe skalowanie i łączenie z innymi niestandardowymi narzędziami i przepływami pracy. | Wolniejsze środowisko rozpoczynania pracy w porównaniu do wystąpienia obliczeniowego opartego na chmurze. |

W tym artykule przedstawiono również dodatkowe wskazówki dotyczące użycia następujących narzędzi:

* [Notebooki Jupyter](#jupyter): Jeśli używasz już notebooka Jupyter, zestaw SDK ma kilka dodatków, które należy zainstalować.

* [Kod programu Visual Studio:](#vscode)Jeśli używasz programu Visual Studio Code, [rozszerzenie usługi Azure Machine Learning](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai) zawiera rozbudowaną obsługę języka języka języka Python, a także funkcje, aby praca z usługą Azure Machine Learning była znacznie wygodniejsza i bardziej wydajna.

## <a name="prerequisites"></a>Wymagania wstępne

Obszar roboczy usługi Azure Machine Learning. Aby utworzyć obszar roboczy, zobacz [Tworzenie obszaru roboczego usługi Azure Machine Learning](how-to-manage-workspace.md). Obszar roboczy to wszystko, czego potrzebujesz, aby rozpocząć pracę z własnym [serwerem przenośnym opartym na chmurze,](#compute-instance) [dsvm](#dsvm)lub [azure databricks](#aml-databricks).

Aby zainstalować środowisko SDK dla [komputera lokalnego,](#local) [serwera notesu Jupyter](#jupyter) lub [kodu programu Visual Studio,](#vscode) potrzebujesz również:

- Menedżer pakietów [Anaconda](https://www.anaconda.com/download/) lub [Miniconda.](https://conda.io/miniconda.html)

- W systemie Linux lub macOS potrzebna jest powłoka bash.

    > [!TIP]
    > Jeśli korzystasz z Linuksa lub macOS i używasz powłoki innej niż bash (na przykład zsh), możesz pojawić się błędy po uruchomieniu niektórych poleceń. Aby obejść ten problem, użyj `bash` polecenia, aby uruchomić nową powłokę bash i uruchomić tam polecenia.

- W systemie Windows potrzebny jest wiersz polecenia lub monit Anaconda (zainstalowany przez Anaconda i Miniconda).

## <a name="your-own-cloud-based-compute-instance"></a><a id="compute-instance"></a>Własne wystąpienie obliczeniowe oparte na chmurze

Wystąpienie obliczeniowe usługi Azure Machine Learning [(wersja zapoznawcza)](concept-compute-instance.md) to bezpieczna, oparta na chmurze stacja robocza platformy Azure, która zapewnia analitykom danych serwer notebooka Jupyter, JupyterLab i w pełni przygotowane środowisko ml.

Nie ma nic do zainstalowania lub skonfigurowania dla wystąpienia obliczeniowego.  Utwórz go w dowolnym momencie z obszaru roboczego usługi Azure Machine Learning. Podaj tylko nazwę i określ typ maszyny Wirtualnej platformy Azure. Wypróbuj teraz z tego [samouczka: Środowisko instalacji i obszaru roboczego](tutorial-1st-experiment-sdk-setup.md).

Dowiedz się więcej o [wystąpieniach obliczeniowych](concept-compute-instance.md).

Aby zatrzymać poniesienie opłat obliczeniowych, [zatrzymaj wystąpienie obliczeniowe](tutorial-1st-experiment-sdk-train.md#clean-up-resources).

## <a name="data-science-virtual-machine"></a><a id="dsvm"></a>Maszyna wirtualna do analizy danych

DSVM jest obraz maszyny wirtualnej dostosowane. Jest przeznaczony do nauki o danych, który jest wstępnie skonfigurowany z:

  - Pakiety takie jak TensorFlow, PyTorch, Scikit-learn, XGBoost i Azure Machine Learning SDK
  - Popularne narzędzia do nauki o danych, takie jak Spark Standalone i Drill
  - Narzędzia platformy Azure, takie jak interfejsu wiersza polecenia platformy Azure, AzCopy i Eksplorator magazynu
  - Zintegrowane środowiska programistyczne (IDE), takie jak Visual Studio Code i PyCharm
  - Serwer notebooka Jupyter

Zestaw SDK usługi Azure Machine Learning działa w wersji systemu Ubuntu lub Windows systemu DSVM. Ale jeśli planujesz używać DSVM jako cel obliczeniowy, jak również, tylko Ubuntu jest obsługiwany.

Aby użyć DSVM jako środowiska programistycznego:

1. Utwórz dsvm w jednym z następujących środowisk:

    * Portal Azure:

        * [Tworzenie maszyny Data Science Virtual Machine z systemem Ubuntu](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)

        * [Tworzenie maszyny Data Science Virtual Machine z systemem Windows](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/provision-vm)

    * Narzędzie interfejsu wiersza polecenia platformy Azure:

        > [!IMPORTANT]
        > * Korzystając z interfejsu wiersza polecenia platformy Azure, należy najpierw `az login` zalogować się do subskrypcji platformy Azure za pomocą polecenia.
        >
        > * Korzystając z poleceń w tym kroku, należy podać nazwę grupy zasobów, nazwę maszyny Wirtualnej, nazwę użytkownika i hasło.

        * Aby utworzyć maszynę wirtualną nauki o danych Ubuntu, użyj następującego polecenia:

            ```azurecli-interactive
            # create a Ubuntu DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            # If you need to create a new resource group use: "az group create --name YOUR-RESOURCE-GROUP-NAME --location YOUR-REGION (For example: westus2)"
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:linux-data-science-vm-ubuntu:linuxdsvmubuntu:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --generate-ssh-keys --authentication-type password
            ```

        * Aby utworzyć maszynę wirtualną do nauki o danych w systemie Windows, należy użyć następującego polecenia:

            ```azurecli-interactive
            # create a Windows Server 2016 DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:dsvm-windows:server-2016:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --authentication-type password
            ```

2. Zestaw SDK usługi Azure Machine Learning jest już zainstalowany w systemie DSVM. Aby użyć środowiska Conda zawierającego sdk, należy użyć jednego z następujących poleceń:

    * Dla Ubuntu DSVM:

        ```bash
        conda activate py36
        ```

    * W przypadku systemu Windows DSVM:

        ```bash
        conda activate AzureML
        ```

1. Aby sprawdzić, czy można uzyskać dostęp do SDK i sprawdzić wersję, użyj następującego kodu Języka Python:

    ```python
    import azureml.core
    print(azureml.core.VERSION)
    ```

1. Aby skonfigurować dsvm do korzystania z obszaru roboczego usługi Azure Machine Learning, zobacz [Tworzenie pliku konfiguracji obszaru roboczego](#workspace) sekcji.

Aby uzyskać więcej informacji, zobacz [Maszyny wirtualne do nauki o danych](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/).

## <a name="local-computer"></a><a id="local"></a>Komputer lokalny

Podczas korzystania z komputera lokalnego (który może być również zdalną maszyną wirtualną), utwórz środowisko Anaconda i zainstaluj SDK. Oto przykład:

1. Pobierz i zainstaluj [Anaconda](https://www.anaconda.com/distribution/#download-section) (wersja Pythona 3.7), jeśli jeszcze jej nie masz.

1. Otwórz monit anakondy i utwórz środowisko z następującymi poleceniami:

    Uruchom następujące polecenie, aby utworzyć środowisko.

    ```bash
    conda create -n myenv python=3.6.5
    ```

    Następnie aktywuj środowisko.

    ```bash
    conda activate myenv
    ```

    W tym przykładzie tworzy środowisko przy użyciu python 3.6.5, ale można wybrać żadnych określonych wywrotek. Zgodność sdk może nie być gwarantowana w niektórych wersjach głównych (zalecane jest 3.5+ i zaleca się wypróbowanie innej wersji/wywrotu w środowisku Anaconda, jeśli napotkasz błędy. Utworzenie środowiska, podczas gdy składniki i pakiety będą pobierane, potrwa kilka minut.

1. Uruchom następujące polecenia w nowym środowisku, aby włączyć jądra IPython specyficzne dla środowiska. Zapewni to oczekiwane zachowanie importu jądra i pakietu podczas pracy z notebookami Jupyter w środowiskach Anaconda:

    ```bash
    conda install notebook ipykernel
    ```

    Następnie uruchom następujące polecenie, aby utworzyć jądro:

    ```bash
    ipython kernel install --user --name myenv --display-name "Python (myenv)"
    ```

1. Aby zainstalować pakiety, użyj następujących poleceń:

    To polecenie instaluje podstawowy zestaw SDK `automl` usługi Azure Machine Learning z notesem i dodatkami. Dodatek `automl` jest duża instalacja i mogą być usunięte z nawiasów, jeśli nie zamierzasz uruchamiać eksperymentów uczenia maszynowego zautomatyzowane. Dodatek `automl` obejmuje również zestaw SDK przygotowania danych usługi Azure Machine Learning domyślnie jako zależność.

    ```bash
    pip install azureml-sdk[notebooks,automl]
    ```

   > [!NOTE]
   > * Jeśli zostanie wyświetlony komunikat, którego nie można odinstalować pyyaml, użyj następującego polecenia:
   >
   >   `pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML`
   >
   > * Począwszy od macOS Catalina, zsh (Z shell) jest domyślną powłoką logowania i powłoką interaktywną. W zsh użyj następującego polecenia, które\\wyjmuje nawiasy z " ( ukośnik odwrotny):
   >
   >   `pip install --upgrade azureml-sdk\[notebooks,automl\]`

   Instalacja pakietu SDK potrwa kilka minut. Aby uzyskać więcej informacji na temat opcji instalacji, zobacz [podręcznik instalacji](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

1. Zainstaluj inne pakiety do eksperymentowania uczenia maszynowego.

    Użyj jednego z następujących poleceń * \<* i zastąp nowy pakiet>pakietem, który chcesz zainstalować. Instalowanie `conda install` pakietów za pośrednictwem wymaga, że pakiet jest częścią bieżących kanałów (nowe kanały mogą być dodawane w Anaconda Cloud).

    ```bash
    conda install <new package>
    ```

    Alternatywnie, można zainstalować `pip`pakiety za pośrednictwem .

    ```bash
    pip install <new package>
    ```

### <a name="jupyter-notebooks"></a><a id="jupyter"></a>Notesy programu Jupyter

Zeszyty Jupyter są częścią [projektu Jupyter](https://jupyter.org/). Zapewniają one interaktywne środowisko kodowania, w którym tworzysz dokumenty, które łączą kod na żywo z tekstem narracji i grafiką. Notesy Jupyter to również świetny sposób na udostępnianie wyników innym osobom, ponieważ można zapisać dane wyjściowe sekcji kodu w dokumencie. Notebooki Jupyter można zainstalować na różnych platformach.

Procedura w sekcji [Komputer lokalny](#local) instaluje składniki niezbędne do uruchamiania notesów Jupyter w środowisku Anaconda.

Aby włączyć te składniki w środowisku notebooka Jupyter:

1. Otwórz monit Anaconda i aktywuj swoje środowisko.

    ```bash
    conda activate myenv
    ```

1. [Sklonuj repozytorium GitHub](https://aka.ms/aml-notebooks) dla zestawu przykładowych notesów.

    ```bash
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Uruchom serwer notebooka Jupyter za pomocą następującego polecenia:

    ```bash
    jupyter notebook
    ```

1. Aby sprawdzić, czy program Jupyter Notebook może korzystać z zestawu SDK, utwórz **nowy** notes, wybierz pozycję **Python 3** jako jądro, a następnie uruchom następujące polecenie w komórce notesu:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. Jeśli wystąpią problemy z importowaniem `ModuleNotFoundError`modułów i otrzymujesz , upewnij się, że jądro Jupyter jest podłączone do właściwej ścieżki dla twojego środowiska, uruchamiając następujący kod w komórce notesu.

    ```python
    import sys
    sys.path
    ```

1. Aby skonfigurować notes Jupyter do korzystania z obszaru roboczego usługi Azure Machine Learning, przejdź do sekcji [Tworzenie pliku konfiguracji obszaru roboczego.](#workspace)

### <a name="visual-studio-code"></a><a id="vscode"></a>Visual Studio Code

Visual Studio Code to bardzo popularny edytor kodów między platformami, który obsługuje obszerny zestaw języków programowania i narzędzi za pośrednictwem rozszerzeń dostępnych w [portalu Visual Studio marketplace.](https://marketplace.visualstudio.com/vscode) [Rozszerzenie usługi Azure Machine Learning](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai) instaluje rozszerzenie języka [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python) do kodowania we wszystkich typach środowisk języka Python (wirtualny, Anaconda itp.). Ponadto zapewnia funkcje wygody do pracy z zasobami usługi Azure Machine Learning i uruchamiania eksperymentów usługi Azure Machine Learning bez opuszczania kodu programu Visual Studio.

Aby użyć programu Visual Studio Code do tworzenia:

1. Zainstaluj rozszerzenie usługi Azure Machine Learning dla programu Visual Studio Code, zobacz [Uczenie maszynowe platformy Azure](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai).

    Aby uzyskać więcej informacji, zobacz [Korzystanie z usługi Azure Machine Learning dla programu Visual Studio Code](tutorial-setup-vscode-extension.md).

1. Dowiedz się, jak używać programu Visual Studio Code dla dowolnego typu programowania języka Python, zobacz [Wprowadzenie do języka Python w programie VSCode.](https://code.visualstudio.com/docs/python/python-tutorial)

    - Aby wybrać środowisko SDK Python zawierające SDK, otwórz program VS Code, a następnie wybierz klawisze Ctrl+Shift+P (Linux i Windows) lub Command+Shift+P (Mac).
        - Zostanie otwarta __paleta poleceń.__

    - Wprowadź __Python: Wybierz interpreter__, a następnie wybierz odpowiednie środowisko

1. Aby sprawdzić, czy można użyć SDK, należy utworzyć nowy plik Języka Python (py), który zawiera następujący kod:

    ```python
    #%%
    import azureml.core
    azureml.core.VERSION
    ```
    Uruchom ten kod, klikając "Uruchom komórkę" CodeLens lub po prostu naciśnij shift-enter.
<a name="aml-databricks"></a>

## <a name="azure-databricks"></a>Azure Databricks
Usługa Azure Databricks to środowisko oparte na platformie Spark firmy Apache w chmurze platformy Azure. Zapewnia środowisko oparte na notebooku oparte na współpracy z klastrem obliczeniowym opartym na procesorze CPU lub GPU.

Jak usługa Azure Databricks współpracuje z usługą Azure Machine Learning:
+ Można przeszkolić model przy użyciu platformy Spark MLlib i wdrożyć model do ACI/AKS z poziomu usługi Azure Databricks.
+ Można również użyć [funkcji automatycznego uczenia maszynowego](concept-automated-ml.md) w specjalnym sdk usługi Azure ML z usługi Azure Databricks.
+ Usługi Azure Databricks można używać jako celu obliczeniowego z [potoku usługi Azure Machine Learning.](concept-ml-pipelines.md)

### <a name="set-up-your-databricks-cluster"></a>Konfigurowanie klastra Databricks

Tworzenie [klastra Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal). Niektóre ustawienia mają zastosowanie tylko wtedy, gdy instalujesz zestaw SDK dla automatycznego uczenia maszynowego w databricks.
**Utworzenie klastra zajmie kilka minut.**

Użyj następujących ustawień:

| Ustawienie |Informacje zawarte w tym artykule dotyczą| Wartość |
|----|---|---|
| Nazwa klastra |zawsze| twojazasomername |
| Środowisko uruchomieniowe usługi Databricks |zawsze|Czas działania 6.5 (scala 2.11, iskra 2.4.3) |
| Wersja języka Python |zawsze| 3 |
| Pracowników |zawsze| 2 lub wyższe |
| Typy maszyn wirtualnych węzła procesu roboczego <br>(określa maksymalny numer równoczesnych iteracji) |Zautomatyzowane uczenie maszynowe<br>Tylko| Preferowana maszyna wirtualna zoptymalizowana pod kątem pamięci |
| Włączanie skalowania automatycznego |Zautomatyzowane uczenie maszynowe<br>Tylko| Usuń zaznaczenie |

Poczekaj, aż klaster zostanie uruchomiony, zanim przejdzie dalej.

### <a name="install-the-correct-sdk-into-a-databricks-library"></a>Instalowanie właściwego pakietu SDK w bibliotece databricks
Po uruchomieniu klastra [utwórz bibliotekę,](https://docs.databricks.com/user-guide/libraries.html#create-a-library) aby dołączyć odpowiedni pakiet zestawu SDK usługi Azure Machine Learning do klastra.

1. Kliknij prawym przyciskiem myszy bieżący folder Obszaru roboczego, w którym chcesz przechowywać bibliotekę. Wybierz **pozycję Utwórz** > **bibliotekę**.

1. Wybierz **tylko jedną** opcję (żadna inna instalacja SDK nie jest obsługiwana)

   |Dodatki do&nbsp;&nbsp;pakietów SDK|Element źródłowy|Nazwa PyPi&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
   |----|---|---|
   |Dla Databricks| Prześlij pythonowe jajko lub PyPI | azureml-sdk[databricks]|
   |Dla Databricks -z-<br> zautomatyzowane funkcje ML| Prześlij pythonowe jajko lub PyPI | azureml-sdk[automl]|

   > [!Warning]
   > Nie można zainstalować żadnych innych dodatków SDK. Wybierz tylko jedną z powyższych opcji [databricks] lub [automl].

   * Nie należy wybierać opcji **Dołącz automatycznie do wszystkich klastrów**.
   * Wybierz **pozycję Dołącz** obok nazwy klastra.

1. Monitoruj, aby nie pojawiały się błędy, aż stan zmieni **się na Dołączono**, co może potrwać kilka minut.  Jeśli ten krok nie powiedzie się:

   Spróbuj ponownie uruchomić klaster, korzystając z:
   1. W lewym okienku wybierz pozycję **Klastry**.
   1. W tabeli wybierz nazwę klastra.
   1. Na karcie **Biblioteki** wybierz pozycję **Uruchom ponownie**.

   Należy również wziąć pod uwagę:
   + W konfiguracji AutoML podczas korzystania z usługi Azure Databricks dodaj następujące parametry:
       1. ```max_concurrent_iterations```zależy od liczby węzłów procesu roboczego w klastrze.
        2. ```spark_context=sc```opiera się na domyślnym kontekście iskrzącym.
   + Lub, jeśli masz starą wersję zestawu SDK, usuń zaznaczenie go z zainstalowanych libs klastra i przejdź do kosza. Zainstaluj nową wersję zestawu SDK i uruchom ponownie klaster. Jeśli wystąpi problem po ponownym uruchomieniu, odłącz i podłącz ponownie klaster.

Jeśli instalacja powiodła się, zaimportowana biblioteka powinna wyglądać następująco:

Zestaw SDK dla **_without_** czajców ![bez zautomatyzowanego uczenia maszynowego Azure Machine Learning SDK dla databricks](./media/how-to-configure-environment/amlsdk-withoutautoml.jpg)

SDK dla Databricks **Z** zautomatyzowanym zestawem SDK uczenia ![maszynowego z zainstalowanym zautomatyzowanym uczeniem maszynowym na Databricks](./media/how-to-configure-environment/automlonadb.png)

### <a name="start-exploring"></a>Rozpocznij eksplorację

Wypróbuj:
+ Chociaż wiele przykładowych notesów są dostępne, **tylko [te przykładowe notesy](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks) działają z usługi Azure Databricks.**

+ Zaimportuj te próbki bezpośrednio z obszaru roboczego. Zobacz ![poniżej:](./media/how-to-configure-environment/azure-db-screenshot.png)
![Wybierz panel Importuj](./media/how-to-configure-environment/azure-db-import.png)

+ Dowiedz się, jak [utworzyć potok z Databricks jako obliczenia szkolenia.](how-to-create-your-first-pipeline.md)

## <a name="create-a-workspace-configuration-file"></a><a id="workspace"></a>Tworzenie pliku konfiguracji obszaru roboczego

Plik konfiguracji obszaru roboczego jest plikiem JSON, który informuje zestaw SDK, jak komunikować się z obszarem roboczym usługi Azure Machine Learning. Plik nosi nazwę *config.json*i ma następujący format:

```json
{
    "subscription_id": "<subscription-id>",
    "resource_group": "<resource-group>",
    "workspace_name": "<workspace-name>"
}
```

Ten plik JSON musi znajdować się w strukturze katalogów, która zawiera skrypty Języka Python lub notesy Jupyter. Może znajdować się w tym samym katalogu, podkatalogu o nazwie *.azureml*lub w katalogu nadrzędnym.

Aby użyć tego pliku z `ws=Workspace.from_config()`kodu, użyj programu . Ten kod ładuje informacje z pliku i łączy się z obszarem roboczym.

Plik konfiguracyjny można utworzyć na trzy sposoby:

* **Użyj [ws.write_config](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)**: napisać plik *config.json.* Plik zawiera informacje o konfiguracji obszaru roboczego. Plik *config.json* można pobrać lub skopiować do innych środowisk programistów.

* **Pobierz plik**: W [witrynie Azure portal](https://ms.portal.azure.com)wybierz pozycję Pobierz plik **config.json** z sekcji **Przegląd** obszaru roboczego.

     ![Azure Portal](./media/how-to-configure-environment/configure.png)

* **Programowo utwórz plik**: We widniech kodu połącz się z obszarem roboczym, podając identyfikator subskrypcji, grupę zasobów i nazwę obszaru roboczego. Następnie zapisuje konfigurację obszaru roboczego w pliku:

    ```python
    from azureml.core import Workspace

    subscription_id = '<subscription-id>'
    resource_group  = '<resource-group>'
    workspace_name  = '<workspace-name>'

    try:
        ws = Workspace(subscription_id = subscription_id, resource_group = resource_group, workspace_name = workspace_name)
        ws.write_config()
        print('Library configuration succeeded')
    except:
        print('Workspace not found')
    ```

    Ten kod zapisuje plik konfiguracyjny w pliku *azureml/config.json.*

## <a name="next-steps"></a>Następne kroki

- [Trenuj model](tutorial-train-models-with-aml.md) w usłudze Azure Machine Learning za pomocą zestawu danych MNIST
- Wyświetlanie zestawu [SDK usługi Azure Machine Learning dla](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) języka Python
