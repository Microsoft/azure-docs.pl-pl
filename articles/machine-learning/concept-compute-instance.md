---
title: Co to jest wystąpienie obliczeniowe Azure Machine Learning?
titleSuffix: Azure Machine Learning
description: Dowiedz się więcej na temat wystąpienia obliczeniowego Azure Machine Learning — w pełni zarządzanej stacji roboczej opartej na chmurze.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 12/13/2019
ms.openlocfilehash: 9f10825d8f22a7ddd9858e86dc2083ae47da9a69
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83712761"
---
# <a name="what-is-an-azure-machine-learning-compute-instance"></a>Co to jest wystąpienie obliczeniowe Azure Machine Learning?

Wystąpienie obliczeniowe Azure Machine Learning (wersja zapoznawcza) to w pełni zarządzana stacja robocza oparta na chmurze dla analityków danych. 

Wystąpienia obliczeniowe ułatwiają rozpoczęcie pracy z programowaniem Azure Machine Learning, a także zapewnianie możliwości zarządzania i gotowości przedsiębiorstwa dla administratorów IT.  

Użyj wystąpienia obliczeniowego jako w pełni skonfigurowanego i zarządzanego środowiska programistycznego w chmurze.

Wystąpienia obliczeniowe są zwykle używane jako środowiska deweloperskie.  Mogą one również służyć jako cel obliczeniowy do szkolenia i inferencing na potrzeby programowania i testowania.  W przypadku dużych zadań [klaster obliczeniowy Azure Machine Learning](how-to-set-up-training-targets.md#amlcompute) z funkcjami skalowania wielu węzłów jest lepszym rozwiązaniem docelowym obliczeń.


## <a name="why-use-a-compute-instance"></a>Dlaczego warto używać wystąpienia obliczeniowego?

Wystąpienie obliczeniowe to w pełni zarządzana stacja robocza oparta na chmurze zoptymalizowana pod kątem środowiska projektowego uczenia maszynowego. Zapewnia następujące korzyści:

|Najważniejsze korzyści||
|----|----|
|Produktywność|Analityki danych mogą tworzyć i wdrażać modele przy użyciu zintegrowanych notesów i następujących narzędzi w swojej przeglądarce internetowej:<br/>-Jupyter<br/>- JupyterLab<br/>-RStudio|
|Zarządzane & bezpieczne|Zmniejsz poziom bezpieczeństwa i Dodaj zgodność z wymaganiami dotyczącymi zabezpieczeń przedsiębiorstwa. Wystąpienia obliczeniowe zapewniają niezawodne zasady zarządzania i bezpieczne konfiguracje sieci, takie jak:<br/><br/>— Autoinicjowanie obsługi z szablonów Menedżer zasobów lub Azure Machine Learning SDK<br/>- [Kontrola dostępu oparta na rolach (RBAC)](/azure/role-based-access-control/overview)<br/>- [Obsługa sieci wirtualnej](how-to-enable-virtual-network.md#compute-instance)<br/>-Zasady protokołu SSH do włączania/wyłączania dostępu SSH|
|Wstępnie skonfigurowany &nbsp; lub &nbsp; ml|Oszczędź czas na zadaniach instalacyjnych ze wstępnie skonfigurowanymi i aktualnymi pakietami ML, platformami uczenia głębokiego, sterownikami procesora GPU.|
|W pełni dostosowywalne|Szeroka pomoc techniczna dla typów maszyn wirtualnych platformy Azure, w tym GPU i utrwalone dostosowanie niskiego poziomu, takie jak instalowanie pakietów i sterowników, sprawia, że zaawansowane scenariusze to Breeze. |

## <a name="tools-and-environments"></a><a name="contents"></a>Narzędzia i środowiska

Wystąpienie obliczeniowe Azure Machine Learning umożliwia tworzenie, uczenie i wdrażanie modeli w pełni zintegrowane środowisko notesu w obszarze roboczym.


Te narzędzia i środowiska są zainstalowane w wystąpieniu obliczeniowym: 

|Narzędzia ogólne & środowiska|Szczegóły|
|----|:----:|
|Sterowniki|`CUDA`</br>`cuDNN`</br>`NVIDIA`</br>`Blob FUSE` |
|Biblioteka Intel MPI||
|Interfejs wiersza polecenia platformy Azure ||
|Przykłady Azure Machine Learning ||
|Docker||
|Nginx||
|NCCL 2,0 ||
|Protobuf|| 

|Środowiska **R** Tools & Environments|Szczegóły|
|----|:----:|
|RStudio Server Open Source Edition||
|Jądro języka R||
|Zestaw SDK Azure Machine Learning dla języka R|[azuremlsdk](https://azure.github.io/azureml-sdk-for-r/reference/index.html)</br>Przykłady dla zestawu SDK|

|Środowiska **Python** Tools & Environments|Szczegóły|
|----|----|
|Anaconda Python||
|Jupyter i rozszerzenia||
|Jupyterlab i rozszerzenia||
[Zestaw SDK usługi Azure Machine Learning dla języka Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)</br>z PyPI|`azureml-sdk[notebooks,contrib,automl,explain]`</br>`azureml-contrib-datadrift`</br>`azureml-telemetry`</br>`azureml-tensorboard`</br>`azureml-contrib-opendatasets`</br>`azureml-opendatasets`</br>`azureml-contrib-reinforcementlearning`</br>`azureml-mlflow`</br>`azureml-contrib-interpret` |
|Inne pakiety PyPI|`jupytext`</br>`tensorboard`</br>`nbconvert`</br>`notebook`</br>`Pillow`|
|Pakiety Conda|`cython`</br>`numpy`</br>`ipykernel`</br>`scikit-learn`</br>`matplotlib`</br>`tqdm`</br>`joblib`</br>`nodejs`</br>`nb_conda_kernels`|
|Pakiety uczenia głębokiego|`PyTorch`</br>`TensorFlow`</br>`Keras`</br>`Horovod`</br>`MLFlow`</br>`pandas-ml`</br>`scrapbook`|
|Pakiety ONNX|`keras2onnx`</br>`onnx`</br>`onnxconverter-common`</br>`skl2onnx`</br>`onnxmltools`|
|Przykłady zestawu SDK języka Azure Machine Learning Python & R||

Wszystkie pakiety języka Python są zainstalowane w środowisku **python 3,6-Azure** .  

Wystąpienia obliczeniowe są zwykle używane jako środowiska deweloperskie.  Mogą one również służyć jako cel obliczeniowy do szkolenia i inferencing na potrzeby programowania i testowania.  W przypadku dużych zadań [klaster obliczeniowy Azure Machine Learning](how-to-set-up-training-targets.md#amlcompute) z funkcjami skalowania wielu węzłów jest lepszym rozwiązaniem docelowym obliczeń.

### <a name="installing-packages"></a>Instalowanie pakietów

Pakiety można instalować bezpośrednio w notesie Jupyter lub RStudio:

* RStudio Użyj karty **pakiety** w prawym dolnym rogu lub karty **konsoli** w lewym górnym rogu.  
* Python: Dodawanie kodu instalacji i wykonywanie w komórce notesu Jupyter.

Możesz też uzyskać dostęp do okna terminalu w dowolny z następujących sposobów:

* RStudio: Wybierz kartę **terminalu** u góry po lewej stronie.
* Jupyter Lab: wybierz kafelek **terminalu** pod **innym** nagłówkiem na karcie Uruchamianie.
* Jupyter: wybierz pozycję **Nowy Terminal>** w prawym górnym rogu na karcie pliki.
* SSH z maszyną.  Następnie zainstaluj pakiety Python w środowisku **python 3,6-Azure** .  Zainstaluj pakiety języka R w środowisku **języka r** .

## <a name="accessing-files"></a>Uzyskiwanie dostępu do plików

Notesy i skrypty języka R są przechowywane na domyślnym koncie magazynu Twojego obszaru roboczego w udziale plików platformy Azure.  Te pliki znajdują się w katalogu "pliki użytkownika". Ten magazyn ułatwia udostępnianie notesów między wystąpieniami obliczeniowymi. Konto magazynu umożliwia również bezpieczne zachowywanie Twoich notesów po zatrzymaniu lub usunięciu wystąpienia obliczeniowego.

Konto udziału plików platformy Azure w obszarze roboczym jest zainstalowane jako dysk w wystąpieniu obliczeniowym. Ten dysk jest domyślnym katalogiem roboczym dla Jupyter, Jupyter Labs i RStudio.

Pliki w udziale plików są dostępne we wszystkich wystąpieniach obliczeniowych w tym samym obszarze roboczym. Wszelkie zmiany tych plików w wystąpieniu obliczeniowym będą niezawodnie utrwalane w udziale plików.

Najnowsze przykłady Azure Machine Learning można także klonować do folderu w katalogu plików użytkownika w udziale plików obszaru roboczego.

Pisanie małych plików może być wolniejsze na dyskach sieciowych niż w przypadku zapisu do samej maszyny wirtualnej.  Jeśli piszesz wiele małych plików, spróbuj użyć katalogu bezpośrednio w wystąpieniu obliczeniowym, takim jak `/tmp` katalog. Pamiętaj, że te pliki nie będą dostępne z innych wystąpień obliczeniowych w obszarze roboczym.

## <a name="managing-a-compute-instance"></a>Zarządzanie wystąpieniem obliczeniowym

W obszarze roboczym programu Azure Machine Learning Studio wybierz pozycję **obliczenia**, a następnie na górze wybierz pozycję **wystąpienie obliczeniowe** .

![Zarządzanie wystąpieniem obliczeniowym](./media/concept-compute-instance/manage-compute-instance.png)

Można wykonać następujące czynności:

* Tworzenie wystąpienia obliczeniowego. Określ nazwę, typ maszyny wirtualnej platformy Azure, w tym procesory GPU (Uwaga: nie można zmienić typu maszyny wirtualnej po utworzeniu), Włącz/Wyłącz dostęp SSH i skonfiguruj ustawienia sieci wirtualnej opcjonalnie. Można również utworzyć wystąpienie bezpośrednio z poziomu zintegrowanych notesów, Azure Portal, szablonu Menedżer zasobów lub zestawu SDK Azure Machine Learning. Przydziały dedykowanych rdzeni na region, które mają zastosowanie do tworzenia wystąpienia obliczeniowego, są ujednolicone i udostępniane Azure Machine Learning przydziału klastra obliczeniowego.
* Odśwież kartę wystąpienia obliczeniowe
* Uruchamianie, zatrzymywanie i ponowne uruchamianie wystąpienia obliczeniowego. Zatrzymaj maszynę wirtualną, gdy nie używasz jej do obniżenia kosztów. Następnie uruchom ją ponownie, gdy będzie potrzebna.
* Usuwanie wystąpienia obliczeniowego

Dla każdego wystąpienia obliczeniowego w obszarze roboczym możesz:

* Dostęp do Jupyter, JupyterLab, RStudio w wystąpieniu obliczeniowym
* Użyj protokołu SSH do wystąpienia obliczeniowego. Dostęp SSH jest domyślnie wyłączony, ale można go włączyć podczas tworzenia wystąpienia obliczeniowego. Dostęp SSH odbywa się za pośrednictwem mechanizmu publicznego/prywatnego klucza. Karta przekaże szczegóły dotyczące połączenia SSH, takie jak adres IP, nazwa użytkownika i numer portu.
* Pobierz szczegóły dotyczące określonego wystąpienia obliczeniowego, takiego jak adres IP i region.

[RBAC](/azure/role-based-access-control/overview) pozwala kontrolować, którzy użytkownicy w obszarze roboczym mogą tworzyć, usuwać, uruchamiać, zatrzymywać, ponownie uruchamiać wystąpienie obliczeniowe. Wszyscy użytkownicy z rolą współautor i właściciel obszaru roboczego mogą tworzyć, usuwać, uruchamiać, zatrzymywać i ponownie uruchamiać wystąpienia obliczeniowe w obszarze roboczym. Jednak tylko twórca określonego wystąpienia obliczeniowego może uzyskać dostęp do Jupyter, JupyterLab i RStudio na tym wystąpieniu obliczeniowym. Twórca wystąpienia obliczeniowego ma dla nich przypisane wystąpienie obliczeniowe, ma dostęp do katalogu głównego i może być terminalem za pomocą Jupyter. Wystąpienie obliczeniowe będzie miało jednokrotne logowanie użytkownika Creator, a wszystkie akcje będą używać tożsamości tego użytkownika do kontroli RBAC i naliczania przebiegów eksperymentu. Dostęp SSH jest kontrolowany za pośrednictwem mechanizmu publicznego/prywatnego klucza.

Można również utworzyć wystąpienie
* Bezpośrednio w środowisku zintegrowanych notesów
* W Azure Portal
* Z szablonu Azure Resource Manager
* Z zestawem SDK Azure Machine Learning

Przydziały dedykowanych rdzeni na region, które mają zastosowanie do tworzenia wystąpienia obliczeniowego, są ujednolicone i udostępniane przy użyciu Azure Machine Learninggo przydziału klastra szkoleniowego. 

## <a name="compute-target"></a>Docelowy obiekt obliczeniowy

Wystąpienia obliczeniowe mogą służyć jako [obiekt docelowy obliczeń szkoleniowych](concept-compute-target.md#train) podobny do klastrów szkoleniowych Azure Machine Learning COMPUTE. Zainicjuj obsługę wieloprocesorowej maszyny wirtualnej w celu uruchamiania zadań szkolenia rozproszonego przy użyciu TensorFlow/PyTorch szacowania. Możesz również utworzyć konfigurację uruchomieniową i użyć jej do uruchomienia eksperymentu w wystąpieniu obliczeniowym. Wystąpienia obliczeniowego można użyć jako lokalnego celu wdrożenia inferencing na potrzeby scenariuszy testowania/debugowania.

## <a name="what-happened-to-notebook-vm"></a><a name="notebookvm"></a>Co się stało z maszyną wirtualną notesu?

Wystąpienia obliczeniowe zamieniają maszynę wirtualną notesu.  

Wszystkie pliki notesu przechowywane w udziale plików obszaru roboczego i dane w magazynach danych obszaru roboczego będą dostępne z wystąpienia obliczeniowego. Jednak wszystkie pakiety niestandardowe zainstalowane wcześniej na maszynie wirtualnej notesu będą musiały zostać ponownie zainstalowane w wystąpieniu obliczeniowym. Ograniczenia przydziałów, które dotyczą tworzenia klastrów obliczeniowych, będą również stosowane do tworzenia wystąpień obliczeniowych. 

Nie można utworzyć nowych maszyn wirtualnych notesu. Można jednak nadal uzyskiwać dostęp do utworzonych maszyn wirtualnych Notes i korzystać z nich z pełną funkcjonalnością. Wystąpienia obliczeniowe można tworzyć w tym samym obszarze roboczym, co istniejące maszyny wirtualne notesu. 


## <a name="next-steps"></a>Następne kroki

 * [Samouczek: uczenie swojego pierwszego modelu ml](tutorial-1st-experiment-sdk-train.md) pokazuje, jak używać wystąpienia obliczeniowego z zintegrowanym notesem.
