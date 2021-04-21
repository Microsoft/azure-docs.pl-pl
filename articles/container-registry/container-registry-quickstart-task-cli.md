---
title: Szybki start — tworzenie obrazu kontenera na żądanie na platformie Azure
description: Użyj Azure Container Registry, aby szybko skompilować, wypchnąć i uruchomić obraz kontenera platformy Docker na żądanie w chmurze platformy Azure.
ms.topic: quickstart
ms.date: 09/25/2020
ms.custom: contperf-fy21q1, devx-track-azurecli
ms.openlocfilehash: d3ef126812cb36874b74c8569d141787f71a29da
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781455"
---
# <a name="quickstart-build-and-run-a-container-image-using-azure-container-registry-tasks"></a>Szybki start: kompilowanie i uruchamianie obrazu kontenera przy użyciu zadania usługi Azure Container Registry

W tym przewodniku Szybki start użyjemy poleceń usługi [zadania usługi Azure Container Registry,][container-registry-tasks-overview] aby szybko kompilować, wypychać i uruchamiać obraz kontenera platformy Docker natywnie na platformie Azure bez lokalnej instalacji platformy Docker. zadania usługi ACR to pakiet funkcji w ramach Azure Container Registry, które ułatwiają zarządzanie obrazami kontenerów i modyfikowanie ich w całym cyklu życia kontenera. W tym przykładzie pokazano, jak odciążyć cykl tworzenia obrazu kontenera "pętli wewnętrznej" do chmury za pomocą kompilacji na żądanie przy użyciu lokalnego pliku Dockerfile. 

Po zakończeniu tego przewodnika Szybki start zapoznaj się z bardziej zaawansowanymi funkcjami usługi zadania usługi ACR [samouczkach.](container-registry-tutorial-quick-task.md) zadania usługi ACR można zautomatyzować kompilacje obrazów na podstawie zatwierdzeń kodu lub aktualizacji obrazu podstawowego albo przetestować wiele kontenerów równolegle, między innymi. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]
    
- Ten przewodnik Szybki start wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.58 lub nowszej. Jeśli używasz Azure Cloud Shell, najnowsza wersja jest już zainstalowana.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Jeśli nie masz jeszcze rejestru kontenerów, najpierw utwórz grupę zasobów za pomocą [polecenia az group create.][az-group-create] Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container-registry"></a>Tworzenie rejestru kontenerów

Utwórz rejestr kontenerów za pomocą [polecenia az acr create.][az-acr-create] Nazwa rejestru musi być unikatowa w obrębie platformy Azure i może zawierać od 5 do 50 znaków alfanumerycznych. W poniższym przykładzie *jest używana tabela myContainerRegistry008.* Zaktualizuj ją do unikatowej wartości.

```azurecli-interactive
az acr create --resource-group myResourceGroup \
  --name myContainerRegistry008 --sku Basic
```

W tym przykładzie *jest owany* rejestr Podstawowy, zoptymalizowany pod kątem kosztów opcja dla deweloperów, którzy uczą się Azure Container Registry. Aby uzyskać szczegółowe informacje na temat dostępnych warstw usług, zobacz [Warstwy usług rejestru kontenerów][container-registry-skus].

## <a name="build-and-push-image-from-a-dockerfile"></a>Kompilowanie i wypychanie obrazu z pliku Dockerfile

Teraz użyj Azure Container Registry, aby skompilować i wypchnąć obraz. Najpierw utwórz lokalny katalog roboczy, a następnie utwórz plik Dockerfile o nazwie *Dockerfile* z pojedynczym wierszem: `FROM mcr.microsoft.com/hello-world` . Jest to prosty przykład tworzenia obrazu kontenera systemu Linux na przykładzie obrazu `hello-world` hostowanej w Microsoft Container Registry. Możesz tworzyć własne standardowe pliki Dockerfile i tworzyć obrazy dla innych platform. Jeśli pracujesz w powłoce bash, utwórz plik Dockerfile za pomocą następującego polecenia:

```bash
echo FROM mcr.microsoft.com/hello-world > Dockerfile
```

Uruchom [polecenie az acr build,][az-acr-build] które skompilowało obraz i po pomyślnym skompilowaniu obrazu wypchnięło go do rejestru. Poniższy przykład tworzy i wypycha `sample/hello-world:v1` obraz. Na końcu polecenia ustawia lokalizację pliku Dockerfile, w tym przypadku `.` bieżącego katalogu.

```azurecli-interactive
az acr build --image sample/hello-world:v1 \
  --registry myContainerRegistry008 \
  --file Dockerfile . 
```

Dane wyjściowe pomyślnej kompilacji i wypychania są podobne do następujących:

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

Teraz możesz szybko uruchomić sbudowaną i wypchniętą do rejestru obraz. W tym miejscu użyj [polecenia az acr run,][az-acr-run] aby uruchomić polecenie kontenera. W przepływie pracy tworzenia kontenera może to być krok weryfikacji przed wdrożeniem obrazu lub dołączenie polecenia do wieloetapowego [pliku YAML.][container-registry-tasks-multi-step] 

W poniższym przykładzie `$Registry` użyto polecenia , aby określić rejestr, w którym jest uruchamiane polecenie:

```azurecli-interactive
az acr run --registry myContainerRegistry008 \
  --cmd '$Registry/sample/hello-world:v1' /dev/null
```

Parametr `cmd` w tym przykładzie uruchamia kontener w konfiguracji domyślnej, ale `cmd` obsługuje dodatkowe `docker run` parametry, a nawet inne `docker` polecenia.

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

W tym przewodniku Szybki start za pomocą funkcji platformy zadania usługi ACR szybko kompilować, wypychać i uruchamiać obraz kontenera platformy Docker natywnie na platformie Azure bez lokalnej instalacji platformy Docker. Przejdź do samouczków zadania usługi Azure Container Registry, aby dowiedzieć się więcej na temat używania zadania usługi ACR do automatyzowania kompilacji i aktualizacji obrazów.

> [!div class="nextstepaction"]
> [zadania usługi Azure Container Registry samouczków][container-registry-tutorial-quick-task]

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
[az-acr-create]: /cli/azure/acr#az_acr_create
[az-acr-build]: /cli/azure/acr#az_acr_build
[az-acr-run]: /cli/azure/acr#az_acr_run
[az-group-create]: /cli/azure/group#az_group_create
[az-group-delete]: /cli/azure/group#az_group_delete
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-tasks-overview]: container-registry-tasks-overview.md
[container-registry-tasks-multi-step]: container-registry-tasks-multi-step.md
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
[container-registry-skus]: container-registry-skus.md
[azure-cli-install]: /cli/azure/install-azure-cli
