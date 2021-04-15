---
title: Wyzwalanie zdarzeń w przepływach pracy uczenia maszynowego (wersja zapoznawcza)
titleSuffix: Azure Machine Learning
description: Konfigurowanie aplikacji opartych na zdarzeniach, procesów lub przepływów pracy uczenia maszynowego CI/CD w Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli
ms.author: shipatel
author: shivp950
ms.reviewer: larryfr
ms.date: 05/11/2020
ms.openlocfilehash: 09a541e784167251e0833f6fd3f6130a450e07d8
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478849"
---
# <a name="trigger-applications-processes-or-cicd-workflows-based-on-azure-machine-learning-events-preview"></a>Wyzwalanie aplikacji, procesów lub przepływów pracy ci/cd na podstawie Azure Machine Learning (wersja zapoznawcza)

W tym artykule dowiesz się, jak skonfigurować oparte na zdarzeniach aplikacje, procesy lub przepływy pracy ci/cd na podstawie zdarzeń usługi Azure Machine Learning, takich jak wiadomości e-mail z powiadomieniami o błędach lub uruchomienia potoku uczenia maszynowego, gdy określone warunki zostaną wykryte przez program [Azure Event Grid](../event-grid/index.yml).

Azure Machine Learning zarządza całym cyklem życia procesu uczenia maszynowego, w tym trenowanie modelu, wdrażanie modelu i monitorowanie. Za pomocą usługi Event Grid można reagować na zdarzenia usługi Azure Machine Learning, takie jak ukończenie przebiegów trenowania, rejestracja i wdrażanie modeli oraz wykrywanie dryfu danych przy użyciu nowoczesnych architektur bez serwera. Następnie możesz subskrybować i korzystać ze zdarzeń, takich jak zmiana stanu uruchomienia, ukończenie uruchamiania, rejestracja modelu, wdrażanie modelu i wykrywanie dryfu danych w obszarze roboczym.

Kiedy należy używać Event Grid dla akcji sterowanych zdarzeniami:
* Wysyłanie wiadomości e-mail po niepowodzeniu uruchomienia i zakończeniu uruchamiania
* Używanie funkcji platformy Azure po zarejestrowaniu modelu
* Przesyłanie strumieniowe zdarzeń Azure Machine Learning do różnych punktów końcowych
* Wyzwalanie potoku uczenia maszynowego po wykryciu dryfu

## <a name="prerequisites"></a>Wymagania wstępne
Aby korzystać Event Grid, musisz mieć dostęp współautora lub właściciela do Azure Machine Learning, dla Azure Machine Learning dla których będziesz tworzyć zdarzenia.

## <a name="the-event-model--types"></a>Typy & zdarzeń

Azure Event Grid odczytuje zdarzenia ze źródeł, takich jak Azure Machine Learning i inne usługi platformy Azure. Te zdarzenia są następnie wysyłane do programów obsługi zdarzeń, takich jak Azure Event Hubs, Azure Functions, Logic Apps i inne. Na poniższym diagramie pokazano, Event Grid łączy źródła i programy obsługi, ale nie jest to pełna lista obsługiwanych integracji.

![Azure Event Grid model funkcjonalny](./media/concept-event-grid-integration/azure-event-grid-functional-model.png)

Aby uzyskać więcej informacji na temat źródeł zdarzeń i programów obsługi zdarzeń, zobacz [Co to jest Event Grid?](../event-grid/overview.md).

### <a name="event-types-for-azure-machine-learning"></a>Typy zdarzeń dla Azure Machine Learning

Azure Machine Learning zdarzenia w różnych punktach cyklu życia uczenia maszynowego: 

| Typ zdarzenia | Opis |
| ---------- | ----------- |
| `Microsoft.MachineLearningServices.RunCompleted` | Wywoływane po zakończeniu eksperymentu uczenia maszynowego |
| `Microsoft.MachineLearningServices.ModelRegistered` | Wywoływane, gdy model uczenia maszynowego jest zarejestrowany w obszarze roboczym |
| `Microsoft.MachineLearningServices.ModelDeployed` | Wywoływane po zakończeniu wdrażania usługi wnioskowania z co najmniej jednym modelem |
| `Microsoft.MachineLearningServices.DatasetDriftDetected` | Wywoływane po zakończeniu zadania wykrywania dryfu danych dla dwóch zestawów danych |
| `Microsoft.MachineLearningServices.RunStatusChanged` | Wywoływane po zmianie stanu uruchomienia, obecnie wywoływane tylko wtedy, gdy stan uruchomienia to "niepowodzenie" |

### <a name="filter--subscribe-to-events"></a>Filtrowanie & subskrybowanie zdarzeń

Te zdarzenia są publikowane za pośrednictwem Azure Event Grid. Korzystając Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure, klienci mogą łatwo subskrybować zdarzenia, określając co najmniej jeden typ zdarzenia i [filtrując warunki.](../event-grid/event-filtering.md) 

Podczas konfigurowania zdarzeń można zastosować filtry, aby wyzwalać tylko określone dane zdarzenia. W poniższym przykładzie dla zdarzeń zmiany stanu uruchomienia można filtrować według typów przebiegów. Zdarzenie jest wyzwalane tylko wtedy, gdy kryteria są spełnione. Zapoznaj się z [Azure Machine Learning usługi Event Grid,](../event-grid/event-schema-machine-learning.md) aby dowiedzieć się więcej o danych zdarzeń, według których można filtrować. 

Subskrypcje dla zdarzeń Azure Machine Learning są chronione przez kontrolę dostępu na podstawie ról (RBAC) platformy Azure. Tylko [współautor lub właściciel](how-to-assign-roles.md#default-roles) obszaru roboczego może tworzyć, aktualizować i usuwać subskrypcje zdarzeń.  Filtry można stosować do subskrypcji zdarzeń podczas [tworzenia](/cli/azure/eventgrid/event-subscription) subskrypcji zdarzeń lub w późniejszym czasie. 


1. Przejdź do Azure Portal wybierz nową lub istniejącą. 

1. Wybierz kartę Filtry i przewiń w dół do opcji Filtry zaawansowane. Dla wartości **Klucz** **i** Wartość podaj typy właściwości, według których chcesz filtrować. W tym miejscu można zobaczyć, że zdarzenie zostanie wyzwolne tylko wtedy, gdy typ uruchomienia to uruchomienie potoku lub krok potoku.  

    :::image type="content" source="media/how-to-use-event-grid/select-event-filters.png" alt-text="filtrowanie zdarzeń":::


+ **Filtruj według typu zdarzenia:** Subskrypcja zdarzeń może określać co najmniej jeden typ Azure Machine Learning zdarzeń.

+ **Filtruj według tematu zdarzenia:** Azure Event Grid obsługuje filtry tematu  oparte na  rozpoczynaniu od i kończy się dopasowaniami, dzięki czemu zdarzenia z pasującymi tematami są dostarczane do subskrybenta. Różne zdarzenia uczenia maszynowego mają inny format tematu.

  | Typ zdarzenia | Format tematu | Temat przykładu |
  | ---------- | ----------- | ----------- |
  | `Microsoft.MachineLearningServices.RunCompleted` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` |
  | `Microsoft.MachineLearningServices.ModelRegistered` | `models/{modelName}:{modelVersion}` | `models/sklearn_regression_model:3` |
  | `Microsoft.MachineLearningServices.ModelDeployed` | `endpoints/{serviceId}` | `endpoints/my_sklearn_aks` |
  | `Microsoft.MachineLearningServices.DatasetDriftDetected` | `datadrift/{data.DataDriftId}/run/{data.RunId}` | `datadrift/4e694bf5-712e-4e40-b06a-d2a2755212d4/run/my_driftrun1_1550564444_fbbcdc0f` |
  | `Microsoft.MachineLearningServices.RunStatusChanged` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` | 

+ **Zaawansowane filtrowanie:** Azure Event Grid zaawansowane filtrowanie na podstawie opublikowanego schematu zdarzeń. Azure Machine Learning szczegóły schematu zdarzeń można znaleźć w Azure Event Grid [zdarzeń dla Azure Machine Learning](../event-grid/event-schema-machine-learning.md).  Niektóre przykładowe zaawansowane filtrowanie, które można wykonać, obejmują:

  W `Microsoft.MachineLearningServices.ModelRegistered` przypadku zdarzenia, aby filtrować wartość tagu modelu:

  ```
  --advanced-filter data.ModelTags.key1 StringIn ('value1')
  ```

  Aby dowiedzieć się więcej na temat stosowania filtrów, zobacz [Zdarzenia filtrowania dla Event Grid](../event-grid/how-to-filter-events.md).

## <a name="consume-machine-learning-events"></a>Zużywanie Machine Learning zdarzeń

Aplikacje, które obsługują Machine Learning zdarzeń, powinny stosować się do kilku zalecanych rozwiązań:

> [!div class="checklist"]
> * Ponieważ wiele subskrypcji można skonfigurować do przekierowania zdarzeń do tego samego programu obsługi zdarzeń, ważne jest, aby nie zakładać, że zdarzenia pochodzą z określonego źródła, ale aby sprawdzić temat komunikatu, aby upewnić się, że pochodzi on z spodziewanego obszaru roboczego uczenia maszynowego.
> * Podobnie sprawdź, czy typ eventType jest gotowy do przetwarzania i nie zakładaj, że wszystkie odbierane zdarzenia będą typami, których oczekujesz.
> * Ponieważ komunikaty mogą być odbierane poza kolejnością i po pewnym opóźnieniu, użyj pól etag, aby sprawdzić, czy informacje o obiektach są nadal aktualne.  Ponadto należy użyć pól programu Sequencer, aby zrozumieć kolejność zdarzeń na dowolnym konkretnym obiekcie.
> * Ignoruj pola, których nie rozumiesz. Takie rozwiązanie pomoże zapewnić odporność na nowe funkcje, które mogą zostać dodane w przyszłości.
> * Niepowodzenie lub anulowanie Azure Machine Learning nie wyzwoli zdarzenia. Jeśli na przykład wdrożenie modelu zakończy się niepowodzeniem, nie zostanie wyzwolony pakiet Microsoft.MachineLearningServices.ModelDeployed. Podczas projektowania aplikacji należy wziąć pod uwagę taki tryb awarii. Zawsze możesz użyć Azure Machine Learning SDK, interfejsu wiersza polecenia lub portalu, aby sprawdzić stan operacji i poznać szczegółowe przyczyny niepowodzenia.

Azure Event Grid klienci mogą tworzyć procedury obsługi komunikatów od sprzężonej, które mogą być wyzwalane przez Azure Machine Learning zdarzeń. Oto kilka notowalnych przykładów programów obsługi komunikatów:
* Azure Functions
* Azure Logic Apps
* Azure Event Hubs
* Azure Data Factory potoku
* Ogólne webhook, które mogą być hostowane na platformie Azure lub w innym miejscu

## <a name="set-up-in-azure-portal"></a>Konfigurowanie w u Azure Portal

1. Otwórz [Azure Portal](https://portal.azure.com) i przejdź do swojego Azure Machine Learning roboczego.

1. Na pasku po lewej stronie wybierz __pozycję Zdarzenia,__ a następnie **wybierz pozycję Subskrypcje zdarzeń.** 

    ![select-events-in-workspace.png](./media/how-to-use-event-grid/select-event.png)

1. Wybierz typ zdarzenia, które ma być używane. Na przykład na poniższym zrzucie ekranu wybrano opcję __Zarejestrowano__ model, __Wdrożono model,__ __Ukończono__ przebieg i Wykryto __dryf zestawu danych:__

    ![add-event-type](./media/how-to-use-event-grid/add-event-type-updated.png)

1. Wybierz punkt końcowy, w celu opublikowania zdarzenia. Na poniższym zrzucie ekranu __centrum zdarzeń__ jest wybranym punktem końcowym:

    ![Zrzut ekranu przedstawia okienko Tworzenie subskrypcji zdarzeń z otwartym poleceniem Wybierz centrum zdarzeń.](./media/how-to-use-event-grid/select-event-handler.png)

Po potwierdzeniu wyboru kliknij pozycję __Utwórz__. Po zakończeniu konfiguracji te zdarzenia zostaną wypchnąć do punktu końcowego.


### <a name="set-up-with-the-cli"></a>Konfigurowanie przy użyciu interfejsu wiersza polecenia

Możesz zainstalować najnowszy interfejs wiersza polecenia [platformy Azure](/cli/azure/install-azure-cli)lub użyć Azure Cloud Shell, który jest dostarczany w ramach subskrypcji platformy Azure.

Aby zainstalować rozszerzenie Event Grid, użyj następującego polecenia z interfejsu wiersza polecenia:

```azurecli-interactive
az add extension --name eventgrid
```

W poniższym przykładzie pokazano sposób wybierania subskrypcji platformy Azure i tworzenia nowej subskrypcji zdarzeń dla Azure Machine Learning:

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

### <a name="example-send-email-alerts"></a>Przykład: Wysyłanie alertów e-mail

Użyj [Azure Logic Apps,](../logic-apps/index.yml) aby skonfigurować wiadomości e-mail dla wszystkich zdarzeń. Dostosuj się do warunków i określ adresatów, aby umożliwić współpracę i świadomość zespołów pracujących razem.

1. W Azure Portal przejdź do obszaru roboczego Azure Machine Learning i wybierz kartę zdarzenia na pasku po lewej stronie. W tym miejscu wybierz pozycję __Aplikacje logiki.__ 

    ![Zrzut ekranu przedstawia Machine Learning zdarzeń obszaru roboczego z Logic Apps.](./media/how-to-use-event-grid/select-logic-ap.png)

1. Zaloguj się do interfejsu użytkownika aplikacji logiki i Machine Learning service jako typ tematu. 

    ![Zrzut ekranu przedstawia okno dialogowe Gdy wystąpi zdarzenie zasobu, w którym jako typ zasobu wybrano uczenie maszynowe.](./media/how-to-use-event-grid/select-topic-type.png)

1. Wybierz zdarzenia, o których chcesz być powiadamiane. Na przykład poniższy zrzut ekranu __runcompleted__.

    ![Zrzut ekranu przedstawia okno dialogowe Gdy wystąpi zdarzenie zasobu z wybranym typem zdarzenia.](./media/how-to-use-event-grid/select-event-runcomplete.png)

1. Możesz użyć metody filtrowania w powyższej sekcji lub dodać filtry, aby wyzwolić aplikację logiki tylko dla podzbioru typów zdarzeń. Na poniższym zrzucie ekranu jest używany __filtr__ __prefiksu /datadriftID/runs/.__

    ![filter-events](./media/how-to-use-event-grid/filtering-events.png)

1. Następnie dodaj krok, aby korzystać z tego zdarzenia i wyszukać wiadomość e-mail. Istnieje kilka różnych kont poczty, których można użyć do odbierania zdarzeń. Możesz również skonfigurować warunki dotyczące wysyłania alertu e-mail.

    ![Zrzut ekranu przedstawia okno dialogowe Wybieranie akcji z wiadomością e-mail wprowadzona w wierszu wyszukiwania.](./media/how-to-use-event-grid/select-email-action.png)

1. Wybierz __pozycję Wyślij wiadomość e-mail__ i wypełnij parametry. W temacie można uwzględnić  typ zdarzenia i __temat,__ aby ułatwić filtrowanie zdarzeń. W treści komunikatu możesz również dołączyć link do strony obszaru roboczego dla przebiegów. 

    ![Zrzut ekranu przedstawia okno dialogowe Wysyłanie wiadomości e-mail z polami Temat i Typ zdarzenia dodanymi do wiersza tematu z listy po prawej stronie.](./media/how-to-use-event-grid/configure-email-body.png)

1. Aby zapisać tę akcję, **wybierz pozycję Zapisz** jako w lewym rogu strony. Na prawym pasku, który zostanie wyświetlony, potwierdź utworzenie tej akcji.

    ![Zrzut ekranu przedstawia przyciski Zapisz jako i Utwórz w Logic Apps Projektanta.](./media/how-to-use-event-grid/confirm-logic-app-create.png)


### <a name="example-data-drift-triggers-retraining"></a>Przykład: Wyzwalacze dryfu danych podczas ponownego trenowania

Modele przestają być przestarzałe i nie będą użyteczne w kontekście, w ramach których są uruchomione. Jednym ze sposobów na wykrycie dryfu danych jest wykrycie dryfu danych na czas ponownego trenowania modelu. 

W tym przykładzie pokazano, jak wyzwolić ponowne trenowanie za pomocą usługi Event Grid z aplikacją logiki platformy Azure. Przykład wyzwala potok Azure Data Factory, gdy występuje dryf danych między zestawami danych trenowania i obsługi modelu.

Przed rozpoczęciem wykonaj następujące czynności:

* Konfigurowanie monitora zestawu danych w celu [wykrywania dryfu danych](how-to-monitor-datasets.md) w obszarze roboczym
* Utwórz opublikowany [potok Azure Data Factory potoku](../data-factory/index.yml).

W tym przykładzie prosty potok Data Factory służy do kopiowania plików do magazynu obiektów blob i uruchamiania opublikowanego Machine Learning potoku. Aby uzyskać więcej informacji na temat tego scenariusza, zobacz jak skonfigurować [Machine Learning krok w Azure Data Factory](../data-factory/transform-data-machine-learning-service.md)

![Zrzut ekranu przedstawia potok trenowania w zasobach fabryki z poleceniem Kopiuj dane1, podając M L Execute Pipeline1.](./media/how-to-use-event-grid/adf-mlpipeline-stage.png)

1. Rozpocznij od utworzenia aplikacji logiki. Przejdź do [Azure Portal](https://portal.azure.com), wyszukaj Logic Apps, a następnie wybierz pozycję Utwórz.

    ![search-logic-app](./media/how-to-use-event-grid/search-for-logic-app.png)

1. Wprowadź żądane informacje. Aby uprościć środowisko, użyj tej samej subskrypcji i grupy zasobów, co Azure Data Factory potoku i Azure Machine Learning obszaru roboczego.

    ![Zrzut ekranu przedstawia okienko Tworzenie aplikacji logiki.](./media/how-to-use-event-grid/set-up-logic-app-for-adf.png)

1. Po utworzeniu aplikacji logiki wybierz pozycję __Gdy wystąpi Event Grid zasobu.__ 

    ![Zrzut ekranu przedstawia Logic Apps z menu Start z typami opcji wyzwalacza, w tym po Event Grid zdarzenia zasobu.](./media/how-to-use-event-grid/select-event-grid-trigger.png)

1. Zaloguj się i podaj szczegóły zdarzenia. W obszarze __Nazwa zasobu__ ustaw nazwę obszaru roboczego. Dla ustawienia __Typ zdarzenia ustaw__ wartość __DatasetDriftDetected__.

    ![Zrzut ekranu przedstawia zdarzenie Gdy wystąpi zdarzenie zasobu z wybranym elementem typu zdarzenia.](./media/how-to-use-event-grid/login-and-add-event.png)

1. Dodaj nowy krok i wyszukaj Azure Data Factory __.__ Wybierz __pozycję Utwórz uruchomienie potoku.__ 

    ![Zrzut ekranu przedstawia okienko Wybieranie akcji z wybraną akcję Utwórz uruchomienie potoku.](./media/how-to-use-event-grid/create-adfpipeline-run.png)

1. Zaloguj się i określ opublikowany Azure Data Factory do uruchomienia.

    ![Zrzut ekranu przedstawia okienko Tworzenie uruchomienia potoku z różnymi wartościami.](./media/how-to-use-event-grid/specify-adf-pipeline.png)

1. Zapisz i utwórz aplikację logiki przy użyciu **przycisku Zapisz** w lewym górnym rogu strony. Aby wyświetlić aplikację, przejdź do obszaru roboczego w Azure Portal [i](https://portal.azure.com) kliknij pozycję **Zdarzenia.**

    ![Zrzut ekranu przedstawia zdarzenia z wyróżniona aplikacją logiki.](./media/how-to-use-event-grid/show-logic-app-webhook.png)

Potok fabryki danych jest teraz wyzwalany w przypadku wystąpienia dryfu. Wyświetl szczegółowe informacje o uruchomieniu dryfu danych i potoku uczenia maszynowego w [nowym portalu obszaru roboczego.](https://ml.azure.com) 

![Zrzut ekranu przedstawia punkty końcowe potoku.](./media/how-to-use-event-grid/view-in-workspace.png)

### <a name="example-deploy-a-model-based-on-tags"></a>Przykład: wdrażanie modelu na podstawie tagów

Obiekt Azure Machine Learning model zawiera parametry, które można przew zależności od wdrożenia, takie jak nazwa modelu, wersja, tag i właściwość. Zdarzenie rejestracji modelu może wyzwolić punkt końcowy. Za pomocą funkcji platformy Azure można wdrożyć model na podstawie wartości tych parametrów.

Aby zapoznać się z przykładem, zobacz [https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid](https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid) repozytorium i postępuj zgodnie z instrukcjami w pliku **readme.**

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o Event Grid i Azure Machine Learning zdarzeń spróbuj:

- [Event Grid — informacje](../event-grid/overview.md)

- [Schemat zdarzeń dla Azure Machine Learning](../event-grid/event-schema-machine-learning.md)
