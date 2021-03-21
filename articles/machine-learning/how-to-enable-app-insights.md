---
title: Monitorowanie i zbieranie danych z punktów końcowych usługi sieci Web Machine Learning
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak zbierać dane z modeli wdrożonych w punktach końcowych usługi sieci Web w usłudze Azure Kubernetes Service (AKS) lub Azure Container Instances (ACI).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: larryfr
author: blackmist
ms.date: 09/15/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, data4ml
ms.openlocfilehash: 2740a86c5ff68e851d592533b48dc8ee60d817ee
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98070816"
---
# <a name="monitor-and-collect-data-from-ml-web-service-endpoints"></a>Monitorowanie i zbieranie danych z punktów końcowych usługi internetowej ML


W tym artykule dowiesz się, jak zbierać dane z modeli wdrożonych w punktach końcowych usługi sieci Web w usłudze Azure Kubernetes Service (AKS) lub Azure Container Instances (ACI). Użyj [usługi Azure Application Insights](../azure-monitor/app/app-insights-overview.md) , aby zebrać następujące dane z punktu końcowego:
* Dane wyjściowe
* Odpowiedzi
* Stawki żądania, czasy odpowiedzi i wskaźniki niepowodzeń
* Stawki zależności, czasy odpowiedzi i wskaźniki niepowodzeń
* Wyjątki

W notesie [enable-App-Insights-in-Product-Service. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-app-insights-in-production-service/enable-app-insights-in-production-service.ipynb) przedstawiono Koncepcje opisane w tym artykule.
 
[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]
 
## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree).

* Azure Machine Learning obszar roboczy, katalog lokalny, który zawiera skrypty, oraz zestaw Azure Machine Learning SDK dla języka Python. Aby dowiedzieć się więcej, zobacz [jak skonfigurować środowisko deweloperskie](how-to-configure-environment.md).

* Model uczenia maszynowego. Aby dowiedzieć się więcej, zobacz Samouczek dotyczący [modelu klasyfikacji obrazów szkolenia](tutorial-train-models-with-aml.md) .

<a name="python"></a>

## <a name="configure-logging-with-the-python-sdk"></a>Konfigurowanie rejestrowania za pomocą zestawu SDK języka Python

W tej sekcji dowiesz się, jak włączyć rejestrowanie w usłudze Application Insights przy użyciu zestawu SDK języka Python. 

### <a name="update-a-deployed-service"></a>Aktualizowanie wdrożonej usługi

Wykonaj następujące kroki, aby zaktualizować istniejącą usługę sieci Web:

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

> [!IMPORTANT]
> Usługa Azure Application Insights rejestruje tylko ładunki o rozmiarze do 64 KB. Po osiągnięciu tego limitu mogą pojawić się błędy, takie jak brak pamięci lub nie można rejestrować informacji. Jeśli dane, które mają być rejestrowane, są większe niż 64 KB, należy je zapisać w usłudze BLOB Storage przy użyciu informacji w temacie [zbieranie danych dla modeli w środowisku produkcyjnym](how-to-enable-data-collection.md).
>
> W przypadku bardziej złożonych sytuacji, takich jak śledzenie modelu w ramach wdrożenia AKS, zalecamy użycie biblioteki innej firmy, takiej jak [OpenCensus](https://opencensus.io).

Aby rejestrować niestandardowe ślady, postępuj zgodnie ze standardowym procesem wdrażania dla AKS lub ACI w temacie [jak wdrożyć i gdzie](how-to-deploy-and-where.md) dokument. Następnie wykonaj następujące czynności:

1. Zaktualizuj plik oceniania, dodając instrukcje Print, które wysyłają dane do Application Insights podczas wnioskowania. Aby uzyskać bardziej złożone informacje, takie jak dane żądania i odpowiedzi, należy użyć struktury JSON. 

    Poniższy przykładowy `score.py` plik rejestruje, kiedy model został zainicjowany, dane wejściowe i wyjściowe podczas wnioskowania oraz czas wystąpienia jakichkolwiek błędów.

    
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

2. Zaktualizuj konfigurację usługi i upewnij się, że Application Insights.
    
    ```python
    config = Webservice.deploy_configuration(enable_app_insights=True)
    ```

3. Utwórz obraz i Wdróż go na AKS lub ACI. Aby uzyskać więcej informacji, zobacz [jak wdrażać i gdzie](how-to-deploy-and-where.md).


### <a name="disable-tracking-in-python"></a>Wyłącz śledzenie w języku Python

Aby wyłączyć usługę Azure Application Insights, użyj następującego kodu:

```python 
## replace <service_name> with the name of the web service
<service_name>.update(enable_app_insights=False)
```

<a name="studio"></a>

## <a name="configure-logging-with-azure-machine-learning-studio"></a>Konfigurowanie rejestrowania w programie Azure Machine Learning Studio

Możesz również włączyć usługę Azure Application Insights w programie Azure Machine Learning Studio. Gdy wszystko jest gotowe do wdrożenia modelu jako usługi sieci Web, wykonaj następujące kroki, aby włączyć Application Insights:

1. Zaloguj się do programu Studio pod adresem https://ml.azure.com .
1. Przejdź do pozycji **modele** i wybierz model, który chcesz wdrożyć.
1. Wybierz pozycję  **+ Wdróż**.
1. Wypełnij formularz **Wdróż model** .
1. Rozwiń menu **Zaawansowane** .

    ![Wdróż formularz](./media/how-to-enable-app-insights/deploy-form.png)
1. Wybierz pozycję **włącz Application Insights diagnostyki i zbierania danych**.

    ![Włączanie usługi App Insights](./media/how-to-enable-app-insights/enable-app-insights.png)

## <a name="view-metrics-and-logs"></a>Wyświetlanie metryk i dzienników

### <a name="query-logs-for-deployed-models"></a>Dzienniki zapytań dla wdrożonych modeli

Dzienniki punktów końcowych w czasie rzeczywistym to dane klientów. Możesz użyć funkcji, `get_logs()` Aby pobrać dzienniki z wcześniej wdrożonej usługi sieci Web. Dzienniki mogą zawierać szczegółowe informacje o błędach, które wystąpiły podczas wdrażania.

```python
from azureml.core import Workspace
from azureml.core.webservice import Webservice

ws = Workspace.from_config()

# load existing web service
service = Webservice(name="service-name", workspace=ws)
logs = service.get_logs()
```

Jeśli masz wiele dzierżawców, może być konieczne dodanie następującego kodu uwierzytelniania przed `ws = Workspace.from_config()`

```python
from azureml.core.authentication import InteractiveLoginAuthentication
interactive_auth = InteractiveLoginAuthentication(tenant_id="the tenant_id in which your workspace resides")
```

### <a name="view-logs-in-the-studio"></a>Wyświetlanie dzienników w Studio

Usługa Azure Application Insights przechowuje dzienniki usługi w tej samej grupie zasobów co obszar roboczy Azure Machine Learning. Aby wyświetlić dane przy użyciu programu Studio, wykonaj następujące kroki:

1. Przejdź do obszaru roboczego Azure Machine Learning w programie [Studio](https://ml.azure.com/).
1. Wybierz **punkty końcowe**.
1. Wybierz wdrożoną usługę.
1. Wybierz łącze **Application Insights adresu URL** .

    [![Lokalizowanie adresu URL Application Insights](./media/how-to-enable-app-insights/appinsightsloc.png)](././media/how-to-enable-app-insights/appinsightsloc.png#lightbox)

1. W Application Insights na karcie **Przegląd** lub w sekcji __monitorowanie__ wybierz pozycję __dzienniki__.

    [![Karta Omówienie monitorowania](./media/how-to-enable-app-insights/overview.png)](./media/how-to-enable-app-insights/overview.png#lightbox)

1. Aby wyświetlić informacje zarejestrowane z pliku score.py, zapoznaj się z tabelą __TRACES__ . Następujące zapytanie wyszukuje dzienniki, w których zarejestrowano wartość __wejściową__ :

    ```kusto
    traces
    | where customDimensions contains "input"
    | limit 10
    ```

   [![dane śledzenia](./media/how-to-enable-app-insights/model-data-trace.png)](././media/how-to-enable-app-insights/model-data-trace.png#lightbox)

Aby uzyskać więcej informacji na temat korzystania z usługi Azure Application Insights, zobacz [co to jest Application Insights?](../azure-monitor/app/app-insights-overview.md).

## <a name="web-service-metadata-and-response-data"></a>Metadane usługi sieci Web i dane odpowiedzi

> [!IMPORTANT]
> Usługa Azure Application Insights rejestruje tylko ładunki o rozmiarze do 64 KB. Po osiągnięciu tego limitu mogą pojawić się błędy, takie jak brak pamięci lub nie można rejestrować informacji.

Aby rejestrować informacje o żądaniu usługi sieci Web, Dodaj `print` instrukcje do pliku Score.py. Każda `print` instrukcja powoduje jeden wpis w tabeli śledzenia Application Insights w komunikacie `STDOUT` . Application Insights przechowuje dane `print` wyjściowe instrukcji w  `customDimensions` i w `Contents` tabeli śledzenia. Drukowanie ciągów JSON tworzy hierarchiczną strukturę danych w danych wyjściowych śledzenia poniżej `Contents` .

## <a name="export-data-for-retention-and-processing"></a>Eksportowanie danych do przechowywania i przetwarzania

>[!Important]
> Usługa Azure Application Insights obsługuje tylko eksporty do magazynu obiektów BLOB. Aby uzyskać więcej informacji na temat limitów tej implementacji, zobacz [Eksportowanie danych telemetrycznych z usługi App Insights](../azure-monitor/app/export-telemetry.md#continuous-export-advanced-storage-configuration).

Użyj Application Insights " [eksportu ciągłego](../azure-monitor/app/export-telemetry.md) ", aby wyeksportować dane do konta magazynu obiektów blob, w którym można zdefiniować ustawienia przechowywania. Application Insights eksportuje dane w formacie JSON. 

:::image type="content" source="media/how-to-enable-app-insights/continuous-export-setup.png" alt-text="Eksport ciągły":::

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób włączania rejestrowania i wyświetlania dzienników dla punktów końcowych usługi sieci Web. Wypróbuj następujące artykuły, aby wykonać kolejne kroki:


* [Jak wdrożyć model w klastrze AKS](./how-to-deploy-azure-kubernetes-service.md)

* [Jak wdrożyć model do Azure Container Instances](./how-to-deploy-azure-container-instance.md)

* [MLOps: Zarządzaj i wdrażaj modele oraz monitoruj je za pomocą Azure Machine Learning](./concept-model-management-and-deployment.md) , aby dowiedzieć się więcej o korzystaniu z danych zebranych z modeli w środowisku produkcyjnym. Takie dane mogą pomóc w nieustannym ulepszaniu procesu uczenia maszynowego.