---
title: Przeczyszczanie tagów i manifestów
description: Użyj przeczyszczania polecenia, aby usunąć wiele tagów i manifestów z usługi Azure Container Registry na podstawie wieku i filtru tagów, i opcjonalnie Zaplanuj operacje przeczyszczania.
ms.topic: article
ms.date: 11/10/2020
ms.openlocfilehash: 406a1f231af57407e9475a8888b68aad9d88dcb3
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94445119"
---
# <a name="automatically-purge-images-from-an-azure-container-registry"></a>Automatycznie Przeczyść obrazy z usługi Azure Container Registry

W przypadku korzystania z usługi Azure Container Registry w ramach przepływu pracy deweloperskiej rejestr może szybko zapełniać obrazy lub inne artefakty, które nie są potrzebne po krótkim czasie. Możesz chcieć usunąć wszystkie Tagi, które są starsze niż określony czas trwania lub pasują do określonego filtru nazw. Aby szybko usunąć wiele artefaktów, w tym artykule wprowadzono `acr purge` polecenie, które można uruchomić jako zadanie na żądanie lub [zaplanowane](container-registry-tasks-scheduled.md) ACR. 

`acr purge`Polecenie jest obecnie dystrybuowane w publicznym obrazie kontenera ( `mcr.microsoft.com/acr/acr-cli:0.3` ), który został utworzony z kodu źródłowego w repozytorium [ACR-CLI](https://github.com/Azure/acr-cli) w serwisie GitHub.

Możesz użyć Azure Cloud Shell lub lokalnej instalacji interfejsu wiersza polecenia platformy Azure, aby uruchomić przykłady zadań ACR w tym artykule. Jeśli chcesz używać go lokalnie, wymagana jest wersja 2.0.76 lub nowsza. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install]. 

> [!IMPORTANT]
> Ta funkcja jest obecnie w wersji zapoznawczej. Wersje zapoznawcze są udostępniane pod warunkiem udzielenia zgody na [dodatkowe warunki użytkowania][terms-of-use]. Niektóre cechy funkcji mogą ulec zmianie, zanim stanie się ona ogólnie dostępna.

> [!WARNING]
> Użyj `acr purge` polecenia z przestrogą — usunięte dane obrazu są nieodwracalne. Jeśli masz systemy, które pobierają obrazy za pomocą skrótu manifestu (w przeciwieństwie do nazwy obrazu), nie należy czyścić obrazów nieoznakowanych. Usunięcie nieoznakowanych obrazów uniemożliwi tym systemom ściąganie obrazów z rejestru. Zamiast ściągania według manifestu należy rozważyć przyjęcie *unikatowego schematu znakowania* , [zalecane najlepsze rozwiązanie](container-registry-image-tag-version.md).

Jeśli chcesz usunąć Tagi pojedynczego obrazu lub manifesty za pomocą poleceń interfejsu wiersza polecenia platformy Azure, zobacz [usuwanie obrazów kontenerów w Azure Container Registry](container-registry-delete.md).

## <a name="use-the-purge-command"></a>Użyj przeczyszczania polecenia

`acr purge`Polecenie kontenera usuwa obrazy przez tag w repozytorium, które pasuje do filtru nazw i które są starsze niż określony czas trwania. Domyślnie tylko odwołania tagów są usuwane, a nie źródłowe [manifesty](container-registry-concepts.md#manifest) i dane warstwowe. Polecenie ma również opcję usuwania manifestów. 

> [!NOTE]
> `acr purge` nie usuwa tagu lub repozytorium obrazu, `write-enabled` w którym atrybut jest ustawiony na `false` . Aby uzyskać więcej informacji, zobacz [blokowanie obrazu kontenera w usłudze Azure Container Registry](container-registry-image-lock.md).

`acr purge` Program jest przeznaczony do uruchamiania jako polecenie kontenera w [zadaniu ACR](container-registry-tasks-overview.md), dzięki czemu jest automatycznie uwierzytelniany przy użyciu rejestru, w którym uruchamiane jest zadanie i wykonuje w nim akcje. Przykłady zadań w tym artykule używają `acr purge` [aliasu](container-registry-tasks-reference-yaml.md#aliases) polecenia zamiast w pełni kwalifikowanego obrazu kontenera polecenia.

Podczas uruchamiania należy określić co najmniej następujące elementy `acr purge` :

* `--filter` -Repozytorium i *wyrażenie regularne* służące do filtrowania tagów w repozytorium. Przykłady: `--filter "hello-world:.*"` dopasowuje wszystkie Tagi w `hello-world` repozytorium i `--filter "hello-world:^1.*"` pasuje do tagów zaczynających się od `1` . Przekaż wiele `--filter` parametrów, aby przeczyścić wiele repozytoriów.
* `--ago` - [Ciąg czasu trwania](https://golang.org/pkg/time/) , aby wskazać czas trwania, po którym obrazy są usuwane. Czas trwania składa się z sekwencji jednej lub więcej liczb dziesiętnych, z których każdy ma sufiks jednostki. Prawidłowe jednostki czasu obejmują "d" dla dni, "h" dla godzin i "m" przez minuty. Na przykład program `--ago 2d3h6m` wybiera wszystkie odfiltrowane obrazy ostatnio modyfikowane ponad 2 dni, 3 godziny i 6 minut temu, a następnie `--ago 1.5h` wybiera obrazy ostatnio zmodyfikowane powyżej 1,5 godzin temu.

`acr purge` obsługuje kilka opcjonalnych parametrów. Poniższe dwa są używane w przykładach w tym artykule:

* `--untagged` -Określa, że manifesty, które nie mają skojarzonych tagów ( *nieoznakowane manifesty* ) są usuwane.
* `--dry-run` -Określa, że żadne dane nie są usuwane, ale dane wyjściowe są takie same jak wtedy, gdy polecenie jest uruchamiane bez tej flagi. Ten parametr jest przydatny do testowania polecenia przeczyszczania, aby upewnić się, że nie powoduje niezamierzonego usuwania danych, które mają być zachowane.
* `--keep` -Określa, że jest zachowywana Najnowsza Liczba tagów do usunięcia.

Aby uzyskać dodatkowe parametry, uruchom polecenie `acr purge --help` . 

`acr purge` obsługuje inne funkcje poleceń ACR Tasks, w tym [uruchamiania zmiennych](container-registry-tasks-reference-yaml.md#run-variables) i [dzienników uruchamiania zadań](container-registry-tasks-logs.md) , które są przesyłane strumieniowo, a także zapisane do późniejszego pobrania.

### <a name="run-in-an-on-demand-task"></a>Uruchamianie w ramach zadania na żądanie

W poniższym przykładzie za pomocą polecenia [AZ ACR Run][az-acr-run] można uruchomić `acr purge` polecenie na żądanie. Ten przykład usuwa wszystkie znaczniki obrazu i manifesty w `hello-world` repozytorium w *rejestrze* , który został zmodyfikowany ponad 1 dzień temu. Polecenie kontenera jest przesyłane przy użyciu zmiennej środowiskowej. Zadanie jest uruchamiane bez kontekstu źródłowego.

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

W poniższym przykładzie za pomocą polecenia [AZ ACR Task Create][az-acr-task-create] zostanie utworzone codzienne [zaplanowane zadanie ACR](container-registry-tasks-scheduled.md). Zadanie Przeczyszcza Tagi zmodyfikowane ponad 7 dni temu w `hello-world` repozytorium. Polecenie kontenera jest przesyłane przy użyciu zmiennej środowiskowej. Zadanie jest uruchamiane bez kontekstu źródłowego.

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

Uruchom polecenie [AZ ACR Task show][az-acr-task-show] , aby zobaczyć, że wyzwalacz czasomierza został skonfigurowany.

### <a name="purge-large-numbers-of-tags-and-manifests"></a>Przeczyszczanie dużej liczby tagów i manifestów

Przeczyszczanie dużej liczby tagów i manifestów może potrwać kilka minut lub dłużej. Aby przeczyścić tysiące tagów i manifestów, polecenie może wymagać uruchomienia dłużej niż domyślny limit czasu wynoszący 600 sekund dla zadania na żądanie lub 3600 sekund dla zaplanowanego zadania. W przypadku przekroczenia limitu czasu zostanie usunięty tylko podzestaw tagów i manifestów. Aby upewnić się, że ukończono przeczyszczanie na dużą skalę, Przekaż `--timeout` parametr w celu zwiększenia wartości. 

Na przykład następujące zadanie na żądanie ustawia limit czasu 3600 sekund (1 godzina):

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

## <a name="example-scheduled-purge-of-multiple-repositories-in-a-registry"></a>Przykład: zaplanowane przeczyszczanie wielu repozytoriów w rejestrze

Ten przykład przeprowadzi Cię przez program `acr purge` , aby okresowo czyścić wiele repozytoriów w rejestrze. Na przykład może istnieć potok programistyczny, który wypchnięciuje obrazy do `samples/devimage1` i `samples/devimage2` repozytoriów. Należy okresowo importować obrazy programistyczne do repozytorium produkcyjnego dla wdrożeń, dzięki czemu nie są już potrzebne obrazy programistyczne. Co tydzień, można przeczyścić `samples/devimage1` `samples/devimage2` repozytoria i w trakcie przygotowania do pracy w nadchodzącym tygodniu.

### <a name="preview-the-purge"></a>Podgląd przeczyszczania

Przed usunięciem danych zalecamy uruchomienie zadania przeczyszczania na żądanie przy użyciu `--dry-run` parametru. Ta opcja umożliwia wyświetlenie tagów i manifestów, które polecenie zostanie przeczyszczone, bez usuwania jakichkolwiek danych. 

W poniższym przykładzie filtr w każdym repozytorium wybiera wszystkie Tagi. `--ago 0d`Parametr dopasowuje obrazy wszystkich okresów przedawnienia w repozytoriach, które pasują do filtrów. Zmodyfikuj kryteria wyboru zgodnie z potrzebami w danym scenariuszu. `--untagged`Parametr wskazuje, aby usunąć manifesty oprócz tagów. Polecenie kontenera jest przesyłane do polecenia [AZ ACR Run][az-acr-run] przy użyciu zmiennej środowiskowej.

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

Przejrzyj dane wyjściowe polecenia, aby zobaczyć Tagi i manifesty zgodne z parametrami wyboru. Ponieważ polecenie jest uruchamiane z `--dry-run` , żadne dane nie są usuwane.

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

### <a name="schedule-the-purge"></a>Zaplanuj przeczyszczanie

Po zweryfikowaniu przebiegu suchego utwórz zaplanowane zadanie, aby zautomatyzować przeczyszczanie. W poniższym przykładzie zaplanujesz cotygodniowe zadanie w niedzielę o godzinie 1:00 czasu UTC, aby uruchomić poprzednie polecenie przeczyszczania:

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

Uruchom polecenie [AZ ACR Task show][az-acr-task-show] , aby zobaczyć, że wyzwalacz czasomierza został skonfigurowany.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o innych opcjach [usuwania danych obrazu](container-registry-delete.md) w Azure Container Registry.

Aby uzyskać więcej informacji na temat magazynu obrazu, zobacz [kontener Image Storage w Azure Container Registry](container-registry-storage.md).

<!-- LINKS - External -->

[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show

