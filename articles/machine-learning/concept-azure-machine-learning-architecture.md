---
title: Kluczowe & architektury
titleSuffix: Azure Machine Learning
description: Ten artykuł zapewnia wiedzę na temat architektury, terminów i pojęć, które Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 08/20/2020
ms.custom: seoapril2019, seodec18
ms.openlocfilehash: f1eb7a5b4697801775d23091c610ab594b0b27ec
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813384"
---
# <a name="how-azure-machine-learning-works-architecture-and-concepts"></a>Jak Azure Machine Learning działa: Architektura i pojęcia

Poznaj architekturę i pojęcia związane z [Azure Machine Learning](overview-what-is-azure-ml.md).  Ten artykuł zawiera wiedzę na temat składników oraz sposobu ich współpracy, aby pomóc w procesie tworzenia, wdrażania i utrzymywania modeli uczenia maszynowego.

## <a name="workspace"></a><a name="workspace"></a> Obszaru roboczego

Obszar [roboczy uczenia maszynowego](concept-workspace.md) jest zasobem najwyższego poziomu dla Azure Machine Learning.

:::image type="content" source="media/concept-azure-machine-learning-architecture/architecture.svg" alt-text="Diagram: Azure Machine Learning architektury obszaru roboczego i jego składników":::

Obszar roboczy jest scentralizowanym miejscem do:

* Zarządzanie zasobami, których używasz do trenowania i wdrażania modeli, takich jak [obliczenia](#compute-instance)
* Przechowuj zasoby, które tworzysz podczas korzystania z Azure Machine Learning, w tym:
  * [Środowiska](#environments)
  * [Eksperymenty](#experiments)
  * [Pipelines](#ml-pipelines)
  * [Zestawy danych](#datasets-and-datastores)
  * [Modele](#models)
  * [Punkty końcowe](#endpoints)

Obszar roboczy zawiera inne zasoby platformy Azure, które są używane przez ten obszar roboczy:

+ [Azure Container Registry (ACR):](https://azure.microsoft.com/services/container-registry/)rejestruje kontenery platformy Docker, które są stosowane podczas trenowania i wdrażania modelu. Aby zminimalizować koszty, usługę ACR tworzy się tylko podczas tworzenia obrazów wdrożenia.
+ [Konto usługi Azure Storage:](https://azure.microsoft.com/services/storage/)jest używane jako domyślny magazyn danych dla obszaru roboczego.  W tym miejscu są również przechowywane notesy Jupyter, które są używane Azure Machine Learning wystąpień obliczeniowych.
+ [Azure Application Insights:](https://azure.microsoft.com/services/application-insights/)przechowuje informacje o monitorowaniu modeli.
+ [Azure Key Vault:](https://azure.microsoft.com/services/key-vault/)przechowuje wpisy tajne używane przez docelowe obiekty obliczeniowe i inne poufne informacje wymagane przez obszar roboczy.

Obszar roboczy można udostępnić innym osobom.

## <a name="computes"></a>Oblicza

<a name="compute-targets"></a> Docelowy [obiekt obliczeniowy](concept-compute-target.md) to dowolna maszyna lub zestaw maszyn, których używasz do uruchamiania skryptu trenowania lub hostowania wdrożenia usługi. Jako docelowego obiektu obliczeniowego możesz użyć maszyny lokalnej lub zdalnego zasobu obliczeniowego.  Za pomocą docelowych obiektów obliczeniowych możesz rozpocząć trenowanie na komputerze lokalnym, a następnie skalować w poziomie do chmury bez zmieniania skryptu trenowania.

Azure Machine Learning wprowadzono dwie w pełni zarządzane maszyny wirtualne w chmurze, które są skonfigurowane do zadań uczenia maszynowego:

* <a name="compute-instance"></a>**Wystąpienie obliczeniowe:** wystąpienie obliczeniowe to maszyna wirtualna, która zawiera wiele narzędzi i środowisk zainstalowanych na platformie uczenia maszynowego. Podstawowym zastosowaniem wystąpienia obliczeniowego jest stacja robocza dewelopera.  Możesz rozpocząć uruchamianie przykładowych notesów bez wymaganej konfiguracji. Wystąpienie obliczeniowe może być również używane jako docelowy obiekt obliczeniowy do trenowania i wnioskowania zadań.

* **Klastry obliczeniowe:** klastry obliczeniowe to klaster maszyn wirtualnych z możliwościami skalowania z wieloma węzłami. Klastry obliczeniowe lepiej nadają się do docelowych obiektów obliczeniowych w przypadku dużych zadań i produkcji.  Klaster jest automatycznie skalowany w górę po przesłaniu zadania.  Użyj jako szkoleniowego docelowego obiektu obliczeniowego lub wdrożenia tworzenia i testowania.

Aby uzyskać więcej informacji na temat trenowania docelowych obiektów obliczeniowych, zobacz [Szkolenie docelowych obiektów obliczeniowych.](concept-compute-target.md#train)  Aby uzyskać więcej informacji na temat docelowych obiektów obliczeniowych wdrożenia, zobacz [Deployment targets (Cele wdrożenia).](concept-compute-target.md#deploy)

## <a name="datasets-and-datastores"></a>Zestawy danych i magazyny danych

[**Azure Machine Learning zestawy danych**](concept-data.md#datasets)  ułatwiają dostęp do danych i pracę z danymi. Tworząc zestaw danych, tworzysz odwołanie do lokalizacji źródła danych wraz z kopią jego metadanych. Ponieważ dane pozostają w istniejącej lokalizacji, nie są naliczane żadne dodatkowe opłaty za magazyn i nie ryzykowne jest integralność źródeł danych.

Aby uzyskać więcej informacji, zobacz [Create and register Azure Machine Learning Datasets (Tworzenie](how-to-create-register-datasets.md)i rejestrowanie Azure Machine Learning zestawów danych).  Aby uzyskać więcej przykładów dotyczących używania zestawów danych, zobacz [przykładowe notesy](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/datasets-tutorial).

Zestawy danych [używają magazynów danych do](concept-data.md#datastores) bezpiecznego łączenia się z usługami azure storage. Magazyny danych przechowują informacje o połączeniu bez narażania na ryzyko poświadczeń uwierzytelniania i integralności oryginalnego źródła danych. Przechowują one informacje o połączeniu, takie jak identyfikator subskrypcji i autoryzacja tokenu w twojej aplikacji Key Vault skojarzonej z obszarem roboczym, dzięki czemu możesz bezpiecznie uzyskać dostęp do magazynu bez konieczności bezpiecznego skojadowania ich w skrypcie.

## <a name="environments"></a>Środowiska

[Obszar roboczy](#workspace)  >  **Środowiska**

Środowisko [to](concept-environments.md) hermetyzacja środowiska, w którym odbywa się trenowanie lub ocenianie modelu uczenia maszynowego. Środowisko określa pakiety języka Python, zmienne środowiskowe i ustawienia oprogramowania wokół skryptów trenowania i oceniania.  

Przykłady kodu można znaleźć w sekcji "Zarządzanie środowiskami" [artykułu How to use environments (Jak używać środowisk).](how-to-use-environments.md#manage-environments)

## <a name="experiments"></a>Eksperymenty

[Obszar roboczy](#workspace)  >  **Eksperymenty**

Eksperyment to grupa wielu przebiegów z określonego skryptu. Zawsze należy do obszaru roboczego. Podczas przesyłania uruchomienia należy podać nazwę eksperymentu. Informacje dotyczące uruchomienia są przechowywane w tym eksperymencie. Jeśli nazwa nie istnieje podczas przesyłania eksperymentu, zostanie automatycznie utworzony nowy eksperyment.
  
Aby uzyskać przykład użycia eksperymentu, zobacz [Tutorial: Train your first model (Samouczek: trenowanie pierwszego modelu).](tutorial-1st-experiment-sdk-train.md)

### <a name="runs"></a>Uruchamianie

[Obszar roboczy](#workspace)  >  [Eksperymenty](#experiments)  >  **Uruchom**

Przebieg to pojedyncze wykonanie skryptu trenowania. Eksperyment będzie zazwyczaj zawierać wiele przebiegów.

Azure Machine Learning rejestruje wszystkie przebiegi i przechowuje następujące informacje w eksperymencie:

* Metadane dotyczące uruchomienia (sygnatura czasowa, czas trwania i tak dalej)
* Metryki rejestrowane przez skrypt
* Pliki wyjściowe, które są automatycznie zbierane przez eksperyment lub jawnie przekazane przez Ciebie
* Migawka katalogu zawierającego skrypty przed uruchomieniem

Uruchomienie jest tworzyć podczas przesyłania skryptu w celu trenowania modelu. Przebieg może mieć zero lub więcej przebiegów podrzędnych. Na przykład przebieg najwyższego poziomu może mieć dwa przebiegi podrzędne, z których każdy może mieć własny przebieg podrzędny.

### <a name="run-configurations"></a>Uruchamianie konfiguracji

[Obszar roboczy](#workspace)  >  [Eksperymenty](#experiments)  >  [Uruchom](#runs)  >  **Uruchamianie konfiguracji**

Konfiguracja uruchamiania definiuje sposób uruchamiania skryptu w określonym docelowym obiektem obliczeniowym. Konfiguracja umożliwia określenie skryptu, docelowego obiektu obliczeniowego i środowiska usługi Azure ML do uruchomienia, wszelkich konfiguracji specyficznych dla zadania rozproszonego oraz niektórych dodatkowych właściwości. Aby uzyskać więcej informacji na temat pełnego zestawu konfigurowalnych opcji przebiegów, zobacz [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig).

Konfigurację uruchamiania można utrwalić w pliku wewnątrz katalogu, który zawiera skrypt trenowania.   Można go też skonstruować jako obiekt w pamięci i użyć do przesyłania uruchomienia.

Aby uzyskać przykładowe konfiguracje uruchamiania, [zobacz Configure a training run (Konfigurowanie uruchomienia trenowania).](how-to-set-up-training-targets.md)

### <a name="snapshots"></a>Migawki

[Obszar roboczy](#workspace)  >  [Eksperymenty](#experiments)  >  [Uruchom](#runs)  >  **Migawka**

Po przesłaniu uruchomienia program Azure Machine Learning katalog zawierający skrypt w pliku zip i wysyła go do docelowego obiektu obliczeniowego. Plik zip jest następnie wyodrębniony i skrypt jest w tym miejscu uruchamiany. Azure Machine Learning przechowuje również plik zip jako migawkę w ramach rekordu uruchomienia. Każda osoba mająca dostęp do obszaru roboczego może przeglądać rekord uruchomienia i pobierać migawkę.

### <a name="logging"></a>Rejestrowanie

Azure Machine Learning automatycznie rejestruje metryki uruchamiania standardowego. Można jednak również użyć [zestawu SDK języka Python, aby rejestrować dowolne metryki.](how-to-log-view-metrics.md)

Istnieje wiele sposobów wyświetlania dzienników: monitorowanie stanu uruchomienia w czasie rzeczywistym lub wyświetlanie wyników po zakończeniu. Aby uzyskać więcej informacji, zobacz [Monitorowanie i wyświetlanie dzienników uruchamiania uczenia maszynowego.](how-to-log-view-metrics.md)


> [!NOTE]
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]

### <a name="git-tracking-and-integration"></a>Śledzenie i integracja usługi Git

Po uruchomieniu uruchomienia trenowania, w którym katalog źródłowy jest lokalnym repozytorium Git, informacje o repozytorium są przechowywane w historii uruchamiania. Działa to z przebiegami przesłanymi przy użyciu konfiguracji uruchamiania skryptu lub potoku uczenia maszynowego. Działa również w przypadku przebiegów przesłanych z zestawu SDK lub Machine Learning wiersza polecenia.

Aby uzyskać więcej informacji, zobacz [Integracja z usługą Git dla Azure Machine Learning](concept-train-model-git-integration.md).

### <a name="training-workflow"></a>Przepływ pracy trenowania

Po uruchomieniu eksperymentu w celu wytowania modelu należy wykonać następujące kroki. Zostały one zilustrowane na poniższym diagramie przepływu pracy szkolenia:

* Azure Machine Learning jest wywoływana z identyfikatorem migawki migawki kodu zapisanej w poprzedniej sekcji.
* Azure Machine Learning tworzy identyfikator uruchomienia (opcjonalnie) i token Machine Learning service, który jest później używany przez docelowe obiekty obliczeniowe, takie jak środowisko obliczeniowe usługi Machine Learning/maszyny wirtualne, do komunikacji z maszynami Machine Learning service.
* Aby uruchamiać zadania szkoleniowe, możesz wybrać zarządzany docelowy obiekt obliczeniowy (na przykład środowisko obliczeniowe usługi Machine Learning) lub nieza zarządzany docelowy obiekt obliczeniowy (na przykład maszyny wirtualne). Poniżej podano przepływy danych dla obu scenariuszy:
   * Maszyny wirtualne/usługa HDInsight dostępne przy użyciu poświadczeń SSH w magazynie kluczy w ramach subskrypcji firmy Microsoft. Azure Machine Learning uruchamia kod zarządzania na docelowym obiektach obliczeniowych, który:

   1. Przygotowuje środowisko. (Docker to opcja dla maszyn wirtualnych i komputerów lokalnych. Zapoznaj się z poniższymi krokami, aby środowisko obliczeniowe usługi Machine Learning, aby zrozumieć, jak działa uruchamianie eksperymentów w kontenerach platformy Docker).
   1. Pobiera kod.
   1. Konfiguruje zmienne środowiskowe i konfiguracje.
   1. Uruchamia skrypty użytkownika (migawkę kodu wymienioną w poprzedniej sekcji).

   * środowisko obliczeniowe usługi Machine Learning dostępne za pośrednictwem tożsamości zarządzanej obszaru roboczego.
Ponieważ środowisko obliczeniowe usługi Machine Learning to zarządzany docelowy obiekt obliczeniowy (czyli jest zarządzany przez firmę Microsoft), jest uruchamiany w ramach subskrypcji firmy Microsoft.

   1. W razie potrzeby rozpocznie się zdalna konstrukcja platformy Docker.
   1. Kod zarządzania jest zapisywany w Azure Files użytkownika.
   1. Kontener jest uruchomiony za pomocą początkowego polecenia. Oznacza to, że kod zarządzania jest opisany w poprzednim kroku.

* Po zakończeniu uruchamiania można zapytania dotyczące przebiegów i metryk. Na poniższym diagramie przepływu ten krok występuje, gdy docelowy obiekt obliczeniowy trenowania zapisuje metryki uruchamiania z powrotem Azure Machine Learning z magazynu w Cosmos DB danych. Klienci mogą wywołać Azure Machine Learning. Machine Learning z kolei ściągnie metryki z bazy Cosmos DB i zwróci je z powrotem do klienta.

[![Przepływ pracy szkolenia](media/concept-azure-machine-learning-architecture/training-and-metrics.png)](media/concept-azure-machine-learning-architecture/training-and-metrics.png#lightbox)

## <a name="models"></a>Modele

W najprostszym przypadku model jest fragmentem kodu, który przyjmuje dane wejściowe i generuje dane wyjściowe. Tworzenie modelu uczenia maszynowego obejmuje wybranie algorytmu, dostarczenie do niego danych i dostrojenie [hiperparametrów.](how-to-tune-hyperparameters.md) Trenowanie jest procesem iteracyjnym, który tworzy wytrenowany model, który hermetyzuje informacje poznane przez model podczas procesu trenowania.

Możesz użyć modelu, który został wytrenowany poza Azure Machine Learning. Możesz też wytszkolić model, [](#experiments) przesyłając [przebieg](#runs) eksperymentu do docelowego obiektu obliczeniowego w [Azure Machine Learning.](#compute-targets) Po zarejestrowaniu modelu należy [go zarejestrować w](#register-model) obszarze roboczym.

Azure Machine Learning jest niezależny od struktury. Podczas tworzenia modelu można użyć dowolnej popularnej struktury uczenia maszynowego, takiej jak Scikit-learn, XGBoost, PyTorch, TensorFlow i Chainer.

Aby uzyskać przykład trenowania modelu przy użyciu biblioteki Scikit-learn, zobacz [Tutorial: Train an image classification model with Azure Machine Learning](tutorial-train-models-with-aml.md)(Samouczek: trenowanie modelu klasyfikacji obrazów za pomocą biblioteki Azure Machine Learning ).


### <a name="model-registry"></a><a name="register-model"></a> Rejestr modeli

[Obszar roboczy](#workspace)  >  **Modele**

Rejestr **modeli umożliwia** śledzenie wszystkich modeli w twoim Azure Machine Learning roboczym.

Modele są identyfikowane według nazwy i wersji. Za każdym razem, gdy rejestrujesz model o takiej samej nazwie jak istniejący, rejestr zakłada, że jest to nowa wersja. Wersja jest zwiększana, a nowy model jest rejestrowany pod taką samą nazwą.

Podczas rejestrowania modelu można podać dodatkowe tagi metadanych, a następnie użyć tagów podczas wyszukiwania modeli.

> [!TIP]
> Zarejestrowany model jest kontenerem logicznym dla co najmniej jednego pliku, który stanowi model. Jeśli na przykład masz model przechowywany w wielu plikach, możesz zarejestrować je jako pojedynczy model w Azure Machine Learning roboczym. Po rejestracji możesz pobrać lub wdrożyć zarejestrowany model i odebrać wszystkie zarejestrowane pliki.

Nie można usunąć zarejestrowanego modelu, który jest używany przez aktywne wdrożenie.

Aby uzyskać przykład rejestrowania modelu, zobacz Train an image classification model with Azure Machine Learning (Trenowanie modelu [klasyfikacji obrazów](tutorial-train-models-with-aml.md)za pomocą Azure Machine Learning ).

## <a name="deployment"></a>Wdrożenie

Zarejestrowany model [jest wdrażany jako](#register-model) punkt końcowy usługi. Potrzebne są następujące składniki:

* **Środowisko**. To środowisko hermetyzuje zależności wymagane do uruchomienia modelu na potrzeby wnioskowania.
* **Kod oceniania**. Ten skrypt akceptuje żądania, ocenia je przy użyciu modelu i zwraca wyniki.
* **Konfiguracja wnioskowania**. Konfiguracja wnioskowania określa środowisko, skrypt wejściowy i inne składniki potrzebne do uruchomienia modelu jako usługi.

Aby uzyskać więcej informacji na temat tych składników, zobacz [Wdrażanie modeli za pomocą Azure Machine Learning](how-to-deploy-and-where.md).

### <a name="endpoints"></a>Punkty końcowe

[Obszar roboczy](#workspace)  >  **Punkty końcowe**

Punkt końcowy to wystąpienia modelu w usłudze internetowej, która może być hostowana w chmurze, lub w module IoT dla zintegrowanych wdrożeń urządzeń.

#### <a name="web-service-endpoint"></a>Punkt końcowy usługi sieci Web

Podczas wdrażania modelu jako usługi internetowej punkt końcowy można wdrożyć na Azure Container Instances, Azure Kubernetes Service układów FPGA. Usługę tworzy się na poziomie modelu, skryptu i skojarzonych plików. Są one umieszczane w obrazie kontenera podstawowego, który zawiera środowisko wykonywania dla modelu. Obraz ma punkt końcowy HTTP o zrównoważonym obciążeniu, który odbiera żądania oceniania wysyłane do usługi internetowej.

Możesz włączyć telemetrię Application Insights lub telemetrię modelu w celu monitorowania usługi internetowej. Dane telemetryczne są dostępne tylko dla Ciebie.  Są one przechowywane w wystąpieniach Application Insights magazynu. Jeśli włączono automatyczne skalowanie, platforma Azure automatycznie skaluje wdrożenie.

Na poniższym diagramie przedstawiono przepływ pracy wnioskowania dla modelu wdrożonego jako punkt końcowy usługi internetowej:

Poniżej znajdują się szczegółowe informacje:

* Użytkownik rejestruje model przy użyciu klienta, takiego jak Azure Machine Learning SDK.
* Użytkownik tworzy obraz przy użyciu modelu, pliku wyników i innych zależności modelu.
* Obraz platformy Docker jest tworzony i przechowywany w Azure Container Registry.
* Usługa internetowa jest wdrażana do docelowego obiektu obliczeniowego (Container Instances/AKS) przy użyciu obrazu utworzonego w poprzednim kroku.
* Szczegóły żądania oceniania są przechowywane Application Insights, która znajduje się w subskrypcji użytkownika.
* Dane telemetryczne są również wypychane do subskrypcji platformy Microsoft/Azure.

[![Przepływ pracy wnioskowania](media/concept-azure-machine-learning-architecture/inferencing.png)](media/concept-azure-machine-learning-architecture/inferencing.png#lightbox)


Aby uzyskać przykład wdrażania modelu jako usługi internetowej, zobacz Wdrażanie modelu klasyfikacji obrazów w [usłudze Azure Container Instances](tutorial-deploy-models-with-aml.md).

#### <a name="real-time-endpoints"></a>Punkty końcowe w czasie rzeczywistym

Podczas wdrażania wytrenowany model w projektancie można go wdrożyć jako punkt [końcowy w czasie rzeczywistym.](tutorial-designer-automobile-price-deploy.md) Punkt końcowy czasu rzeczywistego często odbiera jedno żądanie za pośrednictwem punktu końcowego REST i zwraca przewidywanie w czasie rzeczywistym. Jest to przeciwieńsze przetwarzania wsadowego, które przetwarza wiele wartości jednocześnie i zapisuje wyniki po zakończeniu do magazynu danych.

#### <a name="pipeline-endpoints"></a>Punkty końcowe potoku

Punkty końcowe potoku umożliwiają programowe wywołanie [potoków uczenia](#ml-pipelines) maszynowego za pośrednictwem punktu końcowego REST. Punkty końcowe potoku umożliwiają automatyzację przepływów pracy potoku.

Punkt końcowy potoku to kolekcja opublikowanych potoków. Ta organizacja logiczna umożliwia zarządzanie wieloma potokami i ich wywołanie przy użyciu tego samego punktu końcowego. Każdy opublikowany potok w punkcie końcowym potoku jest wersjonarowany. Możesz wybrać potok domyślny dla punktu końcowego lub określić wersję w wywołaniu REST.
 

#### <a name="iot-module-endpoints"></a>Punkty końcowe modułu IoT

Wdrożony punkt końcowy modułu IoT to kontener platformy Docker, który zawiera model i skojarzony skrypt lub aplikację oraz wszelkie dodatkowe zależności. Te moduły są wdrażane przy użyciu Azure IoT Edge na urządzeniach brzegowych.

Jeśli włączono monitorowanie, platforma Azure zbiera dane telemetryczne z modelu w module Azure IoT Edge danych. Dane telemetryczne są dostępne tylko dla Ciebie i są przechowywane w wystąpieniu konta magazynu.

Azure IoT Edge, że moduł jest uruchomiony, i monitoruje urządzenie, które go hostuje. 
## <a name="automation"></a>Automation

### <a name="azure-machine-learning-cli"></a>Interfejs wiersza polecenia usługi Azure Machine Learning 

Interfejs [Azure Machine Learning wiersza polecenia](reference-azure-machine-learning-cli.md) to rozszerzenie interfejsu wiersza polecenia platformy Azure, międzyplatformowego interfejsu wiersza polecenia dla platformy Azure. To rozszerzenie udostępnia polecenia służące do automatyzowania działań uczenia maszynowego.

### <a name="ml-pipelines"></a>Potoki uczenia maszynowego

Potoki [uczenia maszynowego są służące](concept-ml-pipelines.md) do tworzenia przepływów pracy, które łączy fazy uczenia maszynowego, i zarządzania nimi. Na przykład potok może obejmować fazy przygotowywania danych, trenowania modelu, wdrażania modelu oraz wnioskowania/oceniania. Każda faza może obejmować wiele kroków, z których każdy może być uruchamiany nienadzorowany w różnych docelowych wystąpieniach obliczeniowych. 

Kroki potoku są wielokrotnego użytku i można je uruchamiać bez ponownego uruchamiania poprzednich kroków, jeśli dane wyjściowe tych kroków nie uległy zmianie. Na przykład możesz ponownie wytrenować model bez ponownego przygotowania kosztownych kroków przygotowywania danych, jeśli dane nie uległy zmianie. Potoki umożliwiają również badaczom danych współpracę podczas pracy nad oddzielnymi obszarami przepływu pracy uczenia maszynowego.

## <a name="monitoring-and-logging"></a>Monitorowanie i rejestrowanie

Azure Machine Learning zapewnia następujące możliwości monitorowania i rejestrowania:

* Dla __naukowców zajmujących się__ danymi możesz monitorować eksperymenty i rejestrować informacje z przebiegów trenowania. Aby uzyskać więcej informacji, zobacz następujące artykuły:
   * [Uruchamianie, monitorowanie i anulowanie przebiegów trenowania](how-to-track-monitor-analyze-runs.md)
   * [Rejestrowanie metryk dla przebiegów treningowych](how-to-log-view-metrics.md)
   * [Śledzenie eksperymentów za pomocą platformy MLflow](how-to-use-mlflow.md)
   * [Wizualizacja przebiegów za pomocą narzędzia TensorBoard](how-to-monitor-tensorboard.md)
* W __przypadku__ administratorów można monitorować informacje o obszarze roboczym, powiązanych zasobach platformy Azure i zdarzeniach, takich jak tworzenie i usuwanie zasobów, przy użyciu Azure Monitor. Aby uzyskać więcej informacji, [zobacz Jak monitorować Azure Machine Learning](monitor-azure-machine-learning.md).
* W przypadku __metodyki DevOps__ lub __MLOps__ można monitorować informacje generowane przez modele wdrożone jako usługi internetowe lub moduły usługi IoT Edge, aby identyfikować problemy z wdrożeniami i zbierać dane przesyłane do usługi. Aby uzyskać więcej informacji, zobacz [Collect model data (Zbieranie danych modelu)](how-to-enable-data-collection.md) [i Monitor with Application Insights (Monitorowanie za pomocą Application Insights](how-to-enable-app-insights.md)).

## <a name="interacting-with-your-workspace"></a>Interakcja z obszarem roboczym

### <a name="studio"></a>Studio

[Azure Machine Learning studio](overview-what-is-machine-learning-studio.md) udostępnia widok internetowy wszystkich artefaktów w obszarze roboczym.  Możesz wyświetlać wyniki i szczegóły dotyczące zestawów danych, eksperymentów, potoków, modeli i punktów końcowych.  Możesz również zarządzać zasobami obliczeniowymi i magazynami danych w studio.

Studio to również miejsce, w którym uzyskujesz dostęp do narzędzi interaktywnych, które są częścią Azure Machine Learning:

+ [Azure Machine Learning do wykonywania](concept-designer.md) kroków przepływu pracy bez pisania kodu
+ Środowisko internetowe dla [zautomatyzowanego uczenia maszynowego](concept-automated-ml.md)
+ [Azure Machine Learning notesy do](how-to-run-jupyter-notebooks.md) pisania i uruchamiania własnego kodu na zintegrowanych serwerach notesów Jupyter.
+ [Etykietowanie danych projektów w](how-to-create-labeling-projects.md) celu tworzenia i monitorowania projektów oraz zarządzania nimi w celu etykietowania danych

### <a name="programming-tools"></a>Narzędzia programistyczne

> [!IMPORTANT]
> Narzędzia oznaczone poniżej (wersja zapoznawcza) są obecnie dostępne w publicznej wersji zapoznawczej.
> Wersja zapoznawcza jest dostarczana bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

+  Wchodz w interakcję z usługą w dowolnym środowisku języka Python za pomocą zestawu SDK Azure Machine Learning [dla języka Python.](/python/api/overview/azure/ml/intro)
+ Interakcja z usługą w dowolnym środowisku języka R przy użyciu Azure Machine Learning [SDK dla języka R](https://azure.github.io/azureml-sdk-for-r/reference/index.html) (wersja zapoznawcza).
+ Użyj [Azure Machine Learning, aby](concept-designer.md) wykonać kroki przepływu pracy bez pisania kodu. 
+ Automatyzacja [Azure Machine Learning interfejsu wiersza](./reference-azure-machine-learning-cli.md) polecenia.
+ [Akcelerator](https://aka.ms/many-models) rozwiązań do obsługi wielu modeli (wersja zapoznawcza) jest Azure Machine Learning i umożliwia trenować setki, a nawet tysiące modeli uczenia maszynowego oraz zarządzać nimi.

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć pracę z Azure Machine Learning, zobacz:

* [Co to jest usługa Azure Machine Learning?](overview-what-is-azure-ml.md)
* [Tworzenie obszaru roboczego usługi Azure Machine Learning](how-to-manage-workspace.md)
* [Samouczek (część 1): trenowanie modelu](tutorial-train-models-with-aml.md)