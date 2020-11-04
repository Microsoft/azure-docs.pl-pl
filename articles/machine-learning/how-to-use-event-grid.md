---
title: Zdarzenia wyzwalania w przepływach pracy w ML (wersja zapoznawcza)
titleSuffix: Azure Machine Learning
description: Konfigurowanie aplikacji opartych na zdarzeniach, procesów lub przepływów pracy uczenia maszynowego/ciągłego w Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: shipatel
author: shivp950
ms.reviewer: larryfr
ms.date: 05/11/2020
ms.openlocfilehash: 1fd177273c9dafb04add64d8a8bfef1d81cc65d0
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93319314"
---
# <a name="trigger-applications-processes-or-cicd-workflows-based-on-azure-machine-learning-events-preview"></a>Wyzwalaj aplikacje, procesy lub przepływy pracy ciągłej integracji/ciągłego wdrażania w oparciu o zdarzenia Azure Machine Learning (wersja zapoznawcza)

W tym artykule dowiesz się, jak skonfigurować aplikacje, procesy lub przepływy pracy ciągłej integracji/ciągłego wdrażania, na podstawie zdarzeń Azure Machine Learning, takich jak wiadomości e-mail z powiadomieniami o błędach lub uruchomienia potoku ML, w przypadku wykrycia określonych warunków przez [Azure Event Grid](../event-grid/index.yml).

Azure Machine Learning zarządza całym cyklem życia procesu uczenia maszynowego, w tym szkolenia modelu, wdrażanie modeli i monitorowanie. Za pomocą Event Grid można reagować na zdarzenia Azure Machine Learning, takie jak ukończenie przebiegów szkoleniowych, rejestrowanie i wdrażanie modeli oraz wykrywanie dryfowania danych przy użyciu nowoczesnych architektur bezserwerowych. Następnie można subskrybować i wykorzystywać zdarzenia, takie jak zmiana stanu uruchomienia, zakończenie przebiegu, rejestracja modelu, wdrożenie modelu i wykrywanie dryfowania danych w obszarze roboczym.

Kiedy używać Event Grid dla akcji opartych na zdarzeniach:
* Wyślij wiadomości e-mail w przypadku niepowodzenia przebiegu i zakończenie przebiegu
* Użyj funkcji platformy Azure po zarejestrowaniu modelu
* Przesyłanie strumieniowe zdarzeń z Azure Machine Learning do różnych punktów końcowych
* Wyzwalanie potoku ML w przypadku wykrycia dryfu

> [!NOTE] 
> Obecnie zdarzenia runStatusChanged wyzwalają się tylko wtedy, gdy stan uruchomienia jest **zakończony niepowodzeniem**

## <a name="prerequisites"></a>Wymagania wstępne
Aby użyć Event Grid, musisz mieć uprawnienia współautora lub właściciela do obszaru roboczego Azure Machine Learning, dla którego będą tworzone zdarzenia.

## <a name="the-event-model--types"></a>Model zdarzenia & typy

Azure Event Grid odczytuje zdarzenia ze źródeł, takich jak Azure Machine Learning i inne usługi platformy Azure. Te zdarzenia są następnie wysyłane do programów obsługi zdarzeń, takich jak Azure Event Hubs, Azure Functions, Logic Apps i innych. Na poniższym diagramie przedstawiono sposób, w jaki Event Grid nawiązuje połączenie ze źródłami i procedurami obsługi, ale nie jest kompleksową listą obsługiwanych integracji.

![Azure Event Grid model funkcjonalny](./media/concept-event-grid-integration/azure-event-grid-functional-model.png)

Aby uzyskać więcej informacji o źródłach zdarzeń i obsłudze zdarzeń, zobacz [co to jest Event Grid?](../event-grid/overview.md).

### <a name="event-types-for-azure-machine-learning"></a>Typy zdarzeń dla Azure Machine Learning

Azure Machine Learning udostępnia zdarzenia w różnych punktach cyklu życia uczenia maszynowego: 

| Typ zdarzenia | Opis |
| ---------- | ----------- |
| `Microsoft.MachineLearningServices.RunCompleted` | Uruchamiany po zakończeniu eksperymentu uczenia maszynowego |
| `Microsoft.MachineLearningServices.ModelRegistered` | Uruchamiany, gdy model uczenia maszynowego jest zarejestrowany w obszarze roboczym |
| `Microsoft.MachineLearningServices.ModelDeployed` | Uruchamiany, gdy zostanie wykonane wdrożenie usługi wnioskowania z co najmniej jednym modelem |
| `Microsoft.MachineLearningServices.DatasetDriftDetected` | Uruchamiany po zakończeniu zadania wykrywania dryfu danych dla dwóch zestawów danych |
| `Microsoft.MachineLearningServices.RunStatusChanged` | Uruchamiany, gdy stan uruchomienia zostanie zmieniony, obecnie jest uruchamiany tylko wtedy, gdy stan uruchomienia to "Niepowodzenie" |

### <a name="filter--subscribe-to-events"></a>Filtrowanie & subskrybowanie zdarzeń

Te zdarzenia są publikowane za poorednictwem Azure Event Grid. Korzystając z Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure, klienci mogą łatwo subskrybować zdarzenia, [określając jeden lub więcej typów zdarzeń i warunki filtrowania](../event-grid/event-filtering.md). 

Podczas konfigurowania zdarzeń można zastosować filtry tylko do wyzwalania tylko dla określonych danych zdarzeń. W poniższym przykładzie dla zdarzeń zmiany stanu uruchomienia można filtrować według typów uruchomienia. Zdarzenie jest wyzwalane tylko wtedy, gdy kryteria są spełnione. Zapoznaj się z [schematem siatki zdarzeń Azure Machine Learning](../event-grid/event-schema-machine-learning.md) , aby dowiedzieć się więcej o danych zdarzeń, które można filtrować według. 

Subskrypcje dla zdarzeń Azure Machine Learning są chronione za pomocą kontroli dostępu opartej na rolach (Azure RBAC). Tylko [współautor lub właściciel](how-to-assign-roles.md#default-roles) obszaru roboczego może tworzyć, aktualizować i usuwać subskrypcje zdarzeń.  Filtry mogą być stosowane do subskrypcji zdarzeń podczas [tworzenia](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest&preserve-view=true) subskrypcji zdarzeń lub w późniejszym czasie. 


1. Przejdź do Azure Portal, wybierz nową subskrypcję lub istniejącą. 

1. Wybierz kartę Filtry i przewiń w dół do opcji filtry zaawansowane. Dla **klucza** i **wartości** Podaj typy właściwości, według których chcesz filtrować. W tym miejscu można zobaczyć, że zdarzenie będzie wyzwalane tylko wtedy, gdy typem uruchomienia jest uruchomienie potoku lub krok potoku.  

    :::image type="content" source="media/how-to-use-event-grid/select-event-filters.png" alt-text="Filtruj zdarzenia":::


+ **Filtruj według typu zdarzenia:** W subskrypcji zdarzeń można określić jeden lub więcej typów zdarzeń Azure Machine Learning.

+ **Filtruj według tematu zdarzenia:** Azure Event Grid obsługuje filtry podmiotu w oparciu o __początek__ i __kończące__ się na dopasowaniach, dzięki czemu zdarzenia z podmiotem pasującym są dostarczane do subskrybenta. Różne zdarzenia uczenia maszynowego mają różne formaty podmiotu.

  | Typ zdarzenia | Format tematu | Przykładowy temat |
  | ---------- | ----------- | ----------- |
  | `Microsoft.MachineLearningServices.RunCompleted` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` |
  | `Microsoft.MachineLearningServices.ModelRegistered` | `models/{modelName}:{modelVersion}` | `models/sklearn_regression_model:3` |
  | `Microsoft.MachineLearningServices.ModelDeployed` | `endpoints/{serviceId}` | `endpoints/my_sklearn_aks` |
  | `Microsoft.MachineLearningServices.DatasetDriftDetected` | `datadrift/{data.DataDriftId}/run/{data.RunId}` | `datadrift/4e694bf5-712e-4e40-b06a-d2a2755212d4/run/my_driftrun1_1550564444_fbbcdc0f` |
  | `Microsoft.MachineLearningServices.RunStatusChanged` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` | 

+ **Filtrowanie zaawansowane** : Azure Event Grid obsługuje także zaawansowane filtrowanie na podstawie opublikowanego schematu zdarzeń. Szczegóły schematu zdarzenia Azure Machine Learning można znaleźć w [Azure Event Grid schemacie zdarzeń dla Azure Machine Learning](../event-grid/event-schema-machine-learning.md).  Niektóre przykładowe zaawansowane filtrowanie, które można wykonać, to m.in.:

  Dla `Microsoft.MachineLearningServices.ModelRegistered` zdarzenia, aby filtrować wartość tagu modelu:

  ```
  --advanced-filter data.ModelTags.key1 StringIn ('value1')
  ```

  Aby dowiedzieć się więcej na temat stosowania filtrów, zobacz [filtrowanie zdarzeń dla Event Grid](../event-grid/how-to-filter-events.md).

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

## <a name="set-up-in-azure-portal"></a>Konfiguracja w Azure Portal

1. Otwórz [Azure Portal](https://portal.azure.com) i przejdź do obszaru roboczego Azure Machine Learning.

1. Na pasku po lewej stronie wybierz pozycję __zdarzenia__ , a następnie wybierz pozycję **subskrypcje zdarzeń**. 

    ![select-events-in-workspace.png](./media/how-to-use-event-grid/select-event.png)

1. Wybierz typ zdarzenia do użycia. Na przykład na poniższym zrzucie ekranu __zarejestrowano wybrany model__ , __wdrożono model__ , __wykonano przebieg__ i __wykryto dryf zestawu danych__ :

    ![Dodaj typ zdarzenia](./media/how-to-use-event-grid/add-event-type-updated.png)

1. Wybierz punkt końcowy, do którego ma zostać opublikowane wydarzenie. Na poniższym zrzucie ekranu __centrum zdarzeń__ jest wybranym punktem końcowym:

    ![Zrzut ekranu przedstawia okienko Utwórz subskrypcję zdarzeń z otwartym centrum zdarzeń.](./media/how-to-use-event-grid/select-event-handler.png)

Po potwierdzeniu wyboru, kliknij przycisk __Utwórz__. Po zakończeniu konfiguracji te zdarzenia zostaną wypchnięte do punktu końcowego.


### <a name="set-up-with-the-cli"></a>Konfigurowanie przy użyciu interfejsu wiersza polecenia

Możesz zainstalować najnowszy [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest)lub użyć Azure Cloud Shell dostarczanego w ramach subskrypcji platformy Azure.

Aby zainstalować rozszerzenie Event Grid, należy użyć następującego polecenia w interfejsie CLI:

```azurecli-interactive
az add extension --name eventgrid
```

W poniższym przykładzie pokazano, jak wybrać subskrypcję platformy Azure i utworzyć nową subskrypcję zdarzeń dla Azure Machine Learning:

```azurecli-interactive
# Select the Azure subscription that contains the workspace
az account set --subscription "<name or ID of the subscription>"

# Subscribe to the machine learning workspace. This example uses EventHub as a destination. 
az eventgrid event-subscription create --name {eventGridFilterName} \
  --source-resource-id /subscriptions/{subId}/resourceGroups/{RG}/providers/Microsoft.MachineLearningServices/workspaces/{wsName} \
  --endpoint-type eventhub \
  --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.EventHub/namespaces/n1/eventhubs/EH1 \
  --included-event-types Microsoft.MachineLearningServices.ModelRegistered \
  --subject-begins-with "models/mymodelname"
```

## <a name="examples"></a>Przykłady

### <a name="example-send-email-alerts"></a>Przykład: wysyłanie alertów e-mail

Użyj [Azure Logic Apps](../logic-apps/index.yml) , aby skonfigurować wiadomości e-mail dla wszystkich wydarzeń. Dostosuj warunki i określ adresatów, aby umożliwić współpracę i świadomość w zespołach pracujących wspólnie.

1. W Azure Portal przejdź do obszaru roboczego Azure Machine Learning i wybierz kartę zdarzenia na pasku po lewej stronie. W tym miejscu wybierz pozycję __Aplikacje logiki__. 

    ![Zrzut ekranu przedstawia stronę Machine Learning zdarzeń obszaru roboczego z Logic Apps.](./media/how-to-use-event-grid/select-logic-ap.png)

1. Zaloguj się do interfejsu użytkownika aplikacji logiki i wybierz usługę Machine Learning jako typ tematu. 

    ![Zrzut ekranu przedstawia okno dialogowe gdy występuje zdarzenie dotyczące zasobu z opcją Uczenie maszynowe jako typ zasobu.](./media/how-to-use-event-grid/select-topic-type.png)

1. Wybierz zdarzenia do powiadomienia. Na przykład poniższy zrzut ekranu __RunCompleted__.

    ![Zrzut ekranu przedstawia okno dialogowe gdy występuje zdarzenie zasobu z wybranym typem zdarzenia.](./media/how-to-use-event-grid/select-event-runcomplete.png)

1. Możesz użyć metody filtrowania w powyższej sekcji lub dodać filtry, aby wyzwolić aplikację logiki tylko w podzestawie typów zdarzeń. Na poniższym zrzucie ekranu jest używany __Filtr prefiksu__ __/datadriftID/runs/__ .

    ![Filtrowanie — zdarzenia](./media/how-to-use-event-grid/filtering-events.png)

1. Następnie należy dodać krok, aby użyć tego zdarzenia i wyszukać adres e-mail. Istnieje kilka różnych kont poczty, za pomocą których można odbierać zdarzenia. Możesz również skonfigurować warunki na momentu wysłania alertu e-mail.

    ![Zrzut ekranu przedstawia okno dialogowe Wybieranie akcji z adresem e-mail wprowadzonym w wierszu wyszukiwania.](./media/how-to-use-event-grid/select-email-action.png)

1. Wybierz pozycję __Wyślij wiadomość e-mail__ i wypełnij parametry. W temacie można uwzględnić __Typ zdarzenia__ i __temat__ w celu ułatwienia filtrowania zdarzeń. Możesz również dołączyć link do strony obszaru roboczego dla przebiegów w treści wiadomości. 

    ![Zrzut ekranu przedstawia okno dialogowe Wysyłanie wiadomości e-mail z tematem i typem zdarzenia dodanym do wiersza tematu z listy po prawej stronie.](./media/how-to-use-event-grid/configure-email-body.png)

1. Aby zapisać tę akcję, wybierz pozycję **Zapisz jako** w lewym rogu strony. Na wyświetlonym prawym pasku potwierdź utworzenie tej akcji.

    ![Zrzut ekranu przedstawia przyciski Zapisz jako i Utwórz w projektancie Logic Apps.](./media/how-to-use-event-grid/confirm-logic-app-create.png)


### <a name="example-data-drift-triggers-retraining"></a>Przykład: przeszkolenie z zakresu dryfowania danych

Modele są nieodświeżone w czasie i nie pozostają przydatne w kontekście, w którym działa. Jednym ze sposobów, aby określić, czy czas na ponowne nauczenie modelu wykrywa dryfowanie danych. 

Ten przykład pokazuje, jak używać usługi Event Grid z aplikacją logiki platformy Azure, aby wyzwolić ponowne szkolenie. Przykład wyzwala potok Azure Data Factory, gdy nastąpi przekształcenie danych między uczeniem modelu i obsługującym zestawami.

Przed rozpoczęciem wykonaj następujące czynności:

* Konfigurowanie monitora zestawu danych w celu [wykrywania dryfu danych](how-to-monitor-datasets.md) w obszarze roboczym
* Utwórz [potok Azure Data Factory](../data-factory/index.yml)opublikowany.

W tym przykładzie prosty potok Data Factory jest używany do kopiowania plików do magazynu obiektów blob i uruchamiania potoku opublikowanego Machine Learning. Aby uzyskać więcej informacji na temat tego scenariusza, zobacz Jak skonfigurować [Machine Learning krok w Azure Data Factory](../data-factory/transform-data-machine-learning-service.md)

![Zrzut ekranu przedstawia potok szkoleń w zasobach fabrycznych z kopią Pipeline1.](./media/how-to-use-event-grid/adf-mlpipeline-stage.png)

1. Zacznij od utworzenia aplikacji logiki. Przejdź do [Azure Portal](https://portal.azure.com), wyszukaj pozycję Logic Apps, a następnie wybierz pozycję Utwórz.

    ![Search-Logic-App](./media/how-to-use-event-grid/search-for-logic-app.png)

1. Wprowadź żądane informacje. Aby uprościć środowisko pracy, Użyj tej samej subskrypcji i grupy zasobów co potok Azure Data Factory i Azure Machine Learning obszar roboczy.

    ![Zrzut ekranu przedstawia okienko tworzenia aplikacji logiki.](./media/how-to-use-event-grid/set-up-logic-app-for-adf.png)

1. Po utworzeniu aplikacji logiki wybierz opcję __gdy wystąpi zdarzenie zasobów Event Grid__. 

    ![Zrzut ekranu przedstawia projektanta Logic Apps z opcją Rozpocznij z użyciem typowych opcji wyzwalacza, w tym gdy wystąpi zdarzenie zasobu Event Grid.](./media/how-to-use-event-grid/select-event-grid-trigger.png)

1. Zaloguj się i wprowadź szczegóły zdarzenia. W polu __nazwa zasobu__ Ustaw nazwę obszaru roboczego. Ustaw __Typ zdarzenia__ na __DatasetDriftDetected__.

    ![Zrzut ekranu pokazuje, kiedy zdarzenie jest wykonywane z wybranym elementem typu zdarzenia.](./media/how-to-use-event-grid/login-and-add-event.png)

1. Dodaj nowy krok i Wyszukaj __Azure Data Factory__. Wybierz pozycję __Utwórz uruchomienie potoku__. 

    ![Zrzut ekranu przedstawia okienko wybierz akcję z wybranym przebiegiem tworzenia potoku.](./media/how-to-use-event-grid/create-adfpipeline-run.png)

1. Zaloguj się i określ opublikowany potok Azure Data Factory do uruchomienia.

    ![Zrzut ekranu przedstawia okienko tworzenie uruchomienia potoku z różnymi wartościami.](./media/how-to-use-event-grid/specify-adf-pipeline.png)

1. Zapisz i Utwórz aplikację logiki przy użyciu przycisku **Zapisz** znajdującego się w lewym górnym rogu strony. Aby wyświetlić aplikację, przejdź do obszaru roboczego w [Azure Portal](https://portal.azure.com) i kliknij pozycję **zdarzenia**.

    ![Zrzut ekranu przedstawia zdarzenia z wyróżnioną aplikacją logiki.](./media/how-to-use-event-grid/show-logic-app-webhook.png)

Teraz potok fabryki danych jest wyzwalany, gdy nastąpi dryf. Wyświetl szczegóły dotyczące przebiegu dryfowania danych i potoku uczenia maszynowego w [nowym portalu obszaru roboczego](https://ml.azure.com). 

![Zrzut ekranu przedstawia punkty końcowe potoku.](./media/how-to-use-event-grid/view-in-workspace.png)

### <a name="example-deploy-a-model-based-on-tags"></a>Przykład: Wdrażanie modelu na podstawie tagów

Obiekt modelu Azure Machine Learning zawiera parametry, które można przestawiać wdrożenia, takie jak nazwa modelu, wersja, tag i właściwość. Zdarzenie rejestracji modelu może wyzwolić punkt końcowy i można użyć funkcji platformy Azure do wdrożenia modelu na podstawie wartości tych parametrów.

Aby zapoznać się z przykładem, zobacz [https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid](https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid) repozytorium i postępuj zgodnie z instrukcjami w pliku **README** .

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o Event Grid i nadaj Azure Machine Learning zdarzeniom:

- [Event Grid — informacje](../event-grid/overview.md)

- [Schemat zdarzenia dla Azure Machine Learning](../event-grid/event-schema-machine-learning.md)