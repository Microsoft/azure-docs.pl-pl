---
title: Co to jest wystąpienie obliczeniowe usługi Azure Machine Learning?
titleSuffix: Azure Machine Learning
description: Dowiedz się więcej na temat wystąpienia obliczeniowego Azure Machine Learning — w pełni zarządzanej stacji roboczej opartej na chmurze.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 10/02/2020
ms.openlocfilehash: 309cf3882ade99de3f2e29a037d20ca50e35f490
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106066674"
---
# <a name="what-is-an-azure-machine-learning-compute-instance"></a>Co to jest wystąpienie obliczeniowe usługi Azure Machine Learning?

Wystąpienie obliczeniowe Azure Machine Learning to zarządzana stacja robocza oparta na chmurze dla analityków danych.

Wystąpienia obliczeniowe ułatwiają rozpoczęcie pracy z programowaniem Azure Machine Learning, a także zapewnianie możliwości zarządzania i gotowości przedsiębiorstwa dla administratorów IT.  

Użyj wystąpienia obliczeniowego jako w pełni skonfigurowanego i zarządzanego środowiska programistycznego w chmurze na potrzeby uczenia maszynowego. Mogą one również służyć jako obiekt docelowy obliczeń na potrzeby szkoleń i inferencing na potrzeby tworzenia i testowania.  

Aby uzyskać szkolenia modelu klasy produkcyjnej, użyj [Azure Machine Learning klastra obliczeniowego](how-to-create-attach-compute-cluster.md) z możliwościami skalowania wielowęzłowego. W celu wdrożenia modelu klasy produkcyjnej należy użyć [klastra usługi Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md).

Aby funkcja Jupyter wystąpienia obliczeniowego działała, należy się upewnić, że komunikacja gniazda internetowego nie jest wyłączona. Upewnij się, że sieć zezwala na połączenia protokołu WebSocket z *. instances.azureml.net i *. instances.azureml.ms.

## <a name="why-use-a-compute-instance"></a>Dlaczego warto używać wystąpienia obliczeniowego?

Wystąpienie obliczeniowe to w pełni zarządzana stacja robocza oparta na chmurze zoptymalizowana pod kątem środowiska projektowego uczenia maszynowego. Zapewnia następujące korzyści:

|Najważniejsze korzyści|Opis|
|----|----|
|Produktywność|Możesz tworzyć i wdrażać modele przy użyciu zintegrowanych notesów i następujących narzędzi w programie Azure Machine Learning Studio:<br/>-Jupyter<br/>- JupyterLab<br/>-RStudio (wersja zapoznawcza)<br/>Wystąpienie obliczeniowe jest w pełni zintegrowane z Azure Machine Learning obszarem roboczym i Studio. Notesy i dane można udostępniać innym analitykom danych w obszarze roboczym.<br/> Możesz również użyć [vs Code](https://techcommunity.microsoft.com/t5/azure-ai/power-your-vs-code-notebooks-with-azml-compute-instances/ba-p/1629630) z wystąpieniami obliczeniowymi.
|Zarządzane & bezpieczne|Zmniejsz poziom bezpieczeństwa i Dodaj zgodność z wymaganiami dotyczącymi zabezpieczeń przedsiębiorstwa. Wystąpienia obliczeniowe zapewniają niezawodne zasady zarządzania i bezpieczne konfiguracje sieci, takie jak:<br/><br/>-Autoudostępnianie z szablonów Menedżer zasobów lub Azure Machine Learning SDK<br/>- [Kontrola dostępu oparta na rolach (Azure RBAC)](../role-based-access-control/overview.md)<br/>- [Obsługa sieci wirtualnej](./how-to-secure-training-vnet.md#compute-instance)<br/>-Zasady protokołu SSH do włączania/wyłączania dostępu SSH<br/>Włączono protokół TLS 1,2 |
|Wstępnie skonfigurowane &nbsp; dla &nbsp; ml|Oszczędź czas na zadaniach instalacyjnych ze wstępnie skonfigurowanymi i aktualnymi pakietami ML, platformami uczenia głębokiego, sterownikami procesora GPU.|
|W pełni dostosowywalne|Szeroka pomoc techniczna dla typów maszyn wirtualnych platformy Azure, w tym GPU i utrwalone dostosowanie niskiego poziomu, takie jak instalowanie pakietów i sterowników, sprawia, że zaawansowane scenariusze to Breeze. |

Można [utworzyć wystąpienie obliczeniowe](how-to-create-manage-compute-instance.md?tabs=python#create) samodzielnie lub administrator może [utworzyć wystąpienie obliczeniowe dla Ciebie](how-to-create-manage-compute-instance.md?tabs=python#create-on-behalf-of-preview).

## <a name="tools-and-environments"></a><a name="contents"></a>Narzędzia i środowiska

> [!IMPORTANT]
> Elementy oznaczone (wersja zapoznawcza) w tym artykule są obecnie dostępne w publicznej wersji zapoznawczej.
> Wersja zapoznawcza jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Wystąpienie obliczeniowe Azure Machine Learning umożliwia tworzenie, uczenie i wdrażanie modeli w pełni zintegrowane środowisko notesu w obszarze roboczym.

Notesy Jupyter można uruchamiać w [vs Code](https://techcommunity.microsoft.com/t5/azure-ai/power-your-vs-code-notebooks-with-azml-compute-instances/ba-p/1629630) przy użyciu wystąpienia obliczeniowego jako serwera zdalnego bez konieczności używania protokołu SSH. Możesz również włączyć integrację VS Code za pośrednictwem [zdalnego rozszerzenia SSH](https://devblogs.microsoft.com/python/enhance-your-azure-machine-learning-experience-with-the-vs-code-extension/).

Można [zainstalować pakiety](how-to-access-terminal.md#install-packages) i [dodać jądra](how-to-access-terminal.md#add-new-kernels) do wystąpienia obliczeniowego.  

Następujące narzędzia i środowiska są już zainstalowane w wystąpieniu obliczeniowym: 

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
|RStudio Server Open Source Edition (wersja zapoznawcza)||
|Jądro języka R||
|Zestaw SDK Azure Machine Learning dla języka R|[azuremlsdk](https://azure.github.io/azureml-sdk-for-r/reference/index.html)</br>Przykłady zestawu SDK|

|Środowiska **Python** Tools & Environments|Szczegóły|
|----|----|
|Anaconda Python||
|Jupyter i rozszerzenia||
|Jupyterlab i rozszerzenia||
[Zestaw SDK usługi Azure Machine Learning dla języka Python](/python/api/overview/azure/ml/intro)</br>z PyPI|Zawiera większość dodatkowych pakietów platformy Azure.  Aby wyświetlić pełną listę, [Otwórz okno terminalu na wystąpieniu obliczeniowym](how-to-access-terminal.md) i uruchom polecenie <br/> `conda list -n azureml_py36 azureml*` |
|Inne pakiety PyPI|`jupytext`</br>`tensorboard`</br>`nbconvert`</br>`notebook`</br>`Pillow`|
|Pakiety Conda|`cython`</br>`numpy`</br>`ipykernel`</br>`scikit-learn`</br>`matplotlib`</br>`tqdm`</br>`joblib`</br>`nodejs`</br>`nb_conda_kernels`|
|Pakiety uczenia głębokiego|`PyTorch`</br>`TensorFlow`</br>`Keras`</br>`Horovod`</br>`MLFlow`</br>`pandas-ml`</br>`scrapbook`|
|Pakiety ONNX|`keras2onnx`</br>`onnx`</br>`onnxconverter-common`</br>`skl2onnx`</br>`onnxmltools`|
|Przykłady zestawu SDK języka Azure Machine Learning Python & R||

Wszystkie pakiety języka Python są zainstalowane w środowisku **python 3,6-Azure** .  

## <a name="accessing-files"></a>Uzyskiwanie dostępu do plików

Notesy i skrypty języka R są przechowywane na domyślnym koncie magazynu Twojego obszaru roboczego w udziale plików platformy Azure.  Te pliki znajdują się w katalogu "pliki użytkownika". Ten magazyn ułatwia udostępnianie notesów między wystąpieniami obliczeniowymi. Konto magazynu umożliwia również bezpieczne zachowywanie Twoich notesów po zatrzymaniu lub usunięciu wystąpienia obliczeniowego.

Konto udziału plików platformy Azure w obszarze roboczym jest zainstalowane jako dysk w wystąpieniu obliczeniowym. Ten dysk jest domyślnym katalogiem roboczym dla Jupyter, Jupyter Labs i RStudio. Oznacza to, że Notesy i inne pliki tworzone w Jupyter, JupyterLab lub RStudio są automatycznie przechowywane w udziale plików i dostępne do użycia również w innych wystąpieniach obliczeniowych.

Pliki w udziale plików są dostępne we wszystkich wystąpieniach obliczeniowych w tym samym obszarze roboczym. Wszelkie zmiany tych plików w wystąpieniu obliczeniowym będą niezawodnie utrwalane w udziale plików.

Najnowsze przykłady Azure Machine Learning można także klonować do folderu w katalogu plików użytkownika w udziale plików obszaru roboczego.

Pisanie małych plików może być wolniejsze na dyskach sieciowych niż zapis na dysku lokalnym wystąpienia obliczeniowego.  Jeśli piszesz wiele małych plików, spróbuj użyć katalogu bezpośrednio w wystąpieniu obliczeniowym, takim jak `/tmp` katalog. Uwaga Te pliki nie będą dostępne z innych wystąpień obliczeniowych. 

Możesz użyć `/tmp` katalogu w wystąpieniu obliczeniowym dla danych tymczasowych.  Nie należy jednak zapisywać dużych plików danych na dysku systemu operacyjnego wystąpienia obliczeniowego.  Zamiast nich należy używać [magazynów](concept-azure-machine-learning-architecture.md#datasets-and-datastores) danych. Jeśli zainstalowano rozszerzenie git JupyterLab, może to również prowadzić do spowolnienia działania wystąpienia obliczeniowego.

## <a name="managing-a-compute-instance"></a>Zarządzanie wystąpieniem obliczeniowym

W obszarze roboczym programu Azure Machine Learning Studio wybierz pozycję **obliczenia**, a następnie na górze wybierz pozycję **wystąpienie obliczeniowe** .

![Zarządzanie wystąpieniem obliczeniowym](./media/concept-compute-instance/manage-compute-instance.png)

Można wykonać następujące czynności:

* [Utwórz wystąpienie obliczeniowe](#create). 
* Odśwież kartę wystąpienia obliczeniowe.
* Uruchamianie, zatrzymywanie i ponowne uruchamianie wystąpienia obliczeniowego.  Płatność za wystąpienie jest dokonywana za każdym razem, gdy jest ono uruchomione. Zatrzymaj wystąpienie obliczeniowe, gdy nie jest używane, aby obniżyć koszty. Zatrzymywanie wystąpienia obliczeniowego powoduje jego przydział. Następnie uruchom ją ponownie, gdy będzie potrzebna. Pamiętaj, że zatrzymywanie wystąpienia obliczeniowego zatrzymuje rozliczanie godzin obliczeniowych, ale nadal będzie naliczana opłata za dysk, publiczny adres IP i moduł równoważenia obciążenia w warstwie Standardowa.
* Usuń wystąpienie obliczeniowe.
* Przefiltruj listę wystąpień obliczeniowych, aby wyświetlić tylko te, które zostały utworzone.

Dla każdego wystąpienia obliczeniowego w obszarze roboczym, którego możesz użyć, możesz:

* Dostęp do Jupyter, JupyterLab, RStudio w wystąpieniu obliczeniowym
* Użyj protokołu SSH do wystąpienia obliczeniowego. Dostęp SSH jest domyślnie wyłączony, ale można go włączyć podczas tworzenia wystąpienia obliczeniowego. Dostęp SSH odbywa się za pośrednictwem mechanizmu publicznego/prywatnego klucza. Karta przekaże szczegóły dotyczące połączenia SSH, takie jak adres IP, nazwa użytkownika i numer portu.
* Pobierz szczegóły dotyczące określonego wystąpienia obliczeniowego, takiego jak adres IP i region.

[Usługa Azure RBAC](../role-based-access-control/overview.md) pozwala kontrolować, którzy użytkownicy w obszarze roboczym mogą tworzyć, usuwać, uruchamiać, zatrzymywać, ponownie uruchamiać wystąpienie obliczeniowe. Wszyscy użytkownicy z rolą współautor i właściciel obszaru roboczego mogą tworzyć, usuwać, uruchamiać, zatrzymywać i ponownie uruchamiać wystąpienia obliczeniowe w obszarze roboczym. Jednak tylko twórca określonego wystąpienia obliczeniowego lub użytkownik przypisany, jeśli został utworzony w ich imieniu, może uzyskać dostęp do Jupyter, JupyterLab i RStudio na tym wystąpieniu obliczeniowym. Wystąpienie obliczeniowe jest przeznaczone dla pojedynczego użytkownika, który ma dostęp do katalogu głównego, i może być terminalem za pomocą Jupyter/JupyterLab/RStudio. Wystąpienie obliczeniowe będzie zawierać Logowanie jednokrotne, a wszystkie akcje będą korzystały z tożsamości tego użytkownika dla usługi Azure RBAC oraz do naliczania przebiegów eksperymentów. Dostęp SSH jest kontrolowany za pośrednictwem mechanizmu publicznego/prywatnego klucza.

Te akcje mogą być kontrolowane przez funkcję RBAC platformy Azure:
* *Microsoft. MachineLearningServices/obszary robocze/obliczenia/odczyt*
* *Microsoft.MachineLearningServices/workspaces/computes/write*
* *Microsoft. MachineLearningServices/obszary robocze/obliczenia/usuwanie*
* *Microsoft. MachineLearningServices/obszary robocze/obliczenia/uruchomienie/akcja*
* *Microsoft. MachineLearningServices/obszary robocze/obliczenia/akcja*
* *Microsoft. MachineLearningServices/obszary robocze/obliczenia/ponowne uruchomienie/akcja*

Aby utworzyć wystąpienie obliczeniowe, musisz mieć uprawnienia do następujących akcji:
* *Microsoft.MachineLearningServices/workspaces/computes/write*
* *Microsoft.MachineLearningServices/workspaces/checkComputeNameAvailability/action*


### <a name="create-a-compute-instance"></a><a name="create"></a>Tworzenie wystąpienia obliczeniowego

W obszarze roboczym programu Azure Machine Learning Studio [Utwórz nowe wystąpienie obliczeniowe](how-to-create-attach-compute-studio.md#compute-instance) z sekcji **obliczenia** lub w sekcji **notesy** , gdy wszystko będzie gotowe do uruchomienia jednego z notesów. 

Można również utworzyć wystąpienie
* Bezpośrednio w [środowisku zintegrowanych notesów](tutorial-1st-experiment-sdk-setup.md#azure)
* W Azure Portal
* Z szablonu Azure Resource Manager. Przykładowy szablon można znaleźć w temacie [Create a Azure Machine Learning COMPUTE instance Template](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-computeinstance).
* Z [zestawem SDK Azure Machine Learning](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/machine-learning/concept-compute-instance.md)
* Z [rozszerzenia interfejsu wiersza polecenia dla Azure Machine Learning](reference-azure-machine-learning-cli.md#computeinstance)

Dedykowane rdzenie dla poszczególnych regionów na poszczególne regiony i łączne limity przydziału regionalnego, które mają zastosowanie do tworzenia wystąpienia obliczeniowego, są ujednolicone i udostępniane przy użyciu Azure Machine Learninggo przydziału klastra obliczeniowego. Zatrzymanie wystąpienia obliczeniowego nie powoduje zwolnienia przydziału w celu zapewnienia, że będzie można ponownie uruchomić wystąpienie obliczeniowe.


### <a name="create-on-behalf-of-preview"></a>Utwórz w imieniu (wersja zapoznawcza)

Jako administrator możesz utworzyć wystąpienie obliczeniowe w imieniu Analityka danych i przypisać do nich wystąpienie:
* [Szablon Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-computeinstance).  Aby uzyskać szczegółowe informacje na temat sposobu wyszukiwania TenantID i ObjectID wymaganych w tym szablonie, zobacz [Znajdowanie identyfikatorów obiektów tożsamości dla konfiguracji uwierzytelniania](../healthcare-apis/fhir/find-identity-object-ids.md).  Te wartości można również znaleźć w portalu Azure Active Directory.
* Interfejs API REST

Analityk danych tworzy wystąpienie obliczeniowe wymagające następujących uprawnień RBAC platformy Azure: 
* *Microsoft. MachineLearningServices/obszary robocze/obliczenia/uruchomienie/akcja*
* *Microsoft. MachineLearningServices/obszary robocze/obliczenia/akcja*
* *Microsoft. MachineLearningServices/obszary robocze/obliczenia/ponowne uruchomienie/akcja*
* *Microsoft. MachineLearningServices/obszary robocze/obliczenia/applicationaccess/akcja*

Analityk danych może uruchamiać, zatrzymywać i ponownie uruchamiać wystąpienie obliczeniowe. Mogą używać wystąpienia obliczeniowego dla:
* Jupyter
* JupyterLab
* RStudio
* Zintegrowane notesy

## <a name="compute-target"></a>Docelowy zasób obliczeniowy

Wystąpienia obliczeniowe mogą służyć jako [obiekt docelowy obliczeń szkoleniowych](concept-compute-target.md#train) podobny do klastrów szkoleniowych Azure Machine Learning COMPUTE. 

Wystąpienie obliczeniowe:
* Ma kolejkę zadań.
* Bezpieczne uruchamianie zadań w środowisku sieci wirtualnej, bez konieczności otwierania portu SSH przez przedsiębiorstwa. Zadanie jest wykonywane w środowisku kontenerowym i pakuje zależności modelu w kontenerze platformy Docker.
* Może uruchamiać wiele małych zadań równolegle (wersja zapoznawcza).  Dwa zadania na rdzeń mogą działać równolegle, podczas gdy pozostałe zadania są umieszczane w kolejce.
* Obsługuje wielowęzłowe zadania szkoleniowe wieloprocesorowe.

Wystąpienia obliczeniowego można użyć jako lokalnego celu wdrożenia inferencing na potrzeby scenariuszy testowania/debugowania.

> [!TIP]
> Wystąpienie obliczeniowe ma dysk systemu operacyjnego o 120 GB. Jeśli zabraknie miejsca na [dysku, przed](how-to-access-terminal.md) [zatrzymaniem lub ponownym uruchomieniem](how-to-create-manage-compute-instance.md#manage) wystąpienia obliczeniowego należy wyczyścić co najmniej 1-2 GB pamięci.


## <a name="what-happened-to-notebook-vm"></a><a name="notebookvm"></a>Co się stało z maszyną wirtualną notesu?

Wystąpienia obliczeniowe zamieniają maszynę wirtualną notesu.  

Wszystkie pliki notesu przechowywane w udziale plików obszaru roboczego i dane w magazynach danych obszaru roboczego będą dostępne z wystąpienia obliczeniowego. Jednak wszystkie pakiety niestandardowe zainstalowane wcześniej na maszynie wirtualnej notesu będą musiały zostać zainstalowane ponownie w wystąpieniu obliczeniowym. Ograniczenia limitu przydziału, które dotyczą tworzenia klastrów obliczeniowych, będą również stosowane do tworzenia wystąpień obliczeniowych.

Nie można utworzyć nowych maszyn wirtualnych notesu. Można jednak nadal uzyskiwać dostęp do utworzonych maszyn wirtualnych Notes i korzystać z nich z pełną funkcjonalnością. Wystąpienia obliczeniowe można tworzyć w tym samym obszarze roboczym, co istniejące maszyny wirtualne notesu.


## <a name="next-steps"></a>Następne kroki

* [Tworzenie wystąpienia obliczeniowego i zarządzanie nim](how-to-create-manage-compute-instance.md)
* [Samouczek: uczenie swojego pierwszego modelu ml](tutorial-1st-experiment-sdk-train.md) pokazuje, jak używać wystąpienia obliczeniowego z zintegrowanym notesem.
