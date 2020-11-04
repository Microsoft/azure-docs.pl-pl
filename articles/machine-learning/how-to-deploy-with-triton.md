---
title: Wysoce wydajny model obsługujący Triton (wersja zapoznawcza)
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak wdrożyć model przy użyciu serwera Triton firmy NVIDIA w Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 09/23/2020
ms.topic: conceptual
ms.reviewer: larryfr
ms.custom: deploy
ms.openlocfilehash: afa1d958e054a769ea0f19b82afdf55a94c3d0cf
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93309706"
---
# <a name="high-performance-serving-with-triton-inference-server-preview"></a>Obsługa wysokiej wydajności przy użyciu serwera wnioskowania Triton (wersja zapoznawcza) 

Dowiedz się, jak używać [serwera wnioskowania NVIDIA Triton](https://developer.nvidia.com/nvidia-triton-inference-server) do ulepszania wydajności usługi sieci Web używanej do wnioskowania modeli.

Jednym z metod wdrażania modelu do wnioskowania jest usługa sieci Web. Na przykład wdrożenie w usłudze Azure Kubernetes Service lub Azure Container Instances. Domyślnie Azure Machine Learning korzysta z jednowątkowej platformy sieci Web *ogólnego przeznaczenia* dla wdrożeń usług sieci Web.

Triton to struktura, która jest *zoptymalizowana pod kątem wnioskowania*. Zapewnia lepsze wykorzystanie procesorów GPU i tańsze inciążanie. Po stronie serwera zwraca żądania przychodzące i przesyła te partie do wnioskowania. Przetwarzanie wsadowe zwiększa wykorzystanie zasobów procesora GPU i jest kluczowym elementem wydajności Triton.

> [!IMPORTANT]
> Korzystanie z programu Triton do wdrażania z Azure Machine Learning jest obecnie dostępne w __wersji zapoznawczej__. Funkcja wersji zapoznawczej może nie być objęta wsparciem klienta. Aby uzyskać więcej informacji, zobacz [dodatkowe warunki użytkowania wersji](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) zapoznawczych Microsoft Azure

> [!TIP]
> Fragmenty kodu w tym dokumencie są przeznaczone do celów ilustracyjnych i mogą nie wyświetlać kompletnego rozwiązania. Aby zapoznać się z przykładowym kodem, zobacz [kompleksowe przykłady Triton w Azure Machine Learning](https://github.com/Azure/azureml-examples/tree/main/tutorials).

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
* Żądania wnioskowania używają __identyfikatora URI oceniania__. Na przykład `https://myserevice.azureml.net/score`.

:::image type="content" source="./media/how-to-deploy-with-triton/normal-deploy.png" alt-text="Normalny, nietritonowy, diagram architektury wdrażania":::

**Wdrożenie konfiguracji wnioskowania z Triton**

* Wiele procesów roboczych [Gunicorn](https://gunicorn.org/) jest uruchomionych w celu współbieżnego obsłużenia żądań przychodzących.
* Żądania są przekazywane do **serwera Triton**. 
* Triton przetwarza żądania w partiach, aby zmaksymalizować wykorzystanie procesora GPU.
* Klient używa __identyfikatora URI oceniania__ , aby wykonać żądania. Na przykład `https://myserevice.azureml.net/score`.

:::image type="content" source="./media/how-to-deploy-with-triton/inferenceconfig-deploy.png" alt-text="Wdrażanie Inferenceconfig za pomocą Triton":::

Przepływ pracy, który ma być używany do wdrażania modelu Triton, to:

1. Sprawdź, czy Triton może obsłużyć Twój model.
1. Sprawdź, czy możesz wysyłać żądania do modelu wdrożonego Triton.
1. Uwzględnij kod specyficzny dla Triton we wdrożeniu AML.

## <a name="optional-define-a-model-config-file"></a>Obowiązkowe Zdefiniuj plik konfiguracji modelu

Plik konfiguracji modelu informuje Triton o liczbie danych wejściowych, których oczekuje, i o tym, jakie wymiary będą takie dane wejściowe. Aby uzyskać więcej informacji na temat tworzenia pliku konfiguracji, zobacz [Konfiguracja modelu](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/model_configuration.html) w dokumentacji firmy NVIDIA.

> [!TIP]
> Używamy `--strict-model-config=false` opcji podczas uruchamiania serwera wnioskowania Triton, co oznacza, że nie trzeba podawać `config.pbtxt` pliku dla modeli ONNX i TensorFlow.
> 
> Aby uzyskać więcej informacji na temat tej opcji, zobacz [Konfiguracja wygenerowanego modelu](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/model_configuration.html#generated-model-configuration) w dokumentacji firmy NVIDIA.

## <a name="directory-structure"></a>Struktura katalogów

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
> Ta struktura katalogów jest repozytorium modelu Triton i jest wymagana, aby modele działały z Triton. Aby uzyskać więcej informacji, zobacz [repozytoria modeli Triton](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/model_repository.html) w dokumentacji firmy NVIDIA.

## <a name="test-with-triton-and-docker"></a>Testowanie za pomocą Triton i platformy Docker

Aby przetestować model, aby upewnić się, że działa z Triton, możesz użyć platformy Docker. Następujące polecenia ściągają kontener Triton na komputer lokalny, a następnie uruchamiamy serwer Triton:

1. Aby ściągnąć obraz serwera Triton na komputer lokalny, użyj następującego polecenia:

    ```bash
    docker pull nvcr.io/nvidia/tritonserver:20.09-py3
    ```

1. Aby uruchomić serwer Triton, użyj następującego polecenia. Zamień na `<path-to-models/triton>` ścieżkę do repozytorium modelu Triton, które zawiera modele:

    ```bash
    docker run --rm -ti -v<path-to-models/triton>:/models nvcr.io/nvidia/tritonserver:20.09-py3 tritonserver --model-repository=/models --strict-model-config=false
    ```

    > [!IMPORTANT]
    > Jeśli używasz systemu Windows, może zostać wyświetlony monit o zezwolenie na połączenia sieciowe z tym procesem przy pierwszym uruchomieniu polecenia. Jeśli tak, wybierz opcję włączenia dostępu.

    Po uruchomieniu informacje podobne do następującego tekstu są rejestrowane w wierszu polecenia:

    ```bash
    I0923 19:21:30.582866 1 http_server.cc:2705] Started HTTPService at 0.0.0.0:8000
    I0923 19:21:30.626081 1 http_server.cc:2724] Started Metrics Service at 0.0.0.0:8002
    ```

    Pierwszy wiersz wskazuje adres usługi sieci Web. W tym przypadku, `0.0.0.0:8000` który jest taki sam jak `localhost:8000` .

1. Użyj narzędzia takiego jak zwinięcie, aby uzyskać dostęp do punktu końcowego kondycji.

    ```bash
    curl -L -v -i localhost:8000/v2/health/ready
    ```

    To polecenie zwraca informacje podobne do następujących. Należy pamiętać, że `200 OK` ten stan oznacza, że serwer sieci Web jest uruchomiony.

    ```bash
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

Poza podstawową kontrolę kondycji można utworzyć klienta w celu wysyłania danych do Triton na potrzeby wnioskowania. Aby uzyskać więcej informacji na temat tworzenia klienta, zapoznaj się z [przykładami klienta](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/client_example.html) w dokumentacji firmy NVIDIA. Istnieją także [przykłady języka Python w witrynie GitHub Triton](https://github.com/triton-inference-server/server/tree/master/src/clients/python/examples).

Aby uzyskać więcej informacji na temat uruchamiania Triton przy użyciu platformy Docker, zobacz [Uruchamianie Triton w systemie z procesorem GPU](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/run.html#running-triton-on-a-system-with-a-gpu) i [Uruchamianie Triton w systemie bez procesora GPU](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/run.html#running-triton-on-a-system-without-a-gpu).

## <a name="register-your-model"></a>Zarejestruj model

Po zweryfikowaniu, że model współpracuje z Triton, zarejestruj go przy użyciu Azure Machine Learning. Rejestracja modelu przechowuje pliki modeli w obszarze roboczym Azure Machine Learning i są używane podczas wdrażania przy użyciu zestawu SDK języka Python i interfejsu wiersza polecenia platformy Azure.

W poniższych przykładach pokazano, jak zarejestrować modele:

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core.model import Model

model = Model.register(
    model_path=os.path.join("..", "triton"),
    model_name="bidaf_onnx",
    tags={'area': "Natural language processing", 'type': "Question answering"},
    description="Question answering model from ONNX model zoo",
    workspace=ws
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli
az ml model register --model-path='triton' \
--name='bidaf_onnx' \
--workspace-name='<my_workspace>'
```
---

<a id="processing"></a>

## <a name="add-pre-and-post-processing"></a>Dodaj przed i po przetworzeniu

Po sprawdzeniu, czy usługa sieci Web działa, można dodać kod poprzedzający i przetworzony przez zdefiniowanie _skryptu wejścia_. Ten plik ma nazwę `score.py` . Aby uzyskać więcej informacji na temat skryptów wprowadzania, zobacz [Definiowanie skryptu wejścia](how-to-deploy-and-where.md#define-an-entry-script).

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

## <a name="redeploy-with-an-inference-configuration"></a>Wdróż ponownie z konfiguracją wnioskowania

Konfiguracja wnioskowania umożliwia używanie skryptu wejścia, a także procesu wdrażania Azure Machine Learning przy użyciu zestawu SDK języka Python lub interfejsu wiersza polecenia platformy Azure.

> [!IMPORTANT]
> Należy określić `AzureML-Triton` [środowisko nadzorowane](./resource-curated-environments.md).
>
> Przykład kodu w języku Python klonuje `AzureML-Triton` do innego środowiska o nazwie `My-Triton` . Kod interfejsu wiersza polecenia platformy Azure używa również tego środowiska. Aby uzyskać więcej informacji na temat klonowania środowiska, zobacz odwołanie do [środowiska. klonowania ()](/python/api/azureml-core/azureml.core.environment.environment?preserve-view=true&view=azure-ml-py#clone-new-name-) .

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

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

> [!TIP]
> Aby uzyskać więcej informacji na temat tworzenia konfiguracji wnioskowania, zobacz [Schemat konfiguracji wnioskowania](./reference-azure-machine-learning-cli.md#inference-configuration-schema).

```azurecli
az ml model deploy -n triton-densenet-onnx \
-m densenet_onnx:1 \
--ic inference-config.json \
-e My-Triton --dc deploymentconfig.json \
--overwrite --compute-target=aks-gpu
```

---

Po zakończeniu wdrożenia zostanie wyświetlony identyfikator URI oceniania. W przypadku tego wdrożenia lokalnego będzie to możliwe `http://localhost:6789/score` . W przypadku wdrożenia w chmurze można użyć polecenia [AZ ml Service show](/cli/azure/ext/azure-cli-ml/ml/service?view=azure-cli-latest#ext_azure_cli_ml_az_ml_service_show) CLI, aby uzyskać identyfikator URI oceniania.

Aby uzyskać informacje na temat sposobu tworzenia klienta wysyłającego żądania wnioskowania do identyfikatora URI oceniania, zobacz temat [Korzystanie z modelu wdrożonego jako usługa sieci Web](how-to-consume-web-service.md).

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli planujesz kontynuować korzystanie z obszaru roboczego Azure Machine Learning, ale chcesz pozbyć wdrożonej usługi, użyj jednej z następujących opcji:

# <a name="python"></a>[Python](#tab/python)

```python
local_service.delete()
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli
az ml service delete -n triton-densenet-onnx
```

---

## <a name="next-steps"></a>Następne kroki

* [Zobacz kompleksowe przykłady Triton w Azure Machine Learning](https://aka.ms/aml-triton-sample)
* Zapoznaj się z [przykładami klienta Triton](https://github.com/triton-inference-server/server/tree/master/src/clients/python/examples)
* Przeczytaj [dokumentację dotyczącą serwera wnioskowania Triton](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/index.html)
* [Rozwiązywanie problemów z niepowodzeniem wdrożenia](how-to-troubleshoot-deployment.md)
* [Wdrażanie w usłudze Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [Aktualizowanie usługi internetowej](how-to-deploy-update-web-service.md)
* [Zbieranie danych dla modeli w środowisku produkcyjnym](how-to-enable-data-collection.md)