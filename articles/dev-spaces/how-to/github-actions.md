---
title: Akcje GitHub & usługi Azure Kubernetes (wersja zapoznawcza)
services: azure-dev-spaces
ms.date: 04/03/2020
ms.topic: conceptual
description: Przejrzyj i przetestuj zmiany z żądania ściągnięcia bezpośrednio w usłudze Azure Kubernetes za pomocą akcji usługi GitHub i Azure Dev Spaces
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, kontenery, akcje GitHub, Helm, Siatka usług, routing w sieci usług, polecenia kubectl, k8s
manager: gwallace
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: 9bed61861c80f141270e50b644b32ae42fbe8e77
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92748139"
---
# <a name="github-actions--azure-kubernetes-service-preview"></a>Akcje GitHub & usługi Azure Kubernetes (wersja zapoznawcza)

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

Azure Dev Spaces udostępnia przepływ pracy korzystający z akcji usługi GitHub, które umożliwiają przetestowanie zmian z żądania ściągnięcia bezpośrednio w AKS przed scaleniem żądania ściągnięcia z gałęzią główną repozytorium. Mając działającą aplikację, aby przeglądać zmiany żądania ściągnięcia, można zwiększyć zaufanie zarówno dla deweloperów, jak i członków zespołu. Ta działająca aplikacja może również pomóc członkom zespołu, takim jak Menedżerowie produktów i projektanci, stać się częścią procesu przeglądu podczas wczesnych etapów programowania.

Niniejszy przewodnik zawiera informacje na temat wykonywania następujących czynności:

* Skonfiguruj Azure Dev Spaces w zarządzanym klastrze Kubernetes na platformie Azure.
* Wdróż dużą aplikację z wieloma mikrousługami w miejscu dev.
* Konfigurowanie ciągłej integracji/ciągłego wdrażania za pomocą akcji usługi GitHub.
* Przetestuj pojedynczą mikrousługę w izolowanym miejscu dev w kontekście pełnej aplikacji.

> [!IMPORTANT]
> Ta funkcja jest obecnie w wersji zapoznawczej. Wersje zapoznawcze są udostępniane pod warunkiem udzielenia zgody na [dodatkowe warunki użytkowania](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Niektóre cechy funkcji mogą ulec zmianie, zanim stanie się ona ogólnie dostępna.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/free).
* [Zainstalowany interfejs wiersza polecenia platformy Azure][azure-cli-installed].
* [Helm 3][helm-installed].
* Konto usługi GitHub z [włączonymi akcjami usługi GitHub][github-actions-beta-signup].
* [Przykładowa aplikacja do udostępniania Azure dev Spaces roweru](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp/README.md) uruchamiana w klastrze AKS.

## <a name="create-an-azure-container-registry"></a>Tworzenie rejestru Azure Container Registry

Utwórz Azure Container Registry (ACR):

```azurecli
az acr create --resource-group MyResourceGroup --name <acrName> --sku Basic
```

> [!IMPORTANT]
> Nazwa ACR musi być unikatowa w ramach platformy Azure i zawierać 5-50 znaków alfanumerycznych. Wszystkie używane litery muszą być małymi literami.

Zapisz wartość *loginServer* z danych wyjściowych, ponieważ jest ona używana w późniejszym kroku.

## <a name="create-a-service-principal-for-authentication"></a>Tworzenie jednostki usługi na potrzeby uwierzytelniania

Użyj [AZ AD Sp Create-for-RBAC][az-ad-sp-create-for-rbac] , aby utworzyć nazwę główną usługi. Na przykład:

```azurecli
az ad sp create-for-rbac --sdk-auth --skip-assignment
```

Zapisz dane wyjściowe JSON, ponieważ są używane w późniejszym kroku.

Użyj [AZ AKS show][az-aks-show] , aby wyświetlić *Identyfikator* klastra AKS:

```azurecli
az aks show -g MyResourceGroup -n MyAKS  --query id
```

Użyj [AZ ACR show][az-acr-show] , aby wyświetlić *Identyfikator* ACR:

```azurecli
az acr show --name <acrName> --query id
```

Użyj [AZ role przypisanie Create][az-role-assignment-create] , aby dać *współautor* dostęp do klastra AKS i *AcrPush* dostęp do ACR.

```azurecli
az role assignment create --assignee <ClientId> --scope <AKSId> --role Contributor
az role assignment create --assignee <ClientId>  --scope <ACRId> --role AcrPush
```

> [!IMPORTANT]
> Aby zapewnić jednostce usługi dostęp do tych zasobów, musisz być właścicielem klastra AKS i ACR.

## <a name="configure-your-github-action"></a>Skonfiguruj akcję GitHub

> [!IMPORTANT]
> Musisz mieć włączone akcje usługi GitHub dla repozytorium. Aby włączyć akcje usługi GitHub dla repozytorium, przejdź do repozytorium w serwisie GitHub, kliknij kartę akcje, a następnie Włącz akcje dla tego repozytorium.

Przejdź do repozytorium z rozwidleniem, a następnie kliknij pozycję *Ustawienia* . Kliknij pozycję wpisy *tajne* na lewym pasku bocznym. Kliknij pozycję *Dodaj nowy klucz tajny* , aby dodać każde nowe hasło poniżej:

1. *AZURE_CREDENTIALS* : wszystkie dane wyjściowe z tworzenia jednostki usługi.
1. *RESOURCE_GROUP* : Grupa zasobów dla klastra AKS, która w tym przykładzie jest grupą *zasobów* .
1. *CLUSTER_NAME* : Nazwa klastra AKS, która jest w tym przykładzie *MyAKS* .
1. *CONTAINER_REGISTRY* : *loginServer* dla ACR.
1. *Host* : Host dla obszaru deweloperskiego, który przyjmuje formularz *<MASTER_SPACE>. <APP_NAME>. <HOST_SUFFIX* >, który jest w tym przykładzie *dev.bikesharingweb.fedcab0987.EUS.azds.IO* .
1. *IMAGE_PULL_SECRET* : Nazwa klucza tajnego, którego chcesz użyć, na przykład *Demonstracja-wpis tajny* .
1. *MASTER_SPACE* : Nazwa nadrzędnego obszaru deweloperskiego, który w tym przykładzie jest *deweloperem* .
1. *REGISTRY_USERNAME* : *clientId* z danych wyjściowych JSON z tworzenia jednostki usługi.
1. *REGISTRY_PASSWORD* : *clientSecret* z danych wyjściowych JSON z tworzenia jednostki usługi.

> [!NOTE]
> Wszystkie te wpisy tajne są używane przez akcję GitHub i są konfigurowane w serwisie [GitHub/Workflows/Bikes. yml][github-action-yaml].

Opcjonalnie, jeśli chcesz zaktualizować miejsce główne po scaleniu żądania ściągnięcia, Dodaj *GATEWAY_HOST* klucz tajny, który pobiera formularz *<MASTER_SPACE>. Gateway. <* HOST_SUFFIX>, który w tym przykładzie jest *dev.Gateway.fedcab0987.EUS.azds.IO* . Po scaleniu zmian w gałęzi głównej w rozwidleniu zostanie uruchomiona kolejna Akcja w celu odbudowania i uruchomienia całej aplikacji w głównym obszarze dev. W tym przykładzie obszarem głównym jest *dev* . Ta akcja jest konfigurowana w witrynie [GitHub/Workflows/bikesharing. yml][github-action-bikesharing-yaml].

Ponadto jeśli chcesz, aby zmiany w żądaniu ściągnięcia były uruchamiane w miejscu grandchild, zaktualizuj *MASTER_SPACE* i wpisy tajne *hosta* . Na przykład jeśli aplikacja działa w środowisku *deweloperskim* z przestrzenią podrzędną *dev/azureuser1* , aby można było uruchomić żądanie ściągnięcia w miejscu podrzędnym *dev/azureuser1* :

* Zaktualizuj *MASTER_SPACE* do obszaru podrzędnego, który ma być miejscem nadrzędnym, w tym przykładzie *azureuser1* .
* Zaktualizuj *hosta* do *<GRANDPARENT_SPACE>. <APP_NAME>. <* HOST_SUFFIX>, w tym przykładzie *dev.bikesharingweb.fedcab0987.EUS.azds.IO* .

## <a name="create-a-new-branch-for-code-changes"></a>Utwórz nową gałąź dla zmian kodu

Przejdź do `BikeSharingApp/` i Utwórz nowe rozgałęzienie o nazwie *rower-images* .

```cmd
cd dev-spaces/samples/BikeSharingApp/
git checkout -b bike-images
```

Edytuj [rowery/server.js][bikes-server-js] , aby usunąć wiersze 232 i 233:

```javascript
    // Hard code image url *FIX ME*
    theBike.imageUrl = "/static/logo.svg";
```

Sekcja powinna teraz wyglądać następująco:

```javascript
    var theBike = result;
    theBike.id = theBike._id;
    delete theBike._id;
```

Zapisz plik, a następnie użyj `git add` i `git commit` w celu przygotowania zmian.

```cmd
git add Bikes/server.js 
git commit -m "Removing hard coded imageUrl from /bikes/:id route"
```

## <a name="push-your-changes"></a>Wypchnij zmiany

Użyj polecenia `git push` , aby wypchnąć nową gałąź do repozytorium z rozwidleniem:

```cmd
git push origin bike-images
```

Po zakończeniu wypychania przejdź do repozytorium z rozwidleniem w witrynie GitHub, aby utworzyć żądanie ściągnięcia z gałęzią *główną* w repozytorium rozwidlenia jako gałąź bazową w porównaniu z gałęzią *rower-images* .

Po otwarciu żądania ściągnięcia przejdź do karty *Akcje* . Sprawdź, czy nowa akcja została uruchomiona i kompiluje usługę *Bikes* .

## <a name="view-the-child-space-with-your-changes"></a>Wyświetl obszar podrzędny ze zmianami

Po zakończeniu akcji zobaczysz komentarz z adresem URL do nowego miejsca podrzędnego na podstawie zmian w żądaniu ściągnięcia.

> [!div class="mx-imgBorder"]
> ![Adres URL akcji usługi GitHub](../media/github-actions/github-action-url.png)

Przejdź do usługi *bikesharingweb* , otwierając adres URL z komentarza. Wybierz pozycję *Aurelia Briggs (Customer)* jako użytkownik, a następnie wybierz rower do wynajęcia. Sprawdź, czy obraz symbolu zastępczego dla roweru nie jest już widoczny.

W przypadku scalenia zmian w gałęzi *głównej* w rozwidleniu zostanie uruchomiona kolejna Akcja w celu odbudowania i uruchomienia całej aplikacji w nadrzędnym obszarze dev. W tym przykładzie przestrzeń nadrzędna jest *deweloperem* . Ta akcja jest konfigurowana w witrynie [GitHub/Workflows/bikesharing. yml][github-action-bikesharing-yaml].

## <a name="clean-up-your-azure-resources"></a>Czyszczenie zasobów platformy Azure

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o tym, jak działa Azure Dev Spaces.

> [!div class="nextstepaction"]
> [Jak działa usługa Azure Dev Spaces](../how-dev-spaces-works.md)

[azure-cli-installed]: /cli/azure/install-azure-cli?view=azure-cli-latest
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-aks-show]: /cli/azure/aks?view=azure-cli-latest#az-aks-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
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
