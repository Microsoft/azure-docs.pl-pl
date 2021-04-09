---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 08/04/2020
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 404fd10c3e3610671d2b6e5dbc6aba8bcaa70046
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100019260"
---
## <a name="push-image-to-registry"></a>Wypychanie obrazu do rejestru

Aby wypchnąć obraz do usługi Azure Container Registry, najpierw musisz go mieć. Jeśli nie masz jeszcze żadnych obrazów kontenera lokalnego, uruchom następujące polecenie [Docker pull][docker-pull] , aby ściągnąć istniejący obraz publiczny. Na potrzeby tego przykładu Pobierz `hello-world` obraz z witryny Microsoft Container Registry.

```
docker pull mcr.microsoft.com/hello-world
```

Przed wypchnięciem obrazu do rejestru należy oznaczyć go za pomocą w pełni kwalifikowanej nazwy serwera logowania rejestru. Nazwa serwera logowania ma format *\<registry-name\> . azurecr.IO* (musi zawierać tylko małe litery), na przykład *mycontainerregistry.azurecr.IO*.

Aby dodać tag do obrazu, użyj polecenia [docker tag][docker-tag]. Zastąp element `<login-server>` nazwą serwera logowania wystąpienia usługi ACR.

```
docker tag mcr.microsoft.com/hello-world <login-server>/hello-world:v1
```

Przykład:

```
docker tag mcr.microsoft.com/hello-world mycontainerregistry.azurecr.io/hello-world:v1
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

