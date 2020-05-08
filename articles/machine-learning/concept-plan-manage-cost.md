---
title: Planowanie kosztów i zarządzanie nimi
titleSuffix: Azure Machine Learning
description: Planowanie i zarządzanie kosztami Azure Machine Learning przy użyciu analizy kosztów w Azure Portal. Podczas kompilowania modeli uczenia maszynowego warto dowiedzieć się więcej o dalszych wskazówkach dotyczących oszczędności, aby obniżyć koszty.
author: sdgilley
ms.author: sgilley
ms.custom: subject-cost-optimization
ms.reviewer: nigup
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 04/22/2020
ms.openlocfilehash: b777e6a45bdfe78889366982a6c28e10e21c4fbc
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2020
ms.locfileid: "82856999"
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

Skorzystaj z [kalkulatora cen platformy Azure](https://azure.microsoft.com/pricing/calculator/) , aby oszacować koszty przed utworzeniem zasobów na koncie Azure Machine Learning. Po lewej stronie wybierz pozycję **AI + Machine Learning**, a następnie wybierz pozycję **Azure Machine Learning** , aby rozpocząć.  

Poniższy zrzut ekranu przedstawia oszacowanie kosztów za pomocą kalkulatora:

:::image type="content" source="media/concept-plan-manage-cost/capacity-calculator-cost-estimate.png" alt-text="Oszacowanie kosztów w kalkulatorze platformy Azure":::

Dodając nowe zasoby do obszaru roboczego, Wróć do tego kalkulatora i Dodaj tutaj ten sam zasób, aby zaktualizować oszacowania kosztów.

Wersja Enterprise Edition jest dostępna w wersji zapoznawczej, ale nie ma dopłaty do l. Gdy wersja Enterprise jest ogólnie dostępna, zostanie wykorzystana opłata za uczenie maszynowe (na potrzeby szkoleń i inferencing).  Aby uzyskać więcej informacji, [Azure Machine Learning Cennik](https://azure.microsoft.com/pricing/details/machine-learning/).

## <a name="get-cost-alerts"></a>Pobierz alerty kosztów

Twórz [budżety](../cost-management/tutorial-acm-create-budgets.md) , aby zarządzać kosztami i tworzyć [alerty](../cost-management/cost-mgt-alerts-monitor-usage-spending.md) , które automatycznie powiadamiają uczestników wydatków o wykorzystaniu anomalii i poświęcają ryzyko. Alerty są oparte na wydatkach w porównaniu z budżetem i progami kosztów. Budżety i alerty są tworzone dla subskrypcji platformy Azure i grup zasobów, dzięki czemu są one przydatne jako część ogólnej strategii monitorowania kosztów. Jednak budżety i alerty mogą mieć ograniczoną funkcjonalność do zarządzania poszczególnymi kosztami usług platformy Azure, ponieważ są one przeznaczone do śledzenia kosztów na wyższym poziomie.

## <a name="monitor-costs"></a>Monitorowanie kosztów

Gdy korzystasz z zasobów z Azure Machine Learning, naliczane są koszty. Koszty jednostki użycia zasobów platformy Azure różnią się w zależności od interwału czasu (sekundy, minuty, godziny i dni) lub według użycia jednostki żądania. Po rozpoczęciu korzystania z Azure Machine Learning są naliczane koszty. Zapoznaj się z tymi kosztami w okienku [Analiza kosztów](../cost-management/quick-acm-cost-analysis.md) w Azure Portal.

Wyświetlanie kosztów w wykresach i tabelach dla różnych przedziałów czasu. Kilka przykładów jest dziennie, bieżących, poprzedniego miesiąca i roku. Wyświetlaj także koszty związane z budżetami i prognozowanymi kosztami. Przełączenie na dłuższe widoki w czasie pomaga identyfikować trendy wydatków i zobaczyć, gdzie mogły wystąpić nadwyżki. Jeśli utworzono budżety, zobacz miejsce ich przekroczenia.  

Nie zobaczysz oddzielnego obszaru usługi dla Machine Learning.  Zamiast tego zobaczysz różne zasoby, które zostały dodane do obszarów roboczych Machine Learning.

## <a name="use-amlcompute"></a>Użyj AmlCompute

Dzięki ciągłej zmianie danych potrzebne są szybkie i usprawnione szkolenia modeli oraz przeszkolenie w celu utrzymania dokładnych modeli. Jednak ciągłe szkolenie jest kosztem, szczególnie w przypadku modeli uczenia głębokiego w procesorach GPU. 

Azure Machine Learning użytkownicy mogą korzystać z zarządzanego Azure Machine Learning klastra obliczeniowego, nazywanego również AmlCompute. AmlCompute obsługuje wiele opcji procesora GPU i procesorów. AmlCompute jest wewnętrznie hostowana w imieniu Twojej subskrypcji przez Azure Machine Learning, ale zapewnia te same zabezpieczenia, zgodność i zarządzanie na poziomie przedsiębiorstwa na platformie Azure IaaS w skali chmury.

Ponieważ te pule obliczeniowe należą do infrastruktury IaaS platformy Azure, można wdrożyć, skalować i zarządzać szkoleniami z tymi samymi wymaganiami dotyczącymi zabezpieczeń i zgodności, co w przypadku reszty infrastruktury.  Te wdrożenia są wykonywane w ramach subskrypcji i przestrzegają reguł zarządzania. Dowiedz się więcej na temat [Azure Machine Learning obliczeń](how-to-set-up-training-targets.md#amlcompute).

## <a name="configure-training-clusters-for-autoscaling"></a>Konfigurowanie klastrów szkoleniowych na potrzeby automatycznego skalowania

Automatyczne skalowanie klastrów na podstawie wymagań obciążeń pozwala zmniejszyć koszty, dzięki czemu możesz korzystać tylko z tego, czego potrzebujesz. Klastry AmlCompute są zaprojektowane do automatycznego skalowania na podstawie wymagań obciążeń. Klaster może być skalowany w górę do maksymalnej liczby węzłów zainicjowanych i w ramach limitu przydziału wyznaczonych dla subskrypcji. Po zakończeniu każdego uruchomienia klaster będzie zwalniał węzły i skalowanie automatyczne do wyoznaczonej minimalnej liczby węzłów.

Oprócz ustawienia minimalnej i maksymalnej liczby węzłów, można dostosować ilość czasu bezczynności węzła przed scaleniem w dół. Domyślnie czas bezczynności przed skalą w dół jest ustawiony na 120 sekund.

+ W przypadku przeprowadzania mniej iteracyjnych eksperymentów należy skrócić ten czas, aby zaoszczędzić koszty. 
+ W przypadku przeprowadzania wysoko iteracyjnych eksperymentów związanych z tworzeniem/testowaniem można zwiększyć, aby nie płacić za stałe skalowanie w górę i w dół po każdej zmianie w skrypcie lub środowisku szkoleniowym.

Klastry AmlCompute można skonfigurować w celu zmiany wymagań obciążeń w Azure Portal, przy użyciu [klasy zestawu SDK AmlCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py), [interfejsu wiersza polecenia AmlCompute](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute)z [interfejsami API REST](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/machinelearningservices/resource-manager/Microsoft.MachineLearningServices/stable).

```azure cli
az ml computetarget create amlcompute --name testcluster --vm-size Standard_NC6 --min-nodes 0 --max-nodes 5 --idle-seconds-before-scaledown 300
```

## <a name="set-quotas-on-resources"></a>Ustawianie przydziałów zasobów

Podobnie jak w przypadku innych zasobów obliczeniowych platformy Azure, AmlCompute obejmuje [konfigurację przydziałów (lub ograniczeń)](how-to-manage-quotas.md#azure-machine-learning-compute). Ten przydział dotyczy rodziny maszyn wirtualnych (na przykład serii Dv2, Seria NCV3 Series) i różni się w zależności od regionu dla każdej subskrypcji. Subskrypcje zaczynają się od małych wartości domyślnych, aby można było je uzyskać, ale przy użyciu tego ustawienia można kontrolować ilość zasobów Amlcompute dostępnych do odtworzenia w ramach subskrypcji. 

Skonfiguruj również [przydział poziomu obszaru roboczego według rodziny maszyn wirtualnych](how-to-manage-quotas.md#workspace-level-quota)dla każdego obszaru roboczego w ramach subskrypcji. Dzięki temu można mieć dokładniejszą kontrolę nad kosztami, które mogą zostać poniesione przez każdy obszar roboczy i ograniczyć liczbę rodzin maszyn wirtualnych. 

Aby ustawić przydziały na poziomie obszaru roboczego, Uruchom w [Azure Portal](https://portal.azure.com).  Wybierz dowolny obszar roboczy w ramach subskrypcji, a następnie wybierz pozycję **użycie + przydziały** w okienku po lewej stronie. Następnie wybierz kartę **Konfigurowanie przydziałów** , aby wyświetlić przydziały. Aby ustawić ten przydział, potrzebne są uprawnienia, ponieważ jest to ustawienie wpływające na wiele obszarów roboczych.

## <a name="set-run-auto-termination-policies"></a>Ustaw zasady uruchamiania autokończenia 

Skonfiguruj przebiegi szkoleniowe, aby ograniczyć ich czas trwania lub przerwać ich wczesne działania w przypadku niektórych warunków, szczególnie w przypadku korzystania z wbudowanych funkcji strojenia lub Machine Learning automatycznych w Azure Machine Learning. 

Oto kilka opcji, których dysponujesz:
* Zdefiniuj parametr wywoływany `max_run_duration_seconds` w RunConfiguration, aby kontrolować maksymalny czas trwania uruchomienia w ramach wybranego obliczenia (lokalnie lub w chmurze).
* W przypadku [dostrajania parametrów](how-to-tune-hyperparameters.md#early-termination)należy zdefiniować zasady wczesnego zakończenia na podstawie zasad Banditymi, średniego zatrzymywania zasad lub zasad wyboru obcięcia. Ponadto należy również użyć parametrów, takich jak `max_total_runs` lub `max_duration_minutes` , aby dodatkowo kontrolować różne odchylenia parametrów.
* Aby [zautomatyzować automatyczne Uczenie maszynowe](how-to-configure-auto-train.md#exit), ustaw podobne zasady `enable_early_stopping` kończenia przy użyciu flagi. Należy również użyć właściwości, `iteration_timeout_minutes` takich `experiment_timeout_minutes` jak i, aby kontrolować maksymalny czas trwania przebiegu lub dla całego eksperymentu.

## <a name="use-low-priority-vms"></a>Używanie maszyn wirtualnych o niskim priorytecie

System Azure umożliwia korzystanie z nadmiarowej niewykorzystanej pojemności jako maszyn wirtualnych o niskim priorytecie w ramach zestawów skalowania maszyn wirtualnych, partii i usługi Machine Learning. Te przydziały są wstępnie emptible, ale mają obniżoną cenę w porównaniu do dedykowanych maszyn wirtualnych. Ogólnie rzecz biorąc, zalecamy korzystanie z maszyn wirtualnych o niskim priorytecie dla obciążeń wsadowych lub w przypadku, gdy przerwy są odzyskiwane przez ponowne przesłanie (dla usługi Batch Inferencing) lub za pośrednictwem ponownych uruchomień (dla szkolenia głębokiego uczenia z użyciem punktów kontrolnych).

Maszyny wirtualne o niskim priorytecie mają jeden przydział oddzielny od dedykowanej wartości przydziału, która jest przez rodzinę maszyn wirtualnych. Dowiedz się [więcej na temat przydziałów AmlCompute](how-to-manage-quotas.md).

Ustaw priorytet maszyny wirtualnej w dowolny z następujących sposobów:

* W programie Studio wybierz pozycję **niski priorytet** podczas tworzenia maszyny wirtualnej.

* Za pomocą zestawu SDK języka Python Ustaw `vm_priority` atrybut w konfiguracji aprowizacji.  

    ```python
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                               vm_priority='lowpriority',
                                                               max_nodes=4)
    ```

* Korzystając z interfejsu wiersza polecenia, `vm-priority`należy ustawić:

    ```azurecli-interactive
    az ml computetarget create amlcompute --name lowpriocluster --vm-size Standard_NC6 --max-nodes 5 --vm-priority lowpriority
    ```

 Maszyny wirtualne o niskim priorytecie nie działają w przypadku wystąpień obliczeniowych, ponieważ wymagają one obsługi interaktywnych środowisk notesu. 

## <a name="use-reserved-instances"></a>Użycie wystąpień zarezerwowanych

Wystąpienie zarezerwowane maszyny wirtualnej platformy Azure umożliwia innym sposobem uzyskania ogromnych oszczędności zasobów obliczeniowych przez zatwierdzenie do jednego roku lub trzech lat. Rabaty te obejmują do 72% cen płatności zgodnie z rzeczywistym użyciem i są stosowane bezpośrednio na miesięcznym rachunku za platformę Azure.

Azure Machine Learning COMPUTE obsługuje zarezerwowane wystąpienia. Jeśli więc zakupiono jednoletnie lub trzy lata zarezerwowane wystąpienie, automatycznie zastosuje rabat wystąpienia zarezerwowanego względem zarządzanego obliczenia, który jest używany w Azure Machine Learning bez konieczności przeprowadzania jakichkolwiek dodatkowych ustawień.


## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o zarządzaniu kosztami przy użyciu [analizy kosztów](../cost-management-billing/costs/quick-acm-cost-analysis.md).
* Dowiedz się więcej na temat [Azure Machine Learning obliczeń](how-to-set-up-training-targets.md#amlcompute).