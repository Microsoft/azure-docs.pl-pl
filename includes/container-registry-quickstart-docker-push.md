---
title: plik dołączania
description: plik dołączania
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 08/04/2020
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 16c1dec9e52be98e5b4e0103a583bdc219bc2417
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "88031825"
---
## <a name="push-image-to-registry"></a>Wypychanie obrazu do rejestru

Aby wypchnąć obraz do usługi Azure Container Registry, najpierw musisz go mieć. Jeśli nie masz jeszcze żadnych lokalnych obrazów kontenerów, uruchom następujące polecenie [docker pull][docker-pull], aby ściągnąć istniejący obraz z usługi Docker Hub. W tym przykładzie ściągnij obraz `hello-world`.

```
docker pull hello-world
```

Przed wypchnięciem obrazu do rejestru należy oznaczyć go za pomocą w pełni kwalifikowanej nazwy serwera logowania rejestru. Nazwa serwera logowania ma format * \<registry-name\> . azurecr.IO* (wszystkie małe litery), na przykład *mycontainerregistry.azurecr.IO*.

Aby dodać tag do obrazu, użyj polecenia [docker tag][docker-tag]. Zastąp element `<login-server>` nazwą serwera logowania wystąpienia usługi ACR.

```
docker tag hello-world <login-server>/hello-world:v1
```

Przykład:

```
docker tag hello-world mycontainerregistry.azurecr.io/hello-world:v1
```


Na koniec Użyj [polecenia Docker push][docker-push] , aby wypchnąć obraz do wystąpienia rejestru. Zamień na `<login-server>` nazwę serwera logowania wystąpienia rejestru. W tym przykładzie jest tworzone repozytorium **hello-world** zawierające obraz `hello-world:v1`.

```
docker push <login-server>/hello-world:v1
```

Po wypchnięciu obrazu do rejestru kontenerów usuń obraz `hello-world:v1` ze środowiska lokalnego platformy Docker. (Zwróć uwagę, że to polecenie [docker rmi][docker-rmi] nie powoduje usunięcia obrazu z repozytorium **hello-world** w rejestrze kontenerów platformy Azure).

```
docker rmi <login-server>/hello-world:v1
```

<!-- LINKS - External -->
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/

<!-- LINKS - Internal -->

