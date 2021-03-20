---
title: Wyświetlanie dzienników uruchamiania zadań — zadania
description: Jak wyświetlać dzienniki uruchamiania generowane przez zadania ACR i zarządzać nimi.
ms.topic: article
ms.date: 03/09/2020
ms.openlocfilehash: b2a10d4a3a2746acf38445673af994c6317c77de
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93027180"
---
# <a name="view-and-manage-task-run-logs"></a>Wyświetlanie dzienników uruchamiania zadań i zarządzanie nimi

Każde zadanie uruchamiane w [Azure Container Registry zadania](container-registry-tasks-overview.md) generuje dane wyjściowe dziennika, które można sprawdzić, aby określić, czy kroki zadania zostały wykonane pomyślnie. 

W tym artykule opisano sposób wyświetlania dzienników uruchamiania zadań i zarządzania nimi.

## <a name="view-streamed-logs"></a>Wyświetl dzienniki przesyłane strumieniowo

Gdy zadanie zostanie wyzwolone ręcznie, dane wyjściowe dziennika są przesyłane strumieniowo bezpośrednio do konsoli programu. Na przykład podczas ręcznego wyzwalania zadania za pomocą polecenia [AZ ACR Build](/cli/azure/acr#az-acr-build), [AZ ACR Run](/cli/azure/acr#az-acr-run)lub [AZ ACR Task Run](/cli/azure/acr/task#az-acr-task-run) można zobaczyć dane wyjściowe dziennika przesyłane strumieniowo do konsoli programu. 

Poniższy przykład [AZ ACR Run](/cli/azure/acr#az-acr-run) polecenie ręcznie wyzwala zadanie, które uruchamia kontener ściągnięty z tego samego rejestru:

```azurecli
az acr run --registry mycontainerregistry1220 \
  --cmd '$Registry/samples/hello-world:v1' /dev/null
```

Dziennik strumieniowy:

```console
Queued a run with ID: cf4
Waiting for an agent...
2020/03/09 20:30:10 Alias support enabled for version >= 1.1.0, please see https://aka.ms/acr/tasks/task-aliases for more information.
2020/03/09 20:30:10 Creating Docker network: acb_default_network, driver: 'bridge'
2020/03/09 20:30:10 Successfully set up Docker network: acb_default_network
2020/03/09 20:30:10 Setting up Docker configuration...
2020/03/09 20:30:11 Successfully set up Docker configuration
2020/03/09 20:30:11 Logging in to registry: mycontainerregistry1220azurecr.io
2020/03/09 20:30:12 Successfully logged into mycontainerregistry1220azurecr.io
2020/03/09 20:30:12 Executing step ID: acb_step_0. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2020/03/09 20:30:12 Launching container with name: acb_step_0
Unable to find image 'mycontainerregistry1220azurecr.io/samples/hello-world:v1' locally
v1: Pulling from samples/hello-world
Digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e888a
Status: Downloaded newer image for mycontainerregistry1220azurecr.io/samples/hello-world:v1

Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]

2020/03/09 20:30:13 Successfully executed container: acb_step_0
2020/03/09 20:30:13 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 1.180081)

Run ID: cf4 was successful after 5s
```

## <a name="view-stored-logs"></a>Wyświetlanie dzienników przechowywanych 

Azure Container Registry przechowuje dzienniki uruchamiania dla wszystkich zadań. Przechowywane dzienniki uruchamiania można wyświetlić w Azure Portal. Lub użyj polecenia [AZ ACR Task Logs](/cli/azure/acr/task#az-acr-task-logs) , aby wyświetlić wybrany dziennik. Domyślnie dzienniki są przechowywane przez 30 dni.

Jeśli zadanie jest wyzwalane automatycznie, na przykład przez aktualizację kodu źródłowego, dostęp do przechowywanych dzienników jest *jedynym* sposobem wyświetlania dzienników uruchomienia. Automatyczne Wyzwalacze zadań obejmują zatwierdzenia kodu źródłowego lub żądania ściągnięcia, aktualizacje obrazu podstawowego i wyzwalacze czasomierza.

Aby wyświetlić dzienniki uruchamiania w portalu:

1. Przejdź do rejestru kontenerów.
1. W obszarze **usługi** wybierz pozycję **zadania**  >  **uruchomienia**.
1. Wybierz **Identyfikator uruchomienia** , aby wyświetlić stan uruchomienia i dzienniki uruchomienia. Dziennik zawiera te same informacje, co w przypadku dziennika strumieniowego, jeśli został wygenerowany.

![Wyświetl Portal logowania uruchamiania zadań](./media/container-registry-tasks-logs/portal-task-run-logs.png)

Aby wyświetlić dziennik przy użyciu interfejsu wiersza polecenia platformy Azure, uruchom polecenie [AZ ACR Task Logs](/cli/azure/acr/task#az-acr-task-logs) i określ identyfikator uruchomienia, nazwę zadania lub określony obraz tworzony przez zadanie kompilacji. Jeśli nazwa zadania zostanie określona, polecenie wyświetli dziennik dla ostatniego utworzonego uruchomienia.

Poniższy przykład wyprowadza dziennik dla przebiegu o IDENTYFIKATORze *CF4*:

```azurecli
az acr task logs --registry mycontainerregistry1220 \
  --run-id cf4
```

## <a name="alternative-log-storage"></a>Alternatywny magazyn dzienników

Możesz chcieć przechowywać dzienniki uruchamiania zadań w lokalnym systemie plików lub użyć alternatywnego rozwiązania do archiwizowania, takiego jak Azure Storage.

Można na przykład utworzyć lokalny katalog *tasklogs* i przekierować dane wyjściowe [dzienników zadań AZ ACR](/cli/azure/acr/task#az-acr-task-logs) do pliku lokalnego:

```azurecli
mkdir ~/tasklogs

az acr task logs --registry mycontainerregistry1220 \
  --run-id cf4 > ~/tasklogs/cf4.log
```

Pliki dzienników lokalnych można także zapisać do usługi Azure Storage. Na przykład użyj [interfejsu wiersza polecenia platformy Azure](../storage/blobs/storage-quickstart-blobs-cli.md), [Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md)lub innych metod przekazywania plików na konto magazynu.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [zadaniach Azure Container Registry](container-registry-tasks-overview.md)


<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-pack-build]: /cli/azure/acr/pack#az-acr-pack-build
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-update]: /cli/azure/acr/task#az-acr-task-update
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
