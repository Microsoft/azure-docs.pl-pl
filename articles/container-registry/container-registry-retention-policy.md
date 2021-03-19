---
title: Zasady do zachowywania nieoznakowanych manifestów
description: Informacje o sposobie włączania zasad przechowywania w rejestrze kontenerów platformy Azure w celu automatycznego usunięcia nieoznakowanych manifestów po upływie określonego czasu.
ms.topic: article
ms.date: 10/02/2019
ms.openlocfilehash: 5dda85934bb10cf16fd90381539b892df4f5445c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "83683460"
---
# <a name="set-a-retention-policy-for-untagged-manifests"></a>Ustawianie zasad przechowywania dla nieoznakowanych manifestów

Azure Container Registry zapewnia opcję ustawiania *zasad przechowywania* dla zapisywanych manifestów obrazów, które nie mają skojarzonych tagów (*nieoznakowane manifesty*). Po włączeniu zasad przechowywania, nieoznaczone manifesty w rejestrze są automatycznie usuwane po upływie kilku dni. Ta funkcja zapobiega wypełnianiu przez rejestr artefaktów, które nie są potrzebne, i pomaga zaoszczędzić na kosztach magazynowania. Jeśli `delete-enabled` atrybut manifestu bez znaczników jest ustawiony na `false` , nie można usunąć manifestu, a zasady przechowywania nie są stosowane.

Możesz użyć Azure Cloud Shell lub lokalnej instalacji interfejsu wiersza polecenia platformy Azure, aby uruchomić przykłady poleceń w tym artykule. Jeśli chcesz używać go lokalnie, wymagana jest wersja 2.0.74 lub nowsza. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli].

Zasady przechowywania to funkcja rejestrów kontenerów w **warstwie Premium** . Aby uzyskać informacje na temat warstw usługi Registry, zobacz [Azure Container Registry warstwy usług](container-registry-skus.md).

> [!IMPORTANT]
> Ta funkcja jest obecnie dostępna w wersji zapoznawczej, a niektóre [ograniczenia mają zastosowanie](#preview-limitations). Wersje zapoznawcze są udostępniane pod warunkiem udzielenia zgody na [dodatkowe warunki użytkowania][terms-of-use]. Niektóre cechy funkcji mogą ulec zmianie, zanim stanie się ona ogólnie dostępna.

> [!WARNING]
> Ustawianie zasad przechowywania z obsługą opieki — usunięte dane obrazu są NIEODWRACALNe. Jeśli masz systemy, które pobierają obrazy za pomocą skrótu manifestu (w przeciwieństwie do nazwy obrazu), nie należy ustawiać zasad przechowywania dla nieoznakowanych manifestów. Usunięcie nieoznakowanych obrazów uniemożliwi tym systemom ściąganie obrazów z rejestru. Zamiast ściągania według manifestu należy rozważyć przyjęcie *unikatowego schematu znakowania* , [zalecane najlepsze rozwiązanie](container-registry-image-tag-version.md).

## <a name="preview-limitations"></a>Ograniczenia wersji zapoznawczej

* Zasady przechowywania można ustawić tylko dla manifestów nieoznakowanych.
* Zasady przechowywania są obecnie stosowane tylko do manifestów, które są nieoznakowane *po* włączeniu zasad. Istniejące nieoznaczone manifesty w rejestrze nie podlegają zasadom. Aby usunąć istniejące nieoznaczone manifesty, zobacz przykłady w temacie [usuwanie obrazów kontenera w Azure Container Registry](container-registry-delete.md).

## <a name="about-the-retention-policy"></a>Informacje o zasadach przechowywania

Azure Container Registry zlicza odwołania do manifestów w rejestrze. Gdy manifest jest nieoznakowany, sprawdza zasady przechowywania. Jeśli zasady przechowywania są włączone, operacja usuwania manifestu jest umieszczana w kolejce z określoną datą, zgodnie z liczbą dni ustawioną w zasadach.

Osobne zadanie zarządzania kolejkami ciągle przetwarza komunikaty, skalowanie w razie konieczności. Załóżmy na przykład, że nie istnieją dwa manifesty, od 1 godziny, w rejestrze z zasadami przechowywania wynoszącymi 30 dni. Dwa komunikaty zostaną dodane do kolejki. Następnie 30 dni później, po 1 godzinie, komunikaty zostaną pobrane z kolejki i przetworzone, przy założeniu, że zasady nadal działają.

## <a name="set-a-retention-policy---cli"></a>Ustawianie zasad przechowywania — interfejs wiersza polecenia

Poniższy przykład pokazuje, jak używać interfejsu wiersza polecenia platformy Azure do ustawiania zasad przechowywania dla nieoznakowanych manifestów w rejestrze.

### <a name="enable-a-retention-policy"></a>Włączanie zasad przechowywania

Domyślnie żadna zasada przechowywania nie jest ustawiona w rejestrze kontenerów. Aby ustawić lub zaktualizować zasady przechowywania, uruchom polecenie [AZ ACR config Recovery Update][az-acr-config-retention-update] w interfejsie wiersza polecenia platformy Azure. Możesz określić liczbę dni z zakresu od 0 do 365, aby zachować nieoznaczone manifesty. Jeśli nie określisz liczby dni, polecenie ustawia domyślną wartość 7 dni. Po okresie przechowywania wszystkie nieoznakowane manifesty w rejestrze zostaną automatycznie usunięte.

Poniższy przykład ustawia zasady przechowywania wynoszące 30 dni dla nieoznakowanych manifestów w *rejestrze rejestr:*

```azurecli
az acr config retention update --registry myregistry --status enabled --days 30 --type UntaggedManifests
```

W poniższym przykładzie są ustawiane zasady usuwania dowolnego manifestu w rejestrze, gdy tylko jest to nieoznakowane. Utwórz te zasady, ustawiając okres przechowywania wynoszący 0 dni. 

```azurecli
az acr config retention update --registry myregistry --status enabled --days 0 --type UntaggedManifests
```

### <a name="validate-a-retention-policy"></a>Sprawdzanie poprawności zasad przechowywania

W przypadku włączenia powyższych zasad z okresem przechowywania równym 0 dni można szybko sprawdzić, czy usunięte manifestu nie są usuwane:

1. Wypchnij obraz obrazu testowego `hello-world:latest` do rejestru lub Zastąp inny wybrany obraz testowy.
1. UNTAG `hello-world:latest` obraz, na przykład za pomocą polecenia [AZ ACR Repository UNTAG][az-acr-repository-untag] . Manifest nieoznakowany pozostaje w rejestrze.
    ```azurecli
    az acr repository untag --name myregistry --image hello-world:latest
    ```
1. W ciągu kilku sekund nieoznakowany manifest zostanie usunięty. Można sprawdzić, czy usuwasz listę manifestów w repozytorium, na przykład za pomocą polecenia [AZ ACR Repository show-Manifests][az-acr-repository-show-manifests] . Jeśli obraz testowy był jedynym z nich w repozytorium, samo repozytorium zostanie usunięte.

### <a name="disable-a-retention-policy"></a>Wyłącz zasady przechowywania

Aby wyświetlić zasady przechowywania ustawione w rejestrze, uruchom polecenie [AZ ACR config retencja show][az-acr-config-retention-show] :

```azurecli
az acr config retention show --registry myregistry
```

Aby wyłączyć zasady przechowywania w rejestrze, uruchom polecenie [AZ ACR config przechowywanie aktualizacji][az-acr-config-retention-update] i ustaw `--status disabled` następujące ustawienia:

```azurecli
az acr config retention update --registry myregistry --status disabled --type UntaggedManifests
```

## <a name="set-a-retention-policy---portal"></a>Ustawianie zasad przechowywania — Portal

Można również ustawić zasady przechowywania rejestru w [Azure Portal](https://portal.azure.com). W poniższym przykładzie pokazano, jak za pomocą portalu ustawić zasady przechowywania dla nieoznakowanych manifestów w rejestrze.

### <a name="enable-a-retention-policy"></a>Włączanie zasad przechowywania

1. Przejdź do usługi Azure Container Registry. W obszarze **zasady** wybierz pozycję **przechowywanie** (wersja zapoznawcza).
1. W obszarze **stan** wybierz pozycję **włączone**.
1. Wybierz liczbę dni z przedziału od 0 do 365, aby zachować nieoznaczone manifesty. Wybierz pozycję **Zapisz**.

![Włączanie zasad przechowywania w Azure Portal](media/container-registry-retention-policy/container-registry-retention-policy01.png)

### <a name="disable-a-retention-policy"></a>Wyłącz zasady przechowywania

1. Przejdź do usługi Azure Container Registry. W obszarze **zasady** wybierz pozycję **przechowywanie** (wersja zapoznawcza).
1. W obszarze **stan** wybierz pozycję **wyłączone**. Wybierz pozycję **Zapisz**.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o opcjach [usuwania obrazów i repozytoriów](container-registry-delete.md) w programie Azure Container Registry

* Dowiedz się, jak [automatycznie czyścić](container-registry-auto-purge.md) wybrane obrazy i manifesty z rejestru

* Dowiedz się więcej o opcjach [blokowania obrazów i manifestów](container-registry-image-lock.md) w rejestrze

<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/


<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-config-retention-update]: /cli/azure/acr/config/retention#az-acr-config-retention-update
[az-acr-config-retention-show]: /cli/azure/acr/config/retention#az-acr-config-retention-show
[az-acr-repository-untag]: /cli/azure/acr/repository#az-acr-repository-untag
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
