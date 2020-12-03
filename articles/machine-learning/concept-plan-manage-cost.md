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
ms.openlocfilehash: 3eb9df0a0fde5d99bbeb3c2da182b5957fdea1e3
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96532928"
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

Analiza kosztów obsługuje różne typy kont platformy Azure. Aby wyświetlić pełną listę obsługiwanych typów kont, zobacz [Omówienie danych usługi Cost Management](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Aby wyświetlić dane kosztów, potrzebujesz przynajmniej dostępu do odczytu dla Twojego konta platformy Azure. 

Aby uzyskać informacje na temat przypisywania dostępu do danych usługi Azure Cost Management, zobacz [Przypisywanie dostępu do danych](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="estimate-costs-before-using-azure-machine-learning"></a>Oszacuj koszty przed użyciem Azure Machine Learning

Skorzystaj z [kalkulatora cen platformy Azure](https://azure.microsoft.com/pricing/calculator?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) , aby oszacować koszty przed utworzeniem zasobów na koncie Azure Machine Learning. Po lewej stronie wybierz pozycję **AI + Machine Learning**, a następnie wybierz pozycję **Azure Machine Learning** , aby rozpocząć.  

Poniższy zrzut ekranu przedstawia oszacowanie kosztów za pomocą kalkulatora:

:::image type="content" source="media/concept-plan-manage-cost/capacity-calculator-cost-estimate.png" alt-text="Oszacowanie kosztów w kalkulatorze platformy Azure":::

Dodając nowe zasoby do obszaru roboczego, Wróć do tego kalkulatora i Dodaj tutaj ten sam zasób, aby zaktualizować oszacowania kosztów.

Aby uzyskać więcej informacji, zobacz [Cennik usługi Azure Machine Learning](https://azure.microsoft.com/pricing/details/machine-learning?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="understand-the-full-billing-model-for-azure-machine-learning"></a>Zapoznaj się z pełnym modelem rozliczeń dla Azure Machine Learning

Azure Machine Learning działa w infrastrukturze platformy Azure, która narasta koszty wraz z Azure Machine Learning podczas wdrażania nowego zasobu. Ważne jest, aby zrozumieć, że dodatkowa infrastruktura może naliczać koszt. Ten koszt należy zarządzać po wprowadzeniu zmian w wdrożonych zasobach. 

### <a name="costs-that-typically-accrue-with-azure-machine-learning"></a>Koszty, które zwykle naliczane są przy użyciu Azure Machine Learning

Podczas tworzenia zasobów dla obszaru roboczego Azure Machine Learning są również tworzone zasoby dla innych usług platformy Azure. Są to:

* [Azure Container Registry](https://azure.microsoft.com/pricing/details/container-registry?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) Konto podstawowe
* [Azure Block BLOB Storage](https://azure.microsoft.com/pricing/details/storage/blobs?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) (ogólnego przeznaczenia w wersji 1)
* [Usługa Key Vault](https://azure.microsoft.com/pricing/details/key-vault?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* [Application Insights](https://azure.microsoft.com/en-us/pricing/details/monitor?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
 
### <a name="costs-might-accrue-after-resource-deletion"></a>Koszty mogą naliczane po usunięciu zasobów

Po usunięciu obszaru roboczego Azure Machine Learning w Azure Portal lub przy użyciu interfejsu wiersza polecenia platformy Azure nadal istnieją następujące zasoby. W dalszym ciągu naliczane są koszty, dopóki nie zostaną usunięte.

* Azure Container Registry
* Blob Storage blokowe platformy Azure
* Key Vault
* Application Insights

Aby usunąć obszar roboczy wraz z tymi zasobami zależnymi, użyj zestawu SDK:

```python
ws.delete(delete_dependent_resources=True)
```

Jeśli utworzysz usługę Azure Kubernetes Service (AKS) w obszarze roboczym lub dołączysz wszystkie zasoby obliczeniowe do obszaru roboczego, musisz je usunąć oddzielnie w [Azure Portal](https://portal.azure.com).

### <a name="using-monetary-credit-with-azure-machine-learning"></a>Korzystanie z środków pieniężnych w Azure Machine Learning

Opłaty za Azure Machine Learning są naliczane za zobowiązania pieniężne z tytułu umowy EA. Nie można jednak użyć kredytu zobowiązania pieniężnego umowy EA, aby uregulować opłaty za produkty i usługi innych firm, w tym te z portalu Azure Marketplace.


## <a name="create-budgets"></a>Tworzenie budżetów

W celu zarządzania kosztami można utworzyć [budżety](https://docs.microsoft.com/azure/cost-management/tutorial-acm-create-budgets?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) oraz [alerty](https://docs.microsoft.com/azure/cost-management/cost-mgt-alerts-monitor-usage-spending?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn), które automatycznie powiadamiają uczestników o anomaliach w wydatkach i o ryzyku nadmiernych wydatków. Alerty są oparte na wydatkach w porównaniu z budżetem i progami kosztów. Budżety i alerty są tworzone dla subskrypcji platformy Azure i grup zasobów, dzięki czemu są one przydatne jako część ogólnej strategii monitorowania kosztów. 

Budżety można utworzyć za pomocą filtrów dla określonych zasobów lub usług na platformie Azure, jeśli chcesz mieć większą szczegółowość w monitorowaniu. Filtry ułatwiają zagwarantowanie, że nie zostaną przypadkowo utworzone nowe zasoby, które będą kosztowały dodatkowe pieniądze. Aby uzyskać więcej informacji na temat opcji filtrowania podczas tworzenia budżetu, zobacz [Opcje grup i filtrów](https://docs.microsoft.com/azure/cost-management-billing/costs/group-filter?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="export-cost-data"></a>Eksportowanie danych kosztów

Możesz również [wyeksportować dane kosztu](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) do konta magazynu. Jest to przydatne, gdy potrzebujesz lub inne osoby mogą przeprowadzić dodatkową analizę danych pod kątem kosztów. Na przykład zespoły finansowe mogą analizować dane przy użyciu programu Excel lub Power BI. Możesz wyeksportować koszty codziennie, co tydzień lub miesięcznie i ustawić niestandardowy zakres dat. Eksportowanie danych o kosztach jest zalecanym sposobem na pobranie z nich zbiorów.

## <a name="other-ways-to-manage-and-reduce-costs-for-azure-machine-learning"></a>Inne sposoby zarządzania i obniżyć koszty Azure Machine Learning

Skorzystaj z tych porad dotyczących kosztów zawierających koszty zasobów obliczeniowych usługi Machine Learning.

### <a name="use-azure-machine-learning-compute-cluster-amlcompute"></a>Użyj klastra obliczeniowego Azure Machine Learning (AmlCompute)

Dzięki ciągłej zmianie danych potrzebne są szybkie i usprawnione szkolenia modeli oraz przeszkolenie w celu utrzymania dokładnych modeli. Jednak ciągłe szkolenie jest kosztem, szczególnie w przypadku modeli uczenia głębokiego w procesorach GPU. 

Azure Machine Learning użytkownicy mogą korzystać z zarządzanego Azure Machine Learning klastra obliczeniowego, nazywanego również AmlCompute. AmlCompute obsługuje wiele opcji procesora GPU i procesorów. AmlCompute jest wewnętrznie hostowana w imieniu subskrypcji przez Azure Machine Learning. Zapewnia to takie samo bezpieczeństwo, zgodność i zarządzanie, na platformie Azure IaaS w skali chmury.

Ponieważ te pule obliczeniowe należą do infrastruktury IaaS platformy Azure, można wdrożyć, skalować i zarządzać szkoleniami z tymi samymi wymaganiami dotyczącymi zabezpieczeń i zgodności, co w przypadku reszty infrastruktury.  Te wdrożenia są wykonywane w ramach subskrypcji i przestrzegają reguł zarządzania. Dowiedz się więcej na temat [Azure Machine Learning obliczeń](how-to-create-attach-compute-cluster.md).

### <a name="configure-training-clusters-for-autoscaling"></a>Konfigurowanie klastrów szkoleniowych na potrzeby automatycznego skalowania

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

### <a name="set-quotas-on-resources"></a>Ustawianie przydziałów zasobów

AmlCompute zawiera [konfigurację limitu przydziału (lub limitu)](how-to-manage-quotas.md#azure-machine-learning-compute). Ten przydział dotyczy rodziny maszyn wirtualnych (na przykład serii Dv2, Seria NCV3 Series) i różni się w zależności od regionu dla każdej subskrypcji. Subskrypcje zaczynają się od małych wartości domyślnych, aby można było je uzyskać, ale przy użyciu tego ustawienia można kontrolować ilość zasobów Amlcompute dostępnych do odtworzenia w ramach subskrypcji. 

Skonfiguruj również [przydział poziomu obszaru roboczego według rodziny maszyn wirtualnych](how-to-manage-quotas.md#workspace-level-quotas)dla każdego obszaru roboczego w ramach subskrypcji. Dzięki temu można mieć dokładniejszą kontrolę nad kosztami, które mogą zostać naliczone przez każdy obszar roboczy i ograniczyć liczbę rodzin maszyn wirtualnych. 

Aby ustawić przydziały na poziomie obszaru roboczego, Uruchom w [Azure Portal](https://portal.azure.com).  Wybierz dowolny obszar roboczy w ramach subskrypcji, a następnie wybierz pozycję **użycie + przydziały** w okienku po lewej stronie. Następnie wybierz kartę **Konfigurowanie przydziałów** , aby wyświetlić przydziały. Aby ustawić limit przydziału, potrzebne są uprawnienia, ponieważ jest to ustawienie wpływające na wiele obszarów roboczych.

### <a name="set-run-autotermination-policies"></a>Ustaw zasady autokończenia uruchamiania 

W niektórych przypadkach należy skonfigurować przebiegi szkoleniowe, aby ograniczyć ich czas trwania lub przerwać ich wczesne działanie. Na przykład w przypadku korzystania z wbudowanego dostrajania preparameteru Azure Machine Learning lub automatycznego uczenia maszynowego.

Oto kilka opcji, których dysponujesz:
* Zdefiniuj parametr wywoływany `max_run_duration_seconds` w RunConfiguration, aby kontrolować maksymalny czas trwania uruchomienia w ramach wybranego obliczenia (lokalnie lub w chmurze).
* W przypadku [dostrajania parametrów](how-to-tune-hyperparameters.md#early-termination)należy zdefiniować zasady wczesnego zakończenia na podstawie zasad Banditymi, średniego zatrzymywania zasad lub zasad wyboru obcinania. Aby dodatkowo kontrolować odchylenia parametrów, użyj parametrów takich jak `max_total_runs` lub `max_duration_minutes` .
* Aby [zautomatyzować automatyczne Uczenie maszynowe](how-to-configure-auto-train.md#exit), ustaw podobne zasady kończenia przy użyciu  `enable_early_stopping` flagi. Należy również użyć właściwości, takich jak `iteration_timeout_minutes` i, `experiment_timeout_minutes` Aby kontrolować maksymalny czas trwania przebiegu lub dla całego eksperymentu.

### <a name="use-low-priority-vms"></a><a id="low-pri-vm"></a> Korzystanie z maszyn wirtualnych o niskim priorytecie

System Azure umożliwia używanie nadmiernej pojemności niewykorzystywanej jako Low-Priority maszyn wirtualnych w ramach zestawów skalowania maszyn wirtualnych, partii i usługi Machine Learning. Te przydziały są wstępnie emptible, ale mają obniżoną cenę w porównaniu do dedykowanych maszyn wirtualnych. Ogólnie rzecz biorąc, zalecamy używanie Low-Priority maszyn wirtualnych do obsługi obciążeń wsadowych. Należy również użyć ich w przypadku, gdy przerwy są odzyskiwane za pomocą ponownych przesyłania (dla usługi Batch Inferencing) lub przez ponowne uruchomienie (w przypadku szkolenia głębokiego uczenia się z punktem kontrolnym).

Low-Priority maszyny wirtualne mają jeden przydział oddzielony od dedykowanej wartości przydziału, czyli przez rodzinę maszyn wirtualnych. Dowiedz się [więcej na temat przydziałów AmlCompute](how-to-manage-quotas.md).

 Low-Priority maszyny wirtualne nie działają w przypadku wystąpień obliczeniowych, ponieważ wymagają one obsługi interaktywnego środowiska notesu.

### <a name="use-reserved-instances"></a>Użycie wystąpień zarezerwowanych

Innym sposobem oszczędności zasobów obliczeniowych jest wystąpienie zarezerwowane maszyny wirtualnej platformy Azure. Dzięki tej ofercie możesz zatwierdzić jeden rok lub trzy lata. Rabaty te obejmują do 72% cen płatności zgodnie z rzeczywistym użyciem i są stosowane bezpośrednio na miesięcznym rachunku za platformę Azure.

Azure Machine Learning COMPUTE obsługuje zarezerwowane wystąpienia. W przypadku zakupienia jednego roku lub z 3-letniego wystąpienia zarezerwowanego automatycznie stosuje się rabat do Azure Machine Learning zarządzanych obliczeń.


## <a name="next-steps"></a>Następne kroki

- Dowiedz się [, jak zoptymalizować inwestycję w chmurę za pomocą Azure Cost Management](https://docs.microsoft.com/azure/cost-management-billing/costs/cost-mgt-best-practices?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Dowiedz się więcej o zarządzaniu kosztami przy użyciu [analizy kosztów](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Dowiedz się więcej na temat [zapobiegania nieoczekiwanym kosztom](https://docs.microsoft.com/azure/cost-management-billing/manage/getting-started?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Zapoznaj się z [Cost Management](https://docs.microsoft.com/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) kurs uczenia z przewodnikiem.