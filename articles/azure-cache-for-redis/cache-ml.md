---
title: Wdróż model uczenia maszynowego w celu Azure Functions z użyciem usługi Azure cache for Redis
description: W tym artykule zostanie wdrożony model z Azure Machine Learning jako aplikacja funkcji w Azure Functions za pomocą usługi Azure cache for Redis. Usługa Azure cache for Redis jest niezwykle wydajna i skalowalna — w przypadku sparowania z modelem Azure Machine Learning można uzyskać małe opóźnienia i wysoką przepływność w aplikacji.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 09/30/2020
ms.openlocfilehash: ec8943bc73cac2020350dd4916f040f031cd842b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "102499700"
---
# <a name="deploy-a-machine-learning-model-to-azure-functions-with-azure-cache-for-redis"></a>Wdróż model uczenia maszynowego w celu Azure Functions z użyciem usługi Azure cache for Redis 

W tym artykule zostanie wdrożony model z Azure Machine Learning jako aplikacja funkcji w Azure Functions za pomocą usługi Azure cache for Redis.  

Usługa Azure cache for Redis jest niezwykle wydajna i skalowalna — w przypadku sparowania z modelem Azure Machine Learning można uzyskać małe opóźnienia i wysoką przepływność w aplikacji. Kilka scenariuszy, w których pamięć podręczna jest szczególnie przydatna, jest inferencing danych i rzeczywistymi wynikami wnioskowania modelu. W każdym scenariuszu meta dane lub wyniki są przechowywane w pamięci, co prowadzi do zwiększenia wydajności. 

> [!NOTE]
> Chociaż obie Azure Machine Learning i Azure Functions są ogólnie dostępne, możliwość spakowania modelu z usługi Machine Learning Service for Functions jest dostępna w wersji zapoznawczej.  
>

## <a name="prerequisites"></a>Wymagania wstępne
* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/).
* Obszar roboczy usługi Azure Machine Learning. Aby uzyskać więcej informacji, zobacz artykuł [Tworzenie obszaru roboczego](../machine-learning/how-to-manage-workspace.md) .
* [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).
* Model uczenia maszynowego zarejestrowany w Twoim obszarze roboczym. Jeśli nie masz modelu, Skorzystaj z [samouczka klasyfikacji obrazów: uczenie modelu](../machine-learning/tutorial-train-models-with-aml.md) do uczenia i zarejestrowania go.

> [!IMPORTANT]
> W fragmentach kodu w tym artykule przyjęto założenie, że ustawiono następujące zmienne:
>
> * `ws` — Obszar roboczy Azure Machine Learning.
> * `model` -Zarejestrowany model, który zostanie wdrożony.
> * `inference_config` -Konfiguracja wnioskowania dla modelu.
>
> Aby uzyskać więcej informacji na temat ustawiania tych zmiennych, zobacz [Deploying Models with Azure Machine Learning](../machine-learning/how-to-deploy-and-where.md).

## <a name="create-an-azure-cache-for-redis-instance"></a>Tworzenie wystąpienia pamięci podręcznej Azure Cache for Redis 
Będzie można wdrożyć model uczenia maszynowego w celu Azure Functions z dowolnym wystąpieniem pamięci podręcznej Basic, Standard lub Premium. Aby utworzyć wystąpienie pamięci podręcznej, wykonaj następujące kroki.  

1. Przejdź do strony głównej Azure Portal lub Otwórz menu paska bocznego, a następnie wybierz pozycję **Utwórz zasób**. 
   
1. Na stronie **Nowy** wybierz pozycję **bazy danych** , a następnie wybierz pozycję **Azure cache for Redis**.

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="Wybierz pozycję Pamięć podręczna platformy Azure dla Redis.":::
   
1. Na stronie **nowy Redis Cache** Skonfiguruj ustawienia dla nowej pamięci podręcznej.
   
   | Ustawienie      | Sugerowana wartość  | Opis |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Nazwa DNS** | Podaj globalnie unikatową nazwę. | Nazwa pamięci podręcznej musi być ciągiem z przedziału od 1 do 63 znaków, który zawiera tylko cyfry, litery lub łączniki. Nazwa musi zaczynać się i kończyć cyfrą lub literą i nie może zawierać następujących po sobie łączników. *Nazwa hosta* wystąpienia pamięci podręcznej to *\<DNS name> . Redis.cache.Windows.NET*. | 
   | **Subskrypcja** | Lista rozwijana i wybierz swoją subskrypcję. | Subskrypcja, w ramach której ma zostać utworzone nowe wystąpienie usługi Azure cache for Redis. | 
   | **Grupa zasobów** | Rozwiń i wybierz grupę zasobów lub wybierz pozycję **Utwórz nową** , a następnie wprowadź nową nazwę grupy zasobów. | Nazwa grupy zasobów, w której ma zostać utworzona pamięć podręczna i inne zasoby. Umieszczenie wszystkich zasobów aplikacji w jednej grupie zasobów pozwala łatwo zarządzać nimi i usuwać je razem. | 
   | **Lokalizacja** | Lista rozwijana i wybierz lokalizację. | Wybierz [region](https://azure.microsoft.com/regions/) blisko innych usług, które będą korzystać z pamięci podręcznej. |
   | **Warstwa cenowa** | Lista rozwijana i wybierz [warstwę cenową](https://azure.microsoft.com/pricing/details/cache/). |  Warstwa cenowa decyduje o rozmiarze, wydajności i funkcjach dostępnych dla pamięci podręcznej. Aby uzyskać więcej informacji, zobacz [Omówienie pamięci podręcznej Azure Cache for Redis](cache-overview.md). |

1. Wybierz kartę **Sieć** lub kliknij przycisk **sieci** w dolnej części strony.

1. Na karcie **Sieć** wybierz metodę łączności.

1. Wybierz kartę **Dalej: Zaawansowane** lub kliknij przycisk **Dalej: Zaawansowane** w dolnej części strony.

1. Na karcie **Zaawansowane** dla podstawowego lub standardowego wystąpienia pamięci podręcznej wybierz opcję Włącz przełącznik, jeśli chcesz włączyć port inny niż TLS.

1. Na karcie **Zaawansowane** wystąpienia pamięci podręcznej Premium Skonfiguruj ustawienia dla portu niezwiązanego z protokołem TLS, klastrowanie i trwałość danych.

1. Wybierz kartę **następne: Tagi** lub kliknij przycisk **Dalej: Tagi** w dolnej części strony.

1. Opcjonalnie na karcie **Tagi** wprowadź nazwę i wartość, jeśli chcesz przydzielić zasób. 

1. Wybierz pozycję **Przejrzyj i utwórz**. Nastąpi przekierowanie do karty Recenzja + tworzenie, w której platforma Azure weryfikuje konfigurację.

1. Po wyświetleniu komunikatu o pomyślnym sprawdzeniu poprawności, wybierz pozycję **Utwórz**.

Tworzenie pamięci podręcznej zajmuje trochę czasu. Postęp można monitorować na stronie **Przegląd** usługi Azure cache for Redis. Gdy **stan** jest wyświetlany jako **uruchomiony**, pamięć podręczna jest gotowa do użycia. 

## <a name="prepare-for-deployment"></a>Przygotowanie do wdrożenia

Przed wdrożeniem należy zdefiniować, co jest potrzebne do uruchomienia modelu jako usługi sieci Web. Na poniższej liście opisano podstawowe elementy, które są związane z wdrożeniem:

* __Skrypt wejściowy__. Ten skrypt akceptuje żądania, ocenia żądanie przy użyciu modelu i zwraca wyniki.

    > [!IMPORTANT]
    > Skrypt wejścia jest specyficzny dla modelu; musi on zrozumieć format danych żądania przychodzącego, format danych oczekiwanych przez model i format danych zwracanych do klientów.
    >
    > Jeśli dane żądania są w formacie, którego nie można używać w modelu, skrypt może przekształcić go w akceptowalny format. Może również przekształcić odpowiedź przed zwróceniem jej do klienta.
    >
    > Domyślnie podczas tworzenia pakietów dla funkcji dane wejściowe są traktowane jako tekst. Jeśli interesuje się zużywanie nieprzetworzonych bajtów danych wejściowych (na przykład dla wyzwalaczy obiektów BLOB), należy użyć [AMLRequest do akceptowania danych pierwotnych](../machine-learning/how-to-deploy-advanced-entry-script.md#binary-data).

W przypadku funkcji run upewnij się, że nawiąże połączenie z punktem końcowym Redis.

```python
import json
import numpy as np
import os
import redis
from sklearn.externals import joblib

def init():
    global model
    global azrediscache
    azrediscache = redis.StrictRedis(host='<host_url>', port=6380, password="<access_key>", ssl=True)
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    model = joblib.load(model_path)

@input_schema('data', NumpyParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        input = azrediscache.get(data)
        result = model.predict(input)
        data = np.array(json.loads(data))
        result = model.predict(data)
        # You can return any data type, as long as it is JSON serializable.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

Aby uzyskać więcej informacji na temat skryptu wprowadzania, zobacz [Definiowanie oceniania kodu.](../machine-learning/how-to-deploy-and-where.md?tabs=python#define-an-entry-script)

* **Zależności**, takie jak skrypty pomocnika lub pakiety Python/Conda wymagane do uruchomienia skryptu lub modelu wprowadzania

Te jednostki są hermetyzowane w __konfiguracji wnioskowania__. Konfiguracja wnioskowania przywołuje skrypt wejściowy i inne zależności.

> [!IMPORTANT]
> Podczas tworzenia konfiguracji wnioskowania do użytku z Azure Functions należy użyć obiektu [środowiska](/python/api/azureml-core/azureml.core.environment%28class%29) . Należy pamiętać, że w przypadku definiowania środowiska niestandardowego należy dodać polecenie "Azure-default" z wersją >= 1.0.45 jako zależność PIP. Ten pakiet zawiera funkcje, które są konieczne do hostowania modelu jako usługi sieci Web. Poniższy przykład ilustruje tworzenie obiektu środowiska i używanie go z konfiguracją wnioskowania:
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
>                                                            pip_packages=['azureml-defaults', 'redis'])
> inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
> ```

Aby uzyskać więcej informacji o środowiskach, zobacz [Tworzenie środowisk i zarządzanie nimi na potrzeby szkolenia i wdrażania](../machine-learning/how-to-use-environments.md).

Aby uzyskać więcej informacji na temat konfiguracji wnioskowania, zobacz [Wdrażanie modeli przy użyciu Azure Machine Learning](../machine-learning/how-to-deploy-and-where.md?tabs=python#define-an-inference-configuration).

> [!IMPORTANT]
> Podczas wdrażania w usłudze Functions nie trzeba tworzyć __konfiguracji wdrożenia__.

## <a name="install-the-sdk-preview-package-for-functions-support"></a>Zainstaluj pakiet SDK wersji zapoznawczej dla obsługi funkcji

Aby skompilować pakiety dla Azure Functions, należy zainstalować pakiet SDK w wersji zapoznawczej.

```bash
pip install azureml-contrib-functions
```

## <a name="create-the-image"></a>Tworzenie obrazu

Aby utworzyć obraz platformy Docker wdrożony w Azure Functions, Użyj usługi [Azure. contrib. Functions. Package](/python/api/azureml-contrib-functions/azureml.contrib.functions) lub określonej funkcji pakietu dla wyzwalacza, którego chcesz używać. Poniższy fragment kodu pokazuje, jak utworzyć nowy pakiet z wyzwalaczem HTTP z poziomu konfiguracji modelu i wnioskowania:

> [!NOTE]
> W fragmencie kodu założono `model` , że zawiera zarejestrowany model i `inference_config` zawiera konfigurację środowiska wnioskowania. Aby uzyskać więcej informacji, zobacz [Wdrażanie modeli przy użyciu Azure Machine Learning](../machine-learning/how-to-deploy-and-where.md).

```python
from azureml.contrib.functions import package
from azureml.contrib.functions import HTTP_TRIGGER
model_package = package(ws, [model], inference_config, functions_enabled=True, trigger=HTTP_TRIGGER)
model_package.wait_for_creation(show_output=True)
# Display the package location/ACR path
print(model_package.location)
```

Gdy `show_output=True` są wyświetlane dane wyjściowe procesu kompilacji platformy Docker. Po zakończeniu procesu obraz został utworzony w Azure Container Registry dla obszaru roboczego. Po skompilowaniu obrazu zostanie wyświetlona lokalizacja w Azure Container Registry. Zwrócona Lokalizacja ma format `<acrinstance>.azurecr.io/package@sha256:<imagename>` .

> [!NOTE]
> Pakowanie dla funkcji obecnie obsługuje wyzwalacze HTTP, wyzwalacze obiektów blob i wyzwalacze usługi Service Bus. Aby uzyskać więcej informacji na temat wyzwalaczy, zobacz [Azure Functions powiązania](../azure-functions/functions-bindings-storage-blob-trigger.md#blob-name-patterns).

> [!IMPORTANT]
> Zapisz informacje o lokalizacji, ponieważ są używane podczas wdrażania obrazu.

## <a name="deploy-image-as-a-web-app"></a>Wdrażanie obrazu jako aplikacji sieci Web

1. Użyj poniższego polecenia, aby uzyskać poświadczenia logowania dla Azure Container Registry zawierającej obraz. Zamień na `<myacr>` wartość zwróconą wcześniej z `package.location` : 

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
    > W tym momencie aplikacja funkcji została utworzona. Jednak ponieważ nie podano parametrów połączenia dla wyzwalacza HTTP lub poświadczeń do Azure Container Registry zawierającego obraz, aplikacja funkcji jest nieaktywna. W następnych krokach podano parametry połączenia oraz informacje o uwierzytelnianiu dla rejestru kontenerów. 

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
> Załadowanie obrazu może potrwać kilka minut. Postęp można monitorować przy użyciu Azure Portal.

## <a name="test-azure-functions-http-trigger"></a>Wyzwalacz HTTP Azure Functions testu 

Teraz uruchomimy i przetestujemy nasz Azure Functions wyzwalacza HTTP.

1. Przejdź do aplikacji funkcji w Azure Portal.
1. W obszarze Deweloper wybierz pozycję **Code + test**. 
1. Po prawej stronie wybierz kartę **dane wejściowe** . 
1. Kliknij przycisk **Uruchom** , aby przetestować Azure Functions wyzwalacza http. 

Pomyślnie wdrożono model z Azure Machine Learning jako aplikacji funkcji przy użyciu usługi Azure cache for Redis. Dowiedz się więcej o usłudze Azure cache for Redis, przechodząc do linków w sekcji poniżej.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli zamierzasz przejść do kolejnego samouczka, możesz zachować zasoby utworzone w tym przewodniku Szybki start i użyć ich ponownie.

W przeciwnym razie, Jeśli zakończysz pracę z przewodnikiem Szybki Start, możesz usunąć zasoby platformy Azure utworzone w ramach tego przewodnika Szybki Start, aby uniknąć naliczania opłat. 

> [!IMPORTANT]
> Usunięcie grupy zasobów jest nieodwracalne. Jeśli usuniesz grupę zasobów, wszystkie zawarte w niej zasoby zostaną trwale usunięte. Uważaj, aby nie usunąć przypadkowo niewłaściwych zasobów lub grupy zasobów. Jeśli zasoby do hostowania tego przykładu zostały utworzone wewnątrz istniejącej grupy zasobów zawierającej zasoby, które chcesz zachować, możesz usunąć każdy zasób oddzielnie z odpowiadającego mu bloku zamiast usuwać całą grupę zasobów.

### <a name="to-delete-a-resource-group"></a>Aby usunąć grupę zasobów

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com), a następnie wybierz pozycję **Grupy zasobów**.

2. W polu **Filtruj według nazwy...** wpisz nazwę grupy zasobów. Dla grupy zasobów na liście wyników kliknij pozycję **...**, a następnie wybierz pozycję **Usuń grupę zasobów**.

Zobaczysz prośbę o potwierdzenie usunięcia grupy zasobów. Wpisz nazwę grupy zasobów w celu potwierdzenia, a następnie wybierz pozycję **Usuń**.

Po krótkim czasie grupa zasobów i wszystkie jej zasoby zostaną usunięte.

## <a name="next-steps"></a>Następne kroki 

* Dowiedz się więcej o [usłudze Azure cache for Redis](./cache-overview.md)
* Dowiedz się, jak skonfigurować aplikację funkcji w dokumentacji [funkcji](../azure-functions/functions-create-function-linux-custom-image.md) .
* [Dokumentacja interfejsu API](/python/api/azureml-contrib-functions/azureml.contrib.functions) 
* Tworzenie aplikacji w języku [Python korzystającej z usługi Azure cache for Redis](./cache-python-get-started.md)
