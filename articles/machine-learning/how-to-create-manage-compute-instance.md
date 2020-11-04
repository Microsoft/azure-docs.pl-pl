---
title: Tworzenie wystąpienia obliczeniowego i zarządzanie nim
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak utworzyć wystąpienie obliczeniowe w obszarze roboczym Azure Machine Learning i zarządzać nim. Użyj wystąpienia obliczeniowego jako środowiska deweloperskiego lub do szkolenia i wnioskowania w celach deweloperskich/testowych.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: sgilley
author: sdgilley
ms.reviewer: sgilley
ms.date: 10/02/2020
ms.openlocfilehash: 6e0d80c35a4822ad46973c94b32cf71b129ad1e1
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93318027"
---
# <a name="create-and-manage-an-azure-machine-learning-compute-instance"></a>Tworzenie wystąpienia obliczeniowego Azure Machine Learning i zarządzanie nim

Dowiedz się, jak utworzyć [wystąpienie obliczeniowe](concept-compute-instance.md) w obszarze roboczym Azure Machine Learning i zarządzać nim.

Użyj wystąpienia obliczeniowego jako w pełni skonfigurowanego i zarządzanego środowiska programistycznego w chmurze. Na potrzeby programowania i testowania można również użyć wystąpienia jako [elementu docelowego obliczeń szkoleniowych](concept-compute-target.md#train) lub dla [celu wnioskowania](concept-compute-target.md#deploy).   Wystąpienie obliczeniowe może uruchamiać wiele zadań równolegle i ma kolejkę zadań. Jako środowisko programistyczne wystąpienie obliczeniowe nie może być współużytkowane z innymi użytkownikami w obszarze roboczym.

W tym artykule omówiono sposób wykonywania następujących zadań:

* Tworzenie wystąpienia obliczeniowego 
* Zarządzanie (uruchamianie, zatrzymywanie, ponowne uruchamianie, usuwanie) wystąpienia obliczeniowego
* Dostęp do okna terminalu 
* Zainstaluj pakiety języka R lub Python
* Twórz nowe środowiska lub jądra Jupyter

Wystąpienia obliczeniowe mogą bezpiecznie uruchamiać zadania w [środowisku sieci wirtualnej](how-to-secure-training-vnet.md), bez konieczności otwierania portów SSH przez przedsiębiorstwa. Zadanie jest wykonywane w środowisku kontenerowym i pakuje zależności modelu w kontenerze platformy Docker. 

## <a name="prerequisites"></a>Wymagania wstępne

* Obszar roboczy usługi Azure Machine Learning. Aby uzyskać więcej informacji, zobacz [Tworzenie obszaru roboczego Azure Machine Learning](how-to-manage-workspace.md).

* [Rozszerzenie interfejsu wiersza polecenia platformy Azure dla usługi Machine Learning Service](reference-azure-machine-learning-cli.md), [Azure Machine Learning SDK języka Python](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py)lub [rozszerzenia Azure Machine Learning Visual Studio Code](tutorial-setup-vscode-extension.md).

## <a name="create"></a>Utwórz

**Szacowany czas** : około 5 minut.

Tworzenie wystąpienia obliczeniowego to jednorazowy proces dla Twojego obszaru roboczego. Można ponownie użyć tego obliczenia jako stacji roboczej deweloperskiej lub jako elementu docelowego obliczeń do szkolenia. Do obszaru roboczego można dołączyć wiele wystąpień obliczeniowych.

Dedykowane rdzenie dla poszczególnych regionów na poszczególne regiony i łączne limity przydziału regionalnego, które mają zastosowanie do tworzenia wystąpienia obliczeniowego, są ujednolicone i udostępniane przy użyciu Azure Machine Learninggo przydziału klastra obliczeniowego. Zatrzymanie wystąpienia obliczeniowego nie powoduje zwolnienia przydziału w celu zapewnienia, że będzie można ponownie uruchomić wystąpienie obliczeniowe. Należy pamiętać, że nie można zmienić rozmiaru maszyny wirtualnej wystąpienia obliczeniowego po jego utworzeniu.

Poniższy przykład ilustruje sposób tworzenia wystąpienia obliczeniowego:

# <a name="python"></a>[Python](#tab/python)

```python
import datetime
import time

from azureml.core.compute import ComputeTarget, ComputeInstance
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your instance
# Compute instance name should be unique across the azure region
compute_name = "ci{}".format(ws._workspace_id)[:10]

# Verify that instance does not exist already
try:
    instance = ComputeInstance(workspace=ws, name=compute_name)
    print('Found existing instance, use it.')
except ComputeTargetException:
    compute_config = ComputeInstance.provisioning_configuration(
        vm_size='STANDARD_D3_V2',
        ssh_public_access=False,
        # vnet_resourcegroup_name='<my-resource-group>',
        # vnet_name='<my-vnet-name>',
        # subnet_name='default',
        # admin_user_ssh_public_key='<my-sshkey>'
    )
    instance = ComputeInstance.create(ws, compute_name, compute_config)
    instance.wait_for_completion(show_output=True)
```

Aby uzyskać więcej informacji na temat klas, metod i parametrów używanych w tym przykładzie, zobacz następujące dokumenty referencyjne:

* [Klasa ComputeInstance](/python/api/azureml-core/azureml.core.compute.computeinstance.computeinstance?preserve-view=true&view=azure-ml-py)
* [ComputeTarget. Create](/python/api/azureml-core/azureml.core.compute.computetarget?preserve-view=true&view=azure-ml-py#create-workspace--name--provisioning-configuration-)
* [ComputeInstance.wait_for_completion](/python/api/azureml-core/azureml.core.compute.computeinstance(class)?preserve-view=true&view=azure-ml-py#wait-for-completion-show-output-false--is-delete-operation-false-)


# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
az ml computetarget create computeinstance  -n instance -s "STANDARD_D3_V2" -v
```

Aby uzyskać więcej informacji, zobacz [AZ ml computetarget Create computeinstance](/cli/azure/ext/azure-cli-ml/ml/computetarget/create?preserve-view=true&view=azure-cli-latest#ext_azure_cli_ml_az_ml_computetarget_create_computeinstance) Reference.

# <a name="studio"></a>[Studio](#tab/azure-studio)

W obszarze roboczym programu Azure Machine Learning Studio Utwórz nowe wystąpienie obliczeniowe z sekcji **obliczenia** lub w sekcji **notesy** , gdy wszystko będzie gotowe do uruchomienia jednego z notesów.

Aby uzyskać informacje na temat tworzenia wystąpienia obliczeniowego w programie Studio, zobacz [Tworzenie obiektów docelowych obliczeń w programie Azure Machine Learning Studio](how-to-create-attach-compute-studio.md#compute-instance).

---

Można również utworzyć wystąpienie obliczeniowe z [szablonem Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-computeinstance). 

### <a name="create-on-behalf-of-preview"></a>Utwórz w imieniu (wersja zapoznawcza)

Jako administrator możesz utworzyć wystąpienie obliczeniowe w imieniu Analityka danych i przypisać do nich wystąpienie:
* [Szablon Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-computeinstance).  Aby uzyskać szczegółowe informacje na temat sposobu wyszukiwania TenantID i ObjectID wymaganych w tym szablonie, zobacz [Znajdowanie identyfikatorów obiektów tożsamości dla konfiguracji uwierzytelniania](../healthcare-apis/find-identity-object-ids.md).  Te wartości można również znaleźć w portalu Azure Active Directory.
* Interfejs API REST

Analityk danych, dla którego tworzysz wystąpienie obliczeniowe, musi mieć następujące uprawnienia [kontroli dostępu opartej na rolach (Azure RBAC)](../role-based-access-control/overview.md) : 
* *Microsoft. MachineLearningServices/obszary robocze/obliczenia/uruchomienie/akcja*
* *Microsoft. MachineLearningServices/obszary robocze/obliczenia/akcja*
* *Microsoft. MachineLearningServices/obszary robocze/obliczenia/ponowne uruchomienie/akcja*
* *Microsoft. MachineLearningServices/obszary robocze/obliczenia/applicationaccess/akcja*

Analityk danych może uruchamiać, zatrzymywać i ponownie uruchamiać wystąpienie obliczeniowe. Mogą używać wystąpienia obliczeniowego dla:
* Jupyter
* JupyterLab
* RStudio
* Zintegrowane notesy

## <a name="manage"></a>Zarządzanie

Uruchamianie, zatrzymywanie, ponowne uruchamianie i usuwanie wystąpienia obliczeniowego. Wystąpienie obliczeniowe nie jest automatycznie skalowane w dół, dlatego pamiętaj o zatrzymaniu zasobu, aby uniknąć naliczania opłat.

# <a name="python"></a>[Python](#tab/python)

W poniższych przykładach nazwa wystąpienia obliczeniowego to **wystąpienie**

* Pobierz stan

    ```python
    # get_status() gets the latest status of the ComputeInstance target
    instance.get_status()
    ```

* Stop

    ```python
    # stop() is used to stop the ComputeInstance
    # Stopping ComputeInstance will stop the billing meter and persist the state on the disk.
    # Available Quota will not be changed with this operation.
    instance.stop(wait_for_completion=True, show_output=True)
    ```

* Rozpocznij

    ```python
    # start() is used to start the ComputeInstance if it is in stopped state
    instance.start(wait_for_completion=True, show_output=True)
    ```

* Uruchom ponownie

    ```python
    # restart() is used to restart the ComputeInstance
    instance.restart(wait_for_completion=True, show_output=True)
    ```

* Usuwanie

    ```python
    # delete() is used to delete the ComputeInstance target. Useful if you want to re-use the compute name 
    instance.delete(wait_for_completion=True, show_output=True)
    ```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

W poniższych przykładach nazwa wystąpienia obliczeniowego to **wystąpienie**

* Stop

    ```azurecli-interactive
    az ml computetarget stop computeinstance -n instance -v
    ```

    Aby uzyskać więcej informacji, zobacz [AZ ml computetarget Stop computeinstance](/cli/azure/ext/azure-cli-ml/ml/computetarget/computeinstance?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-computeinstance-stop).

* Rozpocznij 

    ```azurecli-interactive
    az ml computetarget start computeinstance -n instance -v
    ```

    Aby uzyskać więcej informacji, zobacz [AZ ml computetarget Start computeinstance](/cli/azure/ext/azure-cli-ml/ml/computetarget/computeinstance?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-computeinstance-start).

* Uruchom ponownie 

    ```azurecli-interactive
    az ml computetarget restart computeinstance -n instance -v
    ```

    Aby uzyskać więcej informacji, zobacz [AZ ml computetarget restart computeinstance](/cli/azure/ext/azure-cli-ml/ml/computetarget/computeinstance?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-computeinstance-restart).

* Usuwanie

    ```azurecli-interactive
    az ml computetarget delete -n instance -v
    ```

    Aby uzyskać więcej informacji, zobacz [AZ ml computetarget Delete computeinstance](/cli/azure/ext/azure-cli-ml/ml/computetarget?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-delete).

# <a name="studio"></a>[Studio](#tab/azure-studio)

W obszarze roboczym programu Azure Machine Learning Studio wybierz pozycję **obliczenia** , a następnie na górze wybierz pozycję **wystąpienie obliczeniowe** .

![Zarządzanie wystąpieniem obliczeniowym](./media/concept-compute-instance/manage-compute-instance.png)

Można wykonać następujące czynności:

* Tworzenie nowego wystąpienia obliczeniowego 
* Odśwież kartę wystąpienia obliczeniowe.
* Uruchamianie, zatrzymywanie i ponowne uruchamianie wystąpienia obliczeniowego.  Płatność za wystąpienie jest dokonywana za każdym razem, gdy jest ono uruchomione. Zatrzymaj wystąpienie obliczeniowe, gdy nie jest używane, aby obniżyć koszty. Zatrzymywanie wystąpienia obliczeniowego powoduje jego przydział. Następnie uruchom ją ponownie, gdy będzie potrzebna.
* Usuń wystąpienie obliczeniowe.
* Przefiltruj listę wystąpień obliczeniowych, aby wyświetlić tylko te, które zostały utworzone.

Dla każdego wystąpienia obliczeniowego w utworzonym obszarze roboczym (lub który został utworzony przez Ciebie) można:

* Dostęp do Jupyter, JupyterLab, RStudio w wystąpieniu obliczeniowym
* Użyj protokołu SSH do wystąpienia obliczeniowego. Dostęp SSH jest domyślnie wyłączony, ale można go włączyć podczas tworzenia wystąpienia obliczeniowego. Dostęp SSH odbywa się za pośrednictwem mechanizmu publicznego/prywatnego klucza. Karta przekaże szczegóły dotyczące połączenia SSH, takie jak adres IP, nazwa użytkownika i numer portu.
* Pobierz szczegóły dotyczące określonego wystąpienia obliczeniowego, takiego jak adres IP i region.

---

[Usługa Azure RBAC](../role-based-access-control/overview.md) pozwala kontrolować, którzy użytkownicy w obszarze roboczym mogą tworzyć, usuwać, uruchamiać, zatrzymywać, ponownie uruchamiać wystąpienie obliczeniowe. Wszyscy użytkownicy z rolą współautor i właściciel obszaru roboczego mogą tworzyć, usuwać, uruchamiać, zatrzymywać i ponownie uruchamiać wystąpienia obliczeniowe w obszarze roboczym. Jednak tylko twórca określonego wystąpienia obliczeniowego lub użytkownik przypisany, jeśli został utworzony w ich imieniu, może uzyskać dostęp do Jupyter, JupyterLab i RStudio na tym wystąpieniu obliczeniowym. Wystąpienie obliczeniowe jest przeznaczone dla pojedynczego użytkownika, który ma dostęp do katalogu głównego, i może być terminalem za pomocą Jupyter/JupyterLab/RStudio. Wystąpienie obliczeniowe będzie zawierać Logowanie jednokrotne, a wszystkie akcje będą korzystały z tożsamości tego użytkownika dla usługi Azure RBAC oraz do naliczania przebiegów eksperymentów. Dostęp SSH jest kontrolowany za pośrednictwem mechanizmu publicznego/prywatnego klucza.

Te akcje mogą być kontrolowane przez funkcję RBAC platformy Azure:
* *Microsoft. MachineLearningServices/obszary robocze/obliczenia/odczyt*
* *Microsoft. MachineLearningServices/obszary robocze/obliczenia/zapis*
* *Microsoft. MachineLearningServices/obszary robocze/obliczenia/usuwanie*
* *Microsoft. MachineLearningServices/obszary robocze/obliczenia/uruchomienie/akcja*
* *Microsoft. MachineLearningServices/obszary robocze/obliczenia/akcja*
* *Microsoft. MachineLearningServices/obszary robocze/obliczenia/ponowne uruchomienie/akcja*


## <a name="access-the-terminal-window"></a>Dostęp do okna terminalu

Otwórz okno terminalu wystąpienia obliczeniowego w dowolny z następujących sposobów:

* RStudio: Wybierz kartę **terminalu** u góry po lewej stronie.
* Jupyter Lab: wybierz kafelek **terminalu** pod **innym** nagłówkiem na karcie Uruchamianie.
* Jupyter: wybierz pozycję **Nowy Terminal>** w prawym górnym rogu na karcie pliki.
* Protokół SSH do maszyny, jeśli włączono dostęp SSH podczas tworzenia wystąpienia obliczeniowego.

Użyj okna terminalu, aby zainstalować pakiety i utworzyć dodatkowe jądra.

## <a name="install-packages"></a>Zainstaluj pakiety

Pakiety można instalować bezpośrednio w Jupyter Notebook lub RStudio:

* RStudio Użyj karty **pakiety** w prawym dolnym rogu lub karty **konsoli** w lewym górnym rogu.  
* Python: Dodawanie kodu instalacji i wykonywanie w komórce Jupyter Notebook.

Można też zainstalować program z poziomu okna terminalu. Zainstaluj pakiety języka Python w środowisku **python 3,6-Azure** .  Zainstaluj pakiety języka R w środowisku **języka r** .

> [!NOTE]
> Aby zarządzać pakietami w ramach notesu, użyj funkcji **% PIP** lub **% Conda** Magic w celu automatycznego zainstalowania pakietów w **aktualnie uruchomionym jądrze** zamiast **! PIP** lub **! Conda** , które odwołują się do wszystkich pakietów (w tym pakietów poza aktualnie uruchomionym jądrem).

## <a name="add-new-kernels"></a>Dodaj nowe jądra

> [!WARNING]
>  Podczas dostosowywania wystąpienia obliczeniowego upewnij się, że nie usuniesz środowiska **azureml_py36** Conda ani jądra **Python 3,6-Azure** . Jest to niezbędny w przypadku funkcji Jupyter/JupyterLab

Aby dodać nowe jądro Jupyter do wystąpienia obliczeniowego:

1. Tworzenie nowego terminalu z poziomu okienka Jupyter, JupyterLab lub z notesów lub z poziomu protokołu SSH do wystąpienia obliczeniowego
2. Użyj okna terminalu, aby utworzyć nowe środowisko.  Na przykład poniższy kod tworzy `newenv` :

    ```shell
    conda create --name newenv
    ```

3. Aktywuj środowisko.  Na przykład po utworzeniu `newenv` :

    ```shell
    conda activate newenv
    ```

4. Zainstaluj pakiet PIP i ipykernel w nowym środowisku i Utwórz jądro dla tej Conda ENV

    ```shell
    conda install pip
    conda install ipykernel
    python -m ipykernel install --user --name newenv --display-name "Python (newenv)"
    ```

Można zainstalować dowolne z [dostępnych jądra Jupyter](https://github.com/jupyter/jupyter/wiki/Jupyter-kernels) .



## <a name="next-steps"></a>Następne kroki

* [Prześlij przebieg szkoleniowy](how-to-set-up-training-targets.md)