---
title: Szybki Start — Tworzenie obrazu kontenera na żądanie na platformie Azure
description: Użyj poleceń Azure Container Registry, aby szybko kompilować, wypchnięciować i uruchamiać obraz kontenera Docker na żądanie w chmurze platformy Azure.
ms.topic: quickstart
ms.date: 09/25/2020
ms.custom: contperf-fy21q1, devx-track-azurecli
ms.openlocfilehash: c6fe1fc246d112218b492072155175b2db99c8c9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97032953"
---
# <a name="quickstart-build-and-run-a-container-image-using-azure-container-registry-tasks"></a>Szybki Start: kompilowanie i uruchamianie obrazu kontenera za pomocą zadań Azure Container Registry

W tym przewodniku szybki start za pomocą poleceń [Azure Container Registry zadania][container-registry-tasks-overview] można szybko kompilować, wypchnięciować i uruchamiać obraz kontenera platformy Docker w systemie Azure bez lokalnej instalacji platformy Docker. ACR Tasks to zestaw funkcji w ramach Azure Container Registry, które ułatwiają zarządzanie obrazami kontenerów w ramach cyklu życia kontenera i ich modyfikowanie. Ten przykład pokazuje, jak odciążyć cykl programowania obrazów kontenera "wewnętrzna pętla" do chmury z kompilacjami na żądanie przy użyciu lokalnego pliku dockerfile. 

Po tym przewodniku szybki start zapoznaj się z bardziej zaawansowanymi funkcjami zadań ACR przy użyciu [samouczków](container-registry-tutorial-quick-task.md). Zadania ACR umożliwiają automatyzowanie kompilacji obrazów na podstawie zatwierdzeń kodu lub aktualizacji obrazu podstawowego lub testowanie wielu kontenerów równolegle, między innymi scenariuszami. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]
    
- Ten przewodnik Szybki Start wymaga wersji 2.0.58 lub nowszej interfejsu wiersza polecenia platformy Azure. W przypadku korzystania z Azure Cloud Shell Najnowsza wersja jest już zainstalowana.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Jeśli nie masz jeszcze rejestru kontenerów, najpierw utwórz grupę zasobów za pomocą polecenia [AZ Group Create][az-group-create] . Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container-registry"></a>Tworzenie rejestru kontenerów

Utwórz rejestr kontenerów za pomocą polecenia [AZ ACR Create][az-acr-create] . Nazwa rejestru musi być unikatowa w obrębie platformy Azure i może zawierać od 5 do 50 znaków alfanumerycznych. W poniższym przykładzie używany jest *myContainerRegistry008* . Zaktualizuj ją do unikatowej wartości.

```azurecli-interactive
az acr create --resource-group myResourceGroup \
  --name myContainerRegistry008 --sku Basic
```

W tym przykładzie jest tworzony rejestr *podstawowy* , opcja zoptymalizowana pod kątem kosztów dla deweloperów, która uczy się Azure Container Registry. Aby uzyskać szczegółowe informacje o dostępnych warstwach usług, zobacz temat [warstwy usługi Container Registry][container-registry-skus].

## <a name="build-and-push-image-from-a-dockerfile"></a>Kompilowanie i wypychanie obrazu z pliku dockerfile

Teraz Użyj Azure Container Registry do kompilowania i wypychania obrazu. Najpierw utwórz lokalny katalog roboczy, a następnie utwórz pliku dockerfile o nazwie *pliku dockerfile* z pojedynczym wierszem: `FROM mcr.microsoft.com/hello-world` . Jest to prosty przykład, aby skompilować obraz kontenera systemu Linux z `hello-world` obrazu hostowanego w witrynie Microsoft Container Registry. Możesz tworzyć własne obrazy pliku dockerfile i kompilacje dla innych platform. Jeśli pracujesz z powłoką bash, Utwórz pliku dockerfile za pomocą następującego polecenia:

```bash
echo FROM mcr.microsoft.com/hello-world > Dockerfile
```

Uruchom polecenie [AZ ACR Build][az-acr-build] , które kompiluje obraz, a po pomyślnym skompilowaniu obrazu wypchnij go do rejestru. Poniższy przykład kompiluje i wypycha `sample/hello-world:v1` obraz. Na `.` końcu polecenia ustawia lokalizację pliku dockerfile, w tym przypadku bieżący katalog.

```azurecli-interactive
az acr build --image sample/hello-world:v1 \
  --registry myContainerRegistry008 \
  --file Dockerfile . 
```

Dane wyjściowe z pomyślnej kompilacji i wypychania są podobne do następujących:

```console
Packing source code into tar to upload...
Uploading archived source code from '/tmp/build_archive_b0bc1e5d361b44f0833xxxx41b78c24e.tar.gz'...
Sending context (1.856 KiB) to registry: mycontainerregistry008...
Queued a build with ID: ca8
Waiting for agent...
2019/03/18 21:56:57 Using acb_vol_4c7ffa31-c862-4be3-xxxx-ab8e615c55c4 as the home volume
2019/03/18 21:56:57 Setting up Docker configuration...
2019/03/18 21:56:58 Successfully set up Docker configuration
2019/03/18 21:56:58 Logging in to registry: mycontainerregistry008.azurecr.io
2019/03/18 21:56:59 Successfully logged into mycontainerregistry008.azurecr.io
2019/03/18 21:56:59 Executing step ID: build. Working directory: '', Network: ''
2019/03/18 21:56:59 Obtaining source code and scanning for dependencies...
2019/03/18 21:57:00 Successfully obtained source code and scanned for dependencies
2019/03/18 21:57:00 Launching container with name: build
Sending build context to Docker daemon  13.82kB
Step 1/1 : FROM mcr.microsoft.com/hello-world
latest: Pulling from hello-world
Digest: sha256:2557e3c07ed1e38f26e389462d03ed943586fxxxx21577a99efb77324b0fe535
Successfully built fce289e99eb9
Successfully tagged mycontainerregistry008.azurecr.io/sample/hello-world:v1
2019/03/18 21:57:01 Successfully executed container: build
2019/03/18 21:57:01 Executing step ID: push. Working directory: '', Network: ''
2019/03/18 21:57:01 Pushing image: mycontainerregistry008.azurecr.io/sample/hello-world:v1, attempt 1
The push refers to repository [mycontainerregistry008.azurecr.io/sample/hello-world]
af0b15c8625b: Preparing
af0b15c8625b: Layer already exists
v1: digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a size: 524
2019/03/18 21:57:03 Successfully pushed image: mycontainerregistry008.azurecr.io/sample/hello-world:v1
2019/03/18 21:57:03 Step ID: build marked as successful (elapsed time in seconds: 2.543040)
2019/03/18 21:57:03 Populating digests for step ID: build...
2019/03/18 21:57:05 Successfully populated digests for step ID: build
2019/03/18 21:57:05 Step ID: push marked as successful (elapsed time in seconds: 1.473581)
2019/03/18 21:57:05 The following dependencies were found:
2019/03/18 21:57:05
- image:
    registry: mycontainerregistry008.azurecr.io
    repository: sample/hello-world
    tag: v1
    digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/hello-world
    tag: v1
    digest: sha256:2557e3c07ed1e38f26e389462d03ed943586f744621577a99efb77324b0fe535
  git: {}

Run ID: ca8 was successful after 10s
```

## <a name="run-the-image"></a>Uruchamianie obrazu

Teraz możesz szybko uruchomić utworzony wcześniej obraz i wypchnąć go do rejestru. W tym miejscu Użyj polecenia [AZ ACR Run][az-acr-run] , aby uruchomić polecenie Container. W przepływie pracy tworzenia kontenera może to być etap walidacji przed wdrożeniem obrazu lub można umieścić polecenie w [wieloetapowym pliku YAML][container-registry-tasks-multi-step]. 

Poniższy przykład używa `$Registry` do określenia rejestru, w którym uruchamiane jest polecenie:

```azurecli-interactive
az acr run --registry myContainerRegistry008 \
  --cmd '$Registry/sample/hello-world:v1' /dev/null
```

`cmd`Parametr w tym przykładzie służy do uruchamiania kontenera w konfiguracji domyślnej, ale `cmd` obsługuje dodatkowe `docker run` parametry lub nawet inne `docker` polecenia.

Dane wyjściowe będą podobne do następujących:

```console
Packing source code into tar to upload...
Uploading archived source code from '/tmp/run_archive_ebf74da7fcb04683867b129e2ccad5e1.tar.gz'...
Sending context (1.855 KiB) to registry: mycontainerre...
Queued a run with ID: cab
Waiting for an agent...
2019/03/19 19:01:53 Using acb_vol_60e9a538-b466-475f-9565-80c5b93eaa15 as the home volume
2019/03/19 19:01:53 Creating Docker network: acb_default_network, driver: 'bridge'
2019/03/19 19:01:53 Successfully set up Docker network: acb_default_network
2019/03/19 19:01:53 Setting up Docker configuration...
2019/03/19 19:01:54 Successfully set up Docker configuration
2019/03/19 19:01:54 Logging in to registry: mycontainerregistry008.azurecr.io
2019/03/19 19:01:55 Successfully logged into mycontainerregistry008.azurecr.io
2019/03/19 19:01:55 Executing step ID: acb_step_0. Working directory: '', Network: 'acb_default_network'
2019/03/19 19:01:55 Launching container with name: acb_step_0

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/

2019/03/19 19:01:56 Successfully executed container: acb_step_0
2019/03/19 19:01:56 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 0.843801)

Run ID: cab was successful after 6s
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów, rejestr kontenerów i przechowywane w nim obrazy kontenerów nie będą już potrzebne, można je usunąć za pomocą polecenia [az group delete][az-group-delete].

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start użyto funkcji zadań ACR do szybkiego kompilowania, wypychania i uruchamiania obrazu kontenera platformy Docker na platformie Azure bez lokalnej instalacji platformy Docker. Przejdź do samouczków Azure Container Registry zadań, aby dowiedzieć się więcej o używaniu zadań ACR do automatyzowania kompilacji i aktualizacji obrazów.

> [!div class="nextstepaction"]
> [Samouczki zadań Azure Container Registry][container-registry-tutorial-quick-task]

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/
[azure-account]: https://azure.microsoft.com/free/

<!-- LINKS - internal -->
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-tasks-overview]: container-registry-tasks-overview.md
[container-registry-tasks-multi-step]: container-registry-tasks-multi-step.md
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
[container-registry-skus]: container-registry-skus.md
[azure-cli-install]: /cli/azure/install-azure-cli
