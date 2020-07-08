---
title: Zarządzanie niestandardowymi kontenerami systemu Linux przy użyciu interfejsu wiersza polecenia
description: Dowiedz się, jak zarządzać niestandardowymi kontenerami systemu Linux w Azure App Service z poziomu wiersza polecenia. Automatyzowanie aprowizacji lub konserwacji aplikacji.
keywords: Azure App Service, Web App, CLI, Linux, OSS
ms.topic: article
ms.date: 08/22/2017
ms.custom: seodec18
ms.openlocfilehash: 5ca5322467402af710df68c82d747f8f8d65e142
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "78255925"
---
# <a name="manage-web-app-for-containers-using-azure-cli"></a>Zarządzanie funkcją Web App for Containers za pomocą interfejsu wiersza polecenia platformy Azure

Za pomocą poleceń w tym artykule można tworzyć Web App for Containers i zarządzać nimi za pomocą interfejsu wiersza polecenia platformy Azure.
Możesz rozpocząć korzystanie z nowej wersji interfejsu wiersza polecenia na dwa sposoby:

* [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) na komputerze.
* Korzystanie z [Azure Cloud Shell (wersja zapoznawcza)](../../cloud-shell/overview.md)

## <a name="create-a-linux-app-service-plan"></a>Tworzenie planu App Service systemu Linux

Aby utworzyć plan App Service systemu Linux, można użyć następującego polecenia:

```azurecli-interactive
az appservice plan create -n appname -g rgname --is-linux -l "South Central US" --sku S1 --number-of-workers 1
```

## <a name="create-a-custom-docker-container-web-app"></a>Tworzenie niestandardowej aplikacji sieci Web kontenera platformy Docker

Aby utworzyć aplikację sieci Web i skonfigurować ją do uruchamiania niestandardowego kontenera platformy Docker, można użyć następującego polecenia:

```azurecli-interactive
az webapp create -n sname -g rgname -p pname -i elnably/dockerimagetest
```

## <a name="activate-the-docker-container-logging"></a>Aktywuj rejestrowanie kontenerów platformy Docker

Aby aktywować rejestrowanie kontenerów platformy Docker, można użyć następującego polecenia:

```azurecli-interactive
az webapp log config -n sname -g rgname --web-server-logging filesystem
```

## <a name="change-the-custom-docker-container-for-an-existing-web-app-for-containers-app"></a>Zmienianie niestandardowego kontenera platformy Docker dla istniejącej aplikacji Web App for Containers

Aby zmienić utworzoną wcześniej aplikację z bieżącego obrazu platformy Docker na nowy obraz, można użyć następującego polecenia:

```azurecli-interactive
az webapp config container set -n sname -g rgname -c apurvajo/mariohtml5
```

## <a name="using-docker-images-from-a-private-registry"></a>Używanie obrazów platformy Docker z rejestru prywatnego

Aplikację można skonfigurować tak, aby korzystała z obrazów z prywatnego rejestru. Musisz podać adres URL rejestru, nazwę użytkownika i hasło. Można to osiągnąć przy użyciu następującego polecenia:

```azurecli-interactive
az webapp config container set -n sname1 -g rgname -c <container name> -r <server url> -u <username> -p <password>
```

## <a name="enable-continuous-deployments-for-custom-docker-images"></a>Włącz ciągłe wdrożenia dla niestandardowych obrazów platformy Docker

Za pomocą następującego polecenia można włączyć funkcję CD i uzyskać adres URL elementu webhook. Ten adres URL może służyć do konfigurowania repozytoriów DockerHub lub Azure Container Registry.

```azurecli-interactive
az webapp deployment container config -n sname -g rgname -e true
```

## <a name="create-a-web-app-for-containers-app-using-one-of-our-built-in-runtime-frameworks"></a>Tworzenie aplikacji Web App for Containers przy użyciu jednej z naszych wbudowanych platform środowiska uruchomieniowego

Aby utworzyć aplikację Web App for Containers w języku PHP 5,6, możesz użyć poniższego polecenia.

```azurecli-interactive
az webapp create -n sname -g rgname -p pname -r "php|5.6"
```

## <a name="change-framework-version-for-an-existing-web-app-for-containers-app"></a>Zmień wersję platformy dla istniejącej aplikacji Web App for Containers

Aby zmienić wcześniej utworzoną aplikację z bieżącej wersji platformy na Node.js 6,11, można użyć następującego polecenia:

```azurecli-interactive
az webapp config set -n sname -g rgname --linux-fx-version "node|6.11"
```

## <a name="set-up-git-deployments-for-your-web-app"></a>Konfigurowanie wdrożeń usługi git dla aplikacji sieci Web

Aby skonfigurować wdrożenie narzędzia Git dla aplikacji, można użyć następującego polecenia:

```azurecli-interactive
az webapp deployment source config -n sname -g rgname --repo-url <gitrepo url> --branch <branch>
```

## <a name="next-steps"></a>Następne kroki

* [Co to jest Azure App Service w systemie Linux?](app-service-linux-intro.md)
* [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli)
* [Azure Cloud Shell (wersja zapoznawcza)](../../cloud-shell/overview.md)
* [Konfigurowanie środowisk przejściowych w usłudze Azure App Service](../../app-service/deploy-staging-slots.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Ciągłe wdrażanie za pomocą Web App for Containers](app-service-linux-ci-cd.md)
