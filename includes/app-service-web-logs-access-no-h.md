---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/27/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 0dd6618bdee8e6810d414d4b04b16a1e0a9c90ed
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "67183707"
---
Możesz uzyskać dostęp do dzienników konsoli wygenerowanych z wewnątrz kontenera. Najpierw Włącz rejestrowanie kontenerów, uruchamiając następujące polecenie w Cloud Shell:

```azurecli-interactive
az webapp log config --name <app-name> --resource-group myResourceGroup --docker-container-logging filesystem
```

Po włączeniu rejestrowania kontenerów uruchom następujące polecenie, aby wyświetlić strumień dziennika:

```azurecli-interactive
az webapp log tail --name <app-name> --resource-group myResourceGroup
```

Jeśli nie widzisz dzienników konsoli, sprawdź ponownie w ciągu 30 sekund.

> [!NOTE]
> Możesz również sprawdzić pliki dziennika z przeglądarki pod adresem `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

Aby zatrzymać przesyłanie strumieniowe dzienników w dowolnym momencie `Ctrl` + `C`, wpisz polecenie.
