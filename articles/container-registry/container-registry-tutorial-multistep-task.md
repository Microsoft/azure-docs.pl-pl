---
title: Samouczek — wieloetapowe zadanie ACR
description: Z tego samouczka dowiesz się, jak skonfigurować zadanie Azure Container Registry w celu automatycznego wyzwalania wieloetapowego przepływu pracy w celu kompilowania, uruchamiania i wypychania obrazów kontenerów w chmurze po zatwierdzeniu kodu źródłowego do repozytorium Git.
ms.topic: tutorial
ms.date: 11/24/2020
ms.custom: seodec18, mvc, devx-track-azurecli
ms.openlocfilehash: 6d6ca8c41ce808df4b32bc10a7b537ffae809b3a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780681"
---
# <a name="tutorial-run-a-multi-step-container-workflow-in-the-cloud-when-you-commit-source-code"></a>Samouczek: uruchamianie wieloetapowego przepływu pracy kontenera w chmurze po zatwierdzeniu kodu źródłowego

Oprócz szybkiego [](container-registry-tutorial-quick-task.md)zadania program zadania usługi ACR wieloetapowe, oparte na wielu kontenerach przepływy pracy, które mogą być wyzwalane automatycznie po zatwierdzeniu kodu źródłowego do repozytorium Git. 

Z tego samouczka dowiesz się, jak używać przykładowych plików YAML do definiowania wieloetapowych zadań, które kompilowały, uruchamiały i wypychały co najmniej jeden obraz kontenera do rejestru podczas zatwierdzania kodu źródłowego. Aby utworzyć zadanie, które automatyzuje tylko kompilację pojedynczego obrazu po zatwierdzeniu kodu, zobacz [Tutorial: Automate container image builds in the cloud when you commit source code](container-registry-tutorial-build-task.md)(Samouczek: automatyzowanie kompilacji obrazu kontenera w chmurze po zatwierdzeniu kodu źródłowego). Aby uzyskać omówienie zadania usługi ACR, zobacz [Automate OS and framework patching with zadania usługi ACR](container-registry-tasks-overview.md),

W tym samouczku:

> [!div class="checklist"]
> * Definiowanie wieloetapowego zadania przy użyciu pliku YAML
> * Tworzenie zadania podrzędnego
> * Opcjonalnie dodaj poświadczenia do zadania, aby umożliwić dostęp do innego rejestru
> * Testowanie zadania
> * Wyświetlanie stanu zadania podrzędnego
> * Wyzwalanie zadania po zatwierdzeniu kodu

W samouczku założono, że zostały już wykonane kroki z [poprzedniego samouczka](container-registry-tutorial-quick-task.md). Jeśli nie zostało to jeszcze zrobione, przed kontynuowaniem wykonaj kroki wymienione w poprzednim samouczku w części poświęconej [wymaganiom wstępnym](container-registry-tutorial-quick-task.md#prerequisites).

[!INCLUDE [container-registry-task-tutorial-prereq.md](../../includes/container-registry-task-tutorial-prereq.md)]
[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

## <a name="create-a-multi-step-task"></a>Tworzenie zadania wieloetapowego

Teraz, po ukończeniu kroków wymaganych do umożliwienia programowi zadania usługi ACR odczytywania stanu zatwierdzenia i tworzenia webhook w repozytorium, utwórz wieloetapowe zadanie, które wyzwala tworzenie, uruchamianie i wypychanie obrazu kontenera.

### <a name="yaml-file"></a>Plik YAML

Kroki wieloetapowego zadania należy zdefiniować w pliku [YAML.](container-registry-tasks-reference-yaml.md) Pierwsze przykładowe wieloetapowe zadanie w tym samouczku jest zdefiniowane w pliku , który znajduje się w katalogu głównym sklonowanego repozytorium `taskmulti.yaml` GitHub:

```yml
version: v1.1.0
steps:
# Build target image
- build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} -f Dockerfile .
# Run image 
- cmd: -t {{.Run.Registry}}/hello-world:{{.Run.ID}}
  id: test
  detach: true
  ports: ["8080:80"]
- cmd: docker stop test
# Push image
- push:
  - {{.Run.Registry}}/hello-world:{{.Run.ID}}
```

To wieloetapowe zadanie obejmuje następujące czynności:

1. Uruchamia krok `build` kompilowania obrazu z pliku Dockerfile w katalogu roboczy. Obraz jest przeznaczony dla rejestru , w którym jest uruchamiane zadanie, i jest oznaczony unikatowym `Run.Registry` identyfikatorem zadania usługi ACR uruchomienia. 
1. Uruchamia `cmd` krok, aby uruchomić obraz w kontenerze tymczasowym. Ten przykład uruchamia długotrwały kontener w tle i zwraca identyfikator kontenera, a następnie zatrzymuje kontener. W rzeczywistym scenariuszu możesz uwzględnić kroki testowania uruchomionego kontenera, aby upewnić się, że działa prawidłowo.
1. W kroku `push` wypycha obraz, który został sbudowaną do rejestru uruchamiania.

### <a name="task-command"></a>Task , polecenie

Najpierw wypełnij te zmienne środowiskowe powłoki przy użyciu wartości odpowiednich dla danego środowiska. Ten krok nie jest ściśle wymagany, ale trochę ułatwia wykonywanie przedstawionych w tym samouczku wielowierszowych poleceń interfejsu wiersza polecenia platformy Azure. Jeśli te zmienne środowiskowe nie zostaną wypełnione, musisz ręcznie zastąpić każdą wartość wszędzie tam, gdzie jest wyświetlana w przykładowych poleceniach.

```console
ACR_NAME=<registry-name>        # The name of your Azure container registry
GIT_USER=<github-username>      # Your GitHub user account name
GIT_PAT=<personal-access-token> # The PAT you generated in the previous section
```

Teraz utwórz zadanie, wykonując następujące [polecenie az acr task create:][az-acr-task-create]

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name example1 \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git#main \
    --file taskmulti.yaml \
    --git-access-token $GIT_PAT
```

To zadanie określa, że za każdym razem, gdy kod zostanie zatwierdzone do gałęzi *main* w repozytorium określonym przez , zadania usługi ACR zadanie wieloetapowe będzie uruchamiane z kodu `--context` w tej gałęzi. Kroki definiuje plik YAML określony przez w katalogu `--file` głównym repozytorium. 

Dane wyjściowe z pomyślnie wykonanego polecenia [az acr task create][az-acr-task-create] przypominają następujące dane:

```output
{
  "agentConfiguration": {
    "cpu": 2
  },
  "creationDate": "2020-11-20T03:14:31.763887+00:00",
  "credentials": null,
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myregistry/providers/Microsoft.ContainerRegistry/registries/myregistry/tasks/taskmulti",
  "location": "westus",
  "name": "example1",
  "platform": {
    "architecture": "amd64",
    "os": "linux",
    "variant": null
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "status": "Enabled",
  "step": {
    "baseImageDependencies": null,
    "contextAccessToken": null,
    "contextPath": "https://github.com/gituser/acr-build-helloworld-node.git#main",
    "taskFilePath": "taskmulti.yaml",
    "type": "FileTask",
    "values": [],
    "valuesFilePath": null
  },
  "tags": null,
  "timeout": 3600,
  "trigger": {
    "baseImageTrigger": {
      "baseImageTriggerType": "Runtime",
      "name": "defaultBaseimageTriggerName",
      "status": "Enabled"
    },
    "sourceTriggers": [
      {
        "name": "defaultSourceTriggerName",
        "sourceRepository": {
          "branch": "main",
          "repositoryUrl": "https://github.com/gituser/acr-build-helloworld-node.git#main",
          "sourceControlAuthProperties": null,
          "sourceControlType": "Github"
        },
        "sourceTriggerEvents": [
          "commit"
        ],
        "status": "Enabled"
      }
    ]
  },
  "type": "Microsoft.ContainerRegistry/registries/tasks"
}
```

## <a name="test-the-multi-step-workflow"></a>Testowanie wieloetapowego przepływu pracy

Aby przetestować zadanie wieloetapowe, wyzwolij je ręcznie, wykonując [polecenie az acr task run:][az-acr-task-run]

```azurecli-interactive
az acr task run --registry $ACR_NAME --name example1
```

Domyślnie polecenie `az acr task run` przesyła strumieniowo dane wyjściowe dziennika do konsoli podczas wykonywania polecenia. Dane wyjściowe pokazują postęp uruchamiania każdego z kroków zadania. Poniższe dane wyjściowe są skrócone w celu pokazania kluczowych kroków.

```output
Queued a run with ID: cab
Waiting for an agent...
2020/11/20 00:03:31 Downloading source code...
2020/11/20 00:03:33 Finished downloading source code
2020/11/20 00:03:33 Using acb_vol_cfe6bd55-3076-4215-8091-6a81aec3d1b1 as the home volume
2020/11/20 00:03:33 Creating Docker network: acb_default_network, driver: 'bridge'
2020/11/20 00:03:34 Successfully set up Docker network: acb_default_network
2020/11/20 00:03:34 Setting up Docker configuration...
2020/11/20 00:03:34 Successfully set up Docker configuration
2020/11/20 00:03:34 Logging in to registry: myregistry.azurecr.io
2020/11/20 00:03:35 Successfully logged into myregistry.azurecr.io
2020/11/20 00:03:35 Executing step ID: acb_step_0. Working directory: '', Network: 'acb_default_network'
2020/11/20 00:03:35 Scanning for dependencies...
2020/11/20 00:03:36 Successfully scanned dependencies
2020/11/20 00:03:36 Launching container with name: acb_step_0
Sending build context to Docker daemon  24.06kB
[...]
Successfully built f669bfd170af
Successfully tagged myregistry.azurecr.io/hello-world:cf19
2020/11/20 00:03:43 Successfully executed container: acb_step_0
2020/11/20 00:03:43 Executing step ID: acb_step_1. Working directory: '', Network: 'acb_default_network'
2020/11/20 00:03:43 Launching container with name: acb_step_1
279b1cb6e092b64c8517c5506fcb45494cd5a0bd10a6beca3ba97f25c5d940cd
2020/11/20 00:03:44 Successfully executed container: acb_step_1
2020/11/20 00:03:44 Executing step ID: acb_step_2. Working directory: '', Network: 'acb_default_network'
2020/11/20 00:03:44 Pushing image: myregistry.azurecr.io/hello-world:cf19, attempt 1
[...]
2020/11/20 00:03:46 Successfully pushed image: myregistry.azurecr.io/hello-world:cf19
2020/11/20 00:03:46 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 7.425169)
2020/11/20 00:03:46 Populating digests for step ID: acb_step_0...
2020/11/20 00:03:47 Successfully populated digests for step ID: acb_step_0
2020/11/20 00:03:47 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 0.827129)
2020/11/20 00:03:47 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 2.112113)
2020/11/20 00:03:47 The following dependencies were found:
2020/11/20 00:03:47
- image:
    registry: myregistry.azurecr.io
    repository: hello-world
    tag: cf19
    digest: sha256:6b981a8ca8596e840228c974c929db05c0727d8630465de536be74104693467a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 15-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git:
    git-head-revision: 1a3065388a0238e52865db1c8f3e97492a43444c

Run ID: cab was successful after 18s
```

## <a name="trigger-a-build-with-a-commit"></a>Wyzwalanie kompilacji za pomocą zatwierdzenia

Teraz, po przetestowaniu zadania przez jego ręczne uruchomienie, wyzwól je automatycznie za pomocą zmiany kodu źródłowego.

Najpierw upewnij się, że jesteś w katalogu zawierającym lokalny klon [repozytorium][sample-repo]:

```console
cd acr-build-helloworld-node
```

Następnie uruchom następujące polecenia do utworzenia, zatwierdzenia i wypchnięcia nowego pliku do rozwidlenia repozytorium w usłudze GitHub:

```console
echo "Hello World!" > hello.txt
git add hello.txt
git commit -m "Testing ACR Tasks"
git push origin main
```

Być może podczas wykonywania polecenia `git push` będzie konieczne wprowadzenie poświadczeń usługi GitHub. Podaj nazwę użytkownika usługi GitHub i wprowadź osobisty token dostępu (PAT) utworzony wcześniej dla hasła.

```azurecli-interactive
Username for 'https://github.com': <github-username>
Password for 'https://githubuser@github.com': <personal-access-token>
```

Po wypchnięcie zatwierdzenia do repozytorium zostanie wyzminowany i zadania usługi ACR webhook utworzony przez Azure Container Registry. Wyświetl dzienniki dla aktualnie uruchomionego zadania, aby sprawdzić i monitorować postęp kompilacji:

```azurecli-interactive
az acr task logs --registry $ACR_NAME
```

Dane wyjściowe są podobne do następujących danych i przedstawiają aktualnie (lub ostatnio) wykonywane zadanie:

```output
Showing logs of the last created run.
Run ID: cad

[...]

Run ID: cad was successful after 37s
```

## <a name="list-builds"></a>Lista kompilacji

Aby wyświetlić listę przebiegów zadań, które usługa ACR Tasks wykonała dla rejestru, uruchom polecenie [az acr task list-runs][az-acr-task-list-runs]:

```azurecli-interactive
az acr task list-runs --registry $ACR_NAME --output table
```

Dane wyjściowe polecenia powinny wyglądać podobnie do następujących danych. Wyświetlone zostaną przebiegi wykonane przez usługę ACR Tasks, a dla najnowszego zadania w kolumnie TRIGGER pojawi się pozycja „Git Commit”:

```output
RUN ID    TASK            PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  --------------  ----------  ---------  ---------  --------------------  ----------
cad       example1        linux       Succeeded  Commit     2020-11-20T00:22:15Z  00:00:35
cac       taskhelloworld  linux       Succeeded  Commit     2020-11-20T00:22:15Z  00:00:22
cab       example1        linux       Succeeded  Manual     2020-11-20T00:18:36Z  00:00:47
```

## <a name="create-a-multi-registry-multi-step-task"></a>Tworzenie wieloetapowego zadania wieloetapowego rejestru

zadania usługi ACR domyślnie ma uprawnienia do wypychania lub ściągania obrazów z rejestru, w którym jest uruchamiane zadanie. Oprócz rejestru uruchamiania można uruchomić zadanie wieloetapowe, które dotyczy co najmniej jednego rejestru. Na przykład może być konieczne kompilowanie obrazów w jednym rejestrze i przechowywanie obrazów z różnymi tagami w drugim rejestrze, do którego uzyskuje dostęp system produkcyjny. W tym przykładzie pokazano, jak utworzyć takie zadanie i podać poświadczenia dla innego rejestru.

Jeśli nie masz jeszcze drugiego rejestru, utwórz go na przykład. Jeśli potrzebujesz rejestru, zobacz [poprzedni samouczek](container-registry-tutorial-quick-task.md) lub [Szybki start: tworzenie rejestru kontenerów za pomocą interfejsu wiersza polecenia platformy Azure](container-registry-get-started-azure-cli.md).

Aby utworzyć zadanie, potrzebna jest nazwa serwera logowania rejestru, która ma postać mycontainerregistrydate.azurecr.io *(wszystkie* małe litery). W tym przykładzie użyjemy drugiego rejestru do przechowywania obrazów oznaczonych tagiem data kompilacji.

### <a name="yaml-file"></a>Plik YAML

Drugie przykładowe wieloetapowe zadanie w tym samouczku jest zdefiniowane w pliku , który znajduje się w katalogu głównym sklonowanego `taskmulti-multiregistry.yaml` repozytorium GitHub:

```yml
version: v1.1.0
steps:
# Build target images
- build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} -f Dockerfile .
- build: -t {{.Values.regDate}}/hello-world:{{.Run.Date}} -f Dockerfile .
# Run image 
- cmd: -t {{.Run.Registry}}/hello-world:{{.Run.ID}}
  id: test
  detach: true
  ports: ["8080:80"]
- cmd: docker stop test
# Push images
- push:
  - {{.Run.Registry}}/hello-world:{{.Run.ID}}
  - {{.Values.regDate}}/hello-world:{{.Run.Date}}
```

To wieloetapowe zadanie obejmuje następujące czynności:

1. Uruchamia dwa `build` kroki tworzenia obrazów z pliku Dockerfile w katalogu roboczy:
    * Pierwszy z nich jest przeznaczony dla rejestru , w którym jest uruchamiane zadanie, i jest otagowany za pomocą `Run.Registry` zadania usługi ACR identyfikatora uruchomienia. 
    * Drugi jest przeznaczony dla rejestru identyfikowanego przez wartość , ustawianą podczas tworzenia zadania (lub dostarczana za pośrednictwem pliku zewnętrznego `regDate` `values.yaml` przekazanego do `az acr task create` ). Ten obraz jest otagowany datą uruchomienia.
1. Uruchamia `cmd` krok, aby uruchomić jeden z kontenerów wbudowanych. Ten przykład uruchamia długotrwały kontener w tle i zwraca identyfikator kontenera, a następnie zatrzymuje kontener. W rzeczywistym scenariuszu możesz przetestować uruchomiony kontener, aby upewnić się, że działa prawidłowo.
1. W kroku wypycha obrazy, które zostały sbudowaną, pierwszy do rejestru uruchamiania, a drugi do rejestru `push` identyfikowanego przez `regDate` .

### <a name="task-command"></a>Task , polecenie

Używając zdefiniowanych wcześniej zmiennych środowiskowych powłoki, utwórz zadanie, wykonując następujące [polecenie az acr task create.][az-acr-task-create] Zastąp nazwę rejestru nazwą *mycontainerregistrydate*.

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name example2 \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git#main \
    --file taskmulti-multiregistry.yaml \
    --git-access-token $GIT_PAT \
    --set regDate=mycontainerregistrydate.azurecr.io
```

### <a name="add-task-credential"></a>Dodawanie poświadczeń zadania

Aby wypchnąć obrazy do rejestru identyfikowane przez wartość , użyj `regDate` [polecenia az acr task credential add,][az-acr-task-credential-add] aby dodać poświadczenia logowania dla tego rejestru do zadania.

W tym przykładzie zaleca się [](container-registry-auth-service-principal.md) utworzenie jednostki usługi z dostępem do rejestru w zakresie roli *AcrPush,* aby mieć uprawnienia do wypychania obrazów. Aby utworzyć jednostkę usługi, zobacz ten skrypt interfejsu [wiersza polecenia platformy Azure.](https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-create/service-principal-create.sh)

Przekaż identyfikator aplikacji jednostki usługi i hasło w poniższym `az acr task credential add` poleceniu. Pamiętaj, aby zaktualizować nazwę serwera logowania *mycontainerregistrydate* przy użyciu nazwy drugiego rejestru:

```azurecli-interactive
az acr task credential add --name example2 \
    --registry $ACR_NAME \
    --login-server mycontainerregistrydate.azurecr.io \
    --username <service-principal-application-id> \
    --password <service-principal-password>
```

Interfejs wiersza polecenia zwraca nazwę dodanego serwera logowania rejestru.

### <a name="test-the-multi-step-workflow"></a>Testowanie wieloetapowego przepływu pracy

Tak jak w poprzednim przykładzie, aby przetestować zadanie wieloetapowe, wyzwolij je ręcznie, wykonując [polecenie az acr task run.][az-acr-task-run] Aby wyzwolić zadanie za pomocą zatwierdzenia w repozytorium Git, zobacz sekcję [Trigger a build with a commit (Wyzwalanie kompilacji za pomocą zatwierdzenia).](#trigger-a-build-with-a-commit)

```azurecli-interactive
az acr task run --registry $ACR_NAME --name example2
```

Domyślnie polecenie `az acr task run` przesyła strumieniowo dane wyjściowe dziennika do konsoli podczas wykonywania polecenia. Tak jak poprzednio, dane wyjściowe pokazują postęp uruchamiania każdego z kroków zadania. Dane wyjściowe są skrócone w celu pokazania kluczowych kroków.

Dane wyjściowe:

```output
Queued a run with ID: cf1g
Waiting for an agent...
2020/11/20 04:33:39 Downloading source code...
2020/11/20 04:33:41 Finished downloading source code
2020/11/20 04:33:42 Using acb_vol_4569b017-29fe-42bd-83b2-25c45a8ac807 as the home volume
2020/11/20 04:33:42 Creating Docker network: acb_default_network, driver: 'bridge'
2020/11/20 04:33:43 Successfully set up Docker network: acb_default_network
2020/11/20 04:33:43 Setting up Docker configuration...
2020/11/20 04:33:44 Successfully set up Docker configuration
2020/11/20 04:33:44 Logging in to registry: mycontainerregistry.azurecr.io
2020/11/20 04:33:45 Successfully logged into mycontainerregistry.azurecr.io
2020/11/20 04:33:45 Logging in to registry: mycontainerregistrydate.azurecr.io
2020/11/20 04:33:47 Successfully logged into mycontainerregistrydate.azurecr.io
2020/11/20 04:33:47 Executing step ID: acb_step_0. Working directory: '', Network: 'acb_default_network'
2020/11/20 04:33:47 Scanning for dependencies...
2020/11/20 04:33:47 Successfully scanned dependencies
2020/11/20 04:33:47 Launching container with name: acb_step_0
Sending build context to Docker daemon  25.09kB
[...]
Successfully tagged mycontainerregistry.azurecr.io/hello-world:cf1g
2020/11/20 04:33:55 Successfully executed container: acb_step_0
2020/11/20 04:33:55 Executing step ID: acb_step_1. Working directory: '', Network: 'acb_default_network'
2020/11/20 04:33:55 Scanning for dependencies...
2020/11/20 04:33:56 Successfully scanned dependencies
2020/11/20 04:33:56 Launching container with name: acb_step_1
Sending build context to Docker daemon  25.09kB
[...]
Successfully tagged mycontainerregistrydate.azurecr.io/hello-world:20190503-043342z
2020/11/20 04:33:57 Successfully executed container: acb_step_1
2020/11/20 04:33:57 Executing step ID: acb_step_2. Working directory: '', Network: 'acb_default_network'
2020/11/20 04:33:57 Launching container with name: acb_step_2
721437ff674051b6be63cbcd2fa8eb085eacbf38d7d632f1a079320133182101
2020/11/20 04:33:58 Successfully executed container: acb_step_2
2020/11/20 04:33:58 Executing step ID: acb_step_3. Working directory: '', Network: 'acb_default_network'
2020/11/20 04:33:58 Launching container with name: acb_step_3
test
2020/11/20 04:34:09 Successfully executed container: acb_step_3
2020/11/20 04:34:09 Executing step ID: acb_step_4. Working directory: '', Network: 'acb_default_network'
2020/11/20 04:34:09 Pushing image: mycontainerregistry.azurecr.io/hello-world:cf1g, attempt 1
The push refers to repository [mycontainerregistry.azurecr.io/hello-world]
[...]
2020/11/20 04:34:12 Successfully pushed image: mycontainerregistry.azurecr.io/hello-world:cf1g
2020/11/20 04:34:12 Pushing image: mycontainerregistrydate.azurecr.io/hello-world:20190503-043342z, attempt 1
The push refers to repository [mycontainerregistrydate.azurecr.io/hello-world]
[...]
2020/11/20 04:34:19 Successfully pushed image: mycontainerregistrydate.azurecr.io/hello-world:20190503-043342z
2020/11/20 04:34:19 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 8.125744)
2020/11/20 04:34:19 Populating digests for step ID: acb_step_0...
2020/11/20 04:34:21 Successfully populated digests for step ID: acb_step_0
2020/11/20 04:34:21 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 2.009281)
2020/11/20 04:34:21 Populating digests for step ID: acb_step_1...
2020/11/20 04:34:23 Successfully populated digests for step ID: acb_step_1
2020/11/20 04:34:23 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 0.795440)
2020/11/20 04:34:23 Step ID: acb_step_3 marked as successful (elapsed time in seconds: 11.446775)
2020/11/20 04:34:23 Step ID: acb_step_4 marked as successful (elapsed time in seconds: 9.734973)
2020/11/20 04:34:23 The following dependencies were found:
2020/11/20 04:34:23
- image:
    registry: mycontainerregistry.azurecr.io
    repository: hello-world
    tag: cf1g
    digest: sha256:75354e9edb995e8661438bad9913deed87a185fddd0193811f916d684b71a5d2
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 15-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git:
    git-head-revision: 9d9023473c46a5e2c315681b11eb4552ef0faccc
- image:
    registry: mycontainerregistrydate.azurecr.io
    repository: hello-world
    tag: 20190503-043342z
    digest: sha256:75354e9edb995e8661438bad9913deed87a185fddd0193811f916d684b71a5d2
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 15-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git:
    git-head-revision: 9d9023473c46a5e2c315681b11eb4552ef0faccc

Run ID: cf1g was successful after 46s
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku opisano sposób tworzenia wieloetapowych zadań opartych na wielu kontenerach, które są wyzwalane automatycznie po zatwierdzeniu kodu źródłowego do repozytorium Git. Aby uzyskać zaawansowane funkcje zadań wieloetapowych, w tym równoległe i zależne wykonywanie kroków, zobacz [zadania usługi ACR YAML](container-registry-tasks-reference-yaml.md). Przejdź do kolejnego samouczka, aby dowiedzieć się, jak utworzyć zadania, które wyzwalają kompilacje przy aktualizacji obrazu podstawowego obrazu kontenera.

> [!div class="nextstepaction"]
> [Automatyczne kompilacje przy aktualizacji obrazu podstawowego](container-registry-tutorial-base-image-update.md)

<!-- LINKS - External -->
[sample-repo]: https://github.com/Azure-Samples/acr-build-helloworld-node

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az_acr_task_create
[az-acr-task-run]: /cli/azure/acr/task#az_acr_task_run
[az-acr-task-list-runs]: /cli/azure/acr/task#az_acr_task_list_runs
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az_acr_task_credential_add    
[az-login]: /cli/azure/reference-index#az_login

<!-- IMAGES -->
[build-task-01-new-token]: ./media/container-registry-tutorial-build-tasks/build-task-01-new-token.png
[build-task-02-generated-token]: ./media/container-registry-tutorial-build-tasks/build-task-02-generated-token.png
