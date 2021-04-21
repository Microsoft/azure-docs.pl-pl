---
title: Zasady do zachowywania nieoznakowanych manifestów
description: Dowiedz się, jak włączyć zasady przechowywania w rejestrze kontenerów platformy Azure w celu automatycznego usuwania nieoznakowanych manifestów po upływie określonego czasu.
ms.topic: article
ms.date: 10/02/2019
ms.openlocfilehash: 81ce92a2533cc8a688be43da9406cd5b0e726509
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781347"
---
# <a name="set-a-retention-policy-for-untagged-manifests"></a>Ustawianie zasad przechowywania dla nieoznakowanych manifestów

Azure Container Registry udostępnia opcję ustawienia zasad  przechowywania dla manifestów przechowywanych obrazów, które nie mają żadnych skojarzonych tagów *(manifesty bez tagów).* Po włączeniu zasad przechowywania nieoznakowane manifesty w rejestrze są automatycznie usuwane po upływie ustawionej liczby dni. Ta funkcja uniemożliwia wypełnianie rejestru artefaktami, które nie są potrzebne, i pomaga zaoszczędzić na kosztach magazynowania. Jeśli atrybut manifestu bez atrybutu jest ustawiony na wartość , manifestu nie można usunąć, a zasady przechowywania `delete-enabled` `false` nie mają zastosowania.

Aby uruchomić przykłady poleceń Azure Cloud Shell tym artykule, możesz użyć interfejsu wiersza polecenia lub lokalnej instalacji interfejsu wiersza polecenia platformy Azure. Jeśli chcesz używać go lokalnie, wymagana jest wersja 2.0.74 lub nowsza. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli].

Zasady przechowywania to funkcja rejestrów kontenerów w wersji **Premium.** Aby uzyskać informacje o warstwach usług rejestru, [zobacz Azure Container Registry usług .](container-registry-skus.md)

> [!IMPORTANT]
> Ta funkcja jest obecnie dostępna w wersji zapoznawczej i obowiązują [pewne ograniczenia.](#preview-limitations) Wersje zapoznawcze są udostępniane pod warunkiem udzielenia zgody na [dodatkowe warunki użytkowania][terms-of-use]. Niektóre cechy funkcji mogą ulec zmianie, zanim stanie się ona ogólnie dostępna.

> [!WARNING]
> Ustaw zasady przechowywania z zachowaniem ostrożności — usunięte dane obrazu są NIEODWRACALNE. Jeśli masz systemy, które ściągają obrazy za pomocą skrótu manifestu (w przeciwieństwie do nazwy obrazu), nie należy ustawiać zasad przechowywania dla nieoznakowanych manifestów. Usunięcie nieoznakowanych obrazów uniemożliwi tym systemom ściąganie obrazów z rejestru. Zamiast ściągać według manifestu, rozważ przyjęcie *unikatowego* schematu tagowania, co jest [zalecanym najlepszym rozwiązaniem.](container-registry-image-tag-version.md)

## <a name="preview-limitations"></a>Ograniczenia wersji zapoznawczej

* Zasady przechowywania można ustawić tylko dla nieoznakowanych manifestów.
* Zasady przechowywania mają obecnie zastosowanie tylko do manifestów, które nie są oznaczone *po* włączeniu zasad. Istniejące nieoznakowane manifesty w rejestrze nie podlegają zasadom. Aby usunąć istniejące nieoznakowane manifesty, zobacz przykłady w teście [Usuwanie obrazów kontenerów](container-registry-delete.md)w Azure Container Registry .

## <a name="about-the-retention-policy"></a>Informacje o zasadach przechowywania

Azure Container Registry odwoływać się do zliczania manifestów w rejestrze. Gdy manifest jest nieoznakowany, sprawdza zasady przechowywania. Jeśli zasady przechowywania są włączone, operacja usuwania manifestu jest kolejkowana z określoną datą zgodnie z liczbą dni ustawioną w zasadach.

Oddzielne zadanie zarządzania kolejkami stale przetwarza komunikaty i skaluje je zgodnie z potrzebami. Załóżmy na przykład, że w rejestrze nieoznakowane są dwa manifesty, 1 godzina, z zasadami przechowywania o wartości 30 dni. Dwa komunikaty zostaną w kolejce. Następnie, po 30 dniach( około 1 godzinie od siebie), komunikaty zostaną pobrane z kolejki i przetworzone przy założeniu, że zasady nadal obowiązywały.

## <a name="set-a-retention-policy---cli"></a>Ustawianie zasad przechowywania — interfejs wiersza polecenia

W poniższym przykładzie pokazano, jak za pomocą interfejsu wiersza polecenia platformy Azure ustawić zasady przechowywania dla nieoznakowanych manifestów w rejestrze.

### <a name="enable-a-retention-policy"></a>Włączanie zasad przechowywania

Domyślnie w rejestrze kontenerów nie są ustawione żadne zasady przechowywania. Aby ustawić lub zaktualizować zasady przechowywania, uruchom [polecenie az acr config retention update][az-acr-config-retention-update] w interfejsie wiersza polecenia platformy Azure. Możesz określić liczbę dni od 0 do 365, aby zachować nieoznakowane manifesty. Jeśli nie określisz liczby dni, polecenie ustawia wartość domyślną 7 dni. Po upływie okresu przechowywania wszystkie nieoznakowane manifesty w rejestrze są automatycznie usuwane.

Poniższy przykład ustawia zasady przechowywania na 30 dni dla nieoznakowanych manifestów w rejestrze *myregistry*:

```azurecli
az acr config retention update --registry myregistry --status enabled --days 30 --type UntaggedManifests
```

W poniższym przykładzie ustawiane są zasady usuwania dowolnego manifestu w rejestrze natychmiast po jego usunięciu. Utwórz te zasady, ustawiając okres przechowywania na 0 dni. 

```azurecli
az acr config retention update --registry myregistry --status enabled --days 0 --type UntaggedManifests
```

### <a name="validate-a-retention-policy"></a>Weryfikowanie zasad przechowywania

Jeśli włączysz powyższe zasady z okresem przechowywania 0 dni, możesz szybko sprawdzić, czy nieoznakowane manifesty zostały usunięte:

1. Wypchnie obraz testowy do rejestru lub zastąp inny obraz `hello-world:latest` testowy.
1. Odtaguj `hello-world:latest` obraz, na przykład za pomocą [polecenia az acr repository untag.][az-acr-repository-untag] Manifest bez flagi pozostaje w rejestrze.
    ```azurecli
    az acr repository untag --name myregistry --image hello-world:latest
    ```
1. W ciągu kilku sekund manifest bez flagi zostanie usunięty. Usunięcie można zweryfikować, wyświetlając manifesty w repozytorium, na przykład za pomocą polecenia [az acr repository show-manifests.][az-acr-repository-show-manifests] Jeśli obraz testowy był jedynym w repozytorium, samo repozytorium zostanie usunięte.

### <a name="disable-a-retention-policy"></a>Wyłączanie zasad przechowywania

Aby wyświetlić zasady przechowywania ustawione w rejestrze, uruchom [polecenie az acr config retention show:][az-acr-config-retention-show]

```azurecli
az acr config retention show --registry myregistry
```

Aby wyłączyć zasady przechowywania w rejestrze, uruchom polecenie [az acr config retention update][az-acr-config-retention-update] i ustaw polecenie `--status disabled` :

```azurecli
az acr config retention update --registry myregistry --status disabled --type UntaggedManifests
```

## <a name="set-a-retention-policy---portal"></a>Ustawianie zasad przechowywania — portal

Zasady przechowywania rejestru można również ustawić w Azure Portal [.](https://portal.azure.com) W poniższym przykładzie pokazano, jak za pomocą portalu ustawić zasady przechowywania dla nieoznakowanych manifestów w rejestrze.

### <a name="enable-a-retention-policy"></a>Włączanie zasad przechowywania

1. Przejdź do rejestru kontenerów platformy Azure. W **obszarze Zasady** wybierz pozycję **Przechowywanie** (wersja zapoznawcza).
1. W **jęz.** Stanie wybierz pozycję **Włączone.**
1. Wybierz liczbę dni od 0 do 365, aby zachować nieoznakowane manifesty. Wybierz pozycję **Zapisz**.

![Włączanie zasad przechowywania w Azure Portal](media/container-registry-retention-policy/container-registry-retention-policy01.png)

### <a name="disable-a-retention-policy"></a>Wyłączanie zasad przechowywania

1. Przejdź do rejestru kontenerów platformy Azure. W **obszarze Zasady** wybierz pozycję **Przechowywanie** (wersja zapoznawcza).
1. W **1.** stanie wybierz pozycję **Wyłączone.** Wybierz pozycję **Zapisz**.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o opcjach [usuwania obrazów i repozytoriów w](container-registry-delete.md) Azure Container Registry

* Dowiedz się, jak [automatycznie przeczyścić](container-registry-auto-purge.md) wybrane obrazy i manifesty z rejestru

* Dowiedz się więcej o [opcjach blokowania obrazów i manifestów](container-registry-image-lock.md) w rejestrze

<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/


<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-config-retention-update]: /cli/azure/acr/config/retention#az_acr_config_retention_update
[az-acr-config-retention-show]: /cli/azure/acr/config/retention#az_acr_config_retention_show
[az-acr-repository-untag]: /cli/azure/acr/repository#az_acr_repository_untag
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az_acr_repository_show_manifests
