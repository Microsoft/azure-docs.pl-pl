---
title: Odwołanie YAML — zadania usługi ACR
description: Informacje dotyczące definiowania zadań w pliku YAML dla zadania usługi ACR, w tym właściwości zadań, typów kroków, właściwości kroków i zmiennych wbudowanych.
ms.topic: article
ms.date: 07/08/2020
ms.openlocfilehash: 126fcbce0569b2be6d9302cbbb718fa11e3e8046
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780951"
---
# <a name="acr-tasks-reference-yaml"></a>zadania usługi ACR referencyjne: YAML

Wieloetapowa definicja zadania w zadania usługi ACR zawiera skoncentrowany na kontenerach prymityw obliczeniowy skoncentrowany na budowania, testowania i poprawianiu kontenerów. W tym artykule o mowa o poleceniach, parametrach, właściwościach i składni plików YAML, które definiują zadania wieloetapowe.

Ten artykuł zawiera informacje dotyczące tworzenia wieloetapowych plików YAML zadań dla zadania usługi ACR. Jeśli chcesz mieć wprowadzenie do zadania usługi ACR, zobacz [zadania usługi ACR omówienie.](container-registry-tasks-overview.md)

## <a name="acr-taskyaml-file-format"></a>Format pliku acr-task.yaml

zadania usługi ACR obsługuje wieloetapową deklarację zadań w standardowej składni YAML. Kroki zadania definiuje się w pliku YAML. Następnie możesz uruchomić zadanie ręcznie, przekazując plik do [polecenia az acr run.][az-acr-run] Możesz też użyć pliku do utworzenia zadania za pomocą polecenia [az acr task create,][az-acr-task-create] które jest wyzwalane automatycznie po zatwierdzeniu git, aktualizacji obrazu podstawowego lub harmonogramie. Mimo że ten artykuł odwołuje się do pliku zawierającego kroki, zadania usługi ACR obsługuje dowolną prawidłową nazwę pliku `acr-task.yaml` z [obsługiwanym rozszerzeniem](#supported-task-filename-extensions).

Typy pierwotne `acr-task.yaml` najwyższego poziomu to właściwości **zadania,** **typy kroków** i **właściwości kroków**:

* [Właściwości zadania](#task-properties) dotyczą wszystkich kroków wykonywania zadania. Istnieje kilka globalnych właściwości zadania, w tym:
  * `version`
  * `stepTimeout`
  * `workingDirectory`
* [Typy kroków](#task-step-types) zadań reprezentują typy akcji, które mogą być wykonywane w zadaniu. Istnieją trzy typy kroków:
  * `build`
  * `push`
  * `cmd`
* [Właściwości kroku zadania](#task-step-properties) to parametry, które mają zastosowanie do poszczególnych kroków. Istnieje kilka właściwości kroków, w tym:
  * `startDelay`
  * `timeout`
  * `when`
  * ... i wiele innych.

Format podstawowy `acr-task.yaml` pliku, w tym niektóre typowe właściwości kroku, jest następujący. Chociaż nie jest to wyczerpująca reprezentacja wszystkich dostępnych właściwości kroków lub użycia typu kroku, zapewnia szybki przegląd podstawowego formatu pliku.

```yml
version: # acr-task.yaml format version.
stepTimeout: # Seconds each step may take.
steps: # A collection of image or container actions.
  - build: # Equivalent to "docker build," but in a multi-tenant environment
  - push: # Push a newly built or retagged image to a registry.
    when: # Step property that defines either parallel or dependent step execution.
  - cmd: # Executes a container, supports specifying an [ENTRYPOINT] and parameters.
    startDelay: # Step property that specifies the number of seconds to wait before starting execution.
```

### <a name="supported-task-filename-extensions"></a>Obsługiwane rozszerzenia nazw plików zadań

zadania usługi ACR zarezerwowano kilka rozszerzeń nazw plików, w tym `.yaml` , który będzie przetwarzany jako plik zadania. Każde  rozszerzenie, które nie znajduje się na poniższej liście, jest uznawane przez program zadania usługi ACR za plik Dockerfile: .yaml, .yml, .toml, .json, .sh, .bash, .zsh, .ps1, .ps, .cmd, .bat, .ts, .js, .php, .py, .rb, .lua

YAML to jedyny format pliku obecnie obsługiwany przez zadania usługi ACR. Inne rozszerzenia nazw plików są zarezerwowane do przyszłej obsługi.

## <a name="run-the-sample-tasks"></a>Uruchamianie przykładowych zadań

Istnieje kilka przykładowych plików zadań, do których odwołują się poniższe sekcje tego artykułu. Przykładowe zadania znajdują się w publicznym repozytorium GitHub [Azure-Samples/acr-tasks.][acr-tasks] Można je uruchomić za pomocą polecenia interfejsu wiersza polecenia platformy Azure [az acr run][az-acr-run]. Przykładowe polecenia są podobne do:

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

W przypadku formatowania przykładowych poleceń założono, że skonfigurowano rejestr domyślny w interfejsie wiersza polecenia platformy Azure, więc pominięto parametr `--registry` . Aby skonfigurować rejestr domyślny, użyj [polecenia az configure][az-configure] z `--defaults` parametrem , który akceptuje `acr=REGISTRY_NAME` wartość.

Aby na przykład skonfigurować interfejs wiersza polecenia platformy Azure z domyślnym rejestrem o nazwie "myregistry":

```azurecli
az configure --defaults acr=myregistry
```

## <a name="task-properties"></a>Właściwości zadania

Właściwości zadania są zwykle wyświetlane w górnej części pliku i są właściwościami globalnymi, które są stosowane podczas pełnego `acr-task.yaml` wykonywania kroków zadania. Niektóre z tych właściwości globalnych można przesłonić w ramach pojedynczego kroku.

| Właściwość | Typ | Opcjonalne | Opis | Obsługiwane zastępowanie | Wartość domyślna |
| -------- | ---- | -------- | ----------- | ------------------ | ------------- |
| `version` | ciąg | Tak | Wersja pliku analizowana przez usługę `acr-task.yaml` zadania usługi ACR service. Chociaż zadania usługi ACR stara się zachować zgodność z poprzednimi wersjami, ta wartość zadania usługi ACR zachowanie zgodności w ramach zdefiniowanej wersji. Jeśli nie zostanie to określone, zostanie domyślnie włączona najnowsza wersja. | Nie | Brak |
| `stepTimeout` | int (sekundy) | Tak | Maksymalna liczba sekund, przez które można uruchomić krok. Jeśli właściwość jest określona w zadaniu, ustawia właściwość `timeout` domyślną wszystkich kroków. Jeśli właściwość `timeout` jest określona w kroku, zastępuje właściwość podaną przez zadanie. | Tak | 600 (10 minut) |
| `workingDirectory` | ciąg | Tak | Katalog roboczy kontenera w czasie wykonywania. Jeśli właściwość jest określona w zadaniu, ustawia właściwość `workingDirectory` domyślną wszystkich kroków. Jeśli jest określony w kroku, zastępuje właściwość podaną przez zadanie. | Tak | `c:\workspace` w systemie Windows lub `/workspace` Linux |
| `env` | [string, string, ...] | Tak |  Tablica ciągów w `key=value` formacie definiującym zmienne środowiskowe dla zadania. Jeśli właściwość jest określona w zadaniu, ustawia właściwość `env` domyślną wszystkich kroków. Jeśli zostanie określony w kroku, zastąpi wszystkie zmienne środowiskowe odziedziczone po zadaniu. | Tak | Brak |
| `secrets` | [tajne, tajne, ...] | Tak | Tablica [obiektów tajnych.](#secret) | Nie | Brak |
| `networks` | [network, network, ...] | Tak | Tablica [obiektów](#network) sieciowych. | Nie | Brak |
| `volumes` | [wolumin, wolumin, ...] | Tak | Tablica [obiektów woluminów.](#volume) Określa woluminy z zawartością źródłową do instalacji w kroku. | Nie | Brak |

### <a name="secret"></a>wpis tajny

Obiekt tajny ma następujące właściwości.

| Właściwość | Typ | Opcjonalne | Opis | Wartość domyślna |
| -------- | ---- | -------- | ----------- | ------- |
| `id` | ciąg | Nie | Identyfikator klucza tajnego. | Brak |
| `keyvault` | ciąg | Tak | Adres URL Azure Key Vault tajnego. | Brak |
| `clientID` | ciąg | Tak | Identyfikator klienta tożsamości zarządzanej [przypisanej](container-registry-tasks-authentication-managed-identity.md) przez użytkownika dla zasobów platformy Azure. | Brak |

### <a name="network"></a>network

Obiekt sieci ma następujące właściwości.

| Właściwość | Typ | Opcjonalne | Opis | Wartość domyślna |
| -------- | ---- | -------- | ----------- | ------- | 
| `name` | ciąg | Nie | Nazwa sieci. | Brak |
| `driver` | ciąg | Tak | Sterownik do zarządzania siecią. | Brak |
| `ipv6` | bool | Tak | Określa, czy sieć IPv6 jest włączona. | `false` |
| `skipCreation` | bool | Tak | Czy pominąć tworzenie sieci. | `false` |
| `isDefault` | bool | Tak | Określa, czy sieć jest siecią domyślną dostarczaną z Azure Container Registry. | `false` |

### <a name="volume"></a>wolumin

Obiekt woluminu ma następujące właściwości.

| Właściwość | Typ | Opcjonalne | Opis | Wartość domyślna |
| -------- | ---- | -------- | ----------- | ------- | 
| `name` | ciąg | Nie | Nazwa woluminu do instalacji. Może zawierać tylko znaki alfanumeryczne, "-" i "_". | Brak |
| `secret` | map[string]string | Nie | Każdy klucz mapy jest nazwą pliku utworzonego i wypełnionego w woluminie. Każda wartość jest wersją ciągu tajnego. Wartości w kluczu tajnym muszą być zakodowane w formacie Base64. | Brak |

## <a name="task-step-types"></a>Typy kroków zadań

zadania usługi ACR obsługuje trzy typy kroków. Każdy typ kroku obsługuje kilka właściwości, szczegółowo opisane w sekcji dla każdego typu kroku.

| Typ kroku | Opis |
| --------- | ----------- |
| [`build`](#build) | Tworzy obraz kontenera przy użyciu znanej `docker build` składni. |
| [`push`](#push) | Wykonuje obiekt z `docker push` nowo utworzonymi lub ponownie oznaczonymi obrazami w rejestrze kontenerów. Azure Container Registry, inne rejestry prywatne i publiczne Docker Hub są obsługiwane. |
| [`cmd`](#cmd) | Uruchamia kontener jako polecenie z parametrami przekazywanymi do kontenera `[ENTRYPOINT]` . Typ kroku obsługuje parametry takie jak , i inne znane opcje poleceń, co umożliwia testowanie jednostek i funkcji `cmd` `env` przy jednoczesnym `detach` `docker run` wykonywaniu kontenera. |

## <a name="build"></a>kompilacja

Skompilowanie obrazu kontenera. Typ kroku reprezentuje wielodostępny, bezpieczny sposób działania w `build` `docker build` chmurze jako prymityw pierwszej klasy.

### <a name="syntax-build"></a>Składnia: kompilacja

```yml
version: v1.1.0
steps:
  - [build]: -t [imageName]:[tag] -f [Dockerfile] [context]
    [property]: [value]
```

Typ `build` kroku obsługuje parametry z poniższej tabeli. Typ kroku obsługuje również wszystkie opcje kompilacji polecenia `build` [docker build,](https://docs.docker.com/engine/reference/commandline/build/) takie jak ustawianie zmiennych `--build-arg` czasu kompilacji.

| Parametr | Opis | Opcjonalne |
| --------- | ----------- | :-------: |
| `-t` &#124; `--image` | Definiuje w pełni `image:tag` kwalifikowany sbudowaną obraz.<br /><br />Ponieważ obrazy mogą być używane do wewnętrznych weryfikacji zadań, takich jak testy funkcjonalne, nie wszystkie obrazy wymagają `push` rejestru. Jednak aby można było wystąpienie obrazu w ramach wykonania zadania, obraz potrzebuje nazwy, do których ma się odwoływać.<br /><br />W `az acr build` przeciwieństwie do zadania usługi ACR nie zapewnia domyślnego zachowania wypychania. W zadania usługi ACR domyślny scenariusz zakłada możliwość kompilowania, weryfikowania, a następnie wypychania obrazu. Zobacz [wypychanie,](#push) aby dowiedzieć się, jak opcjonalnie wypychać wbudowane obrazy. | Tak |
| `-f` &#124; `--file` | Określa plik Dockerfile przekazany do `docker build` pliku . Jeśli nie zostanie określony, przyjmowany jest domyślny plik Dockerfile w katalogu głównym kontekstu. Aby określić plik Dockerfile, przekaż nazwę pliku względem katalogu głównego kontekstu. | Tak |
| `context` | Katalog główny przekazany do katalogu `docker build` . Katalog główny każdego zadania jest ustawiony na udostępniony katalog [workingDirectory](#task-step-properties)i zawiera katalog główny skojarzonego sklonowanego katalogu Git. | Nie |

### <a name="properties-build"></a>Właściwości: kompilacja

Typ `build` kroku obsługuje następujące właściwości. Szczegółowe informacje o tych właściwościach można znaleźć w sekcji [Właściwości](#task-step-properties) kroku zadania w tym artykule.

| Właściwości | Typ | Wymagane |
| -------- | ---- | -------- |
| `detach` | bool | Opcjonalne |
| `disableWorkingDirectoryOverride` | bool | Opcjonalne |
| `entryPoint` | ciąg | Opcjonalne |
| `env` | [string, string, ...] | Opcjonalne |
| `expose` | [string, string, ...] | Opcjonalne |
| `id` | ciąg | Opcjonalne |
| `ignoreErrors` | bool | Opcjonalne |
| `isolation` | ciąg | Opcjonalne |
| `keep` | bool | Opcjonalne |
| `network` | object | Opcjonalne |
| `ports` | [string, string, ...] | Opcjonalne |
| `pull` | bool | Opcjonalne |
| `repeat` | int | Opcjonalne |
| `retries` | int | Opcjonalne |
| `retryDelay` | int (sekundy) | Opcjonalne |
| `secret` | object | Opcjonalne |
| `startDelay` | int (sekundy) | Opcjonalne |
| `timeout` | int (sekundy) | Opcjonalne |
| `volumeMount` | object | Opcjonalne |
| `when` | [string, string, ...] | Opcjonalne |
| `workingDirectory` | ciąg | Opcjonalne |

### <a name="examples-build"></a>Przykłady: kompilacja

#### <a name="build-image---context-in-root"></a>Obraz kompilacji — kontekst w katalogu głównym

```azurecli
az acr run -f build-hello-world.yaml https://github.com/AzureCR/acr-tasks-sample.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-hello-world.yaml)]

#### <a name="build-image---context-in-subdirectory"></a>Obraz kompilacji — kontekst w podkatalogu

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world -f hello-world.dockerfile ./subDirectory
```

## <a name="push"></a>push

Wypchnąć co najmniej jeden sbudowany lub ponownie oznaczony obraz do rejestru kontenerów. Obsługuje wypychanie do prywatnych rejestrów, takich Azure Container Registry, lub do publicznych Docker Hub.

### <a name="syntax-push"></a>Składnia: wypychanie

Typ `push` kroku obsługuje kolekcję obrazów. Składnia kolekcji YAML obsługuje formaty wbudowane i zagnieżdżone. Wypychanie pojedynczego obrazu jest zwykle reprezentowane przy użyciu składni wbudowanej:

```yml
version: v1.1.0
steps:
  # Inline YAML collection syntax
  - push: ["$Registry/hello-world:$ID"]
```

Aby zwiększyć czytelność, użyj zagnieżdżonych składni podczas wypychania wielu obrazów:

```yml
version: v1.1.0
steps:
  # Nested YAML collection syntax
  - push:
    - $Registry/hello-world:$ID
    - $Registry/hello-world:latest
```

### <a name="properties-push"></a>Właściwości: wypychanie

Typ `push` kroku obsługuje następujące właściwości. Szczegółowe informacje o tych właściwościach można znaleźć w sekcji [Właściwości](#task-step-properties) kroku zadania w tym artykule.

| Właściwość | Typ | Wymagane |
| -------- | ---- | -------- |
| `env` | [string, string, ...] | Opcjonalne |
| `id` | ciąg | Opcjonalne |
| `ignoreErrors` | bool | Opcjonalne |
| `startDelay` | int (sekundy) | Opcjonalne |
| `timeout` | int (sekundy) | Opcjonalne |
| `when` | [string, string, ...] | Opcjonalne |

### <a name="examples-push"></a>Przykłady: wypychanie

#### <a name="push-multiple-images"></a>Wypychanie wielu obrazów

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-push-hello-world.yaml)]

#### <a name="build-push-and-run"></a>Kompilowanie, wypychanie i uruchamianie

```azurecli
az acr run -f build-run-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-run-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-run-hello-world.yaml)]

## <a name="cmd"></a>cmd

Typ `cmd` kroku uruchamia kontener.

### <a name="syntax-cmd"></a>Składnia: cmd

```yml
version: v1.1.0
steps:
  - [cmd]: [containerImage]:[tag (optional)] [cmdParameters to the image]
```

### <a name="properties-cmd"></a>Właściwości: cmd

Typ `cmd` kroku obsługuje następujące właściwości:

| Właściwość | Typ | Wymagane |
| -------- | ---- | -------- |
| `detach` | bool | Opcjonalne |
| `disableWorkingDirectoryOverride` | bool | Opcjonalne |
| `entryPoint` | ciąg | Opcjonalne |
| `env` | [string, string, ...] | Opcjonalne |
| `expose` | [string, string, ...] | Opcjonalne |
| `id` | ciąg | Opcjonalne |
| `ignoreErrors` | bool | Opcjonalne |
| `isolation` | ciąg | Opcjonalne |
| `keep` | bool | Opcjonalne |
| `network` | object | Opcjonalne |
| `ports` | [string, string, ...] | Opcjonalne |
| `pull` | bool | Opcjonalne |
| `repeat` | int | Opcjonalne |
| `retries` | int | Opcjonalne |
| `retryDelay` | int (sekundy) | Opcjonalne |
| `secret` | object | Opcjonalne |
| `startDelay` | int (sekundy) | Opcjonalne |
| `timeout` | int (sekundy) | Opcjonalne |
| `volumeMount` | object | Opcjonalne |
| `when` | [string, string, ...] | Opcjonalne |
| `workingDirectory` | ciąg | Opcjonalne |

Szczegółowe informacje o tych właściwościach można znaleźć w sekcji [Właściwości kroku](#task-step-properties) zadania w tym artykule.

### <a name="examples-cmd"></a>Przykłady: cmd

#### <a name="run-hello-world-image"></a>Uruchamianie obrazu hello-world

To polecenie wykonuje plik zadania, który odwołuje się do `hello-world.yaml` [obrazu hello-world](https://hub.docker.com/_/hello-world/) na Docker Hub.

```azurecli
az acr run -f hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.yaml -->
[!code-yml[task](~/acr-tasks/hello-world.yaml)]

#### <a name="run-bash-image-and-echo-hello-world"></a>Uruchamianie obrazu powłoki Bash i echo "hello world"

To polecenie wykonuje plik zadania, który odwołuje się do `bash-echo.yaml` obrazu powłoki [bash](https://hub.docker.com/_/bash/) na Docker Hub.

```azurecli
az acr run -f bash-echo.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo.yaml)]

#### <a name="run-specific-bash-image-tag"></a>Uruchamianie określonego tagu obrazu powłoki Bash

Aby uruchomić określoną wersję obrazu, określ tag w pliku `cmd` .

To polecenie wykonuje plik zadania, który odwołuje się do obrazu `bash-echo-3.yaml` [bash:3.0](https://hub.docker.com/_/bash/) na Docker Hub.

```azurecli
az acr run -f bash-echo-3.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo-3.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo-3.yaml)]

#### <a name="run-custom-images"></a>Uruchamianie obrazów niestandardowych

Typ `cmd` kroku odwołuje się do obrazów w standardowym `docker run` formacie. Zakłada się, że obrazy, które nie są poprzedzone rejestrem, pochodzą z docker.io. Poprzedni przykład może być reprezentowany w równym stopniu jako:

```yml
version: v1.1.0
steps:
  - cmd: docker.io/bash:3.0 echo hello world
```

Przy użyciu standardowej `docker run` konwencji referencyjnej obrazów program może uruchamiać obrazy z dowolnego rejestru prywatnego lub `cmd` publicznego Docker Hub. Jeśli odwołujesz się do obrazów w tym samym rejestrze, w którym jest wykonywane zadanie ACR, nie musisz określać żadnych poświadczeń rejestru.

* Uruchom obraz z rejestru kontenerów platformy Azure. W poniższym przykładzie przyjęto założenie, że masz rejestr o nazwie `myregistry` i obraz niestandardowy `myimage:mytag` .

    ```yml
    version: v1.1.0
    steps:
        - cmd: myregistry.azurecr.io/myimage:mytag
    ```

* Uogólnij odwołanie do rejestru za pomocą zmiennej lub aliasu uruchamiania

    Zamiast kodować natłok nazwy rejestru w pliku, można uczynić go bardziej przenośnym przy użyciu zmiennej `acr-task.yaml` [run lub](#run-variables) [aliasu](#aliases). Zmienna lub alias rozwija się w czasie wykonywania do nazwy rejestru, w którym `Run.Registry` `$Registry` jest wykonywane zadanie.

    Aby na przykład uogólnić poprzednie zadanie tak, aby działało w dowolnym rejestrze kontenerów platformy Azure, należy odwołać się do $Registry zmiennej w nazwie obrazu:

    ```yml
    version: v1.1.0
    steps:
      - cmd: $Registry/myimage:mytag
    ```

#### <a name="access-secret-volumes"></a>Uzyskiwanie dostępu do woluminów tajnych

Właściwość umożliwia określić woluminy i ich zawartość w kluczu tajnym `volumes` dla kroków i w `build` `cmd` zadaniu. W każdym kroku opcjonalna właściwość zawiera listę woluminów i odpowiednich ścieżek kontenera do instalacji `volumeMounts` w kontenerze w tym kroku. Wpisy tajne są dostarczane jako pliki w ścieżce instalacji każdego woluminu.

Wykonaj zadanie i zainstaluj dwa wpisy tajne w kroku: jeden przechowywany w magazynie kluczy i jeden określony w wierszu polecenia:

```azurecli
az acr run -f mounts-secrets.yaml --set-secret mysecret=abcdefg123456 https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/mounts-secrets.yaml -->
[!code-yml[task](~/acr-tasks/mounts-secrets.yaml)]

## <a name="task-step-properties"></a>Właściwości kroku zadania

Każdy typ kroku obsługuje kilka właściwości odpowiednich dla jego typu. W poniższej tabeli zdefiniowano wszystkie dostępne właściwości kroku. Nie wszystkie typy kroków obsługują wszystkie właściwości. Aby sprawdzić, które z tych właściwości są dostępne dla każdego [](#push) typu kroku, zobacz sekcje referencyjne dotyczące typu [kroku cmd,](#cmd) [kompilacji](#build)i wypychania.

| Właściwość | Typ | Opcjonalne | Opis | Wartość domyślna |
| -------- | ---- | -------- | ----------- | ------- |
| `detach` | bool | Tak | Określa, czy należy odłączyć kontener podczas działania. | `false` |
| `disableWorkingDirectoryOverride` | bool | Tak | Czy wyłączyć funkcję `workingDirectory` zastępowania. Użyj tego w połączeniu z , `workingDirectory` aby mieć pełną kontrolę nad katalogiem pracy kontenera. | `false` |
| `entryPoint` | ciąg | Tak | Zastępuje właściwości `[ENTRYPOINT]` kontenera kroku. | Brak |
| `env` | [string, string, ...] | Tak | Tablica ciągów w `key=value` formacie definiująca zmienne środowiskowe dla kroku. | Brak |
| `expose` | [string, string, ...] | Tak | Tablica portów, które są widoczne z kontenera. |  Brak |
| [`id`](#example-id) | ciąg | Tak | Unikatowo identyfikuje krok w ramach zadania. Inne kroki zadania mogą odwoływać się do kroku , na przykład w przypadku sprawdzania `id` zależności za pomocą . `when`<br /><br />Jest `id` to również nazwa uruchomionego kontenera. Procesy uruchomione w innych kontenerach w zadaniu mogą odwoływać się do nazwy hosta DNS lub na przykład do uzyskiwania do niego dostępu za pomocą dzienników platformy `id` Docker [id]. | `acb_step_%d`, `%d` gdzie jest indeksem opartym na 0 kroku od góry do dołu w pliku YAML |
| `ignoreErrors` | bool | Tak | Czy oznaczyć krok jako pomyślny niezależnie od tego, czy wystąpił błąd podczas wykonywania kontenera. | `false` |
| `isolation` | ciąg | Tak | Poziom izolacji kontenera. | `default` |
| `keep` | bool | Tak | Określa, czy kontener kroku powinien być przechowywany po wykonaniu. | `false` |
| `network` | object | Tak | Identyfikuje sieć, w której działa kontener. | Brak |
| `ports` | [string, string, ...] | Tak | Tablica portów publikowanych z kontenera na hoście. |  Brak |
| `pull` | bool | Tak | Określa, czy należy wymusić ściągnięcie kontenera przed jego wykonaniem, aby zapobiec wszelkim zachowaniom buforowania. | `false` |
| `privileged` | bool | Tak | Określa, czy należy uruchomić kontener w trybie uprzywilejowanym. | `false` |
| `repeat` | int | Tak | Liczba ponownych prób powtórzenia wykonania kontenera. | 0 |
| `retries` | int | Tak | Liczba ponownych prób w przypadku niepowodzenia wykonania kontenera. Próba ponowienia próby jest podejmowana tylko wtedy, gdy kod zakończenia kontenera jest niezerowy. | 0 |
| `retryDelay` | int (sekundy) | Tak | Opóźnienie w sekundach między ponowną próbą wykonania kontenera. | 0 |
| `secret` | object | Tak | Identyfikuje klucz tajny Azure Key Vault tożsamości [zarządzanej dla zasobów platformy Azure.](container-registry-tasks-authentication-managed-identity.md) | Brak |
| `startDelay` | int (sekundy) | Tak | Liczba sekund opóźnienia wykonywania kontenera. | 0 |
| `timeout` | int (sekundy) | Tak | Maksymalna liczba sekund, przez które krok może zostać wykonany przed zakończeniem. | 600 |
| [`when`](#example-when) | [string, string, ...] | Tak | Konfiguruje zależność kroku od co najmniej jednego kroku w ramach zadania. | Brak |
| `user` | ciąg | Tak | Nazwa użytkownika lub identyfikator UID kontenera | Brak |
| `workingDirectory` | ciąg | Tak | Ustawia katalog roboczy dla kroku. Domyślnie program zadania usługi ACR katalog główny jako katalog roboczy. Jeśli jednak kompilacja ma kilka kroków, wcześniejsze kroki mogą udostępniać artefakty w kolejnych krokach, określając ten sam katalog roboczy. | `c:\workspace` w systemie Windows lub `/workspace` Linux |

### <a name="volumemount"></a>volumeMount

Obiekt volumeMount ma następujące właściwości.

| Właściwość | Typ | Opcjonalne | Opis | Wartość domyślna |
| -------- | ---- | -------- | ----------- | ------- | 
| `name` | ciąg | Nie | Nazwa woluminu do instalacji. Musi dokładnie odpowiadać nazwie `volumes` właściwości. | Brak |
| `mountPath`   | ciąg | nie | Ścieżka bezwzględna do instalacji plików w kontenerze.  | Brak |

### <a name="examples-task-step-properties"></a>Przykłady: Właściwości kroku zadania

#### <a name="example-id"></a>Przykład: id

Skompilować dwa obrazy, aby utworzyć funkcjonalny obraz testowy. Każdy krok jest identyfikowany przez unikatowy, który inne kroki odwołania do zadania `id` w jego `when` właściwości.

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

#### <a name="example-when"></a>Przykład: kiedy

Właściwość `when` określa zależność kroku od innych kroków w ramach zadania. Obsługuje ona dwie wartości parametrów:

* `when: ["-"]` - Wskazuje brak zależności od innych kroków. Krok określający rozpoczyna `when: ["-"]` wykonywanie natychmiast i umożliwia równoczesne wykonywanie kroków.
* `when: ["id1", "id2"]` - Wskazuje, że krok jest zależny od kroków z `id` "id1" i `id` "id2". Ten krok nie zostanie wykonany, dopóki nie zostaną wykonane zarówno kroki "id1", jak i "id2".

Jeśli nie zostanie określony w kroku, ten krok zależy od ukończenia `when` poprzedniego kroku w `acr-task.yaml` pliku.

Wykonywanie sekwencyjne kroków bez `when` :

```azurecli
az acr run -f when-sequential-default.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-default.yaml -->
[!code-yml[task](~/acr-tasks/when-sequential-default.yaml)]

Wykonywanie sekwencyjnego kroku za pomocą `when` :

```azurecli
az acr run -f when-sequential-id.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-id.yaml -->
[!code-yml[task](~/acr-tasks/when-sequential-id.yaml)]

Kompilowanie obrazów równoległych:

```azurecli
az acr run -f when-parallel.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel.yaml)]

Równoległa kompilacja obrazu i testowanie zależne:

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

## <a name="run-variables"></a>Uruchamianie zmiennych

zadania usługi ACR zawiera domyślny zestaw zmiennych, które są dostępne dla kroków zadania podczas ich wykonywania. Dostęp do tych zmiennych można uzyskać przy użyciu formatu `{{.Run.VariableName}}` , gdzie jest jedną z następujących `VariableName` wartości:

* `Run.ID`
* `Run.SharedVolume`
* `Run.Registry`
* `Run.RegistryName`
* `Run.Date`
* `Run.OS`
* `Run.Architecture`
* `Run.Commit`
* `Run.Branch`
* `Run.TaskName`

Nazwy zmiennych zazwyczaj nie wymagają wyjaśnień. Poniżej znajdziesz szczegółowe informacje dotyczące często używanych zmiennych. Od wersji YAML można używać skróconego, wstępnie zdefiniowanego `v1.1.0` [aliasu zadania w](#aliases) miejsce większości zmiennych uruchamiania. Na przykład, w miejsce `{{.Run.Registry}}` , użyj `$Registry` aliasu .

### <a name="runid"></a>Run.ID

Każde wykonywanie zadań utworzonych za pośrednictwem wyzwalacza lub uruchom za pośrednictwem programu `az acr run` `az acr task create` ma unikatowy identyfikator. Identyfikator reprezentuje aktualnie wykonywane uruchomienie.

Zwykle używany do unikatowego tagowania obrazu:

```yml
version: v1.1.0
steps:
    - build: -t $Registry/hello-world:$ID .
```

### <a name="runsharedvolume"></a>Run.SharedVolume

Unikatowy identyfikator udostępnionego woluminu, który jest dostępny dla wszystkich kroków zadania. Wolumin jest zainstalowany w systemie `c:\workspace` Windows lub `/workspace` Linux. 

### <a name="runregistry"></a>Run.Registry

W pełni kwalifikowana nazwa serwera rejestru. Zwykle używany do ogólnego odwołania do rejestru, w którym jest uruchamiane zadanie.

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID .
```

### <a name="runregistryname"></a>Run.RegistryName

Nazwa rejestru kontenerów. Zwykle używane w krokach zadań, które nie wymagają w pełni kwalifikowanej nazwy serwera, na przykład kroków, które uruchamiają polecenia interfejsu wiersza polecenia platformy `cmd` Azure w rejestrach.

```yml
version 1.1.0
steps:
# List repositories in registry
- cmd: az login --identity
- cmd: az acr repository list --name $RegistryName
```

### <a name="rundate"></a>Run.Date

Bieżąca godzina UTC uruchomienia.

### <a name="runcommit"></a>Run.Commit

W przypadku zadania wyzwalane przez zatwierdzenie w repozytorium GitHub identyfikator zatwierdzenia.

### <a name="runbranch"></a>Run.Branch

W przypadku zadania wyzwalane przez zatwierdzenie w repozytorium GitHub należy nazwa gałęzi.

## <a name="aliases"></a>Aliasy

W `v1.1.0` programie zadania usługi ACR aliasy, które są dostępne dla kroków zadań podczas ich wykonywania. Aliasy są podobne do aliasów (skrótów poleceń) obsługiwanych w powłoce bash i niektórych innych powłokach poleceń. 

Za pomocą aliasu można uruchomić dowolne polecenie lub grupę poleceń (w tym opcje i nazwy plików), wprowadzając jeden wyraz.

zadania usługi ACR obsługuje kilka wstępnie zdefiniowanych aliasów, a także niestandardowe aliasy, które tworzysz.

### <a name="predefined-aliases"></a>Wstępnie zdefiniowane aliasy

Następujące aliasy zadań są dostępne do użycia w miejsce [zmiennych uruchamiania](#run-variables):

| Alias | Uruchamianie zmiennej |
| ----- | ------------ |
| `ID` | `Run.ID` |
| `SharedVolume` | `Run.SharedVolume` |
| `Registry` | `Run.Registry` |
| `RegistryName` | `Run.RegistryName` |
| `Date` | `Run.Date` |
| `OS` | `Run.OS` |
| `Architecture` | `Run.Architecture` |
| `Commit` | `Run.Commit` |
| `Branch` | `Run.Branch` |

W krokach zadania poprzedzisz alias `$` dyrektywą , jak w tym przykładzie:

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID -f hello-world.dockerfile .
```

### <a name="image-aliases"></a>Aliasy obrazów

Każdy z poniższych aliasów wskazuje stabilny obraz w Microsoft Container Registry (MCR). Można odwoływać się do każdego z nich w `cmd` sekcji pliku zadania bez użycia dyrektywy .

| Alias | Obraz |
| ----- | ----- |
| `acr` | `mcr.microsoft.com/acr/acr-cli:0.1` |
| `az` | `mcr.microsoft.com/acr/azure-cli:a80af84` |
| `bash` | `mcr.microsoft.com/acr/bash:a80af84` |
| `curl` | `mcr.microsoft.com/acr/curl:a80af84` |

Następujące przykładowe zadanie używa [](container-registry-auto-purge.md) kilku aliasów do przeczyszczania tagów obrazów starszych niż 7 dni w repo `samples/hello-world` w rejestrze uruchamiania:

```yml
version: v1.1.0
steps:
  - cmd: acr tag list --registry $RegistryName --repository samples/hello-world
  - cmd: acr purge --registry $RegistryName --filter samples/hello-world:.* --ago 7d
```

### <a name="custom-alias"></a>Alias niestandardowy

Zdefiniuj alias niestandardowy w pliku YAML i użyj go, jak pokazano w poniższym przykładzie. Alias może zawierać tylko znaki alfanumeryczne. Domyślną dyrektywą w celu rozwinięcia aliasu jest `$` znak .

```yml
version: v1.1.0
alias:
  values:
    repo: myrepo
steps:
  - build: -t $Registry/$repo/hello-world:$ID -f Dockerfile .
```

Możesz utworzyć link do zdalnego lub lokalnego pliku YAML, aby uzyskać niestandardowe definicje aliasów. Poniższy przykład łączy się z plikiem YAML w usłudze Azure Blob Storage:

```yml
version: v1.1.0
alias:
  src:  # link to local or remote custom alias files
    - 'https://link/to/blob/remoteAliases.yml?readSasToken'
[...]
```

## <a name="next-steps"></a>Następne kroki

Omówienie zadań wieloetapowych zawiera temat [Run multi-step build, test, and patch tasks in zadania usługi ACR](container-registry-tasks-multi-step.md)(Uruchamianie wieloetapowych zadań kompilacji, testowania i poprawek w programie zadania usługi ACR ).

W przypadku kompilacji jednoetapowych zobacz [omówienie zadania usługi ACR .](container-registry-tasks-overview.md)



<!-- IMAGES -->

<!-- LINKS - External -->
[acr-tasks]: https://github.com/Azure-Samples/acr-tasks

<!-- LINKS - Internal -->
[az-acr-run]: /cli/azure/acr#az_acr_run
[az-acr-task-create]: /cli/azure/acr/task#az_acr_task_create
[az-configure]: /cli/azure/reference-index#az_configure
