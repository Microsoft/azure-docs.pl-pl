---
title: Wdrażanie modeli ml do aplikacji Azure Functions (wersja zapoznawcza)
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak używać Azure Machine Learning do pakowania i wdrażania modelu jako usługi sieci Web w aplikacji Azure Functions.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: vaidyas
author: vaidya-s
ms.reviewer: larryfr
ms.date: 03/06/2020
ms.topic: conceptual
ms.custom: how-to, racking-python, devx-track-azurecli
ms.openlocfilehash: 88408c4d1c1936d364029f73d9e7b2be0bcf9d84
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102213117"
---
# <a name="deploy-a-machine-learning-model-to-azure-functions-preview"></a>Wdróż model uczenia maszynowego w Azure Functions (wersja zapoznawcza)


Dowiedz się, jak wdrożyć model na podstawie Azure Machine Learning jako aplikacji funkcji w programie Azure Functions.

> [!IMPORTANT]
> Chociaż obie Azure Machine Learning i Azure Functions są ogólnie dostępne, możliwość spakowania modelu z usługi Machine Learning Service for Functions jest dostępna w wersji zapoznawczej.

Za pomocą Azure Machine Learning można tworzyć obrazy platformy Docker z przeszkolonych modeli uczenia maszynowego. Azure Machine Learning teraz zawiera funkcję wersji zapoznawczej umożliwiającą tworzenie tych modeli uczenia maszynowego w aplikacjach funkcji, które można [wdrożyć w Azure Functions](../azure-functions/functions-deployment-technologies.md#docker-container).

## <a name="prerequisites"></a>Wymagania wstępne

* Obszar roboczy usługi Azure Machine Learning. Aby uzyskać więcej informacji, zobacz artykuł [Tworzenie obszaru roboczego](how-to-manage-workspace.md) .
* [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).
* Model uczenia maszynowego zarejestrowany w Twoim obszarze roboczym. Jeśli nie masz modelu, Skorzystaj z [samouczka klasyfikacji obrazów: uczenie modelu](tutorial-train-models-with-aml.md) do uczenia i zarejestrowania go.

    > [!IMPORTANT]
    > W fragmentach kodu w tym artykule przyjęto założenie, że ustawiono następujące zmienne:
    >
    > * `ws` — Obszar roboczy Azure Machine Learning.
    > * `model` -Zarejestrowany model, który zostanie wdrożony.
    > * `inference_config` -Konfiguracja wnioskowania dla modelu.
    >
    > Aby uzyskać więcej informacji na temat ustawiania tych zmiennych, zobacz [Deploying Models with Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="prepare-for-deployment"></a>Przygotowanie do wdrożenia

Przed wdrożeniem należy zdefiniować, co jest potrzebne do uruchomienia modelu jako usługi sieci Web. Na poniższej liście opisano podstawowe elementy, które są związane z wdrożeniem:

* __Skrypt wejściowy__. Ten skrypt akceptuje żądania, ocenia żądanie przy użyciu modelu i zwraca wyniki.

    > [!IMPORTANT]
    > Skrypt wejścia jest specyficzny dla modelu; musi on zrozumieć format danych żądania przychodzącego, format danych oczekiwanych przez model i format danych zwracanych do klientów.
    >
    > Jeśli dane żądania są w formacie, którego nie można używać w modelu, skrypt może przekształcić go w akceptowalny format. Może również przekształcić odpowiedź przed powrotem do klienta programu.
    >
    > Domyślnie podczas tworzenia pakietów dla funkcji dane wejściowe są traktowane jako tekst. Jeśli interesuje się zużywanie nieprzetworzonych bajtów danych wejściowych (na przykład dla wyzwalaczy obiektów BLOB), należy użyć [AMLRequest do akceptowania danych pierwotnych](./how-to-deploy-advanced-entry-script.md#binary-data).

Aby uzyskać więcej informacji na temat skryptu wprowadzania, zobacz [Definiowanie oceniania kodu](./how-to-deploy-and-where.md#define-an-entry-script)

* **Zależności**, takie jak skrypty pomocnika lub pakiety Python/Conda wymagane do uruchomienia skryptu lub modelu wprowadzania

Te jednostki są hermetyzowane w __konfiguracji wnioskowania__. Konfiguracja wnioskowania przywołuje skrypt wejściowy i inne zależności.

> [!IMPORTANT]
> Podczas tworzenia konfiguracji wnioskowania do użytku z Azure Functions należy użyć obiektu [środowiska](/python/api/azureml-core/azureml.core.environment%28class%29?preserve-view=true&view=azure-ml-py) . Należy pamiętać, że w przypadku definiowania środowiska niestandardowego należy dodać polecenie "Azure-default" z wersją >= 1.0.45 jako zależność PIP. Ten pakiet zawiera funkcje, które są konieczne do hostowania modelu jako usługi sieci Web. Poniższy przykład ilustruje tworzenie obiektu środowiska i używanie go z konfiguracją wnioskowania:
>
> ```python
> from azureml.core.environment import Environment
> from azureml.core.conda_dependencies import CondaDependencies
>
> # Create an environment and add conda dependencies to it
> myenv = Environment(name="myenv")
> # Enable Docker based environment
> myenv.docker.enabled = True
> # Build conda dependencies
> myenv.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'],
>                                                            pip_packages=['azureml-defaults'])
> inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
> ```

Aby uzyskać więcej informacji o środowiskach, zobacz [Tworzenie środowisk i zarządzanie nimi na potrzeby szkolenia i wdrażania](how-to-use-environments.md).

Aby uzyskać więcej informacji na temat konfiguracji wnioskowania, zobacz [Wdrażanie modeli przy użyciu Azure Machine Learning](how-to-deploy-and-where.md).

> [!IMPORTANT]
> Podczas wdrażania w usłudze Functions nie trzeba tworzyć __konfiguracji wdrożenia__.

## <a name="install-the-sdk-preview-package-for-functions-support"></a>Zainstaluj pakiet SDK wersji zapoznawczej dla obsługi funkcji

Aby skompilować pakiety dla Azure Functions, należy zainstalować pakiet SDK w wersji zapoznawczej.

```bash
pip install azureml-contrib-functions
```

## <a name="create-the-image"></a>Tworzenie obrazu

Aby utworzyć obraz platformy Docker wdrożony w Azure Functions, Użyj usługi [Azure. contrib. Functions. Package](/python/api/azureml-contrib-functions/azureml.contrib.functions?preserve-view=true&view=azure-ml-py) lub określonej funkcji pakietu dla wyzwalacza, którego chcesz używać. Poniższy fragment kodu pokazuje, jak utworzyć nowy pakiet z wyzwalaczem obiektu BLOB z konfiguracji modelu i wnioskowania:

> [!NOTE]
> W fragmencie kodu założono `model` , że zawiera zarejestrowany model i `inference_config` zawiera konfigurację środowiska wnioskowania. Aby uzyskać więcej informacji, zobacz [Wdrażanie modeli przy użyciu Azure Machine Learning](how-to-deploy-and-where.md).

```python
from azureml.contrib.functions import package
from azureml.contrib.functions import BLOB_TRIGGER
blob = package(ws, [model], inference_config, functions_enabled=True, trigger=BLOB_TRIGGER, input_path="input/{blobname}.json", output_path="output/{blobname}_out.json")
blob.wait_for_creation(show_output=True)
# Display the package location/ACR path
print(blob.location)
```

Gdy `show_output=True` są wyświetlane dane wyjściowe procesu kompilacji platformy Docker. Po zakończeniu procesu obraz został utworzony w Azure Container Registry dla obszaru roboczego. Po skompilowaniu obrazu zostanie wyświetlona lokalizacja w Azure Container Registry. Zwrócona Lokalizacja ma format `<acrinstance>.azurecr.io/package@sha256:<imagename>` .

> [!NOTE]
> Pakowanie dla funkcji obecnie obsługuje wyzwalacze HTTP, wyzwalacze obiektów blob i wyzwalacze usługi Service Bus. Aby uzyskać więcej informacji na temat wyzwalaczy, zobacz [Azure Functions powiązania](../azure-functions/functions-bindings-storage-blob-trigger.md#blob-name-patterns).

> [!IMPORTANT]
> Zapisz informacje o lokalizacji, ponieważ są używane podczas wdrażania obrazu.

## <a name="deploy-image-as-a-web-app"></a>Wdrażanie obrazu jako aplikacji sieci Web

1. Użyj poniższego polecenia, aby uzyskać poświadczenia logowania dla Azure Container Registry zawierającej obraz. Zamień na `<myacr>` wartość zwróconą wcześniej z `blob.location` : 

    ```azurecli-interactive
    az acr credential show --name <myacr>
    ```

    Dane wyjściowe tego polecenia są podobne do następującego dokumentu JSON:

    ```json
    {
    "passwords": [
        {
        "name": "password",
        "value": "Iv0lRZQ9762LUJrFiffo3P4sWgk4q+nW"
        },
        {
        "name": "password2",
        "value": "=pKCxHatX96jeoYBWZLsPR6opszr==mg"
        }
    ],
    "username": "myml08024f78fd10"
    }
    ```

    Zapisz wartość dla __nazwy użytkownika__ i jednego z __haseł__.

1. Jeśli nie masz jeszcze grupy zasobów lub planu usługi App Service do wdrożenia usługi, następujące polecenia pokazują, jak utworzyć obie:

    ```azurecli-interactive
    az group create --name myresourcegroup --location "West Europe"
    az appservice plan create --name myplanname --resource-group myresourcegroup --sku B1 --is-linux
    ```

    W tym przykładzie jest używana _podstawowa_ warstwa cenowa systemu Linux ( `--sku B1` ).

    > [!IMPORTANT]
    > Obrazy utworzone przez Azure Machine Learning używają systemu Linux, dlatego należy użyć `--is-linux` parametru.

1. Utwórz konto magazynu, które ma być używane na potrzeby magazynu zadań sieci Web, i Pobierz jego parametry połączenia. Zamień na `<webjobStorage>` nazwę, której chcesz użyć.

    ```azurecli-interactive
    az storage account create --name <webjobStorage> --location westeurope --resource-group myresourcegroup --sku Standard_LRS
    ```
    ```azurecli-interactive
    az storage account show-connection-string --resource-group myresourcegroup --name <webJobStorage> --query connectionString --output tsv
    ```

1. Aby utworzyć aplikację funkcji, użyj następującego polecenia. Zamień na `<app-name>` nazwę, której chcesz użyć. Zamień `<acrinstance>` i `<imagename>` na wartości zwracane `package.location` wcześniej. Zamień `<webjobStorage>` na nazwę konta magazynu z poprzedniego kroku:

    ```azurecli-interactive
    az functionapp create --resource-group myresourcegroup --plan myplanname --name <app-name> --deployment-container-image-name <acrinstance>.azurecr.io/package:<imagename> --storage-account <webjobStorage>
    ```

    > [!IMPORTANT]
    > W tym momencie aplikacja funkcji została utworzona. Jednak ponieważ nie podano parametrów połączenia dla wyzwalacza obiektu BLOB lub poświadczeń do Azure Container Registry zawierającego obraz, aplikacja funkcji jest nieaktywna. W następnych krokach podano parametry połączenia oraz informacje o uwierzytelnianiu dla rejestru kontenerów. 

1. Utwórz konto magazynu, które ma być używane dla magazynu wyzwalacza obiektów blob, i Pobierz jego parametry połączenia. Zamień na `<triggerStorage>` nazwę, której chcesz użyć.

    ```azurecli-interactive
    az storage account create --name <triggerStorage> --location westeurope --resource-group myresourcegroup --sku Standard_LRS
    ```
    ```azurecli-interactive
    az storage account show-connection-string --resource-group myresourcegroup --name <triggerStorage> --query connectionString --output tsv
    ```
    Zarejestruj te parametry połączenia, aby udostępnić aplikację funkcji. Będziemy używać go później, gdy podasz `<triggerConnectionString>`

1. Utwórz kontenery dla danych wejściowych i wyjściowych na koncie magazynu. Zamień na `<triggerConnectionString>` ciąg połączenia zwrócony wcześniej:

    ```azurecli-interactive
    az storage container create -n input --connection-string <triggerConnectionString>
    ```
    ```azurecli-interactive
    az storage container create -n output --connection-string <triggerConnectionString>
    ```

1. Aby skojarzyć parametry połączenia wyzwalacza z aplikacją funkcji, użyj następującego polecenia. Zamień `<app-name>` na nazwę aplikacji funkcji. Zamień na `<triggerConnectionString>` ciąg połączenia zwrócony wcześniej:

    ```azurecli-interactive
    az functionapp config appsettings set --name <app-name> --resource-group myresourcegroup --settings "TriggerConnectionString=<triggerConnectionString>"
    ```
1. Musisz pobrać tag skojarzony z utworzonym kontenerem przy użyciu poniższego polecenia. Zamień na `<username>` nazwę użytkownika zwróconą wcześniej z rejestru kontenerów:

    ```azurecli-interactive
    az acr repository show-tags --repository package --name <username> --output tsv
    ```
    Zapisz zwracaną wartość, która zostanie użyta jako `imagetag` w następnym kroku.

1. Aby zapewnić aplikacji funkcji z poświadczeniami, które są potrzebne do uzyskania dostępu do rejestru kontenerów, użyj następującego polecenia. Zamień `<app-name>` na nazwę aplikacji funkcji. Zamień `<acrinstance>` i `<imagetag>` na wartości z polecenia AZ CLI Call w poprzednim kroku. Zamień `<username>` i na `<password>` pobrane wcześniej informacje logowania ACR:

    ```azurecli-interactive
    az functionapp config container set --name <app-name> --resource-group myresourcegroup --docker-custom-image-name <acrinstance>.azurecr.io/package:<imagetag> --docker-registry-server-url https://<acrinstance>.azurecr.io --docker-registry-server-user <username> --docker-registry-server-password <password>
    ```

    To polecenie zwraca informacje podobne do następującego dokumentu JSON:

    ```json
    [
    {
        "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
        "slotSetting": false,
        "value": "false"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_URL",
        "slotSetting": false,
        "value": "https://myml08024f78fd10.azurecr.io"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_USERNAME",
        "slotSetting": false,
        "value": "myml08024f78fd10"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
        "slotSetting": false,
        "value": null
    },
    {
        "name": "DOCKER_CUSTOM_IMAGE_NAME",
        "value": "DOCKER|myml08024f78fd10.azurecr.io/package:20190827195524"
    }
    ]
    ```

W tym momencie aplikacja funkcji rozpocznie ładowanie obrazu.

> [!IMPORTANT]
> Załadowanie obrazu może potrwać kilka minut. Postęp można monitorować przy użyciu witryny Azure Portal.

## <a name="test-the-deployment"></a>Testowanie wdrożenia

Po załadowaniu obrazu i udostępnieniu aplikacji wykonaj następujące kroki, aby wyzwolić aplikację:

1. Utwórz plik tekstowy, który zawiera dane, których oczekuje plik score.py. Poniższy przykład będzie działał z score.py, który oczekuje tablicy o wartości 10 liczb:

    ```json
    {"data": [[1, 2, 3, 4, 5, 6, 7, 8, 9, 10], [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]]}
    ```

    > [!IMPORTANT]
    > Format danych zależy od tego, czego oczekuje score.py i model.

2. Użyj poniższego polecenia, aby przekazać ten plik do kontenera wejściowego w utworzonym wcześniej obiekcie blob magazynu wyzwalacza. Zamień `<file>` na nazwę pliku zawierającego dane. Zamień na `<triggerConnectionString>` ciąg połączenia zwrócony wcześniej. W tym przykładzie `input` jest nazwą kontenera wejściowego utworzonego wcześniej. Jeśli użyto innej nazwy, Zastąp tę wartość:

    ```azurecli-interactive
    az storage blob upload --container-name input --file <file> --name <file> --connection-string <triggerConnectionString>
    ```

    Dane wyjściowe tego polecenia są podobne do następujących:

    ```json
    {
    "etag": "\"0x8D7C21528E08844\"",
    "lastModified": "2020-03-06T21:27:23+00:00"
    }
    ```

3. Aby wyświetlić dane wyjściowe wytwarzane przez funkcję, użyj następującego polecenia, aby wyświetlić listę generowanych plików wyjściowych. Zamień na `<triggerConnectionString>` ciąg połączenia zwrócony wcześniej. W tym przykładzie `output` jest nazwą kontenera wyjściowego utworzonego wcześniej. Jeśli użyto innej nazwy, Zastąp tę wartość:

    ```azurecli-interactive
    az storage blob list --container-name output --connection-string <triggerConnectionString> --query '[].name' --output tsv
    ```

    Dane wyjściowe tego polecenia są podobne do `sample_input_out.json` .

4. Aby pobrać plik i sprawdzić zawartość, użyj następującego polecenia. Zamień na `<file>` nazwę pliku zwróconego przez poprzednie polecenie. Zamień na `<triggerConnectionString>` ciąg połączenia zwrócony wcześniej: 

    ```azurecli-interactive
    az storage blob download --container-name output --file <file> --name <file> --connection-string <triggerConnectionString>
    ```

    Po zakończeniu wykonywania polecenia Otwórz plik. Zawiera dane zwrócone przez model.

Aby uzyskać więcej informacji na temat korzystania z wyzwalaczy obiektów blob, zobacz artykuł [Tworzenie funkcji wyzwalanej przez usługę Azure Blob Storage](../azure-functions/functions-create-storage-blob-triggered-function.md) .

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak skonfigurować aplikację funkcji w dokumentacji [funkcji](../azure-functions/functions-create-function-linux-custom-image.md) .
* Dowiedz się więcej o usłudze BLOB Storage wyzwala [powiązania usługi Azure Blob Storage](../azure-functions/functions-bindings-storage-blob.md).
* [Wdróż model do Azure App Service](how-to-deploy-app-service.md).
* [Korzystanie z modelu ML wdrożonego jako usługa sieci Web](how-to-consume-web-service.md)
* [Dokumentacja interfejsu API](/python/api/azureml-contrib-functions/azureml.contrib.functions?preserve-view=true&view=azure-ml-py)
