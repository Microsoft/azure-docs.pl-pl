---
title: Zadanie wieloetapowe umożliwiające kompilację, testowanie obrazu & poprawek
description: Wprowadzenie do wieloetapowych zadań, funkcji ACR zadań w Azure Container Registry, które udostępniają przepływy pracy oparte na zadaniach służące do kompilowania, testowania i poprawiania obrazów kontenerów w chmurze.
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: fc671006beb1934b32fcd6ccf967a5bf9ff817fb
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106169202"
---
# <a name="run-multi-step-build-test-and-patch-tasks-in-acr-tasks"></a>Uruchamianie wieloetapowych zadań kompilacji, testów i poprawek w zadaniach ACR

Zadania wieloetapowe zwiększają możliwość tworzenia i wypychania pojedynczych obrazów przez zadania ACR z wieloetapowymi przepływami pracy opartymi na wielu kontenerach. Zadania wieloetapowe służą do kompilowania i wypychania kilku obrazów, w serii lub równolegle. Następnie Uruchom te obrazy jako polecenia w ramach jednego zadania. Każdy krok definiuje kompilację lub operację wypychania obrazu kontenera, a także definiuje wykonywanie kontenera. Każdy krok w zadaniu wieloetapowym używa kontenera jako środowiska wykonawczego.

> [!IMPORTANT]
> Jeśli wcześniej utworzono zadania za pomocą wersji zapoznawczej przy użyciu polecenia `az acr build-task`, trzeba je utworzyć ponownie przy użyciu polecenia [az acr task][az-acr-task].

Na przykład można uruchomić zadanie z krokami, które automatyzują następujące logiki:

1. Tworzenie obrazu aplikacji sieci Web
1. Uruchamianie kontenera aplikacji sieci Web
1. Kompilowanie obrazu testowego aplikacji sieci Web
1. Uruchom kontener testów aplikacji sieci Web, który wykonuje testy względem uruchomionego kontenera aplikacji
1. Jeśli testy zachodzą, Kompiluj pakiet archiwum wykresów Helm
1. Wykonaj `helm upgrade` przy użyciu nowego pakietu archiwum wykresów Helm

Wszystkie kroki są wykonywane w ramach platformy Azure, przeciążać zadania do zasobów obliczeniowych platformy Azure i zwalniać z zarządzania infrastrukturą. Oprócz usługi Azure Container Registry płacisz tylko za zasoby, których używasz. Aby uzyskać informacje na temat cen, zobacz sekcję **kompilacja kontenera** w artykule [Azure Container Registry][pricing].


## <a name="common-task-scenarios"></a>Typowe scenariusze zadań

Zadania wieloetapowe umożliwiają wykonywanie scenariuszy, takich jak następujące:

* Kompiluj, Otaguj i wypchnij jeden lub więcej obrazów kontenerów, w serii lub równolegle.
* Uruchom i Przechwyć wyniki testu jednostkowego i pokrycia kodu.
* Uruchom i Przechwyć testy funkcjonalne. Zadania ACR obsługują więcej niż jeden kontener, wykonując serię żądań między nimi.
* Wykonywanie wykonywane z zadaniami, w tym etapy wstępnego/publikowania kompilacji obrazu kontenera.
* Wdróż jeden lub więcej kontenerów z ulubionym aparatem wdrażania w środowisku docelowym.

## <a name="multi-step-task-definition"></a>Wieloetapowa Definicja zadania

Zadanie wieloetapowe w zadaniach ACR jest zdefiniowane jako seria kroków w pliku YAML. Każdy krok może określać zależności po pomyślnym zakończeniu jednego lub kilku poprzednich kroków. Dostępne są następujące typy etapów zadań:

* [`build`](container-registry-tasks-reference-yaml.md#build): Kompiluj co najmniej jeden obraz kontenera przy użyciu znanej `docker build` składni, w serii lub równolegle.
* [`push`](container-registry-tasks-reference-yaml.md#push): Wypychanie wbudowanych obrazów do rejestru kontenerów. Prywatne rejestry, takie jak Azure Container Registry, są obsługiwane przez publiczny koncentrator platformy Docker.
* [`cmd`](container-registry-tasks-reference-yaml.md#cmd): Uruchom kontener, aby mógł działać jako funkcja w kontekście uruchomionego zadania. Parametry można przekazać do kontenera `[ENTRYPOINT]` i określić właściwości, takie jak ENV, odłącz i inne znane `docker run` parametry. `cmd`Typ kroku umożliwia testowanie jednostkowe i funkcjonalne, przy jednoczesnym wykonywaniu kontenerów.

Poniższe fragmenty kodu pokazują, jak połączyć te typy kroków zadań. Zadania wieloetapowe mogą być tak proste, jak Kompilowanie pojedynczego obrazu z pliku dockerfile i wypychanie do rejestru przy użyciu pliku YAML podobnego do:

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID .
  - push: ["$Registry/hello-world:$ID"]
```

Lub bardziej skomplikowane, takie jak fikcyjna wieloetapowa definicja, która obejmuje kroki kompilowania, testowania, Helm pakietu i wdrożenia Helm (nie podano konfiguracji kontenera kontenerów i repozytorium Helm):

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

Zobacz [przykłady zadań](container-registry-tasks-samples.md) dla wieloetapowych zadań YAML pliki i wieloetapowe dockerfile dla kilku scenariuszy.

## <a name="run-a-sample-task"></a>Uruchamianie przykładowego zadania

Zadania obsługują zarówno wykonywanie ręczne, nazywane "szybkim uruchamianiem", jak i zautomatyzowane wykonywanie przy zatwierdzaniu Git lub aktualizacji obrazu podstawowego.

Aby uruchomić zadanie, należy najpierw zdefiniować kroki zadania w pliku YAML, a następnie wykonać polecenie interfejsu wiersza polecenia platformy Azure [AZ ACR Run][az-acr-run].

Oto przykładowe polecenie interfejsu wiersza polecenia platformy Azure, które uruchamia zadanie przy użyciu przykładowego pliku YAML zadania. Jego kroki kompilują i wypychają obraz. `\<acrName\>`Przed uruchomieniem polecenia zaktualizuj nazwę własnego rejestru kontenerów platformy Azure.

```azurecli
az acr run --registry <acrName> -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

Po uruchomieniu zadania wynik powinien zawierać postęp każdego kroku zdefiniowanego w pliku YAML. W poniższych danych wyjściowych kroki są wyświetlane jako `acb_step_0` i `acb_step_1` .

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

Aby uzyskać więcej informacji na temat zautomatyzowanych kompilacji w ramach zatwierdzenia Git lub aktualizacji obrazu podstawowego, zobacz artykuły z samouczkiem [Automatyzowanie](container-registry-tutorial-build-task.md) Kompilowanie obrazów i tworzenie [obrazów podstawowych](container-registry-tutorial-base-image-update.md) .

## <a name="next-steps"></a>Następne kroki

Informacje o wieloetapowym odniesieniu do zadania i przykłady można znaleźć tutaj:

* [Odwołanie do zadania](container-registry-tasks-reference-yaml.md) — typy etapów zadań, ich właściwości i użycie.
* [Przykłady zadań](container-registry-tasks-samples.md) — przykład `task.yaml` i pliki platformy Docker dla kilku scenariuszy, proste do złożonej.
* [Polecenie cmd repozytorium](https://github.com/AzureCR/cmd) — kolekcja kontenerów jako poleceń dla zadań ACR.

<!-- IMAGES -->

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/
[task-examples]: https://github.com/Azure-Samples/acr-tasks
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-task]: /cli/azure/acr/task
