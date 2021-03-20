---
title: Blokowanie obrazów
description: Ustaw atrybuty dla obrazu kontenera lub repozytorium, aby nie można go było usunąć ani zastąpić w rejestrze kontenerów platformy Azure.
ms.topic: article
ms.date: 09/30/2019
ms.openlocfilehash: da84767523bb6d948b71b1c1ad2ddaffb628354a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "77659700"
---
# <a name="lock-a-container-image-in-an-azure-container-registry"></a>Blokowanie obrazu kontenera w usłudze Azure Container Registry

W usłudze Azure Container Registry można zablokować wersję obrazu lub repozytorium, aby nie można go było usunąć ani zaktualizować. Aby zablokować obraz lub repozytorium, zaktualizuj jego atrybuty przy użyciu interfejsu wiersza polecenia platformy Azure [AZ ACR Repository Update][az-acr-repository-update]. 

Ten artykuł wymaga uruchomienia interfejsu wiersza polecenia platformy Azure w Azure Cloud Shell lub lokalnie (zalecane jest w wersji 2.0.55 lub nowszej). Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli].

> [!IMPORTANT]
> Ten artykuł nie dotyczy blokowania całego rejestru, na przykład przy użyciu **ustawień > blokad** w Azure Portal lub `az lock` poleceń w interfejsie wiersza polecenia platformy Azure. Zablokowanie zasobu rejestru nie uniemożliwia tworzenia, aktualizowania lub usuwania danych w repozytoriach. Blokowanie rejestru dotyczy tylko operacji zarządzania, takich jak dodawanie lub usuwanie replikacji lub usuwanie samego rejestru. Więcej informacji w obszarze [Zablokuj zasoby, aby zapobiec nieoczekiwanym zmianom](../azure-resource-manager/management/lock-resources.md).

## <a name="scenarios"></a>Scenariusze

Domyślnie oznakowany obraz w Azure Container Registry jest *modyfikowalny*, więc z odpowiednimi uprawnieniami można wielokrotnie aktualizować i wypchnąć obraz z tym samym tagiem do rejestru. Obrazy kontenerów można także [usuwać](container-registry-delete.md) w razie konieczności. To zachowanie jest przydatne w przypadku tworzenia obrazów i zachowywania rozmiaru rejestru.

Jednak podczas wdrażania obrazu kontenera w środowisku produkcyjnym może być potrzebny *niezmienny* obraz kontenera. Niezmienny obraz to taki, którego nie można przypadkowo usunąć ani zastąpić.

Zapoznaj się z [zaleceniami dotyczącymi tagowania i przechowywania wersji obrazów kontenerów](container-registry-image-tag-version.md) w celu uzyskania strategii tagów i wersji obrazów w rejestrze.

Użyj polecenia [AZ ACR Repository Update][az-acr-repository-update] , aby ustawić atrybuty repozytorium, dzięki czemu możesz:

* Zablokuj wersję obrazu lub całe repozytorium

* Ochrona wersji obrazu lub repozytorium przed usunięciem, ale Zezwalaj na aktualizacje

* Zapobiegaj operacji odczytu (ściągania) w wersji obrazu lub w całym repozytorium

Przykłady można znaleźć w poniższych sekcjach. 

## <a name="lock-an-image-or-repository"></a>Blokowanie obrazu lub repozytorium 

### <a name="show-the-current-repository-attributes"></a>Pokaż atrybuty bieżącego repozytorium
Aby wyświetlić bieżące atrybuty repozytorium, uruchom następujące polecenie [AZ ACR Repository show][az-acr-repository-show] :

```azurecli
az acr repository show \
    --name myregistry --repository myrepo \
    --output jsonc
```

### <a name="show-the-current-image-attributes"></a>Pokaż atrybuty bieżącego obrazu
Aby wyświetlić bieżące atrybuty tagu, uruchom następujące polecenie [AZ ACR Repository show][az-acr-repository-show] :

```azurecli
az acr repository show \
    --name myregistry --image image:tag \
    --output jsonc
```

### <a name="lock-an-image-by-tag"></a>Zablokuj obraz przez tag

Aby zablokować obraz *taga/webimage: tag* w *rejestrze*, uruchom następujące polecenie [AZ ACR Repository Update][az-acr-repository-update] :

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --write-enabled false
```

### <a name="lock-an-image-by-manifest-digest"></a>Zablokuj obraz przez podsumowanie manifestu

Aby zablokować obraz z *repozytorium/* obrazu identyfikowany przez szyfrowanie manifestu (skrót SHA-256, reprezentowane jako `sha256:...` ), uruchom następujące polecenie. (Aby znaleźć podsumowanie manifestu skojarzone z co najmniej jednym tagiem obrazu, uruchom polecenie [AZ ACR Repository show-Manifests][az-acr-repository-show-manifests] .)

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage@sha256:123456abcdefg \
    --write-enabled false
```

### <a name="lock-a-repository"></a>Blokowanie repozytorium

Aby zablokować repozytorium moje repozytorium */obrazy* i wszystkie znajdujące się w nim obrazy, uruchom następujące polecenie:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --write-enabled false
```

## <a name="protect-an-image-or-repository-from-deletion"></a>Ochrona obrazu lub repozytorium przed usunięciem

### <a name="protect-an-image-from-deletion"></a>Ochrona obrazu przed usunięciem

Aby zezwolić na aktualizowanie obrazu *tagu/repozytorium:* , ale nie został usunięty, uruchom następujące polecenie:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled false --write-enabled true
```

### <a name="protect-a-repository-from-deletion"></a>Ochrona repozytorium przed usunięciem

Następujące polecenie ustawia repozytorium moje *repozytorium/zdjęcie* , więc nie można go usunąć. Poszczególne obrazy można nadal aktualizować lub usuwać.

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled false --write-enabled true
```

## <a name="prevent-read-operations-on-an-image-or-repository"></a>Zapobiegaj operacji odczytu w obrazie lub repozytorium

Aby zapobiec operacji odczytu (ściągania) na obrazie *webrepozytorium/webimage: tag* , uruchom następujące polecenie:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --read-enabled false
```

Aby zapobiec operacji odczytu wszystkich obrazów w repozytorium moje *repozytorium/zdjęcie* , uruchom następujące polecenie:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --read-enabled false
```

## <a name="unlock-an-image-or-repository"></a>Odblokowywanie obrazu lub repozytorium

Aby przywrócić domyślne zachowanie obrazu *taga/webimage: tag* , aby można go było usunąć i zaktualizować, uruchom następujące polecenie:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled true --write-enabled true
```

Aby przywrócić domyślne zachowanie repozytorium moje repozytorium */obrazu* oraz wszystkie obrazy, aby można je było usunąć i zaktualizować, uruchom następujące polecenie:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled true --write-enabled true
```

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono informacje dotyczące korzystania z polecenia [AZ ACR Repository Update][az-acr-repository-update] w celu zapobiegania usunięciu lub aktualizowaniu wersji obrazu w repozytorium. Aby ustawić dodatkowe atrybuty, zobacz [AZ ACR Repository Update][az-acr-repository-update] Reference.

Aby wyświetlić atrybuty ustawione dla wersji obrazu lub repozytorium, użyj polecenia [AZ ACR Repository show][az-acr-repository-show] .

Aby uzyskać szczegółowe informacje o operacjach usuwania, zobacz [usuwanie obrazów kontenerów w Azure Container Registry][container-registry-delete].

<!-- LINKS - Internal -->
[az-acr-repository-update]: /cli/azure/acr/repository#az-acr-repository-update
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-delete]: container-registry-delete.md

