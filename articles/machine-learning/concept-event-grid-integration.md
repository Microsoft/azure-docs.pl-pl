---
title: Korzystanie z zdarzeń Azure Machine Learning
titleSuffix: Azure Machine Learning
description: W tym artykule dowiesz się, jak za pomocą usługi Azure Event Grid subskrybować, reagować i anulować subskrypcję zdarzeń generowanych przez Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: andyxu
author: gogowings
ms.date: 11/04/2019
ms.openlocfilehash: d8a975487c68a21b2c8b6fa2f07d86c312243f12
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79139049"
---
# <a name="consume-azure-machine-learning-events-preview"></a>Korzystanie z zdarzeń Azure Machine Learning (wersja zapoznawcza)

Azure Machine Learning zarządza całym cyklem życia procesu uczenia maszynowego, w tym szkolenia modelu, wdrażanie modeli i monitorowanie. Zdarzenia Azure Machine Learning umożliwiają aplikacjom reagowanie na zdarzenia w trakcie cyklu życia usługi Machine Learning, takie jak ukończenie przebiegów szkoleniowych, rejestrowanie i wdrażanie modeli oraz wykrywanie dryfowania danych przy użyciu nowoczesnych architektur bezserwerowych. 

Te zdarzenia są publikowane za poorednictwem [Azure Event Grid](https://azure.microsoft.com/services/event-grid/). Korzystając z Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure, klienci mogą łatwo subskrybować zdarzenia, [określając jeden lub więcej typów zdarzeń i warunki filtrowania](/azure/event-grid/event-filtering). Klienci mają również możliwość tworzenia szerokiego zakresu obsługi zdarzeń, takich jak Azure Functions, Azure Logic Apps lub ogólne elementy webhook. Azure Machine Learning, wraz z Azure Event Grid, zapewnia wysoką dostępność, niezawodną i odporną na błędy platformę dostarczania zdarzeń, która umożliwia tworzenie aplikacji opartych na zdarzeniach.

Aby uzyskać informacje na temat używania Azure Machine Learning z Event Grid, zobacz [Tworzenie przepływów pracy Machine Learning opartych na zdarzeniach (wersja zapoznawcza)](how-to-use-event-grid.md).

## <a name="the-event-model"></a>Model zdarzenia 

Azure Event Grid odczytuje zdarzenia ze źródeł, takich jak Azure Machine Learning i inne usługi platformy Azure. Te zdarzenia są następnie wysyłane do programów obsługi zdarzeń, takich jak Azure Event Hubs, Azure Functions, Logic Apps i innych. Na poniższym diagramie przedstawiono sposób, w jaki Event Grid nawiązuje połączenie ze źródłami i procedurami obsługi, ale nie jest kompleksową listą obsługiwanych integracji.

![Azure Event Grid model funkcjonalny](./media/concept-event-grid-integration/azure-event-grid-functional-model.png)

Aby uzyskać więcej informacji o źródłach zdarzeń i obsłudze zdarzeń, zobacz [co to jest Event Grid?](/azure/event-grid/overview).

## <a name="azure-machine-learning-event-types"></a>Azure Machine Learning typy zdarzeń

Azure Machine Learning udostępnia zdarzenia w różnych punktach cyklu życia uczenia maszynowego: 

| Typ zdarzenia | Opis |
| ---------- | ----------- |
| `Microsoft.MachineLearningServices.RunCompleted` | Uruchamiany po zakończeniu eksperymentu uczenia maszynowego |
| `Microsoft.MachineLearningServices.ModelRegistered` | Uruchamiany, gdy model uczenia maszynowego jest zarejestrowany w obszarze roboczym |
| `Microsoft.MachineLearningServices.ModelDeployed` | Uruchamiany, gdy zostanie wykonane wdrożenie usługi wnioskowania z co najmniej jednym modelem |
| `Microsoft.MachineLearningServices.DatasetDriftDetected` | Uruchamiany po zakończeniu zadania wykrywania dryfu danych dla dwóch zestawów danych |
| `Microsoft.MachineLearningServices.RunStatusChanged` | Uruchamiany, gdy stan uruchomienia zostanie zmieniony, obecnie jest uruchamiany tylko wtedy, gdy stan uruchomienia to "Niepowodzenie" |

## <a name="subscribe-to-machine-learning-events"></a>Subskrybowanie zdarzeń Machine Learning

Subskrypcje dla zdarzeń Azure Machine Learning są chronione za pomocą kontroli dostępu opartej na rolach (RBAC). Tylko [współautor lub właściciel](how-to-assign-roles.md#default-roles) obszaru roboczego może tworzyć, aktualizować i usuwać subskrypcje zdarzeń.

Subskrypcje zdarzeń można filtrować na podstawie różnych warunków. Filtry mogą być stosowane do subskrypcji zdarzeń podczas [tworzenia](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest) subskrypcji zdarzeń lub [w późniejszym czasie](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest). 

### <a name="filter-by-event-type"></a>Filtruj według typu zdarzenia
W subskrypcji zdarzeń można określić jeden lub więcej typów zdarzeń Azure Machine Learning.

### <a name="filter-by-event-subject"></a>Filtruj według tematu zdarzenia
Azure Event Grid obsługuje filtry podmiotu w oparciu o __początek__ i __kończące__ się na dopasowaniach, dzięki czemu zdarzenia z podmiotem pasującym są dostarczane do subskrybenta. Różne zdarzenia uczenia maszynowego mają różne formaty podmiotu.

| Typ zdarzenia | Format tematu | Przykładowy temat |
| ---------- | ----------- | ----------- |
| `Microsoft.MachineLearningServices.RunCompleted` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` |
| `Microsoft.MachineLearningServices.ModelRegistered` | `models/{modelName}:{modelVersion}` | `models/sklearn_regression_model:3` |
| `Microsoft.MachineLearningServices.ModelDeployed` | `endpoints/{serviceId}` | `endpoints/my_sklearn_aks` |
| `Microsoft.MachineLearningServices.DatasetDriftDetected` | `datadrift/{data.DataDriftId}/run/{data.RunId}` | `datadrift/4e694bf5-712e-4e40-b06a-d2a2755212d4/run/my_driftrun1_1550564444_fbbcdc0f` |
| `Microsoft.MachineLearningServices.RunStatusChanged` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` | 

### <a name="advanced-filtering"></a>Filtrowanie zaawansowane

Azure Event Grid obsługuje także zaawansowane filtrowanie na podstawie opublikowanego schematu zdarzeń. Szczegóły schematu zdarzenia Azure Machine Learning można znaleźć w [Azure Event Grid schemacie zdarzeń dla Azure Machine Learning](../event-grid/event-schema-machine-learning.md).

Niektóre przykładowe zaawansowane filtrowanie, które można wykonać, to m.in.:

* Dla `Microsoft.MachineLearningServices.ModelRegistered` zdarzenia, aby filtrować wartość tagu modelu:

    ```
    --advanced-filter data.ModelTags.key1 StringIn ('value1')
    ```

Aby dowiedzieć się więcej na temat stosowania filtrów, zobacz [filtrowanie zdarzeń dla Event Grid](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

## <a name="consume-machine-learning-events"></a>Korzystanie z zdarzeń Machine Learning

Aplikacje, które obsługują zdarzenia Machine Learning, powinny spełniać kilka zalecanych praktyk:

> [!div class="checklist"]
> * Tak jak wiele subskrypcji można skonfigurować do kierowania zdarzeń do tego samego programu obsługi zdarzeń, dlatego ważne jest, aby nie założyć, że zdarzenia pochodzą z określonego źródła, ale aby sprawdzić temat wiadomości w celu upewnienia się, że pochodzi ona z obszaru roboczego uczenia maszynowego.
> * Podobnie Sprawdź, czy typ zdarzenia jest przygotowana do przetworzenia i nie zakładaj, że wszystkie zdarzenia, które otrzymujesz, są oczekiwanymi typami.
> * Ponieważ komunikaty mogą się pojawiać poza kolejnością i po pewnym opóźnieniu, należy użyć pól ETag, aby zrozumieć, czy informacje o obiektach są nadal aktualne.  Ponadto należy użyć pól programu Sequencer do zrozumienia kolejności zdarzeń dla każdego określonego obiektu.
> * Ignoruj pola, które nie są zrozumiałe. Ta metoda pomaga w zachowaniu odporności na nowe funkcje, które mogą zostać dodane w przyszłości.
> * Niepowodzenie lub anulowanie operacji Azure Machine Learning nie spowoduje wyzwolenia zdarzenia. Na przykład jeśli wdrożenie modelu nie powiedzie się Microsoft. MachineLearningServices. ModelDeployed nie zostanie wyzwolone. Ten tryb awarii należy wziąć pod uwagę podczas projektowania aplikacji. Aby sprawdzić stan operacji i zrozumieć szczegółowe przyczyny niepowodzenia, zawsze możesz użyć zestawu SDK, interfejsu wiersza polecenia i portalu Azure Machine Learning.

Azure Event Grid umożliwia klientom tworzenie niepołączonych programów obsługi komunikatów, które mogą być wyzwalane przez zdarzenia Azure Machine Learning. Oto przykładowe przykłady obsługi komunikatów:
* Azure Functions
* Azure Logic Apps
* Azure Event Hubs
* Potok Azure Data Factory
* Ogólne elementy webhook, które mogą być hostowane na platformie Azure lub w innym miejscu

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o Event Grid i nadaj Azure Machine Learning zdarzeniom:

- [Event Grid — informacje](../event-grid/overview.md)
- [Schemat zdarzeń Azure Event Grid dla Azure Machine Learning](../event-grid/event-schema-machine-learning.md)
- [Tworzenie przepływów pracy opartych na zdarzeniach za pomocą Azure Machine Learning](how-to-use-event-grid.md)
