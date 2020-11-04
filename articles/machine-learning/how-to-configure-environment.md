---
title: Konfigurowanie środowiska deweloperskiego | Python
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak skonfigurować środowisko programistyczne języka Python dla Azure Machine Learning. Używaj środowisk Conda, twórz pliki konfiguracji i Konfiguruj własny, oparty na chmurze serwer notesu, notesy Jupyter, Azure Databricks, środowisk IDE, edytory kodu i Data Science Virtual Machine.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.date: 09/30/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperfq1, devx-track-azurecli
ms.openlocfilehash: 7e189885fbf7befcaea3f63148a42c81dc1da03e
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93320486"
---
# <a name="set-up-a-development-environment-for-azure-machine-learning"></a>Skonfiguruj środowisko programistyczne dla Azure Machine Learning

Dowiedz się, jak skonfigurować środowisko deweloperskie języka Python dla Azure Machine Learning.

W poniższej tabeli przedstawiono wszystkie środowiska deweloperskie omówione w tym artykule wraz z specjalistami i wadami.

| Środowisko | Zalety | Wady |
| --- | --- | --- |
| [Środowisko lokalne](#local) | Pełna kontrola nad środowiskiem deweloperskim i zależnościami. Uruchamiaj przy użyciu dowolnego narzędzia kompilacji, środowiska lub IDE. | Rozpoczęcie pracy trwa dłużej. Wymagane pakiety SDK muszą być zainstalowane, a także należy zainstalować środowisko, jeśli jeszcze go nie masz. |
| [Wystąpienie obliczeniowe usługi Azure Machine Learning](#compute-instance) | Najprostszym sposobem na rozpoczęcie pracy. Cały zestaw SDK jest już zainstalowany na maszynie wirtualnej obszaru roboczego, a samouczki notesu są wstępnie sklonowane i gotowe do uruchomienia. | Brak kontroli nad środowiskiem deweloperskim i zależnościami. Dodatkowe koszty związane z maszyną wirtualną z systemem Linux (maszyna wirtualna może zostać zatrzymana, gdy nie jest używana, aby uniknąć opłat). Zobacz [szczegóły cennika](https://azure.microsoft.com/pricing/details/virtual-machines/linux/). |
| [Azure Databricks](#aml-databricks) | Idealne rozwiązanie do uruchamiania przepływów pracy uczenia maszynowego dużej skali na skalowalnej platformie Apache Spark. | Zbyt obszerne eksperymentalne Uczenie maszynowe, a także na mniejsze eksperymenty i przepływy pracy. Dodatkowe koszty poniesione przez Azure Databricks. Zobacz [szczegóły cennika](https://azure.microsoft.com/pricing/details/databricks/). |
| [Data Science Virtual Machine (DSVM)](#dsvm) | Podobnie jak w przypadku wystąpienia obliczeniowego opartego na chmurze (Python i zestawu SDK są wstępnie zainstalowane), ale przy użyciu dodatkowych popularnych narzędzi do nauki o danych i uczenia maszynowego. Łatwe skalowanie i łączenie z innymi niestandardowymi narzędziami i przepływami pracy. | Wolniejsze środowisko uruchamiania w porównaniu do wystąpienia obliczeniowego opartego na chmurze. |

Ten artykuł zawiera również dodatkowe wskazówki dotyczące użycia następujących narzędzi:

* Notesy Jupyter: Jeśli już korzystasz z notesów Jupyter, zestaw SDK zawiera pewne dodatki, które należy zainstalować.

* Visual Studio Code: Jeśli używasz Visual Studio Code, [rozszerzenie Azure Machine Learning](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai) zawiera rozbudowaną obsługę języków dla języka Python, a także funkcje, które ułatwiają pracę z Azure Machine Learning znacznie wygodniejsze i produktywność.

## <a name="prerequisites"></a>Wymagania wstępne

* Azure Machine Learning obszar roboczy. Jeśli go nie masz, możesz utworzyć obszar roboczy Azure Machine Learning za pomocą szablonów [Azure Portal](how-to-manage-workspace.md), [interfejsu wiersza polecenia platformy Azure](how-to-manage-workspace-cli.md#create-a-workspace)i [Azure Resource Manager](how-to-create-workspace-template.md).

### <a name="local-and-dsvm-only-create-a-workspace-configuration-file"></a><a id="workspace"></a> (Tylko lokalne i DSVM) Utwórz plik konfiguracji obszaru roboczego

Plik konfiguracji obszaru roboczego to plik JSON, który informuje zestaw SDK, jak komunikować się z obszarem roboczym Azure Machine Learning. Plik ma nazwę *config.jsna* i ma następujący format:

```json
{
    "subscription_id": "<subscription-id>",
    "resource_group": "<resource-group>",
    "workspace_name": "<workspace-name>"
}
```

Ten plik JSON musi znajdować się w strukturze katalogów zawierającej skrypty języka Python lub notesy Jupyter. Może znajdować się w tym samym katalogu, podkatalogu o nazwie *. Azure* lub w katalogu nadrzędnym.

Aby użyć tego pliku z kodu, użyj [`Workspace.from_config`](/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#from-config-path-none--auth-none---logger-none---file-name-none-&preserve-view=true) metody. Ten kod ładuje informacje z pliku i łączy się z obszarem roboczym.

Utwórz plik konfiguracji obszaru roboczego w jednej z następujących metod:

* Azure Portal

    **Pobierz plik** : w [Azure Portal](https://ms.portal.azure.com)wybierz pozycję  **Pobierz config.js** z sekcji **Przegląd** w obszarze roboczym.

    ![Azure Portal](./media/how-to-configure-environment/configure.png)

* Azure Machine Learning Python SDK

    Utwórz skrypt, aby połączyć się z obszarem roboczym Azure Machine Learning i użyć [`write_config`](/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#write-config-path-none--file-name-none-&preserve-view=true) metody do wygenerowania pliku i zapisania go jako *. azure/config.jsw systemie*. Pamiętaj o zamianie `subscription_id` , `resource_group` , i `workspace_name` ze swoimi własnymi.

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

## <a name="local-computer"></a><a id="local"></a>Komputer lokalny

Aby skonfigurować lokalne środowisko deweloperskie (które może być również zdalną maszyną wirtualną, taką jak wystąpienie obliczeniowe Azure Machine Learning lub DSVM):

1. Utwórz środowisko wirtualne języka Python (virtualenv, Conda).

    > [!NOTE]
    > Chociaż nie jest to wymagane, zaleca się użycie [Anaconda](https://www.anaconda.com/download/) lub [Miniconda](https://www.anaconda.com/download/) do zarządzania środowiskami wirtualnymi języka Python i instalowaniem pakietów.

    > [!IMPORTANT]
    > Jeśli korzystasz z systemu Linux lub macOS i używasz powłoki innej niż bash (na przykład ZSH), podczas uruchamiania niektórych poleceń mogą pojawić się błędy. Aby obejść ten problem, użyj `bash` polecenia w celu uruchomienia nowej powłoki bash i uruchomienia w niej poleceń.

1. Aktywuj nowo utworzone środowisko wirtualne języka Python.
1. Zainstaluj [zestaw Azure Machine Learning Python SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py).
1. Aby skonfigurować lokalne środowisko do korzystania z obszaru roboczego Azure Machine Learning, [Utwórz plik konfiguracji obszaru roboczego](#workspace) lub Użyj istniejącego.

Teraz, po skonfigurowaniu lokalnego środowiska, możesz rozpocząć pracę z Azure Machine Learning. Zapoznaj się [Azure Machine Learning z przewodnikiem wprowadzenie](tutorial-1st-experiment-sdk-setup-local.md) do języka Python, aby rozpocząć pracę.

### <a name="jupyter-notebooks"></a><a id="jupyter"></a>Notesy programu Jupyter

W przypadku uruchamiania lokalnego serwera Jupyter Notebook zaleca się utworzenie jądra IPython dla środowiska wirtualnego języka Python. Pomaga to zapewnić oczekiwane zachowanie podczas importowania jądra i pakietu.

1. Włącz jądra IPython specyficzne dla środowiska

    ```bash
    conda install notebook ipykernel
    ```

1. Utwórz jądro dla środowiska wirtualnego języka Python. Pamiętaj o zamianie na `<myenv>` nazwę środowiska wirtualnego języka Python.

    ```bash
    ipython kernel install --user --name <myenv> --display-name "Python (myenv)"
    ```

1. Uruchom serwer Jupyter Notebook

Zapoznaj się z [repozytorium Azure Machine Learning notesów](https://github.com/Azure/MachineLearningNotebooks) , aby rozpocząć pracę z notesami Azure Machine Learning i Jupyter.

> [!NOTE]
> Repozytorium z przykładami społecznościowymi można znaleźć w witrynie https://github.com/Azure/azureml-examples .

### <a name="visual-studio-code"></a><a id="vscode"></a>Visual Studio Code

Aby używać Visual Studio Code do tworzenia:

1. Zainstaluj [Visual Studio Code](https://code.visualstudio.com/Download).
1. Zainstaluj [rozszerzenie Visual Studio Code Azure Machine Learning](tutorial-setup-vscode-extension.md) (wersja zapoznawcza).

Po zainstalowaniu rozszerzenia Visual Studio Code możesz zarządzać [zasobami Azure Machine Learning](how-to-manage-resources-vscode.md), [uruchamiać i debugować eksperymenty](how-to-debug-visual-studio-code.md)oraz [wdrażać przeszkolone modele](tutorial-train-deploy-image-classification-model-vscode.md).

## <a name="azure-machine-learning-compute-instance"></a><a id="compute-instance"></a>Wystąpienie obliczeniowe usługi Azure Machine Learning

[Wystąpienie obliczeniowe](concept-compute-instance.md) Azure Machine Learning to bezpieczna, oparta na chmurze stacja robocza platformy Azure, która zapewnia analitykom danych z serwerem Jupyter Notebook, JupyterLab i w pełni zarządzanym środowiskiem uczenia maszynowego.

Nie ma nic do zainstalowania lub skonfigurowania wystąpienia obliczeniowego.  

Utwórz ją w dowolnym momencie w obszarze roboczym Azure Machine Learning. Podaj tylko nazwę i określ typ maszyny wirtualnej platformy Azure. Wypróbuj teraz ten [Samouczek: Konfigurowanie środowiska i obszaru roboczego](tutorial-1st-experiment-sdk-setup.md).

Aby dowiedzieć się więcej o wystąpieniach obliczeniowych, w tym o sposobie instalowania pakietów, zobacz [Tworzenie wystąpienia obliczeniowego Azure Machine Learning i zarządzanie](how-to-create-manage-compute-instance.md)nim.

> [!TIP]
> Aby uniknąć naliczania opłat za nieużywane wystąpienie obliczeniowe, [Zatrzymaj wystąpienie obliczeniowe](how-to-create-manage-compute-instance.md#manage).

Oprócz Jupyter Notebook Server i JupyterLab, można używać wystąpień obliczeniowych w [zintegrowanej funkcji notesu w programie Azure Machine Learning Studio](how-to-run-jupyter-notebooks.md).

Można również użyć rozszerzenia Azure Machine Learning Visual Studio Code, aby [skonfigurować Azure Machine Learning wystąpienie obliczeniowe jako zdalny serwer Jupyter Notebook](how-to-set-up-vs-code-remote.md#configure-compute-instance-as-remote-notebook-server).

## <a name="data-science-virtual-machine"></a><a id="dsvm"></a>Maszyna wirtualna do analizy danych

DSVM to dostosowany obraz maszyny wirtualnej (VM). Została zaprojektowana do pracy z nauką o danych, które są wstępnie skonfigurowane narzędzia i oprogramowanie takie jak:

  - Pakiety takie jak TensorFlow, PyTorch, Scikit-uczyć się, XGBoost i Azure Machine Learning SDK
  - Popularne narzędzia do nauki o danych, takie jak platforma Spark autonomiczna i przechodzenie do szczegółów
  - Narzędzia platformy Azure, takie jak interfejs wiersza polecenia platformy Azure, AzCopy i Eksplorator usługi Storage
  - Zintegrowane środowiska deweloperskie (środowisk IDE), takie jak Visual Studio Code i platformy PyCharm itd
  - Serwer Jupyter Notebook

Aby zapoznać się z bardziej kompleksową listą narzędzi, zobacz [Przewodnik po narzędziach DSVM](data-science-virtual-machine/tools-included.md).

> [!IMPORTANT]
> Jeśli planujesz użycie DSVM jako [elementu docelowego obliczeń](concept-compute-target.md) dla zadań szkoleniowych lub inferencing, obsługiwana jest tylko wartość Ubuntu.

Aby użyć DSVM jako środowiska deweloperskiego

1. Utwórz element DSVM przy użyciu jednej z następujących metod:

    * Użyj Azure Portal, aby utworzyć [Ubuntu](data-science-virtual-machine/dsvm-ubuntu-intro.md) lub DSVM [systemu Windows](data-science-virtual-machine/provision-vm.md) .
    * [Tworzenie DSVM przy użyciu szablonów usługi ARM](data-science-virtual-machine/dsvm-tutorial-resource-manager.md).
    * Używanie interfejsu wiersza polecenia platformy Azure

        Aby utworzyć Ubuntu DSVM, użyj następującego polecenia:

        ```azurecli-interactive
        # create a Ubuntu DSVM in your resource group
        # note you need to be at least a contributor to the resource group in order to execute this command successfully
        # If you need to create a new resource group use: "az group create --name YOUR-RESOURCE-GROUP-NAME --location YOUR-REGION (For example: westus2)"
        az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:linux-data-science-vm-ubuntu:linuxdsvmubuntu:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --generate-ssh-keys --authentication-type password
        ```

        Aby utworzyć DSVM systemu Windows, użyj następującego polecenia:

        ```azurecli-interactive
        # create a Windows Server 2016 DSVM in your resource group
        # note you need to be at least a contributor to the resource group in order to execute this command successfully
        az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:dsvm-windows:server-2016:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --authentication-type password
        ```

1. Aktywuj środowisko Conda zawierające zestaw SDK Azure Machine Learning.

    * Dla Ubuntu DSVM:

        ```bash
        conda activate py36
        ```

    * Dla DSVM systemu Windows:

        ```bash
        conda activate AzureML
        ```

1. Aby skonfigurować DSVM do korzystania z obszaru roboczego Azure Machine Learning, [Utwórz plik konfiguracji obszaru roboczego](#workspace) lub Użyj istniejącego.

Podobnie jak w środowiskach lokalnych, można Visual Studio Code użyć [rozszerzenia Visual Studio Code Azure Machine Learning](#vscode) , aby współdziałać z Azure Machine Learning.

Aby uzyskać więcej informacji, zobacz [Virtual Machines analizy danych](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/).

## <a name="azure-databricks"></a><a name="aml-databricks"></a> Azure Databricks

Azure Databricks jest środowiskiem opartym na Apache Spark w chmurze platformy Azure. Udostępnia środowisko współpracy oparte na notesach z procesorem CPU lub klastrem obliczeniowym opartym na procesorach GPU.

Jak Azure Databricks współpracuje z Azure Machine Learning:

+ Możesz nauczyć model przy użyciu platformy Spark MLlib i wdrożyć model do ACI/AKS z poziomu Azure Databricks.
+ Możesz również użyć funkcji [automatycznego uczenia maszynowego](concept-automated-ml.md) w specjalnym zestawie SDK usługi Azure ML z Azure Databricks.
+ Azure Databricks jako obiekt docelowy obliczeń można użyć z [potoku Azure Machine Learning](concept-ml-pipelines.md).

### <a name="set-up-your-databricks-cluster"></a>Konfigurowanie klastra datakostks

Utwórz [klaster datakostki](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal). Niektóre ustawienia są stosowane tylko w przypadku instalowania zestawu SDK w celu automatycznego uczenia maszynowego w kostkach.
**Utworzenie klastra potrwa kilka minut.**

Użyj następujących ustawień:

| Ustawienie |Dotyczy| Wartość |
|----|---|---|
| Nazwa klastra |zawsze| yourclustername |
| Środowisko uruchomieniowe usługi Databricks |zawsze|Środowisko uruchomieniowe inne niż ML 7,1 (Scala 2,21, Spark 3.0.0) |
| Wersja języka Python |zawsze| 3 |
| Ochotnik |zawsze| 2 lub nowszy |
| Typy maszyn wirtualnych węzła procesu roboczego <br>(określa maksymalną liczbę współbieżnych iteracji) |Zautomatyzowane uczenie maszynowe<br>jedyn| Preferowana maszyna wirtualna zoptymalizowana pod kątem pamięci |
| Włącz Skalowanie automatyczne |Zautomatyzowane uczenie maszynowe<br>jedyn| Usuń zaznaczenie |

Przed kontynuowaniem Zaczekaj, aż klaster zostanie uruchomiony.

### <a name="install-the-correct-sdk-into-a-databricks-library"></a>Zainstaluj poprawny zestaw SDK w bibliotece datakostks

Po uruchomieniu klastra [Utwórz bibliotekę](https://docs.databricks.com/user-guide/libraries.html#create-a-library) , aby dołączyć odpowiedni pakiet Azure Machine Learning SDK do klastra. W przypadku zautomatyzowanej ML przejdź do [zestawu SDK dla kostek datakostków z automatyczną nauką uczenia maszynowego](#sdk-for-databricks-with-automated-machine-learning).

1. Kliknij prawym przyciskiem myszy folder bieżący obszar roboczy, w którym chcesz przechowywać bibliotekę. Wybierz pozycję **Utwórz**  >  **bibliotekę**.

1. Wybierz następującą opcję (nie są obsługiwane żadne inne instalacje zestawu SDK)

   |&nbsp;Dodatki do pakietu SDK &nbsp;|Źródło|&nbsp;Nazwa PyPi&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
   |----|---|---|
   |Dla kostek datakostki| Przekaż Język Python lub PyPI | Azure-SDK [datakostki]|

   > [!Warning]
   > Nie można zainstalować żadnych innych rozszerzeń zestawu SDK. Wybierz tylko opcję [ `databricks` ].

   * Nie wybieraj opcji **Dołącz automatycznie do wszystkich klastrów**.
   * Wybierz pozycję  **Dołącz** obok nazwy klastra.

1. Monitoruj błędy do momentu zmiany stanu **dołączone** , co może potrwać kilka minut.  Jeśli ten krok zakończy się niepowodzeniem:

   Spróbuj ponownie uruchomić klaster przez:
   1. W lewym okienku wybierz pozycję **klastry**.
   1. W tabeli wybierz nazwę klastra.
   1. Na karcie **biblioteki** wybierz pozycję **Uruchom ponownie**.

   Rozważ również:
   + W konfiguracji AutoML podczas korzystania z Azure Databricks dodać następujące parametry:
       1. ```max_concurrent_iterations``` jest oparty na liczbie węzłów procesu roboczego w klastrze.
        2. ```spark_context=sc``` jest oparty na domyślnym kontekście Spark.
   + Lub, jeśli masz starą wersję zestawu SDK, usuń zaznaczenie jej z zainstalowanego libs klastra i Przenieś do kosza. Zainstaluj nową wersję zestawu SDK i ponownie uruchom klaster. Jeśli po ponownym uruchomieniu wystąpi problem, odłącz i ponownie Dołącz klaster.

Jeśli instalacja zakończyła się pomyślnie, zaimportowana biblioteka powinna wyglądać następująco:

#### <a name="sdk-for-databricks"></a>Zestaw SDK dla datakostki
![Zestaw Azure Machine Learning SDK dla datakostki](./media/how-to-configure-environment/amlsdk-withoutautoml.jpg)

#### <a name="sdk-for-databricks-with-automated-machine-learning"></a>Zestaw SDK dla kostek datakostks z automatycznym uczeniem maszynowym
Jeśli klaster został utworzony z użyciem elementów datakostks o wartości innej niż środowisko uruchomieniowe 7,1 lub nowszy, uruchom następujące polecenie w pierwszej komórce notesu, aby zainstalować zestaw SDK AML.

```
%pip install --upgrade --force-reinstall -r https://aka.ms/automl_linux_requirements.txt
```
W przypadku datarangs nie ML Runtime 7,0 i Lower Zainstaluj zestaw AML SDK przy użyciu [skryptu init](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks/automl/README.md).


### <a name="start-exploring"></a>Rozpocznij eksplorację

Wypróbuj:
+ Chociaż dostępnych jest wiele przykładowych notesów, **tylko [te przykładowe notesy](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks) współpracują z Azure Databricks.**

+ Zaimportuj te próbki bezpośrednio z obszaru roboczego. Patrz poniżej: ![ Wybierz pozycję Importuj ](./media/how-to-configure-environment/azure-db-screenshot.png)
 ![ panel importowania](./media/how-to-configure-environment/azure-db-import.png)

+ Dowiedz się [, jak tworzyć potoki z kostkami w ramach obliczeń szkoleniowych](how-to-create-your-first-pipeline.md).

## <a name="next-steps"></a>Następne kroki

- [Uczenie modelu](tutorial-train-models-with-aml.md) na Azure Machine Learning z zestawem danych mnist ręcznie
- Wyświetl informacje o [zestawie Azure Machine Learning SDK dla języka Python](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py)