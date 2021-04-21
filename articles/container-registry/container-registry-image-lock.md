---
title: Blokowanie obrazów
description: Ustaw atrybuty dla obrazu kontenera lub repozytorium, aby nie można było go usunąć ani nadpisać w rejestrze kontenerów platformy Azure.
ms.topic: article
ms.date: 09/30/2019
ms.openlocfilehash: 340beb1bb6666ddf0de7de38adee6be71f5f52bd
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772347"
---
# <a name="lock-a-container-image-in-an-azure-container-registry"></a>Blokowanie obrazu kontenera w rejestrze kontenerów platformy Azure

W rejestrze kontenerów platformy Azure można zablokować wersję obrazu lub repozytorium, aby nie można było go usunąć ani zaktualizować. Aby zablokować obraz lub repozytorium, zaktualizuj jego atrybuty przy użyciu polecenia interfejsu wiersza polecenia platformy Azure [az acr repository update.][az-acr-repository-update] 

Ten artykuł wymaga uruchomienia interfejsu wiersza polecenia platformy Azure Azure Cloud Shell lokalnie (zalecana jest wersja 2.0.55 lub nowsza). Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli].

> [!IMPORTANT]
> Ten artykuł nie dotyczy blokowania całego rejestru, na przykład używania ustawień **> blokady** w Azure Portal lub poleceń w interfejsie wiersza polecenia `az lock` platformy Azure. Blokowanie zasobu rejestru nie uniemożliwia tworzenia, aktualizowania ani usuwania danych w repozytoriach. Blokowanie rejestru ma wpływ tylko na operacje zarządzania, takie jak dodawanie lub usuwanie replikacji lub usuwanie samego rejestru. Więcej informacji zawiera [temat Blokowanie zasobów w celu zapobiegania nieoczekiwanym zmianom.](../azure-resource-manager/management/lock-resources.md)

## <a name="scenarios"></a>Scenariusze

Domyślnie otagowany obraz w Azure Container Registry jest modyfikowalny, więc przy odpowiednich uprawnieniach można wielokrotnie aktualizować i wypychać obraz z tym samym tagiem do rejestru. W razie potrzeby można również [usunąć obrazy](container-registry-delete.md) kontenerów. To zachowanie jest przydatne w przypadku tworzenia obrazów i konieczności zachowania rozmiaru rejestru.

Jednak podczas wdrażania obrazu kontenera w środowisku produkcyjnym może być potrzebny *niezmienny obraz* kontenera. Obraz niezmienny to taki, który nie może zostać przypadkowo usunięty ani nadpisany.

Zobacz [Zalecenia dotyczące tagowania i wersjonarowania obrazów kontenerów,](container-registry-image-tag-version.md) aby uzyskać informacje na temat strategii tagowania i wersjowania obrazów w rejestrze.

Użyj polecenia [az acr repository update,][az-acr-repository-update] aby ustawić atrybuty repozytorium, aby:

* Blokowanie wersji obrazu lub całego repozytorium

* Ochrona wersji obrazu lub repozytorium przed usunięciem, ale zezwalanie na aktualizacje

* Uniemożliwianie operacji odczytu (ściągania) w wersji obrazu lub w całym repozytorium

Przykłady można znaleźć w poniższych sekcjach. 

## <a name="lock-an-image-or-repository"></a>Blokowanie obrazu lub repozytorium 

### <a name="show-the-current-repository-attributes"></a>Wyświetlanie bieżących atrybutów repozytorium
Aby wyświetlić bieżące atrybuty repozytorium, uruchom następujące polecenie [az acr repository show:][az-acr-repository-show]

```azurecli
az acr repository show \
    --name myregistry --repository myrepo \
    --output jsonc
```

### <a name="show-the-current-image-attributes"></a>Wyświetlanie bieżących atrybutów obrazu
Aby wyświetlić bieżące atrybuty tagu, uruchom następujące [polecenie az acr repository show:][az-acr-repository-show]

```azurecli
az acr repository show \
    --name myregistry --image image:tag \
    --output jsonc
```

### <a name="lock-an-image-by-tag"></a>Blokowanie obrazu według tagu

Aby zablokować *obraz myrepo/myimage:tag* w repozytorium *myregistry,* uruchom następujące [polecenie az acr repository update:][az-acr-repository-update]

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --write-enabled false
```

### <a name="lock-an-image-by-manifest-digest"></a>Blokowanie obrazu za pomocą skrótu manifestu

Aby zablokować *obraz myrepo/myimage* identyfikowany przez skrót manifestu (skrót SHA-256 reprezentowany jako `sha256:...` ), uruchom następujące polecenie. (Aby znaleźć skrót manifestu skojarzony z co najmniej jednym tagiem obrazu, uruchom polecenie [az acr repository show-manifests).][az-acr-repository-show-manifests]

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage@sha256:123456abcdefg \
    --write-enabled false
```

### <a name="lock-a-repository"></a>Blokowanie repozytorium

Aby zablokować *repozytorium myrepo/myimage* i wszystkie obrazy w nim, uruchom następujące polecenie:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --write-enabled false
```

## <a name="protect-an-image-or-repository-from-deletion"></a>Ochrona obrazu lub repozytorium przed usunięciem

### <a name="protect-an-image-from-deletion"></a>Ochrona obrazu przed usunięciem

Aby zezwolić *na zaktualizowanie obrazu myrepo/myimage:tag,* ale nie zostanie usunięty, uruchom następujące polecenie:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled false --write-enabled true
```

### <a name="protect-a-repository-from-deletion"></a>Ochrona repozytorium przed usunięciem

Następujące polecenie ustawia *repozytorium myrepo/myimage,* aby nie można było go usunąć. Poszczególne obrazy można nadal aktualizować lub usuwać.

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled false --write-enabled true
```

## <a name="prevent-read-operations-on-an-image-or-repository"></a>Zapobieganie operacjam odczytu obrazu lub repozytorium

Aby uniemożliwić operacje odczytu (ściągania) na *obrazie myrepo/myimage:tag,* uruchom następujące polecenie:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --read-enabled false
```

Aby uniemożliwić odczytywanie wszystkich obrazów w *repozytorium myrepo/myimage,* uruchom następujące polecenie:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --read-enabled false
```

## <a name="unlock-an-image-or-repository"></a>Odblokowywanie obrazu lub repozytorium

Aby przywrócić domyślne zachowanie obrazu *myrepo/myimage:tag,* aby można go było usunąć i zaktualizować, uruchom następujące polecenie:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled true --write-enabled true
```

Aby przywrócić domyślne zachowanie *repozytorium myrepo/myimage* i wszystkich obrazów, aby można je było usunąć i zaktualizować, uruchom następujące polecenie:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled true --write-enabled true
```

## <a name="next-steps"></a>Następne kroki

W tym artykule za pomocą polecenia [az acr repository update][az-acr-repository-update] wiesz już, jak zapobiegać usuwaniu lub aktualizowaniu wersji obrazów w repozytorium. Aby ustawić dodatkowe atrybuty, zobacz informacje o poleceniu [az acr repository update.][az-acr-repository-update]

Aby wyświetlić atrybuty ustawione dla wersji obrazu lub repozytorium, użyj [polecenia az acr repository show.][az-acr-repository-show]

Aby uzyskać szczegółowe informacje o operacjach usuwania, zobacz [Usuwanie obrazów kontenerów w Azure Container Registry][container-registry-delete].

<!-- LINKS - Internal -->
[az-acr-repository-update]: /cli/azure/acr/repository#az_acr_repository_update
[az-acr-repository-show]: /cli/azure/acr/repository#az_acr_repository_show
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az_acr_repository_show_manifests
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-delete]: container-registry-delete.md
