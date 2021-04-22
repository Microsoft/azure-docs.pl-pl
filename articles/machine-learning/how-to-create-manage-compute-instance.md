---
title: Tworzenie wystąpienia obliczeniowego i zarządzanie nimi
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak utworzyć wystąpienie obliczeniowe Azure Machine Learning zarządzać nimi. Należy używać jako środowiska projektowego lub jako docelowego obiektu obliczeniowego na potrzeby tworzenia i testowania.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli
ms.author: sgilley
author: sdgilley
ms.reviewer: sgilley
ms.date: 10/02/2020
ms.openlocfilehash: 5ac525ae062efca25601c9e63a5c8f16f2be29be
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861232"
---
# <a name="create-and-manage-an-azure-machine-learning-compute-instance"></a>Tworzenie wystąpienia obliczeniowego Azure Machine Learning zarządzanie nimi

Dowiedz się, jak utworzyć wystąpienie obliczeniowe i [zarządzać nimi](concept-compute-instance.md) w Azure Machine Learning roboczym.

Używaj wystąpienia obliczeniowego jako w pełni skonfigurowanego i zarządzanego środowiska projektowego w chmurze. Na użytek testowania i testowania można również użyć wystąpienia jako docelowego obiektu obliczeniowego trenowania [lub](concept-compute-target.md#train) [docelowego wnioskowania.](concept-compute-target.md#deploy)   Wystąpienie obliczeniowe może uruchamiać wiele zadań równolegle i ma kolejkę zadań. Jako środowisko projektowe wystąpienie obliczeniowe nie może być udostępniane innym użytkownikom w Twoim obszarze roboczym.

W tym artykule omówiono sposób wykonywania następujących zadań:

* Tworzenie wystąpienia obliczeniowego 
* Zarządzanie wystąpieniem obliczeniowym (uruchamianie, zatrzymywanie, ponowne uruchamianie, usuwanie)
* Uzyskiwanie dostępu do okna terminalu 
* Instalowanie pakietów języka R lub Python
* Tworzenie nowych środowisk lub jąder Jupyter

Wystąpienia obliczeniowe mogą bezpiecznie uruchamiać zadania w środowisku sieci [wirtualnej](how-to-secure-training-vnet.md)bez konieczności otwierania portów SSH przez przedsiębiorstwa. Zadanie jest wykonywane w środowisku konteneryzowym i pakuje zależności modelu w kontenerze platformy Docker. 

## <a name="prerequisites"></a>Wymagania wstępne

* Obszar roboczy usługi Azure Machine Learning. Aby uzyskać więcej informacji, zobacz [Tworzenie Azure Machine Learning roboczego.](how-to-manage-workspace.md)

* Rozszerzenie [interfejsu wiersza polecenia](reference-azure-machine-learning-cli.md)platformy Azure Machine Learning service , Azure Machine Learning python [SDK](/python/api/overview/azure/ml/intro)lub rozszerzenie [Azure Machine Learning Visual Studio Code .](tutorial-setup-vscode-extension.md)

## <a name="create"></a>Utwórz

**Szacowany czas:** około 5 minut.

Tworzenie wystąpienia obliczeniowego jest procesem tylko raz dla obszaru roboczego. Możesz ponownie użyć zasobów obliczeniowych jako dewelopera stacji roboczej lub jako docelowego obiektu obliczeniowego do trenowania. Do obszaru roboczego może być dołączonych wiele wystąpień obliczeniowych.

Dedykowane rdzenie na region na limit przydziału rodziny maszyn wirtualnych i całkowity limit przydziału regionalnego, który ma zastosowanie do tworzenia wystąpień obliczeniowych, są ujednolicone i współdzielone z Azure Machine Learning przydziału klastra obliczeniowego trenowania. Zatrzymanie wystąpienia obliczeniowego nie zwalnia limitu przydziału, aby zapewnić możliwość ponownego uruchomienia wystąpienia obliczeniowego. Pamiętaj, że nie można zmienić rozmiaru maszyny wirtualnej wystąpienia obliczeniowego po jego utworzeniu.

W poniższym przykładzie pokazano, jak utworzyć wystąpienie obliczeniowe:

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

* [ComputeInstance, klasa](/python/api/azureml-core/azureml.core.compute.computeinstance.computeinstance)
* [ComputeTarget.create](/python/api/azureml-core/azureml.core.compute.computetarget#create-workspace--name--provisioning-configuration-)
* [ComputeInstance.wait_for_completion](/python/api/azureml-core/azureml.core.compute.computeinstance(class)#wait-for-completion-show-output-false--is-delete-operation-false-)


# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
az ml computetarget create computeinstance  -n instance -s "STANDARD_D3_V2" -v
```

Aby uzyskać więcej informacji, zobacz [az ml computetarget create computeinstance reference (Az ml computetarget create computeinstance](/cli/azure/ml/computetarget/create#az_ml_computetarget_create_computeinstance) reference).

# <a name="studio"></a>[Studio](#tab/azure-studio)

W obszarze roboczym Azure Machine Learning studio utwórz nowe wystąpienie obliczeniowe w  sekcji Obliczenia lub w sekcji **Notesy,** gdy wszystko będzie gotowe do uruchomienia jednego z notesów.

Aby uzyskać informacje na temat tworzenia wystąpienia obliczeniowego w studio, zobacz Create compute targets in Azure Machine Learning studio (Tworzenie docelowych obiektów obliczeniowych w [programie Azure Machine Learning studio).](how-to-create-attach-compute-studio.md#compute-instance)

---

Wystąpienie obliczeniowe można również utworzyć za pomocą szablonu [Azure Resource Manager obliczeniowego](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-computeinstance). 

### <a name="create-on-behalf-of-preview"></a>Utwórz w imieniu użytkownika (wersja zapoznawcza)

Jako administrator możesz utworzyć wystąpienie obliczeniowe w imieniu analityka danych i przypisać do nich wystąpienie za pomocą:
* [Azure Resource Manager szablonu .](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-computeinstance)  Aby uzyskać szczegółowe informacje na temat sposobu znalezienia identyfikatorów TenantID i ObjectID wymaganych w tym szablonie, zobacz Znajdowanie identyfikatorów obiektów [tożsamości na potrzeby konfiguracji uwierzytelniania.](../healthcare-apis/fhir/find-identity-object-ids.md)  Te wartości można również znaleźć w portalu Azure Active Directory portal.
* Interfejs API REST

Analityk danych, dla którego tworzysz wystąpienie obliczeniowe, musi mieć następujące uprawnienia: Kontrola dostępu na podstawie ról [(RBAC)](../role-based-access-control/overview.md) platformy Azure: 
* *Microsoft.MachineLearningServices/workspaces/computes/start/action*
* *Microsoft.MachineLearningServices/workspaces/computes/stop/action*
* *Microsoft.MachineLearningServices/workspaces/computes/restart/action*
* *Microsoft.MachineLearningServices/workspaces/computes/applicationaccess/action*

Analityk danych może uruchomić, zatrzymać i ponownie uruchomić wystąpienie obliczeniowe. Mogą używać wystąpienia obliczeniowego do:
* Jupyter
* JupyterLab
* RStudio
* Zintegrowane notesy

## <a name="manage"></a>Zarządzanie

Uruchamianie, zatrzymywanie, ponowne uruchamianie i usuwanie wystąpienia obliczeniowego. Wystąpienie obliczeniowe nie jest automatycznie skalowane w dół, dlatego należy zatrzymać zasób, aby zapobiec bieżącym opłatom.

> [!TIP]
> Wystąpienie obliczeniowe ma dysk systemu operacyjnego o pojemności 120 GB. Jeśli zabraknie miejsca na dysku, użyj [terminalu,](how-to-access-terminal.md) aby wyczyścić co najmniej 1–2 GB przed zatrzymaniem lub ponownym uruchomieniem wystąpienia obliczeniowego.

# <a name="python"></a>[Python](#tab/python)

W poniższych przykładach nazwa wystąpienia obliczeniowego to **wystąpienie**

* Uzyskiwanie stanu

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

* Usuń

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

    Aby uzyskać więcej informacji, zobacz [az ml computetarget stop computeinstance](/cli/azure/ml/computetarget/computeinstance#az_ml_computetarget_computeinstance_stop).

* Rozpocznij 

    ```azurecli-interactive
    az ml computetarget start computeinstance -n instance -v
    ```

    Aby uzyskać więcej informacji, zobacz [az ml computetarget start computeinstance](/cli/azure/ml/computetarget/computeinstance#az_ml_computetarget_computeinstance_start).

* Uruchom ponownie 

    ```azurecli-interactive
    az ml computetarget restart computeinstance -n instance -v
    ```

    Aby uzyskać więcej informacji, zobacz [az ml computetarget restart computeinstance](/cli/azure/ml/computetarget/computeinstance#az_ml_computetarget_computeinstance_restart).

* Usuń

    ```azurecli-interactive
    az ml computetarget delete -n instance -v
    ```

    Aby uzyskać więcej informacji, zobacz [az ml computetarget delete computeinstance](/cli/azure/ml/computetarget#az_ml_computetarget_delete).

# <a name="studio"></a>[Studio](#tab/azure-studio)

W obszarze roboczym w Azure Machine Learning studio wybierz pozycję **Obliczenia,** a następnie wybierz pozycję **Wystąpienie obliczeniowe** u góry.

![Zarządzanie wystąpieniem obliczeniowym](./media/concept-compute-instance/manage-compute-instance.png)

Można wykonać następujące czynności:

* Tworzenie nowego wystąpienia obliczeniowego 
* Odśwież kartę wystąpień obliczeniowych.
* Uruchamianie, zatrzymywanie i ponowne uruchamianie wystąpienia obliczeniowego.  Płacisz za wystąpienie zawsze wtedy, gdy jest uruchomione. Zatrzymaj wystąpienie obliczeniowe, gdy nie używasz go w celu obniżenia kosztów. Zatrzymanie wystąpienia obliczeniowego cofnie jego alokację. Następnie uruchom go ponownie, gdy będzie potrzebny.
* Usuwanie wystąpienia obliczeniowego.
* Przefiltruj listę wystąpień obliczeniowych, aby wyświetlić tylko te, które zostały utworzone.

Dla każdego wystąpienia obliczeniowego w utworzonym obszarze roboczym (lub utworzonym automatycznie) można:

* Uzyskiwanie dostępu do aplikacji Jupyter, JupyterLab i RStudio w wystąpieniu obliczeniowym
* SSH do wystąpienia obliczeniowego. Dostęp SSH jest domyślnie wyłączony, ale można go włączyć podczas tworzenia wystąpienia obliczeniowego. Dostęp za pomocą SSH jest za pośrednictwem mechanizmu klucza publicznego/prywatnego. Karta zawiera szczegółowe informacje dotyczące połączenia SSH, takie jak adres IP, nazwa użytkownika i numer portu.
* Uzyskaj szczegółowe informacje o określonym wystąpieniu obliczeniowym, takie jak adres IP i region.

---


[Kontrola RBAC platformy Azure](../role-based-access-control/overview.md) umożliwia kontrolowanie, którzy użytkownicy w obszarze roboczym mogą tworzyć, usuwać, uruchamiać, zatrzymywać i ponownie uruchamiać wystąpienie obliczeniowe. Wszyscy użytkownicy w roli współautora i właściciela obszaru roboczego mogą tworzyć, usuwać, uruchamiać, zatrzymywać i ponownie uruchamiać wystąpienia obliczeniowe w obszarze roboczym. Jednak tylko twórca określonego wystąpienia obliczeniowego lub użytkownik przypisany, jeśli zostało utworzone w jego imieniu, może uzyskać dostęp do aplikacji Jupyter, JupyterLab i RStudio w tym wystąpieniu obliczeniowym. Wystąpienie obliczeniowe jest przeznaczone dla jednego użytkownika, który ma dostęp z uprawnieniami administratora i może być terminalem za pośrednictwem programu Jupyter/JupyterLab/RStudio. Wystąpienie obliczeniowe będzie logować się za pomocą jednego użytkownika, a wszystkie akcje będą używać tożsamości tego użytkownika do kontroli RBAC platformy Azure i przypisania przebiegów eksperymentu. Dostęp do SSH jest kontrolowany za pomocą mechanizmu klucza publicznego/prywatnego.

Te akcje mogą być kontrolowane przez kontrolę RBAC platformy Azure:
* *Microsoft.MachineLearningServices/workspaces/computes/read*
* *Microsoft.MachineLearningServices/workspaces/computes/write*
* *Microsoft.MachineLearningServices/workspaces/computes/delete*
* *Microsoft.MachineLearningServices/workspaces/computes/start/action*
* *Microsoft.MachineLearningServices/workspaces/computes/stop/action*
* *Microsoft.MachineLearningServices/workspaces/computes/restart/action*

## <a name="next-steps"></a>Następne kroki

* [Uzyskiwanie dostępu do terminalu wystąpienia obliczeniowego](how-to-access-terminal.md)
* [Tworzenie plików i zarządzanie nimi](how-to-manage-files.md)
* [Przesyłanie uruchomienia trenowania](how-to-set-up-training-targets.md)
