---
title: Wysoce wydajny model obsługujący Triton (wersja zapoznawcza)
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak wdrożyć model przy użyciu serwera Triton firmy NVIDIA w Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 02/16/2020
ms.topic: conceptual
ms.reviewer: larryfr
ms.custom: deploy
ms.openlocfilehash: 3d2e01b645c1661d4b44520193b9c4557cbc1ea0
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2021
ms.locfileid: "100652178"
---
# <a name="high-performance-serving-with-triton-inference-server-preview"></a>Obsługa wysokiej wydajności przy użyciu serwera wnioskowania Triton (wersja zapoznawcza) 

Dowiedz się, jak używać [serwera wnioskowania NVIDIA Triton](https://aka.ms/nvidia-triton-docs) do ulepszania wydajności usługi sieci Web używanej do wnioskowania modeli.

Jednym z metod wdrażania modelu do wnioskowania jest usługa sieci Web. Na przykład wdrożenie w usłudze Azure Kubernetes Service lub Azure Container Instances. Domyślnie Azure Machine Learning korzysta z jednowątkowej platformy sieci Web *ogólnego przeznaczenia* dla wdrożeń usług sieci Web.

Triton to struktura, która jest *zoptymalizowana pod kątem wnioskowania*. Zapewnia lepsze wykorzystanie procesorów GPU i tańsze inciążanie. Po stronie serwera zwraca żądania przychodzące i przesyła te partie do wnioskowania. Przetwarzanie wsadowe zwiększa wykorzystanie zasobów procesora GPU i jest kluczowym elementem wydajności Triton.

> [!IMPORTANT]
> Korzystanie z programu Triton do wdrażania z Azure Machine Learning jest obecnie dostępne w __wersji zapoznawczej__. Funkcja wersji zapoznawczej może nie być objęta wsparciem klienta. Aby uzyskać więcej informacji, zobacz [dodatkowe warunki użytkowania wersji](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) zapoznawczych Microsoft Azure

> [!TIP]
> Fragmenty kodu w tym dokumencie są przeznaczone do celów ilustracyjnych i mogą nie wyświetlać kompletnego rozwiązania. Aby zapoznać się z przykładowym kodem, zobacz [kompleksowe przykłady Triton w Azure Machine Learning](https://aka.ms/triton-aml-sample).

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**. Jeśli go nie masz, wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree).
* Znajomość [sposobu i miejsca wdrożenia modelu](how-to-deploy-and-where.md) z Azure Machine Learning.
* [Zestaw Azure Machine Learning SDK dla języka Python](/python/api/overview/azure/ml/?view=azure-ml-py) **lub** [interfejs wiersza polecenia platformy Azure](/cli/azure/?view=azure-cli-latest) i [rozszerzenie uczenia maszynowego](reference-azure-machine-learning-cli.md).
* Działająca instalacja platformy Docker na potrzeby testowania lokalnego. Aby uzyskać informacje na temat instalowania i weryfikowania platformy Docker, zobacz [Orientacja i konfiguracja](https://docs.docker.com/get-started/) w dokumentacji platformy Docker.

## <a name="architectural-overview"></a>Omówienie architektury

Przed podjęciem próby użycia Triton dla własnego modelu ważne jest, aby zrozumieć, jak działa Azure Machine Learning i jak jest porównywane z domyślnym wdrożeniem.

**Wdrożenie domyślne bez Triton**

* Wiele procesów roboczych [Gunicorn](https://gunicorn.org/) jest uruchomionych w celu współbieżnego obsłużenia żądań przychodzących.
* Ci pracownicy obsługują wstępne przetwarzanie, wywoływanie modelu i przetwarzanie końcowe. 
* Klienci używają __identyfikatora URI oceniania usługi Azure ml__. Na przykład `https://myservice.azureml.net/score`.

:::image type="content" source="./media/how-to-deploy-with-triton/normal-deploy.png" alt-text="Normalny, nietritonowy, diagram architektury wdrażania":::

**Bezpośrednie wdrażanie za pomocą Triton**

* Żądania są kierowane bezpośrednio do serwera Triton.
* Triton przetwarza żądania w partiach, aby zmaksymalizować wykorzystanie procesora GPU.
* Klient używa __identyfikatora URI Triton__ , aby wykonywać żądania. Na przykład `https://myservice.azureml.net/v2/models/${MODEL_NAME}/versions/${MODEL_VERSION}/infer`.

:::image type="content" source="./media/how-to-deploy-with-triton/triton-deploy.png" alt-text="Inferenceconfig wdrażanie tylko z Triton i bez oprogramowania platformy Python":::

**Wdrożenie konfiguracji wnioskowania z Triton**

* Wiele procesów roboczych [Gunicorn](https://gunicorn.org/) jest uruchomionych w celu współbieżnego obsłużenia żądań przychodzących.
* Żądania są przekazywane do **serwera Triton**. 
* Triton przetwarza żądania w partiach, aby zmaksymalizować wykorzystanie procesora GPU.
* Klient używa __identyfikatora URI oceniania usługi Azure ml__ do tworzenia żądań. Na przykład `https://myservice.azureml.net/score`.

:::image type="content" source="./media/how-to-deploy-with-triton/inference-config-deploy.png" alt-text="Wdrażanie przy użyciu oprogramowania Triton i języka Python":::

Przepływ pracy, który ma być używany do wdrażania modelu Triton, to:

1. Bezpośrednio Obsługuj swój model za pomocą Triton.
1. Sprawdź, czy możesz wysyłać żądania do modelu wdrożonego Triton.
1. Obowiązkowe Tworzenie warstwy programów pośredniczących języka Python na potrzeby wstępnego i końcowego przetwarzania po stronie serwera

## <a name="deploying-triton-without-python-pre--and-post-processing"></a>Wdrażanie Triton bez wstępnego i przetwarzania końcowego języka Python

Najpierw postępuj zgodnie z poniższymi instrukcjami, aby sprawdzić, czy serwer wnioskowania Triton może obsłużyć Twój model.

### <a name="optional-define-a-model-config-file"></a>Obowiązkowe Zdefiniuj plik konfiguracji modelu

Plik konfiguracji modelu informuje Triton o liczbie danych wejściowych, których oczekuje, i o tym, jakie wymiary będą takie dane wejściowe. Aby uzyskać więcej informacji na temat tworzenia pliku konfiguracji, zobacz [Konfiguracja modelu](https://aka.ms/nvidia-triton-docs) w dokumentacji firmy NVIDIA.

> [!TIP]
> Używamy `--strict-model-config=false` opcji podczas uruchamiania serwera wnioskowania Triton, co oznacza, że nie trzeba podawać `config.pbtxt` pliku dla modeli ONNX i TensorFlow.
> 
> Aby uzyskać więcej informacji na temat tej opcji, zobacz [Konfiguracja wygenerowanego modelu](https://aka.ms/nvidia-triton-docs) w dokumentacji firmy NVIDIA.

### <a name="use-the-correct-directory-structure"></a>Użyj prawidłowej struktury katalogów

Podczas rejestrowania modelu z Azure Machine Learning można zarejestrować pojedyncze pliki lub strukturę katalogów. Aby można było korzystać z Triton, rejestracja modelu musi być dla struktury katalogów, która zawiera katalog o nazwie `triton` . Ogólna struktura tego katalogu:

```bash
models
    - triton
        - model_1
            - model_version
                - model_file
                - config_file
        - model_2
            ...
```

> [!IMPORTANT]
> Ta struktura katalogów jest repozytorium modelu Triton i jest wymagana, aby modele działały z Triton. Aby uzyskać więcej informacji, zobacz [repozytoria modeli Triton](https://aka.ms/nvidia-triton-docs) w dokumentacji firmy NVIDIA.

### <a name="register-your-triton-model"></a>Zarejestruj model Triton

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azcli)

```azurecli-interactive
az ml model register -n my_triton_model -p models --model-framework=Multi
```

Aby uzyskać więcej informacji na temat `az ml model register` , zapoznaj się z [dokumentacją referencyjną](/cli/azure/ext/azure-cli-ml/ml/model).

# <a name="python"></a>[Python](#tab/python)


```python

from azureml.core.model import Model

model_path = "models"

model = Model.register(
    model_path=model_path,
    model_name="bidaf-9-tutorial",
    tags={"area": "Natural language processing", "type": "Question-answering"},
    description="Question answering from ONNX model zoo",
    workspace=ws,
    model_framework=Model.Framework.MULTI,  # This line tells us you are registering a Triton model
)

```
Aby uzyskać więcej informacji, zobacz dokumentację [klasy model](/python/api/azureml-core/azureml.core.model.model?preserve-view=true&view=azure-ml-py).

---

### <a name="deploy-your-model"></a>Wdrażanie modelu

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azcli)

Jeśli masz klaster usługi Azure Kubernetes z obsługą procesora GPU o nazwie "AKS-GPU" utworzony za pomocą Azure Machine Learning, możesz użyć następującego polecenia, aby wdrożyć model.

```azurecli
az ml model deploy -n triton-webservice -m triton_model:1 --dc deploymentconfig.json --compute-target aks-gpu
```

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core.webservice import AksWebservice
from azureml.core.model import InferenceConfig
from random import randint

service_name = "triton-webservice"

config = AksWebservice.deploy_configuration(
    compute_target_name="aks-gpu",
    gpu_cores=1,
    cpu_cores=1,
    memory_gb=4,
    auth_enabled=True,
)

service = Model.deploy(
    workspace=ws,
    name=service_name,
    models=[model],
    deployment_config=config,
    overwrite=True,
)
```
---

Zapoznaj się [z tą dokumentacją, aby uzyskać więcej informacji na temat wdrażania modeli](how-to-deploy-and-where.md).

### <a name="call-into-your-deployed-model"></a>Zadzwoń do wdrożonego modelu

Najpierw Pobierz identyfikator URI oceniania i tokeny okaziciela.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azcli)


```azurecli
az ml service show --name=triton-webservice
```
# <a name="python"></a>[Python](#tab/python)

```python
import requests

print(service.scoring_uri)
print(service.get_keys())

```

---

Następnie upewnij się, że usługa jest uruchomiona, wykonując następujące czynności: 

```{bash}
!curl -v $scoring_uri/v2/health/ready -H 'Authorization: Bearer '"$service_key"''
```

To polecenie zwraca informacje podobne do następujących. Należy pamiętać, że `200 OK` ten stan oznacza, że serwer sieci Web jest uruchomiony.

```{bash}
*   Trying 127.0.0.1:8000...
* Connected to localhost (127.0.0.1) port 8000 (#0)
> GET /v2/health/ready HTTP/1.1
> Host: localhost:8000
> User-Agent: curl/7.71.1
> Accept: */*
>
* Mark bundle as not supporting multiuse
< HTTP/1.1 200 OK
HTTP/1.1 200 OK
```

Po sprawdzeniu kondycji można utworzyć klienta do wysyłania danych do Triton w celu wnioskowania. Aby uzyskać więcej informacji na temat tworzenia klienta, zapoznaj się z [przykładami klienta](https://aka.ms/nvidia-client-examples) w dokumentacji firmy NVIDIA. Istnieją także [przykłady języka Python w witrynie GitHub Triton](https://aka.ms/nvidia-triton-docs).

W tym momencie, jeśli nie chcesz dodawać wstępnego i końcowego przetwarzania w języku Python do wdrożonej usługi sieci Web, możesz to zrobić. Jeśli chcesz dodać tę logikę przed przetwarzaniem, przeczytaj artykuł.

## <a name="optional-re-deploy-with-a-python-entry-script-for-pre--and-post-processing"></a>Obowiązkowe Wdróż ponownie z skryptem wejścia w języku Python na potrzeby wstępnego i końcowego przetwarzania

Po sprawdzeniu, czy Triton jest w stanie obsłużyć Twój model, można dodać kod poprzedzający i wytworzony przez zdefiniowanie _skryptu wejścia_. Ten plik ma nazwę `score.py` . Aby uzyskać więcej informacji na temat skryptów wprowadzania, zobacz [Definiowanie skryptu wejścia](how-to-deploy-and-where.md#define-an-entry-script).

Dwa główne kroki polegają na zainicjowaniu klienta HTTP Triton w `init()` metodzie i wywołaniu tego klienta w `run()` funkcji.

### <a name="initialize-the-triton-client"></a>Inicjowanie klienta Triton

Dołącz kod podobny do następującego przykładu w `score.py` pliku. Triton w Azure Machine Learning oczekuje na adres localhost, port 8000. W tym przypadku localhost znajduje się w obrazie platformy Docker dla tego wdrożenia, a nie na komputerze lokalnym:

> [!TIP]
> `tritonhttpclient`Pakiet PIP jest uwzględniony w środowisku nadzorowanym `AzureML-Triton` , dlatego nie trzeba go określać jako zależności PIP.

```python
import tritonhttpclient

def init():
    global triton_client
    triton_client = tritonhttpclient.InferenceServerClient(url="localhost:8000")
```

### <a name="modify-your-scoring-script-to-call-into-triton"></a>Modyfikuj skrypt oceniania, aby wywołać Triton

Poniższy przykład ilustruje sposób dynamicznego żądania metadanych dla modelu:

> [!TIP]
> Można dynamicznie zażądać metadanych modeli, które zostały załadowane za pomocą Triton, za pomocą `.get_model_metadata` metody klienta Triton. Zapoznaj się z przykładowym [notesem](https://aka.ms/triton-aml-sample) .

```python
input = tritonhttpclient.InferInput(input_name, data.shape, datatype)
input.set_data_from_numpy(data, binary_data=binary_data)

output = tritonhttpclient.InferRequestedOutput(
         output_name, binary_data=binary_data, class_count=class_count)

# Run inference
res = triton_client.infer(model_name,
                          [input]
                          request_id='0',
                          outputs=[output])

```

<a id="redeploy"></a>

### <a name="redeploy-with-an-inference-configuration"></a>Wdróż ponownie z konfiguracją wnioskowania

Konfiguracja wnioskowania umożliwia używanie skryptu wejścia, a także procesu wdrażania Azure Machine Learning przy użyciu zestawu SDK języka Python lub interfejsu wiersza polecenia platformy Azure.

> [!IMPORTANT]
> Należy określić `AzureML-Triton` [środowisko nadzorowane](./resource-curated-environments.md).
>
> Przykład kodu w języku Python klonuje `AzureML-Triton` do innego środowiska o nazwie `My-Triton` . Kod interfejsu wiersza polecenia platformy Azure używa również tego środowiska. Aby uzyskać więcej informacji na temat klonowania środowiska, zobacz odwołanie do [środowiska. klonowania ()](/python/api/azureml-core/azureml.core.environment.environment?preserve-view=true&view=azure-ml-py#clone-new-name-) .

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azcli)

> [!TIP]
> Aby uzyskać więcej informacji na temat tworzenia konfiguracji wnioskowania, zobacz [Schemat konfiguracji wnioskowania](./reference-azure-machine-learning-cli.md#inference-configuration-schema).

```azurecli
az ml model deploy -n triton-densenet-onnx \
-m densenet_onnx:1 \
--ic inference-config.json \
-e My-Triton --dc deploymentconfig.json \
--overwrite --compute-target=aks-gpu
```

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core.webservice import LocalWebservice
from azureml.core import Environment
from azureml.core.model import InferenceConfig


local_service_name = "triton-bidaf-onnx"
env = Environment.get(ws, "AzureML-Triton").clone("My-Triton")

for pip_package in ["nltk"]:
    env.python.conda_dependencies.add_pip_package(pip_package)

inference_config = InferenceConfig(
    entry_script="score_bidaf.py",  # This entry script is where we dispatch a call to the Triton server
    source_directory=os.path.join("..", "scripts"),
    environment=env
)

local_config = LocalWebservice.deploy_configuration(
    port=6789
)

local_service = Model.deploy(
    workspace=ws,
    name=local_service_name,
    models=[model],
    inference_config=inference_config,
    deployment_config=local_config,
    overwrite=True)

local_service.wait_for_deployment(show_output = True)
print(local_service.state)
# Print the URI you can use to call the local deployment
print(local_service.scoring_uri)
```

---

Po zakończeniu wdrożenia zostanie wyświetlony identyfikator URI oceniania. W przypadku tego wdrożenia lokalnego będzie to możliwe `http://localhost:6789/score` . W przypadku wdrożenia w chmurze można użyć polecenia [AZ ml Service show](/cli/azure/ext/azure-cli-ml/ml/service?view=azure-cli-latest#ext_azure_cli_ml_az_ml_service_show) CLI, aby uzyskać identyfikator URI oceniania.

Aby uzyskać informacje na temat sposobu tworzenia klienta wysyłającego żądania wnioskowania do identyfikatora URI oceniania, zobacz temat [Korzystanie z modelu wdrożonego jako usługa sieci Web](how-to-consume-web-service.md).

### <a name="setting-the-number-of-workers"></a>Ustawianie liczby procesów roboczych

Aby ustawić liczbę procesów roboczych we wdrożeniu, należy ustawić zmienną środowiskową `WORKER_COUNT` . Jeśli masz obiekt [środowiska](/python/api/azureml-core/azureml.core.environment.environment?preserve-view=true&view=azure-ml-py) o nazwie `env` , możesz wykonać następujące czynności:

```{py}
env.environment_variables["WORKER_COUNT"] = "1"
```

Poinformuje to platformę Azure ML, aby obsłużyć liczbę określonych procesów roboczych.


## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli planujesz kontynuować korzystanie z obszaru roboczego Azure Machine Learning, ale chcesz pozbyć wdrożonej usługi, użyj jednej z następujących opcji:


# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azcli)

```azurecli
az ml service delete -n triton-densenet-onnx
```
# <a name="python"></a>[Python](#tab/python)

```python
local_service.delete()
```


---

## <a name="next-steps"></a>Następne kroki

* [Zobacz kompleksowe przykłady Triton w Azure Machine Learning](https://aka.ms/aml-triton-sample)
* Zapoznaj się z [przykładami klienta Triton](https://aka.ms/nvidia-client-examples)
* Przeczytaj [dokumentację dotyczącą serwera wnioskowania Triton](https://aka.ms/nvidia-triton-docs)
* [Rozwiązywanie problemów z niepowodzeniem wdrożenia](how-to-troubleshoot-deployment.md)
* [Wdrażanie w usłudze Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [Aktualizowanie usługi internetowej](how-to-deploy-update-web-service.md)
* [Zbieranie danych dla modeli w środowisku produkcyjnym](how-to-enable-data-collection.md)