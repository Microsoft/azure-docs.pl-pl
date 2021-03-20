---
title: Samouczek — szybka kompilacja obrazu kontenera
description: Z tego samouczka dowiesz się, jak utworzyć obraz kontenera platformy Docker na platformie Azure przy użyciu usługi Azure Container Registry Tasks (ACR Tasks), a następnie wdrożyć go w usłudze Azure Container Instances.
ms.topic: tutorial
ms.date: 11/24/2020
ms.custom: seodec18, mvc, devx-track-azurecli
ms.openlocfilehash: b218f47348d5a26297f14c4bc788a6cf6b78cc60
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96030337"
---
# <a name="tutorial-build-and-deploy-container-images-in-the-cloud-with-azure-container-registry-tasks"></a>Samouczek: Kompilowanie obrazów kontenera w chmurze przy użyciu zadań usługi Azure Container Registry

Usługa [ACR Tasks](container-registry-tasks-overview.md) to pakiet funkcji usługi Azure Container Registry, który udostępnia udoskonalone i wydajne kompilacje obrazów kontenerów platformy Docker na platformie Azure. Z tego artykułu dowiesz się, jak używać funkcji *szybkiego zadania* usługi ACR Tasks.

Cykl tworzenia oprogramowania „pętli wewnętrznej” to iteracyjny proces pisania kodu, kompilowania i testowania aplikacji przed zaewidencjonowaniem w kontroli źródła. Szybkie zadanie rozszerza pętlę wewnętrzną na chmurę, udostępniając funkcję walidacji powodzenia kompilacji i automatyczne wypychanie pomyślnie skompilowanych obrazów do rejestru kontenerów. Obrazy są wbudowane natywnie w chmurze, blisko rejestru, co umożliwia szybsze wdrażanie.

Całe środowisko pracy z plikami Dockerfile jest przenoszone bezpośrednio do usługi ACR Tasks. Nie trzeba zmieniać plików Dockerfile w celu przeprowadzania kompilacji w chmurze przy użyciu usługi ACR Tasks, wystarczy zmienić uruchamiane polecenie. 

W tym samouczku, będącym pierwszą częścią serii, zostaną wykonane następujące czynności:

> [!div class="checklist"]
> * Pobieranie kodu źródłowego przykładowej aplikacji
> * Kompilowanie obrazu kontenera na platformie Azure
> * Wdrażanie kontenera w usłudze Azure Container Instances

Z kolejnych samouczków dowiesz się, jak używać zadań usługi ACR Tasks na potrzeby automatycznych kompilacji obrazu kontenera podczas zatwierdzania kodu i aktualizacji obrazu podstawowego. Zadania ACR mogą również uruchamiać [zadania wieloetapowe](container-registry-tasks-multi-step.md), używając pliku YAML do definiowania kroków do kompilowania, wypychania i opcjonalnego testowania wielu kontenerów.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="github-account"></a>Konto w serwisie GitHub

Utworzenie konta usługi https://github.com, jeśli jeszcze go nie masz. W tym samouczku do zademonstrowania zautomatyzowanych kompilacji obrazu w usłudze ACR Tasks jest używane repozytorium GitHub.

### <a name="fork-sample-repository"></a>Tworzenie rozwidlenia przykładowego repozytorium

Następnie należy użyć interfejsu użytkownika serwisu GitHub do utworzenia rozwidlenia przykładowego repozytorium na koncie usługi GitHub. W tym samouczku utworzysz obraz kontenera na podstawie źródła w repozytorium, a w następnym samouczku wypchniesz zatwierdzenie do rozwidlenia repozytorium, aby uruchomić automatyczne zadanie.

Utwórz rozwidlenie tego repozytorium: https://github.com/Azure-Samples/acr-build-helloworld-node

![Zrzut ekranu przedstawiający przycisk rozwidlenia (wyróżniony) w serwisie GitHub][quick-build-01-fork]

### <a name="clone-your-fork"></a>Klonowanie rozwidlenia

Po utworzeniu rozwidlenia repozytorium sklonuj rozwidlenie, a następnie wprowadź katalog zawierający klon lokalny.

Sklonuj repozytorium za pomocą `git` usługi, Zastąp ciąg **\<your-github-username\>** nazwą użytkownika serwisu GitHub:

```console
git clone https://github.com/<your-github-username>/acr-build-helloworld-node
```

Wprowadź katalog zawierający kod źródłowy:

```console
cd acr-build-helloworld-node
```

### <a name="bash-shell"></a>Powłoka programu Bash

Polecenia w tym samouczku zostały sformatowane pod kątem powłoki programu Bash. Jeśli wolisz używać programu PowerShell, wiersza polecenia lub innej powłoki, konieczne może być odpowiednie dostosowanie kontynuacji wiersza i formatu zmiennych środowiskowych.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

## <a name="build-in-azure-with-acr-tasks"></a>Kompilowanie na platformie Azure przy użyciu usługi ACR Tasks

Teraz, gdy kod źródłowy został pobrany do komputera, wykonaj następujące kroki, aby utworzyć rejestr kontenerów i skompilować obraz kontenera przy użyciu usługi ACR Tasks.

Aby wykonywanie przykładowych poleceń było łatwiejsze, w tej serii samouczków używaj zmiennych środowiskowych powłoki. Uruchom następujące polecenie, aby ustawić zmienną `ACR_NAME`. Zamień na **\<registry-name\>** unikatową nazwę nowego rejestru kontenerów. Nazwa rejestru musi być unikatowa na platformie Azure, zawierać tylko małe litery i zawierać 5-50 znaków alfanumerycznych. Inne zasoby tworzone w tym samouczku opierają się na tej nazwie, dlatego konieczne powinno być zmodyfikowanie tylko tej pierwszej zmiennej.

```console
ACR_NAME=<registry-name>
```

Po wypełnieniu zmiennej środowiskowej rejestru kontenerów teraz powinno być możliwe skopiowanie i wklejenie pozostałej części poleceń w samouczku bez edytowania wartości. Wykonaj następujące polecenia, aby utworzyć grupę zasobów i rejestr kontenerów:

```azurecli
RES_GROUP=$ACR_NAME # Resource Group name

az group create --resource-group $RES_GROUP --location eastus
az acr create --resource-group $RES_GROUP --name $ACR_NAME --sku Standard --location eastus
```

Teraz, gdy masz rejestr, użyj usługi ACR Tasks do skompilowania obrazu kontenera na podstawie kodu przykładowego. Wykonaj polecenie [az acr build][az-acr-build] w celu przeprowadzenia *szybkiego zadania*:

```azurecli
az acr build --registry $ACR_NAME --image helloacrtasks:v1 .
```

Dane wyjściowe polecenia [az acr build][az-acr-build] są podobne do poniższych. Możesz wyświetlić przekazany kod źródłowy („kontekst”) na platformie Azure i szczegóły operacji `docker build`, którą usługa ACR Tasks uruchamia w chmurze. Ponieważ usługa ACR Tasks używa polecenia `docker build` do kompilowania obrazów, do natychmiastowego rozpoczęcia pracy z usługą ACR Tasks nie są wymagane żadne zmiany plików Dockerfile.

```output
Packing source code into tar file to upload...
Sending build context (4.813 KiB) to ACR...
Queued a build with build ID: da1
Waiting for build agent...
2020/11/18 18:31:42 Using acb_vol_01185991-be5f-42f0-9403-a36bb997ff35 as the home volume
2020/11/18 18:31:42 Setting up Docker configuration...
2020/11/18 18:31:43 Successfully set up Docker configuration
2020/11/18 18:31:43 Logging in to registry: myregistry.azurecr.io
2020/11/18 18:31:55 Successfully logged in
Sending build context to Docker daemon   21.5kB
Step 1/5 : FROM node:15-alpine
15-alpine: Pulling from library/node
Digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
Status: Image is up to date for node:15-alpine
 ---> a56170f59699
Step 2/5 : COPY . /src
 ---> 88087d7e709a
Step 3/5 : RUN cd /src && npm install
 ---> Running in e80e1263ce9a
npm notice created a lockfile as package-lock.json. You should commit this file.
npm WARN helloworld@1.0.0 No repository field.

up to date in 0.1s
Removing intermediate container e80e1263ce9a
 ---> 26aac291c02e
Step 4/5 : EXPOSE 80
 ---> Running in 318fb4c124ac
Removing intermediate container 318fb4c124ac
 ---> 113e157d0d5a
Step 5/5 : CMD ["node", "/src/server.js"]
 ---> Running in fe7027a11787
Removing intermediate container fe7027a11787
 ---> 20a27b90eb29
Successfully built 20a27b90eb29
Successfully tagged myregistry.azurecr.io/helloacrtasks:v1
2020/11/18 18:32:11 Pushing image: myregistry.azurecr.io/helloacrtasks:v1, attempt 1
The push refers to repository [myregistry.azurecr.io/helloacrtasks]
6428a18b7034: Preparing
c44b9827df52: Preparing
172ed8ca5e43: Preparing
8c9992f4e5dd: Preparing
8dfad2055603: Preparing
c44b9827df52: Pushed
172ed8ca5e43: Pushed
8dfad2055603: Pushed
6428a18b7034: Pushed
8c9992f4e5dd: Pushed
v1: digest: sha256:b038dcaa72b2889f56deaff7fa675f58c7c666041584f706c783a3958c4ac8d1 size: 1366
2020/11/18 18:32:43 Successfully pushed image: myregistry.azurecr.io/helloacrtasks:v1
2020/11/18 18:32:43 Step ID acb_step_0 marked as successful (elapsed time in seconds: 15.648945)
The following dependencies were found:
- image:
    registry: myregistry.azurecr.io
    repository: helloacrtasks
    tag: v1
    digest: sha256:b038dcaa72b2889f56deaff7fa675f58c7c666041584f706c783a3958c4ac8d1
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 15-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git: {}

Run ID: da1 was successful after 1m9.970148252s
```

Pod koniec danych wyjściowych usługa ACR Tasks wyświetla odnalezione zależności obrazu. Umożliwia to usłudze ACR Tasks automatyzowanie kompilacji obrazu podczas aktualizacji obrazu podstawowego, na przykład gdy obraz podstawowy jest aktualizowany przy użyciu poprawek systemu operacyjnego lub platformy. Obsługa aktualizacji obrazu podstawowego w usłudze ACR Tasks została omówiona w dalszej części tej serii samouczków.

## <a name="deploy-to-azure-container-instances"></a>Wdrażanie w usłudze Azure Container Instances

Usługa ACR Tasks domyślnie automatycznie wypycha pomyślnie skompilowane obrazy do rejestru, dzięki czemu można natychmiast wdrożyć je z rejestru.

W tej sekcji utworzysz magazyn Azure Key Vault i jednostkę usługi, a następnie wdrożysz kontener w usłudze Azure Container Instances (ACI) przy użyciu poświadczeń jednostki usługi.

### <a name="configure-registry-authentication"></a>Konfigurowanie uwierzytelniania rejestru

Wszystkie scenariusze produkcyjne powinny używać [jednostek usługi][service-principal-auth] do uzyskiwania dostępu do rejestru kontenerów platformy Azure. Jednostki usługi umożliwiają sprawowanie kontroli dostępu opartej na rolach nad obrazami kontenera. Na przykład można skonfigurować jednostkę usługi z dostępem tylko do ściągania do rejestru.

#### <a name="create-a-key-vault"></a>Tworzenie magazynu kluczy

Jeśli nie masz jeszcze magazynu w usłudze [Azure Key Vault](../key-vault/index.yml), utwórz go przy użyciu interfejsu wiersza polecenia platformy Azure przy użyciu poniższych poleceń.

```azurecli
AKV_NAME=$ACR_NAME-vault

az keyvault create --resource-group $RES_GROUP --name $AKV_NAME
```

#### <a name="create-a-service-principal-and-store-credentials"></a>Tworzenie jednostki usługi i przechowywanie poświadczeń

Musisz teraz utworzyć jednostkę usługi i przechowywać jej poświadczenia w magazynie kluczy.

Użyj polecenia [az ad sp create-for-rbac][az-ad-sp-create-for-rbac] do utworzenia jednostki usługi i polecenia [az keyvault secret set][az-keyvault-secret-set] do przechowywania **hasła** jednostki usługi w magazynie:

```azurecli
# Create service principal, store its password in AKV (the registry *password*)
az keyvault secret set \
  --vault-name $AKV_NAME \
  --name $ACR_NAME-pull-pwd \
  --value $(az ad sp create-for-rbac \
                --name $ACR_NAME-pull \
                --scopes $(az acr show --name $ACR_NAME --query id --output tsv) \
                --role acrpull \
                --query password \
                --output tsv)
```

Argument `--role` w poprzednim poleceniu konfiguruje jednostkę usługi z rolą *acrpull*, co spowoduje przyznanie dostępu tylko do ściągania do rejestru. Aby przyznać prawa dostępu do wypychania i ściągania, należy zmienić argument `--role` na wartość *acrpush*.

Następnie przechowuj jednostkę usługi *appId* w magazynie. Jest to **nazwa użytkownika** przekazywana do usługi Azure Container Registry na potrzeby uwierzytelniania:

```azurecli
# Store service principal ID in AKV (the registry *username*)
az keyvault secret set \
    --vault-name $AKV_NAME \
    --name $ACR_NAME-pull-usr \
    --value $(az ad sp show --id http://$ACR_NAME-pull --query appId --output tsv)
```

Utworzono magazyn Azure Key Vault, w którym są przechowywane dwa wpisy tajne:

* `$ACR_NAME-pull-usr`: identyfikator jednostki usługi do użycia jako **nazwa użytkownika** rejestru kontenerów.
* `$ACR_NAME-pull-pwd`: hasło jednostki usługi do użycia jako **hasło** rejestru kontenerów.

Teraz możesz odwoływać się do tych wpisów tajnych według nazwy, gdy Ty lub Twoje aplikacje i usługi ściągają obrazy z rejestru.

### <a name="deploy-a-container-with-azure-cli"></a>Wdrażanie kontenera przy użyciu interfejsu wiersza polecenia platformy Azure

Teraz, gdy poświadczenia główne są przechowywane jako wpisy tajne usługi Azure Key Vault, aplikacje i usługi mogą używać ich do uzyskiwania dostępu do rejestru prywatnego.

Wykonaj polecenie [az container create][az-container-create] w celu wdrożenia wystąpienia kontenera. Polecenie używa poświadczeń jednostki usługi przechowywanych w usłudze Azure Key Vault do uwierzytelniania rejestru kontenerów.

```azurecli
az container create \
    --resource-group $RES_GROUP \
    --name acr-tasks \
    --image $ACR_NAME.azurecr.io/helloacrtasks:v1 \
    --registry-login-server $ACR_NAME.azurecr.io \
    --registry-username $(az keyvault secret show --vault-name $AKV_NAME --name $ACR_NAME-pull-usr --query value -o tsv) \
    --registry-password $(az keyvault secret show --vault-name $AKV_NAME --name $ACR_NAME-pull-pwd --query value -o tsv) \
    --dns-name-label acr-tasks-$ACR_NAME \
    --query "{FQDN:ipAddress.fqdn}" \
    --output table
```

Wartość `--dns-name-label` musi być unikatowa na platformie Azure, dlatego poprzednie polecenie dołącza nazwę rejestru kontenerów do etykiety nazwy DNS kontenera. Dane wyjściowe polecenia wyświetlają w pełni kwalifikowaną nazwę domeny (FQDN) kontenera, na przykład:

```output
FQDN
----------------------------------------------
acr-tasks-myregistry.eastus.azurecontainer.io
```

Zanotuj nazwę FQDN kontenera. Będzie ona używana w następnej sekcji.

### <a name="verify-the-deployment"></a>Weryfikowanie wdrożenia

Aby obejrzeć proces uruchamiania kontenera, użyj polecenia [az container attach][az-container-attach]:

```azurecli
az container attach --resource-group $RES_GROUP --name acr-tasks
```

`az container attach`Dane wyjściowe najpierw wyświetlają stan kontenera podczas ściągania obrazu i zaczynają, a następnie wiążą się z właściwością stdout i stderr konsoli lokalnej z kontenerem.

```output
Container 'acr-tasks' is in state 'Running'...
(count: 1) (last timestamp: 2020-11-18 18:39:10+00:00) pulling image "myregistry.azurecr.io/helloacrtasks:v1"
(count: 1) (last timestamp: 2020-11-18 18:39:15+00:00) Successfully pulled image "myregistry.azurecr.io/helloacrtasks:v1"
(count: 1) (last timestamp: 2020-11-18 18:39:17+00:00) Created container
(count: 1) (last timestamp: 2020-11-18 18:39:17+00:00) Started container

Start streaming logs:
Server running at http://localhost:80
```

Po wyświetleniu elementu `Server running at http://localhost:80` przejdź do nazwy FQDN kontenera w przeglądarce, aby zobaczyć działającą aplikację. Nazwa FQDN powinna zostać wyświetlona w danych wyjściowych polecenia `az container create`, które zostało wykonane w poprzedniej sekcji.

:::image type="content" source="media/container-registry-tutorial-quick-build/quick-build-02-browser.png" alt-text="Przykładowa aplikacja działająca w przeglądarce":::

Aby odłączyć konsolę od kontenera, wybierz pozycję `Control+C`.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Zatrzymaj wystąpienie kontenera przy użyciu polecenia [az container delete][az-container-delete]:

```azurecli
az container delete --resource-group $RES_GROUP --name acr-tasks
```

Aby usunąć *wszystkie* zasoby, które zostały utworzone w tej serii samouczków, w tym rejestr kontenerów, wystąpienie kontenera, magazyn kluczy i jednostkę usługi, należy zastosować poniższe polecenia. Te zasoby są jednak używane w [następnym samouczku](container-registry-tutorial-build-task.md) w serii, więc warto je zachować w przypadku bezpośredniego przechodzenia do następnego samouczka.

```azurecli
az group delete --resource-group $RES_GROUP
az ad sp delete --id http://$ACR_NAME-pull
```

## <a name="next-steps"></a>Następne kroki

Teraz, po przetestowaniu pętli wewnętrznej przy użyciu szybkiego zadania, skonfiguruj **zadanie kompilacji** w celu wyzwolenia kompilacji obrazów kontenera podczas zatwierdzania kodu źródłowego w repozytorium usługi Git:

> [!div class="nextstepaction"]
> [Wyzwalanie kompilacji automatycznych przy użyciu zadań](container-registry-tutorial-build-task.md)

<!-- LINKS - External -->
[sample-archive]: https://github.com/Azure-Samples/acr-build-helloworld-node/archive/master.zip

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-container-attach]: /cli/azure/container#az-container-attach
[az-container-create]: /cli/azure/container#az-container-create
[az-container-delete]: /cli/azure/container#az-container-delete
[az-keyvault-create]: /cli/azure/keyvault/secret#az-keyvault-create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set
[az-login]: /cli/azure/reference-index#az-login
[service-principal-auth]: container-registry-auth-service-principal.md

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
