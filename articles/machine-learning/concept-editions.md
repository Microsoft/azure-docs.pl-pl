---
title: Azure Machine Learning wersje Enterprise i Basic
description: Dowiedz się więcej o różnicach między wersjami Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: j-martens
ms.author: jmartens
ms.date: 06/11/2020
ms.openlocfilehash: 173ff8b638c40773ca2c4bdac5021f2ea4b84549
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85555541"
---
# <a name="enterprise-and-basic-editions-of-azure-machine-learning"></a>Wersje Enterprise i Basic Azure Machine Learning 

Azure Machine Learning oferuje dwie wersje dostosowane do potrzeb uczenia maszynowego. Te wersje określają, które narzędzia uczenia maszynowego są dostępne dla deweloperów i naukowców danych z ich obszaru roboczego.

## <a name="choose-an-edition"></a>Wybieranie wersji

Wydanie jest przypisywane po każdym utworzeniu obszaru roboczego. W tym czasie klienci są zobowiązani do ponoszenia kosztów związanych z obliczeniami i innymi zasobami platformy Azure. Dowiedz się, jak [zarządzać kosztami Azure Machine Learning](concept-plan-manage-cost.md).

Dowiedz się, jak [uaktualnić podstawowy obszar roboczy do wersji Enterprise](how-to-manage-workspace.md#upgrade). 

## <a name="whats-in-each-edition"></a>Co to jest w poszczególnych wersjach

### <a name="data-for-machine-learning-capabilities"></a>Dane dotyczące możliwości Machine Learning  

| Możliwości                     | Wersja                 |
|------------------------------------------------------------------------------------|:-----------:|
| Etykietowanie: [Tworzenie i zarządzanie projektami etykietowania](tutorial-labeling.md) w programie Studio (sieć Web)                                                | Wszystko                     |
| Etykietowanie: Labeler w Studio (sieć Web)                                    | Wszystko                     |
| Etykietowanie: korzystanie z pracowników prywatnych                               | Wszystko                     |
| Etykietowanie: [Klasyfikacja obrazu wspieranego przez ml i wykrywanie obiektów](how-to-label-images.md)                  | Tylko wersja Enterprise |
| Zestawy danych + datastores: Tworzenie i zarządzanie w języku Python                       | Wszystko                     |
| Zestawy danych + datastores: Tworzenie i zarządzanie w programie Studio (sieć Web)                         | Wszystko                     |
| Dryf: wyświetlanie monitorów zestawu danych i zarządzanie nimi w języku Python                           | Wszystko                     |
| Dryf: wyświetlanie monitorów zestawu danych i zarządzanie nimi w programie Studio (sieć Web)                            | Tylko wersja Enterprise |


<br/>
<br/>

### <a name="automated-training-capabilities-automl"></a>Możliwości szkolenia automatycznego (AutoML)

| Możliwości    | Wersja                 |
|------------------------------------------------------------------------------------|:-----------:|
| Twórz i uruchamiaj [eksperymenty AutoML w notesach](how-to-configure-auto-train.md)               | Wszystko                     |
| Twórz i uruchamiaj [eksperymenty AutoML w programie Studio (sieć Web)](how-to-use-automated-ml-for-ml-models.md)   | Tylko wersja Enterprise |
| Wiodące w branży funkcje prognozowania AutoML             | Tylko wersja Enterprise |
| Pomoc techniczna w zakresie uczenia głębokiego i innych zaawansowanych informacji | Tylko wersja Enterprise |
| Duże ilości danych obsługują zadania klasyfikacji i regresji (do 100 GB)                     | Tylko wersja Enterprise |


<br/>
<br/>

### <a name="responsible-machine-learning"></a>Osoby odpowiedzialne Machine Learning

| Możliwości    | Wersja                 |
|------------------------------------------------------------------------------------|:-----------:|
| [Wyjaśnienie modelu](how-to-machine-learning-interpretability-automl.md)                                              | Wszystko                     |
| [Różnicowa Ochrona prywatności](how-to-differential-privacy.md)                          | Wszystko                     |
| Niestandardowe znaczniki do implementowania arkuszy danych    | Wszystko                     |
| Integracja z programem Azure                                      | Wszystko                     |

<br/>
<br/>


### <a name="build-and-train-capabilities"></a>Możliwości kompilacji i uczenia

| Możliwości    | Wersja                 |
|------------------------------------------------------------------------------------|:-----------:|
| Integracja Visual Studio Code                                                     | Wszystko                     |
| Uczenie wzmacniające                                                             | Wszystko                     |
| Interfejs użytkownika eksperymentowania                                                                 | Wszystko                     |
| Jupyter, integracja z JupyterLab                                                    | Wszystko                     |
| Obsługa zestawu SDK języka Python                                                                 | Wszystko                     |
| Obsługa zestawu SDK języka R                                                                      | Wszystko                     |
| Potoki ML: Tworzenie, uruchamianie i publikowanie w języku Python                           | Wszystko                     |
| Potoki ML: Tworzenie, edytowanie i usuwanie zaplanowanych uruchomień potoków w języku Python| Wszystko                     |
| Potoki ML: Tworzenie punktów końcowych potoku w zestawie SDK języka Python                                   | Wszystko                     |
| Potoki ML: Wyświetl szczegóły przebiegu w programie Studio (sieć Web)                                              | Wszystko                     |
| Potoki ML: Tworzenie, uruchamianie, wizualizowanie i publikowanie w projektancie                  | Tylko wersja Enterprise |
| Potoki ML: Tworzenie punktów końcowych potoku w projektancie | Tylko wersja Enterprise |
| Zarządzane wystąpienia obliczeniowe dla zintegrowanych notesów                                 | Wszystko                     |


<br/>
<br/>

### <a name="deployment-and-model-management-capabilities"></a>Możliwości wdrażania i zarządzania modelami

| Możliwości                            | Wersja                 |
|------------------------------------------------------------------------------------|:-----------:|
| Rozszerzenie DevOps platformy Azure dla Machine Learning i Azure ML CLI                 | Wszystko                     |
| [Integracja z usługą Event Grid](how-to-use-event-grid.md)                                                             | Wszystko                     |
| Integracja Azure Stream Analytics z usługą Azure Machine Learning                       | Wszystko                     |
| Tworzenie potoków ML w zestawie SDK                                                         | Wszystko                     |
| Inferencing Batch                                                                  | Wszystko                     |
| Modele z przyspieszaniem sprzętowym oparte na FPGA                                             | Wszystko                     |
| Profilowanie modelu                                                                    | Wszystko                     |

<br/>
<br/>

### <a name="security-governance-and-control-capabilities"></a>Możliwości zabezpieczeń, zarządzania i kontroli

| Możliwości     | Wersja                 |
|------------------------------------------------------------------------------------|:-----------:|
| Obsługa [Access Control opartej na rolach](how-to-assign-roles.md) (RBAC)                                           | Wszystko                     |
| Obsługa [Virtual Network (VNET)](how-to-enable-virtual-network.md) dla obliczeń                                         | Wszystko                     |
| Ocenianie uwierzytelniania punktu końcowego                                                    | Wszystko                     |
| [Prywatny link obszaru roboczego](how-to-configure-private-link.md)                                                            | Wszystko                     |
| [Zarządzanie przydziałami](how-to-manage-quotas.md) w różnych obszarach roboczych                                                 | Tylko wersja Enterprise |

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o tym, co jest dostępne na [stronie Omówienie wersji Azure Machine Learning i cennika](https://azure.microsoft.com/pricing/details/machine-learning/). 

Dowiedz się, jak [uaktualnić podstawowy obszar roboczy do wersji Enterprise](how-to-manage-workspace.md#upgrade). 
