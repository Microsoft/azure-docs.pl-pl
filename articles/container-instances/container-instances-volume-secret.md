---
title: Zainstaluj wolumin tajny w grupie kontenerów
description: Dowiedz się, jak zainstalować wolumin tajny do przechowywania poufnych informacji w celu uzyskania dostępu przez wystąpienia kontenera
ms.topic: article
ms.date: 07/02/2020
ms.openlocfilehash: cd8bd4d59b5e53a0db2455bdfbaf56c05c93d65f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771003"
---
# <a name="mount-a-secret-volume-in-azure-container-instances"></a>Zainstaluj wolumin tajny w Azure Container Instances

Użyj *woluminu tajnego,* aby dostarczyć informacje poufne do kontenerów w grupie kontenerów. Wolumin *wpisów* tajnych przechowuje wpisy tajne w plikach w woluminie, dostępnych dla kontenerów w grupie kontenerów. Dzięki przechowywaniu  wpisów tajnych w woluminie wpisów tajnych można uniknąć dodawania poufnych danych, takich jak klucze SSH lub poświadczenia bazy danych, do kodu aplikacji.

* Po wdrożeniu z wpisami tajnymi w grupie kontenerów wolumin wpisów tajnych jest *tylko do odczytu.*
* Wszystkie woluminy tajne są oparte na [tmpfs][tmpfs], system plików z kopią RAM; Ich zawartość nigdy nie jest zapisywana w magazynie nietrwałym.

> [!NOTE]
> *Woluminy* tajne są obecnie ograniczone do kontenerów systemu Linux. Aby dowiedzieć się, jak przekazywać bezpieczne zmienne środowiskowe dla kontenerów systemów Windows i Linux, [zobacz Ustawianie zmiennych środowiskowych.](container-instances-environment-variables.md) Podczas gdy pracujemy nad dodaniem wszystkich funkcji do kontenerów systemu Windows, bieżące różnice między platformami można znaleźć w [przeglądzie](container-instances-overview.md#linux-and-windows-containers).

## <a name="mount-secret-volume---azure-cli"></a>Zainstaluj wolumin tajny — interfejs wiersza polecenia platformy Azure

Aby wdrożyć kontener z co najmniej jednym wpisem tajnym przy użyciu interfejsu wiersza polecenia platformy Azure, uwzględnij parametry `--secrets` i w poleceniu az container `--secrets-mount-path` [create.][az-container-create] W tym przykładzie wolumin *wpisów tajnych* składający się z dwóch plików zawierających wpisy tajne "mysecret1" i "mysecret2", w `/mnt/secrets` :

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name secret-volume-demo \
    --image mcr.microsoft.com/azuredocs/aci-helloworld \
    --secrets mysecret1="My first secret FOO" mysecret2="My second secret BAR" \
    --secrets-mount-path /mnt/secrets
```

Następujące dane [wyjściowe az container exec][az-container-exec] pokazują otwieranie powłoki w uruchomionym kontenerze, wyświetlanie listy plików w woluminie wpisów tajnych, a następnie wyświetlanie ich zawartości:

```azurecli
az container exec \
  --resource-group myResourceGroup \
  --name secret-volume-demo --exec-command "/bin/sh"
```

```output
/usr/src/app # ls /mnt/secrets
mysecret1
mysecret2
/usr/src/app # cat /mnt/secrets/mysecret1
My first secret FOO
/usr/src/app # cat /mnt/secrets/mysecret2
My second secret BAR
/usr/src/app # exit
Bye.
```

## <a name="mount-secret-volume---yaml"></a>Zainstaluj wolumin tajny — YAML

Grupy kontenerów można również wdrożyć przy użyciu interfejsu wiersza polecenia platformy Azure i [szablonu YAML.](container-instances-multi-container-yaml.md) Wdrażanie za pomocą szablonu YAML jest preferowaną metodą podczas wdrażania grup kontenerów składających się z wielu kontenerów.

W przypadku wdrażania przy użyciu szablonu YAML wartości tajnych muszą być zakodowane w szablonie w formacie **Base64.** Jednak wartości kluczy tajnych są wyświetlane w postaci zwykłego tekstu w plikach w kontenerze.

Poniższy szablon YAML definiuje grupę kontenerów z jednym kontenerem, który zainstaluje wolumin *tajny* w pliku `/mnt/secrets` . Wolumin wpisów tajnych zawiera dwa pliki zawierające wpisy tajne: "mysecret1" i "mysecret2".

```yaml
apiVersion: '2019-12-01'
location: eastus
name: secret-volume-demo
properties:
  containers:
  - name: aci-tutorial-app
    properties:
      environmentVariables: []
      image: mcr.microsoft.com/azuredocs/aci-helloworld:latest
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      volumeMounts:
      - mountPath: /mnt/secrets
        name: secretvolume1
  osType: Linux
  restartPolicy: Always
  volumes:
  - name: secretvolume1
    secret:
      mysecret1: TXkgZmlyc3Qgc2VjcmV0IEZPTwo=
      mysecret2: TXkgc2Vjb25kIHNlY3JldCBCQVIK
tags: {}
type: Microsoft.ContainerInstance/containerGroups
```

Aby wdrożyć za pomocą szablonu YAML, zapisz powyższy plik YAML w pliku o nazwie , a następnie wykonaj polecenie `deploy-aci.yaml` [az container create][az-container-create] z `--file` parametrem :

```azurecli-interactive
# Deploy with YAML template
az container create \
  --resource-group myResourceGroup \
  --file deploy-aci.yaml
```

## <a name="mount-secret-volume---resource-manager"></a>Zainstaluj wolumin tajny — Resource Manager

Oprócz wdrożenia interfejsu wiersza polecenia i yaml można wdrożyć grupę kontenerów przy użyciu szablonu usługi Azure [Resource Manager .](/azure/templates/microsoft.containerinstance/containergroups)

Najpierw wypełnij `volumes` tablicę w sekcji grupy `properties` kontenerów szablonu. W przypadku wdrażania przy Resource Manager szablonu wartości tajnych muszą być zakodowane w szablonie w formacie **Base64.** Jednak wartości kluczy tajnych są wyświetlane w postaci zwykłego tekstu w plikach w kontenerze.

Następnie dla każdego kontenera w grupie kontenerów, w  której chcesz zainstalować wolumin tajny, wypełnij tablicę w sekcji `volumeMounts` definicji `properties` kontenera.

Poniższy szablon Resource Manager definiuje grupę kontenerów z jednym kontenerem, który zainstaluje wolumin *tajny* w pliku `/mnt/secrets` . Wolumin wpisów tajnych ma dwa wpisy tajne: "mysecret1" i "mysecret2".

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-secret.json -->
[!code-json[volume-secret](~/azure-docs-json-samples/container-instances/aci-deploy-volume-secret.json)]

Aby wdrożyć za pomocą Resource Manager szablonu, zapisz powyższy kod JSON w pliku o nazwie , a następnie wykonaj polecenie `deploy-aci.json` az deployment group [create][az-deployment-group-create] z `--template-file` parametrem :

```azurecli-interactive
# Deploy with Resource Manager template
az deployment group create \
  --resource-group myResourceGroup \
  --template-file deploy-aci.json
```

## <a name="next-steps"></a>Następne kroki

### <a name="volumes"></a>Woluminy

Dowiedz się, jak zainstalować inne typy woluminów w Azure Container Instances:

* [Instalowanie udziału plików platformy Azure w usłudze Azure Container Instances](container-instances-volume-azure-files.md)
* [Zainstaluj wolumin emptyDir w Azure Container Instances](container-instances-volume-emptydir.md)
* [Zainstaluj wolumin gitRepo w usłudze Azure Container Instances](container-instances-volume-gitrepo.md)

### <a name="secure-environment-variables"></a>Zabezpieczanie zmiennych środowiskowych

Inną metodą dostarczania poufnych informacji do kontenerów (w tym kontenerów systemu Windows) jest użycie [bezpiecznych zmiennych środowiskowych](container-instances-environment-variables.md#secure-values).

<!-- LINKS - External -->
[tmpfs]: https://wikipedia.org/wiki/Tmpfs

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az_container_create
[az-container-exec]: /cli/azure/container#az_container_exec
[az-deployment-group-create]: /cli/azure/deployment/group#az_deployment_group_create
