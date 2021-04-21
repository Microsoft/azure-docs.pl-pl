---
title: Wieloetapowe zadanie kompilacji, testowania & obrazu poprawki
description: Wprowadzenie do zadań wieloetapowych, funkcji usługi zadania usługi ACR w usłudze Azure Container Registry, która zapewnia oparte na zadaniach przepływy pracy służące do tworzenia, testowania i poprawiania obrazów kontenerów w chmurze.
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: d57044fa8a0db7d661eb50284b34a6bbec9a1879
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781023"
---
# <a name="run-multi-step-build-test-and-patch-tasks-in-acr-tasks"></a>Uruchamianie wieloetapowych zadań kompilacji, testowania i poprawek w zadania usługi ACR

Zadania wieloetapowe rozszerzają możliwość kompilowania i wypychania pojedynczego obrazu zadania usługi ACR wieloetapowych przepływów pracy opartych na wielu kontenerach. Zadania wieloetapowe mogą kompilować i wypychać kilka obrazów serii lub równolegle. Następnie uruchom te obrazy jako polecenia w ramach jednego uruchomienia zadania. Każdy krok definiuje operację kompilowania lub wypychania obrazu kontenera, a także może definiować wykonywanie kontenera. Każdy krok w zadaniu wieloetapowym używa kontenera jako środowiska wykonawczego.

> [!IMPORTANT]
> Jeśli wcześniej utworzono zadania za pomocą wersji zapoznawczej przy użyciu polecenia `az acr build-task`, trzeba je utworzyć ponownie przy użyciu polecenia [az acr task][az-acr-task].

Na przykład można uruchomić zadanie z krokami automatyzują następującą logikę:

1. Tworzenie obrazu aplikacji internetowej
1. Uruchamianie kontenera aplikacji internetowej
1. Tworzenie obrazu testowego aplikacji internetowej
1. Uruchamianie kontenera testowego aplikacji internetowej, który wykonuje testy względem uruchomionego kontenera aplikacji
1. Jeśli testy przebiegną, skompilowanie pakietu archiwum helm chart
1. Wykonywanie operacji `helm upgrade` przy użyciu nowego pakietu archiwum helm chart

Wszystkie kroki są wykonywane na platformie Azure, odciążając pracę do zasobów obliczeniowych platformy Azure i odciążając zarządzanie infrastrukturą. Oprócz rejestru kontenerów platformy Azure płacisz tylko za zasoby, których używasz. Aby uzyskać informacje o cenach, zobacz **sekcję Container Build** w [te Azure Container Registry cen][pricing].


## <a name="common-task-scenarios"></a>Typowe scenariusze zadań

Zadania wieloetapowe umożliwiają realizację scenariuszy, takich jak następująca logika:

* Kompilowanie, tagowanie i wypychanie co najmniej jednego obrazu kontenera w serii lub równolegle.
* Uruchamianie i przechwytywanie wyników testów jednostkowych i pokrycia kodu.
* Uruchamianie i przechwytywanie testów funkcjonalnych. zadania usługi ACR obsługuje uruchamianie więcej niż jednego kontenera, wykonując serię żądań między nimi.
* Wykonywanie oparte na zadaniach, w tym kroki przed/po kompilacji obrazu kontenera.
* Wd wdrażaj co najmniej jeden kontener przy użyciu ulubionego aparatu wdrażania w środowisku docelowym.

## <a name="multi-step-task-definition"></a>Definicja zadania wieloetapowego

Wieloetapowe zadanie w zadania usługi ACR jest definiowane jako seria kroków w pliku YAML. Każdy krok może określać zależności od pomyślnego ukończenia co najmniej jednego poprzedniego kroku. Dostępne są następujące typy kroków zadań:

* [`build`](container-registry-tasks-reference-yaml.md#build): skompilowanie co najmniej jednego obrazu kontenera przy użyciu znanej `docker build` składni, w serii lub równolegle.
* [`push`](container-registry-tasks-reference-yaml.md#push): wypychanie sbudowaną obrazów do rejestru kontenerów. Rejestry prywatne, takie Azure Container Registry, są obsługiwane, tak jak rejestry Docker Hub.
* [`cmd`](container-registry-tasks-reference-yaml.md#cmd): uruchamianie kontenera w taki sposób, aby działał jako funkcja w kontekście uruchomionego zadania. Parametry można przekazać do kontenera i określić właściwości, takie jak `[ENTRYPOINT]` env, detach i inne znane `docker run` parametry. Typ `cmd` kroku umożliwia testowanie jednostek i funkcji z równoczesnym wykonywaniem kontenera.

Poniższe fragmenty kodu pokazują, jak połączyć te typy kroków zadań. Zadania wieloetapowe mogą być proste— wystarczy utworzyć pojedynczy obraz z pliku Dockerfile i wypchnąć go do rejestru przy użyciu pliku YAML podobnego do:

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID .
  - push: ["$Registry/hello-world:$ID"]
```

Lub bardziej złożone, takie jak ta fikcyjna definicja wieloetapowa, która obejmuje kroki kompilacji, testowania, pakietu Helm i wdrażania helm (nie jest wyświetlana konfiguracja rejestru kontenerów i repozytorium Helm):

```yml
version: v1.1.0
steps:
  - id: build-web
    build: -t $Registry/hello-world:$ID .
    when: ["-"]
  - id: build-tests
    build: -t $Registry/hello-world-tests ./funcTests
    when: ["-"]
  - id: push
    push: ["$Registry/helloworld:$ID"]
    when: ["build-web", "build-tests"]
  - id: hello-world-web
    cmd: $Registry/helloworld:$ID
  - id: funcTests
    cmd: $Registry/helloworld:$ID
    env: ["host=helloworld:80"]
  - cmd: $Registry/functions/helm package --app-version $ID -d ./helm ./helm/helloworld/
  - cmd: $Registry/functions/helm upgrade helloworld ./helm/helloworld/ --reuse-values --set helloworld.image=$Registry/helloworld:$ID
```

Zobacz [przykłady zadań](container-registry-tasks-samples.md) dla wieloetapowych plików YAML i plików Dockerfile dla kilku scenariuszy.

## <a name="run-a-sample-task"></a>Uruchamianie przykładowego zadania

Zadania obsługują zarówno ręczne wykonywanie, nazywane "szybkim uruchamianiem", jak i automatyczne wykonywanie po zatwierdzeniu git lub aktualizacji obrazu podstawowego.

Aby uruchomić zadanie, najpierw zdefiniuj kroki zadania w pliku YAML, a następnie wykonaj polecenie interfejsu wiersza polecenia platformy Azure [az acr run][az-acr-run].

Oto przykładowe polecenie interfejsu wiersza polecenia platformy Azure, które uruchamia zadanie przy użyciu przykładowego pliku YAML zadania. Jego kroki są kompilowane, a następnie wypychane do obrazu. Przed uruchomieniem polecenia zaktualizuj go przy użyciu nazwy własnego rejestru `\<acrName\>` kontenerów platformy Azure.

```azurecli
az acr run --registry <acrName> -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

Po uruchomieniu zadania dane wyjściowe powinny pokazywać postęp każdego kroku zdefiniowanego w pliku YAML. W poniższych danych wyjściowych kroki są wyświetlane jako `acb_step_0` i `acb_step_1` .

```azurecli
az acr run --registry myregistry -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

```output
Sending context to registry: myregistry...
Queued a run with ID: yd14
Waiting for an agent...
2018/09/12 20:08:44 Using acb_vol_0467fe58-f6ab-4dbd-a022-1bb487366941 as the home volume
2018/09/12 20:08:44 Creating Docker network: acb_default_network
2018/09/12 20:08:44 Successfully set up Docker network: acb_default_network
2018/09/12 20:08:44 Setting up Docker configuration...
2018/09/12 20:08:45 Successfully set up Docker configuration
2018/09/12 20:08:45 Logging in to registry: myregistry.azurecr-test.io
2018/09/12 20:08:46 Successfully logged in
2018/09/12 20:08:46 Executing step: acb_step_0
2018/09/12 20:08:46 Obtaining source code and scanning for dependencies...
2018/09/12 20:08:47 Successfully obtained source code and scanned for dependencies
Sending build context to Docker daemon  109.6kB
Step 1/1 : FROM hello-world
 ---> 4ab4c602aa5e
Successfully built 4ab4c602aa5e
Successfully tagged myregistry.azurecr-test.io/hello-world:yd14
2018/09/12 20:08:48 Executing step: acb_step_1
2018/09/12 20:08:48 Pushing image: myregistry.azurecr-test.io/hello-world:yd14, attempt 1
The push refers to repository [myregistry.azurecr-test.io/hello-world]
428c97da766c: Preparing
428c97da766c: Layer already exists
yd14: digest: sha256:1a6fd470b9ce10849be79e99529a88371dff60c60aab424c077007f6979b4812 size: 524
2018/09/12 20:08:55 Successfully pushed image: myregistry.azurecr-test.io/hello-world:yd14
2018/09/12 20:08:55 Step id: acb_step_0 marked as successful (elapsed time in seconds: 2.035049)
2018/09/12 20:08:55 Populating digests for step id: acb_step_0...
2018/09/12 20:08:57 Successfully populated digests for step id: acb_step_0
2018/09/12 20:08:57 Step id: acb_step_1 marked as successful (elapsed time in seconds: 6.832391)
The following dependencies were found:
- image:
    registry: myregistry.azurecr-test.io
    repository: hello-world
    tag: yd14
    digest: sha256:1a6fd470b9ce10849be79e99529a88371dff60c60aab424c077007f6979b4812
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/hello-world
    tag: latest
    digest: sha256:0add3ace90ecb4adbf7777e9aacf18357296e799f81cabc9fde470971e499788
  git: {}


Run ID: yd14 was successful after 19s
```

Aby uzyskać więcej informacji na temat zautomatyzowanych kompilacji w zatwierdzaniu git lub aktualizacji obrazu podstawowego, zobacz artykuły z samouczkiem [Automate image builds](container-registry-tutorial-build-task.md) (Automatyzowanie kompilacji obrazów) i [Base image update builds](container-registry-tutorial-base-image-update.md) (Kompilacje aktualizacji obrazu podstawowego).

## <a name="next-steps"></a>Następne kroki

Wieloetapowe informacje i przykłady zadań można znaleźć tutaj:

* [Odwołanie do](container-registry-tasks-reference-yaml.md) zadania — typy kroków zadań, ich właściwości i użycie.
* [Przykłady zadań](container-registry-tasks-samples.md) — `task.yaml` przykładowe i złożone pliki platformy Docker dla kilku scenariuszy.
* [Repo cmd —](https://github.com/AzureCR/cmd) kolekcja kontenerów jako polecenia dla zadań ACR.

<!-- IMAGES -->

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/
[task-examples]: https://github.com/Azure-Samples/acr-tasks
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-acr-task-create]: /cli/azure/acr/task#az_acr_task_create
[az-acr-run]: /cli/azure/acr#az_acr_run
[az-acr-task]: /cli/azure/acr/task
