---
title: Wyświetlanie dzienników uruchamiania zadań — zadania
description: Sposób wyświetlania dzienników uruchamiania generowanych przez zadania usługi ACR.
ms.topic: article
ms.date: 03/09/2020
ms.openlocfilehash: ce5f33853be2aa48bcfd1916c7f8b94b9702f38c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781059"
---
# <a name="view-and-manage-task-run-logs"></a>Wyświetlanie dzienników uruchamiania zadań i zarządzanie nimi

Każde zadanie uruchamiane w [Azure Container Registry generuje](container-registry-tasks-overview.md) dane wyjściowe dziennika, które można sprawdzić, aby ustalić, czy kroki zadania zostały pomyślnie uruchomione. 

W tym artykule wyjaśniono, jak wyświetlać dzienniki uruchamiania zadań i zarządzać nimi.

## <a name="view-streamed-logs"></a>Wyświetlanie strumieniowanych dzienników

Po ręcznym wyzwoleniu zadania dane wyjściowe dziennika są przesyłane strumieniowo bezpośrednio do konsoli. Na przykład po ręcznym wyzwoleniu zadania przy użyciu polecenia [az acr build,](/cli/azure/acr#az_acr_build) [az acr run](/cli/azure/acr#az_acr_run)lub az [acr task run](/cli/azure/acr/task#az_acr_task_run) zobaczysz dane wyjściowe dziennika przesyłane strumieniowo do konsoli. 

Następujące przykładowe [polecenie az acr run](/cli/azure/acr#az_acr_run) ręcznie wyzwala zadanie, które uruchamia kontener ściągnięty z tego samego rejestru:

```azurecli
az acr run --registry mycontainerregistry1220 \
  --cmd '$Registry/samples/hello-world:v1' /dev/null
```

Dziennik przesyłany strumieniowo:

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

## <a name="view-stored-logs"></a>Wyświetlanie przechowywanych dzienników 

Azure Container Registry przechowuje dzienniki uruchamiania dla wszystkich zadań. Możesz wyświetlić przechowywane dzienniki uruchamiania w Azure Portal. Możesz też użyć [polecenia az acr task logs,](/cli/azure/acr/task#az_acr_task_logs) aby wyświetlić wybrany dziennik. Domyślnie dzienniki są przechowywane przez 30 dni.

Jeśli zadanie jest wyzwalane automatycznie, na przykład przez aktualizację kodu  źródłowego, uzyskanie dostępu do przechowywanych dzienników jest jedynym sposobem wyświetlania dzienników uruchamiania. Automatyczne wyzwalacze zadań obejmują zatwierdzenia kodu źródłowego lub żądania ściągnięć, aktualizacje obrazu podstawowego i wyzwalacze czasomierza.

Aby wyświetlić dzienniki uruchamiania w portalu:

1. Przejdź do rejestru kontenerów.
1. W **chmurze Services**(Usługi) wybierz pozycję Tasks Runs   >  **(Zadania są uruchamiane).**
1. Wybierz identyfikator **uruchomienia,** aby wyświetlić stan uruchomienia i dzienniki uruchamiania. Dziennik zawiera te same informacje co przesyłany strumieniowo dziennik, jeśli został wygenerowany.

![Wyświetlanie portalu logowania uruchamiania zadania](./media/container-registry-tasks-logs/portal-task-run-logs.png)

Aby wyświetlić dziennik przy użyciu interfejsu wiersza polecenia platformy Azure, uruchom narzędzie [az acr task logs](/cli/azure/acr/task#az_acr_task_logs) i określ identyfikator uruchomienia, nazwę zadania lub określony obraz, który został przez zadanie kompilacji. Jeśli zostanie określona nazwa zadania, polecenie wyświetla dziennik dla ostatniego utworzonego uruchomienia.

Poniższy przykład wyprowadza dziennik dla uruchomienia o identyfikatorze *cf4*:

```azurecli
az acr task logs --registry mycontainerregistry1220 \
  --run-id cf4
```

## <a name="alternative-log-storage"></a>Alternatywny magazyn dzienników

Możesz przechowywać dzienniki uruchamiania zadań w lokalnym systemie plików lub użyć alternatywnego rozwiązania do archiwizacji, takiego jak usługa Azure Storage.

Na przykład utwórz lokalny katalog *tasklogs* i przekieruj dane wyjściowe [dziennika zadań az acr](/cli/azure/acr/task#az_acr_task_logs) do pliku lokalnego:

```azurecli
mkdir ~/tasklogs

az acr task logs --registry mycontainerregistry1220 \
  --run-id cf4 > ~/tasklogs/cf4.log
```

Możesz również zapisywać lokalne pliki dziennika w usłudze Azure Storage. Możesz na przykład użyć interfejsu wiersza polecenia platformy [Azure,](../storage/blobs/storage-quickstart-blobs-cli.md) [interfejsu Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md)lub innych metod przekazywania plików na konto magazynu.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [zadania usługi Azure Container Registry](container-registry-tasks-overview.md)


<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az_acr_build
[az-acr-pack-build]: /cli/azure/acr/pack#az_acr_pack_build
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az_acr_task_create
[az-acr-task-run]: /cli/azure/acr/task#az_acr_task_run
[az-acr-task-update]: /cli/azure/acr/task#az_acr_task_update
[az-login]: /cli/azure/reference-index#az_login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
