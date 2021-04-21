---
title: Zainstaluj wolumin gitRepo w grupie kontenerów
description: Dowiedz się, jak zainstalować wolumin gitRepo w celu sklonowania repozytorium Git do wystąpień kontenera
ms.topic: article
ms.date: 06/15/2018
ms.openlocfilehash: 7c1249e3120dd680c52bf74fb045bedf5202b9f2
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763725"
---
# <a name="mount-a-gitrepo-volume-in-azure-container-instances"></a>Zainstaluj wolumin gitRepo w programie Azure Container Instances

Dowiedz się, jak zainstalować *wolumin gitRepo* w celu sklonowania repozytorium Git do wystąpień kontenera.

> [!NOTE]
> Instalowanie *woluminu gitRepo* jest obecnie ograniczone do kontenerów systemu Linux. Podczas gdy pracujemy nad dodaniem wszystkich funkcji do kontenerów systemu Windows, bieżące różnice między platformami można znaleźć w [przeglądzie](container-instances-overview.md#linux-and-windows-containers).

## <a name="gitrepo-volume"></a>wolumin gitRepo

Wolumin *gitRepo* zainstaluje katalog i sklonuje do niego określone repozytorium Git podczas uruchamiania kontenera. Korzystając z *woluminu gitRepo* w wystąpieniach kontenerów, można uniknąć dodawania kodu w tym celu w aplikacjach.

Podczas instalacji *woluminu gitRepo* można ustawić trzy właściwości w celu skonfigurowania woluminu:

| Właściwość | Wymagane | Opis |
| -------- | -------- | ----------- |
| `repository` | Tak | Pełny adres URL, w tym lub , repozytorium `http://` `https://` Git do sklonowania.|
| `directory` | Nie | Katalog, do którego należy sklonować repozytorium. Ścieżka nie może zawierać ani zaczynać się od `..` "".  W przypadku określenia `.` "", repozytorium zostanie sklonowane do katalogu woluminu. W przeciwnym razie repozytorium Git zostanie sklonowane do podkatalogu o podanej nazwie w katalogu woluminów. |
| `revision` | Nie | Skrót zatwierdzenia poprawki do sklonowania. Jeśli nie zostanie on nieokreślony, `HEAD` poprawka zostanie sklonowana. |

## <a name="mount-gitrepo-volume-azure-cli"></a>Zainstaluj wolumin gitRepo: interfejs wiersza polecenia platformy Azure

Aby zainstalować wolumin gitRepo podczas wdrażania wystąpień kontenera za pomocą interfejsu wiersza polecenia platformy [Azure,](/cli/azure)należy podać parametry i w poleceniu `--gitrepo-url` az container `--gitrepo-mount-path` [create.][az-container-create] Opcjonalnie możesz określić katalog w woluminie, do który ma zostać sklonowany program ( ) oraz skrót zatwierdzenia poprawki do `--gitrepo-dir` sklonowania ( `--gitrepo-revision` ).

To przykładowe polecenie klonuje przykładową aplikację Microsoft [aci-helloworld][aci-helloworld] w `/mnt/aci-helloworld` wystąpieniu kontenera:

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

Aby sprawdzić, czy wolumin gitRepo został zainstalowany, uruchom powłokę w kontenerze za pomocą [polecenia az container exec][az-container-exec] i wyślij katalog:

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

## <a name="mount-gitrepo-volume-resource-manager"></a>Zainstaluj wolumin gitRepo: Resource Manager

Aby zainstalować wolumin gitRepo podczas wdrażania wystąpień kontenera przy użyciu szablonu [Azure Resource Manager](/azure/templates/microsoft.containerinstance/containergroups), najpierw wypełnij tablicę w sekcji grupy kontenerów `volumes` `properties` szablonu. Następnie dla każdego kontenera w grupie kontenerów, w której chcesz zainstalować wolumin *gitRepo,* wypełnij tablicę w sekcji `volumeMounts` `properties` definicji kontenera.

Na przykład poniższy szablon Resource Manager tworzy grupę kontenerów składającą się z jednego kontenera. Kontener klonuje dwa repozytoria GitHub określone przez *bloki woluminów gitRepo.* Drugi wolumin zawiera dodatkowe właściwości określające katalog do sklonowania oraz skrót zatwierdzenia określonej poprawki do sklonowania.

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-gitrepo.json -->
[!code-json[volume-gitrepo](~/azure-docs-json-samples/container-instances/aci-deploy-volume-gitrepo.json)]

Wynikowa struktura katalogów dwóch sklonowanych repo zdefiniowanych w poprzednim szablonie jest:

```
/mnt/repo1/aci-helloworld
/mnt/repo2/my-custom-clone-directory
```

Aby wyświetlić przykład wdrożenia wystąpienia kontenera przy użyciu szablonu Azure Resource Manager, zobacz [Wdrażanie grup z wieloma](container-instances-multi-container-group.md)kontenerami w Azure Container Instances .

## <a name="private-git-repo-authentication"></a>Uwierzytelnianie prywatnego repozytorium Git

Aby zainstalować wolumin gitRepo dla prywatnego repozytorium Git, określ poświadczenia w adresie URL repozytorium. Zazwyczaj poświadczenia mają postać nazwy użytkownika i osobistego tokenu dostępu, który przyznaje dostęp do repozytorium w zakresie.

Na przykład parametr interfejsu wiersza polecenia platformy Azure dla prywatnego repozytorium GitHub będzie wyglądać podobnie do następującego (gdzie "gituser" to nazwa użytkownika `--gitrepo-url` usługi GitHub, a "abcdef1234fdsa4321abcdef" to osobisty token dostępu użytkownika):

```console
--gitrepo-url https://gituser:abcdef1234fdsa4321abcdef@github.com/GitUser/some-private-repository
```

W przypadku Azure Repos Git określ dowolną nazwę użytkownika (możesz użyć nazwy "azurereposuser", jak w poniższym przykładzie) w połączeniu z prawidłowym patem użytkownika:

```console
--gitrepo-url https://azurereposuser:abcdef1234fdsa4321abcdef@dev.azure.com/your-org/_git/some-private-repository
```

Aby uzyskać więcej informacji na temat osobistych tokenów dostępu dla usług GitHub i Azure Repos, zobacz:

GitHub: [Tworzenie osobistego tokenu dostępu dla wiersza polecenia][pat-github]

Azure Repos: [tworzenie osobistych tokenów dostępu w celu uwierzytelniania dostępu][pat-repos]

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
[az-container-create]: /cli/azure/container#az_container_create
[az-container-exec]: /cli/azure/container#az_container_exec
