---
title: Architektura & kluczowe pojęcia
titleSuffix: Azure Machine Learning
description: Poznaj architekturę, terminy i koncepcje, które składają się Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 08/20/2020
ms.custom: seoapril2019, seodec18
ms.openlocfilehash: f17cdd42c892f6c0d218875cf304846937ba58d7
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94444830"
---
# <a name="how-azure-machine-learning-works-architecture-and-concepts"></a>Jak działa Azure Machine Learning: architektura i koncepcje

Poznaj architekturę i koncepcje [Azure Machine Learning](overview-what-is-azure-ml.md).  Ten artykuł zawiera ogólne omówienie składników oraz sposób ich współdziałania w celu ułatwienia procesu kompilowania, wdrażania i konserwacji modeli uczenia maszynowego.

## <a name="workspace"></a><a name="workspace"></a> Obszary

[Obszar roboczy usługi Machine Learning](concept-workspace.md) jest zasobem najwyższego poziomu dla Azure Machine Learning.

:::image type="content" source="media/concept-azure-machine-learning-architecture/architecture.svg" alt-text="Diagram: Azure Machine Learning architektury obszaru roboczego i jego składników":::

Obszar roboczy jest scentralizowanym miejscem do:

* Zarządzanie zasobami używanymi do szkolenia i wdrażania modeli, takich jak [obliczenia](#compute-instance)
* Magazynowanie zasobów tworzonych podczas korzystania z Azure Machine Learning, w tym:
  * [Środowiska](#environments)
  * [Eksperymenty](#experiments)
  * [Pipelines](#ml-pipelines)
  * [Zestawy danych](#datasets-and-datastores)
  * [Modele](#models)
  * [Punkty końcowe](#endpoints)

Obszar roboczy zawiera inne zasoby platformy Azure, które są używane przez obszar roboczy:

+ [Azure Container Registry (ACR)](https://azure.microsoft.com/services/container-registry/): rejestruje kontenery Docker używane podczas uczenia i podczas wdrażania modelu. Aby zminimalizować koszty, ACR jest tworzony tylko podczas tworzenia obrazów wdrożeń.
+ [Konto usługi Azure Storage](https://azure.microsoft.com/services/storage/): służy jako domyślny magazyn danych dla obszaru roboczego.  Tutaj są przechowywane notesy Jupyter, które są używane z wystąpieniami obliczeniowymi Azure Machine Learning.
+ [Azure Application Insights](https://azure.microsoft.com/services/application-insights/): przechowuje informacje o monitorowaniu dla modeli.
+ [Azure Key Vault](https://azure.microsoft.com/services/key-vault/): przechowuje wpisy tajne, które są używane przez cele obliczeniowe i inne poufne informacje, które są zbędne w obszarze roboczym.

Obszar roboczy można udostępniać innym osobom.

### <a name="create-workspace"></a>Tworzenie obszaru roboczego

Na poniższym diagramie przedstawiono przepływ pracy tworzenia obszaru roboczego.

* Zaloguj się do usługi Azure AD z jednego z obsługiwanych klientów Azure Machine Learning (interfejs wiersza polecenia platformy Azure, zestaw SDK języka Python, Azure Portal) i zażądaj odpowiedniego tokenu Azure Resource Manager.
* Wywołaj Azure Resource Manager, aby utworzyć obszar roboczy. 
* Azure Resource Manager skontaktować się z dostawcą zasobów Azure Machine Learning w celu udostępnienia obszaru roboczego.
* Jeśli nie określisz istniejących zasobów, w subskrypcji zostaną utworzone dodatkowe wymagane zasoby.

W razie potrzeby można również udostępnić innym obiektom docelowym obliczeń, które są dołączone do obszaru roboczego (np. usługi Azure Kubernetes lub maszyn wirtualnych).

[![Przepływ pracy tworzenia obszaru roboczego](media/concept-azure-machine-learning-architecture/create-workspace.png)](media/concept-azure-machine-learning-architecture/create-workspace.png#lightbox)

## <a name="computes"></a>Oblicza

<a name="compute-targets"></a>[Obiekt docelowy obliczeń](concept-compute-target.md) to dowolna maszyna lub zestaw maszyn używanych do uruchamiania skryptu szkoleniowego lub hostowania wdrożenia usługi. Możesz użyć komputera lokalnego lub zdalnego zasobu obliczeniowego jako obiektu docelowego obliczeń.  Elementy docelowe obliczeń umożliwiają rozpoczęcie szkolenia na komputerze lokalnym, a następnie skalowanie do chmury bez konieczności zmiany skryptu szkoleniowego.

W Azure Machine Learning wprowadzono dwie w pełni zarządzane maszyny wirtualne oparte na chmurze, które zostały skonfigurowane na potrzeby zadań uczenia maszynowego:

* <a name="compute-instance"></a>**Wystąpienie obliczeniowe** : wystąpienie obliczeniowe to maszyna wirtualna, która obejmuje wiele narzędzi i środowisk zainstalowanych na potrzeby uczenia maszynowego. Podstawowym użyciem wystąpienia obliczeniowego jest dla stacji roboczej deweloperskiej.  Możesz zacząć uruchamiać przykładowe notesy bez konieczności instalacji. Wystąpienia obliczeniowego można także użyć jako elementu docelowego obliczeń dla zadań szkolenia i inferencing.

* **Klastry obliczeniowe** : Klastry obliczeniowe to klaster maszyn wirtualnych z funkcjami skalowania wielowęzłowego. Klastry obliczeniowe są lepiej dopasowane do celów obliczeniowych dużych zadań i produkcyjnych.  Klaster jest skalowany automatycznie podczas przesyłania zadania.  Użyj jako elementu docelowego obliczeń szkoleniowych lub do wdrożenia deweloperskiego/testowego.

Aby uzyskać więcej informacji na temat szkoleniowych obiektów docelowych, zobacz [uczenie obiektów docelowych obliczeń](concept-compute-target.md#train).  Aby uzyskać więcej informacji na temat celów obliczeniowych wdrożenia, zobacz [cele wdrożenia](concept-compute-target.md#deploy).

## <a name="datasets-and-datastores"></a>Zestawy danych i magazyny datastores

[**Azure Machine Learning zestawy**](concept-data.md#datasets)  danych ułatwiają uzyskiwanie dostępu i pracę z danymi. Tworząc zestaw danych, utworzysz odwołanie do lokalizacji źródła danych wraz z kopią jej metadanych. Ponieważ dane pozostają w istniejącej lokalizacji, nie ponosisz żadnych dodatkowych kosztów magazynu i nie ryzykują integralności źródeł danych.

Aby uzyskać więcej informacji, zobacz [Tworzenie i Rejestrowanie zestawów danych Azure Machine Learning](how-to-create-register-datasets.md).  Aby uzyskać więcej przykładów użycia zestawów danych, zobacz [przykładowe notesy](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/datasets-tutorial).

Zestawy danych umożliwiają bezpieczne łączenie się z usługami Azure Storage przy użyciu [magazynów](concept-data.md#datastores) danych. Magazyny danych przechowują informacje o połączeniu bez konieczności podawania poświadczeń uwierzytelniania i integralności oryginalnego źródła. Przechowują one informacje o połączeniach, takie jak identyfikator subskrypcji i autoryzacja tokenu w Key Vault skojarzony z obszarem roboczym, dzięki czemu można bezpiecznie uzyskać dostęp do magazynu bez konieczności nawiązywania w skrypcie kodu.

## <a name="environments"></a>Środowiska

[Obszar roboczy](#workspace)  >  **Środowiska**

[Środowisko](concept-environments.md) to hermetyzacja środowiska, w którym odbywa się uczenie lub ocenianie modelu uczenia maszynowego. Środowisko określa pakiety języka Python, zmienne środowiskowe i ustawienia oprogramowania wokół skryptów szkoleń i oceniania.  

Aby zapoznać się z przykładami kodu, zobacz sekcję "zarządzanie środowiskami" dotyczącą [korzystania ze środowisk](how-to-use-environments.md#manage-environments).

## <a name="experiments"></a>Eksperymenty

[Obszar roboczy](#workspace)  >  **Eksperymenty**

Eksperyment to grupa wielu przebiegów z określonego skryptu. Zawsze należy do obszaru roboczego. Podczas przesyłania przebiegu należy podać nazwę eksperymentu. Informacje dotyczące przebiegu są przechowywane w ramach tego eksperymentu. Jeśli nazwa nie istnieje podczas przesyłania eksperymentu, zostanie automatycznie utworzony nowy eksperyment.
  
Przykład korzystania z eksperymentu można znaleźć w [samouczku: uczenie pierwszego modelu](tutorial-1st-experiment-sdk-train.md).

### <a name="runs"></a>Uruchamianie

[Obszar roboczy](#workspace)  >  [Eksperymenty](#experiments)  >  **Uruchom**

Uruchomienie to pojedyncze wykonanie skryptu szkoleniowego. Eksperyment zwykle zawiera wiele uruchomień.

Azure Machine Learning rejestruje wszystkie uruchomienia i zapisuje następujące informacje w eksperymentie:

* Metadane dotyczące przebiegu (Sygnatura czasowa, czas trwania itd.)
* Metryki rejestrowane przez skrypt
* Pliki wyjściowe, które są zbierane przez eksperyment lub jawnie przekazane przez użytkownika
* Migawka katalogu zawierającego skrypty przed uruchomieniem

Podczas przesyłania skryptu w celu uczenia modelu należy utworzyć przebieg. Uruchomienie może mieć zero lub więcej uruchomień podrzędnych. Na przykład uruchomienie najwyższego poziomu może mieć dwa uruchomienia podrzędne, z których każdy może mieć własne uruchomienie podrzędne.

### <a name="run-configurations"></a>Uruchom konfiguracje

[Obszar roboczy](#workspace)  >  [Eksperymenty](#experiments)  >  [Uruchom](#runs)  >  **Uruchom konfigurację**

Konfiguracja przebiegu definiuje sposób uruchamiania skryptu w określonym elemencie docelowym obliczeń. Konfiguracja służy do określania skryptu, lokalizacji docelowej obliczeń i środowiska Azure ML do uruchomienia, wszystkich rozdystrybuowanych konfiguracji specyficznych dla zadań i pewnych dodatkowych właściwości. Aby uzyskać więcej informacji na temat pełnego zestawu konfigurowalnych opcji dla przebiegów, zobacz [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py).

Konfigurację przebiegu można utrwalić do pliku znajdującego się w katalogu zawierającym skrypt szkoleniowy.   Lub może być skonstruowany jako obiekt w pamięci i używany do przesyłania przebiegu.

Na przykład Uruchom konfiguracje, zobacz [Konfigurowanie przebiegu szkoleniowego](how-to-set-up-training-targets.md).

### <a name="snapshots"></a>Migawki

[Obszar roboczy](#workspace)  >  [Eksperymenty](#experiments)  >  [Uruchom](#runs)  >  **Migawka**

W przypadku przesyłania przebiegu Azure Machine Learning kompresuje katalog zawierający skrypt jako plik zip i wysyła go do obiektu docelowego obliczeń. Następnie plik zip zostanie wyodrębniony, a skrypt zostanie uruchomiony w tym miejscu. Azure Machine Learning również zapisuje plik zip jako migawkę w ramach rekordu uruchomieniowego. Każda osoba mająca dostęp do obszaru roboczego może przeglądać rekord uruchomienia i pobrać migawkę.

Na poniższym diagramie przedstawiono przepływ pracy migawek kodu.

[![Przepływ pracy migawek kodu](media/concept-azure-machine-learning-architecture/code-snapshot.png)](media/concept-azure-machine-learning-architecture/code-snapshot.png#lightbox)

### <a name="logging"></a>Rejestrowanie

Azure Machine Learning automatycznie rejestruje metryki standardowego uruchamiania. Można jednak [użyć zestawu SDK języka Python do rejestrowania arbitralnych metryk](how-to-track-experiments.md).

Istnieją różne sposoby wyświetlania dzienników: monitorowanie stanu przebiegu w czasie rzeczywistym lub wyświetlanie wyników po zakończeniu. Aby uzyskać więcej informacji, zobacz [monitorowanie i przeglądanie dzienników przebiegów ml](how-to-monitor-view-training-logs.md).


> [!NOTE]
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]

### <a name="git-tracking-and-integration"></a>Śledzenie i integracja usługi git

Po rozpoczęciu szkolenia w przypadku, gdy katalog źródłowy jest lokalnym repozytorium git, informacje o repozytorium są przechowywane w historii uruchamiania. Działa to z przebiegami przesłanymi przy użyciu konfiguracji uruchamiania skryptu lub potoku ML. Działa również w przypadku przebiegów przesłanych z zestawu SDK lub interfejsu wiersza polecenia Machine Learning.

Aby uzyskać więcej informacji, zobacz Integracja z usługą [git dla Azure Machine Learning](concept-train-model-git-integration.md).

### <a name="training-workflow"></a>Przepływ pracy szkolenia

Po uruchomieniu eksperymentu w celu uczenia modelu należy wykonać następujące czynności. Są one zilustrowane na poniższym diagramie przepływu pracy szkoleniowej:

* Azure Machine Learning jest wywoływana z IDENTYFIKATORem migawki dla migawki kodu zapisanej w poprzedniej sekcji.
* Azure Machine Learning tworzy identyfikator uruchomienia (opcjonalnie) i token usługi Machine Learning, który jest później używany przez cele obliczeniowe, takie jak środowisko obliczeniowe usługi Machine Learning/VM, do komunikowania się z usługą Machine Learning.
* Aby uruchamiać zadania szkoleniowe, można wybrać zarządzany obiekt docelowy obliczeń (taki jak środowisko obliczeniowe usługi Machine Learning) lub niezarządzany obiekt docelowy obliczeń (na przykład maszyny wirtualne). Poniżej przedstawiono przepływy danych dla obu scenariuszy:
   * Maszyny wirtualne/HDInsight, do których dostęp odbywa się przy użyciu poświadczeń SSH w magazynie kluczy w ramach subskrypcji firmy Microsoft. Azure Machine Learning uruchamia kod zarządzania w obiekcie docelowym obliczeń, który:

   1. Przygotowuje środowisko. (Docker to opcja dla maszyn wirtualnych i komputerów lokalnych. Aby dowiedzieć się, jak działają eksperymenty w kontenerach platformy Docker, zobacz następujące środowisko obliczeniowe usługi Machine Learning kroki.
   1. Pobiera kod.
   1. Konfiguruje zmienne środowiskowe i konfiguracje.
   1. Uruchamia skrypty użytkownika (migawka kodu wymieniona w poprzedniej sekcji).

   * Środowisko obliczeniowe usługi Machine Learning dostęp do programu za pomocą tożsamości zarządzanej przez obszar roboczy.
Ponieważ środowisko obliczeniowe usługi Machine Learning jest zarządzanym elementem docelowym obliczeń (czyli jest zarządzany przez firmę Microsoft), jest uruchamiany w ramach Twojej subskrypcji firmy Microsoft.

   1. Zdalna konstrukcja platformy Docker jest wyłączona, w razie konieczności.
   1. Kod zarządzania jest zapisywana w udziale Azure Files użytkownika.
   1. Kontener jest uruchamiany przy użyciu polecenia początkowego. Oznacza to, że kod zarządzania zgodnie z opisem w poprzednim kroku.

* Po zakończeniu przebiegu można wykonywać zapytania o uruchomienia i metryki. Na poniższym diagramie przepływu ten krok występuje, gdy obiekt docelowy obliczeń szkolenia zapisuje metryki uruchamiania z powrotem do Azure Machine Learning z magazynu w bazie danych Cosmos DB. Klienci mogą wywoływać Azure Machine Learning. Machine Learning spowoduje włączenie metryk ściągania z bazy danych Cosmos DB i zwrócenie ich z powrotem do klienta.

[![Przepływ pracy szkolenia](media/concept-azure-machine-learning-architecture/training-and-metrics.png)](media/concept-azure-machine-learning-architecture/training-and-metrics.png#lightbox)

## <a name="models"></a>Modele

W najprostszej postaci model to fragment kodu, który pobiera dane wejściowe i generuje dane wyjściowe. Tworzenie modelu uczenia maszynowego obejmuje wybranie algorytmu, dostarczenie go z danymi i [dostrajanie parametrów](how-to-tune-hyperparameters.md). Uczenie to proces iteracyjny, który tworzy szkolony model, który hermetyzuje model, który jest uzyskiwany przez proces szkolenia.

Możesz przenieść model, który został przeszkolony poza Azure Machine Learning. Możesz też przeszkolić model, przesyłając [przebieg](#runs) [eksperymentu](#experiments) do [obiektu docelowego obliczeń](#compute-targets) w Azure Machine Learning. Po utworzeniu modelu należy [zarejestrować model](#register-model) w obszarze roboczym.

Azure Machine Learning to struktura niezależny od. Podczas tworzenia modelu można użyć dowolnej popularnej platformy uczenia maszynowego, takiej jak Scikit-Learning, XGBoost, PyTorch, TensorFlow i łańcucha.

Aby zapoznać się z przykładem szkoleń dotyczących modelu przy użyciu Scikit — zobacz [Samouczek: uczenie modelu klasyfikacji obrazów za pomocą Azure Machine Learning](tutorial-train-models-with-aml.md).


### <a name="model-registry"></a><a name="register-model"></a> Rejestr modelu

[Obszar roboczy](#workspace)  >  **Modele**

**Rejestr modelu** pozwala śledzić wszystkie modele w obszarze roboczym Azure Machine Learning.

Modele są identyfikowane według nazwy i wersji. Za każdym razem, gdy rejestrujesz model o takiej samej nazwie jak istniejący, rejestr założono, że jest to nowa wersja. Wersja jest zwiększana, a nowy model jest zarejestrowany pod tą samą nazwą.

Po zarejestrowaniu modelu można podać dodatkowe Tagi metadanych, a następnie użyć tagów podczas wyszukiwania modeli.

> [!TIP]
> Zarejestrowany model to logiczny kontener dla co najmniej jednego pliku, który składa się z modelu. Na przykład jeśli masz model, który jest przechowywany w wielu plikach, możesz zarejestrować je jako jeden model w obszarze roboczym Azure Machine Learning. Po zarejestrowaniu można pobrać lub wdrożyć zarejestrowany model i odebrać wszystkie zarejestrowane pliki.

Nie można usunąć zarejestrowanego modelu, który jest używany przez aktywne wdrożenie.

Aby zapoznać się z przykładem rejestrowania modelu, zobacz [uczenie modelu klasyfikacji obrazów przy użyciu Azure Machine Learning](tutorial-train-models-with-aml.md).

## <a name="deployment"></a>Wdrożenie

[Zastrzeżony model](#register-model) jest wdrażany jako punkt końcowy usługi. Potrzebne są następujące składniki:

* **Środowisko**. To środowisko hermetyzuje zależności wymagane do uruchomienia modelu do wnioskowania.
* **Kod oceniania**. Ten skrypt akceptuje żądania, ocenia żądania przy użyciu modelu i zwraca wyniki.
* **Konfiguracja wnioskowania**. Konfiguracja wnioskowania określa środowisko, skrypt wejścia i inne składniki, które są konieczne do uruchomienia modelu jako usługi.

Aby uzyskać więcej informacji o tych składnikach, zobacz [Wdrażanie modeli przy użyciu Azure Machine Learning](how-to-deploy-and-where.md).

### <a name="endpoints"></a>Punkty końcowe

[Obszar roboczy](#workspace)  >  **Punkty końcowe**

Punkt końcowy to tworzenie wystąpienia modelu w usłudze sieci Web, która może być hostowana w chmurze lub module IoT na potrzeby wdrożeń zintegrowanych urządzeń.

#### <a name="web-service-endpoint"></a>Punkt końcowy usługi sieci Web

W przypadku wdrażania modelu jako usługi sieci Web punkt końcowy można wdrożyć na Azure Container Instances, usłudze Azure Kubernetes lub FPGA. Usługę można utworzyć z modelu, skryptu i skojarzonych plików. Są one umieszczane w podstawowym obrazie kontenera, który zawiera środowisko wykonawcze dla modelu. Obraz zawiera punkt końcowy HTTP o zrównoważonym obciążeniu, który odbiera żądania oceniania wysyłane do usługi sieci Web.

Aby monitorować usługę sieci Web, można włączyć telemetrię Application Insights lub dane telemetryczne modelu. Dane telemetryczne są dostępne tylko dla Ciebie.  Jest ona przechowywana w Application Insights i wystąpieniach konta magazynu. Jeśli włączono automatyczne skalowanie, platforma Azure automatycznie skaluje wdrożenie.

Na poniższym diagramie przedstawiono przepływ pracy wnioskowania dla modelu wdrożonego jako punkt końcowy usługi sieci Web:

Oto szczegółowe informacje:

* Użytkownik rejestruje model przy użyciu klienta, takiego jak zestaw Azure Machine Learning SDK.
* Użytkownik tworzy obraz przy użyciu modelu, pliku wynikowego i innych zależności modelu.
* Obraz platformy Docker jest tworzony i przechowywany w Azure Container Registry.
* Usługa sieci Web jest wdrażana w obiekcie docelowym obliczeń (Container Instances/AKS) przy użyciu obrazu utworzonego w poprzednim kroku.
* Szczegóły żądania oceniania są przechowywane w Application Insights, które znajdują się w subskrypcji użytkownika.
* Dane telemetryczne są również wypychane do subskrypcji Microsoft/Azure.

[![Przepływ pracy wnioskowania](media/concept-azure-machine-learning-architecture/inferencing.png)](media/concept-azure-machine-learning-architecture/inferencing.png#lightbox)


Aby zapoznać się z przykładem wdrażania modelu jako usługi sieci Web, zobacz [Wdrażanie modelu klasyfikacji obrazów w Azure Container Instances](tutorial-deploy-models-with-aml.md).

#### <a name="real-time-endpoints"></a>Punkty końcowe w czasie rzeczywistym

Podczas wdrażania przeszkolonego modelu w projektancie można [wdrożyć model jako punkt końcowy w czasie rzeczywistym](tutorial-designer-automobile-price-deploy.md). Punkt końcowy w czasie rzeczywistym często odbiera pojedyncze żądanie za pośrednictwem punktu końcowego REST i zwraca prognozę w czasie rzeczywistym. Jest to w przeciwieństwie do przetwarzania wsadowego, który przetwarza wiele wartości jednocześnie i zapisuje wyniki po zakończeniu do magazynu danych.

#### <a name="pipeline-endpoints"></a>Punkty końcowe potoku

Punkty końcowe potoku umożliwiają wywoływanie [potoków ml](#ml-pipelines) programowo za pośrednictwem punktu końcowego REST. Punkty końcowe potoku umożliwiają automatyzację przepływów pracy potoku.

Punkt końcowy potoku to kolekcja opublikowanych potoków. Ta organizacja logiczna umożliwia zarządzanie wieloma potokami i wywoływanie ich przy użyciu tego samego punktu końcowego. Każdy opublikowany potok w punkcie końcowym potoku jest w wersji. Można wybrać domyślny potok dla punktu końcowego lub określić wersję w wywołaniu REST.
 

#### <a name="iot-module-endpoints"></a>Punkty końcowe modułu IoT

Wdrożony punkt końcowy modułu IoT to kontener platformy Docker, który obejmuje model i skojarzony skrypt lub aplikację oraz wszelkie dodatkowe zależności. Te moduły są wdrażane przy użyciu Azure IoT Edge na urządzeniach brzegowych.

Jeśli włączono monitorowanie, platforma Azure zbiera dane telemetryczne z modelu w module Azure IoT Edge. Dane telemetryczne są dostępne tylko dla Ciebie i są przechowywane w wystąpieniu konta magazynu.

Azure IoT Edge zapewnia, że moduł jest uruchomiony, i monitoruje urządzenie, które je obsługuje. 
## <a name="automation"></a>Automation

### <a name="azure-machine-learning-cli"></a>Interfejs wiersza polecenia usługi Azure Machine Learning 

Interfejs wiersza polecenia [Azure Machine Learning](reference-azure-machine-learning-cli.md) jest rozszerzeniem dla platformy Azure, który działa na wielu platformach. To rozszerzenie udostępnia polecenia służące do automatyzowania działań związanych z uczeniem maszynowym.

### <a name="ml-pipelines"></a>Potoki ML

[Potoki uczenia maszynowego](concept-ml-pipelines.md) umożliwiają tworzenie przepływów pracy, które łączą etapy uczenia maszynowego, oraz zarządzanie nimi. Na przykład potok może obejmować przygotowanie danych, szkolenie modeli, wdrożenie modelu i etapy wnioskowania/oceniania. Każda faza może obejmować wiele kroków, z których każdy może działać bez nadzoru w różnych obiektach docelowych obliczeń. 

Kroki potoku są wielokrotnego użytku i można je uruchomić bez ponownego uruchomienia poprzednich kroków, jeśli dane wyjściowe tych kroków nie uległy zmianie. Na przykład możesz ponownie przeprowadzić uczenie modelu bez ponownego uruchamiania kosztownych kroków przygotowywania danych, jeśli dane nie uległy zmianie. Potoki umożliwiają również analitykom danych współpracę podczas pracy nad osobnymi obszarami przepływu pracy uczenia maszynowego.

## <a name="interacting-with-your-workspace"></a>Korzystanie z obszaru roboczego

### <a name="studio"></a>Studio

Program [Azure Machine Learning Studio](overview-what-is-machine-learning-studio.md) udostępnia widok sieci Web wszystkich artefaktów w obszarze roboczym.  Możesz wyświetlić wyniki i szczegóły dotyczące zestawów danych, eksperymentów, potoków, modeli i punktów końcowych.  Można także zarządzać zasobami obliczeniowymi i magazynami danych w programie Studio.

W programie Studio znajduje się również dostęp do interaktywnych narzędzi, które są częścią Azure Machine Learning:

+ [Azure Machine Learning projektanta](concept-designer.md) do wykonywania kroków przepływu pracy bez pisania kodu
+ Środowisko internetowe umożliwiające [Automatyczne Uczenie maszynowe](concept-automated-ml.md)
+ [Azure Machine Learning notesy](how-to-run-jupyter-notebooks.md) do pisania i uruchamiania własnego kodu na zintegrowanych serwerach Jupyter Notes.
+ Tworzenie [etykietowania danych](how-to-create-labeling-projects.md) w celu tworzenia i monitorowania projektów oraz zarządzania nimi w celu etykietowania danych

### <a name="programming-tools"></a>Narzędzia programistyczne

> [!IMPORTANT]
> Narzędzia oznaczone poniżej (wersja zapoznawcza) są obecnie dostępne w publicznej wersji zapoznawczej.
> Wersja zapoznawcza jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

+  Korzystanie z usługi w dowolnym środowisku języka Python z [zestawem SDK Azure Machine Learning dla języka Python](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py).
+ Korzystanie z usługi w dowolnym środowisku R z [zestawem SDK Azure Machine Learning dla języka r](https://azure.github.io/azureml-sdk-for-r/reference/index.html) (wersja zapoznawcza).
+ Za pomocą [projektanta Azure Machine Learning](concept-designer.md) wykonaj kroki przepływu pracy bez pisania kodu. 
+ Użyj [interfejsu wiersza polecenia Azure Machine Learning](./reference-azure-machine-learning-cli.md) do automatyzacji.
+ [Akcelerator rozwiązań wielu modeli](https://aka.ms/many-models) (wersja zapoznawcza) jest oparty na Azure Machine Learning i umożliwia uczenie, obsługę setek lub nawet tysięcy modeli uczenia maszynowego oraz zarządzanie nimi.

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć pracę z Azure Machine Learning, zobacz:

* [Co to jest usługa Azure Machine Learning?](overview-what-is-azure-ml.md)
* [Tworzenie obszaru roboczego usługi Azure Machine Learning](how-to-manage-workspace.md)
* [Samouczek (część 1): uczenie modelu](tutorial-train-models-with-aml.md)