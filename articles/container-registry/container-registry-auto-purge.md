---
title: Przeczyszczanie tagów i manifestów
description: Użyj polecenia przeczyszczania, aby usunąć wiele tagów i manifestów z rejestru kontenerów platformy Azure na podstawie wieku i filtru tagów, a także opcjonalnie zaplanuj operacje przeczyszczania.
ms.topic: article
ms.date: 02/19/2021
ms.openlocfilehash: 562d1940459cb1594b7cd9aca2af280b05a4e419
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784195"
---
# <a name="automatically-purge-images-from-an-azure-container-registry"></a>Automatyczne przeczyszczanie obrazów z rejestru kontenerów platformy Azure

Jeśli używasz rejestru kontenerów platformy Azure w ramach przepływu pracy programowania, rejestr może szybko wypełniać obrazy lub inne artefakty, które nie są potrzebne po krótkim czasie. Możesz usunąć wszystkie tagi starsze niż określony czas lub dopasować je do filtru określonej nazwy. Aby szybko usunąć wiele artefaktów, w tym artykule wprowadzono polecenie, które można uruchomić jako zadanie usługi ACR na żądanie lub `acr purge` [zaplanowane.](container-registry-tasks-scheduled.md) 

Polecenie jest obecnie dystrybuowane w publicznym obrazie kontenera ( ), zbudowanym na pomocą kodu źródłowego w repozytorium `acr purge` `mcr.microsoft.com/acr/acr-cli:0.4` [acr-cli](https://github.com/Azure/acr-cli) w usłudze GitHub.

Aby uruchomić przykłady zadań usługi ACR w tym artykule, możesz użyć interfejsu wiersza polecenia usługi Azure Azure Cloud Shell lub lokalnej instalacji interfejsu wiersza polecenia platformy Azure. Jeśli chcesz używać go lokalnie, wymagana jest wersja 2.0.76 lub nowsza. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install]. 

> [!IMPORTANT]
> Ta funkcja jest obecnie w wersji zapoznawczej. Wersje zapoznawcze są udostępniane pod warunkiem udzielenia zgody na [dodatkowe warunki użytkowania][terms-of-use]. Niektóre cechy funkcji mogą ulec zmianie, zanim stanie się ona ogólnie dostępna.

> [!WARNING]
> Użyj polecenia `acr purge` z ostrzeżeniem — usunięte dane obrazu są NIEODWRACALNE. Jeśli masz systemy, które ściągają obrazy za pomocą skrótu manifestu (w przeciwieństwie do nazwy obrazu), nie należy przeczyszczać obrazów nieoznakowanych. Usunięcie nieoznakowanych obrazów uniemożliwi tym systemom ściąganie obrazów z rejestru. Zamiast ściągać według manifestu, rozważ przyjęcie *unikatowego* schematu tagowania, co jest [zalecanym najlepszym rozwiązaniem.](container-registry-image-tag-version.md)

Jeśli chcesz usunąć tagi lub manifesty pojedynczego obrazu przy użyciu poleceń interfejsu wiersza polecenia platformy Azure, zobacz [Usuwanie obrazów kontenerów](container-registry-delete.md)w Azure Container Registry .

## <a name="use-the-purge-command"></a>Użyj polecenia przeczyszczania

Polecenie container usuwa obrazy według tagów w repozytorium, które pasują do filtru nazwy i są starsze `acr purge` niż określony czas trwania. Domyślnie usuwane są tylko odwołania do tagów, a nie [podstawowe manifesty i](container-registry-concepts.md#manifest) dane warstw. Polecenie ma opcję usuwania manifestów. 

> [!NOTE]
> `acr purge` nie usuwa tagu obrazu ani repozytorium, w którym `write-enabled` atrybut ma wartość `false` . Aby uzyskać więcej informacji, zobacz Lock a container image in an Azure container registry (Blokowanie obrazu kontenera [w rejestrze kontenerów platformy Azure).](container-registry-image-lock.md)

`acr purge` Polecenie jest przeznaczone do uruchamiania jako polecenie kontenera w zadaniu [usługi ACR,](container-registry-tasks-overview.md)dzięki czemu jest automatycznie uwierzytelniane w rejestrze, w którym jest uruchamiane zadanie i wykonuje w nim akcje. Przykłady zadań w tym artykule używają `acr purge` [aliasu polecenia,](container-registry-tasks-reference-yaml.md#aliases) a nie w pełni kwalifikowanego polecenia obrazu kontenera.

Podczas uruchamiania co najmniej określ następujące `acr purge` elementy:

* `--filter` — Repozytorium i wyrażenie *regularne do* filtrowania tagów w repozytorium. Przykłady: dopasowuje wszystkie tagi w repozytorium i tagi `--filter "hello-world:.*"` `hello-world` `--filter "hello-world:^1.*"` zaczynające się od `1` . Przekaż wiele `--filter` parametrów, aby przeczyścić wiele repozytoriów.
* `--ago` - Ciąg czasu trwania w stylu go [wskazujący](https://golang.org/pkg/time/) czas trwania, po którym obrazy są usuwane. Czas trwania składa się z sekwencji co najmniej jednej liczby dziesiętnej, z których każda ma sufiks jednostki. Prawidłowe jednostki czasu obejmują "d" dla dni, "h" dla godzin i "m" dla minut. Na przykład wybiera wszystkie odfiltrowane obrazy ostatnio zmodyfikowane ponad 2 dni, 3 godziny i 6 minut temu, a następnie wybiera obrazy ostatnio zmodyfikowane ponad `--ago 2d3h6m` `--ago 1.5h` 1,5 godz. temu.

`acr purge` Obsługuje kilka parametrów opcjonalnych. W przykładach w tym artykule są używane następujące dwa elementy:

* `--untagged` - Określa, że manifesty, które nie mają skojarzonych tagów *(manifesty bez tagów),* są usuwane.
* `--dry-run` - Określa, że żadne dane nie zostaną usunięte, ale dane wyjściowe są takie same, jak w przypadku uruchomienia polecenia bez tej flagi. Ten parametr jest przydatny do testowania polecenia przeczyszczania, aby upewnić się, że nie powoduje przypadkowego usunięcia danych, które mają być zachowywane.
* `--keep` - Określa, że zostanie zachowana najnowsza liczba x tagów do usunięcia.
* `--concurrency` - Określa liczbę zadań przeczyszczania do jednoczesnego przetwarzania. Jeśli ten parametr nie zostanie podany, zostanie użyta wartość domyślna.

Aby uzyskać dodatkowe parametry, `acr purge --help` uruchom . 

`acr purge`Obsługuje inne funkcje poleceń zadania usługi ACR, w [](container-registry-tasks-logs.md) tym zmienne uruchamiania i dzienniki uruchamiania zadań, które są przesyłane strumieniowo, a także zapisywane do późniejszego pobrania. [](container-registry-tasks-reference-yaml.md#run-variables)

### <a name="run-in-an-on-demand-task"></a>Uruchamianie w zadaniu na żądanie

W poniższym przykładzie użyto [polecenia az acr run,][az-acr-run] aby uruchomić `acr purge` polecenie na żądanie. Ten przykład usuwa wszystkie tagi obrazów i manifesty w repozytorium `hello-world` *w myregistry,* które zostały zmodyfikowane ponad 1 dzień temu. Polecenie container jest przekazywane przy użyciu zmiennej środowiskowej . Zadanie jest uruchamiane bez kontekstu źródłowego.

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge --filter 'hello-world:.*' \
  --untagged --ago 1d"

az acr run \
  --cmd "$PURGE_CMD" \
  --registry myregistry \
  /dev/null
```

### <a name="run-in-a-scheduled-task"></a>Uruchamianie w zaplanowanym zadaniu

W poniższym przykładzie użyto [polecenia az acr task create][az-acr-task-create] w celu utworzenia codziennego zaplanowanego zadania usługi [ACR.](container-registry-tasks-scheduled.md) Zadanie przeczyszcza tagi zmodyfikowane ponad 7 dni temu w `hello-world` repozytorium. Polecenie container jest przekazywane przy użyciu zmiennej środowiskowej . Zadanie jest uruchamiane bez kontekstu źródłowego.

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge --filter 'hello-world:.*' \
  --ago 7d"

az acr task create --name purgeTask \
  --cmd "$PURGE_CMD" \
  --schedule "0 0 * * *" \
  --registry myregistry \
  --context /dev/null
```

Uruchom polecenie [az acr task show,][az-acr-task-show] aby zobaczyć, że wyzwalacz czasomierza jest skonfigurowany.

### <a name="purge-large-numbers-of-tags-and-manifests"></a>Przeczyszczanie dużej liczby tagów i manifestów

Przeczyszczanie dużej liczby tagów i manifestów może potrwać kilka minut lub dłużej. Aby przeczyścić tysiące tagów i manifestów, może być konieczne uruchomienie polecenia dłuższego niż domyślny limit czasu 600 sekund dla zadania na żądanie lub 3600 sekund dla zaplanowanego zadania. Jeśli limit czasu zostanie przekroczony, usuwany jest tylko podzbiór tagów i manifestów. Aby upewnić się, że przeczyszczanie na dużą skalę jest zakończone, przekaż `--timeout` parametr , aby zwiększyć wartość. 

Na przykład następujące zadanie na żądanie ustawia limit czasu na 3600 sekund (1 godzinę):

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge --filter 'hello-world:.*' \
  --ago 1d --untagged"

az acr run \
  --cmd "$PURGE_CMD" \
  --registry myregistry \
  --timeout 3600 \
  /dev/null
```

## <a name="example-scheduled-purge-of-multiple-repositories-in-a-registry"></a>Przykład: Zaplanowane przeczyszczanie wielu repozytoriów w rejestrze

W tym przykładzie ominiemy użycie funkcji , aby okresowo czyścić wiele `acr purge` repozytoriów w rejestrze. Na przykład możesz mieć potok developmentowy, który wypycha obrazy do `samples/devimage1` `samples/devimage2` repozytoriów i . Obrazy dewelopera są okresowo importowane do repozytorium produkcyjnego dla wdrożeń, dzięki czemu nie są już potrzebne. Co tydzień przeczyszczasz repozytoria i w ramach przygotowania do pracy w `samples/devimage1` `samples/devimage2` nadchodzącym tygodniu.

### <a name="preview-the-purge"></a>Podgląd przeczyszczania

Przed usunięciem danych zalecamy uruchomienie zadania przeczyszczania na żądanie przy użyciu `--dry-run` parametru . Ta opcja umożliwia wyświetlanie tagów i manifestów, które polecenie przeczyści, bez usuwania żadnych danych. 

W poniższym przykładzie filtr w każdym repozytorium wybiera wszystkie tagi. Parametr `--ago 0d` dopasowuje obrazy wszystkich grup wiekowych w repozytoriach, które pasują do filtrów. Zmodyfikuj kryteria wyboru zgodnie z potrzebami scenariusza. Parametr `--untagged` wskazuje usuwanie manifestów oprócz tagów. Polecenie container jest przekazywane do [polecenia az acr run][az-acr-run] przy użyciu zmiennej środowiskowej.

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge \
  --filter 'samples/devimage1:.*' --filter 'samples/devimage2:.*' \
  --ago 0d --untagged --dry-run"

az acr run \
  --cmd "$PURGE_CMD" \
  --registry myregistry \
  /dev/null
```

Przejrzyj dane wyjściowe polecenia, aby wyświetlić tagi i manifesty zgodne z parametrami wyboru. Ponieważ polecenie jest uruchamiane z `--dry-run` poleceniem , żadne dane nie są usuwane.

Przykładowe dane wyjściowe:

```console
[...]
Deleting tags for repository: samples/devimage1
myregistry.azurecr.io/samples/devimage1:232889b
myregistry.azurecr.io/samples/devimage1:a21776a
Deleting manifests for repository: samples/devimage1
myregistry.azurecr.io/samples/devimage1@sha256:81b6f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e788b
myregistry.azurecr.io/samples/devimage1@sha256:3ded859790e68bd02791a972ab0bae727231dc8746f233a7949e40f8ea90c8b3
Deleting tags for repository: samples/devimage2
myregistry.azurecr.io/samples/devimage2:5e788ba
myregistry.azurecr.io/samples/devimage2:f336b7c
Deleting manifests for repository: samples/devimage2
myregistry.azurecr.io/samples/devimage2@sha256:8d2527cde610e1715ad095cb12bc7ed169b60c495e5428eefdf336b7cb7c0371
myregistry.azurecr.io/samples/devimage2@sha256:ca86b078f89607bc03ded859790e68bd02791a972ab0bae727231dc8746f233a

Number of deleted tags: 4
Number of deleted manifests: 4
[...]
```

### <a name="schedule-the-purge"></a>Planowanie przeczyszczania

Po zweryfikowaniu uruchomienia suchego utwórz zaplanowane zadanie w celu zautomatyzowania przeczyszczania. W poniższym przykładzie zaplanowano cotygodniowe zadanie w niedzielę o godzinie 1:00 czasu UTC, aby uruchomić poprzednie polecenie przeczyszczania:

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge \
  --filter 'samples/devimage1:.*' --filter 'samples/devimage2:.*' \
  --ago 0d --untagged"

az acr task create --name weeklyPurgeTask \
  --cmd "$PURGE_CMD" \
  --schedule "0 1 * * Sun" \
  --registry myregistry \
  --context /dev/null
```

Uruchom polecenie [az acr task show,][az-acr-task-show] aby zobaczyć, że wyzwalacz czasomierza jest skonfigurowany.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o innych [opcjach usuwania danych obrazu w](container-registry-delete.md) Azure Container Registry.

Aby uzyskać więcej informacji na temat magazynu obrazów, zobacz [Magazyn obrazów kontenerów w Azure Container Registry](container-registry-storage.md).

<!-- LINKS - External -->

[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-acr-run]: /cli/azure/acr#az_acr_run
[az-acr-task-create]: /cli/azure/acr/task#az_acr_task_create
[az-acr-task-show]: /cli/azure/acr/task#az_acr_task_show
