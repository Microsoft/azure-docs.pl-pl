---
title: PRZESTARZAŁE Szybki Start — klaster Azure Docker CE dla systemu Linux
description: Szybka nauka tworzenia klastra Docker CE dla kontenerów systemu Linux w usłudze Azure Container Service za pomocą interfejsu wiersza polecenia platformy Azure.
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: iainfou
ms.custom: ''
ms.openlocfilehash: d4bbd5560681aa73709019e87c6c22470a64ad78
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79481742"
---
# <a name="deprecated-deploy-docker-ce-cluster"></a>PRZESTARZAŁE Wdróż klaster platformy Docker CE

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

W tym przewodniku szybki start klaster Docker CE jest wdrażany przy użyciu interfejsu wiersza polecenia platformy Azure. Następnie w klastrze jest wdrażana i uruchamiana aplikacja obsługująca wiele kontenerów, która składa się z frontonu internetowego i wystąpienia pamięci podręcznej Redis. Po ukończeniu aplikacja będzie dostępna w Internecie.

Tryb Docker CE w usłudze Azure Container Service jest w wersji zapoznawczej i **nie powinien być używany w przypadku obciążeń produkcyjnych**.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten przewodnik szybkiego startu będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az-group-create). Grupa zasobów platformy Azure to logiczna grupa przeznaczona do wdrażania zasobów platformy Azure i zarządzania nimi.

Poniższy przykład tworzy grupę zasobów o nazwie Moja *zasobów* w lokalizacji *westus2* .

```azurecli-interactive
az group create --name myResourceGroup --location westus2
```

Dane wyjściowe:

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup",
  "location": "westus2",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-docker-swarm-cluster"></a>Tworzenie klastra Docker Swarm

Utwórz klaster Docker CE w usłudze Azure Container Service za pomocą polecenia [az acs create](/cli/azure/acs#az-acs-create). Aby uzyskać informacje na temat dostępności regionu platformy Docker CE, zobacz [regiony usług ACS dla platformy Docker ce](https://github.com/Azure/ACS/blob/master/announcements/2017-08-04_additional_regions.md) .

W poniższym przykładzie tworzony jest klaster o nazwie *mySwarmCluster* z jednym węzłem głównym systemu Linux i trzema węzłami agenta systemu Linux.

```azurecli-interactive
az acs create --name mySwarmCluster --orchestrator-type dockerce --resource-group myResourceGroup --generate-ssh-keys
```

W niektórych przypadkach, np. ograniczonej wersji próbnej, subskrypcja platformy Azure ma ograniczony dostęp do zasobów platformy Azure. Jeśli wdrożenie nie powiedzie się z powodu ograniczonej liczby dostępnych rdzeni, zmniejsz domyślną liczbę agentów, dodając `--agent-count 1` do polecenia [az acs create](/cli/azure/acs#az-acs-create). 

Po kilku minutach polecenie zostanie zakończone i zwróci informacje o klastrze sformatowanym przy użyciu formatu JSON.

## <a name="connect-to-the-cluster"></a>Łączenie z klastrem

W tym przewodniku szybki start potrzebna jest nazwa FQDN zarówno wzorca Docker Swarm, jak i puli agentów platformy Docker. Uruchom następujące polecenie, aby zwrócić nazwy FQDN węzła głównego i agenta.

```azurecli
az acs list --resource-group myResourceGroup --query '[*].{Master:masterProfile.fqdn,Agent:agentPoolProfiles[0].fqdn}' -o table
```

Dane wyjściowe:

```output
Master                                                               Agent
-------------------------------------------------------------------  --------------------------------------------------------------------
myswarmcluster-myresourcegroup-d5b9d4mgmt.ukwest.cloudapp.azure.com  myswarmcluster-myresourcegroup-d5b9d4agent.ukwest.cloudapp.azure.com
```

Utwórz tunel SSH do węzła głównego Swarm. Zamień `MasterFQDN` na adres FQDN węzła głównego Swarm.

```bash
ssh -p 2200 -fNL localhost:2374:/var/run/docker.sock azureuser@MasterFQDN
```

Ustaw zmienną środowiskową `DOCKER_HOST`. Umożliwia to uruchamianie poleceń Docker względem klastra Docker Swarm bez konieczności określania nazwy hosta.

```bash
export DOCKER_HOST=localhost:2374
```

Teraz można przystąpić do uruchamiania usług Docker w klastrze Docker Swarm.


## <a name="run-the-application"></a>Uruchamianie aplikacji

Utwórz plik o nazwie `azure-vote.yaml` i skopiuj do niego poniższą zawartość.


```yaml
version: '3'
  azure-vote-back:
    image: redis
    ports:
        - "6379:6379"

  azure-vote-front:
    image: microsoft/azure-vote-front:v1
    environment:
      REDIS: azure-vote-back
    ports:
        - "80:80"
```

Uruchom polecenie [docker stack deploy](https://docs.docker.com/engine/reference/commandline/stack_deploy/), aby utworzyć usługę Azure Vote.

```bash
docker stack deploy azure-vote --compose-file azure-vote.yaml
```

Dane wyjściowe:

```output
Creating network azure-vote_default
Creating service azure-vote_azure-vote-back
Creating service azure-vote_azure-vote-front
```

Za pomocą polecenia [docker stack ps](https://docs.docker.com/engine/reference/commandline/stack_ps/) zwróć stan wdrożenia aplikacji.

```bash
docker stack ps azure-vote
```

Gdy pozycje `CURRENT STATE` poszczególnych usług będą miały wartość `Running`, aplikacja będzie gotowa.

```output
ID                  NAME                            IMAGE                                 NODE                               DESIRED STATE       CURRENT STATE                ERROR               PORTS
tnklkv3ogu3i        azure-vote_azure-vote-front.1   microsoft/azure-vote-front:v1   swarmm-agentpool0-66066781000004   Running             Running 5 seconds ago                            
lg99i4hy68r9        azure-vote_azure-vote-back.1    redis:latest                          swarmm-agentpool0-66066781000002   Running             Running about a minute ago
```

## <a name="test-the-application"></a>Testowanie aplikacji

Przejdź do nazwy FQDN puli agenta Swarm w celu przetestowania aplikacji Azure Vote.

![Obraz przedstawiający przechodzenie do aplikacji Azure Vote](media/container-service-docker-swarm-mode-walkthrough/azure-vote.png)

## <a name="delete-cluster"></a>Usuwanie klastra
Gdy klaster nie będzie już potrzebny, możesz usunąć grupę zasobów, usługę kontenera i wszystkie pokrewne zasoby za pomocą polecenia [az group delete](/cli/azure/group#az-group-delete).

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="get-the-code"></a>Uzyskiwanie kodu

W tym przewodniku szybki start utworzono wstępnie utworzone obrazy kontenerów w celu utworzenia usługi platformy Docker. Powiązany kod aplikacji, plik Dockerfile i plik Compose są dostępne w serwisie GitHub.

[https://github.com/Azure-Samples/azure-voting-app-redis](https://github.com/Azure-Samples/azure-voting-app-redis.git)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start wdrożono klaster Docker Swarm i wdrożono w nim aplikację obsługującą wiele kontenerów.

Aby dowiedzieć się więcej na temat integrowania narzędzia Docker Swarm z usługą Azure DevOps, przejdź do obszaru ciągłej integracji/ciągłego wdrażania za pomocą platformy Docker Swarm i Azure DevOps

> [!div class="nextstepaction"]
> [Ciągła integracja/ciągłe wdrażanie za pomocą struktury Docker Swarm i usługi Azure DevOps](./container-service-docker-swarm-setup-ci-cd.md)
