---
title: Zainstaluj wolumin tajny do grupy kontenerów
description: Dowiedz się, jak zainstalować wolumin tajny, aby przechowywać poufne informacje na potrzeby dostępu do wystąpień kontenerów
ms.topic: article
ms.date: 07/02/2020
ms.openlocfilehash: ea82ba5994feaf102d4622eada284df431e004d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86169565"
---
# <a name="mount-a-secret-volume-in-azure-container-instances"></a>Zainstaluj wolumin tajny w Azure Container Instances

Użyj woluminu *tajnego* , aby dostarczyć poufne informacje do kontenerów w grupie kontenerów. Wolumin *tajny* przechowuje wpisy tajne w plikach w woluminie, które są dostępne dla kontenerów w grupie kontenerów. Przechowywanie wpisów tajnych w woluminie *tajnym* pozwala uniknąć dodawania poufnych danych, takich jak klucze SSH lub poświadczenia bazy danych do kodu aplikacji.

* Po wdrożeniu za pomocą wpisów tajnych w grupie kontenerów wolumin tajny jest *tylko do odczytu*.
* Wszystkie woluminy tajne są obsługiwane przez [tmpfs][tmpfs], system plików z pamięcią RAM; ich zawartość nigdy nie jest zapisywana w magazynie nietrwałym.

> [!NOTE]
> Woluminy *tajne* są obecnie ograniczone do kontenerów systemu Linux. Dowiedz się, jak przekazywać bezpieczne zmienne środowiskowe dla kontenerów systemów Windows i Linux w [ustawieniu zmiennych środowiskowych](container-instances-environment-variables.md). Gdy pracujemy nad przełączeniem wszystkich funkcji do kontenerów systemu Windows, w [przeglądzie](container-instances-overview.md#linux-and-windows-containers)można znaleźć bieżące różnice między platformami.

## <a name="mount-secret-volume---azure-cli"></a>Zainstaluj wolumin tajny — interfejs wiersza polecenia platformy Azure

Aby wdrożyć kontener z co najmniej jednym wpisem tajnym przy użyciu interfejsu wiersza polecenia platformy Azure, Dołącz `--secrets` `--secrets-mount-path` Parametry i w poleceniu [AZ Container Create][az-container-create] . Ten przykład instaluje wolumin *tajny* składający się z dwóch plików zawierających klucze tajne "mysecret1" i "mysecret2" w `/mnt/secrets` :

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name secret-volume-demo \
    --image mcr.microsoft.com/azuredocs/aci-helloworld \
    --secrets mysecret1="My first secret FOO" mysecret2="My second secret BAR" \
    --secrets-mount-path /mnt/secrets
```

Następujące polecenie [AZ Container exec][az-container-exec] Output pokazuje otwieranie powłoki w działającym kontenerze, wyświetlanie listy plików w woluminie tajnym, a następnie wyświetlanie ich zawartości:

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

## <a name="mount-secret-volume---yaml"></a>Zainstaluj wolumin tajny YAML

Możesz również wdrożyć grupy kontenerów za pomocą interfejsu wiersza polecenia platformy Azure i [szablonu YAML](container-instances-multi-container-yaml.md). Wdrażanie przy użyciu szablonu YAML jest preferowaną metodą wdrażania grup kontenerów składających się z wielu kontenerów.

W przypadku wdrażania przy użyciu szablonu YAML wartości klucza tajnego muszą być **zakodowane w formacie base64** w szablonie. Jednak wartości klucza tajnego są wyświetlane w postaci zwykłego tekstu w plikach w kontenerze.

Poniższy szablon YAML definiuje grupę kontenerów z jednym kontenerem instalującym wolumin *tajny* w systemie `/mnt/secrets` . Wolumin tajny ma dwa pliki zawierające wpisy tajne, "mysecret1" i "mysecret2".

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

Aby wdrożyć z szablonem YAML, Zapisz poprzedni YAML do pliku o nazwie `deploy-aci.yaml` , a następnie wykonaj polecenie [AZ Container Create][az-container-create] z `--file` parametrem:

```azurecli-interactive
# Deploy with YAML template
az container create \
  --resource-group myResourceGroup \
  --file deploy-aci.yaml
```

## <a name="mount-secret-volume---resource-manager"></a>Zainstaluj wolumin tajny Menedżer zasobów

Oprócz wdrożenia interfejsu wiersza polecenia i YAML można wdrożyć grupę kontenerów przy użyciu [szablonu Menedżer zasobów](/azure/templates/microsoft.containerinstance/containergroups)platformy Azure.

Najpierw Wypełnij `volumes` tablicę w sekcji Grupa kontenerów `properties` szablonu. W przypadku wdrażania przy użyciu szablonu Menedżer zasobów wartości tajne muszą być **zakodowane w formacie base64** w szablonie. Jednak wartości klucza tajnego są wyświetlane w postaci zwykłego tekstu w plikach w kontenerze.

Następnie dla każdego kontenera w grupie kontenerów, w której chcesz zainstalować wolumin *tajny* , Wypełnij `volumeMounts` tablicę w `properties` sekcji definicji kontenera.

Poniższy szablon Menedżer zasobów definiuje grupę kontenerów z jednym kontenerem instalującym wolumin *tajny* w systemie `/mnt/secrets` . Wolumin tajny ma dwa wpisy tajne, "mysecret1" i "mysecret2".

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-secret.json -->
[!code-json[volume-secret](~/azure-docs-json-samples/container-instances/aci-deploy-volume-secret.json)]

Aby wdrożyć z szablonem Menedżer zasobów, Zapisz poprzedni kod JSON w pliku o nazwie `deploy-aci.json` , a następnie uruchom polecenie [AZ Deployment Group Create][az-deployment-group-create] z `--template-file` parametrem:

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
* [Zainstaluj wolumin gitRepo w Azure Container Instances](container-instances-volume-gitrepo.md)

### <a name="secure-environment-variables"></a>Bezpieczne zmienne środowiskowe

Inną metodą podawania poufnych informacji do kontenerów (w tym kontenerów systemu Windows) jest użycie [bezpiecznych zmiennych środowiskowych](container-instances-environment-variables.md#secure-values).

<!-- LINKS - External -->
[tmpfs]: https://wikipedia.org/wiki/Tmpfs

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create
