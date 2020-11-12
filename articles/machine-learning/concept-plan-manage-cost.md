---
title: Planowanie kosztów i zarządzanie nimi
titleSuffix: Azure Machine Learning
description: Planowanie i zarządzanie kosztami Azure Machine Learning przy użyciu analizy kosztów w Azure Portal. Zapoznaj się z kolejnymi wskazówkami dotyczącymi oszczędności kosztów, aby obniżyć koszty podczas tworzenia modeli ML.
author: sdgilley
ms.author: sgilley
ms.custom: subject-cost-optimization
ms.reviewer: nigup
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: a0e0f61da52ce00fb2eb4b4a7d95ab74082f9472
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94541712"
---
# <a name="plan-and-manage-costs-for-azure-machine-learning"></a>Planowanie i zarządzanie kosztami Azure Machine Learning

W tym artykule opisano sposób planowania kosztów Azure Machine Learning i zarządzania nimi. Najpierw należy skorzystać z kalkulatora cen platformy Azure, aby ułatwić planowanie kosztów przed dodaniem zasobów. Następnie podczas dodawania zasobów platformy Azure Przejrzyj szacowane koszty. Na koniec należy skorzystać ze wskazówek dotyczących oszczędności kosztów podczas uczenia modelu z zarządzanymi Azure Machine Learning klastrami obliczeniowymi.

Po rozpoczęciu korzystania z Azure Machine Learning zasobów Skorzystaj z funkcji zarządzania kosztami, aby ustawić budżety i monitorować koszty. Zapoznaj się również z prognozowanymi kosztami i zidentyfikuj trendy wydatków, aby zidentyfikować obszary, w których może być konieczne działanie.

Należy zrozumieć, że koszty Azure Machine Learning są tylko częścią miesięcznych kosztów rachunku na korzystanie z platformy Azure. Jeśli używasz innych usług platformy Azure, opłaty są naliczane za wszystkie usługi i zasoby platformy Azure używane w ramach subskrypcji platformy Azure, w tym usługi innych firm. W tym artykule wyjaśniono, jak planować i zarządzać kosztami Azure Machine Learning. Po zapoznaniu się z zarządzaniem kosztami Azure Machine Learning stosuj podobne metody, aby zarządzać kosztami wszystkich usług platformy Azure używanych w ramach subskrypcji.

Podczas uczenia modeli uczenia maszynowego Użyj zarządzanych Azure Machine Learning klastrów obliczeniowych, aby skorzystać z dodatkowych porad dotyczących oszczędności:

* Konfigurowanie klastrów szkoleniowych na potrzeby automatycznego skalowania
* Ustawianie przydziałów dla Twojej subskrypcji i obszarów roboczych
* Ustawianie zasad zakończenia w przebiegu szkoleniowym
* Używanie maszyn wirtualnych o niskim priorytecie (VM)
* Korzystanie z wystąpienia zarezerwowanego maszyny wirtualnej platformy Azure

## <a name="prerequisites"></a>Wymagania wstępne

Analiza kosztów obsługuje różne typy kont platformy Azure. Aby wyświetlić pełną listę obsługiwanych typów kont, zobacz [Omówienie danych usługi Cost Management](../cost-management-billing/costs/understand-cost-mgt-data.md). Aby wyświetlić dane kosztów, potrzebujesz przynajmniej dostępu do odczytu dla Twojego konta platformy Azure. 

Aby uzyskać informacje na temat przypisywania dostępu do danych usługi Azure Cost Management, zobacz [Przypisywanie dostępu do danych](../cost-management-billing/costs/assign-access-acm-data.md).

## <a name="estimate-costs"></a>Szacowanie kosztów

Skorzystaj z [kalkulatora cen platformy Azure](https://azure.microsoft.com/pricing/calculator/) , aby oszacować koszty przed utworzeniem zasobów na koncie Azure Machine Learning. Po lewej stronie wybierz pozycję **AI + Machine Learning** , a następnie wybierz pozycję **Azure Machine Learning** , aby rozpocząć.  

Poniższy zrzut ekranu przedstawia oszacowanie kosztów za pomocą kalkulatora:

:::image type="content" source="media/concept-plan-manage-cost/capacity-calculator-cost-estimate.png" alt-text="Oszacowanie kosztów w kalkulatorze platformy Azure":::

Dodając nowe zasoby do obszaru roboczego, Wróć do tego kalkulatora i Dodaj tutaj ten sam zasób, aby zaktualizować oszacowania kosztów.

Aby uzyskać więcej informacji, zobacz [Cennik usługi Azure Machine Learning](https://azure.microsoft.com/pricing/details/machine-learning/).

## <a name="get-cost-alerts"></a>Pobierz alerty kosztów

Twórz [budżety](../cost-management-billing/costs/tutorial-acm-create-budgets.md) , aby zarządzać kosztami i tworzyć [alerty](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md) , które automatycznie powiadamiają uczestników wydatków o wykorzystaniu anomalii i poświęcają ryzyko. Alerty są oparte na wydatkach w porównaniu z budżetem i progami kosztów. Budżety i alerty są tworzone dla subskrypcji platformy Azure i grup zasobów, dzięki czemu są one przydatne jako część ogólnej strategii monitorowania kosztów. Jednak budżety i alerty mogą mieć ograniczoną funkcjonalność do zarządzania poszczególnymi kosztami usług platformy Azure, ponieważ są one przeznaczone do śledzenia kosztów na wyższym poziomie.

## <a name="monitor-costs"></a>Monitorowanie kosztów

Gdy korzystasz z zasobów z Azure Machine Learning, naliczane są koszty. Koszty jednostki użycia zasobów platformy Azure różnią się w zależności od interwału czasu (sekundy, minuty, godziny i dni) lub według użycia jednostki żądania. Po rozpoczęciu korzystania z Azure Machine Learning są naliczane koszty. Zapoznaj się z tymi kosztami w okienku [Analiza kosztów](../cost-management-billing/costs/quick-acm-cost-analysis.md) w Azure Portal.

Możesz wyświetlić koszty w wykresach i tabelach dla różnych przedziałów czasu. Możesz również wyświetlić koszty związane z budżetami i prognozowanymi kosztami. Przełączenie na dłuższe widoki w czasie pomaga identyfikować trendy wydatków i zobaczyć, gdzie mogły wystąpić nadwyżki. Jeśli utworzono budżety, zobacz miejsce ich przekroczenia.  

Nie zobaczysz oddzielnego obszaru usługi dla Machine Learning.  Zamiast tego zobaczysz różne zasoby, które zostały dodane do obszarów roboczych Machine Learning. 

Należy pamiętać, że w przypadku, gdy obszar roboczy Machine Learning nie ma bezpośredniego kosztu, są naliczane opłaty za zarządzane zasoby obliczeniowe. W związku z tym, jeśli dodano Tagi do obszarów roboczych, ponieważ nie ma on bezpośredniego kosztu, w okienku analiza kosztów zostanie odzwierciedlone to dla obszaru roboczego. Aby uzyskać dokładne zarządzanie kosztami za pośrednictwem tagów, należy oznaczyć skojarzone zasoby obliczeniowe.  

## <a name="use-azure-machine-learning-compute-cluster-amlcompute"></a>Użyj klastra obliczeniowego Azure Machine Learning (AmlCompute)

Dzięki ciągłej zmianie danych potrzebne są szybkie i usprawnione szkolenia modeli oraz przeszkolenie w celu utrzymania dokładnych modeli. Jednak ciągłe szkolenie jest kosztem, szczególnie w przypadku modeli uczenia głębokiego w procesorach GPU. 

Azure Machine Learning użytkownicy mogą korzystać z zarządzanego Azure Machine Learning klastra obliczeniowego, nazywanego również AmlCompute. AmlCompute obsługuje wiele opcji procesora GPU i procesorów. AmlCompute jest wewnętrznie hostowana w imieniu subskrypcji przez Azure Machine Learning. Zapewnia to takie samo bezpieczeństwo, zgodność i zarządzanie, na platformie Azure IaaS w skali chmury.

Ponieważ te pule obliczeniowe należą do infrastruktury IaaS platformy Azure, można wdrożyć, skalować i zarządzać szkoleniami z tymi samymi wymaganiami dotyczącymi zabezpieczeń i zgodności, co w przypadku reszty infrastruktury.  Te wdrożenia są wykonywane w ramach subskrypcji i przestrzegają reguł zarządzania. Dowiedz się więcej na temat [Azure Machine Learning obliczeń](how-to-create-attach-compute-cluster.md).

## <a name="configure-training-clusters-for-autoscaling"></a>Konfigurowanie klastrów szkoleniowych na potrzeby automatycznego skalowania

Automatyczne skalowanie klastrów na podstawie wymagań obciążeń pozwala zmniejszyć koszty, dzięki czemu możesz korzystać tylko z tego, czego potrzebujesz.

Klastry AmlCompute są przeznaczone do skalowania dynamicznie na podstawie obciążenia. Klaster może być skalowany w górę do maksymalnej skonfigurowanej liczby węzłów. Po zakończeniu każdego uruchomienia klaster będzie zwalniał węzły i skalować je do skonfigurowanej minimalnej liczby węzłów.

[!INCLUDE [min-nodes-note](../../includes/machine-learning-min-nodes.md)]

Można również skonfigurować ilość czasu bezczynności węzła przed scaleniem w dół. Domyślnie czas bezczynności przed skalą w dół jest ustawiony na 120 sekund.

+ W przypadku przeprowadzania mniej iteracyjnych eksperymentów należy skrócić ten czas, aby zaoszczędzić koszty.
+ W przypadku przeprowadzania wysoko iteracyjnych eksperymentów deweloperskich i testowych może być konieczne zwiększenie czasu, aby nie płacić za stałe skalowanie w górę i w dół po każdej zmianie w skrypcie lub środowisku szkoleniowym.

Klastry AmlCompute można skonfigurować w celu zmiany wymagań obciążeń w Azure Portal, przy użyciu [klasy zestawu SDK AmlCompute](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?preserve-view=true&view=azure-ml-py), [interfejsu wiersza polecenia AmlCompute](/cli/azure/ext/azure-cli-ml/ml/computetarget/create?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute)z [interfejsami API REST](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/machinelearningservices/resource-manager/Microsoft.MachineLearningServices/stable).

```azurecli
az ml computetarget create amlcompute --name testcluster --vm-size Standard_NC6 --min-nodes 0 --max-nodes 5 --idle-seconds-before-scaledown 300
```

## <a name="set-quotas-on-resources"></a>Ustawianie przydziałów zasobów

AmlCompute zawiera [konfigurację limitu przydziału (lub limitu)](how-to-manage-quotas.md#azure-machine-learning-compute). Ten przydział dotyczy rodziny maszyn wirtualnych (na przykład serii Dv2, Seria NCV3 Series) i różni się w zależności od regionu dla każdej subskrypcji. Subskrypcje zaczynają się od małych wartości domyślnych, aby można było je uzyskać, ale przy użyciu tego ustawienia można kontrolować ilość zasobów Amlcompute dostępnych do odtworzenia w ramach subskrypcji. 

Skonfiguruj również [przydział poziomu obszaru roboczego według rodziny maszyn wirtualnych](how-to-manage-quotas.md#workspace-level-quotas)dla każdego obszaru roboczego w ramach subskrypcji. Dzięki temu można mieć dokładniejszą kontrolę nad kosztami, które mogą zostać naliczone przez każdy obszar roboczy i ograniczyć liczbę rodzin maszyn wirtualnych. 

Aby ustawić przydziały na poziomie obszaru roboczego, Uruchom w [Azure Portal](https://portal.azure.com).  Wybierz dowolny obszar roboczy w ramach subskrypcji, a następnie wybierz pozycję **użycie + przydziały** w okienku po lewej stronie. Następnie wybierz kartę **Konfigurowanie przydziałów** , aby wyświetlić przydziały. Aby ustawić limit przydziału, potrzebne są uprawnienia, ponieważ jest to ustawienie wpływające na wiele obszarów roboczych.

## <a name="set-run-autotermination-policies"></a>Ustaw zasady autokończenia uruchamiania 

W niektórych przypadkach należy skonfigurować przebiegi szkoleniowe, aby ograniczyć ich czas trwania lub przerwać ich wczesne działanie. Na przykład w przypadku korzystania z wbudowanego dostrajania preparameteru Azure Machine Learning lub automatycznego uczenia maszynowego.

Oto kilka opcji, których dysponujesz:
* Zdefiniuj parametr wywoływany `max_run_duration_seconds` w RunConfiguration, aby kontrolować maksymalny czas trwania uruchomienia w ramach wybranego obliczenia (lokalnie lub w chmurze).
* W przypadku [dostrajania parametrów](how-to-tune-hyperparameters.md#early-termination)należy zdefiniować zasady wczesnego zakończenia na podstawie zasad Banditymi, średniego zatrzymywania zasad lub zasad wyboru obcinania. Aby dodatkowo kontrolować odchylenia parametrów, użyj parametrów takich jak `max_total_runs` lub `max_duration_minutes` .
* Aby [zautomatyzować automatyczne Uczenie maszynowe](how-to-configure-auto-train.md#exit), ustaw podobne zasady kończenia przy użyciu  `enable_early_stopping` flagi. Należy również użyć właściwości, takich jak `iteration_timeout_minutes` i, `experiment_timeout_minutes` Aby kontrolować maksymalny czas trwania przebiegu lub dla całego eksperymentu.

## <a name="use-low-priority-vms"></a><a id="low-pri-vm"></a> Korzystanie z maszyn wirtualnych o niskim priorytecie

System Azure umożliwia używanie nadmiernej pojemności niewykorzystywanej jako Low-Priority maszyn wirtualnych w ramach zestawów skalowania maszyn wirtualnych, partii i usługi Machine Learning. Te przydziały są wstępnie emptible, ale mają obniżoną cenę w porównaniu do dedykowanych maszyn wirtualnych. Ogólnie rzecz biorąc, zalecamy używanie Low-Priority maszyn wirtualnych do obsługi obciążeń wsadowych. Należy również użyć ich w przypadku, gdy przerwy są odzyskiwane za pomocą ponownych przesyłania (dla usługi Batch Inferencing) lub przez ponowne uruchomienie (w przypadku szkolenia głębokiego uczenia się z punktem kontrolnym).

Low-Priority maszyny wirtualne mają jeden przydział oddzielony od dedykowanej wartości przydziału, czyli przez rodzinę maszyn wirtualnych. Dowiedz się [więcej na temat przydziałów AmlCompute](how-to-manage-quotas.md).

 Low-Priority maszyny wirtualne nie działają w przypadku wystąpień obliczeniowych, ponieważ wymagają one obsługi interaktywnego środowiska notesu.

## <a name="use-reserved-instances"></a>Użycie wystąpień zarezerwowanych

Innym sposobem oszczędności zasobów obliczeniowych jest wystąpienie zarezerwowane maszyny wirtualnej platformy Azure. Dzięki tej ofercie możesz zatwierdzić jeden rok lub trzy lata. Rabaty te obejmują do 72% cen płatności zgodnie z rzeczywistym użyciem i są stosowane bezpośrednio na miesięcznym rachunku za platformę Azure.

Azure Machine Learning COMPUTE obsługuje zarezerwowane wystąpienia. W przypadku zakupienia jednego roku lub z 3-letniego wystąpienia zarezerwowanego automatycznie stosuje się rabat do Azure Machine Learning zarządzanych obliczeń.


## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej:
* [Zarządzanie i zwiększanie przydziałów zasobów](how-to-manage-quotas.md)
* [Zarządzanie kosztami przy użyciu analizy kosztów](../cost-management-billing/costs/quick-acm-cost-analysis.md).
* Utwórz Azure Machine Learning COMPUTE przy użyciu [zestawu SDK](how-to-create-attach-compute-cluster.md) lub w programie [Studio](how-to-create-attach-compute-studio.md#amlcompute).