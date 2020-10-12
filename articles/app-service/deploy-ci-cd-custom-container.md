---
title: Ciągłej integracji/ciągłego wdrażania z niestandardowymi kontenerami systemu Linux
description: Dowiedz się, jak skonfigurować ciągłe wdrażanie do niestandardowego kontenera systemu Linux w Azure App Service. Ciągłe wdrażanie jest obsługiwane w przypadku usługi Docker Hub i ACR.
keywords: Azure App Service, Linux, Docker, ACR, OSS
author: msangapu-msft
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.topic: article
ms.date: 11/08/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: b73c7e62c6b621d3ac86cfb99b10b567460534f1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88961860"
---
# <a name="continuous-deployment-with-web-app-for-containers"></a>Ciągłe wdrażanie za pomocą usługi Web App for Containers

W tym samouczku skonfigurujesz ciągłe wdrażanie niestandardowego obrazu kontenera z zarządzanych [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) repozytoriów lub usługi [Docker Hub](https://hub.docker.com).

## <a name="enable-continuous-deployment-with-acr"></a>Włączanie ciągłego wdrażania za pomocą ACR

![Zrzut ekranu przedstawiający element webhook ACR](./media/deploy-ci-cd-custom-container/ci-cd-acr-02.png)

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz opcję **App Service** po lewej stronie.
3. Wybierz nazwę aplikacji, dla której chcesz skonfigurować ciągłe wdrażanie.
4. Na stronie **Ustawienia kontenera** wybierz pozycję **pojedynczy kontener**
5. Wybierz **Azure Container Registry**
6. Wybierz **> ciągłego wdrażania na**
7. Wybierz pozycję **Zapisz** , aby włączyć ciągłe wdrażanie.

## <a name="use-the-acr-webhook"></a>Korzystanie z elementu webhook ACR

Po włączeniu ciągłego wdrażania można wyświetlić nowo utworzony element webhook na stronie Azure Container Registry elementów webhook.

![Zrzut ekranu przedstawiający element webhook ACR](./media/deploy-ci-cd-custom-container/ci-cd-acr-03.png)

W Container Registry kliknij pozycję elementy webhook, aby wyświetlić bieżące elementy webhook.

## <a name="enable-continuous-deployment-with-docker-hub-optional"></a>Włączanie ciągłego wdrażania za pomocą narzędzia Docker Hub (opcjonalnie)

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz opcję **App Service** po lewej stronie.
3. Wybierz nazwę aplikacji, dla której chcesz skonfigurować ciągłe wdrażanie.
4. Na stronie **Ustawienia kontenera** wybierz pozycję **pojedynczy kontener**
5. Wybieranie **centrum platformy Docker**
6. Wybierz **> ciągłego wdrażania na**
7. Wybierz pozycję **Zapisz** , aby włączyć ciągłe wdrażanie.

![Zrzut ekranu ustawienia aplikacji](./media/deploy-ci-cd-custom-container/ci-cd-docker-02.png)

Skopiuj adres URL elementu webhook. Aby dodać element webhook dla usługi Docker Hub, obserwuj elementy <a href="https://docs.docker.com/docker-hub/webhooks/" target="_blank">webhook dla usługi Docker Hub</a>.

## <a name="automate-with-cli"></a>Automatyzowanie przy użyciu interfejsu wiersza polecenia

Aby skonfigurować CI/CD za pomocą interfejsu wiersza polecenia platformy Azure, uruchom polecenie [AZ webapp Deployment Container config](/cli/azure/webapp/deployment/container?view=azure-cli-latest#az-webapp-deployment-container-config) , aby wygenerować adres URL elementu webhook. Adresu URL można użyć do skonfigurowania DockerHub lub Azure Container Registry.

```azurecli-interactive
az webapp deployment container config --name <app-name> --resource-group <group-name> --enable-cd true
```

## <a name="next-steps"></a>Następne kroki

* [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
* [Tworzenie aplikacji internetowej platformy .NET Core w usłudze App Service w systemie Linux](quickstart-dotnetcore.md?pivots=platform-linux)
* [Tworzenie aplikacji sieci Web Ruby w App Service w systemie Linux](quickstart-ruby.md)
* [Szybki Start: uruchamianie niestandardowego kontenera na App Service](quickstart-custom-container.md?pivots=container-linux)
* [Często zadawane pytania dotyczące usługi App Service w systemie Linux](faq-app-service-linux.md)
* [Konfigurowanie niestandardowych kontenerów systemu Linux](configure-custom-container.md)