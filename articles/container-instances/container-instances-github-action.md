---
title: Wdróż wystąpienie kontenera przez akcję GitHub
description: Skonfiguruj akcję GitHub, która automatyzuje kroki do kompilowania, wypychania i wdrażania obrazu kontenera do Azure Container Instances
ms.topic: article
ms.date: 08/20/2020
ms.custom: github-actions-azure, devx-track-azurecli
ms.openlocfilehash: 0068b9869e5ef7a3b6c0fd21c1b69cdae1d4ab4b
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96454820"
---
# <a name="configure-a-github-action-to-create-a-container-instance"></a>Konfigurowanie akcji usługi GitHub w celu utworzenia wystąpienia kontenera

[Akcje GitHub](https://help.github.com/actions/getting-started-with-github-actions/about-github-actions) to zestaw funkcji w usłudze GitHub umożliwiających automatyzację przepływów pracy tworzenia oprogramowania w tym samym miejscu, w którym można przechowywać kod i współpracować nad żądaniami ściągnięcia i problemami.

Aby zautomatyzować wdrażanie pojedynczego kontenera do Azure Container Instances, użyj akcji [Wdróż do Azure Container Instances](https://github.com/azure/aci-deploy) GitHub. Akcja pozwala ustawić właściwości dla wystąpienia kontenera podobnego do tych w polecenia [AZ Container Create][az-container-create] .

W tym artykule pokazano, jak skonfigurować przepływ pracy w repozytorium GitHub, które wykonuje następujące czynności:

* Tworzenie obrazu z pliku dockerfile
* Wypchnij obraz do usługi Azure Container Registry
* Wdrażanie obrazu kontenera w usłudze Azure Container instance

W tym artykule przedstawiono dwa sposoby konfigurowania przepływu pracy:

* [Konfigurowanie przepływu pracy](#configure-github-workflow) w usłudze GitHub — tworzenie przepływu pracy w repozytorium GitHub przy użyciu akcji wdróż do Azure Container Instances i innych akcji.  
* [Użyj rozszerzenia interfejsu wiersza](#use-deploy-to-azure-extension) polecenia — Użyj narzędzia `az container app up` z rozszerzenia [Wdróż do platformy Azure](https://github.com/Azure/deploy-to-azure-cli-extension) w interfejsie wiersza polecenia platformy Azure. To polecenie usprawnia tworzenie przepływu pracy i etapów wdrażania usługi GitHub.

> [!IMPORTANT]
> Akcja usługi GitHub dla Azure Container Instances jest obecnie dostępna w wersji zapoznawczej. Wersje zapoznawcze są udostępniane pod warunkiem udzielenia zgody na [dodatkowe warunki użytkowania][terms-of-use]. Niektóre cechy funkcji mogą ulec zmianie, zanim stanie się ona ogólnie dostępna.

## <a name="prerequisites"></a>Wymagania wstępne

* **Konto usługi GitHub** — Utwórz konto https://github.com , jeśli jeszcze go nie masz.
* **Interfejs wiersza polecenia platformy Azure** — możesz użyć Azure Cloud Shell lub lokalnej instalacji interfejsu wiersza polecenia platformy Azure, aby ukończyć kroki interfejsu wiersza polecenia platformy Azure. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].
* **Azure Container Registry** — jeśli go nie masz, Utwórz rejestr kontenerów platformy Azure w warstwie Podstawowa przy użyciu [interfejsu wiersza polecenia platformy Azure](../container-registry/container-registry-get-started-azure-cli.md), [Azure Portal](../container-registry/container-registry-get-started-portal.md)lub innych metod. Zanotuj grupę zasobów używaną w ramach wdrożenia, która jest używana na potrzeby przepływu pracy usługi GitHub.

## <a name="set-up-repo"></a>Konfigurowanie repozytorium

* Aby zapoznać się z przykładami w tym artykule, Użyj usługi GitHub, aby utworzyć rozwidlenie następującego repozytorium: https://github.com/Azure-Samples/acr-build-helloworld-node

  To repozytorium zawiera pliki źródłowe i pliku dockerfile, aby utworzyć obraz kontenera małej aplikacji sieci Web.

  ![Zrzut ekranu przedstawiający przycisk rozwidlenia (wyróżniony) w serwisie GitHub](../container-registry/media/container-registry-tutorial-quick-build/quick-build-01-fork.png)

* Upewnij się, że włączono akcje dla repozytorium. Przejdź do repozytorium z rozwidleniem i wybierz pozycję **Ustawienia**  >  **Akcje**. W obszarze **uprawnienia akcji** upewnij się, że wybrano **opcję Włącz akcje lokalne i inne osoby trzecie dla tego repozytorium** .

## <a name="configure-github-workflow"></a>Konfigurowanie przepływu pracy usługi GitHub

### <a name="create-service-principal-for-azure-authentication"></a>Tworzenie jednostki usługi na potrzeby uwierzytelniania na platformie Azure

W przepływie pracy usługi GitHub należy podać poświadczenia platformy Azure w celu uwierzytelnienia w interfejsie wiersza polecenia platformy Azure. Poniższy przykład tworzy jednostkę usługi z rolą współautor objętą zakresem grupy zasobów dla rejestru kontenerów.

Najpierw Pobierz identyfikator zasobu grupy zasobów. Zastąp nazwę grupy w następującym [AZ Group Show][az-group-show] polecenie:

```azurecli
$groupId=$(az group show \
  --name <resource-group-name> \
  --query id --output tsv)
```

Użyj [AZ AD Sp Create-for-RBAC][az-ad-sp-create-for-rbac] , aby utworzyć jednostkę usługi:

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

Zapisz dane wyjściowe JSON, ponieważ są używane w późniejszym kroku. Należy również pamiętać, że należy `clientId` zaktualizować jednostkę usługi w następnej sekcji.

### <a name="update-service-principal-for-registry-authentication"></a>Aktualizowanie jednostki usługi na potrzeby uwierzytelniania w rejestrze

Zaktualizuj poświadczenia jednostki usługi platformy Azure, aby umożliwić wypychanie i ściąganie danych do rejestru kontenerów. Ten krok umożliwia przepływowi pracy w usłudze GitHub użycie jednostki usługi do [uwierzytelniania w rejestrze kontenera](../container-registry/container-registry-auth-service-principal.md) oraz wypychanie i ściąganie obrazu platformy Docker. 

Pobierz identyfikator zasobu rejestru kontenerów. Zastąp nazwę rejestru w następującym [AZ ACR show][az-acr-show] Command:

```azurecli
registryId=$(az acr show \
  --name <registry-name> \
  --query id --output tsv)
```

Użyj polecenia [AZ role Assign Create][az-role-assignment-create] , aby przypisać rolę AcrPush, która umożliwia wypychanie i ściąganie dostępu do rejestru. Zastąp identyfikator klienta nazwy głównej usługi:

```azurecli
az role assignment create \
  --assignee <ClientId> \
  --scope $registryId \
  --role AcrPush
```

### <a name="save-credentials-to-github-repo"></a>Zapisz poświadczenia w repozytorium GitHub

1. W interfejsie użytkownika usługi GitHub przejdź do repozytorium z rozwidleniem i wybierz pozycję **Ustawienia** wpisy  >  **tajne**. 

1. Wybierz pozycję **Dodaj nowy wpis tajny** , aby dodać następujące wpisy tajne:

|Wpis tajny  |Wartość  |
|---------|---------|
|`AZURE_CREDENTIALS`     | Wszystkie dane wyjściowe JSON z kroku tworzenia jednostki usługi |
|`REGISTRY_LOGIN_SERVER`   | Nazwa serwera logowania w rejestrze (wszystkie małe litery). Przykład: *myregistry.azurecr.IO*        |
|`REGISTRY_USERNAME`     |  `clientId`Z danych wyjściowych JSON z tworzenia jednostki usługi       |
|`REGISTRY_PASSWORD`     |  `clientSecret`Z danych wyjściowych JSON z tworzenia jednostki usługi |
| `RESOURCE_GROUP` | Nazwa grupy zasobów użytej do określania zakresu jednostki usługi |

### <a name="create-workflow-file"></a>Utwórz plik przepływu pracy

1. W interfejsie użytkownika usługi GitHub wybierz pozycję **Akcje**  >  **Nowy przepływ pracy**.
1. Wybierz opcję **Skonfiguruj przepływ pracy samodzielnie**.
1. W obszarze **Edytuj nowy plik** wklej następującą zawartość YAML, aby zastąpić przykładowy kod. Zaakceptuj domyślną nazwę pliku `main.yml` lub podaj wybraną nazwę pliku.
1. Wybierz pozycję **Rozpocznij zatwierdzanie**, opcjonalnie Podaj krótkie i rozszerzone opisy zatwierdzenia, a następnie wybierz pozycję **Zatwierdź nowy plik**.

```yml
on: [push]
name: Linux_Container_Workflow

jobs:
    build-and-deploy:
        runs-on: ubuntu-latest
        steps:
        # checkout the repo
        - name: 'Checkout GitHub Action'
          uses: actions/checkout@master
          
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

### <a name="validate-workflow"></a>Sprawdź poprawność przepływu pracy

Gdy zatwierdzisz plik przepływu pracy, przepływ pracy zostanie wyzwolony. Aby przejrzeć postęp przepływu pracy, przejdź do **akcji**  >  **przepływy pracy**. 

![Wyświetl postęp przepływu pracy](./media/container-instances-github-action/github-action-progress.png)

Zobacz [Zarządzanie przebiegiem przepływu pracy](https://help.github.com/actions/configuring-and-managing-workflows/managing-a-workflow-run) , aby uzyskać informacje na temat wyświetlania stanu i wyników każdego kroku w przepływie pracy. Jeśli przepływ pracy nie zostanie ukończony, zobacz [Przeglądanie dzienników w celu diagnozowania błędów](https://docs.github.com/actions/configuring-and-managing-workflows/managing-a-workflow-run#viewing-logs-to-diagnose-failures).

Po pomyślnym ukończeniu przepływu pracy Pobierz informacje o wystąpieniu kontenera o nazwie *ACI-sampleapp* , uruchamiając polecenie [AZ Container show][az-container-show] . Zastąp nazwę grupy zasobów: 

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

Po aprowizacji wystąpienia przejdź do nazwy FQDN kontenera w przeglądarce, aby wyświetlić działającą aplikację sieci Web.

![Uruchamianie aplikacji sieci Web w przeglądarce](./media/container-instances-github-action/github-action-container.png)

## <a name="use-deploy-to-azure-extension"></a>Korzystanie z rozszerzenia Deploy to Azure

Alternatywnie można skonfigurować przepływ pracy za pomocą [rozszerzenia Wdróż do platformy Azure](https://github.com/Azure/deploy-to-azure-cli-extension) w interfejsie wiersza polecenia platformy Azure. `az container app up`Polecenie w rozszerzeniu pobiera parametry wejściowe od użytkownika w celu skonfigurowania przepływu pracy do wdrożenia w Azure Container Instances. 

Przepływ pracy utworzony za pomocą interfejsu wiersza polecenia platformy Azure jest podobny do przepływu pracy, który można [utworzyć ręcznie za pomocą usługi GitHub](#configure-github-workflow).

### <a name="additional-prerequisite"></a>Dodatkowe wymagania wstępne

Oprócz [wymagań wstępnych](#prerequisites) i [konfiguracji repozytorium](#set-up-repo) w tym scenariuszu należy zainstalować  **rozszerzenie Deploy to Azure** dla interfejsu wiersza polecenia platformy Azure.

Uruchom polecenie [AZ Extension Add][az-extension-add] , aby zainstalować rozszerzenie:

```azurecli
az extension add \
  --name deploy-to-azure
```

Aby uzyskać informacje dotyczące znajdowania i instalowania rozszerzeń oraz zarządzania nimi, zobacz [Korzystanie z rozszerzeń przy użyciu interfejsu wiersza polecenia platformy Azure](/cli/azure/azure-cli-extensions-overview).

### <a name="run-az-container-app-up"></a>Uruchom polecenie `az container app up`

Aby uruchomić polecenie [AZ Container App up][az-container-app-up] , podaj minimalną wartość:

* Nazwa rejestru kontenerów platformy Azure, na przykład, *Rejestr*
* Adres URL repozytorium GitHub, na przykład `https://github.com/<your-GitHub-Id>/acr-build-helloworld-node`

Przykładowe polecenie:

```azurecli
az container app up \
  --acr myregistry \
  --repository https://github.com/myID/acr-build-helloworld-node
```

### <a name="command-progress"></a>Postęp polecenia

* Po wyświetleniu monitu podaj swoje poświadczenia usługi GitHub lub podaj [osobisty token dostępu GitHub](https://help.github.com/github/authenticating-to-github/creating-a-personal-access-token-for-the-command-line) , który ma *repozytorium* i zakresy *użytkowników* do uwierzytelniania na koncie usługi GitHub. Jeśli podano poświadczenia usługi GitHub, polecenie tworzy dla Ciebie dyspozycji. Postępuj zgodnie z dodatkowymi monitami, aby skonfigurować przepływ pracy.

* Polecenie tworzy wpisy tajne repozytorium dla przepływu pracy:

  * Poświadczenia nazwy głównej usługi dla interfejsu wiersza polecenia platformy Azure
  * Poświadczenia dostępu do usługi Azure Container Registry

* Gdy polecenie zatwierdzi plik przepływu pracy w repozytorium, przepływ pracy zostanie wyzwolony. 

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

Aby wyświetlić stan przepływu pracy i wyniki każdego kroku w interfejsie użytkownika usługi GitHub, zobacz [Zarządzanie przebiegiem przepływu pracy](https://help.github.com/actions/configuring-and-managing-workflows/managing-a-workflow-run).

### <a name="validate-workflow"></a>Sprawdź poprawność przepływu pracy

Przepływ pracy wdraża wystąpienie kontenera platformy Azure z podstawową nazwą repozytorium GitHub, w tym przypadku *ACR-Build-HelloWorld-Node*. Po pomyślnym ukończeniu przepływu pracy Pobierz informacje o wystąpieniu kontenera o nazwie *ACR-Build-HelloWorld-Node* , uruchamiając polecenie [AZ Container show][az-container-show] . Zastąp nazwę grupy zasobów: 

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

Po aprowizacji wystąpienia przejdź do nazwy FQDN kontenera w przeglądarce, aby wyświetlić działającą aplikację sieci Web.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Zatrzymaj wystąpienie kontenera przy użyciu polecenia [az container delete][az-container-delete]:

```azurecli
az container delete \
  --name <instance-name>
  --resource-group <resource-group-name>
```

Aby usunąć grupę zasobów i wszystkie znajdujące się w niej zasoby, uruchom polecenie [AZ Group Delete][az-group-delete] :

```azurecli
az group delete \
  --name <resource-group-name>
```

## <a name="next-steps"></a>Następne kroki

Przejrzyj [witrynę GitHub Marketplace](https://github.com/marketplace?type=actions) , aby uzyskać więcej działań w celu zautomatyzowania przepływu pracy programistycznej


<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->

[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-show]: /cli/azure/group#az-group-show
[az-group-delete]: /cli/azure/group#az-group-delete
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-container-create]: /cli/azure/container#az-container-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-container-show]: /cli/azure/container#az-container-show
[az-container-delete]: /cli/azure/container#az-container-delete
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-container-app-up]: /cli/azure/ext/deploy-to-azure/container/app#ext-deploy-to-azure-az-container-app-up
