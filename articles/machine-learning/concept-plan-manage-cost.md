---
title: Planowanie kosztów i zarządzanie nimi
titleSuffix: Azure Machine Learning
description: Planowanie i zarządzanie kosztami dla Azure Machine Learning za pomocą analizy kosztów w Azure Portal. Poznaj dodatkowe porady dotyczące oszczędzania kosztów, aby obniżyć koszty podczas tworzenia modeli uczenia maszynowego.
author: sdgilley
ms.author: sgilley
ms.custom: subject-cost-optimization, devx-track-azurecli
ms.reviewer: nigup
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: d8f74e438175758b1f09e1809b5eba15c1b26c3c
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107873635"
---
# <a name="plan-and-manage-costs-for-azure-machine-learning"></a>Planowanie kosztów dla Azure Machine Learning

W tym artykule opisano sposób planowania kosztów dla Azure Machine Learning. Najpierw użyj kalkulatora cen platformy Azure, aby zaplanować koszty przed dodaniem jakichkolwiek zasobów. Następnie, podczas dodawania zasobów platformy Azure, przejrzyj szacowane koszty. Na koniec skorzystaj ze wskazówek dotyczących oszczędzania kosztów podczas trenowania modelu przy użyciu zarządzanych Azure Machine Learning obliczeniowych.

Po zakończeniu korzystania z zasobów Azure Machine Learning, użyj funkcji zarządzania kosztami, aby ustawić budżety i monitorować koszty. Przejrzyj również prognozowane koszty i zidentyfikuj trendy wydatków, aby zidentyfikować obszary, w których możesz chcieć działać.

Należy pamiętać, że koszty Azure Machine Learning są tylko częścią miesięcznych kosztów na rachunku za platformę Azure. Jeśli używasz innych usług platformy Azure, opłaty są naliczane za wszystkie usługi i zasoby platformy Azure używane w ramach subskrypcji platformy Azure, w tym za usługi innych firm. W tym artykule wyjaśniono, jak planować koszty związane z Azure Machine Learning. Po zapoznaniu się z zarządzaniem kosztami usługi Azure Machine Learning zastosuj podobne metody zarządzania kosztami dla wszystkich usług platformy Azure używanych w ramach subskrypcji.

Podczas trenowania modeli uczenia maszynowego użyj zarządzanych klastrów obliczeniowych Azure Machine Learning, aby skorzystać z większej liczby wskazówek dotyczących oszczędności:

* Konfigurowanie klastrów treningowych na celu skalowanie automatyczne
* Ustawianie limitów przydziału dla subskrypcji i obszarów roboczych
* Ustawianie zasad zakończenia w przebiegu trenowania
* Używanie maszyn wirtualnych o niskim priorytecie
* Korzystanie z wystąpienia zarezerwowanego maszyny wirtualnej platformy Azure

## <a name="prerequisites"></a>Wymagania wstępne

Analiza kosztów obsługuje różne typy kont platformy Azure. Aby wyświetlić pełną listę obsługiwanych typów kont, zobacz [Omówienie danych usługi Cost Management](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Aby wyświetlić dane kosztów, potrzebujesz przynajmniej dostępu do odczytu dla Twojego konta platformy Azure. 

Aby uzyskać informacje na temat przypisywania dostępu do danych usługi Azure Cost Management, zobacz [Przypisywanie dostępu do danych](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="estimate-costs-before-using-azure-machine-learning"></a>Szacowanie kosztów przed użyciem Azure Machine Learning

Skorzystaj z [kalkulatora cen platformy Azure,](https://azure.microsoft.com/pricing/calculator?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) aby oszacować koszty przed utworzeniem zasobów na Azure Machine Learning konta. Po lewej stronie wybierz pozycję **AI + Machine Learning**, a następnie Azure Machine Learning **rozpocząć.**  

Poniższy zrzut ekranu przedstawia szacowanie kosztów przy użyciu kalkulatora:

:::image type="content" source="media/concept-plan-manage-cost/capacity-calculator-cost-estimate.png" alt-text="Szacowanie kosztów w kalkulatorze platformy Azure":::

Po dodaniu nowych zasobów do obszaru roboczego wróć do tego kalkulatora i dodaj ten sam zasób w tym miejscu, aby zaktualizować szacowane koszty.

Aby uzyskać więcej informacji, [zobacz Azure Machine Learning cennika.](https://azure.microsoft.com/pricing/details/machine-learning?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)

## <a name="understand-the-full-billing-model-for-azure-machine-learning"></a>Opis pełnego modelu rozliczeń dla Azure Machine Learning

Azure Machine Learning działa w infrastrukturze platformy Azure, która nalicza koszty wraz Azure Machine Learning podczas wdrażania nowego zasobu. Ważne jest, aby zrozumieć, że dodatkowa infrastruktura może kosztować koszty. Musisz zarządzać tym kosztami, gdy wprowadzasz zmiany w wdrożonych zasobach. 

### <a name="costs-that-typically-accrue-with-azure-machine-learning"></a>Koszty, które zazwyczaj są naliczane w Azure Machine Learning

Podczas tworzenia zasobów dla obszaru roboczego Azure Machine Learning są również tworzone zasoby dla innych usług platformy Azure. Są to:

* [Azure Container Registry](https://azure.microsoft.com/pricing/details/container-registry?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) Konto podstawowe
* [Azure Block Blob Storage](https://azure.microsoft.com/pricing/details/storage/blobs?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) (ogólnego przeznaczenia, wersja 1)
* [Usługa Key Vault](https://azure.microsoft.com/pricing/details/key-vault?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* [Application Insights](https://azure.microsoft.com/en-us/pricing/details/monitor?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
 
### <a name="costs-might-accrue-after-resource-deletion"></a>Koszty mogą być naliczane po usunięciu zasobów

Po usunięciu obszaru Azure Machine Learning w witrynie Azure Portal lub za pomocą interfejsu wiersza polecenia platformy Azure nadal istnieją następujące zasoby. Koszty będą naliczane do momentu ich usunięcia.

* Azure Container Registry
* Azure Block Blob Storage
* Key Vault
* Application Insights

Aby usunąć obszar roboczy wraz z tymi zasobami zależnym, użyj zestawu SDK:

```python
ws.delete(delete_dependent_resources=True)
```

W przypadku tworzenia Azure Kubernetes Service (AKS) w obszarze roboczym lub dołączania zasobów obliczeniowych do obszaru roboczego należy je usunąć oddzielnie w Azure Portal [.](https://portal.azure.com)

### <a name="using-azure-prepayment-credit-with-azure-machine-learning"></a>Używanie środków na przedpłatę za platformę Azure z Azure Machine Learning

Opłaty za subskrypcje można Azure Machine Learning pomocą środków na przedpłatę za platformę Azure (wcześniej nazywanych zobowiązaniem pieniężnym). Nie można jednak użyć przedpłaty za platformę Azure, aby płacić za opłaty za produkty i usługi innych firm, w tym za produkty i usługi Azure Marketplace.


## <a name="create-budgets"></a>Tworzenie budżetów

W celu zarządzania kosztami można utworzyć [budżety](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) oraz [alerty](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn), które automatycznie powiadamiają uczestników o anomaliach w wydatkach i o ryzyku nadmiernych wydatków. Alerty są oparte na wydatkach w porównaniu z budżetem i progami kosztów. Budżety i alerty są tworzone dla subskrypcji platformy Azure i grup zasobów, więc są one przydatne w ramach ogólnej strategii monitorowania kosztów. 

Budżety można tworzyć przy użyciu filtrów dla określonych zasobów lub usług na platformie Azure, jeśli chcesz mieć większy poziom szczegółowości w monitorowaniu. Filtry pomagają zagwarantować, że nie utworzysz przypadkowo nowych zasobów, które będą kosztować dodatkowe pieniądze. Aby uzyskać więcej informacji na temat opcji filtrowania podczas tworzenia budżetu, zobacz [Opcje grupowania i filtrowania](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="export-cost-data"></a>Eksportowanie danych kosztów

Możesz również [wyeksportować dane dotyczące kosztów](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) na konto magazynu. Jest to przydatne, gdy potrzebujesz lub innych osób, aby wykonać dodatkową analizę danych dla kosztów. Na przykład zespoły finansowe mogą analizować dane przy użyciu programu Excel lub Power BI. Możesz wyeksportować koszty zgodnie z harmonogramem dziennym, tygodniowym lub miesięcznym i ustawić niestandardowy zakres dat. Eksportowanie danych kosztów jest zalecanym sposobem pobierania zestawów danych kosztów.

## <a name="other-ways-to-manage-and-reduce-costs-for-azure-machine-learning"></a>Inne sposoby zarządzania kosztami dla Azure Machine Learning

Skorzystaj z tych wskazówek dotyczących zawierania kosztów zasobów obliczeniowych uczenia maszynowego.

### <a name="use-azure-machine-learning-compute-cluster-amlcompute"></a>Używanie Azure Machine Learning obliczeniowego (AmlCompute)

W przypadku stale zmieniających się danych potrzebujesz szybkiego i usprawnionego trenowania i ponownego trenowania modelu w celu utrzymania dokładnych modeli. Jednak ciągłe trenowania jest kosztowne, szczególnie w przypadku modeli uczenia głębokiego na procesorach GPU. 

Azure Machine Learning mogą używać zarządzanego klastra obliczeniowego Azure Machine Learning, nazywanego również AmlCompute. AmlCompute obsługuje różne opcje procesora GPU i PROCESORA. AmlCompute jest wewnętrznie hostowany w imieniu subskrypcji przez Azure Machine Learning. Zapewnia te same zabezpieczenia klasy korporacyjnej, zgodność i ład na skalę chmury IaaS platformy Azure.

Ponieważ te pule obliczeniowe znajdują się wewnątrz infrastruktury IaaS platformy Azure, możesz wdrażać i skalować szkolenia oraz zarządzać nimi z tymi samymi wymaganiami w zakresie zabezpieczeń i zgodności co pozostała część infrastruktury.  Te wdrożenia mają miejsce w ramach subskrypcji i są zgodnie z regułami ładu. Dowiedz się więcej o [Azure Machine Learning obliczeniowych.](how-to-create-attach-compute-cluster.md)

### <a name="configure-training-clusters-for-autoscaling"></a>Konfigurowanie klastrów trenowania na skalowanie automatyczne

Skalowanie automatyczne klastrów na podstawie wymagań obciążenia pomaga zmniejszyć koszty, dzięki czemu będziesz korzystać tylko z tego, czego potrzebujesz.

Klastry AmlCompute są zaprojektowane do dynamicznego skalowania na podstawie obciążenia. Klaster można skalować w górę do maksymalnej liczby skonfigurowanych węzłów. Po zakończeniu każdego uruchomienia klaster zwalnia węzły i skaluje je do skonfigurowanej minimalnej liczby węzłów.

[!INCLUDE [min-nodes-note](../../includes/machine-learning-min-nodes.md)]

Można również skonfigurować czas bezczynności węzła przed skalą w dół. Domyślnie czas bezczynności przed skalą w dół jest ustawiony na 120 sekund.

+ Jeśli wykonujesz mniej iteracyjnych eksperymentów, zmniejsz ten czas, aby zaoszczędzić koszty.
+ Jeśli przeprowadzasz wysoce iteracyjne eksperymenty deweloperacyjne/testowe, może być konieczne zwiększenie czasu, aby nie płacić za stałe skalowanie w górę i w dół po każdej zmianie skryptu trenowania lub środowiska.

Klastry AmlCompute można skonfigurować pod potrzeby zmieniających się wymagań dotyczących obciążenia w u Azure Portal przy użyciu klasy zestawu [SDK AmlCompute](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute), interfejsu wiersza polecenia [AmlCompute,](/cli/azure/ml/computetarget/create#az_ml_computetarget_create_amlcompute)z interfejsami [API REST.](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/machinelearningservices/resource-manager/Microsoft.MachineLearningServices/stable)

```azurecli
az ml computetarget create amlcompute --name testcluster --vm-size Standard_NC6 --min-nodes 0 --max-nodes 5 --idle-seconds-before-scaledown 300
```

### <a name="set-quotas-on-resources"></a>Ustawianie limitów przydziału zasobów

W przypadku usługi AmlCompute konfiguracja [przydziału (lub limitu) to](how-to-manage-quotas.md#azure-machine-learning-compute). Ten limit przydziału zależy od rodziny maszyn wirtualnych (na przykład serii Dv2, serii NCv3) i zależy od regionu dla każdej subskrypcji. Subskrypcje zaczynają się od małych wartości domyślnych, aby rozpocząć korzystanie z subskrypcji, ale użyj tego ustawienia do kontrolowania ilości zasobów usługi Amlcompute dostępnych do uruchomienia w ramach subskrypcji. 

Skonfiguruj również [limit przydziału na poziomie obszaru roboczego według rodziny maszyn wirtualnych](how-to-manage-quotas.md#workspace-level-quotas)dla każdego obszaru roboczego w ramach subskrypcji. Dzięki temu można mieć bardziej szczegółową kontrolę nad kosztami, które mogą potencjalnie ponieść poszczególne obszary robocze, i ograniczyć niektóre rodziny maszyn wirtualnych. 

Aby ustawić limity przydziału na poziomie obszaru roboczego, zacznij od [Azure Portal](https://portal.azure.com).  Wybierz dowolny obszar roboczy w subskrypcji, a następnie wybierz **pozycję Użycie i przydziały** w okienku po lewej stronie. Następnie wybierz **kartę Konfigurowanie przydziałów,** aby wyświetlić limity przydziału. Aby ustawić limit przydziału, potrzebne są uprawnienia w zakresie subskrypcji, ponieważ jest to ustawienie, które ma wpływ na wiele obszarów roboczych.

### <a name="set-run-autotermination-policies"></a>Ustawianie zasad autoterminacji uruchamiania 

W niektórych przypadkach należy skonfigurować przebiegi trenowania, aby ograniczyć ich czas trwania lub zakończyć je wcześnie. Na przykład w przypadku korzystania z Azure Machine Learning dostrajania hiperparametrów lub zautomatyzowanego uczenia maszynowego.

Oto kilka dostępnych opcji:
* Zdefiniuj parametr o nazwie w parametrze RunConfiguration, aby kontrolować maksymalny czas trwania uruchomienia, który można rozszerzyć do na podstawie wybieranych zasobów obliczeniowych (obliczenia w chmurze lokalnej `max_run_duration_seconds` lub zdalnej).
* W [przypadku dostrajania hiperparametrów](how-to-tune-hyperparameters.md#early-termination)należy zdefiniować zasady wczesnego zakończenia z zasad bandyt, zasad zatrzymywania mediany lub zasad wyboru przycinania. Aby jeszcze bardziej kontrolować czyszczenie hiperparametrów, użyj parametrów takich `max_total_runs` jak lub `max_duration_minutes` .
* W [przypadku zautomatyzowanego uczenia maszynowego](how-to-configure-auto-train.md#exit)ustaw podobne zasady zakończenia przy użyciu  `enable_early_stopping` flagi . Użyj również właściwości, takich jak i , aby kontrolować `iteration_timeout_minutes` maksymalny czas trwania uruchomienia lub dla całego `experiment_timeout_minutes` eksperymentu.

### <a name="use-low-priority-vms"></a><a id="low-pri-vm"></a> Używanie maszyn wirtualnych o niskim priorytecie

Platforma Azure umożliwia używanie nadmiarowej nie w pełni wykorzystać pojemności jako Low-Priority wirtualnych w zestawach skalowania maszyn wirtualnych, usłudze Batch i Machine Learning service. Te alokacje są wstępnie dostępne, ale ich cena jest obniżona w porównaniu do dedykowanych maszyn wirtualnych. Ogólnie rzecz biorąc, zalecamy używanie Low-Priority wirtualnych dla obciążeń usługi Batch. Należy ich również używać, gdy przerwy można odzyskać za pośrednictwem ponownego prześlij (w przypadku wnioskowania wsadowego) lub za pośrednictwem ponownego uruchomienia (do trenowania uczenia głębokiego z punktami kontrolnymi).

Low-Priority maszyny wirtualne mają jeden limit przydziału oddzielony od wartości dedykowanego limitu przydziału, który jest dla rodziny maszyn wirtualnych. Dowiedz [się więcej o limitach przydziałów AmlCompute.](how-to-manage-quotas.md)

 Low-Priority wirtualne nie działają w przypadku wystąpień obliczeniowych, ponieważ muszą obsługiwać interaktywne środowisko notesów.

### <a name="use-reserved-instances"></a>Użycie wystąpień zarezerwowanych

Innym sposobem oszczędzania pieniędzy na zasobach obliczeniowych jest wystąpienie zarezerwowane maszyny wirtualnej platformy Azure. W przypadku tej oferty zobowiązujesz się do korzystania z warunków jednorocznych lub trzech lat. Te rabaty obejmują do 72% cen z płatnością zgodnie z potrzebami i są stosowane bezpośrednio do miesięcznego rachunku za korzystanie z platformy Azure.

Azure Machine Learning Compute z założenia obsługuje wystąpienia zarezerwowane. Jeśli zakupisz jednoroczne lub trzyroczne wystąpienie zarezerwowane, automatycznie zastosujemy rabat do Twojego wystąpienia zarządzanego Azure Machine Learning obliczeniowego.


## <a name="next-steps"></a>Następne kroki

- Dowiedz [się, jak zoptymalizować inwestycję w chmurę za pomocą Azure Cost Management.](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
- Dowiedz się więcej o zarządzaniu kosztami za pomocą [analizy kosztów.](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
- Dowiedz się, jak [zapobiegać nieoczekiwanym kosztom.](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
- Weź udział [w Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) z przewodnikiem.
