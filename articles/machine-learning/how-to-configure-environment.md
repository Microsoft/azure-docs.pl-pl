---
title: Konfigurowanie środowiska deweloperskiego języka Python
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak skonfigurować środowisko deweloperskie dla Azure Machine Learning. Używaj środowisk Conda, twórz pliki konfiguracji i Konfiguruj własny, oparty na chmurze serwer notesu, notesy Jupyter, Azure Databricks, środowisk IDE, edytory kodu i Data Science Virtual Machine.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.topic: how-to
ms.date: 12/27/2019
ms.custom: seodec18, tracking-python
ms.openlocfilehash: 0d8f6069193607d19e10c013f3d9cb1cf00a7de6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84816720"
---
# <a name="configure-a-development-environment-for-azure-machine-learning"></a>Skonfiguruj środowisko programistyczne dla Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym artykule dowiesz się, jak skonfigurować środowisko deweloperskie do pracy z Azure Machine Learning. Azure Machine Learning jest platformą niezależny od. Jedynym wymaganiem twardym dla środowiska programistycznego jest Python 3. Zalecane jest również środowisko izolowane, takie jak Anaconda lub virtualenv.

W poniższej tabeli przedstawiono wszystkie środowiska deweloperskie omówione w tym artykule wraz z specjalistami i wadami.

| Środowisko | Zalety | Wady |
| --- | --- | --- |
| [Wystąpienie obliczeniowe Azure Machine Learning oparte na chmurze (wersja zapoznawcza)](#compute-instance) | Najprostszym sposobem na rozpoczęcie pracy. Cały zestaw SDK jest już zainstalowany na maszynie wirtualnej obszaru roboczego, a samouczki notesu są wstępnie sklonowane i gotowe do uruchomienia. | Brak kontroli nad środowiskiem deweloperskim i zależnościami. Dodatkowe koszty związane z maszyną wirtualną z systemem Linux (maszyna wirtualna może zostać zatrzymana, gdy nie jest używana, aby uniknąć opłat). Zobacz [szczegóły cennika](https://azure.microsoft.com/pricing/details/virtual-machines/linux/). |
| [Środowisko lokalne](#local) | Pełna kontrola nad środowiskiem deweloperskim i zależnościami. Uruchamiaj przy użyciu dowolnego narzędzia kompilacji, środowiska lub IDE. | Rozpoczęcie pracy trwa dłużej. Wymagane pakiety SDK muszą być zainstalowane, a także należy zainstalować środowisko, jeśli jeszcze go nie masz. |
| [Azure Databricks](#aml-databricks) | Idealne rozwiązanie do uruchamiania przepływów pracy uczenia maszynowego dużej skali na skalowalnej platformie Apache Spark. | Zbyt obszerne eksperymentalne Uczenie maszynowe, a także na mniejsze eksperymenty i przepływy pracy. Dodatkowe koszty poniesione przez Azure Databricks. Zobacz [szczegóły cennika](https://azure.microsoft.com/pricing/details/databricks/). |
| [Data Science Virtual Machine (DSVM)](#dsvm) | Podobnie jak w przypadku wystąpienia obliczeniowego opartego na chmurze (Python i zestawu SDK są wstępnie zainstalowane), ale przy użyciu dodatkowych popularnych narzędzi do nauki o danych i uczenia maszynowego. Łatwe skalowanie i łączenie z innymi niestandardowymi narzędziami i przepływami pracy. | Wolniejsze środowisko uruchamiania w porównaniu do wystąpienia obliczeniowego opartego na chmurze. |

Ten artykuł zawiera również dodatkowe wskazówki dotyczące użycia następujących narzędzi:

* [Notesy Jupyter](#jupyter): Jeśli korzystasz już z Jupyter Notebook, zestaw SDK zawiera pewne dodatki, które należy zainstalować.

* [Visual Studio Code](#vscode): jeśli używasz Visual Studio Code, [rozszerzenie Azure Machine Learning](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai) zawiera rozbudowaną obsługę języków dla języka Python, a także funkcje, które ułatwiają pracę z Azure Machine Learning znacznie wygodniejsze i produktywność.

## <a name="prerequisites"></a>Wymagania wstępne

Obszar roboczy usługi Azure Machine Learning. Aby utworzyć obszar roboczy, zobacz [Tworzenie obszaru roboczego Azure Machine Learning](how-to-manage-workspace.md). Obszar roboczy to wszystko, co jest potrzebne, aby rozpocząć pracę z własnym [serwerem notesu opartym na chmurze](#compute-instance), [DSVM](#dsvm)lub [Azure Databricks](#aml-databricks).

Aby zainstalować środowisko zestawu SDK dla [komputera lokalnego](#local), [Jupyter Notebook serwerze](#jupyter) lub [Visual Studio Code](#vscode) , potrzebne są również następujące elementy:

- Menedżer pakietów [Anaconda](https://www.anaconda.com/download/) lub [Miniconda](https://conda.io/miniconda.html) .

- W systemie Linux lub macOS potrzebna jest powłoka bash.

    > [!TIP]
    > Jeśli korzystasz z systemu Linux lub macOS i używasz powłoki innej niż bash (na przykład ZSH), podczas uruchamiania niektórych poleceń mogą pojawić się błędy. Aby obejść ten problem, użyj `bash` polecenia w celu uruchomienia nowej powłoki bash i uruchomienia w niej poleceń.

- W systemie Windows wymagany jest wiersz polecenia lub monit Anaconda (instalowany przez Anaconda i Miniconda).

## <a name="your-own-cloud-based-compute-instance"></a><a id="compute-instance"></a>Własne wystąpienie obliczeniowe oparte na chmurze

[Wystąpienie obliczeniowe Azure Machine Learning (wersja zapoznawcza)](concept-compute-instance.md) to bezpieczna, oparta na chmurze stacja robocza platformy Azure, która zapewnia analitykom danych za pomocą serwera Jupyter notesu, JupyterLab i w pełni przygotowanego środowiska ml.

Nie ma nic do zainstalowania lub skonfigurowania wystąpienia obliczeniowego.  Utwórz ją w dowolnym momencie w obszarze roboczym Azure Machine Learning. Podaj tylko nazwę i określ typ maszyny wirtualnej platformy Azure. Wypróbuj teraz ten [Samouczek: Konfigurowanie środowiska i obszaru roboczego](tutorial-1st-experiment-sdk-setup.md).

Aby dowiedzieć się więcej o wystąpieniach obliczeniowych, w tym o sposobie instalowania pakietów, zobacz [wystąpienia obliczeniowe](concept-compute-instance.md).

Aby zatrzymać naliczanie opłat za obliczenia, [Zatrzymaj wystąpienie obliczeniowe](tutorial-1st-experiment-sdk-train.md#clean-up-resources).

## <a name="data-science-virtual-machine"></a><a id="dsvm"></a>Maszyna wirtualna do analizy danych

DSVM to dostosowany obraz maszyny wirtualnej (VM). Została zaprojektowana do pracy z nauką o danych, która została wstępnie skonfigurowana za pomocą:

  - Pakiety takie jak TensorFlow, PyTorch, Scikit-uczyć się, XGBoost i Azure Machine Learning SDK
  - Popularne narzędzia do nauki o danych, takie jak platforma Spark autonomiczna i przechodzenie do szczegółów
  - Narzędzia platformy Azure, takie jak interfejs wiersza polecenia platformy Azure, AzCopy i Eksplorator usługi Storage
  - Zintegrowane środowiska deweloperskie (środowisk IDE), takie jak Visual Studio Code i platformy PyCharm itd
  - Serwer Jupyter Notebook

Zestaw Azure Machine Learning SDK działa na Ubuntu lub wersji systemu Windows DSVM. Jeśli jednak planujesz użycie DSVM jako elementu docelowego obliczeń, obsługiwane są tylko Ubuntu.

Aby użyć DSVM jako środowiska deweloperskiego:

1. Utwórz DSVM w dowolnym z następujących środowisk:

    * Azure Portal:

        * [Tworzenie maszyny Data Science Virtual Machine z systemem Ubuntu](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)

        * [Tworzenie maszyny Data Science Virtual Machine z systemem Windows](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/provision-vm)

    * Interfejs wiersza polecenia platformy Azure:

        > [!IMPORTANT]
        > * Korzystając z interfejsu wiersza polecenia platformy Azure, musisz najpierw zalogować się do subskrypcji platformy Azure za pomocą `az login` poleceniu.
        >
        > * W przypadku korzystania z poleceń w tym kroku należy podać nazwę grupy zasobów, nazwę dla maszyny wirtualnej, nazwa użytkownika i hasło.

        * Aby utworzyć Ubuntu Data Science Virtual Machine, użyj następującego polecenia:

            ```azurecli-interactive
            # create a Ubuntu DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            # If you need to create a new resource group use: "az group create --name YOUR-RESOURCE-GROUP-NAME --location YOUR-REGION (For example: westus2)"
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:linux-data-science-vm-ubuntu:linuxdsvmubuntu:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --generate-ssh-keys --authentication-type password
            ```

        * Aby utworzyć Data Science Virtual Machine systemu Windows, użyj następującego polecenia:

            ```azurecli-interactive
            # create a Windows Server 2016 DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:dsvm-windows:server-2016:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --authentication-type password
            ```

2. Zestaw Azure Machine Learning SDK jest już zainstalowany na DSVM. Aby użyć środowiska Conda zawierającego zestaw SDK, użyj jednego z następujących poleceń:

    * Dla Ubuntu DSVM:

        ```bash
        conda activate py36
        ```

    * Dla DSVM systemu Windows:

        ```bash
        conda activate AzureML
        ```

1. Aby sprawdzić, czy możesz uzyskać dostęp do zestawu SDK i sprawdzić wersję, użyj następującego kodu w języku Python:

    ```python
    import azureml.core
    print(azureml.core.VERSION)
    ```

1. Aby skonfigurować DSVM do korzystania z obszaru roboczego Azure Machine Learning, zapoznaj się z sekcją [Tworzenie pliku konfiguracji obszaru roboczego](#workspace) .

Aby uzyskać więcej informacji, zobacz [Virtual Machines analizy danych](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/).

## <a name="local-computer"></a><a id="local"></a>Komputer lokalny

Jeśli używasz komputera lokalnego (co może być również zdalną maszyną wirtualną), Utwórz środowisko Anaconda i Zainstaluj zestaw SDK. Przykład:

1. Pobierz i zainstaluj program [Anaconda](https://www.anaconda.com/distribution/#download-section) (wersja języka Python 3,7), jeśli jeszcze go nie masz.

1. Otwórz monit Anaconda i Utwórz środowisko przy użyciu następujących poleceń:

    Uruchom następujące polecenie, aby utworzyć środowisko.

    ```bash
    conda create -n myenv python=3.7.7
    ```

    Następnie aktywuj środowisko.

    ```bash
    conda activate myenv
    ```

    Ten przykład tworzy środowisko przy użyciu języka Python 3.7.7, ale można wybrać wszystkie konkretne podwersje. Zgodność z zestawem SDK może nie być gwarantowana w niektórych wersjach głównych (zalecane jest 3,5 +) i zaleca się wypróbowanie innej wersji/podwersji w środowisku Anaconda, jeśli wystąpią błędy. Utworzenie środowiska, podczas gdy składniki i pakiety będą pobierane, potrwa kilka minut.

1. Uruchom następujące polecenia w nowym środowisku, aby włączyć jądra IPython specyficzne dla środowiska. Zapewni to oczekiwane zachowanie dotyczące importu jądra i pakietu podczas pracy z notesami Jupyter w środowiskach Anaconda:

    ```bash
    conda install notebook ipykernel
    ```

    Następnie uruchom następujące polecenie, aby utworzyć jądro:

    ```bash
    ipython kernel install --user --name myenv --display-name "Python (myenv)"
    ```

1. Użyj następujących poleceń, aby zainstalować pakiety:

    To polecenie powoduje zainstalowanie podstawowego zestawu SDK Azure Machine Learning przy użyciu notesu i `automl` dodatków. `automl`Dodatkowa jest duża instalacja i można ją usunąć z nawiasów, jeśli nie zamierzasz uruchamiać zautomatyzowanych eksperymentów w usłudze Machine Learning. `automl`Dodatkowo zawiera również zestaw SDK przygotowywania danych Azure Machine Learning.

    ```bash
    pip install azureml-sdk[notebooks,automl]
    ```

   > [!NOTE]
   > * Jeśli zostanie wyświetlony komunikat informujący o tym, że nie można odinstalować programu PyYAML, użyj następującego polecenia zamiast:
   >
   >   `pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML`
   >
   > * Począwszy od macOS Catalina, zsh (Z powłoką) jest domyślną powłoką logowania i interaktywną powłoką. W ZSH Użyj następującego polecenia, które wyprowadza nawiasy klamrowe z " \\ " (ukośnik odwrotny):
   >
   >   `pip install --upgrade azureml-sdk\[notebooks,automl\]`

   Zainstalowanie zestawu SDK potrwa kilka minut. Aby uzyskać więcej informacji na temat opcji instalacji, zobacz [Przewodnik instalacji](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

1. Zainstaluj inne pakiety na potrzeby eksperymentowania w usłudze Machine Learning.

    Użyj jednego z następujących poleceń i Zastąp *\<new package>* pakiet, który chcesz zainstalować. Instalowanie pakietów za pośrednictwem programu `conda install` wymaga, aby pakiet był częścią bieżących kanałów (nowe kanały można dodać w chmurze Anaconda).

    ```bash
    conda install <new package>
    ```

    Alternatywnie możesz zainstalować pakiety za pośrednictwem programu `pip` .

    ```bash
    pip install <new package>
    ```

### <a name="jupyter-notebooks"></a><a id="jupyter"></a>Notesy programu Jupyter

Notesy Jupyter są częścią [projektu Jupyter](https://jupyter.org/). Zapewniają interaktywne środowisko programistyczne, w którym tworzysz dokumenty, które mieszają kod na żywo za pomocą tekstu opisowego i grafiki. Notesy Jupyter są również doskonałym sposobem udostępniania wyników innym osobom, ponieważ można zapisać dane wyjściowe sekcji kodu w dokumencie. Notesy Jupyter można instalować na różnych platformach.

Procedura opisana w sekcji [komputer lokalny](#local) instaluje niezbędne składniki do uruchamiania notesów Jupyter w środowisku Anaconda.

Aby włączyć te składniki w środowisku Jupyter Notebook:

1. Otwórz monit Anaconda i aktywuj środowisko.

    ```bash
    conda activate myenv
    ```

1. Sklonuj [repozytorium GitHub](https://aka.ms/aml-notebooks) dla zestawu przykładowych notesów.

    ```bash
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Uruchom serwer Jupyter Notebook przy użyciu następującego polecenia:

    ```bash
    jupyter notebook
    ```

1. Aby sprawdzić, czy Jupyter Notebook mogą korzystać z zestawu SDK, Utwórz **Nowy** Notes, wybierz opcję **Python 3** jako jądro, a następnie uruchom następujące polecenie w komórce notesu:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. Jeśli wystąpią problemy z importowaniem modułów i odebranie `ModuleNotFoundError` , upewnij się, że jądro Jupyter jest połączone z poprawną ścieżką dla danego środowiska, uruchamiając następujący kod w komórce notesu.

    ```python
    import sys
    sys.path
    ```

1. Aby skonfigurować Jupyter Notebook do korzystania z obszaru roboczego Azure Machine Learning, przejdź do sekcji [Tworzenie pliku konfiguracji obszaru roboczego](#workspace) .

### <a name="visual-studio-code"></a><a id="vscode"></a>Visual Studio Code

Visual Studio Code to bardzo popularny edytor kodu dla wielu platform, który obsługuje obszerny zestaw języków programowania i narzędzi za pomocą rozszerzeń dostępnych w [witrynie Visual Studio Marketplace](https://marketplace.visualstudio.com/vscode). [Rozszerzenie Azure Machine Learning](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai) instaluje rozszerzenie języka [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python) do kodowania we wszystkich typach środowisk Python (Virtual, Anaconda itp.). Ponadto oferuje wygodne funkcje do pracy z zasobami Azure Machine Learning i uruchamiania Azure Machine Learning eksperymentów bez opuszczania Visual Studio Code.

Aby używać Visual Studio Code do tworzenia:

1. Zainstaluj rozszerzenie Azure Machine Learning dla Visual Studio Code, zobacz [Azure Machine Learning](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai).

    Aby uzyskać więcej informacji, zobacz [używanie Azure Machine Learning do Visual Studio Code](tutorial-setup-vscode-extension.md).

1. Dowiedz się, jak używać Visual Studio Code dla dowolnego typu programowania w języku Python, zobacz Wprowadzenie do języka [Python w programu vscode](https://code.visualstudio.com/docs/python/python-tutorial).

    - Aby wybrać środowisko zestawu SDK języka Python zawierające zestaw SDK, Otwórz VS Code, a następnie wybierz klawisze Ctrl + Shift + P (Linux i Windows) lub Command + Shift + P (Mac).
        - Zostanie otwarta __paleta poleceń__ .

    - Wprowadź język __Python: Wybierz interpreter__, a następnie wybierz odpowiednie środowisko

1. Aby sprawdzić, czy można użyć zestawu SDK, Utwórz nowy plik w języku Python (. PR), który zawiera następujący kod:

    ```python
    #%%
    import azureml.core
    azureml.core.VERSION
    ```
    Uruchom ten kod, klikając pozycję "Run Cell" (CodeLens) lub po prostu naciśnij klawisz SHIFT-ENTER.
<a name="aml-databricks"></a>

## <a name="azure-databricks"></a>Azure Databricks
Azure Databricks jest środowiskiem opartym na Apache Spark w chmurze platformy Azure. Udostępnia środowisko współpracy oparte na notesach z procesorem CPU lub klastrem obliczeniowym opartym na procesorach GPU.

Jak Azure Databricks współpracuje z Azure Machine Learning:
+ Możesz nauczyć model przy użyciu platformy Spark MLlib i wdrożyć model do ACI/AKS z poziomu Azure Databricks.
+ Możesz również użyć funkcji [automatycznego uczenia maszynowego](concept-automated-ml.md) w specjalnym zestawie SDK usługi Azure ML z Azure Databricks.
+ Azure Databricks jako obiekt docelowy obliczeń można użyć z [potoku Azure Machine Learning](concept-ml-pipelines.md).

### <a name="set-up-your-databricks-cluster"></a>Konfigurowanie klastra datakostks

Utwórz [klaster datakostki](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal). Niektóre ustawienia są stosowane tylko w przypadku instalowania zestawu SDK w celu automatycznego uczenia maszynowego w kostkach.
**Utworzenie klastra potrwa kilka minut.**

Użyj następujących ustawień:

| Ustawienie |Dotyczy| Wartość |
|----|---|---|
| Nazwa klastra |zawsze| yourclustername |
| Środowisko uruchomieniowe usługi Databricks |zawsze|Środowisko uruchomieniowe inne niż ML 6,5 (Scala 2,11, Spark 2.4.3) |
| Wersja języka Python |zawsze| 3 |
| Ochotnik |zawsze| 2 lub nowszy |
| Typy maszyn wirtualnych węzła procesu roboczego <br>(określa maksymalną liczbę współbieżnych iteracji) |Zautomatyzowane uczenie maszynowe<br>jedyn| Preferowana maszyna wirtualna zoptymalizowana pod kątem pamięci |
| Włącz Skalowanie automatyczne |Zautomatyzowane uczenie maszynowe<br>jedyn| Usuń zaznaczenie |

Przed kontynuowaniem Zaczekaj, aż klaster zostanie uruchomiony.

### <a name="install-the-correct-sdk-into-a-databricks-library"></a>Zainstaluj poprawny zestaw SDK w bibliotece datakostks
Po uruchomieniu klastra [Utwórz bibliotekę](https://docs.databricks.com/user-guide/libraries.html#create-a-library) , aby dołączyć odpowiedni pakiet Azure Machine Learning SDK do klastra.

1. Kliknij prawym przyciskiem myszy folder bieżący obszar roboczy, w którym chcesz przechowywać bibliotekę. Wybierz pozycję **Utwórz**  >  **bibliotekę**.

1. Wybierz **tylko jedną** opcję (nie jest obsługiwana żadna inna instalacja zestawu SDK)

   |&nbsp;Dodatki do pakietu SDK &nbsp;|Element źródłowy|&nbsp;Nazwa PyPi&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
   |----|---|---|
   |Dla kostek datakostki| Przekaż Język Python lub PyPI | Azure-SDK [datakostki]|
   |Dla kostek datakostks — with-<br> możliwości zautomatyzowanej ML| Przekaż Język Python lub PyPI | Azure-SDK [automl]|

   > [!Warning]
   > Nie można zainstalować żadnych innych rozszerzeń zestawu SDK. Wybierz tylko jedną z powyższych opcji [datakostki] lub [automl].

   * Nie wybieraj opcji **Dołącz automatycznie do wszystkich klastrów**.
   * Wybierz pozycję **Dołącz** obok nazwy klastra.

1. Monitoruj błędy do momentu zmiany stanu **dołączone**, co może potrwać kilka minut.  Jeśli ten krok zakończy się niepowodzeniem:

   Spróbuj ponownie uruchomić klaster przez:
   1. W lewym okienku wybierz pozycję **klastry**.
   1. W tabeli wybierz nazwę klastra.
   1. Na karcie **biblioteki** wybierz pozycję **Uruchom ponownie**.

   Rozważ również:
   + W konfiguracji AutoML podczas korzystania z Azure Databricks dodać następujące parametry:
       1. ```max_concurrent_iterations```jest oparty na liczbie węzłów procesu roboczego w klastrze.
        2. ```spark_context=sc```jest oparty na domyślnym kontekście Spark.
   + Lub, jeśli masz starą wersję zestawu SDK, usuń zaznaczenie jej z zainstalowanego libs klastra i Przenieś do kosza. Zainstaluj nową wersję zestawu SDK i ponownie uruchom klaster. Jeśli po ponownym uruchomieniu wystąpi problem, odłącz i ponownie Dołącz klaster.

Jeśli instalacja zakończyła się pomyślnie, zaimportowana biblioteka powinna wyglądać następująco:

Zestaw SDK dla datakostek **_bez_** ZAUTOMATYZOWANEGO zestawu SDK usługi machine Learning ![ Azure Machine Learning dla datakostki](./media/how-to-configure-environment/amlsdk-withoutautoml.jpg)

Zestaw SDK dla zestawów datakostks **z** automatycznym ![ zestawem SDK usługi Machine Learning z automatyczną nauką maszynową zainstalowaną w kostkach](./media/how-to-configure-environment/automlonadb.png)

### <a name="start-exploring"></a>Rozpocznij eksplorację

Wypróbuj:
+ Chociaż dostępnych jest wiele przykładowych notesów, **tylko [te przykładowe notesy](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks) współpracują z Azure Databricks.**

+ Zaimportuj te próbki bezpośrednio z obszaru roboczego. Patrz poniżej: ![ Wybierz pozycję Importuj ](./media/how-to-configure-environment/azure-db-screenshot.png)
 ![ panel importowania](./media/how-to-configure-environment/azure-db-import.png)

+ Dowiedz się [, jak tworzyć potoki z kostkami w ramach obliczeń szkoleniowych](how-to-create-your-first-pipeline.md).

## <a name="create-a-workspace-configuration-file"></a><a id="workspace"></a>Utwórz plik konfiguracji obszaru roboczego

Plik konfiguracji obszaru roboczego to plik JSON, który informuje zestaw SDK, jak komunikować się z obszarem roboczym Azure Machine Learning. Plik ma nazwę *config.jsna*i ma następujący format:

```json
{
    "subscription_id": "<subscription-id>",
    "resource_group": "<resource-group>",
    "workspace_name": "<workspace-name>"
}
```

Ten plik JSON musi znajdować się w strukturze katalogów zawierającej skrypty języka Python lub notesy Jupyter. Może znajdować się w tym samym katalogu, podkatalogu o nazwie *. Azure*lub w katalogu nadrzędnym.

Aby użyć tego pliku z kodu, użyj `ws=Workspace.from_config()` . Ten kod ładuje informacje z pliku i łączy się z obszarem roboczym.

Plik konfiguracji można utworzyć na trzy sposoby:

* **Użyj [ws. write_config](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)**:, aby napisać *config.jsna* pliku. Plik zawiera informacje o konfiguracji dla obszaru roboczego. *config.js* można pobrać lub skopiować do innych środowisk programistycznych.

* **Pobierz plik**: w [Azure Portal](https://ms.portal.azure.com)wybierz pozycję **Pobierz config.js** z sekcji **Przegląd** w obszarze roboczym.

     ![Azure Portal](./media/how-to-configure-environment/configure.png)

* **Utwórz plik programowo**: w poniższym fragmencie kodu Nawiąż połączenie z obszarem roboczym, podając Identyfikator subskrypcji, grupę zasobów i nazwę obszaru roboczego. Następnie zapisuje konfigurację obszaru roboczego do pliku:

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

    Ten kod zapisuje plik konfiguracyjny w pliku *.//config.js.*

## <a name="next-steps"></a>Następne kroki

- [Uczenie modelu](tutorial-train-models-with-aml.md) na Azure Machine Learning z zestawem danych mnist ręcznie
- Wyświetl informacje o [zestawie Azure Machine Learning SDK dla języka Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
