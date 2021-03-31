---
title: Zainstaluj wolumin gitRepo do grupy kontenerów
description: Dowiedz się, jak zainstalować wolumin gitRepo, aby sklonować repozytorium git do wystąpień kontenera
ms.topic: article
ms.date: 06/15/2018
ms.openlocfilehash: 5f361b0bbd4a02f754e47c8321a664af05036601
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86260718"
---
# <a name="mount-a-gitrepo-volume-in-azure-container-instances"></a>Zainstaluj wolumin gitRepo w Azure Container Instances

Dowiedz się, jak zainstalować wolumin *gitRepo* , aby sklonować repozytorium git do wystąpień kontenerów.

> [!NOTE]
> Instalowanie woluminu *gitRepo* jest obecnie ograniczone do kontenerów systemu Linux. Gdy pracujemy nad przełączeniem wszystkich funkcji do kontenerów systemu Windows, w [przeglądzie](container-instances-overview.md#linux-and-windows-containers)można znaleźć bieżące różnice między platformami.

## <a name="gitrepo-volume"></a>wolumin gitRepo

Wolumin *gitRepo* instaluje katalog i klonuje określone repozytorium git przy uruchamianiu kontenera. Przy użyciu woluminu *gitRepo* w wystąpieniach kontenera można uniknąć dodawania kodu do wykonania w aplikacjach.

Podczas instalowania woluminu *gitRepo* można ustawić trzy właściwości, aby skonfigurować wolumin:

| Właściwość | Wymagane | Opis |
| -------- | -------- | ----------- |
| `repository` | Tak | Pełny adres URL, w tym `http://` lub `https://` , repozytorium git do sklonowania.|
| `directory` | Nie | Katalog, do którego ma zostać Sklonowane repozytorium. Ścieżka nie może zawierać znaków "" ani zaczynać się od znaku " `..` ".  Jeśli określisz " `.` ", repozytorium zostanie sklonowane do katalogu woluminu. W przeciwnym razie repozytorium git zostanie sklonowane do podkatalogu o podaną nazwę w katalogu woluminów. |
| `revision` | Nie | Skrót zatwierdzenia poprawki do klonowania. Jeśli nie zostanie określony, `HEAD` poprawka zostanie sklonowana. |

## <a name="mount-gitrepo-volume-azure-cli"></a>Zainstaluj wolumin gitRepo: interfejs wiersza polecenia platformy Azure

Aby zainstalować wolumin gitRepo podczas wdrażania wystąpień kontenerów za pomocą [interfejsu wiersza polecenia platformy Azure](/cli/azure), podaj `--gitrepo-url` Parametry i w `--gitrepo-mount-path` polu [AZ Container Create][az-container-create] . Opcjonalnie możesz określić katalog w woluminie, który ma zostać sklonowany do ( `--gitrepo-dir` ) i skrót zatwierdzenia poprawki do sklonowania ( `--gitrepo-revision` ).

To przykładowe polecenie klonuje przykładową aplikację Microsoft [ACI-HelloWorld][aci-helloworld] do `/mnt/aci-helloworld` programu w ramach wystąpienia kontenera:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name hellogitrepo \
    --image mcr.microsoft.com/azuredocs/aci-helloworld \
    --dns-name-label aci-demo \
    --ports 80 \
    --gitrepo-url https://github.com/Azure-Samples/aci-helloworld \
    --gitrepo-mount-path /mnt/aci-helloworld
```

Aby sprawdzić, czy wolumin gitRepo został zainstalowany, Uruchom powłokę w kontenerze za pomocą [AZ Container exec][az-container-exec] i wystaw katalog:

```azurecli
az container exec --resource-group myResourceGroup --name hellogitrepo --exec-command /bin/sh
```

```output
/usr/src/app # ls -l /mnt/aci-helloworld/
total 16
-rw-r--r--    1 root     root           144 Apr 16 16:35 Dockerfile
-rw-r--r--    1 root     root          1162 Apr 16 16:35 LICENSE
-rw-r--r--    1 root     root          1237 Apr 16 16:35 README.md
drwxr-xr-x    2 root     root          4096 Apr 16 16:35 app
```

## <a name="mount-gitrepo-volume-resource-manager"></a>Zainstaluj wolumin gitRepo: Menedżer zasobów

Aby zainstalować wolumin gitRepo podczas wdrażania wystąpień kontenerów z [szablonem Azure Resource Manager](/azure/templates/microsoft.containerinstance/containergroups), należy najpierw wypełnić `volumes` tablicę w sekcji Grupa kontenerów `properties` szablonu. Następnie dla każdego kontenera w grupie kontenerów, w której chcesz zainstalować wolumin *gitRepo* , Wypełnij `volumeMounts` tablicę w `properties` sekcji definicji kontenera.

Na przykład poniższy szablon Menedżer zasobów tworzy grupę kontenerów składającą się z jednego kontenera. Kontener klonuje dwa repozytoria GitHub określone przez bloki woluminów *gitRepo* . Drugi wolumin zawiera dodatkowe właściwości określające katalog do klonowania oraz skrót zatwierdzania określonej poprawki do klonowania.

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-gitrepo.json -->
[!code-json[volume-gitrepo](~/azure-docs-json-samples/container-instances/aci-deploy-volume-gitrepo.json)]

Powstająca struktura katalogów dwóch sklonowanych repozytoriów zdefiniowanych w poprzednim szablonie to:

```
/mnt/repo1/aci-helloworld
/mnt/repo2/my-custom-clone-directory
```

Aby zobaczyć przykład wdrożenia wystąpienia kontenera z szablonem Azure Resource Manager, zobacz [wdrażanie grup wielokontenerowych w Azure Container Instances](container-instances-multi-container-group.md).

## <a name="private-git-repo-authentication"></a>Uwierzytelnianie prywatnego repozytorium git

Aby zainstalować wolumin gitRepo dla prywatnego repozytorium git, określ poświadczenia w adresie URL repozytorium. Zazwyczaj poświadczenia są w postaci nazwy użytkownika i osobistego tokenu dostępu, który przyznaje dostęp do zakresu repozytorium.

Na przykład parametr interfejsu wiersza polecenia platformy Azure `--gitrepo-url` dla prywatnego repozytorium GitHub będzie wyglądać podobnie do poniższego (gdzie "gituser" jest nazwą użytkownika serwisu GitHub, a "abcdef1234fdsa4321abcdef" jest osobistym tokenem dostępu użytkownika):

```console
--gitrepo-url https://gituser:abcdef1234fdsa4321abcdef@github.com/GitUser/some-private-repository
```

W przypadku repozytorium Azure Repos git określ dowolną nazwę użytkownika (można użyć "azurereposuser", jak w poniższym przykładzie) w połączeniu z prawidłowym identyfikatorem:

```console
--gitrepo-url https://azurereposuser:abcdef1234fdsa4321abcdef@dev.azure.com/your-org/_git/some-private-repository
```

Aby uzyskać więcej informacji na temat osobistych tokenów dostępu dla usługi GitHub i Azure Repos, zobacz następujące tematy:

GitHub: [Tworzenie osobistego tokenu dostępu dla wiersza polecenia][pat-github]

Azure Repos: [Tworzenie osobistych tokenów dostępu w celu uwierzytelniania dostępu][pat-repos]

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak zainstalować inne typy woluminów w Azure Container Instances:

* [Instalowanie udziału plików platformy Azure w usłudze Azure Container Instances](container-instances-volume-azure-files.md)
* [Zainstaluj wolumin emptyDir w Azure Container Instances](container-instances-volume-emptydir.md)
* [Zainstaluj wolumin tajny w Azure Container Instances](container-instances-volume-secret.md)

<!-- LINKS - External -->
[aci-helloworld]: https://github.com/Azure-Samples/aci-helloworld
[pat-github]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
[pat-repos]: /azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
