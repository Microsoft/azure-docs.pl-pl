---
title: Wdrażanie wystąpienia kontenera przez akcję usługi GitHub
description: Konfigurowanie akcji usługi GitHub, która automatyzuje kroki kompilowania, wypychania i wdrażania obrazu kontenera w Azure Container Instances
ms.topic: article
ms.date: 08/20/2020
ms.custom: github-actions-azure, devx-track-azurecli
ms.openlocfilehash: df8f7a546767f1198cee6fc6ceb6032645c14989
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107868991"
---
# <a name="configure-a-github-action-to-create-a-container-instance"></a>Konfigurowanie akcji usługi GitHub w celu utworzenia wystąpienia kontenera

[GitHub Actions](https://docs.github.com/en/actions) to pakiet funkcji w usłudze GitHub, które automatyzują przepływy pracy tworzenia oprogramowania w tym samym miejscu, w którym przechowujesz kod oraz współpracujesz nad żądaniami ściągnięć i problemami.

Użyj akcji [Deploy to Azure Container Instances](https://github.com/azure/aci-deploy) GitHub (Wdrażanie w usłudze GitHub), aby zautomatyzować wdrażanie pojedynczego kontenera w Azure Container Instances. Akcja umożliwia ustawienie właściwości wystąpienia kontenera podobnych do tych w [poleceniu az container create.][az-container-create]

W tym artykule pokazano, jak skonfigurować przepływ pracy w repozytorium GitHub, który wykonuje następujące akcje:

* Kompilowanie obrazu z pliku Dockerfile
* Wypychanie obrazu do rejestru kontenerów platformy Azure
* Wdrażanie obrazu kontenera w wystąpieniu kontenera platformy Azure

W tym artykule przedstawiono dwa sposoby skonfigurowania przepływu pracy:

* [Konfigurowanie przepływu pracy usługi GitHub](#configure-github-workflow) — tworzenie przepływu pracy w repozytorium GitHub przy użyciu akcji Wd wdrażaj Azure Container Instances i innych akcji.  
* [Użyj rozszerzenia interfejsu](#use-deploy-to-azure-extension) wiersza polecenia — `az container app up` użyj polecenia w rozszerzeniu Deploy to Azure (Wdrażanie na [platformie Azure)](https://github.com/Azure/deploy-to-azure-cli-extension) w interfejsie wiersza polecenia platformy Azure. To polecenie usprawnia tworzenie przepływu pracy usługi GitHub i kroków wdrażania.

> [!IMPORTANT]
> Akcja usługi GitHub dla usługi Azure Container Instances jest obecnie w wersji zapoznawczej. Wersje zapoznawcze są udostępniane pod warunkiem udzielenia zgody na [dodatkowe warunki użytkowania][terms-of-use]. Niektóre cechy funkcji mogą ulec zmianie, zanim stanie się ona ogólnie dostępna.

## <a name="prerequisites"></a>Wymagania wstępne

* **Konto usługi GitHub** — utwórz konto, https://github.com jeśli jeszcze go nie masz.
* **Interfejs wiersza polecenia** platformy Azure — możesz użyć Azure Cloud Shell lub lokalnej instalacji interfejsu wiersza polecenia platformy Azure, aby wykonać kroki interfejsu wiersza polecenia platformy Azure. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].
* **Rejestr kontenerów platformy Azure** — jeśli go [nie masz,](../container-registry/container-registry-get-started-portal.md)utwórz rejestr kontenerów platformy Azure w warstwie Podstawowa przy użyciu interfejsu wiersza polecenia platformy [Azure,](../container-registry/container-registry-get-started-azure-cli.md)usługi Azure Portal lub innych metod. Zanotuj grupę zasobów używaną do wdrożenia, która jest używana w przepływie pracy usługi GitHub.

## <a name="set-up-repo"></a>Konfigurowanie repo

* W przykładach w tym artykule użyj usługi GitHub, aby wydychać następujące repozytorium: https://github.com/Azure-Samples/acr-build-helloworld-node

  To repo zawiera plik Dockerfile i pliki źródłowe służące do tworzenia obrazu kontenera małej aplikacji internetowej.

  ![Zrzut ekranu przedstawiający przycisk rozwidlenia (wyróżniony) w serwisie GitHub](../container-registry/media/container-registry-tutorial-quick-build/quick-build-01-fork.png)

* Upewnij się, że dla repozytorium włączono akcje. Przejdź do repozytorium z forkedem i wybierz **pozycję Ustawienia** Actions  >  **(Akcje ustawień).** W **uprawnieniach Akcje** upewnij się, że wybrano opcję Włącz akcje lokalne i akcje innych firm dla **tego** repozytorium.

## <a name="configure-github-workflow"></a>Konfigurowanie przepływu pracy usługi GitHub

### <a name="create-service-principal-for-azure-authentication"></a>Tworzenie jednostki usługi do uwierzytelniania platformy Azure

W przepływie pracy usługi GitHub musisz podać poświadczenia platformy Azure, aby uwierzytelnić się w interfejsie wiersza polecenia platformy Azure. Poniższy przykład tworzy jednostkę usługi z rolą Współautor o zakresie grupy zasobów dla rejestru kontenerów.

Najpierw pobierz identyfikator zasobu grupy zasobów. Zastąp nazwę grupy następującym poleceniem [az group show:][az-group-show]

```azurecli
groupId=$(az group show \
  --name <resource-group-name> \
  --query id --output tsv)
```

Użyj [az ad sp create-for-rbac,][az-ad-sp-create-for-rbac] aby utworzyć jednostkę usługi:

```azurecli
az ad sp create-for-rbac \
  --scope $groupId \
  --role Contributor \
  --sdk-auth
```

Dane wyjściowe są podobne do następujących:

```json
{
  "clientId": "xxxx6ddc-xxxx-xxxx-xxx-ef78a99dxxxx",
  "clientSecret": "xxxx79dc-xxxx-xxxx-xxxx-aaaaaec5xxxx",
  "subscriptionId": "xxxx251c-xxxx-xxxx-xxxx-bf99a306xxxx",
  "tenantId": "xxxx88bf-xxxx-xxxx-xxxx-2d7cd011xxxx",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "activeDirectoryGraphResourceId": "https://graph.windows.net/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```

Zapisz dane wyjściowe JSON, ponieważ będą używane w późniejszym kroku. Ponadto zanotuj `clientId` , który należy zaktualizować jednostkę usługi w następnej sekcji.

### <a name="update-service-principal-for-registry-authentication"></a>Aktualizowanie jednostki usługi na celu uwierzytelnianie rejestru

Zaktualizuj poświadczenia jednostki usługi platformy Azure, aby zezwolić na dostęp wypychania i ściągania do rejestru kontenerów. Ten krok umożliwia przepływowi pracy usługi [](../container-registry/container-registry-auth-service-principal.md) GitHub użycie jednostki usługi do uwierzytelniania w rejestrze kontenerów oraz wypychania i ściągania obrazu platformy Docker. 

Pobierz identyfikator zasobu rejestru kontenerów. Zastąp nazwę rejestru następującym poleceniem [az acr show:][az-acr-show]

```azurecli
registryId=$(az acr show \
  --name <registry-name> \
  --query id --output tsv)
```

Użyj [az role assignment create,][az-role-assignment-create] aby przypisać rolę AcrPush, która zapewnia dostęp wypychania i ściągania do rejestru. Zastąp identyfikator klienta jednostki usługi:

```azurecli
az role assignment create \
  --assignee <ClientId> \
  --scope $registryId \
  --role AcrPush
```

### <a name="save-credentials-to-github-repo"></a>Zapisywanie poświadczeń w repozytorium GitHub

1. W interfejsie użytkownika usługi GitHub przejdź do repozytorium z forkedem i wybierz pozycję **Wpisy**  >  **tajne ustawień.** 

1. Wybierz **pozycję Dodaj nowy wpis tajny,** aby dodać następujące wpisy tajne:

|Wpis tajny  |Wartość  |
|---------|---------|
|`AZURE_CREDENTIALS`     | Wszystkie dane wyjściowe JSON z kroku tworzenia jednostki usługi |
|`REGISTRY_LOGIN_SERVER`   | Nazwa serwera logowania rejestru (wszystkie małe litery). Przykład: *myregistry.azurecr.io*        |
|`REGISTRY_USERNAME`     |  Z `clientId` danych wyjściowych JSON z tworzenia jednostki usługi       |
|`REGISTRY_PASSWORD`     |  Z `clientSecret` danych wyjściowych JSON z tworzenia jednostki usługi |
| `RESOURCE_GROUP` | Nazwa grupy zasobów użytej do zastosowania zakresu jednostki usługi |

### <a name="create-workflow-file"></a>Tworzenie pliku przepływu pracy

1. W interfejsie użytkownika usługi GitHub wybierz pozycję **Akcje Nowy**  >  **przepływ pracy.**
1. Wybierz **samodzielnie pozycję Skonfiguruj przepływ pracy.**
1. W **obszarze Edytuj nowy plik** wklej następującą zawartość YAML, aby zastąpić przykładowy kod. Zaakceptuj domyślną nazwę `main.yml` pliku lub podaj nazwę pliku.
1. Wybierz **pozycję Rozpocznij zatwierdzanie**, opcjonalnie podaj krótkie i rozszerzone opisy zatwierdzenia, a następnie wybierz **pozycję Zat zatwierdzeniu nowego pliku.**

```yml
on: [push]
name: Linux_Container_Workflow

jobs:
    build-and-deploy:
        runs-on: ubuntu-latest
        steps:
        # checkout the repo
        - name: 'Checkout GitHub Action'
          uses: actions/checkout@main
          
        - name: 'Login via Azure CLI'
          uses: azure/login@v1
          with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}
        
        - name: 'Build and push image'
          uses: azure/docker-login@v1
          with:
            login-server: ${{ secrets.REGISTRY_LOGIN_SERVER }}
            username: ${{ secrets.REGISTRY_USERNAME }}
            password: ${{ secrets.REGISTRY_PASSWORD }}
        - run: |
            docker build . -t ${{ secrets.REGISTRY_LOGIN_SERVER }}/sampleapp:${{ github.sha }}
            docker push ${{ secrets.REGISTRY_LOGIN_SERVER }}/sampleapp:${{ github.sha }}

        - name: 'Deploy to Azure Container Instances'
          uses: 'azure/aci-deploy@v1'
          with:
            resource-group: ${{ secrets.RESOURCE_GROUP }}
            dns-name-label: ${{ secrets.RESOURCE_GROUP }}${{ github.run_number }}
            image: ${{ secrets.REGISTRY_LOGIN_SERVER }}/sampleapp:${{ github.sha }}
            registry-login-server: ${{ secrets.REGISTRY_LOGIN_SERVER }}
            registry-username: ${{ secrets.REGISTRY_USERNAME }}
            registry-password: ${{ secrets.REGISTRY_PASSWORD }}
            name: aci-sampleapp
            location: 'west us'
```

### <a name="validate-workflow"></a>Weryfikowanie przepływu pracy

Po zatwierdzeniu pliku przepływu pracy przepływ pracy jest wyzwalany. Aby przejrzeć postęp przepływu pracy, przejdź do **akcji**  >  **Przepływy pracy.** 

![Wyświetlanie postępu przepływu pracy](./media/container-instances-github-action/github-action-progress.png)

Zobacz [Wyświetlanie historii przebiegów przepływu](https://docs.github.com/en/actions/managing-workflow-runs/viewing-workflow-run-history) pracy, aby uzyskać informacje o wyświetlaniu stanu i wyników każdego kroku w przepływie pracy. Jeśli przepływ pracy nie zostanie ukończony, zobacz [Wyświetlanie dzienników, aby zdiagnozować błędy.](https://docs.github.com/en/actions/managing-workflow-runs/using-workflow-run-logs#viewing-logs-to-diagnose-failures)

Po pomyślnym zakończeniu przepływu pracy uzyskaj informacje o wystąpieniu kontenera o nazwie *aci-sampleapp,* uruchamiając [polecenie az container show.][az-container-show] Zastąp nazwę grupy zasobów: 

```azurecli
az container show \
  --resource-group <resource-group-name> \
  --name aci-sampleapp \
  --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" \
  --output table
```

Dane wyjściowe są podobne do następujących:

```console
FQDN                                   ProvisioningState
---------------------------------      -------------------
aci-action01.westus.azurecontainer.io  Succeeded
```

Po aprowieniu wystąpienia przejdź do sieci FQDN kontenera w przeglądarce, aby wyświetlić uruchamianą aplikację internetową.

![Uruchamianie aplikacji internetowej w przeglądarce](./media/container-instances-github-action/github-action-container.png)

## <a name="use-deploy-to-azure-extension"></a>Używanie rozszerzenia Deploy to Azure (Wdrażanie na platformie Azure)

Możesz też skonfigurować przepływ pracy za pomocą rozszerzenia [Deploy to Azure (Wd wdrażaj](https://github.com/Azure/deploy-to-azure-cli-extension) na platformie Azure) w interfejsie wiersza polecenia platformy Azure. Polecenie w rozszerzeniu pobiera parametry wejściowe, aby skonfigurować przepływ pracy do `az container app up` wdrożenia w Azure Container Instances. 

Przepływ pracy utworzony przez interfejs wiersza polecenia platformy Azure jest podobny do przepływu pracy, który można utworzyć [ręcznie przy użyciu usługi GitHub.](#configure-github-workflow)

### <a name="additional-prerequisite"></a>Dodatkowe wymaganie wstępne

Oprócz wymagań wstępnych [i](#prerequisites) konfiguracji [w tym](#set-up-repo) scenariuszu należy zainstalować rozszerzenie Deploy to  **Azure dla** interfejsu wiersza polecenia platformy Azure.

Uruchom polecenie [az extension add,][az-extension-add] aby zainstalować rozszerzenie:

```azurecli
az extension add \
  --name deploy-to-azure
```

Aby uzyskać informacje na temat znajdowania i instalowania rozszerzeń oraz zarządzania nimi, zobacz [Używanie rozszerzeń z interfejsem wiersza polecenia platformy Azure.](/cli/azure/azure-cli-extensions-overview)

### <a name="run-az-container-app-up"></a>Uruchom polecenie `az container app up`

Aby uruchomić polecenie [az container app up,][az-container-app-up] podaj co najmniej:

* Nazwa rejestru kontenerów platformy Azure, na przykład *myregistry*
* Adres URL repozytorium GitHub, na przykład `https://github.com/<your-GitHub-Id>/acr-build-helloworld-node`

Przykładowe polecenie:

```azurecli
az container app up \
  --acr myregistry \
  --repository https://github.com/myID/acr-build-helloworld-node
```

### <a name="command-progress"></a>Postęp polecenia

* Po wyświetleniu monitu podaj poświadczenia usługi GitHub lub podaj osobisty  token dostępu  usługi GitHub z [zakresami](https://docs.github.com/en/github/authenticating-to-github/creating-a-personal-access-token) repozytorium i użytkownika na potrzeby uwierzytelniania przy użyciu konta usługi GitHub. Jeśli poświadczenie usługi GitHub zostanie podać, polecenie utworzy dla Ciebie pat dostępu. Postępuj zgodnie z dodatkowymi monitami, aby skonfigurować przepływ pracy.

* Polecenie tworzy wpisy tajne repo dla przepływu pracy:

  * Poświadczenia jednostki usługi dla interfejsu wiersza polecenia platformy Azure
  * Poświadczenia dostępu do rejestru kontenerów platformy Azure

* Po zatwierdzeniu pliku przepływu pracy do twojego repo polecenie zostanie wyzwolone. 

Dane wyjściowe są podobne do następujących:

```console
[...]
Checking in file github/workflows/main.yml in the Github repository myid/acr-build-helloworld-node
Creating workflow...
GitHub Action Workflow has been created - https://github.com/myid/acr-build-helloworld-node/runs/515192398
GitHub workflow completed.
Workflow succeeded
Your app is deployed at:  http://acr-build-helloworld-node.eastus.azurecontainer.io:8080/
```

Aby wyświetlić stan przepływu pracy i wyniki każdego kroku w interfejsie użytkownika usługi GitHub, zobacz [Wyświetlanie historii uruchamiania przepływu pracy.](https://docs.github.com/en/actions/managing-workflow-runs/viewing-workflow-run-history)

### <a name="validate-workflow"></a>Weryfikowanie przepływu pracy

Przepływ pracy wdraża wystąpienie kontenera platformy Azure o nazwie podstawowej repozytorium GitHub, w tym przypadku *acr-build-helloworld-node.* Po pomyślnym zakończeniu przepływu pracy uzyskaj informacje o wystąpieniu kontenera o nazwie *acr-build-helloworld-node,* uruchamiając [polecenie az container show.][az-container-show] Zastąp nazwę grupy zasobów: 

```azurecli
az container show \
  --resource-group <resource-group-name> \
  --name acr-build-helloworld-node \
  --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" \
  --output table
```

Dane wyjściowe są podobne do następujących:

```console
FQDN                                                   ProvisioningState
---------------------------------                      -------------------
acr-build-helloworld-node.westus.azurecontainer.io     Succeeded
```

Po aprowizeniu wystąpienia przejdź do WQDN kontenera w przeglądarce, aby wyświetlić uruchamianą aplikację internetową.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Zatrzymaj wystąpienie kontenera przy użyciu polecenia [az container delete][az-container-delete]:

```azurecli
az container delete \
  --name <instance-name>
  --resource-group <resource-group-name>
```

Aby usunąć grupę zasobów i wszystkie jej zasoby, uruchom [polecenie az group delete:][az-group-delete]

```azurecli
az group delete \
  --name <resource-group-name>
```

## <a name="next-steps"></a>Następne kroki

Przejrzyj witryny [GitHub Marketplace,](https://github.com/marketplace?type=actions) aby uzyskać więcej akcji w celu zautomatyzowania przepływu pracy tworzenia aplikacji


<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->

[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-show]: /cli/azure/group#az_group_show
[az-group-delete]: /cli/azure/group#az_group_delete
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[az-container-create]: /cli/azure/container#az_container_create
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-container-show]: /cli/azure/container#az_container_show
[az-container-delete]: /cli/azure/container#az_container_delete
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-container-app-up]: /cli/azure/container/app#az_container_app_up
