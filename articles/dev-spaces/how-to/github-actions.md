---
title: GitHub Actions & Azure Kubernetes Service (wersja zapoznawcza)
services: azure-dev-spaces
ms.date: 04/03/2020
ms.topic: conceptual
description: Przeglądanie i testowanie zmian z żądania ściągnięć bezpośrednio w Azure Kubernetes Service przy użyciu GitHub Actions i Azure Dev Spaces
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, GitHub Actions, Helm, service mesh, service mesh routing, kubectl, k8s
manager: gwallace
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: 84d4f94cdb756b0bc11026baaa3acf065604c421
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777549"
---
# <a name="github-actions--azure-kubernetes-service-preview"></a>GitHub Actions & Azure Kubernetes Service (wersja zapoznawcza)

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

Azure Dev Spaces udostępnia przepływ pracy przy użyciu usługi GitHub Actions, który umożliwia testowanie zmian z żądania ściągnnięcia bezpośrednio w u usługi AKS przed scaleniem żądania ściągnnięcia z główną gałęzią repozytorium. Posiadanie uruchomionej aplikacji do przeglądania zmian żądania ściągnnięcia może zwiększyć zaufanie zarówno dewelopera, jak i członków zespołu. Ta uruchomiona aplikacja może również pomóc członkom zespołu, takim jak menedżerowie produktów i projektanci, stać się częścią procesu przeglądu na wczesnych etapach opracowywania.

Niniejszy przewodnik zawiera informacje na temat wykonywania następujących czynności:

* Skonfiguruj Azure Dev Spaces w zarządzanym klastrze Kubernetes na platformie Azure.
* Wdrażanie dużej aplikacji z wieloma mikrousługami w przestrzeni dewelopera.
* Konfigurowanie usługi CI/CD za pomocą akcji usługi GitHub.
* Przetestuj pojedynczą mikrousługę w izolowanym obszarze dewelopera w kontekście pełnej aplikacji.

> [!IMPORTANT]
> Ta funkcja jest obecnie w wersji zapoznawczej. Wersje zapoznawcze są udostępniane pod warunkiem udzielenia zgody na [dodatkowe warunki użytkowania](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Niektóre cechy funkcji mogą ulec zmianie, zanim stanie się ona ogólnie dostępna.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/free).
* [Zainstalowany interfejs wiersza polecenia platformy Azure][azure-cli-installed].
* [Program Helm 3 zainstalował program][helm-installed].
* Konto usługi GitHub z [włączoną GitHub Actions usługą][github-actions-beta-signup].
* Przykładowa [Azure Dev Spaces bike sharing uruchomiona](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp/README.md) w klastrze usługi AKS.

## <a name="create-an-azure-container-registry"></a>Tworzenie rejestru Azure Container Registry

Utwórz Azure Container Registry (ACR):

```azurecli
az acr create --resource-group MyResourceGroup --name <acrName> --sku Basic
```

> [!IMPORTANT]
> Nazwa usługi ACR musi być unikatowa w obrębie platformy Azure i zawierać od 5 do 50 znaków alfanumerycznych. Wszystkie litery, których używasz, muszą być małe.

Zapisz wartość *loginServer* z danych wyjściowych, ponieważ zostanie ona użyta w późniejszym kroku.

## <a name="create-a-service-principal-for-authentication"></a>Tworzenie jednostki usługi do uwierzytelniania

Użyj [az ad sp create-for-rbac,][az-ad-sp-create-for-rbac] aby utworzyć jednostkę usługi. Na przykład:

```azurecli
az ad sp create-for-rbac --sdk-auth --skip-assignment
```

Zapisz dane wyjściowe JSON, ponieważ będą używane w późniejszym kroku.

Użyj [az aks show,][az-aks-show] aby *wyświetlić identyfikator* klastra usługi AKS:

```azurecli
az aks show -g MyResourceGroup -n MyAKS  --query id
```

Użyj [az acr show,][az-acr-show] aby *wyświetlić identyfikator* ACR:

```azurecli
az acr show --name <acrName> --query id
```

Użyj [az role assignment create,][az-role-assignment-create] aby udzielić *współautorowi* dostępu do klastra usługi AKS i *dostępu usługi AcrPush* do usługi ACR.

```azurecli
az role assignment create --assignee <ClientId> --scope <AKSId> --role Contributor
az role assignment create --assignee <ClientId>  --scope <ACRId> --role AcrPush
```

> [!IMPORTANT]
> Musisz być właścicielem klastra usługi AKS i usługi ACR, aby udzielić jednostki usługi dostępu do tych zasobów.

## <a name="configure-your-github-action"></a>Konfigurowanie akcji usługi GitHub

> [!IMPORTANT]
> Musisz mieć włączoną GitHub Actions repozytorium. Aby włączyć GitHub Actions repozytorium, przejdź do repozytorium w witrynie GitHub, kliknij kartę Akcje i wybierz opcję włączenia akcji dla tego repozytorium.

Przejdź do swojego repozytorium z widełami i kliknij pozycję *Ustawienia.* Kliknij pozycję *Wpisy tajne* na lewym pasku bocznym. Kliknij *pozycję Dodaj nowy klucz tajny,* aby dodać każdy nowy klucz tajny poniżej:

1. *AZURE_CREDENTIALS:* wszystkie dane wyjściowe z tworzenia jednostki usługi.
1. *RESOURCE_GROUP:* grupa zasobów klastra usługi AKS, która w tym przykładzie to *MyResourceGroup.*
1. *CLUSTER_NAME:* nazwa klastra usługi AKS, która w tym przykładzie to *MyAKS.*
1. *CONTAINER_REGISTRY:* *loginServer* dla usługi ACR.
1. *HOST:* host przestrzeni dewelopera, który ma postać *<MASTER_SPACE>.<APP_NAME>.<HOST_SUFFIX>*, który w tym przykładzie *jest dev.bikesharingweb.fedcab0987.eus.azds.io*.
1. *IMAGE_PULL_SECRET:* nazwa tajnego, który chcesz użyć, na przykład *demo-secret*.
1. *MASTER_SPACE:* nazwa nadrzędnej przestrzeni dewelopera, która w tym przykładzie to *dev*.
1. *REGISTRY_USERNAME:* *clientId z* danych wyjściowych JSON z tworzenia jednostki usługi.
1. *REGISTRY_PASSWORD:* *clientSecret z* danych wyjściowych JSON z tworzenia jednostki usługi.

> [!NOTE]
> Wszystkie te wpisy tajne są używane przez akcję usługi GitHub i konfigurowane w pliku [.github/workflows/bikes.yml.][github-action-yaml]

Opcjonalnie, jeśli chcesz zaktualizować przestrzeń wzorcową po scaleniu  twojego pryzgodu, dodaj wpis tajny GATEWAY_HOST, który ma postać *<MASTER_SPACE>.gateway.<HOST_SUFFIX>,* która w tym przykładzie jest dev.gateway.fedcab0987.eus.azds.io *.* Po scaleniu zmian z gałęzią główną w twoim widełku zostanie uruchomione kolejne działanie w celu ponownego skompilowania i uruchomienia całej aplikacji w głównej przestrzeni dewelopera. W tym przykładzie przestrzeń główna to *dev*. Ta akcja jest skonfigurowana w [pliku .github/workflows/bikesharing.yml.][github-action-bikesharing-yaml]

Ponadto jeśli chcesz, aby zmiany w twoim pniu ściągnięciem  działały w przestrzeni grandchild, zaktualizuj wpisy tajne MASTER_SPACE *HOST.* Jeśli na przykład aplikacja jest  uruchomiona w programie dev z przestrzenią podrzędną *dev/azureuser1,* aby można było uruchomić to wymaganie ściągnięcie w przestrzeni podrzędnej *dev/azureuser1:*

* Zaktualizuj *MASTER_SPACE* do przestrzeni podrzędnej, która ma być przestrzenią nadrzędną, w tym przykładzie *azureuser1*.
* Zaktualizuj *host do* *<GRANDPARENT_SPACE>.<APP_NAME>.<HOST_SUFFIX>*, w tym *przykładzie* dev.bikesharingweb.fedcab0987.eus.azds.io .

## <a name="create-a-new-branch-for-code-changes"></a>Tworzenie nowej gałęzi dla zmian kodu

Przejdź do `BikeSharingApp/` i utwórz nową gałąź *o nazwie bike-images.*

```cmd
cd dev-spaces/samples/BikeSharingApp/
git checkout -b bike-images
```

Edytuj [rowery/server.js, ][bikes-server-js] aby usunąć wiersze 232 i 233:

```javascript
    // Hard code image url *FIX ME*
    theBike.imageUrl = "/static/logo.svg";
```

Sekcja powinna teraz wyglądać tak:

```javascript
    var theBike = result;
    theBike.id = theBike._id;
    delete theBike._id;
```

Zapisz plik, a następnie użyj funkcji `git add` i , aby wprowadzić `git commit` zmiany.

```cmd
git add Bikes/server.js 
git commit -m "Removing hard coded imageUrl from /bikes/:id route"
```

## <a name="push-your-changes"></a>Wypychanie zmian

Użyj `git push` , aby wypchnąć nową gałąź do repozytorium z fordem:

```cmd
git push origin bike-images
```

Po zakończeniu wypychania przejdź do repozytorium z rozdęciem w usłudze GitHub, aby utworzyć żądanie ściągnnięcia z gałęzią *main* w twoim repozytorium rozdętą jako gałęzią bazową w porównaniu z gałęzią *bike-images.*

Po otwarciu żądania ściągnięcie przejdź do *karty* Akcje. Sprawdź, czy została uruchomiona nowa akcja i czy jest budowania *usługi Bikes.*

## <a name="view-the-child-space-with-your-changes"></a>Wyświetlanie przestrzeni podrzędnej ze zmianami

Po zakończeniu akcji zobaczysz komentarz z adresem URL do nowej przestrzeni podrzędnej na podstawie zmian w żądaniu ściągnięcie.

> [!div class="mx-imgBorder"]
> ![Adres URL akcji usługi GitHub](../media/github-actions/github-action-url.png)

Przejdź do usługi *bikesharingweb,* otwierając adres URL w komentarzu. Wybierz *użytkownika Aurelia Briggs (klient),* a następnie wybierz rower do wypożyczenia. Sprawdź, czy nie widzisz już obrazu zastępczego dla roweru.

Jeśli scalisz zmiany  z główną gałęzią w twoim widełku, zostanie uruchomione kolejne działanie w celu ponownego skompilowania i uruchomienia całej aplikacji w nadrzędnej przestrzeni dewelopera. W tym przykładzie przestrzeń nadrzędna to *dev*. Ta akcja jest skonfigurowana w pliku [.github/workflows/bikesharing.yml.][github-action-bikesharing-yaml]

## <a name="clean-up-your-azure-resources"></a>Czyszczenie zasobów platformy Azure

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o tym, Azure Dev Spaces działa.

> [!div class="nextstepaction"]
> [Jak działa usługa Azure Dev Spaces](../how-dev-spaces-works.md)

[azure-cli-installed]: /cli/azure/install-azure-cli
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[bikes-server-js]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/server.js#L232-L233
[bike-sharing-gh]: https://github.com/Azure/dev-spaces/
[bike-sharing-values-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml
[github-actions-beta-signup]: https://github.com/features/actions
[github-action-yaml]: https://github.com/Azure/dev-spaces/blob/master/.github/workflows/bikes.yml
[github-action-bikesharing-yaml]: https://github.com/Azure/dev-spaces/blob/master/.github/workflows/bikesharing.yml
[helm-installed]: https://helm.sh/docs/intro/install/
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[sp-acr]: ../../container-registry/container-registry-auth-service-principal.md
[sp-aks]: ../../aks/kubernetes-service-principal.md
