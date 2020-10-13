---
title: Modele pakietów
titleSuffix: Azure Machine Learning
description: Pakowanie modelu jako pliku dockerfile
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 07/31/2020
ms.topic: conceptual
ms.openlocfilehash: d5fb2539d79c31de5a5e0196a7a4814c02a84602
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87544598"
---
# <a name="how-to-package-a-registered-model-with-docker"></a>Jak spakować zarejestrowany model przy użyciu platformy Docker

W tym artykule pokazano, jak spakować zarejestrowany model Azure Machine Learning przy użyciu platformy Docker.

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule przyjęto założenie, że w obszarze roboczym uczenia maszynowego został już przeszkolony i zarejestrowany model. Aby dowiedzieć się, jak szkolić i zarejestrować model uczenia scikit, [postępuj zgodnie z tym samouczkiem](how-to-train-scikit-learn.md).


## <a name="package-models"></a>Modele pakietów

W niektórych przypadkach może być konieczne utworzenie obrazu platformy Docker bez wdrażania modelu (Jeśli na przykład planujesz [wdrożyć program w Azure App Service](how-to-deploy-app-service.md)). Możesz też chcieć pobrać obraz i uruchomić go w lokalnej instalacji platformy Docker. Możesz nawet pobrać pliki użyte do skompilowania obrazu, sprawdzić je, zmodyfikować i ręcznie utworzyć obraz.

Pakowanie modeli umożliwia wykonywanie tych czynności. Wszystkie zasoby potrzebne do hostowania modelu jako usługi sieci Web i umożliwiają pobranie w pełni skompilowanego obrazu platformy Docker lub plików niezbędnych do jego skompilowania. Istnieją dwa sposoby używania pakowania modelu:

**Pobierz spakowany model:** Pobierz obraz platformy Docker zawierający model i inne pliki niezbędne do hostowania go jako usługi sieci Web.

**Generuj element pliku dockerfile:** Pobierz pliku dockerfile, model, skrypt wejściowy i inne zasoby, które są konieczne do skompilowania obrazu platformy Docker. Następnie można przeprowadzić inspekcję plików lub wprowadzić zmiany przed rozpoczęciem tworzenia obrazu lokalnie.

Oba pakiety mogą służyć do pobrania lokalnego obrazu platformy Docker.

> [!TIP]
> Tworzenie pakietu jest podobne do wdrażania modelu. Używasz zarejestrowanego modelu i konfiguracji wnioskowania.

> [!IMPORTANT]
> Aby pobrać pełny obraz lub utworzyć obraz lokalnie, należy zainstalować [platformę Docker](https://www.docker.com) w środowisku deweloperskim.

### <a name="download-a-packaged-model"></a>Pobierz spakowany model

Poniższy przykład tworzy obraz, który jest rejestrowany w usłudze Azure Container Registry dla Twojego obszaru roboczego:

```python
package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
```

Po utworzeniu pakietu można użyć `package.pull()` programu w celu ściągnięcia obrazu do lokalnego środowiska platformy Docker. W danych wyjściowych tego polecenia zostanie wyświetlona nazwa obrazu. Na przykład: 

`Status: Downloaded newer image for myworkspacef78fd10.azurecr.io/package:20190822181338`. 

Po pobraniu modelu Użyj `docker images` polecenia, aby wyświetlić listę obrazów lokalnych:

```text
REPOSITORY                               TAG                 IMAGE ID            CREATED             SIZE
myworkspacef78fd10.azurecr.io/package    20190822181338      7ff48015d5bd        4 minutes ago       1.43 GB
```

Aby uruchomić kontener lokalny na podstawie tego obrazu, użyj następującego polecenia, aby uruchomić nazwany kontener z powłoki lub wiersza polecenia. Zastąp `<imageid>` wartość identyfikatorem obrazu zwracanym przez `docker images` polecenie.

```bash
docker run -p 6789:5001 --name mycontainer <imageid>
```

To polecenie powoduje uruchomienie najnowszej wersji obrazu o nazwie `myimage` . Mapuje port lokalny 6789 na port w kontenerze, w którym usługa sieci Web nasłuchuje (5001). Przypisuje również nazwę `mycontainer` do kontenera, co ułatwia zatrzymanie kontenera. Po rozpoczęciu kontenera można przesłać żądania do `http://localhost:6789/score` .

### <a name="generate-a-dockerfile-and-dependencies"></a>Generowanie pliku dockerfile i zależności

Poniższy przykład pokazuje, jak pobrać pliku dockerfile, model i inne zasoby, które są konieczne do lokalnego utworzenia obrazu. `generate_dockerfile=True`Parametr wskazuje, że chcesz, aby pliki nie były w pełni skompilowane.

```python
package = Model.package(ws, [model], inference_config, generate_dockerfile=True)
package.wait_for_creation(show_output=True)
# Download the package.
package.save("./imagefiles")
# Get the Azure container registry that the model/Dockerfile uses.
acr=package.get_container_registry()
print("Address:", acr.address)
print("Username:", acr.username)
print("Password:", acr.password)
```

Ten kod pobiera pliki, które są konieczne do skompilowania obrazu do `imagefiles` katalogu. Pliku dockerfile zawarte w zapisanych plikach odwołuje się do obrazu podstawowego przechowywanego w rejestrze kontenerów platformy Azure. Podczas tworzenia obrazu w lokalnej instalacji platformy Docker należy użyć adresu, nazwy użytkownika i hasła w celu uwierzytelnienia w rejestrze. Wykonaj następujące kroki, aby skompilować obraz przy użyciu lokalnej instalacji platformy Docker:

1. Z poziomu powłoki lub sesji wiersza polecenia Użyj następującego polecenia, aby uwierzytelnić platformę Docker za pomocą usługi Azure Container Registry. Zastąp `<address>` `<username>` wartości, i `<password>` wartościami pobranymi przez `package.get_container_registry()` .

    ```bash
    docker login <address> -u <username> -p <password>
    ```

2. Aby skompilować obraz, użyj następującego polecenia. Zamień na `<imagefiles>` ścieżkę do katalogu, w którym `package.save()` zapisano pliki.

    ```bash
    docker build --tag myimage <imagefiles>
    ```

    To polecenie ustawia nazwę obrazu na `myimage` .

Aby sprawdzić, czy obraz został skompilowany, użyj `docker images` polecenia. Obraz powinien zostać wyświetlony `myimage` na liście:

```text
REPOSITORY      TAG                 IMAGE ID            CREATED             SIZE
<none>          <none>              2d5ee0bf3b3b        49 seconds ago      1.43 GB
myimage         latest              739f22498d64        3 minutes ago       1.43 GB
```

Aby rozpocząć nowy kontener oparty na tym obrazie, użyj następującego polecenia:

```bash
docker run -p 6789:5001 --name mycontainer myimage:latest
```

To polecenie powoduje uruchomienie najnowszej wersji obrazu o nazwie `myimage` . Mapuje port lokalny 6789 na port w kontenerze, w którym usługa sieci Web nasłuchuje (5001). Przypisuje również nazwę `mycontainer` do kontenera, co ułatwia zatrzymanie kontenera. Po rozpoczęciu kontenera można przesłać żądania do `http://localhost:6789/score` .

### <a name="example-client-to-test-the-local-container"></a>Przykładowy klient do testowania lokalnego kontenera

Poniższy kod stanowi przykład klienta języka Python, który może być używany z kontenerem:

```python
import requests
import json

# URL for the web service.
scoring_uri = 'http://localhost:6789/score'

# Two sets of data to score, so we get two results back.
data = {"data":
        [
            [ 1,2,3,4,5,6,7,8,9,10 ],
            [ 10,9,8,7,6,5,4,3,2,1 ]
        ]
        }
# Convert to JSON string.
input_data = json.dumps(data)

# Set the content type.
headers = {'Content-Type': 'application/json'}

# Make the request and display the response.
resp = requests.post(scoring_uri, input_data, headers=headers)
print(resp.text)
```

Na przykład klientów w innych językach programowania można znaleźć w temacie [Korzystanie z modeli wdrożonych jako usługi sieci Web](how-to-consume-web-service.md).

### <a name="stop-the-docker-container"></a>Zatrzymaj kontener platformy Docker

Aby zatrzymać kontener, użyj następującego polecenia z innej powłoki lub wiersza polecenia:

```bash
docker kill mycontainer
```

## <a name="next-steps"></a>Następne kroki

* [Rozwiązywanie problemów z niepowodzeniem wdrożenia](how-to-troubleshoot-deployment.md)
* [Wdrażanie w usłudze Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [Tworzenie aplikacji klienckich w celu korzystania z usług sieci Web](how-to-consume-web-service.md)
* [Aktualizowanie usługi internetowej](how-to-deploy-update-web-service.md)
* [Jak wdrożyć model przy użyciu niestandardowego obrazu platformy Docker](how-to-deploy-custom-docker-image.md)
* [Zabezpieczanie usługi internetowej za pomocą usługi Azure Machine Learning przy użyciu protokołu TLS](how-to-secure-web-service.md)
* [Monitoruj modele Azure Machine Learning przy użyciu Application Insights](how-to-enable-app-insights.md)
* [Zbieranie danych dla modeli w środowisku produkcyjnym](how-to-enable-data-collection.md)
* [Tworzenie alertów zdarzeń i wyzwalaczy na potrzeby wdrożeń modeli](how-to-use-event-grid.md)
