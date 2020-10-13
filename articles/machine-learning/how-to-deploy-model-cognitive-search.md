---
title: Wdrażanie modelu do użycia z usługą Cognitive Search
titleSuffix: Azure Machine Learning
description: W tym artykule przedstawiono sposób użycia Azure Machine Learning do wdrożenia modelu do użycia z usługą Azure Wyszukiwanie poznawcze. Wyszukiwanie poznawcze mogą używać modeli wdrożonych przez Azure Machine Learning jako umiejętność niestandardową w celu wzbogacania środowiska wyszukiwania.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: cgronlun
author: cjgronlund
ms.reviewer: larryfr
ms.date: 06/11/2020
ms.custom: deploy
ms.openlocfilehash: 5d198a27d7aeca679f1ee17f35d3f4384283f13c
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91998908"
---
# <a name="deploy-a-model-for-use-with-cognitive-search"></a>Wdrażanie modelu do użycia z usługą Cognitive Search


W tym artykule przedstawiono sposób użycia Azure Machine Learning do wdrożenia modelu do użycia z [usługą Azure wyszukiwanie poznawcze](../search/search-what-is-azure-search.md).

Wyszukiwanie poznawcze wykonuje przetwarzanie zawartości przez zawartość heterogenicznych, aby uczynić ją Queryable przez ludzi lub aplikacje. Ten proces można rozszerzyć przy użyciu modelu wdrożonego z Azure Machine Learning.

Azure Machine Learning można wdrożyć przeszkolony model jako usługę sieci Web. Usługa sieci Web jest następnie osadzona w Wyszukiwanie poznawczeej _umiejętności_, która wchodzi w skład potoku przetwarzania.

> [!IMPORTANT]
> Informacje zawarte w tym artykule dotyczą wdrażania modelu. Zawiera informacje o obsługiwanych konfiguracjach wdrożenia, które umożliwiają modelowi użycie przez Wyszukiwanie poznawcze.
>
> Aby uzyskać informacje na temat sposobu konfigurowania Wyszukiwanie poznawcze do korzystania ze wdrożonego modelu, zobacz samouczek [kompilacja i wdrażanie niestandardowej umiejętności przy użyciu Azure Machine Learning](../search/cognitive-search-tutorial-aml-custom-skill.md) .
>
> Aby zapoznać się z przykładem, na którym bazuje samouczek, zobacz [https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill) .

Podczas wdrażania modelu do użytku z usługą Azure Wyszukiwanie poznawcze wdrożenie musi spełniać następujące wymagania:

* Użyj usługi Azure Kubernetes Service, aby hostować model do wnioskowania.
* Włącz protokół TLS (Transport Layer Security) dla usługi Azure Kubernetes. Protokół TLS jest używany do zabezpieczania komunikacji przy użyciu protokołu HTTPS między Wyszukiwanie poznawcze i wdrożonym modelem.
* Skrypt wejścia musi używać `inference_schema` pakietu do wygenerowania schematu openapi (Swagger) dla usługi.
* Skrypt wejścia musi także przyjmować dane JSON jako dane wejściowe i generować kod JSON jako dane wyjściowe.


## <a name="prerequisites"></a>Wymagania wstępne

* Obszar roboczy usługi Azure Machine Learning. Aby uzyskać więcej informacji, zobacz [Tworzenie obszaru roboczego Azure Machine Learning](how-to-manage-workspace.md).

* Środowisko programistyczne języka Python z zainstalowanym zestawem SDK Azure Machine Learning. Aby uzyskać więcej informacji, zobacz [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true).  

* Zarejestrowany model. Jeśli nie masz modelu, użyj przykładowego notesu w [https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill) .

* Ogólne zrozumienie [, jak i gdzie należy wdrażać modele](how-to-deploy-and-where.md).

## <a name="connect-to-your-workspace"></a>Nawiązywanie połączenia z obszarem roboczym

Obszar roboczy Azure Machine Learning zapewnia scentralizowane miejsce do pracy ze wszystkimi artefaktami tworzonymi podczas korzystania z Azure Machine Learning. Obszar roboczy zachowuje historię wszystkich przebiegów szkoleniowych, w tym dzienników, metryk, danych wyjściowych i migawek skryptów.

Aby nawiązać połączenie z istniejącym obszarem roboczym, użyj następującego kodu:

> [!IMPORTANT]
> Ten fragment kodu oczekuje, że Konfiguracja obszaru roboczego ma zostać zapisana w bieżącym katalogu lub jego elemencie nadrzędnym. Aby uzyskać więcej informacji, zobacz [Tworzenie obszarów roboczych Azure Machine Learning i zarządzanie nimi](how-to-manage-workspace.md). Aby uzyskać więcej informacji na temat zapisywania konfiguracji do pliku, zobacz [Tworzenie pliku konfiguracji obszaru roboczego](how-to-configure-environment.md#workspace).

```python
from azureml.core import Workspace

try:
    # Load the workspace configuration from local cached inffo
    ws = Workspace.from_config()
    print(ws.name, ws.location, ws.resource_group, ws.location, sep='\t')
    print('Library configuration succeeded')
except:
    print('Workspace not found')
```

## <a name="create-a-kubernetes-cluster"></a>Tworzenie klastra Kubernetes

**Szacowany czas**: około 20 minut.

Klaster Kubernetes to zbiór wystąpień maszyn wirtualnych (nazywanych węzłami), które są używane do uruchamiania zwirtualizowanych aplikacji.

Podczas wdrażania modelu z Azure Machine Learning do usługi Azure Kubernetes, model i wszystkie zasoby potrzebne do hostowania go jako usługi sieci Web są spakowane w kontenerze platformy Docker. Ten kontener jest następnie wdrażany w klastrze.

Poniższy kod ilustruje sposób tworzenia nowego klastra usługi Azure Kubernetes Service (AKS) dla Twojego obszaru roboczego:

> [!TIP]
> Możesz również dołączyć istniejącą usługę Azure Kubernetes do obszaru roboczego Azure Machine Learning. Aby uzyskać więcej informacji, zobacz [jak wdrażać modele w usłudze Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md).

> [!IMPORTANT]
> Zwróć uwagę, że kod używa `enable_ssl()` metody do włączenia protokołu TLS (Transport Layer Security) dla klastra. Jest to wymagane, gdy planujesz używać wdrożonego modelu z Cognitive Services.

```python
from azureml.core.compute import AksCompute, ComputeTarget
# Create or attach to an AKS inferencing cluster

# Create the provisioning configuration with defaults
prov_config = AksCompute.provisioning_configuration()

# Enable TLS (sometimes called SSL) communications
# Leaf domain label generates a name using the formula
#  "<leaf-domain-label>######.<azure-region>.cloudapp.azure.net"
#  where "######" is a random series of characters
prov_config.enable_ssl(leaf_domain_label = "contoso")

cluster_name = 'amlskills'
# Try to use an existing compute target by that name.
# If one doesn't exist, create one.
try:
    
    aks_target = ComputeTarget(ws, cluster_name)
    print("Attaching to existing cluster")
except Exception as e:
    print("Creating new cluster")
    aks_target = ComputeTarget.create(workspace = ws, 
                                  name = cluster_name, 
                                  provisioning_configuration = prov_config)
    # Wait for the create process to complete
    aks_target.wait_for_completion(show_output = True)
```

> [!IMPORTANT]
> Na platformie Azure zostanie naliczona wartość, o ile istnieje klaster AKS. Upewnij się, że klaster AKS został usunięty po zakończeniu pracy z nim.

Aby uzyskać więcej informacji na temat korzystania z programu AKS z Azure Machine Learning, zobacz [jak wdrożyć usługę Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md).

## <a name="write-the-entry-script"></a>Napisz skrypt wpisu

Skrypt wejścia odbiera dane przesyłane do usługi sieci Web, przekazuje je do modelu i zwraca wyniki oceniania. Poniższy skrypt ładuje model przy uruchamianiu, a następnie używa modelu do oceny danych. Ten plik jest czasami wywoływany `score.py` .

> [!TIP]
> Skrypt wejściowy jest specyficzny dla modelu. Na przykład skrypt musi znać strukturę, która ma być używana z modelem, formatami danych itp.

> [!IMPORTANT]
> W przypadku planowania przy użyciu wdrożonego modelu z usługi Azure Cognitive Services należy użyć pakietu, `inference_schema` Aby włączyć generowanie schematu dla wdrożenia. Ten pakiet zawiera dekoratory, które umożliwiają definiowanie formatu danych wejściowych i wyjściowych dla usługi sieci Web, która wykonuje wnioskowanie przy użyciu modelu.

```python
from azureml.core.model import Model
from nlp_architect.models.absa.inference.inference import SentimentInference
from spacy.cli.download import download as spacy_download
import traceback
import json
# Inference schema for schema discovery
from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType
from inference_schema.parameter_types.standard_py_parameter_type import StandardPythonParameterType

def init():
    """
    Set up the ABSA model for Inference  
    """
    global SentInference
    spacy_download('en')
    aspect_lex = Model.get_model_path('hotel_aspect_lex')
    opinion_lex = Model.get_model_path('hotel_opinion_lex') 
    SentInference = SentimentInference(aspect_lex, opinion_lex)

# Use inference schema decorators and sample input/output to
# build the OpenAPI (Swagger) schema for the deployment
standard_sample_input = {'text': 'a sample input record containing some text' }
standard_sample_output = {"sentiment": {"sentence": "This place makes false booking prices, when you get there, they say they do not have the reservation for that day.", 
                                        "terms": [{"text": "hotels", "type": "AS", "polarity": "POS", "score": 1.0, "start": 300, "len": 6}, 
                                                  {"text": "nice", "type": "OP", "polarity": "POS", "score": 1.0, "start": 295, "len": 4}]}}
@input_schema('raw_data', StandardPythonParameterType(standard_sample_input))
@output_schema(StandardPythonParameterType(standard_sample_output))    
def run(raw_data):
    try:
        # Get the value of the 'text' field from the JSON input and perform inference
        input_txt = raw_data["text"]
        doc = SentInference.run(doc=input_txt)
        if doc is None:
            return None
        sentences = doc._sentences
        result = {"sentence": doc._doc_text}
        terms = []
        for sentence in sentences:
            for event in sentence._events:
                for x in event:
                    term = {"text": x._text, "type":x._type.value, "polarity": x._polarity.value, "score": x._score,"start": x._start,"len": x._len }
                    terms.append(term)
        result["terms"] = terms
        print("Success!")
        # Return the results to the client as a JSON document
        return {"sentiment": result}
    except Exception as e:
        result = str(e)
        # return error message back to the client
        print("Failure!")
        print(traceback.format_exc())
        return json.dumps({"error": result, "tb": traceback.format_exc()})
```

Aby uzyskać więcej informacji na temat skryptów wprowadzania, zobacz [jak i gdzie należy wdrożyć](how-to-deploy-and-where.md).

## <a name="define-the-software-environment"></a>Definiowanie środowiska oprogramowania

Klasa Environment służy do definiowania zależności języka Python dla usługi. Zawiera zależności wymagane przez model i skrypt wejścia. W tym przykładzie instaluje pakiety z regularnego indeksu PyPi oraz z repozytorium GitHub. 

```python
from azureml.core.conda_dependencies import CondaDependencies 
from azureml.core import Environment

conda = None
pip = ["azureml-defaults", "azureml-monitoring", 
       "git+https://github.com/NervanaSystems/nlp-architect.git@absa", 'nlp-architect', 'inference-schema',
       "spacy==2.0.18"]

conda_deps = CondaDependencies.create(conda_packages=None, pip_packages=pip)

myenv = Environment(name='myenv')
myenv.python.conda_dependencies = conda_deps
```

Aby uzyskać więcej informacji o środowiskach, zobacz [Tworzenie środowisk i zarządzanie nimi na potrzeby szkolenia i wdrażania](how-to-use-environments.md).

## <a name="define-the-deployment-configuration"></a>Definiowanie konfiguracji wdrożenia

Konfiguracja wdrożenia definiuje środowisko hostingu usługi Azure Kubernetes używane do uruchamiania usługi sieci Web.

> [!TIP]
> Jeśli nie masz pewności, jakie są wymagania dotyczące pamięci, procesora CPU lub procesora GPU, możesz skorzystać z profilowania, aby poznać te informacje. Aby uzyskać więcej informacji, zobacz [jak i gdzie wdrożyć model](how-to-deploy-and-where.md).

```python
from azureml.core.model import Model
from azureml.core.webservice import Webservice
from azureml.core.image import ContainerImage
from azureml.core.webservice import AksWebservice, Webservice

# If deploying to a cluster configured for dev/test, ensure that it was created with enough
# cores and memory to handle this deployment configuration. Note that memory is also used by
# things such as dependencies and AML components.

aks_config = AksWebservice.deploy_configuration(autoscale_enabled=True, 
                                                       autoscale_min_replicas=1, 
                                                       autoscale_max_replicas=3, 
                                                       autoscale_refresh_seconds=10, 
                                                       autoscale_target_utilization=70,
                                                       auth_enabled=True, 
                                                       cpu_cores=1, memory_gb=2, 
                                                       scoring_timeout_ms=5000, 
                                                       replica_max_concurrent_requests=2, 
                                                       max_request_wait_time=5000)
```

Aby uzyskać więcej informacji, zobacz dokumentację referencyjną [AksService.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py&preserve-view=true#&preserve-view=truedeploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none--compute-target-name-none-).

## <a name="define-the-inference-configuration"></a>Definiowanie konfiguracji wnioskowania

Konfiguracja wnioskowania wskazuje skrypt wejścia i obiekt środowiska:

```python
from azureml.core.model import InferenceConfig
inf_config = InferenceConfig(entry_script='score.py', environment=myenv)
```

Aby uzyskać więcej informacji, zobacz dokumentację referencyjną [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py&preserve-view=true).

## <a name="deploy-the-model"></a>Wdrażanie modelu

Wdróż model w klastrze AKS i poczekaj na utworzenie usługi. W tym przykładzie dwa zarejestrowane modele są ładowane z rejestru i wdrażane w usłudze AKS. Po wdrożeniu `score.py` plik we wdrożeniu ładuje te modele i używa ich do wykonywania wnioskowania.

```python
from azureml.core.webservice import AksWebservice, Webservice

c_aspect_lex = Model(ws, 'hotel_aspect_lex')
c_opinion_lex = Model(ws, 'hotel_opinion_lex') 
service_name = "hotel-absa-v2"

aks_service = Model.deploy(workspace=ws,
                           name=service_name,
                           models=[c_aspect_lex, c_opinion_lex],
                           inference_config=inf_config,
                           deployment_config=aks_config,
                           deployment_target=aks_target,
                           overwrite=True)

aks_service.wait_for_deployment(show_output = True)
print(aks_service.state)
```

Aby uzyskać więcej informacji, zobacz dokumentację referencyjną dla [modelu](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py&preserve-view=true).

## <a name="issue-a-sample-query-to-your-service"></a>Wydaj przykładowe zapytanie do usługi

Poniższy przykład używa informacji o wdrożeniu przechowywanych w `aks_service` zmiennej przez poprzednią sekcję kodu. Używa tej zmiennej do pobierania adresu URL oceniania i tokenu uwierzytelniania, który jest wymagany do komunikowania się z usługą:

```python
import requests
import json

primary, secondary = aks_service.get_keys()

# Test data
input_data = '{"raw_data": {"text": "This is a nice place for a relaxing evening out with friends. The owners seem pretty nice, too. I have been there a few times including last night. Recommend."}}'

# Since authentication was enabled for the deployment, set the authorization header.
headers = {'Content-Type':'application/json',  'Authorization':('Bearer '+ primary)} 

# Send the request and display the results
resp = requests.post(aks_service.scoring_uri, input_data, headers=headers)
print(resp.text)
```

Wynik zwrócony przez usługę jest podobny do następującego kodu JSON:

```json
{"sentiment": {"sentence": "This is a nice place for a relaxing evening out with friends. The owners seem pretty nice, too. I have been there a few times including last night. Recommend.", "terms": [{"text": "place", "type": "AS", "polarity": "POS", "score": 1.0, "start": 15, "len": 5}, {"text": "nice", "type": "OP", "polarity": "POS", "score": 1.0, "start": 10, "len": 4}]}}
```

## <a name="connect-to-cognitive-search"></a>Połącz z Wyszukiwanie poznawcze

Aby uzyskać informacje na temat korzystania z tego modelu z Wyszukiwanie poznawcze, zobacz samouczek [kompilacja i wdrażanie niestandardowej umiejętności przy użyciu Azure Machine Learning](../search/cognitive-search-tutorial-aml-custom-skill.md) .

## <a name="clean-up-the-resources"></a>Oczyszczanie zasobów

Jeśli klaster AKS został utworzony w ramach tego przykładu, Usuń zasoby po zakończeniu testowania przy użyciu Wyszukiwanie poznawcze.

> [!IMPORTANT]
> Platforma Azure jest rozliczana na podstawie tego, jak długo wdrożono klaster AKS. Pamiętaj, aby wyczyścić go po zakończeniu pracy z nim.

```python
aks_service.delete()
aks_target.delete()
```

## <a name="next-steps"></a>Następne kroki

* [Kompiluj i wdrażaj niestandardową umiejętność za pomocą Azure Machine Learning](../search/cognitive-search-tutorial-aml-custom-skill.md)
