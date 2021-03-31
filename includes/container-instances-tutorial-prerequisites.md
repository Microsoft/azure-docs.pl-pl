---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: include
ms.date: 03/20/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 10fb9e8169b7f4159ccbf4a0ff36021f6033f811
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "75552425"
---
Aby ukończyć ten samouczek, musisz spełniać następujące wymagania:

**Interfejs wiersza polecenia platformy Azure**: musisz mieć interfejs wiersza polecenia platformy Azure w wersji 2.0.29 lub nowszej zainstalowany na komputerze lokalnym. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

**Docker**: ten samouczek zakłada, że masz podstawową wiedzę na temat najważniejszych pojęć usługi Docker, takich jak kontenery, obrazy kontenera i podstawowe polecenia `docker`. Aby uzyskać podstawowe informacje na temat platformy Docker i kontenerów, zapoznaj się z artykułem [Docker overview][docker-get-started] (Przegląd platformy Docker).

**Docker**: aby ukończyć ten samouczek, potrzebny jest program Docker zainstalowany lokalnie. Platforma Docker udostępnia pakiety, które konfigurują środowisko platformy Docker w systemach [macOS][docker-mac], [Windows][docker-windows] i [Linux][docker-linux].

> [!IMPORTANT]
> Ponieważ usługa Azure Cloud Shell nie zawiera demona platformy Docker, w celu ukończenia tego samouczka *musisz* zainstalować na swoim *komputerze lokalnym* wiersz polecenia platformy Azure i aparat platformy Docker. W tym samouczku nie możesz korzystać z usługi Azure Cloud Shell.

<!-- LINKS - External -->
[docker-get-started]: https://docs.docker.com/engine/docker-overview/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
