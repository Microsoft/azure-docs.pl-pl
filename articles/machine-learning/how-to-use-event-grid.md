---
title: Tworzenie przepływów pracy uczenia maszynowego opartego na zdarzeniach
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak używać programu Event Grid z Azure Machine Learning, aby włączyć rozwiązania oparte na zdarzeniach.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: shipatel
author: shivp950
ms.reviewer: larryfr
ms.date: 03/11/2020
ms.openlocfilehash: 2a1440dcda27a487c89be4ac63e624a2bb6b393a
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82111882"
---
# <a name="create-event-driven-machine-learning-workflows-preview"></a>Tworzenie przepływów pracy uczenia maszynowego opartego na zdarzeniach (Podgląd)

[Azure Event Grid](https://docs.microsoft.com/azure/event-grid/) obsługuje zdarzenia Azure Machine Learning. Można subskrybować i korzystać z zdarzeń, takich jak zmiana stanu uruchomienia, zakończenie przebiegu, rejestracja modelu, wdrożenie modelu i wykrywanie dryfowania danych w obszarze roboczym.

Aby uzyskać więcej informacji na temat typów zdarzeń, zobacz [Azure Machine Learning Integration with Event Grid](concept-event-grid-integration.md) i [schemat usługi Event Grid Azure Machine Learning](/azure/event-grid/event-schema-machine-learning).

Użyj Event Grid, aby włączyć typowe scenariusze, takie jak:

* Wyślij wiadomości e-mail w przypadku niepowodzenia przebiegu i zakończenie przebiegu
* Użyj funkcji platformy Azure po zarejestrowaniu modelu
* Przesyłanie strumieniowe zdarzeń z Azure Machine Learning do różnych punktów końcowych
* Wyzwalanie potoku ML w przypadku wykrycia dryfu

> [!NOTE] 
> Obecnie zdarzenia runStatusChanged wyzwalają się tylko wtedy, gdy stan uruchomienia jest **zakończony niepowodzeniem**
>

## <a name="prerequisites"></a>Wymagania wstępne
* Dostęp współautora lub właściciela do obszaru roboczego Azure Machine Learning, dla którego będą tworzone zdarzenia.

### <a name="configure-eventgrid-using-the-azure-portal"></a>Konfigurowanie EventGrid przy użyciu Azure Portal

1. Otwórz [Azure Portal](https://portal.azure.com) i przejdź do obszaru roboczego Azure Machine Learning.

1. Na pasku po lewej stronie wybierz pozycję __zdarzenia__ , a następnie wybierz pozycję **subskrypcje zdarzeń**. 

    ![SELECT-Events-in-Workspace. png](./media/how-to-use-event-grid/select-event.png)

1. Wybierz typ zdarzenia do użycia. Na przykład na poniższym zrzucie ekranu __zarejestrowano wybrany model__, __wdrożono model__, __wykonano przebieg__i __wykryto dryf zestawu danych__:

    ![Dodaj typ zdarzenia](./media/how-to-use-event-grid/add-event-type-updated.png)

1. Wybierz punkt końcowy, do którego ma zostać opublikowane wydarzenie. Na poniższym zrzucie ekranu __centrum zdarzeń__ jest wybranym punktem końcowym:

    ![Procedura obsługi zaznaczania zdarzeń](./media/how-to-use-event-grid/select-event-handler.png)

Po potwierdzeniu wyboru, kliknij przycisk __Utwórz__. Po zakończeniu konfiguracji te zdarzenia zostaną wypchnięte do punktu końcowego.


### <a name="configure-eventgrid-using-the-cli"></a>Konfigurowanie EventGrid przy użyciu interfejsu wiersza polecenia

Możesz zainstalować najnowszy [interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)lub użyć Azure Cloud Shell dostarczanego w ramach subskrypcji platformy Azure.

Aby zainstalować rozszerzenie Event Grid, należy użyć następującego polecenia w interfejsie CLI:

```azurecli-interactive
az add extension --name eventgrid
```

W poniższym przykładzie pokazano, jak wybrać subskrypcję platformy Azure i utworzyć nową subskrypcję zdarzeń dla Azure Machine Learning:

```azurecli-interactive
# Select the Azure subscription that contains the workspace
az account set --subscription "<name or ID of the subscription>"

# Subscribe to the machine learning workspace.
az eventgrid event-subscription create \
  --name {eventGridFilterName} \
  --source-resource-id "/subscriptions/{subId}/resourceGroups/{rgName}/ \providers/Microsoft.MachineLearningServices/workspaces/{wsName}" \
  --endpoint {event handler endpoint} \
  --included-event-types Microsoft.MachineLearningServices.ModelRegistered \
  --subject-begins-with "models/mymodelname"
```

## <a name="filter-events"></a>Filtruj zdarzenia

Podczas konfigurowania zdarzeń można zastosować filtry tylko do wyzwalania tylko dla określonych danych zdarzeń. W poniższym przykładzie dla zdarzeń zmiany stanu uruchomienia można filtrować według typów uruchomienia. Zdarzenie jest wyzwalane tylko wtedy, gdy kryteria są spełnione. Zapoznaj się z [schematem siatki zdarzeń Azure Machine Learning](/azure/event-grid/event-schema-machine-learning) , aby dowiedzieć się więcej o danych zdarzeń, które można filtrować według. 

1. Przejdź do Azure Portal, wybierz nową subskrypcję lub istniejącą. 

1. Wybierz kartę Filtry i przewiń w dół do opcji filtry zaawansowane. Dla **klucza** i **wartości**Podaj typy właściwości, według których chcesz filtrować. W tym miejscu można zobaczyć, że zdarzenie będzie wyzwalane tylko wtedy, gdy typem uruchomienia jest uruchomienie potoku lub krok potoku.  

    :::image type="content" source="media/how-to-use-event-grid/select-event-filters.png" alt-text="Filtruj zdarzenia":::

## <a name="sample-send-email-alerts"></a>Przykład: wysyłanie alertów e-mail

Użyj [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/) , aby skonfigurować wiadomości e-mail dla wszystkich wydarzeń. Dostosuj warunki i określ adresatów, aby umożliwić współpracę i świadomość w zespołach pracujących wspólnie.

1. W Azure Portal przejdź do obszaru roboczego Azure Machine Learning i wybierz kartę zdarzenia na pasku po lewej stronie. W tym miejscu wybierz pozycję __Aplikacje logiki__. 

    ![SELECT-Logic-AP](./media/how-to-use-event-grid/select-logic-ap.png)

1. Zaloguj się do interfejsu użytkownika aplikacji logiki i wybierz usługę Machine Learning jako typ tematu. 

    ![Wybierz typ tematu](./media/how-to-use-event-grid/select-topic-type.png)

1. Wybierz zdarzenia do powiadomienia. Na przykład poniższy zrzut ekranu __RunCompleted__.

    ![SELECT-Event-runcomplete](./media/how-to-use-event-grid/select-event-runcomplete.png)

1. Możesz użyć metody filtrowania w powyższej sekcji lub dodać filtry, aby wyzwolić aplikację logiki tylko w podzestawie typów zdarzeń. Na poniższym zrzucie ekranu jest używany __Filtr prefiksu__ __/datadriftID/runs/__ .

    ![Filtrowanie — zdarzenia](./media/how-to-use-event-grid/filtering-events.png)

1. Następnie należy dodać krok, aby użyć tego zdarzenia i wyszukać adres e-mail. Istnieje kilka różnych kont poczty, za pomocą których można odbierać zdarzenia. Możesz również skonfigurować warunki na momentu wysłania alertu e-mail.

    ![Wybieranie — wiadomość e-mail — akcja](./media/how-to-use-event-grid/select-email-action.png)

1. Wybierz pozycję __Wyślij wiadomość e-mail__ i wypełnij parametry. W temacie można uwzględnić __Typ zdarzenia__ i __temat__ w celu ułatwienia filtrowania zdarzeń. Możesz również dołączyć link do strony obszaru roboczego dla przebiegów w treści wiadomości. 

    ![Konfigurowanie adresu e-mail — treść](./media/how-to-use-event-grid/configure-email-body.png)

1. Aby zapisać tę akcję, wybierz pozycję **Zapisz jako** w lewym rogu strony. Na wyświetlonym prawym pasku potwierdź utworzenie tej akcji.

    ![potwierdzenie-Logic-App-Create](./media/how-to-use-event-grid/confirm-logic-app-create.png)


## <a name="sample-trigger-retraining-when-data-drift-occurs"></a>Przykład: Wyzwól ponowne szkolenie w przypadku wystąpienia dryfowania danych

Modele są nieodświeżone w czasie i nie pozostają przydatne w kontekście, w którym działa. Jednym ze sposobów, aby określić, czy czas na ponowne nauczenie modelu wykrywa dryfowanie danych. 

Ten przykład pokazuje, jak używać usługi Event Grid z aplikacją logiki platformy Azure, aby wyzwolić ponowne szkolenie. Przykład wyzwala potok Azure Data Factory, gdy nastąpi przekształcenie danych między uczeniem modelu i obsługującym zestawami.

Przed rozpoczęciem wykonaj następujące czynności:

* Konfigurowanie monitora zestawu danych w celu [wykrywania dryfu danych]( https://aka.ms/datadrift) w obszarze roboczym
* Utwórz [potok Azure Data Factory](https://docs.microsoft.com/azure/data-factory/)opublikowany.

W tym przykładzie prosty potok Data Factory jest używany do kopiowania plików do magazynu obiektów blob i uruchamiania potoku opublikowanego Machine Learning. Aby uzyskać więcej informacji na temat tego scenariusza, zobacz Jak skonfigurować [Machine Learning krok w Azure Data Factory](https://docs.microsoft.com/azure/data-factory/transform-data-machine-learning-service)

![ADF — mlpipeline — etap](./media/how-to-use-event-grid/adf-mlpipeline-stage.png)

1. Zacznij od utworzenia aplikacji logiki. Przejdź do [Azure Portal](https://portal.azure.com), wyszukaj pozycję Logic Apps, a następnie wybierz pozycję Utwórz.

    ![Search-Logic-App](./media/how-to-use-event-grid/search-for-logic-app.png)

1. Wprowadź żądane informacje. Aby uprościć środowisko pracy, Użyj tej samej subskrypcji i grupy zasobów co potok Azure Data Factory i Azure Machine Learning obszar roboczy.

    ![Konfiguracja usługi Logic-App-for-ADF](./media/how-to-use-event-grid/set-up-logic-app-for-adf.png)

1. Po utworzeniu aplikacji logiki wybierz opcję __gdy wystąpi zdarzenie zasobów Event Grid__. 

    ![SELECT-Event-Grid-Trigger](./media/how-to-use-event-grid/select-event-grid-trigger.png)

1. Zaloguj się i wprowadź szczegóły zdarzenia. W polu __nazwa zasobu__ Ustaw nazwę obszaru roboczego. Ustaw __Typ zdarzenia__ na __DatasetDriftDetected__.

    ![Logowanie i Dodawanie zdarzenia](./media/how-to-use-event-grid/login-and-add-event.png)

1. Dodaj nowy krok i Wyszukaj __Azure Data Factory__. Wybierz pozycję __Utwórz uruchomienie potoku__. 

    ![Create-adfpipeline-Run](./media/how-to-use-event-grid/create-adfpipeline-run.png)

1. Zaloguj się i określ opublikowany potok Azure Data Factory do uruchomienia.

    ![Określ-ADF — potok](./media/how-to-use-event-grid/specify-adf-pipeline.png)

1. Zapisz i Utwórz aplikację logiki przy użyciu przycisku **Zapisz** znajdującego się w lewym górnym rogu strony. Aby wyświetlić aplikację, przejdź do obszaru roboczego w [Azure Portal](https://portal.azure.com) i kliknij pozycję **zdarzenia**.

    ![show-Logic-App-webhook](./media/how-to-use-event-grid/show-logic-app-webhook.png)

Teraz potok fabryki danych jest wyzwalany, gdy nastąpi dryf. Wyświetl szczegóły dotyczące przebiegu dryfowania danych i potoku uczenia maszynowego w [nowym portalu obszaru roboczego](https://ml.azure.com). 

![Widok w obszarze roboczym](./media/how-to-use-event-grid/view-in-workspace.png)

## <a name="sample-deploy-a-model-based-on-tags"></a>Przykład: Wdrażanie modelu na podstawie tagów

Obiekt modelu Azure Machine Learning zawiera parametry, które można przestawiać wdrożenia, takie jak nazwa modelu, wersja, tag i właściwość. Zdarzenie rejestracji modelu może wyzwolić punkt końcowy i można użyć funkcji platformy Azure do wdrożenia modelu na podstawie wartości tych parametrów.

Aby zapoznać się z przykładem [https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid](https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid) , zobacz repozytorium i postępuj zgodnie z instrukcjami w pliku **README** .

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o dostępnych zdarzeniach, zobacz [schemat zdarzeń Azure Machine Learning](/azure/event-grid/event-schema-machine-learning)
