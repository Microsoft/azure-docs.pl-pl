---
title: Szkolenie i wdrażanie modeli z interfejsu wiersza polecenia
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak używać rozszerzenia uczenia maszynowego dla interfejsu wiersza polecenia platformy Azure do uczenia, rejestrowania i wdrażania modelu z wiersza polecenia.
ms.author: larryfr
author: Blackmist
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: 1cafc311c842cd5bc17fefe34eacbdfc99b7147a
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617727"
---
# <a name="tutorial-train-and-deploy-a-model-from-the-cli"></a>Samouczek: Trenuj i wdrażaj model z interfejsu wiersza polecenia
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym samouczku użyj rozszerzenie uczenia maszynowego dla interfejsu wiersza polecenia platformy Azure do szkolenia, rejestracji i wdrażania modelu.

Skrypty szkoleniowe języka Python w tym samouczku używają [scikit-learn](https://scikit-learn.org/) do uczenia podstawowego modelu. W centrum tego samouczka nie jest na skrypty lub modelu, ale proces przy użyciu interfejsu wiersza polecenia do pracy z usługi Azure Machine Learning.

Dowiedz się, jak wykonać następujące czynności:

> [!div class="checklist"]
> * Instalowanie rozszerzenia uczenia maszynowego
> * Tworzenie obszaru roboczego usługi Azure Machine Learning
> * Tworzenie zasobu obliczeniowego używanego do szkolenia modelu
> * Definiowanie i rejestrowanie zestawu danych używanego do trenowania modelu
> * Rozpoczynanie treningu
> * Zarejestruj się i pobierz model
> * Wdrażanie modelu jako usługi sieci web
> * Oceniaj dane za pomocą usługi internetowej

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję usługi Azure Machine Learning](https://aka.ms/AMLFree) już dziś.

* Aby użyć poleceń interfejsu wiersza polecenia w tym dokumencie ze **środowiska lokalnego,** potrzebujesz [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

    Jeśli używasz [usługi Azure Cloud Shell,](https://azure.microsoft.com//features/cloud-shell/)interfejs wiersza polecenia jest dostępny za pośrednictwem przeglądarki i mieszka w chmurze.

## <a name="download-the-example-project"></a>Pobierz przykładowy projekt

W tym samouczku [https://github.com/microsoft/MLOps](https://github.com/microsoft/MLOps) pobierz projekt. Pliki w `examples/cli-train-deploy` katalogu są używane przez kroki opisane w tym samouczku.

Aby uzyskać lokalną kopię plików, [pobierz archiwum .zip](https://github.com/microsoft/MLOps/archive/master.zip)lub użyj następującego polecenia Git, aby sklonować repozytorium:

```azurecli-interactive
git clone https://github.com/microsoft/MLOps.git
```

### <a name="training-files"></a>Pliki szkoleniowe

Katalog `examples/cli-train-deploy` z projektu zawiera następujące pliki, które są używane podczas szkolenia modelu:

* `.azureml\mnist.runconfig`: Uruchom plik __konfiguracyjny.__ Ten plik definiuje środowisko wykonawcze potrzebne do szkolenia modelu. W tym przykładzie również montuje dane używane do szkolenia modelu w środowisku szkoleniowym.
* `scripts\train.py`: Skrypt szkoleniowy. Ten plik trenuje model.
* `scripts\utils.py`: Plik pomocnika używany przez skrypt szkolenia.
* `.azureml\conda_dependencies.yml`: Definiuje zależności oprogramowania potrzebne do uruchomienia skryptu szkoleniowego.
* `dataset.json`: Definicja zestawu danych. Służy do rejestrowania zestawu danych MNIST w obszarze roboczym usługi Azure Machine Learning.

### <a name="deployment-files"></a>Pliki wdrażania

Repozytorium zawiera następujące pliki, które są używane do wdrażania uczonego modelu jako usługi sieci web:

* `aciDeploymentConfig.yml`: Plik __konfiguracji wdrożenia.__ Ten plik definiuje środowisko hostingu potrzebne dla modelu.
* `inferenceConfig.json`: Plik __konfiguracyjny wnioskowania.__ Ten plik definiuje środowisko oprogramowania używane przez usługę do oceniania danych za pomocą modelu.
* `score.py`: Skrypt języka python, który akceptuje przychodzące dane, ocenia go przy użyciu modelu, a następnie zwraca odpowiedź.
* `scoring-env.yml`: zależności conda potrzebne do uruchomienia `score.py` modelu i skryptu.
* `testdata.json`: Plik danych, który może służyć do testowania wdrożonej usługi sieci web.

## <a name="connect-to-your-azure-subscription"></a>Nawiązywanie połączenia z subskrypcją platformy Azure

Istnieje kilka sposobów, które można uwierzytelnić do subskrypcji platformy Azure z interfejsu wiersza polecenia. Najbardziej podstawowe jest interaktywne uwierzytelnienie za pomocą przeglądarki. Aby uwierzytelnić się interaktywnie, otwórz wiersz polecenia lub terminal i użyj następującego polecenia:

```azurecli-interactive
az login
```

Jeśli interfejs wiersza polecenia może otworzyć Twoją domyślną przeglądarkę, zrobi to i załaduje stronę logowania. W przeciwnym razie musisz otworzyć przeglądarkę i postępować zgodnie z instrukcjami w wierszu polecenia. Instrukcje obejmują przeglądanie [https://aka.ms/devicelogin](https://aka.ms/devicelogin) i wprowadzanie kodu autoryzacyjnego.

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

## <a name="install-the-machine-learning-extension"></a>Instalowanie rozszerzenia uczenia maszynowego

Aby zainstalować rozszerzenie uczenia maszynowego, użyj następującego polecenia:

```azurecli-interactive
az extension add -n azure-cli-ml
```

Jeśli zostanie wyświetlony komunikat, że rozszerzenie jest już zainstalowane, użyj następującego polecenia, aby zaktualizować do najnowszej wersji:

```azurecli-interactive
az extension update -n azure-cli-ml
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów jest podstawowym kontenerem zasobów na platformie Azure. Podczas pracy z usługą Azure Machine Learning grupa zasobów będzie zawierać obszar roboczy usługi Azure Machine Learning. Będzie również zawierać inne usługi platformy Azure używane przez obszar roboczy. Na przykład jeśli trenujesz model przy użyciu zasobu obliczeniowego opartego na chmurze, ten zasób jest tworzony w grupie zasobów.

Aby __utworzyć nową grupę zasobów,__ użyj następującego polecenia. Zamień `<resource-group-name>` na nazwę używana dla tej grupy zasobów. Zamień `<location>` region platformy Azure do użycia dla tej grupy zasobów:

> [!TIP]
> Należy wybrać region, w którym usługa Azure Machine Learning jest dostępna. Aby uzyskać więcej informacji, zobacz [Produkty dostępne według regionu](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service).

```azurecli-interactive
az group create --name <resource-group-name> --location <location>
```

Odpowiedź z tego polecenia jest podobna do następującej JSON:

```json
{
  "id": "/subscriptions/<subscription-GUID>/resourceGroups/<resourcegroupname>",
  "location": "<location>",
  "managedBy": null,
  "name": "<resource-group-name>",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": null
}
```

Aby uzyskać więcej informacji na temat pracy z grupami zasobów, zobacz [az group](https://docs.microsoft.com//cli/azure/group?view=azure-cli-latest).

## <a name="create-a-workspace"></a>Tworzenie obszaru roboczego

Aby utworzyć nowy obszar roboczy, użyj następującego polecenia. Zamień `<workspace-name>` na nazwę, której chcesz użyć dla tego obszaru roboczego. Zamień `<resource-group-name>` na nazwę grupy zasobów:

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name>
```

Dane wyjściowe tego polecenia są podobne do następującego JSON:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>",
  "containerRegistry": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.containerregistry/registries/<acr-name>",
  "creationTime": "2019-08-30T20:24:19.6984254+00:00",
  "description": "",
  "friendlyName": "<workspace-name>",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

## <a name="connect-local-project-to-workspace"></a>Łączenie projektu lokalnego z obszarem roboczym

W wierszu polecenia terminala lub polecenia użyj następujących `cli-train-deploy` poleceń zmień katalogi do katalogu, a następnie połącz się z obszarem roboczym:

```azurecli-interactive
cd ~/MLOps/examples/cli-train-deploy
az ml folder attach -w <workspace-name> -g <resource-group-name>
```

Dane wyjściowe tego polecenia są podobne do następującego JSON:

```json
{
  "Experiment name": "model-training",
  "Project path": "/home/user/MLOps/examples/cli-train-deploy",
  "Resource group": "<resource-group-name>",
  "Subscription id": "<subscription-id>",
  "Workspace name": "<workspace-name>"
}
```

To polecenie `.azureml/config.json` tworzy plik, który zawiera informacje potrzebne do nawiązania połączenia z obszarem roboczym. Pozostałe `az ml` polecenia użyte w tym samouczku będą używać tego pliku, więc nie trzeba dodawać obszaru roboczego i grupy zasobów do wszystkich poleceń.

## <a name="create-the-compute-target-for-training"></a>Tworzenie celu obliczeniowego dla szkolenia

W tym przykładzie użyto klastra obliczeniowego usługi Azure Machine Learning do uczenia modelu. Aby utworzyć nowy klaster obliczeniowy, użyj następującego polecenia:

```azurecli-interactive
az ml computetarget create amlcompute -n cpu-cluster --max-nodes 4 --vm-size Standard_D2_V2
```

Dane wyjściowe tego polecenia są podobne do następującego JSON:

```json
{
  "location": "<location>",
  "name": "cpu-cluster",
  "provisioningErrors": null,
  "provisioningState": "Succeeded"
}
```

To polecenie tworzy nowy obiekt `cpu-cluster`docelowy obliczeń o nazwie , z maksymalnie czterema węzłami. Wybrany rozmiar maszyny Wirtualnej zapewnia maszynę wirtualną z zasobem GPU. Aby uzyskać informacje na temat rozmiaru maszyny Wirtualnej, zobacz [Typy i rozmiary maszyn wirtualnych].

> [!IMPORTANT]
> Nazwa obiektu docelowego obliczeń (w`cpu-cluster` tym przypadku) jest ważna; odwołuje się do `.azureml/mnist.runconfig` niego plik użyty w następnej sekcji.

## <a name="define-the-dataset"></a>Definiowanie zestawu danych

Aby uszkoliwać model, można podać dane szkoleniowe przy użyciu zestawu danych. Aby utworzyć zestaw danych z interfejsu wiersza polecenia, należy podać plik definicji zestawu danych. Plik `dataset.json` podany w repozytorium tworzy nowy zestaw danych przy użyciu danych MNIST. Zestaw danych, który `mnist-dataset`tworzy, nosi nazwę .

Aby zarejestrować zestaw `dataset.json` danych przy użyciu pliku, użyj następującego polecenia:

```azurecli-interactive
az ml dataset register -f dataset.json --skip-validation
```

Dane wyjściowe tego polecenia są podobne do następującego JSON:

```json
{
  "definition": [
    "GetFiles"
  ],
  "registration": {
    "description": "mnist dataset",
    "id": "a13a4034-02d1-40bd-8107-b5d591a464b7",
    "name": "mnist-dataset",
    "tags": {
      "sample-tag": "mnist"
    },
    "version": 1,
    "workspace": "Workspace.create(name='myworkspace', subscription_id='mysubscriptionid', resource_group='myresourcegroup')"
  },
  "source": [
    "http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz",
    "http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz",
    "http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz",
    "http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz"
  ]
}
```

> [!IMPORTANT]
> Skopiuj `id` wartość wpisu, ponieważ jest używana w następnej sekcji.

Aby wyświetlić bardziej kompleksowy szablon zestawu danych, użyj następującego polecenia:

```azurecli-interactive
az ml dataset register --show-template
```

## <a name="reference-the-dataset"></a>Odwoływanie się do zestawu danych

Aby udostępnić zestaw danych w środowisku szkoleniowym, należy odwoływać się do niego z pliku runconfig. Plik `.azureml/mnist.runconfig` zawiera następujące wpisy YAML:

```yaml
# The arguments to the script file.
arguments:
- --data-folder
- DatasetConsumptionConfig:mnist

.....

# The configuration details for data.
data:
  mnist:
# Data Location
    dataLocation:
# the Dataset used for this run.
      dataset:
# Id of the dataset.
        id: a13a4034-02d1-40bd-8107-b5d591a464b7
# the DataPath used for this run.
      datapath:
# Whether to create new folder.
    createOutputDirectories: false
# The mode to handle
    mechanism: mount
# Point where the data is download or mount or upload.
    environmentVariableName: mnist
# relative path where the data is download or mount or upload.
    pathOnCompute:
# Whether to overwrite the data if existing.
    overwrite: false
```

Zmień wartość wpisu, `id` aby dopasować wartość zwróconą po zarejestrowaniu zestawu danych. Ta wartość jest używana do ładowania danych do obiektu docelowego obliczeń podczas szkolenia.

Ten YAML powoduje następujące działania podczas szkolenia:

* Montuje zestaw danych (na podstawie identyfikatora zestawu danych) w środowisku szkoleniowym i przechowuje ścieżkę do punktu instalacji w zmiennej środowiskowej. `mnist`
* Przekazuje lokalizację danych (punkt instalacji) wewnątrz środowiska szkoleniowego `--data-folder` do skryptu przy użyciu argumentu.

Plik runconfig zawiera również informacje używane do konfigurowania środowiska używanego przez przebieg szkolenia. Jeśli sprawdzisz ten plik, zobaczysz, że `cpu-compute` odwołuje się on do obiektu docelowego obliczeń utworzonego wcześniej. Zawiera również listę liczby węzłów do`"nodeCount": "4"`użycia podczas `"condaDependencies"` szkolenia ( ) i zawiera sekcję, która zawiera listę pakietów języka Python potrzebnych do uruchomienia skryptu szkoleniowego.

> [!TIP]
> Chociaż możliwe jest ręczne utworzenie pliku runconfig, ten w `generate-runconfig.py` tym przykładzie został utworzony przy użyciu pliku zawartego w repozytorium. Ten plik pobiera odwołanie do zarejestrowanego zestawu danych, tworzy plik konfiguracyjny, a następnie utrwala go do pliku.

Aby uzyskać więcej informacji na temat uruchamiania plików konfiguracyjnych, zobacz [Konfigurowanie i używanie obiektów docelowych obliczeń do szkolenia modelu](how-to-set-up-training-targets.md#create-run-configuration-and-submit-run-using-azure-machine-learning-cli). Aby uzyskać pełne odwołanie JSON, zobacz [plik runconfigschema.json](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json).

## <a name="submit-the-training-run"></a>Prześlij przebieg szkolenia

Aby rozpocząć szkolenie na `cpu-cluster` celu obliczeniowym, użyj następującego polecenia:

```azurecli-interactive
az ml run submit-script -c mnist -e myexperiment --source-directory scripts -t runoutput.json
```

To polecenie określa nazwę eksperymentu`myexperiment`( ). Eksperyment przechowuje informacje o tym uruchomieniu w obszarze roboczym.

Parametr `-c mnist` określa `.azureml/mnist.runconfig` plik.

Parametr `-t` przechowuje odwołanie do tego uruchomienia w pliku JSON i będzie używany w następnych krokach do rejestracji i pobierania modelu.

W miarę uruchamiania szkolenia przetwarza informacje z sesji szkoleniowej na zdalnym zasobie obliczeniowym. Część informacji jest podobna do następującego tekstu:

```output
Predict the test set
Accuracy is 0.9185
```

Ten tekst jest rejestrowany ze skryptu szkoleniowego i wyświetla dokładność modelu. Inne modele będą miały różne metryki wydajności.

Jeśli sprawdzisz skrypt szkolenia, zauważysz, że używa również wartości alfa, `outputs/sklearn_mnist_model.pkl`gdy przechowuje przeszkolony model do .

Model został zapisany `./outputs` w katalogu w miejscu docelowym obliczeń, w którym został przeszkolony. W takim przypadku wystąpienie obliczeń usługi Azure Machine Learning w chmurze platformy Azure. Proces szkolenia automatycznie przekazuje zawartość `./outputs` katalogu z obiektu docelowego obliczeń, w którym odbywa się szkolenie do obszaru roboczego usługi Azure Machine Learning. Jest przechowywany jako część eksperymentu`myexperiment` (w tym przykładzie).

## <a name="register-the-model"></a>Rejestrowanie modelu

Aby zarejestrować model bezpośrednio z zapisanej wersji w eksperymencie, użyj następującego polecenia:

```azurecli-interactive
az ml model register -n mymodel -f runoutput.json --asset-path "outputs/sklearn_mnist_model.pkl" -t registeredmodel.json
```

To polecenie rejestruje `outputs/sklearn_mnist_model.pkl` plik utworzony przez szkolenie uruchamiany `mymodel`jako nowa rejestracja modelu o nazwie . Odwołuje `--assets-path` się do ścieżki w eksperymencie. W takim przypadku informacje o eksperymencie i uruchomieniu są ładowane z `runoutput.json` pliku utworzonego przez polecenie szkolenia. Tworzy `-t registeredmodel.json` plik JSON, który odwołuje się do nowego zarejestrowanego modelu utworzonego przez to polecenie i jest używany przez inne polecenia interfejsu wiersza polecenia, które działają z zarejestrowanymi modelami.

Dane wyjściowe tego polecenia są podobne do następującego JSON:

```json
{
  "createdTime": "2019-09-19T15:25:32.411572+00:00",
  "description": "",
  "experimentName": "myexperiment",
  "framework": "Custom",
  "frameworkVersion": null,
  "id": "mymodel:1",
  "name": "mymodel",
  "properties": "",
  "runId": "myexperiment_1568906070_5874522d",
  "tags": "",
  "version": 1
}
```

### <a name="model-versioning"></a>Przechowywanie wersji modelu

Zwróć uwagę na numer wersji zwrócony dla modelu. Wersja jest zwiększana za każdym razem, gdy rejestrujesz nowy model o tej nazwie. Na przykład można pobrać model i zarejestrować go z pliku lokalnego za pomocą następujących poleceń:

```azurecli-interactive
az ml model download -i "mymodel:1" -t .
az ml model register -n mymodel -p "sklearn_mnist_model.pkl"
```

Pierwsze polecenie pobiera zarejestrowany model do bieżącego katalogu. Nazwa pliku `sklearn_mnist_model.pkl`jest , który jest plikiem, do którego odwołuje się podczas rejestracji modelu. Drugie polecenie rejestruje model lokalny`-p "sklearn_mnist_model.pkl"`( ) o tej samej nazwie co poprzednia rejestracja (`mymodel`). Tym razem zwrócone dane JSON wyświetla wersję jako 2.

## <a name="deploy-the-model"></a>Wdrażanie modelu

Aby wdrożyć model, użyj następującego polecenia:

```azurecli-interactive
az ml model deploy -n myservice -m "mymodel:1" --ic inferenceConfig.json --dc aciDeploymentConfig.yml
```

> [!NOTE]
> Może pojawić się ostrzeżenie o "Nie można sprawdzić istnienie LocalWebservice" lub "Nie można utworzyć klienta platformy Docker". Można bezpiecznie zignorować tę opcję, ponieważ nie wdrażasz lokalnej usługi sieci web.

To polecenie wdraża nową `myservice`usługę o nazwie , przy użyciu wersji 1 modelu, który został zarejestrowany wcześniej.

Plik `inferenceConfig.yml` zawiera informacje na temat sposobu korzystania z modelu do wnioskowania. Na przykład odwołuje się do`score.py`skryptu wpisu ( ) i zależności oprogramowania.

Aby uzyskać więcej informacji na temat struktury tego pliku, zobacz [schemat konfiguracji wnioskowania](reference-azure-machine-learning-cli.md#inference-configuration-schema). Aby uzyskać więcej informacji na temat skryptów wprowadzania, zobacz [Wdrażanie modeli za pomocą usługi Azure Machine Learning](how-to-deploy-and-where.md#prepare-to-deploy).

Opisuje `aciDeploymentConfig.yml` środowisko wdrażania używane do hostowania usługi. Konfiguracja wdrożenia jest specyficzna dla typu obliczeniowego używanego do wdrożenia. W takim przypadku jest używany wystąpienie kontenera platformy Azure. Aby uzyskać więcej informacji, zobacz [schemat konfiguracji wdrożenia](reference-azure-machine-learning-cli.md#deployment-configuration-schema).

Potrwa kilka minut przed zakończeniem procesu wdrażania.

> [!TIP]
> W tym przykładzie są używane wystąpienia kontenera platformy Azure. Wdrożenia w UA automatycznie tworzą potrzebny zasób ACI. Jeśli zamiast tego należy wdrożyć w usłudze Azure Kubernetes, należy utworzyć klaster AKS z wyprzedzeniem i określić go jako część `az ml model deploy` polecenia. Na przykład wdrażania w usłudze AKS zobacz [Wdrażanie modelu w klastrze usługi Azure Kubernetes.](how-to-deploy-azure-kubernetes-service.md)

Po kilku minutach zwracane są informacje podobne do następujących JSON:

```json
ACI service creation operation finished, operation "Succeeded"
{
  "computeType": "ACI",
  {...ommitted for space...}
  "runtimeType": null,
  "scoringUri": "http://6c061467-4e44-4f05-9db5-9f9a22ef7a5d.eastus2.azurecontainer.io/score",
  "state": "Healthy",
  "tags": "",
  "updatedAt": "2019-09-19T18:22:32.227401+00:00"
}
```

### <a name="the-scoring-uri"></a>Identyfikator URI oceniania

Zwrócony `scoringUri` z wdrożenia jest punktem końcowym REST dla modelu wdrożonego jako usługa sieci web. Ten identyfikator URI można również uzyskać za pomocą następującego polecenia:

```azurecli-interactive
az ml service show -n myservice
```

To polecenie zwraca ten sam dokument `scoringUri`JSON, łącznie z .

Punkt końcowy REST może służyć do wysyłania danych do usługi. Aby uzyskać informacje dotyczące tworzenia aplikacji klienckiej, która wysyła dane do usługi, zobacz [Korzystanie z modelu usługi Azure Machine Learning wdrożonego jako usługa sieci web](how-to-consume-web-service.md)

### <a name="send-data-to-the-service"></a>Wysyłanie danych do usługi

Chociaż można utworzyć aplikację kliencką do wywołania punktu końcowego, interfejs wiersza polecenia uczenia maszynowego zapewnia narzędzie, które może działać jako klient testowy. Użyj następującego polecenia, aby `testdata.json` wysłać dane w pliku do usługi:

```azurecli-interactive
az ml service run -n myservice -d @testdata.json
```

> [!TIP]
> Jeśli używasz programu PowerShell, użyj następującego polecenia:
>
> ```azurecli-interactive
> az ml service run -n myservice -d `@testdata.json
> ```

Odpowiedź z polecenia jest `[ 3 ]`podobna do .

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

> [!IMPORTANT]
> Utworzone zasoby mogą być używane jako wstępnie wymagane składniki w innych samouczkach usługi Azure Machine Learning i artykułach z instrukcjami.

### <a name="delete-deployed-service"></a>Usuwanie wdrożonej usługi

Jeśli planujesz nadal korzystać z obszaru roboczego usługi Azure Machine Learning, ale chcesz pozbyć się wdrożonej usługi w celu zmniejszenia kosztów, użyj następującego polecenia:

```azurecli-interactive
az ml service delete -n myservice
```

To polecenie zwraca dokument JSON zawierający nazwę usuniętej usługi. Może upłynąć kilka minut, zanim usługa zostanie usunięta.

### <a name="delete-the-training-compute"></a>Usuwanie obliczeń szkoleniowych

Jeśli planujesz nadal korzystać z obszaru roboczego usługi Azure `cpu-cluster` Machine Learning, ale chcesz pozbyć się celu obliczeniowego utworzonego dla szkolenia, użyj następującego polecenia:

```azurecli-interactive
az ml computetarget delete -n cpu-cluster
```

To polecenie zwraca dokument JSON, który zawiera identyfikator usuniętego obiektu docelowego obliczeń. Może upłynąć kilka minut, zanim obiekt docelowy obliczeń został usunięty.

### <a name="delete-everything"></a>Usuń wszystko

Jeśli nie zamierzasz korzystać z utworzonych zasobów, usuń je, aby nie ponosić dodatkowych opłat.

Aby usunąć grupę zasobów i wszystkie zasoby platformy Azure utworzone w tym dokumencie, należy użyć następującego polecenia. Zamień `<resource-group-name>` nazwę utworzonej wcześniej grupy zasobów:

```azurecli-interactive
az group delete -g <resource-group-name> -y
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku usługi Azure Machine Learning użyto interfejsu wiersza polecenia uczenia maszynowego dla następujących zadań:

> [!div class="checklist"]
> * Instalowanie rozszerzenia uczenia maszynowego
> * Tworzenie obszaru roboczego usługi Azure Machine Learning
> * Tworzenie zasobu obliczeniowego używanego do szkolenia modelu
> * Definiowanie i rejestrowanie zestawu danych używanego do trenowania modelu
> * Rozpoczynanie treningu
> * Zarejestruj się i pobierz model
> * Wdrażanie modelu jako usługi sieci web
> * Oceniaj dane za pomocą usługi internetowej

Aby uzyskać więcej informacji na temat korzystania z interfejsu wiersza polecenia, zobacz [Używanie rozszerzenia interfejsu wiersza polecenia dla usługi Azure Machine Learning](reference-azure-machine-learning-cli.md).
