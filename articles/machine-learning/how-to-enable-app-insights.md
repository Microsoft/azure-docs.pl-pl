---
title: Monitorowanie i zbieranie danych z punktów końcowych usługi sieci Web Machine Learning
titleSuffix: Azure Machine Learning
description: Monitorowanie usług sieci Web wdrożonych za pomocą Azure Machine Learning platformy Azure Application Insights
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: jmartens
ms.author: larryfr
author: blackmist
ms.date: 07/23/2020
ms.topic: conceptual
ms.custom: how-to, tracking-python
ms.openlocfilehash: e12c22d56399ce1690bee678623c58288cf0163b
ms.sourcegitcommit: 1b2d1755b2bf85f97b27e8fbec2ffc2fcd345120
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/04/2020
ms.locfileid: "87552207"
---
# <a name="monitor-and-collect-data-from-ml-web-service-endpoints"></a>Monitorowanie i zbieranie danych z punktów końcowych usługi sieci Web ML
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym artykule dowiesz się, jak zbierać dane i monitorować modele wdrożone w punktach końcowych usługi sieci Web w usłudze Azure Kubernetes Service (AKS) lub Azure Container Instances (ACI), badając dzienniki i włączając platformę Azure Application Insights za pomocą 
* [Azure Machine Learning Python SDK](#python)
* [Azure Machine Learning Studio](#studio) nahttps://ml.azure.com

Oprócz zbierania danych wyjściowych i odpowiedzi punktu końcowego można monitorować:

* Stawki żądania, czasy odpowiedzi i wskaźniki niepowodzeń
* Stawki zależności, czasy odpowiedzi i wskaźniki niepowodzeń
* Wyjątki

[Dowiedz się więcej o usłudze Azure Application Insights](../azure-monitor/app/app-insights-overview.md). 


## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree) dzisiaj

* Azure Machine Learning obszar roboczy, katalog lokalny, który zawiera skrypty, oraz zestaw Azure Machine Learning SDK dla języka Python. Aby dowiedzieć się, jak uzyskać te wymagania wstępne, zobacz [jak skonfigurować środowisko programistyczne](how-to-configure-environment.md) .

* Model uczenia maszynowego, który ma zostać wdrożony w usłudze Azure Kubernetes Service (AKS) lub Azure Container Instance (ACI). Jeśli go nie masz, zapoznaj się z samouczkiem dotyczącym [modelu klasyfikacji obrazów szkolenia](tutorial-train-models-with-aml.md)

## <a name="query-logs-for-deployed-models"></a>Dzienniki zapytań dla wdrożonych modeli

Aby pobrać dzienniki z wcześniej wdrożonej usługi sieci Web, Załaduj usługę i Użyj `get_logs()` funkcji. Dzienniki mogą zawierać szczegółowe informacje o błędach, które wystąpiły podczas wdrażania.

```python
from azureml.core.webservice import Webservice

# load existing web service
service = Webservice(name="service-name", workspace=ws)
logs = service.get_logs()
```

## <a name="web-service-metadata-and-response-data"></a>Metadane usługi sieci Web i dane odpowiedzi

> [!IMPORTANT]
> Usługa Azure Application Insights rejestruje tylko ładunki o rozmiarze do 64 KB. Po osiągnięciu tego limitu mogą pojawić się błędy, takie jak brak pamięci lub nie można rejestrować informacji.

Aby rejestrować informacje o żądaniu do usługi sieci Web, Dodaj `print` instrukcje do pliku Score.py. Każda `print` instrukcja powoduje jeden wpis w tabeli śledzenia w Application Insights, w obszarze komunikatu `STDOUT` . Zawartość `print` instrukcji zostanie zawarta w `customDimensions` , a następnie `Contents` w tabeli śledzenia. W przypadku drukowania ciągu JSON tworzy hierarchiczną strukturę danych w danych wyjściowych śledzenia poniżej `Contents` .

Możesz wysyłać zapytania do usługi Azure Application Insights bezpośrednio, aby uzyskać dostęp do tych danych, lub skonfigurować [ciągły eksport](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry) do konta magazynu w celu dłuższego przechowywania lub dalszego przetwarzania. Dane modelu można następnie użyć w Azure Machine Learning, aby skonfigurować etykietowanie, przeszkolenie, wyjaśnienie, analizę danych lub inne użycie. 


<a name="python"></a>

## <a name="use-python-sdk-to-configure"></a>Użyj zestawu SDK języka Python, aby skonfigurować 

### <a name="update-a-deployed-service"></a>Aktualizowanie wdrożonej usługi

1. Zidentyfikuj usługę w obszarze roboczym. Wartość dla `ws` jest nazwą obszaru roboczego

    ```python
    from azureml.core.webservice import Webservice
    aks_service= Webservice(ws, "my-service-name")
    ```
2. Aktualizowanie usługi i Włączanie usługi Azure Application Insights

    ```python
    aks_service.update(enable_app_insights=True)
    ```

### <a name="log-custom-traces-in-your-service"></a>Rejestruj niestandardowe ślady w usłudze

Jeśli chcesz rejestrować niestandardowe ślady, postępuj zgodnie ze standardowym procesem wdrażania dla AKS lub ACI w temacie [jak wdrożyć i gdzie](how-to-deploy-and-where.md) dokument. Następnie wykonaj następujące czynności:

1. Aby wysłać dane do Application Insights podczas wnioskowania, zaktualizuj plik oceniania przez dodanie instrukcji Print. Aby zalogować się do bardziej złożonych informacji, takich jak dane żądania i odpowiedź, możemy nam mieć strukturę JSON. Poniższy przykład pliku score.py rejestruje czas inicjowania modelu, dane wejściowe i wyjściowe podczas wnioskowania oraz czas wystąpienia jakichkolwiek błędów:

    > [!IMPORTANT]
    > Usługa Azure Application Insights rejestruje tylko ładunki o rozmiarze do 64 KB. Po osiągnięciu tego limitu mogą pojawić się błędy, takie jak brak pamięci lub nie można rejestrować informacji. Jeśli dane, które mają być rejestrowane, są większe niż 64 KB, należy je zapisać w usłudze BLOB Storage przy użyciu informacji w temacie [zbieranie danych dla modeli w środowisku produkcyjnym](how-to-enable-data-collection.md).
    
    ```python
    import pickle
    import json
    import numpy 
    from sklearn.externals import joblib
    from sklearn.linear_model import Ridge
    from azureml.core.model import Model
    import time

    def init():
        global model
        #Print statement for appinsights custom traces:
        print ("model initialized" + time.strftime("%H:%M:%S"))
        
        # note here "sklearn_regression_model.pkl" is the name of the model registered under the workspace
        # this call should return the path to the model.pkl file on the local disk.
        model_path = Model.get_model_path(model_name = 'sklearn_regression_model.pkl')
        
        # deserialize the model file back into a sklearn model
        model = joblib.load(model_path)
    

    # note you can pass in multiple rows for scoring
    def run(raw_data):
        try:
            data = json.loads(raw_data)['data']
            data = numpy.array(data)
            result = model.predict(data)
            # Log the input and output data to appinsights:
            info = {
                "input": raw_data,
                "output": result.tolist()
                }
            print(json.dumps(info))
            # you can return any datatype as long as it is JSON-serializable
            return result.tolist()
        except Exception as e:
            error = str(e)
            print (error + time.strftime("%H:%M:%S"))
            return error
    ```

2. Aktualizowanie konfiguracji usługi
    
    ```python
    config = Webservice.deploy_configuration(enable_app_insights=True)
    ```

3. Utwórz obraz i Wdróż go na [AKS lub ACI](how-to-deploy-and-where.md).

Aby uzyskać więcej informacji na temat rejestrowania i zbierania danych, zobacz [Włączanie rejestrowania w Azure Machine Learning](how-to-enable-logging.md) i [zbieranie danych z modeli w środowisku produkcyjnym](how-to-enable-data-collection.md).

### <a name="disable-tracking-in-python"></a>Wyłącz śledzenie w języku Python

Aby wyłączyć usługę Azure Application Insights, użyj następującego kodu:

```python 
## replace <service_name> with the name of the web service
<service_name>.update(enable_app_insights=False)
```

<a name="studio"></a>

## <a name="use-azure-machine-learning-studio-to-configure"></a>Użyj Azure Machine Learning Studio, aby skonfigurować

Możesz również włączyć Application Insights platformy Azure z poziomu programu Azure Machine Learning Studio, gdy wszystko będzie gotowe do wdrożenia modelu.

1. Zaloguj się do swojego obszaru roboczego whttps://ml.azure.com/
1. Przejdź do pozycji **modele** i wybierz model, który chcesz wdrożyć
1. Wybierz pozycję **+ Wdróż**
1. Wypełnij formularz **wdrażania modelu**
1. Rozwiń menu **Zaawansowane**

    ![Wdróż formularz](./media/how-to-enable-app-insights/deploy-form.png)
1. Wybierz pozycję **włącz Application Insights diagnostyki i zbierania danych**

    ![Włączanie usługi App Insights](./media/how-to-enable-app-insights/enable-app-insights.png)

## <a name="view-metrics-and-logs"></a>Wyświetlanie metryk i dzienników

Dane usługi są przechowywane na koncie usługi Azure Application Insights w ramach tej samej grupy zasobów co Azure Machine Learning.
Aby ją wyświetlić:

1. Przejdź do obszaru roboczego Azure Machine Learning w programie [Studio](https://ml.azure.com/).
1. Wybierz **punkty końcowe**.
1. Wybierz wdrożoną usługę.
1. Przewiń w dół, aby znaleźć **adres url Application Insights** i wybierz łącze.

    [![Lokalizowanie adresu URL Application Insights](./media/how-to-enable-app-insights/appinsightsloc.png)](././media/how-to-enable-app-insights/appinsightsloc.png#lightbox)

1. W Application Insights na karcie **Przegląd** lub w sekcji __monitorowanie__ na liście po lewej stronie wybierz pozycję __dzienniki__.

    [![Karta Omówienie monitorowania](./media/how-to-enable-app-insights/overview.png)](./media/how-to-enable-app-insights/overview.png#lightbox)

1. Aby wyświetlić informacje zarejestrowane z pliku score.py, zapoznaj się z tabelą __TRACES__ . Następujące zapytanie wyszukuje dzienniki, w których zarejestrowano wartość __wejściową__ :

    ```kusto
    traces
    | where customDimensions contains "input"
    | limit 10
    ```

   [![dane śledzenia](./media/how-to-enable-app-insights/model-data-trace.png)](././media/how-to-enable-app-insights/model-data-trace.png#lightbox)

Aby dowiedzieć się więcej na temat korzystania z usługi Azure Application Insights, zobacz [co to jest Application Insights?](../azure-monitor/app/app-insights-overview.md).

## <a name="export-data-for-further-processing-and-longer-retention"></a>Eksportowanie danych do dalszego przetwarzania i dłuższego przechowywania

>[!Important]
> Usługa Azure Application Insights obsługuje tylko eksporty do magazynu obiektów BLOB. Dodatkowe limity tej możliwości eksportu są wymienione w temacie [Eksportowanie danych telemetrycznych z usługi App Insights](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry#continuous-export-advanced-storage-configuration).

Aby wysyłać komunikaty do obsługiwanego konta magazynu, można użyć usługi Azure Application Insights " [eksport ciągły](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry) ", w której można ustawić dłuższe przechowywanie. Dane są przechowywane w formacie JSON i można je łatwo przeanalizować w celu wyodrębnienia danych modelu. 

Azure Data Factory, potoki Azure ML lub inne narzędzia do przetwarzania danych mogą służyć do przekształcania danych zgodnie z wymaganiami. Po przeniesieniu danych można zarejestrować je w obszarze roboczym Azure Machine Learning jako zestaw danych. Aby to zrobić, zobacz [jak utworzyć i zarejestrować zestawy danych](how-to-create-register-datasets.md).

:::image type="content" source="media/how-to-enable-app-insights/continuous-export-setup.png" alt-text="Eksport ciągły":::


## <a name="example-notebook"></a>Przykładowy notes

W notesie [enable-App-Insights-in-Product-Service. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-app-insights-in-production-service/enable-app-insights-in-production-service.ipynb) przedstawiono Koncepcje opisane w tym artykule. 
 
[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Następne kroki

* Zapoznaj się z artykułem [jak wdrożyć model w klastrze usługi Azure Kubernetes](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-azure-kubernetes-service) lub [jak wdrożyć model do Azure Container Instances](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-azure-container-instance) , aby wdrożyć modele w punktach końcowych usługi sieci Web, i włączyć usługę Azure Application Insights, aby korzystać z zbierania danych i monitorowania punktów końcowych.
* Zobacz [MLOps: zarządzanie, wdrażanie i monitorowanie modeli przy użyciu Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/concept-model-management-and-deployment) , aby dowiedzieć się więcej na temat wykorzystywania danych zebranych z modeli w środowisku produkcyjnym. Takie dane mogą pomóc w nieustannym ulepszaniu procesu uczenia maszynowego
