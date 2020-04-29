---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 01/23/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 09eaf9465ec3912dea6e1f3ee1693f6bfed50abc
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "67183787"
---
## <a name="push-image-to-registry"></a>Wypychanie obrazu do rejestru

Aby wypchnąć obraz do usługi Azure Container Registry, najpierw musisz go mieć. Jeśli nie masz jeszcze żadnych lokalnych obrazów kontenerów, uruchom następujące polecenie [docker pull][docker-pull], aby ściągnąć istniejący obraz z usługi Docker Hub. W tym przykładzie ściągnij obraz `hello-world`.

```
docker pull hello-world
```

Zanim będzie można wypchnąć obraz do rejestru, musisz go otagować w pełni kwalifikowaną nazwą serwera logowania usługi ACR. Nazwa serwera logowania ma format * \<Registry-Name\>. azurecr.IO* (wszystkie małe litery), na przykład *mycontainerregistry007.azurecr.IO*.

Aby dodać tag do obrazu, użyj polecenia [docker tag][docker-tag]. Zastąp element `<acrLoginServer>` nazwą serwera logowania wystąpienia usługi ACR.

```
docker tag hello-world <acrLoginServer>/hello-world:v1
```

Na koniec użyj polecenia [docker push][docker-push], aby wypchnąć obraz do wystąpienia usługi ACR. Zastąp element `<acrLoginServer>` nazwą serwera logowania wystąpienia usługi ACR. W tym przykładzie jest tworzone repozytorium **hello-world** zawierające obraz `hello-world:v1`.

```
docker push <acrLoginServer>/hello-world:v1
```

Po wypchnięciu obrazu do rejestru kontenerów usuń obraz `hello-world:v1` ze środowiska lokalnego platformy Docker. (Zwróć uwagę, że to polecenie [docker rmi][docker-rmi] nie powoduje usunięcia obrazu z repozytorium **hello-world** w rejestrze kontenerów platformy Azure).

```
docker rmi <acrLoginServer>/hello-world:v1
```

<!-- LINKS - External -->
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/

<!-- LINKS - Internal -->

