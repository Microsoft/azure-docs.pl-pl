---
title: Model o wysokiej wydajności obsługujący triton (wersja zapoznawcza)
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak wdrożyć model za pomocą serwera NVIDIA Triton Inference Server Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 02/16/2020
ms.topic: conceptual
ms.reviewer: larryfr
ms.custom: deploy, devx-track-azurecli
ms.openlocfilehash: 9b7b8fe9c05d0de64dcd0cf7c6c324e0d03cb1ac
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874157"
---
# <a name="high-performance-serving-with-triton-inference-server-preview"></a>Obsługa o wysokiej wydajności za pomocą serwera Triton Inference Server (wersja zapoznawcza) 

Dowiedz się, jak używać [serwera wnioskowania NVIDIA Triton w](https://aka.ms/nvidia-triton-docs) celu zwiększenia wydajności usługi internetowej używanej do wnioskowania modelu.

Jednym ze sposobów wdrażania modelu do wnioskowania jest usługa internetowa. Na przykład wdrożenie w Azure Kubernetes Service lub Azure Container Instances. Domyślnie program Azure Machine Learning jednowątkową *platformę* sieci Web ogólnego przeznaczenia na potrzeby wdrożeń usług internetowych.

Triton to framework zoptymalizowany pod kątem *wnioskowania.* Zapewnia lepsze wykorzystanie procesorów GPU i bardziej ekonomiczne wnioskowanie. Po stronie serwera przesyła partie żądań przychodzących i przesyła je do wnioskowania. Przetwarzanie wsadowe lepiej wykorzystuje zasoby procesora GPU i jest kluczowym elementem wydajności tritona.

> [!IMPORTANT]
> Korzystanie z rozwiązania Triton do wdrażania z Azure Machine Learning jest obecnie dostępne w wersji __zapoznawczej__. Funkcje w wersji zapoznawczej mogą nie być objęte pomocą techniczną dla klientów. Aby uzyskać więcej informacji, zobacz Dodatkowe warunki użytkowania dla wersji [Microsoft Azure zapoznawczych](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

> [!TIP]
> Fragmenty kodu w tym dokumencie mają charakter ilustracyjny i mogą nie pokazywać kompletnego rozwiązania. Aby uzyskać przykładowy kod roboczy, zobacz [end-to-end samples of Triton in Azure Machine Learning](https://aka.ms/triton-aml-sample).

> [!NOTE]
> [NVIDIA Triton Inference Server](https://aka.ms/nvidia-triton-docs) to oprogramowanie innych firm typu open source zintegrowane z Azure Machine Learning.

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**. Jeśli go nie masz, wypróbuj bezpłatną [lub płatną](https://aka.ms/AMLFree)wersję Azure Machine Learning.
* Znajomość sposobu [i miejsca wdrażania modelu za pomocą](how-to-deploy-and-where.md) Azure Machine Learning.
* Zestaw [Azure Machine Learning SDK dla języka Python](/python/api/overview/azure/ml/) lub **interfejs** wiersza polecenia [platformy Azure](/cli/azure/) i rozszerzenie [uczenia maszynowego](reference-azure-machine-learning-cli.md).
* Robocza instalacja platformy Docker do testowania lokalnego. Aby uzyskać informacje na temat instalowania i sprawdzania poprawności platformy Docker, zobacz [Orientacja i konfiguracja w](https://docs.docker.com/get-started/) dokumentacji platformy Docker.

## <a name="architectural-overview"></a>Omówienie architektury

Przed podjęciem próby użycia rozwiązania Triton dla własnego modelu ważne jest, aby zrozumieć, jak działa on z Azure Machine Learning i jak wypada w porównaniu z wdrożeniem domyślnym.

**Wdrożenie domyślne bez trytonu**

* Wielu [pracowników serwera Gunicorn](https://gunicorn.org/) rozpoczyna współbieżną obsługę żądań przychodzących.
* Te pracownicy obsługują przetwarzanie wstępne, wywoływanie modelu i przetwarzanie po nim. 
* Klienci korzystają z __URI oceniania usługi Azure ML.__ Na przykład `https://myservice.azureml.net/score`.

:::image type="content" source="./media/how-to-deploy-with-triton/normal-deploy.png" alt-text="Diagram architektury wdrożenia normalny, nies tritonowy":::

**Bezpośrednie wdrażanie za pomocą rozwiązania Triton**

* Żądania są bezpośrednio trafiać do serwera Triton.
* Triton przetwarza żądania w partiach, aby zmaksymalizować wykorzystanie procesora GPU.
* Klient używa __trytonowego URI do__ obsługi żądań. Na przykład `https://myservice.azureml.net/v2/models/${MODEL_NAME}/versions/${MODEL_VERSION}/infer`.

:::image type="content" source="./media/how-to-deploy-with-triton/triton-deploy.png" alt-text="Wdrażanie inferenceconfig tylko przy użyciu tritonu i bez oprogramowania pośredniczącego Języka Python":::

**Wdrażanie konfiguracji wnioskowania za pomocą rozwiązania Triton**

* Wielu [pracowników serwera Gunicorn](https://gunicorn.org/) zaczyna współbieżnie obsługiwać żądania przychodzące.
* Żądania są przekazywane do serwera **Triton**. 
* Triton przetwarza żądania w partiach, aby zmaksymalizować wykorzystanie procesora GPU.
* Klient wysyła żądania przy __użyciu URI oceniania__ usługi Azure ML. Na przykład `https://myservice.azureml.net/score`.

:::image type="content" source="./media/how-to-deploy-with-triton/inference-config-deploy.png" alt-text="Wdrażanie za pomocą oprogramowania pośredniczącego Triton i Python":::

Przepływ pracy do użycia w trybie Triton dla wdrożenia modelu to:

1. Podawaj model bezpośrednio za pomocą tritonu.
1. Sprawdź, czy możesz wysyłać żądania do modelu wdrożonego w trytonie.
1. (Opcjonalnie) Tworzenie warstwy oprogramowania pośredniczącego Python do przetwarzania wstępnego i po stronie serwera

## <a name="deploying-triton-without-python-pre--and-post-processing"></a>Wdrażanie trytonu bez przetwarzania wstępnego i po przetwarzaniu w języku Python

Najpierw wykonaj poniższe kroki, aby sprawdzić, czy serwer Triton Inference Server może obsługiwać model.

### <a name="optional-define-a-model-config-file"></a>(Opcjonalnie) Definiowanie pliku konfiguracji modelu

Plik konfiguracji modelu informuje Trytona, ilu danych wejściowych oczekujemy, oraz o wymiarach tych danych wejściowych. Aby uzyskać więcej informacji na temat tworzenia pliku konfiguracji, zobacz [Konfiguracja modelu](https://aka.ms/nvidia-triton-docs) w dokumentacji firmy NVIDIA.

> [!TIP]
> Używamy opcji podczas uruchamiania serwera Triton Inference Server, co oznacza, że nie trzeba dostarczać pliku dla modeli `--strict-model-config=false` `config.pbtxt` ONNX lub TensorFlow.
> 
> Aby uzyskać więcej informacji na temat tej opcji, zobacz [Generated model configuration (Wygenerowana konfiguracja modelu)](https://aka.ms/nvidia-triton-docs) w dokumentacji firmy NVIDIA.

### <a name="use-the-correct-directory-structure"></a>Używanie prawidłowej struktury katalogów

Podczas rejestrowania modelu w usłudze Azure Machine Learning można zarejestrować pojedyncze pliki lub strukturę katalogów. Aby można było użyć tritonu, rejestracja modelu musi być dla struktury katalogów zawierającej katalog o nazwie `triton` . Ogólna struktura tego katalogu jest:

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
> Ta struktura katalogów jest repozytorium modelu Triton i jest wymagana do współpracy modeli z usługą Triton. Aby uzyskać więcej informacji, zobacz [Repozytoria modelu Triton w](https://aka.ms/nvidia-triton-docs) dokumentacji firmy NVIDIA.

### <a name="register-your-triton-model"></a>Rejestrowanie modelu Triton

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azcli)

```azurecli-interactive
az ml model register -n my_triton_model -p models --model-framework=Multi
```

Aby uzyskać więcej informacji na `az ml model register` temat programu , zapoznaj się z [dokumentacją referencyjną](/cli/azure/ml/model).

Podczas rejestrowania modelu w Azure Machine Learning, wartość parametru musi być nazwą folderu nadrzędnego `--model-path  -p` Triton.  
W powyższym przykładzie  `--model-path` jest to "models".

Wartość `--name  -n` parametru â€ ̃ my_triton_modelâ€™ w przykładzie będzie nazwą modelu znaną Obszar roboczy usługi Azure Machine Learning. 

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
Aby uzyskać więcej informacji, zobacz dokumentację klasy [Model](/python/api/azureml-core/azureml.core.model.model).

---

### <a name="deploy-your-model"></a>Wdrażanie modelu

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azcli)

Jeśli masz klaster usługi Azure Kubernetes Service z obsługą procesora GPU o nazwie "aks-gpu" utworzony za pomocą Azure Machine Learning, możesz użyć następującego polecenia, aby wdrożyć model.

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

Zapoznaj się [z tą dokumentacją, aby uzyskać więcej informacji na temat wdrażania modeli.](how-to-deploy-and-where.md)

### <a name="call-into-your-deployed-model"></a>Wywołanie wdrożonego modelu

Najpierw pobierz swój URI oceniania i tokeny bearer.

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

Następnie upewnij się, że usługa jest uruchomiona, wykonując: 

```{bash}
!curl -v $scoring_uri/v2/health/ready -H 'Authorization: Bearer '"$service_key"''
```

To polecenie zwraca informacje podobne do następujących. Zwróć uwagę `200 OK` na ; ten stan oznacza, że serwer internetowy jest uruchomiony.

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

Po wykonaniu kontroli kondycji możesz utworzyć klienta w celu wysyłania danych do tritonu w celu wnioskowania. Aby uzyskać więcej informacji na temat tworzenia klienta, zobacz [przykłady klientów](https://aka.ms/nvidia-client-examples) w dokumentacji firmy NVIDIA. Przykłady dla [języka Python można również pobrać w witrynie Triton GitHub.](https://aka.ms/nvidia-triton-docs)

Jeśli na tym etapie nie chcesz dodawać przetwarzania wstępnego i post w języku Python do wdrożonej usługi internetowej, możesz to zrobić. Jeśli chcesz dodać tę logikę przetwarzania wstępnego i po przetwarzaniu, przeczytaj dalej.

## <a name="optional-re-deploy-with-a-python-entry-script-for-pre--and-post-processing"></a>(Opcjonalnie) Ponowne wdrażanie za pomocą skryptu wprowadzania w języku Python do przetwarzania wstępnego i po nim

Po sprawdzeniu, czy triton jest w stanie obsługiwać _model,_ możesz dodać kod przed i po przetworzeniu, definiując skrypt wejściowy . Ten plik ma nazwę `score.py` . Aby uzyskać więcej informacji na temat skryptów wejściowych, [zobacz Define an entry script (Definiowanie skryptu wprowadzania).](how-to-deploy-and-where.md#define-an-entry-script)

Dwa główne kroki to zainicjowanie klienta HTTP Triton w metodzie i wywołanie do tego klienta `init()` w `run()` funkcji.

### <a name="initialize-the-triton-client"></a>Inicjowanie klienta Triton

Dołącz do pliku kod podobny do poniższego `score.py` przykładu. Tryton w Azure Machine Learning będzie adresowany na porcie localhost, 8000. W takim przypadku localhost znajduje się wewnątrz obrazu platformy Docker dla tego wdrożenia, a nie na porcie na komputerze lokalnym:

> [!TIP]
> Pakiet pip jest uwzględniony w środowisku z możliwością curated, więc nie trzeba go określać `tritonhttpclient` `AzureML-Triton` jako zależności pip.

```python
import tritonhttpclient

def init():
    global triton_client
    triton_client = tritonhttpclient.InferenceServerClient(url="localhost:8000")
```

### <a name="modify-your-scoring-script-to-call-into-triton"></a>Modyfikowanie skryptu oceniania w celu wywołania do tritona

W poniższym przykładzie pokazano, jak dynamicznie żądać metadanych dla modelu:

> [!TIP]
> Możesz dynamicznie żądać metadanych modeli, które zostały załadowane do aplikacji Triton, przy użyciu `.get_model_metadata` metody klienta Triton. Przykład użycia [można](https://aka.ms/triton-aml-sample) znaleźć w przykładowym notesie.

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

### <a name="redeploy-with-an-inference-configuration"></a>Ponowne wdychaj przy użyciu konfiguracji wnioskowania

Konfiguracja wnioskowania umożliwia użycie skryptu wejścia, a także procesu wdrażania Azure Machine Learning przy użyciu zestawu SDK języka Python lub interfejsu wiersza polecenia platformy Azure.

> [!IMPORTANT]
> Należy określić środowisko `AzureML-Triton` [wsadowe](./resource-curated-environments.md).
>
> Przykładowy kod w języku Python jest `AzureML-Triton` klonowany do innego środowiska o nazwie `My-Triton` . Kod interfejsu wiersza polecenia platformy Azure również używa tego środowiska. Aby uzyskać więcej informacji na temat klonowania środowiska, zobacz [environment.clone()](/python/api/azureml-core/azureml.core.environment.environment#clone-new-name-) reference (Informacje dotyczące klonowania środowiska).

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azcli)

> [!TIP]
> Aby uzyskać więcej informacji na temat tworzenia konfiguracji wnioskowania, zobacz [schemat konfiguracji wnioskowania](./reference-azure-machine-learning-cli.md#inference-configuration-schema).

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

Po zakończeniu wdrażania zostanie wyświetlony URI oceniania. W przypadku tego wdrożenia lokalnego będzie to `http://localhost:6789/score` . Jeśli wdrażasz w chmurze, możesz użyć polecenia [az ml service show](/cli/azure/ml/service#az_ml_service_show) CLI, aby uzyskać interfejs URI oceniania.

Aby uzyskać informacje na temat tworzenia klienta, który wysyła żądania wnioskowania do interfejsu URI oceniania, zobacz Consume a model deployed as a web service (Wykorzystanie [modelu wdrożonego jako usługa internetowa).](how-to-consume-web-service.md)

### <a name="setting-the-number-of-workers"></a>Ustawianie liczby pracowników

Aby ustawić liczbę pracowników we wdrożeniu, ustaw zmienną środowiskową `WORKER_COUNT` . Mając obiekt [Environment](/python/api/azureml-core/azureml.core.environment.environment) o nazwie `env` , możesz wykonać następujące czynności:

```{py}
env.environment_variables["WORKER_COUNT"] = "1"
```

Dzięki temu usługa Azure ML będzie w stanie rozsyłać liczbę pracowników, które określisz.


## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli planujesz dalsze korzystanie z Azure Machine Learning roboczego, ale chcesz pozbyć się wdrożonej usługi, użyj jednej z następujących opcji:


# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azcli)

```azurecli
az ml service delete -n triton-densenet-onnx
```
# <a name="python"></a>[Python](#tab/python)

```python
local_service.delete()
```


---
## <a name="troubleshoot"></a>Rozwiązywanie problemów

* [Rozwiązywanie problemów z nieudanym](how-to-troubleshoot-deployment.md)wdrożeniem . Dowiedz się, jak rozwiązywać typowe błędy, które mogą wystąpić podczas wdrażania modelu, oraz jak je rozwiązywać.

* Jeśli dzienniki wdrażania wskazują, że uruchomienie rozwiązania **TritonServer** nie powiodło się, zapoznaj się z dokumentacją firmy [Nvidiaâ€™s open source.](https://github.com/triton-inference-server/server)

## <a name="next-steps"></a>Następne kroki

* [Zobacz szczegółowe przykłady tritonu w Azure Machine Learning](https://aka.ms/aml-triton-sample)
* Zapoznaj się z [przykładami klientów Triton](https://aka.ms/nvidia-client-examples)
* Przeczytaj dokumentację [serwera Triton Inference Server](https://aka.ms/nvidia-triton-docs)
* [Wdrażanie w usłudze Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [Aktualizowanie usługi internetowej](how-to-deploy-update-web-service.md)
* [Zbieranie danych dla modeli w środowisku produkcyjnym](how-to-enable-data-collection.md)
