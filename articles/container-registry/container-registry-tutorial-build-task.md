---
title: Samouczek — kompilowanie obrazu po zatwierdzeniu kodu
description: Z tego samouczka dowiesz się, jak skonfigurować usługę Azure Container Registry Task w celu automatycznego wyzwalania kompilacji obrazu kontenera w chmurze, gdy zatwierdzasz kod źródłowy do repozytorium Git.
ms.topic: tutorial
ms.date: 11/24/2020
ms.custom: seodec18, mvc, devx-track-azurecli
ms.openlocfilehash: b4806ed30319ff058df6dfae0340a73ad4cb6132
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780789"
---
# <a name="tutorial-automate-container-image-builds-in-the-cloud-when-you-commit-source-code"></a>Samouczek: Automatyzowanie kompilacji obrazu kontenera w chmurze po zatwierdzeniu kodu źródłowego

Oprócz szybkiego [zadania](container-registry-tutorial-quick-task.md)program zadania usługi ACR automatyczne kompilacje obrazu kontenera platformy Docker w chmurze po zatwierdzeniu kodu źródłowego do repozytorium Git. Obsługiwane konteksty usługi Git dla zadania usługi ACR obejmują publiczne lub prywatne usługi GitHub lub Azure Repos.

> [!NOTE]
> Obecnie zadania usługi ACR nie obsługuje wyzwalaczy zatwierdzeń ani żądań ściągnięć w GitHub Enterprise żądania.

W tym samouczku zadanie usługi ACR skompilowało i wypchło pojedynczy obraz kontenera określony w pliku Dockerfile podczas zatwierdzania kodu źródłowego do repozytorium Git. Aby utworzyć [](container-registry-tasks-multi-step.md) wieloetapowe zadanie, które używa pliku YAML do definiowania kroków kompilacji, wypychania i opcjonalnie testowania wielu kontenerów podczas zatwierdzania kodu, zobacz [Samouczek:](container-registry-tutorial-multistep-task.md)uruchamianie wieloetapowego przepływu pracy kontenera w chmurze po zatwierdzeniu kodu źródłowego . Aby uzyskać omówienie zadania usługi ACR, zobacz [Automate OS and framework patching with zadania usługi ACR (Automatyzacja](container-registry-tasks-overview.md) systemu operacyjnego i stosowanie poprawek struktury za pomocą zadania usługi ACR

W tym samouczku:

> [!div class="checklist"]
> * Tworzenie zadania podrzędnego
> * Testowanie zadania
> * Wyświetlanie stanu zadania podrzędnego
> * Wyzwalanie zadania po zatwierdzeniu kodu

W samouczku założono, że zostały już wykonane kroki z [poprzedniego samouczka](container-registry-tutorial-quick-task.md). Jeśli nie zostało to jeszcze zrobione, przed kontynuowaniem wykonaj kroki wymienione w poprzednim samouczku w części poświęconej [wymaganiom wstępnym](container-registry-tutorial-quick-task.md#prerequisites).

[!INCLUDE [container-registry-task-tutorial-prereq.md](../../includes/container-registry-task-tutorial-prereq.md)]
[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

## <a name="create-the-build-task"></a>Tworzenie zadania kompilacji

Po ukończeniu kroków wymaganych do włączenia usługi ACR Tasks w celu odczytywania stanu zatwierdzenia i tworzenia elementów webhook w repozytorium możesz utworzyć zadanie, które wyzwala kompilację obrazu kontenera po zatwierdzeniu do repozytorium.

Najpierw wypełnij te zmienne środowiskowe powłoki przy użyciu wartości odpowiednich dla danego środowiska. Ten krok nie jest ściśle wymagany, ale trochę ułatwia wykonywanie przedstawionych w tym samouczku wielowierszowych poleceń interfejsu wiersza polecenia platformy Azure. Jeśli te zmienne środowiskowe nie zostaną wypełnione, musisz ręcznie zastąpić każdą wartość wszędzie tam, gdzie jest wyświetlana w przykładowych poleceniach.

```console
ACR_NAME=<registry-name>        # The name of your Azure container registry
GIT_USER=<github-username>      # Your GitHub user account name
GIT_PAT=<personal-access-token> # The PAT you generated in the previous section
```

Teraz utwórz zadanie, wykonując następujące [polecenie az acr task create:][az-acr-task-create]

```azurecli
az acr task create \
    --registry $ACR_NAME \
    --name taskhelloworld \
    --image helloworld:{{.Run.ID}} \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git#main \
    --file Dockerfile \
    --git-access-token $GIT_PAT
```


To zadanie określa, że za każdym razem, gdy kod zostanie zatwierdzone do gałęzi *main* w repozytorium określonym przez , zadania usługi ACR skompilować obraz kontenera z kodu `--context` w tej gałęzi. Do kompilowania obrazu jest używany plik Dockerfile określony przez parametr `--file` z katalogu głównego repozytorium. Argument `--image` określa wartość sparametryzowaną elementu `{{.Run.ID}}` dla części wersji tagu obrazu, zapewniając, że skompilowany obraz jest powiązany z określoną kompilacją i jest jednoznacznie oznakowany.

Dane wyjściowe z pomyślnie wykonanego polecenia [az acr task create][az-acr-task-create] przypominają następujące dane:

```output
{
  "agentConfiguration": {
    "cpu": 2
  },
  "creationDate": "2010-11-19T22:42:32.972298+00:00",
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myregistry/providers/Microsoft.ContainerRegistry/registries/myregistry/tasks/taskhelloworld",
  "location": "westcentralus",
  "name": "taskhelloworld",
  "platform": {
    "architecture": "amd64",
    "os": "Linux",
    "variant": null
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "myregistry",
  "status": "Enabled",
  "step": {
    "arguments": [],
    "baseImageDependencies": null,
    "contextPath": "https://github.com/gituser/acr-build-helloworld-node#main",
    "dockerFilePath": "Dockerfile",
    "imageNames": [
      "helloworld:{{.Run.ID}}"
    ],
    "isPushEnabled": true,
    "noCache": false,
    "type": "Docker"
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
          "repositoryUrl": "https://github.com/gituser/acr-build-helloworld-node#main",
          "sourceControlAuthProperties": null,
          "sourceControlType": "GitHub"
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

## <a name="test-the-build-task"></a>Testowanie zadania kompilacji

Masz teraz zadanie, które definiuje kompilację. Aby przetestować potok kompilacji, należy ręcznie wyzwolić kompilację, wykonując polecenie [az acr task run][az-acr-task-run]:

```azurecli
az acr task run --registry $ACR_NAME --name taskhelloworld
```

Domyślnie polecenie `az acr task run` przesyła strumieniowo dane wyjściowe dziennika do konsoli podczas wykonywania polecenia. Dane wyjściowe są skrócone w celu pokazania kluczowych kroków.

```output
2020/11/19 22:51:00 Using acb_vol_9ee1f28c-4fd4-43c8-a651-f0ed027bbf0e as the home volume
2020/11/19 22:51:00 Setting up Docker configuration...
2020/11/19 22:51:02 Successfully set up Docker configuration
2020/11/19 22:51:02 Logging in to registry: myregistry.azurecr.io
2020/11/19 22:51:03 Successfully logged in
2020/11/19 22:51:03 Executing step: build
2020/11/19 22:51:03 Obtaining source code and scanning for dependencies...
2020/11/19 22:51:05 Successfully obtained source code and scanned for dependencies
Sending build context to Docker daemon  23.04kB
Step 1/5 : FROM node:15-alpine
[...]
Step 5/5 : CMD ["node", "/src/server.js"]
 ---> Running in 7382eea2a56a
Removing intermediate container 7382eea2a56a
 ---> e33cd684027b
Successfully built e33cd684027b
Successfully tagged myregistry.azurecr.io/helloworld:da2
2020/11/19 22:51:11 Executing step: push
2020/11/19 22:51:11 Pushing image: myregistry.azurecr.io/helloworld:da2, attempt 1
The push refers to repository [myregistry.azurecr.io/helloworld]
4a853682c993: Preparing
[...]
4a853682c993: Pushed
[...]
da2: digest: sha256:c24e62fd848544a5a87f06ea60109dbef9624d03b1124bfe03e1d2c11fd62419 size: 1366
2020/11/19 22:51:21 Successfully pushed image: myregistry.azurecr.io/helloworld:da2
2020/11/19 22:51:21 Step id: build marked as successful (elapsed time in seconds: 7.198937)
2020/11/19 22:51:21 Populating digests for step id: build...
2020/11/19 22:51:22 Successfully populated digests for step id: build
2020/11/19 22:51:22 Step id: push marked as successful (elapsed time in seconds: 10.180456)
The following dependencies were found:
- image:
    registry: myregistry.azurecr.io
    repository: helloworld
    tag: da2
    digest: sha256:c24e62fd848544a5a87f06ea60109dbef9624d03b1124bfe03e1d2c11fd62419
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git:
    git-head-revision: 68cdf2a37cdae0873b8e2f1c4d80ca60541029bf


Run ID: ca6 was successful after 27s
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

```azurecli
Username for 'https://github.com': <github-username>
Password for 'https://githubuser@github.com': <personal-access-token>
```

Po wypchnięciu zatwierdzenia do repozytorium element webhook utworzony przez usługę ACR Tasks wyzwala i rozpoczyna kompilację w usłudze Azure Container Registry. Wyświetl dzienniki dla aktualnie uruchomionego zadania, aby sprawdzić i monitorować postęp kompilacji:

```azurecli
az acr task logs --registry $ACR_NAME
```

Dane wyjściowe są podobne do następujących danych i przedstawiają aktualnie (lub ostatnio) wykonywane zadanie:

```output
Showing logs of the last created run.
Run ID: ca7

[...]

Run ID: ca7 was successful after 38s
```

## <a name="list-builds"></a>Lista kompilacji

Aby wyświetlić listę przebiegów zadań, które usługa ACR Tasks wykonała dla rejestru, uruchom polecenie [az acr task list-runs][az-acr-task-list-runs]:

```azurecli
az acr task list-runs --registry $ACR_NAME --output table
```

Dane wyjściowe polecenia powinny wyglądać podobnie do następujących danych. Wyświetlone zostaną przebiegi wykonane przez usługę ACR Tasks, a dla najnowszego zadania w kolumnie TRIGGER pojawi się pozycja „Git Commit”:

```output
RUN ID    TASK            PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  --------------  ----------  ---------  ---------  --------------------  ----------
ca7       taskhelloworld  linux       Succeeded  Commit     2020-11-19T22:54:34Z  00:00:29
ca6       taskhelloworld  linux       Succeeded  Manual     2020-11-19T22:51:47Z  00:00:24
ca5                       linux       Succeeded  Manual     2020-11-19T22:23:42Z  00:00:23
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku pokazano, jak używać zadania w celu automatycznego wyzwalania kompilacji obrazu kontenera na platformie Azure po zatwierdzeniu kodu źródłowego do repozytorium Git. Przejdź do kolejnego samouczka, aby dowiedzieć się, jak utworzyć zadania, które wyzwalają kompilacje przy aktualizacji obrazu podstawowego obrazu kontenera.

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
[az-login]: /cli/azure/reference-index#az_login
