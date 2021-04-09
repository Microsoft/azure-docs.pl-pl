---
title: Uruchom kontener wykrywanie języka w usłudze Kubernetes
titleSuffix: Text Analytics -  Azure Cognitive Services
description: Wdróż kontener wykrywania języka z uruchomionym przykładem w usłudze Azure Kubernetes i przetestuj go w przeglądarce sieci Web.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 6918218d8434c06f59b0738e60cad53b94b0a0b5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "98939843"
---
# <a name="deploy-the-text-analytics-language-detection-container-to-azure-kubernetes-service"></a>Wdróż kontener wykrywania języka analiza tekstu w usłudze Azure Kubernetes Service

Dowiedz się, jak wdrożyć kontener wykrywania języka. Ta procedura przedstawia sposób tworzenia lokalnych kontenerów platformy Docker, wypchnięcia kontenerów do prywatnego rejestru kontenerów, uruchomienia kontenera w klastrze Kubernetes i przetestowania go w przeglądarce sieci Web.

## <a name="prerequisites"></a>Wymagania wstępne

Ta procedura wymaga kilku narzędzi, które muszą być zainstalowane i uruchomione lokalnie. Nie należy używać usługi Azure Cloud Shell.

* Użyj subskrypcji platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/cognitive-services).
* Narzędzie [git](https://git-scm.com/downloads) dla systemu operacyjnego umożliwia klonowanie [przykładu](https://github.com/Azure-Samples/cognitive-services-containers-samples) użytego w tej procedurze.
* [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).
* [Aparat platformy Docker](https://www.docker.com/products/docker-engine) i sprawdzanie poprawności działania interfejsu wiersza polecenia platformy Docker w oknie konsoli.
* [polecenia kubectl](https://storage.googleapis.com/kubernetes-release/release/v1.13.1/bin/windows/amd64/kubectl.exe).
* Zasób platformy Azure z poprawną warstwą cenową. Nie wszystkie warstwy cenowe pracują z tym kontenerem:
  * Zasób **Analiza tekstu** tylko za pomocą warstwy cenowej F0 lub standardowa.
  * **Cognitive Services** zasób z warstwą cenową S0.

## <a name="running-the-sample"></a>Uruchamianie przykładowej aplikacji

Ta procedura polega na załadowaniu i uruchomieniu przykładowego kontenera Cognitive Services na potrzeby wykrywania języka. Przykład ma dwa kontenery, jeden dla aplikacji klienckiej i jeden dla kontenera Cognitive Services. Oba te obrazy zostaną wypchnięte do Azure Container Registry. Po umieszczeniu ich w Twoim rejestrze Utwórz usługę Azure Kubernetes, aby uzyskać dostęp do tych obrazów i uruchomić kontenery. Gdy kontenery są uruchomione, użyj interfejsu wiersza polecenia **polecenia kubectl** , aby obserwować wydajność kontenerów. Uzyskaj dostęp do aplikacji klienckiej za pomocą żądania HTTP i Zobacz wyniki.

![Koncepcja koncepcyjna uruchamiania przykładowych kontenerów](../text-analytics/media/how-tos/container-instance-sample/containers.png)

## <a name="the-sample-containers"></a>Przykładowe kontenery

Przykład ma dwa obrazy kontenerów — jeden dla witryny frontonu. Drugi obraz jest kontenerem wykrywania języka zwracającym wykryty język (kulturę) tekstu. Oba kontenery są dostępne z zewnętrznego adresu IP po zakończeniu.

### <a name="the-language-frontend-container"></a>Kontener frontonu języka

Ta witryna sieci Web jest równoważna z własną aplikacją po stronie klienta, która wysyła żądania punktu końcowego wykrywania języka. Po zakończeniu procedury można uzyskać wykryty język ciągu znaków, uzyskując dostęp do kontenera witryny sieci Web w przeglądarce `http://<external-IP>/<text-to-analyze>` . Przykład tego adresu URL: `http://132.12.23.255/helloworld!`. Wynikiem jest przeglądarka `English` .

### <a name="the-language-container"></a>Kontener języka

Kontener wykrywania języka, w ramach tej konkretnej procedury, jest dostępny dla każdego żądania zewnętrznego. Kontener nie został w żaden sposób zmieniony, aby można było uzyskać dostęp do interfejsu API wykrywania języka standardowego Cognitive Services określonego kontenera.

W przypadku tego kontenera ten interfejs API to żądanie POST dotyczące wykrywania języka. Podobnie jak w przypadku wszystkich kontenerów Cognitive Services, można dowiedzieć się więcej na temat kontenera ze swoich informacji o hostowanej strukturze Swagger `http://<external-IP>:5000/swagger/index.html` .

Port 5000 jest domyślnym portem używanym z kontenerami Cognitive Services.

## <a name="create-azure-container-registry-service"></a>Tworzenie usługi Azure Container Registry

Aby wdrożyć kontener w usłudze Azure Kubernetes, obrazy kontenerów muszą być dostępne. Utwórz własną usługę Azure Container Registry, aby hostować obrazy.

1. Logowanie do interfejsu wiersza polecenia platformy Azure

    ```azurecli-interactive
    az login
    ```

1. Utwórz grupę zasobów o nazwie `cogserv-container-rg` , aby przechowywać wszystkie zasoby utworzone w tej procedurze.

    ```azurecli-interactive
    az group create --name cogserv-container-rg --location westus
    ```

1. Utwórz własne Azure Container Registry przy użyciu formatu nazwy, na przykład `registry` `pattyregistry` . Nie używaj kresek ani podkreślać znaków w nazwie.

    ```azurecli-interactive
    az acr create --resource-group cogserv-container-rg --name pattyregistry --sku Basic
    ```

    Zapisz wyniki, aby uzyskać Właściwość **loginServer** . Będzie ona częścią adresu hostowanego kontenera, użytego później w `language.yml` pliku.

    ```azurecli-interactive
    az acr create --resource-group cogserv-container-rg --name pattyregistry --sku Basic
    ```

    ```output
    {
        "adminUserEnabled": false,
        "creationDate": "2019-01-02T23:49:53.783549+00:00",
        "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/cogserv-container-rg/providers/Microsoft.ContainerRegistry/registries/pattyregistry",
        "location": "westus",
        "loginServer": "pattyregistry.azurecr.io",
        "name": "pattyregistry",
        "provisioningState": "Succeeded",
        "resourceGroup": "cogserv-container-rg",
        "sku": {
            "name": "Basic",
            "tier": "Basic"
        },
        "status": null,
        "storageAccount": null,
        "tags": {},
        "type": "Microsoft.ContainerRegistry/registries"
    }
    ```

1. Zaloguj się do rejestru kontenerów. Przed wypchnięciem obrazów do rejestru należy się zalogować.

    ```azurecli-interactive
    az acr login --name pattyregistry
    ```

## <a name="get-website-docker-image"></a>Pobierz obraz platformy Docker witryny sieci Web

1. Przykładowy kod używany w tej procedurze znajduje się w repozytorium Cognitive Services kontenerów przykładów. Sklonuj repozytorium, aby zawierało lokalną kopię przykładu.

    ```console
    git clone https://github.com/Azure-Samples/cognitive-services-containers-samples
    ```

    Gdy repozytorium znajduje się na komputerze lokalnym, Znajdź witrynę sieci Web w katalogu [\dotnet\Language\FrontendService](https://github.com/Azure-Samples/cognitive-services-containers-samples/tree/master/dotnet/Language/FrontendService) . Ta witryna sieci Web działa jako aplikacja kliencka wywołująca interfejs API wykrywania języka hostowanego w kontenerze wykrywania języka.  

1. Utwórz obraz platformy Docker dla tej witryny sieci Web. Upewnij się, że konsola znajduje się w katalogu [\FrontendService](https://github.com/Azure-Samples/cognitive-services-containers-samples/tree/master/dotnet/Language/FrontendService) , w którym znajduje się pliku dockerfile, gdy uruchomisz następujące polecenie:

    ```console
    docker build -t language-frontend -t pattiyregistry.azurecr.io/language-frontend:v1 .
    ```

    Aby śledzić wersję w rejestrze kontenerów, Dodaj tag w formacie wersji, np `v1` ..

1. Wypchnij obraz do rejestru kontenerów. Może to potrwać kilka minut.

    ```console
    docker push pattyregistry.azurecr.io/language-frontend:v1
    ```

    Jeśli `unauthorized: authentication required` wystąpi błąd, zaloguj się za pomocą `az acr login --name <your-container-registry-name>` polecenia. 

    Po zakończeniu procesu wyniki powinny wyglądać podobnie do:

    ```output
    The push refers to repository [pattyregistry.azurecr.io/language-frontend]
    82ff52ee6c73: Pushed
    07599c047227: Pushed
    816caf41a9a1: Pushed
    2924be3aed17: Pushed
    45b83a23806f: Pushed
    ef68f6734aa4: Pushed
    v1: digest: sha256:31930445deee181605c0cde53dab5a104528dc1ff57e5b3b34324f0d8a0eb286 size: 1580
    ```

## <a name="get-language-detection-docker-image"></a>Pobierz obraz platformy Docker wykrywania języka

1. Pobierz najnowszą wersję obrazu platformy Docker na komputer lokalny. Może to potrwać kilka minut. Jeśli istnieje nowsza wersja tego kontenera, należy zmienić wartość z `1.1.006770001-amd64-preview` do nowszej wersji.

    ```console
    docker pull mcr.microsoft.com/azure-cognitive-services/language:1.1.006770001-amd64-preview
    ```

1. Oznacz obraz za pomocą rejestru kontenerów. Znajdź najnowszą wersję i Zastąp ją, `1.1.006770001-amd64-preview` Jeśli masz nowszą wersję. 

    ```console
    docker tag mcr.microsoft.com/azure-cognitive-services/language pattiyregistry.azurecr.io/language:1.1.006770001-amd64-preview
    ```

1. Wypchnij obraz do rejestru kontenerów. Może to potrwać kilka minut.

    ```console
    docker push pattyregistry.azurecr.io/language:1.1.006770001-amd64-preview
    ```

## <a name="get-container-registry-credentials"></a>Pobierz poświadczenia Container Registry

Poniższe kroki są niezbędne do uzyskania informacji wymaganych do połączenia rejestru kontenerów z usługą Azure Kubernetes, którą utworzysz w dalszej części tej procedury.

1. Utwórz nazwę główną usługi.

    ```azurecli-interactive
    az ad sp create-for-rbac --skip-assignment
    ```

    Zapisz wartość wyników `appId` dla parametru przydzielonego w kroku 3, `<appId>` . Zapisz `password` dla parametru Client-Secret w następnej sekcji `<client-secret>` .

    ```output
    {
      "appId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "displayName": "azure-cli-2018-12-31-18-39-32",
      "name": "http://azure-cli-2018-12-31-18-39-32",
      "password": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "tenant": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    }
    ```

1. Pobierz identyfikator rejestru kontenerów.

    ```azurecli-interactive
    az acr show --resource-group cogserv-container-rg --name pattyregistry --query "id" --o table
    ```

    Zapisz dane wyjściowe dla wartości parametru zakresu, `<acrId>` w następnym kroku. Wygląda na to:

    ```output
    /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/cogserv-container-rg/providers/Microsoft.ContainerRegistry/registries/pattyregistry
    ```

    Zapisz pełną wartość dla kroku 3 w tej sekcji.

1. Aby udzielić poprawnego dostępu do klastra AKS do używania obrazów przechowywanych w rejestrze kontenerów, Utwórz przypisanie roli. Zamień `<appId>` i `<acrId>` na wartości zebrane w poprzednich dwóch krokach.

    ```azurecli-interactive
    az role assignment create --assignee <appId> --scope <acrId> --role Reader
    ```

## <a name="create-azure-kubernetes-service"></a>Tworzenie usługi Azure Kubernetes

1. Utwórz klaster Kubernetes. Wszystkie wartości parametrów pochodzą z poprzednich sekcji, z wyjątkiem parametru name. Wybierz nazwę wskazującą, kto je utworzył, i przeznaczenie, na przykład `patty-kube` .

    ```azurecli-interactive
    az aks create --resource-group cogserv-container-rg --name patty-kube --node-count 2  --service-principal <appId>  --client-secret <client-secret>  --generate-ssh-keys
    ```

    Może to potrwać kilka minut. Wynik:

    ```output
    {
      "aadProfile": null,
      "addonProfiles": null,
      "agentPoolProfiles": [
        {
          "count": 2,
          "dnsPrefix": null,
          "fqdn": null,
          "maxPods": 110,
          "name": "nodepool1",
          "osDiskSizeGb": 30,
          "osType": "Linux",
          "ports": null,
          "storageProfile": "ManagedDisks",
          "vmSize": "Standard_DS1_v2",
          "vnetSubnetId": null
        }
      ],
      "dnsPrefix": "patty-kube--65a101",
      "enableRbac": true,
      "fqdn": "patty-kube--65a101-341f1f54.hcp.westus.azmk8s.io",
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/cogserv-container-rg/providers/Microsoft.ContainerService/managedClusters/patty-kube",
      "kubernetesVersion": "1.9.11",
      "linuxProfile": {
        "adminUsername": "azureuser",
        "ssh": {
          "publicKeys": [
            {
              "keyData": "ssh-rsa AAAAB3NzaC...ohR2d81mFC
            }
          ]
        }
      },
      "location": "westus",
      "name": "patty-kube",
      "networkProfile": {
        "dnsServiceIp": "10.0.0.10",
        "dockerBridgeCidr": "172.17.0.1/16",
        "networkPlugin": "kubenet",
        "networkPolicy": null,
        "podCidr": "10.244.0.0/16",
        "serviceCidr": "10.0.0.0/16"
      },
      "nodeResourceGroup": "MC_patty_westus",
      "provisioningState": "Succeeded",
      "resourceGroup": "cogserv-container-rg",
      "servicePrincipalProfile": {
        "clientId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "keyVaultSecretRef": null,
        "secret": null
      },
      "tags": null,
      "type": "Microsoft.ContainerService/ManagedClusters"
    }
    ```

    Usługa została utworzona, ale nie ma jeszcze kontenera witryny sieci Web ani kontenera wykrywania języka.  

1. Pobierz poświadczenia klastra Kubernetes.

    ```azurecli-interactive
    az aks get-credentials --resource-group cogserv-container-rg --name patty-kube
    ```

## <a name="load-the-orchestration-definition-into-your-kubernetes-service"></a>Załaduj definicję aranżacji do usługi Kubernetes

Ta sekcja używa interfejsu wiersza polecenia **polecenia kubectl** , aby komunikować się z usługą Azure Kubernetes.

1. Przed załadowaniem definicji aranżacji Sprawdź, czy **polecenia kubectl** ma dostęp do węzłów.

    ```console
    kubectl get nodes
    ```

    Odpowiedź wygląda następująco:

    ```output
    NAME                       STATUS    ROLES     AGE       VERSION
    aks-nodepool1-13756812-0   Ready     agent     6m        v1.9.11
    aks-nodepool1-13756812-1   Ready     agent     6m        v1.9.11
    ```

1. Skopiuj następujący plik i nadaj mu nazwę `language.yml` . Plik zawiera `service` sekcję i `deployment` sekcję dla dwóch typów kontenerów, `language-frontend` kontenera witryny sieci Web i `language` kontenera wykrywania.

    [!code-yml[Kubernetes orchestration file for the Cognitive Services containers sample](~/samples-cogserv-containers/Kubernetes/language/language.yml "Kubernetes orchestration file for the Cognitive Services containers sample")]

1. Zmień wiersze wdrożenia w programie Language-fronton w `language.yml` oparciu o poniższą tabelę, aby dodać własne nazwy obrazów rejestru kontenerów, klucz tajny klienta i ustawienia analizy tekstu.

    Ustawienia wdrożenia w języku frontonu|Przeznaczenie|
    |--|--|
    |Wiersz 32<br> `image` wartość|Lokalizacja obrazu dla obrazu frontonu w Container Registry<br>`<container-registry-name>.azurecr.io/language-frontend:v1`|
    |Wiersz 44<br> `name` wartość|Container Registry wpis tajny obrazu, określany jako `<client-secret>` w poprzedniej sekcji.|

1. Zmień wiersze wdrożenia języka `language.yml` w oparciu o poniższą tabelę, aby dodać własne nazwy obrazów rejestru kontenerów, klucz tajny klienta i ustawienia analizy tekstu.

    |Ustawienia wdrażania języka|Przeznaczenie|
    |--|--|
    |Wiersz 78<br> `image` wartość|Lokalizacja obrazu dla obrazu języka w Container Registry<br>`<container-registry-name>.azurecr.io/language:1.1.006770001-amd64-preview`|
    |Wiersz 95<br> `name` wartość|Container Registry wpis tajny obrazu, określany jako `<client-secret>` w poprzedniej sekcji.|
    |Wiersz 91<br> `apiKey` wartość|Klucz zasobu analizy tekstu|
    |Wiersz 92<br> `billing` wartość|Punkt końcowy rozliczeń dla zasobu analizy tekstu.<br>`https://westus.api.cognitive.microsoft.com/text/analytics/v2.1`|

    Ponieważ **apiKey** i **punkt końcowy rozliczeniowy** są ustawiane w ramach definicji aranżacji Kubernetes, kontener witryny sieci Web nie musi wiedzieć o nich ani przekazać ich w ramach żądania. Kontener witryny sieci Web odnosi się do kontenera wykrywania języka według jego nazwy programu Orchestrator `language` .

1. Załaduj plik definicji aranżacji dla tego przykładu z folderu, w którym został utworzony i zapisany `language.yml` .

    ```console
    kubectl apply -f language.yml
    ```

    Odpowiedź:

    ```output
    service "language-frontend" created
    deployment.apps "language-frontend" created
    service "language" created
    deployment.apps "language" created
    ```

## <a name="get-external-ips-of-containers"></a>Pobierz zewnętrzne adresy IP kontenerów

W przypadku dwóch kontenerów Sprawdź, `language-frontend` czy `language` usługi i są uruchomione i uzyskaj zewnętrzny adres IP.

```console
kubectl get all
```

```output
NAME                                     READY     STATUS    RESTARTS   AGE
pod/language-586849d8dc-7zvz5            1/1       Running   0          13h
pod/language-frontend-68b9969969-bz9bg   1/1       Running   1          13h

NAME                        TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)          AGE
service/kubernetes          ClusterIP      10.0.0.1      <none>          443/TCP          14h
service/language            LoadBalancer   10.0.39.169   104.42.172.68   5000:30161/TCP   13h
service/language-frontend   LoadBalancer   10.0.42.136   104.42.37.219   80:30943/TCP     13h

NAME                                      DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deployment.extensions/language            1         1         1            1           13h
deployment.extensions/language-frontend   1         1         1            1           13h

NAME                                                 DESIRED   CURRENT   READY     AGE
replicaset.extensions/language-586849d8dc            1         1         1         13h
replicaset.extensions/language-frontend-68b9969969   1         1         1         13h

NAME                                DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/language            1         1         1            1           13h
deployment.apps/language-frontend   1         1         1            1           13h

NAME                                           DESIRED   CURRENT   READY     AGE
replicaset.apps/language-586849d8dc            1         1         1         13h
replicaset.apps/language-frontend-68b9969969   1         1         1         13h
```

Jeśli `EXTERNAL-IP` dla usługi jest wyświetlana jako oczekująca, uruchom ponownie polecenie do momentu wyświetlenia adresu IP przed przejściem do następnego kroku.

## <a name="test-the-language-detection-container"></a>Testowanie kontenera wykrywania języka

Otwórz przeglądarkę i przejdź do zewnętrznego adresu IP `language` kontenera z poprzedniej sekcji: `http://<external-ip>:5000/swagger/index.html` . `Try it`Aby przetestować punkt końcowy wykrywania języka, można użyć funkcji interfejsu API.

![Wyświetlanie dokumentacji struktury Swagger kontenera](../text-analytics/media/how-tos/container-instance-sample/language-detection-container-swagger-documentation.png)

## <a name="test-the-client-application-container"></a>Testowanie kontenera aplikacji klienckiej

Zmień adres URL w przeglądarce na zewnętrzny adres IP `language-frontend` kontenera przy użyciu następującego formatu: `http://<external-ip>/helloworld` . Tekst kultury angielskiej `helloworld` jest przewidywany jako `English` .

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po zakończeniu pracy z klastrem Usuń grupę zasobów platformy Azure.

```azurecli-interactive
az group delete --name cogserv-container-rg
```

## <a name="related-information"></a>Informacje pokrewne

* [polecenia kubectl dla użytkowników platformy Docker](https://kubernetes.io/docs/reference/kubectl/docker-cli-to-kubectl/)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Kontenery usług Cognitive Services](../cognitive-services-container-support.md)
